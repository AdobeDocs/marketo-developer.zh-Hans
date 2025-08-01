---
title: 电子邮件脚本
feature: Email Programs
description: 电子邮件脚本概述
exl-id: ff396f8b-80c2-4c87-959e-fb8783c391bf
source-git-commit: 9012135dc7a295c2462574dad1aca2d06a9077ea
workflow-type: tm+mt
source-wordcount: '947'
ht-degree: 0%

---

# 电子邮件脚本

注意：强烈建议您阅读[Velocity用户指南](https://velocity.apache.org/engine/devel/user-guide.html)，以深入了解Velocity模板语言的行为。

[Apache Velocity](https://velocity.apache.org/)是一种基于Java构建的语言，专为模板化和编写HTML内容脚本而设计。 利用Marketo，可通过使用脚本令牌在电子邮件上下文中使用它。 这样可访问存储在商机和自定义对象中的数据，并允许在电子邮件中创建动态内容。 Velocity提供了标准的高级控制流，其中包含if/else、for和for each ，以允许对内容进行条件和迭代操作。 以下是一个打印问候语和正确问候语的简单示例：

```java
##check if the lead is male
#if(${lead.MarketoSocialGender} == "Male")
    ##if the lead is male, use the salutation 'Mr.'
    #set($greeting = "Dear Mr. ${lead.LastName},")
##check is the lead is female
#elseif(${lead.MarketoSocialGender} == "Female")
    ##if female, use the salutation 'Ms.'
    #set($greeting = "Dear Ms. ${lead.LastName},")
#else
    ##otherwise, use the first name
    #set($greeting = "Dear ${lead.FirstName},")
#end
##print the greeting and some content
${greeting}

    Lorem ipsum dolor sit amet...
```

## 变量

变量始终以“$”为前缀，并使用#set进行设置和更新：

```
#set($variable = "value")
```

随后，可以通过具有不同行为的多种不同引用类型检索其值：

```
$variable ##outputs 'value'
$variablename ##outputs '$variablename'
${variable}name ##outputs 'valuename'
```

还有静默引用表示法，其中在`!`之后有`$`包含。 通常，当velocity遇到未定义的引用时，表示该引用的字符串会保留在原处。 使用静默引用表示法，如果遇到未定义的引用，则不会发出任何值：

```
##Defined Reference

#set($foo = "bar")
$foo ##outputs "bar"

##Undefined Reference

##normal
$baz ##outputs "$baz"

##quiet
$!baz ##outputs nothing
```

有关如何引用变量的更多信息，请参阅[Apache用户指南](https://velocity.apache.org/engine/devel/user-guide.html#formal-reference-notation)。

## Velocity工具

Apache Velocity项目通过使用[Velocity工具](https://velocity.apache.org/tools/devel/apidocs/overview-summary.html)使功能可用。 这些只是Java对象的包装器，通过可供所有脚本使用的全局变量公开其方法。

- [AlternatorTool](https://velocity.apache.org/tools/devel/apidocs/org/apache/velocity/tools/generic/AlternatorTool.html)
- [ComparisonDateTool](https://velocity.apache.org/tools/devel/apidocs/org/apache/velocity/tools/generic/ComparisonDateTool.html)
- [转换工具](https://velocity.apache.org/tools/devel/apidocs/org/apache/velocity/tools/generic/ConversionTool.html)
- [日期工具](https://velocity.apache.org/tools/devel/apidocs/org/apache/velocity/tools/generic/DateTool.html)
- [显示工具](https://velocity.apache.org/tools/devel/apidocs/org/apache/velocity/tools/generic/DisplayTool.html)
- [数学工具](https://velocity.apache.org/tools/devel/apidocs/org/apache/velocity/tools/generic/MathTool.html)
- [NumberTool](https://velocity.apache.org/tools/devel/apidocs/org/apache/velocity/tools/generic/NumberTool.html)
- [EscapeTool](https://velocity.apache.org/tools/devel/apidocs/org/apache/velocity/tools/generic/EscapeTool.html)
- [循环工具](https://velocity.apache.org/tools/devel/apidocs/org/apache/velocity/tools/generic/LoopTool.html)

例如，要使用`ComparisonDateTool`中的方法，如果是从脚本令牌中的`$date`变量访问：

```
#set($birthday = $convert.parseDate("2015-08-07","yyyy-MM-dd"))
##use whenIs to determine how many days away it is
$date.whenIs($birthday).days ##outputs 1
```

## 创建脚本令牌

使用电子邮件脚本令牌在电子邮件中包含Velocity脚本。 这些任务可以在营销活动中的营销文件夹或项目群中创建。 对于要在电子邮件中使用的令牌，电子邮件必须是拥有该令牌或从营销文件夹继承令牌的程序的子项。 要创建令牌，请导航到文件夹或程序，然后选择[!UICONTROL My Tokens]选项卡。 从右侧菜单中，将“Email Script”选项拖动到令牌列表中

![脚本令牌](assets/script-token.png)

在此处，您可以编辑令牌的名称，并通过[!UICONTROL Click to Edit]选项打开编辑器：

![编辑脚本](assets/script-edit.png)

进入编辑器后，您可以创建一个脚本，以便访问可脚本访问对象中的所有变量。 要从对象获取字段引用，请将其从右侧树拖入脚本中：

![编辑脚本令牌](assets/edit-script-token.png)

## 脚本嵌入和测试

在程序“我的令牌”中定义脚本后，即可使用Marketo电子邮件编辑器在给定电子邮件中引用该脚本。

![电子邮件脚本](assets/email-script-marketo-email.png)

您可以使用Marketo Email Designer中的[!UICONTROL Send Sample Email]电子邮件操作测试脚本。 要使脚本正确处理，您必须在[!UICONTROL Lead]字段中选择要模拟的现有潜在客户。 如果您正在使用`$TriggerObject`进行测试，则可以通过[!UICONTROL Trigger]参数选择触发对象。 这会使用该类型的最近更新对象的数据作为`$TriggerObject`变量。

![测试电子邮件脚本](assets/velocity-test.png)

您还可以使用[!UICONTROL Email Preview]来测试您的脚本。 为此，您必须选择&#x200B;**[!UICONTROL View As: Lead Detail]**，然后从可用的静态列表中选择潜在客户。 这还有另一个好处，即输出脚本执行期间可能发生的任何异常：

![查看电子邮件为](assets/view-as.png)

## 有用的提示

给定电子邮件中所有电子邮件脚本令牌的组合长度不得超过100,000字节。 此限制与令牌字符串本身的总长度有关（而不是令牌扩展后的总长度）。

- 电子邮件脚本中引用的变量必须存在于Marketo中且位于脚本可用的某个对象上。
- 您可以引用来自本机集成CRM的第一级和第二级自定义对象，这些自定义对象直接连接到Lead或Contact，但不包括第三级自定义对象。 自定义对象不能是潜在客户或公司的父级
- 对于Marketo自定义对象，您可以引用具有父子关系的二级自定义对象。 例如`Lead <- Parent <- Child`。 您无法引用具有Edge-Bridge关系的第二级自定义对象。 例如，`Lead <- Bridge -> Edge`
- 您可以引用连接到Lead、Contact或Account的自定义对象，但不能引用多个对象。
- 只能通过单个连接、潜在客户、联系人或帐户引用自定义对象
- 您必须在脚本编辑器中选中正在使用的字段的框，否则这些字段将不会处理
- 对于每个自定义对象，每个人员/联系人最近更新的10条记录在运行时可用，并且按照从最近更新（在0时）到最旧更新（在9时）的顺序排列。 您可以按照说明[增加](https://experienceleague.adobe.com/zh-hans/docs/marketo/using/product-docs/administration/email-setup/change-custom-object-retrieval-limits-in-velocity-scripting)可用的记录数。
- 如果电子邮件中包含多个电子邮件脚本，则它们将自上而下执行。 在第一个要执行的脚本中定义的变量的范围将在后续脚本中可用。
- 工具引用： [https://velocity.apache.org/tools/2.0/index.html](https://velocity.apache.org/tools/2.0/index.html)
- 有关包含换行字符“\\n”或“\\r\\n”的令牌的注释。 通过发送示例或批量促销活动发送电子邮件时，令牌中的换行字符会被替换为空格。 通过触发器营销活动发送电子邮件时，新行字符保持不变。
- 要确保正确解析URL，应将整个路径设置为变量并打印，且变量不应在URL引用内打印。 必须包含协议(http://或https://)，并且必须与URL的其余部分分开。 该URL还必须属于完全格式化的锚点(<a>)标记。 为了跟踪链接，脚本必须输出格式完整的锚点标记。 如果链接从for或foreach循环中输出，则不会跟踪这些链接。

```html
<!-- Correct -->
#set($url = "www.example.com/${object.id}")
<a href="http://${url}">Link Text</a>

<!-- Correct -->
<a href="http://www.example.com/${object.id}">Link Text</a>

<!-- Incorrect -->
<a href="${url}">Link Text</a>

<!-- Incorrect -->
<a href="{{my.link}}">Link Text</a>

<!-- Incorrect -->
<a href="http://{{my.link}}">Link Text</a>
```
