---
title: React Native
feature: Mobile Marketing
description: 安装适用于Marketo的React Native
exl-id: 462fd32e-91f1-4582-93f2-9efe4d4761ff
source-git-commit: e7cb23c4d578d949553b2b7a6e127d6be54cdf23
workflow-type: tm+mt
source-wordcount: '811'
ht-degree: 0%

---

# React Native

本文介绍了如何安装和设置Marketo的本机SDK，以将您的移动应用程序与我们的平台集成。

## 先决条件

[在Marketo Admin中添加应用程序](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/mobile-marketing/admin/add-a-mobile-app)（获取应用程序密钥和Munchkin ID）。

## SDK集成

### Android SDK集成

**使用Gradle进行设置**

使用最新版本添加Marketo SDK依赖项：在应用程序级别`build.gradle`文件的“依赖项”部分下，添加(包括相应版本的Marketo SDK)

```
implementation 'com.marketo:MarketoSDK:0.x.x'
```

**添加mavencentral存储库**

Marketo SDK在[maven中央存储库](https://mvnrepository.com/)上可用。 要同步这些文件，请将`mavencentral`存储库添加到根`build.gradle`

```
build script {
  repositories {
    google()
    mavencentral()
  }
}
```

然后，将您的项目与Gradle文件同步。

#### iOS SDK集成

在为React Native项目创建桥接之前，务必要在Xcode项目中设置我们的SDK。

**SDK集成 — 使用CocoaPods**

在您的应用程序中使用我们的iOS SDK很容易。 执行以下步骤，使用CocoaPods在应用程序的Xcode项目中设置插件，以便将我们的平台与应用程序集成。

下载[CocoaPods](https://cocoapods.org/) — 作为Ruby gem分发，它是Objective-C和Swift的依赖关系管理器，简化了在您的代码中使用第三方库(如iOS SDK)的过程。

要下载并安装该软件，请在Mac上启动命令行终端，然后对其运行以下命令：

1. 安装CocoaPods。

`$ sudo gem install cocoapods`

1. 打开您的Podfile。 (在ReactNative项目的iOS文件夹中)

`$ open -a Xcode Podfile`

1. 将以下行添加到您的Podfile中。

`$ pod 'Marketo-iOS-SDK'`

1. 保存并关闭Podfile。

1. 下载并安装Marketo iOS SDK。

`$ pod install`

1. 在Xcode中打开工作区。

`$ open App.xcworkspace`

## 本机模块安装说明

有时，React Native应用程序需要访问JavaScript中默认不可用的本机平台API，例如用于访问Apple或Google Pay的本机API。 您可能希望重用一些现有的Objective-C、Swift、Java或C++库，而无需在JavaScript中重新实施它，或编写一些高性能、多线程代码用于图像处理等。

NativeModule系统向JavaScript (JS)公开Java/Objective-C/C++ （本机）类的实例作为JS对象，从而允许您从JS内执行任意本机代码。 虽然我们不期望此功能成为常规开发过程的一部分，但必须具备此功能。 如果React Native未导出JS应用程序所需的本机API，您应该能够自行导出！

React Native bridge用于JSX和本机应用程序层之间的通信。 在本例中，主机应用程序将能够编写可以调用Marketo SDK方法的JSX代码。

### Android

此文件包含可以使用您提供的参数在内部调用Marketo SDK方法的包装器方法。

```
public class RNMarketoModule extends ReactContextBaseJavaModule {

   final Marketo marketoSdk;
   RNMarketoModule(ReactApplicationContext context) {
       super(context);
       marketoSdk = Marketo.getInstance(context);
   }

   @NonNull
   @Override
   public String getName() {
       return "RNMarketoModule";
   }

   @ReactMethod
   public void associateLead(ReadableMap leadData) {

       MarketoLead mLead = new MarketoLead();
       try {
           mLead.setCity(leadData.getString(MarketoLead.KEY_CITY));
           mLead.setFirstName(leadData.getString(MarketoLead.KEY_FIRST_NAME));
           mLead.setLastName(leadData.getString(MarketoLead.KEY_LAST_NAME));
           mLead.setAddress(leadData.getString(MarketoLead.KEY_ADDRESS));
           mLead.setEmail(leadData.getString(MarketoLead.KEY_EMAIL));
           mLead.setBirthDay(leadData.getString(MarketoLead.KEY_BIRTHDAY));
           mLead.setCountry(leadData.getString(MarketoLead.KEY_COUNTRY));
           mLead.setFacebookId(leadData.getString(MarketoLead.KEY_FACEBOOK));
           mLead.setGender(leadData.getString(MarketoLead.KEY_GENDER));
           mLead.setState(leadData.getString(MarketoLead.KEY_STATE));
           mLead.setPostalCode(leadData.getString(MarketoLead.KEY_POSTAL_CODE));
           mLead.setTwitterId(leadData.getString(MarketoLead.KEY_TWITTER));
           marketoSdk.associateLead(mLead);
       }
       catch (MktoException e){
       }
   }
   @ReactMethod
   public void setSecureSignature(ReadableMap readableMap) {
       MarketoConfig.SecureMode secureMode = new MarketoConfig.SecureMode();
       secureMode.setAccessKey(readableMap.getString("accessKey"));
       secureMode.setEmail(readableMap.getString("email"));
       secureMode.setSignature(readableMap.getString("signature"));
       secureMode.setTimestamp(readableMap.getInt("timeStamp"));
       marketoSdk.setSecureSignature(secureMode);
   }
   @ReactMethod
      public void initializeSDK(String frameworkType, String munchkinId, String appSecreteKey){
          marketoSdk.initializeSDK(munchkinId,appSecreteKey,frameworkType);
    }
   

   @ReactMethod
   public void initializeMarketoPush(String projectId){
       marketoSdk.initializeMarketoPush( projectId);
   }

   @ReactMethod
   public void initializeMarketoPush(String projectId, String channelName){
       marketoSdk.initializeMarketoPush( projectId, channelName);
   }

   @ReactMethod
   public void uninitializeMarketoPush(){
       marketoSdk.uninitializeMarketoPush();
   }

   @ReactMethod
   public void reportAction(String action){
       Marketo.reportAction(action, null);
   }

   @ReactMethod
   public void reportAction(String action, ReadableMap readableMap){
       MarketoActionMetaData marketoActionMetaData = new MarketoActionMetaData();
       marketoActionMetaData.setActionDetails(readableMap.getString("setMetric"));
       marketoActionMetaData.setActionMetric(readableMap.getString("setLength"));
       marketoActionMetaData.setActionLength(readableMap.getString("actionDetails"));
       marketoActionMetaData.setActionType(readableMap.getString("actionType"));
       Marketo.reportAction(action, marketoActionMetaData);
   }
}
```

**注册包**

告知react-native了解Marketo包。

```
public class MarketoPluginPackage implements ReactPackage {

   @NonNull
   @Override
   public List createNativeModules(@NonNull ReactApplicationContext reactContext) {
           List modules = new ArrayList<>();

           modules.add(new RNMarketoModule(reactContext));

           return modules;    
   }

   @NonNull
   @Override
   public List createViewManagers(@NonNull ReactApplicationContext reactContext) {
       return Collections.emptyList();
   }
}
```

要完成包注册，请将MarketoPluginPackage添加到Application类的React包列表中：

```
public class MainApplication extends Application implements ReactApplication {
 
  private final ReactNativeHost mReactNativeHost =
      new ReactNativeHost(this) {
        @Override
        public boolean getUseDeveloperSupport() {
          return BuildConfig.DEBUG;
        }
 
        @Override
        protected List getPackages() {
          @SuppressWarnings("UnnecessaryLocalVariable")
          List packages = new PackageList(this).getPackages();
          packages.add(new MarketoPluginPackage());     //Add the Marketo Package here.
          // Packages that cannot be autolinked yet can be added manually here, for example:
          return packages;
        }
}
```

### iOS

在以下指南中，您将创建一个本机模块&#x200B;_RNMarketoModule_，该模块将允许您从JavaScript访问Marketo的API。

要开始配置，请在Xcode的React Native应用程序中打开iOS项目。 您可以在React Native应用程序中在此处找到您的iOS项目。 我们建议使用Xcode来编写您的本机代码。 Xcode是为iOS开发而构建的，使用它有助于快速解决较小的错误，如代码语法。

创建主自定义原生模块标头和实施文件。 创建一个名为`MktoBridge.h`的新文件并在其中添加以下内容：

```
//
//  MktoBridge.h
//
//  Created by Marketo, An Adobe company.
//

#import <Foundation/Foundation.h>
#import <React/RCTBridgeModule.h>

NS_ASSUME_NONNULL_BEGIN

@interface MktoBridge : NSObject 

@end

NS_ASSUME_NONNULL_END
```

在同一文件夹中创建相应的实施文件`MktoBridge.m`，并包含以下内容：

```
//
//  MktoBridge.h
//  Created by Marketo, An Adobe company.
//

#import <Foundation/Foundation.h>
#import <React/RCTBridgeModule.h>

NS_ASSUME_NONNULL_BEGIN

@interface MktoBridge : NSObject <RCTBridgeModule>

@end

NS_ASSUME_NONNULL_END


//
//  MktoBridge.m
//  Created by Marketo, An Adobe company.
//

#import "MktoBridge.h"
#import <MarketoFramework/Marketo.h>
#import <React/RCTBridge.h>
#import "ConstantStringsHeader.h"

@implementation MktoBridge 

RCT_EXPORT_MODULE(RNMarketoModule);
 
+(BOOL)requiresMainQueueSetup{
  return NO;
}
 
RCT_EXPORT_METHOD(initializeSDK:(NSString *) munchkinId SecretKey: (NSString *) secretKey andFrameworkType: (NSString *) frameworkType){
  [[Marketo sharedInstance] initializeWithMunchkinID:munchkinId appSecret:secretKey mobileFrameworkType:frameworkType launchOptions:nil];
}
 
RCT_EXPORT_METHOD(reportAction:(NSString *)actionName withMetaData:(NSDictionary *)metaData){
  MarketoActionMetaData *meta = [[MarketoActionMetaData alloc] init];
  [meta setType:[metaData objectForKey:KEY_ACTION_TYPE]];
  [meta setDetails:[metaData objectForKey:KEY_ACTION_DETAILS]];
  [meta setLength:[metaData valueForKey:KEY_ACTION_LENGTH]];
  [meta setMetric:[metaData valueForKey:KEY_ACTION_METRIC]];
  [[Marketo sharedInstance] reportAction:actionName withMetaData:meta];
}
 
RCT_EXPORT_METHOD(associateLead:(NSDictionary *)leadDetails){
  MarketoLead *lead = [[MarketoLead alloc] init];
  if ([leadDetails objectForKey:KEY_EMAIL] != nil) {
    [lead setEmail:[leadDetails objectForKey:KEY_EMAIL]];
  }
  if ([leadDetails objectForKey:KEY_FIRST_NAME] != nil) {
    [lead setFirstName:[leadDetails objectForKey:KEY_FIRST_NAME]];
  }
  
  if ([leadDetails objectForKey:KEY_LAST_NAME] != nil) {
    [lead setLastName:[leadDetails objectForKey:KEY_LAST_NAME]];
  }
  
  if ([leadDetails objectForKey:KEY_CITY] != nil) {
    [lead setCity:[leadDetails objectForKey:KEY_CITY]];
  }
    [[Marketo sharedInstance] associateLead:lead];
}
 
RCT_EXPORT_METHOD(uninitializeMarketoPush){
  [[Marketo sharedInstance] unregisterPushDeviceToken];
}
 
RCT_EXPORT_METHOD(reportAll){
  [[Marketo sharedInstance] reportAll];
}
 
RCT_EXPORT_METHOD(setSecureSignature:(NSDictionary *)secureSignature){
  MKTSecuritySignature *secSignature = [[MKTSecuritySignature alloc]
                                        initWithAccessKey:[secureSignature objectForKey:KEY_ACCESSKEY]
                                        signature:[secureSignature objectForKey:KEY_SIGNATURE]
                                        timestamp: [secureSignature objectForKey:KEY_EMAIL]
                                        email:[secureSignature objectForKey:KEY_EMAIL]];
  
    [[Marketo sharedInstance] setSecureSignature:secSignature];
}

RCT_EXPORT_METHOD(requestPermission:(RCTPromiseResolveBlock)resolve rejecter:(RCTPromiseRejectBlock)reject) {
  UNUserNotificationCenter *center = [UNUserNotificationCenter currentNotificationCenter];
  [center requestAuthorizationWithOptions:(UNAuthorizationOptionAlert + UNAuthorizationOptionSound + UNAuthorizationOptionBadge)
                        completionHandler:^(BOOL granted, NSError * _Nullable error) {
    if (error) {
      reject(@"PERMISSION_ERROR", @"Permission request failed", error);
    } else {
      resolve(@(granted));
    }
  }];
}

RCT_EXPORT_METHOD(registerForRemoteNotifications) {
  dispatch_async(dispatch_get_main_queue(), ^{
    [[UIApplication sharedApplication] registerForRemoteNotifications];
  });
}


@end
```

#### 初始化Marketo SDK

在应用程序中查找要向本机模块的createCalendarEvent()方法添加调用的位置。 以下是您可以在应用程序中添加的组件NewModuleButton的示例。 您可以在NewModuleButton的onPress()函数中调用本机模块。

```
import React from 'react';
import { NativeModules, Button } from 'react-native';

const NewModuleButton = () => {
  const onPress = () => {
    console.log('We will invoke the native module here!');
  };

  return (
    
  );
  };

export default NewModuleButton;
```

此JavaScript文件将本机模块加载到JavaScript层。

```javascript
import React from 'react';
import {Node} from 'react';
import { NativeModules } from 'react-native';

const { RNMarketoModule } = NativeModules;
```

正确放置上述文件后，我们可以导入任意js类中的js模块，并直接调用其方法。 例如：

请注意，我们必须将“reactNative”作为React本机应用程序的框架类型传递。 

```
// Initialize marketo SDK with Munchkin & Seretkey you have from step 1.
RNMarketoModule.initializeSDK("MunchkinID","SecreteKEY","FrameworkType")

//You can create a Marketo Lead by calling the associateLead function.
RNMarketoModule.associateLead({ email: "", firstName: "", lastName:"", city:""})

//You can report any user performed action by calling the reportaction function.
RNMarketoModule.reportAction("Bought Shirt", {actionType:"Shopping", actionDetails: "Red Shirt", setLength : 20, setMetric : 30 })

//You can set Secure Signature by calling this method.
RNMarketoModule.setSecureSignature({accessKey: "Key102", email: "testleadrk@001.com", signature : "asdfghjkloiuytds", timeStamp: "12345678987654"})

//This function will Enable user notifications (Only Android)
RNMarketoModule.initializeMarketoPush("350312872033", "MKTO")

//The token can also be unregistered on logout.
RNMarketoModule.uninitializeMarketoPush()
```

#### 配置推送通知

使用项目ID和渠道名称初始化推送

```
RNMarketoModule.initializeMarketoPush("ProjectId", "Channel_name")
```

将以下服务添加到`AndroidManifest.xml`

```xml
<service android:exported="true" android:name=".MyFirebaseMessagingService" android:stopWithTask="true">
    <intent-filter>
        <action  android:name="com.google.firebase.INSTANCE_ID_EVENT"/>
    </intent-filter/>
    <intent-filter> 
        <action android:name="com.google.firebase.MESSAGING_EVENT"/> 
    </intent-filter/>
</activity/>
```

创建名为`FirebaseMessagingService.java`的类并添加以下代码

```java
import com.google.firebase.messaging.FirebaseMessagingService;
import com.google.firebase.messaging.RemoteMessage;
import com.marketo.Marketo;

public class MyFirebaseMessagingService extends FirebaseMessagingService {

   @Override
   public void onNewToken(String token) {
       super.onNewToken(token);
       Marketo marketoSdk = Marketo.getInstance(this.getApplicationContext());
       marketoSdk.setPushNotificationToken(token);
   }

   @Override
   public void onMessageReceived(RemoteMessage remoteMessage) {
       Marketo marketoSdk = Marketo.getInstance(this.getApplicationContext());
       marketoSdk.showPushNotification(remoteMessage);
   }
}
```

必须在Xcode项目中启用权限，才能将推送通知发送到用户的设备。

若要发送推送通知，[添加推送通知](push-notifications.md)。

设置iOS推送通知，
创建PushNotifications.tsx文件并添加以下内容：

```
import { NativeModules } from 'react-native';
const { RNMarketoModule } = NativeModules;

const requestPermission = (): Promise<boolean> => {
return RNMarketoModule.requestPermission()
.then((granted: boolean) => {
console.log('Permission granted:', granted);
return granted;
})
.catch((error: any) => {
console.error('Permission error:', error);
throw error;
});
};

const registerForRemoteNotifications = (): void => {
RNMarketoModule.registerForRemoteNotifications();
};

export { requestPermission, registerForRemoteNotifications };
```


添加`App.tsx`以允许推送通知

```
import React, { useEffect } from 'react';

useEffect(() => {
requestPermission().then(granted => {
if (granted) {
registerForRemoteNotifications();
}
});
}, []);
```

使用APNS委托方法更新`AppDelegate.mm`：

```
#import "AppDelegate.h"
#import "MktoBridge.h"
#import <MarketoFramework/Marketo.h>
#import <React/RCTBundleURLProvider.h>

@implementation AppDelegate

- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
{
  self.moduleName = @"MyNewApp";
  // You can add your custom initial props in the dictionary below.
  // They will be passed down to the ViewController used by React Native.
  self.initialProps = @{};

  return [super application:application didFinishLaunchingWithOptions:launchOptions];
}

- (NSURL *)sourceURLForBridge:(RCTBridge *)bridge
{
  return [self bundleURL];
}

-(void)userNotificationCenter:(UNUserNotificationCenter *)center 
      willPresentNotification:(UNNotification *)notification withCompletionHandler:(void (^)(UNNotificationPresentationOptions options))completionHandler{
    completionHandler(UNAuthorizationOptionSound | UNAuthorizationOptionAlert | UNAuthorizationOptionBadge);
}

- (void)userNotificationCenter:(UNUserNotificationCenter *)center
didReceiveNotificationResponse:(UNNotificationResponse *)response
         withCompletionHandler:(void(^)(void))completionHandler {
    [[Marketo sharedInstance] userNotificationCenter:center 
                      didReceiveNotificationResponse:response
                               withCompletionHandler:completionHandler];
}

- (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken {
    // Register the push token with Marketo
    [[Marketo sharedInstance] registerPushDeviceToken:deviceToken];
}

- (void)applicationWillTerminate:(UIApplication *)application {
    [[Marketo sharedInstance] unregisterPushDeviceToken];
}

-(void)application:(UIApplication *)application didFailToRegisterForRemoteNotificationsWithError:(NSError *)error{
  NSLog(@"Failed to register for remote notification - %@", [error userInfo]);
}

- (NSURL *)bundleURL
{
#if DEBUG
  return [[RCTBundleURLProvider sharedSettings] jsBundleURLForBundleRoot:@"index"];
#else
  return [[NSBundle mainBundle] URLForResource:@"main" withExtension:@"jsbundle"];
#endif
}

@end
```

### 添加测试设备

**Android**

将“MarketoActivity”添加到应用程序标记内的`AndroidManifest.xml`文件中。

```xml
<activity android:name="com.marketo.MarketoActivity" android:configChanges="orientation|screenSize" android:exported="true">
    <intent-filter android:label="MarketoActivity">
        <action  android:name="android.intent.action.VIEW"/>
        <category  android:name="android.intent.category.DEFAULT"/>
        <category  android:name="android.intent.category.BROWSABLE"/>
        <data android:host="add_test_device" android:scheme="mkto"/>
    </intent-filter/>
</activity/>
```

**iOS**

1. 选择项目>目标>信息> URL类型。

1. 添加标识符： ${PRODUCT_NAME}

1. 设置URL方案： `mkto-<S_ecret Key_>`

1. 包含`application:openURL:sourceApplication:annotation:`到`AppDelegate.m`文件(Objective-C)

**iOS — 处理AppDelegate中的自定义Url类型/深层链接** 

```
- (BOOL)application:(UIApplication *)app
            openURL:(NSURL *)url
            options:(NSDictionary *)options{
   
    return [[Marketo sharedInstance] application:app
                                         openURL:url
                                         options:options];    
}
```

从javascript调用API时，将使用这些常量。 您必须创建常量文件并添加以下内容。

```
// Lead attributes.
static NSString *const KEY_FIRST_NAME = @"firstName";
static NSString *const KEY_LAST_NAME = @"lastName";
static NSString *const KEY_ADDRESS = @"address";
static NSString *const KEY_CITY = @"city";
static NSString *const KEY_STATE = @"state";
static NSString *const KEY_COUNTRY = @"country";
static NSString *const KEY_GENDER = @"gender";
static NSString *const KEY_EMAIL = @"email";
static NSString *const KEY_TWITTER = @"twitterId";
static NSString *const KEY_FACEBOOK = @"facebookId";
static NSString *const KEY_LINKEDIN = @"linkedinId";
static NSString *const KEY_LEAD_SOURCE = @"leadSource";
static NSString *const KEY_BIRTHDAY = @"dateOfBirth";
static NSString *const KEY_FACEBOOK_PROFILE_URL = @"facebookProfileURL";
static NSString *const KEY_FACEBOOK_PROFILE_PIC = @"facebookPhotoURL";

// Custom actions
static NSString *const KEY_ACTION_TYPE = @"actionType";
static NSString *const KEY_ACTION_DETAILS = @"actionDetails";
static NSString *const KEY_ACTION_LENGTH = @"setLength";
static NSString *const KEY_ACTION_METRIC = @"setMetric";

//Secure Signature
static NSString *const KEY_ACCESSKEY = @"accessKey";
static NSString *const KEY_SIGNATURE = @"signature";
static NSString *const KEY_TIMESTAMP = @"timeStamp";
```

使用示例

```
//You can create a Marketo Lead by calling the associateLead function.
RNMarketoModule.associateLead({ email: "", firstName: "", lastName:"", city:""})
```
