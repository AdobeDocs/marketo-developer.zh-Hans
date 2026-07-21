---
title: Marketo集成最佳实践
feature: REST API
description: Marketo API集成的最佳实践涵盖配额、速率和并发限制、批处理、批量导入和导出、缓存和延迟计划。
exl-id: 1e418008-a36b-4366-a044-dfa9fe4b5f82
TQID: https://experienceleague.adobe.com/Ld-rmFCwKSx-0W2-ceYICu0FQHK8BKAC1QgqtiOWDn4
product_v2: id: b27e5950-9033-45ac-9f86-eb22e567f615
feature_v2: id: b13bd2ad-8e65-49e5-9691-2a0d31067b35id: b3b8a63f-51fc-40f6-a7d2-a31c5d49fb45id: e64968b2-4ee5-47f9-8cae-0588f184b9ebid: f71e690b-4480-4b67-9ef5-88f42f9cdfdb
role_v2: id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
topic_v2: id: df401a2a-327d-468c-a5e4-b7b7ccd071a0
source-git-commit: 3e6d310c5aec1a3435424fb122b71d825db5af0e
workflow-type: tm+mt
source-wordcount: 882
ht-degree: 0%

---

# Marketo集成最佳实践

围绕您的Marketo实例的共享API限制设计集成。 使用批处理、缓存和保守请求率来提高吞吐量和可靠性。

## API限制

- **每日配额：**&#x200B;大多数订阅每天分配的API调用数为50,000。 配额每天凌晨12:00 CST重置。 请与您的客户经理联系以增加每日配额。
- **速率限制：**&#x200B;每个实例限制为每20秒100次API调用。
- **并发限制：**&#x200B;每个实例最多允许10个并发API调用。
- **批次大小：**&#x200B;潜在客户数据库支持300条记录；资产查询支持200条记录。
- **REST API有效负载大小：** 1 MB。
- **批量导入文件大小：** 10 MB。
- **SOAP最大批次大小：** 300条记录。
- **批量提取作业：**&#x200B;两个正在执行，10个已排队，包括。

## 快速提示

- 设置保守的使用限制，因为您的应用程序与其他应用程序共享配额、速率和并发资源。
- 可用时，请使用Marketo批量方法和批处理方法。 仅在必要时使用单记录或单结果调用。
- 使用[指数回退](https://en.wikipedia.org/wiki/Exponential_backoff)重试因速率或并发限制而失败的API调用。
- 避免并发API调用，除非它们有益于您的用例。

## 批处理

对于插入和更新，请将记录分组到尽可能少的事务中。 从数据存储检索记录时，在提交之前聚合记录，而不是为每次更改提交一个请求。

## 可接受的延迟

定义设计集成时可接受的延迟（提交API调用之前的最长时间）。 此选项确定适合用例的Marketo方法和配置选项。

例如，在用户启动试用时通知销售人员的实时集成可能会在需要立即跟进时提交一个的批次。 大多数用例都能够容忍更多的延迟，并通过排队和批处理调用来提高运行效率。

| 可接受的延迟 | 首选方法 | 注释 |
| --- | --- | --- |
| 低（&lt;10秒） | 同步API（批处理或未批处理） | 确保您的用例需要此项。 发送针对高容量用例的即时和同步调用可能会快速占用每日API配额，并且可能超过速率和并发限制。 |
| Medium（1000万 — 6000万） | 同步API（批处理） | 对于与Marketo的入站数据集成，强烈建议使用具有时限和大小限制的队列。 当达到任一限制时，请刷新队列并使用累积记录提交API请求。 这在速度和效率之间实现了强有力的折衷，确保您的请求以所需的节奏发生，同时按队列的保留时间允许的数量批处理记录。 |
| 高（>60米） | 批量导入/导出（如果支持） | 对于入站数据集成，应通过Marketo批量API（在可用时）对记录进行排队和提交。 |

## 每日限制

每个启用了API的Marketo实例每天至少分配了10,000个REST API调用，但通常为50,000个或更多。 每个实例还具有500 MB或更大的“批量提取”容量。 额外的每日容量可作为Marketo订阅的一部分购买，但应用程序设计应考虑常见的订阅限制。

容量由实例中的所有API服务和用户共享。 尽可能减少多余的呼叫和将记录批量记录到最少呼叫中。

最有效的调用导入方法是Marketo批量导入API，可用于[潜在客户/人员](https://developer.adobe.com/marketo-apis/api/mapi#tag/Bulk-Import-Leads/operation/importLeadUsingPOST)和[自定义对象](https://developer.adobe.com/marketo-apis/api/mapi#tag/Snippets/operation/createSnippetUsingPOST)。 Marketo还为[潜在客户](bulk-lead-extract.md)和[活动](bulk-activity-extract.md)提供批量提取。

### 缓存

以下操作的结果通常可以在客户端缓存一天或更长时间，因为它们很少更改：

- Describe操作的结果
- [活动类型](https://developer.adobe.com/marketo-apis/api/mapi#tag/Activities/operation/getAllActivityTypesUsingGET)
- [分区](https://developer.adobe.com/marketo-apis/api/mapi#tag/Leads/operation/getLeadPartitionsUsingGET)

对于潜在客户或活动数据扩充等用例，您还可以缓存资源类型，如程序、电子邮件和文件夹。

## 速率限制

每个Marketo实例都有一个共享速率限制，即所有第三方API服务每20秒100次调用。 如果调用超过此限制，则API返回606错误代码。

通常，将每个第三方集成限制为每20秒或更短时间内50次调用，以便多个API集成和用户可以共享可用容量。 有些用例可能需要达到完整限制。 但是，使用批处理和目标较低吞吐量的应用程序通常响应性更强、一致性更高，延迟略有增加。

## 并发限制

每个Marketo实例都有一个共享限制，即同时执行REST API调用最多只能有10个。 请勿假定您的应用程序是此限制的唯一使用者。

Marketo计算正在处理且尚未返回的调用数。 当调用返回时，它不再计入并发限制。

大多数集成都不会从并发调用中受益。 如果实施并发，则最初将应用程序限制为五个或更少的并发请求。 只有在确定应用程序需要更多资源后，才应提高限制。

## 错误数

除极少数情况外，API请求返回HTTP状态代码200。 业务逻辑错误也返回200，但包括响应正文中的详细信息。 有关详细信息，请参阅[错误代码](error-codes.md)。

请勿评估HTTP原因短语，因为它是可选的并且可能会发生更改。
