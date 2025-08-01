---
title: 活动
feature: REST API
description: 用于管理Marketo Engage活动的API。
exl-id: 1e69af23-2b0c-467a-897c-1dcf81343e73
source-git-commit: 981ed9b254f277d647a844803d05a1a2549cbaed
workflow-type: tm+mt
source-wordcount: '2029'
ht-degree: 0%

---

# 活动

Marketo允许与潜在客户记录相关的各种活动类型。  几乎每个更改、操作或流程步骤都根据商机的活动日志进行记录，并且可以通过API检索或在智能列表以及智能营销活动过滤器和触发器中使用。  活动始终通过leadId与潜在客户记录相关联，对应于记录的Id字段，并具有其自身的唯一ID。

潜在活动类型非常多，它们可能因订阅而异，并且每种活动类型的定义都各不相同。 虽然每个活动都有自己的唯一`id`、`leadId`和`activityDate`，但`primaryAttributeValueId`和`primaryAttributeValue`值在含义上有所不同。

Marketo还允许通过自定义活动元数据API创建自定义活动类型。 添加自定义活动通过添加自定义活动API完成。

大多数活动将在一段时间后清除。

## 描述

要检索实例的可用类型及其定义的列表，您可以使用[获取活动类型](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Activities/operation/getAllActivityTypesUsingGET)端点。

```
GET /rest/v1/activities/types.json
```

```json
  "requestId": "6e78#148ad3b76f1",
  "success": true,
  "result": [
    {
      "id": 2,
      "name": "Fill Out Form",
      "description": "User fills out and submits form on web page",
      "primaryAttribute": {
        "name": "Webform ID",
        "dataType": "integer"
      },
      "attributes": [
        {
          "name": "Client IP Address",
          "dataType": "string"
        },
        {
          "name": "Form Fields",
          "dataType": "text"
        },
        {
          "name": "Query Parameters",
          "dataType": "string"
        },
        {
          "name": "Referrer URL",
          "dataType": "string"
        },
        {
          "name": "User Agent",
          "dataType": "string"
        },
        {
          "name": "Webpage ID",
          "dataType": "integer"
        }
      ]
    }
  ]
}
```

现实世界的应对措施包括更多定义。 在此示例中，显示的类型为“填写表单”，其主要属性为“Webform ID”，该属性是指所填写表单的Marketo ID，可用于与Marketo中的特定资源相关联。 此外，还有此类型的特定活动记录及其数据类型的每个可能属性的定义。 请注意，如果字段为空，则单个活动记录中会忽略该特定属性。

## 查询

要从Marketo检索活动，请调用[获取潜在客户活动](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Activities/operation/getLeadActivitiesUsingGET)端点。 您需要首先为要开始从中检索活动的日期时间检索分页令牌。 然后在`nextPageToken`查询参数中传递分页令牌。 此外，您最多可以将`activityTypeIds`查询参数中的10个活动类型ID作为逗号分隔列表传递。

您可以选择包括listId查询参数以仅搜索特定静态列表中包含的记录，或者包括leadIds查询参数并仅搜索一组指定潜在客户中的活动。 您最多可以将30个潜在客户ID作为逗号分隔列表传递。

```
GET /rest/v1/activities.json?activityTypeIds=1&nextPageToken=WQV2VQVPPCKHC6AQYVK7JDSA3I3LCWXH3Y6IIZ7YSGQLXHCPVE5Q====
```

```json
{
  "requestId": "24fd#15188a88d7f",
  "result": [
    {
      "id": 102988,
      "marketoGUID": "102988",
      "leadId": 1,
      "activityDate": "2023-01-16T23:32:19Z",
      "activityTypeId": 1,
      "primaryAttributeValueId": 71,
      "primaryAttributeValue": "localhost/munchkintest2.html",
      "attributes": [
        {
          "name": "Client IP Address",
          "value": "10.0.19.252"
        },
        {
          "name": "Query Parameters",
          "value": ""
        },
        {
          "name": "Referrer URL",
          "value": ""
        },
        {
          "name": "User Agent",
          "value": "Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/39.0.2171.95 Safari/537.36"
        },
        {
          "name": "Webpage URL",
          "value": "/munchkintest2.html"
        }
      ]
    }
  ],
  "success": true,
  "nextPageToken": "WQV2VQVPPCKHC6AQYVK7JDSA3J62DUSJ3EXJGDPTKPEBFW3SAVUA====",
  "moreResult": false
}
```

