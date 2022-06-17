---
title: Kubelet 配置 (v1beta1)
content_type: tool-reference
package: kubelet.config.k8s.io/v1beta1
auto_generated: true
---

<!--
title: Kubelet Configuration (v1beta1)
content_type: tool-reference
package: kubelet.config.k8s.io/v1beta1
auto_generated: true
-->

<!--
## Resource Types
-->
## 資源型別

- [CredentialProviderConfig](#kubelet-config-k8s-io-v1beta1-CredentialProviderConfig)
- [KubeletConfiguration](#kubelet-config-k8s-io-v1beta1-KubeletConfiguration)
- [SerializedNodeConfigSource](#kubelet-config-k8s-io-v1beta1-SerializedNodeConfigSource)

## `CredentialProviderConfig`     {#kubelet-config-k8s-io-v1beta1-CredentialProviderConfig}

<!--
CredentialProviderConfig is the configuration containing information about
each exec credential provider. Kubelet reads this configuration from disk and enables
each provider as specified by the CredentialProvider type.
-->
CredentialProviderConfig 包含有關每個 exec 憑據提供者的配置資訊。
Kubelet 從磁碟上讀取這些配置資訊，並根據 CredentialProvider 型別啟用各個提供者。

<table class="table">
<thead><tr><th width="30%"><!--Field-->欄位</th><th><!--Description-->描述</th></tr></thead>
<tbody>
    
<tr><td><code>apiVersion</code><br/>string</td><td><code>kubelet.config.k8s.io/v1beta1</code></td></tr>
<tr><td><code>kind</code><br/>string</td><td><code>CredentialProviderConfig</code></td></tr>
    
  
<tr><td><code>providers</code> <B><!--[Required]-->[必需]</B><br/>
<a href="#kubelet-config-k8s-io-v1beta1-CredentialProvider"><code>[]CredentialProvider</code></a>
</td>
<td>
<!--
providers is a list of credential provider plugins that will be enabled by the kubelet.
Multiple providers may match against a single image, in which case credentials
from all providers will be returned to the kubelet. If multiple providers are called
for a single image, the results are combined. If providers return overlapping
auth keys, the value from the provider earlier in this list is used.
-->
   <p>
   <code>providers</code> 是一組憑據提供者外掛，這些外掛會被 kubelet 啟用。
   多個提供者可以匹配到同一映象上，這時，來自所有提供者的憑據資訊都會返回給 kubelet。
   如果針對同一映象呼叫了多個提供者，則結果會被組合起來。如果提供者返回的認證主鍵有重複，
   列表中先出現的提供者所返回的值將被使用。
   </p>
</td>
</tr>
</tbody>
</table>

## `KubeletConfiguration`     {#kubelet-config-k8s-io-v1beta1-KubeletConfiguration}

<!--
KubeletConfiguration contains the configuration for the Kubelet
-->
KubeletConfiguration 中包含 Kubelet 的配置。

<table class="table">
<thead><tr><th width="30%"><!--Field-->欄位</th><th><!--Description-->描述</th></tr></thead>
<tbody>

<tr><td><code>apiVersion</code><br/>string</td><td><code>kubelet.config.k8s.io/v1beta1</code></td></tr>
<tr><td><code>kind</code><br/>string</td><td><code>KubeletConfiguration</code></td></tr>
<tr><td><code>enableServer</code> <B>[必需]</B><br/>
<code>bool</code>
</td>
<td>
   <!--enableServer enables Kubelet's secured server.
Note: Kubelet's insecure port is controlled by the readOnlyPort option.
Default: true-->
  <p><code>enableServer</code> 會啟用 kubelet 的安全伺服器。</p>
  <p>注意：kubelet 的不安全埠由 <code>readOnlyPort</code> 選項控制。</p>
  <p>預設值：<code>true</code></p>
</td>
</tr>

<tr><td><code>staticPodPath</code><br/>
<code>string</code>
</td>
<td>
   <!--staticPodPath is the path to the directory containing local (static) pods to
run, or the path to a single static pod file.
Default: &quot;&quot;-->
  <p><code>staticPodPath</code> 是指向要執行的本地（靜態）Pod 的目錄，
或者指向某個靜態 Pod 檔案的路徑。</p>
  <p>預設值：&quot;&quot;</p>
</td>
</tr>

<tr><td><code>syncFrequency</code><br/>
<a href="https://pkg.go.dev/k8s.io/apimachinery/pkg/apis/meta/v1#Duration"><code>meta/v1.Duration</code></a>
</td>
<td>
   <!--syncFrequency is the max period between synchronizing running
containers and config.
Default: &quot;1m&quot;-->
  <p><code>syncFrequency</code> 是對執行中的容器和配置進行同步的最長週期。</p>
  <p>預設值：&quot;1m&quot;</p>
</td>
</tr>

<tr><td><code>fileCheckFrequency</code><br/>
<a href="https://pkg.go.dev/k8s.io/apimachinery/pkg/apis/meta/v1#Duration"><code>meta/v1.Duration</code></a>
</td>
<td>
   <!--fileCheckFrequency is the duration between checking config files for
new data.
Default: &quot;20s&quot;-->
  <p><code>fileCheckFrequency</code> 是對配置檔案中新資料進行檢查的時間間隔值。</p>
  <p>預設值：&quot;20s&quot;</p>
</td>
</tr>

<tr><td><code>httpCheckFrequency</code><br/>
<a href="https://pkg.go.dev/k8s.io/apimachinery/pkg/apis/meta/v1#Duration"><code>meta/v1.Duration</code></a>
</td>
<td>
  <!--
   httpCheckFrequency is the duration between checking http for new data.
Default: &quot;20s&quot;
  -->
  <p><code>httpCheckFrequency</code> 是對 HTTP 伺服器上新資料進行檢查的時間間隔值。</p>
  <p>預設值：&quot;20s&quot;</p>
</td>
</tr>

<tr><td><code>staticPodURL</code><br/>
<code>string</code>
</td>
<td>
  <!--staticPodURL is the URL for accessing static pods to run.
Default: &quot;&quot;
  -->
  <p><code>staticPodURL</code> 是訪問要執行的靜態 Pod 的 URL 地址。
  <p>預設值：&quot;&quot;</p>
</td>
</tr>

<tr><td><code>staticPodURLHeader</code><br/>
<code>map[string][]string</code>
</td>
<td>
   <!--
   staticPodURLHeader is a map of slices with HTTP headers to use when accessing the podURL.
Default: nil
   -->
   <p><code>staticPodURLHeader</code>是一個由字串組成的對映表，其中包含的 HTTP
頭部資訊用於訪問<code>podURL</code>。</p>
  <p>預設值：nil</p>
</td>
</tr>

<tr><td><code>address</code><br/>
<code>string</code>
</td>
<td>
   <!--
   address is the IP address for the Kubelet to serve on (set to 0.0.0.0
for all interfaces).
Default: &quot;0.0.0.0&quot;
   -->
   <p><code>address</code> 是 kubelet 提供服務所用的 IP 地址（設定為 0.0.0.0
使用所有網路介面提供服務）。</p>
  <p>預設值：&quot;0.0.0.0&quot;</p>
</td>
</tr>

<tr><td><code>port</code><br/>
<code>int32</code>
</td>
<td>
   <!--
   port is the port for the Kubelet to serve on.
The port number must be between 1 and 65535, inclusive.
Default: 10250
   -->
   <p><code>port</code> 是 kubelet 用來提供服務所使用的埠號。
這一埠號必須介於 1 到 65535 之間，包含 1 和 65535。</p>
  <p>預設值：10250</p>
</td>
</tr>

<tr><td><code>readOnlyPort</code><br/>
<code>int32</code>
</td>
<td>
   <!--readOnlyPort is the read-only port for the Kubelet to serve on with
no authentication/authorization.
The port number must be between 1 and 65535, inclusive.
Setting this field to 0 disables the read-only service.
Default: 0 (disabled)
   -->
   <p><code>readOnlyPort</code> 是 kubelet 用來提供服務所使用的只讀埠號。
此埠上的服務不支援身份認證或鑑權。這一埠號必須介於 1 到 65535 之間，
包含 1 和 65535。將此欄位設定為 0 會禁用只讀服務。</p>
   <p>預設值：0（禁用）</p>
</td>
</tr>

<tr><td><code>tlsCertFile</code><br/>
<code>string</code>
</td>
<td>
   <!--tlsCertFile is the file containing x509 Certificate for HTTPS. (CA cert,
if any, concatenated after server cert). If tlsCertFile and
tlsPrivateKeyFile are not provided, a self-signed certificate
and key are generated for the public address and saved to the directory
passed to the Kubelet's --cert-dir flag.
Default:&quot;quot;
  -->
  <p><code>tlsCertFile</code>是包含 HTTPS 所需要的 x509 證書的檔案
（如果有 CA 證書，會串接到伺服器證書之後）。如果<code>tlsCertFile</code>
和<code>tlsPrivateKeyFile</code>都沒有設定，則系統會為節點的公開地址生成自簽名的證書和私鑰，
並將其儲存到 kubelet <code>--cert-dir</code>引數所指定的目錄下。</p>
  <p>預設值：&quot;&quot;</p>
</td>
</tr>

<tr><td><code>tlsPrivateKeyFile</code><br/>
<code>string</code>
</td>
<td>
   <!--tlsPrivateKeyFile is the file containing x509 private key matching tlsCertFile.
Default: &quot;&quot;
   -->
   <p><code>tlsPrivateKeyFile</code>是一個包含與<code>tlsCertFile</code>
證書匹配的 X509 私鑰的檔案。</p>
  <p>預設值：&quot;&quot;</p>
</td>
</tr>

<tr><td><code>tlsCipherSuites</code><br/>
<code>[]string</code>
</td>
<td>
   <!--tlsCipherSuites is the list of allowed cipher suites for the server.
Values are from tls package constants (https://golang.org/pkg/crypto/tls/#pkg-constants).
Default: nil
   -->
   <p><code>tlsCipherSuites</code>是一個字串列表，其中包含伺服器所接受的加密包名稱。
列表中的每個值來自於<code>tls</code>包中定義的常數（https://golang.org/pkg/crypto/tls/#pkg-constants）。</p>
  <p>預設值：nil</p>
</td>
</tr>

<tr><td><code>tlsMinVersion</code><br/>
<code>string</code>
</td>
<td>
   <!--tlsMinVersion is the minimum TLS version supported.
Values are from tls package constants (https://golang.org/pkg/crypto/tls/#pkg-constants).
Default: &quot;&quot;
   -->
   <p><code>tlsMinVersion</code>給出所支援的最小 TLS 版本。
欄位取值來自於<code>tls</code>包中的常數定義（https://golang.org/pkg/crypto/tls/#pkg-constants）。</p>
  <p>預設值：&quot;&quot;</p>
</td>
</tr>

<tr><td><code>rotateCertificates</code><br/>
<code>bool</code>
</td>
<td>
   <!--rotateCertificates enables client certificate rotation. The Kubelet will request a
new certificate from the certificates.k8s.io API. This requires an approver to approve the
certificate signing requests.
Default: false
   -->
   <p><code>rotateCertificates</code>用來啟用客戶端證書輪換。kubelet 會呼叫
<code>certificates.k8s.io</code> API 來請求新的證書。需要有一個批覆人批准證書籤名請求。</p>
  <p>預設值：false</code>
</td>
</tr>

<tr><td><code>serverTLSBootstrap</code><br/>
<code>bool</code>
</td>
<td>
   <!--serverTLSBootstrap enables server certificate bootstrap. Instead of self
signing a serving certificate, the Kubelet will request a certificate from
the 'certificates.k8s.io' API. This requires an approver to approve the
certificate signing requests (CSR). The RotateKubeletServerCertificate feature
must be enabled when setting this field.
Default: false
   -->
   <p><code>serverTLSBootstrap</code>用來啟用伺服器證書引導。系統不再使用自簽名的服務證書，
kubelet 會呼叫<code>certificates.k8s.io</code> API 來請求證書。
需要有一個批覆人來批准證書籤名請求（CSR）。
設定此欄位時，<code>RotateKubeletServerCertificate</code>特性必須被啟用。</p>
  <p>預設值：false</p>
</td>
</tr>

<tr><td><code>authentication</code><br/>
<a href="#kubelet-config-k8s-io-v1beta1-KubeletAuthentication"><code>KubeletAuthentication</code></a>
</td>
<td>
   <!--authentication specifies how requests to the Kubelet's server are authenticated.
Defaults:
  anonymous:
    enabled: false
  webhook:
    enabled: true
    cacheTTL: &quot;2m&quot;
   -->
   <p><code>authorization</code>設定傳送給 kubelet 伺服器的請求是如何進行身份認證的。</p>
  <p>預設值：</p>
  <pre><code>
  anonymous:
    enabled: false
  webhook:
    enabled: true
    cacheTTL: &quot;2m&quot;
  </code></pre>
</td>
</tr>

<tr><td><code>authorization</code><br/>
<a href="#kubelet-config-k8s-io-v1beta1-KubeletAuthorization"><code>KubeletAuthorization</code></a>
</td>
<td>
   <!--authorization specifies how requests to the Kubelet's server are authorized.
Defaults:
  mode: Webhook
  webhook:
    cacheAuthorizedTTL: &quot;5m&quot;
    cacheUnauthorizedTTL: &quot;30s&quot;</td>
   -->
   <p><code>authorization</code>設定傳送給 kubelet 伺服器的請求是如何進行鑑權的。</p>
  <p>預設值：</p>
  <pre><code>
  mode: Webhook
  webhook:
    cacheAuthorizedTTL: &quot;5m&quot;
    cacheUnauthorizedTTL: &quot;30s&quot;
  </code></pre>
</td>
</tr>

<tr><td><code>registryPullQPS</code><br/>
<code>int32</code>
</td>
<td>
   <!--registryPullQPS is the limit of registry pulls per second.
The value must not be a negative number.
Setting it to 0 means no limit.
Default: 5
   -->
   <p><code>registryPullQPS</code>是每秒鐘可以執行的映象倉庫拉取操作限值。
此值必須不能為負數。將其設定為 0 表示沒有限值。</p>
  <p>預設值：5</code>
</td>
</tr>

<tr><td><code>registryBurst</code><br/>
<code>int32</code>
</td>
<td>
   <!--registryBurst is the maximum size of bursty pulls, temporarily allows
pulls to burst to this number, while still not exceeding registryPullQPS.
The value must not be a negative number.
Only used if registryPullQPS is greater than 0.
Default: 10
   -->
   <p><code>registryBurst</code>是突發性映象拉取的上限值，允許映象拉取臨時上升到所指定數量，
不過仍然不超過<code>registryPullQPS</code>所設定的約束。此值必須是非負值。
只有<code>registryPullQPS</code>引數值大於 0 時才會使用此設定。</p>
  <p>預設值：10</p>
</td>
</tr>

<tr><td><code>eventRecordQPS</code><br/>
<code>int32</code>
</td>
<td>
   <!--eventRecordQPS is the maximum event creations per second. If 0, there
is no limit enforced. The value cannot be a negative number.
Default: 5
   -->
   <p><code>eventRecordQPS</code>設定每秒鐘可建立的事件個數上限。如果此值為 0，
則表示沒有限制。此值不能設定為負數。</p>
  <p>預設值：5</p>
</td>
</tr>

<tr><td><code>eventBurst</code><br/>
<code>int32</code>
</td>
<td>
   <!--
eventBurst is the maximum size of a burst of event creations, temporarily
allows event creations to burst to this number, while still not exceeding
eventRecordQPS. This field canot be a negative number and it is only used
when eventRecordQPS &gt; 0.
Default: 10
   -->
   <p><code>eventBurst</code>是突發性事件建立的上限值，允許事件建立臨時上升到所指定數量，
不過仍然不超過<code>eventRecordQPS</code>所設定的約束。此值必須是非負值，
且只有<code>eventRecordQPS</code> &gt; 0 時才會使用此設定。</p>
  <p>預設值：10</p>
</td>
</tr>

<tr><td><code>enableDebuggingHandlers</code><br/>
<code>bool</code>
</td>
<td>
   <!--enableDebuggingHandlers enables server endpoints for log access
and local running of containers and commands, including the exec,
attach, logs, and portforward features.
Default: true
   -->
   <p><code>enableDebuggingHandlers</code>啟用伺服器上用來訪問日誌、
在本地執行容器和命令的端點，包括<code>exec</code>、<code>attach</code>、
<code>logs</code>和<code>portforward</code>等功能。</p>
  <p>預設值：true</p>
</td>
</tr>

<tr><td><code>enableContentionProfiling</code><br/>
<code>bool</code>
</td>
<td>
   <!--
enableContentionProfiling enables lock contention profiling, if enableDebuggingHandlers is true.
Default: false
   -->
   <p><code>enableContentionProfiling</code>用於啟用鎖競爭效能分析，
僅用於<code>enableDebuggingHandlers</code>為<code>true</code>的場合。</p>
  <p>預設值：false</code>
</td>
</tr>

<tr><td><code>healthzPort</code><br/>
<code>int32</code>
</td>
<td>
   <!--
healthzPort is the port of the localhost healthz endpoint (set to 0 to disable).
A valid number is between 1 and 65535.
Default: 10248
   -->
   <p><code>healthzPort</code>是本地主機上提供<code>healthz</code>端點的埠
（設定值為 0 時表示禁止）。合法值介於 1 和 65535 之間。</p>
  <p>預設值：10248</p>
</td>
</tr>

<tr><td><code>healthzBindAddress</code><br/>
<code>string</code>
</td>
<td>
   <!--
healthzBindAddress is the IP address for the healthz server to serve on.
Default: &quot;127.0.0.1&quot;
   -->
   <p><code>healthzBindAddress<code>是<code>healthz</code>伺服器用來提供服務的 IP 地址。</p>
  <p>預設值：&quot;127.0.0.1&quot;</p>
</td>
</tr>

<tr><td><code>oomScoreAdj</code><br/>
<code>int32</code>
</td>
<td>
   <!--oomScoreAdj is The oom-score-adj value for kubelet process. Values
must be within the range [-1000, 1000].
Default: -999
   -->
   <p><code>oomScoreAdj</code> 是為 kubelet 程序設定的<code>oom-score-adj</code>值。
所設定的取值要在 [-1000, 1000] 範圍之內。</p>
   <p>預設值：-999</p>
</td>
</tr>

<tr><td><code>clusterDomain</code><br/>
<code>string</code>
</td>
<td>
   <!--clusterDomain is the DNS domain for this cluster. If set, kubelet will
configure all containers to search this domain in addition to the
host's search domains.
Default: &quot;&quot;
   -->
   <p><code>clusterDomain</code>是叢集的 DNS 域名。如果設定了此欄位，kubelet
會配置所有容器，使之在搜尋主機的搜尋域的同時也搜尋這裡指定的 DNS 域。</p>
   <p>預設值：&quot;&quot;</p>
</td>
</tr>

<tr><td><code>clusterDNS</code><br/>
<code>[]string</code>
</td>
<td>
   <!--clusterDNS is a list of IP addresses for the cluster DNS server. If set,
kubelet will configure all containers to use this for DNS resolution
instead of the host's DNS servers.
Default: nil
  -->
  <p><code>clusterDNS</code>是叢集 DNS 伺服器的 IP 地址的列表。
如果設定了，kubelet 將會配置所有容器使用這裡的 IP 地址而不是宿主系統上的 DNS
伺服器來完成 DNS 解析。
  <p>預設值：nil</p>
</td>
</tr>

<tr><td><code>streamingConnectionIdleTimeout</code><br/>
<a href="https://pkg.go.dev/k8s.io/apimachinery/pkg/apis/meta/v1#Duration"><code>meta/v1.Duration</code></a>
</td>
<td>
   <!--streamingConnectionIdleTimeout is the maximum time a streaming connection
can be idle before the connection is automatically closed.
Default: &quot;4h&quot;
   -->
   <p><code>streamingConnectionIdleTimeout</code>設定流式連線在被自動關閉之前可以空閒的最長時間。</p>
   <p>預設值：&quot;4h&quot;</p>
</td>
</tr>

<tr><td><code>nodeStatusUpdateFrequency</code><br/>
<a href="https://pkg.go.dev/k8s.io/apimachinery/pkg/apis/meta/v1#Duration"><code>meta/v1.Duration</code></a>
</td>
<td>
   <!--nodeStatusUpdateFrequency is the frequency that kubelet computes node
status. If node lease feature is not enabled, it is also the frequency that
kubelet posts node status to master.
Note: When node lease feature is not enabled, be cautious when changing the
constant, it must work with nodeMonitorGracePeriod in nodecontroller.
Default: &quot;10s&quot;
   -->
   <p><code>nodeStatusUpdateFrequency</code>是 kubelet 計算節點狀態的頻率。
如果未啟用節點租約特性，這一欄位設定的也是 kubelet 向控制面投遞節點狀態的頻率。</p>
   <p>注意：如果節點租約特性未被啟用，更改此引數設定時要非常小心，
所設定的引數值必須與節點控制器的<code>nodeMonitorGracePeriod</code>協同。</p>
   <p>預設值：&quot;10s&quot;</p>
</td>
</tr>

<tr><td><code>nodeStatusReportFrequency</code><br/>
<a href="https://pkg.go.dev/k8s.io/apimachinery/pkg/apis/meta/v1#Duration"><code>meta/v1.Duration</code></a>
</td>
<td>
   <!--nodeStatusReportFrequency is the frequency that kubelet posts node
status to master if node status does not change. Kubelet will ignore this
frequency and post node status immediately if any change is detected. It is
only used when node lease feature is enabled. nodeStatusReportFrequency's
default value is 5m. But if nodeStatusUpdateFrequency is set explicitly,
nodeStatusReportFrequency's default value will be set to
nodeStatusUpdateFrequency for backward compatibility.
Default: &quot;5m&quot;
   -->
   <p><code>nodeStatusReportFrequency</code>是節點狀態未發生變化時，kubelet
向控制面更新節點狀態的頻率。如果節點狀態發生變化，則 kubelet 會忽略這一頻率設定，
立即更新節點狀態。</p>
   <p>此欄位僅當啟用了節點租約特性時才被使用。<code>nodeStatusReportFrequency</code>
的預設值是&quot;5m&quot;。不過，如果<code>nodeStatusUpdateFrequency</code>
被顯式設定了，則<code>nodeStatusReportFrequency</code>的預設值會等於
<code>nodeStatusUpdateFrequency</code>值，這是為了實現向後相容。</p>
   <p>預設值：&quot;5m&quot;</p>
</td>
</tr>

<tr><td><code>nodeLeaseDurationSeconds</code><br/>
<code>int32</code>
</td>
<td>
   <!--nodeLeaseDurationSeconds is the duration the Kubelet will set on its corresponding Lease.
NodeLease provides an indicator of node health by having the Kubelet create and
periodically renew a lease, named after the node, in the kube-node-lease namespace.
If the lease expires, the node can be considered unhealthy.
The lease is currently renewed every 10s, per KEP-0009. In the future, the lease renewal
interval may be set based on the lease duration.
The field value must be greater than 0.
Default: 40
   -->
   <p><code>nodeLeaseDurationSeconds</code>是 kubelet 會在其對應的 Lease 物件上設定的時長值。
<code>NodeLease</code>讓 kubelet 來在<code>kube-node-lease</code>名字空間中建立
按節點名稱命名的租約並定期執行續約操作，並透過這種機制來了解節點健康狀況。</p>
   <p>如果租約過期，則節點可被視作不健康。根據 KEP-0009 約定，目前的租約每 10 秒鐘續約一次。
在將來，租約的續約時間間隔可能會根據租約的時長來設定。</p>
   <p>此欄位的取值必須大於零。</p>
  <p>預設值：40</p>
</td>
</tr>

<tr><td><code>imageMinimumGCAge</code><br/>
<a href="https://pkg.go.dev/k8s.io/apimachinery/pkg/apis/meta/v1#Duration"><code>meta/v1.Duration</code></a>
</td>
<td>
   <!--imageMinimumGCAge is the minimum age for an unused image before it is
garbage collected. 
Default: &quot;2m&quot;
   -->
   <p><code>imageMinimumGCAge</code>是對未使用映象進行垃圾蒐集之前允許其存在的時長。</p>
   <p>預設值：&quot;2m&quot;</p>
</td>
</tr>

<tr><td><code>imageGCHighThresholdPercent</code><br/>
<code>int32</code>
</td>
<td>
   <!--imageGCHighThresholdPercent is the percent of disk usage after which
image garbage collection is always run. The percent is calculated by
dividing this field value by 100, so this field must be between 0 and
100, inclusive. When specified, the value must be greater than
imageGCLowThresholdPercent.
Default: 85
   -->
   <p><code>imageGCHighThresholdPercent</code>所給的是映象的磁碟用量百分數，
一旦映象用量超過此閾值，則映象垃圾收集會一直執行。百分比是用這裡的值除以 100
得到的，所以此欄位取值必須介於 0 和 100 之間，包括 0 和 100。如果設定了此欄位，
則取值必須大於<code>imageGCLowThresholdPercent</code>取值。</p>
   <p>預設值：85</p>
</td>
</tr>

<tr><td><code>imageGCLowThresholdPercent</code><br/>
<code>int32</code>
</td>
<td>
   <!--imageGCLowThresholdPercent is the percent of disk usage before which
image garbage collection is never run. Lowest disk usage to garbage
collect to. The percent is calculated by dividing this field value by 100,
so the field value must be between 0 and 100, inclusive. When specified, the
value must be less than imageGCHighThresholdPercent.
Default: 80
   -->
   <p><code>imageGCLowThresholdPercent</code>所給的是映象的磁碟用量百分數，
映象用量低於此閾值時不會執行映象垃圾收集操作。垃圾收集操作也將此作為最低磁碟用量邊界。
百分比是用這裡的值除以 100 得到的，所以此欄位取值必須介於 0 和 100 之間，包括 0 和 100。
如果設定了此欄位，則取值必須小於<code>imageGCHighThresholdPercent</code>取值。</p>
   <p>預設值：80</p>
</td>
</tr>

<tr><td><code>volumeStatsAggPeriod</code><br/>
<a href="https://pkg.go.dev/k8s.io/apimachinery/pkg/apis/meta/v1#Duration"><code>meta/v1.Duration</code></a>
</td>
<td>
   <!--volumeStatsAggPeriod is the frequency for calculating and caching volume
disk usage for all pods.
Default: &quot;1m&quot;
   -->
   <p><code>volumeStatsAggPeriod</code>是計算和快取所有 Pod 磁碟用量的頻率。</p>
   <p>預設值：&quot;1m&quot;</p>
</td>
</tr>

<tr><td><code>kubeletCgroups</code><br/>
<code>string</code>
</td>
<td>
   <!--kubeletCgroups is the absolute name of cgroups to isolate the kubelet in
Default: &quot;&quot;
   -->
   <p><code>kubeletCgroups</code>是用來隔離 kubelet 的控制組（CGroup）的絕對名稱。</p>
   <p>預設值：&quot;&quot;</p>
</td>
</tr>

<tr><td><code>systemCgroups</code><br/>
<code>string</code>
</td>
<td>
   <!--systemCgroups is absolute name of cgroups in which to place
all non-kernel processes that are not already in a container. Empty
for no container. Rolling back the flag requires a reboot.
The cgroupRoot must be specified if this field is not empty.
Default: &quot;&qout;
   -->
   <p><code>systemCgroups</code>是用來放置那些未被容器化的、非核心的程序的控制組
(CGroup）的絕對名稱。設定為空字串表示沒有這類容器。回滾此欄位設定需要重啟節點。
當此欄位非空時，必須設定<code>cgroupRoot</code>欄位。</p>
   <p>預設值：&quot;&quot;</p>
</td>
</tr>

<tr><td><code>cgroupRoot</code><br/>
<code>string</code>
</td>
<td>
   <!--cgroupRoot is the root cgroup to use for pods. This is handled by the
container runtime on a best effort basis.
   -->
   <p><code>cgroupRoot</code>是用來執行 Pod 的控制組 (CGroup）。
容器執行時會盡可能處理此欄位的設定值。</p>
</td>
</tr>

<tr><td><code>cgroupsPerQOS</code><br/>
<code>bool</code>
</td>
<td>
   <!--cgroupsPerQOS enable QoS based CGroup hierarchy: top level CGroups for QoS classes
and all Burstable and BestEffort Pods are brought up under their specific top level
QoS CGroup.
Default: true
   -->
   <p><code>cgroupsPerQOS</code>用來啟用基於 QoS 的控制組（CGroup）層次結構：
頂層的控制組用於不同 QoS 類，所有<code>Burstable</code>和<code>BestEffort</code> Pod
都會被放置到對應的頂級 QoS 控制組下。</p>
   <p>預設值：true</p>
</td>
</tr>

<tr><td><code>cgroupDriver</code><br/>
<code>string</code>
</td>
<td>
   <!--cgroupDriver is the driver kubelet uses to manipulate CGroups on the host (cgroupfs
or systemd).
Default: &quot;cgroupfs&quot;
   -->
   <p><code>cgroupDriver</code>是 kubelet 用來操控宿主系統上控制組 (CGroup）
的驅動程式（cgroupfs 或 systemd）。</p>
   <p>預設值：&quot;cgroupfs&quot;</p>
</td>
</tr>

<tr><td><code>cpuManagerPolicy</code><br/>
<code>string</code>
</td>
<td>
   <!--cpuManagerPolicy is the name of the policy to use.
Requires the CPUManager feature gate to be enabled.
Default: &quot;None&quot;
   -->
   <p><code>cpuManagerPolicy</code>是要使用的策略名稱。需要啟用<code>CPUManager</code>
特性門控。</p>
   <p>預設值：&quot;None&quot;</p>
</td>
</tr>

<tr><td><code>cpuManagerPolicyOptions</code><br/>
<code>map[string]string</code>
</td>
<td>
   <!--cpuManagerPolicyOptions is a set of key=value which 	allows to set extra options
to fine tune the behaviour of the cpu manager policies.
Requires  both the &quot;CPUManager&quot; and &quot;CPUManagerPolicyOptions&quot; feature gates to be enabled.
Default: nil
   -->
   <p><code>cpuManagerPolicyOptions</code>是一組<code>key=value</code>鍵值對映，
容許透過額外的選項來精細調整 CPU 管理器策略的行為。需要<code>CPUManager</code>和
<code>CPUManagerPolicyOptions</code>兩個特性門控都被啟用。</p>
   <p>預設值：nil</p>
</td>
</tr>

<tr><td><code>cpuManagerReconcilePeriod</code><br/>
<a href="https://pkg.go.dev/k8s.io/apimachinery/pkg/apis/meta/v1#Duration"><code>meta/v1.Duration</code></a>
</td>
<td>
   <!--cpuManagerReconcilePeriod is the reconciliation period for the CPU Manager.
Requires the CPUManager feature gate to be enabled.
Default: &quot;10s&quot;
   -->
   <p><code>cpuManagerReconcilePeriod</code>是 CPU 管理器的協調週期時長。
需要啟用<code>CPUManager</code>特性門控。</p>
   <p>預設值：&quot;10s&quot;</p>
</td>
</tr>

<tr><td><code>memoryManagerPolicy</code><br/>
<code>string</code>
</td>
<td>
   <!--memoryManagerPolicy is the name of the policy to use by memory manager.
Requires the MemoryManager feature gate to be enabled.
Default: &quot;none&quot;
   -->
   <p><code>memoryManagerPolicy</code>是記憶體管理器要使用的策略的名稱。
要求啟用<code>MemoryManager</code>特性門控。</p>
   <p>預設值：&quot;none&quot;</p>
</td>
</tr>

<tr><td><code>topologyManagerPolicy</code><br/>
<code>string</code>
</td>
<td>
  <!--
   <p>topologyManagerPolicy is the name of the topology manager policy to use.
Valid values include:</p>
<ul>
<li><code>restricted</code>: kubelet only allows pods with optimal NUMA node alignment for
requested resources;</li>
<li><code>best-effort</code>: kubelet will favor pods with NUMA alignment of CPU and device
resources;</li>
<li><code>none</code>: kubelet has no knowledge of NUMA alignment of a pod's CPU and device resources.</li>
<li><code>single-numa-node</code>: kubelet only allows pods with a single NUMA alignment
of CPU and device resources.</li>
</ul>
<p>Policies other than &quot;none&quot; require the TopologyManager feature gate to be enabled.
Default: &quot;none&quot;</p>
  -->
   <p><code>topologyManagerPolicy</code>是要使用的拓撲管理器策略名稱。合法值包括：</p> 
   <ul>
    <li><code>restricted</code>：kubelet 僅接受在所請求資源上實現最佳 NUMA 對齊的 Pod。</li>
    <li><code>best-effort</code>：kubelet 會優選在 CPU 和裝置資源上實現 NUMA 對齊的 Pod。</li>
    <li><code>none</code>：kubelet 不瞭解 Pod CPU 和裝置資源 NUMA 對齊需求。</li>
    <li><code>single-numa-node</code>：kubelet 僅允許在 CPU 和裝置資源上對齊到同一 NUMA 節點的 Pod。</li>
   </ul>
   <p>如果策略不是 &quot;none&quot;，則要求啟用<code>TopologyManager</code>特性門控。</p>
   <p>預設值：&quot;none&quot;</p>
</td>
</tr>

<tr><td><code>topologyManagerScope</code><br/>
<code>string</code>
</td>
<td>
   <!--
   <p>topologyManagerScope represents the scope of topology hint generation
that topology manager requests and hint providers generate. Valid values include:</p>
<ul>
<li><code>container</code>: topology policy is applied on a per-container basis.</li>
<li><code>pod</code>: topology policy is applied on a per-pod basis.</li>
</ul>
<p>&quot;pod&quot; scope requires the TopologyManager feature gate to be enabled.
Default: &quot;container&quot;</p>
   -->
   <p><code>topologyManagerScope</code>代表的是拓撲提示生成的範圍，
拓撲提示資訊由提示提供者生成，提供給拓撲管理器。合法值包括：</p>
   <ul>
    <li><code>container</code>：拓撲策略是按每個容器來實施的。</li>
    <li><code>pod</code>：拓撲策略是按每個 Pod 來實施的。</li>
   </ul>
   <p>&quot;pod&quot; 範圍要求啟用<code>TopologyManager</code>特性門控。</p>
   <p>預設值：&quot;container&quot;</p>
</td>
</tr>

<tr><td><code>qosReserved</code><br/>
<code>map[string]string</code>
</td>
<td>
   <!--qosReserved is a set of resource name to percentage pairs that specify
the minimum percentage of a resource reserved for exclusive use by the
guaranteed QoS tier.
Currently supported resources: &quot;memory&quot;
Requires the QOSReserved feature gate to be enabled.
Default: nil
   -->
   <p><code>qosReserved</code>是一組從資源名稱到百分比值的對映，用來為<code>Guaranteed</code>
QoS 型別的負載預留供其獨佔使用的資源百分比。目前支援的資源為：&quot;memory&quot;。
需要啟用<code>QOSReserved</code>特性門控。</p>
   <p>預設值：nil</p>
</td>
</tr>

<tr><td><code>runtimeRequestTimeout</code><br/>
<a href="https://pkg.go.dev/k8s.io/apimachinery/pkg/apis/meta/v1#Duration"><code>meta/v1.Duration</code></a>
</td>
<td>
   <!--runtimeRequestTimeout is the timeout for all runtime requests except long running
requests - pull, logs, exec and attach.
Default: &quot;2m&quot;
   -->
   <p><code>runtimeRequestTimeout</code>用來設定除長期執行的請求（<code>pull</code>、
<code>logs</code>、<code>exec</code>和<code>attach</code>）之外所有執行時請求的超時時長。</p>
   <p>預設值：&quot;2m&quot;</p>
</td>
</tr>

<tr><td><code>hairpinMode</code><br/>
<code>string</code>
</td>
<td>
   <!--p>hairpinMode specifies how the Kubelet should configure the container
bridge for hairpin packets.
Setting this flag allows endpoints in a Service to loadbalance back to
themselves if they should try to access their own Service. Values:</p-->
   <p><code>hairpinMode</code>設定 kubelet 如何為髮夾模式資料包配置容器網橋。
設定此欄位可以讓 Service 中的端點在嘗試訪問自身 Service 時將服務請求路由的自身。
可選值有：</p>
   <!--
<li>&quot;promiscuous-bridge&quot;: make the container bridge promiscuous.</li>
<li>&quot;hairpin-veth&quot;:       set the hairpin flag on container veth interfaces.</li>
<li>&quot;none&quot;:               do nothing.</li>
   -->
   <ul>
    <li>&quot;promiscuous-bridge&quot;：將容器網橋設定為混雜模式。</li>
    <li>&quot;hairpin-veth&quot;：在容器的 veth 介面上設定髮夾模式標記。</li>
    <li>&quot;none&quot;：什麼也不做。</li>
   </ul>
   <!--Generally, one must set <code>--hairpin-mode=hairpin-veth to</code> achieve hairpin NAT,
because promiscuous-bridge assumes the existence of a container bridge named cbr0.
Default: &quot;promiscuous-bridge&quot;
    -->
   <p>一般而言，使用者必須設定<code>--hairpin-mode=hairpin-veth</code>才能實現髮夾模式的網路地址轉譯
（NAT），因為混雜模式的網橋要求存在一個名為<code>cbr0</code>的容器網橋。</p>
   <p>預設值：&quot;promiscuous-bridge&quot;</p>
</td>
</tr>

<tr><td><code>maxPods</code><br/>
<code>int32</code>
</td>
<td>
   <!--maxPods is the maximum number of Pods that can run on this Kubelet.
The value must be a non-negative integer.
Default: 110
   -->
   <p><code>maxPods</code>是此 kubelet 上課執行的 Pod 個數上限。此值必須為非負整數。</p>
   <p>預設值：110</p>
</td>
</tr>

<tr><td><code>podCIDR</code><br/>
<code>string</code>
</td>
<td>
   <!--podCIDR is the CIDR to use for pod IP addresses, only used in standalone mode.
In cluster mode, this is obtained from the control plane.
Default: &quot;&quot;
   -->
   <p><code>podCIDR</code>是用來設定 Pod IP 地址的 CIDR 值，僅用於獨立部署模式。
運行於叢集模式時，這一數值會從控制面獲得。</p>
   <p>預設值：&quot;&quot;</p>
</td>
</tr>

<tr><td><code>podPidsLimit</code><br/>
<code>int64</code>
</td>
<td>
   <!--podPidsLimit is the maximum number of PIDs in any pod.
Default: -1
   -->
   <p><code>podPidsLimit</code>是每個 Pod 中可使用的 PID 個數上限。</p>
   <p>預設值：-1</p>
</td>
</tr>

<tr><td><code>resolvConf</code><br/>
<code>string</code>
</td>
<td>
   <!--resolvConf is the resolver configuration file used as the basis
for the container DNS resolution configuration.
If set to the empty string, will override the default and effectively disable DNS lookups.
Default: "/etc/resolv.conf"
   -->
   <p><code>resolvConf</code>是一個域名解析配置檔案，用作容器 DNS 解析配置的基礎。</p>
   <p>如果此值設定為空字串，則會覆蓋 DNS 解析的預設配置，
本質上相當於禁用了 DNS 查詢。</p>
   <p>預設值：&quot;/etc/resolv.conf&quot;</p>
</td>
</tr>

<tr><td><code>runOnce</code><br/>
<code>bool</code>
</td>
<td>
   <!--runOnce causes the Kubelet to check the API server once for pods,
run those in addition to the pods specified by static pod files, and exit.
Default: false
   -->
   <p><code>runOnce</code>欄位被設定時，kubelet 會諮詢 API 伺服器一次並獲得 Pod 列表，
執行在靜態 Pod 檔案中指定的 Pod 及這裡所獲得的的 Pod，然後退出。</p>
   <p>預設值：false</p>
</td>
</tr>

<tr><td><code>cpuCFSQuota</code><br/>
<code>bool</code>
</td>
<td>
   <!--cpuCFSQuota enables CPU CFS quota enforcement for containers that
specify CPU limits.
Default: true
   -->
   <p><code>cpuCFSQuota</code>允許為設定了 CPU 限制的容器實施 CPU CFS 配額約束。</p>
   <p>預設值：true</p>
</td>
</tr>

<tr><td><code>cpuCFSQuotaPeriod</code><br/>
<a href="https://pkg.go.dev/k8s.io/apimachinery/pkg/apis/meta/v1#Duration"><code>meta/v1.Duration</code></a>
</td>
<td>
   <!--cpuCFSQuotaPeriod is the CPU CFS quota period value, `cpu.cfs_period_us`.
The value must be between 1 us and 1 second, inclusive.
Requires the CustomCPUCFSQuotaPeriod feature gate to be enabled.
Default: &quot;100ms&quot;
   -->
   <p><code>cpuCFSQuotaPeriod</code>設定 CPU CFS 配額週期值，<code>cpu.cfs_period_us</code>。
此值需要介於 1 微秒和 1 秒之間，包含 1 微秒和 1 秒。
此功能要求啟用<code>CustomCPUCFSQuotaPeriod</code>特性門控被啟用。</p>
   <p>預設值：&quot;100ms&quot;</p>
</td>
</tr>

<tr><td><code>nodeStatusMaxImages</code><br/>
<code>int32</code>
</td>
<td>
   <!--nodeStatusMaxImages caps the number of images reported in Node.status.images.
The value must be greater than -2.
Note: If -1 is specified, no cap will be applied. If 0 is specified, no image is returned.
Default: 50
   -->
   <p><code>nodeStatusMaxImages</code>限制<code>Node.status.images</code>中報告的映象數量。
此值必須大於 -2。</p>
   <p>注意：如果設定為 -1，則不會對映象數量做限制；如果設定為 0，則不會返回任何映象。</p>
   <p>預設值：50</p>
</td>
</tr>

<tr><td><code>maxOpenFiles</code><br/>
<code>int64</code>
</td>
<td>
   <!--maxOpenFiles is Number of files that can be opened by Kubelet process.
The value must be a non-negative number.
Default: 1000000
   -->
   <p><code>maxOpenFiles</code>是 kubelet 程序可以開啟的檔案個數。此值必須不能為負數。</p>
   <p>預設值：1000000</p>
</td>
</tr>

<tr><td><code>contentType</code><br/>
<code>string</code>
</td>
<td>
   <!--contentType is contentType of requests sent to apiserver.
Default: &quot;application/vnd.kubernetes.protobuf&quot;
   -->
   <p><code>contentType</code>是向 API 伺服器傳送請求時使用的內容型別。</p>
   <p>預設值：&quot;application/vnd.kubernetes.protobuf&quot;</p>
</td>
</tr>

<tr><td><code>kubeAPIQPS</code><br/>
<code>int32</code>
</td>
<td>
   <!--kubeAPIQPS is the QPS to use while talking with kubernetes apiserver.
Default: 5
   -->
   <p><code>kubeAPIQPS</code>設定與 Kubernetes API 伺服器通訊時要使用的 QPS（每秒查詢數）。</p>
   <p>預設值：5</p>
</td>
</tr>

<tr><td><code>kubeAPIBurst</code><br/>
<code>int32</code>
</td>
<td>
   <!--kubeAPIBurst is the burst to allow while talking with kubernetes API server.
This field cannot be a negative number.
Default: 10
   -->
   <p><code>kubeAPIBurst</code>設定與 Kubernetes API 伺服器通訊時突發的流量級別。
此欄位取值不可以是負數。</p>
   <p>預設值：10</p>
</td>
</tr>

<tr><td><code>serializeImagePulls</code><br/>
<code>bool</code>
</td>
<td>
   <!--serializeImagePulls when enabled, tells the Kubelet to pull images one
at a time. We recommend &lowast;not&lowast; changing the default value on nodes that
run docker daemon with version  < 1.9 or an Aufs storage backend.
Issue #10959 has more details.
Default: true
   -->
   <p><code>serializeImagePulls</code>被啟用時會通知 kubelet 每次僅拉取一個映象。
我們建議<em>不要</em>在所執行的 docker 守護程序版本低於 1.9、使用 aufs
儲存後端的節點上更改預設值。詳細資訊可參見 Issue #10959。</p>
   <p>預設值：true</p>
</td>
</tr>

<tr><td><code>evictionHard</code><br/>
<code>map[string]string</code>
</td>
<td>
   <!--evictionHard is a map of signal names to quantities that defines hard eviction thresholds. 
For example: <code>{&quot;memory.available&quot;: &quot;300Mi&quot;}</code>.
To explicitly disable, pass a 0% or 100% threshold on an arbitrary resource.
Default:
   memory.available:  &quot;100Mi&quot;
   nodefs.available:  &quot;10%&quot;
   nodefs.inodesFree: &quot;5%&quot;
   imagefs.available: &quot;15%&quot;
   -->
   <p><code>evictionHard</code>是一個對映，是從訊號名稱到定義硬性驅逐閾值的對映。
例如：<code>{&quot;memory.available&quot;: &quot;300Mi&quot;}</code>。
如果希望顯式地禁用，可以在任意資源上將其閾值設定為 0% 或 100%。</p>
   <p>預設值：</p>
   <code><pre>
   memory.available:  &quot;100Mi&quot;
   nodefs.available:  &quot;10%&quot;
   nodefs.inodesFree: &quot;5%&quot;
   imagefs.available: &quot;15%&quot;
  </pre></code>
</td>
</tr>

<tr><td><code>evictionSoft</code><br/>
<code>map[string]string</code>
</td>
<td>
   <!--evictionSoft is a map of signal names to quantities that defines soft eviction thresholds.
For example: <code>{&quot;memory.available&quot;: &quot;300Mi&quot;}</code>.
Default: nil
   -->
   <p><code>evictionSoft</code>是一個對映，是從訊號名稱到定義軟性驅逐閾值的對映。
例如：<code>{&quot;memory.available&quot;: &quot;300Mi&quot;}</code>。</p>
   <p>預設值：nil</p>
</td>
</tr>

<tr><td><code>evictionSoftGracePeriod</code><br/>
<code>map[string]string</code>
</td>
<td>
   <!--evictionSoftGracePeriod is a map of signal names to quantities that defines grace
periods for each soft eviction signal. For example: <code>{&quot;memory.available&quot;: &quot;30s&quot;}</code>.
Default: nil
   -->
   <p><code>evictionSoftGracePeriod</code>是一個對映，是從訊號名稱到每個軟性驅逐訊號的寬限期限。
例如：<code>{&quot;memory.available&quot;: &quot;30s&quot;}</code>。</p>
   <p>預設值：nil</p>
</td>
</tr>

<tr><td><code>evictionPressureTransitionPeriod</code><br/>
<a href="https://pkg.go.dev/k8s.io/apimachinery/pkg/apis/meta/v1#Duration"><code>meta/v1.Duration</code></a>
</td>
<td>
   <!--evictionPressureTransitionPeriod is the duration for which the kubelet has to wait
before transitioning out of an eviction pressure condition.
Default: &quot;5m&quot;
   -->
   <p><code>evictionPressureTransitionPeriod</code>設定 kubelet
離開驅逐壓力狀況之前必須要等待的時長。</p>
   <p>預設值：&quot;5m&quot;</p>
</td>
</tr>

<tr><td><code>evictionMaxPodGracePeriod</code><br/>
<code>int32</code>
</td>
<td>
   <!--evictionMaxPodGracePeriod is the maximum allowed grace period (in seconds) to use
when terminating pods in response to a soft eviction threshold being met. This value
effectively caps the Pod's terminationGracePeriodSeconds value during soft evictions.
Note: Due to issue #64530, the behavior has a bug where this value currently just
overrides the grace period during soft eviction, which can increase the grace
period from what is set on the Pod. This bug will be fixed in a future release.
Default: 0
   -->
   <p><code>evictionMaxPodGracePeriod</code>是指達到軟性逐出閾值而引起 Pod 終止時，
可以賦予的寬限期限最大值（按秒計）。這個值本質上限制了軟性逐出事件發生時，
Pod 可以獲得的<code>terminationGracePeriodSeconds</code>。</p>
   <p>注意：由於 Issue #64530 的原因，系統中存在一個缺陷，即此處所設定的值會在軟性逐出時覆蓋
Pod 的寬限期設定，從而有可能增加 Pod 上原本設定的寬限期限時長。
這個缺陷會在未來版本中修復。</p>
   <p>預設值：0</p>
</td>
</tr>

<tr><td><code>evictionMinimumReclaim</code><br/>
<code>map[string]string</code>
</td>
<td>
   <!--evictionMinimumReclaim is a map of signal names to quantities that defines minimum reclaims,
which describe the minimum amount of a given resource the kubelet will reclaim when
performing a pod eviction while that resource is under pressure.
For example: <code>{&quot;imagefs.available&quot;: &quot;2Gi&quot;}</code>.
Default: nil
   -->
   <p><code>evictionMinimumReclaim</code>是一個對映，定義訊號名稱與最小回收量數值之間的關係。
最小回收量指的是資源壓力較大而執行 Pod 驅逐操作時，kubelet 對給定資源的最小回收量。
例如：<code>{&quot;imagefs.available&quot;: &quot;2Gi&quot;}</code>。</p>
   <p>預設值：nil</p>
</td>
</tr>

<tr><td><code>podsPerCore</code><br/>
<code>int32</code>
</td>
<td>
   <!--podsPerCore is the maximum number of pods per core. Cannot exceed maxPods.
The value must be a non-negative integer.
If 0, there is no limit on the number of Pods.
Default: 0
   -->
   <p><code>podsPerCore</code>設定的是每個核上 Pod 個數上限。此值不能超過<code>maxPods</code>。
所設值必須是非負整數。如果設定為 0，則意味著對 Pod 個數沒有限制。</p>
   <p>預設值：0</p>
</td>
</tr>

<tr><td><code>enableControllerAttachDetach</code><br/>
<code>bool</code>
</td>
<td>
   <!--enableControllerAttachDetach enables the Attach/Detach controller to
manage attachment/detachment of volumes scheduled to this node, and
disables kubelet from executing any attach/detach operations.
Note: attaching/detaching CSI volumes is not supported by the kubelet,
so this option needs to be true for that use case.
Default: true
   -->
   <p><code>enableControllerAttachDetach</code>用來允許 Attach/Detach
控制器管理排程到本節點的卷的掛接（attachment）和解除掛接（detachement），
並且禁止 kubelet 執行任何 attach/detach 操作。</p>
   <p>注意：kubelet 不支援掛接 CSI 卷和解除掛接，
因此對於該用例，此選項必須為 true。</p>
   <p>預設值：true</p>
</td>
</tr>

<tr><td><code>protectKernelDefaults</code><br/>
<code>bool</code>
</td>
<td>
   <!--protectKernelDefaults, if true, causes the Kubelet to error if kernel
flags are not as it expects. Otherwise the Kubelet will attempt to modify
kernel flags to match its expectation.
Default: false
   -->
   <p><code>protectKernelDefaults</code>設定為<code>true</code>時，會令 kubelet
在發現核心引數與預期不符時出錯退出。若此欄位設定為<code>false</code>，則 kubelet
會嘗試更改核心引數以滿足其預期。</p>
   <p>預設值：false</p>
</td>
</tr>

<tr><td><code>makeIPTablesUtilChains</code><br/>
<code>bool</code>
</td>
<td>
   <!--makeIPTablesUtilChains, if true, causes the Kubelet ensures a set of iptables rules
are present on host.
These rules will serve as utility rules for various components, e.g. kube-proxy.
The rules will be created based on iptablesMasqueradeBit and iptablesDropBit.
Default: true
   -->
   <p><code>makeIPTablesUtilChains</code>設定為<code>true</code>時，相當於允許 kubelet
確保一組 iptables 規則存在於宿主機上。這些規則會為不同的元件（例如 kube-proxy）
提供工具性質的規則。它們是基於<code>iptablesMasqueradeBit</code>和<code>iptablesDropBit</code>
來建立的。</p>
   <p>預設值：true</p>
</td>
</tr>

<tr><td><code>iptablesMasqueradeBit</code><br/>
<code>int32</code>
</td>
<td>
   <!--iptablesMasqueradeBit is the bit of the iptables fwmark space to mark for SNAT.
Values must be within the range [0, 31]. Must be different from other mark bits.
Warning: Please match the value of the corresponding parameter in kube-proxy.
TODO: clean up IPTablesMasqueradeBit in kube-proxy.
Default: 14
   -->
   <p><code>iptablesMasqueradeBit</code>是 iptables fwmark 空間中用來為 SNAT
作標記的位。此值必須介於<code>[0, 31]</code>區間，必須與其他標記位不同。</p>
   <p>警告：請確保此值設定與 kube-proxy 中對應的引數設定取值相同。</p>
   <p>預設值：14</p>
</td>
</tr>

<tr><td><code>iptablesDropBit</code><br/>
<code>int32</code>
</td>
<td>
   <!--iptablesDropBit is the bit of the iptables fwmark space to mark for dropping packets.
Values must be within the range [0, 31]. Must be different from other mark bits.
Default: 15
   -->
   <p><code>iptablesDropBit</code>是 iptables fwmark 空間中用來標記丟棄包的資料位。
此值必須介於<code>[0, 31]</code>區間，必須與其他標記位不同。</p>
   <p>預設值：15</p>
</td>
</tr>

<tr><td><code>featureGates</code><br/>
<code>map[string]bool</code>
</td>
<td>
   <!--featureGates is a map of feature names to bools that enable or disable experimental
features. This field modifies piecemeal the built-in default values from
&quot;k8s.io/kubernetes/pkg/features/kube_features.go&quot;.
Default: nil
   -->
   <p><code>featureGates</code>是一個從功能特性名稱到布林值的對映，用來啟用或禁用實驗性的功能。
此欄位可逐條更改檔案 &quot;k8s.io/kubernetes/pkg/features/kube_features.go&quot;
中所給的內建預設值。</p>
   <p>預設值：nil</p>
</td>
</tr>

<tr><td><code>failSwapOn</code><br/>
<code>bool</code>
</td>
<td>
   <!--failSwapOn tells the Kubelet to fail to start if swap is enabled on the node.
Default: true
   -->
   <p><code>failSwapOn</code>通知 kubelet 在節點上啟用交換分割槽時拒絕啟動。</p>
   <p>預設值：true</p>
</td>
</tr>

<tr><td><code>memorySwap</code><br/>
<a href="#kubelet-config-k8s-io-v1beta1-MemorySwapConfiguration"><code>MemorySwapConfiguration</code></a>
</td>
<td>
   <!--memorySwap configures swap memory available to container workloads.
   -->
   <p><code>memorySwap</code>配置容器負載可用的交換記憶體。</p>
</td>
</tr>

<tr><td><code>containerLogMaxSize</code><br/>
<code>string</code>
</td>
<td>
   <!--containerLogMaxSize is a quantity defining the maximum size of the container log
file before it is rotated. For example: &quot;5Mi&quot; or &quot;256Ki&quot;.
Default: &quot;10Mi&quot;
   -->
   <p><code>containerLogMaxSize</code>是定義容器日誌檔案被輪轉之前可以到達的最大尺寸。
例如：&quot;5Mi&quot; 或 &quot;256Ki&quot;。</p>
   <p>預設值：&quot;10Mi&quot;</p>
</td>
</tr>

<tr><td><code>containerLogMaxFiles</code><br/>
<code>int32</code>
</td>
<td>
   <!--containerLogMaxFiles specifies the maximum number of container log files that can
be present for a container.
Default: 5
   -->
   <p><code>containerLogMaxFiles</code>設定每個容器可以存在的日誌檔案個數上限。</p>
   <p>預設值：&quot;5&quot;</p>
</td>
</tr>

<tr><td><code>configMapAndSecretChangeDetectionStrategy</code><br/>
<a href="#kubelet-config-k8s-io-v1beta1-ResourceChangeDetectionStrategy"><code>ResourceChangeDetectionStrategy</code></a>
</td>
<td>
   <!--
   <p>configMapAndSecretChangeDetectionStrategy is a mode in which ConfigMap and Secret
managers are running. Valid values include:</p>
<ul>
<li><code>Get</code>: kubelet fetches necessary objects directly from the API server;</li>
<li><code>Cache</code>: kubelet uses TTL cache for object fetched from the API server;</li>
<li><code>Watch</code>: kubelet uses watches to observe changes to objects that are in its interest.</li>
</ul>
<p>Default: &quot;Watch&quot;</p>
   -->
   <p><code>configMapAndSecretChangeDetectionStrategy</code>是 ConfigMap 和 Secret
管理器的執行模式。合法值包括：</p>
   <ul>
    <li><code>Get</code>：kubelet 從 API 伺服器直接取回必要的物件；</li>
    <li><code>Cache</code>：kubelet 使用 TTL 快取來管理來自 API 伺服器的物件；</li>
    <li><code>Watch</code>：kubelet 使用 watch 操作來觀察所關心的物件的變更。</li>
    </ul>
   <p>預設值：&quot;Watch&quot;</p>
</td>
</tr>

<tr><td><code>systemReserved</code><br/>
<code>map[string]string</code>
</td>
<td>
   <!--systemReserved is a set of ResourceName=ResourceQuantity (e.g. cpu=200m,memory=150G)
pairs that describe resources reserved for non-kubernetes components.
Currently only cpu and memory are supported.
See http://kubernetes.io/docs/user-guide/compute-resources for more detail.
Default: nil
   -->
   <p><code>systemReserved</code>是一組<code>資源名稱=資源數量</code>對，
用來描述為非 Kubernetes 元件預留的資源（例如：'cpu=200m,memory=150G'）。</p>
   <p>目前僅支援 CPU 和記憶體。更多細節可參見 http://kubernetes.io/zh-cn/docs/user-guide/compute-resources。</p>
   <p>預設值：Nil</p>
</td>
</tr>

<tr><td><code>kubeReserved</code><br/>
<code>map[string]string</code>
</td>
<td>
   <!--kubeReserved is a set of ResourceName=ResourceQuantity (e.g. cpu=200m,memory=150G) pairs
that describe resources reserved for kubernetes system components.
Currently cpu, memory and local storage for root file system are supported.
See https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/
for more details.
Default: nil
   -->
   <p><code>kubeReserved</code>是一組<code>資源名稱=資源數量</code>對，
用來描述為 Kubernetes 系統元件預留的資源（例如：'cpu=200m,memory=150G'）。
目前支援 CPU、記憶體和根檔案系統的本地儲存。
更多細節可參見 https://kubernetes.io/zh-cn/docs/concepts/configuration/manage-resources-containers/。</p>
   <p>預設值：Nil</p>
</td>
</tr>

<tr><td><code>reservedSystemCPUs</code> <B>[必需]</B><br/>
<code>string</code>
</td>
<td>
   <!--The reservedSystemCPUs option specifies the CPU list reserved for the host
level system threads and kubernetes related threads. This provide a &quot;static&quot;
CPU list rather than the &quot;dynamic&quot; list by systemReserved and kubeReserved.
This option does not support systemReservedCgroup or kubeReservedCgroup.
   -->
   <p><code>reservedSystemCPUs</code>選項設定為宿主級系統執行緒和 Kubernetes
相關執行緒所預留的 CPU 列表。此欄位提供的是一種“靜態”的 CPU 列表，而不是像
<code>systemReserved</code>和<code>kubeReserved</code>所提供的“動態”列表。
此選項不支援<code>systemReservedCgroup</code>或<code>kubeReservedCgroup</code>。</p>
</td>
</tr>

<tr><td><code>showHiddenMetricsForVersion</code><br/>
<code>string</code>
</td>
<td>
   <!--showHiddenMetricsForVersion is the previous version for which you want to show
hidden metrics.
Only the previous minor version is meaningful, other values will not be allowed.
The format is <code>&lt;major&gt;.&lt;minor&gt;</code>, e.g.: <code>1.16</code>.
The purpose of this format is make sure you have the opportunity to notice
if the next release hides additional metrics, rather than being surprised
when they are permanently removed in the release after that.
Default: &quot;&quot;
   -->
   <p><code>showHiddenMetricsForVersion<code>是你希望顯示隱藏度量值的上一版本。
只有上一個次版本是有意義的，其他值都是不允許的。
欄位值的格式為<code>&lt;major&gt;.&lt;minor&gt;</code>，例如：<code>1.16</code>。
此格式的目的是為了確保在下一個版本中有新的度量值被隱藏時，你有機會注意到這類變化，
而不是當這些度量值在其後的版本中徹底去除時來不及應對。</p>
   <p>預設值：&quot;&quot;</p>
</td>
</tr>

<tr><td><code>systemReservedCgroup</code><br/>
<code>string</code>
</td>
<td>
   <!--systemReservedCgroup helps the kubelet identify absolute name of top level CGroup used
to enforce <code>systemReserved</code> compute resource reservation for OS system daemons.
Refer to <a href="https://git.k8s.io/community/contributors/design-proposals/node/node-allocatable.md">Node Allocatable</a>
doc for more information.
Default: &quot;&quot;
   -->
   <p><code>systemReservedCgroup</code>幫助 kubelet 識別用來為 OS 系統級守護程序實施
<code>systemReserved</code>計算資源預留時使用的頂級控制組（CGroup）。
參考 <a href="https://git.k8s.io/community/contributors/design-proposals/node/node-allocatable.md">Node Allocatable</a>
以瞭解詳細資訊。</p>
   <p>預設值：&quot;&quot;</p>
</td>
</tr>


<tr><td><code>kubeReservedCgroup</code><br/>
<code>string</code>
</td>
<td>
   <!--kubeReservedCgroup helps the kubelet identify absolute name of top level CGroup used
to enforce `KubeReserved` compute resource reservation for Kubernetes node system daemons.
Refer to <a href="https://git.k8s.io/community/contributors/design-proposals/node/node-allocatable.md">Node Allocatable</a>
doc for more information.
Default: ""
   -->
   <p><code>kubeReservedCgroup</code> 幫助 kubelet 識別用來為 Kubernetes 節點系統級守護程序實施
<code>kubeReserved</code>計算資源預留時使用的頂級控制組（CGroup）。
參閱 <a href="https://git.k8s.io/community/contributors/design-proposals/node/node-allocatable.md">Node Allocatable</a>
瞭解進一步的資訊。</p>
   <p>預設值：&quot;&quot;</p>
</td>
</tr>

<tr><td><code>enforceNodeAllocatable</code><br/>
<code>[]string</code>
</td>
<td>
   <!--This flag specifies the various Node Allocatable enforcements that Kubelet needs to perform.
This flag accepts a list of options. Acceptable options are <code>none</code>, <code>pods</code>,
<code>system-reserved</code> and <code>kube-reserved</code>.
If <code>none</code> is specified, no other options may be specified.
When <code>system-reserved</code> is in the list, systemReservedCgroup must be specified.
When <code>kube-reserved</code> is in the list, kubeReservedCgroup must be specified.
This field is supported only when <code>cgroupsPerQOS</code> is set to true.
Refer to <a href="https://git.k8s.io/community/contributors/design-proposals/node/node-allocatable.md">Node Allocatable</a>
for more information.
Default: [&quot;pods&quot;]
   -->
   <p>此標誌設定 kubelet 需要執行的各類節點可分配資源策略。此欄位接受一組選項列表。
可接受的選項有<code>none</code>、<code>pods</code>、<code>system-reserved</code>和
<code>kube-reserved</code>。</p>
   <p>如果設定了<code>none</code>，則欄位值中不可以包含其他選項。</p>
   <p>如果列表中包含<code>system-reserved</code>，則必須設定<code>systemReservedCgroup</code>。</p>
   <p>如果列表中包含<code>kube-reserved</code>，則必須設定<code>kubeReservedCgroup</code>。</p>
   <p>這個欄位只有在<code>cgroupsPerQOS</code>被設定為<code>true</code>才被支援。</p>
   <p>參閱<a href="https://git.k8s.io/community/contributors/design-proposals/node/node-allocatable.md">Node Allocatable</a>
瞭解進一步的資訊。</p>
   <p>預設值：[&quot;pods&quot;]</p>
</td>
</tr>

<tr><td><code>allowedUnsafeSysctls</code><br/>
<code>[]string</code>
</td>
<td>
   <!--A comma separated whitelist of unsafe sysctls or sysctl patterns (ending in <code>*</code>).
Unsafe sysctl groups are <code>kernel.shm*</code>, <code>kernel.msg*</code>, <code>kernel.sem</code>, <code>fs.mqueue.*</code>,
and <code>net.*</code>. For example: &quot;<code>kernel.msg*,net.ipv4.route.min_pmtu</code>&quot;
Default: []
  -->
   <p>用逗號分隔的白名單列表，其中包含不安全的 sysctl 或 sysctl 模式（以<code>&lowast;</code>結尾）。
</p>
   <p>不安全的 sysctl 組有 <code>kernel.shm&lowast;</code>、<code>kernel.msg&lowast;</code>、
<code>kernel.sem</code>、<code>fs.mqueue.&lowast;</code> 和<code>net.&lowast;</code>。</p>
   <p>例如：&quot;<code>kernel.msg&lowast;,net.ipv4.route.min\_pmtu</code>&quot;</p>
   <p>預設值：[]</p>
</td>
</tr>

<tr><td><code>volumePluginDir</code><br/>
<code>string</code>
</td>
<td>
   <!--volumePluginDir is the full path of the directory in which to search
for additional third party volume plugins.
Default: &quot;/usr/libexec/kubernetes/kubelet-plugins/volume/exec/&quot;
   -->
   <p><code>volumePluginDir</code>是用來搜尋其他第三方卷外掛的目錄的路徑。</p>
   <p>預設值：&quot;/usr/libexec/kubernetes/kubelet-plugins/volume/exec/&quot;</p>
</td>
</tr>

<tr><td><code>providerID</code><br/>
<code>string</code>
</td>
<td>
   <!--providerID, if set, sets the unique ID of the instance that an external
provider (i.e. cloudprovider) can use to identify a specific node.
Default: &quot;quot;
   -->
   <p><code>providerID</code>欄位被設定時，指定的是一個外部提供者（即雲驅動）例項的唯一 ID，
該提供者可用來唯一性地標識特定節點。</p>
   <p>預設值：&quot;&quot;</p>
</td>
</tr>

<tr><td><code>kernelMemcgNotification</code><br/>
<code>bool</code>
</td>
<td>
   <!--kernelMemcgNotification, if set, instructs the the kubelet to integrate with the
kernel memcg notification for determining if memory eviction thresholds are
exceeded rather than polling.
Default: false
   -->
   <p><code>kernelMemcgNotification</code>欄位如果被設定了，會告知 kubelet 整合核心的
memcg 通知機制來確定是否超出記憶體逐出閾值，而不是使用輪詢機制來判定。</p>
   <p>預設值：false</p>
</td>
</tr>

<tr><td><code>logging</code> <B>[必需]</B><br/>
<a href="#LoggingConfiguration"><code>LoggingConfiguration</code></a>
</td>
<td>
   <!--logging specifies the options of logging.
Refer to <a href="https://github.com/kubernetes/component-base/blob/master/logs/options.go">Logs Options</a>
for more information.
Default:
  Format: text
   -->
   <p><code>logging</code>設定日誌機制選項。更多的詳細資訊科參閱
<a href="https://github.com/kubernetes/component-base/blob/master/logs/options.go">日誌選項</a>。</p>
   <p>預設值：</p>
   <code><pre>Format: text</pre></code>
</td>
</tr>

<tr><td><code>enableSystemLogHandler</code><br/>
<code>bool</code>
</td>
<td>
   <!--enableSystemLogHandler enables system logs via web interface host:port/logs/
Default: true
   -->
   <p><code>enableSystemLogHandler</code>用來啟用透過 Web 介面 host:port/logs/
訪問系統日誌的能力。</p>
   <p>預設值：true</p>
</td>
</tr>

<tr><td><code>shutdownGracePeriod</code><br/>
<a href="https://pkg.go.dev/k8s.io/apimachinery/pkg/apis/meta/v1#Duration"><code>meta/v1.Duration</code></a>
</td>
<td>
   <!--shutdownGracePeriod specifies the total duration that the node should delay the
shutdown and total grace period for pod termination during a node shutdown.
Default: &quot;0s&quot;
   -->
   <p><code>shutdownGracePeriod</code>設定節點關閉期間，節點自身需要延遲以及為
Pod 提供的寬限期限的總時長。</p>
   <p>預設值：&quot;0s&quot;</p>
</td>
</tr>

<tr><td><code>shutdownGracePeriodCriticalPods</code><br/>
<a href="https://pkg.go.dev/k8s.io/apimachinery/pkg/apis/meta/v1#Duration"><code>meta/v1.Duration</code></a>
</td>
<td>
   <!--shutdownGracePeriodCriticalPods specifies the duration used to terminate critical
pods during a node shutdown. This should be less than shutdownGracePeriod.
For example, if shutdownGracePeriod=30s, and shutdownGracePeriodCriticalPods=10s,
during a node shutdown the first 20 seconds would be reserved for gracefully
terminating normal pods, and the last 10 seconds would be reserved for terminating
critical pods.
Default: &quot;0s&quot;
   -->
   <p><code>shutdownGracePeriodCriticalPods</code>設定節點關閉期間用來終止關鍵性
Pod 的時長。此時長要短於<code>shutdownGracePeriod</code>。
例如，如果<code>shutdownGracePeriod=30s</code>，<code>shutdownGracePeriodCriticalPods=10s<code>，
在節點關閉期間，前 20 秒鐘被預留用來體面終止普通 Pod，後 10 秒鐘用來終止關鍵 Pod。</p>
   <p>預設值：&quot;0s&quot;</p>
</td>
</tr>

<tr><td><code>shutdownGracePeriodByPodPriority</code><br/>
<a href="#kubelet-config-k8s-io-v1beta1-ShutdownGracePeriodByPodPriority"><code>[]ShutdownGracePeriodByPodPriority</code></a>
</td>
<td>
   <!--
   shutdownGracePeriodByPodPriority specifies the shutdown grace period for Pods based
on their associated priority class value.
When a shutdown request is received, the Kubelet will initiate shutdown on all pods
running on the node with a grace period that depends on the priority of the pod,
and then wait for all pods to exit.
Each entry in the array represents the graceful shutdown time a pod with a priority
class value that lies in the range of that value and the next higher entry in the
list when the node is shutting down.
For example, to allow critical pods 10s to shutdown, priority&gt;=10000 pods 20s to
shutdown, and all remaining pods 30s to shutdown.
-->
   <p><code>shutdownGracePeriodByPodPriority</code>設定基於 Pod
相關的優先順序類值而確定的體面關閉時間。當 kubelet 收到關閉請求的時候，kubelet
會針對節點上執行的所有 Pod 發起關閉操作，這些關閉操作會根據 Pod 的優先順序確定其寬限期限，
之後 kubelet 等待所有 Pod 退出。</p>
   <p>陣列中的每個表項代表的是節點關閉時 Pod 的體面終止時間；這裡的 Pod
的優先順序類介於列表中當前優先順序類值和下一個表項的優先順序類值之間。</p>
   <p>例如，要賦予關鍵 Pod 10 秒鐘時間來關閉，賦予優先順序&gt;=10000 Pod 20 秒鐘時間來關閉，
賦予其餘的 Pod 30 秒鐘來關閉。</p>
   <p>shutdownGracePeriodByPodPriority:</p>
   <ul>
   <li>priority: 2000000000
   shutdownGracePeriodSeconds: 10</li>
   <li>priority: 10000
   shutdownGracePeriodSeconds: 20</li>
   <li>priority: 0
   shutdownGracePeriodSeconds: 30</li>
   </ul>
<!--
The time the Kubelet will wait before exiting will at most be the maximum of all
shutdownGracePeriodSeconds for each priority class range represented on the node.
When all pods have exited or reached their grace periods, the Kubelet will release
the shutdown inhibit lock.
Requires the GracefulNodeShutdown feature gate to be enabled.
This configuration must be empty if either ShutdownGracePeriod or ShutdownGracePeriodCriticalPods is set.
Default: nil
-->
   <p>在退出之前，kubelet 要等待的時間上限為節點上所有優先順序類的
<code>shutdownGracePeriodSeconds</code>的最大值。
當所有 Pod 都退出或者到達其寬限期限時，kubelet 會釋放關閉防護鎖。
此功能要求<code>GracefulNodeShutdown</code>特性門控被啟用。</p>
   <p>當<code>shutdownGracePeriod</code>或<code>shutdownGracePeriodCriticalPods</code>
被設定時，此配置欄位必須為空。</p>
   <p>預設值：nil</p>
</td>
</tr>

<tr><td><code>reservedMemory</code><br/>
<a href="#kubelet-config-k8s-io-v1beta1-MemoryReservation"><code>[]MemoryReservation</code></a>
</td>
<td>
   <!--reservedMemory specifies a comma-separated list of memory reservations for NUMA nodes.
The parameter makes sense only in the context of the memory manager feature.
The memory manager will not allocate reserved memory for container workloads.
For example, if you have a NUMA0 with 10Gi of memory and the reservedMemory was
specified to reserve 1Gi of memory at NUMA0, the memory manager will assume that
only 9Gi is available for allocation.
You can specify a different amount of NUMA node and memory types.
You can omit this parameter at all, but you should be aware that the amount of
reserved memory from all NUMA nodes should be equal to the amount of memory specified
by the <a href="https://kubernetes.io/docs/tasks/administer-cluster/reserve-compute-resources/#node-allocatable">node allocatable</a>.
If at least one node allocatable parameter has a non-zero value, you will need
to specify at least one NUMA node.
Also, avoid specifying:</p>
<ol>
<li>Duplicates, the same NUMA node, and memory type, but with a different value.</li>
<li>zero limits for any memory type.</li>
<li>NUMAs nodes IDs that do not exist under the machine.</li>
<li>memory types except for memory and hugepages-&lt;size&gt;</li>
</ol>
<p>Default: nil</p>
-->
   <p><code>reservedMemory</code>給出一個逗號分隔的列表，為 NUMA 節點預留記憶體。</p>
   <p>此引數僅在記憶體管理器功能特性語境下有意義。記憶體管理器不會為容器負載分配預留記憶體。
例如，如果你的 NUMA0 節點記憶體為 10Gi，<code>reservedMemory</code>設定為在 NUMA0
上預留 1Gi 記憶體，記憶體管理器會認為其上只有 9Gi 記憶體可供分配。</p>
   <p>你可以設定不同數量的 NUMA 節點和記憶體型別。你也可以完全忽略這個欄位，不過你要清楚，
所有 NUMA 節點上預留記憶體的總量要等於透過
<a href="https://kubernetes.io/docs/tasks/administer-cluster/reserve-compute-resources/#node-allocatable">node allocatable</a>
設定的記憶體量。</p>
   <p>如果至少有一個節點可分配引數設定值非零，則你需要設定至少一個 NUMA 節點。</p>
   <p>此外，避免如下設定：</p>
   <ol>
   <li>在配置值中存在重複項，NUMA 節點和記憶體型別相同，但配置值不同，這是不允許的。</li>
   <li>為任何記憶體型別設定限制值為零。</li>
   <li>NUMA 節點 ID 在宿主系統上不存在。/li>
   <li>除<code>memory</code>和<code>hugepages-&lt;size&gt;</code>之外的記憶體型別。</li>
   </ol>
   <p>預設值：nil</p>
</td>
</tr>

<tr><td><code>enableProfilingHandler</code><br/>
<code>bool</code>
</td>
<td>
   <!--enableProfilingHandler enables profiling via web interface host:port/debug/pprof/
Default: true
   -->
   <p><code>enableProfilingHandler</code>啟用透過 host:port/debug/pprof/ 介面來執行效能分析。</p>
   <p>預設值：true</p>
</td>
</tr>

<tr><td><code>enableDebugFlagsHandler</code><br/>
<code>bool</code>
</td>
<td>
   <!--enableDebugFlagsHandler enables flags endpoint via web interface host:port/debug/flags/v
Default: true
   -->
   <p><code>enableDebugFlagsHandler</code>啟用透過 host:port/debug/flags/v Web
介面上的標誌設定。</p>
   <p>預設值：true</p>
</td>
</tr>

<tr><td><code>seccompDefault</code><br/>
<code>bool</code>
</td>
<td>
   <!--SeccompDefault enables the use of <code>RuntimeDefault</code> as the default seccomp profile for all workloads.
This requires the corresponding SeccompDefault feature gate to be enabled as well.
Default: false
   -->
   <p><code>seccompDefault</code>欄位允許針對所有負載將<code>RuntimeDefault</code>
設定為預設的 seccomp 配置。這一設定要求對應的<code>SeccompDefault</code>特性門控被啟用。</p>
   <p>預設值：false</p>
</td>
</tr>

<tr><td><code>memoryThrottlingFactor</code><br/>
<code>float64</code>
</td>
<td>
   <!--MemoryThrottlingFactor specifies the factor multiplied by the memory limit or node allocatable memory
when setting the cgroupv2 memory.high value to enforce MemoryQoS.
Decreasing this factor will set lower high limit for container cgroups and put heavier reclaim pressure
while increasing will put less reclaim pressure.
See http://kep.k8s.io/2570 for more details.
Default: 0.8
   -->
   <p>當設定 cgroupv2 <code>memory.high</code>以實施<code>MemoryQoS</code>特性時，
<code>memoryThrottlingFactor</code>用來作為記憶體限制或節點可分配記憶體的係數。</p>
   <p>減小此係數會為容器控制組設定較低的 high 限制值，從而增大回收壓力；反之，
增大此係數會降低迴收壓力。更多細節參見 http://kep.k8s.io/2570。</p>
   <p>預設值：0.8</p>
</td>
</tr>

<tr><td><code>registerWithTaints</code><br/>
<a href="https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.23/#taint-v1-core"><code>[]core/v1.Taint</code></a>
</td>
<td>
   <!--registerWithTaints are an array of taints to add to a node object when
the kubelet registers itself. This only takes effect when registerNode
is true and upon the initial registration of the node.
Default: nil
   -->
   <p><code>registerWithTaints</code>是一個由汙點組成的陣列，包含 kubelet
註冊自身時要向節點物件新增的汙點。只有<code>registerNode</code>為<code>true</code>
時才會起作用，並且僅在節點的最初註冊時起作用。</p>
   <p>預設值：nil</p>
</td>
</tr>

<tr><td><code>registerNode</code><br/>
<code>bool</code>
</td>
<td>
   <!--registerNode enables automatic registration with the apiserver.
Default: true
   -->
   <p><code>registerNode</code>啟用向 API 伺服器的自動註冊。</p>
   <p>預設值：true</p>
</td>
</tr>
</tbody>
</table>

## `SerializedNodeConfigSource`     {#kubelet-config-k8s-io-v1beta1-SerializedNodeConfigSource}

<!--
SerializedNodeConfigSource allows us to serialize v1.NodeConfigSource.
This type is used internally by the Kubelet for tracking checkpointed dynamic configs.
It exists in the kubeletconfig API group because it is classified as a versioned input to the Kubelet.
-->
SerializedNodeConfigSource 允許對 `v1.NodeConfigSource` 執行序列化操作。
這一型別供 kubelet 內部使用，以便跟蹤動態配置的檢查點。
此資源存在於 kubeletconfig API 組是因為它被當做是對 kubelet 的一種版本化輸入。

<table class="table">
<thead><tr><th width="30%"><!--Field-->欄位</th><th><!--Description-->描述</th></tr></thead>
<tbody>

<tr><td><code>apiVersion</code><br/>string</td><td><code>kubelet.config.k8s.io/v1beta1</code></td></tr>
<tr><td><code>kind</code><br/>string</td><td><code>SerializedNodeConfigSource</code></td></tr>

<tr><td><code>source</code><br/>
<a href="https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.23/#nodeconfigsource-v1-core"><code>core/v1.NodeConfigSource</code></a>
</td>
<td>
   <!--source is the source that we are serializing.
   -->
   <p><code>source</code>是我們執行序列化的資料來源。</p>
</td>
</tr>

</tbody>
</table>

## `CredentialProvider`     {#kubelet-config-k8s-io-v1beta1-CredentialProvider}

<!--
**Appears in:**
-->
**出現在：**

- [CredentialProviderConfig](#kubelet-config-k8s-io-v1beta1-CredentialProviderConfig)

<!--
CredentialProvider represents an exec plugin to be invoked by the kubelet. The plugin is only
invoked when an image being pulled matches the images handled by the plugin (see matchImages).
-->
CredentialProvider 代表的是要被 kubelet 呼叫的一個 exec 外掛。
這一外掛只會在所拉取的映象與該外掛所處理的映象匹配時才會被呼叫（參見 <code>matchImages</code>）。

<table class="table">
<thead><tr><th width="30%"><!--Field-->欄位</th><th><!--Description-->描述</th></tr></thead>
<tbody>

<tr><td><code>name</code> <B><!--[Required]-->[必需]</B><br/>
<code>string</code>
</td>
<td>
<!--
name is the required name of the credential provider. It must match the name of the
provider executable as seen by the kubelet. The executable must be in the kubelet's
bin directory (set by the --image-credential-provider-bin-dir flag).
-->
   <p>
   <code>name</code> 是憑據提供者的名稱（必需）。此名稱必須與 kubelet
   所看到的提供者可執行檔案的名稱匹配。可執行檔案必須位於 kubelet 的 
   <code>bin</code> 目錄（透過 <code>--image-credential-provider-bin-dir</code> 設定）下。
   </p>
</td>
</tr>
<tr><td><code>matchImages</code> <B><!--[Required]-->[必需]</B><br/>
<code>[]string</code>
</td>
<td>
<!--
matchImages is a required list of strings used to match against images in order to
determine if this provider should be invoked. If one of the strings matches the
requested image from the kubelet, the plugin will be invoked and given a chance
to provide credentials. Images are expected to contain the registry domain
and URL path.
-->
<p><code>matchImages</code> 是一個必須設定的字串列表，用來匹配映象以便確定是否要呼叫此提供者。
如果字串之一與 kubelet 所請求的映象匹配，則此外掛會被呼叫並給予提供憑證的機會。
映象應該包含映象庫域名和 URL 路徑。</p>
<!--
Each entry in matchImages is a pattern which can optionally contain a port and a path.
Globs can be used in the domain, but not in the port or the path. Globs are supported
as subdomains like '<em>.k8s.io' or 'k8s.</em>.io', and top-level-domains such as 'k8s.<em>'.
Matching partial subdomains like 'app</em>.k8s.io' is also supported. Each glob can only match
a single subdomain segment, so *.io does not match *.k8s.io.
-->
<p><code>matchImages</code> 中的每個條目都是一個模式字串，其中可以包含埠號和路徑。
域名部分可以包含統配符，但埠或路徑部分不可以。萬用字元可以用作子域名，例如
'*.k8s.io' 或 'k8s.*.io'，以及頂級域名，如 'k8s.*'。</p>
<p>對類似 'app*.k8s.io' 這類部分子域名的匹配也是支援的。
每個萬用字元只能用來匹配一個子域名段，所以 '*.io' 不會匹配 '*.k8s.io'。</p>
<!--
A match exists between an image and a matchImage when all of the below are true:
-->
<p>映象與 <code>matchImages</code> 之間存在匹配時，以下條件都要滿足：</p>
<ul>
<!--
<li>Both contain the same number of domain parts and each part matches.</li>
<li>The URL path of an imageMatch must be a prefix of the target image URL path.</li>
<li>If the imageMatch contains a port, then the port must match in the image as well.</li>
-->
  <li>二者均包含相同個數的域名部分，並且每個域名部分都對應匹配；</li>
  <li><code>matchImages</code> 條目中的 URL 路徑部分必須是目標映象的 URL 路徑的字首；</li>
  <li>如果 <code>matchImages</code> 條目中包含埠號，則埠號也必須與映象埠號匹配。</li>
</ul>
<!--
Example values of matchImages:
-->
<p><code>matchImages</code> 的一些示例如下：</p>
<ul>
<li>123456789.dkr.ecr.us-east-1.amazonaws.com</li>
<li>*.azurecr.io</li>
<li>gcr.io</li>
<li><em>.</em>.registry.io</li>
<li>registry.io:8080/path</li>
</ul>
</td>
</tr>
<tr><td><code>defaultCacheDuration</code> <B><!--[Required]-->[必需]</B><br/>
<a href="https://pkg.go.dev/k8s.io/apimachinery/pkg/apis/meta/v1#Duration"><code>meta/v1.Duration</code></a>
</td>
<td>
<!--
defaultCacheDuration is the default duration the plugin will cache credentials in-memory
if a cache duration is not provided in the plugin response. This field is required.
-->
   <p>
   <code>defaultCacheDuration</code> 是外掛在記憶體中快取憑據的預設時長，
   在外掛響應中沒有給出快取時長時，使用這裡設定的值。此欄位是必需的。
   </p>
</td>
</tr>
<tr><td><code>apiVersion</code> <B><!--[Required]-->[必需]</B><br/>
<code>string</code>
</td>
<td>
<!--
Required input version of the exec CredentialProviderRequest. The returned CredentialProviderResponse
MUST use the same encoding version as the input. Current supported values are:
-->
   <p>
   要求 exec 外掛 CredentialProviderRequest 請求的輸入版本。
   所返回的 CredentialProviderResponse 必須使用與輸入相同的編碼版本。當前支援的值有：
   </p>
<ul>
<li>credentialprovider.kubelet.k8s.io/v1beta1</li>
</ul>
</td>
</tr>
<tr><td><code>args</code><br/>
<code>[]string</code>
</td>
<td>
<!--
Arguments to pass to the command when executing it.
-->
   <p>在執行外掛可執行檔案時要傳遞給命令的引數。</p>
</td>
</tr>
<tr><td><code>env</code><br/>
<a href="#kubelet-config-k8s-io-v1beta1-ExecEnvVar"><code>[]ExecEnvVar</code></a>
</td>
<td>
<!--
Env defines additional environment variables to expose to the process. These
are unioned with the host's environment, as well as variables client-go uses
to pass argument to the plugin.
-->
   <p>
   <code>env</code> 定義要提供給外掛程序的額外的環境變數。
   這些環境變數會與主機上的其他環境變數以及 client-go 所使用的環境變數組合起來，
   一起傳遞給外掛。
   </p>
</td>
</tr>
</tbody>
</table>

## `ExecEnvVar`     {#kubelet-config-k8s-io-v1beta1-ExecEnvVar}

<!--
**Appears in:**
-->
**出現在：**

- [CredentialProvider](#kubelet-config-k8s-io-v1beta1-CredentialProvider)

<!--
ExecEnvVar is used for setting environment variables when executing an exec-based
credential plugin.
-->
ExecEnvVar 用來在執行基於 exec 的憑據外掛時設定環境變數。

<table class="table">
<thead><tr><th width="30%"><!--Field-->欄位</th><th><!--Description-->描述</th></tr></thead>
<tbody>

<tr><td><code>name</code> <B><!--[Required]-->[必需]</B><br/>
<code>string</code>
</td>
<td>
   <span class="text-muted">
   <!--
   No description provided.
   -->
   無描述
   </span>
</td>
</tr>
<tr><td><code>value</code> <B><!--[Required]-->[必需]</B><br/>
<code>string</code>
</td>
<td>
   <span class="text-muted">
   <!--
   No description provided.
   -->
   無描述
   </span>
</td>
</tr>
</tbody>
</table>


## `KubeletAnonymousAuthentication`     {#kubelet-config-k8s-io-v1beta1-KubeletAnonymousAuthentication}

<!--
**Appears in:**
-->
**出現在：**

- [KubeletAuthentication](#kubelet-config-k8s-io-v1beta1-KubeletAuthentication)

<table class="table">
<thead><tr><th width="30%"><!--Field-->欄位</th><th><!--Description-->描述</th></tr></thead>
<tbody>

<tr><td><code>enabled</code><br/>
<code>bool</code>
</td>
<td>
   <!--
   <p>enabled allows anonymous requests to the kubelet server.
Requests that are not rejected by another authentication method are treated as
anonymous requests.
Anonymous requests have a username of <code>system:anonymous</code>, and a group name of
<code>system:unauthenticated</code>.</p>
   -->
   <p><code>enabled</code>允許匿名使用者向 kubelet 伺服器傳送請求。
未被其他身份認證方法拒絕的請求都會被當做匿名請求。
匿名請求對應的使用者名稱為<code>system:anonymous</code>，對應的使用者組名為
<code>system:unauthenticated</code>。</p>
</td>
</tr>
</tbody>
</table>

## `KubeletAuthentication`     {#kubelet-config-k8s-io-v1beta1-KubeletAuthentication}

<!--
**Appears in:**
-->
**出現在：**

- [KubeletConfiguration](#kubelet-config-k8s-io-v1beta1-KubeletConfiguration)

<table class="table">
<thead><tr><th width="30%"><!--Field-->欄位</th><th><!--Description-->描述</th></tr></thead>
<tbody>

<tr><td><code>x509</code><br/>
<a href="#kubelet-config-k8s-io-v1beta1-KubeletX509Authentication"><code>KubeletX509Authentication</code></a>
</td>
<td>
   <!--x509 contains settings related to x509 client certificate authentication.
   -->
   <p><code>x509</code>包含與 x509 客戶端證書認證相關的配置。</p>
</td>
</tr>

<tr><td><code>webhook</code><br/>
<a href="#kubelet-config-k8s-io-v1beta1-KubeletWebhookAuthentication"><code>KubeletWebhookAuthentication</code></a>
</td>
<td>
   <!--webhook contains settings related to webhook bearer token authentication.-->
   <p><code>webhook</code>包含與 Webhook 持有者令牌認證相關的配置。</p>
</td>
</tr>

<tr><td><code>anonymous</code><br/>
<a href="#kubelet-config-k8s-io-v1beta1-KubeletAnonymousAuthentication"><code>KubeletAnonymousAuthentication</code></a>
</td>
<td>
   <!--anonymous contains settings related to anonymous authentication.-->
   <p><code>anonymous</code>包含與匿名身份認證相關的配置資訊。</p>
</td>
</tr>
</tbody>
</table>

## `KubeletAuthorization`     {#kubelet-config-k8s-io-v1beta1-KubeletAuthorization}

<!--
**Appears in:**
-->
**出現在：**

- [KubeletConfiguration](#kubelet-config-k8s-io-v1beta1-KubeletConfiguration)

<table class="table">
<thead><tr><th width="30%"><!--Field-->欄位</th><th><!--Description-->描述</th></tr></thead>
<tbody>

<tr><td><code>mode</code><br/>
<a href="#kubelet-config-k8s-io-v1beta1-KubeletAuthorizationMode"><code>KubeletAuthorizationMode</code></a>
</td>
<td>
   <!--mode is the authorization mode to apply to requests to the kubelet server.
Valid values are <code>AlwaysAllow</code> and <code>Webhook</code>.
Webhook mode uses the SubjectAccessReview API to determine authorization.</p>
   -->
   <p><code>mode>是應用到 kubelet 伺服器所接收到的請求上的鑑權模式。合法值包括
<code>AlwaysAllow</code>和<code>Webhook</code>。
Webhook 模式使用 <code>SubjectAccessReview</code> API 來確定鑑權。</p>
</td>
</tr>

<tr><td><code>webhook</code><br/>
<a href="#kubelet-config-k8s-io-v1beta1-KubeletWebhookAuthorization"><code>KubeletWebhookAuthorization</code></a>
</td>
<td>
   <!--webhook contains settings related to Webhook authorization.-->
   <p><code>webhook</code>包含與 Webhook 鑑權相關的配置資訊。</p>
</td>
</tr>
</tbody>
</table>

## `KubeletAuthorizationMode`     {#kubelet-config-k8s-io-v1beta1-KubeletAuthorizationMode}

<!--
(Alias of `string`)
-->
（`string` 型別的別名）

<!--
**Appears in:**
-->
**出現在：**

- [KubeletAuthorization](#kubelet-config-k8s-io-v1beta1-KubeletAuthorization)

## `KubeletWebhookAuthentication`     {#kubelet-config-k8s-io-v1beta1-KubeletWebhookAuthentication}

<!--
**Appears in:**
-->
**出現在：**

- [KubeletAuthentication](#kubelet-config-k8s-io-v1beta1-KubeletAuthentication)

<table class="table">
<thead><tr><th width="30%"><!--Field-->欄位</th><th><!--Description-->描述</th></tr></thead>
<tbody>

<tr><td><code>enabled</code><br/>
<code>bool</code>
</td>
<td>
   <!--enabled allows bearer token authentication backed by the
tokenreviews.authentication.k8s.io API.-->
   <p><code>enabled</code>允許使用<code>tokenreviews.authentication.k8s.io</code>
API 來提供持有者令牌身份認證。</p>
</td>
</tr>

<tr><td><code>cacheTTL</code><br/>
<a href="https://pkg.go.dev/k8s.io/apimachinery/pkg/apis/meta/v1#Duration"><code>meta/v1.Duration</code></a>
</td>
<td>
   <!--cacheTTL enables caching of authentication results-->
   <p><code>cacheTTL</code>啟用對身份認證結果的快取。</p>
</td>
</tr>
</tbody>
</table>

## `KubeletWebhookAuthorization`     {#kubelet-config-k8s-io-v1beta1-KubeletWebhookAuthorization}

<!--
**Appears in:**
-->
**出現在：**

- [KubeletAuthorization](#kubelet-config-k8s-io-v1beta1-KubeletAuthorization)

<table class="table">
<thead><tr><th width="30%"><!--Field-->欄位</th><th><!--Description-->描述</th></tr></thead>
<tbody>

<tr><td><code>cacheAuthorizedTTL</code><br/>
<a href="https://pkg.go.dev/k8s.io/apimachinery/pkg/apis/meta/v1#Duration"><code>meta/v1.Duration</code></a>
</td>
<td>
   <!--cacheAuthorizedTTL is the duration to cache 'authorized' responses from the
webhook authorizer.-->
   <p><code>cacheAuthorizedTTL</code>設定來自 Webhook 鑑權元件的 'authorized'
響應的快取時長。</p>
</td>
</tr>
<tr><td><code>cacheUnauthorizedTTL</code><br/>
<a href="https://pkg.go.dev/k8s.io/apimachinery/pkg/apis/meta/v1#Duration"><code>meta/v1.Duration</code></a>
</td>
<td>
   <!--cacheUnauthorizedTTL is the duration to cache 'unauthorized' responses from
the webhook authorizer.-->
   <p><code>cacheUnauthorizedTTL</code>設定來自 Webhook 鑑權元件的 'unauthorized'
響應的快取時長。</p>
</td>
</tr>
</tbody>
</table>

## `KubeletX509Authentication`     {#kubelet-config-k8s-io-v1beta1-KubeletX509Authentication}

<!--
**Appears in:**
-->
**出現在：**

- [KubeletAuthentication](#kubelet-config-k8s-io-v1beta1-KubeletAuthentication)

<table class="table">
<thead><tr><th width="30%"><!--Field-->欄位</th><th><!--Description-->描述</th></tr></thead>
<tbody>

<tr><td><code>clientCAFile</code><br/>
<code>string</code>
</td>
<td>
   <!--clientCAFile is the path to a PEM-encoded certificate bundle. If set, any request
presenting a client certificate signed by one of the authorities in the bundle
is authenticated with a username corresponding to the CommonName,
and groups corresponding to the Organization in the client certificate.-->
   <p><code>clientCAFile</code>是一個指向 PEM 編髮的證書包的路徑。
如果設定了此欄位，則能夠提供由此證書包中機構之一所簽名的客戶端證書的請求會被成功認證，
並且其使用者名稱對應於客戶端證書的<code>CommonName</code>、組名對應於客戶端證書的
<code>Organization</code>。</p>
</td>
</tr>
</tbody>
</table>

## `MemoryReservation`     {#kubelet-config-k8s-io-v1beta1-MemoryReservation}

<!--
**Appears in:**
-->
**出現在：**

- [KubeletConfiguration](#kubelet-config-k8s-io-v1beta1-KubeletConfiguration)

<!--
MemoryReservation specifies the memory reservation of different types for each NUMA node
-->
MemoryReservation 為每個 NUMA 節點設定不同型別的記憶體預留。

<table class="table">
<thead><tr><th width="30%"><!--Field-->欄位</th><th><!--Description-->描述</th></tr></thead>
<tbody>

<tr><td><code>numaNode</code> <B>[必需]</B><br/>
<code>int32</code>
</td>
<td>
   <!--span class="text-muted">No description provided.</span-->
   <p>NUMA 節點</p>
</td>
</tr>

<tr><td><code>limits</code> <B>[必需]</B><br/>
<a href="https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.23/#resourcelist-v1-core"><code>core/v1.ResourceList</code></a>
</td>
<td>
   <!--span class="text-muted">No description provided.</span-->
   <p>資源列表</p>
</td>
</tr>
</tbody>
</table>

## `MemorySwapConfiguration`     {#kubelet-config-k8s-io-v1beta1-MemorySwapConfiguration}

<!--
**Appears in:**
-->
**出現在：**

- [KubeletConfiguration](#kubelet-config-k8s-io-v1beta1-KubeletConfiguration)

<table class="table">
<thead><tr><th width="30%"><!--Field-->欄位</th><th><!--Description-->描述</th></tr></thead>
<tbody>

<tr><td><code>swapBehavior</code><br/>
<code>string</code>
</td>
<td>
   <!--swapBehavior configures swap memory available to container workloads. May be one of
&quot;&quot;, &quot;LimitedSwap&quot;: workload combined memory and swap usage cannot exceed pod memory limit
&quot;UnlimitedSwap&quot;: workloads can use unlimited swap, up to the allocatable limit.
   -->
   <p><code>swapBehavior</code>配置容器負載可以使用的交換記憶體。可以是
   <ul>
    <li>&quot;&quot;、&quot;LimitedSwap&quot;：工作負載的記憶體和交換分割槽總用量不能超過 Pod 的記憶體限制；</li>
    <li>&quot;UnlimitedSwap&quot;：工作負載可以無限制地使用交換分割槽，上限是可分配的約束。</li>
   </ul>
</td>
</tr>
</tbody>
</table>

## `ResourceChangeDetectionStrategy`     {#kubelet-config-k8s-io-v1beta1-ResourceChangeDetectionStrategy}

<!--
(Alias of `string`)
-->
（`string` 型別的別名）

<!--
**Appears in:**
-->
**出現在：**

- [KubeletConfiguration](#kubelet-config-k8s-io-v1beta1-KubeletConfiguration)

<!--
ResourceChangeDetectionStrategy denotes a mode in which internal
managers (secret, configmap) are discovering object changes.
-->
ResourceChangeDetectionStrategy 給出的是內部管理器（ConfigMap、Secret）
用來發現物件變化的模式。

## `ShutdownGracePeriodByPodPriority`     {#kubelet-config-k8s-io-v1beta1-ShutdownGracePeriodByPodPriority}

<!--
**Appears in:**
-->
**出現在：**

- [KubeletConfiguration](#kubelet-config-k8s-io-v1beta1-KubeletConfiguration)

<!--
ShutdownGracePeriodByPodPriority specifies the shutdown grace period for Pods based on their associated priority class value
-->
ShutdownGracePeriodByPodPriority 基於 Pod 關聯的優先順序類數值來為其設定關閉寬限時間。

<table class="table">
<thead><tr><th width="30%"><!--Field-->欄位</th><th><!--Description-->描述</th></tr></thead>
<tbody>

<tr><td><code>priority</code> <B>[必需]</B><br/>
<code>int32</code>
</td>
<td>
   <!--priority is the priority value associated with the shutdown grace period-->
   <p><code>priority</code>是與關閉寬限期限相關聯的優先順序值。</p>
</td>
</tr>

<tr><td><code>shutdownGracePeriodSeconds</code> <B>[必需]</B><br/>
<code>int64</code>
</td>
<td>
   <!--shutdownGracePeriodSeconds is the shutdown grace period in seconds-->
   <p><code>shutdownGracePeriodSeconds</code>是按秒數給出的關閉寬限期限。
</td>
</tr>
</tbody>
</table>

## `FormatOptions`     {#FormatOptions}

<!--
**Appears in:**
-->
**出現在：**

- [LoggingConfiguration](#LoggingConfiguration)

<p>
<!--
FormatOptions contains options for the different logging formats.
-->
FormatOptions 包含為不同日誌格式提供的選項。
</p>

<table class="table">
<thead><tr><th width="30%"><!--Field-->欄位</th><th><!--Description-->描述</th></tr></thead>
<tbody>

<tr><td><code>json</code> <B>[必需]</B><br/>
<a href="#JSONOptions"><code>JSONOptions</code></a>
</td>
<td>
   <p><!--[Experimental] JSON contains options for logging format &quot;json&quot;.-->
   [試驗功能] <code>json</code> 包含為 &quot;json&quot; 日誌格式提供的選項。
   </p>
</td>
</tr>
</tbody>
</table>

## `JSONOptions`     {#JSONOptions}

<!--
**Appears in:**
-->
**出現在：**

- [FormatOptions](#FormatOptions)

<p>
<!--
JSONOptions contains options for logging format &quot;json&quot;.
-->
JSONOptions 包含為 &quot;json&quot; 日誌格式提供的選項。
</p>

<table class="table">
<thead><tr><th width="30%"><!--Field-->欄位</th><th><!--Description-->描述</th></tr></thead>
<tbody>
<tr><td><code>splitStream</code> <B>[必需]</B><br/>
<code>bool</code>
</td>
<td>
   <p>
   <!--[Experimental] SplitStream redirects error messages to stderr while
info messages go to stdout, with buffering. The default is to write
both to stdout, without buffering.
   -->
   [試驗功能] <code>splitStream</code> 將錯誤資訊重定向到標準錯誤輸出（stderr），
而將提示資訊重定向到標準輸出（stdout），併為二者提供快取。
預設設定是將二者都寫出到標準輸出，並且不提供快取。
   </p>
</td>
</tr>

<tr><td><code>infoBufferSize</code> <B>[必需]</B><br/>
<a href="https://pkg.go.dev/k8s.io/apimachinery/pkg/api/resource#QuantityValue"><code>k8s.io/apimachinery/pkg/api/resource.QuantityValue</code></a>
</td>
<td>
   <p>
   <!--[Experimental] InfoBufferSize sets the size of the info stream when
using split streams. The default is zero, which disables buffering.-->
   [試驗功能] <code>infoBufferSize</coe> 在分離資料流時用來設定提示資料流的大小。
預設值為 0，相當於禁止快取。
   </p>
</td>
</tr>
</tbody>
</table>

## `LoggingConfiguration`     {#LoggingConfiguration}

<!--
**Appears in:**
-->
**出現在：**

- [KubeletConfiguration](#kubelet-config-k8s-io-v1beta1-KubeletConfiguration)

<!--
LoggingConfiguration contains logging options
Refer [Logs Options](https://github.com/kubernetes/component-base/blob/master/logs/options.go) for more information.
-->
LoggingConfiguration 包含日誌選項。
參考 [Logs Options](https://github.com/kubernetes/component-base/blob/master/logs/options.go)
以瞭解更多資訊。

<table class="table">
<thead><tr><th width="30%"><!--Field-->欄位</th><th><!--Description-->描述</th></tr></thead>
<tbody>

<tr><td><code>format</code> <B>[必需]</B><br/>
<code>string</code>
</td>
<td>
  <p>
  <!--Format Flag specifies the structure of log messages.
default value of format is `text`-->
  <code>format<code> 設定日誌訊息的結構。預設的格式取值為 <code>text</code>。
  </p>
</td>
</tr>

<tr><td><code>flushFrequency</code> <B>[必需]</B><br/>
<a href="https://pkg.go.dev/time#Duration"><code>time.Duration</code></a>
</td>
<td>
  <p>
  <!--
   Maximum number of nanoseconds (i.e. 1s = 1000000000) between log
   flushes.  Ignored if the selected logging backend writes log
   messages without buffering.
  -->
   對日誌進行清洗的最大間隔納秒數（例如，1s = 1000000000）。
   如果所選的日誌後端在寫入日誌訊息時不提供快取，則此配置會被忽略。
  </p>
</td>
</tr>

<tr><td><code>verbosity</code> <B>[必需]</B><br/>
<code>uint32</code>
</td>
<td>
  <p>
  <!--Verbosity is the threshold that determines which log messages are
logged. Default is zero which logs only the most important
messages. Higher values enable additional messages. Error messages
are always logged.-->
  <code>verbosity</code> 用來確定日誌訊息記錄的詳細程度閾值。預設值為 0，
意味著僅記錄最重要的訊息。數值越大，額外的訊息越多。出錯訊息總是會被記錄下來。
  </p>
</td>
</tr>

<tr><td><code>vmodule</code> <B>[必需]</B><br/>
<a href="#VModuleConfiguration"><code>VModuleConfiguration</code></a>
</td>
<td>
  <p>
  <!--VModule overrides the verbosity threshold for individual files.
Only supported for &quot;text&quot; log format.-->
  <code>vmodule</code> 會在單個檔案層面過載 verbosity 閾值的設定。
這一選項僅支援 &quot;text&quot; 日誌格式。
  </p>
</td>
</tr>

<tr><td><code>options</code> <B>[必需]</B><br/>
<a href="#FormatOptions"><code>FormatOptions</code></a>
</td>
<td>
  <p>
  <!--[Experimental] Options holds additional parameters that are specific
to the different logging formats. Only the options for the selected
format get used, but all of them get validated.-->
  [試驗功能] <code>options</code> 中包含特定於不同日誌格式的配置引數。
只有針對所選格式的選項會被使用，但是合法性檢查時會檢視所有選項配置。
  </p>
</td>
</tr>
</tbody>
</table>

## `VModuleConfiguration`     {#VModuleConfiguration}

<!--
(Alias of `[]k8s.io/component-base/config/v1alpha1.VModuleItem`)
-->
（`[]k8s.io/component-base/config/v1alpha1.VModuleItem` 型別的別名）

<!--
**Appears in:**
-->
**出現在：**

- [LoggingConfiguration](#LoggingConfiguration)

<!--
VModuleConfiguration is a collection of individual file names or patterns
and the corresponding verbosity threshold.
-->
VModuleConfiguration 是一個集合，其中包含一個個檔名（或檔名模式）
及其對應的詳細程度閾值。

