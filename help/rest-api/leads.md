---
title: 潜在客户
feature: REST API
description: 探索Marketo潜在客户REST API功能，包括描述、按ID或过滤器查询、默认字段、限制和检索ECID。
exl-id: 0a2f7c38-02ae-4d97-acfe-9dd108a1f733
source-git-commit: cc4bd7c18124bb039386a1cec06b9f1da0d047cb
workflow-type: tm+mt
source-wordcount: '3411'
ht-degree: 2%

---

# 潜在客户

[潜在客户终结点引用](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Leads)

Marketo Lead的API为针对潜在客户记录的简单CRUD应用程序提供了一系列功能，并提供了修改潜在客户在静态列表和程序中的成员资格，以及启动潜在客户的Smart Campaign处理等功能。

## 描述

Leads API的关键功能之一是Describe方法。 使用描述潜在客户检索可用于通过REST API进行交互的字段的完整列表，以及每个字段的元数据：

* 数据类型
* REST API名称
* 长度（如果适用）
* 只读
* 友好标签

描述是字段是否可用以及有关这些字段的元数据的主要真实来源。

### 请求

```
GET /rest/v1/leads/describe.json
```

### 响应

```json
{
   "requestId":"37ca#1475b74e276",
   "success":true,
   "result":[
      {
         "id":2,
         "displayName":"Company Name",
         "dataType":"string",
         "length":255,
         "rest":{
            "name":"company",
            "readOnly":false
         },
         "soap":{
            "name":"Company",
            "readOnly":false
         }
      }
}
```

通常，响应在结果数组中包含一组更多的字段，但出于演示目的，我们忽略了它们。 结果数组中的每一项都对应于潜在客户记录中可用的字段，并且至少具有id、displayName和数据类型。 对于给定字段，rest和soap子对象可能存在，也可能不存在，并且其存在将指示该字段在REST或SOAP API中是否有效。 `readOnly`属性通过相应的API(REST或SOAP)指示字段是否为只读。 length属性指示字段的最大长度（如果存在）。 dataType属性指示字段的数据类型。

## 查询

商机检索的主要方法有两种：按ID获取商机，和按过滤器类型获取商机。 按ID获取潜在客户采用单个潜在客户ID作为路径参数并返回单个潜在客户记录。

或者，您可以传递一个字段参数，其中包含要返回的以逗号分隔的字段名称列表。 如果此请求中未包含字段参数，则返回以下默认字段： `email`、`updatedAt`、`createdAt`、`lastName`、`firstName`和`id`。 在请求字段列表时，如果请求的是特定字段但未返回，则该值默认为空。

### 请求

```
GET /rest/v1/lead/{id}.json
```

### 响应

```json
{
   "requestId": "10226#14d3049e51b",
   "success": true,
   "result": [
      {
         "id": 318581,
         "updatedAt":"2015-05-07T11:47:30-08:00"
         "lastName": "Doe",
         "email": "jdoe@marketo.com",
         "createdAt": "2015-05-01T16:47:30-08:00",
         "firstName": "John"
      }
   ]
}
```

对于此方法，在结果数组的第一个位置始终有单个记录。

按过滤器类型获取潜在客户将返回相同类型的记录，但每页最多可能返回300条记录。 它需要`filterType`和`filterValues`查询参数。

`filterType`接受任何自定义字段，或大多数常用字段。 调用`Describe2`端点以获取允许在`filterType`中使用的可搜索字段的完整列表。 按自定义字段搜索时，仅支持以下数据类型： `string`、`email`、`integer`。 您可以使用上述Describe方法获取字段详细信息（描述、类型等）。

`filterValues`以逗号分隔格式接受最多300个值。 该调用将搜索潜在客户字段与所包含`filterValues`之一匹配的记录。 如果与商机过滤器匹配的商机数量大于1,000，则会返回错误：“1003，有太多结果与过滤器匹配”。

如果GET请求的总长度超过8 KB，则会返回HTTP错误：“414， URI过长”（根据RFC 7231）。 作为解决方法，您可以将GET更改为POST，添加_method=GET参数，并在请求正文中放置查询字符串。

### 请求

```
GET /rest/v1/leads.json?filterType=id&filterValues=318581,318592
```

### 响应

```json
{
    "requestId": "12951#15699db5c97",
    "result": [
        {
            "id": 318581,
            "updatedAt": "2016-05-17T22:11:45Z",
            "lastName": "Lincoln",
            "email": "abe@usa.gov",
            "createdAt": "2015-03-17T00:18:40Z",
            "firstName": "Abraham"
        },
        {
            "id": 318592,
            "updatedAt": "2016-05-17T22:20:51Z",
            "lastName": "Washington",
            "email": "george@usa.gov",
            "createdAt": "2015-04-06T16:29:21Z",
            "firstName": "George"
        }
    ],
    "success": true
}
```

