---
title: Webhooks
feature: Webhooks
description: 了解如何配置Marketo Webhook以调用第三方服务、设置有效负荷模板、编码、响应映射、令牌、自定义标头和提示。
exl-id: fd283c66-05a1-4aa4-8412-0d41b8d1e3c8
TQID: https://experienceleague.adobe.com/r-GpAqhYPKvlDtMw5l23jeJWzlSqycP65eYJPA3m9EM
product_v2:
  - id: b27e5950-9033-45ac-9f86-eb22e567f615
feature_v2:
  - id: a7170d27-32ab-462b-a333-269abc654483
  - id: b13bd2ad-8e65-49e5-9691-2a0d31067b35
  - id: d1d0a9cd-295d-4976-8c39-ddae266f240e
  - id: f82558ea-6af5-44eb-a424-5b3389abb0a3
subfeature_v2:
  - id: ad89fb33-8541-4339-afe7-bb13d1633714
  - id: fc9b09fe-b844-4544-887b-e420c3b82065
role_v2:
  - id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
topic_v2:
  - id: eddd9b14-83bd-4ff4-9072-54a4a484abb7
source-git-commit: 3e6d310c5aec1a3435424fb122b71d825db5af0e
workflow-type: tm+mt
source-wordcount: 590
ht-degree: 3%

---

# Webhooks

Marketo webhook与第三方Web服务进行通信。 webhook使用GET或POST HTTP动词向特定URL发送数据或从中检索数据。

有关创建webhook并将其添加到Smart Campaign的说明，请参阅：

- [创建 Webhook](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/administration/additional-integrations/create-a-webhook)
- [调用 Webhook](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/core-marketo-concepts/smart-campaigns/flow-actions/call-webhook)
- [在智能营销活动中使用 Webhook](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/core-marketo-concepts/smart-campaigns/flow-actions/use-a-webhook-in-a-smart-campaign)

使用以下属性配置每个webhook：

- **[!UICONTROL URL]** — 您向其提交Web服务请求的URL。
- **[!UICONTROL Request Type]** - HTTP方法。
- **[!UICONTROL Payload Template]** - POST正文中发送的信息模板。 使用支持HTTP POST的任何数据格式，包括XML、JSON或SOAP。 序列化格式必须允许在字符串周围使用双引号。 要插入令牌，请选择&#x200B;**[!UICONTROL Insert Token]**。 Marketo会自动将字符串类型的令牌用双引号引住。
- **[!UICONTROL Request Token Encoding]** — 请求格式JSON或表单/URL，用于对包含特殊字符（如&amp;符号、&#39;&amp;&#39;）的令牌值进行编码。 选择正确的主体编码，以便webhook正确与Web服务通信。
- **[!UICONTROL Response Type]** — 响应格式，JSON或XML。 选择正确的类型以将响应属性映射到Marketo中的潜在客户字段。
- **[!UICONTROL Custom Headers]** — 通过&#x200B;**[!UICONTROL Webhooks Actions]** > **[!UICONTROL Set Custom Header]**&#x200B;添加为HTTP标头的键值对。 您可以添加任意数量的自定义标头。

使用[响应映射](response-mappings.md)将数据从Web服务响应写入回潜在客户。

## 令牌

所有传出webhook字段（包括URL、模板和自定义标头）都会填充与流程步骤相同的上下文中的令牌内容。

商机和系统令牌始终可用。 触发器、营销策划和项目令牌在其各自的范围中可用。 有关更多信息，请参阅：

- [令牌概述](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/demand-generation/landing-pages/personalizing-landing-pages/tokens-overview)
- [系统令牌术语表](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/email-marketing/general/using-tokens/system-tokens-glossary)
- [有趣时刻的令牌](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/marketo-sales-insight/msi-for-salesforce/features/tabs-in-the-msi-panel/interesting-moments/trigger-tokens-for-interesting-moments)

例如，当项目或营销策划映射到第三方资源时，请在项目级别将ID设置为`My Token`。 然后，将ID作为令牌传递到webhook请求中。

## 自定义标题

Webhook可以通过传出请求发送任意数量的自定义标头字段。 通过&#x200B;**[!UICONTROL Webhooks Actions]** > **[!UICONTROL Set Custom Header]**&#x200B;添加标头。

每个标头都是一个键值对，可以包含令牌。

![自定义标头](assets/custom-headers.png)

## 提示

- 仅在“触发”营销活动中使用“调用Webhook”流程步骤。
- 仅当Web服务返回2xx HTTP响应代码时，响应映射才会更新记录。
- 您可以使用Web服务从内部或外部服务执行自定义数据扩充、验证或标准化。
- Webhook执行时间取决于服务的响应时间，并且可能会导致较长的营销活动执行延迟。 即使某项服务只需50毫秒即可执行，100,000次执行也只需1.5小时。
- 在终止呼叫（也称为超时）之前，Marketo会等待长达30秒的服务呼叫。
- Marketo在写入时传递URL字段中的字符。 例如，“&amp;”作为“&amp;”发送，“%26”作为“%26”发送。
  - 要向收件人服务器发送百分比编码的字符，请明确传递表示该字符的字符串。
