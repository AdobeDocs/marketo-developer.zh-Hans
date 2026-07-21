---
title: 应用程序内消息
feature: Mobile Marketing
description: 使用Mobile SDK设置Marketo应用程序内消息，配置自定义事件触发器，跟踪点按活动，以及修复第一个应用程序打开初始化问题。
exl-id: 73c9f862-d154-4b37-94ce-92311aa756e8
TQID: https://experienceleague.adobe.com/RVkEUBaFb-PHd0gE9ngzYc5zOojINwSI7ic2TmcU7-8
product_v2:
  - id: b27e5950-9033-45ac-9f86-eb22e567f615
role_v2:
  - id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
topic_v2:
  - id: c1579802-ddd4-4214-8a91-97b2066abe11
source-git-commit: 3e6d310c5aec1a3435424fb122b71d825db5af0e
workflow-type: tm+mt
source-wordcount: 321
ht-degree: 2%

---

# 应用程序内消息

完成以下步骤以使用Marketo应用程序内消息传送：

1. 按照[Mobile Installation](installation.md)中的说明安装Marketo Mobile SDK。
1. 将您的移动应用程序添加到Marketo，如[添加移动应用程序](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/mobile-marketing/admin/add-a-mobile-app)中所述。
1. 可选：向移动应用添加代码以捕获[自定义操作](custom-actions.md)。

安装Marketo Mobile SDK并将您的应用程序添加到Marketo后，您可以发送在用户打开您的应用程序时显示的应用程序内消息。

默认情况下，应用程序打开时会触发应用程序内消息。 要触发其他事件的消息（例如查看特定页面或选择特定按钮），请向代码添加自定义操作。 有关代码示例，请参阅[自定义操作](custom-actions.md)。

## 故障排除

**应用程序内消息未显示**

仅当使用Marketo平台初始化Marketo Mobile SDK后，Marketo才会响应应用程序触发器。 首次安装和打开应用程序时进行初始化。

由于初始化是在第一个应用程序打开后进行的，因此只有在第二次打开应用程序时才会触发“应用程序打开”事件。 关闭并重新打开应用程序。 随后设备上应会显示一条由“应用程序打开”触发的消息。

自定义事件在应用程序打开后通过用户交互触发。 自定义事件在首次会话期间由Marketo识别。

**应用程序内点击活动跟踪**

要跟踪点按活动并根据点按次数确定显示频率，请将“关闭”以外的操作分配给主按钮或辅助按钮。

有关详细信息，请参阅产品文档中的[应用程序内消息](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/mobile-marketing/in-app-messages/creating-in-app-messages/create-an-in-app-message)。
