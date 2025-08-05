---
title: 数据摄取
feature: REST API, Dynamic Content
description: 使用Marketo API使用数据。
exl-id: 1d501916-53ac-42d8-a804-abb4ab01c7e8
source-git-commit: 3649db037a95cfd20ff0a2c3d81a3b40d0095c39
workflow-type: tm+mt
source-wordcount: '965'
ht-degree: 10%

---

# 数据摄取API

数据摄取API是一种高容量、低延迟、高度可用的服务，旨在以最小的延迟有效处理大量与人员和人员相关的数据摄取。

通过提交异步执行的请求来摄取数据。 通过订阅[Marketo可观察性数据流](https://developer.adobe.com/events/docs/guides/using/marketo/marketo-observability-data-stream-setup)中的事件，可以检索请求状态。&#x200B;

界面提供两种对象类型：人员、自定义对象。 记录操作仅限“插入或更新”。

数据摄取API当前为私有测试版。  受邀者需要拥有[Marketo Engage性能层](https://nation.marketo.com/t5/product-documents/marketo-engage-performance-tiers/ta-p/328835)包的权利。

## 身份验证

数据摄取API使用与Marketo REST API相同的OAuth 2.0身份验证方法来生成访问令牌，但必须通过HTTP标头`X-Mkto-User-Token`传递访问令牌。 您不能通过查询参数传递访问令牌。

通过标头访问令牌示例：

`X-Mkto-User-Token: 11606815-aa7a-405a-80a1-f9683efa528b:ab`

## 权限

数据摄取使用与Marketo REST API相同的权限模型，无需任何其他特殊权限即可使用，不过每个端点需要特定权限。

| 终结点 | 权限 |
|-|-|
| 人员 | 读写潜在客户 |
| 自定义对象 | 读写自定义对象 |

## 标头

数据摄取使用以下自定义HTTP标头。

### 请求

| 键 | 值 | 必需 | 描述 |
| - | - | - | - |
| X-Correlation-Id | 任意字符串（最大长度255个字符）。 | 否 | 可用于跟踪系统中的请求。  请参阅Marketo可观察性数据流 |
| X-Request-Source | 任意字符串（最大长度50个字符）。 | 否 | 可用于跟踪通过系统的请求源。  请参阅Marketo可观察性数据流 |

### 响应

| 键 | 值 | 必需 |
| - | - | - |
| X-Request-Id | 唯一请求ID。 | 是 |

## 请求

使用HTTP POST方法将数据发送到服务器。

数据表示以application/json形式包含在请求正文中。

域名是： `mkto-ingestion-api.adobe.io`

路径以`/subscriptions/MunchkinId`开头，其中MunchkinId特定于您的Marketo实例。 您可以在Marketo Engage UI中的&#x200B;**管理员** > **我的帐户** > **支持信息**&#x200B;下找到您的Munchkin ID。  路径的其余部分用于指定感兴趣的资源。

人员示例URL：

`https://mkto-ingestion-api.adobe.io/subscriptions/556-RJS-213/persons`

自定义对象的示例URL：

`https://mkto-ingestion-api.adobe.io/subscriptions/556-RJS-213/customobjects/purchases`

### 响应

所有响应都通过`X-Request-Id`标头返回唯一的请求ID。

通过标头发送的请求ID示例：

`X-Request-Id: WOUBf3fHJNU6sTmJqLL281lOmAEpMZFw`

### 成功

呼叫成功后，将返回202状态。  未返回响应正文。

成功响应示例：

```
HTTP/1.1 202 Accepted
X-Request-Id: e3d92152-0fb1-444a-8f8f-29d5a2338598
Content-Length: 0
Date: Wed, 18 Oct 2023 18:56:49 GMT
```

### 错误

当调用产生错误时，会返回非202状态以及包含其他错误详细信息的响应正文。  响应正文为application/json，并包含一个包含成员error_code和message的对象。

以下是Adobe Developer Gateway中可重复使用的错误代码。

| HTTP状态代码 | error_code | 条消息 |
| - | - | - |
| 401 | 401013 | Oauth令牌无效 |
| 403 | 403010 | 缺少Oauth令牌 |
| 404 | 404040 | 未找到资源 |
| 429 | 429001 | 达到服务使用限制 |

以下是数据摄取API特有的错误代码，由3个区段组成。  前三位是状态(由Adobe Developer Gateway返回)，后跟零“0”，后跟三位。

| HTTP状态代码 | error_code | 条消息 |
| - | - | - |
| 400 | 4000801 | 请求无效 |
| 400 | 4000802 | 数据无效 |
| 403 | 4030801 | 未授权 |
| 429 | 4290801 | 已达到每日配额 |
| 500 | 5000801 | 内部服务器错误 |

## 重试

检测到临时错误时，服务将重试操作三次。  第一次重试在5分钟的等待时段后发生，第二次在30分钟后发生，最后第三次在30分钟后发生。  重试有多种原因，主要是在从属服务超时或暂时不可用时。

## 端点

摄取端点适用于人员和自定义对象。

### 人员

用于更新插入人员记录的端点。

| 方法 | 路径 |
| - | - |
| POST | /subscriptions/{munchkinId}/person |

#### 标头

| 键 | 值 |
| - | - |
| Content-Type | application/json |
| X-Mkto-User-Token | {accessToken} |

#### 请求正文

| 键 | 数据类型 | 必需 | 值 | 默认值 |
| - | - | - | - | - |
| 优先级 | 字符串 | 否 | 请求的优先级：正常或高 | 普通 |
| partitionName | 字符串 | 否 | 人员分区的名称 | 默认 |
| 删除重复字段 | 对象 | 否 | 要消除重复的属性。 允许一个或两个属性名称。 <br/>在AND操作中使用了两个属性。 例如，如果同时指定了`email`和`firstName`，则它们都用于使用AND操作查找人员。 <br/>支持的属性包括： `id`、`email`、`sfdcAccountId`、`sfdcContactId`、`sfdcLeadId` `sfdcLeadOwnerId`、自定义属性（仅限“字符串”和“整数”类型）、`email` |
| 人 | 对象数组 | 是 | 人员的属性名称 — 值对列表 | - |

所需的权限为`Read-Write Lead`。

### 人员示例

#### 请求

`POST /subscriptions/{munchkinId}/persons`

#### 标头

`Content-Type: application/json`
`X-Mkto-User-Token: {accessToken}`

#### 正文

```json
{
   "priority": "high",
   "partitionName": "EMEA",
   "dedupeFields": {
      "field1": "email",
      "field2": "firstName"
   },
   "persons":[
      {
         "email": "brooklyn.parker@karnv.com",
         "firstName": "Brooklyn",
         "lastName": "Parker"
      },
      {
         "email": "johnny.neal@yvu30.com",
         "firstName": "Johnny",
         "lastName": "Neal"
      }
   ]
}
```

#### 响应

`HTTP/1.1 202`
`X-Request-ID: WOUBf3fHJNU6sTmJqLL281lOmAEpMZFw`

### 自定义对象

用于更新插入自定义对象记录的端点

| 方法 | 路径 |
| - | - |
| POST | `/subscriptions/{munchkinId}/customobjects/{customObjectAPIName}` |

#### 标头

| 键 | 值 |
| - | - |
| Content-Type | application/json |
| X-Mkto-User-Token | {accessToken} |

#### 请求正文

| 键 | 数据类型 | 必需 | 值 | 默认值 |
| - |- | - | - | - |
| 优先级 | 字符串 | 否 | 请求的优先级：正常、高 | 普通 |
| 重复数据删除者 | 字符串 | 否 | 要重复数据删除的属性： dedupeFields、marketoGUID | 删除重复字段 |
| customObject | 对象数组 | 是 | 对象的属性名称 — 值对列表。 | - |

所需的权限为`Read-Write Custom Object`。

如果在请求中指定了某人链接字段，且该人员不存在，则会发生多次重试。 如果在重试窗口（65分钟）期间添加该人员，则更新成功。 例如，如果链接字段为Email on Person，而Person不存在，则会发生重试。

### 自定义对象示例

#### 请求

`POST /subscriptions/{munchkinId}/customobjects/{customObjectAPIName}`

#### 标头

`Content-Type: application/json`
`X-Mkto-User-Token: {accessToken}`

#### 正文

```json
{
   "dedupeBy": "dedupeFields",
   "priority": "high",
   "customObjects": [
      {
         "email": "brooklyn.parker@karnv.com",
         "vin": "20UYA31581L000000",
         "make": "BMW",
         "model": "3-Series 330i",
         "year": 2003
      },
      {
         "email": "johnny.neal@yvu30.com",
         "vin": "19UYA31581L000000",
         "make": "BMW",
         "model": "3-Series 325i",
         "year": 1989
      }
   ]
}
```

#### 响应

`HTTP/1.1 202`
`X-Request-ID: WOUBf3fHJNU6sTmJqLL281lOmAEpMZFw`

## 限制

以下是使用护栏的列表：

* 最大请求大小：1 MB
* 每种对象类型每个请求的最大对象数：1,000
* 每个客户端每秒最大请求数ID：5,000
* 每天最大对象数：10,000,000

## 数据摄取API与REST API

以下是数据摄取API与其他Marketo REST API之间的差异列表：

* 这不是完整的CRUD接口，它仅支持upsert
* 若要进行身份验证，必须使用`X-Mkto-User-Token`标头传递访问令牌
* URL域名是`mkto-ingestion-api.adobe.io`
* URL路径以`/subscriptions/MunchkinId`开头
* 没有查询参数
* 如果调用成功，将返回202状态，并且响应正文为空
* 如果调用失败，则返回非202状态，并且响应正文包含`{ "error_code" : "Error Code", "message" : "Message" }`
* 请求ID通过`X-Request-Id`标头返回
