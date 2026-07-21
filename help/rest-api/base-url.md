---
title: 基本 URL
feature: REST API
description: 了解如何构建Marketo REST API请求、了解基本URL路径资源和参数，以及查找唯一的基本URL。
exl-id: 6c3f122c-3ace-4ed3-bed0-a6b89cedc99a
TQID: https://experienceleague.adobe.com/NZisV6V-FMPi0RHpdaFrc1kZc3nb15YomwRgohaQmEE
product_v2:
  - id: b27e5950-9033-45ac-9f86-eb22e567f615
role_v2:
  - id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
source-git-commit: 3e6d310c5aec1a3435424fb122b71d825db5af0e
workflow-type: tm+mt
source-wordcount: 142
ht-degree: 3%

---

# 基本 URL

[终结点引用](endpoint-reference.md)中的每个API调用都指定了REST方法、路径、资源和参数。 将这些组件附加到基本URL以形成请求。

以下是格式正确的REST URL示例：

`https://284-RPR-133.mktorest.com/rest/v1/lead/318581.json?fields=email,firstName,lastName`

此示例包含以下组件：

- **基本URL：** `https://284-RPR-133.mktorest.com/rest`
- **路径：** `/v1/lead/`
- **资源：** `318582.json`
- **查询参数：** `fields=email,firstName,lastName`

基本URL包含帐户ID（也称为Munchkin ID），并且对于每个Marketo订阅都是唯一的。

要查找基本URL，请登录到Marketo并转到&#x200B;**[!UICONTROL Admin]** > **[!UICONTROL Integration]** > **[!UICONTROL Web Services]**。 基本URL在“REST API”部分中被标记为“端点：”，如下图所示。

![Web服务基本URL终结点](assets/rest-api-base-url-web-services.png)

复制基本URL并将其包含在每个REST API调用的URL中。
