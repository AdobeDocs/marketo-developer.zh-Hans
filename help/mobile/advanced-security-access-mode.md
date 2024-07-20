---
title: 高级安全访问模式
feature: Mobile Marketing
description: 有关高级安全访问模式的详细信息
exl-id: bd4730ff-708b-465e-b494-485a4dbf67ff
source-git-commit: 66add4c38d0230c36d57009de985649bb67fde3e
workflow-type: tm+mt
source-wordcount: '303'
ht-degree: 0%

---

# 高级安全访问模式

Marketo SDK公开了用于设置和删除安全签名的方法。 还有一种实用程序方法可检索设备ID。 登录时，设备ID应与电子邮件一起传递给客户服务器，以用于计算安全签名。 SDK应点击指向上面列出的算法的新端点，以检索必要的字段来实例化签名对象。 如果在Marketo Mobile Admin中启用了安全访问模式，则在SDK中设置此签名是必要的步骤。

## 安全访问模式设置

必须实施此设置，然后才能通过Marketo管理员>移动应用程序及设备页面启用安全访问模式。 下面的进一步步骤描述了完成安全验证过程所需的过程：

安全访问模式要求在客户服务器端实施签名算法，该算法将提供端点以检索访问密钥、计算的签名、到期时间戳和电子邮件。 此算法需要用户访问密钥、访问密钥、电子邮件、时间戳和设备ID来执行计算。 客户负责设置端点，实施算法以预执行签名计算，并更新过期时间戳。

```python
import argparse
import datetime
import hashlib
import hmac


ACCESS_KEY = 'Your Access Key'
ACCESS_SECRET = 'Your access secret'

# Key should not be unicode
def get_signing_key(timestamp):
    return 'MKTO' + ACCESS_SECRET + str(timestamp)

def get_string_to_sign(email, uuid):
    return email + uuid

def get_hmac(key, string_to_sign):
    return hmac.new(key, string_to_sign.encode('utf-8'), hashlib.sha256).hexdigest()

def get_epoch_plus_day():
    epoch = datetime.datetime.utcfromtimestamp(0)
    valid_until_dt = datetime.datetime.utcnow() + datetime.timedelta(days=1)
    return long((valid_until_dt - epoch).total_seconds())

if __name__ == '__main__':
    parser = argparse.ArgumentParser()
    parser.add_argument("-e", "--email", required=True, help="email address")
    parser.add_argument("-u", "--uuid", required=True, help="Device install id")
    parser.add_argument("-t", "--timestamp", type=int, help="Valid until timestamp")
    args = parser.parse_args()
    string_to_sign = get_string_to_sign(args.email, args.uuid)
    if not args.timestamp:
        valid_until = get_epoch_plus_day()
    else:
        valid_until = args.timestamp
    signing_key = get_signing_key(valid_until)
    hmac_string = get_hmac(signing_key, string_to_sign)
    print 'HMAC is ', hmac_string
```

Marketo SDK公开了新的方法来设置和删除安全签名。 还有一种实用程序方法可检索设备ID。 登录时，设备ID应与电子邮件一起传递给客户服务器，以用于计算安全签名。 SDK应点击指向上面列出的算法的新端点，以检索必要的字段来实例化签名对象。 如果在Marketo Mobile Admin中启用了安全访问模式，则在SDK中设置此签名是必要的步骤。

### iOS

```
Marketo * sharedInstance =[Marketo sharedInstance];

// set secure signature
MKTSecuritySignature *signature =
[[MKTSecuritySignature alloc] initWithAccessKey:<ACCESS_KEY> signature:<SIGNATURE_TOKEN> timestamp:<EXPIRY_TIMESTAMP> email:<EMAIL>];
[sharedInstance setSecureSignature:signature];

// remove signature
[sharedInstance removeSecureSignature];

// get device id
[sharedInstance getDeviceId];
```

```
let sharedInstance = Marketo.sharedInstance()

 // set secure signature
let signature = MKTSecuritySignature(accessKey: <ACCESS_KEY>, signature: <SIGNATURE_TOKEN> , timestamp: <EXPIRY_TIMESTAMP>, email: <EMAIL>)
sharedInstance.setSecureSignature(signature)

// remove signature
[sharedInstance removeSecureSignature];

// get device id
sharedInstance.getDeviceId()
```

### Android

```
Marketo sdk = Marketo.getInstance(getApplicationContext());

// set signature
MarketoConfig.SecureMode secureMode = new MarketoConfig.SecureMode();
secureMode.setAccessKey(<ACCESS_KEY>);
secureMode.setEmail(<EMAIL_ADDRESS>);
secureMode.setSignature(<SIGNATURE_TOKEN>);
secureMode.setTimestamp(<EXPIRY_DATE>);
if (secureMode.isValid()) {
  sdk.setSecureSignature(secureMode);
}

// remove signature
sdk.removeSecureSignature();

// get device id
sdk.getDeviceId();
```
