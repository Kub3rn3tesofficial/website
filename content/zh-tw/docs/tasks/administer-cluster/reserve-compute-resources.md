---
title: 為系統守護程序預留計算資源
content_type: task
min-kubernetes-server-version: 1.8
---
<!--
reviewers:
- vishh
- derekwaynecarr
- dashpole
title: Reserve Compute Resources for System Daemons
content_type: task
min-kubernetes-server-version: 1.8
-->

<!-- overview -->
<!--
Kubernetes nodes can be scheduled to `Capacity`. Pods can consume all the
available capacity on a node by default. This is an issue because nodes
typically run quite a few system daemons that power the OS and Kubernetes
itself. Unless resources are set aside for these system daemons, pods and system
daemons compete for resources and lead to resource starvation issues on the
node.

The `kubelet` exposes a feature named 'Node Allocatable' that helps to reserve
compute resources for system daemons. Kubernetes recommends cluster
administrators to configure 'Node Allocatable' based on their workload density
on each node.
-->
Kubernetes 的節點可以按照 `Capacity` 排程。預設情況下 pod 能夠使用節點全部可用容量。
這是個問題，因為節點自己通常運行了不少驅動 OS 和 Kubernetes 的系統守護程序。
除非為這些系統守護程序留出資源，否則它們將與 pod 爭奪資源並導致節點資源短缺問題。

`kubelet` 公開了一個名為 'Node Allocatable' 的特性，有助於為系統守護程序預留計算資源。
Kubernetes 推薦叢集管理員按照每個節點上的工作負載密度配置 “Node Allocatable”。

## {{% heading "prerequisites" %}}

