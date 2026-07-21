---
title: 批量自定义对象提取
feature: REST API, Custom Objects
description: Marketo批量自定义对象提取REST API指南，用于导出具有更新的At和列表筛选器、选定字段等的商机链接自定义对象……
exl-id: 86cf02b0-90a3-4ec6-8abd-b4423cdd94eb
TQID: https://experienceleague.adobe.com/KAT-vab2uZq8FrRbZLy30PCJNfq01znDDuSSWuIu7WE
product_v2:
  - id: b27e5950-9033-45ac-9f86-eb22e567f615
feature_v2:
  - id: d1d0a9cd-295d-4976-8c39-ddae266f240e
role_v2:
  - id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
topic_v2:
  - id: eddd9b14-83bd-4ff4-9072-54a4a484abb7
source-git-commit: 3e6d310c5aec1a3435424fb122b71d825db5af0e
workflow-type: tm+mt
source-wordcount: 1231
ht-degree: 1%

---

# 批量自定义对象提取

[批量自定义对象提取端点引用](https://developer.adobe.com/marketo-apis/api/mapi#tag/Bulk-Export-Custom-Objects)

批量自定义对象提取REST API从Marketo中检索大量自定义对象记录。 使用这些API可在Marketo和外部系统、ETL、数据仓库存储和归档之间持续交换数据。

API可导出直接链接到潜在客户的第一级Marketo自定义对象记录。 指定自定义对象名称和链接潜在客户列表。 对于每个潜在客户，API会将匹配的链接自定义对象记录作为行写入导出文件中。

您可以在Marketo UI中商机的详细信息页面的[自定义对象选项卡中查看自定义对象数据](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/administration/marketo-custom-objects/understanding-marketo-custom-objects)。

## 权限

API用户必须具有具有“只读自定义对象”权限和/或“读写自定义对象”权限的角色。

## 过滤器

自定义对象提取筛选器指定链接到自定义对象的潜在客户列表。 如果列出的潜在客户链接到与指定的自定义对象名称匹配的记录，则API会将这些记录写入导出文件。

每个导出作业仅指定一个过滤器类型。

| 筛选器类型 | 数据类型 | 注释 |
| --- | --- | --- |
| `updatedAt` | Date Range | 接受具有成员`startAt`和`endAt` &amp;nbsp.；`startAt`的JSON对象，该对象接受表示低水位线的日期时间，并且`endAt`接受表示高水位线的日期时间。 范围必须为31天或更少。 具有此筛选器类型的作业将返回在日期范围内更新的所有可访问记录。 日期时间应采用ISO-8601格式，不带毫秒。 |
| `staticListName` | 字符串 | 接受静态列表的名称。 具有此筛选器类型的作业将返回所有可访问的记录，这些记录是作业开始处理时静态列表的成员。 使用“获取列表”端点检索静态列表名称。 |
| `staticListId` | 整数 | 接受静态列表的id。 具有此筛选器类型的作业将返回所有可访问的记录，这些记录是作业开始处理时静态列表的成员。 使用“获取列表”端点检索静态列表ID。 |
| `smartListName`* | 字符串 | 接受智能列表的名称。 具有此筛选器类型的作业将返回在作业开始处理时作为智能列表成员的所有可访问记录。 使用获取智能列表端点检索智能列表名称。 |
| `smartListId`* | 整数 | 接受智能列表的id。 具有此筛选器类型的作业将返回在作业开始处理时作为智能列表成员的所有可访问记录。 使用获取智能列表端点检索智能列表ID。 |

某些订阅不支持此筛选器类型。 如果不可用，则创建导出潜在客户作业终结点将返回`1035, Unsupported filter type for target subscription`。 联系Marketo支持以请求为您的订阅提供此功能。

## 选项

[创建导出自定义对象作业](https://developer.adobe.com/marketo-apis/api/mapi#tag/Bulk-Export-Custom-Objects/operation/createExportCustomObjectsUsingPOST)终结点提供以下选项：

- 指定要包含在导出文件中的字段。
- 重命名导出的列标题。
- 指定导出文件格式。

| 参数 | 数据类型 | 必需 | 注释 |
| --- | --- | --- | --- |
| `fields` | 数组[字符串] | 是 | 字符串数组，其中包含由描述自定义对象端点返回的自定义对象属性名称值。 列出的字段包含在导出的文件中。 |
| `columnHeaderNames` | 对象 | 否 | 包含字段和列标题名称的键值对的JSON对象。 键必须是导出作业中包含的字段的名称。 值是该字段的导出列标题的名称。 |
| `format` | 字符串 | 否 | 接受以下内容之一：CSV、TSV、SSV。 如果设置，导出的文件将分别呈现为逗号分隔的值、制表符分隔的值或空格分隔的值文件。 如果未设置，则默认为CSV。 |

## 创建作业

使用[创建导出自定义对象作业](https://developer.adobe.com/marketo-apis/api/mapi#tag/Bulk-Export-Custom-Objects/operation/createExportCustomObjectsUsingPOST)端点定义导出作业。

请求使用以下参数：

- `apiName`：必需的路径参数。 使用[Describe Custom Object](https://developer.adobe.com/marketo-apis/api/mapi#tag/Custom-Objects/operation/describeUsingGET_1)终结点返回的名称，指定要导出的Marketo自定义对象。 不允许CRM自定义对象。
- `filter`：必需。 通过引用静态列表或智能列表指定链接的潜在客户。
- `fields`：必需。 指定要包含在导出文件中的自定义对象属性的API名称。
- `format`：可选。 指定导出文件格式。
- `columnHeaderNames`：可选。 指定替换列标题名称。

此示例使用具有`Color`、`Make`、`Model`和`VIN`字段的`Car`自定义对象。 链接字段是商机ID，重复数据删除字段是VIN。

自定义对象定义

![自定义对象](assets/custom-object-car.png)

自定义对象字段

![自定义对象字段](assets/custom-object-car-fields.png)

调用[Describe自定义对象](https://developer.adobe.com/marketo-apis/api/mapi#tag/Custom-Objects/operation/describeUsingGET_1)以编程方式检查自定义对象属性。 响应返回`fields`中的属性。

```http
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

使用[同步自定义对象](https://developer.adobe.com/marketo-apis/api/mapi#tag/Custom-Objects/operation/syncCustomObjectsUsingPOST)端点创建自定义对象记录并将每个记录链接到潜在客户。 商机可以链接到多个自定义对象记录，从而创建一对多关系。

```http
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

此示例中的三个潜在客户属于`Car Buyers`静态列表，该列表的`id`为1081。 调用[按列表ID获取潜在客户](https://developer.adobe.com/marketo-apis/api/mapi#tag/Static-Lists/operation/getLeadsByListIdUsingGET_1)端点以检索列表成员。

```http
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

要检索这些记录，请调用[创建导出自定义对象作业](https://developer.adobe.com/marketo-apis/api/mapi#tag/Bulk-Export-Custom-Objects/operation/createExportCustomObjectsUsingPOST)端点。 在`fields`中指定自定义对象属性，在`filter`中指定静态列表ID。

```http
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

响应将确认作业已创建，但导出不会自动启动。 将`apiName`和返回的`exportId`传递到[排入队列导出自定义对象作业](https://developer.adobe.com/marketo-apis/api/mapi#tag/Bulk-Export-Custom-Objects/operation/enqueueExportCustomObjectsUsingPOST)终结点，以启动该作业。

```http
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

排入队列的响应最初返回`Queued`状态。 导出插槽可用时，状态将更改为`Processing`。

## 轮询作业状态

您只能检索由同一API用户创建的作业的状态。

由于导出是异步运行的，因此请使用[获取导出自定义对象作业状态](https://developer.adobe.com/marketo-apis/api/mapi#tag/Bulk-Export-Custom-Objects/operation/getExportCustomObjectsStatusUsingGET)端点轮询其进度。 状态每60秒只更新一次，因此不要更频繁地轮询。

状态可以是`Created`、`Queued`、`Processing`、`Canceled`、`Completed`或`Failed`。

```http
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

此响应显示作业仍在处理中，因此文件不可用。 当作业状态更改为`Completed`时，文件已可供下载。

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

要检索已完成的自定义对象导出，请将`apiName`和`exportId`传递到[获取导出自定义对象文件](https://developer.adobe.com/marketo-apis/api/mapi#tag/Bulk-Export-Custom-Objects/operation/getExportCustomObjectsFileUsingGET)终结点。

端点会按照为作业配置的格式返回文件。 如果请求的自定义对象属性不包含任何数据，则相应的导出字段将包含`null`。

```http
GET /bulk/v1/customobjects/car_c/export/f2c03f1d-226f-47c1-a557-357af8c2b32a/file.json
```

```csv
leadId,color,make,model,vIN
11,Pearl White,Tesla,Model S,5YJSA1E41FF156789
12,Midnight Silver Metallic,Tesla,Model X,LRWXB2B41FF198765
13,Fusion Red,Tesla,Roadster,SFGRC3C41FF154321
```

对于部分或可恢复的检索，文件终结点支持范围类型为`bytes`的可选HTTP `Range`标头。 如果不设置标头，则端点将返回整个文件。 有关详细信息，请参阅[批量提取](bulk-extract.md)。

## 取消作业

要取消配置不正确或不再需要的作业，请调用[取消导出自定义对象作业](https://developer.adobe.com/marketo-apis/api/mapi#tag/Bulk-Export-Custom-Objects/operation/getExportCustomObjectsFileUsingPOST)终结点。 响应状态表示作业已取消。

```http
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
