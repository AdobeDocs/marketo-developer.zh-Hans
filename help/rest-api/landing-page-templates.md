---
title: 登陆页面模板
feature: REST API, Landing Pages
description: 通过REST API端点管理Marketo登陆页面模板，适用于自由格式和引导式类型、按id或名称查询、创建、更新HTML、克隆、Munchkin。
exl-id: f9d1255e-ec13-4b75-96d5-b4cc9457a51b
source-git-commit: 7557b9957c87f63c2646be13842ea450035792be
workflow-type: tm+mt
source-wordcount: '552'
ht-degree: 1%

---

# 登陆页面模板

[登陆页面模板终结点引用](https://developer.adobe.com/marketo-apis/api/asset/#tag/Landing-Page-Templates)

登陆页面模板是父资源，依赖于各个Marketo登陆页面。 登陆页面从父模板派生其内容的骨架。

## 模板类型

Marketo提供两种类型的登陆页面模板：自由表单和引导式。 自由表单登陆页面模板为从这些模板派生的页面提供松散结构的编辑体验。 引导式模板提供了一个结构化的体验，其中可在模板级别限制元素类型和位置。 有关差异的详细信息，请参阅[此文档](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/demand-generation/landing-pages/understanding-landing-pages/understanding-free-form-vs-guided-landing-pages)。

## 查询

登陆页面模板支持[按ID](https://developer.adobe.com/marketo-apis/api/asset/#tag/Landing-Page-Templates/operation/getLandingPageTemplateByIdUsingGET)、[按名称](https://developer.adobe.com/marketo-apis/api/asset/#tag/Landing-Page-Templates/operation/getLandingPageTemplateByNameUsingGET)和[浏览](https://developer.adobe.com/marketo-apis/api/asset/#tag/Landing-Page-Templates/operation/getLandingPageTemplatesUsingGET)的资产标准查询类型。 这些端点返回模板的元数据。 必须通过模板的ID基于每个模板检索HTML内容。

## 创建和更新

模板将创建为具有关联元数据的空资源。 创建模板时，必须包括名称和文件夹，以及可选描述、templateType和enableMunchkin参数。 templateType可以是自由格式或引导格式，默认为freeForm。 有关这些类型之间的差异，请参阅引导式表单与自由式表单一节。 enableMunchkin默认为false，启用时将阻止在模板的任何子登陆页面上执行Munchkin跟踪。

```
POST /rest/asset/v1/landingPageTemplates.json
```

```
Content-Type: application/x-www-form-urlencoded
```

```
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

必须通过[更新登陆页面模板内容](https://developer.adobe.com/marketo-apis/api/asset/#tag/Landing-Page-Templates/operation/updateLandingPageTemplateContentUsingPOST)端点单独填充模板的内容。

### 更新元数据

可以通过[更新登陆页面模板元数据](https://developer.adobe.com/marketo-apis/api/asset/#tag/Landing-Page-Templates/operation/updateLpTemplateUsingPOST)端点更新登陆页面模板的元数据。 名称、描述和enableMunchkin设置可按此方式更新。

### 更新内容

登陆页面模板中的内容是对整个HTML内容的破坏性更新。 内容必须以multipart/form-data形式传递，唯一参数为content。

```
POST /rest/asset/v1/landingPageTemplate/286/content.json
```

```
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

```
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

Marketo提供了一种克隆登陆页面模板的简单方法。 这是application/x-www-url-formencoded POST请求。

`id`路径参数指定要克隆的源登陆页面模板的ID。

`name`参数用于指定新登陆页面模板的名称。

`folder`参数用于指定新登陆页面模板将驻留的父文件夹。 其形式为嵌入JSON对象，包含  `id`和`type`。

可选的`description`参数用于描述新的登陆页面模板。

```
POST /rest/asset/v1/landingPageTemplate/{id}/clone.json
```

```
Content-Type: application/x-www-form-urlencoded
```

```
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

登陆页面模板遵循标准草稿批准模型，其中可以包含草稿版本和/或批准版本。 每当将更新应用于模板时，都会先将更新应用于草稿版本，并且仅在模板获得批准后才可实时查看。

要批准模板，该模板必须符合其类型的规则，无论是在自由格式的引导下。 有关创建和批准相应类型模板要求的更多信息，请参阅各自的创建文档：

- [自由表单登陆页面模板](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/demand-generation/landing-pages/landing-page-templates/create-a-free-form-landing-page-template)
- [引导式登陆页面模板](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/demand-generation/landing-pages/landing-page-templates/create-a-guided-landing-page-template)
- [引导式模板示例](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/demand-generation/landing-pages/landing-page-templates/guided-landing-page-template-list)

## 删除

要删除模板，该模板必须已停用且未批准，这意味着任何子登陆页面都不能引用该模板。  不能使用此API删除嵌入了社交按钮的登陆页面模板。
