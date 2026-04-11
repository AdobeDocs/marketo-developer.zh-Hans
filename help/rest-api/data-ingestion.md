---
title: 数据摄取
feature: REST API, Dynamic Content
description: 使用Marketo数据摄取API可大容量、低延迟地摄取人员、自定义对象、公司和项目成员。
exl-id: 1d501916-53ac-42d8-a804-abb4ab01c7e8
source-git-commit: e2606d6cb12c572603ff069617de58417e43ca63
workflow-type: tm+mt
source-wordcount: '1789'
ht-degree: 13%

---

# 数据摄取API

数据摄取API是一种高容量、低延迟、高度可用的服务，旨在以最小的延迟有效处理大量与人员和人员相关的数据摄取。

通过提交异步执行的请求来摄取数据。 通过订阅[Marketo可观察性数据流](https://developer.adobe.com/events/docs/guides/using/marketo/marketo-observability-data-stream-setup)中的事件，可以检索请求状态。

界面提供四种对象类型：人员、自定义对象、公司和程序成员。 该记录操作仅限“插入或更新”，但同时支持删除的程序成员除外。

>[!NOTE]
>
>访问数据摄取API需要有权访问[Marketo Engage性能层](https://nation.marketo.com/t5/product-documents/marketo-engage-performance-tiers/ta-p/328835)包。

## 身份验证

数据摄取API使用与Marketo REST API相同的OAuth 2.0身份验证方法来生成访问令牌，但必须通过HTTP标头`X-Mkto-User-Token`传递访问令牌。 您不能通过查询参数传递访问令牌。

通过标头访问令牌示例：

`X-Mkto-User-Token: 11606815-aa7a-405a-80a1-f9683efa528b:ab`

## 权限

数据摄取使用与Marketo REST API相同的权限模型，无需任何其他特殊权限即可使用，不过每个端点需要特定权限。

| 终结点 | 权限 |
| --- | --- |
| 人员 | 读写潜在客户 |
| 自定义对象 | 读写自定义对象 |
| 公司 | 读写公司 |
| 计划成员 | 读写潜在客户 |

## 支持的对象类型

| 对象类型 | 支持的操作 |
| --- | --- |
| 人员 | 更新插入（插入或更新） |
| 自定义对象 | 更新插入（插入或更新） |
| 公司 | 同步(`createOnly`， `updateOnly`， `createOrUpdate`) |
| 计划成员 | 同步（更新插入状态）、删除（从程序中删除） |

## 标头

数据摄取使用以下自定义HTTP标头。

### 请求

| 键 | 值 | 必需 | 描述 |
| --- | --- | --- | --- |
| `X-Correlation-Id` | 任意字符串（最大长度255个字符）。 | 否 | 可用于跟踪系统中的请求。 请参阅Marketo可观察性数据流 |
| `X-Request-Source` | 任意字符串（最大长度50个字符）。 | 否 | 可用于跟踪通过系统的请求源。 请参阅Marketo可观察性数据流 |

### 响应

| 键 | 值 | 必需 |
| --- | --- | --- |
| `X-Request-Id` | 唯一请求ID。 | 是 |

## 请求

使用HTTP POST方法将数据发送到服务器。

数据表示以application/json形式包含在请求正文中。

域名是： `mkto-ingestion-api.adobe.io`

路径以`/subscriptions/MunchkinId`开头，其中MunchkinId特定于您的Marketo实例。 您可以在Marketo Engage UI中的&#x200B;**管理员** > **我的帐户** > **支持信息**&#x200B;下找到您的Munchkin ID。  路径的其余部分用于指定感兴趣的资源。

人员示例URL：

`https://mkto-ingestion-api.adobe.io/subscriptions/556-RJS-213/persons`

自定义对象的示例URL：

`https://mkto-ingestion-api.adobe.io/subscriptions/556-RJS-213/customobjects/purchases`

公司的示例URL：

`https://mkto-ingestion-api.adobe.io/subscriptions/556-RJS-213/companies`

项目群成员的示例URL：

`https://mkto-ingestion-api.adobe.io/subscriptions/556-RJS-213/programmembers`

### 响应

所有响应都通过`X-Request-Id`标头返回唯一的请求ID。

通过标头发送的请求ID示例：

`X-Request-Id: WOUBf3fHJNU6sTmJqLL281lOmAEpMZFw`

### 成功

呼叫成功后，将返回202状态。  未返回响应正文。

成功响应示例：

```http
HTTP/1.1 202 Accepted
X-Request-Id: e3d92152-0fb1-444a-8f8f-29d5a2338598
Content-Length: 0
Date: Wed, 18 Oct 2023 18:56:49 GMT
```

### 错误

当调用产生错误时，会返回非202状态以及包含其他错误详细信息的响应正文。 响应正文为`application/json`，且包含具有成员`error_code`和`message`的单个对象。

以下是Adobe Developer Gateway中可重复使用的错误代码。

| HTTP状态代码 | error_code | 条消息 |
| --- | --- | --- |
| 401 | 401013 | Oauth令牌无效 |
| 403 | 403010 | 缺少Oauth令牌 |
| 404 | 404040 | 未找到资源 |
| 429 | 429001 | 达到服务使用限制 |

以下是数据摄取API特有的错误代码，由3个区段组成。  前三位是状态（由Adobe Developer Gateway返回），后跟零“0”，后跟三位。

| HTTP状态代码 | error_code | 条消息 |
| --- | --- | --- |
| 400 | 4000801 | 请求无效 |
| 400 | 4000802 | 数据无效 |
| 403 | 4030801 | 未授权 |
| 429 | 4290801 | 已达到每日配额 |
| 500 | 5000801 | 内部服务器错误 |

## 重试

检测到临时错误时，服务将重试该操作。 重试有多种原因，主要是在从属服务超时或暂时不可用时。

重试间隔：

* 初始操作和第一次重试：5分钟
* 第1和第2:15分钟
* 第2和第3:20分钟
* 第3和第4:20分钟
* 第4和第5:2小时
* 第5次重试后 — > 3小时

## 端点

引入端点适用于人员、自定义对象、公司和项目群成员。

### 人员

用于更新插入人员记录的端点。

| 方法 | 路径 |
| --- | --- |
| POST | /subscriptions/{munchkinId}/person |

#### 标头

| 键 | 值 |
| --- | --- |
| `Content-Type` | application/json |
| `X-Mkto-User-Token` | {accessToken} |

#### 请求正文

| 键 | 数据类型 | 必需 | 值 | 默认值 |
| --- | --- | --- | --- | --- |
| `priority` | 字符串 | 否 | 请求的优先级：正常或高 | 普通 |
| `partitionName` | 字符串 | 否 | 人员分区的名称 | 默认 |
| `dedupeFields` | 对象 | 否 | 要消除重复的属性。 允许一个或两个属性名称。<br/> 在AND操作中使用两个属性。 例如，如果同时指定了`email`和`firstName`，则它们都用于使用AND操作查找人员。 <br/>支持的属性包括： `id`、`email`、`sfdcAccountId`、`sfdcContactId`、`sfdcLeadId` `sfdcLeadOwnerId`、自定义属性（仅限“字符串”和“整数”类型）、`email` |  |
| `persons` | 对象数组 | 是 | 人员的属性名称 — 值对列表 | - |

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
         "lastName": "Parker",
         "company": "Karnv"
      },
      {
         "email": "johnny.neal@yvu30.com",
         "firstName": "Johnny",
         "lastName": "Neal",
         "company": "Acme Inc"
      }
   ]
}
```

#### 响应

`HTTP/1.1 202`
`X-Request-ID: WOUBf3fHJNU6sTmJqLL281lOmAEpMZFw`

### 自定义对象

用于更新插入自定义对象记录的端点。

| 方法 | 路径 |
| --- | --- |
| POST | `/subscriptions/{munchkinId}/customobjects/{customObjectAPIName}` |

#### 标头

| 键 | 值 |
| --- | --- |
| `Content-Type` | application/json |
| `X-Mkto-User-Token` | {accessToken} |

#### 请求正文

| 键 | 数据类型 | 必需 | 值 | 默认值 |
| --- | --- | --- | --- | --- |
| `priority` | 字符串 | 否 | 请求的优先级：正常、高 | 普通 |
| `dedupeBy` | 字符串 | 否 | 要重复数据删除的属性： dedupeFields、marketoGUID | 删除重复字段 |
| `customObjects` | 对象数组 | 是 | 对象的属性名称 — 值对列表。 | - |

所需的权限为`Read-Write Custom Object`。

如果在请求中指定了某人链接字段，且该人员不存在，则会发生多次重试。 如果在重试窗口（65分钟）期间添加该人员，则更新成功。 例如，如果Person上的链接字段为`email`，而Person不存在，则会发生重试。

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

### 公司

用于同步公司记录的端点。 支持根据外部公司ID或Marketo内部ID执行重复数据删除的创建、更新和更新插入操作。

| 方法 | 路径 |
| --- | --- |
| POST | `/subscriptions/{munchkinId}/companies` |

#### 标头

| 键 | 值 | 必需 |
| --- | --- | --- |
| `Content-Type` | application/json | 是 |
| `X-Mkto-User-Token` | {accessToken} | 是 |
| `X-Correlation-Id` | 任意字符串（最大长度255个字符） | 否 |
| `X-Request-Source` | 任意字符串（最大长度50个字符） | 否 |

#### 请求正文

| 键 | 数据类型 | 必需 | 值 | 默认值 |
| --- | --- | --- | --- | --- |
| `action` | 字符串 | 否 | 同步操作： `createOnly`、`updateOnly`或`createOrUpdate` | `createOrUpdate` |
| `dedupeBy` | 字符串 | 否 | 要取消重复的字段： `dedupeFields`或`idField`（不区分大小写）。 对于`createOnly`和`createOrUpdate`，只允许使用`dedupeFields`。 对于`updateOnly`，允许同时使用两者。 | `dedupeFields` |
| `input` | 对象数组 | 是 | 公司属性名称 — 值对的列表。 接受JSON键`input`或`companies`。 | - |

`input`数组中的每个公司对象都支持以下字段：

| 键 | 数据类型 | 必需 | 描述 |
| --- | --- | --- | --- |
| `externalCompanyId` | 字符串 | 条件 | 外部公司标识符。 当`dedupeBy`为`dedupeFields`时必需。 `dedupeBy`为`idField`时不允许使用。 |
| `id` | 长 | 条件 | Marketo内部公司ID。 当`dedupeBy`为`idField`且`action`为`updateOnly`时必需。 `dedupeBy`为`dedupeFields`时不允许使用。 |
| `company` | 字符串 | 否 | 公司名称。 |
| （任何字段） | “任一” | 否 | 由[描述公司](companies.md)定义的其他标准或自定义公司字段。 字段名称不区分大小写。 |

所需的权限为`Read-Write Company`。

### 公司示例

#### 请求

`POST /subscriptions/{munchkinId}/companies`

#### 标头

`Content-Type: application/json`
`X-Mkto-User-Token: {accessToken}`

#### 正文

```json
{
   "action": "createOrUpdate",
   "dedupeBy": "dedupeFields",
   "input": [
      {
         "externalCompanyId": "ext-company-001",
         "company": "Acme Corporation",
         "industry": "Technology",
         "numberOfEmployees": 5000,
         "annualRevenue": 100000000
      },
      {
         "externalCompanyId": "ext-company-002",
         "company": "Globex Industries",
         "industry": "Manufacturing",
         "numberOfEmployees": 1200
      }
   ]
}
```

#### 响应

`HTTP/1.1 202`
`X-Request-ID: WOUBf3fHJNU6sTmJqLL281lOmAEpMZFw`

### 公司按ID更新示例

```json
{
   "action": "updateOnly",
   "dedupeBy": "idField",
   "input": [
      {
         "id": 12345,
         "company": "Acme Corporation (Renamed)",
         "numberOfEmployees": 5500
      }
   ]
}
```

### 公司验证规则

| 规则 | 详细信息 |
| --- | --- |
| 操作 | 必须为`createOnly`、`updateOnly`、`createOrUpdate`之一。 区分大小写。 |
| 重复数据删除者 | 必须为`dedupeFields`或`idField`（不区分大小写）。 默认为`dedupeFields`。 |
| dedupeBy +操作 | `createOnly`和`createOrUpdate`仅允许`dedupeFields`。 `updateOnly`同时允许`dedupeFields`和`idField`。 |
| 当`dedupeBy=dedupeFields` | 每个公司必须具有`externalCompanyId`。 字段`id`不得存在。 |
| 当`dedupeBy=idField` | 每个公司必须具有`id`。 字段`externalCompanyId`不得存在。 |
| `input` / `companies` | 不得为null或为空。 |
| 每个请求的最大对象数 | 1,000 |

### 项目群成员（同步）

用于同步程序成员状态、向程序添加潜在客户或更新其程序状态的端点。

| 方法 | 路径 |
| --- | --- |
| POST | `/subscriptions/{munchkinId}/programmembers` |

#### 标头

| 键 | 值 | 必需 |
| --- | --- | --- |
| Content-Type | application/json | 是 |
| X-Mkto-User-Token | {accessToken} | 是 |
| X-Correlation-Id | 任意字符串（最大长度255个字符） | 否 |
| X-Request-Source | 任意字符串（最大长度50个字符） | 否 |

#### 请求正文

| 键 | 数据类型 | 必需 | 值 | 默认值 |
| --- | --- | --- | --- | --- |
| 程序 | 对象数组 | 是 | 程序操作列表。 每个选项都指定了要同步的程序、目标状态和潜在客户。 | - |

`programs`数组中的每个对象都包含：

| 键 | 数据类型 | 必需 | 描述 |
| --- | --- | --- | --- |
| programId | 长 | 是 | Marketo项目ID。 必须为正整数。 |
| 状态 | 字符串 | 是 | 要设置的项目群成员状态，例如`"Member"`或`"Influenced"`。 接受JSON键`statusName`或`status`。 该值不能为`"Not in Program"`；请改用删除终结点。 |
| 成员 | 对象数组 | 是 | 要在项目群中添加或更新的潜在客户参考列表。 接受JSON键`input`或`members`。 |

`members`数组中的每个对象都包含：

| 键 | 数据类型 | 必需 | 描述 |
| --- | --- | --- | --- |
| leadId | 长 | 是 | Marketo商机ID。 |
| （任何字段） | “任一” | 否 | 其他自定义项目群成员字段。 字段名称不区分大小写。 |

所需的权限为`Read-Write Lead`。

### 程序成员同步示例

#### 请求

`POST /subscriptions/{munchkinId}/programmembers`

#### 标头

`Content-Type: application/json`
`X-Mkto-User-Token: {accessToken}`

#### 正文

```json
{
   "programs": [
      {
         "programId": 1001,
         "status": "Member",
         "members": [
            {
               "leadId": 10001
            },
            {
               "leadId": 10002
            }
         ]
      },
      {
         "programId": 1002,
         "status": "Influenced",
         "members": [
            {
               "leadId": 10003
            }
         ]
      }
   ]
}
```

#### 响应

`HTTP/1.1 202`
`X-Request-ID: e3d92152-0fb1-444a-8f8f-29d5a2338598`

### 程序成员同步验证规则

| 规则 | 详细信息 |
| --- | --- |
| 程序 | 不得为null或为空。 |
| programId | 必需。 必须为正整数。 |
| 状态 | 必需。 不得为空。 不得为`"Not in Program"`（区分大小写）。 请改用删除端点。 |
| 成员 | 不得为null或为空。 |
| leadId | 输入数组中的每个成员均需要此参数。 |
| 每个请求的最大潜在客户数 | 所有项目总计拥有1,000名成员。 |

### 项目群成员（删除）

用于从程序中删除潜在客户的端点。 这会将潜在客户的成员资格状态设置为`"Not in Program"`，并从该计划中删除该成员。

>[!NOTE]
>
>此端点使用POST而不是DELETE，因为请求需要包含结构化数据的JSON主体。

| 方法 | 路径 |
| --- | --- |
| POST | `/subscriptions/{munchkinId}/programmembers/delete` |

#### 标头

| 键 | 值 | 必需 |
| --- | --- | --- |
| Content-Type | application/json | 是 |
| X-Mkto-User-Token | {accessToken} | 是 |
| X-Correlation-Id | 任意字符串（最大长度255个字符） | 否 |
| X-Request-Source | 任意字符串（最大长度50个字符） | 否 |

#### 请求正文

| 键 | 数据类型 | 必需 | 值 | 默认值 |
| --- | --- | --- | --- | --- |
| 程序 | 对象数组 | 是 | 程序删除操作的列表。 每个都指定了一个项目和要删除的潜在客户。 | - |

`programs`数组中的每个对象都包含：

| 键 | 数据类型 | 必需 | 描述 |
| --- | --- | --- | --- |
| programId | 长 | 是 | Marketo项目ID。 必须为正整数。 |
| 成员 | 对象数组 | 是 | 要从项目群中删除的潜在客户引用列表。 接受JSON键`input`或`members`。 |

`members`数组中的每个对象都包含：

| 键 | 数据类型 | 必需 | 描述 |
| --- | --- | --- | --- |
| leadId | 长 | 是 | Marketo商机ID。 |

所需的权限为`Read-Write Lead`。

### 程序成员删除示例

#### 请求

`POST /subscriptions/{munchkinId}/programmembers/delete`

#### 标头

`Content-Type: application/json`
`X-Mkto-User-Token: {accessToken}`

#### 正文

```json
{
   "programs": [
      {
         "programId": 1001,
         "members": [
            {
               "leadId": 10001
            },
            {
               "leadId": 10002
            }
         ]
      },
      {
         "programId": 1002,
         "members": [
            {
               "leadId": 10003
            }
         ]
      }
   ]
}
```

#### 响应

`HTTP/1.1 202`
`X-Request-ID: a1b2c3d4-e5f6-7890-abcd-ef1234567890`

### 项目群成员删除验证规则

| 规则 | 详细信息 |
| --- | --- |
| 程序 | 不得为null或为空。 |
| programId | 必需。 必须为正整数。 |
| 成员 | 不得为null或为空。 |
| leadId | 输入数组中的每个成员均需要此参数。 |
| 每个请求的最大潜在客户数 | 所有项目总计拥有1,000名成员。 |

## 限制

以下是护栏的更新列表：

* 最大请求大小：1 MB
* 每种对象类型每个请求的最大对象数：1,000
* 每个客户端每秒最大请求数ID：5,000
* 每天最大对象数：10,000,000

这些限制统一适用于人员、自定义对象、公司和程序成员。 对于项目群成员，“每个请求的对象”是单个请求中所有项目的潜在客户引用总数。

## 数据摄取API与REST API

以下是数据摄取API与其他Marketo REST API之间的差异列表：

* 若要进行身份验证，必须使用`X-Mkto-User-Token`标头传递访问令牌
* URL域名是`mkto-ingestion-api.adobe.io`
* URL路径以`/subscriptions/MunchkinId`开头
* 没有查询参数
* 如果调用成功，将返回202状态，并且响应正文为空
* 如果调用失败，则返回非202状态，并且响应正文包含`{ "error_code" : "Error Code", "message" : "Message" }`
* 请求ID通过`X-Request-Id`标头返回
