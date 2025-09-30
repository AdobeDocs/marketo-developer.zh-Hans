---
title: PhoneGap
feature: Mobile Marketing
description: 使用Cordova设置Marketo PhoneGap插件，配置Firebase Cloud Messaging，启用iOS和Android推送，跟踪通知，以及初始化SDK。
exl-id: 99f14c76-9438-4942-9309-643bca434d07
source-git-commit: 7557b9957c87f63c2646be13842ea450035792be
workflow-type: tm+mt
source-wordcount: '813'
ht-degree: 1%

---

# PhoneGap

Marketo PhoneGap插件的集成

## 先决条件

1. [在Marketo Admin中添加应用程序](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/mobile-marketing/admin/add-a-mobile-app)&#x200B;(获取您的应用程序密钥和Munchkin ID)。
1. 设置推送通知([iOS](push-notifications.md) | [Android](push-notifications.md))。
1. [安装PhoneGap/Cordova CLI](https://cordova.apache.org/docs/en/latest/guide/cli/)。

## 安装说明

1. 设置Marketo PhoneGap插件

   如果已安装Cordova CLI，请转到PhoneGap应用程序目录并运行以下命令以将Marketo插件添加到应用程序中：

   `$ cordova plugin add https://github.com/Marketo/PhoneGapPlugin.git --variable APPLICATION_SECRET_KEY="YOUR_APPLICATION_SECRET"`

1. 安装FCM插件

   `$ cordova plugin add cordova-plugin-fcm`

   要确认插件已添加到应用程序中，请运行以下命令并验证

   `$ cordova plugin ls com.marketo.plugin 0.X.0 "MarketoPlugin" cordova-plugin-fcm 2.1.2 "FCMPlugin"`

**迁移到较新版本（可选）**

要删除现有插件，请运行以下命令：

`$ cordova plugin remove com.marketo.plugin`

要重新添加插件，请运行以下命令：

`$ cordova plugin add https://github.com/Marketo/PhoneGapPlugin.git --variable APPLICATION_SECRET_KEY="YOUR_APPLICATION_SECRET"`

**Cordova版本8.0.0 (Cordova@Android7.0.0)及更高版本**

构建Cordova Android平台后，使用Android Studio打开该应用程序，并更新在`dirs`文件夹中找到的`Marketo.gradle`文件的`com.marketo.plugin`值。

```
repositories{
  jcenter()
  flatDir{
      dirs '../app/src/main/aar'
   }
}
```

添加应用程序`$cordova platform add android` `$ cordova platform add ios`的目标平台

检查添加了`$cordova platform ls`的平台列表

1. Firebase Cloud Messaging支持

1. 在Firebase控制台上配置Firebase应用程序。
   1. 在[&#128279;](https://console.firebase.google.com/)Firebase控制台上创建/添加项目。
      1. 在[Firebase控制台](https://console.firebase.google.com/)中选择&#x200B;**[!UICONTROL Add Project]**。
      1. 从现有Google Cloud项目列表中选择您的GCM项目，然后选择&#x200B;**[!UICONTROL Add Firebase]**。
      1. 在Firebase欢迎屏幕中，选择“将Firebase添加到Android应用程序”。
      1. 提供您的包名称和SHA-1，然后选择&#x200B;**[!UICONTROL Add App]**。 已下载Firebase应用程序的新`google-services.json`文件。
   1. 在&#x200B;**[!UICONTROL Project Settings]**&#x200B;中导航到[!UICONTROL Project Overview]
      1. 单击&#x200B;**[!UICONTROL General]**&#x200B;选项卡。 下载“google-services.json”文件。
      1. 单击&#x200B;**[!UICONTROL Cloud Messaging]**&#x200B;选项卡。 复制[!UICONTROL Server Key]和[!UICONTROL Sender ID]。 将这些[!UICONTROL Server Key]和[!UICONTROL Sender ID]提供给Marketo。
   1. 在Phonegap应用程序中配置FCM更改
      1. 将下载的`google-services.json`文件移到Phonegap应用程序模块的根目录中
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

### 3.在xCode中启用推送通知

在xCode项目中打开推送通知功能。

### 4.跟踪推送通知

将以下代码粘贴到`application:didFinishLaunchingWithOptions:`函数中。

>[!BEGINTABS]

>[!TAB 目标C]

更新`applicationDidBecomeActive`方法，如下所示

```
Marketo *sharedInstance = [Marketo sharedInstance];

[sharedInstance trackPushNotification:launchOptions];
```

>[!TAB Swift]

更新`applicationDidBecomeActive`方法，如下所示

```
let sharedInstance: Marketo = Marketo.sharedInstance()

sharedInstance.trackPushNotification(launchOptions)
```

>[!ENDTABS]

### 5.初始化Marketo框架

要确保在应用程序启动时启动Marketo框架，请在主JavaScript文件的`onDeviceReady`函数下添加以下代码。

请注意，我们必须将`phonegap`作为PhoneGap应用的框架类型传递。

### 句法

```
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

- 成功回调：Marketo框架初始化成功时要执行的函数。
- 失败回调：当Marketo Framework初始化失败时要执行的函数。
- Munchkin ID ：注册时从Marketo收到的Munchkin ID。
- 密钥：注册时从Marketo收到的密钥。

### 6.初始化Marketo推送通知

要确保启动Marketo推送通知，请在主JavaScript文件中添加初始化函数之后添加以下代码。

### 句法

```
// This function will Enable user notifications (prompts the user to accept push notifications in iOS)
marketo.initializeMarketoPush(
    function() { console.log("Marketo push successfully initialized."); },
    function(error) { console.log("an error occurred:" + error); },
    'YOUR_GCM_PROJECT_ID' // This is required for Android and will be ignored in iOS
);
```

### 参数

- 成功回调：Marketo推送通知初始化成功时要执行的函数。
- 失败回调：当Marketo推送通知初始化失败时要执行的函数。
- GCM_PROJECT_ID ：创建应用程序后，在[Google开发人员控制台](https://console.developers.google.com/)中找到了GCM项目ID。

注销时也可以注销令牌。

```
marketo. uninitializeMarketoPush(
  function() { console.log("Marketo push successfully uninitialized."); } ,
  function(error) { console.log("an error occurred:" + error); }
);
```

## 关联潜在客户

您可以通过调用associateLead函数来创建Marketo Lead。

### 句法

```
marketo.associateLead(
  function(){ console.log("MarketoSDK : Lead Added"); },
  function(error){ console.log("an error occurred:" + error); },
  'Lead_Data_JSON_String'
);
```

### 参数

- Success回调：在Marketo框架成功关联潜在客户时要执行的函数。
- 失败回调：当Marketo Framework无法关联潜在客户时要执行的函数。
- 潜在客户数据：JSON字符串格式的潜在客户数据。

### 示例

```
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

您可以通过调用`reportaction`函数来报告用户执行的任何操作。

### 句法

```
marketo.reportaction(
  function(){ console.log("MarketoSDK : New event sent "); },
  function(error){ console.log("an error occurred:" + error); },
  'Action_Name',
  'Action_Data_JSON_String'
);
```

### 参数

- 成功回调：在Marketo Framework成功报告操作时执行的函数。
- 失败回调：Marketo Framework无法报告操作时要执行的函数。
- 操作名称：操作名称。
- 操作数据：JSON字符串格式的操作数据。

### 示例

```
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

绑定“暂停”和“恢复”事件类型，如下所示，以报告“开始”和“停止”事件。  用于跟踪在移动应用程序上花费的时间。 注意：这在Android中是必需的。

```
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

根据使用的方法，新创建的潜在客户将由不同的触发器和过滤器识别。 使用MME SDK或REST API创建的潜在客户会出现在“创建的潜在客户”触发器和过滤器中。 通过提交表单创建的潜在客户显示在“填写表单”触发器和过滤器中。

最佳做法是在创建潜在客户时与Web应用程序使用的方法保持一致。 如果您已有使用表单提交作为创建潜在客户机制的Web应用程序，则在混合应用程序中创建潜在客户时，请使用该相同的机制。 如果您已有使用我们的REST API作为创建潜在客户机制的Web应用程序，请在混合应用程序中创建潜在客户时使用该相同的机制。 如果您既不使用表单提交也不使用REST API作为在Web应用程序中创建潜在客户的机制，则可以考虑使用MME SDK在Marketo中创建潜在客户。
