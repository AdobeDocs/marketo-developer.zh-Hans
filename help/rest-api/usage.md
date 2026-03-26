---
title: 使用量
feature: REST API
description: 使用每日和最近7天的统计信息端点监测Marketo REST API的使用情况和错误，包括每用户计数和错误代码总数。
source-git-commit: 73fa4c85ecabd4cfd24bc6591aad11dc4e75010a
workflow-type: tm+mt
source-wordcount: '393'
ht-degree: 8%

---

# 使用情况

[使用终结点引用](https://developer.adobe.com/marketo-apis/api/mapi#tag/Usage)

使用API为您的订阅提供REST API使用情况和错误活动的摘要。 这些端点有助于监控集成、跟踪每日呼叫量和确定一段时间内的错误趋势。

使用情况数据包括API调用总计数和每个用户的细分。 错误数据包括错误总数和按错误代码细分。

使用API使用与其他Marketo REST API相同的身份验证方法。 在`Authorization: Bearer {accessToken}`标头中传递访问令牌。

## 端点

| 方法 | 路径 | 描述 |
| --- | --- | --- |
| GET | `/rest/v1/stats/usage.json` | 检索当天的API使用情况。 |
| GET | `/rest/v1/stats/usage/last7days.json` | 检索过去7天的API使用情况。 |
| GET | `/rest/v1/stats/errors.json` | 检索当天的API错误。 |
| GET | `/rest/v1/stats/errors/last7days.json` | 检索过去7天的API错误。 |

## 每日使用情况

检索当天的API使用情况。

```
GET /rest/v1/stats/usage.json
```

```json
{
   "requestId": "5f7f#17d7d8f2b6f",
   "success": true,
   "result": [
      {
         "date": "2015-10-17",
         "total": 1120,
         "users": [
            {
               "userId": "some.body@yahoo.com",
               "count": 200
            },
            {
               "userId": "some.body@marketo.com",
               "count": 200
            },
            {
               "userId": "some.body@gmail.com",
               "count": 720
            }
         ]
      }
   ]
}
```

`result`数组中的每个对象都包含一天的使用总计和每个用户的细分。

## 过去7天的使用情况

检索过去7天的API使用情况。 `result`数组中的每个元素表示一天。

```
GET /rest/v1/stats/usage/last7days.json
```

## 每日错误

检索当天的API错误。

```
GET /rest/v1/stats/errors.json
```

```json
{
   "requestId": "5f7f#17d7d8f2b6f",
   "success": true,
   "result": [
      {
         "date": "2015-10-17",
         "total": 73,
         "errors": [
            {
               "errorCode": "604",
               "count": 1
            },
            {
               "errorCode": "609",
               "count": 56
            },
            {
               "errorCode": "610",
               "count": 16
            }
         ]
      }
   ]
}
```

`result`数组中的每个对象都包含一天错误总计和按错误代码划分的明细。

## 过去7天的错误

检索过去7天的API错误。 `result`数组中的每个元素表示一天。

```
GET /rest/v1/stats/errors/last7days.json
```

## 响应成员

### 使用结果对象

| 名称 | 数据类型 | 描述 |
| --- | --- | --- |
| `date` | 字符串 | 使用`YYYY-MM-DD`格式的使用摘要日期。 |
| `total` | 整数 | 当天的API调用总数。 |
| `users` | 数组 | 当天的每个用户使用计数的列表。 |

### 使用情况用户对象

| 名称 | 数据类型 | 描述 |
| --- | --- | --- |
| `userId` | 字符串 | API用户标识符。 |
| `count` | 整数 | 该用户在当天进行的API调用数。 |

### 错误结果对象

| 名称 | 数据类型 | 描述 |
| --- | --- | --- |
| `date` | 字符串 | 错误摘要的日期，格式为`YYYY-MM-DD`。 |
| `total` | 整数 | 当天的API错误总数。 |
| `errors` | 数组 | 当天的每个错误代码计数列表。 |

### 错误对象

| 名称 | 数据类型 | 描述 |
| --- | --- | --- |
| `errorCode` | 字符串 | Marketo错误代码。 |
| `count` | 整数 | 一天内发生该错误的次数。 |

## 注释

在使用情况响应中会单独报告每个API用户。 通过跨单独的API用户拆分集成，可以更轻松地识别哪个服务占用配额并产生错误。
