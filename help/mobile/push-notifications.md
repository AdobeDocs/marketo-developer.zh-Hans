---
title: 推送通知
feature: Mobile Marketing
description: 指南以通过Marketo启用iOS推送通知，从APNs证书和Xcode设置到Marketo SDK集成、令牌注册、处理。
exl-id: 41d657d8-9eea-4314-ab24-fd4cb2be7f61
source-git-commit: 7557b9957c87f63c2646be13842ea450035792be
workflow-type: tm+mt
source-wordcount: '1344'
ht-degree: 0%

---

# 推送通知

如何启用推送通知。

## 在iOS中设置推送通知

启用推送通知有三个步骤：

1. 在Apple开发人员帐户上配置推送通知。
1. 在xCode中启用推送通知。
1. 使用Marketo SDK在应用程序中启用推送通知。

### 在Apple开发人员帐户上配置推送通知

1. 登录Apple开发人员[成员中心](http://developer.apple.com/membercenter)。
1. 单击“Certificates， Identifiers &amp; Profiles”（证书、标识符和配置文件）。
1. 单击“iOS， tvOS， watchOS”下的“Certificates->All”文件夹。
1. 选择左上角屏幕上的证书![](assets/certificates-plus.png)旁边的“+”
1. 启用“Apple推送通知服务SSL（沙盒和生产）”复选框，然后单击“继续”。
1. 选择您正在使用生成应用程序的应用程序标识符。![](assets/push-appid.png)
1. 创建并上传CSR以生成推送证书。![](assets/push-ssl.png)
1. 将证书下载到本地计算机并双击进行安装。![](assets/certificate-download.png)
1. 打开“密钥链访问”，右键单击证书，然后将2个项目导出到`.p12`文件中。![key_chain](assets/key-chain.png)
1. 通过Marketo Admin Console上传此文件以配置通知。
1. 更新应用程序设置配置文件。

### 在xCode中启用推送通知

在xCode项目中启用推送通知功能。![](assets/push-xcode.png)

### 使用Marketo SDK在应用程序中启用推送通知

将以下代码添加到`AppDelegate.m`文件以将推送通知交付到客户的设备。

**注意** — 如果使用[!DNL Adobe Launch]扩展，则使用`ALMarketo`作为类名

在`AppDelegate.h`中导入以下内容。

>[!BEGINTABS]

>[!TAB 目标C]

```
#import <UserNotifications/UserNotifications.h>
```

>[!TAB Swift]

```
import UserNotifications
```

>[!ENDTABS]

将`UNUserNotificationCenterDelegate`添加到`AppDelegate`，如下所示。

>[!BEGINTABS]

>[!TAB 目标C]

```
@interface AppDelegate : UIResponder <UIApplicationDelegate, UNUserNotificationCenterDelegate>
```

>[!TAB Swift]

```
class AppDelegate: UIResponder, UIApplicationDelegate , UNUserNotificationCenterDelegate
```

>[!ENDTABS]

启动推送通知服务。 要启用推送通知，请添加以下代码。

>[!BEGINTABS]

>[!TAB 目标C]

```objectivec
BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
UNUserNotificationCenter *center = [UNUserNotificationCenter currentNotificationCenter];
        center.delegate = self;
        [center requestAuthorizationWithOptions:(UNAuthorizationOptionSound | UNAuthorizationOptionAlert | UNAuthorizationOptionBadge) completionHandler:^(BOOL granted, NSError * _Nullable error){
            if(!error){
                dispatch_async(dispatch_get_main_queue(), ^{
                    [[UIApplication sharedApplication] registerForRemoteNotifications];
                });
            }
        }];

    return YES;
}
```

>[!TAB Swift]

```
func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?) -> Bool {

    UNUserNotificationCenter.current().requestAuthorization(options: [.alert, .sound,    .badge]) { granted, error in
            if let error = error {
                print("\(error.localizedDescription)")
            } else {
                DispatchQueue.main.async {
                    application.registerForRemoteNotifications()
                }
            }
        }

        return true
}
```

>[!ENDTABS]

调用此方法可启动Apple推送服务的注册流程。 如果注册成功，应用程序将调用应用程序委托对象的`application:didRegisterForRemoteNotificationsWithDeviceToken:`方法，并向其传递设备令牌。

如果注册失败，应用程序将改为调用其应用程序委托的`application:didFailToRegisterForRemoteNotificationsWithError:`方法。

在Marketo中注册推送令牌。 要从Marketo接收推送通知，您必须在Marketo中注册设备令牌。

>[!BEGINTABS]

>[!TAB 目标C]

```
- (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken {
    // Register the push token with Marketo
    [[Marketo sharedInstance] registerPushDeviceToken:deviceToken];
}
```

>[!TAB Swift]

```
func application(_ application: UIApplication, didRegisterForRemoteNotificationsWithDeviceToken deviceToken: Data) {
    // Register the push token with Marketo
    Marketo.sharedInstance().registerPushDeviceToken(deviceToken)
}
```

>[!ENDTABS]

用户注销时也可以注销令牌。

>[!BEGINTABS]

>[!TAB 目标C]

```
[[Marketo sharedInstance] unregisterPushDeviceToken];
```

>[!TAB Swift]

```
Marketo.sharedInstance().unregisterPushDeviceToken
```

>[!ENDTABS]

要重新注册推送令牌，请将步骤3中的代码提取到AppDelegate方法中，并从ViewController登录方法中调用。

处理推送通知。 要从Marketo接收推送通知，您必须在Marketo中注册设备令牌。

>[!BEGINTABS]

>[!TAB 目标C]

```
- (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo
{
    [[Marketo sharedInstance] handlePushNotification:userInfo];
}
```

>[!TAB Swift]

```
func application(_ application: UIApplication, didReceiveRemoteNotification userInfo: [AnyHashable : Any]) {
    Marketo.sharedInstance().handlePushNotification(userInfo)
}
```

>[!ENDTABS]

在AppDelegate中添加以下方法

使用此方法，您可以在应用程序处于前台时显示警报、声音或增加徽章。 必须在此方法中调用您选择的completionHandler。

>[!BEGINTABS]

>[!TAB 目标C]

```
-(void)userNotificationCenter:(UNUserNotificationCenter *)center
    willPresentNotification:(UNNotification *)notification
        withCompletionHandler:(void (^)(UNNotificationPresentationOptions options))completionHandler{

    completionHandler(UNAuthorizationOptionSound | UNAuthorizationOptionAlert | UNAuthorizationOptionBadge);
}
```

>[!TAB Swift]

```
func userNotificationCenter(_ center: UNUserNotificationCenter,
            willPresent notification: UNNotification, withCompletionHandler completionHandler: @escaping (
    UNNotificationPresentationOptions) -> Void) {
    completionHandler([.alert, .sound,.badge])
}
```

>[!ENDTABS]

在AppDelegate中处理新收到的推送通知

当用户通过打开应用程序、取消通知或选择UNNotificationAction来响应通知时，将在委托上调用方法。 在应用程序从applicationDidFinishLaunching：返回之前，必须设置委托。

>[!BEGINTABS]

>[!TAB 目标C]

```
- (void)userNotificationCenter:(UNUserNotificationCenter *)center
didReceiveNotificationResponse:(UNNotificationResponse *)response withCompletionHandler:(void(^)(void))completionHandler {
    [[Marketo sharedInstance] userNotificationCenter:center didReceiveNotificationResponse:response withCompletionHandler:completionHandler];
}
```

>[!TAB Swift]

```
func userNotificationCenter(_ center: UNUserNotificationCenter,
                                didReceive response: UNNotificationResponse,
                                withCompletionHandler
                                completionHandler: @escaping () -> Void) {
        Marketo.sharedInstance().userNotificationCenter(center, didReceive: response, withCompletionHandler: completionHandler)
}
```

>[!ENDTABS]

跟踪推送通知

如果您的应用程序在后台运行（或未处于活动状态），则设备将收到推送通知，如下所示。 Marketo将跟踪用户点击通知的时间。

![移动设备8](assets/mobile8.png)

如果设备收到推送通知，则会将其传递到应用程序委托的`application:didReceiveRemoteNotification:`回调。

以下是Marketo中的Marketo活动日志，其中显示应用程序事件和推送通知事件。

![移动设备9](assets/mobile9.png)

## 在Android中设置推送通知

1. 在应用程序标记中添加以下权限。

   打开`AndroidManifest.xml`并添加以下权限。 您的应用程序必须请求“INTERNET”和“ACCESS_NETWORK_STATE”权限。 如果您的应用程序已经请求这些权限，请跳过此步骤。

   ```xml
   <uses‐permission android:name="android.permission.INTERNET"/>
   <uses‐permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
   
   <!‐‐Following permissions are required for push notification.‐‐>
   <uses-permission android:name="android.permission.GET_ACCOUNTS"/>
   <!‐‐Keeps the processor from sleeping when a message is received.‐‐>
   <uses-permission android:name="android.permission.WAKE_LOCK"/>
   <permission android:name="<PACKAGE_NAME>.permission.C2D_MESSAGE" android:protectionLevel="signature" />
   <uses-permission android:name="<PACKAGE_NAME>.permission.C2D_MESSAGE" />
   <!-- This app has permission to register and receive data message. -->
   <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
   ```

1. 使用HTTPv1设置FCM(Google在2023年6月12日已[弃用的XMPP协议](https://firebase.google.com/docs/cloud-messaging/xmpp-server-ref)，将于2024年6月删除)

- 在Marketo功能管理器![](assets/feature-manager.png)中启用MME FCM HTTPv1
   - 在MLM中上载应用程序的服务帐户Json文件。
   - 您可以从Firebase控制台下载服务帐户Json文件。   ![](assets/fcm-console.png)
   - 在Marketo中上传服务帐户Json文件后等待一小时，然后再发送推送通知。 

## Android测试设备

在应用程序标记的清单文件中添加Marketo活动。

```xml
<activity android:name="com.marketo.MarketoActivity"  android:configChanges="orientation|screenSize">
    <intent-filter android:label="MarketoActivity">
        <action  android:name="android.intent.action.VIEW"/>
        <category  android:name="android.intent.category.DEFAULT"/>
        <category  android:name="android.intent.category.BROWSABLE"/>
        <data android:host="add_test_device" android:scheme="mkto"/>
    </intent-filter/>
</activity/>
```

## 注册Marketo推送服务

1. 要从Marketo接收推送通知，必须将Firebase消息服务添加到`AndroidManifest.xml`。 在结束应用程序标记之前添加。

   ```xml
   <meta-data
       android:name="com.google.android.gms.version"
       android:value="@integer/google_play_services_version" />
   <service android:name=".MyFirebaseMessagingService">
   <intent-filter>
   <action android:name="com.google.firebase.INSTANCE_ID_EVENT"/>
   <action android:name="com.google.firebase.MESSAGING_EVENT"/>
   </intent-filter>
   </service>
   ```

1. 按如下方式在文件`MyFirebaseMessagingService`中添加Marketo SDK方法

   ```java
   import com.marketo.Marketo;
   
   public class MyFirebaseMessagingService extends FirebaseMessagingService {
   
       @Override
       public void onNewToken(String s) {
           super.onNewToken(s);
           Marketo marketoSdk = Marketo.getInstance(this.getApplicationContext());
           marketoSdk.setPushNotificaitonToken(s);
           // Add your code here...
       }
   
       @Override
       public void onMessageReceived(RemoteMessage remoteMessage) {
           Marketo marketoSdk = Marketo.getInstance(this.getApplicationContext());
           marketoSdk.showPushNotificaiton(remoteMessage);
           // Add your code here...
       }
   
   }
   ```

   **注意** — 如果使用Adobe扩展，请按照以下方式添加

   ```java
   import com.marketo.Marketo;
   
   public class MyFirebaseMessagingService extends FirebaseMessagingService {
   
       @Override
       public void onNewToken(String token) {
           super.onNewToken(token);
           ALMarketo.setPushNotificationToken(token);
           // Add your code here...
       }
   
       @Override
       public void onMessageReceived(RemoteMessage remoteMessage) {
           ALMarketo.showPushNotification(remoteMessage);
           // Add your code here...
       }
   
   }
   ```

**注意**： FCM SDK会自动添加所有必需的权限以及必需的接收方功能。 如果您使用早期版本的SDK，请确保从应用程序清单中删除以下过时（并且可能带来危害，因为它们可能会导致消息重复）元素

```xml
<receiver android:name="com.marketo.MarketoBroadcastReceiver" android:permission="com.google.android.c2dm.permission.SEND">
    <intent-filter>
        <!‐‐Receives the actual messages.‐‐>
        <action android:name="com.google.android.c2dm.intent.RECEIVE"/>
        <!‐‐Register to enable push notification‐‐>
        <action android:name="com.google.android.c2dm.intent.REGISTRATION"/>
        <!‐‐‐Replace YOUR_PACKAGE_NAME with your own package name‐‐>
        <category android:name="YOUR_PACKAGE_NAME"/>
    </intent-filter>
</receiver>

<!‐‐Marketo service to handle push registration and notification‐‐>
<service android:name="com.marketo.MarketoIntentService"/>
```

1. 初始化Marketo推送在保存上述配置后，您必须初始化Marketo推送通知。 创建或打开您的Application类，然后复制/粘贴以下代码。 您可以从Firebase控制台获取发件人ID。

   ```java
   Marketo marketoSdk = Marketo.getInstance(getApplicationContext());
   
   // Enable push notification here. The push notification channel name can by any string
   marketoSdk.initializeMarketoPush(SENDER_ID,"ChannelName");
   ```

   如果使用[!DNL Adobe Launch]扩展，请按照以下说明操作

   ```java
   // Enable push notification here. The push notification channel name can by any string
   ALMarketo.initializeMarketoPush(SENDER_ID,"ChannelName");
   ```

   如果您没有SENDER_ID，请完成[本教程](https://developers.google.com/cloud-messaging/)中详述的步骤以启用Google Cloud Messaging Service。

   用户注销时也可以注销令牌。

   ```java
   marketoSdk.uninitializeMarketoPush();
   ```

   如果使用[!DNL Adobe Launch]扩展，请使用下面的说明

   ```java
   ALMarketo.uninitializeMarketoPush();
   ```

   注意：要重新注册推送令牌，请将步骤3中的代码提取到AppDelegate方法中，并从ViewController登录方法中调用。

1. 设置通知图标（可选）要配置自定义通知图标，应调用以下方法。

   ```java
   MarketoConfig.Notification config = new MarketoConfig.Notification();
   // Optional bitmap for honeycomb and above
   config.setNotificationLargeIcon(bitmap);
   
   // Required icon Resource ID
   config.setNotificationSmallIcon(R.drawable.notification_small_icon);
   
   // Set the configuration
   //Use the static methods on ALMarketo class when using Adobe Extension
   Marketo.getInstance(context).setNotificationConfig(config);
   
   // Get the configuration set
   Marketo.getInstance(context).getNotificationConfig();
   ```

## 故障排除

设置移动推送消息涉及多个步骤以及开发人员和营销人员的协调。 如果您遇到困难，可以检查一些简单事项。

在确保简单内容正确无误后，您可以深入了解编程详细信息。

### 推送消息未显示

首先，检查听筒上是否禁用了推送消息。 移动用户可以控制他们是否接收任何特定应用程序的消息。 开发人员（和营销人员）通常会在开发过程中的某个时间点禁用这些消息。 因此，首先要检查的是，收件人是否已为您的应用程序禁用推送消息。

其次，应用程序是否已打开并在设备上处于活动状态？ 当您的应用程序在设备上处于活动状态时，移动推送消息不会出现在屏幕上。 相反，它们会显示在应用程序的“本地通知”区域中。

### 在Marketo中查看活动日志

跟踪错误时，首先需要查看的是Marketo活动日志。 您可以使用活动日志来验证是否已发送消息。

在活动日志中，查看应接收消息之人员的活动记录。 如果消息已发送，则活动日志中将显示一条记录。 如果没有，则该问题可能是由于Marketo中的iOS证书或Android API密钥的配置所致。

### 证书或密钥无效

仔细检查您的配置，确保您为沙盒或生产加载了适当的证书。 有时最好由开发人员重新导出证书(iOS)或密钥(Android)，然后将它们重新加载到Marketo中，以确保它们正确。

### .p12文件缺少证书或密钥(iOS)

导出证书时，请确保将密钥&#x200B;_和_&#x200B;导出到证书。

### 配置配置文件已过期(iOS)

无论何时添加新设备，都必须更新配置配置文件并生成新证书。 确保Xcode项目随后指向正确的用户档案和证书，并将这些证书导入Marketo。

### 无法上传iOS证书(IOS)

确保导出证书时使用的密码不包含空格。  例如，不要这样：

`Hello World 123`

使用此：

`HelloWorld123`

### iOS证书疑难解答

对于沙盒应用程序，您可以使用“开发人员”或“通用”证书。 但是，对于生产应用程序，您必须上传有效的“分发”或“通用”证书。

### 推送退回/令牌无效

现有注册令牌在多种情况下可能不再有效，包括：

- 如果客户端应用程序取消向GCM注册，则为。
- 如果客户端应用程序自动取消注册，如果用户卸载该应用程序，则会发生这种情况。 例如，在iOS上，如果APNS反馈服务报告APNS令牌无效。
- 如果注册令牌过期。 例如，Google可能会决定刷新注册令牌，或iOS设备的APNS令牌已过期。
- 如果客户端应用程序已更新，但新版本未配置为接收消息。
