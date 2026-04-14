---
title: 电子邮件
feature: REST API
description: 使用Marketo Asset REST API查询、创建、更新、克隆、删除、批准和检查电子邮件资源的依赖项。
exl-id: b41a3ae5-2b25-4103-84b4-320fc2c44bd6
source-git-commit: 59684e1c5a8082ad12f1e4bfc854c0d2dde35d2a
workflow-type: tm+mt
source-wordcount: '497'
ht-degree: 5%

---

# 电子邮件

[电子邮件端点引用](https://developer.adobe.com/marketo-apis/api/asset#tag/Emails_New)

电子邮件是定义消息元数据、内容配置、设置和审批状态的资产记录。

## 访问

本文中描述的端点需要访问令牌：

```text
?access_token=<access_token>
```

请求还需要`x-app-type`标头：

```text
x-app-type: <app-type>
```

## 查询

您可以按资源`id`或使用筛选器端点检索电子邮件元数据。

### 按ID

#### 请求

```http
GET /rest/asset/v2/email/{id}
```

#### 响应

```json
{
  "success": true,
  "warnings": [],
  "errors": [],
  "requestId": "7b3c#1900ab12cd3",
  "result": [
    {
      "id": "1017",
      "name": "Spring Launch Email",
      "description": "Main announcement email",
      "status": "draft"
    }
  ]
}
```

### 筛选条件

过滤器端点支持在工作区中搜索，以及使用其他查询参数缩小结果范围。

`workspaceId`为必填项。

支持的查询参数：

| 参数 | 描述 |
| - | - |
| `workspaceId` | 用于设定过滤器请求的范围的工作区标识符。 |
| `folderId` | 将结果过滤到单个文件夹。 |
| `folderIds` | 将结果过滤到多个文件夹的重复参数。 |
| `status` | 按一个或多个电子邮件状态进行过滤的重复参数。 |
| `pageIndex` | 分页结果的页面索引从零开始。 |
| `pageSize` | 每页返回的最大结果数。 |
| `createdBy` | 按创建用户筛选结果。 |
| `createdAtStart` | 返回在此时间戳之上或之后创建的资产。 |
| `createdAtEnd` | 返回在此时间戳或之前创建的资源。 |
| `modifiedBy` | 按上次修改的用户筛选结果。 |
| `modifiedAtStart` | 返回在此时间戳或之后修改的资源。 |
| `modifiedAtEnd` | 返回在此时间戳或之前修改的资源。 |
| `name` | 按电子邮件名称筛选结果。 |
| `sortKey` | 选择用于排序结果的字段。 |
| `sortOrder` | 设置排序方向。 |
| `isCreatedByMe` | 将结果限制为当前用户创建的资源。 |
| `isModifiedByMe` | 将结果限制到当前用户上次修改的资源。 |
| `templateId` | 按模板标识符过滤结果。 |
| `scriptEngine` | 按脚本引擎配置筛选结果。 |
| `isValueNonNullable` | 根据值是否不可为空进行筛选。 |
| `includeArchived` | 结果中包含已存档的资产。 |

#### 请求

```http
GET /rest/asset/v2/email/filter?workspaceId=1001&name=Spring%20Launch&status=draft&status=approved&pageIndex=0&pageSize=20
```

#### 响应

```json
{
  "success": true,
  "warnings": [],
  "errors": [],
  "requestId": "5dd1#1900ab13011",
  "result": [
    {
      "id": "1017",
      "name": "Spring Launch Email",
      "status": "draft"
    }
  ]
}
```

当您需要按名称查找电子邮件时，请使用`name`参数。

## 创建

通过发送JSON有效负载创建电子邮件。 需要`name`、`appData`和`headers`。 `headers.subject`是必需的，并且`appData`必须至少包括`folderId`、`workspaceId`或`programId`之一。

### 请求

```http
POST /rest/asset/v2/email
Content-Type: application/json
```

```json
{
  "name": "Spring Launch Email",
  "description": "Main announcement email",
  "appData": {
    "workspaceId": "1001",
    "folderId": "2002",
    "editorType": "email"
  },
  "headers": {
    "subject": "Introducing the Spring Launch",
    "fromName": "Marketing Team",
    "fromEmail": "marketing@example.com",
    "replyEmail": "reply@example.com",
    "preheader": "See what changed this quarter",
    "ccEmails": [
      "owner@example.com"
    ]
  },
  "settings": {
    "isOperational": false,
    "isTextOnly": false,
    "isWebPageView": true,
    "enableUrlTracking": true
  },
  "templateId": "3003"
}
```

### 响应

```json
{
  "success": true,
  "warnings": [],
  "errors": [],
  "requestId": "238c#1900ab1313f",
  "result": [
    {
      "id": "1017",
      "name": "Spring Launch Email",
      "status": "draft"
    }
  ]
}
```

请求正文可能还包含`data`、`editorContext`、`themeId`、`appType`和`status`。

### 创建字段

* `appData`标识电子邮件的创建位置以及编辑方式。
* `headers`包含邮件标头值，包括主题、发件人、回复地址、邮件标头和可选的CC收件人。
* `settings`控制操作和渲染行为，如纯文本模式、网页视图和URL跟踪。
* `templateId`标识创建电子邮件时使用的电子邮件模板。

## 更新

按资产ID更新电子邮件。 请求正文使用`UpdateEmailRequest`架构，并且所有属性都是可选的，因此您只能发送要更改的字段。

### 请求

```http
POST /rest/asset/v2/email/{id}/update
Content-Type: application/json
```

```json
{
  "description": "Updated announcement email",
  "headers": {
    "subject": "Spring Launch Is Live",
    "preheader": "Read the latest release notes"
  },
  "settings": {
    "enableUrlTracking": true,
    "isWebPageView": true
  }
}
```

### 响应

```json
{
  "success": true,
  "warnings": [],
  "errors": [],
  "requestId": "9fd3#1900ab13210",
  "result": [
    {
      "id": "1017"
    }
  ]
}
```

## 管理状态

电子邮件使用草稿和批准的生命周期。 使用状态转换端点可批准电子邮件、取消批准电子邮件、放弃草稿或创建新草稿。

有效的`action`值为：

* `approve`
* `unapprove`
* `discard`
* `create_draft`

### 请求

```http
POST /rest/asset/v2/email/state/transition
Content-Type: application/json
```

### 响应

```json
{
  "contentId": "1017",
  "action": "approve"
}
```

## 克隆

使用克隆端点创建现有电子邮件的副本。

### 请求

```http
POST /rest/asset/v2/email/clone
Content-Type: application/json
```

### 响应

```json
{
  "assetId": "1017",
  "newAsset": {
    "name": "Spring Launch Email Copy",
    "description": "Cloned from Spring Launch Email"
  }
}
```

## 删除

按资产ID删除电子邮件。

### 请求

```http
POST /rest/asset/v2/email/{id}/delete
Content-Type: application/json
```

此端点采用路径中的资源`id`，并且不定义请求正文。

## 使用者

使用`usedby`端点检索引用给定电子邮件的资源。

### 请求

```http
POST /rest/asset/v2/email/usedby
Content-Type: application/json
```

### 响应

```json
{
  "assetId": "1017",
  "pageIndex": 0,
  "pageSize": 20,
  "type": "all"
}
```

## 注释

查询端点返回资源的元数据。 对可用字段的完整架构和任何特定于环境的属性使用端点引用。
