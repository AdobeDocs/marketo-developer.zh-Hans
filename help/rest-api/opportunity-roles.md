---
title: 机会角色
feature: REST API
description: 通过REST API管理Marketo机会角色，包括描述、使用复合重复数据删除字段查询、创建更新删除、超时以及无CRM同步。
exl-id: 2ba84f4d-82d0-4368-94e8-1fc6d17b69ed
TQID: https://experienceleague.adobe.com/aE27mBhsrn-0SO41M-pV5NFjoMq--1Lp-L2TQGL7-8Y
product_v2:
  - id: b27e5950-9033-45ac-9f86-eb22e567f615
feature_v2:
  - id: c5f60233-d5ea-4453-a799-0ad258b4d399
role_v2:
  - id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
source-git-commit: 3e6d310c5aec1a3435424fb122b71d825db5af0e
workflow-type: tm+mt
source-wordcount: 254
ht-degree: 0%

---

# 机会角色

[机会角色端点引用](https://developer.adobe.com/marketo-apis/api/mapi#tag/Opportunities/operation/getOpportunityRolesUsingGET)

中间`opportunityRole`对象链接指向商机。

机会角色API仅适用于未启用本机CRM同步的订阅。

## 描述

与机会一样，API为机会角色提供了Describe调用和CRUD操作。

```http
GET /rest/v1/opportunities/roles/describe.json
```

```json
{
   "requestId":"185d6#14b51985ff0",
   "success":true,
   "result":[
      {
         "name":"opportunityRole",
         "displayName":"Opportunity Role",
         "createdAt":"2015-02-03T22:36:23Z",
         "updatedAt":"2015-02-03T22:36:24Z",
         "idField":"marketoGUID",
         "dedupeFields":[
            "externalOpportunityId",
            "leadId",
            "role"
         ],
         "searchableFields":[
            [
               "externalOpportunityId",
               "leadId",
               "role"
            ],
            [
               "marketoGUID"
            ],
            [
               "leadId"
            ],
            [
               "externalOpportunityId"
            ]
         ],
         "fields":[
            {
               "name":"marketoGUID",
               "displayName":"Marketo GUID",
               "dataType":"string",
               "length":36,
               "updateable":false
            },
            {
               "name":"externalOpportunityId",
               "displayName":"External Opportunity Id",
               "dataType":"string",
               "length":50,
               "updateable":false
            },
            {
               "name":"leadId",
               "displayName":"Lead Id",
               "dataType":"integer",
               "updateable":false
            },
            {
               "name":"role",
               "displayName":"Role",
               "dataType":"string",
               "length":50,
               "updateable":false
            },
            {
               "name":"isPrimary",
               "displayName":"Is Primary",
               "dataType":"boolean",
               "updateable":true
            },
            {
               "name":"externalCreatedDate",
               "displayName":"External Created Date",
               "dataType":"datetime",
               "updateable":true
            }
         ]
      }
   ]
}
```

## 查询

`dedupeFields`和`searchableFields`值与机会不同。 `dedupeFields`提供了一个需要`externalOpportunityId`、`leadId`和`role`的复合键。 要成功创建记录，目标实例中必须存在由ID字段引用的机会和商机。

`searchableFields`值`marketoGUID`、`leadId`和`externalOpportunityId`对于与机会使用相同模式的单个查询有效。 您还可以通过复合键进行查询。 此查询需要使用`_method=GET`查询参数通过POST提交的JSON对象。

```http
POST /rest/v1/opportunities/roles.json?_method=GET
```

```json
{
   "filterType": "dedupeFields",
   "fields": [
      "marketoGuid",
      "externalOpportunityId",
      "leadId",
      "role"
   ],
   "input": [
      {
        "externalOpportunityId": "Opportunity1",
        "leadId": 1,
        "role": "Captain"
      },
      {
        "externalOpportunityId": "Opportunity2",
        "leadId": 1872,
        "role": "Commander"
      },
      {
        "externalOpportunityId": "Opportunity3",
        "leadId": 273891,
        "role": "Lieutenant Commander"
      }
   ]
}
```

此请求生成与标准GET查询相同的响应类型，但使用不同的请求接口。

## 创建和更新

使用与业务机会相同的界面创建和更新业务机会角色。

```http
POST /rest/v1/opportunities/roles.json
```

```json
{
   "action": "createOrUpdate",
   "dedupeBy": "dedupeFields",
   "input": [
      {
         "externalOpportunityId": "19UYA31581L000000",
         "leadId": 456783,
         "role": "Technical Buyer",
         "isPrimary": false
      },
      {
         "externalOpportunityId": "19UYA31581L000000",
         "leadId": 456784,
         "role": "Technical Buyer",
         "isPrimary": false
      }
   ]
}
```

```json
{
   "requestId": "e42b#14272d07d78",
   "success": true,
   "result":[
      {
         "seq": 0,
         "status": "updated",
         "marketoGUID": "dff23271-f996-47d7-984f-f2676861b5fb"
      },
      {
         "seq": 1,
         "status": "created",
         "marketoGUID": "cff23271-f996-47d7-984f-f2676861b5fb"
      }
   ]
}
```

## 删除

按重复数据消除字段或ID字段删除机会角色。 将deleteBy参数设置为dedupeFields或idField。 缺省值为dedupeFields。

请求正文包含一个要删除的商机角色的输入数组。 每个呼叫最多允许300个机会角色。

```http
POST /rest/v1/opportunities/roles/delete.json
```

```json
{
   "deleteBy": "dedupeFields",
   "input": [
      {
        "externalOpportunityId": "19UYA31581L000000",
        "leadId": 456783,
        "role": "Technical Buyer"
      }
   ]
}
```

```json
{
    "requestId": "10f7c#173264db42d",
    "result": [
        {
            "seq": 0,
            "marketoGUID": "dff23271-f996-47d7-984f-f2676861b5fb"
            "status": "deleted"
        }
    ]
    "success": true
}
```

## 超时

- 除非另有说明，否则机会角色端点的超时为30秒。
- 同步机会角色的超时为60秒。
- 删除机会角色的超时为60秒。