此调用搜索与`filterValues`中包含的ID匹配的记录，并返回任何匹配的记录。

如果未找到记录，则响应将指示成功，但结果数组将为空。

### 响应

```json
{
"requestId": "177a1#1578b643357",
"result": [],
"success": true
}
```

按ID获取潜在客户和按过滤器类型获取潜在客户都将接受字段查询参数，该参数接受以逗号分隔的API字段列表。 如果包含此字段，则响应中的每条记录都将包含这些列出的字段。  如果忽略，则将返回一组默认的字段： `id`、`email`、`updatedAt`、`createdAt`、`firstName`和`lastName`。

## ADOBE ECID

启用Adobe Experience Cloud受众共享功能后，会执行一个Cookie同步过程，以将Adobe Experience Cloud ID (ECID)与Marketo潜在客户关联。  上述商机检索方法可用于检索关联的ECID值。  通过在字段参数中指定`ecids`来执行此操作。 例如：`&fields=email,firstName,lastName,ecids`。

## 创建和更新

除了检索潜在客户数据之外，您还可以通过API创建、更新和删除潜在客户记录。 创建和更新潜在客户与请求中定义的操作类型共享同一端点，同时可以创建或更新多达300条记录。

>[!NOTE]
>
> 不支持使用[同步潜在客户](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Leads/operation/syncLeadUsingPOST)终结点更新公司字段。 请改用[同步公司](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Companies/operation/syncCompaniesUsingPOST)终结点。

>[!NOTE]
>
> 创建或更新人员记录的电子邮件值时，电子邮件地址字段仅支持ASCII字符。

### 请求

```
POST /rest/v1/leads.json
```

### 正文

```json
{
   "action":"createOnly",
   "lookupField":"email",
   "input":[
      {
         "email":"kjashaedd-1@klooblept.com",
         "firstName":"Kataldar-1",
         "postalCode":"04828"
      },
      {
         "email":"kjashaedd-2@klooblept.com",
         "firstName":"Kataldar-2",
         "postalCode":"04828"
      },
      {
         "email":"kjashaedd-3@klooblept.com",
         "firstName":"Kataldar-3",
         "postalCode":"04828"
      }
   ]
}
```

### 响应

```json
{
   "requestId":"e42b#14272d07d78",
   "success":true,
   "result":[
      {
         "id":50,
         "status":"created"
      },
      {
         "id":51,
         "status":"created"
      },
      {
         "id":52,
         "status":"created"
      }
   ]
}
```

在此请求中，您会看到两个重要字段：`action`和`lookupField`。  `action`指定请求的操作类型，可以是`createOrUpdate`、`createOnly`、`updateOnly`或`createDuplicate`。 如果忽略，则该操作将默认为`createOrUpdate`。  `lookupField`参数指定操作为`createOrUpdate`或`updateOnly`时要使用的键。 如果忽略`lookupField`，则默认键为`email`。

缺省情况下，使用缺省分区。 或者，您可以指定`partitionName`参数，该参数仅在操作为`createOnly`或`createOrUpdate`时才有效。 要使`partitionName`用作其他重复数据删除条件，它必须是自定义重复数据删除规则中源类型的一部分。 在更新操作期间，如果指定的分区中不存在潜在客户，则会返回错误。 如果仅API用户没有访问指定分区的权限，则会返回错误。

使用`id`操作时，`updateOnly`字段只能作为参数包含，因为`id`是系统管理的唯一键。

请求还必须具有`input`参数，该参数为潜在客户记录数组。 每个潜在客户记录都是一个JSON对象，其中包含任意数量的潜在客户字段。 记录中包含的键对于该记录应该是唯一的，并且所有JSON字符串都应采用UTF-8编码。 `externalCompanyId`字段可用于将潜在客户记录链接到公司记录。 `externalSalesPersonId`字段可用于将潜在客户记录链接到销售人员记录。

注意：当同时或连续快速执行潜在客户更新插入请求时，如果在首次返回之前使用相同的值执行后续调用，则在使用相同的键值发出多个请求时，可能会导致出现重复记录。 可以通过使用`createOnly`或`updateOnly`来避免这种情况，或者通过将呼叫排入队列并等待您的呼叫返回，然后再使用相同的键进行后续的更新插入调用。

## 字段

潜在客户对象包含标准字段和可选的自定义字段。 标准字段存在于每个Marketo Engage订阅中，而自定义字段由用户根据需要创建。 每个字段定义由一组描述该字段的属性组成。 属性的示例包括显示名称、API名称和数据类型。 这些属性统称为元数据。

以下端点允许您查询、创建和更新潜在客户对象上的字段。 这些API要求拥有权限的API用户具有读写架构标准字段或读写架构自定义字段权限中的一个或两个角色。

## 查询字段

