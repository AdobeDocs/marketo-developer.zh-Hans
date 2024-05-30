---
title: "SOAP API"
feature: SOAP
description: "Marketo SOAP概述"
source-git-commit: 2185972a272b64908d6aac8818641af07c807ac2
workflow-type: tm+mt
source-wordcount: '242'
ht-degree: 0%

---


# SOAP API

SOAP API不再处于积极开发状态。 虽然调用仍可正常运行，但我们的开发重点在于 [REST](https://developer.adobe.com/marketo-apis/) 往前走。

Marketo SOAP API允许创建、检索和删除存储在Marketo中的实体和数据。 您可以找到 [Marketo-SOAP-SDK](https://github.com/Marketo/SOAP-API-Java-Client) 在GitHub上。 还有 [客户端库](https://github.com/Marketo/Community-Supported-Client-Libraries) 来节省你的时间。

最新API版本： 3_1

## SOAP WSDL

要检索SOAP WSDL文档，请从您的 **[!UICONTROL Admin]** > **[!UICONTROL Integration]** > **[!UICONTROL Web Services]** 菜单。

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

1. 转到“管理员”部分，然后单击“Web服务”。

![admin-web-services2](assets/admin-web-services2.png)

1. 设置适当的加密密钥，单击“保存更改”并使用SOAP API端点、用户ID和加密密钥值生成正确的密钥 [身份验证签名](authentication-signature.md) 用于每个SOAP API调用。

![admin-web-services3](assets/admin-web-services3.png)
