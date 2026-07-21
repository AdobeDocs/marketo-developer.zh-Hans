---
title: 电子邮件
feature: REST API
description: 了解如何使用Marketo Asset REST API按ID、名称或文件夹浏览来查询和管理电子邮件资源，并包含有关预测内容和A/B测试限制的注释。
exl-id: 6875730d-c74a-42cf-a3d2-dad7a3ac535d
TQID: https://experienceleague.adobe.com/t2FyPbwS836MvOe5rL0rVS7ibtzzZMmXwmgHBDZEr8Q
product_v2:
  - id: b27e5950-9033-45ac-9f86-eb22e567f615
feature_v2:
  - id: c5f60233-d5ea-4453-a799-0ad258b4d399
  - id: e64968b2-4ee5-47f9-8cae-0588f184b9eb
  - id: f82558ea-6af5-44eb-a424-5b3389abb0a3
role_v2:
  - id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
topic_v2:
  - id: a004cc84-67b9-4a33-a3a7-8ec7273ef4dc
source-git-commit: 3e6d310c5aec1a3435424fb122b71d825db5af0e
workflow-type: tm+mt
source-wordcount: 1813
ht-degree: 1%

---

# 电子邮件

[电子邮件端点引用](https://developer.adobe.com/marketo-apis/api/asset#tag/Emails)

使用电子邮件REST端点查询和管理电子邮件资产。

如果电子邮件包含[Marketo预测内容](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/predictive-content/working-with-predictive-content/understanding-predictive-content)，则以下端点将失败，并出现错误代码709和相应的错误消息：

- [获取电子邮件内容](https://developer.adobe.com/marketo-apis/api/asset#tag/Emails/operation/getEmailContentByIdUsingGET)
- [更新电子邮件内容部分](https://developer.adobe.com/marketo-apis/api/asset#tag/Emails/operation/updateEmailComponentContentUsingPOST)
- [批准电子邮件草稿](https://developer.adobe.com/marketo-apis/api/asset#tag/Emails/operation/approveDraftUsingPOST)

## 查询

电子邮件支持与模板相同的查询模式：[按ID](https://developer.adobe.com/marketo-apis/api/asset#tag/Emails/operation/getEmailByIdUsingGET)、[按名称](https://developer.adobe.com/marketo-apis/api/asset#tag/Emails/operation/getEmailByNameUsingGET)和[浏览](https://developer.adobe.com/marketo-apis/api/asset#tag/Emails/operation/getEmailUsingGET)。 by-name和浏览端点还支持文件夹过滤。

如果电子邮件属于使用[A/B测试](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/email-marketing/email-programs/email-program-actions/email-test-a-b-test/add-an-a-b-test)的电子邮件程序，则以下端点不会返回该电子邮件：

- [按ID获取电子邮件](https://developer.adobe.com/marketo-apis/api/asset#tag/Emails/operation/getEmailByIdUsingGET)
- [按名称获取电子邮件](https://developer.adobe.com/marketo-apis/api/asset#tag/Emails/operation/getEmailByNameUsingGET)
- [获取电子邮件](https://developer.adobe.com/marketo-apis/api/asset#tag/Emails/operation/getEmailUsingGET)

该调用指示成功，但包含警告`No assets found for the given search criteria.`

### 按ID

```http
GET /rest/asset/v1/email/1351.json
```

```json
{
   "success":true,
   "warnings":[ ],
   "errors":[ ],
   "requestId":"9ad0#14a1832af8c",
   "result":[
      {
         "id":1356,
         "name":"sakZxhxkwV",
         "description":"sample description",
         "createdAt":"2014-12-05T02:06:21Z+0000",
         "updatedAt":"2014-12-05T02:06:21Z+0000",
         "subject":{
            "type":"Text",
            "value":"sample subject"
         },
         "fromName":{
            "type":"Text",
            "value":"RBxEtmdQZz"
         },
         "fromEmail":null,
         "replyEmail":{
            "type":"Text",
            "value":"Qlikf@testmail.com"
         },
         "folder":{
            "type":"folder",
            "value":10421
         },
         "operational":false,
         "textOnly":false,
         "publishToMSI":false,
         "webView":false,
         "status":false,
         "template":338,
         "workspace":"Default",
         "isOpenTrackingDisabled": false,
         "version": 2,
         "autoCopyToText": true,
         "ccFields": [
            {
              "attributeId": "157",
              "objectName": "lead",
              "displayName": "Lead Owner Email Address",
              "apiName": null
            }
          ],
         "preHeader": "My awesome preheader!"
      }
   ]
}
```

### 按名称

按名称查询时，可以选择传递一个文件夹以将搜索限制在该文件夹中。

```http
GET /rest/asset/v1/email/byName.json?name=My Email&folder={"id":1056,"type"="Folder"}
```

```json
{
   "success":true,
   "warnings":[
   ],
   "errors":[
   ],
   "requestId":"3a7f#14c484de875",
   "result":[
      {
         "id":1032,
         "name":"My Email",
         "description":"eCjxjIHmYPLtecoSphkvIXlrygOBDLhgyQKnsKMpiKWgSCKhkPMUFvFPUvEylmFiLjQGnffXGaiNLxAwiFOmIDvxEINoaSYascJw",
         "createdAt":"2015-03-23T20:23:25Z+0000",
         "updatedAt":"2015-03-23T20:23:25Z+0000",
         "subject":{
            "type":"Text",
            "value":"ezyKBmDcyCcUIrXASrLSvRuWQgWpRZxQstJoStgMSLEBASGKMpAnVeWrgJsaVFoFJUEXhEIPpDAWpzajzingUruFpiMcRRwtoBzU"
         },
         "fromName":{
            "type":"Text",
            "value":"dAiqRNJOdY"
         },
         "fromEmail":{
            "type":"Text",
            "value":"ilZxG@testmail.com"
         },
         "replyEmail":{
            "type":"Text",
            "value":"VYsCS@testmail.com"
         },
         "folder":{
            "type":"folder",
            "value":1056
         },
         "operational":false,
         "textOnly":false,
         "publishToMSI":false,
         "webView":false,
         "status":"draft",
         "template":32,
         "workspace":"Default",
         "isOpenTrackingDisabled": false,
        "version": 2,
         "autoCopyToText": true,
         "ccFields": [
            {
              "attributeId": "157",
              "objectName": "lead",
              "displayName": "Lead Owner Email Address",
              "apiName": null
            }
          ],
         "preHeader": "My awesome preheader!"
      }
   ]
}
```

### 浏览

电子邮件浏览遵循标准资产API模式，并支持这些可选过滤器：

- `status`： `Approved`或`Draft`。
- `folder`：包含`id`和`type`的JSON对象。
- `earliestUpdatedAt`和`latestUpdatedAt`：更新时间范围。
- `maxReturn`：要返回的结果数。 默认值为20，最大值为200。
- `offset`：与`maxReturn`配合使用以分页大型结果集。 默认值为0。

```http
GET /rest/asset/v1/emails.json?maxReturn=3&folder={"id":341,"type":"Folder"}
```

```json
{
    "success": true,
    "warnings": [],
    "errors": [],
    "requestId": "17576#14e22eb29cb",
    "result": [
        {
            "id": 2137,
            "name": "Social Sharing in Email",
            "description": "",
            "createdAt": "2011-03-04T17:12:42Z+0000",
            "updatedAt": "2011-03-04T19:04:36Z+0000",
            "url": null,
            "subject": {
                "type": "Text",
                "value": "Republish this content to your favorite social site!"
            },
            "fromName": {
                "type": "Text",
                "value": "Demo Master Marketo"
            },
            "fromEmail": {
                "type": "Text",
                "value": "demomaster@marketo.com"
            },
            "replyEmail": {
                "type": "Text",
                "value": "demomaster@marketo.com"
            },
            "folder": {
                "type": "Folder",
                "value": 341,
                "folderName": "Social Media"
            },
            "operational": false,
            "textOnly": false,
            "publishToMSI": false,
            "webView": true,
            "status": "approved",
            "template": null,
            "workspace": "Default",
            "isOpenTrackingDisabled": false,
            "version": 2,
            "autoCopyToText": true,
            "ccFields": [
               {
                 "attributeId": "157",
                 "objectName": "lead",
                 "displayName": "Lead Owner Email Address",
                 "apiName": null
               }
             ],
            "preHeader": "My awesome preheader!"
        }
    ]
}
```

## 查询内容

要[检索电子邮件的可编辑部分](https://developer.adobe.com/marketo-apis/api/asset#tag/Emails/operation/getEmailContentByIdUsingGET)，请查询其内容。 （可选）按状态筛选，以从“已批准”或“草稿”版本返回部分。

```http
GET /rest/asset/v1/email/1356/content.json
```

```json
{
   "success":true,
   "warnings":[ ],
   "errors":[ ],
   "requestId":"8a44#14c484de8c8",
   "result":[
      {
         "htmlId":"edit_text_3",
         "value":[
            {
               "type":"HTML",
               "value":"Content from testCreateEmailTemplate2"
            },
            {
               "type":"Text",
               "value":"Content from testCreateEmailTemplate2"
            }
         ],
         "contentType":"Text"
      }
   ]
}
```

分区可以具有`dynamicContent`类型。 有关详细信息，请参阅[动态内容](dynamic-content.md)。

## 查询抄送字段

调用[获取电子邮件抄送字段](https://developer.adobe.com/marketo-apis/api/asset#tag/Emails/operation/getEmailCCFieldsUsingGET)端点以检索目标实例中为电子邮件抄送启用的字段。

```http
GET /rest/asset/v1/email/ccFields.json
```

```json
{
   "success":true,
   "errors":[ ],
   "requestId":"e54b#16796fdbd4e",
   "warnings":[ ],
   "result":[
      {
         "attributeId":"157",
         "objectName":"lead",
         "displayName":"Lead Owner Email Address",
         "apiName":"leadOwnerEmailAddress"
      },
      {
         "attributeId":"396",
         "objectName":"company",
         "displayName":"Account Owner Email Address",
         "apiName":"accountOwnderEmailAddress"
      }
   ]
}
```

## 创建和更新

[从源模板创建电子邮件](https://developer.adobe.com/marketo-apis/api/asset#tag/Emails/operation/createEmailUsingPOST)。 电子邮件的可编辑部分来自模板的HTML元素，这些元素具有`mktEditable`类以及唯一的`id`属性。

创建电子邮件调用需要以下参数：

- `name`：电子邮件名称。
- `template`：源模板。
- `folder`：父文件夹。

可选的创建参数为`subject`、`fromName`、`fromEmail`、`replyEmail`、`operational`和`isOpenTrackingDisabled`。 忽略这些参数时，将应用以下默认值：

- `subject`为空。
- `fromName`、`fromEmail`和`replyEmail`使用实例默认值。
- `operational`和`isOpenTrackingDisabled`是`false`。

`isOpenTrackingDisabled`参数确定发送的电子邮件是否包含开放跟踪像素。

```http
POST /rest/asset/v1/emails.json
```

```text
Content-Type: application/x-www-form-urlencoded
```

```text
name=My New Email 02 - deverly&folder={"id":1017,"type":"Program"}&template=24&description=This is a test email&subject=Hey There&fromName=SomeBody&fromEmail=somebody@marketo.com&replyEmail=somebody@marketo.com
```

```json
{
    "success": true,
    "warnings": [],
    "errors": [],
    "requestId": "f557#14e22db88d9",
    "result": [
        {
            "id": 2212,
            "name": "My New Email 02 - deverly",
            "description": "This is a test email",
            "createdAt": "2015-06-23T23:58:09Z+0000",
            "updatedAt": "2015-06-23T23:58:09Z+0000",
            "url": "https://app-abm.marketo.com/#EM2212A1LA1",
            "subject": {
                "type": "Text",
                "value": "Hey There"
            },
            "fromName": {
                "type": "Text",
                "value": "SomeBody"
            },
            "fromEmail": {
                "type": "Text",
                "value": "somebody@marketo.com"
            },
            "replyEmail": {
                "type": "Text",
                "value": "somebody@marketo.com"
            },
            "folder": {
                "type": "Program",
                "value": 1017,
                "folderName": "Landing Page - promotion"
            },
            "operational": false,
            "textOnly": false,
            "publishToMSI": false,
            "webView": false,
            "status": "draft",
            "template": 24,
            "workspace": "Default",
            "isOpenTrackingDisabled": false,
            "version": 2,
            "autoCopyToText": false,
            "ccFields": null,
            "preHeader": null
        }
    ]
}
```

要[更新电子邮件](https://developer.adobe.com/marketo-apis/api/asset#tag/Emails/operation/updateEmailContentUsingPOST)，请传递其ID并更新电子邮件的描述或名称。

```http
POST /rest/asset/v1/email/{id}.json
```

```text
Content-Type: application/x-www-form-urlencoded
```

```text
description=This is an Email&name=Updated Email
```

```json
{
    "success": true,
    "warnings": [],
    "errors": [],
    "requestId": "f557#14e22db88d9",
    "result": [
        {
            "id": 2212,
            "name": "Updated Email",
            "description": "This is an Email",
            "createdAt": "2015-06-23T23:58:09Z+0000",
            "updatedAt": "2015-06-23T23:58:09Z+0000",
            "url": "https://app-abm.marketo.com/#EM2212A1LA1",
            "subject": {
                "type": "Text",
                "value": "Hey There"
            },
            "fromName": {
                "type": "Text",
                "value": "SomeBody"
            },
            "fromEmail": {
                "type": "Text",
                "value": "somebody@marketo.com"
            },
            "replyEmail": {
                "type": "Text",
                "value": "somebody@marketo.com"
            },
            "folder": {
                "type": "Program",
                "value": 1017,
                "folderName": "Landing Page - promotion"
            },
            "operational": false,
            "textOnly": false,
            "publishToMSI": false,
            "webView": false,
            "status": "draft",
            "template": 24,
            "workspace": "Default",
            "isOpenTrackingDisabled": false,
            "version": 2,
            "autoCopyToText": false,
            "ccFields": null,
            "preHeader": null
        }
    ]
}
```

### 内容区域、类型和更新

分别更新每个电子邮件内容部分。 使用[更新电子邮件内容](https://developer.adobe.com/marketo-apis/api/asset#tag/Emails/operation/updateEmailContentUsingPOST)端点更新`subject`、`fromName`、`fromEmail`和`replyEmail`。 此端点还允许您设置这些值，以使用动态内容而不是静态内容。

每个参数都是一个类型/值JSON对象。 类型为`Text`或`DynamicContent`。 该值是用于动态内容的分段的相应文本或ID。 将数据作为POST与`application/x-www-form-urlencoded`一起发送，而不是作为JSON发送。 您还可以设置`isOpenTrackingDisabled`以更新电子邮件内容。

```http
POST /rest/asset/v1/email/{id}/content.json
```

```text
Content-Type: application/x-www-form-urlencoded
```

```text
subject={"type":"Text","value":"Gettysburg Address"}&fromEmail={"type":"Text","value":"abe@testmail.com"}&fromName={"type":"Text","value":"Abe Lincoln"}&replyTO={"type":"Text","value":"replies@testmail.com"}
```

```json
{
   "success":true,
   "warnings":[ ],
   "errors":[ ],
   "requestId":"c865#14a1832afac",
   "result":[
      {
         "id":1356
      }
   ]
}
```

要将部分配置为使用动态内容，请首先使用“获取电子邮件内容”检索其部分ID。

### 更新可编辑部分

通过其`htmlId`更新可编辑部分。 电子邮件`id`和节`htmlId`是必需的路径参数。 `type`、`value`和`textValue`参数是可选的。

`type`确定`value`的内容：

- `Text`： `value`是一个包含该部分的HTML内容的字符串。
- `DynamicContent`： `value`是一个JSON对象，其中包含设置为`DynamicContent`的`type`、设置为分段ID的`segmentation`以及设置为默认HTML内容的`default`。
- `Snippet`：支持的节类型。

可选的`textValue`参数包含节的文本版本。 将数据作为POST与`application/x-www-form-urlencoded`一起发送，而不是作为JSON发送。

```http
POST /rest/asset/v1/email/{id}/content/{htmlId}.json
```

```text
Content-Type: application/x-www-form-urlencoded
```

```text
type=Text&value=<h1>Hello World!</h1>&textValue=Hello World!
```

```json
{
    "success": true,
    "warnings": [ ],
    "errors": [ ],
    "requestId": "155ac#14d58dfa9ad",
    "result": [
        {
            "id": 2179
        }
    ]
}
```

如果为嵌入的代码片段禁用了“自动复制到文本”功能，则更新代码片段HTML并更新其他部分的文本版本会导致电子邮件文本版本反映更新的代码片段HTML。 禁用自动复制时，它不会按预期保留以前的代码片段文本。

## 模块

在电子邮件编辑器1.0中，模块是模板中定义的电子邮件部分。 模块可以包含元素、变量和其他HTML内容，如[电子邮件模板语法](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/email-marketing/general/email-editor-2/email-template-syntax#EmailTemplateSyntax-Modules)中所述。

使用模块API管理电子邮件中的模块。 对于使用HTTP POST的模块端点，将请求正文格式设置为`application/x-www-form-urlencoded`，而不是JSON。

大多数模块端点都需要`moduleId`作为路径参数。 [获取电子邮件内容](https://developer.adobe.com/marketo-apis/api/asset#tag/Emails/operation/getEmailContentByIdUsingGET)终结点返回`htmlId`属性中的模块ID。 请参阅[查询](#modules_query)。

### 查询

要使用模块，请指定唯一标识该模块的`moduleId`。 您可能还需要整数模块索引，该索引描述模块在电子邮件中的顺序。

要[检索模块ID及其索引](https://developer.adobe.com/marketo-apis/api/asset#tag/Emails/operation/getEmailContentByIdUsingGET)，请将电子邮件ID指定为路径参数。

以下示例在模板选取器UI的Starter Templates部分中基于`Skeleton`模板查询1.0电子邮件。

```http
GET /rest/asset/v1/email/{moduleId}/content.json
```

```json
{
  "success": true,
  "warnings": [ ],
  "errors": [ ],
  "requestId": "3d79#158da6492bd",
  "result": [
    {
      "htmlId": "free-image",
      "contentType": "Module",
      "index": 1,
      "parentHtmlId": "template-wrapper",
      "isLocked": false
    },
    {
      "htmlId": "single",
      "value": {
        "width": "600",
        "altText": "",
        "style": "-ms-interpolation-mode: bicubic; outline: none; border-right-width: 0; border-bottom-width: 0; border-left-width: 0; text-decoration: none; border-top-width: 0; display: block; max-width: 100%; line-height: 100%; height: auto; width: 600px"
      },
      "contentType": "Image",
      "parentHtmlId": "free-image",
      "isLocked": false
    },
    {
      "htmlId": "video",
      "contentType": "Module",
      "index": 2,
      "parentHtmlId": "template-wrapper",
      "isLocked": false
    },
    {
      "htmlId": "video2",
      "value": {

      },
      "contentType": "Video",
      "parentHtmlId": "video",
      "isLocked": false
    },
    {
      "htmlId": "free-text",
      "contentType": "Module",
      "index": 3,
      "parentHtmlId": "template-wrapper",
      "isLocked": false
    },
    {
      "htmlId": "text",
      "value": [
        {
          "type": "HTML",
          "value": "Lorem ipsum dolor sit amet, consectetur adipisicing elit. Harum officiis dolorum, nulla, mollitia ducimus iure modi perferendis tenetur ea illum veniam aut sapiente deserunt repellendus. Excepturi illo numquam sint harum."
        },
        {
          "type": "Text",
          "value": "Lorem ipsum dolor sit amet, consectetur adipisicing elit. Harum officiis dolorum, nulla, mollitia ducimus iure modi perferendis tenetur ea illum veniam aut sapiente deserunt repellendus. Excepturi illo numquam sint harum."
        }
      ],
      "contentType": "Text",
      "parentHtmlId": "free-text",
      "isLocked": false
    },
    {
      "htmlId": "two-articles",
      "contentType": "Module",
      "index": 6,
      "parentHtmlId": "template-wrapper",
      "isLocked": false
    },
    {
      "htmlId": "article3",
      "value": {
        "height": "auto",
        "width": "270",
        "style": "-ms-interpolation-mode: bicubic; outline: none; border-right-width: 0; border-bottom-width: 0; border-left-width: 0; text-decoration: none; border-top-width: 0; display: block; max-width: 100%; line-height: 100%; height: auto; width: 270px"
      },
      "contentType": "Image",
      "parentHtmlId": "two-articles",
      "isLocked": false
    },
    {
      "htmlId": "articleTitle",
      "value": [
        {
          "type": "HTML",
          "value": "LOREM IPSUM DOLOR SIT AMET"
        },
        {
          "type": "Text",
          "value": "LOREM IPSUM DOLOR SIT AMET"
        }
      ],
      "contentType": "Text",
      "parentHtmlId": "two-articles",
      "isLocked": false
    },
    {
      "htmlId": "text2",
      "value": [
        {
          "type": "HTML",
          "value": "Gumbo beet greens corn soko endive gumbo gourd. shallot courgette tatsoi pea sprouts fava bean collard greens dandelion okra wakame tomato. Dandelion cucumber earthnut pea peanut soko zucchini."
        },
        {
          "type": "Text",
          "value": "Gumbo beet greens corn soko endive gumbo gourd. shallot courgette tatsoi pea sprouts fava bean collard greens dandelion okra wakame tomato. Dandelion cucumber earthnut pea peanut soko zucchini."
        }
      ],
      "contentType": "Text",
      "parentHtmlId": "two-articles",
      "isLocked": false
    },
    {
      "htmlId": "article4",
      "value": {
        "height": "auto",
        "width": "270",
        "style": "-ms-interpolation-mode: bicubic; outline: none; border-right-width: 0; border-bottom-width: 0; border-left-width: 0; text-decoration: none; border-top-width: 0; display: block; max-width: 100%; line-height: 100%; height: auto; width: 270px"
      },
      "contentType": "Image",
      "parentHtmlId": "two-articles",
      "isLocked": false
    },
    {
      "htmlId": "articleTitle2",
      "value": [
        {
          "type": "HTML",
          "value": "LOREM IPSUM DOLOR SIT AMET"
        },
        {
          "type": "Text",
          "value": "LOREM IPSUM DOLOR SIT AMET"
        }
      ],
      "contentType": "Text",
      "parentHtmlId": "two-articles",
      "isLocked": false
    },
    {
      "htmlId": "text3",
      "value": [
        {
          "type": "HTML",
          "value": "Gumbo beet greens corn soko endive gumbo gourd. shallot courgette tatsoi pea sprouts fava bean collard greens dandelion okra wakame tomato. Dandelion cucumber earthnut pea peanut soko zucchini."
        },
        {
          "type": "Text",
          "value": "Gumbo beet greens corn soko endive gumbo gourd. shallot courgette tatsoi pea sprouts fava bean collard greens dandelion okra wakame tomato. Dandelion cucumber earthnut pea peanut soko zucchini."
        }
      ],
      "contentType": "Text",
      "parentHtmlId": "two-articles",
      "isLocked": false
    },
    {
      "htmlId": "footer",
      "contentType": "Module",
      "index": 7,
      "parentHtmlId": "template-wrapper",
      "isLocked": false
    },
    {
      "htmlId": "footerText",
      "value": [
        {
          "type": "HTML",
          "value": "<p style=\"text-align: center;\"><span style=\"color: #333333;\"><strong>Acme, Inc<\/strong><\/span><\/p> \n<div style=\"text-align: center;\">\n  You received this because you have subscribed to our newsletter. Click \n <a href=\"{{system.unsubscribeLink}}\" target=\"_blank\" class=\"mktNoTrack\">here<\/a> to unsubscribe. \n <br> \n<\/div>"
        },
        {
          "type": "Text",
          "value": "Acme, Inc \n You received this because you have subscribed to our newsletter. Click here <{{system.unsubscribeLink}}> to unsubscribe."
        }
      ],
      "contentType": "Text",
      "parentHtmlId": "footer",
      "isLocked": false
    },
    {
      "htmlId": "spacer",
      "contentType": "Module",
      "index": 0,
      "parentHtmlId": "template-wrapper",
      "isLocked": false
    },
    {
      "htmlId": "CTA",
      "contentType": "Module",
      "index": 4,
      "parentHtmlId": "template-wrapper",
      "isLocked": false
    },
    {
      "htmlId": "hr",
      "contentType": "Module",
      "index": 5,
      "parentHtmlId": "template-wrapper",
      "isLocked": false
    }
  ]
}
```

结果数组包含模块和HTML元素描述。 模块元素具有`Module`的`contentType`并包含`index`。 `htmlId`属性包含`moduleId`。

对于`Skeleton`示例，下表将每个`moduleId`映射到其在电子邮件中的索引。

| moduleId （也称为htmlId） | 索引 |
| --- | --- |
| 分隔条 | 0 |
| 自由图像 | 1 |
| 视频 | 2 |
| 自由文本 | 3 |
| CTA | 4 |
| 小时 | 5 |
| 两篇文章 | 6 |
| 页脚 | 7 |

#### Add

要[添加模块](https://developer.adobe.com/marketo-apis/api/asset#tag/Emails/operation/addModuleUsingPOST)，请从电子邮件的模板中选择现有模块。 将电子邮件ID和`moduleId`指定为路径参数。 所需的`index`查询参数确定模块的位置。 如果`index`超过了最大现有索引，则API会将模块附加到电子邮件。

```http
POST /rest/asset/v1/email/{id}/content/{moduleId}/add.json
```

```text
Content-Type: application/x-www-form-urlencoded
```

```text
index=10
```

```json
{
    "success": true,
    "warnings": [ ],
    "errors": [ ],
    "requestId": "1063e#158d6ad2c3f",
    "result": [
        {
            "id": 1028
        }
    ]
}
```

#### 删除

要[删除模块](https://developer.adobe.com/marketo-apis/api/asset#tag/Emails/operation/deleteModuleUsingPOST)，请指定电子邮件ID和`moduleId`作为路径参数。

```http
POST /rest/asset/v1/email/{id}/content/{moduleId}/delete.json
```

```json
{
    "success": true,
    "warnings": [ ],
    "errors": [ ],
    "requestId": "2356#158d6f6104a",
    "result": [
        {
            "id":1028
        }
    ]
}
```

#### 复制

要[复制模块](https://developer.adobe.com/marketo-apis/api/asset#tag/Emails/operation/duplicateModuleUsingPOST)，请指定电子邮件ID和`moduleId`作为路径参数。 API将重复项放置在原始模块下方，并将剩余模块向下移动。

```http
POST /rest/asset/v1/email/{id}/content/{moduleId}/duplicate.json
```

```json
{
    "success": true,
    "warnings": [ ],
    "errors": [ ],
    "requestId": "e740#158d705d967",
    "result": [
        {
            "id":1028
        }
    ]
}
```

#### 重新排列

要[重新排列模块](https://developer.adobe.com/marketo-apis/api/asset#tag/Emails/operation/rearrangeModulesUsingPOST)，请提交包含每个模块及其所需位置的数组。 每个数组元素都是格式为`{ "index": <_index_>, "moduleId": "<_moduleId_>" }`的JSON对象，其中`<_index_>`是从零开始的模块位置，`<_moduleId_>`是模块ID。

```http
POST /rest/asset/v1/email/{id}/content/rearrange.json
```

```text
Content-Type: application/x-www-form-urlencoded
```

```text
positions=[ {"index": 0, "moduleId": "free-image"}, {"index": 1, "moduleId": "title"}, {"index": 2, "moduleId": "mkvideo"}, {"index": 3, moduleId": "free-text"}, {"index": 4, "moduleId": "blankSpace"}, {"index": 5, "moduleId": "Separator"}, {"index": 6, "moduleId": "callToAction"}, {"index": 7, "moduleId": "blankSpace2"}, {"index": 8, "moduleId": "blankSpace3"} ]
```

```json
{
    "success": true,
    "warnings":[ ],
    "errors":[ ],
    "requestId": "e67a#158d72d1cde",
    "result":[
        {
            "id": 1030
        }
    ]
}
```

#### 重命名

要[重命名模块](https://developer.adobe.com/marketo-apis/api/asset#tag/Emails/operation/renameUsingPOST)，请在`name`参数中传递新名称。 将电子邮件ID和现有`moduleId`指定为路径参数。

```http
POST /rest/asset/v1/email/{id}/content/{moduleId}/rename.json
```

```text
Content-Type: application/x-www-form-urlencoded
```

```text
name=MarketoVideo
```

```json
{
    "success": true,
    "warnings":[ ],
    "errors": [ ],
    "requestId":"11521#158d740abc0",
    "result": [
        {
            "id": 1030
        }
    ]
}
```

## 变量

在Email Editor 1.0中，变量存储电子邮件元素的值。 通过向HTML添加特定于Marketo的语法来定义每个变量，如[Email template syntax](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/email-marketing/general/email-editor-2/email-template-syntax#EmailTemplateSyntax-Variables)中所述。 使用变量API管理电子邮件中的变量。

### 查询

要[检索变量](https://developer.adobe.com/marketo-apis/api/asset#tag/Emails/operation/getEmailVariablesUsingGET)，请将电子邮件ID指定为路径参数。

以下示例在模板选取器UI的Starter Templates部分中基于`Skeleton`模板查询1.0电子邮件。

```http
GET /rest/asset/v1/email/{id}/variables.json
```

```json
{
  "success": true,
  "warnings": [ ],
  "errors": [  ],
  "requestId": "756#158dade55e8",
  "result": [
    {
      "name": "twoArticlesSpacer5",
      "value": "20",
      "moduleScope": false
    },
    {
      "name": "twoArticlesSpacer6",
      "value": "15",
      "moduleScope": false
    },
    {
      "name": "twoArticlesBackgroundColor",
      "value": "#ffffff",
      "moduleScope": false
    },
    {
      "name": "footerSpacer2",
      "value": "20",
      "moduleScope": false
    },
    {
      "name": "twoArticlesSpacer7",
      "value": "20",
      "moduleScope": false
    },
    {
      "name": "twoArticlesLinkText2",
      "value": "CALL TO ACTION",
      "moduleScope": false
    },
    {
      "name": "twoArticlesSpacer8",
      "value": "40",
      "moduleScope": false
    },
    {
      "name": "twoArticlesLinkText",
      "value": "CALL TO ACTION",
      "moduleScope": false
    },
    {
      "name": "freeTextSpacer",
      "value": "40",
      "moduleScope": false
    },
    {
      "name": "freeTextSpacer2",
      "value": "40",
      "moduleScope": false
    },
    {
      "name": "ctaSpacer2",
      "value": "20",
      "moduleScope": false
    },
    {
      "name": "hrBackgroundColor",
      "value": "#ffffff",
      "moduleScope": false
    },
    {
      "name": "freeTextBackgroundColor",
      "value": "#ffffff",
      "moduleScope": false
    },
    {
      "name": "spacerBackgroundColor",
      "value": "#ffffff",
      "moduleScope": false
    },
    {
      "name": "twoArticlesLink2",
      "value": "http:\/\/mylink",
      "moduleScope": false
    },
    {
      "name": "hrBorderColor",
      "value": "#e6e6e6",
      "moduleScope": false
    },
    {
      "name": "twoArticlesSpacer",
      "value": "40",
      "moduleScope": false
    },
    {
      "name": "twoArticlesBorderSize",
      "value": "1",
      "moduleScope": false
    },
    {
      "name": "ctaLink",
      "value": "http:\/\/mylink",
      "moduleScope": false
    },
    {
      "name": "freeImageBackgroundColor",
      "value": "#ffffff",
      "moduleScope": false
    },
    {
      "name": "spacerSpacer",
      "value": "40",
      "moduleScope": false
    },
    {
      "name": "footerSpacer",
      "value": "10",
      "moduleScope": false
    },
    {
      "name": "ctaLinkText",
      "value": "CALL TO ACTION",
      "moduleScope": false
    },
    {
      "name": "twoArticlesButtonBackgroundColor2",
      "value": "#333333",
      "moduleScope": false
    },
    {
      "name": "ctaBorderSize",
      "value": "1",
      "moduleScope": false
    },
    {
      "name": "ctaBackgroundColor",
      "value": "#ffffff",
      "moduleScope": false
    },
    {
      "name": "footerBackgroundColor",
      "value": "#ffffff",
      "moduleScope": false
    },
    {
      "name": "twoArticlesLink",
      "value": "http:\/\/mylink",
      "moduleScope": false
    },
    {
      "name": "twoArticlesBorderColor",
      "value": "#333333",
      "moduleScope": false
    },
    {
      "name": "ctaBorderColor",
      "value": "#333333",
      "moduleScope": false
    },
    {
      "name": "twoArticlesBorderColor2",
      "value": "#333333",
      "moduleScope": false
    },
    {
      "name": "hrBorderSize",
      "value": "1",
      "moduleScope": false
    },
    {
      "name": "twoArticlesButtonBackgroundColor",
      "value": "#333333",
      "moduleScope": false
    },
    {
      "name": "twoArticlesBorderSize2",
      "value": "1",
      "moduleScope": false
    },
    {
      "name": "ctaButtonBackgroundColor",
      "value": "#333333",
      "moduleScope": false
    },
    {
      "name": "twoArticlesSpacer4",
      "value": "20",
      "moduleScope": false
    },
    {
      "name": "twoArticlesSpacer3",
      "value": "15",
      "moduleScope": false
    },
    {
      "name": "twoArticlesSpacer2",
      "value": "20",
      "moduleScope": false
    },
    {
      "name": "ctaSpacer",
      "value": "20",
      "moduleScope": false
    }
  ]
}
```

结果数组中的每个元素都描述一个变量。

变量可以具有整个电子邮件的全局范围，也可以具有模块的本地范围。 每个变量都包含`name`、`value`和`moduleScope`属性。 对于全局变量，布尔`moduleScope`属性是`false`；对于局部变量，布尔属性是`true`。 局部变量还包括其关联模块的`moduleId`。

#### 更新

要[更新变量](https://developer.adobe.com/marketo-apis/api/asset#tag/Emails/operation/updateVariableUsingPOST)，请在`value`参数中传递新值。 将电子邮件ID和变量名称指定为路径参数。 在更新模块变量时，也传递`moduleId`以标识关联的模块。

以下示例更新全局变量`hrBorderSize`。

```http
POST /rest/asset/v1/email/{id}/variable/{name}.json
```

```text
Content-Type: application/x-www-form-urlencoded; charset=utf-8
```

```text
value=2
```

```json
{
    "success":true,
    "warnings":[ ],
    "errors":[ ],
    "requestId":"feb5#158db4be57e",
    "result": [
        {
            "name":"hrBorderSize",
            "value":"2",
            "moduleScope":false
        }
    ]
}
```

以下示例将模块`CTA`中的本地变量`ctaLinkText`更新为`Click this button!`。

```http
POST /rest/asset/v1/email/1032/variable/ctaLinkText.json
```

```text
Content-Type: application/x-www-form-urlencoded
```

```text
value=Click this button!&moduleId=CTA
```

```json
{
    "success": true,
    "warnings":[ ],
    "errors":[ ],
    "requestId": "7f34#158dc28d2f7",
    "result": [
        {
            "name":"ctaLinkText",
            "value":"Click this button!",
            "moduleScope":true,
            "moduleId":"CTA"
        }
    ]
}
```

## 审批

电子邮件遵循标准资产审批生命周期。 单独的端点可让您批准草稿、取消批准已批准的版本或放弃现有草稿。

### 批准

审批端点根据Marketo电子邮件规则验证电子邮件。 在批准前必须填充`from name`、`from email`、`reply to email`和`subject`。

```http
POST /rest/asset/v1/email/{id}/approveDraft.json
```

```json
{
   "success":true,
   "warnings":[ ],
   "errors":[ ],
   "requestId":"15dbf#14a1832ae86",
   "result":[
      {
         "id":1362
      }
   ]
}
```

#### 取消批准

仅对批准的电子邮件使用`unapprove`操作。

```http
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

#### 放弃

您只能丢弃处于草稿状态的电子邮件。 无法放弃已批准的电子邮件。

```http
POST /rest/asset/v1/email/{id}/discardDraft.json
```

```json
{
   "success":true,
   "warnings":[ ],
   "errors":[ ],
   "requestId":"182c0#14a1832af4f",
   "result":[
      {
         "id":1362
      }
   ]
}
```

#### 删除

```http
POST /rest/asset/v1/email/{id}/delete.json
```

```json
{
   "success":true,
   "warnings":[ ],
   "errors":[ ],
   "requestId":"169cd#14a1832adba",
   "result":[
      {
         "id":1361
      }
   ]
}
```

## 克隆

要克隆电子邮件，请使用以下参数发送`application/x-www-form-urlencoded` POST请求：

- `name`：必需。 克隆的电子邮件名称。
- `folder`：必需。 包含`id`和`type`的嵌入式JSON对象。
- `description`：可选。 克隆的电子邮件说明。

如果源电子邮件没有批准的版本，则端点将克隆草稿版本。

```http
POST /rest/asset/v1/email/{id}/clone.json
```

```text
Content-Type: application/x-www-form-urlencoded
```

```text
name=Clone of Social Sharing in Email&folder={"id":239,"type":"Folder"}&description=This is a test of clone email
```

```json
{
    "success": true,
    "warnings": [],
    "errors": [],
    "requestId": "bd49#15706f43d96",
    "result": [
        {
            "id": 2250,
            "name": "Clone of Social Sharing in Email",
            "description": "This is a test of clone email",
            "createdAt": "2016-09-07T23:20:52Z+0000",
            "updatedAt": "2016-09-07T23:20:52Z+0000",
            "url": "https://app-abm.marketo.com/#EM2250B2",
            "subject": {
                "type": "Text",
                "value": "Hey There"
            },
            "fromName": {
                "type": "Text",
                "value": "SomeBody"
            },
            "fromEmail": {
                "type": "Text",
                "value": "somebody@marketo.com"
            },
            "replyEmail": {
                "type": "Text",
                "value": "somebody@marketo.com"
            },
            "folder": {
                "type": "Folder",
                "value": 239,
                "folderName": "Tradeshows and Events"
            },
            "operational": false,
            "textOnly": false,
            "publishToMSI": false,
            "webView": false,
            "status": "draft",
            "template": 24,
            "workspace": "Default",
            "isOpenTrackingDisabled": false
        }
    ]
}
```

## 发送示例

若要发送示例电子邮件，请在`emailAddress`查询参数中指定收件人。 您还可以包含以下可选参数：

- `leadId`：模拟数据库中的特定潜在客户。
- `textOnly`：仅发送电子邮件的文本版本。

```http
POST /rest/asset/v1/email/{id}/sendSample.json
```

```text
Content-Type: application/x-www-form-urlencoded
```

```text
emailAddress=abe@testmail.com&textOnly=true
```

```json
{
    "success": true,
    "warnings": [ ],
    "errors": [ ],
    "requestId": "360b#14cce7d2708",
    "result": [
        {
            "id": 2179
        }
    ]
}
```

## 预览电子邮件

使用[获取电子邮件完整内容](https://developer.adobe.com/marketo-apis/api/asset#tag/Emails/operation/getEmailFullContentUsingGET)端点检索电子邮件的实时预览，因为收件人会收到该电子邮件。 此端点仅支持1.0版本的电子邮件。

所需的`id`路径参数标识要预览的电子邮件。 端点还接受三个可选的查询参数：

- `status`：接受`draft`或`approved`。 默认值是已批准电子邮件的已批准版本或未批准电子邮件的草稿版本。
- `type`：接受`Text`或`HTML`。 默认值为 `HTML`。
- `leadId`：接受潜在客户的整数ID，并预览电子邮件，就像该潜在客户已收到电子邮件一样。

```http
GET /rest/asset/v1/email/{id}/fullContent.json
```

```json
{
   "success": true,
   "warnings": [ ],
   "errors": [ ],
   "requestId": null,
   "result": [
      {
         "id": 339,
         "status": "draft",
         "content": "<!DOCTYPE HTML PUBLIC \"-//W3C//DTD HTML 1.01 Transitional//EN\" \"http://www.w1.org/TR/html4/loose.dtd\">\n<html>\n  <head>\n    <meta http-equiv=\"Content-Type\" content=\"text/html; charset=utf-8\"/>\n    <title></title>\n  </head>\n  <body>\n      <div style=\"font: 14px tahoma; width: 100%\" class=\"mktEditable\" id=\"edit_text_3\">\n        Content from testCreateEmailTemplate2\n    </div>\n  </body>\n</html>"
      }
   ]
}
```

## 替换HTML

使用[更新电子邮件完整内容](https://developer.adobe.com/marketo-apis/api/asset#tag/Emails/operation/createEmailFullContentUsingPOST)端点替换电子邮件资源中的所有内容。 此端点仅支持已在UI中使用编辑代码功能且不再链接到其父模板的1.0版本电子邮件。

端点主要适用于作为项目的一部分克隆的资产，这些资产不能通过标准内容端点进行更改。 它不支持包含动态内容的电子邮件。 如果电子邮件仍然链接到其模板，则端点返回错误。

发送路径中带有电子邮件`id`的`multipart/form-data`请求。 请求正文必须包含一个`content`参数，该参数具有完整的HTML电子邮件文档以及内容类型`text/html`。

格式错误的HTML文档会生成警告并可能阻止审批。 包含JavaScript或`<script>`标记会导致调用失败并出现错误。

```http
POST /rest/asset/v1/email/{id}/fullContent.json
```

```text
content-type: multipart/form-data; boundary=--------------------------116301888604800085728247
content-length: 599
```

```html
----------------------------116301888604800085728247
Content-Disposition: form-data; name="content"; filename="email_content.html"
Content-Type: text/html

<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 1.01 Transitional//EN" "http://www.w1.org/TR/html4/loose.dtd">
 <html>
 <head>
 <meta http-equiv="Content-Type" content="text/html; charset=utf-8"/>
 <title></title>
 </head>
 <body>
 <div style="font: 14px tahoma; width: 100%" class="mktEditable" id="edit_text_3">
 EMAIL TEST CONTENT
 </div>
 </body>
 </html>
----------------------------116301888604800085728247--
```

```json
{
   "success": true,
   "warnings": [ ],
   "errors": [ ],
   "requestId": "15dbf#14a1832ae86",
   "result": [
      {
         "id": 1001
      }
   ]
}
```
