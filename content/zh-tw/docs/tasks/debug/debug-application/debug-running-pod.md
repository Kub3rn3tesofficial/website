---
title: 除錯執行中的 Pod
content_type: task
---

<!-- 
reviewers:
- verb
- soltysh
title: Debug Running Pods
content_type: task
-->

<!-- overview -->
<!--
This page explains how to debug Pods running (or crashing) on a Node.
-->
本頁解釋如何在節點上除錯執行中（或崩潰）的 Pod。

## {{% heading "prerequisites" %}}

<!--
* Your {{< glossary_tooltip text="Pod" term_id="pod" >}} should already be
  scheduled and running. If your Pod is not yet running, start with [Debugging
  Pods](/docs/tasks/debug/debug-application/).
* For some of the advanced debugging steps you need to know on which Node the
  Pod is running and have shell access to run commands on that Node. You don't
  need that access to run the standard debug steps that use `kubectl`.
-->
* 你的 {{< glossary_tooltip text="Pod" term_id="pod" >}} 應該已經被排程並正在執行中，
  如果你的 Pod 還沒有執行，請參閱[除錯 Pod](/zh-cn/docs/tasks/debug/debug-application/)。

* 對於一些高階除錯步驟，你應該知道 Pod 具體執行在哪個節點上，並具有在該節點上執行命令的 shell 訪問許可權。
  你不需要任何訪問許可權就可以使用 `kubectl` 去執行一些標準除錯步驟。

<!--
## Using `kubectl describe pod` to fetch details about pods
-->
## 使用 `kubectl describe pod` 命令獲取 Pod 詳情

<!--
For this example we'll use a Deployment to create two pods, similar to the earlier example.
-->
與之前的例子類似，我們使用一個 Deployment 來建立兩個 Pod。

{{< codenew file="application/nginx-with-request.yaml" >}}

<!--
Create deployment by running following command:
-->
使用如下命令建立 Deployment：

```shell
kubectl apply -f https://k8s.io/examples/application/nginx-with-request.yaml
```

```
deployment.apps/nginx-deployment created
```

<!--
Check pod status by following command:
-->
使用如下命令檢視 Pod 狀態：

```shell
kubectl get pods
```

```
NAME                                READY   STATUS    RESTARTS   AGE
nginx-deployment-67d4bdd6f5-cx2nz   1/1     Running   0          13s
nginx-deployment-67d4bdd6f5-w6kd7   1/1     Running   0          13s
```

<!--
We can retrieve a lot more information about each of these pods using `kubectl describe pod`. For example:
-->
我們可以使用 `kubectl describe pod` 命令來查詢每個 Pod 的更多資訊，比如：

```shell
kubectl describe pod nginx-deployment-67d4bdd6f5-w6kd7
```

```none
Name:         nginx-deployment-67d4bdd6f5-w6kd7
Namespace:    default
Priority:     0
Node:         kube-worker-1/192.168.0.113
Start Time:   Thu, 17 Feb 2022 16:51:01 -0500
Labels:       app=nginx
              pod-template-hash=67d4bdd6f5
Annotations:  <none>
Status:       Running
IP:           10.88.0.3
IPs:
  IP:           10.88.0.3
  IP:           2001:db8::1
Controlled By:  ReplicaSet/nginx-deployment-67d4bdd6f5
Containers:
  nginx:
    Container ID:   containerd://5403af59a2b46ee5a23fb0ae4b1e077f7ca5c5fb7af16e1ab21c00e0e616462a
    Image:          nginx
    Image ID:       docker.io/library/nginx@sha256:2834dc507516af02784808c5f48b7cbe38b8ed5d0f4837f16e78d00deb7e7767
    Port:           80/TCP
    Host Port:      0/TCP
    State:          Running
      Started:      Thu, 17 Feb 2022 16:51:05 -0500
    Ready:          True
    Restart Count:  0
    Limits:
      cpu:     500m
      memory:  128Mi
    Requests:
      cpu:        500m
      memory:     128Mi
    Environment:  <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-bgsgp (ro)
Conditions:
  Type              Status
  Initialized       True 
  Ready             True 
  ContainersReady   True 
  PodScheduled      True 
Volumes:
  kube-api-access-bgsgp:
    Type:                    Projected (a volume that contains injected data from multiple sources)
    TokenExpirationSeconds:  3607
    ConfigMapName:           kube-root-ca.crt
    ConfigMapOptional:       <nil>
    DownwardAPI:             true
QoS Class:                   Guaranteed
Node-Selectors:              <none>
Tolerations:                 node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                             node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:
  Type    Reason     Age   From               Message
  ----    ------     ----  ----               -------
  Normal  Scheduled  34s   default-scheduler  Successfully assigned default/nginx-deployment-67d4bdd6f5-w6kd7 to kube-worker-1
  Normal  Pulling    31s   kubelet            Pulling image "nginx"
  Normal  Pulled     30s   kubelet            Successfully pulled image "nginx" in 1.146417389s
  Normal  Created    30s   kubelet            Created container nginx
  Normal  Started    30s   kubelet            Started container nginx
```

