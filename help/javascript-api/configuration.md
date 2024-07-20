---
title: 配置
description: 配置API
feature: Javascript
exl-id: 4700ce7b-f624-4f27-871e-9a050f203973
source-git-commit: 66add4c38d0230c36d57009de985649bb67fde3e
workflow-type: tm+mt
source-wordcount: '608'
ht-degree: 3%

---

# 配置

Munchkin可接受各种配置设置以自定义行为。 配置设置是在调用[Munchkin.init()](lead-tracking.md#munchkin-behavior)时作为第二个参数传递的JavaScript对象的属性

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
|---|---|---|
| altIds | 数组 | 接受Munchkin ID字符串的数组。 启用后，将根据其Munchkin ID将所有Web活动复制到目标订阅。 |
| anonymizeip | 布尔值 | 为新访客匿名处理Marketo中记录的IP地址。您可以通过检查您的`{Munchkin-Id}.mktoresp.com`域是否具有以下地址之一来确定您的订阅是否已配置了Munchkin V2： `192.28.144.124` `134.213.193.62` `192.28.147.68` `103.237.104.82`。 或者，您可以从unix shell运行以下脚本： nslookup {munchkin-id}.mktoresp.com | grep -E -c -e “(192.28.144.124,134.213.193.62,192.28.147.68,103.237.104.82)”如果命令输出“0”，则您的订阅不会配置Munchkin V2；如果命令输出1或更大，则配置该命令。 |
| apiOnly | 布尔值 | 如果设置为true，则`Munchkin.Init()`函数将不会调用`visitsWebPage`。 这对于需要完全控制每`visitsWebPage`个事件的单页Web应用程序非常有用。 |
| asyncOnly | 布尔值 | 如果设置为true，则异步发送XMLHttpRequest的。 默认值为false。 |
| clickTime | 整数 | 设置单击后要阻止的时间以允许点击跟踪请求（以毫秒为单位）。 减少此项会降低点击跟踪的准确性。 默认值为350毫秒。 |
| cookieAnon | 布尔值 | 如果设置为false，则阻止跟踪和Cookie创建新的匿名潜在客户。 商机具有Cookie，在填写Marketo表单或通过从Marketo电子邮件点进来进行跟踪。 默认值为true。 |
| cookieLifeDays | 整数 | 将任何新创建的Munchkin跟踪Cookie的过期日期设置为将来的此天数。 默认值为730天（2年）。 |
| customname | 字符串 | 自定义页面名称。 仅供系统使用。 |
| domainLevel | 整数 | 设置设置Cookie的域属性时要使用的页面域中的部分数。例如，假设当前页面域为“www.example.com”。domainLevel： 2将Cookie域属性设置为“.example.com”domainLevel： 3将Cookie域属性设置为“。www.example.com”。Background：Munchkin将自动管理特定的双字母顶级域。 在正常情况下，如果顶级域是三个字母，则默认使用两部分。 例如，“www.example.com”，最右侧的两个部分用于设置Cookie“.example.com”。对于两个字母的国家代码（例如“.jp”、“.us”、“.cn”和“.uk”），代码默认为三个部分。 例如，“www.example.co.jp”将使用三个最右边的域部分“.example.co.jp”。如果域模式需要不同的行为，则必须使用`domainLevel`参数指定此行为。 |
| domainSelectorV2 | 布尔值 | 如果设置为true，则使用改进的方法来确定如何设置Cookie域属性。 |
| httpsOnly | 布尔值 | 默认为false。 如果设置为true，则在通过https提供跟踪的页面时，会将Cookie设置为使用安全设置。 |
| useBeaconAPI | 布尔值 | 默认为false。 当设置为true时，使用信标API而不是XMLHttpRequest发送非阻塞请求。 如果浏览器不支持此API，则Munchkin回退为使用XMLHttpRequest。 |
| wsInfo | 字符串 | 采用字符串来定位工作区。 此工作区ID可通过在管理员>集成> Munchkin菜单中选择Workspace来获取。 此设置仅适用于匿名潜在客户记录的初始创建。 一旦为该潜在客户记录建立了Munchkin Cookie值，将无法使用wsInfo参数更改其分区。 由于此设置仅影响匿名潜在客户，因此它仅与Web报表中特定于分区的匿名访客相关。 |

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

此示例强制从主线程异步发送所有XMLHttpRequest。

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
