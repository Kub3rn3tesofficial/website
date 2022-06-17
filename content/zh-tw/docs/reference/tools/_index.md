---
title: 其他工具
content_type: concept
weight: 80
no_list: true
---

<!-- 
title: Other Tools
reviewers:
- janetkuo
content_type: concept
weight: 80
no_list: true
-->

<!-- overview -->
<!-- 
Kubernetes contains several tools to help you work with the Kubernetes system.
-->
Kubernetes 包含多種工具來幫助你使用 Kubernetes 系統。


<!-- body -->

<!--  
[`crictl`](https://github.com/kubernetes-sigs/cri-tools) is a command-line
interface for inspecting and debugging {{<glossary_tooltip term_id="cri" text="CRI">}}-compatible
container runtimes.
-->
## crictl

[`crictl`](https://github.com/kubernetes-sigs/cri-tools)
是用於檢查和除錯相容 {{<glossary_tooltip term_id="cri" text="CRI">}} 的容器執行時的命令列介面。

<!-- 
## Dashboard

[`Dashboard`](/docs/tasks/access-application-cluster/web-ui-dashboard/), the web-based user interface of Kubernetes, allows you to deploy containerized applications
to a Kubernetes cluster, troubleshoot them, and manage the cluster and its resources itself.
-->
## 儀表盤

[`Dashboard`](/zh-cn/docs/tasks/access-application-cluster/web-ui-dashboard/)，
基於 Web 的 Kubernetes 使用者介面，
允許你將容器化的應用程式部署到 Kubernetes 叢集，
對它們進行故障排查，並管理叢集及其資源本身。

<!-- 
## Helm

[Helm](https://helm.sh/) is a tool for managing packages of pre-configured
Kubernetes resources. These packages are known as _Helm charts_.
-->
## Helm
{{% thirdparty-content single="true" %}}

[Helm](https://helm.sh/)
是一個用於管理預配置 Kubernetes 資源包的工具。這些包被稱為“Helm 圖表”。

<!-- 
Use Helm to:

* Find and use popular software packaged as Kubernetes charts
* Share your own applications as Kubernetes charts
* Create reproducible builds of your Kubernetes applications
* Intelligently manage your Kubernetes manifest files
* Manage releases of Helm packages
-->
使用 Helm 來：

* 查詢和使用打包為 Kubernetes 圖表的流行軟體
* 將你自己的應用程式共享為 Kubernetes 圖表
* 為你的 Kubernetes 應用程式建立可重現的構建
* 智慧管理你的 Kubernetes 清單檔案
* 管理 Helm 包的釋出

<!-- 
## Kompose

[`Kompose`](https://github.com/kubernetes/kompose) is a tool to help Docker Compose users move to Kubernetes.
-->
## Kompose

[`Kompose`](https://github.com/kubernetes/kompose)
是一個幫助 Docker Compose 使用者遷移到 Kubernetes 的工具。

<!-- 
Use Kompose to:

* Translate a Docker Compose file into Kubernetes objects
* Go from local Docker development to managing your application via Kubernetes
* Convert v1 or v2 Docker Compose `yaml` files or [Distributed Application Bundles](https://docs.docker.com/compose/bundles/)
-->

使用 Kompose：

* 將 Docker Compose 檔案翻譯成 Kubernetes 物件
* 從本地 Docker 開發轉到透過 Kubernetes 管理你的應用程式
* 轉換 Docker Compose v1 或 v2 版本的 `yaml` 檔案或[分散式應用程式包](https://docs.docker.com/compose/bundles/)

## Kui

<!--
[`Kui`](https://github.com/kubernetes-sigs/kui) is a GUI tool that takes your normal
`kubectl` command line requests and responds with graphics.
-->
[`Kui`](https://github.com/kubernetes-sigs/kui)
是一個接受你標準的 `kubectl` 命令列請求並以圖形響應的 GUI 工具。

<!--
Kui takes the normal `kubectl` command line requests and responds with graphics. Instead 
of ASCII tables, Kui provides a GUI rendering with tables that you can sort.
-->
Kui 接受標準的 `kubectl` 命令列工具並以圖形響應。
Kui 提供包含可排序表格的 GUI 渲染，而不是 ASCII 表格。

<!--
Kui lets you:

* Directly click on long, auto-generated resource names instead of copying and pasting
* Type in `kubectl` commands and see them execute, even sometimes faster than `kubectl` itself
* Query a {{< glossary_tooltip text="Job" term_id="job">}} and see its execution rendered
  as a waterfall diagram
* Click through resources in your cluster using a tabbed UI 
-->
Kui 讓你能夠：

* 直接點選長的、自動生成的資源名稱，而不是複製和貼上
* 輸入 `kubectl` 命令並檢視它們的執行，有時甚至比 `kubectl` 本身更快
* 查詢 {{<glossary_tooltip text="Job" term_id="job">}} 並檢視其執行渲染為瀑布圖
* 使用選項卡式 UI 在叢集中單擊資源

## Minikube

<!--
[`minikube`](https://minikube.sigs.k8s.io/docs/) is a tool that
runs a single-node Kubernetes cluster locally on your workstation for
development and testing purposes.
-->
[`minikube`](https://minikube.sigs.k8s.io/docs/)
是一種在你的工作站上本地執行單節點 Kubernetes 叢集的工具，用於開發和測試。