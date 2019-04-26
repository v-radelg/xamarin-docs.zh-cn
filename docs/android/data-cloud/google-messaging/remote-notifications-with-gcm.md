---
title: Google 云消息传送与远程通知
description: 本演练介绍如何使用 Google Cloud Messaging 实现远程通知 （也称为推送通知） 的分步说明，Xamarin.Android 应用程序中。 它描述了为通信使用 Google Cloud Messaging (GCM) 而必须实现的各种类，它还说明了如何访问 GCM、 在 Android 清单中设置权限和它演示了端到端消息传送和示例测试程序。
ms.prod: xamarin
ms.assetid: 4FC3C774-EF93-41B2-A81E-C6A08F32C09B
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/12/2018
ms.openlocfilehash: e5a5e44a61d352b5de05564ebb7192d21ed83dfa
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61012720"
---
# <a name="remote-notifications-with-google-cloud-messaging"></a>Google 云消息传送与远程通知

_本演练介绍如何使用 Google Cloud Messaging 实现远程通知 （也称为推送通知） 的分步说明，Xamarin.Android 应用程序中。它描述了为通信使用 Google Cloud Messaging (GCM) 而必须实现的各种类，它还说明了如何访问 GCM、 在 Android 清单中设置权限和它演示了端到端消息传送和示例测试程序。_

