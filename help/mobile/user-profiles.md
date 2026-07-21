---
title: 用户配置文件
feature: Mobile Marketing, Users and Roles
description: 了解如何使用Objective C Swift和Java、标准和自定义字段、associateLead，在iOS和Android的Marketo Mobile SDK中创建和更新用户配置文件
exl-id: 1b2cfb7f-d678-4022-8cd9-a56004a1ac46
TQID: https://experienceleague.adobe.com/famIZ1O17Z7TTe2SBHqWSLLL-pp6Vx9M6xXhy2SbB-0
product_v2: id: b27e5950-9033-45ac-9f86-eb22e567f615
role_v2: id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
source-git-commit: 3e6d310c5aec1a3435424fb122b71d825db5af0e
workflow-type: tm+mt
source-wordcount: 116
ht-degree: 1%

---

# 用户配置文件

使用Marketo Mobile SDK for iOS或Android创建或更新用户配置文件：

1. [在iOS中创建用户配置文件](#ios_user_profiles)
1. [在Android中创建用户配置文件](#android_user_profiles)

## 在iOS中创建用户配置文件 {#ios_user_profiles}

创建用户档案并填充其用户字段。

```objectivec
MarketoLead *profile = [[MarketoLead alloc] init];

// Get user profile from network and populate
[profile setEmail:@"jd@makesomething.com"];
[profile setFirstName:@"John"];
[profile setLastName:@"Doe"];
[profile setAddress:@"1234KingFishSt"];
[profile setCity:@"SouthPadreIsland"];
[profile setState:@"CA"];
[profile setPostalCode:@"78596"];
[profile setCountry:@"USA"];
[profile setGender:@"male"];
[profile setLeadSource:@"_facebook_ads"];
[profile setBirthDay:@"01/01/1985"];
[profile setFacebookId:@"facebookid"];
[profile setFacebookProfileURL:@"facebook.com/profile"];
[profile setFacebookProfilePicURL:@"faceboook.com/profile/pic"];
[profile setLinkedInId:@"linkedinid"];
[profile setTwitterId:@"twitterid"];
```

```swift
let profile =  MarketoLead()

// Get user profile from network and populate
profile.setEmail("jd@makesomething.com")
profile.setFirstName("John")
profile.setLastName("Doe")
profile.setAddress("1234KingFishSt")
profile.setCity("SouthPadreIsland")
profile.setState("CA")
profile.setPostalCode("78596")
profile.setCountry("USA")
profile.setGender("male")
profile.setLeadSource("_facebook_ads")
profile.setBirthDay("01/01/1985")
profile.setFacebookId("facebookid")
profile.setFacebookProfileURL("facebook.com/profile")
profile.setFacebookProfilePicURL("faceboook.com/profile/pic")
profile.setLinkedInId("linkedinid")
profile.setTwitterId("twitterid")
```

添加其他[标准字段](../rest-api/list-of-standard-fields.md)。

>[!BEGINTABS]

>[!TAB 目标C]

```objectivec
// Add other custom fields
[profile setFieldName:@"mobilePhone"withValue:@"123.456.7890"];
[profile setFieldName:@"numberOfEmployees"withValue:@"10"];
[profile setFieldName:@"phone"withValue:@"123.456.7890"];
```

>[!TAB Swift]

```swift
// Add other custom fields
profile.setFieldName("mobilePhone" , withValue :"123.456.7890");
profile.setFieldName("numberOfEmployees", withValue: "10");
profile.setFieldName("phone", withValue:"123.456.7890");
```

>[!ENDTABS]

报告要创建或更新的用户配置文件。

>[!BEGINTABS]

>[!TAB 目标C]

```objectivec
Marketo *sharedInstance = [Marketo sharedInstance];

// This method will update user profile
[sharedInstance associateLead:profile];
```

>[!TAB Swift]

```swift
let marketo = Marketo.sharedInstance()

// This method will update user profile
marketo.associateLead(profile)
```

>[!ENDTABS]

## 在Android中创建用户配置文件 {#android_user_profiles}

1. 创建用户配置文件。

   创建用户档案并填充其用户字段。

   ```java
   MarketoLead profile = new MarketoLead();
   
   // Get user profile from network and populate
   try {
       profile.setEmail("htcone3@gmail.com");
       profile.setFirstName("Mike");
       profile.setLastName("Gray");
       profile.setFacebookId("facebookid");
       profile.setAddress("1234 King Fish Blvd");
   }
   catch (MktoException e) {
       e.printStackTrace();
   }
   ```

1. 添加其他[标准字段](../rest-api/list-of-standard-fields.md)。

   ```java
   // Add other custom fields
   profile.setCustomField("mobilePhone", "123.456.7890");
   profile.setCustomField("numberOfEmployees", "10");
   profile.setCustomField("phone", "123.456.7890");
   profile.setCustomField("rating", "R");
   profile.setCustomField("facebookDisplayName", "mini");
   profile.setCustomField("facebookReach", "10");
   profile.setCustomField("facebookReferredEnrollments", "100");
   profile.setCustomField("facebookReferredVisits", "9998");
   profile.setCustomField("lastReferredEnrollment", "03/01/2015");
   profile.setCustomField("lastReferredVisit", "03/01/2015");
   profile.setCustomField("linkedInDisplayName", "Android");
   ```

1. 报告要创建或更新的用户配置文件。

   ```java
   MarketoLead profile = new MarketoLead();
   
   // This method will update user profile
   marketoSdk.associateLead(profile);
   ```
