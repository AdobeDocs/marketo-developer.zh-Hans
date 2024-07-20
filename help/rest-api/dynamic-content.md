---
title: 动态内容
feature: REST API, Dynamic Content
description: 使用Marketo API配置动态内容。
exl-id: 8ab97624-5fb5-4a41-911f-ec8616dd43c9
source-git-commit: 66add4c38d0230c36d57009de985649bb67fde3e
workflow-type: tm+mt
source-wordcount: '426'
ht-degree: 1%

---

# 动态内容

Marketo通过对多种资源类型进行商机细分来简化动态内容的使用：

- 电子邮件
- 登陆页面
- 代码片段

## 概述

通过在部分级别实施动态内容，根据在特定分段内商机区段中的资格指定提供给商机的部分的特定变体。 如果一段内容配置为根据特定分段提供动态内容，则表示该内容的商机将获得与其所属的分段匹配的内容变体，或者获得默认内容（如果他们不符合分段条件）。

## 示例

为了演示，我们来看一个电子邮件示例，其中我们进行了地区（美国）分段，并且希望仅为属于西南区段的潜在客户显示活动促销，西南区段包括加利福尼亚州、内华达州、犹他州、科罗拉多州、亚利桑那州和新墨西哥州潜在客户。 为此，我们将ID为“Q1-promotion-banner”的电子邮件中的可编辑部分添加到DynamicContent部分中。 为此，我们必须为我们的电子邮件使用[更新电子邮件内容部分](https://developer.adobe.com/marketo-apis/api/asset/#tag/Emails/operation/updateEmailComponentContentUsingPOST)端点。 `value`参数用于指定分段的ID。

注意：电子邮件和登陆页面都遵循此模式。 代码片段具有不同的模式，有关详情请参阅代码片段API文档。

以下示例将部分设置为动态内容部分，并按分段1001进行分段。

```
POST /rest/asset/v1/email/{id}/content/Q1-promotion-banner.json
```

```
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

要为各个区段添加内容，我们必须调用特定区段的[更新电子邮件动态内容区段](https://developer.adobe.com/marketo-apis/api/asset/#tag/Emails/operation/updateEmailDynamicContentUsingPOST)端点。

以下示例将部分设置为显示西南区段中潜在客户的特殊横幅图像，而不是默认图像。 如果我们想要为更多区段创建更多变体，那么我们将为每个区段和部分再次调用此端点。

```
POST /rest/asset/v1/email/{id}/dynamicContent/{dynamicContentId}.json
```

```
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

分段是Marketo动态内容的核心。 分段是用户定义的单个规则集列表，这些规则集将针对整个潜在客户数据库从上到下进行评估。 潜在客户只能是每个分段中一个区段的成员，并且将是每个分段中它有资格参与的第一个区段的成员。 如果它不符合区段的条件，则它将是默认区段的成员，并将收到使用该区段的任何给定动态内容的默认内容。

### 列表

区段具有列表端点，该端点返回包含可用区段列表的响应。

```
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

区段还具有端点，该端点返回包含来自父区段的区段列表的响应。

```
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
