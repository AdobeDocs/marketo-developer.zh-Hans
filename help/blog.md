---
title: 博客档案
description: Marketo开发人员博客档案2014-2023提供了有关Forms 2.0、Zapier、API更新、SOAP弃用和迁移到REST的历史文章。
exl-id: d7ae88dd-9938-4957-9798-db43090dab4e
source-git-commit: 5881ab969eca3a37d19f56b6570e42828994eff3
workflow-type: tm+mt
source-wordcount: '61723'
ht-degree: 0%

---

# 博客档案

>[!INFO]
>
>这是Marketo博客的存档，时间跨度为2014年至2023年。 此处仅作为历史参考提供。
>某些信息可能已过期。  请始终查看当前文档以了解最新功能。
>

>[!IMPORTANT]
>SOAP API已被弃用，在2026年3月31日后将不再可用。 所有新的开发应使用Marketo REST API完成，并且现有服务应在该日期之前迁移以避免服务中断。 如果您的服务使用SOAP API，请参阅[SOAP API迁移指南](https://experienceleague.adobe.com/en/docs/marketo-developer/marketo/soap/migration)以了解如何迁移的信息。
>

>[!IMPORTANT]
>2026年3月31日，将移除对使用`access_token`查询参数的身份验证的支持。 如果您的项目使用查询参数来传递访问令牌，则应尽快更新以使用[授权标头](https://experienceleague.adobe.com/en/docs/marketo-developer/marketo/rest/authentication#using-an-access-token)。 新开发应专门使用Authorization标头。
>

## 欢迎访问Marketo开发人员博客

欢迎Marketo开发人员！ 我们在此忙于发布Marketo的新功能，以帮助营销人员比以往任何时候都更成功。 当今的营销人员得到杰出的开发人员社区的支持。 此博客专门面向那些支持现代营销人员快速发展的需求的Web开发人员和软件工程师。 随着Marketo平台的发展，我们将宣布推出新的集成选项和API版本更新。 我们还将介绍一系列新的操作方法文章，在这些文章中，我们将分享与Marketo平台集成的代码示例和最佳实践。 本系列中的第一篇文章将指导您了解如何使用API高效地检索Marketo中存储的人员（客户/联系人/潜在客户）的信息。 请使用上述表格订阅以掌握最新信息。 我们会在发布新文章时通过电子邮件向您发送更新。

由&#x200B;_David_&#x200B;发布于&#x200B;_2014-03-06_

## 2014年1月版更新

### Forms 2.0

Forms 2.0使营销人员能够在无需编程知识的情况下创建美观、稳定且灵活的Web窗体。 除了大幅改进的编辑器、条件逻辑和稳健的设计之外，现在还可以比以往更轻松地嵌入Marketo以构成网站的任何页面。 开发人员可以使用JavaScript扩展核心功能；用例包括：

* 成功提交后隐藏表单，以便访客在填写表单后留在页面上
* 在基于自定义业务逻辑的提交上显示自定义错误消息
* 在灯箱样式对话框中显示表单

开发人员文档可在[此处](/help/javascript-api/forms-api-reference.md)获得。

### SOAP API版本2_3现已可用

* [getLeadChanges：](/help/soap-api/getleadchanges.md)引入的请求字段`activityNameFilter`
* [ListOperation：](/help/soap-api/listoperation.md)已删除请求字段`skipActivityLog`

**注意：** SOAP API修订版向后兼容

由&#x200B;_Travis Kaufman_&#x200B;发布于&#x200B;_2014-01-26_

## Zapier第II部分：Marketo集成公告

在前一篇文章中，我们讨论了如何使用Zapier将外部数据源与Marketo集成。 这篇文章提供了一种从头开始构建您自己的Zapier工作流（或“Zap”）以集成Marketo和其他应用程序的实际操作方法。 那么，您是否希望将Zapier与Marketo结合使用，但是需要在入门指南中获得帮助？ 好消息！ Zapier刚刚为Marketo发布了一些示例Zap，使您能够快速入门：

* 捕获新潜在客户
* 培养您的客户
* 分发联系信息
* 对新潜在客户作出反应

除了这些示例，您还可以在Zapier上通过数百个其他应用程序浏览[Marketo集成](https://zapier.com/apps/marketo/integrations)页面，并在几分钟内构建您自己的自动化工作流。 无需编码。 节省时间，让自动化完成您的手动工作。 发挥创意。 天空就是极限！

由&#x200B;_David_&#x200B;发布于&#x200B;_2016-06-01_

## 使用API从Marketo检索客户和潜在客户信息

您可以使用`getLead`和[`getMultipleLeads`](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Leads) SOAP API检索存储在Marketo中的客户和潜在客户信息。 我们通常希望定期提取此信息，以便随着客户和潜在客户信息在Marketo中更新或创建新记录而保持另一个系统的更新。 我们将向您显示循环执行的代码示例，以便轮询Marketo以获取更新。 下图描述了在设置的定期计时器上进行的API调用。 根据用例，可以将定期计时器设置为每10分钟运行一次以下代码。

第一次调用[`getMultipleLeads`](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Leads)将设置时间范围、batchSize以及要返回的字段。 当可用记录数多于指定的batchSize时，Marketo中在指定时间范围内更新的所有潜在客户将与streamPosition一起返回。

**SOAP请求首次调用getMultipleLeads：**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<ns2:paramsGetMultipleLeads xmlns:ns2="<http://www.marketo.com/mktows/">
  <leadSelector xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:type="ns2:LastUpdateAtSelector">
    <latestUpdatedAt>2014-02-13T11:51:08.710-08:00</latestUpdatedAt>
    <oldestUpdatedAt>2014-02-12T11:51:08.713-08:00</oldestUpdatedAt>
  </leadSelector>
  <batchSize>2</batchSize>
  <includeAttributes>
    <stringItem>FirstName</stringItem>
    <stringItem>LastName</stringItem>
    <stringItem>Email</stringItem>
  </includeAttributes>
</ns2:paramsGetMultipleLeads>
```

**来自第一次调用getMultipleLeads的SOAP响应：**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<ns2:successGetMultipleLeads xmlns:ns2="<http://www.marketo.com/mktows/">
  <result>
 <returnCount>2</returnCount><remainingCount>24</remainingCount><newStreamPosition>id:1089965:to:1392234668:tl:1392321068:os:2:rc:24</newStreamPosition><leadRecordList>
      <leadRecord>
        <Id>84105</Id>
        <Email>eimang@marketo.com</Email>
        <ForeignSysPersonId xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:nil="true" />
        <ForeignSysType xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:nil="true" />
        <leadAttributeList xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:nil="true">
          <attribute>
            <attrName>FirstName</attrName>
            <attrType>string</attrType>
            <attrValue>French</attrValue>
          </attribute>
          <attribute>
            <attrName>LastName</attrName>
            <attrType>string</attrType>
            <attrValue>Lead</attrValue>
          </attribute>
        </leadAttributeList>
      </leadRecord>
      <leadRecord>
        <Id>1089965</Id>
        <Email>t@t.com</Email>
        <ForeignSysPersonId xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:nil="true" />
        <ForeignSysType xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:nil="true" />
        <leadAttributeList xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:nil="true">
          <attribute>
            <attrName>FirstName</attrName>
            <attrType>string</attrType>
            <attrValue>George</attrValue>
          </attribute>
          <attribute>
            <attrName>LastName</attrName>
            <attrType>string</attrType>
            <attrValue>of the Jungle</attrValue>
          </attribute>
        </leadAttributeList>
      </leadRecord>
    </leadRecordList>
  </result>
</ns2:successGetMultipleLeads>
```

只要`<remainingCount/>`值大于0，您就可以通过将上一次调用中返回的`getMultipleLeads`值传递到`<newStreamPosition/>`参数中，来对`<streamPosition/>`进行后续调用，以分页查看其余值。 **SOAP请求后续调用getMultipleLeads：**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<ns2:paramsGetMultipleLeads xmlns:ns2="<http://www.marketo.com/mktows/">
  <leadSelector xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:type="ns2:LastUpdateAtSelector">
    <latestUpdatedAt>2014-02-13T11:51:08.710-08:00</latestUpdatedAt>
    <oldestUpdatedAt>2014-02-12T11:51:08.713-08:00</oldestUpdatedAt>
  </leadSelector><streamPosition>id:1089965:to:1392234668:tl:1392321068:os:2:rc:24</streamPosition><batchSize>2</batchSize>
  <includeAttributes>
    <stringItem>FirstName</stringItem>
    <stringItem>LastName</stringItem>
    <stringItem>Email</stringItem>
  </includeAttributes>
</ns2:paramsGetMultipleLeads>
```

只要`<remainingCount/>`大于零，此逻辑就会继续。 **请参阅下面的执行上述方案的示例Java程序。**

```java
import com.marketo.mktows.\*;
import java.net.URL;
import javax.xml.namespace.QName;
import java.text.DateFormat;
import java.text.SimpleDateFormat;
import java.util.Date;
import java.util.GregorianCalendar;
import javax.crypto.Mac;
import javax.crypto.spec.SecretKeySpec;
import org.apache.commons.codec.binary.Hex;
import javax.xml.bind.JAXBContext;
import javax.xml.bind.JAXBElement;
import javax.xml.bind.Marshaller;
import javax.xml.datatype.DatatypeFactory;
import javax.xml.datatype.XMLGregorianCalendar;

public class GetMultipleLeads {
  public static void main(String[] args) {
    System.out.println("Executing GetMultipleLeads");
        try {
        URL marketoSoapEndPoint = new URL("CHANGE ME" + "?WSDL");
        String marketoUserId = "CHANGE ME";
        String marketoSecretKey = "CHANGE ME";
        QName serviceName = new QName("<http://www.marketo.com/mktows/>",
        "MktMktowsApiService");
        MktMktowsApiService service = new
        MktMktowsApiService(marketoSoapEndPoint, serviceName);
        MktowsPort port = service.getMktowsApiSoapPort();

        // Create Signature
        DateFormat df = new SimpleDateFormat("yyyy-MM-dd'T'HH:mm:ssZ");
        String text = df.format(new Date());
        String requestTimestamp = text.substring(0, 22) + ":" +         text.substring(22);
        String encryptString = requestTimestamp + marketoUserId ;

        SecretKeySpec secretKey = new         SecretKeySpec(marketoSecretKey.getBytes(), "HmacSHA1");

        Mac mac = Mac.getInstance("HmacSHA1");
        mac.init(secretKey);
        byte[] rawHmac = mac.doFinal(encryptString.getBytes());
        char[] hexChars = Hex.encodeHex(rawHmac);
        String signature = new String(hexChars);

        // Set Authentication Header
        AuthenticationHeader header = new AuthenticationHeader();
        header.setMktowsUserId(marketoUserId);
        header.setRequestTimestamp(requestTimestamp);
        header.setRequestSignature(signature);

        // Create Request
        ParamsGetMultipleLeads request = new ParamsGetMultipleLeads();

        // Build Request Using LastUpdateAtSelector
        LastUpdateAtSelector leadSelector = new LastUpdateAtSelector();
        GregorianCalendar gc = new GregorianCalendar();
        gc.setTimeInMillis(new Date().getTime());
        gc.add( GregorianCalendar.DAY_OF_YEAR, -20);
        DatatypeFactory factory = DatatypeFactory.newInstance();
        ObjectFactory objectFactory = new ObjectFactory();
        JAXBElement<XMLGregorianCalendar> until =         objectFactory.createLastUpdateAtSelectorLatestUpdatedAt(factory.newXMLGregorianCalendar(gc));

        GregorianCalendar since = new GregorianCalendar();
        since.setTimeInMillis(new Date().getTime());
        since.add( GregorianCalendar.DAY_OF_YEAR, -21);
        leadSelector.setOldestUpdatedAt(factory.newXMLGregorianCalendar(since));
        leadSelector.setLatestUpdatedAt(until);
        request.setLeadSelector(leadSelector);

        ArrayOfString attributes = new ArrayOfString();
        attributes.getStringItems().add("FirstName");
        attributes.getStringItems().add("LastName");
        attributes.getStringItems().add("Email");
        request.setIncludeAttributes(attributes);

        JAXBElement<Integer> batchSize = new
        ObjectFactory().createParamsGetMultipleLeadsBatchSize(2);
        request.setBatchSize(batchSize);

        SuccessGetMultipleLeads result = null;

        int count = 0;

        do {
        if (count > 0) {
        // Set the streamPosition on subsequent calls to paginate
        through large result sets
        String pos = result.getResult().getNewStreamPosition();
        JAXBElement<String> streamPos = new
        ObjectFactory().createParamsGetMultipleLeadsStreamPosition(pos);
        request.setStreamPosition(streamPos);
        }

        JAXBContext context =
        JAXBContext.newInstance(ParamsGetMultipleLeads.class);
        Marshaller m = context.createMarshaller();
        m.setProperty(Marshaller.JAXB_FORMATTED_OUTPUT, true);
        System.out.println("REQUEST:");
        m.marshal(request, System.out);
        result = port.getMultipleLeads(request, header);
        System.out.println("RESPONSE:");
        m.marshal(result, System.out);
        count = result.getResult().getReturnCount();
        } while (result.getResult().getRemainingCount() > 0);
        }

        catch(Exception e) {
        // Update to include appropriate retry/error handling
        e.printStackTrace();
        }

  }

}
```

### 提示和技巧

从Marketo中提取大量联系人时，建议调整API请求以及以下参数：

* `<includeAttributes/>`：建议您只请求希望与系统保持同步的字段。 这减少了响应的有效负载并提高了查询性能。
* `<batchSize/>`： API支持在单个调用中最多返回1000条记录。 将此值调整为500条记录还可减少响应的有效负载。
* `<LastUpdatedAtSelector/>`：建议同时设置`<oldestUpdatedAt/>`和`<latestUpdatedAt/>`参数以限制日期范围。 例如，不要对一年的数据提出单个请求。 中断API调用以请求更小的日期范围。

本文包含用于实施自定义集成的代码。 由于其自定义性质，Marketo技术支持团队无法对自定义工作进行故障诊断。 如果没有适当的技术经验或联系经验丰富的开发人员，请勿尝试实施以下代码示例。

由&#x200B;_Travis Kaufman_&#x200B;在&#x200B;_2014-03-05_&#x200B;发布

## 2014年2月版更新

### SOAP API更新

* [syncMObjects](/help/soap-api/syncmobjects.md)：您现在可以为现有程序添加和更新标记和渠道。

更新已合并到[2_3 WSDL](http://app.marketo.com/soap/mktows/2_3?WSDL)中。

由&#x200B;_Travis Kaufman_&#x200B;发布于&#x200B;_2014-02-26_

## 2014年3月版更新

### SOAP API更新

* 对[syncLead](/help/soap-api/synclead.md)和[syncMultipleLeads](/help/soap-api/syncmultipleleads.md)的性能改进

更新已合并到[2_3 WSDL](http://app.marketo.com/soap/mktows/2_3?WSDL)中。

由&#x200B;_Travis Kaufman_&#x200B;发布于&#x200B;_2014-03-20_

## 在访客填写表单时合并匿名访客活动

在标题为“基于业务逻辑捕获匿名访客活动”的博客帖子中，我们讨论了如何在Marketo中基于自定义事件创建匿名潜在客户记录。 在本博客帖子中，我们将基于该帖子进行构建，并在您收到用户的联系信息后，将匿名潜在客户记录与已知用户关联。 Marketo的[Munchkin跟踪代码](/help/javascript-api/lead-tracking.md)可帮助您跟踪对网站的访问。 首次访问网站上具有Munchkin跟踪代码的页面时，Marketo会创建一个匿名潜在客户并使用浏览器Cookie来跟踪他们。 一旦他们进行自我识别，就会成为已知的潜在客户，并且与其浏览器Cookie关联的历史记录将合并到其Marketo潜在客户记录中。 **当某人为**&#x200B;时创建匿名潜在客户

1. 首次访问Marketo登陆页面
1. 访问具有Munchkin跟踪代码的页面
1. 单击Marketo电子邮件中的以网页形式查看链接

**当有以下人员时，匿名潜在客户将合并到新的或现有的已知潜在客户中：**

1. 单击指向Marketo电子邮件的链接
1. 填写Marketo表单
1. 使用以下技术之一将匿名潜在客户与已知记录相关联。

若要提交人员数据，或将浏览器Web跟踪Cookie与Marketo中的相应人员记录相关联，请使用以下技术之一：**浏览器端提交**&#x200B;如果您的用例要求从浏览器提交人员数据，则您应使用后台表单提交。 **服务器端提交**&#x200B;如果您不需要浏览器端提交，则REST API提供了多种人员数据提交方法，以及一种将Cookie与人员记录关联的专门构建方法。

由&#x200B;_Murta_&#x200B;发布于&#x200B;_2014-04-22_

## 2014年4月发行版更新

### Marketo Forms安全更新

我们对来自单个IP地址的表单帖子提交数量和频率进行了限制。 现在，该限制强制为每分钟30个帖子，以保护我们的客户免受恶意使用程序化表单提交的攻击。 [syncLead API](https://experienceleague.adobe.com/en/docs/marketo-developer/marketo/soap/leads/synclead)是推荐的集成工具，用于在Marketo中以编程方式提交新联系人。

由&#x200B;_Travis Kaufman_&#x200B;发布于&#x200B;_2014-04-29_

## 将您的表单发布集成替换为Marketo API

营销人员通常使用Marketo根据填写了特定Web表单的联系人/商机数量来关联给定营销活动的成功与否。 营销人员只需创建新的Marketo表单，将其放置到登陆页面上，然后在Marketo中设置触发营销活动，以将填写该特定表单的所有联系人关联为该营销计划的成功。 （请参见下面的屏幕快照）。 在记录项目成功时，自然会扩展使用此方法，即使促销活动成功存在于用户无法填写表单的情况下也是如此。 例如，当营销人员执行促销活动并且转化是调入并计划日程安排时。 潜在客户未在该流程的任何时间点填写表单。 在以下文章中，我们将向您说明如何使用Marketo syncLead API创建新联系人（如果他们是新联系人），然后使用requestCampaign API记录给定营销计划的成功情况。

由&#x200B;_Travis Kaufman_&#x200B;发布于&#x200B;_1970-01-01_

## 使用Marketo API删除潜在客户

假设您想通过Marketo API删除潜在客户。 您可以对具有预定义流程操作以删除商机的营销活动调用requestCampaign API来实现这一点。 我们将向您展示如何创建智能营销活动，如何设置触发器以通过API运行营销活动，如何定义将删除商机作为流量操作的一部分，以及第四个用于执行此营销活动的代码示例。 **如何在Marketo中创建新的Smart Campaign** Marketo中的Smart Campaigns执行您的所有营销活动。 在Smart Campaign中，您可以设置一系列要对联系人智能列表执行的自动操作。 如果删除潜在客户，则需在营销策划中设置触发器，如下所示。 首先，让我们设置Smart Campaign。

1. 在营销活动中，选择一个项目，然后在新建下拉列表中，单击新建本地资产1。 单击Smart Campaign
1. 输入智能营销活动名称，然后单击创建将触发器添加到Smart Campaign**将触发器添加到Smart Campaign允许您根据实时事件（在此例中为通过requestCampaign API发出的请求）一次对一个人运行Smart Campaign。
1. 搜索“已请求营销活动”触发器，然后将其拖放到画布上。
1. 在触发器中，选择“is”和“Web服务API”。

**如何在营销活动上创建“删除潜在客户流量”操作**&#x200B;单击顶部菜单中的“流量”。 从右侧的菜单中，搜索Delete Lead，然后将其拖动到中心以将其作为触发器添加到营销活动中。 注意：如果仅从Marketo中删除潜在客户并将其保留在您的CRM中，则该潜在客户的任何数据都会更新时，在Marketo中会重新创建该潜在客户。  **调用requestCampaign API的代码示例**&#x200B;在Marketo界面中设置营销活动和触发器后，我们将向您说明如何使用API运行此营销活动。 第一个示例是XML请求，第二个示例是XML响应，最后一个示例是可用于生成XML请求的Java代码示例。 我们还将向您说明如何查找在调用requestCampaign API时使用的促销活动ID。 API调用还要求您预先知道Marketo促销活动的ID。 您可以使用以下任一方法确定促销活动ID：

1. 使用[getCampaignsForSource](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getCampaignsUsingGET) API
1. 在浏览器中打开Marketo营销活动，并查看URL地址栏。 营销活动ID（以4位整数表示）可在“SC”之后立即找到。 例如：`https://app-stage.marketo.com/#SC**1025**A1`。粗体部分是营销活动ID - &quot;1025&quot;。 SOAP的requestCampaign

```xml
<?xml version="1.0" encoding="UTF-8"?>
<SOAP-ENV:Envelope xmlns:SOAP-ENV="<http://schemas.xmlsoap.org/soap/envelope/>" xmlns:ns1="<http://www.marketo.com/mktows/">
  <SOAP-ENV:Header>
    <ns1:AuthenticationHeader>
      <mktowsUserId>demo17_1_809939944BFABAE58E5D27</mktowsUserId><requestSignature>48397ad47b71a1439f13a51eea3137df46441979</requestSignature><requestTimestamp>2013-08-01T12:31:14-07:00</requestTimestamp>
    </ns1:AuthenticationHeader>
  </SOAP-ENV:Header>
  <SOAP-ENV:Body>
    <ns1:paramsRequestCampaign>
      <source>MKTOWS</source>
      <campaignId>4496</campaignId>
      <leadList>
        <leadKey>
          <keyType>EMAIL</keyType>
          <keyValue>lead@company.com</keyValue>
        </leadKey>
        <leadKey>
          <keyType>EMAIL</keyType>
          <keyValue>anotherlead@company.com</keyValue>
        </leadKey>
      </leadList>
    </ns1:paramsRequestCampaign>
  </SOAP-ENV:Body>
</SOAP-ENV:Envelope>
```

SOAP对requestCampaign的响应

```xml
<?xml version="1.0" encoding="UTF-8"?>
<SOAP-ENV:Envelope xmlns:SOAP-ENV="<http://schemas.xmlsoap.org/soap/envelope/>" xmlns:ns1="<http://www.marketo.com/mktows/">
  <SOAP-ENV:Body>
    <ns1:successRequestCampaign>
      <result>
        <success>true</success>
      </result>
    </ns1:successRequestCampaign>
  </SOAP-ENV:Body>
</SOAP-ENV:Envelope>
```

请参阅下面的执行上述场景的示例Java程序。

```java
import com.marketo.mktows.*;
import java.net.URL;
import javax.xml.namespace.QName;
import java.text.DateFormat;
import java.text.SimpleDateFormat;
import java.util.Date;
import javax.crypto.Mac;
import javax.crypto.spec.SecretKeySpec;
import org.apache.commons.codec.binary.Hex;
import javax.xml.bind.JAXBContext;
import javax.xml.bind.JAXBElement;
import javax.xml.bind.Marshaller;


public class RequestCampaign {

    public static void main(String[] args) {
        System.out.println("Executing Request Campaign");
        try {
            URL marketoSoapEndPoint = new URL("CHANGE ME" + "?WSDL");
            String marketoUserId = "CHANGE ME";
            String marketoSecretKey = "CHANGE ME";

            QName serviceName = new QName("http://www.marketo.com/mktows/", "MktMktowsApiService");
            MktMktowsApiService service = new MktMktowsApiService(marketoSoapEndPoint, serviceName);
            MktowsPort port = service.getMktowsApiSoapPort();

            // Create Signature
            DateFormat df = new SimpleDateFormat("yyyy-MM-dd'T'HH:mm:ssZ");
            String text = df.format(new Date());
            String requestTimestamp = text.substring(0, 22) + ":" + text.substring(22);
            String encryptString = requestTimestamp + marketoUserId ;

            SecretKeySpec secretKey = new SecretKeySpec(marketoSecretKey.getBytes(), "HmacSHA1");
            Mac mac = Mac.getInstance("HmacSHA1");
            mac.init(secretKey);
            byte[] rawHmac = mac.doFinal(encryptString.getBytes());
            char[] hexChars = Hex.encodeHex(rawHmac);
            String signature = new String(hexChars);

            // Set Authentication Header
            AuthenticationHeader header = new AuthenticationHeader();
            header.setMktowsUserId(marketoUserId);
            header.setRequestTimestamp(requestTimestamp);
            header.setRequestSignature(signature);

            // Create Request
            ParamsRequestCampaign request = new ParamsRequestCampaign();

            request.setSource(ReqCampSourceType.MKTOWS);

            ObjectFactory objectFactory = new ObjectFactory();
            JAXBElement<Integer> campaignId = objectFactory.createParamsRequestCampaignCampaignId(4496);
            request.setCampaignId(campaignId);

            ArrayOfLeadKey leadKeyList = new ArrayOfLeadKey();
            LeadKey key = new LeadKey();
            key.setKeyType(LeadKeyRef.EMAIL);
            key.setKeyValue("lead@company.com");

            LeadKey key2 = new LeadKey();
            key2.setKeyType(LeadKeyRef.EMAIL);
            key2.setKeyValue("anotherlead@company.com");

            leadKeyList.getLeadKeies().add(key);
            leadKeyList.getLeadKeies().add(key2);

            JAXBElement<ArrayOfLeadKey> arrayOfLeadKey = objectFactory.createParamsRequestCampaignLeadList(leadKeyList);
            request.setLeadList(arrayOfLeadKey);

            SuccessRequestCampaign result = port.requestCampaign(request, header);

            JAXBContext context = JAXBContext.newInstance(SuccessRequestCampaign.class);
            Marshaller m = context.createMarshaller();
            m.setProperty(Marshaller.JAXB_FORMATTED_OUTPUT, true);
            m.marshal(result, System.out);

        }
        catch(Exception e) {
            e.printStackTrace();
        }
    }
}
```

本文包含用于实施自定义集成的代码。 由于其自定义性质，Marketo技术支持团队无法对自定义工作进行故障诊断。 如果没有适当的技术经验或联系经验丰富的开发人员，请勿尝试实施以下代码示例。

由&#x200B;_Murta_&#x200B;发布于&#x200B;_2014-05-16_

## 使用Munchkin AP进行自定义活动跟踪 — 

让我们来看看Marketo中的自定义活动。 例如，您有一个网页上的视频，并且希望跟踪观看了超过50%视频的访客。 您可以使用Munchkin的自定义活动跟踪功能执行此操作。 要实现此目的，请侦听页面上的事件（视频播放率达到50%），然后调用Munchkin API。 为此，我们必须在Marketo中设置一个自定义活动，以根据页面上的此事件调用。 我们将YouTube用于视频播放器，并使用他们的[YouTube Iframe API](https://developers.google.com/youtube/iframe_api_reference)在Munchkin API上调用方法。

我们首先介绍如何在Marketo中生成Munchkin跟踪代码，其次介绍如何修改Munchkin示例代码以根据页面事件进行触发，第三介绍如何使用由页面上的操作定义的具有流程步骤的智能列表设置营销活动，第五介绍如何验证在Marketo中记录了匿名用户的页面访问。 ====这篇博客帖子是正在解释的代码的一个实时示例。 请填写此表单，以便您是Marketo中的已知用户。 这样一来，当您观看了50%的视频时，它就会向您发送其余视频，如果您观看了100%的视频，它就会向您发送指向其他博客帖子的链接。=== <https://developers.google.com/youtube/iframe_api_reference>

**如何生成Munchkin跟踪代码** Munchkin跟踪代码允许您跟踪对网站的访问。 下面介绍了三种类型的Munchkin代码，但在此示例中，我们使用异步Munchkin跟踪代码。 A)简单：代码行最少，但不会优化网页加载时间。 每次加载网页时，此代码都会加载jQuery库。 B)异步：缩短网页加载时间。 此代码检查jQuery库是否已存在，如果缺少库，则加载它，并在加载网页的其余部分后使用它执行跟踪代码。 C)异步jQuery：减少网页加载时间，并提高系统性能。 此代码假定您已经拥有jQuery，并且不检查以加载它。

1. 单击应用程序右上方的管理员。
1. 单击左侧树中的Munchkin 。
1. 选择异步以跟踪代码类型。
1. 单击并复制JavaScript跟踪代码以放置到您的网站上。 **YouTube代码** <https://developers.google.com/youtube/js_api_reference#EventHandlers> <https://developers.google.com/youtube/iframe_api_reference> 玩家。

`getCurrentTime()`返回自视频开始播放以来经过的时间（以秒为单位）。 `player.getDuration()`返回当前播放视频的持续时间（以秒为单位）。 请注意，在加载视频元数据之前，`getDuration()`将返回0，通常在视频开始播放后立即加载。 当非Cookie用户访问包含Munchkin跟踪代码的页面时，会在用户的浏览器中创建一个新的Cookie，并将在Marketo中创建一个新的匿名潜在客户。 如果用户已经过确认，并且用户是Marketo中的现有潜在客户，则对Marketo页面的访问将记录在该用户活动日志中。 **用于Cookie用户和跟踪事件的代码示例**&#x200B;将跟踪代码放在网页上的`</body>`标记之前。 在Marketo中创建的登陆页面会自动包含跟踪代码，因此您无需在其上放置此代码。 此代码示例将在加载脚本后调用Munchkin API：

```javascript
<script type="text/javascript">
(function() {
  var didInit = false;
  function initMunchkin() {
    if(didInit === false) {
      didInit = true;
      Munchkin.init('XXX-XXX-XXX');
    }
  }
  var s = document.createElement('script');
  s.type = 'text/javascript';
  s.async = true;
  s.src = '//munchkin.marketo.net/munchkin.js';
  s.onreadystatechange = function() {
    if (this.readyState == 'complete' || this.readyState == 'loaded') {
      initMunchkin();
    }
  };
  s.onload = initMunchkin;
  document.getElementsByTagName('head')[0].appendChild(s);
})();
</script>
```

此代码示例将在用户在该页面上停留了5秒并且还向下滚动了500像素之后调用Munchkin API：

```javascript
<script src="https://code.jquery.com/jquery-2.1.0.min.js"></script>
<script type="text/javascript">
$(function(){
 setTimeout(function(){
  $(window).scroll(function() {
      var y_scroll_position = window.pageYOffset;
      var scroll_position = 500; //Sets number of pixels user must scroll to be tracked

  if(y_scroll_position > scroll_position) {
  //Munchkin tracking code
   (function() {
     var didInit = false;
     function initMunchkin() {
      if(didInit === false) {
        didInit = true;
        Munchkin.init('XXX-XXX-XXX');
      }
     }

     var s = document.createElement('script');
     s.type = 'text/javascript';
     s.async = true;
    s.src = '//munchkin.marketo.net/munchkin.js';
     s.onreadystatechange = function() {
      if (this.readyState == 'complete' || this.readyState == 'loaded') {
          initMunchkin();
      }
     };
     s.onload = initMunchkin;
     document.getElementsByTagName('head')[0].appendChild(s);
   })();
   }
 },5000); //Sets time delay before tracking user
});
</script>
```

**如何验证Marketo中记录了匿名用户访问页面的情况**

1. 单击顶部菜单中的Analytics ，然后单击“新建报表”。 选择“网页活动”作为报表类型，然后为您的报表命名。
1. 创建报表后，单击智能列表。 然后从右侧的框中选择已访问网页过滤器。 输入放置Munchkin跟踪代码的网页。
1. 单击“Setup（设置）”。 选择“来自ISP的匿名访客”，并将选项更改为“显示”。
1. 单击报告。 您现在会看到在您选择的网页上跟踪的活动。
1. 双击潜在客户记录，该记录随后将显示活动日志，您可以在其中查看匿名用户访问的特定页面。

本文包含用于实施自定义集成的代码。 由于其自定义性质，Marketo技术支持团队无法对自定义工作进行故障诊断。 如果没有适当的技术经验或联系经验丰富的开发人员，请勿尝试实施以下代码示例。

例如，对于包含多媒体内容的页面，您可能希望进行自定义跟踪。 一个常见示例是将Munchkin跟踪代码添加到页面中，同时使用Munchkin API在Marketo实例中生成事件以用于播放视频或收听音频剪辑等活动。 我们建议您在大多数或所有网页上放置Munchkin跟踪代码。 Munchkin跟踪代码会自动包含在您使用Marketo创建的登陆页中。 使用此调用可记录用户执行了某些操作，例如在Ajax、Flash或其他RIA环境中访问页面。 URL不得包含&#39;&#39;或任何域，但它可以指向任何页面，甚至是不存在的页面。 如果要添加URL参数，请使用params参数。
该事件将作为访问网页事件显示在调用网页的域下的用户活动日志中。 注意：您第一次调用`mktoMunchkin()`时，始终会为当前页面创建一个访问网页事件。 除非要跟踪其他网页访问，否则无需调用`visitWebPage`。 `mktoMunchkinFunction('visitWebPage', { url: '/MyFlashMovie/Story1', params: 'x=y&2=3' });`注意：请确保您有权访问经验丰富的JavaScript开发人员。 Marketo技术支持未设置为协助对自定义JavaScript进行故障诊断。 Munchkin JavaScript API允许您将第三方Web系统与Marketo帐户集成。 通过某些Web开发，您可以捕获新的潜在客户或使用您网站上的现有应用程序更新当前的潜在客户。 假设您有一个用于客户注册的Web应用程序，该应用程序可捕获新的客户信息。 只需进行一点编程，您还可以获得在Marketo中捕获的那些用户的潜在客户信息，以及针对未来Web跟踪设置的Marketo Cookie。

此外，还有一项功能允许您的Web开发人员从富的Web环境（如Flash或Ajax）中捕获和跟踪Web活动信息。 注意：如果您有适当的开发资源，则应当考虑使用我们的SOAP API进行集成，而不是使用此API。 SOAP API比Munchkin API更稳健，并且功能更多。 Marketo SOAP API要求您必须在网页上包含Munchkin JavaScript代码，才能使所有这些代码正常工作。 您可以在Munchkin教程中找到所需的脚本标记。 此外，还可启用Munchkin API，本教程中也对此进行了说明。
在这种氛围下，当您进行Munchkin API调用后，如果用户没有Cookie，它会自动对用户进行Cookie。 在Marketo中，它会在人员的活动日志中记录事件（单击链接、访问网页或新商机）。 如果您使用点击链接或访问网页调用，则该事件将会添加到该潜在客户的活动日志（已知或匿名）。 如果这是新的潜在客户并且您使用关联潜在客户调用，则该潜在客户将成为已知潜在客户，其活动历史记录将被保留。 如果这是现有的潜在客户（基于电子邮件地址匹配），则该潜在客户记录中的任何更改值或新值都将更新。

以下是`munchkinFunction`调用的一般形式。 无论您想在何处调用它，都将其作为标记包含在网页中。 您可以像调用任何其他JavaScript函数一样调用此函数。 但是，您必须先调用Munchkin跟踪函数`mktoMunchkin()`，然后才能进行任何`mktoMunchkinFunction()`调用：

```javascript
<script src="http://munchkin.marketo.net/munchkin.js" type="text/javascript"> mktoMunchkin("###-###-###"); mktoMunchkinFunction('function', { key: 'value', key2: 'value'}, 'hash');
```

其中： `###-###-###`是您帐户函数的Munchkin帐户ID，而您要将参数设置为调用哈希所需的参数数组的调用只需要`associateLead`

由&#x200B;_Murta_&#x200B;发布于&#x200B;_1970-01-01_

## 将数据载入到Marketo中

下面的演示文稿向您展示了将数据导入Marketo的各种方式。 它侧重于表单、自定义对象和集成。

从[Murtza Manzur](https://www.slideshare.net/MurtzaManzur/getting-data-into-marketo-35662408)将数据导入Marketo[](https://www.slideshare.net/MurtzaManzur)

由&#x200B;_Murta_&#x200B;发布于&#x200B;_2014-06-06_

## 在Workspace中创建潜在客户

假设您的公司有两个部门：北美和欧洲。 您希望根据Marketo中的公司部门对潜在客户进行细分。 您可以使用工作区完成此操作，工作区是Marketo中的一项功能，允许您限制对潜在客户的访问。 为此，您需要为北美创建一个工作区，为欧洲创建一个工作区。 然后，您可以使用[syncLead API](/help/soap-api/synclead.md)在特定工作区中创建潜在客户。 如果贵组织具有以下特征，则应考虑使用工作区和潜在客户分区：

1. 为多个产品线划分营销团队
1. 针对不同的地区或国家/地区建立独立的营销团队
1. 母公司及子公司
1. 母公司和经销商

使用潜在客户分区和工作区时，您可以：

1. 限制对组织中潜在客户的访问
1. 限制对组织中资产的访问
1. 在营销团队之间共享资产

我们首先向您说明如何通过UI在Marketo中创建工作区，然后介绍如何使用[syncLead API](/help/soap-api/synclead.md)向该工作区写入潜在客户。 **创建Workspace**&#x200B;工作区是一组潜在客户和Marketo资源。 在工作区中，您只能看到来自该工作区的潜在客户以及该工作区中的资产（电子邮件、营销活动、列表等）。 该工作区中的智能营销活动只会影响该工作区中的潜在客户。 要查看帐户中的工作区，请执行以下操作：

1. 转到“管理员”部分的“工作区和潜在客户分区”页。 您的工作区将显示在“工作区”选项卡中。 1.要创建新工作区，请单击“工作区”选项卡菜单栏中的“新建Workspace”按钮。
1. 在对话框中，您需要添加一些有关新工作区的信息：

* **Workspace名称** — 此工作区在界面中显示的名称
* **描述** — 工作区的可选文本描述
* **潜在客户分区** — 这些潜在客户在此分区中可见。
* **所有潜在客户分区** — 将看到来自所有当前和未来分区的潜在客户
* **选择单个分区** — 只显示来自这些分区的潜在客户（将来不会显示任何分区）
* **主要潜在客户分区** — 默认情况下会将访问您的登陆页面的潜在客户添加到此分区
* **语言** — 工作区的商业语言

我们将向您说明如何使用API编写指向特定工作区的潜在客户。 第一个示例是XML请求，第二个示例是XML响应，最后一个示例是可用于生成XML请求的Ruby代码示例。 1.创建销售线索后，“销售线索分区”是“销售线索信息”中的一个字段。 针对`requestCampaign`的SOAP请求

```xml
<?xml version="1.0" encoding="UTF-8"?>
<SOAP-ENV:Envelope xmlns:SOAP-ENV="<http://schemas.xmlsoap.org/soap/envelope/>" xmlns:ns1="<http://www.marketo.com/mktows/">
  <SOAP-ENV:Header>
    <ns1:AuthenticationHeader>
      <mktowsUserId>demo17_1_809939944BFABAE58E5D27</mktowsUserId><requestSignature>48397ad47b71a1439f13a51eea3137df46441979</requestSignature><requestTimestamp>2013-08-01T12:31:14-07:00</requestTimestamp>
    </ns1:AuthenticationHeader>
  </SOAP-ENV:Header>
  <SOAP-ENV:Body>
    <ns1:paramsRequestCampaign>
      <source>MKTOWS</source>
      <campaignId>4496</campaignId>
      <leadList>
        <leadKey>
          <keyType>EMAIL</keyType>
          <keyValue>lead@company.com</keyValue>
        </leadKey>
        <leadKey>
          <keyType>EMAIL</keyType>
          <keyValue>anotherlead@company.com</keyValue>
        </leadKey>
      </leadList>
    </ns1:paramsRequestCampaign>
  </SOAP-ENV:Body>
</SOAP-ENV:Envelope>
```

SOAP对requestCampaign的响应

```xml
<?xml version="1.0" encoding="UTF-8"?>
<SOAP-ENV:Envelope xmlns:SOAP-ENV="<http://schemas.xmlsoap.org/soap/envelope/>" xmlns:ns1="<http://www.marketo.com/mktows/">
  <SOAP-ENV:Body>
    <ns1:successRequestCampaign>
      <result>
        <success>true</success>
      </result>
    </ns1:successRequestCampaign>
  </SOAP-ENV:Body>
</SOAP-ENV:Envelope>
```

请参阅下面的执行上述场景的示例Java程序。

```java
import com.marketo.mktows.*;
import java.net.URL;
import javax.xml.namespace.QName;
import java.text.DateFormat;
import java.text.SimpleDateFormat;
import java.util.Date;
import javax.crypto.Mac;
import javax.crypto.spec.SecretKeySpec;
import org.apache.commons.codec.binary.Hex;
import javax.xml.bind.JAXBContext;
import javax.xml.bind.JAXBElement;
import javax.xml.bind.Marshaller;


public class RequestCampaign {

    public static void main(String[] args) {
        System.out.println("Executing Request Campaign");
        try {
            URL marketoSoapEndPoint = new URL("CHANGE ME" + "?WSDL");
            String marketoUserId = "CHANGE ME";
            String marketoSecretKey = "CHANGE ME";

            QName serviceName = new QName("http://www.marketo.com/mktows/", "MktMktowsApiService");
            MktMktowsApiService service = new MktMktowsApiService(marketoSoapEndPoint, serviceName);
            MktowsPort port = service.getMktowsApiSoapPort();

            // Create Signature
            DateFormat df = new SimpleDateFormat("yyyy-MM-dd'T'HH:mm:ssZ");
            String text = df.format(new Date());
            String requestTimestamp = text.substring(0, 22) + ":" + text.substring(22);
            String encryptString = requestTimestamp + marketoUserId ;

            SecretKeySpec secretKey = new SecretKeySpec(marketoSecretKey.getBytes(), "HmacSHA1");
            Mac mac = Mac.getInstance("HmacSHA1");
            mac.init(secretKey);
            byte[] rawHmac = mac.doFinal(encryptString.getBytes());
            char[] hexChars = Hex.encodeHex(rawHmac);
            String signature = new String(hexChars);

            // Set Authentication Header
            AuthenticationHeader header = new AuthenticationHeader();
            header.setMktowsUserId(marketoUserId);
            header.setRequestTimestamp(requestTimestamp);
            header.setRequestSignature(signature);

            // Create Request
            ParamsRequestCampaign request = new ParamsRequestCampaign();

            request.setSource(ReqCampSourceType.MKTOWS);

            ObjectFactory objectFactory = new ObjectFactory();
            JAXBElement<Integer> campaignId = objectFactory.createParamsRequestCampaignCampaignId(4496);
            request.setCampaignId(campaignId);

            ArrayOfLeadKey leadKeyList = new ArrayOfLeadKey();
            LeadKey key = new LeadKey();
            key.setKeyType(LeadKeyRef.EMAIL);
            key.setKeyValue("lead@company.com");

            LeadKey key2 = new LeadKey();
            key2.setKeyType(LeadKeyRef.EMAIL);
            key2.setKeyValue("anotherlead@company.com");

            leadKeyList.getLeadKeies().add(key);
            leadKeyList.getLeadKeies().add(key2);

            JAXBElement<ArrayOfLeadKey> arrayOfLeadKey = objectFactory.createParamsRequestCampaignLeadList(leadKeyList);
            request.setLeadList(arrayOfLeadKey);

            SuccessRequestCampaign result = port.requestCampaign(request, header);

            JAXBContext context = JAXBContext.newInstance(SuccessRequestCampaign.class);
            Marshaller m = context.createMarshaller();
            m.setProperty(Marshaller.JAXB_FORMATTED_OUTPUT, true);
            m.marshal(result, System.out);

        }
        catch(Exception e) {
            e.printStackTrace();
        }
    }
}
```

**如何注册工作区和Lead分区？**&#x200B;对于Marketo Enterprise客户，您可以免费访问工作区和潜在客户分区。 有关启用和实施这些功能的详细信息，请联系您的客户启用经理。 对于其他客户，请联系您的Marketo销售主管或发送电子邮件给我们的销售团队，以了解有关此产品的更多信息。

本文包含用于实施自定义集成的代码。 由于其自定义性质，Marketo技术支持团队无法对自定义工作进行故障诊断。 如果没有适当的技术经验或联系经验丰富的开发人员，请勿尝试实施以下代码示例。

由&#x200B;_Murta_&#x200B;发布于&#x200B;_1970-01-01_

## 2014年6月版更新

### Marketo实时Personalization API

Marketo实时Personalization (RTP) JavaScript API扩展了平台的自动个性化功能。 它允许对网页进行事件跟踪和动态自定义。 请在[此处](/help/javascript-api/web-personalization.md)查看完整文档。

由&#x200B;_Travis Kaufman_&#x200B;发布于&#x200B;_2014-06-20_

## 在Marketo中存储外键

在诸如专有CRM或数据仓库等系统之间同步联系和潜在客户记录时，通常要求将潜在客户记录与唯一的系统标识符相关联。 在Marketo中，您可以使用唯一的系统标识符通过[syncMultipleLeads API](/help/soap-api/syncmultipleleads.md)调用创建或更新潜在客户记录。 要完成此操作，您需要将唯一的系统标识符（主键）存储为Marketo中的外键。 Marketo中用于存储外键的此字段的名称是foreignSysPersonId。 以下是需要注意的三项重要事项：

1. foreignSysPersonId在Marketo UI中不可见。 因此，最佳实践是也使用此值填充自定义属性字段。
1. foreignSysPersonId对于潜在客户是唯一的，但一个潜在客户可以有多个foreignSysPersonId。
1. 无法更新或删除foreignSysPersonId，但可以将其重新分配给其他记录。

我们演示了如何调用[syncMultipleLeads API](/help/soap-api/syncmultipleleads.md)以将foreignSysPersonId值写入Marketo中的两个现有潜在客户记录。 **如何使用syncMultipleLeads API编写foreignSysPersonId**&#x200B;您可以插入新的潜在客户记录并指定foreignSysPersonId。 您还可以通过指定Marketo ID和foreignSysPersonId将其添加到现有潜在客户。 我们将向您介绍后一种情况。 **请求syncMultipleLeads SOAP API调用的XML**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<SOAP-ENV:Envelope xmlns:SOAP-ENV="<http://schemas.xmlsoap.org/soap/envelope/>" xmlns:ns1="<http://www.marketo.com/mktows/">
  <SOAP-ENV:Header>
    <ns1:AuthenticationHeader>
      <mktowsUserId>demo17_1_809934544BFABAE58E5D27</mktowsUserId>
      <requestSignature>b5e21953ae9f1b263e644da5eccce9ff33802513</requestSignature>
      <requestTimestamp>2013-08-01T18:22:30-07:00</requestTimestamp>
    </ns1:AuthenticationHeader>
  </SOAP-ENV:Header>
  <SOAP-ENV:Body>
    <ns1:paramsSyncMultipleLeads>
      <leadRecordList>
        <leadRecord>
          <leadId>1090240</leadId>
          <foreignSysPersonId>1224191</foreignSysPersonId>
          <leadAttributeList>
            <attribute>
              <attrName>Company</attrName>
              <attrValue>Marketo1000</attrValue>
            </attribute>
            <attribute>
              <attrName>Phone</attrName>
              <attrValue>650-555-1000</attrValue>
            </attribute>
          </leadAttributeList>
        </leadRecord>
        <leadRecord>
          <leadId>1090239</leadId>
          <foreignSysPersonId>1224192</foreignSysPersonId>
          <leadAttributeList>
            <attribute>
              <attrName>Company</attrName>
              <attrValue>Marketo1001</attrValue>
            </attribute>
            <attribute>
              <attrName>Phone</attrName>
              <attrValue>650-555-1001</attrValue>
            </attribute>
          </leadAttributeList>
        </leadRecord>
      </leadRecordList>
      <dedupEnabled>true</dedupEnabled>
    </ns1:paramsSyncMultipleLeads>
  </SOAP-ENV:Body>
</SOAP-ENV:Envelope>
```

**响应syncMultipleLeads SOAP API调用的XML**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<SOAP-ENV:Envelope xmlns:SOAP-ENV="<http://schemas.xmlsoap.org/soap/envelope/>" xmlns:xsi="<http://www.w3.org/2001/XMLSchema-instance>" xmlns:ns1="<http://www.marketo.com/mktows/">
  <SOAP-ENV:Body>
    <ns1:successSyncMultipleLeads>
      <result>
        <syncStatusList>
          <syncStatus>
            <leadId>1090240</leadId>
            <status>UPDATED</status>
            <error xsi:nil="true" />
          </syncStatus>
          <syncStatus>
            <leadId>1090239</leadId>
            <status>UPDATED</status>
            <error xsi:nil="true" />
          </syncStatus>
        </syncStatusList>
      </result>
    </ns1:successSyncMultipleLeads>
  </SOAP-ENV:Body>
</SOAP-ENV:Envelope>
```

**请参见下面输出上述请求XML的示例Ruby程序。**

```java
require 'savon' # Use version 2.0 Savon gem
require 'date'

mktowsUserId = "" # CHANGE ME
marketoSecretKey = "" # CHANGE ME
marketoSoapEndPoint = "" # CHANGE ME
marketoNameSpace = "<http://www.marketo.com/mktows/>"

# Create Signature
Timestamp = DateTime.now
requestTimestamp = Timestamp.to_s
encryptString = requestTimestamp + mktowsUserId
digest = OpenSSL::Digest.new('sha1')
hashedsignature = OpenSSL::HMAC.hexdigest(digest, marketoSecretKey, encryptString)
requestSignature = hashedsignature.to_s

# Create SOAP Header
headers = {
 'ns1:AuthenticationHeader' => { "mktowsUserId" => mktowsUserId, "requestSignature" => requestSignature,
 "requestTimestamp"  => requestTimestamp
 }
}

client = Savon.client(wsdl: '<http://app.marketo.com/soap/mktows/2_3?WSDL>', soap_header: headers, endpoint: marketoSoapEndPoint, open_timeout: 90, read_timeout: 90, namespace_identifier: :ns1, env_namespace: 'SOAP-ENV')

# Create Request
request = {
        :lead_record_list => {
                :lead_record => {
                        :lead_id => "1090240",
                        :foreign_sys_person_id => "1224191",
                :lead_attribute_list => {
                        :attribute => {
                                :attr_name => "Company",
                                :attr_value => "Marketo1000" },
                         :attribute! => {
                                :attr_name => "Phone",
                                :attr_value => "650-555-1000" }
                }
        },
        :lead_record! => {
                        :lead_id => "1090239",
                        :foreign_sys_person_id => "1224192",
                :lead_attribute_list => {
                        :attribute => {
                                :attr_name => "Company",
                                :attr_value => "Marketo1001" },
                         :attribute! => {
                                :attr_name => "Phone",
                                :attr_value => "650-555-1001" }
                }
        }
        },
    :dedup_enabled => "True"
}

response = client.call(:sync_multiple_leads, message: request)

puts response
```

本文包含用于实施自定义集成的代码。 由于其自定义性质，Marketo技术支持团队无法对自定义工作进行故障诊断。 如果没有适当的技术经验或联系经验丰富的开发人员，请勿尝试实施以下代码示例。

由&#x200B;_Murta_&#x200B;发布于&#x200B;_2014-06-27_

## 更新潜在客户的电子邮件地址

假设一位用户填写了您网站上的Marketo表单。 会发生什么？ Marketo对用户进行Cookie，并将其与用户提供的电子邮件关联。 如果用户下次访问您的网站时，再次使用不同的电子邮件填写相同的表单，该怎么办？ 接下来会发生什么？ Marketo将创建新的潜在客户记录，并覆盖用户浏览器上的第一个Cookie。 用户现在是Marketo中的新/其他潜在客户。 我们为您介绍了四种在Marketo中更新商机电子邮件地址的方法，包括[syncLead API方法](/help/soap-api/synclead.md)、表单方法中的自定义字段、Marketo UI以及导入列表。 **通过syncLead API**&#x200B;您可以使用[syncLead API](/help/soap-api/synclead.md)使用其Marketo ID和新电子邮件地址更新潜在客户记录。 请求`syncMultipleLeads`SOAP API调用的XML

```xml
<?xml version="1.0" encoding="UTF-8"?>
<SOAP-ENV:Envelope xmlns:SOAP-ENV="<http://schemas.xmlsoap.org/soap/envelope/>" xmlns:ns1="<http://www.marketo.com/mktows/">
  <SOAP-ENV:Header>
    <ns1:AuthenticationHeader>
      <mktowsUserId>bigcorp1_461839624B16E06BA2D663</mktowsUserId>
      <requestSignature>92f05a7be4838ae1c0e5aafe814891ee72968a08</requestSignature>
      <requestTimestamp>2013-07-31T12:38:47-07:00</requestTimestamp>
    </ns1:AuthenticationHeader>
  </SOAP-ENV:Header>
  <SOAP-ENV:Body>
    <ns1:paramsSyncLead>
      <leadRecord>
        <leadId>1090240</leadId>
        <Email>t@t.com</Email>
      </leadRecord>
      <returnLead>false</returnLead>
    </ns1:paramsSyncLead>
  </SOAP-ENV:Body>
</SOAP-ENV:Envelope>
```

响应syncMultipleLeads SOAP API调用的XML

```xml
<?xml version="1.0" encoding="UTF-8"?>
<SOAP-ENV:Envelope xmlns:SOAP-ENV="<http://schemas.xmlsoap.org/soap/envelope/>" xmlns:xsi="<http://www.w3.org/2001/XMLSchema-instance>" xmlns:ns1="<http://www.marketo.com/mktows/">
  <SOAP-ENV:Body>
    <ns1:successSyncLead>
      <result>
        <leadId>1090240</leadId>
        <syncStatus>
          <leadId>1090240</leadId>
          <status>UPDATED</status>
          <error xsi:nil="true" />
        </syncStatus>
        <leadRecord xsi:nil="true" />
      </result>
    </ns1:successSyncLead>
  </SOAP-ENV:Body>
</SOAP-ENV:Envelope>
```

请参阅下面的示例Ruby程序，该程序将输出上述请求XML。

```java
require 'savon' # Use version 2.0 Savon gem
require 'date'

mktowsUserId = "" # CHANGE ME
marketoSecretKey = "" # CHANGE ME
marketoSoapEndPoint = "" # CHANGE ME
marketoNameSpace = "<http://www.marketo.com/mktows/>"

# Create Signature
Timestamp = DateTime.now
requestTimestamp = Timestamp.to_s
encryptString = requestTimestamp + mktowsUserId
digest = OpenSSL::Digest.new('sha1')
hashedsignature = OpenSSL::HMAC.hexdigest(digest, marketoSecretKey, encryptString)
requestSignature = hashedsignature.to_s

# Create SOAP Header
headers = {
 'ns1:AuthenticationHeader' => { "mktowsUserId" => mktowsUserId, "requestSignature" => requestSignature,
 "requestTimestamp"  => requestTimestamp
 }
}

client = Savon.client(wsdl: '<http://app.marketo.com/soap/mktows/2_3?WSDL>', soap_header: headers, endpoint: marketoSoapEndPoint, open_timeout: 90, read_timeout: 90, namespace_identifier: :ns1, env_namespace: 'SOAP-ENV')

# Create Request
request = {
 :lead_record => {
  :Email => "<t@t.com>",
  :lead_id => "1090240",
 :return_lead => "false"
}

response = client.call(:sync_lead, message: request)

puts response
```

**通过表单中的自定义字段**&#x200B;您可以在Marketo中为“新电子邮件地址”创建自定义字段。 然后要求用户填写包含此新字段的表单。 然后，在Marketo中创建一个程序，当新的自定义字段“新电子邮件地址”发生更改时，该程序将使用令牌`{{lead.newEmailAddress}}`更改系统电子邮件地址字段的数据值。 **通过Marketo UI**&#x200B;您可以通过Marketo UI手动更新潜在客户的电子邮件地址。 这是一篇[帮助文章](https://nation.marketo.com/)，描述如何执行此操作(查看文章需要Marketo登录)。 **通过导入列表**&#x200B;您可以使用Marketo中的import a list方法更新商机的电子邮件地址，如[此处](https://nation.marketo.com/)所述(需要通过Marketo登录才能查看文章)。  

本文包含用于实施自定义集成的代码。 由于其自定义性质，Marketo技术支持团队无法对自定义工作进行故障诊断。 如果没有适当的技术经验或联系经验丰富的开发人员，请勿尝试实施以下代码示例。

由&#x200B;_Murta_&#x200B;发布于&#x200B;_1970-01-01_

## 存储潜在客户的第二个电子邮件地址

假设您想使用API更改Marketo中的商机分数。 可以通过创建/更新潜在客户端点来处理REST API。 如果要在一个潜在客户记录中存储多个电子邮件，则需要创建一个自定义字段，并将第二封电子邮件存储在该处。 以下是一篇提供更多信息的帮助文章：以下是Ruby中的一个代码示例，说明了如何进行此调用。

```java
require 'rest_client'
require 'json'

# Build request URL
# Replace AAA-BBB-CCC with your Marketo instance
marketo_instance = "<https://AAA-BBB-CCC.mktorest.com>"
endpoint = "/rest/v1/leads.json"
# Replace with your access token
auth_token =  "?access_token=" + "ac756f7a-d54d-41ac-8c3c-f2d2a39ee325:ab"
request_url = marketo_instance + endpoint + auth_token

# Build request body
data = { "action" => "updateOnly", "input" => [ { "email" => "<example@email.com>", "leadScore" => "30" } ] }

# Make request
response = RestClient.post request_url, data.to_json, :content_type => :json, :accept => :json

# Returns Marketo API response
puts response
```

由&#x200B;_Murta_&#x200B;发布于&#x200B;_2015-02-20_

## 在Marketo中创建自定义字段，并通过AP更新此字段

假设您拥有其他不符合Marketo标准字段的销售线索数据。 例如，此自定义字段可以是第三方得分。 您可以在Marketo中创建用于计算第三方得分的自定义字段，然后通过Marketo [REST API](https://developer.adobe.com/marketo-apis/)或[SOAP API](https://experienceleague.adobe.com/en/docs/marketo-developer/marketo/soap/activity-type-filters)更新此字段的值。 我们首先介绍如何在Marketo中创建自定义字段，然后介绍如何使用REST API更新此字段。

### 如何在Marketo中创建自定义字段

1. 在“管理员”下，单击“字段管理”。
1. 单击“新建自定义字段”按钮。
1. 选择字段类型。 这会更改它在Marketo的智能列表和Forms中的呈现方式。
1. 输入您希望在Marketo中显示的名称。 请仔细选择名称和API名称，因为重命名字段可能很困难，并且在某些情况下不可能。
1. 您创建的自定义字段现在可以通过API访问。

### 如何通过REST API更新自定义字段

在上一部分中，我们使用数据类型字符串创建了一个名为`myCustomField`的自定义字段。 要更新该字段的值，我们使用名为“创建/更新潜在客户”的REST API端点。 在向REST API发出请求之前，您需要进行身份验证。 这超出了本文的范围，但Marketo开发人员网站[上提供了深入的信息](/help/rest-api/authentication.md)。

**终结点**

`/rest/v1/leads.json`

**请求正文**

```json
{
   "action":"createOrUpdate",
   "input":[
      {
         "email":"<example@example.com>",
         "myCustomField":"examplestring"
      }
   ]
}
```

本文包含用于实施自定义集成的代码。 由于其自定义性质，Marketo技术支持团队无法对自定义工作进行故障诊断。 如果没有适当的技术经验或联系经验丰富的开发人员，请勿尝试实施以下代码示例。

由&#x200B;_Murta_&#x200B;发布于&#x200B;_2014-08-19_

## 集成Unbounce和Marketo

**注意：这是Fab Capodicasa的访客博客文章。 他是[Hoosh Marketing](http://hooshmarketing.com.au/)的Marketo认证顾问，这是Marketo LaunchPoint代理合作伙伴，专门研究B2C。 过去13年，他同时在SaaS和营销部门工作。 他的背景融合了核心IT、直接营销和企业销售。 Fab也是前Marketo员工。**

**概述**在本文中，我们将演示如何将常用的登陆页面工具Unbounce与Marketo集成。 我们首先向您说明如何将Marketo跟踪插入到“退回”中，然后介绍如何修改“退回”表单以将数据直接插入到Marketo中。 将弹回与Marketo集成的挑战是，弹回不允许重命名默认字段（例如，first_name无法更改为FirstName）。 它也不允许字段标签与字段名称不同。 此集成涉及JavaScript，该集成会调整现有表单以使其与Marketo兼容。 我建议您至少具有JavaScript初学者级别和中级的Marketo知识才能完成本文中的任务。
**第1部分：将Marketo跟踪代码添加到退回**要使Analytics和表单集成正常工作，需要将Marketo的Munchkin跟踪脚本添加到退回页面。 请按照以下步骤操作：从Marketo复制Munchkin代码：导航到管理员 — > Munchkin，并复制JavaScript的“简单”版本。 打开“退回”登陆页面，然后单击“JavaScript” — >“添加新JavaScript”。  单击添加，将脚本命名为“Munchkin”，选择“在Body结束标记之前”，然后粘贴Munchkin代码。 单击“Done（完成）”按钮。 对于将来的弹回页面，请转到JavaScript并启用我们创建的Munchkin脚本。 无需重新创建。
**第2部分：将“退回”表单转换为Marketo表单**&#x200B;现在，我们需要通过添加一些新的隐藏字段和JavaScript来修改“退回”表单，以允许您的“退回”登陆页面将潜在客户信息直接提交到Marketo。 我们将首先创建一个Marketo占位符表单。 在Marketo中，创建一个空白表单并批准它。

这是Marketo中代表“退回”表单的代理表单。 将隐藏字段添加到退件表单。 Marketo需要这些隐藏字段来确定将应用此表单提交的Marketo实例、表单和用户会话。 在弹回中，双击以打开表单。 添加名为`_mkt_trk`的隐藏字段。 添加第二个名为`formid`的隐藏字段。  233需要替换为您的表单ID，您可在Marketo中的Marketo表单嵌入代码中找到该ID。 在Marketo中，打开表单，选择表单操作 — >嵌入代码。 添加名为`returnurl`的隐藏字段。 `http://hooshmarketing.com.au/thank-you`需要替换为跟进URL，这是您希望用户在提交表单后重定向到的URL。 例如，这可能是您的感谢页面。
**第3部分：直接将表单退回Marketo**&#x200B;跟进URL是潜在客户提交到Marketo后，您的潜在客户将被重定向到的页面。 在弹回中，请按照以下步骤操作：单击您的表单。 修改表单确认部分。 更改确认以将表单数据发布到URL。 将URL设置为您想要的后续页面。 `fpmarkets`需要替换为您的Marketo帐户字符串，该字符串可在Marketo中的“管理员” — >“登陆页面”下找到。
**第4部分：将JavaScript添加到“退回”页面**&#x200B;此JavaScript会将表单转换为与Marketo兼容并将其提交到Marketo。 在弹回中，请按照以下步骤操作：打开弹回登陆页面，然后单击JavaScript ->添加新JavaScript 。 单击添加，将脚本命名为“Marketo Form Convert”，选择“Before Body End Tag”。 粘贴下面的JavaScript代码：

```javascript
var MARKETO_MUNCHKIN_ID='614-CGT-700';
var MARKETO_ACCOUNT_STRING = 'fpmarkets';

var UNBOUNCE_MARKETO_FIELD_MAP = new Object();

//default field mappings
UNBOUNCE_MARKETO_FIELD_MAP['first_name'] = 'FirstName';
UNBOUNCE_MARKETO_FIELD_MAP['email'] = 'Email';
UNBOUNCE_MARKETO_FIELD_MAP['last_name'] = 'LastName';
UNBOUNCE_MARKETO_FIELD_MAP['phone_number'] = 'Phone';

function getMarketoField(k) {
    return UNBOUNCE_MARKETO_FIELD_MAP[k];
}


var formFields = [];
var hiddenClonedFields = [];
var firstForm = document.forms[0];

//Convert Unbounce form names to Marketo field names
for(i=0; i<firstForm.elements.length; i++){
 var formField = firstForm.elements[i];
 var newFieldName = getMarketoField(formField.name);

  if(newFieldName != undefined) {


    //save original field as hidden field
    var hiddenField = document.createElement("input");
    hiddenField.setAttribute("type", "hidden");
    hiddenField.setAttribute("name", formField.name);
    hiddenField.setAttribute("id", formField.id);
    hiddenClonedFields.push(hiddenField);

    //change original field
    console.log ( 'Changed form field name: ' + formField.name + '=>' + newFieldName );
    formField.name = newFieldName;
    formField.id = newFieldName;
    formFields.push(formField);


  } else {
    console.log ( 'Couldn't map:' + formField.name );
  }
}

//Add hidden cloned Unbounce fields to form
//for Unbounce validation
for(i=0; i<hiddenClonedFields.length; i++){
    firstForm.appendChild(hiddenClonedFields[i]);
    formFields[i].onchange = (function(hf) {
        return function(event) {
            hf.value = event.target.value;
          console.log('Changed hidden cloned field:' + hf.name + '=>' + hf.value);
        };
    }(hiddenClonedFields[i]));
    console.log ( 'Added cloned field: ' + hiddenClonedFields[i].name );
}


//Add MunchkinId to form
var input = document.createElement("input");
input.setAttribute("type", "hidden");
input.setAttribute("name", "munchkinId");
input.setAttribute("value", MARKETO_MUNCHKIN_ID);
firstForm.appendChild(input);
console.log('Added hidden field:' + input.name + '=' + input.value);
```

如果您的自定义字段的API名称与退件不兼容，则可以将这些字段添加到JavaScript中的映射。 例如，如果您在Marketo中的某个自定义字段是`Comments_c`，但您希望该字段标签显示为评论，则取消退回不允许您将字段名称更改为`Comments_c`。

```
//default field mappings
UNBOUNCE_MARKETO_FIELD_MAP['comments'] = 'Comments_c';
UNBOUNCE_MARKETO_FIELD_MAP['first_name'] = 'FirstName';
UNBOUNCE_MARKETO_FIELD_MAP['email'] = 'Email';
```

_comments是“退回”中字段的名称。 _Comments_c_是Marketo中的字段名称。 对于未来的退回页面，您只需转到JavaScript并启用我们创建的Munchkin脚本即可。 无需重新创建。
**第5部分：测试**&#x200B;最后一步是测试此表单集成是否正常工作。 在Marketo中创建一个在Marketo表单上激活的触发器，并填写和确保潜在客户正确地插入到Marketo中。 提交表单后，页面应该会将您重定向到跟进URL。

由_于&#x200B;_2014-08-04_&#x200B;发布

## 2014年7月发行版更新

### Munchkin更新

Munchkin的新版本是141。 版本142不受支持，并将在2011年9月初删除。 在版本142中，存在未在开发人员网站上记录的公开可访问函数。 这些未记录的功能不再公开可用。 从长远来看，我们将继续支持开发人员网站上的文档功能。

### RTP更新

RTP API具有称为获取访客数据的新函数。 利用此RTP API调用，可获取实时访客数据，例如组织、行业、位置和区段代码匹配。

由&#x200B;_Murta_&#x200B;发布于&#x200B;_2014-07-30_

## 在单个页面上使用多个Munchkin跟踪代码

假设您有多个Marketo实例，并且希望发送Web跟踪事件（如页面访问次数或指向这些多个实例的点击链接），则可以使用Munchkin完成此操作。 Marketo按域（例如“marketo.com”）跟踪您网站的访客。 如果您在非主域（例如“company.com”）上托管此Munchkin脚本，则这些访客将显示为匿名潜在客户，直到他们在该其他域上填写表单为止。 要完成此操作，请将`altIds`参数添加到您的`Munchkin.init`调用。 altIds参数包含应从中发送Web事件的其他Munchkin ID数组。 使用下面的示例，将突出显示的Munchkin ID (XXX-XXX-XXX、YYYY-YYY-ZZZ-ZZZ)替换为应发送跟踪信息的每个Marketo实例中的Munchkin ID。

```javascript
<script src="http://munchkin.marketo.net/munchkin.js" type="text/javascript"></script>
<script>Munchkin.init('XXX-XXX-XXX', { altIds:['YYY-YYY-YYY', 'ZZZ-ZZZ-ZZZ'] });</script>
```

有关Munchkin初始化参数的详细信息，请参阅[本文档](/help/javascript-api/configuration.md)。

由&#x200B;_Murta_&#x200B;发布于&#x200B;_2014-08-08_

## 将Munchkin与Google Tag Management集成

Google Tag Manger允许您向网站添加标记。 您可以将[Google标签管理器](https://marketingplatform.google.com/about/tag-manager/)放置在您的网站上，然后通过Munchkin标签管理器的UI添加诸如[Munchkin](/help/javascript-api/lead-tracking.md)之类的标签，而不是将每个跟踪脚本(如Google)手动添加到您网站的源代码中。 在本帖子中，我们将首先介绍如何在Marketo中生成Munchkin跟踪代码，然后介绍如何将此Munchkin跟踪代码添加到Google标签管理器。

### 如何生成Munchkin跟踪代码

1. 单击应用程序右上角的&#x200B;**管理员**。
1. 单击左侧树中的&#x200B;**Munchkin**。
1. 选择&#x200B;**Asynchronous**&#x200B;以跟踪代码类型。
1. 单击并复制JavaScript跟踪代码。

**如何将Munchkin跟踪代码添加到Google Tag Manager**

1. 登录到您的Google Tag Manager帐户并&#x200B;**添加新标记。**
1. 创建新的&#x200B;**自定义HTML标记**。
1. 将您的Munchkin代码复制并粘贴到&#x200B;**HTML**&#x200B;字段中，然后单击&#x200B;**继续**。
1. 选择&#x200B;**在所有页面上触发**，然后单击&#x200B;**创建标记。**&#x200B;注意：如果您的网站流量非常高，可以使用&#x200B;**在某些页面上触发**&#x200B;排除网站的某些部分。
1. 单击保存，然后验证是否正在您的网站上加载Munchkin跟踪代码。

本文包含用于实施自定义集成的代码。 由于其自定义性质，Marketo技术支持团队无法对自定义工作进行故障诊断。 如果没有适当的技术经验或联系经验丰富的开发人员，请勿尝试实施以下代码示例。

由&#x200B;_Murta_&#x200B;发布于&#x200B;_2014-08-05_

## 提交表单后隐藏Lightbox

### 概述

提交表单时，Marketo生成的当前Lightbox嵌入代码没有消失。 在提交表单后它将重新加载页面，并且表单将再次显示。 如果要创建一个在提交表单后隐藏的灯箱，请按照以下步骤操作。

### 操作指南

1. 在Marketo中创建表单后，生成嵌入代码。 为此，请单击“表单操作”，然后单击“灯箱”作为代码类型。 将此代码复制并粘贴到文本编辑器中，以便在下一步中进行编辑。
1. 将嵌入代码中“(form)”之后的所有代码替换为以下代码：

```javascript
{
var lightbox = MktoForms2.lightbox(form).show();
form.onSuccess(function(){
lightbox.hide();
return false;
});
});
</script>
```

在步骤2之后，完成的版本应该类似于下面的代码。 代码现已准备就绪，可在您的网站上使用。

```javascript
<script src="http://app-sj04.marketo.com/js/forms2/js/forms2.js"></script>
<form id="mktoForm_0000"></form>
<script>MktoForms2.loadForm("http://app-sj04.marketo.com", "AAA-BBB-CCC", 0000, function (form){
var lightbox = MktoForms2.lightbox(form).show();
form.onSuccess(function(){
lightbox.hide();
return false;
});
});
</script>
```

本文包含用于实施自定义集成的代码。 由于其自定义性质，Marketo技术支持团队无法对自定义工作进行故障诊断。 如果没有适当的技术经验或联系经验丰富的开发人员，请勿尝试实施以下代码示例。

由&#x200B;_Murta_&#x200B;发布于&#x200B;_2014-08-21_

## Marketo REST API快速入门指南

本指南向您展示如何在10分钟内首次调用Marketo REST API。 我们将向您展示如何使用[通过ID获取潜在客户](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getLeadByIdUsingGET) REST API端点检索单个潜在客户。 为此，我们将引导您完成身份验证过程以生成访问令牌，您可以使用访问令牌向[按ID获取潜在客户](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getLeadByIdUsingGET)发出HTTP GET请求。 然后，我们为您提供用于发出请求的代码，该请求会返回格式为JSON的潜在客户信息。

### 如何生成身份验证令牌

>[!NOTE]
> 自2025年6月起，不再支持身份验证令牌。 必须使用身份验证标头。
>

Marketo中的自定义服务允许您描述和定义应用程序有权访问的数据。 您需要以Marketo管理员身份登录才能创建自定义服务，并将该服务与单个“仅限API”用户关联。

1. 导航到Marketo应用程序的管理区域。
1. 单击左侧面板上的“用户和角色”节点。
1. 创建新角色。 通过单击访问API显示角色权限列表。 现在，向下滚动并仅选择所需的权限。 在这种情况下，我们将只选择“只读潜在客户”权限。
1. 下一步是创建仅限API的用户，并将其与您在上一步中创建的API角色相关联。 为此，您可以在创建用户时选中仅限API的用户复选框。
1. 需要自定义服务来唯一标识您的客户端应用程序。 要创建自定义应用程序，请转到“管理员”>“LaunchPoint”屏幕并创建新服务。
1. 提供显示名称，选择“自定义”服务类型，提供说明以及在步骤1中创建的用户电子邮件地址。 我们建议使用能代表此自定义REST API服务的公司或目的的描述性显示名称。
1. 单击网格上的“查看详细信息”链接以获取客户端ID和客户端密钥。 您的客户端应用程序能够使用客户端ID和客户端密钥生成访问令牌。
1. 将您的身份验证令牌复制并粘贴到文本编辑器中。 您的身份验证令牌类似于以下示例：

`cdf01657-110d-4155-99a7-f986b2ff13a0:int`

### 如何确定端点URL

在向Marketo API发出请求时，您需要在端点URL中指定Marketo实例。 对Marketo REST API的所有非批量API请求将遵循以下格式：

`<REST API Endpoint URL>/rest/`

可以在Marketo管理员> Web服务面板中找到REST API端点URL。 您的Marketo端点URL结构应类似于以下示例：

`https://100-AEK-913.mktorest.com/rest/v1/lead/{id}.json`

**注意：批量API URL未添加前缀“/rest/”。 对于批量API，您应该只使用主机，并附加适当的路径，如：**

`https://100-AEK-913.mktorest.com/bulk/v1/leads/export/create.json`

### 如何使用身份验证令牌调用Get Lead by Id AP

在前面的部分中，我们生成了一个身份验证令牌并找到了端点URL。 我们现在将向REST API终结点发出请求，该终结点名为[按ID获取潜在客户](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getLeadByIdUsingGET)。 向Marketo REST API发出请求的最简单方法是，将URL粘贴到网页浏览器地址栏中。 请遵循以下格式：

`https://<REST API Endpoint URL for your Marketo instance>/rest/v1/<API that you are calling>?access_token=<access_token>`

### 示例

`https://100-AEK-913.mktorest.com/rest/v1/lead/318581.json?access_token=cdf01657-110d-4155-99a7-f986b2ff13a0:int`

如果调用成功，则会返回具有以下格式的JSON：

```json
{
    "requestId": "d82a#14e26755a9c",
    "result": [
        {
            "id": 318581,
            "updatedAt": "2015-06-11T23:15:23Z",
            "lastName": "Doe",
            "email": "<jdoe@marketo.com>",
            "createdAt": "2015-03-17T00:18:40Z",
            "firstName": "John"
        }
    ],
    "success": true
}
```

如果您有兴趣了解有关Marketo REST API的更多信息，这是[很好的起点](https://developer.adobe.com/marketo-apis/)。

由&#x200B;_David_&#x200B;发布于&#x200B;_2015-09-04_

## 如何删除Marketo跟踪Cookie

问题：“我们在网站上为销售团队设置了一个表格，用于取消订阅口头要求从任何电子邮件消息中移除的个人。 我们的发现是，当他们在电子邮件中输入并提交，表示他们已使用该电子邮件地址进行编码，并开始在我们的网站上接收有关其活动的警报时。 我们目前使用的表单是嵌入表单。 有人想出了在嵌入式表单上禁用烹饪跟踪的方法，我理解如何使用Marketo登陆页面来实现这一点。” 我们能做到的。 要实施此操作，请加快Cookie过期时间。 表单创建Cookie后，您可以强制其立即过期。 由于Cookie已过期，因此浏览器会自动将其删除。 要开始此过程，我们将使用本机表单功能在名为`deleteCookie`的函数下调用。

由&#x200B;_Travis Kaufman_&#x200B;发布于&#x200B;_2014-08-26_

## 将Marketo登录页面与Wordpress集成

假设您的网站是使用Wordpress构建的，并且您希望在其中一个页面上嵌入Marketo登陆页面。 可以使用iframe实现此功能。 iframe允许您将页面嵌入到其他页面中。 在本帖子中，您将展示如何将Marketo登录页面嵌入到Wordpress页面中。 **选择Wordpress插件** Wordpress这里有许多Wordpress插件可供您使用： `http://wordpress.org/plugins/iframe/`以下是使用此方法的一些专业人员和骗子： `http://www.elixiter.com/marketo-landing-page-and-form-hosting-options/`Murtza之后非常好！ Colby，iframe将保留提交表单后您被重定向到PDF的部分。 我们在我们的网站上使用iframe很长一段时间。 穆尔察后很棒！ Colby，iframe将保留提交表单后您被重定向到PDF的部分。 我们在我们的网站上使用iframe很长一段时间。 请注意，如果要将URL参数从主URL传递到iframe，则需要执行一些编码。 此外，请确保您的Google Analytics设置正确。 您不希望每次使用iframe访问页面时，页面查看次数计算两次。

由&#x200B;_Murta_&#x200B;发布于&#x200B;_1970-01-01_

## 优化地与Marketo登录页面集成

[最佳化](https://www.optimizely.com/)使您能够在您的网站上进行A/B测试、多页面测试和多变量测试。 您可以优化地用于Marketo登陆页面。 以下是操作方法：

1. 查找并复制您的Optimizely代码片段。**以优化方式转到您的功能板，然后单击项目代码链接。 复制弹出窗口中显示的代码行。
1. 登录Marketo，然后选择您的登陆页面模板。 然后单击“编辑草稿”。**
1. 单击登陆页面操作。 然后，单击编辑页面Meta Tags **
1. 将您的优化代码段粘贴到自定义HEAD HTML部分，然后单击保存。
1. 测试您的登陆页面，以确认Optimizly代码片段正在运行

由&#x200B;_Murta_&#x200B;发布于&#x200B;_2014-09-18_

## 通过Marketo REST API按全名搜索

**问题：**是否可以通过只使用商机全名的Marketo API来查询商机？
**答案：**&#x200B;不能直接回答。 但是，下面介绍的解决方法允许您执行此操作。

1. 在Marketo中创建一个名为“Fullname”的自定义字段。
1. 使用[getMultipleLeads](/help/soap-api/getmultipleleads.md) SOAP API或[按筛选器类型](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getLeadByIdUsingGET)获取多个潜在客户以查询您的潜在客户数据库。 在对REST或SOAP API的请求中将您的名字和姓氏作为属性包含。
1. 查询潜在客户数据库后，将每个潜在客户的“名字”和“姓氏”连接起来，并将这些数据存储在“全名”列中。 1.使用[syncMultipleLeads](/help/soap-api/syncmultipleleads.md) SOAP API将此数据推送到“Fullname”自定义字段。 或者，您可以使用[Import Lead](/help/rest-api/leads.md) API，或使用Marketo UI导入CSV或XLS。
1. 现在，您可以使用[按筛选器类型API获取多个潜在客户](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getLeadsByFilterUsingGET)来按全名查询，以搜索此自定义字段。 指定“Fullname”为“filterType”，“filterValue”将为“Joe Johnson”，其中包含“按过滤器类型获取多个潜在客户”REST API调用。

由&#x200B;_Murta_&#x200B;发布于&#x200B;_2014-09-09_

## 删除Marketo跟踪Cookie

仅当所需的效果是完全删除Cookie时，才应使用此方法。

此代码示例可用于在提交Marketo表单后从用户浏览器中删除Marketo跟踪Cookie。 它通过在用户提交表单后调用`deleteMarketoCookie`方法来工作。 此方法通过将过期日期设置为过去的日期来过期Cookie。 浏览器的默认行为是删除此Cookie，因为它已过期。

由&#x200B;_Murta_&#x200B;发布于&#x200B;_2014-09-09_

## 限制表单填写时可用的电子邮件域

假设您想限制网站访客提交带有免费电子邮件域（如Gmail或Yahoo）的表单。 要完成此任务，请在Marketo表单页面的源代码中包含以下脚本。 它会检查用户是否输入了非业务电子邮件（Gmail、Hotmail等），并在用户输入时阻止提交Marketo表单。 您可以通过修改第9行来扩展阻止的电子邮件域的列表，以包含要限制的域。

由&#x200B;_Murta_&#x200B;发布于&#x200B;_2014-09-09_

## 调试无法通过API访问的字段

如果您转到此字段<https://nation.marketo.com/>。当我们尝试使用SOAP API更新字段AnnualRevenue时，响应指出记录已更新，但AnnualRevenue字段不会保留更改。 如果我们尝试使用Lead数据库手动更新该字段，则对此字段的更改也不会保留。 检查Admin中是否阻止了该字段。 有时还可能会发生以下情况：可能是从SFDC同步的“帐户”字段。 默认情况下，我们阻止对这些字段的更改，因为SFDC在许多情况下是这些字段的记录系统。 1)创建于2)Marketo SFDC ID 3)Marketo唯一代码4)SFDC类型5)更新于6)紧急情况7)优先级8)创建日期

由&#x200B;_Murta_&#x200B;发布于&#x200B;_1970-01-01_

## 将自定义代码添加到Marketo登录页面

假设您想要将第三方跟踪脚本(例如Google Analytics)添加到您的Marketo登陆页面。 可以通过Marketo UI执行此操作。 一般来说，您可以按照以下说明将任何自定义代码(HTML、CSS、JavaScript)添加到Marketo登陆页面。

1. 选择您的登陆页面，然后单击编辑草稿。
1. 拖入HTML元素。
1. 输入您的自定义代码，然后单击Save。

由&#x200B;_Murta_&#x200B;发布于&#x200B;_2014-09-10_

## 服务器端表单帖子

`https://community.marketo.com/MarketoResource?id=kA650000000GsXXCA0`

由&#x200B;_Murta_&#x200B;发布于&#x200B;_2014-09-11_

## 从Forms 2.0提交内容中清除Marketo跟踪Cookie

### 概述

Forms 1.0包含Munchkin跟踪Cookie的值作为DOM中的字段。 这是与所有其他输入一起提交的。 [Forms 2.0](/help/javascript-api/forms-api-reference.md)忽略此字段，并在提交时而不是在加载表单时动态填充该值。 虽然这通常是可以接受的，但它确实会创建一类用例，这些用例需要清除跟踪Cookie以避免意外跟踪和预填充。 例如，可能会在一次贸易展上发生这种情况，在这种情况下，Marketo客户在同一台设备上使用相同的表单，并从多个人那里获取联系信息。 利用下面的代码片段，可在提交表单时清除Cookie值，而无需从用户的浏览器中删除Cookie本身。

### 代码示例

此代码片段预计页面上只会加载一个表单。 如果有多个表单，则应使用[loadForm或getForm方法](/help/javascript-api/forms-api-reference.md)实施回调。

```javascript
<script>
//add a callback to the first ready form on the page
MktoForms2.whenReady( function(form){
 //add the tracking field to be submitted
        form.addHiddenFields({"_mkt_trk":""});
        //clear the value during the onSubmit event to prevent tracking association
 form.onSubmit( function(form){
  form.vals({"_mkt_trk":""});
 })
})
</script>
```

由&#x200B;_Kenny_&#x200B;发布于&#x200B;_2014-09-11_

## 2014年9月版更新

### REST API的更新

向[获取多个潜在客户](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getLeadsByFilterUsingGET) API添加了新的可选字段值，这将返回与潜在客户记录关联的Munchkin Cookie值。 只需将“？fields=cookie”添加到请求中。

由&#x200B;_Murta_&#x200B;发布于&#x200B;_2014-09-16_

## 将位置数据从RTP API添加到Marketo表单填写

**您需要有效的RTP和MLM订阅才能实施此博客文章中描述的使用案例。**
使用[RTP JavaScript API](/help/javascript-api/web-personalization.md)和[Marketo Forms 2.0](/help/javascript-api/forms-api-reference.md)，您可以从RTP中提取推断的位置数据，并通过填写表单将其推送到Marketo。 这样，您就可以查看在最近一次表单活动期间用户推断的位置（基于IP地址）。 首先，您需要在Marketo中创建三个自定义字符串字段。 如果与Marketo有本机集成，则可以通过您的CRM执行此操作，也可以从Marketo的“管理”部分的“字段管理”菜单中执行此操作。 我建议将这些字段命名为“最新国家/地区”、“最新州”和“最新城市”。 我们将使用此命名约定继续此博客。 这些字段的API名称为“mostRecentCountry”、“mostRecentState”和“mostRecentCity”。 为了检索位置数据，我们使用[RTP方法获取访客的位置数据](/help/javascript-api/web-personalization.md)，然后使用Marketo Forms 2.0中的[addHiddenFields和vals方法](/help/javascript-api/forms-api-reference.md)将其传递到窗体中。在您的页面上，添加您的RTP JS标记和Marketo表单。 然后，包含下面的脚本。 如果使用的命名约定不同于上述命名约定，则需要更改示例代码中目标表单字段的名称。

```javascript
<script>
//modify the form and grab the user
MktoForms2.whenReady( function(form) {
        //add the hidden fields to the form
 form.addHiddenFields({
  "mostRecentCountry":"",
  "mostRecentState":"",
  "mostRecentCity":""});
 //Grab the visitor data, a JS object with it is passed in the callback function of the third argument
 rtp('get','visitor',function(visitor){
  //add the desired info from the visitor object to the form fields
  form.vals({
   "mostRecentCountry":visitor.results.location.country,
   "mostRecentCity":visitor.results.location.city,
   "mostRecentState":visitor.results.location.state});
  }
  );
 });
</script>
```

由&#x200B;_Kenny_&#x200B;发布于&#x200B;_2014-09-17_

## 阻止对Marketo登陆页面进行爬网和搜索索引

假设您想阻止Google等搜索引擎对Marketo登录页面进行爬网并将其显示在结果中。 以下是操作方法：

1. 登录Marketo，然后选择您的登陆页面。 然后单击“编辑草稿”。
1. 单击登陆页面操作。 然后，单击编辑页面Meta Tags
1. 将Robots字段更改为No Index， No Follow。 然后单击“保存”。

由&#x200B;_Murta_&#x200B;发布于&#x200B;_2014-09-19_

## Marketo REST与SOAP API常见问题解答

**更新日期： 2016年3月**&#x200B;以下是有关Marketo [REST](/help/rest-api/rest-api.md)和[SOAP](/help/soap-api/soap-api.md) API的最常见问题解答。 **问：Marketo REST与SOAP API之间有何主要区别？** A：虽然通过REST和SOAP API推送/拉取特定数据的功能通常会重叠，但某些功能仅存在于REST或SOAP API中。 在性能方面，REST API比SOAP API具有更好的[吞吐量](https://en.wikipedia.org/wiki/Throughput)。 在身份验证模型方面，REST API具有一个使用过期令牌的身份验证模型。 我们的REST API还提供了对Marketo [资源](https://developer.adobe.com/marketo-apis/api/asset/)的访问权限。   **问：REST API中有哪些功能在SOAP API中不可用？** A：[列表API的列表](/help/rest-api/list-of-standard-fields.md)、[从列表API中删除潜在客户](/help/rest-api/lead-database.md)、[使用情况API](/help/rest-api/rest-api.md)和[错误API](/help/rest-api/rest-api.md)仅在REST API中可用。 **问：是否有计划增加可用于SOAP API的API数量？** A：否。 **问：是否有计划增加可用于REST API的API数量？** A：是。 REST目前是Marketo API开发的主要重点。

由&#x200B;_Murta_&#x200B;发布于&#x200B;_2014-09-20_

## 服务器端表单帖子

**注意：这是一个非公共的、不受支持的API，它不受支持，并且其行为随时可能更改**&#x200B;如果您在网站上使用自己的表单，则仍可以使用服务器端帖子将此数据发送到Marketo。 此方法的好处是，您可以保留现有的表单和应用程序逻辑，但仍可以在Marketo中使用实际的表单帖子。 这将为Marketo用户提供“填写表单”事件，该事件可用于触发自动化流程或进行分段。 **注意：单个IP地址每分钟有30个服务器端表单帖子的速率限制。**&#x200B;在每个脚本或编程语言中，都有不同的选项来执行服务器端表单提交，并且它们可能有不同的对象/方法可用于进行Post调用。 例如，在PHP中，许多人使用cURL库。 在所有情况下，您都会将名称 — 值对发布到指定的URL。 这些名称需要与Marketo字段的API名称相同。 此外，还需要包含几个系统字段，才能正确捕获表单提交。

1. 创建表单。 第一步是在Marketo中创建表单，或者使用您要提交的现有表单。 表单的名称必须是描述性的，但实际上它不需要任何表单字段。 如果创建新表单，只需输入名称，然后取消选中“打开表单编辑器”框，操作即完成。
1. 查找表单ID。 在Marketo UI中，选择表单并查看URL：其格式应为`https://app-x.marketo.com/#FO8B2ZN12`。 在#符号后面，查看“FO”后面紧接着的数字以查找表单ID。 在这种情况下，表单ID为8。 在某些情况下，您的第一个表单可能编号为1001，并从此处开始计数。 表单ID是一个变量，因此您可以触发不同表单的提交。
1. 获取您的Marketo帐户ID。 转到管理员> Munchkin并复制Munchkin帐户ID，其格式为000-AAA-000。您需要此信息，才能将表单提交到正确的Marketo实例中。
1. 确定发布URL。 在Marketo用户界面中，请注意位置栏中的域，通常采用`<http://app-x.marketo.com/>`格式。 在斜杠后面舍弃所有内容，然后附加“index.php/leadCapture/save”以获取完整的POST URL。 注意1：这区分大小写。 注意2： Marketo沙盒可能与您的生产Marketo系统具有不同的域。 示例URL应为： `http://app-x.marketo.com/index.php/leadCapture/save`您还可以使用HTTPS而不是HTTP（请不要使用CNAME，因为它会引发安全异常）。
1. 查找表单字段名称**转到管理员>字段管理，然后单击“导出字段名称”按钮以下载具有API字段名称的电子表格。 将API名称用作名称 — 值对中的名称。
1. 决定要发布的字段。 您可以在表单提交中包含任何Marketo Lead字段。 请注意，字段名称区分大小写。 除了要提交的字段外，还有两个必填字段和两个建议字段：表单上的必填字段： (1) `munchkinId` — 此字段用于您的Munchkin帐户ID (2) `formid` — 此字段指示Marketo中已提交的表单表单表单上的建议字段： (1)电子邮件 — 此字段用作重复数据删除的主键。 如果Marketo在Marketo数据库中找到匹配的电子邮件地址，它会更新现有记录，否则会创建新记录。 如果有多个匹配项，它会更新最近更新的记录(2) `_mkt_trk` — 此字段包含Cookie信息，因此您可以跟踪个人的网页访问。 如果您的表单页面上有Munchkin，则Munchkin将自动在此隐藏的表单字段中输入值。 如果不能，则从具有相同名称的Cookie中读取它，并将其传递到此字段中的Marketo。 注意：Marketo表单的POST正文必须经过URL编码。
1. 请参阅响应**对表单帖子的响应将是一个HTTP 302重定向代码。 在某些系统中，这将显示为错误。 但是，在这种情况下，这意味着已成功创建或更新Lead。 如果出现错误，您会收到4xx或5xx错误代码。

下面是Justin Cooperman，资深产品经理[关于将此技术用于取消订阅方案](https://nation.marketo.com:443/t5/product-blogs/how-to-build-an-external-subscription-center/ba-p/242185)的[帖子]

由&#x200B;_Murta_&#x200B;发布于&#x200B;_2014-11-07_

## 查找在特定日期范围内更新的潜在客户

假设您要通过[Marketo API](/help/soap-api/soap-api.md)查找在特定日期更新的潜在客户。 使用[getMultipleLeads SOAP API](/help/soap-api/getmultipleleads.md)可以做到这一点。 对于您请求的日期范围，此方法会返回在Marketo中发生数据值更改或新活动的任何潜在客户。 对于`leadSelector`，应指定`LastUpdateAtSelector`。 然后，您将定义时间范围分别为`oldestUpdatedAt`和`latestUpdatedAt`的日期范围。 请参阅下面的请求XML示例，其中显示如何查找在2014年6月6日上午12:00到2011年6月7日上午12:00之间更新的潜在客户。 注意：日期范围不得超过30天。

**用于查找在日期**&#x200B;之前更新的潜在客户的示例请求XML

```xml
<soapenv:Envelope xmlns:xsi="<http://www.w3.org/2001/XMLSchema-instance>" xmlns:soapenv="<http://schemas.xmlsoap.org/soap/envelope/>" xmlns:mkt="<http://www.marketo.com/mktows/">
<soapenv:Header>
<mkt:AuthenticationHeader>
<mktowsUserId>REPLACE</mktowsUserId>
<requestSignature>REPLACE</requestSignature>
<requestTimestamp>2014-10-23T12:19:51-07:00</requestTimestamp>
</mkt:AuthenticationHeader>
</soapenv:Header>
<soapenv:Body>
<mkt:paramsGetMultipleLeads>
<leadSelector xsi:type="mkt:LastUpdateAtSelector">
<oldestUpdatedAt>2014-06-06T00:00:00-08:00</oldestUpdatedAt>
<latestUpdatedAt>2014-06-07T00:00:00-08:00</latestUpdatedAt>
</leadSelector>
</mkt:paramsGetMultipleLeads>
</soapenv:Body>
</soapenv:Envelope>
```

由&#x200B;_Murta_&#x200B;发布于&#x200B;_2014-09-24_

## 向电子邮件添加动态内容

假设您每天发送一封电子邮件，并且您希望在电子邮件模板中自动包含当天的日期。 为此，您需要在Marketo中使用令牌和电子邮件脚本。

1. 创建令牌。**导航到要使用令牌的程序。 单击“我的令牌”。
1. 双击电子邮件脚本。 然后命名此令牌。 然后单击“编辑”。
1. 在此窗口中粘贴下面的电子邮件脚本。 然后单击“保存”。

## 访问Velocity日历对象

`set($x = $date.calendar)`

## 设置日期格式

`set($current_date = $date.format('dd-MM-yyyy', $x.getTime()))`

## 返回今天的日期

`$current_date`

1. 在电子邮件模板中引用令牌。**记下令牌的名称。 导航到您的电子邮件草稿。 包含令牌。  在发送电子邮件时，将填充令牌的值。 有关详细信息，请参阅[电子邮件脚本开发人员文档](https://experienceleague.adobe.com/en/docs/marketo-developer/marketo/email-scripting)。

由&#x200B;_Murta_&#x200B;发布于&#x200B;_2014-11-22_

## Bash安全公告

Marketo已对Bash漏洞(也称为[Shellshock (CVE-2014-6271)](https://nvd.nist.gov/view/vuln/detail?vulnId=CVE-2014-6271))进行了彻底调查，并得出结论认为我们不易遭受这些攻击。 此外，我们已采取预防措施，将软件更新到最新版本，以确保符合[CERT建议](https://www.cisa.gov/news-events/alerts/2014/09/25/gnu-bourne-again-shell-bash-shellshock-vulnerability-cve-2014-6271)。

由&#x200B;_Murta_&#x200B;发布于&#x200B;_2014-09-26_

## 如何更新SOAP API凭据

最佳实践是定期更新您的[SOAP API](/help/soap-api/soap-api.md)凭据。 目前，无法通过Marketo API以编程方式实现这一点。 以下说明将向您展示如何通过Marketo UI更新SOAP API凭据。

1. 转到“管理员”部分，然后单击“Web服务”。
1. 设置至少10个字符的加密密钥，单击“保存更改”。

由&#x200B;_Murta_&#x200B;发布于&#x200B;_2014-09-29_

## 如何清理Marketo数据库

**注意：这是访客博客文章。 Josh Hill是营销自动化机构Perkuto的Marketo业务主管。 Josh在市场营销、销售和技术领域工作，提供创收系统。 他在[MarketingRockstarGuides.com](https://www.marketingrockstarguides.com/)上撰写了关于营销自动化和需求生成的文章。**&#x200B;保持您的Marketo数据库干净是管理此强大系统的重要部分。 如果您的Marketo数据肮脏或CRM数据肮脏，那么您作为营销经理的工作将变得困难得多，因为您可以解释促销活动发送给错误的人或您的报表具有“方向性”的原因。 [一项2011年Gartner研究指出](https://www.data.com/export/sites/data/common/assets/pdf/DS_Gartner.pdf)不良的数据质量使劳动生产率降低了20%。 这是由于必须修复数据而浪费的20%的时间（8小时/每周或1天/每周）。 但是，与由于您的定位错误而导致的收入损失相比，这一损失就相形见绌了。 在保持数据干净方面投入的五大理由如下： — 可以更好地划分潜在客户和客户，从而使您能够在适当的时间将消息聚焦于适当的人。 20%的优惠券应该只提供给新的潜在客户，而不是最好的客户，对吗？  — 避免重复发送电子邮件。 尽管采取了所有这些预防措施，仍可以向同一潜在客户发送多个电子邮件，这通常是因为不合并重复记录。  — 向老板准确报告。 由于CMO在收入表中占有一席之地，因此您必须具有准确、一致且可重复的报告……否则您将无法再成为收入营销人员。  — 如果在销售谈判期间向主要潜在客户发送了错误的营销材料，就会影响未决交易。

如果您的数据库没有在生命周期阶段提供此详细信息，则可能会中止一两笔交易。  — 删除错误或旧的潜在客户，使其保持在定价阈值以下。 关于坏数据的代价，人们已经写了很多。 您最直接的问题是，太多的坏、重复或旧的潜在客户正在将您推向Marketo或Salesforce定价层，这可能会导致每年产生数千项费用。 那么，如何保持数据库清洁？ 您可以遵循这些数据清洁原则，但是如何在Marketo中实现这些原则？ 我对你的系统做了一些假设： — 你有一个标准的Marketo系统。  — 您拥有典型的Lead-Contact-Account Salesforce设置。  — 您正在系统之间同步所有记录。  — 您没有使用有意的重复项。

查找要修复的正确潜在客户**让我们首先查找潜在问题的潜在客户。 对于每个客户端，我都会查看一系列智能列表以确定其数据库的运行状况。 我建议你每月都这样做。 一旦智能列表开始工作，您每月仅需花费15分钟时间。 这是展示您的工作和Marketo投资回报率的绝佳方法。 创建一个表以了解对数据库的总影响。 以下示例包含我要查找的标准，因此请务必包含对您的业务非常重要的其他字段。 仅按Marketo、SFDC潜在客户和SFDC联系人划分每个组。

使用自动化更正数据值：使用自动化规则来更正常见的拼写错误或缺失数据可追溯到几十年前。 20世纪60年代，发送直邮时数据质量低下可能导致数千美元的浪费。 如今，数据质量错误毁掉声誉的速度比错误邮件的速度更快。 电子邮件声誉、语言选择和客户体验至关重要，它们更加重要，因为错误可能会在几分钟内公开传播。 通过自动数据清理保护您公司的声誉。 这些数据管理流程是我在Marketo中设置的首批内容之一。 大多数公司都建立了类似的流程： — 国家/地区修正者（尽管您本应该遵循原则1而不需要它） — 国家/地区修正者或映射者 — 如果您拥有国家/地区和推断国家/地区，通常很有帮助。  — 员工人数到员工范围。  — 不良商机Source向良好商机Source — 如果电子邮件发生更改，则电子邮件无效向电子邮件是良好的。  — 将旧字段值转换为新字段（Full到Empty）。 例如，此流程根据员工编号调整员工范围。

数据附加工具填写空字段对于更好地划分数据库至关重要。 潜在客户并不总是填写正确的行业或职能或者甚至表单上的标题。 有时候，您会从旧系统获取旧数据。 这种数据匮乏意味着，您必须要么将电子邮件发送给更少的人，要么发送给可能错误的人。 要解决此问题，您需要数据附加工具。

选项1：自行填写。 您可以插入数据以回填空字段。 您可能使用SIC代码而不是行业名称或年收入与年收入范围。 Marketo可以轻松自动执行这些修复。

选项2：通过LaunchPoint查找数据附加/扩充供应商在Launchpoint[中有](https://exchange.adobe.com/apps/browse/ec?product=MRKTO)个供应商可以帮助您扩充潜在客户数据，例如NetProspex和ReachForce。 有些人会要求您提供数据表，然后他们清理数据并发送回来。 更好的选项是Marketo或Salesforce中的自动工具，它会检查您需要的字段，然后推送正确的数据。 大多数供应商都使用[Marketo API或Webhook](/help/home.md)来完成此操作。

选项3：使用Marketo API更新潜在客户您可以使用Marketo API识别需要清理的潜在客户，然后通过API更新它们。 [按过滤器类型REST API获取多个潜在客户](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getLeadsByFilterUsingGET)是从Marketo中提取符合特定条件的数据的良好起点。 要更新潜在客户，请查看[创建/更新潜在客户REST API](/help/rest-api/leads.md)。

或者，您可以设置[Marketo Webhook](/help/webhooks/webhooks.md)，以通知外部系统发生了特定事件，例如表单填写。 然后，您可以返回值以更新潜在客户。

您应该自动执行哪些操作？ 我在步骤1中提出了一些建议。 但是，如果我们想要清理更多数据库，我们不仅需要修复数据值，还需要修复其他问题。  — 竞争对手阻止名单：确保您正在自动收集您的竞争对手并将其列入阻止名单。 如果您有竞争对手合作伙伴，则仍最好将其正确标记，并将其放入可能禁止的列表中。   — 多个硬退回：绝对会自动处理此流程。 如果潜在客户硬退回30天内的两次以上，则将其设置为“已暂停”或“无效”。 然后，每月一次查看一次问题是否为打字错误或其他问题。  - 30天内多次软退回：将它们设置为30天内的Marketing Suspended=TRUE 。   — 垃圾邮件陷阱暂停/删除：如果您的产品意味着人们使用可能的垃圾邮件陷阱地址，请务必小心。 查看垃圾邮件陷阱列表。 智能列表。

**不会自动执行的操作**&#x200B;永远不要自动删除潜在客户，因为意外大量删除错误记录的风险太大。 相反，每月审查一次标记为垃圾桶的潜在客户。 但是，如果要删除置入垃圾桶的潜在客户，请运行类似这样的流程，等待30天。

警告：使用自动化是节省时间和保持数据库干净的绝佳方法。 自动化是一把双刃剑，因为如果你设置错误，它会在几分钟内造成破坏。 在进行这些流程时请务必谨慎，并告知每个人。 通常，我建议不要删除任何SFDC联系人，因为他们更有可能是活动的商机、客户或前客户。 Finance或Legal可能要求您保留某些记录，并且这些记录附有联系人。 相反，将重点放在硬退回、无效或不再存在的联系人上。 现在，您知道了保持Marketo数据库干净的一些技术。 如果您想出了使用API或Webhook自动化这些流程的其他方法，请告知我们！

由&#x200B;_Josh_&#x200B;发布于&#x200B;_2014-10-08_

## 2014年10月版更新

### 外部页面预填充

在Marketo登陆页面外部加载时，Marketo表单不提供本机预填充功能。 但是，我们仍可以使用[Marketo API](/help/rest-api/rest-api.md)和[Forms 2.0 JavaScript API](/help/javascript-api/forms-api-reference.md/)实施此操作。 第一步是通过来自服务器的REST调用，从Marketo中检索潜在客户数据。 假设我们无法从服务器直接交叉引用潜在客户ID或其他唯一标识符，我们需要使用Munchkin Cookie“_mkto_trk”，通过[按筛选器类型获取潜在客户方法](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getLeadsByFilterUsingGET)，从Marketo服务器中检索数据。
要进行此调用，我们需要您的实例中的身份验证和REST端点。 当您通过Marketo实例的身份验证后，我们需要在`https://<host>/rest/v1/leads.json`处调用潜在客户API。 然后，我们需要创建一个查询字符串以对Marketo Cookie进行过滤，如下所示`?filterType=cookie&filterValues=`。 您需要从客户端发送到服务器的“_mkto_trk”密钥中检索特定值。 注意：_mkto_trk Cookie值包含与号，需要将URL编码为`%26`才能被Marketo端点正确接受。 默认情况下，潜在客户API返回四个字段：`id`、`email`、`firstName`和`updatedAt`。 要设置一组特定的字段，您需要包含一个`fields`查询参数，其字段名称以逗号分隔，如下所示： `&fields=email,firstName,lastName,company`。 最终，我们的调用将如下所示：

`https://<host>/rest/v1/leads.json?filterType=cookie&filterValues=<cookie>&fields=email,firstName,lastName,company&access_token=<token>`

当我们进行此调用时，它会返回一个JSON对象，如下所示：

```json
{
    "requestId":"e42b#14272d07d78",
    "success":true,
    "result":[
        {
        "id":50,
        "firstName":"Kenny",
 "lastName":"Elkington",
        "email":"<mkto@example.com>",
 "company":"Marketo Inc."
        }]
};
```

现在我们有了商机详细信息，接下来可以使用JavaScript中的whenReady和vals方法将它们映射到Marketo表单中。 首先，我们需要将Lead结果设置为页面上的变量：

```javascript
<script>
//print your JSON object dynamically as the mktoLead variable
var mktoLead = {
    "requestId":"e42b#14272d07d78",
    "success":true,
    "result":[
        {
        "id":50,
        "firstName":"Kenny",
  "lastName":"Elkington",
        "email":"mkto@example.com",
  "company":"Marketo Inc."
        }]
}
</script>
```

现在，我们已在页面上显示了结果，可以将它们映射到我们的表单字段：

```javascript
<script>
MktoForms2.whenReady( function(form) {
 //set the first result as local variable
 var mktoLeadFields = mktoLead.result[0];
    //map your results from REST call to the corresponding field name on the form
 var prefillFields = {
   "Email" : mktoLeadFields.email,
   "FirstName" : mktoLeadFields.firstName,
   "LastName" : mktoLeadFields.lastName,
   "Company" : mktoLeadFields.company
   };
 //pass our prefillFields objects into the form.vals method to fill our fields
 form.vals(prefillFields);
 }
 );
</script>
```

由&#x200B;_Kenny_&#x200B;发布于&#x200B;_2014-10-24_

## 替换电子邮件HTML

本文将向您展示如何删除Marketo为电子邮件生成的HTML。 然后，您可以使用自己的HTML，而无需Marketo重新设置其格式。

1. 导航到电子邮件，然后单击编辑草稿。
1. 单击“电子邮件操作”，单击“HTML工具”，然后单击“替换HTML”。
1. 将此框中的代码替换为您的HTML。 然后，单击保存。

由&#x200B;_Murta_&#x200B;发布于&#x200B;_2014-10-28_

## 获取访客的Cookie ID，然后查询关联的潜在客户数据

使用[按筛选器类型获取多个潜在客户](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getLeadsByFilterUsingGET) REST端点，您可以根据用户的Cookie ID查询潜在客户数据。 例如，您可以使用此方法在非Marketo登录页面上预填表单。 这篇帖子向您展示如何在网页访问期间捕获用户的Cookie值，使用该Cookie ID查询[获取多个潜在客户REST API](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getLeadsByFilterUsingGET)，然后返回用户的潜在客户数据。 首先，我们需要用户的Munchkin Cookie“_mkto_trk”值。 以下是可用于获取Cookie值的示例JavaScript函数。 有关此方法的详细信息，请参阅[此StackOverflow页](https://stackoverflow.com/questions/10730362/get-cookie-by-name)。 我建议在页面加载事件后设置500毫秒的延迟，然后再调用此函数。 这样可让Munchkin有时间加载，并对用户进行Cookie。

```javascript
//Function to read value of a cookie
function readCookie(name) {
    var cookiename = name + "=";
    var ca = document.cookie.split(';');
    for(var i=0;i < ca.length;i++) {
        var c = ca[i];
        while (c.charAt(0)==' ') c = c.substring(1,c.length);
        if (c.indexOf(cookiename) == 0) return c.substring(cookiename.length,c.length);
    }
    return null;
}

//Call readCookie function to get value of user's Marketo cookie
var value = readCookie('_mkto_trk');
```

接下来，将“_mkto_trk”Cookie的值传递到服务器。 要检索潜在客户数据，请从服务器调用具有此Cookie值的[获取多个潜在客户REST API](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getLeadsByFilterUsingGET)。 您需要实例中的身份验证和REST端点。 调用应有如下结构：

注意： `_mkto_trk` Cookie值包含与号，需要将URL编码为`%26`以便Marketo端点正确接受。

```java
require 'rest_client'
require 'json'

# Build request URL
# Replace AAA-BBB-CCC with your Marketo instance
marketo_instance = "<https://AAA-BBB-CCC.mktorest.com>"
endpoint = "/rest/v1/leads.json"
# Replace with your access token
auth_token =  "?access_token=" + "cde42eff-aca0-48cf-a1ac-576ffec65a84:ab"
# Replace with filter type and values
filter_type_and_values = "&filterType=cookies&filterValues=id:AAA-BBB-CCC%26token:_mch-marketo.com-1418418733122-51548&fields=cookies,email"
request_url = marketo_instance + endpoint + auth_token + filter_type_and_values

# Make request
response = RestClient.get request_url

# Returns Marketo API response
puts response
```

以上示例将返回电子邮件以及与用户关联的所有Cookie。 然后，您可以使用此数据对用户访问的后续页面进行个性化设置。

`{"requestId":"aa00#14a405aa786","result":[{"id":583,"email":"<testaccount@gmail.com>","cookies":"_mch-marketo.com-1418418733122-51548"}],"success":true}`

由&#x200B;_Murta_&#x200B;发布于&#x200B;_2014-10-30_

## 您何时需要开发人员来帮助实现营销自动化

注意：这是访客博客帖子。 Josh Hill是营销自动化机构Perkuto的Marketo业务主管。 Josh在市场营销、销售和技术领域工作，提供创收系统。 他在[MarketingRockstarGuides.com](https://www.marketingrockstarguides.com/)上撰写了关于营销自动化和需求生成的文章。

营销自动化平台开箱即用，并且由经验丰富的操作员掌握，功能强大。 根据定义，平台允许使用扩展应用程序来让系统为团队完成更令人惊叹的事情。 您可能认为Marketo的逻辑引擎能够执行很多操作（事实如此），但这其中存在一些限制。 Marketo不可能为您做任何事，也不应该做任何事。

还有其他工具可以更好地执行其功能，而不是Marketo可以构建的功能。 Marketo的平台非常开放，使应用程序的[LaunchPoint生态系统](https://exchange.adobe.com/apps/browse/ec?product=MRKTO)得以存在。 您还可以利用这种开放性来扩展您的站点和Marketo的功能，以满足您的业务需求。 Marketo等平台的好处在于，它使典型的营销人员能够构建页面、电子邮件和路由逻辑，而无需成为全面的程序员。
现在的营销人员确实需要理解逻辑，但实际编程最好留给专家去做。 那么，您如何知道何时需要致电开发人员？ 我有一些基本规则（或启发式）来确定何时应让程序员参与： — 当Marketo没有满足需要的明显过滤器、触发器或功能时，通常可以使用某些JavaScript或jQuery完成。  — 对于Marketo本身而言，这是否过于复杂？ -Marketo甚至能做到这一点吗？  — 这是不是不容易支持的网站自定义？ - Marketo是否需要访问网站或其他数据库？  — 这是否听起来像是计算机可以执行的操作，但Marketo没有相应的功能？ 请记住，虽然Marketo可能不会提供开箱即用的功能，但它会连接到许多第三方集成以及自定义连接。

在[LaunchPoint市场](https://exchange.adobe.com/apps/browse/ec?product=MRKTO)中查看这些类别中的几个类别： - [分析工具](https://exchange.adobe.com/apps/browse/ec?product=MRKTO) - [数据附加](https://exchange.adobe.com/apps/browse/ec?product=MRKTO) - [内容管理系统](https://exchange.adobe.com/apps/browse/ec?product=MRKTO)某些第三方应用程序在平台内提供直观的控制面板和设置工具(GoToWebinar)。 这些是“本机”集成，您最需要做的就是设置登录，然后在Marketo中使用它。 但是，其他扩展需要使用必须更直接编程的更复杂的API。

**Marketo的集成选项** - LaunchPoint集成 — 通常是登录或轻松设置。 - API集成 — 需要设置API和编程： (1) [REST API](/help/rest-api/rest-api.md) (2) [SOAP API](/help/soap-api/soap-api.md) (3) [Webhook集成](/help/webhooks/webhooks.md) — 需要设置特殊代码，但相当容易。 (4) [电子邮件脚本](./email-scripting.md) (Velocity) - JavaScript和jQuery： (1) [Forms 2.0](/help/javascript-api/forms-api-reference.md) (2) [潜在客户跟踪(Munchkin)](/help/javascript-api/lead-tracking.md) (3) [RTP JS](/help/javascript-api/web-personalization.md)以下是一些使用开发人员扩展Marketo平台功能的用例。 您有这些用例之一吗？ 如果是这样的话，也许该和开发人员对话了。 [访问LaunchPoint](https://exchange.adobe.com/apps/browse/ec?product=MRKTO)上的服务合作伙伴部分。

由&#x200B;_Josh_&#x200B;发布于&#x200B;_2014-11-06_

## 将Slack与Marketo集成

[Slack是一个企业协作平台](https://slack.com/)。 如果您的团队位于Slack上，则可以轻松地将Marketo通知引入您的工作流。 本文将向您展示如何在Marketo中发生特定商机活动时向聊天日志添加通知。 潜在用例包括向您的整个团队通知表单填写、访问定价页面或30天内未联系的潜在客户。 以下屏幕截图显示了按照本帮助文章中的步骤操作后，Marketo通知在Slack中的外观。

1. 登录到Slack。 单击Slack中的集成
1. 单击传入Webhook的添加按钮
1. 选择Marketo通知的渠道。 然后单击“添加传入Webhook集成”。
1. 复制并粘贴Webhook URL（步骤8需要）
1. 选择通知的名称
1. 登录到Marketo。 转到“管理员”。 单击Webhooks。
1. 单击新建Webhook
1. 输入Webhook的名称。 输入步骤4中的Webhook URL。 输入“发布”作为请求类型。 输入有效负载模板。

以下是屏幕快照中的有效负载模板。 它使用潜在客户级别的名字、公司和电子邮件地址令牌。

`payload={"text": "DEVELOPER SITE ALERT: {{lead.First Name:default=edit me}} {{lead.Company=edit me}}, {{lead.Email Address:default=no email address}}" }`

1. 在Marketo中设置Trigger Campaign。 流程步骤是调用Webhook到Slack。 智能列表是网页访问。
1. 验证它是否正常工作。

有关Marketo中Webhook的更多信息，请参阅[开发人员文档](./webhooks/webhooks.md)。

由&#x200B;_Murta_&#x200B;发布于&#x200B;_2014-11-10_

## 将Litmus与Marketo集成

[Litmus是一项跨浏览器和电子邮件客户端测试电子邮件的服务](https://www.litmus.com/)。 Litmus还提供电子邮件分析，包括点击次数、打开次数和删除次数。 本文将向您展示如何将Marketo与Litmus集成。

1. 在Marketo中设置电子邮件程序时，单击程序仪表板上的“我的令牌”
1. 将“Email Script”令牌拖到中面板以将其添加。
1. 命名令牌，然后单击“单击以编辑”
1. 在右侧的“标准对象”下，展开“潜在客户”类别。 找到“Email Address（电子邮件地址）”字段并选中方框。 在该页面左侧的空脚本区域中，粘贴由Litmus提供的跟踪代码。 在Litmus代码中，将`{{lead.Email Address}}`的每个实例替换为`${lead.Email}`。 单击“保存”以关闭灯箱窗口，然后在令牌页面上再次单击“保存”。
1. 记下令牌`{{my.LitmusToken}}`的名称。 打开要跟踪的电子邮件。 在电子邮件的最底部，放置新的脚本令牌。 您还可以添加默认信息以匹配Litmus版本`{{my.LitmusToken:default=editme}}`。

在发送电子邮件时，Marketo会将脚本放入电子邮件中。

由&#x200B;_Murta_&#x200B;发布于&#x200B;_2014-11-18_

## 在Facebook上共享Marketo登录页面时指定图像

假设您希望在Facebook上共享Marketo登陆页面时自动显示图像。 也许你希望这张照片不放在Marketo登陆页面上，而只放在脸书共享上。 为此，您可以向Marketo登录页面添加一个开放图形元标记。 执行此操作的具体步骤如下。

1. 选择您的登陆页面。 然后单击“编辑草稿”。
1. 单击编辑页面Meta Tags 。
1. 将开放图形元添加到Facebook组织标签部分。 然后单击“保存”。 格式如下： `<meta property="og:image" content="http://example.com/example.jpg"/>`

[有关详细信息，请参阅Facebook的开发人员文档](https://developers.facebook.com/docs/sharing/best-practices)中有关开放图元标记的内容。

由&#x200B;_Murta_&#x200B;发布于&#x200B;_2014-11-17_

## 根据反向链接重定向页面

假设您想阻止直接流量进入Marketo登陆页面。 假设此页面具有类似于PDF的可下载内容，您希望用户在接收内容之前先填写表单。 您可以通过检查用户是否来自特定页面来解决此问题。 在这种情况下，该页面就是用户必须填写表单的页面。 如果用户不是来自该页面，则随后可以将用户重定向到表单填写页面。 要完成此操作，您必须检查包含内容的登陆页面的反向链接页面是否为表单填写页面。
将以下代码片段中`http://example.com/PageWithForm`的两个实例替换为您希望用户来自的页面的链接。 这可以是表单填写页面。**

```javascript
<script>
window.onload = function() {
  if (document.referrer !== "http://example.com/PageWithForm") {
    document.location.href = "http://example.com/PageWithForm";
  };
 };
</script>
```

在包含内容的Marketo登录页上的结束标记之前包含自定义的JavaScript代码片段。**如果用户未发送到登陆页面，其中包含表单填写页面中的内容，则用户现在将被重定向到表单填写页面。

由&#x200B;_Murta_&#x200B;发布于&#x200B;_2014-11-18_

## 将Trello与Marketo集成

Trello是[流行的基于Web的项目管理应用程序](https://trello.com/)。 如果您的团队使用Trello，则可以轻松地将Marketo通知引入您的工作流。 本文说明如何向Trello展示板添加带有Marketo通知的信息卡。 在Marketo中发生特定商机活动时，将添加此信息卡。 潜在用例包括向您的整个团队通知表单填写、访问定价页面或30天内未联系的潜在客户。

1. 登录Trello。 导航到将在中添加Marketo通知的Trello展示板。 单击“添加列表”，然后命名该列表。
1. 单击显示侧栏。 单击“Email-to-board Settings（电子邮件到展示板设置）”。 请注意“此讨论区的电子邮件地址”框中的电子邮件。 此电子邮件将在步骤6中使用。 选择要添加Marketo通知的列表。
1. 登录到Marketo。 单击新的Smart Campaign。 输入名称，然后单击保存。
1. 导航到智能列表。 为此智能营销活动选择触发器。 在本例中，我们使用“表单填写”触发器。 将“填写表单”触发器拖到中面板中。 选择要触发此通知的表单。
1. 创建电子邮件。 单击“新建”。 单击本地资产。 单击新建电子邮件。 命名电子邮件。 然后单击“创建”。
1. 对于您在步骤5中创建的电子邮件，单击“编辑草稿”。 拖动要在Trello卡片中显示的相关令牌。 Marketo电子邮件的主题行显示在Trello卡的标题中，Marketo电子邮件的正文显示在Trello卡的描述中。 例如，如果您希望潜在客户的名字和姓氏在Trello卡的标题中，则可以使用“潜在客户警报：`{{lead.First Name:default=edit me}}` `{{lead.Last Name:default=edit me}}`”。 然后审批电子邮件。
1. 导航到Smart Campaign。 单击“流”。 将“Send Alert（发送警报）”拖到中间面板。 选择刚创建的电子邮件。 选择“发送至”作为无。 选择“To Other Email”作为第二步中的Trello电子邮件。
1. 单击顶部菜单中的“计划”。 单击激活。 单击“确认”。
1. 测试集成。 标题中包含商机的名字和姓氏的卡片将显示在Trello展示板中。 有关详细信息，请参阅[Trello的文档](https://support.atlassian.com/trello/)。

由&#x200B;_Murta_&#x200B;发布于&#x200B;_2014-11-18_

## 按自定义字段值查找潜在客户

假设您想从Marketo API中获得与特定活动或非活动标准匹配的潜在客户。 例如，您可能希望查找在过去30天内得分未发生更改的潜在客户。 按照本帖中的步骤操作，即可获得此潜在客户列表。 为此，我们在Marketo中创建一个智能营销活动，用于识别过去30天内得分未发生更改的潜在客户，然后在这些潜在客户上存储一个值以识别他们。 然后，我们将使用此值查询API。

1. 创建一个名为customLeadStatus的新自定义字段**登录到Marketo，然后转到“管理员”面板。 单击字段管理。 单击“新建自定义字段”。  命名字段。 然后单击“创建”。
1. 使用智能列表创建一个智能营销活动，该列表会查找30天内未更新的潜在客户。**单击新建Smart Campaign。 命名新的智能营销活动。 “拖动不分数”已从右面板更改为中面板。
1. 从步骤3向智能营销活动添加流程步骤以使用新值更新customLeadStatus字段。**将“更改数据值”从右面板拖到中面板。
1. 更新Smart Campaign以允许潜在客户多次运行。**单击计划。 然后单击“编辑”。  每次都选择。 然后单击“保存”。 营销活动现在将开始运行。
1. 查询[按筛选器类型REST API](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getLeadsByFilterUsingGET)获取多个潜在客户。 提供参数filterType=customLeadStatus &amp; filterValue=needsExtension.**

这是返回此数据的示例请求。

`<https://AAA-BBB-CCC.mktorest.com/rest/v1/leads.json?access_token=><yourAccessToken>&filterType=customLeadStatus&filterValues=needsEnrichment`

成功的API调用会返回其customLeadStatus字段与needsExtension的值匹配的潜在客户的JSON数据。 有关详细信息，请查看[按筛选器类型REST API获取多个潜在客户](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getLeadsByFilterUsingGET)。

由&#x200B;_Murta_&#x200B;发布于&#x200B;_2014-11-22_

## 通过SOAP API同步销售机会

这篇帖子介绍如何通过SOAP API将机会插入Marketo并将它们与公司和潜在客户关联。 首先，它解释了此过程的工作方式，然后为每个场景提供代码示例。

**表结构图**&#x200B;首先，下图描述了表结构。 ID在创建记录时自动生成（序列号）。 需要粗体字段：只有“业务机会人员角色”具有必填字段。 括号中的字段是可选的，带虚线的连接也是可选的。

**基本Opportunity Insertion**&#x200B;首先插入Opportunity，然后插入Opportunity Person Role，该角色将Opportunity链接到Lead。 在本例中，最好使用Lead Id和Opportunity Id来指定Opportunity Person Role。 创建Opportunity时，您可以在SOAP响应中获取Opportunity ID。 Lead Id在Marketo Lead数据库中的每个Lead上均可见。

**公司链接**&#x200B;在大多数情况下，除了个人之外，您还希望将商机链接到公司。 在Marketo中，您无法通过SOAP API访问公司记录，只能访问潜在客户记录（潜在客户记录包含公司字段）。 仍然可以通过向每个Lead添加唯一的公司标识符并在您的Opportunity中使用该ID将Opportunity链接到公司。 步骤1是在潜在客户记录中创建“公司ID”字段，并在该字段中填充唯一标识符（通常来自后端系统）。 第2步是在Opportunity上创建“Company Id”字段：您需要请求Marketo支持或咨询团队为您创建此类字段。 然后在创建Opportunity时填充此字段，以将Opportunity连接到Company。 如果您使用Marketo Revenue Cycle Analytics并且希望使用Opportunity Influence Analyzer（这取决于公司信息），这一点尤为重要。

**使用外部标识符**&#x200B;在许多情况下，在与后端系统集成时，您可能拥有自己的唯一标识符。 可以在Marketo中通过外键使用这些唯一标识符。 对于潜在客户，您通常使用外部系统人员ID (FSPID)，将使用该ID而不是Marketo ID或电子邮件地址作为唯一标识符。 FSPID是一个隐藏的系统字段，在Marketo中不可见。 如果您尚未这样做，则Opportunity sync还必须在自定义字段中保存FSPID，例如“外部ID”（您可以为该字段命名任何内容）。 您可以作为Marketo管理员自行创建此字段。 对于Opportunity ，您让Marketo支持在Opportunity上创建另一个自定义字段，例如“Foreign Id”（您可以将其命名为anything）。 然后在插入机会时填充此字段。 最后，在创建业务机会人员角色时，您同时使用这两个外键来指定销售线索和业务机会之间的链接，而不是使用Marketo ID。 您也可以使用外键来更新业务机会的销售线索。 此时，无法向机会人员角色记录添加外键，因此您必须对此使用自动生成的Marketo ID(在创建机会人员角色后，您将在SOAP响应中获取此ID)。

**代码示例**&#x200B;步骤： 1. 使用外键和公司ID 1插入/更新潜在客户。 使用外键1插入Opportunity。 使用外键1插入机会人员角色。 SOAP请求 — 使用外键和公司Id更新现有潜在客户此项使用外键“12346”和帐户/公司Id“C123”更新现有潜在客户(Marketo Id“6”)。 我们还在自定义字段中保存外键，因为机会人员角色需要它。 使用外键是可选的：您还可以使用Marketo Id将此Lead链接到Opportunity。 使用公司ID也是可选的，但是如果要在RCA中使用Opportunity Influence Analyzer，则必须使用ID。 请求：

```xml
<soapenv:Envelope xmlns:soapenv="<http://schemas.xmlsoap.org/soap/envelope/>" xmlns:mkt="<http://www.marketo.com/mktows/">
   <soapenv:Header>
      <mkt:AuthenticationHeader>
         <mktowsUserId>\*\*\*</mktowsUserId>
         <requestSignature>\*\*\*</requestSignature>
         <requestTimestamp>2014-11-20T15:18:30-07:00</requestTimestamp>
      </mkt:AuthenticationHeader>
   </soapenv:Header>
   <soapenv:Body>
      <mkt:paramsSyncLead>
         <leadRecord>
            <Id>6</Id>
            <ForeignSysPersonId>12346</ForeignSysPersonId>
            <leadAttributeList>
               <attribute>
                  <attrName>FSPID</attrName>
                  <attrValue>12346</attrValue>
               </attribute>
               <attribute>
                  <attrName>cAccountFSID</attrName>
                  <attrValue>C123</attrValue>
               </attribute>
            </leadAttributeList>
         </leadRecord>
         <returnLead>false</returnLead>
      </mkt:paramsSyncLead>
   </soapenv:Body>
</soapenv:Envelope>
```

响应：

```xml
<SOAP-ENV:Envelope xmlns:SOAP-ENV="<http://schemas.xmlsoap.org/soap/envelope/>" xmlns:xsi="<http://www.w3.org/2001/XMLSchema-instance>" xmlns:ns1="<http://www.marketo.com/mktows/">
   <SOAP-ENV:Body>
      <ns1:successSyncLead>
         <result>
            <leadId>6</leadId>
            <syncStatus>
               <leadId>6</leadId>
               <status>UPDATED</status>
               <error xsi:nil="true"/>
            </syncStatus>
            <leadRecord xsi:nil="true"/>
         </result>
      </ns1:successSyncLead>
   </SOAP-ENV:Body>
</SOAP-ENV:Envelope>
```

SOAP请求 — Opportunity创建在此示例中，已在Opportunity表上创建2个自定义字段： - `opportunityId` →包含Opportunity唯一ID - `cAccountFSID` →包含公司引用，而不是指定您自己的Opportunity ID。 您还可以使用Marketo生成的机会ID。 在这种情况下，您会忽略External Key节点。 公司关联也是可选的，但如果要在RCA中使用Opportunity Influence Analyzer ，则它是必需的。 请求：

```xml
<soapenv:Envelope xmlns:soapenv="<http://schemas.xmlsoap.org/soap/envelope/>" xmlns:mkt="<http://www.marketo.com/mktows/">
   <soapenv:Header>
      <mkt:AuthenticationHeader>
         <mktowsUserId>\*\*\*</mktowsUserId>
         <requestSignature>\*\*\*</requestSignature>
         <requestTimestamp>2014-11-20T15:03:28-07:00</requestTimestamp>
      </mkt:AuthenticationHeader>
   </soapenv:Header>
   <soapenv:Body>
      <mkt:paramsSyncMObjects>
         <mObjectList>
            <!--Zero or more repetitions:-->
            <mObject>
               <type>Opportunity</type>
               <externalKey>
                  <name>opportunityId</name>
                  <value>Opportunity_4</value>
               </externalKey>
               <attribList>
                  <attrib>
                     <name>opportunityId</name>
                     <value>Opportunity_4</value>
                  </attrib>
                  <attrib>
                     <name>Name</name>
                     <value>Opportunity 4 for ACME</value>
                  </attrib>
                  <attrib>
                     <name>IsClosed</name>
                     <value>1</value>
                  </attrib>
                  <attrib>
                     <name>IsWon</name>
                     <value>1</value>
                  </attrib>
                  <attrib>
                     <name>Amount</name>
                     <value>501.00</value>
                  </attrib>
                  <attrib>
                     <name>CloseDate</name>
                     <value>2014-10-24</value>
                  </attrib>
                  <attrib>
                     <name>ExpectedRevenue</name>
                     <value>501</value>
                  </attrib>
                  <attrib>
                     <name>Probability</name>
                     <value>100</value>
                  </attrib>
               </attribList>
               <associationList>
                  <mObjAssociation>
                     <mObjType>Company</mObjType>
                     <externalKey>
                        <name>cAccountFSID</name>
                        <value>C123</value>
                     </externalKey>
                  </mObjAssociation>
               </associationList>
            </mObject>
         </mObjectList>
         <operation>UPSERT</operation>
      </mkt:paramsSyncMObjects>
   </soapenv:Body>
</soapenv:Envelope>
```

响应：

```xml
<SOAP-ENV:Envelope xmlns:SOAP-ENV="<http://schemas.xmlsoap.org/soap/envelope/>" xmlns:ns1="<http://www.marketo.com/mktows/">
   <SOAP-ENV:Body>
      <ns1:successSyncMObjects>
         <result>
            <mObjStatusList>
               <mObjStatus>
                  <id>40</id>
                  <externalKey>
                     <name>opportunityId</name>
                     <value>Opportunity_4</value>
                  </externalKey>
                  <status>CREATED</status>
               </mObjStatus>
            </mObjStatusList>
         </result>
      </ns1:successSyncMObjects>
   </SOAP-ENV:Body>
</SOAP-ENV:Envelope>
```

SOAP请求 — Opportunity Person Role此请求将Lead链接到Opportunity。 您可以在单个SOAP请求中指定多个链接（此示例将Opportunity仅链接到1个Lead ）。 这会使用外键指定链接，但在注释中，它还会显示如何使用实际ID（在本例中：6表示Lead ID，40表示Opportunity ID）。 此“IsPrimary”和“Role”字段是可选的。 请求：

```xml
<soapenv:Envelope xmlns:soapenv="<http://schemas.xmlsoap.org/soap/envelope/>" xmlns:mkt="<http://www.marketo.com/mktows/">
   <soapenv:Header>
      <mkt:AuthenticationHeader>
         <mktowsUserId>\*\*\*</mktowsUserId>
         <requestSignature>\*\*\*</requestSignature>
         <requestTimestamp>2014-11-20T15:18:30-07:00</requestTimestamp>
      </mkt:AuthenticationHeader>
   </soapenv:Header>
   <soapenv:Body>
      <mkt:paramsSyncMObjects>
         <mObjectList>
            <!--Zero or more repetitions:-->
            <mObject>
               <type>OpportunityPersonRole</type>
               <attribList>
                  <attrib>
                     <name>IsPrimary</name>
                     <value>1</value>
                  </attrib>
                  <attrib>
                     <name>Role</name>
                     <value>Marketing Manager</value>
                  </attrib>
               </attribList>
               <associationList>
                  <mObjAssociation>
                     <mObjType>Lead</mObjType>
                     <!--id>6</id-->
                     <externalKey>
                      <name>FSPID</name>
                      <value>12346</value>
                     </externalKey>
                  </mObjAssociation>
                  <mObjAssociation>
                     <mObjType>Opportunity</mObjType>
                     <!--id>40</id-->
                     <externalKey>
                      <name>opportunityId</name>
                      <value>Opportunity_4</value>
                    </externalKey>
                  </mObjAssociation>
               </associationList>
            </mObject>
         </mObjectList>
         <operation>UPSERT</operation>
      </mkt:paramsSyncMObjects>
   </soapenv:Body>
</soapenv:Envelope>
```

响应：

```xml
<SOAP-ENV:Envelope xmlns:SOAP-ENV="<http://schemas.xmlsoap.org/soap/envelope/>" xmlns:ns1="<http://www.marketo.com/mktows/">
   <SOAP-ENV:Body>
      <ns1:successSyncMObjects>
         <result>
            <mObjStatusList>
               <mObjStatus>
                  <id>11</id>
                  <status>CREATED</status>
               </mObjStatus>
            </mObjStatusList>
         </result>
      </ns1:successSyncMObjects>
   </SOAP-ENV:Body>
</SOAP-ENV:Envelope>
```

**替代方法（在一次调用中执行步骤2和步骤3）**&#x200B;虽然您可以先插入机会，然后再插入机会人员角色，但也可以在一次SOAP调用中执行此操作。 但是，您需要对Opportunity使用外键（您不能在Opportunity Person Role中使用自动生成的Opportunity ID，因为尚未生成Opportunity ）。 当然，您还可以在此同一API调用中将多个Lead链接到此Opportunity （此示例将Opportunity仅链接到一个Lead ）。 请求：

```xml
<soapenv:Envelope xmlns:soapenv="<http://schemas.xmlsoap.org/soap/envelope/>" xmlns:mkt="<http://www.marketo.com/mktows/">
   <soapenv:Header>
      <mkt:AuthenticationHeader>
         <mktowsUserId>\*\*\*</mktowsUserId>
         <requestSignature>\*\*\*</requestSignature>
         <requestTimestamp>2014-11-20T15:44:08-07:00</requestTimestamp>
      </mkt:AuthenticationHeader>
   </soapenv:Header>
   <soapenv:Body>
      <mkt:paramsSyncMObjects>
         <mObjectList>
            <!--Zero or more repetitions:-->
            <mObject>
               <type>Opportunity</type>
               <externalKey>
                  <name>opportunityId</name>
                  <value>Opportunity_5</value>
               </externalKey>
               <attribList>
                  <attrib>
                     <name>opportunityId</name>
                     <value>Opportunity_5</value>
                  </attrib>
                  <attrib>
                     <name>Name</name>
                     <value>Opportunity 5 for ACME</value>
                  </attrib>
                  <attrib>
                     <name>IsClosed</name>
                     <value>1</value>
                  </attrib>
                  <attrib>
                     <name>IsWon</name>
                     <value>1</value>
                  </attrib>
                  <attrib>
                     <name>Amount</name>
                     <value>1500</value>
                  </attrib>
                  <attrib>
                     <name>CloseDate</name>
                     <value>2014-10-24</value>
                  </attrib>
                  <attrib>
                     <name>ExpectedRevenue</name>
                     <value>1500</value>
                  </attrib>
                  <attrib>
                     <name>Probability</name>
                     <value>100</value>
                  </attrib>
               </attribList>
               <associationList>
                  <mObjAssociation>
                     <mObjType>Company</mObjType>
                     <externalKey>
                        <name>cAccountFSID</name>
                        <value>C123</value>
                     </externalKey>
                  </mObjAssociation>
               </associationList>
            </mObject>
             <mObject>
               <type>OpportunityPersonRole</type>
               <attribList>
                  <attrib>
                     <name>IsPrimary</name>
                     <value>1</value>
                  </attrib>
                  <attrib>
                     <name>Role</name>
                     <value>Marketing Manager</value>
                  </attrib>
               </attribList>
               <associationList>
                  <mObjAssociation>
                     <mObjType>Lead</mObjType>
                     <!--id>6</id-->
                     <externalKey>
                      <name>FSPID</name>
                      <value>12346</value>
                     </externalKey>
                  </mObjAssociation>
                  <mObjAssociation>
                     <mObjType>Opportunity</mObjType>
                     <externalKey>
                      <name>opportunityId</name>
                      <value>Opportunity_5</value>
                    </externalKey>
                  </mObjAssociation>
               </associationList>
            </mObject>
         </mObjectList>
         <operation>UPSERT</operation>
      </mkt:paramsSyncMObjects>
   </soapenv:Body>
</soapenv:Envelope>
```

响应：

```xml
<SOAP-ENV:Envelope xmlns:SOAP-ENV="<http://schemas.xmlsoap.org/soap/envelope/>" xmlns:ns1="<http://www.marketo.com/mktows/">
   <SOAP-ENV:Body>
      <ns1:successSyncMObjects>
         <result>
            <mObjStatusList>
               <mObjStatus>
                  <id>41</id>
                  <externalKey>
                     <name>opportunityId</name>
                     <value>Opportunity_5</value>
                  </externalKey>
                  <status>CREATED</status>
               </mObjStatus>
               <mObjStatus>
                  <id>12</id>
                  <status>CREATED</status>
               </mObjStatus>
            </mObjStatusList>
         </result>
      </ns1:successSyncMObjects>
   </SOAP-ENV:Body>
</SOAP-ENV:Envelope>
```

由&#x200B;_Jep_&#x200B;发布于&#x200B;_2014-11-26_

## 多线程REST API请求

如果您想在调用Marketo API时提高性能，则可以发出并发请求。 此方法允许您在更短的时间内获取更多数据。 发出API请求时，客户端与服务器之间的往返时间的一部分就是网络上的传输时间。 因此，如果能够减少请求在网上的传输时间，就可以提高性能。 下面的示例代码说明了如何在Ruby中执行此操作。 它使用EventMachine，这是一个用于发出多线程请求[的](https://github.com/igrigorik/em-http-request/wiki/Parallel-Requests)事件处理库。 以下示例调用了[潜在客户活动API](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getLeadActivitiesUsingGET)，并发出两个并发请求。 此方法可消除第二个请求从客户端到服务器的传输时间。 为此，它在处理第一个请求的同时包含第二个请求。 API响应将写入文本文件。

```java
require 'em-http-request'
require 'json'

# Build request URL
# Replace AAA-BBB-CCC with your Marketo instance
marketo_instance = "<https://AAA-BBB-CCC.mktorest.com>"
endpoint = "/rest/v1/activities.json"
# Replace with your access token
auth_token =  "?access_token=" + "ac756f7a-d54d-41ac-8c3c-f2d2a39ee325:ab"
# Specify datetime needed as nextPageToken
since_date_time = ["&nextPageToken=A5YMOYZQBOGD2OSYYBYDAQGEMGLBDGDANAABQGRAQWAAKKID", "&nextPageToken=GIYDAOBNGEYS2MBWKQYDAORQGA5DAMBOGAYDAKZQGAYDALBQ"]
# Specify activities needed
activity_type_ids = "&activityTypeIds=1&activityTypeIds=12"
requesturl_a = marketo_instance + endpoint + auth_token + since_date_time.at(0) + activity_type_ids
requesturl_b = marketo_instance + endpoint + auth_token + since_date_time.at(1) + activity_type_ids

# Make request
EventMachine.run do
  http1 = EventMachine::HttpRequest.new(requesturl_a).get
  http2 = EventMachine::HttpRequest.new(requesturl_b).get

# When API response is received, write response to a text file
  http1.callback {
  File.open('response1.txt', 'w') do |t|
  t.puts http1.response
  end }

  http2.callback {
  File.open('response2.txt', 'w') do |t|
  t.puts http2.response
  end }
end
```

由&#x200B;_Murta_&#x200B;发布于&#x200B;_2014-12-03_

## 性能调整API请求

本文讨论在从Marketo API请求数据时提高性能的策略。 但是，您必须根据Marketo API的每日限制操作限制来权衡这些策略的好处。
**策略1 — 在每个API调用中请求更少的数据**&#x200B;通常，当您在API调用中请求更多数据时，Marketo服务器在数据库中查找数据所花费的时间会增加。 如果您使用日期范围进行API调用(例如[getMultipleLeads SOAP API](/help/soap-api/getmultipleleads.md))，请缩短每次调用的时间范围并使用更多调用进行补偿。 例如，不要请求6月1日至7月1日的数据，而是一次请求一天的数据，如6月1日至2日的一个呼叫，然后是6月2日至1日的另一个呼叫。 如果您进行API调用以从Marketo潜在客户字段中返回数据，则仅请求这些必需的字段。 每增加一个潜在客户字段，API调用所花费的时间就会递增。 另一种方法是减少批次大小，或减少每次调用请求的潜在客户数。
**策略2 — 发出并发请求**以提高性能并一次提取更多数据。 您可以对API发出并发请求。 此方法可减少合计花费在线API请求上的时间。 例如，假设您向“按过滤器类型获取多个潜在客户”发出请求。 您可以并发请求一个请求查询潜在客户1至300，另一个请求查询潜在客户301 - 600。
**策略3 — 缓存数据** Marketo中某些数据（如潜在客户字段列表）的更改频率低于其他数据（如潜在客户活动数据）。 如果您缓存的数据更新频率较低，则可以减少必须发出的API调用数。 您还可以获得更好的性能，因为本地查找数据通常比从远程Web服务访问数据更快。

由&#x200B;_Murta_&#x200B;发布于&#x200B;_2014-12-05_

## 将Marketo表单数据发送到Google Analytics

在Google Analytics中，您可以发送自定义数据事件，然后利用这些数据来细分和分析您的网站性能。 下面的JavaScript代码片段允许您在访客提交Web表单后自动将Marketo 2.0表单数据推送到Google Analytics。 以下是设置方法。

**步骤1**&#x200B;将JavaScript标记插入代码底部包含Marketo Forms的任何页面（在标记之前）。 JavaScript仅发送非隐藏字段(sendHiddenFields ： false)。 可以通过将sendHiddenFields从false更改为true来调整此设置。 您还可以通过在“fieldsToExclude”数组中添加其他字段ID来选择要排除的字段。

```javascript
function pushFormDataToGa(a){
setTimeout(function () {
document.getElementsByTagName('form')[0].getElementsByClassName(a.submitButton)[0].addEventListener('click', function() {
  allFields = document.getElementsByTagName('form')[0].getElementsByTagName('input');
  for(i=0;i<allFields.length;i++){
   if( (allFields[i].type !="hidden" && allFields[i].type !="submit" && allFields[i].value !="" && a.fieldsToExclude.indexOf(allFields[i].id) === -1  ) || (allFields[i].type === "hidden" && a.sendHiddenFields) ){
    console.log( allFields[i].name + ": "  + allFields[i].value);
    if(typeof(_gaq) != "undefined"){
    //Classic
    _trackEvent("Marketo Form Submission", allFields[i].value , allFields[i].name
{'nonInteraction': 1});
    }else if(typeof(ga) !="undefined"){
    //Universal
    ga('send', 'event',"Marketo Form Submission", allFields[i].value , allFields[i].name, {'nonInteraction': 1});
}}}}, false);
}, 3000);}
pushFormDataToGa({
 submitButton: "mktoButton",
 fieldsToExclude: ["Email","LastName", "FirstName"],
 sendHiddenFields : false
});
```

**第二步** GA中的数据将显示在“报表”部分中。 转到行为>事件>热门事件。 **脚本限制：** — 此代码示例仅与[Marketo Forms 2.0](/help/javascript-api/forms-api-reference.md)兼容。  — 由于Google的隐私政策，您不能发送任何个人信息（电子邮件或姓名）。 除了潜在的隐私顾虑之外，这是个人身份信息，因此违反了[Google Analytics的服务条款](https://marketingplatform.google.com/about/analytics/terms/us/)：

“您不会（也不允许任何第三方）使用本服务跟踪、收集或上传任何个人身份数据（如姓名、电子邮件地址或账单信息）或Google可合理关联到此类信息的其他数据。”

由&#x200B;_Yanir_&#x200B;发布于&#x200B;_2014-12-16_

## 向Marketo表单添加全名字段

我们知道，缩短网络表单可以提高转化率。 下面的JavaScript代码示例允许您通过将名字和姓氏字段合并到一个全名字段来使表单变得更短。 当访客键入其全名时，脚本会自动将文本拆分为名字和姓氏字段。 对于已知访客，该脚本将连接名字和姓氏，然后将它们复制到新字段中，以便他们不必再次填写该字段。 以下是设置方法。

**第一步**在Marketo中创建一个名为“全名”的新自定义字段。 无需在CRM平台中创建它，因为脚本将仅使用此字段显示全名。
**第二步**将此字段添加到您的所有Web窗体。 将您的名字和姓氏字段设置为隐藏。 在JavaScript中，更改“splitFullName”配置以包含3个字段名称。 注意：请确保这些名称不会出现在页面上的其他位置。
**第三步**&#x200B;将JavaScript插入到代码底部的所有登陆页面中，在标记之前。

```javascript
<script>
MktoForms2.whenReady(function (form){
        function splitFullName(a,b,c){
                String.prototype.capitalize = function(){
                        return this.replace( /(^|s)([a-z])/g , function(m,p1,p2){ return p1+p2.toUpperCase(); } );
                };
                document.getElementsByName[c](0).oninput=function(){
                        var fullName = document.getElementsByName[c](0).value;
                        if((fullName.match(/ /g) || []).length ===0 || fullName.substring(fullName.indexOf(" ")+1,fullName.length) === ""){
                                var first = fullName.capitalize();;
                                var last = "null";
                        }else if(fullName.substring(0,fullName.indexOf(" ")).indexOf(".")>-1){
                                var first = fullName.substring(0,fullName.indexOf(" ")).capitalize() + " " + fullName.substring(fullName.indexOf(" ")+1,fullName.length).substring(0,fullName.substring(fullName.indexOf(" ")+1,fullName.length).indexOf(" ")).capitalize();
                                var last = fullName.substring(first.length +1,fullName.length).capitalize();
                        }else{
                                var first = fullName.substring(0,fullName.indexOf(" ")).capitalize();
                                var last = fullName.substring(fullName.indexOf(" ")+1,fullName.length).capitalize();
                        }
                        document.getElementsByName[a](0).value = first;
                        document.getElementsByName[b](0).value = last;
                };
                //Initial Values
                if(document.getElementsByName[c](0).value.length < 2 && document.getElementsByName[b](0).value.length.length >2 && document.getElementsByName[a](0).value.length.length >2 ){
                        var first = document.getElementsByName[a](0).value.capitalize();
                        var last = document.getElementsByName[b](0).value.capitalize();
                        var fullName =  first + " " + last ;
                        console.log(fullName);
                        document.getElementsByName[c](0).value = fullName;
                }
        }
        splitFullName("FirstName","LastName","leadFullName");
});
</script>
```

注意：此代码仅适用于Marketo Forms 2.0。

由&#x200B;_Yanir_&#x200B;发布于&#x200B;_2014-12-16_

## 使用cURL通过REST API导入潜在客户

是否要通过REST API从CSV文件导入潜在客户，但请注意，使用Postman Chrome扩展很难做到这一点。 在本帖子中，我们将介绍如何使用cURL实现此目的。

1. [下载并安装cURL](https://curl.se/download.html)，我们使用这个命令行工具将数据推送到Marketo的REST API。
1. 打开命令行，然后导航到CSV文件所在的位置。 CSV文件中的列标题必须匹配API字段名称，而不是Marketo字段名称。
1. 您需要访问令牌。 登录到Marketo，转到“管理员”，然后转到“LaunchPoint”。 找到您的REST API用户，然后单击“查看详细信息”。 单击“获取令牌”按钮。
1. 您还将需要特定于Marketo实例的REST端点。 登录到Marketo，然后转到“管理员”，再转到“Web服务”。 在标记为“REST API”的部分中，您可以找到端点URL。
1. 在命令行上，对cURL调用遵循此格式。 使用步骤3中的访问令牌替换`<accesstoken>`并使用步骤4中的REST API端点URL替换`<REST API Endpoint URL>`。 [此处](https://developer.adobe.com/marketo-apis/api/mapi/#operation/importLeadUsingPOST)提供了更多信息。 此处的“/bulk”将替换端点URL末尾的“/rest”。 如果为/rest/bulk设置了端点，则会返回错误。

`curl -i -F format=csv -F file=@leaddata.csv -F access_token=<accesstoken> <REST API Endpoint URL>/bulk/v1/leads.json`

由&#x200B;_Jordan_&#x200B;发布于&#x200B;_2014-12-16_

## 向Marketo添加确认警报，用于

假设当用户单击Marketo表单上的“发送”按钮时，您希望显示一条通知，询问用户是否“发送真的可以吗？” 通过实施几行JavaScript，可以实现这一点，当有人单击“发送”按钮时，该行将显示一个确认框。 以下是如何执行此操作的示例。 将onSubmit函数添加到您的Marketo表单，如下所示。 有关Marketo Forms API的详细信息，请[查看开发人员文档](/help/javascript-api/forms-api-reference.md)。

```javascript
<script src="//app-e.marketo.com/js/forms2/js/forms2.js"></script>
<form id="mktoForm_19"></form>
<script>
MktoForms2.loadForm("//app-e.marketo.com", "212-RBI-463", 19,function(form){

//Add this function to your Marketo form script
form.onSubmit(function(){
alert("Do you really want to submit the form?");
});
});
</script>
```

由&#x200B;_David_&#x200B;发布于&#x200B;_2014-12-17_

## 显示感谢信息，但不显示后续登陆页面

通常，当您使用Marketo表单时，会创建两个登陆页面 — 一个用于将表单放置到上，另一个用于在表单完成后重定向到。 但是，在某些情况下，您可能不希望维护两个独立但非常相似的登陆页面。 实际上，您可以使用Forms 2.0 JavaScript API将同一登陆页面用于表单和感谢消息。 为此，请首先创建您的注册登陆页面和表单，然后按照常规方式将表单放置到登陆页面上。 然后，向页面中添加一个HTML元素。 在此元素中，我们添加了一些在提交表单时激活的代码。 然后，它会隐藏表单并显示隐藏项 <div> 其中包含感谢信息。 您的JavaScript应如下所示：

```javascript
//Edit host with your Marketo instance info
<script src="//<host>/js/forms2/js/forms2.js"></script>
<script>
MktoForms2.whenReady(function (form){
 //Add an onSuccess handler
  form.onSuccess(function(values, followUpUrl){
   //get the form's jQuery element and hide it
   form.getFormElem().hide();
   document.getElementById('confirmform').style.visibility = 'visible';
   //return false to prevent the submission handler from taking the lead to the follow up url.
   return false;
 });
});
</script>
```

编辑感谢消息文本。

`<div id="confirmform" style="visibility:hidden;"><p><strong>Thank you. Check your email for details on your request.</strong></p></div>`

在代码示例中，您将要编辑主机名并发送感谢消息。 第一个应引用您的Marketo实例(例如“//app-sj06.marketo.com/js/forms2/js/forms2.js”)，第二个应包含您希望在表单完成后显示的感谢文本。 该文本会显示在登陆页面中放置HTML元素的确切位置，因此请务必在属性工作表中进行编辑。 您还应确保HTML元素的层小于表单的层。 默认情况下，这两个元素都将被放置在第15层，因此如果您将HTML元素放置在第11层，那么您将处于安全状态。 如果不这样做，您将无法键入与感谢消息重叠的任何表单字段框。 无需更改表单或登陆页面上的跟进类型，因为JavaScript将覆盖这些设置。 有关Marketo Forms API的详细信息，请查看[开发人员文档](/help/javascript-api/forms-api-reference.md)。

由&#x200B;_Kristin_&#x200B;发布于&#x200B;_2014-12-19_

## 突出显示在Source Platform上构建的打开Marketo项目

这是正在进行的系列文章中的第一个帖子，该系列重点讲述了开发人员社区围绕Marketo平台构建的开源项目。 我们在Marketo的GitHub帐户[上维护有](https://github.com/Marketo/Community-Supported-Client-Libraries)列表，我们将在其中跟踪Marketo开发人员社区创建的客户端库和项目。 以下是围绕Marketo REST和SOAP API开发的三个项目。 Daniel Chesterton在PHP中为Marketo REST API创建了[客户端库](https://github.com/dchesterton/marketo-rest-api)。 客户端库当前涵盖12个REST API端点。Elixiter的** Kyle Halstvedt创建了一个项目，用于将Marketo静态列表中的潜在客户提取到Google电子表格中[。 ](https://github.com/Elixiter/mkto_google-spreadsheet)Kyle的项目使用Marketo REST API。  David Santoso为Marketo SOAP API创建了[Ruby gem。](https://github.com/davidsantoso/markety)此项目可帮助您更快地将Marketo SOAP API与Ruby on Rails应用程序集成。  我们很高兴能够在Marketo平台上看到开发人员社区创建的更多项目。 如果您正在处理Marketo平台的开源项目，请[通过拉取请求将其提交到此GitHub存储库](https://github.com/Marketo/Community-Supported-Client-Libraries)。

由&#x200B;_Murta_&#x200B;发布于&#x200B;_2015-01-02_

## 根据用户的位置动态更改页面内容

假设您想要根据用户所在的位置动态更改登陆页面上的电话号码。 例如，如果该人员位于加利福尼亚，则您要在登陆页面上向他们显示加利福尼亚办事处的电话号码，但如果他们位于日本，则您想要向他们显示日本办事处的电话号码。  实现此目标的一种方法是使用JavaScript和[HTML5地理位置API](https://developer.mozilla.org/en-US/docs/Web/API/Geolocation_API)。 此方法的好处是，我们可以创建一个登陆页面，并根据用户的位置动态更改该页面，而不是多个静态登陆页面。 我们将介绍以下技术实施详细信息。 我们为办公室位置创建一个具有经纬度坐标的对象，并创建一个具有办公室电话号码的第二个对象。 在生产中，更好的做法是将这两个对象组合成一个对象。

```json
//Coordinates for Marketo offices
var officeLocations = {
    "San Mateo": {latitude: 37.5596465, longitude: -122.2870142},
    "Atlanta": {latitude: 33.8547013, longitude: -84.35552349999999},
    "Tokyo": {latitude: 35.6895, longitude: 139.6917},
    "Dublin": {latitude: 53.3478, longitude: -6.2603097},
    "Sydney": {latitude: -33.873651, longitude: 151.2068896},
    "Portland": {latitude: 45.512089, longitude: -122.6763367},
    "Tel Aviv": {latitude: 32.0852999, longitude: 34.78176759999999}
}

//Phone numbers for Marketo offices
var officePhoneNumbers = {
    "San Mateo": "+1-650-376-2300",
    "Atlanta": "+1-877-260-6586",
    "Tokyo": "+81-03-6759-8280",
    "Dublin": "+353-1-242-3000",
    "Sydney": "+61-2-9045-2711",
    "Portland": "+1-877-260-6586",
    "Tel Aviv": "+1-877-260-6586"
}
```

我们创建一个方法以请求用户的位置。 为了处理错误，如果无法访问用户的位置，我们将默认使用Marketo的总部及其电话号码。

```javascript
//Method to get user's current location. Returns a position object with user's geo coordinates
function getLocation() {
    if (navigator.geolocation) {
        navigator.geolocation.getCurrentPosition(findNearestOffice);
    } else {
        x.innerHTML = "Marketo Location: San Mateo
Marketo Phone Number: +1-877-260-6586";
    }
}
```

最后，我们创建一个方法以找到距离用户所在位置最近的办公室，然后返回页面上最近办公室的电话号码。 此方法使用[Geolib中的findNearest方法，后者是提供地理空间操作](https://github.com/manuelbieh/Geolib)的JavaScript库。

```javascript
//Find nearest Marketo office to user's location
function findNearestOffice(position) {
        var nearestOffice = geolib.findNearest({latitude: position.coords.latitude, longitude: position.coords.longitude}, officeLocations);
        x.innerHTML = "Marketo Location: " + nearestOffice.key + "
Marketo Phone Number: " +  officePhoneNumbers[nearestOffice.key];
}
```

以下是完整实施。 当用户单击页面上的按钮时，我们会触发getLocation方法。 此[GitHub存储库](https://github.com/MurtzaM/Find-Nearest-Marketo-Office)包含设置此演示所需的文件。

由&#x200B;_Murta_&#x200B;发布于&#x200B;_2014-12-20_

## 商机跟踪和多个域

Marketo的Munchkin跟踪代码可帮助您跟踪对网站的访问。 您可能希望使用Munchkin跟踪代码对网站上大部分或所有页面的Cookie匿名潜在客户。 让我们介绍Munchkin的工作原理。 系统会记录现有潜在客户的页面访问次数，非Cookie访客对页面的访问将导致创建并存储新的Cookie，并在Marketo数据库中创建新的匿名潜在客户。 如果访客当前域尚无Cookie，Munchkin跟踪器将自动Cookie。 在Marketo中，它会在商机的活动日志中记录事件（单击链接、访问网页或新商机）。 对于给定访客，Cookie中存储的值是唯一的。 该值是唯一Munchkin帐户跟踪ID、域名、时间戳和随机整数的组合。
**如果我拥有多个域，会发生什么情况？**&#x200B;假设您有两个要跟踪的网站：`<www.apples.com>`和`<www.bananas.com>`。 您可以将跟踪代码放置到两个网站上，但您需要考虑以下事项。 Marketo Cookie是“第一方Cookie”，因此特定于域。 这意味着网站1的访客将被创建为Marketo中的匿名潜在客户，如果该潜在客户随后转到网站2，则将在Marketo中创建第二个单独的匿名潜在客户。 如果潜在客户填写了网站1上的表单，那么此记录便为已知，则网站2的匿名记录将保留并继续累计对该网站的后续访问。 如果该潜在客户随后使用与网站1上使用的完全相同的电子邮件地址填写网站2上的表单，则两个已知潜在客户将自动合并，并且所有过去和未来的行为都将在Marketo中的一条记录中进行跟踪。 两个Cookie ID都与同一潜在客户相关联，并且所有Web活动（来自任一域）都将位于该潜在客户上。
**多个子域呢？**&#x200B;子域不是问题。 我们以Marketo.com为例。 它有多个用于不同语言的子域，如fr.marketo.com和de.marketo.com。 对于子域，所有活动都将根据相同的潜在客户记录/Cookie进行记录。

由&#x200B;_David_&#x200B;发布于&#x200B;_2015-01-13_

## 更改Marketo表单上的提示文本颜色

假设您想在Forms 2.0中更改提示文本颜色（也称为占位符文本）。这可以通过自定义CSS实现。 例如，在下面的屏幕截图中，我将此Marketo表单中的提示文本显示为蓝色。 根据您使用Marketo Forms的方式，可通过三个选项来完成此操作。

**选项1：如果您正在嵌入Marketo表单，请直接将下面的CSS添加到您的主CSS文件中。**

```css
::-webkit-input-placeholder {
  color: blue;
}
::-moz-placeholder {
  color: blue;
}
:-ms-input-placeholder {
  color: blue;
}
:-moz-placeholder {
  color: blue;
}
```

**选项2：嵌入Marketo表单时，可以在`<style></style>`部分的`<head>`标记之间直接在页面上添加CSS。**

```css
<style>
::-webkit-input-placeholder {
  color: blue;
}
::-moz-placeholder {
  color: blue;
}
:-ms-input-placeholder {
  color: blue;
}
:-moz-placeholder {
  color: blue;
}
</style>
```

**选项3：如果您在Marketo登录页面上使用Marketo表单，可以通过Marketo UI添加此自定义CSS。**&#x200B;在Marketo导航树中找到登陆页面。 然后单击“编辑草稿”。 单击编辑页面Meta Tags 。 将以下CSS添加到自定义HEAD HTML部分。 `<style></style>`标记应包含在内。

```css
<style>
::-webkit-input-placeholder {
  color: blue;
}
::-moz-placeholder {
  color: blue;
}
:-ms-input-placeholder {
  color: blue;
}
:-moz-placeholder {
  color: blue;
}
</style>
```

单击批准草稿。 当您现在访问Marketo登陆页面时，提示文本是您在CSS中定义的颜色。 有关Marketo Forms的详细信息，[请访问文档](/help/javascript-api/forms-api-reference.md)。

由&#x200B;_Murta_&#x200B;发布于&#x200B;_2015-01-14_

## 通过REST API获取活动数据

假设您希望获取本月添加到列表的所有潜在客户。 使用[获取潜在客户活动REST API](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getLeadActivitiesUsingGET)，您可以获取此数据。 在调用获取潜在客户活动API之前，需要从身份验证API获取访问令牌，并从[获取分页令牌API](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getActivitiesPagingTokenUsingGET)获取开始日期令牌。 以下是Ruby中的示例代码，该代码浏览您需要调用的各个API端点，以返回本月添加到列表的所有潜在客户。 1.获取访问令牌**

```ruby
require 'rest_client'
require 'json'

# Build request URL
# Replace AAA-BBB-CCC with your Marketo instance
marketo_instance = "<https://AAA-BBB-CCC.mktorest.com/identity/oauth/token?grant_type=client_credentials>"
# Relace with your client id
client_id = "99985d09-22a9-3jl2-84av-f5baae7c3a45"
# Replace with your your  client secret
client_secret = "tZPVrKiEmUDezE18yZfeaPlTJ2vKn2fw"
request_url = marketo_instance + "&client_id=" + client_id + "&client_secret=" + client_secret

# Make request
response = RestClient.get request_url

# Parse reponse and return only access token
results = JSON.parse(response.body)
access_token = results["access_token"]
puts access_token
```

1. 获取分页令牌

```ruby
require 'rest_client'
require 'json'

# Build request URL
# Replace AAA-BBB-CCC with your Marketo instance
marketo_instance = "<https://AAA-BBB-CCC.mktorest.com>"
endpoint = "/rest/v1/activities/pagingtoken.json"
# Replace with your access token
auth_token =  "?access_token=" + "ac756f7a-d54d-41ac-8c3c-f2d2a39ee325:ab"
# Specify date
since_date_time = "&sinceDatetime=2015-01-01T00:00:00-08:00"
request_url = marketo_instance + endpoint + auth_token + since_date_time

# Make request
response = RestClient.get request_url

# Returns Marketo API response
puts response
```

1. 获取活动数据**要确定此调用所需的活动类型ID，请查询[获取活动类型API](/help/rest-api/activities.md)。 获取活动类型API会返回一个架构，其中包含所有活动类型和关联的ID。 例如，对于创建的新潜在客户，它会返回id 12；对于网页访问，会返回id 1。

```java
require 'rest_client'
require 'json'

# Build request URL
# Replace AAA-BBB-CCC with your Marketo instance
marketo_instance = "<https://AAA-BBB-CCC.mktorest.com>"
endpoint = "/rest/v1/activities.json"
# Replace with your access token
auth_token =  "?access_token=" + "ac756f7a-d54d-41ac-8c3c-f2d2a39ee325:ab"
# Specify datetime needed as nextPageToken
since_date_time = "&nextPageToken=GIYDAOBNGEYS2MBWKQYDAORQGA5DAMBOGAYDAKZQGAYDALBQ"
# Specify activities needed
activity_type_ids = "&activityTypeIds=24"
request_url = marketo_instance + endpoint + auth_token + since_date_time + activity_type_ids

# Make request
response = RestClient.get request_url

# Returns Marketo API response
puts response
```

1. 获取潜在客户活动API会返回一个分页令牌，其中包含可用于对结果集进行分页的每个响应。**有关详细信息，请参阅[REST API文档](/help/rest-api/rest-api.md)。

由&#x200B;_Murta_&#x200B;发布于&#x200B;_2015-01-20_

## 突出显示基于Source平台构建的打开Marketo项目：第二部分

这是正在进行的系列文章中的第二个帖子，其中强调了开发人员社区围绕Marketo平台构建的开源项目。 我们在Marketo的GitHub帐户[上维护有](https://github.com/Marketo/Community-Supported-Client-Libraries)列表，我们将在其中跟踪Marketo开发人员社区创建的客户端库和项目。 以下是围绕Marketo SOAP和Munchkin API开发的三个项目。 [PunchTab](https://www.punchtab.com/)在Python中为Marketo SOAP API创建了[客户端库](https://github.com/PunchTab/suds-marketo)。 [Flickerbox](https://www.flickerbox.com/)在PHP中为Marketo SOAP API创建了[客户端库](https://github.com/flickerbox/marketo)。* [Richard Morrison](https://x.com/mozz100)创建了[一个PHP脚本以从Marketo SOAP API获取潜在客户数据，然后使用JavaScript将此数据传递给客户端。](https://github.com/mozz100/marketo-whodat)此项目可帮助您在Marketo中根据用户数据修改页面。  我们很高兴能够在Marketo平台上看到开发人员社区创建的更多项目。 如果您正在处理Marketo平台的开源项目，请[通过拉取请求将其提交到此GitHub存储库](https://github.com/Marketo/Community-Supported-Client-Libraries)。

由&#x200B;_Murta_&#x200B;发布于&#x200B;_2015-01-20_

## 将RTP推荐引擎点击量发送到Google Analytics

这是一个解决方案，适用于Marketo实时Personalization (RTP)用户，用于查看Google Analytics中内容推荐引擎的点击量。 访客单击内容推荐栏后，即会在事件类别“RTP-Recommendations”下将事件发送到Google Analytics。 在Analytics中，推荐文本（显示在栏中）将附加到“事件标签”，推荐资产的URL将附加到“事件操作”。 该脚本适用于Classic Google Analytics和Google Universal Analytics。 此标记应粘贴到HTML页面代码的末尾，因此它是`</body>`标记之前的最后一个标记。

```javascript
$( document ).ready(function() {
if(document.getElementsByClassName("insightera-bar-content").length
 >0){
document.getElementsByClassName("insightera-bar-content")[0].getElementsByTagName('a')[0].addEventListener("click",
 function(){
assetName
 = document.getElementsByClassName("insightera-bar-content")[0].getElementsByTagName('a')[0].innerText;
assetURL
 = document.getElementsByClassName("insightera-bar-content")[0].getElementsByTagName('a')[0].href;
assetURL=
 assetURL.substring(assetURL.lastIndexOf("/"),assetURL.indexOf("?iesrc"));
console.log(assetName

 * " | " + assetURL);
if(typeof(_gaq)
 != "undefined"){
//Classic
_trackEvent("RTP-Recommendations",
 assetName , assetURL , {'nonInteraction': 1});
}else
 if(typeof(ga) !="undefined"){
//Universal
ga('send',
 'event',"RTP-Recommendations", assetName , assetURL, {'nonInteraction': 1});
}
});
}
});
```

由&#x200B;_Yanir_&#x200B;发布于&#x200B;_2015-01-22_

## 将Marketo REST API与Boomi结合使用：获取和存储REST身份验证Toke

在Marketo中，设置自动导出符合特定标准的潜在客户是非常常见的用例。 虽然目前无法在Marketo界面中执行此操作，但使用第三方工具(例如Dell Boomi、包含一些数据管理促销活动的静态列表和Marketo REST API)可轻松完成此操作。 REST API？ 我以为Boomi没有Marketo REST API连接器！ 目前，它不起作用，但可以使用HTTP连接器和手动定义jSON响应形状来完成相同的操作。 第一步是设置您的Marketo实例以使用REST API，如[REST API Marketo开发人员页面](/help/rest-api/rest-api.md)中所述。 我还将假定您有权访问Dell Boomi帐户，并且具备Boomi技能来创建这些类型的集成流程。 最后一个过程如下所示，并将包括对以下Marketo REST API操作的调用，每个操作均具有一个关联的jSON响应形状，该形状可在开发人员网站上找到。 为了节省时间，我在[身份验证](/help/rest-api/authentication.md)的JSON示例下方列出了它们

```json
{
  "access_token": "",
  "token_type": "",
  "expires_in": 0,
  "scope": ""
}
```

[按列表ID](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Static-Lists)获取多个潜在客户的JSON示例

```json
{
  "requestId": "",
  "success": true,
  "nextPageToken": "",
  "result": [
    {
      "id": 0,
      "email": "",
      "firstName": "",
      "lastName": ""
    },
    {
      "id": 0,
      "email": "",
      "firstName": "",
      "lastName": ""
    },
    {
      "id": 0,
      "email": "",
      "firstName": "",
      "lastName": ""
    }
  ]
}
```

[从列表](https://developer.adobe.com/marketo-apis/api/mapi/#operation/removeLeadsFromListUsingDELETE)中删除潜在客户的JSON示例

```json
{
  "requestId": "",
  "success": true,
  "result": [
    {
      "id": 1,
      "status": ""
    },
    {
      "id": 2,
      "status": "",
      "reasons": [
        {
          "code": "",
          "message": ""
        }
      ]
    }
  ]
}
```

定义属性：在我们开始调用REST之前，务必要外部化并封装您使用的变量。 我定义了下面显示的以下内容。

* ClientID：从REST Launchpoint服务获取此项
* 客户端密钥：从REST Launchpoint服务中获取此密钥
* AccessToken：我们通过REST调用获取此信息
* 静态ListID：我们将操作的静态列表的列表ID。 从Marketo中的URL获取此项
* 字段：Rest服务从Marketo为每个潜在客户提供的以逗号分隔的字段列表。 我的地址是“id， email，firstName，lastName” * IDStringToDelete：最终将包含静态列表中所有潜在客户的ID，以便将其从列表中删除
* ActivityTypes：将在本博客的第2部分中使用，我将在此部分中做进一步介绍！
* SinceDateTime：将在本博客的第2部分中使用，我将在此部分进行详细介绍！
* PagingToken：将用于此博客的第2部分，我将展开此部分！
* 文件夹 — 传出：SFTP服务器上传出文件夹的路径。 在此示例中，我使用“/data/outgoing”。 这使我们能够参数化SFTP操作，以使其成为通用操作。

身份验证令牌：如我所提，在创建具有“无数据”开始形状的流程后，我们将在画布上放置连接器（这只是个人选择，我喜欢我的所有连接器看起来像英国插头）。
连接器应按如下方式进行配置： — 连接器是HTTP GET客户端 — 连接使用URL： `https://123-ABC-456.mktorest.com`(请注意，结尾处没有/rest，因此我们可以将其用于REST调用并用于获取身份访问令牌。 并将123-ABC-456更改为您的Marketo实例的正确名称) — 操作为“获取oAuth令牌”（新！） — 请求配置文件=无 — 响应配置文件= JSON — 名为“身份验证令牌响应”的新配置文件 — 内容类型：普通 — HTTP方法：GET — 资源路径（添加4个不含引号）：“identity/oAuth/token？grant_type=client_credentials&amp;client_id=”；“ClientID（替换变量）”；“&amp;client_secret=”；“Id clientSecret （替换变量）” — “配置 — >参数 — >(+)”下的设置参数：设置ClientID =流程属性客户端ID；设置ClientSecret =流程属性客户端密码之后，将成功令牌存储在流程属性“AccessToken”变量中，如图所示，从jSON响应中提取该令牌。
此步骤的模式将在后续步骤中重复，但会使用具有不同jSON返回配置文件的新操作。 事实上，许多REST调用将以相同的方式进行处理，但会有一些细微的更改！ 在下一批中，我们将展开此内容，并使用REST从静态列表中获取潜在客户列表！ 目前，请运行该流程，但在“设置属性”之后放置一个停止形状，然后在debug中运行，以确保您看到与Marketo相同的令牌。 他们应该完全匹配！

由&#x200B;_John_&#x200B;发布于&#x200B;_2015-01-26_

## 使用Google Font API向Marketo登陆页面添加自定义字体

**注意：这是[Murtza Manzur](https://www.linkedin.com/in/murtzam)的博客文章。 Murtza是旧金山湾区的Marketo开发人员宣传专员。**
假设您正在Marketo中创建登陆页面，并希望使用自定义字体。 可以使用Google Font API实现此功能。  将引用Google Fonts的导入方法添加到CSS文件：

`@import url(http://fonts.googleapis.com/css?family=Open+Sans:400,300,600);`

由&#x200B;_Murta_&#x200B;发布于&#x200B;_2015-01-26_

## 使用电子邮件脚本将商机的名字变为大写

假设某个潜在客户以小写输入其名称，例如“John doe”。 但是，当您发送电子邮件促销活动时，您会希望将电子邮件中的商机名称变为大写，例如John Doe。 您可以使用电子邮件脚本将商机的名称变为大写。 下面是操作方法。

1. 在电子邮件程序中，单击“我的令牌”选项卡。
1. 通过将“Email Script”从右侧面板拖到中面板来创建新的电子邮件脚本令牌。 命名令牌。
1. 在编辑脚本令牌文本框中，粘贴以下代码。 在右侧面板中的Lead对象下，选中First Name复选框。 然后单击“保存”。

```javascript
# set($name = ${lead.FirstName})
# set($formattedFirstName = $name.substring(0).toUpperCase())
$formattedFirstName
```

1. 在电子邮件资源中引用令牌。 它将输出商机的名字并将第一个字母大写。 有关电子邮件脚本的更多信息，请访问[电子邮件脚本文档](/help/email-scripting.md)。

由&#x200B;_Murta_&#x200B;发布于&#x200B;_2015-01-26_

## 从Marketo REST API获取所有潜在客户

StackOverflow中存在[问题，询问如何通过REST API从Marketo获取所有潜在客户列表](https://stackoverflow.com/questions/28184900/how-do-i-get-the-list-of-all-the-leads-in-marketo)。 您可以使用[按筛选器类型REST API终结点](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getLeadsByFilterUsingGET)获取多个潜在客户来查询此数据。 Marketo中的商机按顺序分配商机ID，从1开始。 使用[按过滤器类型REST API终结点](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getLeadsByFilterUsingGET)获取多个潜在客户，您可以在每次调用中按潜在客户ID查询300个潜在客户。 在每次调用此端点时，您必须将id指定为filterType，并将潜在客户id指定为filterValues。 要获取所有潜在客户，您需要逐一查看潜在客户总数300。 Y
您可以通过Marketo UI获取Marketo实例中的商机总数。 在Marketo UI中，转到Lead Database选项卡，单击System Smart Lists ，单击All Leads Smart List ，最后单击Leads选项卡。 然后单击ID列并按降序排序。 在对潜在客户进行排序后，当您查询所有潜在客户时，第一个潜在客户的ID将是潜在客户ID的上限。 如果您无权访问Marketo UI以获取商机总数，则有[备用方法可使用Get Lead Activities REST API获取此值](https://stackoverflow.com/questions/28419967/get-all-leads-programmatically-in-marketo-v1)。

1. 首次API调用：将……替换为以下值之间的所有值：

`/rest/v1/leads.json?filterType=Id&filterValues=1,2,3,...,298,299,300`

以下是Ruby中第一个调用的示例代码。

```java
require 'rest_client'
require 'json'

# Build request URL
# Replace AAA-BBB-CCC with your Marketo instance
marketo_instance = "<https://AAA-BBB-CCC.mktorest.com>"
endpoint = "/rest/v1/leads.json"
# Replace with your access token
auth_token =  "?access_token=" + "ac756f7a-d54d-41ac-8c3c-f2d2a39ee325:ab"
# Replace with filter type and values
ids_needed = (1..300).to_a.join(",")
filter_type_and_values = "&filterType=Id&filterValues=" + ids_needed
request_url = marketo_instance + endpoint + auth_token + filter_type_and_values

# Make request
response = RestClient.get request_url

# Returns Marketo API response
puts response
```

1. 第二个API调用和每个后续API调用将遵循相同的模式，直到达到潜在客户总数：

```
//replace ... with all the values in between
/rest/v1/leads.json?filterType=Id&filterValues=301,302,303,...,598,599,600
```

有关详细信息，请参阅[REST API文档](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getLeadsByFilterUsingGET)。

由&#x200B;_Murta_&#x200B;发布于&#x200B;_2015-01-28_

## 执行从Iframe到父页面的表单提交操作

我们已经看到了一些用户使用iframe表单的情况，他们希望将填写表单的访客定向到感谢页面或PDF、视频等。 问题在于，由于表单嵌入的登陆页面与父页面不同，因此该操作仅在表单所在的内部页面上执行。 要解决此问题，我们创建了2个JavaScript标记。 将作为HTML元素插入您的iframe页面，或直接插入用于iframe的登陆页面模板。 将其放在最后的`</body>`标记之前。 第一个标记在父页面上执行操作，第二个标记将在新选项卡中打开该页面。

**父页面上的表单操作**

```javascript
<script>
MktoForms2.whenReady(function (form){
form.onSuccess(function (values, url){
window.parent.location.assign(url);
return false;
           });
});
</script>
```

在新选项卡中执行&#x200B;**表单操作**

```javascript
<script>
MktoForms2.whenReady(function (form){
var newWin;
form.onSubmit(function (){
newWin = window.open('about:blank', 'myWindow');
});
form.onSuccess(function (values, url){
newWin.location.replace(url);
return
 false;
});
});
</script>
```

由&#x200B;_Yanir_&#x200B;发布于&#x200B;_2015-02-02_

## 将来自YouTube视频的视图数据发送到市场

假设您要根据潜在客户是已开始还是已完成特定视频，在Marketo中对他们进行分段。 在Marketo中使用Munchkin、YouTube的Iframe API和智能列表可做到这一点。 本文中的示例代码可让您通过Munchkin将视频开始事件和视频结束事件发送到Marketo中。 为使此功能正常工作，您还必须先在页面上加载Munchkin，然后才能开始将视频查看事件发送到Marketo。 开始和结束的视频将显示在商机的活动日志中。 将数据放入Marketo中后，您可以创建一个智能列表，并对已开始或完成视频的潜在客户进行分段。

1. 获取要嵌入的YouTube视频的id。**在要使用的YouTube视频的URL中，记下ID，该ID是`v=`之后的随机字符系列。
1. 将步骤1中的YouTube视频ID放置在此代码示例的第八行中。 然后，将代码放置在页面HTML中的`</body>`之前。

```javascript
<div id="player"></div>
<script>
var tag = document.createElement('script');
tag.src = "https://www.youtube.com/iframe_api";
document.getElementsByTagName('head')[0].appendChild(tag);

//Change 'iiqxcjxJ5Us' to video needed
var player, videoId = 'iiqxcjxJ5Us';
function onYouTubeIframeAPIReady() {
player = new YT.Player('player', {
height: '390',
width: '640',
videoId: videoId,
events: {
'onStateChange': onPlayerStateChange
}
});
}

function onPlayerStateChange(event) {
switch( event.data ) {
//Send video started event to Marketo
case YT.PlayerState.PLAYING: Munchkin.munchkinFunction('visitWebPage', {
url: '/video/'+videoId
, params: 'video=started'
}
);
break;
//Send video finished event to Marketo
case YT.PlayerState.ENDED: Munchkin.munchkinFunction('visitWebPage', {
url: '/video/'+videoId
, params: 'video=finished'
}
);
break;
}

}
</script>
```

1. 在Marketo中创建一个智能列表，该智能列表包含要查找的视频URL和视图事件，其值为“Querystring contains”。 有关YouTube Iframe API的详细信息，[请访问YouTube的API文档](https://developers.google.com/youtube/iframe_api_reference)。 有关Munchkin的详细信息，[查阅Marketo开发人员文档](/help/javascript-api/lead-tracking.md)。

由&#x200B;_Murta_&#x200B;发布于&#x200B;_2015-02-02_

## Marketo SOAP API提示和技巧

注意：这是访客博客帖子。 [Ed Blachman是](https://www.linkedin.com/uas/login?session_redirect=https%3A%2F%2Fwww.linkedin.com%2Fprofile%2Fview%3Fid%3D2777965)TIBCO Software的高级架构师[，该公司是企业软件](https://exchange.adobe.com/apps/browse/ec?product=MRKTO)的知名供应商。 Ed正在开发一些产品，这些产品允许Gartner所谓的“公民开发人员”整合他们使用的云服务，而无需自己进行任何编程。 [Marketo的SOAP API](/help/soap-api/soap-api.md)是一个功能强大的工具，开发人员可以借助它来利用Marketo的强大功能并将其与我们自己的应用程序集成。 在[正式文档](./getting-started.md)和[社区资源](https://nation.marketo.com/)之间，提供了许多有关如何使用它的信息。 刚开始使用时，我严重依赖这些信息，并发现它们非常有价值。 但是，在这个过程中，我积累了一些我在这些地方从未见过的技巧和窍门。 下面是我发现的一些问题。

**开发人员的沙盒**沙盒当然是面向API开发人员的绝佳资源：您可以在此安全的地方试验Marketo功能，添加和删除对象而不会干扰组织的实际Marketo用户进行的真实营销活动。 但是，沙盒并非万能药。
例如，我需要和另一个开发团队共享我们的沙盒，这需要一些时间，因为他们已经习惯了他们拥有沙盒的概念。 最终，我们提出了几个共享的最佳实践： — 请勿编写依赖于对沙盒内容的完全了解的测试。 作为共享资源，架构可能会随时更改，恕不另行通知，潜在客户数据库或程序或其他实体中的完整条目也可能随时更改。 如果您的测试假定您完全了解沙盒，则开发周期将为您共享沙盒的组创建封锁期。 由于它们的开发周期通常与您的周期不一致，因此这相当于占用了资源，而不是很酷。 如果你仔细想一想，也没必要。  — 使用惯例来标记您所有的东西 — 您的商机、您的商机架构字段、您的程序，等等。 如果您每个人都可以识别自己的对象，并且如果您同意您的共同租户的意见，即每个人都将保留其他人的对象，那么您应该为共享奠定坚实的基础。 对于潜在客户，您可以创建一个自定义字段，然后使用此自定义字段创建一个约定，以将这些潜在客户识别为测试潜在客户。 对于列表或程序，可以使用某些字符串来开始对象名称，这些字符串将对象标识为您所属的对象。  — 考虑编写测试来清理自身 — 首先创建您感兴趣的对象，然后访问、更新或选择性地删除这些对象，最后删除这些对象。 (请注意，在SOAP API中不能百分之百地实现此目标，因为并非沙盒或这方面的实际实例中的所有内容都可以通过SOAP API进行管理。 即使如此，还是值得尽可能多地这样做。)

**实际实例**&#x200B;沙盒的问题在于它未在生产中使用，因此很难了解在Marketo实例中的实际使用情况。 现在，如果您幸运地拥有团队中的Marketo高级用户，或者您正在为Marketo内部用户执行定制开发，则这并不是问题。 但就我的团队而言，这确实是一笔大买卖。 我们谁也不是Marketo专家，由于要求我们了解大量云服务，我们只是没有足够的人手来成为任何方面的专家。 我们从访问真实实例中收集到的一些见解如下： — 大型潜在客户架构。 我们访问的生产实例中的潜在客户架构具有200多个字段。 这使我们的UI设计人员非常清楚，他们设计的UI必须适应该大小（或更大）的架构。  — 突发使用。 我们看到，最高使用时间和低使用时间之间存在两个数量级的差异（就创建或更新的潜在客户数量而言）。 这会影响我们从API调用中返回的数据量（显而易见），以及API调用做出响应所需的时间（可能不太明显）。

**API调用响应时间**&#x200B;根据一天中的时间、API调用的详细信息和实例的内容，您可能会发现SOAP API的响应时间比平均时间长。 有时，我们的API调用需要一分钟半的时间才能做出响应。 您需要了解处理它的可能性： — 测试。 也许这对您的使用情况没有影响。 但不要只是假设，做一些测试。  — 调整您的用法。 在本例中，最大的问题是我们将[getMultipleLeads](/help/soap-api/getmultipleleads.md)调用的页面大小设置为API允许的最大。 在我们的环境中，这有一定意义，因为我们的目标是尽可能高效地利用客户的API配额。 但在您的上下文中，您可能不需要如此强烈地担心用户的API调用配额，在这种情况下，通过请求更小的数据页面，您一定会获得更好的响应时间。

**潜在客户分区** Marketo提供了功能强大的工具 — 分区和工作区 — 允许多个营销组共享单个Marketo实例。 但是，这些工具不会直接反映在SOAP API中。 例如，当您使用getMultipleLeads来获取自某个日期时间以来已更新或创建的所有潜在客户时，您将获取实例中所有与此情况相同的潜在客户，而不考虑（且无需指示）哪个分区或工作区包含任何给定潜在客户。 潜在客户创建和向列表中添加潜在客户是其他上下文，在这些上下文中，潜在客户分区可能会影响您的API调用的实际操作。 请注意，这意味着分区和工作区可能不是上面讨论的沙盒共享问题所需的解决方案。 那么，你如何判断这是否对你而言是个问题？ 我发现所有这些都很有帮助：开发人员宣传员致力于帮助我们成功使用API，并且在存在问题的地方，他们非常善于寻找答案。 - [API文档](./getting-started.md)。 宣传人员已经将此问题纳入了一些文档中，并且作为他们致力于我们成功的一部分，他们对更新文档真的很在行。  — 您自己的测试用例。 虽然使用分区和工作区共享沙盒可能不是个好主意，但沙盒是一个与分区和工作区一起玩以了解它们是否对您的预期用途构成挑战的好地方。 （这也是缩小你向福音派人士提问的一个好方法，这始终是个好主意。 ）

**TIMTOWTDI和测试**“有多种方法可以做到这一点” — Perl编程准则在某些上下文中实际应用于Marketo SOAP API。 例如，我想将更新一组潜在客户与将这些潜在客户添加到某个列表结合使用。 SOAP API为您提供了两种实现此目标的方法：1. [importToList](/help/soap-api/importtolist.md) + [getImportToListStatus](/help/soap-api/getimporttoliststatus.md)。 在阅读相关文档时，这显然是“正常的”方法。 但是，您必须轮询导入操作的状态这一事实为我带来了一个黄色标记。 这真的是我想以这种方式实施导入吗？ 1. [syncMultipleLeads](/help/soap-api/syncmultipleleads.md) + [listOperation](/help/soap-api/listoperation.md)。 这看起来远不如单一importToList调用优雅，但它不依赖轮询。 这是一个可行的选择吗？ 像这样的案例对于福音传教士来说很难处理，因为它们真的取决于你所处理的案例的性质，以及你试图做的事情。 幸运的是，如果您已经建立了一个强大的单元测试环境，那么您应该能够用它来探索类似这样的问题。 在此特定用例中，发现选项2比选项1更适合我的用例 — 不是因为轮询，而是因为我在importToList上遇到了面向字段的限制，并且也是因为我在尝试编写可以在我无法控制的上下文和实例中使用的代码。 但您的用例可能不同，测试是您发现的唯一方法。

**结论**&#x200B;我认为这些都不是大秘密。 另一方面，如果在开始之前就知道这些的话，我早就领先了。 我希望你能觉得它有用。

由&#x200B;_David_&#x200B;发布于&#x200B;_2015-02-05_

## 在Boomi中使用Marketo REST API：从静态列表中获取和删除潜在客户

在本系列的第1部分中，我讨论了如何通过Boomi和Boomi HTTP连接器开始使用REST API，特别是如何获取访问REST API所需的身份验证令牌并将其存储在进程变量中。 接下来，我们将开始向Marketo发起呼叫，在本期中，我将向您展示如何同时[按列表ID获取多个潜在客户](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Static-Lists)和[从列表中删除潜在客户](/help/rest-api/lead-database.md)。 请特别留意从名单上去掉潜在客户，因为在那里，Boomi有一个非常“轻描淡写”和微妙的方面，当我们到达那里时，我就会对它进行拓展。

在下一次分期付款中，我们将扩展此功能，以开始做一些有趣的事情，如获得Lead活动，但那将是另一天的博客。 在本期中，我们将查看第二和第三个重点区域。 作为回顾，我已在下面包含了我们所需的JSON响应。 请记住，要在Boomi中创建JSON配置文件，您只需创建一个JSON类型的配置文件组件，然后单击“导入”并选择该文件。 Boomi负责其余工作，推断是否应该允许多个ID。 [按列表ID](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Static-Lists)获取多个潜在客户的JSON示例

```json
{
  "requestId": "",
  "success": true,
  "nextPageToken": "",
  "result": [
    {
      "id": 0,
      "email": "",
      "firstName": "",
      "lastName": ""
    },
    {
      "id": 0,
      "email": "",
      "firstName": "",
      "lastName": ""
    },
    {
      "id": 0,
      "email": "",
      "firstName": "",
      "lastName": ""
    }
  ]
}
```

[从列表请求](https://developer.adobe.com/marketo-apis/api/mapi/#operation/removeLeadsFromListUsingDELETE)中删除潜在客户的JSON示例

```json
{
   "input":[
      {
         "id": ""
      },
      {
         "id": ""
      }
   ]
}
```

[从列表响应](https://developer.adobe.com/marketo-apis/api/mapi/#operation/removeLeadsFromListUsingDELETE)中删除潜在客户的JSON示例

```json
{
  "requestId": "",
  "success": true,
  "result": [
    {
      "id": 1,
      "status": ""
    },
    {
      "id": 2,
      "status": "",
      "reasons": [
        {
          "code": "",
          "message": ""
        }
      ]
    }
  ]
}
```

Get Multiple Leads by List ID**使用与上一篇文章中定义的相同的连接，将另一个连接器(Get)放置到您的进程中。 创建一个名为“按列表ID获取多个销售线索”（我是确保一致性的严格要求）的新操作，其属性如下 — 请求配置文件：无（此调用使用请求URL） — 响应配置文件类型：jSON — 响应配置文件：根据上述“按列表ID获取多个销售线索”响应创建新的配置文件。 请注意，您可以对其进行更改，以使其返回所需的字段，而不仅仅是列出的字段。 请务必记住，JSON响应配置文件应与您从REST API请求的字段列表真正匹配，并且您应仅请求所需的字段。 在Process Properties对象中，我们定义了一个名为“fields”的属性，该属性是您希望REST返回的以逗号分隔的字段列表。 这个列表必须与配置文件匹配。 内容类型：text/plain（这只是URL请求） HTTP方法：GET（您在REST API文档中查找此变量，它始终列出）资源路径（添加5） rest/v1/list/ listID（替换变量） /leads.json？access_token= access_token（替换变量） &amp;fields=字段（替换变量）。 然后，在连接器的“参数”选项卡中，您可以输入变量值，我们之前已将所有这些变量值填充到流程属性中。 在下一部分中，我将介绍如何避免手动填充这些内容。 我将跳过流程中的以下部分：我将对“按列表ID获取多个潜在客户”的响应映射到平面文件配置文件，并将其粘贴到FTP服务器上，因为这是简单的Boomi功能。

从列表中删除潜在客户这个很有趣，我的同事之一，[丹羽肯](https://www.linkedin.com/uas/login?session_redirect=https%3A%2F%2Fwww.linkedin.com%2Fprofile%2Fview%3Fid%3D7429494)教我了另一种技术，它非常酷，基于下面显示的Boomi一篇题为“如何为RESTful应用程序构建POST请求”的文章。 ...但首先要做的事情。 在此过程中，从“Get Multiple Leads by List Id”中获得了“Get Multiple Leads by List Id”响应形状，我们需要将其映射到“Remove Leads from List Request”中，这表示映射相当简单，只需将我们从原始列表中的商机获得的ID映射到我们传递给删除jSON的ID列表中。 接下来，删除另一个连接器，并执行“发送”操作，使用相同的连接创建一个名为“从列表请求中删除潜在客户”的新操作。 其属性为请求配置文件的jSON内容类型： application/json请求配置文件： [JSON配置文件]从列表请求中删除潜在客户（从上述文件创建）响应配置文件类型： jSON响应配置文件： [JSON配置文件]从列表响应中删除潜在客户（从上述文件创建）内容类型： application/json HTTP方法：DELETE资源路径（添加4） rest/v1/lists/ listID （替换变量） /leads.json？access_token= access_token （替换变量）此处这个连接器的有趣之处在于。 我们不会在连接器选项卡中明确添加参数。 相反，如文章所述，我们创建与替换变量同名的动态文档属性。 在本例中，这些变量为listID和access_token。 执行此操作时，jSON形状将流入REST调用，并且参数将出现在URL上的相应位置。 我们无法对上次调用执行此操作，因为这是GET而非POST。 因此，此时，您已看到GET和POST REST API调用，并开始看到发出这些REST调用的模式。 在下一批中，我们将开始查看通过REST API导出Lead Activity ，它涉及面更广。

由&#x200B;_John_&#x200B;发布于&#x200B;_2015-02-06_

## 在YouTube登录页面上嵌入带有潜在客户跟踪的Marketo视频

在前一篇博客文章中，我描述了如何根据潜在客户已开始还是已完成特定的MarketoYouTube视频来细分他们。 在本博客帖子中，我们将介绍如何从该帖子中获得实施并将其用于Marketo登陆页面。

1. 导航到Marketo中要创建新登陆页面的项目。 单击新建本地资产，然后单击登陆页面。
1. 命名登陆页面。 分配页面URL。 选择模板。 然后单击“创建”。
1. 创建登陆页面后，单击编辑草稿。
1. 从右侧面板中，将“HTML”按钮拖到左侧的主画布上。
1. 随后会弹出的“自定义HTML编辑器”框中。 然后单击“保存”。
1. 通过拖动框轮廓调整HTML元素的大小。 然后单击“批准并关闭”。
1. 通过单击查看已批准的页面，测试登陆页面的实时版本。 此时将在新窗口中打开一个包含YouTube的登陆页面。 开始和结束的视频将显示在商机的活动日志中，如下面的第一个和第二个屏幕快照所示。 将数据放入Marketo中后，您可以创建一个智能列表，并对已开始或完成视频的潜在客户进行分段，如下面的屏幕快照所示。 有关YouTube Iframe API的详细信息，[请访问YouTube的API文档](https://developers.google.com/youtube/iframe_api_reference)。 有关Munchkin的详细信息，[请查阅Marketo开发人员文档](/help/javascript-api/lead-tracking.md)。

由&#x200B;_Murta_&#x200B;发布于&#x200B;_2015-02-09_

## 使用Munchkin进行单页应用程序Web跟踪

单页应用程序是一个网站，可在首次加载页面时加载导航站点所需的所有资源。 当用户单击链接时，将从第一个页面加载数据加载内容。 对于用户而言，网站的行为与预期一致，因为地址栏中的URL与传统页面导航类似。 Munchkin非常适用于传统网站，因为Munchkin在用户每次加载新页面时都会运行。 但是，对于单页应用程序，如果您不加载新页面，Munchkin将只运行一次。 我在本帖子中介绍的方法是跟踪用户何时单击链接，然后将此信息发送到Munchkin。 我们使用`clickLink` Munchkin函数实现此目标。 以下是jQuery中的示例实现，它将点击事件绑定到`clickLink` Munchkin方法。 在调用`clickLink` Munchkin方法时，它将为被单击的URL传递参数。

```javascript
<script>
$("a").on('click', function(event) {
    var urlThatWasClicked = $(this).attr('href');
    Munchkin.munchkinFunction('clickLink', { href: urlThatWasClicked});
});
</script>
```

由&#x200B;_Murta_&#x200B;发布于&#x200B;_2015-02-11_

## 通过REST API更改商机分数

假设您想使用API更改Marketo中的商机分数。 可以通过创建/更新潜在客户端点来处理REST API。 以下是Ruby中的代码示例，其中显示了如何进行此调用。

```ruby
require 'rest_client'
require 'json'

# Build request URL
# Replace AAA-BBB-CCC with your Marketo instance
marketo_instance = "https://AAA-BBB-CCC.mktorest.com"
endpoint = "/rest/v1/leads.json"
# Replace with your access token
auth_token =  "?access_token=" + "ac756f7a-d54d-41ac-8c3c-f2d2a39ee325:ab"
request_url = marketo_instance + endpoint + auth_token

# Build request body
data = { "action" => "updateOnly", "input" => [ { "email" => "<example@email.com>", "leadScore" => "30" } ] }

# Make request
response = RestClient.post request_url, data.to_json, :content_type => :json, :accept => :json

# Returns Marketo API response
puts response
```

在请求的JSON正文中，我们将`updateOnly`指定为操作。 这意味着请求只有在潜在客户存在时才有效，否则它将失败。 如果要在潜在客户不存在时创建潜在客户，请将`createOrUpdate`指定为操作。 我们使用商机的电子邮件作为主要标识符，以在Marketo中查找商机记录。 最后，我们使用键`leadScore`指定商机得分的值。 使用此方法一次可以更新300个潜在客户。

由&#x200B;_Murta_&#x200B;发布于&#x200B;_2015-02-19_

## 突出显示基于Source平台构建的打开Marketo项目：第三部分

这是正在进行的系列文章中的第三个帖子，其中强调了开发人员社区围绕Marketo平台构建的开源项目。 我们在Marketo的GitHub帐户[上维护有](https://github.com/Marketo/Community-Supported-Client-Libraries)列表，我们将在其中跟踪Marketo开发人员社区创建的客户端库和项目。 以下是围绕Marketo REST API开发的三个项目。 **[Usermind](http://www.usermind.com/)已为Marketo REST API创建[Node.js客户端库](https://github.com/MadKudu/node-marketo)。** **[Arunim Samat](https://github.com/asamat)在Python中为Marketo REST API创建了[客户端库](https://github.com/asamat/python_marketo)。** **[Marketo的Jacques Lemieux](https://www.linkedin.com/in/jalemieux)在Ruby中为Marketo REST API创建了[客户端库。](https://github.com/jalemieux/mkto_rest)**&#x200B;我们很高兴能够在Marketo平台上看到开发人员社区创建的更多项目。 如果您正在处理Marketo平台的开源项目，请[通过拉取请求将其提交到此GitHub存储库](https://github.com/Marketo/Community-Supported-Client-Libraries)。

由&#x200B;_Murta_&#x200B;发布于&#x200B;_2015-02-20_

## 将Marketo表单插入RTP营销活动

许多营销人员都有兴趣将Marketo表单放入Marketo实时Personalization (RTP)营销活动中。 无论是对话框、区域还是小组件RTP促销活动类型，您都可以复制表单HTML代码并将其粘贴到RTP的促销活动编辑器中。 我看到以下用法示例： — 第2次或第3次单击您的网站后，让访客注册您的新闻稿 — 快速、有效的网络研讨会注册表单 — 下载封闭式案例研究 — 提供过去已取消订阅的潜在客户重新订阅在营销活动中填写表单并接收感谢或请求的内容，从而减少一次单击即可实现您的目标。 因此，下面简单介绍了如何这样做并将Marketo Form 2.0嵌入到Marketo RTP促销活动中。 查看下面的eMarketer的良好示例，RTP用户更进一步地执行了这一操作，没有将访客定向到感谢页面，而是决定在RTP促销活动中显示“感谢”消息。 此选项的代码也如下所示。 祝您使用体验愉快，我很高兴听到您使用它的体验！

1. 右键单击已批准的表单。 选择&#x200B;**嵌入代码。**
1. 复制&#x200B;**代码。**
1. 在Marketo RTP中，转到&#x200B;**促销活动**。
1. 单击&#x200B;**新建营销活动**。
1. 在富文本编辑器中，单击&#x200B;**HTML图标**。
1. 将表单嵌入代码粘贴到HTML Source编辑器中。 单击&#x200B;**更新**。
1. 该表单将不会显示在编辑器视图中，但您可以预览它以查看它在营销活动中的呈现方式。
1. 单击&#x200B;**启动**&#x200B;以启动营销活动。

### 注释

对表单的任何更改都必须在编辑表单草稿的Marketo营销活动中完成。

### 相关文章

* [Forms 2.0](/help/javascript-api/forms-api-reference.md)

由&#x200B;_Yanir_&#x200B;发布于&#x200B;_2015-12-20_

## 在Marketo表单中添加重置按钮

```javascript
<script src="//app-sj01.marketo.com/js/forms2/js/forms2.min.js"></script>
<form id="mktoForm_116"></form>
<script>MktoForms2.loadForm("//app-sj01.marketo.com", "410-XOR-673", 116,
function(form) { form.getFormElem()[0].querySelector('button[type="submit"]').insertAdjacentHTML('afterend','<button type="reset" class="mktoButton">Reset</button>') });
</script>
```

由&#x200B;_Murta_&#x200B;发布于&#x200B;_2015-03-18_

## 2015年3月版更新

[Marketo REST Asset API已在2015年3月版本](https://developer.adobe.com/marketo-apis/api/asset/)中发布。 此API允许访问Marketo的文件、文件夹、令牌、电子邮件和电子邮件模板对象。 请注意，为提供对资源API端点的访问，添加了两个角色权限：只读Assets、读写Assets。 如果您的API用户角色早于Asset API的发布，则需要创建具有这些权限的新API用户角色来启用访问。 否则，您会收到603“访问被拒绝”错误响应。 除了REST资产API的发布，还对现有REST API端点进行了更新。 [合并潜在客户REST API终结点](https://developer.adobe.com/marketo-apis/api/mapi/#operation/mergeLeadsUsingPOST)已更新，以允许合并多个潜在客户。 [计划Campaign REST API终结点](https://developer.adobe.com/marketo-apis/api/mapi/#operation/scheduleCampaignUsingPOST)已更新，允许在计划活动时克隆活动。

由&#x200B;_Murta_&#x200B;发布于&#x200B;_2015-03-23_

## 延迟触发RTP营销活动

此自定义JavaScript允许RTP用户在网页加载几秒钟后显示促销活动。 建议将此用于对话框和小组件营销活动。 它可用于在访客查看页面上的常规内容后，在延迟后显示促销活动。 建议仅在应显示促销活动的特定页面上实施此代码。 不建议在所有页面中使用此代码，因为它可能会影响性能。 **设置说明**&#x200B;自定义代码发送RTP自定义数据事件（t=timeOnPage，即：t=60），然后加载与此事件匹配的营销活动。 默认情况下，此事件将在60秒后触发。 您可以通过将sendCustomRTPEvent参数更改为任何其他数字来自定义它。 将代码紧跟在标准RTP代码之后：

```javascript
<script>
function sendCustomRTPEvent(a){
 var eventValue="t="+a;
 setTimeout(function(){
  rtp('send', 'event', {value: eventValue});
  rtp('get', 'campaign',true);
 }, 1000 \* a);
}
sendCustomRTPEvent(60); //Seconds
</script>
```

要设置RTP营销活动以便在出现时间延迟后作出反应，请执行以下操作：1. 登录到您的RTP帐户1。 创建新区段1. 在“区段事件”部分中，添加： `t=60`。 访客在每个会话中只能与每个RTP区段匹配一次，因此每个营销活动只能查看一次（除非将其设置为sticky）。

由&#x200B;_Yanir_&#x200B;发布于&#x200B;_2015-03-24_

## 2015年4月发行版更新

### Marketo Mobile Engagement SDK v0.3.2

Marketo现在包括移动应用程序的营销自动化和用户参与。 将[Marketo Mobile SDK](/help/mobile/mobile.md)安装到您的iOS或Android应用程序后，营销人员可以侦听应用程序内事件并发送相关推送通知。

### REST API增强功能

* 自定义对象

引入新的[自定义对象端点](/help/rest-api/custom-objects.md)，允许您以编程方式列出、描述和CRUD驻留在Marketo自定义对象中的数据。

请注意，添加了角色权限，以提供对自定义对象API端点的访问：只读自定义对象、读写自定义对象。 如果您的API用户角色早于自定义对象API的发布，则您需要创建具有这些权限的新API用户角色以启用访问。 否则，您会收到603“访问被拒绝”错误响应。

* 计划Campaign — 克隆项目

[计划营销活动API](/help/rest-api/data-ingestion.md)中引入了新的可选参数“cloneToProgramName”。 当此参数存在时，将克隆营销活动的父项目并计划新创建的营销活动。 参数为生成的程序指定所需的名称。

由&#x200B;_Travis Kaufman_&#x200B;发布于&#x200B;_2015-04-28_

## 跨实例同步电子邮件取消订阅

您是否管理多个Marketo实例？ 跨实例保持潜在客户信息同步可能具有挑战性。 下面是使用调用外部Web服务的webhook在实例之间同步电子邮件取消订阅的一种方法。 外部Web服务循环遍历每个实例，查找触发取消订阅事件的已知潜在客户。 当找到匹配的潜在客户时，相应潜在客户记录中的“已取消订阅”字段将更新。 下图说明了这个想法。  具体取决于您能否实施Web服务，但下面的代码应该帮助您快速启动流程！

### 外部Web服务

对于需要同步的每个Marketo实例，外部Web服务会执行以下操作：

1. 撰写实例特定的REST API [端点URL](/help/rest-api/endpoint-reference.md)
1. 使用[标识](/help/rest-api/authentication.md)获取访问令牌
1. 使用[按筛选器类型获取多个潜在客户](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getLeadsByFilterUsingGET)获取与电子邮件地址匹配的潜在客户记录列表
1. 使用创建/更新潜在客户更新每个潜在客户记录的“已取消订阅”字段

下图详细显示了外部Web服务调用和Marketo REST API调用。  下面的示例代码不是开箱即用的Web服务。 相反，它是一个控制台模式程序，您可以通过命令行将参数传递到。 此处的目的是说明如何调用适当的Marketo API来跨实例更新潜在客户记录。 实施Web服务是读者的一项练习。
**示例代码**&#x200B;若要启动并运行示例代码，您需要在常用的IDE中创建Java项目。 之后，您需要进行以下更改：1. 示例代码使用[json-simple](https://code.google.com/archive/p/json-simple)来解析JSON字符串。 将json-simple jar添加到您的Java项目中。 1.示例代码的结构包含每个Marketo实例的元数据。 将实例中的实际值放入结构中，如下所示：

```java
public static String instanceInfo[][] = {
{ "AccountId1", "ClientId1","ClientSecret1" },    // Instance 1 metadata
{ "AccountId2", "ClientId2","ClientSecret2" },    // Instance 2 metadata
{ "AccountId3", "ClientId3","ClientSecret3" }     // Instance 3 metadata
};
```

您可以在Marketo管理面板中找到实例的元数据：

* 帐户Id管理员>集成> Munchkin > Munchkin帐户
* 客户端Id和客户端密钥管理员>集成> LaunchPoint >电子邮件取消订阅同步>查看详细信息

示例代码采用两个命令行参数，这些参数模拟上述外部Web服务的“id”和“email”查询参数。

1. 参数[0] =帐户ID
1. 参数[1] =电子邮件地址

将实际值作为参数从实例传递到程序。 以下是Intellij IDEA的项目配置屏幕截图。

**SyncEmailUnsubscribe.java**

```java
package com.marketo;

import org.json.simple.JSONArray;
import org.json.simple.JSONObject;
import org.json.simple.parser.JSONParser;
import org.json.simple.parser.ParseException;

import javax.net.ssl.HttpsURLConnection;
import java.io.IOException;
import java.io.InputStream;
import java.io.OutputStream;
import java.net.MalformedURLException;
import java.net.URL;
import java.util.Iterator;
import java.util.NoSuchElementException;
import java.util.Scanner;

public class SyncEmailUnsubscribe {
    // Define Marketo instance meta data here.
    // Each row contains three elements: Account Id, Client Id, Client Secret.
    // For example:
    //  public static String instanceData[][] = {
    //    {"111-AAA-222", "2f4a4435-f6fa-4bd9-3248-098754982345", "asdf6IVE9h4Jjcl59cOMAKFSk78ut12W"},
    //    {"222-BBB-333", "5f4a6657-f6fa-4cd9-4356-123083238821", "gfjgfIVE9h4Jjcl59cOMAKFSk78ut12W"},
    //    {"444-CCC-444", "9f4a4678-f6fa-4dd9-7735-908713247721", "xzcxvbVE9h4Jjcl59cOMAKFSk78ut12W"}
    //  };
    //
    public static String instanceData[][] = {
            // ADD YOUR INSTANCE META DATA HERE
    };

    public static void main(String[] args) {
        String accountId = args[0];     // Account id that processed the unsubscribe
        String emailAddress = args[1];  // Email address of lead that unsubscribed

        SyncEmailUnsubscribe seu = new SyncEmailUnsubscribe();

        // Loop through each Marketo instance
        for (int i = 0; i < instanceData.length; i++) {

            // Make sure we skip instance that triggered the webhook
            if (!accountId.equals(instanceData[i][0])) {
                String endpointUrl = String.format("https://%s.mktorest.com", instanceData[i][0]);

                // Generate access token
                String identityUrl = String.format("%s/identity/oauth/token?grant_type=client_credentials&client_id=%s&client_secret=%s", endpointUrl, instanceData[i][1], instanceData[i][2]);
                String token = seu.getToken(identityUrl);

                // Get lead records for given email address (may be duplicates)
                String getLeadsUrl = String.format("%s/rest/v1/leads.json?access_token=%s&filterType=email&filterValues=%s", endpointUrl, token, emailAddress);
                String leads = seu.getLeads(getLeadsUrl);

                // Update unsubscribed field in lead record
                String updateLeadsUrl = String.format("%s/rest/v1/leads.json?access_token=%s", endpointUrl, token);
                seu.updateLeads(updateLeadsUrl, leads, accountId);
            }
        }

        System.exit(0);
    }

    // Call Identity Service to generate access token
    public String getToken(String url) {
        // Call Identity Service
        String tokenData = getData(url);

        // Convert response into JSONObject
        JSONParser parser = new JSONParser();
        Object obj = null;
        try {
            obj = parser.parse(tokenData);
        } catch (ParseException pe) {
            System.out.println("position: " + pe.getPosition());
            System.out.println(pe);
        }

        // Retrieve access_token
        JSONObject jsonObject = (JSONObject)obj;
        return jsonObject.get("access_token").toString();
    }

    // Call Get Multiple Leads by Filter Type Service to get lead records
    public String getLeads(String url) {
        return getData(url);
    }

    // Call Create/Update Lead Service to update "unsubscribed" flag in lead record
    public void updateLeads(String url, String leads, String account) {
        JSONObject body = composeBody(leads, account);
        if (body != null) {
            postData(url, body);
        }
    }

    // Compose JSON body for Create/Update Leads Service
    private JSONObject composeBody(String leads, String account) {
        JSONObject body = new JSONObject();

        // Convert leads into JSONObject
        JSONParser parser = new JSONParser();
        Object obj = null;
        try {
            obj = parser.parse(leads);
        } catch (ParseException pe) {
            System.out.println("position: " + pe.getPosition());
            System.out.println(pe);
        }
        JSONObject leadsObj = (JSONObject)obj;

        Object success = leadsObj.get("success");
        if (success.equals(true)) {
            body.put("action", "updateOnly");
            body.put("lookupField", "id");
            body.put("asyncProcessing", "true");

            // Build array of lead objects
            JSONArray input = new JSONArray();
            JSONArray result = (JSONArray) leadsObj.get("result");
            Iterator<JSONObject> iterator = result.iterator();
            while (iterator.hasNext()) {
                JSONObject leadIn = (JSONObject)iterator.next();
                JSONObject lead = new JSONObject();
                lead.put("id", leadIn.get("id"));
                lead.put("unsubscribed", "true");
                lead.put("unsubscribedReason", "Cross instance synch triggered by webhook from: " + account);
                input.add(lead);
            }

            body.put("input", input);
        }
        return body;
    }

    // HTTP POST request
    private String postData(String endpoint, JSONObject body) {
        String data = "";
        try {
            // Make request
            URL url = new URL(endpoint);
            HttpsURLConnection urlConn = (HttpsURLConnection) url.openConnection();
            urlConn.setRequestMethod("POST");
            urlConn.setAllowUserInteraction(false);
            urlConn.setDoOutput(true);
            urlConn.setRequestProperty("Content-type", "application/json");
            urlConn.setRequestProperty("accept", "application/json");
            urlConn.connect();
            OutputStream os = urlConn.getOutputStream();
            os.write(body.toJSONString().getBytes());
            os.close();
            int responseCode = urlConn.getResponseCode();
            if (responseCode == 200) {
                System.out.println("Status: 200");
                InputStream inStream = urlConn.getInputStream();
                data = convertStreamToString(inStream);
                System.out.println(data);
            } else {
                System.out.println(responseCode);
                data = "Status:" + responseCode;
            }
        } catch (MalformedURLException e) {
            System.out.println("URL not valid.");
        } catch (IOException e) {
            System.out.println("IOException: " + e.getMessage());
            e.printStackTrace();
        }
        return data;
    }

    // HTTP GET request
    private String getData(String endpoint) {
        String data = "";
        try {
            URL url = new URL(endpoint);
            HttpsURLConnection urlConn = (HttpsURLConnection) url.openConnection();
            urlConn.setRequestMethod("GET");
            urlConn.setAllowUserInteraction(false);
            urlConn.setDoOutput(true);
            int responseCode = urlConn.getResponseCode();
            if (responseCode == 200) {
                System.out.println("Status: 200");
                InputStream inStream = urlConn.getInputStream();
                data = convertStreamToString(inStream);
                System.out.println(data);
            } else {
                System.out.println(responseCode);
                data = "Status:" + responseCode;
            }
        } catch (MalformedURLException e) {
            System.out.println("URL not valid.");
        } catch (IOException e) {
            System.out.println("IOException: " + e.getMessage());
            e.printStackTrace();
        }
        return data;
    }

    private String convertStreamToString(InputStream inputStream) {
        try {
            return new Scanner(inputStream).useDelimiter("A").next();
        } catch (NoSuchElementException e) {
            return "";
        }
    }
}
```

### Marketo设置

对要同步的每个Marketo实例执行以下步骤。

1. 创建具有角色权限的自定义服务：读写潜在客户。 如果您不熟悉如何创建自定义服务，请单击[此处](/help/rest-api/custom-services.md)。
1. 创建调用外部Web服务的Webhook。 如果您不熟悉如何创建Webhook，请单击[此处](/help/webhooks/webhooks.md)。
1. 将Webhook添加为Smart Campaign中的流程步骤。

以下屏幕截图显示了如何使用令牌自动填充查询参数，创建webhook以调用上面指定的服务。 现在，我们已经创建了webhook，接下来可以将其作为流量操作添加到Smart Campaign中。  智能列表应包含“从电子邮件取消订阅”触发器。

### 验证

要全面测试这一点，请在多个Marketo实例中使用相同的电子邮件地址创建一个潜在客户。 确保您拥有电子邮件地址！ 在触发发送电子邮件流程操作的一个实例中，打开生成的电子邮件，然后单击取消订阅。 要验证结果，请登录到其他每个实例，并检查与电子邮件地址关联的潜在客户记录。 应选中“已取消订阅”复选框，“已取消订阅原因”字段应包含具有启动同步的源帐户ID的注释。

由&#x200B;_David_&#x200B;发布于&#x200B;_2015-05-11_

## 使用REST API同步潜在客户数据更改

该帖子提供了一个代码示例，可以定期运行该代码示例来轮询Marketo以获取更新。 其思想是使用Marketo API识别商机数据的更改，并提取已更改的商机数据。 然后，可以将该数据推送到外部系统以进行同步。 提供的代码示例使用了我们的SOAP API。 我们有[新方式](https://www.youtube.com/watch?v=G-7ZJjLy5D8&feature=youtu.be)，这种方式使用[Marketo REST API](/help/rest-api/rest-api.md)。 这篇帖子向您展示如何使用两个REST端点实现同一个目标：[获取潜在客户更改](/help/rest-api/rest-api.md)，[按ID获取潜在客户](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getLeadByIdUsingGET)。 该程序包含两个主要步骤：

1. 调用Get Lead Changes ，以生成一个包含特定商机字段已更改或在给定时间段内添加的所有商机ID的列表。
1. 为列表中的每个Lead Id调用Get Lead Id ，以从Lead记录中检索字段数据。

我们将采用步骤2中检索到的数据，并将其格式化以供外部系统使用。  **程序输入**&#x200B;默认情况下，程序将从当前日期起“回溯”一天以查找更改。 例如，您可以每天在同一时间运行该程序。 要更早地返回到过去，可以将天数指定为命令行参数，从而有效地增加时间窗口。 该程序包含多个可修改的变量： CUSTOM_SERVICE_DATA — 这包含您的Marketo [自定义服务](/help/rest-api/custom-services.md)数据（帐户ID、客户端ID、客户端密钥）。 LEAD_CHANGE_FIELD_FILTER — 其中包含我们将检查其更改的销售线索字段的逗号分隔列表。 READ_BATCH_SIZE — 这是一次要检索的记录数。 使用此选项可针对主体大小调整响应。 **程序输出**&#x200B;程序将收集所有更改的潜在客户记录并在JSON中将其格式化为潜在客户对象的数组，如下所示：

```json
{
    "result": [
        {
            "leadId": "318592",
            "updatedAt": "2015-07-22T19:19:07Z",
            "firstName": "David",
            "lastName": "Everly",
            "email": "<deverly@marketo.com>"
        },

        ...more lead objects here...
    ]
}
```

其想法是，您随后可以将此JSON作为请求有效负载传递到外部Web服务以同步数据。 **程序逻辑**&#x200B;我们首先建立时间窗口，编写REST端点URL，并获取身份验证访问令牌。 接下来，我们启动一个“获取分页令牌/获取潜在客户变更”循环，该循环将一直运行，直到我们耗尽潜在客户变更的供应。 此循环的目的是累计唯一销售线索ID的列表，以便我们以后可以在程序中将其传递给通过ID获取销售线索。 对于此示例，我们告知Get Lead Changes查找对以下字段的更改：firstName、lastName、email。 您可以根据自己的需要随意选择任意字段组合。 Get Lead Changes返回包含活动类型ID的“result”对象，我们可以使用它来筛选结果。 注意：通过调用[获取活动类型](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getAllActivityTypesUsingGET) REST终结点，可以获得活动类型列表。 我们对返回的2种活动类型感兴趣：1. 新潜在客户(12)

```json
{
    "id": 12024682,
    "leadId": 318581,
    "activityDate": "2015-03-17T00:18:41Z",
    "activityTypeId": 12,
    "primaryAttributeValueId": 318581,
    "primaryAttributeValue": "David Everly",
    "attributes": [
        {
            "name": "Created Date",
            "value": "2015-03-16"
        },
        {
            "name": "Source Type",
            "value": "New lead"
        }
    ]
}
```

1. 更改数据值(13)通过检查更改数据值响应中的“name”属性，我们可以分辨哪个字段发生了更改。

```json
{
    "id": 12024689,
    "leadId": 318581,
    "activityDate": "2015-03-17T22:58:18Z",
    "activityTypeId": 13,
    "fields": [
        {
            "id": 31,
            "name": "lastName",
            "newValue": "Evely",
            "oldValue": "Everly"
        }
    ],
    "attributes": [
        {
            "name": "Source",
            "value": "Web form fillout"
        }
    ]
}
```

当返回这两种活动中的任何一种时，我们会在列表中存储关联的Lead Id。 获得列表后，我们可以对其进行迭代，为每个项目调用按ID获取潜在客户。 这将检索列表中每个潜在客户的最新潜在客户数据。 对于此示例，我们检索以下潜在客户字段： `leadId`、`updatedAt`、`firstName`、`lastName`和`email`。 您可以根据自己的需要随意选择任意字段组合。 这是通过指定Get Lead by Id的字段参数来完成的。 最后，我们将结果作为Lead对象的数组进行JSONify，如上所述。

**程序代码**

```java
package com.marketo;

// minimal-json library (<https://github.com/ralfstx/minimal-json>)
import com.eclipsesource.json.JsonArray;
import com.eclipsesource.json.JsonObject;
import com.eclipsesource.json.JsonValue;

import java.io.\*;
import java.net.MalformedURLException;
import java.net.URL;
import java.text.DateFormat;
import java.text.ParseException;
import java.text.SimpleDateFormat;
import java.util.\*;

import javax.net.ssl.HttpsURLConnection;

public class LeadChanges {
    //
    // Define Marketo REST API access credentials: Account Id, Client Id, Client Secret.  For example:
    //    public static String CUSTOM_SERVICE_DATA[] =
    //      {"111-AAA-222", "2f4a4435-f6fa-4bd9-3248-098754982345", "asdf6IVE9h4Jjcl59cOMAKFSk78ut12W"};
    //
    private static final String CUSTOM_SERVICE_DATA[] =
            {INSERT YOUR CUSTOM SERVICE DATA HERE};

    // Lead fields that we are interested in
    private static final String LEAD_CHANGE_FIELD_FILTER = "firstName,lastName,email";

    // Number of lead records to read at a time
    private static final String READ_BATCH_SIZE = "200";

    // Activity type ids that we are interested in
    private static final int ACTIVITY_TYPE_ID_NEW_LEAD = 12;
    private static final int ACTIVITY_TYPE_ID_CHANGE_DATA_VALE = 13;

    public static void main(String[] args) {
        // Command line argument to set how far back to look for lead changes (number of days)
        int lookBackNumDays = 1;
        if (args.length == 1) {
            lookBackNumDays = Integer.parseInt(args[0]);
        }

        // Establish "since date" using current timestamp minus some number of days (default is 1 day)
        Calendar cal = Calendar.getInstance();
        cal.add(Calendar.DAY_OF_MONTH, -lookBackNumDays);
        SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd'T'HH:mm:ssZ");
        String sinceDateTime = sdf.format(cal.getTime());

        // Compose base URL
        String baseUrl = String.format("https://%s.mktorest.com",
                CUSTOM_SERVICE_DATA[0]);

        // Compose Identity URL
        String identityUrl = String.format("%s/identity/oauth/token?grant_type=%s&client_id=%s&client_secret=%s",
                baseUrl, "client_credentials", CUSTOM_SERVICE_DATA[1], CUSTOM_SERVICE_DATA[2]);

        // Call Identity API
        JsonObject identityObj = JsonObject.readFrom(getData(identityUrl));
        String accessToken = identityObj.get("access_token").asString();

        // Compose URLs for Get Lead Changes, and Get Paging Token
        String leadChangesUrl = String.format("%s/rest/v1/activities/leadchanges.json?access_token=%s&fields=%s&batchSize=%s",
                baseUrl, accessToken, LEAD_CHANGE_FIELD_FILTER, READ_BATCH_SIZE);
        String pagingTokenUrl = String.format("%s/rest/v1/activities/pagingtoken.json?access_token=%s&sinceDatetime=%s",
                baseUrl, accessToken, sinceDateTime);

        HashSet leadIdList = new HashSet();

        // Call Get Paging Token API
        JsonObject pagingTokenObj = JsonObject.readFrom(getData(pagingTokenUrl));
        if (pagingTokenObj.get("success").asBoolean()) {

            String nextPageToken = pagingTokenObj.get("nextPageToken").asString();
            boolean moreResult;

            do {
                moreResult = false;

                // Call Get Lead Changes API
                JsonObject leadChangesObj = JsonObject.readFrom(getData(String.format("%s&nextPageToken=%s",
                        leadChangesUrl, nextPageToken)));
                if (leadChangesObj.get("success").asBoolean()) {
                    moreResult = leadChangesObj.get("moreResult").asBoolean();
                    nextPageToken = leadChangesObj.get("nextPageToken").asString();

                    if (leadChangesObj.get("result") != null) {
                        JsonArray resultAry = leadChangesObj.get("result").asArray();
                        for (JsonValue resultObj : resultAry) {
                            int activityTypeId = resultObj.asObject().get("activityTypeId").asInt();


                            // Store lead ids for later use
                            boolean storeThisId = false;
                            if (activityTypeId == ACTIVITY_TYPE_ID_NEW_LEAD) {
                                storeThisId = true;
                            } else if (activityTypeId == ACTIVITY_TYPE_ID_CHANGE_DATA_VALE) {
                                // See if any of the changed fields are of interest to us
                                JsonArray fieldsAry = resultObj.asObject().get("fields").asArray();
                                for (JsonValue fieldsObj : fieldsAry) {
                                    String name = fieldsObj.asObject().get("name").asString();
                                    if (LEAD_CHANGE_FIELD_FILTER.contains(name)) {
                                        storeThisId = true;
                                    }
                                }

                            }

                            if (storeThisId) {
                                leadIdList.add(resultObj.asObject().get("leadId").toString());
                            }
                        }
                    }
                }

            } while (moreResult);
        }

        JsonObject result = new JsonObject();
        JsonArray leads = new JsonArray();

        for (Object o : leadIdList) {
            String leadId = o.toString();

            // Compose Get Lead by Id URL
            String getLeadUrl = String.format("%s/rest/v1/lead/%s.json?access_token=%s",
                    baseUrl, leadId, accessToken);

            // Call Get Lead by Id API
            JsonObject leadObj = JsonObject.readFrom(getData(getLeadUrl));
            if (leadObj.get("success").asBoolean()) {
                if (leadObj.get("result") != null) {
                    JsonArray resultAry = leadObj.get("result").asArray();
                    for (JsonValue resultObj : resultAry) {

                        // Create lead object
                        JsonObject lead = new JsonObject();
                        lead.add("leadId", leadId);
                        lead.add("updatedAt", resultObj.asObject().get("updatedAt").asString());
                        lead.add("firstName", resultObj.asObject().get("firstName").asString());
                        lead.add("lastName", resultObj.asObject().get("lastName").asString());
                        lead.add("email", resultObj.asObject().get("email").asString());

                        // Add lead object to leads array
                        leads.add(lead);
                    }
                }
            }
        }

        // Add leads array to result object
        result.add("result", leads);

        // Print out result object
        System.out.println(result);

        System.exit(0);
    }

    // Perform HTTP GET request
    private static String getData(String endpoint) {
        String data = "";
        try {
            URL url = new URL(endpoint);
            HttpsURLConnection urlConn = (HttpsURLConnection) url.openConnection();
            urlConn.setRequestMethod("GET");
            urlConn.setAllowUserInteraction(false);
            urlConn.setDoOutput(true);
            int responseCode = urlConn.getResponseCode();
            if (responseCode == 200) {
                InputStream inStream = urlConn.getInputStream();
                data = convertStreamToString(inStream);
            } else {
                System.out.println(responseCode);
                data = "Status:" + responseCode;
            }
        } catch (MalformedURLException e) {
            System.out.println("URL not valid.");
        } catch (IOException e) {
            System.out.println("IOException: " + e.getMessage());
            e.printStackTrace();
        }
        return data;
    }

    private static String convertStreamToString(InputStream inputStream) {
        try {
            return new Scanner(inputStream).useDelimiter("A").next();
        } catch (NoSuchElementException e) {
            return "";
        }
    }
}
```

你看，[生活是一首快乐的歌](https://www.youtube.com/watch?v=zFaBwZDywLk)。 祝您使用愉快！

由&#x200B;_David_&#x200B;发布于&#x200B;_2015-07-31_

## 2015年5月版更新

### REST API

* 机会API。 引入了新的[机会端点](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Opportunities)，允许您以编程方式列出、描述和CRUD Marketo机会对象中的数据。

注意：添加了Role权限以提供对Opportunity端点的访问：只读Opportunity、读写Opportunity。 如果您的API用户角色早于Opportunity API的发布，则您需要创建一个具有这些权限的新API用户角色来启用访问。 否则，您会收到603“访问被拒绝”错误响应。

* 资产API — 代码片段。 已引入代码片段的新[资源端点](https://developer.adobe.com/marketo-apis/api/asset/#snippet_endpoints)，以允许您以编程方式处理代码片段对象。 片段可用作电子邮件和登陆页面中的动态内容块。
* 潜在客户API — 更新潜在客户分区。 已添加分区[的新](https://developer.adobe.com/marketo-apis/api/mapi/#operation/updatePartitionsUsingPOST)潜在客户终结点，允许您更新一个或多个潜在客户的分区。
* 修复了潜在客户相关API在“createdAt”和“updatedAt”属性中缺少时区偏移的问题。
* 修复了在超出每日最大调用数时，计划营销活动未返回正确错误代码的问题。
* 修复了按ID获取文件夹有时会为“parent”和“description”属性返回空值的问题。
* 修复了在某些情况下，按ID批准电子邮件会导致系统错误的问题。
* 修复了“按文件夹ID创建令牌”所生成的令牌在某些情况下不可用的问题。

### 实时Personalization (RTP)

* 富媒体推荐API。 新的[富媒体推荐](/help/javascript-api/web-personalization.md)功能已添加到RTP JavaScript API。 富媒体内容推荐让Web访客接触到由机器学习和预测分析提供支持的最相关的内容。 使用文本描述和图像增强内容资产，并在网站上嵌入多个内容推荐。

### Mobile Engagement SDK

iOS v0.3.4/Android v0.3.3

* 自定义操作。 添加了通过发送自定义操作来跟踪用户交互的功能。 有关详细信息，请参阅[此处](/help/mobile/mobile.md)的“发送自定义操作”。
* 已弃用trackPushNotification方法。

由&#x200B;_David_&#x200B;发布于&#x200B;_2015-05-26_

## 2015年6月版更新

### REST API

* 公司API

引入新的[公司端点](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Companies)，允许您以编程方式列出、描述和CRUD驻留在Marketo公司对象中的数据。

注意：添加了角色权限以提供对“程序”端点的访问：只读公司、读写公司。 如果您的API用户角色在公司API发布之前，则需要使用这些权限更新您的API用户角色才能启用访问。 否则，您会收到603“访问被拒绝”错误响应

* 资产API — 程序

更新了资产API以支持应用程序文件夹和程序文件夹。 多个资产API已接受请求中整数形式的文件夹ID。 文件夹ID可以作为URI的一部分或请求正文的一部分进行传递，具体取决于API。

现在，必须在文件夹ID旁边指定文件夹类型。 文件夹类型为“Program”或“Folder”。 “文件夹”指定应用程序文件夹，而“程序”指定程序文件夹。 根据您调用的API，可以通过两种不同的方式指定文件夹类型：

1. 使用“FolderIdType”数据结构。 FolderIdType是一个简单的JSON块，包含id和类型对，如下所示：

`{ "id" : **id**, "type" : "**type**" }`

其中，**id**&#x200B;是文件夹ID，“**type**”是文件夹类型。 “**类型**”的允许值为“文件夹”或“程序”。

示例 — 创建文件夹

`POST /asset/v1/folders.json`

`parent={"id":416,"type":"Folder"}&name=Test Folder&description=This is a test folder`

1. 使用现有id参数，并使用“type”查询参数指定其类型。

示例 — 按Id获取文件夹

`GET /rest/asset/v1/folder/1016.json?type=Program`

现在，在Result对象中包含文件夹ID的所有API响应也将包含其值为FolderIdType的folderId属性。 这可用于确定给定文件夹ID的文件夹类型。

示例 — 按名称获取文件夹

`GET /rest/asset/v1/folder/byName.json?name=Social Media`

```json
"result": [
{
...

"folderId": {"id":341, "type": "Program"},
...

"id":"341"
}
]
```

要确定给定ID的文件夹类型，您可以使用[浏览文件夹](https://developer.adobe.com/marketo-apis/api/asset/browse-folders/) API。

API响应中的“type”属性已重命名为“folderType”。 这是为了避免与FolderIdType中包含的“type”元素混淆。

例如，从以下内容：

“**类型**”：“营销文件夹”

更改为以下内容：

“**folderType**”：“营销文件夹”

### Mobile Engagement SDK

iOS 0.3.5

* 修复了在主线程上运行设置测试设备对话框的问题。 [MOB-638]
* 添加了无法注册测试设备时的错误处理。 [MOB-639]

Android 0.3.3

* 向AndroidManifest.xml :configChanges元素添加了android`<activity>`特性，以便在添加测试设备并更改方向时阻止取消进度对话框。 [MOB-687]

由&#x200B;_David_&#x200B;发布于&#x200B;_2015-06-30_

## 使用RTP API的应用程序内Web Personalization (Beta)

我们的若干客户为其用户提供Web应用程序解决方案，如果他们可以在安全的Web应用程序环境中使用Marketo Real-Time Personalization (RTP)，我们将会收到请求。 答案是肯定的！ 我们已发布用于应用程序内消息传送的API，因此您可以个性化内容并推广营销活动，例如网络研讨会、新功能发布、向上销售，并根据您的Web应用程序数据吸引用户。 例如，为以下内容个性化应用程序内内容：

* 基于用户活动的试用优惠
* 不同的订阅类型（追加销售、交叉销售或网络研讨会培训）
* 与用户活动相关的新增功能

**用例示例** Marketo的客户成功团队使用应用程序内Web Personalization与特定订阅类型（Spark、Standard、Select或Enterprise）进行通信并提供个性化内容，确保他们看到渐进式促销活动，并根据参与情况培养应用程序内用户。 让我们看看如何为企业订阅类型的用户执行此操作。 **必备项**&#x200B;了解[RTP用户上下文API](/help/javascript-api/web-personalization.md)。 **启用来自Marketo支持的用户上下文API**&#x200B;请求，以便为您的RTP帐户启用用户上下文API。 **设置自定义变量** RTP中有5个可用的自定义变量插槽可将数据发送至。 在本例中，我们将向“自定义变量1”发送用户订阅类型Enterprise 。

`rtp('set', 'customVar1', 'Enterprise');`

**创建新的RTP区段**&#x200B;转到&#x200B;**区段**，单击&#x200B;**新建**。

1. 将&#x200B;**用户上下文API**&#x200B;筛选器拖到区段生成器中。
1. 选择&#x200B;**值**&#x200B;为&#x200B;**Enterprise**&#x200B;的&#x200B;**自定义变量1**（订阅类型）。

**根据上次访问历史记录显示促销活动**&#x200B;如果访客在上次访问中点击了促销活动，则向访客显示其他促销活动。

1. 在&#x200B;**用户上下文API**&#x200B;中，单击&#x200B;**(+)**&#x200B;以添加另一个用户上下文API属性
1. 添加&#x200B;**运算符（AND或OR）。**
1. 选择&#x200B;**营销活动 — 已单击。**&#x200B;将&#x200B;**营销活动ID**&#x200B;设置为营销活动的ID。 （请参阅下面的注释，了解如何查找促销活动ID。）
1. 单击&#x200B;**保存并定义营销活动**&#x200B;以创建营销活动创意。

总体而言，如果访客将自定义变量（订阅类型）关联为等于Enterprise，且访客在上次访问中单击了促销活动(ID：5390)，则此区段将匹配。 下一步是为此区段定义个性化促销活动。 下面的屏幕截图显示了一个RTP对话框营销活动（左下角），该活动显示在“我的Marketo”页面上，用于为Enterprise用户推广网络研讨会。  **注意：** **查找促销活动ID**&#x200B;转到&#x200B;**促销活动**，将鼠标悬停在&#x200B;**促销活动名称**上以查找促销活动ID。
由_David_&#x200B;发布于&#x200B;_2015-06-17_

## 使用Marketo REST API发送事务性电子邮件：第1部分

Marketo中有一些配置要求使用Marketo REST API执行所需的调用。

* 收件人必须在Marketo中拥有记录
* 需要在您的Marketo实例中创建并批准事务型电子邮件。
* 需要有一个活动触发器营销活动，其中请求了Campaign、Source： Web服务API，并设置了以发送电子邮件

首先[创建并批准您的电子邮件](https://experienceleague.adobe.com/zh-hans/docs/marketo/using/home)。 如果电子邮件确实是事务性的，您可能需要将其设置为可操作，但请确保它符合可操作的合法条件。 可使用电子邮件操作>电子邮件设置下的编辑屏幕对此进行配置。 批准它，我们就可以创建营销活动了。 如果您是创建营销活动的新手，请查看docs.marketo.com上的[新建Smart Campaign](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/core-marketo-concepts/smart-campaigns/creating-a-smart-campaign/create-a-new-smart-campaign)文章。 创建活动后，我们需要完成这些步骤。 使用Campaign is Requested触发器配置您的智能列表：现在，我们需要配置流以将“发送电子邮件”步骤指向我们的电子邮件。 在激活之前，您需要在“计划”选项卡中决定一些设置。 如果此特定电子邮件只应发送给给定记录一次，则保留资格设置。 但是，如果要求他们多次接收电子邮件，您需要将此调整为每次接收一次或某个可用频率。 现在我们可以激活了。

### 发送API调用

**注意：**&#x200B;在下面的Java示例中，我们使用minimal-json包处理代码中的JSON表示形式。 您可以在此处阅读有关此项目的更多信息：[https://github.com/ralfstx/minimal-json](https://github.com/ralfstx/minimal-json)通过API发送事务型电子邮件的第一部分是，确保您的Marketo实例中存在具有相应电子邮件地址的记录，并且我们有权访问其潜在客户ID。 出于本文的目的，我们将假设电子邮件地址已在Marketo中，并且我们只需要检索记录的ID。 为此，我们正在使用[通过过滤器类型获取多个潜在客户](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getLeadsByFilterUsingGET)调用，并且我们正在重用上篇帖子中的一些Java代码。 下面我们看一下用于请求营销活动的主要方法：

```java
package dev.marketo.blog_request_campaign;

import com.eclipsesource.json.JsonArray;

public class App
{
    public static void main( String[] args )
    {
     //Create an instance of Auth so that we can authenticate with our Marketo instance
        Leads leadsRequest = new Leads(auth).setFilterType("email").addFilterValue("<requestCampaign.test@marketo.com>");

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

为了从leadsRequest的JsonObject响应中获取这些结果，需要编写一些代码。 要检索Array中的第一个结果，我们需要从JsonObject中提取Array，并获取在0处索引的对象：

`JsonArray leadsResult = leadsRequest.getData().get("result").asArray();`
`int leadId = leadsResult.get(0).asObject().get("id").asInt();`

从现在开始，我们只需执行“请求营销活动”调用。 为此，需要参数：请求URL中的ID，以及包含一个成员“id”的JSON对象数组。 让我们看一下针对此的代码：

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
   String s = endpoint + "?access_token=" + auth.getToken(); //takes the endpoint URL and appends the access_token parameter to authenticate
   System.out.println("Executing RequestCampaign calln" + "Endpoint: " + s + "nRequest Body:n"  + requestBody);
   URL url = new URL(s);
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
   System.out.println("Result:n" + result);
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

此类有一个接受身份验证的构造函数，以及营销活动的ID。 通过将包含记录ID的ArrayList `<Integer>`传递给setLeads，或者使用addLead（它采用一个整数并将其附加到leads属性中的现有ArrayList），将潜在客户添加到对象中。 要触发API调用以将商机记录传递到营销活动，需要调用postData，以返回包含来自请求的响应数据的JsonObject。 调用请求营销活动时，Marketo中的Target触发器营销活动将处理传递给调用的每个商机，并向该商机发送之前创建的电子邮件。 恭喜，您已通过Marketo REST API触发电子邮件。 请留意第2部分，我们将了解如何通过Request Campaign动态自定义电子邮件的内容。

由&#x200B;_Kenny_&#x200B;发布于&#x200B;_2015-07-17_

## 使用REST API从Marketo中验证和检索潜在客户数据

**注意：**&#x200B;在下面的Java示例中，我们使用minimal-json包处理代码中的JSON表示形式。 您可以在此处阅读有关此项目的更多信息：[https://github.com/ralfstx/minimal-json](https://github.com/ralfstx/minimal-json)与Marketo集成时最常见的要求之一是检索潜在客户数据。 大多数集成（如果不是全部集成的话）都需要从Marketo订阅中检索或提交潜在客户数据，因此今天我们将看一个基本的潜在客户信息检索任务，方法是先对订阅进行[身份验证](/help/rest-api/authentication.md)，然后再从中检索潜在客户数据。 要检索我们的潜在客户，我们首先需要使用OAuth 2.0通过Target Marketo实例进行身份验证。我们需要通过Marketo进行身份验证的三项信息：客户端ID、客户端密钥和Marketo实例的主机。 以下是我们用于验证的类：

```java
package dev.marketo.blog_leads;
import java.io.IOException;
import java.io.InputStream;
import java.io.InputStreamReader;
import java.io.Reader;
import java.net.MalformedURLException;
import java.net.URL;
import javax.net.ssl.HttpsURLConnection;
import com.eclipsesource.json.JsonObject;

public class Auth {
 protected String marketoInstance; //Instance Host obtained from Admin -> Web Service
 private String clientId; //clientId obtained from Admin -> Launchpoint -> View Details for selected service
 private String clientSecret; //clientSecret obtained from Admin -> Launchpoint -> View Details for selected service
 private String idEndpoint; //idEndpoint constructed to authenticate with service when constructor is used
 private String token; //token is stored for reuse until expiration
 private Long expiry; //used to store time of expiration

 //Creates an instance of Auth which is used to Authenticate with a particular service on a particular instance
 public Auth(String id, String secret, String instanceUrl) {
  this.clientId = id;
  this.clientSecret = secret;
  this.marketoInstance = instanceUrl;
  this.idEndpoint = marketoInstance + "/identity/oauth/token?grant_type=client_credentials&client_id=" + clientId + "&client_secret=" + clientSecret;
 }
 //The only public method of Auth, used to check if the current value of Token is valid, and then to retrieve a new one if it is not
 public String getToken(){
  long now  = System.currentTimeMillis();
  if (expiry == null || expiry < now){
   System.out.println("Token is empty or expired. Trying new authentication");
   JsonObject jo = getData();
   System.out.println("Got Authentication Response: " + jo);
   this.token = jo.get("access_token").asString();
                        //expires_in is reported as seconds, set expiry to system time in ms + expires \* 1000
   this.expiry = System.currentTimeMillis() + (jo.get("expires_in").asLong() \* 1000);
  }
  return this.token;
 }
 //Executes the authentication request
 private JsonObject getData(){
  JsonObject jsonObject = null;
  try {
   URL url = new URL(idEndpoint);
   HttpsURLConnection urlConn = (HttpsURLConnection) url.openConnection();
   urlConn.setRequestMethod("GET");
            urlConn.setRequestProperty("accept", "application/json");
            System.out.println("Trying to authenticate with " + idEndpoint);
            int responseCode = urlConn.getResponseCode();
            if (responseCode == 200) {
                InputStream inStream = urlConn.getInputStream();
                Reader reader = new InputStreamReader(inStream);
                jsonObject = JsonObject.readFrom(reader);
            }else {
                throw new IOException("Status: " + responseCode);
            }
  } catch (MalformedURLException e) {
   e.printStackTrace();
  }catch (IOException e) {
            e.printStackTrace();
        }
  return jsonObject;
 }
}
```

此代码允许您从“管理员” — >“启动点”（ID和密码）和“管理员” — >“Web服务”（主机）中使用我们的客户端ID、客户端密钥和主机（作为marketoInstance）创建Auth实例。 它会公开getToken方法，该方法测试当前存储的令牌是否为null或已过期，然后返回现有令牌，或发出新的身份验证请求，然后从JSON响应的“access_token”成员返回新令牌。 现在您可以使用Marketo实例进行身份验证，下一步是检索我们的潜在客户。 我们正在使用此类：

```java
package dev.marketo.blog_leads;
import java.io.IOException;
import java.io.InputStream;
import java.io.InputStreamReader;
import java.io.Reader;
import java.net.MalformedURLException;
import java.net.URL;
import java.util.ArrayList;
import javax.net.ssl.HttpsURLConnection;
import com.eclipsesource.json.JsonObject;

public class Leads {
 private StringBuilder endpoint;
 private Auth auth;
 public String filterType;
 public ArrayList filterValues = new ArrayList();
 public Integer batchSize;
 public String nextPageToken;
 public ArrayList fields = new ArrayList();

 public Leads(Auth a) {
  this.auth = a;
  this.endpoint = new StringBuilder(this.auth.marketoInstance + "/rest/v1/leads.json");
 }
 public Leads setFilterType(String filterType) {
  this.filterType = filterType;
  return this;
 }
 public Leads setFilterValues(ArrayList filterValues) {
  this.filterValues = filterValues;
  return this;
 }
 public Leads addFilterValue(String filterVal) {
  this.filterValues.add(filterVal);
  return this;
 }
 public Leads setBatchSize(Integer batchSize) {
  this.batchSize = batchSize;
  return this;
 }
 public Leads setNextPageToken(String nextPageToken) {
  this.nextPageToken = nextPageToken;
  return this;
 }
 public Leads setFields(ArrayList fields) {
  this.fields = fields;
  return this;
 }

 public JsonObject getData() {
        JsonObject result = null;
        try {
         endpoint.append("?access_token=" + auth.getToken() + "&filterType=" + filterType + "&filterValues=" + csvString(filterValues));
         if (batchSize != null && batchSize > 0 && batchSize <= 300){
             endpoint.append("&batchSize=" + batchSize);
            }
            if (nextPageToken != null){
             endpoint.append("&nextPageToken=" + nextPageToken);
            }
            if (fields != null){
             endpoint.append("&fields=" + csvString(fields));
            }
            URL url = new URL(endpoint.toString());
            HttpsURLConnection urlConn = (HttpsURLConnection) url.openConnection();
            urlConn.setRequestMethod("GET");
            urlConn.setRequestProperty("accept", "text/json");
            InputStream inStream = urlConn.getInputStream();
            Reader reader = new InputStreamReader(inStream);
            result = JsonObject.readFrom(reader);
        } catch (MalformedURLException e) {
            System.out.println("URL not valid.");
        } catch (IOException e) {
            System.out.println("IOException: " + e.getMessage());
            e.printStackTrace();
        }
        return result;
    }
 private String csvString(ArrayList fields) {
  StringBuilder fieldCsv = new StringBuilder();
     for (int i = 0; i < fields.size(); i++){
      fieldCsv.append(fields.get(i));
      if (i + 1 != fields.size()){
       fieldCsv.append(",");
      }
     }
  return fieldCsv.toString();
 }
}
```

此类有一个构造函数，该构造函数接受一个Auth对象，然后为可选参数和必需参数公开多个setter。 在这种情况下，我们实际上只需要设置filterType和filterValues即可按电子邮件地址获取潜在客户。 因此，我们将对“email”使用setFilterType，并对需要检索ID的电子邮件地址使用addFilterValue。 设置参数后，您可以使用getData方法从潜在客户端点检索JsonObject，其中包含一个结果数组，该数组具有检索到的潜在客户记录的JSON表示形式。

### 整合在一起

现在，我们已经查看了使用的示例代码，让我们看一个简单的示例，以检索与测试电子邮件地址<testlead@marketo.com>匹配的潜在客户。 为此，我们需要将setFilterType用于“电子邮件”，并将addFilterValue用于我们需要检索其信息的电子邮件地址。 设置参数后，您可以使用getData方法从潜在客户端点检索JsonObject，其中包含一个结果数组，该数组具有检索到的潜在客户记录的JSON表示形式。

```java
package dev.marketo.blog_leads;

import com.eclipsesource.json.JsonObject;

public class App
{
    public static void main( String[] args )
    {
     //Create an instance of Auth so that we can authenticate with our Marketo instance
        //Change the credentials here to your own
        Auth auth = new Auth("CHANGE ME", "CHANGE ME", "CHANGE ME");
        //Create and parameterize an instance of Leads
        Leads getLeads = new Leads(auth)
              .setFilterType("email")
              .addFilterValue("<testlead@marketo.com>");
        //get the inner results array of the response
        JsonObject leadsResult = getLeads.getData();
        System.out.println(leadsResult);
    }
}
```

在此主要方法示例中，我们创建了一个Auth实例，然后将其传递到新的Leads构造函数。 使用setFilterType和addFilterValue，我们配置潜在客户实例以仅检索与电子邮件地址“<testlead@marketo.com>”匹配的潜在客户。 此示例将此内容输出到控制台：

令牌为空或已过期。 正在尝试新身份验证
正在尝试使用<https://299-BYM-827.mktorest.com/identity/oauth/token?grant_type=client_credentials&client_id=b417d98f-9289-47d1-a61f-db141bf0267f&client_secret=0DipOvz4h2wP1ANeVjlfwMvECJpo0ZYc>进行身份验证
获得身份验证响应： {&quot;access_token&quot;：&quot;ec0f02c0-28ac-4d6c-b7d7-00e47ae85ff1:st&quot;，&quot;token_type&quot;：&quot;bearer&quot;，&quot;expires_in&quot;:538，&quot;scope&quot;：&quot;<apiuser@mktosupport.com>&quot;}
{&quot;requestId&quot;：&quot;14fb6#14e6a7a9ad6&quot;，&quot;result&quot;：[{&quot;id&quot;:1026322，&quot;updatedAt&quot;：&quot;2015-07-07T21:43:25Z&quot;，&quot;lastName&quot;：&quot;Lead&quot;，&quot;email&quot;：&quot;<testlead@marketo.com>&quot;，&quot;createdAt&quot;：&quot;2015-07-07T21:43:25Z&quot;，&quot;firstName&quot;：&quot;Name&quot; test&quot;}，{&quot;id&quot;:1026323，&quot;updatedAt&quot;：&quot;2015-07-07T21:43:43Z&quot;，&quot;lastName&quot;：&quot;Lead2&quot;，&quot;email&quot;：&quot;<testlead@marketo.com>&quot;，&quot;createdAt&quot;：&quot;2015-07-07T21:43:43Z&quot;，&quot;firstName&quot;：&quot;Test&quot;}]，&quot;succcess&quot;:true}

现在我们有了主导数据，可以用任何我们需要的办法处理。 感谢阅读，请在评论中提供任何反馈。

由&#x200B;_Kenny_&#x200B;发布于&#x200B;_2015-07-10_

## 2015年7月发行版更新

REST API

* 销售人员API

引入新的[销售人员端点](/help/rest-api/sales-persons.md)，允许您以编程方式列出、描述和CRUD Marketo销售人员对象中的数据。 此外，还可以将销售人员分配给潜在客户、商机或公司。 这是通过在调用潜在客户、商机或公司的Create/Update/Upsert端点时指定“externalSalesPersonId”属性来完成的。

注：已添加角色权限，以提供对“项目群”端点的访问权限：只读销售人员、读写销售人员。 如果您的API用户角色在销售人员API发布之前，则需要使用这些权限更新您的API用户角色才能启用访问权限。 否则，您会收到603“访问被拒绝”错误响应。

* 资产API — 登陆页面模板

引入了新的[登陆页面模板端点](https://developer.adobe.com/marketo-apis/api/asset/#landing_page_templates_endpoints)，允许您以编程方式列出、创建和更新与登陆页面模板关联的数据。

* 资产API — 区段

已引入两个与区段相关的端点：

[获取区段](https://developer.adobe.com/marketo-apis/api/asset/get-segments)

[按Id获取分段](https://developer.adobe.com/marketo-apis/api/asset/get-segmentation-by-id)

* 修复了“按名称获取文件夹”不遵循“workSpace”参数的问题。 [LM-61059]
* 对自定义对象API进行了多项性能改进。

由&#x200B;_David_&#x200B;发布于&#x200B;_2015-07-17_

## 使用Marketo REST API创建并关联潜在客户、公司和机会

为了充分利用Marketo Analytics，在潜在客户、公司和机会记录之间建立正确且强大的关联至关重要。 如果您没有利用本机CRM同步，则构建这些关系可能会遇到一些困难，因此现在我们将逐步构建这些关系。

### 对象关系

在Marketo中，有几个重要关系可以完全建立机会报告：

* 潜在客户和机会与OpportunityRole对象之间存在多对多关系。
* OpportunityRole同时具有leadId和externalopportunityid字段以创建从商机到商机的关系。
* 要符合Has Opportunity智能列表过滤器的条件，潜在客户必须具有与机会相关的OpportunityRole。
* 机会通过externalCompanyId字段与公司对象具有多对一关系。
* 潜在客户通过externalCompanyId字段与公司保持一对多关系。
* 机会归因于基于潜在客户的客户获取计划或其成员资格的计划，以及该计划中的成功（请参阅[了解归因](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/reporting/revenue-cycle-analytics/revenue-tools/attribution/understanding-attribution)）。

通过在潜在客户数据库中建立这些关系，您将能够充分利用Marketo analytics，并了解项目对机会创建和获胜率的影响。

### 公司

建立这些关系的最简单方法是从创建公司开始。 这可确保我们可以在创建过程中将externalCompanyId传递给机会，而不必执行其他API调用以在机会创建后更新这些机会。 根据现有配置，这不一定是必要的步骤，但关联公司的全新潜在客户和联系人需要将这些记录添加到您的Marketo实例中，才能构建关系，因此我们来看看一些用于创建公司记录的代码。

```java
package dev.marketo.opportunities;

import java.io.IOException;
import java.io.InputStream;
import java.io.InputStreamReader;
import java.io.OutputStreamWriter;
import java.io.Reader;
import java.net.MalformedURLException;
import java.net.URL;
import java.util.ArrayList;
import java.util.List;

import javax.net.ssl.HttpsURLConnection;

import com.eclipsesource.json.JsonArray;
import com.eclipsesource.json.JsonObject;

public class UpsertCompanies {
 public List<JsonObject> input; //a list of Companies to use for input.  Each must have a member "externalCompanyId".
 public String action; //specify the action to be undertaken, createOnly, updateOnly, createOrUpdate
 public String dedupeBy; //select mode of Deduplication, dedupeFields for all dedupe parameters(externalCompanyId), idField for marketoId
 private String endpoint; //endpoint URL created with Constructor
 private Auth auth; //Marketo Auth Object


 //Constructs an UpsertOpportunities with Auth, but with no input set
 public UpsertCompanies(Auth auth){
  this.auth = auth;
  this.endpoint = this.auth.marketoInstance + "/rest/v1/companies.json";
 }
 //Constructs and UpsertOpportunities with Auth and input set
 public UpsertCompanies(Auth auth, List<JsonObject> input) {
  this(auth);
  this.input = input;
 }
 //adds input to existing list, creates arraylist if it was built without a list
 public UpsertCompanies addCompanies(JsonObject... companies){
  if (this.input == null){
   this.input = new ArrayList();
  }
  for (JsonObject jo : companies) {
   System.out.println(jo);
   this.input.add(jo);
  }
  return this;
 }

 public JsonObject postData(){
  JsonObject result = null;
  try {
   JsonObject requestBody = buildRequest(); //builds the Json Request Body
   String s = endpoint + "?access_token=" + auth.getToken(); //takes the endpoint URL and appends the access_token parameter to authenticate
   URL url = new URL(s);
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
   urlConn.disconnect();
  } catch (MalformedURLException e) {
   e.printStackTrace();
  } catch (IOException e) {
   e.printStackTrace();
  }
  return result;
 }

 private JsonObject buildRequest(){
  JsonObject requestBody = new JsonObject(); //Create a new JsonObject for the Request Body
  JsonArray in = new JsonArray(); //Create a JsonArray for the "input" member to hold Opp records
  for (JsonObject jo : input) {
   in.add(jo); //add our company records to the input array
  }
  requestBody.add("input", in);
  if (this.action != null){
   requestBody.add("action", action); //add the action member if available
  }
  if (this.dedupeBy != null){
   requestBody.add("dedupeBy", dedupeBy); //add the dedupeBy member if available
  }
  return requestBody;
 }
 //Getters and Setters
 //Setters return the UpsertCompanies instance to allow simple formatting:
 public List<JsonObject> getInput() {
  return input;
 }
 //sets or replaces existing input with list
 public UpsertCompanies setInput(List input) {
  this.input = input;
  return this;
 }
 public String getAction() {
  return action;
 }
 public UpsertCompanies setAction(String action) {
  this.action = action;
  return this;
 }
 public String getDedupeBy() {
  return dedupeBy;
 }
 public UpsertCompanies setDedupeBy(String dedupeBy) {
  this.dedupeBy = dedupeBy;
  return this;
 }

}
```

公司API提供了两个重复数据删除选项，由请求中的dedupeBy参数设置，“dedupeFields”和“idField”。 可以通过调用Describe Companies来显式检索这些项。 如果未设置dedupeBy ，则默认为dedupeFields。 对于公司记录，dedupeFields始终对应于“externalCompanyId”（由外部源设置的任意字符串）和idField（对应于“marketoId”字段），后者是Marketo在创建后生成并返回的整数。 根据为dedupeBy所做的选择，公司记录的任何更新插入调用中必须包含externalCompanyId或marketoId之一。 这些相同的要求适用于Opportunity和Opportunity Role对象API。 我们的代码公开两个构造函数：一个接受Auth对象的单个参数，另一个接受Auth和[JsonObject](https://github.com/ralfstx/minimal-json)公司记录的列表。 如果构建时没有输入List，则必须通过addCompanies方法添加公司记录，此方法将检查是否创建了新的ArrayList（如果输入为空），然后将所有JsonObject参数添加到输入List。 以下是用法示例：

```java
//Create a new company to associate to
JsonObject myCompany = new JsonObject().add("externalCompanyId", "myCompany");
UpsertCompanies upsertCompanies = new UpsertCompanies(auth).addCompanies(myCompany);
JsonObject companiesResult = upsertCompanies.postData();
System.out.println(companiesResult);
```

我们正在创建一个仅包含一个字段`externalCompanyId`的公司JsonObject，然后构造UpsertCompanies的实例，并将我们的公司添加到包含`addCompanies`的输入列表中。

### 商机

与公司对象类似，机会API具有`dedupeBy`参数，接受“dedupeFields”或“idField”，分别对应于“externalopportunityid”和“marketoGUID”。 下面是我们的代码，它与UpsertCompanies类非常相似：

```java
package dev.marketo.opportunities;

import java.io.IOException;
import java.io.InputStream;
import java.io.InputStreamReader;
import java.io.OutputStreamWriter;
import java.io.Reader;
import java.net.MalformedURLException;
import java.net.URL;
import java.util.ArrayList;
import java.util.List;

import javax.net.ssl.HttpsURLConnection;

import com.eclipsesource.json.JsonArray;
import com.eclipsesource.json.JsonObject;

public class UpsertOpportunities {
 public List<JsonObject> input; //a list of Opportunities to use for input.  Each must have a member "externalopportunityid".  Each can optionally include "externalCompanyId" for company association
 public String action; //specify the action to be undertaken, createOnly, updateOnly, createOrUpdate
 public String dedupeBy; //select mode of Deduplication, dedupeFields for all dedupe parameters, idField for marketoId
 private String endpoint; //endpoint URL created with Constructor
 private Auth auth; //Marketo Auth Object


 //Constructs an UpsertOpportunities with Auth, but with no input set
 public UpsertOpportunities(Auth auth){
  this.auth = auth;
  this.endpoint = this.auth.marketoInstance + "/rest/v1/opportunities.json";
 }
 //Constructs and UpsertOpportunities with Auth and input set
 public UpsertOpportunities(Auth auth, List<JsonObject> input) {
  this(auth);
  this.input = input;
 }
 public UpsertOpportunities addOpportunities(JsonObject... opp){
  if (this.input == null){
   this.input = new ArrayList();
  }
  for (JsonObject jo : opp) {
   System.out.println(jo);
   this.input.add(jo);
  }
  return this;
 }

 public JsonObject postData(){
  JsonObject result = null;
  try {
   JsonObject requestBody = buildRequest(); //builds the Json Request Body
   String s = endpoint + "?access_token=" + auth.getToken(); //takes the endpoint URL and appends the access_token parameter to authenticate
   URL url = new URL(s);
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
   urlConn.disconnect();
  } catch (MalformedURLException e) {
   e.printStackTrace();
  } catch (IOException e) {
   e.printStackTrace();
  }
  return result;
 }

 private JsonObject buildRequest(){
  JsonObject requestBody = new JsonObject(); //Create a new JsonObject for the Request Body
  JsonArray in = new JsonArray(); //Create a JsonArray for the "input" member to hold Opp records
  for (JsonObject jo : input) {
   in.add(jo); //add our Opportunity records to the input array
  }
  requestBody.add("input", in);
  if (this.action != null){
   requestBody.add("action", action); //add the action member if available
  }
  if (this.dedupeBy != null){
   requestBody.add("dedupeBy", dedupeBy); //add the dedupeBy member if available
  }
  return requestBody;
 }
 //Getters and Setters
 //Setters return the UpsertOpportunites instance to allow simple formatting:
 public List<JsonObject> getInput() {
  return input;
 }
 public UpsertOpportunities setInput(List<JsonObject> input) {
  this.input = input;
  return this;
 }
 public String getAction() {
  return action;
 }
 public UpsertOpportunities setAction(String action) {
  this.action = action;
  return this;
 }
 public String getDedupeBy() {
  return dedupeBy;
 }
 public UpsertOpportunities setDedupeBy(String dedupeBy) {
  this.dedupeBy = dedupeBy;
  return this;
 }

}
```

提供了相同的构造函数选项，采用Auth或`Auth+List<JsonObject>`以及`addOpportunities`方法来输入JsonObject机会。 以下是使用示例：

```java
//Create some JsonObjects for Opportunity Data
JsonObject opp1 = new JsonObject().add("name", "opportunity1")
    .add("externalopportunityid", "Opportunity1Test")
    .add("externalCompanyId", "myCompany")
    .add("externalCreatedDate", "2015-01-01T00:00:00z");
JsonObject opp2 = new JsonObject().add("name", "opportunity2")
    .add("externalopportunityid", "Opportunity2Test")
    .add("externalCompanyId", "myCompany")
    .add("externalCreatedDate", "2015-01-01T00:00:00z");

//Create an Instance of UpsertOpportunities and POST it
UpsertOpportunities upsertOpps = new UpsertOpportunities(auth)
                        .setAction("createOnly")
                        .addOpportunities(opp1, opp2);
JsonObject oppsResult = upsertOpps.postData();
System.out.println(oppsResult);
```

在此，我们将创建两个示例机会，然后为它们赋予name、externalopportunityid、externalCompanyId和externalCreatedDate字段的值。 我们尚未讨论externalCreatedDate，但必须加以利用，因为在RCE中，它被视为master字段（机会创建时），因此对正确的归因非常重要。 您可以使用贵组织的业务逻辑来确定您在此字段中输入的内容，该输入取决于您是在回填现有业务机会数据，还是在即时创建新业务机会数据。 我们创建UpsertOpportunities的实例，然后通过addOpportunities添加JsonObjects。 现在实例已配置，您可以使用postData将此实例推送到Marketo并打印结果

### 角色

角色与前面两个对象非常相似，不同之处在于，在将dedupeBy设置为dedupeFields时，它们的要求略有不同。 通过此方法创建或更新记录时，角色要求包含三个字段：“leadId”、“role”和“externalopportunityid”。 “role”可以是任何字符串值，但另外两个值必须分别引用商机的有效ID和机会的有效ID。

```java
package dev.marketo.opportunities;

import java.io.IOException;
import java.io.InputStream;
import java.io.InputStreamReader;
import java.io.OutputStreamWriter;
import java.io.Reader;
import java.net.MalformedURLException;
import java.net.URL;
import java.util.ArrayList;
import java.util.List;

import javax.net.ssl.HttpsURLConnection;

import com.eclipsesource.json.JsonArray;
import com.eclipsesource.json.JsonObject;

public class UpsertOpportunityRoles {
 public List<JsonObject> input; //Array of Opportunity Roles as JsonObjects, must have "leadId", "role" and "externalopprtunityid"
 public String action; //specify the action to be undertaken, createOnly, updateOnly, createOrUpdate, defaults to createOrUpdate if unset
 public String dedupeBy;//select mode of Deduplication, dedupeFields for all dedupe parameters, idField for marketoId
 private String endpoint; //endpoint URL created with Constructor
 private Auth auth; //Marketo Auth Object

 //Constructs an UpsertOpportunityRoles with Auth, but with no input set
 public UpsertOpportunityRoles(Auth auth) {
  this.auth = auth;
  this.endpoint = this.auth.marketoInstance + "/rest/v1/opportunities/roles.json";
 }
 //Constructs and UpsertOpportunities with Auth and input set
 public UpsertOpportunityRoles(Auth auth, List<JsonObject> input) {
  this(auth);
  this.input = input;
 }
 public UpsertOpportunityRoles addRoles(JsonObject... role){
  if (this.input == null){
   this.input = new ArrayList();
  }
  for (JsonObject jo : role) {
   System.out.println(jo);
   this.input.add(jo);
  }
  return this;
 }
 //executes the request to Marketo, body will be empty if input is not set
 public JsonObject postData(){
  JsonObject result = null;
  try {
   JsonObject requestBody = buildRequest(); //builds the Json Request Body
   String s = endpoint + "?access_token=" + auth.getToken(); //takes the endpoint URL and appends the access_token parameter to authenticate
   URL url = new URL(s);
   HttpsURLConnection urlConn = (HttpsURLConnection) url.openConnection(); //Return a URL connection and cast to HttpsURLConnection
   urlConn.setRequestMethod("POST");
   urlConn.setRequestProperty("Content-type", "application/json");//"application/json" content-type is required.
            urlConn.setRequestProperty("accept", "text/json");
            urlConn.setDoOutput(true);
   OutputStreamWriter wr = new OutputStreamWriter(urlConn.getOutputStream());
   wr.write(requestBody.toString());
   wr.flush();
   InputStream inStream = urlConn.getInputStream();  //get the inputStream from the URL connection
   Reader reader = new InputStreamReader(inStream);
   result = JsonObject.readFrom(reader); //Read from the stream into a JsonObject
   urlConn.disconnect();
  } catch (MalformedURLException e) {
   e.printStackTrace();
  } catch (IOException e) {
   e.printStackTrace();
  }
  return result;
 }

 public JsonObject buildRequest(){
  JsonObject requestBody = new JsonObject();
  JsonArray in = new JsonArray();
  for (JsonObject jo : input) {
   in.add(jo);
  }
  requestBody.add("input", in);
  if (this.action != null){
   requestBody.add("action", action);
  }
  if (this.dedupeBy != null){
   requestBody.add("dedupeBy", dedupeBy);
  }
  return requestBody;
 }
 //Getters and Setters
 //Setters return the UpsertOpportunites instance to allow simple formatting:
 public List<JsonObject> getInput() {
  return input;
 }
 public UpsertOpportunityRoles setInput(List<JsonObject> input) {
  this.input = input;
  return this;
 }
 public String getAction() {
  return action;
 }
 public UpsertOpportunityRoles setAction(String action) {
  this.action = action;
  return this;
 }
 public String getDedupeBy() {
  return dedupeBy;
 }
 public UpsertOpportunityRoles setDedupeBy(String dedupeBy) {
  this.dedupeBy = dedupeBy;
  return this;
 }

}
```

我们遵循的构造函数和addRoles方法的模式与上一个示例类似。 下面是一个示例。

```java
//Create Some opp roles now
JsonObject opp1Role = new JsonObject()
                        .add("role", "Captain")
                        .add("externalopportunityid", opp1.get("externalopportunityid").asString())
                        .add("leadId", 318794);
JsonObject opp2Role = new JsonObject()
                        .add("role", "Commander")
                        .add("externalopportunityid", opp2.get("externalopportunityid").asString())
                        .add("leadId", 318795);

//Create an Instance of UpsertOpportunityRoles and POST it
UpsertOpportunityRoles upsertRoles = new UpsertOpportunityRoles(auth)
                        .setAction("createOnly")
                        .addRoles(opp1Role, opp2Role);
JsonObject rolesResult = upsertRoles.postData();
System.out.println(rolesResult);
```

这里，我们为两个示例角色创建新的JsonObjects，并添加其必需的dedupeFields，从已创建的机会中提取externalopportunityid，然后将它们推送到Marketo。

### 融于一起

以下是主要方法的完整示例：

```java
package dev.marketo.opportunities;

import com.eclipsesource.json.JsonObject;

public class App
{
    public static void main( String[] args )
    {
     //create an Instance of Auth
        Auth auth = new Auth("CLIENT_ID_CHANGE_ME", "CLIENT_SECRET_CHANGE_ME", "MARKETO_HOST_CHANGE_ME");

        //Create a new company to associate to
        JsonObject myCompany = new JsonObject().add("externalCompanyId", "myCompany");
        UpsertCompanies upsertCompanies = new UpsertCompanies(auth).addCompanies(myCompany);
        JsonObject companiesResult = upsertCompanies.postData();
        System.out.println(companiesResult);

        //Create some JsonObjects for Opportunity Data
        JsonObject opp1 = new JsonObject().add("name", "opportunity1")
           .add("externalopportunityid", "Opportunity1Test")
           .add("externalCompanyId", "myCompany")
           .add("externalCreatedDate", "2015-01-01T00:00:00z");
        JsonObject opp2 = new JsonObject().add("name", "opportunity2")
           .add("externalopportunityid", "Opportunity2Test")
           .add("externalCompanyId", "myCompany")
           .add("externalCreatedDate", "2015-01-01T00:00:00z");

        //Create an Instance of UpsertOpportunities and POST it
        UpsertOpportunities upsertOpps = new UpsertOpportunities(auth)
                                .setAction("createOnly")
                                .addOpportunities(opp1, opp2);
        JsonObject oppsResult = upsertOpps.postData();
        System.out.println(oppsResult);

        //Create Some opp roles now
        JsonObject opp1Role = new JsonObject()
           .add("role", "Captain")
           .add("externalopportunityid", opp1.get("externalopportunityid").asString())
           .add("leadId", 318794);
        JsonObject opp2Role = new JsonObject()
           .add("role", "Commander")
           .add("externalopportunityid", opp2.get("externalopportunityid").asString())
           .add("leadId", 318795);

        //Create an Instance of UpsertOpportunityRoles and POST it
        UpsertOpportunityRoles upsertRoles = new UpsertOpportunityRoles(auth)
           .setAction("createOnly")
           .addRoles(opp1Role, opp2Role);
        JsonObject rolesResult = upsertRoles.postData();
        System.out.println(rolesResult);

    }
}
```

您可以看到创建公司、机会和角色的顺序。 现在，您已准备好将您的公司和机会数据同步到Marketo。

由&#x200B;_Kenny_&#x200B;发布于&#x200B;_2015-08-07_

## 使用Marketo REST API发送事务性电子邮件：第2部分，自定义内容

本周，我们将了解如何通过Request Campaign API调用将动态内容传递给我们的电子邮件。 请求营销活动不仅允许在外部触发电子邮件，而且您还可以替换电子邮件中[我的令牌](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/core-marketo-concepts/programs/tokens/understanding-my-tokens-in-a-program)的内容。 我的令牌是可重复使用的内容，可以在项目或营销文件夹级别进行自定义。 这些占位符也可以作为占位符存在，以通过请求营销活动调用替换。

### 构建电子邮件

为了自定义我们的内容，我们首先需要在Marketo中配置[程序](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/core-marketo-concepts/programs/creating-programs/create-a-program)和[电子邮件](https://experienceleague.adobe.com/zh-hans/docs/marketo/using/home)。 要生成自定义内容，我们需要在项目中创建令牌，然后将其放入要发送的电子邮件中。 为了方便起见，在本例中，我们仅使用一个令牌，但您可以在一封电子邮件、发件人电子邮件、发件人姓名、回复或电子邮件中的任何内容中替换任意数量的令牌。 因此，让我们创建一个用于替换的令牌富文本，并将其称为“bodyReplacement”。 富文本允许我们将令牌中的任何内容替换为我们要输入的任意HTML。 令牌在空时无法保存，因此请在此处插入一些占位符文本。 现在，我们需要将令牌插入到电子邮件中：现在，可以通过“请求营销活动”调用访问此令牌以进行替换。 此令牌可以非常简单，只需一行文本，需要根据每封电子邮件进行替换，也可以包含电子邮件的几乎整个布局。

### 代码

我们正在对上周的代码进行扩展，以将自定义令牌传递到我们的请求促销活动调用。

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
        String bodyReplacement = "<div class="replacedContent"><p>This content has been replaced</p></div>";
        rc.addToken("{{my.bodyReplacement}}", bodyReplacement);
        rc.postData();
    }
}
```

这次，我们在bodyReplacement变量中创建令牌的内容，然后使用addToken方法将其添加到请求中。 addToken获取一个键和一个值，然后创建一个JsonObject表示形式并将其添加到内部令牌数组。 然后在postData方法期间序列化它，并创建如下主体：

`{"input":{"leads":[{"id":1}],"tokens":[{"name":"{{my.bodyReplacement}}","value":"<div class="replacedContent"><p>This content has been replaced</p></div>"}]}}`

综合起来，我们的控制台输出如下所示：

```bash
Token is empty or expired. Trying new authentication
Trying to authenticate with&nbsp;...
Got Authentication Response: {"access_token":"19d51b9a-ff60-4222-bbd5-be8b206f1d40:st","token_type":"bearer","expires_in":3565,"scope":"<apiuser@mktosupport.com>"}
Executing RequestCampaign call
Endpoint: .../rest/v1/campaigns/1578/trigger.json?access_token=19d51b9a-ff60-4222-bbd5-be8b206f1d40:st
Request Body:
{"input":{"leads":[{"id":1}],"tokens":[{"name":"{{my.bodyReplacement}}","value":"<div class="replacedContent"><p>This content has been replaced</p></div>"}]}}
Result:
{"requestId":"1e8d#14eadc5143d","result":[{"id":1578}],"success":true}
```

### 正在结束

此方法可通过多种方式扩展，可更改单个布局分区内电子邮件中的内容或外部电子邮件中的内容，从而将自定义值传递到任务或有趣的时间点。 通过此方法，可以在程序中使用令牌的任何位置进行自定义。 [计划营销活动](https://developer.adobe.com/marketo-apis/api/mapi/#operation/scheduleCampaignUsingPOST)调用也提供了类似功能，可让您处理整个批次营销活动中的令牌。 无法基于每个潜在客户自定义这些内容，但对于在广泛的潜在客户群中自定义内容非常有用。

由&#x200B;_Kenny_&#x200B;发布于&#x200B;_2015-07-24_

## 使用API更新Marketo中的潜在客户数据

一年多以前，我们在Marketo中发布了使用API更新客户和潜在客户信息。 该帖子提供了一个代码示例，可以定期运行该代码示例来轮询外部系统以获取更新。 其思想是检索外部数据，并将其推送到Marketo以更新潜在客户信息。 提供的代码示例使用了我们的SOAP API。 REST端点以实现相同的目标。 **程序输入**&#x200B;您可能需要将数据从外部系统转换为可由Marketo REST API使用的格式。 由于我们使用的是创建/更新潜在客户API，因此数据必须采用JSON格式，并在请求正文中发送。 以一个示例对此进行了最好的解释。 在下面的Java示例代码中，我们将模拟潜在客户数据放置在字符串数组中。 每个潜在客户的以下数据为每个字符串：名字、姓氏、电子邮件地址、职务。

```java
private static String externalLeadData[] = {
        "Henry, Adams, <henry@superstar.com>, Director of Demand Generation",
        "Suzie, Smith, <ssmith@gmail.com>, VP Marketing"
};
```

示例代码将数组转换为下面的JSON块。

```json
{
"input":
    [
        {"firstName":"Henry", "lastName":"Adams", "email":"<henry@superstar.com>", "title":"Director of Demand Generation"},
        {"firstName":"Suzie", "lastName":"Smith", "email":"<ssmith@gmail.com>", "title":"VP Marketing"}
    ]
}
```

每个“input”数组项都对应于Marketo中的单个潜在客户。 Marketo数组项是包含一个或多个潜在客户字段名称及其相应值的JSON对象。 您决定指定的字段名称（在本例中为firstName、lastName、email和title）必须与为Marketo订阅定义的REST API名称匹配。 您可以通过导出字段名称，在Marketo管理面板的字段管理部分找到REST API名称。 字段名称将导出到Excel文件中，如下所示。 或者，您可以通过调用[Describe Lead](https://developer.adobe.com/marketo-apis/api/mapi/#operation/describeUsingGET_2) API以编程方式查找字段名称。 例如，以下是一个Describe响应片段，其中包含名字字段的REST API名称。

```json
{
    "id": 29,
    "displayName": "First Name",
    "dataType": "string",
    "length": 255,
    "soap": {
        "name": "FirstName",
        "readOnly": false
    },
    "rest": {
        "name": "firstName",
        "readOnly": false
    }
},
```

**项目逻辑**&#x200B;有效负载采用正确的格式后，我们便可以调用“创建/更新潜在客户”。 请注意，在此示例中，我们未指定任何可选参数。 默认行为是创建或更新潜在客户记录(upsert)，使用电子邮件进行重复数据删除，并使用同步处理。 如果创建/更新潜在客户调用成功，则响应正文包含一个包含Marketo潜在客户ID和创建/更新操作状态的“结果”数组。 根据您在请求中传递的“action”参数，状态可以是“已更新”、“已创建”或“已跳过”。 继续我们的例子，假设第一个线索不存在（亨利·亚当斯），而第二个线索存在（苏西·史密斯）。 类似于以下内容的响应将指示第一个潜在客户已创建，第二个潜在客户已更新。

```json
{
    "success":true,
    "requestId":"118f8#14f1a0b82fc",
    "result":[
        {
            "id":318798,
            "status":"created"
        },
        {
            "id":318797,
            "status":"updated"
        }
    ]
}
```

就目前而言，就这样了。 快乐的编程！ **程序代码**

```java
package com.marketo;

// minimal-json library (<https://github.com/ralfstx/minimal-json>)
import com.eclipsesource.json.JsonArray;
import com.eclipsesource.json.JsonObject;

import javax.net.ssl.HttpsURLConnection;
import java.io.IOException;
import java.io.InputStream;
import java.io.OutputStreamWriter;
import java.net.MalformedURLException;
import java.net.URL;
import java.util.\*;

public class CreateUpdateLeads {
    //
    // Define Marketo REST API access credentials: Account Id, Client Id, Client Secret.  For example:
    //    public static String CUSTOM_SERVICE_DATA[] =
    //      {"111-AAA-222", "2f4a4435-f6fa-4bd9-3248-098754982345", "asdf6IVE9h4Jjcl59cOMAKFSk78ut12W"};
    //
    private static final String CUSTOM_SERVICE_DATA[] =
            { INSERT YOUR CUSTOM SERVICE DATA HERE };

    //
    // Mock up data that could be read from an external data source.
    // An array of CSV strings the form:
    //   "firstName, lastName, email, title"
    private static String externalLeadData[] = {
        "Henry, Adams, henry@superstar.com, Director of Demand Generation",
        "Suzie, Smith, ssmith@gmail.com, VP Marketing"
    };

    public static void main(String[] args) {
        // Compose base URL
        String baseUrl = String.format("https://%s.mktorest.com",
                CUSTOM_SERVICE_DATA[0]);

        // Compose Identity URL
        String identityUrl = String.format("%s/identity/oauth/token?grant_type=%s&client_id=%s&client_secret=%s",
                baseUrl, "client_credentials", CUSTOM_SERVICE_DATA[1], CUSTOM_SERVICE_DATA[2]);

        // Call Identity API
        JsonObject identityObj = JsonObject.readFrom(httpRequest("GET", identityUrl, null));
        String accessToken = identityObj.get("access_token").asString();

        // Compose URL for Create/Update Leads
        String createUpdateLeadsUrl = String.format("%s/rest/v1/leads.json?access_token=%s", baseUrl, accessToken);

        // Convert String array into JsonArray to pass as part of request body
        JsonArray input = convertExternalLeadDataToJson(externalLeadData);

        // Build request body JSON
        JsonObject requestObj = new JsonObject();
        requestObj.add("input", input);

        // Call Create/Update Lead API
        JsonArray result = new JsonArray();
        JsonObject leadObj = JsonObject.readFrom(httpRequest("POST", createUpdateLeadsUrl, requestObj.toString()));
        if (leadObj.get("success").asBoolean()) {
            if (leadObj.get("result") != null) {
                result = leadObj.get("result").asArray();
            }
        }

        // Print out results object
        System.out.println(result);
        System.exit(0);
    }

    // Convert array of CSV formatted Strings into an array of JSON objects
    private static JsonArray convertExternalLeadDataToJson(String input[]) {
        JsonArray output = new JsonArray();

        // Loop through each CSV row in array
        for (int i = 0; i < input.length; i++) {
            // Split CSV row into separate fields
            List items = Arrays.asList(input[i].split(","));

            // Add fields to JSON object
            JsonObject lead = new JsonObject();
            lead.add("firstName", items.get(0));
            lead.add("lastName", items.get(1));
            lead.add("email", items.get(2));
            lead.add("title", items.get(3));
            output.add(lead);
        }
        return output;
    }

    // Perform HTTP request
    private static String httpRequest(String method, String endpoint, String body) {
        String data = "";
        try {
            URL url = new URL(endpoint);
            HttpsURLConnection urlConn = (HttpsURLConnection) url.openConnection();
            urlConn.setDoOutput(true);
            urlConn.setRequestMethod(method);
            switch (method) {
                case "GET":
                    break;
                case "POST":
                    urlConn.setRequestProperty("Content-type", "application/json");
                    urlConn.setRequestProperty("accept", "text/json");
                    OutputStreamWriter wr = new OutputStreamWriter(urlConn.getOutputStream());
                    wr.write(body);
                    wr.flush();
                    break;
                default:
                    System.out.println("Error: Invalid method.");
                    return data;
            }
            int responseCode = urlConn.getResponseCode();
            if (responseCode == 200) {
                InputStream inStream = urlConn.getInputStream();
                data = convertStreamToString(inStream);
            } else {
                System.out.println(responseCode);
                data = "Status:" + responseCode;
            }
        } catch (MalformedURLException e) {
            System.out.println("URL not valid.");
        } catch (IOException e) {
            System.out.println("IOException: " + e.getMessage());
            e.printStackTrace();
        }
        return data;
    }

    private static String convertStreamToString(InputStream inputStream) {
        try {
            return new Scanner(inputStream).useDelimiter("A").next();
        } catch (NoSuchElementException e) {
            return "";
        }
    }
}
```

由&#x200B;_David_&#x200B;发布于&#x200B;_2015-08-14_

## 将SalesPerson数据添加到Marketo

使用新的SalesPerson API，您可以在没有本机CRM集成的实例中将Marketo潜在客户与SalesPerson记录自由关联。 这允许在Marketo中使用{{lead.Lead Owner Email Address}}以及相关的字段和令牌。

### 创建销售人员记录

为了将销售线索与SalesPerson记录相关联，我们首先需要将SalesPerson记录输入到Marketo中。 以下是PHP中的一个示例类：

```php
<?php

class SalesPerson{
 private $auth;//auth object
 private $action;// string designating request action, createOnly, updateOnly, createOrUpdate
 private $dedupeBy;//dedupeFields or idField
 private $input;//array of salesperson objects for input

 //takes an Auth object as the first argument
 public function _construct($auth, $input){
  $this->auth = $auth;
  $this->input = $input;
 }

 //constructs the json request body
 private function bodyBuilder(){
  $body = new stdClass();
  $body->input = $this->input;
  if (isset($this->action)){
   $body->action = $this->action;
  }
  if (isset($this->dedupeBy)){
   $body->dedupeBy = $this->dedupeBy;
  }
  return json_encode($body);
 }
 //submits the request to Marketo and returns the response as a string
 public function postData(){
  $url = $this->auth->getHost() . "/rest/v1/salespersons.json?access_token=" . $this->auth->getToken();
  $ch = curl_init($url);
  $requestBody = $this->bodyBuilder();
  curl_setopt($ch,  CURLOPT_RETURNTRANSFER, 1);
  curl_setopt($ch, CURLOPT_HTTPHEADER, array('accept: application/json','Content-Type: application/json'));
  curl_setopt($ch, CURLOPT_POST, 1);
  curl_setopt($ch, CURLOPT_POSTFIELDS, $requestBody);
  curl_getinfo($ch);
  $response = curl_exec($ch);
  curl_close($ch);
  return $response;
 }
 //getters and setters
 public function setAction($action){
  $this->action = $action;
 }
 public function getAction($action){
  return $this->action;
 }
 public function setDedupeBy($dedupeBy){
  $this->dedupeBy = $dedupeBy;
 }
 public function getDedupeBy($dedupeBy){
  return $this->dedupeBy;
 }
 public function addSalesPersons($input){
  if($this->input != null){
   if (is_array($input)){
    foreach($input as $item){
     array_push($this->input, $item);
    }
   }else{
    array_push($this->input, $input);
   }
  }else{
   $this->input = $input;
  }
 }
 public function getInput(){
  return $this->input;
 }

}
```

在上述类中，$input变量是一个stdClass对象的数组，可能具有成员：

* externalSalesPersonId（仅在创建时有效）
* id（仅作为updateOnly模式中的键）
* 电子邮件
* 传真
* firstName
* 姓氏
* 移动电话
* 电话
* 标题

可通过Describe SalesPerson调用检索有关类型和字段长度的更多详细信息。

### 同步潜在客户

下面是一个用于同步我们需要的潜在客户的快速示例类：

```php
<?php

class Leads{
 private $auth;//auth object
 private $action;// string designating request action, createOnly, updateOnly, createOrUpdate
 private $lookupField;//dedupeFields or idField
 private $input;//array of salesperson objects for input
 private $asyncProcessing;//specify whether input should be processed asynchronously
 private $partitionName;//partition name for update if requires

 //takes an Auth object as the first argument
 public function _construct($auth, $input){
  $this->auth = $auth;
  $this->input = $input;
 }

 //constructs the json request body
 private function bodyBuilder(){
  $body = new stdClass();
  $body->input = $this->input;
  if (isset($this->action)){
   $body->action = $this->action;
  }
  if (isset($this->lookupField)){
   $body->lookupField = $this->lookupField;
  }
  return json_encode($body);
 }
 //submits the request to Marketo and returns the response as a string
 public function postData(){
  $url = $this->auth->getHost() . "/rest/v1/leads.json?access_token=" . $this->auth->getToken();
  $ch = curl_init($url);
  $requestBody = $this->bodyBuilder();
  curl_setopt($ch,  CURLOPT_RETURNTRANSFER, 1);
  curl_setopt($ch, CURLOPT_HTTPHEADER, array('accept: application/json','Content-Type: application/json'));
  curl_setopt($ch, CURLOPT_POST, 1);
  curl_setopt($ch, CURLOPT_POSTFIELDS, $requestBody);
  curl_getinfo($ch);
  $response = curl_exec($ch);
  curl_close($ch);
  return $response;
 }
 //getters and setters
 public function setAction($action){
  $this->action = $action;
 }
 public function getAction(){
  return $this->action;
 }
 public function setDedupeBy($lookupField){
  $this->dedupeBy = $lookupField;
 }
 public function getDedupeBy(){
  return $this->dedupeBy;
 }
 public function addLeads($input){
  if($this->input != null){
   if (is_array($input)){
    foreach($input as $item){
     array_push($this->input, $item);
    }
   }else{
    array_push($this->input, $input);
   }
  }else if (is_array($input)){
   $this->input = $input;
  }else{
   $this->input = [$input];
  }
 }
 public function getInput(){
  return $this->input;
 }
 public function setAsyncProcessing($asyncProcessing){
  $this->asyncProcessing = $asyncProcessing;
 }
 public function getAsyncProcessing() {
  return $this->asyncProcessing;
 }
 public function setPartitionName($partitionName){
  $this->partitionName = $partitionName;
 }
 public function getPartitionName() {
  return $this->partitionName;
 }

}
```

### 进程

以下是创建两个销售人员记录并将它们与两个潜在客户记录关联的示例：

```php
<?php

require 'Auth.php';
require 'SalesPerson.php';
require 'Leads.php';

//Create an Auth object for authentication
$auth = new Auth("https://284-RPR-133.mktorest.com", "7f99bd49-0e0e-4715-a72a-0c9319f75552", "O5lZHhrQDfDKRhulY89IU42PfdhRSe6m");

//Compose new SalesPerson Records
$sales1 = new stdClass();
$sales1->externalSalesPersonId = "SalesPerson 1";
$sales1->email = "sales1@example.com";
$sales1->firstName = "Jane";
$sales1->lastName = "Doe";

$sales2 = new stdClass();
$sales2->externalSalesPersonId = "SalesPerson 2";
$sales2->email = "sales2@example.com";
$sales2->firstName = "John";
$sales2->lastName = "Doe";

//Compose lead records
$lead1 = new stdClass();
$lead1->email = "marketoDev@example.com";
//Add the external id of the desired salesperson
$lead1->externalSalesPersonId = $sales1->externalSalesPersonId;

$lead2 = new stdClass();
$lead2->email = "devBlog@example.com";
$lead2->externalSalesPersonId = $sales2->externalSalesPersonId;

//Create a new instance of SalesPerson to submit records
$salesPerson = new SalesPerson($auth, [$sales1, $sales2]);
$spResponse = $salesPerson->postData();
print_r($spResponse . "rn");
$json = json_decode($spResponse);

//Check for success on synching SalesPersons
if ($json->success){
 $leads = new Leads($auth, [$lead1, $lead2]);
 $leadsResponse = $leads->postData();
 print_r($leadsResponse . "rn");
}else{
 print_r("SalesPerson request failed with errors:rn");
 foreach ($json->errors as $error){
  print_r("code: " . $error->code . ", message: " . $error->message . "rn");
 }
}
```

对于我们的示例类，我们只是创建stdClass对象来表示需要同步的SalesPerson和Lead记录，并将每个所需的字段添加为成员。 执行此代码后，潜在客户<marketoDev@example.com>和<devBlog@example.com>将同时填充“潜在客户所有者电子邮件”、“潜在客户所有者名字”和“潜在客户所有者姓氏”字段，从而使他们能够使用这些字段的相关令牌，并按相关的智能列表筛选器进行筛选。

### 身份验证类

```php
<?php

class Auth{
 private $host;//host of the target Marketo instance
 private $clientId;//client Id
 private $clientSecret;//client secret
 private $token;//access_token
 private $expiry;

 function _construct($host, $clientId, $clientSecret){
  $this->host = $host;
  $this->clientId = $clientId;
  $this->clientSecret = $clientSecret;
 }
 public function getToken(){
  if (!isset($this->token) || $this->expiry > time()){
   $data = $this->getData();
   $this->expiry = time() + $data->expires_in;
   $this->token = $data->access_token;
   return $this->token;
  }else{
   return $this->token;
  }
 }
 private function getData(){
  $url = $this->host . "/identity/oauth/token?grant_type=client_credentials&client_id=" . $this->clientId . "&client_secret="
     . $this->clientSecret;
  $ch = curl_init($url);
  curl_setopt($ch,  CURLOPT_RETURNTRANSFER, 1);
  curl_setopt($ch, CURLOPT_HTTPHEADER, array('accept: application/json','Content-Type: application/json'));
  $response = curl_exec($ch);
  $json = json_decode($response);
  curl_close($ch);
  return $json;
 }
 public function getHost(){
  return $this->host;
 }
}
```

由&#x200B;_Kenny_&#x200B;发布于&#x200B;_2015-08-21_

## API用户和自定义服务的最佳实践

Marketo的REST API使用自定义服务进行身份验证，并且其中每个服务都由仅限API的Marketo用户拥有。 每个自定义服务的功能由分配给该用户的每个角色的权限决定。 将个人用户和自定义服务分配给集成可为您带来多项好处：

* 您可以通过为用户提供的角色微调提供给每个单独服务的[权限](/help/rest-api/custom-services.md)。
* 您可以通过删除相应的自定义服务来禁止各个Web服务调用您的实例，而无需禁用其他服务。
* API调用使用情况报告将按用户细分，以便您识别高使用率和异常使用率
* 更容易确定每个Web服务被授予访问权限的数据
* 启用了Workspace的实例可以限制对特定业务部门的访问，方法是仅将角色授予可访问的工作区

### API 使用情况

每个API用户在API使用情况报表中单独报告，因此按用户拆分Web服务允许您轻松计算每个集成的使用情况。 如果对实例的API调用数量超过限制并导致后续调用失败，则使用此实践可让您考虑来自每个服务的数量，并让您评估如何解决此问题。 通过转至“管理员” — >“Web服务”并单击过去7天内的呼叫数来查看您的使用情况。

### 禁用服务

如果集成产生不良影响，并且尚未为每个集成分配单独的自定义服务，则可能乏味且难以禁用该集成。 逐个划分这些区段就像在管理员 — >启动点中删除违规服务一样容易。

### Workspace管理

对于Marketo Enterprise订阅，服务通常只需要访问单个工作区，并且可以通过向API用户分配角色来[强制实施此功能](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/administration/workspaces-and-person-partitions/allow-user-access-to-a-workspace)。 每个用户角色都可以全局分配，也可以按工作区进行分配，因此可以根据需要在工作区中限制访问，尽可能提供最低的权限集。

由&#x200B;_Kenny_&#x200B;发布于&#x200B;_2015-08-28_

## 如何使用REST API指定Lead分区

**潜在客户分区** Marketo潜在客户分区提供了一种隔离潜在客户的便捷方法。 分区可允许组织内的不同营销组共享单个Marketo实例。 有关详细信息，请参阅[了解工作区和潜在客户分区](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/administration/workspaces-and-person-partitions/understanding-workspaces-and-person-partitions)。 假设您正在使用潜在客户分区，并使用Marketo REST API以编程方式创建潜在客户。 如何确保您创建的潜在客户最终将位于正确的分区中？ 这篇帖子向您展示如何做到这一点！ 在本例中，我们将使用工作区和分区根据地理位置隔离潜在客户。

首先，我们将定义一个名为“国家/地区”的工作区。 接下来，在该工作区中创建两个分区，分别名为“墨西哥”和“加拿大”。  **在分区中创建潜在客户**&#x200B;假设现在要在“墨西哥”分区中创建两个潜在客户。 要创建潜在客户，我们调用。 要指定分区，必须在请求正文中包含“partitionName”属性。 我们如何知道要用于partitionName值的内容？ 通过调用[Get Lead Partitions](https://developer.adobe.com/marketo-apis/api/mapi/#operation/describeProgramMemberUsingGET) API，我们可以检索实例的有效分区名称值列表，如下所示：

`GET /rest/v1/leads/partitions.json`

```json
{
    "requestId": "20ae#14f9a1a5a30",
    "result": [
        {
            "id": 1,
            "name": "Default",
            "description": "Initial system lead partition"
        },
        {
            "id": 2,
            "name": "Mexico",
            "description": "Leads that live in Mexico"
        },
        {
            "id": 3,
            "name": "Canada",
            "description": "Leads that live in Canada"
        }
    ],
    "success": true
}
```

在本例中，正确的值为“Mexico”，因此我们按如下方式将其传递给“创建/更新潜在客户”：

`POST /rest/v1/leads.json`

```json
{
    "input": [
        {"email":"enrique.pena-nieto@gob.mx"},
        {"email":"angelica.rivera@gob.mx"}
    ],
    "action":"createOrUpdate",
    "partitionName":"Mexico"
}
```

以下是我们在Marketo中新创建的潜在客户。  **更新分区中的潜在客户**&#x200B;要更新分区中的现有潜在客户，我们只需调用“创建/更新潜在客户”并指定partitionName，与之前相同。 对于此更新，我们将为上面创建的潜在客户分配名字、姓氏和标题。

`POST /rest/v1/leads.json`

```json
{
"input": [
        {"email":"enrique.pena-nieto@gob.mx", "firstName":"Enrique", "lastName":"Pena Neito", "title": "El Presidente"},
        {"email":"angelica.rivera@gob.mx", "firstName":"Angelica", "lastName": "Rivera", "title": "Primera Dama"}
    ],
    "action":"createOrUpdate",
    "partitionName":"Mexico"
}
```

以下是我们Marketo中最新更新的潜在客户。
**识别潜在客户的分区**&#x200B;我们如何知道潜在客户位于哪个分区？ 为此，我们使用[Get Lead by Id](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getLeadByIdUsingGET) API，并在“字段”查询参数中指定“leadPartitionId”。 在本例中，我们将检索上面创建的商机ID318816信息。

`GET /rest/v1/lead/318816.json?fields=leadPartitionId,email,firstName,lastName,title`

```json
{
    "requestId": "5c57#14f9a495b1f",
    "result": [
        {
            "id": 318816,
            "lastName": "Pena Neito",
            "title": "El Presidente",
            "email": "enrique.pena-nieto@gob.mx",
            "firstName": "Enrique",
            "leadPartitionId": 2
        }
    ],
    "success": true
}
```

请注意，我们将返回leadPartitionId而不是partitionName。 要获取partitionName，我们需要从上面的Get Lead Partitions API重新访问输出。

```json
{
    "id": 2,
    "name": "Mexico",
    "description": "Leads that live in Mexico"
},
```

在本例中，leadPartitionId值2映射到“Mexico”的partitionName。 现在就到此为止了。 分区快乐！

由&#x200B;_David_&#x200B;发布于&#x200B;_2015-09-04_

## 比较Marketo电子邮件脚本中的得分字段

**注意：**&#x200B;这是Cathal Moran的访客帖子。 Cathal是Marketo在爱尔兰都柏林的EMEA办事处工作的解决方案顾问。

### 比较得分字段

许多Marketo客户（尤其是专注于交叉销售的客户）具有多个得分字段，这通常用于衡量潜在客户对特定产品/领域的兴趣。 想象一下，我卖苹果和香蕉，如果一个商机对苹果打分50分，对香蕉打分10分，那么就很清楚偏好在哪里，如果我的内容反映了这种偏好，那不是件好事。 电子邮件脚本可用于比较分数，并根据接收电子邮件的特定商机的最高（或最低）分数，对电子邮件中的内容进行个性化。

### 由于我想比较2个数字，因此我需要将字段值转换为整数

```
#set ($score1 = $math.toInteger(${lead.Apple_Score}))
#set ($score2 = $math.toInteger(${lead.Banana_Score}))
##check if the lead score is greater than feature score
#if($score1 >= $score2)
                ##if Apple score is greater
                #set($Interest = "Special offer on Apples")
##check is the feature score is higher
#elseif($score2 >= $score1)
                ##if Feature score is greater
                #set($Interest = "Special offer on Bananas")
#else
                ##otherwise
                #set($Interest = "Special offer on Fruit")
#end
##display the Interest as content
${Interest}
```

在上例中，我只对文本进行个性化设置，但我也可以轻松地根据较高的分数显示不同的图像。

由&#x200B;_Kenny_&#x200B;发布于&#x200B;_2015-09-14_

## 在后台提交Marketo表单

当您的组织拥有许多不同的平台来托管Web内容和客户数据时，通常需要从表单中并行提交数据，以便在不同的平台上收集结果数据。 有多种策略可以做到这一点，但最好的策略通常最简单：使用Forms 2 API提交隐藏的Marketo表单。 此操作适用于任何新的Marketo表单，但理想情况下，您应该为此创建一个没有字段的空表单。 这将确保表单不会加载任何超出所需数量的数据，因为我们不需要渲染任何内容。 现在，只需从表单中获取[嵌入代码](https://experienceleague.adobe.com/zh-hans/docs/marketo/using/home)并将其添加到所需页面的正文中，即可进行较小的修改。 您的嵌入代码包含如下表单元素：

`<form id="mktoForm_1068"></form>`

您需要将&#39;style=&quot;display:none&quot;&#39;添加到元素，使其不可见，如下所示：

`<form id="mktoForm_1068" style="display:none"></form>`

一旦嵌入并隐藏了表单，提交表单的代码就会非常简单：

```javascript
var myForm = MktoForms2.allForms()[0];
myForm.addHiddenFields({
 //These are the values which will be submitted to Marketo
 "Email":"test@example.com",
 "FirstName":"John",
 "LastName":"Doe"
 });
myForm.submit();
```

Forms以这种方式提交的行为将与潜在客户填写并提交可见表单时的行为完全相同。 由于每个实施采用不同的操作来提示提交，因此触发提交的方式因实施而异，但您基本上可以在任何操作中进行触发。 重要的部分是正确设置字段和值。 请确保使用您可在[导出字段名称](/help/rest-api/list-of-standard-fields.md)中找到的字段的SOAP API名称，以确保正确提交值。

从Munchkin关联潜在客户迁移

后台表单提交是Munchkin关联商机的推荐替换方法之一。 下面的示例HTML页面通过重用提交给“关联潜在客户”的相同值来演示高级别的迁移。

```html
<html>
<head>
    <!--
      Munchkin Code
      Replace with your own instance code
    -->
    <script type="text/javascript">
        (function() {
          var didInit = false;
          function initMunchkin() {
            if(didInit === false) {
              didInit = true;
              Munchkin.init('CHANGE ME');
            }
          }
          var s = document.createElement('script');
          s.type = 'text/javascript';
          s.async = true;
          s.src = '//munchkin.marketo.net/munchkin-beta.js';
          s.onreadystatechange = function() {
            if (this.readyState == 'complete' || this.readyState == 'loaded') {
              initMunchkin();
            }
          };
          s.onload = initMunchkin;
          document.getElementsByTagName('head')[0].appendChild(s);
        })();
        </script>
</head>

<body>
  <!--
    Start Embed code.
    Pasted from Form Actions -> Embed Code except for addition of 'style="display:none"' to the form tag in order to hide it, and instance-specific codes redacted
    Replace with your own code for testing
  -->
  <script src="CHANGE ME"></script>
  <form id="CHANGE ME" style="display:none"></form>
  <script>MktoForms2.loadForm("CHANGE ME", "CHANGE ME", "CHANGE ME TO AN INTEGER ID");</script>
  <!--End Embed Code-->

  <!--Demo code-->
    <script>
        //The same map which is assembled for the Munchkin submission can be reused for the form submission
        let values = {
            "Email": "email@example.com",
            "FirstName": "Test",
            "LastName": "Record"
        }
        //whenReady lets us apply a callback to all mkto forms on the page
        //the callback function fires whenever a form is completely loaded
        //for most use cases this will just be used to capture a reference to the form for later usage
        //submission is done in line here for demonstration only
        MktoForms2.whenReady(function(form){

            //the addHiddenFields methods lets us add arbitrary fields to the form as well as their values
            form.addHiddenFields(values);

            //submit the form
            form.submit();


        })
    </script>
</body>
</html>
```

由&#x200B;_Kenny_&#x200B;发布于&#x200B;_2015-09-25_

## Marketo REST API异常和错误处理：第1部分

Marketo REST API可能会返回异常或错误，为了方便起见，我们将从此处开始调用错误。 错误可能发生在三个不同的级别：

* HTTP级别，这些错误由4xx代码指示
* 响应级别，这些错误包含在JSON响应的“错误”数组中
* 记录级别时，这些错误包含在JSON响应的“结果”数组中，并使用“状态”字段和“原因”数组以单个记录为基础指示

### HTTP错误

在正常操作情况下，Marketo应只返回两个HTTP状态错误：413请求实体太大，414请求URI太长。 通过捕获错误、修改请求和重试，这些都可恢复，但通过智能编码实践，您绝不应该在野外遇到这些情况。 如果请求有效负载超过1MB，则Marketo将返回413，如果是[导入潜在客户](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Bulk-Import-Leads)，则将返回10MB。 在大多数情况下，不会达到这些限制，但是向请求的大小添加检查并移动任何导致限制超过新请求的记录应该可以防止任何端点返回导致此错误的任何情况。 当GET请求的URI超过8KiB时，将返回414。 要避免出现这种情况，请检查查询字符串的长度，看看是否超过此限制。 如果它确实将您的请求更改为POST方法，则使用额外的参数“_method=GET”将您的查询字符串输入为请求正文。 这放弃了URI的限制。 在大多数情况下，很少达到此限制，但在检索具有长单个过滤器值（如GUID）的大量记录时，这种情况比较常见。

### 响应级错误

将响应的“success”参数设置为false时，出现响应级别错误。 “错误”中的每个条目都有两个成员，“编码”一个数字（6xx或7xx）和一个“消息”，提供错误的纯文本原因。 6xx代码始终指示请求完全失败并且未执行。 例如，“Access token invalid”601，可通过重新验证并使用请求传递新访问令牌来恢复该值。 7xx错误表示请求失败，可能是因为未返回任何数据，或者请求被错误地参数化，如包含无效的日期，或缺少所需的参数。

### 记录级错误

记录级别错误表示无法为单个记录完成操作，但请求本身有效。 这些操作出现在响应的“result”数组中的单个记录中。 将“跳过”这些记录的“状态”字段，并会出现“原因”数组。 每个原因都包含一个“代码”成员和一个“消息”成员。 代码将始终为1xxx，并且消息将指示跳过记录的原因。 例如，Create/Update Leads请求的“action”设置为“createOnly”，但已提交的记录中的某个键已存在商机。 此示例返回代码1005，并显示消息“Lead already exists”（潜在客户已存在）。 在接下来的几个帖子中，我们将查看可恢复的错误，以及代码中如何处理这些错误的一些示例。

由&#x200B;_Kenny_&#x200B;发布于&#x200B;_2015-10-09_

## 来宾帖子 — 将SQL连接器直接连接到Marketo数据库

**这是来自Sumit Sarkar of Progress， Inc.的客座帖子。**

指向Marketo数据库的&#x200B;**SQL连接器** Marketo已详细记录用于访问数据的API。 但是，有时组织需要直接SQL访问。 在Marketo分店，我们看到Marketing和IT之间的界线变得模糊，这增加了人们对基于标准的SQL连接的需求。 到Marketo的直接SQL连接器可通过[Progress DataDirect Cloud](https://www.progress.com/connectors/cloud-data-integration)使用。 DataDirect Cloud是一种数据连接服务，它通过开放的行业标准公开Marketo数据，以实现跨ODBC（“开放式数据库连接”）或JDBC（“Java数据库连接”）的SQL访问；以及使用OData（“开放式数据协议”）的REST。 以下是使用DataDirect Cloud现成连接Marketo数据的一些常见用例：

* 数据发现和可视化( Qlik 、 Tableau 、 SAP Lumira )
* 企业报告（ SAP业务对象、微战略、Cognos ）
* 数据集成(SQL Server Integration Services - SSIS、Oracle Data Integrator - ODI、Informatica)
* 数据联合(SQL Server Linked Server、SAP Hana SDA、Oracle Database Gateway)
* 临时查询(Microsoft Office、DB Visualizer、Aqua Data Studio)
* 数据准备(Alteryx、Trifecta、Paxata)

**对Marketo的SQL访问如何工作？**

* DataDirect Cloud为Marketo的集成API公开的数据创建逻辑架构。
* DataDirect Cloud对从Marketo API获取的数据使用弹性实时查询引擎处理来自轻量级ODBC或JDBC客户端的SQL请求。
* DataDirect Cloud连接是直接而实时的，没有任何数据重复。
* DataDirect Cloud Service对Marketo API进行了抽象，这样最终用户就不必担心API版本更改，也不必担心SOAP与REST的使用。

自2006年以来，DataDirect一直在构建这种与SaaS数据源的连接方式，首先是在其Web服务API之上构建的第一个Salesforce ODBC驱动程序。 **开始连接到Marketo：**

1. [注册DataDirect云登录](https://pacific.progress.com/console/register?productName=d2c&ignoreCookie=true)
1. 单击“数据源”，然后单击“+新建数据Source”按钮
1. 选择“Marketo”并输入连接信息。 您可以咨询Marketo管理员或登录名以查找[SOAP集成的连接信息](/help/soap-api/soap-api.md)。
1. 单击“测试连接”按钮。 请注意，有一个OData选项卡可从Marketo生成OData，我们将在未来的博客帖子中讨论。
1. 如果要检查Marketo架构是否公开或从UI中发出基本SQL查询，请单击“SQL测试”。
1. 单击左侧的“下载”，并为要安装的应用程序和平台选择DataDirect Cloud ODBC或JDBC驱动程序。
1. 安装DataDirect Cloud ODBC或JDBC驱动程序后，您可以将任何基于标准的应用程序连接到Marketo。

以下是使用DataDirect Cloud ODBC客户端[连接](https://www.youtube.com/watch?v=H6PHra56Iig)的视频示例。 以下是适用于Marketo的其他DataDirect Cloud教程：

* [SAP数据分析](http://scn.sap.com/community/lumira/blog/2015/08/05/connect-sap-lumira-to-eloqua-marketo-google-analytics)
* [Microstrategy Enterprise报告](https://community.microstrategy.com/t5/Tech-Corner/What-MSTR-developers-should-know-about-Cloud-Data-Sources/ba-p/253083)
* [Oracle数据集成](https://www.ateam-oracle.com/post/a-universal-cloud-applications-adapter-for-odi/)

**跨云源(如Marketo)构建SQL连接的研发挑战**

并非所有SaaS API都公开标准查询语言。 在这些情况下，工程团队会分别查看每个对象。 每个对象可以通过不同的API公开，这些API具有用于调用、搜索过滤等的唯一规则。 它需要付出巨大的努力来提供跨整个数据模型的标准体验查询。

处理完全连接功能。 如果SaaS API不支持具有JOIN功能的查询语言，则工程团队必须执行该操作。 这要求在SQL中进行转换，以便有效地调用Marketo API，从而在执行连接之前返回最小的数据量。 当连接两个非常大的对象时，数据访问层可能会占用应用服务器或桌面上的大量资源。 因此，将数据访问层部署到弹性云服务（如DataDirect Cloud）很有意义，原因有二：

1. 提高性能，减少客户端应用程序服务器或桌面上的内存/CPU资源
1. 利用DataDirect Cloud和Marketo之间卓越的带宽，可在其中交换预连接的数据集。

如何处理数据模型？ 是静态还是动态？ 如何检测到更改并将其传达给客户端？ 每个SaaS数据源各不相同，在Marketo中，某些对象可通过视图进行查询，而其他对象则可通过表进行查询。 处理跨越所有SaaS源的数据模型和对象的矩阵无疑是一项挑战。

**开发人员的Marketo和DataDirect云参考：**

* Marketo连接属性（[链接到文档](https://www.progress.com/output/DataDirect/DataDirectCloud/index.html)）
* 支持Marketo的SQL和扩展（[链接到文档](https://www.progress.com/output/DataDirect/DataDirectCloud/index.html,-hubspot,-and-marketo.html)）
* 显示Marketo表和视图（[链接到文档](https://www.progress.com/output/DataDirect/DataDirectCloud/index.html)）
* 从Marketo返回的常见错误消息（[指向文档的链接](https://www.progress.com/output/DataDirect/DataDirectCloud/index.html)）

**个人简历：** Sumit Sarkar是Progress的首席数据宣传员，在数据连接领域拥有10多年的工作经验。 Sumit是世界一流关于开放数据标准与云数据连接的顾问，他的兴趣包括数据访问层的性能调整（他已经为数据访问层开发了一项正在申请专利的技术用于其分析）；SaaS平台的商业智能和数据仓库；以及PaaS环境的数据连接，重点关注ODBC、JDBC、ADO.NET和ODATA等标准。 彼为IBM Cognos Business Intelligence之IBM认证顾问及TDWI会员。 他在各种会议上介绍了关于数据连接的讲座，包括Dreamforce、Oracle OpenWorld、Strata Hadoop、MongoDB World和SAP Analytics and Business Objects Conference等等。 Twitter：@SAsInSumit LinkedIn： [www.linkedin.com/in/meetsumit](http://www.linkedin.com/in/meetsumit)

由&#x200B;_Kenny_&#x200B;发布于&#x200B;_2015-12-07_

## 为API使用和错误计数创建功能板

作为Marketo API消费者，这是您应留意的有用信息。 如果您可以获取历史使用数据以帮助检测随时间变化的趋势，该怎么办？ 如果您可以获取API错误代码的摘要来帮助衡量集成的运行状况，该怎么办？ 作为Marketo技术合作伙伴，如果您可以在一个仪表板中获取所有客户帐户的使用情况和错误数据，该怎么办？ 该员额将提供回答上述问题的办法。 系好安全带，开始！
**计划统计信息检索作业**&#x200B;让我们创建使用[获取每日使用情况](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getLast7DaysErrorsUsingGET)和[获取每日错误](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getDailyErrorsUsingGET)端点来检索使用情况和错误数据的应用程序。 该应用程序计划每天运行一次。 每次应用程序运行时，它都会将一天使用情况数据附加到另一个文件，并将一天错误数据附加到另一个文件。 在每个月初，将创建一对新文件。 这些文件将用作我们随时可以访问的历史记录。 以下是应用程序逻辑……

* 从外部源读取Marketo帐户信息(Munchkin id和客户端凭据)。 注意：此源必须安全，以防止他人访问帐户数据。
* 逐一查看每个帐户，然后……
   * 调用“获取每日使用情况”以检索一天的使用情况数据
   * 将每日使用情况数据附加到每月的“使用情况”文件
   * 致电获取每日错误以检索一天的错误数据
   * 将每日错误数据附加到每月的“错误”文件

输出文件格式输出文件的格式为JSON，与从各个API调用（用法和错误）返回的“结果”数组匹配。 “result”数组的每个元素都是一个JSON对象，其中包含一天的数据。 输出文件命名输出文件的命名如下：

`<type>_<yyyy>_<mm>_<account>.json`

其中，

`<type>` — 数据类型（“使用情况”或“错误”） `<yyyy>` — 年份（4位） `<mm>` — 月份（2位） `<account>` — 帐户ID (Munchkin ID)

输出文件示例usage_2015_10_111-AAA-222.json

```json
[
    { "date": "2015-10-15", "total": 0, "users" : [] },
    { "date": "2015-10-16", "total": 9, "users": [ { "userId": "some.body@yahoo.com", "count": 9 } ] },
    { "date": "2015-10-17", "total": 1120, "users": [ { "userId": "some.body@yahoo.com", "count": 200 }, { "userId": "some.body@marketo.com", "count": 200 }, { "userId": "some.body@gmail.com", "count": 720 } ] },
]
```

`errors_2015_10_111-AAA-222.json:`

```json
[
    { "date": "2015-10-15", "total":80, "errors": [ { "errorCode":"1003", "count":80 } ] },
    { "date": "2015-10-16", "total":148, "errors": [ { "errorCode":"612", "count":40 }, { "errorCode":"609", "count":70 }, { "errorCode":"1008", "count":38 } ] },
    { "date": "2015-10-17", "total":73, "errors": [ { "errorCode":"604", "count":1 }, { "errorCode":"609", "count":56 }, { "errorCode":"610", "count":16 } ] },
]
```

此应用程序的代码将在本帖子的末尾显示(Stats.java)。 **统计Web服务**。因此，现在我们需要一种方法将此数据导入我们的浏览器。 建议创建一个Web服务来交付数据。 Web服务将使用应用程序的输出文件，然后以易于呈现的格式将数据返回到浏览器。 为了简单起见，并绕过同域策略限制，我们将利用[JSONP](https://en.wikipedia.org/wiki/JSONP)模式。 以下是外部Web服务的建议的REST终结点规范： **URI**： /stats **方法**： GET

**参数**

**描述**

**示例**

月

检索本月的数据。 要包含的月份列表（以逗号分隔）（两位表示）。 默认为所有月份。

10,11

年

检索今年的数据。 要包含的年份的逗号分隔列表（4位数表示法）。 默认使用所有年份。

2015

account

检索此帐户的数据(Munchkin id)。

111-AAA-222

callback

用于包装JSON内容的函数的名称。

processStats

示例请求

`GET //localhost:8080/stats?month=10&year=2015&account=111-AAA-222&callback=processStats`

Web服务读取“使用情况”和“错误”文件，并将它们组合在一起并以此格式返回：

```html
**<Name of Callback here>**

<Contents of Usage file here>,

<Contents of Error file here>
```

这是带有2个参数的JSONP回调。 第一个参数是用法“结果”数组。 第二个参数是错误“结果”数组。 示例响应

```json
processStats(
    [
        { "date": "2015-10-15", "total": 0, "users" : [] },
        { "date": "2015-10-16", "total": 9, "users": [ { "userId": "some.body@yahoo.com", "count": 9 } ] },
        { "date": "2015-10-17", "total": 1120, "users": [ { "userId": "some.body@yahoo.com", "count": 200 }, { "userId": "some.body@marketo.com", "count": 200 }, { "userId": "some.body@gmail.com", "count": 720 } ] }
    ],
    [
        { "date": "2015-10-15", "total":80, "errors": [ { "errorCode":"1003", "count":80 } ] },
        { "date": "2015-10-16", "total":148, "errors": [ { "errorCode":"612", "count":40 }, { "errorCode":"609", "count":70 }, { "errorCode":"1008", "count":38 } ] },
        { "date": "2015-10-17", "total":73, "errors": [ { "errorCode":"604", "count":1 }, { "errorCode":"609", "count":56 }, { "errorCode":"610", "count":16 } ] },
    ]
);
```

如您所见，Web服务只是简单地包装了应用程序中的两个输出文件的内容。 我们已使用[Mocky](https://designer.mocky.io)创建模拟Web服务响应。 此处是模拟的Web服务的示例[。](http://www.mocky.io/v2/5627b2f9270000f2226eec63?month=10&year=2015&account=111-AAA-222&callback=processStats)创建此Web服务的工作留给读者来做：**仪表板网页**。现在我们只需要一个网页，它调用我们的Web服务并格式化数据。 要使用JSONP模式，我们只需要添加调用Web服务的`<script>`标记：

`<script src="http: //<hostname>/stats?month=10&year=2015&account=284-RPR-133&callback=processStats"></script>`

这会将Web服务响应正文直接插入HTML页面。 然后，我们添加JSONP回调函数：

```javascript
function processStats(usage, errors) {
    var cfg = { maxDepth: 5};
    document.write("<h2>Usage</h2>");
    document.body.appendChild(prettyPrint(usage, cfg));
    document.write("<h2>Errors</h2>");
    document.body.appendChild(prettyPrint(errors, cfg));
;
```

此函数在Web服务调用后自动调用。 在此示例中，我们在每个数组上调用一个名为[prettyPrint.js](https://github.com/padolsey-archive/prettyprint.js/tree/master)的简单JavaScript“变量转储器”。 prettyPrint函数仅会使用数组的内容生成一个HTML表。 这是HTML表格的屏幕截图。 薇拉，那是我们的仪表板！ 当然，这并不十分优雅，但它应该让你了解什么是可能的。 没有什么可以阻止您以自己喜欢的方式转换数据，以便制作引人注目的可视化图表。 HTML页面位于下方(Index.html)。您可以使用以下步骤在浏览器中实时查看上述表：

1. 保存Index.html的本地副本
1. 保存prettyPrint.js的本地副本
1. 在浏览器中打开Index.html

就这样了。 希望这篇帖子为您提供了有关监控Marketo API统计信息的一些想法。 快乐的编程！ **Stats.java**

```java
package com.marketo;

// minimal-json library (https://github.com/ralfstx/minimal-json)
import com.eclipsesource.json.JsonArray;
import com.eclipsesource.json.JsonObject;

import java.io.\*;
import java.lang.reflect.Array;
import java.net.MalformedURLException;
import java.net.URL;
import java.util.\*;

import java.nio.file.Files;
import java.nio.file.Paths;

import javax.net.ssl.HttpsURLConnection;

public class Stats {
    //
    // Define Marketo instance meta data here. Each row contains three elements: Account Id, Client Id, Client Secret.
    //
    // Note: that this information would typically be stored read from an external source (i.e. database)
    //
    // For example:
    //
    // private static String final CUSTOM_SERVICE_DATA[][] = {
    // {"111-AAA-222", "2f4a4435-f6fa-4bd9-3248-098754982345", "asdf6IVE9h4Jjcl59cOMAKFSk78ut12W"},
    // {"222-BBB-333", "5f4a6657-f6fa-4cd9-4356-123083238821", "gfjgfIVE9h4Jjcl59cOMAKFSk78ut12W"},
    // {"444-CCC-444", "9f4a4678-f6fa-4dd9-7735-908713247721", "xzcxvbVE9h4Jjcl59cOMAKFSk78ut12W"}
    // };
    //
    private static final String CUSTOM_SERVICE_DATA[][] = {
    };

    // Output directory for stats files
    private static final String OUTPUT_DIR = "C:stats";

public static void main(String[] args) {

    // Loop through each Marketo instance
    for (int i = 0; i < CUSTOM_SERVICE_DATA.length; i++) {

        // Compose base URL
        String baseUrl = String.format("https://%s.mktorest.com",
            CUSTOM_SERVICE_DATA[i][0]);

        // Compose Identity URL
        String identityUrl = String.format("%s/identity/oauth/token?grant_type=%s&client_id=%s&client_secret=%s",
            baseUrl, "client_credentials", CUSTOM_SERVICE_DATA[i][1], CUSTOM_SERVICE_DATA[i][2]);

        // Call Identity API
        JsonObject identityObj = JsonObject.readFrom(httpRequest("GET", identityUrl, null));
        String accessToken = identityObj.get("access_token").asString();

        // Compose Get Last 7 Days Usage URL
        String usageUrl = String.format("%s/rest/v1/stats/usage/last7days.json?access_token=%s",
            baseUrl, accessToken);

        // Compose Get Last 7 Days Errors URL
        String errorsUrl = String.format("%s/rest/v1/stats/errors/last7days.json?access_token=%s",
            baseUrl, accessToken);

        // Process usage data
        JsonObject usageObj = JsonObject.readFrom(httpRequest("GET", usageUrl, null));
        if (usageObj.get("success").asBoolean()) {
            if (usageObj.get("result") != null) {
                updateFile(usageObj, "usage", CUSTOM_SERVICE_DATA[i][0]);
            }
        }

        // Process errors data
        JsonObject errorsObj = JsonObject.readFrom(httpRequest("GET", errorsUrl, null));
        if (usageObj.get("success").asBoolean()) {
            if (errorsObj.get("result") != null) {
                updateFile(errorsObj, "errors", CUSTOM_SERVICE_DATA[i][0]);
            }
        }
    }
    System.exit(0);
}

// Write yesterday's data to output file
private static void updateFile(JsonObject usageObj, String statsType, String account){

    JsonArray usageResultAry = usageObj.get("result").asArray();
    JsonObject yesterdayUsageResultObj = usageResultAry.get(1).asObject();

    String yesterdayDate = yesterdayUsageResultObj.get("date").asString();
    String[] yesterdayDateAry = yesterdayDate.split("[-]+");

    // "C:statsstats_yyyy_mm_account.json"
    String statsFile = String.format("%s%s_%s_%s_%s.json",
        OUTPUT_DIR, statsType, yesterdayDateAry[0], yesterdayDateAry[1], account);

    // Create file
    File file = new File(statsFile);
    try {
        if (file.createNewFile()) {
            // created new file, seed with empty array
            FileWriter fw = new FileWriter(file.getAbsoluteFile());
            BufferedWriter bw = new BufferedWriter(fw);
            bw.write("[n]");
            bw.close();
        }
    } catch (IOException e) {
        e.printStackTrace();
    }

    // Read file
    String content = null;
    try {
        content = new String(Files.readAllBytes(Paths.get(statsFile)));
    } catch (IOException e) {
        e.printStackTrace();
    }

    // Remove trailing "]", append new record, append trailing "]"
    content = content.substring(0, content.length() - 1);
    content += yesterdayUsageResultObj.toString();
    content += "n]";

    // Write file
    FileWriter fw = null;
    try {
        fw = new FileWriter(file.getAbsoluteFile());
        BufferedWriter bw = new BufferedWriter(fw);
        bw.write(content);
        bw.close();
    } catch (IOException e) {
        e.printStackTrace();
    }
}

// Perform HTTP request
private static String httpRequest(String method, String endpoint, String body) {
    String data = "";
    try {
        URL url = new URL(endpoint);
        HttpsURLConnection urlConn = (HttpsURLConnection) url.openConnection();
        urlConn.setDoOutput(true);
        urlConn.setRequestMethod(method);
        switch (method) {
            case "GET":
                break;
            case "POST":
                urlConn.setRequestProperty("Content-type", "application/json");
                urlConn.setRequestProperty("accept", "text/json");
                OutputStreamWriter wr = new OutputStreamWriter(urlConn.getOutputStream());
                wr.write(body);
                wr.flush();
                break;
            default:
                System.out.println("Error: Invalid method.");
                return data;
        }
        int responseCode = urlConn.getResponseCode();
        if (responseCode == 200) {
            InputStream inStream = urlConn.getInputStream();
            data = convertStreamToString(inStream);
        } else {
            System.out.println(responseCode);
            data = "Status:" + responseCode;
        }
    } catch (MalformedURLException e) {
        System.out.println("URL not valid.");
    } catch (IOException e) {
        System.out.println("IOException: " + e.getMessage());
        e.printStackTrace();
    }
    return data;
}

private static String convertStreamToString(InputStream inputStream) {
    try {
        return new Scanner(inputStream).useDelimiter("A").next();
    } catch (NoSuchElementException e) {
        return "";
    }
}
}
```

**索引.html**

```html
<html>
<head>
<title>Marketo API Stats</title>
<!-- Browser JavaScript variable dumper                  -->
<!-- https://github.com/padolsey-archive/prettyprint.js  -->
<script src="prettyPrint.js"></script>
</head>
<body>

<h1>Marketo API Stats</h1>

<script>
// JSONP callback that uses prettyPrint to format API stats
function processStats(usage, errors) {
    var cfg = { maxDepth: 5};
    document.write("<h2>Usage</h2>");
    document.body.appendChild(prettyPrint(usage, cfg));
    document.write("<h2>Errors</h2>");
    document.body.appendChild(prettyPrint(errors, cfg));
};
</script>

<!-- Web service for you to implement as an exercise                                                        -->
<!-- <script src="http://localhost:8080/stats?month=10&account=111-AAA-222&callback=processStats"></script> -->

<!-- Mock web service that returns sample payload -->
<!-- http://www.mocky.io/                         -->
<script src="http://www.mocky.io/v2/5627b2f9270000f2226eec63?month=10&year=2015&account=111-AAA-222&callback=processStats"></script>"
</body>
</html>
```

由&#x200B;_David_&#x200B;发布于&#x200B;_2015-10-22_

## Marketo REST API异常和错误处理：第3部分

在大多数情况下，从Marketo REST API收到的错误将不会自动恢复。 但是，在少数情况下，您可以自动恢复，或确保不会看到特定类型的错误。

### 请求大小错误

正如我们在本系列的最后一篇文章中所看到的，如果您的URI长度超过8KiB，Marketo将发出HTTP状态代码414；如果您的请求正文超过1MB，则将发出413；如果导入商机超过10MB，则将发出HTTP状态代码414。 尽管414非常少见，但如果您使用“按过滤器获取潜在客户”类型来根据300个单独的GUID或类似标准请求记录，则您可能会看到这些情况。 假设您有以下请求： `<https://AAA-BBB-CCC.mktorest.com/rest/v1/leads.json?filterType=customGUID&fields=email`， company...firstName， lastName>当您提交请求时，Marketo返回状态414，因为URI超过8KiB。

要解决此问题，我们需要更改此请求的模式，提交POST而不是GET，将“_method=GET”附加到URI，并将请求正文中的查询字符串作为x-www-form-urlencoded请求传递： URI： `<https://AAA-BBB-CCC.mktorest.com/rest/v1/leads.json?_method=GET>`请求正文： filterType=customGUID&amp;fields=email，company...firstName， lastName不要从HTTP响应中捕获此异常，但是，我们只需在运行时检查请求的总长度，并在URI超过8k时部署此替代模式。 或者，您可以在所有情况下使用POST方法批量检索记录。 对于413s，我们可以遵循类似的模式，在序列化步骤中添加记录时检查请求正文的长度，如果超过此限制，则将请求拆分为多个部分。

### 身份验证错误

我们下一类可恢复的错误与身份验证相关。 当在expires_in期限过后使用以前有效的访问令牌时，第一次使用将返回错误代码602，“访问令牌已过期”。 之后，使用同一令牌将返回601，“Access token invalid”（访问令牌无效）。 如果对字符串的任何其他使用不是目标订阅的有效访问令牌，则会导致601。 在这两种情况下，都可以通过重新身份验证并通过重试失败的请求传递新访问令牌来从恢复此错误。

### 超时

在极少数情况下，调用可能会在30秒超时后返回604“请求超时”。 对于已分批的请求（如创建/更新潜在客户），可以将请求拆分为较小的批并重试，直到返回成功（如果已将批拆分为少于100条记录且请求仍然超时，则您可能应该提交支持案例）。 最常见的其他情况是资产审批调用，在这种情况下，其他用户或服务可能会对当前批准的记录持有锁定，例如[电子邮件](https://developer.adobe.com/marketo-apis/api/asset/approve-email-by-id/)或[电子邮件模板](https://developer.adobe.com/marketo-apis/api/asset/approve-email-template-by-id/)的情况。 在这些情况下，应使用[指数回退](https://en.wikipedia.org/wiki/Exponential_backoff)进行重试，以允许解决任何现有的锁定。 请在接下来的几周内再次查看本系列的最后部分，我们将更详细地了解一些特定的、无法恢复的错误。

由&#x200B;_Kenny_&#x200B;发布于&#x200B;_2015-10-30_

## Marketo安全增强功能

在Marketo，我们非常重视安全问题。 作为&#x200B;**[行业计划](https://security.googleblog.com/2014/09/gradually-sunsetting-sha-1.html)**&#x200B;的一部分，Marketo正在升级Web身份验证和加密，以增强我们的安全保护。 计划于2011年12月12日推出。 **谁将受影响？**&#x200B;只有少数用户会受到影响，仅限那些从已使用10年以上且最近未更新的系统集成到Marketo的用户。 有关支持的系统和版本的更多信息，请参阅&#x200B;**[此列表](https://www.digicert.com/faq/sha2/sha-2-compatibility)**。 **以下用户不会受到影响：**

* 最终用户通过新式浏览器访问Marketo.com （[请参阅列表](https://www.digicert.com/faq/sha2/sha-2-compatibility)）
* 使用Informatica、Dell Boomi和Scribe等集成合作伙伴的客户。
* 使用Launchpoint合作伙伴的客户。

所有其他Marketo域均已使用SHA2证书。

由&#x200B;_Kenny_&#x200B;发布于&#x200B;_2015-11-18_

## 使用REST API轮询活动

活动是Marketo平台中的核心对象。 活动是存储的有关每次网页访问、电子邮件打开、网络研讨会出席或展会出席情况的行为数据。 一个常见用例是将活动数据与来自组织其他部分的数据相结合。 此示例程序包含6个步骤：

1. 调用[获取潜在客户活动](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getLeadActivitiesUsingGET)以生成在给定日期/时间创建的所有活动记录的列表。 我们使用过滤器来限制返回的活动记录类型。
1. 从每个活动记录中提取感兴趣的字段。
1. 调用[按筛选器类型获取多个潜在客户](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getLeadsByFilterUsingGET)，以生成与步骤1中的活动对应的潜在客户记录列表。 我们使用在步骤2中从活动记录中提取的leadId字段作为过滤器来指定返回哪些潜在客户。
1. 从每个潜在客户记录中提取感兴趣的字段。
1. 使用步骤4中的潜在客户数据加入步骤2中的活动数据。
1. 将Step 5中的数据转换为外部系统可使用的格式。

如下图所示，在本例中，我们选择了捕获与电子邮件相关的活动。

**程序输入**&#x200B;默认情况下，程序会从当前日期开始的一天后回溯以查找更改。 例如，您可以每天在同一时间运行该程序。 要更早地返回到过去，可以将天数指定为命令行参数，从而有效地增加时间窗口。 该程序包含多个可修改的变量：CUSTOM_SERVICE_DATA — 这包含您的Marketo自定义服务数据（帐户ID、客户端ID、客户端密码）。 READ_BATCH_SIZE — 这是一次要检索的记录数。 使用此选项可针对主体大小调整响应。 LEAD_FIELDS — 其中包含我们要收集的潜在客户字段列表。 ACTIVITY_TYPES — 其中包含我们要收集的活动类型列表。

**程序逻辑**&#x200B;我们首先建立时间窗口，编写REST端点URL，并获取身份验证访问令牌。 接下来，我们启动一个Get Paging Token/Get Lead Activities循环，该循环将一直运行，直到我们耗尽活动的供给。 此循环的目的是检索活动记录，并从这些记录中提取感兴趣的字段。 我们让“获取潜在客户活动”只查找以下活动类型：

* 电子邮件已送达
* 取消订阅电子邮件
* 打开电子邮件
* 单击电子邮件。

我们从每个活动记录中提取以下感兴趣的字段：

* leadId
* activitytype
* activitydate
* primaryAttribute值

您可以根据自己的需要，自由选择活动类型和活动字段的任意组合。 接下来，我们启动一个“通过过滤类型获取多个导线”循环，该循环将一直运行，直到我们耗尽导线的供应。 请注意，我们使用“filterType=id”参数与一系列“filterValues”参数组合，将检索到的潜在客户记录限制为仅与我们之前检索到的活动关联的那些潜在客户。 我们从每个潜在客户记录中提取以下感兴趣的字段：

* firstName
* 姓氏
* 电子邮件

同样，您可以选择喜欢的任何潜在客户字段。 接下来，我们将使用潜在客户ID将潜在客户字段与活动字段链接在一起。 最后，我们会循环处理所有数据，将其转换为JSON格式，然后打印到控制台。 **程序输出**&#x200B;以下是示例程序的输出示例。 这会显示活动字段和潜在客户字段组合为JSON“结果”对象。 这里的想法是，您可以将此JSON作为请求有效负载传递到外部Web服务。

```json
{
    "result": [
        {
            "leadId": 318581,
            "activityType": "Email Delivered",
            "activityDate": "2015-03-17T20:00:06Z",
            "primaryAttributeValue": "My Email Program",
            "firstName": "David",
            "lastName": "Everly",
            "email": "everlyd@marketo.com"
        },
        {
            "leadId":318581,
            "activityType":"Open Email",
            "activityDate":"2015-03-17T23:23:12Z",
            "primaryAttributeValue":"My Email Program - Auto Response",
            "firstName":"David",
            "lastName":"Everly",
            "email":"everlyd@marketo.com"
       },
        ... more result objects here...
    ]
}
```

**程序代码**

```java
package com.marketo;

// minimal-json library (https://github.com/ralfstx/minimal-json)
import com.eclipsesource.json.JsonArray;
import com.eclipsesource.json.JsonObject;
import com.eclipsesource.json.JsonValue;

import java.io.\*;
import java.net.MalformedURLException;
import java.net.URL;
import java.text.DateFormat;
import java.text.ParseException;
import java.text.SimpleDateFormat;
import java.util.\*;

import javax.net.ssl.HttpsURLConnection;

public class LeadActivities {
//
// Define Marketo REST API access credentials: Account Id, Client Id, Client Secret.  For example:
    private static Map<String, String> CUSTOM_SERVICE_DATA;
    static {
        CUSTOM_SERVICE_DATA = new HashMap<String, String>();
//        CUSTOM_SERVICE_DATA.put("accountId", "111-AAA-222");
//        CUSTOM_SERVICE_DATA.put("clientId", "2f4a4435-f6fa-4bd9-3248-098754982345");
//        CUSTOM_SERVICE_DATA.put("clientSecret", "asdf6IVE9h4Jjcl59cOMAKFSk78ut12W");
    }

    // Number of lead records to read at a time
    private static final String READ_BATCH_SIZE = "200";

    // Lookup lead records using lead id as primary key
    private static final String LEAD_FILTER_TYPE = "id";

    // Lead record lookup returns these fields
    private static final String LEAD_FIELDS = "firstName,lastName,email";

    // Lookup activity records for these activity types
    private static Map<Integer, String> ACTIVITY_TYPES;
    static {
        ACTIVITY_TYPES = new HashMap<Integer, String>();
        ACTIVITY_TYPES.put(7, "Email Delivered");
        ACTIVITY_TYPES.put(9, "Unsubscribe Email");
        ACTIVITY_TYPES.put(10, "Open Email");
        ACTIVITY_TYPES.put(11, "Click Email");
    }

    public static void main(String[] args) {
        // Command line argument to set how far back to look for lead changes (number of days)
        int lookBackNumDays = 1;
        if (args.length == 1) {
            lookBackNumDays = Integer.parseInt(args[0]);
        }

        // Establish "since date" using current timestamp minus some number of days (default is 1 day)
        Calendar cal = Calendar.getInstance();
        cal.add(Calendar.DAY_OF_MONTH, -lookBackNumDays);
        SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd'T'HH:mm:ssZ");
        String sinceDateTime = sdf.format(cal.getTime());

        // Compose base URL
        String baseUrl = String.format("https://%s.mktorest.com",
            CUSTOM_SERVICE_DATA.get("accountId"));

        // Compose Identity URL
        String identityUrl = String.format("%s/identity/oauth/token?grant_type=%s&client_id=%s&client_secret=%s",
                baseUrl, "client_credentials", CUSTOM_SERVICE_DATA.get("clientId"), CUSTOM_SERVICE_DATA.get("clientSecret"));

        // Call Identity API
        JsonObject identityObj = JsonObject.readFrom(getData(identityUrl));
        String accessToken = identityObj.get("access_token").asString();

        // Compose URLs for Get Lead Changes, and Get Paging Token
        String activityTypesUrl = String.format("%s/rest/v1/activities/types.json?access_token=%s",
                baseUrl, accessToken);
        String pagingTokenUrl = String.format("%s/rest/v1/activities/pagingtoken.json?access_token=%s&sinceDatetime=%s",
                baseUrl, accessToken, sinceDateTime);

        // Use activity ids to create filter parameter
        String activityTypeIds = "";
        for (Integer id : ACTIVITY_TYPES.keySet()) {
            activityTypeIds += "&activityTypeIds=" + id.toString();
        }

        // Compose URL for Get Lead Activities
        // Only retrieve activities that match our defined activity types
        String leadActivitiesUrl = String.format("%s/rest/v1/activities.json?access_token=%s%s&batchSize=%s",
                baseUrl, accessToken, activityTypeIds, READ_BATCH_SIZE);

        Map<Integer, List> activitiesMap = new HashMap<Integer, List>();
        Set leadsSet = new HashSet();

        // Call Get Paging Token API
        JsonObject pagingTokenObj = JsonObject.readFrom(getData(pagingTokenUrl));
        if (pagingTokenObj.get("success").asBoolean()) {
            String nextPageToken = pagingTokenObj.get("nextPageToken").asString();
            boolean moreResult;
            do {
                moreResult = false;

                // Call Get Lead Activities API to retrieve activity data
                JsonObject leadActivitiesObj = JsonObject.readFrom(getData(String.format("%s&nextPageToken=%s",
                        leadActivitiesUrl, nextPageToken)));
                if (leadActivitiesObj.get("success").asBoolean()) {
                    moreResult = leadActivitiesObj.get("moreResult").asBoolean();
                    nextPageToken = leadActivitiesObj.get("nextPageToken").asString();

                    if (leadActivitiesObj.get("result") != null) {
                        JsonArray activitiesResultAry = leadActivitiesObj.get("result").asArray();
                        for (JsonValue activitiesResultObj : activitiesResultAry) {

                            // Extract activity fields of interest (leadID, activityType, activityDate, primaryAttributeValue)
                            JsonObject leadObj = new JsonObject();
                            int leadId = activitiesResultObj.asObject().get("leadId").asInt();
                            leadObj.add("activityType", ACTIVITY_TYPES.get(activitiesResultObj.asObject().get("activityTypeId").asInt()));
                            leadObj.add("activityDate", activitiesResultObj.asObject().get("activityDate").asString());
                            leadObj.add("primaryAttributeValue", activitiesResultObj.asObject().get("primaryAttributeValue").asString());

                            // Store JSON containing activity fields in a map using lead id as key
                            List leadLst = activitiesMap.get(leadId);
                            if (leadLst == null) {
                                activitiesMap.put(leadId, new ArrayList());
                                leadLst = activitiesMap.get(leadId);
                            }
                            leadLst.add(leadObj);

                            // Store unique lead ids for use as lead filter value below
                            leadsSet.add(leadId);
                        }
                    }
                }
            } while (moreResult);
        }

        // Use unique lead id values to create filter parameter
        String filterValues = "";
        for (Object object : leadsSet) {
            if (filterValues.length() > 0) {
                filterValues += ",";
            }
            filterValues += String.format("%s", object);
        }

        // Compose Get Multiple Leads by Filter Type URL
        // Only retrieve leads that match the list of lead ids that was accumulated during activity query
        String getMultipleLeadsUrl = String.format("%s/rest/v1/leads.json?access_token=%s&filterType=%s&fields=%s&filterValues=%s&batchSize=%s",
                baseUrl, accessToken, LEAD_FILTER_TYPE, LEAD_FIELDS, filterValues, READ_BATCH_SIZE);
        String nextPageToken = "";

        do {
            String gmlUrl = getMultipleLeadsUrl;

            // Append paging token to Get Multiple Leads by Filter Type URL
            if (nextPageToken.length() > 0) {
                gmlUrl = String.format("%s&nextPageToken=%s", getMultipleLeadsUrl, nextPageToken);
            }

            // Call Get Multiple Leads by Filter Type API to retrieve lead data
            JsonObject multipleLeadsObj = JsonObject.readFrom(getData(gmlUrl));
            if (multipleLeadsObj.get("success").asBoolean()) {
                if (multipleLeadsObj.get("result") != null) {
                    JsonArray multipleLeadsResultAry = multipleLeadsObj.get("result").asArray();

                    // Iterate through lead data
                    for (JsonValue leadResultObj : multipleLeadsResultAry) {
                        int leadId = leadResultObj.asObject().get("id").asInt();

                        // Join activity data with lead fields of interest (firstName, lastName, email)
                        List leadLst = activitiesMap.get(leadId);
                        for (JsonObject leadObj : leadLst) {
                            leadObj.add("firstName", leadResultObj.asObject().get("firstName").asString());
                            leadObj.add("lastName", leadResultObj.asObject().get("lastName").asString());
                            leadObj.add("email", leadResultObj.asObject().get("email").asString());
                        }
                    }
                }
            }

            nextPageToken = "";
            if (multipleLeadsObj.asObject().get("nextPageToken") != null) {
                nextPageToken = multipleLeadsObj.get("nextPageToken").asString();
            }
        } while (nextPageToken.length() > 0);

        // Now place activity data into an array of JSON objects
        JsonArray activitiesAry = new JsonArray();
        for (Map.Entry<Integer, List> activity : activitiesMap.entrySet()) {
            int leadId = activity.getKey();
            for (JsonObject leadObj : activity.getValue()) {
                // do something with leadId and each leadObj
                leadObj.add("leadId", leadId);
                activitiesAry.add(leadObj);
            }
        }

        // Print out result objects
        JsonObject result = new JsonObject();
        result.add("result", activitiesAry);
        System.out.println(result);

        System.exit(0);
    }

    // Perform HTTP GET request
    private static String getData(String endpoint) {
        String data = "";
        try {
            URL url = new URL(endpoint);
            HttpsURLConnection urlConn = (HttpsURLConnection) url.openConnection();
            urlConn.setRequestMethod("GET");
            urlConn.setAllowUserInteraction(false);
            urlConn.setDoOutput(true);
            int responseCode = urlConn.getResponseCode();
            if (responseCode == 200) {
                InputStream inStream = urlConn.getInputStream();
                data = convertStreamToString(inStream);
            } else {
                System.out.println(responseCode);
                data = "Status:" + responseCode;
            }
        } catch (MalformedURLException e) {
            System.out.println("URL not valid.");
        } catch (IOException e) {
            System.out.println("IOException: " + e.getMessage());
            e.printStackTrace();
        }
        return data;
    }

    private static String convertStreamToString(InputStream inputStream) {
        try {
            return new Scanner(inputStream).useDelimiter("A").next();
        } catch (NoSuchElementException e) {
            return "";
        }
    }
}
```

就是这样。 快乐的编程！

由&#x200B;_David_&#x200B;发布于&#x200B;_2015-11-20_

## 将SoundCloud播放器与Munchkin API集成

SoundCloud提供了一个令人难以置信的音频托管平台，其丰富的分析和功能适用于所有内容，从志向远大的独立摇滚艺人，到音乐行业顶级的EDM艺人，以及storytelling播客。 除了该平台令人难以置信的原生功能外，还配备了世界一流的API程序，用于移动数据和跟踪收听行为。 这对于播客特别有用，并且可以让您将特定的收听操作（如播放、暂停和共享）与脚本和音频中的特定内容相关联。 今天，我们将了解如何利用[SoundCloud的小部件API](https://developers.soundcloud.com/docs/api/html5-widget)在Marketo中发送和跟踪这些活动。 首先，我们来看一下如何生成Munchkin活动，该活动将被记录到商机的活动登录Marketo中。 在最基本的层面上，我们调用Munchkin.munchkinFunction，并将“visitWebPage”作为第一个参数传递。 这将使用Marketo记录“访问网页”活动，并记录我们传递到方法的任何任意URL和查询字符串数据。 第二个参数接受包含我们数据的JavaScript对象，其中有两个成员，“url”和“params”，均为字符串。 url成员对应于Marketo中的活动网页，而params对应于Querystring。 出于我们的目的，我们将使用url作为SoundCloud相关操作“soundCloudInteraction”的标识符，而参数将包含有关特定活动的其他数据。 以下是我们用于跟踪每个操作的函数：

```javascript
var trackActivity = function(action){

    //set action param to be the string passed to the function
    var qs = "action=" + action;

    //use getCurrentSound callback to get the name of the current track
    soundCloudMunchkin.widget.getCurrentSound(function(currentSound){

        //add it to our querystring
        qs = qs + "&sound=" + currentSound.title;

        //use the getPosition callback to get the position of the track in ms
        soundCloudMunchkin.widget.getPosition(function(position){

            //add it to the querystring
            qs = qs + "&position=" + position;

            //assemble our data object for the munchkin activity
            var dataObject = {
                "url": "soundCloudInteraction",
                "params": qs
            }

            //call the munchkinFunction to submit the activity
            Munchkin.munchkinFunction("visitWebPage", dataObject);
        });
    });
}
```

由于标准SoundCloud构件嵌入在iframe中，因此该构件会使用post消息进行通信，并且需要使用回调来获取数据，如您在currentSound和getPosition方法中所看到的。 SoundCloud Widget API提供了一组JavaScript回调，我们可以使用这些回调响应播放器中的单个事件并将它们提交到Marketo。 我们感兴趣的事件是用户侦听的内容、用户侦听的时间长短，以及用户与播放器进行的交互，因此我们查看的是以下事件：

* 播放
* 暂停
* 完成
* 搜寻
* CLICK_DOWN
* CLICK_BUY
* OPEN_SHARE_PANEL

我们还需要使用小部件中的bind()方法向其中每个事件添加回调。 我们来看一个示例：

```javascript
widget.bind(SC.Widget.Events.PLAY, function(){
    soundCloudMunchkin.trackPlay();
});
```

这样一来，每当播放某个跟踪时，我们都会触发trackPlay方法，向Marketo发送一个包含有关当前跟踪的数据的事件。 您可以在[此处](https://gist.github.com/kelkingtron/6750bb07c1397d93d9c7#file-soundcloudmunchkin-js)找到完整的脚本。 soundCloudMunchkin对象具有init方法，该方法接受SoundCloud小组件对象作为其唯一参数，它将跟踪方法绑定到相关回调，并将设置小组件以跟踪到Marketo的活动。 您的页面需要加载[Munchkin代码](/help/javascript-api/lead-tracking.md)以及[SoundCloud API库](https://w.soundcloud.com/player/api.js)。 除了嵌入实际的SoundCloud小组件之外，您还需要初始化所有内容：

```javascript
window.onload=function(){
var iframe = document.getElementById(iframeId);
    if(iframe) {
        widget = SC.Widget(iframe);
        soundCloudMunchkin.init(widget);
    };
};
```

由&#x200B;_Kenny_&#x200B;发布于&#x200B;_2015-12-21_

## RTP和欧盟电子隐私指令

本文说明如何使用RTP通知网站访客他们被跟踪或自动禁用欧洲访客的跟踪。 自2012年以来，欧洲访客访问的任何网站都必须遵守欧盟电子隐私指令。 2011年生效的新法律禁止在用户不知情或未同意的情况下将身份识别信息存储在用户计算机上。 如果您使用Cookie或任何其他技术进行非必要跟踪，则必须：

1. 告知用户使用的是跟踪技术。
1. 解释使用这些技术的原因。
1. 在使用该技术之前获得用户的同意，并允许他们随时撤销权限。

由&#x200B;_Yanir_&#x200B;发布于&#x200B;_1970-01-01_

## 使用AP更新Marketo中的客户和潜在客户信息

在某些情况下，会使用专有系统来更新客户和潜在客户信息。 营销团队希望将这些更新反映在Marketo中，以便他们有最准确的记录系统用于营销活动。 通过使用以下方法，您可以设置定期上传到Marketo，以使用在该专有系统中修改的数据来更新Marketo联系信息。 下图显示了在设置的定期计时器上进行的API调用。 当定期计时器被触发时，客户端逻辑将首先从专有系统检索更新的联系人。 对于使用API或数据导出的系统，此操作方式会因系统而异，不同于专有系统。 我们将详细介绍在检索更新的联系人信息后执行的Marketo API。 SOAP的syncMultipleLeads请求：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<ns2:paramsSyncMultipleLeads xmlns:ns2="<http://www.marketo.com/mktows/">
  <leadRecordList>
    <leadRecord>
      <Email>henry@superstar.com</Email>
      <ns2:leadAttributeList>
        <attribute>
          <attrName>FirstName</attrName>
          <attrValue>Henry</attrValue>
        </attribute>
        <attribute>
          <attrName>LastName</attrName>
          <attrValue>Adams</attrValue>
        </attribute>
        <attribute>
          <attrName>Title</attrName>
          <attrValue>Director of Demand Generation</attrValue>
        </attribute>
      </ns2:leadAttributeList>
    </leadRecord>
    <leadRecord>
      <Email>ssmith@gmail.com</Email>
      <ns2:leadAttributeList>
        <attribute>
          <attrName>FirstName</attrName>
          <attrValue>Suzie</attrValue>
        </attribute>
        <attribute>
          <attrName>LastName</attrName>
          <attrValue>Smith</attrValue>
        </attribute>
        <attribute>
          <attrName>Title</attrName>
          <attrValue>VP Marketing</attrValue>
        </attribute>
      </ns2:leadAttributeList>
    </leadRecord>
  </leadRecordList>
  <dedupEnabled>true</dedupEnabled>
</ns2:paramsSyncMultipleLeads>
```

来自syncMultilpeLeads的SOAP响应：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<ns2:successSyncMultipleLeads xmlns:ns2="<http://www.marketo.com/mktows/">
  <result>
    <syncStatusList>
      <syncStatus>
        <leadId>1094593</leadId>
        <status>UPDATED</status>
        <error xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:nil="true" />
      </syncStatus>
      <syncStatus>
        <leadId>1094594</leadId>
        <status>UPDATED</status>
        <error xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:nil="true" />
      </syncStatus>
    </syncStatusList>
  </result>
</ns2:successSyncMultipleLeads>
```

`syncMultipleLeads`执行UPSERT操作。 如果根据提交的电子邮件地址，Marketo内的联系人已存在，则将更新属性。 如果联系人不存在，则会创建该联系人。 来自`syncMultipleLeads`的响应将返回每个提交联系人的状态。 `<attrName/>`中的`<leadAttributeList/>`值必须与为该Marketo订阅定义的SOAP API名称匹配。 您可以通过导出字段名称，在SOAP管理面板的字段管理部分中发现Marketo API名称。

请参阅以下执行上述场景的示例Java程序：

```java
 import com.marketo.mktows.\*;
import java.net.URL;
import javax.xml.namespace.QName;
import java.text.DateFormat;
import java.text.SimpleDateFormat;
import java.util.Date;
import javax.crypto.Mac;
import javax.crypto.spec.SecretKeySpec;
import org.apache.commons.codec.binary.Hex;
import javax.xml.bind.JAXBContext;
import javax.xml.bind.JAXBElement;
import javax.xml.bind.Marshaller;
import java.util.\*;

public class SyncMultipleLeadsExample {

  public static void main(String[] args) {

    try {
      URL marketoSoapEndPoint = new URL("CHANGE ME" + "?WSDL");
      String marketoUserId = "CHANGE ME";
      String marketoSecretKey = "CHANGE ME";

      QName serviceName = new QName("http://www.marketo.com/mktows/", "MktMktowsApiService");
      MktMktowsApiService service = new MktMktowsApiService(marketoSoapEndPoint, serviceName);
      MktowsPort port = service.getMktowsApiSoapPort();

      // Create Signature
      DateFormat df = new SimpleDateFormat("yyyy-MM-dd'T'HH:mm:ssZ");
      String text = df.format(new Date());
      String requestTimestamp = text.substring(0, 22) + ":" + text.substring(22);
      String encryptString = requestTimestamp + marketoUserId ;

      SecretKeySpec secretKey = new SecretKeySpec(marketoSecretKey.getBytes(), "HmacSHA1");
      Mac mac = Mac.getInstance("HmacSHA1");
      mac.init(secretKey);
      byte[] rawHmac = mac.doFinal(encryptString.getBytes());
      char[] hexChars = Hex.encodeHex(rawHmac);
      String signature = new String(hexChars);

      // Set Authentication Header
      AuthenticationHeader header = new AuthenticationHeader();
      header.setMktowsUserId(marketoUserId);
      header.setRequestTimestamp(requestTimestamp);
      header.setRequestSignature(signature);

      // Create Request
      ParamsSyncMultipleLeads request = new ParamsSyncMultipleLeads();

      ObjectFactory objectFactory = new ObjectFactory();

      JAXBElement dedup = objectFactory.createParamsSyncMultipleLeadsDedupEnabled(true);
      request.setDedupEnabled(dedup);

      // The list of contacts defined here would be retrieved from the proprietary system
      Contact contact = new Contact("Henry","Adams","henry@superstar.com", "Director of Demand Generation");
      Contact contact2 = new Contact("Suzie","Smith","ssmith@gmail.com", "VP Marketing");

      ArrayList updatedContacts = new ArrayList();
      updatedContacts.add(contact);
      updatedContacts.add(contact2);

      ArrayOfLeadRecord arrayOfLeadRecords = new ArrayOfLeadRecord();

      Iterator it = updatedContacts.iterator();
      while(it.hasNext())
      {
        Contact c = it.next();

        LeadRecord leadRec = new LeadRecord();

        JAXBElement email = objectFactory.createLeadRecordEmail(c.email);
        leadRec.setEmail(email);

        Attribute attr1 = new Attribute();
        attr1.setAttrName("FirstName");
        attr1.setAttrValue(c.fname);

        Attribute attr2 = new Attribute();
        attr2.setAttrName("LastName");
        attr2.setAttrValue(c.lname);

        Attribute attr3 = new Attribute();
        attr3.setAttrName("Title");
        attr3.setAttrValue(c.title);

        ArrayOfAttribute aoa = new ArrayOfAttribute();
        aoa.getAttributes().add(attr1);
        aoa.getAttributes().add(attr2);
        aoa.getAttributes().add(attr3);

        QName qname = new QName("http://www.marketo.com/mktows/", "leadAttributeList");
        JAXBElement attrList = new JAXBElement(qname, ArrayOfAttribute.class, aoa);

        leadRec.setLeadAttributeList(attrList);
        arrayOfLeadRecords.getLeadRecords().add(leadRec);

      }

      request.setLeadRecordList(arrayOfLeadRecords);

      JAXBContext context = JAXBContext.newInstance(SuccessSyncMultipleLeads.class);
      Marshaller m = context.createMarshaller();
      m.setProperty(Marshaller.JAXB_FORMATTED_OUTPUT, true);
      m.marshal(request, System.out);

      SuccessSyncMultipleLeads result = port.syncMultipleLeads(request, header);

      m.marshal(result, System.out);
    }

    catch(Exception e) {
      e.printStackTrace();
    }
  }

  public static class Contact {
    public String fname, lname, email, title;

      public Contact(String fname, String lname, String email, String title) {
          this.fname = fname;
          this.lname = lname;
          this.email = email;
          this.title = title;
      }
  }
}
```

本文包含用于实施自定义集成的代码。 由于其自定义性质，Marketo技术支持团队无法对自定义工作进行故障诊断。 如果没有适当的技术经验或联系经验丰富的开发人员，请勿尝试实施以下代码示例。

由&#x200B;_Travis Kaufman_&#x200B;发布于&#x200B;_2014-03-24_

## 使用API从Marketo发送事务型电子邮件

它要求使用Marketo UI创建现有的Smart Campaign。 它还需要电子邮件收件人存在于Marketo中。 因此，在调用requestCampaign API之前，请使用[getLead API]&#x200B;(/help/soap-api/getlead.md)来验证电子邮件是否存在于Marketo中。 在通过requestCampaign API进行调用后，您可以通过检查以查看Smart Campaign是否已在Marketo中运行来进行确认。 我们将向您展示如何创建智能营销活动，如何设置触发器以通过API发送营销活动，如何定义电子邮件作为流操作的一部分，以及第四个用于执行此营销活动的代码示例。
**如何在Marketo中创建新的Smart Campaign** Marketo中的Smart Campaigns执行您的所有营销活动。 您可以设置一系列对智能联系人列表执行的自动操作。 在发送事务性电子邮件时，可在营销活动中设置触发器（如下所示）以使用API发送电子邮件。 首先，让我们设置Smart Campaign。 1.在“营销活动”中，选择一个项目，然后在“新建”下拉列表下，单击“新建本地资产”。

1. 单击Smart Campaign
1. 输入智能营销活动名称，然后单击创建

**将触发器添加到Smart Campaign**&#x200B;将触发器添加到Smart Campaign允许您根据实时事件，一次对一个人运行Smart Campaign，在此例中是通过[requestCampaign API](https://developer.adobe.com/marketo-apis/api/mapi/#operation/triggerCampaignUsingPOST)发出的请求。 1.搜索“已请求营销活动”触发器，然后将其拖放到画布上。

1. 在触发器中，选择“is”和“Web服务API”。

**如何在营销活动中创建电子邮件流操作**&#x200B;将电子邮件与Smart Campaign关联后，营销人员可以管理他们希望电子邮件呈现的外观，并允许第三方应用程序确定接收者和接收时间。 将电子邮件创建为新本地资产后，可将其设置为营销活动中的流量操作。  查找并选择您要发送的电子邮件。

**调用requestCampaign API的代码示例**&#x200B;在Marketo界面中设置活动和触发器后，我们将向您说明如何使用API发送电子邮件。 第一个示例是XML请求，第二个示例是XML响应，最后一个示例是可用于生成XML请求的Java代码示例。 我们还将向您说明如何查找在调用`requestCampaign` API时使用的促销活动ID。
API调用还要求您预先知道Marketo促销活动的ID。 您可以使用以下任一方法确定促销活动ID： 1. 使用[getCampaignsForSource](/help/soap-api/getcampaignsforsource.md) API 1。 在浏览器中打开Marketo营销活动，并查看URL地址栏。 营销活动ID（以4位整数表示）可在“SC”之后立即找到。 例如：`<https://app-stage.marketo.com/#SC**1025**A1>`。粗体部分是营销活动ID - &quot;1025&quot;。 针对`requestCampaign`的SOAP请求

```xml
<?xml version="1.0" encoding="UTF-8"?>
<SOAP-ENV:Envelope xmlns:SOAP-ENV="<http://schemas.xmlsoap.org/soap/envelope/>" xmlns:ns1="<http://www.marketo.com/mktows/">
  <SOAP-ENV:Header>
    <ns1:AuthenticationHeader>
      <mktowsUserId>demo17_1_809939944BFABAE58E5D27</mktowsUserId><requestSignature>48397ad47b71a1439f13a51eea3137df46441979</requestSignature><requestTimestamp>2013-08-01T12:31:14-07:00</requestTimestamp>
    </ns1:AuthenticationHeader>
  </SOAP-ENV:Header>
  <SOAP-ENV:Body>
    <ns1:paramsRequestCampaign>
      <source>MKTOWS</source>
      <campaignId>4496</campaignId>
      <leadList>
        <leadKey>
          <keyType>EMAIL</keyType>
          <keyValue>lead@company.com</keyValue>
        </leadKey>
        <leadKey>
          <keyType>EMAIL</keyType>
          <keyValue>anotherlead@company.com</keyValue>
        </leadKey>
      </leadList>
    </ns1:paramsRequestCampaign>
  </SOAP-ENV:Body>
</SOAP-ENV:Envelope>
```

SOAP对requestCampaign的响应

```xml
<?xml version="1.0" encoding="UTF-8"?>
<SOAP-ENV:Envelope xmlns:SOAP-ENV="<http://schemas.xmlsoap.org/soap/envelope/>" xmlns:ns1="<http://www.marketo.com/mktows/">
  <SOAP-ENV:Body>
    <ns1:successRequestCampaign>
      <result>
        <success>true</success>
      </result>
    </ns1:successRequestCampaign>
  </SOAP-ENV:Body>
</SOAP-ENV:Envelope>
```

请参阅下面的执行上述场景的示例Java程序。

```java
import com.marketo.mktows.*;
import java.net.URL;
import javax.xml.namespace.QName;
import java.text.DateFormat;
import java.text.SimpleDateFormat;
import java.util.Date;
import javax.crypto.Mac;
import javax.crypto.spec.SecretKeySpec;
import org.apache.commons.codec.binary.Hex;
import javax.xml.bind.JAXBContext;
import javax.xml.bind.JAXBElement;
import javax.xml.bind.Marshaller;


public class RequestCampaign {

    public static void main(String[] args) {
        System.out.println("Executing Request Campaign");
        try {
            URL marketoSoapEndPoint = new URL("CHANGE ME" + "?WSDL");
            String marketoUserId = "CHANGE ME";
            String marketoSecretKey = "CHANGE ME";

            QName serviceName = new QName("http://www.marketo.com/mktows/", "MktMktowsApiService");
            MktMktowsApiService service = new MktMktowsApiService(marketoSoapEndPoint, serviceName);
            MktowsPort port = service.getMktowsApiSoapPort();

            // Create Signature
            DateFormat df = new SimpleDateFormat("yyyy-MM-dd'T'HH:mm:ssZ");
            String text = df.format(new Date());
            String requestTimestamp = text.substring(0, 22) + ":" + text.substring(22);
            String encryptString = requestTimestamp + marketoUserId ;

            SecretKeySpec secretKey = new SecretKeySpec(marketoSecretKey.getBytes(), "HmacSHA1");
            Mac mac = Mac.getInstance("HmacSHA1");
            mac.init(secretKey);
            byte[] rawHmac = mac.doFinal(encryptString.getBytes());
            char[] hexChars = Hex.encodeHex(rawHmac);
            String signature = new String(hexChars);

            // Set Authentication Header
            AuthenticationHeader header = new AuthenticationHeader();
            header.setMktowsUserId(marketoUserId);
            header.setRequestTimestamp(requestTimestamp);
            header.setRequestSignature(signature);

            // Create Request
            ParamsRequestCampaign request = new ParamsRequestCampaign();

            request.setSource(ReqCampSourceType.MKTOWS);

            ObjectFactory objectFactory = new ObjectFactory();
            JAXBElement<Integer> campaignId = objectFactory.createParamsRequestCampaignCampaignId(4496);
            request.setCampaignId(campaignId);

            ArrayOfLeadKey leadKeyList = new ArrayOfLeadKey();
            LeadKey key = new LeadKey();
            key.setKeyType(LeadKeyRef.EMAIL);
            key.setKeyValue("lead@company.com");

            LeadKey key2 = new LeadKey();
            key2.setKeyType(LeadKeyRef.EMAIL);
            key2.setKeyValue("anotherlead@company.com");

            leadKeyList.getLeadKeies().add(key);
            leadKeyList.getLeadKeies().add(key2);

            JAXBElement<ArrayOfLeadKey> arrayOfLeadKey = objectFactory.createParamsRequestCampaignLeadList(leadKeyList);
            request.setLeadList(arrayOfLeadKey);

            SuccessRequestCampaign result = port.requestCampaign(request, header);

            JAXBContext context = JAXBContext.newInstance(SuccessRequestCampaign.class);
            Marshaller m = context.createMarshaller();
            m.setProperty(Marshaller.JAXB_FORMATTED_OUTPUT, true);
            m.marshal(result, System.out);

        }
        catch(Exception e) {
            e.printStackTrace();
        }
    }
}
```

本文包含用于实施自定义集成的代码。 由于其自定义性质，Marketo技术支持团队无法对自定义工作进行故障诊断。 如果没有适当的技术经验或联系经验丰富的开发人员，请勿尝试实施以下代码示例。

由&#x200B;_Murta_&#x200B;发布于&#x200B;_2014-03-27_

## 使用AP从Marketo发送包含动态内容的电子邮件 — 

假设您要自动处理呼叫中心跟进电子邮件。 在您的支持代表与客户交谈后，您想自动发送一封电子邮件，感谢他们联系您的公司。 让我们更进一步地说，您希望包括与您在CRM中跟踪的客户讨论的特定对话主题。 您可以在Marketo中使用requestCampaign SOAP API执行此操作，以发送包含动态内容的电子邮件。 requestCampaign API允许您传入潜在客户。 它还允许您传入可与现有Campaign一起使用的项目令牌以发送动态内容。 requestCampaign SOAP API要求电子邮件收件人存在于Marketo中。 因此，在调用requestCampaign API之前，请使用[getLead API](/help/soap-api/getlead.md)验证该电子邮件是否存在于Marketo中。 我们将向您展示如何创建智能营销活动，如何设置触发器以通过API发送营销活动，如何创建通过项目令牌接受动态内容的电子邮件，如何定义电子邮件作为流操作的一部分，以及第五个用于执行此营销活动的代码示例。 **如何在Marketo中创建新的Smart Campaign** Marketo中的Smart Campaigns执行您的所有营销活动。 您可以设置一系列对智能联系人列表执行的自动操作。 在发送事务性电子邮件时，可在营销活动中设置触发器（如下所示）以使用API发送电子邮件。 首先，让我们设置Smart Campaign。 1.在“营销活动”中，选择一个项目，然后在“新建”下拉列表下，单击“新建本地资产”

1. 单击Smart Campaign
1. 输入智能营销活动名称，然后单击创建&#x200B;**将触发器添加到Smart Campaign**&#x200B;将触发器添加到Smart Campaign允许您根据实时事件（在此例中为通过[requestCampaign API](https://developer.adobe.com/marketo-apis/api/mapi/#operation/triggerCampaignUsingPOST)发出的请求）逐个对一个人运行Smart Campaign。
1. 搜索“已请求营销活动”触发器，然后将其拖放到画布上。
1. 在触发器中，选择“is”和“Web服务API”。

**如何在Marketo中使用API传递动态内容**，我的令牌是您可以在程序中使用的变量。 “我的令牌”允许您在一个位置输入与项目相关的信息，将该信息替换为您指定的值，并在应用程序的其它部分（如电子邮件模板）中检索该信息。 使用requestCampaign SOAP API，您可以传递一系列程序令牌，它们将覆盖现有令牌。 营销活动运行后，令牌将被丢弃。 您可以在Campaign文件夹级别或项目级别创建“我的令牌”。 Campaign文件夹级别的我的令牌将向下继承到Campaign文件夹中包含的所有项目。 如果在Campaign文件夹级别创建“我的令牌”，则可以在项目级别覆盖继承的值。 例如，如果您在Campaign文件夹级别为项目群日期和项目群描述定义令牌，则可以在单个项目群级别覆盖这些值。

下面是操作方法。 1.从“营销活动”树中，选择要创建令牌的Campaign文件夹或项目。 从顶部菜单栏中选择我的令牌。 随后将显示“我的令牌”画布。 从右侧树中，将令牌类型拖到画布上，在本例中为“文本”。 在“令牌名称”字段中，突出显示“我的令牌”，然后输入唯一的令牌名称，在本例中为“my.conversationtopic”。 在值字段中，为令牌输入相关的值，在本例中为“感谢您今天给我们打电话”。 请注意，通过使用API，我们将覆盖默认的“我的令牌”值。 单击“保存”以保存自定义令牌。  1.单击“新建”创建新电子邮件。 然后单击新建本地Assets并选择电子邮件。 接下来，填写相关字段以命名电子邮件。 起草电子邮件时，单击令牌图标以在电子邮件中包含令牌。 现在，您已使用令牌创建模板电子邮件，我们将在后续步骤中将电子邮件添加为Campaign的流操作。 因此，当您通过API调用营销活动时，将会发送电子邮件。
**如何在营销活动中创建电子邮件流操作**将电子邮件与Smart Campaign关联后，营销人员可以管理他们希望电子邮件呈现的外观，并允许第三方应用程序确定接收者和接收时间。 将电子邮件创建为新本地资产后，可将其设置为营销活动中的流量操作。 查找并选择您要发送的电子邮件。
**调用requestCampaign API的代码示例**&#x200B;在Marketo界面中设置活动和触发器后，我们将向您说明如何使用API发送电子邮件。 第一个示例是XML请求，第二个示例是XML响应，最后一个示例是可用于生成XML请求的Java代码示例。 我们还将向您说明如何查找在调用requestCampaign API时使用的促销活动ID。 API调用还要求您预先知道Marketo促销活动的ID。 您可以使用以下任一方法确定促销活动ID： 1. 使用[getCampaignsForSource](/help/soap-api/getcampaignsforsource.md) API 1。 在浏览器中打开Marketo营销活动，并查看URL地址栏。 营销活动ID（以4位整数表示）可在“SC”之后立即找到。 例如：`<https://app-stage.marketo.com/#SC**1025**A1>`。粗体部分是营销活动ID - &quot;1025&quot;。 SOAP的requestCampaign

```xml
<?xml version="1.0" encoding="UTF-8"?>
<SOAP-ENV:Envelope xmlns:SOAP-ENV="<http://schemas.xmlsoap.org/soap/envelope/>" xmlns:ns1="<http://www.marketo.com/mktows/">
  <SOAP-ENV:Header>
    <ns1:AuthenticationHeader>
      <mktowsUserId>demo17_1_809939944BFABAE58E5D27</mktowsUserId><requestSignature>48397ad47b71a1439f13a51eea3137df46441979</requestSignature><requestTimestamp>2013-08-01T12:31:14-07:00</requestTimestamp>
    </ns1:AuthenticationHeader>
  </SOAP-ENV:Header>
  <SOAP-ENV:Body>
    <ns1:paramsRequestCampaign>
      <source>MKTOWS</source>
      <campaignId>4496</campaignId>
      <leadList>
        <leadKey>
          <keyType>EMAIL</keyType>
          <keyValue>lead@company.com</keyValue>
        </leadKey>
      </leadList>
      <programTokenList>
        <attrib>
          <name>{{my.conversationtopic}}</name>
          <value>Thank you for calling about adding a line of service to your current plan.</value>
        </attrib>
      </programTokenList>
    </ns1:paramsRequestCampaign>
  </SOAP-ENV:Body>
</SOAP-ENV:Envelope>
```

SOAP对requestCampaign的响应

```xml
<?xml version="1.0" encoding="UTF-8"?>
<SOAP-ENV:Envelope xmlns:SOAP-ENV="<http://schemas.xmlsoap.org/soap/envelope/>" xmlns:ns1="<http://www.marketo.com/mktows/">
  <SOAP-ENV:Body>
    <ns1:successRequestCampaign>
      <result>
        <success>true</success>
      </result>
    </ns1:successRequestCampaign>
  </SOAP-ENV:Body>
</SOAP-ENV:Envelope>
```

请参阅下面的执行上述场景的示例Java程序。

```java
import com.marketo.mktows.\*;
import java.net.URL;
import javax.xml.namespace.QName;
import java.text.DateFormat;
import java.text.SimpleDateFormat;
import java.util.Date;
import javax.crypto.Mac;
import javax.crypto.spec.SecretKeySpec;
import org.apache.commons.codec.binary.Hex;
import javax.xml.bind.JAXBContext;
import javax.xml.bind.JAXBElement;
import javax.xml.bind.Marshaller;


public class RequestCampaign {

    public static void main(String[] args) {
        System.out.println("Executing Request Campaign");
        try {
            URL marketoSoapEndPoint = new URL("CHANGE ME" + "?WSDL");
            String marketoUserId = "CHANGE ME";
            String marketoSecretKey = "CHANGE ME";

            QName serviceName = new QName("http://www.marketo.com/mktows/", "MktMktowsApiService");
            MktMktowsApiService service = new MktMktowsApiService(marketoSoapEndPoint, serviceName);
            MktowsPort port = service.getMktowsApiSoapPort();

            // Create Signature
            DateFormat df = new SimpleDateFormat("yyyy-MM-dd'T'HH:mm:ssZ");
            String text = df.format(new Date());
            String requestTimestamp = text.substring(0, 22) + ":" + text.substring(22);
            String encryptString = requestTimestamp + marketoUserId ;

            SecretKeySpec secretKey = new SecretKeySpec(marketoSecretKey.getBytes(), "HmacSHA1");
            Mac mac = Mac.getInstance("HmacSHA1");
            mac.init(secretKey);
            byte[] rawHmac = mac.doFinal(encryptString.getBytes());
            char[] hexChars = Hex.encodeHex(rawHmac);
            String signature = new String(hexChars);

            // Set Authentication Header
            AuthenticationHeader header = new AuthenticationHeader();
            header.setMktowsUserId(marketoUserId);
            header.setRequestTimestamp(requestTimestamp);
            header.setRequestSignature(signature);

            // Create Request
            ParamsRequestCampaign request = new ParamsRequestCampaign();

            request.setSource(ReqCampSourceType.MKTOWS);

            ObjectFactory objectFactory = new ObjectFactory();
            JAXBElement<Integer> campaignId = objectFactory.createParamsRequestCampaignCampaignId(4496);
            request.setCampaignId(campaignId);

            ArrayOfLeadKey leadKeyList = new ArrayOfLeadKey();
            LeadKey key = new LeadKey();
            key.setKeyType(LeadKeyRef.EMAIL);
            key.setKeyValue("lead@company.com");

            leadKeyList.getLeadKeies().add(key);

            JAXBElement<ArrayOfLeadKey> arrayOfLeadKey = objectFactory.createParamsRequestCampaignLeadList(leadKeyList);
            request.setLeadList(arrayOfLeadKey);

            ArrayOfAttrib aoa = new ArrayOfAttrib();

            Attrib attrib = new Attrib();
            attrib.setName("{{my.conversationtopic}}");
            attrib.setValue("Thank you for calling about adding a line of service to your current plan.");

            aoa.getAttribs().add(attrib);

            JAXBElement<ArrayOfAttrib> arrayOfAttrib = objectFactory.createParamsRequestCampaignProgramTokenList(aoa);
            request.setProgramTokenList(arrayOfAttrib);

            SuccessRequestCampaign result = port.requestCampaign(request, header);

            JAXBContext context = JAXBContext.newInstance(SuccessRequestCampaign.class);
            Marshaller m = context.createMarshaller();
            m.setProperty(Marshaller.JAXB_FORMATTED_OUTPUT, true);
            m.marshal(result, System.out);

        }
        catch(Exception e) {
            e.printStackTrace();
        }
    }
}
```

在通过requestCampaign API进行调用后，您可以通过检查以查看Smart Campaign是否已在Marketo中运行来进行确认。

本文包含用于实施自定义集成的代码。 由于其自定义性质，Marketo技术支持团队无法对自定义工作进行故障诊断。 如果没有适当的技术经验或联系经验丰富的开发人员，请勿尝试实施以下代码示例。

由&#x200B;_Murta_&#x200B;发布于&#x200B;_2014-04-03_

## 基于业务逻辑捕获匿名访客活动

假设您想跟踪访问公司博客上特定帖子的用户。 假设在访问帖子的用户总数中，您只想跟踪表示感兴趣的用户，方法是至少花费5秒并向下滚动页面。 对于匿名用户，您希望通过此事件在Marketo中创建一个新的潜在客户；对于已知用户，您希望通过此事件更新其潜在客户活动。 您可以使用网站上的[Munchkin跟踪代码](/help/javascript-api/lead-tracking.md)完成此操作。 当非Cookie用户访问包含Munchkin跟踪代码的页面时，会在用户的浏览器中创建一个新的Cookie，并将在Marketo中创建一个新的匿名潜在客户。 如果用户已经过确认，并且用户是Marketo中的现有潜在客户，则对Marketo页面的访问将记录在该用户活动日志中。 我们首先介绍如何在Marketo中生成Munchkin跟踪代码，其次介绍如何修改Munchkin示例代码以使其仅在满足某些条件时触发，最后介绍如何验证是否在Marketo中记录了匿名用户的页面访问。

**如何生成Munchkin跟踪代码** Munchkin跟踪代码允许您跟踪对网站的访问。 下面介绍了三种类型的Munchkin代码，但在此示例中，我们使用异步Munchkin跟踪代码。 A)简单：代码行最少，但不会优化网页加载时间。 每次加载网页时，此代码都会加载jQuery库。 B)异步：缩短网页加载时间。 此代码检查jQuery库是否已存在，如果缺少库，则加载它，并在加载网页的其余部分后使用它执行跟踪代码。 C)异步jQuery：减少网页加载时间，并提高系统性能。 此代码假定您已经拥有jQuery，并且不检查以加载它。 1.单击应用程序右上角的管理员。  1.单击左侧树中的Munchkin 。  1.选择异步以跟踪代码类型。 1.单击并复制JavaScript跟踪代码以放置到您的网站上。
**用于Cookie用户和跟踪事件的代码示例**&#x200B;将跟踪代码放在网页上的`</body>`标记之前。 在Marketo中创建的登陆页面会自动包含跟踪代码，因此您无需在其上放置此代码。 此代码示例将在加载脚本后调用Munchkin API：

```javascript
<script type="text/javascript">
(function() {
  var didInit = false;
  function initMunchkin() {
    if(didInit === false) {
      didInit = true;
      Munchkin.init('XXX-XXX-XXX');
    }
  }
  var s = document.createElement('script');
  s.type = 'text/javascript';
  s.async = true;
  s.src = '//munchkin.marketo.net/munchkin.js';
  s.onreadystatechange = function() {
    if (this.readyState == 'complete' || this.readyState == 'loaded') {
      initMunchkin();
    }
  };
  s.onload = initMunchkin;
  document.getElementsByTagName('head')[0].appendChild(s);
})();
</script>
```

此代码示例将在用户在该页面上停留了5秒并且还向下滚动了500像素之后调用Munchkin API：

```javascript
<script src="https://code.jquery.com/jquery-2.1.0.min.js"></script>
<script type="text/javascript">
$(function(){
 setTimeout(function(){
  $(window).scroll(function() {
      var y_scroll_position = window.pageYOffset;
      var scroll_position = 500; //Sets number of pixels user must scroll to be tracked

  if(y_scroll_position > scroll_position) {
  //Munchkin tracking code
   (function() {
     var didInit = false;
     function initMunchkin() {
      if(didInit === false) {
        didInit = true;
        Munchkin.init('XXX-XXX-XXX');
      }
     }

     var s = document.createElement('script');
     s.type = 'text/javascript';
     s.async = true;
    s.src = '//munchkin.marketo.net/munchkin.js';
     s.onreadystatechange = function() {
      if (this.readyState == 'complete' || this.readyState == 'loaded') {
          initMunchkin();
      }
     };
     s.onload = initMunchkin;
     document.getElementsByTagName('head')[0].appendChild(s);
   })();
   }
 },5000); //Sets time delay before tracking user
});
</script>
```

**如何验证Marketo中记录了匿名用户访问页面的情况**

1. 单击顶部菜单中的Analytics ，然后单击“新建报表”。 选择“网页活动”作为报表类型，然后为您的报表命名。
1. 创建报表后，单击智能列表。 然后从右侧的框中选择已访问网页过滤器。 输入放置Munchkin跟踪代码的网页。
1. 单击“Setup（设置）”。 选择“来自ISP的匿名访客”，并将选项更改为“显示”。
1. 单击报告。 您现在会看到在您选择的网页上跟踪的活动。
1. 双击潜在客户记录，该记录随后将显示活动日志，您可以在其中查看匿名用户访问的特定页面。

本文包含用于实施自定义集成的代码。 由于其自定义性质，Marketo技术支持团队无法对自定义工作进行故障诊断。 如果没有适当的技术经验或联系经验丰富的开发人员，请勿尝试实施以下代码示例。

由&#x200B;_Murta_&#x200B;发布于&#x200B;_2014-04-17_

## 使用RTP动态更改本地电话号码

Personalization就是一切 — 我们很久以前就明白了。 话虽如此，我仍感到惊讶的是，每次我需要立即帮助时，很难在网站上找到相关的当地电话号码。 [上安装了](https://business.adobe.com/products/marketo/content-personalization.html)Marketo Real-Time Personalization<https://business.adobe.com/products/marketo/adobe-marketo.html> (RTP)，这是好事。 我们可以利用[RTP访客API](/help/javascript-api/web-personalization.md)动态更改Web访客在网站不同部分看到的电话号码。 哇！ 你能相信吗？ 这魔法是怎么运作的？ 首先，需要在您的网站上安装RTP，如[此处](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/web-personalization/rtp-tag-implementation/deploy-the-rtp-javascript)所述。 接下来，按照以下说明并在您的网站上实施JavaScript代码：

1. 在&#x200B;**defaultPhone**&#x200B;配置中插入您的国际电话号码
1. 在&#x200B;**divIds**&#x200B;配置中插入HTML元素ID
1. 如果要使电话号码成为移动设备浏览器的点击呼叫链接，请将&#x200B;**mobileLink**&#x200B;配置设置为&#x200B;**true**。
1. 在&#x200B;**cityPhone**、**statePhone**&#x200B;和&#x200B;**countryPhone**&#x200B;配置中使用电话号码映射不同的位置

例如，以下是配置设置的示例值：

```json
  defaultPhone:"+1.503.608.4679", // Optional
  divIds:["phoneId1","phoneId2"],
  mobileLink: true,
  cityPhone: {
    "<a href="#">yanir</a>": ["San Mateo", "San Francisco"],
    "+353.1.242.3000": ["tel-aviv"]
  },
  statePhone: {
    "+1.650.376.2300": ["CA"],
    "+1.650.376.2302": ["OR"]
  },
  countryPhone: {
    "+1.650.376.2300": ["United States"],
    "+353.1.242.3000": ["Israel"]
  }
```

最后，插入一个HTML锚点标记，该标记包含与&#x200B;**divIds**&#x200B;中的一个ID匹配的ID（来自上面的步骤2）。 例如，如果您在&#x200B;**divIds**&#x200B;中指定了“phoneId1”，则HTML锚点标记将如下所示：

`<a href="tel:+1800229933" id="phoneId1">+1800229933</a>`

该脚本将检查以下顺序中是否存在匹配项： cityPhone > statePhone > countryPhone > defaultPhone您还可以使用文本替换电话号码（例如：“加入我们的旧金山用户组！”） 或HTML代码，并根据地理位置动态更改内容。 祝您使用愉快！

```html
<a href="tel:+1800229933" id="phoneId1">+1800229933</a>
<script>
(function(a){
    rtp('get','visitor',function(yc){
        var location = yc.results.location;
        var loop = true;
        var phoneChanged = false;
        console.log(yc.results);
        function checkObj(obj){
            return Object.getOwnPropertyNames(obj).length >0;
        }
        function changePhone(p){
            d=a.divIds;
            for(i=0;i<d.length;i++){
                if(document.getElementById(d[i]) !== null){
                    document.getElementById(d[i]).innerHTML = p;
                    if(a.mobileLink){
                        document.getElementById(d[i]).href= "tel:" + p;
                    }
                    console.log(p);
                }
            }
            loop = false;
            phoneChanged = true;
        }
        function matchLocation(loc,objc){
            for (var key in objc) {
                for(i=0;i<objc[key].length && loop;i++){
                    if (!loop) { return true;};
                    val = objc[key][i];
                    //console.log(loc + location[loc] + " ? " + val);
                    if(location[loc].toLowerCase() === val.toLowerCase()){
                        changePhone(key);
                    }
                }
            }
        }
        if(checkObj(a.cityPhone)){
            matchLocation("city",a.cityPhone);
        }else if(checkObj(a.statePhone)){
            matchLocation("state",a.statePhone);
        }else if(checkObj(a.countryPhone)){
            matchLocation("country", a.countryPhone);
        }else if(!phoneChanged && a.defaultPhone.length > 0 ){
            changePhone(a.divId,a.defaultPhone);
        }
    });
})({
    defaultPhone:"",  //  [Optional] the number to show if visitor does not match the mapping below
    divIds:["phoneId1","Floater"],  //the phone HTML element ID, can be <div>, <a>, <span>, <p> etc.
    mobileLink: true,  //if you use click to call link (with href="tel:") you can also change its number

    cityPhone: {
        "<a href='#'>yanir</a>": ["San Mateo", "San Francisco"],
        "+353.1.242.3000": ["tel-aviv"]
    },
    statePhone: {
        "+1.650.376.2300": ["CA"],
        "+1.650.376.2302": ["OR"]
    },
    countryPhone: {
        "+1.650.376.2300": ["United States"],
        "+353.1.242.3000": ["Israel"]
    }
});
</script>
```

由&#x200B;_Yanir_&#x200B;发布于&#x200B;_2016-02-02_

## 2016年冬季更新

### 自定义对象

* 现在支持[自定义对象N:N关系](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/administration/marketo-custom-objects/create-marketo-custom-objects)
   * 现在，潜在客户或客户记录可以通过定义中间对象通过自定义对象建立多对多关系。 创建独立自定义对象类型后，可以创建中间对象类型，其链接字段指向独立对象以及潜在客户或帐户。
   * 此功能没有新的API调用，但必须正确配置对象定义才能通过API利用这些关系。
* `getLeadActivities`和`getLeadChanges`将不再返回匿名潜在客户的活动。 有关详细信息，请参阅[下一代Munchkin跟踪常见问题解答](https://experienceleague.adobe.com/zh-hans/docs/marketo/using/home)

由&#x200B;_Kenny_&#x200B;发布于&#x200B;_2016-02-05_

## 使用REST API检索单个Lead的活动

以下是我们的开发人员社区反复提出的一个问题：

“我如何获得单个潜在客户过去的活动列表？”

直到最近，还没有一种简单的方法可以使用REST API完成此操作。 但现在有了！ 2016年冬季版的REST API包含一些非常有用的增强功能。 [获取潜在客户活动](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getLeadActivitiesUsingGET)现在接受可用于指定潜在客户ID的&#x200B;**leadIds**&#x200B;参数。 当指定&#x200B;**leadIds**&#x200B;参数时，将仅返回该潜在客户ID的活动。 您可以将此视为潜在客户ID过滤器。 请注意，如果要过滤多个潜在客户（最多30个）的结果，则&#x200B;**leadIds**&#x200B;参数可以包含以逗号分隔的潜在客户ID列表。 例如，当将活动限制为特定公司的潜在客户时，这可能很有用。 下面的&#x200B;**示例**&#x200B;是对[获取潜在客户活动](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getLeadActivitiesUsingGET)的示例请求，该请求包含&#x200B;**leadIds**&#x200B;参数。 我已为&#x200B;**leadIds**&#x200B;参数指定值“50”，该值对应于我的Marketo实例中的任意商机。 我为activityTypeIds参数指定的值为“129”，该值对应于我Marketo实例上的“移动设备应用程序会话”活动。

`<https://123-abc-456.mktorest.com/rest/v1/activities.json?leadIds=50&activityTypeIds=129&nextPageToken=WQV2VQVPPCKHC6AQYVK7JDSA3J4SMAZRQO4RKIXCEMLFCM2APRSQ====>`

以下是上述请求响应的摘录。 如您所见，它只包含具有“leadId”： 50和“activityTypeId”： 129的结果对象。

```json
{
    "id": 846,
    "leadId": 50,
    "activityDate": "2015-04-06T21:58:59Z",
    "activityTypeId": 129,
    "primaryAttributeValueId": 13,
    "primaryAttributeValue": "Sample App",
    "attributes": [
        {
            "name": "Device Type",
            "value": "iPhone"
        },
        {
            "name": "Mobile App Session Length",
            "value": 7
        },
        {
            "name": "Platform",
            "value": "ios"
        }
    ]
}
{
    "id": 879,
    "leadId": 50,
    "activityDate": "2015-04-07T00:45:11Z",
    "activityTypeId": 129,
    "primaryAttributeValueId": 13,
    "primaryAttributeValue": "Sample App",
    "attributes": [
        {
            "name": "Device Type",
            "value": "iPhone"
        },
        {
            "name": "Mobile App Session Length",
            "value": 5
        },
        {
            "name": "Platform",
            "value": "ios"
        }
    ]
}
{
    "id": 1114,
    "leadId": 50,
    "activityDate": "2015-04-08T00:02:41Z",
    "activityTypeId": 129,
    "primaryAttributeValueId": 13,
    "primaryAttributeValue": "Sample App",
    "attributes": [
        {
            "name": "Device Type",
            "value": "iPhone"
        },
        {
            "name": "Mobile App Session Length",
            "value": 241
        },
        {
            "name": "Platform",
            "value": "ios"
        }
    ]
}
{
    "id": 1551,
    "leadId": 50,
    "activityDate": "2015-04-09T23:31:56Z",
    "activityTypeId": 129,
    "primaryAttributeValueId": 13,
    "primaryAttributeValue": "Sample App",
    "attributes": [
        {
            "name": "Device Type",
            "value": "iPhone"
        },
        {
            "name": "Mobile App Session Length",
            "value": 223
        },
        {
            "name": "Platform",
            "value": "ios"
        }
    ]
}
{
    "id": 1716,
    "leadId": 50,
    "activityDate": "2015-04-15T22:44:19Z",
    "activityTypeId": 129,
    "primaryAttributeValueId": 13,
    "primaryAttributeValue": "Sample App",
    "attributes": [
        {
            "name": "Device Type",
            "value": "iPhone"
        },
        {
            "name": "Mobile App Session Length",
            "value": 223
        },
        {
            "name": "Platform",
            "value": "ios"
        }
    ]
}
```

## 使用Zapier与Marketo和500多个应用程序无缝集成

这是Marketo首席解决方案顾问Philippe Delle Case的帖子。

### 目标

本文详细介绍了如何在Zapier的帮助下将Marketo与可能超过500个云应用程序集成。 为此，我们将从头开始构建适用于Marketo的Zapier连接器，并实施两个实际的集成用例：用例1：从FullContact Card Reader到Marketo的单向Leads集成

* 使用FullContact移动卡片Reader应用程序扫描任何联系人的名片，并获取在Marketo中自动创建的商机。
* 在Marketo中将现有潜在客户添加到静态列表，并查找自动添加到Google工作表中的潜在客户。
* 在Google工作表中修改任何潜在客户，发现所做的更改会回馈到Marketo。

### 先决条件

**使用Zapier** [Zapier](https://zapier.com/)注册免费帐户，这是一项Web应用自动化服务，可让您轻松地在其他联机应用之间自动执行任务，而无需程序员或任何IT资源。 可以在[此处](https://zapier.com/api/v4/helpdocs/category/redirect/what-is-zapier)找到简介。 如今，Zapier可以在许多不同领域(如营销、CRM、CMS、客户支持、电子签名、Forms等)支持500多个应用程序。 一个应用程序与另一个应用程序之间的单个集成称为Zap。 有关支持的Web应用的详尽列表，请查看Zapier的[zapbook](https://zapier.com/apps)。  在[此处](https://zapier.com/sign-up/)注册免费帐户。 您每月最多可以访问100个任务，每15分钟可以运行5个zap和zap。 您当然可以通过订阅Zapier的付费计划（基本、业务、业务等）获得更多好处

**以管理员身份或使用提供的API用户帐户访问Marketo实例**&#x200B;我们的Zapier连接器将使用Marketo REST API将潜在客户数据推送到Marketo。 要使用此API，您需要一个API用户和一项自定义服务。如果您是Marketo实例的管理员，则可以自行创建这些服务。 如果没有，则管理员需要向您提供这些内容。 还可以创建一个Webhook，只有Marketo管理员可以访问。 有关如何创建Marketo API用户和自定义服务的分步说明，请参阅[此处](http://rest-api/custom-services/)。 完成后，您应该具有以下凭据来调用Marketo REST API：客户端ID、客户端密钥、Munchkin帐户ID、Munchkin帐户ID

您可以从Munchkin或Web服务管理员屏幕获取Munchkin帐户ID。 其模式如下所示： `000-XXX-000`。  无需获取访问令牌，因为它仅在一小时内有效。 连接器将自动为您生成令牌。
**使用Google Docs、Sheets和Slides免费注册一个帐户，这些是工作效率应用程序，可让您创建各种联机文档，与其他人实时处理这些文档，并将它们在线存储在Google驱动器中。 我们的用例需要一个Google工作表。 可在[此处](https://workspace.google.com/products/docs/)找到Google Docs的其他功能以及使用Google创建帐户。
**使用FullContact注册免费帐户** FullContact通过拉入您的所有联系人并持续将他们与社交个人资料、照片、电子邮件签名、公司信息等的更改进行同步，让您与最重要的用户保持完全的联系。 它们提供一款移动名片阅读器，可将名片扫描到250多种网络应用程序中，包括Zapier。 您可以在[此处](https://app.fullcontact.com/login)注册免费帐户。 您还可以订购功能更强、容量更大的高级付费帐户。 可以从[Apple AppStore](https://apps.apple.com/us/app/fullcontact-business-card/id576780807)或[Google Play](https://play.google.com/store/apps/details?id=com.fullcontact.cardreader)下载移动设备应用程序。 FullContact Zap在[此处](https://zapier.com/apps/contacts-plus/integrations)记录。

### 适用于Zapier的Marketo连接器的实施

**从Zapier Web界面创建Marketo应用程序**，转到开发人员门户。 单击&#x200B;**添加新应用程序**&#x200B;并至少填写标题(例如“Marketo”)和说明。 标识是可选的，但很好用。\ **身份验证**&#x200B;在此部分中，我们将声明用于Marketo REST API身份验证和身份验证设置的各个字段。 首先创建以下字段：

编辑“身份验证设置”：

* 身份验证类型：会话身份验证
* 身份验证映射：

  `{"access_token":"{{access_token}}"}`

* 访问Querystring中的令牌位置**：**令牌

创建Marketo自定义服务后，客户端ID和客户端密钥将变为可用。 我们使用客户端ID和客户端密钥通过REST API [身份验证](/help/rest-api/authentication.md)终结点生成访问令牌。 然后，我们可以使用此访问令牌对REST API发出后续请求。 令牌在一小时后过期，必须再次生成才能继续调用REST API。 我们选择了身份验证类型=“会话身份验证”，因为它允许我们在会话令牌过期时执行自定义身份验证脚本。 我们将在“脚本API”部分中看到如何实施此机制，该机制只能用于此类型的身份验证。
**触发器** Zapier触发器用于将数据导入Zapier。 我们的用例不需要一个，因为我们将改用Marketo Webhook。 但是，我们仍需要编写一个虚拟触发器，作为Marketo连接器的强制测试。 我们将创建一个调用Marketo REST API [获取每日使用情况](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getDailyUsageUsingGET)端点的测试触发器。 单击&#x200B;**添加新触发器**&#x200B;以启动向导，并填写以下字段（未提及的字段可留空）：名称和描述

* 名称：测试触发器
* 密钥： test_trigger
* 描述：Marketo应用程序的测试触发器
* 重要事项？ 未选中
* 隐藏？ 已选中

触发器字段

* None

数据来源

* 数据Source：轮询
* 轮询URL： `https://{{munchkin_account_id}}.mktorest.com/rest/v1/stats/usage.json`

示例结果

* 留空

单击&#x200B;**管理触发器设置**，并将我们的测试触发器设置为将用于验证用户凭据的触发器。 **操作** Zapier操作用于从Zapier发送数据。 我们将通过调用Marketo REST API来实施Create_Update潜在客户操作。 通过此操作，我们可以在Marketo中创建一个新商机；如果该商机已存在，则会使用提交的值更新它。 我们将使用字段“email”执行重复数据删除。 单击&#x200B;**添加新操作**&#x200B;以启动向导，并填写以下字段（未提及的字段可留空）：名称和描述

* 名称：Create_Update销售线索
* 名词：潜在客户
* 密钥： create-update-lead
* 描述：在Marketo中创建新潜在客户，如果该潜在客户已存在，则使用提交的值更新它
* 重要事项？ 已选中
* 隐藏？ 未选中

操作字段操作字段是用户要将数据映射到其中的字段。 请根据您的需求仔细选择这些量度，因为它们将代表您可以在Marketo中更新的所有数据。 Zapier中有一个选项可为Marketo中的最终用户提供所有可用字段，但这样会增加代码和复杂性，一次性连接器不需要这样做。 例如，我们选择了以下字段。

在我们的示例中，分区名称是必需的，因为我们的Marketo实例具有正在使用的潜在客户分区。 否则，可以忽略。 我们将其与“input”组分开，以便最终用户了解这不是要同步的字段。 “注释”字段来自Marketo和Salesforce之间的同步。如果您的Marketo实例中没有此字段，请不要使用它。 字段“Called”是在我们的Marketo实例中创建的，如果您的Marketo实例中没有，请勿使用它。 当然，目标是让您从Marketo中选择所需的字段。 建议从小处开始，稍后添加额外的字段。 发送数据的位置

* 操作终结点URL： `https://{{munchkin_account_id}}.mktorest.com/rest/v1/leads.json`

示例结果

* 留空

### Zapier脚本API

Zapier的脚本功能允许您处理在您的应用程序API和Zapier之间交换的请求和响应。 您可以在发送HTTP请求之前修改请求，并且可以在Zapier执行任何操作之前分析响应。 我们需要它来完成我们的自定义“会话身份验证”身份验证，以便与Marketo配合使用。 更多信息[此处](https://zapier.com/developer/documentation/scripting/)。 复制以下代码，稍后我们将进行一些解释：

```javascript
var Zap = {

    get_session_info: function(bundle) {

       console.log('Entering get_session_info method ...');

         var access_token,
            access_token_request_payload,
            access_token_response;


        // Assemble the meta data for our Access Token swap request
         console.log('building Request with client_id=' + bundle.auth_fields.client_id + ', and client_secret=' + bundle.auth_fields.client_secret);
        access_token_request_payload = {
            method: 'POST',
            url: 'https://' + bundle.auth_fields.munchkin_account_id + '.mktorest.com/identity/oauth/token',
            params: {
                'grant_type' : 'client_credentials',
                'client_id' : bundle.auth_fields.client_id,
                'client_secret' : bundle.auth_fields.client_secret
            },
            headers: {
                'Content-Type': 'application/json',  // Could be anything.
                Accept: 'application/json'
            }
        };

        // Fire off the Access Token request.
        access_token_response = z.request(access_token_request_payload);

        // Extract the Access Token from returned JSON.
        access_token = JSON.parse(access_token_response.content).access_token;
        console.log('New Access_Token=' + access_token);

        // This will be mixed into bundle.auth_fields in future calls.
        //bundle.auth_fields.access_token=access_token;
        return {'access_token': access_token};
    },


    test_trigger_pre_poll: function(bundle) {

         console.log('Entering test_trigger_pre_poll method ...');

         bundle.request.params = {
         'access_token':bundle.auth_fields.access_token
         };

         return bundle.request;

    },


    test_trigger_post_poll: function(bundle) {

        console.log('Entering test_trigger_post_poll method ...');

        var data = JSON.parse(bundle.response.content);
        if ((!data.success)&&((data.errors[0].code=="601")||(data.errors[0].code=="600"))){
            console.log('Access Token expired or invalid, requesting new one - data.success=' + data.success + ', data.errors[0].code=' + data.errors[0].code);

           throw new InvalidSessionException(); // Calling get_session_info() to regenerate Access Token
        }

        return JSON.parse(bundle.response.content);
    },

    create_update_lead_pre_write: function(bundle) {

       bundle.request.params = {'access_token':bundle.auth_fields.access_token};
       return bundle.request;
    },

    create_update_lead_post_write: function(bundle) {

         var data = JSON.parse(bundle.response.content);
         if ((!data.success)&&((data.errors[0].code=="601")||(data.errors[0].code=="600"))){
            console.log('Access Token expired or invalid, requesting new one - data.success=' + data.success + ', data.errors[0].code=' + data.errors[0].code);
            throw new InvalidSessionException(); // Calling get_session_info() to regenerate Access Token
        }
        return JSON.parse(bundle.response.content);
    }
};
```

**方法** **get_session_info**

* 此方法负责生成或重新生成调用Marketo REST API [身份验证](/help/rest-api/authentication.md)端点的访问令牌。
* 每当任何“post_poll”方法遇到“访问令牌过期”错误时，都会调用此项。 访问令牌计划每1小时过期一次，因此可正常进行。
* 操作终结点URL： https://{{munchkin_account_id}}.mktorest.com/identity/oauth/token

**pre_poll， pre_write**

* 我们必须在我们创建的任何Trigger上创建“pre-poll”方法，以便在发送HTTP请求之前对其进行修改，以便我们可以在其参数中添加Marketo访问令牌。
* 出于同样的原因，我们必须对我们创建的任何操作创建一个“预写入”方法。

**post_poll， post_write**

* 我们必须在我们创建的任何Trigger上创建“post-poll”方法，以便在Zapier对响应执行任何操作之前对其进行解析，并最终截获“访问令牌已过期”错误。
* 出于同样的原因，我们必须对我们创建的任何操作创建“写入后”方法。
* 如果发生此类错误，我们会抛出InvalidSessionException，告诉Zapier重新执行身份验证并再次执行get_session_info方法。

请注意，您可以从屏幕右上角的“快速链接”菜单从脚本API访问捆绑包日志。 这对于调试脚本非常有用。

用例1：将Marketo与FullContact卡片Reader集成

对于此集成，我们将从FullContact创建一个Zap到Marketo。 通过此Zap，您可以使用FullContact移动卡Reader扫描名片，并将潜在客户推送到Marketo。   从Zapier仪表板中&#x200B;**Zap FullContact -> Marketo**&#x200B;单击“新建Zap”按钮。

Zapier中的&#x200B;**触发器**

* 选择应用程序FullContact
* 选择FullContact触发器“新名片”
* 连接到您的FullContact帐户
* 测试FullContact应用程序

Zapier中的&#x200B;**操作**

* 选择我们之前创建的应用程序Marketo，它应会显示在Beta中
* 选择Marketo操作“Create_Update Lead”
* 连接到您的Marketo帐户，并填写身份验证参数(Munchkin帐户ID、客户端ID、客户端密码)
* 将字段从FullContact映射到Marketo
* 最终填充新潜在客户应前往的分区名称(仅当Marketo实例中存在分区时)
* 测试Marketo应用程序
* 激活您的Zap

注意：确保从FullContact下载名片Reader并直接从移动设备激活Zapier集成。

用例2：Marketo与Google的集成 — 

对于此集成，我们将创建两个Zap。 一个从Marketo到Google工作表，另一个从Google工作表到Marketo。 通过此Zap，您可以在Marketo和Google Sheet之间同步一些潜在客户或联系人。   **Zap Marketo Webhook -> Google工作表**
对于首个Zap，我们不依赖Marketo的自定义连接器，但我们利用Marketo的Webhooks和“Zapier的Webhooks”触发器。 在Zapier仪表板中，单击“生成新的Zap”按钮。 **在Zapier中触发第1部分**

* 选择“Webhooks by Zapier”触发器应用程序
* 选中“Catch Hook”，以允许等待POST或GET转到Zapier URL
* 无需摘下子键
* Zapier生成了一个自定义&#x200B;**webhook URL**，供您发送请求并复制到剪贴板中

在Marketo中&#x200B;**Webhook（管理员要执行的步骤）**

* 转到管理员 — > Webhooks
* 创建一个名为“将潜在客户推送到Zapier”的新Webhook并编辑Webhook表单。

在模板的字段中，声明要转移到Zapier的所有潜在客户字段，并使用Marketo的令牌。 对于我们的用例，我们采用我们为自定义Zapier连接器定义的字段，该连接器会将潜在客户推送到Marketo：

```json
{
"firstName":"{{lead.First Name}}",
"lastName":"{{lead.Last Name}}",
"email":"{{lead.Email Address}}",
"phone":"{{lead.Phone Number}}",
"leadOwner":"{{lead.Lead Owner First Name}} {{lead.Lead Owner Last Name}}",
"leadOwnerEmail":"{{lead.Lead Owner Email Address}}",
"leadNotes":"{{lead.Lead Notes:default=edit me}}",
"called":"{{lead.Called}}"
}
```

* 保存表单
* 无需响应映射，因此您已完成Webhook

**在Marketo中测试营销活动（由营销人员或管理员执行的步骤）**

* 在营销活动中，创建新的智能营销活动

出于测试目的，我们将创建一个营销活动，每当潜在客户状态更改为MQL时，该营销活动都会触发我们的Webhook。 当然，您可以将Webhook用于任何其他业务目的。

* 编辑智能列表
* 在流中调用Webhook
* 计划活动
* 确保每个潜在客户每次都可以在整个流程中运行
* 激活Smart Campaign

**在Zapier中触发第2部分**

* 为了完成“Webhooks by Zapier”触发器应用程序，我们需要触发一次Marketo智能促销活动，并在Zapier中捕获Webhook
* 在我们的测试用例中，我们只需要转到Marketo Lead数据库，打开一个lead并将其状态更改为“MQL”

**在Google Sheets中创建电子表格**

* 创建新电子表格
* 创建工作表或使用默认工作表
* 为您要从Marketo同步的每个字段(在Marketo webhook中声明的字段)添加一列

  Zapier中的&#x200B;**操作**

* 选择应用程序Google工作表
* 选中选项“创建电子表格行”
* 连接到您的Google Sheets帐户
* 选择Google工作表电子表格
* 选择工作表
* 映射“Webhooks by Zapier”触发器应用程序与Google工作表之间的所有字段。

* 测试Google Sheets应用程序
* 激活您的Zap

**Zap Google工作表 — > Marketo**

在Zapier仪表板中，单击“生成新的Zap”按钮。

Zapier中的&#x200B;**触发器**

* 选择“Google工作表”触发器应用程序
* 勾选在电子表格中添加或修改新行时触发的“更新的电子表格行”
* 连接到您的Google帐户
* 选择要从中触发的电子表格（应与上一个Zap中使用的电子表格相同）和工作表
* 将触发器列设置为“any_column”
* 测试Google Sheets应用程序

Zapier中的&#x200B;**操作**

* 选择我们之前创建的应用程序Marketo，它应会显示在Beta中
* 选择Marketo操作“Create_Update Lead”
* 连接到您的Marketo帐户，并填写身份验证参数(Munchkin帐户ID、客户端ID、客户端密码)
* 将字段从Google工作表映射到Marketo
* 最终填充新潜在客户应前往的分区名称(仅当Marketo实例中存在分区时)
* 测试Marketo应用程序
* 激活您的Zap

### 结论

以下是针对Zapier的Marketo连接器改进的一些想法：

* 添加与不同Marketo对象（列表、自定义对象等）相关的其他触发器和操作
* 可以从Marketo动态提取字段，而不是硬编码来自Marketo的字段，但这需要在Marketo和Zapier之间开展一些技术翻译工作。
* 与开发团队共享该连接器，并最终使其公开发布。

Zapier可能会部署Premium Marketo适配器，以便更轻松地实施我们的用例。 无论如何，本文始终可用于将Marketo与Zapier集成到免费的Zapier计划中，并构建高级适配器可能不支持的自定义用例。 我们希望您喜欢本文，它将帮助您在Marketo和Zapier方面取得更大的成功。 谢谢！

由&#x200B;_David_&#x200B;发布于&#x200B;_2016-04-17_

## 2016年春季更新

**REST API**

* 资产API — 网页
   * **登陆页面**&#x200B;现在通过15个新端点公开，这些新端点将允许为登陆页面创建、更新、删除、克隆和草稿管理。 登陆页面模板现在也公开草稿管理端点
      * 获取登陆页面
      * 按ID获取登陆页面
      * 按名称获取登陆页面
      * 创建登陆页面
      * 更新登陆页面元数据
      * 获取登陆页面内容
      * 添加登陆页面内容部分
      * 更新登陆页面内容部分
      * 删除登陆页面内容部分
      * 获取动态内容部分
      * 更新动态内容部分
      * 放弃登陆页面草稿
      * 批准登陆页面
      * 取消授权登陆页面草稿
      * 删除登陆页面
   * **登陆页面模板**
      * 放弃登陆页面模板草稿
      * 批准登陆页面模板
      * 取消登陆页面模板
      * 删除登陆页面模板
   * **Forms**&#x200B;已发布21个新端点，它们通过API提供完整的创建、编辑和管理功能。 这些API将不支持对Forms 1.0表单的更改。
      * 获取Forms
      * 按ID获取表单
      * 按名称获取表单
      * 获取表单字段列表
      * 更新表单字段列表
      * 创建表单
      * 获取表单感谢页面
      * 更新表单感谢页面
      * 更新表单
      * 放弃表单草稿
      * 批准表单
      * 取消批准表单
      * 克隆表单
      * 删除表单
      * 更新表单字段
      * 删除表单字段
      * 更新表单字段可见性规则
      * 添加富文本表单字段
      * 添加字段集
      * 从字段集中删除字段
      * 获取可用的表单字段
      * 更改表单字段位置
      * 更新提交按钮
   * 使用&#x200B;**获取或浏览项目**&#x200B;时，将为链接到SFDC营销活动的项目返回SFDC营销活动ID

**自定义对象**&#x200B;自定义对象现在将支持文本区域数据类型，允许在此类型的自定义对象字段中存储最多2000个字符的字符串字段。 **IP地址白名单**&#x200B;管理员用户现在可以管理IP地址白名单，以防止通过API进行未经授权的访问。 [您可以在此阅读有关此功能的更多信息](https://experienceleague.adobe.com/zh-hans/docs/marketo/using/home)。 **自定义活动UI**&#x200B;管理员用户现在将能够在其管理员菜单中定义自定义活动类型，并通过[添加自定义活动](https://developer.adobe.com/marketo-apis/api/mapi/#operation/addCustomActivityUsingPOST) API向潜在客户添加记录。 [您可以在此处阅读有关定义自定义活动类型的内容](https://experienceleague.adobe.com/zh-hans/docs/marketo/using/home)。

由&#x200B;_Kenny_&#x200B;发布于&#x200B;_2016-06-01_

## 2016年夏季更新

在9月23日发布的2016年夏季版本中，将发布三项面向开发人员的功能。

### REST API中的Email 2.0支持

现在启用了仅与v1.0电子邮件和模板兼容的所有[预先存在的资源API](/help/rest-api/assets.md)，以便与v2.0电子邮件资源一起使用。

### 将潜在客户推送到Marketo

[推送潜在客户](/help/rest-api/leads.md)是备用潜在客户同步方法，旨在更轻松地在Smart Campaigns中触发。 您可以创建单个活动日志项目，关联潜在客户，并在一次呼叫中更新潜在客户记录。 这与潜在客户填写单个表单的工作方式类似，可以更轻松地用作表单提交的代理方法，而不是使用现有的Sync Leads方法。

### HTTP压缩

REST API现在可以使用HTTP 1.1规范定义的标准来压缩响应。 这有助于减小响应大小，这将提高传输速度，并最大限度地降低带宽利用率。  

由&#x200B;_Kenny_&#x200B;发布于&#x200B;_2016-09-23_

## 在Marketo中使用swagger-codegen

[Swagger-codegen](https://github.com/swagger-api/swagger-codegen)是一个功能强大的Java库，可从swagger定义生成服务器存根和API客户端。 这可以显着降低为任何特定语言生成客户端的难度和成本。 要开始并生成第一个客户端，您首先需要获取Marketo的Swagger定义之一的特定于实例的副本。 输入要测试的实例中的Munchkin ID。 从身份定义开始。 现在，您有了特定于实例的定义，接下来需要下载并安装swagger-codegen。 此过程特定于您的操作系统，您可以在[此处](https://github.com/swagger-api/swagger-codegen#prerequisites)获取相关说明。在默认设置下，codegen将输出一个客户端，该客户端涵盖所有提供的端点和模型。 这些端点通常通过名为DefaultApi的类进行管理，该类包含用于调用可用端点的方法以及“docs”文件夹中提供的示例（默认情况下，并非所有语言都包含此类模板）。 现在，让我们构建第一个客户端。 创建要在其中保存代码的文件夹，然后在终端会话中转到该文件夹，然后使用“生成”命令以所需的语言构建客户端（我们假定您已使用homebrew install方法）：

swagger-codegen生成 — i $definitionLocation -l $yourLanguage -o $yourLocation

这会将客户端代码输出到所需的位置。 现在，我们来看看是否使用此项来调用身份端点并获取访问令牌：

```java
 public static void main(String[] args){
  String client_id = args[0];
  String client_secret = args[1];
  ApiClient client = new ApiClient();
  DefaultApi id = new DefaultApi();
  try {
   String token = id.identityOauthTokenGet(client_id, client_secret, "client_credentials").getAccessToken();
   System.out.println(token);
  } catch (ApiException e) {
   e.printStackTrace();
  }
 }
```

```php
<?php
require_once(_DIR_ . '/vendor/autoload.php');

$api_instance = new Swagger\\Client\\Api\\DefaultApi();
$client_id = "client_id_example";
$client_secret = "client_secret_example";
$grant_type = "grant_type_example";

try {
    $result = $api_instance->identityOauthTokenGet($client_id, $client_secret, $grant_type);
    print_r($result->getAccessToken);
} catch (Exception $e) {
    echo 'Exception when calling DefaultApi->identityOauthTokenGet: ', $e->getMessage(), "\\n";
}
?>
```

```c#
  public static void Main(string[] args)
  {
      string clientId = "CHANGE ME";
      string clientSecret = "CHANGE ME";

      IdentityApi instance = new IdentityApi();
      ResponseOfIdentity response = instance.IdentityUsingGET(clientId, clientSecret, "client_credentials");

      string message = string.Format("Access Token: {0}, Expires In: {1}, Scope: {2}, Token Type: {3}",
          response.AccessToken, response.ExpiresIn, response.Scope, response.TokenType);
      Console.WriteLine(message);
  }
```

现在我们知道如何获得授权，我们将在未来几周内看一看自动生成客户端的更高级用例。

由&#x200B;_Kenny_&#x200B;发布于&#x200B;_2016-10-10_

## Excel集成第1部分：使用Power Query提取和形状Marketo数据

这是一系列文章中的第一篇，这些文章解释了如何利用Microsoft Excel内置的Power BI技术，通过Marketo创建真正的自助式业务分析体验。 利用这些文章中涵盖的概念，您能够：

* 将数据从Marketo导入Excel
* 导入和组合来自其他源（SaaS应用程序、数据库、平面文件等）的数据
* 为业务需求和分析目的形成数据
* 按需刷新Excel中的数据
* 使用公式创建计算列和度量
* 创建异构数据之间的关系
* 使用数据透视表和数据透视图分析数据并构建高级报表
* 制作令人惊叹的数据可视化图表

### Power Query for Excel

本文首先介绍了使用Power Query技术进行数据导入和整形的过程。 Power Query是一种数据连接技术，它使您能够发现、连接、组合和优化数据源，以满足您的分析需求。 Power Query中的功能在Excel和Power BI Desktop中可用。 Power Query可以连接到许多数据源，如数据库、Facebook、Salesforce、MS Dynamics CRM等。 Marketo不受开箱即用支持，但幸运的是，我们可以使用Marketo REST API远程执行系统的许多功能，并且Power Query附带一组丰富的公式（非正式地称为“M”），允许您为自定义数据源编写脚本。

### 自定义连接器

使用Power Query编写单个REST API调用的脚本很少，但处理以下要求会变得更加困难：

* 访问令牌管理，包括身份验证机制和定期令牌刷新
* 用于大型数据集的分页机制
* 错误处理

本文介绍如何构建强大的自定义连接器，该连接器可以使用Marketo的REST API来拉取各种数据（潜在客户、活动、自定义对象、程序等）。 您唯一的限制是低于Marketo API每日请求限制。 此处介绍的概念主要针对Marketo，但它们也可用于集成其他提供REST API的SaaS解决方案。

### 先决条件

#### Power查询

在Excel 2016发布之前，Microsoft Power Query for Excel作为Excel加载项运行，已下载并安装在Excel 2010或Excel 2011上。 从Excel 2016开始，此技术是一项原生功能，集成到“获取和转换”部分下的“数据”功能区中。 为本文生成的所有脚本已在Excel 2016 for Windows上进行了测试。 Excel 2013或Excel 2010的概念应该相同，但可能需要一些调整。  Power Query当前仅在Microsoft Windows版本的Excel上可用；很遗憾，不支持Mac版本。

#### Marketo

Power Query将使用Marketo REST API从Marketo访问数据。 要使用这些API，您需要一个API用户和一项自定义服务。如果您是Marketo实例的管理员，则可以自行创建这些服务。 如果没有，则管理员需要向您提供这些内容。 有关如何创建Marketo API用户和自定义服务的分步说明，请参阅[此处](/help/rest-api/custom-services.md)。 完成后，您应该具有以下凭据以调用Marketo REST API： **客户端ID**&#x200B;和&#x200B;**客户端密钥**。 可以在Marketo的Web服务管理员的REST API部分找到&#x200B;**REST API端点**，它应具有以下模式：

`<https://XXX-XXX-XXX.mktorest.com/rest>`

Marketo的API具有每日请求限制，此限制可在Web服务管理员以及使用情况报表中找到。 **在设计查询时，请确保不要超出每日限制，因为您可能会丢失报告中的某些数据**。

### Power Query工作簿创建

让我们从新的Excel工作簿开始。 我们创建一个特定的配置工作表，用于声明所有Marketo REST API设置。 在此工作表中，我们创建了三个表：

表“**REST_API_Authentication**”具有列： **URL**：您的Marketo REST API终结点。 **客户端ID**：来自您的Marketo REST API OAuth2.0凭据。 **客户端密钥**：来自您的Marketo REST API OAuth2.0凭据。
表“**作用域**”具有列： **分页令牌SinceDatetime**：遵循ISO 8601标准日期表示法的日期（例如“2016-10-06T13:22:17-08:00”、“2016-10-06”为有效日期/时间），该标记用于获取自给定时间段以来的Marketo活动，这得益于初始“基于日期”的分页令牌。 此日期主要用于限制导入工作簿的数据量。 **列表ID**： Marketo中引用了我们正在处理的所有潜在客户/联系人的静态列表的ID。 此静态列表可在Marketo中自由管理（例如，智能营销活动可定期或实时向潜在客户和联系人提供内容）。
要获取静态列表的ID，请在Marketo中打开它，并从URL中获取其数字ID，例如`<https://myorg.marketo.com/#ST3517A1LA1>`，列表ID=3511。 **最大记录页数**：用于我们的伪递归算法，该算法使用“基于位置”的分页令牌对Marketo输出数据进行迭代，每页最多可包含300条记录。 由于我们希望每页记录数量尽可能多，因此我们将保持在300条。 因此，通常，如果最大记录页数设置为33.333，则意味着容量为33.333 X 300 = 99.99亿记录；但这还意味着将33.333 K作为您的Marketo API每日请求限制。 只要从查询中获得所有数据，算法就会立即停止，因此该参数只是循环的安全限制。

表`Leads`具有列： **潜在客户字段**：在查询潜在客户和联系人时，要从Marketo中收集以逗号分隔的潜在客户字段。 在Excel中声明表非常简单。 在电子表格中输入两行列名和列值，用鼠标突出显示表的外围，在“插入”菜单中选择表图标，然后为其命名。 为表及其列指定的名称非常重要，因为我们的脚本将直接调用它们。

## 身份验证和访问令牌

### 关于Marketo REST API身份验证

Marketo的REST API使用双腿OAuth 2.0进行身份验证。客户端ID和客户端密钥由您定义的自定义服务提供。 每个自定义服务均由仅限API的用户拥有，该用户具有一组角色和权限，可授权服务执行特定操作。 访问令牌与单个自定义服务关联。
Marketo开发人员网站上的[此处](/help/rest-api/authentication.md)介绍了完整的身份验证机制。 最初创建访问令牌时，其生命周期为3600秒或1小时。 同一自定义服务的每个连续身份验证调用均会返回当前访问令牌及其剩余生命周期。 令牌过期后，身份验证将返回全新的访问令牌。 管理访问令牌过期对于确保您的集成顺利工作并防止在正常操作期间发生意外身份验证错误很重要。

#### 创建查询

单击“数据”菜单的“Get&amp;Transform”部分中的“新建查询”图标，以创建新查询。 选择一个要以开头的空白查询，并为其命名，例如“MktoAccessToken”。 从查询编辑器启动高级编辑器，以便您可以手动编写一些Power Query公式的脚本。 在高级编辑器中输入以下代码：

```
let
    // Get url and credentials from config worksheet - Table REST_API_Authentication
    mktoUrlStr = Excel.CurrentWorkbook(){[Name="REST_API_Authentication"]}[Content]{0}[URL],
    clientIdStr = Excel.CurrentWorkbook(){[Name="REST_API_Authentication"]}[Content]{0}[Client ID],
    clientSecretStr = Excel.CurrentWorkbook(){[Name="REST_API_Authentication"]}[Content]{0}[Client Secret],

    // Calling Marketo API Get Access Token
    getAccessTokenUrl = mktoUrlStr & "/identity/oauth/token?grant_type=client_credentials&client_id=" & clientIdStr & "&client_secret=" & clientSecretStr,
    TokenJson = try Json.Document(Web.Contents(getAccessTokenUrl)) otherwise "Marketo REST API Authentication failed, please check your credentials",

    // Parsing access token
    accessTokenStr = TokenJson [access_token]

in
    accessTokenStr
```

源代码中以“//”开头的注释使代码具有自我说明性。 如果您需要参考任何函数，请查看本文参考部分中提供的链接。 单击“完成”按钮。 检查访问令牌是否已成功显示在最终应用步骤“accessTokenStr”的输出中。  关于Excel中安全性的快速评论；有时可能会要求您启用黄色横幅中的外部数据连接。 这是让查询正常工作所必需的。

#### 将查询转换为函数

返回到高级编辑器，并使用以下函数声明来封装代码：

```
let
    FnMktoGetAccessToken =()=>

        let
            // Get url and credentials from config worksheet - Table REST_API_Authentication
            mktoUrlStr = Excel.CurrentWorkbook(){[Name="REST_API_Authentication"]}[Content]{0}[URL],
            clientIdStr = Excel.CurrentWorkbook(){[Name="REST_API_Authentication"]}[Content]{0}[Client ID],
            clientSecretStr = Excel.CurrentWorkbook(){[Name="REST_API_Authentication"]}[Content]{0}[Client Secret],

            // Calling Marketo API Get Access Token
           getAccessTokenUrl = mktoUrlStr & "/identity/oauth/token?grant_type=client_credentials&client_id=" & clientIdStr & "&client_secret=" & clientSecretStr,
           TokenJson = try Json.Document(Web.Contents(getAccessTokenUrl)) otherwise "Marketo REST API Authentication failed, please check your credentials",

            // Parsing access token from Json
           accessTokenStr = TokenJson [access_token]

        in
            accessTokenStr

in FnMktoGetAccessToken
```

该函数不接受输入中的任何参数，而是从配置工作表中获取这些参数。 它生成访问令牌作为输出。 重命名您的查询`FnMktoGetAccessToken`并保存它。 请注意，您可以随时在Excel中查看所有查询，只需单击“数据”菜单“获取和转换”部分中的“显示查询”按钮。 现在，您的函数应使用函数图标“Fx”进行标记。

### 加载静态列表的成员

#### 获取潜在客户

Marketo Lead API提供了针对潜在客户记录的简单CRUD操作、修改潜在客户在静态列表和程序中的成员资格以及启动潜在客户的Smart Campaign处理的功能。 [此处](/help/rest-api/leads.md)介绍了所有这些功能。 可以根据静态列表或项目中的成员资格检索大量潜在客户记录。 使用静态列表的ID，您可以检索属于该静态列表的所有潜在客户记录。 列表的ID是调用中的路径参数。 有关详细信息，请参阅Marketo开发人员文档中的“列表和计划成员资格”一章。 每个API调用最多可以获取300条潜在客户记录，因此我们需要利用分页令牌来收集每页300条记录的记录。 在第一次调用后，我们在Json应答中获取寻呼令牌，当寻呼令牌不再位于输出中时，我们知道我们已经完成了。

### 基本查询

让我们开始一个功能齐全的查询，旨在从静态列表中下载所有潜在客户。 创建一个名为“MktoLeads”的新空白查询，并在高级编辑器中输入以下代码：

```
let
    // Get Url from config worksheet - Table REST_API_Authentication
    mktoUrlStr = Excel.CurrentWorkbook(){[Name="REST_API_Authentication"]}[Content]{0}[URL],
    // Get the number of iterations (pages of 300 records) - Table Scoping
    iterationsNum = Excel.CurrentWorkbook(){[Name="Scoping"]}[Content]{0}[Max Records Pages],
    // Get the List id - Table Scoping
    listIdStr = Number.ToText(Excel.CurrentWorkbook(){[Name="Scoping"]}[Content]{0}[List ID], "D", ""),
    // Get the Lead fields to extract - Table Leads
    LeadFieldsStr = Excel.CurrentWorkbook(){[Name="Leads"]}[Content]{0}[Lead Fields],


    // Build Multiple Leads by List Id URL
    getMultipleLeadsByListIdUrl = mktoUrlStr & "/rest/v1/list/" & listIdStr & "/leads.json?fields=" & LeadFieldsStr,

    // Build Marketo Access Token URL parameter
    accessTokenParamStr = "&access_token=" & FnMktoGetAccessToken(),

    pagingTokenParamStr = "",

    // Function iterating though the pages
    FnProcessOnePage =
    (accessTokenParamStr, pagingTokenParamStr) as record =>
        let

            // Send REST API Request
            content = Web.Contents(getMultipleLeadsByListIdUrl & accessTokenParamStr & pagingTokenParamStr),

            // Recover Json output and watch if token is expired, in that case, regenerate access token
            newAccessTokenParamStr = if Json.Document(content)[success]=true then accessTokenParamStr else "?access_token=" & FnMktoGetAccessToken(),
            getMultipleLeadsByListIdJson = if Json.Document(content)[success]=true then Json.Document(content) else Json.Document(Web.Contents(getMultipleLeadsByListIdUrl & newAccessTokenParamStr & pagingTokenParamStr)),

            // Parse Json outputs: data and next page token
            data = try getMultipleLeadsByListIdJson[result] otherwise null,
            next  = try  "&nextPageToken=" & getMultipleLeadsByListIdJson[nextPageToken] otherwise null,
            res = [Data=data, Next=next, Access=newAccessTokenParamStr]
        in
            res,

    // Generates a list of values given four functions that generate the initial value initial, test against a condition, and if successful select the result and generate the next value next. An optional parameter, selector, may also be specified
    GeneratedList =
        List.Generate(
            ()=>[i=0, res = FnProcessOnePage(accessTokenParamStr, pagingTokenParamStr)],
            each [i]<iterationsNum and [res][Data]<>null,
            each [i=[i]+1, res = FnProcessOnePage([res][Access],[res][Next])],
            each [res][Data])
in
    GeneratedList
```

Power Query不提供传统的循环功能(例如For-loop、While-loop)和不支持递归。 一个好的解决方法是使用List.Generate实现For循环。 [此处](http://msdn.microsoft.com/query-bi/m/list-generate)记录此函数。 与列表。 生成可在页面上迭代的数据。 在迭代的每个步骤中，我们提取一页数据，保留包含下一页的分页令牌的URL，并将结果存储在生成的列表的下一项中。 来自[Datachant](https://datachant.com/2016/06/27/cursor-based-pagination-power-query/)的博客是解决此问题的有用资源。 我们的参数“最大记录页面数”旨在限制页面数量，并将其限制在实际的范围内，以避免无限循环。 另一个挑战是确保访问令牌永不过期。 使用Power Query跟踪其剩余生命周期会过于复杂。 因此，对REST API的所有调用均进行了错误检查来备份；如果出现错误，我们假定令牌已过期，我们会先续订它并重新播放调用。 如果第二次调用失败，则将向Excel通知第二次失败（在最坏的情况下，您不会收到任何数据）。 在保存查询或随时单击“刷新按钮”后，启动该查询。  在我们的示例中，提取了1364条潜在客户记录，拟合到5个列表中的5页数据。

### 塑造数据

我们需要调整数据以将所有这些记录放在一个简单的记录列表中。 有两种方法可以做到这一点：

* 使用更多代码
* 利用Power Query UI

右键单击输出网格，然后在上下文菜单中选择“至表”以将其转换为列表表。  在“至表”弹出窗口中，将默认值保留在2个选取列表中。  现在，展开生成的列表表。 现在，我们已将所有记录放在一个列表中。 以Json格式编码的记录包含字段及其关联值。 再次展开。 在弹出窗口中选择要保留的所有字段，取消选中“使用原始列名作为前缀”复选框。  瞧！ 所有记录都妥善地显示在表格中。 如果重新打开高级编辑器，我们可以看到已添加3行代码来塑造数据：

```
# "Converted to Table" = Table.FromList(GeneratedList, Splitter.SplitByNothing(), null, null, ExtraValues.Error),
# "Expanded Column1" = Table.ExpandListColumn(#"Converted to Table", "Column1"),
# "Expanded Column2" = Table.ExpandRecordColumn(#"Expanded Column1", "Column1", {"id", "updatedAt", "lastName", "email", "createdAt", "firstName"}, {"id", "updatedAt", "lastName", "email", "createdAt", "firstName"})
```

您可以使用Power Query完成更多工作，例如使用计算值创建额外的列，稍后我们将看到更多可能性。 让我们保存并关闭此查询。 现在可随时手动刷新或通过后台刷新自动刷新。

### 重定向结果

现在的问题是，将结果数据发送到何处？ 将鼠标悬停在查询上并在上下文菜单中选择菜单“加载至……”。 在弹出窗口中，您可以选择：

* &#39;Table&#39;如果要将所有成形数据发送到工作表（新数据或现有数据），
* 如果您的目标是在Power Pivot中执行进一步分析，则选择“仅创建连接”。

“将此数据添加到数据模型”复选框允许您利用Power Pivot中的数据；这是我们希望用于本文第二部分的内容。

### 管理分页

由于我们项目的目标是构建更多查询，因此让我们执行一些重构并提取可重用函数来管理分页。 创建一个名为&#x200B;**FnMktoGetPagedData**&#x200B;的新空白查询，并在高级编辑器中输入以下代码：

```
let
    FnMktoGetPagedData =(url, accessTokenParamStr, pagingTokenParamStr)=>

    let

        // Get the number of iterations (pages of 300 records) - Table Scoping
        iterationsNum = Excel.CurrentWorkbook(){[Name="Scoping"]}[Content]{0}[Max Records Pages],

        // Sub-function iterating though the REST API service result pages
        FnProcessOnePage =
        (accessTokenParamStr, pagingTokenParamStr) as record =>
            let

                // Send REST API Request
                content = Web.Contents(url& accessTokenParamStr & pagingTokenParamStr),

                // Recover Json output and watch if token is expired, in that case, regenerate access token
                newAccessTokenParamStr = if Json.Document(content)[success]=true then accessTokenParamStr else "?access_token=" & FnMktoGetAccessToken(),
                contentJson = if Json.Document(content)[success]=true then Json.Document(content) else Json.Document(Web.Contents(url & newAccessTokenParamStr & pagingTokenParamStr)),

                // Parse Json outputs: data and next page token
                data = try contentJson[result] otherwise null,
                next  = try  "&nextPageToken=" & contentJson[nextPageToken] otherwise null,
                res = [Data=data, Next=next, Access=newAccessTokenParamStr]
            in
                res,

        // Generates a list of values given four functions that generate the initial value initial, test against a condition, and if successful select the result and generate the next value next. An optional parameter, selector, may also be specified
        GeneratedList =
            List.Generate(
                ()=>[i=0, res = FnProcessOnePage(accessTokenParamStr, pagingTokenParamStr)],
                each [i]<iterationsNum and [res][Data]<>null,
                each [i=[i]+1, res = FnProcessOnePage([res][Access],[res][Next])],
                each [res][Data])
    in
        GeneratedList

in FnMktoGetPagedData
```

保存查询。 我们下一步将使用它。

### 简化的查询

让我们再次重写查询“MktoLeads”，它将调用&#x200B;**FnMktoGetPagedData**&#x200B;函数。

```
let
    // Get Url from config worksheet - Table REST_API_Authentication
    mktoUrlStr = Excel.CurrentWorkbook(){[Name="REST_API_Authentication"]}[Content]{0}[URL],
    // Get the List id - Table Scoping
    listIdStr = Number.ToText(Excel.CurrentWorkbook(){[Name="Scoping"]}[Content]{0}[List ID], "D", ""),
    // Get the Lead fields to extract - Table Leads
    LeadFieldsStr = Excel.CurrentWorkbook(){[Name="Leads"]}[Content]{0}[Lead Fields],


    // Build Multiple Leads by List Id URL
    getMultipleLeadsByListIdUrl = mktoUrlStr & "/rest/v1/list/" & listIdStr & "/leads.json?fields=" & LeadFieldsStr,

    // Build Marketo Access Token URL parameter
    accessTokenParamStr = "&access_token=" & FnMktoGetAccessToken(),

    // No initial paging token required for this call
    pagingTokenParamStr = "",

    // Invoke the multiple REST API calls through the FnMktoGetPagedData function
    result = FnMktoGetPagedData (getMultipleLeadsByListIdUrl , accessTokenParamStr, pagingTokenParamStr)

in
    result
```

如您所见，我们的查询现在非常易于阅读和维护。 我们将再次将&#x200B;**FnMktoGetPagedData**&#x200B;函数用于其他查询。

### 从定义的时间段加载特定活动

#### 通过分页获取活动

Marketo允许与潜在客户记录相关的各种活动类型。 几乎每个更改、操作或流程步骤都根据商机的活动日志进行记录，并且可以通过API检索或在智能列表以及智能营销活动过滤器和触发器中使用。 活动始终通过leadId与潜在客户记录相关联，对应于记录的ID，并具有自己的唯一整数ID。 您可以在[此处](/help/rest-api/activities.md)找到完整的REST API文档。

潜在活动类型非常多，它们可能因订阅而异，并且每种活动类型的定义都各不相同。 虽然每个活动都有自己的唯一id、leadId和activityDate，但primaryAttributeValueId和primaryAttributeValue的含义会有所不同。 我们将重点介绍“有趣的时刻”，它是一种Marketo使用ID 41跟踪的活动。 我们要解决的新挑战是：

* 我们需要启动“基于日期”的分页令牌以定义活动发生的时间段，
* 根据活动类型，以Json格式提供活动特定属性的列表，并且需要对它进行解析和扁平化以简化分析，因此数据整形比较棘手。

#### 基于日期的分页令牌

我们需要首先构建此函数以生成初始“基于日期”的分页令牌，这是确定活动查询的时间范围所必需的。 您可以在[此处](/help/rest-api/paging-tokens.md)找到有关分页令牌的文档。 创建一个名为&#x200B;**FnMktoGetPagingToken**&#x200B;的新空白查询，并在高级编辑器中输入以下代码：

```
let
    FnMktoGetPagingToken =(accessTokenStr)=>

        let
            // Get url from config worksheet - Table REST_API_Authentication
            mktoUrlStr = Excel.CurrentWorkbook(){[Name="REST_API_Authentication"]}[Content]{0}[URL],

            // Get Paging Token SinceDatetime from config worksheet - Table Scoping
            mktoPTSinceDatetimeStr = DateTime.ToText(Excel.CurrentWorkbook(){[Name="Scoping"]}[Content]{0}[Paging Token SinceDatetime], "yyyy-MM-ddThh:mm:ss"),

            // Building URL for API Call
            getPagingTokenUrl = mktoUrlStr & "/rest/v1/activities/pagingtoken.json?access_token=" & accessTokenStr & "&sinceDatetime=" & mktoPTSinceDatetimeStr,

            // Calling Marketo API Get Paging Token
            content = Web.Contents(getPagingTokenUrl),

            // Recover Json output and watch if access token is expired, in that case, regenerate it
            newAccessTokenStr = if Json.Document(content)[success]=true then accessTokenStr else "?access_token=" & FnMktoGetAccessToken(),
            pagingTokenJson = if Json.Document(content)[success]=true then Json.Document(content) else Json.Document(Web.Contents(mktoUrlStr & "/rest/v1/activities/pagingtoken.json?access_token=" & newAccessTokenStr & "&sinceDatetime=" & mktoPTSinceDatetimeStr)),

            // Parsing Paging Token
            pagingTokenStr = pagingTokenJson[nextPageToken]

        in
            pagingTokenStr

in FnMktoGetPagingToken
```

保存函数。 我们下一步将使用它。

#### 有趣的时刻活动

现在，我们编写查询“MktoInterestedMentsActivities”，它将调用&#x200B;**FnMktoGetPagedData**&#x200B;和&#x200B;**FnMktoGetPagingToken**&#x200B;函数。

```
let

    // Get Url from config worksheet - Table REST_API_Authentication
    mktoUrlStr = Excel.CurrentWorkbook(){[Name="REST_API_Authentication"]}[Content]{0}[URL],
    // Get the List id - Table Scoping
    listIdStr = Number.ToText(Excel.CurrentWorkbook(){[Name="Scoping"]}[Content]{0}[List ID], "D", ""),

    // Build Get Activities URL
    getActivitiesUrl = mktoUrlStr & "/rest/v1/activities.json?ListId=" & listIdStr & "&activityTypeIds=46",

    // Build Marketo Access Token URL parameter
    accessTokenStr = FnMktoGetAccessToken(),
    accessTokenParamStr = "&access_token=" & accessTokenStr,

    // Obtain date-based paging token used to scope in time the activities
    pagingTokenParamStr = "&nextPageToken=" & FnMktoGetPagingToken(accessTokenStr),

    // Invoke the multiple REST API calls through the FnMktoGetPagedData function
    result = FnMktoGetPagedData (getActivitiesUrl , accessTokenParamStr, pagingTokenParamStr)

in
    result
```

此查询的结果再次是列表列表，因此它需要进一步的数据处理才能用于分析。

### 塑造数据

让我们对潜在客户进行同样的成型操作：

* 右键单击输出网格，然后在上下文菜单中选择“至表”以将其转换为列表表，
* 展开生成的列表表，
* 再次展开，在弹出菜单中选择要保留的所有字段（取消选中“使用原始列名作为前缀”复选框）。

您可以看到列及其值，但“属性”列除外，该列仍包含与有趣时刻关联的特定属性列表。 让我们展开这些属性。 现在，列表已展开为记录，我们再次展开，选择所需的字段（每个属性的名称和值），并取消选中“使用原始列名作为前缀”复选框。  因此，我们的所有数据都是可见的（包括属性），但每个有趣的时刻活动都跨越三行。 这将很难用于我们的分析。  理想情况下，我们希望每个活动只显示一行，并且所有属性都显示为额外的列。 通过从表格中旋转这3个属性，我们可以轻松实现这一点。 从活动属性中选择2列“名称”和“值”，然后单击“转换”菜单中的“透视列”。 在弹出窗口中询问前进选项，然后选择“值列”= value和“不聚合”值函数。  单击“确定”，您必须为每个活动输出一行数据。  以下“数据成形”代码行应已自动附加到查询的脚本中：

```
  #"Converted to Table" = Table.FromList(result, Splitter.SplitByNothing(), null, null, ExtraValues.Error),
    #"Expanded Column1" = Table.ExpandListColumn(#"Converted to Table", "Column1"),
    #"Expanded Column2" = Table.ExpandRecordColumn(#"Expanded Column1", "Column1", {"id", "leadId", "activityDate", "activityTypeId", "campaignId", "primaryAttributeValue", "attributes"}, {"id", "leadId", "activityDate", "activityTypeId", "campaignId", "primaryAttributeValue", "attributes"}),
    #"Expanded attributes" = Table.ExpandListColumn(#"Expanded Column2", "attributes"),
    #"Expanded attributes1" = Table.ExpandRecordColumn(#"Expanded attributes", "attributes", {"name", "value"}, {"name", "value"}),
    #"Pivoted Column" = Table.Pivot(#"Expanded attributes1", List.Distinct(#"Expanded attributes1"[name]), "name", "value")
in
    #"Pivoted Column"
```

### 后续步骤

现在，您应该能够设计所需的所有查询，以访问可通过其REST API获得的任何特定Marketo数据。 我们希望您喜欢这篇文章，并希望它有助于您利用Excel和Marketo结合在一起的巨大优势。 第二篇文章中还提供了包含所有查询的示例工作簿。

### 引用

#### Power查询

* [Power Query — 概述和学习](https://support.microsoft.com/en-us/article/Power-Query-Overview-and-Learning-ed614c81-4b00-4291-bd3a-55d80767f81d)
* [Power Query公式引用](http://msdn.microsoft.com/query-bi/m/power-query-m-reference)
* [Matt Masson博客](http://www.mattmasson.com/tag/power-query/)提供了一些有关Power Query的有用资源
* [DataChant Blog](https://datachant.com/2016/06/27/cursor-based-pagination-power-query/)对于实现分页机制非常有用

由&#x200B;_菲利普_&#x200B;发布于&#x200B;_2016-10-18_

## 2016年秋季更新

在2016年秋季版本中，我们将为电子邮件v2变量和模块添加CRUD支持，并为指定帐户添加CRUD支持。 现在，您可以使用Marketo REST API在本地读取和编辑内容，以及移动、重新排序和删除这些内容。 请参阅下面的完整更新列表：

### 潜在客户数据库API

* [**指定帐户**](/help/rest-api/named-accounts.md)
   * 读取、更新和删除命名帐户的新端点
   * 已知问题：
      * 自2016年秋季版本起，无法通过API将潜在客户与指定帐户关联

### 资源API

* [**电子邮件**](https://developer.adobe.com/marketo-apis/api/asset/#operation/describeUsingGET_5)
   * 用于处理电子邮件v2变量的新端点
   * 用于操作电子邮件v2模块的新端点
   * 已知问题：
      * 对包含预测令牌的部分的查询和更新将返回错误
      * 含有包含预测令牌的内容部分的电子邮件可能无法使用API进行审批

由&#x200B;_Kenny_&#x200B;发布于&#x200B;_2016-12-07_

## 我们为什么在推特Handl3@MarketoDev退休

我们决定撤销在推特上的@MarketoDev控件。 该账户将于2011年12月9日停用。 好奇为什么？ **回顾到2014年初……**&#x200B;我们刚刚启动了Marketo开发人员网站，以帮助开发人员针对我们的API进行构建。 我们希望提高对Marketo平台的认识，并降低开发人员进入的障碍。 在做出这一努力的同时，我们还创建了@MarketoDev来与愿意与Marketo构建集成解决方案的技术合作伙伴和客户接洽。 就像任何勇敢的新事业一样，我们不确定这种情况会如何发展。 最初，我们发推文发布了新的博客文章和新的API版本。 情况不错；开发人员网站的流量增加了！ 我们也开始收到各种各样的问题，我们忠实地回答了。 **快进到2016年底……**&#x200B;随着[LaunchPoint合作伙伴](https://exchange.adobe.com/apps/browse/ec?product=MRKTO)生态系统的增长，推文活动量也增加了。 回答张贴到@MarketoDev上的大量问题对于我们这个小团队来说是一项大工作。 我们收到了越来越多的与一般产品和销售相关的问题，我们已将这些问题重定向到@Marketo或@MarketoCares。 我们还发现，140个字符根本不足以回答与发展有关的问题。 回答这些问题往往会导致冗长且涉及多个对话主题，这种方法无法扩展。 最后，我们分析了开发人员网站访客的流量来源，发现大多数流量来自免费搜索以及我们博客上的“立即订阅”功能。 出于这些原因，我们决定放弃@MarketoDev。 **从这里开始……**&#x200B;如果您是Twitter粉丝（不是粉丝），请不要担心！ 我们的企业Twitter处理@Marketo的流程仍在运行；我们的客户支持处理@MarketoCares的流程也是如此。

由&#x200B;_David_&#x200B;发布于&#x200B;_2016-12-02_

## Excel集成第2部分：使用Power Pivot和Power View构建高级Marketo报表和数据可视化图表 — 

本文是两篇文章系列中的第二篇，其中阐述了如何利用Microsoft Excel内置的Power BI技术，通过Marketo创建真正的自助式业务分析体验。 利用这些文章中涵盖的概念，您能够：

* 将数据从Marketo导入Excel
* 导入和组合来自其他源（SaaS应用程序、数据库、平面文件等）的数据
* 形成数据以满足业务需求和分析目的
* 在Excel中按需刷新数据
* 使用公式创建计算列和度量
* 创建异构数据之间的关系
* 使用数据透视表和数据透视图分析数据并构建高级报表
* 制作令人惊叹的数据可视化图表

### Power Pivot和Power View for Excel

在本篇文章中，我们提供了有关如何构建以下项目的示例：

* 使用Power Pivot利用不同Marketo数据收集之间的关系的高级Marketo报表
* 使用Power View冷却静态和动画可视化

**Power Pivot**&#x200B;是一个Excel加载项，已包含在Excel 2016中，可用于执行强大的数据分析和创建复杂的数据模型。 借助Power Pivot，您可以混合来自各种来源的大量数据、快速执行信息分析以及轻松共享见解。 从使用Power Query的不同数据源提取的数据可以发送到数据模型、Excel电子表格或同时发送到两者。 在第一篇文章中，我们从Marketo中导入并塑造了数据，并将其发送到数据模型，以便在电子表格上提供它之前执行更复杂的分析。 **Power View**&#x200B;是Excel可视化图层的替代方法。 它是一种交互式数据探索、可视化和演示体验，可鼓励直观的临时报告和仪表板。

本文中介绍的所有步骤已在Excel 2016 for Windows上进行了测试。 对于Excel 2013或Excel 2010（没有Power View），概念应该相同，但可能需要一些调整。 Power Pivot和Power View当前仅在Microsoft Windows版本的Excel 2016上可用，Office 365版本的Excel 2016不完全支持Power Pivot和Power View。

### Marketo Power工作簿

#### 下载工作簿

在第一篇文章中，我们介绍了使用Power Query技术进行数据导入和整形的过程。 我们了解了如何实施一些高级功能查询以从Marketo中提取商机和活动。 因为有些人会希望直接跳转到构建报告和可视化图表的位置，而无需编码。 此工作簿包含第一篇文章中详述的所有查询以及其他一些查询。 我们改进了错误处理，并在配置工作表中添加了一些额外的参数。 如果您阅读了第一篇文章，我们仍建议您下载Marketo Power工作簿并查看已添加的内容。

免责声明： Marketo Power工作簿不是官方的Marketo产品，因此Marketo不支持它。 您可以随意使用和扩展以满足您的个人业务需求，但要自担风险。

#### 配置工作簿

从Marketo配置工作表中填写所有必需的信息：

* **Marketo REST API身份验证：**&#x200B;必需
* **范围：**&#x200B;设置分页令牌SinceDatetime以及包含您要分析的所有潜在客户的Marketo静态列表的ID
* **潜在客户：**&#x200B;对于即将到来的报告，您必须至少指定以下潜在客户字段： `id`、`firstName`、`lastName`、`email`、创建`edAt`、`updatedAt`、`title`、`company`、`industry`、`inferredCountry`、`inferredCity`
   * 如果某个自定义字段中的城市信息更准确，则可以改用自己的字段
* **活动：**&#x200B;此处为每个活动集指定了要从Marketo数据库获取的活动类型，现在无需更改此类型。
   * 请注意，我们提供了工作簿上的实用工具查询，如果您稍后要调整此信息，则该查询会直接在Excel工作簿中列出所有现有的活动类型

请注意，您可能会看到一些与安全相关的弹出窗口。 信任外部连接，并将其设置为“公共”。 如果您看到下面的弹出窗口，请保留“匿名”Web访问内容。 对Marketo的身份验证直接由我们的自定义查询管理，因此无需启用任何其他类型的访问。

#### 下载Marketo数据

首先，确保您在Marketo配置工作表的范围区域中定义的参数不会导致下载过多数据，超过您的Marketo API每日请求限制。 准备就绪后，单击“数据”菜单中的“全部刷新”按钮，然后等待所有数据都下载到工作簿中。 如果在下载数据时显示格式错误消息（类似于“column1 not found”），则意味着一个或多个查询无法获取数据，因此格式设置也会失败。 如果错误仍然存在，请稍后重试，然后检查您的Excel版本（请勿使用Office 365中的Excel 2016）。 此外，还必须尊重Marketo平台的延迟。 如果您在静态列表或潜在客户数据中执行任何更改，则最好等待后再启动Power Queries。

### Power Pivot中的数据建模

通过单击Power Pivot菜单的“管理”按钮（位于顶部菜单栏中）打开Power Pivot（如果不可用，请检查Excel版本，Power Pivot可以作为增益集安装在某些Excel版本中）。 从Marketo下载并发送到数据模型的所有数据应可从Power Pivot窗口底部的不同选项卡访问。

### 数据分析表达式(DAX)

我们需要扩充或重新格式化某些报表的数据。 让我们使用Power Pivot数据分析表达式(DAX)将一些自定义计算定义为计算列和度量（也称为计算字段）。 请参阅引用部分中的“Power Pivot中的DAX”链接，了解有关DAX的更多信息。 确保计算区域显示在Power Pivot窗口中；如果未显示，则从Power Pivot主页菜单启用计算区域。  选择&#x200B;**MktoLeads**&#x200B;选项卡，并在潜在客户计算区域中的任意位置添加&#x200B;**个潜在客户计数**&#x200B;个度量值： **个潜在客户计数：=****DISTINCTCOUNT****([id])**。 此衡量标准基于列表中可用的独特潜在客户的ID进行计数。 它还将考虑到在报告中落实的最终筛选条件。 此指标实际上并不是必需的，因为报告能够汇总商机数量，但我们这样做是为了让商机计数具有比“MktoLeads总和”更好的名称。 它还是一个简单的示例，可让您轻松想象一些更复杂的指标，它们对特定类型的数据输入执行平均值、最小值、最大值操作（例如，得分高于50的所有潜在客户、平均得分等）。 ...)。  现在，让我们选择&#x200B;**MktoWebActivities**&#x200B;选项卡并创建三个计算列。 通过滚动到表的最右侧并单击列“添加列”来插入以下计算列。 **活动：**&#x200B;通过在表MktoActivityTypes中查找活动ID获取用户友好的活动标签。 **\=****LOOKUPVALUE****（MktoActivityTypes[名称]，MktoActivityTypes[ID]，[activityTypeId]）** **年 — 月：**&#x200B;使用更适合某些报表的模式“YYYYmm”重新格式化活动日期。 **\=****LEFT****([activityDate]，4)&amp;****MID****([activityDate]，6,2)** **日期：**&#x200B;活动日期只是我们原始查询中的字符串，请将其转换为适当的日期。 **\=****DATE****(****LEFT****([activityDate]，4)，****MID****([activityDate]，6,2)，****MID****([activityDate]，9,2)**&#x200B;现在，让我们为&#x200B;**MktoEmailActivities**&#x200B;选项卡创建三个相同的测量，再创建两个测量：**促销活动：**&#x200B;通过在MktoCampaigns表中查找营销活动ID，获取用户友好的营销活动名称。 **\=****LOOKUPVALUE****(MktoCampaigns[name]，MktoCampaigns[id]，[campaignId])** **项目：**&#x200B;通过在表MktoCampaigns中查找活动ID获取用户友好的项目名称。 MktoPrograms表可以提供有关该程序的更多详细信息，如文件夹、工作区等。 **\=****LOOKUPVALUE****(MktoCampaigns[programName]，MktoCampaigns[id]，[campaignId])**

### 实体关系

我们以前看到过一种方式，即从模型内的另一个表中查找信息以完成一些缺失的信息。 Power Pivot提供了一个功能更强大的选项，用于定义数据模型的某些表之间的关系，从而允许我们直接从报表利用这些关系。 让我们定义报告的关键关系。 从Power Pivot窗口中选择“图表视图”。 在数据模型图中跟踪以下关系：

* **MktoInterestedMomentActivities:leadId →** **MktoLeads:id**
* **MktoScoringActivities:leadId →** **MktoLeads:id**
* **MktoRevenueStageActivities:leadId →** **MktoLeads:id**
* **MktoWebActivities:leadId →** **MktoLeads:id**
* **MktoEmailActivities:leadId →** **MktoLeads： ID**

我们不会在报告中使用所有这些关系和对象，而是仅使用潜在客户、Web活动和电子邮件活动。 现在是时候制作一些报告了。

### 电子邮件性能枢纽分析图

第一个报表基于标准Excel数据透视图显示电子邮件性能KPI。 它允许我们按行业和/或营销活动过滤数据。 通过从“数据透视表”选择器中选择“数据透视图”，可以从Power Pivot菜单直接创建数据透视图。  另一种方法是直接从Excel电子表格创建数据透视图，勾选“使用此工作簿的数据模型”选项。  拖放&#x200B;**MktoEmailActivities**&#x200B;和&#x200B;**MktoLeads**&#x200B;表中的字段，如下图所示： **MktoEmailActivities.Activity →** **Legend** （这使用我们在&#x200B;**MktoEmailActivities**&#x200B;之前实施的DAX计算列） **MktoEmailActivities.Date →** **Axis** （这使用我们实施的DAX计算列&#x200B;**MKTOEmAILAcTIVITIES**&#x200B;更早） **MKTOEmAILAcTIVITIES.Id →** **∑值** **MKTOEmAILAcTIVITIES.CamPAIGN →** **筛选器** **MKTOLeADS.INDUSTRY →** **筛选器**

您可以通过在每个放置的字段上选择“值字段设置”来创建自定义名称。 在这种情况下，我们将“电子邮件活动ID”字段拖入“∑值”部分，并将其自定义名称编辑为“活动数”。 现在，让我们配置数据透视图。 直接右键单击图表，然后在上下文菜单中选择“更改图表类型”选项。 这就是我们如何为所有数据系列选择不同的图表类型。

### 具有Power View的Leads映射

第二个报告按地理位置和行业显示您的潜在客户和联系人。 此报表需要Power View。 请按照下面的引用链接打开Excel中的菜单。 或者，您只需在Excel搜索框中键入“power view”即可。 选择“插入Power View报表”。  在空白的Power View报表上，选择右侧面板上的&#x200B;**MktoLeads**&#x200B;表，然后拖放潜在客户位置字段（例如&#x200B;**inferredCity**）。 现在，主菜单中会显示“设计”菜单。

通过在Power View“设计”菜单中选择“地图”切换到地图可视化图表。 从&#x200B;**MktoLeads**&#x200B;表中拖放字段，如下图所示： **MktoLeads.industry →** **颜色** **MktoLeads.inferredCity →** **位置** **MktoLeads.Leads计数→** **∑大小**（这使用我们以前在&#x200B;**MktoLeads**&#x200B;上实施的DAXmeasure）并且您的销售机会映射已准备就绪！ 您只需要调整地图的大小，自定义标题和图例。 Power View允许您在一个电子表格上构建具有多个图形的高级仪表板。 查看“[创建令人惊叹的Power View报表](https://support.microsoft.com/en-us/article/Tutorial-Create-Amazing-Power-View-Reports-Part-1-e2842c8f-585f-4a07-bcbd-5bf8ff2243a7)”下的参考教程，了解如何使用Power View继续处理更多仪表板组件。

### 在3D地图上绘制动画的Web活动

此第三个报告在3D世界地图上按行业显示您的Lead Web活动。 我们需要一份三维地图来做这份报告。 只需在Excel搜索框中键入“3D”，然后选择“3D Map”。 从弹出窗口中创建新导览。  选择右侧面板上的气泡图。 从&#x200B;**MktoLeads**&#x200B;和&#x200B;**MktoWebActivities**&#x200B;表中拖放字段，如下图所示： **MktoLeads.industry →** **Category** **MktoLeads.inferredCity →** **位置** **MktoWebActivities.Activity →** **时间** （这使用我们在&#x200B;**上实现的DAX计算列） MKTOWebAcTIVITIES**&#x200B;更早版本。 该ID字段还可用于对活动进行计数。) **MktoWebActivities.Date →** **Time** （这使用我们在&#x200B;**MktoWebActivities**&#x200B;之前实施的DAX计算列） **MktoWebActivities.Activity**&#x200B;还可用作筛选条件以筛选出不同类型的Web活动。

使用“主题”按钮可更改3D地图的颜色方案。 打开“场景选项”以自定义动画。
你用完了3D世界地图，现在你可以快乐地给地球制作动画，并从中制作视频。

### 后续步骤

我们只是大致了解了如何使用Excel Power BI工具。 我们建议您在Web上搜索其他精彩的文章和教程，以扩展您的Excel技能并设计实现业务目标所需的报告。 我们希望您喜欢这些文章，希望它们能帮助您充分利用Excel和Marketo的强大优势。

### 引用

#### Power Pivot

* [Power Pivot：在Excel中强大的数据分析和数据建模](https://support.microsoft.com/en-us/article/Power-Pivot-Powerful-data-analysis-and-data-modeling-in-Excel-d7b119ed-1b3b-4f23-b634-445ab141b59b)
* Power Pivot中的[数据分析表达式(DAX)](https://support.microsoft.com/en-us/article/Data-Analysis-Expressions-DAX-in-Power-Pivot-bab3fbe3-2385-485a-980b-5f64d3b0f730)

#### Power View

* [在Excel 2016中打开Power View](https://support.microsoft.com/en-us/article/Turn-on-Power-View-in-Excel-2016-for-Windows-f8fc21a6-08fc-407a-8a91-643fa848729a)
* [教程：创建令人惊叹的Power View报告](https://support.microsoft.com/en-us/article/Tutorial-Create-Amazing-Power-View-Reports-Part-1-e2842c8f-585f-4a07-bcbd-5bf8ff2243a7)

由&#x200B;_菲利普_&#x200B;发布于&#x200B;_2017-02-02_

## 对Marketo API中的活动记录的重要更改

**注意：将更新此帖子，以反映由于迁移到新基础结构而对API返回的活动记录所做的更改。** **上次更新： 2018年9月13日**&#x200B;随着Marketo的新一代活动服务在2017年9月开始推出，我们将无法强制在Marketo API返回的活动、数据值更改或商机删除记录中保留唯一性或存在整数“id”字段。 为避免检索活动记录的集成服务中断，应将id字段视为可选字段。 直接转换此更改将开始影响订阅和即将发布的版本。 此更改将影响以下端点： REST API

受影响的SOAP类型为`ActivityRecord`和`LeadChangeRecord`。

### 示例

以下示例显示了将受影响的记录类型。 在这两个示例中，受影响的字段都称为“id”。

**示例REST字段： ID**

```json
  {
      "id" : 102988,
      "leadId" : 1,
      "activityDate" : "2015-01-16T23:32:19Z",
      "activityTypeId" : 1,
      "primaryAttributeValueId" : 71,
      "primaryAttributeValue" : "localhost/munchkintest2.html",
      "attributes" : [
          {
              "name" : "Client IP Address",
              "value" : "10.0.19.252"
          },
          {
              "name" : "Query Parameters",
              "value" : ""
          },
          {
              "name" : "Referrer URL",
              "value" : ""
          },
          {
              "name" : "User Agent",
              "value" : "Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/39.0.2171.95 Safari/537.36"
          },
          {
              "name" : "Webpage URL",
              "value" : "/munchkintest2.html"
          }
      ]
  }
```

**示例SOAP字段： ID**

```xml
  <activityRecord>
    <id>1030680</id>
    <activityDateTime>2013-07-09T16:44:28-05:00</activityDateTime>
    <activityType>Visit Webpage</activityType>
    <mktgAssetName>ClickDemo</mktgAssetName>
    <activityAttributes>
      <attribute>
        <attrName>Webpage ID</attrName>
        <attrType xsi:nil="true" />
        <attrValue>2547</attrValue>
      </attribute>
      <attribute>
        <attrName>Webpage URL</attrName>
        <attrType xsi:nil="true" />
        <attrValue>/ClickDemo.html</attrValue>
      </attribute>
    </activityAttributes>
    <campaign />
    <personName xsi:nil="true" />
    <mktPersonId>1089965</mktPersonId>
    <foreignSysId xsi:nil="true" />
    <orgName xsi:nil="true" />
    <foreignSysOrgId xsi:nil="true" />
  </activityRecord>
```

由&#x200B;_Kenny_&#x200B;发布于&#x200B;_2017-03-01_

## 2017年冬季更新

在2017年冬季版本中，我们将添加异步批量导入自定义对象数据的功能，以及在引导式登陆页面中处理变量的功能。 请参阅下面的完整更新列表。

### 潜在客户数据库API

#### 批量导入自定义对象

支持批量导入自定义对象的新端点。 可在[此处](/help/rest-api/custom-objects.md)找到详细信息。

#### 活动即将更改的通知

请注意，当Marketo发布其下一代活动服务时，将会发生重大变化。 此更改将影响以下端点：

SOAP

[getLeadActivity](/help/soap-api/getleadactivity.md)，[getLeadChanges](/help/soap-api/getleadchanges.md)

不再保证由这些端点返回的记录中包含的整数“id”字段是唯一的。 这将影响活动、数据值更改和潜在客户删除记录类型。 为避免检索这些记录类型的集成服务中断，应将id字段视为可选字段。

#### 推送令牌删除

添加了通过SDK API删除推送令牌的功能。 可在此处找到详细信息：[iOS](/help/mobile/push-notifications.md)、[Android](/help/mobile/push-notifications.md)。

由&#x200B;_David_&#x200B;发布于&#x200B;_2017-03-01_

## 2017年春季更新

在2017年12月发行的Spring版本中，我们将添加批量异步提取商机和活动对象数据以及处理指定帐户列表的功能。 请参阅下面的完整更新列表。

### 批量提取潜在客户

支持批量提取潜在客户的新端点。 使用各种选项指定记录选择标准。 可在[此处](/help/rest-api/bulk-lead-extract.md)找到详细信息。

### 批量提取活动

支持批量提取活动的新端点。 使用日期范围和活动列表指定记录选择标准。 可在[此处](/help/rest-api/bulk-extract.md)找到详细信息。

### 指定帐户列表

新端点支持对指定帐户列表的CRUD操作。 可在[此处](/help/rest-api/named-account-lists.md)找到详细信息。

### 其他增强功能

* [获取营销活动](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getCampaignByIdUsingGET)端点现在允许您筛选“可触发”营销活动。 这是通过将“isTriggerable=true”作为查询参数传递来实现的。
* [克隆程序](https://developer.adobe.com/marketo-apis/api/asset/#operation/cloneProgramUsingPOST)终结点现在支持包含除SMS消息之外的所有资源类型的程序。

### 离子型

您现在可以使用Marketo Mobile MME和[Ionic](https://ionicframework.com/)应用程序框架。 可在[此处](/help/mobile/ionic.md)找到详细信息。

### 取消注册推送通知令牌

在SDK中增加了一种新方法，允许您在Marketo中取消注册推送通知令牌。 当用户注销您的应用程序时，这非常有用。 有关用法，请参阅`unregisterPushDeviceToken` (iOS)或`uninitializeMarketoPush` (Android)方法[此处](/help/mobile/push-notifications.md)。

由&#x200B;_David_&#x200B;发布于&#x200B;_2017-06-16_

## 使用IFTTT和Zapier的营销人员物联网

物联网(IoT)是互联设备、设备、可穿戴设备、车辆等的互连网络。 借助嵌入式电子设备、软件、传感器和网络连接，这些对象能够收集数据并与云信息系统交换数据。 这些技术正在迅速发展和普及，很快将影响我们的生活方式、工作方式以及商业方式。 Marketo这一领先的营销参与平台已经为物联网做好了准备，能够扩展任何形式的通信渠道并与之交互。 Marketo可以跟踪已超过70种与电子邮件、Web、移动设备、CRM等相关的活动，并且还支持任何第三方系统都可以提供的[自定义活动](https://experienceleague.adobe.com/docs/marketo/using/product-docs/administration/marketo-custom-activities/create-a-custom-activity.html)。 Marketo [自定义对象](https://experienceleague.adobe.com/docs/marketo/using/product-docs/administration/marketo-custom-objects/understanding-marketo-custom-objects.html)允许跟踪与您的业务相关的各种第三方量度，并允许营销人员直接从Marketo智能营销活动过滤器和触发器利用这些量度。 为消费者实施IoT需要集中式服务器与消费者设备交互，并且该服务器将与Marketo开放平台以及REST API、自定义对象、自定义活动等功能交换数据。  — 记录了[此处](http://eto.com/)。 通过博客帖子演示并不容易。 我们将IFTT服务与Marketo集成以便为营销人员实施一些酷炫的物联网用例，例如：

* 每次有潜在客户登记参加路演时，在办公室中闪烁彩色灯光让您的营销团队振作起来
* 每次通过自动触发连接到连接电源插头的铃铛来赢得交易时，即可让您的销售团队振作起来
* 自动在LinkedIn、Facebook、Slack等社交网络上发布营销成功里程碑。 ...
* 根据以下各项自动启动营销活动：
   * 当天气警报发生时（风、温度、雨等）
   * 当《纽约时报》等报纸发表新文章时，符合某些特定标准
   * 美国参众两院投票表决时
   * 当国际空间站经过某个地点时
   * 等等。 ...

你可能会发现这些场景很有趣，但却毫无用处，但它们在这里演示了一种新的概念方法，不仅是对人进行营销的，而且是对我们这个互联世界中的事物进行营销的。 本文讨论的另一个有趣点是，如何利用开放的Web集成平台（如Zapier）作为第三方系统与Marketo之间的“服务门户”来管理身份验证，例如。

### IFTTT服务

IFTTT是“IF This Then That”的缩写。 这是一种免费的基于Web的服务，人们用它来创建简单的条件语句链，称为applet。 小程序由某些合作伙伴Web服务中发生的更改触发，因此，操作将发送到其他合作伙伴Web服务。 IFTTT于2011年由林登·蒂贝茨、杰西·塔内、斯科特·唐和亚历山大·蒂贝茨在旧金山发起。 乍一看，IFTT与[Zapier](https://zapier.com/)之类的服务相似，例如，它对消费者和IoT设备（远程设备、报警器、灯具、恒温器、汽车、打印机、手机等等）的关注度更高。  首先，必须从[IFTTT网站](https://ifttt.com/explore)为IFTT创建帐户。 请随时探索所有已有的酷炫小程序，因为这将为您提供一些其他方案设想！

### 制作者渠道

没有渠道的Web应用程序（即与IFTTT建立合作关系）必须使用Maker Channel。 使用Maker Channel，您可以创建可与任何发出或接收Web请求的设备或应用程序配合使用的Applet。 它提供了以下集成：

* 入站触发器，用于接收来自第三方系统的Web请求以触发操作
* 用于向第三方系统发出Web请求并在Internet上公开访问的出站操作

在IFTTT中，搜索“Maker”服务并单击它。  第一次，您需要通过单击“连接”按钮来激活它。  现在，Maker Channel处于活动状态。 您可以通过单击“生成器设置”按钮获取密钥：复制提供的URL并将其粘贴到浏览器以获取更多详细信息。

### 从市场直接触发IFTTT操作

首先，我们将侧重于从Marketo触发各种第三方Web服务操作。 为此，我们将使用[Marketo Webhook](https://experienceleague.adobe.com/docs/marketo/using/product-docs/administration/additional-integrations/create-a-webhook.html)。 我们将从通过IFTTT移动应用程序在手机或平板电脑上发送推送消息开始，然后实施闪烁Philips色相指示灯的物联网场景。

### Marketo Webhook

从Marketo触发事件，充当IFTTT的“if”非常简单。 您只需按照以下模式URL，向IFTTT发送POST Web请求，其中包含事件名称和您的密钥：

`<https://maker.ifttt.com/trigger/{event_name}/with/key/{secret_key}>`

Maker还可以通过Web请求传输最多3个参数。 这可以使用查询参数完成，

`<https://maker.ifttt.com/trigger/{event_name}/with/key/{secret_key}?value1={value1}&value2={value2}&value3={value3}>`

或使用最多由三个值组成的JSON主体：

`{ "value1" : "{value1}", "value2" : "{value2}", "value3" : "{value3}" }`

在Marketo中，从管理员界面创建一个新的Webhook。  为新Webhook提供以下信息：

**Webhook名称：** IFTTT计划成功

**描述：**&#x200B;从Smart Campaign中触发有关IFTTT的事件，以便程序成功

**URL：** `<https://maker.ifttt.com/trigger/{event_name}/with/key/{secret_key}?value1={{program.name}}&value2={{lead.Email> Address}}&value3={{lead.Full Name}}`

event_name，例如，使用MarketoProgramSuccess

secret_key，使用IFTTT生成器服务中的密钥

对三个可用值使用静态文本或Marketo令牌。 您可通过在程序级别定义自己的令牌并通过这些值传递它们，来推送更具交互性的消息。

**请求类型：**&#x200B;帖子

**模板：**&#x200B;留空

**请求令牌编码：**&#x200B;表单/Url

**响应类型：**&#x200B;无

无需定义响应映射。

### IFTTT小程序

在IFTTT Web门户中，从主菜单中选择“我的小程序”。  单击“新建Applet”按钮，然后单击&#x200B;**+this**&#x200B;部分。  搜索Maker服务。  创建触发器，该触发器将在Maker服务每次收到Web请求以通知发生事件时触发。 使用与Marketo Webhook的URL中指定的事件名称相同的事件名称，例如“MarketoProgramSuccess”，然后单击“创建触发器”按钮。  现在，可以通过单击部分&#x200B;**+该**来指定操作服务。  我们将从简单的操作服务开始，任何人都可以测试该服务，而无需投资任何IoT设备，即Notifications Service。 搜索并选择通知服务。
选择“发送通知”操作以向设备发送通知。  您可以利用从Marketo发送的3个值，将这些值添加为构成要素，以便向用户发送有意义的通知，如下面的示例一样……然后单击按钮“创建操作”。 查看并完成IFTTT Applet。 确保启用它。

### 测试IFTTT小程序

如果要在移动设备上获得通知，则必须首先为设备下载IFTTT应用程序。  您可以在Marketo Smart Campaign流程中使用Webhook触发Marketo项目成功事件。 请记住，Marketo Webhook专门用于触发的智能营销活动（例如，将联系人填写表单添加到列表后触发等）。  下面是手机上的IFTTT通知示例。

### 让我们使用IFTTT获取Creative

IFTT为300多个合作伙伴提供Applet Actions，因此您的应用程序和设备组合以及您的想像力是极限……让我们以Philips提供的[色调](https://www.philips-hue.com/en-us)灯为例，您可以在电子商店或在线上的任何位置购买该灯。 当Marketo触发项目成功时，以下小程序会将您的其中一个指示灯与其当前分配的颜色进行闪烁，这可能会增强您在办公室的营销团队。 我们将按照与之前相同的步骤创建一个新Applet，其中使用webhook触发Marketo，但此时我们从Philips Hue服务中选择操作。

让我们选择“闪光灯”操作。 该应用会向飞利浦色调请求你所有可用的光源，这样你就可以挑选一个闪烁。 您需要先用Philips Hue和Hue桥建立一个帐户，当然至少要有一个Hue灯泡、灯条、投影仪或灯具。  我们刚刚添加了一个新的Applet，每当潜在客户注册到路演或网络研讨会时，它都会闪烁彩色指示灯。 您的营销团队每天都会在办公室中设置此设置，为此而欢呼。

### 通过Zapie从IFTTT执行Marketo操作

现在，我们将从IFTTT平台触发一个Marketo Smart Campaign。 为此，我们将使用[Marketo REST API](/help/rest-api/rest-api.md)。 由于此API是安全的，并且在调用任何内容之前都需要OAuth2身份验证，因此我们需要通过其他平台（例如Zapier）处理该身份验证，因为IFTT不允许将API上的两个连续调用与Maker Channel链接在一起。 我们选择了[Zapier](https://zapier.com/) Web应用程序自动化服务，因为我们发布了该服务，其中已介绍Zapier并逐步说明了如何为Zapier实施自定义Marketo连接器。 其他平台（如[Workato](https://www.workato.com/integrations/marketo)）也可以是解决方案。

### Marketo Campaign

使用计划的Smart Campaign创建Marketo项目。 出于测试目的，您可以创建以下Smart Campaign作为示例： **智能列表**&#x200B;仅使用筛选器，而不使用触发器。 至少要确保你符合条件。 **流程**&#x200B;向您发送电子邮件或任何其他类型的通知。 **计划**&#x200B;确保您可以每次运行流来处理多个测试。 您可以从URL获取Smart Campaign Id。 示例： _https://{{marketo_url}}/#SC4289A1_ — 智能营销活动ID应为4289。 您可以通过Marketo REST API触发此营销活动。 例如，您可以使用适用于Chrome的[Postman](https://www.postman.com/)插件并发送以下两个连续HTTPS调用： **身份验证步骤：**

`https://{{Your Munchkin_Account_id}}.mktorest.com/identity/oauth/token?grant_type=client_credentials&client_id={{Your_Client_Id}}&client_secret={{Your_Client_Secret}}`

从JSON响应中恢复访问令牌。 **营销活动启动步骤：**

`https://{{Your_Munchkin_Account_id}}.mktorest.com/rest/v1/campaigns/{{Campaign_Id}}/schedule.json?access_token={{access_token}}`

### 用于启动Marketo促销活动的中间Zapier自定义连接器

我们需要构建一个自定义Zapier连接器，该连接器可通过Marketo REST API进行身份验证并启动我们的Smart Campaign。

* 先决条件
* 适用于Zapier的Marketo连接器的实施
* 使用其他标题，如“Marketo营销活动”
   * 执行“身份验证”步骤
   * 执行“触发器”步骤（对于Zapier测试而言是必需的）
   * 执行以下特定的“操作”步骤，负责启动Marketo营销活动，如下所述：

数据操作端点URL的发送位置：

`https://{{munchkin_account_id}}.mktorest.com/rest/v1/campaigns/{{CampaignId}}/schedule.json`

将其他可选字段留空。

#### 脚本API

Zapier的脚本功能允许您处理在您的应用程序API和Zapier之间交换的请求和响应。 您可以在发送HTTP请求之前修改请求，并且可以在Zapier执行任何操作之前分析响应。 我们需要它来完成我们的自定义“会话身份验证”身份验证。 有关更多信息，请参阅原始文章。 复制以下代码与原始代码非常相似，我们只是更改了操作方法：

```javascript
var Zap = {

 get_session_info: function(bundle) {

 console.log('Entering get_session_info method ...');

 var access_token,
 access_token_request_payload,
 access_token_response;


 // Assemble the meta data for our Access Token swap request
 console.log('building Request with client_id=' + bundle.auth_fields.client_id + ', and client_secret=' + bundle.auth_fields.client_secret);
 access_token_request_payload = {
 method: 'POST',
 url: 'https://' + bundle.auth_fields.munchkin_account_id + '.mktorest.com/identity/oauth/token',
 params: {
 'grant_type' : 'client_credentials',
 'client_id' : bundle.auth_fields.client_id,
 'client_secret' : bundle.auth_fields.client_secret
 },
 headers: {
 'Content-Type': 'application/json', // Could be anything.
 Accept: 'application/json'
 }
 };

 // Fire off the Access Token request.
 access_token_response = z.request(access_token_request_payload);

 // Extract the Access Token from returned JSON.
 access_token = JSON.parse(access_token_response.content).access_token;
 console.log('New Access_Token=' + access_token);

 // This will be mixed into bundle.auth_fields in future calls.
 //bundle.auth_fields.access_token=access_token;
 return {'access_token': access_token};
 },

 test_trigger_pre_poll: function(bundle) {

 console.log('Entering test_trigger_pre_poll method ...');

 bundle.request.params = {
 'access_token':bundle.auth_fields.access_token
 };

 return bundle.request;

 },

 test_trigger_post_poll: function(bundle) {

 console.log('Entering test_trigger_post_poll method ...');

 var data = JSON.parse(bundle.response.content);
 if ((!data.success)&&((data.errors[0].code=="601")||(data.errors[0].code=="600"))){
 console.log('Access Token expired or invalid, requesting new one - data.success=' + data.success + ', data.errors[0].code=' + data.errors[0].code);

 throw new InvalidSessionException(); // Calling get_session_info() to regenerate Access Token
 }

 return JSON.parse(bundle.response.content);
 },

 launch_campaign_pre_write: function(bundle) {

 bundle.request.params = {'access_token':bundle.auth_fields.access_token};
 return bundle.request;
 },

 launch_campaign_post_write: function(bundle) {

 var data = JSON.parse(bundle.response.content);
 if ((!data.success)&&((data.errors[0].code=="601")||(data.errors[0].code=="600"))){
 console.log('Access Token expired or invalid, requesting new one - data.success=' + data.success + ', data.errors[0].code=' + data.errors[0].code);
 throw new InvalidSessionException(); // Calling get_session_info() to regenerate Access Token
 }
 return JSON.parse(bundle.response.content);
 }

};
```

##### 新建Zap

在Zapier仪表板中，单击“生成新的Zap”按钮。 **触发器**

* 选择“Webhooks by Zapier”触发器应用程序
* 勾选“Catch Hook”
* 无需摘下子键
* Zapier生成了一个自定义webhook URL供您发送请求，以便在某处安全
* 通过启动下面的“调用Zapier Webhook的IFTTT小程序”方案，测试webhook URL。 这允许Zapier了解webhook有效负荷，并允许您为操作分配营销活动ID

**操作**

* 选择之前创建的Marketo Campaign连接器
* 选择唯一可用的操作： **启动促销活动**
* 连接到您的Marketo帐户，并填写身份验证参数(Munchkin帐户ID、客户端ID、客户端密码)
* 编辑模板，并将触发器中的促销活动ID关联到“启动促销活动”促销活动ID参数
* 测试该步骤并检查是否启动了Marketo营销活动

### 调用Zapier Webhook的IFTTT小程序

我们先来看一个容易测试的简单场景。 我们在IFTT中选择将每小时启动一次Marketo Campaign的日期和时间触发器。 该操作是一个发布到Zapier Webhook URL并传递Smart Campaign ID的Web请求。 确保Zapier Zap和IFTTT小程序处于活动状态，并测试是否一切按预期运行。

### 让我们用IFTTT获取Creative

IFTT为300多个合作伙伴提供了Applet Triggers，因此您的应用程序和设备组合以及您的想像力同样有限……让我们举一个例子，看一看[Weather Underground](https://www.wunderground.com/)服务，我们将使用该服务在天气警报时启动Marketo促销活动。 以下触发程序将在宣布Rain条件时启动。 然后将触发器与Maker Webhook操作相关联，就像之前填充Zapier webhook参数一样。  瞧，现在需要好雨来再次确认这是否真的有用。

我们希望您能乐于运用本文提供的概念。 但最重要的是，我们认为它将帮助任何希望将Marketo与其他第三方系统集成的人员，这要归功于本文中的关键概念：

* MARKETO REST API
* Marketo Webhooks
* 如何利用开放的Web集成平台（如Zapier）作为第三方系统与Marketo之间的“服务门户”，以管理身份验证

由&#x200B;_菲利普_&#x200B;发布于&#x200B;_2017-06-20_

## 2017年夏季更新

在2017年夏季版本中，我们将发布项目API的一些次要增强功能。

### 浏览程序

通过添加可选的earliestUpdatedAt和latestUpdatedAt参数，我们将按日期范围获取程序的功能添加到[获取程序](https://developer.adobe.com/marketo-apis/api/asset/#operation/browseProgramsUsingGET)端点。 您可以使用所选的日期时间设置这两个参数中的任意一个，以仅返回在两个日期时间之间创建或更新的程序。 这对于检索新的和更新的营销宣传材料集非常有用，其中最重要的是对于翻译和商业智能用例。

由&#x200B;_Kenny_&#x200B;发布于&#x200B;_1970-01-01_

## 使用Marketo Cloud功能扩展Google业务逻辑 — 

本文基于以下简单示例，提出了一个使用Marketo Google Cloud Platform (GCP)扩展具有某些业务逻辑功能的解决方案：Marketo潜在客户记录上的3个自定义字段：

* **OnLinePreference**：增量分数，表示潜在客户/客户对在线通信的喜好。
* **OfflinePreference**：增量分数，表示潜在客户/客户对离线通信的偏好。
* **首选项**：由GCP计算的字段，如果离线分数高于在线分数，则显示“离线”，反之则显示“在线”

此技术为更高级的业务逻辑以及最终对外部Web服务开放，从而在Marketo中转换和整合成果。  

### 关于Google Cloud平台和函数

[Google Cloud Platform](https://cloud.google.com/) (GCP)是一套云计算服务，运行的基础架构与Google内部用于最终用户产品(如Google Search和YouTube)的基础架构相同。 除了一组管理工具外，它还提供一系列模块化云服务，包括计算、数据存储、数据分析、机器学习、大数据等等。 我们本可以根据自己的需要使用许多不同的GCP服务，例如计算引擎、应用程序引擎或Kubernetes引擎，但我们出于以下主要优势而选择了[云功能](https://cloud.google.com/functions)&#x200B;(仍位于Beta中)：

* 在无服务器云计算中，可以按需旋转逻辑以响应HTTP调用等事件。
* 减轻服务器维护和部署带来的大部分困难。
* 具成本效益，因为您只为每次函数调用支付GCP费用，而不为保持服务器正常运行而支付。
* 实施简单快速，因为您只关注应用程序逻辑。
* 自动扩展，可适应非常高的工作负载。

请查看[GCP网站](https://cloud.google.com/)，了解有关此技术及其定价的详细信息。 通常，本教程不应产生任何重要成本，并且完全符合GCP试用免费信用。  

### 准备Google Cloud环境

您需要Google Cloud帐户。 你可以免费试用GCP，但信用额度足以运行本教程，只需单击[GCP网站](https://cloud.google.com/)上的“免费试用”按钮即可。 按照Google的[HTTP教程](https://cloud.google.com/functions/docs/calling)中的“开始之前”部分的所有步骤进行操作：

1. 创建云平台项目：转到管理资源页面
1. 为项目启用计费： [启用计费](https://cloud.google.com/billing/docs/how-to/modify-project?visit_id=638816637273392093-1926929734&rd=1)
1. 启用云函数API：[启用API](https://accounts.google.com/InteractiveLogin?continue=https://console.cloud.google.com/flows/enableapi?apiid%3Dcloudfunctions%26redirect%3Dhttps://cloud.google.com/functions/docs/tutorials/http&followup=https://console.cloud.google.com/flows/enableapi?apiid%3Dcloudfunctions%26redirect%3Dhttps://cloud.google.com/functions/docs/tutorials/http&osid=1&passive=1209600&service=cloudconsole&ifkv=ASKV5Mh81NGNsqcJqhx7hst0KFnyA0MJ-2zay8ovyluBfpvDoM820nF9Wq_SKbC1m_sjQvvRNoKSuA)
1. [安装和初始化Cloud SDK](https://cloud.google.com/sdk/docs/)
1. 更新和安装Gcloud组件

   **Gcloud组件更新和&amp;** **Gcloud组件安装测试版**

1. 为Node.js开发准备环境： [转到安装指南](https://cloud.google.com/nodejs/docs/setup)

### scoreCompare云函数的实施

1. 创建云存储段以暂存云函数文件。 您可以使用命令行完成此操作：

   `gsutil mb gs://[YOUR_STAGING_BUCKET_NAME]`

   或从Google Cloud Web界面中，选择您的项目并单击存储菜单：
   * 为存储段提供一个唯一的名称
   * 选择默认存储类
   * 选择最适合的区域位置

1. 在本地系统上为应用程序代码创建一个目录。
1. 在此目录中使用以下JavaScript代码创建一个“index.js”文件：此代码非常易于理解。 它解析JSON中HTTP请求正文的两个输入参数，进行处理并在JSON中编码HTTP响应。

```javascript
 /\*\*
     \* HTTP scoreCompare Cloud Function.
     \*
     \* @param {Object} req Cloud Function request context.
     \* @param {Object} res Cloud Function response context.
     \*/
    exports.scoreCompare = function scoreCompare (req, res) {
     var onlineScore=parseInt(req.body.onlineScore);
     var offlineScore=parseInt(req.body.offlineScore);
     console.log('/scoreCompare: got values onlineScore =' + onlineScore + ', offlineScore =' + offlineScore);
     var result;
     if (onlineScore>offlineScore) {result = 'online';} else {result = 'offline';}
     console.log('/scoreCompare: and result is ' + result);
     res.status(200).json({output: result}).end();
    };
```

部署函数scoreCompare与HTTP触发器。 从目录中运行以下命令：

**gcloud测试版函数部署[FUNCTION] —stage-bucket [YOUR_STAGING_BUCKET_NAME] —trigger-http**

其中[YOUR_STAGING_BUCKET_NAME]是暂存云存储段的名称。 在我们的示例中：

**gcloud测试版函数部署scoreCompare —stage-bucket mktostorage —trigger-http**

1. 请注意控制台输出中的云函数URL (httpsTrigger URL)，它类似于： `https://[YOUR_REGION]-[YOUR_PROJECT_ID].cloudfunctions.net/[FUNCTION]`，其中

   * [YOUR_REGION]是部署函数的区域。 当您的函数完成部署时，可在终端中看到此项。
   * [YOUR_PROJECT_ID]是您的云项目ID。 当您的函数完成部署时，可在终端中看到此项。
   * [FUNCTION]是您的函数名称。

   在我们的示例中：[**https://us-central1-marketo-cloud-logic.cloudfunctions.net/scoreCompare**](https://us-central1-marketo-cloud-logic.cloudfunctions.net/scoreCompare)
1. 使用诸如[Postman](https://www.postman.com/)之类的工具测试您的功能：
   * HTTP动词： POST
   * URL： [https://us-central1-marketo-cloud-logic.cloudfunctions.net/scoreCompare](https://us-central1-marketo-cloud-logic.cloudfunctions.net/scoreCompare)
   * 标头： content-type = application/json
   * 正文： {&quot;onlineScore&quot;:110，&quot;offlineScore&quot;:200}输出应提供： {&quot;output&quot;： &quot;offline&quot;}。

### 从Marketo的Webhook调用云函数

必须在Marketo中的Lead记录上创建以下三个自定义字段：

* **OnlinePreference**：整数
* **OfflinePreference**：整数
* **首选项**：字符串

使用您的“scoreCompare”云函数URL和自定义字段的令牌，从Marketo管理界面创建以下webhook。 使用Marketo触发的智能营销活动测试webhook。

* **只能从触发的智能营销活动中调用Marketo webhook，而不能从批处理智能营销活动中调用。**
* **如果不使用Cloud功能，请将其删除或删除整个项目，以免导致Google Cloud Platform帐户产生费用。**

我们希望本教程值得您花些时间，并能让您考虑涉及复杂处理和第三方服务的更高级方案。 一个很好的例子就是利用Google Cloud AI，Google的机器学习服务。 例如，您可以从Marketo表单中解析一些自由文本，并请求Google自然语言API揭示文本结构和含义，然后在Marketo中保存此分析；只需打开闸门即可获取想法。

由&#x200B;_菲利普_&#x200B;发布于&#x200B;_2017-11-21_

## 2017年秋季更新

在2017年秋季版本中，我们将发布几项资产API增强功能。 请参阅下面的完整更新列表。

### 按日期范围浏览程序

我们已将按日期范围获取程序的功能添加到我们的[获取程序](https://developer.adobe.com/marketo-apis/api/asset/#operation/cloneEmailUsingPOST)端点。 可使用`earliestUpdatedAt`和`latestUpdatedAt`参数完成此操作。 您可以使用所选的日期时间设置这两个参数中的任意一个，以仅返回在两个日期时间之间创建或更新的程序。

### 预览电子邮件

您现在使用[获取电子邮件完整内容](https://developer.adobe.com/marketo-apis/api/asset/#operation/getEmailFullContentUsingGET)端点预览电子邮件，该端点返回电子邮件的序列化HTML版本。 所有令牌、代码段、动态内容和嵌入的组件都将完全呈现。 可以传递可选的&#x200B;**leadId**&#x200B;参数来模拟给定的潜在客户。

### 替换电子邮件2.0的HTML

我们已添加[更新电子邮件完整内容](https://developer.adobe.com/marketo-apis/api/asset/#operation/createEmailFullContentUsingPOST)终结点，以便允许您替换HTML电子邮件内容的块。 如果您使用HTML Email 2.0编辑器编辑Marketo电子邮件的Marketo代码，则电子邮件与其模板之间的关系断开，有关[此处](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/email-marketing/general/functions-in-the-editor/edit-an-emails-html)的详细信息。 使用此端点，您可以以编程方式更新关系已断开的电子邮件的HTML内容。 此外，我们还修改了所有其他与电子邮件生命周期相关的端点，以与关系已断开的电子邮件兼容：

* 批准电子邮件草稿
* 取消批准电子邮件
* 删除电子邮件
* 放弃电子邮件草稿
* 克隆电子邮件
* 更新电子邮件元数据

### 其他增强功能

* 日期范围过滤器的最大时间范围已增加到31天。 这与[批量潜在客户提取筛选器](/help/rest-api/bulk-lead-extract.md) （createdAd或updatedAt）和[批量活动提取筛选器](/help/rest-api/bulk-activity-extract.md) (createdAt)有关。

由&#x200B;_David_&#x200B;发布于&#x200B;_2017-12-15_

## 测试 — 社区上的外部视频链接

[电子邮件可投放性功能包教程视频](https://nation.marketo.com:443/t5/product-space-archive-videos/email-deliverability-power-pack-tutorial-video/m-p/283550)  

由&#x200B;_David_&#x200B;发布于&#x200B;_1970-01-01_

## 2018年冬季更新

在2018年冬季版本中，我们将发布一些API增强功能。 请参阅下面的完整更新列表。

### 激活/取消激活触发器营销活动

我们添加了激活和停用触发器营销活动的功能，这可以简化项目模板自动化的过程。 这是通过调用两个新添加的端点来实现的：[激活Smart Campaign](https://developer.adobe.com/marketo-apis/api/asset/#operation/activateSmartCampaignUsingPOST)、[停用Smart Campaign](https://developer.adobe.com/marketo-apis/api/asset/#operation/deactivateSmartCampaignUsingPOST)。 有关详细信息，请参阅[促销活动](/help/rest-api/assets.md)文档中的“触发器”部分。

### 按名称获取计划

向[按名称获取程序](https://developer.adobe.com/marketo-apis/api/asset/#operation/getProgramByNameUsingGET)端点添加了两个参数，以便检索程序成本和程序标记。 有关详细信息，请参阅&#x200B;**程序**&#x200B;文档中的&#x200B;**includeCosts**&#x200B;和[includeTags](/help/rest-api/assets.md)参数。

### 其他增强功能

批量提取API现在具有“工作区感知功能”。 为[自定义服务](/help/rest-api/custom-services.md)创建仅API用户时，必须为一个或多个工作区选择具有API访问权限的用户角色。 以前，自定义服务被授予访问所有工作区的权限。 现在，自定义服务仅被授予在仅API用户创建期间选择的工作区的访问权限。

由&#x200B;_David_&#x200B;发布于&#x200B;_2018-03-02_

## 2018年春季更新

在2018年春季版本中，我们将发布新的REST API和Web跟踪隐私增强功能。 请参阅下面的完整更新列表。

REST API

### 静态列表CRUD

允许用户远程创建、读取、更新和删除静态列表记录。 支持通过REST API管理静态列表的整个生命周期，包括填充和维护成员资格。 可在[此处](/help/rest-api/static-lists.md)找到详细信息。

### 自定义活动元数据

允许用户远程创建自定义活动记录。 支持通过REST API管理自定义活动的整个生命周期，包括处理类型和类型属性。 可在[此处](/help/rest-api/activities.md)找到详细信息。

### 智能列表元数据

允许用户远程读取、克隆和删除智能列表记录。 通过REST API启用智能列表管理。 可在[此处](/help/rest-api/smart-lists.md)找到详细信息。

### Web跟踪隐私

Munchkin JavaScript Web跟踪代码已得到增强，现包括以下与隐私相关的增强功能：

* Opt-Out — 允许访客永久选择退出Web跟踪的功能。 可在[此处](/help/javascript-api/lead-tracking.md)找到详细信息。
* IP地址匿名化 — 通过提供对Web访客的IP地址进行匿名化的功能，遵守当地和国际隐私法规。 在&#x200B;**此处，查看** anonymizeIP[配置参数](/help/javascript-api/configuration.md)。

### 其他增强功能

* 现在，当指定非根父项且maxDepth=1时，[Get Folders](https://developer.adobe.com/marketo-apis/api/asset/#operation/getFolderUsingGET)终结点返回所有文件夹。
* 现在，[按Id](https://developer.adobe.com/marketo-apis/api/asset/#operation/getLandingPageByIdUsingGET)获取登陆页面端点在所有情况下都返回协议的URL属性(http://或https://)。

由&#x200B;_David_&#x200B;发布于&#x200B;_2018-06-29_

## 2018年夏季更新

2018年夏季版本主要由维护版本组成，包括次要增强功能和缺陷解决方案。 请参阅下面的完整更新列表。

### REST API

* 添加了对最初不必要地忽略的电子邮件处置字段的支持。 这些字段现在可以相应地在REST中读取和写入。
   * 已列入阻止列表
   * 营销暂停
   * 电子邮件暂停
   * 相对紧迫性
* 按筛选器类型[的](https://developer.adobe.com/marketo-apis/api/lead-database-endpoint-reference/#/Leads/getLeadsByFilterUsingGET)获取潜在客户Id终结点现在支持将leadPartionId作为filterType。

### 缺陷分辨率

**REST终结点**

**描述**

[批准计划](https://developer.adobe.com/marketo-apis/api/asset/#operation/approveProgramUsingPOST)

如果在创建程序时将“阻止非操作电子邮件”设置为false，则对“批准程序”的调用将重置为true。

[批量提取](/help/rest-api/bulk-extract.md)

提取输出文件中的某些Unicode字符已损坏。

[克隆程序](https://developer.adobe.com/marketo-apis/api/asset/#operation/cloneProgramUsingPOST)

* 如果克隆了电子邮件程序，则无论初始设置如何，SmartList筛选器逻辑在生成的程序中都会重置为“全部”。
* 如果您尝试克隆包含静态列表（已删除）的程序，则会收到709错误：“以下资源不受支持:List”。
* 如果尝试跨工作区克隆程序，则会收到“无法克隆程序”错误611。

[按Id获取静态列表](https://developer.adobe.com/marketo-apis/api/asset/#operation/getStaticListByIdUsingGET)

如果自定义服务具有只读资产角色权限，您将收到603“访问被拒绝”错误。

[将潜在客户推送到Marketo](https://developer.adobe.com/marketo-apis/api/mapi/#operation/pushToMarketoUsingPOST)

如果在&#x200B;**input**&#x200B;数组潜在客户对象中指定了&#x200B;**Cookie**&#x200B;属性，则以前的匿名活动未与新创建的潜在客户关联。

[计划营销活动](https://developer.adobe.com/marketo-apis/api/mapi/#operation/scheduleCampaignUsingPOST)

如果您指定了遥远的&#x200B;**runAt**&#x200B;日期，则营销活动从不运行，并且返回了&#x200B;**success=true**。 现在，如果&#x200B;**runAt**&#x200B;日期晚于未来2年，则会返回错误[1042](/help/rest-api/error-codes.md)。

[同步潜在客户](https://developer.adobe.com/marketo-apis/api/mapi/#operation/syncLeadUsingPOST)

如果您指定了`action=createDuplicate`和`externalCompanyId`参数（以将新的潜在客户与现有公司关联），则该潜在客户与空的公司关联（而不是指定的公司）。

#### 其他

* 已从所有终结点响应中删除以下数据更改值： mktoClientReqId。 这仅供内部使用。
* 添加了错误查找功能。 在搜索框中输入REST API错误代码，然后从下面的自动完成列表中选择以跳转到错误说明。
* 添加了[终结点引用](/help/rest-api/endpoint-reference.md)页面。 这是一个可排序的列表，列出了一个位置的所有REST API端点。 您还可以使用此页面生成应用程序所需的最小权限集。 这在创建自定义服务时可方便使用。

由&#x200B;_David_&#x200B;发布于&#x200B;_2018-10-12_

## 2018年秋季更新

2019年秋季版本主要是维护版本，包含一些次要增强功能和缺陷解决方案。 请参阅下面的完整更新列表。

### 增强功能

* 添加了对[资产API](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/email-marketing/general/email-cc)的[电子邮件抄送字段](/help/rest-api/assets.md)支持。 抄送字段设置在审批/克隆操作期间按预期传播（电子邮件或电子邮件模板草稿审批、电子邮件或项目群克隆）。 现在，所有与电子邮件相关的端点都返回&#x200B;**ccFields**&#x200B;属性中的“抄送字段”值。 在下面的响应中向下滚动以查看示例。 此更改会影响以下端点：[按ID获取电子邮件](https://developer.adobe.com/marketo-apis/api/asset/#operation/getEmailByIdUsingGET)、[按名称获取电子邮件](https://developer.adobe.com/marketo-apis/api/asset/#operation/getEmailByNameUsingGET)、[获取电子邮件](https://developer.adobe.com/marketo-apis/api/asset/#operation/getEmailUsingGET)、[批准电子邮件草稿](https://developer.adobe.com/marketo-apis/api/asset/#operation/approveDraftUsingPOST)、[批准电子邮件模板草稿](https://developer.adobe.com/marketo-apis/api/asset/#operation/approveDraftUsingPOST_1)、[克隆电子邮件](https://developer.adobe.com/marketo-apis/api/asset/#operation/cloneEmailUsingPOST)、[克隆程序。](https://developer.adobe.com/marketo-apis/api/asset/#operation/cloneProgramUsingPOST)

```json
{
    "success": true,
    "errors": [],
    "requestId": "cc96#166e836348b",
    "warnings": [],
    "result": [
        {
            "id": 2061,
            "name": "Test Email",
            "description": "This is a test",
            "createdAt": "2018-11-06T05:27:10Z+0000",
            "updatedAt": "2018-11-06T08:33:16Z+0000",
            "url": "<https://app-sjqe.marketo.com/#EM2061A1LA1>",
            "subject": {
                "type": "Text",
                "value": "This is a test with CC Fields"
            },
            "fromName": {
                "type": "Text",
                "value": "Tommy Tester"
            },
            "fromEmail": {
                "type": "Text",
                "value": "<tommy.tester@marketo.com>"
            },
            "replyEmail": {
                "type": "Text",
                "value": "<tommy.tester@marketo.com>"
            },
            "folder": {
                "type": "Program",
                "value": 7494,
                "folderName": "Initiative"
            },
            "operational": false,
            "textOnly": false,
            "publishToMSI": false,
            "webView": false,
            "status": "approved",
            "template": 1218,
            "workspace": "Default",
            "version": 2,
            "autoCopyToText": true,
            "ccFields": [
                {
                    "attributeId": "855",
                    "objectName": "lead",
                    "displayName": "emailcc",
                    "apiName": "emailcc"
                },
                {
                    "attributeId": "857",
                    "objectName": "lead",
                    "displayName": "leadDetails",
                    "apiName": "leadDetails"
                },
                {
                    "attributeId": "859",
                    "objectName": "company",
                    "displayName": "headquarters",
                    "apiName": "headquarters"
                }
            ]
        }
    ]
}
```

### 缺陷分辨率

* 已调整[资产API](https://experienceleague.adobe.com/zh-hans/docs/marketo/using/home)对[多个品牌化域](/help/rest-api/assets.md)的支持。 以前，在批准电子邮件草稿、克隆电子邮件或克隆项目时，不会传播多个品牌策略域设置。 此问题已得到纠正。 此更改会影响以下端点：[批准电子邮件草稿](https://developer.adobe.com/marketo-apis/api/asset/#operation/approveDraftUsingPOST)、[克隆电子邮件](https://developer.adobe.com/marketo-apis/api/asset/#operation/cloneEmailUsingPOST)、[克隆程序。](https://developer.adobe.com/marketo-apis/api/asset/#operation/cloneProgramUsingPOST)
* 添加了[apiOnly](/help/javascript-api/configuration.md)配置设置。 默认情况下，包含Munchkin标记的网页在浏览器中加载网页时触发“访问网页”事件。 在某些情况下，这是不可取的。 例如，需要完全控制何时触发此事件的单页Web应用程序。 为了支持此用例，我们添加了一个新的&#x200B;**apiOnly**&#x200B;配置设置。 如果设置为true，则Munchkin标记在页面加载期间不会生成“访问网页”活动。
* 添加了[domainSelectorV2](/help/javascript-api/configuration.md)配置设置。 默认情况下，Munchkin标记无法正确处理在包含双字母[国家/地区代码顶级域](https://en.wikipedia.org/wiki/Country_code_top-level_domain)的网站上托管的网页（示例： .io、.co、.ly）。 这会导致Munchkin Cookie域属性设置不正确。 为了获得更好的开箱即用体验，我们添加了新的&#x200B;**domainSelectorV2**&#x200B;配置设置。 如果设置为true，则使用改进的算法自动设置Munchkin Cookie域属性。
* 已调整[选择退出](/help/javascript-api/lead-tracking.md) Cookie域。 在某些情况下，Munchkin选择退出Cookie (mkto_opt_out)的域属性设置不正确。 Munchkin选择退出Cookie现在使用与Munchkin Cookie (_mkto_trk)相同的逻辑来确定域Cookie属性，包括遵守&#x200B;**domainLevel**&#x200B;配置设置。
* Android应用程序开发人员现在可以直接将Google的[Firebase Cloud Messaging](https://firebase.google.com/docs/cloud-messaging/) (FCM)与此SDK结合使用。 可在[此处](/help/mobile/installation.md)找到详细信息。

由&#x200B;_David_&#x200B;发布于&#x200B;_2018-12-07_

## 2019年春季更新

2019年春季版本主要是一个维护版本，包含一些次要增强功能和缺陷解决方案。 请参阅下面的完整更新列表。

由&#x200B;_David_&#x200B;发布于&#x200B;_2019-03-19_

## 2019年6月更新

2019年6月版本主要是一个维护版本，包含一些次要增强功能和缺陷解决方案。 请参阅下面的完整更新列表。

### REST API

1. 向“批量导出”状态端点添加了一个校验和。 您可以将校验和与检索文件的哈希进行比较，以验证检索文件的完整性。 校验和是导出文件的SHA-256散列，在导出作业完成时存储在fileCheckSum属性中。

以下端点返回校验和： [获取导出潜在客户作业状态](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getExportLeadsStatusUsingGET)，[获取导出潜在客户作业](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getExportLeadsFileUsingGET)，[获取导出活动作业状态](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getExportLeadsStatusUsingGET)，[获取导出活动作业](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getExportActivitiesUsingGET)

#### 缺陷分辨率

1. 修复了在将十进制数字导入整数字段时[批量自定义对象导入](/help/rest-api/bulk-custom-object-import.md)的问题。 在修正之前，通过指定整数部分并舍弃小数部分，将小数转换为整数（例如，5.432转换为5）。 现在，会为每个包含数据不匹配的行生成“字段Source ID中的数据类型无效”错误。
1. 修复了使用[克隆程序](https://developer.adobe.com/marketo-apis/api/asset/#operation/cloneProgramUsingPOST)端点创建的电子邮件程序在某些情况下不遵循通信限制设置的问题。
1. 修复了[批准登陆页面草稿](https://developer.adobe.com/marketo-apis/api/asset/#operation/approveLandingPageUsingPOST)终结点返回了611的问题。 登陆页面包含电子邮件取消订阅表单时出现“系统错误”。
1. 修复了[批准登陆页面草稿](https://developer.adobe.com/marketo-apis/api/asset/#operation/approveLandingPageUsingPOST)终结点返回了611的问题。 使用[克隆登陆页面](https://developer.adobe.com/marketo-apis/api/asset/#operation/cloneLandingPageUsingPOST)端点克隆登陆页面时出现“系统错误”。

#### 弃用

1. 作为[Email Editor 1.0弃用](https://nation.marketo.com:443/t5/knowledgebase/email-editor-1-0-is-being-deprecated-june-18th/ta-p/250666)的一部分，1.0 Email Assets将在2019年底变为只读。 所有1.0电子邮件Assets都必须转换为2.0，作为describeDiscard电子邮件草稿，位于[此处](https://nation.marketo.com:443/t5/knowledgebase/email-editor-1-0-is-being-deprecated-june-18th/ta-p/250666)。 为帮助开发人员准备该事件，我们向尝试修改1.0 Email Assets的所有电子邮件相关端点添加了警告。 以下是包含警告的示例响应：

`{` `"success": true,` `"errors": [],` `"requestId": "15c57#16b338d6e75",` `"warnings": [` `"This is a v1 email asset. API support for modifying v1 emails is being dropped, and this operation will not work on v1 emails in the future. To avoid service interruptions, upgrade this and related assets by editing them in the User Interface."` `],` `"result": [` `"{\"service\":\"sendTestEmail\",\"result\":true}"` `]` `}`

以下与电子邮件相关的端点返回警告：

* 更新电子邮件完整内容
* 更新电子邮件内容
* 发送示例电子邮件
* 取消批准电子邮件
* 克隆程序
* 克隆电子邮件
* 批准电子邮件草稿
* 更新电子邮件动态内容部分
* 更新电子邮件元数据
* 批准项目

电子邮件脚本(Apache Velocity)

#### 弃用

1. 出于安全原因，禁用了Velocity脚本功能的子集。 这包括以下方法和变量： getClass()、$class、$context、$text。 可在[此处](https://nation.marketo.com:443/t5/knowledgebase/unsupported-velocity-tools-disabled-in-june-2019-release/ta-p/251177)找到更多信息。

由&#x200B;_David_&#x200B;发布于&#x200B;_2019-06-14_

## 2019年8月更新

2019年8月，我们将发布新的REST API，增强现有API并解决缺陷。 请参阅下面的完整更新列表。

### 增强功能

1. 增强了Smart Campaign生命周期功能。 添加了新端点，以允许您对Smart Campaigns执行以下操作：按名称获取、创建、更新、克隆和删除。 完整信息可在[此处](/help/rest-api/smart-campaigns.md)找到。
1. 增强了智能列表端点以改进查询功能。
   1. 传递[includeRules](https://developer.adobe.com/marketo-apis/api/asset/#operation/getSmartListByIdUsingGET)布尔参数时，**按ID获取智能列表**&#x200B;端点现在返回智能列表规则描述（触发器和过滤器）。
   1. [获取智能列表](https://developer.adobe.com/marketo-apis/api/asset/#operation/getSmartListsUsingGET)终结点现在允许您在传递&#x200B;**earliestUpdatedAt**&#x200B;和&#x200B;**latestUpdatedAt**&#x200B;日期时间参数时按日期范围筛选结果。 此外，此端点现在会返回作为营销活动和电子邮件项目成员的智能列表。
1. 添加了用于提取智能列表定义的端点。
   1. 按Smart Campaign Id[获取](https://developer.adobe.com/marketo-apis/api/asset/#operation/getSmartListBySmartCampaignIdUsingGET)智能列表端点返回给定智能营销活动ID的智能列表记录。
   1. 按程序ID [获取](https://developer.adobe.com/marketo-apis/api/asset/#operation/getSmartListByProgramIdUsingGET)智能列表端点返回给定程序ID的智能列表记录。
1. 增强了[更新电子邮件内容](https://developer.adobe.com/marketo-apis/api/asset/#operation/updateEmailContentUsingPOST)端点，以允许更新从其模板（主题、名称、电子邮件、回复）断开的电子邮件的电子邮件标题字段。 [此处](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/email-marketing/general/functions-in-the-editor/edit-an-emails-html)描述了从模板断开的情况。

### 缺陷分辨率

1. 修复了在批准的登陆页面上调用[删除登陆页面](https://developer.adobe.com/marketo-apis/api/asset/#operation/deleteLandingPageByIdUsingPOST)将删除登陆页面的问题。 现在，它正确返回了“709，无法删除已批准的登陆页面”错误。 [LM-127271]
1. 修复了[发送示例电子邮件](https://developer.adobe.com/marketo-apis/api/asset/#operation/sendSampleEmailUsingPOST)终结点返回611的问题。 电子邮件从其模板中损坏时出现“系统错误”。 [LM-127288]
1. 修复了[删除程序](https://developer.adobe.com/marketo-apis/api/asset/#operation/cloneProgramUsingPOST)终结点的问题，在某些情况下，它会删除正在使用的程序，而不是发出“709，无法删除程序”。 资产在其他地方使用或不可删除”错误。 [LM-125431]

### 弃用

1. API对电子邮件编辑器1.0的支持计划于2020年1月被弃用。 请记住先将您的资源转换为2.0，然后再转换。 在1月之后尝试写入或克隆Email 1.0资源将导致错误而非警告。 在[此处](https://nation.marketo.com:443/t5/knowledgebase/email-2-0-and-email-api-faq-s/ta-p/251423)了解有关电子邮件API的更多信息。
1. 为了符合Adobe的世界级安全标准，从2019年12月13日起，我们将停止支持传输层安全性(TLS) 1.0和1.1。 与Marketo集成的系统如果不符合1.2协议，则可能会失去对Marketo Engage服务的访问权限。 要维护您的Marketo Engage访问权限，请确保在2019年12月13日之前所有客户端系统都符合TLS 1.2标准。 更多详细信息可在[此处](https://nation.marketo.com:443/t5/knowledgebase/tls-1-0-1-1-deprecation-faq/ta-p/249085)找到。

1. 现在，所有与Smart Campaign相关的内容都位于[Smart Campaigns](/help/rest-api/smart-campaigns.md)菜单项中(位于REST API > Assets下)。

由&#x200B;_David_&#x200B;发布于&#x200B;_2019-08-16_

## 2020年1月更新

2020年1月，我们将发布新的REST API，增强现有API并解决缺陷。 请参阅下面的完整更新列表。

* 添加了以编程方式创建自定义对象架构定义的功能。 这允许您定义自定义对象一次，并根据需要将其预配到任意数量的实例。 这允许您使用户能够有效地利用沙盒和卓越中心模型。 还允许ISV简化客户入门流程。 您需要适当的订阅类型才能访问自定义对象元数据API。
* 添加了批量导入和导出项目群成员的功能。 这组新端点遵循现有的Marketo REST API模式，用于创建异步批量处理作业。 项目群成员记录可以包含项目群成员自定义字段和/或潜在客户字段。
* 添加了获取可用的表单程序成员字段端点，以支持将程序成员自定义字段用作表单字段。 这会返回可在Marketo表单中使用的所有项目群成员自定义字段的列表。
* 添加了[获取由](/help/rest-api/email-templates.md)端点使用的电子邮件模板，该模板可返回依赖于给定电子邮件模板的电子邮件资产列表。 这使您能够快速了解潜在电子邮件模板更改的影响，并更轻松地解决这些依赖关系。

由&#x200B;_David_&#x200B;发布于&#x200B;_2020-01-17_

## 如何检索每个自定义对象

我们经常被问到如何使用Marketo的API获取所有[自定义对象](https://experienceleague.adobe.com/zh-hans/docs/marketo/using/home) (CO)的列表。 查询CO需要超过其名称：还需要关于每个CO的某些&#x200B;_先验知识_。 获取该知识的方法可能不明显，因为API没有提供直接查询它的方法。 与Marketo Engage中的许多目标一样，智能列表为与人员（潜在客户）关联的CO提供了答案。 智能列表与公司的运作方式不同，最终会产生一个列表，其中包含其公司链接到筛选器的对象类型的所有人员，因此您可能会发现必须根据您的目标删除重复公司。 无论何时批准新的自定义对象，都会创建关联的过滤器。 该名称将采用“**具有CO名称**”的格式。 在下面的示例中，自定义对象名称为“**会议曲目订阅”**，其筛选器名为“**具有会议曲目订阅**”。 创建智能列表后，您可以使用[自定义对象端点](/help/rest-api/custom-objects.md)检索查询关联CO所需的信息。 导出列表，确保包含链接的字段（ID或电子邮件地址）。 您可以通过[smartListName](/help/rest-api/bulk-lead-extract.md)、**smartListId**&#x200B;筛选器或&#x200B;**从UI**&#x200B;导出，使用[批量潜在客户提取API](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/core-marketo-concepts/smart-lists-and-static-lists/managing-people-in-smart-lists/export-people-to-excel-from-a-list-or-smart-list)进行导出。 在下一步中，您将使用每个链接字段值单独查询关联的自定义对象。 在此示例中，自定义对象的名称为&#x200B;**“会议跟踪订阅”**，其API名称为&#x200B;**conferenceTrackSubscription_c**。 您在UI中找到API名称“**API名称**”，并通过API找到API名称“**名称**”。  管理员 | Marketo自定义对象[/caption]，以下是[列表自定义对象API](https://developer.adobe.com/marketo-apis/api/mapi/#operation/listCustomObjectsUsingGET)端点返回的片段：

```json
{
    "name": "conferenceTrackSubscription_c",
    "displayName": "Conference Track Subscription",
    "description": "Track subscription for conference attendees",
    "createdAt": "2020-01-13T19:50:06Z",
    "updatedAt": "2020-01-13T19:50:06Z",
    "idField": "marketoGUID",
    "dedupeFields": [
        "subscriptionID"
    ],
    "searchableFields": [
        [
            "subscriptionID"
        ],
        [
            "marketoGUID"
        ],
        [
            "leadID"
        ]
    ],
    "relationships": [
        {
            "field": "leadID",
            "type": "child",
            "relatedTo": {
                "name": "Lead",
                "field": "Id"
            }
        }
    ]
}
```

要检索与智能列表中的人员相关联的一对一(1:1)或一对多(1:N)的自定义对象，请发出如下请求：

`GET /rest/v1/customobjects/conferenceTrackSubscription_c.json?filterType=leadID&filterValues=1000302,1000303,1000304,1000306,1000307`

在此示例中，此自定义对象通过&#x200B;**leadID**&#x200B;字段链接到人员，因此过滤器类型为“**leadID**”。 过滤器值参数是从智能列表导出中获取的ID的逗号分隔列表。 请求可以包含您可在单个请求URI中容纳的任意数量的过滤器值：最多8K个字符。 超过此长度的请求会返回414 HTTP级别的错误代码。 响应可以在多个块中返回。 如果是，则&#x200B;**moreResult**&#x200B;将为&#x200B;**true**，并将包含一个&#x200B;**nextPageToken**。 然后，您需要[逐页浏览](/help/rest-api/paging-tokens.md)结果，直到&#x200B;**moreResult**&#x200B;为&#x200B;**false**&#x200B;为止。 以下是上述API请求的结果的一部分：

```json
"result": [
    {
        "seq": 0,
        "marketoGUID": "d6b3ed3d-4eb8-4066-a9cd-184c8d385cfe",
        "leadID": "1000302",
        "subscriptionID": "4ad59184-6bf1-4eeb-a583-d82aeee68210",
        "createdAt": "2020-01-13T21:24:04Z",
        "updatedAt": "2020-01-13T21:24:04Z"
    },
    {
        "seq": 1,
        "marketoGUID": "e5e0aba4-f27f-494d-93ed-9cb580989bf3",
        "leadID": "1000303",
        "subscriptionID": "fc5596d5-6fa2-4848-b4a2-89d96e245c59",
        "createdAt": "2020-01-13T21:24:04Z",
        "updatedAt": "2020-01-13T21:24:04Z"
    },
    {
        "seq": 2,
        "marketoGUID": "e65007cd-86b1-4c17-8d55-057c96e1788a",
        "leadID": "1000304",
        "subscriptionID": "7e54b8a0-2170-4d81-a809-4eac349508d0",
        "createdAt": "2020-01-13T21:24:04Z",
        "updatedAt": "2020-01-13T21:24:04Z"
    },
    {
        "seq": 3,
        "marketoGUID": "39d956b2-85e2-4c24-94e7-e9fa5a09d3d0",
        "leadID": "1000306",
        "subscriptionID": "644c8e5b-fc0c-4d4a-80f8-358a65ce0a68",
        "createdAt": "2020-01-13T21:24:04Z",
        "updatedAt": "2020-01-13T21:24:04Z"
    },
    {
        "seq": 4,
        "marketoGUID": "a2151350-50c8-437f-bc71-7a054bb601f0",
        "leadID": "1000307",
        "subscriptionID": "bf14218c-ae6a-42b3-a14e-f7182903cbcd",
        "createdAt": "2020-01-13T21:24:04Z",
        "updatedAt": "2020-01-13T21:24:04Z"
    }
```

现在，每个自定义对象的值都与智能列表中的人员直接关联，除了检索这些值之外，您还可以使用&#x200B;**marketoGUID**&#x200B;来[更新](/help/rest-api/custom-objects.md)或[删除](/help/rest-api/custom-objects.md)这些对象。 对于在多对多关系(N:N)中与人员相关联的自定义对象，上述技术返回第一级对象，该对象是将每个人员连接到多个第二级CO的中间对象。

要检索这些第二级CO，请通过对链接字段和从第一级中间对象提取的值进行筛选，为第二级CO类型启动一组新的查询。 例如，上述“**会议跟踪订阅”**&#x200B;对象可能具有表示名为&#x200B;**“会话”**&#x200B;的会话的另一个级别对象，这些会话可能由&#x200B;**subscriptionID**&#x200B;链接。 随后，检索与上述会议跟踪订阅关联的会话的请求将如下所示：

`GET /rest/v1/customobjects/session_c.json?filterType=subscriptionID&filterValues=4ad59184-6bf1-4eeb-a583-d82aeee68210,e5e0aba4-f27f-494d-93ed-9cb580989bf3,e65007cd-86b1-4c17-8d55-057c96e1788a,39d956b2-85e2-4c24-94e7-e9fa5a09d3d0,bf14218c-ae6a-42b3-a14e-f7182903cbcd`

_脚注_ _1)**smartListName**和&#x200B;**smartListId**筛选器类型不适用于某些订阅。 如果订阅不可用，则在调用“创建导出潜在客户作业”终结点（**&quot;1035，目标订阅&quot;**&quot;的过滤器类型不受支持）时收到错误。 客户可以联系Marketo支持部门以在其订阅中启用此功能。_

由&#x200B;_Tony_&#x200B;发布于&#x200B;_2020-01-14_

## 如何检索每个人（潜在客户）

我们会收到许多有关从Marketo Engage实例检索每个人（潜在客户）所需的流程的查询。 我们提供了许多有用的答案，但没有一个如此完整。 我已经确定了使用Marketo的Bulk Extract API提取每个商机所需的几个关键概念。 所有其他详细信息都可以从我创建的演示代码中学习。 在阅读本文并浏览演示代码后，您将了解从Marketo Engage实例检索每个潜在客户所需了解的所有信息。

### 概述

核心技术使用[批量潜在客户提取API](/help/rest-api/bulk-lead-extract.md)。 您可能希望能够创建没有过滤器的批量潜在客户导出作业，但您不能：**API需要过滤器**。 可用的筛选器为&#x200B;**createdAt**、**staticListName**、**staticListId、** **updatedAt**、**smartListName**&#x200B;和&#x200B;**smartListId**。 按没有过滤器的智能列表进行过滤似乎也很有吸引力。 尝试该选项后，您会发现系统足够智能，无需相同筛选器即可处理智能列表：API也需要此处的筛选器。 由于我们需要筛选器，因此要使用的可信且规范的筛选器是&#x200B;**createdAt**。 此筛选器类型允许日期时间范围最长为31天，因此我们需要运行多个作业并合并结果。 我们首先是在目标实例中查找潜在客户可能的最早创建日期。 从可能的最早日期开始，我们将创建时间跨度为31天减一秒（以后将更多）的作业。 创建每个作业后，我们会将其排入队列并等待其完成。 然后，我们将下载生成的文件，并使用校验和检查其完整性。 最后，按ID删除重复的商机，然后写入唯一的商机，以生成一个输出CSV文件。

### 查找您最旧的商机

我正在用一个“小技巧”来获取Target实例中潜在客户的最早可能日期。 没有专用于该任务的API端点，因此我们需要一点创造性。 我所做的是查询具有&#x200B;**maxDepth = 1**&#x200B;的所有文件夹，这将为我们提供实例中所有顶级文件夹的列表。 然后，我收集&#x200B;**createdAt**&#x200B;日期，解析这些日期并查找最早的日期。 此方法可正常使用，因为有些默认的顶级文件夹是使用实例创建的，在此之前无法创建潜在客户。

### 选择必填字段

您需要决定需要提取哪些字段。 使用[Describe Lead 2终结点](https://developer.adobe.com/marketo-apis/api/mapi/#operation/describeUsingGET_2)查找目标实例的可用字段。 对该请求的响应将包含一个名为“fields”的列表。 以下是示例响应中的摘录：

```json
  "fields": [
      {
          "name": "AccountSource",
          "displayName": "Account Source",
          "dataType": "string",
          "length": 40,
          "updateable": true,
          "crmManaged": false
      },
      {
          "name": "acquisitionProgramId",
          "displayName": "Acquisition Program",
          "dataType": "reference",
          "updateable": true,
          "crmManaged": false
      },
      {
          "name": "Active_c",
          "displayName": "Active",
          "dataType": "string",
          "length": 255,
          "updateable": true,
          "crmManaged": false
      },
      {
          "name": "address",
          "displayName": "Address",
          "dataType": "text",
          "updateable": true,
          "crmManaged": false
      },
      {
          "name": "Address_lead",
          "displayName": "Address (L)",
          "dataType": "string",
          "length": 255,
          "updateable": true,
          "crmManaged": false
      },
      {
          "name": "annualRevenue",
          "displayName": "Annual Revenue",
          "dataType": "currency",
          "updateable": true,
          "crmManaged": false
      },
      {
          "name": "anonymousIP",
          "displayName": "Anonymous IP",
          "dataType": "string",
          "length": 255,
          "updateable": true,
          "crmManaged": false
      }, ...
```

此端点返回包含标准字段和自定义字段的详尽列表。 您请求的字段越多，导出作业完成所需的时间越长，结果文件越大。 您通常只应选择所需的字段。 没有任何内容会阻止您请求每个可用的字段，因此我正在演示的就是这些内容。 创建导出作业时所需的字段标识符是&#x200B;**名称**&#x200B;值。 我将抽取名称值到所有字段名称的列表中。 然后，在创建每个导出作业时，我会使用它来请求每个可用字段。

### 导出作业日期范围：每个日期范围31天

每个导出作业最长可持续31天。 我正在使用的演示实例是在2016年8月创建的，所以今天我需要创造超过40个工作岗位。 这是自第一个创建日期以来的天数除以31所得的结果。 该API允许同时处理两个导出作业，以便您可以使用并行运行的两个作业进行提取。 批量提取作业是与所有其他集成共享的资源，因此我将做得很好。 我将保留其他作业可用于其他集成，并将演示如何逐个运行单个作业。 用于&#x200B;**createdAt**&#x200B;过滤器的日期使用[ISO 8601规范](https://www.w3.org/TR/NOTE-datetime)进行格式设置。 它们始终采用GMT (Z+0000)，因此时区将仅以“Z”或“+00:00”表示。 2016年8月1日为&#x200B;**2016-08-01T00:00:00+00:00**，31天后为2016年9月1日，即&#x200B;**2016-09-01T00:00:00+00:00。**&#x200B;开始时间和结束时间均包含，因此我要从该结束时间减去1秒：**2016-09-01T00:00:00+00:00**&#x200B;变为&#x200B;**2016-08-31T23:59:59+00:00**。 减去秒数可避免时间重叠。 由于GMT是默认值，因此您也可以禁用&#x200B;**Z**&#x200B;或&#x200B;**+00:00**。

### 重复数据删除

尽管我费了不少心思去避免时间重叠，但我还是实施了重复数据消除。 我这样做是因为在某些极端情况下，时间更改（[夏令时](https://en.wikipedia.org/wiki/Daylight_saving_time)）会导致值不明确，因此Marketo的批量提取API可能会返回意外的重复商机。 很少发生这种情况，但需要在使用日期时间过滤器范围的任何集成中进行说明。 我抽出一秒钟，明确表示时间是包含在内的。 我不希望您认为创建作业时分别使用&#x200B;**2016-08-01T00** 00Z **和** 2016-09-01T00 **00Z:00:的** createdAt **和:00:endAt**&#x200B;次，而不包括在&#x200B;**2016-09-01T00:00:00Z**&#x200B;上创建的潜在客户；威尔。

### 创建作业

第一步是使用[创建导出潜在客户作业终结点](https://developer.adobe.com/marketo-apis/api/mapi/#operation/createExportLeadsUsingPOST)创建作业。 在本演示中，创建第一个导出作业的请求如下所示：

`POST /bulk/v1/leads/export/create.json`

```json
{ "filter": {"createdAt": {"startAt": "2016-08-01T00:00:00",
                           "endAt": "2016-09-01T00:00:00"}},
"fields":["AccountSource","acquisitionProgramId","Active_c","address","Address_lead","annualRevenue","anonymousIP","BillingAddress","billingCity","billingCountry","BillingGeocodeAccuracy","BillingLatitude","BillingLongitude","billingPostalCode","billingState","billingStreet","blackListed","blackListedCause","city","CleanStatus","CleanStatus_account","company","CompanyDunsNumber","contactCompany","cookies","country","createdAt","customfield","DandbCompanyId","DandbCompanyId_account","dateOfBirth","dddS","department","doNotCall","doNotCallReason","dS","dueDate","DunsNumber","email","EmailBouncedDate","EmailBouncedReason","emailInvalid","emailInvalidCause","emailSuspended","emailSuspendedAt","emailSuspendedCause","externalCompanyId","externalSalesPersonId","facebookDisplayName","facebookId","facebookPhotoURL","facebookProfileURL","facebookReach","facebookReferredEnrollments","facebookReferredVisits","fax","firstName","gender","GeocodeAccuracy","holll","id","industry","inferredCity","inferredCompany","inferredCountry","inferredMetropolitanArea","inferredPhoneAreaCode","inferredPostalCode","inferredStateRegion","interested","interestedIn","isAnonymous","IsEmailBounced","isLead","iSTRUE","Jigsaw","JigsawCompanyId_account","JigsawContactId_lead","Jigsaw_account","Languages_c","lastName","LastReferencedDate","LastReferencedDate_account","lastReferredEnrollment","lastReferredVisit","LastViewedDate","LastViewedDate_account","Latitude","leadPartitionId","leadPerson","leadRevenueCycleModelId","leadRevenueStageId","leadRole","leadScore","leadSource","leadStatus","linkedInDisplayName","linkedInId","linkedInPhotoURL","linkedInProfileURL","linkedInReach","linkedInReferredEnrollments","linkedInReferredVisits","links","Longitude","MailingAddress","MailingGeocodeAccuracy","MailingLatitude","MailingLongitude","mainPhone","marketingSuspended","marketingSuspendedCause","middleName","mktoAcquisitionDate","mktocomment1","mktocomments2","mktoCompanyNotes","mktoDoNotCallCause","mktoIsCustomer","mktoIsPartner","mktoName","mktoPersonNotes","mktosync","mktotest1","mobile","mobilePhone","NaicsCode","NaicsDesc","newcustom","numberOfEmployees","originalReferrer","originalSearchEngine","originalSearchPhrase","originalSourceInfo","originalSourceType","OtherAddress","OtherGeocodeAccuracy","OtherLatitude","OtherLongitude","personPrimaryLeadInterest","personTimeZone","personType","phone","PhotoUrl","PhotoUrl_account","postalCode","priority","ProductInterest_c","rating","referal","registrationSourceInfo","registrationSourceType","relativeScore","relativeUrgency","requiredNumberofCylinder","salutation","sfdcAccountId","sfdcContactId","sfdcId","sfdcLeadId","sfdcLeadOwnerId","sfdcType","ShippingAddress","ShippingGeocodeAccuracy","ShippingLatitude","ShippingLongitude","sicCode","SicDesc","site","state","surveyAnswers","syndicationId","testBooleanField","testscore","title","totalReferredEnrollments","totalReferredVisits","Tradestyle","twitterDisplayName","twitterId","twitterPhotoURL","twitterProfileURL","twitterReach","twitterReferredEnrollments","twitterReferredVisits","uNSUBSCIBE","unsubscribed","unsubscribedReason","updatedAt","urgency","url","website","YearStarted"]}
```

响应如下所示：

```json
{
  "requestId": "6902#16fb52118bf",
  "result": [
      {
          "exportId": "4f2b9115-c3f2-4e40-a87c-bf803bbfed99",
          "format": "CSV",
          "status": "Created",
          "createdAt": "2020-01-17T20:10:43Z"
      }
  ],
  "success": true
}
```

### 将作业排入队列

现在工作已经创造了，只是坐在那里，什么也不做。 要运行作业，我们需要使用[exportId](https://developer.adobe.com/marketo-apis/api/mapi/#operation/enqueueExportLeadsUsingPOST)值调用&#x200B;**入队端点**&#x200B;以生成请求的URI。 它看起来像这样：

`POST /bulk/v1/leads/export/4f2b9115-c3f2-4e40-a87c-bf803bbfed99/enqueue.json`

此POST没有正文，我们只是使用POST HTTP动词。 该请求将生成如下响应：

```json
{
  "requestId": "1836a#16fb5238a48",
  "result": [
      {
          "exportId": "4f2b9115-c3f2-4e40-a87c-bf803bbfed99",
          "format": "CSV",
          "status": "Queued",
          "createdAt": "2020-01-17T20:10:43Z",
          "queuedAt": "2020-01-17T20:13:23Z"
      }
  ],
  "success": true
}
```

正如我前面提到的，每次可以运行的作业数量有限。 同时对同时排队的作业数也有限制：10。 我们需要超过40个，这样一来限制我们就无法一次创造所有工作岗位。 其他集成也可以运行作业，因此我们需要考虑所有插槽均已满的可能性。 当已有10个已排队的作业时，尝试将新作业排入队列将生成[1029](/help/rest-api/error-codes.md)错误。 当您收到&#x200B;**1029**&#x200B;时，请使用指数回退，直到作业可以入队。 我等待1分钟，并在每次收到请求之间最多4分钟的&#x200B;**1029**&#x200B;错误代码时加倍该值，但最长时间不会超过该值。 此技术称为[截断二进制指数回退](https://devopedia.org/binary-exponential-backoff)，是可恢复的错误和状态检查的最佳实践。

### 等待作业完成

每个作业都需要一些时间才能运行，因此我们将调用[状态终结点](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getExportLeadsStatusUsingGET)来监视其进度。 再次重申，我们将在请求URI中包括&#x200B;**exportId**，如下所示：

`GET /bulk/v1/leads/export/4f2b9115-c3f2-4e40-a87c-bf803bbfed99/status.json`

在作业完成之前，您会获得如下响应：

```json
{
    "requestId": "153cb#16fb525435d",
    "result": [
        {
            "exportId": "4f2b9115-c3f2-4e40-a87c-bf803bbfed99",
            "format": "CSV",
            "status": "Processing",
            "createdAt": "2020-01-17T20:10:43Z",
            "queuedAt": "2020-01-17T20:13:23Z",
            "startedAt": "2020-01-17T20:13:49Z"
        }
    ],
    "success": true
}
```

当作业状态为“已排队”时，我执行相同的指数回退（1分钟到4分钟）。 状态不是实时的；每分钟更新一次，因此加快轮询几乎没有好处。 当作业状态变为“正在处理”时，我重置回退。 我们正在等待“已完成”状态，如下所示：

```json
{
  "requestId": "10ad9#16fb5268f9b",
  "result": [
      {
          "exportId": "4f2b9115-c3f2-4e40-a87c-bf803bbfed99",
          "format": "CSV",
          "status": "Completed",
          "createdAt": "2020-01-17T20:10:43Z",
          "queuedAt": "2020-01-17T20:13:23Z",
          "startedAt": "2020-01-17T20:13:49Z",
          "finishedAt": "2020-01-17T20:15:28Z",
          "numberOfRecords": 59,
          "fileSize": 74436,
          "fileChecksum": "sha256:de553362e7ffad6556ae9ea749655c35010c7f0e944fc5a85782183130dca18d"
      }
  ],
  "success": true
}
```

请求未返回潜在客户时，**numberOfRecords**&#x200B;值为零。 我检查此值，并在有意义时跳过后续步骤。 当返回潜在客户时，我提取&#x200B;**fileChecksum**&#x200B;值。 我们使用它来检查下载时文件的完整性。

### 获取您的潜在客户

如果&#x200B;**numberOfRecords**&#x200B;大于零，请使用[获取导出潜在客户文件](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getExportLeadsFileUsingGET)下载导出的文件，请求如下：

`GET /bulk/v1/leads/export/4f2b9115-c3f2-4e40-a87c-bf803bbfed99/file.json`

检查是否传输了文件且未出现错误：计算文件的校验和，并与之前保存的&#x200B;**fileChecksum**&#x200B;进行比较。 使用[SHA-2](https://en.wikipedia.org/wiki/SHA-2)，特别是SHA256哈希函数计算校验和。 如果计算的校验和不匹配，则说明文件传输出错，您可以重试传输或中止并手动恢复。

### 聚合数据

从第一个线索到今天，每隔31天便完成一次循环，您将拥有一套完整的流程。 每个范围还将有一个文件。 使用每个潜在客户构建单个聚合文件的最简单方法是，在删除除第一个文件外的所有文件的标题行后连接这些文件。 如果这样做，请不要忘记期望以后在您的数据处理管道中发生潜在重复并做出计划。 在演示中，我会在下载文件时对其进行处理。 在将每一行数据添加到输出文件之前，我通过检查该行潜在客户ID是否已写入来删除重复数据。

我已开发了一些托管在[此处](https://github.com/Marketo/REST-Sample-Code/tree/master/python/LeadDatabase/Leads)的演示代码，希望这些代码填写此过程的详细信息，并可用作您自己的开发模板。 演示代码旨在用作学习工具，因此生产系统需要提高稳健性。 **代码按MIT许可证提供的“原样”**，但它可能适合在人工监督下一次性使用。 现在没有什么能阻止你！ 在遵循此流程后，您将使用Marketo的批量提取API提取每个商机，并可能提取目标Marketo Engage实例的每个字段。 要进一步扩展数据，请使用技术了解每个潜在客户的活动。

由&#x200B;_Tony_&#x200B;发布于&#x200B;_2020-03-05_

## 2020年2月更新

2020年2月，我们将发布新的REST API。 请参阅下面的完整更新列表。

### 公告

* 2020年9月之后，[Asset API](/help/rest-api/assets.md)端点将不再接受&#x200B;**_method**&#x200B;查询参数。 此标头用于在POST正文中传递查询参数以绕过URI长度限制。 为了适应需要此参数的请求，Asset API的URI限制将从6KiB增加到65KiB。
* 有关我们在ITP上的立场，请参阅此Marketo社区帖子：[浏览器Cookie更新：Marketo/Munchkin受到什么影响](https://nation.marketo.com:443/t5/knowledgebase/browser-cookie-updates-how-marketo-munchkin-is-affected/ta-p/251524)
* 对“进程中的更改状态”活动进行了更改。 为了支持即将推出的“计划成员自定义字段”功能，添加了“计划成员ID”属性。

由&#x200B;_David_&#x200B;发布于&#x200B;_2020-02-26_

## 弃用Munchkin关联潜在客户方法

在Munchkin JavaScript Client的下一个版本版本159中，我们将开始弃用Munchkin [关联潜在客户方法](/help/javascript-api/api-reference.md)。 从此版本开始，在调用方法时，浏览器控制台中将发出警告，指示在将来的版本中删除方法。 删除方法后，尝试使用方法将导致失败。 我们最近确定使用这种方法的Marketo客户将单独收到有关其使用的通知。 有关Munchkin 159版本的详细信息，[请参阅此Marketing Nation帖子](https://nation.marketo.com:443/t5/product-documents/munchkin-javascript-version-159-amp-associate-lead-deprecation/ta-p/299687)。

### 常见问题解答

我如何知道自己是否受到影响？

Adobe将通知我们观察到其订阅中使用此方法的客户，并将在弃用期间多次进行通知。

何时删除方法？

此方法将在Munchkin JS v161中删除，计划与2021年10月的Marketo版本一起正式发布。

为什么要删除此方法？

自引入此方法以来，已经实施和发布了一些更高效的方法来实现相同的用例。 为了提高服务的性能和运行状况，有时必须删除无法达到可接受标准的功能。

我应该使用什么来代替此方法？

Munchkin关联潜在客户有两个主要用例：提交人员数据，以及将浏览器Web跟踪Cookie关联到Marketo中的相应人员记录。 自从引入Munchkin Associate Lead以来，已经实施了更可靠的数据提交和Cookie关联方法。

#### 服务器端提交

如果您不需要浏览器端提交，则REST API会为[人员数据提交](/help/rest-api/leads.md)提供多种方法，并提供用于将Cookie与人员记录关联的[专门构建的方法](/help/rest-api/leads.md)。

何时推出版本159的Munchkin？

版本159自2020年10月Marketo发布以来已正式发布。

由&#x200B;_Kenny_&#x200B;发布于&#x200B;_2020-05-06_

## 在后台提交Marketo表单

当您的组织拥有许多不同的平台来托管Web内容和客户数据时，通常需要从表单中并行提交数据，以便在不同的平台上收集结果数据。 有多种策略可以做到这一点，但最好的策略通常最简单：使用Forms 2 API提交隐藏的Marketo表单。 此操作适用于任何新的Marketo表单，但理想情况下，您应该为此创建一个没有字段的空表单。 这将确保表单不会加载任何超出所需数量的数据，因为我们不需要渲染任何内容。 现在，只需从表单中获取[嵌入代码](https://experienceleague.adobe.com/zh-hans/docs/marketo/using/home)并将其添加到所需页面的正文中，即可进行较小的修改。 您的嵌入代码包含如下表单元素：

`<form id="mktoForm_1068"></form>`

您需要将&#39;style=&quot;display:none&quot;&#39;添加到元素，使其不可见，如下所示：

`<form id="mktoForm_1068" style="display:none"></form>`

一旦嵌入并隐藏了表单，提交表单的代码就会非常简单：

```javascript
var myForm = MktoForms2.allForms()[0];
myForm.addHiddenFields({
 //These are the values which will be submitted to Marketo
 "Email":"<test@example.com>",
 "FirstName":"John",
 "LastName":"Doe"
 });
myForm.submit();
```

Forms以这种方式提交的行为将与潜在客户填写并提交可见表单时的行为完全相同。 由于每个实施采用不同的操作进行提示，因此触发提交因实施而异，但您基本上可以执行任何操作。 重要的部分是正确设置字段和值。 请确保使用您可在导出字段名称中找到的SOAP API字段名称，以确保正确提交值。

### 从Munchkin关联潜在客户迁移

后台表单提交是Munchkin关联商机的推荐替换方法之一。 下面的示例HTML页面通过重用提交给“关联潜在客户”的相同值来演示高级别的迁移。

```html
<html>
<head>
    <!--
      Munchkin Code
      Replace with your own instance code
    -->
    <script type="text/javascript">
        (function() {
          var didInit = false;
          function initMunchkin() {
            if(didInit === false) {
              didInit = true;
              Munchkin.init('CHANGE ME');
            }
          }
          var s = document.createElement('script');
          s.type = 'text/javascript';
          s.async = true;
          s.src = '//munchkin.marketo.net/munchkin-beta.js';
          s.onreadystatechange = function() {
            if (this.readyState == 'complete' || this.readyState == 'loaded') {
              initMunchkin();
            }
          };
          s.onload = initMunchkin;
          document.getElementsByTagName['head'](0).appendChild(s);
        })();
        </script>
</head>

<body>
  <!--
    Start Embed code.
    Pasted from Form Actions -> Embed Code except for addition of 'style="display:none"' to the form tag in order to hide it, and instance-specific codes redacted
    Replace with your own code for testing
  -->
  <script src="CHANGE ME"></script>
  <form id="CHANGE ME" style="display:none"></form>
  <script>MktoForms2.loadForm("CHANGE ME", "CHANGE ME", "CHANGE ME TO AN INTEGER ID");</script>
  <!--End Embed Code-->

  <!--Demo code-->
    <script>
        //The same map which is assembled for the Munchkin submission can be reused for the form submission
        let values = {
            "Email": "email@example.com",
            "FirstName": "Test",
            "LastName": "Record"
        }
        //whenReady lets us apply a callback to all mkto forms on the page
        //the callback function fires whenever a form is completely loaded
        //for most use cases this will just be used to capture a reference to the form for later usage
        //submission is done in line here for demonstration only
        MktoForms2.whenReady(function(form){

            //the addHiddenFields methods lets us add arbitrary fields to the form as well as their values
            form.addHiddenFields(values);

            //pass the same set of values to associateLead
            //hashString: secret + email
            Munchkin.munchkinFunction('associateLead', values, "CHANGE ME");

            //submit the form
            form.submit();


        })
    </script>
</body>

</html>
```

由&#x200B;_Kenny_&#x200B;发布于&#x200B;_2020-05-26_

## 2020年7月更新

2020年7月，我们将发布新的REST API，增强现有API并解决缺陷。 请参阅下面的完整更新列表。

* 添加了两个端点，可让您查询和删除尚未接受其邀请的用户（即“待处理”用户）。 [按ID获取受邀用户](/help/rest-api/user-management.md)端点允许您查询待处理用户。 [删除受邀用户](/help/rest-api/user-management.md)终结点允许您删除待处理用户。
* [邀请用户](/help/rest-api/user-management.md)终结点已更新，以接受&#x200B;**expiresAt**&#x200B;参数的ISO 8601兼容日期时间字符串。
* 已更新[按ID获取用户](/help/rest-api/user-management.md)和[更新用户属性](/help/rest-api/user-management.md)端点，以返回&#x200B;**lastLoginAt**&#x200B;属性中的上次用户登录时间。
* 修复了在尝试创建已存在的静态列表时，[创建静态列表](https://developer.adobe.com/marketo-apis/api/asset/#operation/createStaticListUsingPOST)端点返回错误“611，系统错误”的问题。 已更改为返回错误“709，已存在同名的静态列表”。 [LM-135934]
* 修复了在尝试创建已存在的电子邮件时，[创建电子邮件](https://developer.adobe.com/marketo-apis/api/asset/#operation/createEmailUsingPOST)端点返回错误“611，系统错误”的问题。 已更改为返回错误“709，同名电子邮件已存在”。 [LM-138648]
* 修复了登陆页面查询端点返回错误的&#x200B;**createdAt**&#x200B;值的问题。 端点返回了上次批准登陆页面的时间。 现在，它们会返回到创建登陆页面的时间。 [LM-138648]
* 修复了[合并潜在客户](https://developer.adobe.com/marketo-apis/api/mapi/#operation/mergeLeadsUsingPOST)终结点因无效的合并操作而返回错误“611，系统错误”的问题。 在合并导致重复商机且&#x200B;**mergeinCRM**&#x200B;设置为true时发生。 已更改为返回错误“712，您正在创建重复记录。 我们建议您改用现有记录”。 [LM-137463]

由&#x200B;_David_&#x200B;发布于&#x200B;_2020-08-01_

## 来宾帖子 — 深入分析：自定义对象、自定义活动与自定义字段

**这是来自Amit Jain、Marketo Champion 2020、MarTech IT Specialist的客座帖子**&#x200B;作为企业级营销自动化平台，Marketo管理从多个来源获得的用户/客户/潜在客户信息，并在Marketo中维护这些信息，以实现更好的个性化、细分和报告。 这些源可以从您的网站表单到列表构建，从您的CRM数据到您的电子商务数据。 Marketo提供标准对象，如Lead、Company、Opportunity和Activity等。 这些标准对象有时不足以满足在Marketo中可用的扩展数据集的新兴营销需求。

例如，添加到购物车的项目、申请不同课程的学生、特定人员拥有的产品以及同意不同订阅等。 此信息对于营销人员通过跨平台提供更加个性化的内容和统一的用户体验来保持客户/潜在客户参与度至关重要。 为了满足这些业务需求，Marketo提供了自定义方式，以便将此类型的数据存储在自定义字段、自定义活动和自定义对象中。 我观察到人们在理解何时使用哪种选项时遇到问题。 在这篇博客文章中，**我们将详细了解这三件事的实际含义以及何时使用哪一个和某些示例一起使用。**

**自定义字段**

Marketo中的“Lead”对象是主对象，所有其他对象直接或间接与此主对象相关联。 Marketo允许您在“Lead”对象、“Company”对象上创建自定义字段，最近他们宣布支持“Program member”自定义字段。 这些自定义字段满足您存储特定类型数据的需要。 例如，您可能需要存储工作功能或用户不同的同意。 Marketo中有两种类型的自定义字段：

1. **从CRM字段同步：**&#x200B;作为自动Marketo↔︎SFDC同步的一部分，Marketo将同步SFDC中对Marketo用户可见的所有自定义字段，包括潜在客户、联系人、帐户和机会对象。
1. **仅Marketo字段：**&#x200B;您可以在Marketo中直接创建字段。 这些字段的数据将不会与SFDC同步。

**快速提示**

* 您是否拥有仅限Marketo的字段，现在想要在SFDC中同步数据？ 查看[此博客](https://themarketingautomationblog.com/2019/12/06/field-management-merging-remapping-hiding-marketo-fields/)以了解如何执行此操作。
* 在[此处](https://themarketingautomationblog.com/2019/11/15/knowing-your-marketo-fields/)了解有关自定义字段的更多信息。
* Marketo API目前不支持更新/创建自定义字段。

**自定义对象**

除了标准对象之外，Marketo还允许您创建自己的自定义对象。 _您可以创建自定义对象并与公司或潜在客户对象或其他自定义对象相关。_&#x200B;自定义对象是补充标准潜在客户和公司记录的一组自定义记录。 自定义对象允许您以可扩展的方式存储其他数据，并将该数据链接到潜在客户或公司记录。 您可以使用标准（链接字段）和自定义字段的任意组合构建自定义对象，填充这些字段以创建自定义对象&#x200B;_记录_，然后将这些记录关联到潜在客户或公司记录。 强大而灵活的链接记录通过允许您使用未在潜在客户字段和公司字段中找到的信息来构建这些资产，从而丰富了您的区段、智能列表和营销活动。 自定义对象可以具有以下关系类型之一：

* **一对一关系**：其中每个自定义对象都有一个Lead/Company对象记录。
* **一对多关系**：其中每个自定义对象包含多个与潜在客户/公司相关的自定义对象记录。
* **多对多关系：**，其中多个自定义对象记录可以与多个潜在客户/公司对象链接。 例如，多个学生从课程目录注册了多个课程。

**快速提示**

* 在[此处](https://experienceleague.adobe.com/zh-hans/docs/marketo/using/home)了解有关设置自定义对象的更多信息。
* 您可以将Marketo自定义对象用作中间对象，其含义也包括自定义对象的自定义对象。

### 为何选择自定义对象？

自定义对象允许您编译和使用与公司或潜在客户相关的唯一数据，但这不一定是有关公司或潜在客户的静态信息。 尽管潜在客户字段与个人的潜在客户信息（_电子邮件地址_、_邮政编码_&#x200B;等）、业务信息（_工作标题_、_行业_&#x200B;等）或系统驱动的信息(_Marketo ID_、SFDC ID或创建日期等)相关，但自定义对象字段是完全可自定义的。 例如，使用自定义对象存储如下信息：

* 用户的购买历史记录。
* 购物车信息。
* 客户是否使用了您的限时促销折扣代码。
* 从调查结果、访谈和活动出席情况中获取的补充信息。
* 用户的试用信息，包括试用实例URL、开始日期、结束日期、用户数量等。

### Marketo自定义对象限制

1. 默认情况下，Marketo允许您创建10个自定义对象。 您可以提高限额，但需支付额外的订阅费用。
1. 每个对象的默认字段数为50，但您可以根据需要请求Marketo支持其他字段。 感谢您[Michael Florin](https://www.linkedin.com/in/michaelflorin/)在此输入其他信息。
1. 您可以在所有自定义对象中拥有的记录数存在限制。 这取决于您对Marketo的订购。 可以增加此限制，但需支付额外的订阅费用。
1. 如果自定义对象是使用API创建的，则Marketo不允许从Marketo UI中编辑CO架构。

**快速提示**

* Marketo API支持对自定义对象执行CRUD（创建、读取、更新和删除）操作。
* 您可以将此自定义对象数据用于使用Velocity脚本进行电子邮件个性化。
* 您可以在[此处](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getExportProgramMembersStatusUsingGET)找到所有与自定义对象相关的端点。

### 自定义对象术语

**自定义对象**：包含所有自定义对象记录分组的容器。 以前称为数据卡集或自定义表。 **自定义对象记录**：包含可绑定到潜在客户或公司的其他字段信息的数据记录。 记录可由标准潜在客户或公司字段以及自定义对象记录字段组成。 正式称为数据卡或数据表行。 **自定义对象记录字段**：可完全自定义的字段以收集唯一或临时信息。 这些字段在自定义对象本身中创建和存储。 正式名称为数据卡字段或数据库表字段/列。 **链接字段**：自定义对象记录字段的特殊类型，用于定义自定义对象记录与链接的潜在客户/公司对象记录之间的关系。 创建自定义对象时，必须提供链接字段以将自定义对象记录连接到正确的父记录。

* 对于一对多或一对一自定义结构，使用自定义对象中的链接字段将其连接到人员或公司。
* 对于多对多结构，使用两个链接字段，它们通过单独创建的中间对象（也是一种自定义对象）连接。 一个链接连接到数据库中的人员或公司，另一个链接连接到自定义对象。 在这种情况下，链接字段不在自定义对象本身中。

### 自定义活动

**用户可通过多种方式与我们的组织交互。 他们可能会访问您公司的网站、参加您的某次贸易展，或单击您发送的电子邮件中的链接。 这些操作是活动**，无论它们执行什么操作，Marketo都会捕获这些操作，以便您的营销和销售团队能够更好地了解用户的行为以实现个性化和统一的参与。 **_自定义活动_** _可以帮助您跟踪与Marketo表单、电子邮件或登陆页面无关的活动_。 例如，如果要跟踪用户何时在网站上查看了视频或进行了调查，请使用自定义活动。 自定义活动与自定义对象不同。 值可以更改时使用自定义对象（即“汽车颜色”从蓝色更改为红色）。 当跟踪已发生并且其详细信息无法更改的时刻（即“购买的汽车”）时，使用自定义活动。 默认情况下，可定义的自定义活动最大数量限制为10。 可以另外收取订阅费来增加此费用。 根据[Marketo数据保留策略](https://nation.marketo.com/t5/knowledgebase/tkb-p/support_solutions-documents)，自定义活动将在25个月后自动删除。

**自定义活动：**&#x200B;您要在Marketo中跟踪的非Marketo事件。 **自定义活动ID：** Marketo为自定义活动分配一个数字ID，尝试使用Marketo API推送/拉取活动数据时可使用该ID。 **自定义活动字段：**&#x200B;活动元数据可以存储在活动字段中。 例如，如果要跟踪视频查看，则字段可以是页面URL、视频标题等。 **自定义活动主字段：**&#x200B;可用作智能列表筛选条件的自定义活动字段。

**快速提示**

* 自定义活动的API端点在[此处](https://developer.adobe.com/marketo-apis/api/mapi/#operation/addCustomActivityUsingPOST)可用。

## 自定义对象与自定义活动

**自定义对象**

**自定义活动**

1

默认情况下，每个实例最多10个自定义对象。

默认情况下，最多有10个自定义活动。

2

每个自定义对象最多50个自定义对象字段。

每个自定义活动类型最多可以具有20个辅助属性。

3

最大1MM自定义对象记录；可能非常取决于您的订阅。

25个月后，将根据Marketo数据保留策略删除自定义活动。

4

可用作智能列表和智能营销活动中的过滤器和触发器。

可用作智能列表和智能营销活动中的过滤器和触发器。

5

可用于个性化电子邮件内容。

无法用于个性化电子邮件内容。

6

可以对自定义对象记录执行CRUD操作。

自定义活动中只允许创建和读取。

7

值可以更改时使用自定义对象（即“汽车颜色”从蓝色更改为红色）。

当跟踪已发生并且其详细信息无法更改的时刻（即“购买的汽车”）时，使用自定义活动。

8

自定义对象会告诉您事实。 即现值。

自定义活动可告知您过去发生的事件。

由&#x200B;_Amit_&#x200B;发布于&#x200B;_2020-10-18_

## 2021年1月更新

2021年1月，我们发布了新的REST API并解决了几个缺陷。 请参阅下面的完整更新列表。

* 添加了[提交表单](/help/rest-api/leads.md)终结点，该终结点允许您执行程序化表单提交。 第三方表单现在可以与Marketo表单集成，以利用现有的营销工作流程。
* 添加了[获取登陆页面完整内容](/help/rest-api/landing-pages.md)端点，该端点返回登陆页面的序列化HTML版本。 允许您在不登录Marketo Engage的情况下呈现登陆页面的完全个性化预览。 这有助于简化集成应用程序中的编辑和翻译工作流。
* 您现在可以配置可通过Velocity脚本访问的自定义对象的数量。 可在[此处](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/administration/email-setup/change-custom-object-retrieval-limits-in-velocity-scripting)找到配置说明。

### 缺陷分辨率

* 修复了[删除用户](/help/rest-api/user-management.md)端点将允许您删除自定义服务正在使用的仅API用户的问题。 现在它返回错误“611，您不能删除API服务中使用的API用户”。 [LM-141893]
* 修复了[获取用户](/help/rest-api/user-management.md)端点在某些情况下会返回已删除用户的问题。 [LM-141542]
* 修复了[克隆程序](https://developer.adobe.com/marketo-apis/api/asset/#operation/cloneProgramUsingPOST)终结点的问题。 如果指定的程序名称超过255个字符，则会返回“611， Unable to clone program error”。 现在，它返回“701，名称不能超过255个字符”。 [LM-143436]
* 修复了[批准登陆页面草稿](https://developer.adobe.com/marketo-apis/api/asset/#operation/approveLandingPageUsingPOST)端点的问题。 当您批准激活了移动设备版本的登陆页面时，在某些情况下，您会在桌面版本中看到移动设备版本中的内容。 [LM-146867]
* 修复了[取消批准登陆页面](https://developer.adobe.com/marketo-apis/api/asset/#operation/unapproveLandingPageByIdUsingPOST)端点的问题，该问题允许您取消批准一个或多个表单正在用作跟进页面的登陆页面。 它现在返回错误“709，取消批准登陆页面失败。 一个或多个表单正在使用登陆页面作为表单ID为&#x200B;[_formId1，formId2，..._]”的后续页面。 [LM-143326]

由&#x200B;_David_&#x200B;发布于&#x200B;_2021-01-15_

## Munchkin 160 Beta和Beacon API

**2021年1月27日：**&#x200B;某些将受“关联潜在客户”弃用影响的Marketo用户错误地收到一封电子邮件通知，指示他们在其一个或多个实例上启用了Munchkin Beta设置。 此版本将保留，直到通知正确的受众。 从Munchkin JavaScript版本160开始，[Beacon API](https://developer.mozilla.org/en-US/docs/Web/API/Beacon_API)成为Munchkin与Marketo后端通信的默认方式。 在2020年夏季通过&#x200B;**useBeaconAPI**&#x200B;配置参数发布版本159时，此选项成为可用选项。 与使用旧的XMLHttpRequest方法相比，信标API具有许多优势，但主要改进是它是一种用于HTTP通信的非阻塞异步API，可用于所有现代互联网浏览器。 虽然大多数Munchkin用户不会注意到网站行为发生变化，但此更新将阻止Munchkin在等待将点击事件提交到后端时阻止导航，更简单地说，这几乎消除了Munchkin在点击指向新页面的链接后导致浏览器“挂起”的可能性。 对于一些Marketo客户而言，这种情况很少发生，但却令人沮丧。

自2021年1月27日起，此版本的推出处于暂停状态，等待重新计划。 虽然预计不会出现与此更改相关的问题，并且在测试过程中也未发现任何问题，但Marketo无法测试Munchkin的所有可能部署配置，您可能希望预先测试这些更改，或者在正式发布此版本之前放弃此更改。 各种情况的说明如下所示。

### 测试信标API

如果您希望针对即将发布的版本测试更新的信标API，可以通过在外部测试页面上将&#x200B;**useBeaconAPI**&#x200B;参数添加到您的Munchkin配置中来执行此操作。 此测试适用于正式发布或Beta版本的Munchkin。 在第7行`Munchkin.init()`方法调用的第二个参数中显示配置参数： `{ 'useBeaconAPI': true}`

```javascript
<script type="text/javascript">
(function() {
  var didInit = false;
  function initMunchkin() {
    if(didInit === false) {
      didInit = true;
      Munchkin.init('299-BYM-827', {"useBeaconAPI":true});
    }
  }
  var s = document.createElement('script');
  s.type = 'text/javascript';
  s.async = true;
  s.src = '//munchkin.marketo.net/munchkin.js';
  s.onreadystatechange = function() {
    if (this.readyState == 'complete' || this.readyState == 'loaded') {
      initMunchkin();
    }
  };
  s.onload = initMunchkin;
  document.getElementsByTagName['head'](0).appendChild(s);
})();
</script>
```

### 在Munchkin登陆页面上禁用Marketo Beta

要在Marketo登陆页面上禁用Munchkin Beta，您需要访问订阅的“管理员”部分中的[Treasure Chest](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/administration/settings/enable-or-disable-treasure-chest-features)菜单，并将“登陆页面上的Munchkin Beta”设置更改为“已禁用”。

### 在外部页面上禁用Munchkin Beta

如果您已将Beta版本的Munchkin JavaScript部署到外部网页，并希望放弃此更改，直到它正式可用，您需要更改Munchkin JS代码片段以针对**munchkin。****js**&#x200B;文件而非**munchkin-beta文件。****js**&#x200B;文件。 在以下示例中，这是第11行的&#x200B;**s.src**&#x200B;变量的值。 您的代码片段可能与以下示例不完全相似，也可能会被标签管理器部署在外部页面上，您可能需要联系IT资源或启用Munchkin跟踪的网站管理者。

```javascript
<script type="text/javascript">
(function() {
  var didInit = false;
  function initMunchkin() {
    if(didInit === false) {
      didInit = true;
      Munchkin.init('299-BYM-827');
    }
  }
  var s = document.createElement('script');
  s.type = 'text/javascript';
  s.async = true;
  s.src = '//munchkin.marketo.net/munchkin.js';//This line should have the munchkin.js file, not munchkin-beta.js
  s.onreadystatechange = function() {
    if (this.readyState == 'complete' || this.readyState == 'loaded') {
      initMunchkin();
    }
  };
  s.onload = initMunchkin;
  document.getElementsByTagName['head'](0).appendChild(s);
})();
</script>
```

由&#x200B;_Kenny_&#x200B;发布于&#x200B;_2021-01-08_

## 电子邮件V1的最终API弃用

[几乎两年前](https://nation.marketo.com:443/t5/knowledgebase/email-editor-1-0-is-being-deprecated-june-18th/ta-p/250666)就开始弃用电子邮件V1，从2021年3月17日面向伦敦和荷兰订阅的3月维护版本和2021年3月19日面向所有其他订阅的维护版本开始，将终止对V1电子邮件的所有API支持。 在此版本之后，任何尝试通过资产API与V1电子邮件交互都将导致错误，并且不会采取任何操作。 已通知自2021年2月24日以来所有已知的其余用户，但可能仍然存在尝试与这些资源交互的集成。 受影响集成的最常见类型是提供数字资产管理、翻译和本地化的服务。 如果您发现集成由于此更改而失败，[您仍可以通过编辑和批准有问题的资源来升级这些资源](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/email-marketing/general/email-editor-2/transitioning-to-email-editor-2-0)。 电子邮件资产升级到V2后，您应该能够将其与集成服务结合使用。

由&#x200B;_Kenny_&#x200B;发布于&#x200B;_2021-03-17_

## 2021年5月更新

2021年5月，我们将发布新的REST API，增强现有REST API，并解决若干缺陷。 请参阅下面的完整更新列表。

* 添加了程序成员API，允许您检索、更新和删除程序成员资格记录。 有关详细信息，请参阅[REST API >潜在客户数据库>计划成员](/help/rest-api/program-members.md)。
* 添加了批量自定义对象提取API ，允许您导出与一对多关系中的潜在客户关联的第一级Marketo自定义对象记录。 有关详细信息，请参阅[REST API >批量提取>批量自定义对象提取](/help/rest-api/bulk-custom-object-extract.md)。
* 我们已增强[潜在客户API](/help/rest-api/leads.md)和[批量潜在客户提取API](/help/rest-api/bulk-lead-extract.md)，以允许用户检索Adobe Experience Cloud ID (ECID)。 这允许[从Adobe Experience Cloud](https://experienceleague.adobe.com/docs/marketo/using/product-docs/core-marketo-concepts/miscellaneous/set-up-adobe-experience-cloud-audience-sharing.html)同步受众的用户识别具有关联ECID的潜在客户。 这提供了[集成的可能性](https://adobeexchangeec.zendesk.com/hc/en-us/articles/360024277392-Adobe-Experience-Cloud-Using-the-ECID-for-integration)与其他Adobe Experience Cloud产品。
* 我们增强了[批量潜在客户导入API](/help/rest-api/bulk-lead-import.md)，以支持在导入过程中将潜在客户添加到公司记录。 这是通过在导入文件中包含&#x200B;**externalCompanyId**&#x200B;字段来完成的。
* 我们增强了多个项目端点，以提供与Marketo Engage UI中的功能对等性。 我们已增强[创建程序](/help/rest-api/assets.md)和[克隆程序](https://developer.adobe.com/marketo-apis/api/asset/)端点，以允许对事件程序执行创建、克隆或移动操作。 这适用于通过将事件程序“嵌套”在其他程序类型下来组织这些程序的用户。 我们还增强了[删除程序](https://developer.adobe.com/marketo-apis/api/asset/)端点，以允许删除包含以下资源的程序：推送通知、应用程序内消息、报告、嵌入了社交Assets的登陆页面。
* 作为Marketo管理员，您可以[将特定字段标记为“敏感”](https://experienceleague.adobe.com/zh-hans/docs/marketo/using/home)，以便其值[绝不会预先填充在表单中](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/demand-generation/forms/form-fields/disable-pre-fill-for-a-form-field)，从而保护用户的敏感数据。 我们增强了多个表单字段端点以提供与Marketo Engage UI中此功能的对等性。

### 缺陷分辨率

* 修复了删除项目端点的问题。 如果尝试删除共享文件夹中的程序，将返回“611，系统错误”。 现在，它会返回“ ”Target程序位于共享文件夹中且无法删除。 在尝试删除之前，必须取消共享该文件夹。”
* 修复了克隆程序端点的问题。 如果尝试克隆流程步骤中包含DateTime的程序，它将返回“611，系统错误”。 现在，它成功地克隆了程序。
* 修复了创建程序端点的问题，该问题意外地允许您在电子邮件程序下创建程序（这是不允许的）。
* 修复了克隆程序端点的问题。 如果您克隆了包含登陆页面的项目，则目标项目中登陆页面的名称在项目名称和登陆页面名称之间缺少下划线。 如`http://<_pod_\>.marketo.com/lp/<_munchkin_\>/<_program name_\>**_**<_LP name_\>.html`

由&#x200B;_David_&#x200B;发布于&#x200B;_2021-05-07_

## 加入Adobe交易所限时优惠

Marketo Engage合作伙伴社区支持是我们客户成功的支柱之一。 我们希望确保Marketo Engage集成生态系统在Exchange Marketplace中有充分的代表性，并为LaunchPoint合作伙伴提供特别优惠。 在极短的时间内（不会延长），我们将向LaunchPoint合作伙伴提供到2022年底在Exchange计划中的免费创新合作伙伴关系（价值约1.5万美元）。 我们设计了此产品，以鼓励LaunchPoint合作伙伴在Exchange合作伙伴门户中创建其集成列表，然后可以在Adobe Exchange Marketplace中公开搜索这些集成列表。 查看截至2022年12月您免费获得的“创新合作伙伴关系”权益的完整列表。

1. 转到[Adobe Exchange合作伙伴支持中心](https://adobeexchangeec.zendesk.com/hc/en-us?mkt_tok=NjA4LURIVi05MTUAAAF-P5lIeVWOuBmKMS_uE_NpgFKtC0ukt7z_ksnq_Sbzb6mzXUuXpqpqQeujtPdZ24WcjMdptygQSR9XrYt_Cw)
1. 单击右上角的“提交请求”
1. 在&#x200B;**中，请从下面选择您的问题**&#x200B;下拉列表中选择“Adobe Exchange支持”
1. 在&#x200B;**您的电子邮件地址**&#x200B;中输入您的电子邮件地址
1. 在&#x200B;**主题**&#x200B;框中，输入“LaunchPoint选件”
1. 在&#x200B;**Description**&#x200B;框中，输入“LaunchPoint选件”
1. 在&#x200B;**支持类型**&#x200B;下拉列表中，选择“项目支持”
1. 在&#x200B;**Adobe Exchange产品**&#x200B;下拉列表中，选择“Adobe Exchange项目”
1. 提交表单。 我们的团队很快便会与您联系！

由&#x200B;_David_&#x200B;发布于&#x200B;_2021-07-22_

## 2021年8月更新

2021年8月，我们增强了现有REST API，并解决了几个缺陷。 请参阅下面的完整更新列表。

* 我们增强了批量活动提取API，以允许用户使用主要属性对6种不同的活动类型进行筛选。 有关详细信息，请参阅[批量活动提取](/help/rest-api/bulk-activity-extract.md)。
* 为了给Marketo Sales Connect用户更多访问其销售活动数据的权限，我们启用了其他销售活动属性。 我们为发送销售电子邮件、打开销售电子邮件并单击“销售电子邮件”活动添加了以下属性：

* Marketo销售人员ID - Sales Connect中人员记录的唯一ID
* Sales Campaign Name — 销售活动的名称（如果电子邮件是销售活动的一部分）
* Sales Campaign URL — 如果电子邮件是销售活动的一部分，则为销售活动提供的Sales Connect URL
* Sales Template Name — Sales Connect中电子邮件模板的名称（如果使用模板）
* Sales Template URL — 电子邮件模板的Sales Connect URL （如果使用模板）

### 电子邮件

* 我们通过添加`earliestUpdatedAt`/`latestUpdatedAt`筛选器增强了“获取电子邮件”端点。 这允许您使用`updatedAt`字段仅搜索电子邮件的子集，并允许增量同步。
* 我们增强了“获取电子邮件”、“按名称获取电子邮件”、“按ID获取电子邮件”端点以支持检索[Champion和Challenger](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/email-marketing/general/functions-in-the-editor/email-tests-champion-challenger/add-an-email-champion-challenger)类型的电子邮件记录。

### 缺陷分辨率

* 修复了获取用户端点的问题。 未返回已颁发[营销日历](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/core-marketo-concepts/marketing-calendar/understanding-the-calendar/issue-revoke-a-marketing-calendar-license)许可证的用户。 现在，可正确返回“营销日历”用户。
* 修复了提交表单端点的问题。 如果存在重复的潜在客户记录，可使用“提交表单”发出“1007，多个潜在客户匹配查找条件”错误。 提交表单现在会以[Forms 2.0 API](/help/javascript-api/forms-api-reference.md)相同的方式更新最近更新的记录。
* 改进了“更新潜在客户字段”和“创建潜在客户字段”端点返回的若干误导性错误消息。 [LM-151890、LM-151888、LM-151889]
* 修复了“按名称获取潜在客户字段”和“获取潜在客户字段端点”的问题。 两个端点都可能会返回略微过时的信息。 它们现在始终返回当前信息。
* 修复了使用“范围”HTTP标头进行部分检索时未返回范围中最后一个字节的[批量提取API](/help/rest-api/bulk-extract.md)的问题。
* 修复了更新代码片段元数据端点的问题。 更新代码片段名称或描述时，代码片段状态将更改为“已批准并草稿”。 现在，在更新代码片段名称或描述后，代码片段状态将保持不变。
* 修复了“添加电子邮件模块”端点的问题。 添加包含代码片段的模块时，该模块返回“611，系统错误”。 现在，它可以将模块正确添加到电子邮件中。
* 修复了删除项目端点的问题。 删除包含应用程序内消息本地资产的程序时，它会返回“611，系统错误”。 现在可以正确删除程序。

由&#x200B;_David_&#x200B;发布于&#x200B;_2021-08-22_

## Munchkin版本161推出

在2021年9月7日，Munchkin版本161将开始向10%的订阅推广，其中启用了Munchkin Beta的订阅将从9月16日起50%推广，9月30日将全面推广100%。 此更改将影响Marketo登陆页面，以及提供给外部登陆页面的munchkin-beta.js文件版本，这些登陆页面从新版本已转出的订阅加载。 此版本完全弃用Munchkin关联潜在客户方法，该方法允许将人员数据提交到Marketo订阅以及具有已知人员记录的关联Web浏览历史记录。 正在删除关联潜在客户，以支持更现代和更安全的替代方案，如[Forms JS API](/help/javascript-api/forms-api-reference.md)、表单提交API和[关联潜在客户REST API](/help/rest-api/leads.md)。 如果您或您的组织使用此方法，则应当在2021年10月12日之前（计划于该日期开始10月版本推出）从使用量中迁移出来。 如果您不再希望选择使用Munchkin测试版，可以通过将“登陆页面上的Munchkin Beta”功能切换到`disabled`Treasure Chest菜单[中的](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/administration/settings/enable-or-disable-treasure-chest-features)来禁止在Marketo登陆页面上使用。 如果您已将Munchkin Beta JavaScript部署到外部网页，并且希望切换到默认的Munchkin发布渠道，则需要更新代码片段，以从munchkin.js而不是munchkin-beta.js加载Munchkin JavaScript。

由&#x200B;_Kenny_&#x200B;发布于&#x200B;_2021-08-24_

## TLS 1.0和TLS 1.1 Munchkin服务生命周期终止

从2021年10月21日开始，munchkin.marketo.net (用于向访客提供Munchkin JavaScript)将不再接受使用[TLS 1.0或TLS 1.1的连接。](https://en.wikipedia.org/wiki/Transport_Layer_Security)这些加密标准不再作为Web安全最佳实践的一部分被接受，并且不再受到现代浏览器版本的支持。 预计此更改不会产生任何负面影响。

由&#x200B;_Kenny_&#x200B;发布于&#x200B;_2021-10-04_

## 2021年10月更新

2021年10月，我们增强了现有REST API，并解决了几个缺陷。 请参阅下面的完整更新列表。

* 我们增强了[提交表单](https://developer.adobe.com/marketo-apis/api/mapi/#operation/SubmitFormUsingPOST)端点，以支持在表单提交过程中使用项目成员自定义字段。 或者，可以将程序指定为要向其中添加表单的程序，和/或指定要向其中添加程序成员自定义字段的程序，如[此处](/help/rest-api/leads.md)所述。
我们已增强[获取项目成员](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getProgramMembersUsingGET)终结点，以支持基于updatedAt属性的基于日期范围的查询。 这是通过传递开始和结束日期时间参数来完成的，如[此处](/help/rest-api/program-members.md)所述。
* 我们已增强[潜在客户字段](/help/rest-api/leads.md) API以支持[敏感字段](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/administration/field-management/mark-a-field-as-sensitive)。 [按名称获取潜在客户字段](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getLeadFieldByNameUsingGET)、[获取潜在客户字段](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getLeadFieldsUsingGET)、[创建潜在客户字段](https://developer.adobe.com/marketo-apis/api/mapi/#operation/createLeadFieldUsingPOST)和[更新潜在客户字段](https://developer.adobe.com/marketo-apis/api/mapi/#operation/updateLeadFieldUsingPOST)端点现在支持isSensitive特性。

### 缺陷分辨率

* 修复了[用户管理](/help/rest-api/user-management.md) API的问题。 属于配置为与[Sales Insight](https://business.adobe.com/products/marketo/sales-insight.html)一起使用的Marketo用户。 这些用户现在由[获取用户](https://developer.adobe.com/marketo-apis/api/user/#operation/getUsersUsingGET)端点返回，这些用户现在可以使用[删除用户](https://developer.adobe.com/marketo-apis/api/user/#operation/deleteUserUsingPOST)端点删除。 [LM-155864]
* 修复了添加[富文本字段](https://developer.adobe.com/marketo-apis/api/asset/#tag/Form-Fields/addRichTextFieldUsingPOST)终结点的问题。 向电子邮件、登陆页面、代码片段或表单添加长度超过6.5万个字符的富文本字段时，会返回“611，系统错误”。 它现在返回错误“701，无法完成操作。 “content”超过了最大长度65,535字节”。

由&#x200B;_David_&#x200B;发布于&#x200B;_2021-10-25_

## 2022年1月更新

2022年1月，我们增强了现有REST API，并解决了几个缺陷。 请参阅下面的完整更新列表。

* 我们已增强[批量自定义对象提取](/help/rest-api/bulk-custom-object-extract.md) API，以允许用户使用&#x200B;**updatedAt**&#x200B;日期范围进行筛选。
* 添加了程序成员字段元数据API，允许您创建、更新和检索程序成员字段的元数据。 有关详细信息，请参阅[计划成员>字段](/help/rest-api/program-members.md)。
* 添加了公司字段元数据API，允许您检索公司字段的元数据。 有关详细信息，请参阅[公司>字段](/help/rest-api/companies.md)。
* 添加了允许您检索Opportunity字段元数据的机会字段元数据API。 有关详细信息，请参阅[机会>字段](/help/rest-api/opportunities.md)。
* 添加了指定帐户字段元数据API，允许您检索指定帐户字段的元数据。 有关详细信息，请参阅[命名帐户>字段](/help/rest-api/named-accounts.md)。
* 更新了字段元数据端点以返回新的布尔属性&#x200B;**isApiCreated**，以指示REST API是否创建了字段。

### 缺陷分辨率

* 修复了调用[创建潜在客户字段](https://developer.adobe.com/marketo-apis/api/mapi/#operation/createLeadFieldUsingPOST)端点的时间与新创建的潜在客户字段在智能列表中可用的时间之间的延迟问题。 [LM-152838]
* 修复了[创建潜在客户字段](https://developer.adobe.com/marketo-apis/api/mapi/#operation/createLeadFieldUsingPOST)端点的问题，该问题导致在Marketo Engage UI中用于[将字段添加到表单](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/demand-generation/forms/creating-a-form/add-a-field-to-a-form)的表单字段下拉列表中创建的字段不可用。 [LM-158243]
* 修复了在指定isTriggerable=true参数时未返回可触发营销活动的[Get Campaigns](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getCampaignsUsingGET)端点的问题。 [LM-158283]
* 修复了在某些情况下[按列表ID](https://developer.adobe.com/marketo-apis/api/mapi/#operation/deleteTokenByNameUsingPOST)获取潜在客户端点将返回错误“611，系统错误”的问题。 [LM-157214]
* 已清除[更新潜在客户字段](/help/rest-api/leads.md)终结点返回的若干错误消息。 [LM-151886、LM-151888、LM-151889]

由&#x200B;_David_&#x200B;发布于&#x200B;_2022-01-27_

## 2022年3月更新

2022年3月，我们增强了现有REST API，并解决了几个缺陷。 请参阅下面的完整更新列表。

* 我们已将&#x200B;**actionResult**&#x200B;字段添加到批量活动提取API生成的导出文件中。 此字段可用于区分成功、跳过和失败的活动。
* 我们已将&#x200B;**isOpenTrackingDisabled**&#x200B;字段添加到来自[电子邮件API](/help/rest-api/emails.md)的响应。 此字段可用于确定是否启用了[禁用打开跟踪](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/email-marketing/general/email-editor-2/email-editor-v2-0-overview)功能。
* 我们添加了两个端点，以便您有选择地管理项目标记。 [更新程序标记](/help/rest-api/programs.md)端点允许您有选择地更新程序标记。 [删除程序标记](/help/rest-api/programs.md)终结点允许您有选择地删除程序标记。
* 我们已将&#x200B;**isExecutable**&#x200B;参数添加到[克隆智能营销活动](/help/rest-api/smart-campaigns.md)终结点。 此参数允许您将程序克隆为可执行程序。
* 我们已将&#x200B;**headStart**&#x200B;字段添加到[程序API](/help/rest-api/programs.md)。 这允许您创建、更新和检索电子邮件程序的[Head Start](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/email-marketing/email-programs/email-program-actions/head-start-for-email-programs)设置。

### 缺陷分辨率

* 修复了[获取电子邮件动态内容](https://developer.adobe.com/marketo-apis/api/asset/#operation/getEmailDynamicContentUsingGET)终结点的问题。 在尝试从已断开模板关系的电子邮件中检索包含动态内容的主题行时，返回了一个错误(709)：“API只允许对包含模板的电子邮件进行操作”。 现在，端点会返回动态内容。 [LM-152331]
* 修复了[同步潜在客户](https://developer.adobe.com/marketo-apis/api/mapi/#operation/syncLeadUsingPOST)终结点的问题。 当使用externalSalesPersonId将销售人员与使用externalSalesPersonId的销售线索相关联，并使用action = createDuplicate时，销售人员关联将不会发生。 [LM-158990]

### Adobe IMS集成

* 已登记到[Adobe IMS](https://experienceleague.adobe.com/zh-hans/docs/marketo/using/product-docs/administration/marketo-with-adobe-identity/adobe-identity-management-overview)的用户无法使用所有[Marketo用户管理API](/help/rest-api/user-management.md)。 在调用与Adobe IMS集成的Marketo实例时，以下端点将返回错误：[邀请用户](https://developer.adobe.com/marketo-apis/api/user/#operation/inviteUserUsingPOST)、[按Id获取受邀请用户](https://developer.adobe.com/marketo-apis/api/user/#operation/getInvitedUserUsingGET)、[更新用户属性](https://developer.adobe.com/marketo-apis/api/user/#operation/updateUserAttributeUsingPOST)、[删除用户](https://developer.adobe.com/marketo-apis/api/user/#operation/deleteUserUsingPOST)和[删除受邀请用户](https://developer.adobe.com/marketo-apis/api/user/#operation/deleteInvitedUserUsingPOST)。 作为替代，应使用[Adobe用户管理API](https://developer.adobe.com/umapi/)。

由&#x200B;_David_&#x200B;发布于&#x200B;_2022-03-14_

## 2022年5月更新 — 

2022年5月，我们增强了现有REST API，并解决了几个缺陷。 请参阅下面的完整更新列表。

* 我们已添加在您的Marketo Engage实例中启用[SFDC同步](/help/rest-api/companies.md)或[Microsoft Dynamics同步](/help/rest-api/opportunities.md)时检索[公司](/help/rest-api/sales-persons.md)、[机会](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/crm-sync/salesforce-sync/sfdc-sync-details/sfdc-sync-field-sync)和[销售人员](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/crm-sync/microsoft-dynamics/microsoft-dynamics-sync-details/microsoft-dynamics-sync-user-sync)记录的功能。
* 我们更新了[获取电子邮件动态内容](https://developer.adobe.com/marketo-apis/api/asset/#operation/getEmailDynamicContentUsingGET)终结点，允许您从电子邮件主题行检索[动态内容](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/email-marketing/general/functions-in-the-editor/using-dynamic-content-in-an-email)。 无论给定的电子邮件是否链接到电子邮件模板，此功能都会起作用。

`POST /rest/asset/v1/form/{id}/field/State.json?values=[{"label":"Alaska"},{"value":"AK"},{"label":"West Virginia","value":"WV"},{"label":"Wyoming","value":"WY"}]`

* 我们已更新[添加表单字段可见性规则](https://developer.adobe.com/marketo-apis/api/asset/#operation/getAllProgramMemberFieldsUsingGET)终结点，允许您为&#x200B;**isNot**&#x200B;类型[不可见性规则](/help/rest-api/forms.md)添加多个比较值。 示例如下：

`POST /rest/asset/v1/form/{id}/field/LastName/visibility.json?visibilityRule={"ruleType":"show","rules":[{"subjectField":"LastName","operator":"isNot","values":["A","B","C"]}`

### 缺陷分辨率

* 修复了为[leadFormFields](/help/rest-api/leads.md)参数中的属性传递“null”时[提交表单](/help/rest-api/leads.md)端点发生的问题，返回了一个错误：“611，系统错误”。 它现在正确返回“1003，表单验证失败”错误。 [LM-162213]

由&#x200B;_David_&#x200B;发布于&#x200B;_2022-05-09_

## 2022年8月更新

2022年8月，我们增强了现有REST API。 请参阅下面的完整更新列表。

我们添加了几个新筛选器，在调用“创建导出程序成员作业”端点时可以使用这些筛选器。 请注意，许多过滤器可以相互组合使用以细化提取的数据集。

* **programIds**&#x200B;筛选器可用于指定最多10个程序标识符，以帮助提高吞吐量。
* **isExhausted**&#x200B;筛选器可用于筛选已用完内容[的](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/email-marketing/drip-nurturing/using-engagement-programs/people-who-have-exhausted-content)人员的记录。
* **nurtureCadence**&#x200B;筛选器可用于根据[参与计划节奏](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/core-marketo-concepts/smart-campaigns/program-flow-actions/change-engagement-program-cadence)筛选记录。
* **statusNames**&#x200B;筛选器可用于筛选一个或多个[程序状态](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/core-marketo-concepts/programs/creating-programs/understanding-program-membership)的记录。
* **updatedAt**&#x200B;筛选器可用于根据日期范围筛选记录。

### 公告

* [标识](https://developer.adobe.com/marketo-apis/api/identity/#operation/identityUsingGET)终结点的行为已更改。 当您调用终结点并且不包含&#x200B;**access_token**&#x200B;参数时，将返回“603，访问被拒绝”错误。 以前，返回“600，空访问令牌”错误。 请注意，“600，空访问令牌”错误已被弃用。

由&#x200B;_David_&#x200B;发布于&#x200B;_2022-09-03_

## 2022年10月更新

2022年10月，我们增强了现有REST API。 请参阅下面的完整更新列表。

* 我们增强了[Bulk Lead Import API](/help/rest-api/bulk-lead-import.md)，以支持在导入过程中将Lead添加到Sales Persones记录。 此操作可通过在导入文件中包含&#x200B;**externalSalesPersonId**&#x200B;字段来完成。
* 修复了在创建得分类型字段时发生的[创建潜在客户字段](/help/rest-api/leads.md)端点问题。 这些字段在Marketo Engage UI中的[更改得分](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/core-marketo-concepts/smart-campaigns/flow-actions/change-score)流程操作中不可用。 [LM-166815]

### 公告

* 项目成员资格属性`acquiredBy`已更改为可更新。

由&#x200B;_David_&#x200B;发布于&#x200B;_2022-10-18_

## 即将更改Marketo Forms REST AP

从2022.R2版本开始（当前计划于2023年3月24日这一周发布），Adobe Marketo Engage Forms Asset API将始终只返回不带前缀项目名称的表单名称，无论该表单是否为项目的子级。 此更改将使Forms API有关资源名称的行为与其他Adobe Marketo Engage Asset API保持一致。 为避免服务中断，您应该审查任何使用Marketo Engage Forms API的集成，并与您的集成商合作，查看是否需要进行任何更改来适应此要求。在此即将进行的更改之前，Forms API返回的名称，不一致地为作为营销活动中项目子项的表单使用项目名称作为前缀。 例如，名为“Form 1”的表单（它是“Program 1”的子项）的名称可能由API返回为：Program 1.Form 1或Form 1从2022.R2版本开始，表单名称将始终不带前缀的程序名称。 使用相同的示例，名称将始终为：表单1

由&#x200B;_Kenny_&#x200B;发布于&#x200B;_2022-11-04_

## 2023年1月更新

2023年1月，我们对Admin UI进行了与API相关的增强，并将发布两项公告。 请参阅下面的完整更新列表。

管理员UI

### 批量潜在客户提取

* 我们增强了Marketo Engage管理UI，允许您查看针对订阅的“批量提取API”每日容量分配。 此外，您还可以查看API用户在过去7天内的容量使用情况。 可在[此处](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/administration/settings/bulk-export-api-information)找到更多信息。

### 缺陷分辨率

* 修复了[删除机会](https://developer.adobe.com/marketo-apis/api/mapi/#operation/deleteOpportunitiesUsingPOST)终结点的问题。 在某些情况下，不会生成“从机会中删除”活动。 [LM-172208]

### 公告

* 请参阅Marketo社区上关于REST API的[这篇文章](https://nation.marketo.com/t5/product-documents/upcoming-change-to-marketo-rest-api/ta-p/331698)以及对HTTP响应消息[原因短语](https://www.rfc-editor.org/rfc/rfc7230#section-3.1.2)的更改。
* 项目成员资格属性&#x200B;**statusReason**&#x200B;已更改为可更新。

由&#x200B;_David_&#x200B;发布于&#x200B;_2023-01-21_
