---
title: 数据流
description: Marketo Engage数据流概述，支持近乎实时的潜在客户活动和用户审核事件，放宽了性能层客户的API限制
exl-id: 5617b6a5-ebc8-4d97-a290-e3b87f83e360
TQID: https://experienceleague.adobe.com/JnhN70HexjmNueZa9MAVrxjEhZ5yJatWqZiowl22quA
product_v2:
  - id: b27e5950-9033-45ac-9f86-eb22e567f615
role_v2:
  - id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
topic_v2:
  - id: e1e0219c-f879-479f-8427-888ed2a6e9c2
source-git-commit: 3e6d310c5aec1a3435424fb122b71d825db5af0e
workflow-type: tm+mt
source-wordcount: 1342
ht-degree: 3%

---

# 数据流

>[!NOTE]
>
>有关数据流的当前信息现在位于[使用数据流](https://developer.adobe.com/events/docs/guides/using/marketo/marketo-data-streams#)。
>

数据流近乎实时地向外部系统提供大量Marketo Engage数据。 使用流式处理的数据来支持及时决策、有针对性的营销活动、外部营销流程和审核。

数据流具有以下优势：

- 减少对速率限制的API请求的依赖。
- 减少API限制警报。
- 在不运行批量导出的情况下提供数据。

数据流适用于已购买[Marketo Engage性能层包](https://nation.marketo.com/t5/product-documents/marketo-engage-performance-tiers/ta-p/328835)的用户。

## 潜在客户活动数据流概述

潜在客户活动数据流近乎实时地向外部系统发送大量潜在客户活动数据。 使用流审核潜在客户事件和使用模式、查看潜在客户更改以及触发来自潜在客户事件的工作流。

您可以订阅144种以上的活动类型。

流可以包括：

1. 更改了所有潜在客户字段和新创建的潜在客户。
1. 所有记录的潜在客户活动类型。
1. 已删除潜在客户。
1. 所有潜在客户自定义对象（请求时）。 您无法选择单个自定义对象。

常见用例包括：

- 自定义警报：将条件不一致的潜在客户添加到列表。 该流将“添加到列表”活动发送到跟进过程。
- 机器学习模型：在外部评分模型中使用活动分析，然后将得分发送到Marketo以影响培养活动或其他流程。

流式处理活动列表：

| AchieveGoalInReferral | ClickPredictiveContent | ReceivedForwardToFriendEmail |
| --- | --- | --- |
| AddToList | ClickRTPCallToAction | ReceiveSalesEmail |
| AddToNurture | ClickSalesEmail | RefctosocialApp |
| AddToOpportunity | 单击共享链接 | RemoveFromList |
| AddToSalesCampaign | Convertlead | RemoveFromOpportunity |
| CallWebhook | DeleteLead | Requestcampaign |
| ChangeDataValue | 取消抽奖资格 | SalesEmailBounced |
| ChangeLedPartition | EarnEntryInSocialApp | 发送警报 |
| ChangeNurtureCadence | 电子邮件已退回 | 发送电子邮件 |
| ChangeNurtureTrack | EmailBouncedSoft | SendSalesEmail |
| ChangeOwner | EmailDelivered | SentForwardToFriendEmail |
| ChangeProgramData | ExtendedWithDataDotCom | SFDCActivity |
| ChangeProgramMemberdata | EnterSweepstackes | 共享内容 |
| ChangeRevenueStage | FillOutFacebookLeadAdsForm | SignupForReferralOffer |
| ChangeRevenueStageManual | 填写表单 | SyncLeadToMicrosoft |
| ChangeScore | InterestedMoment | SyncLeadToSFDC |
| ChangeSegment | 合并潜在客户 | 取消订阅电子邮件 |
| ChangeStatusInProgress | NewLead | UpdatePortunity |
| ChangeStatusInSalesCampaign | OpenEmail | VisitewebPage |
| ClickEmail | OpenSalesEmail | VoteInPoll |
| ClickLink | PushLeadToMarketo | WinSweepstates |

流式传输自定义对象时，包括所有潜在客户相关的自定义对象。 您无法选择单个自定义对象。

## 用户审核数据流概述

用户审核数据流近乎实时地跟踪用户对资源的更改。 使用它可以审核资产事件、查看用户更改以及从审核事件触发流程。

Adobe I/O Events将更改发送到可配置的端点。 订阅每种资源类型所需的事件类型。

一个用例是：

- 跨营销系统跟踪更改：当CRM或其他系统与Marketo交换潜在客户时，请使用审核事件来识别哪个系统进行了最新更改。

流式用户审核事件列表：

| 组件 | 事件类型列表 |
| --- | --- |
| 默认项目 | 克隆、创建、删除、编辑通道、导出、修改程序设置、修改程序令牌、重命名 |
| 电子邮件 | 批准、克隆、创建、删除、编辑、移动、重命名、取消批准 |
| 电子邮件批处理程序 | 批准、子更新、克隆、创建、删除、编辑、编辑通道、修改程序计划、修改程序设置、修改程序令牌、重命名、取消批准 |
| 电子邮件模板 | 批准、克隆、创建、删除、草稿创建、草稿放弃、编辑、重命名、取消批准 |
| 参与项目 | 克隆、创建、删除、编辑通道、修改程序设置、修改程序流、修改程序令牌、重命名 |
| 活动项目 | 克隆、创建、删除、编辑通道、修改程序计划、修改程序设置、修改程序令牌、重命名 |
| 文件夹 | 创建、删除、编辑、重命名 |
| 表单 | 批准、克隆、创建、删除、草稿创建、编辑、移动、重命名 |
| 表单 — >登陆页面表单 | 创建、克隆、编辑、删除、批准、重命名 |
| 登陆页面 | 批准、克隆、创建、删除、草稿放弃、编辑、重命名、取消批准 |
| 登陆页面模板 | 批准、克隆、创建、删除、草稿创建、草稿放弃、编辑、重命名、取消批准 |
| 智能列表 | 克隆、创建、删除、编辑、导出、修改智能列表设置、重命名 |
| 营销文件夹 | 创建、编辑、删除 |
| 培养项目 | 克隆、创建、删除、编辑通道、修改程序设置、修改程序流、修改程序令牌、重命名 |
| 区段 | 创建、删除、编辑、重命名 |
| 分段 | 批准、创建、删除、草稿创建、草稿丢弃、重命名、取消批准 |
| 智能活动 | 中止、激活、克隆、创建、停用、删除、编辑、修改营销活动计划、修改流程步骤操作、修改智能列表设置、移动、重命名 |
| 代码段 | 批准、批准（无草稿）、克隆、创建、删除、编辑、重命名、取消批准 |
| 管理员UI -> Launchpoint ->集成 | 创建、删除、编辑 |
| 管理员UI ->用户 | 创建、编辑、删除（仅适用于API用户） |
| 管理员登录 — >用户 | 登录成功，登录失败 |
| 程序 — >电子邮件批处理程序 | 编辑（用于更改选定的电子邮件地址）资产API |
| 计划 — >营销计划 | 创建，克隆 |

用户审核事件示例：

```json
{
    "event_id": "a1b2c3d4-zyxw-9876-9z8y-a1b2c3d4e5f6",
    "event": {
        "specversion": "1.0",
        "id": "b77c743a-8e28-40f2-8aab-9541bbc85e68",
        "type": "com.adobe.platform.marketo.audit.user.email",
        "source": "https://www.marketo.com",
        "time": "2020-05-28T19:20:47.28Z",
        "datacontenttype": "application/json",
        "dataschema": "V1.0",
        "data": {
            "componentId": 232459,
            "componentType": "Email",
            "eventAction": "approve",
            "munchkinId": "123-ABC-456",
            "imsOrgId": "ADOBEORGID@AdobeOrg",
            "user": 253,
            "userId": "example@marketo.com"
        }
    }
}
```

## 通知数据流概述

通知数据流作为Marketo Engage性能级别产品的一部分提供。

Marketo通知中心可以向某个电子邮件地址发送通知。 Notification Data Stream还会通过Adobe I/O Events将这些通知发送到可配置的端点。 这些通知与从Marketo UI的铃铛图标中显示的通知相同。

通知事件列表：

| 组件 | 事件类型列表 |
| --- | --- |
| 通知 | campaign中止、campaign失败、nurture（项目已耗尽）、salesforce同步失败、测试组（A/B测试结果）、web服务（每日配额） |

通知事件示例：

```json
{
    "event_id": "a1b2c3d4-zyxw-9876-9z8y-a1b2c3d4e5f6",
    "event": {
        "specversion": "1.0",
        "type": "com.adobe.platform.marketo.notification.campaign_abort",
        "source": "https://www.marketo.com",
        "time": "2021-05-27T10:22:37.489-5:00",
        "datacontenttype": "application/json",
        "dataschema": "V1.0",
        "data": {
            "componentType": "campaign_abort",
            "subType": "user_campaign_abort",
            "eventAction": {
                "campaignId":1234,
                "userId":"example@marketo.com",
            }
            "imsOrgId":"ADOBEORGID@AdobeOrg",
            "munchkinId":"123-ABC-456"
        }
    }
}
```

## 技术详细信息

以下部分介绍了从每个流接收数据所需的配置。 每个流都需要端点设置和集成代码。

### 潜在客户活动数据流

Lead Activity Stream发送具有以下服务特征的已订阅的lead activity事件：

- 默认情况下，每两秒推送一次数据。
- 每个订阅使用100-500条记录的批次。
- 客户REST服务具有20秒的超时时间和三分钟间隔的三次重试。 成功的重试将自动启用该服务。 在三次失败后，服务将暂停并每三分钟重试一次，除非手动取消设置。
- 已排队的数据最多可保留7天。

要实施潜在客户活动数据流，请执行以下操作：

1. 公开可以从公共Internet接收带有JSON正文的POST请求的HTTP端点。 活动推送数据流将请求发送至：
1. 向Adobe提供以下信息：
   1. 适用于其订阅的Marketo Munchkin ID
   1. 步骤1中的端点的URL
   1. 他们希望收到的活动类型（以上完整的列表）
   1. 一种身份验证方法，以便客户可以验证请求是否合法。 可以：
      1. OAuth [客户端凭据身份验证](https://www.oauth.com/oauth2-servers/access-tokens/client-credentials/)的标识提供程序URL、客户端ID和客户端密钥
      1. API令牌，可包含在授权http标头中由潜在客户活动数据流发送的请求中

Adobe会在收到所需信息后启用数据流。 然后，端点开始接收数据。

典型潜在客户活动数据流调用的UML图：

![潜在客户活动数据流图](assets/lead-activity-data-stream.png)

URL端点创建示例：

```javascript
/*
Copyright 2022 Adobe
All Rights Reserved.

NOTICE: Adobe permits you to use, modify, and distribute this file in
accordance with the terms of the Adobe license agreement accompanying
it.
*/
constexpress=require('express')
constwinston=require('winston');
constport=3000

constapp=express().use(express.json())

constlogger=winston.createLogger({
  level: 'info',
  format: winston.format.json(),
  defaultMeta: {service: 'activity-stream-consumer-example'},
  transports: [
    // - Write all logs with level `error` and below to `error.log`
    newwinston.transports.File({filename: 'error.log',level: 'error'}),
    // - Write all logs with level `info` and below to `combined.log`
    newwinston.transports.File({filename: 'combined.log'}),
    newwinston.transports.Console({format: winston.format.simple()})
  ],
});

app.get('/',(req,res)=>{
  logger.info(JSON.stringify(req.query))
  res.sendStatus(200)
})

app.post('/',(req,res)=>{
  logger.info(JSON.stringify(req.body))
  res.sendStatus(200)
})

app.listen(port,()=>{
  logger.info(`app listening on port ${port}`)
})
```

有关示例应用程序代码，请参阅[潜在客户活动数据流使用者示例](https://github.com/ihgrant/activity-stream-consumer-example)。

### 用户审核数据流和通知数据流

用户审核事件通过Adobe I/O发送。要通过Adobe ID使用受众，请执行以下操作：

1. 向Adobe提供以下信息：
   1. Adobe ID
   1. 适用于其订阅的Marketo Munchkin ID
1. 公开REST端点（通常是webhook）以使用事件。
1. 在收到端点信息后，Adobe将为订阅启用流。
1. 在Adobe I/O中配置流。
   1. 此步骤需要使用Adobe组织
   1. 要求Adobe组织用户具有开发人员或系统管理员角色

要配置Adobe I/O，请参阅[使用Adobe I/O设置Marketo用户审核数据流](https://developer.adobe.com/events/docs/guides/using/marketo/marketo-user-audit-data-stream-setup#)。

### 在Marketo中设置用户审核数据流

用户审计数据流当前作为性能包及其他3个数据流的一部分提供。 有关软件包的详细信息，请参阅[产品描述页](https://helpx.adobe.com/cn/legal/product-descriptions/adobe-marketo-engage---product-description.html)以了解产品限制和功能。

### 设置Adobe I/O

[请参阅Adobe I/O Events快速入门](https://developer.adobe.com/runtime/docs/guides/getting-started/)

有关此用例的基本说明，请从[console.adobe.io](https://developer.adobe.com/console)开始：

出现提示时，选择&#x200B;**[!UICONTROL Create New Project]**&#x200B;或&#x200B;**[!UICONTROL Add Event]**。

### 新项目入门

要开始使用Adobe服务，请添加API、事件或运行时，查看我们的[文档](https://developer.adobe.com/runtime/docs/)。

## 公共文档

- [Marketo数据流](https://developer.adobe.com/events/docs/guides/using/marketo/marketo-data-streams/)
- [Adobe IO活动和Webhook简介](https://developer.adobe.com/events/docs/guides/)
- [Data Streams博客](https://blog.developer.adobe.com/introducing-the-adobe-marketo-engage-data-streams-61198b567fbb)