查询潜在客户字段很简单。 您可以按API名称查询单个潜在客户字段，或查询所有潜在客户字段集。 根据所使用的角色权限，可以检索标准字段和自定义字段。 还检索隐藏字段。

## 按名称

按名称获取潜在客户字段端点可检索潜在客户对象上单个字段的元数据。 必填的fieldApiName路径参数指定字段的API名称。 响应类似于Describe Lead端点，但包含其他元数据，例如isCustom属性，该属性指示字段是否为自定义字段。

### 请求

```
GET /rest/v1/leads/schema/fields/{fieldApiName}.json
```

### 响应

```json
{
    "requestId": "cd97#1793ee0fec4",
    "result": [
        {
            "displayName": "Email Address",
            "name": "email",
            "description": null,
            "dataType": "email",
            "length": 255,
            "isHidden": false,
            "isHtmlEncodingInEmail": true,
            "isSensitive": true,
            "isCustom": false
        }
    ],
    "success": true
}
```

## 浏览

获取潜在客户字段端点检索潜在客户对象中所有字段的元数据，包括。 默认情况下，最多返回300条记录。 您可以使用`batchSize`查询参数来减少此数量。 如果`moreResult`属性为true，则表示有更多的结果可用。 继续调用此终结点，直到`moreResult`属性返回false，这意味着没有可用的结果。 从此API返回的`nextPageToken`应始终在此调用的下一个迭代中重用。

### 请求

```
GET /rest/v1/leads/schema/fields.json
```

### 响应（已截断）

```json
{
    "requestId": "142c3#1793eb976d8",
    "result": [
        {
            "displayName": "Salutation",
            "name": "salutation",
            "description": null,
            "dataType": "string",
            "length": 255,
            "isHidden": false,
            "isHtmlEncodingInEmail": true,
            "isSensitive": true,
            "isCustom": false
        },
        {
            "displayName": "First Name",
            "name": "firstName",
            "description": null,
            "dataType": "string",
            "length": 255,
            "isHidden": false,
            "isHtmlEncodingInEmail": true,
            "isSensitive": true,
            "isCustom": false
        },
        {
            "displayName": "Middle Name",
            "name": "middleName",
            "description": null,
            "dataType": "string",
            "length": 255,
            "isHidden": false,
            "isHtmlEncodingInEmail": true,
            "isSensitive": true,
            "isCustom": false
        },
        {
            "displayName": "Last Name",
            "name": "lastName",
            "description": null,
            "dataType": "string",
            "length": 255,
            "isHidden": false,
            "isHtmlEncodingInEmail": true,
            "isSensitive": true,
            "isCustom": false
        },
        {
            "displayName": "Date of Birth",
            "name": "dateOfBirth",
            "description": null,
            "dataType": "date",
            "isHidden": false,
            "isHtmlEncodingInEmail": false,
            "isSensitive": true,
            "isCustom": false
        },
        {
            "displayName": "Email Address",
            "name": "email",
            "description": null,
            "dataType": "email",
            "length": 255,
            "isHidden": false,
            "isHtmlEncodingInEmail": true,
            "isSensitive": true,
            "isCustom": false
        },
        {
            "displayName": "Phone Number",
            "name": "phone",
            "description": null,
            "dataType": "phone",
            "length": 255,
            "isHidden": false,
            "isHtmlEncodingInEmail": true,
            "isSensitive": true,
            "isCustom": false
        },
        {
            "displayName": "Mobile Phone Number",
            "name": "mobilePhone",
            "description": null,
            "dataType": "phone",
            "length": 255,
            "isHidden": false,
            "isHtmlEncodingInEmail": true,
            "isSensitive": true,
            "isCustom": false
        },
        {
            "displayName": "Fax Number",
            "name": "fax",
            "description": null,
            "dataType": "phone",
            "length": 255,
            "isHidden": false,
            "isHtmlEncodingInEmail": true,
            "isSensitive": true,
            "isCustom": false
        },
        {
            "displayName": "Job Title",
            "name": "title",
            "description": null,
            "dataType": "string",
            "length": 255,
            "isHidden": false,
            "isHtmlEncodingInEmail": true,
            "isSensitive": true,
            "isCustom": false
        },
        {
            "displayName": "Unsubscribed",
            "name": "unsubscribed",
            "description": null,
            "dataType": "boolean",
            "isHidden": false,
            "isHtmlEncodingInEmail": false,
            "isSensitive": true,
            "isCustom": false
        },
        ...
    ],
    "success": true,
    "moreResult": false
}
```

## 创建字段

“创建潜在客户字段”端点在潜在客户对象中创建一个或多个自定义字段。 此端点提供的功能与Marketo Engage UI中提供的功能相当。 您可以使用此端点创建最多100个自定义字段。
请仔细考虑您在使用API的Marketo Engage生产实例中创建的每个字段。  创建字段后，便无法删除它（只能隐藏它）。 未使用字段的激增是一种不良做法，会增加实例的混乱。

