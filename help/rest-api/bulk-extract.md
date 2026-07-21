---
title: 批量提取
feature: REST API
description: 了解如何使用Marketo批量提取REST API导出潜在客户、活动、项目成员和自定义对象，OAuth、作业队列和每日限制为500 MB。
exl-id: 6a15c8a9-fd85-4c7d-9f65-8b2e2cba22ff
TQID: https://experienceleague.adobe.com/ECSchsjqp8fyxXbUGl5DgXHUkXuN0sIUc3yJfVaIe1E
product_v2: id: b27e5950-9033-45ac-9f86-eb22e567f615
feature_v2: id: f71e690b-4480-4b67-9ef5-88f42f9cdfdb
role_v2: id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
source-git-commit: 3e6d310c5aec1a3435424fb122b71d825db5af0e
workflow-type: tm+mt
source-wordcount: 1549
ht-degree: 0%

---

# 批量提取

Marketo批量提取提供用于检索大量人员和人员相关数据的界面。 接口当前可用于四种对象类型：

- 潜在客户（人员）
- 活动
- 计划成员
- 自定义对象

要执行批量提取，请执行以下操作：

1. 创建作业并定义要检索的数据。
1. 将作业排入队列。
1. 等待作业完成文件写入。
1. 通过HTTP检索文件。

批量提取作业将异步运行。 轮询作业以检索导出状态。

`Note:`批量API端点未像其他端点一样带有“/rest”前缀。

## 身份验证

批量提取API使用与其他Marketo REST API相同的OAuth 2.0身份验证方法。 在`Authorization: Bearer {_AccessToken_}` HTTP标头中发送有效的访问令牌。

>[!IMPORTANT]
>
>2026年8月31日，将移除对使用&#x200B;**access_token**&#x200B;查询参数的身份验证的支持。 如果您的项目使用查询参数来传递访问令牌，则应尽快更新以使用&#x200B;**Authorization**&#x200B;标头。 新开发应仅使用&#x200B;**Authorization**&#x200B;标头。

## 限制

- 最大并发导出作业数：2
- 已排队导出作业的最大数量（包括当前正在导出的作业）：10
- 文件保留期：7天
- 默认每日导出分配：500MB。 该分配在每日中午12:00 CST重置。 增加部分可购买。
- 日期范围筛选器（`createdAt`或`updatedAt`）的最大时间范围：31天

对于某些订阅类型，UpdatedAt和智能列表的批量潜在客户提取过滤器不可用。 如果这些过滤器不可用，则创建导出潜在客户作业端点返回错误“1035，目标订阅的过滤器类型不受支持”。 请联系Marketo支持，为您的订阅启用此功能。

### 队列

批量提取API使用一个在潜在客户、活动、项目成员和自定义对象之间共享的作业队列。 首先，调用“创建导出潜在客户/活动/项目群成员作业”端点以创建提取作业。 然后，调用相应的“排入队列导出潜在客户/活动/项目群成员作业”端点以将作业排入队列。 当计算资源可用时，作业即开始。

队列最多可包含10个作业。 如果在队列已满时尝试将作业排入队列，则“将导出作业排入队列”端点将返回错误“1029，队列中的作业过多”。 最多两个作业可以具有“正在处理”状态并同时运行。

### 文件大小

批量提取API根据批量提取作业检索的数据的磁盘上大小进行计数。 要确定文件大小（以字节为单位），请读取导出作业的已完成状态响应中的`fileSize`属性。

