---
title: 指定帐户
feature: REST API
description: 通过API处理命名帐户。
exl-id: 2aa1d2a0-9e54-4a9a-abb1-0d0479ed3558
source-git-commit: 981ed9b254f277d647a844803d05a1a2549cbaed
workflow-type: tm+mt
source-wordcount: '679'
ht-degree: 0%

---

# 指定帐户

[命名帐户终结点引用](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Named-Accounts)

Marketo提供了一组API，用于对命名帐户执行CRUD操作以用于Marketo ABM。 这些API遵循商机数据库API的标准接口模式，提供Describe、Create/Update、Delete和Query选项。

目前，通过Marketo的API提供的唯一与ABM相关的功能是针对指定帐户的CRUD操作；无法通过任何API将潜在客户关联到指定帐户。

## 描述

描述命名帐户将通过Marketo的API返回与命名帐户的使用相关的元数据，包括在查询时可搜索的有效字段列表，以及可供API使用的所有字段列表。 命名帐户的`idField`始终为`marketoGUID`，唯一可用的`dedupeField`，创建键是对象的`name`字段。

```
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

查询命名帐户基于filterType和一组最多300个逗号分隔的filterValues的使用。 `filterType`可以是命名帐户的describe结果的`searchableFields`成员中返回的任何单个字段，而filterValues可以是字段数据类型的任意有效输入。 要从中返回一组特定的字段，必须传递一个字段参数，其中的值是要在响应中返回的以逗号分隔的字段列表。 像其他查询选项一样，单个查询页面的最大记录数为300，并且必须使用调用返回的nextPageToken请求集中的其他记录。

```
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

创建和更新指定帐户遵循标准潜在客户数据库模式。 必须在POST请求的JSON主体的输入成员中传递记录。 `input`是唯一必需的成员，`action`和`dedupeBy`是可选成员。 在输入中最多可包含300条记录。 操作可以是createOnly、updateOnly或createOrUpdate之一。 如果未指定，则操作默认为createOrUpdate。 dedupeBy只能在action为updateOnly时指定，并且只接受分别与name和marketoGUID字段对应的dedupeFields或idField中的一个。

```
POST /rest/v1/namedaccounts.json
```

```
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

命名帐户对象包含一组字段。 每个字段定义由一组描述该字段的属性组成。 属性的示例包括显示名称、API名称和数据类型。 这些属性统称为元数据。

以下端点允许您查询公司对象上的字段。 这些API要求拥有权限的API用户具有读写架构标准字段或读写架构自定义字段权限中的一个或两个角色。

### 查询字段

查询指定帐户字段非常简单。 您可以按API名称查询单个指定帐户字段，或查询所有公司字段集。

#### 按名称

[按名称获取命名帐户字段](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Named-Accounts/operation/getNamedAccountFieldByNameUsingGET)终结点为命名帐户对象上的单个字段检索元数据。 必填的fieldApiName路径参数指定字段的API名称。 响应类似于描述命名帐户端点，但包含其他元数据，例如表示字段是否为自定义字段的isCustom属性。

```
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

[获取命名帐户字段](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Named-Accounts/operation/getNamedAccountFieldByNameUsingGET)终结点检索命名帐户对象中所有字段的元数据。 默认情况下，最多返回300条记录。 可以使用batchSize查询参数来减少此数量。 如果moreResult属性为true，则表示有更多的结果可用。 继续调用此端点，直到moreResult属性返回false，这意味着没有可用的结果。 对于此调用的下一个迭代，应始终重用从此API返回的nextPageToken。

```
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

删除操作是通过JSON POST请求完成的，并且具有必需的输入成员和可选的deleteBy成员。 deleteBy可以是“dedupeFields”或“idField”之一，分别与name或marketoGUID相对应，如果未设置，则将默认使用dedupeFields。 输入成员接受一个最多包含300条记录的数组，每个记录包含一个成员，即name或marketoGUID，具体取决于deleteBy的设置。

```
POST /rest/v1/namedaccounts/delete.json
```

```
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

- 除非下面说明，否则命名帐户端点的超时为30秒
   - 同步指定帐户： 120秒 
   - 删除指定帐户：60秒
