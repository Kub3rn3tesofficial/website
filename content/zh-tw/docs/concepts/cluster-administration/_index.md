---
title: 叢集管理
weight: 100
content_type: concept
description: >
  關於建立和管理 Kubernetes 叢集的底層細節。
no_list: true
---

<!--
title: Cluster Administration
reviewers:
- davidopp
- lavalamp
weight: 100
content_type: concept
description: >
  Lower-level detail relevant to creating or administering a Kubernetes cluster.
no_list: true
-->

<!-- overview -->
<!--
The cluster administration overview is for anyone creating or administering a Kubernetes cluster.
It assumes some familiarity with core Kubernetes [concepts](/docs/concepts/).
-->
叢集管理概述面向任何建立和管理 Kubernetes 叢集的讀者人群。
我們假設你大概瞭解一些核心的 Kubernetes [概念](/zh-cn/docs/concepts/)。


<!-- body -->
<!--
## Planning a cluster

See the guides in [Setup](/docs/setup/) for examples of how to plan, set up, and configure Kubernetes clusters. The solutions listed in this article are called *distros*.

Not all distros are actively maintained. Choose distros which have been tested with a recent version of Kubernetes.

Before choosing a guide, here are some considerations:
-->
## 規劃叢集   {#planning-a-cluster}

查閱[安裝](/zh-cn/docs/setup/)中的指導，獲取如何規劃、建立以及配置 Kubernetes
叢集的示例。本文所列的文章稱為*發行版* 。

{{< note >}}
並非所有發行版都是被積極維護的。
請選擇使用最近 Kubernetes 版本測試過的發行版。
{{< /note >}}

在選擇一個指南前，有一些因素需要考慮：

