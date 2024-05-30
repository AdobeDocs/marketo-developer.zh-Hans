---
title: "资产"
feature: REST API
description: “用于使用Marketo资源的API。”
source-git-commit: 8c1ffb6db05da49e7377b8345eeb30472ad9b78b
workflow-type: tm+mt
source-wordcount: '876'
ht-degree: 1%

---


# 资源

Marketo提供了可与Marketo中的大多数营销和组织资源交互的API。

## 资源

Marketo资源包括：

- 文件夹
- 程序
- 电子邮件
- 电子邮件模板
- 登陆页面
- 登陆页面模板
- 代码片段
- Forms
- 令牌
- 文件

## API

有关Asset API端点的完整列表，包括参数和建模信息，请参阅 [资源API端点引用](endpoint-reference.md).

## 查询

通常可通过三种模式检索资产：按id、按名称和浏览。  按ID和按名称都将检索给定参数的单个资产，而浏览将返回并允许分页查看该类型的整个资产列表。  单个类型的资产具有各种可用于对其进行过滤的参数，因此请务必查看其各个文档以了解详情。

在某些情况下，某些资产类型的浏览端点将不会返回子资产，例如标记的可允许值，必须使用“按名称”或“按ID”端点单独检索它们，以返回完整的元数据集。  其他则可能有完全不同的端点，用于检索表单字段等依赖对象。

### 按Id

```
GET /rest/asset/v1/folder/{id}.json?type=Folder
```

```json
{
    "success": true,
    "warnings": [],
    "errors": [],
    "requestId": "1241b#14e21ca814a",
    "result": [
        {
            "name": "Social Media",
            "description": null,
            "createdAt": "2011-03-04T17:01:32Z+0000",
            "updatedAt": "2011-03-04T17:01:32Z+0000",
            "url": null,
            "folderId": {
                "id": 341,
                "type": "Folder"
            },
            "folderType": "Email",
            "parent": {
                "id": 11,
                "type": "Folder"
            },
            "path": "/Design Studio/Default/Emails/Social Media",
            "isArchive": false,
            "isSystem": false,
            "accessZoneId": 1,
            "workspace": "Default",
            "id": 341
        }
    ]
}
```

### 按名称

由于技术原因，资产API无法搜索包含逗号(，)的资产名称。  建议命名规则在所有资产类型中排除逗号。

```
GET /rest/asset/v1/file/byName.json?name=My File
```

```json
{
   "success":true,
   "warnings":[ ],
   "errors":[ ],
   "requestId":null,
   "result":[
      {
         "id":148,
         "size":270313,
         "mimeType":"image/jpeg",
         "url":"http://mlm.devlocal.marketo.com/rs/test/assets/piKLbhVFvW",
         "folder":{
            "type":"Email",
            "id":10614
         },
         "name":"My File",
         "description":null,
         "createdAt":"2014-12-09T22:33:57Z+0000",
         "updatedAt":"2014-12-09T22:33:57Z+0000"
      }
   ]
}
```

### 浏览

浏览资产将始终允许使用两个查询参数：

- offset — 返回结果的起始整数偏移。
- maxReturn — 限制返回的记录数。  如果未设置，则默认为20，最大值为200。

```
GET /rest/asset/v1/emailTemplates.json?offset=10&maxReturn=50
```

```json
{
   "success":true,
   "warnings":[ ],
   "errors":[ ],
   "requestId":"33c4#14a1832b4a8",
   "result":[
      {
         "id":18,
         "name":"AAA0unit3CreateTestEmailTemplateName.2314673e-7bc2-47da-a1e8-66dfdd8a1f1d",
         "description":"AssetAPI: getTemplates test",
         "createdAt":"2014-11-03T19:52:58Z+0000",
         "updatedAt":"2014-11-03T19:52:58Z+0000",
         "folder":{
            "type":"Folder",
            "value":15
         },
         "status":"Draft",
         "workspace":"Default"
      },
      {
         "id":177,
         "name":"ABfRGutnwN",
         "description":"HMmHkdTRrGaRpPakdgGKICxfMunCEWDUWiThgAbInfaBXxGxSFfjKQIwerngCHRlGTnAJhKPmwlXLcsjGPtWEiILGyeIJTNVHoHg",
         "createdAt":"2014-11-20T19:31:06Z+0000",
         "updatedAt":"2014-11-20T19:31:06Z+0000",
         "folder":{
            "type":"Folder",
            "value":15
         },
         "status":"Draft",
         "workspace":"Default"
      },
      {
         "id":148,
         "name":"ADVHJBQLyw",
         "description":null,
         "createdAt":"2014-11-20T06:42:57Z+0000",
         "updatedAt":"2014-11-20T06:42:57Z+0000",
         "folder":{
            "type":"Folder",
            "value":15
         },
         "status":"Draft",
         "workspace":"Default"
      }
   ]
}
```

