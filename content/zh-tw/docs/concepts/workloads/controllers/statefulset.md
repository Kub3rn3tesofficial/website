---
title: StatefulSets
content_type: concept
weight: 30
---

<!--
title: StatefulSets
content_type: concept
weight: 40
-->

<!-- overview -->

<!--
StatefulSet is the workload API object used to manage stateful applications.
-->
StatefulSet 是用來管理有狀態應用的工作負載 API 物件。

{{< glossary_definition term_id="statefulset" length="all" >}}

<!-- body -->

<!--
## Using StatefulSets

StatefulSets are valuable for applications that require one or more of the
following.
-->
## 使用 StatefulSets

StatefulSets 對於需要滿足以下一個或多個需求的應用程式很有價值：

<!--
* Stable, unique network identifiers.
* Stable, persistent storage.
* Ordered, graceful deployment and scaling.
* Ordered, automated rolling updates.
-->
* 穩定的、唯一的網路識別符號。
* 穩定的、持久的儲存。
* 有序的、優雅的部署和縮放。
* 有序的、自動的滾動更新。

<!--
In the above, stable is synonymous with persistence across Pod (re)scheduling.
If an application doesn't require any stable identifiers or ordered deployment,
deletion, or scaling, you should deploy your application using a workload object
that provides a set of stateless replicas.
[Deployment](/docs/concepts/workloads/controllers/deployment/) or
[ReplicaSet](/docs/concepts/workloads/controllers/replicaset/) may be better suited to your stateless needs.
-->
在上面描述中，“穩定的”意味著 Pod 排程或重排程的整個過程是有永續性的。
如果應用程式不需要任何穩定的識別符號或有序的部署、刪除或伸縮，則應該使用
由一組無狀態的副本控制器提供的工作負載來部署應用程式，比如
[Deployment](/zh-cn/docs/concepts/workloads/controllers/deployment/) 或者
[ReplicaSet](/zh-cn/docs/concepts/workloads/controllers/replicaset/)
可能更適用於你的無狀態應用部署需要。

<!--
## Limitations
-->
## 限制  {#limitations}

