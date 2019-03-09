---
title: 从 Azure 移动应用发送推送通知
description: 本文介绍如何使用 Azure 通知中心将推送通知从 Azure 移动应用实例发送到 Xamarin.Forms 应用程序。
ms.prod: xamarin
ms.assetid: A1EF400F-73F4-43E9-A0C3-1569A0F34A3B
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 11/02/2017
ms.openlocfilehash: 92c068ceb3d382ed4612318dc987d950ec7e7ef2
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/08/2019
ms.locfileid: "57672542"
---
# <a name="sending-push-notifications-from-azure-mobile-apps"></a>从 Azure 移动应用发送推送通知

[![下载示例](~/media/shared/download.png)下载示例](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoAzurePush/)

_Azure 通知中心提供可伸缩的推送基础结构用于发送移动推送通知从任何后端向任何移动平台，同时消除了无需与不同平台通知系统进行通信的后端的复杂性。本文介绍如何使用 Azure 通知中心将推送通知从 Azure 移动应用实例发送到 Xamarin.Forms 应用程序。_

> [!VIDEO https://youtube.com/embed/le2lDY22xwM]

**Azure 通过推送通知中心和 Xamarin.Forms， [Xamarin 学院课程](https://university.xamarin.com/)**

推送通知用于从移动设备上的应用程序来提高应用程序参与度与使用量的后端系统提供的信息，如一条消息。 通知可以发送在任何时间，即使用户没有频繁使用目标应用程序。

后端系统推送通知发送到移动设备通过平台通知系统 (PNS)，如以下关系图中所示：

[![](azure-images/pns.png "平台通知系统")](azure-images/pns-large.png#lightbox "平台通知系统")

若要发送推送通知后, 端系统，请联系特定于平台的 PNS 将通知发送到客户端应用程序实例。 这会显著增加后端的复杂性时跨平台推送通知是必需的因为后端必须使用每个特定于平台的 PNS API 和协议。

Azure 通知中心会提取不同平台通知系统的详细信息通过消除这种复杂性，从而允许跨平台通知发送使用单个 API 调用，如以下关系图中所示：

[![](azure-images/notification-hub.png)](azure-images/notification-hub-large.png#lightbox)

将发送推送通知后, 端系统唯一联系人 Azure 通知中心，这反过来与不同的平台通知系统进行通信，因此降低复杂性的后端代码，该发送推送通知。

Azure 移动应用具有使用通知中心来推送通知的内置支持。 将从 Azure 移动应用实例的推送通知发送到 Xamarin.Forms 应用程序的过程是按如下所示：

1. Xamarin.Forms 应用程序注册到 PNS，返回的句柄。
1. Azure 移动应用实例将通知发送到 Azure 通知中心，指定要设定为目标的设备的句柄。
1. Azure 通知中心将通知发送到设备的相应的 PNS。
1. PNS 将通知发送到指定的设备。
1. Xamarin.Forms 应用程序处理通知，并显示它。

示例应用程序演示了其数据存储在 Azure 移动应用实例中的待办事项列表应用程序。 每次新项添加到 Azure 移动应用实例中，推送通知发送到 Xamarin.Forms 应用程序。 以下屏幕截图显示每个平台显示已接收的推送通知：

[![](azure-images/screenshots.png "示例应用程序接收推送通知")](azure-images/screenshots-large.png#lightbox "示例应用程序接收推送通知")

有关 Azure 通知中心的详细信息，请参阅[Azure 通知中心](https://azure.microsoft.com/documentation/articles/notification-hubs-push-notification-overview/)并[向 Xamarin.Forms 应用添加推送通知](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-push/)。

## <a name="azure-and-platform-notification-system-setup"></a>Azure 和平台通知系统设置

将 Azure 通知中心集成到 Azure 移动应用实例的过程如下所示：

1. 创建 Azure 移动应用实例。 有关详细信息，请参阅[使用 Azure 移动应用](~/xamarin-forms/data-cloud/consuming/azure.md)。
1. 配置通知中心。 有关详细信息，请参阅[配置通知中心](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-push#configure-hub)。
1. 更新 Azure 移动应用实例，以发送推送通知。 有关详细信息，请参阅[更新服务器项目以发送推送通知](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-push#update-the-server-project-to-send-push-notifications)。
1. 向每个 PNS 注册。
1. 配置通知中心与每个 PNS 进行通信。

以下部分提供每个平台的其他安装说明。

### <a name="ios"></a>iOS

若要使用 Apple 推送通知服务 (APNS) 从 Azure 通知中心，必须实施以下附加步骤：

1. 生成证书签名请求为推送证书与 Keychain Access 工具。 有关详细信息，请参阅[生成的推送证书的证书签名请求文件](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-forms-get-started-push/#generate-the-certificate-signing-request-file-for-the-push-certificate)Azure 文档中心上。
1. 注册在 Apple 开发人员中心的推送通知支持 Xamarin.Forms 应用程序。 有关详细信息，请参阅[注册应用以推送通知](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-forms-get-started-push/#register-your-app-for-push-notifications)Azure 文档中心上。
1. 在 Apple 开发人员中心上创建推送通知已启用预配配置文件为 Xamarin.Forms 应用程序。 有关详细信息，请参阅[创建应用程序的预配配置文件](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-forms-get-started-push/#create-a-provisioning-profile-for-the-app)Azure 文档中心上。
1. 配置通知中心与 APNS 通信。 有关详细信息，请参阅[为 APNS 配置通知中心](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-forms-get-started-push/#configure-the-notification-hub-for-apns)。
1. Xamarin.Forms 应用程序配置为使用新的应用 ID 和预配配置文件。 有关详细信息，请参阅[Xamarin Studio 中配置的 iOS 项目](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-forms-get-started-push/#configuring-the-ios-project-in-xamarin-studio)或[在 Visual Studio 中配置的 iOS 项目](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-forms-get-started-push/#configuring-the-ios-project-in-visual-studio)Azure 文档中心上。

### <a name="android"></a>Android

若要使用 Firebase Cloud Messaging (FCM) 从 Azure 通知中心，必须实施以下附加步骤：

1. 为 FCM 注册。 服务器 API 密钥和客户端 ID 是自动生成，并且打包`google-services.json`下载文件。 有关详细信息，请参阅[启用 Firebase Cloud Messaging (FCM)](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-push#enable-firebase-cloud-messaging-fcm)。
1. 配置要与其 FCM 的通知中心。 有关详细信息，请参阅[移动应用后端以发送推送请求使用 FCM 配置](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-push#configure-the-mobile-apps-back-end-to-send-push-requests-by-using-fcm)。

### <a name="universal-windows-platform"></a>通用 Windows 平台

若要使用 Azure 通知中心从 Windows 通知服务 (WNS)，必须实施以下附加步骤：

1. 注册 Windows 通知服务 (WNS)。 有关详细信息，请参阅[Windows 应用以推送通知注册到 WNS](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-forms-get-started-push/#register-your-windows-app-for-push-notifications-with-wns) Azure 文档中心上。
1. 配置为与 WNS 的通知中心。 有关详细信息，请参阅[为 WNS 配置通知中心](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-forms-get-started-push/#configure-the-notification-hub-for-wns)Azure 文档中心上。

## <a name="adding-push-notification-support-to-the-xamarinforms-application"></a>向 Xamarin.Forms 应用程序添加推送通知支持

以下各节讨论每个特定于平台的项目中支持推送通知所需的实现。

### <a name="ios"></a>iOS

在 iOS 应用程序中实现的推送通知支持的过程如下所示：

1. 注册与 Apple 推送通知服务 (APNS) 在`AppDelegate.FinishedLaunching`方法。 有关详细信息，请参阅[Apple 推送通知系统中注册](#ios_register)。
1. 实现`AppDelegate.RegisteredForRemoteNotifications`方法以处理注册响应。 有关详细信息，请参阅[处理注册响应](#ios_registration_response)。
1. 实现`AppDelegate.DidReceiveRemoteNotification`方法来处理传入的推送通知。 有关详细信息，请参阅[处理传入的推送通知](#ios_process_incoming)。

<a name="ios_register" />

### <a name="registering-with-the-apple-push-notification-service"></a>向 Apple Push Notification 服务注册

IOS 应用程序才能接收推送通知之前，它必须注册与 Apple 推送通知服务 (APNS)，这将生成唯一的设备令牌并将其返回到应用程序。 注册调用中`FinishedLaunching`重写中`AppDelegate`类：

```csharp
public override bool FinishedLaunching(UIApplication app, NSDictionary options)
{
    ...
    var settings = UIUserNotificationSettings.GetSettingsForTypes(
        UIUserNotificationType.Alert, new NSSet());

    UIApplication.SharedApplication.RegisterUserNotificationSettings(settings);
    UIApplication.SharedApplication.RegisterForRemoteNotifications();
    ...
}
```

IOS 应用程序将向 APNS 注册时，它必须指定所要接收推送通知的类型。 `RegisterUserNotificationSettings`方法注册的应用程序可以接收，通知类型的与`RegisterForRemoteNotifications`注册以接收来自 APNS 的推送通知的方法。

> [!NOTE]
> 未能调用`RegisterUserNotificationSettings`方法将导致应用程序以无提示方式接收推送通知。

<a name="ios_registration_response" />

### <a name="handling-the-registration-response"></a>处理注册响应

在后台发生，APNS 注册请求。 收到响应后，将调用 iOS`RegisteredForRemoteNotifications`重写中`AppDelegate`类：

```csharp
public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
{
    const string templateBodyAPNS = "{\"aps\":{\"alert\":\"$(messageParam)\"}}";

    JObject templates = new JObject();
    templates["genericMessage"] = new JObject
    {
        {"body", templateBodyAPNS}
    };

    // Register for push with the Azure mobile app
    Push push = TodoItemManager.DefaultManager.CurrentClient.GetPush();
    push.RegisterAsync(deviceToken, templates);
}
```

此方法会创建一个简单的通知消息模板为 JSON，并注册设备以从通知中心接收模板通知。

> [!NOTE]
> `FailedToRegisterForRemoteNotifications`应实现重写以处理无网络连接等情况。 这很重要，因为用户可能会启动应用程序，同时脱机。

<a name="ios_process_incoming" />

### <a name="processing-incoming-push-notifications"></a>处理传入的推送通知

`DidReceiveRemoteNotification`重写中`AppDelegate`类用于处理传入的推送通知时该应用程序正在运行，并接收通知时要调用：

```csharp
public override void DidReceiveRemoteNotification(
    UIApplication application, NSDictionary userInfo, Action<UIBackgroundFetchResult> completionHandler)
{
    NSDictionary aps = userInfo.ObjectForKey(new NSString("aps")) as NSDictionary;

    string alert = string.Empty;
    if (aps.ContainsKey(new NSString("alert")))
        alert = (aps[new NSString("alert")] as NSString).ToString();

    // Show alert
    if (!string.IsNullOrEmpty(alert))
    {
      var notificationAlert = UIAlertController.Create("Notification", alert, UIAlertControllerStyle.Alert);
      notificationAlert.AddAction(UIAlertAction.Create("OK", UIAlertActionStyle.Cancel, null));
      UIApplication.SharedApplication.KeyWindow.RootViewController.PresentViewController(notificationAlert, true, null);
    }
}
```

`userInfo`字典包含`aps`键，其值是`alert`与剩余的通知数据的字典。 检索此字典时，使用`string`在对话框中显示的通知消息。

> [!NOTE]
> 如果应用程序没有运行推送通知到达时，将启动应用程序，但`DidReceiveRemoteNotification`方法不会处理该通知。 相反，获取通知有效负载并适当地响应来自`WillFinishLaunching`或`FinishedLaunching`重写。

APNS 的详细信息，请参阅[iOS 中的推送通知](~/ios/platform/user-notifications/deprecated/remote-notifications-in-ios.md)。

### <a name="android"></a>Android

在 Android 应用程序中实现的推送通知支持的过程如下所示：

1. 添加[Xamarin.Firebase.Messaging](https://www.nuget.org/packages/Xamarin.Firebase.Messaging/) NuGet 打包到 Android 项目，并设置应用程序的目标版本为 Android 7.0 或更高。
1. 添加`google-services.json`文件中，从 Firebase 控制台中，下载到 Android 项目的根目录，并将其生成操作设置为**GoogleServicesJson**。 有关详细信息，请参阅[Google 服务 JSON 文件添加](~/android/data-cloud/google-messaging/remote-notifications-with-fcm.md)。
1. 通过声明 Android 清单中的接收器注册使用 Firebase Cloud Messaging (FCM) 文件，并通过实现`FirebaseRegistrationService.OnTokenRefresh`方法。 有关详细信息，请参阅[注册到 Firebase Cloud Messaging](#android_register_fcm)。
1. 使用 Azure 通知中心注册`AzureNotificationHubService.RegisterAsync`方法。 有关详细信息，请参阅[向 Azure 通知中心注册](#android_register_azure)。
1. 实现`FirebaseNotificationService.OnMessageReceived`方法来处理传入的推送通知。 有关详细信息，请参阅[显示的推送通知内容](#android_displaying_notification)。

Firebase Cloud Messaging 的详细信息，请参阅[Firebase Cloud Messaging](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md)并[远程通知使用 Firebase Cloud Messaging](~/android/data-cloud/google-messaging/remote-notifications-with-fcm.md)。

<a name="android_register_fcm" />

#### <a name="registering-with-firebase-cloud-messaging"></a>注册到 Firebase 云消息传送

Android 应用程序才能接收推送通知之前，它必须注册 FCM，这将生成的注册令牌并将其返回到应用程序。 有关注册令牌的详细信息，请参阅[FCM 注册](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md#registration)。

这是通过实现：

- 声明 Android 清单中的接收器。 有关详细信息，请参阅[声明在 Android 清单中的接收方](#declaring_a_receiver)。
- 实现 Firebase 实例 ID 服务。 有关详细信息，请参阅[实现 Firebase 实例 ID 服务](#implementing-firebase-instance-id-service)。

<a name="declaring_a_receiver" />

##### <a name="declaring-the-receiver-in-the-android-manifest"></a>声明在 Android 清单中的接收方

编辑**AndroidManifest.xml** ，并插入以下`<receiver>`元素插入`<application>`元素：

```xml
<receiver android:name="com.google.firebase.iid.FirebaseInstanceIdInternalReceiver" android:exported="false" />
<receiver android:name="com.google.firebase.iid.FirebaseInstanceIdReceiver" android:exported="true" android:permission="com.google.android.c2dm.permission.SEND">
  <intent-filter>
    <action android:name="com.google.android.c2dm.intent.RECEIVE" />
    <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
    <category android:name="${applicationId}" />
  </intent-filter>
</receiver>
```

此 XML 将执行以下操作：

- 声明的内部`FirebaseInstanceIdInternalReceiver`用于安全地启动服务的实现。
- 声明`FirebaseInstanceIdReceiver`实现，为每个应用实例提供唯一标识符。 此接收器还进行身份验证和授权操作。

`FirebaseInstanceIdReceiver`接收`FirebaseInstanceId`并`FirebaseMessaging`事件并将其传递到派生自类`FirebasesInstanceIdService`。

<a name="implementing-firebase-instance-id-service" />

##### <a name="implementing-the-firebase-instance-id-service"></a>实现 Firebase 实例 ID 服务

使用 FCM 注册应用程序通过派生类中的实现`FirebaseInstanceIdService`类。 此类负责生成授权客户端应用程序访问 FCM 的安全令牌。 在示例应用程序`FirebaseRegistrationService`类派生自`FirebaseInstanceIdService`类，并在下面的代码示例所示：

```csharp
[Service]
[IntentFilter(new[] { "com.google.firebase.INSTANCE_ID_EVENT" })]
public class FirebaseRegistrationService : FirebaseInstanceIdService
{
    const string TAG = "FirebaseRegistrationService";

    public override void OnTokenRefresh()
    {
        var refreshedToken = FirebaseInstanceId.Instance.Token;
        Log.Debug(TAG, "Refreshed token: " + refreshedToken);
        SendRegistrationTokenToAzureNotificationHub(refreshedToken);
    }

    void SendRegistrationTokenToAzureNotificationHub(string token)
    {
        // Update notification hub registration
        Task.Run(async () =>
        {
            await AzureNotificationHubService.RegisterAsync(TodoItemManager.DefaultManager.CurrentClient.GetPush(), token);
        });
    }
}
```

`OnTokenRefresh`调用方法时应用程序收到来自 FCM 注册令牌。 方法检索颁发的令牌`FirebaseInstanceId.Instance.Token`属性通过 FCM 以异步方式更新。 `OnTokenRefresh`不经常调用方法，因为安装或卸载，当用户删除应用程序数据，应用程序清除实例 ID，该应用程序时，才会更新令牌或令牌的安全后受到影响。 此外，FCM 实例 ID 服务将请求的应用程序定期刷新其令牌，通常每隔 6 个月。

`OnTokenRefresh`方法还会调用`SendRegistrationTokenToAzureNotificationHub`方法，用于将用户的注册令牌与 Azure 通知中心相关联。

<a name="android_register_azure" />

#### <a name="registering-with-the-azure-notification-hub"></a>Azure 通知中心注册

`AzureNotificationHubService`类提供了`RegisterAsync`方法，使用户的注册令牌与 Azure 通知中心相关联。 下面的代码示例演示`RegisterAsync`方法，通过调用`FirebaseRegistrationService`类用户的注册令牌更改时：

```csharp
public class AzureNotificationHubService
{
    const string TAG = "AzureNotificationHubService";

    public static async Task RegisterAsync(Push push, string token)
    {
        try
        {
            const string templateBody = "{\"data\":{\"message\":\"$(messageParam)\"}}";
            JObject templates = new JObject();
            templates["genericMessage"] = new JObject
            {
                {"body", templateBody}
            };

            await push.RegisterAsync(token, templates);
            Log.Info("Push Installation Id: ", push.InstallationId.ToString());
        }
        catch (Exception ex)
        {
            Log.Error(TAG, "Could not register with Notification Hub: " + ex.Message);
        }
    }
}
```

此方法创建简单的通知消息模板为 JSON 和寄存器以从使用 Firebase 注册令牌的通知中心接收模板通知。 这可确保从 Azure 通知中心发送任何通知将面向注册令牌所表示的设备。

<a name="android_displaying_notification" />

#### <a name="displaying-the-contents-of-a-push-notification"></a>显示一条推送通知的内容

显示一条推送通知的内容通过派生类中的实现`FirebaseMessagingService`类。 此类包括可重写`OnMessageReceived`方法，该应用程序接收来自 FCM 的通知时调用，提供应用程序在前台运行。 在示例应用程序`FirebaseNotificationService`类派生自`FirebaseMessagingService`类，并在下面的代码示例所示：

```csharp
[Service]
[IntentFilter(new[] { "com.google.firebase.MESSAGING_EVENT" })]
public class FirebaseNotificationService : FirebaseMessagingService
{
    const string TAG = "FirebaseNotificationService";

    public override void OnMessageReceived(RemoteMessage message)
    {
        Log.Debug(TAG, "From: " + message.From);

        // Pull message body out of the template
        var messageBody = message.Data["message"];
        if (string.IsNullOrWhiteSpace(messageBody))
            return;

        Log.Debug(TAG, "Notification message body: " + messageBody);
        SendNotification(messageBody);
    }

    void SendNotification(string messageBody)
    {
        var intent = new Intent(this, typeof(MainActivity));
        intent.AddFlags(ActivityFlags.ClearTop);
        var pendingIntent = PendingIntent.GetActivity(this, 0, intent, PendingIntentFlags.OneShot);

        var notificationBuilder = new NotificationCompat.Builder(this)
            .SetSmallIcon(Resource.Drawable.ic_stat_ic_notification)
            .SetContentTitle("New Todo Item")
            .SetContentText(messageBody)
            .SetContentIntent(pendingIntent)
            .SetSound(RingtoneManager.GetDefaultUri(RingtoneType.Notification))
            .SetAutoCancel(true);

        var notificationManager = NotificationManager.FromContext(this);
        notificationManager.Notify(0, notificationBuilder.Build());
    }
}
```

当应用程序接收来自 FCM 的通知`OnMessageReceived`方法中提取消息内容，并调用`SendNotification`方法。 此方法转换为本地运行应用程序时，并将通知显示在通知区域启动的通知消息内容。

##### <a name="handling-notification-intents"></a>处理通知意向

当用户点击通知时，随附的通知消息的任何数据都可以在`Intent`其他功能。 可以使用以下代码中提取此数据：

```csharp
if (Intent.Extras != null)
{
  foreach (var key in Intent.Extras.KeySet())
  {
    var value = Intent.Extras.GetString(key);
    Log.Debug(TAG, "Key: {0} Value: {1}", key, value);
  }
}
```

应用程序的启动器`Intent`当用户点击其通知消息，因此此代码将记录中的任何随附的数据时触发`Intent`到输出窗口。

### <a name="universal-windows-platform"></a>通用 Windows 平台

之前通用 Windows 平台 (UWP) 应用程序可以接收推送通知，它必须注册使用 Windows 通知服务 (WNS)，它将返回通知通道。 注册通过调用`InitNotificationsAsync`中的方法`App`类：

```csharp
private async Task InitNotificationsAsync()
{
    var channel = await PushNotificationChannelManager
          .CreatePushNotificationChannelForApplicationAsync();

    const string templateBodyWNS =
        "<toast><visual><binding template=\"ToastText01\"><text id=\"1\">$(messageParam)</text></binding></visual></toast>";

    JObject headers = new JObject();
    headers["X-WNS-Type"] = "wns/toast";

    JObject templates = new JObject();
    templates["genericMessage"] = new JObject
    {
        {"body", templateBodyWNS},
        {"headers", headers} // Needed for WNS.
    };

    await TodoItemManager.DefaultManager.CurrentClient.GetPush()
          .RegisterAsync(channel.Uri, templates);
}
```

此方法获取推送通知通道、 创建通知消息模板为 JSON，并注册设备以从通知中心接收模板通知。

`InitNotificationsAsync`方法调用从`OnLaunched`重写中`App`类：

```csharp
protected override async void OnLaunched(LaunchActivatedEventArgs e)
{
    ...
    await InitNotificationsAsync();
}
```

这可确保创建或刷新每次启动应用程序，因此确保 WNS 推送通道始终处于活动状态时注册推送通知。

收到推送通知时它将自动显示为*toast* – 一个包含消息的无模式窗口。

## <a name="summary"></a>总结

本文演示了如何使用 Azure 通知中心将推送通知从 Azure 移动应用实例发送到 Xamarin.Forms 应用程序。 Azure 通知中心提供可伸缩的推送基础结构用于发送移动推送通知从任何后端向任何移动平台，同时消除了无需与不同平台通知系统进行通信的后端的复杂性。


## <a name="related-links"></a>相关链接

- [使用 Azure 移动应用](~/xamarin-forms/data-cloud/consuming/azure.md)
- [Azure 通知中心](https://azure.microsoft.com/documentation/articles/notification-hubs-push-notification-overview/)
- [向 Xamarin.Forms 应用添加推送通知](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-forms-get-started-push/)
- [在 iOS 中的推送通知](~/ios/platform/user-notifications/deprecated/remote-notifications-in-ios.md)
- [Firebase 云消息传送](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md)
- [TodoAzurePush （示例）](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoAzurePush/)
- [Azure 移动客户端 SDK](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)
