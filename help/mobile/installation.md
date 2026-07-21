---
title: 安装
feature: Mobile Marketing
description: 使用CocoaPods、Swift Package Manager或Gradle在iOS和Android上安装和初始化Marketo Mobile SDK，支持推送和应用程序内消息。
exl-id: e0b79d85-3509-46d2-a77d-cee211c5ec7f
TQID: https://experienceleague.adobe.com/zYNoGPwJTQnqmP6CH0NDbmb-b8vAKRScMmms6vy0Sb4
product_v2:
  - id: b27e5950-9033-45ac-9f86-eb22e567f615
feature_v2:
  - id: b3b8a63f-51fc-40f6-a7d2-a31c5d49fb45
  - id: e2290edd-b061-4880-9d79-dee306cf5aa9
  - id: f71e690b-4480-4b67-9ef5-88f42f9cdfdb
role_v2:
  - id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
topic_v2:
  - id: b5ce8718-c3af-4fdb-a1a9-fca32f83a87c
source-git-commit: 3e6d310c5aec1a3435424fb122b71d825db5af0e
workflow-type: tm+mt
source-wordcount: 765
ht-degree: 0%

---

# 安装

安装和初始化Marketo Mobile SDK ，以发送推送通知和/或应用程序内消息。

## 在iOS上安装Marketo SDK

### 先决条件

1. [在Marketo管理员中添加应用程序](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/mobile-marketing/admin/add-a-mobile-app)并获取该应用程序的密钥和Munchkin ID。
1. 可选： [设置推送通知](push-notifications.md)。

### 通过CocoaPods安装框架

1. 安装CocoaPods。`$ sudo gem install cocoapods`
1. 将目录更改为项目目录并创建具有智能默认值的Podfile。`$ pod init`
1. 打开您的Podfile。`$ open -a Xcode Podfile`
1. 将以下行添加到您的Podfile中。`$ pod 'Marketo-iOS-SDK'`
1. 保存并关闭Podfile。
1. 下载并安装Marketo iOS SDK。`$ pod install`
1. 在Xcode中打开工作区。`$ open App.xcworkspace`

### 使用Swift Package Manager安装框架

1. 在项目导航器中选择您的项目。 在“添加包依赖关系”下，选择“+”。

   ![添加依赖项](assets/dependency-manager-add.png)

1. 从<https://github.com/Marketo/ios-sdk>添加Marketo包。

   ![存储库URL](assets/dependency-manager-url.png)

1. 添加资源捆绑。 在项目导航器中找到`MarketoFramework.XCframework`并在查找器中将其打开。 拖动`MKTResources.bundle`以复制捆绑包资源。

### 设置Swift桥接标头

1. 转到“文件”>“新建”>“文件”，然后选择“头文件”。

   ![选择“头文件”](assets/choose-header-file.png)

1. 将文件命名为“&lt;_ProjectName_>-Bridging-Header”。

1. 转到“项目”>“Target”>“构建阶段”>“Swift编译器”>“代码生成”。 将以下路径添加到目标桥接标头：

   `$(PODS_ROOT)/<_ProjectName_>-Bridging-Header.h`

   ![生成阶段](assets/build-phases.png)

## 初始化SDK

使用您的Marketo帐户ID和应用程序密钥初始化Munchkin iOS SDK。 在Marketo管理员的“移动应用程序和设备”下找到这两个值。

1. 打开Objective-C的AppDelegate.m文件或Swift的桥接文件。 导入Marketo.h头文件。

   ```
   #import <MarketoFramework/MarketoFramework.h>
   ```

1. 将以下代码粘贴到`application:didFinishLaunchingWithOptions`：函数中。

   将“本机”作为本机应用程序的框架类型传递。

>[!BEGINTABS]

>[!TAB 目标C]

```objectivec
Marketo *sharedInstance = [Marketo sharedInstance];

[sharedInstance initializeWithMunchkinID:@"munchkinAccountId" appSecret:@"secretKey" mobileFrameworkType:@"native" launchOptions:launchOptions];
```

>[!TAB Swift]

```swift
let sharedInstance: Marketo = Marketo.sharedInstance()

sharedInstance.initialize(withMunchkinID: "munchkinAccountId", appSecret: "secretKey", mobileFrameworkType: "native", launchOptions: launchOptions)
```

>[!ENDTABS]

1. 将`munkinAccountId`和`secretKey`替换为Marketo **[!UICONTROL Admin]** > **[!UICONTROL Mobile Apps and Devices]**&#x200B;中的“Munchkin帐户ID”和“密钥”。

## iOS测试设备

1. 选择项目>目标>信息> URL类型。
1. 添加标识符${PRODUCT_NAME}。
1. 将URL方案设置为`mkto-<Secret Key_>`。
1. 将应用程序:openURL:sourceApplication:annotation:添加到Objective-C的AppDelegate.m文件中。

