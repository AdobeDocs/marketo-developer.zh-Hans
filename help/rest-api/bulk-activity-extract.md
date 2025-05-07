---
title: 批量活动提取
feature: REST API
description: 从Marketo批量处理活动数据。
exl-id: 6bdfa78e-bc5b-4eea-bcb0-e26e36cf6e19
source-git-commit: a5b855691e7fb9e628e2d68fd14a8a6c689d6750
workflow-type: tm+mt
source-wordcount: '1332'
ht-degree: 3%

---

# 批量活动提取

[批量活动提取终结点引用](https://developer.adobe.com/marketo-apis/api/mapi/)

REST API的批量活动提取集提供了一个编程接口，用于从Marketo中检索大量活动数据。  对于不需要低延迟，并且必须从Marketo中传输大量活动数据的情况，例如CRM集成、ETL、数据仓库存储和数据存档。

## 权限

批量活动提取API要求API用户具有“只读活动”或“读写活动”权限。

## 过滤器

| 筛选器类型 | 数据类型 | 必需 | 注释 |
| --- | --- | --- | --- |
| createdat | Date Range | 是 | 接受具有成员`startAt`和`endAt`的JSON对象。 `startAt`接受表示低水位线的日期时间，`endAt`接受表示高水位线的日期时间。 范围必须为31天或更少。 具有此筛选器类型的作业将返回在日期范围内创建的所有可访问记录。 日期时间应采用ISO-8601格式，不带毫秒。 |
| activityTypeIds | 数组\[整数\] | 否 | 接受具有一个成员`activityTypeIds`的JSON对象。 该值必须是一个整数数组，对应于所需的活动类型。 不支持“删除潜在客户”活动（请改用[获取已删除的潜在客户](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Activities/operation/getDeletedLeadsUsingGET)端点）。 使用[获取活动类型终结点](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Activities/operation/getAllActivityTypesUsingGET)检索活动类型ID。 |
| [primaryAttributeValueIds](#primaryattributevalueids-options) | 数组\[整数\] | 否 | 接受具有一个成员`primaryAttributeValueIds`的JSON对象。 该值是一个ID数组，用于指定要过滤的主属性。 最多可以指定50个ID。 ID是潜在客户字段或资产的唯一标识符，可以通过调用相应的REST API端点进行检索。 例如，要筛选“填写表单”活动的特定表单，请将表单名称传递到[按名称获取表单](https://developer.adobe.com/marketo-apis/api/asset/#tag/Forms/operation/getLpFormByNameUsingGET)端点以检索表单ID。 以下是支持主属性过滤的活动类型列表。 |
| [primaryAttributeValues](#primaryattributevalues-options) | 数组\[字符串\] | 否 | 接受具有一个成员`primaryAttributeValues`的JSON对象。 值是一个名称数组，用于指定要过滤的主属性。 最多可以指定50个名称。 这些名称是潜在客户字段或资产的唯一标识符，可通过调用相应的REST API端点进行检索。 例如，要筛选“填写表单”活动的特定表单，请将表单ID传递给[按ID获取表单](https://developer.adobe.com/marketo-apis/api/asset/#tag/Sales-Persons/operation/describeUsingGET_5)端点以检索表单名称。 以下是支持主属性过滤的活动类型列表。 |

### primaryAttributeValueIds选项 {#primaryattributevalueids-options}

| 活动类型 | 主要属性值ID | 检索端点 | 资产组 |
| --- | --- | --- | --- |
| 更改数据值 | 商机字段ID | [描述潜在客户](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Leads/operation/describeUsingGET_2) | 属性名称 |
| 更改得分 | 商机字段ID | [描述潜在客户](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Leads/operation/describeUsingGET_2) | 属性名称 |
| 进程中的更改状态 | 项目ID | [按名称获取计划](https://developer.adobe.com/marketo-apis/api/asset/#tag/Programs/operation/getProgramByNameUsingGET) | 营销计划 |
| 添加到 List | 静态列表ID | [按名称获取静态列表](https://developer.adobe.com/marketo-apis/api/asset/#tag/Static-Lists/operation/getStaticListByNameUsingGET) | 静态列表 |
| 从列表中移除 | 静态列表ID | [按名称获取静态列表](https://developer.adobe.com/marketo-apis/api/asset/#tag/Static-Lists/operation/getStaticListByNameUsingGET) | 静态列表 |
| 填写表单 | 表单ID | [按名称获取表单](https://developer.adobe.com/marketo-apis/api/asset/#tag/Forms/operation/getLpFormByNameUsingGET) | Web窗体 |

使用`primaryAttributeValueIds`时，`activityTypeIds`过滤器必须存在，并且只包含与相应资产组匹配的活动ID。 例如，如果您正在筛选Web窗体资源，则`activityTypeIds`中仅允许“填写窗体”活动类型ID。

示例请求正文：

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
| 更改数据值 | 潜在客户字段displayName | [描述潜在客户](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Leads/operation/describeUsingGET_2) | 属性名称 |
| 更改得分 | 潜在客户字段displayName | [描述潜在客户](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Leads/operation/describeUsingGET_2) | 属性名称 |
| 进程中的更改状态 | 项目名称 | [按Id获取计划](https://developer.adobe.com/marketo-apis/api/asset/#tag/Programs/operation/getProgramByIdUsingGET) | 营销计划 |
| 添加到 List | 静态列表名称 | [按Id获取静态列表](https://developer.adobe.com/marketo-apis/api/asset/#tag/Static-Lists/operation/getStaticListByIdUsingGET) | 静态列表 |
| 从列表中移除 | 静态列表名称 | [按Id获取静态列表](https://developer.adobe.com/marketo-apis/api/asset/#tag/Static-Lists/operation/getStaticListByIdUsingGET) | 静态列表 |
| 填写表单 | 表单名称 | [按Id获取表单](https://developer.adobe.com/marketo-apis/api/asset/#tag/Sales-Persons/operation/describeUsingGET_5) | Web窗体 |

请注意，您必须使用“&lt;<em>程序</em>>”。&lt;<em>资产</em>>表示法，用于指定以下资产组的名称：营销计划、静态列表、Web窗体。 例如，名为“MPS Outbound”的表单位于名为“GL_OP_ALL_2021”的程序下，该表单将指定为“GL_OP_ALL_2021.MPS Outbound”。

示例请求正文：

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

使用`primaryAttributeValues`时，`activityTypeIds`过滤器必须存在，并且只包含与相应资产组匹配的活动ID。 例如，如果您正在筛选Web窗体资源，则`activityTypeIds`中仅允许“填写表单”活动类型ID。 `primaryAttributeValues`和`primaryAttributeValueIds`不能一起使用。

## 选项

| 参数 | 数据类型 | 必需 | 注释 |
|---|---|---|---|
| filter | 数组[对象] | 是 | 接受过滤器数组。 数组中必须正好包含一个`createdAt`筛选器。 可能包括可选的`activityTypeIds`筛选器。 过滤器将应用于可访问的活动集，并且导出作业会返回生成的活动集。 |
| 格式 | 字符串 | 否 | 接受以下项之一：CSV、TSV、SSV导出的文件将分别呈现为逗号分隔的值、制表符分隔的值或空格分隔的值文件（如果设置）。 如果未设置，则默认为CSV。 |
| columnHeaderName | 对象 | 否 | 包含字段和列标题名称的键值对的JSON对象。 键必须是导出作业中包含的字段的名称。 值是该字段的导出列标题的名称。 |
| 字段 | 数组[字符串] | 否 | 包含字段值的可选字符串数组。 列出的字段包含在导出的文件中。 默认情况下，将返回以下字段： <ul><li>`marketoGUIDleadId`</li><li> `activityDate` </li><li>`activityTypeId` </li><li>`campaignId`</li><li> `primaryAttributeValueId` </li><li>`primaryAttributeValue`</li><li> `attributes`</li></ul>。此参数可用于通过从上述列表中指定子集来减少返回的字段数：`"fields": ["leadId", "activityDate", "activityTypeId"]`。 可以指定附加字段`actionResult`以包括活动操作： `("succeeded", "skipped", or "failed")`。 |


## 创建作业

要导出记录，必须先定义作业和要检索的记录集。  使用[创建导出活动作业](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Bulk-Export-Activities/operation/createExportActivitiesUsingPOST)终结点创建作业。  导出活动时，有两个可应用的主要筛选器：`createdAt`和`activityTypeIds`，前者始终是必需的，后者是可选的。  `createdAt`筛选器用于使用`startAt`和`endAt`参数定义创建活动的日期范围，这两个参数都是日期时间字段，分别表示允许的最早创建日期和允许的最晚创建日期。  您还可以选择使用`activityTypeIds`筛选器仅筛选某些类型的活动。  这对于删除与用例无关的结果非常有用。

```
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

作业现在处于“已创建”状态，但尚未在处理队列中。  若要将其放入队列中以便开始处理，请使用创建状态响应中的exportId调用[排入队列导出活动作业](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Bulk-Export-Activities/operation/enqueueExportActivitiesUsingPOST)端点。

```
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

现在，状态为报告作业已排队。  当工作人员可用于此作业时，状态将切换为“正在处理”，并且作业开始从Marketo聚合记录。

## 轮询作业状态

只能检索同一API用户创建的作业的作业状态。

Marketo的批量活动提取是一个异步端点，因此必须轮询作业状态以确定作业何时完成。  使用[获取导出活动作业状态](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Bulk-Export-Activities/operation/getExportActivitiesStatusUsingGET)终结点进行轮询，如下所示：

```
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

状态字段可以使用以下值之一进行响应：

- 已创建
- 已排队
- 正在处理
- 已取消
- 已完成
- 失败

## 检索数据

作业完成后，使用[获取导出活动文件](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Bulk-Export-Activities/operation/getExportActivitiesFileUsingGET)端点检索数据。

```
GET /bulk/v1/activities/export/{exportId}/file.json
```

响应包含以作业配置方式格式化的文件。 端点使用文件的内容进行响应。

如果请求的潜在客户字段为空（不包含数据），则`then null`将被放置在导出文件中的相应字段中。  在以下示例中，返回的活动的`campaignId`字段为空。

```json
marketoGUID,leadId,activityDate,activityTypeId,campaignId,primaryAttributeValueId,primaryAttributeValue,attributes
783957693,5414087,2022-02-13T14:06:20Z,104,8497,1670,MembershipTest1,"{""Reason"":""Changed by Smart Campaign MembershipTestCampaignStepChoice.MembershipTestCampaignStepChoiceSetUp action Change Data Value"",""Program Member ID"":3240303,""Acquired By"":true,""Old Status"":""Not in Program"",""New Status ID"":21,""Success"":false,""New Status"":""On List"",""Old Status ID"":20}"
783958220,5414094,2022-02-13T14:08:50Z,104,17240,3569,SuccessWebCPS,"{""Program Member ID"":3240305,""Acquired By"":false,""Old Status"":""Not in Program"",""New Status ID"":6,""Success"":true,""New Status"":""Attended"",""Old Status ID"":1}"
783958306,5414094,2022-02-13T14:09:16Z,104,17240,3569,SuccessWebCPS,"{""Program Member ID"":3240305,""Acquired By"":false,""Old Status"":""Attended"",""New Status ID"":6,""Success"":false,""New Status"":""Attended"",""Old Status ID"":6}"
783961924,5316669,2022-02-13T14:27:21Z,104,11614,2333,Nurture Automation,"{""Program Member ID"":3240306,""Acquired By"":false,""Old Status"":""Not in Program"",""New Status ID"":27,""Success"":false,""New Status"":""Member"",""Old Status ID"":26}"
```

为了支持对提取的数据进行部分检索和便于恢复检索，文件终结点可以选择性地支持类型为`bytes`的HTTP标头`Range`。  如果未设置标头，则将返回所有内容。  您可以阅读有关将Range标头与Marketo [批量提取](bulk-extract.md)结合使用的更多信息。

## 取消作业

如果作业配置不正确或变得不必要，可以使用[取消导出活动作业](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Bulk-Export-Activities/operation/cancelExportActivitiesUsingPOST)端点轻松取消该作业：

```
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

此响应的状态表示作业已取消。
