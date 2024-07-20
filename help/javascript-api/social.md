---
title: 社交
description: 社交
feature: Social, Javascript
exl-id: 82d2b86f-5efe-4434-b617-d27f76515a79
source-git-commit: 66add4c38d0230c36d57009de985649bb67fde3e
workflow-type: tm+mt
source-wordcount: '776'
ht-degree: 2%

---

# 社交

[Marketo社交营销](https://business.adobe.com/products/marketo/social-marketing.html)允许营销人员将社交小组件嵌入网站和登陆页面中。 社交小组件包括投票、社交分享按钮、视频、抽奖和推荐优惠等促销活动。

## 嵌入式共享小部件示例

```html
<!-- Marketo Widget Loader Script --> 

<script type="text/javascript" src="//b2c-mlm.marketo.com/jsloader/271d8232-1500-4305-b7ed-05d451b9ee0c/loader.php.js">
</script>

 <!-- The Location of the Social Widget --> 

<divclass='cf_widgetloader cf_w_245d8f3c0955454cbd26abc39d0d874c'="" options="{&quot;outerHeight&quot;:400, &quot;outerWidth&quot;:600}">
</divclass='cf_widgetloader'>
```

![social-share-widget](assets/social-share-widget.png)

自定义社交构件有两种基本方法：

1. 使用产品的常规UI并附加事件侦听器，以在UI中发生某些操作时接收通知，以执行其他业务逻辑。
1. 将产品的普通UI替换为自定义UI，并根据需要激活UI的弹出式“阶段”。

## 将事件附加到常规UI

可通过两种方式在CF JavaScript库中全局订阅事件或订阅单个小部件。 事件记录在“事件”表的下方。

### 全局事件订阅

```html
<script>
cf_scripts.afterload(function(){
    CF.events.listen("event_name_here",
        function(event, arg1){
            //Your code to do something on the event goes here.
            //It will be fired whenever ANY widget fires the event "event_name_here".
        }
    );
});
</script>
```

### 每个小组件事件订阅

```html
<script>
cf_scripts.afterload(function(){
    CF.widget.listen("widget_name_here", "event_name_here",
        function(event, arg1){
            //Your code to do something on the event goes here.
            //It will be fired whenever the widget named "widget_name_here" fires the event "event_name_here".
        }
    );
});
</script>
```

## 示例

此示例显示了用户完成名为“referral_Signup”的小部件的优惠注册后ID为“signedUp”的先前隐藏的元素。

```html
<div id='signedUp'style='display:none; color:green;'>This is a custom message to let you know that you signed up!</div>
<div class='cf_widgetLoader cf_w_referral_SignUp'></div>

<script>
    cf_scripts.afterload(function(){
        CF.widget.listen("referral_SignUp", "offer_enrolled", function(){
        cf_jq("#signedUp").show();
    });
});
</script>
```

## 基本事件表

| 事件名称 | 描述 | 使用此事件的构件 | 支持的参数（传递到事件回调函数） |
| --- | --- | --- | --- | 
| share_sent | 每次将共享请求发送到服务器进行处理时触发 | 所有能够共享的widget | 1.”share_sent&quot; （字符串）<br>2。 参数已发送（对象） |
| share_成功次数 | 成功处理共享请求时触发。 | 所有能够共享的构件。 | 1.”share_success&quot; （字符串）<br>2。 共享响应对象，包含发送的消息和缩短的URL（对象） |
| 投票成功 | 当用户在投票中成功投票时触发。 | 投票、VS、投票小组件 | 1. &quot;vote_success&quot; （字符串）<br>2。 投票赞成的项目，包括标题、描述、实体标识符（对象） |
| offer_registered | 用户成功注册选件时触发 | 所有优惠小组件 | 1.”offer_registered&quot; （字符串）<br>2。 已更改用户属性（对象），<br>3。 更改的用户属性（对象） |
| profile_saved | 当用户从配置文件捕获更新其配置文件时触发 | 已启用配置文件捕获的所有非选件小组件 | 1.”profile_saved&quot; （字符串）<br>2。 已更改用户属性（对象）<br>3。 更改的用户属性（对象） |
| video_loaded | 在完全加载和初始化嵌入的视频时触发。 | VideoShare构件 | 1. &quot;video_loaded&quot;（字符串） 2. 保存视频的“.cf_videoshare_wrap”元素（jQuery对象） |

## 将UI替换为自定义UI

若要使用自定义UI替换UI，必须先关闭普通UI，可通过将选项&#x200B;_popupUIOnly_&#x200B;设置为&#x200B;_true_&#x200B;来完成此操作。 设置此选项后，标准UI将不会在页面加载时渲染，而是由小组件获取其数据，并通过调用&#x200B;_CF.widget.activate_&#x200B;函数并提供其应执行的操作的选项来等待您启动其某个弹出阶段。

以下是创建自定义按钮的示例，该按钮会为名为&#x200B;_referral_Signup_&#x200B;的推荐优惠构件启动推荐优惠注册流程。

```html
<button id="myNewSignUpButton">My newSign Up button</button>

<!-- Turn off the defaultreferral offer UI by setting popupUIOnly to true-->
<div class="cf_widgetLoader cf_w_referral_SignUp" options="{popupUIOnly:true}"></div>

<script>
cf_scripts.afterload(function($, CF){
    // After the cf script library has loaded, find the button with
    // id="myNewSignUpButton", and attach a click listener to it.
    $("#myNewSignUpButton").click(function(){
        // When it is clicked, activate the popup widget flow for the referral,
        // asking it to point to the clicked button.
        CF.widget.activate("referral_SignUp", {pointTo:$(this)});
    });
});
</script>
```

由于添加点击处理程序很常见，因此可以使用快捷方法添加它们。 下面的功能等效于前面的示例。

```html
<button id="myNewSignUpButton">My newSign Up button</button>
<div class="cf_widgetLoader cf_w_referral_SignUp" options="{popupUIOnly:true}"></div>

<script>
cf_scripts.afterload(function($, CF){
    // Use the addClickActivate convenience method, which will
    // automatically make the popup point at the clicked item with id myNewSignUpButton.
    CF.widget.addClickActivate("#myNewSignUpButton", "referral_SignUp", {});
});
</script>
```

## 将构件UI数据放入替换UI

如果您需要有关该构件的数据来绘制替代UI，则可以从特殊事件&#x200B;_ui_data_&#x200B;中获取数据。 您可以使用普通`CF.widget.listen`函数侦听此事件，但这样做可能会导致出现潜在的争用情况，即在小部件已触发_ui_data_事件之后添加事件侦听器，这样您就永远不会接收数据。 为避免此争用，只要执行了会导致重新绘制构件标准UI的操作，即会触发`CF.widget.uiData_ method instead, which will give you the most recent available _ui_data_, and listen for all future updates as well. The _ui_data`事件，即使您已使用`popupUIOnly`选项禁用该UI也是如此。

使用`uiData`函数显示用户参加小部件名称为&#x200B;_sweeps_Sweepstakes_&#x200B;的抽奖活动的条目数的示例。

```html
<span>You have <span id="entryCount">?</span> entries.</span>

<div class="cf_widgetLoader cf_w_sweeps_Sweepstakes"></div>

<button id='myNewSweepsButton'>New Sweeps Up Button!</button>

<script>
cf_scripts.afterload(function($, CF){
    CF.widget.uiData("sweeps_Sweepstakes", function(uiData){
        if(uiData.user && uiData.userStatus && uiData.userEntries){
            $("entryCount").html(""+ uiData.userEntries);
        }
        else{
            $("entryCount").html("0");
        }
    });
});
</script>
```

## 反向链接优惠注册UI数据参考

| 类型 | 描述 |
|---------------|----------------------------------------------------|
| 日期 | 格式为“yyyy-MM-dd”的日期值 |
| 数字 | 整数或浮点数 |
| 富文本 | HTML字符串 |
| 分数 | 带符号的32位整数 |
| sfdc营销活动 | 用于Salesforce营销活动管理集成 |
| 文本 | 文本字符串 |

## 反向链接优惠TrackProgress UI数据参考

| 类型 | 描述 |
|---------------|----------------------------------------------------|
| 日期 | 格式为“yyyy-MM-dd”的日期值 |
| 数字 | 整数或浮点数 |
| 富文本 | HTML字符串 |
| 分数 | 带符号的32位整数 |
| sfdc营销活动 | 用于Salesforce营销活动管理集成 |
| 文本 | 文本字符串 |

## 抽奖UI数据引用（用于社交活动抽奖，而不是LM抽奖）

| 类型 | 描述 |
|---------------|----------------------------------------------------|
| 日期 | 格式为“yyyy-MM-dd”的日期值 |
| 数字 | 整数或浮点数 |
| 富文本 | HTML字符串 |
| 分数 | 带符号的32位整数 |
| sfdc营销活动 | 用于Salesforce营销活动管理集成 |
| 文本 | 文本字符串 |

## 社交登录（表单填写小组件）数据参考

| 类型 | 描述 |
|---------------|----------------------------------------------------|
| 日期 | 格式为“yyyy-MM-dd”的日期值 |
| 数字 | 整数或浮点数 |
| 富文本 | HTML字符串 |
| 分数 | 带符号的32位整数 |
| sfdc营销活动 | 用于Salesforce营销活动管理集成 |
| 文本 | 文本字符串 |

```javascript
{
"alt_id": "http://www.facebook.com/profile.php?id=1526228678",
"provider_name": "facebook",
"default_photo_url": "https://graph.facebook.com/1526228678/picture?type=large",
"email": "ian.b.taylor@gmail.com",
"verified_email": "ian.b.taylor@gmail.com",
"gender": "male",
"preferred_user_name": "IanTaylor",
"display_name": "Ian Taylor",
"birth_date": 343954800000,
"first_name": "Ian",
"last_name": "Taylor",
"city": null,
"state": null,
"region": null,
"postal_code": null,
"country": null,
"time_zone": null,
"connection_count": 0,
"credentials": {
"uid": "1526228678",
"scopes": "publish_actions",
"expires": "1371994082",
"accessToken": "BAAGFJ0KUFpcBABuNMptmYY...",
"type": "Facebook"
},
"about_me": null,
"cur_pos_title": "Senior Staff Engineer",
"phone_number": null,
"company": "Marketo",
"cur_pos_start_date": 1333231200000,
"cur_pos_summary": null
}
```