所需的输入参数是潜在客户字段对象的数组。 每个对象都包含一个或多个属性。 必需的属性是`displayName`、`name`和`dataType`，它们分别对应于字段的UI显示名称、字段的API名称和字段类型。  您可以选择指定`description`、`isHidden`、`isHtmlEncodingInEmail`和`isSensitive`。

有一些规则与名称和`displayName`命名关联。 name属性必须是唯一的，以字母开头，并且只包含字母、数字或下划线。 `displayName`必须是唯一的，并且不能包含特殊字符。  常见的命名惯例是将驼峰式大小写应用于`displayName`以生成名称。 例如，“我的自定义字段”的`displayName`将生成“myCustomField”的名称。

### 请求

```
POST /rest/v1/leads/schema/fields.json
```

### 正文

```json
{
  "input": [
      {
        "displayName": "Acme Access Code",
        "name": "acmeAccessCode",
        "description": "Acme Direct Mail Integration",
        "dataType": "string"
      },
      {
        "displayName": "Acme Mail Date",
        "name": "acmeMailDate",
        "description": "Acme Direct Mail Integration",
        "dataType": "string"
      }
  ]
}
```

### 响应

```json
{
    "requestId": "d9f1#17943666811",
    "result": [
        {
            "name": "acmeAccessCode",
            "status": "created"
        },
        {
            "name": "acmeMailDate",
            "status": "created"
        }
    ],
    "success": true
}
```

## 更新字段

“更新潜在客户字段”端点更新潜在客户对象上的单个自定义字段。 大多数情况下，使用Marketo Engage UI执行的字段更新操作可通过API实现。 下表总结了几项差异。

<table>
<tbody>
<tr>
<td style="width: 26.5306%;" rowspan="2"><strong>属性</strong></td>
<td style="width: 35%;" colspan="2"><strong>标准字段</strong></td>
<td style="width: 38.2654%;" colspan="2"><strong>自定义字段</strong></td>
</tr>
<tr>
<td style="width: 17.449%;"><strong>可由API更新？</strong></td>
<td style="width: 17.551%;"><strong>可通过UI更新？</strong></td>
<td style="width: 19.3878%;"><strong>可由API更新？</strong></td>
<td style="width: 18.8776%;"><strong>可通过UI更新？</strong></td>
</tr>
<tr>
<td style="width: 26.5306%;">数据类型</td>
<td style="width: 17.449%;">否</td>
<td style="width: 17.551%;">否</td>
<td style="width: 19.3878%;">否</td>
<td style="width: 18.8776%;">是</td>
</tr>
<tr>
<td style="width: 26.5306%;">描述</td>
<td style="width: 17.449%;">是</td>
<td style="width: 17.551%;">是</td>
<td style="width: 19.3878%;">是</td>
<td style="width: 18.8776%;">是</td>
</tr>
<tr>
<td style="width: 26.5306%;">显示名称</td>
<td style="width: 17.449%;">否</td>
<td style="width: 17.551%;">否</td>
<td style="width: 19.3878%;">是</td>
<td style="width: 18.8776%;">是</td>
</tr>
<tr>
<td style="width: 26.5306%;">isCustom</td>
<td style="width: 17.449%;">否</td>
<td style="width: 17.551%;">否</td>
<td style="width: 19.3878%;">否</td>
<td style="width: 18.8776%;">否</td>
</tr>
<tr>
<td style="width: 26.5306%;">isHidden</td>
<td style="width: 17.449%;">否</td>
<td style="width: 17.551%;">是</td>
<td style="width: 19.3878%;">是（如果由API创建）</td>
<td style="width: 18.8776%;">是</td>
</tr>
<tr>
<td style="width: 26.5306%;">isHtmlEncodingInEmail</td>
<td style="width: 17.449%;">是</td>
<td style="width: 17.551%;">是</td>
<td style="width: 19.3878%;">是</td>
<td style="width: 18.8776%;">是</td>
</tr>
<tr>
<td style="width: 26.5306%;">isSensitive</td>
<td style="width: 17.449%;">是</td>
<td style="width: 17.551%;">是</td>
<td style="width: 19.3878%;">是</td>
<td style="width: 18.8776%;">是</td>
</tr>
<tr>
<td style="width: 26.5306%;">length</td>
<td style="width: 17.449%;">否</td>
<td style="width: 17.551%;">否</td>
<td style="width: 19.3878%;">否</td>
<td style="width: 18.8776%;">否</td>
</tr>
<tr>
<td style="width: 26.5306%;">name</td>
<td style="width: 17.449%;">否</td>
<td style="width: 17.551%;">否</td>
<td style="width: 19.3878%;">否</td>
<td style="width: 18.8776%;">否</td>
</tr>
</tbody>
</table>

