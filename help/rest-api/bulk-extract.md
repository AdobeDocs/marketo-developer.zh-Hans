---
title: 批量提取
feature: REST API
description: 用于提取Marketo数据的批处理操作。
exl-id: 6a15c8a9-fd85-4c7d-9f65-8b2e2cba22ff
source-git-commit: e7d893a81d3ed95e34eefac1ee8f1ddd6852f5cc
workflow-type: tm+mt
source-wordcount: '1683'
ht-degree: 0%

---

# 批量提取

Marketo提供了用于检索大量人员和人员相关数据的接口，称为“批量提取”。 目前，界面提供三种对象类型：

- 潜在客户（人员）
- 活动
- 计划成员
- 自定义对象

批量提取是通过以下方式执行的：创建作业、定义要检索的数据集、将作业排入队列、等待作业完成文件写入，然后通过HTTP检索文件。 这些作业以异步方式执行，并且可以轮询以检索导出状态。

`Note:`批量API端点未像其他端点一样带有“/rest”前缀。

## 身份验证

批量提取API使用与其他Marketo REST API相同的OAuth 2.0身份验证方法。 这需要有效的访问令牌作为HTTP标头`Authorization: Bearer {_AccessToken_}`发送。

>[!IMPORTANT]
>
>2025年6月30日，将移除对使用&#x200B;**access_token**&#x200B;查询参数的身份验证的支持。 如果您的项目使用查询参数来传递访问令牌，则应尽快更新以使用&#x200B;**Authorization**&#x200B;标头。 新开发应仅使用&#x200B;**Authorization**&#x200B;标头。

## 限制

- 最大并发导出作业数：2
- 最大已排队导出作业数（包括当前导出作业）：10
- 文件保留期：7天
- 默认每日导出分配：500MB（CST凌晨12:00每日重置）。 增加的可购买部分。
- 日期范围过滤器（createdAt或updatedAt）的最大时间范围：31天

对于某些订阅类型，UpdatedAt和智能列表的批量潜在客户提取过滤器不可用。 如果不可用，对创建导出潜在客户作业端点的调用将返回错误“1035，目标订阅的过滤器类型不受支持”。 客户可以联系Marketo支持部门以在其订阅中启用此功能。

### 队列

批量提取API使用作业队列（在潜在客户、活动、项目成员和自定义对象之间共享）。 必须先创建提取作业，然后通过调用“创建导出潜在客户/活动/项目群成员作业”和“将导出潜在客户/活动/项目群成员作业端点排入队列”来将提取作业排入队列。 一旦排入队列，作业就会从队列中提取，并在计算资源可用时启动。

队列中的最大作业数为10。 如果在队列已满时尝试将作业排入队列，则“将导出作业排入队列”端点将返回错误“1029，队列中的作业过多”。 最多可同时运行两个作业（状态为“正在处理”）。

### 文件大小

批量提取API根据批量提取作业检索的数据的磁盘大小进行计量。 可以通过从导出作业的已完成状态响应中读取`fileSize`属性来确定作业的显式大小（字节）。

