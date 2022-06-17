---
title: 執行一個有狀態的應用程式
content_type: tutorial
weight: 30
---
<!--
reviewers:
- enisoc
- erictune
- foxish
- janetkuo
- kow3ns
- smarterclayton
title: Run a Replicated Stateful Application
content_type: tutorial
weight: 30
-->

<!-- overview -->

<!--
This page shows how to run a replicated stateful application using a
[StatefulSet](/docs/concepts/workloads/controllers/statefulset/) controller.
This application is a replicated MySQL database. The example topology has a
single primary server and multiple replicas, using asynchronous row-based
replication.
-->
本頁展示如何使用 [StatefulSet](/zh-cn/docs/concepts/workloads/controllers/statefulset/)
控制器執行一個有狀態的應用程式。此例是多副本的 MySQL 資料庫。
示例應用的拓撲結構有一個主伺服器和多個副本，使用非同步的基於行（Row-Based）
的資料複製。

<!--
**this is not a production configuration**.
In particular, MySQL settings remain on insecure defaults to keep the focus
on general patterns for running stateful applications in Kubernetes.
-->
{{< note >}}
**這不是生產環境下配置**。
尤其注意，MySQL 設定都使用的是不安全的預設值，這是因為我們想把重點放在 Kubernetes
中執行有狀態應用程式的一般模式上。
{{< /note >}}

## {{% heading "prerequisites" %}}

* {{< include "task-tutorial-prereqs.md" >}} {{< version-check >}}
* {{< include "default-storage-class-prereqs.md" >}}

<!--
* This tutorial assumes you are familiar with
  [PersistentVolumes](/docs/concepts/storage/persistent-volumes/)
  and [StatefulSets](/docs/concepts/workloads/controllers/statefulset/),
  as well as other core concepts like [Pods](/docs/concepts/workloads/pods/),
  [Services](/docs/concepts/services-networking/service/), and
  [ConfigMaps](/docs/tasks/configure-pod-container/configure-pod-configmap/).
* Some familiarity with MySQL helps, but this tutorial aims to present
  general patterns that should be useful for other systems.
* You are using the default namespace or another namespace that does not contain any conflicting objects.
-->
* 本教程假定你熟悉
  [PersistentVolumes](/zh-cn/docs/concepts/storage/persistent-volumes/)
  與 [StatefulSet](/zh-cn/docs/concepts/workloads/controllers/statefulset/),
  以及其他核心概念，例如 [Pod](/zh-cn/docs/concepts/workloads/pods/)、
  [服務](/zh-cn/docs/concepts/services-networking/service/) 與
  [ConfigMap](/zh-cn/docs/tasks/configure-pod-container/configure-pod-configmap/).
* 熟悉 MySQL 會有所幫助，但是本教程旨在介紹對其他系統應該有用的常規模式。
* 你正在使用預設名稱空間或不包含任何衝突物件的另一個名稱空間。

## {{% heading "objectives" %}}

<!--
* Deploy a replicated MySQL topology with a StatefulSet controller.
* Send MySQL client traffic.
* Observe resistance to downtime.
* Scale the StatefulSet up and down.
-->
* 使用 StatefulSet 控制器部署多副本 MySQL 拓撲架構。
* 傳送 MySQL 客戶端請求
* 觀察對宕機的抵抗力
* 擴縮 StatefulSet 的規模

<!-- lessoncontent -->

<!--
## Deploy MySQL

The example MySQL deployment consists of a ConfigMap, two Services,
and a StatefulSet.
-->
## 部署 MySQL  {#deploy-mysql}

MySQL 示例部署包含一個 ConfigMap、兩個 Service 與一個 StatefulSet。

### ConfigMap

<!--
Create the ConfigMap from the following YAML configuration file:
-->
使用以下的 YAML 配置檔案建立 ConfigMap ：

{{< codenew file="application/mysql/mysql-configmap.yaml" >}}

```shell
kubectl apply -f https://k8s.io/examples/application/mysql/mysql-configmap.yaml
```

