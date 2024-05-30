---
title: "渠道"
feature: REST API
description: “使用Marketo API配置渠道数据。”
source-git-commit: d335bdd9f939c3e557a557b43fb3f33934e13fef
workflow-type: tm+mt
source-wordcount: '125'
ht-degree: 1%

---


# 渠道

[渠道端点引用](https://developer.adobe.com/marketo-apis/api/asset/#tag/Channels)

渠道是所有项目类型的标准字段和必填字段。 每种渠道类型只能与给定的一起使用 `applicableProgramType` 并提供对每个项目中的项目群成员有效的可用项目群状态的列表。 如果在创建项目后更改了渠道的项目群状态，则潜在客户可更改为的项目群状态列表将与当时渠道提供的列表相匹配，但不会追溯性地更改任何现有项目群成员资格记录的项目群状态。

## 查询

渠道可作为标准资产进行查询，但没有按ID检索渠道的端点。

### 浏览

```
GET /rest/asset/v1/channels.json?offset=10
```

```json
{
    "success": true,
    "warnings": [],
    "errors": [],
    "requestId": "651#1504ebbbfcf",
    "result": [
        {
            "id": 3,
            "name": "Blog",
            "applicableProgramType": "program",
            "progressionStatuses": [
                {
                    "name": "Not in Program",
                    "step": 0,
                    "description": null,
                    "hidden": false,
                    "success": false
                },
                {
                    "name": "Invited",
                    "step": 10,
                    "description": null,
                    "hidden": false,
                    "success": false
                },
                {
                    "name": "Visited Booth",
                    "step": 20,
                    "description": null,
                    "hidden": false,
                    "success": false
                },
                {
                    "name": "Influenced",
                    "step": 30,
                    "description": null,
                    "hidden": false,
                    "success": true
                }
            ],
            "createdAt": "2015-07-15T11:40:57Z+0000",
            "updatedAt": "2015-07-15T11:40:57Z+0000"
        },
        {
            "id": 4,
            "name": "Online Advertising",
            "applicableProgramType": "program",
            "progressionStatuses": [
                {
                    "name": "Not in Program",
                    "step": 0,
                    "description": null,
                    "hidden": false,
                    "success": false
                },
                {
                    "name": "Invited",
                    "step": 10,
                    "description": null,
                    "hidden": false,
                    "success": false
                },
                {
                    "name": "Registered",
                    "step": 20,
                    "description": null,
                    "hidden": false,
                    "success": false
                },
                {
                    "name": "No Show",
                    "step": 30,
                    "description": null,
                    "hidden": false,
                    "success": false
                },
                {
                    "name": "Attended",
                    "step": 40,
                    "description": null,
                    "hidden": false,
                    "success": true
                }
            ],
            "createdAt": "2015-07-15T11:40:58Z+0000",
            "updatedAt": "2015-07-15T11:40:58Z+0000"
        }
    ]
}
```

### 按名称

```
GET /rest/asset/v1/channel/byName.json?name=Online Advertising
```

```json
{
    "success": true,
    "warnings": [],
    "errors": [],
    "requestId": "394c#1504eb476ed",
    "result": [
        {
            "id": 4,
            "name": "Online Advertising",
            "applicableProgramType": "program",
            "progressionStatuses": [
                {
                    "name": "Not in Program",
                    "step": 0,
                    "description": null,
                    "hidden": false,
                    "success": false
                },
                {
                    "name": "Invited",
                    "step": 10,
                    "description": null,
                    "hidden": false,
                    "success": false
                },
                {
                    "name": "Registered",
                    "step": 20,
                    "description": null,
                    "hidden": false,
                    "success": false
                },
                {
                    "name": "No Show",
                    "step": 30,
                    "description": null,
                    "hidden": false,
                    "success": false
                },
                {
                    "name": "Attended",
                    "step": 40,
                    "description": null,
                    "hidden": false,
                    "success": true
                }
            ],
            "createdAt": "2015-07-15T11:40:58Z+0000",
            "updatedAt": "2015-07-15T11:40:58Z+0000"
        }
    ]
}
```