## 创建和更新

对于文件夹、令牌和文件等简单资产类型，通常只有单个端点可供创建，然后是额外的端点，用于按ID更新记录。  创建资产时使用的名称始终是必需的，然后创建或更新响应会返回任何元数据和ID。

例如，下面是如何创建令牌：

```
POST /rest/asset/v1/folder/{id}/tokens.json
```

```
Content-Type: application/x-www-form-urlencoded
```

```
name=April Fools&value=2015-04-01&type=date&folderType=Folder
```

```json
{
    "success": true,
    "warnings": [],
    "errors": [],
    "requestId": "e3c2#14e280db5dc",
    "result": [
        {
            "folder": {
                "type": "Folder",
                "value": 416
            },
            "tokens": [
                {
                    "name": "April Fools",
                    "type": "date",
                    "value": "2015-04-01",
                    "computedUrl": "https://app-abm.marketo.com/#MF1047C3"
                }
            ]
        }
    ]
}
```

要更新文件夹，请执行以下操作：

```
POST /rest/asset/v1/folder/{id}.json
```

```
Content-Type: application/x-www-form-urlencoded
```

```
type=Folder&description=This is a test (update 01)
```

```json
{
    "success": true,
    "warnings": [],
    "errors": [],
    "requestId": "c5b2#14e1f3954bf",
    "result": [
        {
            "name": "Learning - deverly",
            "description": "This is a test (update 01)",
            "createdAt": "2015-03-17T00:17:02Z+0000",
            "updatedAt": "2015-06-23T07:02:07Z+0000",
            "url": "https://app-abm.marketo.com/#MF1044A1",
            "folderId": {
                "id": 407,
                "type": "FOLDER"
            },
            "folderType": "Marketing Folder",
            "parent": {
                "id": 15,
                "type": "FOLDER"
            },
            "path": "/Marketing Activities/Default/Learning - deverly",
            "isArchive": false,
            "isSystem": false,
            "accessZoneId": 1,
            "workspace": "Default",
            "id": 407
        }
    ]
}
```

其他资源的结构更复杂，需要更新其他子部分或子对象，最终必须在使用之前获得批准。  这些资源类型包括Forms、电子邮件、电子邮件模板、登陆页面和登陆页面模板。  每个端点将有一个用于创建记录的端点，然后是其他用于更新元数据、内容和内容部分的端点。

例如，要创建登陆页面，您必须使用模板ID调用其创建端点，然后检索其内容部分，并单独更新每个部分以添加内容，然后再批准它，以便可以实时部署。

### 复杂创建

登陆页面首先需要使用父模板创建登陆页面资产。  这将创建一个新登陆页面，其中包含每个内容部分的模板默认内容。

```
POST rest/asset/v1/landingPages.json
```

```
Content-Type: application/x-www-form-urlencoded
```

```
name=createLandingPage&folder={"type": "Folder", "id": 11}&template=1&description=this is a test&workspace=default&title=test create&keywords=awesome&formPrefill=false
```

```json
{
    "success": true,
    "warnings": [],
    "errors": [],
    "requestId": "7a39#154cf7922c6",
    "result": [
        {
            "id": 27,
            "name": "createLandingPage",
            "description": "this is a test",
            "createdAt": "2016-05-20T18:41:43Z+0000",
            "updatedAt": "2016-05-20T18:41:43Z+0000",
            "folder": {
                "type": "Folder",
                "value": 11,
                "folderName": "Landing Pages"
            },
            "workspace": "Default",
            "status": "draft",
            "template": 1,
            "title": "test create",
            "keywords": "awesome",
            "robots": "index, nofollow",
            "formPrefill": false,
            "mobileEnabled": false,
            "URL": "https://app-devlocal1.marketo.com/lp/622-LME-718/createLandingPage.html",
            "computedUrl": "https://app-devlocal1.marketo.com/#LP27B2"
        }
    ]
}
```

#### 获取分区

要填充登陆页面的内容，您必须检索内容部分的列表，然后为任何偏离模板的部分执行个别更新。

```
GET /rest/asset/v1/landingPage/{id}/content.json
```