对于第一次调用，请使用获取分页令牌API获取`nextPageToken`。 对于对此端点的后续调用，请使用响应中的`nextPageToken returned`。 此终结点始终返回`the nextPageToken`。

如果`moreResult`属性为true，则表示有更多的结果可用。 继续调用此终结点，直到`moreResult`属性返回false，这意味着没有可用的结果。 从此API返回的`nextPageToken`应始终在此调用的下一个迭代中重用。

在某些情况下，此API可能会以少于300个活动项进行响应，但也会将`moreResult`属性设置为true。  这表示可以返回的活动更多，并且可以通过在后续调用中包含返回的`nextPageToken`来查询终结点以获取更新的活动。

请注意，在每个结果数组项中，`id` integer属性将替换为`marketoGUID` string属性作为唯一标识符。 

### 数据值更改

对于数据值更改活动，提供了活动API的专用版本。 [Get Lead Changes](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Activities/operation/getLeadChangesUsingGET)终结点仅返回数据值更改记录到Lead字段的活动。 该界面与Get Lead Activities API相同，但有两个区别：

* 没有`activityTypeIds`参数，因为终结点仅返回数据值更改和新潜在客户活动。
* `fields`查询参数是必需的，您可以在其中传递以逗号分隔的字段列表，以指示您要检索哪些字段的更改。

```
GET /rest/v1/activities/leadchanges.json?nextPageToken=GIYDAOBNGEYS2MBWKQYDAORQGA5DAMBOGAYDAKZQGAYDALBQ&fields=firstName,lastName,department
```

```json
{
  "requestId": "a9ae#148add1e53d",
  "success": true,
  "nextPageToken": "GIYDAOBNGEYS2MBWKQYDAORQGA5DAMBOGAYDAKZQGAYDALBRGA3TQ===",
  "moreResult": true,
  "result": [
    {
      "id": 1078,
      "marketoGUID": "1078",
      "leadId": 775,
      "activityDate": "2014-09-17T22:31:49+0000",
      "activityTypeId": 13,
      "fields": [
        {
          "id": 48,
          "name": "firstName",
          "newValue": "FirstName_6176",
          "oldValue": "FirstName_4914"
        }
      ],
      "attributes": [
        {
          "name": "Reason",
          "value": "Web service API"
        },
        {
          "name": "Source",
          "value": "Web service API"
        },
        {
          "name": "Lead ID",
          "value": 775
        }
      ]
    }
  ]
}
```

响应中的每个活动都有一个字段数组，其中包括活动中的更改列表，该列表将指定更改字段的`id`和`name`，以及相对于更改的新值和旧值。

请注意，在每个结果数组项中，`id` integer属性将替换为`marketoGUID` string属性作为唯一标识符。

### 已删除的潜在客户

还有一个特殊的端点[获取已删除的潜在客户](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Activities/operation/getDeletedLeadsUsingGET)，用于从Marketo中检索已删除的活动。

```
GET /rest/v1/activities/deletedleads.json?nextPageToken=GIYDAOBNGEYS2MBWKQYDAORQGA5DAMBOGAYDAKZQGAYDALBQ
```

```json
{
  "requestId": "a9ae#148add1e53d",
  "success": true,
  "nextPageToken": "GIYDAOBNGEYS2MBWKQYDAORQGA5DAMBOGAYDAKZQGAYDALBRGA3TQ===",
  "moreResult": true,
  "result": [
    {
      "id": 2,
      "marketoGUID": "2",
      "leadId": 6,
      "activityDate": "2013-09-26T06:56:35+0000",
      "activityTypeId": 37,
      "primaryAttributeValueId": 6,
      "primaryAttributeValue": "Owyliphys Iledil",
      "attributes": []
    },
    {
      "id": 3,
      "marketoGUID": "3",
      "leadId": 9,
      "activityDate": "2013-12-28T00:39:45+0000",
      "activityTypeId": 37,
      "primaryAttributeValueId": 4,
      "primaryAttributeValue": "First Last",
      "attributes": []
    }
  ]
}
```

