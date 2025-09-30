---
title: 批量程序成员导入
feature: REST API
description: 了解如何使用小于10MB、队列限制、所需参数和轮询作业状态的CSV TSV或SSV文件，通过Marketo REST API批量导入项目成员。
exl-id: b0e1039a-fe9b-4fb7-9aa6-9980a06da673
source-git-commit: 7557b9957c87f63c2646be13842ea450035792be
workflow-type: tm+mt
source-wordcount: '860'
ht-degree: 0%

---

# 批量程序成员导入

[批量程序成员导入终结点引用](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Bulk-Import-Program-Members)

对于大量程序成员记录，可以使用[批量API](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Bulk-Import-Program-Members)异步导入程序成员。 这样，您就可以使用带有分隔符（逗号、制表符或分号）的平面文件，将一系列记录导入Marketo。 文件可以包含任意数量的记录，只要文件总计小于10MB即可。 记录操作仅限“插入或更新”。

## 处理限制

您可以提交多个批量导入请求，但存在限制。 每个请求都将作为作业添加到要处理的FIFO队列。 最多可同时处理两个作业。 在任意给定时间（包括当前正在处理的2个），队列中最多允许10个作业。 如果超过十个作业的最大值，则会返回“1016，导入次数过多”错误。

## 导入文件

文件的第一行必须是标头，该标头将相应的REST API名称作为字段列出，以便将每行的值映射到其中。 可以使用[Describe Lead](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Leads/operation/describeUsingGET_2)和/或[Describe Program Member](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Leads/operation/describeProgramMemberUsingGET)端点检索REST API名称。 记录可以包含潜在客户字段、自定义潜在客户字段和自定义计划成员字段。

典型的文件将遵循以下基本模式：

```
email,firstName,lastName
test@example.com,John,Doe
```

调用本身是使用`multipart/form-data`内容类型进行的。

此请求类型可能难以实施，因此强烈建议您使用现有的库实施。

## 创建作业

[导入程序成员](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Bulk-Import-Program-Members/operation/importProgramMemberUsingPOST)终结点读取包含程序成员记录的文件，并将它们添加到具有给定状态的程序中。 记录可以同时包含潜在客户字段和项目群成员自定义字段。 所有记录都必须包含email字段，该字段用于重复数据删除目的。

`programId` path参数指定向其中添加成员的程序。

有三个必需的查询参数。 `format`参数指定导入文件格式（CSV、TSV或SSV），`programMemberStatus`参数为要添加到程序中的成员指定程序状态，而`file`参数包含包含包含程序成员记录的导入文件的名称。

```
POST /bulk/v1/program/{programId}/members/import.json?format=csv&programMemberStatus=On List
```

```
Content-Type: multipart/form-data; boundary=--------------------------118046853683028616211319
Content-Length: 772
Host: <munchkinId>.mktorest.com
```

```
----------------------------118046853683028616211319
Content-Disposition: form-data; name="file"; filename="Lead-House-Lannister.csv"
Content-Type: text/csv

firstName,lastName,email,title,company,leadScore
Joanna,Lannister,Joanna@Lannister.com,Lannister,House Lannister,0
Tywin,Lannister,Tywin@Lannister.com,Lannister,House Lannister,0
Cersei,Lannister,Cersei@Lannister.com,Lannister,House Lannister,0
Jamie,Lannister,Jamie@Lannister.com,Lannister,House Lannister,0
Tyrion,Lannister,Tyrion@Lannister.com,Lannister,House Lannister,0
Kevan,Lannister,Kevan@Lannister.com,Lannister,House Lannister,0
Dorna,Lannister,Dorna@Lannister.com,Lannister,House Lannister,0
Lancel,Lannister,Lancel@Lannister.com,Lannister,House Lannister,0

----------------------------118046853683028616211319--
```

```json
{
    "requestId": "17f4a#16f87f87325",
    "result": [
        {
            "batchId": 1040,
            "importId": "1040",
            "status": "Queued"
        }
    ],
    "success": true
}
```

请注意，在响应我们的调用时，结果数组中的记录有`batchId`和`status`字段。 由于此端点是异步的，因此它可以返回“已排队”、“正在导入”或“失败”状态。 您必须保留`batchId`才能获取导入作业的状态，并在完成时检索失败和/或警告。 `batchId`的有效期为七天。

使用上面的示例，调用端点的简单方法是从命令行使用cURL：

```bash
curl -i -F format='csv' -F programMemberStatus='On List' -F file='@Lead-House-Lannister.csv' -F access_token='<Access Token>' <REST API Endpoint Base URL>/bulk/v1/program/{programId}/members/import.json
```

其中，导入文件“Lead-House-Lannister.csv”包含以下内容：

```
firstName,lastName,email,title,company,leadScore
Joanna,Lannister,Joanna@Lannister.com,Lannister,House Lannister,0
Tywin,Lannister,Tywin@Lannister.com,Lannister,House Lannister,0
Cersei,Lannister,Cersei@Lannister.com,Lannister,House Lannister,0
Jamie,Lannister,Jamie@Lannister.com,Lannister,House Lannister,0
Tyrion,Lannister,Tyrion@Lannister.com,Lannister,House Lannister,0
Kevan,Lannister,Kevan@Lannister.com,Lannister,House Lannister,0
Dorna,Lannister,Dorna@Lannister.com,Lannister,House Lannister,0
Lancel,Lannister,Lancel@Lannister.com,Lannister,House Lannister,0
```

