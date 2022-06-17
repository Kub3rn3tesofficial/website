---
title: 在叢集級別應用 Pod 安全標準
content_type: tutorial
weight: 10
---
<!-- 
title: Apply Pod Security Standards at the Cluster Level
content_type: tutorial
weight: 10
-->

{{% alert title="Note" %}}
<!-- This tutorial applies only for new clusters. -->
本教程僅適用於新叢集。
{{% /alert %}}

<!-- 
Pod Security admission (PSA) is enabled by default in v1.23 and later, as it has
[graduated to beta](/blog/2021/12/09/pod-security-admission-beta/).
Pod Security
is an admission controller that carries out checks against the Kubernetes
[Pod Security Standards](/docs/concepts/security/pod-security-standards/) when new pods are
created. This tutorial shows you how to enforce the `baseline` Pod Security
Standard at the cluster level which applies a standard configuration
to all namespaces in a cluster.

To apply Pod Security Standards to specific namespaces, refer to [Apply Pod Security Standards at the namespace level](/docs/tutorials/security/ns-level-pss).
-->
Pod 安全准入（PSA）在 v1.23 及更高版本預設啟用，
因為它[升級到測試版（beta）](/blog/2021/12/09/pod-security-admission-beta/)。
Pod 安全准入是在建立 Pod 時應用
[Pod 安全標準](/zh-cn/docs/concepts/security/pod-security-standards/)的准入控制器。
本教程將向你展示如何在叢集級別實施 `baseline` Pod 安全標準，
該標準將標準配置應用於叢集中的所有名稱空間。

要將 Pod 安全標準應用於特定名字空間，
請參閱[在名字空間級別應用 Pod 安全標準](/zh-cn/docs/tutorials/security/ns-level-pss)。

## {{% heading "prerequisites" %}}
<!-- 
Install the following on your workstation:

