---
title: 命名帐户
feature: REST API
description: Marketo REST指南，用于在ABM的指定帐户上执行CRUD，其中提供了说明、查询、创建更新示例、可搜索的字段、重复数据删除规则以及无潜在客户链接。
exl-id: 2aa1d2a0-9e54-4a9a-abb1-0d0479ed3558
TQID: https://experienceleague.adobe.com/iY3UYVelm3aKuuDBCTxaVCbkXfwnJzDjV3Kvn9rcNbA
product_v2: id: b27e5950-9033-45ac-9f86-eb22e567f615
feature_v2: id: c5f60233-d5ea-4453-a799-0ad258b4d399
role_v2: id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
topic_v2: id: a004cc84-67b9-4a33-a3a7-8ec7273ef4dc
source-git-commit: 3e6d310c5aec1a3435424fb122b71d825db5af0e
workflow-type: tm+mt
source-wordcount: 590
ht-degree: 1%

---

# 命名帐户

[命名帐户终结点引用](https://developer.adobe.com/marketo-apis/api/mapi#tag/Named-Accounts)

Marketo提供了API用于对命名帐户执行CRUD操作，以与Marketo ABM一起使用。 这些API遵循标准的Lead Database接口模式，并提供Describe 、 Create/Update 、 Delete和Query选项。

目前，Marketo API仅支持对指定帐户执行CRUD操作。 您无法通过API将潜在客户关联到指定帐户。

## 描述

描述命名帐户通过Marketo API为使用命名帐户返回元数据。 响应包括有效的可搜索字段和API可用的所有字段。

命名帐户的`idField`始终为`marketoGUID`。 对象的`name`字段是唯一可用的`dedupeField`和创建键。

```http
GET /rest/v1/namedaccounts/describe.json
```

```json
{
   "requestId":"d65e#156c27ac57d",
   "result":[
      {
         "name":"Named Account",
         "description":"Marketo standard account attribute map",
         "createdAt":"2016-08-18T20:16:41Z",
         "updatedAt":"2016-08-18T20:16:41Z",
         "idField":"marketoGUID",
         "dedupeFields":[
            "name"
         ],
         "searchableFields":[
            [
               "marketoGUID",
            ],
            [
               "annualRevenue"
            ],
            [
               "city"
            ],
            [
               "country"
            ],
            [
               "domainName"
            ],
            [
               "industry"
            ],
            [
               "logoUrl"
            ],
            [
               "membershipCount"
            ],
            [
               "name"
            ],
            [
               "numberOfEmployees"
            ],
            [
               "opptyAmount"
            ],
            [
               "opptyCount"
            ],
            [
               "score1"
            ],
            [
               "score2"
            ],
            [
               "score3"
            ],
            [
               "score4"
            ],
            [
               "score5"
            ],
            [
               "sicCode"
            ],
            [
               "state"
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
               "name":"annualRevenue",
               "displayName":"annualRevenue",
               "dataType":"currency",
               "updateable":true
            },
            {
               "name":"city",
               "displayName":"city",
               "dataType":"string",
               "length":255,
               "updateable":true
            },
            {
               "name":"country",
               "displayName":"country",
               "dataType":"string",
               "length":255,
               "updateable":true
            }
         ]
      }
   ],
   "success":true
}
```

### 查询

使用filterType和最多300个逗号分隔的filterValues查询命名帐户。 filterType可以是Describe响应的`searchableFields`成员中返回的任何单个字段。 每个filterValues条目都必须是字段数据类型的有效值。

要返回特定字段，请传递一个字段参数，其中包含以逗号分隔的字段列表。 一个查询页最多包含300条记录。 要检索其他记录，请使用调用返回的nextPageToken 。

```http
GET /rest/v1/namedaccounts.json?filterType=name&filterValues=Google,Yahoo
```

```json
{
    "requestId": "6dac#157d4ddc9d7",
    "result": [
        {
            "seq": 0,
            "marketoGUID": "16efafdd-0148-4ea7-8782-f451d7c6345d",
            "createdAt": "2016-10-17T22:49:04Z",
            "name": "Google",
            "updatedAt": "2016-10-17T22:49:04Z"
        },
        {
            "seq": 1,
            "marketoGUID": "44d62353-7f9d-4d43-b9cc-7ef0f7a09137",
            "createdAt": "2016-10-17T22:49:04Z",
            "name": "Yahoo",
            "updatedAt": "2016-10-17T22:49:04Z"
        }
    ],
    "success": true
}
```

### 创建和更新

使用标准Lead Database模式创建和更新指定帐户。 在POST请求的JSON正文的输入成员中传递记录。 您最多可以包含300条记录。

请求成员包括：

- `input`：唯一必需的成员。
- `action`：可选成员，接受createOnly、updateOnly或createOrUpdate。 缺省值为createOrUpdate。
- `dedupeBy`：可选成员，仅在操作为updateOnly时可用。 它接受dedupeFields或idField，这两个字段分别与name和marketoGUID字段相对应。

```http
POST /rest/v1/namedaccounts.json
```

```text
Content-Type: application/json
```

```json
{
   "action":"updateOnly",
   "dedupeBy":"dedupeFields",
   "input":[
      {
         "name":"Google",
         "domainName":"www.google.com"
      },
      {
         "name":"Yahoo",
         "domainName":"www.yahoo.com"
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
         "marketoGUID":"dff23271-f996-47d7-984f-f2676861b5fc"
      }
   ]
}
```

### 字段

命名帐户对象包含由显示名称、API名称和数据类型等属性定义的字段。 这些属性统称为元数据。

公司对象上的以下端点查询字段。 API用户必须具有具有“读写架构标准字段”权限和/或“读写架构自定义字段”权限的角色。

### 查询字段

按API名称查询一个指定帐户字段或检索所有公司字段。

#### 按名称

[按名称获取命名帐户字段](https://developer.adobe.com/marketo-apis/api/mapi#tag/Named-Accounts/operation/getNamedAccountFieldByNameUsingGET)终结点为命名帐户对象上的一个字段检索元数据。 必填的fieldApiName路径参数指定字段的API名称。

响应类似于“描述命名帐户”响应，但包含其他元数据。 例如，isCustom属性指示字段是否为自定义字段。

```http
GET /rest/v1/namedaccounts/schema/fields/annualRevenue.json
```

```json
{
    "requestId": "371c#17e979c5d1f",
    "result": [
        {
            "displayName": "Annual Revenue",
            "name": "annualRevenue",
            "description": null,
            "dataType": "currency",
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

[获取命名帐户字段](https://developer.adobe.com/marketo-apis/api/mapi#tag/Named-Accounts/operation/getNamedAccountFieldByNameUsingGET)终结点检索命名帐户对象中所有字段的元数据。 默认情况下，它最多返回300条记录。 使用batchSize查询参数减少此数量。

如果moreResult属性为true，则有更多结果可用。 使用返回的nextPageToken继续调用端点，直到moreResult为false。

```http
GET /rest/v1/namedaccounts/schema/fields.json?batchSize=5
```

```json
{
    "requestId": "f287#17e995bd0c5",
    "result": [
        {
            "displayName": "Name",
            "name": "name",
            "description": null,
            "dataType": "string",
            "length": 255,
            "isHidden": false,
            "isHtmlEncodingInEmail": true,
            "isSensitive": false,
            "isCustom": false,
            "isApiCreated": false
        },
        {
            "displayName": "Domain Name",
            "name": "domainName",
            "description": null,
            "dataType": "string",
            "length": 255,
            "isHidden": false,
            "isHtmlEncodingInEmail": true,
            "isSensitive": false,
            "isCustom": false,
            "isApiCreated": false
        },
        {
            "displayName": "Industry",
            "name": "industry",
            "description": null,
            "dataType": "string",
            "length": 255,
            "isHidden": false,
            "isHtmlEncodingInEmail": true,
            "isSensitive": false,
            "isCustom": false,
            "isApiCreated": false
        },
        {
            "displayName": "SIC Code",
            "name": "sicCode",
            "description": null,
            "dataType": "string",
            "length": 40,
            "isHidden": false,
            "isHtmlEncodingInEmail": true,
            "isSensitive": false,
            "isCustom": false,
            "isApiCreated": false
        },
        {
            "displayName": "City",
            "name": "city",
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
    "nextPageToken": "N42LHXWEULHZ3N2I77DKOJUVOY======",
    "moreResult": true
}
```

### 删除

通过发送带有JSON正文的POST请求删除命名帐户。 该请求包括必需的输入成员和可选的deleteBy成员。

deleteBy成员接受“dedupeFields”或“idField”，它们分别与name和marketoGUID相对应。 如果未设置，则默认为dedupeFields。 输入成员最多接受300条记录。 每个记录都包含name或marketoGUID，具体取决于deleteBy设置。

```http
POST /rest/v1/namedaccounts/delete.json
```

```text
Content-Type: application/json
```

```json
{
   "deleteBy":"dedupeFields",
   "input":[
      {
         "name":"Google"
      },
      {
         "name":"Yahoo"
      },
      {
         "name":"Marketo"
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
         "status":"deleted"
      },
      {
         "seq":1,
         "id":"dff23271-f996-47d7-984f-f2676861b5fc",
         "status":"deleted"
      },
      {
         "seq":2,
         "status":"skipped",
         "reasons":[
            {
               "code":"1013",
               "message":"Record not found"
            }
         ]
      }
   ]
}
```

## 超时

- 除非另有说明，否则命名帐户端点的超时为30秒。
- 同步指定帐户的超时为120秒。
- 删除指定帐户的超时为60秒。
