---
title: '[!DNL Ionic]'
feature: Mobile Marketing
description: 分步指南将Marketo Cordova插件与Ionic集成、启用推送通知、初始化SDK、跟踪会话并关联潜在客户。
exl-id: 204e5fb4-c9d6-43a6-9d77-0b2a67ddbed3
TQID: https://experienceleague.adobe.com/UTNWd69NliR896RcO-XM2GG35liuLeNNhTXo9GRtB4o
product_v2:
  - id: b27e5950-9033-45ac-9f86-eb22e567f615
feature_v2:
  - id: ea90ebee-5c84-42d9-8b21-006bdabc95a3
role_v2:
  - id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
topic_v2:
  - id: aa2f3246-cb95-4b30-8899-fdf7d73550cc
source-git-commit: 3e6d310c5aec1a3435424fb122b71d825db5af0e
workflow-type: tm+mt
source-wordcount: 581
ht-degree: 2%

---

# 离子型

将Marketo Cordova插件与[!DNL Ionic]应用程序集成。[!DNL Ionic] 当前不支持电容器。

## 先决条件

1. [在Marketo管理员中添加应用程序](https://experienceleague.adobe.com/zh-hans/docs/marketo/using/product-docs/mobile-marketing/admin/add-a-mobile-app)并获取该应用程序的密钥和Munchkin ID。
1. 为[iOS](push-notifications.md)或[Android](push-notifications.md)设置推送通知。
1. 安装[[!DNL Ionic]](https://ionicframework.com/getting-started/)和[Cordova CLI](https://cordova.apache.org/docs/en/latest/guide/cli/)。

## 安装说明

### 设置Marketo [!DNL Ionic]插件

1. 转到[!DNL Ionic]应用程序目录并运行以下命令以添加Marketo插件：

   `$ ionic plugin add https://github.com/Marketo/PhoneGapPlugin.git --variable APPLICATION_SECRET_KEY="YOUR_APPLICATION_SECRET"`

1. 运行以下命令以确认添加了插件：

   `$ ionic plugin list com.marketo.plugin 0.X.0 "MarketoPlugin"`

### 迁移到较新版本（可选）

1. 要删除现有插件，请运行以下命令：

   `$ ionic plugin remove com.marketo.plugin`

1. 要再次添加插件，请运行以下命令：

   `$ ionic plugin add https://github.com/Marketo/PhoneGapPlugin.git --variable APPLICATION_SECRET_KEY="YOUR_APPLICATION_SECRET"`

### 在xCode中启用推送通知

1. 在xCode项目中打开推送通知功能。![通知功能](assets/notification-capability.png)

### 跟踪推送通知

将以下代码粘贴到`application:didFinishLaunchingWithOptions:`函数中。

>[!BEGINTABS]

>[!TAB 目标C]

```objectivec
Marketo *sharedInstance = [Marketo sharedInstance];

[sharedInstance trackPushNotification:launchOptions];
```

>[!TAB Swift]

```swift
let sharedInstance: Marketo = Marketo.sharedInstance()

sharedInstance.trackPushNotfication(launchOptions)
```

>[!ENDTABS]

### 初始化Marketo框架

要在应用程序启动时初始化Marketo框架，请在JavaScript主文件中的`onDeviceReady`函数下添加以下代码。

传递`ionicCordova`作为[!DNL Ionic] Cordova应用的框架类型。

#### 句法

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

- 成功回调：Marketo框架初始化成功时要运行的函数。
- 失败回调：Marketo Framework初始化失败时要运行的函数。
- Munchkin ID：注册期间从Marketo收到的Munchkin ID。
- 密钥：注册期间从Marketo收到的密钥。

### 初始化Marketo推送通知

要初始化Marketo推送通知，请在主JavaScript文件中的初始化函数后添加以下代码。

#### 句法

```javascript
// This function will Enable user notifications (prompts the user to accept push notifications in iOS)
marketo.initializeMarketoPush(
    function() { console.log("Marketo push successfully initialized."); },
    function(error) { console.log("an error occurred:" + error); },
    'YOUR_GCM_PROJECT_ID' // This is required for Android and will be ignored in iOS
);
```

#### 参数

- 成功回调：如果Marketo推送通知初始化成功，则要运行的函数。
- 失败回调：Marketo推送通知初始化失败时要运行的函数。
- GCM_PROJECT_ID：创建应用程序后，在[Google开发人员控制台](https://accounts.google.com/ServiceLogin?service=cloudconsole&passive=1209600&osid=1&continue=https://console.cloud.google.com/apis/dashboard&followup=https://console.cloud.google.com/apis/dashboard)中找到了GCM项目ID。

您还可以在注销时注销令牌。

```javascript
marketo.uninitializeMarketoPush(
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
