---
title: 程序 ID 約束與預留
content_type: concept
weight: 40
---

<!--
reviewers:
- derekwaynecarr
title: Process ID Limits And Reservations
content_type: concept
weight: 40
-->

<!-- overview -->

{{< feature-state for_k8s_version="v1.20" state="stable" >}}

<!--
Kubernetes allow you to limit the number of process IDs (PIDs) that a
{{< glossary_tooltip term_id="Pod" text="Pod" >}} can use.
You can also reserve a number of allocatable PIDs for each {{< glossary_tooltip term_id="node" text="node" >}}
for use by the operating system and daemons (rather than by Pods).
-->
Kubernetes 允許你限制一個 {{< glossary_tooltip term_id="Pod" text="Pod" >}} 中可以使用的
程序 ID（PID）數目。你也可以為每個 {{< glossary_tooltip term_id="node" text="節點" >}}
預留一定數量的可分配的 PID，供作業系統和守護程序（而非 Pod）使用。

<!-- body -->

<!--
Process IDs (PIDs) are a fundamental resource on nodes. It is trivial to hit the
task limit without hitting any other resource limits, which can then cause
instability to a host machine.
-->
程序 ID（PID）是節點上的一種基礎資源。很容易就會在尚未超出其它資源約束的時候就
已經觸及任務個數上限，進而導致宿主機器不穩定。

<!--
Cluster administrators require mechanisms to ensure that Pods running in the
cluster cannot induce PID exhaustion that prevents host daemons (such as the
{{< glossary_tooltip text="kubelet" term_id="kubelet" >}} or
{{< glossary_tooltip text="kube-proxy" term_id="kube-proxy" >}},
and potentially also the container runtime) from running.
In addition, it is important to ensure that PIDs are limited among Pods in order
to ensure they have limited impact on other workloads on the same node.
-->
叢集管理員需要一定的機制來確保叢集中執行的 Pod 不會導致 PID 資源枯竭，甚而
造成宿主機上的守護程序（例如
{{< glossary_tooltip text="kubelet" term_id="kubelet" >}} 或者
{{< glossary_tooltip text="kube-proxy" term_id="kube-proxy" >}}
乃至包括容器執行時本身）無法正常執行。
此外，確保 Pod 中 PID 的個數受限對於保證其不會影響到同一節點上其它負載也很重要。

{{< note >}}
<!--
On certain Linux installations, the operating system sets the PIDs limit to a low default,
such as `32768`. Consider raising the value of `/proc/sys/kernel/pid_max`.
-->
在某些 Linux 安裝環境中，作業系統會將 PID 約束設定為一個較低的預設值，例如
`32768`。這時可以考慮提升 `/proc/sys/kernel/pid_max` 的設定值。
{{< /note >}}

<!--
You can configure a kubelet to limit the number of PIDs a given Pod can consume.
For example, if your node's host OS is set to use a maximum of `262144` PIDs and
expect to host less than `250` Pods, one can give each Pod a budget of `1000`
PIDs to prevent using up that node's overall number of available PIDs. If the
admin wants to overcommit PIDs similar to CPU or memory, they may do so as well
with some additional risks. Either way, a single Pod will not be able to bring
the whole machine down. This kind of resource limiting helps to prevent simple
fork bombs from affecting operation of an entire cluster.
-->
你可以配置 kubelet 限制給定 Pod 能夠使用的 PID 個數。
例如，如果你的節點上的宿主作業系統被設定為最多可使用 `262144` 個 PID，同時預期
節點上會執行的 Pod 個數不會超過 `250`，那麼你可以為每個 Pod 設定 `1000` 個 PID
的預算，避免耗盡該節點上可用 PID 的總量。
如果管理員系統像 CPU 或記憶體那樣允許對 PID 進行過量分配（Overcommit），他們也可以
這樣做，只是會有一些額外的風險。不管怎樣，任何一個 Pod 都不可以將整個機器的執行
狀態破壞。這類資源限制有助於避免簡單的派生炸彈（Fork
Bomb）影響到整個叢集的執行。

<!--
Per-Pod PID limiting allows administrators to protect one Pod from another, but
does not ensure that all Pods scheduled onto that host are unable to impact the node overall.
Per-Pod limiting also does not protect the node agents themselves from PID exhaustion.

