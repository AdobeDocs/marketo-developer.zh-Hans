---
title: syncMObjects
feature: SOAP
description: syncMObjects SOAP调用
exl-id: 68bb69ce-aa8c-40b7-8938-247f4fe97b5d
source-git-commit: 66add4c38d0230c36d57009de985649bb67fde3e
workflow-type: tm+mt
source-wordcount: '421'
ht-degree: 3%

---

# syncMObjects

接受要创建或更新的[MObjects](marketo-objects.md)的数组，每个调用最多为100个，并返回操作(CREATED、UPDATED、FAILED、UNCHANGED、SKIPPED)的结果（状态）和MObject的Marketo ID。 API可通过以下三种操作模式之一进行调用：

1. 插入 — 仅插入新对象，跳过现有对象
1. UPDATE — 仅更新现有对象，跳过新对象。
   - 对于程序对象，只能在UPDATE模式下调用API以添加新的期间成本信息或添加/更新现有程序的标记/渠道。 （标签/渠道必须已定义：无法通过API创建新标签/渠道）
1. UPSERT — 插入新对象并更新现有对象

UPDATE和UPSERT操作使用ID作为密钥。 在单个API调用中，某些更新可能会成功，而某些更新可能会失败。 每次失败都将返回一条错误消息

## 请求

| 字段名称 | 必需/可选 | 描述 |
| --- | --- | --- |
| mObjectList->mObject->type | 必需 | 可以是：`Program`、`Opportunity`、`OpportunityPersonRole`之一 |
| mObjectList->mObject->id | 必需 | MObject的ID。 每个调用最多可以指定100个MObjects。 |
| mObjectList->mObject->typeAttribList->typeAttrib->attrType | 必需 | 成本（仅在更新项目群时使用）可以是： `Cost`、`Tag`之一 |
| mObjectList->mObject->typeAttribList->typeAttrib->attrList->attrib->name | 必需 | 对于程序MObject，以下属性可作为名称 — 值对传递。 对于成本： `Month (Required)`、`Amount (Required)`、`Id (Cost Id - Optional)`、`Note (Optional)`。 对于标记/渠道： `Type (Required)`，`Value (Required)`。 对于Opportunity MObject，[describeMObject](describemobject.md)输出中的所有字段都可以作为名称 — 值对传递。 以下列表是所有可选字段和标准属性集。 您在Opportunity对象中可能有通过支持请求创建的其他字段。 |

1. 数量
1. 关闭日期
1. 描述
1. ExpectedRevenue
1. ExternalCreatedDate
1. 会计
1. FiscalQuarter
1. Ficationyear
1. 预测类别名称
1. IsClosed
1. IsWon
1. LastActivityDate
1. 商机来源
1. 名称
1. 下一步
1. 概率
1. 数量
1. 阶段
1. 类型

对于OpportunityPersonRole MObject，您可以提供[describeMObject](./describemobject.md)输出中的所有字段作为名称 — 值对。 此处列出了OpportunityPersonRole对象上的标准属性集：

1. OpportunityId（必需）
1. PersonId（必需）
   1. 与Marketo中的人员/联系人ID相对应。
1. 角色（可选）
1. ExternalCreatedDate（可选）
1. IsPrimary（可选）
1. 角色（可选）

|
| mObjAssociationList->mObjAssociation->mObjType | 可选 | 用于使用关联对象的ID或外部键更新Opportunity/OpportunityPersonRole对象。 关联的对象可以是：公司（用于更新Opportunity对象）、潜在客户（用于更新OpportunityPersonRole对象）、Opportunity（用于更新OpportunityPersonRole对象）|
| mObjAssociationList->mObjAssociation->id | 可选 | 关联对象（潜在客户/公司/机会）的ID |
| mObjAssociationList->mObjAssociation->externalKey | 可选 | 关联对象的自定义属性 |

## 请求XML

```xml
<?xml version="1.0" encoding="UTF-8"?>
<SOAP-ENV:Envelope xmlns:SOAP-ENV="http://schemas.xmlsoap.org/soap/envelope/" xmlns:ns1="http://www.marketo.com/mktows/">
  <SOAP-ENV:Header>
    <ns1:AuthenticationHeader>
      <mktowsUserId>demo17_1_809934544BFABAE58E5D27</mktowsUserId>
      <requestSignature>40e6d89bd2f7f0daaddaf150ce7a7ca49788ffe7</requestSignature>
      <requestTimestamp>2013-08-05T14:22:45-07:00</requestTimestamp>
    </ns1:AuthenticationHeader>
  </SOAP-ENV:Header>
  <SOAP-ENV:Body>
    <ns1:paramsSyncMObjects>
      <mObjectList>
        <mObject>
          <type>Program</type>
          <id>1970</id>
          <typeAttribList>
            <typeAttrib>
              <attrType>Cost</attrType>
              <attrList>
                <attrib>
                  <name>Month</name>
                  <value>2013-06</value>
                </attrib>
                <attrib>
                  <name>Amount</name>
                  <value>2000</value>
                </attrib>
                <attrib>
                  <name>Id</name>
                  <value>153</value>
                </attrib>
              </attrList>
            </typeAttrib>
          </typeAttribList>
        </mObject>
      </mObjectList>
      <operation>UPDATE</operation>
    </ns1:paramsSyncMObjects>
  </SOAP-ENV:Body>
</SOAP-ENV:Envelope>
```

