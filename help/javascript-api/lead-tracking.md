---
title: 商机跟踪
description: 了解如何嵌入Marketo Munchkin JavaScript、跟踪访问和点击量、管理已知和匿名潜在客户、跨域Cookie以及选择退出智能营销活动。
feature: Munchkin Tracking Code, Javascript
exl-id: 7ece5133-9d32-4be3-a940-4ac0310c4d8b
TQID: https://experienceleague.adobe.com/nGUcLLgL9X7PBKf2E5IzppDj8e-SyEtxmkQaESd90mE
product_v2: id: b27e5950-9033-45ac-9f86-eb22e567f615
feature_v2: id: a7170d27-32ab-462b-a333-269abc654483id: b3b8a63f-51fc-40f6-a7d2-a31c5d49fb45id: d1d0a9cd-295d-4976-8c39-ddae266f240e
subfeature_v2: id: d0251300-e25f-466f-9856-7e11ce8fa7aa
role_v2: id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
topic_v2: id: eddd9b14-83bd-4ff4-9072-54a4a484abb7
source-git-commit: 3e6d310c5aec1a3435424fb122b71d825db5af0e
workflow-type: tm+mt
source-wordcount: 718
ht-degree: 0%

---

# 商机跟踪API

Marketo的Munchkin JavaScript跟踪Marketo登陆页面和外部网页上的页面访问次数和链接点击次数。 Marketo将这些交互记录为“访问网页”和“已单击网页上的链接”活动。

在智能营销活动和智能列表的触发器和过滤器中使用活动。

## 嵌入代码

您的Marketo实例提供了预配置的代码片段，用于跟踪来自外部页面的活动。 嵌入代码的使用受此[许可协议](../munchkin-license.pdf)的约束。

提供了三种跟踪代码类型：

1. 简单 — 同步加载。
1. 异步 — 异步加载。
1. 异步jQuery — 异步加载并要求jQuery先加载。

使用异步跟踪代码在外部页面上嵌入Munchkin。 要获得可能的最高执行成功率，请将代码置于每个页面的`<head>`元素中。

某些内容管理系统在嵌入任意脚本时可能具有特定方法或限制。

您的最终页面应该在HTML文档的`<head>`元素中包含与以下示例类似的代码：

```html
<head>
    <script type="text/javascript">
    (function() {
        var didInit = false;
        function initMunchkin() {
            if(didInit === false) {
                didInit = true;
                Munchkin.init('CHANGE-ME');
            }
        }
        var s = document.createElement('script');
        s.type = 'text/javascript';
        s.async = true;
        s.src = '//munchkin.marketo.net/munchkin.js';
        s.onreadystatechange = function() {
            if (this.readyState == 'complete' || this.readyState == 'loaded') {
                initMunchkin();
            }
        };
        s.onload = initMunchkin;
        document.getElementsByTagName('head')[0].appendChild(s);
        })();
    </script>
    ...
</head>
```

## Munchkin行为

默认情况下，Marketo Munchkin会在页面加载时执行以下操作：

1. 检查当前浏览器是否具有Munchkin Cookie，并在必要时创建一个。
1. 使用当前页面和浏览器的信息将“访问网页”事件发送到指定的Marketo实例。 此事件会在相应的Marketo记录中记录一个活动。
1. 当用户选择链接时，发送“网页上的点击链接”事件。

使用Munchkin [配置设置](configuration.md)更改此行为。 例如，使用`cookieAnon`控制Munchkin是否为访问页面的所有潜在客户创建了Cookie，还是使用`clickTime`更改点击延迟。

要禁用访问活动，请将`apiOnly`设置为true。 从版本162（2022年8月）开始，Munchkin除`http/s`链接之外还跟踪`tel`和`mailto`链接的点击量。

## 已知和匿名潜在客户

当商机首次访问您域中的页面时，Marketo会创建一个匿名商机记录。 此记录的主键是在用户浏览器中创建的Munchkin Cookie (`_mkto_trk`)。

Marketo会将该浏览器中后续的Web活动记录到匿名记录中。 要将活动与已知Marketo记录关联，必须发生以下事件之一：

- 潜在客户必须通过跟踪的Munchkin电子邮件链接访问查询字符串中包含`mkt_tok`参数的Marketo跟踪页面。
- 潜在客户必须填写Marketo表单。
- 必须发送REST [关联潜在客户](https://developer.adobe.com/marketo-apis/api/mapi#tag/Leads/operation/associateLeadUsingPOST)调用。

当发生这些事件之一时，Marketo会将Cookie和所有相关Web活动与已知商机关联。

Marketo会为每个浏览器创建一个匿名Web活动记录。 如果潜在客户从新计算机或浏览器访问您的域，则必须再次发生关联。

## 域

Munchkin基于每个域创建和跟踪Cookie。 要跨域跟踪已知商机，必须在每个域上发生商机关联事件。

例如，假设您控制`marketo.com`和`example.com`。 潜在客户在`marketo.com`上提交表单，稍后转到`example.com`。 `marketo.com`上的活动与已知潜在客户关联，但`example.com`上的活动是匿名的。

已知潜在客户会在子域之间保留。 `www.example.com`上的已知潜在客户也是`info.example.com`上的已知潜在客户。

如果您的顶级域包含两部分，如`.co.uk`，请将`domainLevel`参数添加到您的Munchkin代码片段。 有关详细信息，请参阅[配置](configuration.md#domainlevel)。

## Cookie

Munchkin Cookie使用键`_mkto_trk`和一个遵循以下模式之一的值：

`id:561-HYG-937&token:_mch-marketo.com-1374552656411-90718`

或

`id:561-HYG-937&token:_mch-marketo.com-97bf4361ef4433921a6da262e8df45a`

Munchkin Cookie特定于每个二级域，如`example.com`。 默认的Cookie生命周期为2年（730天）。

## Beta 版

要选择启用登陆页面的Munchkin测试版渠道，请转到[管理员 — > Treasure Chest](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/administration/settings/enable-or-disable-treasure-chest-features)并启用“登陆页面上的Munchkin Beta”设置。

此设置将代码段添加到&#x200B;**[!UICONTROL Admin]** -> **[!UICONTROL Munchkin]**&#x200B;菜单。 使用这些代码片段在外部网站上运行测试版。

## 选择禁用

访客可以通过向浏览器中的URL添加`querystring`参数“marketo_opt_out=true”来选择退出Munchkin跟踪。 当Munchkin JavaScript检测到此设置时，它会尝试设置一个值为`true`的新“mkto_opt_out”Cookie。

然后，Munchkin删除所有其他Marketo跟踪Cookie，不设置新Cookie，并且不发出HTTP请求。
