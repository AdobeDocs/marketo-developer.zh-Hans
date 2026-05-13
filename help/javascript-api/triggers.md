---
title: 触发器
description: 在Web Personalization中使用RTP触发器在rtp状态下运行函数，包括userContextReady，以及语法、参数和位置示例。
feature: Javascript
exl-id: 588836fa-1e4d-41f3-aec5-5cd17eb16071
TQID: https://experienceleague.adobe.com/yTz9i4bnD4I0PDAmpnjdD1okYJzd40wriA-2ZzO5OMM
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
source-git-commit: 00118a89f25a23b931fac671130932bb0e0e4e4e
workflow-type: tm+mt
source-wordcount: 116
ht-degree: 8%

---

# 触发器

添加在全局rtp对象的某些状态上触发函数的功能。

您必须是Web Personalization客户并在您的网站上部署[RTP标记](https://experienceleague.adobe.com/zh-hans/docs/marketo/using/product-docs/web-personalization/rtp-tag-implementation/deploy-the-rtp-javascript)，然后才能使用用户上下文API。

## 使用情况

`rtp('triggerName', function_to_trigger);`

| 参数 | 可选/必填 | 类型 | 描述 |
| --- | --- | --- | --- |
| &#39;triggerName&#39; | 必需 | 字符串 | 方法名称。 |
| function_to_trigger | 必需 | 函数 | 要触发的函数。 |

### 用户上下文就绪触发器

根据用户位置设置自定义变量。 当“rtpUserContext”全局对象就绪时，将调用此函数。

```javascript
rtp('userContextReady', function() {
    if (rtpUserContext.location.state == 'CA') {
        rtp('set', 'custom1', 'productA');
    }
});
```
