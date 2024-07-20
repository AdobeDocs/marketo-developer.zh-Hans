---
title: 终结点引用
feature: REST API
description: Marketo API端点引用
exl-id: 27d16b6f-865a-4e40-ab9c-cbabe2927472
source-git-commit: 66add4c38d0230c36d57009de985649bb67fde3e
workflow-type: tm+mt
source-wordcount: '216'
ht-degree: 0%

---

# 终结点引用

- [资源](https://developer.adobe.com/marketo-apis/api/asset/)
- [标识](https://developer.adobe.com/marketo-apis/api/identity/)
- [潜在客户数据库](https://developer.adobe.com/marketo-apis/api/mapi/)
- [用户管理](https://developer.adobe.com/marketo-apis/api/user/)

## 终结点引用

Marketo使用Swagger为其REST API提供公共界面的正式定义。 Swagger为URL结构、请求模型和响应模型提供了丰富的定义模型，并开发了用于API交互、测试和客户端生成的工具生态系统。

终结点引用使用[Swagger-UI](https://swagger.io/tools/swagger-ui/) JavaScript包在客户端生成引用页面。 列出了每个公共端点，并提供了响应模型的结构、所需的请求参数以及所需的请求模型。

## 使用Marketo的Swagger定义

Swagger标准要求提供主机，或者由处理文件的主机生成主机。 在使用任何现有工具之前，请务必在定义中更正主机，因为Marketo会为定义提供一个空的主机参数。 每个Marketo实例的REST API主机是唯一的，并遵循以下模式：

`{Munchkin ID}.mktorest.com`

## 资源API

Marketo Asset API在需要POST方法的端点请求中使用`application/x-www-url-formencoded`样式参数。 但是，在某些情况下（如文件夹参数），参数的值可以是JSON数组或对象。 这些参数在端点引用中有说明。
