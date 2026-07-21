---
title: REST API
feature: REST API
description: 了解如何使用Marketo REST API、设置API用户和LaunchPoint、查看配额和限制、使用授权标头进行身份验证以及检索潜在客户。
exl-id: 4b9beaf0-fc04-41d7-b93a-a1ae3147ce67
TQID: https://experienceleague.adobe.com/GqhWI816wWX-2zf89wWj-GXpg9i615HRFVl2ljdYVj0
product_v2:
  - id: b27e5950-9033-45ac-9f86-eb22e567f615
feature_v2:
  - id: b13bd2ad-8e65-49e5-9691-2a0d31067b35
  - id: c5f60233-d5ea-4453-a799-0ad258b4d399
  - id: e64968b2-4ee5-47f9-8cae-0588f184b9eb
role_v2:
  - id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
source-git-commit: 3e6d310c5aec1a3435424fb122b71d825db5af0e
workflow-type: tm+mt
source-wordcount: 765
ht-degree: 2%

---

# REST API

Marketo REST API提供对许多系统功能的远程访问。 您可以用它来创建项目、批量导入潜在客户以及在详细级别控制Marketo实例。

REST API分为两大类：

- [潜在客户数据库](https://developer.adobe.com/marketo-apis/api/mapi) API检索并与Marketo人员记录和关联的对象类型（如商机和公司）进行交互。
- [资产](https://developer.adobe.com/marketo-apis/api/asset) API与营销宣传材料和工作流相关记录进行交互。

>[!NOTE]
>
>SOAP API已被弃用，在2026年7月31日后将不再可用。 所有新的开发应使用Marketo [REST API](./rest-api.md)完成，并且现有服务应在该日期之前迁移以避免服务中断。 如果您的服务使用SOAP API，请参阅SOAP API [迁移指南](../soap-api/migration.md)以了解有关如何迁移的信息。
>

>[!IMPORTANT]
>
>请参阅此[Nation帖子](https://nation.marketo.com/t5/product-blogs/rest-api-double-slash-deprecation/ba-p/358616)，了解如何在API网关URL中弃用双斜杠。
>

- **每日配额：**&#x200B;每个订阅每天分配了50,000个API调用。 配额每天凌晨12:00 CST重置。 请与您的客户经理联系以增加每日配额。
- **速率限制：**&#x200B;每个实例限制为每20秒100次API调用。
- **并发限制：**&#x200B;每个实例最多允许10个并发API调用。

标准API调用的最大URI长度为8 KB，最大正文大小为1 MB。 批量API调用支持最大正文大小10 MB。

当调用包含错误时，API通常仍返回HTTP状态代码200。 JSON响应包含值为`false`的`success`成员以及`errors`成员中的错误数组。 [此处](error-codes.md)提供了有关错误的详细信息。

## 快速入门

您需要具有Marketo实例管理员权限才能完成以下步骤。 此工作流会创建API凭据，并使用它们检索潜在客户记录。

首先，创建API用户并获取经过身份验证的调用的凭据。 登录您的实例，然后转到&#x200B;**[!UICONTROL Admin]** > **[!UICONTROL Users and Roles]**。

![管理员用户和角色](assets/admin-users-and-roles.png)

选择&#x200B;**[!UICONTROL Roles]**&#x200B;选项卡，然后选择新建角色。 至少从访问API组为角色分配“只读潜在客户”（或“只读人员”）权限。 为角色提供一个描述性名称并选择&#x200B;**[!UICONTROL Create]**。

![新角色](assets/new-role.png)

返回[!UICONTROL Users]选项卡并选择&#x200B;**[!UICONTROL Invite New User]**。 输入将用户标识为API用户的描述性名称，输入电子邮件地址，然后选择&#x200B;**[!UICONTROL Next]**。

![新用户信息](assets/new-user-info.png)

选择[!UICONTROL API Only]选项，分配您创建的API角色，然后选择&#x200B;**[!UICONTROL Next]**。

![新用户权限](assets/new-user-permissions.png)

选择&#x200B;**[!UICONTROL Send]**&#x200B;以创建用户。

![新用户消息](assets/new-user-message.png)

接下来，转到[!UICONTROL Admin]菜单并选择&#x200B;**[!UICONTROL LaunchPoint]**。

![启动点](assets/admin-launchpoint.png)

选择&#x200B;**[!UICONTROL New]** > **[!UICONTROL New Service]**。 输入描述性名称和描述，然后从[!UICONTROL Service]菜单中选择&#x200B;**[!UICONTROL Custom]**。 从[!UICONTROL API Only User]菜单中选择您的新用户，然后选择&#x200B;**[!UICONTROL Create]**。

![新Launchpoint服务](assets/admin-launchpoint-new-service.png)

为新服务选择&#x200B;**[!UICONTROL View Details]**&#x200B;以访问客户端ID和客户端密钥。 选择&#x200B;**[!UICONTROL Get Token]**&#x200B;以生成一个有效一小时的访问令牌。 保存第一个API调用的令牌。

![获取令牌](assets/get-token.png)

转到&#x200B;**[!UICONTROL Admin]** > **[!UICONTROL Web Services]**。

![Web服务](assets/admin-web-services.png)

在REST API框中找到[!UICONTROL Endpoint]并将其保存以用于首次API调用。

![REST终结点](assets/admin-web-services-rest-endpoint-1.png)

每个REST API调用都必须在HTTP标头中包含访问令牌。

```text
Authorization: Bearer cdf01657-110d-4155-99a7-f986b2ff13a0:int
```

>[!IMPORTANT]
>
>2025年6月30日，将移除对使用&#x200B;**access_token**&#x200B;查询参数的身份验证的支持。 如果您的项目使用查询参数来传递访问令牌，则应尽快更新以使用&#x200B;**Authorization**&#x200B;标头。 新开发应仅使用&#x200B;**Authorization**&#x200B;标头。

打开新的浏览器选项卡并输入以下URL。 将占位符替换为实例的终结点和电子邮件地址，以按筛选器类型调用[获取潜在客户](https://developer.adobe.com/marketo-apis/api/mapi#tag/Leads/operation/getLeadsByFilterUsingGET)。

```text
<Your Endpoint URL>/rest/v1/leads.json?&filterType=email&filterValues=<Your Email Address>
```

如果数据库不包含带有电子邮件地址的潜在客户记录，请使用现有潜在客户的电子邮件地址。 提交URL以接收类似于以下示例的JSON响应：

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

API使用情况报表单独跟踪每个API用户。 为每个Web服务分配单独的用户可帮助您识别每个集成的API使用情况。

如果调用次数超过实例限制，且后续调用失败，请使用该报表确定每个服务的调用数量。 转到&#x200B;**[!UICONTROL Admin]** > **[!UICONTROL Integration]** > **[!UICONTROL Web Services]**，然后选择过去七天中进行的呼叫数。

对于返回每日和最近7天使用情况和错误统计信息的REST端点，请参阅[使用情况](usage.md)。
