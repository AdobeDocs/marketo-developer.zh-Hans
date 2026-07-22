---
title: Munchkin API参考
description: 使用Munchkin Javascript API跟踪具有init、createTrackingCookie和munchkinFunction方法的页面访问次数、链接点击量和自定义事件。
feature: Munchkin Tracking Code, Javascript
exl-id: e9727691-5501-4223-bc98-2b4bacc33513
TQID: https://experienceleague.adobe.com/s97x6wVZijnnxZwS7HMIkQAKlxXkcfPXuSZG4KjXGoc
product_v2:
  - id: b27e5950-9033-45ac-9f86-eb22e567f615
feature_v2:
  - id: b3b8a63f-51fc-40f6-a7d2-a31c5d49fb45
role_v2:
  - id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
source-git-commit: 3e6d310c5aec1a3435424fb122b71d825db5af0e
workflow-type: tm+mt
source-wordcount: 414
ht-degree: 7%

---

# Munchkin API参考

Munchkin提供了JavaScript功能，可用于对浏览器事件进行自定义跟踪。 例如，您可以跟踪非链接元素的视频播放或单击情况。

## 功能

Munchkin API包含以下函数：

- `init`
- `createTrackingCookie`
- `munchkinFunction`

<a name="munchkin_init"></a>

### Munchkin.init()

必须在任何其他函数之前调用`Munchkin.init()`。 它在当前页面上设置Munchkin以将活动发送到特定实例，并为当前页面生成“访问网页”活动。

| 参数名称 | 可选/必填 | 类型 | 描述 |
| --- | --- | --- | --- |
| Munchkin ID | 必需 | 字符串 | 可在管理员>集成> Munchkin菜单下找到Munchkin帐户ID。 设置要将活动发送到的目标实例。 |
| [配置设置](configuration.md) | 可选 | 对象 | 为Munchkin启用替代行为设置。 |

```javascript
Munchkin.init('299-BYM-827');
```

### Munchkin.createTrackingCookie()

`Munchkin.createTrackingCookie()`检查浏览器中是否存在`_mkto_trk` Cookie。 如果该Cookie不存在，则函数将创建一个。

当`cookieAnon`设置为false时，使用此函数在特定操作（如注册或下载资源）期间跟踪用户。

| 参数名称 | 可选/必填 | 类型 | 描述 |
| --- | --- | --- | --- |
| forceCreate | 必需 | 布尔值 | 即使`cookieAnon`设置为false，也创建Cookie。 |

```javascript
Munchkin.createTrackingCookie(true);
```

### Munchkin.munchkinFunction()

使用`Munchkin.munchkinFunction()`创建自定义跟踪行为。 例如，跟踪视频播放器活动或来自非标准导航（如哈希更改）的页面访问。

| 参数名称 | 可选/必填 | 类型 | 描述 |
| --- | --- | --- | --- |
| 函数类型 | 必需 | 字符串 | 确定要记录的活动。 允许的值： `visitWebPage`、`clickLink`、`associateLead` |
| 数据 | 必需 | 对象 | 包含要记录的活动的数据。 |

#### visitWebPage

使用`visitWebPage`调用`munchkinFunction()`会将当前用户的“访问”活动发送到Marketo。 使用第二个参数中的数据对象自定义URL和`querystring`。

| 数据属性名称 | 可选/必填 | 类型 | 描述 |
| --- | --- | --- | --- |
| url | 必需 | 字符串 | 用于记录页面访问的URL文件路径。  此值附加到当前域名以创建完整页面名称。 例如，如果url为`/index.html`，域名为`www.example.com`，则访问的页面将记录为`www.example.com/index.html`。 |
| params | 可选 | 字符串 | 要记录的所需参数的查询字符串。 |

例如：`foo=bar&biz=baz`。

```javascript
Munchkin.munchkinFunction('visitWebPage', {
        'url': '/Football/Team/Seahawks',
        'params': 'defense=legion_of_boom&qb=wilson'
    }
);
```

#### clickLink

使用`clickLink`调用`munchkinFunction()`会将当前用户的点击活动发送至Marketo。 使用数据对象中的`href`属性自定义点击URL。

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
