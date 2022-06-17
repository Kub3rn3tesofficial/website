---
title: 埠和協議
content_type: reference
weight: 50
---
<!--
---
title: Ports and Protocols
content_type: reference
weight: 50
---
-->

<!--
When running Kubernetes in an environment with strict network boundaries, such 
as on-premises datacenter with physical network firewalls or Virtual 
Networks in Public Cloud, it is useful to be aware of the ports and protocols 
used by Kubernetes components
-->
當你在一個有嚴格網路邊界的環境裡執行 Kubernetes，例如擁有物理網路防火牆或者擁有公有云中虛擬網路的自有資料中心，瞭解 Kubernetes 元件使用了哪些埠和協議是非常有用的。

<!--
## Control plane

| Protocol | Direction | Port Range | Purpose                 | Used By                   |
|----------|-----------|------------|-------------------------|---------------------------|
| TCP      | Inbound   | 6443       | Kubernetes API server   | All                       |
| TCP      | Inbound   | 2379-2380  | etcd server client API  | kube-apiserver, etcd      |
| TCP      | Inbound   | 10250      | Kubelet API             | Self, Control plane       |
| TCP      | Inbound   | 10259      | kube-scheduler          | Self                      |
| TCP      | Inbound   | 10257      | kube-controller-manager | Self                      |

Although etcd ports are included in control plane section, you can also host your own
etcd cluster externally or on custom ports. 
-->
## 控制面  {#control-plane}

| 協議     | 方向      | 埠範圍     | 目的                     | 使用者                     |
|----------|-----------|------------|-------------------------|---------------------------|
| TCP      | 入站       | 6443       | Kubernetes API server   | 所有                       |
| TCP      | 入站       | 2379-2380  | etcd server client API  | kube-apiserver, etcd      |
| TCP      | 入站       | 10250      | Kubelet API             | 自身, 控制面                |
| TCP      | 入站       | 10259      | kube-scheduler          | 自身                       |
| TCP      | 入站       | 10257      | kube-controller-manager | 自身                       |

儘管 etcd 的埠也列舉在控制面的部分，但你也可以在外部自己託管 etcd 叢集或者自定義埠。

<!--
## Worker node(s) {#node}

| Protocol | Direction | Port Range  | Purpose               | Used By                 |
|----------|-----------|-------------|-----------------------|-------------------------|
| TCP      | Inbound   | 10250       | Kubelet API           | Self, Control plane     |
| TCP      | Inbound   | 30000-32767 | NodePort Services†    | All                     |

† Default port range for [NodePort Services](/docs/concepts/services-networking/service/).

All default port numbers can be overridden. When custom ports are used those 
ports need to be open instead of defaults mentioned here. 

One common example is API server port that is sometimes switched
to 443. Alternatively, the default port is kept as is and API server is put 
behind a load balancer that listens on 443 and routes the requests to API server
on the default port.
-->
## 工作節點  {#node}

| 協議     | 方向      | 埠範圍     | 目的                     | 使用者                  |
|----------|-----------|-------------|-----------------------|-------------------------|
| TCP      | 入站       | 10250       | Kubelet API           | 自身, 控制面             |
| TCP      | 入站       | 30000-32767 | NodePort Services†    | 所有                    |

† [NodePort Services](/zh-cn/docs/concepts/services-networking/service/)的預設埠範圍。

所有預設埠都可以重新配置。當使用自定義的埠時，你需要開啟這些埠來代替這裡提到的預設埠。

一個常見的例子是 API 伺服器的埠有時會配置為443。或者你也可以使用預設埠，把 API 伺服器放到一個監聽443 埠的負載均衡器後面，並且路由所有請求到 API 伺服器的預設埠。
