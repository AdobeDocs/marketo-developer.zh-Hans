---
title: 启用深层链接
feature: Mobile Marketing
description: 了解如何使用自定义URI方案以及iOS、Android和PhoneGap指导和最佳实践，在您的应用程序中为Marketo推送消息启用深层链接。
exl-id: c3647416-d81d-4f15-b660-bcb3e54cb9bc
source-git-commit: 7557b9957c87f63c2646be13842ea450035792be
workflow-type: tm+mt
source-wordcount: '397'
ht-degree: 0%

---

# 启用深层链接

深层链接允许您将用户重定向到应用程序中的特定内容（资源）。 例如，当某人单击某条展示紫色T恤衫的移动推送消息时，您可以直接打开该应用程序以显示紫色T恤衫内容（而非主页）。

此过程如下所示：

1. Marketo用户在其推送消息的点按操作中放置自定义URI。
1. 当人员在设备上点按推送消息时，Marketo MME SDK会触发一个具有自定义URI的事件。
1. 然后，您的应用程序会处理该事件，并将用户重定向到您的应用程序中的相应内容。

这要求您为应用程序定义自定义URI结构；在应用程序的清单中注册该方案；然后添加代码以处理深层链接事件并路由到应用程序中的适当位置。

对于iOS，请参阅有关[为您的应用程序定义自定义URL方案](https://developer.apple.com/documentation/xcode/defining-a-custom-url-scheme-for-your-app)的Apple文档。

对于Android，请参阅有关[启用应用程序内容的深层链接](https://developer.android.com/training/app-links/deep-linking)的Google文档。

对于PhoneGap应用程序，深层链接不像本机iOS或Android应用程序那么直接，但有一些插件可让您的混合应用程序对iOS和Android上的深层链接自定义URL方案以及通用/应用程序链接做出响应。 考虑[这些插件](https://cordova.apache.org/plugins/?q=deeplink)。

在应用程序中启用深层链接后，请将自定义URI与Marketo用户共享，以便他们能够将这些URI插入到推送消息的点按操作中。

设置测试设备时，Marketo使用预定义的URI结构。 有关详细信息，请参阅[安装指南](installation.md)的“测试设备”部分。

## 定义URI结构的最佳实践

如果您的品牌现有移动网站，则最佳实践为也遵循深层链接URI的URL结构。 例如，如果`https://myappname.com/products/purple-shirt`是相关产品的网站地址，则`myappname://products/purple-shirt`将是可在您的应用程序中使用的良好深层链接URI结构。

通常，您的方案应该特定于您的品牌。 虽然目前没有法规规定方案应具有全球唯一性，但一种有助于确保方案唯一性的方法是：反转域名（例如，`org.companyname`）。
