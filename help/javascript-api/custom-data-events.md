---
title: 自定义数据事件
description: 使用自定义数据事件Javascript API跟踪您的独特事件。
feature: Javascript
exl-id: ef7cab9c-3bd0-450e-9247-9324b1e6f9ab
source-git-commit: e609f9d5d58f656298412acef5e2106a19765396
workflow-type: tm+mt
source-wordcount: '231'
ht-degree: 3%

---

# 自定义数据事件

此方法会发送用于跟踪和实时个性化的自定义事件。 此类数据可用于发送第三方数据，或根据访客行为触发您自己的自定义事件。 自定义数据事件在访客的会话中会计为一次。

您必须成为Web Personalization客户并在您的网站上部署[RTP标记](https://experienceleague.adobe.com/zh-hans/docs/marketo/using/product-docs/web-personalization/rtp-tag-implementation/deploy-the-rtp-javascript)，然后才能使用用户上下文API。

| 参数 | 可选/必填 | 类型 | 描述 |
|---|---|---|---|
| `send` | 必需 | 字符串 | 方法操作。 |
| `event` | 必需 | 字符串 | 方法名称。 |
| `customData` | 必需 | 字符串或数组 | 自定义数据。 |

## 示例

### 使用自定义数据的字符串发送事件

```javascript
var customData = {value: 'MyEvent'};
rtp('send', 'event', customData);
```

### 使用自定义数据的字符串数组发送事件

自定义数据数组最多可包含四个元素。  如果必须发送四个以上的元素，请重复调用发送事件API（最多包含四个项目），直到发送所有项目为止。

```javascript
var customData = {value: ['MyEvent', 'download - example whitepaper']};
rtp('send', 'event', customData);
```

### 根据按钮点击发送事件

Marketo会向下载特定白皮书的Web访客个性化其网站上的内容。 他们通过捕获访客的点击白皮书下载按钮来做到这一点，该按钮会发送自定义数据事件。 RTP实时细分所有点击了下载白皮书按钮的访客，为每位访客显示一个个性化促销活动，可提供2次点击。 这是通过显示与下载的白皮书相关的另一段内容来实现的。

```html
<button id="download-whitepaper" onclick="rtp('send', 'event', {value :'download - example whitepaper'})">Download</button>
```
