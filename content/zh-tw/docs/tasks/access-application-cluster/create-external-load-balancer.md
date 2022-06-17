---
title: 建立外部負載均衡器
content_type: task
weight: 80
---

<!--
title: Create an External Load Balancer
content_type: task
weight: 80
-->

<!-- overview -->

<!--
This page shows how to create an external load balancer.
-->
本文展示如何建立一個外部負載均衡器。

<!--
When creating a {{< glossary_tooltip text="Service" term_id="service" >}}, you have
the option of automatically creating a cloud load balancer. This provides an
externally-accessible IP address that sends traffic to the correct port on your cluster
nodes,
_provided your cluster runs in a supported environment and is configured with
the correct cloud load balancer provider package_.
-->
建立 {{< glossary_tooltip text="服務" term_id="service" >}} 時，你可以選擇自動建立雲網絡負載均衡器。
負載均衡器提供外部可訪問的 IP 地址，可將流量傳送到叢集節點上的正確埠上
（ **假設叢集在支援的環境中執行，並配置了正確的雲負載均衡器驅動包**）。

<!--
You can also use an {{< glossary_tooltip term_id="ingress" >}} in place of Service.
For more information, check the [Ingress](/docs/concepts/services-networking/ingress/)
documentation.
-->
你還可以使用 {{< glossary_tooltip text="Ingress" term_id="ingress" >}} 代替 Service。
更多資訊，請參閱 [Ingress](/zh-cn/docs/concepts/services-networking/ingress/) 文件。

## {{% heading "prerequisites" %}}

{{< include "task-tutorial-prereqs.md" >}}

<!--
Your cluster must be running in a cloud or other environment that already has support
for configuring external load balancers.
-->
你的叢集必須在已經支援配置外部負載均衡器的雲或其他環境中執行。

<!-- steps -->

<!--
## Create a Service

### Create a Service from a manifest

To create an external load balancer, add the following line to your
Service manifest:
-->
## 建立服務   {#create-a-service}

### 基於清單檔案建立服務   {#create-a-service-from-a-manifest}

要建立外部負載均衡器，請將以下內容新增到你的 Service 清單檔案：

```yaml
    type: LoadBalancer
```

<!--
Your manifest might then look like:
-->
你的清單檔案可能會如下所示：

```yaml
apiVersion: v1
kind: Service
metadata:
  name: example-service
spec:
  selector:
    app: example
  ports:
    - port: 8765
      targetPort: 9376
  type: LoadBalancer
```

<!--
### Create a Service using kubectl

You can alternatively create the service with the `kubectl expose` command and
its `--type=LoadBalancer` flag:
-->
### 使用 kubectl 建立 Service   {#create-a-service-using-kubectl}

你也可以使用 `kubectl expose` 命令及其 `--type=LoadBalancer` 引數建立服務：

```bash
kubectl expose deployment example --port=8765 --target-port=9376 \
        --name=example-service --type=LoadBalancer
```

<!--
This command creates a new Service using the same selectors as the referenced
resource (in the case of the example above, a
{{< glossary_tooltip text="Deployment" term_id="deployment" >}} named `example`).

