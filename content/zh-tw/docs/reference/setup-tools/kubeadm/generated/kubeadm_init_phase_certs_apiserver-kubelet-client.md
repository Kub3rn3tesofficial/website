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
Generate the certificate for the API server to connect to kubelet 
-->
生成供 API 伺服器連線 kubelet 的證書

<!-- 
### Synopsis
-->

### 概要

<!--
Generate the certificate for the API server to connect to kubelet, and save them into apiserver-kubelet-client.crt and apiserver-kubelet-client.key files.
-->
生成供 API 伺服器連線 kubelet 的證書，並將其儲存到 apiserver-kubelet-client.crt 和 apiserver-kubelet-client.key 檔案中。

<!--
If both files already exist, kubeadm skips the generation step and existing files will be used.
-->

如果兩個檔案都已存在，則 kubeadm 將跳過生成步驟，使用現有檔案。

<!--
Alpha Disclaimer: this command is currently alpha.
-->

Alpha 免責宣告：此命令當前為 Alpha 功能。

```
kubeadm init phase certs apiserver-kubelet-client [flags]
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
<td colspan="2">
<!--
--cert-dir string&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Default: "/etc/kubernetes/pki"
-->
--cert-dir string&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;預設值："/etc/kubernetes/pki"
</td>
</tr>
<tr>
<td></td><td style="line-height: 130%; word-wrap: break-word;">
<!--
<p>The path where to save and store the certificates.</p>
-->
<p>儲存證書的路徑。</p>
</td>
</tr>

<tr>
<td colspan="2">--config string</td>
</tr>
<tr>
<td></td><td style="line-height: 130%; word-wrap: break-word;">
<!--
<p>Path to a kubeadm configuration file.</p>
-->
<p>kubeadm 配置檔案路徑。</p>
</td>
</tr>

<tr>
<td colspan="2">-h, --help</td>
</tr>
<tr>
<td></td><td style="line-height: 130%; word-wrap: break-word;">
<!--
<p>help for apiserver-kubelet-client</p>
-->
<p>apiserver-kubelet-client 操作的幫助命令</p>
</td>
</tr>

<tr>
<td colspan="2">
<!--
--kubernetes-version string&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Default: "stable-1"
-->
--kubernetes-version string&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;預設值："stable-1"
</td>
</tr>
<tr>
<td></td><td style="line-height: 130%; word-wrap: break-word;">
<!--
<p>Choose a specific Kubernetes version for the control plane.</p>
-->
<p>為控制平面指定特定的 Kubernetes 版本。</p>
</td>
</tr>

</tbody>
</table>

<!--
### Options inherited from parent commands
-->

### 繼承於父命令的選項

   <table style="width: 100%; table-layout: fixed;">
<colgroup>
<col span="1" style="width: 10px;" />
<col span="1" />
</colgroup>
<tbody>

<tr>
<td colspan="2">--rootfs string</td>
</tr>
<tr>
<td></td><td style="line-height: 130%; word-wrap: break-word;">
<!--
<p>[EXPERIMENTAL] The path to the 'real' host root filesystem.</p>
-->
<p>[實驗] 指向宿主機上的 '實際' 根檔案系統的路徑。</p>
</td>
</tr>

</tbody>
</table>

