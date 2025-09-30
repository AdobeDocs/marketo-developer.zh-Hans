---
title: 商机
feature: REST API
description: Marketo REST API可以使用SFDC或Dynamics同步来描述、查询、创建和更新机会、删除重复和可搜索字段、限制以及只读行为。
exl-id: 46451285-4125-4857-890a-575069a68288
source-git-commit: 7557b9957c87f63c2646be13842ea450035792be
workflow-type: tm+mt
source-wordcount: '803'
ht-degree: 0%

---

# 商机

[机会终结点引用](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Opportunities)

Marketo公开了用于读取、写入、创建和更新机会记录的API。 在Marketo中，商机记录通过中间的Opportunity Role对象链接到潜在客户和联系人记录，因此opportunity可以链接到许多单独的潜在客户。  这两种对象类型都通过API公开，并且与大多数Lead Database对象类型一样，它们都有相应的Describe调用，该调用返回有关对象类型的元数据。

对于已启用[SFDC同步](https://experienceleague.adobe.com/docs/marketo/using/product-docs/crm-sync/salesforce-sync/sfdc-sync-details/sfdc-sync-field-sync.html?lang=en)或[Microsoft Dynamics同步](https://experienceleague.adobe.com/docs/marketo/using/product-docs/crm-sync/microsoft-dynamics/microsoft-dynamics-sync-details/microsoft-dynamics-sync-user-sync.html?lang=en)的订阅，机会API是只读访问权限。

## 描述

描述Opportunity记录时遵循了Lead数据库对象的标准模式。

```
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

此响应类型最重要的字段是`idField`、`dedupeFields`和`searchableFields`。  idField表示机会的主键marketoGUID。  这是系统生成的唯一键，可用于读取和更新操作，但不能用于插入，因为它由系统管理。  dedupeFields数组指示哪些字段是插入操作的有效键；在机会的情况下，这只是externalOpportunityId。  searchableFields数组为您提供一组有效的查询字段： externalOpportunityId和marketoGUID。

## 查询

[查询机会](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Opportunities/operation/getOpportunitiesUsingGET)的模式与潜在客户API的模式非常相似，添加了限制：`filterType`参数接受`searchableFields`数组或相应描述调用（即dedupeFields）中列出的字段。  请注意，如果您使用的是自定义机会字段，那么searchableFields数组中只会列出类型为String或Integer的自定义机会字段。

```
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

您还可以包括可选查询参数`fields`，用于返回其他机会字段`nextPageToken`，用于分页处理大于批次大小`batchSize`的集，该值默认为且最大值为300。  在请求`fields`的列表时，如果请求了特定字段但未返回，则该值默认为null。

## 创建和更新

机会会密切遵循潜在客户API模式，但有一些限制。  `action`可用的值为： createOnly、createOrUpdate和updateOnly。  使用createOnly或createOrUpdate模式时，每个记录中必须包含externalOpportunityId字段。  对于updateOnly模式，可以使用marketoGUID或externalOpportunityId。  如果未指定，则该模式默认为createOrUpdate。

潜在客户API中的`lookupField`参数不可用，将被dedupeBy参数取代，该参数仅在操作为updateOnly时有效。  dedupeBy可用的值是“dedupeFields”或“idField”，这两个值分别在describe调用中指定为externalOpportunityId和marketoGUID。  如果未指定dedupeBy ，则默认为dedupeFields模式。  “name”字段不能为空。

一次最多可以提交300条记录。

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

API将为每个记录响应`marketoGUID`，并响应`status`字段（用于指示每个记录是成功还是失败）以及用于关联提交的记录与响应顺序的`seq`字段。  字段中的数字是请求中提交的记录的索引。

### 字段

公司对象包含一组字段。  每个字段定义由一组描述该字段的属性组成。  属性的示例包括显示名称、API名称和数据类型。  这些属性统称为元数据。

以下端点允许您查询公司对象上的字段。 这些API要求拥有权限的API用户必须具有具有`Read-Write Schema Standard Field`或`Read-Write Schema Custom Field`权限之一或两者的角色。

### 查询字段

查询机会字段非常简单。  您可以按API名称查询单个公司字段或查询所有公司字段集。

#### 按名称

[按名称获取机会字段](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Opportunities/operation/getOpportunityFieldByNameUsingGET)终结点检索公司对象上单个字段的元数据。  所需的`fieldApiName`路径参数指定字段的API名称。  响应类似于Describe Opportunity端点，但包含其他元数据，例如`isCustom`属性，该属性指示字段是否为自定义字段。

```
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

[获取机会字段](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Opportunities/operation/getOpportunityFieldsUsingGET)终结点检索公司对象中所有字段的元数据。  默认情况下，最多返回300条记录。  您可以使用`batchSize`查询参数来减少此数量。  如果`moreResult`属性为true，则表示有更多的结果可用。  继续调用此端点，直到moreResult属性返回false，这意味着没有可用的结果。  从此API返回的`nextPageToken`应始终在此调用的下一个迭代中重用。

```
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

您可以按重复项字段或ID字段删除业务机会。 使用值为dedupeFields或idField的`deleteBy`参数指定。 如果未指定，则缺省值为dedupeFields。 请求正文包含要删除的`input`机会数组。 每个调用最多允许300个机会。

```
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

- 除非下面说明，否则Opportunity端点的超时为30秒
   - 同步机会：60秒
   - 删除机会：60秒
