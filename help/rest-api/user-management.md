---
title: 用户管理
feature: REST API
description: 适用于CRUD的Marketo用户管理API指南，内容涉及用户、基于标题的身份验证、角色和工作区、状态代码处理、日期时间格式和查询端点。
exl-id: 2a58f496-0fe6-4f7e-98ef-e9e5a017c2de
TQID: https://experienceleague.adobe.com/V1NzpIl-peHBi9rqy8YwdJDh3O-dViIdF0cBsDSI-w8
product_v2: id: b27e5950-9033-45ac-9f86-eb22e567f615
feature_v2: id: b13bd2ad-8e65-49e5-9691-2a0d31067b35id: d1d0a9cd-295d-4976-8c39-ddae266f240eid: d65b4a73-87a3-4d56-b638-74e74d9939ce
role_v2: id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
topic_v2: id: eddd9b14-83bd-4ff4-9072-54a4a484abb7
source-git-commit: 3e6d310c5aec1a3435424fb122b71d825db5af0e
workflow-type: tm+mt
source-wordcount: 1440
ht-degree: 6%

---

# 用户管理

[用户管理终结点引用](https://developer.adobe.com/marketo-apis/api/user/)

Marketo用户管理端点对用户记录执行CRUD操作。 要创建用户，请发送邀请。 然后，用户设置密码并首次访问Marketo。

与其他Marketo REST API不同，在使用用户管理API时：

- 以HTTP标头发送访问令牌。 您不能将访问令牌作为查询字符串参数传递。 请参阅[身份验证指南](authentication.md)。
- 创建REST API [自定义服务](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/administration/additional-integrations/create-a-custom-service-for-use-with-rest-api)的用户角色时，从以下每个组中选择权限：
  1. 来自[访问管理员](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/administration/users-and-roles/descriptions-of-role-permissions)组的“访问用户”权限
  1. 从[Access API](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/administration/users-and-roles/descriptions-of-role-permissions)组访问“Access User Management Api”
- 评估HTTP响应状态代码，因为响应正文不包含“success”布尔属性。 成功的调用返回状态代码200。 失败的调用返回非200状态代码和标准“错误”数组，其中含有错误代码和描述性消息。
- 将日期时间字符串格式设置为`yyyyMMdd'T'HH:mm:ss.SSS't'+|-hhmm`。 此格式适用于`createdAt`、`updatedAt`和`expiresAt`。
- 请勿为用户管理API端点添加“/rest”前缀。

## 查询

“用户管理”查询可以检索所有用户、角色和工作区。 他们还可以按用户ID检索一个用户或关联的角色和工作区记录。

### 用户（按ID）

[通过ID获取用户](https://developer.adobe.com/marketo-apis/api/user/#tag/User-Management/operation/getUserUsingGET)终结点采用单个`userid`路径参数，并为已接受其邀请的用户返回单个用户记录。

```http
GET /userservice/management/v1/users/{userid}/user.json
```

```json
{
  "userid": "jamie@houselannister.com",
  "firstName": "Jamie",
  "lastName": "Lannister",
  "emailAddress": "jamie@lannister.com",
  "optedIn": false,
  "failedLogins": 0,
  "failedDeviceCode": 0,
  "isLocked": false,
  "lockedReason": null,
  "id": 0,
  "apiOnly": false,
  "userRoleWorkspaces": [
    {
      "accessRoleId": 1,
      "accessRoleName": "Admin",
      "workspaceId": 0,
      "workspaceName": "AllZones"
    },
    {
      "accessRoleId": 2,
      "accessRoleName":
      "Standard User",
      "workspaceId": 1008,
      "workspaceName": "World"
    }
  ],
  "expiresAt": "2020-12-31T08:00:00.000t+0000",
  "lastLoginAt": "2020-02-05T01:02:23.000t+0000"
}
```

### 按ID列出的邀请用户

[按ID获取受邀用户](https://developer.adobe.com/marketo-apis/api/user/#tag/User-Management/operation/getInvitedUserUsingGET)端点采用单个`userid`路径参数并返回“待处理”用户的单个用户记录（尚未接受其邀请）。

```http
GET /userservice/management/v1/users/{userid}/invite.json
```

```json
{
    "id": 25112,
    "firstName": "Jamie",
    "lastName": "Lannister",
    "emailAddress": "jamie@lannister.com",
    "userId": "jamie@lannister.com",
    "subscriptionId": 3381,
    "status": "pending",
    "expiresAt": "20200807T20:49:54.0t+0000",
    "createdAt": "20200731T20:49:54.0t+0000",
    "updatedAt": "20200731T20:49:54.0t+0000"
}
```

### 按Id划分的角色和工作区

[按ID](https://developer.adobe.com/marketo-apis/api/user/#tag/User-Management/operation/getUserRolesAndWorkspacesUsingGET)获取角色和工作区端点采用一个`userid`路径参数并返回用户的角色和工作区记录。 响应数组中的每个对象都包含角色和工作区ID和名称。

```http
GET /userservice/management/v1/users/{userid}/roles.json
```

```json
[
  {
    "accessRoleId": 1,
    "accessRoleName": "Admin",
    "workspaceId": 0,
    "workspaceName": "AllZones"
  },
  {
    "accessRoleId": 2,
    "accessRoleName": "Standard User",
    "workspaceId": 1008,
    "workspaceName": "World"
  }
]
```

### 浏览用户

[获取用户](https://developer.adobe.com/marketo-apis/api/user/#tag/User-Management/operation/getUsersUsingGET)终结点返回所有用户记录。 它支持以下可选的integer参数：

- `pageSize`指定要返回的最大条目数。 默认值为20，最大值为200。
- `pageOffset`指定从何处开始检索条目。 默认值为0，可以与`pageSize`一起使用。

```http
GET /userservice/management/v1/users/allusers.json
```

```json
[
  {
    "userid": "jamie@lannister.com",
    "firstName": "Jamie",
    "lastName": "Lannister",
    "emailAddress": "jamie@houselannister.com",
    "id": 6785,
    "apiOnly": false
  },
  {
    "userid": "jeoffery@housebaratheon.com",
    "firstName": "Jeoffery",
    "lastName": "Baratheon",
    "emailAddress": "jeoffery@housebaratheon.com",
    "id": 7718,
    "apiOnly": false
  },
  {
    "userid": "rickon@housestark.com",
    "firstName": "Rickon",
    "lastName": "Stark",
    "emailAddress": "rickon@housestark.com",
    "id": 8612,
    "apiOnly": false
  }
]
```

>[!NOTE]
>
>在上述代码示例中，显示的`userid`适用于已迁移到Adobe IMS的客户。 尚未迁移的客户将在`userid`字段中看到常规电子邮件地址。

### 浏览角色

[获取角色](https://developer.adobe.com/marketo-apis/api/user/#tag/User-Management/operation/getRolesUsingGET)终结点返回所有角色记录的列表。

```http
GET /userservice/management/v1/users/roles.json
```

```json
[
    {
        "id": 1,
        "name": "Admin",
        "description": "All permissions",
        "type": "system",
        "hidden": false,
        "onlyAllZones": true,
        "createdAt": "20100327T18:27:42.0t+0000",
        "updatedAt": "20100327T18:27:42.0t+0000"
    },
    {
        "id": 2,
        "name": "Standard User",
        "description": "All permissions except Admin",
        "type": "system",
        "hidden": false,
        "onlyAllZones": false,
        "createdAt": "20100327T18:27:42.0t+0000",
        "updatedAt": "20180423T02:33:29.0t+0000"
    },
    {
        "id": 24,
        "name": "RTP Launcher",
        "description": "Role required for launcher in RTP",
        "type": "system",
        "hidden": false,
        "onlyAllZones": false,
        "createdAt": "20151024T01:45:40.0t+0000",
        "updatedAt": "20171024T23:41:24.0t+0000"
    },
    {
        "id": 25,
        "name": "RTP Editor",
        "description": "Role required for editor in RTP",
        "type": "system",
        "hidden": false,
        "onlyAllZones": false,
        "createdAt": "20151024T01:45:40.0t+0000",
        "updatedAt": "20171024T23:41:24.0t+0000"
    },
    {
        "id": 101,
        "name": "Analytics User",
        "description": "Has access to Analytics",
        "type": "custom",
        "hidden": false,
        "onlyAllZones": false,
        "createdAt": "20100327T18:27:42.0t+0000",
        "updatedAt": "20180423T02:33:29.0t+0000"
    },
    {
        "id": 102,
        "name": "Marketing User",
        "description": "All permissions except Admin",
        "type": "custom",
        "hidden": false,
        "onlyAllZones": false,
        "createdAt": "20100327T18:27:42.0t+0000",
        "updatedAt": "20100327T18:27:42.0t+0000"
    },
    {
        "id": 103,
        "name": "Web Designer",
        "description": "Has access to Design Studio except approval permission",
        "type": "custom",
        "hidden": false,
        "onlyAllZones": false,
        "createdAt": "20100327T18:27:42.0t+0000",
        "updatedAt": "20180423T02:33:29.0t+0000"
    }
]
```

### 浏览工作区

[获取工作区](https://developer.adobe.com/marketo-apis/api/user/#tag/User-Management/operation/getWorkspacesUsingGET)终结点返回所有工作区记录的列表。

```http
GET /userservice/management/v1/users/workspaces.json
```

```json
[
  {
    "id": 1,
    "name": "Default",
    "description": "Initial workspace for Marketing Activities, Design Studio, and so on.",
    "globalViz": 0,
    "status": "active",
    "currencyInfo": null,
    "createdAt": "20160910T23:08:05.0t+0000",
    "updatedAt": "20160910T23:08:05.0t+0000"
  },
  {
    "id": 1008,
    "name": "World",
    "description": "",
    "globalViz": 0,
    "status": "active",
    "currencyInfo": null,
    "createdAt": "20181119T21:59:36.0t+0000",
    "updatedAt": "20181119T21:59:36.0t+0000"
  },
  {
    "id": 1009,
    "name": "Reproduction - US English - All Leads",
    "description": "A Workspace for recreating customer-reported problems.",
    "globalViz": 1,
    "status": "active",
    "currencyInfo": null,
    "createdAt": "20190129T23:36:37.0t+0000",
    "updatedAt": "20190129T23:36:37.0t+0000"
  },
  {
    "id": 1010,
    "name": "US",
    "description": "United States - Qualified Leads",
    "globalViz": 0,
    "status": "active",
    "currencyInfo": null,
    "createdAt": "20190322T15:55:40.0t+0000",
    "updatedAt": "20190322T15:55:40.0t+0000"
  }
]
```

## 邀请用户

在[Adobe IMS集成订阅](https://experienceleague.adobe.com/zh-hans/docs/marketo/using/product-docs/administration/marketo-with-adobe-identity/adobe-identity-management-overview)上，此终结点仅支持[仅API用户](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/administration/users-and-roles/create-an-api-only-user)的邀请。 要邀请[标准用户](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/administration/users-and-roles/managing-marketo-users)，请改用[Adobe用户管理API](https://developer.adobe.com/umapi/)。

[邀请用户](https://developer.adobe.com/marketo-apis/api/user/#tag/User-Management/operation/inviteUserUsingPOST)端点向新用户发送“欢迎使用Marketo”电子邮件邀请。 该电子邮件包含“登录到Marketo”链接。 收件人将选择链接、创建密码并获得对Marketo的访问权限。

在收件人接受邀请之前，其状态为“待处理”，无法编辑用户记录。 待处理的邀请将在发送七天后过期。 有关详细信息，请参阅[Marketo用户管理文档](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/administration/users-and-roles/managing-marketo-users)。

以`application/json`格式传递请求正文中的参数。

必需的参数为`emailAddress`、`firstName`、`lastName`和`userRoleWorkspaces`。 `userRoleWorkspaces`参数是包含`accessRoleId`和`workspaceId`属性的对象数组。

`userid`参数是用于登录的唯一用户标识符，必须格式化为电子邮件地址。 如果请求省略`userid`，则其值默认为`emailAddress`的值。

布尔`apiOnly`参数指定用户是否为[仅限API的用户](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/administration/users-and-roles/create-an-api-only-user)。 `expiresAt`参数指定用户登录过期的时间，并使用W3C ISO-8601格式，不带毫秒。 如果请求省略`expiresAt`，则用户永不过期。 `reason`参数描述了邀请的原因。

邀请成功时，端点会返回“true”。 否则，它会返回一条错误消息。

```http
POST /userservice/management/v1/users/invite.json
```

```text
Content-Type: application/json
```

```json
{
  "emailAddress": "daenerys@housetargaryen.com",
  "firstName": "Daenerys",
  "lastName": "Targaryen",
  "expiresAt": "2020-12-31T23:59:59-05:00",
  "reason": "Keeper of dragons",
  "userRoleWorkspaces": [
    {
      "accessRoleId": 1,
      "workspaceId": 0
    }
  ]
}
```

```text
true
```

下图显示了发送给新用户的“欢迎使用Marketo”电子邮件。 主题是“Marketo登录信息”。 发件人是与[REST API自定义服务](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/administration/additional-integrations/create-a-custom-service-for-use-with-rest-api)关联的仅API用户的电子邮件地址。 firstName、lastName和emailAddress参数指定收件人。

![邀请用户电子邮件](assets/invite-user-email.png)

用户通过输入两次密码并选择“CREATE PASSWORD”按钮来接受邀请。 然后，用户将获得对Marketo的访问权限。

## 更新用户

用户接受邀请后，您可以更新用户属性或删除用户。 将属性作为参数以application/json格式传递到请求正文中。

### 更新用户属性

在[Adobe IMS集成订阅](https://experienceleague.adobe.com/zh-hans/docs/marketo/using/product-docs/administration/marketo-with-adobe-identity/adobe-identity-management-overview)上，此终结点仅支持更新[仅限API的用户](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/administration/users-and-roles/create-an-api-only-user)的属性。 要更新[标准用户](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/administration/users-and-roles/managing-marketo-users)的属性，请改用[Adobe用户管理API](https://developer.adobe.com/umapi/)。

[更新用户属性](https://developer.adobe.com/marketo-apis/api/user/#tag/User-Management/operation/updateUserAttributeUsingPOST)终结点采用单个`userid`路径参数并返回单个用户记录。 请求正文包含一个或多个要更新的用户属性： `emailAddress`、`firstName`、`lastName`、`expiresAt`。

```http
POST /userservice/management/v1/users/{userid}/update.json
```

```text
Content-Type: application/json
```

```json
{
  "firstName": "JAMIE",
  "lastName": "LANISTER",
  "expiresAt": "20211231T08:00:00.000t+0000"
}
```

```json
{
  "userid": "jamie@houselannister.com",
  "firstName": "JAMIE",
  "lastName": "LANISTER",
  "emailAddress": "jamie@houselannister.com",
  "optedIn": false,
  "failedLogins": 0,
  "failedDeviceCode": 0,
  "isLocked": false,
  "lockedReason": null,
  "id": 0,
  "apiOnly": false,
  "userRoleWorkspaces": [
    {
      "accessRoleId": 1,
      "accessRoleName": "Admin",
      "workspaceId": 0,
      "workspaceName": "AllZones"
    },
    {
      "accessRoleId": 2,
      "accessRoleName":
      "Standard User",
      "workspaceId": 1008,
      "workspaceName": "World"
    }
  ],
  "expiresAt": "2021-12-31T08:00:00.000t+0000"
  "lastLoginAt": "2020-02-05T01:02:23.000t+0000"
}
```

#### 删除用户

在[Adobe IMS集成订阅](https://experienceleague.adobe.com/zh-hans/docs/marketo/using/product-docs/administration/marketo-with-adobe-identity/adobe-identity-management-overview)上，此端点仅支持删除[仅限API的用户](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/administration/users-and-roles/create-an-api-only-user)。 要删除[标准用户](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/administration/users-and-roles/managing-marketo-users)，请改用[Adobe用户管理API](https://developer.adobe.com/umapi/)。

[删除用户](https://developer.adobe.com/marketo-apis/api/user/#tag/User-Management/operation/deleteUserUsingPOST)终结点采用单个`userid`路径参数并从实例中删除相应的用户。 这是破坏性删除，无法撤消。 如果成功，则返回200状态代码，否则返回错误消息。

```http
POST /userservice/management/v1/users/{userid}/delete.json
```

#### 删除受邀用户

[删除受邀用户](https://developer.adobe.com/marketo-apis/api/user/#tag/User-Management/operation/deleteInvitedUserUsingPOST)终结点采用单个`userid`路径参数并从实例中删除相应的“挂起”用户（用户尚未接受其邀请）。 这是破坏性删除，无法撤消。 如果成功，则返回200状态代码，否则返回错误消息。

```http
POST /userservice/management/v1/users/{userid}/invite/delete.json
```

## 更新角色

您可以添加或删除角色。 将属性作为参数以application/json格式传递到请求正文中。

## 添加角色

[添加角色](https://developer.adobe.com/marketo-apis/api/user/#tag/User-Management/operation/addRolesUsingPOST)终结点采用单个`userid`路径参数，并将一个或多个用户角色添加到相应的用户。 请求正文包含一个或多个对象的列表，每个对象都包含`accessRoleId`和`workspaceId`属性。 如果成功，则返回指定用户的`accessRoleId/workspaceId`对的完整列表。

```http
POST /userservice/management/v1/users/{userid}/roles/create.json
```

```text
Content-Type: application/json
```

```json
[
  {
    "accessRoleId": 2,
    "workspaceId": 1008
  }
]
```

```json
[
  {
    "accessRoleId": 1,
    "accessRoleName": "Admin",
    "workspaceId": 0,
    "workspaceName": "AllZones"
  },
  {
    "accessRoleId": 2,
    "accessRoleName": "Standard User",
    "workspaceId": 1008,
    "workspaceName": "World"
  }
]
```

## 删除角色

[删除角色](https://developer.adobe.com/marketo-apis/api/user/#tag/User-Management/operation/deleteRolesUsingPOST)终结点采用单个`userid`路径参数，并从相应的用户中删除一个或多个用户角色。 请求正文包含一个或多个对象的列表，每个对象都包含`accessRoleId`和`workspaceId`属性。 如果成功，则会返回指定用户的accessRoleId/workspaceId对的剩余列表。

```http
POST /userservice/management/v1/users/{userid}/roles/delete.json
```

```text
Content-Type: application/json
```

```json
[
  {
    "accessRoleId": 2,
    "workspaceId": 1008
  }
]
```

```json
[
  {
    "accessRoleId": 1,
    "accessRoleName": "Admin",
    "workspaceId": 0,
    "workspaceName": "AllZones"
  }
]
```
