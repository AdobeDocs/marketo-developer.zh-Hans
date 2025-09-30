---
title: 自定义操作
feature: Mobile Marketing
description: 了解如何使用适用于iOS和Android的Marketo Mobile SDK发送和报告自定义操作，将离线排队，触发智能营销活动，并符合20个字符的要求……
exl-id: 8c2698ce-4e39-4b2b-9d36-0864c55be17a
source-git-commit: 7557b9957c87f63c2646be13842ea450035792be
workflow-type: tm+mt
source-wordcount: '313'
ht-degree: 0%

---

# 自定义操作

您可以通过发送自定义操作来跟踪用户交互。 当移动设备应用程序调用Marketo SDK以发送自定义操作时，自定义操作最初保存到设备。 Marketo SDK随后在发送自定义操作之前检查是否存在足够的Internet连接。 因此，发送自定义操作与Marketo接收自定义操作之间可能存在延迟。

自定义操作可用作智能营销活动中的触发器和过滤器。 有关详细信息，请参阅[移动设备应用程序活动](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/core-marketo-concepts/smart-campaigns/flow-actions/triggers-and-filters-for-mobile-smart-campaigns)。

## 在iOS上发送自定义操作

发送自定义操作。

>[!BEGINTABS]

>[!TAB 目标C]

```
Marketo *sharedInstance = [Marketo sharedInstance];
[sharedInstance reportAction:@"Login" withMetaData:nil];
```

>[!TAB Swift]

```
sharedInstance.reportAction("Login", withMetaData:nil);
```

>[!ENDTABS]

发送包含元数据的自定义操作。

>[!BEGINTABS]

>[!TAB 目标C]

```
MarketoActionMetaData *meta = [[MarketoActionMetaData alloc] init];
[meta setType:@"Shopping"];
[meta setDetails:@"RedShirt"];
[meta setLength:20];
[meta setMetric:30];

[sharedInstance reportAction:@"Bought Shirt" withMetaData:meta];
```

>[!TAB Swift]

```
let meta = MarketoActionMetaData()
meta.setType("Shopping");
meta.setDetails("RedShirt");
meta.setLength(20);
meta.setMetric(30);

sharedInstance.reportAction("Bought Shirt", withMetaData:meta);
```

>[!ENDTABS]

立即报告所有操作（发送所有保存的操作）。

>[!BEGINTABS]

>[!TAB 目标C]

```
[sharedInstance reportAll];
```

>[!TAB Swift]

```
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

1. 立即报告所有自定义操作（发送所有保存的操作）。

   ```
   Marketo.reportAll();
   ```

## 自定义操作疑难解答

设置移动设备自定义操作很简单，但您可以从Mobile SDK发送到Marketo的字符数存在限制。 确保通过移动设备SDK报告回Marketo的所有自定义操作的长度少于20个字符。

**有关共享设备上的多用户用例的注意事项：**&#x200B;当用户登录到与Marketo SDK集成的移动应用程序时，将进行第一次调用以将潜在客户与应用程序安装关联起来。 成功完成此调用后，可在商机的活动日志中查看应用程序中的其他用户活动。 注意，由于这是异步调用，如果在登录后立即记录任何自定义操作，则这些操作可能会与之前登录的用户相关联，直到关联调用成功。
