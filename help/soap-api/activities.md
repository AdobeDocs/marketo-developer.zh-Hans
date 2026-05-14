---
title: 活动
feature: SOAP
description: 了解如何使用SOAP与Activities交互，检索潜在客户活动，以及通过getLeadActivities和getLeadChanges跟踪潜在客户更改
exl-id: fd695ab6-e7be-4ced-89c9-c4cd2d4c2ab0
TQID: https://experienceleague.adobe.com/6zUkvoDCqlRmblFDPWzLjdwITsyWxcXrJBKbLux76WI
product_v2:
  - id: b27e5950-9033-45ac-9f86-eb22e567f615
role_v2:
  - id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
source-git-commit: e71bcf289229867bc969345d79c8f014761aaaf9
workflow-type: tm+mt
source-wordcount: 79
ht-degree: 2%

---

# 活动

可以使用以下SOAP调用与活动进行交互。

- [getleadactivities](getleadactivity.md)
- [getleadchanges](getleadchanges.md)

>[!CAUTION]
>
>从2026-12-30开始，如果目标列表包含10,000个或更多潜在客户，对包含`listId`参数的`Get Lead Activities`和`Get Lead Changes`端点的调用将失败（错误代码1003）。 为避免服务中断，请确保正确限定调用的范围以避免此限制。 请参阅[迁移指南](migration.md)。
