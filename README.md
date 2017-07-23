# K8SMeetup 翻译流程与翻译校稿规范

time：2017-07-13 update：2017-07-20 author：xiaolong@caicloud.io

翻译背景: 分析之前很多的翻译为什么做不好，就是因为迭代太快，版本跟不上，旧的文件没翻译完，新旧文件又混一起。没有一个适合的版本管理流程，这也是我们不建议大家从官网直接提取文件的原因。

K8SMeetup 维护两个仓库：

- 一个版本管理翻译库 [kubernetes.github.io](https://github.com/k8smeetup/kubernetes.github.io) 每个月会同步一次官网的文档库。
- 一个版本中文预览库 [k8smeetup.github.io](https://github.com/k8smeetup/k8smeetup.github.io)
此库我们会将原始英文剔除，译者向上游提`pr`的时候，可以直接从预览库提取，免除删除原文的过程，以减轻译者的工作负担。

翻译流程主要讲解社区其他译者如何参与 `Kubernetes` 文档，中文化翻译的过程。翻译校稿规范主要讲解，如何预定翻译文档的校验，以提升翻译质量。版本控制与翻译文件更新样例，提示如何更新翻译文件。

**注： 所有的翻译文件，都要保留原文，尽可能一段英文，一段中文。方便大家一起`review`，保证翻译质量。**

## 一、k8s 翻译流程讲解

![](./images/k8s.io.png)

### Step 1. 领取翻译任务

任务地址：[翻译任务](https://docs.google.com/spreadsheets/d/1FDFCv9RK5nSMgLXhPrJ5k7r5QvHnNEFnXbvoFiM8v20/edit#gid=906253755)

### Step 2. 准备翻译文档

注册译者自己的 GITHUB 账户

Fork [K8SMeetup](https://github.com/k8smeetup/kubernetes.github.io) 代码库到译者自己的 Github 账户

### Step 3. 开始翻译并向K8SMeetup提交PR
译者在本地，克隆代码库
打开 https://github.com/译者账户/kubernetes.github.io
```
mkdir work
cd work
git clone https://github.com/译者账户/kubernetes.github.io.git
```
翻译文件，看一下翻译过的文件
```
cd ~/work/kubernetes.github.io
git status
```
添加修改过的文件至暂存区
```
// 例如翻译的文件是 docs/setup/index.md
git add docs/setup/index.md
```
提交到自己的本地库
```
// 翻译文件命名规则：文件夹间隔用 - 连接线代替，后面跟pr标志并加上文件版本日期
git commit -m 'setup-index-pr-2017-07-13'
// push 到译者的github仓库 (输入用户名+密码)
git push
```
在译者的代码库创建PR

### Step 4. 根据 review 意见完成修改

```
git add docs/setup/index.md
git commit -m 'setup-index-pr-2017-07-14'
git push
```

### Step 5. K8SMeetup PR合并完成，向官网推PR

步骤同 Step 2 Fork 官网库，修改内容更新至Fork库，向上游推PR。


## 二、Kubernetes 文档翻译校对

### 为什么要进行校对

文档初稿翻译难免会有不太理想的地方，所以我们希望能有更多人志愿参与校对工作，进一步完善 Kubernetes 中文文档。

### kubernetes.io 文档的构成

Kubernetes 文档由若干 `md` 和 `html` 文档构成,翻译即是将原始 `md` 和 `html`文件中需要翻译的文字用 tag 注释包起来，然后再拷贝一份进行翻译。原始英文用符号 `<!-- -->` 注释掉，每一段英文，对应一段中文，方便其他译者 review，如下例：

```
<!--
#### Kubernetes is

* **Portable**: public, private, hybrid, multi-cloud
* **Extensible**: modular, pluggable, hookable, composable
* **Self-healing**: auto-placement, auto-restart, auto-replication, auto-scaling
-->

#### Kubernetes 具有如下特点:

* **便携性**: 无论公有云、私有云、混合云还是多云架构都全面支持
* **可扩展**: 它是模块化、可插拔、可挂载、可组合的，支持各种形式的扩展
* **自修复**: 它可以自保持应用状态、可自重启、自复制、自缩放的，通过声明式语法提供了强大的自修复能力
```

### 参与规则

- 校对者只需要具备基本的 kubernetes 知识,能够理解文档中讲述的内容即可
- 校对作业以 `md/html` 为单位，但对于很大的 `md` 或 `html` 文件，也可以按主题拆分成多份
- 为了避免不必要的重复翻译或校对，翻译或校对前先在[分工及进度]中对要翻译或校对的文件进行预定
- 预定校对作业时，以文件为单位，不建议一次预定太多，希望量力而行
- 预定了某个 `md` 或 `html` 文件并不代表别人不会同时修改此文件，所以如果克隆了`git`仓库到本地，仍然要注意及时从远程仓库同步更新
- 如果某个 `md` 或 `html` 文件的校对工作进展缓慢，或某个已校对的 `md` 或 `html`文件仍有翻译问题，可以对正在校对或已经校对过的文件进行再次校对
- 由于每个月我们会同步一次最新的版本，需要译者对自己所译的文件内容更新负责
- 有任何问题，可以发 Issues 或 在微信群里讨论

### 校对步骤

- 登录github
  如果还没有github账号，先注册一个，然后登录。
- 校对预定
  点击[任务分配置表](https://docs.google.com/spreadsheets/d/1JuG_TtpWiEIi2htnvJal7-MlYJxZl8Bvf2Un1opcuwk/edit#gid=0)，预定指定的 `md` 或 `html` 文件, 在后面填上自己的 `github` 的用户名，比如 `校对预定By:@markthink`。 同时把此文件的的状态从`Translating`改成`Under Internal Review`，对于很大的文件，也可以只预定其中的一部门标题，比如：`校对预定By:[起始标题-结束标题]@markthink`
- 检查译文
  对照英文原文检查译文,可以点开对应文件的链接，对照 `md` 或 `html` 中被注释的英文原文进行检查(发现问题可以在线修改),或提交 `Comment` 给译者。
- 问题纠正
  对于译者，如果发现问题，可直接修改 `md` 或 `html`文件，对暂时不太好处理的问题可以发行issues报告。
- 状态更新
  校对完成后在[作业分工及进度]中更新校对状态，比如`校对完成By:@markthink`。同时把翻译文件的的状态从`Under Internal Review`改成`Pull Request Sent`。

## 三、版本控制与翻译文件更新样例

初始化`md`或`html`文件会有两个文件,例如：
```
/*在原始文件上翻译-保留原始英文*/
/docs/tutorials/object-management-kubectl/object-management.md
/*用于版本差异更新使用，不需要翻译*/
/docs/tutorials/object-management-kubectl/object-management-2017-7-15.md
```
有日期的文件是版本控制用的，不需要翻译 `object-management-2017-7-15.md` 在`object-management.md`原文中翻译。
每个月会同步版本文件，如果`object-management.md`未更新，不会创建新文件，如果`object-management.md` 有新的更新，会生成一个新的日期文件`object-management-2017-8-15.md`，译者的更新步骤如下：

- 更新源码库
- 比较`object-management-2017-7-15.md`和`object-management-2017-8-15.md`文件，找出新增的差异化英文
- 将新的差异化英文更新至 `object-management.md` 文件
- 删除旧版本日期文件 `object-management-2017-7-15.md`
- 提交新的PR文件，格式`object-management-pr-2017-8-15`

## 谢谢您!

Kubernetes 在社区参与中茁壮成长，K8SMeetup 总是保持翻译文件同时仅有一个最新日期的原始文件，以保持与官网文档的更新,我们非常感谢您对我们的网站和文档的贡献！
