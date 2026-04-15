---
title: MCP服务器
description: 了解如何使用MCP服务器将AI助手连接到Marketo。 使用Marketo凭据配置Claude Desktop、Cursor、Claude Code或VS代码。
hidefromtoc: true
badgeBeta: label="Beta 版" type="informative" tooltip="此功能当前处于早期测试阶段"
exl-id: ab446e56-6250-4af5-b03e-162991d09a5c
source-git-commit: ad7c64f3e2cbc25db1c2ffe61e2d42b0acbbd573
workflow-type: tm+mt
source-wordcount: '1272'
ht-degree: 1%

---

# [!DNL Marketo] MCP服务器

模型上下文协议(MCP)是一个开放标准，它使AI工具能够与外部服务进行通信。 [!DNL Marketo] MCP服务器充当您的AI助手与[!DNL Marketo]之间的桥梁。 它公开跨表单、项目、智能营销策划、潜在客户、电子邮件、代码片段、列表和文件夹的100多项操作。

当您的AI工具调用MCP服务器时，服务器会使用您在每个请求中提供的凭据，代表您执行相应的REST API调用。 您无需安装、部署或运行任何服务器端软件。

## 先决条件

- 启用了REST API访问权限的[!DNL Marketo]实例
- 在[!DNL Marketo] LaunchPoint中创建API凭据的管理员访问权限
- 以下AI工具之一：Claude Desktop、Cursor、Claude Code (CLI)或使用GitHub Copilot的VS代码
- 对MCP服务器URL的网络访问： `https://marketo-mcp.adobe.io/mcp`

## 获取Marketo凭据

您需要[!DNL Marketo]实例中的以下值：

- **客户端ID**
- **客户端密码**
- **Munchkin帐户ID**

