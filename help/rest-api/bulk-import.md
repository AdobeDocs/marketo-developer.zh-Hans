---
title: 批量导入
feature: REST API
description: Marketo批量导入，用于通过多部分上传加载潜在客户、自定义对象和程序成员，创建异步作业、轮询状态和处理故障。
exl-id: f7922fd2-8408-4d04-8955-0f8f58914d24
TQID: https://experienceleague.adobe.com/lr9dyX-fY-oJ2LM5P0zE1m24HtFYKQYYbxMkVe--PkE
product_v2:
  - id: b27e5950-9033-45ac-9f86-eb22e567f615
feature_v2:
  - id: c5f60233-d5ea-4453-a799-0ad258b4d399
role_v2:
  - id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
source-git-commit: 3e6d310c5aec1a3435424fb122b71d825db5af0e
workflow-type: tm+mt
source-wordcount: 538
ht-degree: 2%

---

# 批量导入

批量导入提供用于插入大量人员和人员相关数据的界面。 您可以导入三种对象类型：

- 潜在客户（人员）
- 自定义对象
- 计划成员

要执行批量导入，请创建读取已上载文件的作业。 该作业以异步方式运行，因此请轮询该作业以检索导入状态。

每个RFC 2399使用HTTP `multipart/form-data`上载文件。

与其他端点不同，批量API端点不以`/rest`为前缀。

## 身份验证

批量导入API使用与其他Marketo REST API相同的OAuth 2.0身份验证方法。 在`Authorization: Bearer {_AccessToken_}` HTTP标头中发送有效的访问令牌。

>[!IMPORTANT]
>
>2025年6月30日，将移除对使用&#x200B;**access_token**&#x200B;查询参数的身份验证的支持。 如果您的项目使用查询参数来传递访问令牌，则应尽快更新以使用&#x200B;**Authorization**&#x200B;标头。 新开发应仅使用&#x200B;**Authorization**&#x200B;标头。

## 限制

- 最大并发导入任务数：2
- 已排队的最大导入作业（包括当前导入的作业）数：10
- 最大导入文件大小：10 MB

## 权限

批量导入使用与Marketo REST API相同的权限模型。 它不需要额外的权限，但每组端点需要特定的权限。

## 记录操作

批量导入是“插入或更新”记录操作。 如果数据库包含匹配记录，则该操作会更新该记录。 否则，该操作将创建一个记录。

批量导入响应不指示单个记录是否已更新或插入。

## 创建作业

通过调用[导入潜在客户](https://developer.adobe.com/marketo-apis/api/mapi#tag/Bulk-Import-Leads/operation/importLeadUsingPOST)终结点来创建潜在客户导入作业。 此终结点使用[multipart/form-data作为content-type](https://www.w3.org/Protocols/rfc1341/7_2_Multipart.html)。

为首选语言使用HTTP支持库来构建多部分请求。 您还可以使用[curl](https://curl.se/)开始。

```http
POST /bulk/v1/leads.json?format=csv
```

```text
Content-Type: multipart/form-data; boundary=--------------------------WebKitFormBoundaryBQACkJZyaiIAXogC
Content-Length: 311
Host: <munchkinId>.mktorest.com
```

```text
------WebKitFormBoundaryBQACkJZyaiIAXogC
Content-Disposition: form-data; name="file"; filename="leads.csv"
Content-Type: text/csv

firstName,lastName,email
Able,Baker,ablebaker@marketo.com
Charlie,Dog,charliedog@marketo.com
Easy,Fox,easyfox@marketo.com
------WebKitFormBoundaryBQACkJZyaiIAXogC--
```

此请求创建一个作业，该作业从名为`leads.csv`的CSV文件导入值。

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

响应返回`batchId`。 使用此值可检查作业状态。

### 常用参数

每个作业创建端点共享用于配置导入文件的参数。 导入子类型还可以支持其他参数。

| 参数 | 数据类型 | 注释 |
| --- | --- | --- |
| 格式 | 字符串 | 使用逗号分隔值、制表符分隔值和分号分隔值的选项确定导入数据的文件格式。 接受以下内容之一：CSV、SSV、TSV。 格式默认为CSV。 |
| 文件 | 字符串 | 数据通过文件中的多部分表单数据指定。 |

## 轮询作业状态

将`batchId`传递给[获取导入商机状态](https://developer.adobe.com/marketo-apis/api/mapi#tag/Bulk-Import-Leads/operation/getImportLeadStatusUsingGET)终结点，以检索作业状态。

```http
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

`status`成员表示作业进度。 其值可以是`Queued`、`Importing`、`Complete`或`Failed`。

在本例中，作业已完成，因此轮询可以停止。

## 故障

获取导入商机状态响应中的`numOfRowsFailed`属性指示失败的行数。 值大于零表示发生故障。

要检索失败的记录及其原因，请使用[获取导入潜在客户失败](https://developer.adobe.com/marketo-apis/api/mapi#tag/Bulk-Import-Leads/operation/getImportLeadFailuresUsingGET)端点。

```http
GET /bulk/v1/leads/batch/{batchId}/failures.json
```

失败文件将标识每个失败的行，并解释记录失败的原因。
