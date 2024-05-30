---
title: "触发器"
description: "触发器"
feature: Javascript
source-git-commit: d335bdd9f939c3e557a557b43fb3f33934e13fef
workflow-type: tm+mt
source-wordcount: '80'
ht-degree: 8%

---


# 触发器

添加在全局rtp对象的某些状态上触发函数的功能。

您必须是Web个性化客户并且具有 [已部署RTP标记](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/web-personalization/rtp-tag-implementation/deploy-the-rtp-javascript) 使用用户上下文API之前，在您的网站上执行以下操作。

## 使用情况

`rtp('triggerName', function_to_trigger);`

| 参数 | 可选/必填 | 类型 | 描述 |
|---------------------|-------------------|----------|----------------------|
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
