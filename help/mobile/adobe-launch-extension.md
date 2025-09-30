---
title: 适用于 [!DNL Adobe Launch]的Marketo Mobile扩展
feature: Mobile Marketing
description: 在适用于Marketo和Android的Adobe Launch中安装和配置iOS Mobile SDK扩展，包括设置推送通知和应用程序内消息。
exl-id: 2f8691ff-0442-45a5-aeba-c91c3af5c711
source-git-commit: 7557b9957c87f63c2646be13842ea450035792be
workflow-type: tm+mt
source-wordcount: '287'
ht-degree: 1%

---

# [!DNL Adobe Launch]的Marketo Mobile扩展

[!DNL Adobe Launch]中Marketo Mobile SDK扩展的安装说明。 发送推送通知和/或应用程序内消息需要执行以下步骤。

## 先决条件

- [在Marketo Admin中添加应用程序](https://experienceleague.adobe.com/zh-hans/docs/marketo/using/product-docs/mobile-marketing/admin/add-a-mobile-app)&#x200B;(获取应用程序密钥和Munchkin ID)
- 按照[!DNL Adobe Launch]门户中提供的说明进行安装
- [设置推送通知](push-notifications.md)（可选）

## iOS

### 设置Swift桥接标头

1. 转到“文件”>“新建”>“文件”，然后选择“头文件”。
1. 将文件命名为“&lt;_ProjectName_>-Bridging-Header”。
1. 转到“项目”>“Target”>“构建阶段”>“Swift编译器”>“代码生成”。 将以下路径添加到目标桥接标头：

   `$(PODS_ROOT)/<_ProjectName_>-Bridging-Header.h`

对于Swift用户：删除以下import语句，因为在上述步骤中添加了桥接标头。

`import Marketo/ALMarketo`

### iOS测试设备

按照[添加iOS测试设备](installation.md#ios_test_devices)中的说明操作

### 在AppDelegate中处理自定义Url类型

在[此处](installation.md#ios_test_devices)按照说明操作

### 在iOS中设置推送通知

按照[此处](push-notifications.md)的说明进行操作，并使用类名称“ALMarketo”而不是“Marketo”

## Android

### 配置权限

打开`AndroidManifest.xml`并添加以下权限。 您的应用程序必须请求“INTERNET”和“ACCESS_NETWORK_STATE”权限。 如果您的应用程序已经请求这些权限，请跳过此步骤。

```xml
<uses‐permission android:name="android.permission.INTERNET"></uses‐permission>
<uses‐permission android:name="android.permission.ACCESS_NETWORK_STATE"></uses‐permission>
```

### ProGuard配置（可选）

如果您正在使用应用程序的ProGuard，请在`proguard.cfg`文件中添加以下行。 该文件位于您的项目文件夹中。 添加此代码会将Marketo SDK排除在模糊处理过程之外。

```
-dontwarn com.marketo.*
-dontnote com.marketo.*
-keep class com.marketo.**{ *; }
```

### Android测试设备

在[此处](installation.md#android_test_devices)按照说明操作

## 在Android中设置推送通知

按照[此处](installation.md#android_firebase_cloud_messaging_support)的说明进行操作，并使用类名称“ALMarketo”而不是“Marketo”

若要设置用户配置文件，请按照[此处](user-profiles.md)的说明操作；若要自定义操作，请按照[此处](custom-actions.md#android_custom_action)的说明操作。 在下面的说明中，使用类名称“ALMarketo”而不是“Marketo”