## 响应XML

```xml
<?xml version="1.0" encoding="UTF-8"?>
<SOAP-ENV:Envelope xmlns:SOAP-ENV="http://schemas.xmlsoap.org/soap/envelope/" xmlns:ns1="http://www.marketo.com/mktows/">
  <SOAP-ENV:Body>
    <ns1:successSyncMObjects>
      <result>
        <mObjStatusList>
          <mObjStatus>
            <id>1970</id>
            <status>UPDATED</status>
          </mObjStatus>
        </mObjStatusList>
      </result>
    </ns1:successSyncMObjects>
  </SOAP-ENV:Body>
</SOAP-ENV:Envelope>
```

## 示例代码 — PHP

```php
<?php
$debug = true;
$marketoSoapEndPoint    = "";  // CHANGE ME
$marketoUserId      = ""; // CHANGE ME
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
////////////////////////////////
$params = prepareUpdateProgramRequest();
// -or- 
//$params = prepareCreateOpptyRequest();
// -or-
//$params= prepareCreateOpptyPersonRoleRequest();
////////////////////////////////
            
$soapClient = new SoapClient($marketoSoapEndPoint ."?WSDL", $options);
try {
  $leads = $soapClient->__soapCall('syncMObjects', array($params), $options, $authHdr);
}
catch(Exception $ex) {
  var_dump($ex);
}

if ($debug) {
  print "RAW request:\n" .$soapClient->__getLastRequest() ."\n";
  print "RAW response:\n" .$soapClient->__getLastResponse() ."\n";
}

function prepareUpdateProgramRequest() {
  $params = new stdClass();

  $mObj = new stdClass();
  $mObj->type = 'Program';
  $mObj->id="1970";

  $attrib1 = new stdClass();
  $attrib1->name="Month";
  $attrib1->value="2013-06";
        
  $attrib2 = new stdClass();
  $attrib2->name="Amount";
  $attrib2->value="2000";
        
  $attrib3 = new stdClass();
  $attrib3->name="Id";
  $attrib3->value="153";
  $attribList = array ($attrib1, $attrib2, $attrib3);
        
  $costAttrib = new stdClass();
  $costAttrib->attrType="Cost";
  $costAttrib->attrList = $attribList;

  $mObj->typeAttribList= array($costAttrib);
  $params->mObjectList = array($mObj);

  $params->operation="UPDATE";

  return $params;
}

function prepareCreateOpptyRequest() {
  $params = new stdClass();

  $mObj = new stdClass();
  $mObj->type = 'Opportunity';

  $attrib1 = new stdClass();
  $attrib1->name="Name";
  $attrib1->value="Q1 2014";
        
  $attrib2 = new stdClass();
  $attrib2->name="Amount";
  $attrib2->value="2000";
        
  $attrib3 = new stdClass();
  $attrib3->name="Probability";
  $attrib3->value="80%";
  $attribs = array ($attrib1, $attrib2, $attrib3);

  $attribList = new stdClass();
  $attribList->attrib = $attribs;

  $mObj->attribList = $attribList;
  $params->mObjectList = array($mObj);

  $params->operation="INSERT";
  
  return $params;
}

function prepareCreateOpptyPersonRoleRequest() {
  $params = new stdClass();

  $mObj = new stdClass();
  $mObj->type = 'OpportunityPersonRole';

  $attrib1 = new stdClass();
  $attrib1->name="OpportunityId";
  $attrib1->value="64";
        
  $attrib2 = new stdClass();
  $attrib2->name="PersonId";
  $attrib2->value="19";
        
  $attrib3 = new stdClass();
  $attrib3->name="Role";
  $attrib3->value="Influencer/Champion";
  $attribs = array ($attrib1, $attrib2, $attrib3);

  $attribList = new stdClass();
  $attribList->attrib = $attribs;

  $mObj->attribList = $attribList;
  $params->mObjectList = array($mObj);

  $params->operation="INSERT";
  return $params;
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
 
 
public class SyncMObjects {
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
            ////////////////////////////////
            ParamsSyncMObjects request = prepareUpdateProgramRequest();
            // -or- 
            //ParamsSyncMObjects request = prepareCreateOpptyRequest();
            // -or-
            //ParamsSyncMObjects request = prepareCreateOpptyPersonRoleRequest();
            ////////////////////////////////
             
            SuccessSyncMObjects result = port.syncMObjects(request, header);
            
            JAXBContext context = JAXBContext.newInstance(SuccessSyncMObjects.class);
            Marshaller m = context.createMarshaller();
            m.setProperty(Marshaller.JAXB_FORMATTED_OUTPUT, true);
            m.marshal(result, System.out);
            
        }
        catch (Exception e) {
            e.printStackTrace();
        }
    }
    
    private static ParamsSyncMObjects prepareUpdateProgramRequest() {
        ParamsSyncMObjects request = new ParamsSyncMObjects();
        request.setOperation(SyncOperationEnum.UPDATE);

        MObject mobj = new MObject();
        mobj.setType("Program");
        mobj.setId(1970);
        
        TypeAttrib typeAttrib = new TypeAttrib();
        typeAttrib.setAttrType("Cost");
        
        Attrib attrib = new Attrib();
        attrib.setName("Month");
        attrib.setValue("2013-06");
        
        Attrib attrib2 = new Attrib();
        attrib1.setName("Amount");
        attrib1.setValue("2000");
        
        Attrib attrib3 = new Attrib();
        attrib1.setName("Id");
        attrib1.setValue("153");
        
        ArrayOfAttrib attribList = new ArrayOfAttrib();
        attribList.getAttribs().add(attrib);
        attribList.getAttribs().add(attrib2);
        attribList.getAttribs().add(attrib3);
        typeAttrib.setAttrList(attribList);
        
        ArrayOfTypeAttrib typeAttribList = new ArrayOfTypeAttrib();
        typeAttribList.getTypeAttribs().add(typeAttrib);
        mobj.setTypeAttribList(typeAttribList);
        
        ArrayOfMObject objList = new ArrayOfMObject();
        objList.getMObjects().add(mobj);
        request.setMObjectList(objList);
        
        return request;
    }

    private static ParamsSyncMObjects prepareCreateOpptyRequest() {
        ParamsSyncMObjects request = new ParamsSyncMObjects();
        request.setOperation(SyncOperationEnum.INSERT);
        
        MObject mobj = new MObject();
        mobj.setType("Opportunity");
        
        Attrib attrib = new Attrib();
        attrib.setName("Name");
        attrib.setValue("Q1 2014");

        Attrib attrib2 = new Attrib();        
        attrib1.setName("Amount");
        attrib1.setValue("2000");
        
        Attrib attrib3 = new Attrib();
        attrib1.setName("Probability");
        attrib1.setValue("80%");
        
        ArrayOfAttrib attribList = new ArrayOfAttrib();
        attribList.getAttribs().add(attrib);
        attribList.getAttribs().add(attrib2);
        attribList.getAttribs().add(attrib3);
        mobj.setAttribList(attribList);
        
        ArrayOfMObject objList = new ArrayOfMObject();
        objList.getMObjects().add(mobj);
        request.setMObjectList(objList);
        
        return request;
    }
    private static ParamsSyncMObjects prepareCreateOpptyPersonRoleRequest() {
        ParamsSyncMObjects request = new ParamsSyncMObjects();
        request.setOperation(SyncOperationEnum.INSERT);
        
        MObject mobj = new MObject();
        mobj.setType("OpportunityPersonRole");
        
        Attrib attrib = new Attrib();
        attrib.setName("OpportunityId");
        attrib.setValue("64"); // Id of the opportunity created earlier
        
        Attrib attrib2 = new Attrib();
        attrib1.setName("PersonId");
        attrib1.setValue("19");
        
        Attrib attrib3 = new Attrib();
        attrib1.setName("Role");
        attrib1.setValue("Influencer/Champion");
        
        ArrayOfAttrib attribList = new ArrayOfAttrib();
        attribList.getAttribs().add(attrib);
        attribList.getAttribs().add(attrib2);
        attribList.getAttribs().add(attrib3);
        mobj.setAttribList(attribList);
        
        ArrayOfMObject objList = new ArrayOfMObject();
        objList.getMObjects().add(mobj);
        request.setMObjectList(objList);
        
        return request;
    }
            
}
```

## 示例代码 — Ruby

```ruby
require 'savon' # Use version 1.0 Savon gem
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
    :m_object_list => {
          :m_object => {
              :type => "Program",
              :id => "1970",
              :type_attrib_list => {
                  :type_attrib => {
                      :attr_type => "Cost",
                      :attr_list => {
                          :attrib => {
                              :name => "Month",
                              :value => "2013-06" },
                        :attrib! => {
                              :name => "Amount",
                              :value =>  "2000" },
                        :attrib! => {
                              :name => "Id",
                              :value => "153" }
                    }
                }
            }
        }
    },
      :operation => "UPDATE"
}

response = client.call(:sync_m_objects, message: request)

puts response
```
