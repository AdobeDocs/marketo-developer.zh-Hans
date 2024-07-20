---
title: 字段
feature: REST API, Field Management
description: 支持的字段名称列表。
exl-id: 9033f32a-c7cb-4bbf-abcf-38ca4112139f
source-git-commit: 66add4c38d0230c36d57009de985649bb67fde3e
workflow-type: tm+mt
source-wordcount: '194'
ht-degree: 2%

---

# 字段

REST API和SOAP API对潜在客户字段使用不同的命名约定。

## 检索字段名称列表

使用REST“描述潜在客户”端点检索潜在客户记录中所有支持的字段名称的列表。

## 在何处使用哪种字段名称类型？

有时，很难知道在利用特定集成相关功能时必须使用的字段名称类型。 以下是功能使用REST或SOAP字段名称类型的快速参考。

| 功能 | 要使用的字段名称类型 |
|--- |--- |
| 商机跟踪API (Munchkin) | SOAP |
| Forms 2.0 API | SOAP |
| 列表导入(UI) | SOAP |
| 列表导入(REST API) | REST |
| Webhook响应映射 | SOAP |
| 电子邮件脚本(Velocity) | SOAP |
| SOAP API | SOAP |
| REST API | REST |

### 为什么REST API字段sfdcId始终返回值null？

字段`sfdcId`是一个公式字段，该字段错误地包含在REST API的原始字段映射中。 通过REST API检索的记录不会计算公式字段的值，因此该值将始终为空。 要捕获真正的SFDC ID，您应该使用名为`sfdcLeadId`和`sfdcContactId`的字段。