For more information, including optional flags, refer to the
[`kubectl expose` reference](/docs/reference/generated/kubectl/kubectl-commands/#expose).
-->
此命令透過使用與引用資源（在上面的示例的情況下，名為 `example` 的
{{< glossary_tooltip text="Deployment" term_id="deployment" >}}）
相同的選擇器來建立一個新的服務。

更多資訊（包括更多的可選引數），請參閱
[`kubectl expose` 指南](/docs/reference/generated/kubectl/kubectl-commands/#expose)。

<!--
## Finding your IP address

You can find the IP address created for your service by getting the service
information through `kubectl`:
-->
## 找到你的 IP 地址   {#finding-your-ip-address}

你可以透過 `kubectl` 獲取服務資訊，找到為你的服務建立的 IP 地址：

```bash
kubectl describe services example-service
```

<!--
which should produce output similar to:
-->
這將獲得類似如下輸出：

```
Name:                     example-service
Namespace:                default
Labels:                   app=example
Annotations:              <none>
Selector:                 app=example
Type:                     LoadBalancer
IP Families:              <none>
IP:                       10.3.22.96
IPs:                      10.3.22.96
LoadBalancer Ingress:     192.0.2.89
Port:                     <unset>  8765/TCP
TargetPort:               9376/TCP
NodePort:                 <unset>  30593/TCP
Endpoints:                172.17.0.3:9376
Session Affinity:         None
External Traffic Policy:  Cluster
Events:                   <none>
```

<!--
The load balancer's IP address is listed next to `LoadBalancer Ingress`.
-->
負載均衡器的 IP 地址列在 `LoadBalancer Ingress` 旁邊。

<!--
If you are running your service on Minikube, you can find the assigned IP address and port with:
-->
{{< note >}}
如果你在 Minikube 上執行服務，你可以透過以下命令找到分配的 IP 地址和埠：

```bash
minikube service example-service --url
```
{{< /note >}}

<!--
## Preserving the client source IP

By default, the source IP seen in the target container is *not the original
source IP* of the client. To enable preservation of the client IP, the following
fields can be configured in the `.spec` of the Service:
-->
## 保留客戶端源 IP   {#preserving-the-client-source-ip}

預設情況下，目標容器中看到的源 IP 將**不是客戶端的原始源 IP**。
要啟用保留客戶端 IP，可以在服務的 `.spec` 中配置以下欄位：

<!--
* `.spec.externalTrafficPolicy` - denotes if this Service desires to route
  external traffic to node-local or cluster-wide endpoints. There are two available
  options: `Cluster` (default) and `Local`. `Cluster` obscures the client source
  IP and may cause a second hop to another node, but should have good overall
  load-spreading. `Local` preserves the client source IP and avoids a second hop
  for LoadBalancer and NodePort type Services, but risks potentially imbalanced
  traffic spreading.
-->
* `.spec.externalTrafficPolicy` - 表示此 Service 是否希望將外部流量路由到節點本地或叢集範圍的端點。
  有兩個可用選項：`Cluster`（預設）和 `Local`。
  `Cluster` 隱藏了客戶端源 IP，可能導致第二跳到另一個節點，但具有良好的整體負載分佈。
  `Local` 保留客戶端源 IP 並避免 LoadBalancer 和 NodePort 型別服務的第二跳，
  但存在潛在的不均衡流量傳播風險。

<!--
* `.spec.healthCheckNodePort` - specifies the health check node port
  (numeric port number) for the service. If you don't specify
  `healthCheckNodePort`, the service controller allocates a port from your
  cluster's NodePort range.
  You can configure that range by setting an API server command line option,
  `--service-node-port-range`. The Service will use the user-specified
  `healthCheckNodePort` value if you specify it, provided that the
  Service `type` is set to LoadBalancer and `externalTrafficPolicy` is set
  to `Local`.
-->
* `.spec.healthCheckNodePort` - 指定服務的 healthcheck nodePort（數字埠號）。
  如果你未指定 `healthCheckNodePort`，服務控制器從叢集的 NodePort 範圍內分配一個埠。
  你可以透過設定 API 伺服器的命令列選項 `--service-node-port-range` 來配置上述範圍。
  在服務 `type` 設定為 LoadBalancer 並且 `externalTrafficPolicy` 設定為 `Local` 時，
  Service 將會使用使用者指定的 `healthCheckNodePort` 值（如果你指定了它）。

<!--
Setting `externalTrafficPolicy` to Local in the Service manifest
activates this feature. For example:
-->
可以透過在服務的清單檔案中將 `externalTrafficPolicy` 設定為 Local 來啟用此功能。比如：

```yaml
apiVersion: v1
kind: Service
metadata:
  name: example-service
spec:
  selector:
    app: example
  ports:
    - port: 8765
      targetPort: 9376
  externalTrafficPolicy: Local
  type: LoadBalancer
```

<!--
### Caveats and limitations when preserving source IPs

Load balancing services from some cloud providers do not let you configure different weights for each target.

With each target weighted equally in terms of sending traffic to Nodes, external
traffic is not equally load balanced across different Pods. The external load balancer
is unaware of the number of Pods on each node that are used as a target.
-->
### 保留源 IP 時的注意事項和限制   {#caveats-and-limitations-when-preserving-source-ips}

一些雲服務供應商的負載均衡服務不允許你為每個目標配置不同的權重。

由於每個目標在向節點發送流量方面的權重相同，因此外部流量不會在不同 Pod 之間平均負載。
外部負載均衡器不知道每個節點上用作目標的 Pod 數量。

<!--
Where `NumServicePods <<  _NumNodes` or `NumServicePods >> NumNodes`, a fairly close-to-equal
distribution will be seen, even without weights.

Internal pod to pod traffic should behave similar to ClusterIP services, with equal probability across all pods.
-->
在 `NumServicePods <<  _NumNodes` 或 `NumServicePods >> NumNodes` 時，
即使沒有權重，也會看到接近相等的分佈。

內部 Pod 到 Pod 的流量應該與 ClusterIP 服務類似，所有 Pod 的機率相同。

<!--
## Garbage Collecting Load Balancers

{{< feature-state for_k8s_version="v1.17" state="stable" >}}

In usual case, the correlating load balancer resources in cloud provider should
be cleaned up soon after a LoadBalancer type Service is deleted. But it is known
that there are various corner cases where cloud resources are orphaned after the
associated Service is deleted. Finalizer Protection for Service LoadBalancers was
introduced to prevent this from happening. By using finalizers, a Service resource
will never be deleted until the correlating load balancer resources are also deleted.
-->
## 回收負載均衡器   {#garbage-collecting-load-balancers}

{{< feature-state for_k8s_version="v1.17" state="stable" >}}

在通常情況下，應在刪除 LoadBalancer 型別 Service 後立即清除雲服務供應商中的相關負載均衡器資源。
但是，眾所周知，在刪除關聯的服務後，雲資源被孤立的情況很多。
引入了針對服務負載均衡器的終結器保護，以防止這種情況發生。
透過使用終結器，在刪除相關的負載均衡器資源之前，也不會刪除服務資源。

<!--
Specifically, if a Service has `type` LoadBalancer, the service controller will attach
a finalizer named `service.kubernetes.io/load-balancer-cleanup`.
The finalizer will only be removed after the load balancer resource is cleaned up.
This prevents dangling load balancer resources even in corner cases such as the
service controller crashing.
-->
具體來說，如果服務具有 `type` LoadBalancer，則服務控制器將附加一個名為
`service.kubernetes.io/load-balancer-cleanup` 的終結器。
僅在清除負載均衡器資源後才能刪除終結器。
即使在諸如服務控制器崩潰之類的極端情況下，這也可以防止負載均衡器資源懸空。

<!--
## External load balancer providers

It is important to note that the datapath for this functionality is provided by a load balancer external to the Kubernetes cluster.
-->
## 外部負載均衡器供應商   {#external-load-balancer-providers}

請務必注意，此功能的資料路徑由 Kubernetes 叢集外部的負載均衡器提供。

<!--
When the Service `type` is set to LoadBalancer, Kubernetes provides functionality equivalent to `type` equals ClusterIP to pods
within the cluster and extends it by programming the (external to Kubernetes) load balancer with entries for the nodes
hosting the relevant Kubernetes pods. The Kubernetes control plane automates the creation of the external load balancer,
health checks (if needed), and packet filtering rules (if needed). Once the cloud provider allocates an IP address for the load
balancer, the control plane looks up that external IP address and populates it into the Service object.
-->
當服務 `type` 設定為 LoadBalancer 時，Kubernetes 向叢集中的 Pod 提供的功能等同於
`type` 設定為 ClusterIP，並透過使用託管了相關 Kubernetes Pod 的節點作為條目對負載均衡器
（從外部到 Kubernetes）進行程式設計來擴充套件它。
Kubernetes 控制平面自動建立外部負載均衡器、健康檢查（如果需要）和包過濾規則（如果需要）。
一旦雲服務供應商為負載均衡器分配了 IP 地址，控制平面就會查詢該外部 IP 地址並將其填充到 Service 物件中。

## {{% heading "whatsnext" %}}

<!--
* Read about [Service](/docs/concepts/services-networking/service/)
* Read about [Ingress](/docs/concepts/services-networking/ingress/)
* Read [Connecting Applications with Services](/docs/concepts/services-networking/connect-applications-service/)
-->
* 閱讀[服務](/zh-cn/docs/concepts/services-networking/service/)
* 閱讀 [Ingress](/zh-cn/docs/concepts/services-networking/ingress/)
* 閱讀[使用 Service 連線到應用](/zh-cn/docs/concepts/services-networking/connect-applications-service/)

