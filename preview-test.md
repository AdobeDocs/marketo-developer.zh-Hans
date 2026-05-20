---
title: EXL预览测试
description: 用于测试扩展预览的Adobe EXL Markdown语法示例。
source-git-commit: 87d2584ed0ef2c1fa219f2a3ad120c91dc5491e0
workflow-type: tm+mt
source-wordcount: '348'
ht-degree: 11%

---


# EXL预览测试

## 警报块

>[!NOTE]
>
>这是便条。 使用注释可获取读者应了解的补充信息。

>[!TIP]
>
>这是个小贴士。 使用提示获取可选但有用的信息。

>[!IMPORTANT]
>
>这是一个重要警报。 用于读者不能忽略的信息。

>[!WARNING]
>
>这是警告。 用于了解有关潜在问题的信息。

>[!CAUTION]
>
>这是警告。 用于获取有关潜在风险的信息。

>[!ADMIN]
>
>这是管理员警报。 用于仅限管理员的内容。

>[!AVAILABILITY]
>
>这是可用性说明。 有关功能可用性的详细信息。

>[!PREREQUISITES]
>
>这是一个先决条件块。 列出读者需要什么才能开始。

## 阴影框

>[!BEGINSHADEBOX “可选标题”]

此内容以灰色背景显示。 使用阴影框以可视方式将相关内容分组。

您可以包含列表：

- 项目一
- 项目二
- 项目三

>[!ENDSHADEBOX]

>[!BEGINSHADEBOX]

不带标题的阴影框。

>[!ENDSHADEBOX]

## 可折叠部分

+++单击以展开 — 基本示例

在用户选择标题之前，此内容是隐藏的。

您可以在此处包含任何内容，包括代码块：

```javascript
const example = 'hello world';
console.log(example);
```

+++

+++高级配置

为可选内容或高级内容使用可折叠的部分，否则这些内容将会使主流混乱。

| 设置 | 值 | 描述 |
| --- | --- | --- |
| timeout | 30 | 请求超时前的秒数 |
| 重试 | 3 | 重试次数 |

{style="table-layout:auto"}

+++

## 情景帮助

上下文帮助在预览中隐藏。 看！
>[!CONTEXTUALHELP]
>id="models_insights_undefinedchannels"
>title="未定义渠道"
>abstract="未定义渠道会包含在内，但不会归因任何转化。"

## 嵌入式视频

>[!VIDEO](https://video.tv.adobe.com/v/3427028/?quality=12&learn=on)

## 本地化宏

使用[!DNL Marketo]将产品名称换行，以便它们不会本地化。

对UI元素标签使用&#x200B;**[!UICONTROL Admin]** > **[!UICONTROL LaunchPoint]**。

组合示例：在[!DNL Adobe Analytics]中，选择&#x200B;**[!UICONTROL Workspace]** > **[!UICONTROL Create project]**。

## 徽章

[!BADGE Beta 版]{type=Informative}

[!BADGE 一般可用]{type=Positive}

[!BADGE 已弃用]{type=Negative}

[!BADGE 实验性]{type=Caution}

## 选项卡

>[!BEGINTABS]

>[!TAB 要求]

>[!IMPORTANT]
>
>您必须具有管理员权限才能完成此任务。

必填字段：

| 字段 | 类型 | 必需 |
| --- | --- | --- |
| 名称 | 字符串 | 是 |
| 电子邮件 | 字符串 | 是 |
| 角色 | 枚举 | 否 |

>[!TAB 步骤]

1. 打开Admin Console。
1. 选择&#x200B;**[!UICONTROL Users]** > **[!UICONTROL Add user]**。
1. 填写必填字段。
1. 选择 **[!UICONTROL Save]**。

>[!NOTE]
>
>更改会立即生效。

>[!TAB 结果]

新用户会收到一封欢迎电子邮件，其中包含用于设置其密码的链接。

- 链接将在24小时后过期。
- 用户可以从登录页面请求新链接。

>[!ENDTABS]

## 代码块

```json
{
  "name": "example",
  "version": "1.0.0",
  "enabled": true
}
```

```javascript
function greet(name) {
  return `Hello, ${name}!`;
}
```

## 表格

| 第一列 | 第二列 | 栏目3 |
| --- | --- | --- |
| [!UICONTROL Row 1]，单元格1 | 行1，单元格2 | [!DNL Row 1, cell 3] |
| 行2，单元格1 | 行2，单元格2 | 行2，单元格3 |

