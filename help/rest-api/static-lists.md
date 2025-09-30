---
title: 静态列表
feature: REST API, Static Lists
description: 使用Marketo REST API查询、创建、更新和删除静态列表，以及包含ID、名称和浏览、文件夹作用域、分页和日期过滤器的端点。
exl-id: 20679fd2-fae2-473e-84bc-cb4fdf2f5151
source-git-commit: 7557b9957c87f63c2646be13842ea450035792be
workflow-type: tm+mt
source-wordcount: '760'
ht-degree: 1%

---

# 静态列表

[静态列表终结点引用](https://developer.adobe.com/marketo-apis/api/asset/#tag/Static-Lists)

[列表成员资格终结点引用](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Static-Lists)

Marketo提供了一组REST API，用于对静态列表执行CRUD操作。 这些API遵循资产API的标准界面模式，提供查询、创建、更新和删除选项。

## 查询

查询静态列表遵循[按ID](https://developer.adobe.com/marketo-apis/api/asset/#tag/Static-Lists/operation/getStaticListByIdUsingGET)、[按名称](https://developer.adobe.com/marketo-apis/api/asset/#tag/Static-Lists/operation/getStaticListByNameUsingGET)和[浏览](https://developer.adobe.com/marketo-apis/api/asset/#tag/Static-Lists/operation/getStaticListsUsingGET)的标准查询类型。

### 按Id

[按ID](https://developer.adobe.com/marketo-apis/api/asset/#tag/Static-Lists/operation/getStaticListByIdUsingGET)进行查询，将单个静态列表`id`作为路径参数并返回单个静态列表记录。

```
GET /rest/asset/v1/staticList/{id}.json
```

```json
{
    "success": true,
    "errors": [],
    "requestId": "843c#1641f969e96",
    "result": [
        {
            "id": 1021,
            "name": "Foundation Seed List",
            "createdAt": "2017-07-27T01:38:33Z+0000",
            "updatedAt": "2017-07-27T01:39:26Z+0000",
            "folder": {
                "id": 13,
                "type": "Folder"
            },
            "computedUrl": "https://app-sjqe.marketo.com/#ST1021A1"
        }
    ]
}
```

#### 按名称

[按名称](https://developer.adobe.com/marketo-apis/api/asset/#tag/Smart-Lists/operation/getSmartListByNameUsingGET)查询将静态列表`name`作为参数并返回单个静态列表记录。 对实例中的所有静态列表名称执行完全匹配的字符串，并返回与该名称匹配的静态列表的结果。

```
GET /rest/asset/v1/staticList/byName.json?name=Foundation Seed List
```

```json
{
    "success": true,
    "errors": [],
    "requestId": "28ab#1641fa246b9",
    "result": [
        {
            "id": 1021,
            "name": "Foundation Seed List",
            "createdAt": "2017-07-27T01:38:33Z+0000",
            "updatedAt": "2017-07-27T01:39:26Z+0000",
            "folder": {
                "id": 13,
                "type": "Folder"
            },
            "computedUrl": "https://app-sjqe.marketo.com/#ST1021A1"
        }
    ]
}
```

#### 浏览

静态列表也可在批次[中检索](https://developer.adobe.com/marketo-apis/api/asset/#tag/Static-Lists/operation/getStaticListsUsingGET)。 `folder`参数可用于指定将在其中执行查询的父文件夹，并将其格式化为包含ID和类型的JSON对象。 与其他批量资源检索端点一样，`offset`和`maxReturn`是可用于分页的可选参数。 `earliestUpdatedAt`和`latestUpdatedAt`参数允许您为返回在给定范围内创建或更新的静态列表设置低日期时间水印和高日期时间水印。 日期时间值必须是有效的ISO-8601字符串，并且不应包括毫秒

```
GET /rest/asset/v1/staticLists.json?folder={"id":13,"type":"Folder"}
```

```json
{
    "success": true,
    "errors": [],
    "requestId": "2dc0#1641f846633",
    "result": [
        {
            "id": 1021,
            "name": "Foundation Seed List",
            "createdAt": "2017-07-27T01:38:33Z+0000",
            "updatedAt": "2017-07-27T01:39:26Z+0000",
            "folder": {
                "id": 13,
                "type": "Folder"
            },
            "computedUrl": "https://app-sjqe.marketo.com/#ST1021A1"
        },
        {
            "id": 1022,
            "name": "Blacklist Seed List",
            "createdAt": "2017-07-27T23:19:33Z+0000",
            "updatedAt": "2017-07-27T23:21:29Z+0000",
            "folder": {
                "id": 13,
                "type": "Folder"
            },
            "computedUrl": "https://app-sjqe.marketo.com/#ST1022A1"
        },
        {
            "id": 1023,
            "name": "Possible Duplicates Seed List",
            "createdAt": "2017-07-28T00:10:02Z+0000",
            "updatedAt": "2017-07-28T00:11:22Z+0000",
            "folder": {
                "id": 13,
                "type": "Folder"
            },
            "computedUrl": "https://app-sjqe.marketo.com/#ST1023A1"
        }
    ]
}
```

## 创建和更新

[创建静态列表](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Static-Lists/createStaticListUsingPOST)是使用application/x-www-form-urlencoded POST执行的，带有两个必需的参数。 `folder`参数用于指定要在其下创建静态列表的父文件夹，其格式为包含ID和类型的JSON对象。 `name`参数用于命名静态列表，必须是唯一的。 （可选）可以使用`description`参数描述静态列表。

```
POST /rest/asset/v1/staticLists.json
```

```
Content-Type: application/x-www-form-urlencoded
```

```
folder={"id":1034,"type":"Program"}&name=My Static List
```

```json
{
    "success": true,
    "errors": [],
    "requestId": "1269d#164209d6e1e",
    "result": [
        {
            "id": 1027,
            "name": "My Static List",
            "createdAt": "2018-06-21T04:32:25Z+0000",
            "updatedAt": "2018-06-21T04:32:25Z+0000",
            "folder": {
                "id": 1034,
                "type": "Program"
            },
            "computedUrl": "https://app-sjqe.marketo.com/#ST1027A1"
        }
    ]
}
```

[对静态列表](https://developer.adobe.com/marketo-apis/api/asset/#tag/Static-Lists/operation/updateStaticListUsingPOST)的更新是通过带有两个可选参数的单独终结点进行的。 `description`参数可用于更新静态列表描述。 `name`参数可用于更新静态列表名称，并且必须是唯一的。

```
POST /rest/asset/v1/staticList/{id}.json
```

```
Content-Type: application/x-www-form-urlencoded
```

```
description=This is a static list used for testing
```

```json
{
    "success": true,
    "errors": [],
    "requestId": "f84f#16420b4c746",
    "result": [
        {
            "id": 1027,
            "name": "My Static List",
            "description": "This is a static list used for testing",
            "createdAt": "2018-06-21T04:32:26Z+0000",
            "updatedAt": "2018-06-21T04:57:55Z+0000",
            "folder": {
                "id": 1034,
                "type": "Program"
            },
            "computedUrl": "https://app-sjqe.marketo.com/#ST1027A1"
        }
    ]
}
```

### 删除

[删除静态列表](https://developer.adobe.com/marketo-apis/api/asset/#tag/Static-Lists/operation/deleteStaticListByIdUsingPOST)需要一个静态列表`id`作为路径参数。 无法删除导入或导出操作正在使用的静态列表，或其他资产正在使用的静态列表。

```
POST /rest/asset/v1/staticList/{id}/delete.json
```

```json
{
    "success": true,
    "errors": [],
    "requestId": "2c79#16420ded0e9",
    "result": [
        {
            "id": 1027
        }
    ]
}
```

## 列表成员资格

列表成员资格端点提供添加、删除和查询静态列表成员的功能。 此外，您还可以查询静态列表成员资格。

### 添加到列表

[添加到列表](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Static-Lists/operation/addLeadsToListUsingPOST)终结点用于将一个或多个成员添加到列表。 终结点采用必需的`listId`路径参数，以及一个或多个ID查询参数，这些参数包含潜在客户ID（允许的最大值为300）。

响应包含由JSON对象组成的`result`数组，每个潜在客户ID的状态在请求中指定。

```
POST /rest/v1/lists/{listId}/leads.json?id=318594&id=318595
```

```json
{
    "requestId": "6860#1706170ba29",
    "result": [
        {
            "id": 318594,
            "status": "added"
        },
        {
            "id": 318595,
            "status": "skipped",
            "reasons": [
                {
                    "code": "1004",
                    "message": "Lead not found"
                }
            ]
        }
    ],
    "success": true
}
```

### 从列表中移除

[从列表中删除](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Static-Lists/operation/removeLeadsFromListUsingDELETE)终结点用于从列表中删除一个或多个成员。 终结点采用必需的`listId`路径参数，以及一个或多个`id`查询参数，这些参数包含潜在客户ID（允许的最大值为300）。

响应包含由JSON对象组成的`result`数组，每个潜在客户ID的状态在请求中指定。

```
DELETE /rest/v1/lists/{listId}/leads.json?id=318603&id=318595&id=999999
```

```
{
    "requestId": "9e79#17061689ac3",
    "result": [
        {
            "id": 318603,
            "status": "removed"
        },
        {
            "id": 318595,
            "status": "removed"
        },
        {
            "id": 999999,
            "status": "skipped",
            "reasons": [
                {
                    "code": "1004",
                    "message": "Lead not found"
                }
            ]
        }
    ],
    "success": true
}
```

### 查询列表

[按列表ID ](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Static-Lists/operation/getLeadsByListIdUsingGET)获取潜在客户端点用于检索列表的成员。 终结点采用必需的`listId`路径参数，并允许多个可选查询参数指定筛选条件。

`batchSize`参数用于指定在单个调用中返回的潜在客户记录数（默认值为300，最大值为300）。

`nextPageToken`参数用于在大型结果集中分页。 此参数不是在第一次调用中传递，而是仅在分页的后续调用中传递。

`fields`参数包含响应中要返回的以逗号分隔的字段名称列表。 如果此请求中未包含字段参数，则返回以下默认字段： email、updatedAt、createdAt、lastName、firstName和id。

响应包含一个`result`数组，该数组由包含请求中指定的潜在客户字段的JSON对象组成。

```
GET /rest/v1/lists/{listId}/leads.json?batchSize=3
```

```json
{
    "requestId": "ddae#170615ba0cc",
    "result": [
        {
            "id": 318594,
            "firstName": "Hanna",
            "lastName": "Crawford",
            "email": "208161Robert.L.Deacon@pookmail.com",
            "updatedAt": "2015-04-06T17:13:50Z",
            "createdAt": "2015-04-06T17:13:50Z"
        },
        {
            "id": 318595,
            "firstName": "Bertha",
            "lastName": "Fulton",
            "email": "208160Tyrone.V.Dyer@trashymail.com",
            "updatedAt": "2015-04-06T17:13:50Z",
            "createdAt": "2015-04-06T17:13:50Z"
        },
        {
            "id": 318596,
            "firstName": "Faith",
            "lastName": "England",
            "email": "208159Rex.M.Bailey@dodgit.com",
            "updatedAt": "2015-04-06T17:13:50Z",
            "createdAt": "2015-04-06T17:13:50Z"
        }
    ],
    "success": true,
    "nextPageToken": "PS5VL5WD4UOWGOUCJR6VY7JQO24LC2U5DRBU4WO4RQMPHDHTK2T3BEZOR75VLQXYB3245WW2GMDSK==="
}
```

#### 按潜在客户Id查询列表成员资格

List[终结点的](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Static-Lists/operation/areLeadsMemberOfListUsingGET)成员用于查看一个或多个潜在客户是否为列表的成员。 终结点采用必需的`listId`路径参数，以及一个或多个`id`查询参数，这些参数包含潜在客户ID（允许的最大值为300）。

响应包含由JSON对象组成的`result`数组，每个潜在客户ID的状态在请求中指定。

```
GET /rest/v1/lists/{listId}/leads/ismember.json?id=309901&id=318603&id=999999
```

```json
{
    "requestId": "693a#17061475cf9",
    "result": [
        {
            "id": 309901,
            "status": "memberof"
        },
        {
            "id": 318603,
            "status": "notmemberof"
        },
        {
            "id": 999999,
            "status": "skipped",
            "reasons": [
                {
                    "code": "1004",
                    "message": "Lead not found"
                }
            ]
        }
    ],
    "success": true
}
```