<!--
Here you can see configuration information about the container(s) and Pod (labels, resource requirements, etc.), as well as status information about the container(s) and Pod (state, readiness, restart count, events, etc.).
-->
在這裡，你可以看到有關容器和 Pod 的配置資訊（標籤、資源需求等），
以及有關容器和 Pod 的狀態資訊（狀態、就緒、重啟計數、事件等） 。

<!--
The container state is one of Waiting, Running, or Terminated. Depending on the state, additional information will be provided -- here you can see that for a container in Running state, the system tells you when the container started.
-->
容器狀態是 Waiting、Running 和 Terminated 之一。
根據狀態的不同，還有對應的額外的資訊 —— 在這裡你可以看到，
對於處於執行狀態的容器，系統會告訴你容器的啟動時間。

<!--
Ready tells you whether the container passed its last readiness probe. (In this case, the container does not have a readiness probe configured; the container is assumed to be ready if no readiness probe is configured.)
-->
Ready 指示是否通過了最後一個就緒態探測。
(在本例中，容器沒有配置就緒態探測；如果沒有配置就緒態探測，則假定容器已經就緒。)

<!--
Restart Count tells you how many times the container has been restarted; this information can be useful for detecting crash loops in containers that are configured with a restart policy of 'always.'
-->
Restart Count 告訴你容器已重啟的次數；
這些資訊對於定位配置了 “Always” 重啟策略的容器持續崩潰問題非常有用。

<!--
Currently the only Condition associated with a Pod is the binary Ready condition, which indicates that the pod is able to service requests and should be added to the load balancing pools of all matching services.
-->
目前，唯一與 Pod 有關的狀態是 Ready 狀況，該狀況表明 Pod 能夠為請求提供服務，
並且應該新增到相應服務的負載均衡池中。

<!--
Lastly, you see a log of recent events related to your Pod. The system compresses multiple identical events by indicating the first and last time it was seen and the number of times it was seen. "From" indicates the component that is logging the event, "SubobjectPath" tells you which object (e.g. container within the pod) is being referred to, and "Reason" and "Message" tell you what happened.
-->
最後，你還可以看到與 Pod 相關的近期事件。
系統透過指示第一次和最後一次看到事件以及看到該事件的次數來壓縮多個相同的事件。
“From” 標明記錄事件的元件，
“SubobjectPath” 告訴你引用了哪個物件（例如 Pod 中的容器），
“Reason” 和 “Message” 告訴你發生了什麼。

<!--
## Example: debugging Pending Pods

A common scenario that you can detect using events is when you've created a Pod that won't fit on any node. For example, the Pod might request more resources than are free on any node, or it might specify a label selector that doesn't match any nodes. Let's say we created the previous Deployment with 5 replicas (instead of 2) and requesting 600 millicores instead of 500, on a four-node cluster where each (virtual) machine has 1 CPU. In that case one of the Pods will not be able to schedule. (Note that because of the cluster addon pods such as fluentd, skydns, etc., that run on each node, if we requested 1000 millicores then none of the Pods would be able to schedule.)
-->
## 例子: 除錯 Pending 狀態的 Pod

可以使用事件來除錯的一個常見的場景是，你建立 Pod 無法被排程到任何節點。
比如，Pod 請求的資源比較多，沒有任何一個節點能夠滿足，或者它指定了一個標籤，沒有節點可匹配。
假定我們建立之前的 Deployment 時指定副本數是 5（不再是 2），並且請求 600 毫核（不再是 500），
對於一個 4 個節點的叢集，若每個節點只有 1 個 CPU，這時至少有一個 Pod 不能被排程。
（需要注意的是，其他叢集外掛 Pod，比如 fluentd、skydns 等等會在每個節點上執行，
如果我們需求 1000 毫核，將不會有 Pod 會被排程。）

