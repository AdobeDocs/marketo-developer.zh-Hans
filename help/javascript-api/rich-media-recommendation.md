---
title: 富媒体推荐
description: 富媒体推荐
feature: Javascript
exl-id: ee92e46d-e529-40a2-a0d0-ee233916f004
source-git-commit: 3649db037a95cfd20ff0a2c3d81a3b40d0095c39
workflow-type: tm+mt
source-wordcount: '812'
ht-degree: 4%

---

# 富媒体推荐

必须在要显示富媒体推荐模板的页面上设置以下标记和API调用。

1. 在页眉中
   1. 已安装RTP标记
   1. 将GET调用添加到页面以填充推荐
   1. 添加SET调用以配置模板
1. 在页面正文中
   1. 将模板标记（div类）放置在要显示该模板的位置

[此处](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/predictive-content/enabling-predictive-content/enable-predictive-content-for-web-rich-media)提供了详细信息。

## 模板标记

| 属性 | 可选/必填 | 描述 |
|---|---|---|
| 类 | 必需 | 指定此div HTML元素为RTP推荐div。 |
| data-rtp-template-id | 必需 | 模板id。 这会确定推荐的对齐方式。 使用“template1”进行水平对齐，“template2”进行垂直对齐，或“template3”进行仅包含标题和描述的垂直对齐。 该脚本将匹配的模板注入此`div.Permissible`值：template1、template2、template3。 |

### 示例

要以水平对齐方式显示推荐，请使用“template1”。

```html
<div class="RTP_RCMD2" data-rtp-template-id="template1"></div>
```

要以垂直对齐方式显示推荐，请使用“template2”。

```html
<div class="RTP_RCMD2" data-rtp-template-id="template2"></div>
```

要以仅与标题和描述垂直对齐的方式显示推荐，请使用“template3”。

```html
<div class="RTP_RCMD2" data-rtp-template-id="template3"></div>
```

