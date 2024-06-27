---
title: 快速入门
description: Marketo EngageAPI快速入门
exl-id: 78c44c32-4e59-4d55-a45c-ef0d7dac814d
source-git-commit: 82bea1ab3d0d83a8867bb7efefb828ce2d92747c
workflow-type: tm+mt
source-wordcount: '1243'
ht-degree: 0%

---

# 快速入门

Marketo Engage是一个营销自动化平台，它使营销人员能够向潜在客户和客户管理个性化的多渠道项目和营销活动。 可以使用集成点扩展Marketo Engage平台。 在下面您可以找到核心实体及其关系。

启用本机同步后，无法通过REST API访问以下对象：公司、Opportunity、Opportunity角色、Sales Person

![数据模型](assets/data_model.png)

## 人员（潜在客户）

人员是任何营销自动化平台的基础。 在Marketo中，所有非销售人员记录都称为潜在客户，无论从销售角度看他们是否被指定为潜在客户、潜在客户、嫌疑人、联系人等。 潜在客户对象随附了一组 [标准字段](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Leads/operation/getLeadFieldsUsingGET) 例如电子邮件、名字和姓氏。 可以向潜在客户对象类型添加其他字段，以扩展与系统中记录关联的信息类型。 自定义属性可以像标准字段一样读取和写入。 可在Marketo中找到完整的字段列表 **[!UICONTROL Admin]** > **[!UICONTROL Field Management]** 菜单。 潜在客户在Marketo中通过id字段进行唯一标识。 必须在系统外部强制实施其他唯一键。