```shell
kubectl get pods
```

```
NAME                                READY     STATUS    RESTARTS   AGE
nginx-deployment-1006230814-6winp   1/1       Running   0          7m
nginx-deployment-1006230814-fmgu3   1/1       Running   0          7m
nginx-deployment-1370807587-6ekbw   1/1       Running   0          1m
nginx-deployment-1370807587-fg172   0/1       Pending   0          1m
nginx-deployment-1370807587-fz9sd   0/1       Pending   0          1m
```

<!--
To find out why the nginx-deployment-1370807587-fz9sd pod is not running, we can use `kubectl describe pod` on the pending Pod and look at its events:
-->
為了查詢 Pod nginx-deployment-1370807587-fz9sd 沒有執行的原因，我們可以使用
`kubectl describe pod` 命令描述 Pod，檢視其事件：

```shell
kubectl describe pod nginx-deployment-1370807587-fz9sd
```

```none
  Name:		nginx-deployment-1370807587-fz9sd
  Namespace:	default
  Node:		/
  Labels:		app=nginx,pod-template-hash=1370807587
  Status:		Pending
  IP:
  Controllers:	ReplicaSet/nginx-deployment-1370807587
  Containers:
    nginx:
      Image:	nginx
      Port:	80/TCP
      QoS Tier:
        memory:	Guaranteed
        cpu:	Guaranteed
      Limits:
        cpu:	1
        memory:	128Mi
      Requests:
        cpu:	1
        memory:	128Mi
      Environment Variables:
  Volumes:
    default-token-4bcbi:
      Type:	Secret (a volume populated by a Secret)
      SecretName:	default-token-4bcbi
  Events:
    FirstSeen	LastSeen	Count	From			        SubobjectPath	Type		Reason			    Message
    ---------	--------	-----	----			        -------------	--------	------			    -------
    1m		    48s		    7	    {default-scheduler }			        Warning		FailedScheduling	pod (nginx-deployment-1370807587-fz9sd) failed to fit in any node
  fit failure on node (kubernetes-node-6ta5): Node didn't have enough resource: CPU, requested: 1000, used: 1420, capacity: 2000
  fit failure on node (kubernetes-node-wul5): Node didn't have enough resource: CPU, requested: 1000, used: 1100, capacity: 2000
```

<!--
Here you can see the event generated by the scheduler saying that the Pod failed to schedule for reason `FailedScheduling` (and possibly others).  The message tells us that there were not enough resources for the Pod on any of the nodes.
-->
這裡你可以看到由排程器記錄的事件，它表明了 Pod 不能被排程的原因是 `FailedScheduling`（也可能是其他值）。
其 message 部分表明沒有任何節點擁有足夠多的資源。

<!--
To correct this situation, you can use `kubectl scale` to update your Deployment to specify four or fewer replicas. (Or you could leave the one Pod pending, which is harmless.)
-->
要糾正這種情況，可以使用 `kubectl scale` 更新 Deployment，以指定 4 個或更少的副本。
(或者你可以讓 Pod 繼續保持這個狀態，這是無害的。)

<!--
Events such as the ones you saw at the end of `kubectl describe pod` are persisted in etcd and provide high-level information on what is happening in the cluster. To list all events you can use
-->
你在 `kubectl describe pod` 結尾處看到的事件都儲存在 etcd 中，
並提供關於叢集中正在發生的事情的高階資訊。
如果需要列出所有事件，可使用命令：

```shell
kubectl get events
```

<!--
but you have to remember that events are namespaced. This means that if you're interested in events for some namespaced object (e.g. what happened with Pods in namespace `my-namespace`) you need to explicitly provide a namespace to the command:
-->
但是，需要注意的是，事件是區分名字空間的。
如果你對某些名字空間域的物件（比如 `my-namespace` 名字下的 Pod）的事件感興趣,
你需要顯式地在命令列中指定名字空間：

```shell
kubectl get events --namespace=my-namespace
```

<!--
To see events from all namespaces, you can use the `--all-namespaces` argument.
-->
檢視所有 namespace 的事件，可使用 `--all-namespaces` 引數。

