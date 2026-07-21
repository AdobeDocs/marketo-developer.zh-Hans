---
title: 登陆页面重定向规则
feature: REST API, Landing Pages
description: 使用Marketo Asset REST API创建、查询、更新和删除登陆页面重定向规则，这些规则包含过滤器、分页、主机名选项以及非Marketo目标。
exl-id: f63aa5ef-5872-4401-be75-6fb9b2977734
TQID: https://experienceleague.adobe.com/2gePbKA3xeoRdnL8mNnObN-GPTX00Ii4-zcM0lBjs-o
product_v2:
  - id: b27e5950-9033-45ac-9f86-eb22e567f615
feature_v2:
  - id: f82558ea-6af5-44eb-a424-5b3389abb0a3
role_v2:
  - id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
source-git-commit: 3e6d310c5aec1a3435424fb122b71d825db5af0e
workflow-type: tm+mt
source-wordcount: 626
ht-degree: 3%

---

# 登陆页面重定向规则

[登陆页面重定向规则端点参考](https://developer.adobe.com/marketo-apis/api/asset#tag/Landing-Page-Redirect-Rules)

使用登陆页面重定向规则REST API查询、创建、更新和删除登陆页面重定向URL。

重定向规则将一个登陆页面URL发送到另一个页面URL。 源和目标可以是Marketo页面或非Marketo页面。 有关相关产品文档，请参阅[Marketo Engage文档](https://experienceleague.adobe.com/docs/marketo/using/home.html)。

## 查询

按ID[&#128279;](#by_id)或[浏览](#browse)查询登陆页面重定向规则。

### 按Id

[按ID](https://developer.adobe.com/marketo-apis/api/asset#tag/Landing-Page-Redirect-Rules/operation/getLandingPageRedirectRuleByIdUsingGET)获取登陆页面重定向规则端点采用一个重定向规则`id`路径参数并返回匹配记录。

```http
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

[获取登陆页面重定向规则](https://developer.adobe.com/marketo-apis/api/asset#tag/Landing-Page-Redirect-Rules/operation/getLandingPageRedirectRulesUsingGET)端点返回登陆页面重定向规则记录。

使用可选的查询参数筛选结果。

`offset`参数是一个整数，它指定要返回的最大条目数（默认值为20）。 最大值为200。 `maxReturn`参数是一个整数，它指定从何处开始检索条目。 可以与offset一起使用（默认值为0）。

`hostname`参数按登陆页主机名进行筛选。

`redirectToLandingPageId`整数按目标登陆页面ID进行筛选。 `redirectToPath`参数按目标登陆页面路径进行筛选。

`earliestUpdatedAt`和`latestUpdatedAt`参数设置低日期时间边界和高日期时间边界。 端点返回在该范围内创建或更新的规则。

```http
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

使用`application/x-www-form-urlencoded` POST请求调用[创建登陆页面重定向规则](https://developer.adobe.com/marketo-apis/api/asset#tag/Landing-Page-Redirect-Rules/operation/createLandingPageRedirectRuleUsingPOST)端点。 请求具有三个必需的参数。

`hostname`参数指定登陆页面主机名。 它必须属于品牌策略域或别名，且不能超过255个字符。

`redirectFrom`参数将源登陆页面指定为具有类型/值对的JSON对象。 对于Marketo登录页面，`type`属性可以是`landingPageId`，对于非Marketo页面可以是`path`。

| 参数 | 可选/必填 | 类型 | 描述 |
| --- | --- | --- | --- |
| &#39;get&#39; | 必需 | 字符串 | 方法操作。 |
| &#39;访客&#39; | 必需 | 字符串 | 方法名称。 |
| callback | 必需 | 函数 | 要为每个返回的营销活动触发的回调函数。 |

`redirectTo`参数将目标指定为具有类型/值对的JSON对象。 对于Marketo登录页面，`type`属性可以是`landingPageId`，对于非Marketo页面可以是`url`。

| 登陆页面类型 | redirectTo类型 | 示例 |
| --- | --- | --- |
| Marketo | landingPageId | {&quot;type&quot;：&quot;landingPageId&quot;，&quot;value&quot;：&quot;1774&quot;} |
| 非Marketo | url | {&quot;type&quot;：&quot;url&quot;，&quot;value&quot;：&quot;www.contactLogs.com&quot;} |

有关详细信息，请参阅[将Marketo登录页面重定向到其他页面](https://experienceleague.adobe.com/docs/marketo/using/product-docs/demand-generation/landing-pages/landing-page-actions/redirect-a-marketo-landing-page-to-another-page.html)。

```http
POST /rest/asset/v1/redirectRules.json
```

```text
Content-Type: application/x-www-form-urlencoded
```

```text
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

[更新登陆页面重定向规则](https://developer.adobe.com/marketo-apis/api/asset#tag/Landing-Page-Redirect-Rules/operation/updateLandingPageRedirectRuleUsingPOST)终结点采用一个重定向规则`id`路径参数。 将更新作为`application/x-www-form-urlencoded` POST请求发送。

传递一个或多个这些参数以选择要更新的属性： `hostname`、`redirectFrom`或`redirectTo`。

响应将返回更新的重定向规则记录。

```http
POST /rest/asset/v1/redirectRule/{id}.json
```

```text
Content-Type: application/x-www-form-urlencoded
```

```text
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

[按ID](https://developer.adobe.com/marketo-apis/api/asset#tag/Landing-Page-Redirect-Rules/operation/deleteLandingPageRedirectRuleUsingPOST)端点删除登陆页面重定向规则采用一个重定向规则`id`路径参数。

```http
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

[获取登陆页面域](https://developer.adobe.com/marketo-apis/api/asset#tag/Landing-Page-Redirect-Rules/operation/getLandingPageDomainsUsingGET)终结点返回登陆页面域记录。

使用两个可选查询参数筛选结果。

`offset`参数是一个整数，它指定要返回的最大条目数（默认值为20，最大值为200）。

`maxReturn`参数是一个整数，它指定从何处开始检索条目。 可以与`offset`一起使用（默认值为0）。

```http
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