相关API： [REST](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Leads)， [SOAP](soap-api/leads.md)， [JavaScript](javascript-api/lead-tracking.md#lead-tracking-api)

## 活动

潜在客户可通过几种方式与您的组织进行交互。 潜在客户可以访问您公司网站上的某个页面、参加贸易展览或下载白皮书。 其中每个操作都可以在Marketo中捕获，以帮助营销人员更好地了解潜在客户执行的活动以及何时可以协调及时且相关的通信。 活动始终按leadId与潜在客户相关联。

您可以定义自己的自定义活动。 创建并发布自定义活动后，可以通过Marketo API添加自定义活动。 可以找到有关自定义活动的更多信息 [此处](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/administration/marketo-custom-activities/understanding-custom-activities).

相关API： [REST](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Activities)， [SOAP](soap-api/activities.md)， [JavaScript](javascript-api/lead-tracking.md#munchkin-behavior)

## 项目和营销策划

项目是一种机制，营销人员可通过该机制从一个中心位置组织其所有不同类型的营销工作。 例如，电子邮件爆炸就是程序。 潜在客户可以执行与给定项目相关的多种操作/活动，这些操作/活动将与该项目相关联。 这称为潜在客户进展。 电子邮件爆炸计划的示例进程将记录何时向潜在客户发送电子邮件、人员何时打开电子邮件或者他们是否点击了电子邮件中的链接。

创建营销活动是为了在项目中服务于特定目的和特定目标。 促销活动的一个示例可能是缩小一组潜在客户并向他们发送电子邮件直播，或者如果潜在客户点击了电子邮件直播计划中的链接，则通知销售代表采取跟进。

相关API： [REST](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Campaigns)， [SOAP](soap-api/getcampaignsforsource.md)

## 标记

标记是一种出于报告目的对数据进行分组的方法。 这些标识符使您能够分类数据并定义您想如何报告项目，以了解项目效果和ROI。

作为Marketo管理员，您可以在Marketo用户创建项目时创建可供选择的必需和可选标记类型。 其中每种标记类型的可能值均由您定义，并反映贵公司希望如何使用自定义标记进行报告。

例如，您可能希望创建一个具有多个标记值（例如，Northeast、Southeast）的自定义“区域”标记类型，以便分析哪个区域产生的商机最多。 或者，例如，您可以创建“所有者”标记类型，这使您能够评估和了解哪些项目所有者（例如，Maria、David或John）对创建商机和机会的影响最大。 可以找到有关标记的更多信息 [此处](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/core-marketo-concepts/programs/working-with-programs/understanding-tags).

相关API： [REST](https://developer.adobe.com/marketo-apis/api/asset/)， [SOAP](soap-api/gettags.md)

## 列表

列表允许营销人员组织潜在客户集合。 Marketo中有两种类型的列表，即静态列表和智能列表。 静态列表是营销人员可以选择添加或删除的固定潜在客户列表。 智能列表是基于一组指定特性的潜在客户动态集合。 智能列表的示例为“所有访问过我们网站上的定价页面的潜在客户”。 随着更多潜在客户访问定价页面，此智能列表将继续增长。 可以找到有关列表的详细信息 [此处](https://experienceleague.adobe.com/en/docs/marketo/using/home).

相关API： [REST](https://developer.adobe.com/marketo-apis/api/asset/#tag/Static-Lists)， [SOAP](soap-api/getimporttoliststatus.md)

## 机会

营销人员以机会的形式将销售线索投放给销售人员。 Opportunity表示潜在的销售交易，并与Marketo中的潜在客户或联系人以及组织相关联。 机会角色是给定商机与组织之间的交集。 商机角色与潜在客户在组织中的职能相关。

相关API： [REST](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Opportunities)， [SOAP](soap-api/getmobjects.md)

## 公司

组织(有时称为Marketo中的帐户)是指人员所属的组织。 在Marketo或Revenue Cycle Analytics (RCA)中使用ROI报表时，必须将人员与其组织和机会关联起来，以便确定适当的ROI归因。

相关API： [REST](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Companies)， [SOAP](soap-api/leads.md)

## 资源

Assets是指在项目中使用的登陆页面、电子邮件、表单和图像。 Assets可以是给定项目的本地项目，也可以是全局项目。 全局资产可在任何项目中使用。

相关API： [REST](https://developer.adobe.com/marketo-apis/api/asset/)

## 令牌

利用令牌，营销人员可以使用资产对消息进行个性化并在流程操作中添加逻辑。 有用于整个系统、项目、潜在客户和公司的令牌。 潜在客户令牌的示例为 {{lead.First Name}}. 此令牌可放置在电子邮件中以显示商机的名字。

在Marketo中，在项目或文件夹级别定义的令牌称为“我的令牌”。 我的令牌可以是三种类型之一：本地、继承或覆盖。

在特定营销活动文件夹或项目群中本地创建的“我的令牌”可用于该特定项目或营销活动文件夹（本地）。 在营销活动文件夹级别创建的“我的令牌”可用于该营销活动文件夹中包含的所有项目（已继承）。 在程序级别使用自定义值修改的“我的令牌”不会更改程序文件夹级别令牌的父“我的令牌”值（已覆盖）。

我的令牌使用命名约定 {{my.My Token}}, with the word "my" added to the beginning of the token name. For example, if you create a Date type My Token with the name EventDate, the name of the token is {{my.EventDate}}. 有关“我的令牌”的更多信息，请访问 [此处](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/core-marketo-concepts/programs/tokens/understanding-my-tokens-in-a-program).

相关API： [REST](https://developer.adobe.com/marketo-apis/api/asset/#tag/Tokens)， [SOAP](soap-api/getcampaignsforsource.md)

## 自定义对象

Marketo自定义对象允许在您的Marketo潜在客户与自定义对象记录之间创建一对多或多对多关系(Edge-Bridge-Edge)。 创建并发布Marketo自定义对象后，您可以通过Marketo API对自定义对象执行CRUD操作。 可以找到有关自定义对象创建的更多信息 [此处](https://experienceleague.adobe.com/en/docs/marketo/using/home). 在将新记录添加到自定义对象时，您可以使用智能列表触发器做出响应。 您还可以在智能列表（分段）中使用自定义对象数据作为过滤器，或者在电子邮件中使用 [电子邮件脚本](email-scripting.md).

相关API： [REST](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Custom-Objects)， [SOAP](soap-api/custom-objects.md)

## 销售人员

如果没有启用本机CRM集成，则可以在Marketo中管理销售人员记录和潜在客户关系。 这些记录包含有关销售人员的基本信息，如姓名、电子邮件和职务，当销售线索归某人所有时，可用于在Marketo中进行筛选和标记。 与销售人员的关系通过“externalSalesPersonId”字段在潜在客户级别进行管理，该字段必须通过 [同步潜在客户](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Leads/operation/syncLeadUsingPOST) API。

相关API： [REST](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Sales-Persons)
