---
title: deleteCustomObjects
feature: SOAP, Custom Objects
description: deleteCustomObjects SOAP调用。
exl-id: 214b7cd1-dc4e-45dc-a29a-91518a275e61
source-git-commit: 981ed9b254f277d647a844803d05a1a2549cbaed
workflow-type: tm+mt
source-wordcount: '78'
ht-degree: 7%

---

# deleteCustomObjects

删除一个或多个自定义对象并返回操作的结果(DELETED、UNCHANGED、FAILED)。

## 请求

| 字段名称 | 必需/可选 | 描述 |
| --- | --- | --- |
| objTypeName | 必需 | 自定义对象的名称 |
| customObjKeyList->keyList->属性 | 必需 | 属性是用于标识要删除的自定义对象的键/值对。 您可以在customObjKeyLists中指定多个属性 |

## 请求XML

```xml
<?xml version="1.0" encoding="UTF-8"?>
<SOAP-ENV:Envelope xmlns:SOAP-ENV="http://schemas.xmlsoap.org/soap/envelope/" xmlns:ns1="http://www.marketo.com/mktows/">
  <SOAP-ENV:Header>
    <ns1:AuthenticationHeader>
      <mktowsUserId>demo17_1_809934544BFABAE58E5D27</mktowsUserId>
      <requestSignature>a975513bb547bc4a5766a54f6f30e7319eb9811f</requestSignature>
      <requestTimestamp>2013-08-20T11:42:32-07:00</requestTimestamp>
    </ns1:AuthenticationHeader>
  </SOAP-ENV:Header>
  <SOAP-ENV:Body>
    <ns1:paramsDeleteCustomObjects>
      <objTypeName>RoadShow</objTypeName>
      <customObjKeyLists>
        <keyList>
          <attribute>
            <attrName>MKTOID</attrName>
            <attrValue>1090177</attrValue>
          </attribute>
          <attribute>
            <attrName>rid</attrName>
            <attrValue>123456</attrValue>
          </attribute>
        </keyList>
      </customObjKeyLists>
    </ns1:paramsDeleteCustomObjects>
  </SOAP-ENV:Body>
</SOAP-ENV:Envelope>
```

## 响应XML

```xml
<?xml version="1.0" encoding="UTF-8"?>
<SOAP-ENV:Envelope xmlns:SOAP-ENV="http://schemas.xmlsoap.org/soap/envelope/" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:ns1="http://www.marketo.com/mktows/">
  <SOAP-ENV:Body>
    <ns1:successDeleteCustomObjects>
      <result>
        <deleteCustomObjStatusList>
          <syncCustomObjStatus>
            <objTypeName>Roadshow</objTypeName>
            <customObjKeyList>
              <attribute>
                <attrName>MKTOID</attrName>
                <attrType xsi:nil="true" />
                <attrValue>1090177</attrValue>
              </attribute>
              <attribute>
                <attrName>rid</attrName>
                <attrType xsi:nil="true" />
                <attrValue>123456</attrValue>
              </attribute>
            </customObjKeyList>
            <status>DELETED</status>
            <error xsi:nil="true" />
          </syncCustomObjStatus>
        </deleteCustomObjStatusList>
      </result>
    </ns1:successDeleteCustomObjects>
  </SOAP-ENV:Body>
</SOAP-ENV:Envelope>
```

## 示例代码 — PHP

```php
<?php
$debug = true;
$marketoSoapEndPoint     = "";  // CHANGE ME
$marketoUserId           = "";    // CHANGE ME
$marketoSecretKey        = "";    // CHANGE ME
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
$options = array("connection_timeout" => 15, "location" => $marketoSoapEndPoint);
if ($debug) {
  $options["trace"] = 1;
}
// Create Request
$keyAttrib1 = new stdClass();
$keyAttrib1->attrName = 'MKTOID';
$keyAttrib1->attrValue = '1090177';
$keyAttrib2 = new stdClass();
$keyAttrib2->attrName = 'rid';
$keyAttrib2->attrValue = '123456';
$keyAttrList = array($keyAttrib1, $keyAttrib2);
$keyList = new stdClass();
$keyList->attribute = $keyAttrList;
$params = new stdClass();
$params->objTypeName = 'RoadShow';
$params->customObjKeyLists = array($keyList);
$soapClient = new SoapClient($marketoSoapEndPoint ."?WSDL", $options);
try {
  $leads = $soapClient->__soapCall('deleteCustomObjects', array($params), $options, $authHdr);
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
public class DeleteCustomObjects {


    public static void main(String[] args) {
        System.out.println("Executing Delete Custom Objects");
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
            ParamsDeleteCustomObjects request = new ParamsDeleteCustomObjects();
            request.setObjTypeName("RoadShow");

            ArrayOfAttribute arrayOfAttribute = new ArrayOfAttribute();

            Attribute attr = new Attribute();
            attr.setAttrName("MKTOID");
            attr.setAttrValue("1090177");
            arrayOfAttribute.getAttributes().add(attr);

            Attribute attr2 = new Attribute();
            attr2.setAttrName("rid");
            attr2.setAttrValue("123456");
            arrayOfAttribute.getAttributes().add(attr2);

            ArrayOfKeyList keyList = new ArrayOfKeyList();
            keyList.getKeyLists().add(arrayOfAttribute);

            request.setCustomObjKeyLists(keyList);
            SuccessDeleteCustomObjects result = port.deleteCustomObjects(request, header);
            JAXBContext context = JAXBContext.newInstance(SuccessDeleteCustomObjects.class);
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
    :obj_type_name => "RoadShow",
    :custom_obj_key_lists => {
        :key_list => {
            :attribute => {
                  :attr_name => "MKTOID",
                         :attr_value => "1090177" },
          :attribute! => {
                  :attr_name => "rid",
                        :attr_value => "123456" }
        }
    }
}

response = client.call(:delete_custom_objects, message: request)

puts response
```
