---
title: 获取访客数据
description: 使用RTP用户上下文API获取实时访客识别，其中包含参数、回调示例以及区段、ABM和位置的示例响应。
feature: Javascript
exl-id: 39a2446d-8a31-461e-bbe6-a7edf24b4d52
TQID: https://experienceleague.adobe.com/B-JMACtMs3aRVsb1eJKaRoQGgVKB6MTbd0KBoZ7Ay6k
product_v2: id: b27e5950-9033-45ac-9f86-eb22e567f615
feature_v2: id: b0bb9048-d951-48d8-8232-45cf248a7e27id: e2290edd-b061-4880-9d79-dee306cf5aa9id: ed6be6bb-75bb-4ea9-9a42-3bcaa65e1bcc
role_v2: id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
topic_v2: id: b5ce8718-c3af-4fdb-a1a9-fca32f83a87cid: e0eb8757-182f-49f3-94a4-1587d16f5094
source-git-commit: 3e6d310c5aec1a3435424fb122b71d825db5af0e
workflow-type: tm+mt
source-wordcount: 214
ht-degree: 4%

---

# 获取访客数据

使用此方法可实时获取访客识别数据。

- 您必须是Web Personalization客户并在您的网站上部署[RTP标记](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/web-personalization/rtp-tag-implementation/deploy-the-rtp-javascript)，然后才能使用用户上下文API。
- RTP不支持基于帐户的营销指定帐户列表。 ABM列表和代码仅与在RTP中管理的已上传帐户列表（CSV文件）相关。

如果发生错误，响应JSON将包含错误消息。 如果API返回500代码，请联系支持并提供导致错误的请求。

| 参数 | 可选/必填 | 类型 | 描述 |
| --- | --- | --- | --- |
| `get` | 必需 | 字符串 | 方法操作。 |
| `visitor` | 必需 | 字符串 | 方法名称。 |
| `callback` | 必需 | 函数 | 要为每个返回的营销活动触发的回调函数。 |

## 示例

以下示例获取访客识别数据。

```javascript
function callbackFunction() {
    console.log('RTP is awesome!');
}
rtp('get', 'visitor', callbackFunction);
```

具有区段匹配的响应：

以下响应包含`matchedSegments`，因为访客在获取访客数据API调用之前匹配了实时区段。

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

以下响应不包含`matchedSegments`，因为访客在获取访客数据API调用之前不匹配任何实时区段。

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
