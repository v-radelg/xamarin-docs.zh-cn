---
title: 远程通知使用 Firebase Cloud Messaging
description: 本演练介绍如何使用 Firebase Cloud Messaging 实现远程通知 （也称为推送通知） 的分步说明，Xamarin.Android 应用程序中。 它演示如何实现的各种类所需的通信使用 Firebase Cloud Messaging (FCM)，提供有关如何配置用于访问 FCM，Android 清单的示例并演示如何使用 Firebase 下游消息传递控制台。
ms.prod: xamarin
ms.assetid: 4D7C5F46-C997-49F6-AFDA-6763E68CDC90
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 07/31/2018
ms.openlocfilehash: a50a2014e28becacb2c9f4965b7f3377be57ab16
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67830322"
---
# <a name="remote-notifications-with-firebase-cloud-messaging"></a>远程通知使用 Firebase Cloud Messaging

_本演练介绍如何使用 Firebase Cloud Messaging 实现远程通知 （也称为推送通知） 的分步说明，Xamarin.Android 应用程序中。它演示如何实现的各种类所需的通信使用 Firebase Cloud Messaging (FCM)，提供有关如何配置用于访问 FCM，Android 清单的示例并演示如何使用 Firebase 下游消息传递控制台。_

## <a name="fcm-notifications-overview"></a>FCM 通知概述

在本演练中，一个基本的应用中调用**FCMClient**将创建用于说明的 FCM 消息传送基础知识。 **FCMClient**检查 Google Play 服务是否存在、 接收来自 FCM 注册令牌，显示远程用户从 Firebase 控制台中，发送的通知和订阅主题的消息：

