---
title: Event Rate Limit Configuration (v1alpha1)
content_type: tool-reference
package: eventratelimit.admission.k8s.io/v1alpha1
---

<!--
## Resource Types
-->
## 資源型別  {#resource-types}

- [Configuration](#eventratelimit-admission-k8s-io-v1alpha1-Configuration)

## `Configuration`     {#eventratelimit-admission-k8s-io-v1alpha1-Configuration}

<!--
<p>Configuration provides configuration for the EventRateLimit admission
controller.</p>
-->
<p>Configuration 為 EventRateLimit 准入控制器提供配置資料。</p>

<table class="table">
<thead><tr><th width="30%"><!--Field-->欄位</th><th><!--Description-->描述</th></tr></thead>
<tbody>

<tr><td><code>apiVersion</code><br/>string</td><td><code>eventratelimit.admission.k8s.io/v1alpha1</code></td></tr>
<tr><td><code>kind</code><br/>string</td><td><code>Configuration</code></td></tr>

<tr><td><code>limits</code> <B>[Required]</B><br/>
<a href="#eventratelimit-admission-k8s-io-v1alpha1-Limit"><code>[]Limit</code></a>
</td>
<td>
  <!--
   <p>limits are the limits to place on event queries received.
Limits can be placed on events received server-wide, per namespace,
per user, and per source+object.
At least one limit is required.</p>
  -->
  <p>limits 是為所接收到的事件查詢設定的限制。可以針對伺服器端接收到的事件設定限制，
按逐個名字空間、逐個使用者、或逐個來源+物件組合的方式均可以。
至少需要設定一種限制。</p>
</td>
</tr>
</tbody>
</table>

## `Limit`     {#eventratelimit-admission-k8s-io-v1alpha1-Limit}

<!--
**Appears in:**
-->
**出現在：**

- [Configuration](#eventratelimit-admission-k8s-io-v1alpha1-Configuration)

<!--
<p>Limit is the configuration for a particular limit type</p>
-->
<p>Limit 是為特定限制類型提供的配置資料。</p>

<table class="table">
<thead><tr><th width="30%"><!--Field-->欄位</th><th><!--Description-->描述</th></tr></thead>
<tbody>

<tr><td><code>type</code> <B><!--[Required]-->[必需]</B><br/>
<a href="#eventratelimit-admission-k8s-io-v1alpha1-LimitType"><code>LimitType</code></a>
</td>
<td>
  <!--
   <p>type is the type of limit to which this configuration applies</p>
  -->
  <p>type 是此配置所適用的限制的型別。</p>
</td>
</tr>
<tr><td><code>qps</code> <B><!--[Required]-->[必需]</B><br/>
<code>int32</code>
</td>
<td>
  <!--
   <p>qps is the number of event queries per second that are allowed for this
type of limit. The qps and burst fields are used together to determine if
a particular event query is accepted. The qps determines how many queries
are accepted once the burst amount of queries has been exhausted.</p>
  -->
   <p>qps 是針對此型別的限制每秒鐘所允許的事件查詢次數。qps 和 burst
欄位一起用來確定是否特定的事件查詢會被接受。qps 確定的是當超出查詢數量的
burst 值時可以接受的查詢個數。</p>
</td>
</tr>
<tr><td><code>burst</code> <B><!--[Required]-->[必需]</B><br/>
<code>int32</code>
</td>
<td>
  <!--
   <p>burst is the burst number of event queries that are allowed for this type
of limit. The qps and burst fields are used together to determine if a
particular event query is accepted. The burst determines the maximum size
of the allowance granted for a particular bucket. For example, if the burst
is 10 and the qps is 3, then the admission control will accept 10 queries
before blocking any queries. Every second, 3 more queries will be allowed.
If some of that allowance is not used, then it will roll over to the next
second, until the maximum allowance of 10 is reached.</p>
  -->
   <p>burst 是針對此型別限制的突發事件查詢數量。qps 和 burst 欄位一起使用可用來確定特定的事件查詢是否被接受。
burst 欄位確定針對特定的事件桶（bucket）可以接受的規模上限。
例如，如果 burst 是 10，qps 是 3，那麼准入控制器會在接收 10 個查詢之後阻塞所有查詢。
每秒鐘可以額外允許 3 個查詢。如果這一限額未被用盡，則剩餘的限額會被順延到下一秒鐘，
直到再次達到 10 個限額的上限。</p>
</td>
</tr>
<tr><td><code>cacheSize</code><br/>
<code>int32</code>
</td>
<td>
  <!--
   <p>cacheSize is the size of the LRU cache for this type of limit. If a bucket
is evicted from the cache, then the allowance for that bucket is reset. If
more queries are later received for an evicted bucket, then that bucket
will re-enter the cache with a clean slate, giving that bucket a full
allowance of burst queries.</p>
<p>The default cache size is 4096.</p>
<p>If limitType is 'server', then cacheSize is ignored.</p>
  -->
   <p>cacheSize 是此型別限制的 LRU 快取的規模。如果某個事件桶（bucket）被從快取中剔除，
該事件桶所對應的限額也會被重置。如果後來再次收到針對某個已被剔除的事件桶的查詢，
則該事件桶會重新以乾淨的狀態進入快取，因而獲得全量的突發查詢配額。</p>
  <p>預設的快取大小是 4096。</p>
  <p>如果 limitType 是 “server”，則 cacheSize 設定會被忽略。</p>
</td>
</tr>
</tbody>
</table>

## `LimitType`     {#eventratelimit-admission-k8s-io-v1alpha1-LimitType}

<!--
(Alias of `string`)

**Appears in:**
-->
（`string` 型別的別名）

**出現在：**

- [Limit](#eventratelimit-admission-k8s-io-v1alpha1-Limit)

<!--
<p>LimitType is the type of the limit (e.g., per-namespace)</p>
-->
<p>LimitType 是限制類型（例如：per-namespace）。</p>


