---
title: 片段
feature: REST API
description: 使用Marketo Asset REST API查询、创建、更新、克隆、删除、批准和检查片段的依赖项。
exl-id: 9dd532d1-1dd7-4581-86dd-1943fab66cbb
source-git-commit: 0e0a3e5a08e81f349044cbc327d1aba963ab30e4
workflow-type: tm+mt
source-wordcount: '272'
ht-degree: 9%

---

# 片段

片段是可重复使用的内容资产，可由其他资产（如电子邮件）引用。

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

您可以按资源ID或使用过滤器端点检索片段元数据。

### 按ID

#### 请求

```text
GET /rest/asset/v2/fragment/{id}
```

#### 响应

```json
{
  "success": true,
  "warnings": [],
  "errors": [],
  "requestId": "fa0f#1900ab15001",
  "result": [
    {
      "id": "83",
      "name": "Hero Banner Fragment",
      "description": "Reusable hero block",
      "status": "approved"
    }
  ]
}
```

### 筛选条件

过滤器端点支持在工作区中搜索，以及使用其他查询参数缩小结果范围。 `workspaceId`为必填项。

todo：使其成为表
支持的筛选器包括`folderId`、重复`folderIds`、重复`status`、`pageIndex`、`pageSize`、`createdBy`、`createdAtStart`、`createdAtEnd`、`modifiedBy`、`modifiedAtStart`、`modifiedAtEnd`、`name`、`fragmentType`、`sortKey`、`sortOrder`、`isCreatedByMe`、`isModifiedByMe`、`scriptEngine`、`isValueNonNullable`和`includeArchived`。

#### 请求

```text
GET /rest/asset/v2/fragment/filter?workspaceId=1001&fragmentType=email&pageIndex=0&pageSize=20
```

#### 响应

```json
{
  "success": true,
  "warnings": [],
  "errors": [],
  "requestId": "f9cc#1900ab1504a",
  "result": [
    {
      "id": "83",
      "name": "Hero Banner Fragment",
      "status": "approved"
    }
  ]
}
```

需要按名称查找片段时使用`name`参数。

## 创建

通过发送JSON有效负载创建片段。 需要`name`、`appData`和`settings`。 `settings`必须包括`fragmentType`和`supportedChannels`。

### 请求

```text
POST /rest/asset/v2/fragment
Content-Type: application/json
```

```json
{
  "name": "Hero Banner Fragment",
  "description": "Reusable hero block",
  "appData": {
    "workspaceId": "1001",
    "folderId": "395",
    "editorType": "fragment"
  },
  "settings": {
    "fragmentType": "email",
    "fragmentSubType": "html",
    "supportedChannels": [
      "email"
    ]
  }
}
```

### 响应

```json
{
  "success": true,
  "warnings": [],
  "errors": [],
  "requestId": "bd57#1900ab1509d",
  "result": [
    {
      "id": "13",
      "name": "Hero Banner Fragment",
      "status": "draft"
    }
  ]
}
```

请求正文可能还包含`data`、`editorContext`、`themeId`、`appType`和`status`。

### 创建字段

* `appData`标识片段的存储位置以及编辑方式。
* `settings.fragmentType`标识片段类别，如电子邮件片段。
* `settings.fragmentSubType`可用于进一步定义片段格式。
* `settings.supportedChannels`列出了可以使用片段的渠道。

## 更新

按资产ID更新片段。

### 请求

```text
POST /rest/asset/v2/fragment/{id}/update
Content-Type: application/json
```

```json
{
  "name": "Hero Banner Fragment v2",
  "description": "Updated reusable hero block",
  "settings": {
    "fragmentSubType": "html",
    "supportedChannels": [
      "email"
    ]
  }
}
```

### 响应

```json
{
  "success": true,
  "warnings": [],
  "errors": [],
  "requestId": "73d9#1900ab150f0",
  "result": [
    {
      "id": "13"
    }
  ]
}
```

## 管理状态

片段使用草稿和批准的生命周期。 使用状态转换端点可批准片段、取消批准片段、放弃草稿或创建新草稿。

有效的`action`值为：

* `approve`
* `unapprove`
* `discard`
* `create_draft`

### 请求

```text
POST /rest/asset/v2/fragment/state/transition
Content-Type: application/json
```

### 响应

```json
{
  "contentId": "13",
  "action": "approve"
}
```

## 克隆

使用克隆端点创建现有片段的副本。

### 请求

```text
POST /rest/asset/v2/fragment/clone
Content-Type: application/json
```

### 响应

```json
{
  "assetId": "13",
  "newAsset": {
    "name": "Hero Banner Fragment Copy",
    "description": "Cloned fragment"
  }
}
```

## 删除

按资产ID删除片段。

### 请求

```text
POST /rest/asset/v2/fragment/{id}/delete
Content-Type: application/json
```

此端点采用路径中的片段ID，并且不定义请求正文。

## 使用者

使用`usedby`端点检索引用给定片段的资源。

### 请求

```text
POST /rest/asset/v2/fragment/usedby
Content-Type: application/json
```

### 响应

```json
{
  "assetId": "13",
  "pageIndex": 0,
  "pageSize": 20,
  "type": "all"
}
```
