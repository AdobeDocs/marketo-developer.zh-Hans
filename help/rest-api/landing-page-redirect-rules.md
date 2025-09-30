---
title: 登陆页面重定向规则
feature: REST API, Landing Pages
description: 使用Marketo Asset REST API创建、查询、更新和删除登陆页面重定向规则，这些规则包含过滤器、分页、主机名选项以及非Marketo目标。
exl-id: f63aa5ef-5872-4401-be75-6fb9b2977734
source-git-commit: 7557b9957c87f63c2646be13842ea450035792be
workflow-type: tm+mt
source-wordcount: '655'
ht-degree: 2%

---

# 登陆页面重定向规则

[登陆页面重定向规则终结点引用](https://developer.adobe.com/marketo-apis/api/asset/#tag/Landing-Page-Redirect-Rules)

Marketo提供了一组REST API，用于对登陆页面重定向URL执行CRUD操作。 这些API遵循资产API的标准界面模式，提供查询、创建、更新和删除选项。

利用登陆页面重定向规则，可将登陆页面URL重定向到其他页面URL。 您可以重定向Marketo登陆页面、非Marketo登陆页面或其组合。 可在[此处](https://experienceleague.adobe.com/docs/marketo/using/home.html?lang=zh-Hans)找到有关重定向登陆页面规则的其他信息。

## 查询

查询登陆页面重定向规则遵循[按ID](#by_id)和[浏览](#browse)的标准查询类型。

### 按Id

[按ID获取登陆页面重定向规则](https://developer.adobe.com/marketo-apis/api/asset/#tag/Landing-Page-Redirect-Rules/operation/getLandingPageRedirectRuleByIdUsingGET)端点采用单个登陆页面规则重定向`id`路径参数并返回单个登陆页面重定向规则记录。

```
GET /rest/asset/v1/redirectRule/{id}.json
```

```json
{
    "success": true,
    "errors": [],
    "requestId": "3d0#1707b2521e4",
    "warnings": [],
    "result": [
        {
            "id": 20,
            "redirectFromUrl": "https://calqeauto.com/DefDelPro1_LandingPage1.html",
            "hostname": "calqeauto.com",
            "redirectFrom": {
                "type": "landingPageId",
                "value": 5483
            },
            "redirectTo": {
                "type": "landingPageId",
                "value": 5559
            },
            "redirectToUrl": "https://calqeauto.com/DefDelPro1_LandingPage2.html",
            "createdAt": "2020-02-25T06:56:44Z+0000",
            "updatedAt": "2020-02-25T06:56:44Z+0000"
        }
    ]
}
```

### 浏览

[获取登陆页面重定向规则](https://developer.adobe.com/marketo-apis/api/asset/#tag/Landing-Page-Redirect-Rules/operation/getLandingPageRedirectRulesUsingGET)端点返回登陆页面重定向规则记录的列表。

可以通过传递多个可选查询参数来筛选结果。

`offset`参数是一个整数，它指定要返回的最大条目数（默认值为20）。 最大值为200。 `maxReturn`参数是一个整数，它指定从何处开始检索条目。 可以与offset一起使用（默认值为0）。

`hostname`参数可用于根据登陆页的主机名进行筛选。

`redirectToLandingPageId`是一个整数，可用于过滤要重定向到的登陆页面的ID。 `redirectToPath`可用于过滤要重定向到的登陆页面的路径。

`earliestUpdatedAt`和`latestUpdatedAt`参数允许您为返回登陆页面重定向规则（在给定范围内已更新或最初创建）设置低日期和高日期时间水印。

```
GET /rest/asset/v1/redirectRules.json&maxReturn=3
```

```json
{
    "success": true,
    "errors": [],
    "requestId": "12213#1707b27efb5",
    "warnings": [],
    "result": [
        {
            "id": 5,
            "redirectFromUrl": "https://www.kirtideep.contact/LandingPage2.html",
            "hostname": "www.kirtideep.contact",
            "redirectFrom": {
                "type": "landingPageId",
                "value": 5406
            },
            "redirectTo": {
                "type": "landingPageId",
                "value": 5404
            },
            "redirectToUrl": "https://www.kirtideep.contact/www.showLogs.com.html",
            "createdAt": "2019-11-14T06:26:29Z+0000",
            "updatedAt": "2019-11-14T06:26:29Z+0000"
        },
        {
            "id": 6,
            "redirectFromUrl": "https://www.kirtideep.contact/www.showLogs.com.html",
            "hostname": "www.kirtideep.contact",
            "redirectFrom": {
                "type": "landingPageId",
                "value": 5404
            },
            "redirectTo": {
                "type": "url",
                "value": "www.contactLogs.com"
            },
            "redirectToUrl": "www.contactLogs.com",
            "createdAt": "2019-11-14T06:27:10Z+0000",
            "updatedAt": "2019-11-14T06:27:10Z+0000"
        },
        {
            "id": 7,
            "redirectFromUrl": "https://www.kirtideep.contact/contact/log/check",
            "hostname": "www.kirtideep.contact",
            "redirectFrom": {
                "type": "path",
                "value": "/contact/log/check"
            },
            "redirectTo": {
                "type": "landingPageId",
                "value": 5404
            },
            "redirectToUrl": "https://www.kirtideep.contact/www.showLogs.com.html",
            "createdAt": "2019-11-14T06:27:49Z+0000",
            "updatedAt": "2019-11-14T06:27:49Z+0000"
        }
    ]
}
```

## 创建

使用application/x-www-form-urlencoded POST执行[创建登陆页面重定向规则](https://developer.adobe.com/marketo-apis/api/asset/#tag/Landing-Page-Redirect-Rules/operation/createLandingPageRedirectRuleUsingPOST)端点，该POST具有以下三个必需参数。

`hostname`参数指定登陆页的主机名。 这应该属于品牌策略域或别名。 最大长度为255个字符。

`redirectFrom`参数指定源登陆页面。 这是一个JSON对象，其中包含类型/值对，可确定源是Marketo登陆页面，还是非Marketo登陆页面。 `type`属性可以是“landingPageId”或“path”。

| 参数 | 可选/必填 | 类型 | 描述 |
|---|---|---|---|
| &#39;get&#39; | 必需 | 字符串 | 方法操作。 |
| &#39;访客&#39; | 必需 | 字符串 | 方法名称。 |
| callback | 必需 | 函数 | 要为每个返回的营销活动触发的回调函数。 |

`redirectTo`参数指定目标登陆页面。 这是一个JSON对象，其中包含类型/值对，可确定源是Marketo登陆页面，还是非Marketo登陆页面。 `type`属性可以是“landingPageId”或“url”。

| 登陆页面类型 | redirectTo类型 | 示例 |
|---|---|---|
| Marketo | landingPageId | {&quot;type&quot;：&quot;landingPageId&quot;，&quot;value&quot;：&quot;1774&quot;} |
| 非Marketo | url | {&quot;type&quot;：&quot;url&quot;，&quot;value&quot;：&quot;www.contactLogs.com&quot;} |

有关创建登陆页面重定向规则的更多信息，请访问[此处](https://experienceleague.adobe.com/docs/marketo/using/product-docs/demand-generation/landing-pages/landing-page-actions/redirect-a-marketo-landing-page-to-another-page.html)。

```
POST /rest/asset/v1/redirectRules.json
```

```
Content-Type: application/x-www-form-urlencoded
```

```
hostname=calqeauto.com&redirectFrom={"type":"landingPageId", "value":"5483"}&redirectTo={"type":"landingPageId", "value":"5559"}
```

```json
{
    "success": true,
    "errors": [],
    "requestId": "d7c6#1707b223522",
    "warnings": [],
    "result": [
        {
            "id": 20,
            "redirectFromUrl": "https://calqeauto.com/DefDelPro1_LandingPage1.html",
            "hostname": "calqeauto.com",
            "redirectFrom": {
                "type": "landingPageId",
                "value": 5483
            },
            "redirectTo": {
                "type": "landingPageId",
                "value": 5559
            },
            "redirectToUrl": "https://calqeauto.com/DefDelPro1_LandingPage2.html",
            "createdAt": "2020-02-25T06:56:44Z+0000",
            "updatedAt": "2020-02-25T06:56:44Z+0000"
        }
    ]
}
```

## 更新

[更新登陆页面重定向规则](https://developer.adobe.com/marketo-apis/api/asset/#tag/Landing-Page-Redirect-Rules/operation/updateLandingPageRedirectRuleUsingPOST)端点采用单个登陆页面重定向规则`id`路径参数。 此端点使用application/x-www-form-urlencoded POST执行。

与上述创建调用一样，传递了以下一个或多个查询参数以指定要更新的规则属性： `hostname`、`redirectFrom`、`redirectTo`。

更新的登陆页面重定向规则记录将在响应中返回。

```
POST /rest/asset/v1/redirectRule/{id}.json
```

```
Content-Type: application/x-www-form-urlencoded
```

```
redirectTo={"type":"landingPageId", "value":"5561"}
```

```json
{
    "success": true,
    "errors": [],
    "requestId": "57b2#1707b3852d7",
    "warnings": [],
    "result": [
        {
            "id": 20,
            "redirectFromUrl": "https://calqeauto.com/DefDelPro1_LandingPage1.html",
            "hostname": "calqeauto.com",
            "redirectFrom": {
                "type": "landingPageId",
                "value": 5483
            },
            "redirectTo": {
                "type": "landingPageId",
                "value": 5561
            },
            "redirectToUrl": "https://calqeauto.com/DefDelPro1_LandingPage3.html",
            "createdAt": "2020-02-25T06:56:44Z+0000",
            "updatedAt": "2020-02-25T07:20:53Z+0000"
        }
    ]
}
```

## 删除

[按Id](https://developer.adobe.com/marketo-apis/api/asset/#tag/Landing-Page-Redirect-Rules/operation/deleteLandingPageRedirectRuleUsingPOST)端点删除登陆页面重定向规则采用单个登陆页面规则重定向`id`路径参数。

```
POST /rest/asset/v1/redirectRule/{id}/delete.json
```

```json
{
  "success": true,
  "warnings": [],
  "errors": [],
  "requestId": "d505#154d01c8364",
  "result": [
    {
      "id": 2
    }
  ]
}
```

## 浏览登陆页面域

[获取登陆页面域](https://developer.adobe.com/marketo-apis/api/asset/#tag/Landing-Page-Redirect-Rules/operation/getLandingPageDomainsUsingGET)端点返回登陆页面域记录的列表。

有两个可选的查询参数可以传递以筛选结果。

`offset`参数是一个整数，它指定要返回的最大条目数（默认值为20，最大值为200）。

`maxReturn`参数是一个整数，它指定从何处开始检索条目。 可以与`offset`一起使用（默认值为0）。

```
POST /rest/asset/v1/landingPageDomains.json?maxReturn=3
```

```json
{
    "success": true,
    "errors": [],
    "requestId": "6eb8#1707b43d3cb",
    "warnings": [],
    "result": [
        {
            "hostname": "calqeauto.com",
            "type": "domain"
        },
        {
            "hostname": "www.google.com",
            "type": "domain-alias"
        },
        {
            "hostname": "www.kirti.com",
            "type": "domain-alias"
        }
    ]
}
```
