---
title: 智能营销活动
feature: REST API, Smart Campaigns
description: 了解如何将Marketo REST API用于Smart Campaigns，包括按ID或名称查询、浏览筛选器、创建克隆删除以及计划或请求触发器
exl-id: 540bdf59-b102-4081-a3d7-225494a19fdd
TQID: https://experienceleague.adobe.com/iysRjtqd9plkreyIMuNjAF3YVFHtDUIrc-GInB4V8mg
product_v2:
  - id: b27e5950-9033-45ac-9f86-eb22e567f615
feature_v2:
  - id: a7170d27-32ab-462b-a333-269abc654483
  - id: e64968b2-4ee5-47f9-8cae-0588f184b9eb
subfeature_v2:
  - id: ad89fb33-8541-4339-afe7-bb13d1633714
  - id: d0251300-e25f-466f-9856-7e11ce8fa7aa
role_v2:
  - id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
source-git-commit: 3e6d310c5aec1a3435424fb122b71d825db5af0e
workflow-type: tm+mt
source-wordcount: 1009
ht-degree: 1%

---

# 智能营销活动

[智能营销活动端点引用（资产）](https://developer.adobe.com/marketo-apis/api/asset#tag/Smart-Campaigns)

[营销活动端点引用（潜在客户）](https://developer.adobe.com/marketo-apis/api/mapi#tag/Campaigns)

使用Smart Campaign REST API查询、创建、克隆和删除智能营销活动。 您还可以计划批量营销活动、请求触发营销活动和管理营销活动激活。

## 查询

按ID[&#128279;](#by_id)、[名称](#by_name)或[浏览](#browse)查询智能营销活动。

### 按Id

[按ID获取Smart Campaign](https://developer.adobe.com/marketo-apis/api/asset#tag/Smart-Campaigns/operation/getSmartCampaignByIdUsingGET)端点将单个Smart Campaign `id`作为路径参数并返回单个Smart Campaign记录。

```http
GET /rest/asset/v1/smartCampaign/{id}.json
```

```json
{
    "success": true,
    "errors": [],
    "requestId": "7883#169838a32f0",
    "warnings": [],
    "result": [
        {
            "id": 1001,
            "name": "Process Bounced Emails",
            "description": "System smart campaign for processing bounced email events",
            "createdAt": "2016-09-10T23:16:19Z+0000",
            "updatedAt": "2016-09-10T23:16:19Z+0000",
            "status": "Never Run",
            "type": "batch",
            "isSystem": false,
            "isActive": false,
            "isRequestable": false,
            "isCommunicationLimitEnabled": false,
            "recurrence": {
                "weekdayOnly": false
            },
            "qualificationRuleType": "once",
            "workspace": "Default",
            "smartListId": 1001,
            "flowId": 1001,
            "computedUrl": "https://app-sjqe.marketo.com/#SC1001A1"
        }
    ]
}
```

终结点在`result`数组的第一个位置返回一个记录。

### 按名称

[按名称获取Smart Campaign](https://developer.adobe.com/marketo-apis/api/asset#tag/Smart-Campaigns/operation/getSmartCampaignByNameUsingGET)端点将单个智能营销活动`name`作为参数并返回单个智能营销活动记录。

```http
GET /rest/asset/v1/smartCampaign/byName.json?name=Test Trigger Campaign
```

```json
{
    "success": true,
    "errors": [],
    "requestId": "14494#16c886ffa44",
    "warnings": [],
    "result": [
        {
            "id": 1069,
            "name": "Test Trigger Campaign",
            "description": "",
            "createdAt": "2018-02-16T01:34:39Z+0000",
            "updatedAt": "2019-08-13T00:45:21Z+0000",
            "folder": {
                "id": 327,
                "type": "Folder"
            },
            "status": "Inactive",
            "type": "trigger",
            "isSystem": false,
            "isActive": false,
            "isRequestable": false,
            "isCommunicationLimitEnabled": false,
            "recurrence": {
                "weekdayOnly": false
            },
            "qualificationRuleType": "once",
            "workspace": "Default",
            "smartListId": 2747,
            "flowId": 1088,
            "computedUrl": "https://app-sjqe.marketo.com/#SC1069A1"
        }
    ]
}
```

终结点在`result`数组的第一个位置返回一个记录。

### 浏览

[获取智能营销活动](https://developer.adobe.com/marketo-apis/api/asset#tag/Smart-Campaigns/operation/getAllSmartCampaignsGET)端点支持用于筛选和分页的可选查询参数。

`earliestUpdatedAt`和`latestUpdatedAt`参数接受ISO-8601格式的`datetimes`（不含毫秒）。 如果同时设置了两者，则learestUpdatedAt必须在latestUpdatedAt之前。

`folder`参数指定要浏览的父文件夹。 将其作为包含`id`和`type`的JSON对象传递。

`maxReturn`整数指定最大条目数。 默认值为20，最大值为200。

`offset`整数指定开始检索条目的位置。 将它与`maxReturn`一起使用。 默认值为0。

设置`isActive`布尔值参数以仅返回活动的触发器营销活动。

```http
GET /rest/asset/v1/smartCampaigns.json?earliestUpdatedAt=2016-09-10T23:15:00-00:00&latestUpdatedAt=2016-09-10T23:17:00-00:00
```

```json
{
    "success": true,
    "errors": [],
    "requestId": "626#16983a92965",
    "warnings": [],
    "result": [
        {
            "id": 1001,
            "name": "Process Bounced Emails",
            "description": "System smart campaign for processing bounced email events",
            "createdAt": "2016-09-10T23:16:19Z+0000",
            "updatedAt": "2016-09-10T23:16:19Z+0000",
            "status": "Never Run",
            "type": "batch",
            "isSystem": false,
            "isActive": false,
            "isRequestable": false,
            "isCommunicationLimitEnabled": false,
            "recurrence": {
                "weekdayOnly": false
            },
            "qualificationRuleType": "once",
            "workspace": "Default",
            "smartListId": 1001,
            "flowId": 1001,
            "computedUrl": "https://app-sjqe.marketo.com/#SC1001A1"
        },
        {
            "id": 1002,
            "name": "Process Unsubscribes",
            "description": "System smart campaign for processing unsubscribe events",
            "createdAt": "2016-09-10T23:16:19Z+0000",
            "updatedAt": "2016-09-10T23:16:19Z+0000",
            "status": "Never Run",
            "type": "batch",
            "isSystem": false,
            "isActive": false,
            "isRequestable": false,
            "isCommunicationLimitEnabled": false,
            "recurrence": {
                "weekdayOnly": false
            },
            "qualificationRuleType": "once",
            "workspace": "Default",
            "smartListId": 1002,
            "flowId": 1002,
            "computedUrl": "https://app-sjqe.marketo.com/#SC1002A1"
        }
    ]
}
```

终结点在`result`数组中返回一个或多个记录。

## 创建

向[创建Smart Campaign](https://developer.adobe.com/marketo-apis/api/asset#tag/Smart-Campaigns/operation/createSmartCampaignUsingPOST)终结点发送`application/x-www-form-urlencoded`个POST请求。 `name`和`folder`参数是必需的。 将`folder`作为包含`id`和`type`的JSON对象传递。

或者，您可以使用`description`参数（最多2,000个字符）描述智能营销活动。

```http
POST /rest/asset/v1/smartCampaigns.json
```

```text
Content-Type: application/x-www-form-urlencoded
```

```text
name=Smart Campaign 02&folder={"type": "folder","id": 640}&description=This is a smart campaign creation test.
```

```json
{
    "success": true,
    "errors": [],
    "requestId": "25bc#16c9138f148",
    "warnings": [],
    "result": [
        {
            "id": 1076,
            "name": "Smart Campaign 02",
            "description": "This is a smart campaign creation test.",
            "createdAt": "2019-08-14T17:42:04Z+0000",
            "updatedAt": "2019-08-14T17:42:04Z+0000",
            "folder": {
                "id": 640,
                "type": "Folder"
            },
            "status": "Never Run",
            "type": "batch",
            "isSystem": false,
            "isActive": false,
            "isRequestable": false,
            "isCommunicationLimitEnabled": true,
            "recurrence": {
                "weekdayOnly": false
            },
            "qualificationRuleType": "once",
            "workspace": "Default",
            "smartListId": 5132,
            "flowId": 1095,
            "computedUrl": "https://app-sjqe.marketo.com/#SC1076A1"
        }
    ]
}
```

## 更新

向[更新Smart Campaign](https://developer.adobe.com/marketo-apis/api/asset)终结点发送`application/x-www-form-urlencoded`个POST请求。 smart-campaign `id`路径参数是必需的。 使用`name`更改名称，或使用`description`更改描述。

```http
POST /rest/asset/v1/smartCampaign/{id}.json
```

```text
Content-Type: application/x-www-form-urlencoded
```

```sql
name=Smart Campaign 02 Update&description=This is a smart campaign update test.
```

```json
{
    "success": true,
    "errors": [],
    "requestId": "14b6a#16c924b992f",
    "warnings": [],
    "result": [
        {
            "id": 1076,
            "name": "Smart Campaign 02 Update",
            "description": "This is a smart campaign update test.",
            "createdAt": "2019-08-14T17:42:04Z+0000",
            "updatedAt": "2019-08-14T22:42:04Z+0000",
            "folder": {
                "id": 640,
                "type": "Folder"
            },
            "status": "Never Run",
            "type": "batch",
            "isSystem": false,
            "isActive": false,
            "isRequestable": false,
            "isCommunicationLimitEnabled": true,
            "recurrence": {
                "weekdayOnly": false
            },
            "qualificationRuleType": "once",
            "workspace": "Default",
            "smartListId": 5132,
            "flowId": 1095,
            "computedUrl": "https://app-sjqe.marketo.com/#SC1076A1"
        }
    ]
}
```

## 克隆

向[克隆智能营销活动](https://developer.adobe.com/marketo-apis/api/asset#tag/Sales-Persons/operation/describeUsingGET_5)端点发送`application/x-www-form-urlencoded`个POST请求。 `id`、`name`和`folder`参数是必需的。 它们指定源营销活动、新营销活动名称和父文件夹。 将`folder`作为包含`id`和`type`的JSON对象传递。

或者，您可以使用`description`参数（最多2,000个字符）描述智能营销活动。

```http
POST /rest/asset/v1/smartCampaign/{id}/clone.json
```

```text
Content-Type: application/x-www-form-urlencoded
```

```text
name=Test Trigger Campaign Clone&folder={"type": "folder","id": 640}&description=This is a smart campaign clone test.
```

```json
{
    "success": true,
    "errors": [],
    "requestId": "681d#16c9339499b",
    "warnings": [],
    "result": [
        {
            "id": 1077,
            "name": "Test Trigger Campaign Clone",
            "description": "This is a smart campaign clone test.",
            "createdAt": "2019-08-15T03:01:41Z+0000",
            "updatedAt": "2019-08-15T03:01:41Z+0000",
            "folder": {
                "id": 640,
                "type": "Folder"
            },
            "status": "Inactive",
            "type": "trigger",
            "isSystem": false,
            "isActive": false,
            "isRequestable": false,
            "isCommunicationLimitEnabled": false,
            "recurrence": {
                "weekdayOnly": false
            },
            "qualificationRuleType": "once",
            "workspace": "Default",
            "smartListId": 5135,
            "flowId": 1096,
            "computedUrl": "https://app-sjqe.marketo.com/#SC1077A1"
        }
    ]
}
```

## 删除

[删除Smart Campaign](https://developer.adobe.com/marketo-apis/api/asset#tag/Smart-Campaigns/operation/deleteSmartCampaignUsingPOST)终结点将单个Smart Campaign `id`作为路径参数。

```http
POST /rest/asset/v1/smartCampaign/{id}/delete.json
```

```json
{
    "success": true,
    "errors": [],
    "requestId": "d757#16c934216ac",
    "warnings": [],
    "result": [
        {
            "id": 1077
        }
    ]
}
```

## 批次

在指定的时间批量运行智能营销活动，并将定义的潜在客户集一起处理。

## 计划

使用[计划营销活动](https://developer.adobe.com/marketo-apis/api/mapi#tag/Campaigns/operation/scheduleCampaignUsingPOST)来计划批次营销活动。 活动`id`路径参数是必需的。 在JSON请求正文中传递可选的`tokens`、`runAt`和`cloneToProgram`参数。

`tokens`数组将覆盖此运行的现有程序“我的令牌”。 在营销策划运行后，Marketo会放弃覆盖。 每个项目都包含一个名称/值对，并且令牌名称必须使用`{{my.name}}`格式。

`runAt`日期时间参数指定运行营销活动的时间。 如果忽略，则营销活动将在请求后五分钟运行。 该值以后不能超过两年。

通过此API计划的营销活动始终在运行时至少等待五分钟。

`cloneToProgram`字符串参数包含生成程序的名称。  设置后，这将导致使用生成的新名称创建营销活动、父项目及其所有资产。 克隆了父项目，并将计划新创建的营销策划。 生成的项目将在父项目下创建。 包含代码片段、推送通知、应用程序内消息、静态列表、报表和社交资产的程序可能无法以这种方式克隆。 在使用时，此端点限制每天最多20次调用。 [克隆程序](https://developer.adobe.com/marketo-apis/api/asset#tag/Sales-Persons/operation/describeUsingGET_5)端点是推荐的替代项。

```http
POST /rest/v1/campaigns/{id}/schedule.json
```

```json
{
   "input":
      {
         "runAt": "2018-03-28T18:05:00+0000",
         "tokens": [
            {
               "name": "{{my.message}}",
               "value": "Updated message"
            },
            {
               "name": "{{my.other token}}",
               "value": "Value for other token"
            }
          ]
      }
}
```

```json
{
    "requestId": "52b#161d90e1743",
    "result": [
        {
            "id": 3713
        }
    ],
    "success": true
}
```

## 触发器

触发智能营销活动每次处理一个人以响应事件。

### 请求

使用[请求营销活动](https://developer.adobe.com/marketo-apis/api/mapi#tag/Campaigns/operation/triggerCampaignUsingPOST)通过触发器营销活动流程传递潜在客户。 营销活动必须使用将Web服务API用作其源的Campaign is Requested触发器。

市场活动`id`路径参数和`leads`商机ID的整数数组是必需的。 每个调用最多接受100个潜在客户。

（可选）可以使用`tokens`数组参数覆盖营销活动父项目本地的“我的令牌”。 `tokens`最多接受100个令牌。 每个`tokens`数组项都包含一个名称/值对。 令牌的名称必须格式化为“`{{my.name}}`”。 如果您使用[添加系统令牌作为电子邮件](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/email-marketing/general/using-tokens/add-a-system-token-as-a-link-in-an-email)方法中的链接来添加“viewAsWebPageLink”系统令牌，则无法使用`tokens`覆盖它。 请改为使用[将视图作为网页链接添加到电子邮件](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/email-marketing/general/functions-in-the-editor/add-a-view-as-web-page-link-to-an-email)方法，此方法允许您使用`tokens`覆盖“viewAsWebPageLink”。

在JSON请求正文中传递`leads`和`tokens`参数。

```http
POST /rest/v1/campaigns/{id}/trigger.json
```

```json
{
   "input":
      {
         "leads" : [
            {
               "id" : 318592
            },
            {
               "id" : 318593
            }
         ],
         "tokens" : [
            {
               "name": "{{my.message}}",
               "value": "Updated message"
            },
            {
               "name": "{{my.other token}}",
               "value": "Value for other token"
            }
         ]
      }
}
```

```json
{
    "requestId": "9e01#161d922f1aa",
    "result": [
        {
            "id": 3712
        }
    ],
    "success": true
}
```

### 激活

[激活Smart Campaign](https://developer.adobe.com/marketo-apis/api/asset#tag/Smart-Campaigns/operation/activateSmartCampaignUsingPOST)终结点很简单。 `id`路径参数是必需的。 要使激活成功，营销活动必须满足以下条件：

- 营销活动已停用。
- 营销活动至少具有一个触发器和一个流程步骤。
- 营销活动具有无错误的触发器、过滤器和流程步骤。

```http
POST /rest/asset/v1/smartCampaign/{id}/activate.json
```

```json
{
    "success": true,
    "errors": [],
    "requestId": "a33a#161d9c0dcf3",
    "result": [
        {
            "id": 1069
        }
    ]
}
```

### 停用

[停用智能营销活动](https://developer.adobe.com/marketo-apis/api/asset#tag/Smart-Campaigns/operation/deactivateSmartCampaignUsingPOST)很简单。 `id`路径参数是必需的。 要成功停用，必须激活营销活动。

```http
POST /rest/asset/v1/smartCampaign/{id}/deactivate.json
```

```json
{
    "success": true,
    "errors": [],
    "requestId": "6228#161d9c29fbf",
    "result": [
        {
            "id": 1069
        }
    ]
}
```
