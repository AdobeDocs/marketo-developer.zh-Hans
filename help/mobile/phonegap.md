---
title: PhoneGap
feature: Mobile Marketing
description: 使用Cordova设置Marketo PhoneGap插件，配置Firebase Cloud Messaging，启用iOS和Android推送，跟踪通知，以及初始化SDK。
exl-id: 99f14c76-9438-4942-9309-643bca434d07
TQID: https://experienceleague.adobe.com/eFAwR7r5IE6vKigsEWrJdCmC3VrfB-nl0h8x7Vgt1VY
product_v2: id: b27e5950-9033-45ac-9f86-eb22e567f615
feature_v2: id: b0bb9048-d951-48d8-8232-45cf248a7e27id: ea90ebee-5c84-42d9-8b21-006bdabc95a3
role_v2: id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
topic_v2: id: aa2f3246-cb95-4b30-8899-fdf7d73550cc
source-git-commit: 3e6d310c5aec1a3435424fb122b71d825db5af0e
workflow-type: tm+mt
source-wordcount: 758
ht-degree: 2%

---

# PhoneGap

将Marketo PhoneGap插件与Cordova应用程序集成。

## 先决条件

1. [在Marketo管理员中添加应用程序](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/mobile-marketing/admin/add-a-mobile-app)并获取该应用程序的密钥和Munchkin ID。
1. 为[iOS](push-notifications.md)或[Android](push-notifications.md)设置推送通知。
1. [安装PhoneGap/Cordova CLI](https://cordova.apache.org/docs/en/latest/guide/cli/)。

## 安装说明

1. 设置Marketo PhoneGap插件。

   转到PhoneGap应用程序目录并运行以下命令以添加Marketo插件：

   `$ cordova plugin add https://github.com/Marketo/PhoneGapPlugin.git --variable APPLICATION_SECRET_KEY="YOUR_APPLICATION_SECRET"`

1. 安装FCM插件。

   `$ cordova plugin add cordova-plugin-fcm`

   运行以下命令以确认添加了插件：

   `$ cordova plugin ls com.marketo.plugin 0.X.0 "MarketoPlugin" cordova-plugin-fcm 2.1.2 "FCMPlugin"`

**迁移到较新版本（可选）**

要删除现有插件，请运行以下命令：

`$ cordova plugin remove com.marketo.plugin`

要再次添加插件，请运行以下命令：

`$ cordova plugin add https://github.com/Marketo/PhoneGapPlugin.git --variable APPLICATION_SECRET_KEY="YOUR_APPLICATION_SECRET"`

**Cordova版本8.0.0 (Cordova@Android7.0.0)及更高版本**

构建Cordova Android平台后，在Android Studio中打开应用程序。 更新`com.marketo.plugin`文件夹中`Marketo.gradle`文件中的`dirs`值。

```groovy
repositories{
  jcenter()
  flatDir{
      dirs '../app/src/main/aar'
   }
}
```

为应用程序添加目标平台： `$cordova platform add android` `$ cordova platform add ios`

检查添加的平台： `$cordova platform ls`

1. Firebase Cloud Messaging支持

1. 在Firebase控制台中配置Firebase应用程序。
   1. 在[](https://console.firebase.google.com/)Firebase控制台中创建或添加项目。
      1. 在[Firebase控制台](https://console.firebase.google.com/)中选择&#x200B;**[!UICONTROL Add Project]**。
      1. 从现有Google Cloud项目列表中选择您的GCM项目，然后选择&#x200B;**[!UICONTROL Add Firebase]**。
      1. 在Firebase欢迎屏幕中，选择“将Firebase添加到Android应用程序”。
      1. 提供您的包名称和SHA-1，然后选择&#x200B;**[!UICONTROL Add App]**。 已下载Firebase应用程序的新`google-services.json`文件。
   1. 在[!UICONTROL Project Overview]中转到&#x200B;**[!UICONTROL Project Settings]**。
      1. 选择&#x200B;**[!UICONTROL General]**&#x200B;选项卡并下载“google-services.json”文件。
      1. 选择 **[!UICONTROL Cloud Messaging]** 选项卡。 复制[!UICONTROL Server Key]和[!UICONTROL Sender ID]，并将其提供给Marketo。
   1. 在PhoneGap应用程序中配置FCM。
      1. 将下载的`google-services.json`文件移到PhoneGap应用程序模块的根目录中。
      1. 从位置`platforms/android/app/src/main/java/com/gae/scaffolder/plugin`删除文件“MyFirebaseInstanceIDService”（已弃用）
      1. 按如下方式修改位置`platforms/android/app/src/main/java/com/gae/scaffolder/plugin`中的文件“MyFirebaseMessagingService”：

         ```
         import com.marketo.Marketo;
         
         public class MyFirebaseMessagingService extends FirebaseMessagingService{
         
         @Override
         public void onNewToken(String s){
           super.onNewToken(s);
           MarketoExtension.setPushNotificaitonTokens(s);
           //Add your code here
         }
         
         @Override
         public void onMessageReceived(RemoteMessage remoteMessage) {
           MarketoExtension.showPushNotificaiton(remoteMessage);
           //Add your code here
         }
         }
         ```

         1. 按如下方式修改位置插件/cordova-plugin-fcm/scripts中的“fcm_config_files_process.js”文件

            ```
            //change
            var strings = fs.readFileSync("platforms/android/res/values/strings.xml").toString();
            //to
            var strings = fs.readFileSync("platforms/android/app/src/main/res/values/strings.xml").toString();
            
            //AND change
            fs.writeFileSync("platforms/android/res/values/strings.xml", strings);
            //to
            fs.writeFileSync("platforms/android/app/src/main/res/values/strings.xml", strings);
            ```

### &#x200B;3. 在xCode中启用推送通知

在xCode项目中打开推送通知功能。

### &#x200B;4. 跟踪推送通知

将以下代码粘贴到`application:didFinishLaunchingWithOptions:`函数中。

>[!BEGINTABS]

>[!TAB 目标C]

按如下方式更新`applicationDidBecomeActive`方法。

```objectivec
Marketo *sharedInstance = [Marketo sharedInstance];

[sharedInstance trackPushNotification:launchOptions];
```

>[!TAB Swift]

按如下方式更新`applicationDidBecomeActive`方法。

```swift
let sharedInstance: Marketo = Marketo.sharedInstance()

sharedInstance.trackPushNotification(launchOptions)
```

>[!ENDTABS]

### &#x200B;5. 初始化Marketo框架

要在应用程序启动时初始化Marketo框架，请在JavaScript主文件中的`onDeviceReady`函数下添加以下代码。

传递`phonegap`作为PhoneGap应用的框架类型。

### 句法

```javascript
// This method will Initialize the Marketo Framework using Your MunchkinId and Secret Key
marketo.initialize(
  function() { console.log("MarketoSDK Init done."); },
  function(error) { console.log("an error occurred:" + error); },
  'YOUR_MUNCHKIN_ID',
  'YOUR_SECRET_KEY',
  'FRAMEWORK_TYPE'
);

// For session tracking, add following.
marketo.onStart(
  function(){ console.log("onStart."); },
  function(error){ console.log("Failed to report onStart." + error); }
);
```

### 参数

- 成功回调：Marketo框架初始化成功时要运行的函数。
- 失败回调：Marketo Framework初始化失败时要运行的函数。
- Munchkin ID：注册期间从Marketo收到的Munchkin ID。
- 密钥：注册期间从Marketo收到的密钥。

### &#x200B;6. 初始化Marketo推送通知

要初始化Marketo推送通知，请在主JavaScript文件中的初始化函数后添加以下代码。

### 句法

```javascript
// This function will Enable user notifications (prompts the user to accept push notifications in iOS)
marketo.initializeMarketoPush(
    function() { console.log("Marketo push successfully initialized."); },
    function(error) { console.log("an error occurred:" + error); },
    'YOUR_GCM_PROJECT_ID' // This is required for Android and will be ignored in iOS
);
```

### 参数

- 成功回调：如果Marketo推送通知初始化成功，则要运行的函数。
- 失败回调：Marketo推送通知初始化失败时要运行的函数。
- GCM_PROJECT_ID：创建应用程序后，在[Google开发人员控制台](https://console.developers.google.com/)中找到了GCM项目ID。

您还可以在注销时注销令牌。

```javascript
marketo. uninitializeMarketoPush(
  function() { console.log("Marketo push successfully uninitialized."); } ,
  function(error) { console.log("an error occurred:" + error); }
);
```

## 关联潜在客户

调用associateLead函数以创建Marketo潜在客户。

### 句法

```javascript
marketo.associateLead(
  function(){ console.log("MarketoSDK : Lead Added"); },
  function(error){ console.log("an error occurred:" + error); },
  'Lead_Data_JSON_String'
);
```

### 参数

- Success回调：在Marketo框架成功关联潜在客户时要运行的函数。
- 失败回调：在Marketo Framework无法关联潜在客户时运行的函数。
- 潜在客户数据：以JSON字符串格式表示的潜在客户数据。

### 示例

```javascript
// First create a lead as shown below
var lead = {};
lead[marketo.KEY_FIRST_NAME] = "Phone";
lead[marketo.KEY_LAST_NAME] = "Gap";
lead[marketo.KEY_EMAIL] = email;
lead[marketo.KEY_ADDRESS] = "demo address";
lead[marketo.KEY_CITY] = "city";
lead[marketo.KEY_STATE] = "state";
lead[marketo.KEY_COUNTRY] = "country";
lead[marketo.KEY_POSTAL_CODE] = "postalCode";
lead[marketo.KEY_GENDER] = "gender";
// To use lead custom field, use the REST API NAME as key
lead["REST API NAME"] = "value";

// Use associateLead function to associate it.
marketo.associateLead(
  function() { console.log("MarketoSDK : Lead Associated"); },
  function(error) { console.log("an error occurred:" + error); },
  JSON.stringify(lead)
);
```

## 报表操作

调用`reportaction`函数以报告用户操作。

### 句法

```javascript
marketo.reportaction(
  function(){ console.log("MarketoSDK : New event sent "); },
  function(error){ console.log("an error occurred:" + error); },
  'Action_Name',
  'Action_Data_JSON_String'
);
```

### 参数

- 成功回调：在Marketo框架成功报告操作时要运行的函数。
- 失败回调：Marketo框架无法报告操作时要运行的函数。
- 操作名称：操作名称。
- 操作数据： JSON字符串格式的操作数据。

### 示例

```javascript
// First create an event as below
var event = {
    "Action Type":"Add To Cart",
    "Action Details":"Adding Product in cart",
    "Action Metric":"10",
    "Action Length":"1"
}

marketo.reportaction(
    function(){ console.log("Reported action successfully."); },
    function(error){ console.log("Failed to report action." + error); },
    "Add To Cart",
    JSON.stringify(event)
);
```

## 会话报告

绑定“暂停”和“恢复”事件类型以报告“开始”和“停止”事件。 这些事件可跟踪在移动应用程序中所花费的时间，并且在Android中是必需的。

```javascript
//Add the following code in your www/js/index.js

bindEvents: function() {
   document.addEventListener('pause', this.onStop, false);
   document.addEventListener('resume', this.onStart, false);
},
onStop: function() {
   marketo.onStop(
       function(){ console.log("onStop"); },
       function(error){ console.log("Failed to report onStop." + error); }
   );
},
onStart: function() {
   marketo.onStart(
       function(){ console.log("onStart."); },
       function(error){console.log( "Failed to report onStart." + error); }
   );
},
```

## 创建潜在客户

可通过三种方式从混合应用程序创建潜在客户：

1. MARKETO MME SDK
1. MARKETO REST API
1. 表单提交

识别新商机的触发器和过滤器取决于创建方法：

- 使用MME SDK或REST API创建的潜在客户会出现在“创建的潜在客户”触发器和过滤器中。
- 通过表单提交创建的潜在客户显示在“填写表单”触发器和过滤器中。

在混合应用程序和Web应用程序中使用相同的潜在客户创建方法。 如果Web应用程序使用表单提交或REST API，请在混合应用程序中使用该方法。 如果Web应用程序既不使用也不使用这两种方法，请考虑使用MME SDK在Marketo中创建潜在客户。
