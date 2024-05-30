---
title: "标记"
feature: REST API, Tags
description: “在Marketo中管理程序的标记。”
source-git-commit: 8c1ffb6db05da49e7377b8345eeb30472ad9b78b
workflow-type: tm+mt
source-wordcount: '205'
ht-degree: 1%

---


# 标记

[标记端点引用](https://developer.adobe.com/marketo-apis/api/asset/#tag/Tags)

标记是用户为项目定义的字段。 每个标记可能适用于一个或多个程序类型，并且可以是必需或可选的，具体取决于标记的定义方式。 标记还可以提供必须从中选择以供使用的允许值的列表。

## 查询

标记使用标准资源模式进行查询，但没有按ID的端点。 仅当按名称查询标记时，才会返回标记的可允许值列表。

### 获取标记

```
GET /rest/asset/v1/tagTypes.json
```

```json
{
    "success": true,
    "warnings": [],
    "errors": [],
    "requestId": "1488a#1504ecfccf8",
    "result": [
        {
            "tagType": "AAA1 Required Tag Type",
            "applicableProgramTypes": "[program,email_batch,nurture,event,webinar]",
            "required": true
        },
        {
            "tagType": "AAA2 Required Event Tag Type",
            "applicableProgramTypes": "[event]",
            "required": true
        },
        {
            "tagType": "AAA3 Not Required Tag Type",
            "applicableProgramTypes": "[program,email_batch,nurture,event,webinar]",
            "required": false
        }
    ]
}
```

### 按名称

```
GET /rest/asset/v1/tagType/byName.json?name=AAA1 Required Tag Type
```

```json
{
    "success": true,
    "warnings": [],
    "errors": [],
    "requestId": "8a44#1504ed0da2f",
    "result": [
        {
            "tagType": "AAA1 Required Tag Type",
            "applicableProgramTypes": "[program,email_batch,nurture,event,webinar]",
            "required": true,
            "allowableValues": "[AAA1 RT1, AAA1 RT2, AAA1 RT3, AAA1 RT4]"
        }
    ]
}
```

## 更新

此 [更新项目标记](https://developer.adobe.com/marketo-apis/api/asset/#tag/Programs/operation/updateProgramUsingPOST) 端点允许您更新给定标记类型的值。 端点需要一个 `id` 和 `tagType` 路径参数，用于指定程序id和要更新的标记类型。 A `tagValue` 查询参数用于为标记类型指定新值。 所有参数都是必需的。

```
POST /rest/asset/v1/program/{id}/tag/{tagType}.json?tagValue=David
```

```json
{
    "success": true,
    "errors": [],
    "requestId": "fd84#17f84a885a6",
    "warnings": [],
    "result": [
        {
            "id": 1067
        }
    ]
}
```

可以使用批量更新标记 [更新项目元数据](https://developer.adobe.com/marketo-apis/api/asset/#tag/Programs/operation/updateProgramUsingPOST) 端点。 可以找到示例 [此处](programs.md#update).

## 删除

此 [删除项目标记](https://developer.adobe.com/marketo-apis/api/asset/#tag/Programs/operation/deleteProgramUsingPOST) 端点允许您删除不需要的标记类型。 端点采用 `id` 和 `tagType` 路径参数，用于指定程序id和要删除的标记类型。

```
POST /rest/asset/v1/program/{id}/tag/{tagType}/delete.json
```

```json
{
    "success": true,
    "errors": [],
    "requestId": "d998#17f84ad36a7",
    "warnings": [],
    "result": [
        {
            "id": 1067
        }
    ]
}
```
