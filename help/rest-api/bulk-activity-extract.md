---
title: 批量活动提取
feature: REST API
description: Marketo批量活动提取REST API以使用31天日期范围、活动和主要属性过滤器来导出ETL和CRM的大量活动数据。
exl-id: 6bdfa78e-bc5b-4eea-bcb0-e26e36cf6e19
TQID: https://experienceleague.adobe.com/lIlXNjatN-F77Dv3xsVkQ3hAWwLZ4wlSW0zKNkFJFMA
product_v2:
  - id: b27e5950-9033-45ac-9f86-eb22e567f615
feature_v2:
  - id: b0bb9048-d951-48d8-8232-45cf248a7e27
  - id: e64968b2-4ee5-47f9-8cae-0588f184b9eb
  - id: ea90ebee-5c84-42d9-8b21-006bdabc95a3
role_v2:
  - id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
topic_v2:
  - id: aa2f3246-cb95-4b30-8899-fdf7d73550cc
source-git-commit: 3e6d310c5aec1a3435424fb122b71d825db5af0e
workflow-type: tm+mt
source-wordcount: 1268
ht-degree: 4%

---

# 批量活动提取

[批量活动提取端点引用](https://developer.adobe.com/marketo-apis/api/mapi)

“批量活动提取REST API”可从Marketo检索大量活动数据。 将这些API用于不需要低延迟的流程，例如CRM集成、ETL、数据仓库存储和数据存档。

## 权限

API用户必须具有“只读活动”或“读写活动”权限。

## 过滤器

| 筛选器类型 | 数据类型 | 必需 | 注释 |
| --- | --- | --- | --- |
| `createdAt` | Date Range | 是 | 包含`startAt`和`endAt`的JSON对象。 `startAt`是低水位线日期时间，`endAt`是高水位线日期时间。 范围必须为31天或更少。 作业将返回在日期范围内创建的所有可访问记录。 使用不带毫秒的ISO-8601日期时间值。 |
| `activityTypeIds` | 数组\[整数\] | 否 | 所请求活动类型的整数数组。 不支持“删除潜在客户”活动。 请改用[Get Deleted Leads](https://developer.adobe.com/marketo-apis/api/mapi#tag/Activities/operation/getDeletedLeadsUsingGET)终结点。 使用[获取活动类型终结点](https://developer.adobe.com/marketo-apis/api/mapi#tag/Activities/operation/getAllActivityTypesUsingGET)检索活动类型ID。 |
| [`primaryAttributeValueIds`](#primaryattributevalueids-options) | 数组\[整数\] | 否 | 一个数组，主属性最多接受50个ID。 每个id唯一标识一个商机字段或资产。 通过调用相应的REST API端点检索ID。 例如，要筛选“填写表单”活动的特定表单，请将表单名称传递到[按名称获取表单](https://developer.adobe.com/marketo-apis/api/asset#tag/Forms/operation/getLpFormByNameUsingGET)端点以检索表单ID。 有关支持的活动类型，请参阅[primaryAttributeValueIds选项](#primaryattributevalueids-options)。 |
| [`primaryAttributeValues`](#primaryattributevalues-options) | 数组\[字符串\] | 否 | 一个数组，它最多接受主属性的50个名称。 每个名称唯一标识潜在客户字段或资产。 通过调用相应的REST API端点检索名称。 例如，要筛选“填写表单”活动的特定表单，请将表单ID传递给[按ID获取表单](https://developer.adobe.com/marketo-apis/api/asset#tag/Forms/operation/getLpFormByIdUsingGET)端点以检索表单名称。 有关支持的活动类型，请参阅[primaryAttributeValues选项](#primaryattributevalues-options)。 |

### primaryAttributeValueIds选项 {#primaryattributevalueids-options}

| 活动类型 | 主要属性值ID | 检索端点 | 资产组 |
| --- | --- | --- | --- |
| 更改数据值 | 商机字段ID | [描述潜在客户](https://developer.adobe.com/marketo-apis/api/mapi#tag/Leads/operation/describeUsingGET_2) | 属性名称 |
| 更改评分 | 商机字段ID | [描述潜在客户](https://developer.adobe.com/marketo-apis/api/mapi#tag/Leads/operation/describeUsingGET_2) | 属性名称 |
| 进程中的更改状态 | 项目ID | [按名称获取计划](https://developer.adobe.com/marketo-apis/api/asset#tag/Programs/operation/getProgramByNameUsingGET) | 营销计划 |
| 添加到列表 | 静态列表ID | [按名称获取静态列表](https://developer.adobe.com/marketo-apis/api/asset#tag/Static-Lists/operation/getStaticListByNameUsingGET) | 静态列表 |
| 从列表中移除 | 静态列表ID | [按名称获取静态列表](https://developer.adobe.com/marketo-apis/api/asset#tag/Static-Lists/operation/getStaticListByNameUsingGET) | 静态列表 |
| 填写表单 | 表单ID | [按名称获取表单](https://developer.adobe.com/marketo-apis/api/asset#tag/Forms/operation/getLpFormByNameUsingGET) | Web窗体 |

使用`primaryAttributeValueIds`时，还必须包含`activityTypeIds`筛选器。 此过滤器只能包含与相应资产组匹配的活动ID。 例如，在筛选Web窗体资源时，`activityTypeIds`只能包含“填写窗体”活动类型ID。

以下请求包含`primaryAttributeValueIds`筛选器：

```json
{
  "filter": {
    "createdAt": {
      "startAt": "2021-07-01T23:59:59-00:00",
      "endAt": "2021-07-02T23:59:59-00:00"
    },
    "activityTypeIds": [
      2
    ],
    "primaryAttributeValueIds": [
      16,102,95,8
    ]
  }
}
```

`primaryAttributeValueIds`和`primaryAttributeValues`不能一起使用。

### primaryAttributeValues选项 {#primaryattributevalues-options}

| 活动类型 | 主要属性值 | 检索端点 | 资产组 |
| --- | --- | --- | --- |
| 更改数据值 | 潜在客户字段displayName | [描述潜在客户](https://developer.adobe.com/marketo-apis/api/mapi#tag/Leads/operation/describeUsingGET_2) | 属性名称 |
| 更改评分 | 潜在客户字段displayName | [描述潜在客户](https://developer.adobe.com/marketo-apis/api/mapi#tag/Leads/operation/describeUsingGET_2) | 属性名称 |
| 进程中的更改状态 | 项目名称 | [按Id获取计划](https://developer.adobe.com/marketo-apis/api/asset#tag/Programs/operation/getProgramByIdUsingGET) | 营销计划 |
| 添加到列表 | 静态列表名称 | [按Id获取静态列表](https://developer.adobe.com/marketo-apis/api/asset#tag/Static-Lists/operation/getStaticListByIdUsingGET) | 静态列表 |
| 从列表中移除 | 静态列表名称 | [按Id获取静态列表](https://developer.adobe.com/marketo-apis/api/asset#tag/Static-Lists/operation/getStaticListByIdUsingGET) | 静态列表 |
| 填写表单 | 表单名称 | [按Id获取表单](https://developer.adobe.com/marketo-apis/api/asset#tag/Forms/operation/getLpFormByIdUsingGET) | Web窗体 |

使用`&lt;program&gt;.&lt;asset&gt;`表示法指定营销计划、静态列表和Web窗体资产组的名称。 例如，将“GL_OP_ALL_2021”程序中的“MPS出站”表单指定为“GL_OP_ALL_2021.MPS出站”。

以下请求包含`primaryAttributeValues`筛选器：

```json
{
  "filter": {
    "createdAt": {
      "startAt": "2021-07-01T23:59:59-00:00",
      "endAt": "2021-07-02T23:59:59-00:00"
    },
    "activityTypeIds": [
      2
    ],
    "primaryAttributeValues": [
      "GL_OP_ALL_2021.MPS Outbound"
    ]
  }
}
```

使用`primaryAttributeValues`时，还必须包含`activityTypeIds`筛选器。 此过滤器只能包含与相应资产组匹配的活动ID。 例如，在筛选Web窗体资源时，`activityTypeIds`只能包含“填写窗体”活动类型ID。

`primaryAttributeValues`和`primaryAttributeValueIds`不能一起使用。

## 选项

| 参数 | 数据类型 | 必需 | 注释 |
| --- | --- | --- | --- |
| `filter` | 对象 | 是 | 一个对象，其中包含应用于可访问活动集的筛选器。 仅包括一个`createdAt`筛选器。 您还可以包含`activityTypeIds`筛选器。 导出作业将返回生成的活动集。 |
| `format` | 字符串 | 否 | 导出文件格式：CSV、TSV或SSV。 这些值将分别生成逗号分隔、制表符分隔或空格分隔的值。 默认值为CSV。 |
| `columnHeaderNames` | 对象 | 否 | 字段和列标题键值对的JSON对象。 每个键都必须命名导出作业中包含的字段。 其值设置该字段的导出列标题。 |
| `fields` | 数组\[字符串\] | 否 | 要包含在导出文件中的字段数组。 默认情况下，响应包括`marketoGUID`、`leadId`、`activityDate`、`activityTypeId`、`campaignId`、`primaryAttributeValueId`、`primaryAttributeValue`和`attributes`。 要返回子集，请指定此列表中的字段，如`"fields": ["leadId", "activityDate", "activityTypeId"]`。 您还可以指定`actionResult`以包括活动操作： `("succeeded", "skipped", or "failed")`。 |

## 创建作业

创建导出作业以定义要检索的记录。 使用[创建导出活动作业](https://developer.adobe.com/marketo-apis/api/mapi#tag/Bulk-Export-Activities/operation/createExportActivitiesUsingPOST)终结点。

每个作业都需要一个`createdAt`筛选器。 其`startAt`和`endAt`日期时间参数定义允许的最早和最晚的活动创建日期。 要排除不相关的活动类型，请同时包括可选的`activityTypeIds`过滤器。

以下请求为日期范围内的选定活动类型创建CSV导出作业：

```http
POST /bulk/v1/activities/export/create.json
```

```json
{
   "format": "CSV",
   "filter": {
      "createdAt": {
         "startAt": "2017-07-01T23:59:59-00:00",
         "endAt": "2017-07-31T23:59:59-00:00"
      },
      "activityTypeIds": [
         1,
         12,
         13
      ]
   }
}
```

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

响应返回`exportId`和状态“已创建”。 创建的作业尚未在处理队列中。

要将作业添加到队列，请从创建响应调用[队列导出活动作业](https://developer.adobe.com/marketo-apis/api/mapi#tag/Bulk-Export-Activities/operation/enqueueExportActivitiesUsingPOST)终结点（包含`exportId`）。

```http
POST /bulk/v1/activities/export/{exportId}/enqueue.json
```

```json
{
   "requestId": "e42b#14272d07d78",
   "success": true,
   "result": [
      {
         "exportId": "ce45a7a1-f19d-4ce2-882c-a3c795940a7d",
         "status": "Queued",
         "createdAt": "2017-01-21T11:47:30-08:00",
         "queuedAt": "2017-01-21T11:48:30-08:00",
         "format": "CSV"
      }
   ]
}
```

响应状态现在为“已排队”。 当工作人员变得可用时，状态将更改为“正在处理”，并且作业将开始从Marketo中聚合记录。

## 轮询作业状态

只能检索同一API用户创建的作业的作业状态。

“批量活动提取”以异步方式处理作业。 轮询[获取导出活动作业状态](https://developer.adobe.com/marketo-apis/api/mapi#tag/Bulk-Export-Activities/operation/getExportActivitiesStatusUsingGET)终结点以确定作业何时完成：

```http
GET /bulk/v1/activities/export/{exportId}/status.json
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
         "numberOfRecords": 15423,
         "fileSize": 12342,
         "fileChecksum": "sha256:c16514c7e80fcac5ea055dacae9617fc3c29aff5365e3743071313ce0ed2a815"
      }
   ]
}
```

`status`字段返回以下值之一：

- `Created`
- `Queued`
- `Processing`
- `Canceled`
- `Completed`
- `Failed`

## 检索数据

当作业状态为“已完成”时，使用[获取导出活动文件](https://developer.adobe.com/marketo-apis/api/mapi#tag/Bulk-Export-Activities/operation/getExportActivitiesFileUsingGET)端点检索导出的数据：

```http
GET /bulk/v1/activities/export/{exportId}/file.json
```

响应正文包含为作业配置的格式的文件。

如果请求的活动字段不包含任何数据，则相应的export-file字段中会显示`null`。 以下示例显示了导出的活动数据：

```json
marketoGUID,leadId,activityDate,activityTypeId,campaignId,primaryAttributeValueId,primaryAttributeValue,attributes
783957693,5414087,2022-02-13T14:06:20Z,104,8497,1670,MembershipTest1,"{""Reason"":""Changed by Smart Campaign MembershipTestCampaignStepChoice.MembershipTestCampaignStepChoiceSetUp action Change Data Value"",""Program Member ID"":3240303,""Acquired By"":true,""Old Status"":""Not in Program"",""New Status ID"":21,""Success"":false,""New Status"":""On List"",""Old Status ID"":20}"
783958220,5414094,2022-02-13T14:08:50Z,104,17240,3569,SuccessWebCPS,"{""Program Member ID"":3240305,""Acquired By"":false,""Old Status"":""Not in Program"",""New Status ID"":6,""Success"":true,""New Status"":""Attended"",""Old Status ID"":1}"
783958306,5414094,2022-02-13T14:09:16Z,104,17240,3569,SuccessWebCPS,"{""Program Member ID"":3240305,""Acquired By"":false,""Old Status"":""Attended"",""New Status ID"":6,""Success"":false,""New Status"":""Attended"",""Old Status ID"":6}"
783961924,5316669,2022-02-13T14:27:21Z,104,11614,2333,Nurture Automation,"{""Program Member ID"":3240306,""Acquired By"":false,""Old Status"":""Not in Program"",""New Status ID"":27,""Success"":false,""New Status"":""Member"",""Old Status ID"":26}"
```

对于部分或可恢复的检索，文件终结点支持范围为`bytes`的可选HTTP `Range`标头。 如果忽略此标头，则端点将返回整个文件。 有关使用`Range`标头的更多信息，请参阅[批量提取](bulk-extract.md)。

## 取消作业

要停止配置不正确或不必要的作业，请调用[取消导出活动作业](https://developer.adobe.com/marketo-apis/api/mapi#tag/Bulk-Export-Activities/operation/cancelExportActivitiesUsingPOST)终结点：

```http
POST /bulk/v1/activities/export/{exportId}/cancel.json
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

响应状态表示作业已取消。
