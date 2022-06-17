---
title: 容忍度（Toleration）
id: toleration
date: 2019-01-11
full_link: /zh-cn/docs/concepts/scheduling-eviction/taint-and-toleration/
short_description: >
  一個核心物件，由三個必需的屬性組成：key、value 和 effect。
  容忍度允許將 Pod 排程到具有對應汙點的節點或節點組上。

aka:
tags:
- core-object
- fundamental
---

<!--
title: Toleration
id: toleration
date: 2019-01-11
full_link: /docs/concepts/scheduling-eviction/taint-and-toleration/
short_description: >
  A core object consisting of three required properties: key, value, and effect. Tolerations enable the scheduling of pods on nodes or node groups that have a matching taint.
  
aka:
tags:
- core-object
- fundamental
-->

<!--
 A core object consisting of three required properties: key, value, and effect. Tolerations enable the scheduling of pods on nodes or node groups that have matching {{< glossary_tooltip text="taints" term_id="taint" >}}.
-->
一個核心物件，由三個必需的屬性組成：key、value 和 effect。
容忍度允許將 Pod 排程到具有對應{{< glossary_tooltip text="汙點" term_id="taint" >}}
的節點或節點組上。
 
<!--more-->

<!--
Tolerations and {{< glossary_tooltip text="taints" term_id="taint" >}} work together to ensure that pods are not scheduled onto inappropriate nodes. One or more tolerations are applied to a {{< glossary_tooltip text="pod" term_id="pod" >}}. A toleration indicates that the {{< glossary_tooltip text="pod" term_id="pod" >}} is allowed (but not required) to be scheduled on nodes or node groups with matching {{< glossary_tooltip text="taints" term_id="taint" >}}.
-->
容忍度和{{< glossary_tooltip text="汙點" term_id="taint" >}}共同作用可以
確保不會將 Pod 排程在不適合的節點上。
在同一 {{< glossary_tooltip text="Pod" term_id="pod" >}} 上可以設定一個
或者多個容忍度。
容忍度表示在包含對應{{< glossary_tooltip text="汙點" term_id="taint" >}}
的節點或節點組上排程 {{< glossary_tooltip text="Pod" term_id="pod" >}}
是允許的（但不必要）。