<!--
In addition to `kubectl describe pod`, another way to get extra information about a pod (beyond what is provided by `kubectl get pod`) is to pass the `-o yaml` output format flag to `kubectl get pod`. This will give you, in YAML format, even more information than `kubectl describe pod`--essentially all of the information the system has about the Pod. Here you will see things like annotations (which are key-value metadata without the label restrictions, that is used internally by Kubernetes system components), restart policy, ports, and volumes.
-->
除了 `kubectl describe pod` 以外，另一種獲取 Pod 額外資訊（除了 `kubectl get pod`）的方法
是給 `kubectl get pod` 增加 `-o yaml` 輸出格式引數。
該命令將以 YAML 格式為你提供比 `kubectl describe pod` 更多的資訊 —— 實際上是系統擁有的關於 Pod 的所有資訊。
在這裡，你將看到註解（沒有標籤限制的鍵值元資料，由 Kubernetes 系統元件在內部使用）、
重啟策略、埠和卷等。

```shell
kubectl get pod nginx-deployment-1006230814-6winp -o yaml
```

```yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: "2022-02-17T21:51:01Z"
  generateName: nginx-deployment-67d4bdd6f5-
  labels:
    app: nginx
    pod-template-hash: 67d4bdd6f5
  name: nginx-deployment-67d4bdd6f5-w6kd7
  namespace: default
  ownerReferences:
  - apiVersion: apps/v1
    blockOwnerDeletion: true
    controller: true
    kind: ReplicaSet
    name: nginx-deployment-67d4bdd6f5
    uid: 7d41dfd4-84c0-4be4-88ab-cedbe626ad82
  resourceVersion: "1364"
  uid: a6501da1-0447-4262-98eb-c03d4002222e
spec:
  containers:
  - image: nginx
    imagePullPolicy: Always
    name: nginx
    ports:
    - containerPort: 80
      protocol: TCP
    resources:
      limits:
        cpu: 500m
        memory: 128Mi
      requests:
        cpu: 500m
        memory: 128Mi
    terminationMessagePath: /dev/termination-log
    terminationMessagePolicy: File
    volumeMounts:
    - mountPath: /var/run/secrets/kubernetes.io/serviceaccount
      name: kube-api-access-bgsgp
      readOnly: true
  dnsPolicy: ClusterFirst
  enableServiceLinks: true
  nodeName: kube-worker-1
  preemptionPolicy: PreemptLowerPriority
  priority: 0
  restartPolicy: Always
  schedulerName: default-scheduler
  securityContext: {}
  serviceAccount: default
  serviceAccountName: default
  terminationGracePeriodSeconds: 30
  tolerations:
  - effect: NoExecute
    key: node.kubernetes.io/not-ready
    operator: Exists
    tolerationSeconds: 300
  - effect: NoExecute
    key: node.kubernetes.io/unreachable
    operator: Exists
    tolerationSeconds: 300
  volumes:
  - name: kube-api-access-bgsgp
    projected:
      defaultMode: 420
      sources:
      - serviceAccountToken:
          expirationSeconds: 3607
          path: token
      - configMap:
          items:
          - key: ca.crt
            path: ca.crt
          name: kube-root-ca.crt
      - downwardAPI:
          items:
          - fieldRef:
              apiVersion: v1
              fieldPath: metadata.namespace
            path: namespace
status:
  conditions:
  - lastProbeTime: null
    lastTransitionTime: "2022-02-17T21:51:01Z"
    status: "True"
    type: Initialized
  - lastProbeTime: null
    lastTransitionTime: "2022-02-17T21:51:06Z"
    status: "True"
    type: Ready
  - lastProbeTime: null
    lastTransitionTime: "2022-02-17T21:51:06Z"
    status: "True"
    type: ContainersReady
  - lastProbeTime: null
    lastTransitionTime: "2022-02-17T21:51:01Z"
    status: "True"
    type: PodScheduled
  containerStatuses:
  - containerID: containerd://5403af59a2b46ee5a23fb0ae4b1e077f7ca5c5fb7af16e1ab21c00e0e616462a
    image: docker.io/library/nginx:latest
    imageID: docker.io/library/nginx@sha256:2834dc507516af02784808c5f48b7cbe38b8ed5d0f4837f16e78d00deb7e7767
    lastState: {}
    name: nginx
    ready: true
    restartCount: 0
    started: true
    state:
      running:
        startedAt: "2022-02-17T21:51:05Z"
  hostIP: 192.168.0.113
  phase: Running
  podIP: 10.88.0.3
  podIPs:
  - ip: 10.88.0.3
  - ip: 2001:db8::1
  qosClass: Guaranteed
  startTime: "2022-02-17T21:51:01Z"
```

