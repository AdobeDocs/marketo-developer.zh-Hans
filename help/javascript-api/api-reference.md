---
title: Munchkin API参考
description: 使用Munchkin Javascript API自定义Munchkin数据。
feature: Javascript
source-git-commit: c6c0a492ede415471e10efb6213eb3f590e63ebe
workflow-type: tm+mt
source-wordcount: '414'
ht-degree: 7%

---


# Munchkin API参考

Munchkin提供了几种可通过Javascript手动调用的函数。 这些功能允许对浏览器事件进行自定义跟踪，例如视频播放或非链接上的点击次数。

## 功能

Munchkin API包含以下函数： `init`、`createTrackingCookie`、`munchkinFunction`。

### Munchkin.init()

必须在任何其他函数之前调用`Munchkin.init()`。 它在当前页面上设置Munchkin以将活动发送到特定实例，并为当前页面生成“访问网页”活动。

| 参数名称 | 可选/必填 | 类型 | 描述 |
| --- | --- | --- | --- |
| Munchkin ID | 必需 | 字符串 | 可在“管理员”>“集成”>“Munchkin”菜单下找到Munchkin帐户ID。 设置要将活动发送到的目标实例。 |
| [配置设置](configuration.md) | 可选 | 对象 | 为Munchkin启用替代行为设置。 |

```javascript
Munchkin.init('299-BYM-827');
```

### Munchkin.createTrackingCookie()

调用时，将检查浏览器中是否存在`_mkto_trk` Cookie，如果不存在，将创建一个。 如果`cookieAnon`设置为false，则这对于在特定操作期间跟踪用户很有用，例如注册或下载资源。

| 参数名称 | 可选/必填 | 类型 | 描述 |
| --- | --- | --- | --- |
| forceCreate | 必需 | 布尔值 | 即使`cookieAnon`设置为false，也创建Cookie。 |


```javascript
Munchkin.createTrackingCookie(true);
```

### Munchkin.munchkinFunction()

用于生成自定义跟踪行为，如视频播放器播放和暂停，或生成非标准导航的页面访问次数，如哈希代码。

| 参数名称 | 可选/必填 | 类型 | 描述 |
| --- | --- | --- | --- |
| 函数类型 | 必需 | 字符串 | 确定要记录的活动。 允许的值： `visitWebPage`、`clickLink`、`associateLead` |
| 数据 | 必需 | 对象 | 包含要记录的活动的数据。 |

#### visitWebPage

使用`visitWebPage`调用`munchkinFunction()`会将当前用户的“访问”活动发送到Marketo。 您可以自定义与第二个参数中的数据对象一起发送的URL和`querystring`。

| 数据属性名称 | 可选/必填 | 类型 | 描述 |
| --- | --- | --- | --- |
| url | 必需 | 字符串 | 用于记录页面访问的URL文件路径。  此值附加到当前域名以创建完整页面名称。 例如，如果url为`/index.html`，域名为`www.example.com`，则访问的页面将记录为`www.example.com/index.html`。 |
| 参数 | 可选 | 字符串 | 要记录的所需参数的查询字符串。 |

例如：`foo=bar&biz=baz`。

```javascript
Munchkin.munchkinFunction('visitWebPage', {
        'url': '/Football/Team/Seahawks',
        'params': 'defense=legion_of_boom&qb=wilson'
    }
);
```

#### clickLink

使用`clickLink`调用`munchkinFunction()`会将当前用户的点击活动发送至Marketo。 您可以使用数据对象中的`href`属性自定义点击URL。

| 数据属性名称 | 可选/必填 | 类型 | 描述 |
| --- | --- | --- | --- |
| href | 必需 | 字符串 | 用于记录链接点击的URL文件路径。 此值附加到当前域名以创建完整链接。 |

例如，如果href为`/index.html`且域名为`www.example.com`，则链接点击将被记录为`www.example.com/index.html`。

```javascript
Munchkin.munchkinFunction('clickLink', {
        'href': '/Football/Team/Seahawks'
    }
);
```

#### 关联潜在客户

此方法已弃用，不再可用。
