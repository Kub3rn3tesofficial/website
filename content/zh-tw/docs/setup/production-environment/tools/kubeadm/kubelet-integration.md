---
title: 使用 kubeadm 配置叢集中的每個 kubelet
content_type: concept
weight: 80
---
<!--
reviewers:
- sig-cluster-lifecycle
title: Configuring each kubelet in your cluster using kubeadm
content_type: concept
weight: 80
-->

<!-- overview -->

{{% dockershim-removal %}}

{{< feature-state for_k8s_version="v1.11" state="stable" >}}

<!--
The lifecycle of the kubeadm CLI tool is decoupled from the
[kubelet](/docs/reference/command-line-tools-reference/kubelet), which is a daemon that runs
on each node within the Kubernetes cluster. The kubeadm CLI tool is executed by the user when Kubernetes is
initialized or upgraded, whereas the kubelet is always running in the background.

Since the kubelet is a daemon, it needs to be maintained by some kind of an init
system or service manager. When the kubelet is installed using DEBs or RPMs,
systemd is configured to manage the kubelet. You can use a different service
manager instead, but you need to configure it manually.

Some kubelet configuration details need to be the same across all kubelets involved in the cluster, while
other configuration aspects need to be set on a per-kubelet basis to accommodate the different
characteristics of a given machine (such as OS, storage, and networking). You can manage the configuration
of your kubelets manually, but kubeadm now provides a `KubeletConfiguration` API type for
[managing your kubelet configurations centrally](#configure-kubelets-using-kubeadm).
-->
kubeadm CLI 工具的生命週期與 [kubelet](/zh-cn/docs/reference/command-line-tools-reference/kubelet)
解耦；kubelet 是一個守護程式，在 Kubernetes 叢集中的每個節點上執行。
當 Kubernetes 初始化或升級時，kubeadm CLI 工具由使用者執行，而 kubelet 始終在後臺執行。

由於kubelet是守護程式，因此需要透過某種初始化系統或服務管理器進行維護。
當使用 DEB 或 RPM 安裝 kubelet 時，配置系統去管理 kubelet。
你可以改用其他服務管理器，但需要手動地配置。

叢集中涉及的所有 kubelet 的一些配置細節都必須相同，
而其他配置方面則需要基於每個 kubelet 進行設定，以適應給定機器的不同特性（例如作業系統、儲存和網路）。
你可以手動地管理 kubelet 的配置，但是 kubeadm 現在提供一種 `KubeletConfiguration` API 型別
用於[集中管理 kubelet 的配置](#configure-kubelets-using-kubeadm)。

<!-- body -->

<!--
## Kubelet configuration patterns
-->
## Kubelet 配置模式    {#kubelet-configuration-patterns}

<!--
The following sections describe patterns to kubelet configuration that are simplified by
using kubeadm, rather than managing the kubelet configuration for each Node manually.
-->
以下各節講述了透過使用 kubeadm 簡化 kubelet 配置模式，而不是在每個節點上手動地管理 kubelet 配置。

<!--
### Propagating cluster-level configuration to each kubelet
-->
### 將叢集級配置傳播到每個 kubelet 中    {#propagating-cluster-level-configuration-to-each-kubelet}

<!--
You can provide the kubelet with default values to be used by `kubeadm init` and `kubeadm join`
commands. Interesting examples include using a different container runtime or setting the default subnet
used by services.

If you want your services to use the subnet `10.96.0.0/12` as the default for services, you can pass
the `--service-cidr` parameter to kubeadm:
-->
你可以透過 `kubeadm init` 和 `kubeadm join` 命令為 kubelet 提供預設值。
有趣的示例包括使用其他容器執行時或透過伺服器設定不同的預設子網。

如果你想使用子網 `10.96.0.0/12` 作為服務的預設網段，你可以給 kubeadm 傳遞 `--service-cidr` 引數：

```bash
kubeadm init --service-cidr 10.96.0.0/12
```

<!--
Virtual IPs for services are now allocated from this subnet. You also need to set the DNS address used
by the kubelet, using the `--cluster-dns` flag. This setting needs to be the same for every kubelet
on every manager and Node in the cluster. The kubelet provides a versioned, structured API object
that can configure most parameters in the kubelet and push out this configuration to each running
kubelet in the cluster. This object is called
[`KubeletConfiguration`](/docs/reference/config-api/kubelet-config.v1beta1/).
The `KubeletConfiguration` allows the user to specify flags such as the cluster DNS IP addresses expressed as
a list of values to a camelCased key, illustrated by the following example:
-->
現在，可以從該子網分配服務的虛擬 IP。
你還需要透過 kubelet 使用 `--cluster-dns` 標誌設定 DNS 地址。
在叢集中的每個管理器和節點上的 kubelet 的設定需要相同。
kubelet 提供了一個版本化的結構化 API 物件，該物件可以配置 kubelet 中的大多數引數，並將此配置推送到叢集中正在執行的每個 kubelet 上。
此物件被稱為 [`KubeletConfiguration`](/zh-cn/docs/reference/config-api/kubelet-config.v1beta1/)。
`KubeletConfiguration` 允許使用者指定標誌，例如用駱峰值代表叢集的 DNS IP 地址，如下所示：

```yaml
apiVersion: kubelet.config.k8s.io/v1beta1
kind: KubeletConfiguration
clusterDNS:
- 10.96.0.10
```

<!--
For more details on the `KubeletConfiguration` have a look at [this section](#configure-kubelets-using-kubeadm).
-->
有關 `KubeletConfiguration` 的更多詳細資訊，請參閱[本節](#configure-kubelets-using-kubeadm)。

<!--
### Providing instance-specific configuration details

Some hosts require specific kubelet configurations due to differences in hardware, operating system,
networking, or other host-specific parameters. The following list provides a few examples.
-->
### 提供特定於某例項的配置細節    {#providing-instance-specific-configuration-details}

由於硬體、作業系統、網路或者其他主機特定引數的差異。某些主機需要特定的 kubelet 配置。
以下列表提供了一些示例。

<!--
- The path to the DNS resolution file, as specified by the `--resolv-conf` kubelet
  configuration flag, may differ among operating systems, or depending on whether you are using
  `systemd-resolved`. If this path is wrong, DNS resolution will fail on the Node whose kubelet
  is configured incorrectly.

- The Node API object `.metadata.name` is set to the machine's hostname by default,
  unless you are using a cloud provider. You can use the `--hostname-override` flag to override the
  default behavior if you need to specify a Node name different from the machine's hostname.

- Currently, the kubelet cannot automatically detect the cgroup driver used by the container runtime,
  but the value of `--cgroup-driver` must match the cgroup driver used by the container runtime to ensure
  the health of the kubelet.

- To specify the container runtime you must set its endpoint with the
  `--container-runtime-endpoint=<path>` flag.

You can specify these flags by configuring an individual kubelet's configuration in your service manager,
such as systemd.
-->
- 由 kubelet 配置標誌 `--resolv-conf` 指定的 DNS 解析檔案的路徑在作業系統之間可能有所不同，
  它取決於你是否使用 `systemd-resolved`。
  如果此路徑錯誤，則在其 kubelet 配置錯誤的節點上 DNS 解析也將失敗。

- 除非你使用雲驅動，否則預設情況下 Node API 物件的 `.metadata.name` 會被設定為計算機的主機名。
  如果你需要指定一個與機器的主機名不同的節點名稱，你可以使用 `--hostname-override` 標誌覆蓋預設值。

- 當前，kubelet 無法自動檢測容器執行時使用的 cgroup 驅動程式，
  但是值 `--cgroup-driver` 必須與容器執行時使用的 cgroup 驅動程式匹配，以確保 kubelet 的健康執行狀況。

- 要指定容器執行時，你必須用 `--container-runtime-endpoint=<path>` 標誌來指定端點。

你可以在服務管理器（例如 systemd）中設定某個 kubelet 的配置來指定這些引數。

<!--
## Configure kubelets using kubeadm

It is possible to configure the kubelet that kubeadm will start if a custom `KubeletConfiguration`
API object is passed with a configuration file like so `kubeadm ... --config some-config-file.yaml`.

By calling `kubeadm config print init-defaults --component-configs KubeletConfiguration` you can
see all the default values for this structure.

Also have a look at the
[reference for the KubeletConfiguration](/docs/reference/config-api/kubelet-config.v1beta1/)
for more information on the individual fields.
-->
## 使用 kubeadm 配置 kubelet    {#configure-kubelets-using-kubeadm}

如果自定義的 `KubeletConfiguration` API 物件使用像  `kubeadm ... --config some-config-file.yaml` 這樣的配置檔案進行傳遞，則可以配置 kubeadm 啟動的 kubelet。

透過呼叫 `kubeadm config print init-defaults --component-configs KubeletConfiguration`，
你可以看到此結構中的所有預設值。

也可以閱讀 [KubeletConfiguration 參考](/zh-cn/docs/reference/config-api/kubelet-config.v1beta1/)
來獲取有關各個欄位的更多資訊。

<!--
### Workflow when using `kubeadm init`
-->
### 使用 `kubeadm init` 時的工作流程    {#workflow-when-using-kubeadm-init}

<!--
When you call `kubeadm init`, the kubelet configuration is marshalled to disk
at `/var/lib/kubelet/config.yaml`, and also uploaded to a `kubelet-config` ConfigMap in the `kube-system`
namespace of the cluster. A kubelet configuration file is also written to `/etc/kubernetes/kubelet.conf`
with the baseline cluster-wide configuration for all kubelets in the cluster. This configuration file
points to the client certificates that allow the kubelet to communicate with the API server. This
addresses the need to
[propagate cluster-level configuration to each kubelet](#propagating-cluster-level-configuration-to-each-kubelet).
-->
當呼叫 `kubeadm init` 時，kubelet 的配置會被寫入磁碟 `/var/lib/kubelet/config.yaml`，
並上傳到叢集 `kube-system` 名稱空間的 `kubelet-config` ConfigMap。
kubelet 配置資訊也被寫入 `/etc/kubernetes/kubelet.conf`，其中包含叢集內所有 kubelet 的基線配置。
此配置檔案指向允許 kubelet 與 API 伺服器通訊的客戶端證書。
這解決了[將叢集級配置傳播到每個 kubelet](#propagating-cluster-level-configuration-to-each-kubelet) 的需求。

<!--
To address the second pattern of
[providing instance-specific configuration details](#providing-instance-specific-configuration-details),
kubeadm writes an environment file to `/var/lib/kubelet/kubeadm-flags.env`, which contains a list of
flags to pass to the kubelet when it starts. The flags are presented in the file like this:
-->
針對[為特定例項提供配置細節](#providing-instance-specific-configuration-details)的第二種模式，
kubeadm 的解決方法是將環境檔案寫入 `/var/lib/kubelet/kubeadm-flags.env`，其中包含了一個標誌列表，
當 kubelet 啟動時，該標誌列表會傳遞給 kubelet 標誌在檔案中的顯示方式如下：

```bash
KUBELET_KUBEADM_ARGS="--flag1=value1 --flag2=value2 ..."
```

<!--
In addition to the flags used when starting the kubelet, the file also contains dynamic
parameters such as the cgroup driver and whether to use a different container runtime socket
(`--cri-socket`).
-->
除了啟動 kubelet 時所使用的標誌外，該檔案還包含動態引數，例如 cgroup 驅動程式以及是否使用其他容器執行時套接字（`--cri-socket`）。

<!--
After marshalling these two files to disk, kubeadm attempts to run the following two
commands, if you are using systemd:
-->
將這兩個檔案編組到磁碟後，如果使用 systemd，則 kubeadm 嘗試執行以下兩個命令：

```bash
systemctl daemon-reload && systemctl restart kubelet
```

<!--
If the reload and restart are successful, the normal `kubeadm init` workflow continues.
-->
如果重新載入和重新啟動成功，則正常的 `kubeadm init` 工作流程將繼續。

<!--
### Workflow when using `kubeadm join`

When you run `kubeadm join`, kubeadm uses the Bootstrap Token credential to perform
a TLS bootstrap, which fetches the credential needed to download the
`kubelet-config` ConfigMap and writes it to `/var/lib/kubelet/config.yaml`. The dynamic
environment file is generated in exactly the same way as `kubeadm init`.
-->
### 使用 `kubeadm join` 時的工作流程    {#workflow-when-using-kubeadm-join}

當執行 `kubeadm join` 時，kubeadm 使用 Bootstrap Token 證書執行 TLS 引導，該引導會獲取一份證書，
該證書需要下載 `kubelet-config` ConfigMap 並把它寫入 `/var/lib/kubelet/config.yaml` 中。
動態環境檔案的生成方式恰好與 `kubeadm init` 完全相同。

<!--
Next, `kubeadm` runs the following two commands to load the new configuration into the kubelet:
-->
接下來，`kubeadm` 執行以下兩個命令將新配置載入到 kubelet 中：

```bash
systemctl daemon-reload && systemctl restart kubelet
```

<!--
After the kubelet loads the new configuration, kubeadm writes the
`/etc/kubernetes/bootstrap-kubelet.conf` KubeConfig file, which contains a CA certificate and Bootstrap
Token. These are used by the kubelet to perform the TLS Bootstrap and obtain a unique
credential, which is stored in `/etc/kubernetes/kubelet.conf`.
-->
在 kubelet 載入新配置後，kubeadm 將寫入 `/etc/kubernetes/bootstrap-kubelet.conf` KubeConfig 檔案中，
該檔案包含 CA 證書和載入程式令牌。
kubelet 使用這些證書執行 TLS 載入程式並獲取唯一的憑據，該憑據被儲存在 `/etc/kubernetes/kubelet.conf` 中。

<!--
When the `/etc/kubernetes/kubelet.conf` file is written, the kubelet has finished performing the TLS Bootstrap.
Kubeadm deletes the `/etc/kubernetes/bootstrap-kubelet.conf` file after completing the TLS Bootstrap.
-->
當 `/etc/kubernetes/kubelet.conf` 檔案被寫入後，kubelet 就完成了 TLS 引導過程。
Kubeadm 在完成 TLS 引導過程後將刪除 `/etc/kubernetes/bootstrap-kubelet.conf` 檔案。

<!--
##  The kubelet drop-in file for systemd
-->
## kubelet 的 systemd drop-in 檔案    {#the-kubelet-drop-in-file-for-systemd}

<!--
`kubeadm` ships with configuration for how systemd should run the kubelet.
Note that the kubeadm CLI command never touches this drop-in file.
-->
`kubeadm` 中附帶了有關係統如何執行 kubelet 的 systemd 配置檔案。
請注意 kubeadm CLI 命令不會修改此檔案。

<!--
This configuration file installed by the `kubeadm`
[DEB](https://github.com/kubernetes/release/blob/master/cmd/kubepkg/templates/latest/deb/kubeadm/10-kubeadm.conf) or
[RPM package](https://github.com/kubernetes/release/blob/master/cmd/kubepkg/templates/latest/rpm/kubeadm/10-kubeadm.conf) is written to
`/etc/systemd/system/kubelet.service.d/10-kubeadm.conf` and is used by systemd.
It augments the basic
[`kubelet.service` for RPM](https://github.com/kubernetes/release/blob/master/cmd/kubepkg/templates/latest/rpm/kubelet/kubelet.service) or
[`kubelet.service` for DEB](https://github.com/kubernetes/release/blob/master/cmd/kubepkg/templates/latest/deb/kubelet/lib/systemd/system/kubelet.service):
-->
透過 `kubeadm` [DEB 包](https://github.com/kubernetes/release/blob/master/cmd/kubepkg/templates/latest/deb/kubeadm/10-kubeadm.conf)
或者 [RPM 包](https://github.com/kubernetes/release/blob/master/cmd/kubepkg/templates/latest/rpm/kubeadm/10-kubeadm.conf)
安裝的配置檔案被寫入 `/etc/systemd/system/kubelet.service.d/10-kubeadm.conf` 並由 systemd 使用。
它對原來的 [RPM 版本 `kubelet.service`](https://github.com/kubernetes/release/blob/master/cmd/kubepkg/templates/latest/rpm/kubelet/kubelet.service)
或者 [DEB 版本 `kubelet.service`](https://github.com/kubernetes/release/blob/master/cmd/kubepkg/templates/latest/deb/kubelet/lib/systemd/system/kubelet.service)
作了增強：

<!--
{{< note >}}
The contents below are just an example. If you don't want to use a package manager
follow the guide outlined in the [Without a package manager](/docs/setup/production-environment/tools/kubeadm/install-kubeadm/#k8s-install-2))
section.
{{< /note >}}
-->
{{< note >}}
下面的內容只是一個例子。如果你不想使用包管理器，
請遵循[沒有包管理器](/zh-cn/docs/setup/productionenvironment/tools/kubeadm/install-kubeadm/#k8s-install-2))
章節的指南。
{{< /note >}}

<!--
```none
[Service]
Environment="KUBELET_KUBECONFIG_ARGS=--bootstrap-kubeconfig=/etc/kubernetes/bootstrap-kubelet.conf --kubeconfig=/etc/kubernetes/kubelet.conf"
Environment="KUBELET_CONFIG_ARGS=--config=/var/lib/kubelet/config.yaml"
# This is a file that "kubeadm init" and "kubeadm join" generate at runtime, populating
the KUBELET_KUBEADM_ARGS variable dynamically
EnvironmentFile=-/var/lib/kubelet/kubeadm-flags.env
# This is a file that the user can use for overrides of the kubelet args as a last resort. Preferably,
# the user should use the .NodeRegistration.KubeletExtraArgs object in the configuration files instead.
# KUBELET_EXTRA_ARGS should be sourced from this file.
EnvironmentFile=-/etc/default/kubelet
ExecStart=
ExecStart=/usr/bin/kubelet $KUBELET_KUBECONFIG_ARGS $KUBELET_CONFIG_ARGS $KUBELET_KUBEADM_ARGS $KUBELET_EXTRA_ARGS
```
-->
```none
[Service]
Environment="KUBELET_KUBECONFIG_ARGS=--bootstrap-kubeconfig=/etc/kubernetes/bootstrap-kubelet.conf --kubeconfig=/etc/kubernetes/kubelet.conf"
Environment="KUBELET_CONFIG_ARGS=--config=/var/lib/kubelet/config.yaml"
# 這是 "kubeadm init" 和 "kubeadm join" 執行時生成的檔案，動態地填充 KUBELET_KUBEADM_ARGS 變數
EnvironmentFile=-/var/lib/kubelet/kubeadm-flags.env
# 這是一個檔案，使用者在不得已下可以將其用作替代 kubelet args。
# 使用者最好使用 .NodeRegistration.KubeletExtraArgs 物件在配置檔案中替代。
# KUBELET_EXTRA_ARGS 應該從此檔案中獲取。
EnvironmentFile=-/etc/default/kubelet
ExecStart=
ExecStart=/usr/bin/kubelet $KUBELET_KUBECONFIG_ARGS $KUBELET_CONFIG_ARGS $KUBELET_KUBEADM_ARGS $KUBELET_EXTRA_ARGS
```

<!--
This file specifies the default locations for all of the files managed by kubeadm for the kubelet.
-->
此檔案指定由 kubeadm 為 kubelet 管理的所有檔案的預設位置。

<!--
- The KubeConfig file to use for the TLS Bootstrap is `/etc/kubernetes/bootstrap-kubelet.conf`,
  but it is only used if `/etc/kubernetes/kubelet.conf` does not exist.
- The KubeConfig file with the unique kubelet identity is `/etc/kubernetes/kubelet.conf`.
- The file containing the kubelet's ComponentConfig is `/var/lib/kubelet/config.yaml`.
- The dynamic environment file that contains `KUBELET_KUBEADM_ARGS` is sourced from `/var/lib/kubelet/kubeadm-flags.env`.
- The file that can contain user-specified flag overrides with `KUBELET_EXTRA_ARGS` is sourced from
  `/etc/default/kubelet` (for DEBs), or `/etc/sysconfig/kubelet` (for RPMs). `KUBELET_EXTRA_ARGS`
  is last in the flag chain and has the highest priority in the event of conflicting settings.
-->
- 用於 TLS 載入程式的 KubeConfig 檔案為 `/etc/kubernetes/bootstrap-kubelet.conf`，
  但僅當 `/etc/kubernetes/kubelet.conf` 不存在時才能使用。
- 具有唯一 kubelet 標識的 KubeConfig 檔案為 `/etc/kubernetes/kubelet.conf`。
- 包含 kubelet 的元件配置的檔案為 `/var/lib/kubelet/config.yaml`。
- 包含的動態環境的檔案 `KUBELET_KUBEADM_ARGS` 是來源於 `/var/lib/kubelet/kubeadm-flags.env`。
- 包含使用者指定標誌替代的檔案 `KUBELET_EXTRA_ARGS` 是來源於
  `/etc/default/kubelet`（對於 DEB），或者 `/etc/sysconfig/kubelet`（對於 RPM）。
  `KUBELET_EXTRA_ARGS` 在標誌鏈中排在最後，並且在設定衝突時具有最高優先順序。

<!--
## Kubernetes binaries and package contents
-->
## Kubernetes 可執行檔案和軟體包內容    {#kubernetes-binaries-and-package-contents}

<!--
The DEB and RPM packages shipped with the Kubernetes releases are:
-->
Kubernetes 版本對應的 DEB 和 RPM 軟體包是：

<!--
| Package name | Description |
|--------------|-------------|
| `kubeadm`    | Installs the `/usr/bin/kubeadm` CLI tool and the [kubelet drop-in file](#the-kubelet-drop-in-file-for-systemd) for the kubelet. |
| `kubelet`    | Installs the `/usr/bin/kubelet` binary. |
| `kubectl`    | Installs the `/usr/bin/kubectl` binary. |
| `cri-tools` | Installs the `/usr/bin/crictl` binary from the [cri-tools git repository](https://github.com/kubernetes-sigs/cri-tools). |
| `kubernetes-cni` | Installs the `/opt/cni/bin` binaries from the [plugins git repository](https://github.com/containernetworking/plugins). |
-->
| 軟體包名稱   | 描述        |
|--------------|-------------|
| `kubeadm`    | 給 kubelet 安裝 `/usr/bin/kubeadm` CLI 工具和 [kubelet 的 systemd drop-in 檔案](#the-kubelet-drop-in-file-for-systemd)。 |
| `kubelet`    | 安裝 `/usr/bin/kubelet` 可執行檔案。 |
| `kubectl`    | 安裝 `/usr/bin/kubectl` 可執行檔案。 |
| `cri-tools` | 從 [cri-tools git 倉庫](https://github.com/kubernetes-sigs/cri-tools)中安裝 `/usr/bin/crictl` 可執行檔案。 |
| `kubernetes-cni` | 從 [plugins git 倉庫](https://github.com/containernetworking/plugins)中安裝 `/opt/cni/bin` 可執行檔案。|
