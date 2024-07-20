---
title: 文件夹
feature: REST API
description: 使用Marketo API处理文件夹。
exl-id: 4b55c256-ef0a-42b4-9548-ff8a4106f064
source-git-commit: 66add4c38d0230c36d57009de985649bb67fde3e
workflow-type: tm+mt
source-wordcount: '1008'
ht-degree: 0%

---

# 文件夹

[文件夹终结点引用](https://developer.adobe.com/marketo-apis/api/asset/#tag/Folders)

文件夹是Marketo中的核心组织资源，所有其他类型的资源至少具有一个文件夹作为父级。 此父文件夹可以是纯组织文件夹，也可以是与其他资产类型具有功能关系并且可以是其他资产父级的项目群。 可以通过API创建、查询、更新和删除文件夹，还可以允许检索其内容的列表。 虽然程序可以通过查询文件夹API返回，但创建、更新和删除程序必须通过程序API执行。

## 查询

查询文件夹遵循ID为[、名称为](https://developer.adobe.com/marketo-apis/api/asset/#tag/Folders/operation/getFolderByNameUsingGET)且浏览次数为[的[和](https://developer.adobe.com/marketo-apis/api/asset/#tag/Folders/operation/getFolderUsingGET)的标准查询类型。](https://developer.adobe.com/marketo-apis/api/asset/#tag/Folders/operation/getFolderByIdUsingGET)

### 按Id

```
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

type参数是必需的，并且必须为“文件夹”或“程序”之一。  类型指示对文件夹的查找是针对文件夹ID还是项目ID完成的。 对于此端点，结果数组中只返回单个记录。 请注意响应中的folderType参数。 这可能表示许多不同类型的文件夹。 “Marketo活动”文件夹具有一种类型的营销文件夹或程序，其中可以包含多种不同类型的资源，而Design Studio文件夹具有一种对应于它们可以保留的资源类型的类型。 例如， folderType为“电子邮件”的文件夹可能仅包含电子邮件或其他子文件夹，这些子文件夹可能具有folderType为“电子邮件”或“电子邮件模板”。 类型可能包括：

- 电子邮件
- 电子邮件模板
- 登陆页面
- 登陆页面模板
- 代码片段
- 文件

### 按名称

[还允许按名称](https://developer.adobe.com/marketo-apis/api/asset/#tag/Folders/operation/getFolderByNameUsingGET)查询。 按名称查询终结点将“名称”作为唯一必需的参数。 Name对实例中文件夹的name字段执行完全匹配的字符串，并返回与该名称匹配的每个文件夹的结果。 它还具有“类型”的可选查询参数，可以是“文件夹”或“程序”、“根”要搜索的文件夹的ID，或“工作区”要搜索的工作区的名称。 如果设置了root参数，则还必须设置type参数。

```
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

按名称搜索时，请务必注意，Marketing Activities和Design Studio都是其自己的根文件夹，因此可以按名称检索它们，并用于在目标实例中遍历文件夹层次结构的其余部分。

### 浏览

也可以批量](https://developer.adobe.com/marketo-apis/api/asset/#tag/Folders/operation/getFolderUsingGET)检索文件夹[。 “root”参数可用于指定要在其中执行查询的父文件夹，并将格式化为嵌入为查询参数值的JSON对象。 Root有两个成员：

1. id — 文件夹或程序的ID。
1. 类型 — 文件夹或程序，具体取决于要浏览的根文件夹的类型。

如果根文件夹未知，或者其目的是检索给定区域中的所有文件夹，则可以将根指定为“营销活动”、“Design Studio”或“潜在客户数据库”区域。 可以通过[按名称获取文件夹](https://developer.adobe.com/marketo-apis/api/asset/#tag/Folders/operation/getFolderByNameUsingGET) API来检索其中每个ID，并指定所需区域的名称。

与其他批量资源检索端点一样，offset和maxReturn是分页的可选参数。   其他可选参数包括：

- workSpace — 要过滤的工作区的名称。
- maxDepth — 在文件夹层次结构中遍历的最大级别数。 如果设置为0，则仅返回在root中指定的文件夹。 如果未指定，则默认值为2。

```
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

文件夹响应结构中的许多内容都可以自行解释，但有一些字段值得单独注意。 `folderId`和父字段是JSON对象，其中包括文件夹本身的显式ID和类型。 此类型是API在查询、根和父参数中使用的类型，以确保在文件夹和程序类型的文件夹之间进行正确的描述。 `folderType`反映了该文件夹的使用情况，该文件夹可能是“营销文件夹”、“项目”、“电子邮件”、“电子邮件模板”、“登陆页面”、“登陆页面模板”、“代码片段”、“图像”、“区域”或“文件”之一。  营销文件夹和项目群类型指示它们存在于营销活动中，并可包含多种类型的资产。 其他类型指示它们可能仅包含该类型的资源、子文件夹以及该类型的模板版本（如果适用）。 类型Zone表示在营销活动中找到的根级文件夹。

文件夹的路径在文件夹树中显示其层次结构，类似于Unix样式的路径。 路径中的第一个条目将始终为“营销活动”或“设计工作室”。 如果目标实例具有工作区，则路径中的第二个条目将是拥有的工作区的名称。 `url`字段显示指定实例中资产的显式URL。 这不是通用链接，必须经过用户身份验证才能正常工作。 `isSystem`指示该文件夹是否为系统文件夹。 如果将此值设置为true，则文件夹本身为只读，不过可以创建其子文件夹。

## 创建和更新

[创建文件夹](https://developer.adobe.com/marketo-apis/api/asset/#tag/Folders/operation/createFolderUsingPOST)非常简单，并且使用application/x-www-form-urlencodedPOST执行，该模板具有两个必需的参数：“name”（名称）、“string”（字符串）和“parent”（父级），用于在其中创建文件夹，该文件夹是一个嵌入式JSON对象，具有两个成员：ID和类型：“Folder”（文件夹）或“Program”（程序），具体取决于目标文件夹的类型。 也可选择包括一个字符串“description”，其长度最多为2000个字符。

```
POST /rest/asset/v1/folders.json
```

```
Content-Type: application/x-www-form-urlencoded
```

```
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

文件夹的更新通过单独的端点进行，描述、名称和`isArchive`是用于更新的可选参数。 如果更新更改了`isArchive`，这会导致在Marketo UI中存档文件夹（如果更改为true），或取消存档文件夹（如果更改为false）。 无法使用此API更新程序。

```
POST /rest/asset/v1/folder/{id}.json
```

```
Content-Type: application/x-www-form-urlencoded
```

```
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

如果单个文件夹为空（即不包含任何资源或子文件夹），则可以对它们进行删除。 如果文件夹属于Program类型，或者将isSystem字段设置为true，则无法使用此API将其删除。

```
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
