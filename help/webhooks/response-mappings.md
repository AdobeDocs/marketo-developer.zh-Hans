---
title: 响应映射
feature: Webhooks
description: Marketo Webhook会映射JSON和XML的响应，将属性映射到具有SOAP API名称、点和数组表示法以及类型兼容性的潜在客户字段。
exl-id: 95c6e33e-487c-464b-b920-3c67e248d84e
TQID: https://experienceleague.adobe.com/-OGDeKLPS1KmWGIKj6BGq5DGXoCSj5ip-dVr7-kKDro
product_v2: id: b27e5950-9033-45ac-9f86-eb22e567f615
feature_v2: id: c5f60233-d5ea-4453-a799-0ad258b4d399
role_v2: id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
source-git-commit: 3e6d310c5aec1a3435424fb122b71d825db5af0e
workflow-type: tm+mt
source-wordcount: 373
ht-degree: 0%

---

# 响应映射

Marketo可以从JSON或XML翻译webhook数据，并将值写入潜在客户字段。 Marketo字段参数始终使用字段的[SOAP API名称](../rest-api/fields.md)。

每个webhook可以有无限数量的响应映射。 要添加或编辑映射，请在webhook的“响应映射”窗格中选择[!UICONTROL Edit]：

![响应映射](assets/response-mapping.png)

响应映射将配对以下值：

- “响应属性”： XML或JSON文档中所需属性的路径。
- “Marketo字段”： Marketo将响应属性值写入到的潜在客户字段。

要通过Marketo响应映射访问属性，其键只能包含字母数字字符、短划线(-)、下划线(_)、冒号(：)和空格。

## JSON映射

使用点表示法和数组表示法访问JSON属性。 Marketo数组表示法仅接受整数，而不接受字符串。

要从JSON文档检索数据，请将响应类型设置为JSON：

```json
{ "foo":"bar"}
```

`foo`属性位于JSON对象的第一级。 在响应映射中使用其属性`name` `foo`：

![响应映射](assets/json-resp.png)

以下示例包含一个数组：

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

要从orders数组的第一个元素访问orderDate，请使用`orders[0].orderDate`。

## XML映射

使用点表示法访问单个XML元素中的值，与JSON映射类似。 请看以下示例：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<example>
    <foo>bar</foo>
</example>
```

要访问foo属性，请使用`example.foo`。

在访问`foo`之前引用示例元素。 映射必须引用属性层次结构中的每个元素。

对于具有数组的XML文档，请考虑以下示例：

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

父数组是`elementList`。 每个子元素都包含`foo`属性。 Marketo响应映射将数组引用为`elementList.element`，并通过`elementList.element[i]`访问其子项。

要从elementList的第一个子级获取foo的值，请使用响应属性`elementList.element[0].foo`。 此映射将值“baz”返回到指定字段。

访问包含唯一和非唯一元素名称的元素内的属性会生成未定义的行为。 每个元素必须是单个属性或数组。 不要混用各种类型。

## 类型

将属性映射到字段时，请确保webhook响应类型与目标字段兼容。 例如，Marketo不会将字符串响应值写入整数类型的字段。 有关详细信息，请参阅[字段类型](../rest-api/field-types.md)。
