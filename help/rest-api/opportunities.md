---
title: 商机
feature: REST API
description: Marketo REST API可以使用SFDC或Dynamics同步来描述、查询、创建和更新机会、删除重复和可搜索字段、限制以及只读行为。
exl-id: 46451285-4125-4857-890a-575069a68288
TQID: https://experienceleague.adobe.com/rBDJcXWQrN5qyKRWHyzVC-sc9BH2mQFLm7fKUk-NUn8
product_v2: id: b27e5950-9033-45ac-9f86-eb22e567f615
feature_v2: id: c5f60233-d5ea-4453-a799-0ad258b4d399
role_v2: id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
topic_v2: id: a004cc84-67b9-4a33-a3a7-8ec7273ef4dc
source-git-commit: 3e6d310c5aec1a3435424fb122b71d825db5af0e
workflow-type: tm+mt
source-wordcount: 708
ht-degree: 0%

---

# 商机

[机会端点引用](https://developer.adobe.com/marketo-apis/api/mapi#tag/Opportunities)

Marketo提供了用于读取、写入、创建和更新商机记录的API。 在Marketo中，中间的Opportunity Role对象将机会记录链接到潜在客户和联系人记录。 因此，一个机会可以关联到许多单个潜在客户。

API会公开这两种对象类型。 与大多数Lead Database对象类型一样，每个对象类型都有一个返回对象元数据的相应Describe调用。

对于启用了[SFDC Sync](https://experienceleague.adobe.com/docs/marketo/using/product-docs/crm-sync/salesforce-sync/sfdc-sync-details/sfdc-sync-field-sync.html?lang=en)或[Microsoft Dynamics Sync](https://experienceleague.adobe.com/docs/marketo/using/product-docs/crm-sync/microsoft-dynamics/microsoft-dynamics-sync-details/microsoft-dynamics-sync-user-sync.html?lang=en)的订阅，机会API提供了只读访问权限。

## 描述

使用Lead Database对象的标准模式描述Opportunity记录。

```http
GET /rest/v1/opportunities/describe.json
```

```json
{
   "requestId":"185d6#14b51985ff0",
   "success":true,
   "result":[
      {
         "name":"opportunity",
         "displayName":"Opportunity",
         "createdAt":"2015-02-03T22:36:23Z",
         "updatedAt":"2015-02-03T22:36:24Z",
         "idField":"marketoGUID",
         "dedupeFields":[
            "externalOpportunityId"
         ],
         "searchableFields":[
            [
               "externalOpportunityId"
            ],
            [
               "marketoGUID"
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
               "name":"createdAt",
               "displayName":"Created At",
               "dataType":"datetime",
               "updateable":false
            },
            {
               "name":"updatedAt",
               "displayName":"Updated At",
               "dataType":"datetime",
               "updateable":false
            },
            {
               "name":"externalOpportunityId",
               "displayName":"External Opportunity Id",
               "dataType":"string",
               "length":50,
               "updateable":false
            }
         ]
      }
   ]
}
```

关键响应字段包括：

- `idField`：标识机会主键marketoGUID。 此系统生成的密钥支持读取和更新操作，但不支持插入。
- `dedupeFields`：标识用于插入操作的有效键。 对于机会，唯一键是externalOpportunityId。
- `searchableFields`：标识对查询有效的字段。 这些字段为externalOpportunityId和marketoGUID。

## 查询

[查询商机](https://developer.adobe.com/marketo-apis/api/mapi#tag/Opportunities/operation/getOpportunitiesUsingGET)的模式非常遵循Leads API。 但是，`filterType`参数仅接受在相应Describe响应或dedupeFields的`searchableFields`数组中列出的字段。

对于自定义机会字段，可搜索的Fields数组中只显示String或Integer类型的字段。

```http
GET /rest/v1/opportunities.json?filterType=marketoGUID&filterValues=dff23271-f996-47d7-984f-f2676861b5fa&dff23271-f996-47d7-984f-f2676861b5fc,dff23271-f996-47d7-984f-f2676861b5fb
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

您可以包含以下可选查询参数：

- `fields`：返回其他机会字段。
- `nextPageToken`：通过大于批次大小的结果集的页数。
- `batchSize`：指定批次大小。 缺省值和最大值是300。

当您请求`fields`的列表时，未返回的请求字段的隐含值为null。

## 创建和更新

机会遵循一些限制的销售线索API模式。 `action`值为createOnly、createOrUpdate和updateOnly。

- 对于createOnly或createOrUpdate模式，请在每个记录中包含externalOpportunityId字段。
- 对于updateOnly模式，请使用marketoGUID或externalOpportunityId。
- 如果未指定，则模式默认为createOrUpdate。

潜在客户API中的`lookupField`参数不可用。 dedupeBy参数将替换该参数，并且仅在操作为updateOnly时有效。

dedupeBy值为“dedupeFields”和“idField”，Describe响应分别将其标识为externalOpportunityId和marketoGUID。 如果未指定dedupeBy ，则默认为dedupeFields模式。 “name”字段不能为空。

一次最多可以提交300条记录。

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

响应包括每个记录的以下值：

- `marketoGUID`：记录标识符。
- `status`：单个记录是成功还是失败。
- `seq`：提交的记录的索引，它将请求记录与响应顺序关联。

### 字段

公司对象包含由显示名称、API名称和数据类型等属性定义的字段。 这些属性统称为元数据。

公司对象上的以下端点查询字段。 API用户必须具有具有`Read-Write Schema Standard Field`权限和/或`Read-Write Schema Custom Field`权限的角色。

### 查询字段

按API名称查询一个公司字段或检索所有公司字段。

#### 按名称

[按名称获取机会字段](https://developer.adobe.com/marketo-apis/api/mapi#tag/Opportunities/operation/getOpportunityFieldByNameUsingGET)终结点为公司对象上的一个字段检索元数据。 所需的`fieldApiName`路径参数指定字段的API名称。

响应类似于Describe Opportunity响应，但包含其他元数据。 例如，`isCustom`属性指示字段是否为自定义字段。

```http
GET /rest/v1/opportunities/schema/fields/externalOpportunityId.json
```

```json
{
    "requestId": "12331#17e9779cb4b",
    "result": [
        {
            "displayName": "SFDC Oppty Id",
            "name": "externalOpportunityId",
            "description": null,
            "dataType": "string",
            "length": 50,
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

[获取机会字段](https://developer.adobe.com/marketo-apis/api/mapi#tag/Opportunities/operation/getOpportunityFieldsUsingGET)终结点检索公司对象中所有字段的元数据。 默认情况下，它最多返回300条记录。 使用`batchSize`查询参数减少此数量。

如果`moreResult`属性为true，则有更多结果可用。 继续使用返回的`nextPageToken`调用终结点，直到moreResult为false。

```http
GET /rest/v1/opportunities/schema/fields.json?batchSize=5
```

```json
{
    "requestId": "b4a#17e995b31da",
    "result": [
        {
            "displayName": "SFDC Oppty Id",
            "name": "externalOpportunityId",
            "description": null,
            "dataType": "string",
            "length": 50,
            "isHidden": false,
            "isHtmlEncodingInEmail": true,
            "isSensitive": false,
            "isCustom": false,
            "isApiCreated": false
        },
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
            "displayName": "Description",
            "name": "description",
            "description": null,
            "dataType": "string",
            "length": 2000,
            "isHidden": false,
            "isHtmlEncodingInEmail": true,
            "isSensitive": false,
            "isCustom": false,
            "isApiCreated": false
        },
        {
            "displayName": "Type",
            "name": "type",
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
            "displayName": "Stage",
            "name": "stage",
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
    "nextPageToken": "E5ZONGE4SAHALYYW6FS25KB5BM======",
    "moreResult": true
}
```

#### 删除

按重复数据消除字段或ID字段删除机会。 将`deleteBy`参数设置为dedupeFields或idField。 缺省值为dedupeFields。

请求正文包含要删除的`input`机会数组。 每个呼叫最多允许300个机会。

```http
POST /rest/v1/opportunities/delete.json
```

```json
{
   "deleteBy":"dedupeFields",
   "input":[
      {
         "externalOpportunityId":"19UYA31581L000000"
      },
      {
         "externalOpportunityId":"29UYA31581L000000"
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
         "marketoGUID":"cff23271-f996-47d7-984f-f2676861b5fb",
         "status":"deleted"
      }
   ]
}
```

## 超时

- 除非另有说明，否则Opportunity端点的超时为30秒。
- 同步机会的超时为60秒。
- 删除机会的超时为60秒。
