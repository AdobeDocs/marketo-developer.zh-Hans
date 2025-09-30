---
title: 安装
feature: Mobile Marketing
description: 使用CocoaPods、Swift Package Manager或Gradle在iOS和Android上安装和初始化Marketo Mobile SDK，支持推送和应用程序内消息。
exl-id: e0b79d85-3509-46d2-a77d-cee211c5ec7f
source-git-commit: 7557b9957c87f63c2646be13842ea450035792be
workflow-type: tm+mt
source-wordcount: '774'
ht-degree: 0%

---

# 安装

Marketo Mobile SDK的安装说明。 发送推送通知和/或应用程序内消息需要执行以下步骤。

## 在iOS上安装Marketo SDK

### 先决条件

1. [在Marketo Admin中添加应用程序](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/mobile-marketing/admin/add-a-mobile-app)&#x200B;(获取应用程序密钥和Munchkin ID)
1. [设置推送通知](push-notifications.md)（可选）

### 通过CocoaPods安装框架

1. 安装CocoaPods。`$ sudo gem install cocoapods`
1. 将目录更改为项目目录并创建具有智能默认值的Podfile。`$ pod init`
1. 打开您的Podfile。`$ open -a Xcode Podfile`
1. 将以下行添加到您的Podfile中。`$ pod 'Marketo-iOS-SDK'`
1. 保存并关闭Podfile。
1. 下载并安装Marketo iOS SDK。`$ pod install`
1. 在Xcode中打开工作区。`$ open App.xcworkspace`

### 使用Swift Package Manager安装框架

1. 从项目导航器中选择您的项目，然后在“添加包依赖关系”下，单击“+”，如下所示：

   ![添加依赖项](assets/dependency-manager-add.png)

1. 从此存储库添加Marketo包。 为此存储库添加此URL： <https://github.com/Marketo/ios-sdk>。

   ![存储库URL](assets/dependency-manager-url.png)

1. 现在添加资源包，如下所示：在项目导航器中找到`MarketoFramework.XCframework`并在Finder中将其打开。 拖放`MKTResources.bundle`以复制捆绑包资源。

### 设置Swift桥接标头

1. 转到“文件”>“新建”>“文件”，然后选择“头文件”。

   ![选择“头文件”](assets/choose-header-file.png)

1. 将文件命名为“&lt;_ProjectName_>-Bridging-Header”。

1. 转到“项目”>“Target”>“构建阶段”>“Swift编译器”>“代码生成”。 将以下路径添加到目标桥接标头：

   `$(PODS_ROOT)/<_ProjectName_>-Bridging-Header.h`

   ![生成阶段](assets/build-phases.png)

## 初始化SDK

在使用Marketo iOS SDK之前，必须使用Munchkin帐户ID和应用程序密钥对其进行初始化。 您可以在Marketo管理区域的“移动设备应用程序和设备”下方找到每个应用程序或设备。

1. 打开AppDelegate.m文件(Objective-C)或桥接文件(Swift)，并导入Marketo.h头文件。

   ```
   #import <MarketoFramework/MarketoFramework.h>
   ```

1. 将以下代码粘贴到`application:didFinishLaunchingWithOptions`：函数中。

   请注意，我们必须将“本机”作为本机应用程序的框架类型传递。

>[!BEGINTABS]

>[!TAB 目标C]

```
Marketo *sharedInstance = [Marketo sharedInstance];

[sharedInstance initializeWithMunchkinID:@"munchkinAccountId" appSecret:@"secretKey" mobileFrameworkType:@"native" launchOptions:launchOptions];
```

>[!TAB Swift]

```
let sharedInstance: Marketo = Marketo.sharedInstance()

sharedInstance.initialize(withMunchkinID: "munchkinAccountId", appSecret: "secretKey", mobileFrameworkType: "native", launchOptions: launchOptions)
```

>[!ENDTABS]

1. 使用可在Marketo `munkinAccountId` > `secretKey`部分中找到的“Munchkin帐户ID”和“密钥”替换上述&#x200B;**[!UICONTROL Admin]**&#x200B;和&#x200B;**[!UICONTROL Mobile Apps and Devices]**。

## iOS测试设备

