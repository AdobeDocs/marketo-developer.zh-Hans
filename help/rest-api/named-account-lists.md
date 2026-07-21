---
title: 指定帐户列表
feature: REST API
description: 了解如何使用REST API管理Marketo指定帐户列表，包括权限、字段、筛选以及用于查询、创建、更新和删除的端点。
exl-id: 98f42780-8329-42fb-9cd8-58e5dbea3809
TQID: https://experienceleague.adobe.com/18lMhheW21Gz1-3TMHwleHhmLTOqJsZSQ5aqkbbchhM
product_v2: id: b27e5950-9033-45ac-9f86-eb22e567f615
feature_v2: id: c5f60233-d5ea-4453-a799-0ad258b4d399
role_v2: id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
source-git-commit: 3e6d310c5aec1a3435424fb122b71d825db5af0e
workflow-type: tm+mt
source-wordcount: 686
ht-degree: 2%

---

# 指定帐户列表

[命名帐户列出终结点引用](https://developer.adobe.com/marketo-apis/api/mapi#tag/Named-Account-Lists)

[指定帐户列表](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/target-account-management/target/account-lists)是Marketo中的指定帐户的集合。 可使用它们进行分类、数据扩充和智能营销活动筛选。

通过指定帐户列表API，可远程管理列表资产及其成员资格。
`Content`

## 权限

所需的权限取决于操作：

- 查询指定帐户列表：只读指定帐户列表或读写指定帐户列表。
- 创建、更新或删除列表：读写指定帐户列表。
- 查询列表成员资格：只读指定帐户或读写指定帐户。
- 管理列表成员资格：读写指定帐户。

## 模型

指定帐户列表具有一组有限的标准字段，不支持自定义字段。
`Named Account List Field`

| 名称 | 数据类型 | 可更新 | 注释 |
| --- | --- | --- | --- |
| marketoGUID | 字符串 | False | 指定帐户列表的唯一字符串标识符。 此字段由系统管理，在创建记录时不允许作为字段。 “dedupeBy”：“idField”执行创建或更新时使用的字段。 |
| name | 字符串 | True | 列表的名称。 执行create或update时“dedupeBy”使用的字段：“dedupeFields”。 |
| createdat | 日期时间 | False | 创建列表的日期时间。 此字段由系统管理，在创建或更新记录时不允许作为字段。 |
| 更新时间 | 日期时间 | False | 列表最近更新的日期时间。 此字段由系统管理，在创建或更新记录时不允许作为字段。 |
| 类型 | 字符串 | False | 列表的类型。 其值可以为“default”或“external”。 外部列表是由CRM帐户视图创建的列表。 |

## 查询

命名帐户列表查询支持两种filterTypes：“dedupeFields”和“idField”。 在`filterType`查询参数中设置该字段，并在`filterValues as`中以逗号分隔的列表中提供值。

`nextPageToken`和`batchSize`筛选器是可选的。

```http
GET /rest/v1/namedAccountLists.json?filterType=idField&filterValues=dff23271-f996-47d7-984f-f2676861b5fb,dff23271-f996-47d7-984f-f2676861b5fc
```

```json
{
   "requestId": "e42b#14272d07d78",
   "success": true,
   "result": [
      {
         "seq": 0,
         "marketoGUID": "dff23271-f996-47d7-984f-f2676861b5fb",
         "name": "Saas List",
         "createdAt": "xxxxxxxx",
         "updatedAt": "xxxxxxxx",
         "type": "default",
         "updateable": true
      },
      {
         "seq": 1,
         "marketoGUID": "dff23271-f996-47d7-984f-f2676861b5fc",
         "name": "My Account List",
         "createdAt": "xxxxxxxx",
         "updatedAt": "xxxxxxxx",
         "type": "default",
         "updateable": true
      }
   ]
}
```

## 创建和更新

使用标准Lead Database模式创建和更新指定帐户列表记录。 命名帐户列表只有一个可更新的字段： `name`。

端点支持两种标准操作类型：“createOnly”和“updateOnly”。 `action defaults`设置为“createOnly”。

当操作为`updateOnly`时，您可以指定可选的`dedupeBy parameter`。 允许的值为“dedupeFields”（对应于“name”）和“idField”（对应于“marketoGUID”）。

在`createOnly`模式中，仅允许“name”作为`dedupeBy`字段。 一次最多可以提交300条记录。

```http
POST /rest/v1/namedAccountLists.json
```

```json
{
   "action": "createOnly",
   "dedupeBy": "dedupeFields",
   "input": [
      {
         "name": "SAAS List"
      },
      {
         "name": "Manufacturing (Domestic)"
      }
   ]
}
```

```json
{
   "requestId": "e42b#14272d07d78",
   "success": true,
   "result": [
      {
         "seq": 0,
         "status": "created",
         "marketoGUID": "dff23271-f996-47d7-984f-f2676861b5fb"
      },
      {
         "seq": 1,
         "status": "created",
         "marketoGUID": "dff23271-f996-47d7-984f-f2676861b5fc"
      }
   ]
}
```

## 删除

使用列表的`name`或`marketoGUID`删除命名帐户列表。 要选择该键，请在请求的`deleteB`成员中为name传递“dedupeFields”，为marketoGUID传递“idField”。

如果未设置，该值默认为dedupeFields。 一次最多可以删除300条记录。

```http
POST /rest/v1/namedAccountLists/delete.json
```

```json
{
   "deleteBy": "dedupeFields",
   "input": [
      {
         "name": "Saas List"
      },
      {
         "name": "B2C List"
      },
      {
         "name": "Launchpoint Partner List"
      }
   ]
}
```

```json
{
   "requestId": "e42b#14272d07d78",
   "success": true,
   "result": [
      {
         "seq": 0,
         "marketoGUID": "dff23271-f996-47d7-984f-f2676861b5fb",
         "status": "deleted"
      },
      {
         "seq": 1,
         "id": "dff23271-f996-47d7-984f-f2676861b5fc",
         "status": "deleted"
      },
      {
         "seq": 2,
         "status": "skipped",
         "reasons": [
            {
               "code": "1013",
               "message": "Record not found"
            }
         ]
      }
   ]
}
```

如果找不到键的记录，则相应的结果项具有`status`个“已跳过”。 它还包含原因以及描述失败的代码和消息。

## 管理成员资格

### 查询成员资格

通过提供帐户列表的`i`查询命名帐户列表成员资格。 可选参数包括：

-`field` — 要包含在响应记录中的以逗号分隔的字段列表
-`nextPageToke` — 对结果集进行分页
-`batchSiz` — 用于指定要返回的记录数

如果未设置`field`，则将返回`marketoGUI`、`nam`、`createdA`和`updatedA`。 `batchSiz`的最大值和默认值为300。

```http
GET /rest/v1/namedAccountList/{id}/namedAccounts.json
```

```json
{
   "requestId": "e42b#14272d07d78",
   "success": true,
   "result": [
      {
         "seq": 0,
         "marketoGUID": "dff23271-f996-47d7-984f-f2676861b5fb",
         "name": "Saas List",
         "createdAt": "2017-02-01T00:00:00Z",
         "updatedAt": "2017-03-05T17:21:15Z"
      },
      {
         "seq": 1,
         "marketoGUID": "dff23271-f996-47d7-984f-f2676861b5fc",
         "name": "My Account List",
         "createdAt": "2017-02-01T00:00:00Z",
         "updatedAt": "2017-03-05T17:21:15Z"
      }
   ]
}
```

### 添加成员

使用命名帐户的marketoGUID将其添加到命名帐户列表。 一次最多可以添加300条记录。

```http
POST /rest/v1/namedAccountList/{id}/namedAccounts.json
```

```json
{
    "input": [
        {
             "marketoGUID": "dff23271-f996-47d7-984f-f2676861b5fb"
        },
        {
             "marketoGUID": "dff23271-f996-47d7-984f-f2676861b5fb"
        }
    ]
}
```

```json
{
    "requestId": "string",
    "result": [
        {
            "seq": 0,
            "marketoGUID": "dff23271-f996-47d7-984f-f2676861b5fb",
            "status": "added"
        },
        {
            "seq": 1,
            "marketoGUID": "dff23271-f996-47d7-984f-f2676861b5fb",
            "status": "added"
        }
    ],
    "success": true,
}
```

### 删除成员

从帐户列表中删除记录使用不同的路径但界面相同。 为要删除的每个记录提供`marketoGUI`。 一次最多可以删除300条记录。

```http
POST /rest/v1/namedAccountList/{id}/namedAccounts/remove.json
```

```json
{
    "input": [
        {
             "marketoGUID": "dff23271-f996-47d7-984f-f2676861b5fb"
        },
        {
             "marketoGUID": "dff23271-f996-47d7-984f-f2676861b5fb"
        }
    ]
}
```

```json
{
    "requestId": "string",
    "result": [
        {
            "seq": 0,
            "marketoGUID": "dff23271-f996-47d7-984f-f2676861b5fb",
            "status": "added"
        },
        {
            "seq": 1,
            "marketoGUID": "dff23271-f996-47d7-984f-f2676861b5fb",
            "status": "added"
        }
    ],
    "success": true
}
```

## 超时

- 除非另有说明，否则命名帐户列表端点的超时为30秒。
- 同步指定帐户列表的超时为60秒。
- 删除指定帐户列表的超时时间为60秒。
- 获取指定帐户列表的超时时间为60秒。
- 添加指定帐户列表成员的超时为60秒。
- 删除指定帐户列表成员的超时为60秒。
- 获取指定帐户列表成员的超时为60秒。
