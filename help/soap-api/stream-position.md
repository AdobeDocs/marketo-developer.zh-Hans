---
title: "流位置"
feature: SOAP
description: “Steam定位概述”
source-git-commit: d335bdd9f939c3e557a557b43fb3f33934e13fef
workflow-type: tm+mt
source-wordcount: '138'
ht-degree: 0%

---


# 流位置

流位置元素包含一个或多个时序数据的逻辑流的位置引用。 位置引用可以是近似的外部规范，如时间戳，或者由先前API调用返回的不透明的内部位置规范。 流位置可以定义为复杂的多元素类型或可以是字符串。

流位置用于批量检索数据，并允许调用方对结果分页。 在API请求中传递的流位置是上次响应中返回的流位置的值。 不建议修改从上一个API调用返回的流位置，这可能会导致意外的API行为。

## 支持流位置的API

- [getCustomObjects](getcustomobjects.md)
- [getleadchanges](getleadchanges.md)
- [getLeadActivity](getleadactivity.md)
- [getMObjects](getmobjects.md)
- [getMultipleLeads](getmultipleleads.md)

## 简单流位置

```
<streamPosition>8UJZetaMb1V6uUZl+L7DcPP2jG+PMmtpF</streamPosition>
```

## 复杂流位置

```xml
<startPosition>
  <latestCreatedAt  />
  <oldestCreatedAt>2013-08-01T00:13:13+00:00</oldestCreatedAt>
  <activityCreatedAt  />
  <offset>ID:1086173</offset>
</startPosition>
```
