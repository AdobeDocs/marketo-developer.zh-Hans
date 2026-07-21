---
title: 适用于 [!DNL Adobe Launch]的Marketo Mobile扩展
feature: Mobile Marketing
description: 在适用于Marketo和Android的Adobe Launch中安装和配置iOS Mobile SDK扩展，包括设置推送通知和应用程序内消息。
exl-id: 2f8691ff-0442-45a5-aeba-c91c3af5c711
TQID: https://experienceleague.adobe.com/Bk5GTnQjm6NDosl5Iw6TS-NRjH8owNRUKoE0mZ-H3pY
product_v2: id: b27e5950-9033-45ac-9f86-eb22e567f615
feature_v2: id: b3b8a63f-51fc-40f6-a7d2-a31c5d49fb45
role_v2: id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
topic_v2: id: d095671a-1355-40aa-8b5f-06c33c68080b
source-git-commit: 3e6d310c5aec1a3435424fb122b71d825db5af0e
workflow-type: tm+mt
source-wordcount: 303
ht-degree: 0%

---

# [!DNL Adobe Launch]的Marketo Mobile扩展

在[!DNL Adobe Launch]中安装Marketo Mobile SDK扩展以发送推送通知和/或应用程序内消息。

## 先决条件

- [在Marketo管理员中添加应用程序](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/mobile-marketing/admin/add-a-mobile-app)并获取该应用程序的密钥和Munchkin ID。
- 按照[!DNL Adobe Launch]门户中的安装说明操作。
- 可选： [设置推送通知](push-notifications.md)。

## iOS

### 设置Swift桥接标头

1. 转到“文件”>“新建”>“文件”，然后选择“头文件”。
1. 将文件命名为“&lt;_ProjectName_>-Bridging-Header”。
1. 转到“项目”>“Target”>“构建阶段”>“Swift编译器”>“代码生成”。
1. 将以下路径添加到目标桥接标头：

   `$(PODS_ROOT)/<_ProjectName_>-Bridging-Header.h`

对于Swift，请删除以下import语句，因为前面的步骤添加了桥接标头。

`import Marketo/ALMarketo`

### iOS测试设备

按照[添加iOS测试设备](installation.md#ios_test_devices)中的说明操作。

### 在AppDelegate中处理自定义Url类型

按照[自定义URL说明](installation.md#ios_test_devices)操作。

### 在iOS中设置推送通知

按照[推送通知说明](push-notifications.md)操作。 使用类名称“ALMarketo”而不是“Marketo”。

## Android

### 配置权限

打开`AndroidManifest.xml`并添加以下权限。 您的应用程序必须请求“INTERNET”和“ACCESS_NETWORK_STATE”权限。 如果应用程序已经请求这些请求，请跳过此步骤。

```xml
<uses‐permission android:name="android.permission.INTERNET"></uses‐permission>
<uses‐permission android:name="android.permission.ACCESS_NETWORK_STATE"></uses‐permission>
```

### ProGuard配置（可选）

如果您的应用程序使用ProGuard，请将以下行添加到项目文件夹中的`proguard.cfg`文件中。 此配置排除Marketo SDK进行模糊处理。

```text
-dontwarn com.marketo.*
-dontnote com.marketo.*
-keep class com.marketo.**{ *; }
```

### Android测试设备

按照[Android Test Devices](installation.md#android_test_devices)中的说明操作。

## 在Android中设置推送通知

按照[Android Firebase Cloud Messaging说明](installation.md#android_firebase_cloud_messaging_support)操作。 使用类名称“ALMarketo”而不是“Marketo”。

要设置用户配置文件，请按照[用户配置文件说明](user-profiles.md)操作。 要设置自定义操作，请按照[自定义操作说明](custom-actions.md#android_custom_action)操作。 在这两组指令中，使用类名称“ALMarketo”而不是“Marketo”。
