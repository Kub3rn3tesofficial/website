---
title: ReplicationController
feature:
  title: 自我修復
  anchor: ReplicationController 如何工作
  description: >
    重新啟動失敗的容器，在節點死亡時替換並重新排程容器，殺死不響應使用者定義的健康檢查的容器，並且在它們準備好服務之前不會將它們公佈給客戶端。
content_type: concept
weight: 90
---     

<!--
reviewers:
- bprashanth
- janetkuo
title: ReplicationController
feature:
  title: Self-healing
  anchor: How a ReplicationController Works
  description: >
    Restarts containers that fail, replaces and reschedules containers when nodes die, kills containers that don't respond to your user-defined health check, and doesn't advertise them to clients until they are ready to serve.

content_type: concept
weight: 90
-->

<!-- overview -->

<!--
A [`Deployment`](/docs/concepts/workloads/controllers/deployment/) that configures a [`ReplicaSet`](/docs/concepts/workloads/controllers/replicaset/) is now the recommended way to set up replication.
-->
{{< note >}}
現在推薦使用配置 [`ReplicaSet`](/zh-cn/docs/concepts/workloads/controllers/replicaset/) 的
[`Deployment`](/zh-cn/docs/concepts/workloads/controllers/deployment/) 來建立副本管理機制。
{{< /note >}}

<!--
A _ReplicationController_ ensures that a specified number of pod replicas are running at any one
time. In other words, a ReplicationController makes sure that a pod or a homogeneous set of pods is
always up and available.
-->
_ReplicationController_ 確保在任何時候都有特定數量的 Pod 副本處於執行狀態。
換句話說，ReplicationController 確保一個 Pod 或一組同類的 Pod 總是可用的。

<!-- body -->

<!--
## How a ReplicationController Works

If there are too many pods, the ReplicationController terminates the extra pods. If there are too few, the
ReplicationController starts more pods. Unlike manually created pods, the pods maintained by a
ReplicationController are automatically replaced if they fail, are deleted, or are terminated.
For example, your pods are re-created on a node after disruptive maintenance such as a kernel upgrade.
For this reason, you should use a ReplicationController even if your application requires
only a single pod. A ReplicationController is similar to a process supervisor,
but instead of supervising individual processes on a single node, the ReplicationController supervises multiple pods
across multiple nodes.
-->
## ReplicationController 如何工作

當 Pod 數量過多時，ReplicationController 會終止多餘的 Pod。當 Pod 數量太少時，ReplicationController 將會啟動新的 Pod。
與手動建立的 Pod 不同，由 ReplicationController 建立的 Pod 在失敗、被刪除或被終止時會被自動替換。
例如，在中斷性維護（如核心升級）之後，你的 Pod 會在節點上重新建立。
因此，即使你的應用程式只需要一個 Pod，你也應該使用 ReplicationController 建立 Pod。
ReplicationController 類似於程序管理器，但是 ReplicationController 不是監控單個節點上的單個程序，而是監控跨多個節點的多個 Pod。

<!--
ReplicationController is often abbreviated to "rc" in discussion, and as a shortcut in
kubectl commands.

A simple case is to create one ReplicationController object to reliably run one instance of
a Pod indefinitely.  A more complex use case is to run several identical replicas of a replicated
service, such as web servers.
-->
在討論中，ReplicationController 通常縮寫為 "rc"，並作為 kubectl 命令的快捷方式。

一個簡單的示例是建立一個 ReplicationController 物件來可靠地無限期地執行 Pod 的一個例項。
更復雜的用例是執行一個多副本服務（如 web 伺服器）的若干相同副本。

<!--
## Running an example ReplicationController

This example ReplicationController config runs three copies of the nginx web server.
-->
## 執行一個示例 ReplicationController

這個示例 ReplicationController 配置執行 nginx Web 伺服器的三個副本。

{{< codenew file="controllers/replication.yaml" >}}

<!--
Run the example job by downloading the example file and then running this command:
-->
透過下載示例檔案並執行以下命令來執行示例任務:

```shell
kubectl apply -f https://k8s.io/examples/controllers/replication.yaml
```

<!--
The output is similar to this:
-->
輸出類似於：

