---
title: “寻呼令牌”
feature: REST API
description: “查看分页令牌数据。”
source-git-commit: d335bdd9f939c3e557a557b43fb3f33934e13fef
workflow-type: tm+mt
source-wordcount: '345'
ht-degree: 0%

---


# 分页令牌

为了翻阅结果或检索相对于给定数据更新的数据，Marketo提供了翻页令牌。

在某些情况下，可能会返回长分页令牌字符串。 这可能会导致您遇到HTTP 414错误代码。 您可以找到有关如何处理这些事件的更多信息 [错误](error-codes.md).

## 令牌类型

Marketo提供两种相关但不同的页面令牌类型：

- 基于日期
- 基于位置

## 基于日期

第一个是表示日期的分页令牌。 它们用于检索在分页令牌表示的日期之后发生的活动、数据值更改和删除潜在客户。 此类型的分页令牌通过调用 [获取分页令牌](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Activities/operation/getActivitiesPagingTokenUsingGET) 端点，包括日期时间。

```
GET /rest/v1/activities/pagingtoken.json?sinceDatetime=2014-10-06T13:22:17-08:00
```

```json
{
    "requestId": "1607c#14884f3e74e",
    "success": true,
    "nextPageToken": "GIYDAOBNGEYS2MBWKQYDAORQGA5DAMBOGAYDAKZQGAYDALBQ"
}
```

的格式 `sinceDateTime` 参数必须符合 [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) 标准日期表示法。 要获得最佳结果，请使用包含时区的完整日期时间。 可使用以下格式将时区表示为与GMT的时间差：

`yyyy-mm-ddThh:mm:ss+|-hh:mm`

或者使用大写字母“Z”表示UTC，如下所示：

`yyyy-mm-ddThh:mm:ssZ`

示例

`2016-09-15T15:53:00+05:00`

`2016-09-15T10:53:00Z`

因为 `sinceDateTime` 是查询参数，必须为URL编码。

此 `nextPageToken` 字符串随后提供给 [获取潜在客户活动](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Activities/operation/getLeadActivitiesUsingGET)， [获取潜在客户更改](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Activities/operation/getLeadChangesUsingGET)，或 [获取已删除的潜在客户](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Activities/operation/getDeletedLeadsUsingGET) 调用，活动在提供给获取寻呼令牌API的日期时间后检索。

```
GET /rest/v1/activities.json?nextPageToken=GIYDAOBNGEYS2MBWKQYDAORQGA5DAMBOGAYDAKZQGAYDALBQ&activityTypeIds=1&activityTypeIds=12
```

## 基于位置

第二类型的分页令牌可以通过对Lead数据库API的任何批次检索调用返回。 这种分页令牌在概念上类似于数据库游标，后者允许遍历记录。 例如，按过滤器类型获取潜在客户调用可能表示一个大于给定批次大小的集，通常最大和默认值为300。 如果有更多结果，则响应中的moreResult字段为true，并且 `nextPageToken` 会返回。 要检索结果集中的其他记录，请额外调用，包括 `nextPageToken` ，其中包含新调用中来自上一个响应的接收值。 生成的响应将返回结果集中的下一页。