```json
{
    "success": true,
    "warnings": [],
    "errors": [],
    "requestId": "6307#154ea1689d7",
    "result": [
        {
            "id": "67",
            "type": "Form",
            "index": 1,
            "content": {
                "content": "189",
                "contentType": "Form",
                "contentUrl": "https://app-devlocal1.marketo.com/#FO189A1ZN13LA1"
            },
            "formattingOptions": {
                "zIndex": 15,
                "left": "359px",
                "top": "122px"
            }
        }
    ]
}
```

#### 更新部分

```
POST /rest/asset/v1/landingPage/{id}/content/{contentId}.json?type=Form&value=1
```

```json
{
    "success": true,
    "warnings": [],
    "errors": [],
    "requestId": "5c37#154ea32cf11",
    "result": [
        {
            "id": 174
        }
    ]
}
```

## 审批

许多资源类型具有关联的草稿和审批系统，包括电子邮件、登陆页面、代码片段、Forms及其相应的模板。  尝试批准资产时，将根据一组特定的验证规则对其进行评估，然后将其设置为已批准状态，或返回失败原因。  对于这些类型的资产，每当对特定资产的内容进行更新时，都会对资产的草稿进行更改，这不会影响批准的版本。  这样可安全地更改内容，而不会影响资产的实时版本。  然后，可以使用审批端点将更改应用于实时版本。  这也会清除资产的草稿状态，直到应用任何其他更新为止。

```
POST /rest/asset/v1/emailTemplate/{id}/approveDraft.json
```

```json
{
   "success":true,
   "warnings":[ ],
   "errors":[ ],
   "requestId":"abe2#14a1832a97d",
   "result":[
      {
         "id":338,
         "name":"lvAVYMZqPS",
         "description":"fZLJQSJRvnYbjGTUpIHHqDOuQgQzXQcWIXoOUPwrVLdMHKcbRqwLoSLkWZTUmaMiCIJSfQiufnnrgITUIqjuAPBLpmliiKuIUFYG",
         "createdAt":"2014-12-05T02:06:21Z+0000",
         "updatedAt":"2014-12-05T02:06:21Z+0000",
         "folder":{
            "type":"Folder",
            "value":15
         },
         "status":"Approved",
         "workspace":"Default"
      }
   ]
}
```

成功的审批会将以前的实时版本替换为更新版本。

也可以通过每个有效资产类型的端点放弃草稿。  在处于已批准草稿状态的资产上使用此项将放弃当前草稿及其具有的任何待处理更改。  在目前没有批准版本的资产上使用此项将不会产生任何效果并返回错误。  只能删除草稿的资源，但不能放弃它们。

```
POST /rest/asset/v1/emailTemplate/{id}/discardDraft.json
```

```json
{
   "success":true,
   "warnings":[ ],
   "errors":[ ],
   "requestId":"17bfa#14a1832b3c4",
   "result":[
      {
         "id":344,
         "name":"LkilkvKrkp",
         "description":"yAyUEXuWMtdhpODUmnCkGjpBcyEKnYucxaSoTyYeQzyNbYanxCXWPOzwiIWmeXPUwjfGAUmgnxlhgOPluVqwNittuvxJmNTaHxYM",
         "createdAt":"2014-12-05T02:06:23Z+0000",
         "updatedAt":"2014-12-05T02:06:23Z+0000",
         "folder":{
            "type":"Folder",
            "value":15
         },
         "status":"Draft",
         "workspace":"Default"
      }
   ]
}
```

如果资产处于仅批准状态，也可以取消批准。  这将删除资产的任何实时版本，并将资产返回到仅草稿状态，同时放弃任何关联的草稿。  只有在Marketo的任何地方均未使用时（例如发送电子邮件流程步骤中引用的电子邮件，或者电子邮件中嵌入的代码片段），才能对大多数资源执行此操作。

```
POST /rest/asset/v1/email/{id}/unapprove.json
```

```json
{
   "success":true,
   "warnings":[ ],
   "errors":[ ],
   "requestId":"3514#14a1832b0fa",
   "result":[
      {
         "id":1364
      }
   ]
}
```

## 删除

具有批准和草稿状态的资产（表单除外）在批准时不得删除，并且在删除之前必须未批准。  通常，仅当资产未批准且不再使用时，以及在文件夹中为空资产时，才能执行删除。  一个值得注意的例外是程序，只要程序及其内容在程序边界之外的任何地方都未使用，这些程序可以与其所有子内容一起删除。

```
POST /rest/asset/v1/program/{id}/delete.json
```

```json
{
    "success": true,
    "warnings": [],
    "errors": [],
    "requestId": "16501#14db042c6b7",
    "result": [
        {
            "id": 1109
        }
    ]
}
```

## 超时

资源API的超时为300秒
