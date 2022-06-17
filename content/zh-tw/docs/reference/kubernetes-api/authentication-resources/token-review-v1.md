---
api_metadata:
  apiVersion: "authentication.k8s.io/v1"
  import: "k8s.io/api/authentication/v1"
  kind: "TokenReview"
content_type: "api_reference"
description: "TokenReview 嘗試透過驗證令牌來確認已知使用者。"
title: "TokenReview"
weight: 3
auto_generated: true
---

<!--
api_metadata:
  apiVersion: "authentication.k8s.io/v1"
  import: "k8s.io/api/authentication/v1"
  kind: "TokenReview"
content_type: "api_reference"
description: "TokenReview attempts to authenticate a token to a known user."
title: "TokenReview"
weight: 3
auto_generated: true
-->

`apiVersion: authentication.k8s.io/v1`

`import "k8s.io/api/authentication/v1"`

<!--
## TokenReview {#TokenReview}
TokenReview attempts to authenticate a token to a known user. Note: TokenReview requests may be cached by the webhook token authenticator plugin in the kube-apiserver.
-->
## TokenReview {#TokenReview}

TokenReview 嘗試透過驗證令牌來確認已知使用者。
注意：TokenReview 請求可能會被 kube-apiserver 中的 webhook 令牌驗證器外掛快取。

<hr>

- **apiVersion**: authentication.k8s.io/v1


- **kind**: TokenReview


- **metadata** (<a href="{{< ref "../common-definitions/object-meta#ObjectMeta" >}}">ObjectMeta</a>)

  <!--
  Standard object's metadata. More info: https://git.k8s.io/community/contributors/devel/sig-architecture/api-conventions.md#metadata
  -->
  標準物件的元資料，更多資訊： https://git.k8s.io/community/contributors/devel/sig-architecture/api-conventions.md#metadata

- **spec** (<a href="{{< ref "../authentication-resources/token-review-v1#TokenReviewSpec" >}}">TokenReviewSpec</a>), required

  <!--
  Spec holds information about the request being evaluated
  -->
  spec 儲存有關正在評估的請求的資訊

- **status** (<a href="{{< ref "../authentication-resources/token-review-v1#TokenReviewStatus" >}}">TokenReviewStatus</a>)

  <!--
  Status is filled in by the server and indicates whether the request can be authenticated.
  -->
  status 由伺服器填寫，指示請求是否可以透過身份驗證。


## TokenReviewSpec {#TokenReviewSpec}

<!--
TokenReviewSpec is a description of the token authentication request.
-->
TokenReviewPec 是對令牌身份驗證請求的描述。

<hr>

- **audiences** ([]string)

  <!--
  Audiences is a list of the identifiers that the resource server presented with the token identifies as. Audience-aware token authenticators will verify that the token was intended for at least one of the audiences in this list. If no audiences are provided, the audience will default to the audience of the Kubernetes apiserver.
  -->
  audiences 是帶有令牌的資源伺服器標識為受眾的識別符號列表。
  受眾感知令牌身份驗證器將驗證令牌是否適用於此列表中的至少一個受眾。
  如果未提供受眾，受眾將預設為 Kubernetes API 伺服器的受眾。

- **token** (string)

  <!--
  Token is the opaque bearer token.
  -->
  token 是不透明的持有者令牌（Bearer Token）。

## TokenReviewStatus {#TokenReviewStatus}

<!--
TokenReviewStatus is the result of the token authentication request.
-->
TokenReviewStatus 是令牌認證請求的結果。

<hr>

- **audiences** ([]string)

  <!--
  Audiences are audience identifiers chosen by the authenticator that are compatible with both the TokenReview and token. An identifier is any identifier in the intersection of the TokenReviewSpec audiences and the token's audiences. A client of the TokenReview API that sets the spec.audiences field should validate that a compatible audience identifier is returned in the status.audiences field to ensure that the TokenReview server is audience aware. If a TokenReview returns an empty status.audience field where status.authenticated is "true", the token is valid against the audience of the Kubernetes API server.
  -->
  audiences 是身份驗證者選擇的與 TokenReview 和令牌相容的受眾識別符號。識別符號是
  TokenReviewSpec 受眾和令牌受眾的交集中的任何識別符號。設定 spec.audiences
  欄位的 TokenReview API 的客戶端應驗證在 status.audiences 欄位中返回了相容的受眾識別符號，
  以確保 TokenReview 伺服器能夠識別受眾。如果 TokenReview
  返回一個空的 status.audience 欄位，其中 status.authenticated 為 “true”，
  則該令牌對 Kubernetes API 伺服器的受眾有效。

- **authenticated** (boolean)
  <!--
  Authenticated indicates that the token was associated with a known user.
  -->
  authenticated 表示令牌與已知使用者相關聯。

- **error** (string)

  <!--
  Error indicates that the token couldn't be checked
  -->
  error 表示無法檢查令牌

- **user** (UserInfo)

  <!--
  User is the UserInfo associated with the provided token.
  -->
  user 是與提供的令牌關聯的 UserInfo。

  <a name="UserInfo"></a>
  <--
  *UserInfo holds the information about the user needed to implement the user.Info interface.*
  -->
  **UserInfo 儲存實現 user.Info 介面所需的使用者資訊**

  - **user.extra** (map[string][]string)

    <!--
    Any additional information provided by the authenticator.
    -->
   驗證者提供的任何附加資訊。

  - **user.groups** ([]string)

    <!--
    The names of groups this user is a part of.
    -->
   此使用者所屬的組的名稱。

  - **user.uid** (string)

    <!--
    A unique value that identifies this user across time. If this user is deleted and another user by the same name is added, they will have different UIDs.
    -->
   跨時間標識此使用者的唯一值。如果刪除此使用者並新增另一個同名使用者，他們將擁有不同的 UID。

  - **user.username** (string)

    <!--
    The name that uniquely identifies this user among all active users.
    -->
   在所有活動使用者中唯一標識此使用者的名稱。

<!--
## Operations {#Operations}
-->
## 操作 {#Operations}

<hr>

<!--
### `create` create a TokenReview

#### HTTP Request
-->
### `create` 建立一個TokenReview

#### HTTP 請求

POST /apis/authentication.k8s.io/v1/tokenreviews

<!--
#### Parameters
- **body**: <a href="{{< ref "../authentication-resources/token-review-v1#TokenReview" >}}">TokenReview</a>, required
-->
#### 引數

- **body**: <a href="{{< ref "../authentication-resources/token-review-v1#TokenReview" >}}">TokenReview</a>, 必需

- **dryRun** (*in query*): string

  <a href="{{< ref "../common-parameters/common-parameters#dryRun" >}}">dryRun</a>

- **fieldManager** (*in query*): string

  <a href="{{< ref "../common-parameters/common-parameters#fieldManager" >}}">fieldManager</a>

- **fieldValidation** (*in query*): string

  <a href="{{< ref "../common-parameters/common-parameters#fieldValidation" >}}">fieldValidation</a>

- **pretty** (*in query*): string

  <a href="{{< ref "../common-parameters/common-parameters#pretty" >}}">pretty</a>

<!--
#### Response
-->
#### 響應

200 (<a href="{{< ref "../authentication-resources/token-review-v1#TokenReview" >}}">TokenReview</a>): OK

201 (<a href="{{< ref "../authentication-resources/token-review-v1#TokenReview" >}}">TokenReview</a>): Created

202 (<a href="{{< ref "../authentication-resources/token-review-v1#TokenReview" >}}">TokenReview</a>): Accepted

401: Unauthorized