<!--
This ConfigMap provides `my.cnf` overrides that let you independently control
configuration on the primary MySQL server and replicas.
In this case, you want the primary server to be able to serve replication logs to replicas
and you want repicas to reject any writes that don't come via replication. 
-->
這個 ConfigMap 提供 `my.cnf` 覆蓋設定，使你可以獨立控制 MySQL 主伺服器和從伺服器的配置。
在這裡，你希望主伺服器能夠將複製日誌提供給副本伺服器，並且希望副本伺服器拒絕任何不是透過
複製進行的寫操作。

<!--
There's nothing special about the ConfigMap itself that causes different
portions to apply to different Pods.
Each Pod decides which portion to look at as it's initializing,
based on information provided by the StatefulSet controller. 
-->
ConfigMap 本身沒有什麼特別之處，因而也不會出現不同部分應用於不同的 Pod 的情況。
每個 Pod 都會在初始化時基於 StatefulSet 控制器提供的資訊決定要檢視的部分。

<!--
### Services 

Create the Services from the following YAML configuration file: 
-->
### 服務  {#services}

使用以下 YAML 配置檔案建立服務：

{{< codenew file="application/mysql/mysql-services.yaml" >}}

```shell
kubectl apply -f https://k8s.io/examples/application/mysql/mysql-services.yaml
```

<!--
The Headless Service provides a home for the DNS entries that the StatefulSet
controller creates for each Pod that's part of the set.
Because the Headless Service is named `mysql`, the Pods are accessible by
resolving `<pod-name>.mysql` from within any other Pod in the same Kubernetes
cluster and namespace. 
-->
這個無頭服務給 StatefulSet 控制器為集合中每個 Pod 建立的 DNS 條目提供了一個宿主。
因為無頭服務名為 `mysql`，所以可以透過在同一 Kubernetes 叢集和名稱空間中的任何其他 Pod
內解析 `<Pod 名稱>.mysql` 來訪問 Pod。

<!--
The Client Service, called `mysql-read`, is a normal Service with its own
cluster IP that distributes connections across all MySQL Pods that report
being Ready. The set of potential endpoints includes the primary MySQL server and all
replicas. 
-->
客戶端服務稱為 `mysql-read`，是一種常規服務，具有其自己的叢集 IP。
該叢集 IP 在報告就緒的所有MySQL Pod 之間分配連線。
可能的端點集合包括 MySQL 主節點和所有副本節點。

<!--
Note that only read queries can use the load-balanced Client Service.
Because there is only one primary MySQL server, clients should connect directly to the
primary MySQL Pod (through its DNS entry within the Headless Service) to execute
writes. 
-->
請注意，只有讀查詢才能使用負載平衡的客戶端服務。
因為只有一個 MySQL 主伺服器，所以客戶端應直接連線到 MySQL 主伺服器 Pod
（透過其在無頭服務中的 DNS 條目）以執行寫入操作。

### StatefulSet

<!--
Finally, create the StatefulSet from the following YAML configuration file: 
-->
最後，使用以下 YAML 配置檔案建立 StatefulSet：

{{< codenew file="application/mysql/mysql-statefulset.yaml" >}}

```shell
kubectl apply -f https://k8s.io/examples/application/mysql/mysql-statefulset.yaml
```

<!--
You can watch the startup progress by running: 
-->
你可以透過執行以下命令檢視啟動進度：

```shell
kubectl get pods -l app=mysql --watch
```

<!--
After a while, you should see all 3 Pods become Running: 
-->
一段時間後，你應該看到所有 3 個 Pod 進入 Running 狀態：

```
NAME      READY     STATUS    RESTARTS   AGE
mysql-0   2/2       Running   0          2m
mysql-1   2/2       Running   0          1m
mysql-2   2/2       Running   0          1m
```

