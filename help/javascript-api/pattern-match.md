---
title: 模式匹配
description: 使用RTP rtp.checkPattern实用程序测试带有百分比通配符的字符串模式，请参阅同步限制、使用和URL示例以及所需的RTP标记设置。
feature: Javascript
exl-id: 4ebd13e3-375b-449b-850f-3b18f570ca75
TQID: https://experienceleague.adobe.com/-HopUg6-2EchL9kJrPDbz62mRlrqYaXYdufILjkvP1Y
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
source-wordcount: 188
ht-degree: 4%

---

# 模式匹配

RTP提供了一个实用程序函数，用于检查模式是否与字符串匹配。 该实用程序会同步返回匹配结果，不能异步使用。

您必须是Web Personalization客户并在您的网站上部署[RTP标记](https://experienceleague.adobe.com/zh-hans/docs/marketo/using/product-docs/web-personalization/rtp-tag-implementation/deploy-the-rtp-javascript)，然后才能使用用户上下文API。

## 使用情况

> rtp.checkPattern(check_about， pattern)；

| 参数 | 可选/必填 | 类型 | 描述 |
| --- | --- | --- | --- |
| check_about | 必需 | 字符串 | 匹配模式的字符串，例如当前页面URL或产品名称。 |
| 模式 | 必需 | 字符串 | 要匹配的模式。 添加`%`作为通配符以匹配字符串的开始、结束或内容。 忽略`%`以进行完全匹配。 |

## 示例

当当前页面URL以“productA”结尾时，此示例在索引1处设置自定义变量。

```javascript
if (rtp.checkPattern(window.location.href, '%productA')) {
    rtp('set', 'custom1', 'productA');
}
```

在以下示例中，当前URL路径为“/products/productB”。 此示例检查路径是否包含“products”，然后设置一个自定义变量。

```javascript
var currentURLPath = '/products/productB';
if (rtp.checkPattern(currentURLPath, '%products%')) {
    rtp('set', 'custom1', 'products');
}
```
