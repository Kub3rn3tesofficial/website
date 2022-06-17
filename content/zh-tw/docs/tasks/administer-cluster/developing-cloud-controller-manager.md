---
title: 開發雲控制器管理器
content_type: task
---

<!--
reviewers:
- luxas
- thockin
- wlan0
title: Developing Cloud Controller Manager
content_type: concept
-->

<!-- overview -->

{{< feature-state for_k8s_version="v1.11" state="beta" >}}

{{< glossary_definition term_id="cloud-controller-manager" length="all">}}

<!-- body -->

<!--
## Background

Since cloud providers develop and release at a different pace compared to the Kubernetes project, abstracting the provider-specific code to the `cloud-controller-manager` binary allows cloud vendors to evolve independently from the core Kubernetes code.
-->
## 背景

由於雲驅動的開發和釋出與 Kubernetes 專案本身步調不同，將特定於雲環境的程式碼抽象到
`cloud-controller-manager` 二進位制元件有助於雲廠商獨立於 Kubernetes
核心程式碼推進其驅動開發。

<!--
The Kubernetes project provides skeleton cloud-controller-manager code with Go interfaces to allow you (or your cloud provider) to plug in your own implementations. This means that a cloud provider can implement a cloud-controller-manager by importing packages from Kubernetes core; each cloudprovider will register their own code by calling `cloudprovider.RegisterCloudProvider` to update a global variable of available cloud providers.
-->
Kubernetes 專案提供 cloud-controller-manager 的框架程式碼，其中包含 Go 語言的介面，
便於你（或者你的雲驅動提供者）接駁你自己的實現。這意味著每個雲驅動可以透過從
Kubernetes 核心程式碼匯入軟體包來實現一個 cloud-controller-manager；
每個雲驅動會透過呼叫 `cloudprovider.RegisterCloudProvider` 介面來註冊其自身實現程式碼，
從而更新一個用來記錄可用雲驅動的全域性變數。

<!--
## Developing
-->
## 開發

### 樹外（Out of Tree）

<!--
To build an out-of-tree cloud-controller-manager for your cloud, follow these steps:
-->
要為你的雲環境構建一個樹外（Out-of-Tree）雲控制器管理器：

<!--
1. Create a go package with an implementation that satisfies [cloudprovider.Interface](https://github.com/kubernetes/cloud-provider/blob/master/cloud.go).
2. Use [main.go in cloud-controller-manager](https://github.com/kubernetes/kubernetes/blob/master/cmd/cloud-controller-manager/main.go) from Kubernetes core as a template for your main.go. As mentioned above, the only difference should be the cloud package that will be imported.
3. Import your cloud package in `main.go`, ensure your package has an `init` block to run [cloudprovider.RegisterCloudProvider](https://github.com/kubernetes/cloud-provider/blob/master/plugins.go).
-->
1. 使用滿足 [`cloudprovider.Interface`](https://github.com/kubernetes/cloud-provider/blob/master/cloud.go)
   介面的實現來建立一個 Go 語言包。
2. 使用來自 Kubernetes 核心程式碼庫的
   [cloud-controller-manager 中的 main.go](https://github.com/kubernetes/kubernetes/blob/master/cmd/cloud-controller-manager/main.go)
   作為 `main.go` 的模板。如上所述，唯一的區別應該是將匯入的雲包不同。
3. 在 `main.go` 中匯入你的雲包，確保你的包有一個 `init` 塊來執行
   [`cloudprovider.RegisterCloudProvider`](https://github.com/kubernetes/cloud-provider/blob/master/plugins.go)。

<!--
Many cloud providers publish their controller manager code as open source. If you are creating
a new cloud-controller-manager from scratch, you could take an existing out-of-tree cloud
controller manager as your starting point.
-->
很多雲驅動都將其控制器管理器程式碼以開原始碼的形式公開。
如果你在開發一個新的 cloud-controller-manager，你可以選擇某個樹外（Out-of-Tree）
雲控制器管理器作為出發點。

### 樹內（In Tree）

<!--
For in-tree cloud providers, you can run the in-tree cloud controller manager as a {{< glossary_tooltip term_id="daemonset" >}} in your cluster. See [Cloud Controller Manager Administration](/docs/tasks/administer-cluster/running-cloud-controller/) for more details.
-->
對於樹內（In-Tree）驅動，你可以將樹內雲控制器管理器作為叢集中的
{{< glossary_tooltip term_id="daemonset" text="DaemonSet" >}} 來執行。
有關詳細資訊，請參閱[雲控制器管理器管理](/zh-cn/docs/tasks/administer-cluster/running-cloud-controller/)。

