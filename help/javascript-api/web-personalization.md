---
title: Web 个性化
description: Web Personalization JavaScript API和RTP标记指南，涵盖页面查看事件、帐户设置、机器人排除以及核心脚本和按需脚本
feature: Web Personalization, Javascript
exl-id: b2c26b28-e9bf-4faf-8b6e-c102f41aeaa1
source-git-commit: 7557b9957c87f63c2646be13842ea450035792be
workflow-type: tm+mt
source-wordcount: '422'
ht-degree: 5%

---

# Web 个性化

Web Personalization JavaScript API扩展了平台的自动个性化功能。 它允许对网页进行事件跟踪和动态自定义。 其他功能：[自定义数据事件](custom-data-events.md)、[动态内容](web-personalization.md)、[获取访客数据](get-visitor-data.md)、[排除特定机器人的标记](#exclude_tag_for_specific_bots)。

- 您必须成为Web Personalization客户并在您的网站上部署[RTP标记](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/web-personalization/rtp-tag-implementation/deploy-the-rtp-javascript)，然后才能使用用户上下文API。
- RTP不支持基于帐户的营销指定帐户列表。 ABM列表和代码仅与在RTP中管理的已上传帐户列表（CSV文件）相关。

## 标记设置

RTP标记应插入到个性化页面的标题中。

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

在标记级别自动调用此方法以设置相关帐户ID。 当您希望在不同域之间拆分时，可以设置帐户ID。

| 参数 | 可选/必填 | 类型 | 描述 |
|--------------|-------------------|--------|--------------|
| &#39;setAccount&#39; | 必需 | 字符串 | 方法名称。 |
| accountId | 必需 | 字符串 | 帐户ID。 |

```javascript
var accountId = '561-HYG-937';
rtp('setAccount', accountId);
```

## 事件发送函数

此方法会发送一个用于页面跟踪的查看事件。 在以下示例中，当前页面URL被跟踪为访客页面查看。

通过在此方法中传递可选的“page”参数，可以覆盖当前页面。

| 参数 | 可选/必填 | 类型 | 描述 |
|-----------|-------------------|--------|---------------------------------|
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

要将特定浏览器排除在将数据发送到Web Personalization平台之外（对于已识别的机器人），请将以下IF语句添加到标记脚本中。

在以下代码示例中，“GoogleBot|msnbot”用作机器人示例，以从Web Personalization活动中排除。

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

在使用Web Personalization和预测内容时添加到网站的JavaScript的描述。

### 核心/依赖于JavaScript

| 名称 | 描述 | 控件 |
|---------------------------|-------------|--------------------------------------------------------|
| rtp.js | - | 由Marketo控制 |
| jquery.min.js | v1.8.3 | 可以通过联系Marketo客户支持来禁用 |
| jquery-custom-ui-min.js | v1.9.2 | 可以通过联系Marketo客户支持来禁用 |
| query-ui-1.8.17-dialog.js | v1.9.2* | 可以通过联系Marketo客户支持来禁用 |

*仅在jQuery UI缺少对话框时使用

### On Demand JavaScript

| 名称 | 描述 | 控件 |
|-------------------------|-----------------------------------------------------------------------|-----------------------|
| ga集成–2.0.1.js | 在启用Google Analytics/Facebook/SiteCatalyst集成时使用 | 由Marketo控制 |
| insightera-bar-2.1.js | 在启用了预测内容推荐栏时使用 | 由Marketo控制 |
| froogaloop2.min.js | 在启用了内容跟踪并且页面上存在Vimeo播放器时使用 | - |
| iframe-api-v1.js | 在启用了内容跟踪并且页面上存在YouTube播放器时使用 | - |
