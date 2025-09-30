---
title: 自定义对象
feature: SOAP
description: 了解Marketo自定义对象如何链接一个指向多个记录，其中具有结构、限制以及用于获取、同步、删除以及智能列表和电子邮件使用的SOAP API调用。
exl-id: 29d65841-4b44-4d94-b14e-c583d433d015
source-git-commit: 7557b9957c87f63c2646be13842ea450035792be
workflow-type: tm+mt
source-wordcount: '256'
ht-degree: 0%

---

# 自定义对象

Marketo自定义对象允许在您的Marketo潜在客户与自定义对象记录之间创建一对多关系。 例如，您可能希望跟踪潜在客户参加的所有路演。 由于潜在客户可能会参加许多路演（历时多年），因此自定义对象更适合存储此信息。

除Spark之外的所有Marketo版本都支持自定义对象。 在您的帐户中成功配置Marketo自定义对象后，您可以

- 通过Marketo SOAP API在自定义对象中创建/读取/更新/删除记录
- 将新记录添加到自定义对象时，使用智能列表触发器
- 在智能列表中将自定义对象数据用作筛选器
- 使用Marketo电子邮件脚本在电子邮件中使用自定义对象数据

## 自定义对象的结构

自定义对象包括：

- 一小部分对所有自定义对象通用的固定属性：
   - 对象名称（又称“对象类型名称”）
   - 对象说明
   - 自定义对象到Marketo潜在客户链接字段名称 — 这是自定义对象引用的潜在客户（人员）对象上的字段
   - 对象键字段名称 — 对象使用的主键
- 一个或多个对象特定的字段（我们最多支持50个此类字段）

## 自定义对象操作

以下调用可用于与CO交互。

- [getCustomObjects](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Custom-Objects/operation/getCustomObjectsUsingGET)
- [syncCustomObjects](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Custom-Objects/operation/syncCustomObjectsUsingPOST)
- [deleteCustomObjects](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Custom-Objects/operation/deleteCustomObjectsUsingPOST)
