---
title: Marketo Engage MCP操作
description: 了解哪些与AI助理一起使用的Marketo Engage MCP操作。
autotag-review: '2026-06-02T13:31:42.084Z'
TQID: 'https://experienceleague.adobe.com/qvrWbHOCsCCHctduNDxMhkE8JAKxZk8FCYfKvzxfcYA'
product_v2:
  - id: b27e5950-9033-45ac-9f86-eb22e567f615
feature_v2:
  - id: a7170d27-32ab-462b-a333-269abc654483
  - id: b0bb9048-d951-48d8-8232-45cf248a7e27
  - id: dca84292-69e9-4116-a575-667d31fa060d
  - id: e64968b2-4ee5-47f9-8cae-0588f184b9eb
topic_v2:
  - id: bbbea26f-9621-49eb-9ab8-e06fb3bbce8c
source-git-commit: c631b7c3d571f29083673f9b97d22230d109abfc
workflow-type: tm+mt
source-wordcount: 1228
ht-degree: 25%

---


# [!DNL Marketo Engage] MCP操作

以下操作可通过[!DNL Marketo Engage] MCP服务器使用。 服务器提供只读或无损端点。 AI系统无法使用`Delete`或其他破坏性操作。

>[!NOTE]
>
>智能列表和智能营销活动`create`和`update`工具的目标发布日期为2026年9月版。

有关如何使用Marketo AI和Marketo Engage MCP服务器处理数据的信息，请参阅[数据信息](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/marketo-ai/data-information)页。

## 批量导出

