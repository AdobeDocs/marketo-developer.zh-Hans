---
title: Marketo Engage MCP 服务器
description: 了解如何使用Marketo Engage MCP服务器将AI助手连接到Marketo。 使用Marketo凭据配置Claude Desktop、Cursor、Claude Code或VS代码。
badgeBeta: label="有限发布版" type="informative" tooltip="此功能当前为有限测试版"
exl-id: ab446e56-6250-4af5-b03e-162991d09a5c
autotag-review: '2026-06-02T13:31:15.329Z'
TQID: 'https://experienceleague.adobe.com/PJJm7yv8HmbwMB2fsnfDCXs8zprDJK5Q5z2uiiCJRZI'
product_v2:
  - id: b27e5950-9033-45ac-9f86-eb22e567f615
feature_v2:
  - id: a7170d27-32ab-462b-a333-269abc654483
  - id: b0bb9048-d951-48d8-8232-45cf248a7e27
  - id: b13bd2ad-8e65-49e5-9691-2a0d31067b35
  - id: b3b8a63f-51fc-40f6-a7d2-a31c5d49fb45
  - id: c2dbad80-0f5c-4d96-a798-2a65f93b8721
  - id: dca84292-69e9-4116-a575-667d31fa060d
  - id: e2290edd-b061-4880-9d79-dee306cf5aa9
  - id: e64968b2-4ee5-47f9-8cae-0588f184b9eb
  - id: f82558ea-6af5-44eb-a424-5b3389abb0a3
role_v2:
  - id: ff6a42d2-313e-452e-93a6-792e4fad9ff8
topic_v2:
  - id: bbbea26f-9621-49eb-9ab8-e06fb3bbce8c
source-git-commit: 1a8728ec05e15bef1271274248ce9fc25b14c768
workflow-type: tm+mt
source-wordcount: 1923
ht-degree: 1%

---

# [!DNL Marketo Engage] MCP服务器

