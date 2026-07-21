---
title: 事务性电子邮件
feature: REST API
description: 了解如何为Marketo配置事务性电子邮件，并通过REST API请求营销活动触发这些电子邮件，以及设置步骤和Java代码示例。
exl-id: 057bc342-53f3-4624-a3c0-ae619e0c81a5
TQID: https://experienceleague.adobe.com/eUw2THnwDdIuEO3MsuG4cSaoPnKVvdZ0ZTV-gxP-pJQ
product_v2: id: b27e5950-9033-45ac-9f86-eb22e567f615
feature_v2: id: b3b8a63f-51fc-40f6-a7d2-a31c5d49fb45id: e64968b2-4ee5-47f9-8cae-0588f184b9eb
role_v2: id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
source-git-commit: 3e6d310c5aec1a3435424fb122b71d825db5af0e
workflow-type: tm+mt
source-wordcount: 897
ht-degree: 1%

---

# 事务性电子邮件

使用[请求营销活动](https://developer.adobe.com/marketo-apis/api/mapi#tag/Campaigns/operation/triggerCampaignUsingPOST) API向特定Marketo记录发送事务性电子邮件。 在发出请求之前，配置电子邮件并触发营销活动。

- 确保收件人具有Marketo记录。
- 在Marketo实例中创建和批准事务型电子邮件。
- 激活使用“Campaign is Requested， 1”的触发器营销活动。 Source： Web服务API”并发送电子邮件。

首先，[创建和批准电子邮件](https://experienceleague.adobe.com/docs/marketo/using/home.html)。 如果电子邮件在法律上符合操作条件，请在“电子邮件操作”>“电子邮件设置”下将其配置为可操作：

![Request-Campaign-Email-Settings](assets/request-campaign-email-settings.png)

![Request-Campaign-Operational](assets/request-campaign-operational.png)

在创建营销活动之前批准电子邮件：

![RequestCampaign-Prove-Draft](assets/request-campaign-approve-draft.png)

如果需要，请参阅[创建新的Smart Campaign](https://experienceleague.adobe.com/docs/marketo/using/product-docs/core-marketo-concepts/smart-campaigns/creating-a-smart-campaign/create-a-new-smart-campaign.html)。 使用Campaign is Requested触发器配置活动的智能列表：

![Request-Campaign-Smart-List](assets/request-campaign-smart-list.png)

配置引用事务型电子邮件的发送电子邮件流程步骤：

![Request-Campaign-Flow](assets/request-campaign-flow.png)

在激活之前，请在“计划”选项卡上配置资格设置。 如果每个记录只接收一次电子邮件，则保留默认设置。 否则，将允许收件人每次或以可用节奏获得资格。

激活营销活动：

![Request-Campaign-Schedule](assets/request-campaign-schedule.png)

## 发送API调用

Java示例使用[minimal-json包](https://github.com/ralfstx/minimal-json)来处理JSON呈现。

在发送电子邮件之前，确认该电子邮件地址存在Marketo记录并检索其潜在客户ID。 此示例假定电子邮件地址已存在。

使用[按筛选器类型](https://developer.adobe.com/marketo-apis/api/mapi#tag/Leads/operation/getLeadsByFilterUsingGET)获取潜在客户，以检索ID。 然后，以下主要方法请求营销活动：

```java
package dev.marketo.blog_request_campaign;

import com.eclipsesource.json.JsonArray;

public class App
{
    public static void main( String[] args )
    {
        //Create an instance of Auth so that we can authenticate with our Marketo instance
        Leads leadsRequest = new Leads(auth).setFilterType("email").addFilterValue("requestCampaign.test@marketo.com");

        //Create and parameterize an instance of Leads
        //Set your email filterValue appropriately
        Leads leadsRequest = new Leads(auth).setFilterType("email").addFilterValue("test.requestCamapign@example.com");

        //Get the inner results array of the response
        JsonArray leadsResult = leadsRequest.getData().get("result").asArray();

        //Get the id of the record indexed at 0
        int lead = leadsResult.get(0).asObject().get("id").asInt();

        //Set the ID of your campaign from Marketo
        int campaignId = 0;
        RequestCampaign rc = new RequestCampaign(auth, campaignId).addLead(lead);

        //Send the request to Marketo
        rc.postData();
    }
}
```

从`JsonObject`响应中提取结果数组，并在索引0处检索对象：

```java
JsonArray leadsResult = leadsRequest.getData().get("result").asArray();
int leadId = leadsResult.get(0).asObject().get("id").asInt();
```

使用请求URL中的促销活动ID调用请求促销活动。 请求正文包含具有`id`成员的JSON对象数组：

```java
package dev.marketo.blog_request_campaign;
import java.io.IOException;
import java.io.InputStream;
import java.io.InputStreamReader;
import java.io.OutputStreamWriter;
import java.io.Reader;
import java.net.MalformedURLException;
import java.net.URL;
import java.util.ArrayList;
import javax.net.ssl.HttpsURLConnection;
import com.eclipsesource.json.JsonArray;
import com.eclipsesource.json.JsonObject;

public class RequestCampaign {
    private String endpoint;
    private Auth auth;
    public ArrayList leads = new ArrayList();
    public ArrayList tokens = new ArrayList();

    public RequestCampaign(Auth auth, int campaignId) {
        this.auth = auth;
        this.endpoint = this.auth.marketoInstance + "/rest/v1/campaigns/" + campaignId + "/trigger.json";
    }
    public RequestCampaign setLeads(ArrayList leads) {
        this.leads = leads;
        return this;
    }
    public RequestCampaign addLead(int lead){
        leads.add(lead);
        return this;
    }
    public RequestCampaign setTokens(ArrayList tokens) {
        this.tokens = tokens;
        return this;
    }
    public RequestCampaign addToken(String tokenKey, String val){
        JsonObject jo = new JsonObject().add("name", tokenKey);
        jo.add("value", val);
        tokens.add(jo);
        return this;
    }
    public JsonObject postData(){
        JsonObject result = null;
        try {
            JsonObject requestBody = buildRequest(); //builds the Json Request Body
            System.out.println("Executing RequestCampaign call\n" + "Endpoint: " + endpoint + "\nRequest Body:\n"  + requestBody);
            URL url = new URL(endpoint);
            HttpsURLConnection urlConn = (HttpsURLConnection) url.openConnection(); //Return a URL connection and cast to HttpsURLConnection
            urlConn.setRequestMethod("POST");
            urlConn.setRequestProperty("Content-type", "application/json");
            urlConn.setRequestProperty("accept", "text/json");
            urlConn.setDoOutput(true);
            OutputStreamWriter wr = new OutputStreamWriter(urlConn.getOutputStream());
            wr.write(requestBody.toString());
            wr.flush();
            InputStream inStream = urlConn.getInputStream(); //get the inputStream from the URL connection
            Reader reader = new InputStreamReader(inStream);
            result = JsonObject.readFrom(reader); //Read from the stream into a JsonObject
            System.out.println("Result:\n" + result);
        } catch (MalformedURLException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        }
        return result;
    }

    private JsonObject buildRequest(){
        JsonObject requestBody = new JsonObject(); //Create a new JsonObject for the Request Body
        JsonObject input = new JsonObject();
        JsonArray leadsArray = new JsonArray();
        for (int lead : leads) {
            JsonObject jo = new JsonObject().add("id", lead);
            leadsArray.add(jo);
        }
        input.add("leads", leadsArray);
        JsonArray tokensArray = new JsonArray();
        for (JsonObject jo : tokens) {
            tokensArray.add(jo);
        }
        input.add("tokens", tokensArray);
        requestBody.add("input", input);
        return requestBody;
    }

}
```

此类有一个接受身份验证的构造函数，以及营销活动的ID。 通过将包含记录ID的`ArrayList<Integer>`传递给setLeads，或者使用addLead（它采用一个整数并将其附加到leads属性中的现有ArrayList），将潜在客户添加到对象中。 要触发API调用以将潜在客户记录传递到营销活动，必须调用postData，这会返回包含来自请求的响应数据的JsonObject。 在调用request campaign时，Marketo中的target触发器营销活动将处理传递给调用的每个商机，并向其发送之前创建的电子邮件。 恭喜，您已通过Marketo REST API触发电子邮件。 请留意第2部分，我们将通过“请求营销活动”动态自定义电子邮件的内容。

### 构建电子邮件

若要自定义我们的内容，我们必须首先在Marketo中配置[程序](https://experienceleague.adobe.com/docs/marketo/using/product-docs/core-marketo-concepts/programs/creating-programs/create-a-program.html)和[电子邮件](https://experienceleague.adobe.com/docs/marketo/using/home.html)。 要生成自定义内容，我们必须在程序中创建令牌，然后将它们放入要发送的电子邮件中。 为了方便起见，在本例中，我们仅使用一个令牌，但您可以替换电子邮件、发件人电子邮件、发件人姓名、回复或电子邮件中任何内容的任何数量的令牌。 因此，让我们创建一个用于替换的令牌富文本，并将其称为“bodyReplacement”。 富文本允许我们将令牌中的任何内容替换为我们要输入的任意HTML。

![新令牌](assets/New-Token.png)

令牌在空时无法保存，因此请在此处插入一些占位符文本。 现在，我们必须将令牌插入到电子邮件中：

![添加令牌](assets/Add-Token.png)

此令牌现在可通过请求营销活动调用进行替换。 此令牌可以非常简单，只需一行文本（必须根据每封电子邮件替换它），也可以包含电子邮件的几乎整个布局。

### 代码

```java
package dev.marketo.blog_request_campaign;

import com.eclipsesource.json.JsonArray;

public class App
{
    public static void main( String[] args )
    {
        //Create an instance of Auth so that we can authenticate with our Marketo instance
        Auth auth = new Auth("Client ID - CHANGE ME", "Client Secret - CHANGE ME", "Host - CHANGE ME");

        //Create and parameterize an instance of Leads
        Leads leadsRequest = new Leads(auth).setFilterType("email").addFilterValue("requestCampaign.test@marketo.com");

        //get the inner results array of the response
        JsonArray leadsResult = leadsRequest.getData().get("result").asArray();

        //get the id of the record indexed at 0
        int lead = leadsResult.get(0).asObject().get("id").asInt();

        //Set the ID of our campaign from Marketo
        int campaignId = 1578;
        RequestCampaign rc = new RequestCampaign(auth, campaignId).addLead(lead);

        //Create the content of the token here, and add it to the request
        String bodyReplacement = "<div class=\"replacedContent\"><p>This content has been replaced</p></div>";
        rc.addToken("{{my.bodyReplacement}}", bodyReplacement);
        rc.postData();
    }
}
```

如果代码看起来很熟悉，那是因为它只有上面主方法中的另外两行。 这次我们在bodyReplacement变量中创建令牌的内容，然后使用addToken方法将其添加到请求中。 addToken获取一个键和一个值，然后创建一个JsonObject表示形式并将其添加到内部令牌数组。 然后在postData方法期间序列化它，并创建如下主体：

```json
{
    "input":
    {
        "leads": [
            {
                "id": 1
            }
        ],
        "tokens": [
            {
                "name": "{{my.bodyReplacement}}",
                "value": "<div class=\"replacedContent\"><p>This content has been replaced</p></div>"
            }
        ]
    }
}
```

综合起来，我们的控制台输出如下所示：

```bash
Token is empty or expired. Trying new authentication
Trying to authenticate with ...
Got Authentication Response: {"access_token":"19d51b9a-ff60-4222-bbd5-be8b206f1d40:st","token_type":"bearer","expires_in":3565,"scope":"apiuser@mktosupport.com"}
Executing RequestCampaign call
Endpoint: .../rest/v1/campaigns/1578/trigger.json
Request Body:
{"input":{"leads":[{"id":1}],"tokens":[{"name":"{{my.bodyReplacement}}","value":"<div class=\"replacedContent\"><p>This content has been replaced</p></div>"}]}}
Result:
{"requestId":"1e8d#14eadc5143d","result":[{"id":1578}],"success":true}
```

## 正在结束

此方法可通过多种方式扩展，可更改单个布局分区内电子邮件中的内容或外部电子邮件中的内容，从而将自定义值传递到任务或有趣的时间点。 通过此方法，可以在程序中使用令牌的任何位置进行自定义。 [计划营销活动](https://developer.adobe.com/marketo-apis/api/mapi#tag/Campaigns/operation/scheduleCampaignUsingPOST)调用也提供了类似功能，可让您处理整个批次营销活动中的令牌。 无法基于每个潜在客户自定义这些内容，但可用于在广泛的潜在客户集中自定义内容。
