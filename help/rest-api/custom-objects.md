---
title: 自定义对象
feature: REST API, Custom Objects
description: 创建和处理自定义Marketo对象。
source-git-commit: f1c9c5ba07013c2fdccb9f799b5c0077eb789a4b
workflow-type: tm+mt
source-wordcount: '2910'
ht-degree: 0%

---


# 自定义对象

[**自定义对象终结点引用**](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Custom-Objects) Marketo允许用户定义与Marketo标准对象（潜在客户、公司）或其他Marketo自定义对象相关的Marketo自定义对象。  Marketo自定义对象可以使用[此处](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/administration/marketo-custom-objects/create-marketo-custom-objects)所述的Marketo UI创建，也可以使用如下所述的自定义对象元数据API创建。

访问自定义对象元数据API需要适当的Marketo订阅类型。  有关详细信息，请咨询您的CSM。

## 列表

除了可用于潜在客户数据库对象的标准描述、查询、更新和删除调用之外，自定义对象还有[列表调用](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Custom-Objects/operation/getCustomObjectsUsingGET)可用。  调用此端点将返回一个响应，其中包含目标实例中可用的自定义对象列表，以及有关这些对象的其他元数据。

```
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

响应将给出每个对象上存在的关系的列表。  关系将具有`field`成员，指示保存链接值的对象上的字段；一个`type`成员，指示关系是到父类型对象还是子类型对象；一个`relatedTo`对象，指示相关对象的名称，以及该对象上的链接字段。

## 描述

自定义对象的[describe调用](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Custom-Objects/operation/describeUsingGET_1)遵循与Opportunities和Companies相同的模式，在响应中添加`relationships`数组并在URI中添加`apiName`路径参数，该参数采用要描述的自定义对象类型的API名称。  与列表调用类似，这将列出可用于此自定义对象类型的所有关系。

```
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

[查询自定义对象](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Custom-Objects/operation/getCustomObjectsUsingGET)与其他Lead Database API略有不同，它采用`apiName`路径参数，如描述。  对于普通的filterType参数，查询是简单的GET，例如对其他类型记录的查询，并且需要`filterType`和`filterValues`。  它将选择性地接受`**fields**`、`batchSize`和`nextPageToken`参数。  在请求字段列表时，如果请求的是特定字段但未返回，则该值默认为空。

```
GET /rest/v1/customobjects/{apiName}.json?filterType=idField&filterValues=dff23271-f996-47d7-984f-f2676861b5fa,dff23271-f996-47d7-984f-f2676861b5fb
```

```
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

或者，在使用复合键进行查询时，API的行为类似于Opportunity Roles API，接受具有JSON主体的POST。  JSON正文可以具有与GET查询相同的成员，但`filterValues`除外。  有一个`input`数组而不是筛选器值，该数组采用包含为对象类型的每个`dedupeFields`命名成员的对象。

```
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

