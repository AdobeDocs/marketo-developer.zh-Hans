---
title: 计划成员
feature: REST API
description: 使用Marketo REST API读取、创建、更新和删除程序成员，管理标准和自定义字段，以及使用可搜索字段进行查询。
exl-id: 22f29a42-2a30-4dce-a571-d7776374cf43
TQID: https://experienceleague.adobe.com/scEHyXYq9C7cCS1kIX810wG7ahT9fsa448NwIfBmzQM
product_v2:
  - id: b27e5950-9033-45ac-9f86-eb22e567f615
feature_v2:
  - id: b0bb9048-d951-48d8-8232-45cf248a7e27
  - id: c5f60233-d5ea-4453-a799-0ad258b4d399
  - id: d1d0a9cd-295d-4976-8c39-ddae266f240e
  - id: e64968b2-4ee5-47f9-8cae-0588f184b9eb
role_v2:
  - id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
topic_v2:
  - id: a004cc84-67b9-4a33-a3a7-8ec7273ef4dc
  - id: eddd9b14-83bd-4ff4-9072-54a4a484abb7
source-git-commit: 3e6d310c5aec1a3435424fb122b71d825db5af0e
workflow-type: tm+mt
source-wordcount: 1670
ht-degree: 2%

---

# 计划成员

[程序成员终结点引用](https://developer.adobe.com/marketo-apis/api/mapi#tag/Program-Members)

Marketo提供了用于读取、创建、更新和删除程序成员记录的API。 商机ID字段将项目成员记录与商机记录相关联。

每个记录包含标准字段，最多可包含20个自定义字段。 这些字段存储特定于项目的成员数据，以便在表单、筛选器、触发器和流操作中使用。 您可以在Marketo Engage UI的程序[成员选项卡](https://experienceleague.adobe.com/zh-hans/docs/marketo/using/product-docs/core-marketo-concepts/programs/working-with-programs/manage-and-view-members)中查看此数据。

## 描述

[Describe程序成员](https://developer.adobe.com/marketo-apis/api/mapi#tag/Program-Members/operation/describeProgramMemberUsingGET2)终结点遵循Lead数据库对象的标准模式。

- `searchableFields`数组标识对查询有效的字段。
- `fields`数组包含元数据，如REST API名称、显示名称以及字段是否可更新。

```http
GET /rest/v1/programs/members/describe.json
```

```json
{
    "requestId": "f813#1791563c7cc",
    "result": [
        {
            "name": "API Program Membership",
            "description": "Map for API program membership fields",
            "createdAt": "2021-03-20T01:30:05Z",
            "updatedAt": "2021-03-20T01:30:05Z",
            "dedupeFields": [
                "leadId",
                "programId"
            ],
            "searchableFields": [
                [
                    "leadId"
                ],
                [
                    "myCustomField"
                ],
                [
                    "reachedSuccess"
                ],
                [
                    "statusName"
                ]
            ],
            "fields": [
                {
                    "name": "acquiredBy",
                    "displayName": "acquiredBy",
                    "dataType": "boolean",
                    "updateable": false,
                    "crmManaged": false
                },
                {
                    "name": "attendanceLikelihood",
                    "displayName": "attendanceLikelihood",
                    "dataType": "integer",
                    "updateable": false,
                    "crmManaged": false
                },
                {
                    "name": "createdAt",
                    "displayName": "createdAt",
                    "dataType": "datetime",
                    "updateable": false,
                    "crmManaged": false
                },
                {
                    "name": "isExhausted",
                    "displayName": "isExhausted",
                    "dataType": "boolean",
                    "updateable": false,
                    "crmManaged": false
                },
                {
                    "name": "leadId",
                    "displayName": "leadId",
                    "dataType": "integer",
                    "updateable": false,
                    "crmManaged": false
                },
                {
                    "name": "membershipDate",
                    "displayName": "membershipDate",
                    "dataType": "datetime",
                    "updateable": false,
                    "crmManaged": false
                },
                {
                    "name": "nurtureCadence",
                    "displayName": "nurtureCadence",
                    "dataType": "string",
                    "length": 4,
                    "updateable": false,
                    "crmManaged": false
                },
                {
                    "name": "program",
                    "displayName": "program",
                    "dataType": "string",
                    "length": 255,
                    "updateable": false,
                    "crmManaged": false
                },
                {
                    "name": "programId",
                    "displayName": "programId",
                    "dataType": "integer",
                    "updateable": false,
                    "crmManaged": false
                },
                {
                    "name": "reachedSuccess",
                    "displayName": "reachedSuccess",
                    "dataType": "boolean",
                    "updateable": false,
                    "crmManaged": false
                },
                {
                    "name": "reachedSuccessDate",
                    "displayName": "reachedSuccessDate",
                    "dataType": "datetime",
                    "updateable": false,
                    "crmManaged": false
                },
                {
                    "name": "registrationLikelihood",
                    "displayName": "registrationLikelihood",
                    "dataType": "integer",
                    "updateable": false,
                    "crmManaged": false
                },
                {
                    "name": "statusName",
                    "displayName": "statusName",
                    "dataType": "string",
                    "length": 255,
                    "updateable": false,
                    "crmManaged": false
                },
                {
                    "name": "statusReason",
                    "displayName": "statusReason",
                    "dataType": "string",
                    "length": 255,
                    "updateable": false,
                    "crmManaged": false
                },
                {
                    "name": "trackName",
                    "displayName": "trackName",
                    "dataType": "string",
                    "length": 255,
                    "updateable": false,
                    "crmManaged": false
                },
                {
                    "name": "updatedAt",
                    "displayName": "updatedAt",
                    "dataType": "datetime",
                    "updateable": false,
                    "crmManaged": false
                },
                {
                    "name": "waitlistPriority",
                    "displayName": "waitlistPriority",
                    "dataType": "integer",
                    "updateable": false,
                    "crmManaged": false
                },
                {
                    "name": "myCustomField",
                    "displayName": "myCustomField",
                    "dataType": "string",
                    "length": 255,
                    "updateable": true,
                    "crmManaged": false
                },
                {
                    "name": "registrationCode",
                    "displayName": "registrationCode",
                    "dataType": "string",
                    "length": 100,
                    "updateable": true,
                    "crmManaged": false
                },
                {
                    "name": "webinarUrl",
                    "displayName": "webinarUrl",
                    "dataType": "string",
                    "length": 2000,
                    "updateable": true,
                    "crmManaged": false
                }
            ]
        }
    ],
    "success": true
}
```

## 查询

使用[获取程序成员](https://developer.adobe.com/marketo-apis/api/mapi#tag/Program-Members/operation/getProgramMembersUsingGET)端点检索程序的成员。 该请求需要`programId`路径参数以及`filterType`和`filterValues`查询参数。

`programId`指定要搜索的程序。

`filterType`指定要用作搜索筛选器的字段。 它接受[Describe Program Member](https://developer.adobe.com/marketo-apis/api/mapi#tag/Program-Members/operation/describeProgramMemberUsingGET2)端点返回的“searchableFields”列表中的任何字段。 对于自定义字段，dataType必须为“字符串”或“整数”。

当filterType不是“leadId”时，请求最多可以处理100,000个项目成员记录。 根据您的Marketo实例配置，您会收到以下错误之一：

- 如果项目群成员的总数超过100,000，则会返回错误：“1003，成员资格总大小：100,001超过过滤器允许的100,000的限制”。
- 如果与筛选器&#x200B;_匹配的程序成员_&#x200B;的总数超过100,000，将返回错误：“1003，匹配成员大小：100,001超过此API允许的限制(100,000)”。

要查询成员数量超过限制的程序，请改用[批量程序成员提取API](bulk-program-member-extract.md)。

`filterValues`指定要搜索的值，并且最多接受300个逗号分隔的值。 该调用搜索程序成员字段与所包含的filterValues之一匹配的记录。

或者，通过将`updatedAt`指定为filterType并提供`startAt`和`endAt`日期时间参数来按日期范围过滤。 范围必须等于或少于7天。 对于日期时间值，请使用不带毫秒的ISO-8601格式。

可选的`fields`查询参数接受[Describe Program Member](https://developer.adobe.com/marketo-apis/api/mapi#tag/Program-Members/operation/describeProgramMemberUsingGET2)终结点返回的以逗号分隔的字段API名称列表。 包括时，每个响应记录都包含指定字段。 如果忽略，则默认情况下响应返回`acquiredBy`、`leadId`、`membershipDate`、`programId`和`reachedSuccess`。

默认情况下，端点最多返回300条记录。 使用`batchSize`查询参数减少此数量。

如果&#x200B;**moreResult**&#x200B;属性为true，则有更多结果可用。 继续使用返回的`nextPageToken`调用终结点，直到moreResult为false。

如果GET请求的总长度超过8KB，则端点返回HTTP错误“414， URI太长”。 要解决此限制，请将请求从GET更改为POST，添加`_method=GET`参数，并将查询字符串放入请求正文中。

```http
GET /rest/v1/programs/{programId}/members.json?filterType=statusName&filterValues=Influenced
```

```json
{
    "requestId": "109da#17915eec072",
    "result": [
        {
            "seq": 0,
            "leadId": 1789,
            "reachedSuccess": true,
            "programId": 1044,
            "acquiredBy": true,
            "membershipDate": "2020-01-08T18:10:26Z"
        },
        {
            "seq": 1,
            "leadId": 1790,
            "reachedSuccess": true,
            "programId": 1044,
            "acquiredBy": true,
            "membershipDate": "2020-01-08T18:10:26Z"
        },
        {
            "seq": 2,
            "leadId": 1791,
            "reachedSuccess": true,
            "programId": 1044,
            "acquiredBy": true,
            "membershipDate": "2020-01-08T18:10:26Z"
        },
        {
            "seq": 3,
            "leadId": 1792,
            "reachedSuccess": true,
            "programId": 1044,
            "acquiredBy": true,
            "membershipDate": "2020-01-08T18:10:26Z"
        },
        {
            "seq": 4,
            "leadId": 1793,
            "reachedSuccess": true,
            "programId": 1044,
            "acquiredBy": true,
            "membershipDate": "2020-01-08T18:10:26Z"
        },
        {
            "seq": 5,
            "leadId": 1794,
            "reachedSuccess": true,
            "programId": 1044,
            "acquiredBy": true,
            "membershipDate": "2020-01-08T18:10:26Z"
        },
        {
            "seq": 6,
            "leadId": 1795,
            "reachedSuccess": true,
            "programId": 1044,
            "acquiredBy": true,
            "membershipDate": "2020-01-08T18:10:26Z"
        },
        {
            "seq": 7,
            "leadId": 1796,
            "reachedSuccess": true,
            "programId": 1044,
            "acquiredBy": true,
            "membershipDate": "2020-01-08T18:10:26Z"
        },
        {
            "seq": 8,
            "leadId": 1797,
            "reachedSuccess": true,
            "programId": 1044,
            "acquiredBy": true,
            "membershipDate": "2020-01-08T18:10:26Z"
        },
        {
            "seq": 9,
            "leadId": 1798,
            "reachedSuccess": true,
            "programId": 1044,
            "acquiredBy": true,
            "membershipDate": "2020-01-08T18:10:26Z"
        },
        {
            "seq": 10,
            "leadId": 1799,
            "reachedSuccess": true,
            "programId": 1044,
            "acquiredBy": true,
            "membershipDate": "2020-01-08T18:10:26Z"
        },
        {
            "seq": 11,
            "leadId": 1800,
            "reachedSuccess": true,
            "programId": 1044,
            "acquiredBy": true,
            "membershipDate": "2020-01-08T18:10:26Z"
        }
    ],
    "success": true,
    "moreResult": false
}
```

## 创建和更新

两个端点支持对项目成员进行创建和更新操作：

- 一个端点仅更新程序成员状态。
- 一个端点更新标记为“可更新”的程序成员字段。

每个端点最多可以修改300个程序成员记录。

### 项目群成员状态

使用[同步程序成员状态](https://developer.adobe.com/marketo-apis/api/mapi#tag/Program-Members/operation/syncProgramMemberStatusUsingPOST)终结点为一个或多个成员创建或更新程序状态。

必需的参数包括：

- `programId`：指定包含要创建或更新的成员的程序的路径参数。
- `statusName`：指定要应用于潜在客户列表的程序状态。 statusName必须与项目频道的可用状态匹配。 使用[Get Channels](https://developer.adobe.com/marketo-apis/api/asset#tag/Channels/operation/getAllChannelsUsingGET)终结点检索有效状态。 如果商机的状态比指定的statusName具有更大的步骤值，则请求将跳过该商机。
- `input`：与项目群成员相对应的`leadId`值的数组。 每个调用最多可提交300个潜在客户ID。

端点对每个记录执行更新插入。 如果leadId与项目群成员关联，则端点会更新其成员资格状态。 否则，它将创建一个项目群成员记录，将该记录与leadId相关联，并分配成员资格状态。

响应包括`status`个“已更新”、“已创建”或“已跳过”。 跳过的结果还包括`reasons`数组。 `seq`字段是将每个提交的记录与响应顺序关联的索引。

如果呼叫成功，“更改计划状态”活动将写入商机的活动日志。

```http
POST /rest/v1/programs/{programId}/members/status.json
```

```text
Content-Type: application/json
```

```json
{
    "statusName":"Influenced",
    "input":[
        {
            "leadId": 1800
        },
        {
            "leadId": 1801
        },
        {
            "leadId": 1235
        }
    ]
}
```

```json
{
    "requestId": "14b2d#17916378ec5",
    "result": [
        {
            "seq": 0,
            "status": "skipped",
            "reasons": [
                {
                    "code": "1037",
                    "message": "Lead skipped because it is already in or past this status"
                }
            ]
        },
        {
            "seq": 1,
            "status": "updated",
            "leadId": 1801
        },
        {
            "seq": 2,
            "status": "created",
            "leadId": 1235
        }
    ],
    "success": true
}
```

### 项目群成员数据

使用[同步程序成员数据](https://developer.adobe.com/marketo-apis/api/mapi#tag/Program-Members/operation/syncProgramMemberDataUsingPOST)终结点更新一个或多个成员的程序成员字段数据。 您可以修改任何自定义字段或任何由[描述程序成员](https://developer.adobe.com/marketo-apis/api/mapi#tag/Program-Members/operation/describeProgramMemberUsingGET2)端点标记为“可更新”的标准字段。

必需的参数包括：

- `programId`：指定包含要更新的成员的程序的路径参数。
- `input`：一个数组，其元素包含`leadId`和一个或多个要按API名称更新的字段。 每个调用最多可提交300个潜在客户ID。

端点更新每个记录。 leadId必须与项目群成员关联，并且每个字段都必须可更新。

响应包含`status`个“已更新”或“已跳过”。 跳过的结果还包括`reasons`数组。 `seq`字段是将每个提交的记录与响应顺序关联的索引。

如果调用成功，“更改项目成员数据”活动将写入商机的活动日志。

```http
POST /rest/v1/programs/{programId}/members.json
```

```text
Content-Type: application/json
```

```json
{
    "input":[
        {
            "leadId": 1789,
            "registrationCode": "dcff5f12-a7c7-11eb-bcbc-0242ac130002"
        },
        {
            "leadId": 1790,
            "registrationCode": "c0404b78-d3fd-47bf-82c4-d16f3852ab3a"
        },
        {
            "leadId": 1003,
            "registrationCode": "aa880c57-75b8-426b-a33a-fbf6302d7cb4"
        }
    ]
}
```

```json
{
    "requestId": "edc3#1791659b8d2",
    "result": [
        {
            "seq": 0,
            "status": "updated",
            "leadId": 1789
        },
        {
            "seq": 1,
            "status": "updated",
            "leadId": 1790
        },
        {
            "seq": 2,
            "status": "skipped",
            "reasons": [
                {
                    "code": "1013",
                    "message": "Membership not found"
                }
            ]
        }
    ],
    "success": true
}
```

## 字段

程序成员对象包含标准字段和可选自定义字段。 标准字段存在于每个Marketo Engage订阅中，而用户根据需要创建自定义字段。

每个字段均由显示名称、API名称和数据类型等属性定义。 这些属性统称为元数据。

以下端点查询、创建和更新程序成员对象上的字段。 API用户必须具有具有&#x200B;**读写架构标准字段**&#x200B;权限和/或&#x200B;**读写架构自定义字段**&#x200B;权限的角色。

### 查询字段

按API名称查询一个程序成员字段或检索所有程序成员字段。 角色权限决定响应可以包括标准字段和/或自定义字段。 响应还包括隐藏字段。

#### 按名称

[按名称获取项目群成员字段](https://developer.adobe.com/marketo-apis/api/mapi#tag/Program-Members/operation/getProgramMemberFieldByNameUsingGET)终结点为项目群成员对象上的一个字段检索元数据。 所需的`fieldApiName`路径参数指定字段的API名称。

响应类似于“描述项目群成员”响应，但包含其他元数据。 例如，`isCustom`属性指示字段是否为自定义字段。

```http
GET /rest/v1/programs/members/schema/fields/{fieldApiName}.json
```

```json
{
    "requestId": "15416#17e955554de",
    "result": [
        {
            "displayName": "Status",
            "name": "statusName",
            "description": null,
            "dataType": "string",
            "length": 255,
            "isHidden": false,
            "isHtmlEncodingInEmail": true,
            "isSensitive": false,
            "isCustom": false,
            "isApiCreated": false
        }
    ],
    "success": true
}
```

#### 浏览

[获取程序成员字段](https://developer.adobe.com/marketo-apis/api/mapi#tag/Program-Members/operation/getProgramMemberFieldsUsingGET)终结点检索程序成员对象中所有字段的元数据。 默认情况下，它最多返回300条记录。 使用`batchSize`查询参数减少此数量。

如果`moreResult`属性为true，则有更多结果可用。 继续使用返回的`nextPageToken`调用终结点，直到moreResult为false。

```http
GET /rest/v1/programs/members/schema/fields.json?batchSize=5
```

```json
{
    "requestId": "102f6#17e9557f123",
    "result": [
        {
            "displayName": "Acquired By",
            "name": "acquiredBy",
            "description": null,
            "dataType": "boolean",
            "isHidden": false,
            "isHtmlEncodingInEmail": false,
            "isSensitive": false,
            "isCustom": false,
            "isApiCreated": false
        },
        {
            "displayName": "Nurture Cadence",
            "name": "nurtureCadence",
            "description": null,
            "dataType": "string",
            "length": 4,
            "isHidden": false,
            "isHtmlEncodingInEmail": true,
            "isSensitive": false,
            "isCustom": false,
            "isApiCreated": false
        },
        {
            "displayName": "Nurture Exhausted",
            "name": "isExhausted",
            "description": null,
            "dataType": "boolean",
            "isHidden": false,
            "isHtmlEncodingInEmail": false,
            "isSensitive": false,
            "isCustom": false,
            "isApiCreated": false
        },
        {
            "displayName": "Member Date",
            "name": "membershipDate",
            "description": null,
            "dataType": "datetime",
            "isHidden": false,
            "isHtmlEncodingInEmail": false,
            "isSensitive": false,
            "isCustom": false,
            "isApiCreated": false
        },
        {
            "displayName": "Program",
            "name": "program",
            "description": null,
            "dataType": "string",
            "length": 255,
            "isHidden": false,
            "isHtmlEncodingInEmail": true,
            "isSensitive": false,
            "isCustom": false,
            "isApiCreated": false
        }
    ],
    "success": true,
    "nextPageToken": "BC7J6EPVLT6T4B5FKUU3APCYN4======",
    "moreResult": true
}
```

### 创建字段

[创建程序成员字段](https://developer.adobe.com/marketo-apis/api/mapi#tag/Program-Members/operation/createProgramMemberFieldUsingPOST)端点在程序成员对象上创建自定义字段。 它提供的功能与[Marketo Engage UI](https://experienceleague.adobe.com/zh-hans/docs/marketo/using/product-docs/core-marketo-concepts/programs/working-with-programs/program-member-custom-fields)相当。 您最多可以使用此端点创建20个自定义字段。

在生产Marketo Engage实例中创建每个字段之前，请仔细考虑每个字段。 创建字段后不能将其删除；[只能隐藏它](https://experienceleague.adobe.com/zh-hans/docs/marketo/using/product-docs/administration/field-management/delete-a-custom-field-in-marketo)。 未使用的字段会为实例添加待筛选项。

所需的`input`参数是程序成员字段对象的数组。 每个对象都包含一个或多个属性。

- 必需的属性为`displayName`、`name`和`dataType`。 它们分别对应于UI显示名称、API名称和字段类型。
- 可选属性为`description`、`isHidden`、`isHtmlEncodingInEmail`和`isSensitive`。

`name`和`displayName`属性具有以下命名规则：

- `name`属性必须是唯一的，以字母开头，并且只包含字母、数字或下划线。
- *`isplayName`必须是唯一的，并且不能包含特殊字符。

常见的惯例是将[驼峰式大小写](https://en.wikipedia.org/wiki/Camel_case#)应用于`displayName`以生成`name`。 例如，“我的自定义字段”的`displayName`生成“myCustomField”的`name`。

```http
POST /rest/v1/programs/members/schema/fields.json
```

```json
{
  "input": [
    {
        "displayName": "PMCF Custom Field 03",
        "name": "pMCFCustomField03",
        "description": "My third custom field",
        "dataType": "string"
    }
  ]
}
```

```json
{
    "requestId": "13a7#17e955fcb44",
    "result": [
        {
            "name": "pMCFCustomField03",
            "status": "created"
        }
    ],
    "success": true
}
```

### 更新字段

[更新程序成员字段](https://developer.adobe.com/marketo-apis/api/mapi#tag/Program-Members/operation/updateProgramMemberFieldUsingPOST)终结点更新了程序成员对象上的一个自定义字段。 Marketo Engage UI中提供的大多数字段更新也可通过API获得。 下表总结了二者的差异。

| 属性 | 可由API更新？ | 可通过UI更新？ | 可由API更新？ | 可通过UI更新？ |
| --- | --- | --- | --- | --- |
| 数据类型 | 否 | 否 | 否 | 是 |
| 描述 | 是 | 是 | 是 | 是 |
| 显示名称 | 否 | 否 | 是 | 是 |
| isCustom | 否 | 否 | 否 | 否 |
| isHidden | 否 | 是 | 是（如果由API创建） | 是 |
| isHtmlEncodingInEmail | 是 | 是 | 是 | 是 |
| isSensitive | 是 | 是 | 是 | 是 |
| length | 否 | 否 | 否 | 否 |
| name | 否 | 否 | 否 | 否 |

请求需要以下参数：

- `fieldApiName`：一个路径参数，它指定要更新的字段的API名称。
- `input`：一个数组，它包含一个具有一个或多个属性的潜在客户字段对象。

```http
POST /rest/v1/programs/members/schema/fields/pMCFCustomField03.json
```

```json
{
  "input": [
      {
        "displayName": "Lunch Preference",
        "description": "Attendee food preference",
        "isHtmlEncodingInEmail": true
      }
  ]
}
```

```json
{
    "requestId": "215f#17e95663955",
    "result": [
        {
            "name": "pMCFCustomField03",
            "status": "updated"
        }
    ],
    "success": true
}
```

## 删除

使用[删除程序成员](https://developer.adobe.com/marketo-apis/api/mapi#tag/Program-Members/operation/deleteProgramMemberUsingPOST)终结点删除程序成员记录。 所需的`programId`路径参数指定包含要删除成员的程序。

请求正文包含`input`个潜在客户ID数组。 每个调用最多允许300个潜在客户ID。

响应包含`status`个“已删除”或“已跳过”。 跳过的结果还包括`reasons`数组。 `seq`字段是将每个提交的记录与响应顺序关联的索引。

```http
POST /rest/v1/programs/{programId}/members/delete.json
```

```text
Content-Type: application/json
```

```json
{
    "input":[
        {
            "leadId": 1235
        },
        {
            "leadId": 77
        }
    ]
}
```

```json
{
    "requestId": "302a#17916619417",
    "result": [
        {
            "seq": 0,
            "status": "deleted",
            "leadId": 1235
        },
        {
            "seq": 1,
            "status": "skipped",
            "reasons": [
                {
                    "code": "1037",
                    "message": "Lead not in program"
                }
            ]
        }
    ],
    "success": true
}
```
