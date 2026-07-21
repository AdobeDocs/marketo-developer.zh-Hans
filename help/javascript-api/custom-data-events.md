---
title: 自定义数据事件
description: 使用适用于Web Personalization的RTP JavaScript API（包含参数、字符串或数组数据，最多四项）发送自定义事件，以及基于点击的触发器。
feature: Javascript
exl-id: ef7cab9c-3bd0-450e-9247-9324b1e6f9ab
TQID: https://experienceleague.adobe.com/oWDmtMF94xG5HYXeTwkx5zF9PWo98bpwoVB6kAKLYDo
product_v2:
  - id: b27e5950-9033-45ac-9f86-eb22e567f615
feature_v2:
  - id: e2290edd-b061-4880-9d79-dee306cf5aa9
  - id: ed6be6bb-75bb-4ea9-9a42-3bcaa65e1bcc
role_v2:
  - id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
topic_v2:
  - id: b5ce8718-c3af-4fdb-a1a9-fca32f83a87c
  - id: e0eb8757-182f-49f3-94a4-1587d16f5094
source-git-commit: 3e6d310c5aec1a3435424fb122b71d825db5af0e
workflow-type: tm+mt
source-wordcount: 241
ht-degree: 3%

---

# 自定义数据事件

使用此方法可发送用于跟踪和实时个性化的自定义事件。 您可以发送第三方数据，或触发基于访客行为的自定义事件。

在访客会话期间，每个自定义数据事件均会计数一次。

您必须是Web Personalization客户并在您的网站上部署[RTP标记](https://experienceleague.adobe.com/zh-hans/docs/marketo/using/product-docs/web-personalization/rtp-tag-implementation/deploy-the-rtp-javascript)，然后才能使用用户上下文API。

| 参数 | 可选/必填 | 类型 | 描述 |
| --- | --- | --- | --- |
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

自定义数据数组最多可以包含四个元素。 要发送四个以上的元素，请重复调用发送事件API，每次调用不超过4个项目。

```javascript
var customData = {value: ['MyEvent', 'download - example whitepaper']};
rtp('send', 'event', customData);
```

### 根据按钮点击发送事件

此示例会在访客选择按钮下载特定白皮书时发送自定义数据事件。 RTP可以使用事件实时细分这些访客。

然后，网站可以在再点击两次后显示个性化促销活动。 例如，营销策划可以展示与下载的白皮书相关的另一段内容。

```html
<button id="download-whitepaper" onclick="rtp('send', 'event', {value :'download - example whitepaper'})">Download</button>
```