You can also reserve an amount of PIDs for node overhead, separate from the
allocation to Pods. This is similar to how you can reserve CPU, memory, or other
resources for use by the operating system and other facilities outside of Pods
and their containers.
-->
在 Pod 級別設定 PID 限制使得管理員能夠保護 Pod 之間不會互相傷害，不過無法
確保所有排程到該宿主機器上的所有 Pod 都不會影響到節點整體。
Pod 級別的限制也無法保護節點代理任務自身不會受到 PID 耗盡的影響。

你也可以預留一定量的 PID，作為節點的額外開銷，與分配給 Pod 的 PID 集合獨立。
這有點類似於在給作業系統和其它設施預留 CPU、記憶體或其它資源時所做的操作，
這些任務都在 Pod 及其所包含的容器之外執行。

<!--
PID limiting is a an important sibling to [compute
resource](/docs/concepts/configuration/manage-resources-containers/) requests
and limits. However, you specify it in a different way: rather than defining a
Pod's resource limit in the `.spec` for a Pod, you configure the limit as a
setting on the kubelet. Pod-defined PID limits are not currently supported.
-->
PID 限制是與[計算資源](/zh-cn/docs/concepts/configuration/manage-resources-containers/)
請求和限制相輔相成的一種機制。不過，你需要用一種不同的方式來設定這一限制：
你需要將其設定到 kubelet 上而不是在 Pod 的 `.spec` 中為 Pod 設定資源限制。
目前還不支援在 Pod 級別設定 PID 限制。


{{< caution >}}
<!--
This means that the limit that applies to a Pod may be different depending on
where the Pod is scheduled. To make things simple, it's easiest if all Nodes use
the same PID resource limits and reservations.
-->
這意味著，施加在 Pod 之上的限制值可能因為 Pod 執行所在的節點不同而有差別。
為了簡化系統，最簡單的方法是為所有節點設定相同的 PID 資源限制和預留值。
{{< /caution >}}

<!--
## Node PID limits

Kubernetes allows you to reserve a number of process IDs for the system use. To
configure the reservation, use the parameter `pid=<number>` in the
`--system-reserved` and `--kube-reserved` command line options to the kubelet.
The value you specified declares that the specified number of process IDs will
be reserved for the system as a whole and for Kubernetes system daemons
respectively.
-->
## 節點級別 PID 限制   {#node-pid-limits}

Kubernetes 允許你為系統預留一定量的程序 ID。為了配置預留數量，你可以使用
kubelet 的 `--system-reserved` 和 `--kube-reserved` 命令列選項中的引數
`pid=<number>`。你所設定的引數值分別用來宣告為整個系統和 Kubernetes 系統
守護程序所保留的程序 ID 數目。

{{< note >}}
<!--
Before Kubernetes version 1.20, PID resource limiting with Node-level
reservations required enabling the [feature
gate](/docs/reference/command-line-tools-reference/feature-gates/)
`SupportNodePidsLimit` to work.
-->
在 Kubernetes 1.20 版本之前，在節點級別透過 PID 資源限制預留 PID 的能力
需要啟用[特性門控](/zh-cn/docs/reference/command-line-tools-reference/feature-gates/)
`SupportNodePidsLimit` 才行。
{{< /note >}}

<!--
## Pod PID limits

Kubernetes allows you to limit the number of processes running in a Pod. You
specify this limit at the node level, rather than configuring it as a resource
limit for a particular Pod. Each Node can have a different PID limit.  
To configure the limit, you can specify the command line parameter `--pod-max-pids`
to the kubelet, or set `PodPidsLimit` in the kubelet
[configuration file](/docs/tasks/administer-cluster/kubelet-config-file/).
-->
## Pod 級別 PID 限制   {#pod-pid-limits}

Kubernetes 允許你限制 Pod 中執行的程序個數。你可以在節點級別設定這一限制，
而不是為特定的 Pod 來將其設定為資源限制。
每個節點都可以有不同的 PID 限制設定。
要設定限制值，你可以設定 kubelet 的命令列引數 `--pod-max-pids`，或者
在 kubelet 的[配置檔案](/zh-cn/docs/tasks/administer-cluster/kubelet-config-file/)
中設定 `PodPidsLimit`。

