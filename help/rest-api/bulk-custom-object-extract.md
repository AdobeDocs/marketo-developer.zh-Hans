---
title: 批量自定义对象提取
feature: REST API, Custom Objects
description: 批量处理自定义Marketo对象。
exl-id: 86cf02b0-90a3-4ec6-8abd-b4423cdd94eb
source-git-commit: 3649db037a95cfd20ff0a2c3d81a3b40d0095c39
workflow-type: tm+mt
source-wordcount: '1298'
ht-degree: 1%

---

# 批量自定义对象提取

[批量自定义对象提取终结点引用](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Bulk-Export-Custom-Objects)

REST API的批量自定义对象提取集提供了一个编程接口，用于从Marketo中检索大量自定义对象记录。 对于需要在Marketo与一个或多个外部系统之间持续交换数据的用例，这是推荐的界面，用于ETL、数据仓库存储和存档目的。

此API支持导出直接链接到潜在客户的第一级Marketo自定义对象记录。 传入自定义对象的名称和该对象链接到的潜在客户列表。 对于列表中的每个潜在客户，与指定的自定义对象名称匹配的链接自定义对象记录将作为行写入导出文件中。 可在Marketo UI[中商机的详细信息页面的](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/administration/marketo-custom-objects/understanding-marketo-custom-objects)自定义对象选项卡中查看自定义对象数据。

## 权限

批量自定义对象提取API要求API用户具有一个角色，该角色具有“只读自定义对象”或“读写自定义对象”权限中的一个或两个。

## 过滤器

自定义对象提取支持多个过滤器选项，这些选项用于指定链接到自定义对象的潜在客户列表。 如果列表中的商机链接到与给定的自定义对象名称匹配的自定义对象记录，则记录将写入导出文件。 每个导出作业只能指定一个过滤器类型。

| 筛选器类型 | 数据类型 | 注释 |
|---|---|---|
| `updatedAt` | Date Range | 接受具有成员`startAt`和`endAt` &amp;amp；nbsp的JSON对象。；`startAt`接受表示低水位线的日期时间，`endAt`接受表示高水位线的日期时间。 范围必须为31天或更少。 具有此筛选器类型的作业将返回在日期范围内更新的所有可访问记录。 日期时间应采用ISO-8601格式，不带毫秒。 |
| `staticListName` | 字符串 | 接受静态列表的名称。 具有此筛选器类型的作业将返回所有可访问的记录，这些记录是作业开始处理时静态列表的成员。 使用“获取列表”端点检索静态列表名称。 |
| `staticListId` | 整数 | 接受静态列表的id。 具有此筛选器类型的作业将返回所有可访问的记录，这些记录是作业开始处理时静态列表的成员。 使用“获取列表”端点检索静态列表ID。 |
| `smartListName`* | 字符串 | 接受智能列表的名称。 具有此筛选器类型的作业将返回在作业开始处理时作为智能列表成员的所有可访问记录。 使用获取智能列表端点检索智能列表名称。 |
| `smartListId`* | 整数 | 接受智能列表的id。 具有此筛选器类型的作业将返回在作业开始处理时作为智能列表成员的所有可访问记录。 使用获取智能列表端点检索智能列表ID。 |

过滤器类型不适用于某些订阅。 如果您的订阅不可用，则在调用“创建导出潜在客户作业”终结点（“1035，目标订阅不受支持的过滤器类型”）时会收到一个错误。 客户可以联系Marketo支持部门以在其订阅中启用此功能。

## 选项

[创建导出自定义对象作业](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Bulk-Export-Custom-Objects/operation/createExportCustomObjectsUsingPOST)终结点提供了多个格式选项。 利用这些选项，用户能够：

- 指定要包含在导出文件中的字段
- 重命名这些字段的列标题
- 指定导出文件的格式

| 参数 | 数据类型 | 必需 | 注释 |
|---|---|---|---|
| `fields` | 数组[字符串] | 是 | 字符串数组，其中包含由描述自定义对象端点返回的自定义对象属性名称值。 列出的字段包含在导出的文件中。 |
| `columnHeaderNames` | 对象 | 否 | 包含字段和列标题名称的键值对的JSON对象。 键必须是导出作业中包含的字段的名称。 值是该字段的导出列标题的名称。 |
| `format` | 字符串 | 否 | 接受以下内容之一：CSV、TSV、SSV。 如果设置，导出的文件将分别呈现为逗号分隔的值、制表符分隔的值或空格分隔的值文件。 如果未设置，则默认为CSV。 |

## 创建作业

作业的参数是在使用[创建导出自定义对象作业](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Bulk-Export-Custom-Objects/operation/createExportCustomObjectsUsingPOST)终结点开始导出之前定义的。

所需的`apiName`路径参数是[描述自定义对象](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Custom-Objects/operation/describeUsingGET_1)终结点返回的自定义对象名称。 这会指定要导出的Marketo自定义对象。 不允许CRM自定义对象。 所需的`filter`参数包含链接到自定义对象的潜在客户列表。 这可以引用静态列表或智能列表。 所需的`fields`参数包含要包含在导出文件中的自定义对象属性的API名称。 或者，我们可以定义文件的`format`和`columnHeaderNames`。

