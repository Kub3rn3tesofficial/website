---
title: 除錯 StatefulSet
content_type: task
weight: 30
---
<!-- 
reviewers:
- bprashanth
- enisoc
- erictune
- foxish
- janetkuo
- kow3ns
- smarterclayton
title: Debug a StatefulSet
content_type: task
weight: 30
-->

<!-- overview -->
<!--
This task shows you how to debug a StatefulSet.
-->
此任務展示如何除錯 StatefulSet。

## {{% heading "prerequisites" %}}

<!--
* You need to have a Kubernetes cluster, and the kubectl command-line tool must be configured to communicate with your cluster.
* You should have a StatefulSet running that you want to investigate.
-->
* 你需要有一個 Kubernetes 叢集，已配置好的 kubectl 命令列工具與你的叢集進行通訊。
* 你應該有一個執行中的 StatefulSet，以便用於除錯。

<!-- steps -->

<!--
## Debugging a StatefulSet

In order to list all the pods which belong to a StatefulSet, which have a label `app=myapp` set on them,
you can use the following:
-->
## 除錯 StatefulSet   {#debuggin-a-statefulset}

StatefulSet 在建立 Pod 時為其設定了 `app=myapp` 標籤，列出僅屬於某 StatefulSet
的所有 Pod 時，可以使用以下命令：

```shell
kubectl get pods -l app=myapp
```

<!--
If you find that any Pods listed are in `Unknown` or `Terminating` state for an extended period of time,
refer to the [Deleting StatefulSet Pods](/docs/tasks/run-application/delete-stateful-set/) task for
instructions on how to deal with them.
You can debug individual Pods in a StatefulSet using the
[Debugging Pods](/docs/tasks/debug/debug-application/debug-pods/) guide.
-->
如果你發現列出的任何 Pod 長時間處於 `Unknown` 或 `Terminating` 狀態，請參閱
[刪除 StatefulSet Pod](/zh-cn/docs/tasks/run-application/delete-stateful-set/)
瞭解如何處理它們的說明。
你可以參考[除錯 Pod](/zh-cn/docs/tasks/debug/debug-application/debug-pods/)
來除錯 StatefulSet 中的各個 Pod。

## {{% heading "whatsnext" %}}

<!--
Learn more about [debugging an init-container](/docs/tasks/debug/debug-application/debug-init-containers/).
-->
* 進一步瞭解如何[除錯 Init 容器](/zh-cn/docs/tasks/debug/debug-application/debug-init-containers/)。