请注意，在每个结果数组项中，`id` integer属性将替换为`marketoGUID` string属性作为唯一标识符。

### 翻阅结果

默认情况下，此部分中提到的端点一次返回300个活动项目。  如果`moreResult`属性为true，则有更多结果可用。 调用终结点，直到`moreResult`属性返回false，这意味着没有其他可用结果。 从此终结点返回的`nextPageToken`应始终为此调用的下一个迭代重用。

在某些情况下，此终结点可能会以少于300个活动项进行响应，但也会将`moreResult`属性设置为true。  这表示存在可返回的其他活动，并且可以通过在后续调用中包含返回的`nextPageToken`来查询终结点以查看较新的活动。 请注意，`nextPageToken`需要在请求中进行编码。

## 自定义活动类型

自定义活动的功能与标准活动类似，不同之处在于，架构由第三方管理，而不是由Marketo管理。 自定义活动的实例通过`leadId`与潜在客户记录关联（与标准活动一样），但主属性和辅助属性都是任意定义的。 在批准自定义活动类型后，将创建相应的智能列表触发器和过滤器，以便可以根据当前或历史自定义活动数据处理潜在客户。

* 自定义活动的最大数量：10
* 每个自定义活动的最大属性数：20

检索自定义活动数据的方式与通过[获取潜在客户活动](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Activities/operation/getLeadActivitiesUsingGET) API执行标准活动相同。

## 查询类型

除了标准Get Activity Types端点之外，[Get Custom Activity Types](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Activities/operation/getCustomActivityTypeUsingGET)和[Describe Custom Activity Type](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Activities/operation/describeCustomActivityTypeUsingGET)端点还返回有关在Marketo实例中设置的活动类型的详细信息，以及有关给定类型属性的元数据。 普通的[获取活动类型](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Activities/operation/getAllActivityTypesUsingGET)仍返回有关自定义活动的元数据，但不指示给定类型是否为自定义类型。

### 获取类型

```
GET /rest/v1/activities/external/types.json
```

```json
{
  "requestId": "185d6#14b51985ff0",
  "success": true,
  "result": [
    {
      "id": 100001,
      "apiName": "attendConference",
      "name": "Attend Conference",
      "description": "Attend the conference",
      "triggerName": "Attends Conference",
      "filterName": "Attended Conference",
      "createdAt": "2016-02-03T22:36:23Z",
      "updatedAt": "2016-02-03T22:36:23Z",
      "status": "approved"
    }
  ]
}
```

### 描述类型

对于类型描述，必须将`apiName`作为路径参数传递。 默认情况下，您会获得该活动的批准版本。 您可以选择传递`draft=true`参数以检索活动的草稿版本。

```
GET /rest/v1/activities/external/type/{apiName}/describe.json
```

```json
{
  "requestId": "185d6#14b51985ff0",
  "success": true,
  "result": [
    {
      "id": 100001,
      "apiName": "attendConference",
      "name": "Attend Conference",
      "description": "Attend the conference",
      "triggerName": "Attends Conference",
      "filterName": "Attended Conference",
      "createdAt": "2016-02-03T22:36:23Z",
      "updatedAt": "2016-02-03T22:36:23Z",
      "status": "approved",
      "primaryAttribute": {
        "apiName": "conferenceName",
        "name": "Conference Name",
        "description": "Name of the conference",
        "dataType": "string"
      },
      "attributes": [
        {
          "apiName": "conferenceDate",
          "name": "Conference Date",
          "description": "Date of the conference",
          "dataType": "datetime"
        },
        {
          "apiName": "numberOfAttendees",
          "name": "Number of Attendees",
          "description": "Number of people attending conference",
          "dataType": "integer"
        }
      ]
    }
  ]
}
```

## 创建类型

