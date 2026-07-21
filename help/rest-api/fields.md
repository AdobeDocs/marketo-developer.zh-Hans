---
title: 字段
feature: REST API, Field Management
description: 了解REST和SOAP潜在客户字段命名，通过REST列出字段描述潜在客户，功能映射，为何sfdcId为null，以及使用sfdcLeadId或sfdcContactId。
exl-id: 9033f32a-c7cb-4bbf-abcf-38ca4112139f
TQID: https://experienceleague.adobe.com/H2Bvhy-67U8JJ1V3JwYJ0O0vj4i11fwUCyYQtjxm8u0
product_v2:
  - id: b27e5950-9033-45ac-9f86-eb22e567f615
feature_v2:
  - id: b0bb9048-d951-48d8-8232-45cf248a7e27
  - id: c5f60233-d5ea-4453-a799-0ad258b4d399
role_v2:
  - id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
source-git-commit: 3e6d310c5aec1a3435424fb122b71d825db5af0e
workflow-type: tm+mt
source-wordcount: 194
ht-degree: 2%

---

# 字段

REST API和SOAP API对潜在客户字段使用不同的命名约定。 使用每个集成功能所需的字段名称约定。

## 检索字段名称列表

使用REST“描述潜在客户”端点检索潜在客户记录的所有受支持的字段名称。

## 在何处使用哪种字段名称类型？

必需的字段名称类型取决于集成功能。 下表标识了每个功能是使用REST还是SOAP字段名称。

| 功能 | 要使用的字段名称类型 |
| --- | --- |
| 商机跟踪API (Munchkin) | SOAP |
| Forms 2.0 API | SOAP |
| 列表导入(UI) | SOAP |
| 列表导入(REST API) | REST |
| Webhook响应映射 | SOAP |
| 电子邮件脚本(Velocity) | SOAP |
| SOAP API | SOAP |
| REST API | REST |

### 为什么REST API字段sfdcId始终返回值null？

`sfdcId`字段是一个公式字段，它包含在REST API的原始字段映射中。 通过REST API检索的记录不计算公式字段值，因此`sfdcId`始终返回null。

要检索SFDC ID，请使用`sfdcLeadId`和`sfdcContactId`字段。
