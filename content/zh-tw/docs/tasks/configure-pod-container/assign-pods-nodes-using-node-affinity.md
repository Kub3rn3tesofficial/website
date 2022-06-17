---
title: 用節點親和性把 Pods 分配到節點
min-kubernetes-server-version: v1.10
content_type: task
weight: 120
---
<!--
title: Assign Pods to Nodes using Node Affinity
min-kubernetes-server-version: v1.10
content_type: task
weight: 120
-->

<!-- overview -->
<!--
This page shows how to assign a Kubernetes Pod to a particular node using Node Affinity in a
Kubernetes cluster.
-->
本頁展示在 Kubernetes 叢集中，如何使用節點親和性把 Kubernetes Pod 分配到特定節點。

## {{% heading "prerequisites" %}}


{{< include "task-tutorial-prereqs.md" >}} {{< version-check >}}



<!-- steps -->

<!--
## Add a label to a node

1. List the nodes in your cluster, along with their labels:
-->
## 給節點新增標籤

1. 列出叢集中的節點及其標籤：

    ```shell
    kubectl get nodes --show-labels
    ```
    <!--
    The output is similar to this:
    -->
    輸出類似於此：

    ```
    NAME      STATUS    ROLES    AGE     VERSION        LABELS
    worker0   Ready     <none>   1d      v1.13.0        ...,kubernetes.io/hostname=worker0
    worker1   Ready     <none>   1d      v1.13.0        ...,kubernetes.io/hostname=worker1
    worker2   Ready     <none>   1d      v1.13.0        ...,kubernetes.io/hostname=worker2
    ```
    <!--
    1. Chose one of your nodes, and add a label to it:
    -->
1. 選擇一個節點，給它新增一個標籤：

    ```shell
    kubectl label nodes <your-node-name> disktype=ssd
    ```
    <!--
        where `<your-node-name>` is the name of your chosen node.

    1. Verify that your chosen node has a `disktype=ssd` label:
    -->

    其中 `<your-node-name>` 是你所選節點的名稱。

2. 驗證你所選節點具有 `disktype=ssd` 標籤：

    ```shell
    kubectl get nodes --show-labels
    ```

    <!--
    The output is similar to this:
    -->
    輸出類似於此：

    ```
    NAME      STATUS    ROLES    AGE     VERSION        LABELS
    worker0   Ready     <none>   1d      v1.13.0        ...,disktype=ssd,kubernetes.io/hostname=worker0
    worker1   Ready     <none>   1d      v1.13.0        ...,kubernetes.io/hostname=worker1
    worker2   Ready     <none>   1d      v1.13.0        ...,kubernetes.io/hostname=worker2
    ```

    <!--
        In the preceding output, you can see that the `worker0` node has a
        `disktype=ssd` label.
    -->
    在前面的輸出中，可以看到 `worker0` 節點有一個 `disktype=ssd` 標籤。

<!--
## Schedule a Pod using required node affinity

This manifest describes a Pod that has a `requiredDuringSchedulingIgnoredDuringExecution` node affinity,`disktype: ssd`. 
This means that the pod will get scheduled only on a node that has a `disktype=ssd` label. 
-->
## 依據強制的節點親和性排程 Pod  {#schedule-a-Pod-using-required-node-affinity}

下面清單描述了一個 Pod，它有一個節點親和性配置 `requiredDuringSchedulingIgnoredDuringExecution`，`disktype=ssd`。
這意味著 pod 只會被排程到具有 `disktype=ssd` 標籤的節點上。

{{< codenew file="pods/pod-nginx-required-affinity.yaml" >}}

<!--
1. Apply the manifest to create a Pod that is scheduled onto your
   chosen node:
-->
1. 執行（Apply）此清單來建立一個排程到所選節點上的 Pod：

    ```shell
    kubectl apply -f https://k8s.io/examples/pods/pod-nginx-required-affinity.yaml
    ```

    <!--
    1. Verify that the pod is running on your chosen node:
    -->
1. 驗證 pod 已經在所選節點上執行：

    ```shell
    kubectl get pods --output=wide
    ```

    <!--
    The output is similar to this:
    -->
    輸出類似於此：

    ```
    NAME     READY     STATUS    RESTARTS   AGE    IP           NODE
    nginx    1/1       Running   0          13s    10.200.0.4   worker0
    ```
    
<!--    
## Schedule a Pod using preferred node affinity

This manifest describes a Pod that has a `preferredDuringSchedulingIgnoredDuringExecution` node affinity,`disktype: ssd`. 
This means that the pod will prefer a node that has a `disktype=ssd` label. 
-->
## 使用首選的節點親和性排程 Pod {#schedule-a-Pod-using-preferred-node-affinity}

本清單描述了一個Pod，它有一個節點親和性設定 `preferredDuringSchedulingIgnoredDuringExecution`，`disktype: ssd`。
這意味著 pod 將首選具有 `disktype=ssd` 標籤的節點。

{{< codenew file="pods/pod-nginx-preferred-affinity.yaml" >}}

<!--
1. Apply the manifest to create a Pod that is scheduled onto your
   chosen node:
-->
1. 執行此清單建立一個會排程到所選節點上的 Pod：
    
    ```shell
    kubectl apply -f https://k8s.io/examples/pods/pod-nginx-preferred-affinity.yaml
    ```

    <!--
    1. Verify that the pod is running on your chosen node:
    -->
1. 驗證 pod 是否在所選節點上執行：
   
    ```shell
    kubectl get pods --output=wide
    ```

    <!--
    The output is similar to this:
    -->
    輸出類似於此：
    
    ```
    NAME     READY     STATUS    RESTARTS   AGE    IP           NODE
    nginx    1/1       Running   0          13s    10.200.0.4   worker0
    ```



## {{% heading "whatsnext" %}}

<!--
Learn more about
[Node Affinity](/docs/concepts/scheduling-eviction/assign-pod-node/#node-affinity).
-->
進一步瞭解
[節點親和性](/zh-cn/docs/concepts/scheduling-eviction/assign-pod-node/#node-affinity).
