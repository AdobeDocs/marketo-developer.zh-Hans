---
title: 电子邮件模板
feature: REST API
description: 了解如何创建和管理Marketo REST API电子邮件模板，包括HTML要求、按id或名称查询以及浏览文件夹
exl-id: 0ecf4da6-eb7e-43c1-8d5c-0517c43b47c8
source-git-commit: 7557b9957c87f63c2646be13842ea450035792be
workflow-type: tm+mt
source-wordcount: '585'
ht-degree: 1%

---

# 电子邮件模板

[电子邮件模板终结点引用](https://developer.adobe.com/marketo-apis/api/asset/#tag/Email-Templates)

电子邮件模板构成了Marketo中每个新电子邮件的基础。  虽然可以通过HTML的替换来取消电子邮件与模板的链接，但最初创建电子邮件时必须使用模板作为基础。  在Marketo中，模板是作为纯HTML文档创建的，其中包含名称和描述等元数据。  对内容的限制很少，但模板的HTML必须有效，并且必须至少包含一个可编辑部分，该部分遵循此处[概述的要求](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/email-marketing/general/functions-in-the-editor/add-editable-sections-to-email-templates-v1-0)。

## 查询

查询电子邮件模板遵循资产的标准模式，允许按ID [查询](https://developer.adobe.com/marketo-apis/api/asset/#tag/Email-Templates/operation/getTemplateByIdUsingGET)、按名称[查询](https://developer.adobe.com/marketo-apis/api/asset/#tag/Email-Templates/operation/getTemplateByNameUsingGET)和[浏览](https://developer.adobe.com/marketo-apis/api/asset/#tag/Email-Templates/operation/getEmailTemplatesUsingGET)给定文件夹。

### 按Id

```
GET /rest/asset/v1/emailTemplate/{id}.json
```

```json
{
    "success": true,
    "warnings": [ ],
    "errors": [ ],
    "requestId": "14f9e#14a12955df1",
    "result": [
        {
            "id": 19,
            "name": "aFgSxuZrBI",
            "description": "fUMhVfIyVkhHzRolYzjGyWouTMfjXCPIAZxHMAEmszAjguVKDtbznEeqbqiDuNBzQoHwBJFdXiMzYiMlGUwtuklUhjGfJlDbhaTL",
            "createdAt": "2014-11-14T02:41:26Z+0000",
            "updatedAt": "2014-11-14T02:41:26Z+0000",
            "folder": {
                "type": "Folder",
                "value": 15
            },
            "status": "Draft",
            "workspace": "Default"
        }
    ]
}
```

#### 按名称

```
GET /rest/asset/v1/emailTemplate/byName.json?name=Test Template
```

```json
{
    "success": true,
    "warnings": [ ],
    "errors": [ ],
    "requestId": "14f9e#14a12955df1",
    "result": [
        {
            "id": 19,
            "name": "aFgSxuZrBI",
            "description": "fUMhVfIyVkhHzRolYzjGyWouTMfjXCPIAZxHMAEmszAjguVKDtbznEeqbqiDuNBzQoHwBJFdXiMzYiMlGUwtuklUhjGfJlDbhaTL",
            "createdAt": "2014-11-14T02:41:26Z+0000",
            "updatedAt": "2014-11-14T02:41:26Z+0000",
            "folder": {
                "type": "Folder",
                "value": 15
            },
            "status": "Draft",
            "workspace": "Default"
        }
    ]
}
```

#### 浏览

```
GET /rest/asset/v1/emailTemplates.json
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
      },
      {
         "id":201,
         "name":"AIpwuwiaqb",
         "description":null,
         "createdAt":"2014-11-25T20:49:06Z+0000",
         "updatedAt":"2014-11-25T20:49:06Z+0000",
         "folder":{
            "type":"Folder",
            "value":15
         },
         "status":"Draft",
         "workspace":"Default"
      },
      {
         "id":240,
         "name":"aqZGoAskEF",
         "description":"uOMEhLpXOEWkwdZxkpcdDjTjKfokxuHEYHPVIVsADFIUEUobzIEaDiqFFxezwfovGfwjuPTJRxUmuHmGpyIklJdDdVosPJdyOVom",
         "createdAt":"2014-11-26T21:11:56Z+0000",
         "updatedAt":"2014-11-26T21:11:56Z+0000",
         "folder":{
            "type":"Folder",
            "value":15
         },
         "status":"Draft",
         "workspace":"Default"
      },
      {
         "id":199,
         "name":"BAxnkVfLGi",
         "description":"TzUMQKzKXdgukNCCcaiJHUWASceqlZswhCqDQFDFZULqzYkEiyKcwtQRzKERynReqtMHOhqjnhExCsZopyfzglmXAOjEJdxNURCX",
         "createdAt":"2014-11-25T20:49:06Z+0000",
         "updatedAt":"2014-11-25T20:49:06Z+0000",
         "folder":{
            "type":"Folder",
            "value":15
         },
         "status":"Draft",
         "workspace":"Default"
      },
      {
         "id":278,
         "name":"bcBNCUIHrL",
         "description":"UJEPYBRGTSYosZRnMnahMyVtdyxjRpzJMSXyncATKwcLlDAqDnSCFezGVsDZFpZwPzQvBlvaOZzOzBIsIAtqIerZhJFfpqMogoiB",
         "createdAt":"2014-11-30T11:30:07Z+0000",
         "updatedAt":"2014-11-30T11:30:07Z+0000",
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

查询记录本身将只返回有关记录的元数据。 要获取内容，请参阅#content一节。

## 创建和更新

[创建](https://developer.adobe.com/marketo-apis/api/asset/#tag/Email-Templates/operation/createEmailTemplateUsingPOST)或[更新](https://developer.adobe.com/marketo-apis/api/asset/#tag/Email-Templates/operation/updateEmailTemplateContentUsingPOST)模板相当简单。 每个模板的内容存储为HTML文档，必须使用POST的多部分/表单数据类型传递到Marketo。 必须传递相应的Content-Type标头，该标头包括[multipart](https://www.w3.org/Protocols/rfc1341/7_2_Multipart.html)和[multipart/form-data](https://www.ietf.org/rfc/rfc2388.txt)的RFC中所述的边界。

创建模板需要您包含三个参数：名称、文件夹和内容。 可包括一个可选的描述参数。  HTML文档在content参数中传递，该参数还必须包含常规文件名参数作为其Content-Disposition标头的一部分。

```
POST /rest/asset/v1/emailTemplates.json
```

```
Content-Type: multipart/form-data; boundary=mktoBoundary1480963323998
```

```html
--mktoBoundary1480963323998
Content-Disposition: form-data; name="name"

Sample Email Template
--mktoBoundary1480963323998
Content-Disposition: form-data; name="folder"

{"id":15,"type":"Folder"}
--mktoBoundary1480963323998
Content-Disposition: form-data; name="content"; filename="testHTML.html"
Content-Type: text/html

<html>
<body>
<h1>TEST HTML</h1>
</body>
</html>

--mktoBoundary1480963323998
Content-Disposition: form-data; name="description"

Create email template using API
--mktoBoundary1480963323998--
```

```json
{
    "success": true,
    "warnings": [ ],
    "errors": [ ],
    "requestId": "a99f#14e22b2b85e",
    "result": [
        {
            "id": 1022,
            "name": "Sample Email Template",
            "description": "Create email template using API",
            "createdAt": "2015-06-23T23:13:34Z+0000",
            "updatedAt": "2015-06-23T23:13:34Z+0000",
            "url": "https://app-abm.marketo.com/#ET1022B2ZN12",
            "folder": {
                "type": "Folder",
                "value": 15,
                "folderName": "Templates"
            },
            "status": "draft",
            "workspace": "Default",
            "version": 1
        }
    ]
}
```

使用需要电子邮件模板的ID的[单独终结点](https://developer.adobe.com/marketo-apis/api/asset/#tag/Email-Templates/operation/updateEmailTemplateContentUsingPOST)更新内容。 此端点仅允许提交正文中的内容参数。 进行更新时，如果更新了已批准的版本，则内容参数中传递的任何内容都将完全替换新草稿中的电子邮件现有内容；如果资产处于仅草稿状态，则替换当前草稿。

```
POST /rest/asset/v1/emailTemplate/{id}/content.json
```

```
Content-Type: multipart/form-data; boundary=mktoBoundaryEiJFFFPFKK2WovsT
```

```html
--mktoBoundaryEiJFFFPFKK2WovsT
Content-Disposition: form-data; name="content"; filename="testHTML2.html"
Content-Type: text/html

<html>
<body>
<h1>TEST HTML WITH UPDATE</h1>
<div class="mktEditable"></div>
</body>
</html>
--mktoBoundaryEiJFFFPFKK2WovsT--
```

```json
{
   "success": true,
   "warnings": [ ],
   "errors": [ ],
   "requestId": "f8e2#158d0ae24f8",
   "result":[
      {
         "id":1022,
         "status":"Draft",
         "content":"<html>\n<body>\n<h1>TEST HTML WITH UPDATE</h1>\n<div class="mktEditable"></div>\n</body>\n</html>"
      }
   ]
}
```

## 更新元数据

要[更新模板的元数据](https://developer.adobe.com/marketo-apis/api/asset/#tag/Email-Templates/operation/updateEmailTemplateUsingPOST)、名称和描述，您可以使用与相同的端点来更新内容，但可以传递application/x-www-url-formencoded POST，并使用名称和描述参数。

```
POST /rest/asset/v1/emailTemplate/{id}.json
```

```
Content-Type: application/x-www-form-urlencoded
```

```
description=Updated description&name=New Name
```

```json
{
    "success": true,
    "warnings": [ ],
    "errors": [ ],
    "requestId": "17ca5#14a12ab900a",
    "result": [
        {
            "id": 19,
            "name": "New Name",
            "description": "Updated description",
            "createdAt": "2014-11-14T02:41:26Z+0000",
            "updatedAt": "2014-11-14T02:41:26Z+0000",
            "folder": {
                "type": "Folder",
                "value": 15
            },
            "status": "Draft",
            "workspace": "Default"
        }
    ]
}
```

## 审批

电子邮件模板遵循用于审批资产记录的标准模式。 您可以批准草稿、取消批准已批准的版本以及通过每个终端节点放弃电子邮件模板的现有草稿。

### 批准

在调用审批端点时，将根据Marketo电子邮件规则验证电子邮件。 在批准电子邮件之前，必须填充发件人姓名、发件人电子邮件、回复电子邮件和主题。

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

### 取消批准

取消批准端点只能在批准的模板上使用。

```
POST /rest/asset/v1/emailTemplate/{id}/unapprove.json
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

### 放弃

模板的草稿版本在更新已批准的电子邮件后创建。

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

### 删除

```
POST /rest/asset/v1/emailTemplate/{id}/delete.json
```

```json
{
   "success":true,
   "warnings":[ ],
   "errors":[ ],
   "requestId":"15cef#149d3de83db",
   "result":[
      {
         "id":12
      }
   ]
}
```

## 克隆

Marketo为[克隆电子邮件模板](https://developer.adobe.com/marketo-apis/api/asset/#tag/Email-Templates/operation/cloneTemplateUsingPOST)提供了一种简单的方法。 与创建不同，此类请求是使用application/x-www-url-formencoded POST创建的，并使用名称和文件夹这两个必需参数，即具有ID和类型的嵌入式JSON对象。  Description也是一个可选参数。

```
POST /rest/asset/v1/emailTemplate/{id}/clone.json
```

```
Content-Type: application/x-www-form-urlencoded
```

```
name=Sample Template 01 - deverly&folder={"id":12,"type":"Folder"}&description=This is a sample template
```

```json
{
    "success": true,
    "warnings": [],
    "errors": [],
    "requestId": "905e#14e22c693f8",
    "result": [
        {
            "id": 1024,
            "name": "Sample Template 01 - deverly",
            "description": "This is a sample template",
            "createdAt": "2015-06-23T23:35:16Z+0000",
            "updatedAt": "2015-06-23T23:35:16Z+0000",
            "url": "https://app-abm.marketo.com/#ET1024B2ZN12",
            "folder": {
                "type": "Folder",
                "value": 12,
                "folderName": "Templates"
            },
            "status": "draft",
            "workspace": "Default"
        }
    ]
}
```

## 查询电子邮件依赖关系

使用[获取](https://developer.adobe.com/marketo-apis/api/asset/#tag/Email-Templates/operation/getEmailTemplateUsedByUsingGET)端点使用的电子邮件模板检索依赖于给定电子邮件模板的电子邮件列表。  `id`路径参数指定父电子邮件模板。

有2个可选参数。 `maxReturn`  是一个限制结果数的整数（默认值为20，最大值为200），`offset`是一个可与`maxReturn`一起用于读取大型结果集的整数（默认值为0）。

```
GET /rest/asset/v1/emailTemplates/{id}/usedBy.json
```

```json
{
    "success": true,
    "errors": [],
    "requestId": "18b0#16fa3344169",
    "warnings": [],
    "result": [
        {
            "id": 1022,
            "name": "EmailPr.Email2",
            "type": "Email",
            "status": "approved",
            "updatedAt": "2019-12-02T00:42:21Z+0000"
        },
        {
            "id": 1023,
            "name": "Default.Email1.email1",
            "type": "Email",
            "status": "approved",
            "updatedAt": "2019-12-02T00:42:21Z+0000"
        },
        {
            "id": 1025,
            "name": "Defa.E1",
            "type": "Email",
            "status": "draft",
            "updatedAt": "2019-12-02T00:42:21Z+0000"
        },
        {
            "id": 1052,
            "name": "Email v1 Program.Email v1 Email",
            "type": "Email",
            "status": "draft",
            "updatedAt": "2019-06-07T20:07:16Z+0000"
        }
    ]
}
```