1. 选择项目>目标>信息> URL类型。
1. 添加标识符： ${PRODUCT_NAME}
1. 设置URL方案： `mkto-<Secret Key_>`
1. 将应用程序:openURL:sourceApplication:annotation:包含到AppDelegate.m文件(Objective-C)

## 在AppDelegate中处理自定义Url类型

>[!BEGINTABS]

>[!TAB 目标C]

```
- (BOOL)application:(UIApplication *)app
            openURL:(NSURL *)url
            options:(NSDictionary<UIApplicationOpenURLOptionsKey,id> *)options{

    return [[Marketo sharedInstance] application:app
                                         openURL:url
                                         options:options];
}
```

>[!TAB Swift]

```
private func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool
    {
        return Marketo.sharedInstance().application(app, open: url, options: options)
    }
```

>[!ENDTABS]

## 如何在Android上安装Marketo SDK

### 先决条件

1. [在Marketo Admin中添加应用程序](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/mobile-marketing/admin/add-a-mobile-app)&#x200B;(获取应用程序密钥和Munchkin ID)
1. [设置推送通知](push-notifications.md#android_setup_push)（可选）
1. [下载适用于Android的Marketo SDK](https://codeload.github.com/Marketo/android-sdk/zip/refs/heads/master)

### 使用Gradle设置Android SDK

1. 在应用程序级别build.gradle文件中，在依赖关系部分下添加

`implementation 'com.marketo:MarketoSDK:0.8.9'`

1. 根`build.gradle`文件应具有

   ```
   buildscript {
       repositories {
           google()
           mavenCentral()
       }
   ```

1. 将项目与Gradle文件同步

### 配置权限

打开`AndroidManifest.xml`并添加以下权限。 您的应用程序必须请求“INTERNET”和“ACCESS_NETWORK_STATE”权限。 如果您的应用程序已经请求这些权限，请跳过此步骤。

```xml
<uses‐permission android:name="android.permission.INTERNET"></uses‐permission>
<uses‐permission android:name="android.permission.ACCESS_NETWORK_STATE"></uses‐permission>
```

### 初始化SDK

1. 在应用程序中打开Application或Activity类，然后将Marketo SDK导入setContentView或Application Context中的活动。

   ```java
   // Initialize Marketo
   Marketo marketoSdk = Marketo.getInstance(getApplicationContext());
   marketoSdk.initializeSDK("native","munchkinAccountId","secretKey");
   ```

1. ProGuard配置（可选）

   如果您正在使用应用程序的ProGuard，请在`proguard.cfg`文件中添加以下行。 该文件位于您的项目文件夹中。 添加此代码会将Marketo SDK排除在模糊处理过程之外。

   ```
   -dontwarn com.marketo.*
   -dontnote com.marketo.*
   -keep class com.marketo.`{ *; }
   ```

## Android测试设备

将“MarketoActivity”添加到应用程序标记内的`AndroidManifest.xml`文件中。

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
      1. 在[Firebase控制台](https://accounts.google.com/ServiceLogin?passive=1209600&osid=1&continue=https://console.firebase.google.com/&followup=https://console.firebase.google.com/)中选择`Add Project`。
      1. 从现有Google Cloud项目列表中选择您的GCM项目，然后选择`Add Firebase`。
      1. 在Firebase欢迎屏幕中，选择`Add Firebase to your Android App`。
      1. 提供您的包名称和SHA-1，然后选择`Add App`。 已下载Firebase应用程序的新`google-services.json`文件。
      1. 选择`Continue`，然后按照在Android Studio中添加Google Services插件的详细说明操作。

   1. 在项目概述中导航到“项目设置”
      1. 单击“常规”选项卡。 下载“google-services.json”文件。
      1. 单击“Cloud Messaging”选项卡。 复制“服务器密钥”和“发件人ID”。 向Marketo提供这些“服务器密钥”和“发件人ID”。
   1. 在Android应用程序中配置FCM更改
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

         1. 最后，单击ID中显示的栏中的“立即同步”
   1. 编辑应用程序的清单FCM SDK会自动添加所有必需权限和必需的接收器功能。 确保从应用程序的清单中删除以下过时（且可能有害的，因为它们可能会导致消息重复）元素：

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
