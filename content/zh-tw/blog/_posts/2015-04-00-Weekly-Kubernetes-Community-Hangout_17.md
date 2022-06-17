---
title: " Kubernetes 社群每週聚會筆記- 2015年4月17日 "
date: 2015-04-17
slug: weekly-kubernetes-community-hangout_17
---

<!--
---
title: " Weekly Kubernetes Community Hangout Notes - April 17 2015 "
date: 2015-04-17
slug: weekly-kubernetes-community-hangout_17
url: /zh-cn/blog/2015/04/Weekly-Kubernetes-Community-Hangout_17
---
-->

<!--
Every week the Kubernetes contributing community meet virtually over Google Hangouts. We want anyone who's interested to know what's discussed in this forum.
-->
每個星期，Kubernetes 貢獻者社群幾乎都會在谷歌 Hangouts 上聚會。我們希望任何對此感興趣的人都能瞭解這個論壇的討論內容。

<!--
Agenda

* Mesos Integration
* High Availability (HA)
* Adding performance and profiling details to e2e to track regressions
* Versioned clients

-->
議程

* Mesos 整合
* 高可用性（HA）
* 向 e2e 新增效能和分析詳細資訊以跟蹤迴歸
* 客戶端版本化

<!--
Notes
-->
筆記

<!--

* Mesos integration

    * Mesos integration proposal:

    * No blockers to integration.

    * Documentation needs to be updated.

-->

* Mesos 整合

    * Mesos 整合提案：

    * 沒有阻塞整合的因素。

    * 文件需要更新。

<!--

* HA

    * Proposal should land today.

    * Etcd cluster.

    * Load-balance apiserver.

    * Cold standby for controller manager and other master components.

-->

* HA

    * 提案今天應該會提交。

    * Etcd 叢集。

    * apiserver 負載均衡。

    * 控制器管理器和其他主元件的冷備用。

<!--

* Adding performance and profiling details to e2e to track regression

    * Want red light for performance regression

    * Need a public DB to post the data

        * See

    * Justin working on multi-platform e2e dashboard

-->

* 向 e2e 新增效能和分析詳細資訊以跟蹤迴歸

    * 希望紅色為效能迴歸

    * 需要公共資料庫才能釋出資料

        * 檢視

    * Justin 致力於多平臺 e2e 儀表盤

<!--

* Versioned clients

    *

    *

    * Client library currently uses internal API objects.

    * Nobody reported that frequent changes to types.go have been painful, but we are worried about it.

    * Structured types are useful in the client. Versioned structs would be ok.

    * If start with json/yaml (kubectl), shouldn’t convert to structured types. Use swagger.

-->

* 客戶端版本化

    *

    *

    * 客戶端庫當前使用內部 API 物件。

    * 儘管沒有人反映頻繁修改 `types.go` 有多痛苦，但我們很為此擔心。

    * 結構化型別在客戶端中很有用。版本化的結構就可以了。

    * 如果從 json/yaml (kubectl) 開始，則不應轉換為結構化型別。使用 swagger。

<!--

* Security context

    *

    * Administrators can restrict who can run privileged containers or require specific unix uids

    * Kubelet will be able to get pull credentials from apiserver

    * Policy proposal coming in the next week or so
-->

* Security context

    *

    * 管理員可以限制誰可以執行特權容器或需要特定的 unix uid

    * kubelet 將能夠從 apiserver 獲取證書

    * 政策提案將於下週左右出臺

<!--

* Discussing upstreaming of users, etc. into Kubernetes, at least as optional
* 1.0 Roadmap

    * Focus is performance, stability, cluster upgrades

    * TJ has been making some edits to [roadmap.md][4] but hasn’t sent out a PR yet
* Kubernetes UI

    * Dependencies broken out into third-party

    * @lavalamp is reviewer

-->

* 討論使用者的上游，等等進入Kubernetes，至少是可選的
* 1.0 路線圖

    * 重點是效能，穩定性，叢集升級

    * TJ 一直在對[roadmap.md][4]進行一些編輯，但尚未釋出PR
