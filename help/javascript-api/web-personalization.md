---
title: Web 个性化
description: Web Personalization JavaScript API和RTP标记指南，涵盖页面查看事件、帐户设置、机器人排除以及核心脚本和按需脚本
feature: Web Personalization, Javascript
exl-id: b2c26b28-e9bf-4faf-8b6e-c102f41aeaa1
TQID: https://experienceleague.adobe.com/yplunKmgjOJ7gJTA2TDc9cfJXyXbrVWuM-NdVbDMN4A
product_v2: id: b27e5950-9033-45ac-9f86-eb22e567f615
feature_v2: id: e2290edd-b061-4880-9d79-dee306cf5aa9id: ed6be6bb-75bb-4ea9-9a42-3bcaa65e1bcc
subfeature_v2: id: cdd4e0f6-e87e-453f-88ee-2ee54a7de272
role_v2: id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
topic_v2: id: b5ce8718-c3af-4fdb-a1a9-fca32f83a87cid: e0eb8757-182f-49f3-94a4-1587d16f5094
source-git-commit: 3e6d310c5aec1a3435424fb122b71d825db5af0e
workflow-type: tm+mt
source-wordcount: 435
ht-degree: 6%

---

# Web 个性化

Web Personalization JavaScript API跟踪事件并动态自定义网页。 它扩展了平台的自动个性化功能。

相关功能包括[自定义数据事件](custom-data-events.md)、[动态内容](web-personalization.md)、[获取访客数据](get-visitor-data.md)和[排除特定机器人的标记](#exclude_tag_for_specific_bots)。

- 您必须是Web Personalization客户并在您的网站上部署[RTP标记](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/web-personalization/rtp-tag-implementation/deploy-the-rtp-javascript)，然后才能使用用户上下文API。
- RTP不支持基于帐户的营销指定帐户列表。 ABM列表和代码仅与在RTP中管理的已上传帐户列表（CSV文件）相关。

## 标记设置

在每个个性化页面的标题中插入RTP标记。

```javascript
<!-- RTP tag -->
<script type='text/javascript'>
(function(c,h,a,f,e,i){c[a]=c[a]||function(){(c[a].q=c[a].q||[]).push(arguments)};
c[a].p=e;c[a].a=i;var g=h.createElement("script");g.async=true;g.type="text/javascript";
g.src=f;var b=h.getElementsByTagName("script")[0];b.parentNode.insertBefore(g,b)})
(window,document,"rtp","[rtp-js-cdn-url]","[pod-url]","[accountId]");
</script>
<!-- End of RTP tag -->
```

## 帐户设置

标记会自动调用此方法以设置相关的帐户ID。 如果您希望为不同的域使用不同的帐户，请显式设置帐户ID。

| 参数 | 可选/必填 | 类型 | 描述 |
| --- | --- | --- | --- |
| &#39;setAccount&#39; | 必需 | 字符串 | 方法名称。 |
| accountId | 必需 | 字符串 | 帐户ID。 |

```javascript
var accountId = '561-HYG-937';
rtp('setAccount', accountId);
```

## 事件发送函数

此方法会发送用于页面跟踪的查看事件。 以下示例中的第一个调用将当前页面URL作为访客页面查看进行跟踪。

传递可选的“page”参数以覆盖当前页面，如第二次调用中所示。

| 参数 | 可选/必填 | 类型 | 描述 |
| --- | --- | --- | --- |
| &#39;发送&#39; | 必需 | 字符串 | 方法操作。 |
| &#39;视图&#39; | 必需 | 字符串 | 方法名称。 |
| 页面 | 可选 | 字符串 | 相对路径或整页URL。 |

```javascript
// Example for Default Page
rtp('send', 'view');

// Example for Overriding Default Page
var page = 'my-page?param=1';
rtp('send', 'view', page);
```

## 排除特定机器人（用户代理）的标记

要阻止已识别的机器人向Web Personalization平台发送数据，请将以下`if`语句添加到标记脚本中。

此示例不包括Web Personalization活动中的“GoogleBot|msnbot”用户代理。

```javascript
<!-- RTP tag -->
<script type='text/javascript'>
if(navigator.userAgent.match(/.(Googlebot|msnbot)./gi) == null){
    (function(c,h,a,f,i){c[a]=c[a]||function(){(c[a].q=c[a].q||[]).push(arguments)};
    c[a].a=i;var g=h.createElement("script");g.async=true;g.type="text/javascript";
    g.src=f+'?rh='+c.location.hostname+'&aid='+i;var b=h.getElementsByTagName("script")[0];b.parentNode.insertBefore(g,b);
})(window,document,"rtp","//[cdn-pod-X-url]/rtp-api/v1/rtp.js","[accountId]");

    rtp('send','view');
    rtp('get', 'campaign', true);
}
</script>
<!-- End of RTP tag -->
```

## JavaScript调用说明

下表介绍了添加到使用Web Personalization和预测内容的网站的JavaScript。

### 核心/依赖于JavaScript

| 名称 | 描述 | 控件 |
| --- | --- | --- |
| rtp.js | - | 由Marketo控制 |
| jquery.min.js | v1.8.3 | 可以通过联系Marketo客户支持来禁用 |
| jquery-custom-ui-min.js | v1.9.2 | 可以通过联系Marketo客户支持来禁用 |
| query-ui-1.8.17-dialog.js | v1.9.2* | 可以通过联系Marketo客户支持来禁用 |

*仅在缺少jQuery UI对话框时使用。

### On Demand JavaScript

| 名称 | 描述 | 控件 |
| --- | --- | --- |
| ga集成–2.0.1.js | 在启用Google Analytics/Facebook/SiteCatalyst集成时使用 | 由Marketo控制 |
| insightera-bar-2.1.js | 在启用了预测内容推荐栏时使用 | 由Marketo控制 |
| froogalop2.min.js | 在启用了内容跟踪并且页面上存在Vimeo播放器时使用 | - |
| iframe-api-v1.js | 在启用了内容跟踪并且页面上存在YouTube播放器时使用 | - |
