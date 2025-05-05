---
title: 错误代码
feature: REST API
description: Marketo错误代码描述。
exl-id: a923c4d6-2bbc-4cb7-be87-452f39b464b6
source-git-commit: d0750eab0a37df0b7f80c6252f46c95068975000
workflow-type: tm+mt
source-wordcount: '2273'
ht-degree: 3%

---

# 错误代码

以下是REST API错误代码列表，并说明如何将错误返回给应用程序。

## 处理和记录异常

在针对Marketo进行开发时，如果遇到意外异常，请务必记录请求和响应。 虽然重新身份验证可以安全地处理某些类型的异常（例如过期的身份验证），但其他异常可能需要支持交互，在此情况下将始终请求请求和响应。

## 错误类型

在正常操作下，Marketo REST API可以返回三种不同类型的错误：

* HTTP级别：这些错误由`4xx`代码指示。
* 响应级别：这些错误包含在JSON响应的“错误”数组中。
* 记录级别：这些错误包含在JSON响应的“result”数组中，并且是以“status”字段和“reasons”数组单独记录为基础指示的。

对于响应级别和记录级别错误类型，返回HTTP状态代码200。 对于所有错误类型，不应评估HTTP原因短语，因为它是可选的且可能会发生更改。

### HTTP级别错误

在正常操作情况下，Marketo应仅返回两个HTTP状态代码错误： `413 Request Entity Too Large`和`414 Request URI Too Long`。 通过捕获错误、修改请求和重试，这些都可恢复，但通过智能编码实践，您绝不应该在野外遇到这些情况。

如果请求有效负载超过1MB，Marketo将返回413，对于Import Lead将返回10MB。 在大多数情况下，不会达到这些限制，但是如果向请求的大小添加检查，并移动任何记录，导致超过新请求的限制，则应防止出现任何情况，这种情况会导致任何端点返回此错误。

