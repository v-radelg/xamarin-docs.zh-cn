---
title: Nougat 功能
description: 如何开始使用 Xamarin.Android 开发适用于 Android Nougat 应用。
ms.prod: xamarin
ms.assetid: 5C74ABE2-C862-4ED0-8EA5-C7FEE5251D4B
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/02/2018
ms.openlocfilehash: de2b92a4007f085a14c16f0c1e8ca9e568df1aff
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/08/2019
ms.locfileid: "57668358"
---
# <a name="nougat-features"></a>Nougat 功能

_如何开始使用 Xamarin.Android 开发适用于 Android Nougat 应用。_

本文提供了 Android Nougat 中引入的功能的概述介绍了如何准备 Xamarin.Android 进行 Android Nougat 开发，并且提供了示例应用程序，展示了如何使用 Android Nougat 功能中的链接Xamarin.Android 应用。


## <a name="overview"></a>概述

[Android Nougat](https://developer.android.com/about/versions/nougat/android-7.0.html)是到 Android 6.0 Marshmallow Google 的跟进。 Xamarin.Android 提供的支持**Android 7.x 绑定**Xamarin Android 7.0 及更高版本。 Android Nougat 添加 Nougat 功能; 如下所述的许多新 Api使用 Xamarin.Android 7.0 时，这些 Api 是适用于 Xamarin.Android 应用。

[![Android 平板电脑和手机运行 Android Nougat 的 Hero 映像](nougat-images/android-n-hero-sml.png)](nougat-images/android-n-hero.png#lightbox)

有关 Android 7.x Api 的详细信息，请参阅[面向开发人员的 Android 7.1](https://developer.android.com/preview/api-overview.html)。
有关 Xamarin.Android 7.0 的已知问题的列表，请参阅[发行说明](https://developer.xamarin.com/releases/android/xamarin.android_7/xamarin.android_7.0/)。

Android Nougat Xamarin.Android 开发人员提供所需的许多新功能。 这些功能包括：

-   **多窗口支持**&ndash;此增强功能使用户可以同时打开两个应用程序在屏幕上的。

-   **通知增强功能**&ndash;经过重新设计的通知系统中 Android Nougat 包括*直接回复*功能，使用户能够快速响应直接从通知短信用户界面。 此外，如果您的应用程序创建的通知接收到消息，则新*捆绑通知*功能可以捆绑在通知一起作为一个组时收到多条消息。

-   **数据保护程序**&ndash;此功能是一种新的系统服务，从而降低应用手机网络数据使用; 它给予用户对应用程序如何使用移动电话网络数据的控制。

此外，Android Nougat 带来了许多其他增强功能等新的网络安全的配置功能的应用程序开发人员感兴趣的 Doze 随时随地、 密钥证明，新快速设置 Api，ICU4J Api 的多区域设置支持 web 视图增强功能，对 Java 8 语言功能的访问、 作用域内的目录访问权限、 自定义指针 API、 平台 VR 支持、 虚拟文件和后台处理优化。

本文介绍如何开始使用 Android Nougat 来试用新功能和规划迁移或功能的工作，以面向新的 Android Nougat 平台构建应用程序。


## <a name="requirements"></a>要求

以下是所需的基于 Xamarin 的应用中使用新的 Android Nougat 功能：

-   **Visual Studio 或 Visual Studio for Mac** &ndash;如果使用的 Visual Studio，版本 4.2.0.628 或更高版本的 Visual Studio Tools for Xamarin 是必需的。 如果你使用 Visual Studio for Mac，版本 6.1.0 或更高版本的 Visual Studio for Mac 所需。

-   **Xamarin.Android** &ndash; Xamarin.Android 7.0 或更高版本必须安装并配置与 Visual Studio 或 Visual Studio for mac。

-   **Android SDK** -Android SDK 7.0 (API 24) 或更高版本必须安装通过 Android SDK 管理器。

-   **Java 开发人员工具包** &ndash; Xamarin Android 7.0 开发需要[JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)或更高，如果你正在开发的 API 级别 24 或更高版本 （JDK 8 还支持 API 级别低于 24）。 如果使用的自定义控件或窗体预览程序，则需要 JDK 8 的 64 位版本。

> [!IMPORTANT]
> Xamarin.Android 不支持 JDK 9。

请注意，应用必须重新生成 Xamarin C6SR4 或更高版本才能可靠地使用 Android Nougat。 因为可以仅为链接 Android Nougat [NDK 提供本机库](https://developer.android.com/about/versions/nougat/android-7.0-changes.html)，如使用库的现有应用程序**Mono.Data.Sqlite.dll** Android Nougat 上运行，如果它们不正确时可能会崩溃重新生成。



## <a name="getting-started"></a>入门

若要开始使用 Xamarin.Android 使用 Android Nougat，必须下载并安装最新工具和 SDK 包，然后才能创建 Android Nougat 项目：

1.  从 Xamarin 安装最新的 Xamarin.Android 更新。

2.  安装**Android 7.0 (API 24)** 包和工具或更高版本。

3.  创建新的 Xamarin.Android 项目面向 Android Nougat。

4.  为 Android Nougat 配置仿真器或设备。

以下部分解释了每个步骤：


### <a name="install-xamarin-updates"></a>安装 Xamarin 更新

若要添加对 Android Nougat Xamarin 支持，将更新通道在 Visual Studio 或 Visual Studio for Mac 更改为稳定通道并应用最新的更新。 如果您还需要仅在 Alpha 或 Beta 通道中当前可用的功能，您可以切换到 Alpha 或 Beta 通道 （Alpha 和 Beta 通道还提供支持用于 Android 7.x）。 有关如何更改更新 （版本） 通道的信息，请参阅[更改更新通道](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/change_updates_channel)。



### <a name="install-the-android-sdk"></a>安装 Android SDK

若要使用 Xamarin Android 7.0 创建项目，您必须首先使用 Android SDK 管理器来安装**SDK 平台 Android N (API 24)** 或更高版本。 你还必须安装最新**Android SDK Tools**:

1.  启动 Android SDK 管理器 (在 Visual Studio for Mac 中，使用**工具 > 打开 Android SDK 管理器&hellip;**; 在 Visual Studio 中，使用**工具 > Android > Android SDK 管理器**)。

2.  安装**Android 7.0 (API 24)** 或更高版本：

    [![选择 Android SDK 管理器中的 Android 7.0 包](nougat-images/preview-packages.png)](nougat-images/preview-packages.png#lightbox)

3.  安装最新的 Android SDK 工具：

    [![选择 Android SDK 管理器中的最新的 Android SDK 工具](nougat-images/preview-tools.png)](nougat-images/preview-tools.png#lightbox)

    必须安装 Android SDK Tools 修订 25.2.2 或更高版本、 Android SDK 平台工具 24.0.3 或更高版本，以及 Android SDK 生成工具 24.0.2 或更高版本。

4.  确认**Java Development Kit 位置**为 JDK 1.8 配置：

    [![配置工具选项下的 JDK 8 路径](nougat-images/use-jdk-1.8.png)](nougat-images/use-jdk-1.8.png#lightbox)

    若要在 Visual Studio 中查看此设置，请单击**工具 > 选项 > Xamarin > Android 设置**。 在 Visual Studio for Mac 中，单击**首选项 > 项目 > SDK 位置 > Android**。



### <a name="start-a-xamarinandroid-project"></a>启动 Xamarin.Android 项目

创建新的 Xamarin.Android 项目。 如果您不熟悉如何使用 Xamarin 进行 Android 开发，请参阅[Hello，Android](~/android/get-started/hello-android/index.md)若要了解有关创建 Xamarin.Android 项目。

创建 Android 项目时，必须配置的版本设置应用到目标 Android 7.0 或更高版本。 例如，若要面向的 Android 7.0 你的项目，必须配置您的项目的目标 Android API 级别**Android 7.0 (API 24-Nougat)**。 建议，将目标框架设置为 API 24 或更高版本。 有关配置 Android API 级别级别的详细信息，请参阅[了解 Android API 级别](~/android/app-fundamentals/android-api-levels.md)。


> [!NOTE]
> 当前必须设置**最低 Android 版本**到**Android 7.0 (API 24-Nougat)** 若要将您的应用程序部署到 Android Nougat 设备或仿真程序。



### <a name="configure-an-emulator-or-device"></a>配置仿真器或设备

如果使用仿真程序，启动 Android AVD 管理器，并创建新的设备使用以下设置：

-   设备:Nexus 5 X，Nexus 6，Nexus 6 P，Nexus 播放器 Nexus 9 或像素 c。
-   目标:Android 7.0-API 级别 24
-   ABI: x86 或 x86\_64

例如，此虚拟设备被配置为模拟 Nexus 6:

[![配置使用 Nexus 6 设备、 Android 7.0 目标和 Intel Atom x86 CPU/ABI 的 AVD](nougat-images/android-n-avd.png)](nougat-images/android-n-avd.png#lightbox)

如果使用的物理设备，如 Nexus 5 X、 6 或 9，可以通过无线 (OTA) 更新通过自动更新你的设备或下载的系统映像并直接刷新你的设备。 有关手动将设备更新到 Android Nougat 的详细信息，请参阅[OTA 映像用于 Nexus 设备](https://developers.google.com/android/nexus/ota)。

请注意，Nexus 5 设备不受 Android Nougat。



## <a name="new-features"></a>新增功能

Android Nougat 引入了各种新功能和功能，例如多窗口支持、 通知增强功能和数据保护程序。 以下部分重点介绍了这些特性，并提供链接，以帮助你开始在应用中使用它们。



### <a name="multi-window-mode"></a>多窗口模式

多窗口模式使用户可以同时打开两个应用具有完整的多任务处理支持。 这些应用可以在拆分屏幕模式下运行并行 （横向） 或一个-上面--其他 （纵向）。
用户可以拖动以调整其大小在应用之间的分隔符，并且他们可以剪切和粘贴的内容应用程序之间。 两个应用都显示在多窗口模式下，所选的活动继续运行而未选定的活动会暂停，但仍然可见。 多窗口模式下不会修改 Android 活动生命周期。

[![在纵向和横向模式的多窗口模式下运行的示例应用](nougat-images/multi-window-mode.png)](nougat-images/multi-window-mode.png#lightbox)

你可以配置活动的 Xamarin.Android 应用程序如何支持多窗口模式下运行。 例如，你可以配置在多窗口模式下设置的最小大小的默认高度和宽度的您的应用程序的属性。 你可以使用新`Activity.IsInMultiWindowMode`属性以确定您的活动是否在多窗口模式下。 例如：

```csharp
if (!IsInMultiWindowMode) {
    multiDisabledMessage.Visibility = ViewStates.Visible;
} else {
    multiDisabledMessage.Visibility = ViewStates.Gone;
}
```

[MultiWindowPlayground](https://developer.xamarin.com/samples/monodroid/android-n/MultiWindowPlayground/)示例应用包含 C# 代码演示了如何利用多个窗口具有您的应用程序用户界面。

有关多窗口模式的详细信息，请参阅[多窗口支持](https://developer.android.com/guide/topics/ui/multi-window.html)。



### <a name="enhanced-notifications"></a>增强的通知

Android Nougat 引入了重新设计的通知系统。 它提供了一个新的直接答复功能，使用户快速通知的通知 UI 中直接传入短信回复。 从 Android 7.0，消息可以捆绑在一起作为一个组时收到多条消息的通知开始。 此外，开发人员可以自定义视图，利用系统修饰在通知中，并充分利用新的通知模板时生成通知的通知。


#### <a name="direct-reply"></a>直接回复

当用户收到传入消息的通知时，Android Nougat 使得回复中通知的消息 （而不是打开消息传送应用程序发送回复）。
此内联答复功能使用户能够快速响应直接在通知接口中的短信或文本消息：

[![包含内联直接回复字段的通知的屏幕截图](nougat-images/notifications-inline-reply-sml.png)](nougat-images/notifications-inline-reply.png#lightbox)

若要在您的应用程序中支持此功能，必须添加*内联答复操作*到你的应用通过[RemoteInput](https://developer.xamarin.com/api/type/Android.App.RemoteInput/)对象，以便用户可以直接从通知 UI 回复信作为联系方式。
例如，下面的代码生成`RemoteInput`用于接收文本输入，生成答复操作挂起意向并创建远程输入已启用的操作：

```csharp
// Build a RemoteInput for receiving text input:
var remoteInput = new Android.Support.V4.App.RemoteInput.Builder (EXTRA_REMOTE_REPLY)
    .SetLabel (GetString (Resource.String.reply))
    .Build ();

// Build a Pending Intent for the reply action to trigger:
PendingIntent replyIntent = PendingIntent.GetBroadcast (ApplicationContext,
                                conversation.ConversationId,
                                GetMessageReplyIntent (conversation.ConversationId),
                                PendingIntentFlags.UpdateCurrent);

// Build an Android 7.0 compatible Remote Input enabled action:
NotificationCompat.Action actionReplyByRemoteInput = new NotificationCompat.Action.Builder (
    Resource.Drawable.notification_icon,
    GetString (Resource.String.reply),
    replyIntent).AddRemoteInput (remoteInput).Build ();
```

此操作添加到通知：

```csharp
// Create the notification:
NotificationCompat.Builder builder = new NotificationCompat.Builder (ApplicationContext)
   .SetSmallIcon (Resource.Drawable.notification_icon)
   ...
   .AddAction (actionReplyByRemoteInput);
```

[消息传递服务](https://developer.xamarin.com/samples/monodroid/android-n/MessagingService/)示例应用包含 C# 代码演示如何扩展使用通知`RemoteInput`对象。 有关添加内联答复详细信息到您的应用程序的操作的 Android 7.0 或更高版本，请参阅 Android[答复通知](https://developer.android.com/guide/topics/ui/notifiers/notifications.html#direct)主题。


#### <a name="bundled-notifications"></a>捆绑的通知

Android Nougat 可以通知消息分组 （例如，通过消息主题），并显示组，而不是每个单独的消息。
这*捆绑通知*功能使用户可以关闭或存档中一个操作的通知组。 用户可以向下滑动，若要展开的通知，以查看详细信息中的每个通知捆绑包：

[![捆绑的通知的屏幕截图示例](nougat-images/bundled-notifications-sml.png)](nougat-images/bundled-notifications.png#lightbox)

若要支持捆绑的通知，您的应用程序可以使用[Builder.SetGroup](https://developer.xamarin.com/api/member/Android.App.Notification+Builder.SetGroup/p/System.String/)方法捆绑类似的通知。 有关在 Android N 捆绑的通知组的详细信息，请参阅 Android[捆绑通知](https://developer.android.com/guide/topics/ui/notifiers/notifications.html#bundle)主题。


#### <a name="custom-views"></a>自定义视图

Android Nougat 使您可以使用系统通知标头、 操作和可扩展布局创建自定义通知视图。 有关 Android Nougat 中的自定义通知视图的详细信息，请参阅 Android[通知增强功能](https://developer.android.com/about/versions/nougat/android-7.0.html#notification_enhancements)主题。



### <a name="data-saver"></a>数据保护程序

从 Android Nougat 开始，用户可以启用一个新*数据保护程序*设置阻止后台数据使用情况。 此设置还向您的应用程序在前台中使用较少的数据，只要有可能发出信号。 [ConnectivityManager](https://developer.xamarin.com/api/type/Android.Net.ConnectivityManager/)延长了 Android Nougat，以便您的应用程序可以检查，以便您的应用程序可以致力于限制其数据使用情况数据保护程序启用时，用户是否已启用数据保护程序。

有关 Android Nougat 中新的数据保护程序功能的详细信息，请参阅 Android[优化网络数据使用情况](https://developer.android.com/training/basics/network-ops/data-saver.html)主题。



### <a name="app-shortcuts"></a>应用程序快捷方式

引入了 android 7.1*应用程序快捷方式*，可以让用户快速开始常见或推荐任务与你的应用的功能。
若要激活的快捷方式菜单，用户 long 类型的值的按应用图标第二个或更多&ndash;菜单显示时有快速振动。
释放按下了会导致要保持的菜单：

[![消息传送应用程序应用程序快捷方式菜单的示例屏幕](nougat-images/app-shortcuts-sml.png)](nougat-images/app-shortcuts.png#lightbox)

此功能是可用唯一 API 级别 25 或更高版本。
有关 Android 7.1 中的新应用程序快捷方式功能的详细信息，请参阅 Android[应用程序快捷方式](https://developer.android.com/guide/topics/ui/shortcuts.html)主题。


### <a name="sample-code"></a>代码示例

多个 Xamarin.Android 示例可以用来说明如何利用 Android Nougat 功能：

-   [MultiWindowPlayground](https://developer.xamarin.com/samples/monodroid/android-n/MultiWindowPlayground/)演示如何使用 Android Nougat 中提供的多窗口 API。 可切换到多 windows 模式，以查看它如何影响应用程序的生命周期和行为的示例应用程序。

-   [消息传递服务](https://developer.xamarin.com/samples/monodroid/android-n/MessagingService/)发送通知的简单服务使用`NotificationCompatManager`。 它还扩展了该通知`RemoteInput`对象，以允许 Android Nougat 设备回复文本通过直接从通知，而无需打开应用。

-   [活动通知](https://developer.xamarin.com/samples/monodroid/android-n/ActiveNotifications/)演示了如何使用`NotificationManager`API 来告诉您当前正在显示你的应用程序的通知数。

-   [限定的目录访问权限](https://developer.xamarin.com/samples/monodroid/android-n/ScopedDirectoryAccess/)演示了如何使用作用域内的目录访问 API 可以轻松地访问特定的目录。 这将作为无需定义一种替代方法`READ_EXTERNAL_STORAGE`或`WRITE_EXTERNAL_STORAGE`在清单中的权限。

-   [直接启动](https://developer.xamarin.com/samples/monodroid/android-n/DirectBoot/)说明了如何在设备加密时引导设备同时之前和之后输入任何用户 credentials(PIN/Pattern/Password) 是始终可用的存储中存储数据。


## <a name="summary"></a>总结

本文介绍 Android Nougat 并介绍了如何安装和配置最新的工具和包 Xamarin.Android 开发 Android Nougat 上。 它还提供指向示例的源代码，以帮助您入门中创建适用于 Android Nougat 应用 Android Nougat 中提供的关键功能的概述。


## <a name="related-links"></a>相关链接

- [面向开发人员的 android 7.1](https://developer.android.com/about/versions/nougat/android-7.1.html)
- [Xamarin Android 7.0 发行说明](https://developer.xamarin.com/releases/android/xamarin.android_7/xamarin.android_7.0/)
