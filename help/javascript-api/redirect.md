---
title: 重定向
description: 实施RTP重定向API，使用ABM、组织、位置和区段等字段将分段访客发送到目标URL，并提供示例和提示。
feature: Javascript
exl-id: bbf91245-42e5-47ae-a561-e522cc65ff49
TQID: https://experienceleague.adobe.com/frvGjN7DBJ1RJ3QFvWxo1qGiTNFmvyxi3H6FeynJHLU
product_v2:
  - id: b27e5950-9033-45ac-9f86-eb22e567f615
feature_v2:
  - id: e2290edd-b061-4880-9d79-dee306cf5aa9
  - id: ed6be6bb-75bb-4ea9-9a42-3bcaa65e1bcc
role_v2:
  - id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
topic_v2:
  - id: b5ce8718-c3af-4fdb-a1a9-fca32f83a87c
  - id: cdd65e7e-8839-44a2-bc21-0e03623b5dd1
  - id: e0eb8757-182f-49f3-94a4-1587d16f5094
source-git-commit: 3e6d310c5aec1a3435424fb122b71d825db5af0e
workflow-type: tm+mt
source-wordcount: 473
ht-degree: 8%

---

# 重定向

使用RTP重定向API将分段受众发送到目标URL。

- 您必须是Web Personalization客户并在您的网站上部署[RTP标记](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/web-personalization/rtp-tag-implementation/deploy-the-rtp-javascript)，然后才能使用用户上下文API。
- RTP不支持基于帐户的营销指定帐户列表。 ABM列表和代码仅与在RTP中管理的已上传帐户列表（CSV文件）相关。

## 使用情况

`rtp('send' , 'redirect' , 'field_name' , [ 'values_array' , '...' , '...' ] , 'www.redirect_url.com' , true/false )`

| 参数 | 可选/必填 | 类型 | 描述 |
| --- | --- | --- | --- |
| &#39;发送&#39; | 必需 | 字符串 | 方法操作。 |
| &#39;重定向&#39; | 必需 | 字符串 | 方法名称。 |
| field_name | 必需 | 字符串 | 要匹配的字段名称。 示例：`abm.name`（见下文）。 |
| values_array | 必需 | 数组 | 匹配字段的值列表（不区分大小写）。 |
| redirect_url | 必需 | 字符串 | 用于重定向符合条件的访客的目标URL。 |
| redirect_matched_visitors | 可选 | 布尔值 | 如果为true，则将重定向条件匹配的访客。 如果为false，则将重定向不匹配的条件访客。 默认值： true。 |

重定向条件可以使用组织、行业、ABM列表、位置、ISP或匹配的区段。

