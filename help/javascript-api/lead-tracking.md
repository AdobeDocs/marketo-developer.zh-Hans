---
title: 商机跟踪
description: 商机跟踪API
feature: Munchkin Tracking Code, Javascript
exl-id: 7ece5133-9d32-4be3-a940-4ac0310c4d8b
source-git-commit: 66add4c38d0230c36d57009de985649bb67fde3e
workflow-type: tm+mt
source-wordcount: '766'
ht-degree: 0%

---

# 商机跟踪API

Marketo的Munchkin JavaScript允许跟踪最终用户对Marketo登陆页面和外部网页的访问次数和点击次数。 这些链接在Marketo中记录为“访问网页”和“网页上的点击链接”活动，随后可用于智能营销活动和智能列表的触发器和过滤器中。

## 嵌入代码

您的Marketo实例会自动提供预配置的跟踪代码片段以在外部页面上嵌入代码，这些代码可跟踪回您的Marketo实例中的活动。 使用嵌入代码应受此约束 [许可协议](../munchkin-license.pdf).

提供了三种跟踪代码类型：

1. 简单 — 同步加载
1. 异步 — 异步加载
1. 异步jQuery — 异步加载，并需要预先加载jQuery

强烈建议使用异步跟踪代码在外部页面上嵌入Munchkin。 要确保执行尽可能高的成功率，请将异步跟踪代码嵌入到 `<head>` 每个页面的。

某些内容管理系统在嵌入任意脚本时可能具有特定方法或限制。

作为参考，您的最终页面应将与以下内容类似的代码包含在 `<head>` HTML文档的：

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

Marketo Munchkin的默认行为是在页面加载时执行以下操作：

1. 检查当前浏览器是否具有Munchkin Cookie，如果没有，请创建一个。
1. 使用当前页面和浏览器的信息将“访问网页”事件发送到指定的Marketo实例。 这会将活动记录到Marketo中的相应记录。
1. 对于在链接上发生的任何用户点击，发送“网页上的点击链接”事件。

Munchkin的行为可以通过使用Munchkin来修改 [配置设置](lead-tracking.md#lead-tracking-api)，例如在使用访问页面时是否为所有潜在客户创建Cookie `cookieAnon` 设置或修改单击延迟 `clickTime` 设置。 通过将apiOnly设置设置为true，可以禁用访问活动的发送。 自版本162（2022年8月）起，单击 `tel` 和 `mailto` 此外，还跟踪链接 `http/s` 链接。

## 已知和匿名潜在客户

当商机首次访问您域中的某个页面时，将在Marketo中创建一个新的匿名商机记录。 该记录的主键是Munchkin Cookie (`_mkto_trk`)的用户浏览器中创建。 将针对此匿名记录记录记录该浏览器上的所有后续Web活动。 要与Marketo中的已知记录关联，必须执行以下任一操作：

- 潜在客户必须访问包含 `mkt_tok` 参数作为查询字符串的参数，该字符串来自跟踪的Marketo电子邮件链接。
- 潜在客户必须填写Marketo表单。
- SOAP [syncLead](../soap-api/leads.md) 或REST [关联潜在客户](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Leads/operation/associateLeadUsingPOST) 必须发送调用。

一旦满足了这些条件之一，Cookie和所有关联的Web活动就会关联到已知商机。

系统会为每个单独的浏览器创建新的匿名Web活动记录，因此，如果潜在客户首次使用新计算机和/或浏览器访问您的域，则必须再次进行此关联。

## 域

Munchkin会按域创建和跟踪各个Cookie，因此，为了使跨域进行已知商机跟踪，必须为每个域发生商机关联事件。 例如，如果我控制两个域， `marketo.com`、和 `example.com`，并且潜在客户填写表单 `marketo.com`，然后导航到 `example.com` 之后，其活动 `marketo.com` 根据已知的潜在客户记录进行跟踪，但其活动在 `example.com` 匿名。 但是，已知潜在客户会在子域之间保留，因此已知潜在客户位于 `www.example.com` 也是已知的销售线索 `info.example.com`.

如果顶级域由两部分组成，例如 `.co.uk`，然后向您的Munchkin代码片段中添加一个domainLevel参数，以便代码能够正确跟踪。 请参阅 [此处](lead-tracking.md#domains) 以了解更多详细信息。

## Cookie

Munchkin Cookie使用键 `_mkto_trk`，并且具有遵循此模式的值：

`id:561\-HYG\-937&token:_mch\-marketo.com\-1374552656411\-90718`

Munchkin Cookie特定于每个二级域，即， `example.com`. Cookie的默认有效期为2年（730天）。

## Beta

要选择启用登陆页面的Munchkin测试版渠道，请转到 [管理员 — > Treasure Chest](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/administration/settings/enable-or-disable-treasure-chest-features) 菜单并启用“Munchkin Beta on Landing Pages”设置。 这会在以下位置提供新的代码片段： **[!UICONTROL Admin]** ->  **[!UICONTROL Munchkin]** 菜单，让您能够在外部网站上使用测试版。

## 选择禁用

访客可以通过添加 `querystring` 参数“marketo_opt_out=true”。 当Munchkin JavaScript检测到此设置时，它会尝试使用值()设置新的Cookie“mkto_opt_out” `true`. 删除所有其他Marketo跟踪Cookie，不设置新的Cookie，并且Munchkin在检测到此设置时不会发出任何HTTP请求。
