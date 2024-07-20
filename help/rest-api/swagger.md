---
title: 下载Swagger定义
feature: REST API, Programs
description: 下载供本地使用的Swagger定义文件。
source-git-commit: 85062243d57a3fc6d15251163e926495858edf2a
workflow-type: tm+mt
source-wordcount: '170'
ht-degree: 0%

---

# 下载Swagger定义

[Swagger](https://swagger.io/)或[OpenAPI](https://www.openapis.org/)定义是描述REST API的所有方法和参数的数据文件。 您可以在本地下载并使用这些数据文件作为个人API参考。

要使用Marketo EngageSwagger/OpenAPI定义，必须更新每个文档的`host`字段，以匹配Marketo Engage实例中的REST API主机名。

首先，下载您要使用的OpenAPI定义：

* [资产](assets/swagger-asset.json)
* [商机](assets/swagger-mapi.json)
* [身份       ](assets/swagger-identity.json)
* [User Management](assets/swagger-user.json)

接下来，从Marketo管理员获取REST API主机名。 转到Marketo Engage中的&#x200B;_管理员_-> _Web服务_&#x200B;菜单，并从“终结点”字段复制主机名。 `hostname`是协议`https://`和`/rest`之间的字符串，它类似于`AAA-999-AAA.mktorest.com`

在文本编辑器中打开OpenAPI文件，在JSON中找到`host`字段，并将其值更改为REST API主机名： `"host":"localhost:8080"`至`"host":"AAA-999-AAA.mktorest.com"`。

保存后，您可以在Swagger UI实例或任何其他OpenAPI软件中运行定义文件。
