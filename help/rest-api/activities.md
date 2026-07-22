---
title: 活动
feature: REST API
description: 使用Marketo Engage Activities REST API可列出活动类型、获取包含分页令牌的潜在客户活动以及处理自定义和数据值更改。
exl-id: 1e69af23-2b0c-467a-897c-1dcf81343e73
TQID: https://experienceleague.adobe.com/62keaj4uNoxIPCzr9AQzKrIsfuHBvC25knYisZRUvF4
product_v2:
  - id: b27e5950-9033-45ac-9f86-eb22e567f615
feature_v2:
  - id: c5f60233-d5ea-4453-a799-0ad258b4d399
role_v2:
  - id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
topic_v2:
  - id: a004cc84-67b9-4a33-a3a7-8ec7273ef4dc
source-git-commit: 3e6d310c5aec1a3435424fb122b71d825db5af0e
workflow-type: tm+mt
source-wordcount: 1758
ht-degree: 0%

---

# 活动

Marketo支持许多与潜在客户记录相关的活动类型。 几乎每个更改、操作或流程步骤都记录在商机的活动日志中。 您可以通过API检索这些活动，或在智能列表和智能营销活动过滤器和触发器中使用它们。

每个活动都有一个唯一的`id`，并通过`leadId`连接到潜在客户记录，这与记录的ID字段相对应。 每个活动也有一个`activityDate`。

可用的活动类型因订阅而异，每种类型都有自己的定义。 `primaryAttributeValueId`和`primaryAttributeValue`的含义取决于活动类型。

使用自定义活动元数据API创建自定义活动类型。 使用添加自定义活动API添加自定义活动记录。

大多数活动将在一段时间后清除。

## 描述