[![示例应用的屏幕截图](remote-notifications-with-fcm-images/00-app-example-sml.png)](remote-notifications-with-fcm-images/00-app-example.png#lightbox)

将探讨下列主题领域：

1.  背景通知

2.  主题的消息

3.  前景色通知

在本演练中，您将以增量方式将功能添加到**FCMClient**并运行设备或仿真程序以了解它如何与 FCM 交互上。 将使用日志记录以实时应用事务 FCM 服务器与见证服务器，您将观察到如何从输入到 Firebase 控制台通知 GUI 的 FCM 消息生成的通知。

## <a name="requirements"></a>要求


这会有助于自己应熟悉[不同类型的消息](https://firebase.google.com/docs/cloud-messaging/concept-options#notifications_and_data_messages)，可以通过 Firebase Cloud Messaging 发送。 消息的负载将确定如何客户端应用程序将接收和处理消息。

您可以继续执行本演练之前，必须获取所需的凭据以使用 Google 的 FCM 服务器;此过程所述[Firebase Cloud Messaging](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md#setup_fcm)。
具体而言，必须下载**google-services.json**要用于此演练中介绍的示例代码文件。 如果你尚未创建一个项目在 Firebase 控制台中 (或者如果尚未下载**google-services.json**文件)，请参阅[Firebase Cloud Messaging](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md)。

若要运行示例应用程序中，将需要 Android 测试设备或仿真程序是使用 Firebase 兼容。 Firebase Cloud Messaging 支持运行 Android 4.0 或更高版本，客户端和这些设备还必须安装 Google Play 应用商店应用 （Google Play Services 9.2.1 或更高版本）。 如果你还没有在设备上安装 Google Play 应用商店应用程序，请访问[Google Play](https://support.google.com/googleplay)网站上下载并安装它。 或者，可以使用 Google Play Services 安装而不是测试设备 （您无需安装 Google Play 商店，如果你使用 Android SDK 仿真器） 使用 Android SDK 仿真器。

## <a name="start-an-app-project"></a>启动应用程序项目

若要开始，创建一个名为的新空 Xamarin.Android 项目**FCMClient**。 如果您不熟悉创建 Xamarin.Android 项目，请参阅[Hello，Android](~/android/get-started/hello-android/hello-android-quickstart.md)。
创建新应用后下, 一步是设置包名称，并安装将用于与 FCM 进行通信的多个 NuGet 包。

### <a name="set-the-package-name"></a>设置包名称

在中[Firebase Cloud Messaging](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md)，指定 FCM 启用应用的包名称。 此包名称也可作为[*应用程序 ID* ](./firebase-cloud-messaging.md#fcm-in-action-app-id)关联[API 密钥](firebase-cloud-messaging.md#fcm-in-action-api-key)。 将应用配置为使用此包名称：

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1.  打开的属性**FCMClient**项目。

2.  在中**Android 清单**页上，将包名称。

在以下示例中，包名称设置为`com.xamarin.fcmexample`:

[![设置包名称](remote-notifications-with-fcm-images/01-package-name-vs-sml.png)](remote-notifications-with-fcm-images/01-package-name-vs.png#lightbox)

更新时**Android 清单**，还检查，以确保`Internet`启用权限。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

1.  打开的属性**FCMClient**项目。

2.  在中**Android 应用程序**页上，将包名称。

在以下示例中，包名称设置为`com.xamarin.fcmexample`:

[![设置包名称](remote-notifications-with-fcm-images/01-package-name-xs-sml.png)](remote-notifications-with-fcm-images/01-package-name-xs.png#lightbox)

更新时**Android 清单**，还检查，以确保`INTERNET`启用权限 (在**所需的权限**)。

-----

> [!IMPORTANT]
> 客户端应用程序将不接收来自 FCM 注册令牌，如果此包名称不能*完全*与已输入到 Firebase 控制台中的包名称匹配。

### <a name="add-the-xamarin-google-play-services-base-package"></a>添加 Xamarin Google Play 服务基本包

Firebase Cloud Messaging 取决于 Google Play 服务，因为[Xamarin Google Play 服务的基本](https://www.nuget.org/packages/Xamarin.GooglePlayServices.Base/)必须将 NuGet 包添加到 Xamarin.Android 项目。 需要版本 29.0.0.2 或更高版本。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1.  在 Visual Studio 中，右键单击**引用 > 管理 NuGet 包...** .

2.  单击**浏览**选项卡并搜索**Xamarin.GooglePlayServices.Base**。

3.  安装到此包**FCMClient**项目：

    [![安装 Google Play 服务基础](remote-notifications-with-fcm-images/02-google-play-services-vs-sml.png)](remote-notifications-with-fcm-images/02-google-play-services-vs.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

1.  在 Visual Studio for Mac 中，右键单击**包 > 添加包...** .

2.  搜索**Xamarin.GooglePlayServices.Base**。

3.  安装到此包**FCMClient**项目：

    [![安装 Google Play 服务基础](remote-notifications-with-fcm-images/02-google-play-services-xs-sml.png)](remote-notifications-with-fcm-images/02-google-play-services-xs.png#lightbox)

-----

如果在安装 NuGet 的过程中遇到错误，关闭**FCMClient**项目，重新打开，然后重试 NuGet 安装。

当你安装**Xamarin.GooglePlayServices.Base**，还安装所有必要的依赖项。 编辑**MainActivity.cs**并添加以下`using`语句：

```csharp
using Android.Gms.Common;
```

此语句使`GoogleApiAvailability`类中**Xamarin.GooglePlayServices.Base**供**FCMClient**代码。
`GoogleApiAvailability` 用于检查存在的 Google Play 服务。

### <a name="add-the-xamarin-firebase-messaging-package"></a>添加 Xamarin Firebase Messaging 包

若要从 FCM，接收消息[Xamarin Firebase-消息传送](https://www.nuget.org/packages/Xamarin.Firebase.Messaging/)必须将 NuGet 包添加到应用程序项目。 无此包的 Android 应用程序无法接收来自 FCM 服务器的消息。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1.  在 Visual Studio 中，右键单击**引用 > 管理 NuGet 包...** .

2. 搜索**Xamarin.Firebase.Messaging**。

3.  安装到此包**FCMClient**项目：

    [![安装 Xamarin Firebase 消息传送](remote-notifications-with-fcm-images/03-firebase-messaging-vs-sml.png)](remote-notifications-with-fcm-images/03-firebase-messaging-vs.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

1.  在 Visual Studio for Mac 中，右键单击**包 > 添加包...** .

2.  搜索**Xamarin.Firebase.Messaging**。

3.  安装到此包**FCMClient**项目：

    [![安装 Xamarin Firebase 消息传送](remote-notifications-with-fcm-images/03-firebase-messaging-xs-sml.png)](remote-notifications-with-fcm-images/03-firebase-messaging-xs.png#lightbox)

-----

当你安装**Xamarin.Firebase.Messaging**，还安装所有必要的依赖项。

接下来，编辑**MainActivity.cs**并添加以下`using`语句：

```csharp
using Firebase.Messaging;
using Firebase.Iid;
using Android.Util;
```

前两个语句进行中的类型**Xamarin.Firebase.Messaging** NuGet 包可供**FCMClient**代码。 **Android.Util**添加将用于观察 FMS 的事务的日志记录功能。

### <a name="add-googleplayservices-json"></a>添加 Google 服务 JSON 文件

下一步是添加**google-services.json**到你的项目的根目录的文件：

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1.  复制**google-services.json**到项目文件夹。

2.  添加**google-services.json**向应用程序项目 (单击**显示所有文件**中**解决方案资源管理器**，右键单击**google-services.json**，然后选择**包括在项目**)。

3.  选择**google-services.json**中**解决方案资源管理器**窗口。

4.  在中**属性**窗格中，设置**生成操作**到**GoogleServicesJson**:

    [![将生成操作设置为 GoogleServicesJson](remote-notifications-with-fcm-images/04-google-services-json-vs-sml.png)](remote-notifications-with-fcm-images/04-google-services-json-vs.png#lightbox)

    > [!NOTE] 
    > 如果**GoogleServicesJson**生成操作不会显示，保存并关闭解决方案，然后重新打开它。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

1.  复制**google-services.json**到项目文件夹。

2.  添加**google-services.json**到应用程序项目。

3.  右键单击**google-services.json**。

4.  设置**生成操作**到**GoogleServicesJson**:

    [![将生成操作设置为 GoogleServicesJson](remote-notifications-with-fcm-images/04-google-services-json-xs-sml.png)](remote-notifications-with-fcm-images/04-google-services-json-xs.png#lightbox)

-----

当**google-services.json**添加到项目 (并**GoogleServicesJson**生成操作设置)，生成过程中提取客户端 ID 和[API 密钥](./firebase-cloud-messaging.md#fcm-in-action-api-key)，然后将这些凭据添加到合并/生成**AndroidManifest.xml**驻留在**obj/Debug/android/AndroidManifest.xml**。 此合并进程会自动添加的任何权限和其他 FCM 元素所需的 FCM 服务器的连接。


## <a name="check-for-google-play-services-and-create-a-notification-channel"></a>检查 Google Play Services 并创建通知通道

Google 建议访问 Google Play Services 功能之前的 Android 应用检查是否存在的 Google Play Services APK (有关详细信息，请参阅[检查 Google Play services](https://firebase.google.com/docs/cloud-messaging/android/client#sample-play))。

将首先创建应用程序的 UI 的初始布局。 编辑**Resources/layout/Main.axml**并将其内容替换为以下 XML:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:padding="10dp">
    <TextView
        android:text=" "
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:id="@+id/msgText"
        android:textAppearance="?android:attr/textAppearanceMedium"
        android:padding="10dp" />
</LinearLayout>
```

这`TextView`将用于显示指明是否已安装 Google Play 服务的消息。 保存对更改**Main.axml**。


编辑**MainActivity.cs**并添加以下实例变量添加到`MainActivity`类：

```csharp
public class MainActivity : AppCompatActivity
{
    static readonly string TAG = "MainActivity";

    internal static readonly string CHANNEL_ID = "my_notification_channel";
    internal static readonly int NOTIFICATION_ID = 100;

    TextView msgText;
```

变量`CHANNEL_ID`并`NOTIFICATION_ID`将在方法中使用[ `CreateNotificationChannel` ](#create-notification-channel-code) ，将添加到`MainActivity`稍后在本演练中。


在以下示例中，`OnCreate`方法将验证 Google Play Services 之前是应用程序尝试使用 FCM 服务是否可用。
添加以下方法`MainActivity`类：

```csharp
public bool IsPlayServicesAvailable ()
{
    int resultCode = GoogleApiAvailability.Instance.IsGooglePlayServicesAvailable (this);
    if (resultCode != ConnectionResult.Success)
    {
        if (GoogleApiAvailability.Instance.IsUserResolvableError (resultCode))
            msgText.Text = GoogleApiAvailability.Instance.GetErrorString (resultCode);
        else
        {
            msgText.Text = "This device is not supported";
            Finish ();
        }
        return false;
    }
    else
    {
        msgText.Text = "Google Play Services is available.";
        return true;
    }
}
```

此代码检查设备以查看是否已安装 Google Play Services APK。 如果未安装，在显示一条消息`TextBox`，指示用户可以从 Google Play 商店下载 APK （或设备的系统设置中启用它）。

<a name="create-notification-channel-code"></a>Android 8.0 （API 级别 26） 或更高版本运行的应用程序必须创建[_通知通道_](~/android/app-fundamentals/notifications/local-notifications.md)发布其通知。  添加以下方法`MainActivity`类将创建通知通道 （如有必要）：

```csharp
void CreateNotificationChannel()
{
    if (Build.VERSION.SdkInt < BuildVersionCodes.O)
    {
        // Notification channels are new in API 26 (and not a part of the
        // support library). There is no need to create a notification
        // channel on older versions of Android.
        return;
    }

    var channel = new NotificationChannel(CHANNEL_ID,
                                          "FCM Notifications",
                                          NotificationImportance.Default)
                  {

                      Description = "Firebase Cloud Messages appear in this channel"
                  };

    var notificationManager = (NotificationManager)GetSystemService(Android.Content.Context.NotificationService);
    notificationManager.CreateNotificationChannel(channel);
}
```

将 `OnCreate` 方法替换为以下代码：

```csharp
protected override void OnCreate (Bundle bundle)
{
    base.OnCreate (bundle);
    SetContentView (Resource.Layout.Main);
    msgText = FindViewById<TextView> (Resource.Id.msgText);

    IsPlayServicesAvailable ();

    CreateNotificationChannel();
}
```

`IsPlayServicesAvailable` 结束时调用`OnCreate`，以便 Google Play Services 检查运行每次启动应用。 该方法`CreateNotificationChannel`被调用，以确保通知通道存在于运行 Android 8 的设备或更高版本。 如果你的应用程序`OnResume`方法，则应调用`IsPlayServicesAvailable`从`OnResume`也。 完全重新生成并运行应用程序。 如果所有配置正确，应看到一个屏幕，如以下屏幕截图所示：

[![应用指示 Google Play 服务可用](remote-notifications-with-fcm-images/05-gps-available-sml.png)](remote-notifications-with-fcm-images/05-gps-available.png#lightbox)

如果没有获得此结果，请验证你的设备上是否安装了 Google Play Services APK (有关详细信息，请参阅[设置 Google Play 服务](https://developers.google.com/android/guides/setup))。
此外验证是否已添加**Xamarin.Google.Play.Services.Base**打包到你**FCMClient**项目，如前面所述。


## <a name="add-the-instance-id-receiver"></a>添加实例 ID 接收器

下一步是添加一项服务，扩展了`FirebaseInstanceIdService`来处理旋转、 创建和更新[Firebase 注册令牌](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md#fcm-in-action-registration-token)。 `FirebaseInstanceIdService`所必需的 FCM，以便能够将消息发送到设备服务。 当`FirebaseInstanceIdService`服务添加到客户端应用程序中，应用将自动接收 FCM 消息并将其显示为通知，每当应用程序在后台运行。

### <a name="declare-the-receiver-in-the-android-manifest"></a>声明在 Android 清单中的接收方

编辑**AndroidManifest.xml** ，并插入以下`<receiver>`元素插入`<application>`部分：

```xml
<receiver
    android:name="com.google.firebase.iid.FirebaseInstanceIdInternalReceiver"
    android:exported="false" />
<receiver
    android:name="com.google.firebase.iid.FirebaseInstanceIdReceiver"
    android:exported="true"
    android:permission="com.google.android.c2dm.permission.SEND">
    <intent-filter>
        <action android:name="com.google.android.c2dm.intent.RECEIVE" />
        <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
        <category android:name="${applicationId}" />
    </intent-filter>
</receiver>
```

此 XML 将执行以下操作：

-   声明`FirebaseInstanceIdReceiver`实现，提供[唯一标识符](https://developers.google.com/instance-id/)针对每个应用实例。 此接收器还进行身份验证和授权操作。

-   声明的内部`FirebaseInstanceIdInternalReceiver`用于安全地启动服务的实现。

-   [应用程序 ID](./firebase-cloud-messaging.md#fcm-in-action-app-id)存储在**google-services.json**已[添加到项目](#add-googleplayservices-json)。 Xamarin.Android Firebase 绑定将替换令牌`${applicationId}`应用 id 中; 无需其他代码的客户端应用提供应用程序 id。

`FirebaseInstanceIdReceiver`是`WakefulBroadcastReceiver`接收`FirebaseInstanceId`并`FirebaseMessaging`事件并将其传递到从派生类`FirebaseInstanceIdService`。

### <a name="implement-the-firebase-instance-id-service"></a>实现 Firebase 实例 ID 服务

由自定义处理向 FCM 注册应用程序的工作`FirebaseInstanceIdService`提供服务。
`FirebaseInstanceIdService` 执行以下步骤：

1.  使用[实例 ID API](https://developers.google.com/android/reference/com/google/android/gms/iid/InstanceID)生成授权客户端应用程序访问 FCM 和应用程序服务器的安全令牌。 反过来，应用将获取[注册令牌](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md#fcm-in-action-registration-token)来自 FCM。

2.  如果应用程序服务器有此要求，将转发到应用服务器的注册令牌。

添加名为的新文件**MyFirebaseIIDService.cs**和其模板代码替换为以下代码：

```csharp
using System;
using Android.App;
using Firebase.Iid;
using Android.Util;

namespace FCMClient
{
    [Service]
    [IntentFilter(new[] { "com.google.firebase.INSTANCE_ID_EVENT" })]
    public class MyFirebaseIIDService : FirebaseInstanceIdService
    {
        const string TAG = "MyFirebaseIIDService";
        public override void OnTokenRefresh()
        {
            var refreshedToken = FirebaseInstanceId.Instance.Token;
            Log.Debug(TAG, "Refreshed token: " + refreshedToken);
            SendRegistrationToServer(refreshedToken);
        }
        void SendRegistrationToServer(string token)
        {
            // Add custom implementation, as needed.
        }
    }
}
```

此服务实现`OnTokenRefresh`注册令牌最初创建或更改时调用的方法。 当`OnTokenRefresh`运行时，它检索的最新令牌从`FirebaseInstanceId.Instance.Token`属性 （它通过 FCM 以异步方式更新）。 在此示例中，以便可以在输出窗口中查看该记录的刷新的令牌：

```csharp
var refreshedToken = FirebaseInstanceId.Instance.Token;
Log.Debug(TAG, "Refreshed token: " + refreshedToken);
```

`OnTokenRefresh` 不经常调用： 它用于更新在下列情况下的令牌：

-   安装或卸载应用程序时。

-   当用户删除应用程序数据。

-   应用程序清除实例 id。

-   当令牌的安全已遭到破坏。

根据 Google[实例 ID](https://developers.google.com/instance-id/guides/android-implementation)文档，FCM 实例 ID 服务将请求该应用程序刷新其令牌定期 （通常情况下，每 6 个月）。

`OnTokenRefresh` 此外会调用`SendRegistrationToAppServer`关联用户的注册令牌与服务器端的帐户 （如果有），由应用程序维护：

```csharp
void SendRegistrationToAppServer (string token)
{
    // Add custom implementation here as needed.
}
```

因为此实现取决于应用程序服务器的设计，在此示例中提供空的方法体。 如果你的应用程序服务器要求 FCM 注册信息，修改`SendRegistrationToAppServer`若要将用户的 FCM 实例 ID 令牌由您的应用程序维护任何服务器端的帐户相关联。 （请注意，该令牌是不透明的客户端应用程序。）

在将令牌发送到应用服务器`SendRegistrationToAppServer`应保持指示令牌是否已发送到服务器的布尔值。 如果此布尔值为 false，`SendRegistrationToAppServer`将该令牌发送到应用服务器&ndash;否则，该令牌已发送到上一次调用中的应用程序服务器。 在某些情况下 (如这`FCMClient`示例)，应用程序服务器不需要令牌; 因此，此方法不是此示例所必需的。

## <a name="implement-client-app-code"></a>实现客户端应用程序代码

现在，接收方服务位于适当位置，可以编写客户端应用程序代码以利用这些服务。 在以下部分中，一个按钮添加到 UI，以记录注册令牌 (也称为*实例 ID 令牌*)，并且更多的代码添加到`MainActivity`若要查看`Intent`时从启动应用程序的信息通知：

[![日志标记按钮添加到应用屏幕](remote-notifications-with-fcm-images/06-log-token-sml.png)](remote-notifications-with-fcm-images/06-log-token.png#lightbox)

### <a name="log-tokens"></a>日志标记

在此步骤中添加的代码仅供演示之&ndash;生产客户端应用程序必须无需记录注册令牌。 编辑**Resources/layout/Main.axml**并添加以下`Button`声明后立即`TextView`元素：

```xml
<Button
  android:id="@+id/logTokenButton"
  android:layout_width="match_parent"
  android:layout_height="wrap_content"
  android:layout_gravity="center_horizontal"
  android:text="Log Token" />
```

在 `MainActivity.OnCreate` 方法的末尾添加以下代码：

```csharp
var logTokenButton = FindViewById<Button>(Resource.Id.logTokenButton);
logTokenButton.Click += delegate {
    Log.Debug(TAG, "InstanceID token: " + FirebaseInstanceId.Instance.Token);
};
```

此代码会在当前令牌记录到输出窗口时**日志令牌**点击按钮。

### <a name="handle-notification-intents"></a>处理通知意向

当用户点击通知从颁发**FCMClient**随附该通知的任何数据、 消息可在`Intent`其他功能。 编辑**MainActivity.cs**并将以下代码添加到顶部`OnCreate`方法 (在对调用之前`IsPlayServicesAvailable`):

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

应用程序的启动器`Intent`当用户点击其通知消息，因此此代码将记录中的任何随附的数据时触发`Intent`到输出窗口。 具有不同`Intent`必须在激发`click_action`通知消息的字段必须设置为该`Intent`(启动器`Intent`时不使用`click_action`指定)。


## <a name="background-notifications"></a>背景通知

生成并运行**FCMClient**应用。 **日志令牌**显示按钮：

[![显示日志令牌按钮](remote-notifications-with-fcm-images/06-log-token-sml.png)](remote-notifications-with-fcm-images/06-log-token.png#lightbox)

点击**日志令牌**按钮。 IDE 输出窗口中，应显示如下所示的消息：

[![实例 ID 令牌显示在输出窗口](remote-notifications-with-fcm-images/07-token-received-sml.png)](remote-notifications-with-fcm-images/07-token-received.png#lightbox)

带标记的一长串**令牌**是实例 ID 令牌，然后将其粘贴到 Firebase 控制台&ndash;选择，然后将此字符串复制到剪贴板。 如果看不到的实例 ID 令牌，将以下行添加到顶部`OnCreate`方法以验证**google-services.json**已正确分析：

```csharp
Log.Debug(TAG, "google app id: " + GetString(Resource.String.google_app_id));
```

`google_app_id`记录到输出窗口的值应与匹配`mobilesdk_app_id`中记录的值**google-services.json**。

### <a name="send-a-message"></a>发送一条消息

登录到[Firebase 控制台](https://console.firebase.google.com)，选择你的项目，单击**通知**，然后单击**发送第一条消息**:

[![发送你的第一条消息按钮](remote-notifications-with-fcm-images/08-first-notification-sml.png)](remote-notifications-with-fcm-images/08-first-notification.png#lightbox)

上**Compose 消息**页上，输入消息文本，然后选择**单个设备**。 从 IDE 输出窗口复制的实例 ID 令牌并将其粘贴到**FCM 注册令牌**Firebase 控制台中的字段：

[![编写消息对话框](remote-notifications-with-fcm-images/09-compose-message-sml.png)](remote-notifications-with-fcm-images/09-compose-message.png#lightbox)

Android 设备 （或模拟器） 上，通过点击 Android 后台应用程序**概述**按钮和触摸主屏幕。 在设备准备就绪后，单击**发送消息**Firebase 控制台中：

[![发送消息按钮](remote-notifications-with-fcm-images/10-send-message-sml.png)](remote-notifications-with-fcm-images/10-send-message.png#lightbox)

当**评审消息**显示对话框中，单击**发送**。
通知图标应出现在通知区域中的设备 （或仿真器）：

[![显示通知图标](remote-notifications-with-fcm-images/11-notification-icon-sml.png)](remote-notifications-with-fcm-images/11-notification-icon.png#lightbox)

打开通知图标以查看该消息。 通知消息应为完全什么已键入**消息正文**Firebase 控制台中的字段：

[![在设备上显示通知消息](remote-notifications-with-fcm-images/12-notification-sml.png)](remote-notifications-with-fcm-images/12-notification.png#lightbox)

点击该通知图标即可启动**FCMClient**应用。 `Intent`发送到 extras **FCMClient** IDE 输出窗口中列出：

[![意向 extras 列出了从键、 消息 ID 和折叠键](remote-notifications-with-fcm-images/13-intent-extras-sml.png)](remote-notifications-with-fcm-images/13-intent-extras.png#lightbox)

在此示例中，**从**键设置为应用程序的 Firebase 项目编号 (在此示例中， `41590732`)，和**collapse_key**设置为其包名称 (**com.xamarin.fcmexample**)。
如果您不会收到一条消息，请尝试删除**FCMClient**设备 （或仿真器） 上的应用，然后重复上述步骤。


> [!NOTE]
> 如果强制关闭应用，FCM 将停止将通知传递。 Android 可阻止后台服务广播无意中或不必要地启动已停止的应用程序的组件。 (有关此行为的详细信息，请参阅[启动已停止的应用程序上的控件](https://developer.android.com/about/versions/android-3.1.html#launchcontrols)。)出于此原因，则务必手动卸载该应用每次运行它，并在调试会话中停止&ndash;这会强制 FCM，生成新令牌，以便将继续接收消息。

### <a name="add-a-custom-default-notification-icon"></a>添加自定义默认通知图标

在上一示例中，通知图标设置为应用程序图标。 下面的 XML 配置通知的自定义的默认图标。 Android 显示所有通知消息，其中的通知图标未显式设置此自定义的默认图标。

若要添加自定义默认通知图标，添加到应用图标**资源/drawable**目录中，编辑**AndroidManifest.xml**，并插入以下`<meta-data>`元素插入`<application>`部分：

```xml
<meta-data
    android:name="com.google.firebase.messaging.default_notification_icon"
    android:resource="@drawable/ic_stat_ic_notification" />
```

在此示例中，通知图标，位于**资源/drawable/ic\_stat\_ic\_notification.png**将用作自定义默认通知图标。 如果未在中配置自定义的默认图标**AndroidManifest.xml**和无图标设置通知有效负载中，Android 作为通知图标 （如上面的通知图标屏幕截图中所示） 将使用应用程序图标。

## <a name="handle-topic-messages"></a>处理主题的消息

到目前为止编写的代码处理注册令牌，并将远程通知功能添加到应用程序。 下一步的示例将添加代码，可侦听*主题消息*并将其转发到该用户为远程通知。 主题的消息是发送到某个特定主题订阅的一个或多个设备的 FCM 消息。 有关主题的消息的详细信息，请参阅[消息传递主题](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md)。

### <a name="subscribe-to-a-topic"></a>订阅主题

编辑**Resources/layout/Main.axml**并添加以下`Button`紧前面的声明`Button`元素：

```xml
<Button
  android:id="@+id/subscribeButton"
  android:layout_width="match_parent"
  android:layout_height="wrap_content"
  android:layout_gravity="center_horizontal"
  android:layout_marginTop="20dp"
  android:text="Subscribe to Notifications" />
```

此 XML 将添加**通知订阅**到布局的按钮。
编辑**MainActivity.cs**并将以下代码添加到末尾`OnCreate`方法：

```csharp
var subscribeButton = FindViewById<Button>(Resource.Id.subscribeButton);
subscribeButton.Click += delegate {
    FirebaseMessaging.Instance.SubscribeToTopic("news");
    Log.Debug(TAG, "Subscribed to remote notifications");
};
```

此代码定位**通知订阅**布局中的按钮并将其 click 处理程序分配给调用的代码`FirebaseMessaging.Instance.SubscribeToTopic`，并在已订阅的主题中，传递_新闻_。 当用户点击**Subscribe**按钮，该应用程序订阅_新闻_主题。 在以下部分中，_新闻_将从 Firebase 控制台通知 GUI 发送主题消息。

### <a name="send-a-topic-message"></a>将发送主题消息

卸载应用、 重新生成，并再次运行。 单击**订阅通知**按钮：

[![订阅通知按钮](remote-notifications-with-fcm-images/14-subscribe-sml.png)](remote-notifications-with-fcm-images/14-subscribe.png#lightbox)

如果已成功订阅了该应用程序后，应会看到**主题同步成功**在 IDE 中输出窗口：

[![输出窗口会显示主题同步成功消息](remote-notifications-with-fcm-images/15-topic-sync-sml.png)](remote-notifications-with-fcm-images/15-topic-sync.png#lightbox)

使用以下步骤将发送主题消息：

1.  在 Firebase 控制台中，单击**新消息**。

2.  上**Compose 消息**页上，输入消息文本，然后选择**主题**。

3.  在中**主题**下拉菜单，选择内置主题**新闻**:

    [![选择的新闻主题](remote-notifications-with-fcm-images/16-topic-message-sml.png)](remote-notifications-with-fcm-images/16-topic-message.png#lightbox)

4.  Android 设备 （或模拟器） 上，通过点击 Android 后台应用程序**概述**按钮和触摸主屏幕。

5.  在设备准备就绪后，单击**发送消息**Firebase 控制台中。

6.  检查 IDE 输出窗口以查看 **/主题/新闻**的日志输出：

    [![显示从 /topic/news 的消息](remote-notifications-with-fcm-images/17-message-arrived-sml.png)](remote-notifications-with-fcm-images/17-message-arrived.png#lightbox)

在输出窗口中会出现此消息，通知图标应也出现在 Android 设备上的通知区域中。 打开通知图标以查看该主题的消息：

[![主题消息显示为一个通知](remote-notifications-with-fcm-images/18-other-news-sml.png)](remote-notifications-with-fcm-images/18-other-news.png#lightbox)

如果您不会收到一条消息，请尝试删除**FCMClient**设备 （或仿真器） 上的应用，然后重复上述步骤。

## <a name="foreground-notifications"></a>前景色通知

若要在 foregrounded 应用中接收通知，必须实现`FirebaseMessagingService`。 此服务，还需要用于接收数据负载和发送上游消息。 下面的示例演示了如何以实现扩展的服务`FirebaseMessagingService`&ndash;生成的应用将能够在前台运行时处理远程通知。

### <a name="implement-firebasemessagingservice"></a>实现 FirebaseMessagingService

`FirebaseMessagingService`服务负责接收和处理从 Firebase 消息。 每个应用必须子类，此类型和重写`OnMessageReceived`以处理传入的消息。 当应用位于前台，`OnMessageReceived`回调将始终处理该消息。

> [!NOTE]
> 应用程序只能有 10 秒，用来处理传入 Firebase 云消息。 所花时间超过这应计划使用一个库，类似于后台执行任何工作[Android 作业计划程序](~/android/platform/android-job-scheduler.md)或[Firebase 作业调度程序](~/android/platform/firebase-job-dispatcher.md)。

添加名为的新文件**MyFirebaseMessagingService.cs**和其模板代码替换为以下代码：

```csharp
using System;
using Android.App;
using Android.Content;
using Android.Media;
using Android.Util;
using Firebase.Messaging;

namespace FCMClient
{
    [Service]
    [IntentFilter(new[] { "com.google.firebase.MESSAGING_EVENT" })]
    public class MyFirebaseMessagingService : FirebaseMessagingService
    {
        const string TAG = "MyFirebaseMsgService";
        public override void OnMessageReceived(RemoteMessage message)
        {
            Log.Debug(TAG, "From: " + message.From);
            Log.Debug(TAG, "Notification Message Body: " + message.GetNotification().Body);
        }
    }
}
```

请注意，`MESSAGING_EVENT`必须声明意向筛选器，以便新 FCM 消息定向到`MyFirebaseMessagingService`:

```csharp
[IntentFilter(new[] { "com.google.firebase.MESSAGING_EVENT" })]
```

当客户端应用程序收到来自 FCM，一条消息时`OnMessageReceived`提取从传入的消息内容`RemoteMessage`对象通过调用其`GetNotification`方法。 接下来，以便可以在 IDE 输出窗口中查看该日志的消息内容：

```csharp
var body = message.GetNotification().Body;
Log.Debug(TAG, "Notification Message Body: " + body);
```

> [!NOTE]
> 如果在中设置断点`FirebaseMessagingService`、 你在调试会话可能或不可能由于 FCM 消息的传递会命中这些断点。


### <a name="send-another-message"></a>发送另一条消息

卸载该应用、 重新生成，它再次运行，并请执行以下步骤将发送另一条消息：

1.  在 Firebase 控制台中，单击**新消息**。

2.  上**Compose 消息**页上，输入消息文本，然后选择**单个设备**。

3.  从 IDE 输出窗口复制的标记字符串并将其粘贴到**FCM 注册令牌**Firebase 控制台与以前的字段。

4.  请确保在应用运行在前台，然后单击**发送消息**Firebase 控制台中：

    [![从控制台发送另一条消息](remote-notifications-with-fcm-images/19-hello-again-sml.png)](remote-notifications-with-fcm-images/19-hello-again.png#lightbox)

5.  当**评审消息**显示对话框中，单击**发送**。

6.  传入的消息记录到 IDE 输出窗口中：

    [![消息正文显示到输出窗口](remote-notifications-with-fcm-images/20-logged-message.png)](remote-notifications-with-fcm-images/20-logged-message.png#lightbox)


### <a name="add-a-local-notification-sender"></a>添加本地通知发件人

在剩余的示例中，传入的 FCM 消息将转换为本地应用程序在前台运行时启动的通知。 编辑**MyFirebaseMessageService.cs**并添加以下`using`语句：

```csharp
using FCMClient;
using System.Collections.Generic;
```

添加以下方法`MyFirebaseMessagingService`:

<a name="sendnotification-method"></a>

```csharp
void SendNotification(string messageBody, IDictionary<string, string> data)
{
    var intent = new Intent(this, typeof(MainActivity));
    intent.AddFlags(ActivityFlags.ClearTop);
    foreach (var key in data.Keys)
    {
        intent.PutExtra(key, data[key]);
    }

    var pendingIntent = PendingIntent.GetActivity(this,
                                                  MainActivity.NOTIFICATION_ID,
                                                  intent,
                                                  PendingIntentFlags.OneShot);

    var notificationBuilder = new  NotificationCompat.Builder(this, MainActivity.CHANNEL_ID)
                              .SetSmallIcon(Resource.Drawable.ic_stat_ic_notification)
                              .SetContentTitle("FCM Message")
                              .SetContentText(messageBody)
                              .SetAutoCancel(true)
                              .SetContentIntent(pendingIntent);

    var notificationManager = NotificationManagerCompat.From(this);
    notificationManager.Notify(MainActivity.NOTIFICATION_ID, notificationBuilder.Build());
}
```

为了区分从后台通知此通知，此代码将标记通知图标不同于应用程序图标。 将文件添加[ic\_stat\_ic\_notification.png](remote-notifications-with-fcm-images/ic-stat-ic-notification.png)到**资源/drawable**并将其包含在**FCMClient**项目.

`SendNotification`方法使用`NotificationCompat.Builder`若要创建通知，和`NotificationManagerCompat`用于启动该通知。 通知持有`PendingIntent`，将允许用户打开应用并查看传入的字符串的内容`messageBody`。 有关详细信息`NotificationCompat.Builder`，请参阅[本地通知](~/android/app-fundamentals/notifications/local-notifications.md)。

调用`SendNotification`方法末尾处`OnMessageReceived`方法：

```csharp
public override void OnMessageReceived(RemoteMessage message)
{
    Log.Debug(TAG, "From: " + message.From);

    var body = message.GetNotification().Body;
    Log.Debug(TAG, "Notification Message Body: " + body);
    SendNotification(body, message.Data);
}
```

这些更改会导致`SendNotification`将运行该应用位于前台，而该通知会在通知区域中收到通知。

应用时在后台[消息的负载](https://firebase.google.com/docs/cloud-messaging/concept-options#notifications_and_data_messages)将确定如何处理该消息：

* **通知**&ndash;消息将发送到**系统托盘**。 将其中显示本地通知。 当用户点击通知时将启动该应用程序。
* **数据**&ndash;消息将由处理`OnMessageReceived`。
* **这两**&ndash;将直接发送到系统托盘中的通知和数据负载的消息。 数据有效负载应用启动时，将出现在`Extras`的`Intent`用于启动应用程序。

在此示例中，如果应用在后台运行`SendNotification`如果消息具有数据负载将运行。 否则，将启动后台通知 （在本演练前面所示）。

### <a name="send-the-last-message"></a>发送最后一条消息

卸载应用程序，重新生成它，它再次运行，然后使用以下步骤将发送最后一条消息：

1.  在 Firebase 控制台中，单击**新消息**。

2.  上**Compose 消息**页上，输入消息文本，然后选择**单个设备**。

3.  从 IDE 输出窗口复制的标记字符串并将其粘贴到**FCM 注册令牌**Firebase 控制台与以前的字段。

4.  请确保在应用运行在前台，然后单击**发送消息**Firebase 控制台中：

    [![发送前景色消息](remote-notifications-with-fcm-images/21-console-fg-msg-sml.png)](remote-notifications-with-fcm-images/21-console-fg-msg.png#lightbox)

这一次，在输出窗口中记录了该消息还打包在一个新的通知&ndash;通知图标出现在通知栏中，应用程序在前台运行时：

[![前景消息的通知图标](remote-notifications-with-fcm-images/22-foreground-icon-sml.png)](remote-notifications-with-fcm-images/22-foreground-icon.png#lightbox)

当您打开的通知时，应看到从 Firebase 控制台通知 GUI 发送最后一条消息：

[![前景色通知会显示前景色图标](remote-notifications-with-fcm-images/23-foreground-msg-sml.png)](remote-notifications-with-fcm-images/23-foreground-msg.png#lightbox)


## <a name="disconnecting-from-fcm"></a>来自 FCM 断开连接

若要取消订阅从主题，请调用[UnsubscribeFromTopic](https://firebase.google.com/docs/reference/android/com/google/firebase/messaging/FirebaseMessaging.html#unsubscribeFromTopic%28java.lang.String%29)方法[FirebaseMessaging](https://firebase.google.com/docs/reference/android/com/google/firebase/messaging/FirebaseMessaging)类。 例如，若要取消订阅_新闻_主题订阅更早版本， **Unsubscribe**按钮可以添加到具有以下处理程序代码的布局：

```csharp
var unSubscribeButton = FindViewById<Button>(Resource.Id.unsubscribeButton);
unSubscribeButton.Click += delegate {
    FirebaseMessaging.Instance.UnsubscribeFromTopic("news");
    Log.Debug(TAG, "Unsubscribed from remote notifications");
};
```

若要取消注册从 FCM 完全设备，删除的实例 ID，通过调用[DeleteInstanceId](https://firebase.google.com/docs/reference/android/com/google/firebase/iid/FirebaseInstanceId.html#deleteInstanceId%28%29)方法[FirebaseInstanceId](https://firebase.google.com/docs/reference/android/com/google/firebase/iid/FirebaseInstanceId)类。 例如:

```csharp
FirebaseInstanceId.Instance.DeleteInstanceId();
```

此方法调用将删除实例 ID 和与之关联的数据。 因此，将停止定期发送 FCM 数据复制到设备。


## <a name="troubleshooting"></a>疑难解答

以下描述问题和解决方法使用 Xamarin.Android 使用 Firebase Cloud Messaging 时可能出现。

### <a name="firebaseapp-is-not-initialized"></a>未初始化 FirebaseApp

在某些情况下，可能会看到此错误消息：

```shell
Java.Lang.IllegalStateException: Default FirebaseApp is not initialized in this process
Make sure to call FirebaseApp.initializeApp(Context) first.
```

这是一个可以通过清除解决方案并重新生成项目解决的已知的问题 (**生成 > 清理解决方案**，**生成 > 重新生成解决方案**)。

## <a name="summary"></a>总结

本演练详细 Xamarin.Android 应用程序中实现 Firebase Cloud Messaging 远程通知的步骤。 介绍了如何安装所需的包所需的 FCM 的通信，并介绍了如何配置 Android 清单的 FCM 服务器访问。 它提供了示例代码，说明了如何检查存在的 Google Play 服务。 它演示了如何实现与 FCM 注册令牌，协商实例 ID 侦听器服务，并且它说明了此代码将应用程序在后台运行时如何创建背景通知。 本文介绍了如何订阅主题消息，并提供了用于接收和应用程序在前台运行时显示远程通知的消息侦听器服务的实现示例。


## <a name="related-links"></a>相关链接

- [FCMNotifications （示例）](https://developer.xamarin.com/samples/monodroid/Firebase/FCMNotifications)
- [Firebase 云消息传送](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md)
- [关于 FCM 消息](https://firebase.google.com/docs/cloud-messaging/concept-options)