每日配额最大为每天500 MB，在潜在客户、活动、项目群成员和自定义对象之间共享。 当超出配额时，在每日配额于[中部时间](https://en.wikipedia.org/wiki/Central_Time_Zone)午夜重置之前，您无法创建或将另一个作业排入队列。 在此之前，将返回错误“1029， Export daily quota exceeded”。 除每日配额外，没有文件大小上限。

一旦作业排队或处理，它就会运行到完成（除非出现错误或作业取消）。 如果作业由于某个原因失败，则必须重新创建它。 仅当作业达到已完成状态（从不写入部分文件）时，才会完全写入文件。 通过计算SHA-256哈希值，并将其与作业状态端点返回的校验和进行比较，可以验证文件是否已完全写入。

您可以通过调用Get Export Lead/Activity/Program Member Jobs来确定当天使用的磁盘总数。 这些端点会返回过去七天中的所有作业的列表。 您可以对该列表进行向下筛选，仅显示当天完成的作业（使用`status`和`finishedAt`属性）。 然后，对这些作业的文件大小求和，以生成使用的总数量。 无法删除文件以回收磁盘空间。

## 权限

批量提取使用与Marketo REST API相同的权限模型，无需任何额外的特殊权限即可使用，但需要为每个端点集设置特定权限。

批量提取作业仅可供创建这些作业的API用户访问，包括轮询状态和检索文件内容。

“批量提取”端点不了解Marketo工作区。 提取请求始终包含所有工作区的数据，无论您如何为自定义服务定义仅API用户。

## 创建作业

Marketo的批量提取API使用作业的概念来启动和执行数据提取。 我们来看看如何创建一个简单的潜在客户导出作业。

```
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

此简单请求将构造一个作业，该作业将返回“firstName”和“lastName”字段中包含的值，并将列标题“First Name”和“Last Name”作为CSV文件，包含2023年1月1日至2023年1月31日期间创建的每个潜在客户。

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

创建作业时，它会返回`exportId`属性中的作业ID。 然后，我们可以使用此作业ID将作业排入队列、取消作业、检查其状态或检索已完成的文件。

### 常用参数

每个作业创建端点共享一些用于配置批量提取作业的文件格式、字段名和过滤器的常用参数。 提取作业的每种子类型可能都有其他参数：

| 参数 | 数据类型 | 注释 |
|---|---|---|
| 格式 | 字符串 | 使用逗号分隔值、制表符分隔值和分号分隔值的选项确定提取数据的文件格式。 接受以下内容之一：CSV、SSV、TSV。 格式默认为CSV。 |
| columnHeaderName | 对象 | 允许设置返回文件中列标题的名称。 每个成员键是要重命名的列标题的名称，该值是列标题的新名称。 例如，“columnHeaderNames”：{“firstName”：“First Name”，“lastName”：“Last Name” }， |
| filter | 对象 | 应用于提取作业的过滤器。 类型和选项因作业类型而异。 |


## 正在检索作业

有时候，您可能必须检索最近的作业。 使用相应对象类型的“获取导出作业”可轻松完成此操作。 每个“获取导出作业”终结点都支持`status`筛选器字段，即  `batchSize`用于限制返回的作业数，`nextPageToken`用于分页大型结果集。 状态筛选器支持导出作业的每个有效状态：“已创建”、“已排队”、“正在处理”、“已取消”、“已完成”和“失败”。 batchSize的最大值和缺省值均为300。 我们来获取潜在客户导出作业列表：

```
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

终结点响应结果数组中过去七天为该对象类型创建的每个作业的`status`响应。 响应将仅包括发出调用的API用户所拥有的作业的结果。

## 启动作业

有了工作ID，让我们开始工作：

```
POST /bulk/v1/leads/export/{exportId}/enqueue.json
```

此操作将启动作业的执行并返回状态响应。 由于导出始终以异步方式完成，因此我们必须轮询作业的状态以确定是否已完成导出。 给定作业的状态更新频率不会超过每60秒一次，因此不应超过此频率轮询状态。 但是，请记住，大多数用例不应要求轮询超过每5分钟一次。 每次成功导出的数据会保留10天。

## 轮询作业状态

确定作业的状态很简单。

只能轮询由创建作业的相同API用户创建的作业的状态。

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

内部`status`成员指示作业进度，它可以是以下值之一：“已创建”、“已排队”、“正在处理”、“已取消”、“已完成”、“失败”。 在这种情况下，我们的作业已完成，因此我们可以停止轮询并继续检索文件。 完成后，`fileSize`成员表示文件的总长度（以字节为单位），`fileChecksum`成员包含文件的SHA-256哈希。 作业状态在达到“已完成”或“失败”状态后的30天内可用。

## 检索数据

作业完成后，您可以轻松检索文件。

```
GET /bulk/v1/leads/export/{exportId}/file.json
```

响应包含以作业配置方式格式化的文件。 端点使用文件的内容进行响应。 与大多数其他Marketo REST端点不同，如果作业尚未完成或传递了错误的作业ID，则文件端点会以404 Not Found状态响应，并以纯文本错误消息作为有效负载。

为了支持对提取的数据进行部分检索和恢复友好检索，文件终结点可以选择性地支持类型为`bytes`的HTTP标头`Range`（根据[RFC 7233](https://datatracker.ietf.org/doc/html/rfc7233)）。 如果未设置标头，则将返回所有内容。 要检索文件的前10,000字节，您需要将以下标头作为GET请求的一部分传递到端点，从字节0开始：

```
Range: bytes=0-9999
```

检索部分文件时，端点使用状态代码206做出响应，并返回Accept-Ranges、Content-Length和Content-Range标头：

```
Accept-Ranges: bytes
Content-Length: 1000
Content-Range: bytes 0-9999/123424
```

### 部分检索和恢复

文件可部分检索，或稍后使用`Range`标头恢复。 文件的范围从字节0开始，到值`fileSize`减1结束。 在调用Get Export File终结点时，文件的长度还报告为`Content-Range`响应标头值中的分母。 如果检索部分失败，可以稍后恢复。 例如，如果您尝试检索长度为1000字节的文件，但只收到前725字节，则可以从失败点重试检索，方法是再次调用端点并传递一个新范围：

```
Range: bytes 724-999
```

这将返回文件的剩余275字节。

#### 文件完整性验证

当`status`为“已完成”时，作业状态端点在`fileChecksum`属性中返回校验和。 校验和是导出文件的SHA-256哈希值。 您可以将校验和与检索文件的SHA-256哈希进行比较，以验证其是否已完成。

以下是包含校验和的示例响应：

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

以下是使用sha256sum命令行实用程序创建名为“bulk_lead_export.csv”的检索文件的SHA-256哈希的示例：

```
$ sha256sum bulk_lead_export.csv
83aca1351c9398d2770330e21a9e278880fd2f1eeaf8c8238bf7676d5c21d1c6 *bulk_lead_export.csv
```

## 取消作业

如果作业配置不正确或变得不必要，可以轻松地取消作业：

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
         "format": "CSV",
      }
   ]
}
```

此响应带有指示作业已取消的状态。
