---
title: 智能营销活动
feature: REST API, Smart Campaigns
description: Smart campaign概述
exl-id: 540bdf59-b102-4081-a3d7-225494a19fdd
source-git-commit: 66add4c38d0230c36d57009de985649bb67fde3e
workflow-type: tm+mt
source-wordcount: '989'
ht-degree: 1%

---

# 智能营销活动

[智能营销活动终结点引用（资源）](https://developer.adobe.com/marketo-apis/api/asset/#tag/Smart-Campaigns)

[营销活动终结点引用（潜在客户）](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Campaigns)

Marketo提供了一组REST API用于对智能营销活动执行操作。 这些API遵循资产API的标准界面模式，提供查询、创建、克隆和删除选项。 此外，您还可以通过计划批量营销活动或请求触发营销活动来管理智能营销活动执行。

## 查询

查询智能营销活动遵循[按ID](#by_id)、[按名称](#by_name)和[浏览](#browse)的标准查询类型。

### 按Id

[按ID获取Smart Campaign](https://developer.adobe.com/marketo-apis/api/asset/#tag/Smart-Campaigns/operation/getSmartCampaignByIdUsingGET)端点将单个Smart Campaign `id`作为路径参数并返回单个Smart Campaign记录。

```
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

使用此终结点，`result`数组的第一个位置将始终有一个记录。

### 按名称

[按名称获取Smart Campaign](https://developer.adobe.com/marketo-apis/api/asset/#tag/Smart-Campaigns/operation/getSmartCampaignByNameUsingGET)端点将单个智能营销活动`name`作为参数并返回单个智能营销活动记录。

```
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

使用此终结点，`result`数组的第一个位置将始终有一个记录。

### 浏览

[获取智能营销活动](https://developer.adobe.com/marketo-apis/api/asset/#tag/Smart-Campaigns/operation/getAllSmartCampaignsGET)终结点的工作方式与其他资产API浏览终结点类似，并且允许多个可选查询参数指定筛选条件。

`earliestUpdatedAt`和`latestUpdatedAt`参数接受ISO-8601格式的`datetimes`（不含毫秒）。 如果同时设置了两者，则learestUpdatedAt必须在latestUpdatedAt之前。

`folder`参数指定要在其中浏览的父文件夹。 格式为包含`id`和`type`特性的JSON块。

`maxReturn`参数是一个整数，它指定要返回的最大条目数。 默认值为20。 最大值为200。

`offset`参数是一个整数，它指定从何处开始检索条目。 可以与`maxReturn`一起使用。 默认值为0。

`isActive`参数是一个布尔值，指定仅返回活动的触发器营销活动。

```
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

使用此终结点，`result`数组中将显示一个或多个记录。

## 创建

使用application/x-www-form-urlencodedPOST执行[创建Smart Campaign](https://developer.adobe.com/marketo-apis/api/asset/#tag/Smart-Campaigns/operation/createSmartCampaignUsingPOST)终结点，该表达式带有两个必需的参数。 `name`参数指定要创建的智能营销活动的名称。 `folder`参数指定创建智能营销活动的父文件夹。 格式为包含`id`和`type`特性的JSON块。

或者，您可以使用`description`参数（最多2,000个字符）描述智能营销活动。

```
POST /rest/asset/v1/smartCampaigns.json
```

```
Content-Type: application/x-www-form-urlencoded
```

```
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

使用application/x-www-form-urlencodedPOST执行[Update Smart Campaign](https://developer.adobe.com/marketo-apis/api/asset/)终结点。 它将单个智能营销活动`id`作为路径参数。 您可以使用`name`参数更新智能营销活动的名称，或者使用`description`参数更新智能营销活动的描述。

```
POST /rest/asset/v1/smartCampaign/{id}.json
```

```
Content-Type: application/x-www-form-urlencoded
```

```
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

使用application/x-www-form-urlencodedPOST执行[Clone Smart Campaign](https://developer.adobe.com/marketo-apis/api/asset/#tag/Sales-Persons/operation/describeUsingGET_5)终结点，并带有三个必需的参数。 它需要指定要克隆的智能营销活动的`id`参数、指定新智能营销活动名称的`name`参数以及指定创建新智能营销活动所在的父文件夹的`folder`参数。 格式为包含`id`和`type`特性的JSON块。

或者，您可以使用`description`参数（最多2,000个字符）描述智能营销活动。

```
POST /rest/asset/v1/smartCampaign/{id}/clone.json
```

```
Content-Type: application/x-www-form-urlencoded
```

```
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

[删除Smart Campaign](https://developer.adobe.com/marketo-apis/api/asset/#tag/Smart-Campaigns/operation/deleteSmartCampaignUsingPOST)终结点将单个Smart Campaign `id`作为路径参数。

```
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

在特定时间批量启动智能营销活动，并一次影响一组特定的潜在客户。

## 计划

使用[计划营销活动](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Campaigns/operation/scheduleCampaignUsingPOST)端点计划立即运行或在将来日期运行的批处理营销活动。 营销活动`id`是必需的路径参数。 可选参数为`tokens`、`runAt`和`cloneToProgram`，它们作为application/json在请求正文中传递。

令牌数组参数是“我的令牌”的数组，用于覆盖现有程序令牌。 营销活动运行后，令牌将被丢弃。  每个令牌数组项都包含名称/值对。 令牌的名称必须格式化为“{{my.name}}”。

runAt日期时间参数指定运行营销活动的时间。 如果未指定，则营销活动将在调用终结点后5分钟运行。 日期时间值在以后不能超过两年。

通过此API计划的营销活动始终在运行时至少等待五分钟。

`cloneToProgram`字符串参数包含生成程序的名称。  设置后，这将导致使用生成的新名称创建营销活动、父项目及其所有资产。 克隆了父项目，并将计划新创建的营销策划。 生成的项目将在父项目下创建。 包含代码片段、推送通知、应用程序内消息、静态列表、报表和社交资产的程序可能无法以这种方式克隆。 在使用时，此端点限制每天最多20次调用。 [克隆程序](https://developer.adobe.com/marketo-apis/api/asset/#tag/Sales-Persons/operation/describeUsingGET_5)端点是推荐的替代项。

```
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

触发智能营销活动根据触发的事件一次影响一个人。

### 请求

使用[请求营销活动](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Campaigns/operation/triggerCampaignUsingPOST)端点向要通过营销活动流程的触发器营销活动传递一组潜在客户。 营销活动必须具有“已请求营销活动”触发器，并将“Web服务API”作为源。

此端点需要营销活动`id`作为路径参数，以及包含潜在客户ID的`leads`整数数组参数。 每次调用最多允许100个潜在客户。

（可选）可以使用`tokens`数组参数覆盖营销活动父项目本地的“我的令牌”。 `tokens`最多接受100个令牌。 每个`tokens`数组项都包含一个名称/值对。 令牌的名称必须格式化为“{{my.name}}”。 如果您使用[添加系统令牌作为电子邮件](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/email-marketing/general/using-tokens/add-a-system-token-as-a-link-in-an-email)方法中的链接来添加“viewAsWebPageLink”系统令牌，则无法使用`tokens`覆盖它。 请改为使用[将视图作为网页链接添加到电子邮件](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/email-marketing/general/functions-in-the-editor/add-a-view-as-web-page-link-to-an-email)方法，此方法允许您使用`tokens`覆盖“viewAsWebPageLink”。

`leads`和`tokens`参数在请求正文中作为application/json传递。

```
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

[激活Smart Campaign](https://developer.adobe.com/marketo-apis/api/asset/#tag/Smart-Campaigns/operation/activateSmartCampaignUsingPOST)终结点很简单。 `id`路径参数是必需的。 要使激活成功，营销活动必须满足以下条件：

- 必须停用
- 必须具有至少一个触发器和一个流步骤
- 必须具有无错误的触发器、过滤器和流程步骤

```
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

[停用智能营销活动](https://developer.adobe.com/marketo-apis/api/asset/#tag/Smart-Campaigns/operation/deactivateSmartCampaignUsingPOST)很简单。 `id`路径参数是必需的。 要成功停用，必须激活营销活动。

```
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