所需的`fieldApiName`路径参数指定要更新的字段的API名称。 所需的输入参数是一个包含单个潜在客户字段对象的数组。  字段对象包含一个或多个属性。

### 请求

```
POST /rest/v1/leads/schema/fields/{fieldApiName}.json
```

### 正文

```json
{
  "input": [
      {
        "displayName": "Acme Access Code",
        "description": "Acme Direct Mail Integration",
        "isHtmlEncodingInEmail": true
      }
  ]
}
```

### 响应

```json
{
    "requestId": "9f57#1794324f44c",
    "result": [
        {
            "name": "acmeAccessCode",
            "status": "updated"
        }
    ],
    "success": true
}
```

## 将潜在客户推送到Marketo

推送商机是同步到Marketo商机的一种替代方法，它的主要功能是允许比标准同步商机具有更大的触发能力(与Marketo表单的使用方式类似)。 除了同步潜在客户字段之外，此端点还允许根据传递到端点的Cookie值关联潜在客户。 此操作可通过以下方式完成：传递通过单击Marketo电子邮件生成的`mkt_tok`值，或在调用中传递项目名称。 此端点还会创建单个可触发活动，该活动与Marketo中的项目和/或营销策划相关联。 这允许触发归因于特定活动或项目的商机捕获事件，以从Marketo中启动关联的工作流。

Push Lead界面与Sync Lead非常相似。 所有相同的主键均有效，字段使用相同的API名称（没有操作参数，因为这始终是更新插入操作）。 `programName`和输入参数是必需的，`lookupField`、`source`和`reason`参数是可选的。 输入参数是lead对象的数组。 生成的活动将归属于相应的命名项目。 `source`和`reason`参数是任意字符串字段，可添加到请求以将这些值嵌入生成的活动。 这些限制可以用作相应触发器(将潜在客户推送到Marketo)和过滤器(将潜在客户推送到Marketo)中的约束。

有关匿名活动的注释。 如果要将以前的匿名活动与新创建的潜在客户关联，则不要在潜在客户对象中指定Cookie属性，并在推送潜在客户之后调用“关联潜在客户”。 如果要创建一个没有活动历史记录的新潜在客户，则只需在潜在客户对象中指定Cookie属性即可。

### 请求

```
POST /rest/v1/leads/push.json
```

### 正文

```json
{
    "programName": "Big Blue Thing Product Launch",
    "source": "Cool Sales Site",
    "reason": "Downloaded pricing sheet",
    "lookupField": "email",
    "input": [
        {
             "email": "Theresa.May@westminister.gov.uk",
             "country": "united kingdom",
             "firstName": "Theresa",
             "website": "www.brexit.com",
             "leadScore": 45,
             "marketoSocialFacebookProfileURL": "http://www.facebook.com/id/23434456",
             "jobTitle": "Prime Minister"
         },
         {
             "email": "Justin.Trudeau@ottowa.gov.ca",
             "country": "canada",
             "firstName": "Justin",
             "website": "www.take-off-eh.com",
             "leadScore": 92,
             "marketoSocialFacebookProfileURL": "http://www.facebook.com/id/42434",
             "jobTitle": "Sonny"
         }
     ]
}
```

### 响应

```json
{
    "requestId": "939079529805",
    "success": true,
    "warnings": [],
    "result": [
       {
           "id": 483894,
           "status": "created"
       },
       {
           "id": 1087425,
           "status": "updated"
       },
       {
           "id": 3525,
           "reasons": [
                    {
                        "code": "501",
                        "message": "Bad stuff happened"
                    }
           ]
       }
    ]
}
```

要传递`mkt_tok`参数，请将该值分配给输入参数中的潜在客户记录中的mktToken成员，如下所示。

### 正文

```json
{
  "programName": "Big Blue Thing Product Launch",
  "source": "Cool Sales Site",
  "reason": "Downloaded pricing sheet",
  "lookupField": "mktToken",
  "input" : [
     {
       "mktToken" : "<tokenValue>",
       "firstName" : "Thelma"
     },
     {
       "mktToken" : "<tokenValue>",
       "firstName" : "Louise"
     }
   ]
}
```

## 提交表单

提交表单是将潜在客户同步到Marketo的替代方法，旨在提供等同于Marketo表单提交的功能。 这允许触发归因于特定活动或项目的商机捕获事件，以从Marketo中启动关联的工作流。

提交表单端点支持以下功能：

* 使用电子邮件字段作为主键更新插入潜在客户记录
* 创建与项目和/或营销策划关联的“填写表单”活动
* 允许基于Cookie值的商机关联
* 执行表单字段验证

提交表单时遵循标准潜在客户数据库模式。 单个对象记录在POST请求的JSON正文的必需输入成员中传递。 所需的`formId`成员包含目标Marketo表单ID。

