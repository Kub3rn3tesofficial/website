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
Generate the certificate for serving the Kubernetes API 
-->
生成用於服務 Kubernetes API 的證書

<!--
### Synopsis
-->

### 概要

<!--
Generate the certificate for serving the Kubernetes API, and save them into apiserver.crt and apiserver.key files.
-->
生成用於服務 Kubernetes API 的證書，並將其儲存到 apiserver.crt 和 apiserver.key 檔案中。


<!--
If both files already exist, kubeadm skips the generation step and existing files will be used.
-->

如果兩個檔案都已存在，則 kubeadm 將跳過生成步驟，使用現有檔案。

<!--
Alpha Disclaimer: this command is currently alpha.
-->

Alpha 免責宣告：此命令當前為 Alpha 功能。

```
kubeadm init phase certs apiserver [flags]
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
<td colspan="2">--apiserver-advertise-address string</td>
</tr>
<tr>
<td></td><td style="line-height: 130%; word-wrap: break-word;">
<!--
<p>The IP address the API Server will advertise it's listening on. If not set the default network interface will be used.</p>
-->
<p>API 伺服器所公佈的其正在監聽的 IP 地址。如果未設定，則使用預設的網路介面。</p>
</td>
</tr>

<tr>
<td colspan="2">--apiserver-cert-extra-sans strings</td>
</tr>
<tr>
<td></td><td style="line-height: 130%; word-wrap: break-word;">
<!--
<p>Optional extra Subject Alternative Names (SANs) to use for the API Server serving certificate. Can be both IP addresses and DNS names.</p>
-->
<p>用於 API Server 服務證書的可選附加主體備用名稱（SAN）。可以是 IP 地址和 DNS 名稱。</p>
</td>
</tr>

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
<p>證書的儲存路徑。</p>
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
<p>kubeadm 配置檔案的路徑。</p>
</td>
</tr>

<tr>
<td colspan="2">--control-plane-endpoint string</td>
</tr>
<tr>
<td></td><td style="line-height: 130%; word-wrap: break-word;">
<!--
<p>Specify a stable IP address or DNS name for the control plane.</p>
-->
<p>為控制平面指定一個穩定的 IP 地址或 DNS 名稱。</p>
</td>
</tr>

<tr>
<td colspan="2">-h, --help</td>
</tr>
<tr>
<td></td><td style="line-height: 130%; word-wrap: break-word;">
<!--
<p>help for apiserver</p>
-->
<p>apiserver 操作的幫助命令</p>
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

<tr>
<td colspan="2">
<!--
--service-cidr string&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Default: "10.96.0.0/12"
-->
--service-cidr string&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;預設值："10.96.0.0/12"
</td>
</tr>
<tr>
<td></td><td style="line-height: 130%; word-wrap: break-word;">
<!--
<p>Use alternative range of IP address for service VIPs.</p>
-->
<p>指定服務 VIP 可使用的其他 IP 地址段。</p>
</td>
</tr>

<tr>
<td colspan="2">
<!--
--service-dns-domain string&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Default: "cluster.local"
-->
--service-dns-domain string&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;預設值："cluster.local"
</td>
</tr>
<tr>
<td></td><td style="line-height: 130%; word-wrap: break-word;">
<!--
<p>Use alternative domain for services, e.g. "myorg.internal".</p>
-->
<p>為服務使用其他域名，例如 "myorg.internal"。</p>
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
<p>[實驗] 到 '真實' 主機根檔案系統的路徑。</p>
</td>
</tr>

</tbody>
</table>