当GET请求的URI超过8KB时，将返回414。 要避免出现这种情况，请检查查询字符串的长度，看看是否超过此限制。 如果它确实将您的请求更改为POST方法，则使用附加参数`_method=GET`将您的查询字符串输入为请求正文。 这放弃了URI的限制。 在大多数情况下，很少达到此限制，但在检索具有长单个过滤器值（如GUID）的大量记录时，这种情况比较常见。
[标识](https://developer.adobe.com/marketo-apis/api/identity/)终结点可能返回401未授权错误。 这通常是由于无效的客户端ID或无效的客户端密钥。 HTTP级别的错误代码

<table>
  <thead>
    <tr>
      <th> 响应代码 </th>
      <th> 描述 </th>
      <th colspan="1"> 注释 </th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><a name="413"></a>413</td>
      <td>请求实体太大</td>
      <td>有效负载超出1MB限制。</td>
    </tr>
    <tr>
      <td><a name="414"></a>414</td>
      <td>请求URI过长</td>
      <td>请求的URI超过了8k。 该请求应作为在URL中带有param '_method=GET'的POST进行重试，其余查询字符串应作为请求正文重试。</td>
    </tr>
  </tbody>
</table>


#### 响应级别错误

将响应的`success`参数设置为false时，将出现响应级别错误，其结构如下所示：

```json
{
    "requestId": "e42b#14272d07d78",
    "success": false,
    "errors": [
        {
            "code": "601",
            "message": "Unauthorized"
        }
    ]
}
```

“错误”数组中的每个对象都有两个成员，`code`，它是从601到799的带引号的整数，一个`message`提供了错误的纯文本原因。 6xx代码始终指示请求完全失败并且未执行。 例如，“访问令牌无效”601可通过重新身份验证并使用请求传递新访问令牌而恢复。 7xx错误表示请求失败，可能是因为未返回任何数据，或者请求被错误地参数化，如包含无效的日期，或缺少所需的参数。

#### 响应级错误代码

返回此响应代码的API调用不会计入每日配额或速率限制中。

<table>
  <thead>
    <tr>
      <th> 响应代码 </th>
      <th> 描述 </th>
      <th> 注释 </th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><a name="502"></a>502</td>
      <td>错误网关</td>
      <td>远程服务器返回了一个错误。 可能是超时。 应重试该请求，并返回指数回退。</td>
    </tr>
    <tr>
      <td><a name="601"></a>601*</td>
      <td>访问令牌无效</td>
      <td>请求中包含访问令牌参数，但值不是有效的访问令牌。</td>
    </tr>
    <tr>
      <td><a name="602"></a>602*</td>
      <td>访问令牌已过期</td>
      <td>调用中包含的访问令牌因过期而不再有效。</td>
    </tr>
    <tr>
      <td><a name="603"></a>603</td>
      <td>访问被拒绝</td>
      <td>身份验证成功，但用户没有足够的权限调用此API。 [其他权限](custom-services.md)可能需要分配给用户允许列表，或可能启用<a href="https://experienceleague.adobe.com/zh-hans/docs/marketo/using/product-docs/administration/additional-integrations/create-an-allowlist-for-ip-based-api-access">角色以访问基于IP的API</a>。</td>
    </tr>
    <tr>
      <td><a name="604"></a>604*</td>
      <td>请求超时</td>
      <td>请求运行时间过长（例如，遇到数据库争用），或超过了调用标头中指定的超时期限。</td>
    </tr>
    <tr>
      <td><a name="605"></a>605*</td>
      <td>不支持HTTP方法</td>
      <td>同步潜在客户端点不支持GET。 必须使用POST。</td>
    </tr>
    <tr>
      <td><a name="606"></a>606</td>
      <td>最大速率限制“%s”；超出了“%s”秒中的限制</td>
      <td>过去20秒内的呼叫数大于100</td>
    </tr>
    <tr>
      <td><a name="607"></a>607</td>
      <td>已达到每日配额</td>
      <td>今天的呼叫数超过了订阅的配额（CST每天凌晨12:00重置）。&gt;可以在“管理员” — &gt;“Web服务”菜单中找到您的配额。 您可以通过帐户管理员增加配额。</td>
    </tr>
    <tr>
      <td><a name="608"></a>608*</td>
      <td>API暂时不可用</td>
      <td></td>
    </tr>
    <tr>
      <td><a name="609"></a>609</td>
      <td>JSON无效</td>
      <td>请求中包含的主体不是有效的JSON。</td>
    </tr>
    <tr>
      <td><a name="610"></a>610</td>
      <td>未找到请求的资源</td>
      <td>调用中的URI与REST API资源类型不匹配。 这通常是由于URI拼写错误或格式错误所致</td>
    </tr>
    <tr>
      <td><a name="611"></a>611*</td>
      <td>系统错误</td>
      <td>所有未处理的异常</td>
    </tr>
    <tr>
      <td><a name="612"></a>612</td>
      <td>内容类型无效</td>
      <td>如果看到此错误，请向请求添加一个指定JSON格式的内容类型标头。 例如，尝试使用“content type： application/json”。 <a href="https://stackoverflow.com/questions/28181325/why-invalid-content-type">有关更多详细信息，请参阅此StackOverflow问题</a>。</td>
    </tr>
    <tr>
      <td><a name="613"></a>613</td>
      <td>无效的多部分请求</td>
      <td>POST的多部分内容格式不正确</td>
    </tr>
    <tr>
      <td><a name="614"></a>614</td>
      <td>订阅无效</td>
      <td>找不到目标订阅或目标订阅无法访问。 这通常表示暂时无法访问。</td>
    </tr>
    <tr>
      <td><a name="615"></a>615</td>
      <td>已达到并发访问限制</td>
      <td>一次最多由任何订阅10处理请求。 如果已经有10个正在进行的请求，则会返回此项。</td>
    </tr>
    <tr>
      <td><a name="616"></a>616</td>
      <td>订阅类型无效</td>
      <td>访问自定义对象元数据API需要适当的Marketo订阅类型。 有关详细信息，请咨询您的CSM。</td>
    </tr>
    <tr>
      <td><a name="701"></a>701</td>
      <td>%s不能为空</td>
      <td>请求中报告的字段不能为空</td>
    </tr>
    <tr>
      <td><a name="702"></a>702</td>
      <td>未找到给定搜索方案的数据</td>
      <td>没有与给定搜索参数匹配的记录。
        注意：许多失败的搜索操作返回“success = true”，并且没有错误，同时设置了警告信息字符串。</td>
    </tr>
    <tr>
      <td><a name="703"></a>703</td>
      <td>订阅未启用该功能</td>
      <td>用户订阅中未启用的测试版功能</td>
    </tr>
    <tr>
      <td><a name="704"></a>704</td>
      <td>日期格式无效</td>
      <td><ul>
          <li>指定的日期格式不正确</li>
          <li>指定的动态内容ID无效</li>
        </ul></td>
    </tr>
    <tr>
      <td><a name="709"></a>709</td>
      <td>违反业务规则</td>
      <td>调用无法完成，因为它违反了创建或更新资产的要求，例如，尝试在没有模板的情况下创建电子邮件。 尝试以下操作时也可能出现此错误：
        <ul>
          <li>检索包含社交内容的登陆页面的内容。</li>
          <li>克隆包含特定资产类型的程序（有关详细信息，请参阅<a href="programs.md#clone">程序克隆</a>）。</li>
          <li>批准没有草稿（即已批准）的资产。</li>
        </ul></td>
    </tr>
    <tr>
      <td><a name="710"></a>710</td>
      <td>未找到父文件夹</td>
      <td>找不到指定的父文件夹</td>
    </tr>
    <tr>
      <td><a name="711"></a>711</td>
      <td>不兼容的文件夹类型</td>
      <td>指定的文件夹的类型不正确，无法完成请求</td>
    </tr>
    <tr>
      <td><a name="712"></a>712</td>
      <td>合并到人员帐户操作无效</td>
      <td>由于尝试合并Salesforce人员帐户中的潜在客户，合并潜在客户调用失败。  Salesforce人员帐户必须合并到Salesforce中。</td>
    </tr>
    <tr>
      <td><a name="713"></a>713</td>
      <td>暂时性错误</td>
      <td>在API调用时，系统资源暂时不可用。 遇到此错误时，建议等待一段时间，然后重试请求。</td>
    </tr>
    <tr>
      <td><a name="714"></a>714</td>
      <td>找不到默认记录类型</td>
      <td>由于无法找到默认记录类型，合并潜在客户调用失败。</td>
    </tr>
    <tr>
      <td><a name="718"></a>718</td>
      <td>未找到ExternalSalesPersonID</td>
      <td>使用不存在的“ExternalSalesPersonID”值执行了同步机会调用。</td>
    </tr>
    <tr>
      <td>719</td>
      <td>锁定等待超时异常</td>
      <td>已发出克隆程序调用，等待锁定时超时。</td>
    </tr>
  </tbody>
</table>

### 记录级别 {#record_level_errors}

记录级别错误表示无法为单个记录完成操作，但请求本身有效。 具有记录级错误的响应遵循以下模式：


#### 响应

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
         "status":"skipped",
         "reasons":[  
            {  
               "code":"1005",
               "message":"Lead already exists"
            }
         ]
      }
   ]
}
```

调用结果数组中包含的记录的排序方式与请求的输入数组相同。
成功请求中的每个记录都可能在单独的基础上成功或失败，响应结果数组中包含的每个记录的状态字段即表示这一点。 将“跳过”这些记录的“状态”字段，并存在“原因”数组。 每个原因都包含一个“代码”成员和一个“消息”成员。 代码始终为1xxx，该消息指示跳过记录的原因。 例如，同步潜在客户请求的“action”设置为“createOnly”，但已提交的记录中的某个键已存在潜在客户。 此案例会返回一个代码1005，并显示一条消息“Lead already exists”（商机已存在），如上所示。

#### 记录级错误代码

<table>
  <tbody>
    <tr>
      <td>响应代码</td>
      <td>描述</td>
      <td>注释</td>
    </tr>
    <tr>
      <td><a name="1001"></a>1001</td>
      <td>值“%s”无效。 需要“%s”类型</td>
      <td>每当参数值有类型不匹配时，就会生成错误。 例如，为integer参数指定的字符串值。</td>
    </tr>
    <tr>
      <td><a name="1002"></a>1002</td>
      <td>缺少所需参数“%s”的值</td>
      <td>请求中缺少所需的参数时会生成错误</td>
    </tr>
    <tr>
      <td><a name="1003"></a>1003</td>
      <td>数据无效</td>
      <td>提交的数据不是给定端点或模式的有效类型时；例如，通过指定为createOnly的操作提交商机的id时，或在批量营销活动中使用请求营销活动时。</td>
    </tr>
    <tr>
      <td><a name="1004"></a>1004</td>
      <td>未找到潜在客户</td>
      <td>对于syncLead，当操作为“updateOnly”且未找到潜在客户时</td>
    </tr>
    <tr>
      <td><a name="1005"></a>1005</td>
      <td>潜在客户已存在</td>
      <td>对于syncLead，当操作为“createOnly”并且商机已存在时</td>
    </tr>
    <tr>
      <td><a name="1006"></a>1006</td>
      <td>找不到字段“%s”</td>
      <td>调用中包含的字段不是有效字段。</td>
    </tr>
    <tr>
      <td><a name="1007"></a>1007</td>
      <td>多个潜在客户符合查找条件</td>
      <td>多个潜在客户符合查找条件。 仅当键与单个记录匹配时才能执行更新</td>
    </tr>
    <tr>
      <td><a name="1008"></a>1008</td>
      <td>访问分区“%s”被拒绝</td>
      <td>自定义服务的用户无权访问包含该记录所在分区的工作区。</td>
    </tr>
    <tr>
      <td><a name="1009"></a>1009</td>
      <td>必须指定分区名称</td>
      <td></td>
    </tr>
    <tr>
      <td><a name="1010"></a>1010</td>
      <td>不允许分区更新</td>
      <td>指定的记录已存在于单独的Lead分区中。</td>
    </tr>
    <tr>
      <td><a name="1011"></a>1011</td>
      <td>不支持字段“%s”</td>
      <td>当查找字段或用不支持的标准字段（例如：firstName、lastName）指定的“filterType”时</td>
    </tr>
    <tr>
      <td><a name="1012"></a>1012</td>
      <td>无效的Cookie值“%s”</td>
      <td>调用<a href="https://developer.adobe.com/marketo-apis/api/mapi/#tag/Leads/operation/associateLeadUsingPOST">关联潜在客户</a>时，可能会发生这种情况，因为该潜在客户的“cookie”参数值无效。
        当使用“filterType=cookies”和“filterValues”参数的无效值通过筛选类型</a>调用<a href="https://developer.adobe.com/marketo-apis/api/mapi/#tag/Leads/operation/getLeadsByFilterUsingGET">获取潜在客户时，也会发生这种情况。</td>
    </tr>
    <tr>
      <td><a name="1013"></a>1013</td>
      <td>未找到对象</td>
      <td>按ID获取对象（列表、营销活动）会返回此错误代码</td>
    </tr>
    <tr>
      <td><a name="1014"></a>1014</td>
      <td>创建对象失败</td>
      <td>创建对象（列表）失败</td>
    </tr>
    <tr>
      <td><a name="1015"></a>1015</td>
      <td>潜在客户不在列表中</td>
      <td>指定的潜在客户不是目标列表的成员</td>
    </tr>
    <tr>
      <td><a name="1016"></a>1016</td>
      <td>导入过多</td>
      <td>有太多导入已排队。 最多允许10个</td>
    </tr>
    <tr>
      <td><a name="1017"></a>1017</td>
      <td>对象已存在</td>
      <td>创建失败，因为记录已存在</td>
    </tr>
    <tr>
      <td><a name="1018"></a>1018</td>
      <td>CRM已启用</td>
      <td>无法执行操作，因为实例启用了本机CRM集成。</td>
    </tr>
    <tr>
      <td><a name="1019"></a>1019</td>
      <td>正在导入</td>
      <td>目标列表已在导入到</td>
    </tr>
    <tr>
      <td><a name="1020"></a>1020</td>
      <td>要编程的克隆太多</td>
      <td>订阅已达到当天的计划计划中“cloneToProgramName”的分配使用量</td>
    </tr>
    <tr>
      <td><a name="1021"></a>1021</td>
      <td>不允许公司更新</td>
      <td>不允许在syncLead期间更新公司</td>
    </tr>
    <tr>
      <td><a name="1022"></a>1022</td>
      <td>正在使用的对象</td>
      <td>当某个对象正由另一个对象使用时，不允许删除</td>
    </tr>
    <tr>
      <td><a name="1025"></a>1025</td>
      <td>程序状态未找到</td>
      <td>为更改潜在客户计划状态指定的状态与计划渠道可用的状态不匹配。</td>
    </tr>
    <tr>
      <td><a name="1026"></a>1026</td>
      <td>未启用自定义对象</td>
      <td>无法执行该操作，因为实例未启用自定义对象集成。</td>
    </tr>
    <tr>
      <td><a name="1027"></a>1027</td>
      <td>已达到最大活动类型限制</td>
      <td>订阅已达到可用自定义活动类型的最大数。</td>
    </tr>
    <tr>
      <td><a name="1028"></a>1028</td>
      <td>已达到最大字段限制</td>
      <td>自定义活动最多有20个次要属性。</td>
    </tr>
    <tr>
      <td><a name="1029"></a>1029</td>
      <td><ul>
          <li>队列中的作业过多</li>
          <li>超出导出每日配额</li>
        </ul></td>
      <td><ul>
          <li>在任何给定时间，队列中最多允许10个批量提取作业。</li>
          <li>默认情况下，提取作业限制为每天500 MB（CST凌晨12:00每日重置）。</li>
        </ul></td>
    </tr>
    <tr>
      <td><a name="1035"></a>1035</td>
      <td>不支持的筛选器类型</td>
      <td>在某些订阅中，不支持以下批量潜在客户提取过滤器类型： updatedAt、smartListId、smartListName。</td>
    </tr>
    <tr>
      <td><a name="1036"></a>1036</td>
      <td>在输入中发现重复的对象</td>
      <td>调用了使用相同的外键更新两个或多个记录。 例如，同步公司调用时对多个公司使用相同的externalCompanyId。</td>
    </tr>
    <tr>
      <td><a name="1037"></a>1037</td>
      <td>已跳过潜在客户</td>
      <td>已跳过潜在客户，因为它已经处于或超过此状态。</td>
    </tr>
    <tr>
      <td><a name="1042"></a>1042</td>
      <td>运行日期无效</td>
      <td>为计划营销活动指定的runAt日期太长了（最长为2年）。</td>
    </tr>
    <tr>
      <td><a name="1048"></a>1048</td>
      <td>自定义对象放弃草稿失败</td>
      <td>已进行调用以放弃自定义对象的草稿版本。</td>
    </tr>
    <tr>
      <td><a name="1049"></a>1049</td>
      <td>创建活动失败</td>
      <td>属性数组太长。
        传递给记录的属性数组超过了65536字节的最大长度</td>
    </tr>
    <tr>
      <td><a name="1076"></a>1076</td>
      <td>使用mergeInCRM标志的<a href="https://developer.adobe.com/marketo-apis/api/mapi/#tag/Leads/operation/mergeLeadsUsingPOST">合并潜在客户</a>调用为4。</td>
      <td>您正在创建重复记录。 建议您改用现有记录。
        这是Marketo在Salesforce中合并时收到的错误消息。</td>
    </tr>
    <tr>
      <td><a name="1077"></a>1077</td>
      <td>由于“SFDC字段”长度，<a href="https://developer.adobe.com/marketo-apis/api/mapi/#tag/Leads/operation/mergeLeadsUsingPOST">合并潜在客户</a>调用失败</td>
      <td>由于“SFDC字段”超出允许的字符限制，mergeInCRM设置为true的合并潜在客户调用失败。 要更正此问题，请缩短“SFDC字段”的长度，或将mergeInCRM设置为false。</td>
    </tr>
    <tr>
      <td><a name="1078"></a>1078</td>
      <td><a href="https://developer.adobe.com/marketo-apis/api/mapi/#tag/Leads/operation/mergeLeadsUsingPOST">合并潜在客户</a>调用失败，因为实体已删除，不是潜在客户/联系人，或者字段筛选条件不匹配。</td>
      <td>合并失败，无法在本地同步的CRM中执行合并操作
        这是Marketo在Salesforce中合并时收到的错误消息。</td>
    </tr>
  </tbody>
</table>