{{< include "task-tutorial-prereqs.md" >}} {{< version-check >}}
<!-- 
Your Kubernetes server must be at or later than version 1.17 to use
the kubelet command line option `--reserved-cpus` to set an
[explicitly reserved CPU list](#explicitly-reserved-cpu-list).
-->
你的 kubernetes 伺服器版本必須至少是 1.17 版本，才能使用 kubelet
命令列選項 `--reserved-cpus` 設定
[顯式預留 CPU 列表](#explicitly-reserved-cpu-list)。

<!-- steps -->

<!--
## Node Allocatable

![node capacity](/images/docs/node-capacity.svg)

'Allocatable' on a Kubernetes node is defined as the amount of compute resources
that are available for pods. The scheduler does not over-subscribe
'Allocatable'. 'CPU', 'memory' and 'ephemeral-storage' are supported as of now.

Node Allocatable is exposed as part of `v1.Node` object in the API and as part
of `kubectl describe node` in the CLI.

Resources can be reserved for two categories of system daemons in the `kubelet`.
-->
## 節點可分配   {#node-allocatable}

![節點容量](/images/docs/node-capacity.svg)

Kubernetes 節點上的 'Allocatable' 被定義為 pod 可用計算資源量。
排程器不會超額申請 'Allocatable'。
目前支援 'CPU'、'memory' 和 'ephemeral-storage' 這幾個引數。

可分配的節點暴露為 API 中 `v1.Node` 物件的一部分，也是 CLI 中
`kubectl describe node` 的一部分。

在 `kubelet` 中，可以為兩類系統守護程序預留資源。

<!--
### Enabling QoS and Pod level cgroups

To properly enforce node allocatable constraints on the node, you must
enable the new cgroup hierarchy via the `--cgroups-per-qos` flag. This flag is
enabled by default. When enabled, the `kubelet` will parent all end-user pods
under a cgroup hierarchy managed by the `kubelet`.
-->
### 啟用 QoS 和 Pod 級別的 cgroups  {#enabling-qos-and-pod-level-cgroups}

為了恰當的在節點範圍實施節點可分配約束，你必須透過 `--cgroups-per-qos`
標誌啟用新的 cgroup 層次結構。這個標誌是預設啟用的。
啟用後，`kubelet` 將在其管理的 cgroup 層次結構中建立所有終端使用者的 Pod。

<!--
### Configuring a cgroup driver

The `kubelet` supports manipulation of the cgroup hierarchy on
the host using a cgroup driver. The driver is configured via the
`--cgroup-driver` flag.

The supported values are the following:

* `cgroupfs` is the default driver that performs direct manipulation of the
cgroup filesystem on the host in order to manage cgroup sandboxes.
* `systemd` is an alternative driver that manages cgroup sandboxes using
transient slices for resources that are supported by that init system.

Depending on the configuration of the associated container runtime,
operators may have to choose a particular cgroup driver to ensure
proper system behavior. For example, if operators use the `systemd`
cgroup driver provided by the `containerd` runtime, the `kubelet` must
be configured to use the `systemd` cgroup driver.
-->
### 配置 cgroup 驅動  {#configuring-a-cgroup-driver}

`kubelet` 支援在主機上使用 cgroup 驅動操作 cgroup 層次結構。
驅動透過 `--cgroup-driver` 標誌配置。

支援的引數值如下：

* `cgroupfs` 是預設的驅動，在主機上直接操作 cgroup 檔案系統以對 cgroup
  沙箱進行管理。
* `systemd` 是可選的驅動，使用 init 系統支援的資源的瞬時切片管理
  cgroup 沙箱。

取決於相關容器執行時的配置，操作員可能需要選擇一個特定的 cgroup 驅動
來保證系統正常執行。
例如，如果操作員使用 `containerd` 執行時提供的 `systemd` cgroup 驅動時，
必須配置 `kubelet` 使用 `systemd` cgroup 驅動。

<!--
### Kube Reserved

- **Kubelet Flag**: `--kube-reserved=[cpu=100m][,][memory=100Mi][,][ephemeral-storage=1Gi][,][pid=1000]`
- **Kubelet Flag**: `--kube-reserved-cgroup=`

`kube-reserved` is meant to capture resource reservation for kubernetes system
daemons like the `kubelet`, `container runtime`, `node problem detector`, etc.
It is not meant to reserve resources for system daemons that are run as pods.
`kube-reserved` is typically a function of `pod density` on the nodes.

In addition to `cpu`, `memory`, and `ephemeral-storage`, `pid` may be
specified to reserve the specified number of process IDs for
kubernetes system daemons.
-->
### Kube 預留值  {#kube-reserved}

- **Kubelet 標誌**: `--kube-reserved=[cpu=100m][,][memory=100Mi][,][ephemeral-storage=1Gi][,][pid=1000]`
- **Kubelet 標誌**: `--kube-reserved-cgroup=`

`kube-reserved` 用來給諸如 `kubelet`、容器執行時、節點問題監測器等
kubernetes 系統守護程序記述其資源預留值。
該配置並非用來給以 Pod 形式執行的系統守護程序保留資源。`kube-reserved`
通常是節點上 `pod 密度` 的函式。

除了 `cpu`，`記憶體` 和 `ephemeral-storage` 之外，`pid` 可用來指定為
kubernetes 系統守護程序預留指定數量的程序 ID。

<!--
To optionally enforce `kube-reserved` on kubernetes system daemons, specify the parent
control group for kube daemons as the value for `--kube-reserved-cgroup` kubelet
flag.

It is recommended that the kubernetes system daemons are placed under a top
level control group (`runtime.slice` on systemd machines for example). Each
system daemon should ideally run within its own child control group. Refer to
[the design proposal](https://git.k8s.io/design-proposals-archive/node/node-allocatable.md#recommended-cgroups-setup)
for more details on recommended control group hierarchy.

Note that Kubelet **does not** create `--kube-reserved-cgroup` if it doesn't
exist. Kubelet will fail if an invalid cgroup is specified.
-->
要選擇性地對 kubernetes 系統守護程序上執行 `kube-reserved` 保護，需要把 kubelet 的
`--kube-reserved-cgroup` 標誌的值設定為 kube 守護程序的父控制組。

推薦將 kubernetes 系統守護程序放置於頂級控制組之下（例如 systemd 機器上的
`runtime.slice`）。
理想情況下每個系統守護程序都應該在其自己的子控制組中執行。
請參考
[這個設計方案](https://git.k8s.io/design-proposals-archive/node/node-allocatable.md#recommended-cgroups-setup)，
進一步瞭解關於推薦控制組層次結構的細節。

請注意，如果 `--kube-reserved-cgroup` 不存在，Kubelet 將 **不會** 建立它。
如果指定了一個無效的 cgroup，Kubelet 將會失敗。

<!--
### System Reserved

- **Kubelet Flag**: `--system-reserved=[cpu=100m][,][memory=100Mi][,][ephemeral-storage=1Gi][,][pid=1000]`
- **Kubelet Flag**: `--system-reserved-cgroup=`

`system-reserved` is meant to capture resource reservation for OS system daemons
like `sshd`, `udev`, etc. `system-reserved` should reserve `memory` for the
`kernel` too since `kernel` memory is not accounted to pods in Kubernetes at this time.
Reserving resources for user login sessions is also recommended (`user.slice` in
systemd world).

In addition to `cpu`, `memory`, and `ephemeral-storage`, `pid` may be
specified to reserve the specified number of process IDs for OS system
daemons.
-->
### 系統預留值  {#system-reserved}

- **Kubelet 標誌**: `--system-reserved=[cpu=100m][,][memory=100Mi][,][ephemeral-storage=1Gi][,][pid=1000]`
- **Kubelet 標誌**: `--system-reserved-cgroup=`

`system-reserved` 用於為諸如 `sshd`、`udev` 等系統守護程序記述其資源預留值。
`system-reserved` 也應該為 `kernel` 預留 `記憶體`，因為目前 `kernel`
使用的記憶體並不記在 Kubernetes 的 Pod 上。
同時還推薦為使用者登入會話預留資源（systemd 體系中的 `user.slice`）。

除了 `cpu`，`記憶體` 和 `ephemeral-storage` 之外，`pid` 可用來指定為
kubernetes 系統守護程序預留指定數量的程序 ID。

<!--
To optionally enforce `system-reserved` on system daemons, specify the parent
control group for OS system daemons as the value for `--system-reserved-cgroup`
kubelet flag.

It is recommended that the OS system daemons are placed under a top level
control group (`system.slice` on systemd machines for example).

Note that `kubelet` **does not** create `--system-reserved-cgroup` if it doesn't
exist. `kubelet` will fail if an invalid cgroup is specified.
-->
要想為系統守護程序上可選地實施 `system-reserved` 約束，請指定 kubelet 的
`--system-reserved-cgroup` 標誌值為 OS 系統守護程序的父級控制組。

推薦將 OS 系統守護程序放在一個頂級控制組之下（例如 systemd 機器上的
`system.slice`）。

請注意，如果 `--system-reserved-cgroup` 不存在，`kubelet` **不會** 建立它。
如果指定了無效的 cgroup，`kubelet` 將會失敗。

<!--
### Explicitly Reserved CPU List
-->
### 顯式保留的 CPU 列表 {#explicitly-reserved-cpu-list}

{{< feature-state for_k8s_version="v1.17" state="stable" >}}

<!--
**Kubelet Flag**: `--reserved-cpus=0-3`
-->
**Kubelet 標誌**：`--reserved-cpus=0-3`

<!--
`reserved-cpus` is meant to define an explicit CPU set for OS system daemons and
kubernetes system daemons. `reserved-cpus` is for systems that do not intend to
define separate top level cgroups for OS system daemons and kubernetes system daemons
with regard to cpuset resource.
If the Kubelet **does not** have `--system-reserved-cgroup` and `--kube-reserved-cgroup`,
the explicit cpuset provided by `reserved-cpus` will take precedence over the CPUs
defined by `--kube-reserved` and `--system-reserved` options.
-->
`reserved-cpus` 旨在為作業系統守護程式和 kubernetes 系統守護程式保留一組明確指定編號的
CPU。`reserved-cpus` 適用於不打算針對 cpuset 資源為作業系統守護程式和 kubernetes
系統守護程式定義獨立的頂級 cgroups 的系統。
如果 Kubelet **沒有** 指定引數 `--system-reserved-cgroup` 和 `--kube-reserved-cgroup`，
則 `reserved-cpus` 的設定將優先於 `--kube-reserved` 和 `--system-reserved` 選項。

<!--
This option is specifically designed for Telco/NFV use cases where uncontrolled
interrupts/timers may impact the workload performance. you can use this option
to define the explicit cpuset for the system/kubernetes daemons as well as the
interrupts/timers, so the rest CPUs on the system can be used exclusively for
workloads, with less impact from uncontrolled interrupts/timers. To move the
system daemon, kubernetes daemons and interrupts/timers to the explicit cpuset
defined by this option, other mechanism outside Kubernetes should be used.
For example: in Centos, you can do this using the tuned toolset.
-->
此選項是專門為電信/NFV 用例設計的，在這些用例中不受控制的中斷或計時器可能會
影響其工作負載效能。
你可以使用此選項為系統或 kubernetes 守護程式以及中斷或計時器顯式定義 cpuset，
這樣系統上的其餘 CPU 可以專門用於工作負載，因不受控制的中斷或計時器的影響得以
降低。
要將系統守護程式、kubernetes 守護程式和中斷或計時器移動到此選項定義的顯式
cpuset 上，應使用 Kubernetes 之外的其他機制。
例如：在 Centos 系統中，可以使用 tuned 工具集來執行此操作。

<!--
### Eviction Thresholds

**Kubelet Flag**: `--eviction-hard=[memory.available<500Mi]`

Memory pressure at the node level leads to System OOMs which affects the entire
node and all pods running on it. Nodes can go offline temporarily until memory
has been reclaimed. To avoid (or reduce the probability of) system OOMs kubelet
provides [out of resource](/docs/concepts/scheduling-eviction/node-pressure-eviction/)
management. Evictions are
supported for `memory` and `ephemeral-storage` only. By reserving some memory via
`--eviction-hard` flag, the `kubelet` attempts to evict pods whenever memory
availability on the node drops below the reserved value. Hypothetically, if
system daemons did not exist on a node, pods cannot use more than `capacity -
eviction-hard`. For this reason, resources reserved for evictions are not
available for pods.
-->
### 驅逐閾值   {#eviction-Thresholds}

**Kubelet 標誌**：`--eviction-hard=[memory.available<500Mi]`

節點級別的記憶體壓力將導致系統記憶體不足，這將影響到整個節點及其上執行的所有 Pod。
節點可以暫時離線直到記憶體已經回收為止。
為了防止（或減少可能性）系統記憶體不足，kubelet 提供了
[資源不足](/zh-cn/docs/concepts/scheduling-eviction/node-pressure-eviction/)管理。
驅逐操作只支援 `memory` 和 `ephemeral-storage`。
透過 `--eviction-hard` 標誌預留一些記憶體後，當節點上的可用記憶體降至保留值以下時，
`kubelet` 將嘗試驅逐 Pod。
如果節點上不存在系統守護程序，Pod 將不能使用超過 `capacity-eviction-hard` 所
指定的資源量。因此，為驅逐而預留的資源對 Pod 是不可用的。

<!--
### Enforcing Node Allocatable

**Kubelet Flag**: `--enforce-node-allocatable=pods[,][system-reserved][,][kube-reserved]`

The scheduler treats 'Allocatable' as the available `capacity` for pods.

`kubelet` enforce 'Allocatable' across pods by default. Enforcement is performed
by evicting pods whenever the overall usage across all pods exceeds
'Allocatable'. More details on eviction policy can be found
on the [node pressure eviction](/docs/concepts/scheduling-eviction/node-pressure-eviction/)
page. This enforcement is controlled by
specifying `pods` value to the kubelet flag `--enforce-node-allocatable`.

Optionally, `kubelet` can be made to enforce `kube-reserved` and
`system-reserved` by specifying `kube-reserved` & `system-reserved` values in
the same flag. Note that to enforce `kube-reserved` or `system-reserved`,
`--kube-reserved-cgroup` or `--system-reserved-cgroup` needs to be specified
respectively.
-->
### 實施節點可分配約束   {#enforcing-node-allocatable}

**Kubelet 標誌**：`--enforce-node-allocatable=pods[,][system-reserved][,][kube-reserved]`

排程器將 'Allocatable' 視為 Pod 可用的 `capacity`（資源容量）。

`kubelet` 預設對 Pod 執行 'Allocatable' 約束。
無論何時，如果所有 Pod 的總用量超過了 'Allocatable'，驅逐 Pod 的措施將被執行。
有關驅逐策略的更多細節可以在
[節點壓力驅逐](/zh-cn/docs/concepts/scheduling-eviction/pod-priority-preemption/)頁找到。
可透過設定 kubelet `--enforce-node-allocatable` 標誌值為 `pods` 控制這個措施。

可選地，透過在同一標誌中同時指定 `kube-reserved` 和 `system-reserved` 值，
可以使 `kubelet` 強制實施 `kube-reserved` 和 `system-reserved` 約束。
請注意，要想執行 `kube-reserved` 或者 `system-reserved` 約束，
需要對應設定 `--kube-reserved-cgroup` 或者 `--system-reserved-cgroup`。

<!--
## General Guidelines

System daemons are expected to be treated similar to
[Guaranteed pods](/docs/tasks/configure-pod-container/quality-service-pod/#create-a-pod-that-gets-assigned-a-qos-class-of-guaranteed). 
System daemons can burst within their bounding control groups and this behavior needs
to be managed as part of kubernetes deployments. For example, `kubelet` should
have its own control group and share `kube-reserved` resources with the
container runtime. However, Kubelet cannot burst and use up all available Node
resources if `kube-reserved` is enforced.
-->
## 一般原則   {#general-guidelines}

系統守護程序一般會被按照類似
[Guaranteed pods](/zh-cn/docs/tasks/configure-pod-container/quality-service-pod/#create-a-pod-that-gets-assigned-a-qos-class-of-guaranteed)
一樣對待。
系統守護程序可以在與其對應的控制組中出現突發資源用量，這一行為要作為
kubernetes 部署的一部分進行管理。
例如，`kubelet` 應該有它自己的控制組並和容器執行時共享 `kube-reserved` 資源。
不過，如果執行了 `kube-reserved` 約束，則 kubelet 不可出現突發負載並用光
節點的所有可用資源。

<!--
Be extra careful while enforcing `system-reserved` reservation since it can lead
to critical system services being CPU starved, OOM killed, or unable
to fork on the node. The
recommendation is to enforce `system-reserved` only if a user has profiled their
nodes exhaustively to come up with precise estimates and is confident in their
ability to recover if any process in that group is oom-killed.

* To begin with enforce 'Allocatable' on `pods`.
* Once adequate monitoring and alerting is in place to track kube system
  daemons, attempt to enforce `kube-reserved` based on usage heuristics.
* If absolutely necessary, enforce `system-reserved` over time.
-->
在執行 `system-reserved` 預留策略時請加倍小心，因為它可能導致節點上的
關鍵系統服務出現 CPU 資源短缺、因為記憶體不足而被終止或者無法在節點上建立程序。
建議只有當用戶詳盡地描述了他們的節點以得出精確的估計值，
並且對該組中程序因記憶體不足而被殺死時，有足夠的信心將其恢復時，
才可以強制執行 `system-reserved` 策略。

* 作為起步，可以先針對 `pods` 上執行 'Allocatable' 約束。
* 一旦用於追蹤系統守護程序的監控和告警的機制到位，可嘗試基於用量估計的
  方式執行 `kube-reserved` 策略。
* 隨著時間推進，在絕對必要的時候可以執行 `system-reserved` 策略。

<!--
The resource requirements of kube system daemons may grow over time as more and
more features are added. Over time, kubernetes project will attempt to bring
down utilization of node system daemons, but that is not a priority as of now.
So expect a drop in `Allocatable` capacity in future releases.
-->
隨著時間推進和越來越多特性被加入，kube 系統守護程序對資源的需求可能也會增加。
以後 kubernetes 專案將嘗試減少對節點系統守護程序的利用，但目前這件事的優先順序
並不是最高。
所以，將來的釋出版本中 `Allocatable` 容量是有可能降低的。

<!-- discussion -->

<!--
## Example Scenario

Here is an example to illustrate Node Allocatable computation:

* Node has `32Gi` of `memory`, `16 CPUs` and `100Gi` of `Storage`
* `--kube-reserved` is set to `cpu=1,memory=2Gi,ephemeral-storage=1Gi`
* `--system-reserved` is set to `cpu=500m,memory=1Gi,ephemeral-storage=1Gi`
* `--eviction-hard` is set to `memory.available<500Mi,nodefs.available<10%`
-->
## 示例場景   {#example-scenario}

這是一個用於說明節點可分配（Node Allocatable）計算方式的示例：

* 節點擁有 `32Gi` `memeory`，`16 CPU` 和 `100Gi` `Storage` 資源
* `--kube-reserved` 被設定為 `cpu=1,memory=2Gi,ephemeral-storage=1Gi`
* `--system-reserved` 被設定為 `cpu=500m,memory=1Gi,ephemeral-storage=1Gi`
* `--eviction-hard` 被設定為 `memory.available<500Mi,nodefs.available<10%`

<!--
Under this scenario, 'Allocatable' will be 14.5 CPUs, 28.5Gi of memory and
`88Gi` of local storage.
Scheduler ensures that the total memory `requests` across all pods on this node does
not exceed 28.5Gi and storage doesn't exceed 88Gi.
Kubelet evicts pods whenever the overall memory usage across pods exceeds 28.5Gi,
or if overall disk usage exceeds 88Gi If all processes on the node consume as
much CPU as they can, pods together cannot consume more than 14.5 CPUs.

If `kube-reserved` and/or `system-reserved` is not enforced and system daemons
exceed their reservation, `kubelet` evicts pods whenever the overall node memory
usage is higher than 31.5Gi or `storage` is greater than 90Gi.
-->
在這個場景下，'Allocatable' 將會是 14.5 CPUs、28.5Gi 記憶體以及 `88Gi` 本地儲存。
排程器保證這個節點上的所有 Pod 的記憶體 `requests` 總量不超過 28.5Gi，
儲存不超過 '88Gi'。
當 Pod 的記憶體使用總量超過 28.5Gi 或者磁碟使用總量超過 88Gi 時，
kubelet 將會驅逐它們。
如果節點上的所有程序都儘可能多地使用 CPU，則 Pod 加起來不能使用超過
14.5 CPUs 的資源。

當沒有執行 `kube-reserved` 和/或 `system-reserved` 策略且系統守護程序
使用量超過其預留時，如果節點記憶體用量高於 31.5Gi 或 `storage` 大於 90Gi，
kubelet 將會驅逐 Pod。

