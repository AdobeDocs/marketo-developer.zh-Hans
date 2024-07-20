---
title: 标准字段
feature: REST API, Field Management
description: 标准Marketo字段的表。
exl-id: 147dbdff-4bc9-4ab3-8918-c4de3e1aa97a
source-git-commit: 66add4c38d0230c36d57009de985649bb67fde3e
workflow-type: tm+mt
source-wordcount: '1140'
ht-degree: 11%

---

# 标准字段

以下是可通过API访问的Marketo中可用标准字段列表。

您可以使用REST [Describe Lead](https://developer.adobe.com/marketo-apis/api/mapi/)端点检索潜在客户记录中可用的所有受支持字段名称的列表。

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
| 国家/地区 | 国家 | 国家 | 潜在客户所在的国家/地区 |
| dateOfBirth | DateofBirth | 出生日期 | 商机的出生日期 |
| 部门 | 部门 | 部门 | 公司中的领导部门 |
| doNotCall | DoNotcall | 请勿来电 | 潜在客户的不致电偏好设置 |
| doNotCallReason | DoNotCallReason | 请勿来电的理由 | 潜在客户的“不致电”偏好设置说明 |
| 电子邮件 | 电子邮件 | 电子邮件地址 | 商机的电子邮件地址。 潜在客户记录的标准Marketo键字段 |
| 传真 | 传真 | 传真号码 | 商机的传真号 |
| 名字 | 名字 | 名字 | 商机的名字 |
| industry | 行业 | 行业 | 潜在客户行业 |
| inferredCompany | 推断的公司 | 推断公司 | 通过对商机的首次记录的Web访问进行反向IP查找推断出的公司名称 |
| 推断的国家/地区 | 推断的国家/地区 | 推断的国家 | 通过对商机的首次记录的Web访问进行反向IP查找推断出的国家/地区 |
| 姓氏 | 姓氏 | 姓氏 | 商机的姓氏 |
| leadRole | 潜在客户角色 | 角色 | 潜在客户在其公司中的角色 |
| 商机得分 | 商机得分 | 潜在客户分数 | 通过评分营销活动和项目授予商机的整数分数 |
| leadSource | 商机来源 | 潜在客户来源 | 记录商机来源的字段 |
| 商机状态 | 潜在客户状态 | 潜在客户状态 | 记录商机的当前营销/销售状态的字段 |
| mainPhone | 主电话 | 主要电话 | 潜在客户公司的主要电话号码 |
| jigsawContactId | Marketo拼图联系人ID | MARKETO Data.com ID | 潜在客户的Data.com ID（如果可用） |
| jigsawContactStatus | Marketo拼图联系人状态 | Marketo Data.com状态 | 潜在客户的Data.com状态（如果可用） |
| facebookDisplayName | MarketoSocialFacebookDisplayName | Marketo Social Facebook显示名称 | 潜在客户的Facebook显示名称。 系统已在社交登录期间填充 |
| facebookId | MarketoSocialFacebookId | Marketo Social Facebook Id | 潜在客户的Facebook ID。 系统已在社交登录期间填充 |
| facebookPhotoURL | MarketoSocialFacebookPhotoURL | Marketo Social Facebook照片URL | 商机的Facebook配置文件照片的URL。 系统已在社交登录期间填充 |
| facebookProfileURL | MarketoSocialFacebookProfileURL | Marketo Social Facebook配置文件URL | 商机的Facebook配置文件的URL。 系统已在社交登录期间填充 |
| facebookReach | MarketoSocialFacebookReach | Marketo Social Facebook范围 | 领先者的Facebook触角。 系统已在社交登录期间填充 |
| facebookReferredEnrollments | MarketoSocialFacebookReferredEnrollments | Marketo Social Facebook引用的注册 | 通过Facebook归因于商机的被引用注册数。 受管系统 |
| facebookReferredVisits | MarketoSocialFacebookReferredVisits | Marketo Social Facebook反向访问 | 通过Facebook归因于商机的反向访问次数。 受管系统 |
| 性别 | MarketoSocialGender | Marketo社会性别 | 商机的性别。 系统已在社交登录期间填充 |
| lastReferredEnrollment | MarketoSocialLastReferredEnrollment | Marketo Social上次引用的注册 | 上次完成转介的日期。 受管系统 |
| lastReferredVisit | MarketoSocialLastReferredVisit | Marketo Social上次反向访问 | 上次引用访问的日期。 受管系统 |
| linkedInDisplayName | MarketoSocialLinkedInDisplayName | Marketo Social LinkedIn显示名称 | 潜在客户的LinkedIn显示名称。 系统已在社交登录期间填充 |
| linkedInId | MarketoSocialLinkedInId | Marketo Social LinkedIn Id | 潜在客户的LinkedIn ID。 系统已在社交登录期间填充 |
| linkedInPhotoURL | MarketoSocialLinkedInPhotoURL | Marketo Social LinkedIn照片URL | 潜在客户的LinkedIn照片URL。 系统已在社交登录期间填充 |
| linkedinprofileURL | MarketoSocialLinkedInProfileURL | Marketo Social LinkedIn配置文件URL | 潜在客户的LinkedIn配置文件。 系统已在社交登录期间填充 |
| linkedInReach | MarketoSocialLinkedInReach | Marketo Social LinkedIn范围 | Lead的LinkedIn范围。 系统已在社交登录期间填充 |
| linkedInReferredEnrollments | MarketoSocialLinkedInReferredEnrollments | Marketo Social LinkedIn引用的注册 | 通过LinkedIn归因于商机的被引用注册数。 受管系统 |
| linkedInReferredVisits | MarketoSocialLinkedInReferredVisits | Marketo Social LinkedIn反向访问 | 通过LinkedIn归因于商机的反向访问次数。 受管系统 |
| syndicationId |  - | Marketo社交联合ID | 商机的内部Marketo社交ID。 受管系统 |
| totalReferredEnrollments | MarketoSocialTotalReferredEnrollments | Marketo Social 反向注册次数总计 | 归属于潜在客户的已完成转介注册总数 |
| totalReferredVisits | MarketoSocialTotalReferredVisits | Marketo Social 反向访问次数总计 | 归因于商机的反向访问总数 |
| twitterDisplayName | MarketoSocialTwitterDisplayName | Marketo社交Twitter显示名称 | 商机的Twitter显示名称。 系统已在社交登录期间填充 |
| twitterId | MarketoSocialTwitterId | Marketo社交TwitterID | 商机的TwitterID。 系统已在社交登录期间填充 |
| twitterPhotoURL | MarketoSocialTwitterPhotoURL | Marketo社交Twitter照片URL | 商机的Twitter照片URL。 系统已在社交登录期间填充 |
| twitterProfileURL | MarketoSocialTwitterProfileURL | Marketo社交Twitter配置文件URL | 商机的Twitter配置文件URL。 系统已在社交登录期间填充 |
| twitterReach | MarketoSocialTwitterReach | Marketo社交Twitter范围 | 潜在客户的Twitter范围。 系统已在社交登录期间填充 |
| twitterReferredEnrollments | MarketoSocialTwitterReferredEnrollments | Marketo社交Twitter引用的注册 | 通过Twitter归因于商机的被引用注册数。 受管系统 |
| twitterReferredVisits | MarketoSocialTwitterReferredVisits | Marketo社交Twitter反向访问 | 通过Twitter归因于商机的反向访问次数。 受管系统 |
| 中间名 | 中间名 | 中间名称 | 商机的中间名 |
| 移动电话 | 移动电话 | 手机号码 | 商机的手机号码 |
| 员工数 | 员工数 | 员工数 | 潜在客户公司的员工人数 |
| 电话 | 电话 | 电话号码 | 潜在客户的电话号码 |
| 邮政编码 | 邮政编码 | 邮政编码 | 商机的邮政编码 |
| 评级 | 评级 | 潜在客户评级 | 商机的营销/销售评级 |
| salutation | 称谓 | 称谓 | 潜在顾客首选的称呼，即先生、小姐……等。 |
| sicCode | SICCode | SIC 代码 | 潜在客户公司的标准行业分类代码 |
| 站点 | 地点 | 地点 |  |
| state | 州 | 州 | 商机的状态 |
| 标题 | 标题 | 职务 | 潜在客户的工作职位 |
| 已取消订阅 | 退订 | 退订 | 商机的电子邮件取消订阅状态。 部分系统受管理。 如果设置为true，将阻止接收非操作电子邮件。 |
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
| 相对分数 |  - | 相对分数 | 商机的销售分析相对分数。 系统受管。 |
| 紧急 |  - | 紧急 | Lead的Sales Insight紧迫性。 系统受管。 |
