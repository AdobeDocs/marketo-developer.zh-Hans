---
title: 列表成员资格（静态列表）
feature: REST API, Static Lists
description: 使用Marketo潜在客户数据库REST API向静态列表添加潜在客户、删除潜在客户、检索列表成员和检查列表成员资格。
exl-id: b8f74bcf-834a-44db-81fd-621048afeba4
source-git-commit: 3e6d310c5aec1a3435424fb122b71d825db5af0e
workflow-type: tm+mt
source-wordcount: '427'
ht-degree: 5%

---

# 列表成员资格（静态列表）

[列表成员资格端点引用](https://developer.adobe.com/marketo-apis/api/mapi#tag/Static-Lists)

List Membership API提供了用于管理静态列表成员的Lead Database端点。 使用这些端点可以：

- 将潜在客户添加到列表。
- 从列表中删除潜在客户。
- 检索列表的成员。
- 确定潜在客户是否为列表的成员。

## 端点

| 终结点 | 方法 | 路径 |
| --- | --- | --- |
| 添加到列表 | POST | `/rest/v1/lists/{listId}/leads.json` |
| 从列表中移除 | DELETE | `/rest/v1/lists/{listId}/leads.json` |
| 按列表ID获取潜在客户 | GET | `/rest/v1/lists/{listId}/leads.json` |
| List 会员 | GET | `/rest/v1/lists/{listId}/leads/ismember.json` |

## 添加到列表

使用[添加到列表](https://developer.adobe.com/marketo-apis/api/mapi#tag/Static-Lists/operation/addLeadsToListUsingPOST)终结点向列表添加一个或多个成员。 传递所需的`listId`路径参数以及一个或多个包含潜在客户ID的`id`查询参数。 商机ID的最大数量为300。

响应包含一个`result`数组，请求中每个潜在客户ID的状态均为该数组。

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

使用[从列表中删除](https://developer.adobe.com/marketo-apis/api/mapi#tag/Static-Lists/operation/removeLeadsFromListUsingDELETE)终结点从列表中删除一个或多个成员。 传递所需的`listId`路径参数以及一个或多个包含潜在客户ID的`id`查询参数。 商机ID的最大数量为300。

响应包含一个`result`数组，请求中每个潜在客户ID的状态均为该数组。

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

使用[按列表ID获取潜在客户](https://developer.adobe.com/marketo-apis/api/mapi#tag/Static-Lists/operation/getLeadsByListIdUsingGET)端点检索列表的成员。 传递所需的`listId`路径参数。 您还可以传递可选的查询参数以指定筛选条件。

可选的查询参数包括：

- `batchSize`：指定在一次调用中返回的潜在客户记录数。 缺省值和最大值是300。
- `nextPageToken`：对大型结果集进行分页。 在第一次调用中省略此参数，并将其包含在后续调用中。
- `fields`：指定要返回的以逗号分隔的字段名称列表。 如果忽略此参数，则响应将包括`email`、`updatedAt`、`createdAt`、`lastName`、`firstName`和`id`。

响应包含具有在请求中指定的潜在客户字段的`result`数组。

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

使用[Member of List](https://developer.adobe.com/marketo-apis/api/mapi#tag/Static-Lists/operation/areLeadsMemberOfListUsingGET)端点确定一个或多个潜在客户是否为列表的成员。 传递所需的`listId`路径参数以及一个或多个包含潜在客户ID的`id`查询参数。 商机ID的最大数量为300。

响应包含一个`result`数组，请求中每个潜在客户ID的状态均为该数组。

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
