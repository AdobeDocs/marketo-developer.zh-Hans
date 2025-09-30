---
title: 指定帐户列表
feature: REST API
description: 了解如何使用REST API管理Marketo指定帐户列表，包括权限、字段、筛选以及用于查询、创建、更新和删除的端点。
exl-id: 98f42780-8329-42fb-9cd8-58e5dbea3809
source-git-commit: 7557b9957c87f63c2646be13842ea450035792be
workflow-type: tm+mt
source-wordcount: '716'
ht-degree: 2%

---

# 指定帐户列表

[命名帐户列出终结点引用](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Named-Account-Lists)

Marketo中的[命名帐户列表](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/target-account-management/target/account-lists)表示命名帐户的集合。 它们可用于多种情况，包括分类、数据扩充和智能营销活动过滤。 命名帐户列表API允许远程管理这些列表资源及其成员资格。
`Content`

## 权限

要查询指定帐户列表，需要只读指定帐户列表或读写指定帐户列表权限。 要创建、更新或删除列表，需要读写指定帐户列表权限。 查询列表成员资格需要只读命名帐户或读写命名帐户权限，而管理成员资格需要读写命名帐户权限。

## 模型

指定帐户列表的标准字段数量有限，并且不能与自定义字段一起扩展。
`Named Account List Field`

| 名称 | 数据类型 | 可更新 | 注释 |
|---|---|---|---|
| marketoGUID | 字符串 | False | 指定帐户列表的唯一字符串标识符。 此字段由系统管理，在创建记录时不允许作为字段。 “dedupeBy”：“idField”执行创建或更新时使用的字段。 |
| name | 字符串 | True | 列表的名称。 执行create或update时“dedupeBy”使用的字段：“dedupeFields”。 |
| createdat | 日期时间 | False | 创建列表的日期时间。 此字段由系统管理，在创建或更新记录时不允许作为字段。 |
| 更新时间 | 日期时间 | False | 列表最近更新的日期时间。 此字段由系统管理，在创建或更新记录时不允许作为字段。 |
| 类型 | 字符串 | False | 列表的类型。 其值可以为“default”或“external”。 外部列表是由CRM帐户视图创建的列表。 |

## 查询

查询帐户列表简单方便。 目前，查询指定帐户列表时只有两个有效的filterTypes：“dedupeFields”和“idField”。 要筛选的字段在查询的`filterType`参数中设置，值在`filterValues as`中以逗号分隔的列表中设置。 `nextPageToken`和`batchSize`筛选器也是可选参数。

```
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

创建和更新指定帐户列表记录遵循其他Lead Database创建和更新操作的已建立模式。 请记住，指定帐户列表只有一个可更新的字段`name`。

端点允许使用两种标准操作类型：“createOnly”和“updateOnly”。  `action defaults`设置为“createOnly”。

如果操作为`dedupeBy parameter`，则可以指定可选`updateOnly`。  允许的值为“dedupeFields”（对应于“name”）或“idField”（对应于“marketoGUID”）。  在`createOnly`模式中，仅允许“name”作为`dedupeBy`字段。 一次最多可以提交300条记录。

```
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

删除指定帐户列表非常简单，可以根据列表的`name`或`marketoGUID`完成。 要选择要使用的键，请在请求的`deleteB`成员中为name传递“dedupeFields”，为marketoGUID传递“idField”。 如果未设置，则默认为dedupeFields。 一次最多可以删除300条记录。

```
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

在找不到给定键的记录的情况下，相应的结果项将具有`status`个“已跳过”，并且会有一个原因和一个代码和消息描述失败，如上面的示例所示。

## 管理成员资格

### 查询成员资格

查询指定帐户列表的成员资格非常简单，只需要帐户列表的`i`。 可选参数包括：

-`field` — 要包含在响应记录中的以逗号分隔的字段列表
-`nextPageToke` — 用于分页结果集
-`batchSiz` — 用于指定要返回的记录数

如果未设置`field`，则将返回`marketoGUI`、`nam`、`createdA`和`updatedA`。 `batchSiz`的最大值和默认值为300。

```
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

可以轻松地将指定帐户添加到指定帐户列表。 只能使用其marketoGUID添加帐户。 一次最多可以添加300条记录。

```
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

从帐户列表中删除记录的路径不同，但接口相同，需要为要删除的每个记录使用`marketoGUI`。 一次最多可以删除300条记录。

```
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

- 除非下面说明，否则命名帐户列表端点的超时为30秒
   - 同步指定帐户列表： 60秒
   - 删除指定帐户列表：60秒
   - 获取指定帐户列表：60秒
   - 添加指定帐户列表成员：60秒
   - 删除指定帐户列表成员：60秒
   - 获取指定帐户列表成员：60秒
