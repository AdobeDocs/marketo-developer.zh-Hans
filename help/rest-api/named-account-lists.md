---
title: “指定帐户列表”
feature: REST API
description: “配置指定帐户列表。”
source-git-commit: 8c1ffb6db05da49e7377b8345eeb30472ad9b78b
workflow-type: tm+mt
source-wordcount: '696'
ht-degree: 2%

---


# 指定帐户列表

[命名帐户列出终结点引用](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Named-Account-Lists)

[指定帐户列表](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/target-account-management/target/account-lists) 在Marketo中，表示指定帐户的集合。 它们可用于多种情况，包括分类、数据扩充和智能营销活动过滤。 命名帐户列表API允许远程管理这些列表资源及其成员资格。
`Content`

## 权限

要查询指定帐户列表，需要只读指定帐户列表或读写指定帐户列表权限。 要创建、更新或删除列表，需要读写指定帐户列表权限。 查询列表成员资格需要只读命名帐户或读写命名帐户权限，而管理成员资格需要读写命名帐户权限。

## 模型

指定帐户列表的标准字段数量有限，并且不能与自定义字段一起扩展。
`Named Account List Field`

| 名称 | 数据类型 | 可更新 | 备注 |
|---|---|---|---|
| marketoGUID | 字符串 | 假 | 指定帐户列表的唯一字符串标识符。 此字段由系统管理，在创建记录时不允许作为字段。 “dedupeBy”：“idField”执行创建或更新时使用的字段。 |
| name | 字符串 | 真 | 列表的名称。 执行create或update时“dedupeBy”使用的字段：“dedupeFields”。 |
| createdat | 日期时间 | 假 | 创建列表的日期时间。 此字段由系统管理，在创建或更新记录时不允许作为字段。 |
| 更新时间 | 日期时间 | 假 | 列表最近更新的日期时间。 此字段由系统管理，在创建或更新记录时不允许作为字段。 |
| 类型 | 字符串 | 假 | 列表的类型。 其值可以为“default”或“external”。 外部列表是由CRM帐户视图创建的列表。 |


## 查询

查询帐户列表简单方便。 目前，查询指定帐户列表时只有两个有效的filterTypes：“dedupeFields”和“idField”。 要过滤的字段是在 `filterType` 参数，值设置于 `filterValues as` 以逗号分隔的列表。 此 `nextPageToken` 和 `batchSize` 过滤器也是可选参数。

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

创建和更新指定帐户列表记录遵循其他Lead Database创建和更新操作的已建立模式。 请记住，指定帐户列表只有一个可更新字段， `name`.

端点允许使用两种标准操作类型：“createOnly”和“updateOnly”。  此 `action defaults` 到“createOnly”中。

可选 `dedupeBy parameter` 可以指定，如果操作为 `updateOnly`.  允许的值为“dedupeFields”（对应于“name”）或“idField”（对应于“marketoGUID”）。  在 `createOnly` 模式，仅允许“name”作为 `dedupeBy` 字段。 一次最多可以提交300条记录。

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

删除指定帐户列表非常简单，您可以根据 `name`，或 `marketoGUID` 列表的。 要选择要使用的键，请在中将“dedupeFields”传递给name，或将“idField”传递给marketoGUID`deleteB` 请求的成员。 如果未设置，则默认为dedupeFields。 一次最多可以删除300条记录。

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

如果找不到给定键的记录，则相应的结果项将具有`status` 跳过”，并且是包含用于描述失败的代码和消息的原因，如上面的示例所示。

## 管理成员资格

### 查询成员资格

查询指定帐户列表的成员资格非常简单，只需指定`i` 帐户列表的。 可选参数包括：

-`field`  — 要包含在响应记录中的以逗号分隔的字段列表 — `nextPageToke`  — 用于分页结果集 — `batchSiz`  — 用于指定要返回的记录数

如果`field` 未设置，则`marketoGUI`，`nam`， `createdA`、和`updatedA` 将被退回。 `batchSiz` 的最大值和默认值为300。

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

从帐户列表中删除记录具有不同的路径，但接口相同，需要`marketoGUI` 要删除的每个记录。 一次最多可以删除300条记录。

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
