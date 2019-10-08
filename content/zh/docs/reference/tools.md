<!--
---
reviewers:
- janetkuo
title: Tools
content_template: templates/concept
---
-->
---
reviewers:
- janetkuo
<<<<<<< HEAD
title: 工具
=======
title: ����
>>>>>>> Update localization guidelines (#10485)
content_template: templates/concept
---

<!--
<<<<<<< HEAD
Kubernetes contains several built-in tools to help you work with the Kubernetes system.
-->
{{% capture overview %}}
Kubernetes 包含一些内置工具，可以帮助用户更好的使用 Kubernetes 系统。
=======
{{% capture overview %}}
Kubernetes contains several built-in tools to help you work with the Kubernetes system.
{{% /capture %}}
-->
{{% capture overview %}}
Kubernetes ����һЩ���ù��ߣ����԰����û����õ�ʹ�� Kubernetes ϵͳ��
>>>>>>> Update localization guidelines (#10485)
{{% /capture %}}

{{% capture body %}}
## Kubectl

<!--
[`kubectl`](/docs/tasks/tools/install-kubectl/) is the command line tool for Kubernetes. It controls the Kubernetes cluster manager.
-->
<<<<<<< HEAD
[`kubectl`](/docs/tasks/tools/install-kubectl/) 是 Kubernetes 命令行工具，可以用来操控 Kubernetes 集群。
=======
[`kubectl`](/docs/tasks/tools/install-kubectl/) �� Kubernetes �����й��ߣ����������ٿ� Kubernetes ��Ⱥ��
>>>>>>> Update localization guidelines (#10485)

## Kubeadm 

<!--
[`kubeadm`](/docs/tasks/tools/install-kubeadm/) is the command line tool for easily provisioning a secure Kubernetes cluster on top of physical or cloud servers or virtual machines (currently in alpha).
-->
<<<<<<< HEAD
[`kubeadm`](/docs/tasks/tools/install-kubeadm/) 是一个命令行工具，可以用来在物理机、云服务器或虚拟机（目前处于 alpha 阶段）上轻松部署一个安全可靠的 Kubernetes 集群。
=======
[`kubeadm`](/docs/tasks/tools/install-kubeadm/) ��һ�������й��ߣ�������������������Ʒ��������������Ŀǰ���� alpha �׶Σ������ɲ���һ����ȫ�ɿ��� Kubernetes ��Ⱥ��
>>>>>>> Update localization guidelines (#10485)

## Kubefed

<!--
[`kubefed`](/docs/tasks/federation/set-up-cluster-federation-kubefed/) is the command line tool
to help you administrate your federated clusters.
-->
<<<<<<< HEAD
[`kubefed`](/docs/tasks/federation/set-up-cluster-federation-kubefed/) 是一个命令行工具，可以用来帮助用户管理联邦集群。
=======
[`kubefed`](/docs/tasks/federation/set-up-cluster-federation-kubefed/) ��һ�������й��ߣ��������������û��������Ⱥ��
>>>>>>> Update localization guidelines (#10485)


## Minikube

<!--
[`minikube`](/docs/tasks/tools/install-minikube/) is a tool that makes it
easy to run a single-node Kubernetes cluster locally on your workstation for
development and testing purposes.
-->
<<<<<<< HEAD
[`minikube`](/docs/tasks/tools/install-minikube/) 是一个可以方便用户在其工作站点本地部署一个单节点 Kubernetes 集群的工具，用于开发和测试。
=======
[`minikube`](/docs/tasks/tools/install-minikube/) ��һ�����Է����û����乤��վ�㱾�ز���һ�����ڵ� Kubernetes ��Ⱥ�Ĺ��ߣ����ڿ����Ͳ��ԡ�
>>>>>>> Update localization guidelines (#10485)


## Dashboard 

<!--
[`Dashboard`](/docs/tasks/access-application-cluster/web-ui-dashboard/), the web-based user interface of Kubernetes, allows you to deploy containerized applications
to a Kubernetes cluster, troubleshoot them, and manage the cluster and its resources itself. 
-->
<<<<<<< HEAD
[`Dashboard`](/docs/tasks/access-application-cluster/web-ui-dashboard/), 是 Kubernetes 基于 Web 的用户管理界面，允许用户部署容器化应用到 Kubernetes 集群，进行故障排查以及管理集群和集群资源。 
=======
[`Dashboard`](/docs/tasks/access-application-cluster/web-ui-dashboard/), �� Kubernetes ���� Web ���û�������棬�����û�����������Ӧ�õ� Kubernetes ��Ⱥ�����й����Ų��Լ�����Ⱥ�ͼ�Ⱥ��Դ�� 
>>>>>>> Update localization guidelines (#10485)

## Helm

<!--
[`Kubernetes Helm`](https://github.com/kubernetes/helm) is a tool for managing packages of pre-configured
Kubernetes resources, aka Kubernetes charts.
-->
<<<<<<< HEAD
[`Kubernetes Helm`](https://github.com/kubernetes/helm) 是一个管理预先配置 Kubernetes 资源包的工具，这里的资源在 Helm 中也被称作 Kubernetes charts。
=======
[`Kubernetes Helm`](https://github.com/kubernetes/helm) ��һ������Ԥ������ Kubernetes ��Դ���Ĺ��ߣ��������Դ�� Helm ��Ҳ������ Kubernetes charts��
>>>>>>> Update localization guidelines (#10485)

<!--
Use Helm to:

* Find and use popular software packaged as Kubernetes charts
* Share your own applications as Kubernetes charts
* Create reproducible builds of your Kubernetes applications
* Intelligently manage your Kubernetes manifest files
* Manage releases of Helm packages
-->
<<<<<<< HEAD
使用 Helm：

* 查找并使用已经打包为 Kubernetes charts 的流行软件
* 分享您自己的应用作为 Kubernetes charts
* 为 Kubernetes 应用创建可重复执行的构建
* 为您的 Kubernetes 清单文件提供更智能化的管理
* 管理 Helm 软件包的发布
=======
ʹ�� Helm��

*���Ҳ�ʹ���Ѿ����Ϊ Kubernetes charts ���������
*�������Լ���Ӧ����Ϊ Kubernetes charts
*Ϊ Kubernetes Ӧ�ô������ظ�ִ�еĹ���
*Ϊ���� Kubernetes �嵥�ļ��ṩ�����ܻ��Ĺ���
*���� Helm ������ķ���
>>>>>>> Update localization guidelines (#10485)

## Kompose

<!--
[`Kompose`](https://github.com/kubernetes-incubator/kompose) is a tool to help Docker Compose users move to Kubernetes.
-->
<<<<<<< HEAD
[`Kompose`](https://github.com/kubernetes-incubator/kompose) 一个转换工具，用来帮助 Docker Compose 用户迁移至 Kubernetes。
=======
[`Kompose`](https://github.com/kubernetes-incubator/kompose) һ��ת�����ߣ��������� Docker Compose �û�Ǩ���� Kubernetes��
>>>>>>> Update localization guidelines (#10485)

<!--
Use Kompose to:

* Translate a Docker Compose file into Kubernetes objects
* Go from local Docker development to managing your application via Kubernetes
* Convert v1 or v2 Docker Compose `yaml` files or [Distributed Application Bundles](https://docs.docker.com/compose/bundles/)
<<<<<<< HEAD
-->
使用 Kompose:

* 将一个 Docker Compose 文件解释成 Kubernetes 对象
* 将本地 Docker 开发 转变成通过 Kubernetes 来管理
* 转换 v1 或 v2 Docker Compose `yaml` 文件 或 [分布式应用程序包](https://docs.docker.com/compose/bundles/)
{{% /capture %}}
=======
{{% /capture %}}
-->
ʹ�� Kompose:

* ��һ�� Docker Compose �ļ����ͳ� Kubernetes ����
* ������ Docker ���� ת���ͨ�� Kubernetes ������
* ת�� v1 �� v2 Docker Compose `yaml` �ļ� �� [�ֲ�ʽӦ�ó����](https://docs.docker.com/compose/bundles/)
{{% /capture %}}
>>>>>>> Update localization guidelines (#10485)
