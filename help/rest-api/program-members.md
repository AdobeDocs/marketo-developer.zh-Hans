---
title: 计划成员
feature: REST API
description: 使用Marketo REST API读取、创建、更新和删除程序成员，管理标准和自定义字段，以及使用可搜索字段进行查询。
exl-id: 22f29a42-2a30-4dce-a571-d7776374cf43
source-git-commit: 7557b9957c87f63c2646be13842ea450035792be
workflow-type: tm+mt
source-wordcount: '1726'
ht-degree: 2%

---

# 计划成员

[程序成员终结点引用](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Program-Members)

Marketo公开了用于读取、创建、更新和删除项目成员记录的API。 项目群成员记录通过lead id字段与lead记录相关。 记录由一组标准字段和最多20个其他自定义字段组成（可选）。 这些字段包含每个成员特定于程序的数据，可用于表单、筛选器、触发器和流操作。 此数据可在Marketo Engage UI中程序的[成员选项卡](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/core-marketo-concepts/programs/working-with-programs/manage-and-view-members)中查看。

## 描述

[Describe Program Member](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Program-Members/operation/describeProgramMemberUsingGET2)终结点遵循潜在客户数据库对象的标准模式。 `searchableFields`数组为您提供一组有效用于查询的字段。 `fields`数组包含字段元数据，包括REST API名称、显示名称和字段更新能力。

```
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

[获取程序成员](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Program-Members/operation/getProgramMembersUsingGET)终结点允许您检索程序的成员。 它需要`programId`路径参数以及`filterType`和`filterValues`查询参数。

`programId`用于指定要搜索的程序。

`filterType`用于指定要用作搜索筛选器的字段。 它接受[Describe Program Member](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Program-Members/operation/describeProgramMemberUsingGET2)端点返回的“searchableFields”列表中的任何字段。 如果您指定的filterType是自定义字段，则自定义字段的dataType必须为“string”或“integer”。 如果指定“leadId”以外的filterType，则请求最多可以处理100,000个程序成员记录。 根据您的Marketo实例的配置方式，您会收到以下错误之一：

- 如果项目群成员的总数超过100,000，则会返回错误：“1003，成员资格总大小：100,001超过过滤器允许的100,000的限制”。
- 如果与筛选器&#x200B;_匹配的程序成员_&#x200B;的总数超过100,000，将返回错误：“1003，匹配成员大小：100,001超过此API允许的限制(100,000)”。

要查询成员计数超过限制的程序，请改用[批量程序成员提取API](bulk-program-member-extract.md)。

`filterValues`用于指定要搜索的值，并以逗号分隔的格式接受最多300个值。 该调用搜索程序成员的字段与所包含的filterValues之一匹配的记录。

或者，您可以通过将`updatedAt`指定为带有`startAt`和`endAt`日期时间参数的filterType来按日期范围过滤。 范围必须等于或少于7天。 日期时间应采用ISO-8601格式，不带毫秒。

可选的`fields`查询参数接受[Describe Program Member](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Program-Members/operation/describeProgramMemberUsingGET2)终结点返回的以逗号分隔的字段API名称列表。 包括时，响应中的每个记录都包括指定字段。 如果省略，则返回的默认字段集是`acquiredBy`、`leadId`、`membershipDate`、`programId`和`reachedSuccess`。

默认情况下，最多返回300条记录。 您可以使用`batchSize`查询参数来减少此数量。 如果&#x200B;**moreResult**&#x200B;属性为true，则表示有更多的结果可用。 继续调用此端点，直到moreResult属性返回false，这意味着没有可用的结果。 从此API返回的`nextPageToken`应始终在此调用的下一个迭代中重用。

如果GET请求的总长度超过8KB，则会返回HTTP错误：“414， URI太长”。 作为解决方法，您可以将GET更改为POST，添加`_method=GET`参数，并将查询字符串放入请求正文中。

```
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

有两个端点支持对项目成员执行创建/更新操作。 其中一项允许您仅更新项目成员状态。 另一个允许您更新标记为“可更新”的程序成员字段集。 利用这两个端点，您最多可以修改每个调用300个项目成员记录。

### 项目群成员状态

[同步程序成员状态](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Program-Members/operation/syncProgramMemberStatusUsingPOST)终结点用于创建或更新一个或多个成员的程序状态。

所需的`programId`路径参数指定包含要创建或更新的成员的程序。

