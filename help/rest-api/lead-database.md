---
title: 潜在客户数据库
feature: REST API, Database
description: Marketo Lead Database API指南，涵盖对象、CRUD和Describe方法、查询模式、批量限制和CRM集成限制。
exl-id: e62e381f-916b-4d56-bc3d-0046219b68d3
TQID: https://experienceleague.adobe.com/7lGbhE92lvIE-XkMyUIaK9GrreZVRdM-WVZTpHARhxE
product_v2: id: b27e5950-9033-45ac-9f86-eb22e567f615
feature_v2: id: c5f60233-d5ea-4453-a799-0ad258b4d399
role_v2: id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
topic_v2: id: a004cc84-67b9-4a33-a3a7-8ec7273ef4dc
source-git-commit: 3e6d310c5aec1a3435424fb122b71d825db5af0e
workflow-type: tm+mt
source-wordcount: 1058
ht-degree: 1%

---

# 潜在客户数据库

Marketo潜在客户数据库API与Marketo交换人员和人员相关数据。 此数据包括活动、机会和公司。

## 对象

Lead数据库包括以下对象：

- 潜在客户
- 公司/帐户
- 命名帐户
- 商机
- 机会角色
- SalesPerson
- 自定义对象
- 活动
- 列表和计划成员资格

大多数Lead Database对象都支持Create、Read、Update和Delete方法。 Describe方法提供了每种对象类型的可用字段。 对于非Lead对象，它还标识用于重复数据消除的字段以及在检索记录时可搜索的字段。

潜在客户对象支持最广泛的功能集，因为潜在客户在Marketo应用程序中的用途最多。

## API

