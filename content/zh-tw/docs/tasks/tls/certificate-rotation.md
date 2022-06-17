---
title: 為 kubelet 配置證書輪換
content_type: task
---
<!--
reviewers:
- jcbsmpsn
- mikedanese
title: Configure Certificate Rotation for the Kubelet
content_type: task
-->

<!-- overview -->
<!--
This page shows how to enable and configure certificate rotation for the kubelet.
-->
本文展示如何在 kubelet 中啟用並配置證書輪換。

{{< feature-state for_k8s_version="v1.19" state="stable" >}}

## {{% heading "prerequisites" %}}

<!--
* Kubernetes version 1.8.0 or later is required
-->
* 要求 Kubernetes 1.8.0 或更高的版本

<!-- steps -->

<!--
## Overview

The kubelet uses certificates for authenticating to the Kubernetes API.  By
default, these certificates are issued with one year expiration so that they do
not need to be renewed too frequently.
-->
## 概述

Kubelet 使用證書進行 Kubernetes API 的認證。
預設情況下，這些證書的簽發期限為一年，所以不需要太頻繁地進行更新。

<!--
Kubernetes contains [kubelet certificate
rotation](/docs/reference/access-authn-authz/kubelet-tls-bootstrapping/),
that will automatically generate a new key and request a new certificate from
the Kubernetes API as the current certificate approaches expiration. Once the
new certificate is available, it will be used for authenticating connections to
the Kubernetes API.
-->
Kubernetes 包含特性
[kubelet 證書輪換](/zh-cn/docs/reference/access-authn-authz/kubelet-tls-bootstrapping/)，
在當前證書即將過期時，
將自動生成新的秘鑰，並從 Kubernetes API 申請新的證書。 一旦新的證書可用，它將被用於與
Kubernetes API 間的連線認證。

<!--
## Enabling client certificate rotation

The `kubelet` process accepts an argument `--rotate-certificates` that controls
if the kubelet will automatically request a new certificate as the expiration of
the certificate currently in use approaches.
-->
## 啟用客戶端證書輪換

 `kubelet` 程序接收 `--rotate-certificates` 引數，該引數決定 kubelet 在當前使用的
證書即將到期時，是否會自動申請新的證書。

<!--
The `kube-controller-manager` process accepts an argument
`--cluster-signing-duration`  (`--experimental-cluster-signing-duration` prior to 1.19)
that controls how long certificates will be issued for.
-->
`kube-controller-manager` 程序接收 `--cluster-signing-duration` 引數
（在 1.19 版本之前為 `--experimental-cluster-signing-duration`），用來
控制簽發證書的有效期限。

<!--
## Understanding the certificate rotation configuration

When a kubelet starts up, if it is configured to bootstrap (using the
`--bootstrap-kubeconfig` flag), it will use its initial certificate to connect
to the Kubernetes API and issue a certificate signing request. You can view the
status of certificate signing requests using:
-->
## 理解證書輪換配置

當 kubelet 啟動時，如被配置為自舉（使用`--bootstrap-kubeconfig` 引數），kubelet
會使用其初始證書連線到 Kubernetes API ，併發送證書籤名的請求。
可以透過以下方式檢視證書籤名請求的狀態：

```shell
kubectl get csr
```

<!--
Initially a certificate signing request from the kubelet on a node will have a
status of `Pending`. If the certificate signing requests meets specific
criteria, it will be auto approved by the controller manager, then it will have
a status of `Approved`. Next, the controller manager will sign a certificate,
issued for the duration specified by the
`--cluster-signing-duration` parameter, and the signed certificate
will be attached to the certificate signing request.
-->
最初，來自節點上 kubelet 的證書籤名請求處於 `Pending` 狀態。 如果證書籤名請求滿足特定條件，
控制器管理器會自動批准，此時請求會處於 `Approved` 狀態。 接下來，控制器管理器會簽署證書，
證書的有效期限由 `--cluster-signing-duration` 引數指定，簽署的證書會被附加到證書籤名請求中。

<!--
The kubelet will retrieve the signed certificate from the Kubernetes API and
write that to disk, in the location specified by `--cert-dir`. Then the kubelet
will use the new certificate to connect to the Kubernetes API.
-->
Kubelet 會從 Kubernetes API 取回簽署的證書，並將其寫入磁碟，儲存位置透過 `--cert-dir`
引數指定。
然後 kubelet 會使用新的證書連線到 Kubernetes API。

<!--
As the expiration of the signed certificate approaches, the kubelet will
automatically issue a new certificate signing request, using the Kubernetes API. 
This can happen at any point between 30% and 10% of the time remaining on the 
certificate. Again, the controller manager will automatically approve the certificate
request and attach a signed certificate to the certificate signing request. The
kubelet will retrieve the new signed certificate from the Kubernetes API and
write that to disk. Then it will update the connections it has to the
Kubernetes API to reconnect using the new certificate.
-->
當簽署的證書即將到期時，kubelet 會使用 Kubernetes API，自動發起新的證書籤名請求。
該請求會發生在證書的有效時間剩下 30% 到 10% 之間的任意時間點。
同樣地，控制器管理器會自動批准證書請求，並將簽署的證書附加到證書籤名請求中。 Kubelet
會從 Kubernetes API 取回簽署的證書，並將其寫入磁碟。 然後它會更新與 Kubernetes API
的連線，使用新的證書重新連線到 Kubernetes API。

