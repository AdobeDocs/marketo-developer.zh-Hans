---
title: 批量商机导入
feature: REST API
description: 批量导入潜在客户数据。
exl-id: 615f158b-35f9-425a-b568-0a7041262504
source-git-commit: 66add4c38d0230c36d57009de985649bb67fde3e
workflow-type: tm+mt
source-wordcount: '803'
ht-degree: 0%

---

# 批量商机导入

[批量潜在客户导入终结点引用](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Bulk-Import-Leads)

对于大量潜在客户记录，可以使用[批量API](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Bulk-Import-Leads/operation/importLeadUsingPOST)异步导入潜在客户。 这样，您就可以使用带有分隔符（逗号、制表符或分号）的平面文件，将一系列记录导入Marketo。 文件可以包含任意数量的记录，只要文件总计小于10MB即可。 记录操作仅限“插入或更新”。

## 处理限制

您可以提交多个批量导入请求，但存在限制。 每个请求都将作为作业添加到要处理的FIFO队列。 最多可同时处理两个作业。 在任意给定时间（包括当前正在处理的2个），队列中最多允许10个作业。 如果超过十个作业的最大值，则会返回“1016，导入次数过多”错误。

## 导入文件

文件的第一行必须是标头，其中列出了要将每行的值映射到其中的相应REST API字段。 典型的文件将遵循以下基本模式：

```
email,firstName,lastName
test@example.com,John,Doe
```

`externalCompanyId`字段可用于将潜在客户记录链接到公司记录。 `externalSalesPersonId`字段可用于将潜在客户记录链接到销售人员记录。

调用本身是使用`multipart/form-data`内容类型进行的。

此请求类型可能难以实施，因此强烈建议您使用现有的库实施。

## 创建作业

要进行批量导入请求，必须将内容类型标头设置为“multipart/form-data”，并至少包含一个包含文件内容的文件参数，以及一个格式参数，其值为“csv”、“tsv”或“ssv”，表示您的文件格式。

```
POST /bulk/v1/leads.json?format=csv
```

```
Content-Type: multipart/form-data; boundary=------WebKitFormBoundaryBQACkJZyaiIAXogC
Content-Length: 311
Host: <munchkinId>.mktorest.com
```

```
------WebKitFormBoundaryBQACkJZyaiIAXogC
Content-Disposition: form-data; name="file"; filename="leads.csv"
Content-Type: text/csv

FirstName,LastName,Email,Company
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

此终结点使用[multipart/form-data作为content-type](https://www.w3.org/Protocols/rfc1341/7_2_Multipart.html)。 这很难做到，因此最佳实践是为您选择的语言使用HTTP支持库。 通过命令行中的cURL可轻松实现这一点，如下所示：

```
curl -i -F format=csv -F file=@lead_data.csv -F access_token=<Access Token> <REST API Endpoint Base URL>/bulk/v1/leads.json
```

其中，导入文件“lead_data.csv”包含以下内容：

```
FirstName,LastName,Email,Company
Able,Baker,ablebaker@marketo.com,Marketo
Charlie,Dog,charliedog@marketo.com,Marketo
Easy,Fox,easyfox@marketo.com,Marketo
```

您还可以选择在请求中包含`lookupField`、`listId`和`partitionName`参数。 `lookupField`允许您选择要删除重复项的特定字段（如“同步潜在客户”），并默认为电子邮件。 您可以将`id`指定为`lookupField`以指示“仅更新”操作。 `listId`允许您选择静态列表以导入潜在客户列表；这将导致列表中的潜在客户成为此静态列表的成员，以及导入导致的任何创建或更新。 `partitionName`选择要导入到的特定分区。 有关更多信息，请参阅工作区和分区部分。

请注意，在响应我们的调用时，没有同步潜在客户之类的成功或失败列表，而是结果数组中记录的batchId和status字段。 这是因为此API是异步的，可返回“已排队”、“正在导入”或“失败”状态。 您必须保留batchId才能获取导入作业的状态，并在完成时检索失败和/或警告。 batchId的有效期为7天。

## 轮询作业状态

最佳实践是每5-30秒轮询一次作业，具体取决于所需的延迟和API调用限制，以查看导入作业的状态。 您可以使用Get Import Lead Status API执行此操作。

```
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

此响应显示已完成的导入，但状态可以是：

- 完成
- 已排队
- 正在导入
- 已失败

如果作业已完成，则会列出已处理、失败和出现警告的行数。 如果状态为“失败”，则消息参数可能也会给出失败消息。

## 故障

在Get Import Lead Status响应中，失败由“numOfRowsFailed”属性指示。 如果“numOfRowsFailed”大于零，则该值指示发生的失败次数。

要检索失败行的记录和原因，您必须检索失败文件：

```
GET /bulk/v1/leads/batch/{id}/failures.json
```

API将回复一个指示哪些行失败的文件，以及一个指示记录失败原因的消息。 文件的格式与作业创建期间在“format”参数中指定的格式相同。 附加字段将附加到每个记录中并包含失败描述。

## 警告

在Get Import Lead Status响应中，警告由“numOfRowsWithWarning”属性表示。 如果“numOfRowsWithWarning”大于零，则该值表示发生的警告数。

要检索记录以及警告行的原因，请检索警告文件：

```
GET /bulk/v1/leads/batch/{id}/warnings.json
```

API会用一个文件做出响应，该文件指示哪些行产生了警告，另外还会显示一条消息，指示记录失败的原因。 文件的格式与作业创建期间在“format”参数中指定的格式相同。 附加字段将附加到每个记录并附有警告说明。
