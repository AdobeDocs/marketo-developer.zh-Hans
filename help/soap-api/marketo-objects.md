---
title: "Marketo对象"
feature: SOAP
description: “Marketo对象概述”
source-git-commit: d335bdd9f939c3e557a557b43fb3f33934e13fef
workflow-type: tm+mt
source-wordcount: '249'
ht-degree: 0%

---


# Marketo对象

Marketo使用Marketo对象(MObjects)表示各种类，如Program、Opportunity和OpportunityPersonRole。

## 对象结构

MObjects可以是三种类型之一：标准、自定义或虚拟。 标准和自定义MO对象表示不同的实体，如Lead或Company ，而虚拟对象（如LeadRecord ）由一个或多个对象的字段组成。 虚拟对象是API中使用的方便对象，但在Marketo应用程序中不存在。

MObject包括：

- 所有MObject共有的一小组固定属性
   - 所需类型
   - 可选的externalKey
   - 只读id、createdAt、updatedAt
- 一个或多个对象特定属性的列表，如名称/值对，其中一些属性可能是必需的。 例如， Opportunity上的名称。
- 关联对象引用的列表，如对象名称加号
   - Marketo ID或
   - 作为属性名称/属性值对的外部键。

### 外部密钥

外部键是在Marketo对象（如Lead或Opportunity ）上定义的自定义字段。 名称是字段名称，值是在外部系统中生成的字段值。 **Marketo不对这些值强制实施唯一约束。** API用户负责确保值的唯一性。 如果发生重复，Marketo将使用最近添加的对象。 这类似于电子邮件地址标准字段的行为。

### 可用API

| API | 可以运行 |
|---|---|
| describeMObject | ActivityRecord， LeadRecord， Opportunity， OpportunityPersonRole |
| getMObjects | Opportunity 、 OpportunityPersonRole 、 Program |
| syncMObjects | Opportunity 、 OpportunityPersonRole 、 Program |
| deleteMObjects | Opportunity， OpportunityPersonRole |
| listMObjects | ActivityRecord， LeadRecord， Opportunity， OpportunityPersonRole |