{{< note >}}
<!--
Before Kubernetes version 1.20, PID resource limiting for Pods required enabling
the [feature gate](/docs/reference/command-line-tools-reference/feature-gates/)
`SupportPodPidsLimit` to work.
-->
在 Kubernetes 1.20 版本之前，為 Pod 設定 PID 資源限制的能力需要啟用
[特性門控](/zh-cn/docs/reference/command-line-tools-reference/feature-gates/)
`SupportNodePidsLimit` 才行。
{{< /note >}}

<!--
## PID based eviction

You can configure kubelet to start terminating a Pod when it is misbehaving and consuming abnormal amount of resources.
This feature is called eviction. You can
[Configure Out of Resource Handling](/docs/concepts/scheduling-eviction/node-pressure-eviction/)
for various eviction signals.
Use `pid.available` eviction signal to configure the threshold for number of PIDs used by Pod.
You can set soft and hard eviction policies.
However, even with the hard eviction policy, if the number of PIDs growing very fast,
node can still get into unstable state by hitting the node PIDs limit.
Eviction signal value is calculated periodically and does NOT enforce the limit.
-->
## 基於 PID 的驅逐    {#pid-based-eviction}

你可以配置 kubelet 使之在 Pod 行為不正常或者消耗不正常數量資源的時候將其終止。
這一特性稱作驅逐。你可以針對不同的驅逐訊號
[配置資源不足的處理](/zh-cn/docs/concepts/scheduling-eviction/node-pressure-eviction/)。
使用 `pid.available` 驅逐訊號來配置 Pod 使用的 PID 個數的閾值。
你可以設定硬性的和軟性的驅逐策略。不過，即使使用硬性的驅逐策略，
如果 PID 個數增長過快，節點仍然可能因為觸及節點 PID 限制而進入一種不穩定狀態。
驅逐訊號的取值是週期性計算的，而不是一直能夠強制實施約束。

<!--
PID limiting - per Pod and per Node sets the hard limit.
Once the limit is hit, workload will start experiencing failures when trying to get a new PID.
It may or may not lead to rescheduling of a Pod,
depending on how workload reacts on these failures and how liveleness and readiness
probes are configured for the Pod. However, if limits were set correctly,
you can guarantee that other Pods workload and system processes will not run out of PIDs
when one Pod is misbehaving.
-->
Pod 級別和節點級別的 PID 限制會設定硬性限制。
一旦觸及限制值，工作負載會在嘗試獲得新的 PID 時開始遇到問題。
這可能會也可能不會導致 Pod 被重新排程，取決於工作負載如何應對這類失敗
以及 Pod 的存活性和就緒態探測是如何配置的。
可是，如果限制值被正確設定，你可以確保其它 Pod 負載和系統程序不會因為某個
Pod 行為不正常而沒有 PID 可用。

## {{% heading "whatsnext" %}}

<!--
- Refer to the [PID Limiting enhancement document](https://github.com/kubernetes/enhancements/blob/097b4d8276bc9564e56adf72505d43ce9bc5e9e8/keps/sig-node/20190129-pid-limiting.md) for more information.
- For historical context, read
  [Process ID Limiting for Stability Improvements in Kubernetes 1.14](/blog/2019/04/15/process-id-limiting-for-stability-improvements-in-kubernetes-1.14/).
- Read [Managing Resources for Containers](/docs/concepts/configuration/manage-resources-containers/).
- Learn how to [Configure Out of Resource Handling](/docs/concepts/scheduling-eviction/node-pressure-eviction/).
-->
- 參閱 [PID 約束改進文件](https://github.com/kubernetes/enhancements/blob/097b4d8276bc9564e56adf72505d43ce9bc5e9e8/keps/sig-node/20190129-pid-limiting.md)
  以瞭解更多資訊。
- 關於歷史背景，請閱讀
  [Kubernetes 1.14 中限制程序 ID 以提升穩定性](/blog/2019/04/15/process-id-limiting-for-stability-improvements-in-kubernetes-1.14/)
  的博文。
- 請閱讀[為容器管理資源](/zh-cn/docs/concepts/configuration/manage-resources-containers/)。
- 學習如何[配置資源不足情況的處理](/zh-cn/docs/concepts/scheduling-eviction/node-pressure-eviction/)。

