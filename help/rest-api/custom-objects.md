---
title: 自定义对象
feature: REST API, Custom Objects
description: 了解如何通过REST API创建和管理Marketo自定义对象，包括列出和描述端点、元数据、关系、字段和查询。
exl-id: 88e8829b-f8f1-46d7-a753-5aa6e20e2c40
TQID: https://experienceleague.adobe.com/NWm9CjFVqQdVDJRrnE4nA299-Lg53-JR7xvY-82dUqY
product_v2: id: b27e5950-9033-45ac-9f86-eb22e567f615
feature_v2: id: b3b8a63f-51fc-40f6-a7d2-a31c5d49fb45id: c5f60233-d5ea-4453-a799-0ad258b4d399id: d1d0a9cd-295d-4976-8c39-ddae266f240e
subfeature_v2: id: ea4e3ff5-e7b9-4b4c-a5a0-dc27cc3f4275
role_v2: id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
topic_v2: id: a004cc84-67b9-4a33-a3a7-8ec7273ef4dcid: eddd9b14-83bd-4ff4-9072-54a4a484abb7
source-git-commit: 3e6d310c5aec1a3435424fb122b71d825db5af0e
workflow-type: tm+mt
source-wordcount: 2938
ht-degree: 0%

---

# 自定义对象

[**自定义对象终结点引用**](https://developer.adobe.com/marketo-apis/api/mapi#tag/Custom-Objects)

Marketo自定义对象可以与Marketo标准对象（如潜在客户和公司）相关联，也可以与其他Marketo自定义对象相关联。 在[Marketo UI](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/administration/marketo-custom-objects/create-marketo-custom-objects)中创建Marketo自定义对象，或者使用本文档中所述的自定义对象元数据API创建自定义对象。

访问自定义对象元数据API需要适当的Marketo订阅类型。 有关详细信息，请与您的CSM联系。

## 列表

除了Lead数据库对象的标准Describe、Query、Update和Delete调用之外，Custom Objects还提供[列表调用](https://developer.adobe.com/marketo-apis/api/mapi#tag/Custom-Objects/operation/getCustomObjectsUsingGET)。 端点返回目标实例中可用的自定义对象以及有关每个对象的元数据。

```http
GET /rest/v1/customobjects.json
```

```json
{
   "requestId":"185d6#14b51985ff0",
   "success":true,
   "result":[
      {
         "name":"Car",
         "displayName":"Car",
         "description":"Car owner",
         "createdAt":"2015-02-03T22:36:23Z",
         "updatedAt":"2015-02-03T22:36:24Z",
         "idField":"marketoGUID",
         "dedupeFields":["vin"],
         "searchableFields":[
            ["vin"],
            ["marketoGUID"],
            ["siebelId"]
         ],
         "relationships":[
            {
               "field":"siebelId",
               "type":"parent",
               "relatedTo":{
                  "name":"Lead",
                  "field":"siebelId"
               }
            }
         ]
      }
   ]
}
```

响应将列出每个对象的关系。 每个关系都包含：

- `field`：包含链接值的对象上的字段。
- `type`：相关对象是父对象还是子对象。
- `relatedTo`：相关对象的名称及其链接字段。

## 描述

自定义对象的[Describe调用](https://developer.adobe.com/marketo-apis/api/mapi#tag/Custom-Objects/operation/describeUsingGET_1)遵循与“机会”和“公司”相同的模式，添加了两个内容：

- `apiName`路径参数指定要描述的自定义对象类型的API名称。
- 响应包含列出了自定义对象类型可用关系的`relationships`数组。

```http
GET /rest/v1/customobjects/{apiName}/describe.json
```

```json
{
   "requestId":"185d6#14b51985ff0",
   "success":true,
   "result":[
      {
         "name":"Car",
         "displayName":"Car",
         "description":"Car owner",
         "createdAt":"2015-02-03T22:36:23Z",
         "updatedAt":"2015-02-03T22:36:24Z",
         "idField":"marketoGUID",
         "dedupeFields":["vin"],
         "searchableFields":[
            ["vin"],
            ["marketoGUID"],
            ["siebelId"]
         ],
         "relationships":[
            {
               "field":"siebelId",
               "type":"parent",
               "object":{
                  "name":"Lead",
                  "field":"siebelId"
               }
            }
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
               "name":"vin",
               "displayName":"VIN",
               "description":"Vehicle Identification Number",
               "dataType":"string",
               "length":36,
               "updateable":false
            },
            {
               "name":"siebelId",
               "displayName":"External Id",
               "description":"External Id",
               "dataType":"string",
               "length":36,
               "updateable":true
            },
            {
               "name":"make",
               "displayName":"Make",
               "dataType":"string",
               "length":36,
               "updateable":true
            },
            {
               "name":"model",
               "displayName":"Model",
               "description":"Vehicle Model",
               "dataType":"string",
               "length":255,
               "updateable":true
            },
            {
               "name":"year",
               "displayName":"Year",
               "dataType":"integer",
               "updateable":true
            },
            {
               "name":"color",
               "displayName":"Color",
               "description":"Vehicle color",
               "dataType":"String",
               "length": 255,
               "updateable":true
            }
         ]
      }
   ]
}
```

## 查询

[查询自定义对象](https://developer.adobe.com/marketo-apis/api/mapi#tag/Custom-Objects/operation/getCustomObjectsUsingGET)与查询其他Lead数据库对象略有不同。 与Describe一样，请求采用`apiName`路径参数。

对于普通filterType，发送带有所需`filterType`和`filterValues`参数的GET请求。 您还可以包括可选的`**fields**`、`batchSize`和`nextPageToken`参数。

当您请求字段列表时，未返回的请求字段的隐含值为null。

```http
GET /rest/v1/customobjects/{apiName}.json?filterType=idField&filterValues=dff23271-f996-47d7-984f-f2676861b5fa,dff23271-f996-47d7-984f-f2676861b5fb
```

```json
{
   "requestId":"e42b#14272d07d78",
   "success":true,
   "result":[
      {
         "seq":0,
         "marketoGUID":"dff23271-f996-47d7-984f-f2676861b5fa",
         "vin":"19UYA31581L000000",
         "createdAt":"2015-02-23T18:21:53Z",
         "updatedAt":"2015-02-23T18:23:41Z"
      },
      {
         "seq":1,
         "marketoGUID":"dff23271-f996-47d7-984f-f2676861b5fb",
         "vin":"29UYA31581L000000",
         "createdAt":"2015-02-23T18:21:53Z",
         "updatedAt":"2015-02-23T18:23:41Z"
      },
   ]
}
```

使用复合键进行查询时，API的行为与机会角色API类似，可接受具有JSON正文的POST请求。 正文可以包含与GET查询相同的成员，但`filterValues`除外。

请提供一个`input`对象数组，而不是筛选器值。 每个对象都包含对象类型`dedupeFields`中每个字段的成员。

```http
POST /rest/v1/customobjects/{apiName}.json?_method=GET
```

```json
{
   "filterType":"dedupeFields",
   "fields":[
      "marketoGuid",
      "Bedrooms",
      "yearBuilt"
   ],
   "input":[
      {
         "mlsNum":"1962352",
         "houseOwnerId":"42645756"
      },
      {
         "mlsNum":"2962352",
         "houseOwnerId":"52645756"
      },
      {
         "mlsNum":"3962352",
         "houseOwnerId":"62645756"
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
         "marketoGUID":"dff23271-f996-47d7-984f-f2676861b5fa",
         "Bedrooms":3,
         "yearBuilt":1948,
         "createdAt":"2015-02-23T18:21:53Z",
         "updatedAt":"2015-02-23T18:23:41Z"
      },
      {
         "seq":1,
         "marketoGUID":"dff23271-f996-47d7-984f-f2676861b5fb",
         "Bedrooms":4,
         "yearBuilt":1956,
         "createdAt":"2015-02-23T18:21:53Z",
         "updatedAt":"2015-02-23T18:23:41Z"
      },
      {
         "seq":2,
         "marketoGUID":"dff23271-f996-47d7-984f-f2676861b5fc",
         "Bedrooms":3,
         "yearBuilt":2001,
         "createdAt":"2015-02-23T18:21:53Z",
         "updatedAt":"2015-02-23T18:23:41Z"
      }
   ]
}
```

## 创建和更新

使用[同步自定义对象](https://developer.adobe.com/marketo-apis/api/mapi#tag/Custom-Objects/operation/syncCustomObjectsUsingPOST)端点创建或更新自定义对象。 使用`action`参数指定操作。 每个调用最多可创建或更新300条记录。

根据[Describe Custom Objects](https://experienceleague.adobe.com/en/docs/marketo-developer/marketo/rest/endpoint-reference#!/Custom_Objects/describeUsingGET_1)终结点返回的信息确定`input`数组中的值。 在示例car对象中，唯一的重复数据删除字段是`vin`。 使用dedupeFields模式创建或更新记录时，在输入数组的每个对象中至少包含一个`vin`字段。

```http
POST /rest/v1/customobjects/{apiName}.json
```

```json
{
   "action":"updateOnly",
   "dedupeBy":"dedupeFields",
   "input":[
      {
         "vin":"19UYA31581L000000",
         "siebelId":"f2676861b5fb",
         "make":"BMW",
         "model":"3-Series 330i",
         "year":2003
      },
      {
         "vin":"29UYA31581L000000",
         "siebelId":"f2676861b5fc",
         "make":"BMW",
         "model":"3-Series 330i",
         "year":2003
      },
      {
         "vin":"39UYA31581L000000",
         "siebelId":"f2676861b5fd",
         "make":"BMW",
         "model":"3-Series 330i",
         "year":2003
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
         "status": "updated",
         "marketoGUID":"dff23271-f996-47d7-984f-f2676861b5fb"
      },
      {
         "seq":1,
         "status": "created",
         "marketoGUID":"cff23271-f996-47d7-984f-f2676861b5fb"
      },
      {
         "seq":2,
         "status": "skipped"
         "reasons":[
            {
               "code":"1004",
               "message":"Lead not found"
            }
         ]
      }
   ]
}
```

在`idField`模式下更新记录时，`idField`始终为`marketoGUID`。 在每个记录中包含一个`marketoGUID`字段。

由于此字段是系统托管的，`idField`仅对updateOnly操作类型有效。 结果数组包含每个记录的&#x200B;**状态**。 它还包括用于成功操作的`marketoGUID`或用于失败操作的`reasons`数组。

## 删除

要[删除记录](https://developer.adobe.com/marketo-apis/api/mapi#tag/Custom-Objects/operation/deleteCustomObjectsUsingPOST)，请选择`idField`或`dedupeFields`的`deleteBy`模式。 在`input`数组的每个记录中包含相应的字段。 每个调用最多允许300条记录。

```http
POST /rest/v1/customobjects/{apiName}/delete.json
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

与更新一样，结果包含每个记录的状态。 它还包含用于成功删除的`marketoGUID`或用于失败删除的`reasons`数组。

## 自定义对象类型

自定义对象元数据API允许您远程管理自定义对象架构。 使用它可创建自定义对象类型或修改现有对象类型。 创建或修改类型后，在使用前批准它。

有关详细信息，请参阅[自定义对象产品文档](https://experienceleague.adobe.com/zh-hans/docs/marketo/using/home)。

- 您不能在Marketo UI中修改由API创建的自定义对象类型。
- 自定义对象类型的最大数量为10。
- 每种类型的自定义对象字段的最大数量为50。
- 自定义对象类型API名称和显示名称可以包含字母数字字符和下划线字符“_”。

### 查询类型

通过以下任一方式检索自定义对象类型元数据：

- 描述自定义对象类型返回一个自定义对象类型记录，并支持按审批状态筛选。
- 列表自定义对象类型返回订阅中的所有自定义对象类型，并支持按名称和审批状态进行筛选。

### 描述类型

[Describe自定义对象类型](https://developer.adobe.com/marketo-apis/api/mapi#tag/Custom-Objects/operation/describeUsingGET_1)终结点返回一个自定义对象类型的元数据。 所需的`apiName`路径参数指定要描述的类型的API名称。

如果存在批准的版本，则端点会返回该版本。 否则，将返回草稿版本。 使用可选的`state`参数请求`draft`、`approved`或`approvedWithDraft`。

```http
GET /rest/v1/customobjects/schema/{apiName}/describe.json?state=approved
```

```json
{
    "requestId": "d9bf#16876fa84b9",
    "result": [
        {
            "state": "approved",
            "version": "approved",
            "displayName": "Car",
            "description": "Automobile owned",
            "apiName": "car",
            "idField": "marketoGUID",
            "createdAt": "2019-01-22T19:12:18Z",
            "updatedAt": "2019-01-22T19:12:18Z",
            "dedupeFields": [
                "vin"
            ],
            "searchableFields": [
                [
                    "vin"
                ],
                [
                    "marketoGUID"
                ],
                [
                    "leadID"
                ]
            ],
            "relationships": [
                {
                    "field": "leadID",
                    "type": "child",
                    "relatedTo": {
                        "name": "Lead",
                        "field": "id"
                    }
                }
            ],
            "fields": [
                {
                    "name": "createdAt",
                    "displayName": "Created At",
                    "dataType": "datetime",
                    "updateable": false,
                    "crmManaged": false
                },
                {
                    "name": "marketoGUID",
                    "displayName": "Marketo GUID",
                    "dataType": "string",
                    "length": 36,
                    "updateable": false,
                    "crmManaged": false
                },
                {
                    "name": "updatedAt",
                    "displayName": "Updated At",
                    "dataType": "datetime",
                    "updateable": false,
                    "crmManaged": false
                },
                {
                    "name": "leadID",
                    "displayName": "Lead ID",
                    "dataType": "integer",
                    "updateable": true,
                    "crmManaged": false
                },
                {
                    "name": "make",
                    "displayName": "Make",
                    "dataType": "string",
                    "length": 255,
                    "updateable": true,
                    "crmManaged": false
                },
                {
                    "name": "model",
                    "displayName": "Model",
                    "dataType": "string",
                    "length": 255,
                    "updateable": true,
                    "crmManaged": false
                },
                {
                    "name": "vin",
                    "displayName": "VIN",
                    "dataType": "string",
                    "length": 255,
                    "updateable": true,
                    "crmManaged": false
                },
                {
                    "name": "year",
                    "displayName": "Year",
                    "dataType": "integer",
                    "updateable": true,
                    "crmManaged": false
                }
            ]
        }
    ],
    "success": true
}
```

响应包含：

- 元数据：state、displayName、description、apiName、idField、createdAt、updatedAt、dedupeFields、searchableFields、relationships。
- 标准字段： marketoGUID、createdAt、updatedAt。
- 自定义字段：leadId、vin、make、model、year。

### 列表类型

[列出自定义对象类型](https://developer.adobe.com/marketo-apis/api/mapi#tag/Custom-Objects/operation/listCustomObjectTypesUsingGET)终结点返回目标实例中所有可用自定义对象类型的元数据。 它类似于[列出自定义对象](https://experienceleague.adobe.com/docs/marketo-developer/marketo/soap/custom-objects/custom-objects.html?lang=en)，但包含其他元数据，如状态、关系和字段。

如果存在批准的版本，则端点会返回该版本。 否则，将返回草稿版本。

可选参数包括：

- **state**：指定要返回的版本。 有效值为&#x200B;**草稿**、**已批准**&#x200B;和&#x200B;**approvedWithDraft**。
- **names**：指定要作为逗号分隔的API名称列表返回的自定义对象类型。

```http
GET /rest/v1/customobjects/schema.json?names=purchaseHistory
```

```json
{
    "requestId": "a181#167ebe94703",
    "result": [
        {
            "state": "approved",
            "displayName": "Purchases",
            "description": "Purchase data",
            "apiName": "purchaseHistory",
            "idField": "marketoGUID",
            "createdAt": "2014-09-12T16:13:37Z",
            "updatedAt": "2014-09-12T16:13:42Z",
            "dedupeFields": [
                "lead_id",
                "product_name"
            ],
            "searchableFields": [
                [
                    "lead_id",
                    "product_name"
                ],
                [
                    "marketoGUID"
                ],
                [
                    "lead_id"
                ]
            ],
            "relationships": [
                {
                    "field": "lead_id",
                    "type": "child",
                    "relatedTo": {
                        "name": "Lead",
                        "field": "lead_id"
                    }
                }
            ],
            "fields": [
                {
                    "name": "marketoGUID",
                    "displayName": "marketoGUID",
                    "dataType": "string",
                    "length": 36,
                    "updateable": false,
                    "crmManaged": false
                },
                {
                    "name": "amount",
                    "displayName": "Amount",
                    "dataType": "float",
                    "updateable": true,
                    "crmManaged": false
                },
                {
                    "name": "lead_id",
                    "displayName": "lead_id",
                    "dataType": "integer",
                    "updateable": true,
                    "crmManaged": false
                },
                {
                    "name": "product_name",
                    "displayName": "Product Name",
                    "dataType": "string",
                    "length": 255,
                    "updateable": true,
                    "crmManaged": false
                },
                {
                    "name": "purchase_date",
                    "displayName": "Transaction Date",
                    "dataType": "datetime",
                    "updateable": true,
                    "crmManaged": false
                }
            ]
        },
        {
            "state": "approved",
            "version": "approved",
            "displayName": "Car",
            "description": "No really, it is a car!",
            "apiName": "car_c",
            "idField": "marketoGUID",
            "createdAt": "2017-02-22T19:55:51Z",
            "updatedAt": "2018-12-11T23:52:56Z",
            "dedupeFields": [
                "vin"
            ],
            "searchableFields": [
                [
                    "vin"
                ],
                [
                    "marketoGUID"
                ]
            ],
            "relationships": [],
            "fields": [
                {
                    "name": "createdAt",
                    "displayName": "Created At",
                    "dataType": "datetime",
                    "updateable": false,
                    "crmManaged": false
                },
                {
                    "name": "marketoGUID",
                    "displayName": "Marketo GUID",
                    "dataType": "string",
                    "length": 36,
                    "updateable": false,
                    "crmManaged": false
                },
                {
                    "name": "updatedAt",
                    "displayName": "Updated At",
                    "dataType": "datetime",
                    "updateable": false,
                    "crmManaged": false
                },
                {
                    "name": "color",
                    "displayName": "Color",
                    "dataType": "string",
                    "length": 255,
                    "updateable": true,
                    "crmManaged": false
                },
                {
                    "name": "make",
                    "displayName": "Make",
                    "dataType": "string",
                    "length": 255,
                    "updateable": true,
                    "crmManaged": false
                },
                {
                    "name": "model",
                    "displayName": "Model",
                    "dataType": "string",
                    "length": 255,
                    "updateable": true,
                    "crmManaged": false
                },
                {
                    "name": "vin",
                    "displayName": "VIN",
                    "dataType": "string",
                    "length": 255,
                    "updateable": true,
                    "crmManaged": false
                },
                {
                    "name": "year",
                    "displayName": "Year",
                    "dataType": "string",
                    "length": 255,
                    "updateable": true,
                    "crmManaged": false
                }
            ]
        }
    ],
    "success": true
}
```

### 创建和更新类型

#### 创建类型

使用[同步自定义对象类型](https://developer.adobe.com/marketo-apis/api/mapi#tag/Custom-Objects/operation/syncCustomObjectsUsingPOST)端点创建或更新自定义对象类型。

这些属性包括：

- **action**：控制记录操作的可选属性。 有效值为&#x200B;**createOnly**、**createOrUpdate**&#x200B;和&#x200B;**updateOnly**。 缺省值为createOrUpdate。
- **displayName**&#x200B;和&#x200B;**apiName**：除非操作是updateOnly，否则必需。 这两个参数必须唯一，以避免与客户配置的类型发生冲突。 LaunchPoint合作伙伴应在代表性命名空间的前面加上。 对于apiName，请使用小写或驼峰式大小写来将它与其他文本字符串区分开来。
- **pluralName**：指定displayName的多种形式的可选属性。
- **description**：描述自定义对象类型的可选属性。
- **showInLeadDetail**：一个可选的布尔属性，可在Marketo UI的“潜在客户数据库”页中启用自定义对象数据。 默认值为false。

请仔细选择自定义对象名称。 为每个新的自定义对象名称添加一个用于标识贵公司的字符串作为前缀。 前缀可包含字母数字字符或下划线。 此约定使对象更容易在MLM UI中找到，并帮助确保其名称是唯一的。

以下示例使用API名称“transaction”创建自定义对象类型。

```http
POST /rest/v1/customobjects/schema.json
```

```json
{
  "action":"createOnly",
  "displayName": "Transaction",
  "apiName": "transaction",
  "description": "Commerce happens"
}
```

```json
{
    "requestId": "fb9d#167f2879557",
    "result": [],
    "success": true
}
```

以下请求描述了新创建的类型。

```http
GET /rest/v1/customobjects/schema/transaction/describe.json
```

```json
{
    "requestId": "cf9b#167f28db0a9",
    "result": [
        {
            "state": "draft",
            "displayName": "Transaction",
            "description": "Commerce happens",
            "apiName": "transaction",
            "idField": null,
            "createdAt": null,
            "updatedAt": null,
            "dedupeFields": [],
            "searchableFields": [
                []
            ],
            "relationships": [],
            "fields": [
                {
                    "name": "marketoGUID",
                    "displayName": "Marketo GUID",
                    "dataType": "string",
                    "length": 36,
                    "updateable": false,
                    "crmManaged": false
                },
                {
                    "name": "createdAt",
                    "displayName": "Created At",
                    "dataType": "datetime",
                    "updateable": false,
                    "crmManaged": false
                },
                {
                    "name": "updatedAt",
                    "displayName": "Updated At",
                    "dataType": "datetime",
                    "updateable": false,
                    "crmManaged": false
                }
            ]
        }
    ],
    "success": true
}
```

响应包含：

- 元数据：state、displayName、description、apiName、idField、createdAt、updatedAt、dedupeFields、searchableFields、relationships。
- 标准字段： marketoGUID、createdAt、updatedAt。

#### 更新类型

以下示例更新API名称为“transaction”的现有类型的描述。 **apiName**&#x200B;属性是必需的。 由于该类型已存在，因此请求对可选的&#x200B;**action**&#x200B;属性使用updateOnly。

除了&#x200B;**apiName**&#x200B;之外，您还可以更新创建期间可用的属性。

```http
POST /rest/v1/customobjects/schema.json
```

```json
{
  "action":"updateOnly",
  "apiName": "transaction",
  "description":"No really, commerce happens!"
}
```

```json
{
    "requestId": "103c3#167f2223fd7",
    "result": [],
    "success": true
}
```

## 批准类型

在使用自定义对象类型之前，请批准它们。 当您使用[同步自定义对象类型](https://developer.adobe.com/marketo-apis/api/mapi#tag/Custom-Objects/operation/syncCustomObjectTypeUsingPOST)端点创建类型时，Marketo会创建一个草稿版本。 添加自定义字段后，批准草稿。 批准创建批准的版本并删除草稿。

当您使用“同步自定义对象类型”或“添加/更新/删除自定义对象类型字段”端点修改现有类型时，Marketo会创建一个草稿。 对类型或其字段的更改仅影响草稿版本。 进行更改后，批准草稿。 批准会将批准的版本替换为草稿，并删除草稿。

有关详细信息，请参阅[自定义对象审批文档](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/administration/marketo-custom-objects/approve-a-custom-object)。

自定义对象类型获得批准后，您无法：

- 更新`displayName`或`apiName`。
- 添加或删除链接字段。
- 添加或删除重复数据删除字段。

在批准类型之前，请仔细规划架构和命名约定。

### 批准类型

使用[批准自定义对象类型](https://developer.adobe.com/marketo-apis/api/mapi#tag/Custom-Objects/operation/approveCustomObjectTypeUsingPOST)端点将草稿作为新批准的版本发布。 唯一必需的参数是&#x200B;**apiName**&#x200B;路径参数。

仅当类型处于草稿状态并满足记录的[验证规则](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/administration/marketo-custom-objects/approve-a-custom-object)时，才能批准该类型。

```http
POST /rest/v1/customobjects/schema/{apiName}/approve.json
```

```json
{
    "requestId": "11d86#1685304a983",
    "result": [],
    "success": true
}
```

### 放弃类型

使用[放弃自定义对象类型草稿](https://developer.adobe.com/marketo-apis/api/mapi#tag/Custom-Objects/operation/discardCustomObjectTypeUsingPOST)端点删除草稿版本。 唯一必需的参数是`apiName`路径参数。

您只能丢弃处于草稿状态的类型。 不能放弃已批准的类型。

```http
POST /rest/v1/customobjects/schema/{apiName}/discardDraft.json
```

```json
{
    "requestId": "5228#1684edde793",
    "result": [],
    "success": true
}
```

### 删除类型

使用[删除自定义对象类型](https://developer.adobe.com/marketo-apis/api/mapi#tag/Custom-Objects/operation/deleteCustomObjectsUsingPOST)端点删除批准的版本。 唯一必需的参数是`apiName`路径参数。

此操作具有破坏性，无法撤消。 在删除类型之前，请从资源（如触发器和过滤器）中删除该类型。 使用获取依赖自定义对象的Assets端点可检索某种类型的依赖资源。

POST /rest/v1/customobjects/schema/{apiName}/delete.json

```json
{
    "requestId": "14e36#1684efc4227",
    "result": [],
    "success": true
}
```

## 自定义对象字段

默认情况下，所有自定义对象类型都包含以下标准字段：

- Marketo GUID：自定义对象类型的唯一标识符。
- 创建时间：创建自定义对象类型的日期时间。
- 更新时间：上次更新自定义对象类型的日期时间。

使用以下端点添加、更改或删除自定义字段。

- 字段的最大数量为50。
- 批准自定义对象后，您最多可以向其添加20个附加字段。
- 至少需要一个重复数据删除字段。 最多允许三个重复数据消除字段。
- 字段API名称和显示名称可以包含字母数字字符和下划线字符“_”。

有关详细信息，请参阅[自定义对象字段文档](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/administration/marketo-custom-objects/add-marketo-custom-object-fields)。

### 添加字段

使用[添加自定义对象类型字段](https://developer.adobe.com/marketo-apis/api/mapi#tag/Custom-Objects/operation/addCustomObjectTypeFieldsUsingPOST)端点向自定义对象添加一个或多个字段。 请求正文包含具有一个或多个元素的`input`数组。 每个元素都是一个JSON对象，其属性用于描述字段。

字段属性包括：

- `name`：必需。 字段的API名称，该名称对于自定义对象必须是唯一的。 使用小写或驼峰式大小写区分名称与其他文本字符串。
- `displayName`：必需。 易于用户识别的字段名称，该名称对于自定义对象必须是唯一的。
- `dataType`：必需。 字段的数据类型。 使用[获取自定义对象类型字段数据类型](https://developer.adobe.com/marketo-apis/api/mapi#tag/Custom-Objects/operation/getCustomObjectTypeFieldDataTypesUsingGET)终结点检索允许的数据类型。
- `description`：可选。 字段描述。
- `isDedupeField`：可选布尔值，指定在自定义对象更新操作期间是否将该字段用于去重。 默认值为false。 一对多关系需要重复数据删除字段。
- `relatedTo`：指定链接字段的可选对象。 对于一对多关系，`name`标识“链接对象”或父对象，`field`标识父对象中的“链接字段”或键字段。

自定义对象可以包含数据类型为“link”的字段。 链接字段在自定义对象和其他对象类型（如商机和公司）之间建立关系。 有关链接字段的详细信息，请参阅[自定义对象字段文档](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/administration/marketo-custom-objects/add-marketo-custom-object-fields)。 使用[获取自定义对象可链接对象](https://developer.adobe.com/marketo-apis/api/mapi#tag/Custom-Objects/operation/getCustomObjectTypeLinkableObjectsUsingGET)端点检索允许的链接对象。

自定义对象无法链接到具有现有链接字段的其他自定义对象。 有关详细信息，请参阅[链接字段文档](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/administration/marketo-custom-objects/add-marketo-custom-object-fields)。

### 一对多关系

对于一对多自定义对象结构，请使用链接字段将自定义对象连接到标准Lead或Company对象。 以下工作流使用[车主示例](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/administration/marketo-custom-objects/add-marketo-custom-object-link-fields#AddMarketoCustomObjectLinkFields-CreateaLinkFieldforaOne-to-ManyStructure)创建存储车信息并连接到潜在客户的自定义对象。

1. 创建&#x200B;**Car**&#x200B;对象。
1. 向&#x200B;**Car**&#x200B;对象添加字段：**VIN**&#x200B;上的重复数据删除以及指向&#x200B;**潜在客户****/潜在客户ID**的链接。
1. 批准&#x200B;**Car**&#x200B;对象。

首先，创建包含汽车特定信息的自定义对象类型。

```http
POST /rest/v1/customobjects/schema.json
```

```json
{
    "action":"createOnly",
    "displayName": "Car",
    "pluralName": "Cars"
    "apiName": "car",
    "description": "Automobile owned",
    "showInLeadDetail": true
}
```

```json
{
    "requestId": "cbaa#16876dd3da6",
    "result": [],
    "success": true
}
```

接下来，向Car自定义对象类型添加字段。 使用链接字段指定要连接的对象和字段。 在本例中，链接对象是Lead ，链接字段是ID。

使用字符串字段删除重复项(VIN)。 再添加三个字段以存储“制造”、“模型”和“年”属性。

```http
POST /rest/v1/customobjects/schema/car/addField.json
```

```json
{
  "input": [
    {
      "displayName": "Lead ID",
      "description": "Link field to Lead object",
      "name": "leadID",
      "dataType": "link",
      "relatedTo": {
        "field": "id",
        "name": "lead"
      }
    },
    {
      "displayName": "VIN",
      "description": "Vehicle ID number",
      "name": "vin",
      "dataType": "string",
      "isDedupeField": true
    },
    {
      "displayName": "Make",
      "description": "Vehicle make",
      "name": "make",
      "dataType": "string"
    },
    {
      "displayName": "Model",
      "description": "Vehicle model",
      "name": "model",
      "dataType": "string"
    },
    {
      "displayName": "Year",
      "description": "Vehicle year",
      "name": "year",
      "dataType": "integer"
    }
  ]
}

{
    "requestId": "b359#16876f17996",
    "result": [],
    "success": true
}
```

最后，批准自定义对象类型。

```http
POST /rest/v1/customobjects/schema/course/approve.json
```

```json
{
    "requestId": "460b#16896055fa3",
    "result": [],
    "success": true
}
```

### 多对多关系

多对多关系在标准对象（例如Lead或Company）和“边缘”自定义对象之间使用“bridge”自定义对象。 边缘对象是主要实体，包含描述性字段。

bridge对象解析与两个链接字段的关系。 一个字段指向父级标准对象，如一对多关系。 另一个指向边缘对象，该对象是没有链接的自定义对象。 桥接对象还可以包含描述性字段。

以下工作流使用[大学课程注册示例](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/administration/marketo-custom-objects/add-marketo-custom-object-link-fields#AddMarketoCustomObjectLinkFields-CreateaLinkFieldforaOne-to-ManyStructure)。 它会创建一个Course Edge对象和一个Enrollment bridge对象，用于将课程与潜在客户连接起来。

1. 创建&#x200B;**课程**&#x200B;边缘对象。
1. 向&#x200B;**课程：**&#x200B;添加字段，以便对&#x200B;**课程ID**&#x200B;进行重复数据删除。
1. 批准&#x200B;**课程**。
1. 创建&#x200B;**注册**&#x200B;桥接对象。
1. 向&#x200B;**注册：**&#x200B;注册ID **上的重复数据删除**&#x200B;添加字段，链接到&#x200B;**课程****/课程ID**&#x200B;字段，以及链接到**潜在客户****/潜在客户ID**。
1. 批准&#x200B;**注册**。

首先，创建包含课程特定信息的边缘对象类型：

```http
POST /rest/v1/customobjects/schema.json
```

```json
{
    "action":"createOnly",
    "displayName": "Course",
    "pluralName": "Courses",
    "apiName": "course",
    "description": "Modeling a college course, an edge object in Marketo",
    "showInLeadDetail": true
}
```

```json
{
    "requestId": "4aec#168879ede00",
    "result": [],
    "success": true
}
```

接下来，添加四个自定义字段来模拟大学课程：课程ID、课程讲师、课程位置和课程名称。 将课程ID指定为重复数据消除字段，因为至少需要一个重复数据消除字段。

```http
POST /rest/v1/customobjects/schema/course/addField.json
```

```json
{
    "input": [
        {
            "displayName": "Course ID",
            "name": "courseID",
            "dataType": "string",
            "isDedupeField": true
        },
        {
            "displayName": "Course Instructor",
            "name": "courseInstructor",
            "dataType": "string"
        },
        {
            "displayName": "Course Location",
            "name": "courseLocation",
            "dataType": "string"
        },
        {
            "displayName": "Course Name",
            "name": "courseName",
            "dataType": "string"
        }
    ]
}
```

```json
{
    "requestId": "cc36#16895b82a41",
    "result": [],
    "success": true
}
```

批准边缘对象类型，以便在链接到桥接对象类型时可以引用它。 自定义对象类型必须先获得批准，然后才能被选为链接对象。

```http
POST /rest/v1/customobjects/schema/course/approve.json
```

```json
{
    "requestId": "460b#16896055fa3",
    "result": [],
    "success": true
}
```

完成边缘对象后，创建包含注册特定信息的桥接对象类型。

```http
POST /rest/v1/customobjects/schema.json
```

```json
{
    "action": "createOnly",
    "displayName": "Enrollment",
    "pluralName": "Enrollments",
    "apiName": "enrollment",
    "description": "Bridge object for Course custom object",
    "showInLeadDetail": true
}
```

```json
{
    "requestId": "8fbb#168960f671b",
    "result": [],
    "success": true
}
```

向桥接对象类型添加两个链接字段：一个链接到Lead对象，另一个链接到Course对象。 使用“潜在客户ID”字段链接到“潜在客户”，使用“课程ID”字段链接到“课程”。

添加注册ID作为重复数据删除字段，因为至少需要一个重复数据删除字段。 然后，添加一个Grade字段以跟踪该学生的表现。

```http
POST /rest/v1/customobjects/schema/enrollment/addField.json
```

```json
{
    "input": [
        {
            "displayName": "Lead ID",
            "description": "Link field to Lead object",
            "name": "leadID",
            "dataType": "link",
            "relatedTo": {
                "field": "id",
                "name": "lead"
            }
        },
        {
            "displayName": "Course ID",
            "description": "Link field to Course object",
            "name": "courseID",
            "dataType": "link",
            "relatedTo": {
                "field": "courseID",
                "name": "course"
            }
        },
        {
            "displayName": "Enrollment ID",
            "description": "Unique ID for deduplication",
            "name": "enrollmentID",
            "dataType": "string",
            "isDedupeField": true
        },
        {
            "displayName": "Grade",
            "description": "Grade for the course",
            "name": "grade",
            "dataType": "string"
        }
    ]
}
```

```json
{
    "requestId": "7be5#168973f5052",
    "result": [],
    "success": true
}
```

最后，批准桥接对象。

```http
POST /rest/v1/customobjects/schema/enrollment/approve.json
```

```json
{
    "requestId": "9a76#16897b0e84b",
    "result": [],
    "success": true
}
```

使用[同步自定义对象](#create_and_update)或[批量自定义对象导入](https://experienceleague.adobe.com/docs/marketo-developer/marketo/rest/bulk-import/bulk-custom-object-import.html?lang=en)，以编程方式填充自定义对象记录。 或者，在Marketo UI中使用[导入自定义对象数据](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/administration/marketo-custom-objects/import-custom-object-data)。

## 更新字段

使用[更新自定义对象类型字段](https://developer.adobe.com/marketo-apis/api/mapi#tag/Custom-Objects/operation/updateCustomObjectTypeFieldUsingPOST)端点更新草稿自定义对象中的字段。

所需的路径参数包括：

- `apiName`：自定义对象类型的API名称。
- `fieldAPIName`：自定义对象类型字段的API名称。

请求正文包含一个JSON对象，该对象具有指定要更新的字段属性的键/值对。

```http
POST /rest/v1/customobjects/schema/{apiName}/{fieldApiName}/updateField.json
```

```json
{
  "displayName": "Very Long Title",
  "dataType": "text"
}
```

```json
{
    "requestId": "d523#1684f355db9",
    "result": [],
    "success": true
}
```

## 删除字段

使用[删除自定义对象类型字段](https://developer.adobe.com/marketo-apis/api/mapi#tag/Custom-Objects/operation/deleteCustomObjectTypeFieldsUsingPOST)端点从自定义对象中删除一个或多个字段。 所需的`apiName`路径参数指定自定义对象类型的API名称。

请求正文包含一个JSON对象，该对象具有一个或多个元素的`input`数组。 每个元素都是一个JSON对象，其`name`属性指定要删除的字段的API名称。

```http
POST /rest/v1/customobjects/schema/{apiName}/deleteField.json
```

```json
{
    "input":
    [
        {
            "name": "title"
        },
        {
            "name": "author"
        }
    ]
}
```

```json
{
"requestId": "b359#19934f17996",
"result": [],
"success": true
}
```

## 列表字段数据类型

[获取自定义对象类型字段数据类型](https://developer.adobe.com/marketo-apis/api/mapi#tag/Custom-Objects/operation/getCustomObjectTypeFieldDataTypesUsingGET)终结点返回所有允许的字段数据类型。 使用此端点可标识在为自定义对象类型建模时可用的自定义字段数据类型。

```http
GET /rest/v1/customobjects/schema/fieldDataTypes.json
```

```json
{
    "requestId": "c405#167ed49e866",
    "result": [
        "string",
        "boolean",
        "integer",
        "float",
        "link",
        "email",
        "currency",
        "date",
        "datetime",
        "phone",
        "text"
    ],
    "success": true
}
```

## 列出可链接自定义对象

[获取自定义对象可链接对象](https://developer.adobe.com/marketo-apis/api/mapi#tag/Custom-Objects/operation/getCustomObjectTypeLinkableObjectsUsingGET)端点返回所有允许的链接对象及其链接字段。 响应包括“标准对象”（如“潜在客户”和“公司”）以及在实例中创建的任何自定义对象。

```http
GET /rest/v1/customobjects/schema/linkableObjects.json
```

```json
{
    "requestId": "11e62#167f1160e4e",
    "result": [
        {
            "name": "lead",
            "displayName": "Lead",
            "fields": [
                {
                    "name": "Account Balance",
                    "displayName": "Account Balance",
                    "dataType": "integer"
                },
                {
                    "name": "Email Address",
                    "displayName": "Email Address",
                    "dataType": "email"
                },
                {
                    "name": "Id",
                    "displayName": "Id",
                    "dataType": "integer"
                },
                {
                    "name": "Marketo Social Facebook Display Name",
                    "displayName": "Marketo Social Facebook Display Name",
                    "dataType": "string"
                },
                {
                    "name": "Marketo Social Facebook Id",
                    "displayName": "Marketo Social Facebook Id",
                    "dataType": "string"
                },
                {
                    "name": "Marketo Social Facebook Photo URL",
                    "displayName": "Marketo Social Facebook Photo URL",
                    "dataType": "string"
                },
                {
                    "name": "Marketo Social Facebook Profile URL",
                    "displayName": "Marketo Social Facebook Profile URL",
                    "dataType": "string"
                },
                {
                    "name": "Marketo Social Facebook Reach",
                    "displayName": "Marketo Social Facebook Reach",
                    "dataType": "integer"
                },
                {
                    "name": "Marketo Social Facebook Referred Enrollments",
                    "displayName": "Marketo Social Facebook Referred Enrollments",
                    "dataType": "integer"
                },
                {
                    "name": "Marketo Social Facebook Referred Visits",
                    "displayName": "Marketo Social Facebook Referred Visits",
                    "dataType": "integer"
                },
                {
                    "name": "Marketo Social Gender",
                    "displayName": "Marketo Social Gender",
                    "dataType": "string"
                },
                {
                    "name": "Marketo Social LinkedIn Display Name",
                    "displayName": "Marketo Social LinkedIn Display Name",
                    "dataType": "string"
                },
                {
                    "name": "Marketo Social LinkedIn Id",
                    "displayName": "Marketo Social LinkedIn Id",
                    "dataType": "string"
                },
                {
                    "name": "Marketo Social LinkedIn Photo URL",
                    "displayName": "Marketo Social LinkedIn Photo URL",
                    "dataType": "string"
                },
                {
                    "name": "Marketo Social LinkedIn Profile URL",
                    "displayName": "Marketo Social LinkedIn Profile URL",
                    "dataType": "string"
                },
                {
                    "name": "Marketo Social LinkedIn Reach",
                    "displayName": "Marketo Social LinkedIn Reach",
                    "dataType": "integer"
                },
                {
                    "name": "Marketo Social LinkedIn Referred Enrollments",
                    "displayName": "Marketo Social LinkedIn Referred Enrollments",
                    "dataType": "integer"
                },
                {
                    "name": "Marketo Social LinkedIn Referred Visits",
                    "displayName": "Marketo Social LinkedIn Referred Visits",
                    "dataType": "integer"
                },
                {
                    "name": "Marketo Social Syndication Id",
                    "displayName": "Marketo Social Syndication Id",
                    "dataType": "string"
                },
                {
                    "name": "Marketo Social Total Referred Enrollments",
                    "displayName": "Marketo Social Total Referred Enrollments",
                    "dataType": "integer"
                },
                {
                    "name": "Marketo Social Total Referred Visits",
                    "displayName": "Marketo Social Total Referred Visits",
                    "dataType": "integer"
                },
                {
                    "name": "Marketo Social Twitter Display Name",
                    "displayName": "Marketo Social Twitter Display Name",
                    "dataType": "string"
                },
                {
                    "name": "Marketo Social Twitter Id",
                    "displayName": "Marketo Social Twitter Id",
                    "dataType": "string"
                },
                {
                    "name": "Marketo Social Twitter Photo URL",
                    "displayName": "Marketo Social Twitter Photo URL",
                    "dataType": "string"
                },
                {
                    "name": "Marketo Social Twitter Profile URL",
                    "displayName": "Marketo Social Twitter Profile URL",
                    "dataType": "string"
                },
                {
                    "name": "Marketo Social Twitter Reach",
                    "displayName": "Marketo Social Twitter Reach",
                    "dataType": "integer"
                },
                {
                    "name": "Marketo Social Twitter Referred Enrollments",
                    "displayName": "Marketo Social Twitter Referred Enrollments",
                    "dataType": "integer"
                },
                {
                    "name": "Marketo Social Twitter Referred Visits",
                    "displayName": "Marketo Social Twitter Referred Visits",
                    "dataType": "integer"
                }
            ]
        },
        {
            "name": "company",
            "displayName": "Company",
            "fields": [
                {
                    "name": "Id",
                    "displayName": "Id",
                    "dataType": "integer"
                }
            ]
        },
        {
            "name": "car_c",
            "displayName": "Car",
            "fields": [
                {
                    "name": "marketoGUID",
                    "displayName": "Marketo GUID",
                    "dataType": "string"
                },
                {
                    "name": "vin",
                    "displayName": "VIN",
                    "dataType": "string"
                }
            ]
        }
    ],
    "success": true
}
```

## 获取依赖于自定义对象的Assets

[Get Custom Object Dependent Assets](https://developer.adobe.com/marketo-apis/api/mapi#tag/Custom-Objects/operation/getCustomObjectTypeDependentAssetsUsingGET)端点返回自定义对象类型的依赖资源及其在实例中的位置。 在删除集成时，使用该插件可识别正在使用自定义对象类型的任意位置。

```http
GET /rest/v1/customobjects/schema/{apiName}/dependentAssets.json
```

```json
{
    "requestId": "71cf#16a21f30ed6",
    "result": [
        {
            "assetType": "Smart Campaign",
            "assetId": 3773,
            "assetName": "CarTest.HasCar (Smart List)"
        },
        {
            "assetType": "Smart Campaign",
            "assetId": 3773,
            "assetName": "CarTest.HasCar (Smart List)",
            "usedFields": [
                "leadID",
                "make",
                "model",
                "vin",
                "year"
            ]
        }
    ],
    "success": true
}
```

## 超时

- 除非另有说明，否则自定义对象端点的超时为30秒。
- 同步自定义对象的超时为120秒。
- 删除自定义对象的超时为60秒。
