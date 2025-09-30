---
title: 基本 URL
feature: REST API
description: 了解如何构建Marketo REST API请求、了解基本URL路径资源和参数，以及查找唯一的基本URL。
exl-id: 6c3f122c-3ace-4ed3-bed0-a6b89cedc99a
source-git-commit: 7557b9957c87f63c2646be13842ea450035792be
workflow-type: tm+mt
source-wordcount: '152'
ht-degree: 2%

---

# 基本 URL

每个API调用的[端点引用](endpoint-reference.md)文档显示了必须附加到基本URL以形成请求的REST方法、路径、资源和参数。

以下是格式正确的REST URL示例：

`https://284-RPR-133.mktorest.com/rest/v1/lead/318581.json?fields=email,firstName,lastName`

，包括以下几个部分：

- 基本URL： `https://284-RPR-133.mktorest.com/rest`
- 路径： `/v1/lead/`
- 资源： `318582.json`
- 查询参数： `fields=email,firstName,lastName`

基本URL包含帐户ID(也称为Munchkin ID)，因此每个Marketo订阅的基本URL都是唯一的。 通过登录Marketo并导航到&#x200B;**[!UICONTROL Admin]** > **[!UICONTROL Integration]** > **[!UICONTROL Web Services]**&#x200B;菜单，可找到您的基本URL。 它在“REST API”部分下标记为“端点：”，如以下屏幕截图所示。

![Web服务基本URL终结点](assets/rest-api-base-url-web-services.png)

找到基本URL后，请复制该URL，并将其包含在调用任何REST API时使用的URL中。
