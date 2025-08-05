---
title: 公司
feature: REST API
description: 使用Marketo API配置公司数据。
exl-id: 80e514a2-1c86-46a7-82bc-e4db702189b0
source-git-commit: 3649db037a95cfd20ff0a2c3d81a3b40d0095c39
workflow-type: tm+mt
source-wordcount: '564'
ht-degree: 1%

---

# 公司

[公司终结点引用](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Companies)

公司表示潜在客户记录所属的组织。 通过使用`externalCompanyId`同步潜在客户[或](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Leads/operation/syncLeadUsingPOST)批量潜在客户导入[端点填充其对应的](bulk-lead-import.md)字段，将潜在客户添加到公司。 将商机添加到公司后，您无法从该公司中删除该商机（除非将该商机添加到其他公司）。 链接到公司记录的潜在客户将直接继承公司记录中的值，就像值存在于潜在客户自己的记录中一样。

对于已启用[SFDC同步](https://experienceleague.adobe.com/docs/marketo/using/product-docs/crm-sync/salesforce-sync/sfdc-sync-details/sfdc-sync-field-sync.html?lang=zh-Hans)或[Microsoft Dynamics同步](https://experienceleague.adobe.com/docs/marketo/using/product-docs/crm-sync/microsoft-dynamics/microsoft-dynamics-sync-details/microsoft-dynamics-sync-user-sync.html?lang=zh-Hans)的订阅，公司API是只读访问权限。

## 描述

描述公司对象会为您提供必须与之交互的所有信息。

```
GET /rest/v1/companies/describe.json
```

```json
{
   "success":true,
   "requestId":"5847#14d44113ad7",
   "result":[
      {
         "name":"Company",
         "description":"Company object",
         "createdAt":"2015-05-11T17:11:32Z",
         "updatedAt":"2015-05-11T17:11:32Z",
         "idField":"id",
         "dedupeFields":[
            "externalCompanyId"
         ],
         "searchableFields":[
            [
               "externalCompanyId"
            ],
            [
               "id"
            ],
            [
               "company"
            ]
         ],
         "fields":[
            {
               "name":"createdAt",
               "displayName":"Created At",
               "dataType":"datetime",
               "updateable":false
            },
            {
               "name":"externalCompanyId",
               "displayName":"External Company Id",
               "dataType":"string",
               "length":100,
               "updateable":false
            },
            {
               "name":"id",
               "displayName":"Id",
               "dataType":"integer",
               "updateable":false
            },
            {
               "name":"updatedAt",
               "displayName":"Updated At",
               "dataType":"datetime",
               "updateable":false
            },
            {
               "name":"annualRevenue",
               "displayName":"Annual Revenue",
               "dataType":"currency",
               "updateable":true
            }
            {
               "name":"company",
               "displayName":"Company Name",
               "dataType":"string",
               "length":255,
               "updateable":true
            }
         ]
      }
   ]
}
```

## 查询

[查询公司](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Companies/operation/getCompaniesUsingGET)的模式紧跟潜在客户API的模式，添加了限制，`filterType`参数接受在Describe Companies调用的searchableFields数组或dedupeFields中列出的字段。

`filterType`和`filterValues`是必需的查询参数。  `fields`、`nextPageToken`和`batchSize`是可选参数。  这些参数的功能与Leads和Opportunities API中的相应参数类似。 在请求`fields`的列表时，如果请求了特定字段但未返回，则该值默认为null。

如果省略fields参数，则返回的默认字段集为：

- ID
- 删除重复字段
- 更新时间
- createdat

```
GET /rest/v1/companies.json?filterType=id&filterValues=3433,5345
```

```json
{
   "requestId":"e42b#14272d07d78",
   "success":true,
   "result":[
      {
         "seq":0,
         "id":3433,
         "externalCompanyId":"19UYA31581L000000",
         "company":"Google"
      },
      {
         "seq":1,
         "id":5345,
         "externalCompanyId":"29UYA31581L000000",
         "company":"Yahoo"
      }
   ]
}
```

## 创建和更新

[同步公司](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Companies/operation/syncCompaniesUsingPOST)终结点接受包含公司对象数组的必需`input`参数。 与机会一样，创建和更新公司有三种模式：createOnly、updateOnly和createOrUpdate。  在请求的`action`参数中指定了模式。 `dedupeBy`和`action`参数都是可选的，它们分别默认为dedupeFields和createOrUpdate模式。

```
POST /rest/v1/companies.json
```

```
Content-Type: application/json
```

```json
{
   "action":"createOrUpdate",
   "dedupeBy":"dedupeFields",
   "input":[
      {
         "externalCompanyId":"19UYA31581L000000",
         "company":"Google"
      },
      {
         "externalCompanyId":"29UYA31581L000000",
         "company":"Yahoo"
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
         "id":1232
      },
      {
         "seq":1,
         "status":"created",
         "id":1323
      }
   ]
}
```

### 字段

公司对象包含一组字段。 每个字段定义由一组描述该字段的属性组成。 属性的示例包括显示名称、API名称和数据类型。 这些属性统称为元数据。

以下端点允许您查询公司对象上的字段。 这些API要求拥有权限的API用户必须具有具有`Read-Write Schema Standard Field`或`Read-Write Schema Custom Field`权限之一或两者的角色。

### 查询字段

查询公司字段非常简单。 您可以按API名称查询单个公司字段或查询所有公司字段集。

#### 按名称

[按名称获取公司字段](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Companies/operation/getCompanyFieldByNameUsingGET)终结点为公司对象上的单个字段检索元数据。 所需的`fieldApiName`路径参数指定字段的API名称。 响应类似于Describe Company端点，但包含其他元数据，例如`isCustom`属性，该属性指示字段是否为自定义字段。

```
GET /rest/v1/companies/schema/fields/industry.json
```

```json
{
    "requestId": "88f6#17e976d6ab4",
    "result": [
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
        }
    ],
    "success": true
}
```

#### 浏览

[获取公司字段](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Companies/operation/getCompanyFieldsUsingGET)终结点检索公司对象中所有字段的元数据。 默认情况下，最多返回300条记录。 您可以使用`batchSize`查询参数来减少此数量。 如果`moreResult`属性为true，则表示有更多的结果可用。 继续调用此端点，直到moreResult属性返回false，这意味着没有可用的结果。 从此API返回的`nextPageToken`应始终在此调用的下一个迭代中重用。

```
GET /rest/v1/companies/schema/fields.json?batchSize=5
```

```json
{
    "requestId": "b50e#17e995c2d35",
    "result": [
        {
            "displayName": "Company Name",
            "name": "company",
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
            "displayName": "Site",
            "name": "site",
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
            "displayName": "Website",
            "name": "website",
            "description": null,
            "dataType": "url",
            "length": 255,
            "isHidden": false,
            "isHtmlEncodingInEmail": true,
            "isSensitive": false,
            "isCustom": false,
            "isApiCreated": false
        },
        {
            "displayName": "Main Phone",
            "name": "mainPhone",
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
            "displayName": "Annual Revenue",
            "name": "annualRevenue",
            "description": null,
            "dataType": "currency",
            "isHidden": false,
            "isHtmlEncodingInEmail": false,
            "isSensitive": false,
            "isCustom": false,
            "isApiCreated": false
        }
    ],
    "success": true,
    "nextPageToken": "L7XD3EFJ3OLFZKXKJBWYULOTRA======",
    "moreResult": true
}
```

### 删除

删除条件在`input`数组中指定，该数组包含搜索值列表。  在`deleteBy`参数中指定了删除方法。  允许值为：dedupeFields、idField。  默认值为dedupeFields。

```
Content-Type: application/json
```

```
POST /rest/v1/companies/delete.json
```

```json
{
   "deleteBy":"dedupeFields",
   "input":[
      {
         "externalCompanyId":"19UYA31581L000000"
      },
      {
         "externalCompanyId":"29UYA31581L000000"
      },
      {
         "externalCompanyId":"39UYA31581L000000"
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
         "id":1234,
         "status":"deleted"
      },
      {
         "seq":1,
         "id":56456,
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

- 除非下面说明，否则公司端点的超时为30秒
   - 同步公司：60多岁
   - 删除公司：60秒
