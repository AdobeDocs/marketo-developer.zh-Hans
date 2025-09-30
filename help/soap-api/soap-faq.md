---
title: SOAP常见问题解答
feature: SOAP
description: 了解如何通过Marketo SOAP API列出具有getMObjects的项目、优化getMultipleLeads、创建机会以及发送或计划个性化电子邮件。
exl-id: a2d8f144-cd5f-41bc-8231-29c42af935b8
source-git-commit: 7557b9957c87f63c2646be13842ea450035792be
workflow-type: tm+mt
source-wordcount: '353'
ht-degree: 0%

---

# SOAP常见问题解答

**问：**&#x200B;如何获取Marketo中所有项目的列表及其元数据？

**A：**&#x200B;要检索所有程序的列表，可以使用[getMObjects](./getmobjects.md)，将类型传递给“Program”并将includeDetails设置为true。

**问：**&#x200B;是否有办法提高getMultipleLeads的性能？

**A：**&#x200B;有几个选项可加快getMultipleLeads调用的性能。 第一是减少您为每次调用请求的batchSize。 推荐的批次大小为200。 第二个选项是使用includeAttributes过滤器指定您感兴趣的字段。 这样可加快查询速度并减少您收到的响应负载。 最后一种方法是使用LastUpdateAtSelector并指定oldestUpdatedAt和latestUpdatedAt。 您可以指定不同的日期范围，然后同时线程化多个请求。 如果使用线程方法，请确保您的SOAP/WSDL客户端支持[永久连接](https://www.w3.org/Protocols/rfc2616/rfc2616-sec8.html)。

**问：**&#x200B;未与Salesforce或Microsoft Dynamics等CRM集成时，如何通过SOAP API创建机会？

**A：**&#x200B;您可以使用SOAP API创建机会，方法是使用写入OpportunityPersonRole和机会[MObject](syncmobjects.md)类型的[syncMObjects](marketo-objects.md)调用。

**问：**&#x200B;我能否从Marketo以编程方式发送电子邮件？ 如果是这样，如何为每个电子邮件收件人发送自定义内容？

**A：**，绝对是。 您可以使用[requestCampaign](requestcampaign.md)或[importToList](importtolist.md)和[scheduleCampaign](schedulecampaign.md) SOAP API的组合从Marketo请求发送电子邮件。 若要立即向一个或多个用户发送电子邮件，请使用[requestCampaign](requestcampaign.md)。 如果要计划在指定的日期和时间发送电子邮件，则可以使用[importToList](importtolist.md)指定电子邮件的收件人，并使用[scheduleCampaign](schedulecampaign.md)指定发送该电子邮件的时间。

如果要自定义每个电子邮件收件人的内容，可以通过覆盖在电子邮件模板中设置的[程序令牌](../rest-api/tokens.md)的值来实现此目的。
