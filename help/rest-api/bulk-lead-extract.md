---
title: 批量潜在客户提取
feature: REST API
description: 了解如何使用Marketo批量潜在客户提取REST API批量导出具有日期、列表和智能列表筛选器、自定义字段和CSV/TSV格式的潜在客户。
exl-id: 42796e89-5468-463e-9b67-cce7e798677b
TQID: https://experienceleague.adobe.com/4eMJR87fHDdccrVid3wHtspvBVQmrBGHYMlIwFCSdEI
product_v2: id: b27e5950-9033-45ac-9f86-eb22e567f615
role_v2: id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
source-git-commit: 3e6d310c5aec1a3435424fb122b71d825db5af0e
workflow-type: tm+mt
source-wordcount: 1037
ht-degree: 2%

---

# 批量潜在客户提取

[批量潜在客户提取终结点引用](https://developer.adobe.com/marketo-apis/api/mapi#tag/Bulk-Export-Leads)

Bulk Lead Extract REST API从Marketo中检索大量商机/人员记录。 您还可以根据记录创建日期、最近更新、静态列表成员资格或智能列表成员资格增量检索潜在客户。

使用Bulk Lead Extract在Marketo和外部系统（包括ETL、数据仓库存储和归档工作流）之间连续交换数据。

## 权限

拥有作业的API用户必须具有具有“只读潜在客户”权限、“读写潜在客户”权限或同时具有这两个权限的角色。

## 过滤器

潜在客户导出作业支持多种过滤器类型。 每个导出作业只能使用一个过滤器类型。

`updatedAt`、`smartListName`和`smartListId`筛选器需要并非所有订阅都可用的基础结构。

| 筛选器类型 | 数据类型 | 注释 |
| --- | --- | --- |
| createdat | Date Range | 具有`startAt`和`endAt`成员的JSON对象。 `startAt`是低水位线日期时间，`endAt`是高水位线日期时间。 使用ISO-8601日期和时间值（不含毫秒）。 范围必须为31天或更少。 作业将返回在日期范围内创建的所有可访问记录。 |
| 更新时间* | Date Range | 具有`startAt`和`endAt`成员的JSON对象。 `startAt`是低水位线日期时间，`endAt`是高水位线日期时间。 使用ISO-8601日期和时间值（不含毫秒）。 范围必须为31天或更少。 此筛选器不使用可见的`updatedAt`字段，该字段仅反映对标准字段的更新。 相反，它会使用潜在客户记录的最近字段更新时间。 作业会返回日期范围内最近更新的所有可访问记录。 |
| staticListName | 字符串 | 静态列表的名称。 当作业开始处理时，该作业将返回作为静态列表成员的所有可访问记录。 使用“获取列表”端点检索静态列表名称。 |
| staticListId | 整数 | 静态列表的ID。 当作业开始处理时，该作业将返回作为静态列表成员的所有可访问记录。 使用获取列表端点检索静态列表ID。 |
| smartListName* | 字符串 | 智能列表的名称。 当作业开始处理时，该作业将返回作为智能列表成员的所有可访问记录。 使用获取智能列表端点检索智能列表名称。 |
| smartListId* | 整数 | 智能列表的ID。 当作业开始处理时，该作业将返回作为智能列表成员的所有可访问记录。 使用获取智能列表端点检索智能列表ID。 |

某些订阅无法使用标有星号的过滤器类型。 如果您的订阅无法使用某个过滤器类型，则创建导出潜在客户作业端点会返回错误“1035，目标订阅不支持该过滤器类型”。 请联系Marketo支持，为您的订阅启用此功能。

## 选项

“创建导出潜在客户作业”端点提供了选择导出的字段、重命名列标题和设置文件格式的选项。

| 参数 | 数据类型 | 必需 | 注释 |
| --- | --- | --- | --- |
| 字段 | 数组[字符串] | 是 | JSON字符串数组。 每个字符串必须是Marketo潜在客户字段的REST API名称。 导出包括每个列出的字段，并使用其REST API名称作为列标题，除非`columnHeaderNames`覆盖它。 启用[!DNL Adobe Experience Cloud Audience Sharing]功能后，Cookie同步过程会将[!DNL Adobe Experience Cloud] ID (ECID)与Marketo潜在客户关联。 指定要在导出文件中包含ECID的`ecids`字段。 |
| columnHeaderName | 对象 | 否 | 字段和列标题键值对的JSON对象。 每个密钥必须是导出作业中包含的字段的API名称。 通过调用Describe Lead检索API名称。 每个值都是该字段的导出列标题。 |
| 格式 | 字符串 | 否 | 导出文件格式：CSV表示逗号分隔的值，TSV表示制表符分隔的值，或SSV表示空格分隔的值。 默认值为CSV。 |

## 创建作业

使用[创建导出潜在客户作业](https://developer.adobe.com/marketo-apis/api/mapi#tag/Bulk-Export-Leads/operation/createExportLeadsUsingPOST)端点定义导出作业。 指定要导出的`fields`、一个`filter`类型及其参数、文件`format`以及任何自定义列标题名称。

```http
POST /bulk/v1/leads/export/create.json
```

```json
{
   "fields": [
      "firstName",
      "lastName",
      "id",
      "email"
   ],
   "format": "CSV",
   "columnHeaderNames": {
      "firstName": "First Name",
      "lastName": "Last Name",
      "id": "Marketo Id",
      "email": "Email Address"
   },
   "filter": {
      "createdAt": {
         "startAt": "2017-01-01T00:00:00Z",
         "endAt": "2017-01-31T00:00:00Z"
      }
   }
}
```

此请求会为2017年1月1日至2017年1月31日期间创建的潜在客户创建一个导出作业。 导出包括来自`firstName`、`lastName`、`id`和`email`字段的值。

```json
{
   "requestId": "e42b#14272d07d78",
   "success": true,
   "result": [
      {
         "exportId": "ce45a7a1-f19d-4ce2-882c-a3c795940a7d",
         "status": "Created",
         "createdAt": "2017-01-21T11:47:30-08:00",
         "queuedAt": "2017-01-21T11:48:30-08:00",
         "format": "CSV"
      }
   ]
}
```

响应将确认作业已创建但未启动。 要启动作业，请从创建响应中使用`exportId`调用[Enqueue Export Lead Job](https://developer.adobe.com/marketo-apis/api/mapi#tag/Bulk-Export-Leads/operation/enqueueExportLeadsUsingPOST)终结点。

```http
POST /bulk/v1/leads/export/{exportId}/enqueue.json
```

```json
{
    "requestId": "147e4#16b24d9b913",
    "result": [
        {
            "exportId": "fad2cd1b-e822-4025-be1e-9caa9cf1d4b8",
            "format": "CSV",
            "status": "Queued",
            "createdAt": "2019-06-04T23:35:43Z",
            "queuedAt": "2019-06-04T23:36:17Z"
        }
    ],
    "success": true
}
```

入队响应的`status`为“已排队”。 导出插槽可用时，状态将更改为“正在处理”。

## 轮询作业状态

您只能检索同一API用户创建的作业的状态。

商机导出作业异步运行。 轮询[获取导出潜在客户作业状态](https://developer.adobe.com/marketo-apis/api/mapi#tag/Bulk-Export-Leads/operation/getExportLeadsStatusUsingGET)终结点以跟踪作业的进度。

状态每60秒只更新一次。 不要更频繁地轮询；在几乎所有情况下，该间隔仍然过大。

```http
GET /bulk/v1/leads/export/{exportId}/status.json
```

```json
{
   "requestId": "e42b#14272d07d78",
   "success": true,
   "result": [
      {
         "exportId": "ce45a7a1-f19d-4ce2-882c-a3c795940a7d",
         "status": "Processing",
         "createdAt": "2017-01-21T11:47:30-08:00",
         "queuedAt": "2017-01-21T11:48:30-08:00",
         "format": "CSV"
      }
   ]
}
```

此响应显示作业仍在处理中，因此文件不可用。 当作业状态变为“已完成”时，即可下载文件。

`status`字段可以返回以下任意值：

- 创建时间
- 已排队
- 正在处理
- 已取消
- 已完成
- 失败

## 检索数据

要检索已完成的潜在客户导出，请使用`exportId`调用[获取导出潜在客户文件](https://developer.adobe.com/marketo-apis/api/mapi#tag/Bulk-Export-Leads/operation/getExportLeadsFileUsingGET)终结点。

```http
GET /bulk/v1/leads/export/{exportId}/file.json
```

响应正文包含为作业配置的格式的文件。

如果请求的潜在客户字段不包含任何数据，则导出文件中的相应字段将包含`null`。 在以下示例中，返回的商机具有空的电子邮件字段。

```csv
firstName,lastName,email,cookies
Russell,Wilson,null,_mch-localhost-1536605780000-12105
```

对于部分或可恢复的检索，文件终结点支持类型为`bytes`的可选HTTP `Range`标头。 如果不设置标头，则端点将返回所有内容。 了解有关将`Range`标头与Marketo [批量提取](bulk-extract.md)结合使用的更多信息。

## 取消作业

要取消配置不正确或不必要的作业，请调用[取消导出潜在客户作业](https://developer.adobe.com/marketo-apis/api/mapi#tag/Bulk-Export-Leads/operation/cancelExportLeadsUsingPOST)终结点。

```http
POST /bulk/v1/leads/export/{exportId}/cancel.json
```

```json
{
   "requestId": "e42b#14272d07d78",
   "success": true,
   "result": [
      {
         "exportId": "ce45a7a1-f19d-4ce2-882c-a3c795940a7d",
         "status": "Cancelled",
         "createdAt": "2017-01-21T11:47:30-08:00",
         "format": "CSV"
      }
   ]
}
```

响应将确认作业已取消。
