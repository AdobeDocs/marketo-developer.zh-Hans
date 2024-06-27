---
title: SOAP API
feature: SOAP
description: Marketo SOAP概述
exl-id: 6618cc82-15ae-4030-aa00-438e635d8369
source-git-commit: 6fc45ff98998217923e2a5b02d00d1522fe3272c
workflow-type: tm+mt
source-wordcount: '230'
ht-degree: 0%

---

# SOAP API

SOAP API不再处于积极开发状态。 虽然调用仍可正常运行，但我们的开发重点在于 [REST](https://developer.adobe.com/marketo-apis/) 往前走。

Marketo SOAP API允许创建、检索和删除存储在Marketo中的实体和数据。 您可以找到 [Marketo-SOAP-SDK](https://github.com/Marketo/SOAP-API-Java-Client) 在GitHub上。 还有 [客户端库](https://github.com/Marketo/Community-Supported-Client-Libraries) 来节省你的时间。

最新API版本： 3_1

## SOAP WSDL

要检索SOAP SOAP WSDL文档，请从您的 **[!UICONTROL Admin]** > **[!UICONTROL Integration]** > **[!UICONTROL Web Services]** 菜单。

![SOAP端点](assets/endpoint-soap.png)

您的WSDL URL为：

`<SOAP API Endpoint> + ?WSDL`

请勿使用WSDL中定义的端点。 每个Marketo实例都有一个唯一的端点，可在其中调用。

## 限制

- **每日配额：** 大多数订阅每天会分配10,000次API调用（这会在每日的12:00 CST重置）。 您可以通过客户经理增加每日配额。
- **速率限制：** 每个实例的API访问限制为每20秒100次调用。
- **并发限制：**  最多十次并发API调用。

我们的建议是批量大小不得大于300。 不支持更大的尺寸，这可能会导致超时，并且在极端情况下会进行限制。

## Marketo中的SOAP API设置

1. 转到 **[!UICONTROL Admin]** 部分并单击 **[!UICONTROL Web Services]**.

![admin-web-services2](assets/admin-web-services2.png)

1. 设置适当的 [!UICONTROL Encryption Key]，单击 **[!UICONTROL Save Changes]** 和使用SOAP API [!UICONTROL Endpoint]， [!UICONTROL User ID]、和 [!UICONTROL Encryption Key] 值以生成正确的 [身份验证签名](authentication-signature.md) 用于每个SOAP API调用。

![admin-web-services3](assets/admin-web-services3.png)
