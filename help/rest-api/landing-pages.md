---
title: 登陆页面
feature: REST API, Landing Pages
description: 使用Marketo REST API查询元数据和内容，创建、更新、批准、删除和克隆登陆页面，包括引导式和自由格式类型。
exl-id: 2f986fb0-0a6b-469f-b199-1c526cd5a882
TQID: https://experienceleague.adobe.com/NssOtB6BEMGOQzzauLI7AszLpN3fVcEeJcr9VNTkpJE
product_v2: id: b27e5950-9033-45ac-9f86-eb22e567f615
feature_v2: id: b0bb9048-d951-48d8-8232-45cf248a7e27id: f82558ea-6af5-44eb-a424-5b3389abb0a3
role_v2: id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
topic_v2: id: a004cc84-67b9-4a33-a3a7-8ec7273ef4dc
source-git-commit: 3e6d310c5aec1a3435424fb122b71d825db5af0e
workflow-type: tm+mt
source-wordcount: 917
ht-degree: 2%

---

# 登陆页面

[登陆页面端点引用](https://developer.adobe.com/marketo-apis/api/asset#tag/Landing-Pages)

登陆页面是Marketo托管的网页。 使用登陆页面REST API查询和管理其元数据、内容、生命周期和预览。

## 查询

按名称](https://developer.adobe.com/marketo-apis/api/asset#tag/Landing-Pages/operation/getLandingPageByNameUsingGET)、[按ID](https://developer.adobe.com/marketo-apis/api/asset#tag/Landing-Pages/operation/getLandingPageByIdUsingGET)或[浏览](https://developer.adobe.com/marketo-apis/api/asset#tag/Landing-Pages/operation/browseLandingPagesUsingGET)查询登陆页面[。 这些查询仅返回元数据。 按页面ID单独查询登陆页面的内容部分。

查询登陆页面内容将返回其可用的内容部分。 必须先在此列表中显示一个节，然后才能更新它。

```http
GET /rest/asset/v1/landingPage/{id}/content.json
```

```json
{
    "success": true,
    "warnings": [],
    "errors": [],
    "requestId": "6307#154ea1689d7",
    "result": [
        {
            "id": "67",
            "type": "Form",
            "index": 1,
            "content": {
                "content": "189",
                "contentType": "Form",
                "contentUrl": "https://app-devlocal1.marketo.com/#FO189A1ZN13LA1"
            },
            "formattingOptions": {
                "zIndex": 15,
                "left": "359px",
                "top": "122px"
            }
        }
    ]
}
```

引导式登陆页面包含由其模板定义的部分。 自由表单页面不包含预定义部分，因此在编辑之前请添加其内容。

`content`属性的格式取决于`type`属性以及该字段是静态字段还是动态字段。

## 创建和更新

[从模板创建登陆页面](https://developer.adobe.com/marketo-apis/api/asset#tag/Landing-Pages/operation/createLandingPageUsingPOST)。 需要页面名称、模板ID和目标文件夹。 请参阅可选元数据的端点引用。

[登陆页面内容](https://developer.adobe.com/marketo-apis/api/asset#tag/Landing-Page-Content)端点支持这些内容类型： `richText`、`HTML`、`Form`、`Image`、`Rectangle`和`Snippet`。

```http
POST rest/asset/v1/landingPages.json
```

```text
Content-Type: application/x-www-form-urlencoded
```

```text
name=createLandingPage&folder={"type": "Folder", "id": 11}&template=1&description=this is a test&workspace=default&title=test create&keywords=awesome&formPrefill=false
```

```json
{
    "success": true,
    "warnings": [],
    "errors": [],
    "requestId": "7a39#154cf7922c6",
    "result": [
        {
            "id": 27,
            "name": "createLandingPage",
            "description": "this is a test",
            "createdAt": "2016-05-20T18:41:43Z+0000",
            "updatedAt": "2016-05-20T18:41:43Z+0000",
            "folder": {
                "type": "Folder",
                "value": 11,
                "folderName": "Landing Pages"
            },
            "workspace": "Default",
            "status": "draft",
            "template": 1,
            "title": "test create",
            "keywords": "awesome",
            "robots": "index, nofollow",
            "formPrefill": false,
            "mobileEnabled": false,
            "URL": "https://app-devlocal1.marketo.com/lp/622-LME-718/createLandingPage.html",
            "computedUrl": "https://app-devlocal1.marketo.com/#LP27B2"
        }
    ]
}
```

可以使用[更新登陆页面元数据终结点](https://developer.adobe.com/marketo-apis/api/asset#tag/Landing-Pages/operation/updateLandingPageUsingPOST)来更新登陆页面元数据。

## 审批

登陆页面使用标准草稿和已批准的模型。 更新适用于草稿，并在获得批准后才生效。

## 删除

在删除登陆页面之前，请确保该页面未获得批准，并且没有其他Marketo资源引用它。 使用[删除登陆页面](https://developer.adobe.com/marketo-apis/api/asset#tag/Landing-Pages/operation/deleteLandingPageByIdUsingPOST)端点单独删除页面。 无法使用此API删除嵌入了社交按钮的页面。

## 克隆

克隆具有`application/x-www-url-formencoded` POST请求的登陆页面。

`id`路径参数指定源登陆页面。

`name`参数指定新的登陆页面名称。

`folder`参数指定父文件夹。 将其作为包含`id`和`type`的嵌入式JSON对象传递。

`template`参数指定源登陆页面模板ID。

可选的`description`参数描述了新登陆页面。

```http
POST /rest/asset/v1/landingPage/{id}/clone.json
```

```text
Content-Type: application/x-www-form-urlencoded
```

```text
name=MyNewLandingPage&folder={"type":"Program","id":1119}&template=57
```

```json
{
    "success": true,
    "errors": [],
    "requestId": "1078d#1683e4881c6",
    "warnings": [],
    "result": [
        {
            "id": 3291,
            "name": "MyNewLandingPage",
            "createdAt": "2019-01-11T18:59:25Z+0000",
            "updatedAt": "2019-01-11T18:59:25Z+0000",
            "folder": {
                "type": "Program",
                "value": 1119,
                "folderName": "DefaultProgramWithGuidedLP"
            },
            "workspace": "Default",
            "status": "draft",
            "template": 57,
            "robots": "index, nofollow",
            "formPrefill": false,
            "mobileEnabled": false,
            "URL": "http://na-abm.marketo.com/lp/284-RPR-133/DefaultProgramWithGuidedLPPerkutoTestLP-Clone-1.html",
            "computedUrl": "https://app-abm.marketo.com/#LP3291A1LA1"
        }
    ]
}
```

## 管理内容部分

内容部分按其`index`属性排序，并根据客户端的CSS规则显示。 使用[添加](https://developer.adobe.com/marketo-apis/api/asset#tag/Landing-Page-Content/operation/addLandingPageContentUsingPOST)、[更新](https://developer.adobe.com/marketo-apis/api/asset#tag/Landing-Page-Content/operation/updateLandingPageContentUsingPOST)和[删除](https://developer.adobe.com/marketo-apis/api/asset#tag/Landing-Page-Content/operation/removeLandingPageContentUsingPOST)端点管理节。 使用[获取登陆页面内容](https://developer.adobe.com/marketo-apis/api/asset#tag/Landing-Page-Content/operation/getLandingPageContentUsingGET)来查询这些内容。

每个部分都有`type`和`value`参数。 `type`确定预期的`value`。 将数据作为POST `x-www-form-urlencoded`而不是作为JSON传递给这些端点。

**节类型**

| 类型 | 值 |
| --- | --- |
| 动态内容 | 分段的ID。 |
| 表单 | 表单的ID。 |
| HTML | 文本HTML内容。 |
| 图像 | 图像资源的ID。 |
| 矩形 | 空。 |
| 富文本 | 文本HTML内容。  只能包含富文本元素。 |
| 代码段 | 代码片段的id。 |
| 社交按钮 | 社交按钮的id。 |
| 视频 | 视频的id。 |

对于自由格式页面，添加每个所需的内容部分。 Marketo将它们嵌入到ID为`mktoContent`的`div`元素中。

引导式页面可以包含由[获取登陆页面内容](https://developer.adobe.com/marketo-apis/api/asset#tag/Landing-Page-Content/operation/getLandingPageContentUsingGET)返回的预定义元素。 使用相应的端点添加元素或[更新其内容](https://developer.adobe.com/marketo-apis/api/asset#tag/Landing-Page-Content/operation/updateLandingPageContentUsingPOST)。

### 动态内容

要使某个部分成为动态部分，请首先确保它显示在登陆页面的内容列表中。 然后使用[更新登陆页面内容部分](https://developer.adobe.com/marketo-apis/api/asset#tag/Landing-Page-Content/operation/updateLandingPageContentUsingPOST)将其类型设置为`DynamicContent`。

Marketo会创建基础动态部分，这些部分继承已转换元素的基本类型和内容。

```http
GET /rest/asset/v1/landingPage/{id}/dynamicContent/RVMtNDg=.json
```

```json
{
  "success": true,
  "warnings": [],
  "errors": [],
  "requestId": "46e#1560fa169d9",
  "result": [
    {
      "createdAt": "2016-07-21",
      "updatedAt": "2016-07-21",
      "segmentation": 1007,
      "segments": [
        {
          "segmentId": 1018,
          "segmentName": "Default",
          "type": "RichText",
          "content": "\n\t\t\t\t\t\t\tAlice was beginning to get very tired of sitting by her sister on the bank, and having nothing to do: once or twice she had peeped into the book her sister was reading, but it had no pictures or conversations in it.\n\t\t\t\t\t\t"
        },
        {
          "segmentId": 1017,
          "segmentName": "New Segment",
          "type": "RichText",
          "content": "\n\t\t\t\t\t\t\tAlice was beginning to get very tired of sitting by her sister on the bank, and having nothing to do: once or twice she had peeped into the book her sister was reading, but it had no pictures or conversations in it.\n\t\t\t\t\t\t"
        }
      ]
    }
  ]
}
```

[根据区段ID为每个区段更新内容](https://developer.adobe.com/marketo-apis/api/asset#tag/Landing-Page-Content/operation/updateLandingPageDynamicContentUsingPOST)。

```http
POST /rest/asset/v1/landingPage/{id}/dynamicContent/{dynamicContentId}.json
```

```text
Content-Type: application/x-www-form-urlencoded
```

```text
segment=New Segment&value=New Content
```

```json
 {
  "success": true,
  "warnings": [],
  "errors": [],
  "requestId": "7516#14e08fe7cbbc",
  "result": [
    {
      "id": 1012
    }
  ]
}
```

## 变量

引导式登陆页面支持包含元素值的可编辑变量。 在登陆页面编辑器中修改变量：

![登陆页面变量](assets/landing-page-variables.png)

变量是引导式登陆页面模板的`<head>`元素中的元标记。 支持的类型包括String、Color和Boolean。 以下示例为每个类型定义一个变量：

```html
<head>
  <meta charset="utf-8">
  <meta class="mktoString" mktoName="My String Variable" id="stringVar" default="Hello World!">
  <meta class="mktoColor" mktoName="My Color Variable" id="colorVar" default="#ffffff">
  <meta class="mktoBoolean" mktoName="My Boolean Variable" id="boolVar" default="true">
</head>
```

有关详细信息，请参阅[创建引导式登陆页面模板](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/demand-generation/landing-pages/landing-page-templates/create-a-guided-landing-page-template)文档中的“可编辑变量”部分。

### 查询

通过将登陆页面ID传递到获取登陆页面变量端点，为引导式登陆页面检索变量。

```http
GET /rest/asset/v1/landingPage/{id}/variables.json
```

```json
{
    "success": true,
    "warnings": [],
    "errors": [],
    "requestId": "10843#15a6d7e5fa1",
    "result": [
        {
            "id": "stringVar",
            "value": "Hello World!",
            "type": "string"
        },
        {
            "id": "colorVar",
            "value": "#FFFFFF",
            "type": "color"
        },
        {
            "id": "boolVar",
            "value": "true",
            "type": "boolean"
        }
    ]
}
```

此引导式登陆页面包含三个变量： `stringVar`、`colorVar`和`boolVar`。

### 更新

通过将登陆页面ID、变量ID和变量值传递到“更新登陆页面变量”端点，更新引导式登陆页面的变量。

```http
POST /rest/asset/v1/landingPage/{id}/variable/{variableId}.json?value={newValue}
```

```json
{
    "success": true,
    "warnings": [],
    "errors": [],
    "requestId": "2b07#15a6db77da3",
    "result": [
        {
            "id": "stringVar",
            "value": "Hello Brave New World!",
            "type": "String"
        }
    ]
}
```

## 预览登陆页面

使用[获取登陆页面完整内容](https://developer.adobe.com/marketo-apis/api/asset#tag/Landing-Pages/operation/getLandingPageFullContentUsingGET)检索浏览器渲染的预览。 登陆页面`id`路径参数是必需的。 端点还接受两个可选的查询参数：

- `segmentation`：包含`segmentationId`和`segmentId`的JSON对象数组。 预览表示与这些区段匹配的潜在客户。
- `leadId`：整数潜在客户ID。 预览表示指定的潜在客户。

```http
GET /rest/asset/v1/landingPage/{id}/fullContent.json?leadId=1001&segmentation=[{"segmentationId":1030,"segmentId":1103}]
```

```json
{
  "success": true,
  "errors": [],
  "requestId": "119ab#17692849f1e",
  "warnings": [],
  "result": [
    {
      "id": 1023,
      "content": "<!DOCTYPE html>\n<html>\n <head>\n <meta charset=\"utf-8\">\n \n \n <meta name=\"robots\" content=\"index, nofollow\">\n <title></title>\n <style>\n body {background:#FFFFFF} \n #myConditionalDisplayArea {\n display: true;\n }\n </style>\n <link rel=\"shortcut icon\" href=\"/favicon.ico\" type=\"image/x-icon\" >\n<link rel=\"icon\" href=\"/favicon.ico\" type=\"image/x-icon\" >\n\n\n<style>.mktoGen.mktoImg {display:inline-block; line-height:0;}</style>\n </head>\n <body id=\"bodyId\">\n \n Hello Brave New World!\n <div class=\"mktoText\" id=\"exampleText\"><div>This is an example editable text area.</div>\n<div>Lead Full Name = Hanna Crawford</div>\n<div><br /></div>\n <script type=\"text/javascript\" src=\"//munchkin.marketo.net//munchkin.js\"></script><script>Munchkin.init('123-ABC-456', {customName: 'Test-Landing-Page-APIs_Guided-Landing-Page---deverly', PURL_VISIT_TOKEN, wsInfo: 'j1RR'});</script>\n<div id=\"mktoClickBlockingDiv\"></div>\n </body>\n</html>\n"
    }
  ]
}
```
