---
title: getCustomObjects
feature: SOAP, Custom Objects
description: getCustomObjects SOAP调用
exl-id: 32ff208a-f824-4420-a26f-1fd969a2bc4c
source-git-commit: 981ed9b254f277d647a844803d05a1a2549cbaed
workflow-type: tm+mt
source-wordcount: '156'
ht-degree: 5%

---

# getCustomObjects

使用由零或一个自定义对象键组成的条件组合检索一个或多个自定义对象。

返回匹配自定义对象的列表，这些对象全部为单一类型，一个批次中最多有100个，以及用于检索连续批次的[流位置](stream-position.md)令牌。

## 请求

| 字段名称 | 必需/可选 | 描述 |
| --- | --- | --- |
| objTypeName | 必需 | 自定义对象的名称 |
| customObjKeyList->keyList->属性 | 必需 | 属性是用于标识要检索的自定义对象的键/值对。 您可以在`customObjKeyLists`中指定多个属性 |
| includeAttributes | 必需 | 要检索的自定义对象的字段列表。 传递none将返回所有值。 |
| batchSize | 可选 | 要返回的对象的数量（最大为100） |
| streamPosition | 可选 | 用于在多个结果集中分页。 传递的值是上一个`getCustomObjects`调用返回的值。 |

## 请求XML

```xml
<?xml version="1.0" encoding="UTF-8"?>
<SOAP-ENV:Envelope xmlns:SOAP-ENV="http://schemas.xmlsoap.org/soap/envelope/" xmlns:ns1="http://www.marketo.com/mktows/">
  <SOAP-ENV:Header>
    <ns1:AuthenticationHeader>
      <mktowsUserId>demo17_1_809934544BFABAE58E5D27</mktowsUserId>
      <requestSignature>974ad482f5625657d82f38ee1522b69f0d6bc715</requestSignature>
      <requestTimestamp>2013-08-20T11:18:12-07:00</requestTimestamp>
    </ns1:AuthenticationHeader>
  </SOAP-ENV:Header>
  <SOAP-ENV:Body>
    <ns1:paramsGetCustomObjects>
      <objTypeName>RoadShow</objTypeName>
      <customObjKeyList>
        <attribute>
          <attrName>MKTOID</attrName>
          <attrValue>1090177</attrValue>
        </attribute>
      </customObjKeyList>
      <includeAttributes />
    </ns1:paramsGetCustomObjects>
  </SOAP-ENV:Body>
</SOAP-ENV:Envelope>
```

## 响应XML

