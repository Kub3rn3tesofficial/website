---
title: 在叢集中使用級聯刪除
content_type: task
---

<!--
title: Use Cascading Deletion in a Cluster
content_type: task
-->

<!--overview-->

<!--
This page shows you how to specify the type of
[cascading deletion](/docs/concepts/architecture/garbage-collection/#cascading-deletion)
to use in your cluster during {{<glossary_tooltip text="garbage collection" term_id="garbage-collection">}}.
-->
本頁面向你展示如何設定在你的叢集執行{{<glossary_tooltip text="垃圾收集" term_id="garbage-collection">}}
時要使用的[級聯刪除](/zh-cn/docs/concepts/architecture/garbage-collection/#cascading-deletion)
型別。

## {{% heading "prerequisites" %}}

{{< include "task-tutorial-prereqs.md" >}}

<!--
You also need to [create a sample Deployment](/docs/tasks/run-application/run-stateless-application-deployment/#creating-and-exploring-an-nginx-deployment) 
to experiment with the different types of cascading deletion. You will need to
recreate the Deployment for each type.
-->
你還需要[建立一個 Deployment 示例](/zh-cn/docs/tasks/run-application/run-stateless-application-deployment/#creating-and-exploring-an-nginx-deployment)
以試驗不同型別的級聯刪除。你需要為每種級聯刪除型別來重建 Deployment。

<!--
## Check owner references on your pods

Check that the `ownerReferences` field is present on your pods:
-->
## 檢查 Pod 上的屬主引用    {#check-owner-references-on-your-pods}

檢查確認你的 Pods 上存在 `ownerReferences` 欄位：

```shell 
kubectl get pods -l app=nginx --output=yaml
```

<!--
The output has an `ownerReferences` field similar to this:
-->
輸出中包含 `ownerReferences` 欄位，類似這樣：

```yaml
apiVersion: v1
    ...
    ownerReferences:
    - apiVersion: apps/v1
      blockOwnerDeletion: true
      controller: true
      kind: ReplicaSet
      name: nginx-deployment-6b474476c4
      uid: 4fdcd81c-bd5d-41f7-97af-3a3b759af9a7
    ...
```

<!--
## Use foreground cascading deletion {#use-foreground-cascading-deletion}

By default, Kubernetes uses [background cascading deletion](/docs/concepts/architecture/garbage-collection/#background-deletion)
to delete dependents of an object. You can switch to foreground cascading deletion
using either `kubectl` or the Kubernetes API, depending on the Kubernetes
version your cluster runs. {{<version-check>}}
-->
## 使用前臺級聯刪除    {#use-foreground-cascading-deletion}

預設情況下，Kubernetes 使用[後臺級聯刪除](/zh-cn/docs/concepts/architecture/garbage-collection/#background-deletion)
以刪除依賴某物件的其他物件。取決於你的叢集所執行的 Kubernetes 版本，
你可以使用 `kubectl` 或者 Kubernetes API 來切換到前臺級聯刪除。
{{<version-check>}}

{{<tabs name="foreground_deletion">}}
{{% tab name="Kubernetes 1.20.x 及更新版本" %}}

<!--
You can delete objects using foreground cascading deletion using `kubectl` or the
Kubernetes API.
-->
你可以使用 `kubectl` 或者 Kubernetes API 來基於前臺級聯刪除來刪除物件。

<!--
**Using kubectl**

Run the following command:
-->
**使用 kubectl**

執行下面的命令：

<!--TODO: verify release after which the --cascade flag is switched to a string in https://github.com/kubernetes/kubectl/commit/fd930e3995957b0093ecc4b9fd8b0525d94d3b4e-->

```shell
kubectl delete deployment nginx-deployment --cascade=foreground
```

<!--
**Using the Kubernetes API**
-->
**使用 Kubernetes API**

<!--
1. Start a local proxy session:
-->
1. 啟動一個本地代理會話：

   ```shell
   kubectl proxy --port=8080
   ```

<!--
1. Use `curl` to trigger deletion:
-->
2. 使用 `curl` 來觸發刪除操作：

   ```shell
   curl -X DELETE localhost:8080/apis/apps/v1/namespaces/default/deployments/nginx-deployment \
       -d '{"kind":"DeleteOptions","apiVersion":"v1","propagationPolicy":"Foreground"}' \
       -H "Content-Type: application/json"
   ```

   <!--
   The output contains a `foregroundDeletion` {{<glossary_tooltip text="finalizer" term_id="finalizer">}}
   like this:
   -->
   輸出中包含 `foregroundDeletion` {{<glossary_tooltip text="finalizer" term_id="finalizer">}}，
   類似這樣：

   ```
   "kind": "Deployment",
   "apiVersion": "apps/v1",
   "metadata": {
       "name": "nginx-deployment",
       "namespace": "default",
       "uid": "d1ce1b02-cae8-4288-8a53-30e84d8fa505",
       "resourceVersion": "1363097",
       "creationTimestamp": "2021-07-08T20:24:37Z",
       "deletionTimestamp": "2021-07-08T20:27:39Z",
       "finalizers": [
         "foregroundDeletion"
       ]
       ...
   ```

{{% /tab %}}
{{% tab name="Kubernetes 1.20.x 之前的版本" %}}

<!--
You can delete objects using foreground cascading deletion by calling the
Kubernetes API.

For details, read the [documentation for your Kubernetes version](/docs/home/supported-doc-versions/).
-->
你可以透過呼叫 Kubernetes API 來基於前臺級聯刪除模式刪除物件。

進一步的細節，可閱讀[特定於你的 Kubernetes 版本的文件](/zh-cn/docs/home/supported-doc-versions)。

<!--
1. Start a local proxy session:
-->
1. 啟動一個本地代理會話：

   ```shell
   kubectl proxy --port=8080
   ```

<!--
1. Use `curl` to trigger deletion:
-->
2. 使用 `curl` 來觸發刪除操作：

   ```shell
   curl -X DELETE localhost:8080/apis/apps/v1/namespaces/default/deployments/nginx-deployment \
       -d '{"kind":"DeleteOptions","apiVersion":"v1","propagationPolicy":"Foreground"}' \
       -H "Content-Type: application/json"
   ```

   <!--
   The output contains a `foregroundDeletion` {{<glossary_tooltip text="finalizer" term_id="finalizer">}}
   like this:
   -->
   輸出中包含 `foregroundDeletion` {{<glossary_tooltip text="finalizer" term_id="finalizer">}}，
   類似這樣：

   ```
   "kind": "Deployment",
   "apiVersion": "apps/v1",
   "metadata": {
       "name": "nginx-deployment",
       "namespace": "default",
       "uid": "d1ce1b02-cae8-4288-8a53-30e84d8fa505",
       "resourceVersion": "1363097",
       "creationTimestamp": "2021-07-08T20:24:37Z",
       "deletionTimestamp": "2021-07-08T20:27:39Z",
       "finalizers": [
         "foregroundDeletion"
       ]
       ...
   ```
{{% /tab %}}
{{</tabs>}}

<!--
## Use background cascading deletion {#use-background-cascading-deletion}
-->
## 使用後臺級聯刪除 {#use-background-cascading-deletion}

<!--
1. [Create a sample Deployment](/docs/tasks/run-application/run-stateless-application-deployment/#creating-and-exploring-an-nginx-deployment).
1. Use either `kubectl` or the Kubernetes API to delete the Deployment,
   depending on the Kubernetes version your cluster runs. {{<version-check>}}
-->
1. [建立一個 Deployment 示例](/zh-cn/docs/tasks/run-application/run-stateless-application-deployment/#creating-and-exploring-an-nginx-deployment)。
1. 基於你的叢集所執行的 Kubernetes 版本，使用 `kubectl` 或者 Kubernetes API 來刪除 Deployment。
   {{<version-check>}}

{{<tabs name="background_deletion">}}
{{% tab name="Kubernetes 1.20.x 及更新版本" %}}

<!--
You can delete objects using background cascading deletion using `kubectl`
or the Kubernetes API.

Kubernetes uses background cascading deletion by default, and does so
even if you run the following commands without the `--cascade` flag or the
`propagationPolicy` argument.
-->
你可以使用 `kubectl` 或者 Kubernetes API 來執行後臺級聯刪除方式的物件刪除操作。

Kubernetes 預設採用後臺級聯刪除方式，如果你在執行下面的命令時不指定
`--cascade` 標誌或者 `propagationPolicy` 引數時，用這種方式來刪除物件。

<!--
**Using kubectl**

Run the following command:
-->
**使用 kubectl**

執行下面的命令：

```shell
kubectl delete deployment nginx-deployment --cascade=background
```

<!--
**Using the Kubernetes API**
-->
**使用 Kubernetes API**

<!--
1. Start a local proxy session:
-->
1. 啟動一個本地代理會話：

   ```shell
   kubectl proxy --port=8080
   ```

<!--
1. Use `curl` to trigger deletion:
-->
2. 使用 `curl` 來觸發刪除操作：

   ```shell
   curl -X DELETE localhost:8080/apis/apps/v1/namespaces/default/deployments/nginx-deployment \
       -d '{"kind":"DeleteOptions","apiVersion":"v1","propagationPolicy":"Background"}' \
       -H "Content-Type: application/json"
   ```

   <!--
   The output is similar to this:
   -->
   輸出類似於：

   ```
   "kind": "Status",
   "apiVersion": "v1",
   ...
   "status": "Success",
   "details": {
       "name": "nginx-deployment",
       "group": "apps",
       "kind": "deployments",
       "uid": "cc9eefb9-2d49-4445-b1c1-d261c9396456"
   }
   ```
{{% /tab %}}
{{% tab name="Kubernetes 1.20.x 之前的版本" %}}

<!--
Kubernetes uses background cascading deletion by default, and does so
even if you run the following commands without the `--cascade` flag or the
`propagationPolicy: Background` argument.
-->
Kubernetes 預設採用後臺級聯刪除方式，如果你在執行下面的命令時不指定
`--cascade` 標誌或者 `propagationPolicy` 引數時，用這種方式來刪除物件。

<!--
For details, read the [documentation for your Kubernetes version](/docs/home/supported-doc-versions/).
-->
進一步的細節，可閱讀[特定於你的 Kubernetes 版本的文件](/zh-cn/docs/home/supported-doc-versions)。

<!--
**Using kubectl**

Run the following command:
-->
**使用 kubectl**

執行下面的命令：

```shell
kubectl delete deployment nginx-deployment --cascade=true
```

<!--
**Using the Kubernetes API**
-->
**使用 Kubernetes API**

<!--
1. Start a local proxy session:
-->
1. 啟動一個本地代理會話：

   ```shell
   kubectl proxy --port=8080
   ```

<!--
1. Use `curl` to trigger deletion:
-->
2. 使用 `curl` 來觸發刪除操作：

   ```shell
   curl -X DELETE localhost:8080/apis/apps/v1/namespaces/default/deployments/nginx-deployment \
       -d '{"kind":"DeleteOptions","apiVersion":"v1","propagationPolicy":"Background"}' \
       -H "Content-Type: application/json"
   ```

   <!--
   The output is similar to this:
   -->
   輸出類似於：

   ```
   "kind": "Status",
   "apiVersion": "v1",
   ...
   "status": "Success",
   "details": {
       "name": "nginx-deployment",
       "group": "apps",
       "kind": "deployments",
       "uid": "cc9eefb9-2d49-4445-b1c1-d261c9396456"
   }
   ```
{{% /tab %}}
{{</tabs>}}

<!--
## Delete owner objects and orphan dependents {#set-orphan-deletion-policy}

By default, when you tell Kubernetes to delete an object, the
{{<glossary_tooltip text="controller" term_id="controller">}} also deletes
dependent objects. You can make Kubernetes *orphan* these dependents using
`kubectl` or the Kubernetes API, depending on the Kubernetes version your
cluster runs. {{<version-check>}}
-->
## 刪除屬主物件和孤立的依賴物件   {#set-orphan-deletion-policy}

預設情況下，當你告訴 Kubernetes 刪除某個物件時，
{{<glossary_tooltip text="控制器" term_id="controller">}} 也會刪除依賴該物件
的其他物件。
取決於你的叢集所執行的 Kubernetes 版本，你也可以使用 `kubectl` 或者 Kubernetes
API 來讓 Kubernetes *孤立* 這些依賴物件。{{<version-check>}}

{{<tabs name="orphan_objects">}}
{{% tab name="Kubernetes 1.20.x 及更新版本" %}}

<!--
**Using kubectl**

Run the following command:
-->
**使用 kubectl**

執行下面的命令：

```shell
kubectl delete deployment nginx-deployment --cascade=orphan
```

<!--
**Using the Kubernetes API**
-->
**使用 Kubernetes API**

<!--
1. Start a local proxy session:
-->
1. 啟動一個本地代理會話：

   ```shell
   kubectl proxy --port=8080
   ```

<!--
1. Use `curl` to trigger deletion:
-->
2. 使用 `curl` 來觸發刪除操作：

   ```shell
   curl -X DELETE localhost:8080/apis/apps/v1/namespaces/default/deployments/nginx-deployment \
       -d '{"kind":"DeleteOptions","apiVersion":"v1","propagationPolicy":"Orphan"}' \
       -H "Content-Type: application/json"
   ```

   <!--
   The output contains `orphan` in the `finalizers` field, similar to this:
   -->
   輸出中在 `finalizers` 欄位中包含 `orphan`，如下所示：

   ```
   "kind": "Deployment",
   "apiVersion": "apps/v1",
   "namespace": "default",
   "uid": "6f577034-42a0-479d-be21-78018c466f1f",
   "creationTimestamp": "2021-07-09T16:46:37Z",
   "deletionTimestamp": "2021-07-09T16:47:08Z",
   "deletionGracePeriodSeconds": 0,
   "finalizers": [
     "orphan"
   ],
   ...
   ```

{{% /tab %}}
{{% tab name="Kubernetes 1.20.x 之前的版本" %}}

<!--
For details, read the [documentation for your Kubernetes version](/docs/home/supported-doc-versions/).
-->
進一步的細節，可閱讀[特定於你的 Kubernetes 版本的文件](/zh-cn/docs/home/supported-doc-versions)。

<!--
**Using kubectl**

Run the following command:
-->
**使用 kubectl**

執行下面的命令：

```shell
kubectl delete deployment nginx-deployment --cascade=orphan
```

<!--
**Using the Kubernetes API**
-->
**使用 Kubernetes API**

<!--
1. Start a local proxy session:
-->
1. 啟動一個本地代理會話：

   ```shell
   kubectl proxy --port=8080
   ```

<!--
1. Use `curl` to trigger deletion:
-->
2. 使用 `curl` 來觸發刪除操作：


   ```shell
   curl -X DELETE localhost:8080/apis/apps/v1/namespaces/default/deployments/nginx-deployment \
       -d '{"kind":"DeleteOptions","apiVersion":"v1","propagationPolicy":"Orphan"}' \
       -H "Content-Type: application/json"
   ```

   <!--
   The output contains `orphan` in the `finalizers` field, similar to this:
   -->
   輸出中在 `finalizers` 欄位中包含 `orphan`，如下所示：

   ```
   "kind": "Deployment",
   "apiVersion": "apps/v1",
   "namespace": "default",
   "uid": "6f577034-42a0-479d-be21-78018c466f1f",
   "creationTimestamp": "2021-07-09T16:46:37Z",
   "deletionTimestamp": "2021-07-09T16:47:08Z",
   "deletionGracePeriodSeconds": 0,
   "finalizers": [
     "orphan"
   ],
   ...
   ```
{{% /tab %}}
{{</tabs>}}

<!--
You can check that the Pods managed by the Deployment are still running:
-->
你可以檢查 Deployment 所管理的 Pods 仍然處於執行狀態：

```shell
kubectl get pods -l app=nginx
```

## {{% heading "whatsnext" %}}

<!--
* Learn about [owners and dependents](/docs/concepts/overview/working-with-objects/owners-dependents/) in Kubernetes.
* Learn about Kubernetes [finalizers](/docs/concepts/overview/working-with-objects/finalizers/).
* Learn about [garbage collection](/docs/concepts/workloads/controllers/garbage-collection/).
-->
* 瞭解 Kubernetes 中的[屬主與依賴](/zh-cn/docs/concepts/overview/working-with-objects/owners-dependents/)
* 瞭解 Kubernetes [finalizers](/zh-cn/docs/concepts/overview/working-with-objects/finalizers/)
* 瞭解[垃圾收集](/zh-cn/docs/concepts/architecture/garbage-collection/).

