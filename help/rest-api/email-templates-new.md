---
title: 电子邮件模板
feature: REST API
description: 使用Marketo Asset REST API查询、创建、更新、克隆、删除、批准和检查电子邮件模板的依赖项。
exl-id: 50bb0047-d6ea-4c94-a900-18c37b17a147
source-git-commit: 59684e1c5a8082ad12f1e4bfc854c0d2dde35d2a
workflow-type: tm+mt
source-wordcount: '292'
ht-degree: 9%

---

# 电子邮件模板

[电子邮件模板端点引用](https://developer.adobe.com/marketo-apis/api/asset#tag/Email-Templates)

电子邮件模板定义了创建电子邮件时使用的结构和可重用布局。

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

您可以按资源ID或使用过滤器端点检索电子邮件模板元数据。

### 按ID

#### 请求

```http
GET /rest/asset/v2/emailtemplate/{id}
```

#### 响应

```json
{
  "success": true,
  "warnings": [],
  "errors": [],
  "requestId": "14f9e#1900ab14001",
  "result": [
    {
      "id": "19",
      "name": "Base Newsletter Template",
      "description": "Core responsive template",
      "status": "draft"
    }
  ]
}
```

### 筛选条件

过滤器端点支持在工作区中搜索，以及使用其他查询参数缩小结果范围。 `workspaceId`为必填项。

支持的筛选器包括`folderId`、重复`folderIds`、重复`status`、`pageIndex`、`pageSize`、`createdBy`、`createdAtStart`、`createdAtEnd`、`modifiedBy`、`modifiedAtStart`、`modifiedAtEnd`、`name`、`sortKey`、`sortOrder`、`isCreatedByMe`、`isModifiedByMe`、`scriptEngine`、`isValueNonNullable`和`includeArchived`。

#### 请求

```http
GET /rest/asset/v2/emailtemplate/filter?workspaceId=1001&name=Newsletter&pageIndex=0&pageSize=20
```

#### 响应

```json
{
  "success": true,
  "warnings": [],
  "errors": [],
  "requestId": "33c4#1900ab1402f",
  "result": [
    {
      "id": "19",
      "name": "Base Newsletter Template",
      "status": "draft"
    }
  ]
}
```

当您需要按名称查找模板时，请使用`name`参数。

## 创建

通过发送JSON有效负载创建电子邮件模板。 `name`和`appData`是必需的。 `appData`必须至少包括`folderId`或`workspaceId`。

### 请求

```http
POST /rest/asset/v2/emailtemplate
Content-Type: application/json
```

```json
{
  "name": "Base Newsletter Template",
  "description": "Core responsive template",
  "appData": {
    "workspaceId": "1001",
    "folderId": "15",
    "editorType": "emailTemplate"
  },
  "themeId": "42"
}
```

### 响应

```json
{
  "success": true,
  "warnings": [],
  "errors": [],
  "requestId": "a99f#1900ab1407e",
  "result": [
    {
      "id": "1022",
      "name": "Base Newsletter Template",
      "status": "draft"
    }
  ]
}
```

请求正文可能还包含`data`、`editorContext`、`appType`和`status`。

### 创建字段

`appData`标识模板的存储位置及其编辑方式。

`themeId`可用于将主题与模板关联（如果适用）。

## 更新

按资产ID更新模板。

### 请求

```http
POST /rest/asset/v2/emailtemplate/{id}/update
Content-Type: application/json
```

```json
{
  "name": "Base Newsletter Template v2",
  "description": "Updated responsive template",
  "appData": {
    "folderId": "15"
  }
}
```

### 响应

```json
{
  "success": true,
  "warnings": [],
  "errors": [],
  "requestId": "cf10#1900ab140b3",
  "result": [
    {
      "id": "1022"
    }
  ]
}
```

## 管理状态

电子邮件模板使用草稿和批准的生命周期。 使用状态转换端点可批准、取消批准、放弃草稿或创建新草稿。

有效的`action`值为：

- `approve`
- `unapprove`
- `discard`
- `create_draft`

### 请求

```http
POST /rest/asset/v2/emailtemplate/state/transition
Content-Type: application/json
```

### 响应

```json
{
  "contentId": "1022",
  "action": "approve"
}
```

## 克隆

使用克隆端点创建现有模板的副本。

### 请求

```http
POST /rest/asset/v2/emailtemplate/clone
Content-Type: application/json
```

### 响应

```json
{
  "assetId": "1022",
  "newAsset": {
    "name": "Base Newsletter Template Copy",
    "description": "Cloned template"
  }
}
```

## 删除

按资产ID删除模板。

### 请求

```http
POST /rest/asset/v2/emailtemplate/{id}/delete
Content-Type: application/json
```

此端点采用路径中的模板ID，并且不定义请求正文。

## 使用者

使用`usedby`端点检索引用给定模板的资源。

### 请求

```http
POST /rest/asset/v2/emailtemplate/usedby
Content-Type: application/json
```

### 响应

```json
{
  "assetId": "1022",
  "pageIndex": 0,
  "pageSize": 20,
  "type": "all"
}
```

## 注释

查询端点返回资源的元数据。 将端点引用用于完整响应架构和任何特定于环境的属性。
