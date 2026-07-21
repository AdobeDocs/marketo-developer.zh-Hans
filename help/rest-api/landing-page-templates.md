---
title: 登陆页面模板
feature: REST API, Landing Pages
description: 通过REST API端点管理Marketo登陆页面模板，适用于自由格式和引导式类型、按id或名称查询、创建、更新HTML、克隆、Munchkin。
exl-id: f9d1255e-ec13-4b75-96d5-b4cc9457a51b
TQID: https://experienceleague.adobe.com/U9K1MG-q2gIgJMgfM3lt1S4olETt8ln9seOIKZUncBY
product_v2:
  - id: b27e5950-9033-45ac-9f86-eb22e567f615
feature_v2:
  - id: f82558ea-6af5-44eb-a424-5b3389abb0a3
role_v2:
  - id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
topic_v2:
  - id: a004cc84-67b9-4a33-a3a7-8ec7273ef4dc
source-git-commit: 3e6d310c5aec1a3435424fb122b71d825db5af0e
workflow-type: tm+mt
source-wordcount: 519
ht-degree: 2%

---

# 登陆页面模板

[登陆页面模板端点引用](https://developer.adobe.com/marketo-apis/api/asset#tag/Landing-Page-Templates)

登陆页面模板是Marketo登陆页面的父资源。 每个登陆页面都从其父模板派生其初始内容结构。

## 模板类型

Marketo提供自由格式和引导式登陆页面模板。 自由格式模板提供了结构松散的编辑体验。 引导式模板可以在模板级别限制元素类型和位置。

有关详细的比较，请参阅[了解自由表单与引导式登陆页面](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/demand-generation/landing-pages/understanding-landing-pages/understanding-free-form-vs-guided-landing-pages)。

## 查询

按ID[&#128279;](https://developer.adobe.com/marketo-apis/api/asset#tag/Landing-Page-Templates/operation/getLandingPageTemplateByIdUsingGET)、[名称](https://developer.adobe.com/marketo-apis/api/asset#tag/Landing-Page-Templates/operation/getLandingPageTemplateByNameUsingGET)或[浏览](https://developer.adobe.com/marketo-apis/api/asset#tag/Landing-Page-Templates/operation/getLandingPageTemplatesUsingGET)查询登陆页面模板。 这些端点返回模板元数据。 按ID分别检索每个模板的HTML内容。

## 创建和更新

模板将创建为带元数据的空资源。 `name`和`folder`参数是必需的。 `description`、`templateType`和`enableMunchkin`参数是可选的。

`templateType`值可以是`freeform`或`guided`，默认为`freeForm`。 `enableMunchkin`值默认为`false`。 启用后，它会阻止对模板的子登陆页面进行Munchkin跟踪。

```http
POST /rest/asset/v1/landingPageTemplates.json
```

```text
Content-Type: application/x-www-form-urlencoded
```

```text
name=New LPT - PHP&folder={"id":12,"type":"Folder"}
```

```json
{
    "success": true,
    "warnings": [],
    "errors": [],
    "requestId": "11b7#14dfe1e3bcf",
    "result": [
        {
            "id": 286,
            "name": "assetAPITest",
            "description": "test",
            "createdAt": "2015-06-16T20:45:03Z+0000",
            "updatedAt": "2015-06-16T20:45:03Z+0000",
            "url": "https://app-devlocal1.marketo.com/#LT286B2ZN12",
            "folder": {
                "type": "Folder",
                "value": 12,
                "folderName": "Templates"
            },
            "status": "draft",
            "workspace": "Default"
        }
    ]
}
```

使用[更新登陆页面模板内容](https://developer.adobe.com/marketo-apis/api/asset#tag/Landing-Page-Templates/operation/updateLandingPageTemplateContentUsingPOST)端点单独添加模板内容。

### 更新元数据

使用[更新登陆页面模板元数据](https://developer.adobe.com/marketo-apis/api/asset#tag/Landing-Page-Templates/operation/updateLpTemplateUsingPOST)端点更改名称、描述或`enableMunchkin`设置。

### 更新内容

更新模板内容将替换所有现有HTML内容。 在`content`参数中传递替换为`multipart/form-data`。

```http
POST /rest/asset/v1/landingPageTemplate/286/content.json
```

```html
content-type: multipart/form-data; boundary=--------------------------435851813185237176536801
----------------------------435851813185237176536801
Content-Disposition: form-data; name="content"; filename="content.txt"
Content-Type: text/plain

<html>
<head>
</head>
<body>
<div>Placeholder Content</div>
</body>
</html>
----------------------------435851813185237176536801--
```

```json
 {
  "success": true,
  "warnings": [],
  "errors": [],
  "requestId": "7516#14e0dc60bbc",
  "result": [
    {
      "id": 286
    }
  ]
}
```

## 克隆

克隆包含`application/x-www-url-formencoded` POST请求的登陆页面模板。

`id`路径参数指定源登陆页面模板。

`name`参数指定新登陆页面模板的名称。

`folder`参数指定新模板的父文件夹。 将其作为包含`id`和`type`的嵌入式JSON对象传递。

可选的`description`参数描述了新模板。

```http
POST /rest/asset/v1/landingPageTemplate/{id}/clone.json
```

```text
Content-Type: application/x-www-form-urlencoded
```

```text
name=Standard Template Clone&folder={"type": "Folder", "id": 732}
```

```json
{
    "success": true,
    "errors": [],
    "requestId": "dee6#1683e9fd410",
    "warnings": [],
    "result": [
        {
            "id": 61,
            "name": "Standard Template Clone",
            "createdAt": "2019-01-11T20:34:48Z+0000",
            "updatedAt": "2019-01-11T20:34:48Z+0000",
            "url": "https://app-abm.marketo.com/#LT61B2ZN732",
            "folder": {
                "type": "Folder",
                "value": 732,
                "folderName": "Test LP Template Clone"
            },
            "status": "draft",
            "workspace": "Default",
            "templateType": "freeForm",
            "enableMunchkin": true
        }
    ]
}
```

## 审批

登陆页面模板使用标准草稿和已批准的模型。 更新首先应用于草稿，并在模板获得批准后才生效。

在批准之前，模板必须满足其引导式或自由格式表单类型的要求。 请参阅以下资源：

- [自由格式登陆页面模板](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/demand-generation/landing-pages/landing-page-templates/create-a-free-form-landing-page-template)
- [引导式登陆页面模板](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/demand-generation/landing-pages/landing-page-templates/create-a-guided-landing-page-template)
- [引导式模板示例](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/demand-generation/landing-pages/landing-page-templates/guided-landing-page-template-list)

## 删除

要删除模板，请确保该模板未获得批准且没有子登陆页面引用它。 无法使用此API删除嵌入了社交按钮的登陆页面模板。
