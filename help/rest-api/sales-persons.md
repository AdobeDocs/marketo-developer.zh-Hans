---
title: 销售人员
feature: REST API
description: 使用SFDC或Dynamics同步，使用externalSalesPersonId与潜在客户关联并执行查询、更新插入和删除的Marketo REST API销售人员记录指南。
exl-id: f8ed5aa5-63c1-4c5b-8683-bf47eed1ea18
source-git-commit: 7557b9957c87f63c2646be13842ea450035792be
workflow-type: tm+mt
source-wordcount: '322'
ht-degree: 0%

---

# 销售人员

[销售人员终结点引用](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Sales-Persons)

对于已启用[SFDC同步](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/crm-sync/salesforce-sync/sfdc-sync-details/sfdc-sync-field-sync)或[Microsoft Dynamics同步](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/crm-sync/microsoft-dynamics/microsoft-dynamics-sync-details/microsoft-dynamics-sync-user-sync)的订阅，销售人员API是只读访问权限。 销售人员是销售线索记录的销售负责人的人员记录类型。 它们按每个Lead记录上的externalSalesPersonId字段与Lead记录相关。 当Lead通过填充的externalSalesPersonId字段与Sales Person关联时，将在Marketo中为该商机记录填充相应的Lead Owner查找字段，从而允许使用相应的过滤器和令牌。

通过使用[同步潜在客户](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Leads/operation/syncLeadUsingPOST)终结点并传递externalSalesPersonId属性，销售人员与潜在客户记录相关联。

通过使用[Sync Opportunities](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Opportunities/operation/syncOpportunitiesUsingPOST)端点并传递externalSalesPersonId属性，销售人员与机会记录相关联。

通过使用[同步公司](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Companies/operation/syncCompaniesUsingPOST)端点并传递externalSalesPersonId属性，销售人员与公司记录相关联。

销售人员记录只能通过API进行编辑。

## 描述

描述销售人员记录遵循销售线索数据库对象的标准模式。

```
GET /rest/v1/salespersons/describe.json
```

```json
{
   "requestId":"185d6#14b51985ff0",
   "success":true,
   "result":[
      {
         "name":"SalesPerson",
         "createdAt":"2015-02-03T22:36:23Z",
         "updatedAt":"2015-02-03T22:36:24Z",
         "idField":"id",
         "dedupeFields":[
            "externalSalesPersonId"
         ],
         "searchableFields":[
            [
               "email"
            ],
            [
               "id"
            ],
            [
               "externalSalesPersonId"
            ]
         ],
         "fields":[
            {
               "name":"id",
               "displayName":"Marketo Id",
               "dataType":"integer",
               "updateable":false
            },
            {
               "name":"createdAt",
               "displayName":"Created At",
               "dataType":"datetime",
               "updateable":false
            },
            {
               "name":"updatedAt",
               "displayName":"Updated At",
               "dataType":"datetime",
               "updateable":false
            },
            {
               "name":"email",
               "displayName":"Email",
               "dataType":"string",
               "length":255,
               "updateable":false
            },
            {
               "name":"externalSalesPersonId",
               "displayName":"External Sales Person Id",
               "dataType":"string",
               "length":255,
               "updateable":false
            }
         ]
      }
   ]
}
```

默认情况下，销售人员的`idField`是“id”，`dedupeFields`只是“externalSalesPersonId”。

## 查询

使用简单键的标准查询模式的销售人员。 此示例显示用户电子邮件用作externalSalesPersonId。 默认情况下，查询会返回为返回记录填充的所有字段。

```
GET /rest/v1/salespersons.json?filterType=dedupeFields&filterValues=david@test.com,sam@test.com
```

```json
 {
   "requestId":"e42b#14272d07d78",
   "success":true,
   "result":[
      {
         "seq":0,
         "id":53453,
         "externalSalesPersonId":"sam@test.com",
         "createdAt":"2015-02-03T22:36:23Z",
         "updatedAt":"2015-02-03T22:36:23Z"
      },
      {
         "seq":1,
         "id":53454,
         "externalSalesPersonId":"david@test.com",
         "createdAt":"2015-02-03T22:36:23Z",
         "updatedAt":"2015-02-03T22:36:23Z"
      }
   ]
}
```

## 创建和更新

更新模式是标准模式。

```
POST /rest/v1/salespersons.json
```

```json
{
   "action":"createOrUpdate",
   "dedupeBy":"dedupeFields",
   "input":[
      {
         "externalSalesPersonId":"sam@test.com",
         "email":"sam@test.com",
         "firstName":"Sam",
         "lastName":"Sanosin"
      },
      {
         "externalSalesPersonId":"david@test.com",
         "email":"david@test.com",
         "firstName":"David",
         "lastName":"Aulassak"
      }
   ]
}
```

```json
{
   "requestId":"e42b#14272d07d78",
   "success":true,
   "result":[
      {
         "seq":0,
         "status": "updated",
         "id":45232
      },
      {
         "seq":1,
         "status": "created",
         "id":45236
      }
   ]
}
```

## 删除

删除的模式是标准的。

“使用中”时不允许删除销售人员。 在这种情况下，将跳过Sales Person。 示例：

- 当销售人员与有效潜在客户关联时
- 当销售人员与已删除的公司关联时

```
POST /rest/v1/salespersons/delete.json
```

```json
{
   "deleteBy":"dedupeFields",
   "input":[
      {
         "externalSalesPersonId":"sam@test.com"
      },
      {
         "externalSalesPersonId":"david@test.com"
      },
      {
         "externalSalesPersonId":"raj@test.com"
      }
   ]
}
```

```json
{
   "requestId":"e42b#14272d07d78",
   "success":true,
   "result":[
      {
         "seq":0,
         "id":56343,
         "status": "deleted"
      },
      {
         "seq":1,
         "id":53453,
         "status": "deleted"
      },
      {
         "seq":2,
         "status": "skipped"
         "reasons":[
            {
               "code":"1013",
               "message":"Record not found"
            }
         ]
      }
   ]
}
```

## 超时

- 除非下面说明，否则销售人员端点的超时为30秒
   - 同步销售人员：60多岁
   - 删除销售人员：60岁
