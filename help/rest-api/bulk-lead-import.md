---
title: 批量商机导入
feature: REST API
description: 使用CSV TSV或SSV在Marketo中创建和监控异步批量潜在客户导入。
exl-id: 615f158b-35f9-425a-b568-0a7041262504
TQID: https://experienceleague.adobe.com/UamXYWis5J1ERqnp5lAnfUf3pFcgfSOLfKRXRB-Yg4I
product_v2: id: b27e5950-9033-45ac-9f86-eb22e567f615
feature_v2: id: e2290edd-b061-4880-9d79-dee306cf5aa9
role_v2: id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
topic_v2: id: b5ce8718-c3af-4fdb-a1a9-fca32f83a87c
source-git-commit: 3e6d310c5aec1a3435424fb122b71d825db5af0e
workflow-type: tm+mt
source-wordcount: 623
ht-degree: 0%

---

# 批量商机导入

[批量潜在客户导入终结点引用](https://developer.adobe.com/marketo-apis/api/mapi#tag/Bulk-Import-Leads)

使用[批量API](https://developer.adobe.com/marketo-apis/api/mapi#tag/Bulk-Import-Leads/operation/importLeadUsingPOST)异步导入大量潜在客户记录。 以逗号、制表符或分号分隔的平面文件提供小于10 MB的记录。

批量商机导入仅支持“插入或更新”记录操作。

## 处理限制

每个批量导入请求都将作为作业添加到先进先出(FIFO)队列。 以下限制适用：

- 最多可同时处理两个作业。
- 队列中最多可以有10个作业，包括正在处理的两个作业。

如果超过10个作业的最大值，则API返回`1016, Too many imports`错误。

## 导入文件

文件的第一行必须是标头，该标头列出了每行中的值所映射到的REST API字段。 典型文件遵循以下模式：

```csv
email,firstName,lastName
test@example.com,John,Doe
```

使用`externalCompanyId`将潜在客户记录链接到公司记录。 使用`externalSalesPersonId`将潜在客户记录链接到销售人员记录。

使用`multipart/form-data`内容类型发送请求。 使用现有的库实现来构造多部分请求。

## 创建作业

要创建批量导入作业，请将内容类型设置为`multipart/form-data`并包括以下参数：

- `file`：导入文件内容。
- `format`：文件格式。 有效值为`csv`、`tsv`和`ssv`。

```http
POST /bulk/v1/leads.json?format=csv
```

```text
Content-Type: multipart/form-data; boundary=------WebKitFormBoundaryBQACkJZyaiIAXogC
Content-Length: 311
Host: <munchkinId>.mktorest.com
```

```text
------WebKitFormBoundaryBQACkJZyaiIAXogC
Content-Disposition: form-data; name="file"; filename="leads.csv"
Content-Type: text/csv

firstName,lastName,email,company
Able,Baker,ablebaker@marketo.com,Marketo
Charlie,Dog,charliedog@marketo.com,Marketo
Easy,Fox,easyfox@marketo.com,Marketo
------WebKitFormBoundaryBQACkJZyaiIAXogC--
```

```json
{
    "requestId": "d01f#15d672f8560",
    "result": [
        {
            "batchId": 3404,
            "importId": "3404",
            "status": "Queued"
        }
    ],
    "success": true
}
```

此终结点使用[multipart/form-data作为content-type](https://www.w3.org/Protocols/rfc1341/7_2_Multipart.html)。 为您的首选语言使用HTTP支持库以正确构建请求。 以下示例使用命令行中的cURL：

```bash
curl -i -F format=csv -F file=@lead_data.csv -F access_token=<Access Token> <REST API Endpoint Base URL>/bulk/v1/leads.json
```

在此示例中，`lead_data.csv`导入文件包含以下数据：

```text
firstName,lastName,email,company
Able,Baker,ablebaker@marketo.com,Marketo
Charlie,Dog,charliedog@marketo.com,Marketo
Easy,Fox,easyfox@marketo.com,Marketo
```

您还可以包含以下可选参数：

- `lookupField`：选择用于删除重复项的字段，默认值为`email`。 指定`id`以执行“仅更新”操作。
- `listId`：选择静态列表。 除了导入创建或更新的任何记录外，导入的潜在客户还会成为此列表的成员。
- `partitionName`：选择要导入的分区。 有关更多信息，请参阅工作区和分区部分。

由于API是异步的，响应包含`batchId`和`status`字段，而不是单个成功和失败。 状态可以是`Queued`、`Importing`或`Failed`。

保留`batchId`以检查作业状态并在完成之后检索失败或警告。 `batchId`的有效期为七天。

## 轮询作业状态

使用获取导入商机状态API轮询作业（每5-30秒一次），具体取决于延迟要求和API调用限制。

```http
GET /bulk/v1/leads/batch/{id}.json
```

```json
{
   "requestId":"8136#146daebc2ed",
   "success":true,
   "result":[
      {
         "batchId":1022,
         "status":"Complete",
         "numOfLeadsProcessed":2,
         "numOfRowsFailed":1,
         "numOfRowsWithWarning":0,
         "message":"Import completed with errors, 2 records imported (2 members), 1 failed"
      }
   ]
}
```

此响应显示已完成的导入。 状态可以是以下值之一：

- 完成
- 已排队
- 正在导入
- 失败

作业完成后，响应将列出已处理、失败和已处理但出现警告的行数。 当状态为`Failed`时，`message`参数也可以提供失败消息。

## 故障

获取导入商机状态响应中的`numOfRowsFailed`属性指示失败的行数。 值大于零表示发生故障。

要检索失败记录及其原因，请请求失败文件：

```http
GET /bulk/v1/leads/batch/{id}/failures.json
```

该API会返回一个文件，该文件可标识每个失败的行，并解释记录失败的原因。 该文件在创建作业期间使用`format`参数指定的格式。 每个记录上的附加字段描述了失败。

## 警告

获取导入商机状态响应中的`numOfRowsWithWarning`属性指示带有警告的行数。 值大于零表示出现警告。

要检索受影响的记录及其原因，请请求警告文件：

```http
GET /bulk/v1/leads/batch/{id}/warnings.json
```

该API返回一个文件，该文件使用警告标识每一行，并解释出现警告的原因。 该文件在创建作业期间使用`format`参数指定的格式。 每个记录上的附加字段都描述了警告。
