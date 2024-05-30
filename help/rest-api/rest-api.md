---
title: "REST API"
feature: REST API
description: “REST API概述”
source-git-commit: 2185972a272b64908d6aac8818641af07c807ac2
workflow-type: tm+mt
source-wordcount: '643'
ht-degree: 0%

---


# REST API

Marketo会公开一个REST API，该API允许远程执行系统的多项功能。 从创建程序到批量引导导入，有许多选项允许对Marketo实例进行细粒度控制。

这些API通常分为两大类： [潜在客户数据库](https://developer.adobe.com/marketo-apis/api/mapi/)、和 [资产](https://developer.adobe.com/marketo-apis/api/asset/). Lead Database API允许检索Marketo人员记录以及相关的对象类型（如Opportunities和Companies ）并与之交互。 资产API允许与营销宣传材料和工作流相关记录进行交互。

- **每日配额：** 订阅每天会分配50,000次API调用（CST每天凌晨12:00重置）。 您可以通过客户经理增加每日配额。
- **速率限制：** 每个实例的API访问限制为每20秒100次调用。
- **并发限制：**  最多十次并发API调用。

标准调用的大小限制为8KB的URI长度，正文大小为1MB，尽管我们的批量API的正文可以为10MB。 如果您的调用中有错误，则API通常仍会返回状态代码200，但JSON响应将包含一个值为的“success”成员 `false`和“errors”成员中的错误数组。 有关错误的更多信息 [此处](error-codes.md).

## 快速入门

以下步骤需要在Marketo实例中具有管理员权限。

对于您首次致电Marketo，您将检索一条潜在客户记录。 要开始使用Marketo，您必须获取API凭据才能对实例进行经过身份验证的调用。 登录您的实例，然后转到 **[!UICONTROL Admin]** -> **[!UICONTROL Users and Roles]**.

![管理员用户和角色](assets/admin-users-and-roles.png)

单击 [!UICONTROL Roles] 选项卡，然后新建角色，并至少将“只读潜在客户”（或“只读人员”）权限分配给访问API组中的角色。 请确保为其提供描述性名称，然后单击 [!UICONTROL Create].

![新建角色](assets/new-role.png)

现在返回到“用户”选项卡，然后单击“邀请新用户”。 为用户提供说明性名称和Email Address ，并点击 **[!UICONTROL Next]**.

![新建用户信息](assets/new-user-info.png)

然后，选中仅限API选项，并向用户授予您创建的API角色，然后单击 **[!UICONTROL Next]**.

![新用户权限](assets/new-user-permissions.png)

要完成用户创建过程，请单击 **[!UICONTROL Send]**.

![新建用户消息](assets/new-user-message.png)

接下来，转到管理员菜单并单击 **[!UICONTROL LaunchPoint]**.

![启动点](assets/admin-launchpoint.png)

单击“新建”菜单并选择 [!UICONTROL New Service]. 为您的服务提供一个描述性名称，然后从服务下拉菜单中选择“自定义”。 提供说明，然后从仅API用户下拉菜单中选择新用户，然后单击 [!UICONTROL Create].

![新建Launchpoint服务](assets/admin-launchpoint-new-service.png)

单击新服务的查看详细信息，以访问客户端ID和客户端密钥。 现在，您可以单击 [!UICONTROL Get Token] 按钮以生成有效期为一小时的访问令牌。 暂时将令牌保存在注释中。

![获取令牌](assets/get-token.png)

接下来，转到管理员菜单，然后转到 **[!UICONTROL Web Services]**.

![Web服务](assets/admin-web-services.png)

暂时在REST API框中查找端点并保存在注释中。

![REST端点](assets/admin-web-services-rest-endpoint-1.png)

打开新的浏览器选项卡，并使用要调用的相应信息输入以下内容 [按筛选器类型获取潜在客户](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Leads/operation/getLeadsByFilterUsingGET)：

```
<Your Endpoint URL>/rest/v1/leads.json?access_token=<Your Access Token>&filterType=email&filterValues=<Your Email Address>
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

每个API用户在API使用情况报表中单独报告，因此按用户拆分Web服务允许您轻松计算每个集成的使用情况。 如果对实例的API调用数量超过限制并导致后续调用失败，则使用此实践可让您考虑来自每个服务的数量，并让您评估如何解决此问题。 通过以下方式查看您的使用情况： **[!UICONTROL Admin]** -> **[!UICONTROL Integration]** > **[!UICONTROL Web Services]** 单击过去七天内的呼叫数。
