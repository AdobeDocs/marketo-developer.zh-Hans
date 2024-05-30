---
title: "模式匹配"
description: "模式匹配"
feature: Javascript
source-git-commit: d335bdd9f939c3e557a557b43fb3f33934e13fef
workflow-type: tm+mt
source-wordcount: '134'
ht-degree: 5%

---


# 模式匹配

RTP公开一个实用程序函数，以检查模式是否与特定字符串匹配。 该实用程序不能在异步中使用，因为它返回了是否匹配的指示。

您必须成为Web个性化客户并拥有 [已部署RTP标记](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/web-personalization/rtp-tag-implementation/deploy-the-rtp-javascript) 使用用户上下文API之前，将其放在您的网站上。

## 使用情况

> rtp.checkPattern(check_about， pattern)；

| 参数 | 可选/必填 | 类型 | 描述 |
|---|---|---|---|
| check_about | 必需 | 字符串 | 匹配模式的字符串。 例如：当前页面URL、产品名称。 |
| 模式 | 必需 | 字符串 | 添加%作为通配符。 模式可以是：start withend withcontainsfull match |


## 示例

如果当前页面URL以“productA”结尾，则在索引1中设置自定义变量。

```javascript
if (rtp.checkPattern(window.location.href, '%productA')) {
    rtp('set', 'custom1', 'productA');
}
```

当前URL路径为“/products/productB”。 此示例检查路径是否包含“products”并设置自定义变量。

```javascript
var currentURLPath = '/products/productB';
if (rtp.checkPattern(currentURLPath, '%products%')) {
    rtp('set', 'custom1', 'products');
}
```
