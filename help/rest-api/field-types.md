---
title: 字段类型
feature: REST API
description: 包含定义、示例和格式的Marketo字段类型的完整列表，包括ISO 8601日期时间、文本区域限制、货币和布尔值。
exl-id: a0ba9e02-ed42-4be3-9cdd-a97fee9a726e
source-git-commit: 7557b9957c87f63c2646be13842ea450035792be
workflow-type: tm+mt
source-wordcount: '322'
ht-degree: 7%

---

# 字段类型

以下是Marketo中字段类型的描述。 有关字段类型的其他信息可在[此处](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/administration/field-management/custom-field-type-glossary)找到。 有关字段类型限制的其他信息可在[此处](https://nation.marketo.com/t5/knowledgebase/marketo-field-limits-by-field-type/ta-p/251613)找到。

| 字段类型 | 描述 | 示例 |
| --- | --- | --- |
| 日期时间 | 用于输入日期和时间。 遵循[W3C格式](https://www.w3.org/TR/NOTE-datetime) (ISO 8601)。 最佳实践是包括时区偏移。 完成日期加上小时和分钟： YYYY-MM-DDThh:mm:ssTZD，其中TZD为“+hh:mm”或“ — hh:mm”注意：对于`updatedAt`和`createdAt`，某些资产API返回“Z+0000”作为TZD。 | 2010-05-07T15:41:32-05:00 |
| 电子邮件 | 接受电子邮件地址的字符串字段 | <example@example.com> |
| 浮点值 | 一个数字字段，包含实数并且可以使用小数位。 | 10.4 |
| 整数 | 整数 | 10 |
| 公式 | 其值通过处理潜在客户记录上存在的其他字段的数据而生成的字段。 它们不会导出，并且无法在Smart Campaigns中使用。 | 查看此[文章](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/administration/field-management/create-and-use-a-concatenated-string-formula-field) |
| 百分比 | 以整数表示的百分比 | 30 |
| URL | 一个文本字段，用于限制对URL的输入，包括URL的协议。 | <http://example.com/> |
| 电话 | 电话号码 | 111-111-1111 |
| 文本区域 | 较长的文本。 | 支持最多30,000字节。 标准ASCII字符每个字符使用1个字节（最多允许30,000个字符）。 Unicode字符在每个字符中最多可以使用4字节(减少  字符数允许少于30,000个字符)。 |
| 字符串 | 较短的文本 | 文本最多255个字符 |
| 得分 | 可以使用“更改得分”流程步骤处理的整数字段 | 10 |
| 布尔值（以前为Checkbox） | 允许用户选择True（选中）或False（取消选中）值。 | True |
| 货币 | 浮动字段，表示为Marketo订阅选择的默认货币类型 | 10.40 |
| 日期 | 用于日期。 遵循W3C格式。 | 2010-05-07 |
| 参考 | 包含指向另一条记录的键（外键）的字符串字段。 | 联系公司 |
