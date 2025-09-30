---
title: 获取访客数据
description: 使用RTP用户上下文API获取实时访客识别，其中包含参数、回调示例以及区段、ABM和位置的示例响应。
feature: Javascript
exl-id: 39a2446d-8a31-461e-bbe6-a7edf24b4d52
source-git-commit: 7557b9957c87f63c2646be13842ea450035792be
workflow-type: tm+mt
source-wordcount: '203'
ht-degree: 4%

---

# 获取访客数据

该方法用于实时获取访客身份识别数据。

- 您必须成为Web Personalization客户，并在您的网站上部署[RTP标记](https://experienceleague.adobe.com/zh-hans/docs/marketo/using/product-docs/web-personalization/rtp-tag-implementation/deploy-the-rtp-javascript)，然后才能使用用户上下文API。
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
