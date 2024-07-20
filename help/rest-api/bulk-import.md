---
title: 批量导入
feature: REST API
description: 批量导入人员数据。
exl-id: f7922fd2-8408-4d04-8955-0f8f58914d24
source-git-commit: 66add4c38d0230c36d57009de985649bb67fde3e
workflow-type: tm+mt
source-wordcount: '554'
ht-degree: 1%

---

# 批量导入

Marketo提供了用于插入大量人员和人员相关数据的界面，称为批量导入。 目前，界面提供三种对象类型：

- 潜在客户（人员）
- 自定义对象
- 计划成员

批量导入是通过创建作业，然后等待作业完成文件读取来执行的。 这些作业以异步方式执行，并且可以轮询以检索导入的状态。 每个RFC 2399文件使用HTTP多部分/表单数据上传。

批量API端点未像其他端点一样带有“/rest”前缀。

## 身份验证

批量导入API使用与其他Marketo REST API相同的OAuth 2.0身份验证方法。  这要求将有效的访问令牌作为查询字符串参数`access_token={_AccessToken_}`或HTTP标头`Authorization: Bearer {_AccessToken_}`嵌入。

## 限制

- 最大并发导入任务数：2
- 最大已排队导入作业数（包括当前导入的作业）：10
- 导入文件的最大大小：10 MB

## 权限

批量导入使用与Marketo REST API相同的权限模型，并且无需任何其他特殊权限即可使用，不过需要为每个端点集设置特定权限。

## 记录操作

批量导入是“插入或更新”记录操作。 如果在数据库中找到匹配记录，则更新该记录。 否则，将创建新记录。 批量导入响应不指示给定记录是否已更新或插入。

## 创建作业

Marketo的批量导入API使用作业的概念来执行数据导入。 我们来看看如何使用[导入潜在客户](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Bulk-Import-Leads/operation/importLeadUsingPOST)端点创建一个简单的潜在客户导入作业。  请注意，此端点使用[multipart/form-data作为内容类型](https://www.w3.org/Protocols/rfc1341/7_2_Multipart.html)。 这很难做到，因此最佳实践是为您选择的语言使用HTTP支持库。  如果您只是想让脚湿润，我们建议您使用[curl](https://curl.se/)。

```
POST /bulk/v1/leads.json?format=csv
```

```
Content-Type: multipart/form-data; boundary=--------------------------WebKitFormBoundaryBQACkJZyaiIAXogC
Content-Length: 311
Host: <munchkinId>.mktorest.com
```

```
------WebKitFormBoundaryBQACkJZyaiIAXogC
Content-Disposition: form-data; name="file"; filename="leads.csv"
Content-Type: text/csv

firstName,lastName,email
Able,Baker,ablebaker@marketo.com
Charlie,Dog,charliedog@marketo.com
Easy,Fox,easyfox@marketo.com
------WebKitFormBoundaryBQACkJZyaiIAXogC--
```

此请求将构造一个作业，用于导入名为“leads.csv”的CSV文件中包含的值，该文件具有列标题“FirstName”、“LastName”、“Email”、“Company”。

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

当我们提交作业时，它会返回一个batchId，然后我们可以使用它来检查其状态。

### 常用参数

每个作业创建端点共享一些用于配置批量提取作业的文件格式、字段名和过滤器的常用参数。  提取作业的每种子类型可能都有其他参数：

| 参数 | 数据类型 | 备注 |
|---|---|---|
| 格式 | 字符串 | 使用逗号分隔值、制表符分隔值和分号分隔值的选项确定导入数据的文件格式。 接受以下内容之一：CSV、SSV、TSV。 格式默认为CSV。 |
| 文件 | 字符串 | 数据通过文件中的多部分表单数据指定。 |


## 轮询作业状态

使用[获取导入商机状态](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Bulk-Import-Leads/operation/getImportLeadStatusUsingGET)终结点确定作业状态非常简单。

```
GET /bulk/v1/leads/batch/{batchId}.json
```

```json
{
    "requestId": "1f63#15d6738fd15",
    "result": [
        {
            "batchId": 3404,
            "importId": "3404",
            "status": "Complete",
            "numOfLeadsProcessed": 3,
            "numOfRowsFailed": 0,
            "numOfRowsWithWarning": 0,
            "message": "Import succeeded, 3 records imported (3 members)"
        }
    ],
    "success": true
}
```

内部`status`成员将指示作业进度，并且可能是以下值之一：“已排队”、“正在导入”、“完成”、“失败”。 在这种情况下，我们的作业已完成，因此我们可以停止轮询。

## 故障

在“获取导入潜在客户状态”响应中，`numOfRowsFailed`属性指示失败。 如果`numOfRowsFailed`大于零，则该值表示发生的失败次数。

要检索失败行的记录和原因，您必须使用[获取导入潜在客户失败](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Bulk-Import-Leads/operation/getImportLeadFailuresUsingGET)端点检索失败文件。

```
GET /bulk/v1/leads/batch/{batchId}/failures.json
```

该文件指示哪些行失败，并显示一条指示记录失败原因的消息。
