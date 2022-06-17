---
title: 客戶端庫
content_type: concept
weight: 30
---

<!--
title: Client Libraries
reviewers:
- ahmetb
content_type: concept
weight: 30
-->

<!-- overview -->
<!--
This page contains an overview of the client libraries for using the Kubernetes
API from various programming languages.
-->
本頁面包含基於各種程式語言使用 Kubernetes API 的客戶端庫概述。


<!-- body -->
<!--
To write applications using the [Kubernetes REST API](/docs/reference/using-api/),
you do not need to implement the API calls and request/response types yourself.
You can use a client library for the programming language you are using.
-->
在使用 [Kubernetes REST API](/zh-cn/docs/reference/using-api/) 編寫應用程式時，
你並不需要自己實現 API 呼叫和 “請求/響應” 型別。
你可以根據自己的程式語言需要選擇使用合適的客戶端庫。

<!--
Client libraries often handle common tasks such as authentication for you.
Most client libraries can discover and use the Kubernetes Service Account to
authenticate if the API client is running inside the Kubernetes cluster, or can
understand the [kubeconfig file](/docs/tasks/access-application-cluster/configure-access-multiple-clusters/)
format to read the credentials and the API Server address.
-->
客戶端庫通常為你處理諸如身份驗證之類的常見任務。
如果 API 客戶端在 Kubernetes 叢集中執行，大多數客戶端庫可以發現並使用 Kubernetes 服務帳戶進行身份驗證，
或者能夠理解 [kubeconfig 檔案](/zh-cn/docs/tasks/access-application-cluster/configure-access-multiple-clusters/)
格式來讀取憑據和 API 伺服器地址。

<!--
## Officially-supported Kubernetes client libraries
-->
## 官方支援的 Kubernetes 客戶端庫  {#officially-supported-kubernetes-client-libraries}

