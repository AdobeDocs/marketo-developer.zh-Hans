---
title: "性能"
feature: REST API
description: “有关使用Marketo API的性能提示。”
source-git-commit: 8c1ffb6db05da49e7377b8345eeb30472ad9b78b
workflow-type: tm+mt
source-wordcount: '117'
ht-degree: 0%

---


# 性能

本页包含可用于提高集成性能的性能相关主题的列表。

## HTTP压缩

Marketo REST API支持使用HTTP 1.1规范定义的标准对响应主体进行HTTP压缩。  建议启用压缩，因为它减少了带宽使用量和检索数据所花费的时间。

**注意：**  将不会压缩小于1024字节的有效负载。

要启用压缩，请在请求中包含以下HTTP标头：

```html
Accept-Encoding: gzip
```

Marketo REST API将压缩响应正文并包含此标头：

```html
Content-Encoding: gzip
```

以下是使用Curl调用 [按筛选器类型获取潜在客户](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Leads/operation/getLeadsByFilterUsingGET) 要检索5个潜在客户的端点：

```bash
$ curl -H 'Accept-Encoding: gzip' 'https://123-ABC-456.mktorest.com/rest/v1/leads.json?filterType=id&filterValues=4,5,7,12,13'
```
