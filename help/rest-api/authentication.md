---
title: 身份验证
feature: REST API
description: 对Marketo用户进行API使用身份验证。
exl-id: f89a8389-b50c-4e86-a9e4-6f6acfa98e7e
source-git-commit: d57167d60f1cc6a32c600b72829afcba81e6ec92
workflow-type: tm+mt
source-wordcount: '560'
ht-degree: 0%

---

# 身份验证

Marketo的REST API使用双腿OAuth 2.0进行身份验证。客户端ID和客户端密钥由您定义的自定义服务提供。 每个自定义服务均由仅限API的用户拥有，该用户具有一组角色和权限，可授权服务执行特定操作。 访问令牌与单个自定义服务关联。 访问令牌过期与实例中可能存在的其他自定义服务相关联的令牌无关。

## 创建访问令牌

通过选择自定义服务并单击&#x200B;**[!UICONTROL View Details]**，可在&#x200B;**[!UICONTROL Admin]** > **[!UICONTROL Integration]** > **[!UICONTROL LaunchPoint]**&#x200B;菜单中找到`Client ID`和`Client Secret`。

![获取REST服务详细信息](assets/authentication-service-view-details.png)

![启动点凭据](assets/admin-launchpoint-credentials.png)

在REST API部分的&#x200B;**[!UICONTROL Admin]** > **[!UICONTROL Integration]** > **[!UICONTROL Web Services]**&#x200B;菜单中找到`Identity URL`。

使用HTTPGET(或POST)请求创建访问令牌，如下所示：

```
GET <Identity URL>/oauth/token?grant_type=client_credentials&client_id=<Client Id>&client_secret=<Client Secret>
```

如果您的请求有效，您将收到类似于以下内容的JSON响应：

```json
{
    "access_token": "cdf01657-110d-4155-99a7-f986b2ff13a0:int",
    "token_type": "bearer",
    "expires_in": 3599,
    "scope": "apis@acmeinc.com"
}
```

响应定义

- `access_token` — 您在后续调用中传递的令牌，用于与目标实例进行身份验证。
- `token_type` - OAuth身份验证方法。
- `expires_in` — 当前令牌的剩余生命周期（以秒为单位）（在此之后将无效）。 最初创建访问令牌时，其生命周期为3600秒或1小时。
- `scope` — 用于进行身份验证的自定义服务的拥有用户。

## 使用访问令牌

调用REST API方法时，必须在每次调用中包含访问令牌才能成功调用。

访问令牌必须作为HTTP标头发送。

`Authorization: Bearer cdf01657-110d-4155-99a7-f986b2ff13a0:int`

>[!IMPORTANT]
>
>2025年6月30日，将移除对使用&#x200B;**access_token**&#x200B;查询参数的身份验证的支持。 如果您的项目使用查询参数来传递访问令牌，则应尽快更新以使用&#x200B;**Authorization**&#x200B;标头。 新开发应仅使用&#x200B;**Authorization**&#x200B;标头。

## 提示和最佳实践

管理访问令牌过期对于确保您的集成顺利工作并防止在正常操作期间发生意外身份验证错误很重要。 在为集成设计身份验证时，请确保存储身份响应中包含的令牌和过期时间。

在进行任何REST调用之前，您应该根据令牌的剩余生命周期检查令牌的有效性。 如果令牌已过期，则通过调用[标识](https://developer.adobe.com/marketo-apis/api/identity/#tag/Identity/operation/identityUsingGET)终结点来续订该令牌。 这有助于确保您的REST调用绝不会由于令牌过期而失败。 这有助于您以可预测的方式管理REST调用的延迟，这对于面向最终用户的应用程序至关重要。

如果使用过期的令牌来验证REST调用，则REST调用将失败并返回602错误代码。 如果使用无效令牌对REST调用进行身份验证，则会返回601错误代码。 如果收到这两个代码中的任意一个，则客户端应通过调用标识端点来续订令牌。

如果您在令牌过期之前调用身份端点，则响应中将返回相同的令牌及其剩余生命周期。

请记住，您的访问令牌是按自定义服务而不是按用户分配的。 即使两个身份响应可以将范围限定为同一个用户，但是如果使用来自两个不同服务的凭据创建访问令牌，则访问令牌和过期期限将相互独立。 当同一应用程序中有多组凭据时，请记住这一点；客户端ID可以用作独立管理凭据的有用密钥。
