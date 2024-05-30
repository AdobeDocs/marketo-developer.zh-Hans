---
title: "适用于的Marketo Mobile扩展 [!DNL Adobe Launch]"
feature: Mobile Marketing
description: '"适用于的Marketo Mobile扩展 [!DNL Adobe Launch] 概述”'
source-git-commit: 2185972a272b64908d6aac8818641af07c807ac2
workflow-type: tm+mt
source-wordcount: '269'
ht-degree: 0%

---


# Marketo Mobile Extension for [!DNL Adobe Launch]

中的Marketo Mobile SDK扩展安装说明 [!DNL Adobe Launch]. 发送推送通知和/或应用程序内消息需要执行以下步骤。

## 先决条件

- [在Marketo管理中添加应用程序](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/mobile-marketing/admin/add-a-mobile-app) （获取您的应用程序密钥和Munchkin ID）
- 请按照 [!DNL Adobe Launch] 安装门户
- [设置推送通知](push-notifications.md) （可选）

## iOS

### 设置Swift桥接标头

1. 转到“文件”>“新建”>“文件”，然后选择“头文件”。
1. 将文件命名为“&lt;”_项目名称_>-Bridging-Header”。
1. 转到“项目”>“Target”>“构建阶段”>“Swift编译器”>“代码生成”。 将以下路径添加到目标桥接标头：

   `$(PODS_ROOT)/<_ProjectName_>-Bridging-Header.h`

对于Swift用户：删除以下import语句，因为在上述步骤中添加了桥接标头。

`import Marketo/ALMarketo`

### iOS测试设备

按照上的说明操作 [添加iOS测试设备](installation.md#ios_test_devices)

### 在AppDelegate中处理自定义Url类型

按照说明操作 [此处](installation.md#ios_test_devices)

### 在iOS中设置推送通知

按照说明操作 [此处](push-notifications.md) 并使用类名称“ALMarketo”而不是“Marketo”

## Android

### 配置权限

打开 `AndroidManifest.xml` 并添加以下权限。 您的应用程序必须请求“INTERNET”和“ACCESS_NETWORK_STATE”权限。 如果您的应用程序已经请求这些权限，请跳过此步骤。

```xml
<uses‐permission android:name="android.permission.INTERNET"></uses‐permission>
<uses‐permission android:name="android.permission.ACCESS_NETWORK_STATE"></uses‐permission>
```

### ProGuard配置（可选）

如果您的应用程序使用的是ProGuard，请在应用程序中添加以下行 `proguard.cfg` 文件。 该文件位于您的项目文件夹中。 添加此代码会将Marketo SDK排除在模糊处理过程之外。

```
-dontwarn com.marketo.*
-dontnote com.marketo.*
-keep class com.marketo.**{ *; }
```

### Android测试设备

按照说明操作 [此处](installation.md#android_test_devices)

## 在Android中设置推送通知

按照说明操作 [此处](installation.md#android_firebase_cloud_messaging_support) 并使用类名称“ALMarketo”而不是“Marketo”

要设置用户配置文件，请按照说明 [此处](user-profiles.md) 对于自定义操作，请按照相关说明操作 [此处](custom-actions.md#android_custom_action). 在下面的说明中，使用类名称“ALMarketo”而不是“Marketo”
