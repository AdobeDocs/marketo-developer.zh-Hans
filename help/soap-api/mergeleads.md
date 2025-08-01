---
title: mergeLeads
feature: SOAP
description: mergeLeads SOAP调用
exl-id: a667cb76-525d-4583-b26a-1181d320a68c
source-git-commit: 981ed9b254f277d647a844803d05a1a2549cbaed
workflow-type: tm+mt
source-wordcount: '83'
ht-degree: 8%

---

# mergeLeads

接受获胜商机的密钥和多失败商机的密钥以执行合并操作。 返回潜在客户的ID和状态。

## 请求

| 字段名称 | 必需/可选 | 描述 |
| --- | --- | --- |
| winningLeadKeyList | 必需 | 用于标识入选商机的键。 可能的值为： `IDNUM`、`EMAIL`、`SFDCLEADID`、`LEADOWNEREMAIL`、`SFDCACCOUNTID`、`SFDCCONTACTID`、`SFDCLEADID`、`SFDCLEADOWNERID`、`SFDCOPPTYID` |
| lowingLeadKeyList | 必需 | 用于识别失去的潜在客户的键值列表。 |
| mergeInSales | 可选 | 作为布尔值传递，可以为“True”或“False” |

## 请求XML

```xml
<?xml version="1.0" encoding="UTF-8"?>
<SOAP-ENV:Envelope xmlns:SOAP-ENV="http://schemas.xmlsoap.org/soap/envelope/" xmlns:ns1="http://www.marketo.com/mktows/">
  <SOAP-ENV:Header>
    <ns1:AuthenticationHeader>
      <mktowsUserId>demo17_1_809934544BFABAE58E5D27</mktowsUserId>
      <requestSignature>cdea055214cc726b651dc44ef694e1db4babfcb1</requestSignature>
      <requestTimestamp>2013-08-20T13:32:02-07:00</requestTimestamp>
    </ns1:AuthenticationHeader>
  </SOAP-ENV:Header>
  <SOAP-ENV:Body>
    <ns1:paramsMergeLeads>
      <winningLeadKeyList>
        <attribute>
          <attrName>IDNUM</attrName>
          <attrValue>2</attrValue>
        </attribute>
      </winningLeadKeyList>
      <losingLeadKeyLists>
        <keyList>
          <attribute>
            <attrName>IDNUM</attrName>
            <attrValue>15</attrValue>
          </attribute>
        </keyList>
        <keyList>
          <attribute>
            <attrName>IDNUM</attrName>
            <attrValue>16</attrValue>
          </attribute>
        </keyList>
      </losingLeadKeyLists>
    </ns1:paramsMergeLeads>
  </SOAP-ENV:Body>
</SOAP-ENV:Envelope>
```

## 响应XML

```xml
<?xml version="1.0" encoding="UTF-8"?>
<SOAP-ENV:Envelope xmlns:SOAP-ENV="http://schemas.xmlsoap.org/soap/envelope/" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:ns1="http://www.marketo.com/mktows/">
  <SOAP-ENV:Body>
    <ns1:successMergeLeads>
      <result>
        <mergeStatus>
          <winningLeadId>2</winningLeadId>
          <losingLeadIdList>
            <integerItem>15</integerItem>
            <integerItem>16</integerItem>
          </losingLeadIdList>
          <status>MERGED</status>
          <error xsi:nil="true" />
        </mergeStatus>
      </result>
    </ns1:successMergeLeads>
  </SOAP-ENV:Body>
</SOAP-ENV:Envelope>
```

## 示例代码 — PHP

```php
<?php
$debug = true;
$marketoSoapEndPoint    = "";  // CHANGE ME
$marketoUserId      = "";  // CHANGE ME
$marketoSecretKey   = ""; // CHANGE ME
$marketoNameSpace   = "http://www.marketo.com/mktows/";

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
$keyAttrib1 = new stdClass();
$keyAttrib1->attrName = 'IDNUM';
$keyAttrib1->attrValue = '2';
$winningKeyList = new stdClass();
$winningKeyList->attribute = array($keyAttrib1);
$loser1 = new stdClass();
$loser1->attrName = "IDNUM";
$loser1->attrValue = "15";
$loser2 = new stdClass();
$loser2->attrName = "IDNUM";
$loser2->attrValue = "16";
$keyList1 = new stdClass();
$keyList1->attribute = array($loser1);
$keyList2 = new stdClass();
$keyList2->attribute = array($loser2);
$params = new stdClass();
$params->winningLeadKeyList = $winningKeyList;
$params->losingLeadKeyLists = array($keyList1, $keyList2);
$soapClient = new SoapClient($marketoSoapEndPoint ."?WSDL", $options);
try {
  $leads = $soapClient->__soapCall('mergeLeads', array($params), $options, $authHdr);
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
import javax.xml.bind.Marshaller;

public class MergeLeads {

    public static void main(String[] args) {
        System.out.println("Executing Merge Lead");
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
            ParamsMergeLeads request = new ParamsMergeLeads();

            ArrayOfAttribute winningLeadArray = new ArrayOfAttribute();

            Attribute winner = new Attribute();
            winner.setAttrName("IDNUM");
            winner.setAttrValue("2");
            winningLeadArray.getAttributes().add(winner);
            request.setWinningLeadKeyList(winningLeadArray);

            ArrayOfAttribute losingLeadArray = new ArrayOfAttribute();

            Attribute loser = new Attribute();
            loser.setAttrName("IDNUM");
            loser.setAttrValue("15");
            losingLeadArray.getAttributes().add(loser);

            ArrayOfAttribute losingLeadArray2 = new ArrayOfAttribute();
            Attribute loser2 = new Attribute();
            loser2.setAttrName("IDNUM");
            loser2.setAttrValue("16");
            losingLeadArray2.getAttributes().add(loser2);

            ArrayOfKeyList losingKeyList = new ArrayOfKeyList();
            losingKeyList.getKeyLists().add(losingLeadArray);
            losingKeyList.getKeyLists().add(losingLeadArray2);
            request.setLosingLeadKeyLists(losingKeyList);

            SuccessMergeLeads result = port.mergeLeads(request, header);

            JAXBContext context = JAXBContext.newInstance(SuccessMergeLeads.class);
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

mktows                                                = "" # CHANGE ME
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
    :winning_lead_key_list => {
          :attribute => {
            :attr_name => "IDNUM",
            :attr_value => "2" }
    },
    :losing_lead_key_lists => {
        :key_list => {
            :attribute => {
                :attr_name => "IDNUM",
                :attr_value => "15" },
            :attribute! => {
                :attr_name => "IDNUM",
                :attr_value => "16" }
        }
    }
}

response = client.call(:merge_leads, message: request)

puts response
```
