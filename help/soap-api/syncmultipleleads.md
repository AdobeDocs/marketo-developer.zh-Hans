---
title: syncMultipleLeads
feature: SOAP
description: syncMultipleLeads SOAP调用
exl-id: 91980b82-dff9-48a7-b03e-20dce9d0d046
source-git-commit: 981ed9b254f277d647a844803d05a1a2549cbaed
workflow-type: tm+mt
source-wordcount: '221'
ht-degree: 3%

---

# syncMultipleLeads

此函数请求&#x200B;_多个_&#x200B;潜在客户记录的插入或更新(upsert)操作。 在更新现有潜在客户时，可以使用以下键之一标识该潜在客户：

- Marketo ID
- 外部系统ID
- 电子邮件

如果存在多个键，则Marketo ID优先于`ForeignSysPersonId`，并将更新后者。 但是，如果电子邮件也以键的形式存在，则不会更新电子邮件，除非在属性列表中指定了该电子邮件。

我们的建议是批量大小不得大于300。 不支持更大的尺寸，这可能会导致超时并在极端情况下进行限制。

通过此函数调用，您可以关闭重复数据消除功能。 如果dedupEnabled设置为true ，并且没有提供其他唯一标识符(`foreignSysPersonId`或Marketo潜在客户ID)，则使用电子邮件地址对潜在客户记录进行重复数据删除。 请记住，传入false将在Marketo中创建重复项。

## 请求

| 字段名称 | 必需/可选 | 描述 |
| --- | --- | --- |
| leadRecordList->leadRecord | 必需 | 要同步的LeadRecords数组。 LeadRecords必须指定潜在客户ID、电子邮件或ForeignSysPersonId |
| dedupEnabled | 可选 | 可选值，您可以使用该值关闭重复数据消除功能。 传入值`false`将在Marketo中创建重复项 |

## 请求XML

```xml
<?xml version="1.0" encoding="UTF-8"?>
<SOAP-ENV:Envelope xmlns:SOAP-ENV="http://schemas.xmlsoap.org/soap/envelope/" xmlns:ns1="http://www.marketo.com/mktows/">
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
          <Email>em1000@etestd.marketo.net</Email>
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
          <Email>em1001@etestd.marketo.net</Email>
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

## 响应XML

```xml
<?xml version="1.0" encoding="UTF-8"?>
<SOAP-ENV:Envelope xmlns:SOAP-ENV="http://schemas.xmlsoap.org/soap/envelope/" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:ns1="http://www.marketo.com/mktows/">
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

## 示例代码 — PHP

```php
<?php
$debug = true;
$marketoSoapEndPoint    = "";  // CHANGE ME
$marketoUserId      = "";  // CHANGE ME
$marketoSecretKey   = "";  // CHANGE ME
$marketoNameSpace       = "http://www.marketo.com/mktows/";

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
$options = array("connection_timeout" => 15, "location" => $marketoSoapEndPoint);
if ($debug) {
  $options["trace"] = 1;
}

// Create Request
for ($i=1000; $i < 1002; ++$i)
{
  $uid = $i;
  $attrs = array();
  $attr1 = new stdClass();
  $attr1->attrName = "Company";
  $attr1->attrValue = "Marketo$uid";
  $attr2 = new stdClass();
  $attr2->attrName = "Phone";
  $attr2->attrValue = "650-555-$uid";
  $attrs[] = $attr1;
  $attrs[] = $attr2;

  $attrList = new stdClass();
  $attrList->attribute = $attrs;

  $leadRec = new stdClass();
  $leadRec->Email = 'em' . $uid . '@etestd.marketo.net';
  $leadRec->leadAttributeList = $attrList;
  $leads[] = $leadRec;
}
$leadRecordList = new stdClass();
$leadRecordList->leadRecord = $leads;
$params = new stdClass();
$params->leadRecordList = $leadRecordList;
$params->dedupEnabled = true;
$soapClient = new SoapClient($marketoSoapEndPoint ."?WSDL", $options);
try {
  $leads = $soapClient->__soapCall('syncMultipleLeads', array($params), $options, $authHdr);
  //      print_r($leads);
}
catch(Exception $ex) {
  var_dump($ex);
}
if ($debug) {
  print "RAW request:\n" .$soapClient->__getLastRequest() ."\n";
  print "RAW response:\n" .$soapClient->__getLastResponse() ."\n";
}
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

public class SyncMultipleLeads {

    public static void main(String[] args) {
        System.out.println("Executing syncMultipleLeads");
        try {
            URL marketoSoapEndPoint = new URL("https://100-AEK-913.mktoapi.com/soap/mktows/2_1" + "?WSDL");
            String marketoUserId = "demo17_1_809934544BFABAE58E5D27";
            String marketoSecretKey = "27272727aa";

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

            JAXBElement<Boolean> dedup = objectFactory.createParamsSyncMultipleLeadsDedupEnabled(true);
            request.setDedupEnabled(dedup);

            ArrayOfLeadRecord arrayOfLeadRecords = new ArrayOfLeadRecord();

            // Create First Lead Record
            LeadRecord rec1 = new LeadRecord();

            JAXBElement<String> email = objectFactory.createLeadRecordEmail("t@t.com");
            rec1.setEmail(email);

            Attribute attr1 = new Attribute();
            attr1.setAttrName("FirstName");
            attr1.setAttrValue("George");

            Attribute attr2 = new Attribute();
            attr2.setAttrName("LastName");
            attr2.setAttrValue("of the Jungle");

            ArrayOfAttribute aoa = new ArrayOfAttribute();
            aoa.getAttributes().add(attr1);
            aoa.getAttributes().add(attr2);

            QName qname = new QName("http://www.marketo.com/mktows/", "leadAttributeList");
            JAXBElement<ArrayOfAttribute> attrList = new JAXBElement(qname, ArrayOfAttribute.class, aoa);

            rec1.setLeadAttributeList(attrList);
            arrayOfLeadRecords.getLeadRecords().add(rec1);

            // Create Second Lead Record
            LeadRecord rec2 = new LeadRecord();

            JAXBElement<String> email2 = objectFactory.createLeadRecordEmail("myemail@test.com");
            rec2.setEmail(email2);

            Attribute attr11 = new Attribute();
            attr11.setAttrName("FirstName");
            attr11.setAttrValue("Nancy");

            Attribute attr21 = new Attribute();
            attr21.setAttrName("LastName");
            attr21.setAttrValue("Lady");

            ArrayOfAttribute aoa2 = new ArrayOfAttribute();
            aoa2.getAttributes().add(attr11);
            aoa2.getAttributes().add(attr21);

            qname = new QName("http://www.marketo.com/mktows/", "leadAttributeList");
            JAXBElement<ArrayOfAttribute> attrList2 = new JAXBElement(qname, ArrayOfAttribute.class, aoa2);

            rec2.setLeadAttributeList(attrList);
            arrayOfLeadRecords.getLeadRecords().add(rec2);

            request.setLeadRecordList(arrayOfLeadRecords);

            SuccessSyncMultipleLeads result = port.syncMultipleLeads(request, header);

            JAXBContext context = JAXBContext.newInstance(SuccessSyncMultipleLeads.class);
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
    :lead_record_list => {
           :lead_record => {
               :email => "em1000@etestd.marketo.net",
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
               :email => "em1001@etestd.marketo.net",
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
