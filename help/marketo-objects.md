---
title: Marketo对象
feature: Email Programs
description: 有关将Marketo Velocity与潜在客户、商机和自定义对象、加载字段、前10位列表访问权限、SFDC关系和$TriggerObject结合使用的指南。
exl-id: 88c63d72-7aa5-4550-9e1a-887a479872e1
TQID: https://experienceleague.adobe.com/PvLJb-AOk6DKaNINycpzk5ojZiL8UNcanRg3vXmsGCI
product_v2: id: b27e5950-9033-45ac-9f86-eb22e567f615
feature_v2: id: e2290edd-b061-4880-9d79-dee306cf5aa9id: e64968b2-4ee5-47f9-8cae-0588f184b9eb
role_v2: id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
topic_v2: id: b5ce8718-c3af-4fdb-a1a9-fca32f83a87c
source-git-commit: 3e6d310c5aec1a3435424fb122b71d825db5af0e
workflow-type: tm+mt
source-wordcount: 452
ht-degree: 0%

---

# Marketo对象

Marketo的Velocity实施可以使用来自以下Marketo来源的数据：

- 潜在客户
- 商机
- 自定义对象
- 移动应用程序
- 移动应用程序安装

## 正在加载字段

要在脚本中使用字段，请在脚本令牌编辑器的相应列表下选择字段。

如果脚本引用了未加载的字段，则脚本在运行时将失败。 将字段菜单中的字段拖入脚本以加载该字段，并在光标处添加引用。

## Opportunity和自定义对象列表

对于机会和自定义对象，Marketo仅加载每种类型的10个最近更新的对象。 您可以按照此处介绍的步骤增加可用自定义对象的数量。

Marketo在名为`<objectName>List`的列表中提供对象，这些对象按照从最近更新的记录到最近更新最小的记录的顺序排列。 要从最近更新的Opportunity中访问Amount字段，请使用：

`${OpportunityList.get(0).Amount}`

此示例引用OpportunityList对象，使用get方法访问索引0处的记录，并从该记录中检索Amount属性。

将Opportunity或Custom Object字段拖入编辑器时，Marketo会自动从索引0处的记录中检索该字段。

## SFDC自定义对象关系

要使用SFDC自定义对象，该对象必须只与Marketo潜在客户有一个关系。 对象通常通过联系人和帐户进行链接。 仅同步启用了潜在客户/联系人关系的对象。

## 触发器对象

当营销活动使用Added to Opportunity 、 Opportunity已更新或添加到`<Custom Object Name>`触发器时，`$TriggerObject`变量可用于在触发器营销活动中运行的脚本令牌。 `<Custom Object Name>` is Updated触发器不支持此变量。

此变量引用触发营销活动的对象。 它包含通过其他变量名称访问对象时可用的相同记录数据。

请勿在批处理营销活动中使用引用`$TriggerObject`的令牌。 对象在批量营销活动中不可用，因此电子邮件发送失败。

例如，如果产品订单的自定义对象触发了促销活动，则`$TriggerObject`变量将显示商机所添加的订单。

以下示例显示了订单跟进电子邮件的脚本：

```html
<div>
<strong>Your order information:</strong>
##pull information from the Triggering Order and format it in a list
<ul>
<li>Product Ordered: $!{TriggerObject.ProductName}</li>
<li>Product Quantity: $!{TriggerObject.Quanitity}</li>
<li>Shipping Address: $!{TriggerObject.ShippingAddress}</li>
<li>Billing Address: $!{TriggerObject.BillingAddress}</li>
<li>Order Total: $!{TriggerObject.Amount}</li>
</ul>
<p><a href="$!{TriggerObject.OrderURL}">View Your Order Online</a></p>
</div>
```

触发操作确定对象。 您不需要其他代码来确定哪个可用对象包含本地数据。 当它可用且合适时，使用`$TriggerObject`，因为它显式标识要引用的对象。

注意：使用`$TriggerObject`时，请在编辑窗格中选择对象的字段，以使它们可用于脚本。

注意2：`$TriggerObject`仅适用于“已添加”触发器，而不适用于“已更新”触发器。