* Kubernetes UI

    * 依賴關係分解為第三方

    * @lavalamp 是評論家


[1]: http://kubernetes.io/images/nav_logo.svg
[2]: http://kubernetes.io/docs/
[3]: https://kubernetes.io/blog/
[4]: https://github.com/GoogleCloudPlatform/kubernetes/blob/master/docs/roadmap.md
[5]: https://kubernetes.io/blog/2015/04/weekly-kubernetes-community-hangout_17 "permanent link"
[6]: https://resources.blogblog.com/img/icon18_edit_allbkg.gif
[7]: https://www.blogger.com/post-edit.g?blogID=112706738355446097&postID=630924463010638300&from=pencil "Edit Post"
[8]: https://www.blogger.com/share-post.g?blogID=112706738355446097&postID=630924463010638300&target=email "Email This"
[9]: https://www.blogger.com/share-post.g?blogID=112706738355446097&postID=630924463010638300&target=blog "BlogThis!"
[10]: https://www.blogger.com/share-post.g?blogID=112706738355446097&postID=630924463010638300&target=twitter "Share to Twitter"
[11]: https://www.blogger.com/share-post.g?blogID=112706738355446097&postID=630924463010638300&target=facebook "Share to Facebook"
[12]: https://www.blogger.com/share-post.g?blogID=112706738355446097&postID=630924463010638300&target=pinterest "Share to Pinterest"
[13]: https://kubernetes.io/blog/search/label/community%20meetings
[14]: https://kubernetes.io/blog/search/label/containers
[15]: https://kubernetes.io/blog/search/label/docker
[16]: https://kubernetes.io/blog/search/label/k8s
[17]: https://kubernetes.io/blog/search/label/kubernetes
[18]: https://kubernetes.io/blog/search/label/open%20source
[19]: https://kubernetes.io/blog/2015/04/kubernetes-and-mesosphere-dcos "Newer Post"
[20]: https://kubernetes.io/blog/2015/04/introducing-kubernetes-v1beta3 "Older Post"
[21]: https://kubernetes.io/blog/feeds/630924463010638300/comments/default
[22]: https://img2.blogblog.com/img/widgets/arrow_dropdown.gif
[23]: https://img1.blogblog.com/img/icon_feed12.png
[24]: https://img1.blogblog.com/img/widgets/subscribe-netvibes.png
[25]: https://www.netvibes.com/subscribe.php?url=http%3A%2F%2Fblog.kubernetes.io%2Ffeeds%2Fposts%2Fdefault
[26]: https://img1.blogblog.com/img/widgets/subscribe-yahoo.png
[27]: https://add.my.yahoo.com/content?url=http%3A%2F%2Fblog.kubernetes.io%2Ffeeds%2Fposts%2Fdefault
[28]: https://kubernetes.io/blog/feeds/posts/default
[29]: https://www.netvibes.com/subscribe.php?url=http%3A%2F%2Fblog.kubernetes.io%2Ffeeds%2F630924463010638300%2Fcomments%2Fdefault
[30]: https://add.my.yahoo.com/content?url=http%3A%2F%2Fblog.kubernetes.io%2Ffeeds%2F630924463010638300%2Fcomments%2Fdefault
[31]: https://resources.blogblog.com/img/icon18_wrench_allbkg.png
[32]: //www.blogger.com/rearrange?blogID=112706738355446097&widgetType=Subscribe&widgetId=Subscribe1&action=editWidget§ionId=sidebar-right-1 "Edit"
[33]: https://twitter.com/kubernetesio
[34]: https://github.com/kubernetes/kubernetes
[35]: http://slack.k8s.io/
[36]: http://stackoverflow.com/questions/tagged/kubernetes
[37]: http://get.k8s.io/
[38]: //www.blogger.com/rearrange?blogID=112706738355446097&widgetType=HTML&widgetId=HTML2&action=editWidget§ionId=sidebar-right-1 "Edit"
[39]: javascript:void(0)
[40]: https://kubernetes.io/blog/2018/
[41]: https://kubernetes.io/blog/2018/01/
[42]: https://kubernetes.io/blog/2017/
[43]: https://kubernetes.io/blog/2017/12/
[44]: https://kubernetes.io/blog/2017/11/
[45]: https://kubernetes.io/blog/2017/10/
[46]: https://kubernetes.io/blog/2017/09/
[47]: https://kubernetes.io/blog/2017/08/
[48]: https://kubernetes.io/blog/2017/07/
[49]: https://kubernetes.io/blog/2017/06/
[50]: https://kubernetes.io/blog/2017/05/
[51]: https://kubernetes.io/blog/2017/04/
[52]: https://kubernetes.io/blog/2017/03/
[53]: https://kubernetes.io/blog/2017/02/
[54]: https://kubernetes.io/blog/2017/01/
[55]: https://kubernetes.io/blog/2016/
[56]: https://kubernetes.io/blog/2016/12/
[57]: https://kubernetes.io/blog/2016/11/
[58]: https://kubernetes.io/blog/2016/10/
[59]: https://kubernetes.io/blog/2016/09/
[60]: https://kubernetes.io/blog/2016/08/
[61]: https://kubernetes.io/blog/2016/07/
[62]: https://kubernetes.io/blog/2016/06/
[63]: https://kubernetes.io/blog/2016/05/
[64]: https://kubernetes.io/blog/2016/04/
[65]: https://kubernetes.io/blog/2016/03/
[66]: https://kubernetes.io/blog/2016/02/
[67]: https://kubernetes.io/blog/2016/01/
[68]: https://kubernetes.io/blog/2015/
[69]: https://kubernetes.io/blog/2015/12/
[70]: https://kubernetes.io/blog/2015/11/
[71]: https://kubernetes.io/blog/2015/10/
[72]: https://kubernetes.io/blog/2015/09/
[73]: https://kubernetes.io/blog/2015/08/
[74]: https://kubernetes.io/blog/2015/07/
[75]: https://kubernetes.io/blog/2015/06/
[76]: https://kubernetes.io/blog/2015/05/
[77]: https://kubernetes.io/blog/2015/04/
[78]: https://kubernetes.io/blog/2015/04/weekly-kubernetes-community-hangout_29
[79]: https://kubernetes.io/blog/2015/04/borg-predecessor-to-kubernetes
[80]: https://kubernetes.io/blog/2015/04/kubernetes-and-mesosphere-dcos
[81]: https://kubernetes.io/blog/2015/04/weekly-kubernetes-community-hangout_17
[82]: https://kubernetes.io/blog/2015/04/introducing-kubernetes-v1beta3
[83]: https://kubernetes.io/blog/2015/04/kubernetes-release-0150
[84]: https://kubernetes.io/blog/2015/04/weekly-kubernetes-community-hangout_11
[85]: https://kubernetes.io/blog/2015/04/faster-than-speeding-latte
[86]: https://kubernetes.io/blog/2015/04/weekly-kubernetes-community-hangout
[87]: https://kubernetes.io/blog/2015/03/
[88]: //www.blogger.com/rearrange?blogID=112706738355446097&widgetType=BlogArchive&widgetId=BlogArchive1&action=editWidget§ionId=sidebar-right-1 "Edit"
[89]: //www.blogger.com/rearrange?blogID=112706738355446097&widgetType=HTML&widgetId=HTML1&action=editWidget§ionId=sidebar-right-1 "Edit"
[90]: https://www.blogger.com
[91]: //www.blogger.com/rearrange?blogID=112706738355446097&widgetType=Attribution&widgetId=Attribution1&action=editWidget§ionId=footer-3 "Edit"

  [*[3:27 PM]: 2015-04-17T15:27:00-07:00