可选的`programId`可用于指定要向其添加潜在客户的程序，和/或指定要向其添加程序成员自定义字段的程序。 如果提供了`programId`，则商机将添加到项目群，并且表单中存在的所有项目群成员字段也会被添加。 请注意，指定的程序必须与表单位于同一工作区中。 如果表单不包含项目群成员自定义字段并且未提供`programId`，则潜在客户不会添加到项目群。 如果表单驻留在程序中，但未提供`programId`，则当表单中存在一个或多个程序成员自定义字段时，将使用该程序。

在输入记录中，`leadFormFields`对象是必需的。 此对象包含一个或多个与要填充的表单字段对应的名称/值对。  所有指定的字段必须在指定的表单中定义。 该名称是字段的REST API名称。 请注意，`email`字段为必填项。

`visitorData`成员对象是可选的，包含与页面访问数据（包括`pageURL`、`queryString`、`leadClientIpAddress`和`userAgentString`）对应的名称/值对。 可用于填充其他活动字段以进行筛选和触发。

Cookie成员字符串是可选的，用于将Munchkin Cookie与Marketo中的人员记录相关联。 创建新潜在客户时，任何以前的匿名活动都会与该潜在客户关联，除非该Cookie值以前曾与其他已知记录关联。 如果以前关联过Cookie值，则系统会根据记录跟踪新活动，但不会从现有已知记录迁移旧活动。 要创建没有活动历史记录的新潜在客户，只需忽略Cookie成员。

在窗体所在的工作区的主分区中创建新的潜在客户。

### 请求

```
POST /rest/v1/leads/submitForm.json
```

### 标头

```
Content-Type: application/json
```

### 正文

```json
{
  "formId": 1029,
  "input": [
    {
      "leadFormFields": {
        "firstName": "Marge",
        "lastName": "Simpson",
        "email": "marge.simpson@fox.com",
        "pMCFField": "PMCF value"
      },
      "visitorData": {
        "pageURL": "https://na-sjst.marketo.com/lp/063-GJP-217/UnsubscribePage.html",
        "queryString": "Unsubscribed=yes",
        "leadClientIpAddress": "192.150.22.5",
        "userAgentString": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/84.0.4147.89 Safari/537.36"
      },
      "cookie": "id:063-GJP-217&token:_mch-marketo.com-1594662481190-60776"
    }
  ]
}
```

### 响应

```json
{
  "requestId": "10667#173bc585ca5",
  "result": [
    {
      "id": 319174,
      "status": "updated"
    }
  ],
  "success": true
}
```

在这里，我们可以从Marketo Engage UI中看到相应的“填写表单”活动详细信息：

![填写表单UI](assets/fill_out_form_activity_details.png)

## 合并

>[!NOTE]
>从2026年3月31日开始，在合并潜在客户API调用的`leadIds`参数中包含超过25个ID的调用将导致1080错误代码，并且将跳过该调用。 需要将超过25条记录合并为一个的工作应该被分割成多个工作以确保这些调用成功。
>

有时候，合并重复记录是必要的，Marketo通过合并潜在客户API为此提供了便利。 合并潜在客户将合并其活动日志、项目、营销策划、列表成员资格和CRM信息，并将其所有字段值合并到单个记录中。 合并潜在客户将潜在客户ID作为路径参数，单个`leadId`作为查询参数，或者`leadIds`参数中逗号分隔的ID为25个或更少的列表


### 请求

```
POST /rest/v1/leads/{id}/merge.json?leadId=1324
```

### 响应

```json
{
   "requestId":"e42b#14272d07d78",
   "success":true
}
```

在path参数中指定的商机是入选商机，因此，如果合并的记录之间有任何字段冲突，则采用来自入选者的值，但入选记录中的字段为空且失败记录中的对应字段为空的除外。 在`leadId`或`leadIds`参数中指定的潜在客户是失败的潜在客户。

如果您具有启用SFDC同步的订阅，则还可以在请求中使用`mergeInCRM`参数。 如果设置为true，则还会在CRM中执行相应的合并。 如果两个潜在客户都在SFDC中，其中一个是CRM潜在客户，而另一个是CRM联系人，则入选者将成为CRM联系人（无论哪个潜在客户被指定为入选者）。 如果其中一个潜在客户位于SFDC中，而另一个仅位于Marketo中，则入选者将成为SFDC潜在客户（无论将哪个潜在客户指定为入选者）。

## 关联Web活动

通过潜在客户跟踪(Munchkin)，Marketo记录网站和Marketo登陆页面访客的Web活动。 这些活动（访问和点击）使用与在潜在客户浏览器中设置的“_mkto_trk”Cookie对应的键进行记录，Marketo将使用此键来跟踪同一人员的活动。 通常，当潜在客户从Marketo电子邮件点击进来或填写Marketo表单时，就会发生与潜在客户记录的关联，但有时关联可能由其他类型的事件触发，为此，您可以使用关联潜在客户端点来执行。 端点将已知潜在客户记录的ID作为路径参数，并在Cookie查询参数中采用“_mkto_trk”Cookie值。

