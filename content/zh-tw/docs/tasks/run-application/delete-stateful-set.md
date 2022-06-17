---
title: 刪除 StatefulSet
content_type: task
weight: 60
---

<!--
reviewers:
- bprashanth
- erictune
- foxish
- janetkuo
- smarterclayton
title: Delete a StatefulSet
content_type: task
weight: 60
-->

<!-- overview -->

<!--
This task shows you how to delete a StatefulSet.
-->
本任務展示如何刪除 StatefulSet。

## {{% heading "prerequisites" %}}

<!--
* This task assumes you have an application running on your cluster represented by a StatefulSet.
-->
* 本任務假設在你的叢集上已經運行了由 StatefulSet 建立的應用。

<!-- steps -->

## 刪除 StatefulSet   {#deleting-a-statefulset}

<!--
You can delete a StatefulSet in the same way you delete other resources in Kubernetes: use the `kubectl delete` command, and specify the StatefulSet either by file or by name.
-->
你可以像刪除 Kubernetes 中的其他資源一樣刪除 StatefulSet：使用 `kubectl delete` 命令，並按檔案或者名字指定 StatefulSet。

```shell
kubectl delete -f <file.yaml>
```

<!--
```shell
kubectl delete statefulsets <statefulset-name>
```
-->
```shell
kubectl delete statefulsets <statefulset 名稱>
```

<!--
You may need to delete the associated headless service separately after the StatefulSet itself is deleted.

```shell
kubectl delete service <service-name>
```
-->
刪除 StatefulSet 之後，你可能需要單獨刪除關聯的無頭服務。

```shell
kubectl delete service <服務名稱>
```

<!--
When deleting a StatefulSet through `kubectl`, the StatefulSet scales down to 0. All Pods that are part of this workload are also deleted. If you want to delete only the StatefulSet and not the Pods, use `--cascade=orphan`.
For example:
--->
當透過 `kubectl` 刪除 StatefulSet 時，StatefulSet 會被縮容為 0。
屬於該 StatefulSet 的所有 Pod 也被刪除。
如果你只想刪除 StatefulSet 而不刪除 Pod，使用 `--cascade=orphan`。

```shell
kubectl delete -f <file.yaml> --cascade=orphan
```

<!--
By passing `--cascade=orphan` to `kubectl delete`, the Pods managed by the StatefulSet are left behind even after the StatefulSet object itself is deleted. If the pods have a label `app=myapp`, you can then delete them as follows:
--->
透過將 `--cascade=orphan` 傳遞給 `kubectl delete`，在刪除 StatefulSet 物件之後，
StatefulSet 管理的 Pod 會被保留下來。如果 Pod 具有標籤 `app=myapp`，則可以按照
如下方式刪除它們：

```shell
kubectl delete pods -l app=myapp
```


<!--
### Persistent Volumes

Deleting the Pods in a StatefulSet will not delete the associated volumes. This is to ensure that you have the chance to copy data off the volume before deleting it. Deleting the PVC after the pods have left the [terminating state](/docs/concepts/workloads/pods/pod/#termination-of-pods) might trigger deletion of the backing Persistent Volumes depending on the storage class and reclaim policy. You should never assume ability to access a volume after claim deletion.
-->
### 持久卷  {#persistent-volumes}

刪除 StatefulSet 管理的 Pod 並不會刪除關聯的卷。這是為了確保你有機會在刪除卷之前從卷中複製資料。
在 Pod 離開[終止狀態](/zh-cn/docs/concepts/workloads/pods/pod-lifecycle/#pod-termination)
後刪除 PVC 可能會觸發刪除背後的 PV 持久卷，具體取決於儲存類和回收策略。
永遠不要假定在 PVC 刪除後仍然能夠訪問卷。

<!--
Use caution when deleting a PVC, as it may lead to data loss.
-->
{{< note >}}
刪除 PVC 時要謹慎，因為這可能會導致資料丟失。
{{< /note >}}

<!--
### Complete deletion of a StatefulSet

To simply delete everything in a StatefulSet, including the associated pods, you can run a series of commands similar to the following:
-->
### 完全刪除 StatefulSet  {#complete-deletion-of-a-statefulset}

要刪除 StatefulSet 中的所有內容，包括關聯的 pods，你可以執行
一系列如下所示的命令：

```shell
grace=$(kubectl get pods <stateful-set-pod> --template '{{.spec.terminationGracePeriodSeconds}}')
kubectl delete statefulset -l app=myapp
sleep $grace
kubectl delete pvc -l app=myapp
```

<!--
In the example above, the Pods have the label `app=myapp`; substitute your own label as appropriate.
-->
在上面的例子中，Pod 的標籤為 `app=myapp`；適當地替換你自己的標籤。

<!--
### Force deletion of StatefulSet pods

If you find that some pods in your StatefulSet are stuck in the 'Terminating' or 'Unknown' states for an extended period of time, you may need to manually intervene to forcefully delete the pods from the apiserver. This is a potentially dangerous task. Refer to [Deleting StatefulSet Pods](/docs/tasks/manage-stateful-set/delete-pods/) for details.
-->
### 強制刪除 StatefulSet 的 Pod

如果你發現 StatefulSet 的某些 Pod 長時間處於 'Terminating' 或者 'Unknown' 狀態，
則可能需要手動干預以強制從 API 伺服器中刪除這些 Pod。
這是一項有點危險的任務。詳細資訊請閱讀
[刪除 StatefulSet 型別的 Pods](/zh-cn/docs/tasks/run-application/force-delete-stateful-set-pod/)。

## {{% heading "whatsnext" %}}

<!--
Learn more about [force deleting StatefulSet Pods](/docs/tasks/run-application/force-delete-stateful-set-pod/).
-->
進一步瞭解[強制刪除 StatefulSet 的 Pods](/zh-cn/docs/tasks/run-application/force-delete-stateful-set-pod/)。