>[!AVAILABILITY]
>
> 此功能仅在有限范围内可用。 若要请求访问权限，请填写[此表单](https://forms.cloud.microsoft/Pages/ResponsePage.aspx?id=Wht7-jR7h0OUrtLBeN7O4Y-uSf63sAxCmWyqMJg8eMFUMVZSVExSNDA3T0I4SEcwRDFSVTBGWU01Uy4u&origin=QRCode){target="_blank"}。 确保准备好您的订阅Munchkin ID。

模型上下文协议(Model Context Protocol，MCP)是一种开放标准，它使AI工具能够与外部服务进行通信。 [!DNL Marketo] MCP服务器充当您的AI助手与[!DNL Marketo]之间的桥梁。 它公开跨表单、项目、智能营销策划、潜在客户、电子邮件、代码片段、列表和文件夹的100多项操作。

当您的AI工具调用MCP服务器时，服务器会使用您在每个请求中提供的凭据，代表您执行相应的REST API调用。 您无需安装、部署或运行任何服务器端软件。

有关如何使用Marketo AI和Marketo Engage MCP服务器处理数据的更多信息，请参阅[数据信息](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/marketo-ai/data-information)页。

>[!IMPORTANT]
>
>模型上下文协议(MCP)是一种新兴的开源标准，可能会带来安全性或可靠性风险。 Adobe MCP服务器集成和相关文档按“原样”提供，不提供任何类型的担保。将MCP客户端或服务器连接到Adobe产品是客户选择的配置，客户负责评估任何MCP集成的安全性和适用性。 Adobe对于因错误配置、滥用MCP、第三方实施中的漏洞或通过支持MCP的工作流执行的意外操作而产生的问题，概不负责。为了降低风险，Adobe鼓励您在生产使用之前在沙盒环境中测试集成，并在确认或依赖集成之前，仔细审查和验证所有MCP启动的操作和响应。

## MCP基础知识

>将MCP想象为用于AI应用程序的USB-C端口。 正如USB-C提供了一种标准化方式将您的设备连接到各种外围设备和附件，MCP提供了一种标准化方式将AI模型连接到数据源和工具。 — [模型上下文协议](https://modelcontextprotocol.io/docs/getting-started/intro){target="_blank"}

MCP允许AI工具同时连接到多个外部服务。 例如，AI助手可以：

* 连接到文字处理器以进行AI辅助文档生成
* 连接到动画工具（如Blender）以构建可视化图表
* 连接到Adobe After Effects以进行视频编辑

MCP是一种通信协议：任何应用程序都可以实施的开放标准，向AI工具公开其数据和操作。

## [!DNL Marketo Engage] MCP执行和不执行的操作

了解MCP的范围有助于在连接AI工具之前设定期望值。

**MCP可以：**

* 通过标准REST API提供对[!DNL Marketo]数据和功能的访问
* 使用您在每个请求中提供的凭据代表您执行API调用
* 支持多个同步用户，每个用户均使用自己的凭据进行连接
* 自动处理OAuth令牌刷新。 您不需要管理令牌过期
* 在租户隔离的环境中操作，因此您的数据绝不会与其他用户的会话相交

**MCP不是：**

* 使用、托管或运行任何AI或机器学习模型。 所有AI处理都发生在AI工具中，而不是MCP中
* 对任何数据进行培训或从中学习，包括您的客户数据
* 生成预测、建议或决策。 决策是下游人工智能工具或用户的责任
* 在请求之间存储或保留凭据、请求数据或会话状态
* 要求您安装、部署或管理任何服务器端软件

根据API使用情况，MCP可能会传输数据，包括潜在的敏感字段，但B2B数据涉及客户业务数据，而不涉及PII数据。

## 先决条件

* 启用了REST API访问权限的[!DNL Marketo]实例
* 在[!DNL Marketo] LaunchPoint中创建API凭据的管理员访问权限
* 以下AI工具之一：Claude Desktop、Cursor、Codex、Claude Code (CLI)或具有GitHub Copilot的VS Code
* 对MCP服务器URL的网络访问： `https://marketo-mcp.adobe.io/mcp`

## 获取Marketo凭据

您需要[!DNL Marketo]实例中的以下值：

* **客户端ID**
* **客户端密码**
* **Munchkin帐户ID**

如果您已经拥有这些工具，请跳到[配置您的AI工具](#configure-your-ai-tool)。

### 客户端ID和客户端密码

1. 转到&#x200B;**[!UICONTROL Admin]** > **[!UICONTROL LaunchPoint]**。
1. 选择您的API服务。 如果没有服务类型，请选择&#x200B;**[!UICONTROL New]** > **[!UICONTROL New Service]**，选择&#x200B;**[!UICONTROL Custom]**&#x200B;作为服务类型，并分配一个专用的API用户。
1. 选择&#x200B;**[!UICONTROL View Details]**&#x200B;并复制&#x200B;**[!UICONTROL Client ID]**&#x200B;和&#x200B;**[!UICONTROL Client Secret]**&#x200B;值。

### Munchkin帐户ID

1. 转到&#x200B;**[!UICONTROL Admin]** > **[!UICONTROL Munchkin]**。
1. 复制&#x200B;**[!UICONTROL Munchkin Account ID]**。 格式为`XXX-XXX-XXX`，与实例URL的前缀匹配。

## 配置AI工具

每个AI工具的设置略有不同。 为常用工具提供了连接示例。

* [克劳德桌面](#claude-desktop)
* [光标](#cursor)
* [克劳德代码CLI](#claude-code)
* [OpenAI代码](#codex)
* [带有GitHub Copilot的VSCode](#vscode)
* [Glean](#glean)
* [其他工具](#other-tools)

>[!TIP]
>
>若要连接到多个[!DNL Marketo]实例，请在MCP配置中添加具有唯一名称的单独条目： `marketo-prod`和`marketo-staging`，每个条目都具有相应的凭据。

### 克劳德桌面 {#claude-desktop}

要连接到Claude Desktop，请下载[marketo-mcp-bridge.zip](assets/marketo-mcp-bridge.zip)并将其解包。 将`marketo-mcp-bridge.mjs`放置到已知位置，以便在下一步中可以参考。

您还需要：

* Node.js v18+
* npm

1. 打开Claude Desktop
1. 转到&#x200B;**设置>开发人员>编辑配置**
1. 将以下内容添加到`claude_desktop_config.json`：

```json
{
  "preferences": {
    ...
  },
  "mcpServers": {
    "marketo-mcp": {
      "command": "node",
      "args": ["/path/to/marketo-bridge/bridge.mjs"],
      "env": {
        "MARKETO_MCP_PROD_CLIENT_ID": "<your-client-id>",
        "MARKETO_MCP_PROD_CLIENT_SECRET": "<your-client-secret>",
        "MARKETO_MCP_PROD_MUNCHKIN_ID": "<your-munchkin-id>"
      }
    }
  }
}
```

1. 重新启动Claude Desktop

### 光标 {#cursor}

如果游标MCP配置已包含其他服务器，请在`mcpServers`下添加`marketo`项。以下示例显示项目目录中&#x200B;**[!UICONTROL Settings]** > **[!UICONTROL MCP]**&#x200B;或`.cursor/mcp.json`中的完整`mcpServers`块：

>[!BEGINTABS]

>[!TAB IMS令牌]

```json
{
  "mcpServers": {
    "marketo": {
      "type": "http",
      "url": "https://marketo-mcp.adobe.io/mcp",
      "headers": {
        "Authorization": "Bearer YOUR-IMS-TOKEN",
        "x-gw-ims-org-id": "YOUR-IMS-ORG-ID"
      }
    }
  }
}
```

>[!TAB Marketo客户端凭据]

```json
{
  "mcpServers": {
    "marketo": {
      "type": "http",
      "url": "https://marketo-mcp.adobe.io/mcp",
      "headers": {
        "X-Marketo-Client-Id": "YOUR-CLIENT-ID",
        "X-Marketo-Client-Secret": "YOUR-CLIENT-SECRET",
        "X-Marketo-Munchkin-Id": "YOUR-MUNCHKIN-ID"
      }
    }
  }
}
```

>[!ENDTABS]

重新启动光标。

### 克劳德代码(CLI) {#claude-code}

在终端中运行以下命令，替换您的凭据：

>[!BEGINTABS]

>[!TAB IMS令牌]

```bash
claude mcp add --transport http marketo \
  https://marketo-mcp.adobe.io/mcp \
  --header "Authorization: Bearer YOUR-IMS-TOKEN" \
  --header "x-gw-ims-org-id: YOUR-IMS-ORG-ID"
```

>[!TAB Marketo客户端凭据]

```bash
claude mcp add --transport http marketo \
  https://marketo-mcp.adobe.io/mcp \
  --header "X-Marketo-Client-Id: YOUR-CLIENT-ID" \
  --header "X-Marketo-Client-Secret: YOUR-CLIENT-SECRET" \
  --header "X-Marketo-Munchkin-Id: YOUR-MUNCHKIN-ID"
```

>[!ENDTABS]

### OpenAI代码 {#codex}

1. 转到“设置”>“MCP服务器”>“添加服务器”
1. 添加服务器URL： `https://marketo-mcp.adobe.io/mcp`
1. 为身份验证方法添加标头：

>[!BEGINTABS]

>[!TAB IMS令牌]

* 授权：“持有者YOUR-IMS-TOKEN”
* x-gw-ims-org-id： &quot;YOUR-IMS-ORG-ID&quot;

>[!TAB Marketo客户端凭据]

* X-Marketo-Client-Id： &quot;YOUR-CLIENT-ID&quot;
* X-Marketo-Client-Secret： &quot;YOUR-CLIENT-SECRET&quot;
* X-Marketo-Munchkin-Id： &quot;YOUR-MUNCHKIN-ID&quot;

>[!ENDTABS]

1. 单击保存以完成该过程。


### 使用GitHub Copilot的VS代码 {#vscode}

按&#x200B;**[!UICONTROL Ctrl+Shift+P]**（或macOS上的&#x200B;**[!UICONTROL Cmd+Shift+P]**），键入&#x200B;**[!UICONTROL MCP: Open User Configuration]**，然后按Enter。 这将打开`mcp.json`。 在`servers`对象中添加`marketo`条目：

>[!BEGINTABS]

>[!TAB IMS令牌]

```json
{
  "servers": {
    "marketo": {
      "type": "http",
      "url": "https://marketo-mcp.adobe.io/mcp",
      "headers": {
        "Authorization": "Bearer YOUR-IMS-TOKEN",
        "x-gw-ims-org-id": "YOUR-IMS-ORG-ID"
      }
    }
  }
}
```

>[!TAB Marketo客户端凭据]

```json
{
  "servers": {
    "marketo": {
      "type": "http",
      "url": "https://marketo-mcp.adobe.io/mcp",
      "headers": {
        "X-Marketo-Client-Id": "YOUR-CLIENT-ID",
        "X-Marketo-Client-Secret": "YOUR-CLIENT-SECRET",
        "X-Marketo-Munchkin-Id": "YOUR-MUNCHKIN-ID"
      }
    }
  }
}
```

>[!ENDTABS]

>[!NOTE]
>
>为安全起见，请在配置文件中使用环境变量插值，而不是直接粘贴凭据。 您可以使用语法（如`${MARKETO_CLIENT_SECRET}`）引用变量并在环境中设置它们。 这样可防止在版本控制文件中以纯文本形式存储凭据。

### Glean {#glean}

要将Glean连接到Marketo Engage MCP服务器，必须由[Glean支持团队](https://docs.glean.com/release-notes/releases/2026-04-22-april-release#admin-features)配置以下自定义标头。

| 标头 | 值 |
| ------ | ----- |
| `X-Marketo-Client-Id` | 您的客户端ID |
| `X-Marketo-Client-Secret` | 您的客户端密码 |
| `X-Marketo-Munchkin-Id` | 您的Munchkin帐户ID |

### 其他工具 {#other-tools}

[!DNL Marketo] MCP服务器由Adobe托管，并在公共URL中公开。 任何支持通过可流式传输HTTP传输远程服务器的MCP客户端都可以连接到该客户端。您不需要工具专用的桥接器或任何本地安装的软件。 如果您的工具未在上面列出，请使用下面的连接详细信息来手动配置它。

**连接详细信息：**

| 设置 | 值 |
| ------- | ----- |
| 传输 | HTTP（可流式处理HTTP） |
| 服务器URL | `https://marketo-mcp.adobe.io/mcp` |

**身份验证标头：**

通过每个请求发送以下身份验证方法之一的标头。 在何处输入服务器URL和标头取决于您的工具，因此请查阅其MCP文档。

>[!BEGINTABS]

>[!TAB IMS令牌]

| 标头 | 值 |
| ------ | ----- |
| `Authorization` | `Bearer YOUR-IMS-TOKEN` |
| `x-gw-ims-org-id` | 您的IMS组织ID |

>[!TAB Marketo客户端凭据]

| 标头 | 值 |
| ------ | ----- |
| `X-Marketo-Client-Id` | 您的客户端ID |
| `X-Marketo-Client-Secret` | 您的客户端密码 |
| `X-Marketo-Munchkin-Id` | 您的Munchkin帐户ID |

>[!ENDTABS]

如果您的工具接受JSON配置，请从[Cursor](#cursor)或[VS代码](#vscode)示例开始，并调整键(`mcpServers`、`servers`)以匹配您工具的架构。

## 可用操作

连接后，您可以要求AI助手跨以下类别执行操作。 有关包含API引用的支持的操作的完整列表，请参阅[支持的MCP操作](mcp-server-operations.md)。

### 表单

浏览、创建、克隆和批准表单。 添加或删除字段、配置字段可见性规则以及识别表单嵌入的位置。

示例提示：

* &quot;显示所有批准的表单&quot;
* “将‘与我们联系’表单克隆到第2季度Campaign文件夹”
* 在演示请求表单中添加公司字段

### 智能营销活动

创建智能营销活动、配置智能列表过滤器、添加流程步骤以及激活或停用营销活动。

示例提示：

* “目前哪些智能营销活动处于活动状态？”
* “在‘操作’文件夹中创建一个名为‘潜在客户评分更新’的新智能营销活动”
* “向我显示欢迎电子邮件营销活动中的流程步骤”

### 潜在客户和列表

按电子邮件地址查找潜在客户，创建或更新潜在客户记录，以及管理静态列表成员资格。

示例提示：

* “通过电子邮件查找潜在客户jane@example.com”
* “将潜在客户ID 12345添加到第2季度MQL列表”
* “新建一个称为夏季活动参与者的静态列表”

### 项目

创建、克隆和标记程序。 按类型、渠道或日期范围浏览程序。

示例提示：

* “将Q4网络研讨会项目克隆到2026年事件文件夹”
* “在Campaigns文件夹中创建一个名为Summer Sale的新电子邮件项目”
* “向我显示标记为网络研讨会的所有程序”

### 电子邮件和代码片段

浏览电子邮件、从模板创建电子邮件、更新内容部分和管理可重复使用的代码片段。

示例提示：

* “显示所有草稿电子邮件”
* “更新欢迎电子邮件的标题部分”
* “哪些资产使用假日促销活动片段？”

### 实例结构

要了解您的[!DNL Marketo]配置，请浏览文件夹、渠道、标记类型和活动类型。

示例提示：

* “列出Marketo中的所有文件夹”
* &quot;显示所有可用渠道&quot;
* “配置了哪些标记类型？”

### 批量操作

批量导出潜在客户数据并检查导入或导出作业状态。

示例提示：

* “批量导出过去30天内创建的潜在客户”
* “检查导出作业xx的状态”

## 故障排除

| 错误 | 原因 | 修复 |
| ------- | ------- | ----- |
| “未提供Marketo凭据” | `X-Marketo-Client-Id`、`X-Marketo-Client-Secret`或`X-Marketo-Munchkin-Id`中的一个或多个缺失。 | 验证您的配置中是否存在所有Marketo客户端凭据标头。 |
| “401未授权” | 您的凭据缺失、无效或已过期。 使用Marketo客户端凭据时，客户端ID或客户端密钥不正确。 使用IMS令牌时，令牌无效或过期。 | 验证验证方法的凭据。 对于客户端凭据，在&#x200B;**[!UICONTROL Admin]** > **[!UICONTROL LaunchPoint]**&#x200B;中重新检查&#x200B;**[!UICONTROL Client ID]**&#x200B;和&#x200B;**[!UICONTROL Client Secret]**。 对于IMS令牌，生成新令牌并更新`Authorization`标头。 |
| “403禁止访问” | 您的凭据有效，但您的[!DNL Marketo]实例未启用MCP访问。 | 请与您的[!DNL Marketo] MCP管理员联系，为您的Munchkin帐户ID启用MCP访问。 |
| “请求过多”（速率限制） | 您在短时间内发送的请求过多，或同时发送的请求过多，已达到[!DNL Marketo]实例的API限制。 | 减少一次发送的请求频度和数量，并在重试之前等待一小段时间。 使用专用API用户跟踪和管理您的配额。 |
| 连接超时或已拒绝 | 无法从您的网络访问MCP服务器。 | 确认您可以从环境访问服务器URL。 检查VPN要求（如果适用）。 |
| 工具调用返回空结果 | API用户缺少所请求资源类型的权限。 | 要求您的[!DNL Marketo]管理员检查API用户角色和权限。 |

## 安全性注意事项

>[!IMPORTANT]
>
>在[!DNL Marketo]中使用专用API用户，但仅具有您的工作所需的权限。 请勿重复使用管理员凭据进行API访问。

* **每个请求的凭据。** 客户端ID、客户端密钥、Munchkin ID和REST API端点将随每个请求以HTTP标头传输。 服务器不会存储或缓存这些文件。
* **多租户隔离。** 每个请求都使用自己的凭据集。 您的数据不会与任何其他用户的会话相交。
* **Munchkin ID 允许列表。** 服务器仅接受已批准[!DNL Marketo]实例的请求。 使用未经授权的Munchkin ID的请求会被拒绝，并出现403错误。
* **API速率限制。** MCP服务器继承[!DNL Marketo]实例的API速率限制。 使用专用API用户跟踪和管理配额消耗。
* **将凭据保留在版本控制之外。** 如果AI工具支持使用环境变量插值(`${MARKETO_CLIENT_SECRET}`)，因此凭据不会以纯文本形式存储在存储库文件中。