每日配额为500MB，在潜在客户、活动、计划成员和自定义对象之间共享。 当超过配额时，在配额于[中部时间](https://en.wikipedia.org/wiki/Central_Time_Zone)午夜重置之前，您无法创建或将另一个作业排入队列。 在重置之前，API返回错误“1029，超出导出每日配额”。 除每日配额外，没有文件大小上限。

在作业排队或处理之后，除非发生错误或取消作业，否则该作业将运行到完成。 如果作业失败，则必须重新创建它。

API仅在作业达到已完成状态时才写入完整文件。 它不会写入部分文件。 要验证该文件，请计算其SHA-256哈希，并将其与作业状态端点返回的校验和进行比较。

要确定当天使用的总磁盘空间，请调用“获取导出潜在客户/活动/项目群成员作业”端点。 这些端点返回过去七天的所有作业。

使用`status`和`finishedAt`属性将列表筛选为当天完成的作业。 然后，添加这些作业的文件大小。 无法删除文件以回收磁盘空间。

## 权限

批量提取使用与Marketo REST API相同的权限模型。 它不需要额外的特殊权限，但每组端点需要特定的权限。

只有创建批量提取作业的API用户才能访问它、轮询其状态或检索其文件内容。

“批量提取”端点不了解Marketo工作区。 提取请求包含来自所有工作区的数据，这与您为自定义服务定义“仅限API用户”的方式无关。

## 创建作业

Marketo批量提取API使用作业来启动和运行数据提取。 以下请求将创建潜在客户导出作业：

```http
POST /bulk/v1/leads/export/create.json
```

```json
{
   "fields": [
      "firstName",
      "lastName"
   ],
   "format": "CSV",
   "columnHeaderNames": {
      "firstName": "First Name",
      "lastName": "Last Name"
   },
   "filter": {
      "createdAt": {
         "startAt": "2023-01-01T00:00:00Z",
         "endAt": "2023-01-31T00:00:00Z"
      }
   }
}
```

此请求会创建一个作业，用于导出2023年1月1日至2023年1月31日期间创建的每个潜在客户。 CSV文件包含“firstName”和“lastName”字段中的值，并使用列标题“First Name”和“Last Name”。

```json
{
   "requestId": "e42b#14272d07d78",
   "success": true,
   "result": [
      {
         "exportId": "ce45a7a1-f19d-4ce2-882c-a3c795940a7d",
         "status": "Created",
         "createdAt": "2023-01-21T11:47:30-08:00",
         "queuedAt": "2023-01-21T11:48:30-08:00",
         "format": "CSV",
      }
   ]
}
```

响应返回`exportId`属性中的作业ID。 使用此作业ID可以排入队列或取消作业、检查其状态或检索已完成的文件。

### 常用参数

每个作业创建端点都具有用于配置文件格式、字段名和过滤器的公共参数。 每个提取作业子类型还可以具有其他参数：

| 参数 | 数据类型 | 注释 |
| --- | --- | --- |
| 格式 | 字符串 | 使用逗号分隔值、制表符分隔值和分号分隔值的选项确定提取数据的文件格式。 接受以下内容之一：CSV、SSV、TSV。 格式默认为CSV。 |
| columnHeaderName | 对象 | 允许设置返回文件中列标题的名称。 每个成员键是要重命名的列标题的名称，该值是列标题的新名称。 例如，“columnHeaderNames”：{“firstName”：“First Name”，“lastName”：“Last Name” }， |
| filter | 对象 | 应用于提取作业的过滤器。 类型和选项因作业类型而异。 |

## 正在检索作业

使用相应对象类型的“获取导出作业”端点可检索最近的作业。 每个“获取导出作业”端点都支持以下参数：

- `status`按导出状态筛选作业。 有效值为“已创建”、“已排队”、“正在处理”、“已取消”、“已完成”和“失败”。
- `batchSize`限制返回的作业数。 缺省值和最大值是300。
- `nextPageToken`页通过大型结果集。

以下请求将检索状态为“已完成”或“失败”的潜在客户导出作业：

```http
GET /bulk/v1/leads/export.json?status=Completed,Failed
```

```json
{
   "requestId": "e42b#14272d07d78",
   "success": true,
   "result": [
      {
         "exportId": "ce45a7a1-f19d-4ce2-882c-a3c795940a7d",
         "status": "Completed",
         "createdAt": "2017-01-21T11:47:30-08:00",
         "queuedAt": "2017-01-21T11:48:30-08:00",
         "startedAt": "2017-01-21T11:51:30-08:00",
         "finishedAt": "2017-01-21T12:59:30-08:00",
         "format": "CSV",
         "numberOfRecords": 122323,
         "fileSize": 123424,
         "fileChecksum": "sha256:c16514c7e80fcac5ea055dacae9617fc3c29aff5365e3743071313ce0ed2a815"
      }
      ...
   ]
}
```

结果数组包含过去七天内为该对象类型创建的每个作业的状态响应。 响应仅包含发出调用的API用户拥有的作业。

## 启动作业

创建作业后，使用其作业ID将作业排入队列并启动：

```http
POST /bulk/v1/leads/export/{exportId}/enqueue.json
```

请求将启动作业并返回状态响应。 由于导出是异步运行的，因此会轮询作业状态以确定导出何时完成。

## 轮询作业状态

轮询状态端点以确定作业的进度。 只有创建作业的API用户才能轮询其状态。

作业状态的更新频率不会超过每60秒一次。 轮询频率不要高于该频率。 对于大多数用例，每5分钟轮询一次就足够了。 每次成功导出的数据会保留10天。

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
         "status": "Completed",
         "createdAt": "2017-01-21T11:47:30-08:00",
         "queuedAt": "2017-01-21T11:48:30-08:00",
         "startedAt": "2017-01-21T11:51:30-08:00",
         "finishedAt": "2017-01-21T12:59:30-08:00",
         "format": "CSV",
         "numberOfRecords": 122323,
         "fileSize": 123424,
         "fileChecksum": "sha256:d9c73f0b6960c71623c8bafe29603b3e8e20fd0e4eeaefd119c0227506ea9be4"
      }
   ]
}
```

内部`status`成员指示作业进度。 其值可以是Create、Queued、Processing、Canceled、Completed或Failed。

在本例中，作业已完成，因此您可以停止轮询并检索文件。 对于已完成的作业，`fileSize`成员表示文件总长度（以字节为单位），`fileChecksum`成员包含文件的SHA-256哈希。 作业状态会在达到“已完成”或“失败”状态后的30天内可用。

## 检索数据

作业完成后，检索导出的文件：

```http
GET /bulk/v1/leads/export/{exportId}/file.json
```

响应包含为作业配置的格式的文件。 如果作业不完整或请求包含无效的作业ID，则文件端点将返回“404未找到”状态和纯文本错误消息。 此响应与大多数其他Marketo REST端点响应不同。

为了支持部分和可恢复的检索，文件终结点支持[RFC 7233](https://datatracker.ietf.org/doc/html/rfc7233)中定义的`bytes`类型的可选HTTP `Range`标头。 如果不设置标头，则端点将返回整个文件。

要检索文件的前10,000个字节，请在GET请求中传递以下标头。 范围从字节0开始：

```text
Range: bytes=0-9999
```

对于部分文件，端点返回状态代码206，以及Accept-ranges、Content-Length和Content-Range标头：

```text
Accept-Ranges: bytes
Content-Length: 10000
Content-Range: bytes 0-9999/123424
```

### 部分检索和恢复

使用`Range`标头检索文件的一部分或恢复检索。 文件范围从字节0开始，到值`fileSize`减1结束。 “获取导出文件”终结点还将文件长度报告为`Content-Range`响应标头中的分母。

如果检索部分失败，您可以恢复它。 例如，如果您尝试检索一个1000字节的文件，但只接收前725字节，请再次调用端点并传递一个新范围：

```text
Range: bytes=725-999
```

此请求返回文件的剩余275字节。

#### 文件完整性验证

当`status`为“已完成”时，作业状态端点将在`fileChecksum`属性中返回校验和。 校验和是导出文件的SHA-256哈希值。 将其与检索的文件的SHA-256哈希进行比较，验证该文件是否完整。

以下响应包含校验和：

```json
{
    "exportId": "45547609-6732-418a-bb7b-17b0160b2317",
    "format": "CSV",
    "status": "Completed",
    "createdAt": "2019-06-04T23:13:12Z",
    "queuedAt": "2019-06-04T23:14:02Z",
    "startedAt": "2019-06-04T23:15:19Z",
    "finishedAt": "2019-06-04T23:36:40Z",
    "numberOfRecords": 1776,
    "fileSize": 400785,
    "fileChecksum": "sha256:83aca1351c9398d2770330e21a9e278880fd2f1eeaf8c8238bf7676d5c21d1c6"
}
```

以下示例使用sha256sum命令行实用程序创建名为“bulk_lead_export.csv”的检索文件的SHA-256哈希：

```bash
$ sha256sum bulk_lead_export.csv
83aca1351c9398d2770330e21a9e278880fd2f1eeaf8c8238bf7676d5c21d1c6 *bulk_lead_export.csv
```

## 取消作业

如果作业配置不正确或不再需要，请取消该作业：

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
         "format": "CSV",
      }
   ]
}
```

响应状态表示作业已取消。