<!--
Press **Ctrl+C** to cancel the watch.
If you don't see any progress, make sure you have a dynamic PersistentVolume
provisioner enabled as mentioned in the [prerequisites](#before-you-begin). 
-->
輸入 **Ctrl+C** 結束 watch 操作。
如果你看不到任何進度，確保已啟用[前提條件](#準備開始)
中提到的動態 PersistentVolume 預配器。

<!--
This manifest uses a variety of techniques for managing stateful Pods as part of
a StatefulSet. The next section highlights some of these techniques to explain
what happens as the StatefulSet creates Pods. 
-->
此清單使用多種技術來管理作為 StatefulSet 的一部分的有狀態 Pod。
下一節重點介紹其中的一些技巧，以解釋 StatefulSet 建立 Pod 時發生的狀況。

<!--
## Understanding stateful Pod initialization 

The StatefulSet controller starts Pods one at a time, in order by their
ordinal index.
It waits until each Pod reports being Ready before starting the next one. 
-->
## 瞭解有狀態的 Pod 初始化

StatefulSet 控制器按序數索引順序地每次啟動一個 Pod。
它一直等到每個 Pod 報告就緒才再啟動下一個 Pod。

<!--
In addition, the controller assigns each Pod a unique, stable name of the form
`<statefulset-name>-<ordinal-index>`, which results in Pods named `mysql-0`,
`mysql-1`, and `mysql-2`. 
-->
此外，控制器為每個 Pod 分配一個唯一、穩定的名稱，形如 `<statefulset 名稱>-<序數索引>`，
其結果是 Pods 名為 `mysql-0`、`mysql-1` 和 `mysql-2`。

<!--
The Pod template in the above StatefulSet manifest takes advantage of these
properties to perform orderly startup of MySQL replication. 
-->
上述 StatefulSet 清單中的 Pod 模板利用這些屬性來執行 MySQL 副本的有序啟動。

<!--
### Generating configuration 

Before starting any of the containers in the Pod spec, the Pod first runs any
[Init Containers](/docs/concepts/workloads/pods/init-containers/)
in the order defined. 
-->
### 生成配置

在啟動 Pod 規約中的任何容器之前，Pod 首先按順序執行所有的
[Init 容器](/zh-cn/docs/concepts/workloads/pods/init-containers/)。

<!--
The first Init Container, named `init-mysql`, generates special MySQL config
files based on the ordinal index. 
-->
第一個名為 `init-mysql` 的 Init 容器根據序號索引生成特殊的 MySQL 配置檔案。

<!--
The script determines its own ordinal index by extracting it from the end of
the Pod name, which is returned by the `hostname` command.
Then it saves the ordinal (with a numeric offset to avoid reserved values)
into a file called `server-id.cnf` in the MySQL `conf.d` directory.
This translates the unique, stable identity provided by the StatefulSet
controller into the domain of MySQL server IDs, which require the same
properties. 
-->
該指令碼透過從 Pod 名稱的末尾提取索引來確定自己的序號索引，而 Pod 名稱由 `hostname` 命令返回。
然後將序數（帶有數字偏移量以避免保留值）儲存到 MySQL `conf.d` 目錄中的檔案 `server-id.cnf`。
這一操作將 StatefulSet 所提供的唯一、穩定的標識轉換為 MySQL 伺服器的 ID，
而這些 ID 也是需要唯一性、穩定性保證的。

<!--
The script in the `init-mysql` container also applies either `primary.cnf` or
`replica.cnf` from the ConfigMap by copying the contents into `conf.d`.
Because the example topology consists of a single primary MySQL server and any number of
replicas, the script assigns ordinal `0` to be the primary server, and everyone
else to be replicas. 

Combined with the StatefulSet controller's
[deployment order guarantee](/docs/concepts/workloads/controllers/statefulset/#deployment-and-scaling-guarantees),
this ensures the primary MySQL server is Ready before creating replicas, so they can begin
replicating.
-->
透過將內容複製到 `conf.d` 中，`init-mysql` 容器中的指令碼也可以應用 ConfigMap 中的
`primary.cnf` 或 `replica.cnf`。
由於示例部署結構由單個 MySQL 主節點和任意數量的副本節點組成，
因此指令碼僅將序數 `0` 指定為主節點，而將其他所有節點指定為副本節點。

與 StatefulSet 控制器的
[部署順序保證](/zh-cn/docs/concepts/workloads/controllers/statefulset/#deployment-and-scaling-guarantees)
相結合，
可以確保 MySQL 主伺服器在建立副本伺服器之前已準備就緒，以便它們可以開始複製。

<!--
### Cloning existing data 

In general, when a new Pod joins the set as a replica, it must assume the primary MySQL
server might already have data on it. It also must assume that the replication
logs might not go all the way back to the beginning of time. 
-->
### 克隆現有資料

通常，當新 Pod 作為副本節點加入集合時，必須假定 MySQL 主節點可能已經有資料。
還必須假設複製日誌可能不會一直追溯到時間的開始。

<!--
These conservative assumptions are the key to allow a running StatefulSet
to scale up and down over time, rather than being fixed at its initial size. 
-->
這些保守的假設是允許正在執行的 StatefulSet 隨時間擴大和縮小而不是固定在其初始大小的關鍵。

<!--
The second Init Container, named `clone-mysql`, performs a clone operation on
a replica Pod the first time it starts up on an empty PersistentVolume.
That means it copies all existing data from another running Pod,
so its local state is consistent enough to begin replicating from the primary server.
-->
第二個名為 `clone-mysql` 的 Init 容器，第一次在帶有空 PersistentVolume 的副本 Pod
上啟動時，會在從屬 Pod 上執行克隆操作。
這意味著它將從另一個執行中的 Pod 複製所有現有資料，使此其本地狀態足夠一致，
從而可以開始從主伺服器複製。

<!--
MySQL itself does not provide a mechanism to do this, so the example uses a
popular open-source tool called Percona XtraBackup.
During the clone, the source MySQL server might suffer reduced performance.
To minimize impact on the primary MySQL server, the script instructs each Pod to clone
from the Pod whose ordinal index is one lower.
This works because the StatefulSet controller always ensures Pod `N` is
Ready before starting Pod `N+1`. 
-->
MySQL 本身不提供執行此操作的機制，因此本示例使用了一種流行的開源工具 Percona XtraBackup。
在克隆期間，源 MySQL 伺服器效能可能會受到影響。
為了最大程度地減少對 MySQL 主伺服器的影響，該指令碼指示每個 Pod 從序號較低的 Pod 中克隆。
可以這樣做的原因是 StatefulSet 控制器始終確保在啟動 Pod `N + 1` 之前 Pod `N` 已準備就緒。

<!--
### Starting replication 

After the Init Containers complete successfully, the regular containers run.
The MySQL Pods consist of a `mysql` container that runs the actual `mysqld`
server, and an `xtrabackup` container that acts as a
[sidecar](https://kubernetes.io/blog/2015/06/the-distributed-system-toolkit-patterns). 
-->
### 開始複製

Init 容器成功完成後，應用容器將執行。
MySQL Pod 由執行實際 `mysqld` 服務的 `mysql` 容器和充當
[輔助工具](https://kubernetes.io/blog/2015/06/the-distributed-system-toolkit-patterns)
的 xtrabackup 容器組成。

<!--
The `xtrabackup` sidecar looks at the cloned data files and determines if
it's necessary to initialize MySQL replication on the replica.
If so, it waits for `mysqld` to be ready and then executes the
`CHANGE MASTER TO` and `START SLAVE` commands with replication parameters
extracted from the XtraBackup clone files. 
-->
`xtrabackup` sidecar 容器檢視克隆的資料檔案，並確定是否有必要在副本伺服器上初始化 MySQL 複製。
如果是這樣，它將等待 `mysqld` 準備就緒，然後使用從 XtraBackup 克隆檔案中提取的複製引數
執行  `CHANGE MASTER TO` 和 `START SLAVE` 命令。

<!--
Once a replica begins replication, it remembers its primary MySQL server and
reconnects automatically if the server restarts or the connection dies.
Also, because replicas look for the primary server at its stable DNS name
(`mysql-0.mysql`), they automatically find the primary server even if it gets a new
Pod IP due to being rescheduled. 
-->
一旦副本伺服器開始複製後，它會記住其 MySQL 主伺服器，並且如果伺服器重新啟動或
連線中斷也會自動重新連線。
另外，因為副本伺服器會以其穩定的 DNS 名稱查詢主伺服器（`mysql-0.mysql`），
即使由於重新排程而獲得新的 Pod IP，它們也會自動找到主伺服器。

<!--
Lastly, after starting replication, the `xtrabackup` container listens for
connections from other Pods requesting a data clone.
This server remains up indefinitely in case the StatefulSet scales up, or in
case the next Pod loses its PersistentVolumeClaim and needs to redo the clone. 
-->
最後，開始複製後，`xtrabackup` 容器監聽來自其他 Pod 的連線，處理其資料克隆請求。
如果 StatefulSet 擴大規模，或者下一個 Pod 失去其 PersistentVolumeClaim 並需要重新克隆，
則此伺服器將無限期保持執行。

<!--
## Sending client traffic 

You can send test queries to the primary MySQL server (hostname `mysql-0.mysql`)
by running a temporary container with the `mysql:5.7` image and running the
`mysql` client binary. 
-->
## 傳送客戶端請求

你可以透過執行帶有 `mysql:5.7` 映象的臨時容器並執行 `mysql` 客戶端二進位制檔案，
將測試查詢傳送到 MySQL 主伺服器（主機名 `mysql-0.mysql`）。

```shell
kubectl run mysql-client --image=mysql:5.7 -i --rm --restart=Never --\
  mysql -h mysql-0.mysql <<EOF
CREATE DATABASE test;
CREATE TABLE test.messages (message VARCHAR(250));
INSERT INTO test.messages VALUES ('hello');
EOF
```

<!--
Use the hostname `mysql-read` to send test queries to any server that reports
being Ready: 
-->
使用主機名 `mysql-read` 將測試查詢傳送到任何報告為就緒的伺服器：

```shell
kubectl run mysql-client --image=mysql:5.7 -i -t --rm --restart=Never --\
  mysql -h mysql-read -e "SELECT * FROM test.messages"
```

<!--
You should get output like this: 
-->
你應該獲得如下輸出：

```
Waiting for pod default/mysql-client to be running, status is Pending, pod ready: false
+---------+
| message |
+---------+
| hello   |
+---------+
pod "mysql-client" deleted
```

<!--
To demonstrate that the `mysql-read` Service distributes connections across
servers, you can run `SELECT @@server_id` in a loop: 
-->
為了演示 `mysql-read` 服務在伺服器之間分配連線，你可以在迴圈中執行 `SELECT @@server_id`：

```shell
kubectl run mysql-client-loop --image=mysql:5.7 -i -t --rm --restart=Never --\
  bash -ic "while sleep 1; do mysql -h mysql-read -e 'SELECT @@server_id,NOW()'; done"
```

<!--
You should see the reported `@@server_id` change randomly, because a different
endpoint might be selected upon each connection attempt: 
-->
你應該看到報告的 `@@server_id` 發生隨機變化，因為每次嘗試連線時都可能選擇了不同的端點：

```
+-------------+---------------------+
| @@server_id | NOW()               |
+-------------+---------------------+
|         100 | 2006-01-02 15:04:05 |
+-------------+---------------------+
+-------------+---------------------+
| @@server_id | NOW()               |
+-------------+---------------------+
|         102 | 2006-01-02 15:04:06 |
+-------------+---------------------+
+-------------+---------------------+
| @@server_id | NOW()               |
+-------------+---------------------+
|         101 | 2006-01-02 15:04:07 |
+-------------+---------------------+
```

<!--
You can press **Ctrl+C** when you want to stop the loop, but it's useful to keep
it running in another window so you can see the effects of the following steps. 
-->
要停止迴圈時可以按 **Ctrl+C** ，但是讓它在另一個視窗中執行非常有用，
這樣你就可以看到以下步驟的效果。

<!--
## Simulating Pod and Node downtime 

To demonstrate the increased availability of reading from the pool of replicas
instead of a single server, keep the `SELECT @@server_id` loop from above
running while you force a Pod out of the Ready state. 
-->
## 模擬 Pod 和 Node 的宕機時間

為了證明從副本節點快取而不是單個伺服器讀取資料的可用性提高，請在使 Pod 退出 Ready
狀態時，保持上述 `SELECT @@server_id` 迴圈一直執行。

<!--
### Break the Readiness Probe 

The [readiness probe](/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/#define-readiness-probes)
for the `mysql` container runs the command `mysql -h 127.0.0.1 -e 'SELECT 1'`
to make sure the server is up and able to execute queries. 
-->
### 破壞就緒態探測

`mysql` 容器的
[就緒態探測](/zh-cn/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/#define-readiness-probes)
執行命令 `mysql -h 127.0.0.1 -e 'SELECT 1'`，以確保伺服器已啟動並能夠執行查詢。

<!--
One way to force this readiness probe to fail is to break that command: 
-->
迫使就緒態探測失敗的一種方法就是中止該命令：

```shell
kubectl exec mysql-2 -c mysql -- mv /usr/bin/mysql /usr/bin/mysql.off
```

<!--
This reaches into the actual container's filesystem for Pod `mysql-2` and
renames the `mysql` command so the readiness probe can't find it.
After a few seconds, the Pod should report one of its containers as not Ready,
which you can check by running: 
-->
此命令會進入 Pod `mysql-2` 的實際容器檔案系統，重新命名 `mysql` 命令，導致就緒態探測無法找到它。
幾秒鐘後， Pod 會報告其中一個容器未就緒。你可以透過執行以下命令進行檢查：

```shell
kubectl get pod mysql-2
```

<!--
Look for `1/2` in the `READY` column: 
-->
在 `READY` 列中查詢 `1/2`：

```
NAME      READY     STATUS    RESTARTS   AGE
mysql-2   1/2       Running   0          3m
```

<!--
At this point, you should see your `SELECT @@server_id` loop continue to run,
although it never reports `102` anymore.
Recall that the `init-mysql` script defined `server-id` as `100 + $ordinal`,
so server ID `102` corresponds to Pod `mysql-2`. 
-->
此時，你應該會看到 `SELECT @@server_id` 迴圈繼續執行，儘管它不再報告 `102`。
回想一下，`init-mysql` 指令碼將 `server-id` 定義為 `100 + $ordinal`，
因此伺服器 ID `102` 對應於 Pod `mysql-2`。

<!--
Now repair the Pod and it should reappear in the loop output
after a few seconds: 
-->
現在修復 Pod，幾秒鐘後它應該重新出現在迴圈輸出中：

```shell
kubectl exec mysql-2 -c mysql -- mv /usr/bin/mysql.off /usr/bin/mysql
```

<!--
### Delete Pods 

The StatefulSet also recreates Pods if they're deleted, similar to what a
ReplicaSet does for stateless Pods. 
-->
### 刪除 Pods

如果刪除了 Pod，則 StatefulSet 還會重新建立 Pod，類似於 ReplicaSet 對無狀態 Pod 所做的操作。

```shell
kubectl delete pod mysql-2
```

<!--
The StatefulSet controller notices that no `mysql-2` Pod exists anymore,
and creates a new one with the same name and linked to the same
PersistentVolumeClaim.
You should see server ID `102` disappear from the loop output for a while
and then return on its own. 
-->
StatefulSet 控制器注意到不再存在 `mysql-2` Pod，於是建立一個具有相同名稱並連結到相同
PersistentVolumeClaim 的新 Pod。
你應該看到伺服器 ID `102` 從迴圈輸出中消失了一段時間，然後又自行出現。

<!--
### Drain a Node 

If your Kubernetes cluster has multiple Nodes, you can simulate Node downtime
(such as when Nodes are upgraded) by issuing a
[drain](/docs/reference/generated/kubectl/kubectl-commands/#drain). 
-->
### 騰空節點   {#drain-a-node}

如果你的 Kubernetes 叢集具有多個節點，則可以透過發出以下
[drain](/docs/reference/generated/kubectl/kubectl-commands/#drain)
命令來模擬節點停機（就好像節點在被升級）。

<!--
First determine which Node one of the MySQL Pods is on: 
-->
首先確定 MySQL Pod 之一在哪個節點上：

```shell
kubectl get pod mysql-2 -o wide
```

<!--
The Node name should show up in the last column: 
-->
節點名稱應顯示在最後一列中：

```
NAME      READY     STATUS    RESTARTS   AGE       IP            NODE
mysql-2   2/2       Running   0          15m       10.244.5.27   kubernetes-node-9l2t
```

<!--
Then drain the Node by running the following command, which cordons it so
no new Pods may schedule there, and then evicts any existing Pods.
Replace `<node-name>` with the name of the Node you found in the last step. 
-->
然後透過執行以下命令騰空節點，該命令將其保護起來，以使新的 Pod 不能排程到該節點，
然後逐出所有現有的 Pod。將 `<節點名稱>` 替換為在上一步中找到的節點名稱。


<!--
This might impact other applications on the Node, so it's best to
**only do this in a test cluster**. 

```shell
kubectl drain <node-name> --force --delete-local-data --ignore-daemonsets
```
-->
這可能會影響節點上的其他應用程式，因此最好 **僅在測試叢集中執行此操作**。

```shell
kubectl drain <節點名稱> --force --delete-local-data --ignore-daemonsets
```

<!--
Now you can watch as the Pod reschedules on a different Node: 
-->
現在，你可以看到 Pod 被重新排程到其他節點上：

```shell
kubectl get pod mysql-2 -o wide --watch
```

<!--
It should look something like this: 
-->
它看起來應該像這樣：

```
NAME      READY   STATUS          RESTARTS   AGE       IP            NODE
mysql-2   2/2     Terminating     0          15m       10.244.1.56   kubernetes-node-9l2t
[...]
mysql-2   0/2     Pending         0          0s        <none>        kubernetes-node-fjlm
mysql-2   0/2     Init:0/2        0          0s        <none>        kubernetes-node-fjlm
mysql-2   0/2     Init:1/2        0          20s       10.244.5.32   kubernetes-node-fjlm
mysql-2   0/2     PodInitializing 0          21s       10.244.5.32   kubernetes-node-fjlm
mysql-2   1/2     Running         0          22s       10.244.5.32   kubernetes-node-fjlm
mysql-2   2/2     Running         0          30s       10.244.5.32   kubernetes-node-fjlm
```

<!--
And again, you should see server ID `102` disappear from the
`SELECT @@server_id` loop output for a while and then return. 
-->
再次，你應該看到伺服器 ID `102` 從 `SELECT @@server_id` 迴圈輸出
中消失一段時間，然後自行出現。

<!--
Now uncordon the Node to return it to a normal state: 

```shell
kubectl uncordon <node-name>
```
-->
現在去掉節點保護（Uncordon），使其恢復為正常模式:

```shell
kubectl uncordon <節點名稱>
```

<!--
## Scaling the number of replicas

With MySQL replication, you can scale your read query capacity by adding replicas.
With StatefulSet, you can do this with a single command: 
-->
## 擴充套件副本節點數量

使用 MySQL 複製，你可以透過新增副本節點來擴充套件讀取查詢的能力。
使用 StatefulSet，你可以使用單個命令執行此操作：

```shell
kubectl scale statefulset mysql --replicas=5
```

<!--
Watch the new Pods come up by running: 
-->
檢視新的 Pod 的執行情況：

```shell
kubectl get pods -l app=mysql --watch
```

<!--
Once they're up, you should see server IDs `103` and `104` start appearing in
the `SELECT @@server_id` loop output.

You can also verify that these new servers have the data you added before they
existed: 
-->
一旦 Pod 啟動，你應該看到伺服器 IDs `103` 和 `104` 開始出現在 `SELECT @@server_id` 迴圈輸出中。

你還可以驗證這些新伺服器在存在之前已添加了資料：

```shell
kubectl run mysql-client --image=mysql:5.7 -i -t --rm --restart=Never --\
  mysql -h mysql-3.mysql -e "SELECT * FROM test.messages"
```

```
Waiting for pod default/mysql-client to be running, status is Pending, pod ready: false
+---------+
| message |
+---------+
| hello   |
+---------+
pod "mysql-client" deleted
```

<!--
Scaling back down is also seamless: 
-->
向下縮容操作也是很平滑的：

```shell
kubectl scale statefulset mysql --replicas=3
```

<!--
Note, however, that while scaling up creates new PersistentVolumeClaims
automatically, scaling down does not automatically delete these PVCs.
This gives you the choice to keep those initialized PVCs around to make
scaling back up quicker, or to extract data before deleting them. 
-->
但是請注意，按比例擴大會自動建立新的 PersistentVolumeClaims，而按比例縮小不會自動刪除這些 PVC。
這使你可以選擇保留那些初始化的 PVC，以更快地進行縮放，或者在刪除它們之前提取資料。

<!--
You can see this by running: 
-->
你可以透過執行以下命令檢視此資訊：

```shell
kubectl get pvc -l app=mysql
```

<!--
Which shows that all 5 PVCs still exist, despite having scaled the
StatefulSet down to 3: 
-->
這表明，儘管將 StatefulSet 縮小為3，所有5個 PVC 仍然存在：

```
NAME           STATUS    VOLUME                                     CAPACITY   ACCESSMODES   AGE
data-mysql-0   Bound     pvc-8acbf5dc-b103-11e6-93fa-42010a800002   10Gi       RWO           20m
data-mysql-1   Bound     pvc-8ad39820-b103-11e6-93fa-42010a800002   10Gi       RWO           20m
data-mysql-2   Bound     pvc-8ad69a6d-b103-11e6-93fa-42010a800002   10Gi       RWO           20m
data-mysql-3   Bound     pvc-50043c45-b1c5-11e6-93fa-42010a800002   10Gi       RWO           2m
data-mysql-4   Bound     pvc-500a9957-b1c5-11e6-93fa-42010a800002   10Gi       RWO           2m
```

<!--
If you don't intend to reuse the extra PVCs, you can delete them: 
-->
如果你不打算重複使用多餘的 PVC，則可以刪除它們：

```shell
kubectl delete pvc data-mysql-3
kubectl delete pvc data-mysql-4
```

## {{% heading "cleanup" %}}


<!--
1. Cancel the `SELECT @@server_id` loop by pressing **Ctrl+C** in its terminal,
   or running the following from another terminal: 
-->
1. 透過在終端上按 **Ctrl+C** 取消 `SELECT @@server_id` 迴圈，或從另一個終端執行以下命令：

   ```shell
   kubectl delete pod mysql-client-loop --now
   ```

<!--
1. Delete the StatefulSet. This also begins terminating the Pods. 
-->
2. 刪除 StatefulSet。這也會開始終止 Pod。

   ```shell
   kubectl delete statefulset mysql
   ```

<!--
1. Verify that the Pods disappear.
   They might take some time to finish terminating. 
-->
3. 驗證 Pod 消失。他們可能需要一些時間才能完成終止。

   ```shell
   kubectl get pods -l app=mysql
   ```

   <!-- You'll know the Pods have terminated when the above returns: -->
   當上述命令返回如下內容時，你就知道 Pod 已終止：

   ```
   No resources found.
   ```

<!--
1. Delete the ConfigMap, Services, and PersistentVolumeClaims. 
-->
4. 刪除 ConfigMap、Services 和 PersistentVolumeClaims。

   ```shell
   kubectl delete configmap,service,pvc -l app=mysql
   ```

<!--
1. If you manually provisioned PersistentVolumes, you also need to manually
   delete them, as well as release the underlying resources.
   If you used a dynamic provisioner, it automatically deletes the
   PersistentVolumes when it sees that you deleted the PersistentVolumeClaims.
   Some dynamic provisioners (such as those for EBS and PD) also release the
   underlying resources upon deleting the PersistentVolumes. 
-->
5. 如果你手動供應 PersistentVolume，則還需要手動刪除它們，並釋放下層資源。
   如果你使用了動態預配器，當得知你刪除 PersistentVolumeClaims 時，它將自動刪除 PersistentVolumes。
   一些動態預配器（例如用於 EBS 和 PD 的預配器）也會在刪除 PersistentVolumes 時釋放下層資源。

## {{% heading "whatsnext" %}}

<!--
* Learn more about [scaling a StatefulSet](/docs/tasks/run-application/scale-stateful-set/).
* Learn more about [debugging a StatefulSet](/docs/tasks/debug/debug-application/debug-statefulset/).
* Learn more about [deleting a StatefulSet](/docs/tasks/run-application/delete-stateful-set/).
* Learn more about [force deleting StatefulSet Pods](/docs/tasks/run-application/force-delete-stateful-set-pod/).
* Look in the [Helm Charts repository](https://artifacthub.io/)
  for other stateful application examples.
-->
* 進一步瞭解[為 StatefulSet 擴縮容](/zh-cn/docs/tasks/run-application/scale-stateful-set/).
* 進一步瞭解[除錯 StatefulSet](/zh-cn/docs/tasks/debug/debug-application/debug-statefulset/).
* 進一步瞭解[刪除 StatefulSet](/zh-cn/docs/tasks/run-application/delete-stateful-set/).
* 進一步瞭解[強制刪除 StatefulSet Pods](/zh-cn/docs/tasks/run-application/force-delete-stateful-set-pod/).
* 在 [Helm Charts 倉庫](https://artifacthub.io/)中查詢其他有狀態的應用程式示例。