每个自定义活动类型都需要显示名称、API名称、触发器名称、过滤器名称和主要属性。

要确保类型与Marketo惯例的一致性并避免冲突，在创建类型时请务必遵循以下准则：

**显示名称：**&#x200B;活动类型的显示名称应简要描述活动记录的含义，如“发送电子邮件”或“更改数据值”。 这些名称通常应采用不定形式，即“出席事件”。  显示名称接受字母数字字符、空格和下划线。 显示名称必须至少包含一个字母。

**API名称：** API名称由字母数字字符组成（最大长度为255）。 如果您是LaunchPoint合作伙伴，则应在活动类型API名称前添加一个代表性命名空间。 这是为了避免与客户配置的类型发生冲突。  使用全部小写或驼峰式大小写有助于区分其他文本字符串。

**描述：**&#x200B;对于可能具有非明显行为的活动，应包含有关该活动类型相对于潜在客户的表现的描述。

**触发器名称：**&#x200B;每个活动类型都必须有一个人工可读的唯一触发器名称。 触发器名称应采用第三人称现在时，如“Attends an Event”。 LaunchPoint合作伙伴应在活动中包括他们的公司名称，如“Attends网络研讨会 — Acme公司”。

**筛选器名称：**  每个活动类型必须具有人工可读的唯一过滤器名称。 过滤器名称应采用第三人称过去式，如“Attended an Event”。 LaunchPoint合作伙伴应在活动中包括他们的公司名称，即“已参加的网络研讨会 — Acme公司”。

**主要属性：**&#x200B;自定义活动的主要属性应该是该活动类型最重要的字段。 例如，对于“已参与的事件”活动，这将是事件的名称。 默认情况下，主要属性将作为参数包含在该活动类型的每个触发器或过滤器实例中，该值显示在人员记录的活动日志中，而无需向下钻取到活动。

创建自定义活动时，会将它创建为草稿，必须先获得批准，然后才能使用它添加该类型的活动记录。 所有更新都隐式应用于该类型的草稿版本。 要反映该类型的实时版本中的更改，必须批准该类型。 当自定义活动类型获得批准并在使用中时，不得对上述字段进行更改。

创建类型时，描述参数是可选的，而以下所有参数都是必需的： `apiName`、`name`、`triggerName`、`filterName`、`primaryAttribute`。

```
POST /rest/v1/activities/external/type.json
```

```json
{
  "apiName": "attendConference",
  "name": "Attend Conference",
  "description": "Attend the conference",
  "triggerName": "Attends Conference",
  "filterName": "Attended Conference",
  "primaryAttribute": {
    "apiName": "conferenceName",
    "name": "Conference Name",
    "description": "Name of the conference"
  }
}
```

```json
{
  "requestId": "e42b#14272d07d78",
  "success": true,
  "result": [
    {
      "apiName": "attendConference",
      "name": "Attend Conference",
      "description": "Attend the conference",
      "triggerName": "Attends Conference",
      "filterName": "Attended Conference",
      "status": "draft",
      "primaryAttribute": {
        "apiName": "conferenceName",
        "name": "Conference Name",
        "description": "Name of the conference",
        "dataType": "string"
      }
    }
  ]
}
```

## 更新类型

更新类型非常相似，不同之处在于， apiName是唯一作为path参数所需的参数。

```
POST /rest/v1/activities/external/type/{apiName}.json
```

```json
{
  "name": "Attend Conference",
  "description": "Attend the conference",
  "triggerName": "Attend Conference",
  "filterName": "Attended Conference",
  "primaryAttribute": {
    "apiName": "conferenceName",
    "name": "Conference Name",
    "description": "Name of the conference"
  }
}
```

```json
{
  "requestId": "e42b#14272d07d78",
  "success": true,
  "result": [
    {
      "apiName": "attendConference",
      "name": "Attend Conference",
      "description": "Attend the conference",
      "triggerName": "Attend Conference",
      "filterName": "Attended Conference",
      "status": "draft",
      "primaryAttribute": {
        "apiName": "conferenceName",
        "name": "Conference Name",
        "description": "Name of the conference",
        "dataType": "string"
      }
    }
  ]
}
```

