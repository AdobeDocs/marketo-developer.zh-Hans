---
title: 启用深层链接
feature: Mobile Marketing
description: 了解如何使用自定义URI方案以及iOS、Android和PhoneGap指导和最佳实践，在您的应用程序中为Marketo推送消息启用深层链接。
exl-id: c3647416-d81d-4f15-b660-bcb3e54cb9bc
TQID: https://experienceleague.adobe.com/UswOvHXGlfTrTUqr4Gsf3j2Z7Xpv2FF2luXeygT4qE0
product_v2: id: b27e5950-9033-45ac-9f86-eb22e567f615
feature_v2: id: f71e690b-4480-4b67-9ef5-88f42f9cdfdb
role_v2: id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
source-git-commit: 3e6d310c5aec1a3435424fb122b71d825db5af0e
workflow-type: tm+mt
source-wordcount: 363
ht-degree: 1%

---

# 启用深层链接

深层链接会将用户定向到您的应用程序中的特定内容。 例如，当人员选择一条展示紫色T恤的移动推送消息时，应用程序可以打开紫色T恤内容而不是主页。

此过程如下所示：

1. Marketo用户在推送消息的点按操作中放置自定义URI。
1. 当人员在设备上点按推送消息时，Marketo MME SDK会触发一个具有自定义URI的事件。
1. 您的应用程序会处理该事件，并将用户引导至相应的内容。

要启用此流程，请执行以下操作：

1. 为您的应用程序定义自定义URI结构。
1. 在应用程序清单中注册方案。
1. 添加用于处理深层链接事件并将人员路由到相应内容的代码。

对于iOS，请参阅有关[为您的应用程序定义自定义URL方案](https://developer.apple.com/documentation/xcode/defining-a-custom-url-scheme-for-your-app)的Apple文档。

对于Android，请参阅有关[启用应用程序内容的深层链接](https://developer.android.com/training/app-links/deep-linking)的Google文档。

对于PhoneGap应用程序，请使用插件使您的混合应用程序能够响应iOS和Android上的自定义URL方案和通用/应用程序链接。 查看可用的[深层链接插件](https://cordova.apache.org/plugins/?q=deeplink)。

在应用程序中启用深层链接后，请将自定义URI与Marketo用户共享，以便他们能够将这些URI插入到推送消息的点按操作中。

设置测试设备时，Marketo使用预定义的URI结构。 有关详细信息，请参阅[安装指南](installation.md)中的“测试设备”。

## 定义URI结构的最佳实践

如果您的品牌具有移动网站，请在定义深层链接URI时遵循其URL结构。 例如，如果产品URL为`https://myappname.com/products/purple-shirt`，则使用`myappname://products/purple-shirt`作为对应的深层链接URI。

使用您的品牌所独有的方案。 虽然没有任何法规要求方案具有全局唯一性，但您可以通过反转域名（如`org.companyname`）来帮助创建唯一的方案。
