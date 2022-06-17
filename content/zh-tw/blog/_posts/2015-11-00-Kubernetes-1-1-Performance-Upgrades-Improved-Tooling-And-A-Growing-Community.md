---
title: " Kubernetes 1.1 效能升級，工具改進和社群不斷壯大  "
date: 2015-11-09
slug: kubernetes-1-1-performance-upgrades-improved-tooling-and-a-growing-community
url: /zh-cn/blog/2015/11/Kubernetes-1-1-Performance-Upgrades-Improved-Tooling-And-A-Growing-Community
---
<!--
---
title: " Kubernetes 1.1 Performance upgrades, improved tooling and a growing community  "
date: 2015-11-09
slug: kubernetes-1-1-performance-upgrades-improved-tooling-and-a-growing-community
url: /zh-cn/blog/2015/11/Kubernetes-1-1-Performance-Upgrades-Improved-Tooling-And-A-Growing-Community
---
-->
<!--
Since the Kubernetes 1.0 release in July, we’ve seen tremendous adoption by companies building distributed systems to manage their container clusters. We’re also been humbled by the rapid growth of the community who help make Kubernetes better everyday. We have seen commercial offerings such as Tectonic by CoreOS and RedHat Atomic Host emerge to deliver deployment and support of Kubernetes. And a growing ecosystem has added Kubernetes support including tool vendors such as Sysdig and Project Calico.  
-->
自從 Kubernetes 1.0 在七月釋出以來，我們已經看到大量公司採用建立分散式系統來管理其容器叢集。
我們也對幫助 Kubernetes 社群變得更好，迅速發展的人感到欽佩。
我們已經看到諸如 CoreOS 的 Tectonic 和 RedHat Atomic Host 之類的商業產品應運而生，用以提供 Kubernetes 的部署和支援。
一個不斷髮展的生態系統增加了 Kubernetes 的支援，包括 Sysdig 和 Project Calico 等工具供應商。

<!--
With the help of hundreds of contributors, we’re proud to announce the availability of Kubernetes 1.1, which offers major performance upgrades, improved tooling, and new features that make applications even easier to build and deploy.  
-->
在數百名貢獻者的幫助下，我們自豪地宣佈 Kubernetes 1.1 的可用性，它提供了主要的效能升級、改進的工具和新特性，使應用程式更容易構建和部署。

<!--
Some of the work we’d like to highlight includes:
-->
我們想強調的一些工作包括:

<!--
- **Substantial performance improvements** : We have architected Kubernetes from day one to handle Google-scale workloads, and our customers have put it through their paces. In Kubernetes 1.1, we have made further investments to ensure that you can run in extremely high-scale environments; later this week, we will be sharing examples of running thousand node clusters, and running over a million QPS against a single cluster.&nbsp;
-->
- **實質性的效能提升** ：從第一天開始，我們就設計了 Kubernetes 來處理 Google 規模的工作負載，而我們的客戶已經按照自己的進度進行了調整。
 在 Kubernetes 1.1 中，我們進行了進一步的投資，以確保您可以在超大規模環境中執行；
 本週晚些時候，我們將分享執行數千個節點叢集，並針對單個叢集執行超過一百萬個 QPS 的示例。

<!--
- **Significant improvement in network throughput** : Running Google-scale workloads also requires Google-scale networking. In Kubernetes 1.1, we have included an option to use native IP tables offering an 80% reduction in tail latency, an almost complete elimination of CPU overhead and improvements in reliability and system architecture ensuring Kubernetes can handle high-scale throughput well into the future.&nbsp;
-->
- **網路吞吐量顯著提高** : 執行 Google 規模的工作負載也需要 Google 規模的網路。
 在 Kubernetes 1.1 中，我們提供了使用本機IP表的選項，可將尾部延遲減少80％，幾乎完全消除了CPU開銷，並提高了可靠性和系統架構，從而確保Kubernetes可以很好地處理未來的大規模吞吐量。 

