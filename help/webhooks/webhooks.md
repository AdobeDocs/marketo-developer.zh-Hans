---
title: Webhooks
feature: Webhooks
description: Webhooks概述
exl-id: fd283c66-05a1-4aa4-8412-0d41b8d1e3c8
source-git-commit: 6fc45ff98998217923e2a5b02d00d1522fe3272c
workflow-type: tm+mt
source-wordcount: '562'
ht-degree: 0%

---

# Webhooks

Marketo允许使用Webhook与第三方Web服务进行通信。 Webhook支持使用GET或POSTHTTP动词从特定URL推送或检索数据。 有关应用程序内创建Webhook以及如何将其添加到智能营销活动的详细说明，请参阅以下文章：

- [创建Webhook](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/administration/additional-integrations/create-a-webhook)
- [调用Webhook](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/core-marketo-concepts/smart-campaigns/flow-actions/call-webhook)
- [在Smart Campaign中使用Webhook](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/core-marketo-concepts/smart-campaigns/flow-actions/use-a-webhook-in-a-smart-campaign)

每个webhook都有以下属性：

- [!UICONTROL URL]  — 输入用于向Web服务提交请求的URL。
- [!UICONTROL Request Type] - HTTP方法。
- [!UICONTROL Payload Template]  — 如果要在POST正文中传输信息，请输入模板。 使用支持HTTPPOST的任何数据格式，包括XML、JSON或SOAP。 序列化格式必须允许在字符串周围使用双引号。 要在模板中插入令牌，请单击 **[!UICONTROL Insert Token]**.  字符串类型的令牌会自动用双引号引住。
- [!UICONTROL Request Token Encoding]  — 如果令牌值包含特殊字符（例如与号、“&amp;”），请指示请求的格式（JSON或表单/Url）。 应为主体选择正确的编码，以确保Webhook与Web服务正确通信。
- [!UICONTROL Response Type]  — 选择您从服务收到的响应的格式（JSON或XML）。 必须选择正确的响应类型，才能将响应的属性映射回Marketo中的潜在客户字段
- [!UICONTROL Custom Headers]  — 访问方式 [!UICONTROL Webhooks Actions] -> [!UICONTROL Set Custom Header]，此菜单允许添加任意数量的自定义键值对作为HTTP标头。

通过使用，可以从Web服务响应将数据写回潜在客户 [响应映射](response-mappings.md)

## 令牌

Webhook中的所有传出字段（URL、模板和自定义标头）都会在流程步骤的同一上下文中填充令牌的内容。 这意味着潜在客户令牌和系统令牌始终可用，而触发器、营销活动和项目令牌则在其各自的范围中可用。 请参阅与令牌相关的文章：

- [令牌概述](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/demand-generation/landing-pages/personalizing-landing-pages/tokens-overview)
- [系统令牌术语表](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/email-marketing/general/using-tokens/system-tokens-glossary)
- [有趣时刻的令牌](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/marketo-sales-insight/msi-for-salesforce/features/tabs-in-the-msi-panel/interesting-moments/trigger-tokens-for-interesting-moments)

这种情况的一个常见情况是，项目或营销策划被显式映射到第三方资源。 可以在项目级别将ID设置为 `My Token`，然后作为令牌传递到Webhook请求中。

## 自定义标题

Webhook允许使用任意数量的自定义标头字段随传出请求一起发送。 可以通过添加这些字段 **[!UICONTROL Webhooks Actions]** > **[!UICONTROL Set Custom Header]**. 每个标头记录为一个简单的键值对。 令牌可在此区域使用。

![自定义标题](assets/custom-headers.png)

## 提示

- 调用Webhook流程步骤仅在触发营销活动中有效。
- 仅当Web服务使用2xx HTTP响应代码进行响应时，才会通过响应映射进行更新。 其他类型的代码将不会导致记录的更新。
- 您可以使用Web服务从内部或外部服务执行自定义数据扩充、验证或标准化。
- Webhook执行时间取决于所用服务的响应时间，这可能会导致较长的营销活动执行延迟。 即使某个服务只需要50毫秒即可执行，当执行100,000次时也需要1.5小时。
- 在终止呼叫（即超时）之前，Marketo会等待长达30秒的服务呼叫。
- URL字段中嵌入的字符在写入时传递，例如“&amp;”作为“&amp;”发送，“%26”作为“%26”发送
   - 如果某个字符在收件人服务器收到时应采用百分比编码，则应将该字符作为表示该字符的字符串显式传递
