---
title: 程序
feature: REST API, Programs
description: 创建和编辑项目信息。
exl-id: 30700de2-8f4a-4580-92f2-7036905deb80
source-git-commit: 66add4c38d0230c36d57009de985649bb67fde3e
workflow-type: tm+mt
source-wordcount: '843'
ht-degree: 1%

---

# 程序

[程序终结点引用](https://developer.adobe.com/marketo-apis/api/asset/#tag/Programs)

项目是Marketo营销活动的核心组织组件。 它们可以是大多数类型资产的父级，并允许在单个营销计划的上下文中跟踪成员资格和商机的成功。 程序可以是除LP、电子邮件模板和文件之外的所有类型记录的父级。

## 程序类型

Marketo中有五种核心类型的程序：

- 默认
- 活动
- 网络研讨会活动
- 参与
- 电子邮件

参与计划可能是其他类型的计划的父级，而默认、事件和网络研讨会事件可能只是电子邮件计划的父级。

项目始终有一个渠道。它们从创建它们的渠道中派生出可能的设置项目成员状态，可以通过“获取渠道API”进行检索。 一个节目也可以具有一组相关的标签。 标记是可自定义的字段，可将其配置为任何给定类型项目的可选或必填字段，这些项目具有从Marketo Admin中配置的列表中选择的值。

## 查询

程序遵循资产查询的标准模式，并提供按标记类型和值查询的附加选项。 可以使用[获取标记类型](https://developer.adobe.com/marketo-apis/api/asset/#tag/Tags/operation/getTagTypesUsingGET)检索可用的标记和值。

### 按Id

[按ID](https://developer.adobe.com/marketo-apis/api/asset/#tag/Sales-Persons/operation/describeUsingGET_5)获取程序终结点需要`id`路径参数。

可以在UI中从程序的URL获取程序ID，其中URL类似于`https://app-\*\*\*.marketo.com/#PG1001A1`。 在此URL中，`id`为1001。 它始终介于URL中的第一组字母和第二组字母之间。

```
GET /rest/asset/v1/program/{id}.json
```

```json
{
    "success": true,
    "warnings": [],
    "errors": [],
    "requestId": "948f#14db037ec71",
    "result": [
        {
            "id": 1107,
            "name": "AAA2QueryProgramName",
            "description": "AssetAPI: getProgram tests",
            "createdAt": "2015-05-21T22:45:13Z+0000",
            "updatedAt": "2015-05-21T22:45:13Z+0000",
            "url": "https://app-devlocal1.marketo.com/#PG1107A1",
            "type": "Default",
            "channel": "Online Advertising",
            "folder": {
                "type": "Folder",
                "value": 1910,
                "folderName": "ProgramQueryTestFolder"
            },
            "status": "",
            "workspace": "Default",
            "tags": [
                {
                    "tagType": "AAA1 Required Tag Type",
                    "tagValue": "AAA1 RT1"
                }
            ],
            "costs": null,
            "headStart": false
        }
    ]
}
```

### 按名称

[按名称](https://developer.adobe.com/marketo-apis/api/asset/)获取程序终结点需要`name`查询参数。 可选的布尔查询参数是`includeTags`和`includeCosts`，这两个参数分别用于返回程序标记和程序成本。

```
GET /rest/asset/v1/program/byName.json?name=TestProgramName&includeTags=true
```

```json
{
    "success": true,
    "warnings": [],
    "errors": [],
    "requestId": "16026#14db03e070c",
    "result": [
        {
            "id": 1107,
            "name": "AAA2QueryProgramName",
            "description": "AssetAPI: getProgram tests",
            "createdAt": "2015-05-21T22:45:13Z+0000",
            "updatedAt": "2015-05-21T22:45:13Z+0000",
            "url": "https://app-devlocal1.marketo.com/#PG1107A1",
            "type": "Default",
            "channel": "Online Advertising",
            "folder": {
                "type": "Folder",
                "value": 1910,
                "folderName": "ProgramQueryTestFolder"
            },
            "status": "",
            "workspace": "Default",
            "tags": [
                {
                    "tagType": "AAA1 Required Tag Type",
                    "tagValue": "AAA1 RT1"
                }
            ],
            "costs": null,
            "headStart": false
        }
    ]
}
```

### 浏览

[获取程序](https://developer.adobe.com/marketo-apis/api/asset/#tag/Sales-Persons/operation/describeUsingGET_5)终结点允许您浏览程序。

可选的`status`参数允许您根据程序状态进行筛选。 此参数仅适用于参与和电子邮件程序。 参与计划的可能值为“on”和“off”，电子邮件计划的可能值为“unlocked”。

可选的`maxReturn`参数控制要返回的程序数（最大值为200，默认值为20）。 用于分页结果的可选`offset`参数（默认值为0）。

请注意，此端点不会返回与程序关联的标记。 可以使用[按ID获取程序](https://developer.adobe.com/marketo-apis/api/asset/#tag/Programs/operation/getProgramByIdUsingGET)或[按名称获取程序](https://developer.adobe.com/marketo-apis/api/asset/#tag/Programs/operation/getProgramByNameUsingGET)来检索程序标记。

```
GET /rest/asset/v1/programs.json
```

```json
{
    "success": true,
    "warnings": [],
    "errors": [],
    "requestId": "7a39#1511bf8a41c",
    "result": [
        {
            "id": 1035,
            "name": "clone it",
            "description": "",
            "createdAt": "2015-11-18T15:25:35Z+0000",
            "updatedAt": "2015-11-18T15:25:46Z+0000",
            "url": "https://app-devlocal1.marketo.com/#NP1035A1",
            "type": "Engagement",
            "channel": "Nurture",
            "folder": {
                "type": "Folder",
                "value": 28,
                "folderName": "Nurturing"
            },
            "status": "on",
            "workspace": "Default",
            "headStart": false
        },
        {
            "id": 1032,
            "name": "email prog",
            "description": "",
            "createdAt": "2015-11-18T14:56:28Z+0000",
            "updatedAt": "2015-11-18T14:56:28Z+0000",
            "url": "https://app-devlocal1.marketo.com/#EBP1032A1",
            "type": "Email",
            "channel": "Email Send",
            "folder": {
                "type": "Folder",
                "value": 26,
                "folderName": "Data Management"
            },
            "status": "unlocked",
            "workspace": "Default",
            "headStart": false
        }
    ]
}
```

### 按日期范围

我们的[获取程序](https://developer.adobe.com/marketo-apis/api/asset/#tag/Sales-Persons/operation/describeUsingGET_5)端点的`earliestUpdatedAt`和`latestUpdatedAt`参数允许您为返回的程序设置低日期时间和高日期时间水印，这些程序在给定范围内已更新或最初创建。

```
GET /rest/asset/v1/programs.json?earliestUpdatedAt=2017-01-01T00:00:00-05:00&latestUpdatedAt=2017-01-30T00:00:00-05:00
```

```json
{
    "success": true,
    "errors": [],
    "requestId": "1225a#15f82a83875",
    "warnings": [],
    "result": [
        {
            "id": 1070,
            "name": "Bulk Import - Test",
            "description": "",
            "createdAt": "2017-01-13T19:34:17Z+0000",
            "updatedAt": "2017-01-13T19:34:18Z+0000",
            "url": "https://app-abm.marketo.com/#PG1070A1",
            "type": "Default",
            "channel": "Content",
            "folder": {
                "type": "Folder",
                "value": 637,
                "folderName": "Avention"
            },
            "status": "",
            "workspace": "Default",
            "headStart": false
        },
        {
            "id": 1069,
            "name": "Program With Email",
            "description": "",
            "createdAt": "2017-01-03T22:53:14Z+0000",
            "updatedAt": "2017-01-03T22:53:15Z+0000",
            "url": "https://app-abm.marketo.com/#EBP1069A1",
            "type": "Email",
            "channel": "Email Send",
            "folder": {
                "type": "Folder",
                "value": 621,
                "folderName": "Smartling"
            },
            "status": "unlocked",
            "workspace": "Default",
            "headStart": false
        },
        {
            "id": 1071,
            "name": "Program with Guided Landing Page Template",
            "description": "",
            "createdAt": "2017-01-24T22:59:21Z+0000",
            "updatedAt": "2017-01-24T22:59:22Z+0000",
            "url": "https://app-abm.marketo.com/#PG1071A1",
            "type": "Default",
            "channel": "Content",
            "folder": {
                "type": "Folder",
                "value": 621,
                "folderName": "Smartling"
            },
            "status": "",
            "workspace": "Default",
            "headStart": false
        },
        {
            "id": 1047,
            "name": "ReachForce List Update",
            "description": "",
            "createdAt": "2016-05-24T19:38:35Z+0000",
            "updatedAt": "2017-01-13T19:28:09Z+0000",
            "url": "https://app-abm.marketo.com/#PG1047A1",
            "type": "Default",
            "channel": "Content",
            "folder": {
                "type": "Folder",
                "value": 407,
                "folderName": "Everly Tests"
            },
            "status": "",
            "workspace": "Default",
            "headStart": false
        }
    ]
}
```

### 按标记类型

[按标记获取程序](https://developer.adobe.com/marketo-apis/api/asset/#tag/Programs/operation/getProgramListByTagUsingGET)终结点检索与提供的标记类型和标记值匹配的程序列表。

有两个必需的参数，`tagType`是要筛选的标记类型，`tagValue`是要筛选的标记值。  有一个可选的integer `maxReturn`参数用于控制要返回的程序数（最大值为200，默认值为20），以及一个可选的integer `offset`参数用于分页结果（默认值为0）。  结果以随机顺序返回。

```
GET /rest/asset/v1/program/byTag.json?tagType=Presenter&tagValue=Dennis
```

```json
{
    "success" : true,
    "warnings" : [],
    "errors" : [],
    "requestId" : "13b6d#152b38d5be4",
    "result" : [{
            "id" : 1004,
            "name" : "It's a Program",
            "description" : "",
            "createdAt" : "2013-02-26T00:37:37Z+0000",
            "updatedAt" : "2013-03-11T15:32:02Z+0000",
            "url" : "https://app-sjst.marketo.com/#PG1004A1",
            "type" : "Default",
            "channel" : "Email Blast",
            "folder" : {
                "type" : "Folder",
                "value" : 38,
                "folderName" : "Test"
            },
            "status" : "",
            "workspace" : "Default",
            "tags" : [{
                    "tagType" : "Presenter",
                    "tagValue" : "Dennis"
                }
            ],
                        "headStart": false
    ]
}
```

## 创建和更新

[正在创建]https://developer.adobe.com/marketo-apis/api/asset/#tag/Programs/operation/createProgramUsingPOST)和[正在更新](https://developer.adobe.com/marketo-apis/api/asset/#tag/Programs/operation/updateProgramUsingPOST)程序，这些程序遵循标准资源模式，并且具有`folder`、`name`、`type`和`channel`作为必需参数，其中`description`、`costs`和`tags`为可选参数。 渠道和类型只能在创建项目时设置。 创建后只能更新描述、名称、`tags`和`costs`，并允许额外的`costsDestructiveUpdate`参数。 将`costsDestructiveUpdate`传递为true将导致清除所有现有成本，并将其替换为调用中包含的任何成本。 请注意，在某些订阅中，某些程序类型可能需要使用标记，但这取决于配置，应首先使用“获取标记”检查标记，以查看是否有实例特定的要求。

创建或更新电子邮件程序时，可能也会传递`startDate`和`endDate`。

### 创建

```
POST /rest/asset/v1/programs.json
```

```
Content-Type: application/x-www-form-urlencoded
```

```
name=API Test Program&folder={"id":1035,"type":"Folder"}&description=Sample API Program&type=Default&channel=Email Blast&costs=[{"startDate":"2015-01-01","cost":2000}]
```

```json
{
    "success": true,
    "warnings": [],
    "errors": [],
    "requestId": "d505#14d9bd96352",
    "result": [
        {
            "id": 1207,
            "name": "newProgram",
            "description": "This is a test",
            "createdAt": "2015-05-28T18:47:15Z+0000",
            "updatedAt": "2015-05-28T18:47:15Z+0000",
            "url": "https://app-devlocal1.marketo.com/#ME1207A1",
            "type": "Event",
            "channel": "channelOne",
            "folder": {
                "type": "Folder",
                "value": 59,
                "folderName": "blah blah"
            },
            "status": "",
            "workspace": "Default",
            "headStart": false
            "tags": null,
            "costs": [
                {
                    "startDate":"2015-01-01",
                    "cost":2000
                }
            ]
        }
    ]
}
```

### 更新

在更新程序成本时，若要追加新成本，只需将其添加到您的`costs`阵列即可。 要执行破坏性更新，请将新成本以及设置为`true`的参数`costsDestructiveUpdate`一起传递。 若要清除项目的所有成本，请不要传递`costs`参数，而只需传递设置为`true`的`costsDestructiveUpdate`。

```
POST /rest/asset/v1/program/{id}.json
```

```
Content-Type: application/x-www-form-urlencoded
```

```
description=This is an updated description&name=Updated Program Name&costs=[{"startDate":"2016-01-01","cost":200,"note":"Google Adwords"}]
```

```json
{
    "success": true,
    "warnings": [],
    "errors": [],
    "requestId": "5c37#14db05608aa",
    "result": [
        {
            "id": 1110,
            "name": "Updated Program Name",
            "description": "This is a updated description",
            "createdAt": "2015-05-21T22:45:14Z+0000",
            "updatedAt": "2015-06-01T18:13:58Z+0000",
            "url": "https://app-devlocal1.marketo.com/#NP1110A1",
            "type": "Engagement",
            "channel": "Nurture",
            "folder": {
                "type": "Folder",
                "value": 1910,
                "folderName": "ProgramQueryTestFolder"
            },
            "status": "on",
            "workspace": "Default",
            "headStart": false,
            "tags": [
                {
                    "tagType": "AAA1 Required Tag Type",
                    "tagValue": "AAA1 RT1"
                },
                {
                    "tagType": "tagTypeOne",
                    "tagValue": "tagTypeValue1"
                }
            ],
            "costs": [
                {
                    "startDate": "2016-01-01",
                    "cost": 200,
                    "note": "Google Adwords"
                }
            ]
        }
    ]
}
```

## 审批

可以远程批准或取消批准电子邮件程序，这将导致程序在给定的startDate运行并在给定的endDate结束。 必须设置这两个参数以批准项目，并通过UI配置有效且批准的电子邮件和智能列表。

### 审批

```
POST /rest/asset/v1/program/{id}/approve.json
```

```json
{
    "success": true,
    "warnings": [],
    "errors": [],
    "requestId": "16026#150b5bf7692",
    "result": [
        {
            "id": 11062
        }
    ]
}
```

### 取消批准

```
POST /rest/asset/v1/program/{id}/unapprove.json
```

```json
{
    "success": true,
    "warnings": [],
    "errors": [],
    "requestId": "16026#150b5bf7692",
    "result": [
        {
            "id": 11062
        }
    ]
}
```

## 克隆

[克隆程序](https://developer.adobe.com/marketo-apis/api/asset/#tag/Programs/operation/cloneProgramUsingPOST)遵循标准资源模式，将新名称和文件夹作为必需参数和可选描述。  `name`参数必须是全局唯一的，且不能超过255个字符。  `folder`参数是父文件夹。  必须将`folder`参数类型属性设置为“文件夹”，并且目标文件夹必须与要克隆的程序位于相同的工作区中。

包含特定类型资源的程序可能无法通过此API进行克隆，包括推送通知、应用程序内消息、报表和社交Assets。 应用程序内程序可能无法通过此API进行克隆。

```
POST /rest/asset/v1/program/{id}/clone.json
```

```
Content-Type: application/x-www-form-urlencoded
```

```
name=Cloned Program - PHP&folder={"id":5562,"type":"Folder"}&description=Description
```

```json
{
    "success": true,
    "warnings": [],
    "errors": [],
    "requestId": "3a7f#14db06990cc",
    "result": [
        {
            "id": 1221,
            "name": "cloneProgram",
            "description": "This is a description for the cloned program",
            "createdAt": "2015-06-01T18:36:57Z+0000",
            "updatedAt": "2015-06-01T18:36:57Z+0000",
            "url": "https://app-devlocal1.marketo.com/#PG1221A1",
            "type": "Default",
            "channel": "Blog",
            "folder": {
                "type": "Folder",
                "value": 59,
                "folderName": "blah blah"
            },
            "status": "",
            "workspace": "Default",
            "headStart": false
            "tags": null,
            "costs": null
        }
    ]
}
```

## 删除项目群

删除程序遵循标准资产删除模式。

```
POST /rest/asset/v1/program/{id}/delete.json
```

```json
{
    "success": true,
    "warnings": [],
    "errors": [],
    "requestId": "16501#14db042c6b7",
    "result": [
        {
            "id": 1109
        }
    ]
}
```