<!--
- **Horizontal pod autoscaling (Beta)**: Many workloads can go through spiky periods of utilization, resulting in uneven experiences for your users. Kubernetes now has support for horizontal pod autoscaling, meaning your pods can scale up and down based on CPU usage. Read more about [Horizontal pod autoscaling](http://kubernetes.io/v1.1/docs/user-guide/horizontal-pod-autoscaler.html).&nbsp;
-->
- **水平 Pod 自動縮放 (測試版)**：許多工作負載可能會經歷尖峰的使用期，從而給使用者帶來不均勻的體驗。
 Kubernetes 現在支援水平 Pod 自動縮放，這意味著您的 Pod 可以根據 CPU 使用率進行縮放。
 閱讀有關[水平 Pod 自動縮放](http://kubernetes.io/v1.1/docs/user-guide/horizontal-pod-autoscaler.html)的更多資訊。

<!--
- **HTTP load balancer (Beta)**: Kubernetes now has the built-in ability to route HTTP traffic based on the packets introspection. This means you can have ‘http://foo.com/bar’ go to one service, and ‘http://foo.com/meep’ go to a completely independent service. Read more about the [Ingress object](http://kubernetes.io/v1.1/docs/user-guide/ingress.html).&nbsp;
-->
- **HTTP 負載均衡器 (測試版)**：Kubernetes 現在具有基於資料包自省功能來路由 HTTP 流量的內建功能。
 這意味著您可以讓 ‘http://foo.com/bar’ 使用一項服務，而 ‘http://foo.com/meep’ 使用一項完全獨立的服務。
 閱讀有關[Ingress物件](http://kubernetes.io/v1.1/docs/user-guide/ingress.html)的更多資訊。

<!--
- **Job objects (Beta)**: We’ve also had frequent request for integrated batch jobs, such as processing a batch of images to create thumbnails or a particularly large data file that has been broken down into many chunks. [Job objects](https://github.com/kubernetes/kubernetes/blob/master/docs/user-guide/jobs.md#writing-a-job-spec) introduces a new API object that runs a workload, restarts it if it fails, and keeps trying until it’s successfully completed. Read more about the[Job object](http://kubernetes.io/v1.1/docs/user-guide/jobs.html).&nbsp;
-->
- **Job 物件 (測試版)**：我們也經常需要整合的批處理 Job ，例如處理一批影象以建立縮圖，或者將特別大的資料檔案分解成很多塊。
 [Job 物件](https://github.com/kubernetes/kubernetes/blob/master/docs/user-guide/jobs.md#writing-a-job-spec)引入了一個新的 API 物件，該物件執行工作負載，
如果失敗，則重新啟動它，並繼續嘗試直到成功完成。
 閱讀有關[Job 物件](http://kubernetes.io/v1.1/docs/user-guide/jobs.html)的更多資訊。

<!--
- **New features to shorten the test cycle for developers** : We continue to work on making developing for applications for Kubernetes quick and easy. Two new features that speeds developer’s workflows include the ability to run containers interactively, and improved schema validation to let you know if there are any issues with your configuration files before you deploy them.&nbsp;
-->
- **新功能可縮短開發人員的測試周期** :我們將繼續致力於快速便捷地為 Kubernetes 開發應用程式。
 加快開發人員工作流程的兩項新功能包括以互動方式執行容器的功能，以及改進的架構驗證功能，可在部署配置檔案之前讓您知道配置檔案是否存在任何問題。

<!--
- **Rolling update improvements** : Core to the DevOps movement is being able to release new updates without any affect on a running service. Rolling updates now ensure that updated pods are healthy before continuing the update.&nbsp;
-->
- **滾動更新改進** : DevOps 運動的核心是能夠釋出新更新，而不會影響正在執行的服務。
 滾動更新現在可確保在繼續更新之前，已更新的 Pod 狀況良好。

<!--
- And many more. For a complete list of updates, see the [1.1. release](https://github.com/kubernetes/kubernetes/releases) notes on GitHub&nbsp;
-->
- 還有很多。有關更新的完整列表，請參見[1.1. 釋出](https://github.com/kubernetes/kubernetes/releases)在GitHub上的筆記

<!--
Today, we’re also proud to mark the inaugural Kubernetes conference, [KubeCon](https://kubecon.io/), where some 400 community members along with dozens of vendors are in attendance supporting the Kubernetes project.  
-->
今天，我們也很榮幸地慶祝首屆Kubernetes會議[KubeCon](https://kubecon.io/)，約有400個社群成員以及數十個供應商參加支援 Kubernetes 專案的會議。

<!--
We’d love to highlight just a few of the many partners making Kubernetes better:  
-->
我們想強調幾個使 Kubernetes 變得更好的眾多合作伙伴中的幾位：

<!--
> “We are betting our major product, Tectonic – which enables any company to deploy, manage and secure its containers anywhere – on Kubernetes because we believe it is the future of the data center. The release of Kubernetes 1.1 is another major milestone that will create more widespread adoption of distributed systems and containers, and puts us on a path that will inevitably lead to a whole new generation of products and services.” – Alex Polvi, CEO, CoreOS.
-->
> “我們押注我們的主要產品 Tectonic-它能使任何公司都能在任何地方部署、管理和保護其容器-在 Kubernetes 上使用，因為我們認為這是資料中心的未來。
  Kubernetes 1.1 的釋出是另一個重要的里程碑，它將使分散式系統和容器得到更廣泛的採用，並使我們走上一條必將導致新一代產品和服務的道路。” – CoreOS 執行長Alex Polvi

<!--
> “Univa’s customers are looking for scalable, enterprise-caliber solutions to simplify managing container and non-container workloads in the enterprise. We selected Kubernetes as a foundational element of our new Navops suite which will help IT and DevOps rapidly integrate containerized workloads into their production systems and extend these workloads into cloud services.” – Gary Tyreman, CEO, Univa.
-->
> “Univa 的客戶正在尋找可擴充套件的企業級解決方案，以簡化企業中容器和非容器工作負載的管理。
 我們選擇Kubernetes作為我們新的 Navops 套件的基礎，它將幫助 IT 和 DevOps 將容器化工作負載快速整合到他們的生產系統中，並將這些工作負載擴充套件到雲服務中。” – Univa 執行長 Gary Tyreman

<!--
> “The tremendous customer demand we’re seeing to run containers at scale with Kubernetes is a critical element driving growth in our professional services business at Redapt. As a trusted advisor, it’s great to have a tool like Kubernetes in our tool belt to help our customers achieve their objectives.” – Paul Welch, SR VP Cloud Solutions, Redapt
-->
> “我們看到透過 Kubernetes 大規模執行容器的巨大客戶需求是推動 Redapt 專業服務業務增長的關鍵因素。
  作為值得信賴的顧問，在我們的工具帶中有像 Kubernetes 這樣的工具能夠幫助我們的客戶實現他們的目標，這是非常棒的。“ – Redapt SR 雲解決方案副總裁 Paul Welch 
>
<!--
As we mentioned above, we would love your help:  
-->
如上所述，我們希望得到您的幫助:

<!--
- Get involved with the Kubernetes project on [GitHub](https://github.com/kubernetes/kubernetes)&nbsp;
- Connect with the community on [Slack](http://slack.kubernetes.io/)
- Follow us on Twitter [@Kubernetesio](https://twitter.com/kubernetesio) for latest updates&nbsp;
- Post questions (or answer questions) on Stackoverflow&nbsp;
- Get started running, deploying, and using Kubernetes [guides](/docs/tutorials/kubernetes-basics/);
-->
- 在 [GitHub](https://github.com/kubernetes/kubernetes)上參與 Kubernetes 專案；
- 透過 [Slack](http://slack.kubernetes.io/) 與社群聯絡；
- 關注我們的 Twitter [@Kubernetesio](https://twitter.com/kubernetesio) 獲取最新資訊；
- 在 Stackoverflow 上釋出問題（或回答問題）
- 開始執行，部署和使用 Kubernetes [指南](/docs/tutorials/kubernetes-basics/)；

<!--
But, most of all, just let us know how you are transforming your business using Kubernetes, and how we can help you do it even faster. Thank you for your support!  
-->
但是，最重要的是，請讓我們知道您是如何使用 Kubernetes 改變您的業務的，以及我們如何可以幫助您更快地做到這一點。謝謝您的支援!

<!--
&nbsp;- David Aronchick, Senior Product Manager for Kubernetes and Google Container Engine
-->
&nbsp;- David Aronchick, Kubernetes 和谷歌容器引擎的高階產品經理

