---
title: 潜在客户数据库
feature: REST API, Database
description: 处理主潜在客户数据库。
exl-id: e62e381f-916b-4d56-bc3d-0046219b68d3
source-git-commit: 66add4c38d0230c36d57009de985649bb67fde3e
workflow-type: tm+mt
source-wordcount: '1345'
ht-degree: 0%

---

# 潜在客户数据库

Marketo Lead Database API是Marketo提供的最常用的API，因为它们允许来自Marketo的人员和人员相关数据（如活动、商机和公司）的数据交换。

## 对象

潜在客户数据库对象包括：

- 潜在客户
- 公司/帐户
- 指定帐户
- 机会
- 机会角色
- SalesPerson
- 自定义对象
- 活动
- 列表和计划成员资格

这些对象中的大多数至少包括Create、Read、Update和Delete方法。 其中还包括一个“描述”方法，该方法提供了每种类型的可用字段列表，用于重复数据消除的字段列表（对于非Lead对象），以及可搜索哪些字段以检索记录。 我们为潜在客户提供的功能集最丰富，因为他们在Marketo应用程序中拥有的功能种类最多。

## API

有关Lead数据库API端点的完整列表，包括参数和建模信息，请参阅[Lead数据库API端点参考](https://developer.adobe.com/marketo-apis/api/mapi/)。

对于启用了本机CRM集成(Microsoft Dynamics或Salesforce.com)的实例，将禁用Company、Opportunity、Opportunity Role和Sales Person API。 记录在启用时通过CRM进行管理，并且无法通过Marketo的API访问或更新。

- 最大批次大小（标准）：300条记录
- 最大批次大小（批量）：10MB文件
- 默认批次大小：300条记录
- 内容类型标头（标准）： application/json
- Content-type标头（批量）：multipart/form-data

## 描述

对于Lead 、 Companies 、 Opportunity 、 Roles 、 SalesPerson和Custom Objects ，提供了一个描述API。 调用此项将检索对象的元数据，以及可用于更新和查询的字段列表。 描述是设计与Marketo的正确集成的重要组成部分。 它提供了丰富的元数据，介绍了如何与对象进行交互和无法与对象进行交互，以及如何创建、更新和查询对象。 除了描述潜在客户之外，每个潜在客户都在`dedupeFields`响应参数中返回可用于`deduplication`的键列表。 字段列表可用作在`searchableFields`响应参数中进行查询的键。

```
GET /rest/v1/opportunities/roles/describe.json
```

```json
{  
   "requestId":"185d6#14b51985ff0",
   "success":true,
   "result":[  
      {  
         "name":"opportunityRole",
         "displayName":"Opportunity Role",
         "createdAt":"2015-02-03T22:36:23Z",
         "updatedAt":"2015-02-03T22:36:24Z",
         "idField":"marketoGUID",
         "dedupeFields":[  
            "externalOpportunityId",
            "leadId",
            "role"
         ],
         "searchableFields":[  
            [  
               "externalOpportunityId",
               "leadId",
               "role"
            ],
            [  
               "marketoGUID"
            ],
            [  
               "leadId"
            ],
            [  
               "externalOpportunityId"
            ]
         ],
         "fields":[  
            {  
               "name":"marketoGUID",
               "displayName":"Marketo GUID",
               "dataType":"string",
               "length":36,
               "updateable":false
            },
            {  
               "name":"externalOpportunityId",
               "displayName":"External Opportunity Id",
               "dataType":"string",
               "length":50,
               "updateable":false
            },
            {  
               "name":"leadId",
               "displayName":"Lead Id",
               "dataType":"integer",
               "updateable":false
            },
            {  
               "name":"role",
               "displayName":"Role",
               "dataType":"string",
               "length":50,
               "updateable":false
            },
            {  
               "name":"isPrimary",
               "displayName":"Is Primary",
               "dataType":"boolean",
               "updateable":true
            },
            {  
               "name":"externalCreatedDate",
               "displayName":"External Created Date",
               "dataType":"datetime",
               "updateable":true
            }
         ]
      }
   ]
}
```

在此示例中，`dedupeFields`实际上是一个复合键。 这意味着，在将来的更新和创建中，在使用`dedupeFields`模式时，您必须为每个角色包含`externalOpportunityId`、`leadId`和`role`的所有三个。 `searchableFields`数组还提供了可用于查询角色记录的字段列表。 这还包括`externalOpportunityId`、`leadId`和`role`的复合键。

还有一个字段响应参数，该参数将提供每个字段的名称、显示在Marketo UI中的`displayName`、字段的数据类型、创建后是否可以更新以及字段的长度（如果适用）。

## 查询

Lead Database对象都共享用于查询简单键的基本模式，其中仅引用一个字段。

```
GET /rest/v1/{type}.json?filterType={field to query}&filterValues={comma-separated list of possible values}
```

对于除潜在客户之外的所有对象，您可以从相应的describe调用的searchableFields中选择{field to query}，并撰写最多300个值的逗号分隔列表。 还有以下可选查询参数：

- `batchSize` — 要返回的结果数的整数计数。 默认值和最大值是300。
- `nextPageToken` — 从上一次分页调用返回的令牌。 有关详细信息，请参阅[分页令牌](paging-tokens.md)。
- `fields` — 要为每个记录返回的以逗号分隔的字段名称列表。 有关有效字段列表，请参阅相应的描述。 如果请求了某个特定字段但未返回，则该值将默认为空。
- `_method` — 用于使用POSTHTTP方法提交查询。 请参阅下面的_method=GET部分以了解用法。

下面是一个快速示例，我们来了解一下查询销售机会：

```
GET /rest/v1/opportunities.json?filterType=idField&filterValues=dff23271-f996-47d7-984f-f2676861b5fa&dff23271-f996-47d7-984f-f2676861b5fc,dff23271-f996-47d7-984f-f2676861b5fb
```

```json
{  
   "requestId":"e42b#14272d07d78",
   "success":true,
   "result":[  
      {  
         "seq":0,
         "marketoGUID":"dff23271-f996-47d7-984f-f2676861b5fa ",
         "externalOpportunityId":"19UYA31581L000000",
         "name":"Chairs",
         "description":"Chairs",
         "amount":"1604.47",
         "source":"Inbound Sales Call/Email"
      },
      {  
         "seq":1,
         "marketoGUID":"dff23271-f996-47d7-984f-f2676861b5fc ",
         "externalOpportunityId":"29UYA31581L000000",
         "name":"Big Dog Day Care-Phase12",
         "description":"Big Dog Day Care-Phase12",
         "amount":"1604.47",
         "source":"Email"
      }
   ]
}
```

此调用中指定的`filterType`是“idField”，而不是“marketoGUID”。 这和“dedupeFields”都是特殊情况，在这种情况下，对应于idField或dedupeFields的字段可以按此方式进行别名。 “marketoGUID”仍然是调用中生成的查找字段，但它未在调用中显式设置。 对象描述的`idField`和`dedupeFields`指示的字段和/或字段集对于查询始终有效`filterTypes`。 此调用将搜索与filterValues中包含的GUID匹配的记录，并返回任何匹配的记录。 如果没有使用此方法找到记录，响应仍指示成功，但结果数组将为空，因为搜索已成功执行，但没有要返回的记录。

如果查询中的记录集超过300或指定的`batchSize`（以较小者为准），则响应将具有一个值为true的成员`moreResult`和一个可包含在后续调用中以检索更多集合的`nextPageToken`。 有关详细信息，请参阅[分页令牌](paging-tokens.md)。

### 长URI

有时，例如通过GUID进行查询时，您的URI可能会很长，并且超过REST服务所允许的8KB。 在这种情况下，您必须使用HTTPPOST方法而不是GET，并添加查询参数`_method=GET`。 此外，其余查询参数必须作为“application/x-www-form-urlencoded”字符串传递到POST正文中，并传递关联的Content-type标头。

```
POST /rest/v1/opportunities.json?_method=GET
```

```
Content-Type: application/x-www-form-urlencoded
```

```
filterType=idField&filterValues=dff23271-f996-47d7-984f-f2676861b5fa&dff23271-f996-47d7-984f-f2676861b5fc,dff23271-f996-47d7-984f-f2676861b5fb,544fb7f5-2ddf-4fca-ae32-7e6ef1415e9f,f1ba41a2-69d1-4a35-9807-0e159d66f2c9,f7521272-3331-4a89-a768-222baff2f894
```

除了长URI外，查询复合键时也需要此参数。

### 复合键

查询复合键的模式与简单键不同，因为它需要提交具有JSON主体的POST。 这并非在所有情况下都需要，仅当使用具有多个字段的`dedupeFields`选项作为`filterType`时才需要。 目前，复合键仅由Opportunity角色和一些自定义对象使用。 让我们看一下使用`dedupeFields`中的复合键查询Opportunity Roles的示例：

```
POST /rest/v1/opportunities/roles.json?_method=GET
```

```json
{  
   "filterType":"dedupeFields",
   "fields":[  
      "marketoGuid",
      "externalOpportunityId",
      "leadId",
      "role"
   ],
   "input":[  
      {  
        "externalOpportunityId":"Opportunity1",
        "leadId": 1,
        "role": "Captain"
      },
      {  
        "externalOpportunityId":"Opportunity2",
        "leadId": 1872,
        "role": "Commander"
      },
      {  
        "externalOpportunityId":"Opportunity3",
        "leadId": 273891,
        "role": "Lieutenant Commander"
      }
   ]
}
```

JSON对象的结构大部分是平面的，具有简单键的查询的所有查询参数都是有效成员，`filterValues`除外。 有一个JSON对象的“输入”数组，而不是筛选器值，对于每个对象，复合键中的每个字段都必须有一个成员；在这种情况下，它们是`externalOpportunityId`、`leadId`和`role`。 这将对提供的输入执行`roles`的查询并返回匹配结果。 如果响应返回带有`moreResult=true`和`nextPageToken`的参数，则必须包括所有原始输入和`nextPageToken`，查询才能正确执行。

## 创建和更新

为潜在客户数据库记录创建和更新，均通过带有JSON主体的POST执行。 Opportunity 、 Roles 、 Custom Objects 、 Company和SalesPerson的界面是相同的。 Lead的界面稍有不同，您可以在此处详细了解它。

唯一必需的参数是一个名为`input`的数组，它最多包含300个对象，每个对象具有要作为成员插入/更新的字段。 您还可以选择包含`action`参数，该参数可以是： `createOnly`、`updateOnly`或`createOrUpdate`之一。 如果省略该操作，则模式默认为`createOrUpdate`。 `dedupeBy`是另一个可选参数，可在操作设置为createOnly或`createOrUpdate`时使用。 ` dedupeBy`可以是`idField`或`dedupeFields`。 如果选择`idField`，则说明中列出的`idField`将用于重复数据删除，并且必须包含在每个记录中。 `idField`模式与`createOnly`模式不兼容。 如果选择`dedupeFields` ，则在使用的对象描述中列出`dedupeFields`，并且每个记录中都必须包含每个描述。 如果省略`dedupeBy`参数，则模式默认为`dedupeFields`。

传递字段值列表时，`null`的值或空字符串将作为`null`写入数据库。

```
POST /rest/v1/opportunities.json
```

```json
{  
   "action":"createOrUpdate",
   "dedupeBy":"dedupeFields",
   "input":[  
      {  
         "externalOpportunityId":"19UYA31581L000000",
         "name":"Chairs",
         "description":"Chairs",
         "amount":"1604.47",
         "source":"Inbound Sales Call/Email"
      },
      {  
         "externalOpportunityId":"29UYA31581L000000",
         "name":"Big Dog Day Care-Phase12",
         "description":"Big Dog Day Care-Phase12",
         "amount":"1604.47",
         "source":"Email"
      }
   ]
}
```

```json
{  
   "requestId":"e42b#14272d07d78",
   "success":true,
   "result":[  
      {  
         "seq":0,
         "status":"updated",
         "marketoGUID":"dff23271-f996-47d7-984f-f2676861b5fb"
      },
      {  
         "seq":1,
         "status":"created",
         "marketoGUID":"cff23271-f996-47d7-984f-f2676861b5fb"
      }
   ]
}
```

除潜在客户API外，创建或更新潜在客户数据库对象的调用在`result`数组中的每个对象中返回`seq`字段。 列出的编号对应于在请求中更新记录的顺序。 每个项都返回对象类型的`idField`值和`status`。 状态字段指示“已创建”、“已更新”或“已跳过”之一。  如果跳过状态，则还会有相应的“原因”数组，其中一个或多个原因对象包括代码和消息，指示跳过记录的原因。 有关其他详细信息，请参阅[错误代码](error-codes.md)。

### 删除

除潜在客户外，删除界面是Lead数据库对象的标准界面。 除输入外，只有一个必需的参数`deleteBy,`可以具有idField或dedupeFields值。 让我们看一下删除一些自定义对象。

```
POST /rest/v1/customobjects/{name}/delete.json
```

```json
{  
   "deleteBy":"dedupeFields",
   "input":[  
      {  
         "vin":"19UYA31581L000000"
      },
      {  
         "vin":"29UYA31581L000000"
      },
      {  
         "vin":"39UYA31581L000000"
      }
   ]
}
```

```json
{
   "requestId":"e42b#14272d07d78",
   "success":true,
   "result":[
      {
         "seq":0,
         "marketoGUID":"dff23271-f996-47d7-984f-f2676861b5fb",
         "status": "deleted"
      },
      {
         "seq":1,
         "marketoGUID":"da42707c-4dc4-4fc1-9fef-f30a3017240a",
         "status": "deleted"
      },
      {
         "seq":2,
         "status": "skipped"
         "reasons":[
            {
               "code":"1013",
               "message":"Object not found"
            }
         ]
      }
   ]
}
```

您现在应该已经熟悉`seq`、`status`、`marketoGUID`和`reasons`。

有关使用每种对象类型的CRUD操作的更多详细信息，请查看其各自的页面。