### 请求

```
POST /rest/v1/leads/{id}/associate.json?cookie=id:287-GTJ-838%26token:_mch-marketo.com-1396310362214-46169
```

### 响应

```json
{
   "requestId":"e42b#14272d07d78",
   "success":true
}
```

如果某个Cookie已经与某个已知潜在客户记录关联，则在不同潜在客户记录上使用此API会导致根据该记录记录记录记录新的Web活动，但不会将任何现有Web活动移至新记录。
会员资格

还可以根据静态列表或项目中的成员资格来检索潜在客户记录。 此外，您可以检索潜在客户所属的所有静态列表、项目或智能营销策划。

响应结构和可选参数与“按过滤器类型获取潜在客户”的响应结构和可选参数相同，不过filterType和filterValues不能与此API一起使用。
要通过Marketo UI访问列表ID，请导航到列表。 列表`id`在静态列表`https://app-****.marketo.com/#ST1001A1`的URL中。 在此示例中，1001是列表的`id`。

### 请求

```
GET /rest/v1/list/{listId}/leads.json?batchSize=3
```

### 响应

```json
{
   "requestId":"e42b#14272d07d78",
   "success":true,
   "nextPageToken":
"PS5VL5WD4UOWGOUCJR6VY7JQO2KUXL7BGBYXL4XH4BYZVPYSFBAONP4V4KQKN4SSBS55U4LEMAKE6===",
    "result":[
       {
            "id":50,
            "email":"kjashaedd@klooblept.com",
            "firstName":"Kataldar",
             "postalCode":"04828"
       },
       {
           "id":2343,
           "email":"kjashaedd@klooblept.com",
           "firstName":"Kataldar",
           "postalCode":"04828"
       },
      {
           "id":88498,
           "email":"kjashaedd@klooblept.com",
           "firstName":"Kataldar",
         "postalCode":"04828"
         }
    ]
}
```

按潜在客户ID获取列表端点采用潜在客户记录`id`路径参数，并返回该潜在客户所属的所有静态列表记录。

### 请求

```
GET /rest/v1/leads/{id}/listMembership.json?batchSize=3
```

### 响应

```json
{
    "requestId": "1184b#1706f0ec23f",
    "result": [
        {
            "listId": 3379,
            "createdAt": "2016-05-17T19:32:44Z",
            "updatedAt": "2016-05-17T19:32:44Z"
        },
        {
            "listId": 2792,
            "createdAt": "2009-05-19T18:29:15Z",
            "updatedAt": "2009-05-19T18:29:15Z"
        },
        {
            "listId": 42,
            "createdAt": "2009-04-22T19:24:22Z",
            "updatedAt": "2009-04-22T19:24:22Z"
        }
    ],
    "success": true,
    "nextPageToken": "BFRV7OMVSNJWDVKVTUFS3XHT4E======",
    "moreResult": true
}
```

## 项目

计划会员资格可采用与列表类似的方式检索。 在调用Get Leads by Program ID端点并传递`programId`路径参数时，可以使用相同的可选请求参数。

或者，您可以传递一个字段参数，其中包含要返回的以逗号分隔的字段名称列表。 如果此请求中未包含字段参数，则将返回以下默认字段：`email`、`updatedAt`、`createdAt`、`lastName`、`firstName`、`membership`和`id`。 在请求字段列表时，如果请求的是特定字段但未返回，则该值默认为空。

响应结构非常相似，因为结果数组中的每个项都是一个潜在客户，只是每个记录还具有一个名为“membership”的子对象。 此成员资格对象包括有关潜在客户与调用中指示的程序的关系的数据，始终显示其`progressionStatus`、`acquiredBy`、`reachedSuccess`和`membershipDate`。 如果父计划也是参与计划，则成员资格将具有成员`stream`、`nurtureCadence`和`isExhausted`，以指示其在参与计划中的位置和活动。

### 请求

```
GET /rest/v1/leads/programs/{programId}.json?batchSize=3
```

### 响应

