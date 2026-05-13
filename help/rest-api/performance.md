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
source-git-commit: 00118a89f25a23b931fac671130932bb0e0e4e4e
workflow-type: tm+mt
source-wordcount: 146
ht-degree: 1%

---

# 性能

本页包含可用于提高集成性能的性能相关主题的列表。

## HTTP压缩

Marketo REST API支持使用HTTP 1.1规范定义的标准对响应主体进行HTTP压缩。 建议启用压缩，因为它减少了带宽使用量和检索数据所花费的时间。

>[!NOTE]
>
>小于1024字节的有效负载不压缩，并且批量API不支持压缩。

要启用压缩，请在请求中包含以下HTTP标头：

```html
Accept-Encoding: gzip
```

Marketo REST API将压缩响应正文并包含此标头：

```html
Content-Encoding: gzip
```

以下是使用Curl调用[Get Leads by Filter Type](https://developer.adobe.com/marketo-apis/api/mapi#tag/Leads/operation/getLeadsByFilterUsingGET)端点以检索5个潜在客户的示例：

```bash
curl -H 'Accept-Encoding: gzip' 'https://123-ABC-456.mktorest.com/rest/v1/leads.json?filterType=id&filterValues=4,5,7,12,13'
```
