---
---

* TOC
{:toc}

# The Kubernetes model for connecting containers

Now that you have a continuously running, replicated application you can expose it on a network. Before discussing the Kubernetes approach to networking, it is worthwhile to contrast it with the "normal" way networking works with Docker.

By default, Docker uses host-private networking, so containers can talk to other containers only if they are on the same machine. In order for Docker containers to communicate across nodes, they must be allocated ports on the machine's own IP address, which are then forwarded or proxied to the containers. This obviously means that containers must either coordinate which ports they use very carefully or else be allocated ports dynamically.

Coordinating ports across multiple developers is very difficult to do at scale and exposes users to cluster-level issues outside of their control. Kubernetes assumes that pods can communicate with other pods, regardless of which host they land on. We give every pod its own cluster-private-IP address so you do not need to explicitly create links between pods or mapping container ports to host ports. This means that containers within a Pod can all reach each other's ports on localhost, and all pods in a cluster can see each other without NAT. The rest of this document will elaborate on how you can run reliable services on such a networking model.

This guide uses a simple nginx server to demonstrate proof of concept. The same principles are embodied in a more complete [Jenkins CI application](http://blog.kubernetes.io/2015/07/strong-simple-ssl-for-kubernetes.html).

## Exposing pods to the cluster

We did this in a previous example, but lets do it once again and focus on the networking perspective. Create an nginx pod, and note that it has a container port specification:

```yaml
$ cat nginxrc.yaml
apiVersion: v1
kind: ReplicationController
metadata:
  name: my-nginx
spec:
  replicas: 2
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx
        ports:
        - containerPort: 80
```

This makes it accessible from any node in your cluster. Check the nodes the pod is running on:

```shell
$ kubectl create -f ./nginxrc.yaml
$ kubectl get pods -l app=nginx -o wide
my-nginx-6isf4   1/1       Running   0          2h        e2e-test-beeps-node-93ly
my-nginx-t26zt   1/1       Running   0          2h        e2e-test-beeps-node-93ly
```

Check your pods' IPs:

```shell
$ kubectl get pods -l app=nginx -o json | grep podIP
                "podIP": "10.245.0.15",
                "podIP": "10.245.0.14",
```

You should be able to ssh into any node in your cluster and curl both IPs. Note that the containers are *not* using port 80 on the node, nor are there any special NAT rules to route traffic to the pod. This means you can run multiple nginx pods on the same node all using the same containerPort and access them from any other pod or node in your cluster using IP. Like Docker, ports can still be published to the host node's interface(s), but the need for this is radically diminished because of the networking model.

You can read more about [how we achieve this](/docs/admin/networking/#how-to-achieve-this) if you're curious.

## Creating a Service

So we have pods running nginx in a flat, cluster wide, address space. In theory, you could talk to these pods directly, but what happens when a node dies? The pods die with it, and the replication controller will create new ones, with different IPs. This is the problem a Service solves.

A Kubernetes Service is an abstraction which defines a logical set of Pods running somewhere in your cluster, that all provide the same functionality. When created, each Service is assigned a unique IP address (also called clusterIP). This address is tied to the lifespan of the Service, and will not change while the Service is alive. Pods can be configured to talk to the Service, and know that communication to the Service will be automatically load-balanced out to some pod that is a member of the Service.

You can create a Service for your 2 nginx replicas with the following yaml:

```yaml
$ cat nginxsvc.yaml
apiVersion: v1
kind: Service
metadata:
  name: nginxsvc
  labels:
    app: nginx
spec:
  ports:
  - port: 80
    protocol: TCP
  selector:
    app: nginx
```

This specification will create a Service which targets TCP port 80 on any Pod with the `app=nginx` label, and expose it on an abstracted Service port (`targetPort`: is the port the container accepts traffic on, `port`: is the abstracted Service port, which can be any port other pods use to access the Service). View [service API object](/docs/api-reference/v1/definitions/#_v1_service) to see the list of supported fields in service definition.
Check your Service:

```shell
$ kubectl get svc
NAME         CLUSTER_IP       EXTERNAL_IP       PORT(S)                SELECTOR     AGE
kubernetes   10.179.240.1     <none>            443/TCP                <none>       8d
nginxsvc     10.179.252.126   122.222.183.144   80/TCP,81/TCP,82/TCP   run=nginx2   11m
```

As mentioned previously, a Service is backed by a group of pods. These pods are exposed through `endpoints`. The Service's selector will be evaluated continuously and the results will be POSTed to an Endpoints object also named `nginxsvc`. When a pod dies, it is automatically removed from the endpoints, and new pods matching the Service's selector will automatically get added to the endpoints. Check the endpoints, and note that the IPs are the same as the pods created in the first step:

```shell
$ kubectl describe svc nginxsvc
Name:			nginxsvc
Namespace:		default
Labels:			app=nginx
Selector:		app=nginx
Type:			ClusterIP
IP:			10.0.116.146
Port:			<unnamed>	80/TCP
Endpoints:		10.245.0.14:80,10.245.0.15:80
Session Affinity:	None
No events.

$ kubectl get ep
NAME         ENDPOINTS
nginxsvc     10.245.0.14:80,10.245.0.15:80
```

You should now be able to curl the nginx Service on `10.0.116.146:80` from any node in your cluster. Note that the Service IP is completely virtual, it never hits the wire, if you're curious about how this works you can read more about the [service proxy](/docs/user-guide/services/#virtual-ips-and-service-proxies).

## Accessing the Service

Kubernetes supports 2 primary modes of finding a Service - environment variables and DNS. The former works out of the box while the latter requires the [kube-dns cluster addon](http://releases.k8s.io/{{page.githubbranch}}/cluster/addons/dns/README.md).

### Environment Variables

When a Pod is run on a Node, the kubelet adds a set of environment variables for each active Service. This introduces an ordering problem. To see why, inspect the environment of your running nginx pods:

```shell
$ kubectl exec my-nginx-6isf4 -- printenv | grep SERVICE
KUBERNETES_SERVICE_HOST=10.0.0.1
KUBERNETES_SERVICE_PORT=443
```

Note there's no mention of your Service. This is because you created the replicas before the Service. Another disadvantage of doing this is that the scheduler might put both pods on the same machine, which will take your entire Service down if it dies. We can do this the right way by killing the 2 pods and waiting for the replication controller to recreate them. This time around the Service exists *before* the replicas. This will given you scheduler level Service spreading of your pods (provided all your nodes have equal capacity), as well as the right environment variables:

```shell
$ kubectl scale rc my-nginx --replicas=0; kubectl scale rc my-nginx --replicas=2;
$ kubectl get pods -l app=nginx -o wide
NAME             READY   STATUS     RESTARTS   AGE   NODE
my-nginx-5j8ok   1/1     Running   	0         2m    node1
my-nginx-90vaf   1/1     Running   0          2m    node2

$ kubectl exec my-nginx-5j8ok -- printenv | grep SERVICE
KUBERNETES_SERVICE_PORT=443
NGINXSVC_SERVICE_HOST=10.0.116.146
KUBERNETES_SERVICE_HOST=10.0.0.1
NGINXSVC_SERVICE_PORT=80
```

### DNS

Kubernetes offers a DNS cluster addon Service that uses skydns to automatically assign dns names to other Services. You can check if it's running on your cluster:

```shell
$ kubectl get services kube-dns --namespace=kube-system
NAME       CLUSTER_IP      EXTERNAL_IP   PORT(S)         SELECTOR           AGE
kube-dns   10.179.240.10   <none>        53/UDP,53/TCP   k8s-app=kube-dns   8d
```

If it isn't running, you can [enable it](http://releases.k8s.io/{{page.githubbranch}}/cluster/addons/dns/README.md#how-do-i-configure-it). The rest of this section will assume you have a Service with a long lived IP (nginxsvc), and a dns server that has assigned a name to that IP (the kube-dns cluster addon), so you can talk to the Service from any pod in your cluster using standard methods (e.g. gethostbyname). Let's create another pod to test this:

```yaml
$ cat curlpod.yaml
apiVersion: v1
kind: Pod
metadata:
  name: curlpod
spec:
  containers:
  - image: radial/busyboxplus:curl
    command:
      - sleep
      - "3600"
    imagePullPolicy: IfNotPresent
    name: curlcontainer
  restartPolicy: Always
```

And perform a lookup of the nginx Service

```shell
$ kubectl create -f ./curlpod.yaml
default/curlpod
$ kubectl get pods curlpod
NAME      READY     STATUS    RESTARTS   AGE
curlpod   1/1       Running   0          18s

$ kubectl exec curlpod -- nslookup nginxsvc
Server:    10.0.0.10
Address 1: 10.0.0.10
Name:      nginxsvc
Address 1: 10.0.116.146
```

## Securing the Service

Till now we have only accessed the nginx server from within the cluster. Before exposing the Service to the internet, you want to make sure the communication channel is secure. For this, you will need:

* Self signed certificates for https (unless you already have an identity certificate)
* An nginx server configured to use the certificates
* A [secret](/docs/user-guide/secrets) that makes the certificates accessible to pods

You can acquire all these from the [nginx https example](https://github.com/kubernetes/kubernetes/tree/{{page.githubbranch}}/examples/https-nginx/), in short:

```shell
$ make keys secret KEY=/tmp/nginx.key CERT=/tmp/nginx.crt SECRET=/tmp/secret.json
$ kubectl create -f /tmp/secret.json
secrets/nginxsecret
$ kubectl get secrets
NAME                  TYPE                                  DATA
default-token-il9rc   kubernetes.io/service-account-token   1
nginxsecret           Opaque                                2
```

Now modify your nginx replicas to start a https server using the certificate in the secret, and the Service, to expose both ports (80 and 443):

```yaml
$ cat nginx-app.yaml
apiVersion: v1
kind: Service
metadata:
  name: nginxsvc
  labels:
    app: nginx
spec:
  type: NodePort
  ports:
  - port: 8080
    targetPort: 80
    protocol: TCP
    name: http
  - port: 443
    protocol: TCP
    name: https
  selector:
    app: nginx
---
apiVersion: v1
kind: ReplicationController
metadata:
  name: my-nginx
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: nginx
    spec:
      volumes:
      - name: secret-volume
        secret:
          secretName: nginxsecret
      containers:
      - name: nginxhttps
        image: bprashanth/nginxhttps:1.0
        ports:
        - containerPort: 443
        - containerPort: 80
        volumeMounts:
        - mountPath: /etc/nginx/ssl
          name: secret-volume
```

Noteworthy points about the nginx-app manifest:

- It contains both rc and service specification in the same file
- The [nginx server](https://github.com/kubernetes/kubernetes/tree/{{page.githubbranch}}/examples/https-nginx/default.conf) serves http traffic on port 80 and https traffic on 443, and nginx Service exposes both ports.
- Each container has access to the keys through a volume mounted at /etc/nginx/ssl. This is setup *before* the nginx server is started.

```shell
$ kubectl delete rc,svc -l app=nginx; kubectl create -f ./nginx-app.yaml
replicationcontrollers/my-nginx
services/nginxsvc
services/nginxsvc
replicationcontrollers/my-nginx
```

At this point you can reach the nginx server from any node.

```shell
$ kubectl get pods -o json | grep -i podip
    "podIP": "10.1.0.80",
node $ curl -k https://10.1.0.80
...
<h1>Welcome to nginx!</h1>
```

Note how we supplied the `-k` parameter to curl in the last step, this is because we don't know anything about the pods running nginx at certificate generation time,
so we have to tell curl to ignore the CName mismatch. By creating a Service we linked the CName used in the certificate with the actual DNS name used by pods during Service lookup.
Lets test this from a pod (the same secret is being reused for simplicity, the pod only needs nginx.crt to access the Service):

```shell
$ cat curlpod.yaml
apiVersion: v1
kind: ReplicationController
metadata:
  name: curlrc
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: curlpod
    spec:
      volumes:
      - name: secret-volume
        secret:
          secretName: nginxsecret
      containers:
      - name: curlpod
        command:
        - sh
        - -c
        - while true; do sleep 1; done
        image: radial/busyboxplus:curl
        volumeMounts:
        - mountPath: /etc/nginx/ssl
          name: secret-volume

$ kubectl create -f ./curlpod.yaml
$ kubectl get pods
NAME             READY     STATUS    RESTARTS   AGE
curlpod          1/1       Running   0          2m
my-nginx-7006w   1/1       Running   0          24m

$ kubectl exec curlpod -- curl https://nginxsvc --cacert /etc/nginx/ssl/nginx.crt
...
<title>Welcome to nginx!</title>
...
```

## Exposing the Service

For some parts of your applications you may want to expose a Service onto an external IP address. Kubernetes supports two ways of doing this: NodePorts and LoadBalancers. The Service created in the last section already used `NodePort`, so your nginx https replica is ready to serve traffic on the internet if your node has a public IP.

```shell
$ kubectl get svc nginxsvc -o json | grep -i nodeport -C 5
            {
                "name": "http",
                "protocol": "TCP",
                "port": 80,
                "targetPort": 80,
                "nodePort": 32188
            },
            {
                "name": "https",
                "protocol": "TCP",
                "port": 443,
                "targetPort": 443,
                "nodePort": 30645
            }

$ kubectl get nodes -o json | grep ExternalIP -C 2
                    {
                        "type": "ExternalIP",
                        "address": "104.197.63.17"
                    }
--
                    },
                    {
                        "type": "ExternalIP",
                        "address": "104.154.89.170"
                    }
$ curl https://104.197.63.17:30645 -k
...
<h1>Welcome to nginx!</h1>
```

Lets now recreate the Service to use a cloud load balancer, just change the `Type` of Service in the nginx-app.yaml from `NodePort` to `LoadBalancer`:

```shell
$ kubectl delete rc,svc -l app=nginx
$ kubectl create -f ./nginx-app.yaml
$ kubectl get svc nginxsvc
NAME      CLUSTER_IP       EXTERNAL_IP       PORT(S)                SELECTOR     AGE
nginxsvc  10.179.252.126   162.222.184.144   80/TCP,81/TCP,82/TCP   run=nginx2   13m

$ curl https://162.22.184.144 -k
...
<title>Welcome to nginx!</title>
```

The IP address in the `EXTERNAL_IP` column is the one that is available on the public internet.  The `CLUSTER_IP` is only available inside your
cluster/private cloud network.

Note that on AWS, type `LoadBalancer` creates an ELB, which uses a (long)
hostname, not an IP.  It's too long to fit in the standard `kubectl get svc`
output, in fact, so you'll need to do `kubectl describe service nginxsvc` to
see it.  You'll see something like this:

```shell
> kubectl describe service nginxsvc
...
LoadBalancer Ingress:   a320587ffd19711e5a37606cf4a74574-1142138393.us-east-1.elb.amazonaws.com
...
```

## Loadbalancing multiple Services

Till now we had restricted ourselves to a single Service, but Kubernetes has a resource called [Ingress](/docs/user-guide/ingress) that spans Services and loadbalances requests at [layer 7](https://en.wikipedia.org/wiki/OSI_model#Layer_7:_Application_Layer). This is useful for reasons like:

* Cost saving, since the Ingress only needs one public IP, instead of 1 per Service of `Type=LoadBalancer`.
* Centralized security configuration, instead of loading TLS certs as volumes in each Service backend.
* Smarter routing, since the Ingress has visibility into layer 7 of the OSI model.

For demonstration purposes we will deploy a second Service that simply echoes the HTTP Headers it receives:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: echoheaders
  labels:
    app: echoheaders
spec:
  type: NodePort
  ports:
  - port: 80
    targetPort: 8080
    protocol: TCP
    name: http
  selector:
    app: echoheaders
---
apiVersion: v1
kind: ReplicationController
metadata:
  name: echoheaders
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: echoheaders
    spec:
      containers:
      - name: echoheaders
        image: gcr.io/google_containers/echoserver:1.2
        ports:
        - containerPort: 8080
```

To make use of the Ingress resource, you need to first deploy an Ingress controller. On GCE/GKE there should already be an l7-controller deployed into the `kube-system` namespace:

```shell
$ kubectl get pods --namespace=kube-system -l name=glbc
NAME                            READY     STATUS    RESTARTS   AGE
l7-lb-controller-v0.6.0-chnan   2/2       Running   0          1d
```

Since Ingress is still a beta resource, make sure you review the [beta limitations](https://github.com/kubernetes/contrib/tree/master/ingress/controllers/gce/BETA_LIMITATIONS.md) of the controller. In particular, you need to create a single firewall-rule on your cloudprovider, to allow health checks. On GKE this would be:

```shell
$ export TAG=$(basename `gcloud container clusters describe ${CLUSTER_NAME} --zone ${ZONE} | grep gke | awk '{print $2}'` | sed -e s/group/node/)
$ export NODE_PORT=$(kubectl get -o jsonpath="{.spec.ports[0].nodePort}" services echoheaders)
$ gcloud compute firewall-rules create allow-130-211-0-0-22 \
  --source-ranges 130.211.0.0/22 \
  --target-tags $TAG \
  --allow tcp:$NODE_PORT
```

In environments other than GCE/GKE, you need to [deploy a controller](https://github.com/kubernetes/contrib/tree/master/ingress/controllers).
Once your controller is Running, you can create an Ingress:

```yaml
kind: Ingress
metadata:
  name: test
spec:
  # Catchall default backend for requests that don't match a host/url
  backend:
    serviceName: echoheaders
    servicePort: 80
  rules:
  # Everything with a host header of example.com
  - host: example.com
    http:
      paths:
      - backend:
          serviceName: nginxsvc
          servicePort: 80
  # Everything that's not example.com goes to default backend unless the path
  # ends in /nginx
  - http:
      paths:
      - path: /nginx
        backend:
          serviceName: nginxsvc
          servicePort: 80
```

And wait till it acquires an IP:

```yaml
$ kubectl get ing
NAME      RULE          BACKEND          ADDRESS        AGE
test      -             echoheaders:80   130.211.5.76   2m
          example.com
                        nginxsvc:80

          /nginx        nginxsvc:80
```

You can get more detailed information about the state of the Ingress through `kubectl describe`. This output is controller specific. The GCE controller for example, will annotate the output of `kubectl describe` with information about the backend health checks. Requests will fail if backends are not healthy.

```shell
$ kubectl describe ing
Name:			test
Namespace:		default
Address:		130.211.5.76
Default backend:	echoheaders:80 (10.245.2.3:8080)
Rules:
  Host		Path	Backends
  ----		----	--------
  example.com
    		 	nginxsvc:80 (10.245.3.5:80)

    		/nginx 	nginxsvc:80 (10.245.3.5:80)
Annotations:
  forwarding-rule:	k8s-fw-default-test--uid
  target-proxy:		k8s-tp-default-test--uid
  url-map:		k8s-um-default-test--uid
  backends:		{"k8s-be-31058--uid":"HEALTHY","k8s-be-32583--uid":"HEALTHY"}
Events:
  FirstSeen	LastSeen	Count	From				SubobjectPath	Type		Reason	Message
  ---------	--------	-----	----				-------------	--------	------	-------
  3m		3m		1	{loadbalancer-controller }			Normal		ADD	default/test
  2m		2m		1	{loadbalancer-controller }			Normal		CREATE	ip: 130.211.5.76
```

Now you can try curling the endpoints:

```shell
$ curl 130.211.5.76 -H 'Host:example.com'
...
<p><em>Thank you for using nginx.</em></p>

$ curl 130.211.5.76/nginx
...
<head><title>404 Not Found</title></head>
<hr><center>nginx/1.9.1</center>
```

Note that the second url returned a 404. This is because the Ingress controller caught `/nginx` and passed that onto the backend (as `/nginx`), but nginx doesn't know how to handle that path. This is also evident from the `real path=` key in the output of the echoserver default backend:

```shell
$ curl 130.211.5.76/unknown-url
CLIENT VALUES:
client_address=10.240.0.3
command=GET
real path=/unknown-url
...
```

You can learn more about different modes of Ingress [here](/docs/user-guide/ingress).

## What's next?

[Learn about more Kubernetes features that will help you run containers reliably in production.](/docs/user-guide/production-pods)
