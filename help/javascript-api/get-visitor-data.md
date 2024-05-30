---
title: "获取访客数据"
description: "获取访客数据"
feature: Javascript
source-git-commit: d335bdd9f939c3e557a557b43fb3f33934e13fef
workflow-type: tm+mt
source-wordcount: '184'
ht-degree: 4%

---


# 获取访客数据

该方法用于实时获取访客身份识别数据。

- 您必须成为Web个性化客户并拥有 [已部署RTP标记](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/web-personalization/rtp-tag-implementation/deploy-the-rtp-javascript) 使用用户上下文API之前，将其放在您的网站上。
- RTP不支持基于帐户的营销指定帐户列表。 ABM列表和代码仅与在RTP中管理的已上传帐户列表（CSV文件）相关。

如果发生错误，响应JSON中将出现一条错误消息。 如果返回了500代码，请与支持人员联系以告知您提出的请求。

| 参数 | 可选/必填 | 类型 | 描述 |
|---|---|---|---|
| `get` | 必需 | 字符串 | 方法操作。 |
| `visitor` | 必需 | 字符串 | 方法名称。 |
| `callback` | 必需 | 函数 | 要为每个返回的营销活动触发的回调函数。 |

## 示例

获取访客识别数据：

```javascript
function callbackFunction() {
    console.log('RTP is awesome!');
}
rtp('get', 'visitor', callbackFunction);
```

具有区段匹配的响应：

下面是一个示例响应，如果访客在获取访客数据API调用之前与实时区段匹配，则会返回此响应。

```json
{
    "status": 200,
    "results": {
        "matchedSegments": [
            {
                "name": "first click",
                "id": 177
            }
        ],
        "abm": [
            {
                "code": 4,
                "name": "abm_saleforce_customers"
            },
            {
                "code": 5,
                "name": "abm_top_customers"
            }
        ],
        "org": "Marketo",
        "location": {
            "country": "United States",
            "city": "San Mateo",
            "state": "CA"
        },
        "industries": [
            "Software & Internet"
        ],
        "isp": false
    }
}
```

无区段匹配的响应：

下面是一个示例响应，当访客在获取访客数据API调用之前不匹配任何实时区段时会返回该响应。

```json
{
    "status": 200,
    "results": {
        "abm": [
            {
                "code": 4,
                "name": "abm_saleforce_customers"
            },
            {
                "code": 5,
                "name": "abm_top_customers"
            }
        ],
        "org": "Marketo",
        "location": {
            "country": "United States",
            "city": "San Mateo",
            "state": "CA"
        },
        "industries": [
            "Software & Internet"
        ],
        "isp": false
    }
}
```
