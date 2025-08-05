---
title: '[!DNL Adobe Launch]扩展安装'
feature: Mobile Marketing
description: '[!DNL Adobe Launch]扩展安装概述'
exl-id: d71b7cd7-309b-4882-9bba-7daaaa5ef32d
source-git-commit: 3649db037a95cfd20ff0a2c3d81a3b40d0095c39
workflow-type: tm+mt
source-wordcount: '664'
ht-degree: 0%

---

# [!DNL Adobe Launch]扩展安装

[!DNL Adobe Launch] Marketo扩展的安装说明。 发送推送通知和/或应用程序内消息需要执行以下步骤。

## 先决条件

1. [在Marketo Admin中添加应用程序](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/mobile-marketing/admin/add-a-mobile-app)&#x200B;(获取应用程序密钥和Munchkin ID)
1. [在 [!DNL Adobe Launch] 门户](https://experience.adobe.com/#/@amc/data-collection/home)中配置属性
1. 在[!DNL Adobe Launch]门户中配置属性的应用程序密钥和Munchkin ID
1. [设置推送通知](push-notifications.md)（可选）

## 如何在iOS上安装Marketo扩展

### 设置Swift桥接标头

1. 转到[!UICONTROL File] > [!UICONTROL New] > [!UICONTROL File]并选择&#x200B;**[!UICONTROL Header File]**。

1. 将文件命名为“&lt;_ProjectName_>-Bridging-Header”。

1. 转到[!UICONTROL Project] > [!UICONTROL Target] > [!UICONTROL Build Settings] > [!UICONTROL Swift Compiler] > [!UICONTROL Code Generation]。 将以下路径添加到“目标桥接”标头：

`$(PODS_ROOT)/<_ProjectName_>-Bridging-Header.h`

## 初始化扩展

>[!BEGINTABS]

>[!TAB 目标C]

更新`applicationDidBecomeActive`方法，如下所示

```
(void)applicationDidBecomeActive:(UIApplication*) application
{
 [[ALMarketo sharedInstance] initializeMarketo:nil];
}
```

>[!TAB Swift]

更新`applicationDidBecomeActive`方法，如下所示

```
func applicationDidBecomeActive(_ application: UIApplication)
{
 ALMarketo.sharedInstance().initializeMarketo(nil)
}
```

>[!ENDTABS]

## iOS测试设备

1. 选择&#x200B;**[!UICONTROL Project]** > **[!UICONTROL Target]** > **[!UICONTROL Info]** > **[!UICONTROL URL Types]**。
1. 添加标识符： ${PRODUCT_NAME}
1. 设置URL方案： mkto-&lt;S_Ecret Key_>
1. 包括`application:openURL:sourceApplication:annotation:`到`AppDelegate.m file` (Objective-C)

### 在AppDelegate中处理自定义Url类型

>[!BEGINTABS]

>[!TAB 目标C]

```
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

```
func application(_ application: UIApplication, open url: URL, sourceApplication: String?, annotation: Any) -> Bool {
    return ALMarketo.sharedInstance().application(application, open: url, sourceApplication: nil, annotation: nil)
}
```

>[!ENDTABS]

## 如何在Android上安装Marketo SDK

### Android扩展设置

按照[!DNL Adobe Launch]门户中的说明操作

### 配置权限

打开`AndroidManifest.xml`并添加以下权限。 您的应用程序必须请求“INTERNET”和“ACCESS_NETWORK_STATE”权限。 如果您的应用程序已经请求这些权限，请跳过此步骤。

```xml
<uses‐permission android:name="android.permission.INTERNET"></uses‐permission>
<uses‐permission android:name="android.permission.ACCESS_NETWORK_STATE"></uses‐permission>
```

## 初始化扩展

ProGuard配置（可选）

如果您正在使用应用程序的ProGuard，请在`proguard.cfg`文件中添加以下行。 该文件位于您的`project`文件夹中。 添加此代码会将Marketo SDK排除在模糊处理过程之外。

```
-dontwarn com.marketo.*
-dontnote com.marketo.*
-keep class com.marketo.**{ *; }
```

## Android  测试  设备

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

适用于Android的MME软件开发工具包(SDK)已更新到一个更现代、稳定和可扩展的框架，其中包含更灵活的功能和面向Android应用程序开发人员的新工程功能。

Android应用程序开发人员现在可以直接将Google的[Firebase Cloud Messaging](https://firebase.google.com/docs/cloud-messaging/) (FCM)与此SDK结合使用。

### 将FCM添加到应用程序

1. 在Android应用程序中集成最新的Marketo Android SDK。  步骤位于[GitHub](https://github.com/Marketo/android-sdk)。
1. 在Firebase控制台上配置Firebase应用程序。
   1. 在[](https://accounts.google.com/ServiceLogin?passive=1209600&osid=1&continue=https://console.firebase.google.com/&followup=https://console.firebase.google.com/)Firebase控制台上创建/添加项目。
      1. 在[Firebase控制台](https://accounts.google.com/ServiceLogin?passive=1209600&osid=1&continue=https://console.firebase.google.com/&followup=https://console.firebase.google.com/)中选择&#x200B;**[!UICONTROL Add Project]**。
      1. 从现有Google Cloud项目列表中选择您的GCM项目，然后选择&#x200B;**[!UICONTROL Add Firebase]**。
      1. 在Firebase欢迎屏幕中，选择&#x200B;**[!UICONTROL Add Firebase to your Android App]**。
      1. 提供您的包名称和SHA-1，然后选择&#x200B;**[!UICONTROL Add App]**。 已下载Firebase应用程序的新`google-services.json`文件。
      1. 选择&#x200B;**[!UICONTROL Continue]**，然后按照在Android Studio中添加Google Services插件的详细说明操作。

   1. 在&#x200B;**[!UICONTROL Project Settings]**&#x200B;中导航到[!UICONTROL Project Overview]
      1. 单击&#x200B;**[!UICONTROL General]**&#x200B;选项卡。 下载 `google-services.json` 文件。
      1. 单击&#x200B;**[!UICONTROL Cloud Messaging]**&#x200B;选项卡。 复制[!UICONTROL Server Key]和[!UICONTROL Sender ID]。 将这些[!UICONTROL Server Key]和[!UICONTROL Sender ID]提供给Marketo。
   1. 在Android应用程序中配置FCM更改
      1. 切换到Android Studio中的“项目”视图，查看项目根目录
         1. 将下载的`google-services.json`文件移到Android应用程序模块的根目录中
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

         1. 最后，单击ID中显示的栏中的&#x200B;**[!UICONTROL Sync now]**
   1. 编辑应用程序的清单FCM SDK会自动添加所有必需权限和必需的接收器功能。 确保从应用程序的清单中删除以下过时（且可能有害的，因为它们可能会导致消息重复）元素：

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

有关Firebase Cloud Messaging支持的常见问题。

**问：在哪里可以找到更新到MME SDK最新版本的说明？在Marketo开发人员网站**&#x200B;此处[找到](installation.md)说明。

**问：更新到SDK的最新版本是否需要我将Android应用程序的更新版本发布到现有用户？**&#x200B;编号

**问：它对于已发布与Marketo Android SDK集成的Android应用程序的现有MME客户有何影响？**&#x200B;他们可以将Android上的现有GCM客户端应用程序迁移到Firebase Cloud Messaging (FCM)，如下所示：

1. 在[Firebase控制台](https://accounts.google.com/ServiceLogin?passive=1209600&osid=1&continue=https://console.firebase.google.com/&followup=https://console.firebase.google.com/)中选择&#x200B;**[!UICONTROL Add Project]**。
1. 从现有Google Cloud项目列表中选择您的GCM项目，然后选择&#x200B;**[!UICONTROL Add Firebase]**。
1. 在Firebase欢迎屏幕中，选择&#x200B;**[!UICONTROL Add Firebase to your Android App]**。
1. 提供您的包名称和SHA-1，然后选择&#x200B;**[!UICONTROL Add App]**。 适用于您的的新google-services.json文件
1. 已下载Firebase应用程序。
1. 选择&#x200B;**[!UICONTROL Continue]**，然后按照在Android Studio中添加Google Services插件的详细说明操作。

**问：我们能否定位使用旧版Marketo SDK（使用GCM应用程序）创建的潜在客户？**&#x200B;是。 使用Marketo SDK创建的所有潜在客户都可以定位来发送推送通知。