[批量导出API参考](https://developer.adobe.com/marketo-apis/api/mapi){target="_blank"}

- `bulk_export_create`
- `bulk_export_enqueue`
- `bulk_export_file`
- `bulk_export_status`
- `get_import_status`

## 渠道和标记

[渠道API引用](https://developer.adobe.com/marketo-apis/api/asset#tag/Channels){target="_blank"} | [标记API引用](https://developer.adobe.com/marketo-apis/api/asset#tag/Tags){target="_blank"}

- `browse_channels`
- `browse_tag_types`
- `get_channel_by_name`
- `get_tag_type_by_name`

## 电子邮件

[电子邮件API参考](https://developer.adobe.com/marketo-apis/api/asset#tag/Emails){target="_blank"}

- `approve_email`
- `browse_emails`
- `create_email`
- `get_email_by_id`
- `get_email_by_name`
- `get_email_content`
- `update_email_content`

## 文件夹

[文件夹API参考](https://developer.adobe.com/marketo-apis/api/asset#tag/Folders){target="_blank"}

- `browse_folders`
- `create_folder`
- `delete_folder`
- `get_folder_by_id`
- `get_folder_by_name`
- `get_folder_content`
- `update_folder`

## 表单

[Forms API参考](https://developer.adobe.com/marketo-apis/api/asset#tag/Forms){target="_blank"}

- `add_field_set`
- `add_field_to_form`
- `add_field_visibility_rule`
- `add_rich_text_field`
- `approve_form`
- `browse_forms`
- `clone_form`
- `create_form`
- `delete_field_from_fieldset`
- `delete_form`
- `delete_form_field`
- `discard_form_draft`
- `get_form_by_id`
- `get_form_by_name`
- `get_form_field_metadata`
- `get_form_fields`
- `get_forms_used_by`
- `get_program_member_fields`
- `get_thank_you_page`
- `set_field_autofill`
- `update_field_positions`
- `update_form`
- `update_form_field`

## 潜在客户

[潜在客户API参考](https://developer.adobe.com/marketo-apis/api/mapi#tag/Leads){target="_blank"}

- `add_leads_to_list`
- `describe_lead`
- `get_activity_types`
- `get_lead_activities`
- `get_leads_by_filter`
- `get_leads_by_smart_list`
- `get_paging_token`

## 项目

[程序API参考](https://developer.adobe.com/marketo-apis/api/asset#tag/Programs){target="_blank"}

- `approve_program`
- `browse_email_batch_programs`
- `browse_nurture_programs`
- `browse_program_details`
- `browse_program_events`
- `browse_programs`
- `browse_scheduled_programs`
- `clone_program`
- `create_program`
- `delete_program_tag`
- `get_program_by_id`
- `get_program_by_name`
- `get_program_creation_options`
- `get_program_smart_list`
- `get_programs_by_tag`
- `unapprove_program`
- `update_program`
- `update_program_tag`

## 智能营销活动

[智能营销活动API参考](https://developer.adobe.com/marketo-apis/api/asset#tag/Smart-Campaigns){target="_blank"}

- `activate_smart_campaign`
- `add_flow_step`
- `browse_smart_campaigns`
- `create_smart_campaign`
- `facet_smart_campaigns`
- `get_smart_campaign_auto_suggest`
- `get_smart_campaign_by_id`
- `get_smart_campaign_by_name`
- `get_smart_campaign_flow_step_by_name`
- `get_smart_campaign_flow_step_type_by_name`
- `get_smart_campaign_flow_step_types`
- `get_smart_campaign_flow_steps`
- `get_smart_campaign_rule_by_name`
- `get_smart_campaign_rules`
- `get_smart_campaign_scheduled_runs`
- `get_smart_campaign_used_by`
- `get_smart_list_by_campaign_id`
- `schedule_campaign`
- `trigger_campaign`
- `update_flow_step_choice`
- `update_smart_campaign`

## 智能列表

[智能列表API参考](https://developer.adobe.com/marketo-apis/api/asset#tag/Smart-Lists){target="_blank"}

- `add_smart_list_rule`
- `browse_smart_lists`
- `clone_smart_list`
- `create_smart_list`
- `delete_all_smart_list_rules`
- `get_smart_list_auto_suggest`
- `get_smart_list_by_id`
- `get_smart_list_by_name`
- `get_smart_list_rule_by_name`
- `get_smart_list_rules`
- `get_smart_list_used_by`
- `remove_smart_list_rule_constraint`
- `reorder_smart_list_rules`
- `update_smart_list_filter_logic`
- `update_smart_list_rule`

## 代码段

[代码片段API参考](https://developer.adobe.com/marketo-apis/api/asset#tag/Snippets){target="_blank"}

- `approve_snippet`
- `browse_snippets`
- `clone_snippet`
- `create_snippet`
- `delete_snippet`
- `discard_snippet_draft`
- `facet_snippets`
- `get_snippet_by_id`
- `get_snippet_content`
- `get_snippet_dynamic_content`
- `unapprove_snippet`
- `update_snippet`
- `update_snippet_content`
- `update_snippet_dynamic_content`

## 静态列表

[静态列表API参考](https://developer.adobe.com/marketo-apis/api/mapi#tag/Static-Lists){target="_blank"}

- `browse_lists`
- `create_list`
- `get_list_by_id`
- `get_list_by_name`
- `get_list_members`
- `remove_from_list`
- `update_list`

## 令牌

[令牌API引用](https://developer.adobe.com/marketo-apis/api/asset#tag/Tokens){target="_blank"}

- `create_calendar_token`
- `create_token`
- `delete_token`
- `get_calendar_tokens`
- `get_tokens_by_folder`

## 已启用MCP流程步骤工具

<table style="table-layout:auto">
<tr>
<th>流程步骤</th>
<th>触发器</th>
<th>过滤器（活动）</th>
<th>过滤器（属性）</th>
</tr>
<tr>
<td valign="top"><ul><li>添加到字段集</li><li>添加到列表</li><li>添加到 Microsoft 营销活动</li><li>添加到培养</li><li>添加到 SFDC 营销活动</li><li>调用 Webhook</li><li>更改数据值</li><li>更改潜在客户分区</li><li>更改培养节奏</li><li>更改培养轨迹</li><li>更改所有者</li><li>更改 Microsoft 中的所有者</li><li>更改项目数据</li><li>更改项目成员数据</li><li>更改收入阶段</li><li>更改评分</li><li>更改区段</li><li>进程中的更改状态</li><li>更改 SFDC 营销活动中的状态</li><li>转化商机</li><li>创建任务</li><li>在 Microsoft 中创建任务</li><li>删除潜在客户</li><li>从Microsoft中删除潜在客户</li><li>从SFDC中删除潜在客户</li><li>执行营销活动</li><li>重要时刻</li><li>从字段集中删除</li><li>从流程中移除</li><li>从列表中移除</li><li>从 Microsoft 营销活动中移除</li><li>从 SFDC 营销活动中移除</li><li>请求营销活动</li><li>发送警报</li><li>发送电子邮件</li><li>将潜在客户同步到Microsoft</li><li>将潜在客户同步到SFDC</li><li>等待</li></ul></td>
<td valign="top"><ul><li>活动已记录</li><li>活动已更新</li><li>已添加到列表</li><li>已添加到Microsoft Campaign</li><li>已添加到Nurture</li><li>已添加到机会</li><li>已添加到机会（帐户）</li><li>已添加到机会（联系人）</li><li>已添加到SFDC Campaign</li><li>在活动期间提出问题</li><li>出席活动</li><li>已请求营销活动</li><li>点击链接</li><li>单击电子邮件中的链接</li><li>点击销售电子邮件中的链接</li><li>点击短信消息中的链接</li><li>链接点击次数</li><li>数据值更改</li><li>下载资产</li><li>电子邮件退回</li><li>电子邮件软退回</li><li>电子邮件已投放</li><li>采用对话流程</li><li>使用对话框</li><li>在对话流中与座席接洽</li><li>在对话框中与代理接洽</li><li>填写表单</li><li>具有有趣的时刻</li><li>在对话流中与文档交互</li><li>在对话框中与文档交互</li><li>已发送销售电子邮件</li><li>商机已转化</li><li>商机已创建</li><li>从Microsoft中删除潜在客户</li><li>从SFDC中删除潜在客户</li><li>潜在客户推送到Marketo</li><li>商机已同步到Microsoft</li><li>商机已同步到SFDC</li><li>潜在客户分区更改</li><li>手动阶段更改</li><li>培养节奏变化</li><li>Nurture跟踪更改</li><li>打开电子邮件</li><li>打开销售电子邮件</li><li>机会（帐户）已更新</li><li>机会（联系人）已更新</li><li>机会已更新</li><li>所有者更改</li><li>Microsoft中的所有者更改</li><li>项目群成员数据已更改</li><li>进度状态已更改</li><li>实现对话框目标</li><li>在会话流中实现目标</li><li>已接收转发给朋友的电子邮件</li><li>已从列表中删除</li><li>已从 Microsoft 营销活动中移除</li><li>已从机会中移除</li><li>从机会（帐户）中移除</li><li>已从机会中移除（联系人）</li><li>从SFDC Campaign中移除</li><li>对销售电子邮件的回复</li><li>响应投票</li><li>回复调查</li><li>收入阶段已更改</li><li>销售电子邮件退回</li><li>已收到销售电子邮件</li><li>在对话流中安排会议</li><li>安排对话中的会议</li><li>分数已更改</li><li>区段更改</li><li>已发送警报</li><li>转发给朋友的电子邮件</li><li>短信消息退回</li><li>短信消息已投放</li><li>SFDC Campaign中的状态已更改</li><li>取消订阅电子邮件</li><li>访问网页</li><li>已调用Webhook</li></ul></td>
<td valign="top"><ul><li>活动已记录</li><li>活动已更新</li><li>已发送警报</li><li>已执行营销活动</li><li>已请求营销活动</li><li>单击链接</li><li>电子邮件中的已单击链接</li><li>已单击销售电子邮件中的链接</li><li>短信消息中的点击链接</li><li>已单击链接</li><li>数据值已更改</li><li>下载了资源</li><li>电子邮件已退回</li><li>电子邮件软退回</li><li>已参与会话流</li><li>参与了对话</li><li>在会话流中与座席接洽</li><li>在对话中与代理进行了互动</li><li>已填写表单</li><li>具有有趣的时刻</li><li>已在活动期间提出问题</li><li>已参与活动</li><li>在对话流中与文档交互</li><li>在对话中与文档交互</li><li>潜在客户分区已更改</li><li>商机已转化</li><li>潜在客户已创建</li><li>潜在客户已从Microsoft中删除</li><li>潜在客户已从SFDC中删除</li><li>潜在客户已推送到Marketo</li><li>商机已同步到Microsoft</li><li>商机已同步到SFDC</li><li>培养节奏已更改</li><li>Nurture Track已更改</li><li>已打开的电子邮件</li><li>已打开的销售电子邮件</li><li>机会（帐户）已更新</li><li>机会（联系人）已更新</li><li>机会已更新</li><li>所有者已更改</li><li>在Microsoft中更改了所有者</li><li>项目群成员数据已更改</li><li>进度状态已更改</li><li>达成了对话目标</li><li>会话流中已实现的目标</li><li>已接收转发给朋友的电子邮件</li><li>已回复销售电子邮件</li><li>回复了投票</li><li>已回复一项调查</li><li>收入阶段已更改</li><li>销售电子邮件已退回</li><li>已收到销售电子邮件</li><li>会话流中的已计划会议</li><li>在对话中安排了会议</li><li>分数已更改</li><li>区段已更改</li><li>转发给朋友的电子邮件</li><li>短信消息退回</li><li>已取消订阅电子邮件</li><li>已访问网页</li><li>已添加到列表</li><li>已添加到Nurture</li><li>已添加到机会</li><li>已添加到机会（帐户）</li><li>已添加到机会（联系人）</li><li>已送达电子邮件</li><li>已投放的短信消息</li><li>已从列表中删除</li><li>已从机会中删除</li><li>已从机会（帐户）中删除</li><li>已从机会（联系人）中删除</li><li>已发送电子邮件</li><li>已发送销售电子邮件</li><li>已调用Webhook</li></ul></td>
<td valign="top"><ul><li>帐户所有者电子邮件地址</li><li>帐户所有者名字</li><li>帐户所有者姓氏</li><li>获取日期</li><li>客户获取计划</li><li>客户获取计划名称</li><li>地址</li><li>年营业额</li><li>匿名IP</li><li>帐单寄送地址</li><li>帐单寄送城市</li><li>帐单寄送国家</li><li>帐单邮政编码</li><li>帐单寄送州</li><li>已列入阻止列表</li><li>城市</li><li>公司 Microsoft 类型</li><li>公司名称</li><li>国家/地区</li><li>创建时间</li><li>出生日期</li><li>部门</li><li>请勿来电</li><li>请勿来电的理由</li><li>重复字段</li><li>电子邮件地址</li><li>电子邮件无效</li><li>电子邮件无效原因</li><li>电子邮件暂停</li><li>电子邮件暂停时间</li><li>电子邮件暂停原因</li><li>传真号码</li><li>名字</li><li>全名</li><li>具有机会</li><li>行业</li><li>推断的城市</li><li>推断公司</li><li>推断国家</li><li>推断的都市区</li><li>推断的电话区号</li><li>推断的邮政编码</li><li>推断的状态区域</li><li>是客户</li><li>是合作伙伴</li><li>职务</li><li>姓</li><li>潜在客户所有者电子邮件地址</li><li>潜在客户所有者名字</li><li>潜在客户所有者职务</li><li>潜在客户所有者姓氏</li><li>潜在客户所有者电话号码</li><li>潜在客户分区名称</li><li>销售线索评级</li><li>销售线索分数</li><li>潜在客户来源</li><li>潜在客户状态</li><li>主要电话</li><li>营销暂停</li><li>字段集成员</li><li>List 会员</li><li>Nurture会员</li><li>项目会员</li><li>收入模型会员</li><li>收入阶段成员</li><li>SFDC 营销活动会员</li><li>Smart Campaign 会员</li><li>Smart List 会员</li><li>Microsoft 帐号</li><li>Microsoft 创建日期</li><li>已删除 Microsoft</li><li>Microsoft 类型</li><li>中间名称</li><li>手机号码</li><li>注释</li><li>员工数</li><li>机会数</li><li>原始反向链接</li><li>原始搜索引擎</li><li>原始搜索短语</li><li>原始来源信息</li><li>原始来源类型</li><li>母公司名称</li><li>人员时区</li><li>电话号码</li><li>邮政编码</li><li>随机抽样</li><li>注册Source信息</li><li>注册Source类型</li><li>角色</li><li>称谓</li><li>SFDC帐户编号</li><li>SFDC创建日期</li><li>已删除 SFDC</li><li>SFDC 类型</li><li>标准产业分类(SIC)代码</li><li>现场</li><li>State</li><li>机会总金额</li><li>总机会预期收入</li><li>取消订阅</li><li>退订原因</li><li>更新时间</li><li>网站</li></ul></td>
</tr>
</table>