使用[获取活动类型](https://developer.adobe.com/marketo-apis/api/mapi#tag/Activities/operation/getAllActivityTypesUsingGET)端点检索实例的可用活动类型及其定义。

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

实际响应包括更多定义。 此示例显示了“填写表单”活动类型。 其主要属性“Webform ID”是指已提交表单的Marketo ID，并将活动链接到该资源。

响应还会为活动类型及其数据类型定义每个可能的属性。 如果某个字段为空，则该属性将从单个活动记录中忽略。

## 查询

使用[获取潜在客户活动](https://developer.adobe.com/marketo-apis/api/mapi#tag/Activities/operation/getLeadActivitiesUsingGET)端点检索活动。 首先，检索开始活动检索的日期时间的分页令牌。 在`nextPageToken`查询参数中传递该令牌。

在`activityTypeIds`查询参数中最多传递10个活动类型ID以逗号分隔的列表。

（可选）使用以下参数之一缩小查询：

- `listId`将结果限制为特定静态列表中的记录。
- `leadIds`将结果限制为最多30个潜在客户的活动，以逗号分隔列表形式提供。

>[!CAUTION]
>
>从2026-12-30开始，如果目标列表包含10,000个或更多潜在客户，对包含`listId`参数的`Get Lead Activities`和`Get Lead Changes`端点的调用将失败（错误代码1003）。 为避免服务中断，请确保正确限定调用的范围以避免此限制。 请参阅[迁移指南](migration.md)。

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

对于第一次调用，请使用获取分页令牌API获取`nextPageToken`。 对于每个后续调用，传递上一个响应返回的`nextPageToken`。 此终结点始终返回`nextPageToken`。

如果`moreResult`为true，则有更多结果可用。 继续使用返回的`nextPageToken`调用终结点，直到`moreResult`为false。

将`moreResult`设置为true时，API可返回的活动项少于300个。 在这种情况下，将返回的`nextPageToken`包含在另一个调用中以检索更近的活动。

在每个结果数组项中，`marketoGUID`字符串属性将`id`整数属性替换为唯一标识符。

### 数据值更改

使用[获取潜在客户更改](https://developer.adobe.com/marketo-apis/api/mapi#tag/Activities/operation/getLeadChangesUsingGET)端点检索潜在客户字段的数据值更改记录。 其界面与获取潜在客户活动API在以下两个方面有所不同：

- 终结点没有`activityTypeIds`参数，因为它只返回数据值更改和新潜在客户活动。
- 所需的`fields`查询参数接受要检索其更改的以逗号分隔的字段列表。

>[!CAUTION]
>
>从2026-12-30开始，如果目标列表包含10,000个或更多潜在客户，对包含`listId`参数的`Get Lead Activities`和`Get Lead Changes`端点的调用将失败（错误代码1003）。 为避免服务中断，请确保正确限定调用的范围以避免此限制。 请参阅[迁移指南](migration.md)。

```http
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

响应中的每个活动都有一个字段数组，其中列出了其更改。 每个更改都指定字段的`id`和`name`，以及新值和旧值。

在每个结果数组项中，`marketoGUID`字符串属性将`id`整数属性替换为唯一标识符。

### 已删除的潜在客户

使用[获取已删除的潜在客户](https://developer.adobe.com/marketo-apis/api/mapi#tag/Activities/operation/getDeletedLeadsUsingGET)端点从Marketo中检索已删除的潜在客户活动。

```http
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

在每个结果数组项中，`marketoGUID`字符串属性将`id`整数属性替换为唯一标识符。

### 翻阅结果

默认情况下，此部分中的端点一次返回300个活动项目。 如果`moreResult`为true，则有更多结果可用。 在随后的每次调用中传递返回的`nextPageToken`，直到`moreResult`为false。

将`moreResult`设置为true时，终结点可以返回少于300个活动项。 在这种情况下，将返回的`nextPageToken`包含在另一个调用中以检索更近的活动。 请求中的URL编码`nextPageToken`。

## 自定义活动类型

自定义活动与标准活动类似，但第三方管理其架构。 自定义活动记录通过`leadId`链接到潜在客户记录，并且其主要属性和次要属性是用户定义的属性。

批准自定义活动类型后，Marketo会创建相应的智能列表触发器和过滤器。 然后，您可以根据当前或历史自定义活动数据处理潜在客户。

- 最大自定义活动数：10
- 每个自定义活动的最大属性数：20

通过[获取潜在客户活动](https://developer.adobe.com/marketo-apis/api/mapi#tag/Activities/operation/getLeadActivitiesUsingGET) API检索自定义活动数据，与检索标准活动的方式相同。

## 查询类型

使用[获取自定义活动类型](https://developer.adobe.com/marketo-apis/api/mapi#tag/Activities/operation/getCustomActivityTypeUsingGET)来检索有关在Marketo实例中设置的类型的详细信息。 使用[Describe自定义活动类型](https://developer.adobe.com/marketo-apis/api/mapi#tag/Activities/operation/describeCustomActivityTypeUsingGET)检索特定类型的属性元数据。

标准[获取活动类型](https://developer.adobe.com/marketo-apis/api/mapi#tag/Activities/operation/getAllActivityTypesUsingGET)终结点也返回自定义活动元数据，但无法识别类型是否为自定义类型。

### 获取类型

```http
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

要描述类型，请将`apiName`作为路径参数传递。 默认情况下，端点会返回活动的批准版本。 要检索草稿版本，请传递可选的`draft=true`参数。

```http
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

每个自定义活动类型都需要显示名称、API名称、触发器名称、过滤器名称和主要属性。 请遵循以下准则来使类型与Marketo惯例保持一致，并避免命名冲突：

- **显示名称：**&#x200B;简要描述活动记录所代表的内容，如“发送电子邮件”或“更改数据值”。 使用无限形式，如“Attend Event”。 显示名称接受字母数字字符、空格和下划线，并且必须至少包含一个字母。

- **API名称：**&#x200B;使用最大长度为255的字母数字字符。 如果您是LaunchPoint合作伙伴，请在活动类型API名称前添加一个代表性命名空间，以避免与客户配置的类型发生冲突。 使用小写或驼峰式大小写区分API名称和其他字符串。

- **描述：**&#x200B;对于行为不明显的活动，请解释该活动类型相对于潜在客户的含义。

- **触发器名称：**&#x200B;以第三人称现时提供人类可读的唯一名称，如“出席活动”。 LaunchPoint合作伙伴应包含其公司名称，如“Attends网络研讨会 — Acme公司”。

- **筛选器名称：**&#x200B;提供一个用第三人称过去时表示的、人类可读的唯一名称，如“已参加活动”。 LaunchPoint合作伙伴应包含其公司名称，如“已参加的网络研讨会 — Acme公司”。

- **主要属性：**&#x200B;为活动类型选择最重要的字段。 对于“已参与的事件”活动，此字段是事件名称。 默认情况下，主要属性将作为活动类型的每个触发器或过滤器中的参数显示。 其值也会显示在人员的活动日志中，而无需深入查看活动。

新的自定义活动类型已创建为草稿。 在添加该类型的活动记录之前批准该类型。 更新适用于草稿版本，并且必须先获得批准，然后才会显示在实时版本中。 在自定义活动类型获得批准并正在使用后，无法更改前面的字段。

创建类型时，说明参数是可选的。 必需的参数为`apiName`、`name`、`triggerName`、`filterName`和`primaryAttribute`。

```http
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

要更新类型，请将所需的apiName作为路径参数传递。 可在请求正文中提供其他字段。

```http
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

与标准Marketo资源一样，使用“批准自定义活动类型”、“放弃自定义活动类型草稿”和“删除自定义活动类型”来管理类型。

## 自定义活动类型属性

每个自定义活动类型可以具有0到20个次要属性。 辅助属性可使用任何有效的Marketo字段类型。 从父类型中单独添加、更新和删除辅助属性。

您可以在活动类型正在使用时编辑属性，然后批准更改。 批准后创建的活动使用新的辅助属性集。 更改不会追溯应用于该类型的现有活动。

删除属性也会删除它们在相应筛选器中的可用性。

对辅助属性列表的更新将每个属性的API名称用作主键。 要更改API名称，请删除属性，然后使用所需的API名称再次添加它。

属性的有效数据类型是：字符串、布尔值、整数、浮点数、链接、电子邮件、货币、日期、日期时间、电话、文本。

在更改活动类型的主属性之前，请通过将`isPrimary`设置为false来降级现有的主属性。

### 创建属性

要创建属性，请传递所需的`apiName`路径参数。 `name`和`dataType`参数也是必需的。 说明和`isPrimary`参数是可选的。

```http
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

更新属性时，属性`apiName`是主键，并且必须已存在。 无法使用更新更改`apiName`。

```http
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

要删除属性，请为自定义活动传递所需的`apiName`路径参数。 还将所需的属性参数作为属性对象的数组传递。 每个对象都必须包含自定义活动类型的`apiName`参数。

```http
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

自定义活动是个人记录历史活动的一次性写入记录。 Marketo管理员可以在Marketo中管理其架构，或者，API集成可以远程管理该架构。

使用[添加自定义活动](https://developer.adobe.com/marketo-apis/api/mapi#tag/Activities/operation/addCustomActivityUsingPOST)端点将自定义活动添加到潜在客户记录。 `leadId`字段将每个活动与潜在客户关联。 在商机的活动日志中查看自定义活动，或通过指定自定义活动类型ID通过Get Lead Activities检索它们。

将自定义活动用于无需更新或覆盖的与某个人相关的数据。 例如，将事件出席情况记录为“已出席事件”活动。

将自定义对象用于可更改的人员相关记录，如学生注册。 可以更新自定义对象，但不能更新自定义活动。

输入成员是活动对象的数组。 一次最多可提交300条活动记录。

`leadId`、`activityDate`、`activityTypeId`、`primaryAttributeValue`和属性成员是必需的。 属性数组必须包含非主属性。 请为其指定名称（字段名称）或apiName（API名称），并为要设置的值指定值。

```http
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

除以下端点外，活动端点的超时为30秒：

- 获取分页令牌： 300秒
- 添加自定义活动：90秒
