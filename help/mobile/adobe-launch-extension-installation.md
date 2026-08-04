---
title: '[!DNL Adobe Launch]扩展安装'
feature: Mobile Marketing
description: 安装适用于移动设备的Adobe Launch Marketo扩展。 请按照iOS和Android的设置、测试设备、权限以及FCM步骤执行推送和应用程序内操作。
exl-id: d71b7cd7-309b-4882-9bba-7daaaa5ef32d
TQID: https://experienceleague.adobe.com/UZRHaRBISIZsE6E25Ee7CnnYwyZwi6w2YgOQJ-JL00U
product_v2: id: b27e5950-9033-45ac-9f86-eb22e567f615
feature_v2: id: b3b8a63f-51fc-40f6-a7d2-a31c5d49fb45id: ed6be6bb-75bb-4ea9-9a42-3bcaa65e1bcc
role_v2: id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
topic_v2: id: e0eb8757-182f-49f3-94a4-1587d16f5094
source-git-commit: 3e6d310c5aec1a3435424fb122b71d825db5af0e
workflow-type: tm+mt
source-wordcount: 696
ht-degree: 1%

---

# [!DNL Adobe Launch]扩展安装

安装[!DNL Adobe Launch] Marketo扩展以发送推送通知、应用程序内消息或同时发送这两者。

## 先决条件

