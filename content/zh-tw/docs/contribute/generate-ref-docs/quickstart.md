---
title: 快速入門
content_type: task
weight: 40
---
<!--
title: Quickstart
content_type: task
weight: 40
-->

<!-- overview -->

<!--
This page shows how to use the `update-imported-docs.py` script to generate
the Kubernetes reference documentation. The script automates
the build setup and generates the reference documentation for a release.
-->
本頁討論如何使用 `update-imported-docs.py` 指令碼來生成 Kubernetes 參考文件。
此指令碼將構建的配置過程自動化，併為某個發行版本生成參考文件。

## {{% heading "prerequisites" %}}

{{< include "prerequisites-ref-docs.md" >}}

<!-- steps -->
<!--
## Getting the docs repository

Make sure your `website` fork is up-to-date with the `kubernetes/website` remote on
GitHub (`main` branch), and clone your `website` fork.
-->
## 獲取文件倉庫 {#getting-the-docs-repository}

確保你的 `website` 派生倉庫與 GitHub 上的 `kubernetes/website` 遠端倉庫（`main` 分支）保持同步，
並克隆你的派生倉庫。

```shell
mkdir github.com
cd github.com
git clone git@github.com:<your_github_username>/website.git
```

<!--
Determine the base directory of your clone. For example, if you followed the
preceding step to get the repository, your base directory is
`github.com/website.` The remaining steps refer to your base directory as
`<web-base>`.

{{< note>}}
If you want to change the content of the component tools and API reference,
see the [contributing upstream guide](/docs/contribute/generate-ref-docs/contribute-upstream).
{{< /note >}}
-->
確定你的克隆副本的根目錄。例如，如果你按照前面的步驟獲取了倉庫，你的根目錄
會是 `github.com/website`。接下來的步驟中，`<web-base>` 用來指代你的根目錄。

{{< note>}}
如果你希望更改構建工具和 API 參考資料，可以閱讀
[上游貢獻指南](/zh-cn/docs/contribute/generate-ref-docs/contribute-upstream).
{{< /note >}}

<!--
## Overview of update-imported-docs

The `update-imported-docs.py` script is located in the `<web-base>/update-imported-docs/`
directory.

The script builds the following references:

* Component and tool reference pages
* The `kubectl` command reference
* The Kubernetes API reference
-->
## update-imported-docs 的概述

指令碼 `update-imported-docs.py` 位於 `<web-base>/update-imported-docs/` 目錄下，
能夠生成以下參考文件：

* Kubernetes 元件和工具的參考頁面
* `kubectl` 命令參考文件
* Kubernetes API 參考文件

<!--
The `update-imported-docs.py` script generates the Kubernetes reference documentation
from the Kubernetes source code. The script creates a temporary directory
under `/tmp` on your machine and clones the required repositories: `kubernetes/kubernetes` and
`kubernetes-sigs/reference-docs` into this directory.
The script sets your `GOPATH` to this temporary directory.
Three additional environment variables are set:

* `K8S_RELEASE`
* `K8S_ROOT`
* `K8S_WEBROOT`
-->
指令碼 `update-imported-docs.py` 基於 Kubernetes 原始碼生成參考文件。
過程中會在你的機器的 `/tmp` 目錄下建立臨時目錄，克隆所需要的倉庫
`kubernetes/kubernetes` 和 `kubernetes-sigs/reference-docs` 到此臨時目錄。
指令碼會將 `GOPATH` 環境變數設定為指向此臨時目錄。
此外，指令碼會設定三個環境變數：

* `K8S_RELEASE`
* `K8S_ROOT`
* `K8S_WEBROOT`

<!--
The script requires two arguments to run successfully:

* A YAML configuration file (`reference.yml`)
* A release version, for example:`1.17`

The configuration file contains a `generate-command` field.
The `generate-command` field defines a series of build instructions
from `kubernetes-sigs/reference-docs/Makefile`. The `K8S_RELEASE` variable
determines the version of the release.
-->
指令碼需要兩個引數才能成功執行：

* 一個 YAML 配置檔案（`reference.yml`）
* 一個發行版本字串，例如：`1.17`

