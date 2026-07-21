---
title: 动态内容
feature: REST API, Dynamic Content
description: 使用分段通过REST API配置部分级别的Marketo动态内容，以个性化包含端点和示例的电子邮件、登陆页和代码片段
exl-id: 8ab97624-5fb5-4a41-911f-ec8616dd43c9
TQID: https://experienceleague.adobe.com/MwfPxu74qk0bPZMr6yuxQi--e3gMvP1tXQZ5iMil02o
product_v2: id: b27e5950-9033-45ac-9f86-eb22e567f615
feature_v2: id: c5f60233-d5ea-4453-a799-0ad258b4d399
role_v2: id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
source-git-commit: 3e6d310c5aec1a3435424fb122b71d825db5af0e
workflow-type: tm+mt
source-wordcount: 329
ht-degree: 3%

---

# 动态内容

使用商机分段在以下资源类型中提供动态内容：

- 电子邮件
- 登陆页面
- 代码段

## 概述

动态内容在部分级别运行。 每个部分都可以提供选定分段中区段的变体。

当商机查看资源时，Marketo显示商机区段的变体。 如果潜在客户不符合区段的条件，则Marketo会显示默认内容。

## 示例

此示例使用区域(US)分段来显示针对西南区段中的潜在客户的事件促销。 该区段包括来自加利福尼亚州、内华达州、犹他州、科罗拉多州、亚利桑那州和新墨西哥州的潜在客户。

使用[更新电子邮件内容节](https://developer.adobe.com/marketo-apis/api/asset#tag/Emails/operation/updateEmailComponentContentUsingPOST)端点将ID为`Q1-promotion-banner`的可编辑节更改为`DynamicContent`节。 `value`参数指定分段ID。

电子邮件和登陆页面遵循此模式。 代码片段使用代码片段API文档中描述的不同模式。

以下示例将部分设置为动态内容部分，并按分段1001进行分段。

```http
POST /rest/asset/v1/email/{id}/content/Q1-promotion-banner.json
```

```text
type=DynamicContent&value=1001
```

```json
{
  "success": true,
  "errors": [],
  "requestId": "891b#1729b34b9a5",
  "warnings": [],
  "result": [
    {
      "id": 1909
    }
  ]
}
```

调用[更新电子邮件动态内容节](https://developer.adobe.com/marketo-apis/api/asset#tag/Emails/operation/updateEmailDynamicContentUsingPOST)端点为特定节中的区段添加内容。

以下请求显示一个特殊横幅，而不是西南区段中商机的默认内容。 要创建更多变体，请调用每个区段和部分的端点。

```http
POST /rest/asset/v1/email/{id}/dynamicContent/{dynamicContentId}.json
```

```text
segment=Southwest&type=HTML&value=<img src='//www.example.com/SuperSpecialBannerForAmericanSouthwestLeads.jpg'/>
```

```json
{
  "success": true,
  "errors": [],
  "requestId": "891b#1729b34b9a5",
  "warnings": [],
  "result": [
    {
      "id": 1637
    }
  ]
}
```

## 分段

分段是用户定义的规则集列表，Marketo会根据潜在客户数据库从上到下评估这些规则集。 每个区段中的潜在客户只能属于一个区段。 潜在客户将加入其符合条件的第一个区段。

如果潜在客户不符合另一个区段的条件，则它将加入默认区段并接收区段的默认内容。

### 列表

使用列表端点检索可用分段。

```http
GET /rest/asset/v1/segmentation.json
```

```json
{
  "success": true,
  "warnings": [ ],
  "errors": [ ],
  "requestId": "78eb#14e9de95868",
  "result": [
    {
      "id": 1001,
      "name": "My Industry Segmentation",
      "description": "",
      "createdAt": "2015-04-06T18:23:32Z+0000",
      "updatedAt": "2015-04-06T18:37:10Z+0000",
      "url": "https://app-abm.marketo.com/#SG1001A1",
      "folder": {
        "type": "Program",
        "value": 396,
        "folderName": null
      },
      "status": "approved",
      "workspace": "Default"
    },
    {
      "id": 1002,
      "name": "My Country Segmentation",
      "description": "",
      "createdAt": "2015-04-06T18:28:23Z+0000",
      "updatedAt": "2015-04-06T18:37:18Z+0000",
      "url": "https://app-abm.marketo.com/#SG1002A1",
      "folder": {
        "type": "Program",
        "value": 396,
        "folderName": null
      },
      "status": "approved",
      "workspace": "Default"
    }
  ]
}
```

使用区段端点检索父分段中的区段。

```http
GET /rest/asset/v1/segmentation/1001/segments.json
```

```json
{
  "success": true,
  "warnings": [ ],
  "errors": [ ],
  "requestId": "2031#14e9df08796",
  "result": [
    {
      "id": 1001,
      "name": "Manufacturing",
      "description": null,
      "createdAt": "2015-04-06T18:23:32Z+0000",
      "updatedAt": "2015-04-06T18:37:09Z+0000",
      "status": "approved",
      "segmentationId": 1001
    },
    {
      "id": 1002,
      "name": "Healthcare",
      "description": null,
      "createdAt": "2015-04-06T18:23:32Z+0000",
      "updatedAt": "2015-04-06T18:37:09Z+0000",
      "url": "https://app-abm.marketo.com/#SL769688A1",
      "status": "approved",
      "segmentationId": 1001
    },
    {
      "id": 1003,
      "name": "Financial",
      "description": null,
      "createdAt": "2015-04-06T18:23:32Z+0000",
      "updatedAt": "2015-04-06T18:37:09Z+0000",
      "url": "https://app-abm.marketo.com/#SL769690A1",
      "status": "approved",
      "segmentationId": 1001
    },
    {
      "id": 1004,
      "name": "Technology",
      "description": null,
      "createdAt": "2015-04-06T18:23:32Z+0000",
      "updatedAt": "2015-04-06T18:37:09Z+0000",
      "url": "https://app-abm.marketo.com/#SL769692A1",
      "status": "approved",
      "segmentationId": 1001
    },
    {
      "id": 1005,
      "name": "Default",
      "description": null,
      "createdAt": "2015-04-06T18:23:32Z+0000",
      "updatedAt": "2015-04-06T18:37:09Z+0000",
      "url": "https://app-abm.marketo.com/#SL769694A1",
      "status": "approved",
      "segmentationId": 1001
    }
  ]
}
```
