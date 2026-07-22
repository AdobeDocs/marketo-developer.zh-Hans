---
title: 令牌
feature: REST API, Tokens
description: 使用Asset REST API管理Marketo我的令牌。 查看支持的数据类型，按文件夹或程序获取，通过表单编码的POST创建或更新，并按名称删除。
exl-id: 4f8d87d7-ba2a-4c90-8b39-4d20679d404a
TQID: https://experienceleague.adobe.com/uqOpu2vDuiQiZhILKuxZJQGadd0K14zwIaAdmNfK1-I
product_v2:
  - id: b27e5950-9033-45ac-9f86-eb22e567f615
role_v2:
  - id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
source-git-commit: 3e6d310c5aec1a3435424fb122b71d825db5af0e
workflow-type: tm+mt
source-wordcount: 290
ht-degree: 3%

---

# 令牌

[令牌端点引用](https://developer.adobe.com/marketo-apis/api/asset#tag/Tokens)

令牌是Marketo在运行时用其他数据替换的字符串。 该API只能编辑我的令牌，这些令牌是文件夹或程序的本地子令牌。

使用令牌API读取、创建、更新和删除我的令牌。

## 数据类型

可以使用以下数据类型创建令牌：

| 类型 | 描述 |
| --- | --- |
| 日期 | 格式为“yyyy-MM-dd”的日期值 |
| 数字 | 整数或浮点数 |
| 富文本 | HTML字符串 |
| 分数 | 带符号的32位整数 |
| sfdc营销活动 | 用于Salesforce campaign管理集成 |
| 文本 | 文本字符串 |

在创建令牌时，API仅支持这些数据类型。

## 查询

[按文件夹ID获取令牌](https://developer.adobe.com/marketo-apis/api/asset#tag/Tokens/operation/getTokensByFolderIdUsingGET)将程序或文件夹的ID作为路径参数。 使用`folderType`参数指定类型。

```http
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

[创建令牌](https://developer.adobe.com/marketo-apis/api/asset#tag/Tokens/operation/addTokenTOFolderUsingPOST)端点创建令牌或使用提交的值更新现有令牌。 令牌属于文件夹或程序。

`id`路径参数标识父文件夹。 需要`name`、`type`、`value`和`folderType`参数。 将数据作为POST `x-www-form-urlencoded`传递，而不是作为JSON传递。 令牌`name`不能超过50个字符。

```http
POST /rest/asset/v1/folder/{id}/tokens.json
```

```text
Content-Type: application/x-www-form-urlencoded
```

```text
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

[按名称删除令牌](https://developer.adobe.com/marketo-apis/api/asset#tag/Tokens/operation/deleteTokenByNameUsingPOST)将程序或文件夹的ID作为路径参数。 使用`folderType`指定类型。

需要父文件夹、令牌`name`和令牌`type`。 将数据作为POST `x-www-form-urlencoded`传递，而不是作为JSON传递。

```http
POST /rest/asset/v1/folder/{id}/tokens/delete.json
```

```text
Content-Type: application/x-www-form-urlencoded
```

```text
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