配置檔案中包含 `generate-command` 欄位，其中定義了一系列來自於
`kubernetes-sigs/reference-docs/Makefile` 的構建指令。
變數 `K8S_RELEASE` 用來確定所針對的發行版本。

<!--
The `update-imported-docs.py` script performs the following steps:

1. Clones the related repositories specified in a configuration file. For the
   purpose of generating reference docs, the repository that is cloned by
   default is `kubernetes-sigs/reference-docs`.
1. Runs commands under the cloned repositories to prepare the docs generator and
   then generates the HTML and Markdown files.
1. Copies the generated HTML and Markdown files to a local clone of the `<web-base>`
   repository under locations specified in the configuration file.
1. Updates `kubectl` command links from `kubectl`.md to the refer to
   the sections in the `kubectl` command reference.
-->
指令碼 `update-imported-docs.py` 執行以下步驟：

1. 克隆配置檔案中所指定的相關倉庫。就生成參考文件這一目的而言，要克隆的
   倉庫預設為 `kubernetes-sigs/reference-docs`。
1. 在所克隆的倉庫下執行命令，準備文件生成器，之後生成 HTML 和 Markdown 檔案。
1. 將所生成的 HTML 和 Markdown 檔案複製到 `<web-base>` 本地克隆副本中，
   放在配置檔案中所指定的目錄下。
1. 更新 `kubectl.md` 檔案中對 `kubectl` 命令文件的連結，使之指向 `kubectl`
   命令參考中對應的節區。

<!--
When the generated files are in your local clone of the `<web-base>`
repository, you can submit them in a [pull request](/docs/contribute/start/)
to `<web-base>`.
-->
當所生成的檔案已經被放到 `<web-base>` 目錄下，你就可以將其提交到你的派生副本中，
並基於所作提交發起[拉取請求（PR）](/docs/contribute/start/)到 k/website 倉庫。

<!--
## Configuration file format

Each configuration file may contain multiple repos that will be imported together. When
necessary, you can customize the configuration file by manually editing it. You
may create new config files for importing other groups of documents.
The following is an example of the YAML configuration file:
-->
## 配置檔案格式 {#configuration-file-format}

每個配置檔案可以包含多個被匯入的倉庫。當必要時，你可以透過手工編輯此檔案進行定製。
你也可以透過建立新的配置檔案來匯入其他文件集合。
下面是 YAML 配置檔案的一個例子：

```yaml
repos:
- name: community
  remote: https://github.com/kubernetes/community.git
  branch: master
  files:
  - src: contributors/devel/README.md
    dst: docs/imported/community/devel.md
  - src: contributors/guide/README.md
    dst: docs/imported/community/guide.md
```

<!--
Single page Markdown documents, imported by the tool, must adhere to
the [Documentation Style Guide](/docs/contribute/style/style-guide/).
-->
透過工具匯入的單頁面的 Markdown 文件必須遵從
[文件樣式指南](/zh-cn/docs/contribute/style/style-guide/)。

<!--
## Customizing reference.yml

