---
title: 配置
description: 使用Marketo API配置JavaScript Munchkin。 了解Munchkin.init设置，如altIds、anonymizeIP、asyncOnly、Cookie生命周期、domainLevel、Beacon API。
feature: Munchkin Tracking Code, Javascript
exl-id: 4700ce7b-f624-4f27-871e-9a050f203973
TQID: https://experienceleague.adobe.com/ip2cCGgoa83v8m9GYLYXe132veYxS1C6UWX1iLB6X5Q
product_v2:
  - id: b27e5950-9033-45ac-9f86-eb22e567f615
feature_v2:
  - id: b3b8a63f-51fc-40f6-a7d2-a31c5d49fb45
  - id: ea90ebee-5c84-42d9-8b21-006bdabc95a3
role_v2:
  - id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
topic_v2:
  - id: aa2f3246-cb95-4b30-8899-fdf7d73550cc
source-git-commit: 3e6d310c5aec1a3435424fb122b71d825db5af0e
workflow-type: tm+mt
source-wordcount: 541
ht-degree: 5%

---

# 配置

Munchkin接受用于自定义其行为的配置设置。 将设置作为JavaScript对象的属性传递到[Munchkin.init()](api-reference.md#munchkin_init)的第二个参数。

```json
Munchkin.init("AAA-BBB-CCC", {
        "configName":"configValue",
        "configName2":"configValue2"
    }
);
```

配置设置对象可以包含下表中的任意数量的属性。

## 属性

| 名称 | 数据类型 | 描述 |
| --- | --- | --- |
| altIds | 数组 | 接受Munchkin ID字符串的数组。 启用后，会将所有Web活动复制到其Munchkin ID标识的订阅。 |
| anonymizeip | 布尔值 | 将Marketo中记录的新访客的IP地址匿名化。 |
| apiOnly | 布尔值 | 如果设置为true，则`Munchkin.Init()`函数将不会调用`visitsWebPage`。 这对于需要完全控制每`visitsWebPage`个事件的单页Web应用程序非常有用。 |
| asyncOnly | 布尔值 | 如果设置为true，则异步发送XMLHttpRequests。 默认值为false。 |
| clickTime | 整数 | 设置单击后要阻止的时间（以毫秒为单位），以便完成点击跟踪请求。 减小此值会降低点击跟踪准确性。 默认值为350毫秒。 |
| cookieAnon | 布尔值 | 如果设置为false，则阻止为新匿名潜在客户跟踪和Cookie创建。 在提交Marketo表单或从Marketo电子邮件点进之后，潜在客户会收到Cookie并受到跟踪。 默认值为true。 |
| cookieLifeDays | 整数 | 将任何新创建的Munchkin跟踪Cookie的过期日期设置为将来的此天数。 默认值为730天（2年）。 |
| customname | 字符串 | 自定义页面名称。 仅供系统使用。 |
| <a name="domainlevel"></a>域级别 | 整数 | 设置要用于Cookie的域属性的页面域部分数。<br><br>对于“www.example.com”，`domainLevel: 2`将Cookie域设置为“.example.com”，而`domainLevel: 3`将其设置为“。www.example.com”。<br><br>默认情况下，当顶级域包含三个字母时，Munchkin使用两个部分。 例如，“www.example.com”使用“.example.com”。<br><br>对于双字母国家/地区代码（例如“.jp”、“.us”、“.cn”和“.uk”），Munchkin使用三个部分。 例如，“www.example.co.jp”使用“.example.co.jp”。<br><br>当域模式需要不同的行为时，请使用`domainLevel`参数。 |
| domainSelectorV2 | 布尔值 | 如果设置为true，则使用改进的方法来确定如何设置Cookie域属性。 |
| httpsOnly | 布尔值 | 默认为false。 如果设置为true，则在通过https提供跟踪的页面时，会将Cookie设置为使用安全设置。 |
| useBeaconAPI | 布尔值 | 默认为false。 当设置为true时，使用[Beacon API](https://developer.mozilla.org/en-US/docs/Web/API/Beacon_API)发送非阻止请求，而不是[XMLHttpRequest](https://developer.mozilla.org/zh-CN/docs/Web/API/XMLHttpRequest)。 如果浏览器不支持信标API，Munchkin会使用XMLHttpRequest。 |
| wsInfo | 字符串 | 定位工作区。 通过在管理员>集成> Munchkin菜单中选择工作区，获取工作区ID。<br><br>此设置仅在最初创建匿名潜在客户记录时适用。 为该潜在客户记录建立Munchkin Cookie值后，wsInfo参数无法更改其分区。<br><br>由于此设置仅影响匿名潜在客户，因此它仅与Web报表中特定于分区的[匿名访客](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/reporting/basic-reporting/report-activity/display-people-or-anonymous-visitors-in-web-reports)相关。 |

## 示例

### 将活动发送到多个订阅

此示例将所有Web活动发送到具有Munchkin ID“AAA-BBB-CCC”和“XXX-YYYY-ZZZ”的实例。

```javascript
<script type="text/javascript">
(function() {
  var didInit = false;
  function initMunchkin() {
    if(didInit === false) {
      didInit = true;
      // Add configuration settings to the init method
      Munchkin.init('AAA-BBB-CCCC', { 'altIds': ['XXX-YYY-ZZZ'] });
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
```

### 将跟踪设置为异步

此示例强制从主线程异步发送所有XMLHttpRequests。

```javascript
<script type="text/javascript">
(function() {
  var didInit = false;
  function initMunchkin() {
    if(didInit === false) {
      didInit = true;
      // Add configuration settings to the init method
      Munchkin.init('AAA-BBB-CCC', { 'asyncOnly': true });
    }
  }
  var s = document.createElement('script');
  s.type = 'text/javascript';
  s.async = true;
  s.src = '//munchkin.marketo.net/munchkin-beta.js';
  s.onreadystatechange = function() {
    if (this.readyState == 'complete' || this.readyState == 'loaded') {
      initMunchkin();
    }
  };
  s.onload = initMunchkin;
  document.getElementsByTagName('head')[0].appendChild(s);
})();
</script>
```
