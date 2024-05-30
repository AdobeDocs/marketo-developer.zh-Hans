---
title: "SOAP常见问题解答"
feature: SOAP
description: "SOAP常见问题解答"
source-git-commit: d335bdd9f939c3e557a557b43fb3f33934e13fef
workflow-type: tm+mt
source-wordcount: '333'
ht-degree: 0%

---


# SOAP常见问题解答

**问：** 如何获取Marketo中所有项目的列表及其元数据？

**答：** 要检索所有程序的列表，您可以使用 [getMObjects](./getmobjects.md) 将类型传递给“Program”，并将includeDetails设置为true。

**问：** 是否有办法提高getMultipleLeads的性能？

**答：** 有几个选项可加快getMultipleLeads调用的性能。 第一是减少您为每次调用请求的batchSize。 推荐的批次大小为200。 第二个选项是使用includeAttributes过滤器指定您感兴趣的字段。 这样可加快查询速度并减少您收到的响应负载。 最后一种方法是使用LastUpdateAtSelector并指定oldestUpdatedAt和latestUpdatedAt。 您可以指定不同的日期范围，然后同时线程化多个请求。 如果使用线程方法，请确保您的SOAP/WSDL客户端支持 [持久连接](https://www.w3.org/Protocols/rfc2616/rfc2616-sec8.html).

**问：** 与SalesForce或Microsoft Dynamics等CRM未集成时，如何通过SOAP API创建机会？

**答：** 您可以使用SOAP API创建业务机会 [syncMObjects](syncmobjects.md) 调用写入OpportunityPersonRole和Opportunity [对象](marketo-objects.md) 类型。

**问：** 我能否以编程方式从Marketo发送电子邮件？ 如果是这样，如何为每个电子邮件收件人发送自定义内容？

**答：** 当然。 您可以使用以下任一方式从Marketo请求发送电子邮件 [requestCampaign](requestcampaign.md) 或以下项的组合 [importToList](importtolist.md) 和 [scheduleCampaign](schedulecampaign.md) SOAP API。 要立即向一个或多个用户发送电子邮件，您可以使用 [requestCampaign](requestcampaign.md). 如果要计划在指定的日期和时间发送电子邮件，您可以使用 [importToList](importtolist.md) 指定电子邮件的收件人，以及 [scheduleCampaign](schedulecampaign.md) 指定这些收件人何时将收到该电子邮件。

如果要自定义每个电子邮件收件人的内容，可以通过覆盖的值 [项目令牌](../rest-api/tokens.md) 在电子邮件模板中设置的属性。