如果您已经拥有这些工具，请跳到[配置您的AI工具](#configure-your-ai-tool)。

### 客户端ID和客户端密码

1. 转到&#x200B;**[!UICONTROL Admin]** > **[!UICONTROL LaunchPoint]**。
1. 单击您的API服务。 如果没有服务类型，请选择&#x200B;**[!UICONTROL New]** > **[!UICONTROL New Service]**，选择&#x200B;**[!UICONTROL Custom]**&#x200B;作为服务类型，并分配一个专用的API用户。
1. 单击&#x200B;**[!UICONTROL View Details]**&#x200B;并复制&#x200B;**[!UICONTROL Client ID]**&#x200B;和&#x200B;**[!UICONTROL Client Secret]**&#x200B;值。

### Munchkin帐户ID

1. 转到&#x200B;**[!UICONTROL Admin]** > **[!UICONTROL Munchkin]**。
1. 复制&#x200B;**[!UICONTROL Munchkin Account ID]**。 格式为`XXX-XXX-XXX`，与实例URL的前缀匹配。

## 配置AI工具

每个AI工具从不同的位置读取MCP服务器配置。 请在下面找到您的工具，然后按照步骤添加[!DNL Marketo] MCP服务器。

### 克劳德桌面

配置文件是`claude_desktop_config.json`。 从以下位置之一打开它：

- **macOS**： `~/Library/Application Support/Claude/claude_desktop_config.json`
- **Windows**： `%APPDATA%\Claude\claude_desktop_config.json`
- **Linux**： `~/.config/Claude/claude_desktop_config.json`

如果文件已包含其他MCP服务器，请在`mcpServers`下添加`marketo`项。 以下示例显示完整的`mcpServers`块：

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

保存文件，退出Claude Desktop，然后重新打开它。

### 光标

如果游标MCP配置已包含其他服务器，请在`mcpServers`下添加`marketo`项。 以下示例显示项目目录中&#x200B;**[!UICONTROL Settings]** > **[!UICONTROL MCP]**&#x200B;或`.cursor/mcp.json`中的完整`mcpServers`块：

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

重新启动光标。

### 克劳德代码(CLI)

在终端中运行以下命令，替换您的凭据：

```bash
claude mcp add --transport http marketo \
  https://marketo-mcp.adobe.io/mcp \
  --header "X-Marketo-Client-Id: YOUR-CLIENT-ID" \
  --header "X-Marketo-Client-Secret: YOUR-CLIENT-SECRET" \
  --header "X-Marketo-Munchkin-Id: YOUR-MUNCHKIN-ID"
```

### 使用GitHub Copilot的VS代码

在macOS上按&#x200B;**[!UICONTROL Ctrl+Shift+P]**&#x200B;或&#x200B;**[!UICONTROL Cmd+Shift+P]**，然后选择&#x200B;**[!UICONTROL Preferences: Open User Settings (JSON)]**&#x200B;以打开您的VS代码`settings.json`。 添加以下示例：

```json
{
  "mcp": {
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
}
```

按&#x200B;**[!UICONTROL Ctrl+Shift+P]**（或macOS上的&#x200B;**[!UICONTROL Cmd+Shift+P]**），键入&#x200B;**[!UICONTROL Reload Window]**，然后按Enter。

>[!NOTE]
>
>为安全起见，请在配置文件中使用环境变量插值，而不是直接粘贴凭据。 您可以使用语法（如`${MARKETO_CLIENT_SECRET}`）引用变量并在环境中设置它们。 这样可以防止凭据以纯文本形式存储在可能提交到版本控制的文件中。

## 可用操作

连接后，您可以要求AI助手跨以下类别执行操作。

### 表单

浏览、创建、克隆和批准表单。 添加或删除字段、配置字段可见性规则以及识别表单嵌入的位置。

示例提示：

- &quot;显示所有批准的表单&quot;
- “将‘与我们联系’表单克隆到第2季度Campaign文件夹”
- 在演示请求表单中添加公司字段

### 智能营销活动

创建智能营销活动、配置智能列表过滤器、添加流程步骤以及激活或停用营销活动。

示例提示：

- “目前哪些智能营销活动处于活动状态？”
- “在‘操作’文件夹中创建一个名为‘潜在客户评分更新’的新智能营销活动”
- “向我显示欢迎电子邮件营销活动中的流程步骤”

### 潜在客户和列表

按电子邮件地址查找潜在客户，创建或更新潜在客户记录，以及管理静态列表成员资格。

示例提示：

- “通过电子邮件查找潜在客户jane@example.com”
- “将潜在客户ID 12345添加到第2季度MQL列表”
- “新建一个称为夏季活动参与者的静态列表”

### 项目

创建、克隆和标记程序。 按类型、渠道或日期范围浏览程序。

示例提示：

- “将Q4网络研讨会项目克隆到2026年事件文件夹”
- “在Campaigns文件夹中创建一个名为Summer Sale的新电子邮件项目”
- “向我显示标记为网络研讨会的所有程序”

### 电子邮件和代码片段

浏览电子邮件、从模板创建电子邮件、更新内容部分和管理可重复使用的代码片段。

示例提示：

- “显示所有草稿电子邮件”
- “更新欢迎电子邮件的标题部分”
- “哪些资产使用假日促销活动片段？”

### 实例结构

浏览文件夹、渠道、标记类型和活动类型以了解您的[!DNL Marketo]配置。

示例提示：

- “列出Marketo中的所有文件夹”
- &quot;显示所有可用渠道&quot;
- “配置了哪些标记类型？”

### 批量操作

批量导出潜在客户数据并检查导入或导出作业状态。

示例提示：

- “批量导出过去30天内创建的潜在客户”
- “检查导出作业xx的状态”

## 故障排除

| 错误 | 原因 | 修复 |
| ------- | ------- | ----- |
| “未提供Marketo端点” | 您的配置中缺少`X-Marketo-Endpoint`标头。 | 重新检查您的MCP配置，并确认所有四个标头都存在。 |
| “未提供Marketo凭据” | `X-Marketo-Client-Id`、`X-Marketo-Client-Secret`或`X-Marketo-Munchkin-Id`中的一个或多个缺失。 | 验证配置中是否存在所有四个标头。 |
| “身份验证错误” | 您的凭据无效或已过期。 | 在&#x200B;**[!UICONTROL Admin]** > **[!UICONTROL LaunchPoint]**&#x200B;中重新检查您的客户端ID和客户端密钥。 |
| “403禁止访问” | 您的Munchkin ID不在服务器允许列表上。 | 请联系您的[!DNL Marketo] MCP管理员以添加您的Munchkin ID。 |
| 连接超时或已拒绝 | 无法从您的网络访问MCP服务器。 | 确认您可以从环境访问服务器URL。 检查VPN要求（如果适用）。 |
| 工具调用返回空结果 | API用户缺少所请求资源类型的权限。 | 要求您的[!DNL Marketo]管理员检查API用户角色和权限。 |

## 常见问题

+++我的数据安全吗？

凭据将以HTTP标头与每个单独的请求一起传输。 服务器不会在会话之间存储或缓存凭据，并且每个请求都是完全隔离的。

+++

+++是否可以多人同时使用它？

可以。 服务器是多租户。 每个用户都使用自己的凭据进行连接，并且请求相互隔离。

+++

+++如果我的访问令牌过期，会发生什么情况？

使用客户端ID和客户端密钥进行身份验证时，服务器会自动处理令牌刷新。 您无需采取任何操作。

+++

+++我需要安装或运行任何项目吗？

不会。 MCP服务器由Adobe托管。 您只需配置人工智能工具即可连接到该工具。

+++

+++我的API用户需要哪些[!DNL Marketo]权限？

API用户需要访问您打算管理的资源类型。 至少为浏览操作分配只读角色，为创建或修改资产分配读写角色。 与您的[!DNL Marketo]管理员合作以分配适当的权限。

+++

+++什么是速率限制？

MCP服务器继承[!DNL Marketo]实例的API速率限制。 使用专用API用户跟踪和管理配额消耗。

+++

+++支持哪些AI工具？

Claude Desktop、Cursor、Claude Code (CLI)和带GitHub Copilot的VS代码。 任何支持通过HTTP的模型上下文协议的AI工具都应正常工作。

+++

+++我可以连接到多个[!DNL Marketo]实例吗？

可以。 在AI工具的MCP配置中添加多个条目，每个条目都具有唯一名称和相应实例的凭据。 例如，您可以将`marketo-prod`和`marketo-staging`配置为单独的服务器。

+++

## 安全性注意事项

>[!IMPORTANT]
>
>在[!DNL Marketo]中使用专用API用户，但仅具有您的工作所需的权限。 请勿重复使用管理员凭据进行API访问。

- **每个请求的凭据。** 客户端ID、客户端密钥、Munchkin ID和REST API端点将随每个请求以HTTP标头传输。 服务器不会存储或缓存这些文件。
- **多租户隔离。** 每个请求都使用自己的凭据集。 您的数据不会与任何其他用户的会话相交。
- **Munchkin ID 允许列表。** 服务器仅接受已批准[!DNL Marketo]实例的请求。 使用未经授权的Munchkin ID的请求会被拒绝，并出现403错误。
- **将凭据保留在版本控制之外。** 如果您的AI工具支持使用环境变量插值(`${MARKETO_CLIENT_SECRET}`)，因此凭据不会以纯文本形式存储在提交到存储库的文件中。
