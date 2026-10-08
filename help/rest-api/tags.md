---
title: 标记
feature: REST API, Tags
description: 查询标记类型、按名称获取允许的值、通过REST Asset API更新或删除Marketo中的程序标记，以及请求示例。
exl-id: 64731d1a-a749-4d6f-b336-16c733d002f0
TQID: 'https://experienceleague.adobe.com/zjdyfoofVWytE0Q-K4lk598jmleTSFOD7tSRqeAHsjk'
product_v2:
  - id: b27e5950-9033-45ac-9f86-eb22e567f615
    internal-label: Marketo Engage
feature_v2:
  - id: e64968b2-4ee5-47f9-8cae-0588f184b9eb
    internal-label: Programs
  - id: dca84292-69e9-4116-a575-667d31fa060d
    internal-label: APIs
  - id: d1d0a9cd-295d-4976-8c39-ddae266f240e
    internal-label: Administration
subfeature_v2:
  - id: cf1396d8-ab85-4e93-b35d-d9b573024abf
    internal-label: REST APIs
  - id: eabd8318-c438-41ef-8756-bedd6f38b8fc
    internal-label: Tag administration
role_v2:
  - id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
    internal-label: Admin
topic_v2:
  - id: a004cc84-67b9-4a33-a3a7-8ec7273ef4dc
    internal-label: Metadata
source-git-commit: 5620f050ba834be3f6648650b5cc7d781ea394bf
workflow-type: tm+mt
source-wordcount: '221'
ht-degree: 2%
---
# 标记

[标记端点引用](https://developer.adobe.com/marketo-apis/api/asset#tag/Tags)

标记是用户为项目定义的字段。 标记可以应用于一个或多个程序类型，可以是必需或可选的。 标记还可以定义用户必须从中进行选择的允许值列表。

## 查询

使用标准资源模式查询标记。 标记没有“按ID”端点。 要检索标记的允许值，请按名称查询标记。

### 获取标记

```http
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

```http
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

使用[更新程序标记](https://developer.adobe.com/marketo-apis/api/asset#operation/updateProgramUsingPOST)端点更新标记类型的值。 所有参数都是必需的：

- `id`路径参数指定程序ID。
- `tagType`路径参数指定要更新的标记类型。
- `tagValue`查询参数指定新值。

```http
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

要更新多个标记，请使用[更新程序元数据](https://developer.adobe.com/marketo-apis/api/asset#operation/updateProgramUsingPOST)端点。 请参阅[程序更新部分](programs.md#update)中的示例。

## 删除

使用[删除程序标记](https://developer.adobe.com/marketo-apis/api/asset#operation/deleteProgramUsingPOST)端点删除非必需的标记类型。 `id` path参数指定程序ID，`tagType` path参数指定要删除的标记类型。

```http
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