<!--
* The storage for a given Pod must either be provisioned by a [PersistentVolume Provisioner](https://github.com/kubernetes/examples/tree/master/staging/persistent-volume-provisioning/README.md) based on the requested `storage class`, or pre-provisioned by an admin.
* Deleting and/or scaling a StatefulSet down will *not* delete the volumes associated with the StatefulSet. This is done to ensure data safety, which is generally more valuable than an automatic purge of all related StatefulSet resources.
* StatefulSets currently require a [Headless Service](/docs/concepts/services-networking/service/#headless-services) to be responsible for the network identity of the Pods. You are responsible for creating this Service.
* StatefulSets do not provide any guarantees on the termination of pods when a StatefulSet is deleted. To achieve ordered and graceful termination of the pods in the StatefulSet, it is possible to scale the StatefulSet down to 0 prior to deletion.
* When using [Rolling Updates](#rolling-updates) with the default
  [Pod Management Policy](#pod-management-policies) (`OrderedReady`),
  it's possible to get into a broken state that requires
  [manual intervention to repair](#forced-rollback).
-->
* 給定 Pod 的儲存必須由
  [PersistentVolume 驅動](https://github.com/kubernetes/examples/tree/master/staging/persistent-volume-provisioning/README.md)
  基於所請求的 `storage class` 來提供，或者由管理員預先提供。
* 刪除或者收縮 StatefulSet 並*不會*刪除它關聯的儲存卷。
  這樣做是為了保證資料安全，它通常比自動清除 StatefulSet 所有相關的資源更有價值。
* StatefulSet 當前需要[無頭服務](/zh-cn/docs/concepts/services-networking/service/#headless-services)
  來負責 Pod 的網路標識。你需要負責建立此服務。
* 當刪除 StatefulSets 時，StatefulSet 不提供任何終止 Pod 的保證。
  為了實現 StatefulSet 中的 Pod 可以有序地且體面地終止，可以在刪除之前將 StatefulSet
  縮放為 0。
* 在預設 [Pod 管理策略](#pod-management-policies)(`OrderedReady`) 時使用
  [滾動更新](#rolling-updates)，可能進入需要[人工干預](#forced-rollback)
  才能修復的損壞狀態。

<!--
## Components
The example below demonstrates the components of a StatefulSet.
-->
## 元件  {#components}

下面的示例演示了 StatefulSet 的元件。

```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx
  labels:
    app: nginx
spec:
  ports:
  - port: 80
    name: web
  clusterIP: None
  selector:
    app: nginx
---
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: web
spec:
  selector:
    matchLabels:
      app: nginx # 必須匹配 .spec.template.metadata.labels
  serviceName: "nginx"
  replicas: 3 # 預設值是 1
  minReadySeconds: 10 # 預設值是 0
  template:
    metadata:
      labels:
        app: nginx # 必須匹配 .spec.selector.matchLabels
    spec:
      terminationGracePeriodSeconds: 10
      containers:
      - name: nginx
        image: k8s.gcr.io/nginx-slim:0.8
        ports:
        - containerPort: 80
          name: web
        volumeMounts:
        - name: www
          mountPath: /usr/share/nginx/html
  volumeClaimTemplates:
  - metadata:
      name: www
    spec:
      accessModes: [ "ReadWriteOnce" ]
      storageClassName: "my-storage-class"
      resources:
        requests:
          storage: 1Gi
```

<!--
In the above example:

* A Headless Service, named `nginx`, is used to control the network domain.
* The StatefulSet, named `web`, has a Spec that indicates that 3 replicas of the nginx container will be launched in unique Pods.
* The `volumeClaimTemplates` will provide stable storage using [PersistentVolumes](/docs/concepts/storage/persistent-volumes/) provisioned by a PersistentVolume Provisioner.

The name of a StatefulSet object must be a valid
[DNS subdomain name](/docs/concepts/overview/working-with-objects/names#dns-subdomain-names).

-->
上述例子中：

* 名為 `nginx` 的 Headless Service 用來控制網路域名。
* 名為 `web` 的 StatefulSet 有一個 Spec，它表明將在獨立的 3 個 Pod 副本中啟動 nginx 容器。
* `volumeClaimTemplates` 將透過 PersistentVolumes 驅動提供的
  [PersistentVolumes](/zh-cn/docs/concepts/storage/persistent-volumes/) 來提供穩定的儲存。

StatefulSet 的命名需要遵循[DNS 子域名](/zh-cn/docs/concepts/overview/working-with-objects/names#dns-subdomain-names)規範。

<!--
### Pod Selector
-->
### Pod 選擇算符     {#pod-selector}

<!--
You must set the `.spec.selector` field of a StatefulSet to match the labels of its `.spec.template.metadata.labels`. Failing to specify a matching Pod Selector will result in a validation error during StatefulSet creation.
-->
你必須設定 StatefulSet 的 `.spec.selector` 欄位，使之匹配其在
`.spec.template.metadata.labels` 中設定的標籤。
未指定匹配的 Pod 選擇器將在建立 StatefulSet 期間導致驗證錯誤。

<!-- 
### Volume Claim Templates

You can set the  `.spec.volumeClaimTemplates` which can provide stable storage using [PersistentVolumes](/docs/concepts/storage/persistent-volumes/) provisioned by a PersistentVolume Provisioner.
-->
### 卷申領模版  {#volume-claim-templates}

你可以設定 `.spec.volumeClaimTemplates`，
它可以使用 PersistentVolume 製備程式所準備的
[PersistentVolumes](/zh-cn/docs/concepts/storage/persistent-volumes/) 來提供穩定的儲存。

<!-- ### Minimum ready seconds -->
### 最短就緒秒數 {#minimum-ready-seconds}

{{< feature-state for_k8s_version="v1.23" state="beta" >}}

<!-- 
`.spec.minReadySeconds` is an optional field that specifies the minimum number of seconds for which a newly
created Pod should be ready without any of its containers crashing, for it to be considered available.
Please note that this feature is beta and enabled by default. Please opt out by unsetting the StatefulSetMinReadySeconds flag, if you don't
want this feature to be enabled. This field defaults to 0 (the Pod will be considered 
available as soon as it is ready). To learn more about when a Pod is considered ready, see [Container Probes](/docs/concepts/workloads/pods/pod-lifecycle/#container-probes).
-->
`.spec.minReadySeconds` 是一個可選欄位，
它指定新建立的 Pod 應該準備好且其任何容器不崩潰的最小秒數，以使其被視為可用。
請注意，此功能是測試版，預設啟用。如果你不希望啟用此功能，
請透過取消設定 StatefulSetMinReadySeconds 標誌來選擇退出。
該欄位預設為 0（Pod 準備就緒後將被視為可用）。
要了解有關何時認為 Pod 準備就緒的更多資訊，
請參閱[容器探針](/zh-cn/docs/concepts/workloads/pods/pod-lifecycle/#container-probes)。

<!--
## Pod Identity

StatefulSet Pods have a unique identity that is comprised of an ordinal, a
stable network identity, and stable storage. The identity sticks to the Pod,
regardless of which node it's (re)scheduled on.
-->
## Pod 標識   {#pod-identity}

StatefulSet Pod 具有唯一的標識，該標識包括順序標識、穩定的網路標識和穩定的儲存。
該標識和 Pod 是繫結的，不管它被排程在哪個節點上。

<!--
### Ordinal Index

For a StatefulSet with N replicas, each Pod in the StatefulSet will be
assigned an integer ordinal, from 0 up through N-1, that is unique over the Set.
-->
### 有序索引   {#ordinal-index}

對於具有 N 個副本的 StatefulSet，StatefulSet 中的每個 Pod 將被分配一個整數序號，
從 0 到 N-1，該序號在 StatefulSet 上是唯一的。

<!--
### Stable Network ID

Each Pod in a StatefulSet derives its hostname from the name of the StatefulSet
and the ordinal of the Pod. The pattern for the constructed hostname
is `$(statefulset name)-$(ordinal)`. The example above will create three Pods
named `web-0,web-1,web-2`.
A StatefulSet can use a [Headless Service](/docs/concepts/services-networking/service/#headless-services)
to control the domain of its Pods. The domain managed by this Service takes the form:
`$(service name).$(namespace).svc.cluster.local`, where "cluster.local" is the
cluster domain.
As each Pod is created, it gets a matching DNS subdomain, taking the form:
`$(podname).$(governing service domain)`, where the governing service is defined
by the `serviceName` field on the StatefulSet.
-->
### 穩定的網路 ID   {#stable-network-id}

StatefulSet 中的每個 Pod 根據 StatefulSet 的名稱和 Pod 的序號派生出它的主機名。
組合主機名的格式為`$(StatefulSet 名稱)-$(序號)`。
上例將會建立三個名稱分別為 `web-0、web-1、web-2` 的 Pod。
StatefulSet 可以使用 [無頭服務](/zh-cn/docs/concepts/services-networking/service/#headless-services)
控制它的 Pod 的網路域。管理域的這個服務的格式為：
`$(服務名稱).$(名稱空間).svc.cluster.local`，其中 `cluster.local` 是叢集域。
一旦每個 Pod 建立成功，就會得到一個匹配的 DNS 子域，格式為：
`$(pod 名稱).$(所屬服務的 DNS 域名)`，其中所屬服務由 StatefulSet 的 `serviceName` 域來設定。

<!--
Depending on how DNS is configured in your cluster, you may not be able to look up the DNS
name for a newly-run Pod immediately. This behavior can occur when other clients in the
cluster have already sent queries for the hostname of the Pod before it was created.
Negative caching (normal in DNS) means that the results of previous failed lookups are
remembered and reused, even after the Pod is running, for at least a few seconds.

If you need to discover Pods promptly after they are created, you have a few options:

- Query the Kubernetes API directly (for example, using a watch) rather than relying on DNS lookups.
- Decrease the time of caching in your Kubernetes DNS provider (typically this means editing the config map for CoreDNS, which currently caches for 30 seconds).


As mentioned in the [limitations](#limitations) section, you are responsible for
creating the [Headless Service](/docs/concepts/services-networking/service/#headless-services)
responsible for the network identity of the pods.

-->
取決於叢集域內部 DNS 的配置，有可能無法查詢一個剛剛啟動的 Pod 的 DNS 命名。
當叢集內其他客戶端在 Pod 建立完成前發出 Pod 主機名查詢時，就會發生這種情況。
負快取 (在 DNS 中較為常見) 意味著之前失敗的查詢結果會被記錄和重用至少若干秒鐘，
即使 Pod 已經正常運行了也是如此。

如果需要在 Pod 被建立之後及時發現它們，有以下選項：

- 直接查詢 Kubernetes API（比如，利用 watch 機制）而不是依賴於 DNS 查詢
- 縮短 Kubernetes DNS 驅動的快取時長（通常這意味著修改 CoreDNS 的 ConfigMap，目前快取時長為 30 秒）

正如[限制](#limitations)中所述，你需要負責建立[無頭服務](/zh-cn/docs/concepts/services-networking/service/#headless-services)
以便為 Pod 提供網路標識。

<!--
Here are some examples of choices for Cluster Domain, Service name,
StatefulSet name, and how that affects the DNS names for the StatefulSet's Pods.


Cluster Domain | Service (ns/name) | StatefulSet (ns/name)  | StatefulSet Domain  | Pod DNS | Pod Hostname |
-------------- | ----------------- | ----------------- | -------------- | ------- | ------------ |
 cluster.local | default/nginx     | default/web       | nginx.default.svc.cluster.local | web-{0..N-1}.nginx.default.svc.cluster.local | web-{0..N-1} |
 cluster.local | foo/nginx         | foo/web           | nginx.foo.svc.cluster.local     | web-{0..N-1}.nginx.foo.svc.cluster.local     | web-{0..N-1} |
 kube.local    | foo/nginx         | foo/web           | nginx.foo.svc.kube.local        | web-{0..N-1}.nginx.foo.svc.kube.local        | web-{0..N-1} |

-->
下面給出一些選擇叢集域、服務名、StatefulSet 名、及其怎樣影響 StatefulSet 的 Pod 上的 DNS 名稱的示例：

叢集域名       | 服務（名字空間/名字）| StatefulSet（名字空間/名字） | StatefulSet 域名 | Pod DNS | Pod 主機名   |
-------------- | -------------------- | ---------------------------- | ---------------- | ------- | ------------ |
 cluster.local | default/nginx     | default/web       | nginx.default.svc.cluster.local | web-{0..N-1}.nginx.default.svc.cluster.local | web-{0..N-1} |
 cluster.local | foo/nginx         | foo/web           | nginx.foo.svc.cluster.local     | web-{0..N-1}.nginx.foo.svc.cluster.local     | web-{0..N-1} |
 kube.local    | foo/nginx         | foo/web           | nginx.foo.svc.kube.local        | web-{0..N-1}.nginx.foo.svc.kube.local        | web-{0..N-1} |

<!--
Cluster Domain will be set to `cluster.local` unless
[otherwise configured](/docs/concepts/services-networking/dns-pod-service/#how-it-works).
-->
{{< note >}}
叢集域會被設定為 `cluster.local`，除非有[其他配置](/zh-cn/docs/concepts/services-networking/dns-pod-service/)。
{{< /note >}}

<!--
### Stable Storage

For each VolumeClaimTemplate entry defined in a StatefulSet, each Pod receives one PersistentVolumeClaim. In the nginx example above, each Pod receives a single PersistentVolume with a StorageClass of `my-storage-class` and 1 Gib of provisioned storage. If no StorageClass
is specified, then the default StorageClass will be used. When a Pod is (re)scheduled
onto a node, its `volumeMounts` mount the PersistentVolumes associated with its
PersistentVolume Claims. Note that, the PersistentVolumes associated with the
Pods' PersistentVolume Claims are not deleted when the Pods, or StatefulSet are deleted.
This must be done manually.
-->
### 穩定的儲存  {#stable-storage}

對於 StatefulSet 中定義的每個 VolumeClaimTemplate，每個 Pod 接收到一個 PersistentVolumeClaim。在上面的 nginx 示例中，每個 Pod 將會得到基於 StorageClass `my-storage-class` 提供的
1 Gib 的 PersistentVolume。
如果沒有宣告 StorageClass，就會使用預設的 StorageClass。
當一個 Pod 被排程（重新排程）到節點上時，它的 `volumeMounts` 會掛載與其 
PersistentVolumeClaims 相關聯的 PersistentVolume。
請注意，當 Pod 或者 StatefulSet 被刪除時，與 PersistentVolumeClaims 相關聯的 
PersistentVolume 並不會被刪除。要刪除它必須透過手動方式來完成。

<!--
### Pod Name Label

When the StatefulSet {{< glossary_tooltip term_id="controller" >}} creates a Pod,
it adds a label, `statefulset.kubernetes.io/pod-name`, that is set to the name of
the Pod. This label allows you to attach a Service to a specific Pod in
the StatefulSet.
-->
### Pod 名稱標籤   {#pod-name-label}

當 StatefulSet {{< glossary_tooltip term_id="controller" >}} 建立 Pod 時，
它會新增一個標籤 `statefulset.kubernetes.io/pod-name`，該標籤值設定為 Pod 名稱。
這個標籤允許你給 StatefulSet 中的特定 Pod 繫結一個 Service。

<!--
## Deployment and Scaling Guarantees

* For a StatefulSet with N replicas, when Pods are being deployed, they are created sequentially, in order from {0..N-1}.
* When Pods are being deleted, they are terminated in reverse order, from {N-1..0}.
* Before a scaling operation is applied to a Pod, all of its predecessors must be Running and Ready.
* Before a Pod is terminated, all of its successors must be completely shutdown.
-->
## 部署和擴縮保證   {#deployment-and-scaling-guarantees}

* 對於包含 N 個 副本的 StatefulSet，當部署 Pod 時，它們是依次建立的，順序為 `0..N-1`。
* 當刪除 Pod 時，它們是逆序終止的，順序為 `N-1..0`。
* 在將縮放操作應用到 Pod 之前，它前面的所有 Pod 必須是 Running 和 Ready 狀態。
* 在 Pod 終止之前，所有的繼任者必須完全關閉。

<!--
The StatefulSet should not specify a `pod.Spec.TerminationGracePeriodSeconds` of 0. This practice is unsafe and strongly discouraged. For further explanation, please refer to [force deleting StatefulSet Pods](/docs/tasks/run-application/force-delete-stateful-set-pod/).
-->
StatefulSet 不應將 `pod.Spec.TerminationGracePeriodSeconds` 設定為 0。
這種做法是不安全的，要強烈阻止。更多的解釋請參考
[強制刪除 StatefulSet Pod](/zh-cn/docs/tasks/run-application/force-delete-stateful-set-pod/)。

<!--
When the nginx example above is created, three Pods will be deployed in the order
web-0, web-1, web-2. web-1 will not be deployed before web-0 is
[Running and Ready](/docs/user-guide/pod-states/), and web-2 will not be deployed until
web-1 is Running and Ready. If web-0 should fail, after web-1 is Running and Ready, but before
web-2 is launched, web-2 will not be launched until web-0 is successfully relaunched and
becomes Running and Ready.
-->
在上面的 nginx 示例被建立後，會按照 web-0、web-1、web-2 的順序部署三個 Pod。
在 web-0 進入 [Running 和 Ready](/zh-cn/docs/concepts/workloads/pods/pod-lifecycle/)
狀態前不會部署 web-1。在 web-1 進入 Running 和 Ready 狀態前不會部署 web-2。
如果 web-1 已經處於 Running 和 Ready 狀態，而 web-2 尚未部署，在此期間發生了
web-0 執行失敗，那麼 web-2 將不會被部署，要等到 web-0 部署完成並進入 Running 和
Ready 狀態後，才會部署 web-2。

<!--
If a user were to scale the deployed example by patching the StatefulSet such that
`replicas=1`, web-2 would be terminated first. web-1 would not be terminated until web-2
is fully shutdown and deleted. If web-0 were to fail after web-2 has been terminated and
is completely shutdown, but prior to web-1's termination, web-1 would not be terminated
until web-0 is Running and Ready.
-->
如果使用者想將示例中的 StatefulSet 收縮為 `replicas=1`，首先被終止的是 web-2。
在 web-2 沒有被完全停止和刪除前，web-1 不會被終止。
當 web-2 已被終止和刪除、web-1 尚未被終止，如果在此期間發生 web-0 執行失敗，
那麼就不會終止 web-1，必須等到 web-0 進入 Running 和 Ready 狀態後才會終止 web-1。

<!--
### Pod Management Policies

StatefulSet allows you to relax its ordering guarantees while
preserving its uniqueness and identity guarantees via its `.spec.podManagementPolicy` field.
-->
### Pod 管理策略 {#pod-management-policies}

StatefulSet 允許你放寬其排序保證，
同時透過它的 `.spec.podManagementPolicy` 域保持其唯一性和身份保證。

<!--
#### OrderedReady Pod Management

`OrderedReady` pod management is the default for StatefulSets. It implements the behavior
described [above](#deployment-and-scaling-guarantees).
-->
#### OrderedReady Pod 管理

`OrderedReady` Pod 管理是 StatefulSet 的預設設定。它實現了
[上面](#deployment-and-scaling-guarantees)描述的功能。

<!--
#### Parallel Pod Management

`Parallel` pod management tells the StatefulSet controller to launch or
terminate all Pods in parallel, and to not wait for Pods to become Running
and Ready or completely terminated prior to launching or terminating another
Pod. This option only affects the behavior for scaling operations. Updates are not affected.

-->
#### 並行 Pod 管理   {#parallel-pod-management}

`Parallel` Pod 管理讓 StatefulSet 控制器並行的啟動或終止所有的 Pod，
啟動或者終止其他 Pod 前，無需等待 Pod 進入 Running 和 ready 或者完全停止狀態。
這個選項只會影響伸縮操作的行為，更新則不會被影響。

<!--
## Update Strategies

A StatefulSet's `.spec.updateStrategy` field allows you to configure
and disable automated rolling updates for containers, labels, resource request/limits, and
annotations for the Pods in a StatefulSet. There are two possible values:
-->
## 更新策略  {#update-strategies}

StatefulSet 的 `.spec.updateStrategy` 欄位讓
你可以配置和禁用掉自動滾動更新 Pod 的容器、標籤、資源請求或限制、以及註解。
有兩個允許的值：
<!--
`OnDelete`
: When a StatefulSet's `.spec.updateStrategy.type` is set to `OnDelete`,
  the StatefulSet controller will not automatically update the Pods in a
  StatefulSet. Users must manually delete Pods to cause the controller to
  create new Pods that reflect modifications made to a StatefulSet's `.spec.template`.

`RollingUpdate`
: The `RollingUpdate` update strategy implements automated, rolling update for the Pods in a StatefulSet. This is the default update strategy.
-->
`OnDelete`
: 當 StatefulSet 的 `.spec.updateStrategy.type` 設定為 `OnDelete` 時，
  它的控制器將不會自動更新 StatefulSet 中的 Pod。
  使用者必須手動刪除 Pod 以便讓控制器建立新的 Pod，以此來對 StatefulSet 的
  `.spec.template` 的變動作出反應。

`RollingUpdate`
: `RollingUpdate` 更新策略對 StatefulSet 中的 Pod 執行自動的滾動更新。這是預設的更新策略。
 
<!--
## Rolling Updates

When a StatefulSet's `.spec.updateStrategy.type` is set to `RollingUpdate`, the
StatefulSet controller will delete and recreate each Pod in the StatefulSet. It will proceed
in the same order as Pod termination (from the largest ordinal to the smallest), updating
each Pod one at a time.

The Kubernetes control plane waits until an updated Pod is Running and Ready prior
to updating its predecessor. If you have set `.spec.minReadySeconds` (see
[Minimum Ready Seconds](#minimum-ready-seconds)), the control plane additionally waits that
amount of time after the Pod turns ready, before moving on.
-->
## 滾動更新 {#rolling-updates}

當 StatefulSet 的 `.spec.updateStrategy.type` 被設定為 `RollingUpdate` 時，
StatefulSet 控制器會刪除和重建 StatefulSet 中的每個 Pod。
它將按照與 Pod 終止相同的順序（從最大序號到最小序號）進行，每次更新一個 Pod。

Kubernetes 控制面會等到被更新的 Pod 進入 Running 和 Ready 狀態，然後再更新其前身。
如果你設定了 `.spec.minReadySeconds`（檢視[最短就緒秒數](#minimum-ready-seconds)），控制面在 Pod 就緒後會額外等待一定的時間再執行下一步。

<!--
### Partitioned rolling updates {#partitions}

The `RollingUpdate` update strategy can be partitioned, by specifying a
`.spec.updateStrategy.rollingUpdate.partition`. If a partition is specified, all Pods with an
ordinal that is greater than or equal to the partition will be updated when the StatefulSet's
`.spec.template` is updated. All Pods with an ordinal that is less than the partition will not
be updated, and, even if they are deleted, they will be recreated at the previous version. If a
StatefulSet's `.spec.updateStrategy.rollingUpdate.partition` is greater than its `.spec.replicas`,
updates to its `.spec.template` will not be propagated to its Pods.
In most cases you will not need to use a partition, but they are useful if you want to stage an
update, roll out a canary, or perform a phased roll out.
-->
### 分割槽滾動更新   {#partitions}

透過宣告 `.spec.updateStrategy.rollingUpdate.partition` 的方式，`RollingUpdate`
更新策略可以實現分割槽。
如果聲明瞭一個分割槽，當 StatefulSet 的 `.spec.template` 被更新時，
所有序號大於等於該分割槽序號的 Pod 都會被更新。
所有序號小於該分割槽序號的 Pod 都不會被更新，並且，即使他們被刪除也會依據之前的版本進行重建。
如果 StatefulSet 的 `.spec.updateStrategy.rollingUpdate.partition` 大於它的
`.spec.replicas`，對它的 `.spec.template` 的更新將不會傳遞到它的 Pod。
在大多數情況下，你不需要使用分割槽，但如果你希望進行階段更新、執行金絲雀或執行
分階段上線，則這些分割槽會非常有用。

<!--
### Maximum unavailable Pods
-->
### 最大不可用 Pod

{{< feature-state for_k8s_version="v1.24" state="alpha" >}}

<!--
You can control the maximum number of Pods that can be unavailable during an update
by specifying the `.spec.updateStrategy.rollingUpdate.maxUnavailable` field.
The value can be an absolute number (for example, `5`) or a percentage of desired
Pods (for example, `10%`). Absolute number is calculated from the percentage value
by rounding it up. This field cannot be 0. The default setting is 1.
-->
你可以透過指定 `.spec.updateStrategy.rollingUpdate.maxUnavailable` 
欄位來控制更新期間不可用的 Pod 的最大數量。
該值可以是絕對值（例如，“5”）或者是期望 Pod 個數的百分比（例如，`10%`）。
絕對值是根據百分比值四捨五入計算的。
該欄位不能為 0。預設設定為 1。

<!--
This field applies to all Pods in the range `0` to `replicas - 1`. If there is any
unavailable Pod in the range `0` to `replicas - 1`, it will be counted towards
`maxUnavailable`.
-->
該欄位適用於 `0` 到 `replicas - 1` 範圍內的所有 Pod。
如果在 `0` 到 `replicas - 1` 範圍內存在不可用 Pod，這類 Pod 將被計入 `maxUnavailable` 值。

<!--
{{< note >}}
The `maxUnavailable` field is in Alpha stage and it is honored only by API servers
that are running with the `MaxUnavailableStatefulSet`
[feature gate](/docs/reference/commmand-line-tools-reference/feature-gates/)
enabled.
{{< /note >}}
-->
{{< note >}}
`maxUnavailable` 欄位處於 Alpha 階段，僅當 API 伺服器啟用了 `MaxUnavailableStatefulSet`
[特性門控](/zh-cn/docs/reference/commmand-line-tools-reference/feature-gates/)時才起作用。
{{< /note >}}

<!--
### Forced Rollback

When using [Rolling Updates](#rolling-updates) with the default
[Pod Management Policy](#pod-management-policies) (`OrderedReady`),
it's possible to get into a broken state that requires manual intervention to repair.

If you update the Pod template to a configuration that never becomes Running and
Ready (for example, due to a bad binary or application-level configuration error),
StatefulSet will stop the rollout and wait.
-->
### 強制回滾 {#forced-rollback}

在預設 [Pod 管理策略](#pod-management-policies)(`OrderedReady`) 下使用
[滾動更新](#rolling-updates) ，可能進入需要人工干預才能修復的損壞狀態。

如果更新後 Pod 模板配置進入無法執行或就緒的狀態（例如，由於錯誤的二進位制檔案
或應用程式級配置錯誤），StatefulSet 將停止回滾並等待。

<!--
In this state, it's not enough to revert the Pod template to a good configuration.
Due to a [known issue](https://github.com/kubernetes/kubernetes/issues/67250),
StatefulSet will continue to wait for the broken Pod to become Ready
(which never happens) before it will attempt to revert it back to the working
configuration.

After reverting the template, you must also delete any Pods that StatefulSet had
already attempted to run with the bad configuration.
StatefulSet will then begin to recreate the Pods using the reverted template.
-->
在這種狀態下，僅將 Pod 模板還原為正確的配置是不夠的。由於
[已知問題](https://github.com/kubernetes/kubernetes/issues/67250)，StatefulSet
將繼續等待損壞狀態的 Pod 準備就緒（永遠不會發生），然後再嘗試將其恢復為正常工作配置。

恢復模板後，還必須刪除 StatefulSet 嘗試使用錯誤的配置來執行的 Pod。這樣，
StatefulSet 才會開始使用被還原的模板來重新建立 Pod。

<!-- ## PersistentVolumeClaim retention -->
## PersistentVolumeClaim 保留  {#persistentvolumeclaim-retention}

{{< feature-state for_k8s_version="v1.23" state="alpha" >}}

<!-- 
The optional `.spec.persistentVolumeClaimRetentionPolicy` field controls if
and how PVCs are deleted during the lifecycle of a StatefulSet. You must enable the
`StatefulSetAutoDeletePVC` [feature gate](/docs/reference/command-line-tools-reference/feature-gates/)
to use this field. Once enabled, there are two policies you can configure for each
StatefulSet:
-->
在 StatefulSet 的生命週期中，可選欄位 
`.spec.persistentVolumeClaimRetentionPolicy` 控制是否刪除以及如何刪除 PVC。
使用該欄位，你必須啟用 `StatefulSetAutoDeletePVC`
[特性門控](/zh-cn/docs/reference/command-line-tools-reference/feature-gates/)。
啟用後，你可以為每個 StatefulSet 配置兩個策略：

<!-- 
`whenDeleted`
: configures the volume retention behavior that applies when the StatefulSet is deleted

`whenScaled`
: configures the volume retention behavior that applies when the replica count of
  the StatefulSet   is reduced; for example, when scaling down the set.
  
For each policy that you can configure, you can set the value to either `Delete` or `Retain`.
-->
`whenDeleted`
: 配置刪除 StatefulSet 時應用的卷保留行為

`whenScaled`
: 配置當 StatefulSet 的副本數減少時應用的卷保留行為；例如，縮小集合時。
  
對於你可以配置的每個策略，你可以將值設定為 `Delete` 或 `Retain`。

<!-- 
`Delete`
: The PVCs created from the StatefulSet `volumeClaimTemplate` are deleted for each Pod
  affected by the policy. With the `whenDeleted` policy all PVCs from the
  `volumeClaimTemplate` are deleted after their Pods have been deleted. With the
  `whenScaled` policy, only PVCs corresponding to Pod replicas being scaled down are
  deleted, after their Pods have been deleted.
-->
`Delete`
: 對於受策略影響的每個 Pod，基於 StatefulSet 的 `volumeClaimTemplate` 欄位建立的 PVC 都會被刪除。
  使用 `whenDeleted` 策略，所有來自 `volumeClaimTemplate` 的 PVC 在其 Pod 被刪除後都會被刪除。
  使用 `whenScaled` 策略，只有與被縮減的 Pod 副本對應的 PVC 在其 Pod 被刪除後才會被刪除。

<!-- 
`Retain` (default)
: PVCs from the `volumeClaimTemplate` are not affected when their Pod is
  deleted. This is the behavior before this new feature.
-->
`Retain`（預設）
: 來自 `volumeClaimTemplate` 的 PVC 在 Pod 被刪除時不受影響。這是此新功能之前的行為。

<!-- 
Bear in mind that these policies **only** apply when Pods are being removed due to the
StatefulSet being deleted or scaled down. For example, if a Pod associated with a StatefulSet
fails due to node failure, and the control plane creates a replacement Pod, the StatefulSet
retains the existing PVC.  The existing volume is unaffected, and the cluster will attach it to
the node where the new Pod is about to launch.

The default for policies is `Retain`, matching the StatefulSet behavior before this new feature.

Here is an example policy.
-->
請記住，這些策略**僅**適用於由於 StatefulSet 被刪除或被縮小而被刪除的 Pod。
例如，如果與 StatefulSet 關聯的 Pod 由於節點故障而失敗，
並且控制平面建立了替換 Pod，則 StatefulSet 保留現有的 PVC。
現有卷不受影響，叢集會將其附加到新 Pod 即將啟動的節點上。

策略的預設值為 `Retain`，與此新功能之前的 StatefulSet 行為相匹配。

這是一個示例策略。

```yaml
apiVersion: apps/v1
kind: StatefulSet
...
spec:
  persistentVolumeClaimRetentionPolicy:
    whenDeleted: Retain
    whenScaled: Delete
...
```

<!--
The StatefulSet {{<glossary_tooltip text="controller" term_id="controller">}} adds [owner
references](/docs/concepts/overview/working-with-objects/owners-dependents/#owner-references-in-object-specifications)
to its PVCs, which are then deleted by the {{<glossary_tooltip text="garbage collector"
term_id="garbage-collection">}} after the Pod is terminated. This enables the Pod to
cleanly unmount all volumes before the PVCs are deleted (and before the backing PV and
volume are deleted, depending on the retain policy).  When you set the `whenDeleted`
policy to `Delete`, an owner reference to the StatefulSet instance is placed on all PVCs
associated with that StatefulSet. 
-->
StatefulSet {{<glossary_tooltip text="控制器" term_id="controller">}}為其 PVC 添加了
[屬主引用](/zh-cn/docs/concepts/overview/working-with-objects/owners-dependents/#owner-references-in-object-specifications)，
這些 PVC 在 Pod 終止後被{{<glossary_tooltip text="垃圾回收器" term_id="garbage-collection">}}刪除。
這使 Pod 能夠在刪除 PVC 之前（以及在刪除後備 PV 和卷之前，取決於保留策略）乾淨地解除安裝所有卷。
當你設定 `whenDeleted` 刪除策略，對 StatefulSet 例項的屬主引用放置在與該 StatefulSet 關聯的所有 PVC 上。

<!--
The `whenScaled` policy must delete PVCs only when a Pod is scaled down, and not when a
Pod is deleted for another reason. When reconciling, the StatefulSet controller compares
its desired replica count to the actual Pods present on the cluster. Any StatefulSet Pod
whose id greater than the replica count is condemned and marked for deletion. If the
`whenScaled` policy is `Delete`, the condemned Pods are first set as owners to the
associated StatefulSet template PVCs, before the Pod is deleted. This causes the PVCs
to be garbage collected after only the condemned Pods have terminated.
-->
`whenScaled` 策略必須僅在 Pod 縮減時刪除 PVC，而不是在 Pod 因其他原因被刪除時刪除。
執行協調操作時，StatefulSet 控制器將其所需的副本數與叢集上實際存在的 Pod 進行比較。
對於 StatefulSet 中的所有 Pod 而言，如果其 ID 大於副本數，則將被廢棄並標記為需要刪除。
如果 `whenScaled` 策略是 `Delete`，則在刪除 Pod 之前，
首先將已銷燬的 Pod 設定為與 StatefulSet 模板 對應的 PVC 的屬主。
這會導致 PVC 僅在已廢棄的 Pod 終止後被垃圾收集。

<!-- 
This means that if the controller crashes and restarts, no Pod will be deleted before its
owner reference has been updated appropriate to the policy. If a condemned Pod is
force-deleted while the controller is down, the owner reference may or may not have been
set up, depending on when the controller crashed. It may take several reconcile loops to
update the owner references, so some condemned Pods may have set up owner references and
other may not. For this reason we recommend waiting for the controller to come back up,
which will verify owner references before terminating Pods. If that is not possible, the
operator should verify the owner references on PVCs to ensure the expected objects are
deleted when Pods are force-deleted.
-->
這意味著如果控制器崩潰並重新啟動，在其屬主引用更新到適合策略的 Pod 之前，不會刪除任何 Pod。
如果在控制器關閉時強制刪除了已廢棄的 Pod，則屬主引用可能已被設定，也可能未被設定，具體取決於控制器何時崩潰。
更新屬主引用可能需要幾個協調迴圈，因此一些已廢棄的 Pod 可能已經被設定了屬主引用，而其他可能沒有。
出於這個原因，我們建議等待控制器恢復，控制器將在終止 Pod 之前驗證屬主引用。
如果這不可行，則操作員應驗證 PVC 上的屬主引用，以確保在強制刪除 Pod 時刪除預期的物件。

<!-- 
### Replicas

`.spec.replicas` is an optional field that specifies the number of desired Pods. It defaults to 1.

Should you manually scale a deployment, example via `kubectl scale
statefulset statefulset --replicas=X`, and then you update that StatefulSet
based on a manifest (for example: by running `kubectl apply -f
statefulset.yaml`), then applying that manifest overwrites the manual scaling
that you previously did.
-->
### 副本數 {#replicas}

`.spec.replicas` 是一個可選欄位，用於指定所需 Pod 的數量。它的預設值為 1。

如果你手動擴縮已部署的負載，例如透過 `kubectl scale statefulset statefulset --replicas=X`，
然後根據清單更新 StatefulSet（例如：透過執行 `kubectl apply -f statefulset.yaml`），
那麼應用該清單的操作會覆蓋你之前所做的手動縮放。

<!-- 
If a [HorizontalPodAutoscaler](/docs/tasks/run-application/horizontal-pod-autoscale/)
(or any similar API for horizontal scaling) is managing scaling for a
Statefulset, don't set `.spec.replicas`. Instead, allow the Kubernetes
{{<glossary_tooltip text="control plane" term_id="control-plane" >}} to manage
the `.spec.replicas` field automatically.
-->
如果 [HorizontalPodAutoscaler](/zh-cn/docs/tasks/run-application/horizontal-pod-autoscale/)
（或任何類似的水平縮放 API）正在管理 Statefulset 的縮放，
請不要設定 `.spec.replicas`。
相反，允許 Kubernetes 控制平面自動管理 `.spec.replicas` 欄位。

## {{% heading "whatsnext" %}}

<!--
* Learn about [Pods](/docs/concepts/workloads/pods).
* Find out how to use StatefulSets
  * Follow an example of [deploying a stateful application](/docs/tutorials/stateful-application/basic-stateful-set/).
  * Follow an example of [deploying Cassandra with Stateful Sets](/docs/tutorials/stateful-application/cassandra/).
  * Follow an example of [running a replicated stateful application](/docs/tasks/run-application/run-replicated-stateful-application/).
  * Learn how to [scale a StatefulSet](/docs/tasks/run-application/scale-stateful-set/).
  * Learn what's involved when you [delete a StatefulSet](/docs/tasks/run-application/delete-stateful-set/).
  * Learn how to [configure a Pod to use a volume for storage](/docs/tasks/configure-pod-container/configure-volume-storage/).
  * Learn how to [configure a Pod to use a PersistentVolume for storage](/docs/tasks/configure-pod-container/configure-persistent-volume-storage/).
* `StatefulSet` is a top-level resource in the Kubernetes REST API.
  Read the {{< api-reference page="workload-resources/stateful-set-v1" >}}
  object definition to understand the API for stateful sets.
* Read about [PodDisruptionBudget](/docs/concepts/workloads/pods/disruptions/) and how
  you can use it to manage application availability during disruptions.
-->
* 瞭解 [Pod](/zh-cn/docs/concepts/workloads/pods)。
* 瞭解如何使用 StatefulSet
  * 跟隨示例[部署有狀態應用](/zh-cn/docs/tutorials/stateful-application/basic-stateful-set/)。
  * 跟隨示例[使用 StatefulSet 部署 Cassandra](/zh-cn/docs/tutorials/stateful-application/cassandra/)。
  * 跟隨示例[執行多副本的有狀態應用程式](/zh-cn/docs/tasks/run-application/run-replicated-stateful-application/)。
  * 瞭解如何[擴縮 StatefulSet](/zh-cn/docs/tasks/run-application/scale-stateful-set/)。
  * 瞭解[刪除 StatefulSet](/zh-cn/docs/tasks/run-application/delete-stateful-set/)涉及到的操作。
  * 瞭解如何[配置 Pod 以使用捲進行儲存](/zh-cn/docs/tasks/configure-pod-container/configure-volume-storage/)。
  * 瞭解如何[配置 Pod 以使用 PersistentVolume 作為儲存](/zh-cn/docs/tasks/configure-pod-container/configure-persistent-volume-storage/)。
* `StatefulSet` 是 Kubernetes REST API 中的頂級資源。閱讀 {{< api-reference page="workload-resources/stateful-set-v1" >}}
   物件定義理解關於該資源的 API。
* 閱讀 [Pod 干擾預算（Disruption Budget）](/zh-cn/docs/concepts/workloads/pods/disruptions/)，瞭解如何在干擾下執行高度可用的應用。
