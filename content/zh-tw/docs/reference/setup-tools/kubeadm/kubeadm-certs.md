---
title: kubeadm certs
content_type: concept
weight: 90
---

<!--
`kubeadm certs` provides utilities for managing certificates.
For more details on how these commands can be used, see
[Certificate Management with kubeadm](/docs/tasks/administer-cluster/kubeadm/kubeadm-certs/).
-->
`kubeadm certs` 提供管理證書的工具。關於如何使用這些命令的細節，可參見
[使用 kubeadm 管理證書](/zh-cn/docs/tasks/administer-cluster/kubeadm/kubeadm-certs/)。

## kubeadm certs {#cmd-certs}

<!--
A collection of operations for operating Kubernetes certificates.
-->
用來操作 Kubernetes 證書的一組命令。

{{< tabs name="tab-certs" >}}
{{< tab name="概覽" include="generated/kubeadm_certs.md" />}}
{{< /tabs >}}

## kubeadm certs renew {#cmd-certs-renew}

<!--
You can renew all Kubernetes certificates using the `all` subcommand or renew them selectively.
For more details see [Manual certificate renewal](/docs/tasks/administer-cluster/kubeadm/kubeadm-certs/#manual-certificate-renewal).
-->
你可以使用 `all` 子命令來續訂所有 Kubernetes 證書，也可以選擇性地續訂部分證書。
更多的相關細節，可參見
[手動續訂證書](/zh-cn/docs/tasks/administer-cluster/kubeadm/kubeadm-certs/#manual-certificate-renewal)。

{{< tabs name="tab-certs-renew" >}}
{{< tab name="renew" include="generated/kubeadm_certs_renew.md" />}}
{{< tab name="all" include="generated/kubeadm_certs_renew_all.md" />}}
{{< tab name="admin.conf" include="generated/kubeadm_certs_renew_admin.conf.md" />}}
{{< tab name="apiserver-etcd-client" include="generated/kubeadm_certs_renew_apiserver-etcd-client.md" />}}
{{< tab name="apiserver-kubelet-client" include="generated/kubeadm_certs_renew_apiserver-kubelet-client.md" />}}
{{< tab name="apiserver" include="generated/kubeadm_certs_renew_apiserver.md" />}}
{{< tab name="controller-manager.conf" include="generated/kubeadm_certs_renew_controller-manager.conf.md" />}}
{{< tab name="etcd-healthcheck-client" include="generated/kubeadm_certs_renew_etcd-healthcheck-client.md" />}}
{{< tab name="etcd-peer" include="generated/kubeadm_certs_renew_etcd-peer.md" />}}
{{< tab name="etcd-server" include="generated/kubeadm_certs_renew_etcd-server.md" />}}
{{< tab name="front-proxy-client" include="generated/kubeadm_certs_renew_front-proxy-client.md" />}}
{{< tab name="scheduler.conf" include="generated/kubeadm_certs_renew_scheduler.conf.md" />}}
{{< /tabs >}}

## kubeadm certs certificate-key {#cmd-certs-certificate-key}

<!--
This command can be used to generate a new control-plane certificate key.
The key can be passed as `--certificate-key` to [`kubeadm init`](/docs/reference/setup-tools/kubeadm/kubeadm-init)
and [`kubeadm join`](/docs/reference/setup-tools/kubeadm/kubeadm-join)
to enable the automatic copy of certificates when joining additional control-plane nodes.
-->
此命令可用來生成一個新的控制面證書金鑰。金鑰可以作為 `--certificate-key`
標誌的取值傳遞給 [`kubeadm init`](/zh-cn/docs/reference/setup-tools/kubeadm/kubeadm-init)
和 [`kubeadm join`](/zh-cn/docs/reference/setup-tools/kubeadm/kubeadm-join)
命令，從而在新增新的控制面節點時能夠自動完成證書複製。

{{< tabs name="tab-certs-certificate-key" >}}
{{< tab name="certificate-key" include="generated/kubeadm_certs_certificate-key.md" />}}
{{< /tabs >}}

## kubeadm certs check-expiration {#cmd-certs-check-expiration}

<!--
This command checks expiration for the certificates in the local PKI managed by kubeadm.
For more details see
[Check certificate expiration](/docs/tasks/administer-cluster/kubeadm/kubeadm-certs/#check-certificate-expiration).
-->
此命令檢查 kubeadm 所管理的本地 PKI 中的證書是否以及何時過期。
更多的相關細節，可參見
[檢查證書過期](/zh-cn/docs/tasks/administer-cluster/kubeadm/kubeadm-certs/#check-certificate-expiration)。


{{< tabs name="tab-certs-check-expiration" >}}
{{< tab name="check-expiration" include="generated/kubeadm_certs_check-expiration.md" />}}
{{< /tabs >}}

## kubeadm certs generate-csr {#cmd-certs-generate-csr}

<!--
This command can be used to generate keys and CSRs for all control-plane certificates and kubeconfig files.
The user can then sign the CSRs with a CA of their choice.
-->
此命令可用來為所有控制面證書和 kubeconfig 檔案生成金鑰和 CSR（簽名請求）。
使用者可以根據自身需要選擇 CA 為 CSR 簽名。

{{< tabs name="tab-certs-generate-csr" >}}
{{< tab name="generate-csr" include="generated/kubeadm_certs_generate-csr.md" />}}
{{< /tabs >}}

## {{% heading "whatsnext" %}}

<!--
* [kubeadm init](/docs/reference/setup-tools/kubeadm/kubeadm-init/) to bootstrap a Kubernetes control-plane node
* [kubeadm join](/docs/reference/setup-tools/kubeadm/kubeadm-join/) to connect a node to the cluster
* [kubeadm reset](/docs/reference/setup-tools/kubeadm/kubeadm-reset/) to revert any changes made to this host by `kubeadm init` or `kubeadm join`
-->
* 用來啟動引導 Kubernetes 控制面節點的
  [kubeadm init](/zh-cn/docs/reference/setup-tools/kubeadm/kubeadm-init/)
  命令
* 用來將節點連線到叢集的
  [kubeadm join](/zh-cn/docs/reference/setup-tools/kubeadm/kubeadm-join/)
  命令
* 用來回滾 `kubeadm init` 或 `kubeadm join` 對當前主機所做修改的
  [kubeadm reset](/zh-cn/docs/reference/setup-tools/kubeadm/kubeadm-reset/)
  命令

