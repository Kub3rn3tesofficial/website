---
title: kubeadm config
content_type: concept
weight: 50
---

<!-- overview -->
<!--
During `kubeadm init`, kubeadm uploads the `ClusterConfiguration` object to your cluster
in a ConfigMap called `kubeadm-config` in the `kube-system` namespace. This configuration is then read during
`kubeadm join`, `kubeadm reset` and `kubeadm upgrade`.
-->
在 `kubeadm init` 執行期間，kubeadm 將 `ClusterConfiguration` 物件上傳
到你的叢集的 `kube-system` 名字空間下名為 `kubeadm-config` 的 ConfigMap 物件中。
然後在 `kubeadm join`、`kubeadm reset` 和 `kubeadm upgrade` 執行期間讀取此配置。 

<!--
You can use `kubeadm config print` to print the default static configuration that kubeadm
uses for `kubeadm init` and `kubeadm join`.
-->
你可以使用 `kubeadm config print` 命令列印預設靜態配置，
kubeadm 執行 `kubeadm init` and `kubeadm join` 時將使用此配置。

<!-- 
The output of the command is meant to serve as an example. You must manually edit the output
of this command to adapt to your setup. Remove the fields that you are not certain about and kubeadm
will try to default them on runtime by examining the host. 
-->
{{< note >}}
此命令的輸出旨在作為示例。你必須手動編輯此命令的輸出來適配你的設定。
刪除你不確定的欄位，kubeadm 將透過檢查主機來嘗試在執行時給它們設預設值。
{{< /note >}}

<!--
For more information on `init` and `join` navigate to
[Using kubeadm init with a configuration file](/docs/reference/setup-tools/kubeadm/kubeadm-init/#config-file)
or [Using kubeadm join with a configuration file](/docs/reference/setup-tools/kubeadm/kubeadm-join/#config-file).
-->
更多有關 `init` 和 `join` 的資訊請瀏覽[使用帶配置檔案的 kubeadm init](/zh-cn/docs/reference/setup-tools/kubeadm/kubeadm-init/#config-file)
或[使用帶配置檔案的 kubeadm join](/zh-cn/docs/reference/setup-tools/kubeadm/kubeadm-join/#config-file)。

<!--
For more information on using the kubeadm configuration API navigate to
[Customizing components with the kubeadm API](/docs/setup/production-environment/tools/kubeadm/control-plane-flags).
-->
有關使用 kubeadm 的配置 API 的更多資訊，
請瀏覽[使用 kubeadm API 來自定義元件](/zh-cn/docs/setup/production-environment/tools/kubeadm/control-plane-flags)。

<!-- 
You can use `kubeadm config migrate` to convert your old configuration files that contain a deprecated
API version to a newer, supported API version. 
-->
你可以使用 `kubeadm config migrate` 來轉換舊配置檔案，
把其中已棄用的 API 版本更新為受支援的 API 版本。

<!-- 
`kubeadm config images list` and `kubeadm config images pull` can be used to list and pull the images
that kubeadm requires. 
-->
`kubeadm config images list` 和 `kubeadm config images pull` 可以用來列出和拉取 kubeadm 所需的映象。

<!-- body -->
## kubeadm config print {#cmd-config-print}
{{< include "generated/kubeadm_config_print.md" >}}

## kubeadm config print init-defaults {#cmd-config-print-init-defaults}
{{< include "generated/kubeadm_config_print_init-defaults.md" >}}

## kubeadm config print join-defaults {#cmd-config-print-join-defaults}
{{< include "generated/kubeadm_config_print_join-defaults.md" >}}

## kubeadm config migrate {#cmd-config-migrate}
{{< include "generated/kubeadm_config_migrate.md" >}}

## kubeadm config images list {#cmd-config-images-list}
{{< include "generated/kubeadm_config_images_list.md" >}}

## kubeadm config images pull {#cmd-config-images-pull}
{{< include "generated/kubeadm_config_images_pull.md" >}}

## {{% heading "whatsnext" %}}

<!--
* [kubeadm upgrade](/docs/reference/setup-tools/kubeadm/kubeadm-upgrade/) to upgrade a Kubernetes cluster to a newer version
-->

* [kubeadm upgrade](/zh-cn/docs/reference/setup-tools/kubeadm/kubeadm-upgrade/)
  將 Kubernetes 叢集升級到更新版本 [kubeadm upgrade]


