---
title: 列表成员资格（静态列表）
feature: REST API, Static Lists
description: 使用Marketo潜在客户数据库REST API向静态列表添加潜在客户、删除潜在客户、检索列表成员和检查列表成员资格。
exl-id: b8f74bcf-834a-44db-81fd-621048afeba4
source-git-commit: e2606d6cb12c572603ff069617de58417e43ca63
workflow-type: tm+mt
source-wordcount: '482'
ht-degree: 4%

---

# 列表成员资格（静态列表）

[列表成员资格端点引用](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Static-Lists)

List Membership API提供了用于处理静态列表成员的Lead Database端点。 这些端点可用于向列表添加潜在客户、从列表中删除潜在客户、检索列表的成员以及确定一个或多个潜在客户是否是列表的成员。

## 端点

| 终结点 | 方法 | 路径 |
| --- | --- | --- |
| 添加到列表 | POST | `/rest/v1/lists/{listId}/leads.json` |
| 从列表中移除 | DELETE | `/rest/v1/lists/{listId}/leads.json` |
| 按列表ID获取潜在客户 | GET | `/rest/v1/lists/{listId}/leads.json` |
| List 会员 | GET | `/rest/v1/lists/{listId}/leads/ismember.json` |

## 添加到列表

[添加到列表](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Static-Lists/operation/addLeadsToListUsingPOST)终结点用于将一个或多个成员添加到列表。 终结点采用必需的`listId`路径参数，以及一个或多个`id`查询参数，这些参数包含潜在客户ID（允许的最大值为300）。

响应包含由JSON对象组成的`result`数组，每个潜在客户ID的状态在请求中指定。

```http
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

## 从列表中移除

[从列表中删除](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Static-Lists/operation/removeLeadsFromListUsingDELETE)终结点用于从列表中删除一个或多个成员。 终结点采用必需的`listId`路径参数，以及一个或多个`id`查询参数，这些参数包含潜在客户ID（允许的最大值为300）。

响应包含由JSON对象组成的`result`数组，每个潜在客户ID的状态在请求中指定。

```http
DELETE /rest/v1/lists/{listId}/leads.json?id=318603&id=318595&id=999999
```

```json
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

## 按列表ID获取潜在客户

[按列表ID &#x200B;](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Static-Lists/operation/getLeadsByListIdUsingGET)获取潜在客户端点用于检索列表的成员。 终结点采用必需的`listId`路径参数，并允许多个可选查询参数指定筛选条件。

`batchSize`参数用于指定在单个调用中返回的潜在客户记录数。 默认值和最大值是300。

`nextPageToken`参数用于在大型结果集中分页。 此参数不是在第一次调用中传递，而是仅在后续分页调用中传递。

`fields`参数包含响应中要返回的以逗号分隔的字段名称列表。 如果此请求中未包含`fields`参数，则返回以下默认字段： `email`、`updatedAt`、`createdAt`、`lastName`、`firstName`和`id`。

响应包含一个`result`数组，该数组由包含请求中指定的潜在客户字段的JSON对象组成。

```http
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

## List 会员

List[&#128279;](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Static-Lists/operation/areLeadsMemberOfListUsingGET)终结点的成员用于查看一个或多个潜在客户是否为列表的成员。 终结点采用必需的`listId`路径参数，以及一个或多个`id`查询参数，这些参数包含潜在客户ID（允许的最大值为300）。

响应包含由JSON对象组成的`result`数组，每个潜在客户ID的状态在请求中指定。

```http
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
