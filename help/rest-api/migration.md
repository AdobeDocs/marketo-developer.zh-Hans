---
title: 获取潜在客户API更新
feature: REST API
description: 了解“获取潜在客户活动”和“获取潜在客户更改端点”的限制更改。
source-git-commit: 3e6d310c5aec1a3435424fb122b71d825db5af0e
workflow-type: tm+mt
source-wordcount: '311'
ht-degree: 0%

---

# 获取潜在客户API更新

从2026年9月30日开始，如果目标列表包含10,000个或更多潜在客户，对[获取潜在客户活动](https://developer.adobe.com/marketo-apis/api/mapi#operation/getLeadActivitiesUsingGET)或[获取潜在客户更改](https://developer.adobe.com/marketo-apis/api/mapi#operation/getLeadChangesUsingGET)端点（包含`listId`参数）的调用将失败。 端点将返回1003错误代码，指示目标静态列表具有过多记录。

此更改会影响一个或多个最近的API调用。 为避免服务中断，您可能需要在2026年9月30日之前更新应用程序与Marketo的集成方式。

这些查询通常会创建没有潜在结果的搜索，或在查找结果之前超时。 限制设置大小可提高查询响应性，并帮助搜索及时完成。

## 我如何判断自己是否受到影响？

此更改仅影响少量Marketo Engage实例。 受影响订阅的管理员在应用更改之前将收到应用程序内通知。

## 我需要做什么？

与负责您的Marketo Engage集成的人员或团队共享此文档。

根据您的用例，使用以下迁移选项之一：

* 将用于活动提取的静态列表限制为10,000个成员。 将现有列表拆分为较小的列表，以继续轮询活动的相同受众。
* 使用批量活动提取或数据流提取活动或数据值更改。 将结果加入具有[getLeadByListId](https://developer.adobe.com/marketo-apis/api/mapi#operation/getLeadsByListIdUsingGET_1)或[批量潜在客户提取](https://experienceleague.adobe.com/en/docs/marketo-developer/marketo/rest/bulk-extract/bulk-lead-extract)的静态列表成员资格。

## 如果我什么都不做，会发生什么？

从具有大量成员的静态列表中查询活动时，您的API集成可能会因未处理的错误而中断。