有关Lead数据库API端点、参数和建模信息的完整列表，请参阅[Lead数据库API端点参考](https://developer.adobe.com/marketo-apis/api/mapi)。

当实例具有本机Microsoft Dynamics或Salesforce.com CRM集成时， Company 、 Opportunity 、 Opportunity Role和Sales Person API将被禁用。 CRM将管理这些记录，因此您无法通过Marketo API访问或更新它们。

- 最大批次大小（标准）：300条记录
- 最大批次大小（批量）：10MB文件
- 默认批次大小：300条记录
- 内容类型标头（标准）： application/json
- Content-type标头（批量）：multipart/form-data

## 描述

Describe API适用于Leads、Companies、Opportunity、Roles、SalesPerson和Custom Object。 使用它检索对象元数据和可用于更新和查询的字段。

除Describe Lead外，每个Describe端点都将返回：

- `dedupeFields`：可用于重复数据删除的键。
- `searchableFields`：可用于查询的键。

```http
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

在此示例中，`dedupeFields`是复合键。 当您使用`dedupeFields`模式进行以后的创建和更新时，请为每个角色包含`externalOpportunityId`、`leadId`和`role`。

`searchableFields`数组列出了可用于查询角色记录的字段。 此列表包括`externalOpportunityId`、`leadId`和`role`的复合键。

`fields`响应参数为每个字段提供以下信息：

- 名称。
- `displayName`，如Marketo UI中所示。
- 数据类型。
- 字段在创建后是否可以更新。
- 字段长度（如果适用）。

## 查询

Lead Database对象共享引用一个字段的简单键的基本查询模式。

```http
GET /rest/v1/{type}.json?filterType={field to query}&filterValues={comma-separated list of possible values}
```

对于除潜在客户之外的所有对象，从对应的Describe响应中的`searchableFields`中选择`{field to query}`。 提供最多300个值的逗号分隔列表。

您还可以包含以下可选查询参数：

- `batchSize`：一个整数，它指定要返回的结果数。 缺省值和最大值是300。
- `nextPageToken`：从上一调用返回的分页令牌。 有关详细信息，请参阅[分页令牌](paging-tokens.md)。
- `fields`：要为每个记录返回的以逗号分隔的字段名称列表。 查看有效字段的相应描述。 如果请求未返回的字段，则其值默认为空。
- `_method`：使用POST HTTP方法提交查询。 请参阅_method=GET部分以了解用法。

以下示例查询业务机会：

```http
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

此调用中的`filterType`是“idField”，而不是“marketoGUID”。 “idField”和“dedupeFields”都是特殊情况，允许您为相应一个或多个字段使用别名。 尽管调用没有明确设置“marketoGUID”，但它仍然是查找字段。

对象描述中由`idField`和`dedupeFields`标识的字段或字段集对于查询始终有效`filterTypes`。 此调用返回与filterValues中的GUID匹配的记录。 如果没有匹配的记录，则响应将指示success并返回空的结果数组。

如果匹配的记录集超过300或指定的`batchSize`（取较小者），则响应将包含值为true的`moreResult`和`nextPageToken`。 在后续调用中包含该令牌以检索更多记录。 有关详细信息，请参阅[分页令牌](paging-tokens.md)。

### 长URI

URI可以超过REST服务的8KB限制，例如，当您按GUID进行查询时。 在这种情况下，请使用HTTP POST方法而不是GET并添加`_method=GET`查询参数。

将其余查询参数作为“application/x-www-form-urlencoded”字符串传递到POST正文中。 同时传递关联的Content-type标头。

```http
POST /rest/v1/opportunities.json?_method=GET
```

```text
Content-Type: application/x-www-form-urlencoded
```

```text
filterType=idField&filterValues=dff23271-f996-47d7-984f-f2676861b5fa&dff23271-f996-47d7-984f-f2676861b5fc,dff23271-f996-47d7-984f-f2676861b5fb,544fb7f5-2ddf-4fca-ae32-7e6ef1415e9f,f1ba41a2-69d1-4a35-9807-0e159d66f2c9,f7521272-3331-4a89-a768-222baff2f894
```

查询复合键时还需要`_method=GET`参数。

### 复合键

要查询复合键，请提交带有JSON正文的POST请求。 仅当`filterType`是具有多个字段的`dedupeFields`选项时才使用此模式。

复合键当前仅由Opportunity Roles和一些自定义对象使用。 以下示例使用复合键从`dedupeFields`查询机会角色：

```http
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

JSON对象接受用于简单键查询的所有查询参数，但`filterValues`除外。 提供JSON对象的“输入”数组而不是`filterValues`。 每个对象必须包含复合键中的每个字段。 在此示例中，字段为`externalOpportunityId`、`leadId`和`role`。

请求根据提供的输入查询`roles`并返回匹配结果。 如果响应包含`moreResult=true`和`nextPageToken`，请在下一个请求中包含所有原始输入和`nextPageToken`。

## 创建和更新

通过发送带有JSON主体的POST请求创建和更新Lead Database记录。 Opportunity 、 Roles 、 Custom Objects 、 Company和SalesPerson使用相同的界面。 潜在客户使用不同的界面，有关该界面的说明，请参见Leads文档。

唯一需要的参数是`input`，一个最多包含300个对象的数组。 每个对象都包含要插入或更新的字段。

您还可以包含以下可选参数：

- `action`：接受`createOnly`、`updateOnly`或`createOrUpdate`。 如果忽略，则模式默认为`createOrUpdate`。
- `dedupeBy`：当操作设置为createOnly或`createOrUpdate`时，接受`idField`或`dedupeFields`。 如果忽略，则模式默认为`dedupeFields`。

当`dedupeBy`为`idField`时，描述中列出的`idField`用于重复数据删除，必须包含在每个记录中。 `idField`模式与`createOnly`模式不兼容。

当`dedupeBy`为`dedupeFields`时，在每个记录中包括对象描述中列出的每个`dedupeFields`字段。

传递字段值时，数据库将值`null`或空字符串写入为`null`。

```http
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

除潜在客户API外，创建和更新调用在`result`数组中的每个对象中返回`seq`字段。 编号对应于请求中已更新记录的位置。

每个结果还返回对象类型的`idField`值和`status`的“已创建”、“已更新”或“已跳过”。 如果跳过状态，结果将包含“原因”数组。 每个原因对象都包含一个代码和消息，用于解释跳过记录的原因。 有关详细信息，请参阅[错误代码](error-codes.md)。

### 删除

除潜在客户外，Lead Database对象使用标准的删除界面。 除了输入之外，唯一必需的参数是接受idField或dedupeFields的`deleteBy,`。

以下示例删除自定义对象：

```http
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

响应包括`seq`、`status`和`marketoGUID`。 对于跳过的记录，它还包括`reasons`。

有关特定对象类型的CRUD操作的详细信息，请参阅有关该对象的文档。