例如，假设我们创建了一个名为“Car”的自定义对象，该对象包含以下字段：Color、Make、Model、VIN。 链接字段是商机ID，重复数据删除字段是VIN。

自定义对象定义

![自定义对象](assets/custom-object-car.png)

自定义对象字段

![自定义对象字段](assets/custom-object-car-fields.png)

我们可以调用[Describe Custom Object](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Custom-Objects/operation/describeUsingGET_1)以编程方式检查响应中`fields`属性中显示的自定义对象属性。

```
GET /rest/v1/customobjects/car_c/describe.json
```

```json
{
    "requestId": "148ef#1793e00f64f",
    "result": [
        {
            "name": "car_c",
            "displayName": "Car",
            "description": "It's a car.",
            "createdAt": "2021-05-05T16:14:41Z",
            "updatedAt": "2021-05-05T16:14:42Z",
            "idField": "marketoGUID",
            "dedupeFields": [
                "vIN"
            ],
            "searchableFields": [
                [
                    "vIN"
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
                        "field": "Id"
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
                    "name": "color",
                    "displayName": "Color",
                    "dataType": "string",
                    "length": 255,
                    "updateable": true,
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
                    "name": "vIN",
                    "displayName": "VIN",
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

创建多个自定义对象记录，并使用[同步自定义对象](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Custom-Objects/operation/syncCustomObjectsUsingPOST)端点将每个记录链接到不同的潜在客户。 一个商机可以链接到许多自定义对象记录。 这就是所谓的“一对多”关系。

```
POST /rest/v1/customobjects/car_c.json
```

```json
{
   "action":"createOrUpdate",
   "input":[
       {
           "leadId": 11,
           "color": "Pearl White",
           "make": "Tesla",
           "model": "Model S",
           "vIN": "5YJSA1E41FF156789"
       },
       {
           "leadId": 12,
           "color": "Midnight Silver Metallic",
           "make": "Tesla",
           "model": "Model X",
           "vIN": "LRWXB2B41FF198765"
       },
       {
           "leadId": 13,
           "color": "Fusion Red",
           "make": "Tesla",
           "model": "Roadster",
           "vIN": "SFGRC3C41FF154321"
       }
    ]
}
```

```json
{
    "requestId": "50d9#1793e066088",
    "result": [
        {
            "seq": 0,
            "marketoGUID": "d911eaa1-fd0b-4a99-9b71-c6a7233c782c",
            "status": "created"
        },
        {
            "seq": 1,
            "marketoGUID": "20d04ffb-51f0-4336-924c-c783b9bb4215",
            "status": "created"
        },
        {
            "seq": 2,
            "marketoGUID": "e7da4331-8e7a-473b-85c8-047638eb6c7f",
            "status": "created"
        }
    ],
    "success": true
}
```

上面引用的三个潜在客户都属于名为“购车者”的静态列表，其`id`为1081，如下面通过调用[按列表ID获取潜在客户](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Static-Lists/operation/getLeadsByListIdUsingGET_1)端点可以看出。

```
GET /rest/v1/lists/1081/leads.json
```

```json
{
    "requestId": "d023#1793e1e982b",
    "result": [
        {
            "id": 11,
            "firstName": "Hanna",
            "lastName": "Crawford",
            "email": "208161Hanna.Crawford@pookmail.com",
            "updatedAt": "2020-01-16T02:38:22Z",
            "createdAt": "2017-07-27T01:38:42Z"
        },
        {
            "id": 12,
            "firstName": "Bertha",
            "lastName": "Fulton",
            "email": "208160Bertha.Fulton@trashymail.com",
            "updatedAt": "2020-01-16T02:38:22Z",
            "createdAt": "2017-07-27T01:38:42Z"
        },
        {
            "id": 13,
            "firstName": "Faith",
            "lastName": "England",
            "email": "208159Faith.England@dodgit.com",
            "updatedAt": "2020-01-16T02:38:22Z",
            "createdAt": "2017-07-27T01:38:42Z"
        }
    ],
    "success": true
}
```

现在，让我们创建一个导出作业来检索这些记录。 我们使用[创建导出自定义对象作业](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Bulk-Export-Custom-Objects/operation/createExportCustomObjectsUsingPOST)终结点，在`fields`参数中指定自定义对象属性，在`filter`参数中指定静态列表ID。

```
POST /bulk/v1/customobjects/car_c/export/create.json
```

```json
{
    "fields": [
        "leadId",
        "color",
        "make",
        "model",
        "vIN"
    ],
    "filter": {
        "staticListId": 1081
    }
}
```

```json
{
    "requestId": "8d2f#1793e289e87",
    "result": [
        {
            "exportId": "f2c03f1d-226f-47c1-a557-357af8c2b32a",
            "format": "CSV",
            "status": "Created",
            "createdAt": "2021-05-05T20:12:01Z"
        }
    ],
    "success": true
}
```

这会在响应中返回一个状态，指示作业已创建。 作业已定义和创建，但尚未开始。 为此，必须使用[并从创建状态响应中调用](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Bulk-Export-Custom-Objects/operation/enqueueExportCustomObjectsUsingPOST)排入队列导出自定义对象作业`apiName`终结点`exportId`。

```
POST /bulk/v1/customobjects/car_c/export/f2c03f1d-226f-47c1-a557-357af8c2b32a/enqueue.json
```

```json
{
    "requestId": "cfaf#1793e2a0762",
    "result": [
        {
            "exportId": "f2c03f1d-226f-47c1-a557-357af8c2b32a",
            "format": "CSV",
            "status": "Queued",
            "createdAt": "2021-05-05T20:12:01Z",
            "queuedAt": "2021-05-05T20:13:32Z"
        }
    ],
    "success": true
}
```

此响应带有初始`status`的“已排队”，当存在可用的导出槽时，将在此之后设置为“正在处理”。

## 轮询作业状态

只能检索同一API用户创建的作业的状态。

由于这是异步端点，因此创建作业后，必须轮询其状态以确定其进度。 使用[获取导出自定义对象作业状态](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Bulk-Export-Custom-Objects/operation/getExportCustomObjectsStatusUsingGET)终结点进行轮询。 状态仅每60秒更新一次，因此不建议使用低于此值的轮询频率，并且在几乎所有情况下，仍然会过度轮询。 状态字段可以使用以下任意一项进行响应：“已创建”、“已排队”、“正在处理”、“已取消”、“已完成”或“失败”。

```
GET /bulk/v1/customobjects/{apiName}/export/{exportId}/status.json
```

```json
{
    "requestId": "14daa#1793e2cf9de",
    "result": [
        {
            "exportId": "f2c03f1d-226f-47c1-a557-357af8c2b32a",
            "format": "CSV",
            "status": "Processing",
            "createdAt": "2021-05-05T20:12:01Z",
            "queuedAt": "2021-05-05T20:13:32Z",
            "startedAt": "2021-05-05T20:14:15Z"
        }
    ],
    "success": true
}
```

状态终结点响应指示作业仍在处理，因此文件尚不可检索。 作业`status`更改为“已完成”后，便可以下载。

```json
{
    "requestId": "14daa#1793e2cf9de",
    "result": [
        {
            "exportId": "f2c03f1d-226f-47c1-a557-357af8c2b32a",
            "format": "CSV",
            "status": "Completed",
            "createdAt": "2021-05-05T20:12:01Z",
            "queuedAt": "2021-05-05T20:13:32Z",
            "startedAt": "2021-05-05T20:14:15Z",
            "finishedAt": "2021-05-05T20:14:28Z",
            "numberOfRecords": 3,
            "fileSize": 182,
            "fileChecksum": "sha256:fac0cabc2352229c12e18b2fde03d1f24178bc71e9e926f520ae8d61bbe98c01"
        }
    ],
    "success": true
}
```

## 检索数据

要检索已完成的自定义对象导出的文件，只需使用[和](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Bulk-Export-Custom-Objects/operation/getExportCustomObjectsFileUsingGET)调用`apiName`获取导出自定义对象文件`exportId`终结点。

响应包含以作业配置方式格式化的文件。 端点使用文件的内容进行响应。 如果请求的自定义对象属性为空（不包含数据），则`null`将置于导出文件中的相应字段中。

```
GET /bulk/v1/customobjects/car_c/export/f2c03f1d-226f-47c1-a557-357af8c2b32a/file.json
```

```csv
leadId,color,make,model,vIN
11,Pearl White,Tesla,Model S,5YJSA1E41FF156789
12,Midnight Silver Metallic,Tesla,Model X,LRWXB2B41FF198765
13,Fusion Red,Tesla,Roadster,SFGRC3C41FF154321
```

为了支持对提取的数据进行部分检索和便于恢复检索，文件终结点可以选择性地支持类型为`Range`的HTTP标头`bytes`。 如果未设置标头，则将返回所有内容。 有关在Marketo [批量提取](bulk-extract.md)中使用Range标头的更多信息，请参阅。

## 取消作业

如果作业配置不正确或变得不必要，可以使用[取消导出自定义对象作业](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Bulk-Export-Custom-Objects/operation/getExportCustomObjectsFileUsingPOST)端点轻松取消该作业。 此响应包含`status`，指示作业已取消。

```
POST /bulk/v1/customobjects/car_c/export/f2c03f1d-226f-47c1-a557-357af8c2b32a/cancel.json
```

```json
{
    "requestId": "e5f9#179391286a7",
    "result": [
        {
            "exportId": "4a8cdd80-0d16-4dd6-9923-6ec97e30e91b",
            "format": "CSV",
            "status": "Cancelled",
            "createdAt": "2021-05-04T20:24:33Z"
        }
    ],
    "success": true
}
```