所需的`statusName`参数指定要应用于潜在客户列表的程序状态。 statusName必须与项目频道的可用状态匹配。 可以使用[获取通道](https://developer.adobe.com/marketo-apis/api/asset/#tag/Channels/operation/getAllChannelsUsingGET)终结点检索有效状态。 如果商机的状态具有的步骤值大于指定的statusName，则将跳过该商机。

所需的`input`参数是对应于程序成员的`leadId`数组。 每个调用最多可提交300个潜在客户ID。 对每个记录执行更新插入操作。 如果leadId与项目群成员相关联，则其成员资格状态会更新。 如果没有，则会创建新的项目群成员记录，该记录与leadId相关联，并分配成员资格状态。

终结点以“已更新”、“已创建”或“已跳过”的`status`进行响应。 如果跳过，还将包括`reasons`数组。 终结点还将响应`seq`字段，该字段是一个索引，可用于将提交的记录与响应的顺序相关联。

如果呼叫成功，“更改计划状态”活动将写入商机的活动日志。

```
POST /rest/v1/programs/{programId}/members/status.json
```

```
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

[同步程序成员数据](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Program-Members/operation/syncProgramMemberDataUsingPOST)终结点用于更新一个或多个成员的程序成员字段数据。 您可以修改任何自定义字段或“可更新”的标准字段（请参阅[描述程序成员](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Program-Members/operation/describeProgramMemberUsingGET2)端点）。

所需的`programId`路径参数指定包含要更新的成员的程序。

所需的`input`参数是一个数组。 每个数组元素包含一个`leadId`和一个或多个要更新的字段（使用API名称）。 对每个记录执行更新操作。 leadId必须与项目群成员关联。 字段必须可更新。 每个调用最多可提交300个潜在客户ID。

终结点以“已更新”或“已跳过”的`status`进行响应。 如果跳过，还将包括`reasons`数组。 终结点还将响应`seq`字段，该字段是一个索引，可用于将提交的记录与响应的顺序相关联。

如果调用成功，“更改项目成员数据”活动将写入商机的活动日志。

```
POST /rest/v1/programs/{programId}/members.json
```

```
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

程序成员对象包含标准字段和可选自定义字段。 标准字段存在于每个Marketo Engage订阅中，而自定义字段由用户根据需要创建。 每个字段定义由一组描述该字段的属性组成。 属性的示例包括显示名称、API名称和数据类型。 这些属性统称为元数据。

以下端点允许您查询、创建和更新程序成员对象上的字段。 这些API要求拥有权限的API用户必须具有具有&#x200B;**读写架构标准字段**&#x200B;或&#x200B;**读写架构自定义字段**&#x200B;权限之一或两者的角色。

### 查询字段

查询程序成员字段非常简单。 您可以按API名称查询单个程序成员字段，或查询所有程序成员字段集。 根据所使用的角色权限，可以检索标准字段和自定义字段。 还检索隐藏字段。

#### 按名称

[按名称获取程序成员字段](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Program-Members/operation/getProgramMemberFieldByNameUsingGET)终结点检索程序成员对象上单个字段的元数据。 所需的`fieldApiName`路径参数指定字段的API名称。 响应类似于“描述程序成员”端点，但包含其他元数据，例如`isCustom`属性，该属性指示字段是否为自定义字段。

```
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

[获取程序成员字段](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Program-Members/operation/getProgramMemberFieldsUsingGET)终结点检索程序成员对象中所有字段的元数据。 默认情况下，最多返回300条记录。 您可以使用`batchSize`查询参数来减少此数量。 如果`moreResult`属性为true，则表示有更多的结果可用。 继续调用此端点，直到moreResult属性返回false，这意味着没有可用的结果。 从此API返回的`nextPageToken`应始终在此调用的下一个迭代中重用。

```
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

[创建程序成员字段](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Program-Members/operation/createProgramMemberFieldUsingPOST)终结点在程序成员对象上创建一个或多个自定义字段。 此端点提供的功能与Marketo Engage UI[中的](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/core-marketo-concepts/programs/working-with-programs/program-member-custom-fields)类似。 您可以使用此端点创建最多20个自定义字段。

请仔细考虑您在使用API的Marketo Engage生产实例中创建的每个字段。 创建字段后，便不能将其删除（[您只能隐藏它](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/administration/field-management/delete-a-custom-field-in-marketo)）。 未使用字段的激增是一种不良做法，会增加实例的混乱。

所需的`input`参数是程序成员字段对象的数组。 每个对象都包含一个或多个属性。 必需的属性是`displayName`、`name`和`dataType`，它们分别对应于字段的UI显示名称、字段的API名称和字段类型。 您可以选择指定`description`、`isHidden`、`isHtmlEncodingInEmail`和`isSensitive`。

有一些规则与`name`和`displayName`命名关联。 `name`属性必须是唯一的，以字母开头，并且只包含字母、数字或下划线。 *`isplayName`必须是唯一的，并且不能包含特殊字符。 常见的命名惯例是将[驼峰式大小写](https://en.wikipedia.org/wiki/Camel_case#)应用于`displayName`以生成`name`。 例如，“我的自定义字段”的`displayName`将生成“myCustomField”的`name`。

```
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

[更新程序成员字段](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Program-Members/operation/updateProgramMemberFieldUsingPOST)终结点更新程序成员对象上的单个自定义字段。 通常，使用Marketo Engage UI执行的字段更新操作可通过API实现。 下表总结了几项差异。

| 属性 | 可由API更新？ | 可通过UI更新？ | 可由API更新？ | 可通过UI更新？ |
|---|---|---|---|---|
| 数据类型 | 否 | 否 | 否 | 是 |
| 描述 | 是 | 是 | 是 | 是 |
| 显示名称 | 否 | 否 | 是 | 是 |
| isCustom | 否 | 否 | 否 | 否 |
| isHidden | 否 | 是 | 是（如果由API创建） | 是 |
| isHtmlEncodingInEmail | 是 | 是 | 是 | 是 |
| isSensitive | 是 | 是 | 是 | 是 |
| length | 否 | 否 | 否 | 否 |
| name | 否 | 否 | 否 | 否 |

所需的`fieldApiName`路径参数指定要更新的字段的API名称。 所需的`input`参数是一个包含单个潜在客户字段对象的数组。 字段对象包含一个或多个属性。

```
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

[删除程序成员](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Program-Members/operation/deleteProgramMemberUsingPOST)终结点用于删除程序成员记录。 所需的`programId`路径参数指定包含要删除成员的程序。 请求正文包含`input`个潜在客户ID数组。 最多300个潜在客户ID  允许每次调用。

终结点以“已删除”或“已跳过”的`status`进行响应。 如果跳过，还将包括`reasons`数组。 终结点还将响应`seq`字段，该字段是一个索引，可用于将提交的记录与响应的顺序相关联。

```
POST /rest/v1/programs/{programId}/members/delete.json
```

```
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
