---
title: 自定义服务
feature: REST API
description: 创建Marketo自定义服务，设置仅限API的角色和权限，在LaunchPoint中获取客户端ID和客户端密钥，以及获取访问令牌。
exl-id: 38b05c4c-4404-4c30-a7cb-d31b28a3a72e
TQID: https://experienceleague.adobe.com/lvT-8bYucf-K5LYxb5jQ7BHc137W71SvsGg7cWJlxEs
product_v2:
  - id: b27e5950-9033-45ac-9f86-eb22e567f615
feature_v2:
  - id: b13bd2ad-8e65-49e5-9691-2a0d31067b35
role_v2:
  - id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
topic_v2:
  - id: a004cc84-67b9-4a33-a3a7-8ec7273ef4dc
source-git-commit: 3e6d310c5aec1a3435424fb122b71d825db5af0e
workflow-type: tm+mt
source-wordcount: 872
ht-degree: 0%

---

# 自定义服务

自定义服务提供用于向Marketo进行身份验证和从Marketo [身份服务](https://developer.adobe.com/marketo-apis/api/identity/#tag/Identity/operation/identityUsingGET)获取访问令牌的凭据。 每个自定义服务的作用范围仅限定于一个API专用用户，并从该用户获取其权限。

## 角色

在创建自定义服务之前，请创建一个角色以分配给相关的仅限API用户。 转到 **[!UICONTROL Admin]** > **[!UICONTROL Users & Roles]** > **[!UICONTROL Roles]**。

角色包含允许或限制访问特定功能的各个权限。 在启用了工作区和分区的订阅中，为每个工作区分配权限。 用户只能在具有这些权限的工作区中执行允许的操作。

要创建角色，请选择&#x200B;**[!UICONTROL New Role]**。

![用户和角色](assets/admin-users-and-roles-roles.png)

为角色提供一个描述性名称。 仅API用户具有一组与标准用户权限不同的特定权限。 API权限显示在“访问API”树下自己的层次结构中。

![新角色权限](assets/new-role-access-api-permissions.png)

### 角色权限

仅“访问API”组中的权限适用于API用户。 分配所有管理员权限不会将API权限授予用户。

构建角色时，请确定应用程序必须执行的操作。 仅分配这些操作所需的最低权限。 不必要的权限可能会允许集成在您的订阅中执行不需要的操作。

使用[权限工具](endpoint-reference.md)确定最小权限集。 查看[权限](#permission_list)的完整列表。

## 用户

创建角色后，创建“仅限API”用户。 其他用户管理仅API用户，而仅API用户无法登录到Marketo。 他们可以：

- 创建自定义服务
- 确定这些服务的权限范围
- 访问REST API

>[!MORELIKETHIS]
>
>要创建仅限API的用户，请转到&#x200B;**[!UICONTROL Admin]** > **[!UICONTROL Users & Roles]** > **[!UICONTROL Users]**&#x200B;菜单并选择&#x200B;**[!UICONTROL Invite New User]**。

![新用户信息](assets/new-user-info.png)

根据将使用该帐户的服务和应用程序，为用户提供一个描述性名称和电子邮件地址。 电子邮件地址不一定有效。 填写必填字段，选中&#x200B;**[!UICONTROL API Only]**&#x200B;复选框，然后将您的某个API角色分配给用户。 此操作将角色的权限集分配给用户。

![新用户权限](assets/new-user-permissions.png)

选择&#x200B;**[!UICONTROL Send]**&#x200B;以创建仅限API的用户。

为新应用程序设置凭据时，请考虑为服务创建单独的用户，即使其他集成使用相同的权限集。 按用户跟踪API调用使用情况统计信息和错误。

每个应用程序的用户有助于将使用和问题隔离到特定应用程序。 当集成达到每日API调用限制或生成API错误时，此分离非常有用。

## 自定义服务

自定义服务提供使用Marketo实例进行身份验证所需的客户端ID和客户端密钥。 要预配服务，请转到&#x200B;**[!UICONTROL Admin]** > **[!UICONTROL Integrations]** > **[!UICONTROL LaunchPoint]**，然后选择&#x200B;**[!UICONTROL New Service]**。

为服务提供一个描述性名称。 从“服务”列表中，选择“自定义”。 输入详细说明，从“仅API用户”列表中选择适当的用户，然后选择&#x200B;**[!UICONTROL Create]**。

![新自定义服务](assets/admin-launchpoint-new-service.png)

该服务将以“查看详细信息”选项显示在LaunchPoint Services列表中。 选择“查看详细信息”以访问客户端ID、客户端密钥、所属用户和获取令牌选项。

使用获取令牌进行短期测试。 令牌的生命周期与从[Identity服务](https://developer.adobe.com/marketo-apis/api/identity/#tag/Identity/operation/identityUsingGET)获取的令牌的生命周期相同，在创建后有效期为3,600秒。

![获取令牌](assets/get-token.png)

## 工作区和分区

在具有工作区和分区的订阅中，用户在工作区中的角色权限决定了对记录和资产的访问权限。 每个工作区都有权访问一个或多个分区，每个潜在客户都属于一个分区。

如果仅限API的用户可以读取或写入工作区中的潜在客户记录，则用户可以访问该工作区可用的分区中的所有记录。

Assets属于工作区。 当用户具有在资源工作区中具有所需权限的角色时，用户可以读取或写入资源。

## 权限列表

下表列出了仅限API用户可用的权限以及每个权限授予的访问权限。

| 角色权限 | 授予对……的访问权限 |
| --- | --- |
| 批准Assets | 批准资源 |
| 执行营销活动 | 请求或计划营销活动 |
| 只读活动 | 检索潜在客户活动 |
| 只读活动元数据 | 检索潜在客户活动元数据 |
| 只读Assets | 检索资源详细信息 |
| 只读营销活动 | 检索营销活动详细信息 |
| 只读公司 | 检索公司详细信息 |
| 只读自定义对象 | 检索自定义对象详细信息 |
| 只读潜在客户 | 检索潜在客户详细信息 |
| 只读指定帐户 | 检索指定帐户详细信息 |
| 只读指定帐户列表 | 检索指定帐户列表详细信息 |
| 只读机会 | 检索机会详细信息 |
| 只读销售人员 | 检索销售人员详细信息 |
| 读写活动 | 检索和创建潜在客户活动 |
| 读写活动元数据 | 检索和创建潜在客户活动元数据 |
| 读写Assets | 检索、创建和更新资源 |
| 读写营销活动 | 检索、创建和更新营销活动 |
| 读写公司 | 检索、创建和更新公司 |
| 读写自定义对象 | 检索、创建和更新自定义对象 |
| 读写潜在客户 | 检索、创建和更新潜在客户详细信息 |
| 读写指定帐户 | 检索、创建和更新指定帐户 |
| 读写指定帐户列表 | 检索、创建和更新指定帐户列表 |
| 读写机会 | 检索、创建和更新机会 |
| 读写销售人员 | 检索、创建和更新销售人员 |
