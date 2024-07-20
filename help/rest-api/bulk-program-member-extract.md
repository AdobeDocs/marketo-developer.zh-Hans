---
title: 批量程序成员提取
feature: REST API
description: 批量处理成员数据提取。
exl-id: 6e0a6bab-2807-429d-9c91-245076a34680
source-git-commit: 66add4c38d0230c36d57009de985649bb67fde3e
workflow-type: tm+mt
source-wordcount: '1142'
ht-degree: 2%

---

# 批量程序成员提取

[批量程序成员提取终结点引用](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Bulk-Export-Program-Members)

REST API的批量程序成员提取集提供了一个用于从Marketo中检索大量程序成员记录的编程接口。 对于需要在Marketo与一个或多个外部系统之间持续交换数据的用例，这是推荐的界面，用于ETL、数据仓库存储和存档目的。

## 权限

批量程序成员提取API要求拥有的API用户具有只读潜在客户或读写潜在客户权限中的一个或两个角色。

## 描述

[描述计划成员](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Program-Members/operation/describeProgramMemberUsingGET2)是字段是否可用以及有关这些字段的元数据的主要真实来源。 `name`属性包含REST API名称。

```
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

项目群成员支持各种筛选器选项。 可以为一个作业指定多个过滤器类型，在这种情况下，它们将“与”组合在一起。 您必须指定`programId`或`programIds`筛选器。 所有其他过滤器都是可选的。 `updatedAt`筛选器需要其他基础架构组件，这些组件尚未转出到所有订阅。

<table>
  <tbody>
    <tr>
      <td>筛选器类型</td>
      <td>数据类型</td>
      <td>备注</td>
    </tr>
    <tr>
      <td>programId</td>
      <td>整数</td>
      <td>接受项目的ID。 作业返回所有可访问的记录，这些记录是作业开始处理时的程序成员。使用<a href="https://developer.adobe.com/marketo-apis/api/asset/#tag/Programs">获取程序</a>端点检索程序ID。无法与programIds过滤器一起使用。</td>
    </tr>
    <tr>
      <td>programIds</td>
      <td>数组[整数]</td>
      <td>接受最多10个项目ID的数组。 作业返回在作业开始处理时属于程序成员的所有可访问记录。附加字段“programId”将作为第一个字段添加到导出文件中。 此字段标识从提取程序成员资格记录的程序。使用<a href="https://developer.adobe.com/marketo-apis/api/asset/#tag/Programs">获取程序</a>端点检索程序ID。无法与programId筛选器一起使用。</td>
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
      <td>接受程序成员状态名称的数组。 多个状态名是OR组合在一起的。具有此筛选器类型的作业将返回其程序成员状态与任何指定状态名匹配的所有可访问记录。 可以使用默认状态和用户定义的状态名。如果statusNames筛选器与“programIds”筛选器一起使用，则会检查每个程序的成员资格记录，这些记录的状态与任何状态名相匹配。 如果在任何程序中都找不到状态名称，则会返回“1003， Invalid Data（1003数据无效）”错误。
        <table>
          <tbody>
            <tr>
              <td>已参加</td>
              <td>按需参加</td>
              <td>已退回</td>
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
              <td>会员</td>
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
              <td>退订</td>
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
      <td>日期范围</td>
      <td>接受具有成员startAt和endAt的JSON对象。 startAt接受表示低水位线的日期时间，endAt接受表示高水位线的日期时间。 范围必须为31天或更少。 日期时间应采用ISO-8601格式，不带毫秒。具有此筛选器类型的作业将返回日期范围内最近更新的所有可访问的记录。</td>
    </tr>
  </tbody>
</table>

过滤器类型不适用于某些订阅。 如果您的订阅不可用，则在调用“创建导出程序成员作业”终结点（“1035，目标订阅的过滤器类型不受支持”）时会收到一个错误。 客户可以联系Marketo支持部门以在其订阅中启用此功能。

## 选项

创建导出程序成员作业端点提供了多个格式选项。 利用这些选项，用户能够：

- 指定要包含在导出文件中的字段
- 重命名这些字段的列标题
- 指定导出文件的格式

| 参数 | 数据类型 | 必需 | 备注 |
|---|---|---|---|
| 字段 | 数组[字符串] | 是 | 字段参数接受字符串的JSON数组。 列出的字段包含在导出的文件中。 可以导出以下字段类型：`LeadCustom` `LeadProgram` MemberCustom `ProgramMember`。 使用其REST API名称指定一个字段，可使用“描述潜在客户2”和/或“描述项目成员”端点检索该字段。 |
| columnHeaderName | 对象 | 否 | 包含字段和列标题名称的键值对的JSON对象。 键必须是导出作业中包含的字段的名称。 值是该字段的导出列标题的名称。 |
| 格式 | 字符串 | 否 | 接受以下内容之一：CSV、TSV、SSV。 如果设置，导出的文件将分别呈现为逗号分隔的值、制表符分隔的值或空格分隔的值文件。 如果未设置，则默认为CSV。 |


## 创建作业

作业的参数是在使用[创建导出程序成员作业](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Bulk-Export-Program-Members/operation/createExportProgramMembersUsingPOST)终结点开始导出之前定义的。 我们必须定义包含项目ID的`filter`以及导出所需的`fields`。 或者，我们可以定义文件的`format`和`columnHeaderNames`。

```
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

这将返回一个状态响应，指示作业已创建。 作业已定义和创建，但尚未开始。 为此，必须使用创建状态响应中的`exportId`调用[排入队列导出程序成员作业](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Bulk-Export-Program-Members/operation/enqueueExportProgramMembersUsingPOST)终结点：

```
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

这将以初始`status`的“已排队”作出响应，之后，当存在可用的导出插槽时，它将被设置为“正在处理”。

## 轮询作业状态

注意：只能检索由同一API用户创建的作业的状态。

由于这是异步端点，因此创建作业后，必须轮询其状态以确定其进度。 使用[获取导出程序成员作业状态](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Bulk-Export-Leads/operation/getExportLeadsStatusUsingGET)终结点进行轮询。 状态仅每60秒更新一次，因此不建议使用低于此值的轮询频率，并且在几乎所有情况下，仍然会过度轮询。 状态字段可以使用以下任意一项进行响应：“已创建”、“已排队”、“正在处理”、“已取消”、“已完成”、“失败”。

```
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

状态终结点响应指示作业仍在处理，因此文件尚不可检索。 作业`status`更改为“已完成”后，便可以下载。

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

要检索已完成程序成员导出的文件，只需使用`exportId`调用[获取导出程序成员文件](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Bulk-Export-Program-Members/operation/getExportProgramMembersFileUsingGET)终结点。

响应包含以作业配置方式格式化的文件。 端点使用文件的内容进行响应。 如果请求的程序成员字段为空（不包含数据），则`null`将置于导出文件中的相应字段中。

```
GET /bulk/v1/program/members/export/{exportId}/file.json
```

```
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

为了支持提取数据的部分检索和恢复友好检索，文件端点可以选择性地支持类型字节的HTTP标头范围。 如果未设置标头，则将返回所有内容。 您可以阅读有关将Range标头与Marketo [批量提取](bulk-extract.md)结合使用的更多信息。

## 取消作业

如果作业配置不正确或变得不必要，可以使用[取消导出程序成员作业](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Bulk-Export-Program-Members/operation/cancelExportProgramMembersUsingPOST)端点轻松取消该作业：

```
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

此响应包含`status`，指示作业已取消。