- [KinD](https://kind.sigs.k8s.io/docs/user/quick-start/#installation)
- [kubectl](https://kubernetes.io/docs/tasks/tools/)
-->
在你的工作站中安裝以下內容：

- [KinD](https://kind.sigs.k8s.io/docs/user/quick-start/#installation)
- [kubectl](https://kubernetes.io/docs/tasks/tools/)

<!--
## Choose the right Pod Security Standard to apply

[Pod Security Admission](/docs/concepts/security/pod-security-admission/)
lets you apply built-in [Pod Security Standards](/docs/concepts/security/pod-security-standards/)
with the following modes: `enforce`, `audit`, and `warn`.

To gather information that helps you to choose the Pod Security Standards
that are most appropriate for your configuration, do the following: 
-->
## 正確選擇要應用的 Pod 安全標準  {#choose-the-right-pod-security-standard-to-apply}

[Pod 安全准入](/zh-cn/docs/concepts/security/pod-security-admission/)
允許你使用以下模式應用內建的
[Pod 安全標準](/zh-cn/docs/concepts/security/pod-security-standards/):
`enforce`、`audit` 和 `warn`。

要收集資訊以便選擇最適合你的配置的 Pod 安全標準，請執行以下操作：

<!-- 
1. Create a cluster with no Pod Security Standards applied:
 -->
1. 建立一個沒有應用 Pod 安全標準的叢集：

   ```shell
   kind create cluster --name psa-wo-cluster-pss --image kindest/node:v1.23.0
   ```
   <!-- The output is similar to this: -->
   輸出類似於：
   ```
   Creating cluster "psa-wo-cluster-pss" ...
   ✓ Ensuring node image (kindest/node:v1.23.0) 🖼
   ✓ Preparing nodes 📦  
   ✓ Writing configuration 📜
   ✓ Starting control-plane 🕹️
   ✓ Installing CNI 🔌
   ✓ Installing StorageClass 💾
   Set kubectl context to "kind-psa-wo-cluster-pss"
   You can now use your cluster with:
   
   kubectl cluster-info --context kind-psa-wo-cluster-pss
   
   Thanks for using kind! 😊
   
   ```

<!-- 
1. Set the kubectl context to the new cluster:
-->
2. 將 kubectl 上下文設定為新叢集：

   ```shell
   kubectl cluster-info --context kind-psa-wo-cluster-pss
   ```
   <!-- The output is similar to this: -->
   輸出類似於：

   ```
   Kubernetes control plane is running at https://127.0.0.1:61350 
   CoreDNS is running at https://127.0.0.1:61350/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy
   
   To further debug and diagnose cluster problems, use 'kubectl cluster-info dump'.
   ```

<!-- 
1. Get a list of namespaces in the cluster:
-->
3. 獲取叢集中的名字空間列表：

   ```shell
   kubectl get ns
   ```
   <!-- The output is similar to this: -->
   輸出類似於：
   ```      
   NAME                 STATUS   AGE
   default              Active   9m30s
   kube-node-lease      Active   9m32s
   kube-public          Active   9m32s
   kube-system          Active   9m32s
   local-path-storage   Active   9m26s
   ```

<!-- 
1. Use `--dry-run=server` to understand what happens when different Pod Security Standards
   are applied:
 -->
4. 使用 `--dry-run=server` 來了解應用不同的 Pod 安全標準時會發生什麼：

   1. Privileged
      ```shell
      kubectl label --dry-run=server --overwrite ns --all \                    
      pod-security.kubernetes.io/enforce=privileged
      ```
      <!-- The output is similar to this: -->
      輸出類似於：
       ```      
       namespace/default labeled
       namespace/kube-node-lease labeled
       namespace/kube-public labeled
       namespace/kube-system labeled
       namespace/local-path-storage labeled
       ```
   2. Baseline
      ```shell    
      kubectl label --dry-run=server --overwrite ns --all \
      pod-security.kubernetes.io/enforce=baseline
      ```
      <!-- The output is similar to this: -->
      輸出類似於：
      ```   
      namespace/default labeled
      namespace/kube-node-lease labeled
      namespace/kube-public labeled
      Warning: existing pods in namespace "kube-system" violate the new PodSecurity enforce level "baseline:latest"
      Warning: etcd-psa-wo-cluster-pss-control-plane (and 3 other pods): host namespaces, hostPath volumes
      Warning: kindnet-vzj42: non-default capabilities, host namespaces, hostPath volumes
      Warning: kube-proxy-m6hwf: host namespaces, hostPath volumes, privileged
      namespace/kube-system labeled
      namespace/local-path-storage labeled
      ```   

   3. Restricted
      ```shell
      kubectl label --dry-run=server --overwrite ns --all \
      pod-security.kubernetes.io/enforce=restricted
      ```
      <!-- The output is similar to this: -->
      輸出類似於：
      ```   
      namespace/default labeled
      namespace/kube-node-lease labeled
      namespace/kube-public labeled
      Warning: existing pods in namespace "kube-system" violate the new PodSecurity enforce level "restricted:latest"
      Warning: coredns-7bb9c7b568-hsptc (and 1 other pod): unrestricted capabilities, runAsNonRoot != true, seccompProfile
      Warning: etcd-psa-wo-cluster-pss-control-plane (and 3 other pods): host namespaces, hostPath volumes, allowPrivilegeEscalation != false, unrestricted capabilities, restricted volume types, runAsNonRoot != true
      Warning: kindnet-vzj42: non-default capabilities, host namespaces, hostPath volumes, allowPrivilegeEscalation != false, unrestricted capabilities, restricted volume types, runAsNonRoot != true, seccompProfile
      Warning: kube-proxy-m6hwf: host namespaces, hostPath volumes, privileged, allowPrivilegeEscalation != false, unrestricted capabilities, restricted volume types, runAsNonRoot != true, seccompProfile
      namespace/kube-system labeled
      Warning: existing pods in namespace "local-path-storage" violate the new PodSecurity enforce level "restricted:latest"
      Warning: local-path-provisioner-d6d9f7ffc-lw9lh: allowPrivilegeEscalation != false, unrestricted capabilities, runAsNonRoot != true, seccompProfile
      namespace/local-path-storage labeled
      ```

<!-- 
From the previous output, you'll notice that applying the `privileged` Pod Security Standard shows no warnings
for any namespaces. However, `baseline` and `restricted` standards both have
warnings, specifically in the `kube-system` namespace.
-->
從前面的輸出中，你會注意到應用 `privileged` Pod 安全標準不會顯示任何名字空間的警告。
然而，`baseline` 和 `restricted` 標準都有警告，特別是在 `kube-system` 名字空間中。

<!-- 
## Set modes, versions and standards

In this section, you apply the following Pod Security Standards to the `latest` version:

* `baseline` standard in `enforce` mode.
* `restricted` standard in `warn` and `audit` mode.
-->
## 設定模式、版本和標準  {#set-modes-versions-and-standards}

在本節中，你將以下 Pod 安全標準應用於最新（`latest`）版本：

* 在 `enforce` 模式下的 `baseline` 標準。
* `warn` 和 `audit` 模式下的 `restricted` 標準。

<!-- 
The `baseline` Pod Security Standard provides a convenient
middle ground that allows keeping the exemption list short and prevents known
privilege escalations.

Additionally, to prevent pods from failing in `kube-system`, you'll exempt the namespace
from having Pod Security Standards applied.

When you implement Pod Security Admission in your own environment, consider the
following:
-->
`baseline` Pod 安全標準提供了一個方便的中間立場，能夠保持豁免列表簡短並防止已知的特權升級。

此外，為了防止 `kube-system` 中的 Pod 失敗，你將免除該名字空間應用 Pod 安全標準。

在你自己的環境中實施 Pod 安全准入時，請考慮以下事項：

<!-- 
1. Based on the risk posture applied to a cluster, a stricter Pod Security
   Standard like `restricted` might be a better choice.
1. Exempting the `kube-system` namespace allows pods to run as
   `privileged` in this namespace. For real world use, the Kubernetes project
   strongly recommends that you apply strict RBAC
   policies that limit access to `kube-system`, following the principle of least
   privilege.
   To implement the preceding standards, do the following:
1. Create a configuration file that can be consumed by the Pod Security
   Admission Controller to implement these Pod Security Standards:
-->
1. 根據應用於叢集的風險狀況，更嚴格的 Pod 安全標準（如 `restricted`）可能是更好的選擇。
2. 對 `kube-system` 名字空間進行赦免會允許 Pod 在其中以 `privileged` 模式執行。
   對於實際使用，Kubernetes 專案強烈建議你應用嚴格的 RBAC 策略來限制對 `kube-system` 的訪問，
   遵循最小特權原則。
3. 建立一個配置檔案，Pod 安全准入控制器可以使用該檔案來實現這些 Pod 安全標準：

   ```
   mkdir -p /tmp/pss
   cat <<EOF > /tmp/pss/cluster-level-pss.yaml 
   apiVersion: apiserver.config.k8s.io/v1
   kind: AdmissionConfiguration
   plugins:
   - name: PodSecurity
     configuration:
       apiVersion: pod-security.admission.config.k8s.io/v1beta1
       kind: PodSecurityConfiguration
       defaults:
         enforce: "baseline"
         enforce-version: "latest"
         audit: "restricted"
         audit-version: "latest"
         warn: "restricted"
         warn-version: "latest"
       exemptions:
         usernames: []
         runtimeClasses: []
         namespaces: [kube-system]
   EOF
   ```

<!-- 
1. Configure the API server to consume this file during cluster creation:
-->
4. 在建立叢集時配置 API 伺服器使用此檔案：

   ```
   cat <<EOF > /tmp/pss/cluster-config.yaml 
   kind: Cluster
   apiVersion: kind.x-k8s.io/v1alpha4
   nodes:
   - role: control-plane
     kubeadmConfigPatches:
     - |
       kind: ClusterConfiguration
       apiServer:
           extraArgs:
             admission-control-config-file: /etc/config/cluster-level-pss.yaml
           extraVolumes:
             - name: accf
               hostPath: /etc/config
               mountPath: /etc/config
               readOnly: false
               pathType: "DirectoryOrCreate"
     extraMounts:
     - hostPath: /tmp/pss
       containerPath: /etc/config
       # optional: if set, the mount is read-only.
       # default false
       readOnly: false
       # optional: if set, the mount needs SELinux relabeling.
       # default false
       selinuxRelabel: false
       # optional: set propagation mode (None, HostToContainer or Bidirectional)
       # see https://kubernetes.io/docs/concepts/storage/volumes/#mount-propagation
       # default None
       propagation: None
   EOF
   ```

   {{<note>}}
   <!-- 
   If you use Docker Desktop with KinD on macOS, you can
   add `/tmp` as a Shared Directory under the menu item
   **Preferences > Resources > File Sharing**.
   -->
   如果你在 macOS 上使用 Docker Desktop 和 KinD，
   你可以在選單項 **Preferences > Resources > File Sharing**
   下新增 `/tmp` 作為共享目錄。
   {{</note>}}

<!-- 
1. Create a cluster that uses Pod Security Admission to apply
   these Pod Security Standards:
-->
5. 建立一個使用 Pod 安全准入的叢集來應用這些 Pod 安全標準：

   ```shell
   kind create cluster --name psa-with-cluster-pss --image kindest/node:v1.23.0 --config /tmp/pss/cluster-config.yaml
   ```
   <!-- The output is similar to this: -->
   輸出類似於：
   ```
   Creating cluster "psa-with-cluster-pss" ...
    ✓ Ensuring node image (kindest/node:v1.23.0) 🖼 
    ✓ Preparing nodes 📦  
    ✓ Writing configuration 📜 
    ✓ Starting control-plane 🕹️ 
    ✓ Installing CNI 🔌 
    ✓ Installing StorageClass 💾 
   Set kubectl context to "kind-psa-with-cluster-pss"
   You can now use your cluster with:
   
   kubectl cluster-info --context kind-psa-with-cluster-pss
   
   Have a question, bug, or feature request? Let us know! https://kind.sigs.k8s.io/#community 🙂
   ```

<!-- 
1. Point kubectl to the cluster
-->
6. 將 kubectl 指向叢集

   ```shell
   kubectl cluster-info --context kind-psa-with-cluster-pss
   ```
   <!-- The output is similar to this: -->
   輸出類似於：
   ```
   Kubernetes control plane is running at https://127.0.0.1:63855
   CoreDNS is running at https://127.0.0.1:63855/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy
 
   To further debug and diagnose cluster problems, use 'kubectl cluster-info dump'.
   ```

<!-- 
1. Create the following Pod specification for a minimal configuration in the default namespace:
-->
7. 建立以下 Pod 規約作為在 default 名字空間中的一個最小配置：

   ```
   cat <<EOF > /tmp/pss/nginx-pod.yaml
   apiVersion: v1
   kind: Pod
   metadata:
     name: nginx
   spec:
     containers:
       - image: nginx
         name: nginx
         ports:
           - containerPort: 80
   EOF
   ```

<!-- 
1. Create the Pod in the cluster:
-->
8. 在叢集中建立 Pod：

   ```shell
   kubectl apply -f /tmp/pss/nginx-pod.yaml
   ```
   <!-- The output is similar to this: -->
   輸出類似於：
   ```
   Warning: would violate PodSecurity "restricted:latest": allowPrivilegeEscalation != false (container "nginx" must set securityContext allowPrivilegeEscalation=false), unrestricted capabilities (container "nginx" must set securityContext.capabilities.drop=["ALL"]), runAsNonRoot != true (pod or container "nginx" must set securityContext.runAsNonRoot=true), seccompProfile (pod or container "nginx" must set securityContext seccompProfile.type to "RuntimeDefault" or "Localhost")
   pod/nginx created
   ```

<!-- 
## Clean up

Run `kind delete cluster --name psa-with-cluster-pss` and
`kind delete cluster --name psa-wo-cluster-pss` to delete the clusters you
created.
-->
## 清理  {#clean-up}

執行 `kind delete cluster --name psa-with-cluster-pss` 和
`kind delete cluster --name psa-wo-cluster-pss` 來刪除你建立的叢集。

## {{% heading "whatsnext" %}}

<!-- 
- Run a
  [shell script](/examples/security/kind-with-cluster-level-baseline-pod-security.sh)
  to perform all the preceding steps at once:
  1. Create a Pod Security Standards based cluster level Configuration
  2. Create a file to let API server consumes this configuration
  3. Create a cluster that creates an API server with this configuration
  4. Set kubectl context to this new cluster
  5. Create a minimal pod yaml file
  6. Apply this file to create a Pod in the new cluster
- [Pod Security Admission](/docs/concepts/security/pod-security-admission/)
- [Pod Security Standards](/docs/concepts/security/pod-security-standards/)
- [Apply Pod Security Standards at the namespace level](/docs/tutorials/security/ns-level-pss/)
-->
- 執行一個 [shell 指令碼](/zh-cn/examples/security/kind-with-cluster-level-baseline-pod-security.sh)
  一次執行前面的所有步驟：
  1. 建立一個基於 Pod 安全標準的叢集級別配置
  2. 建立一個檔案讓 API 伺服器消費這個配置
  3. 建立一個叢集，用這個配置建立一個 API 伺服器
  4. 設定 kubectl 上下文為這個新叢集
  5. 建立一個最小的 Pod yaml 檔案
  6. 應用這個檔案，在新叢集中建立一個 Pod
- [Pod 安全准入](/zh-cn/docs/concepts/security/pod-security-admission/)
- [Pod 安全標準](/zh-cn/docs/concepts/security/pod-security-standards/)
- [在名字空間級別應用 Pod 安全標準](/zh-cn/docs/tutorials/security/ns-level-pss/)