使用[同步自定义对象](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Custom-Objects/operation/syncCustomObjectsUsingPOST)端点创建或更新自定义对象，您可以使用`action`参数指定操作。  在一次调用中最多可以创建或更新300条记录。  `input`数组中使用的值主要基于[Describe Custom Objects](https://experienceleague.adobe.com/en/docs/marketo-developer/marketo/rest/endpoint-reference#!/Custom_Objects/describeUsingGET_1)端点返回的信息。 在示例car对象中，只有一个重复数据删除字段`vin`。  要在使用dedupeFields模式时更新或创建记录，输入数组中的每条记录需要至少包含一个`vin`字段。

```
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

通过`idField`模式执行更新时，`idField`将始终为`marketoGUID`，因此每个记录将始终需要`marketoGUID`字段。  请记住，`idField`仅对updateOnly操作类型有效，因为此字段始终由系统管理。  您的响应将包括结果数组中每个单独记录的&#x200B;**状态**，以及一个`marketoGUID`或`reasons`数组，具体取决于单个记录的操作是否成功。

## Delete

[删除记录](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Custom-Objects/operation/deleteCustomObjectsUsingPOST)非常简单。  只需选择您的`deleteBy`模式（`idField`或`dedupeFields`），并在`input`数组中的每个记录中包含相应的字段。 每个调用最多允许300条记录。

```
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

与更新一样，您的结果将包含每个记录的状态，以及`marketoGUID`或`reasons`数组，具体取决于删除是否成功。

## 自定义对象类型

自定义对象元数据API允许您远程管理自定义对象架构。  该API允许您创建新的自定义对象类型或修改现有对象类型。  创建或修改自定义对象类型后，必须批准其使用。  有关自定义对象的详细信息，请参阅产品文档[此处](https://experienceleague.adobe.com/zh-hans/docs/marketo/using/home)。

* 无法使用Marketo UI修改由API创建的自定义对象类型
* 允许的最大自定义对象类型数为10
* 每种类型允许的最大自定义对象字段数为50
* 自定义对象类型API名称和显示名称可以包含字母数字字符和下划线“_”

### 查询类型

检索自定义对象类型元数据的方式有两种：描述自定义对象类型，它会返回  单个自定义对象类型的记录，可按批准状态筛选，以及列表自定义对象类型，该列表返回预订中所有自定义对象类型的列表，并可按名称和批准状态筛选。

### 描述类型

[Describe自定义对象类型](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Custom-Objects/operation/describeUsingGET_1)终结点返回单个自定义对象类型的元数据。 所需的`apiName`路径参数是所描述的自定义对象类型的API名称。  如果存在批准的版本，则会返回该版本。  否则，将返回草稿版本。  可选的`state`参数用于指定要返回的版本： `draft`、`approved`或`approvedWithDraft`。

```
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

在这里，我们可以看到以下属性：

* 元数据：state、displayName、description、apiName、idField、createdAt、updatedAt、dedupeFields、searchableFields、relationships
* 标准字段： marketoGUID、createdAt、updatedAt
* 自定义字段leadId、vin、make、  model， year

### 列表类型

[列出自定义对象类型](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Custom-Objects/operation/listCustomObjectTypesUsingGET)终结点返回目标实例中所有可用自定义对象类型的元数据。  请注意，此端点类似于[列表自定义对象](https://experienceleague.adobe.com/docs/marketo-developer/marketo/soap/custom-objects/custom-objects.html?lang=en)，但更全面，并且包括其他元数据，如状态、关系和字段。 如果存在批准的版本，则会返回该版本。  否则，将返回草稿版本。  可选的&#x200B;**state**&#x200B;参数用于指定要返回的自定义对象类型的版本： **草稿**、**已批准**&#x200B;或&#x200B;**approvedWithDraft**。  可选的&#x200B;**names**&#x200B;参数用于指定要返回的特定自定义对象类型名称；该参数的结构为API名称列表（以逗号分隔）。

```
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
            "description": "No really, it's a car!",
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

[同步自定义对象类型](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Custom-Objects/operation/syncCustomObjectsUsingPOST)终结点用于创建或更新自定义对象类型。  要执行的记录操作由可选的&#x200B;**action**&#x200B;属性控制，该属性可以是&#x200B;**createOnly**、**createOrUpdate**&#x200B;或&#x200B;**updateOnly**。  默认设置为createOrUpdate。 需要&#x200B;**displayName**&#x200B;和&#x200B;**apiName**&#x200B;属性，使用updateOnly作为操作时除外。   这两个参数必须唯一，以避免与客户配置的类型发生冲突。  如果您是LaunchPoint合作伙伴，则应该在这些名称的前面添加一个代表性命名空间。  对于apiName，约定是使用小写或驼峰式大小写来帮助区分其他文本字符串。 可选的&#x200B;**pluralName**&#x200B;属性指定displayName的复数形式。  可选的&#x200B;**description**&#x200B;属性用于描述自定义对象类型。  可选的&#x200B;**showInLeadDetail**&#x200B;布尔属性用于在Marketo UI的“潜在客户数据库”页中查看自定义对象数据。  默认设置为false。

命名自定义对象时要小心。 创建新自定义对象时，建议在名称前面添加一个字符串，以指示您的公司名称（允许使用字母数字或下划线）。 这使得自定义对象可以在MLM UI中轻松搜索，并且还有助于确定名称是唯一的。

以下是使用API创建新的自定义对象类型的示例  命名为“transaction”。

```
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

下面是描述新创建类型的后续调用。

```
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

在这里，我们可以看到以下与自定义对象相关的数据：

* 元数据：state、displayName、description、apiName、idField、createdAt、updatedAt、dedupeFields、searchableFields、relationships
* 标准字段： marketoGUID、createdAt、updatedAt

#### 更新类型

以下示例更新了API名称为“transaction”的现有类型的描述。  **apiName**&#x200B;属性是必需的。  在此，我们将假定该类型已存在，并对可选的&#x200B;**action**&#x200B;属性使用updateOnly。  除了&#x200B;**apiName**&#x200B;之外，还可以更新可用于创建的属性。

```
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

自定义对象类型必须先获得批准，然后才能使用。 使用[同步自定义对象类型](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Custom-Objects/operation/syncCustomObjectTypeUsingPOST)端点创建新的自定义对象类型时，该类型会创建为草稿版本。 添加完自定义字段后，必须批准草稿版本。 这将创建一个批准的版本并删除草稿版本。 当使用同步自定义对象类型端点或者使用添加/更新/删除自定义对象类型字段端点修改现有自定义对象类型时，将创建草稿版本。 对类型或其字段的所有修改仅影响草稿版本。 完成修改后，必须批准草稿版本。 这会将批准的版本替换为草稿版本，并删除草稿版本。 有关自定义对象审批的详细信息，请参阅产品文档[此处](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/administration/marketo-custom-objects/approve-a-custom-object)。

自定义对象类型获得批准后，您无法：

* 更新`displayName`或`apiName`
* 添加或删除链接字段
* 添加或删除重复数据删除字段

出于这些原因，请务必仔细考虑您计划使用的架构和命名约定。

### 批准类型

使用[批准自定义对象类型](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Custom-Objects/operation/approveCustomObjectTypeUsingPOST)端点将草稿版本作为新批准的版本发布。  **apiName**&#x200B;是唯一作为path参数所需的参数。  除非某个类型处于草稿状态，并且满足[此处](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/administration/marketo-custom-objects/approve-a-custom-object)描述的验证规则集，否则无法批准该类型。

```
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

使用[放弃自定义对象类型草稿](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Custom-Objects/operation/discardCustomObjectTypeUsingPOST)端点删除草稿版本。 `apiName`是作为路径参数唯一必需的参数。 要放弃的类型必须处于草稿状态，即不能放弃已批准的类型。

```
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

使用[删除自定义对象类型](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Custom-Objects/operation/deleteCustomObjectsUsingPOST)端点删除批准的版本。  `apiName`是作为路径参数唯一必需的参数。  请注意，这是一个破坏性操作；无法撤消此操作。  无法删除类型，除非已从任何资源（如触发器或过滤器）的使用中删除该类型。  获取依赖自定义对象的Assets端点可用于检索给定类型的依赖资源列表。

POST/rest/v1/customobjects/schema/{apiName}/delete.json

```json
{
    "requestId": "14e36#1684efc4227",
    "result": [],
    "success": true
}
```

## 自定义对象字段

默认情况下，所有自定义对象类型都包含以下标准字段：

* Marketo GUID — 自定义对象类型的唯一标识符
* 创建时间 — 创建自定义对象类型的日期时间
* 更新时间 — 上次更新自定义对象类型的日期时间

您可以随意使用下述端点添加/更改/删除自定义字段。

* 允许的最大字段数为50
* 批准自定义对象后，您最多可以向该自定义对象添加20个附加字段
* 至少需要1个重复数据删除字段，最多允许3个
* 字段API名称和显示名称可以包含字母数字字符和下划线“_”

有关自定义对象字段的详细信息，请参阅产品文档[此处](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/administration/marketo-custom-objects/add-marketo-custom-object-fields)。

### 添加字段

[添加自定义对象类型字段](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Custom-Objects/operation/addCustomObjectTypeFieldsUsingPOST)端点允许您向自定义对象添加一个或多个字段。  请求正文包含具有一个或多个元素的`input`数组。  每个元素都是一个JSON对象，其属性用于描述字段。 必需的`name`属性是字段的API名称，并且必须是自定义对象的唯一属性。   使用小写或驼峰式大小写有助于区分其他文本字符串。 必需的`displayName`属性是易于用户识别的字段名称，并且该属性对于自定义对象必须是唯一的。 必需的`dataType`属性是字段的数据类型。  A  通过调用[获取自定义对象类型字段数据类型](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Custom-Objects/operation/getCustomObjectTypeFieldDataTypesUsingGET)终结点，可以获得允许的数据类型列表。  自定义对象可以包含数据类型为“link”的字段。  链接字段用于在自定义对象与系统中的其他对象类型（如潜在客户、公司）之间建立关系。  可在[此处](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/administration/marketo-custom-objects/add-marketo-custom-object-fields)找到有关链接字段的更多信息。 可选的`description`属性是字段的说明。 可选的`isDedupeField`布尔属性指定在自定义对象更新操作期间是否将该字段用于去重。  默认设置为false。  对于一对多关系，需要重复数据删除字段。 可选的`relatedTo`对象属性指定了链接字段。  对于一对多关系，此对象包含`name`属性（要链接到的链接对象或父对象）和`field`属性（链接字段）。  或父对象中要用作键属性的字段。  调用[获取自定义对象可链接对象](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Custom-Objects/operation/getCustomObjectTypeLinkableObjectsUsingGET)端点以检索允许链接对象的列表。  有关链接字段的详细信息，请参阅产品文档[此处](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/administration/marketo-custom-objects/add-marketo-custom-object-fields)。 自定义对象无法链接到具有现有链接字段的其他自定义对象。

### 一对多关系

对于一对多自定义对象结构，请使用自定义对象中的链接字段将其连接到标准对象：潜在客户或公司。 使用Marketo产品文档[此处](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/administration/marketo-custom-objects/add-marketo-custom-object-link-fields#AddMarketoCustomObjectLinkFields-CreateaLinkFieldforaOne-to-ManyStructure)中的车主示例，我们创建包含要与潜在客户连接的车相关信息的自定义对象。

1. 创建&#x200B;**Car**&#x200B;对象
1. 向&#x200B;**Car**&#x200B;对象添加字段：**VIN**&#x200B;上的重复数据删除，链接到&#x200B;**潜在客户****/潜在客户ID**
1. 批准&#x200B;**Car**&#x200B;对象

首先，创建自定义对象类型以包含特定于car的信息。

```
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

现在，向Car自定义对象类型添加字段。 我们使用链接字段指定要连接的对象和字段。 在这种情况下，链接对象是Lead，链接字段是ID。 使用字符串字段删除重复项(VIN)。  我们将再添加三个字段来存储其他汽车属性（品牌、型号、年份）。

```
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

```
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

多对多关系在标准自定义对象（如Lead或Company）和“边缘”自定义对象之间使用“桥”或中间、自定义对象表示。 Edge对象是包含描述性属性（字段）的主要实体。 桥接对象包含使用2个链接字段解析对象关系的数据。  一个链接字段指向父标准对象，就像在  一对多关系配置。  另一个链接字段指向边缘对象，该对象是没有链接的自定义对象。  桥接对象还可以包含描述性属性（字段）。 使用Marketo产品文档[此处](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/administration/marketo-custom-objects/add-marketo-custom-object-link-fields#AddMarketoCustomObjectLinkFields-CreateaLinkFieldforaOne-to-ManyStructure)中的大学课程注册示例，我们创建了一个用于包含课程相关信息的边缘自定义对象，以及一个用于将课程与潜在客户连接的注册桥接对象。 步骤如下：

1. 创建&#x200B;**课程**&#x200B;边缘对象
1. 将字段添加到&#x200B;**课程：**&#x200B;课程ID **上的重复数据删除**
1. 批准&#x200B;**课程**
1. 创建&#x200B;**注册**&#x200B;桥接对象
1. 将字段添加到&#x200B;**注册：**&#x200B;注册ID **上的重复数据删除**，链接到&#x200B;**课程****/课程ID**字段，以及链接到&#x200B;**潜在客户****/潜在客户ID**
1. 批准&#x200B;**注册**

首先，创建Edge对象类型以包含特定于课程的信息：

```
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

接下来，我们将自定义字段添加到边缘对象类型。  在此示例中，我们将添加以下四个自定义字段来模拟大学课程：课程ID、课程讲师、课程位置、课程名称。  请注意，我们将指定课程ID作为重复数据消除字段，因为至少需要一个重复数据消除字段。

```
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

```
{
    "requestId": "cc36#16895b82a41",
    "result": [],
    "success": true
}
```

现在，我们需要批准Edge对象类型，以便我们以后在链接到桥接对象类型时可以引用它。  请注意，自定义对象类型必须获得批准才能选择为链接对象。

```
POST /rest/v1/customobjects/schema/course/approve.json
```

```json
{
    "requestId": "460b#16896055fa3",
    "result": [],
    "success": true
}
```

边缘对象已完成。  现在，让我们继续创建桥接对象类型，以包含特定于注册的信息。

```
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

要将自定义字段添加到桥接对象类型，请添加两个链接字段：一个链接到Lead对象，另一个链接到我们刚刚创建的Course对象。 要链接到Lead对象，请使用Lead Id字段。 要链接到课程对象，请使用“课程ID”字段。  接下来，添加注册ID唯一标识符作为重复数据删除字段，因为至少需要一个重复数据删除字段。 最后，添加一个Grade字段以跟踪该学生的表现。

```
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

```
POST /rest/v1/customobjects/schema/enrollment/approve.json
```

```json
{
    "requestId": "9a76#16897b0e84b",
    "result": [],
    "success": true
}
```

您可以使用[同步自定义对象](#create_and_update)或[批量自定义对象导入](https://experienceleague.adobe.com/docs/marketo-developer/marketo/rest/bulk-import/bulk-custom-object-import.html?lang=en)以编程方式填充自定义对象记录。 或者，您可以使用Marketo用户界面功能[导入自定义对象数据](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/administration/marketo-custom-objects/import-custom-object-data)。

## 更新字段

[更新自定义对象类型字段](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Custom-Objects/operation/updateCustomObjectTypeFieldUsingPOST)端点允许您更新草稿自定义对象中的字段。  所需的路径参数`apiName`是自定义对象类型的API名称。  必需的路径参数`fieldAPIName`是自定义对象类型字段的API名称。  请求正文包含一个JSON对象，该对象包含指定要更新的字段属性的键/值对。

```
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

[删除自定义对象类型字段](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Custom-Objects/operation/deleteCustomObjectTypeFieldsUsingPOST)端点允许您从自定义对象中删除一个或多个字段。  所需的路径参数`apiName`是自定义对象类型的API名称。  请求正文包含的JSON对象具有带一个或多个元素的`input`数组。  每个元素都是一个具有`name`属性的JSON对象，该属性指定要删除的字段的API名称。

```
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

[获取自定义对象类型字段数据类型](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Custom-Objects/operation/getCustomObjectTypeFieldDataTypesUsingGET)终结点返回所有允许的字段数据类型的列表。 在为自定义对象类型建模以标识支持的自定义字段数据类型时，这非常有用。

```
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

[获取自定义对象可链接对象](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Custom-Objects/operation/getCustomObjectTypeLinkableObjectsUsingGET)端点返回所有允许的链接对象及其链接字段的列表。  该列表将包含标准对象（潜在客户、公司）以及在实例中创建的任何自定义对象。

```
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

[获取依赖自定义对象的Assets](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Custom-Objects/operation/getCustomObjectTypeDependentAssetsUsingGET)端点返回自定义对象类型的依赖资源列表，包括其实例内位置。  这在删除集成时很有用，并且您需要标识正在使用自定义对象类型的每个位置。

```
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

* 除非下面说明，否则自定义对象端点的超时为30秒
   * 同步自定义对象：120秒 
   * 删除自定义对象：60秒
