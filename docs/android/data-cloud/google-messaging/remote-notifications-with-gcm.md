---
title: Google Cloud Messaging 的远程通知
description: 本演练逐步说明如何使用 Google Cloud Messaging 来实现 Xamarin Android 应用程序中的远程通知 (也称为推送通知)。 它介绍了为了与 Google Cloud Messaging (GCM) 进行通信而必须实现的各种类, 并说明如何在 Android 清单中设置权限以访问 GCM, 并使用示例测试程序演示端到端消息传送。
ms.prod: xamarin
ms.assetid: 4FC3C774-EF93-41B2-A81E-C6A08F32C09B
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 05/02/2019
ms.openlocfilehash: 8c816bf98d9997d09b73e7c9cb0d2ff436b65fbb
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2019
ms.locfileid: "68643975"
---
# <a name="remote-notifications-with-google-cloud-messaging"></a>Google Cloud Messaging 的远程通知

> [!WARNING]
> 2018年4月10日起已弃用的 Google GCM。 下面的文档和示例项目可能不再维护。 Google 的 GCM 服务器和客户端 Api 将在 2019 5 月29日后删除。 Google 建议将 GCM 应用迁移到 Firebase Cloud 消息 (FCM)。 有关 GCM 弃用和迁移的详细信息, 请参阅[Google Cloud Messaging-已弃用](https://developers.google.com/cloud-messaging/)。
>
> 若要开始使用 Firebase Cloud 消息传递到 Xamarin 的远程通知, 请参阅[使用 FCM 的远程通知](remote-notifications-with-fcm.md)。

_本演练逐步说明如何使用 Google Cloud Messaging 来实现 Xamarin Android 应用程序中的远程通知 (也称为推送通知)。它介绍了为了与 Google Cloud Messaging (GCM) 进行通信而必须实现的各种类, 并说明如何在 Android 清单中设置权限以访问 GCM, 并使用示例测试程序演示端到端消息传送。_

## <a name="gcm-notifications-overview"></a>GCM 通知概述

在本演练中, 我们将创建一个 Xamarin Android 应用程序, 该应用程序使用 Google Cloud Messaging (GCM) 来实施远程通知 (也称为*推送通知*)。 我们将实现使用 GCM 进行远程消息传递的各种意向和侦听器服务, 并使用可模拟应用程序服务器的命令行程序来测试实现。 

在继续本演练之前, 必须获取所需的凭据才能使用 Google 的 GCM 服务器;[Google Cloud Messaging](~/android/data-cloud/google-messaging/google-cloud-messaging.md)中对此过程进行了说明。 特别是, 您将需要一个*API 密钥*和一个*发送程序 ID* , 以插入本演练中提供的示例代码。 

我们将使用以下步骤创建支持 GCM 的 Xamarin 客户端应用:

1.  安装与 GCM 服务器进行通信所需的其他包。
2.  配置用于访问 GCM 服务器的应用权限。
3.  实现代码以检查是否存在 Google Play Services。 
4.  实现一个注册意向服务, 该服务与 GCM 协商注册令牌。
5.  实现一个实例 ID 侦听器服务, 该服务侦听 GCM 的注册令牌更新。
6.  实现一个 GCM 侦听器服务, 该服务从应用服务器通过 GCM 接收远程消息。

此应用将使用称为*主题消息传递*的新 GCM 功能。 在主题消息传递中, 应用服务器将消息发送到主题, 而不是发送到单个设备的列表。 订阅该主题的设备可以接收主题消息作为推送通知。 有关 GCM 主题消息传送的详细信息, 请参阅 Google 的[实现主题消息](https://developers.google.com/cloud-messaging/topic-messaging)。 

当客户端应用程序准备就绪时, 我们将实现一个命令C#行应用程序, 该应用程序通过 GCM 向我们的客户端应用程序发送推送通知。 

## <a name="walkthrough"></a>演练

首先, 让我们创建一个名为 **"** 的新的空解决方案。 接下来, 让我们将新的 Android 项目添加到此解决方案, 该解决方案基于**Android 应用程序**模板。 让我们调用此项目**ClientApp**。 (如果您不熟悉如何创建 Xamarin Android 项目, 请参阅[Hello、android](~/android/get-started/hello-android/hello-android-quickstart.md)。)**ClientApp**项目将包含 Xamarin Android 客户端应用程序的代码, 该应用程序通过 GCM 接收远程通知。 

### <a name="add-required-packages"></a>添加所需包

在我们可以实现客户端应用程序代码之前, 必须安装多个包用于与 GCM 通信。 此外, 还必须将 Google Play 商店应用程序添加到设备 (如果尚未安装)。

#### <a name="add-the-xamarin-google-play-services-gcm-package"></a>添加 Xamarin Google Play Services GCM 包

若要从 Google Cloud Messaging 接收消息, [Google Play Services](https://www.nuget.org/packages/Xamarin.GooglePlayServices.Gcm/)框架必须存在于设备上。 如果没有此框架, Android 应用程序将无法从 GCM 服务器接收消息。 Google Play Services 在运行 Android 设备时, 在后台运行, 不知不觉地侦听来自 GCM 的消息。 当这些消息到达时, Google Play Services 将消息转换为意向, 然后将这些意向广播给为其注册的应用程序。 

在 Visual Studio 中, 右键单击 "**引用" > "管理 NuGet 包 ...** "在 Visual Studio for Mac 中, 右键单击 "**包" > "添加包 ...** "。搜索**Xamarin GOOGLE PLAY SERVICES GCM** , 并将此包安装到**ClientApp**项目中: 

[![安装 Google Play Services](remote-notifications-with-gcm-images/1-google-play-services-sml.png)](remote-notifications-with-gcm-images/1-google-play-services.png#lightbox)

安装**xamarin GOOGLE PLAY SERVICES GCM**时, 会自动安装**xamarin Google Play Services 基础**。 如果遇到错误, 请将项目的*最低 Android 目标*设置更改为**使用 SDK 版本的编译**以外的值, 然后再次尝试安装 NuGet。 

接下来, 编辑**MainActivity.cs**并添加以下`using`语句:

```csharp
using Android.Gms.Common;
using Android.Util;
```

这使得 Google Play Services GMS 包中的类型可用于我们的代码, 并添加了日志记录功能, 我们将使用这些功能跟踪 GMS 的事务。 

#### <a name="google-play-store"></a>Google Play 商店

若要从 GCM 接收消息, 则必须在设备上安装 Google Play 商店应用程序。 (每当在设备上安装 Google Play 应用程序时, 也会安装 Google Play 商店, 因此可能已在测试设备上安装了该应用程序。)如果没有 Google Play, Android 应用程序将无法从 GCM 接收消息。 如果你尚未在设备上安装 Google Play 商店应用, 请访问[Google Play](https://support.google.com/googleplay)网站下载并安装 Google Play。 

或者, 你可以使用运行 Android 2.2 或更高版本的 Android 仿真程序, 而不是使用测试设备 (无需在 Android 模拟器上安装 Google Play 商店)。 但是, 如果使用模拟器, 则必须使用 Wi-fi 连接到 GCM, 并且必须打开 Wi-fi 防火墙中的几个端口, 如本演练稍后部分中所述。 

### <a name="set-the-package-name"></a>设置包名称

在[Google Cloud Messaging](~/android/data-cloud/google-messaging/google-cloud-messaging.md)中, 我们为启用了 GCM 的应用指定了包名称 (此包名称还用作与我们的 API 密钥和发送方 id 相关联的*应用程序 id* )。 接下来, 打开**ClientApp**项目的属性, 并将包名称设置为此字符串。 在此示例中, 我们将包名称设置`com.xamarin.gcmexample`为:

[![设置包名称](remote-notifications-with-gcm-images/2-package-name-sml.png)](remote-notifications-with-gcm-images/2-package-name.png#lightbox)

请注意, 如果此包名称与我们在 Google 开发人员控制台中输入的包名称不*完全*匹配, 则客户端应用将无法从 GCM 接收注册令牌。 

### <a name="add-permissions-to-the-android-manifest"></a>将权限添加到 Android 清单

Android 应用程序必须配置有以下权限, 然后才能接收来自 Google Cloud Messaging 的通知: 

-   `com.google.android.c2dm.permission.RECEIVE`&ndash;向应用授予注册和接收来自 Google Cloud Messaging 的消息的权限。 (这是`c2dm`什么意思？ 这代表_云到设备的消息传送_, 这是 GCM 的现不推荐使用的前置任务。 
    GCM 仍在`c2dm`它的许多权限字符串中使用。) 

-   `android.permission.WAKE_LOCK`&ndash; (可选) 在侦听消息时, 阻止设备 CPU 进入睡眠状态。 

-   `android.permission.INTERNET`&ndash;授予 internet 访问权限, 以便客户端应用能够与 GCM 通信。 

-   *package_name 将应用程序*`.permission.C2D_MESSAGE`注册到 Android 并请求权限, 以独占接收所有 C2D (云到设备) 消息。 &ndash; *Package_name*前缀与应用程序 ID 相同。 

我们将在 Android 清单中设置这些权限。 让我们编辑**androidmanifest.xml** , 并将内容替换为以下 xml: 

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

在上面的 XML 中, 将*YOUR_PACKAGE_NAME*更改为客户端应用程序项目的包名称。 例如， `com.xamarin.gcmexample` 。 

### <a name="check-for-google-play-services"></a>检查 Google Play Services

对于本演练, 我们将使用用户界面中的单个来创建一个`TextView`简单的应用。 此应用不直接指示与 GCM 交互。 相反, 我们将观看 "输出" 窗口, 查看应用如何与 GCM 握手, 并在通知托盘收到新通知时将其选中。 

首先, 让我们为消息区域创建布局。 编辑**main.axml** , 并将内容替换为以下 XML: 

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

保存**main.axml**并关闭它。

当客户端应用程序启动时, 我们想要在尝试联系 GCM 之前验证 Google Play Services 是否可用。 编辑**MainActivity.cs**并将``count``实例变量声明替换为以下实例变量声明: 

```csharp
TextView msgText;
```

接下来, 将以下方法添加到**MainActivity**类: 

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

此代码检查设备以查看是否安装了 Google Play Services 的 APK。 如果未安装, 则会在消息区域显示一条消息, 指示用户从 Google Play 商店下载 APK (或在设备的系统设置中启用该程序)。 由于我们希望在客户端应用程序启动时运行此检查, 因此我们将在末尾`OnCreate`添加对此方法的调用。 


接下来, 将`OnCreate`方法替换为以下代码:

```csharp
protected override void OnCreate (Bundle bundle)
{
    base.OnCreate (bundle);

    SetContentView (Resource.Layout.Main);
    msgText = FindViewById<TextView> (Resource.Id.msgText);

    IsPlayServicesAvailable ();
}
```

此代码检查是否存在 Google Play Services APK, 并将结果写入消息区域。 

让我们完全重新生成并运行该应用程序。 应会看到如下屏幕截图所示的屏幕: 

[![Google Play Services 可用](remote-notifications-with-gcm-images/3-first-screen-sml.png)](remote-notifications-with-gcm-images/3-first-screen.png#lightbox)

如果没有得到此结果, 请验证是否在设备上安装了 Google Play Services APK, 并将**Xamarin GOOGLE PLAY SERVICES GCM**包添加到**ClientApp**项目 (如前文所述)。 如果遇到生成错误, 请尝试清除解决方案并重新生成项目。 

接下来, 我们将编写代码来联系 GCM 并取回注册令牌。

### <a name="register-with-gcm"></a>向 GCM 注册

此应用必须在 GCM 上注册并获取注册令牌, 然后应用才能从应用服务器接收远程通知。 使用 GCM 注册应用程序的工作由`IntentService`我们创建的处理。 我们`IntentService`执行以下步骤: 

1.  使用[InstanceID](https://developers.google.com/instance-id/) API 生成授权客户端应用访问应用服务器的安全令牌。 返回后, 我们从 GCM 获取注册令牌。

2.  将注册令牌转发到应用服务器 (如果应用服务器需要)。

3.  订阅一个或多个通知主题通道。

实现此`IntentService`方法后, 我们将对其进行测试, 看看我们是否从 GCM 获取注册令牌。

添加一个名为**RegistrationIntentService.cs**的新文件, 并将模板代码替换为以下代码:


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

在上面的示例代码中, 将*YOUR_SENDER_ID*更改为客户端应用程序项目的发送方 ID 号。 获取项目的发送方 ID: 

1.  登录到[Google Cloud Console](https://console.cloud.google.com/) , 并从下拉菜单中选择你的项目名称。 在为项目显示的 "**项目信息**" 窗格中, 单击 "**前往项目设置**":

    [![选择 XamarinGCM 项目](remote-notifications-with-gcm-images/7-choose-project-sml.png)](remote-notifications-with-gcm-images/7-choose-project.png#lightbox)

2.  在 "**设置**" 页上, 找到项目**编号** &ndash; , 这是项目的发送方 ID:

    [![显示的项目编号](remote-notifications-with-gcm-images/9-project-number-sml.png)](remote-notifications-with-gcm-images/9-project-number.png#lightbox)

我们想要在应用`RegistrationIntentService`程序开始运行时启动我们。 编辑**MainActivity.cs**并修改`OnCreate`方法, 以便在检查`RegistrationIntentService` Google Play Services 的状态后启动我们: 

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

现在, 让我们看一下的`RegistrationIntentService`每个部分, 了解其工作原理。 

首先, 我们将`RegistrationIntentService`使用以下属性批注, 以指示我们的服务不会被系统实例化: 

```csharp
[Service (Exported = false)]
```

该`RegistrationIntentService`构造函数将工作线程命名为*RegistrationIntentService* , 以便更轻松地进行调试。 

```csharp
public RegistrationIntentService() : base ("RegistrationIntentService") { }
```

的核心功能`RegistrationIntentService`位于`OnHandleIntent`方法中。 让我们演练一下此代码, 了解它如何向 GCM 注册我们的应用程序。


##### <a name="request-a-registration-token"></a>请求注册令牌

`OnHandleIntent`首先调用 Google 的[GetToken](https://developers.google.com/android/reference/com/google/android/gms/iid/InstanceID.html#getToken&#40;java.lang.String,%20java.lang.String&#41;)方法, 以从 GCM 请求注册令牌。 我们将此代码包装在`lock`中, 以防止多个注册方法`lock`同时&ndash;出现的可能性, 从而确保按顺序处理这些意向。 如果我们无法获取注册令牌, 则会引发异常, 并记录一个错误。 如果注册成功, `token`则将设置为我们从 GCM 返回的注册令牌: 

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

如果我们获取注册令牌 (即未引发异常), 我们会调用`SendRegistrationToAppServer`将用户的注册令牌与应用程序维护的服务器端帐户 (如果有) 相关联。 由于此实现依赖于应用服务器的设计, 因此在此处提供了一个空方法: 

```csharp
void SendRegistrationToAppServer (string token)
{
    // Add custom implementation here as needed.
}
```

在某些情况下, 应用服务器不需要用户的注册令牌;在这种情况下, 可以省略此方法。 向应用服务器发送注册令牌时, `SendRegistrationToAppServer`应维护一个布尔值以指示令牌是否已发送到服务器。 如果此布尔值为 false `SendRegistrationToAppServer` , 则会将令牌发送到&ndash;应用服务器, 否则会将令牌发送到以前调用中的应用服务器。 


##### <a name="subscribe-to-the-notification-topic"></a>订阅通知主题

接下来, 我们调用`Subscribe`方法, 向 GCM 指出我们想要订阅通知主题。 在`Subscribe`中, 我们将调用[GcmPubSub](https://developers.google.com/android/reference/com/google/android/gms/gcm/GcmPubSub.html#subscribe&#40;java.lang.String,%20java.lang.String,%20android.os.Bundle&#41;) API, 以将客户端应用订阅到下面`/topics/global`的所有消息:

```csharp
void Subscribe (string token)
{
    var pubSub = GcmPubSub.GetInstance(this);
    pubSub.Subscribe(token, "/topics/global", null);
}
```

`/topics/global`如果要接收, 应用服务器必须向发送通知消息。 请注意, 只要应用服务器`/topics`和客户端应用都同意这些名称, 下的主题名称就可以是你需要的任何内容。 (在这里, 我们选择了`global`该名称, 指出我们想要接收应用服务器支持的所有主题的消息。) 

有关服务器端上的 GCM 主题消息传送的信息, 请参阅 Google 的向[主题发送消息](https://developers.google.com/cloud-messaging/topic-messaging)。 


#### <a name="implement-an-instance-id-listener-service"></a>实现实例 ID 侦听器服务

注册令牌唯一且安全;但是, 在应用程序重新安装或安全问题的情况下, 客户端应用程序 (或 GCM) 可能需要刷新注册令牌。 出于此原因, 我们必须实现`InstanceIdListenerService`响应 GCM 的令牌刷新请求。 

添加一个名为**InstanceIdListenerService.cs**的新文件, 并将模板代码替换为以下代码: 

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

使用`InstanceIdListenerService`以下属性进行批注, 以指示该服务不是由系统实例化的, 并且可以接收 GCM 注册令牌 (也称为*实例 ID*) refresh 请求: 

```csharp
[Service(Exported = false), IntentFilter(new[] { "com.google.android.gms.iid.InstanceID" })]
```

服务`OnTokenRefresh`中的方法会`RegistrationIntentService`启动, 以便它可以截取新的注册令牌。


#### <a name="test-registration-with-gcm"></a>通过 GCM 测试注册

让我们完全重新生成并运行该应用程序。 如果成功接收到 GCM 的注册令牌, 则应在 "输出" 窗口中显示注册令牌。 例如: 

```shell
D/Mono    ( 1934): Assembly Ref addref ClientApp[0xb4ac2400] -> Xamarin.GooglePlayServices.Gcm[0xb4ac2640]: 2
I/RegistrationIntentService( 1934): Calling InstanceID.GetToken
I/RegistrationIntentService( 1934): GCM Registration Token: f8LdveCvXig:APA91bFIsjUAbP-V8TPQdLR89qQbEJh1SYG38AcCbBUf34z5gSdUc5OsXrgs93YFiGcRSRafPfzkz23lf3-LvYV1CwrFheMjHgwPeFSh12MywnRIhz

```

### <a name="handle-downstream-messages"></a>处理下游消息 

到目前为止, 我们实现的代码只是 "设置" 代码;它将检查是否安装了 Google Play Services 并与 GCM 和应用服务器协商, 以便为客户端应用程序准备接收远程通知。 但是, 我们还在实现实际接收和处理下游通知消息的代码。 为此, 必须实现*GCM 侦听器服务*。 此服务从应用程序服务器接收主题消息, 并在本地将其作为通知广播。 实现此服务后, 我们将创建一个测试程序, 用于将消息发送到 GCM, 以便查看实现是否正常工作。 


#### <a name="add-a-notification-icon"></a>添加通知图标

首先添加一个小图标, 该图标将在通知启动时出现在通知区域中。 您可以将[此图标](remote-notifications-with-gcm-images/ic-stat-ic-notification.png)复制到您的项目或创建您自己的自定义图标。 我们会将图标文件**ic_stat_button_click** , 并将其复制到**资源/可绘制**文件夹中。 请记住, 使用 "**添加 > 现有项 ...** " 将此图标文件包含在项目中。


#### <a name="implement-a-gcm-listener-service"></a>实现 GCM 侦听器服务

添加一个名为**GcmListenerService.cs**的新文件, 并将模板代码替换为以下代码:

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

我们来看看我们`GcmListenerService`的每个部分, 了解其工作原理。 

首先, 我们使用`GcmListenerService`特性进行批注, 以指示该服务不是由系统实例化的, 并且我们包含一个意向筛选器来指示它接收 GCM 消息: 

```csharp
[Service (Exported = false), IntentFilter (new [] { "com.google.android.c2dm.intent.RECEIVE" })]
```

从`GcmListenerService` GCM 收到消息时, 将`OnMessageReceived`调用方法。 此方法提取传入`Bundle`的消息内容, 记录消息内容 (以便我们可以在 "输出" 窗口中查看该内容), 并调用`SendNotification`启动包含收到的消息内容的本地通知: 

```csharp
var message = data.GetString ("message");
Log.Debug ("MyGcmListenerService", "From:    " + from);
Log.Debug ("MyGcmListenerService", "Message: " + message);
SendNotification (message);
```

方法使用`Notification.Builder`创建通知, 然后使用`NotificationManager`启动通知。 `SendNotification` 实际上, 这会将远程通知消息转换为向用户显示的本地通知。
有关使用`Notification.Builder`和`NotificationManager`的详细信息, 请参阅[本地通知](~/android/app-fundamentals/notifications/local-notifications.md)。


#### <a name="declare-the-receiver-in-the-manifest"></a>在清单中声明接收方

我们必须在 Android 清单中声明 GCM 侦听器, 然后才能从 GCM 接收消息。 让我们编辑**androidmanifest.xml** , 并将`<application>`节替换为以下 xml: 

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

在上面的 XML 中, 将*YOUR_PACKAGE_NAME*更改为客户端应用程序项目的包名称。 在我们的演练示例中, 包名称`com.xamarin.gcmexample`是。 

让我们看看此 XML 中的每个设置的作用:

|设置|描述|
|---|---|
|`com.google.android.gms.gcm.GcmReceiver`|声明我们的应用程序实现了捕获和处理传入推送通知消息的 GCM 接收方。|
|`com.google.android.c2dm.permission.SEND`|声明仅 GCM 服务器可以直接将消息发送到应用程序。|
|`com.google.android.c2dm.intent.RECEIVE`|意向筛选器广告, 我们的应用处理来自 GCM 的广播消息。|
|`com.google.android.c2dm.intent.REGISTRATION`|意向筛选器广告: 我们的应用程序处理新的注册意向 (也就是说, 我们实现了一个实例 ID 侦听器服务)。|

或者, 您也可以`GcmListenerService`用这些特性来修饰, 而不是用 XML 来指定; 这里, 我们在**androidmanifest.xml**中指定这些特性, 使代码示例更易于理解。 


### <a name="create-a-message-sender-to-test-the-app"></a>创建消息发件人来测试应用程序

让我们将C#桌面控制台应用程序项目添加到解决方案, 并将其称为**MessageSender**。 我们将使用此控制台应用程序模拟应用程序服务器&ndash; , 它会通过 GCM 向**ClientApp**发送通知消息。 


#### <a name="add-the-jsonnet-package"></a>添加 Json.NET 包

在此控制台应用中, 我们将生成一个 JSON 有效负载, 其中包含我们要发送到客户端应用程序的通知消息。 我们将使用**MessageSender**中的**Json.NET**包, 以便更轻松地生成 GCM 所需的 Json 对象。 在 Visual Studio 中, 右键单击 "**引用" > "管理 NuGet 包 ...** "在 Visual Studio for Mac 中, 右键单击 "**包" > "添加包 ...** "。 

让我们搜索**Json.NET**包, 并将其安装在项目中: 

[![安装 Json.NET 包](remote-notifications-with-gcm-images/4-add-json.net-sml.png)](remote-notifications-with-gcm-images/4-add-json.net.png#lightbox)


#### <a name="add-a-reference-to-systemnethttp"></a>添加对 System .Net. Http 的引用

还需要添加对`System.Net.Http`的引用, 以便可以`HttpClient`实例化以便将测试消息发送到 GCM。 在**MessageSender**项目中, 右键单击 "**引用" > 添加引用**并向下滚动,**直到看到 "** "。 选中 " **System .net. Http** " 旁边的复选标记, 然后单击 **"确定"** 。 


#### <a name="implement-code-that-sends-a-test-message"></a>实现发送测试消息的代码

在**MessageSender**中, 编辑**Program.cs** , 并将内容替换为以下代码:

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

在上面的代码中, 将*YOUR_API_KEY*更改为客户端应用程序项目的 API 密钥。 

此测试应用服务器将以下 JSON 格式的消息发送到 GCM:

```csharp
{
  "to": "/topics/global",
  "data": {
    "message": "Hello, Xamarin!"
  }
}
```

然后, GCM 会将此消息转发到客户端应用。 让我们生成**MessageSender** , 并打开一个控制台窗口, 可在其中通过命令行运行该窗口。



### <a name="try-it"></a>尝试一下！

现在, 我们已准备好测试客户端应用程序。 如果你使用的是仿真程序, 或者如果你的设备通过 Wi-fi 与 GCM 通信, 则必须在防火墙上打开以下 TCP 端口, 以便 GCM 消息通过:5228、5229和5230。

启动客户端应用程序并查看 "输出" 窗口。 `RegistrationIntentService`成功接收 GCM 的注册令牌后, "输出" 窗口应显示带有日志输出的令牌, 如下所示:

```shell
I/RegistrationIntentService(16103): GCM Registration Token: eX9ggabZV1Q:APA91bHjBnQXMUeBOT6JDiLpRt8m2YWtY ...
```

此时, 客户端应用程序已准备好接收远程通知消息。 在命令行中运行**MessageSender**程序, 将 "Hello, Xamarin" 通知消息发送到客户端应用程序。
如果尚未生成**MessageSender**项目, 请立即执行此操作。

若要在 Visual Studio 下运行**MessageSender** , 请打开命令提示符, 更改为**MessageSender/bin/Debug**目录, 并直接运行命令:

```cmd
MessageSender.exe
```

若要在 Visual Studio for Mac 下运行**MessageSender** , 请打开终端会话, 更改为**MessageSender/bin/Debug**目录, 并使用 mono 运行**MessageSender** 

```bash
mono MessageSender.exe
```

消息通过 GCM 传播并返回到客户端应用可能需要长达一分钟的时间。 如果成功接收消息, 则应在输出窗口中看到类似于以下内容的输出: 

```shell
D/MyGcmListenerService(16103): From:    /topics/global
D/MyGcmListenerService(16103): Message: Hello, Xamarin!
```

此外, 还应注意通知托盘中显示了新的通知图标: 

[![设备上显示的通知图标](remote-notifications-with-gcm-images/5-icon-appears-sml.png)](remote-notifications-with-gcm-images/5-icon-appears.png#lightbox)

打开通知托盘以查看通知时, 应会看到远程通知:

[![显示通知消息](remote-notifications-with-gcm-images/6-notification-in-tray-sml.png)](remote-notifications-with-gcm-images/6-notification-in-tray.png#lightbox)

恭喜, 你的应用程序已收到第一个远程通知!

请注意, 如果应用程序被强制停止, 将不再接收 GCM 消息。 若要在强制停止后恢复通知, 必须手动重新启动应用。 有关此 Android 策略的详细信息, 请参阅[在已停止的应用程序上启动控件](https://developer.android.com/about/versions/android-3.1.html#launchcontrols)和此[堆栈溢出 post](https://stackoverflow.com/questions/5051687/broadcastreceiver-not-receiving-boot-completed/19856267#19856267)。 

 
## <a name="summary"></a>总结

本演练详细介绍了在 Xamarin Android 应用程序中实施远程通知的步骤。 本文介绍了如何安装 GCM 通信所需的其他程序包, 并介绍了如何配置应用程序权限以访问 GCM 服务器。 其中提供了示例代码, 该代码演示了如何检查是否存在 Google Play Services, 如何实现注册意向服务和实例 ID 侦听器服务, 该服务与 GCM 协商以获取注册令牌, 以及如何实现 GCM 侦听器接收和处理远程通知消息的服务。 最后, 我们实现了一个命令行测试程序, 用于通过 GCM 将测试通知发送到客户端应用程序。 


## <a name="related-links"></a>相关链接

- [GCM "(示例)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/remotenotifications)
- [Google 云消息传送](~/android/data-cloud/google-messaging/google-cloud-messaging.md)
