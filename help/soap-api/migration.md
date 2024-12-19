---
title: 迁移到REST API
feature: SOAP
description: 从SOAP迁移到REST API
source-git-commit: 4c410afbe8ccfff644376670b8990fac671459eb
workflow-type: tm+mt
source-wordcount: '646'
ht-degree: 0%

---


# 迁移到REST API

Marketo EngageSOAP API将在2025年10月31日后停用。 应在此日期之前停用所有使用SOAP API的现有集成，或将其迁移到[Marketo EngageREST API](https://experienceleague.adobe.com/en/docs/marketo-developer/marketo/rest/rest-api)，以避免服务中断。

## 迁移

与[REST AP](https://experienceleague.adobe.com/en/docs/marketo-developer/marketo/rest/rest-api)I相比，SOAP API支持的用例范围有限。在确定要映射用例的端点时，您应遵循[Marketo集成最佳实践](https://experienceleague.adobe.com/en/docs/marketo-developer/marketo/rest/marketo-integration-best-practices)

[参考体系结构](https://experienceleague.adobe.com/en/docs/marketo-developer/marketo/rest/reference-architectures)可用于[CRM同步](https://experienceleague.adobe.com/docs/marketo-developer/assets/sync-architecture-whitepaper.pdf?lang=en)和[Data Warehouse导出](https://experienceleague.adobe.com/docs/marketo-developer/assets/reference_architecture.pdf?lang=en)用例。

## 身份验证

[身份验证文档](https://experienceleague.adobe.com/en/docs/marketo-developer/marketo/rest/authentication)

Marketo REST API使用基于OAuth 2.0的身份验证以及客户端凭据授权类型。 访问令牌在创建后一小时内有效。

## 潜在客户

[潜在客户API文档](https://experienceleague.adobe.com/en/docs/marketo-developer/marketo/rest/lead-database/leads)

SOAP API支持潜在客户数据同步、[Munchkin Cookie关联](https://experienceleague.adobe.com/en/docs/marketo-developer/marketo/javascriptapi/leadtracking/lead-tracking)以及潜在客户合并。 如果您的应用程序调用SOAP syncLead方法并设置`marketoCookie`参数，则可以通过以下任一方式进行迁移：

1. 使用[同步潜在客户](https://developer.adobe.com/marketo-apis/api/mapi/#operation/syncLeadUsingPOST) REST方法，后跟[关联的潜在客户](https://developer.adobe.com/marketo-apis/api/mapi/#operation/associateLeadUsingPOST)
2. 您可以调用[提交表单](https://experienceleague.adobe.com/en/docs/marketo-developer/marketo/rest/lead-database/leads&quot;%20\l%20&quot;submit-form)，但需要配置一些Marketing Assets以及与[Forms API](https://experienceleague.adobe.com/en/docs/marketo-developer/marketo/rest/assets/forms)进行交互

使用`foreignSysPersonId`键类型的应用程序应使用自定义潜在客户字段来表示此外部标识符，并使用[同步潜在客户](https://experienceleague.adobe.com/en/docs/marketo-developer/marketo/rest/lead-database/leads#create-and-update)或[批量潜在客户导入](https://experienceleague.adobe.com/en/docs/marketo-developer/marketo/rest/bulk-import/bulk-lead-import) REST方法。

| SOAP方法 | REST方法 |
| --- | --- |
| [getLead](https://experienceleague.adobe.com/en/docs/marketo-developer/marketo/soap/leads/getlead) | [按ID获取潜在客户](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getLeadByIdUsingGET)，[按筛选器类型获取潜在客户](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getLeadsByFilterUsingGET) |
| [getMultipleLeads](https://experienceleague.adobe.com/en/docs/marketo-developer/marketo/soap/leads/getmultipleleads) | [按ID获取潜在客户](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getLeadByIdUsingGET)，[按筛选器类型获取潜在客户](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getLeadsByFilterUsingGET)，[按计划ID获取潜在客户](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getLeadsByProgramIdUsingGET)，[按列表ID获取潜在客户](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getLeadsByListIdUsingGET)，[批量潜在客户导出](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Bulk-Export-Leads) |
| [mergeLeads](https://experienceleague.adobe.com/en/docs/marketo-developer/marketo/soap/leads/mergeleads) | [合并潜在客户](https://developer.adobe.com/marketo-apis/api/mapi/#operation/mergeLeadsUsingPOST) |
| [syncLead](https://experienceleague.adobe.com/en/docs/marketo-developer/marketo/soap/leads/synclead) | [同步潜在客户](https://developer.adobe.com/marketo-apis/api/mapi/#operation/syncLeadUsingPOST) [提交表单](https://developer.adobe.com/marketo-apis/api/mapi/#operation/SubmitFormUsingPOST) [关联潜在客户](https://developer.adobe.com/marketo-apis/api/mapi/#operation/associateLeadUsingPOST) |
| [syncMultipleLeads](https://experienceleague.adobe.com/en/docs/marketo-developer/marketo/soap/leads/syncmultipleleads) | [同步潜在客户](https://developer.adobe.com/marketo-apis/api/mapi/#operation/syncLeadUsingPOST) [批量导入](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Bulk-Import-Leads) |

## M对象

M Objects是一个通用概念，用于支持导出Opportunity Attribution数据以进行外部分析，它使用三种对象类型：Opportunity 、 Opportunity Roles和Programs。

REST文档：

- [机会](https://experienceleague.adobe.com/en/docs/marketo-developer/marketo/rest/lead-database/opportunities)
- [角色](https://experienceleague.adobe.com/en/docs/marketo-developer/marketo/rest/lead-database/opportunity-roles)
- [程序](https://experienceleague.adobe.com/en/docs/marketo-developer/marketo/rest/assets/programs)

| SOAP方法 | REST方法 |
| --- | --- |
| [deleteMObjects](https://experienceleague.adobe.com/en/docs/marketo-developer/marketo/soap/marketo-objects/deletemobjects) | [删除机会](https://developer.adobe.com/marketo-apis/api/mapi/#operation/deleteOpportunitiesUsingPOST)，[删除机会角色](https://developer.adobe.com/marketo-apis/api/mapi/#operation/deleteOpportunityRolesUsingPOST) |
| [describeMObjects](https://experienceleague.adobe.com/en/docs/marketo-developer/marketo/soap/marketo-objects/describemobject) | [描述机会](https://developer.adobe.com/marketo-apis/api/mapi/#operation/describeUsingGET_4)，[描述机会角色](https://developer.adobe.com/marketo-apis/api/mapi/#operation/describeOpportunityRoleUsingGET) |
| [getMObjects](https://experienceleague.adobe.com/en/docs/marketo-developer/marketo/soap/marketo-objects/getmobjects) | [获取机会](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getOpportunitiesUsingGET)，[获取机会角色](https://developer.adobe.com/marketo-apis/api/mapi/#operation/describeOpportunityRoleUsingGET) |
| [listMObjects](https://experienceleague.adobe.com/en/docs/marketo-developer/marketo/soap/marketo-objects/listmobjects) | 不适用 |
| [syncMObjects](https://experienceleague.adobe.com/en/docs/marketo-developer/marketo/soap/marketo-objects/syncmobjects) | [同步机会](https://developer.adobe.com/marketo-apis/api/mapi/#operation/syncOpportunitiesUsingPOST)，[同步机会角色](https://developer.adobe.com/marketo-apis/api/mapi/#operation/syncOpportunityRolesUsingPOST) |
| [getChannels](https://experienceleague.adobe.com/en/docs/marketo-developer/marketo/soap/programs/getchannels) | [获取渠道](https://developer.adobe.com/marketo-apis/api/asset/#operation/getAllChannelsUsingGET) |
| [getTags](https://experienceleague.adobe.com/en/docs/marketo-developer/marketo/soap/programs/gettags) | [获取标记类型](https://developer.adobe.com/marketo-apis/api/asset/#operation/getTagTypesUsingGET)，[按名称获取标记](https://developer.adobe.com/marketo-apis/api/asset/#operation/getTagByNameUsingGET) |

## 静态列表

SOAP API中的静态列表用例仅限于摄取成员资格和潜在客户数据，以及可以通过[添加到列表](https://developer.adobe.com/marketo-apis/api/mapi/#operation/addLeadsToListUsingPOST)、[批量导入潜在客户](https://experienceleague.adobe.com/en/docs/marketo-developer/marketo/rest/bulk-import/bulk-lead-import)或[从列表](https://developer.adobe.com/marketo-apis/api/mapi/#operation/removeLeadsFromListUsingDELETE) REST方法删除成员资格来删除成员资格。

| SOAP方法 | REST方法 |
| --- | --- |
| [getImportToListStatus](https://experienceleague.adobe.com/en/docs/marketo-developer/marketo/soap/static-lists/getimporttoliststatus) | [批量导入潜在客户](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Bulk-Import-Leads) |
| [importToList](https://experienceleague.adobe.com/en/docs/marketo-developer/marketo/soap/static-lists/importtolist) | [添加到列表](https://developer.adobe.com/marketo-apis/api/mapi/#operation/addLeadsToListUsingPOST) [批量导入潜在客户](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Bulk-Import-Leads) |
| [listOperation](https://experienceleague.adobe.com/en/docs/marketo-developer/marketo/soap/static-lists/listoperation) | [从列表中删除](https://developer.adobe.com/marketo-apis/api/mapi/#operation/removeLeadsFromListUsingDELETE) |

## 活动

SOAP API仅支持检索活动。

REST文档：

- [同步活动](https://experienceleague.adobe.com/en/docs/marketo-developer/marketo/rest/lead-database/activities)
- [批量活动提取](https://experienceleague.adobe.com/en/docs/marketo-developer/marketo/rest/bulk-extract/bulk-activity-extract)

| SOAP方法 | REST方法 |
| --- | --- |
| [getLeadActivity](https://experienceleague.adobe.com/en/docs/marketo-developer/marketo/soap/activities/getleadactivity) | [批量导出活动](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Bulk-Export-Activities) [获取潜在客户活动](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getLeadActivitiesUsingGET) |
| [getLeadChanges](https://experienceleague.adobe.com/en/docs/marketo-developer/marketo/soap/activities/getleadchanges) | [批量导出活动](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Bulk-Export-Activities) [获取潜在客户更改](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getLeadChangesUsingGET) |

## 营销活动

REST文档：

- [智能营销活动](https://experienceleague.adobe.com/en/docs/marketo-developer/marketo/rest/assets/smart-campaigns“%20\h%20HYPERLINK%20”https://experienceleague.adobe.com/en/docs/marketo-developer/marketo/rest/assets/smart-campaigns)

SOAP API仅支持智能营销活动的三个用例：[触发潜在客户以符合可请求的Smart Campaign](https://experienceleague.adobe.com/en/docs/marketo-developer/marketo/rest/assets/smart-campaigns#trigger)、检索这些可请求的营销活动以及[计划将来运行Smart Campaign](https://experienceleague.adobe.com/en/docs/marketo-developer/marketo/rest/assets/smart-campaigns#schedule)。

| SOAP方法 | REST方法 |
| --- | --- |
| [getCampaignsForSource](https://experienceleague.adobe.com/en/docs/marketo-developer/marketo/soap/campaigns/getcampaignsforsource) | [获取智能营销活动](https://developer.adobe.com/marketo-apis/api/asset/#operation/getAllSmartCampaignsGET) |
| [requestCampaign](https://experienceleague.adobe.com/en/docs/marketo-developer/marketo/soap/campaigns/requestcampaign) | [请求营销活动](https://developer.adobe.com/marketo-apis/api/mapi/#operation/triggerCampaignUsingPOST) |
| [scheduleCampaign](https://experienceleague.adobe.com/en/docs/marketo-developer/marketo/soap/campaigns/schedulecampaign) | [计划营销活动](https://developer.adobe.com/marketo-apis/api/mapi/#operation/scheduleCampaignUsingPOST) |

## 自定义对象

REST文档：

- [自定义对象](https://experienceleague.adobe.com/en/docs/marketo-developer/marketo/rest/lead-database/custom-objects“%20\h%20HYPERLINK%20”https://experienceleague.adobe.com/en/docs/marketo-developer/marketo/rest/lead-database/custom-objects)

SOAP API仅支持对自定义对象执行CRUD操作。

| SOAP方法 | REST方法 |
| --- | --- |
| [deleteCustomObjects](https://experienceleague.adobe.com/en/docs/marketo-developer/marketo/soap/custom-objects/deletecustomobjects) | [删除自定义对象](https://developer.adobe.com/marketo-apis/api/mapi/#operation/deleteCustomObjectsUsingPOST) |
| [getCustomObjects](https://experienceleague.adobe.com/en/docs/marketo-developer/marketo/soap/custom-objects/getcustomobjects) | [获取自定义对象](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getCustomObjectsUsingGET) |
| [syncCustomObjects](https://experienceleague.adobe.com/en/docs/marketo-developer/marketo/soap/custom-objects/synccustomobjects) | [同步自定义对象](https://developer.adobe.com/marketo-apis/api/mapi/#operation/syncCustomObjectsUsingPOST) [批量导入自定义对象](https://experienceleague.adobe.com/en/docs/marketo-developer/marketo/rest/bulk-import/bulk-custom-object-import) |