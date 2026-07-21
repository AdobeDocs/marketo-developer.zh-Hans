---
title: 文件夹
feature: REST API
description: Marketo REST API指南，适用于包含创建、更新、删除、按ID和名称查询、使用根批量浏览、工作区、maxDepth和分页的文件夹。
exl-id: 4b55c256-ef0a-42b4-9548-ff8a4106f064
TQID: https://experienceleague.adobe.com/OxCNdy8qW6jwq8u57RF9mqVKPVvH99UmuiOBjFprHCM
product_v2: id: b27e5950-9033-45ac-9f86-eb22e567f615
feature_v2: id: b0bb9048-d951-48d8-8232-45cf248a7e27id: c5f60233-d5ea-4453-a799-0ad258b4d399id: d65b4a73-87a3-4d56-b638-74e74d9939ceid: e64968b2-4ee5-47f9-8cae-0588f184b9eb
role_v2: id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
source-git-commit: 3e6d310c5aec1a3435424fb122b71d825db5af0e
workflow-type: tm+mt
source-wordcount: 806
ht-degree: 1%

---

# 文件夹

[文件夹端点引用](https://developer.adobe.com/marketo-apis/api/asset#tag/Folders)

文件夹是Marketo中的核心组织资源。 每个其他资源类型都至少有一个父资源是文件夹或项目群。 文件夹完全是组织性的，而项目群与其他资产类型具有功能关系，也可以包含资产。

使用文件夹API创建、查询、更新和删除文件夹或检索其内容。 文件夹查询可以返回程序，但必须使用程序API创建、更新或删除程序。

## 查询

文件夹支持标准资源查询模式：[按ID](https://developer.adobe.com/marketo-apis/api/asset#tag/Folders/operation/getFolderByIdUsingGET)、[按名称](https://developer.adobe.com/marketo-apis/api/asset#tag/Folders/operation/getFolderByNameUsingGET)和[浏览](https://developer.adobe.com/marketo-apis/api/asset#tag/Folders/operation/getFolderUsingGET)。

### 按Id

```http
GET /rest/asset/v1/folder/{id}.json?type=Folder
```

```json
{
    "success": true,
    "warnings": [],
    "errors": [],
    "requestId": "1241b#14e21ca814a",
    "result": [
        {
            "name": "Social Media",
            "description": null,
            "createdAt": "2011-03-04T17:01:32Z+0000",
            "updatedAt": "2011-03-04T17:01:32Z+0000",
            "url": null,
            "folderId": {
                "id": 341,
                "type": "Folder"
            },
            "folderType": "Email",
            "parent": {
                "id": 11,
                "type": "Folder"
            },
            "path": "/Design Studio/Default/Emails/Social Media",
            "isArchive": false,
            "isSystem": false,
            "accessZoneId": 1,
            "workspace": "Default",
            "id": 341
        }
    ]
}
```

`type`参数是必需的，必须为`Folder`或`Program`。 它确定端点查找的是文件夹ID还是程序ID。 终结点在结果数组中返回一个记录。

响应`folderType`标识文件夹可以包含的内容。 “营销活动”文件夹具有一种类型的营销文件夹或项目，并且可以包含多种资产类型。 Design Studio文件夹的类型与其可以包含的资产相对应。 例如，电子邮件文件夹可以包含文件夹类型为“电子邮件”或“电子邮件模板”的电子邮件和子文件夹。

文件夹类型包括：

- 电子邮件
- 电子邮件模板
- 登陆页面
- 登陆页面模板
- 代码段
- 文件

### 按名称

按名称](https://developer.adobe.com/marketo-apis/api/asset#tag/Folders/operation/getFolderByNameUsingGET)进行的[查询端点需要`name`，它将对文件夹名称执行完全匹配并返回每个匹配的文件夹。

端点还接受以下可选参数：

- `type`：文件夹类型`Folder`或`Program`。
- `root`：要搜索的文件夹的ID。 如果设置`root`，则还必须设置`type`。
- `workspace`：要搜索的工作区的名称。

```http
GET /rest/asset/v1/folder/byName.json?name=Test%2010%20-%20deverly
```

```json
{
    "success": true,
    "warnings": [],
    "errors": [],
    "requestId": "19#14e1f2f3688",
    "result": [
        {
            "name": "Test 10 - deverly",
            "description": "This is a test",
            "createdAt": "2015-06-23T06:27:04Z+0000",
            "updatedAt": "2015-06-23T06:27:04Z+0000",
            "url": "https://app-abm.marketo.com/#MF1070A1",
            "folderId": {
                "id": 454,
                "type": "FOLDER"
            },
            "folderType": "Marketing Folder",
            "parent": {
                "id": 416,
                "type": "FOLDER"
            },
            "path": "/Marketing Activities/Default/Marketing Programs - deverly/Test 10 - deverly",
            "isArchive": false,
            "isSystem": false,
            "accessZoneId": 1,
            "workspace": "Default",
            "id": 454
        }
    ]
}
```

营销活动和Design Studio是根文件夹。 按名称检索根，然后使用它遍历目标实例中的文件夹层次结构。

### 浏览

您也可以[批量检索文件夹](https://developer.adobe.com/marketo-apis/api/asset#tag/Folders/operation/getFolderUsingGET)。 使用`root`参数指定要查询的父文件夹。 将`root`作为具有两个成员的嵌入JSON对象传递：

1. `id`：文件夹或项目群的ID。
1. `type`： `Folder`或`Program`，具体取决于根文件夹类型。

如果您不知道根文件夹或者希望检索某个区域中的所有文件夹，请使用Marketing Activities、Design Studio或Lead Database根目录。 通过将区域名称传递到[按名称获取文件夹](https://developer.adobe.com/marketo-apis/api/asset#tag/Folders/operation/getFolderByNameUsingGET) API来检索根ID。

与其他批量资源检索端点一样，请使用可选的`offset`和`maxReturn`参数进行分页。 其他可选参数包括：

- `workSpace`：要作为筛选依据的工作区的名称。
- `maxDepth`：文件夹层次结构中要遍历的最大级别数。 值为0仅返回`root`指定的文件夹。 默认值为2。

```http
GET /rest/asset/v1/folders.json?root={"id":14,"type":"Folder"}
```

```json
{
    "success": true,
    "warnings": [],
    "errors": [],
    "requestId": "9bd8#14e1f49047c",
    "result": [
        {
            "name": "Marketing Activities",
            "description": "Root node for the Marketing Activities app area",
            "createdAt": "2010-03-27T18:27:45Z+0000",
            "updatedAt": "2010-03-27T18:27:45Z+0000",
            "url": null,
            "folderId": {
                "id": 14,
                "type": "Folder"
            },
            "folderType": "Zone",
            "parent": null,
            "path": "/Marketing Activities",
            "isArchive": false,
            "isSystem": true,
            "accessZoneId": 1,
            "workspace": "Default",
            "id": 14
        },
        {
            "name": "Default",
            "description": "Root node of the Marketing activities Default",
            "createdAt": "2010-03-27T18:27:45Z+0000",
            "updatedAt": "2010-03-27T18:27:45Z+0000",
            "url": null,
            "folderId": {
                "id": 15,
                "type": "Folder"
            },
            "folderType": "Zone",
            "parent": {
                "id": 14,
                "type": "Folder"
            },
            "path": "/Marketing Activities/Default",
            "isArchive": false,
            "isSystem": true,
            "accessZoneId": 1,
            "workspace": "Default",
            "id": 15
        },
        {
            "name": "Archive",
            "description": "",
            "createdAt": "2010-03-27T18:28:17Z+0000",
            "updatedAt": "2010-03-27T18:28:17Z+0000",
            "url": "https://app-abm.marketo.com/#MF157A1",
            "folderId": {
                "id": 310,
                "type": "Folder"
            },
            "folderType": "Marketing Folder",
            "parent": {
                "id": 15,
                "type": "Folder"
            },
            "path": "/Marketing Activities/Default/Archive",
            "isArchive": false,
            "isSystem": false,
            "accessZoneId": 1,
            "workspace": "Default",
            "id": 310
        }
    ]
}
```

## 响应结构

`folderId`和`parent`字段是包含文件夹ID和类型的JSON对象。 API在查询、`root`和`parent`参数中使用此类型来区分文件夹和程序文件夹类型。

`folderType`字段描述如何使用文件夹。 其值可以是营销文件夹、项目、电子邮件、电子邮件模板、登陆页面、登陆页面模板、代码片段、图像、区域或文件。 营销活动中存在营销文件夹和项目，它们可以包含多种资产类型。 其他文件夹类型仅包含相应的资源类型、子文件夹以及该资源类型的模板版本（如果适用）。 区域表示营销活动中的根级别文件夹。

文件夹`path`将其层次结构显示为Unix样式路径。 第一个条目始终是营销活动或设计工作室。 如果实例具有工作区，则第二个条目是拥有的工作区名称。

`url`字段包含指定实例的资源URL。 它不是通用链接，需要用户身份验证。 `isSystem`字段指示文件夹是否为只读系统文件夹。 您可以在系统文件夹下创建子文件夹。

## 创建和更新

要[创建文件夹](https://developer.adobe.com/marketo-apis/api/asset#tag/Folders/operation/createFolderUsingPOST)，请使用以下参数发送`application/x-www-form-urlencoded` POST请求：

- `name`：包含文件夹名称的必需字符串。
- `parent`：包含`id`和`type`的必需嵌入JSON对象。 类型是`Folder`或`Program`，具体取决于父级。
- `description`：最多2000个字符的可选字符串。

```http
POST /rest/asset/v1/folders.json
```

```text
Content-Type: application/x-www-form-urlencoded
```

```text
parent={"id":416,"type":"Folder"}&name=Test 10 - deverly&description=This is a test
```

```json
{
    "success": true,
    "warnings": [],
    "errors": [],
    "requestId": "111be#14e1f193e31",
    "result": [
        {
            "name": "Test 10 - deverly",
            "description": "This is a test",
            "createdAt": "2015-06-23T06:27:04Z+0000",
            "updatedAt": "2015-06-23T06:27:04Z+0000",
            "url": "https://app-abm.marketo.com/#MF1070A1",
            "folderId": {
                "id": 454,
                "type": "FOLDER"
            },
            "folderType": "Marketing Folder",
            "parent": {
                "id": 416,
                "type": "FOLDER"
            },
            "path": "/Marketing Activities/Default/Test 10 - deverly",
            "isArchive": false,
            "isSystem": false,
            "accessZoneId": 1,
            "workspace": "Default",
            "id": 454
        }
    ]
}
```

使用更新终结点更改可选的`description`、`name`或`isArchive`参数。 将`isArchive`设置为`true`会在Marketo UI中存档文件夹。 将其设置为`false`会从存档中删除文件夹。

无法使用此API更新程序。

```http
POST /rest/asset/v1/folder/{id}.json
```

```text
Content-Type: application/x-www-form-urlencoded
```

```sql
type=Folder&description=This is a test (update 01)
```

```json
{
    "success": true,
    "warnings": [],
    "errors": [],
    "requestId": "c5b2#14e1f3954bf",
    "result": [
        {
            "name": "Learning - deverly",
            "description": "This is a test (update 01)",
            "createdAt": "2015-03-17T00:17:02Z+0000",
            "updatedAt": "2015-06-23T07:02:07Z+0000",
            "url": "https://app-abm.marketo.com/#MF1044A1",
            "folderId": {
                "id": 407,
                "type": "FOLDER"
            },
            "folderType": "Marketing Folder",
            "parent": {
                "id": 15,
                "type": "FOLDER"
            },
            "path": "/Marketing Activities/Default/Learning - deverly",
            "isArchive": false,
            "isSystem": false,
            "accessZoneId": 1,
            "workspace": "Default",
            "id": 407
        }
    ]
}
```

### 删除

仅当单个文件夹不包含资源或子文件夹时，您才可以将其删除。 无法使用此API删除`isSystem`字段为`true`的程序或文件夹。

```http
POST /rest/asset/v1/folder/{id}/delete.json
```

```json
{
    "success": true,
    "warnings": [],
    "errors": [],
    "requestId": "4180#14e1f3fc017",
    "result": [
        {
            "id": 453
        }
    ]
}
```
