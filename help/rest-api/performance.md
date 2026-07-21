---
title: 性能
feature: REST API
description: 通过HTTP压缩提高Marketo REST API性能。 启用gzip以压缩带宽；不支持批量API，并且不会压缩1024个字节以下的API。
exl-id: 173a398a-9d36-4e8d-9dd3-7d0d375b085a
TQID: https://experienceleague.adobe.com/foJCTd890HZtL-UzWx2cjRXwTxqgW56A79sB7FPEWis
product_v2:
  - id: b27e5950-9033-45ac-9f86-eb22e567f615
role_v2:
  - id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
source-git-commit: 3e6d310c5aec1a3435424fb122b71d825db5af0e
workflow-type: tm+mt
source-wordcount: 131
ht-degree: 1%

---

# 性能

使用此页上的性能选项可以提高集成的效率。

## HTTP压缩

Marketo REST API支持HTTP 1.1规范定义的HTTP响应主体压缩。 启用压缩以减少带宽使用和数据检索时间。

>[!NOTE]
>
>小于1024字节的有效负载不压缩，并且批量API不支持压缩。

要启用压缩，请在请求中包含以下HTTP标头：

```html
Accept-Encoding: gzip
```

Marketo REST API压缩响应正文并包含以下标头：

```html
Content-Encoding: gzip
```

以下cURL示例调用[按筛选器类型获取潜在客户](https://developer.adobe.com/marketo-apis/api/mapi#tag/Leads/operation/getLeadsByFilterUsingGET)端点以检索五个潜在客户：

```bash
curl -H 'Accept-Encoding: gzip' 'https://123-ABC-456.mktorest.com/rest/v1/leads.json?filterType=id&filterValues=4,5,7,12,13'
```
