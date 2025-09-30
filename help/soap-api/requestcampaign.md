---
title: requestCampaign
feature: SOAP, Smart Campaigns
description: 通过SOAP requestCampaign在Marketo Smart Campaign中运行潜在客户，包括参数集、令牌处理、100个潜在客户限制、XML请求和PHP Java示例。
exl-id: b5367eb9-4f4c-4e1d-8b6d-36de8f134f0e
source-git-commit: 7557b9957c87f63c2646be13842ea450035792be
workflow-type: tm+mt
source-wordcount: '298'
ht-degree: 3%

---

# requestCampaign

此函数在Marketo Smart Campaign中运行现有的Marketo潜在客户。 Smart Campaign必须具有具有Web服务API源的“已请求营销活动”触发器（请参阅下文）。

![Web服务API](assets/webserviceapi.png)

可以使用两个参数集。 第一种情况是使用`campaignName` + `programName` + `programTokenList`。 在这种情况下，`programTokenList`可以为空。 第二种情况是单独使用`campaignId`。 任何其他组合都会引发错误的参数异常。

注意：每个调用最多有100个leadKey值。 忽略其他键。

| 字段名称 | 必需/可选 | 描述 |
| --- | --- | --- |
| leadList->leadKey->keyType | 必需 | `keyType`允许您指定查询潜在客户的字段。 可能的值包括：`IDNUM`、`EMAIL`、`SFDCLEADID`、`LEADOWNEREMAIL`、`SFDCACCOUNTID`、`SFDCCONTACTID`、`SFDCLEADID`、`SFDCLEADOWNERID`、`SFDCOPPTYID` |
| leadList->leadKey->keyValue | 必需 | `keyValue`是您希望作为潜在客户查询依据的值。 |
| 源 | 必需 | 营销活动源。 可能的值： `MKTOWS`或`SALES`。 枚举是在WSDL中定义的。 |
| campaignId | 当`campaignName`、`programName`和`programTokenList`在参数站点中同时存在时是可选的；否则，需要`campaignId` | 营销活动的ID。 注意：如果同时传递`campaignID`和`campaignName`，则会发生错误的参数错误。 |
| campaignName | 当campaignId存在时为可选；否则，在集合中需要为`campaignName`、programName和programTokenList | 营销活动的名称 |
| 项目名称 | 当campaignId存在时为可选；否则，在集合中需要为`campaignName`、programName和programTokenList | 项目的名称 |
| programTokenList | 当campaignId存在时为可选；否则在集中需要为`campaignName`、`programName`和`programTokenList` | 要在营销活动中使用的令牌数组。 指定令牌时，需要programName和`campaignName`。 |
| programTokenList->attrib->name | 可选 | 您希望传递值的程序令牌的名称。 例如：{{my.message}} |
| programTokenList->attrib->value | 可选 | 指定令牌名称的值。 |

## 请求XML

```xml
<?xml version="1.0" encoding="UTF-8"?>
<SOAP-ENV:Envelope xmlns:SOAP-ENV="http://schemas.xmlsoap.org/soap/envelope/" xmlns:ns1="http://www.marketo.com/mktows/">
  <SOAP-ENV:Header>
    <ns1:AuthenticationHeader>
      <mktowsUserId>demo17_1_809939944BFABAE58E5D27</mktowsUserId>
      <requestSignature>48397ad47b71a1439f13a51eea3137df46441979</requestSignature>
      <requestTimestamp>2013-08-01T12:31:14-07:00</requestTimestamp>
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

## 响应XML

```xml
<?xml version="1.0" encoding="UTF-8"?>
<SOAP-ENV:Envelope xmlns:SOAP-ENV="http://schemas.xmlsoap.org/soap/envelope/" xmlns:ns1="http://www.marketo.com/mktows/">
  <SOAP-ENV:Body>
    <ns1:successRequestCampaign>
      <result>
        <success>true</success>
      </result>
    </ns1:successRequestCampaign>
  </SOAP-ENV:Body>