<!--
## Examining pod logs {#examine-pod-logs}

First, look at the logs of the affected container:

```shell
kubectl logs ${POD_NAME} ${CONTAINER_NAME}
```

If your container has previously crashed, you can access the previous container's crash log with:

```shell
kubectl logs --previous ${POD_NAME} ${CONTAINER_NAME}
```
-->
## 檢查 Pod 的日誌 {#examine-pod-logs}

首先，檢視受到影響的容器的日誌：

```shell
kubectl logs ${POD_NAME} ${CONTAINER_NAME}
```

如果你的容器之前崩潰過，你可以透過下面命令訪問之前容器的崩潰日誌：

```shell
kubectl logs --previous ${POD_NAME} ${CONTAINER_NAME}
```

<!--
## Debugging with container exec {#container-exec}

If the {{< glossary_tooltip text="container image" term_id="image" >}} includes
debugging utilities, as is the case with images built from Linux and Windows OS
base images, you can run commands inside a specific container with
`kubectl exec`:

```shell
kubectl exec ${POD_NAME} -c ${CONTAINER_NAME} -- ${CMD} ${ARG1} ${ARG2} ... ${ARGN}
```

{{< note >}}
`-c ${CONTAINER_NAME}` is optional. You can omit it for Pods that only contain a single container.
{{< /note >}}

As an example, to look at the logs from a running Cassandra pod, you might run

```shell
kubectl exec cassandra -- cat /var/log/cassandra/system.log
```

You can run a shell that's connected to your terminal using the `-i` and `-t`
arguments to `kubectl exec`, for example:

```shell
kubectl exec -it cassandra -- sh
```

For more details, see [Get a Shell to a Running Container](
/docs/tasks/debug/debug-application/get-shell-running-container/).
-->
## 使用容器 exec 進行除錯 {#container-exec}

如果 {{< glossary_tooltip text="容器映象" term_id="image" >}} 包含除錯程式，
比如從 Linux 和 Windows 作業系統基礎映象構建的映象，你可以使用 `kubectl exec` 命令
在特定的容器中執行一些命令：

```shell
kubectl exec ${POD_NAME} -c ${CONTAINER_NAME} -- ${CMD} ${ARG1} ${ARG2} ... ${ARGN}
```
{{< note >}}
`-c ${CONTAINER_NAME}` 是可選擇的。如果Pod中僅包含一個容器，就可以忽略它。
{{< /note >}}

例如，要檢視正在執行的 Cassandra pod中的日誌，可以執行：

```shell
kubectl exec cassandra -- cat /var/log/cassandra/system.log
```

你可以在 `kubectl exec` 命令後面加上 `-i` 和 `-t` 來執行一個連線到你的終端的 Shell，比如：

```shell
kubectl exec -it cassandra -- sh
```

若要了解更多內容，可檢視[獲取正在執行容器的 Shell](/zh-cn/docs/tasks/debug/debug-application/get-shell-running-container/)。

<!--
## Debugging with an ephemeral debug container {#ephemeral-container}

{{< feature-state state="beta" for_k8s_version="v1.23" >}}

