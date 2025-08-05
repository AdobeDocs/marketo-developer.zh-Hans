---
title: 性能
feature: REST API
description: 有关使用Marketo API的性能提示。
exl-id: 173a398a-9d36-4e8d-9dd3-7d0d375b085a
source-git-commit: 3649db037a95cfd20ff0a2c3d81a3b40d0095c39
workflow-type: tm+mt
source-wordcount: '122'
ht-degree: 0%

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

以下是使用Curl调用[Get Leads by Filter Type](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Leads/operation/getLeadsByFilterUsingGET)端点以检索5个潜在客户的示例：

```bash
curl -H 'Accept-Encoding: gzip' 'https://123-ABC-456.mktorest.com/rest/v1/leads.json?filterType=id&filterValues=4,5,7,12,13'
```
