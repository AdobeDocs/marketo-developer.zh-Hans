---
title: 批量程序成员导入
feature: REST API
description: 了解如何使用小于10MB、队列限制、所需参数和轮询作业状态的CSV TSV或SSV文件，通过Marketo REST API批量导入项目成员。
exl-id: b0e1039a-fe9b-4fb7-9aa6-9980a06da673
TQID: https://experienceleague.adobe.com/T1PAzLN1mnp38kJ0jwh6kPv6r1Uvxc7-o9zeTHetIV0
product_v2: id: b27e5950-9033-45ac-9f86-eb22e567f615
feature_v2: id: e2290edd-b061-4880-9d79-dee306cf5aa9
role_v2: id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
topic_v2: id: b5ce8718-c3af-4fdb-a1a9-fca32f83a87c
source-git-commit: 3e6d310c5aec1a3435424fb122b71d825db5af0e
workflow-type: tm+mt
source-wordcount: 771
ht-degree: 0%

---

# 批量程序成员导入

[批量程序成员导入终结点引用](https://developer.adobe.com/marketo-apis/api/mapi#tag/Bulk-Import-Program-Members)

使用[批量API](https://developer.adobe.com/marketo-apis/api/mapi#tag/Bulk-Import-Program-Members)异步导入大量程序成员记录。 以逗号、制表符或分号分隔的平面文件提供小于10 MB的记录。

批量程序成员导入仅支持“插入或更新”记录操作。

## 处理限制

每个批量导入请求都将作为作业添加到先进先出(FIFO)队列。 以下限制适用：

- 最多可同时处理两个作业。
- 队列中最多可以有10个作业，包括正在处理的两个作业。

如果超过10个作业的最大值，则API返回`1016, Too many imports`错误。

## 导入文件

文件的第一行必须是标头，该标头列出了每行中的值所映射到的REST API字段名称。 使用[Describe Lead](https://developer.adobe.com/marketo-apis/api/mapi#tag/Leads/operation/describeUsingGET_2)和[Describe Program Member](https://developer.adobe.com/marketo-apis/api/mapi#tag/Leads/operation/describeProgramMemberUsingGET)端点检索这些名称。

记录可以包含潜在客户字段、自定义潜在客户字段和自定义计划成员字段。

典型文件遵循以下模式：

```text
email,firstName,lastName
test@example.com,John,Doe
```

使用`multipart/form-data`内容类型发送请求。 使用现有的库实现来构造多部分请求。

## 创建作业

[导入程序成员](https://developer.adobe.com/marketo-apis/api/mapi#tag/Bulk-Import-Program-Members/operation/importProgramMemberUsingPOST)终结点从文件中读取程序成员记录，并将其添加到具有指定状态的程序中。 记录可以包含潜在客户字段和自定义项目群成员字段。

每个记录都必须包含用于重复数据删除的email字段。

`programId` path参数指定向其中添加成员的程序。

该请求需要三个查询参数：

- `format`：导入文件格式（`CSV`、`TSV`或`SSV`）。
- `programMemberStatus`：分配给导入成员的项目群状态。
- `file`：包含程序成员记录的文件的名称。

```http
POST /bulk/v1/program/{programId}/members/import.json?format=csv&programMemberStatus=On List
```

```text
Content-Type: multipart/form-data; boundary=--------------------------118046853683028616211319
Content-Length: 772
Host: <munchkinId>.mktorest.com
```

```text
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

由于终结点是异步的，响应包含`batchId`和`status`字段。 状态可以是`Queued`、`Importing`或`Failed`。

保留`batchId`以检查导入状态并在完成之后检索失败或警告。 `batchId`的有效期为七天。

以下命令行cURL请求提交示例作业：

```bash
curl -i -F format='csv' -F programMemberStatus='On List' -F file='@Lead-House-Lannister.csv' -F access_token='<Access Token>' <REST API Endpoint Base URL>/bulk/v1/program/{programId}/members/import.json
```

在此示例中，`Lead-House-Lannister.csv`导入文件包含以下数据：

```text
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

创建导入作业后，每5-30秒轮询一次。 将`batchId`路径参数传递到[获取导入程序成员状态](https://developer.adobe.com/marketo-apis/api/mapi#tag/Bulk-Import-Program-Members/operation/getImportProgramMemberStatusUsingGET)终结点。

```http
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

此响应显示已完成的导入。 状态可以是`Complete`、`Queued`、`Importing`或`Failed`。

作业完成后，响应将列出已处理、失败和已处理但出现警告的行数。 当状态为`Failed`时，`message`参数也可以提供失败消息。

## 故障

[获取导入程序成员状态](https://developer.adobe.com/marketo-apis/api/mapi#tag/Bulk-Import-Program-Members/operation/getImportProgramMemberStatusUsingGET)响应中的`numOfRowsFailed`属性指示失败的行数。 值大于零表示发生故障。

将`batchId`路径参数传递到“获取导入程序成员失败”终结点，以检索失败记录及其原因。

```http
GET /bulk/v1/program/members/import/{batchId}/failures.json
```

端点返回一个标识每个失败行的文件，并解释记录失败的原因。 该文件在创建作业期间使用`format`参数指定的格式。 每个记录上的附加字段描述了失败。

例如，假设您导入的以下文件具有无效商机分数：

```text
firstName,lastName,email,title,company,leadScore
Aerys,Targaryen,Aerys@Targaryen.com,Targaryen,House Targaryen,TEXT_VALUE_IN_INTEGER_FIELD
```

作业状态返回`numOfRowsFailed`为1，表示发生了故障：

```http
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

检索故障文件以了解更多信息：

```http
GET /bulk/v1/program/members/import/{batchId}/failures.json
```

```text
firstName,lastName,email,title,company,leadScore,Import Failure Reason
Aerys,Targaryen,Aerys@Targaryen.com,Targaryen,House Targaryen,TEXT_VALUE_IN_INTEGER_FIELD,Invalid data type in field Lead Score
```

## 警告

[获取导入程序成员状态](https://developer.adobe.com/marketo-apis/api/mapi#tag/Bulk-Import-Program-Members/operation/getImportProgramMemberStatusUsingGET)响应中的`numOfRowsWithWarning`属性指示带有警告的行数。 值大于零表示出现警告。

将`batchId`路径参数传递到[获取导入程序成员警告](https://developer.adobe.com/marketo-apis/api/mapi#tag/Bulk-Import-Program-Members/operation/getImportProgramMemberWarningsUsingGET)终结点，以检索受影响的记录及其原因。

```http
GET /bulk/v1/program/members/import/{batchId}/warnings.json
```

端点返回一个文件，该文件用警告标识每一行并解释警告发生的原因。 该文件在创建作业期间使用`format`参数指定的格式。 每个记录上的附加字段都描述了警告。

例如，假设您导入的以下文件具有无效的电子邮件地址：

```text
firstName,lastName,email,title,company,leadScore
Aerys,Targaryen,INVALID_EMAIL,Targaryen,House Targaryen,0
```

作业状态返回`numOfRowsWithWarning`为1，表示出现警告：

```http
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

检索警告文件以了解更多信息：

```http
GET /bulk/v1/program/members/import/{batchId}/warnings.json
```

```text
firstName,lastName,email,title,company,leadScore,Import Warning Reason
Aerys,Targaryen,INVALID_EMAIL,Targaryen,House Targaryen,0,Invalid email address
```
