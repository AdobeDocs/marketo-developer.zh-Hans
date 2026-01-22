---
title: REST API
feature: REST API
description: 了解如何使用Marketo REST API、设置API用户和LaunchPoint、查看配额和限制、使用授权标头进行身份验证以及检索潜在客户。
exl-id: 4b9beaf0-fc04-41d7-b93a-a1ae3147ce67
source-git-commit: 5881ab969eca3a37d19f56b6570e42828994eff3
workflow-type: tm+mt
source-wordcount: '806'
ht-degree: 1%

---

# REST API

Marketo会公开一个REST API，该API允许远程执行系统的多项功能。 从创建程序到批量引导导入，有许多选项允许对Marketo实例进行细粒度控制。

这些API通常分为两大类：[潜在客户数据库](https://developer.adobe.com/marketo-apis/api/mapi/)和[资产](https://developer.adobe.com/marketo-apis/api/asset/)。 Lead Database API允许检索Marketo人员记录和相关对象类型（如商机和公司）并与之交互。 资产API允许与营销宣传材料和工作流相关记录进行交互。

>[!NOTE]
>SOAP API已被弃用，在2026年3月31日后将不再可用。 所有新的开发应使用Marketo [REST API](./rest-api.md)完成，并且现有服务应在该日期之前迁移以避免服务中断。 如果您的服务使用SOAP API，请参阅SOAP API [迁移指南](../soap-api/migration.md)以了解有关如何迁移的信息。
>

>[!IMPORTANT]
>请参阅此[Nation帖子](https://nation.marketo.com/t5/product-blogs/rest-api-double-slash-deprecation/ba-p/358616)，了解如何在API网关URL中弃用双斜杠。
>

- **每日配额：**&#x200B;订阅每天分配了50,000个API调用（该调用在12:00AM CST每日重置）。 您可以通过客户经理增加每日配额。
- **速率限制：**&#x200B;每个实例的API访问限制为每20秒100次调用。
- **并发限制：**  最多十次并发API调用。

标准调用的大小限制为8KB的URI长度，正文大小为1MB，尽管我们的批量API的正文可以为10MB。 如果您的调用中有错误，则API通常仍会返回状态代码200，但JSON响应将包含一个值为`false`的“success”成员，以及“errors”成员中的错误数组。 [此处](error-codes.md)提供有关错误的更多信息。

## 快速入门

以下步骤需要在Marketo实例中具有管理员权限。

对于您首次调用Marketo，您将检索一条潜在客户记录。 要开始使用Marketo，您必须获取API凭据才能对实例进行经过身份验证的调用。 登录到您的实例并转到&#x200B;**[!UICONTROL Admin]** -> **[!UICONTROL Users and Roles]**。

![管理员用户和角色](assets/admin-users-and-roles.png)

单击&#x200B;**[!UICONTROL Roles]**&#x200B;选项卡，然后单击“新建角色”，并至少为Access API组中的角色分配“只读潜在客户”（或“只读人员”）权限。 请确保为其提供一个描述性名称，然后单击&#x200B;**[!UICONTROL Create]**。

![新角色](assets/new-role.png)

现在，返回[!UICONTROL Users]选项卡并单击&#x200B;**[!UICONTROL Invite New User]**。 为您的用户提供一个描述性名称（指示它是API用户）和一个电子邮件地址，然后单击&#x200B;**[!UICONTROL Next]**。

![新用户信息](assets/new-user-info.png)

然后，选中[!UICONTROL API Only]选项并授予用户您创建的API角色，然后单击&#x200B;**[!UICONTROL Next]**。

![新用户权限](assets/new-user-permissions.png)

要完成用户创建过程，请单击&#x200B;**[!UICONTROL Send]**。

![新用户消息](assets/new-user-message.png)

接下来，转到[!UICONTROL Admin]菜单并单击&#x200B;**[!UICONTROL LaunchPoint]**。

![启动点](assets/admin-launchpoint.png)

单击&#x200B;**[!UICONTROL New]**&#x200B;菜单并选择&#x200B;**[!UICONTROL New Service]**。 为您的服务提供一个描述性名称，然后从&#x200B;**[!UICONTROL Custom]**&#x200B;下拉菜单中选择[!UICONTROL Service]。 提供描述，然后从[!UICONTROL API Only User]下拉菜单中选择您的新用户并单击&#x200B;**[!UICONTROL Create]**。

![新Launchpoint服务](assets/admin-launchpoint-new-service.png)

单击新服务的&#x200B;**[!UICONTROL View Details]**&#x200B;以访问客户端ID和客户端密钥。 现在，您可以单击&#x200B;**[!UICONTROL Get Token]**&#x200B;按钮以生成一个有效期为一小时的访问令牌。 暂时将令牌保存在注释中。

![获取令牌](assets/get-token.png)

接下来，转到&#x200B;**[!UICONTROL Admin]**&#x200B;菜单，然后转到&#x200B;**[!UICONTROL Web Services]**。

![Web服务](assets/admin-web-services.png)

暂时在REST API框中找到[!UICONTROL Endpoint]并将其保存在注释中。

![REST终结点](assets/admin-web-services-rest-endpoint-1.png)

调用REST API方法时，必须在每次调用中包含访问令牌才能成功调用。 访问令牌必须作为HTTP标头发送。

```
Authorization: Bearer cdf01657-110d-4155-99a7-f986b2ff13a0:int
```

>[!IMPORTANT]
>
>2025年6月30日，将移除对使用&#x200B;**access_token**&#x200B;查询参数的身份验证的支持。 如果您的项目使用查询参数来传递访问令牌，则应尽快更新以使用&#x200B;**Authorization**&#x200B;标头。 新开发应仅使用&#x200B;**Authorization**&#x200B;标头。

打开新的浏览器选项卡并输入以下内容，使用适当的信息调用[按筛选器类型获取潜在客户](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Leads/operation/getLeadsByFilterUsingGET)

```
<Your Endpoint URL>/rest/v1/leads.json?&filterType=email&filterValues=<Your Email Address>
```

如果您的数据库中没有包含电子邮件地址的潜在客户记录，请将其替换为您知道存在的潜在客户记录。 在URL栏中按Enter键，您应会获得与以下内容类似的JSON响应：

```json
{
    "requestId":"c493#1511ca2b184",
    "result":[
       {
           "id":1,
           "updatedAt":"2015-08-24T20:17:23Z",
           "lastName":"Elkington",
           "email":"developerfeedback@marketo.com",
           "createdAt":"2013-02-19T23:17:04Z",
           "firstName":"Kenneth"
        }
    ],
    "success":true
}
```

## API 使用情况

每个API用户在API使用情况报表中单独报告，因此按用户拆分Web服务允许您轻松计算每个集成的使用情况。 如果对实例的API调用数量超过限制并导致后续调用失败，则使用此实践可让您考虑来自每个服务的数量，并让您评估如何解决此问题。 通过转到&#x200B;**[!UICONTROL Admin]** -> **[!UICONTROL Integration]** > **[!UICONTROL Web Services]**&#x200B;并单击过去七天的呼叫数查看您的使用情况。
