---
title: 使用 crictl 對 Kubernetes 節點進行除錯
content_type: task
weight: 30
---
<!--
reviewers:
- Random-Liu
- feiskyer
- mrunalp
title: Debugging Kubernetes nodes with crictl
content_type: task
weight: 30
-->

<!-- overview -->

{{< feature-state for_k8s_version="v1.11" state="stable" >}}

<!--
`crictl` is a command-line interface for CRI-compatible container runtimes.
You can use it to inspect and debug container runtimes and applications on a
Kubernetes node. `crictl` and its source are hosted in the
[cri-tools](https://github.com/kubernetes-sigs/cri-tools) repository.
-->

`crictl` 是 CRI 相容的容器執行時命令列介面。
你可以使用它來檢查和除錯 Kubernetes 節點上的容器執行時和應用程式。
`crictl` 和它的原始碼在
[cri-tools](https://github.com/kubernetes-sigs/cri-tools) 程式碼庫。

## {{% heading "prerequisites" %}}

<!--
`crictl` requires a Linux operating system with a CRI runtime.
-->
`crictl` 需要帶有 CRI 執行時的 Linux 作業系統。

<!-- steps -->

<!--
## Installing crictl

You can download a compressed archive `crictl` from the cri-tools
[release page](https://github.com/kubernetes-sigs/cri-tools/releases), for several
different architectures. Download the version that corresponds to your version
of Kubernetes. Extract it and move it to a location on your system path, such as
`/usr/local/bin/`.
-->
## 安裝 crictl    {#installing-crictl}

你可以從 cri-tools [釋出頁面](https://github.com/kubernetes-sigs/cri-tools/releases)
下載一個壓縮的 `crictl` 歸檔檔案，用於幾種不同的架構。
下載與你的 kubernetes 版本相對應的版本。
提取它並將其移動到系統路徑上的某個位置，例如`/usr/local/bin/`。

<!--
## General usage

The `crictl` command has several subcommands and runtime flags. Use
`crictl help` or `crictl <subcommand> help` for more details.
-->
## 一般用法    {#general-usage}

`crictl` 命令有幾個子命令和執行時引數。
有關詳細資訊，請使用 `crictl help` 或 `crictl <subcommand> help` 獲取幫助資訊。

<!--
You can set the endpoint for `crictl` by doing one of the following:
-->
你可以用以下方法之一來為 `crictl` 設定端點：

<!--
* Set the `--runtime-endpoint` and `--image-endpoint` flags.
* Set the `CONTAINER_RUNTIME_ENDPOINT` and `IMAGE_SERVICE_ENDPOINT` environment
  variables.
* Set the endpoint in the configuration file `/etc/crictl.yaml`. To specify a
  different file, use the `--config=PATH_TO_FILE` flag when you run `crictl`.
-->
- 設定引數 `--runtime-endpoint` 和 `--image-endpoint`。
- 設定環境變數 `CONTAINER_RUNTIME_ENDPOINT` 和 `IMAGE_SERVICE_ENDPOINT`。
- 在配置檔案 `--config=/etc/crictl.yaml` 中設定端點。
  要設定不同的檔案，可以在執行 `crictl` 時使用 `--config=PATH_TO_FILE` 標誌。

<!--
You can also specify timeout values when connecting to the server and enable or
disable debugging, by specifying `timeout` or `debug` values in the configuration
file or using the `--timeout` and `--debug` command-line flags.
-->
你還可以在連線到伺服器並啟用或禁用除錯時指定超時值，方法是在配置檔案中指定
`timeout` 或 `debug` 值，或者使用 `--timeout` 和 `--debug` 命令列引數。

<!--
To view or edit the current configuration, view or edit the contents of
`/etc/crictl.yaml`. For example, the configuration when using the `containerd`
container runtime would be similar to this:
-->
要檢視或編輯當前配置，請檢視或編輯 `/etc/crictl.yaml` 的內容。
例如，使用 `containerd` 容器執行時的配置會類似於這樣：

```
runtime-endpoint: unix:///var/run/containerd/containerd.sock
image-endpoint: unix:///var/run/containerd/containerd.sock
timeout: 10
debug: true
```

<!--
To learn more about `crictl`, refer to the [`crictl`
documentation](https://github.com/kubernetes-sigs/cri-tools/blob/master/docs/crictl.md).
-->
要進一步瞭解 `crictl`，參閱
[`crictl` 文件](https://github.com/kubernetes-sigs/cri-tools/blob/master/docs/crictl.md)。

<!--
## Example crictl commands

The following examples show some `crictl` commands and example output.
-->
## crictl 命令示例    {#example-crictl-commands}

{{< warning >}}
<!--
If you use `crictl` to create pod sandboxes or containers on a running
Kubernetes cluster, the Kubelet will eventually delete them. `crictl` is not a
general purpose workflow tool, but a tool that is useful for debugging.
-->
如果使用 `crictl` 在正在執行的 Kubernetes 叢集上建立 Pod 沙盒或容器，
kubelet 最終將刪除它們。
`crictl` 不是一個通用的工作流工具，而是一個對除錯有用的工具。
{{< /warning >}}

<!--
### List pods

List all pods:
-->
### 列印 Pod 清單    {#list-pods}

列印所有 Pod 的清單：

```shell
crictl pods
```

<!--
The output is similar to this:
-->
輸出類似於：

```
POD ID              CREATED              STATE               NAME                         NAMESPACE           ATTEMPT
926f1b5a1d33a       About a minute ago   Ready               sh-84d7dcf559-4r2gq          default             0
4dccb216c4adb       About a minute ago   Ready               nginx-65899c769f-wv2gp       default             0
a86316e96fa89       17 hours ago         Ready               kube-proxy-gblk4             kube-system         0
919630b8f81f1       17 hours ago         Ready               nvidia-device-plugin-zgbbv   kube-system         0
```

<!--
List pods by name:
-->
根據名稱列印 Pod 清單：

```shell
crictl pods --name nginx-65899c769f-wv2gp
```

<!--
The output is similar to this:
-->
輸出類似於這樣：

```
POD ID              CREATED             STATE               NAME                     NAMESPACE           ATTEMPT
4dccb216c4adb       2 minutes ago       Ready               nginx-65899c769f-wv2gp   default             0
```

<!--
List pods by label:
-->
根據標籤列印 Pod 清單：

```shell
crictl pods --label run=nginx
```

<!--
The output is similar to this:
-->
輸出類似於這樣：

```none
POD ID              CREATED             STATE               NAME                     NAMESPACE           ATTEMPT
4dccb216c4adb       2 minutes ago       Ready               nginx-65899c769f-wv2gp   default             0
```

<!--
### List images

List all images:
-->
### 列印映象清單    {#list-containers}

列印所有映象清單：

```shell
crictl images
```

<!--
The output is similar to this:
-->
輸出類似於這樣：

```none
IMAGE                                     TAG                 IMAGE ID            SIZE
busybox                                   latest              8c811b4aec35f       1.15MB
k8s-gcrio.azureedge.net/hyperkube-amd64   v1.10.3             e179bbfe5d238       665MB
k8s-gcrio.azureedge.net/pause-amd64       3.1                 da86e6ba6ca19       742kB
nginx                                     latest              cd5239a0906a6       109MB
```

<!--
List images by repository:
-->
根據倉庫列印映象清單：

```shell
crictl images nginx
```

<!--
The output is similar to this:
-->
輸出類似於這樣：

```none
IMAGE               TAG                 IMAGE ID            SIZE
nginx               latest              cd5239a0906a6       109MB
```

<!--
Only list image IDs:
-->
只打印映象 ID：

```shell
crictl images -q
```

<!--
The output is similar to this:
-->
輸出類似於這樣：

```none
sha256:8c811b4aec35f259572d0f79207bc0678df4c736eeec50bc9fec37ed936a472a
sha256:e179bbfe5d238de6069f3b03fccbecc3fb4f2019af741bfff1233c4d7b2970c5
sha256:da86e6ba6ca197bf6bc5e9d900febd906b133eaa4750e6bed647b0fbe50ed43e
sha256:cd5239a0906a6ccf0562354852fae04bc5b52d72a2aff9a871ddb6bd57553569
```

<!--
### List containers

List all containers:
-->
### 列印容器清單    {#list-containers}

列印所有容器清單：

```shell
crictl ps -a
```

<!--
The output is similar to this:
-->
輸出類似於這樣：

```none
CONTAINER ID        IMAGE                                                                                                             CREATED             STATE               NAME                       ATTEMPT
1f73f2d81bf98       busybox@sha256:141c253bc4c3fd0a201d32dc1f493bcf3fff003b6df416dea4f41046e0f37d47                                   7 minutes ago       Running             sh                         1
9c5951df22c78       busybox@sha256:141c253bc4c3fd0a201d32dc1f493bcf3fff003b6df416dea4f41046e0f37d47                                   8 minutes ago       Exited              sh                         0
87d3992f84f74       nginx@sha256:d0a8828cccb73397acb0073bf34f4d7d8aa315263f1e7806bf8c55d8ac139d5f                                     8 minutes ago       Running             nginx                      0
1941fb4da154f       k8s-gcrio.azureedge.net/hyperkube-amd64@sha256:00d814b1f7763f4ab5be80c58e98140dfc69df107f253d7fdd714b30a714260a   18 hours ago        Running             kube-proxy                 0
```

<!--
List running containers:
-->
列印正在執行的容器清單：

```shell
crictl ps
```

<!--
The output is similar to this:
-->
輸出類似於這樣：

```none
CONTAINER ID        IMAGE                                                                                                             CREATED             STATE               NAME                       ATTEMPT
1f73f2d81bf98       busybox@sha256:141c253bc4c3fd0a201d32dc1f493bcf3fff003b6df416dea4f41046e0f37d47                                   6 minutes ago       Running             sh                         1
87d3992f84f74       nginx@sha256:d0a8828cccb73397acb0073bf34f4d7d8aa315263f1e7806bf8c55d8ac139d5f                                     7 minutes ago       Running             nginx                      0
1941fb4da154f       k8s-gcrio.azureedge.net/hyperkube-amd64@sha256:00d814b1f7763f4ab5be80c58e98140dfc69df107f253d7fdd714b30a714260a   17 hours ago        Running             kube-proxy                 0
```

<!--
### Execute a command in a running container
-->
### 在正在執行的容器上執行命令    {#execute-a-command-in-a-running-container}

```shell
crictl exec -i -t 1f73f2d81bf98 ls
```

<!--
The output is similar to this:
-->
輸出類似於這樣：

```none
bin   dev   etc   home  proc  root  sys   tmp   usr   var
```

<!--
### Get a container's logs

Get all container logs:
-->
### 獲取容器日誌    {#get-a-container-s-logs}

獲取容器的所有日誌：

```shell
crictl logs 87d3992f84f74
```

<!--
The output is similar to this:
-->
輸出類似於這樣：

```none
10.240.0.96 - - [06/Jun/2018:02:45:49 +0000] "GET / HTTP/1.1" 200 612 "-" "curl/7.47.0" "-"
10.240.0.96 - - [06/Jun/2018:02:45:50 +0000] "GET / HTTP/1.1" 200 612 "-" "curl/7.47.0" "-"
10.240.0.96 - - [06/Jun/2018:02:45:51 +0000] "GET / HTTP/1.1" 200 612 "-" "curl/7.47.0" "-"
```

<!--
Get only the latest `N` lines of logs:
-->
獲取最近的 `N` 行日誌：

```shell
crictl logs --tail=1 87d3992f84f74
```

<!--
The output is similar to this:
-->
輸出類似於這樣：

```none
10.240.0.96 - - [06/Jun/2018:02:45:51 +0000] "GET / HTTP/1.1" 200 612 "-" "curl/7.47.0" "-"
```

<!--
### Run a pod sandbox

Using `crictl` to run a pod sandbox is useful for debugging container runtimes.
On a running Kubernetes cluster, the sandbox will eventually be stopped and
deleted by the Kubelet.
-->
### 執行 Pod 沙盒    {#run-a-pod-sandbox}

用 `crictl` 執行 Pod 沙盒對容器執行時排錯很有幫助。
在執行的 Kubernetes 叢集中，沙盒會隨機地被 kubelet 停止和刪除。

<!--
1. Create a JSON file like the following:
-->
1. 編寫下面的 JSON 檔案：

   ```json
   {
       "metadata": {
           "name": "nginx-sandbox",
           "namespace": "default",
           "attempt": 1,
           "uid": "hdishd83djaidwnduwk28bcsb"
       },
       "logDirectory": "/tmp",
       "linux": {
       }
   }
   ```

<!--
2. Use the `crictl runp` command to apply the JSON and run the sandbox.
-->
2. 使用 `crictl runp` 命令應用 JSON 檔案並執行沙盒。

   ```shell
   crictl runp pod-config.json
   ```

   <!--
   The ID of the sandbox is returned.
   -->
   返回了沙盒的 ID。

<!--
### Create a container

Using `crictl` to create a container is useful for debugging container runtimes.
On a running Kubernetes cluster, the sandbox will eventually be stopped and
deleted by the Kubelet.
-->
### 建立容器 {#create-a-container}

用 `crictl` 建立容器對容器執行時排錯很有幫助。
在執行的 Kubernetes 叢集中，沙盒會隨機的被 kubelet 停止和刪除。

<!--
1. Pull a busybox image
-->
1. 拉取 busybox 映象

   ```shell
   crictl pull busybox
   ```
   ```none
   Image is up to date for busybox@sha256:141c253bc4c3fd0a201d32dc1f493bcf3fff003b6df416dea4f41046e0f37d47
   ```

<!--
2. Create configs for the pod and the container:
-->
2. 建立 Pod 和容器的配置：

   <!--
   **Pod config**:
   -->
   **Pod 配置**：

   ```json
   {
       "metadata": {
           "name": "nginx-sandbox",
           "namespace": "default",
           "attempt": 1,
           "uid": "hdishd83djaidwnduwk28bcsb"
       },
       "log_directory": "/tmp",
       "linux": {
       }
   }
   ```

   <!--
   **Container config**:
   -->
   **容器配置**：

   ```json
   {
     "metadata": {
       "name": "busybox"
     },
     "image":{
       "image": "busybox"
     },
     "command": [
       "top"
     ],
     "log_path":"busybox.log",
     "linux": {
     }
   }
   ```

<!--
3. Create the container, passing the ID of the previously-created pod, the
   container config file, and the pod config file. The ID of the container is
   returned.
-->
3. 建立容器，傳遞先前建立的 Pod 的 ID、容器配置檔案和 Pod 配置檔案。返回容器的 ID。

   ```bash
   crictl create f84dd361f8dc51518ed291fbadd6db537b0496536c1d2d6c05ff943ce8c9a54f container-config.json pod-config.json
   ```

<!--
4. List all containers and verify that the newly-created container has its
   state set to `Created`.
-->
4. 查詢所有容器並確認新建立的容器狀態為 `Created`。

   ```bash
   crictl ps -a
   ```
   <!--
   The output is similar to this:
   -->
   輸出類似於這樣：
 
   ```none
   CONTAINER ID        IMAGE               CREATED             STATE               NAME                ATTEMPT
   3e025dd50a72d       busybox             32 seconds ago      Created             busybox             0
   ```

<!--
### Start a container

To start a container, pass its ID to `crictl start`:
-->
### 啟動容器 {#start-a-container}

要啟動容器，要將容器 ID 傳給 `crictl start`：

```shell
crictl start 3e025dd50a72d956c4f14881fbb5b1080c9275674e95fb67f965f6478a957d60
```

<!--
The output is similar to this:
-->
輸出類似於這樣：

```
3e025dd50a72d956c4f14881fbb5b1080c9275674e95fb67f965f6478a957d60
```

<!--
Check the container has its state set to `Running`.
-->
確認容器的狀態為 `Running`。

```shell
crictl ps
```

<!--
The output is similar to this:
-->
輸出類似於這樣：

```
CONTAINER ID   IMAGE    CREATED              STATE    NAME     ATTEMPT
3e025dd50a72d  busybox  About a minute ago   Running  busybox  0
```

## {{% heading "whatsnext" %}}

<!--
* [Learn more about `crictl`](https://github.com/kubernetes-sigs/cri-tools).
* [Map `docker` CLI commands to `crictl`](/docs/reference/tools/map-crictl-dockercli/).
-->
* [進一步瞭解 `crictl`](https://github.com/kubernetes-sigs/cri-tools)
* [將 `docker` CLI 命令對映到 `crictl`](/zh-cn/docs/reference/tools/map-crictl-dockercli/)