## 轮询作业状态

创建导入作业后，必须查询其状态。 最佳实践是每5-30秒轮询一次导入作业。 为此，请将`batchId`路径参数传递到[获取导入程序成员状态](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Bulk-Import-Program-Members/operation/getImportProgramMemberStatusUsingGET)终结点。

```
GET /bulk/v1/program/members/import/{batchId}/status.json
```

```json
{
    "requestId": "e0cb#16f87f8b177",
    "result": [
        {
            "batchId": 1040,
            "importId": "1040",
            "status": "Complete",
            "numOfLeadsProcessed": 8,
            "numOfRowsFailed": 0,
            "numOfRowsWithWarning": 0,
            "message": "Import succeeded, 8 records imported (8 members)"
        }
    ],
    "success": true
}
```

此响应显示已完成的导入。 状态可以是：“完成”、“已排队”、“导入”、“失败”之一。

如果作业已完成，则会列出已处理、失败或出现警告的行数。 如果状态为“失败”，则消息参数可能也会给出失败消息。

## 故障

`numOfRowsFailed`获取导入程序成员状态[响应中的](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Bulk-Import-Program-Members/operation/getImportProgramMemberStatusUsingGET)属性指示失败。 如果numOfRowsFailed大于零，则该值表示发生的失败次数。

使用“获取导入程序成员失败”端点通过传递`batchId`路径参数来检索失败行的记录和原因。

```
GET /bulk/v1/program/members/import/{batchId}/failures.json
```

终结点以一个文件作出响应，该文件指示哪些行失败，同时显示一条消息，指示记录失败的原因。 文件格式与作业创建期间在`format`参数中指定的格式相同。 附加字段将附加到每个记录中并包含失败描述。

例如，假设您导入的以下文件具有无效商机分数：

```
firstName,lastName,email,title,company,leadScore
Aerys,Targaryen,Aerys@Targaryen.com,Targaryen,House Targaryen,TEXT_VALUE_IN_INTEGER_FIELD
```

当您检查作业状态时，您看到`numOfRowsFailed`为1，这表示发生了故障：

```
GET /bulk/v1/program/members/import/{batchId}/status.json
```

```json
{
    "requestId": "4c2d#16f8b32c8ef",
    "result": [
        {
            "batchId": 1046,
            "importId": "1046",
            "status": "Complete",
            "numOfLeadsProcessed": 0,
            "numOfRowsFailed": 1,
            "numOfRowsWithWarning": 0,
            "message": "Import completed with errors, 0 records imported (0 members), 1 failed"
        }
    ],
    "success": true
}
```

然后，检索故障文件以获取有关故障的其他详细信息：

```
GET /bulk/v1/program/members/import/{batchId}/failures.json
```

```
firstName,lastName,email,title,company,leadScore,Import Failure Reason
Aerys,Targaryen,Aerys@Targaryen.com,Targaryen,House Targaryen,TEXT_VALUE_IN_INTEGER_FIELD,Invalid data type in field Lead Score
```

## 警告

`numOfRowsWithWarning`获取导入程序成员状态[响应中的](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Bulk-Import-Program-Members/operation/getImportProgramMemberStatusUsingGET)属性指示警告。 如果`numOfRowsWithWarning`大于零，则该值表示发生的警告数。

使用[获取导入程序成员警告](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Bulk-Import-Program-Members/operation/getImportProgramMemberWarningsUsingGET)终结点通过传递`batchId`路径参数来检索记录以及警告行的原因。

```
GET /bulk/v1/program/members/import/{batchId}/warnings.json
```

终结点以一个文件作出响应，该文件指示哪些行生成了警告，同时还会显示一条消息，指示记录生成警告的原因。 文件格式与作业创建期间在`format`参数中指定的格式相同。 附加字段将附加到每个记录并附有警告说明。

例如，假设您导入的以下文件具有无效的电子邮件地址：

```
firstName,lastName,email,title,company,leadScore
Aerys,Targaryen,INVALID_EMAIL,Targaryen,House Targaryen,0
```

当您检查作业状态时，您看到`numOfRowsWithWarning`为1，这表示出现了警告：

```
GET /bulk/v1/program/members/import/{batchId}/status.json
```

```json
{
   "requestId":"4ca1#16f883c2003",
   "result":[
      {
         "batchId":1041,
         "importId":"1041",
         "status":"Complete",
         "numOfLeadsProcessed":1,
         "numOfRowsFailed":0,
         "numOfRowsWithWarning":1,
         "message":"Import succeeded, 1 records imported (1 members), 1 warning."
      }
   ],
   "success":true
}
```

然后，可检索警告文件以获取有关警告的其他详细信息：

```
GET /bulk/v1/program/members/import/{batchId}/warnings.json
```

```
firstName,lastName,email,title,company,leadScore,Import Warning Reason
Aerys,Targaryen,INVALID_EMAIL,Targaryen,House Targaryen,0,Invalid email address
```
