---
title: 自定义操作
feature: Mobile Marketing
description: 了解如何使用适用于iOS和Android的Marketo Mobile SDK发送和报告自定义操作，将离线排队，触发智能营销活动，并符合20个字符的要求……
exl-id: 8c2698ce-4e39-4b2b-9d36-0864c55be17a
TQID: https://experienceleague.adobe.com/yZKzdm-dH0cYPGGKE-Z-4KcbhGIwyFl0Z9vEqcv1QXI
product_v2:
  - id: b27e5950-9033-45ac-9f86-eb22e567f615
feature_v2:
  - id: a7170d27-32ab-462b-a333-269abc654483
role_v2:
  - id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
topic_v2:
  - id: a004cc84-67b9-4a33-a3a7-8ec7273ef4dc
  - id: c1579802-ddd4-4214-8a91-97b2066abe11
  - id: d095671a-1355-40aa-8b5f-06c33c68080b
source-git-commit: 3e6d310c5aec1a3435424fb122b71d825db5af0e
workflow-type: tm+mt
source-wordcount: 259
ht-degree: 2%

---

# 自定义操作

自定义操作可跟踪移动应用程序中的用户交互。 当应用程序调用Marketo SDK以发送自定义操作时，SDK会先将该操作保存到设备。 SDK在检测到足够的Internet连接后发送操作，因此Marketo可能会在延迟后收到操作。

自定义操作可用作智能营销活动中的触发器和过滤器。 有关详细信息，请参阅[移动设备应用程序活动](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/core-marketo-concepts/smart-campaigns/flow-actions/triggers-and-filters-for-mobile-smart-campaigns)。

## 在iOS上发送自定义操作

发送自定义操作。

>[!BEGINTABS]

>[!TAB 目标C]

```objectivec
Marketo *sharedInstance = [Marketo sharedInstance];
[sharedInstance reportAction:@"Login" withMetaData:nil];
```

>[!TAB Swift]

```swift
sharedInstance.reportAction("Login", withMetaData:nil);
```

>[!ENDTABS]

发送包含元数据的自定义操作。

>[!BEGINTABS]

>[!TAB 目标C]

```objectivec
MarketoActionMetaData *meta = [[MarketoActionMetaData alloc] init];
[meta setType:@"Shopping"];
[meta setDetails:@"RedShirt"];
[meta setLength:20];
[meta setMetric:30];

[sharedInstance reportAction:@"Bought Shirt" withMetaData:meta];
```

>[!TAB Swift]

```swift
let meta = MarketoActionMetaData()
meta.setType("Shopping");
meta.setDetails("RedShirt");
meta.setLength(20);
meta.setMetric(30);

sharedInstance.reportAction("Bought Shirt", withMetaData:meta);
```

>[!ENDTABS]

立即报告所有已保存的操作。

>[!BEGINTABS]

>[!TAB 目标C]

```objectivec
[sharedInstance reportAll];
```

>[!TAB Swift]

```swift
sharedInstance.reportAll();
```

>[!ENDTABS]

## 在Android上发送自定义操作

1. 发送自定义操作。

   ```
   Marketo.reportAction("Login", null);
   ```

1. 发送包含元数据的自定义操作。

   ```
   MarketoActionMetaData meta = new MarketoActionMetaData();
   meta.setActionType("Shopping");
   meta.setActionDetails("RedShirt");
   meta.setActionLength("20");
   meta.setActionMetric("30");
   
   Marketo.reportAction("Bought Shirt", meta);
   ```

1. 立即报告所有已保存的自定义操作。

   ```
   Marketo.reportAll();
   ```

## 对自定义操作进行故障排除

从Mobile SDK发送到Marketo的自定义操作名称必须少于20个字符。

**共享设备上的多用户用例：**&#x200B;当用户登录到使用Marketo SDK的移动应用程序时，第一次调用会将潜在客户与应用程序安装关联。 调用成功后，后续用户活动将显示在商机的活动日志中。

关联调用是异步调用。 在调用成功之前，登录后立即记录的自定义操作可能与之前登录的用户相关联。
