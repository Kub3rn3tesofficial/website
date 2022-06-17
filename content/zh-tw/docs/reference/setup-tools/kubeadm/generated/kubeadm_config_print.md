<!--
The file is auto-generated from the Go source code of the component using a generic
[generator](https://github.com/kubernetes-sigs/reference-docs/). To learn how
to generate the reference documentation, please read
[Contributing to the reference documentation](/docs/contribute/generate-ref-docs/).
To update the reference conent, please follow the 
[Contributing upstream](/docs/contribute/generate-ref-docs/contribute-upstream/)
guide. You can file document formatting bugs against the
[reference-docs](https://github.com/kubernetes-sigs/reference-docs/) project.
-->

<!--
Print configuration
-->
列印配置

<!--
### Synopsis
-->
### 概要

<!--
This command prints configurations for subcommands provided.
For details, see: https://pkg.go.dev/k8s.io/kubernetes/cmd/kubeadm/app/apis/kubeadm#section-directories
-->
此命令列印子命令所提供的配置資訊。
相關細節可參閱: https://pkg.go.dev/k8s.io/kubernetes/cmd/kubeadm/app/apis/kubeadm#section-directories

```
kubeadm config print [flags]
```

<!--
### Options
-->
### 選項

   <table style="width: 100%; table-layout: fixed;">
<colgroup>
<col span="1" style="width: 10px;" />
<col span="1" />
</colgroup>
<tbody>

<tr>
<td colspan="2">-h, --help</td>
</tr>
<tr>
<td></td><td style="line-height: 130%; word-wrap: break-word;"><p><!--help for print-->print 命令的幫助資訊</p></td>
</tr>

</tbody>
</table>


<!--
### Options inherited from parent commands
-->
### 從父命令繼承而來的選項

   <table style="width: 100%; table-layout: fixed;">
<colgroup>
<col span="1" style="width: 10px;" />
<col span="1" />
</colgroup>
<tbody>

<tr>
<td colspan="2">--kubeconfig string&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<!--Default:-->預設值："/etc/kubernetes/admin.conf"</td>
</tr>
<tr>
<!--td></td><td style="line-height: 130%; word-wrap: break-word;"><p>The kubeconfig file to use when talking to the cluster. If the flag is not set, a set of standard locations can be searched for an existing kubeconfig file.</p></td -->
<td></td><td style="line-height: 130%; word-wrap: break-word;"><p>與叢集通訊時使用的 kubeconfig 檔案。如此標誌未設定，將在一組標準位置中搜索現有的kubeconfig 檔案。</p></td>
</tr>

<tr>
<td colspan="2">--rootfs string</td>
</tr>
<tr>
<!--td></td><td style="line-height: 130%; word-wrap: break-word;"><p>[EXPERIMENTAL] The path to the 'real' host root filesystem.</p></td-->
<td></td><td style="line-height: 130%; word-wrap: break-word;"><p>[試驗性] 指向“真實”宿主根檔案系統的路徑。</p></td>
</tr>

</tbody>
</table>



