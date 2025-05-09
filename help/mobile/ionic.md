---
title: '[!DNL Ionic]'
feature: Mobile Marketing
description: 在移动设备的Marketo中使用 [!DNL Ionic]
exl-id: 204e5fb4-c9d6-43a6-9d77-0b2a67ddbed3
source-git-commit: 6fc45ff98998217923e2a5b02d00d1522fe3272c
workflow-type: tm+mt
source-wordcount: '606'
ht-degree: 1%

---

# 离子型

本主题介绍如何集成Marketo Cordova插件。 当前不支持[!DNL Ionic]电容器。

## 先决条件

1. [在Marketo Admin中添加应用程序](https://experienceleague.adobe.com/zh-hans/docs/marketo/using/product-docs/mobile-marketing/admin/add-a-mobile-app)（获取应用程序密钥和Munchkin ID）。
1. 设置推送通知([iOS](push-notifications.md) | [Android](push-notifications.md) )。
1. 安装[[!DNL Ionic]](https://ionicframework.com/getting-started/)和[Cordova CLI](https://cordova.apache.org/docs/en/latest/guide/cli/)。

## 安装说明

### 设置Marketo [!DNL Ionic]插件

1. 如果已安装Cordova CLI，请转到[!DNL Ionic]应用程序目录并运行以下命令以将Marketo插件添加到应用程序中：

   `$ ionic plugin add https://github.com/Marketo/PhoneGapPlugin.git --variable APPLICATION_SECRET_KEY="YOUR_APPLICATION_SECRET"`

1. 要确认插件已添加到应用程序中，请运行以下命令：

   `$ ionic plugin list com.marketo.plugin 0.X.0 "MarketoPlugin"`

### 迁移到较新版本（可选）

1. 要删除现有插件，请运行以下命令：

   `$ ionic plugin remove com.marketo.plugin`

1. 要读取插件，请运行以下命令：

   `$ ionic plugin add https://github.com/Marketo/PhoneGapPlugin.git --variable APPLICATION_SECRET_KEY="YOUR_APPLICATION_SECRET"`

### 在xCode中启用推送通知

1. 在xCode项目中打开推送通知功能。![通知功能](assets/notification-capability.png)

### 跟踪推送通知

将以下代码粘贴到`application:didFinishLaunchingWithOptions:`函数中。

>[!BEGINTABS]

>[!TAB 目标C]

```
Marketo *sharedInstance = [Marketo sharedInstance];

[sharedInstance trackPushNotification:launchOptions];
```

>[!TAB Swift]

```
let sharedInstance: Marketo = Marketo.sharedInstance()

sharedInstance.trackPushNotfication(launchOptions)
```

>[!ENDTABS]

### 初始化Marketo框架

要确保在应用程序启动时启动Marketo框架，请在主JavaScript文件的`onDeviceReady`函数下添加以下代码。

您必须将`ionicCordova`作为[!DNL Ionic] Cordova应用的框架类型传递。

#### 语法

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

#### 参数

- 成功回调：Marketo框架初始化成功时要执行的函数。
- 失败回调：当Marketo Framework初始化失败时要执行的函数。
- MUNCHKIN ID ：注册时从Marketo收到的Munchkin ID。
- 密钥：注册时从Marketo收到的密钥。

### 初始化Marketo推送通知

要确保启动Marketo推送通知，请在主JavaScript文件中初始化函数后添加以下代码。

#### 语法

```javascript
// This function will Enable user notifications (prompts the user to accept push notifications in iOS)
marketo.initializeMarketoPush(
    function() { console.log("Marketo push successfully initialized."); },
    function(error) { console.log("an error occurred:" + error); },
    'YOUR_GCM_PROJECT_ID' // This is required for Android and will be ignored in iOS
);
```

#### 参数

- 成功回调：Marketo推送通知初始化成功时要执行的函数。
- 失败回调：当Marketo推送通知初始化失败时要执行的函数。
- GCM_PROJECT_ID ：创建应用程序后，在[Google开发人员控制台](https://accounts.google.com/ServiceLogin?service=cloudconsole&amp;passive=1209600&amp;osid=1&amp;continue=https://console.cloud.google.com/apis/dashboard&amp;followup=https://console.cloud.google.com/apis/dashboard)中找到了GCM项目ID。

注销时也可以注销令牌。

```javascript
marketo.uninitializeMarketoPush(
  function() { console.log("Marketo push successfully uninitialized."); } ,
  function(error) { console.log("an error occurred:" + error); }
);
```

## 关联潜在客户

您可以通过调用associateLead函数来创建Marketo Lead。

### 语法

```javascript
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

```javascript
// First create a lead as shown below
var lead = {};
lead[marketo.KEY_FIRST_NAME] = "Ionic";
lead[marketo.KEY_LAST_NAME] = "App";
lead[marketo.KEY_EMAIL] = email;
lead[marketo.KEY_ADDRESS] = "demo address";
lead[marketo.KEY_CITY] = "city";
lead[marketo.KEY_STATE] = "state";
lead[marketo.KEY_COUNTRY] = "country";
lead[marketo.KEY_POSTAL_CODE] = "postalCode";
lead[marketo.KEY_GENDER] = "gender";

// Use associateLead function to associate it.
marketo.associateLead(
  function() { console.log("MarketoSDK : Lead Associated"); },
  function(error) { console.log("an error occurred:" + error); },
  JSON.stringify(lead)
);
```

## 报表操作

您可以通过调用`reportaction`函数来报告用户执行的任何操作。

### 语法

```javascript
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

绑定“暂停”和“恢复”事件类型，如下所示，以报告“开始”和“停止”事件。 用于跟踪在移动应用程序上花费的时间。 注意：这在Android中是必需的。

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

根据使用的方法，新创建的潜在客户会被不同的触发器和过滤器识别。 使用MME SDK或REST API创建的潜在客户会出现在“创建的潜在客户”触发器和过滤器中。 通过提交表单创建的潜在客户显示在“填写表单”触发器和过滤器中。

最佳做法是在创建潜在客户时与Web应用程序使用的方法保持一致。 如果您已有使用表单提交作为创建潜在客户机制的Web应用程序，则在混合应用程序中创建潜在客户时，请使用该相同的机制。 如果您已有使用我们的REST API作为创建潜在客户机制的Web应用程序，请在混合应用程序中创建潜在客户时使用该相同的机制。 如果您未使用表单提交或REST API作为在Web应用程序中创建潜在客户的机制，则可以考虑使用MME SDK在Marketo中创建潜在客户。