| 条件 | 数据层次结构 | 示例 |
| --- | --- | --- |
| 匹配区段（仅在首次单击后生效） | matchedSegments.name | rtp( &#39;send&#39;， &#39;redirect&#39; ， &#39;matchedSegments.name&#39; ， [&#39;Fortune 1,000&#39; ， &#39;Enterprise&#39;] ， &#39;<https://www.example.com>&#39;)； |
| 匹配区段（仅在首次单击后生效） | matchedSegments.id | rtp( &#39;send&#39;， &#39;redirect&#39;， &#39;matchedSegments.id&#39;， [106， 107， 190]， &#39;<https://www.example.com>&#39;)； |
| ABM列表 | abm.name | rtp( &#39;send&#39;， &#39;redirect&#39; ， &#39;abm.name&#39; ， [&#39;top_key_accounts&#39;， &#39;active_customers&#39;] ， &#39;<https://www.example.com>&#39;)； |
| ABM列表 | abm.code | rtp( &#39;send&#39;， &#39;redirect&#39;， &#39;abm.code&#39;， [13， 15]， &#39;<https://www.example.com>&#39;)； |
| 组织 | org | rtp( &#39;send&#39;， &#39;redirect&#39;， &#39;org&#39;， [&#39;ebay&#39;]， &#39;<https://www.example.com>&#39;)； |
| 位置 | location.country | rtp（ &#39;send&#39;， &#39;redirect&#39;， &#39;location.country&#39;， [&#39;美国&#39;]， &#39;<https://www.example.com>&#39;）； |
| 位置 | location.state | rtp( &#39;send&#39;， &#39;redirect&#39;， &#39;location.state&#39;， [&#39;ca&#39;]， &#39;<https://www.example.com>&#39;)； |
| 位置 | location.city | rtp( &#39;send&#39;， &#39;redirect&#39;， &#39;location.city&#39;， [&#39;San Mateo&#39;]， &#39;<https://www.example.com>&#39;)； |
| 行业 | 行业 | rtp( &#39;send&#39;， &#39;redirect&#39;， &#39;industries&#39;， [&#39;Education&#39;]， &#39;<https://www.example.com>&#39;)； |
| ISP | isp | rtp( &#39;send&#39;， &#39;redirect&#39; ， isp ， [&#39;False&#39;]， &#39;<https://www.example.com>&#39;)； |

## 注释

- 要减少基于固件（如公司、行业或位置）的重定向的延迟，请在rtp(&#39;send&#39;、&#39;view&#39;)和rtp(&#39;get&#39;、&#39;campaign&#39;)之前插入重定向代码。
- 将重定向代码紧跟在页眉中的rtp标记之后。
- 优化网站加载以提高浏览器端JavaScript重定向的速度。
- 避免自行重定向。 rtp包含阻止循环重定向调用的安全机制。

```html
<!DOCTYPE html>
<html lang="en-US">
<head>
<!-- RTP tag -->
<script type='text/javascript'>

// This tag needs to be replaced with your account tag
(function(c,h,a,f,i){c[a]=c[a]||function(){(c[a].q=c[a].q||[]).push(arguments)};
c[a].a=i;var g=h.createElement("script");g.async=true;g.type="text/javascript";
g.src=f+'?rh='+c.location.hostname+'&aid='+i;var b=h.getElementsByTagName("script")[0];b.parentNode.insertBefore(g,b);
})(window,document,"rtp","//xyz.marketo.com/rtp-api/v1/rtp.js","xyz");

// START REDIRECT EXAMPLE
//   - Using a helper redirect function
//   - Redirect based on named account
rtp('send','redirect','org', ['microsoft'],'http://www.marketo.com');

// Redirect based on named account list (ABM)
rtp('send','redirect','abm.name', {
    // Redirect visitors that match 'first_abm' list to www.marketo.com
    'http://www.marketo.com' : ['first_abm'],
    // Redirect visitors that match 'second_abm' list to blog.marketo.com
    'http://blog.marketo.com' : ['second_abm']
});
// END REDIRECT EXAMPLE
rtp('send','view');
rtp('get','campaign');
</script>
<!-- End of RTP tag -->
```

## 如何重定向跟踪的访客

1. 将参数附加到目标URL，例如&lt;www.marketo.com？rtp=redirect>。
1. 创建名为“由RTP重定向”的区段。
1. 使用“特定页面”参数定位查看包含该参数的页面的访客。

![跟踪重定向的访客](assets/tracking-redirected-vistors.png)

## 如何使用不同的目标URL定义多个条件

重定向调用支持多个调用。 使用多个调用可组合字段并创建包含不同URL和值的条件。

### 使用情况

`rtp('send', 'redirect', field_name, url_values_map);`

| 参数 | 可选/必填 | 类型 | 描述 |
| --- | --- | --- | --- |
| &#39;发送&#39; | 必需 | 字符串 | 方法操作。 |
| &#39;重定向&#39; | 必需 | 字符串 | 方法名称。 |
| field_name | 必需 | 字符串 | 要匹配的字段名称。 示例：`abm.name`（请参阅上文）。 |
| url_values_map | 必需 | 对象 | 在重定向url和值列表之间映射。 示例：{&#39;<https://www.example.com>&#39; ： [&#39;first_abm&#39;， &#39;second_abm&#39;]} |

#### 示例

```javascript
rtp('send','redirect','abm.name', {
    // Redirect visitors that match 'first_abm' list to www.marketo.com
    'http://www.marketo.com' : ['first_abm'],
    // Redirect visitors that match 'second_abm' list to blog.marketo.com
    'http://blog.marketo.com' : ['second_abm']
});
rtp('send','redirect','org', {
    // Redirect visitors from 'Microsoft' to www.marketo.com/enterprise
    'http://www.marketo.com/enterprise' : ['microsoft']
});
```