Open `<web-base>/update-imported-docs/reference.yml` for editing.
Do not change the content for the `generate-command` field unless you understand
how the command is used to build the references.
You should not need to update `reference.yml`. At times, changes in the
upstream source code, may require changes to the configuration file
(for example: golang version dependencies and third-party library changes).
If you encounter build issues, contact the SIG-Docs team on the
[#sig-docs Kubernetes Slack channel](https://kubernetes.slack.com).
-->

## 定製 reference.yml

開啟 `<web-base>/update-imported-docs/reference.yml` 檔案進行編輯。
在不瞭解參考文件構造命令的情況下，不要更改 `generate-command` 欄位的內容。
你一般不需要更新 `reference.yml` 檔案。不過也有時候上游的原始碼發生變化，
導致需要對配置檔案進行更改（例如：Golang 版本依賴或者第三方庫發生變化）。
如果你遇到類似問題，請在 [Kubernetes Slack 的 #sig-docs 頻道](https://kubernetes.slack.com)
聯絡 SIG-Docs 團隊。

<!--
{{< note >}}
The `generate-command` is an optional entry, which can be used to run a
given command or a short script to generate the docs from within a repository.
{{< /note >}}

In `reference.yml`, `files` contains a list of `src` and `dst` fields.
The `src` field contains the location of a generated Markdown file in the cloned
`kubernetes-sigs/reference-docs` build directory, and the `dst` field specifies
where to copy this file in the cloned `kubernetes/website` repository.
For example:
-->

{{< note >}}
注意，`generate-command` 是一個可選項，用來執行指定命令或者短指令碼以在倉庫
內生成文件。
{{< /note >}}

在 `reference.yml` 檔案中，`files` 屬性包含了一組 `src` 和 `dst` 欄位。
`src` 欄位給出在所克隆的 `kubernetes-sigs/reference-docs` 構造目錄中生成的
Markdown 檔案的位置，而 `dst` 欄位則給出了對應檔案要複製到的、所克隆的
`kubernetes/website` 倉庫中的位置。例如：

```yaml
repos:
- name: reference-docs
  remote: https://github.com/kubernetes-sigs/reference-docs.git
  files:
  - src: gen-compdocs/build/kube-apiserver.md
    dst: content/en/docs/reference/command-line-tools-reference/kube-apiserver.md
  ...
```

<!--
Note that when there are many files to be copied from the same source directory
to the same destination directory, you can use wildcards in the value given to
`src`. You must provide the directory name as the value for `dst`.
For example:
-->
注意，如果從同一源目錄中有很多檔案要複製到目標目錄，你可以在為 `src` 所設定的
值中使用萬用字元。這時，為 `dst` 所設定的值必須是目錄名稱。例如：

```yaml
  files:
  - src: gen-compdocs/build/kubeadm*.md
    dst: content/en/docs/reference/setup-tools/kubeadm/generated/
```

<!--
## Running the update-imported-docs tool

You can run the `update-imported-docs.py` tool as follows:
-->
## 執行 update-imported-docs 工具

你可以用如下方式執行 `update-imported-docs.py` 工具：

```shell
cd <web-base>/update-imported-docs
./update-imported-docs.py <configuration-file.yml> <release-version>
```

<!-- For example: -->
例如：

```shell
./update-imported-docs.py reference.yml 1.17
```

<!-- Revisit: is the release configuration used -->
<!-- ## Fixing Links

The `release.yml` configuration file contains instructions to fix relative links.
To fix relative links within your imported files, set the`gen-absolute-links`
property to `true`. You can find an example of this in
[`release.yml`](https://github.com/kubernetes/website/blob/main/update-imported-docs/release.yml).
-->
## 修復連結

配置檔案 `release.yml` 中包含用來修復相對連結的指令。
若要修復匯入檔案中的相對連結，將 `gen-absolute-links` 屬性設定為 `true`。
你可以在 [`release.yml`](https://github.com/kubernetes/website/blob/main/update-imported-docs/release.yml)
檔案中找到示例。

<!--
## Adding and committing changes in kubernetes/website

List the files that were generated and copied to `<web-base>`:
-->
## 新增並提交 kubernetes/website 中的變更

列舉新生成並複製到 `<web-base>` 的檔案：

```shell
cd <web-base>
git status
```

<!--
The output shows the new and modified files. The generated output varies
depending upon changes made to the upstream source code.

### Generated component tool files
-->
輸出顯示新生成和已修改的檔案。取決於上游原始碼的修改多少，
所生成的輸出也會不同。

### 生成的 Kubernetes 元件文件

```
content/en/docs/reference/command-line-tools-reference/cloud-controller-manager.md
content/en/docs/reference/command-line-tools-reference/kube-apiserver.md
content/en/docs/reference/command-line-tools-reference/kube-controller-manager.md
content/en/docs/reference/command-line-tools-reference/kube-proxy.md
content/en/docs/reference/command-line-tools-reference/kube-scheduler.md
content/en/docs/reference/setup-tools/kubeadm/generated/kubeadm.md
content/en/docs/reference/kubectl/kubectl.md
```

<!-- ### Generated kubectl command reference files -->
### 生成的 kubectl 命令參考檔案

```
static/docs/reference/generated/kubectl/kubectl-commands.html
static/docs/reference/generated/kubectl/navData.js
static/docs/reference/generated/kubectl/scroll.js
static/docs/reference/generated/kubectl/stylesheet.css
static/docs/reference/generated/kubectl/tabvisibility.js
static/docs/reference/generated/kubectl/node_modules/bootstrap/dist/css/bootstrap.min.css
static/docs/reference/generated/kubectl/node_modules/highlight.js/styles/default.css
static/docs/reference/generated/kubectl/node_modules/jquery.scrollto/jquery.scrollTo.min.js
static/docs/reference/generated/kubectl/node_modules/jquery/dist/jquery.min.js
static/docs/reference/generated/kubectl/css/font-awesome.min.css
```

<!-- ### Generated Kubernetes API reference directories and files -->
### 生成的 Kubernetes API 參考目錄與檔案

```
static/docs/reference/generated/kubernetes-api/{{< param "version" >}}/index.html
static/docs/reference/generated/kubernetes-api/{{< param "version" >}}/js/navData.js
static/docs/reference/generated/kubernetes-api/{{< param "version" >}}/js/scroll.js
static/docs/reference/generated/kubernetes-api/{{< param "version" >}}/js/query.scrollTo.min.js
static/docs/reference/generated/kubernetes-api/{{< param "version" >}}/css/font-awesome.min.css
static/docs/reference/generated/kubernetes-api/{{< param "version" >}}/css/bootstrap.min.css
static/docs/reference/generated/kubernetes-api/{{< param "version" >}}/css/stylesheet.css
static/docs/reference/generated/kubernetes-api/{{< param "version" >}}/fonts/FontAwesome.otf
static/docs/reference/generated/kubernetes-api/{{< param "version" >}}/fonts/fontawesome-webfont.eot
static/docs/reference/generated/kubernetes-api/{{< param "version" >}}/fonts/fontawesome-webfont.svg
static/docs/reference/generated/kubernetes-api/{{< param "version" >}}/fonts/fontawesome-webfont.ttf
static/docs/reference/generated/kubernetes-api/{{< param "version" >}}/fonts/fontawesome-webfont.woff
static/docs/reference/generated/kubernetes-api/{{< param "version" >}}/fonts/fontawesome-webfont.woff2
```

<!--
Run `git add` and `git commit` to commit the files.

## Creating a pull request

Create a pull request to the `kubernetes/website` repository. Monitor your
pull request, and respond to review comments as needed. Continue to monitor
your pull request until it is merged.

A few minutes after your pull request is merged, your updated reference
topics will be visible in the
[published documentation](/docs/home/).
-->
執行 `git add` 和 `git commit` 提交檔案。

## 建立拉取請求 {#creating-a-pull-request}

接下來建立一個對 `kubernetes/website` 倉庫的拉取請求（PR）。
監視所建立的 PR，並根據需要對評閱意見給出反饋。
繼續監視該 PR 直到其被合併為止。

當你的 PR 被合併幾分鐘之後，你所做的對參考文件的變更就會出現
[釋出的文件](/zh-cn/docs/home/)上。

## {{% heading "whatsnext" %}}

<!--
To generate the individual reference documentation by manually setting up the required build repositories and
running the build targets, see the following guides:

* [Generating Reference Documentation for Kubernetes Components and Tools](/docs/contribute/generate-ref-docs/kubernetes-components/)
* [Generating Reference Documentation for kubectl Commands](/docs/contribute/generate-ref-docs/kubectl/)
* [Generating Reference Documentation for the Kubernetes API](/docs/contribute/generate-ref-docs/kubernetes-api/)
-->
要手動設定所需的構造倉庫，執行構建目標，以生成各個參考文件，可參考下面的指南：

* [為 Kubernetes 元件和工具生成參考文件](/zh-cn/docs/contribute/generate-ref-docs/kubernetes-components/)
* [為 kubectl 命令生成參考文件](/zh-cn/docs/contribute/generate-ref-docs/kubectl/)
* [為 Kubernetes API 生成參考文件](/zh-cn/docs/contribute/generate-ref-docs/kubernetes-api/)

