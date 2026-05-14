---
title: 获取潜在客户API更新
feature: REST API
description: 了解“获取潜在客户活动”和“获取潜在客户更改端点”的限制更改。
source-git-commit: e71bcf289229867bc969345d79c8f014761aaaf9
workflow-type: tm+mt
source-wordcount: '356'
ht-degree: 0%

---

# 获取潜在客户API更新

从2026年9月30日开始，对[获取潜在客户活动](https://developer.adobe.com/marketo-apis/api/mapi#operation/getLeadActivitiesUsingGET)或[获取潜在客户更改](https://developer.adobe.com/marketo-apis/api/mapi#operation/getLeadChangesUsingGET)端点（包括`listId`参数）的调用将失败，如果目标列表包含10,000个或更多潜在客户，错误代码为1003，表示目标静态列表具有过多记录。 最近进行了一个或多个API调用，它们将受此更改的影响。 为避免服务中断，您可能需要在2026年9月30日之前更新应用程序与Marketo的集成方式。

这些类型的查询通常会创建搜索，这些搜索可能没有潜在结果，或者在找到任何结果之前超时。 限制数据集的大小可以提高这些类型查询的响应性，并确保及时完成数据集的搜索。

## 我如何判断自己是否受到影响？

此更改只会影响少量Marketo Engage实例。 应用更改之前，将会在应用程序中通知受影响订阅的管理员。

## 我需要做什么？

您应该与负责Marketo Engage集成的人员或团队共享此文档。

根据您的用例，有两个基本选项可将您的应用程序迁移到：

* 将从中提取活动的静态列表限制为最多10,000个成员。 您可以将任何现有列表拆分为更小的列表，以继续轮询相同受众的活动。
* 使用批量活动提取或数据流提取您的活动或数据值更改，并将这些结果加入具有[getLeadByListId](https://developer.adobe.com/marketo-apis/api/mapi#operation/getLeadsByListIdUsingGET_1)或[批量潜在客户提取](https://experienceleague.adobe.com/zh-hans/docs/marketo-developer/marketo/rest/bulk-extract/bulk-lead-extract)的静态列表成员资格

## 如果我什么都不做，会发生什么？

在从具有大量成员的静态列表中查询活动时，由于未经处理的错误，API集成的功能可能会遇到中断。
