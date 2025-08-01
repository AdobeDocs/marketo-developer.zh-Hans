---
title: 用户上下文
feature: REST API
description: 用户上下文概述和API描述
exl-id: b8daace2-07a5-4621-aa3a-03fa9f66ea73
source-git-commit: 981ed9b254f277d647a844803d05a1a2549cbaed
workflow-type: tm+mt
source-wordcount: '266'
ht-degree: 5%

---

# 用户上下文

用户上下文JavaScript API跨多个会话公开用户和访客级别的数据，以使用历史用户行为和数据启用高级个性化功能。 该API不仅适用于数据读取，而且还会公开自定义变量，从而允许您将有意义的数据和事件推送到RTP后端以实现高级分段和个性化目的。 其他功能： [触发器](../javascript-api/triggers.md)，[模式匹配](../javascript-api/pattern-match.md)。

- 您必须成为Web Personalization客户并在您的网站上部署[RTP标记](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/web-personalization/rtp-tag-implementation/deploy-the-rtp-javascript)，然后才能使用用户上下文API。
- 用户上下文API是一项功能，Marketo支持部门必须应请求启用它。 启用API后，RTP全局对象下的userContext对象将会公开。

## 用户上下文属性

| 名称 | 类型 | 描述 |
|------------------|-------------|------|
| customVar[1-5] | 字符串 | 自定义数据保存在用户上下文中。 |
| viewedCampaigns | 营销活动ID的逗号分隔字符串 | 查看了当前或以前的访问中的营销活动。 |
| clickedCampaigns | 营销活动ID的逗号分隔字符串 | 在当前或以前的访问中点击促销活动。 |

## 设置自定义变量

将自定义数据添加到用户上下文。

### 使用情况

`rtp('set', 'customVar'[1-5], my_custom_value);`

| 参数 | 可选/必填 | 类型 | 描述 |
|-----------------|-------------------|--------|-----------------|
| &#39;设置&#39; | 必需 | 字符串 | 方法操作。 |
| customVar | 必需 | 字符串 | 自定义变量名称。 |
| my_custom_value | 必需 | 字符串 | 要保存在索引1-5中的自定义变量上的自定义值。 |

注意：自定义变量仅在视图调用中发送到RTP，因此建议在调用视图之前设置自定义变量。 否则，它将仅在下次视图调用中发送。

自定义Var限制

- 自定义变量长度不能超过100个字符。
- Campaign数据限于最近十次访问，每次访问包含十次活动。

### 使用情况

`rtp('set', 'customVar', 'A');`

```javascript
// Set and get customVars
rtp('set', 'customVar1', 'foo');

// Read location
if (rtp.userContext.location.state == 'CA')  {
    // Do something
}

// Check if user viewed campaign id 45:
// The campaign id is exposed in the RTP UI when hovering over a campaign name.
if (rtp.userContext.viewedCampaign('45')) {
    // Do something
}
```
