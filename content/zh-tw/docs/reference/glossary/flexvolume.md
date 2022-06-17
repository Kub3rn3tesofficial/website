---
title: FlexVolume
id: flexvolume
date: 2018-06-25
full_link: /zh-cn/docs/concepts/storage/volumes/#flexvolume
short_description: >
  FlexVolume 是一個已棄用的介面，用於建立樹外卷外掛。
  {{< glossary_tooltip text="容器儲存介面（CSI）" term_id="csi" >}}
  是比 Flexvolume 更新的介面，它解決了 Flexvolumes 的一些問題。

aka: 
tags:
- storage 
---

<!--
title: FlexVolume
id: flexvolume
date: 2018-06-25
full_link: /docs/concepts/storage/volumes/#flexvolume
short_description: >
  FlexVolume is a deprecated interface for creating out-of-tree volume plugins. The {{< glossary_tooltip text="Container Storage Interface" term_id="csi" >}} is a newer interface that addresses several problems with FlexVolume.
aka: 
tags:
- storage 
-->
<!-- 
FlexVolume is a deprecated interface for creating out-of-tree volume plugins. The {{< glossary_tooltip text="Container Storage Interface" term_id="csi" >}} is a newer interface that addresses several problems with FlexVolume.
-->
FlexVolume 是一個已棄用的介面，用於建立樹外卷外掛。
{{< glossary_tooltip text="容器儲存介面（CSI）" term_id="csi" >}}
是比 Flexvolume 更新的介面，它解決了 Flexvolume 的一些問題。

<!--more--> 

<!-- 
FlexVolumes enable users to write their own drivers and add support for their volumes in Kubernetes. FlexVolume driver binaries and dependencies must be installed on host machines. This requires root access. The Storage SIG suggests implementing a {{< glossary_tooltip text="CSI" term_id="csi" >}} driver if possible since it addresses the limitations with FlexVolumes.
-->
Flexvolume 允許使用者編寫自己的驅動程式，並在 Kubernetes 中加入對使用者自己的資料卷的支援。
FlexVolume 驅動程式的二進位制檔案和依賴項必須安裝在主機上。
這需要 root 許可權。如果可能的話，SIG Storage 建議實現
{{< glossary_tooltip text="CSI" term_id="csi" >}} 驅動程式，
因為它解決了 Flexvolumes 的限制。

<!-- 
* [FlexVolume in the Kubernetes documentation](/docs/concepts/storage/volumes/#flexvolume)
* [More information on FlexVolumes](https://github.com/kubernetes/community/blob/master/contributors/devel/sig-storage/flexvolume.md)
* [Volume Plugin FAQ for Storage Vendors](https://github.com/kubernetes/community/blob/master/sig-storage/volume-plugin-faq.md) 
-->
* [Kubernetes 文件中的 Flexvolume](/zh-cn/docs/concepts/storage/volumes/#flexvolume)
* [更多關於 Flexvolumes 的資訊](https://github.com/kubernetes/community/blob/master/contributors/devel/sig-storage/flexvolume.md)
* [儲存供應商的卷外掛 FAQ](https://github.com/kubernetes/community/blob/master/sig-storage/volume-plugin-faq.md)