```
replicationcontroller/nginx created
```

<!--
Check on the status of the ReplicationController using this command:
-->
使用以下命令檢查 ReplicationController 的狀態:

```shell
kubectl describe replicationcontrollers/nginx
```

<!--
The output is similar to this:
-->
輸出類似於：

```
Name:        nginx
Namespace:   default
Selector:    app=nginx
Labels:      app=nginx
Annotations:    <none>
Replicas:    3 current / 3 desired
Pods Status: 0 Running / 3 Waiting / 0 Succeeded / 0 Failed
Pod Template:
  Labels:       app=nginx
  Containers:
   nginx:
    Image:              nginx
    Port:               80/TCP
    Environment:        <none>
    Mounts:             <none>
  Volumes:              <none>
Events:
  FirstSeen       LastSeen     Count    From                        SubobjectPath    Type      Reason              Message
  ---------       --------     -----    ----                        -------------    ----      ------              -------
  20s             20s          1        {replication-controller }                    Normal    SuccessfulCreate    Created pod: nginx-qrm3m
  20s             20s          1        {replication-controller }                    Normal    SuccessfulCreate    Created pod: nginx-3ntk0
  20s             20s          1        {replication-controller }                    Normal    SuccessfulCreate    Created pod: nginx-4ok8v
```

<!--
Here, three pods are created, but none is running yet, perhaps because the image is being pulled.
A little later, the same command may show:
-->
在這裡，建立了三個 Pod，但沒有一個 Pod 正在執行，這可能是因為正在拉取映象。
稍後，相同的命令可能會顯示：

```
Pods Status:    3 Running / 0 Waiting / 0 Succeeded / 0 Failed
```

<!--
To list all the pods that belong to the ReplicationController in a machine readable form, you can use a command like this:
-->
要以機器可讀的形式列出屬於 ReplicationController 的所有 Pod，可以使用如下命令：

```shell
pods=$(kubectl get pods --selector=app=nginx --output=jsonpath={.items..metadata.name})
echo $pods
```

<!--
The output is similar to this:
-->
輸出類似於：

```
nginx-3ntk0 nginx-4ok8v nginx-qrm3m
```

<!--
Here, the selector is the same as the selector for the ReplicationController (seen in the
`kubectl describe` output), and in a different form in `replication.yaml`.  The `--output=jsonpath` option
specifies an expression with the name from each pod in the returned list.
-->
這裡，選擇算符與 ReplicationController 的選擇算符相同（參見 `kubectl describe` 輸出），並以不同的形式出現在 `replication.yaml` 中。
`--output=jsonpath` 選項指定了一個表示式，僅從返回列表中的每個 Pod 中獲取名稱。

<!--
## Writing a ReplicationController Spec

As with all other Kubernetes config, a ReplicationController needs `apiVersion`, `kind`, and `metadata` fields.
For general information about working with configuration files, see [object management](/docs/concepts/overview/working-with-objects/object-management/).

