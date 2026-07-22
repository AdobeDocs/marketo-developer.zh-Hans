---
title: 身份验证
feature: REST API
description: 使用2条腿的OAuth 2.0验证Marketo REST API，创建和使用访问令牌，切换到授权标头，管理过期，处理601和602错误。
exl-id: f89a8389-b50c-4e86-a9e4-6f6acfa98e7e
TQID: https://experienceleague.adobe.com/cIeI0m61CyIWq4HEosZ-QAsxzZb0WcrQRpCud2qysfY
product_v2:
  - id: b27e5950-9033-45ac-9f86-eb22e567f615
role_v2:
  - id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
source-git-commit: 3e6d310c5aec1a3435424fb122b71d825db5af0e
workflow-type: tm+mt
source-wordcount: 519
ht-degree: 0%

---

# 身份验证

Marketo REST API使用双腿OAuth 2.0进行身份验证。 自定义服务提供用于获取访问令牌的客户端ID和客户端密钥。

每个自定义服务都属于一个仅限API的用户。 用户的角色和权限可授权服务执行特定操作。 访问令牌属于单个自定义服务，其到期与实例中其他自定义服务的令牌无关。

## 创建访问令牌

要查找`Client ID`和`Client Secret`，请转到&#x200B;**[!UICONTROL Admin]** > **[!UICONTROL Integration]** > **[!UICONTROL LaunchPoint]**。 选择自定义服务，然后选择&#x200B;**[!UICONTROL View Details]**。

![获取REST服务详细信息](assets/authentication-service-view-details.png)

![启动点凭据](assets/admin-launchpoint-credentials.png)

要查找`Identity URL`，请转到&#x200B;**[!UICONTROL Admin]** > **[!UICONTROL Integration]** > **[!UICONTROL Web Services]**。 该URL将显示在REST API部分中。

使用HTTP GET或POST请求创建访问令牌：

```http
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

响应包含以下字段：

- `access_token`：您在后续调用中传递的令牌，用于与目标实例进行身份验证。
- `token_type`： OAuth身份验证方法。
- `expires_in`：当前令牌的剩余生命周期（以秒为单位）。 新的访问令牌的有效期为3,600秒或1小时。
- `scope`：拥有用于身份验证的自定义服务的用户。

## 使用访问令牌

每个REST API调用都必须在HTTP标头中包含访问令牌。

>[!IMPORTANT]
>
>2026年8月31日，将移除对使用`access_token`查询参数的身份验证的支持。 如果您的项目使用查询参数来传递访问令牌，则应尽快更新以使用[授权标头](https://experienceleague.adobe.com/zh-hans/docs/marketo-developer/marketo/rest/authentication#using-an-access-token)。 新开发应仅使用`Authorization`标头。

### 切换到授权标头

要将`access_token`查询参数替换为授权标头，请更新请求发送令牌的方式。

以下cURL示例将`access_token`值作为带有`-F`标志的表单参数发送：

```bash
curl ...  -F access_token=<Access Token> <REST API Endpoint Base URL>/bulk/v1/apiCall.json
```

以下示例在带有`-H`标志的`Authorization: Bearer` HTTP标头中发送相同的值：

```bash
curl ... -H 'Authorization: Bearer <Access Token>' <REST API Endpoint Base URL>/bulk/v1/apiCall.json
```

## 提示和最佳实践

存储来自身份响应的访问令牌和过期期限。 管理令牌过期有助于防止在正常操作期间出现意外的身份验证错误。

在进行REST调用之前，请检查令牌的剩余生命周期。 如果令牌已过期，请通过调用[Identity](https://developer.adobe.com/marketo-apis/api/identity/#tag/Identity/operation/identityUsingGET)终结点来续订该令牌。 主动续订可防止令牌过期导致的故障，并提高REST调用延迟的可预测性，这对于面向最终用户的应用程序非常重要。

身份验证错误返回以下代码：

- `602`：访问令牌已过期。
- `601`：访问令牌无效。

如果客户端收到任一代码，请通过调用身份端点来续订令牌。

如果在令牌过期之前调用身份端点，则响应将返回相同的令牌及其剩余生命周期。

访问令牌属于自定义服务，而不属于用户。 如果来自两个不同服务的凭据生成范围限定为同一用户的身份响应，则其访问令牌和过期期限保持独立。

当应用程序使用多个凭据集时，请使用客户端ID作为密钥来独立管理每个令牌。