> [!NOTE]
> 已被取代 GCM [Firebase Cloud Messaging](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md) (FCM)。
> GCM 服务器和客户端 Api[已弃用](https://firebase.googleblog.com/2018/04/time-to-upgrade-from-gcm-to-fcm.html)将不再提供为 2019 年 4 月 11 日推出。

## <a name="gcm-notifications-overview"></a>GCM 通知概述

在本演练中，我们将创建一个使用 Google Cloud Messaging (GCM) 来实现远程通知的 Xamarin.Android 应用程序 (也称为*推送通知*)。 我们将实现将 GCM 用于远程消息的各种意向和侦听器服务，我们将测试我们的实现与命令行程序，用于模拟的应用程序服务器。 

请注意，Firebase Cloud Messaging (FCM) 是新版本的 GCM &ndash; Google 强烈建议使用 FCM，而不是 GCM。 如果你当前使用 GCM，建议升级到 FCM。 有关 FCM 的详细信息，请参阅[Firebase Cloud Messaging](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md)。 

您可以继续执行本演练之前，必须获取所需的凭据以使用 Google 的 GCM 服务器;此过程所述[Google Cloud Messaging](~/android/data-cloud/google-messaging/google-cloud-messaging.md)。 具体而言，将需要*API 密钥*和一个*发件人 ID*要插入到在本演练中提供的示例代码。 

我们将使用以下步骤创建一个启用 GCM 的 Xamarin.Android 客户端应用程序：

1.  安装与 GCM 服务器通信所需的其他包。
2.  配置应用程序对 GCM 服务器的访问权限。
3.  实现代码来检查 Google Play 服务存在。 
4.  实现与 GCM 注册令牌协商的注册意向服务。
5.  实现实例 ID 侦听器服务，可侦听从 GCM 注册令牌更新。
6.  实现应用服务器通过 GCM 中接收远程消息的 GCM 侦听器服务。

此应用程序将使用名为的新 GCM 功能*消息传递主题*。 在主题消息，应用服务器发送一条消息到一个主题，而不是单个设备的列表。 订阅该主题的设备可以接收作为推送通知的主题的消息。 GCM 主题消息传送的详细信息，请参阅 Google[实现主题的消息传送](https://developers.google.com/cloud-messaging/topic-messaging)。 

客户端应用程序准备就绪后，我们将实现的命令行C#向我们的客户端应用程序通过 GCM 发送推送通知的应用程序。 

## <a name="walkthrough"></a>演练

若要开始，让我们创建一个新的空解决方案，称为**远程通知**。 接下来，让我们将新的 Android 项目添加到此解决方案基于**Android 应用**模板。 让我们称此项目**ClientApp**。 (如果您不熟悉创建 Xamarin.Android 项目，请参阅[Hello，Android](~/android/get-started/hello-android/hello-android-quickstart.md)。)**ClientApp**项目将包含接收远程通知通过 GCM Xamarin.Android 客户端应用程序的代码。 

### <a name="add-required-packages"></a>添加所需的包

我们可以实现我们的客户端应用程序代码之前，必须安装多个包，我们将使用与 GCM 通信。 此外，我们必须添加到我们的设备的 Google Play 商店应用程序，如果尚未安装。

#### <a name="add-the-xamarin-google-play-services-gcm-package"></a>添加 Xamarin Google Play Services GCM 包

若要从 Google Cloud Messaging，接收消息[Google Play Services](https://www.nuget.org/packages/Xamarin.GooglePlayServices.Gcm/)框架必须在设备上存在。 缺少这种框架，Android 应用程序不能从 GCM 服务器接收消息。 Google Play Services 在后台运行 Android 设备处于打开状态，而安静地侦听来自 GCM 的消息。 这些消息到达时，Google Play 服务将消息转换意向，然后广播到已注册为它们的应用程序这些意图。 

在 Visual Studio 中，右键单击**引用 > 管理 NuGet 包...**; 在 Visual Studio for Mac 中，右键单击**包 > 添加包...**.搜索**Xamarin Google Play 服务-GCM**并安装到此包**ClientApp**项目： 

[![安装 Google Play Services](remote-notifications-with-gcm-images/1-google-play-services-sml.png)](remote-notifications-with-gcm-images/1-google-play-services.png#lightbox)

当你安装**Xamarin Google Play 服务-GCM**， **Xamarin Google Play 服务的基本**自动安装。 如果遇到错误时，更改项目的*的最低 Android 目标*而不设置为值**编译使用 SDK 版本**，然后重试 NuGet 安装。 

接下来，编辑**MainActivity.cs**并添加以下`using`语句：

```csharp
using Android.Gms.Common;
using Android.Util;
```

这使得类型在 Google Play Services GMS 包中提供给我们的代码，并添加日志记录功能，我们会将跟踪与 GMS 我们事务。 

#### <a name="google-play-store"></a>Google Play 商店

若要从 GCM 中接收消息，必须在设备上安装 Google Play 商店应用程序。 （每次在设备上安装 Google Play 应用程序时，Google Play 商店也会安装，因此很可能它已经安装在测试设备上。）没有 Google Play 的 Android 应用程序不能从 GCM 中接收消息。 如果你还没有在设备上安装 Google Play 应用商店应用程序，请访问[Google Play](https://support.google.com/googleplay)网站上下载并安装 Google Play。 

或者，可以使用 Android 模拟器运行 Android 2.2 或更高版本，而不是测试设备 （您无需在 Android 仿真程序上安装 Google Play 商店）。 但是，如果使用仿真程序，您必须使用 Wi-fi 连接到 GCM，并且稍后在本演练中所述，必须在你的 Wi-fi 的防火墙中打开多个端口。 

### <a name="set-the-package-name"></a>设置包名称

在中[Google Cloud Messaging](~/android/data-cloud/google-messaging/google-cloud-messaging.md)，我们指定我们 GCM 启用的应用程序的包名称 (此包名称也可作为*应用程序 ID*与我们的 API 密钥和发件人 ID 相关联)。 让我们打开的属性**ClientApp**项目和包名称设置为此字符串。 在此示例中，我们可以将包名称设置为`com.xamarin.gcmexample`:

[![设置包名称](remote-notifications-with-gcm-images/2-package-name-sml.png)](remote-notifications-with-gcm-images/2-package-name.png#lightbox)

请注意，客户端应用程序将无法从 GCM 中接收的注册令牌，如果此包名称不*完全*我们进入 Google 开发人员控制台的包名称匹配。 

### <a name="add-permissions-to-the-android-manifest"></a>将权限添加到 Android 清单

Android 应用程序必须具有配置才能从 Google Cloud Messaging 接收通知的以下权限： 

-   `com.google.android.c2dm.permission.RECEIVE` &ndash; 向我们注册并接收来自 Google Cloud Messaging 的消息的应用程序授予权限。 (用途`c2dm`意味着？ 这代表_云到设备的消息传送_，这是到 GCM 现已弃用前置任务。 
    GCM 仍使用`c2dm`许多及其权限的字符串中。) 

-   `android.permission.WAKE_LOCK` &ndash; （可选）防止设备 CPU 进入睡眠状态时侦听的一条消息。 

-   `android.permission.INTERNET` &ndash; 授予 internet 访问权限，以便客户端应用程序能够与 GCM 通信。 

-   *package_name* `.permission.C2D_MESSAGE` &ndash;与 Android 一起注册该应用程序并请求权限以独占方式接收所有 C2D （云到设备） 的消息。 *Package_name*前缀等同于应用程序 id。 

我们将在 Android 清单中设置这些权限。 让我们编辑**AndroidManifest.xml**并将内容替换为以下 XML: 

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android" 
    package="YOUR_PACKAGE_NAME" 
    android:versionCode="1" 
    android:versionName="1.0" 
    android:installLocation="auto">
    <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
    <uses-permission android:name="android.permission.WAKE_LOCK" />
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="YOUR_PACKAGE_NAME.permission.C2D_MESSAGE" />
    <permission android:name="YOUR_PACKAGE_NAME.permission.C2D_MESSAGE" 
                android:protectionLevel="signature" />
    <application android:label="ClientApp" android:icon="@drawable/Icon">
    </application>
</manifest>
```

在上述 XML 中，更改*您的包名*为你的客户端应用程序项目的包名称。 例如 `com.xamarin.gcmexample`。 

### <a name="check-for-google-play-services"></a>检查 Google Play 服务

对于本演练，我们要创建一个基本应用程序使用单个`TextView`在 UI 中。 此应用程序并不直接指示 GCM 与的交互。 相反，我们会监视输出窗口以查看如何使用 GCM，我们应用握手到达，我们将检查新的通知的通知栏。 

首先，让我们创建适用于在消息区域的布局。 编辑**Resources.layout.Main.axml**并将内容替换为以下 XML: 

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

保存**Main.axml**并将其关闭。

客户端应用程序启动时，我们想让它验证之前我们尝试联系 GCM Google Play 服务可用。 编辑**MainActivity.cs** ，并将为``count``实例使用以下实例变量声明的变量声明： 

```csharp
TextView msgText;
```

接下来，添加以下方法**MainActivity**类： 

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
            msgText.Text = "Sorry, this device is not supported";
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

此代码检查设备以查看是否已安装 Google Play Services APK。 如果未安装，是在消息区域中，指示用户从 Google Play 商店下载 APK （或设备的系统设置中启用它） 中显示一条消息。 因为我们想要运行此检查，客户端应用程序启动时，我们将在末尾添加对此方法的调用`OnCreate`。 


接下来，将`OnCreate`方法使用以下代码：

```csharp
protected override void OnCreate (Bundle bundle)
{
    base.OnCreate (bundle);

    SetContentView (Resource.Layout.Main);
    msgText = FindViewById<TextView> (Resource.Id.msgText);

    IsPlayServicesAvailable ();
}
```

此代码检查存在的 Google Play Services APK，并将结果写入到消息区域中。 

让我们完全重新生成并运行应用程序。 应看到如以下屏幕截图所示的屏幕： 

[![Google Play Services 不可用](remote-notifications-with-gcm-images/3-first-screen-sml.png)](remote-notifications-with-gcm-images/3-first-screen.png#lightbox)

如果没有获得此结果，请验证你的设备上是否安装了 Google Play Services APK **Xamarin Google Play 服务-GCM**包添加到你**ClientApp**项目如下所述前面。 如果收到生成错误，请尝试清除解决方案，然后再次生成项目。 

接下来，我们将编写代码，请联系 GCM 并重新注册令牌。

### <a name="register-with-gcm"></a>向 GCM 注册

应用可从应用服务器接收远程通知之前，它必须向 GCM 注册并重新获取注册令牌。 向 GCM 注册我们的应用程序的工作由`IntentService`我们创建的。 我们`IntentService`将执行以下步骤： 

1.  使用[InstanceID](https://developers.google.com/instance-id/) API 来生成授权我们的客户端应用程序来访问应用程序服务器的安全令牌。 作为回报，我们得到一个注册令牌从 GCM。

2.  （如果该应用程序服务器都需要它），将转发到应用服务器的注册令牌。

3.  订阅一个或多个通知主题通道。

我们实现这后`IntentService`，我们将测试以查看是否收到注册令牌从 GCM。

添加名为的新文件**RegistrationIntentService.cs**和模板代码替换为以下：


```csharp
using System;
using Android.App;
using Android.Content;
using Android.Util;
using Android.Gms.Gcm;
using Android.Gms.Gcm.Iid;

namespace ClientApp
{
    [Service(Exported = false)]
    class RegistrationIntentService : IntentService
    {
        static object locker = new object();

        public RegistrationIntentService() : base("RegistrationIntentService") { }

        protected override void OnHandleIntent (Intent intent)
        {
            try
            {
                Log.Info ("RegistrationIntentService", "Calling InstanceID.GetToken");
                lock (locker)
                {
                    var instanceID = InstanceID.GetInstance (this);
                    var token = instanceID.GetToken (
                        "YOUR_SENDER_ID", GoogleCloudMessaging.InstanceIdScope, null);

                    Log.Info ("RegistrationIntentService", "GCM Registration Token: " + token);
                    SendRegistrationToAppServer (token);
                    Subscribe (token);
                }
            }
            catch (Exception e)
            {
                Log.Debug("RegistrationIntentService", "Failed to get a registration token");
                return;
            }
        }

        void SendRegistrationToAppServer (string token)
        {
            // Add custom implementation here as needed.
        }

        void Subscribe (string token)
        {
            var pubSub = GcmPubSub.GetInstance(this);
            pubSub.Subscribe(token, "/topics/global", null);
        }
    }
}
```

在上面的示例代码中，更改*YOUR_SENDER_ID*到你的客户端应用程序项目的发件人 ID 号。 若要获取你的项目的发件人 ID: 

1.  登录到[Google Cloud Console](https://console.cloud.google.com/) ，然后从下拉菜单中选择你的项目名称。 在中**项目信息**窗格中显示的项目中，单击**转到项目设置**:

    [![选择 XamarinGCM 项目](remote-notifications-with-gcm-images/7-choose-project-sml.png)](remote-notifications-with-gcm-images/7-choose-project.png#lightbox)

2.  上**设置**页上，找到**项目编号**&ndash;这是你的项目的发件人 ID:

    [![显示的项目编号](remote-notifications-with-gcm-images/9-project-number-sml.png)](remote-notifications-with-gcm-images/9-project-number.png#lightbox)

我们想要启动我们`RegistrationIntentService`我们的应用程序开始运行时。 编辑**MainActivity.cs**并修改`OnCreate`方法，以便我们`RegistrationIntentService`后我们检查是否存在的 Google Play 服务已启动： 

```csharp
protected override void OnCreate (Bundle bundle)
{
    base.OnCreate (bundle);

    SetContentView(Resource.Layout.Main);
    msgText = FindViewById<TextView> (Resource.Id.msgText);

    if (IsPlayServicesAvailable ())
    {
        var intent = new Intent (this, typeof (RegistrationIntentService));
        StartService (intent);
    }
}
```

现在让我们看一看每个部分的`RegistrationIntentService`若要了解其工作原理。 

首先，我们添加批注我们`RegistrationIntentService`与要标明我们的服务时不应由系统进行实例化的以下属性： 

```csharp
[Service (Exported = false)]
```

`RegistrationIntentService`构造函数名称的工作线程*RegistrationIntentService*使调试更加容易。 

```csharp
public RegistrationIntentService() : base ("RegistrationIntentService") { }
```

核心功能`RegistrationIntentService`驻留在`OnHandleIntent`方法。 让我们演练一下此代码来查看它如何向 GCM 注册我们的应用程序。


##### <a name="request-a-registration-token"></a>请求的注册令牌

`OnHandleIntent` 首先调用 Google [InstanceID.GetToken](https://developers.google.com/android/reference/com/google/android/gms/iid/InstanceID.html#getToken&#40;java.lang.String,%20java.lang.String&#41;)方法来请求从 GCM 注册令牌。 我们将在该代码打包`lock`若要防止同时发生多个注册意向的可能性&ndash;`lock`可确保按顺序处理这些意图。 如果我们无法获取注册令牌，将引发异常和我们记录了错误。 如果注册成功，`token`设置为我们返回从 GCM 注册令牌： 

```csharp
static object locker = new object ();
...
try
{
    lock (locker)
    {
        var instanceID = InstanceID.GetInstance (this);
        var token = instanceID.GetToken (
            "YOUR_SENDER_ID", GoogleCloudMessaging.InstanceIdScope, null);
        ...
    }
}
catch (Exception e)
{
    Log.Debug ...
```

##### <a name="forward-the-registration-token-to-the-app-server"></a>将注册令牌转发到应用服务器

如果我们获取注册令牌 （即，未引发任何异常），我们调用`SendRegistrationToAppServer`关联用户的注册令牌与服务器端的帐户 （如果有），维护通过我们的应用程序。 因为此实现取决于应用程序服务器的设计，此处提供了一个空的方法： 

```csharp
void SendRegistrationToAppServer (string token)
{
    // Add custom implementation here as needed.
}
```

在某些情况下，应用程序服务器不需要用户的注册令牌;在这种情况下，可以省略此方法。 在将注册令牌发送到应用服务器`SendRegistrationToAppServer`应保持指示令牌是否已发送到服务器的布尔值。 如果此布尔值为 false，`SendRegistrationToAppServer`将该令牌发送到应用服务器&ndash;否则，该令牌已发送到上一次调用中的应用程序服务器。 


##### <a name="subscribe-to-the-notification-topic"></a>订阅通知主题

接下来，我们调用我们`Subscribe`方法，以指示到我们想要通知主题订阅的 GCM。 在中`Subscribe`，我们调用[GcmPubSub.Subscribe](https://developers.google.com/android/reference/com/google/android/gms/gcm/GcmPubSub.html#subscribe&#40;java.lang.String,%20java.lang.String,%20android.os.Bundle&#41;) API 来订阅我们的客户端应用程序下的所有消息`/topics/global`:

```csharp
void Subscribe (string token)
{
    var pubSub = GcmPubSub.GetInstance(this);
    pubSub.Subscribe(token, "/topics/global", null);
}
```

应用程序服务器必须将发送通知消息到`/topics/global`如果我们以接收推送通知。 请注意，本主题下名称`/topics`，只要应用程序服务器和客户端应用程序都达成这些名称可以是任何所需内容。 (在这里，我们选择了名称`global`表示我们希望支持的应用程序服务器的所有主题上接收的消息。) 

在服务器端消息传送的 GCM 主题有关的信息，请参阅 Google[发送到主题消息传送](https://developers.google.com/cloud-messaging/topic-messaging)。 


#### <a name="implement-an-instance-id-listener-service"></a>实现实例 ID 侦听器服务

注册令牌是唯一的安全;但是，客户端应用程序 （或 GCM） 可能需要刷新出现应用程序重新安装或安全问题时的注册令牌。 出于此原因，我们必须实现`InstanceIdListenerService`从 GCM 令牌刷新请求的响应。 

添加名为的新文件**InstanceIdListenerService.cs**和模板代码替换为以下： 

```csharp
using Android.App;
using Android.Content;
using Android.Gms.Gcm.Iid;

namespace ClientApp
{
    [Service(Exported = false), IntentFilter(new[] { "com.google.android.gms.iid.InstanceID" })]
    class MyInstanceIDListenerService : InstanceIDListenerService
    {
        public override void OnTokenRefresh()
        {
            var intent = new Intent (this, typeof (RegistrationIntentService));
            StartService (intent);
        }
    }
}
```

批注`InstanceIdListenerService`具有以下属性以指示服务处于无法进行实例化系统，它可以接收 GCM 注册令牌 (也称为*实例 ID*) 刷新的请求： 

```csharp
[Service(Exported = false), IntentFilter(new[] { "com.google.android.gms.iid.InstanceID" })]
```

`OnTokenRefresh`我们的服务中的方法启动`RegistrationIntentService`，以便它可以截取新的注册令牌。


#### <a name="test-registration-with-gcm"></a>测试注册到 GCM

让我们完全重新生成并运行应用程序。 如果已成功从 GCM 中接收的注册令牌，应在输出窗口中显示注册令牌。 例如： 

```shell
D/Mono    ( 1934): Assembly Ref addref ClientApp[0xb4ac2400] -> Xamarin.GooglePlayServices.Gcm[0xb4ac2640]: 2
I/RegistrationIntentService( 1934): Calling InstanceID.GetToken
I/RegistrationIntentService( 1934): GCM Registration Token: f8LdveCvXig:APA91bFIsjUAbP-V8TPQdLR89qQbEJh1SYG38AcCbBUf34z5gSdUc5OsXrgs93YFiGcRSRafPfzkz23lf3-LvYV1CwrFheMjHgwPeFSh12MywnRIhz

```

### <a name="handle-downstream-messages"></a>句柄的下游消息 

我们到目前为止已实现的代码是仅"设置"代码;它检查以查看 Google Play 服务已安装并协商使用 GCM 和应用程序服务器准备我们的客户端应用程序接收远程通知。 但是，我们尚未实现实际接收和处理下游通知消息的代码。 若要执行此操作，我们必须实现*GCM 侦听器服务*。 此服务接收来自应用程序服务器的主题消息，然后将其本地广播作为通知。 我们实现此服务后，我们将创建测试程序，用于将消息发送到 GCM，以便我们可以看到如果我们实现能够正常运行。 


#### <a name="add-a-notification-icon"></a>添加通知图标

让我们先添加启动我们通知时将出现在通知区域中的小图标。 可以将复制[此图标](remote-notifications-with-gcm-images/ic-stat-ic-notification.png)到你的项目或创建你自己的自定义图标。 我们将命名图标文件**ic_stat_button_click.png**将其复制到**资源/drawable**文件夹。 请记住使用**添加 > 现有项...** 此图标文件包含在项目中。


#### <a name="implement-a-gcm-listener-service"></a>实现 GCM 侦听器服务

添加名为的新文件**GcmListenerService.cs**和模板代码替换为以下：

```csharp
using Android.App;
using Android.Content;
using Android.OS;
using Android.Gms.Gcm;
using Android.Util;

namespace ClientApp
{
    [Service (Exported = false), IntentFilter (new [] { "com.google.android.c2dm.intent.RECEIVE" })]
    public class MyGcmListenerService : GcmListenerService
    {
        public override void OnMessageReceived (string from, Bundle data)
        {
            var message = data.GetString ("message");
            Log.Debug ("MyGcmListenerService", "From:    " + from);
            Log.Debug ("MyGcmListenerService", "Message: " + message);
            SendNotification (message);
        }

        void SendNotification (string message)
        {
            var intent = new Intent (this, typeof(MainActivity));
            intent.AddFlags (ActivityFlags.ClearTop);
            var pendingIntent = PendingIntent.GetActivity (this, 0, intent, PendingIntentFlags.OneShot);

            var notificationBuilder = new Notification.Builder(this)
                .SetSmallIcon (Resource.Drawable.ic_stat_ic_notification)
                .SetContentTitle ("GCM Message")
                .SetContentText (message)
                .SetAutoCancel (true)
                .SetContentIntent (pendingIntent);

            var notificationManager = (NotificationManager)GetSystemService(Context.NotificationService);
            notificationManager.Notify (0, notificationBuilder.Build());
        }
    }
}
```

让我们看看每个部分我们`GcmListenerService`若要了解其工作原理。 

首先，我们添加批注`GcmListenerService`属性以指示，此服务并不是由系统进行实例化以及我们将添加一个意向筛选器，以指示它将接收 GCM 消息： 

```csharp
[Service (Exported = false), IntentFilter (new [] { "com.google.android.c2dm.intent.RECEIVE" })]
```

当`GcmListenerService`从 GCM 中接收消息`OnMessageReceived`调用方法。 此方法从传入的提取消息内容`Bundle`，记录消息内容 （因此，我们可以在输出窗口中查看它），并调用`SendNotification`启动包含接收的消息内容的本地通知： 

```csharp
var message = data.GetString ("message");
Log.Debug ("MyGcmListenerService", "From:    " + from);
Log.Debug ("MyGcmListenerService", "Message: " + message);
SendNotification (message);
```

`SendNotification`方法使用`Notification.Builder`创建通知，然后使用`NotificationManager`以启动该通知。 实际上，这将转换的本地通知，以便向用户显示为远程通知消息。
有关使用详细信息`Notification.Builder`并`NotificationManager`，请参阅[本地通知](~/android/app-fundamentals/notifications/local-notifications.md)。


#### <a name="declare-the-receiver-in-the-manifest"></a>声明在清单中的接收方

我们可以从 GCM 中接收消息之前，我们必须声明 Android 清单中的 GCM 侦听器。 让我们编辑**AndroidManifest.xml** ，并将为`<application>`部分使用以下 XML: 

```xml
<application android:label="RemoteNotifications" android:icon="@drawable/Icon">
    <receiver android:name="com.google.android.gms.gcm.GcmReceiver" 
              android:exported="true" 
              android:permission="com.google.android.c2dm.permission.SEND">
        <intent-filter>
            <action android:name="com.google.android.c2dm.intent.RECEIVE" />
            <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
            <category android:name="YOUR_PACKAGE_NAME" />
        </intent-filter>
    </receiver>
</application>
```

在上述 XML 中，更改*您的包名*为你的客户端应用程序项目的包名称。 在演练本示例中，包名称是`com.xamarin.gcmexample`。 

让我们看看此 XML 中的每个设置的作用：

|设置|描述|
|---|---|
|`com.google.android.gms.gcm.GcmReceiver`|声明我们的应用程序实现 GCM 接收方，捕获和处理传入的推送通知消息。|
|`com.google.android.c2dm.permission.SEND`|声明仅 GCM 服务器可以直接向应用程序发送消息。|
|`com.google.android.c2dm.intent.RECEIVE`|广告，我们的应用程序处理广播的消息从 GCM 意向筛选器。|
|`com.google.android.c2dm.intent.REGISTRATION`|广告，我们的应用程序处理新注册意向的意向筛选器 （即，我们已实现实例 ID 侦听器服务）。|

或者，您可以修饰`GcmListenerService`使用这些属性，而无需在 XML; 中指定此处我们中指定它们**AndroidManifest.xml** ，以便更轻松地遵循代码示例。 


### <a name="create-a-message-sender-to-test-the-app"></a>创建消息发送者，以测试应用程序

让我们将添加C#桌面控制台应用程序项目和解决方案并调用其**MessageSender**。 我们将使用此控制台应用程序来模拟应用程序服务器&ndash;它将通知消息发送到**ClientApp**通过 GCM。 


#### <a name="add-the-jsonnet-package"></a>添加 Json.NET 包

在此控制台应用中，我们要生成包含我们想要发送到客户端应用程序的通知消息的 JSON 有效负载。 我们将使用**Json.NET**中打包**MessageSender**以便更轻松地生成所需的 GCM 的 JSON 对象。 在 Visual Studio 中，右键单击**引用 > 管理 NuGet 包...**; 在 Visual Studio for Mac 中，右键单击**包 > 添加包...**. 

我们来搜索**Json.NET**包并将其安装在项目中： 

[![安装 Json.NET 包](remote-notifications-with-gcm-images/4-add-json.net-sml.png)](remote-notifications-with-gcm-images/4-add-json.net.png#lightbox)


#### <a name="add-a-reference-to-systemnethttp"></a>添加对 System.Net.Http 的引用

我们还需要添加对的引用`System.Net.Http`，以便我们可以实例化`HttpClient`将我们的测试消息发送到 GCM。 在中**MessageSender**项目，右键单击**引用 > 添加引用**向下滚动直到您看到**System.Net.Http**。 旁边放置一个复选标记**System.Net.Http**然后单击**确定**。 


#### <a name="implement-code-that-sends-a-test-message"></a>发送测试消息的实现代码

在中**MessageSender**，编辑**Program.cs**和内容替换为以下代码：

```csharp
using System;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Text;
using System.Threading.Tasks;
using Newtonsoft.Json.Linq;

namespace MessageSender
{
    class MessageSender
    {
        public const string API_KEY = "YOUR_API_KEY";
        public const string MESSAGE = "Hello, Xamarin!";

        static void Main (string[] args)
        {
            var jGcmData = new JObject();
            var jData = new JObject();

            jData.Add ("message", MESSAGE);
            jGcmData.Add ("to", "/topics/global");
            jGcmData.Add ("data", jData);

            var url = new Uri ("https://gcm-http.googleapis.com/gcm/send");
            try
            {
                using (var client = new HttpClient())
                {
                    client.DefaultRequestHeaders.Accept.Add(
                        new MediaTypeWithQualityHeaderValue("application/json"));

                    client.DefaultRequestHeaders.TryAddWithoutValidation (
                        "Authorization", "key=" + API_KEY);

                    Task.WaitAll(client.PostAsync (url,
                        new StringContent(jGcmData.ToString(), Encoding.Default, "application/json"))
                            .ContinueWith(response =>
                            {
                                Console.WriteLine(response);
                                Console.WriteLine("Message sent: check the client device notification tray.");
                            }));
                }
            }
            catch (Exception e)
            {
                Console.WriteLine("Unable to send GCM message:");
                Console.Error.WriteLine(e.StackTrace);
            }
        }
    }
}
```

在上述代码中，更改*YOUR_API_KEY*访问 API 密钥的客户端应用程序项目。 

此测试应用程序服务器将以下 JSON 格式的消息发送到 GCM:

```csharp
{
  "to": "/topics/global",
  "data": {
    "message": "Hello, Xamarin!"
  }
}
```

GCM，反过来，这将消息转发到客户端应用程序。 让我们构建**MessageSender**和打开我们可以在其中运行命令行从它的控制台窗口。



### <a name="try-it"></a>尝试一下！

现在我们已准备好测试我们的客户端应用程序。 如果使用仿真程序或你的设备与 GCM 通信通过 Wi-fi，你必须 GCM 消息才能通过防火墙上打开以下 TCP 端口：5228、 5229 和 5230。

启动客户端应用，并观察输出窗口。 之后`RegistrationIntentService`成功接收一个注册从 GCM 令牌，输出窗口应显示标记使用与下面类似的日志输出：

```shell
I/RegistrationIntentService(16103): GCM Registration Token: eX9ggabZV1Q:APA91bHjBnQXMUeBOT6JDiLpRt8m2YWtY ...
```

此时客户端应用程序已准备好接收远程通知消息。 从命令行中，运行**MessageSender.exe**要将"Hello，Xamarin"的通知消息发送到客户端应用程序。
如果你不生成**MessageSender**项目中，现在执行此操作。

若要运行**MessageSender.exe**在 Visual Studio 中，打开命令提示符下，更改到**MessageSender/bin/Debug**目录，并运行命令直接：

```cmd
MessageSender.exe
```

若要运行**MessageSender.exe**在 Visual Studio for Mac 中，打开终端会话，更改到**MessageSender/bin/Debug**目录，并使用 mono 运行**MessageSender.exe** 

```bash
mono MessageSender.exe
```

它可能需要一分钟来传播通过 GCM 和返回到客户端应用程序的消息。 如果已成功收到消息时，应该会看到类似于输出窗口中的以下输出： 

```shell
D/MyGcmListenerService(16103): From:    /topics/global
D/MyGcmListenerService(16103): Message: Hello, Xamarin!
```

此外，您会注意到一个新的通知图标已显示在通知栏： 

[![在设备上会显示通知图标](remote-notifications-with-gcm-images/5-icon-appears-sml.png)](remote-notifications-with-gcm-images/5-icon-appears.png#lightbox)

当您打开的通知送纸器，若要查看通知时，应会看到我们远程通知：

[![显示通知消息](remote-notifications-with-gcm-images/6-notification-in-tray-sml.png)](remote-notifications-with-gcm-images/6-notification-in-tray.png#lightbox)

恭喜，您的应用程序已收到其第一个远程通知 ！

请注意是否应用强制停止，将无法再接收 GCM 消息。 若要强制停止后恢复通知，应用程序必须手动重新启动。 有关此 Android 策略的详细信息，请参阅[启动已停止的应用程序上的控件](https://developer.android.com/about/versions/android-3.1.html#launchcontrols)，这[堆栈溢出文章](https://stackoverflow.com/questions/5051687/broadcastreceiver-not-receiving-boot-completed/19856267#19856267)。 

 
## <a name="summary"></a>总结

本演练详细 Xamarin.Android 应用程序中实现远程通知的步骤。 介绍了如何安装所需的 GCM 通信的其他包，并介绍了如何配置应用程序对 GCM 服务器的访问权限。 它提供示例代码，说明如何检查存在的 Google Play 服务、 如何实现注册意向服务和实例 ID 侦听器服务协商向 GCM 注册令牌，以及如何实现 GCM 侦听器接收并处理远程通知消息的服务。 最后，我们实现了命令行测试程序，用于将测试通知发送到 GCM 通过我们的客户端应用程序。 


## <a name="related-links"></a>相关链接

- [GCM 远程通知 （示例）](https://developer.xamarin.com/samples/monodroid/RemoteNotifications)
- [Google 云消息传送](~/android/data-cloud/google-messaging/google-cloud-messaging.md)
