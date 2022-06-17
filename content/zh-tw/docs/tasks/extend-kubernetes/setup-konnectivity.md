---
title: 設定 Konnectivity 服務
content_type: task
weight: 70
---

<!-- overview -->
<!--
The Konnectivity service provides a TCP level proxy for the control plane to cluster
communication.
-->
Konnectivity 服務為控制平面提供叢集通訊的 TCP 級別代理。

## {{% heading "prerequisites" %}}

<!--
You need to have a Kubernetes cluster, and the kubectl command-line tool must
be configured to communicate with your cluster. It is recommended to run this
tutorial on a cluster with at least two nodes that are not acting as control
plane hosts. If you do not already have a cluster, you can create one by using
[minikube](https://minikube.sigs.k8s.io/docs/tutorials/multi_node/).
-->
你需要有一個 Kubernetes 叢集，並且 kubectl 命令可以與叢集通訊。
建議在至少有兩個不充當控制平面主機的節點的叢集上執行本教程。
如果你還沒有叢集，可以使用
[minikube](https://minikube.sigs.k8s.io/docs/tutorials/multi_node/) 建立一個叢集。

<!-- steps -->
<!--
## Configure the Konnectivity service

The following steps require an egress configuration, for example:
-->
## 配置 Konnectivity 服務

接下來的步驟需要出口配置，比如：

{{< codenew file="admin/konnectivity/egress-selector-configuration.yaml" >}}

<!--
You need to configure the API Server to use the Konnectivity service
and direct the network traffic to the cluster nodes:

1. Make sure that
[Service Account Token Volume Projection](/docs/tasks/configure-pod-container/configure-service-account/#service-account-token-volume-projection)
feature enabled in your cluster. It is enabled by default since Kubernetes v1.20.
1. Create an egress configuration file such as `admin/konnectivity/egress-selector-configuration.yaml`.
1. Set the `--egress-selector-config-file` flag of the API Server to the path of
your API Server egress configuration file.
1. If you use UDS connection, add volumes config to the kube-apiserver:
   ```yaml
   spec:
     containers:
       volumeMounts:
       - name: konnectivity-uds
         mountPath: /etc/kubernetes/konnectivity-server
         readOnly: false
     volumes:
     - name: konnectivity-uds
       hostPath:
         path: /etc/kubernetes/konnectivity-server
         type: DirectoryOrCreate
   ```
-->
你需要配置 API 伺服器來使用 Konnectivity 服務，並將網路流量定向到叢集節點：

確保[服務賬號令牌卷投射](/zh-cn/docs/tasks/configure-pod-container/configure-service-account/#service-account-token-volume-projection)
特性被啟用。該特性自 Kubernetes v1.20 起預設已被啟用。

1. 建立一個出站流量配置檔案，比如 `admin/konnectivity/egress-selector-configuration.yaml`。
1. 將 API 伺服器的 `--egress-selector-config-file` 引數設定為你的 API 伺服器的
   離站流量配置檔案路徑。
1. 如果你在使用 UDS 連線，須將卷配置新增到 kube-apiserver：
   ```yaml
   spec:
     containers:
       volumeMounts:
       - name: konnectivity-uds
         mountPath: /etc/kubernetes/konnectivity-server
         readOnly: false
     volumes:
     - name: konnectivity-uds
       hostPath:
         path: /etc/kubernetes/konnectivity-server
         type: DirectoryOrCreate
   ```

<!--
Generate or obtain a certificate and kubeconfig for konnectivity-server.
For example, you can use the OpenSSL command line tool to issue a X.509 certificate,
using the cluster CA certificate `/etc/kubernetes/pki/ca.crt` from a control-plane host.
-->
為 konnectivity-server 生成或者取得證書和 kubeconfig 檔案。
例如，你可以使用 OpenSSL 命令列工具，基於存放在某控制面主機上
`/etc/kubernetes/pki/ca.crt` 檔案中的叢集 CA 證書來
發放一個 X.509 證書，

```bash
openssl req -subj "/CN=system:konnectivity-server" -new -newkey rsa:2048 -nodes -out konnectivity.csr -keyout konnectivity.key -out konnectivity.csr
openssl x509 -req -in konnectivity.csr -CA /etc/kubernetes/pki/ca.crt -CAkey /etc/kubernetes/pki/ca.key -CAcreateserial -out konnectivity.crt -days 375 -sha256
SERVER=$(kubectl config view -o jsonpath='{.clusters..server}')
kubectl --kubeconfig /etc/kubernetes/konnectivity-server.conf config set-credentials system:konnectivity-server --client-certificate konnectivity.crt --client-key konnectivity.key --embed-certs=true
kubectl --kubeconfig /etc/kubernetes/konnectivity-server.conf config set-cluster kubernetes --server "$SERVER" --certificate-authority /etc/kubernetes/pki/ca.crt --embed-certs=true
kubectl --kubeconfig /etc/kubernetes/konnectivity-server.conf config set-context system:konnectivity-server@kubernetes --cluster kubernetes --user system:konnectivity-server
kubectl --kubeconfig /etc/kubernetes/konnectivity-server.conf config use-context system:konnectivity-server@kubernetes
rm -f konnectivity.crt konnectivity.key konnectivity.csr
```

<!--
Next, you need to deploy the Konnectivity server and agents.
[kubernetes-sigs/apiserver-network-proxy](https://github.com/kubernetes-sigs/apiserver-network-proxy)
is a reference implementation.

Deploy the Konnectivity server on your control plane node. The provided
`konnectivity-server.yaml` manifest assumes
that the Kubernetes components are deployed as a {{< glossary_tooltip text="static Pod"
term_id="static-pod" >}} in your cluster. If not, you can deploy the Konnectivity
server as a DaemonSet.
-->
接下來，你需要部署 Konnectivity 伺服器和代理。
[kubernetes-sigs/apiserver-network-proxy](https://github.com/kubernetes-sigs/apiserver-network-proxy)
是一個參考實現。

在控制面節點上部署 Konnectivity 服務。
下面提供的 `konnectivity-server.yaml` 配置清單假定在你的叢集中
Kubernetes 元件都是部署為{{< glossary_tooltip text="靜態 Pod" term_id="static-pod" >}} 的。
如果不是，你可以將 Konnectivity 服務部署為 DaemonSet。

{{< codenew file="admin/konnectivity/konnectivity-server.yaml" >}}

<!--
Then deploy the Konnectivity agents in your cluster:
-->
在你的叢集中部署 Konnectivity 代理：

{{< codenew file="admin/konnectivity/konnectivity-agent.yaml" >}}

<!--
Last, if RBAC is enabled in your cluster, create the relevant RBAC rules:
-->
最後，如果你的叢集啟用了 RBAC，請建立相關的 RBAC 規則：

{{< codenew file="admin/konnectivity/konnectivity-rbac.yaml" >}}

