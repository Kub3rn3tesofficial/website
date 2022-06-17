---
api_metadata:
  apiVersion: "apps/v1"
  import: "k8s.io/api/apps/v1"
  kind: "ControllerRevision"
content_type: "api_reference"
description: "ControllerRevision 實現了狀態資料的不可變快照。"
title: "ControllerRevision"
weight: 7
auto_generated: false
---

<!--
api_metadata:
  apiVersion: "apps/v1"
  import: "k8s.io/api/apps/v1"
  kind: "ControllerRevision"
content_type: "api_reference"
description: "ControllerRevision implements an immutable snapshot of state data."
title: "ControllerRevision"
weight: 7
auto_generated: true
-->


`apiVersion: apps/v1`

`import "k8s.io/api/apps/v1"`

<!-- 
## ControllerRevision {#ControllerRevision 
-->
## ControllerRevision {#ControllerRevision}

<!--
ControllerRevision implements an immutable snapshot of state data.
Clients are responsible for serializing and deserializing the objects that contain their internal state.
Once a ControllerRevision has been successfully created, it can not be updated. 
The API Server will fail validation of all requests that attempt to mutate the Data field.
ControllerRevisions may, however, be deleted. Note that, due to its use by both the DaemonSet and StatefulSet controllers for update and rollback,
this object is beta. However, it may be subject to name and representation changes in future releases, and clients should not depend on its stability.
It is primarily for internal use by controllers.

<hr>
-->
ControllerRevision 實現了狀態資料的不可變快照。
客戶端負責序列化和反序列化物件，包含物件內部狀態。
成功建立 ControllerRevision 後，將無法對其進行更新。
API 伺服器將無法成功驗證所有嘗試改變 data 欄位的請求。
但是，可以刪除 ControllerRevisions。
請注意，由於 DaemonSet 和 StatefulSet 控制器都使用它來進行更新和回滾，所以這個物件是 beta 版。
但是，它可能會在未來版本中更改名稱和表示形式，客戶不應依賴其穩定性。
它主要供控制器內部使用。

<hr>

<!--
- **apiVersion**: apps/v1
-->
- **apiVersion**: apps/v1

<!--
- **kind**: ControllerRevision
-->
- **kind**: ControllerRevision

<!--
- **metadata** (<a href="{{< ref "../common-definitions/object-meta#ObjectMeta" >}}">ObjectMeta</a>)

  Standard object's metadata. More info: https://git.k8s.io/community/contributors/devel/sig-architecture/api-conventions.md#metadata
-->
- **metadata** (<a href="{{< ref "../common-definitions/object-meta#ObjectMeta" >}}">ObjectMeta</a>)

  標準的物件元資料。
  更多資訊：https://git.k8s.io/community/contributors/devel/sig-architecture/api-conventions.md#metadata

<!--
- **revision** (int64), required

  Revision indicates the revision of the state represented by Data.
-->
- **revision** (int64)，必需

  revision 表示 data 表示的狀態的修訂。

<!--
- **data** (RawExtension)

  Data is the serialized representation of the state.
-->
- **data** (RawExtension)

  data 是狀態的序列化表示。
  
  <!--
  <a name="RawExtension"></a>
    *RawExtension is used to hold extensions in external versions.
  -->
  <a name="RawExtension"></a>
  *RawExtension 用於以外部版本來儲存擴充套件資料。
  
  <!--
  To use this, make a field which has RawExtension as its type in your external, versioned struct, and Object in your internal struct. You also need to register your various plugin types.
  -->
  要使用它，請生成一個欄位，在外部、版本化結構中以 RawExtension 作為其型別，在內部結構中以 Object 作為其型別。
  
  <!--
  // Internal package: type MyAPIObject struct {
    	runtime.TypeMeta `json:",inline"`
    	MyPlugin runtime.Object `json:"myPlugin"`
    } type PluginA struct {
    	AOption string `json:"aOption"`
    }
  -->
  // 內部包：  
  type MyAPIObject struct {  
  	runtime.TypeMeta `json:",inline"`   
  	MyPlugin runtime.Object `json:"myPlugin"`  
  }       
  type PluginA struct {  
  	AOption string `json:"aOption"`  
  }
  
  <!--
  // External package: type MyAPIObject struct {
    	runtime.TypeMeta `json:",inline"`
    	MyPlugin runtime.RawExtension `json:"myPlugin"`
    } type PluginA struct {
    	AOption string `json:"aOption"`
    }
  -->
  // 外部包：   
  type MyAPIObject struct {  
  	runtime.TypeMeta `json:",inline"`  
  	MyPlugin runtime.RawExtension `json:"myPlugin"`    
  }   
  type PluginA struct {  
  	AOption string `json:"aOption"`  
  }
  
  <!--
  // On the wire, the JSON will look something like this: {
    	"kind":"MyAPIObject",
    	"apiVersion":"v1",
    	"myPlugin": {
    		"kind":"PluginA",
    		"aOption":"foo",
    	},
    }
  -->
  // 在網路上，JSON 看起來像這樣：   
  {  
  	"kind":"MyAPIObject",  
  	"apiVersion":"v1",  
  	"myPlugin": {  
  		"kind":"PluginA",  
  		"aOption":"foo",  
  	},  
  }
  
  <!--
  So what happens? Decode first uses json or yaml to unmarshal the serialized data into your external MyAPIObject. That causes the raw JSON to be stored, but not unpacked. The next step is to copy (using pkg/conversion) into the internal struct. The runtime package's DefaultScheme has conversion functions installed which will unpack the JSON stored in RawExtension, turning it into the correct object type, and storing it in the Object. (TODO: In the case where the object is of an unknown type, a runtime.Unknown object will be created and stored.)*
  -->
  那麼會發生什麼？
  解碼首先使用 json 或 yaml 將序列化資料解組到你的外部 MyAPIObject 中。
  這會導致原始 JSON 被儲存下來，但不會被解包。
  下一步是複製（使用 pkg/conversion）到內部結構中。
  runtime 包的 DefaultScheme 安裝了轉換函式，它將解析儲存在 RawExtension 中的 JSON，
  將其轉換為正確的物件型別，並將其儲存在 Object 中。
  （TODO：如果物件是未知型別，將建立並存儲一個 `runtime.Unknown`物件。）*

