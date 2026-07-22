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
source-git-commit: 3e6d310c5aec1a3435424fb122b71d825db5af0e
workflow-type: tm+mt
source-wordcount: 210
ht-degree: 2%

---

# 错误数

本页介绍了Marketo Webhook的错误响应代码并说明了如何处理Webhook错误。

Marketo生成错误代码1000和1001。 Marketo webhook调用的系统返回2xx到5xx响应代码。

仅当Web服务返回2xx响应代码时，Marketo才会将响应值映射到字段。 如果webhook响应旨在更改Marketo潜在客户记录中的值，则所有其他响应代码会导致Marketo忽略字段更新的响应。

| 响应代码 | 描述 |
| --- | --- |
| 1000 | 这表示“调用Webhook”流操作存储在批处理营销活动中。 Webhook只能从触发营销活动中触发。 |
| 1001 | 这表示Web服务发出空的响应正文。 |

## 捕获Webhook错误

使用&#x200B;**[!UICONTROL Webhook is Called]**&#x200B;触发器捕获和处理webhook错误：

![已调用Webhook](assets/webhook-called.png)

* **响应** — 请求接收的文本响应有效负载。
* **错误类型** - HTTP状态消息的原因短语。

使用这些值可响应可预测的错误和异常。 根据集成的服务，您可以自动从某些错误类中恢复，并为意外错误创建警报。
