---
title: 错误数
feature: Webhooks
description: 了解Marketo webhook错误代码，为什么更新潜在客户字段需要2xx响应，以及如何使用Webhook捕获和处理错误。
exl-id: adce40c3-87b1-4f31-8995-eb64e8a72b55
TQID: https://experienceleague.adobe.com/N2jNA4EUMMTUFL9uJHZhOor6Tlz4-EXWciwoXrPml48
product_v2:
  - id: b27e5950-9033-45ac-9f86-eb22e567f615
role_v2:
  - id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
source-git-commit: 00118a89f25a23b931fac671130932bb0e0e4e4e
workflow-type: tm+mt
source-wordcount: 252
ht-degree: 1%

---

# 错误数

本页列出了Marketo中Webhook的错误响应代码。

1000和1001由Marketo生成，2xx到5xx是从Marketo webhook调用的系统返回的错误。

要使Marketo将值映射回字段，webhook响应代码必须为2xx类型。 如果webhook的意图是通过响应更改Marketo潜在客户记录中的值，那么调用的Web服务必须返回2xx，则所有其他响应代码都会导致webhook被忽略以便更新潜在客户记录值。

| 响应代码 | 描述 |
| --- | --- |
| 1000 | 这表示“调用Webhook”流操作存储在批处理营销活动中。 Webhook只能从触发营销活动中触发。 |
| 1001 | 这表示Web服务发出空的响应正文。 |

## 捕获Webhook错误

来自Webhook的错误可由&#x200B;**[!UICONTROL Webhook is Called]**&#x200B;触发器捕获和处理：

![已调用Webhook](assets/webhook-called.png)

* **Response** - Response是请求收到的文字响应有效负载。
* **错误类型** — 这对应于HTTP状态消息的原因短语。

这些资源可用于处理和响应可预测的错误和异常。 根据您与集成的服务的不同，可能会自动恢复某些类别的错误，同时还可以创建警报以通知用户发生意外错误。