1. [在Marketo管理员中添加应用程序](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/mobile-marketing/admin/add-a-mobile-app)并获取该应用程序的密钥和Munchkin ID。
1. [在 [!DNL Adobe Launch] 门户](https://experience.adobe.com/#/@amc/data-collection/home)中配置属性。
1. 在[!DNL Adobe Launch]门户中为该属性配置应用程序密钥和Munchkin ID。
1. 可选： [设置推送通知](push-notifications.md)。

## 如何在iOS上安装Marketo扩展

### 设置Swift桥接标头

1. 转到[!UICONTROL File] > [!UICONTROL New] > [!UICONTROL File]并选择&#x200B;**[!UICONTROL Header File]**。

1. 将文件命名为“&lt;_ProjectName_>-Bridging-Header”。

1. 转到[!UICONTROL Project] > [!UICONTROL Target] > [!UICONTROL Build Settings] > [!UICONTROL Swift Compiler] > [!UICONTROL Code Generation]。
1. 将以下路径添加到“目标桥接”标头：

`$(PODS_ROOT)/<_ProjectName_>-Bridging-Header.h`

## 初始化扩展

>[!BEGINTABS]

>[!TAB 目标C]

按如下方式更新`applicationDidBecomeActive`方法。

```objectivec
(void)applicationDidBecomeActive:(UIApplication*) application
{
 [[ALMarketo sharedInstance] initializeMarketo:nil];
}
```

>[!TAB Swift]

按如下方式更新`applicationDidBecomeActive`方法。

```objectivec
func applicationDidBecomeActive(_ application: UIApplication)
{
 ALMarketo.sharedInstance().initializeMarketo(nil)
}
```

>[!ENDTABS]

## iOS测试设备

1. 选择&#x200B;**[!UICONTROL Project]** > **[!UICONTROL Target]** > **[!UICONTROL Info]** > **[!UICONTROL URL Types]**。
1. 添加标识符${PRODUCT_NAME}。
1. 将URL方案设置为mkto-&lt;S_Ecret Key_>。
1. 为Objective-C添加`application:openURL:sourceApplication:annotation:`到`AppDelegate.m file`。

### 在AppDelegate中处理自定义Url类型

>[!BEGINTABS]

>[!TAB 目标C]

```objectivec
#ifdef __IPHONE_10_0
-(BOOL)application:(UIApplication *)application
           openURL:(NSURL *)url
           options:(NSDictionary *)options{
    return [[ALMarketo sharedInstance] application:application
                                         openURL:url
                               sourceApplication:nil
                                      annotation:nil];
}
#endif

- (BOOL)application:(UIApplication *)application
            openURL:(NSURL *)url
  sourceApplication:(NSString *)sourceApplication
         annotation:(id)annotation {
    return [[ALMarketo sharedInstance] application:application
                                         openURL:url
                               sourceApplication:nil
                                      annotation:nil];
}
```

>[!TAB Swift]

```objectivec
func application(_ application: UIApplication, open url: URL, sourceApplication: String?, annotation: Any) -> Bool {
    return ALMarketo.sharedInstance().application(application, open: url, sourceApplication: nil, annotation: nil)
}
```

>[!ENDTABS]

## 如何在Android上安装Marketo SDK

### Android扩展设置

按照[!DNL Adobe Launch]门户中的说明操作。

### 配置权限

打开`AndroidManifest.xml`并添加以下权限。 您的应用程序必须请求“INTERNET”和“ACCESS_NETWORK_STATE”权限。 如果应用程序已经请求这些请求，请跳过此步骤。

```xml
<uses‐permission android:name="android.permission.INTERNET"></uses‐permission>
<uses‐permission android:name="android.permission.ACCESS_NETWORK_STATE"></uses‐permission>
```

## 初始化扩展

ProGuard配置（可选）

如果您的应用程序使用ProGuard，请将以下行添加到`project`文件夹中的`proguard.cfg`文件中。 此配置排除Marketo SDK进行模糊处理。

```text
-dontwarn com.marketo.*
-dontnote com.marketo.*
-keep class com.marketo.**{ *; }
```

## Android测试设备

将“MarketoActivity”添加到应用程序标记中的`AndroidManifest.xml`。

```xml
<activity android:name="com.marketo.MarketoActivity"  android:configChanges="orientation|screenSize" >
    <intent-filter android:label="MarketoActivity" >
        <action  android:name="android.intent.action.VIEW"/>
        <category  android:name="android.intent.category.DEFAULT"/>
        <category  android:name="android.intent.category.BROWSABLE"/>
        <data android:host="add_test_device" android:scheme="mkto" />
    </intent-filter>
</activity>
```

## Firebase Cloud Messaging支持

MME SDK for Android支持直接使用Google的[Firebase Cloud Messaging](https://firebase.google.com/docs/cloud-messaging/) (FCM)。

### 将FCM添加到应用程序

1. 将最新的Marketo Android SDK集成到Android应用程序。 查看[GitHub](https://github.com/Marketo/android-sdk)上的步骤。
1. 在Firebase控制台中配置Firebase应用程序。
   1. 在[](https://accounts.google.com/ServiceLogin?passive=1209600&osid=1&continue=https://console.firebase.google.com/&followup=https://console.firebase.google.com/)Firebase控制台中创建或添加项目。
      1. 在[Firebase控制台](https://accounts.google.com/ServiceLogin?passive=1209600&osid=1&continue=https://console.firebase.google.com/&followup=https://console.firebase.google.com/)中选择&#x200B;**[!UICONTROL Add Project]**。
      1. 从现有Google Cloud项目列表中选择您的GCM项目，然后选择&#x200B;**[!UICONTROL Add Firebase]**。
      1. 在Firebase欢迎屏幕中，选择&#x200B;**[!UICONTROL Add Firebase to your Android App]**。
      1. 提供您的包名称和SHA-1，然后选择&#x200B;**[!UICONTROL Add App]**。 已下载Firebase应用程序的新`google-services.json`文件。
      1. 选择&#x200B;**[!UICONTROL Continue]**，然后按照在Android Studio中添加Google Services插件的详细说明操作。

   1. 在[!UICONTROL Project Overview]中转到&#x200B;**[!UICONTROL Project Settings]**。
      1. 选择&#x200B;**[!UICONTROL General]**&#x200B;选项卡并下载`google-services.json`。
      1. 选择 **[!UICONTROL Cloud Messaging]** 选项卡。 复制[!UICONTROL Server Key]和[!UICONTROL Sender ID]，并将其提供给Marketo。
   1. 在Android应用程序中配置FCM。
      1. 切换到Android Studio中的“项目”视图，以显示项目根目录。
         1. 将下载的`google-services.json`文件移到Android应用程序模块的根目录中。
         1. 在项目级别`build.gradle`中添加以下内容：

            ```
            buildscript {
              dependencies {
                classpath 'com.google.gms:google-services:4.0.0'
              }
            }
            ```

         1. 在应用程序级别的build.gradle中，添加以下内容：

            ```
            dependencies {
              compile 'com.google.firebase:firebase-core:17.4.0'
            }
            // Add to the bottom of the file
            apply plugin: 'com.google.gms.google-services'
            ```

         1. 在IDE中显示的栏中选择&#x200B;**[!UICONTROL Sync now]**。
   1. 编辑应用程序清单。 FCM SDK会自动添加所需的权限和接收者功能。 删除以下过时元素，这些元素可能导致消息重复：

      ```xml
      <uses-permission android:name="android.permission.WAKE_LOCK" />
      <permission android:name="<your-package-name>.permission.C2D_MESSAGE" android:protectionLevel="signature" />
      <uses-permission android:name="<your-package-name>.permission.C2D_MESSAGE" />
      
      ...
      
      <receiver>
        android:name="com.google.android.gms.gcm.GcmReceiver"
        android:exported="true"
        android:permission="com.google.android.c2dm.permission.SEND">
        <intent-filter>
          <action android:name="com.google.android.c2dm.intent.RECEIVE" />
          <category android:name="<your-package-name> />
        </intent-filter>
      </receiver>
      ```

### fcm常见问题解答

这些问题涵盖Firebase Cloud Messaging支持。

**问：在哪里可以找到更新到MME SDK最新版本的说明？** 请参阅Marketo开发人员网站上的[安装说明](installation.md)。

**问：更新到SDK的最新版本是否需要我将Android应用程序的更新版本发布到现有用户？** 不会。

**问：如果现有MME客户已发布使用Marketo Android SDK的Android应用程序，它将会受到什么影响？** 按如下方式将现有Android GCM客户端应用程序迁移到Firebase Cloud Messaging (FCM)：

1. 在[Firebase控制台](https://accounts.google.com/ServiceLogin?passive=1209600&osid=1&continue=https://console.firebase.google.com/&followup=https://console.firebase.google.com/)中选择&#x200B;**[!UICONTROL Add Project]**。
1. 从现有Google Cloud项目列表中选择您的GCM项目，然后选择&#x200B;**[!UICONTROL Add Firebase]**。
1. 在Firebase欢迎屏幕中，选择&#x200B;**[!UICONTROL Add Firebase to your Android App]**。
1. 提供您的包名称和SHA-1，然后选择&#x200B;**[!UICONTROL Add App]**。 已下载适用于您的Firebase应用程序的新google-services.json文件。
1. 选择&#x200B;**[!UICONTROL Continue]**，然后按照在Android Studio中添加Google Services插件的详细说明操作。

**问：我们是否可以定位使用使用GCM应用程序的旧Marketo SDK创建的潜在客户？** 可以。 您可以定位使用Marketo SDK创建的所有潜在客户以进行推送通知。