<!--
The following client libraries are officially maintained by
[Kubernetes SIG API Machinery](https://github.com/kubernetes/community/tree/master/sig-api-machinery).
-->
以下客戶端庫由 [Kubernetes SIG API Machinery](https://github.com/kubernetes/community/tree/master/sig-api-machinery) 正式維護。

<!--
| Language | Client Library | Sample Programs |
|----------|----------------|-----------------|
| dotnet   | [github.com/kubernetes-client/csharp](https://github.com/kubernetes-client/csharp) | [browse](https://github.com/kubernetes-client/csharp/tree/master/examples/simple)
| Go       | [github.com/kubernetes/client-go/](https://github.com/kubernetes/client-go/) | [browse](https://github.com/kubernetes/client-go/tree/master/examples)
| Haskell  | [github.com/kubernetes-client/haskell](https://github.com/kubernetes-client/haskell) | [browse](https://github.com/kubernetes-client/haskell/tree/master/kubernetes-client/example)
| Java     | [github.com/kubernetes-client/java](https://github.com/kubernetes-client/java/) | [browse](https://github.com/kubernetes-client/java#installation)
| JavaScript   | [github.com/kubernetes-client/javascript](https://github.com/kubernetes-client/javascript) | [browse](https://github.com/kubernetes-client/javascript/tree/master/examples)
| Python   | [github.com/kubernetes-client/python/](https://github.com/kubernetes-client/python/) | [browse](https://github.com/kubernetes-client/python/tree/master/examples)
-->
|   語言  |     客戶端庫    |     樣例程式    |
|---------|-----------------|-----------------|
| dotnet   | [github.com/kubernetes-client/csharp](https://github.com/kubernetes-client/csharp) | [瀏覽](https://github.com/kubernetes-client/csharp/tree/master/examples/simple)
| Go       | [github.com/kubernetes/client-go/](https://github.com/kubernetes/client-go/) | [瀏覽](https://github.com/kubernetes/client-go/tree/master/examples)
| Haskell  | [github.com/kubernetes-client/haskell](https://github.com/kubernetes-client/haskell) | [瀏覽](https://github.com/kubernetes-client/haskell/tree/master/kubernetes-client/example)
| Java     | [github.com/kubernetes-client/java](https://github.com/kubernetes-client/java/) | [瀏覽](https://github.com/kubernetes-client/java#installation)
| JavaScript   | [github.com/kubernetes-client/javascript](https://github.com/kubernetes-client/javascript) | [瀏覽](https://github.com/kubernetes-client/javascript/tree/master/examples)
| Python   | [github.com/kubernetes-client/python/](https://github.com/kubernetes-client/python/) | [瀏覽](https://github.com/kubernetes-client/python/tree/master/examples)

<!--
## Community-maintained client libraries
-->
## 社群維護的客戶端庫  {#community-maintained-client-libraries}

{{% thirdparty-content %}}

<!--
The following Kubernetes API client libraries are provided and maintained by
their authors, not the Kubernetes team.
-->
以下 Kubernetes API 客戶端庫是由社群，而非 Kubernetes 團隊支援、維護的。

<!--
| Language             | Client Library                           |
| -------------------- | ---------------------------------------- |
| Clojure              | [github.com/yanatan16/clj-kubernetes-api](https://github.com/yanatan16/clj-kubernetes-api) |
| DotNet               | [github.com/tonnyeremin/kubernetes_gen](https://github.com/tonnyeremin/kubernetes_gen) |
| DotNet (RestSharp)   | [github.com/masroorhasan/Kubernetes.DotNet](https://github.com/masroorhasan/Kubernetes.DotNet) |
| Elixir               | [github.com/obmarg/kazan](https://github.com/obmarg/kazan/) |
| Elixir               | [github.com/coryodaniel/k8s](https://github.com/coryodaniel/k8s) |
| Go                   | [github.com/ericchiang/k8s](https://github.com/ericchiang/k8s) |
| Java (OSGi)          | [bitbucket.org/amdatulabs/amdatu-kubernetes](https://bitbucket.org/amdatulabs/amdatu-kubernetes) |
| Java (Fabric8, OSGi) | [github.com/fabric8io/kubernetes-client](https://github.com/fabric8io/kubernetes-client) |
| Java                 | [github.com/manusa/yakc](https://github.com/manusa/yakc) |
| Lisp                 | [github.com/brendandburns/cl-k8s](https://github.com/brendandburns/cl-k8s) |
| Lisp                 | [github.com/xh4/cube](https://github.com/xh4/cube) |
| Node.js (TypeScript) | [github.com/Goyoo/node-k8s-client](https://github.com/Goyoo/node-k8s-client) |
| Node.js              | [github.com/ajpauwels/easy-k8s](https://github.com/ajpauwels/easy-k8s)
| Node.js              | [github.com/godaddy/kubernetes-client](https://github.com/godaddy/kubernetes-client) |
| Node.js              | [github.com/tenxcloud/node-kubernetes-client](https://github.com/tenxcloud/node-kubernetes-client) |
| Perl                 | [metacpan.org/pod/Net::Kubernetes](https://metacpan.org/pod/Net::Kubernetes) |
| PHP                  | [github.com/allansun/kubernetes-php-client](https://github.com/allansun/kubernetes-php-client) |
| PHP                  | [github.com/maclof/kubernetes-client](https://github.com/maclof/kubernetes-client) |
| PHP                  | [github.com/travisghansen/kubernetes-client-php](https://github.com/travisghansen/kubernetes-client-php) |
| PHP                  | [github.com/renoki-co/php-k8s](https://github.com/renoki-co/php-k8s) |
| Python               | [github.com/fiaas/k8s](https://github.com/fiaas/k8s) |
| Python               | [github.com/gtsystem/lightkube](https://github.com/gtsystem/lightkube) |
| Python               | [github.com/mnubo/kubernetes-py](https://github.com/mnubo/kubernetes-py) |
| Python               | [github.com/tomplus/kubernetes_asyncio](https://github.com/tomplus/kubernetes_asyncio) |
| Python               | [github.com/Frankkkkk/pykorm](https://github.com/Frankkkkk/pykorm) |
| Ruby                 | [github.com/abonas/kubeclient](https://github.com/abonas/kubeclient) |
| Ruby                 | [github.com/k8s-ruby/k8s-ruby](https://github.com/k8s-ruby/k8s-ruby) |
| Ruby                 | [github.com/kontena/k8s-client](https://github.com/kontena/k8s-client) |
| Rust                 | [github.com/clux/kube-rs](https://github.com/clux/kube-rs) |
| Rust                 | [github.com/ynqa/kubernetes-rust](https://github.com/ynqa/kubernetes-rust) |
| Scala                | [github.com/hagay3/skuber](https://github.com/hagay3/skuber) |
| Scala                | [github.com/joan38/kubernetes-client](https://github.com/joan38/kubernetes-client) |
-->
| 語言                   | 客戶端庫                                  |
|----------------------| ---------------------------------------- |
| Clojure              | [github.com/yanatan16/clj-kubernetes-api](https://github.com/yanatan16/clj-kubernetes-api) |
| DotNet               | [github.com/tonnyeremin/kubernetes_gen](https://github.com/tonnyeremin/kubernetes_gen) |
| DotNet (RestSharp)   | [github.com/masroorhasan/Kubernetes.DotNet](https://github.com/masroorhasan/Kubernetes.DotNet) |
| Elixir               | [github.com/obmarg/kazan](https://github.com/obmarg/kazan/) |
| Elixir               | [github.com/coryodaniel/k8s](https://github.com/coryodaniel/k8s) |
| Go                   | [github.com/ericchiang/k8s](https://github.com/ericchiang/k8s) |
| Java (OSGi)          | [bitbucket.org/amdatulabs/amdatu-kubernetes](https://bitbucket.org/amdatulabs/amdatu-kubernetes) |
| Java (Fabric8, OSGi) | [github.com/fabric8io/kubernetes-client](https://github.com/fabric8io/kubernetes-client) |
| Java                 | [github.com/manusa/yakc](https://github.com/manusa/yakc) |
| Lisp                 | [github.com/brendandburns/cl-k8s](https://github.com/brendandburns/cl-k8s) |
| Lisp                 | [github.com/xh4/cube](https://github.com/xh4/cube) |
| Node.js (TypeScript) | [github.com/Goyoo/node-k8s-client](https://github.com/Goyoo/node-k8s-client) |
| Node.js              | [github.com/ajpauwels/easy-k8s](https://github.com/ajpauwels/easy-k8s)
| Node.js              | [github.com/godaddy/kubernetes-client](https://github.com/godaddy/kubernetes-client) |
| Node.js              | [github.com/tenxcloud/node-kubernetes-client](https://github.com/tenxcloud/node-kubernetes-client) |
| Perl                 | [metacpan.org/pod/Net::Kubernetes](https://metacpan.org/pod/Net::Kubernetes) |
| PHP                  | [github.com/allansun/kubernetes-php-client](https://github.com/allansun/kubernetes-php-client) |
| PHP                  | [github.com/maclof/kubernetes-client](https://github.com/maclof/kubernetes-client) |
| PHP                  | [github.com/travisghansen/kubernetes-client-php](https://github.com/travisghansen/kubernetes-client-php) |
| PHP                  | [github.com/renoki-co/php-k8s](https://github.com/renoki-co/php-k8s) |
| Python               | [github.com/fiaas/k8s](https://github.com/fiaas/k8s) |
| Python               | [github.com/gtsystem/lightkube](https://github.com/gtsystem/lightkube) |
| Python               | [github.com/mnubo/kubernetes-py](https://github.com/mnubo/kubernetes-py) |
| Python               | [github.com/tomplus/kubernetes_asyncio](https://github.com/tomplus/kubernetes_asyncio) |
| Python               | [github.com/Frankkkkk/pykorm](https://github.com/Frankkkkk/pykorm) |
| Ruby                 | [github.com/abonas/kubeclient](https://github.com/abonas/kubeclient) |
| Ruby                 | [github.com/k8s-ruby/k8s-ruby](https://github.com/k8s-ruby/k8s-ruby) |
| Ruby                 | [github.com/kontena/k8s-client](https://github.com/kontena/k8s-client) |
| Rust                 | [github.com/clux/kube-rs](https://github.com/clux/kube-rs) |
| Rust                 | [github.com/ynqa/kubernetes-rust](https://github.com/ynqa/kubernetes-rust) |
| Scala                | [github.com/hagay3/skuber](https://github.com/hagay3/skuber) |
| Scala                | [github.com/joan38/kubernetes-client](https://github.com/joan38/kubernetes-client) |
| Swift                | [github.com/swiftkube/client](https://github.com/swiftkube/client) |

