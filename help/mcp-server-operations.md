---
title: Marketo Engage MCP操作
description: 了解哪些与AI助理一起使用的Marketo Engage MCP操作。
autotag-review: '2026-06-02T13:31:42.084Z'
TQID: 'https://experienceleague.adobe.com/qvrWbHOCsCCHctduNDxMhkE8JAKxZk8FCYfKvzxfcYA'
product_v2:
  - id: b27e5950-9033-45ac-9f86-eb22e567f615
    internal-label: Marketo Engage
feature_v2:
  - id: a7170d27-32ab-462b-a333-269abc654483
    internal-label: Smart Campaigns
  - id: b0bb9048-d951-48d8-8232-45cf248a7e27
    internal-label: Forms
  - id: dca84292-69e9-4116-a575-667d31fa060d
    internal-label: APIs
  - id: e64968b2-4ee5-47f9-8cae-0588f184b9eb
    internal-label: Programs
role_v2:
  - id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
    internal-label: Admin
topic_v2:
  - id: bbbea26f-9621-49eb-9ab8-e06fb3bbce8c
    internal-label: Artificial intelligence
source-git-commit: 1cdb28422994b772a0fa430e213facaf26817952
workflow-type: tm+mt
source-wordcount: '260'
ht-degree: 0%
---

# [!DNL Marketo Engage] MCP操作

[!DNL Marketo Engage] MCP服务器支持跨资产管理、潜在客户数据和营销活动自动化的182项操作。



## 支持的操作

您可以通过连接的AI助手发现可用的操作。

如果您的连接公开发现工具，请使用以下提示：

* “使用Marketo MCP `list_tool_categories`工具列出可用的操作类别。”
* “使用Marketo MCP `search_tools`工具查找用于管理智能营销活动的操作。”

用您需要的操作替换第二个提示中的任务。 如果发现工具未公开，请让您的AI助手列出[!DNL Marketo] MCP服务器中可用的工具。

结果反映了连接的权限和功能可用性。 它们描述可用的MCP工具，而不是完整的[!DNL Marketo] REST API。

对于使用[!DNL Marketo]客户端凭据的连接，不支持以下功能：

* 短信工具
* 推送通知工具
* 应用程序内消息和应用程序内程序工具
* 使用`schedule_smart_campaign`计划智能营销活动
* 用户管理和批量服务工具

客户端凭据连接支持`schedule_campaign`操作。

可用操作取决于您的API用户权限、[!DNL Marketo]实例已启用的功能以及MCP客户端中的任何限制。 某些更改或删除数据的操作需要确认和附加访问权限。 支持类别并不意味着可以访问每个REST端点。

有关连接说明和示例提示，请参见[MCP服务器](mcp-server.md#available-operations)页。 有关如何使用[!DNL Marketo] AI和[!DNL Marketo Engage] MCP服务器处理数据的信息，请参阅[数据信息](https://experienceleague.adobe.com/zh-hans/docs/marketo/using/product-docs/marketo-ai/data-information)页。