<!--
## ControllerRevisionList {#ControllerRevisionList}

ControllerRevisionList is a resource containing a list of ControllerRevision objects.

<hr>
-->
## ControllerRevisionList {#ControllerRevisionList}

ControllerRevisionList 是一個包含 ControllerRevision 物件列表的資源。

<hr>

<!--
- **apiVersion**: apps/v1
-->
- **apiVersion**: apps/v1

<!--
- **kind**: ControllerRevisionList
-->
- **kind**: ControllerRevisionList

<!--
- **metadata** (<a href="{{< ref "../common-definitions/list-meta#ListMeta" >}}">ListMeta</a>)

  More info: https://git.k8s.io/community/contributors/devel/sig-architecture/api-conventions.md#metadata
-->
- **metadata** (<a href="{{< ref "../common-definitions/list-meta#ListMeta" >}}">ListMeta</a>)

  更多資訊：https://git.k8s.io/community/contributors/devel/sig-architecture/api-conventions.md#metadata

<!--
- **items** ([]<a href="{{< ref "../workload-resources/controller-revision-v1#ControllerRevision" >}}">ControllerRevision</a>), required

  Items is the list of ControllerRevisions
-->
- **items** ([]<a href="{{< ref "../workload-resources/controller-revision-v1#ControllerRevision" >}}">ControllerRevision</a>)，必需

  items 是 ControllerRevisions 的列表

<!--
## Operations {#Operations}

<hr>
-->
## 操作 {#Operations}

<hr>

<!--
### `get` read the specified ControllerRevision
-->
### `get` 讀取特定的 ControllerRevision

<!--
#### HTTP Request

GET /apis/apps/v1/namespaces/{namespace}/controllerrevisions/{name}
-->
#### HTTP 請求

GET /apis/apps/v1/namespaces/{namespace}/controllerrevisions/{name}

<!--
#### Parameters
-->
#### 引數

<!--
- **name** (*in path*): string, required

  name of the ControllerRevision
-->
- **name** （**路徑引數**）：string，必需

  ControllerRevision 的名稱

<!--
- **namespace** (*in path*): string, required

  <a href="{{< ref "../common-parameters/common-parameters#namespace" >}}">namespace</a>
-->
- **namespace** （**路徑引數**）：string，必需

  <a href="{{< ref "../common-parameters/common-parameters#namespace" >}}">namespace</a>

<!--
- **pretty** (*in query*): string

  <a href="{{< ref "../common-parameters/common-parameters#pretty" >}}">pretty</a>
-->
- **pretty** （**查詢引數**）：string

  <a href="{{< ref "../common-parameters/common-parameters#pretty" >}}">pretty</a>

<!--
#### Response


200 (<a href="{{< ref "../workload-resources/controller-revision-v1#ControllerRevision" >}}">ControllerRevision</a>): OK

401: Unauthorized
-->
#### 響應


200 (<a href="{{< ref "../workload-resources/controller-revision-v1#ControllerRevision" >}}">ControllerRevision</a>): OK

401: Unauthorized

<!--
### `list` list or watch objects of kind ControllerRevision
-->
### `list` 列出或監視 ControllerRevision 類別的物件

<!--
#### HTTP Request

GET /apis/apps/v1/namespaces/{namespace}/controllerrevisions
-->
#### HTTP 請求

GET /apis/apps/v1/namespaces/{namespace}/controllerrevisions

<!--
#### Parameters
-->
#### 引數

<!--
- **namespace** (*in path*): string, required

  <a href="{{< ref "../common-parameters/common-parameters#namespace" >}}">namespace</a>
-->
- **namespace** （**路徑引數**）：string，必需

  <a href="{{< ref "../common-parameters/common-parameters#namespace" >}}">namespace</a>

<!--
- **allowWatchBookmarks** (*in query*): boolean

  <a href="{{< ref "../common-parameters/common-parameters#allowWatchBookmarks" >}}">allowWatchBookmarks</a>
-->
- **allowWatchBookmarks** （**查詢引數**）： boolean

  <a href="{{< ref "../common-parameters/common-parameters#allowWatchBookmarks" >}}">allowWatchBookmarks</a>

<!--
- **continue** (*in query*): string

  <a href="{{< ref "../common-parameters/common-parameters#continue" >}}">continue</a>
-->
- **continue** （**查詢引數**）：string

  <a href="{{< ref "../common-parameters/common-parameters#continue" >}}">continue</a>

<!--
- **fieldSelector** (*in query*): string

  <a href="{{< ref "../common-parameters/common-parameters#fieldSelector" >}}">fieldSelector</a>
-->
- **fieldSelector** （**查詢引數**）：string

  <a href="{{< ref "../common-parameters/common-parameters#fieldSelector" >}}">fieldSelector</a>

<!--
- **labelSelector** (*in query*): string

  <a href="{{< ref "../common-parameters/common-parameters#labelSelector" >}}">labelSelector</a>
-->
- **labelSelector** （**查詢引數**）：string

  <a href="{{< ref "../common-parameters/common-parameters#labelSelector" >}}">labelSelector</a>

<!--
- **limit** (*in query*): integer

  <a href="{{< ref "../common-parameters/common-parameters#limit" >}}">limit</a>
-->
- **limit** (**查詢引數**)): integer

  <a href="{{< ref "../common-parameters/common-parameters#limit" >}}">limit</a>

<!--
- **pretty** (*in query*): string

  <a href="{{< ref "../common-parameters/common-parameters#pretty" >}}">pretty</a>
-->
- **pretty** （**查詢引數**）：string

  <a href="{{< ref "../common-parameters/common-parameters#pretty" >}}">pretty</a>

<!--
- **resourceVersion** (*in query*): string

  <a href="{{< ref "../common-parameters/common-parameters#resourceVersion" >}}">resourceVersion</a>
-->
- **resourceVersion** （**查詢引數**）：string

  <a href="{{< ref "../common-parameters/common-parameters#resourceVersion" >}}">resourceVersion</a>

<!--
- **resourceVersionMatch** (*in query*): string

  <a href="{{< ref "../common-parameters/common-parameters#resourceVersionMatch" >}}">resourceVersionMatch</a>
-->
- **resourceVersionMatch** （**查詢引數**）：string

  <a href="{{< ref "../common-parameters/common-parameters#resourceVersionMatch" >}}">resourceVersionMatch</a>

<!--
- **timeoutSeconds** (*in query*): integer

  <a href="{{< ref "../common-parameters/common-parameters#timeoutSeconds" >}}">timeoutSeconds</a>
-->
- **timeoutSeconds** （**查詢引數**）： integer

  <a href="{{< ref "../common-parameters/common-parameters#timeoutSeconds" >}}">timeoutSeconds</a>

<!--
- **watch** (*in query*): boolean

  <a href="{{< ref "../common-parameters/common-parameters#watch" >}}">watch</a>
-->
- **watch** （**查詢引數**）： boolean

  <a href="{{< ref "../common-parameters/common-parameters#watch" >}}">watch</a>

<!--
#### Response

200 (<a href="{{< ref "../workload-resources/controller-revision-v1#ControllerRevisionList" >}}">ControllerRevisionList</a>): OK

401: Unauthorized
-->
#### 響應

200 (<a href="{{< ref "../workload-resources/controller-revision-v1#ControllerRevisionList" >}}">ControllerRevisionList</a>): OK

401: Unauthorized

<!--
### `list` list or watch objects of kind ControllerRevision
-->
### `list` 列出或監視 ControllerRevision 類別的物件

<!--
#### HTTP Request

GET /apis/apps/v1/controllerrevisions
-->
#### HTTP 請求

GET /apis/apps/v1/controllerrevisions

<!--
#### Parameters
-->
#### 引數

<!--
- **allowWatchBookmarks** (*in query*): boolean

  <a href="{{< ref "../common-parameters/common-parameters#allowWatchBookmarks" >}}">allowWatchBookmarks</a>
-->
- **allowWatchBookmarks** （**查詢引數**）： boolean

  <a href="{{< ref "../common-parameters/common-parameters#allowWatchBookmarks" >}}">allowWatchBookmarks</a>

<!--
- **continue** (*in query*): string

  <a href="{{< ref "../common-parameters/common-parameters#continue" >}}">continue</a>
-->
- **continue** （**查詢引數**）：string

  <a href="{{< ref "../common-parameters/common-parameters#continue" >}}">continue</a>

<!--
- **fieldSelector** (*in query*): string

  <a href="{{< ref "../common-parameters/common-parameters#fieldSelector" >}}">fieldSelector</a>
-->
- **fieldSelector** （**查詢引數**）：string

  <a href="{{< ref "../common-parameters/common-parameters#fieldSelector" >}}">fieldSelector</a>

<!--
- **labelSelector** (*in query*): string

  <a href="{{< ref "../common-parameters/common-parameters#labelSelector" >}}">labelSelector</a>
-->
- **labelSelector** （**查詢引數**）：string

  <a href="{{< ref "../common-parameters/common-parameters#labelSelector" >}}">labelSelector</a>

<!--
- **limit** (*in query*): integer

  <a href="{{< ref "../common-parameters/common-parameters#limit" >}}">limit</a>
-->
- **limit** （**查詢引數**）： integer

  <a href="{{< ref "../common-parameters/common-parameters#limit" >}}">limit</a>

<!--
- **pretty** (*in query*): string

  <a href="{{< ref "../common-parameters/common-parameters#pretty" >}}">pretty</a>
-->
- **pretty** （**查詢引數**）：string

  <a href="{{< ref "../common-parameters/common-parameters#pretty" >}}">pretty</a>

<!--
- **resourceVersion** (*in query*): string

  <a href="{{< ref "../common-parameters/common-parameters#resourceVersion" >}}">resourceVersion</a>
-->
- **resourceVersion** （**查詢引數**）：string

  <a href="{{< ref "../common-parameters/common-parameters#resourceVersion" >}}">resourceVersion</a>

<!--
- **resourceVersionMatch** (*in query*): string

  <a href="{{< ref "../common-parameters/common-parameters#resourceVersionMatch" >}}">resourceVersionMatch</a>
-->
- **resourceVersionMatch** （**查詢引數**）：string

  <a href="{{< ref "../common-parameters/common-parameters#resourceVersionMatch" >}}">resourceVersionMatch</a>

<!--
- **timeoutSeconds** (*in query*): integer

  <a href="{{< ref "../common-parameters/common-parameters#timeoutSeconds" >}}">timeoutSeconds</a>
-->
- **timeoutSeconds** （**查詢引數**）： integer

  <a href="{{< ref "../common-parameters/common-parameters#timeoutSeconds" >}}">timeoutSeconds</a>

<!--
- **watch** (*in query*): boolean

  <a href="{{< ref "../common-parameters/common-parameters#watch" >}}">watch</a>
-->
- **watch** （**查詢引數**）： boolean

  <a href="{{< ref "../common-parameters/common-parameters#watch" >}}">watch</a>

<!--
#### Response


200 (<a href="{{< ref "../workload-resources/controller-revision-v1#ControllerRevisionList" >}}">ControllerRevisionList</a>): OK

401: Unauthorized
-->
#### 響應

200 (<a href="{{< ref "../workload-resources/controller-revision-v1#ControllerRevisionList" >}}">ControllerRevisionList</a>): OK

401: Unauthorized

<!--
### `create` create a ControllerRevision
-->
### `create` 建立一個 ControllerRevision

<!--
#### HTTP Request

POST /apis/apps/v1/namespaces/{namespace}/controllerrevisions
-->
#### HTTP 請求

POST /apis/apps/v1/namespaces/{namespace}/controllerrevisions

<!--
#### Parameters
-->
#### 引數

<!--
- **namespace** (*in path*): string, required

  <a href="{{< ref "../common-parameters/common-parameters#namespace" >}}">namespace</a>
-->
- **namespace** （**路徑引數**）：string，必需

  <a href="{{< ref "../common-parameters/common-parameters#namespace" >}}">namespace</a>

<!--
- **body**: <a href="{{< ref "../workload-resources/controller-revision-v1#ControllerRevision" >}}">ControllerRevision</a>, required
-->
- **body**: <a href="{{< ref "../workload-resources/controller-revision-v1#ControllerRevision" >}}">ControllerRevision</a>，必需

<!--
- **dryRun** (*in query*): string

  <a href="{{< ref "../common-parameters/common-parameters#dryRun" >}}">dryRun</a>
-->  
- **dryRun** （**查詢引數**）：string

  <a href="{{< ref "../common-parameters/common-parameters#dryRun" >}}">dryRun</a>

<!--
- **fieldManager** (*in query*): string

  <a href="{{< ref "../common-parameters/common-parameters#fieldManager" >}}">fieldManager</a>
-->
- **fieldManager** （**查詢引數**）：string

  <a href="{{< ref "../common-parameters/common-parameters#fieldManager" >}}">fieldManager</a>

<!--
- **fieldValidation** (*in query*): string

  <a href="{{< ref "../common-parameters/common-parameters#fieldValidation" >}}">fieldValidation</a>
-->
- **fieldValidation** （**查詢引數**）：string

  <a href="{{< ref "../common-parameters/common-parameters#fieldValidation" >}}">fieldValidation</a>

<!--
- **pretty** (*in query*): string

  <a href="{{< ref "../common-parameters/common-parameters#pretty" >}}">pretty</a>
-->
- **pretty** （**查詢引數**）：string

  <a href="{{< ref "../common-parameters/common-parameters#pretty" >}}">pretty</a>

<!--
#### Response

200 (<a href="{{< ref "../workload-resources/controller-revision-v1#ControllerRevision" >}}">ControllerRevision</a>): OK

201 (<a href="{{< ref "../workload-resources/controller-revision-v1#ControllerRevision" >}}">ControllerRevision</a>): Created

202 (<a href="{{< ref "../workload-resources/controller-revision-v1#ControllerRevision" >}}">ControllerRevision</a>): Accepted

401: Unauthorized
-->
#### 響應

200 (<a href="{{< ref "../workload-resources/controller-revision-v1#ControllerRevision" >}}">ControllerRevision</a>): OK

201 (<a href="{{< ref "../workload-resources/controller-revision-v1#ControllerRevision" >}}">ControllerRevision</a>): Created

202 (<a href="{{< ref "../workload-resources/controller-revision-v1#ControllerRevision" >}}">ControllerRevision</a>): Accepted

401: Unauthorized

<!--
### `update` replace the specified ControllerRevision
-->
### `update` 替換特定的 ControllerRevision

<!--
#### HTTP Request

PUT /apis/apps/v1/namespaces/{namespace}/controllerrevisions/{name}
-->
#### HTTP 引數

PUT /apis/apps/v1/namespaces/{namespace}/controllerrevisions/{name}

<!--
#### Parameters
-->
#### 引數

<!--
- **name** (*in path*): string, required

  name of the ControllerRevision
-->
- **name** （**路徑引數**）：string，必需

  ControllerRevision 的名稱

<!--
- **namespace** (*in path*): string, required

  <a href="{{< ref "../common-parameters/common-parameters#namespace" >}}">namespace</a>
-->
- **namespace** （**路徑引數**）：string，必需

  <a href="{{< ref "../common-parameters/common-parameters#namespace" >}}">namespace</a>

<!--
- **body**: <a href="{{< ref "../workload-resources/controller-revision-v1#ControllerRevision" >}}">ControllerRevision</a>, required
-->
- **body**: <a href="{{< ref "../workload-resources/controller-revision-v1#ControllerRevision" >}}">ControllerRevision</a>，必需

<!--
- **dryRun** (*in query*): string

  <a href="{{< ref "../common-parameters/common-parameters#dryRun" >}}">dryRun</a>
-->
- **dryRun** （**查詢引數**）：string

  <a href="{{< ref "../common-parameters/common-parameters#dryRun" >}}">dryRun</a>

<!--
- **fieldManager** (*in query*): string

  <a href="{{< ref "../common-parameters/common-parameters#fieldManager" >}}">fieldManager</a>
-->
- **fieldManager** （**查詢引數**）：string

  <a href="{{< ref "../common-parameters/common-parameters#fieldManager" >}}">fieldManager</a>

<!--
- **fieldValidation** (*in query*): string

  <a href="{{< ref "../common-parameters/common-parameters#fieldValidation" >}}">fieldValidation</a>
-->
- **fieldValidation** （**查詢引數**）：string

  <a href="{{< ref "../common-parameters/common-parameters#fieldValidation" >}}">fieldValidation</a>

<!--
- **pretty** (*in query*): string

  <a href="{{< ref "../common-parameters/common-parameters#pretty" >}}">pretty</a>
-->
- **pretty** （**查詢引數**）：string

  <a href="{{< ref "../common-parameters/common-parameters#pretty" >}}">pretty</a>


<!--
#### Response

200 (<a href="{{< ref "../workload-resources/controller-revision-v1#ControllerRevision" >}}">ControllerRevision</a>): OK

201 (<a href="{{< ref "../workload-resources/controller-revision-v1#ControllerRevision" >}}">ControllerRevision</a>): Created

401: Unauthorized
-->
#### 響應

200 (<a href="{{< ref "../workload-resources/controller-revision-v1#ControllerRevision" >}}">ControllerRevision</a>): OK

201 (<a href="{{< ref "../workload-resources/controller-revision-v1#ControllerRevision" >}}">ControllerRevision</a>): Created

401: Unauthorized

<!--
### `patch` partially update the specified ControllerRevision
-->
### `patch` 部分更新特定的 ControllerRevision

<!--
#### HTTP Request

PATCH /apis/apps/v1/namespaces/{namespace}/controllerrevisions/{name}
-->
#### HTTP 請求

PATCH /apis/apps/v1/namespaces/{namespace}/controllerrevisions/{name}

<!--
#### Parameters
-->
#### 引數

<!--
- **name** (*in path*): string, required

  name of the ControllerRevision
-->
- **name** （**路徑引數**）：string，必需

  ControllerRevision 的名稱

<!--
- **namespace** (*in path*): string, required

  <a href="{{< ref "../common-parameters/common-parameters#namespace" >}}">namespace</a>
-->
- **namespace** （**路徑引數**）：string，必需

  <a href="{{< ref "../common-parameters/common-parameters#namespace" >}}">namespace</a>

<!--
- **body**: <a href="{{< ref "../common-definitions/patch#Patch" >}}">Patch</a>, required
-->
- **body**: <a href="{{< ref "../common-definitions/patch#Patch" >}}">Patch</a>，必需

<!--
- **dryRun** (*in query*): string

  <a href="{{< ref "../common-parameters/common-parameters#dryRun" >}}">dryRun</a>
-->
- **dryRun** （**查詢引數**）：string

  <a href="{{< ref "../common-parameters/common-parameters#dryRun" >}}">dryRun</a>

<!--
- **fieldManager** (*in query*): string

  <a href="{{< ref "../common-parameters/common-parameters#fieldManager" >}}">fieldManager</a>
-->
- **fieldManager** （**查詢引數**）：string

  <a href="{{< ref "../common-parameters/common-parameters#fieldManager" >}}">fieldManager</a>

<!--
- **fieldValidation** (*in query*): string

  <a href="{{< ref "../common-parameters/common-parameters#fieldValidation" >}}">fieldValidation</a>
-->
- **fieldValidation** （**查詢引數**）：string

  <a href="{{< ref "../common-parameters/common-parameters#fieldValidation" >}}">fieldValidation</a>

<!--
- **force** (*in query*): boolean

  <a href="{{< ref "../common-parameters/common-parameters#force" >}}">force</a>
-->
- **force** （**查詢引數**）： boolean

  <a href="{{< ref "../common-parameters/common-parameters#force" >}}">force</a>

<!--
- **pretty** (*in query*): string

  <a href="{{< ref "../common-parameters/common-parameters#pretty" >}}">pretty</a>
-->
- **pretty** （**查詢引數**）：string

  <a href="{{< ref "../common-parameters/common-parameters#pretty" >}}">pretty</a>


<!--
#### Response

200 (<a href="{{< ref "../workload-resources/controller-revision-v1#ControllerRevision" >}}">ControllerRevision</a>): OK

201 (<a href="{{< ref "../workload-resources/controller-revision-v1#ControllerRevision" >}}">ControllerRevision</a>): Created

401: Unauthorized
-->
#### 響應

200 (<a href="{{< ref "../workload-resources/controller-revision-v1#ControllerRevision" >}}">ControllerRevision</a>): OK

201 (<a href="{{< ref "../workload-resources/controller-revision-v1#ControllerRevision" >}}">ControllerRevision</a>): Created

401: Unauthorized

<!--
### `delete` delete a ControllerRevision
-->
### `delete` 刪除一個 ControllerRevision

<!--
#### HTTP Request

DELETE /apis/apps/v1/namespaces/{namespace}/controllerrevisions/{name}
-->
#### HTTP 請求

DELETE /apis/apps/v1/namespaces/{namespace}/controllerrevisions/{name}

<!--
#### Parameters
-->
#### 引數

<!--
- **name** (*in path*): string, required

  name of the ControllerRevision
-->
- **name** （**路徑引數**）：string，必需

  ControllerRevision 的名稱

<!--
- **namespace** (*in path*): string, required

  <a href="{{< ref "../common-parameters/common-parameters#namespace" >}}">namespace</a>
-->
- **namespace** （**路徑引數**）：string，必需

  <a href="{{< ref "../common-parameters/common-parameters#namespace" >}}">namespace</a>

<!--
- **body**: <a href="{{< ref "../common-definitions/delete-options#DeleteOptions" >}}">DeleteOptions</a>
-->
- **body**: <a href="{{< ref "../common-definitions/delete-options#DeleteOptions" >}}">DeleteOptions</a>

<!--
- **dryRun** (*in query*): string

  <a href="{{< ref "../common-parameters/common-parameters#dryRun" >}}">dryRun</a>
-->  
- **dryRun** （**查詢引數**）：string

  <a href="{{< ref "../common-parameters/common-parameters#dryRun" >}}">dryRun</a>

<!--
- **gracePeriodSeconds** (*in query*): integer

  <a href="{{< ref "../common-parameters/common-parameters#gracePeriodSeconds" >}}">gracePeriodSeconds</a>
-->
- **gracePeriodSeconds** （**查詢引數**）： integer

  <a href="{{< ref "../common-parameters/common-parameters#gracePeriodSeconds" >}}">gracePeriodSeconds</a>

<!--
- **pretty** (*in query*): string

  <a href="{{< ref "../common-parameters/common-parameters#pretty" >}}">pretty</a>
-->
- **pretty** （**查詢引數**）：string

  <a href="{{< ref "../common-parameters/common-parameters#pretty" >}}">pretty</a>

<!--
- **propagationPolicy** (*in query*): string

  <a href="{{< ref "../common-parameters/common-parameters#propagationPolicy" >}}">propagationPolicy</a>
-->
- **propagationPolicy** （**查詢引數**）：string

  <a href="{{< ref "../common-parameters/common-parameters#propagationPolicy" >}}">propagationPolicy</a>


<!--
#### Response

200 (<a href="{{< ref "../common-definitions/status#Status" >}}">Status</a>): OK

202 (<a href="{{< ref "../common-definitions/status#Status" >}}">Status</a>): Accepted

401: Unauthorized
-->
#### 響應

200 (<a href="{{< ref "../common-definitions/status#Status" >}}">Status</a>): OK

202 (<a href="{{< ref "../common-definitions/status#Status" >}}">Status</a>): Accepted

401: Unauthorized

<!--
### `deletecollection` delete collection of ControllerRevision
-->
### `deletecollection` 刪除 ControllerRevision 集合

<!--
#### HTTP Request

DELETE /apis/apps/v1/namespaces/{namespace}/controllerrevisions
-->
#### HTTP 請求

DELETE /apis/apps/v1/namespaces/{namespace}/controllerrevisions

<!--
#### Parameters
-->
#### 引數

<!--
- **namespace** (*in path*): string, required

  <a href="{{< ref "../common-parameters/common-parameters#namespace" >}}">namespace</a>
-->
- **namespace** （**路徑引數**）：string，必需

  <a href="{{< ref "../common-parameters/common-parameters#namespace" >}}">namespace</a>

<!--
- **body**: <a href="{{< ref "../common-definitions/delete-options#DeleteOptions" >}}">DeleteOptions</a>
-->
- **body**: <a href="{{< ref "../common-definitions/delete-options#DeleteOptions" >}}">DeleteOptions</a>

<!--
- **continue** (*in query*): string

  <a href="{{< ref "../common-parameters/common-parameters#continue" >}}">continue</a>
-->  
- **continue** （**查詢引數**）：string

  <a href="{{< ref "../common-parameters/common-parameters#continue" >}}">continue</a>

<!--
- **dryRun** (*in query*): string

  <a href="{{< ref "../common-parameters/common-parameters#dryRun" >}}">dryRun</a>
-->
- **dryRun** （**查詢引數**）：string

  <a href="{{< ref "../common-parameters/common-parameters#dryRun" >}}">dryRun</a>

<!--
- **fieldSelector** (*in query*): string

  <a href="{{< ref "../common-parameters/common-parameters#fieldSelector" >}}">fieldSelector</a>
-->
- **fieldSelector** （**查詢引數**）：string

  <a href="{{< ref "../common-parameters/common-parameters#fieldSelector" >}}">fieldSelector</a>

<!--
- **gracePeriodSeconds** (*in query*): integer

  <a href="{{< ref "../common-parameters/common-parameters#gracePeriodSeconds" >}}">gracePeriodSeconds</a>
-->
- **gracePeriodSeconds** （**查詢引數**）： integer

  <a href="{{< ref "../common-parameters/common-parameters#gracePeriodSeconds" >}}">gracePeriodSeconds</a>

<!--
- **labelSelector** (*in query*): string

  <a href="{{< ref "../common-parameters/common-parameters#labelSelector" >}}">labelSelector</a>
-->
- **labelSelector** （**查詢引數**）：string

  <a href="{{< ref "../common-parameters/common-parameters#labelSelector" >}}">labelSelector</a>

<!--
- **limit** (*in query*): integer

  <a href="{{< ref "../common-parameters/common-parameters#limit" >}}">limit</a>
-->
- **limit** （**查詢引數**）： integer

  <a href="{{< ref "../common-parameters/common-parameters#limit" >}}">limit</a>

<!--
- **pretty** (*in query*): string

  <a href="{{< ref "../common-parameters/common-parameters#pretty" >}}">pretty</a>
-->
- **pretty** （**查詢引數**）：string

  <a href="{{< ref "../common-parameters/common-parameters#pretty" >}}">pretty</a>

<!--
- **propagationPolicy** (*in query*): string

  <a href="{{< ref "../common-parameters/common-parameters#propagationPolicy" >}}">propagationPolicy</a>
-->
- **propagationPolicy** （**查詢引數**）：string

  <a href="{{< ref "../common-parameters/common-parameters#propagationPolicy" >}}">propagationPolicy</a>

<!--
- **resourceVersion** (*in query*): string

  <a href="{{< ref "../common-parameters/common-parameters#resourceVersion" >}}">resourceVersion</a>
-->
- **resourceVersion** （**查詢引數**）：string

  <a href="{{< ref "../common-parameters/common-parameters#resourceVersion" >}}">resourceVersion</a>

<!--
- **resourceVersionMatch** (*in query*): string

  <a href="{{< ref "../common-parameters/common-parameters#resourceVersionMatch" >}}">resourceVersionMatch</a>
-->
- **resourceVersionMatch** （**查詢引數**）：string

  <a href="{{< ref "../common-parameters/common-parameters#resourceVersionMatch" >}}">resourceVersionMatch</a>

<!--
- **timeoutSeconds** (*in query*): integer

  <a href="{{< ref "../common-parameters/common-parameters#timeoutSeconds" >}}">timeoutSeconds</a>
-->
- **timeoutSeconds** （**查詢引數**）： integer

  <a href="{{< ref "../common-parameters/common-parameters#timeoutSeconds" >}}">timeoutSeconds</a>


<!--
#### Response

200 (<a href="{{< ref "../common-definitions/status#Status" >}}">Status</a>): OK

401: Unauthorized
-->
#### 響應

200 (<a href="{{< ref "../common-definitions/status#Status" >}}">Status</a>): OK

401: Unauthorized