## 批准类型

可以使用批准自定义活动类型、放弃自定义活动类型草稿和删除自定义活动类型来管理类型，操作方法与标准Marketo资源类似。


## 自定义活动类型属性

每个自定义活动类型可以具有0到20个次要属性。 辅助属性可以具有Marketo字段的任何有效字段类型。 它们会从父类型中单独添加、更新和删除，但在活动类型正在使用中并随后获得批准时可以编辑。 在实时类型上编辑字段时，该类型的所有活动在批准后都将具有新的辅助属性集。 更改将不会逆向应用于共享该类型的现有活动。

请务必小心移除属性，因为这会影响它们在相应过滤器中使用的可用性。

对次要属性列表所做的更新将每个属性的API名称用作主键。 属性的API名称不能更改，必须将其删除并使用所需的API名称再次添加。

属性的有效数据类型是：字符串、布尔值、整数、浮点数、链接、电子邮件、货币、日期、日期时间、电话、文本。

更改活动类型的主属性时，应首先将`isPrimary`设置为false，以降级任何现有的主属性。

### 创建属性

创建属性需要使用必需的`apiName`路径参数。 `name`和`dataType`参数也是必需的。` The description and` `isPrimary`参数是可选的。

```
POST /rest/v1/activities/external/type/{apiName}/attributes/create.json
```

```json
{
  "attributes": [
    {
      "apiName": "conferenceDate",
      "name": "Conference Date",
      "description": "Date of the conference",
      "dataType": "datetime"
    },
    {
      "apiName": "numberOfAttendees",
      "name": "Number of Attendees",
      "description": "Number of people attending conference",
      "dataType": "integer"
    }
  ]
}
```

```json
{
  "requestId": "e42b#14272d07d78",
  "success": true,
  "result": [
    {
      "id": 100001,
      "apiName": "attendConference",
      "name": "Attend Conference",
      "description": "Attend the conference",
      "triggerName": "Attend Conference",
      "filterName": "Attended Conference",
      "createdAt": "2016-02-03T22:36:23Z",
      "updatedAt": "2016-02-03T22:36:23Z",
      "status": "approved with draft",
      "primaryAttribute": {
        "apiName": "conferenceName",
        "name": "Conference Name",
        "description": "Name of the conference",
        "dataType": "string"
      },
      "attributes": [
        {
          "apiName": "conferenceDate",
          "name": "Conference Date",
          "description": "Date of the conference",
          "dataType": "datetime"
        },
        {
          "apiName": "numberOfAttendees",
          "name": "Number of Attendees",
          "description": "Number of people attending conference",
          "dataType": "integer"
        }
      ]
    }
  ]
}
```

### 更新属性

执行属性更新时，属性的`apiName`是主键。 必须存在`apiName`参数才能成功更新（即，不能使用更新更改`apiName`参数）。

```
POST /rest/v1/activities/external/type/{apiName}/attributes/update.json
```

```json
{
  "attributes": [
    {
      "apiName": "conferenceDate",
      "name": "Conference Date",
      "description": "Date of the conference",
      "dataType": "datetime"
    },
    {
      "apiName": "numberOfAttendee",
      "name": "Number of Attendee",
      "description": "Number of people attending conference",
      "dataType": "integer"
    }
  ]
}
```

```json
{
  "requestId": "e42b#14272d07d78",
  "success": true,
  "result": [
    {
      "id": 100001,
      "apiName": "attendConference",
      "name": "Attend Conference",
      "description": "Attend the conference",
      "triggerName": "Attend Conference",
      "filterName": "Attended Conference",
      "createdAt": "2016-02-03T22:36:23Z",
      "updatedAt": "2016-02-03T22:36:23Z",
      "status": "approved with draft",
      "primaryAttribute": {
        "apiName": "conferenceName",
        "name": "Conference Name",
        "description": "Name of the conference",
        "dataType": "string"
      },
      "attributes": [
        {
          "apiName": "conferenceDate",
          "name": "Conference Date",
          "description": "Date of the conference",
          "dataType": "datetime"
        },
        {
          "apiName": "numberOfAttendee",
          "name": "Number of Attendee",
          "description": "Number of people attending conference",
          "dataType": "integer"
        }
      ]
    }
  ]
}
```

