---
title: “自定义对象”
feature: SOAP
description: “创建自定义对象。”
source-git-commit: d335bdd9f939c3e557a557b43fb3f33934e13fef
workflow-type: tm+mt
source-wordcount: '231'
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
