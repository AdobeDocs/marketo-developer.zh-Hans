---
title: 批量程序成员提取
feature: REST API
description: 使用Marketo批量程序成员提取REST API导出大型成员记录，以使用权限和字段元数据进行ETL、数据仓库存储和存档。
exl-id: 6e0a6bab-2807-429d-9c91-245076a34680
TQID: https://experienceleague.adobe.com/w4qaVTKSe0EORaSiURB6WbJXi29JUdEgfkb2dnfuVFw
product_v2: id: b27e5950-9033-45ac-9f86-eb22e567f615
feature_v2: id: e64968b2-4ee5-47f9-8cae-0588f184b9eb
role_v2: id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
topic_v2: id: a004cc84-67b9-4a33-a3a7-8ec7273ef4dc
source-git-commit: 3e6d310c5aec1a3435424fb122b71d825db5af0e
workflow-type: tm+mt
source-wordcount: 1026
ht-degree: 2%

---

# 批量程序成员提取

[批量程序成员提取终结点引用](https://developer.adobe.com/marketo-apis/api/mapi#tag/Bulk-Export-Program-Members)

批量程序成员提取REST API从Marketo中检索大量程序成员记录。 使用这些API可在Marketo和外部系统、ETL、数据仓库存储和归档之间持续交换数据。

## 权限

API用户必须具有具有“只读潜在客户”权限和/或“读写潜在客户”权限的角色。

## 描述

使用[Describe Program Member](https://developer.adobe.com/marketo-apis/api/mapi#tag/Program-Members/operation/describeProgramMemberUsingGET2)确定哪些字段可用并检索其元数据。 `name`属性包含REST API字段名。

```http
GET /rest/v1/programs/members/describe.json
```

```json
{
    "requestId": "f813#1791563c7cc",
    "result": [
        {
            "name": "API Program Membership",
            "description": "Map for API program membership fields",
            "createdAt": "2021-03-20T01:30:05Z",
            "updatedAt": "2021-03-20T01:30:05Z",
            "dedupeFields": [
                "leadId",
                "programId"
            ],
            "searchableFields": [
                [
                    "leadId"
                ],
                [
                    "myCustomField"
                ],
                [
                    "reachedSuccess"
                ],
                [
                    "statusName"
                ]
            ],
            "fields": [
                {
                    "name": "acquiredBy",
                    "displayName": "acquiredBy",
                    "dataType": "boolean",
                    "updateable": false,
                    "crmManaged": false
                },
                {
                    "name": "attendanceLikelihood",
                    "displayName": "attendanceLikelihood",
                    "dataType": "integer",
                    "updateable": false,
                    "crmManaged": false
                },
                {
                    "name": "createdAt",
                    "displayName": "createdAt",
                    "dataType": "datetime",
                    "updateable": false,
                    "crmManaged": false
                },
                {
                    "name": "isExhausted",
                    "displayName": "isExhausted",
                    "dataType": "boolean",
                    "updateable": false,
                    "crmManaged": false
                },
                {
                    "name": "leadId",
                    "displayName": "leadId",
                    "dataType": "integer",
                    "updateable": false,
                    "crmManaged": false
                },
                {
                    "name": "membershipDate",
                    "displayName": "membershipDate",
                    "dataType": "datetime",
                    "updateable": false,
                    "crmManaged": false
                },
                {
                    "name": "nurtureCadence",
                    "displayName": "nurtureCadence",
                    "dataType": "string",
                    "length": 4,
                    "updateable": false,
                    "crmManaged": false
                },
                {
                    "name": "program",
                    "displayName": "program",
                    "dataType": "string",
                    "length": 255,
                    "updateable": false,
                    "crmManaged": false
                },
                {
                    "name": "programId",
                    "displayName": "programId",
                    "dataType": "integer",
                    "updateable": false,
                    "crmManaged": false
                },
                {
                    "name": "reachedSuccess",
                    "displayName": "reachedSuccess",
                    "dataType": "boolean",
                    "updateable": false,
                    "crmManaged": false
                },
                {
                    "name": "reachedSuccessDate",
                    "displayName": "reachedSuccessDate",
                    "dataType": "datetime",
                    "updateable": false,
                    "crmManaged": false
                },
                {
                    "name": "registrationLikelihood",
                    "displayName": "registrationLikelihood",
                    "dataType": "integer",
                    "updateable": false,
                    "crmManaged": false
                },
                {
                    "name": "statusName",
                    "displayName": "statusName",
                    "dataType": "string",
                    "length": 255,
                    "updateable": false,
                    "crmManaged": false
                },
                {
                    "name": "statusReason",
                    "displayName": "statusReason",
                    "dataType": "string",
                    "length": 255,
                    "updateable": false,
                    "crmManaged": false
                },
                {
                    "name": "trackName",
                    "displayName": "trackName",
                    "dataType": "string",
                    "length": 255,
                    "updateable": false,
                    "crmManaged": false
                },
                {
                    "name": "updatedAt",
                    "displayName": "updatedAt",
                    "dataType": "datetime",
                    "updateable": false,
                    "crmManaged": false
                },
                {
                    "name": "waitlistPriority",
                    "displayName": "waitlistPriority",
                    "dataType": "integer",
                    "updateable": false,
                    "crmManaged": false
                },
                {
                    "name": "myCustomField",
                    "displayName": "myCustomField",
                    "dataType": "string",
                    "length": 255,
                    "updateable": true,
                    "crmManaged": false
                },
                {
                    "name": "registrationCode",
                    "displayName": "registrationCode",
                    "dataType": "string",
                    "length": 100,
                    "updateable": true,
                    "crmManaged": false
                },
                {
                    "name": "webinarUrl",
                    "displayName": "webinarUrl",
                    "dataType": "string",
                    "length": 2000,
                    "updateable": true,
                    "crmManaged": false
                }
            ]
        }
    ],
    "success": true
}
```

## 过滤器

项目成员导出支持多个筛选器选项。 当作业指定多个过滤器类型时，API会将这些类型与AND操作组合在一起。

每个作业都必须指定`programId`或`programIds`。 所有其他过滤器都是可选的。 `updatedAt`筛选器需要在所有订阅中不可用的基础结构。

<table>
  <tbody>
    <tr>
      <td>筛选器类型</td>
      <td>数据类型</td>
      <td>注释</td>
    </tr>
    <tr>
      <td>programId</td>
      <td>整数</td>
      <td>接受项目的ID。 作业会返回在作业开始处理时属于程序成员的所有可访问记录。使用<a href="https://developer.adobe.com/marketo-apis/api/asset#tag/Programs">获取程序</a>终结点检索程序ID。无法与programIds过滤器一起使用。</td>
    </tr>
    <tr>
      <td>programIds</td>
      <td>数组[整数]</td>
      <td>接受最多10个项目ID的数组。 作业会返回在作业开始处理时属于程序成员的所有可访问记录。附加字段“programId”作为第一个字段添加到导出文件中。 此字段标识从中提取项目成员资格记录的项目。使用<a href="https://developer.adobe.com/marketo-apis/api/asset#tag/Programs">获取程序</a>终结点检索程序ID。无法与programId筛选器一起使用。</td>
    </tr>
    <tr>
      <td>isExhausted</td>
      <td>布尔值</td>
      <td>接受一个布尔值，用于筛选已用完内容</a>的<a href="https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/email-marketing/drip-nurturing/using-engagement-programs/people-who-have-exhausted-content">人的计划成员资格记录。</td>
    </tr>
    <tr>
      <td>nurtureCadence</td>
      <td>字符串</td>
      <td>接受用于筛选给定培养节奏的项目成员资格记录的字符串。允许的值有：
        <ul>
          <li>pause — 节奏已暂停</li>
          <li>正常 — 节奏正常</li>
        </ul></td>
    </tr>
    <tr>
      <td>statusName</td>
      <td>Array[String]</td>
      <td>接受程序成员状态名称的数组。多个状态名是OR组合在一起的。具有此筛选器类型的作业将返回其程序成员状态与任何指定状态名匹配的所有可访问记录。可以使用默认状态和用户定义的状态名。如果statusNames筛选器与“programIds”筛选器一起使用，则会检查每个程序的成员资格记录，这些记录的状态与任何状态名相匹配。如果在任何程序中都找不到状态名称，则会返回“1003， Invalid Data（1003数据无效）”错误。
        <table>
          <tbody>
            <tr>
              <td>已参加</td>
              <td>按需参加</td>
              <td>已跳出</td>
            </tr>
            <tr>
              <td>已单击</td>
              <td>已联系</td>
              <td>已转换</td>
            </tr>
            <tr>
              <td>已参与</td>
              <td>填写表单</td>
              <td>受影响的</td>
            </tr>
            <tr>
              <td>已邀请</td>
              <td>成员</td>
              <td>无节目</td>
            </tr>
            <tr>
              <td>不在计划中</td>
              <td>在列表中</td>
              <td>已打开</td>
            </tr>
            <tr>
              <td>已注册</td>
              <td>注册中</td>
              <td>注册错误</td>
            </tr>
            <tr>
              <td>已发送</td>
              <td>已订阅</td>
              <td>取消订阅</td>
            </tr>
            <tr>
              <td>已查看</td>
              <td>已访问</td>
              <td>已访问展位</td>
            </tr>
            <tr>
              <td>轮候</td>
              <td>Web内容</td>
              <td></td>
            </tr>
          </tbody>
        </table></td>
    </tr>
    <tr>
      <td>更新时间*</td>
      <td>Date Range</td>
      <td>接受具有成员startAt和endAt的JSON对象。 startAt接受表示低水位线的日期时间，endAt接受表示高水位线的日期时间。 范围必须为31天或更少。 日期时间应采用ISO-8601格式，不带毫秒。具有此筛选器类型的作业将返回日期范围内最近更新的所有可访问记录。</td>
    </tr>
  </tbody>
</table>

某些订阅不支持此筛选器类型。 如果不可用，则创建导出程序成员作业终结点将返回`1035, Unsupported filter type for target subscription`。 联系Marketo支持以请求为您的订阅提供此功能。

## 选项

“创建导出程序成员作业”端点提供以下选项：

- 指定要包含在导出文件中的字段。
- 重命名导出的列标题。
- 指定导出文件格式。

| 参数 | 数据类型 | 必需 | 注释 |
| --- | --- | --- | --- |
| 字段 | 数组[字符串] | 是 | 字段参数接受字符串的JSON数组。 列出的字段包含在导出的文件中。 可以导出以下字段类型：`LeadCustom` `LeadProgram` MemberCustom `ProgramMember`。 使用REST API名称指定字段，可使用“描述潜在客户2”和/或“描述项目成员”端点检索该字段。 |
| columnHeaderName | 对象 | 否 | 包含字段和列标题名称的键值对的JSON对象。 键必须是导出作业中包含的字段的名称。 值是该字段的导出列标题的名称。 |
| 格式 | 字符串 | 否 | 接受以下内容之一：CSV、TSV、SSV。 如果设置，导出的文件将分别呈现为逗号分隔的值、制表符分隔的值或空格分隔的值文件。 如果未设置，则默认为CSV。 |

## 创建作业

使用[创建导出程序成员作业](https://developer.adobe.com/marketo-apis/api/mapi#tag/Bulk-Export-Program-Members/operation/createExportProgramMembersUsingPOST)端点定义导出作业。 指定包含程序ID和要导出的`fields`的`filter`。 您还可以指定`format`和`columnHeaderNames`。

```http
POST /bulk/v1/program/members/export/create.json
```

```json
{
   "format": "CSV",
   "fields": [
        "firstName",
        "lastName",
        "email",
        "membershipDate",
        "program",
        "statusName",
        "leadId",
        "reachedSuccess",
        "leadCustomField01",
        "leadCustomField02",
        "pMCustomField01",
        "pMCustomField02"
   ],
   "filter": {
      "programId":1044
   }
}
```

```json
{
    "requestId": "4d44#16f92734f6e",
    "result": [
        {
            "exportId": "b5ca52a9-5ecb-4966-b5a9-11659a8b4c2b",
            "format": "CSV",
            "status": "Created",
            "createdAt": "2020-01-11T02:33:48Z"
        }
    ],
    "success": true
}
```

响应将确认作业已创建，但导出不会自动启动。 将返回的`exportId`传递到[排入队列导出程序成员作业](https://developer.adobe.com/marketo-apis/api/mapi#tag/Bulk-Export-Program-Members/operation/enqueueExportProgramMembersUsingPOST)终结点以启动作业：

```http
POST /bulk/v1/program/members/export/{exportId}/enqueue.json
```

```json
{
    "requestId": "d70b#16f9273ae32",
    "result": [
        {
            "exportId": "b5ca52a9-5ecb-4966-b5a9-11659a8b4c2b",
            "format": "CSV",
            "status": "Queued",
            "createdAt": "2020-01-11T02:33:48Z",
            "queuedAt": "2020-01-11T02:34:13Z"
        }
    ],
    "success": true
}
```

排入队列的响应最初返回`Queued`状态。 导出插槽可用时，状态将更改为`Processing`。

## 轮询作业状态

您只能检索由同一API用户创建的作业的状态。

由于导出是异步运行的，请使用[获取导出程序成员作业状态](https://developer.adobe.com/marketo-apis/api/mapi#tag/Bulk-Export-Leads/operation/getExportLeadsStatusUsingGET)端点轮询其进度。 状态每60秒只更新一次，因此不要更频繁地轮询。

状态可以是`Created`、`Queued`、`Processing`、`Canceled`、`Completed`或`Failed`。

```http
GET /bulk/v1/program/members/export/{exportId}/status.json
```

```json
{
    "requestId": "9a40#16f9274d250",
    "result": [
        {
            "exportId": "b5ca52a9-5ecb-4966-b5a9-11659a8b4c2b",
            "format": "CSV",
            "status": "Processing",
            "createdAt": "2020-01-11T02:33:48Z",
            "queuedAt": "2020-01-11T02:34:13Z",
            "startedAt": "2020-01-11T02:35:19Z"
        }
    ],
    "success": true
}
```

此响应显示作业仍在处理中，因此文件不可用。 当作业状态更改为`Completed`时，文件已可供下载。

```json
{
    "requestId": "11ad1#16f9ff6da23",
    "result": [
        {
            "exportId": "1118dc83-273b-4d44-becb-4d212fece550",
            "format": "CSV",
            "status": "Completed",
            "createdAt": "2020-01-11T02:33:48Z",
            "queuedAt": "2020-01-11T02:34:13Z",
            "startedAt": "2020-01-11T02:35:19Z"
            "finishedAt": "2020-01-11T02:36:12Z",
            "numberOfRecords": 13,
            "fileSize": 1752,
            "fileChecksum": "sha256:b3c8e70e6e501cf1025e345a66b409d4fd07364c7da773cfa68a2b68ce1a7212"
        }
    ],
    "success": true
}
```

## 检索数据

要检索已完成的程序成员导出，请将`exportId`传递到[获取导出程序成员文件](https://developer.adobe.com/marketo-apis/api/mapi#tag/Bulk-Export-Program-Members/operation/getExportProgramMembersFileUsingGET)终结点。

端点会按照为作业配置的格式返回文件。 如果请求的程序成员字段不包含数据，则相应的导出字段包含`null`。

```http
GET /bulk/v1/program/members/export/{exportId}/file.json
```

```text
firstName,lastName,email,Member Date,Program,Status,Lead Id,Success,leadCustomField01,leadCustomField02,pMCustomField01,pMCustomField02
Meera,Reed,mree@housestark.com,2020-01-08T18:10:26Z,PMCF Program,On List,1789,false,Lead01_Value,Lead02_Value,PM01_Value,PM02_Value
Jon,Umber,jumb@housestark.com,2020-01-08T18:10:26Z,PMCF Program,On List,1790,false,Lead01_Value,Lead02_Value,PM01_Value,PM02_Value
Lyanna,Mormont,lmor@housestark.com,2020-01-08T18:10:26Z,PMCF Program,On List,1791,false,Lead01_Value,Lead02_Value,PM01_Value,PM02_Value
Rickon,Stark,rsta@housestark.com,2020-01-08T18:10:26Z,PMCF Program,On List,1792,false,Lead01_Value,Lead02_Value,PM01_Value,PM02_Value
Hodor,null,hodor@housestark.com,2020-01-08T18:10:26Z,PMCF Program,On List,1793,false,Lead01_Value,Lead02_Value,PM01_Value,PM02_Value
Osha,null,osha@housestark.com,2020-01-08T18:10:26Z,PMCF Program,On List,1794,false,Lead01_Value,Lead02_Value,PM01_Value,PM02_Value
Jojen,Reed,Jree@housestark.com,2020-01-08T18:10:26Z,PMCF Program,On List,1795,false,Lead01_Value,Lead02_Value,PM01_Value,PM02_Value
Rickard,Karstark,rkar@housestark.com,2020-01-08T18:10:26Z,PMCF Program,On List,1796,false,Lead01_Value,Lead02_Value,PM01_Value,PM02_Value
Maester,Luwin,mluw@housestark.com,2020-01-08T18:10:26Z,PMCF Program,On List,1797,false,Lead01_Value,Lead02_Value,PM01_Value,PM02_Value
Rodrik,Cassel,rcas@housestark.com,2020-01-08T18:10:26Z,PMCF Program,On List,1798,false,Lead01_Value,Lead02_Value,PM01_Value,PM02_Value
Jory,Cassel,jcas@housestark.com,2020-01-08T18:10:26Z,PMCF Program,On List,1799,false,Lead01_Value,Lead02_Value,PM01_Value,PM02_Value
Septa,Mordane,smor@housestark.com,2020-01-08T18:10:26Z,PMCF Program,On List,1800,false,Lead01_Value,Lead02_Value,PM01_Value,PM02_Value
```

对于部分或可恢复的检索，文件终结点支持范围类型为`bytes`的可选HTTP `Range`标头。 如果不设置标头，则端点将返回整个文件。 有关详细信息，请参阅[批量提取](bulk-extract.md)。

## 取消作业

要取消配置不正确或不再需要的作业，请调用[取消导出程序成员作业](https://developer.adobe.com/marketo-apis/api/mapi#tag/Bulk-Export-Program-Members/operation/cancelExportProgramMembersUsingPOST)终结点：

```http
POST /bulk/v1/program/members/export/{exportId}/cancel.json
```

```json
{
    "requestId": "bb4f#16f86727f89",
    "result": [
        {
            "exportId": "f0d3520c-3a60-4568-9e71-2e619d3805a4",
            "format": "CSV",
            "status": "Cancelled",
            "createdAt": "2020-01-07T21:47:35Z"
        }
    ],
    "success": true
}
```

响应状态表示作业已取消。