### 删除属性

删除属性需要使用作为自定义活动API名称的必需`apiName`路径参数。  另外，还需要一个属性参数，它是一个属性对象的数组。  每个对象必须包含一个`apiName`参数，该参数是自定义活动类型API名称。

```
POST /rest/v1/activities/external/type/{apiName}/attributes/delete.json
```

```json
{ "attributes":[ { "apiName":"conferenceDate" }, { "apiName":"numberOfAttendees" } ] }
```

```json
{
  "requestId": "e42b#14272d07d78",
  "success": true,
  "result": [
    {
      "id": 100001,
      "apiName": "attendConference",
      "name": "Attend Conference",
      "description": "Attend the conference",
      "triggerName": "Attend Conference",
      "filterName": "Attended Conference",
      "createdAt": "2016-02-03T22:36:23Z",
      "updatedAt": "2016-02-03T22:36:23Z",
      "status": "approved with draft",
      "primaryAttribute": {
        "apiName": "conferenceName",
        "name": "Conference Name",
        "description": "Name of the conference",
        "dataType": "string"
      }
    }
  ]
}
```

## 添加自定义活动

自定义活动是与Marketo中的个人记录相关的历史活动的一次性写入记录。 这些活动具有由Marketo管理员管理或通过API集成远程管理的架构。 自定义活动通过[添加自定义活动](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Activities/operation/addCustomActivityUsingPOST)端点添加到潜在客户记录，并通过其`leadId`字段与每个潜在客户记录相关联。 自定义活动可通过潜在客户的活动日志在用户界面中查看，或通过指定自定义活动的类型ID而通过“获取潜在客户活动”端点进行检索。

自定义活动适用于记录与单个人员记录相关且不需要更新或覆盖的数据。 例如，将参加活动的人记录为“已参加活动”活动。 对于与可能更改的人员相关的记录（如学生注册），应改用自定义对象，因为如果自定义活动未更新，这些对象可能会更新。

输入成员是活动对象的数组。 一次最多可以提交300个活动记录。

`leadId`、`activityDate`、`activityTypeId`、`primaryAttributeValue`和属性成员是必需的。 属性数组必须包含非主属性。 可以使用name（字段名称）或apiName（API名称）以及与您设置的值对应的值来指定此值。

```
POST /rest/v1/activities/external.json
```

```json
{
  "input": [
    {
      "leadId": 1001,
      "activityDate": "2016-09-26T06:56:35+07:00",
      "activityTypeId": 1001,
      "primaryAttributeValue": "Game Giveaway",
      "attributes": [
        {
          "apiName": "uRL",
          "value": "http://www.nvidia.com/game-giveaway"
        }
      ]
    },
    {
      "leadId": 1200,
      "activityDate": "2016-09-26T06:56:35+07:00",
      "activityTypeId": 1001,
      "primaryAttributeValue": "Game Giveaway",
      "attributes": [
        {
          "apiName": "uRL",
          "value": "http://www.nvidia.com/game-giveaway"
        }
      ]
    },
    {
      "leadId": 3000,
      "activityDate": "2016-09-26T06:56:35+07:00",
      "activityTypeId": 1001,
      "primaryAttributeValue": "Contest Form",
      "attributes": [
        {
          "apiName": "uRL",
          "value": "http://www.nvidia.com/game-giveaway"
        }
      ]
    }
  ]
}
```

```json
{
  "requestId": "e42b#14272d07d78",
  "success": true,
  "result": [
    {
      "id": 50,
      "marketoGUID": "50",
      "status": "added"
    },
    {
      "id": 51,
      "marketoGUID": "51",
      "status": "added"
    },
    {
      "status": "skipped",
      "errors": [
        {
          "code": "1004",
          "message": "Lead not found"
        }
      ]
    }
  ]
}
```

## 超时

除非下面说明，否则活动端点的超时为30秒。

* 获取分页令牌： 300秒 
* 添加自定义活动：90秒
