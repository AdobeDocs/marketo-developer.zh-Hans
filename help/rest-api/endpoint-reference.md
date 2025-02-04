---
title: 终结点引用
feature: REST API
description: Marketo API端点引用
exl-id: 27d16b6f-865a-4e40-ab9c-cbabe2927472
source-git-commit: 8a019985fc9ce7e1aa690ca26bfa263cd3c48cfc
workflow-type: tm+mt
source-wordcount: '4676'
ht-degree: 3%

---

# 终结点引用

以下是Marketo REST API引用的链接。

- [资源](https://developer.adobe.com/marketo-apis/api/asset/)
- [身份标识](https://developer.adobe.com/marketo-apis/api/identity/)
- [潜在客户数据库](https://developer.adobe.com/marketo-apis/api/mapi/)
- [User Management](https://developer.adobe.com/marketo-apis/api/user/)

## 端点列表

以下是REST API端点的完整列表。

| 名称 | 组 | 方法 | URI | 所需权限 |
|---|---|---|---|---|
| 添加自定义活动 | 活动 | POST | /rest/v1/activities/external.json | 读写活动 |
| 批准自定义活动类型 | 活动 | POST | /rest/v1/activities/external/type/{apiName}/approve.json | 读写活动元数据 |
| 创建自定义活动类型属性 | 活动 | POST | /rest/v1/activities/external/type/{apiName}/attributes/create.json | 读写活动元数据 |
| 创建自定义活动类型 | 活动 | POST | /rest/v1/activities/external/type.json | 读写活动元数据 |
| 删除自定义活动类型 | 活动 | POST | /rest/v1/activities/external/type/{apiName}/delete.json | 读写活动元数据 |
| 删除自定义活动类型属性 | 活动 | POST | /rest/v1/activities/external/type/{apiName}/attributes/delete.json | 读写活动元数据 |
| 描述自定义活动类型 | 活动 | GET | /rest/v1/activities/external/type/{apiName}/describe.json | 只读活动元数据 |
| 放弃自定义活动类型草稿 | 活动 | POST | /rest/v1/activities/external/type/{apiName}/discardDraft.json | 读写活动元数据 |
| 获取活动类型 | 活动 | GET | /rest/v1/activities/types.json | 只读活动 |
| 获取自定义活动类型 | 活动 | GET | /rest/v1/activities/external/types.json | 只读活动元数据 |
| 获取已删除的潜在客户 | 活动 | GET | /rest/v1/activities/deletedleads.json | 只读活动 |
| 获取潜在客户活动 | 活动 | GET | /rest/v1/activities.json | 只读活动 |
| 获取潜在客户更改 | 活动 | GET | /rest/v1/activities/leadchanges.json | 只读活动 |
| 获取分页令牌 | 活动 | GET | /rest/v1/activities/pagingtoken.json | 只读活动 |
| 更新自定义活动类型 | 活动 | POST | /rest/v1/activities/external/type/{apiName}.json | 读写活动元数据 |
| 更新自定义活动类型属性 | 活动 | POST | /rest/v1/activities/external/type/{apiName}/attributes/update.json | 读写活动元数据 |
| 身份标识 | 身份验证 | GET或POST | /identity/oauth/token | None |
| 取消导出活动作业 | 批量导出活动 | POST | /bulk/v1/activities/export/{exportid}/cancel.json | 只读活动 |
| 创建导出活动作业 | 批量导出活动 | POST | /bulk/v1/activities/export/create.json | 只读活动 |
| 将导出活动作业排入队列 | 批量导出活动 | POST | /bulk/v1/activities/export/{exportid}/enqueue.json | 只读活动 |
| 获取导出活动文件 | 批量导出活动 | GET | /bulk/v1/activities/export/{exportid}/file.json | 只读活动 |
| 获取导出活动作业状态 | 批量导出活动 | GET | /bulk/v1/activities/export/{exportid}/status.json | 只读活动 |
| 获取导出活动作业 | 批量导出活动 | GET | /bulk/v1/activities/export.json | 只读活动 |
| 取消导出自定义对象作业 | 批量导出自定义对象 | POST | /bulk/v1/customobjects/export/{exportid}/cancel.json | 只读自定义对象 |
| 创建导出自定义对象作业 | 批量导出自定义对象 | POST | /bulk/v1/customobjects/export/create.json | 只读自定义对象 |
| 排入导出自定义对象作业 | 批量导出自定义对象 | POST | /bulk/v1/customobjects/export/{exportid}/enqueue.json | 只读自定义对象 |
| 获取导出自定义对象文件 | 批量导出自定义对象 | GET | /bulk/v1/customobjects/export/{exportid}/file.json | 只读自定义对象 |
| 获取导出自定义对象作业状态 | 批量导出自定义对象 | GET | /bulk/v1/customobjects/export/{exportid}/status.json | 只读自定义对象 |
| 获取导出自定义对象作业 | 批量导出自定义对象 | GET | /bulk/v1/customobjects/export.json | 只读自定义对象 |
| 取消导出潜在客户作业 | 批量导出潜在客户 | POST | /bulk/v1/leads/export/{exportid}/cancel.json | 只读潜在客户 |
| 创建导出潜在客户作业 | 批量导出潜在客户 | POST | /bulk/v1/leads/export/create.json | 只读潜在客户 |
| 将导出潜在客户作业排入队列 | 批量导出潜在客户 | POST | /bulk/v1/leads/export/{exportid}/enqueue.json | 只读潜在客户 |
| 获取导出潜在客户文件 | 批量导出潜在客户 | GET | /bulk/v1/leads/export/{exportid}/file.json | 只读潜在客户 |
| 获取导出潜在客户作业状态 | 批量导出潜在客户 | GET | /bulk/v1/leads/export/{exportid}/status.json | 只读潜在客户 |
| 获取导出潜在客户作业 | 批量导出潜在客户 | GET | /bulk/v1/leads/export.json | 只读潜在客户 |
| 取消导出计划成员作业 | 批量导出计划成员 | POST | /bulk/v1/program/members/export/{exportid}/cancel.json | 只读潜在客户 |
| 创建导出程序成员作业 | 批量导出计划成员 | POST | /bulk/v1/program/members/export/create.json | 只读潜在客户 |
| 排入导出项目成员作业 | 批量导出计划成员 | POST | /bulk/v1/program/members/export/{exportid}/enqueue.json | 只读潜在客户 |
| 获取导出程序成员文件 | 批量导出计划成员 | GET | /bulk/v1/program/members/export/{exportid}/file.json | 只读潜在客户 |
| 获取导出计划成员作业状态 | 批量导出计划成员 | GET | /bulk/v1/program/members/export/{exportid}/status.json | 只读潜在客户 |
| 获取导出程序成员作业 | 批量导出计划成员 | GET | /bulk/v1/program/members/export.json | 只读潜在客户 |
| 获取导入自定义对象失败 | 批量导入自定义对象 | GET | /bulk/v1/customobjects/import/{id}/failures.json | 读写自定义对象 |
| 获取导入自定义对象状态 | 批量导入自定义对象 | GET | /bulk/v1/customobjects/import/{id}/status.json | 读写自定义对象 |
| 获取导入自定义对象警告 | 批量导入自定义对象 | GET | /bulk/v1/customobjects/import/{id}/warnings.json | 读写自定义对象 |
| 导入自定义对象 | 批量导入自定义对象 | POST | /bulk/v1/customobjects/{apiName}/import.json | 读写自定义对象 |
| 获取导入潜在客户失败 | 批量导入潜在客户 | GET | /bulk/v1/leads/batch/{id}/failures.json | 读写潜在客户 |
| 获取导入商机状态 | 批量导入潜在客户 | GET | /bulk/v1/leads/batch/{id}.json | 读写潜在客户 |
| 获取导入商机警告 | 批量导入潜在客户 | GET | /bulk/v1/leads/batch/{id}/warnings.json | 读写潜在客户 |
| 导入潜在客户 | 批量导入潜在客户 | POST | /bulk/v1/leads.json | 读写潜在客户 |
| 获取导入程序成员失败 | 批量导入计划成员 | GET | /bulk/v1/program/members/import/{id}/failures.json | 读写潜在客户 |
| 获取导入计划成员状态 | 批量导入计划成员 | GET | /bulk/v1/program/members/import/{id}/status.json | 读写潜在客户 |
| 获取导入程序成员警告 | 批量导入计划成员 | GET | /bulk/v1/program/members/import/{id}/warnings.json | 读写潜在客户 |
| 导入计划成员 | 批量导入计划成员 | POST | /bulk/v1/program/{programId}/members/import.json | 读写潜在客户 |
| 按ID获取营销活动 | 营销活动 | GET | /rest/v1/campaigns/{id}.json | 只读营销活动 |
| 获取营销活动 | 营销活动 | GET | /rest/v1/campaigns.json | 只读营销活动 |
| 请求营销活动 | 营销活动 | POST | /rest/v1/campaigns/{id}/trigger.json | 读写营销活动 |
| 计划营销活动 | 营销活动 | POST | /rest/v1/campaigns/{id}/schedule.json | 读写营销活动 |
| 按名称获取渠道 | 渠道 | GET | /rest/asset/v1/channel/byName.json | 只读资产 |
| 获取渠道 | 渠道 | GET | /rest/asset/v1/channels.json | 只读资产 |
| 删除公司 | 公司 | POST | /rest/v1/companies/delete.json | 读写公司 |
| 描述公司 | 公司 | GET | /rest/v1/companies/describe.json | 只读公司 |
| 获取公司 | 公司 | GET | /rest/v1/companies.json | 只读公司 |
| 同步公司 | 公司 | POST | /rest/v1/companies.json | 读写公司 |
| 按名称获取公司字段 | 公司 | GET | /rest/v1/companies/schema/fields/{fieldApiName}.json | 读写架构自定义字段 |
| 获取公司字段 | 公司 | GET | /rest/v1/companies/schema/fields.json | 读写架构自定义字段 |
| 添加自定义对象类型字段 | 自定义对象 | POST | /rest/v1/customobjects/schema/{apiName}/addField.json | 读写自定义对象类型 |
| 批准自定义对象类型 | 自定义对象 | POST | /rest/v1/customobjects/schema/{apiName}/approve.json | 读写自定义对象类型 |
| 删除自定义对象 | 自定义对象 | POST | /rest/v1/customobjects/{name}/delete.json | 读写自定义对象 |
| 删除自定义对象类型 | 自定义对象 | POST | /rest/v1/customobjects/schema/{apiName}/delete.json | 读写自定义对象类型 |
| 删除自定义对象类型字段 | 自定义对象 | POST | /rest/v1/customobjects/schema/{apiName}/deleteField.json | 读写自定义对象类型 |
| 描述自定义对象 | 自定义对象 | GET | /rest/v1/customobjects/{name}/describe.json | 只读自定义对象 |
| 描述自定义对象类型 | 自定义对象 | GET | /rest/v1/customobjects/schema/{apiName}/describe.json | 只读自定义对象类型 |
| 放弃自定义对象类型草稿 | 自定义对象 | POST | /rest/v1/customobjects/schema/{apiName}/discardDraft.json | 读写自定义对象类型 |
| 获取自定义对象 | 自定义对象 | GET | /rest/v1/customobjects/{name}.json | 只读自定义对象 |
| 获取自定义对象可链接对象 | 自定义对象 | GET | /rest/v1/customobjects/schema/linkableObjects.json | 只读自定义对象类型 |
| 获取依赖于自定义对象的Assets | 自定义对象 | GET | /rest/v1/customobjects/schema/{apiName}/dependentAssets.json | 只读自定义对象类型 |
| 获取自定义对象类型字段数据类型 | 自定义对象 | GET | /rest/v1/customobjects/schema/fieldDataTypes.json | 只读自定义对象类型 |
| 自定义对象列表 | 自定义对象 | GET | /rest/v1/customobjects.json | 只读自定义对象 |
| 自定义对象类型列表 | 自定义对象 | GET | /rest/v1/customobjects/schema.json | 只读自定义对象类型 |
| 同步自定义对象 | 自定义对象 | POST | /rest/v1/customobjects/{name}.json | 读写自定义对象 |
| 同步自定义对象类型 | 自定义对象 | POST | /rest/v1/customobjects/schema.json | 读写自定义对象类型 |
| 更新自定义对象类型字段 | 自定义对象 | POST | /rest/v1/customobjects/schema/{apiName}/updateField.json | 读写自定义对象类型 |
| 批准电子邮件模板草稿 | 电子邮件模板 | POST | /rest/asset/v1/emailTemplate/{id}/approveDraft.json | 读写资产 |
| 克隆电子邮件模板 | 电子邮件模板 | POST | /rest/asset/v1/emailTemplate/{id}/clone.json | 读写资产 |
| 创建电子邮件模板 | 电子邮件模板 | POST | /rest/asset/v1/emailTemplates.json | 读写资产 |
| 删除电子邮件模板 | 电子邮件模板 | POST | /rest/asset/v1/emailTemplate/{id}/delete.json | 读写资产 |
| 放弃电子邮件模板草稿 | 电子邮件模板 | POST | /rest/asset/v1/emailTemplate/{id}/discardDraft.json | 读写资产 |
| 按ID获取电子邮件模板 | 电子邮件模板 | GET | /rest/asset/v1/emailTemplate/{id}.json | 只读资产 |
| 按名称获取电子邮件模板 | 电子邮件模板 | GET | /rest/asset/v1/emailTemplate/byName.json | 只读资产 |
| 按ID获取电子邮件模板内容 | 电子邮件模板 | GET | /rest/asset/v1/emailTemplate/{id}/content.json | 只读资产 |
| 获取使用的电子邮件模板 | 电子邮件模板 | GET | /rest/asset/v1/emailTemplates/{id}/usedBy.json | 只读资产 |
| 获取电子邮件模板 | 电子邮件模板 | GET | /rest/asset/v1/emailTemplates.json | 只读资产 |
| 取消批准电子邮件模板草稿 | 电子邮件模板 | POST | /rest/asset/v1/emailTemplate/{id}/unapprove.json | 读写资产 |
| 更新电子邮件模板内容 | 电子邮件模板 | POST | /rest/asset/v1/emailTemplate/{id}/content.json | 读写资产 |
| 更新电子邮件模板元数据 | 电子邮件模板 | POST | /rest/asset/v1/emailTemplate/{id}.json | 读写资产 |
| 添加电子邮件模块 | 电子邮件 | POST | /rest/asset/v1/email/{id}/content/{moduleId}/add.json | 读写资产 |
| 批准电子邮件草稿 | 电子邮件 | POST | /rest/asset/v1/email/{id}/approveDraft.json | 读写资产 |
| 克隆电子邮件 | 电子邮件 | POST | /rest/asset/v1/email/{id}/clone.json | 读写资产 |
| 创建电子邮件 | 电子邮件 | POST | /rest/asset/v1/emails.json | 读写资产 |
| 删除电子邮件 | 电子邮件 | POST | /rest/asset/v1/email/{id}/delete.json | 读写资产 |
| 删除模块 | 电子邮件 | POST | /rest/asset/v1/email/{id}/content/{moduleId}/delete.json | 读写资产 |
| 放弃电子邮件草稿 | 电子邮件 | POST | /rest/asset/v1/email/{id}/discardDraft.json | 读写资产 |
| 复制电子邮件模块 | 电子邮件 | POST | /rest/asset/v1/email/{id}/content/{moduleId}/duplicate.json | 读写资产 |
| 按ID获取电子邮件 | 电子邮件 | GET | /rest/asset/v1/email/{id}.json | 只读资产 |
| 按名称获取电子邮件 | 电子邮件 | GET | /rest/asset/v1/email/byName.json | 只读资产 |
| 获取电子邮件内容 | 电子邮件 | GET | /rest/asset/v1/email/{id}/content.json | 只读资产 |
| 获取电子邮件动态内容 | 电子邮件 | GET | /rest/asset/v1/email/{id}/dynamicContent/{dynamicContentId}.json | 只读资产 |
| 获取电子邮件完整内容 | 电子邮件 | GET | /rest/asset/v1/email/{id}/fullContent.json | 只读资产 |
| 获取电子邮件变量 | 电子邮件 | GET | /rest/asset/v1/email/{id}/variables.json | 只读资产 |
| 获取电子邮件抄送字段 | 电子邮件 | GET | /rest/asset/v1/email/ccFields.json | 只读资产 |
| 获取电子邮件 | 电子邮件 | GET | /rest/asset/v1/emails.json | 只读资产 |
| 重新排列电子邮件模块 | 电子邮件 | POST | /rest/asset/v1/email/{id}/content/rearrange.json | 读写资产 |
| 重命名电子邮件模块 | 电子邮件 | POST | /rest/asset/v1/email/{id}/content/{moduleId}/rename.json | 读写资产 |
| 发送示例电子邮件 | 电子邮件 | POST | /rest/asset/v1/email/{id}/sendSample.json | 读写资产 |
| 取消批准电子邮件 | 电子邮件 | POST | /rest/asset/v1/email/{id}/unapprove.json | 读写资产 |
| 更新电子邮件内容 | 电子邮件 | POST | /rest/asset/v1/email/{id}/content.json | 读写资产 |
| 更新电子邮件内容部分 | 电子邮件 | POST | /rest/asset/v1/email/{id}/content/{htmlId}.json | 读写资产 |
| 更新电子邮件动态内容部分 | 电子邮件 | POST | /rest/asset/v1/email/{id}/dynamicContent/{dynamicContentId}.json | 读写资产 |
| 更新电子邮件完整内容 | 电子邮件 | POST | /rest/asset/v1/emails/{id}/fullContent.json | 读写资产 |
| 更新电子邮件元数据 | 电子邮件 | POST | /rest/asset/v1/email/{id}.json | 读写资产 |
| 更新电子邮件变量 | 电子邮件 | POST | /rest/asset/v1/email/{id}/variable/{name}.json | 读写资产 |
| 创建文件 | 文件 | POST | /rest/asset/v1/files.json | 读写资产 |
| 按ID获取文件 | 文件 | GET | /rest/asset/v1/file/{id}.json | 只读资产 |
| 按名称获取文件 | 文件 | GET | /rest/asset/v1/file/byName.json | 只读资产 |
| 获取文件 | 文件 | GET | /rest/asset/v1/files.json | 只读资产 |
| 更新文件内容 | 文件 | POST | /rest/asset/v1/file/{id}/content.json | 读写资产 |
| 创建文件夹 | 文件夹 | POST | /rest/asset/v1/folders.json | 读写资产 |
| 删除文件夹 | 文件夹 | POST | /rest/asset/v1/folder/{id}/delete.json | 读写资产 |
| 按ID获取文件夹 | 文件夹 | GET | /rest/asset/v1/folder/{id}.json | 只读资产 |
| 按名称获取文件夹 | 文件夹 | GET | /rest/asset/v1/folder/byName.json | 只读资产 |
| 获取文件夹内容 | 文件夹 | GET | /rest/asset/v1/folder/{id}/content.json | 只读资产 |
| 获取文件夹 | 文件夹 | GET | /rest/asset/v1/folders.json | 只读资产 |
| 更新文件夹元数据 | 文件夹 | POST | /rest/asset/v1/folder/{id}.json | 读写资产 |
| 将字段添加到表单 | 表单字段 | POST | /rest/asset/v1/form/{id}/fields.json | 读写资产 |
| 将字段集添加到表单 | 表单字段 | POST | /rest/asset/v1/form/{id}/fieldSet.json | 读写资产 |
| 添加表单字段可见性规则 | 表单字段 | POST | /rest/asset/v1/form/{formId}/field/{fieldId}/visibility.json | 读写资产 |
| 添加富文本字段 | 表单字段 | POST | /rest/asset/v1/form/{id}/richText.json | 读写资产 |
| 从字段集中删除字段 | 表单字段 | POST | /rest/asset/v1/form/{id}/fieldSet/{fieldSetId}/field/{fieldId}/delete.json | 读写资产 |
| 删除表单字段 | 表单字段 | POST | /rest/asset/v1/form/{id}/field/{fieldId}/delete.json | 读写资产 |
| 获取可用的表单字段 | 表单字段 | GET | /rest/asset/v1/form/fields.json | 只读资产 |
| 获取可用的表单计划成员字段 | 表单字段 | GET | /rest/asset/v1/form/programMemberFields.json | 只读资产 |
| 获取表单字段 | 表单字段 | GET | /rest/asset/v1/form/{id}/fields.json | 只读资产 |
| 更新字段位置 | 表单字段 | POST | /rest/asset/v1/form/{id}/reArrange.json | 读写资产 |
| 更新表单字段 | 表单字段 | POST | /rest/asset/v1/form/{id}/field/{fieldId}.json | 读写资产 |
| 批准表单草稿 | Forms | POST | /rest/asset/v1/form/{id}/approveDraft.json | 读写资产 |
| 克隆表单 | Forms | POST | /rest/asset/v1/form/{id}/clone.json | 读写资产 |
| 创建表单 | Forms | POST | /rest/asset/v1/forms.json | 读写资产 |
| 获取表单使用者 | Forms | GET | /rest/asset/v1/form/{id}/usedBy.json | 读写资产 |
| 删除表单 | Forms | POST | /rest/asset/v1/form/{id}/delete.json | 读写资产 |
| 放弃表单草稿 | Forms | POST | /rest/asset/v1/form/{id}/discardDraft.json | 读写资产 |
| 按ID获取表单 | Forms | GET | /rest/asset/v1/form/{id}.json | 只读资产 |
| 按名称获取表单 | Forms | GET | /rest/asset/v1/form/byName.json | 只读资产 |
| 获取Forms | Forms | GET | /rest/asset/v1/forms.json | 只读资产 |
| 按表单ID获取感谢页面 | Forms | GET | /rest/asset/v1/form/{id}/thankYouPage.json | 只读资产 |
| 更新表单元数据 | Forms | POST | /rest/asset/v1/form/{id}.json | 读写资产 |
| 更新提交按钮 | Forms | POST | /rest/asset/v1/{id}/submitButton.json | 读写资产 |
| 更新感谢页面 | Forms | POST | /rest/asset/v1/form/{id}/thankYouPage.json | 读写资产 |
| 添加登陆页面内容部分 | 登陆页面内容 | POST | /rest/asset/v1/landingPage/{id}/content.json | 读写资产 |
| 删除登陆页面内容部分 | 登陆页面内容 | POST | /rest/asset/v1/landingPage/{id}/content/{contentId}/delete.json | 读写资产 |
| 获取登陆页面内容 | 登陆页面内容 | GET | /rest/asset/v1/landingPage/{id}/content.json | 只读资产 |
| 获取登陆页面动态内容 | 登陆页面内容 | GET | /rest/asset/v1/landingPage/{id}/dynamicContent/{dynamicContentId}.json | 只读资产 |
| 更新登陆页面内容部分 | 登陆页面内容 | POST | /rest/asset/v1/landingPage/{id}/content/{contentId}.json | 读写资产 |
| 更新登陆页面动态内容部分 | 登陆页面内容 | POST | /rest/asset/v1/landingPage/{id}/dynamicContent/{dynamicContentId}.json | 读写资产 |
| 批准登陆页面模板草稿 | 登陆页面模板 | POST | /rest/asset/v1/landingPageTemplate/{id}/approveDraft.json | 读写资产 |
| 克隆登陆页面模板 | 登陆页面模板 | POST | /rest/asset/v1/landingPageTemplate/{id}/clone.json | 读写资产 |
| 创建登陆页面模板 | 登陆页面模板 | POST | /rest/asset/v1/landingPageTemplate.json | 读写资产 |
| 删除登陆页面模板 | 登陆页面模板 | POST | /rest/asset/v1/landingPageTemplate/{id}/delete.json | 读写资产 |
| 放弃登陆页面模板草稿 | 登陆页面模板 | POST | /rest/asset/v1/landingPageTemplate/{id}/discardDraft.json | 读写资产 |
| 按ID获取登陆页面模板 | 登陆页面模板 | GET | /rest/asset/v1/landingPageTemplate/{id}.json | 只读资产 |
| 按名称获取登陆页面模板 | 登陆页面模板 | GET | /rest/asset/v1/landingPageTemplates/byName.json | 只读资产 |
| 获取登陆页面模板内容 | 登陆页面模板 | GET | /rest/asset/v1/landingPageTemplate/{id}/content.json | 只读资产 |
| 获取登陆页面模板 | 登陆页面模板 | GET | /rest/asset/v1/landingPageTemplates.json | 只读资产 |
| 取消批准登陆页面模板 | 登陆页面模板 | POST | /rest/asset/v1/landingPageTemplate/{id}/unapprove.json | 读写资产 |
| 更新登陆页面模板内容 | 登陆页面模板 | POST | /rest/asset/v1/landingPageTemplate/{id}/content.json | 读写资产 |
| 更新登陆页面模板元数据 | 登陆页面模板 | POST | /rest/asset/v1/landingPageTemplate/{id}.json | 读写资产 |
| 批准登陆页面草稿 | 登陆页面 | POST | /rest/asset/v1/landingPage/{id}/approveDraft.json | 读写资产 |
| 克隆登陆页面 | 登陆页面 | POST | /rest/asset/v1/landingPage/{id}/clone.json | 读写资产 |
| 创建登陆页面 | 登陆页面 | POST | /rest/asset/v1/landingPages.json | 读写资产 |
| 删除登陆页面 | 登陆页面 | POST | /rest/asset/v1/landingPage/{id}/delete.json | 读写资产 |
| 放弃登陆页面草稿 | 登陆页面 | POST | /rest/asset/v1/landingPage/{id}/discardDraft.json | 读写资产 |
| 按ID获取登陆页面 | 登陆页面 | GET | /rest/asset/v1/landingPage/{id}.json | 只读资产 |
| 按名称获取登陆页面 | 登陆页面 | GET | /rest/asset/v1/landingPage/byName.json | 只读资产 |
| 获取登陆页面变量 | 登陆页面 | GET | /rest/asset/v1/landingPage/{id}/variables.json | 只读资产 |
| 获取登陆页面 | 登陆页面 | GET | /rest/asset/v1/landingPages.json | 只读资产 |
| 预览登陆页面 | 登陆页面 | GET | /rest/asset/v1/landingPage/{id}/preview.json | 只读资产 |
| 取消批准登陆页面 | 登陆页面 | POST | /rest/asset/v1/landingPage/{id}/unapprove.json | 读写资产 |
| 更新登陆页面元数据 | 登陆页面 | POST | /rest/asset/v1/{id}.json | 读写资产 |
| 更新登陆页面变量 | 登陆页面 | POST | /rest/asset/v1/landingPage/{id}/variable/{variableId}.json | 读写资产 |
| 创建登陆页面重定向规则 | 登陆页面 | POST | /rest/asset/v1/redirectRules.json | 读写重定向规则 |
| 删除登陆页面重定向规则 | 登陆页面 | POST | /rest/asset/v1/redirectRule/{id}/delete.json | 读写重定向规则 |
| 获取登陆页面重定向规则 | 登陆页面 | GET | /rest/asset/v1/redirectRules.json | 只读重定向规则 |
| 按ID获取登陆页面重定向规则 | 登陆页面 | GET | /rest/asset/v1/redirectRule/{id}.json | 只读重定向规则 |
| 更新登陆页面重定向规则 | 登陆页面 | POST | /rest/asset/v1/redirectRule/{id}.json | 读写重定向规则 |
| 获取登陆页面域 | 登陆页面 | GET | /rest/asset/v1/landingPageDomains.json | 只读重定向规则 |
| 关联潜在客户 | 潜在客户 | POST | /rest/v1/leads/{id}/associate.json | 读写潜在客户 |
| 更改潜在客户计划状态 | 潜在客户 | POST | /rest/v1/leads/programs/{programId}/status.json | 读写潜在客户 |
| 删除潜在客户 | 潜在客户 | POST | /rest/v1/leads.json | 读写潜在客户 |
| 描述潜在客户 | 潜在客户 | GET | /rest/v1/leads/describe.json | 只读潜在客户 |
| 描述潜在客户2 | 潜在客户 | GET | /rest/v1/leads/describe2.json | 只读潜在客户 |
| 描述计划成员 | 潜在客户 | GET | /rest/v1/program/members/describe.json | 只读潜在客户 |
| 按ID获取潜在客户 | 潜在客户 | GET | /rest/v1/lead/{id}.json | 只读潜在客户 |
| 获取潜在客户分区 | 潜在客户 | GET | /rest/v1/leads/partitions.json | 只读潜在客户 |
| 按筛选器类型获取潜在客户 | 潜在客户 | GET | /rest/v1/leads.json | 只读潜在客户 |
| 按项目ID获取潜在客户 | 潜在客户 | GET | /rest/v1/leads/programs/{programId}.json | 只读潜在客户 |
| 合并潜在客户 | 潜在客户 | POST | /rest/v1/leads/{id}/merge.json | 读写潜在客户 |
| 按潜在客户ID获取列表 | 潜在客户 | GET | /rest/v1/leads/{leadId}.json | 只读资产 |
| 按潜在客户ID获取计划 | 潜在客户 | GET | /rest/v1/leads/{leadId}programMembership.json | 只读资产 |
| 按潜在客户ID获取智能促销活动 | 潜在客户 | GET | /rest/v1/leads/{leadId}/smartCampaignMembership.json | 只读资产 |
| 将潜在客户推送到Marketo | 潜在客户 | POST | /rest/v1/leads/partitions.json | 读写潜在客户 |
| 提交表单 | 潜在客户 | POST | /rest/v1/leads/submitForm.json | 读写潜在客户 |
| 同步潜在客户 | 潜在客户 | POST | /rest/v1/leads.json | 读写潜在客户 |
| 更新潜在客户分区 | 潜在客户 | POST | /rest/v1/leads/partitions.json | 读写潜在客户 |
| 按名称获取潜在客户字段 | 潜在客户 | GET | /rest/v1/leads/schema/fields/{fieldApiName}.json | 读写架构自定义字段 |
| 获取潜在客户字段 | 潜在客户 | GET | /rest/v1/leads/schema/fields.json | 读写架构自定义字段 |
| 创建潜在客户字段 | 潜在客户 | POST | /rest/v1/leads/schema/fields.json | 读写架构自定义字段 |
| 更新潜在客户字段 | 潜在客户 | POST | /rest/v1/leads/schema/fields/{fieldApiName}.json | 读写架构自定义字段 |
| 添加指定帐户列表成员 | 指定帐户列表 | POST | /rest/v1/namedaccountlist/{id}/namedaccounts.json | 读写指定帐户 |
| 删除指定帐户列表 | 指定帐户列表 | POST | /rest/v1/namedaccountlists/delete.json | 读写指定帐户列表 |
| 获取指定帐户列表成员 | 指定帐户列表 | GET | /rest/v1/namedaccountlist/{id}/namedaccounts.json | 只读指定帐户 |
| 获取指定帐户列表 | 指定帐户列表 | GET | /rest/v1/namedaccountlists.json | 只读指定帐户列表 |
| 删除指定帐户列表成员 | 指定帐户列表 | POST | /rest/v1/namedaccountlist/{id}/namedaccounts/remove.json | 读写指定帐户 |
| 同步指定帐户列表 | 指定帐户列表 | POST | /rest/v1/namedaccountlists.json | 读写指定帐户列表 |
| 删除指定帐户 | 指定帐户 | POST | /rest/v1/namedaccounts/delete.json | 读写指定帐户 |
| 描述指定帐户 | 指定帐户 | GET | /rest/v1/namedaccounts/describe.json | 只读指定帐户 |
| 获取指定帐户 | 指定帐户 | GET | /rest/v1/namedaccounts.json | 只读指定帐户 |
| 同步指定帐户 | 指定帐户 | POST | /rest/v1/namedaccounts.json | 读写指定帐户 |
| 按名称获取指定帐户字段 | 指定帐户 | GET | /rest/v1/namedaccounts/schema/fields/{fieldApiName}.json | 读写架构自定义字段 |
| 获取指定帐户字段 | 指定帐户 | GET | /rest/v1/namedaccounts/schema/fields.json | 读写架构自定义字段 |
| 删除机会 | 机会 | POST | /rest/v1/opportunities/delete.json | 读写机会 |
| 删除机会角色 | 机会 | POST | /rest/v1/opportunities/roles/delete.json | 读写机会 |
| 描述机会 | 机会 | GET | /rest/v1/opportunities/describe.json | 只读机会 |
| 描述机会角色 | 机会 | GET | /rest/v1/opportunities/roles/describe.json | 只读机会 |
| 获取机会 | 机会 | GET | /rest/v1/opportunities.json | 只读机会 |
| 获取机会角色 | 机会 | GET | /rest/v1/opportunities/roles.json | 只读机会 |
| 同步机会 | 机会 | POST | /rest/v1/opportunities.json | 读写机会 |
| 同步机会角色 | 机会 | POST | /rest/v1/opportunities/roles.json | 读写机会 |
| 按名称获取机会字段 | 机会 | GET | /rest/v1/opportunities/schema/fields/{fieldApiName}.json | 读写架构自定义字段 |
| 获取机会字段 | 机会 | GET | /rest/v1/opportunities/schema/fields.json | 读写架构自定义字段 |
| 删除项目群成员 | 计划成员 | POST | /rest/v1/programs/{programId}/members/delete.json | 读写潜在客户 |
| 描述计划成员 | 计划成员 | GET | /rest/v1/programs/members/describe.json | 只读潜在客户 |
| 获取计划成员 | 计划成员 | GET | /rest/v1/programs/{programId}/members.json | 只读潜在客户 |
| 同步程序成员数据 | 计划成员 | POST | /rest/v1/programs/{programId}/members.json | 读写潜在客户 |
| 同步程序成员状态 | 计划成员 | POST | /rest/v1/programs/{programId}/members/status.json | 读写潜在客户 |
| 按名称获取计划成员字段 | 计划成员 | GET | /rest/v1/programs/members/schema/fields/{fieldApiName}.json | 读写架构自定义字段 |
| 获取项目群成员字段 | 计划成员 | GET | /rest/v1/programs/members/schema/fields.json | 读写架构自定义字段 |
| 创建项目群成员字段 | 计划成员 | POST | /rest/v1/programs/members/schema/fields.json | 读写架构自定义字段 |
| 更新项目群成员字段 | 计划成员 | POST | /rest/v1/programs/members/schema/fields/{fieldApiName}.json | 读写架构自定义字段 |
| 批准项目 | 程序 | POST | /rest/asset/v1/program/{id}/approve.json | 读写资产 |
| 克隆程序 | 程序 | POST | /rest/asset/v1/program/{id}/clone.json | 读写资产 |
| 创建程序 | 程序 | POST | /rest/asset/v1/programs.json | 读写资产 |
| 删除项目群 | 程序 | POST | /rest/asset/v1/program/{id}/delete.json | 读写资产 |
| 按ID获取计划 | 程序 | GET | /rest/asset/v1/program/{id}.json | 只读资产 |
| 按名称获取计划 | 程序 | GET | /rest/asset/v1/program/byName.json | 只读资产 |
| 获取项目群 | 程序 | GET | /rest/asset/v1/programs.json | 只读资产 |
| 按标记获取程序 | 程序 | GET | /rest/asset/v1/program/byTag.json | 只读资产 |
| 按程序ID获取智能列表 | 程序 | GET | /rest/asset/v1/program/{id}/smartList.json | 只读资产 |
| 取消批准项目 | 程序 | POST | /rest/asset/v1/program/{id}/unapprove.json | 读写资产 |
| 更新项目元数据 | 程序 | POST | /rest/asset/v1/program/{id}.json | 读写资产 |
| 更新项目标记 | 程序 | POST | /rest/asset/v1/program/{id}/tag/{tagType}.json | 读写资产 |
| 删除项目标记 | 程序 | POST | /rest/asset/v1/program/{id}/tag/{tagType}/delete.json | 读写资产 |
| 删除SalesPerson | 销售人员 | POST | /rest/v1/salespersons/delete.json | 读写销售人员 |
| 描述SalesPerson | 销售人员 | GET | /rest/v1/salespersons/describe.json | 只读销售人员 |
| 获取SalesPerson | 销售人员 | GET | /rest/v1/salespersons.json | 只读销售人员 |
| 同步SalesPerson | 销售人员 | POST | /rest/v1/salespersons.json | 读写销售人员 |
| 获取区段 | 区段 | GET | /rest/asset/v1/segmentation.json | 只读资产 |
| 获取分段的区段 | 区段 | GET | /rest/asset/v1/segmentation/{id}/segments.json | 只读资产 |
| 激活Smart Campaign | 智能营销活动 | POST | /rest/asset/v1/smartCampaign/{id}/activate.json | 激活营销活动 |
| 克隆智能营销活动 | 智能营销活动 | POST | /rest/asset/v1/smartCampaign/{id}/clone.json | 读写资产 |
| 创建Smart Campaign | 智能营销活动 | POST | /rest/asset/v1/smartCampaigns.json | 读写资产 |
| 取消激活Smart Campaign | 智能营销活动 | POST | /rest/asset/v1/smartCampaign/{id}/deactivate.json | 停用营销活动 |
| 删除智能营销活动 | 智能营销活动 | POST | /rest/asset/v1/smartCampaign/{id}/delete.json | 读写资产 |
| 获取智能营销活动 | 智能营销活动 | GET | /rest/asset/v1/smartCampaigns.json | 只读资产 |
| 按ID获取Smart Campaign | 智能营销活动 | GET | /rest/asset/v1/smartCampaign/{id}.json | 只读资产 |
| 按名称获取智能营销活动 | 智能营销活动 | GET | /rest/asset/v1/smartCampaign/byName.json | 只读资产 |
| 按智能营销活动ID获取智能列表 | 智能营销活动 | GET | /rest/asset/v1/smartCampaign/{id}/smartList.json | 只读资产 |
| 更新Smart Campaign | 智能营销活动 | POST | /rest/asset/v1/smartCampaign/{id}.json | 读写资产 |
| 克隆智能列表 | 智能列表 | POST | /rest/asset/v1/smartList/{id}/clone.json | 读写资产 |
| 删除智能列表 | 智能列表 | POST | /rest/asset/v1/smartList/{id}/delete.json | 读写资产 |
| 按ID获取智能列表 | 智能列表 | GET | /rest/asset/v1/smartList/{id}.json | 只读资产 |
| 按名称获取智能列表 | 智能列表 | GET | /rest/asset/v1/smartList/byName.json | 只读资产 |
| 获取智能列表 | 智能列表 | GET | /rest/asset/v1/smartLists.json | 只读资产 |
| 批准代码片段草稿 | 片段 | POST | /rest/asset/v1/snippet/{id}/approveDraft.json | 读写资产 |
| 克隆代码片段 | 片段 | POST | /rest/asset/v1/snippet/{id}/clone.json | 读写资产 |
| 创建代码片段 | 片段 | POST | /rest/asset/v1/snippets.json | 读写资产 |
| 删除代码片段 | 片段 | POST | /rest/asset/v1/snippet/{id}/delete.json | 读写资产 |
| 放弃代码片段草稿 | 片段 | POST | /rest/asset/v1/snippet/{id}/discardDraft.json | 读写资产 |
| 获取动态内容 | 片段 | GET | /rest/asset/v1/snippet/{id}/dynamicContent.json | 只读资产 |
| 按ID获取代码片段 | 片段 | GET | /rest/asset/v1/snippet/{id}.json | 只读资产 |
| 获取代码片段内容 | 片段 | GET | /rest/asset/v1/snippet/{id}/content.json | 只读资产 |
| 获取代码片段 | 片段 | GET | /rest/asset/v1/snippets.json | 只读资产 |
| 取消批准代码片段 | 片段 | POST | /rest/asset/v1/snippet/{id}/unapprove.json | 读写资产 |
| 更新代码片段内容 | 片段 | POST | /rest/asset/v1/snippet/{id}/content.json | 读写资产 |
| 更新代码片段动态内容 | 片段 | POST | /rest/asset/v1/snippet/{id}/dynamicContent/{segmentId}.json | 读写资产 |
| 更新代码片段元数据 | 片段 | POST | /rest/asset/v1/snippet/{id}.json | 读写资产 |
| 添加到列表 | 静态列表 | POST | /rest/v1/lists/{listId}/leads.json | 读写潜在客户 |
| 创建静态列表 | 静态列表 | POST | /asset/v1/staticLists.json | 读写资产 |
| 删除静态列表 | 静态列表 | POST | /asset/v1/staticList/{id}/delete.json | 读写资产 |
| 按列表ID获取潜在客户 | 静态列表 | GET | /rest/v1/lists/{listId}/leads.json | 只读潜在客户 |
| 按ID获取列表 | 静态列表 | GET | /rest/v1/lists/{id}.json | 只读潜在客户 |
| 获取列表 | 静态列表 | GET | /rest/v1/lists.json | 只读潜在客户 |
| 按ID获取静态列表 | 静态列表 | GET | /asset/v1/staticList/{id}.json | 只读资产 |
| 按名称获取静态列表 | 静态列表 | GET | /asset/v1/staticList/byName.json | 只读资产 |
| 获取静态列表 | 静态列表 | GET | /asset/v1/staticLists.json | 只读资产 |
| 列表成员 | 静态列表 | GET | /rest/v1/lists/{listId}/leads/ismember.json | 只读潜在客户 |
| 从列表中删除 | 静态列表 | DELETE | /rest/v1/lists/{listId}/leads.json | 读写潜在客户 |
| 更新静态列表元数据 | 静态列表 | POST | /asset/v1/staticList/{id}.json | 读写资产 |
| 按名称获取标记 | 标记 | GET | /rest/asset/v1/tagType/byName.json | 只读资产 |
| 获取标记类型 | 标记 | GET | /rest/asset/v1/tagTypes.json | 只读资产 |
| 创建令牌 | 令牌 | POST | /rest/asset/v1/folder/{id}/tokens.json | 读写资产 |
| 按名称删除令牌 | 令牌 | POST | /rest/asset/v1/folder/{id}/tokens/delete.json | 读写资产 |
| 按文件夹ID获取令牌 | 令牌 | GET | /rest/asset/v1/folder/{id}/tokens.json | 只读资产 |
| 添加角色 | User Management | POST | /userservice/management/v1/users/{userid}/roles/create.json | 访问用户管理Api |
| 删除受邀用户 | User Management | POST | /userservice/management/v1/users/{userId}/invite/delete.json | 访问用户管理Api |
| 删除角色 | User Management | POST | /userservice/management/v1/users/{userid}/roles/delete.json | 访问用户管理Api |
| 删除用户 | User Management | POST | /userservice/management/v1/users/{userId}/delete.json | 访问用户管理Api |
| 按ID获取受邀用户 | User Management | GET | /userservice/management/v1/users/{userid}/invite.json | 访问用户管理Api |
| 获取角色 | User Management | GET | /userservice/management/v1/users/roles.json | 访问用户管理Api |
| 按Id获取角色和工作区 | User Management | GET | /userservice/management/v1/users/{userid}/roles.json | 访问用户管理Api |
| 获取用户 | User Management | GET | /userservice/management/v1/users/allusers.json | 访问用户管理Api |
| 按ID获取用户 | User Management | GET | /userservice/management/v1/users/{userid}/user.json | 访问用户管理Api |
| 获取工作区 | User Management | GET | /userservice/management/v1/users/workspaces.json | 访问用户管理Api |
| 邀请用户 | User Management | POST | /userservice/management/v1/users/invite.json | 访问用户管理Api |
| 更新用户属性 | User Management | POST | /userservice/management/v1/users/{userId}/update.json | 访问用户管理Api |