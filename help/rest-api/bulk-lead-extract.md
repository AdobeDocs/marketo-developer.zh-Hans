---
title: 批量潜在客户提取
feature: REST API
description: 批量提取潜在客户数据。
exl-id: 42796e89-5468-463e-9b67-cce7e798677b
source-git-commit: 66add4c38d0230c36d57009de985649bb67fde3e
workflow-type: tm+mt
source-wordcount: '1173'
ht-degree: 2%

---

# 批量潜在客户提取

[批量潜在客户提取终结点引用](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Bulk-Export-Leads)

REST API的“批量潜在客户提取”集提供了一个编程接口，用于从Marketo中检索大量潜在客户/人员记录。 此外，它可用于根据记录的创建日期、最近更新、静态列表成员资格或智能列表成员资格增量检索潜在客户。 推荐的界面，用于需要在Marketo与一个或多个外部系统之间持续交换数据的用例，用于ETL、数据仓库存储和存档目的。

## 权限

Bulk Lead Extract API要求拥有的API用户具有只读潜在客户或读写潜在客户权限中的一个或两个角色。

## 过滤器

潜在客户支持各种筛选器选项。 某些筛选器（包括`updatedAt`、`smartListName`和`smartListId`）需要额外的基础结构组件，这些组件尚未推广到所有订阅。 每个导出作业只能指定一个过滤器类型。

| 筛选器类型 | 数据类型 | 备注 |
|---|---|---|
| createdat | 日期范围 | 接受具有成员`startAt`和`endAt`的JSON对象。 `startAt`接受表示低水位线的日期时间，`endAt`接受表示高水位线的日期时间。 范围必须为31天或更少。 日期时间应采用ISO-8601格式，不带毫秒。 具有此筛选器类型的作业将返回在日期范围内创建的所有可访问记录。 |
| 更新时间* | 日期范围 | 接受具有成员`startAt`和`endAt`的JSON对象。 `startAt`接受表示低水位线的日期时间，`endAt`接受表示高水位线的日期时间。 范围必须为31天或更少。 日期时间应采用ISO-8601格式，不带毫秒。 注意：此过滤器不筛选可见的“updatedAt”字段，该字段仅反映对标准字段的更新。 它根据最近一次字段更新商机记录的时间进行筛选。使用此筛选类型的作业将返回日期范围内最近更新的所有可访问记录。 |
| staticListName | 字符串 | 接受静态列表的名称。 具有此筛选器类型的作业将返回所有可访问的记录，这些记录是作业开始处理时静态列表的成员。 使用“获取列表”端点检索静态列表名称。 |
| staticListId | 整数 | 接受静态列表的id。 具有此筛选器类型的作业将返回所有可访问的记录，这些记录是作业开始处理时静态列表的成员。 使用Get Lists端点检索静态列表ID。 |
| smartListName* | 字符串 | 接受智能列表的名称。 具有此筛选器类型的作业将返回在作业开始处理时作为智能列表成员的所有可访问记录。 使用获取智能列表端点检索智能列表名称。 |
| smartListId* | 整数 | 接受智能列表的id。 具有此筛选器类型的作业将返回在作业开始处理时作为智能列表成员的所有可访问记录。 使用获取智能列表端点检索智能列表ID。 |


过滤器类型不适用于某些订阅。 如果您的订阅不可用，则在调用“创建导出潜在客户作业”终结点（“1035，目标订阅不受支持的过滤器类型”）时会收到一个错误。 客户可以联系Marketo支持部门以在其订阅中启用此功能。

## 选项

创建导出潜在客户作业端点提供了多个格式选项，使用户能够在导出的文件中包括特定字段，能够重命名这些字段的列标题，以及导出文件的格式。