在[此处](#example_of_rich_media_recommendation_template_1)查看模板对齐的屏幕截图。

## 填充推荐

此方法使用推荐填充页面上的所有富媒体`<divs>`。

### 使用情况

`rtp('get', 'rcmd', 'richmedia');`

| 参数 | 可选/必填 | 类型 | 描述 |
|---|---|---|---|
| &#39;get&#39; | 必需 | 字符串 | 方法操作。 |
| &#39;rcmd&#39; | 必需 | 字符串 | 方法名称。 |
| &#39;富媒体&#39; | 必需 | 字符串 | 子方法名称。 |

## 更改模板配置

此方法会更改模板的默认配置。

注意：使用此方法时，必须在调用rtp(&#39;get&#39;，&#39;rcmd&#39;， &#39;richmedia&#39;)之前调用它；

### 使用情况

`rtp('set', 'rcmd', 'richmedia', 'template_id', conf_obj);`

| 参数 | 可选/必填 | 类型 | 描述 |
|---|---|---|---|
| &#39;设置&#39; | 必需 | 字符串 | 方法操作。 |
| &#39;rcmd&#39; | 必需 | 字符串 | 方法名称。 |
| &#39;富媒体&#39; | 必需 | 字符串 | 子方法名称。 |
| template_id | 可选 | 字符串 | 用于配置更改的模板ID。 用于仅为一个模板指定设置更改。 |
| conf_obj | 必需 | 对象 | 新配置。 对象将所有配置作为键/值对保留。 |

### 示例

此代码片段会更改模板的标题文本。

```javascript
rtp("set", "rcmd", "richmedia","template1",
    {
        "rcmd.title.text": "RECOMMENDED CONTENT"
    }
);
```

此代码片段显示了如何为一个模板设置具有多个配置的类别。

```javascript
rtp("set", "rcmd", "richmedia",
    {
        "template1":
        {
            "rcmd.title.text": "RECOMMENDED CONTENT",
            "rcmd.general.font.family": "arial",
            "category":
            [
                "webinar",
                "blog posts",
                "pricing_page_category",
                "product_a_category"
            ]
        }
    }
);
```

注意：使用“类别”可筛选在预测内容推荐结果中显示的内容。 要将预测内容应用于所有启用的内容片段，请将“类别”留空。 如果只想为富媒体模板中的输出推荐特定内容，请在设置内容页面中为内容添加类别，并在推荐模板代码中关联该类别。 根据网站区域（产品或解决方案）对相关内容进行分类。

此代码片段显示了如何为一个模板设置多个模板配置。

```javascript
rtp("set", "rcmd", "richmedia",
    {
        "template1":
        {
            "rcmd.title.text": "RECOMMENDED CONTENT",
            "rcmd.general.font.family": "arial"
        }
    }
);
```

#### 配置属性

| 配置 | 示例 | 描述 |
|---|---|---|
| rcmd.general.font.family | &quot;rcmd.general.font.family&quot; ： &quot;arial&quot; | 更改模板中所有文本的字体系列。 此属性支持按浏览器类型列出的所有CSS值。 如果页面上存在自定义字体系列，则可以使用它。 |
| rcmd.content.background.color | &quot;rcmd.content.background.color&quot; ： &quot;black&quot; | 更改模板内部框的背景颜色。 此属性支持按浏览器类型划分的所有CSS值。 |
| rcmd.title.text | &quot;rcmd.title.text&quot; ：&quot;推荐的内容&quot; | 更改模板标题。 |
| rcmd.title.background.color | &quot;rcmd.title.background.color&quot; ： &quot;blue&quot; | 更改标题框的背景颜色。 此属性支持所有css颜色值（颜色名称、rgb、...） |
| rcmd.title.font.size | &quot;rcmd.title.font.size&quot; ： &quot;26px&quot; | 更改标题字体大小。 属性支持所有可能的字体大小CSS值(px、em、...) |
| rcmd.title.font.color | &quot;rcmd.title.font.color&quot; ： &quot;white&quot; | 更改标题字体颜色。 此属性支持所有字体颜色值（rgb、十六进制、...） |
| rcmd.description.font.color | &quot;rcmd.description.font.color&quot; ： &quot;white&quot; | 更改说明字体颜色。 此属性支持所有字体颜色值（rgb、十六进制、...） |
| rcmd.cta.background.color | &quot;rcmd.cta.background.color&quot; ： &quot;green&quot; | 更改按钮的背景颜色。 此属性支持所有css颜色值（颜色名称、rgb、...） |
| rcmd.cta.font.color | “rcmd.cta.font.color” ：“rgb(90， 84， 164)” | 更改按钮字体颜色。 此属性支持所有字体颜色值（rgb、十六进制、...） |
| rcmd.cta.text | &quot;rcmd.cta.text&quot; ：&quot;推送&quot; | 更改按钮文本。 所有按钮的文本都相同。 |
| 类别 | &quot;category&quot; ： [&quot;one category&quot;] | 更改此模板支持的推荐类别。 模板仅显示具有此配置设置的某个类别的推荐。 |

注意：每个模板的配置支持可能会发生更改。

#### 基本示例

此示例有一个包含三个推荐的模板。 将此示例复制到HTML页面，然后将RTP标记替换为您的标记。

```html
<!DOCTYPE>
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
<title>RTP recommendation</title>
<!-- RTP tag -->
<script type='text/javascript'>

// This tag needs to be replaced with your account tag
(function(c,h,a,f,i,e){c[a]=c[a]||function(){(c[a].q=c[a].q||[]).push(arguments)};
c[a].a=i;c[a].e=e;var g=h.createElement("script");g.async=true;g.type="text/javascript";
g.src=f+'?aid='+i;var b=h.getElementsByTagName("script")[0];b.parentNode.insertBefore(g,b);
})(window,document,"rtp","//example.rtp.com/rtp-api/v1/rtp.js","account_id");

// Send page view (required by  the recommendation)
rtp('send','view');
// Populate recommendation
rtp('get','rcmd', 'richmedia');
</script>
<!-- End of RTP tag -->
</head>
<body>
<div class="RTP_RCMD2" data-rtp-template-id="template1"></div>
</body>
</html>
```

#### 高级示例

此示例有一个包含三个推荐的模板。 模板标题为“推荐的内容”，按钮文本将为“了解更多”。 将此示例复制到HTML页面，然后将RTP标记替换为您的标记。

```html
<!DOCTYPE>
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
<title>RTP recommendation</title>
<!-- RTP tag -->
<script type='text/javascript'>

// This tag needs to be replaced with your account tag
(function(c,h,a,f,i,e){c[a]=c[a]||function(){(c[a].q=c[a].q||[]).push(arguments)};
c[a].a=i;c[a].e=e;var g=h.createElement("script");g.async=true;g.type="text/javascript";
g.src=f+'?aid='+i;var b=h.getElementsByTagName("script")[0];b.parentNode.insertBefore(g,b);
})(window,document,"rtp","//example.rtp.com/rtp-api/v1/rtp.js","account_id");

// Send page view (required by  the recommendation)
rtp('send','view');
// Populate the recommendation zone
rtp('get', 'campaign',true);
// Change template configuration
rtp('set', 'rcmd', 'richmedia',
    {
        template1 :
        {
            "rcmd.title.text" : "RECOMMENDED CONTENT",
            "rcmd.cta.text" : "Read More"
        }
    }
);
// Populate recommendation
rtp('get','rcmd', 'richmedia');
</script>
<!-- End of RTP tag -->
</head>
<body>
<div class="RTP_RCMD2" data-rtp-template-id="template1"></div>
</body>
</html>
```

#### 富媒体推荐模板#1示例

**名称**： template1 **描述**：包含图像、标题和描述的水平内容以及call to action按钮。

![富媒体模板](assets/rich-media-template1.png)

#### 富媒体推荐模板#2示例

**名称**： template2 **描述**：垂直内容（包括图像、标题和描述）以及call to action按钮。

![富媒体模板](assets/rich-media-template2.png)

#### 富媒体推荐模板#3示例

**名称**：模板3 **描述**：仅包含标题和描述的垂直内容。 鼠标悬停时，标题会更改颜色并超链接到内容URL。 描述还链接到不发生颜色更改的内容。 ![富媒体模板](assets/rich-media-template3.png)