{{< glossary_tooltip text="Ephemeral containers" term_id="ephemeral-container" >}}
are useful for interactive troubleshooting when `kubectl exec` is insufficient
because a container has crashed or a container image doesn't include debugging
utilities, such as with [distroless images](
https://github.com/GoogleContainerTools/distroless).
-->
## 使用臨時除錯容器來進行除錯 {#ephemeral-container}

{{< feature-state state="beta" for_k8s_version="v1.23" >}}

當由於容器崩潰或容器映象不包含除錯程式（例如[無發行版映象](https://github.com/GoogleContainerTools/distroless)等）
而導致 `kubectl exec` 無法執行時，{{< glossary_tooltip text="臨時容器" term_id="ephemeral-container" >}}對於排除互動式故障很有用。

<!--
### Example debugging using ephemeral containers {#ephemeral-container-example}

You can use the `kubectl debug` command to add ephemeral containers to a
running Pod. First, create a pod for the example:

```shell
kubectl run ephemeral-demo --image=k8s.gcr.io/pause:3.1 --restart=Never
```

The examples in this section use the `pause` container image because it does not
contain debugging utilities, but this method works with all container
images.
-->
## 使用臨時容器來除錯的例子 {#ephemeral-container-example}

你可以使用 `kubectl debug` 命令來給正在執行中的 Pod 增加一個臨時容器。
首先，像示例一樣建立一個 pod：

```shell
kubectl run ephemeral-demo --image=k8s.gcr.io/pause:3.1 --restart=Never
```

{{< note >}}
本節示例中使用 `pause` 容器映象，因為它不包含除錯程式，但是這個方法適用於所有容器映象。
{{< /note >}}

<!--
If you attempt to use `kubectl exec` to create a shell you will see an error
because there is no shell in this container image.
-->
如果你嘗試使用 `kubectl exec` 來建立一個 shell，你將會看到一個錯誤，因為這個容器映象中沒有 shell。

```shell
kubectl exec -it ephemeral-demo -- sh
```

```
OCI runtime exec failed: exec failed: container_linux.go:346: starting container process caused "exec: \"sh\": executable file not found in $PATH": unknown
```

<!-- 
You can instead add a debugging container using `kubectl debug`. If you
specify the `-i`/`--interactive` argument, `kubectl` will automatically attach
to the console of the Ephemeral Container.
-->

你可以改為使用 `kubectl debug` 新增除錯容器。
如果你指定 `-i` 或者 `--interactive` 引數，`kubectl` 將自動掛接到臨時容器的控制檯。

```shell
kubectl debug -it ephemeral-demo --image=busybox:1.28 --target=ephemeral-demo
```

```
Defaulting debug container name to debugger-8xzrl.
If you don't see a command prompt, try pressing enter.
/ #
```

<!--
This command adds a new busybox container and attaches to it. The `--target`
parameter targets the process namespace of another container. It's necessary
here because `kubectl run` does not enable [process namespace sharing](
/docs/tasks/configure-pod-container/share-process-namespace/) in the pod it
creates.

{{< note >}}
The `--target` parameter must be supported by the {{< glossary_tooltip
text="Container Runtime" term_id="container-runtime" >}}. When not supported,
the Ephemeral Container may not be started, or it may be started with an
isolated process namespace so that `ps` does not reveal processes in other
containers.
{{< /note >}}

You can view the state of the newly created ephemeral container using `kubectl describe`:
-->
此命令新增一個新的 busybox 容器並將其掛接到該容器。`--target` 引數指定另一個容器的程序名稱空間。
這是必需的，因為 `kubectl run` 不能在它建立的pod中啟用
[共享程序名稱空間](/zh-cn/docs/tasks/configure-pod-container/share-process-namespace/)。

{{< note >}}
{{< glossary_tooltip text="容器執行時" term_id="container-runtime" >}}必須支援 `--target` 引數。
如果不支援，則臨時容器可能不會啟動，或者可能使用隔離的程序名稱空間啟動，
以便 `ps` 不顯示其他容器內的程序。
{{< /note >}}

你可以使用 `kubectl describe` 檢視新建立的臨時容器的狀態：

```shell
kubectl describe pod ephemeral-demo
```

```
...
Ephemeral Containers:
  debugger-8xzrl:
    Container ID:   docker://b888f9adfd15bd5739fefaa39e1df4dd3c617b9902082b1cfdc29c4028ffb2eb
    Image:          busybox
    Image ID:       docker-pullable://busybox@sha256:1828edd60c5efd34b2bf5dd3282ec0cc04d47b2ff9caa0b6d4f07a21d1c08084
    Port:           <none>
    Host Port:      <none>
    State:          Running
      Started:      Wed, 12 Feb 2020 14:25:42 +0100
    Ready:          False
    Restart Count:  0
    Environment:    <none>
    Mounts:         <none>
...
```

<!--
Use `kubectl delete` to remove the Pod when you're finished:
-->
使用 `kubectl delete` 來移除已經結束掉的 Pod：

```shell
kubectl delete pod ephemeral-demo
```

<!--
## Debugging using a copy of the Pod
-->
## 透過 Pod 副本除錯

<!--
Sometimes Pod configuration options make it difficult to troubleshoot in certain
situations. For example, you can't run `kubectl exec` to troubleshoot your
container if your container image does not include a shell or if your application
crashes on startup. In these situations you can use `kubectl debug` to create a
copy of the Pod with configuration values changed to aid debugging.
-->
有些時候 Pod 的配置引數使得在某些情況下很難執行故障排查。
例如，在容器映象中不包含 shell 或者你的應用程式在啟動時崩潰的情況下，
就不能透過執行 `kubectl exec` 來排查容器故障。
在這些情況下，你可以使用 `kubectl debug` 來建立 Pod 的副本，透過更改配置幫助除錯。

<!--
### Copying a Pod while adding a new container
-->
### 在新增新的容器時建立 Pod 副本

<!--
Adding a new container can be useful when your application is running but not
behaving as you expect and you'd like to add additional troubleshooting
utilities to the Pod.
-->
當應用程式正在執行但其表現不符合預期時，你會希望在 Pod 中新增額外的除錯工具，
這時新增新容器是很有用的。

<!--
For example, maybe your application's container images are built on `busybox`
but you need debugging utilities not included in `busybox`. You can simulate
this scenario using `kubectl run`:
-->
例如，應用的容器映象是建立在 `busybox` 的基礎上，
但是你需要 `busybox` 中並不包含的除錯工具。
你可以使用 `kubectl run` 模擬這個場景:

```shell
kubectl run myapp --image=busybox:1.28 --restart=Never -- sleep 1d
```
<!--
Run this command to create a copy of `myapp` named `myapp-debug` that adds a
new Ubuntu container for debugging:
-->
透過執行以下命令，建立 `myapp` 的一個名為 `myapp-debug` 的副本，
新增了一個用於除錯的 Ubuntu 容器，

```shell
kubectl debug myapp -it --image=ubuntu --share-processes --copy-to=myapp-debug
```

```
Defaulting debug container name to debugger-w7xmf.
If you don't see a command prompt, try pressing enter.
root@myapp-debug:/#
```
<!--
* `kubectl debug` automatically generates a container name if you don't choose
  one using the `--container` flag.
* The `-i` flag causes `kubectl debug` to attach to the new container by
  default.  You can prevent this by specifying `--attach=false`. If your session
  becomes disconnected you can reattach using `kubectl attach`.
* The `--share-processes` allows the containers in this Pod to see processes
  from the other containers in the Pod. For more information about how this
  works, see [Share Process Namespace between Containers in a Pod](
  /docs/tasks/configure-pod-container/share-process-namespace/).
-->
{{< note >}}
* 如果你沒有使用 `--container` 指定新的容器名，`kubectl debug` 會自動生成的。
* 預設情況下，`-i` 標誌使 `kubectl debug` 附加到新容器上。
  你可以透過指定 `--attach=false` 來防止這種情況。
  如果你的會話斷開連線，你可以使用 `kubectl attach` 重新連線。
* `--share-processes` 允許在此 Pod 中的其他容器中檢視該容器的程序。
  參閱[在 Pod 中的容器之間共享程序名稱空間](/zh-cn/docs/tasks/configure-pod-container/share-process-namespace/)
  獲取更多資訊。
{{< /note >}}

<!--
Don't forget to clean up the debugging Pod when you're finished with it:
-->
不要忘了清理除錯 Pod：

```shell
kubectl delete pod myapp myapp-debug
```

<!--
### Copying a Pod while changing its command
-->
### 在改變 Pod 命令時建立 Pod 副本

<!--
Sometimes it's useful to change the command for a container, for example to
add a debugging flag or because the application is crashing.
-->
有時更改容器的命令很有用，例如新增除錯標誌或因為應用崩潰。

<!--
To simulate a crashing application, use `kubectl run` to create a container
that immediately exits:
-->
為了模擬應用崩潰的場景，使用 `kubectl run` 命令建立一個立即退出的容器：

```
kubectl run --image=busybox:1.28 myapp -- false
```

<!--
You can see using `kubectl describe pod myapp` that this container is crashing:
-->
使用 `kubectl describe pod myapp` 命令，你可以看到容器崩潰了：

```
Containers:
  myapp:
    Image:         busybox
    ...
    Args:
      false
    State:          Waiting
      Reason:       CrashLoopBackOff
    Last State:     Terminated
      Reason:       Error
      Exit Code:    1
```

<!--
You can use `kubectl debug` to create a copy of this Pod with the command
changed to an interactive shell:
-->
你可以使用 `kubectl debug` 命令建立該 Pod 的一個副本，
在該副本中命令改變為互動式 shell：

```
kubectl debug myapp -it --copy-to=myapp-debug --container=myapp -- sh
```

```
If you don't see a command prompt, try pressing enter.
/ #
```

<!--
Now you have an interactive shell that you can use to perform tasks like
checking filesystem paths or running the container command manually.
-->
現在你有了一個可以執行類似檢查檔案系統路徑或者手動執行容器命令的互動式 shell。 

<!--
* To change the command of a specific container you must
  specify its name using `--container` or `kubectl debug` will instead
  create a new container to run the command you specified.
* The `-i` flag causes `kubectl debug` to attach to the container by default.
  You can prevent this by specifying `--attach=false`. If your session becomes
  disconnected you can reattach using `kubectl attach`.
-->
{{< note >}}
* 要更改指定容器的命令，你必須用 `--container` 命令指定容器的名字，
  否則 `kubectl debug` 將建立一個新的容器執行你指定的命令。
* 預設情況下，標誌 `-i` 使 `kubectl debug` 附加到容器。
  你可透過指定 `--attach=false` 來防止這種情況。
  如果你的斷開連線，可以使用 `kubectl attach` 重新連線。
{{< /note >}} 

<!--
Don't forget to clean up the debugging Pod when you're finished with it:
-->
不要忘了清理除錯 Pod：

```shell
kubectl delete pod myapp myapp-debug
```
<!--
### Copying a Pod while changing container images

In some situations you may want to change a misbehaving Pod from its normal
production container images to an image containing a debugging build or
additional utilities.

As an example, create a Pod using `kubectl run`:
-->
### 在更改容器映象時建立 Pod 副本

在某些情況下，你可能想從正常生產容器映象中
把行為異常的 Pod 改變為包含除錯版本或者附加應用的映象。

下面的例子，用 `kubectl run`建立一個 Pod：

```
kubectl run myapp --image=busybox:1.28 --restart=Never -- sleep 1d
```
<!--
Now use `kubectl debug` to make a copy and change its container image
to `ubuntu`:
-->
現在可以使用 `kubectl debug`  建立一個副本
並改變容器映象為 `ubuntu`：

```
kubectl debug myapp --copy-to=myapp-debug --set-image=*=ubuntu
```

<!--
The syntax of `--set-image` uses the same `container_name=image` syntax as
`kubectl set image`. `*=ubuntu` means change the image of all containers
to `ubuntu`.

Don't forget to clean up the debugging Pod when you're finished with it:
-->
`--set-image` 與 `container_name=image` 使用相同的 `kubectl set image` 語法。
`*=ubuntu` 表示把所有容器的映象改為 `ubuntu`。

```shell
kubectl delete pod myapp myapp-debug
```

<!--
## Debugging via a shell on the node {#node-shell-session}

If none of these approaches work, you can find the Node on which the Pod is
running and create a privileged Pod running in the host namespaces. To create
an interactive shell on a node using `kubectl debug`, run:
-->
## 在節點上透過 shell 來進行除錯 {#node-shell-session}

如果這些方法都不起作用，你可以找到執行 Pod 的節點，然後在節點上部署一個執行在宿主名字空間的特權 Pod。

你可以透過`kubectl debug` 在節點上建立一個互動式 shell：

```shell
kubectl debug node/mynode -it --image=ubuntu
```

```
Creating debugging pod node-debugger-mynode-pdx84 with container debugger on node mynode.
If you don't see a command prompt, try pressing enter.
root@ek8s:/#
```

<!--
When creating a debugging session on a node, keep in mind that:

* `kubectl debug` automatically generates the name of the new Pod based on
  the name of the Node.
* The container runs in the host IPC, Network, and PID namespaces.
* The root filesystem of the Node will be mounted at `/host`.

Don't forget to clean up the debugging Pod when you're finished with it:
-->
當在節點上建立除錯會話，注意以下要點：
* `kubectl debug` 基於節點的名字自動生成新的 Pod 的名字。
* 新的除錯容器執行在宿主命名空間裡（IPC, 網路 還有PID名稱空間）。
* 節點的根檔案系統會被掛載在 `/host`。

當你完成節點除錯時，不要忘記清理除錯 Pod：

```shell
kubectl delete pod node-debugger-mynode-pdx84
```