A ReplicationController also needs a [`.spec` section](https://git.k8s.io/community/contributors/devel/sig-architecture/api-conventions.md#spec-and-status).
-->
## 編寫一個 ReplicationController 規約

與所有其它 Kubernetes 配置一樣，ReplicationController 需要 `apiVersion`、
`kind` 和 `metadata` 欄位。
有關使用配置檔案的常規資訊，參考
[物件管理](/zh-cn/docs/concepts/overview/working-with-objects/object-management/)。

ReplicationController 也需要一個 [`.spec` 部分](https://git.k8s.io/community/contributors/devel/sig-architecture/api-conventions.md#spec-and-status)。

<!--
### Pod Template

The `.spec.template` is the only required field of the `.spec`.

The `.spec.template` is a [pod template](/docs/concepts/workloads/pods/pod-overview/#pod-templates). It has exactly the same schema as a [pod](/docs/concepts/workloads/pods/pod/), except it is nested and does not have an `apiVersion` or `kind`.
-->
### Pod 模板  {#pod-template}

`.spec.template` 是 `.spec` 的唯一必需欄位。

`.spec.template` 是一個 [Pod 模板](/zh-cn/docs/concepts/workloads/pods/#pod-templates)。
它的模式與 [Pod](/zh-cn/docs/concepts/workloads/pods/) 完全相同，只是它是巢狀的，沒有 `apiVersion` 或 `kind` 屬性。

<!--
In addition to required fields for a Pod, a pod template in a ReplicationController must specify appropriate
labels and an appropriate restart policy. For labels, make sure not to overlap with other controllers. See [pod selector](#pod-selector).

Only a [`.spec.template.spec.restartPolicy`](/docs/concepts/workloads/pods/pod-lifecycle/#restart-policy) equal to `Always` is allowed, which is the default if not specified.

For local container restarts, ReplicationControllers delegate to an agent on the node,
for example the [Kubelet](/docs/admin/kubelet/) or Docker.
-->
除了 Pod 所需的欄位外，ReplicationController 中的 Pod 模板必須指定適當的標籤和適當的重新啟動策略。
對於標籤，請確保不與其他控制器重疊。參考 [Pod 選擇算符](#pod-selector)。

只允許 [`.spec.template.spec.restartPolicy`](/zh-cn/docs/concepts/workloads/pods/pod-lifecycle/#restart-policy) 等於 `Always`，如果沒有指定，這是預設值。

對於本地容器重啟，ReplicationController 委託給節點上的代理，
例如 [Kubelet](/zh-cn/docs/reference/command-line-tools-reference/kubelet/) 或 Docker。

<!--
### Labels on the ReplicationController

The ReplicationController can itself have labels (`.metadata.labels`).  Typically, you
would set these the same as the `.spec.template.metadata.labels`; if `.metadata.labels` is not specified
then it defaults to  `.spec.template.metadata.labels`. However, they are allowed to be
different, and the `.metadata.labels` do not affect the behavior of the ReplicationController.
-->
### ReplicationController 上的標籤

ReplicationController 本身可以有標籤 （`.metadata.labels`）。
通常，你可以將這些設定為 `.spec.template.metadata.labels`； 
如果沒有指定 `.metadata.labels` 那麼它預設為 `.spec.template.metadata.labels`。  
但是，Kubernetes 允許它們是不同的，`.metadata.labels` 不會影響 ReplicationController 的行為。

<!--
### Pod Selector

The `.spec.selector` field is a [label selector](/docs/concepts/overview/working-with-objects/labels/#label-selectors). A ReplicationController
manages all the pods with labels that match the selector. It does not distinguish
between pods that it created or deleted and pods that another person or process created or
deleted. This allows the ReplicationController to be replaced without affecting the running pods.
-->
### Pod 選擇算符 {#pod-selector}

`.spec.selector` 欄位是一個[標籤選擇算符](/zh-cn/docs/concepts/overview/working-with-objects/labels/#label-selectors)。 
ReplicationController 管理標籤與選擇算符匹配的所有 Pod。
它不區分它建立或刪除的 Pod 和其他人或程序建立或刪除的 Pod。
這允許在不影響正在執行的 Pod 的情況下替換 ReplicationController。

<!--
If specified, the `.spec.template.metadata.labels` must be equal to the `.spec.selector`, or it will
be rejected by the API.  If `.spec.selector` is unspecified, it will be defaulted to
`.spec.template.metadata.labels`.
-->
如果指定了 `.spec.template.metadata.labels`，它必須和 `.spec.selector` 相同，否則它將被 API 拒絕。
如果沒有指定 `.spec.selector`，它將預設為 `.spec.template.metadata.labels`。

<!--
Also you should not normally create any pods whose labels match this selector, either directly, with
another ReplicationController, or with another controller such as Job. If you do so, the
ReplicationController thinks that it created the other pods.  Kubernetes does not stop you
from doing this.

If you do end up with multiple controllers that have overlapping selectors, you
will have to manage the deletion yourself (see [below](#working-with-replicationcontrollers)).
-->
另外，通常不應直接使用另一個 ReplicationController 或另一個控制器（例如 Job）
來建立其標籤與該選擇算符匹配的任何 Pod。如果這樣做，ReplicationController 會認為它建立了這些 Pod。
Kubernetes 並沒有阻止你這樣做。

如果你的確建立了多個控制器並且其選擇算符之間存在重疊，那麼你將不得不自己管理刪除操作（參考[後文](#working-with-replicationcontrollers)）。

<!--
### Multiple Replicas

You can specify how many pods should run concurrently by setting `.spec.replicas` to the number
of pods you would like to have running concurrently.  The number running at any time may be higher
or lower, such as if the replicas were just increased or decreased, or if a pod is gracefully
shutdown, and a replacement starts early.

If you do not specify `.spec.replicas`, then it defaults to 1.
-->
### 多個副本

你可以透過設定 `.spec.replicas` 來指定應該同時執行多少個 Pod。
在任何時候，處於執行狀態的 Pod 個數都可能高於或者低於設定值。例如，副本個數剛剛被增加或減少時，或者一個 Pod 處於優雅終止過程中而其替代副本已經提前開始建立時。

如果你沒有指定 `.spec.replicas` ，那麼它預設是 1。

<!--
## Working with ReplicationControllers

### Deleting a ReplicationController and its Pods

To delete a ReplicationController and all its pods, use [`kubectl
delete`](/docs/reference/generated/kubectl/kubectl-commands#delete).  Kubectl will scale the ReplicationController to zero and wait
for it to delete each pod before deleting the ReplicationController itself.  If this kubectl
command is interrupted, it can be restarted.

When using the REST API or [client library](/docs/reference/using-api/client-libraries), you need to do the steps explicitly (scale replicas to
0, wait for pod deletions, then delete the ReplicationController).
-->
## 使用 ReplicationController {#working-with-replicationcontrollers}

### 刪除一個 ReplicationController 以及它的 Pod

要刪除一個 ReplicationController 以及它的 Pod，使用
[`kubectl delete`](/docs/reference/generated/kubectl/kubectl-commands#delete)。
kubectl 將 ReplicationController 縮放為 0 並等待以便在刪除 ReplicationController 本身之前刪除每個 Pod。
如果這個 kubectl 命令被中斷，可以重新啟動它。

當使用 REST API 或[客戶端庫](/zh-cn/docs/reference/using-api/client-libraries)時，你需要明確地執行這些步驟（縮放副本為 0、
等待 Pod 刪除，之後刪除 ReplicationController 資源）。

<!--
### Deleting only a ReplicationController

You can delete a ReplicationController without affecting any of its pods.

Using kubectl, specify the `--cascade=orphan` option to [`kubectl delete`](/docs/reference/generated/kubectl/kubectl-commands#delete).

When using the REST API or [client library](/docs/reference/using-api/client-libraries), you can delete the ReplicationController object.
-->
### 只刪除 ReplicationController

你可以刪除一個 ReplicationController 而不影響它的任何 Pod。

使用 kubectl，為 [`kubectl delete`](/docs/reference/generated/kubectl/kubectl-commands#delete) 指定 `--cascade=orphan` 選項。

當使用 REST API 或客戶端庫(/zh-cn/docs/reference/using-api/client-libraries)時，只需刪除 ReplicationController 物件。

<!--
Once the original is deleted, you can create a new ReplicationController to replace it.  As long
as the old and new `.spec.selector` are the same, then the new one will adopt the old pods.
However, it will not make any effort to make existing pods match a new, different pod template.
To update pods to a new spec in a controlled way, use a [rolling update](#rolling-updates).
-->
一旦原始物件被刪除，你可以建立一個新的 ReplicationController 來替換它。
只要新的和舊的 `.spec.selector` 相同，那麼新的控制器將領養舊的 Pod。
但是，它不會做出任何努力使現有的 Pod 匹配新的、不同的 Pod 模板。
如果希望以受控方式更新 Pod 以使用新的 spec，請執行[滾動更新](#rolling-updates)操作。

<!--
### Isolating pods from a ReplicationController

Pods may be removed from a ReplicationController's target set by changing their labels. This technique may be used to remove pods from service for debugging and data recovery. Pods that are removed in this way will be replaced automatically (assuming that the number of replicas is not also changed).
-->
### 從 ReplicationController 中隔離 Pod

透過更改 Pod 的標籤，可以從 ReplicationController 的目標中刪除 Pod。
此技術可用於從服務中刪除 Pod 以進行除錯、資料恢復等。以這種方式刪除的 Pod
將被自動替換（假設複製副本的數量也沒有更改）。

<!--
## Common usage patterns
-->
## 常見的使用模式

<!--
### Rescheduling

As mentioned above, whether you have 1 pod you want to keep running, or 1000, a ReplicationController will ensure that the specified number of pods exists, even in the event of node failure or pod termination (for example, due to an action by another control agent).
-->
### 重新排程   {#rescheduling}

如上所述，無論你想要繼續執行 1 個 Pod 還是 1000 個 Pod，一個 ReplicationController 都將確保存在指定數量的 Pod，即使在節點故障或 Pod 終止(例如，由於另一個控制代理的操作)的情況下也是如此。
<!--
### Scaling

The ReplicationController enables scaling the number of replicas up or down, either manually or by an auto-scaling control agent, by updating the `replicas` field.
-->
### 擴縮容   {#scaling}

透過設定 `replicas` 欄位，ReplicationController 可以允許擴容或縮容副本的數量。
你可以手動或透過自動縮放控制代理來控制 ReplicationController 執行此操作。

<!--
### Rolling updates

The ReplicationController is designed to facilitate rolling updates to a service by replacing pods one-by-one.

As explained in [#1353](http://issue.k8s.io/1353), the recommended approach is to create a new ReplicationController with 1 replica, scale the new (+1) and old (-1) controllers one by one, and then delete the old controller after it reaches 0 replicas. This predictably updates the set of pods regardless of unexpected failures.
-->
### 滾動更新 {#rolling-updates}

ReplicationController 的設計目的是透過逐個替換 Pod 以方便滾動更新服務。

如 [#1353](https://issue.k8s.io/1353) PR 中所述，建議的方法是使用 1 個副本建立一個新的 ReplicationController，
逐個擴容新的（+1）和縮容舊的（-1）控制器，然後在舊的控制器達到 0 個副本後將其刪除。
這一方法能夠實現可控的 Pod 集合更新，即使存在意外失效的狀況。

<!--
Ideally, the rolling update controller would take application readiness into account, and would ensure that a sufficient number of pods were productively serving at any given time.

The two ReplicationControllers would need to create pods with at least one differentiating label, such as the image tag of the primary container of the pod, since it is typically image updates that motivate rolling updates.

Rolling update is implemented in the client tool
[`kubectl rolling-update`](/docs/reference/generated/kubectl/kubectl-commands#rolling-update). Visit [`kubectl rolling-update` task](/docs/tasks/run-application/rolling-update-replication-controller/) for more concrete examples.
-->
理想情況下，滾動更新控制器將考慮應用程式的就緒情況，並確保在任何給定時間都有足夠數量的 Pod 有效地提供服務。

這兩個 ReplicationController 將需要建立至少具有一個不同標籤的 Pod，比如 Pod 主要容器的映象標籤，因為通常是映象更新觸發滾動更新。

滾動更新是在客戶端工具 [`kubectl rolling-update`](/docs/reference/generated/kubectl/kubectl-commands#rolling-update)
中實現的。訪問 [`kubectl rolling-update` 任務](/zh-cn/docs/tasks/run-application/rolling-update-replication-controller/)以獲得更多的具體示例。

<!--
### Multiple release tracks

In addition to running multiple releases of an application while a rolling update is in progress, it's common to run multiple releases for an extended period of time, or even continuously, using multiple release tracks. The tracks would be differentiated by labels.

For instance, a service might target all pods with `tier in (frontend), environment in (prod)`.  Now say you have 10 replicated pods that make up this tier.  But you want to be able to 'canary' a new version of this component.  You could set up a ReplicationController with `replicas` set to 9 for the bulk of the replicas, with labels `tier=frontend, environment=prod, track=stable`, and another ReplicationController with `replicas` set to 1 for the canary, with labels `tier=frontend, environment=prod, track=canary`.  Now the service is covering both the canary and non-canary pods.  But you can mess with the ReplicationControllers separately to test things out, monitor the results, etc.
-->
### 多個版本跟蹤

除了在滾動更新過程中執行應用程式的多個版本之外，通常還會使用多個版本跟蹤來長時間，
甚至持續執行多個版本。這些跟蹤將根據標籤加以區分。

例如，一個服務可能把具有 `tier in (frontend), environment in (prod)` 的所有 Pod 作為目標。
現在假設你有 10 個副本的 Pod 組成了這個層。但是你希望能夠 `canary` （`金絲雀`）釋出這個元件的新版本。
你可以為大部分副本設定一個 ReplicationController，其中 `replicas` 設定為 9，
標籤為 `tier=frontend, environment=prod, track=stable` 而為 `canary`
設定另一個 ReplicationController，其中 `replicas` 設定為 1，
標籤為 `tier=frontend, environment=prod, track=canary`。
現在這個服務覆蓋了 `canary` 和非 `canary` Pod。但你可以單獨處理
ReplicationController，以測試、監控結果等。

<!--
### Using ReplicationControllers with Services

Multiple ReplicationControllers can sit behind a single service, so that, for example, some traffic
goes to the old version, and some goes to the new version.

A ReplicationController will never terminate on its own, but it isn't expected to be as long-lived as services. Services may be composed of pods controlled by multiple ReplicationControllers, and it is expected that many ReplicationControllers may be created and destroyed over the lifetime of a service (for instance, to perform an update of pods that run the service). Both services themselves and their clients should remain oblivious to the ReplicationControllers that maintain the pods of the services.
-->
### 和服務一起使用 ReplicationController

多個 ReplicationController 可以位於一個服務的後面，例如，一部分流量流向舊版本，
一部分流量流向新版本。

一個 ReplicationController 永遠不會自行終止，但它不會像服務那樣長時間存活。
服務可以由多個 ReplicationController 控制的 Pod 組成，並且在服務的生命週期內
（例如，為了執行 Pod 更新而執行服務），可以建立和銷燬許多 ReplicationController。
服務本身和它們的客戶端都應該忽略負責維護服務 Pod 的 ReplicationController 的存在。

<!--
## Writing programs for Replication

Pods created by a ReplicationController are intended to be fungible and semantically identical, though their configurations may become heterogeneous over time. This is an obvious fit for replicated stateless servers, but ReplicationControllers can also be used to maintain availability of master-elected, sharded, and worker-pool applications. Such applications should use dynamic work assignment mechanisms, such as the [RabbitMQ work queues](https://www.rabbitmq.com/tutorials/tutorial-two-python.html), as opposed to static/one-time customization of the configuration of each pod, which is considered an anti-pattern. Any pod customization performed, such as vertical auto-sizing of resources (for example, cpu or memory), should be performed by another online controller process, not unlike the ReplicationController itself.
-->
## 編寫多副本的應用

由 ReplicationController 建立的 Pod 是可替換的，語義上是相同的，
儘管隨著時間的推移，它們的配置可能會變得異構。
這顯然適合於多副本的無狀態伺服器，但是 ReplicationController 也可以用於維護主選、
分片和工作池應用程式的可用性。
這樣的應用程式應該使用動態的工作分配機制，例如
[RabbitMQ 工作佇列](https://www.rabbitmq.com/tutorials/tutorial-two-python.html)，
而不是靜態的或者一次性定製每個 Pod 的配置，這被認為是一種反模式。
執行的任何 Pod 定製，例如資源的垂直自動調整大小（例如，CPU 或記憶體），
都應該由另一個線上控制器程序執行，這與 ReplicationController 本身沒什麼不同。

<!--
## Responsibilities of the ReplicationController

The ReplicationController ensures that the desired number of pods matches its label selector and are operational. Currently, only terminated pods are excluded from its count. In the future, [readiness](http://issue.k8s.io/620) and other information available from the system may be taken into account, we may add more controls over the replacement policy, and we plan to emit events that could be used by external clients to implement arbitrarily sophisticated replacement and/or scale-down policies.
-->
## ReplicationController 的職責

ReplicationController 僅確保所需的 Pod 數量與其標籤選擇算符匹配，並且是可操作的。
目前，它的計數中只排除終止的 Pod。
未來，可能會考慮系統提供的[就緒狀態](https://issue.k8s.io/620)和其他資訊，
我們可能會對替換策略新增更多控制，
我們計劃發出事件，這些事件可以被外部客戶端用來實現任意複雜的替換和/或縮減策略。

<!--
The ReplicationController is forever constrained to this narrow responsibility. It itself will not perform readiness nor liveness probes. Rather than performing auto-scaling, it is intended to be controlled by an external auto-scaler (as discussed in [#492](http://issue.k8s.io/492)), which would change its `replicas` field. We will not add scheduling policies (for example, [spreading](http://issue.k8s.io/367#issuecomment-48428019)) to the ReplicationController. Nor should it verify that the pods controlled match the currently specified template, as that would obstruct auto-sizing and other automated processes. Similarly, completion deadlines, ordering dependencies, configuration expansion, and other features belong elsewhere. We even plan to factor out the mechanism for bulk pod creation ([#170](http://issue.k8s.io/170)).
-->
ReplicationController 永遠被限制在這個狹隘的職責範圍內。
它本身既不執行就緒態探測，也不執行活躍性探測。
它不負責執行自動縮放，而是由外部自動縮放器控制（如
[#492](https://issue.k8s.io/492) 中所述），後者負責更改其 `replicas` 欄位值。
我們不會向 ReplicationController 新增排程策略(例如，
[spreading](https://issue.k8s.io/367#issuecomment-48428019))。
它也不應該驗證所控制的 Pod 是否與當前指定的模板匹配，因為這會阻礙自動調整大小和其他自動化過程。
類似地，完成期限、整理依賴關係、配置擴充套件和其他特性也屬於其他地方。
我們甚至計劃考慮批次建立 Pod 的機制（查閱 [#170](https://issue.k8s.io/170)）。

<!--
The ReplicationController is intended to be a composable building-block primitive. We expect higher-level APIs and/or tools to be built on top of it and other complementary primitives for user convenience in the future. The "macro" operations currently supported by kubectl (run, scale, rolling-update) are proof-of-concept examples of this. For instance, we could imagine something like [Asgard](https://netflixtechblog.com/asgard-web-based-cloud-management-and-deployment-2c9fc4e4d3a1) managing ReplicationControllers, auto-scalers, services, scheduling policies, canaries, etc.
-->
ReplicationController 旨在成為可組合的構建基元。
我們希望在它和其他補充原語的基礎上構建更高級別的 API 或者工具，以便於將來的使用者使用。
kubectl 目前支援的 "macro" 操作（執行、縮放、滾動更新）就是這方面的概念示例。
例如，我們可以想象類似於 [Asgard](https://netflixtechblog.com/asgard-web-based-cloud-management-and-deployment-2c9fc4e4d3a1)
的東西管理 ReplicationController、自動定標器、服務、排程策略、金絲雀釋出等。

<!--
## API Object

Replication controller is a top-level resource in the Kubernetes REST API. More details about the
API object can be found at:
[ReplicationController API object](/docs/reference/generated/kubernetes-api/{{< param "version" >}}/#replicationcontroller-v1-core).
-->
## API 物件

在 Kubernetes REST API 中 Replication controller 是頂級資源。
更多關於 API 物件的詳細資訊可以在
[ReplicationController API 物件](/docs/reference/generated/kubernetes-api/{{< param "version" >}}/#replicationcontroller-v1-core)找到。

<!--
## Alternatives to ReplicationController
### ReplicaSet

[`ReplicaSet`](/docs/concepts/workloads/controllers/replicaset/) is the next-generation ReplicationController that supports the new [set-based label selector](/docs/concepts/overview/working-with-objects/labels/#set-based-requirement).
It’s mainly used by [Deployment](/docs/concepts/workloads/controllers/deployment/) as a mechanism to orchestrate Pod creation, deletion and updates.
Note that we recommend using Deployments instead of directly using Replica Sets, unless you require custom update orchestration or don’t require updates at all.
-->
## ReplicationController 的替代方案

### ReplicaSet

[`ReplicaSet`](/zh-cn/docs/concepts/workloads/controllers/replicaset/) 是下一代 ReplicationController，
支援新的[基於集合的標籤選擇算符](/zh-cn/docs/concepts/overview/working-with-objects/labels/#set-based-requirement)。
它主要被 [`Deployment`](/zh-cn/docs/concepts/workloads/controllers/deployment/)
用來作為一種編排 Pod 建立、刪除及更新的機制。
請注意，我們推薦使用 Deployment 而不是直接使用 ReplicaSet，除非
你需要自定義更新編排或根本不需要更新。

<!--
### Deployment (Recommended)

[`Deployment`](/docs/concepts/workloads/controllers/deployment/) is a higher-level API object that updates its underlying Replica Sets and their Pods.
Deployments are recommended if you want the rolling update functionality,
because they are declarative, server-side, and have additional features.
-->
### Deployment （推薦）

[`Deployment`](/zh-cn/docs/concepts/workloads/controllers/deployment/) 是一種更高級別的 API 物件，用於更新其底層 ReplicaSet 及其 Pod。
如果你想要這種滾動更新功能，那麼推薦使用 Deployment，因為它們是宣告式的、服務端的，並且具有其它特性。

<!--
### Bare Pods

Unlike in the case where a user directly created pods, a ReplicationController replaces pods that are deleted or terminated for any reason, such as in the case of node failure or disruptive node maintenance, such as a kernel upgrade. For this reason, we recommend that you use a ReplicationController even if your application requires only a single pod. Think of it similarly to a process supervisor, only it supervises multiple pods across multiple nodes instead of individual processes on a single node.  A ReplicationController delegates local container restarts to some agent on the node (for example, Kubelet or Docker).
-->
### 裸 Pod

與使用者直接建立 Pod 的情況不同，ReplicationController 能夠替換因某些原因
被刪除或被終止的 Pod ，例如在節點故障或中斷節點維護的情況下，例如核心升級。
因此，我們建議你使用 ReplicationController，即使你的應用程式只需要一個 Pod。
可以將其看作類似於程序管理器，它只管理跨多個節點的多個 Pod ，而不是單個節點上的單個程序。
ReplicationController 將本地容器重啟委託給節點上的某個代理(例如，Kubelet 或 Docker)。

<!--
### Job

Use a [`Job`](/docs/concepts/jobs/run-to-completion-finite-workloads/) instead of a ReplicationController for Pods that are expected to terminate on their own
(that is, batch jobs).
-->
### Job

對於預期會自行終止的 Pod (即批處理任務)，使用
[`Job`](/zh-cn/docs/concepts/workloads/controllers/job/) 而不是 ReplicationController。

<!--
### DaemonSet

Use a [`DaemonSet`](/docs/concepts/workloads/controllers/daemonset/) instead of a ReplicationController for pods that provide a
machine-level function, such as machine monitoring or machine logging.  These pods have a lifetime that is tied
to a machine lifetime: the pod needs to be running on the machine before other pods start, and are
safe to terminate when the machine is otherwise ready to be rebooted/shutdown.
-->
### DaemonSet

對於提供機器級功能（例如機器監控或機器日誌記錄）的 Pod，
使用 [`DaemonSet`](/zh-cn/docs/concepts/workloads/controllers/daemonset/) 而不是
ReplicationController。
這些 Pod 的生命期與機器的生命期繫結：它們需要在其他 Pod 啟動之前在機器上執行，
並且在機器準備重新啟動或者關閉時安全地終止。

<!--
## {{% heading "whatsnext" %}}

* Learn about [Pods](/docs/concepts/workloads/pods).
* Learn about [Deployment](/docs/concepts/workloads/controllers/deployment/), the replacement
  for ReplicationController.
* `ReplicationController` is part of the Kubernetes REST API.
  Read the {{< api-reference page="workload-resources/replication-controller-v1" >}}
  object definition to understand the API for replication controllers.
-->
## {{% heading "whatsnext" %}}

- 瞭解 [Pods](/zh-cn/docs/concepts/workloads/pods)。
- 瞭解 [Depolyment](/zh-cn/docs/concepts/workloads/controllers/deployment/)，ReplicationController 的替代品。
- `ReplicationController` 是 Kubernetes REST API 的一部分，閱讀 {{< api-reference page="workload-resources/replication-controller-v1" >}}
  物件定義以瞭解 replication controllers 的 API。

