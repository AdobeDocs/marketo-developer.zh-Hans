---
title: 分页令牌
feature: REST API
description: 使用Marketo REST API分页令牌检索活动和潜在客户，其中涵盖基于日期和基于位置的令牌、ISO 8601 sinceDatetime和414错误。
exl-id: 63fbbf03-8daf-4add-85b0-a8546c825e5b
TQID: https://experienceleague.adobe.com/Ut05n-Y-qPJnvcNRs9liwE3NVBMbJlvaGyv-nExRsek
product_v2:
  - id: b27e5950-9033-45ac-9f86-eb22e567f615
feature_v2:
  - id: c5f60233-d5ea-4453-a799-0ad258b4d399
role_v2:
  - id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
source-git-commit: 3e6d310c5aec1a3435424fb122b71d825db5af0e
workflow-type: tm+mt
source-wordcount: 387
ht-degree: 0%

---

# 分页令牌

Marketo提供分页令牌以分页浏览结果或检索相对于特定日期更新的数据。

某些响应会返回长分页令牌字符串，这可能会导致HTTP 414错误。 查看有关处理这些[错误](error-codes.md)的信息。

请参阅[分页令牌API](https://developer.adobe.com/marketo-apis/api/mapi#tag/Activities/operation/getActivitiesPagingTokenUsingGET)文档。

## 令牌类型

Marketo提供两种相关但不同的页面令牌类型：

- 基于日期的令牌可检索在指定的日期时间之后发生的记录。
- 基于位置的令牌遍历结果集中的记录。

## 基于日期

基于日期的分页令牌表示日期时间。 使用它可检索在该日期时间之后发生的活动、数据值更改和删除的潜在客户。

通过调用具有日期时间的[获取分页令牌](https://developer.adobe.com/marketo-apis/api/mapi#tag/Activities/operation/getActivitiesPagingTokenUsingGET)端点生成基于日期的令牌：

```http
GET /rest/v1/activities/pagingtoken.json?sinceDatetime=2014-10-06T13:22:17-08:00
```

```json
{
    "requestId": "1607c#14884f3e74e",
    "success": true,
    "nextPageToken": "GIYDAOBNGEYS2MBWKQYDAORQGA5DAMBOGAYDAKZQGAYDALBQ"
}
```

`sinceDateTime`参数必须使用[ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)标准日期表示法。 要获得最佳结果，请提供带有时区的完整日期时间。

采用以下格式将时区表示为与GMT的时间差：

`yyyy-mm-ddThh:mm:ss+|-hh:mm`

或者，使用大写字母“Z”表示UTC：

`yyyy-mm-ddThh:mm:ssZ`

例如：

`2016-09-15T15:53:00+05:00`

`2016-09-15T10:53:00Z`

由于`sinceDateTime`是查询参数，因此对其值进行URL编码。

将返回的`nextPageToken`字符串传递给[获取潜在客户活动](https://developer.adobe.com/marketo-apis/api/mapi#tag/Activities/operation/getLeadActivitiesUsingGET)、[获取潜在客户更改](https://developer.adobe.com/marketo-apis/api/mapi#tag/Activities/operation/getLeadChangesUsingGET)或[获取已删除的潜在客户](https://developer.adobe.com/marketo-apis/api/mapi#tag/Activities/operation/getDeletedLeadsUsingGET)调用。 调用检索在提供给获取分页令牌API的日期时间之后发生的记录。

```http
GET /rest/v1/activities.json?nextPageToken=GIYDAOBNGEYS2MBWKQYDAORQGA5DAMBOGAYDAKZQGAYDALBQ&activityTypeIds=1&activityTypeIds=12
```

## 基于位置

通过对Lead数据库API的任何批量检索调用，可返回基于位置的分页令牌。 令牌的工作方式与数据库光标类似，允许遍历记录。

例如，按过滤器类型获取潜在客户调用可能会返回大于所请求批次大小的结果集，该批次大小的最大值和默认值通常为300。 如果有更多结果可用，响应会将moreResult字段设置为true并返回`nextPageToken`。

要检索下一页，请再次调用，并从上一个响应传递`nextPageToken`值。 响应将返回结果集中的下一页。
