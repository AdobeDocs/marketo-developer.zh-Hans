---
title: 标准字段
feature: REST API, Field Management
description: 浏览Marketo标准潜在客户字段的完整列表，其中包含REST和SOAP名称、标签和描述，以及如何通过描述潜在客户API检索它们。
exl-id: 147dbdff-4bc9-4ab3-8918-c4de3e1aa97a
source-git-commit: ff0a95e838cecd1d8b1f90ca029a320043824242
workflow-type: tm+mt
source-wordcount: '742'
ht-degree: 16%

---

# 标准字段

以下是可通过API访问的Marketo中可用标准字段列表。

您可以使用REST [Describe Lead](https://developer.adobe.com/marketo-apis/api/mapi)端点检索潜在客户记录中可用的所有受支持字段名称的列表。

| REST API名称 | SOAP API名称 | 友好标签 | 描述 |
| --- | --- | --- | --- |
| 地址 | 地址 | 地址 | 商机地址 |
| 年收入 | 年收入 | 年收入 | 潜在客户公司的年收入 |
| anonymousIP | 匿名IP | 匿名 IP | 商机首次记录的Web访问的IP地址 |
| billingcity | 计费城市 | 帐单寄送城市 | 商机的帐单地址所在的城市 |
| billingCountry | 帐单国家/地区 | 帐单寄送国家 | 商机的帐单地址的国家/地区 |
| billingPostalCode | BillingPostCode | 帐单邮政编码 | 商机的帐单地址的邮政编码 |
| billingState | BillingState | 帐单寄送州 | 商机的帐单地址所在的省/市/自治区 |
| billingStreet | 帐单街道 | 帐单寄送地址 | 潜在客户公司的帐单街道地址 |
| 城市 | 城市 | 城市 | 潜在客户所在城市 |
| 公司 | 公司 | 公司名称 | 商机的公司名称 |
| 国家/地区 | 国家/地区 | 国家/地区 | 潜在客户所在的国家/地区 |
| dateOfBirth | DateofBirth | 出生日期 | 商机的出生日期 |
| 部门 | 部门 | 部门 | 公司中的领导部门 |
| doNotCall | DoNotcall | 请勿来电 | 潜在客户的不致电偏好设置 |
| doNotCallReason | DoNotCallReason | 请勿来电的理由 | 潜在客户的“不致电”偏好设置说明 |
| 电子邮件 | 电子邮件 | 电子邮件地址 | 商机的电子邮件地址。 潜在客户记录的标准Marketo键字段 |
| 传真 | 传真 | 传真号码 | 商机的传真号 |
| firstName | 名字 | 名字 | 商机的名字 |
| industry | 行业 | 行业 | 潜在客户行业 |
| inferredCompany | 推断的公司 | 推断公司 | 通过对商机的首次记录的Web访问进行反向IP查找推断出的公司名称 |
| 推断的国家/地区 | 推断的国家/地区 | 推断的国家 | 通过对商机的首次记录的Web访问进行反向IP查找推断出的国家/地区 |
| 姓氏 | 姓氏 | 姓氏 | 商机的姓氏 |
| leadRole | 潜在客户角色 | 角色 | 潜在客户在其公司中的角色 |
| 商机得分 | 商机得分 | 销售线索分数 | 通过评分营销活动和项目授予商机的整数分数 |
| leadSource | 商机来源 | 销售线索来源 | 记录商机来源的字段 |
| 商机状态 | 潜在客户状态 | 销售线索状态 | 记录商机的当前营销/销售状态的字段 |
| mainPhone | 主电话 | 主要电话 | 潜在客户公司的主要电话号码 |
| jigsawContactId | Marketo拼图联系人ID | MARKETO Data.com ID | 潜在客户的Data.com ID（如果可用） |
| jigsawContactStatus | Marketo拼图联系人状态 | Marketo Data.com状态 | 潜在客户的Data.com状态（如果可用） |
| 中间名 | 中间名 | 中间名称 | 商机的中间名 |
| 移动电话 | 移动电话 | 手机号码 | 商机的手机号码 |
| 员工数 | 员工数 | 员工数 | 潜在客户公司的员工人数 |
| 电话 | 电话 | 电话号码 | 潜在客户的电话号码 |
| 邮政编码 | 邮政编码 | 邮政编码 | 商机的邮政编码 |
| 评级 | 评级 | 销售线索评级 | 商机的营销/销售评级 |
| salutation | 称谓 | 称谓 | Lead首选的称呼，是Mister、Misses...等等 |
| sicCode | SICCode | SIC 代码 | 潜在客户公司的标准行业分类代码 |
| 站点 | 地点 | 地点 |  |
| state | State | State | 商机的状态 |
| 标题 | 标题 | 职务 | 潜在客户的工作职位 |
| 已取消订阅 | 取消订阅 | 取消订阅 | 商机的电子邮件取消订阅状态。 部分系统受管理。 如果设置为true，将阻止接收非操作电子邮件。 |
| unsubscriptedReason | 取消订阅的原因 | 退订原因 | 商机取消订阅状态的原因。 部分系统受管理。 如果潜在客户直接取消订阅了Marketo电子邮件，则使用电子邮件信息填充。 |
| 网站 | 网站 | 网站 | 商机公司的网站URL |
| createdat |  - | 创建时间 | 初始创建潜在客户记录的时间。 受管系统 |
| 更新时间 |  - | 更新时间 | 上次更新商机记录的时间。 受管系统 |
| 电子邮件无效 |  - | 电子邮件无效 | 电子邮件状态无效。 如果设置为true，则将阻止发送到该地址的所有电子邮件。 如果退回则表明电子邮件无效，则会自动将此字段设置为true。 |
| emailInvalidCause |  - | 电子邮件无效原因 | 导致电子邮件状态无效的原因。 当电子邮件无效设置为true时，将在此字段中记录引发退回消息。 |
| 推断的城市 |  - | 推断的城市 | 通过对商机的首次记录的Web访问进行反向IP查找推断出的商机所在城市。 |
| inferredMetropolitanArea |  - | 推断的都市区 | 通过对商机的首次记录的Web访问进行反向IP查找推断出的商机的大都市区。 |
| inferredPhoneAreaCode |  - | 推断的电话区号 | 通过对商机的首次记录的Web访问进行反向IP查找推断出的商机的电话区号。 |
| inferredPostalCode |  - | 推断的邮政编码 | 通过对商机的首次记录的Web访问进行反向IP查找推断出的商机的邮政编码。 |
| inferredStateRegion |  - | 推断的状态区域 | 通过对商机的首次记录的Web访问进行反向IP查找推断出的商机的州区域。 |
| isAnonymous |  - | 匿名 | 潜在客户记录的匿名状态。 系统受管。 |
| 优先级 |  - | 优先级 | 潜在客户的Sales Insight优先级。 系统受管。 |
| 相对分数 |  - | 相对分数 | 潜在客户的Sales Insight相对分数。 系统受管。 |
| 紧急 |  - | 紧迫性 | Lead的Sales Insight紧迫性。 系统受管。 |