| 参数 | 数据类型 | 必需 | 备注 |
|---|---|---|---|
| 字段 | 数组[字符串] | 是 | 字段参数接受字符串的JSON数组。 每个字符串必须是Marketo潜在客户字段的REST API名称。 列出的字段包含在导出的文件中。 除非用columnHeader覆盖，否则每个字段的列标题将是每个字段的REST API名称。 注意：启用[!DNL Adobe Experience Cloud Audience Sharing]功能后，将进行Cookie同步过程以将[!DNL Adobe Experience Cloud] ID (ECID)与Marketo潜在客户关联。 您可以指定“ecid”字段以将ECID包含在导出文件中。 |
| columnHeaderName | 对象 | 否 | 包含字段和列标题名称的键值对的JSON对象。 键必须是导出作业中包含的字段的名称。 这是可以通过调用Describe Lead检索的字段的API名称。 值是该字段的导出列标题的名称。 |
| 格式 | 字符串 | 否 | 接受以下内容之一：CSV、TSV、SSV。 如果设置，导出的文件将分别呈现为逗号分隔的值、制表符分隔的值或空格分隔的值文件。 如果未设置，则默认为CSV。 |


## 创建作业

作业的参数是在使用[创建导出潜在客户作业](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Bulk-Export-Leads/operation/createExportLeadsUsingPOST)终结点开始导出之前定义的。 我们必须定义导出所需的`fields`、`filter`的参数类型、文件的`format`以及列标题名称（如果有）。

```
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
         "`endAt`": "2017-01-31T00:00:00Z"
      }
   }
}
```

此请求将开始导出在2017年1月1日至2017年1月31日期间创建的一组潜在客户，包括来自相应`firstName`、`lastName`、`id`和`email`字段的值。

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

这将返回一个状态响应，指示作业已创建。 作业已定义和创建，但尚未开始。 为此，必须使用创建状态响应中的exportId调用[排队Export Lead作业](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Bulk-Export-Leads/operation/enqueueExportLeadsUsingPOST)终结点：

```
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

该响应包含`status`个“已排队”，之后当存在可用的导出插槽时，该响应将设置为“正在处理”。

## 轮询作业状态

只能检索同一API用户创建的作业的`Note:`状态。

由于这是异步端点，因此，在创建作业后，我们必须轮询其状态以确定其进度。 使用[获取导出潜在客户作业状态](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Bulk-Export-Leads/operation/getExportLeadsStatusUsingGET)终结点进行轮询。 状态仅每60秒更新一次，因此不建议使用低于此值的轮询频率，并且在几乎所有情况下，仍然会过度轮询。 让我们来快速了解一下投票。

```
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

状态终结点响应指示作业仍在处理，因此文件尚不可检索。 当作业状态变为“已完成”时，它就会准备下载。

状态字段可以使用以下任意一项进行响应：

- 已创建
- 已排队
- 正在处理
- 已取消
- 已完成
- 已失败

## 检索数据

要检索已完成的潜在客户导出的文件，只需使用`exportId`调用[获取导出潜在客户文件](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Bulk-Export-Leads/operation/getExportLeadsFileUsingGET)终结点。

```
GET /bulk/v1/leads/export/{exportId}/file.json
```

响应包含以作业配置方式格式化的文件。 端点使用文件的内容进行响应。

如果请求的潜在客户字段为空（不包含数据），则`null`将被放置在导出文件中的相应字段中。 在下面的示例中，返回的商机的email字段为空。

```csv
firstName,lastName,email,cookies
Russell,Wilson,null,_mch-localhost-1536605780000-12105
```

为了支持提取数据的部分检索和恢复友好检索，文件端点可以选择性地支持类型字节的HTTP标头范围。 如果未设置标头，则会返回整个内容。 阅读有关将Range标头与Marketo [批量提取](bulk-extract.md)结合使用的更多信息。

## 取消作业

如果某个作业配置不正确或变得不必要，可以使用[取消导出潜在客户作业](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Bulk-Export-Leads/operation/cancelExportLeadsUsingPOST)端点轻松取消该作业：

```
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

此响应带有指示作业已取消的状态。
