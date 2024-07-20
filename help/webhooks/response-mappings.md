---
title: 响应映射
feature: Webhooks
description: Marketo的响应映射
exl-id: 95c6e33e-487c-464b-b920-3c67e248d84e
source-git-commit: 66add4c38d0230c36d57009de985649bb67fde3e
workflow-type: tm+mt
source-wordcount: '460'
ht-degree: 0%

---

# 响应映射

Marketo可以翻译Webhook从两种内容类型接收的数据，并将这些值返回潜在客户字段：JSON和XML。 Marketo字段参数将始终使用字段的[SOAP API名称](../rest-api/fields.md)。 每个Webhook可以有无限数量的响应映射，可通过单击Webhook的“响应映射”窗格中的[!UICONTROL Edit]按钮来添加和编辑这些映射：

![响应映射](assets/response-mapping.png)

响应映射是通过“响应属性”、XML或JSON文档中所需属性的路径以及“Marketo字段”的配对创建的，该字段指定了“潜在客户”字段，该字段具有从“响应属性”写入的值。

属性的键必须由字母数字字符、短划线(-)、下划线(_)、冒号(：)和空格组成，以便通过Marketo响应映射进行访问。

## JSON映射

使用点表示法和数组表示法访问JSON属性。 Marketo中的数组表示法不接受字符串作为输入，仅接受整数。 要从JSON文档检索数据，必须将响应类型设置为JSON：

```json
{ "foo":"bar"}
```

要在响应映射中访问`foo`属性，请使用属性的`name`，因为该属性位于JSON对象的第一个级别`foo`。 以下是Marketo中的外观：

![响应映射](assets/json-resp.png)

下面是数组的一个更复杂的示例：

```json
{
    "profileId" : 1234,
    "firstName" : "Jane",
    "lastName" : "Doe",
    "orders" : [
        {
            "orderId" : 5678,
            "orderDate" : "2015-01-01",
            "orderProductId" : "4982"
        },
        {
            "orderId" : 5678,
            "orderDate" : "2014-05-07",
            "orderProductId" : "4982"
        }
    ]
}
```

我们要从orders数组的第一个元素访问orderDate。 要访问此属性，请使用以下内容： `orders[0].orderDate`

## XML映射

可以从XML文档中的单个元素访问值。 这使用与JSON映射类似的点表示法。 让我们看一下这个简单的示例：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<example>
    <foo>bar</foo>
</example>
```

要在此处访问foo属性，请使用以下内容： `example.foo`

访问`foo`之前必须先引用示例元素。 要访问属性，必须在映射中引用层级中的所有元素。 带有数组的XML文档要复杂一些。 使用以下示例：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<elementList>
    <element>
        <foo>baz</foo>
    </element>
    <element>
        <foo>bar</foo>
    </element>
    <element>
        <foo>bar</foo>
    </element>
</elementList>
```

该文档由父数组`elementList`和子元素组成，该元素包含一个属性： `foo`。 在Marketo响应映射中，数组被引用为`elementList.element`，因此可通过`elementList.element[i]`访问elementList的子项。 为了从elementList的第一个子项中获取foo的值，我们使用此响应属性： `elementList.element[0].foo`这会将值“baz”返回到我们的指定字段。 尝试访问包含唯一和非唯一元素名称的元素内的属性会导致未定义的行为。 每个元素必须是单个属性或数组，不能混合使用类型。

## 类型

将属性映射到字段时，必须确保webhook响应中的类型与目标字段兼容。 例如，如果响应中的值为字符串，并且所选字段的类型为整数，则不会写入该值。 了解[字段类型](../rest-api/field-types.md)。
