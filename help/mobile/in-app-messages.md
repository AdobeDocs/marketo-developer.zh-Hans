---
title: "应用程序内消息"
feature: "Mobile Marketing"
description: “应用程序内消息概述”
source-git-commit: e8bb45a7b3bee71c3d0ab6117296a75c8959d72e
workflow-type: tm+mt
source-wordcount: '293'
ht-degree: 0%

---


# 应用程序内消息

要使用Marketo的应用程序内消息传送功能，您必须执行以下步骤：

1. 安装Marketo Mobile SDK，如中所述 [移动设备安装](installation.md).
1. 将您的移动应用程序添加到Marketo，如中所述 [添加移动应用程序](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/mobile-marketing/admin/add-a-mobile-app).
1. （可选）将代码添加到要捕获的移动应用程序 [自定义操作](custom-actions.md).

安装Marketo Mobile SDK并在Marketo中添加完您的应用程序后，便可以发送应用程序内消息，该消息将在用户打开您的应用程序时显示。

默认情况下，应用程序内消息会在应用程序打开时触发。 如果要触发其他事件的应用程序内消息，例如查看特定页面或按下特定按钮时，您必须将自定义操作添加到代码中。 请参阅 [自定义操作](custom-actions.md) 部分，了解相关的代码示例。

## 故障排除

**未显示应用程序内消息**

仅当使用Marketo平台初始化Marketo Mobile SDK后，Marketo才会响应来自应用程序的触发器。 当您首次安装和打开应用程序时，会发生初始化过程。 由于初始化在第一个应用程序打开后发生，因此直到再次打开应用程序才会触发“应用程序打开”事件。 关闭应用程序并再次将其打开，则设备上应会显示由“应用程序打开”触发的消息。

自定义事件在应用程序打开后通过用户交互触发。 自定义事件在首次会话期间由Marketo识别。

**应用程序内点按活动跟踪**

确保将除“取消”之外的操作分配给主要按钮或次要按钮之一，以跟踪点按活动并根据点按次数使用基本显示频率。

欲了解更多信息，请参见 [应用程序内消息](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/mobile-marketing/in-app-messages/creating-in-app-messages/create-an-in-app-message) 部分。