```xml
<?xml version="1.0" encoding="UTF-8"?>
<SOAP-ENV:Envelope xmlns:SOAP-ENV="http://schemas.xmlsoap.org/soap/envelope/" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:ns1="http://www.marketo.com/mktows/">
  <SOAP-ENV:Body>
    <ns1:successGetCustomObjects>
      <result>
        <objTypeName>Roadshow</objTypeName>
        <returnCount>6</returnCount>
        <remainingCount>0</remainingCount>
        <newStreamPosition>{"ky":["1090177"],"os":6}</newStreamPosition>
        <customObjList>
          <customObj>
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
            <customObjAttributeList>
              <attribute>
                <attrName>city</attrName>
                <attrType xsi:nil="true" />
                <attrValue>city4</attrValue>
              </attribute>
              <attribute>
                <attrName>state</attrName>
                <attrType xsi:nil="true" />
                <attrValue>state4</attrValue>
              </attribute>
              <attribute>
                <attrName>zip</attrName>
                <attrType xsi:nil="true" />
                <attrValue>zip4</attrValue>
              </attribute>
            </customObjAttributeList>
          </customObj>
          <customObj>
            <customObjKeyList>
              <attribute>
                <attrName>MKTOID</attrName>
                <attrType xsi:nil="true" />
                <attrValue>1090177</attrValue>
              </attribute>
              <attribute>
                <attrName>rid</attrName>
                <attrType xsi:nil="true" />
                <attrValue>rid0</attrValue>
              </attribute>
            </customObjKeyList>
            <customObjAttributeList>
              <attribute>
                <attrName>city</attrName>
                <attrType xsi:nil="true" />
                <attrValue>city0</attrValue>
              </attribute>
              <attribute>
                <attrName>state</attrName>
                <attrType xsi:nil="true" />
                <attrValue>state0</attrValue>
              </attribute>
              <attribute>
                <attrName>zip</attrName>
                <attrType xsi:nil="true" />
                <attrValue>zip0</attrValue>
              </attribute>
            </customObjAttributeList>
          </customObj>
          <customObj>
            <customObjKeyList>
              <attribute>
                <attrName>MKTOID</attrName>
                <attrType xsi:nil="true" />
                <attrValue>1090177</attrValue>
              </attribute>
              <attribute>
                <attrName>rid</attrName>
                <attrType xsi:nil="true" />
                <attrValue>rid1</attrValue>
              </attribute>
            </customObjKeyList>
            <customObjAttributeList>
              <attribute>
                <attrName>city</attrName>
                <attrType xsi:nil="true" />
                <attrValue>SanMateo</attrValue>
              </attribute>
              <attribute>
                <attrName>state</attrName>
                <attrType xsi:nil="true" />
                <attrValue>California</attrValue>
              </attribute>
              <attribute>
                <attrName>zip</attrName>
                <attrType xsi:nil="true" />
                <attrValue>94404</attrValue>
              </attribute>
            </customObjAttributeList>
          </customObj>
          <customObj>
            <customObjKeyList>
              <attribute>
                <attrName>MKTOID</attrName>
                <attrType xsi:nil="true" />
                <attrValue>1090177</attrValue>
              </attribute>
              <attribute>
                <attrName>rid</attrName>
                <attrType xsi:nil="true" />
                <attrValue>rid2</attrValue>
              </attribute>
            </customObjKeyList>
            <customObjAttributeList>
              <attribute>
                <attrName>city</attrName>
                <attrType xsi:nil="true" />
                <attrValue>city2</attrValue>
              </attribute>
              <attribute>
                <attrName>state</attrName>
                <attrType xsi:nil="true" />
                <attrValue>state2</attrValue>
              </attribute>
              <attribute>
                <attrName>zip</attrName>
                <attrType xsi:nil="true" />
                <attrValue>zip2</attrValue>
              </attribute>
            </customObjAttributeList>
          </customObj>
          <customObj>
            <customObjKeyList>
              <attribute>
                <attrName>MKTOID</attrName>
                <attrType xsi:nil="true" />
                <attrValue>1090177</attrValue>
              </attribute>
              <attribute>
                <attrName>rid</attrName>
                <attrType xsi:nil="true" />
                <attrValue>rid3</attrValue>
              </attribute>
            </customObjKeyList>
            <customObjAttributeList>
              <attribute>
                <attrName>city</attrName>
                <attrType xsi:nil="true" />
                <attrValue>city3</attrValue>
              </attribute>
              <attribute>
                <attrName>state</attrName>
                <attrType xsi:nil="true" />
                <attrValue>state3</attrValue>
              </attribute>
              <attribute>
                <attrName>zip</attrName>
                <attrType xsi:nil="true" />
                <attrValue>zip3</attrValue>
              </attribute>
            </customObjAttributeList>
          </customObj>
          <customObj>
            <customObjKeyList>
              <attribute>
                <attrName>MKTOID</attrName>
                <attrType xsi:nil="true" />
                <attrValue>1090177</attrValue>
              </attribute>
              <attribute>
                <attrName>rid</attrName>
                <attrType xsi:nil="true" />
                <attrValue>rid4</attrValue>
              </attribute>
            </customObjKeyList>
            <customObjAttributeList>
              <attribute>
                <attrName>city</attrName>
                <attrType xsi:nil="true" />
                <attrValue>city4</attrValue>
              </attribute>
              <attribute>
                <attrName>state</attrName>
                <attrType xsi:nil="true" />
                <attrValue>state4</attrValue>
              </attribute>
              <attribute>
                <attrName>zip</attrName>
                <attrType xsi:nil="true" />
                <attrValue>zip4</attrValue>
              </attribute>
            </customObjAttributeList>
          </customObj>
        </customObjList>
      </result>
    </ns1:successGetCustomObjects>
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
$options = array("connection_timeout" => 15, "location" => $marketoSoapEndPoint);
if ($debug) {
  $options["trace"] = 1;
}
// Create Request
$keyAttrib1 = new stdClass();
$keyAttrib1->attrName = 'MKTOID';
$keyAttrib1->attrValue = '1090177';
$keyAttrList = array($keyAttrib1);
$keyList = new stdClass();
$keyList->attribute = $keyAttrList;
$params = new stdClass();
$params->objTypeName = 'RoadShow';
$params->customObjKeyList = $keyList;
$params->includeAttributes = null;
$soapClient = new SoapClient($marketoSoapEndPoint ."?WSDL", $options);
try {
  $leads = $soapClient->__soapCall('getCustomObjects', array($params), $options, $authHdr);
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


public class GetCustomObjects {
    public static void main(String[] args) {
        System.out.println("Executing Get Custom Objects");
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
            ParamsGetCustomObjects request = new ParamsGetCustomObjects();
            request.setObjTypeName("RoadShow");

            ArrayOfAttribute arrayOfAttribute = new ArrayOfAttribute();

            Attribute attr = new Attribute();
            attr.setAttrName("MKTOID");
            attr.setAttrValue("1090177");
            arrayOfAttribute.getAttributes().add(attr);

            JAXBElement<ArrayOfAttribute> attributes = new ObjectFactory().createParamsGetCustomObjectsCustomObjKeyList(arrayOfAttribute);
            request.setCustomObjKeyList(attributes);

            SuccessGetCustomObjects result = port.getCustomObjects(request, header);
            JAXBContext context = JAXBContext.newInstance(SuccessGetCustomObjects.class);
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

client = Savon.client(wsdl: 'http://app.marketo.com/soap/mktows/2_3?WSDL', soap_header: headers, endpoint: marketoSoapEndPoint, namespaces: namespaces, open_timeout: 90, read_timeout: 90, namespace_identifier: :ns1, env_namespace: 'SOAP-ENV')

#Create Request
request = {
    :obj_type_name => "RoadShow",
    :custom_obj_key_list => {
        :attribute => {
            :attr_name => "MKTOID",
            :attr_value => "1090177" }
    },
    :"include_attributes/" => ""
}

response = client.call(:get_custom_objects, message: request)

puts response
```