```json
{
    "requestId": "13ad4#1727b748a17",
    "result": [
        {
            "id": 319141,
            "firstName": "Meera",
            "lastName": "Reed",
            "email": "mree@housestark.com",
            "updatedAt": "2020-04-21T16:27:14Z",
            "createdAt": "2020-04-21T16:27:14Z",
            "membership": {
                "id": 1127,
                "progressionStatus": "Visited",
                "progressionStatusType": "Visited",
                "isExhausted": false,
                "acquiredBy": true,
                "reachedSuccess": false,
                "membershipDate": "2020-04-21T16:27:16Z",
                "updatedAt": "2020-04-21T16:27:16Z"
            }
        },
        {
            "id": 319142,
            "firstName": "Jon",
            "lastName": "Umber",
            "email": "jumb@housestark.com",
            "updatedAt": "2020-04-21T16:27:14Z",
            "createdAt": "2020-04-21T16:27:14Z",
            "membership": {
                "id": 1127,
                "progressionStatus": "Visited",
                "progressionStatusType": "Visited",
                "isExhausted": false,
                "acquiredBy": true,
                "reachedSuccess": false,
                "membershipDate": "2020-04-21T16:27:16Z",
                "updatedAt": "2020-04-21T16:27:16Z"
            }
        },
        {
            "id": 319143,
            "firstName": "Lyanna",
            "lastName": "Mormont",
            "email": "lmor@housestark.com",
            "updatedAt": "2020-04-21T16:27:14Z",
            "createdAt": "2020-04-21T16:27:14Z",
            "membership": {
                "id": 1127,
                "progressionStatus": "Visited",
                "progressionStatusType": "Visited",
                "isExhausted": false,
                "acquiredBy": true,
                "reachedSuccess": false,
                "membershipDate": "2020-04-21T16:27:16Z",
                "updatedAt": "2020-04-21T16:27:16Z"
            }
        }
    ],
    "success": true,
    "nextPageToken": "SW3PTMBVFCNHSHJGZ7LQH3ZWNUOHKADJZ3MOQ2LOZZVNO3WEIUPDKPRTTHBSMW756KOCWURTOF2XS==="
}
```

“按潜在客户ID获取程序”端点采用潜在客户记录ID路径参数，并返回该潜在客户所属的所有程序记录。 可选的`filterType`和`filterValues`参数允许您根据程序ID进行筛选。

### 请求

```
GET /rest/v1/leads/{id}/programMembership.json
```

### 响应

```json
{
    "requestId": "12e84#1706f13a379",
    "result": [
        {
            "id": 1044,
            "progressionStatus": "Sent",
            "isExhausted": false,
            "acquiredBy": false,
            "reachedSuccess": false,
            "membershipDate": "2016-05-27T19:50:29Z",
            "updatedAt": "2016-05-27T19:50:29Z"
        }
    ],
    "success": true,
    "moreResult": false
}
```

## 智能营销活动

按商机ID获取智能营销活动端点采用商机记录ID路径参数，并返回该商机所属的所有智能营销活动记录。

### 请求

```
GET /rest/v1/leads/{id}/smartCampaignMembership.json?batchSize=3
```

### 响应

```json
{
    "requestId": "e7b0#1706f163632",
    "result": [
        {
            "smartCampaignId": 3746,
            "createdAt": "2018-06-01T18:00:04Z",
            "updatedAt": "2018-06-01T18:00:06Z"
        },
        {
            "smartCampaignId": 3678,
            "createdAt": "2015-04-06T18:37:30Z",
            "updatedAt": "2015-04-06T18:37:41Z"
        },
        {
            "smartCampaignId": 3680,
            "createdAt": "2015-04-06T18:37:30Z",
            "updatedAt": "2015-04-06T18:37:40Z"
        }
    ],
    "success": true,
    "nextPageToken": "TNGAH3NKDUFDHNXUVGTNBXJCQM======",
    "moreResult": true
}
```

## 删除

使用“删除潜在客户”端点可以直接删除潜在客户。  使用正文中的id属性指定要删除的潜在客户id。  每个请求的最大商机为300个。  使用Content-Type： application/json标头。

### 请求

```
POST /rest/v1/leads/delete.json
```

### 正文

```json
{
   "input":[
      {
         "id": 235
      },
      {
         "id":766
      }
   ]
}
```

### 响应

```json
{
  "requestId":"3608#16664333670",
  "result":[
    {
      "id":235,
      "status":"deleted"
    },
    {
      "id":766,
      "status":"deleted"
    }
  ],
  "success":true
}
```

## 关系

* 潜在客户记录中通过externalCompanyId字段列出的公司
* 潜在客户记录上的SalesPerson through externalSalesPersonId字段
* 通过计划成员资格的计划
* 通过列表成员资格列出的列表
* 通过活动中的leadId字段进行的活动
* 通过潜在客户记录中的单个区段字段进行分段
* 在潜在客户记录中通过leadPartitionId进行分区

## 超时

潜在客户端点具有30秒的超时时间，除非在下文中说明：

* 同步潜在客户：90秒
* 关联潜在客户：60多岁
* 合并潜在客户：180秒
* 更新潜在客户分区： 60秒
* 将潜在客户推送到Marketo：90多岁
* 按筛选器类型获取潜在客户：60秒
* 按列表ID获取潜在客户：60秒