</SOAP-ENV:Envelope>
```

## 示例代码 — PHP

```php
 <?php

  $debug = true;

  $marketoSoapEndPoint     = "";  // CHANGE ME
  $marketoUserId           = "";  // CHANGE ME
  $marketoSecretKey        = "";  // CHANGE ME
  $marketoNameSpace        = "http://www.marketo.com/mktows/";

  // Create Signature
  $dtzObj = new DateTimeZone("America/Los_Angeles");
  $dtObj  = new DateTime('now', $dtzObj);
  $timeStamp = $dtObj->format(DATE_W3C);
  $encryptString = $timeStamp . $marketoUserId;
  $signature = hash_hmac('sha1', $encryptString, $marketoSecretKey);

  // Create SOAP Header
  $attrs = new stdClass();
  $attrs->mktowsUserId = $marketoUserId;
  $attrs->requestSignature = $signature;
  $attrs->requestTimestamp = $timeStamp;
  $authHdr = new SoapHeader($marketoNameSpace, 'AuthenticationHeader', $attrs);
  $options = array("connection_timeout" => 20, "location" => $marketoSoapEndPoint);
  if ($debug) {
    $options["trace"] = true;
  }

  // Create Request
  $leadKey = array("keyType" => "EMAIL", "keyValue" => "lead@company.com");
  $leadKey2 = array("keyType" => "EMAIL&qquot;, "keyValue" => "anotherlead@company.com");

  $leadList = new stdClass();
  $leadList->leadKey = array($leadKey, $leadKey2);

  $source = "MKTOWS";
  $campaignId = "4496";

  $paramsRequestCampaign = new stdClass();
  $paramsRequestCampaign->campaignId = $campaignId;
  $paramsRequestCampaign->source = $source;
  $paramsRequestCampaign->leadList = $leadList;

  $params = array("paramsRequestCampaign" => $paramsRequestCampaign);

  $soapClient = new SoapClient($marketoSoapEndPoint ."?WSDL", $options);
  try {
    $response = $soapClient->__soapCall('requestCampaign', $params, $options, $authHdr);
  }
  catch(Exception $ex) {
    var_dump($ex);
  }
  if ($debug) {
    print "RAW request:\n" .$soapClient->__getLastRequest() ."\n";
    print "RAW response:\n" .$soapClient->__getLastResponse() ."\n";
  }
  print_r($response);
?>
```

## 示例代码 — Java

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

## 示例代码 — Ruby

```ruby
require 'savon' # Use version 2.0 Savon gem
require 'date'

mktowsUserId = "" # CHANGE ME
marketoSecretKey = "" # CHANGE ME
marketoSoapEndPoint = "" # CHANGE ME
marketoNameSpace = "http://www.marketo.com/mktows/"

#Create Signature
Timestamp = DateTime.now
requestTimestamp = Timestamp.to_s
encryptString = requestTimestamp + mktowsUserId
digest = OpenSSL::Digest.new('sha1')
hashedsignature = OpenSSL::HMAC.hexdigest(digest, marketoSecretKey, encryptString)
requestSignature = hashedsignature.to_s

#Create SOAP Header
headers = {
    'ns1:AuthenticationHeader' => { "mktowsUserId" => mktowsUserId, "requestSignature" => requestSignature,
    "requestTimestamp"  => requestTimestamp
    }
}

client = Savon.client(wsdl: 'http://app.marketo.com/soap/mktows/2_3?WSDL', soap_header: headers, endpoint: marketoSoapEndPoint, open_timeout: 90, read_timeout: 90, namespace_identifier: :ns1, env_namespace: 'SOAP-ENV')

#Create Request
request = {
    :source => "MKTOWS",
    :campaign_id => "4496",
    :lead_list => {
        :lead_key => {
            :key_type => "EMAIL",
            :key_value => "lead@company.com" },
        :lead_key! => {
            :key_type => "EMAIL",
            :key_value => "anotherlead@company.com" }
    }
}

response = client.call(:request_campaign, message: request)

puts response
```