## 在AppDelegate中处理自定义Url类型

>[!BEGINTABS]

>[!TAB 目标C]

```objectivec
- (BOOL)application:(UIApplication *)app
            openURL:(NSURL *)url
            options:(NSDictionary<UIApplicationOpenURLOptionsKey,id> *)options{

    return [[Marketo sharedInstance] application:app
                                         openURL:url
                                         options:options];
}
```

>[!TAB Swift]

```swift
private func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool
    {
        return Marketo.sharedInstance().application(app, open: url, options: options)
    }
```

>[!ENDTABS]

## 如何在Android上安装Marketo SDK

### 先决条件

1. [在Marketo管理员中添加应用程序](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/mobile-marketing/admin/add-a-mobile-app)并获取该应用程序的密钥和Munchkin ID。
1. 可选： [设置推送通知](push-notifications.md#android_setup_push)。
1. [下载适用于Android的Marketo SDK](https://codeload.github.com/Marketo/android-sdk/zip/refs/heads/master)

### 使用Gradle设置Android SDK

1. 在应用程序级别的build.gradle文件中，在依赖项部分下添加依赖项。

   `implementation 'com.marketo:MarketoSDK:0.8.9'`

1. 将以下配置添加到根`build.gradle`文件。

   ```
   buildscript {
       repositories {
           google()
           mavenCentral()
       }
   ```

1. 将项目与Gradle文件同步。

### 配置权限

打开`AndroidManifest.xml`并添加以下权限。 您的应用程序必须请求“INTERNET”和“ACCESS_NETWORK_STATE”权限。 如果应用程序已经请求这些请求，请跳过此步骤。

```xml
<uses‐permission android:name="android.permission.INTERNET"></uses‐permission>
<uses‐permission android:name="android.permission.ACCESS_NETWORK_STATE"></uses‐permission>
```

### 初始化SDK

1. 打开Application或Activity类。 在setContentView之前或在Application Context中将Marketo SDK导入活动。

   ```java
   // Initialize Marketo
   Marketo marketoSdk = Marketo.getInstance(getApplicationContext());
   marketoSdk.initializeSDK("native","munchkinAccountId","secretKey");
   ```

1. ProGuard配置（可选）

   如果您的应用程序使用ProGuard，请将以下行添加到项目文件夹中的`proguard.cfg`文件中。 此配置排除Marketo SDK进行模糊处理。

   ```
   -dontwarn com.marketo.*
   -dontnote com.marketo.*
   -keep class com.marketo.`{ *; }
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
   1. 在[&#128279;](https://accounts.google.com/ServiceLogin?passive=1209600&osid=1&continue=https://console.firebase.google.com/&followup=https://console.firebase.google.com/)Firebase控制台上创建/添加项目。
      1. 在[Firebase控制台](https://accounts.google.com/ServiceLogin?passive=1209600&osid=1&continue=https://console.firebase.google.com/&followup=https://console.firebase.google.com/)中选择`Add Project`。
      1. 从现有Google Cloud项目列表中选择您的GCM项目，然后选择`Add Firebase`。
      1. 在Firebase欢迎屏幕中，选择`Add Firebase to your Android App`。
      1. 提供您的包名称和SHA-1，然后选择`Add App`。 已下载Firebase应用程序的新`google-services.json`文件。
      1. 选择`Continue`，然后按照在Android Studio中添加Google Services插件的详细说明操作。

   1. 在项目概述中导航到“项目设置”
      1. 单击“常规”选项卡。 下载“google-services.json”文件。
      1. 单击“Cloud Messaging”选项卡。 复制“服务器密钥”和“发件人ID”。 向Marketo提供这些“服务器密钥”和“发件人ID”。
   1. 在Android应用程序中配置FCM。
      1. 切换到Android Studio中的“项目”视图，查看项目根目录
         1. 将下载的`google-services.json`文件移到Android应用程序模块的根目录中
         1. 在项目级别的build.gradle中，添加以下内容：

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

         1. 最后，在ID中显示的栏中选择&#x200B;**[!UICONTROL Sync now]**
   1. 编辑应用程序清单。 FCM SDK会自动添加所需的权限和接收者功能。 删除以下过时元素，这些元素可能导致消息重复：

      ```xml
      <uses-permission android:name="android.permission.WAKE_LOCK" />
      <permission android:name="<your-package-name>.permission.C2D_MESSAGE" android:protectionLevel="signature" />
      <uses-permission android:name="<your-package-name>.permission.C2D_MESSAGE" />
      
      ...
      
      <receiver>
        android:name="com.google.android.gms.gcm.GcmReceiver"
        android:exported="true"
        android:permission="com.google.android.c2dm.permission.SEND"
        <intent-filter>
          <action android:name="com.google.android.c2dm.intent.RECEIVE" />
          <category android:name="<your-package-name> />
        </intent-filter>
      </receiver>
      ```
