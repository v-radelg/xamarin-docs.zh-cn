---
title: 发送和接收使用 Azure 通知中心和 Xamarin.Forms 的推送通知
description: 本文介绍如何使用 Azure 通知中心将跨平台推送通知发送到 Xamarin.Forms 应用程序。
ms.prod: xamarin
ms.assetid: 07D13195-3A0D-4C95-ACF0-143A9084973C
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 05/23/2019
ms.openlocfilehash: 474398922bf00e3a430166d8b2e073d200e6ed6e
ms.sourcegitcommit: c1d85b2c62ad84c22bdee37874ad30128581bca6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/08/2019
ms.locfileid: "67659324"
---
# <a name="send-and-receive-push-notifications-with-azure-notification-hubs-and-xamarinforms"></a>发送和接收使用 Azure 通知中心和 Xamarin.Forms 的推送通知

[![下载示例](~/media/shared/download.png)下载示例](https://github.com/xamarin/xamarin-forms-samples/tree/master/WebServices/AzureNotificationHub)

为移动应用程序，从后端系统推送通知传递信息。 Apple、 Google 和其他平台每个具有其自己推送通知服务 (PNS)。 Azure 通知中心，你可以集中跨平台通知，以便后端应用程序能够与单个中心，它负责将分发到每个特定于平台的 PNS 的通知的通信。

将 Azure 通知中心集成到移动应用中，通过执行以下步骤：

1. [设置推送通知服务和 Azure 通知中心](#set-up-push-notification-services-and-azure-notification-hub)。
1. [了解如何使用模板和标记](#register-templates-and-tags-with-the-azure-notification-hub)。
1. [创建跨平台 Xamarin.Forms 应用程序](#xamarinforms-application-functionality)。
1. [配置推送通知的本机 Android 项目](#configure-the-android-application-for-notifications)。
1. [配置本机 iOS 项目以发送推送通知](#configure-ios-for-notifications)。
1. [测试使用 Azure 通知中心通知](#test-notifications-in-the-azure-portal)。
1. [创建后端应用程序发送通知](#create-a-notification-dispatcher)。

## <a name="set-up-push-notification-services-and-azure-notification-hub"></a>设置推送通知服务和 Azure 通知中心

与 Xamarin.Forms 移动应用中集成 Azure 通知中心是类似于将 Azure 通知中心与 Xamarin 本机应用程序相集成。 设置**FCM 应用程序**Firebase 控制台中按照以下步骤[推送通知发送到 Xamarin.Android 使用 Azure 通知中心](/azure/notification-hubs/xamarin-notification-hubs-push-notifications-android-gcm#create-a-firebase-project-and-enable-firebase-cloud-messaging)。 完成以下步骤使用 Xamarin.Android 教程：

1. 定义一个 Android 包名称，如`com.xamarin.notifysample`，会在此示例中使用。
1. 下载**google-services.json**从 Firebase 控制台。 会将此文件添加到 Android 应用程序中以后的步骤。
1. 创建 Azure 通知中心实例，并为其提供一个名称。 此文章和示例，请使用`xdocsnotificationhub`作为中心名称。
1. 将复制的 FCM**服务器密钥**并将其保存为**API 密钥**下**Google (GCM/FCM)** 在 Azure 通知中心。

下面的屏幕截图显示了 Azure 通知中心中的 Google 平台配置：

![Azure 通知中心 Google 配置的屏幕截图](azure-notification-hub-images/fcm-notification-hub-config.png "Azure 通知中心 Google 配置")

您将需要用于完成适用于 iOS 设备设置的 macOS 计算机。 通过以下初始步骤中设置 APNS[将通知推送到使用 Azure 通知中心的 Xamarin.iOS](/azure/notification-hubs/xamarin-notification-hubs-ios-push-notification-apns-get-started#generate-the-certificate-signing-request-file)。 完成以下步骤使用 Xamarin.iOS 教程：

1. 定义 iOS 捆绑包标识符。 此文章和示例，请使用`com.xamarin.notifysample`作为捆绑包标识符。
1. 创建证书签名请求 (CSR) 文件，并使用它来生成的推送通知证书。
1. 将下的推送通知证书上传**Apple (APNS)** 在 Azure 通知中心。

下面的屏幕截图显示了 Azure 通知中心中的 Apple 平台配置：

![Azure 通知中心 Apple 配置的屏幕截图](azure-notification-hub-images/apns-notification-hub-config.png "Azure 通知中心 Apple 配置")

## <a name="register-templates-and-tags-with-the-azure-notification-hub"></a>使用 Azure 通知中心注册模板和标记

Azure 通知中心需要移动应用程序注册到中心，定义模板和订阅标记。 注册链接到 Azure 通知中心中的标识符的特定于平台的 PNS 句柄。 若要了解有关注册的详细信息，请参阅[注册管理](/azure/notification-hubs/notification-hubs-push-notification-registration-management)。

模板允许设备指定参数化的消息模板。 每个设备，每个标记，可以自定义传入消息。 若要了解有关模板的详细信息，请参阅[模板](/azure/notification-hubs/notification-hubs-templates-cross-platform-push-messages)。

可以使用标记来订阅消息类别，如新闻、 体育和天气。 为简单起见，示例应用程序的默认模板定义的单个参数名为`messageParam`和单个标记调用`default`。 在更复杂系统中，特定于用户的标记可用于消息在发送个性化通知的设备上的用户。 若要了解有关标记的详细信息，请参阅[路由和标记表达式](/azure/notification-hubs/notification-hubs-tags-segment-push-message)。

若要成功接收消息，每个本机应用程序必须执行以下步骤：

1. 从平台 PNS 中获取 PNS 句柄或令牌。
1. 向 Azure 通知中心注册 PNS 句柄。
1. 指定包含作为传出消息的相同参数的模板。
1. 订阅的传出消息目标的标记。

为在每个平台的进一步详细介绍这些步骤[配置 Android 应用程序以通知](#configure-the-android-application-for-notifications)并[配置适用于通知的 iOS](#configure-ios-for-notifications)部分。

## <a name="xamarinforms-application-functionality"></a>Xamarin.Forms 应用程序功能

示例 Xamarin.Forms 应用程序显示推送通知消息的列表。 此，可以使用`AddMessage`方法，将指定的推送通知消息添加到 UI。 此方法还可防止重复的消息添加到 UI，并在主线程上运行，因此它可以从任何线程调用。 下面的代码演示了 `AddMessage` 方法：

```csharp
public void AddMessage(string message)
{
    Device.BeginInvokeOnMainThread(() =>
    {
        if (messageDisplay.Children.OfType<Label>().Where(c => c.Text == message).Any())
        {
            // Do nothing, an identical message already exists
        }
        else
        {
            Label label = new Label()
            {
                Text = message,
                HorizontalOptions = LayoutOptions.CenterAndExpand,
                VerticalOptions = LayoutOptions.Start
            };
            messageDisplay.Children.Add(label);
        }
    });
}
```

示例应用程序包含**AppConstants.cs**文件，用于定义所用平台项目的属性。 需要使用 Azure 通知中心中的值进行自定义此文件。 下面的代码演示**AppConstants.cs**文件：

```csharp
public static class AppConstants
{
    public static string NotificationChannelName { get; set; } = "XamarinNotifyChannel";
    public static string NotificationHubName { get; set; } = "< Insert your Azure Notification Hub name >";
    public static string ListenConnectionString { get; set; } = "< Insert your DefaultListenSharedAccessSignature >";
    public static string DebugTag { get; set; } = "XamarinNotify";
    public static string[] SubscriptionTags { get; set; } = { "default" };
    public static string FCMTemplateBody { get; set; } = "{\"data\":{\"message\":\"$(messageParam)\"}}";
    public static string APNTemplateBody { get; set; } = "{\"aps\":{\"alert\":\"$(messageParam)\"}}";
}
```

自定义中的以下值`AppConstants`连接到 Azure 通知中心示例应用程序：

* `NotificationHubName`：使用 Azure 门户中创建的 Azure 通知中心的名称。
* `ListenConnectionString`：在下的 Azure 通知中心中找到该值**访问策略**。

以下屏幕截图显示了这些值在 Azure 门户中的位置：

![Azure 通知中心访问策略的屏幕截图](azure-notification-hub-images/notification-hub-access-policy.png "Azure 通知中心访问策略")

## <a name="configure-the-android-application-for-notifications"></a>配置通知的 Android 应用程序

完成以下步骤以配置 Android 应用程序来接收和处理通知：

1. 配置 Android**包名称**以匹配在 Firebase 控制台中的包名称。
1. 安装以下 NuGet 包与 Google Play、 Firebase 和 Azure 通知中心进行交互：
    1. Xamarin.GooglePlayServices.Base.
    1. Xamarin.Firebase.Messaging。
    1. Xamarin.Azure.NotificationHubs.Android.
1. 复制`google-services.json`文件下载到项目的 FCM 安装过程并生成操作设置为`GoogleServicesJson`。
1. [配置与 Firebase 进行通信的 AndroidManifest.xml](#configure-android-manifest)。
1. [使用 Azure 通知中心和 Firebase 注册应用程序使用`FirebaseInstanceIdService` ](#register-using-a-custom-firebaseinstanceidservice)。
1. [处理的消息`FirebaseMessagingService` ](#process-messages-with-a-firebasemessagingservice)。
1. [将传入通知添加到 Xamarin.Forms UI](#add-incoming-notifications-to-the-xamarinforms-ui)。

> [!NOTE]
> **GoogleServicesJson**生成操作属于**Xamarin.GooglePlayServices.Base** NuGet 包。 Visual Studio 2019 设置在启动期间可用的生成操作。 如果没有看到**GoogleServicesJson**作为生成操作之后, 重新启动 Visual Studio 2019 安装 NuGet 包。

### <a name="configure-android-manifest"></a>配置 Android 清单

`receiver`中的元素`application`元素允许应用与 Firebase 进行通信。 `uses-permission`元素使应用能够处理消息并向 Azure 通知中心注册。 完整**AndroidManifest.xml**应类似于下面的示例：

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android" android:versionCode="1" android:versionName="1.0" package="YOUR_PACKAGE_NAME" android:installLocation="auto">
  <uses-sdk android:minSdkVersion="21" />
  <uses-permission android:name="android.permission.INTERNET" />
  <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
  <uses-permission android:name="android.permission.WAKE_LOCK" />
  <uses-permission android:name="android.permission.GET_ACCOUNTS"/>
  <application android:label="Notification Hub Sample">
    <receiver android:name="com.google.firebase.iid.FirebaseInstanceIdInternalReceiver" android:exported="false" />
    <receiver android:name="com.google.firebase.iid.FirebaseInstanceIdReceiver" android:exported="true" android:permission="com.google.android.c2dm.permission.SEND">
      <intent-filter>
        <action android:name="com.google.android.c2dm.intent.RECEIVE" />
        <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
        <category android:name="${applicationId}" />
      </intent-filter>
    </receiver>
  </application>
</manifest>
```

### <a name="register-using-a-custom-firebaseinstanceidservice"></a>使用自定义 FirebaseInstanceIdService 注册

Firebase 颁发令牌的唯一地标识 PNS 上的设备。 令牌具有长使用期限，但偶尔会刷新一次。 令牌是颁发或刷新时，应用程序需要向 Azure 通知中心注册其新的令牌。 由派生类的实例处理注册`FirebaseInstanceIdService`。

在示例应用程序，`FirebaseRegistrationService`类继承自`FirebaseInstanceIdService`。 此类具有`IntentFilter`，其中包含`com.google.firebase.INSTANCE_ID_EVENT`，从而允许 Android OS 自动调用`OnTokenRefresh`Firebase 时颁发令牌。

下面的代码演示了自定义`FirebaseInstanceIdService`示例应用程序：

```csharp
[Service]
[IntentFilter(new [] { "com.google.firebase.INSTANCE_ID_EVENT"})]
public class FirebaseRegistrationService : FirebaseInstanceIdService
{
    public override void OnTokenRefresh()
    {
        string token = FirebaseInstanceId.Instance.Token;

        // NOTE: logging the token is not recommended in production but during
        // development it is useful to test messages directly from Firebase
        Log.Info(AppConstants.DebugTag, $"Token received: {token}");

        SendRegistrationToServer(token);
    }

    void SendRegistrationToServer(string token)
    {
        try
        {
            NotificationHub hub = new NotificationHub(AppConstants.NotificationHubName, AppConstants.ListenConnectionString, this);

            // register device with Azure Notification Hub using the token from FCM
            Registration reg = hub.Register(token, AppConstants.SubscriptionTags);

            // subscribe to the SubscriptionTags list with a simple template.
            string pnsHandle = reg.PNSHandle;
            var cats = string.Join(", ", reg.Tags);
            var temp = hub.RegisterTemplate(pnsHandle, "defaultTemplate", AppConstants.FCMTemplateBody, AppConstants.SubscriptionTags);
        }
        catch (Exception e)
        {
            Log.Error(AppConstants.DebugTag, $"Error registering device: {e.Message}");
        }
    }
}
```

`SendRegistrationToServer`中的方法`FirebaseRegistrationClass`向 Azure 通知中心注册设备并订阅的标记和模板。 示例应用程序定义名为单个标记`default`且具有一个参数的模板调用`messageParam`中**AppConstants.cs**文件。 有关注册、 标记和模板的详细信息，请参阅[注册到 Azure 通知中心的模板和标记](#register-templates-and-tags-with-the-azure-notification-hub)

### <a name="process-messages-with-a-firebasemessagingservice"></a>使用 FirebaseMessagingService 处理消息

传入消息路由到`FirebaseMessagingService`实例，其中他们可以转换为本地通知。 示例应用程序中的 Android 项目包含一个名为类`FirebaseService`，它继承自`FirebaseMessagingService`。 此类具有`IntentFilter`，其中包含`com.google.firebase.MESSAGING_EVENT`，允许 Android 操作系统会自动调用`OnMessageReceived`收到推送通知消息。

下面的示例演示`FirebaseService`示例应用程序：

```csharp
[Service]
[IntentFilter(new[] { "com.google.firebase.MESSAGING_EVENT" })]
public class FirebaseService : FirebaseMessagingService
{
    public override void OnMessageReceived(RemoteMessage message)
    {
        base.OnMessageReceived(message);
        string messageBody = string.Empty;

        if (message.GetNotification() != null)
        {
            messageBody = message.GetNotification().Body;
        }

        // NOTE: test messages sent via the Azure portal will be received here
        else
        {
            messageBody = message.Data.Values.First();
        }

        // convert the incoming message to a local notification
        SendLocalNotification(messageBody);

        // send the incoming message directly to the MainPage
        SendMessageToMainPage(messageBody);
    }

    void SendLocalNotification(string body)
    {
        var intent = new Intent(this, typeof(MainActivity));
        intent.AddFlags(ActivityFlags.ClearTop);
        intent.PutExtra("message", body);
        var pendingIntent = PendingIntent.GetActivity(this, 0, intent, PendingIntentFlags.OneShot);

        var notificationBuilder = new NotificationCompat.Builder(this)
            .SetContentTitle("XamarinNotify Message")
            .SetSmallIcon(Resource.Drawable.ic_launcher)
            .SetContentText(body)
            .SetAutoCancel(true)
            .SetShowWhen(false)
            .SetContentIntent(pendingIntent);

        if (Build.VERSION.SdkInt >= BuildVersionCodes.O)
        {
            notificationBuilder.SetChannelId(AppConstants.NotificationChannelName);
        }

        var notificationManager = NotificationManager.FromContext(this);
        notificationManager.Notify(0, notificationBuilder.Build());
    }

    void SendMessageToMainPage(string body)
    {
        (App.Current.MainPage as MainPage)?.AddMessage(body);
    }
}
```

传入消息转换为使用本地通知`SendLocalNotification`方法。 此方法创建一个新`Intent`，并将消息放入内容`Intent`作为`string` `Extra`。 当用户点击本地通知，无论应用是在前台或后台`MainActivity`启动和有权访问通过消息内容`Intent`对象。

本地通知和`Intent`示例要求用户对通知执行的点击操作。 用户应该采取的操作之前应用程序状态更改时，这是理想的。 但是，你可能想要访问消息数据，而无需用户操作，在某些情况下。 前面的示例还会将消息发送到当前直接`MainPage`实例与`SendMessageToMainPage`方法。 在生产中，如果实现单一消息类型，这两种方法`MainPage`对象将获得重复的消息，如果在用户点击通知。

> [!NOTE]
> 如果运行位于后台还是前台，Android 应用程序将仅收到推送通知。 若要接收推送通知时主`Activity`是未运行，必须实现一个服务，它不在此示例的范围。 有关详细信息，请参阅[创建 Android 服务](/xamarin/android/app-fundamentals/services/)

### <a name="add-incoming-notifications-to-the-xamarinforms-ui"></a>将传入通知添加到 Xamarin.Forms UI

`MainActivity`类需要获取处理通知和管理传入消息的数据的权限。 下面的代码演示的完整`MainActivity`实现：

```csharp
[Activity(Label = "NotificationHubSample", Icon = "@mipmap/icon", Theme = "@style/MainTheme", MainLauncher = true, ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation, LaunchMode = LaunchMode.SingleTop)]
public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsAppCompatActivity
{
    protected override void OnCreate(Bundle savedInstanceState)
    {
        TabLayoutResource = Resource.Layout.Tabbar;
        ToolbarResource = Resource.Layout.Toolbar;

        base.OnCreate(savedInstanceState);

        global::Xamarin.Forms.Forms.Init(this, savedInstanceState);
        LoadApplication(new App());

        if (IsPlayServiceAvailable() == false)
        {
            throw new Exception("This device does not have Google Play Services and cannot receive push notifications.");
        }

        CreateNotificationChannel();
    }

    protected override void OnNewIntent(Intent intent)
    {
        if (intent.Extras != null)
        {
            var message = intent.GetStringExtra("message");
            (App.Current.MainPage as MainPage)?.AddMessage(message);
        }

        base.OnNewIntent(intent);
    }

    bool IsPlayServiceAvailable()
    {
        int resultCode = GoogleApiAvailability.Instance.IsGooglePlayServicesAvailable(this);
        if (resultCode != ConnectionResult.Success)
        {
            if (GoogleApiAvailability.Instance.IsUserResolvableError(resultCode))
                Log.Debug(AppConstants.DebugTag, GoogleApiAvailability.Instance.GetErrorString(resultCode));
            else
            {
                Log.Debug(AppConstants.DebugTag, "This device is not supported");
            }
            return false;
        }
        return true;
    }

    void CreateNotificationChannel()
    {
        // Notification channels are new as of "Oreo".
        // There is no need to create a notification channel on older versions of Android.
        if (Build.VERSION.SdkInt >= BuildVersionCodes.O)
        {
            var channelName = AppConstants.NotificationChannelName;
            var channelDescription = String.Empty;
            var channel = new NotificationChannel(channelName, channelName, NotificationImportance.Default)
            {
                Description = channelDescription
            };

            var notificationManager = (NotificationManager)GetSystemService(NotificationService);
            notificationManager.CreateNotificationChannel(channel);
        }
    }
}
```

`Activity`属性将设置应用程序`LaunchMode`到`SingleTop`。 此启动模式下，将告知 Android OS 为仅允许此活动的单一实例。 在此的启动模式下，传入`Intent`数据路由到`OnNewIntent`方法，用于提取消息数据并将其发送到`MainPage`实例通过`AddMessage`方法。 如果你的应用程序使用不同的启动模式，它必须处理`Intent`数据以不同的方式。

`OnCreate`方法使用一个名为的帮助器方法`IsPlayServiceAvailable`以确保设备支持 Google Play 服务。 仿真程序或不支持 Google Play 服务的设备不能从 Firebase 接收推送通知。

## <a name="configure-ios-for-notifications"></a>配置 iOS 通知

配置 iOS 应用程序以接收通知的过程是：

1. 配置**捆绑包标识符**中**Info.plist**文件以匹配预配配置文件中使用的值。
1. 添加**启用推送通知**选项设为**Entitlements.plist**文件。
1. 添加**Xamarin.Azure.NotificationHubs.iOS**到你的项目的 NuGet 包。
1. [向 APNS 注册通知](#register-for-notifications-with-apns)。
1. [Azure 通知中心注册应用程序并订阅标记](#register-with-azure-notification-hub-and-subscribe-to-tags)。
1. [将 APNS 的通知添加到 Xamarin.Forms UI](#add-apns-notifications-to-xamarinforms-ui)。

以下屏幕截图显示**启用推送通知**中选择选项**Entitlements.plist** Visual Studio 中的文件：

![推送通知权利的屏幕截图](azure-notification-hub-images/push-notification-entitlement.png "推送通知权利")

### <a name="register-for-notifications-with-apns"></a>注册使用 APNS 的通知

`FinishedLaunching`中的方法**AppDelegate.cs**必须重写文件注册远程通知。 注册取决于所使用设备上的 iOS 版本。 示例应用程序中的 iOS 项目重写`FinishedLaunching`方法来调用`RegisterForRemoteNotifications`如下面的示例中所示：

```csharp
public override bool FinishedLaunching(UIApplication app, NSDictionary options)
{
    global::Xamarin.Forms.Forms.Init();
    LoadApplication(new App());

    base.FinishedLaunching(app, options);

    RegisterForRemoteNotifications();

    return true;
}

void RegisterForRemoteNotifications()
{
    // register for remote notifications based on system version
    if (UIDevice.CurrentDevice.CheckSystemVersion(10, 0))
    {
        UNUserNotificationCenter.Current.RequestAuthorization(UNAuthorizationOptions.Alert |
            UNAuthorizationOptions.Sound |
            UNAuthorizationOptions.Sound,
            (granted, error) =>
            {
                if (granted)
                    InvokeOnMainThread(UIApplication.SharedApplication.RegisterForRemoteNotifications);
            });
    }
    else if (UIDevice.CurrentDevice.CheckSystemVersion(8, 0))
    {
        var pushSettings = UIUserNotificationSettings.GetSettingsForTypes(
        UIUserNotificationType.Alert | UIUserNotificationType.Badge | UIUserNotificationType.Sound,
        new NSSet());

        UIApplication.SharedApplication.RegisterUserNotificationSettings(pushSettings);
        UIApplication.SharedApplication.RegisterForRemoteNotifications();
    }
    else
    {
        UIRemoteNotificationType notificationTypes = UIRemoteNotificationType.Alert | UIRemoteNotificationType.Badge | UIRemoteNotificationType.Sound;
        UIApplication.SharedApplication.RegisterForRemoteNotificationTypes(notificationTypes);
    }
}
```

### <a name="register-with-azure-notification-hub-and-subscribe-to-tags"></a>Azure 通知中心注册和订阅标记

当设备已成功注册的远程通知期间`FinishedLaunching`方法时，iOS 将调用`RegisteredForRemoteNotifications`方法。 应重写此方法以执行以下操作：

1. 实例化`SBNotificationHub`。
1. 取消注册任何现有注册。
1. 使用通知中心注册设备。
1. 订阅特定标记的模板。

有关注册的设备、 模板和标记的详细信息，请参阅[注册到 Azure 通知中心的模板和标记](#register-templates-and-tags-with-the-azure-notification-hub)。 下面的代码演示如何注册设备和模板：

```csharp
public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
{
    Hub = new SBNotificationHub(AppConstants.ListenConnectionString, AppConstants.NotificationHubName);

    // update registration with Azure Notification Hub
    Hub.UnregisterAllAsync(deviceToken, (error) =>
    {
        if (error != null)
        {
            Debug.WriteLine($"Unable to call unregister {error}");
            return;
        }

        var tags = new NSSet(AppConstants.SubscriptionTags.ToArray());
        Hub.RegisterNativeAsync(deviceToken, tags, (errorCallback) =>
        {
            if (errorCallback != null)
            {
                Debug.WriteLine($"RegisterNativeAsync error: {errorCallback}");
            }
        });

        var templateExpiration = DateTime.Now.AddDays(120).ToString(System.Globalization.CultureInfo.CreateSpecificCulture("en-US"));
        Hub.RegisterTemplateAsync(deviceToken, "defaultTemplate", AppConstants.APNTemplateBody, templateExpiration, tags, (errorCallback) =>
        {
            if (errorCallback != null)
            {
                if (errorCallback != null)
                {
                    Debug.WriteLine($"RegisterTemplateAsync error: {errorCallback}");
                }
            }
        });
    });
}
```

> [!NOTE]
> 注册远程通知在无网络连接等情况下可能会失败。 您可以选择重写`FailedToRegisterForRemoveNotifications`方法以处理注册失败。

### <a name="add-apns-notifications-to-xamarinforms-ui"></a>将 APNS 的通知添加到 Xamarin.Forms UI

当设备接收远程通知，iOS 调用`ReceivedRemoteNotification`方法。 JSON 转换为传入消息`NSDictionary`对象，并`ProcessNotification`方法从字典中提取值并将其发送到 Xamarin.Forms`MainPage`实例。 `ReceivedRemoteNotifications`重写方法以调用`ProcessNotification`如下面的代码中所示：

```csharp
public override void ReceivedRemoteNotification(UIApplication application, NSDictionary userInfo)
{
    ProcessNotification(userInfo, false);
}

void ProcessNotification(NSDictionary options, bool fromFinishedLaunching)
{
    // make sure we have a payload
    if (options != null && options.ContainsKey(new NSString("aps")))
    {
        // get the APS dictionary and extract message payload. Message JSON will be converted
        // into a NSDictionary so more complex payloads may require more processing
        NSDictionary aps = options.ObjectForKey(new NSString("aps")) as NSDictionary;
        string payload = string.Empty;
        NSString payloadKey = new NSString("alert");
        if (aps.ContainsKey(payloadKey))
        {
            payload = aps[payloadKey].ToString();
        }

        if (!string.IsNullOrWhiteSpace(payload))
        {
            (App.Current.MainPage as MainPage)?.AddMessage(payload);
        }

    }
    else
    {
        Debug.WriteLine($"Received request to process notification but there was no payload.");
    }
}
```

## <a name="test-notifications-in-the-azure-portal"></a>在 Azure 门户中测试通知

Azure 通知中心，你可以检查您的应用程序可以接收测试消息。 **测试发送**中通知中心部分，可选择的目标平台和发送消息。 设置**发送到标记表达式**到`default`将消息发送到已注册的模板的应用程序`default`标记。 单击**发送**按钮生成的报告包含并显示消息已达到设备数。 下面的屏幕截图显示了在 Azure 门户中的 Android 通知测试：

![Azure 通知中心测试消息的屏幕截图](azure-notification-hub-images/azure-notification-hub-test-send.png "Azure 通知中心测试消息")

### <a name="testing-tips"></a>测试的提示

1. 测试应用程序可以接收推送通知时必须使用物理设备。 Android 和 iOS 的虚拟设备可能未正确配置以接收推送通知。
1. 示例 Android 应用程序注册及其标记和模板后发出 Firebase 令牌时。 在测试期间，您可能需要请求新令牌，并重新注册到 Azure 通知中心。 强制的最佳方法是要清理你的项目，请删除`bin`和`obj`文件夹和重新生成和部署之前从设备中卸载该应用程序。
1. 以异步方式执行推送通知流中的不同部分。 这可能会导致未命中或按意外顺序命中的断点。 而不会中断应用程序流中使用跟踪执行的设备或调试日志记录。 Android 设备日志使用筛选`DebugTag`中指定`Constants`。

## <a name="create-a-notification-dispatcher"></a>创建通知调度程序

Azure 通知中心启用后端应用程序进行跨平台分派通知到设备。 此示例演示如何使用通知调度**NotificationDispatcher**控制台应用程序。 该应用程序包括**DispatcherConstants.cs**文件，它定义以下属性：

```csharp
public static class DispatcherConstants
{
    public static string[] SubscriptionTags { get; set; } = { "default" };
    public static string NotificationHubName { get; set; } = "< Insert your Azure Notification Hub name >";
    public static string FullAccessConnectionString { get; set; } = "< Insert your DefaultFullSharedAccessSignature >";
}
```

必须配置**DispatcherConstants.cs**以匹配 Azure 通知中心的配置相匹配。 值`SubscriptionTags`属性应与客户端应用程序中使用的值匹配。 `NotificationHubName`属性是 Azure 通知中心实例的名称。 `FullAccessConnectionString`属性是在你的通知中心中找到的访问密钥**访问策略**。 以下屏幕截图显示的位置`NotificationHubName`和`FullAccessConnectionString`在 Azure 门户中的属性：

![Azure 通知中心名称和 FullAccessConnectionString 的屏幕截图](azure-notification-hub-images/notification-hub-full-access-policy.png "Azure 通知中心名称和 FullAccessConnectionString")

控制台应用程序遍历每个`SubscriptionTags`值并将通知发送到订阅服务器使用的实例`NotificationHubClient`类。 下面的代码演示控制台应用程序`Program`类：

``` csharp
class Program
{
    static int messageCount = 0;

    static void Main(string[] args)
    {
        Console.WriteLine($"Press the spacebar to send a message to each tag in {string.Join(", ", DispatcherConstants.SubscriptionTags)}");
        WriteSeparator();
        while (Console.ReadKey().Key == ConsoleKey.Spacebar)
        {
            SendTemplateNotificationsAsync().GetAwaiter().GetResult();
        }
    }

    private static async Task SendTemplateNotificationsAsync()
    {
        NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(DispatcherConstants.FullAccessConnectionString, DispatcherConstants.NotificationHubName);
        Dictionary<string, string> templateParameters = new Dictionary<string, string>();

        messageCount++;

        // Send a template notification to each tag. This will go to any devices that
        // have subscribed to this tag with a template that includes "messageParam"
        // as a parameter
        foreach (var tag in DispatcherConstants.SubscriptionTags)
        {
            templateParameters["messageParam"] = $"Notification #{messageCount} to {tag} category subscribers!";
            try
            {
                await hub.SendTemplateNotificationAsync(templateParameters, tag);
                Console.WriteLine($"Sent message to {tag} subscribers.");
            }
            catch (Exception ex)
            {
                Console.WriteLine($"Failed to send template notification: {ex.Message}");
            }
        }

        Console.WriteLine($"Sent messages to {DispatcherConstants.SubscriptionTags.Length} tags.");
        WriteSeparator();
    }

    private static void WriteSeparator()
    {
        Console.WriteLine("==========================================================================");
    }
}
```

运行示例控制台应用程序时，可以按下空格键发送消息。 运行带编号的通知的客户端应用程序应接收的设备提供正确配置。

## <a name="related-links"></a>相关链接

* [推送通知模板](/azure/notification-hubs/notification-hubs-templates-cross-platform-push-messages)。
* [设备注册管理](/azure/notification-hubs/notification-hubs-push-notification-registration-management)。
* [路由和标记表达式](/azure/notification-hubs/notification-hubs-tags-segment-push-message)。
* [Xamarin.Android Azure 通知中心教程](/azure/notification-hubs/xamarin-notification-hubs-push-notifications-android-gcm)。
* [Xamarin.iOS Azure 通知中心教程](/azure/notification-hubs/xamarin-notification-hubs-ios-push-notification-apns-get-started)。
