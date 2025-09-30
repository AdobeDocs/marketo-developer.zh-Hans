---
title: 数据流
description: Marketo Engage数据流概述，支持近乎实时的潜在客户活动和用户审核事件，放宽了性能层客户的API限制
exl-id: 5617b6a5-ebc8-4d97-a290-e3b87f83e360
source-git-commit: 7557b9957c87f63c2646be13842ea450035792be
workflow-type: tm+mt
source-wordcount: '1615'
ht-degree: 1%

---

# 数据流

>[!NOTE]
> 有关数据流的当前信息现在位于[使用数据流](https://developer.adobe.com/events/docs/guides/using/marketo/marketo-data-streams/)。
>

我们客户的营销组织依靠及时而集中的营销活动保持业务领先和竞争力。 为了支持快节奏的决策并快速实现战略变革，拥有数据来支持并推动那些能够开展重点突出、目标明确的促销活动的关键决策非常重要。 还有一些客户在Marketo Engage内部和外部对其客户区段级别执行营销工作。 为了支持这些不同的努力，Marketo已创建通过数据流近乎实时地获取大量数据的能力。

除了近乎实时数据的好处之外，还有与产品相关的好处：

- 由于改用流式传输，因此缓解了API限制的瓶颈。
- 减少API限制方案，生成更少的警报消息。
- 由于具有数据流式处理功能，因此无需执行批量导出即可提取数据。

数据流适用于已购买[Marketo Engage性能层包](https://nation.marketo.com/t5/product-documents/marketo-engage-performance-tiers/ta-p/328835)的用户。

## 潜在客户活动数据流概述

潜在客户活动数据流提供审核跟踪Lead活动的近乎实时的流式传输，其中可以将大量Lead活动发送到客户的外部系统。 通过数据流，客户能够有效地审核与销售线索相关的事件、使用模式、提供销售线索变更的视图，并根据不同类型的销售线索事件触发流程和工作流。 超过144种活动类型可以订阅并通过流发送。

流式处理的潜在客户数据的类型：

1. 潜在客户更改 — 所有字段和新潜在客户的所有更改
1. Lead Activities — 文档中介绍的所有Lead活动类型
1. 已删除的潜在客户
1. 商机上的所有自定义对象（如果请求）。 现在要么全是要么全是。

通过提供有关商机变化的视图，客户可以更快地就其总体营销策略做出决策，并创建更有针对性的促销活动。 一些常见用例包括：

- 自定义警报：当发现某些潜在客户具有不一致的条件时，可以将他们添加到列表中。 活动流可以选取这些内容，并为客户推送“添加到列表”活动以进行任何后续操作。
- 支持ML模型：一些客户计划构建使用活动见解的评分模型，并将其反馈给Marketo或根据需要在其自己的内部评分模型中使用。 通过打分Lead，客户可以通知Marketo将客户添加到Nurture营销活动以提高其得分。

流式处理活动列表：

| AchieveGoalInReferral | ClickPredictiveContent | ReceivedForwardToFriendEmail |
|--- |--- |--- |
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

请注意，如果自定义对象应流式处理，则必须是所有与Lead相关的自定义对象。 目前无法选择需要哪些。

## 用户审核数据流概述

用户审核数据流可提供对用户资源更改的近乎实时的审核跟踪&#x200B;。 这使客户能够有效地审核资产事件，提供用户更改的视图，以及根据不同类型的审核事件触发流程或工作流。 近乎实时的资源更改通过Adobe I/O事件发送到可配置的端点。 审核事件按资产类型进行划分，可以订阅对审核事件很重要的审核事件。

订阅此流的良好用例是：

- 使用多个营销系统时跟踪更改：有些客户还会在其他系统(如CRM，如Salesforce)中执行某个级别的营销活动，然后将潜在客户传递到Marketo。 Lead有时会更新并前后同步，因此跟踪哪个系统最近进行了更改非常重要。

流式用户审核事件列表：

| 组件 | 事件类型列表 |
|--- |--- |
| 默认项目 | 克隆、创建、删除、编辑通道、导出、修改程序设置、修改程序令牌、重命名 |
| 电子邮件 | 批准、克隆、创建、删除、编辑、移动、重命名、取消批准 |
| 电子邮件批处理程序 | 批准、子更新、克隆、创建、删除、编辑、编辑通道、修改程序计划、修改程序设置、修改程序令牌、重命名、取消批准 |
| 电子邮件模板 | 批准、克隆、创建、删除、草稿创建、草稿放弃、编辑、重命名、取消批准 |
| 互动项目 | 克隆、创建、删除、编辑通道、修改程序设置、修改程序流、修改程序令牌、重命名 |
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

目前，可以将Marketo中的通知中心配置为将通知发送到电子邮件地址。 通知数据流允许通过Adobe I/O事件将通知直接发送到可配置的端点。 现在，通知通过UI提供，并且可以通过屏幕右上方的橙色铃铛引用，此流接收这些通知并通过流发送它们。

通知事件列表：

| 组件 | 事件类型列表 |
|--- |--- |
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

本节提供有关所需内容、如何连接和接收每个流的流数据的指南。 每个应用程序都涉及一定级别的编码和设置。

### 潜在客户活动数据流

潜在客户活动流提供Marketo潜在客户活动事件的近乎实时的流，并发送具有可配置属性的订阅活动类型更改：

- 默认情况下，每2秒推送一次数据的频率。
- 每个订阅100到500之间的批次。
- 客户REST服务的超时为20秒，每3分钟重试3次，并在成功后自动启用。 否则，它们将暂停。 暂停后，服务会每三分钟重试一次，尝试重新启用，除非手动取消配置。
- 数据在队列中保留长达7天。

要实施潜在客户活动数据流，以下是客户应遵循的步骤：

1. 公开可以从公共Internet接收带有JSON正文的POST请求的HTTP端点。 活动推送数据流将请求发送至：
1. 向Adobe提供以下信息：
   1. 适用于其订阅的Marketo Munchkin ID
   1. 步骤1中的端点的URL
   1. 他们希望收到的活动类型（以上完整的列表）
   1. 一种身份验证方法，以便客户可以验证请求是否合法。 可以：
      1. OAuth [客户端凭据身份验证](https://www.oauth.com/oauth2-servers/access-tokens/client-credentials/)的标识提供程序URL、客户端ID和客户端密钥
      1. API令牌，可包含在授权http标头中由潜在客户活动数据流发送的请求中

然后，Adobe会启用数据流，客户将在该时间点开始接收数据。

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

使用Marketo潜在客户活动数据流的应用程序的代码示例可在[此处](https://github.com/ihgrant/activity-stream-consumer-example)找到。

### 用户审核数据流和通知数据流

用户审核事件将发送到Adobe IO，并可通过使用Adobe ID登录来使用。 以下是需遵循的步骤：

1. 客户向Adobe提供以下信息：
   1. Adobe ID
   1. 适用于其订阅的Marketo Munchkin ID
1. 客户公开REST端点以正常使用webhook形式的事件。
1. 提供该设置后，Adobe将为客户订阅启用流。
1. 然后，客户在Adobe IO中设置流（需要提供的说明）
   1. 此步骤需要使用Adobe组织
   1. 要求Adobe组织用户具有开发人员或系统管理员角色

要设置Adobe IO，请参阅公共文档部分中的[使用Adobe IO设置Marketo用户审核数据流](https://developer.adobe.com/events/docs/guides/using/marketo/marketo-user-audit-data-stream-setup/)。

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
- [数据流博客](https://blog.developer.adobe.com/introducing-the-adobe-marketo-engage-data-streams-61198b567fbb)
