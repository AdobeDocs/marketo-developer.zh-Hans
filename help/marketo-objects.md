---
title: Marketo对象
feature: Email Programs
description: 在Velocity脚本中使用Marketo对象的概述
source-git-commit: 3ccb27a0d184e0c1314979d404022bc4e0794f7b
workflow-type: tm+mt
source-wordcount: '518'
ht-degree: 0%

---

# Marketo对象

Marketo的Velocity实施可以对Marketo中多个来源的数据进行操作：潜在客户、机会、自定义对象、移动设备应用程序、移动设备应用程序安装。

## 正在加载字段

要加载字段以在脚本中使用，必须在脚本令牌编辑器的相应列表下选中该字段。

如果不加载字段并在脚本中引用它，则脚本执行在运行时失败。 您可以将字段菜单中的字段拖放到脚本中。 这将允许加载，并在光标处添加对字段的引用。

## Opportunity和自定义对象列表

从“机会”或“自定义对象”中检索时，只加载某个类型的10个最近更新的对象。 可以按照此处描述的步骤增加可用自定义对象的数量。 这些记录以列表形式提供，名称为`<objectName>List`，按最近更新的记录从高到低排序。 因此，要从Opportunity中访问最近更新的Amount字段，您将使用以下方法：

`${OpportunityList.get(0).Amount}`

在本例中，您引用OpportunityList对象，使用get方法访问索引为0的记录，然后从返回的对象中检索Amount属性。 如果将字段从Opportunity或Custom Object拖到编辑器中，它将自动从0处索引的记录中检索该字段。

## SFDC自定义对象关系

要可供使用，SFDC自定义对象必须与Marketo潜在客户只有一个关系。 对象通常同时通过联系人和帐户进行链接，因此，在启用了潜在客户/联系人关系的情况下，务必要仅将对象同步到Marketo。

## 触发器对象

当通过Added to Opportunity 、 Opportunity被更新或添加到`<Custom Object Name>`触发器触发营销活动时，在触发器营销活动上下文中执行的脚本令牌中将出现一个特殊变量： `$TriggerObject `（不支持`<Custom Object Name>`的is Updated trigger）。  如果在批处理营销活动中使用使用`$TriggerObject`引用的令牌，则电子邮件发送将失败，因为此对象在任何类型的批处理营销活动中均不可用。  这是对触发营销活动的对象的引用。 对象包含通过其他变量名称访问记录时该记录拥有的所有数据。

例如，如果促销活动是通过产品订单的自定义对象触发的，则向其添加商机的订单会在`$TriggerObject`变量中显示。

以下是订单跟进电子邮件的脚本示例：

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

使用`$TriggerObject`变量的优点在于，您无需指定任何代码来确定要从其中提取本地数据的可用对象。  对象由触发动作确定。 这是选择要引用的对象的最明确方式，只要可用且合适，都应使用此方法。

注意：使用`$TriggerObject`时，必须在编辑窗格中选中字段，以使对象可用于脚本。

注意2： `$TriggerObject`仅适用于“已添加”触发器，而不适用于“已更新”触发器。
