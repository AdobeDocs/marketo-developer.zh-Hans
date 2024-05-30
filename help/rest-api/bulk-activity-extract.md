---
title: "批量活动提取"
feature: REST API
description: “从Marketo批量处理活动数据。”
source-git-commit: 8c1ffb6db05da49e7377b8345eeb30472ad9b78b
workflow-type: tm+mt
source-wordcount: '1381'
ht-degree: 1%

---


# 批量活动提取

[批量活动提取端点引用](https://developer.adobe.com/marketo-apis/api/mapi/)

REST API的批量活动提取集提供了一个编程接口，用于从Marketo中检索大量活动数据。  对于不要求低延迟，并且必须从Marketo中传输大量活动数据的情况，例如CRM集成、ETL、数据仓库存储和数据存档。

## 权限

批量活动提取API要求API用户具有“只读活动”或“读写活动”权限。

## 过滤器

<table>
  <tbody>
    <tr>
      <td>筛选器类型</td>
      <td>数据类型</td>
      <td>必需</td>
      <td>备注</td>
    </tr>
    <tr>
      <td>createdat</td>
      <td>日期范围</td>
      <td>是</td>
      <td>接受具有成员startAt和endAt的JSON对象。 startAt接受表示低水位线的日期时间，endAt接受表示高水位线的日期时间。 范围必须为31天或更短。具有此筛选器类型的作业将返回在日期范围内创建的所有可访问的记录。日期时间应采用ISO-8601格式，不带毫秒。</td>
    </tr>
    <tr>
      <td>activityTypeIds</td>
      <td>数组[整数]</td>
      <td>否</td>
      <td>接受具有一个成员activityTypeIds的JSON对象。 该值必须是一个整数数组，对应于所需的活动类型。不支持“删除潜在客户”活动(使用 <a href="https://developer.adobe.com/marketo-apis/api/mapi/#tag/Activities/operation/getDeletedLeadsUsingGET">获取已删除的潜在客户</a>终结点)。使用<a href="https://developer.adobe.com/marketo-apis/api/mapi/#tag/Activities/operation/getActivitiesPagingTokenUsingGET">获取活动类型</a>端点。</td>
    </tr>
    <tr>
      <td>primaryAttributeValueIds</td>
      <td>数组[整数]</td>
      <td>否</td>
      <td>接受具有一个成员primaryAttributeValueIds的JSON对象。 该值是一个ID数组，其中指定要过滤的主属性。 最多可以指定50个id。这些id是潜在客户字段或资产的唯一标识符，可以通过调用相应的REST API端点进行检索。 例如，要筛选“填写表单”活动的特定表单，请将表单名称传递给 <a href="https://developer.adobe.com/marketo-apis/api/asset/#tag/Forms/operation/getLpFormByNameUsingGET">按名称获取表单</a> 端点以检索表单ID。以下是支持主属性过滤的活动类型列表。
        <table>
          <tbody>
            <tr>
              <td>活动类型</td>
              <td>主要属性值ID</td>
              <td>检索端点</td>
              <td>资产组</td>
            </tr>
            <tr>
              <td>更改数据值</td>
              <td>商机字段ID</td>
              <td><a href="https://developer.adobe.com/marketo-apis/api/mapi/#tag/Leads/operation/describeUsingGET_2">描述潜在客户</a></td>
              <td>属性名称</td>
            </tr>
            <tr>
              <td>更改得分</td>
              <td>商机字段ID</td>
              <td><a href="https://developer.adobe.com/marketo-apis/api/mapi/#tag/Leads/operation/describeUsingGET_2">描述潜在客户</a></td>
              <td>属性名称</td>
            </tr>
            <tr>
              <td>进程中的更改状态</td>
              <td>项目ID</td>
              <td><a href="https://developer.adobe.com/marketo-apis/api/asset/#tag/Programs/operation/getProgramByNameUsingGET">按名称获取计划</a></td>
              <td>营销计划</td>
            </tr>
            <tr>
              <td>添加到列表</td>
              <td>静态列表ID</td>
              <td><a href="https://developer.adobe.com/marketo-apis/api/asset/#tag/Static-Lists/operation/getStaticListByNameUsingGET">按名称获取静态列表</a></td>
              <td>静态列表</td>
            </tr>
            <tr>
              <td>从列表中删除</td>
              <td>静态列表ID</td>
              <td><a href="https://developer.adobe.com/marketo-apis/api/asset/#tag/Static-Lists/operation/getStaticListByNameUsingGET">按名称获取静态列表</a></td>
              <td>静态列表</td>
            </tr>
            <tr>
              <td>填写表单</td>
              <td>表单ID</td>
              <td><a href="https://developer.adobe.com/marketo-apis/api/asset/#tag/Forms/operation/getLpFormByNameUsingGET">按名称获取表单</a></td>
              <td>Web窗体</td>
            </tr>
          </tbody>
        </table>
        使用primaryAttributeValueIds时，activityTypeIds过滤器必须存在，并且仅包含与相应资产组匹配的活动ID。例如：如果您正在筛选Web窗体资产，则activityTypeIds中仅允许“填写表单”活动类型ID。示例请求正文：{"filter"：{"createdAt"：{"startAt"： "2021-07-01T23:59:59-00:00”，“endAt”：“2021-07-02T23:59:59-00:00"}，"activityTypeIds"：[2]，"primaryAttributeValueIds" ： [16,102,95,8]}}primaryAttributeValueIds和primaryAttributeValues不能一起使用。</td>
    </tr>
    <tr>
      <td>primaryAttributeValue</td>
      <td>Array[String]</td>
      <td>否</td>
      <td>接受具有一个成员primaryAttributeValues的JSON对象。 值是一个名称数组，其中指定要过滤的主属性。 最多可以指定50个名称。这些名称是潜在客户字段或资产的唯一标识符，可通过调用相应的REST API端点进行检索。 例如，要筛选“填写表单”活动的特定表单，请将表单ID传递给 <a href="https://developer.adobe.com/marketo-apis/api/asset/#tag/Sales-Persons/operation/describeUsingGET_5">按ID获取表单</a> 端点以检索表单名称。以下是支持主属性过滤的活动类型列表。
        <table>
          <tbody>
            <tr>
              <td>活动类型</td>
              <td>主要属性值</td>
              <td>检索端点</td>
              <td>资产组</td>
            </tr>
            <tr>
              <td>更改数据值</td>
              <td>潜在客户字段displayName</td>
              <td><a href="https://developer.adobe.com/marketo-apis/api/mapi/#tag/Leads/operation/describeUsingGET_2">描述潜在客户</a></td>
              <td>属性名称</td>
            </tr>
            <tr>
              <td>更改得分</td>
              <td>潜在客户字段displayName</td>
              <td><a href="https://developer.adobe.com/marketo-apis/api/mapi/#tag/Leads/operation/describeUsingGET_2">描述潜在客户</a></td>
              <td>属性名称</td>
            </tr>
            <tr>
              <td>进程中的更改状态</td>
              <td>项目名称</td>
              <td><a href="https://developer.adobe.com/marketo-apis/api/asset/#tag/Programs/operation/getProgramByIdUsingGET">按ID获取计划</a></td>
              <td>营销计划</td>
            </tr>
            <tr>
              <td>添加到列表</td>
              <td>静态列表名称</td>
              <td><a href="https://developer.adobe.com/marketo-apis/api/asset/#tag/Static-Lists/operation/getStaticListByIdUsingGET">按ID获取静态列表</a></td>
              <td>静态列表</td>
            </tr>
            <tr>
              <td>从列表中删除</td>
              <td>静态列表名称</td>
              <td><a href="https://developer.adobe.com/marketo-apis/api/asset/#tag/Static-Lists/operation/getStaticListByIdUsingGET">按ID获取静态列表</a></td>
              <td>静态列表</td>
            </tr>
            <tr>
              <td>填写表单</td>
              <td>表单名称</td>
              <td><a href="https://developer.adobe.com/marketo-apis/api/asset/#tag/Sales-Persons/operation/describeUsingGET_5">按ID获取表单</a></td>
              <td>Web窗体</td>
            </tr>
          </tbody>
        </table>
        请注意，您必须使用“&lt;<em>项目</em>&gt;.&lt;<em>资产</em>&gt;”表示法，用于为以下资产组唯一指定名称：营销计划、静态列表、Web窗体。例如：例如，位于名为“GL_OP_ALL_2021”的计划下且名称为“MPS Outbound”的表单将指定为“GL_OP_ALL_2021.MPS Outbound”。示例请求正文：{"filter"：{"createdAt"：{"startAt"： "2021-07-01T2 3:59:59-00:00”，“endAt”：“2021-07-02T23:59:59-00:00"}，"activityTypeIds"：[2]，"primaryAttributeValues"：["GL_OP_ALL_2021.MPS Outbound"]}}使用primaryAttributeValues时，必须存在activityTypeIds过滤器，并且仅包含与相应资产组匹配的活动ID。 例如，如果您筛选Web表单资产，则activityTypeIds.primaryAttributeValues中仅允许“填写表单”活动类型ID，而primaryAttributeValueIds不能一起使用。</td>
    </tr>
  </tbody>
</table>

## 选项

| 参数 | 数据类型 | 必需 | 备注 |
|---|---|---|---|
| 筛选 | 数组[对象] | 是 | 接受过滤器数组。 数组中必须正好包含一个createdAt过滤器。 可包含可选的activityTypeIds过滤器。这些过滤器将应用于可访问的活动集，并且导出作业将返回生成的活动集。 |
| 格式 | 字符串 | 否 | 接受以下项之一：CSV、TSV、SSV如果设置，导出的文件将分别呈现为逗号分隔值、制表符分隔值或空格分隔值文件。如果未设置，则将默认为CSV。 |
| columnHeaderName | 对象 | 否 | 包含字段和列标题名称的键值对的JSON对象。 键必须是导出作业中包含的字段的名称。 值是该字段的导出列标题的名称。 |
| 字段 | 数组[字符串] | 否 | 包含字段值的可选字符串数组。 列出的字段包含在导出的文件中。默认情况下，返回以下字段： `marketoGUIDleadId` `activityDate` `activityTypeId` `campaignId` `primaryAttributeValueId` `primaryAttributeValueattributes`，此参数可用于通过从上述列表中指定子集来减少返回的字段数。示例：&quot;fields&quot;： [&quot;leadId&quot;、&quot;activityDate&quot;、&quot;activityTypeId&quot;]可以指定附加字段“actionResult”以包含活动操作（“succeeded”、“skipped”或“failed”）。 |


## 创建作业

要导出记录，必须先定义作业和要检索的记录集。  使用创建作业 [创建导出活动作业](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Bulk-Export-Activities/operation/createExportActivitiesUsingPOST) 端点。  导出活动时，可以应用两个主要过滤器： `createdAt`，始终是必需的，并且 `activityTypeIds`，可选。  createdAt过滤器用于定义创建活动的日期范围，使用 `startAt` 和 `endAt` 参数，两者都是日期时间字段，分别表示允许的最早创建日期和允许的最晚创建日期。  您也可以选择仅过滤某些类型的活动，使用 `activityTypeIds` 筛选。  这对于删除与用例无关的结果非常有用。

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

作业现在处于“已创建”状态，但尚未在处理队列中。  要将其放入队列以便开始处理，我们必须调用 [将导出活动作业排入队列](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Bulk-Export-Activities/operation/enqueueExportActivitiesUsingPOST) 终结点使用创建状态响应中的exportId。

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

现在，状态为报告作业已排队。  当工作人员可用于此作业时，状态将切换为“正在处理”，作业将开始从Marketo聚合记录。

## 轮询作业状态

只能检索同一API用户创建的作业的作业状态。

Marketo的批量活动提取是一个异步端点，因此必须轮询作业状态以确定作业何时完成。  使用轮询 [获取导出活动作业状态](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Bulk-Export-Activities/operation/getExportActivitiesStatusUsingGET) 如下所示的端点：

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
- 已失败

## 检索数据

作业完成后，使用 [获取导出活动文件](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Bulk-Export-Activities/operation/getExportActivitiesFileUsingGET) 端点。

```
GET /bulk/v1/activities/export/{exportId}/file.json
```

响应包含以作业配置方式格式化的文件。 端点使用文件的内容进行响应。

如果请求的潜在客户字段为空（不包含数据）， `then null` 放置在导出文件的相应字段中。  在以下示例中，返回的活动的campaignId字段为空。

```json
marketoGUID,leadId,activityDate,activityTypeId,campaignId,primaryAttributeValueId,primaryAttributeValue,attributes
783957693,5414087,2022-02-13T14:06:20Z,104,8497,1670,MembershipTest1,"{""Reason"":""Changed by Smart Campaign MembershipTestCampaignStepChoice.MembershipTestCampaignStepChoiceSetUp action Change Data Value"",""Program Member ID"":3240303,""Acquired By"":true,""Old Status"":""Not in Program"",""New Status ID"":21,""Success"":false,""New Status"":""On List"",""Old Status ID"":20}"
783958220,5414094,2022-02-13T14:08:50Z,104,17240,3569,SuccessWebCPS,"{""Program Member ID"":3240305,""Acquired By"":false,""Old Status"":""Not in Program"",""New Status ID"":6,""Success"":true,""New Status"":""Attended"",""Old Status ID"":1}"
783958306,5414094,2022-02-13T14:09:16Z,104,17240,3569,SuccessWebCPS,"{""Program Member ID"":3240305,""Acquired By"":false,""Old Status"":""Attended"",""New Status ID"":6,""Success"":false,""New Status"":""Attended"",""Old Status ID"":6}"
783961924,5316669,2022-02-13T14:27:21Z,104,11614,2333,Nurture Automation,"{""Program Member ID"":3240306,""Acquired By"":false,""Old Status"":""Not in Program"",""New Status ID"":27,""Success"":false,""New Status"":""Member"",""Old Status ID"":26}"
```

为了支持提取数据的部分和恢复友好检索，文件端点可以选择支持HTTP标头 `Range` 类型 `bytes`.  如果未设置标头，则将返回所有内容。  您可以阅读有关在Marketo中使用范围标头的更多信息 [批量提取](bulk-extract.md).

## 取消作业

如果作业配置不正确或变得不必要，可以使用轻松取消 [取消导出活动作业](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Bulk-Export-Activities/operation/cancelExportActivitiesUsingPOST) 端点：

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
