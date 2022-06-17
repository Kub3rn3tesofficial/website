---
title: Pod 優先順序和搶佔
content_type: concept
weight: 50
---

<!-- 
reviewers:
- davidopp
- wojtek-t
title: Pod Priority and Preemption
content_type: concept
weight: 50
-->

<!-- overview -->

{{< feature-state for_k8s_version="v1.14" state="stable" >}}

<!--  
[Pods](/docs/concepts/workloads/pods/) can have _priority_. Priority indicates the
importance of a Pod relative to other Pods. If a Pod cannot be scheduled, the
scheduler tries to preempt (evict) lower priority Pods to make scheduling of the
pending Pod possible.
-->
[Pod](/zh-cn/docs/concepts/workloads/pods/) 可以有 _優先順序_。
優先順序表示一個 Pod 相對於其他 Pod 的重要性。
如果一個 Pod 無法被排程，排程程式會嘗試搶佔（驅逐）較低優先順序的 Pod，
以使懸決 Pod 可以被排程。

<!-- body -->

{{< warning >}}
<!-- 
In a cluster where not all users are trusted, a malicious user could create Pods
at the highest possible priorities, causing other Pods to be evicted/not get
scheduled.
An administrator can use ResourceQuota to prevent users from creating pods at
high priorities.

See [limit Priority Class consumption by default](/docs/concepts/policy/resource-quotas/#limit-priority-class-consumption-by-default)
for details.
-->
在一個並非所有使用者都是可信的叢集中，惡意使用者可能以最高優先順序建立 Pod，
導致其他 Pod 被驅逐或者無法被排程。
管理員可以使用 ResourceQuota 來阻止使用者建立高優先順序的 Pod。
參見[預設限制優先順序消費](/zh-cn/docs/concepts/policy/resource-quotas/#limit-priority-class-consumption-by-default)。

{{< /warning >}}

<!--  
## How to use priority and preemption

To use priority and preemption:

1.  Add one or more [PriorityClasses](#priorityclass).

1.  Create Pods with[`priorityClassName`](#pod-priority) set to one of the added
    PriorityClasses. Of course you do not need to create the Pods directly;
    normally you would add `priorityClassName` to the Pod template of a
    collection object like a Deployment.

Keep reading for more information about these steps.
-->
## 如何使用優先順序和搶佔

要使用優先順序和搶佔：

1.  新增一個或多個 [PriorityClass](#priorityclass)。

1.  建立 Pod，並將其 [`priorityClassName`](#pod-priority) 設定為新增的 PriorityClass。
    當然你不需要直接建立 Pod；通常，你將會新增 `priorityClassName` 到集合物件（如 Deployment）
    的 Pod 模板中。

繼續閱讀以獲取有關這些步驟的更多資訊。

{{< note >}}
<!-- 
Kubernetes already ships with two PriorityClasses:
`system-cluster-critical` and `system-node-critical`.
These are common classes and are used to [ensure that critical components are always scheduled first](/docs/tasks/administer-cluster/guaranteed-scheduling-critical-addon-pods/).
-->
Kubernetes 已經提供了 2 個 PriorityClass：
`system-cluster-critical` 和 `system-node-critical`。
這些是常見的類，用於[確保始終優先排程關鍵元件](/zh-cn/docs/tasks/administer-cluster/guaranteed-scheduling-critical-addon-pods/)。
{{< /note >}}

<!-- 
## PriorityClass

A PriorityClass is a non-namespaced object that defines a mapping from a
priority class name to the integer value of the priority. The name is specified
in the `name` field of the PriorityClass object's metadata. The value is
specified in the required `value` field. The higher the value, the higher the
priority.
The name of a PriorityClass object must be a valid
[DNS subdomain name](/docs/concepts/overview/working-with-objects/names#dns-subdomain-names),
and it cannot be prefixed with `system-`.
-->
## PriorityClass {#priorityclass}

PriorityClass 是一個無名稱空間物件，它定義了從優先順序類名稱到優先順序整數值的對映。
名稱在 PriorityClass 物件元資料的 `name` 欄位中指定。
值在必填的 `value` 欄位中指定。值越大，優先順序越高。
PriorityClass 物件的名稱必須是有效的
[DNS 子域名](/zh-cn/docs/concepts/overview/working-with-objects/names#dns-subdomain-names)，
並且它不能以 `system-` 為字首。

<!--  
A PriorityClass object can have any 32-bit integer value smaller than or equal
to 1 billion. Larger numbers are reserved for critical system Pods that should
not normally be preempted or evicted. A cluster admin should create one
PriorityClass object for each such mapping that they want.

PriorityClass also has two optional fields: `globalDefault` and `description`.
The `globalDefault` field indicates that the value of this PriorityClass should
be used for Pods without a `priorityClassName`. Only one PriorityClass with
`globalDefault` set to true can exist in the system. If there is no
PriorityClass with `globalDefault` set, the priority of Pods with no
`priorityClassName` is zero.

The `description` field is an arbitrary string. It is meant to tell users of the
cluster when they should use this PriorityClass.
-->
PriorityClass 物件可以設定任何小於或等於 10 億的 32 位整數值。
較大的數字是為通常不應被搶佔或驅逐的關鍵的系統 Pod 所保留的。
叢集管理員應該為這類對映分別建立獨立的 PriorityClass 物件。

PriorityClass 還有兩個可選欄位：`globalDefault` 和 `description`。
`globalDefault` 欄位表示這個 PriorityClass 的值應該用於沒有 `priorityClassName` 的 Pod。
系統中只能存在一個 `globalDefault` 設定為 true 的 PriorityClass。
如果不存在設定了 `globalDefault` 的 PriorityClass，
則沒有 `priorityClassName` 的 Pod 的優先順序為零。

`description` 欄位是一個任意字串。
它用來告訴叢集使用者何時應該使用此 PriorityClass。

<!--  
### Notes about PodPriority and existing clusters

-   If you upgrade an existing cluster without this feature, the priority
    of your existing Pods is effectively zero.

-   Addition of a PriorityClass with `globalDefault` set to `true` does not
    change the priorities of existing Pods. The value of such a PriorityClass is
    used only for Pods created after the PriorityClass is added.

-   If you delete a PriorityClass, existing Pods that use the name of the
    deleted PriorityClass remain unchanged, but you cannot create more Pods that
    use the name of the deleted PriorityClass.
-->
### 關於 PodPriority 和現有叢集的注意事項

-   如果你升級一個已經存在的但尚未使用此特性的叢集，該叢集中已經存在的 Pod 的優先順序等效於零。

-   新增一個將 `globalDefault` 設定為 `true` 的 PriorityClass 不會改變現有 Pod 的優先順序。
    此類 PriorityClass 的值僅用於新增 PriorityClass 後建立的 Pod。

-   如果你刪除了某個 PriorityClass 物件，則使用被刪除的 PriorityClass 名稱的現有 Pod 保持不變，
    但是你不能再建立使用已刪除的 PriorityClass 名稱的 Pod。

<!-- ### Example PriorityClass -->
### PriorityClass 示例

```yaml
apiVersion: scheduling.k8s.io/v1
kind: PriorityClass
metadata:
  name: high-priority
value: 1000000
globalDefault: false
description: "此優先順序類應僅用於 XYZ 服務 Pod。"
```

<!--  
## Non-preempting PriorityClass {#non-preempting-priority-class}

{{< feature-state for_k8s_version="v1.24" state="stable" >}}

Pods with `preemptionPolicy: Never` will be placed in the scheduling queue
ahead of lower-priority pods,
but they cannot preempt other pods.
A non-preempting pod waiting to be scheduled will stay in the scheduling queue,
until sufficient resources are free,
and it can be scheduled.
Non-preempting pods,
like other pods,
are subject to scheduler back-off.
This means that if the scheduler tries these pods and they cannot be scheduled,
they will be retried with lower frequency,
allowing other pods with lower priority to be scheduled before them.

Non-preempting pods may still be preempted by other,
high-priority pods.
-->
## 非搶佔式 PriorityClass {#non-preempting-priority-class}

{{< feature-state for_k8s_version="v1.24" state="stable" >}}

配置了 `preemptionPolicy: Never` 的 Pod 將被放置在排程佇列中較低優先順序 Pod 之前，
但它們不能搶佔其他 Pod。等待排程的非搶佔式 Pod 將留在排程佇列中，直到有足夠的可用資源，
它才可以被排程。非搶佔式 Pod，像其他 Pod 一樣，受排程程式回退的影響。
這意味著如果排程程式嘗試這些 Pod 並且無法排程它們，它們將以更低的頻率被重試，
從而允許其他優先順序較低的 Pod 排在它們之前。

非搶佔式 Pod 仍可能被其他高優先順序 Pod 搶佔。

<!--  
`preemptionPolicy` defaults to `PreemptLowerPriority`,
which will allow pods of that PriorityClass to preempt lower-priority pods
(as is existing default behavior).
If `preemptionPolicy` is set to `Never`,
pods in that PriorityClass will be non-preempting.

An example use case is for data science workloads.
A user may submit a job that they want to be prioritized above other workloads,
but do not wish to discard existing work by preempting running pods.
The high priority job with `preemptionPolicy: Never` will be scheduled
ahead of other queued pods,
as soon as sufficient cluster resources "naturally" become free.
-->
`preemptionPolicy` 預設為 `PreemptLowerPriority`，
這將允許該 PriorityClass 的 Pod 搶佔較低優先順序的 Pod（現有預設行為也是如此）。
如果 `preemptionPolicy` 設定為 `Never`，則該 PriorityClass 中的 Pod 將是非搶佔式的。

資料科學工作負載是一個示例用例。使用者可以提交他們希望優先於其他工作負載的作業，
但不希望因為搶佔執行中的 Pod 而導致現有工作被丟棄。
設定為 `preemptionPolicy: Never` 的高優先順序作業將在其他排隊的 Pod 之前被排程，
只要足夠的叢集資源“自然地”變得可用。

<!-- ### Example Non-preempting PriorityClass -->
### 非搶佔式 PriorityClass 示例

```yaml
apiVersion: scheduling.k8s.io/v1
kind: PriorityClass
metadata:
  name: high-priority-nonpreempting
value: 1000000
preemptionPolicy: Never
globalDefault: false
description: "This priority class will not cause other pods to be preempted."
```

<!-- 
## Pod priority

After you have one or more PriorityClasses, you can create Pods that specify one
of those PriorityClass names in their specifications. The priority admission
controller uses the `priorityClassName` field and populates the integer value of
the priority. If the priority class is not found, the Pod is rejected.

The following YAML is an example of a Pod configuration that uses the
PriorityClass created in the preceding example. The priority admission
controller checks the specification and resolves the priority of the Pod to
1000000.
-->
## Pod 優先順序 {#pod-priority}

在你擁有一個或多個 PriorityClass 物件之後，
你可以建立在其規約中指定這些 PriorityClass 名稱之一的 Pod。
優先順序准入控制器使用 `priorityClassName` 欄位並填充優先順序的整數值。
如果未找到所指定的優先順序類，則拒絕 Pod。

以下 YAML 是 Pod 配置的示例，它使用在前面的示例中建立的 PriorityClass。
優先順序准入控制器檢查 Pod 規約並將其優先順序解析為 1000000。

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
  labels:
    env: test
spec:
  containers:
  - name: nginx
    image: nginx
    imagePullPolicy: IfNotPresent
  priorityClassName: high-priority
```

<!--  
### Effect of Pod priority on scheduling order

When Pod priority is enabled, the scheduler orders pending Pods by
their priority and a pending Pod is placed ahead of other pending Pods
with lower priority in the scheduling queue. As a result, the higher
priority Pod may be scheduled sooner than Pods with lower priority if
its scheduling requirements are met. If such Pod cannot be scheduled,
scheduler will continue and tries to schedule other lower priority Pods.
-->
### Pod 優先順序對排程順序的影響

當啟用 Pod 優先順序時，排程程式會按優先順序對懸決 Pod 進行排序，
並且每個懸決的 Pod 會被放置在排程佇列中其他優先順序較低的懸決 Pod 之前。
因此，如果滿足排程要求，較高優先順序的 Pod 可能會比具有較低優先順序的 Pod 更早排程。
如果無法排程此類 Pod，排程程式將繼續並嘗試排程其他較低優先順序的 Pod。

<!-- 
## Preemption

When Pods are created, they go to a queue and wait to be scheduled. The
scheduler picks a Pod from the queue and tries to schedule it on a Node. If no
Node is found that satisfies all the specified requirements of the Pod,
preemption logic is triggered for the pending Pod. Let's call the pending Pod P.
Preemption logic tries to find a Node where removal of one or more Pods with
lower priority than P would enable P to be scheduled on that Node. If such a
Node is found, one or more lower priority Pods get evicted from the Node. After
the Pods are gone, P can be scheduled on the Node.
-->
## 搶佔    {#preemption}

Pod 被建立後會進入佇列等待排程。
排程器從佇列中挑選一個 Pod 並嘗試將它排程到某個節點上。
如果沒有找到滿足 Pod 的所指定的所有要求的節點，則觸發對懸決 Pod 的搶佔邏輯。
讓我們將懸決 Pod 稱為 P。搶佔邏輯試圖找到一個節點，
在該節點中刪除一個或多個優先順序低於 P 的 Pod，則可以將 P 排程到該節點上。
如果找到這樣的節點，一個或多個優先順序較低的 Pod 會被從節點中驅逐。
被驅逐的 Pod 消失後，P 可以被排程到該節點上。

<!--  
### User exposed information

When Pod P preempts one or more Pods on Node N, `nominatedNodeName` field of Pod
P's status is set to the name of Node N. This field helps scheduler track
resources reserved for Pod P and also gives users information about preemptions
in their clusters.

Please note that Pod P is not necessarily scheduled to the "nominated Node".
The scheduler always tries the "nominated Node" before iterating over any other nodes.
After victim Pods are preempted, they get their graceful termination period. If
another node becomes available while scheduler is waiting for the victim Pods to
terminate, scheduler may use the other node to schedule Pod P. As a result
`nominatedNodeName` and `nodeName` of Pod spec are not always the same. Also, if
scheduler preempts Pods on Node N, but then a higher priority Pod than Pod P
arrives, scheduler may give Node N to the new higher priority Pod. In such a
case, scheduler clears `nominatedNodeName` of Pod P. By doing this, scheduler
makes Pod P eligible to preempt Pods on another Node.
-->
### 使用者暴露的資訊

當 Pod P 搶佔節點 N 上的一個或多個 Pod 時，
Pod P 狀態的 `nominatedNodeName` 欄位被設定為節點 N 的名稱。
該欄位幫助排程程式跟蹤為 Pod P 保留的資源，併為使用者提供有關其叢集中搶佔的資訊。

請注意，Pod P 不一定會排程到“被提名的節點（Nominated Node）”。
排程程式總是在迭代任何其他節點之前嘗試“指定節點”。
在 Pod 因搶佔而犧牲時，它們將獲得體面終止期。
如果排程程式正在等待犧牲者 Pod 終止時另一個節點變得可用，
則排程程式可以使用另一個節點來排程 Pod P。
因此，Pod 規約中的 `nominatedNodeName` 和 `nodeName` 並不總是相同。
此外，如果排程程式搶佔節點 N 上的 Pod，但隨後比 Pod P 更高優先順序的 Pod 到達，
則排程程式可能會將節點 N 分配給新的更高優先順序的 Pod。
在這種情況下，排程程式會清除 Pod P 的 `nominatedNodeName`。
透過這樣做，排程程式使 Pod P 有資格搶佔另一個節點上的 Pod。

<!-- 
### Limitations of preemption

#### Graceful termination of preemption victims

When Pods are preempted, the victims get their
[graceful termination period](/docs/concepts/workloads/pods/pod-lifecycle/#pod-termination).
They have that much time to finish their work and exit. If they don't, they are
killed. This graceful termination period creates a time gap between the point
that the scheduler preempts Pods and the time when the pending Pod (P) can be
scheduled on the Node (N). In the meantime, the scheduler keeps scheduling other
pending Pods. As victims exit or get terminated, the scheduler tries to schedule
Pods in the pending queue. Therefore, there is usually a time gap between the
point that scheduler preempts victims and the time that Pod P is scheduled. In
order to minimize this gap, one can set graceful termination period of lower
priority Pods to zero or a small number.
-->
### 搶佔的限制

#### 被搶佔犧牲者的體面終止

當 Pod 被搶佔時，犧牲者會得到他們的
[體面終止期](/zh-cn/docs/concepts/workloads/pods/pod-lifecycle/#pod-termination)。
它們可以在體面終止期內完成工作並退出。如果它們不這樣做就會被殺死。
這個體面終止期在排程程式搶佔 Pod 的時間點和待處理的 Pod (P) 
可以在節點 (N) 上排程的時間點之間劃分出了一個時間跨度。
同時，排程器會繼續排程其他待處理的 Pod。當犧牲者退出或被終止時，
排程程式會嘗試在待處理佇列中排程 Pod。
因此，排程器搶佔犧牲者的時間點與 Pod P 被排程的時間點之間通常存在時間間隔。
為了最小化這個差距，可以將低優先順序 Pod 的體面終止時間設定為零或一個小數字。

<!-- 
#### PodDisruptionBudget is supported, but not guaranteed

A [PodDisruptionBudget](/docs/concepts/workloads/pods/disruptions/) (PDB)
allows application owners to limit the number of Pods of a replicated application
that are down simultaneously from voluntary disruptions. Kubernetes supports
PDB when preempting Pods, but respecting PDB is best effort. The scheduler tries
to find victims whose PDB are not violated by preemption, but if no such victims
are found, preemption will still happen, and lower priority Pods will be removed
despite their PDBs being violated.
-->
#### 支援 PodDisruptionBudget，但不保證

[PodDisruptionBudget](/zh-cn/docs/concepts/workloads/pods/disruptions/) 
(PDB) 允許多副本應用程式的所有者限制因自願性質的干擾而同時終止的 Pod 數量。
Kubernetes 在搶佔 Pod 時支援 PDB，但對 PDB 的支援是基於盡力而為原則的。
排程器會嘗試尋找不會因被搶佔而違反 PDB 的犧牲者，但如果沒有找到這樣的犧牲者，
搶佔仍然會發生，並且即使違反了 PDB 約束也會刪除優先順序較低的 Pod。

<!-- 
#### Inter-Pod affinity on lower-priority Pods

A Node is considered for preemption only when the answer to this question is
yes: "If all the Pods with lower priority than the pending Pod are removed from
the Node, can the pending Pod be scheduled on the Node?"

{{< note >}}
Preemption does not necessarily remove all lower-priority
Pods. If the pending Pod can be scheduled by removing fewer than all
lower-priority Pods, then only a portion of the lower-priority Pods are removed.
Even so, the answer to the preceding question must be yes. If the answer is no,
the Node is not considered for preemption.
{{< /note >}}
-->
#### 與低優先順序 Pod 之間的 Pod 間親和性

只有當這個問題的答案是肯定的時，才考慮在一個節點上執行搶佔操作：
“如果從此節點上刪除優先順序低於懸決 Pod 的所有 Pod，懸決 Pod 是否可以在該節點上排程？”

{{< note >}}
搶佔並不一定會刪除所有較低優先順序的 Pod。
如果懸決 Pod 可以透過刪除少於所有較低優先順序的 Pod 來排程，
那麼只有一部分較低優先順序的 Pod 會被刪除。
即便如此，上述問題的答案必須是肯定的。
如果答案是否定的，則不考慮在該節點上執行搶佔。
{{< /note >}}

<!-- 
If a pending Pod has inter-pod {{< glossary_tooltip text="affinity" term_id="affinity" >}}
to one or more of the lower-priority Pods on the Node, the inter-Pod affinity
rule cannot be satisfied in the absence of those lower-priority Pods. In this case, 
the scheduler does not preempt any Pods on the Node. Instead, it looks for another
Node. The scheduler might find a suitable Node or it might not. There is no 
guarantee that the pending Pod can be scheduled.

Our recommended solution for this problem is to create inter-Pod affinity only
towards equal or higher priority Pods.
-->
如果懸決 Pod 與節點上的一個或多個較低優先順序 Pod 具有 Pod 間{{< glossary_tooltip text="親和性" term_id="affinity" >}}，
則在沒有這些較低優先順序 Pod 的情況下，無法滿足 Pod 間親和性規則。
在這種情況下，排程程式不會搶佔節點上的任何 Pod。
相反，它尋找另一個節點。排程程式可能會找到合適的節點，
也可能不會。無法保證懸決 Pod 可以被排程。

我們針對此問題推薦的解決方案是僅針對同等或更高優先順序的 Pod 設定 Pod 間親和性。

<!-- 
#### Cross node preemption

Suppose a Node N is being considered for preemption so that a pending Pod P can
be scheduled on N. P might become feasible on N only if a Pod on another Node is
preempted. Here's an example:

*   Pod P is being considered for Node N.
*   Pod Q is running on another Node in the same Zone as Node N.
*   Pod P has Zone-wide anti-affinity with Pod Q (`topologyKey:
    topology.kubernetes.io/zone`).
*   There are no other cases of anti-affinity between Pod P and other Pods in
    the Zone.
*   In order to schedule Pod P on Node N, Pod Q can be preempted, but scheduler
    does not perform cross-node preemption. So, Pod P will be deemed
    unschedulable on Node N.

If Pod Q were removed from its Node, the Pod anti-affinity violation would be
gone, and Pod P could possibly be scheduled on Node N.

We may consider adding cross Node preemption in future versions if there is
enough demand and if we find an algorithm with reasonable performance.
-->
#### 跨節點搶佔

假設正在考慮在一個節點 N 上執行搶佔，以便可以在 N 上排程待處理的 Pod P。
只有當另一個節點上的 Pod 被搶佔時，P 才可能在 N 上變得可行。
下面是一個例子：

*   正在考慮將 Pod P 排程到節點 N 上。
*   Pod Q 正在與節點 N 位於同一區域的另一個節點上執行。
*   Pod P 與 Pod Q 具有 Zone 維度的反親和（`topologyKey:topology.kubernetes.io/zone`）。
*   Pod P 與 Zone 中的其他 Pod 之間沒有其他反親和性設定。
*   為了在節點 N 上排程 Pod P，可以搶佔 Pod Q，但排程器不會進行跨節點搶佔。
    因此，Pod P 將被視為在節點 N 上不可排程。

如果將 Pod Q 從所在節點中移除，則不會違反 Pod 間反親和性約束，
並且 Pod P 可能會被排程到節點 N 上。

如果有足夠的需求，並且如果我們找到效能合理的演算法，
我們可能會考慮在未來版本中新增跨節點搶佔。

<!-- 
## Troubleshooting

Pod priority and pre-emption can have unwanted side effects. Here are some
examples of potential problems and ways to deal with them.
-->
## 故障排除

Pod 優先順序和搶佔可能會產生不必要的副作用。以下是一些潛在問題的示例以及處理這些問題的方法。

<!--  
### Pods are preempted unnecessarily

Preemption removes existing Pods from a cluster under resource pressure to make
room for higher priority pending Pods. If you give high priorities to
certain Pods by mistake, these unintentionally high priority Pods may cause
preemption in your cluster. Pod priority is specified by setting the
`priorityClassName` field in the Pod's specification. The integer value for
priority is then resolved and populated to the `priority` field of `podSpec`.

To address the problem, you can change the `priorityClassName` for those Pods
to use lower priority classes, or leave that field empty. An empty
`priorityClassName` is resolved to zero by default.

When a Pod is preempted, there will be events recorded for the preempted Pod.
Preemption should happen only when a cluster does not have enough resources for
a Pod. In such cases, preemption happens only when the priority of the pending
Pod (preemptor) is higher than the victim Pods. Preemption must not happen when
there is no pending Pod, or when the pending Pods have equal or lower priority
than the victims. If preemption happens in such scenarios, please file an issue.
-->
### Pod 被不必要地搶佔

搶佔在資源壓  力較大時從叢集中刪除現有 Pod，為更高優先順序的懸決 Pod 騰出空間。
如果你錯誤地為某些 Pod 設定了高優先順序，這些無意的高優先順序 Pod 可能會導致叢集中出現搶佔行為。
Pod 優先順序是透過設定 Pod 規約中的 `priorityClassName` 欄位來指定的。
優先順序的整數值然後被解析並填充到 `podSpec` 的 `priority` 欄位。

為了解決這個問題，你可以將這些 Pod 的 `priorityClassName` 更改為使用較低優先順序的類，
或者將該欄位留空。預設情況下，空的 `priorityClassName` 解析為零。

當 Pod 被搶佔時，叢集會為被搶佔的 Pod 記錄事件。只有當叢集沒有足夠的資源用於 Pod 時，
才會發生搶佔。在這種情況下，只有當懸決 Pod（搶佔者）的優先順序高於受害 Pod 時才會發生搶佔。
當沒有懸決 Pod，或者懸決 Pod 的優先順序等於或低於犧牲者時，不得發生搶佔。
如果在這種情況下發生搶佔，請提出問題。

<!-- 
### Pods are preempted, but the preemptor is not scheduled

When pods are preempted, they receive their requested graceful termination
period, which is by default 30 seconds. If the victim Pods do not terminate within
this period, they are forcibly terminated. Once all the victims go away, the
preemptor Pod can be scheduled.

While the preemptor Pod is waiting for the victims to go away, a higher priority
Pod may be created that fits on the same Node. In this case, the scheduler will
schedule the higher priority Pod instead of the preemptor.

This is expected behavior: the Pod with the higher priority should take the place
of a Pod with a lower priority.
-->
### 有 Pod 被搶佔，但搶佔者並沒有被排程

當 Pod 被搶佔時，它們會收到請求的體面終止期，預設為 30 秒。
如果受害 Pod 在此期限內沒有終止，它們將被強制終止。
一旦所有犧牲者都離開，就可以排程搶佔者 Pod。

在搶佔者 Pod 等待犧牲者離開的同時，可能某個適合同一個節點的更高優先順序的 Pod 被建立。
在這種情況下，排程器將排程優先順序更高的 Pod 而不是搶佔者。

這是預期的行為：具有較高優先順序的 Pod 應該取代具有較低優先順序的 Pod。

<!-- 
### Higher priority Pods are preempted before lower priority pods

The scheduler tries to find nodes that can run a pending Pod. If no node is
found, the scheduler tries to remove Pods with lower priority from an arbitrary
node in order to make room for the pending pod.
If a node with low priority Pods is not feasible to run the pending Pod, the scheduler
may choose another node with higher priority Pods (compared to the Pods on the
other node) for preemption. The victims must still have lower priority than the
preemptor Pod.

When there are multiple nodes available for preemption, the scheduler tries to
choose the node with a set of Pods with lowest priority. However, if such Pods
have PodDisruptionBudget that would be violated if they are preempted then the
scheduler may choose another node with higher priority Pods.

When multiple nodes exist for preemption and none of the above scenarios apply,
the scheduler chooses a node with the lowest priority.
-->
### 優先順序較高的 Pod 在優先順序較低的 Pod 之前被搶佔

排程程式嘗試查詢可以執行懸決 Pod 的節點。如果沒有找到這樣的節點，
排程程式會嘗試從任意節點中刪除優先順序較低的 Pod，以便為懸決 Pod 騰出空間。
如果具有低優先順序 Pod 的節點無法執行懸決 Pod，
排程器可能會選擇另一個具有更高優先順序 Pod 的節點（與其他節點上的 Pod 相比）進行搶佔。
犧牲者的優先順序必須仍然低於搶佔者 Pod。

當有多個節點可供執行搶佔操作時，排程器會嘗試選擇具有一組優先順序最低的 Pod 的節點。
但是，如果此類 Pod 具有 PodDisruptionBudget，當它們被搶佔時，
則會違反 PodDisruptionBudget，那麼排程程式可能會選擇另一個具有更高優先順序 Pod 的節點。

當存在多個節點搶佔且上述場景均不適用時，排程器會選擇優先順序最低的節點。

<!-- 
## Interactions between Pod priority and quality of service {#interactions-of-pod-priority-and-qos}

Pod priority and {{< glossary_tooltip text="QoS class" term_id="qos-class" >}}
are two orthogonal features with few interactions and no default restrictions on
setting the priority of a Pod based on its QoS classes. The scheduler's
preemption logic does not consider QoS when choosing preemption targets.
Preemption considers Pod priority and attempts to choose a set of targets with
the lowest priority. Higher-priority Pods are considered for preemption only if
the removal of the lowest priority Pods is not sufficient to allow the scheduler
to schedule the preemptor Pod, or if the lowest priority Pods are protected by
`PodDisruptionBudget`.
-->
## Pod 優先順序和服務質量之間的相互作用 {#interactions-of-pod-priority-and-qos}

Pod 優先順序和 {{<glossary_tooltip text="QoS 類" term_id="qos-class" >}}
是兩個正交特徵，互動很少，並且對基於 QoS 類設定 Pod 的優先順序沒有預設限制。
排程器的搶佔邏輯在選擇搶佔目標時不考慮 QoS。
搶佔會考慮 Pod 優先順序並嘗試選擇一組優先順序最低的目標。
僅當移除優先順序最低的 Pod 不足以讓排程程式排程搶佔式 Pod，
或者最低優先順序的 Pod 受 PodDisruptionBudget 保護時，才會考慮優先順序較高的 Pod。

<!-- 
The kubelet uses Priority to determine pod order for [node-pressure eviction](/docs/concepts/scheduling-eviction/node-pressure-eviction/).
You can use the QoS class to estimate the order in which pods are most likely
to get evicted. The kubelet ranks pods for eviction based on the following factors:

  1. Whether the starved resource usage exceeds requests
  1. Pod Priority
  1. Amount of resource usage relative to requests 

See [evicting end-user pods](/docs/concepts/scheduling-eviction/node-pressure-eviction/#pod-selection-for-kubelet-eviction)
for more details.

kubelet node-pressure eviction does not evict Pods when their
usage does not exceed their requests. If a Pod with lower priority is not
exceeding its requests, it won't be evicted. Another Pod with higher priority
that exceeds its requests may be evicted.
-->
kubelet 使用優先順序來確定
[節點壓力驅逐](/zh-cn/docs/concepts/scheduling-eviction/pod-priority-preemption/) Pod 的順序。
你可以使用 QoS 類來估計 Pod 最有可能被驅逐的順序。kubelet 根據以下因素對 Pod 進行驅逐排名：

  1. 對緊俏資源的使用是否超過請求值
  1. Pod 優先順序
  1. 相對於請求的資源使用量

有關更多詳細資訊，請參閱
[kubelet 驅逐時 Pod 的選擇](/zh-cn/docs/concepts/scheduling-eviction/node-pressure-eviction/#pod-selection-for-kubelet-eviction)。

當某 Pod 的資源用量未超過其請求時，kubelet 節點壓力驅逐不會驅逐該 Pod。
如果優先順序較低的 Pod 沒有超過其請求，則不會被驅逐。
另一個優先順序高於其請求的 Pod 可能會被驅逐。

## {{% heading "whatsnext" %}}

<!-- 
* Read about using ResourceQuotas in connection with PriorityClasses: 
  [limit Priority Class consumption by default](/docs/concepts/policy/resource-quotas/#limit-priority-class-consumption-by-default)
* Learn about [Pod Disruption](/docs/concepts/workloads/pods/disruptions/)
* Learn about [API-initiated Eviction](/docs/reference/generated/kubernetes-api/v1.23/)
* Learn about [Node-pressure Eviction](/docs/concepts/scheduling-eviction/node-pressure-eviction/)
-->
* 閱讀有關將 ResourceQuota 與 PriorityClass 結合使用的資訊：
  [預設限制優先順序消費](/zh-cn/docs/concepts/policy/resource-quotas/#limit-priority-class-consumption-by-default)
* 瞭解 [Pod 干擾](/zh-cn/docs/concepts/workloads/pods/disruptions/)
* 瞭解 [API 發起的驅逐](/docs/reference/generated/kubernetes-api/v1.23/)
* 瞭解[節點壓力驅逐](/zh-cn/docs/concepts/scheduling-eviction/pod-priority-preemption/)