<!--
- Do you want to try out Kubernetes on your computer, or do you want to build a high-availability, multi-node cluster? Choose distros best suited for your needs.
- Will you be using **a hosted Kubernetes cluster**, such as [Google Kubernetes Engine](https://cloud.google.com/kubernetes-engine/), or **hosting your own cluster**?
- Will your cluster be **on-premises**, or **in the cloud (IaaS)**? Kubernetes does not directly support hybrid clusters. Instead, you can set up multiple clusters.
- **If you are configuring Kubernetes on-premises**, consider which [networking model](/docs/concepts/cluster-administration/networking/) fits best.
- Will you be running Kubernetes on **"bare metal" hardware** or on **virtual machines (VMs)**?
- Do you **want to run a cluster**, or do you expect to do **active development of Kubernetes project code**? If the
  latter, choose an actively-developed distro. Some distros only use binary releases, but
  offer a greater variety of choices.
- Familiarize yourself with the [components](/docs/concepts/overview/components/) needed to run a cluster.
-->
- 你是打算在你的計算機上嘗試 Kubernetes，還是要構建一個高可用的多節點叢集？
  請選擇最適合你需求的發行版。
- 你正在使用類似 [Google Kubernetes Engine](https://cloud.google.com/kubernetes-engine/)
  這樣的**被託管的 Kubernetes 叢集**, 還是**管理你自己的叢集**？
- 你的叢集是在**本地**還是**雲（IaaS）** 上？Kubernetes 不能直接支援混合叢集。
  作為代替，你可以建立多個叢集。
- **如果你在本地配置 Kubernetes**，需要考慮哪種
  [網路模型](/zh-cn/docs/concepts/cluster-administration/networking/)最適合。
- 你的 Kubernetes 在**裸金屬硬體**上還是**虛擬機器（VMs）** 上執行？
- 你是想**執行一個叢集**，還是打算**參與開發 Kubernetes 專案程式碼**？
  如果是後者，請選擇一個處於開發狀態的發行版。
  某些發行版只提供二進位制釋出版，但提供更多的選擇。
- 讓你自己熟悉執行一個叢集所需的[元件](/zh-cn/docs/concepts/overview/components/)。

<!--
## Managing a cluster

* Learn how to [manage nodes](/docs/concepts/nodes/node/).

* Learn how to set up and manage the [resource quota](/docs/concepts/policy/resource-quotas/) for shared clusters.
-->
## 管理叢集   {#managing-a-cluster}

* 學習如何[管理節點](/zh-cn/docs/concepts/architecture/nodes/)。

* 學習如何設定和管理叢集共享的[資源配額](/zh-cn/docs/concepts/policy/resource-quotas/) 。

<!--
## Securing a cluster

* [Generate Certificates](/docs/tasks/administer-cluster/certificates/) describes the steps to generate certificates using different tool chains.
* [Kubernetes Container Environment](/docs/concepts/containers/container-environment/) describes the environment for Kubelet managed containers on a Kubernetes node.
* [Controlling Access to the Kubernetes API](/docs/reference/access-authn-authz/controlling-access/) describes how to set up permissions for users and service accounts.
* [Authenticating](/docs/reference/access-authn-authz/authentication/) explains authentication in Kubernetes, including the various authentication options.
* [Authorization](/docs/reference/access-authn-authz/authorization/) is separate from authentication, and controls how HTTP calls are handled.
* [Using Admission Controllers](/docs/reference/access-authn-authz/admission-controllers/) explains plug-ins which intercepts requests to the Kubernetes API server after authentication and authorization.
* [Using Sysctls in a Kubernetes Cluster](/docs/concepts/cluster-administration/sysctl-cluster/) describes to an administrator how to use the `sysctl` command-line tool to set kernel parameters .
* [Auditing](/docs/tasks/debug/debug-cluster/audit/) describes how to interact with Kubernetes' audit logs.
-->
## 保護叢集  {#securing-a-cluster}

* [生成證書](/zh-cn/docs/tasks/administer-cluster/certificates/)
  節描述了使用不同的工具鏈生成證書的步驟。
* [Kubernetes 容器環境](/zh-cn/docs/concepts/containers/container-environment/)
  描述了 Kubernetes 節點上由 Kubelet 管理的容器的環境。
* [控制到 Kubernetes API 的訪問](/zh-cn/docs/concepts/security/controlling-access/)
  描述瞭如何為使用者和 service accounts 建立許可權許可。
* [身份認證](/zh-cn/docs/reference/access-authn-authz/authentication/)
  節闡述了 Kubernetes 中的身份認證功能，包括許多認證選項。
* [鑑權](/zh-cn/docs/reference/access-authn-authz/authorization/)
  與身份認證不同，用於控制如何處理 HTTP 請求。
* [使用准入控制器](/zh-cn/docs/reference/access-authn-authz/admission-controllers)
  闡述了在認證和授權之後攔截到 Kubernetes API 服務的請求的外掛。
* [在 Kubernetes 叢集中使用 Sysctls](/zh-cn/docs/tasks/administer-cluster/sysctl-cluster/)
  描述了管理員如何使用 `sysctl` 命令列工具來設定核心引數。
* [審計](/zh-cn/docs/tasks/debug/debug-cluster/audit/)
  描述瞭如何與 Kubernetes 的審計日誌互動。

<!--
### Securing the kubelet

* [Master-Node communication](/docs/concepts/architecture/master-node-communication/)
* [TLS bootstrapping](/docs/reference/access-authn-authz/kubelet-tls-bootstrapping/)
* [Kubelet authentication/authorization](/docs/admin/kubelet-authentication-authorization/)
-->
### 保護 kubelet   {#securing-the-kubelet}

* [主控節點通訊](/zh-cn/docs/concepts/architecture/control-plane-node-communication/)
* [TLS 引導](/zh-cn/docs/reference/access-authn-authz/kubelet-tls-bootstrapping/)
* [Kubelet 認證/授權](/zh-cn/docs/reference/access-authn-authz/kubelet-authn-authz/)

<!--
## Optional Cluster Services

* [DNS Integration](/docs/concepts/services-networking/dns-pod-service/) describes how to resolve a DNS name directly to a Kubernetes service.
* [Logging and Monitoring Cluster Activity](/docs/concepts/cluster-administration/logging/) explains how logging in Kubernetes works and how to implement it.
-->
## 可選叢集服務   {#optional-cluster-services}

* [DNS 整合](/zh-cn/docs/concepts/services-networking/dns-pod-service/)
  描述瞭如何將一個 DNS 名解析到一個 Kubernetes service。
* [記錄和監控叢集活動](/zh-cn/docs/concepts/cluster-administration/logging/)
  闡述了 Kubernetes 的日誌如何工作以及怎樣實現。

