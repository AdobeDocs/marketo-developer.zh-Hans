---
title: 令牌
feature: REST API, Tokens
description: 在Marketo中管理令牌。
exl-id: 4f8d87d7-ba2a-4c90-8b39-4d20679d404a
source-git-commit: 66add4c38d0230c36d57009de985649bb67fde3e
workflow-type: tm+mt
source-wordcount: '297'
ht-degree: 2%

---

# 令牌

[令牌终结点引用](https://developer.adobe.com/marketo-apis/api/asset/#tag/Tokens)

Marketo中的令牌是类似于快捷键的特殊字符串，在运行时会被替换为单独的数据段。 Marketo中有多种类型的令牌可用，但只能通过API编辑“我的令牌”。 我的令牌是特定文件夹或程序的本地子令牌。 可以通过API读取、创建和删除令牌。

## 数据类型

可以使用以下数据类型创建令牌：

| 类型 | 描述 |
|---------------|----------------------------------------------------|
| 日期 | 格式为“yyyy-MM-dd”的日期值 |
| 数字 | 整数或浮点数 |
| 富文本 | HTML字符串 |
| 分数 | 带符号的32位整数 |
| sfdc营销活动 | 用于Salesforce营销活动管理集成 |
| 文本 | 文本字符串 |


通过API创建令牌时，只能使用这些数据类型。

## 查询

[按文件夹ID获取令牌](https://developer.adobe.com/marketo-apis/api/asset/#tag/Tokens/operation/getTokensByFolderIdUsingGET)将`id`作为程序或文件夹类型的路径参数。 此类型由`folderType`参数指定。

```curl
GET /rest/asset/v1/folder/{id}/tokens.json?folderType=Folder
```

```json
{
    "success": true,
    "warnings": [],
    "errors": [],
    "requestId": "4fbe#14e27fc9bbf",
    "result": [
        {
            "folder": {
                "type": "Folder",
                "value": 416
            },
            "tokens": [
                {
                    "name": "AprilFool - deverly",
                    "type": "date",
                    "value": "2015-04-01",
                    "computedUrl": "https://app-abm.marketo.com/#MF1047C3"
                }
            ]
        }
    ]
}
```

## 创建和更新

[创建令牌](https://developer.adobe.com/marketo-apis/api/asset/#tag/Tokens/operation/addTokenTOFolderUsingPOST)端点创建令牌，或者如果存在令牌，则使用提交的值更新令牌。 令牌是在文件夹或项目群的上下文中创建的。 所需的`id`路径参数是将与令牌关联的文件夹的ID。 `name`、`type`、`value`和`folderType`都是令牌的必需参数。 数据以x-www-form-urlencodedPOST传递，而不是以JSON格式传递。 令牌的`name`字段不能超过50个字符。

```
POST /rest/asset/v1/folder/{id}/tokens.json
```

```
Content-Type: application/x-www-form-urlencoded
```

```
name=April Fools&type=date&value=2015-04-01&folderType=Folder
```

```json
{
    "success": true,
    "warnings": [],
    "errors": [],
    "requestId": "e3c2#14e280db5dc",
    "result": [
        {
            "folder": {
                "type": "Folder",
                "value": 416
            },
            "tokens": [
                {
                    "name": "April Fools",
                    "type": "date",
                    "value": "2015-04-01",
                    "computedUrl": "https://app-abm.marketo.com/#MF1047C3"
                }
            ]
        }
    ]
}
```

## 删除

[按名称删除令牌](https://developer.adobe.com/marketo-apis/api/asset/#tag/Tokens/operation/deleteTokenByNameUsingPOST)将ID作为程序或文件夹类型的路径参数。 此类型由`folderType`参数指定。 根据令牌的父文件夹、`name`和令牌的`type`删除令牌，每个令牌都是必需的。 数据以x-www-form-urlencodedPOST传递，而不是以JSON格式传递。

```
POST /rest/asset/v1/folder/{id}/tokens/delete.json
```

```
Content-Type: application/x-www-form-urlencoded
```

```
name=AprilFool - deverly&type=date&folderType=Program
```

```json
{
    "success": true,
    "warnings": [],
    "errors": [],
    "requestId": "12ed2#14e2800f89c",
    "result": [
        {
            "id": 416
        }
    ]
}
```
