---
title: Nougat 功能
description: 如何开始使用 Xamarin 开发适用于 Android 的应用 Nougat。
ms.prod: xamarin
ms.assetid: 5C74ABE2-C862-4ED0-8EA5-C7FEE5251D4B
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/02/2018
ms.openlocfilehash: 7a50488d8b18682dffd76dee582767c063920840
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2019
ms.locfileid: "68510509"
---
# <a name="nougat-features"></a>Nougat 功能

_如何开始使用 Xamarin 开发适用于 Android 的应用 Nougat。_

本文概述了 Android Nougat 中引入的功能, 介绍了如何为 Android Nougat 开发准备 Xamarin, 并提供指向示例应用程序的链接, 这些示例应用程序演示了如何使用中的 Android Nougat 功能Xamarin Android 应用。


## <a name="overview"></a>概述

[Android Nougat](https://developer.android.com/about/versions/nougat/android-7.0.html)是 Google 对 Android 6.0 Marshmallow 的跟踪。 Xamarin android 在 Xamarin Android 7.0 及更高版本中提供对**android 7、Windows 绑定**的支持。 Android Nougat 为如下所述的 Nougat 功能添加了许多新 Api;使用 Xamarin 7.0 时, 这些 Api 可供 Xamarin Android 应用使用。

[![Android 平板电脑和运行 Android 的手机的 Nougat](nougat-images/android-n-hero-sml.png)](nougat-images/android-n-hero.png#lightbox)

有关 Android 7、windows Api 的详细信息, 请参阅[android 7.1 For 开发人员](https://developer.android.com/preview/api-overview.html)。
有关已知的 Xamarin 7.0 问题的列表, 请参阅[发行说明](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/android/xamarin.android_7/xamarin.android_7.0/index.md)。

Android Nougat 为 Xamarin Android 开发人员提供了许多新功能。 这些功能包括：

-   **多窗口支持**&ndash;这种增强功能使得用户可以一次在屏幕上打开两个应用。

-   **通知增强功能**Android Nougat 中经过重新设计的通知系统包括*直接答复*功能, 该功能允许用户直接从通知 UI 中快速响应短信。 &ndash; 此外, 如果你的应用程序为收到的消息创建了通知, 则在收到多条消息时, 新的*捆绑通知*功能可以将通知一起作为一个组进行捆绑。

-   **数据保护程序**&ndash;此功能是一项新的系统服务, 可帮助减少应用程序使用的手机网络数据; 它使用户能够控制应用使用手机网络数据的方式。

此外, Android Nougat 还为应用开发人员带来了许多其他的增强功能, 如新的网络安全配置功能、Doze、关键证明、新快速设置 Api、多区域设置支持、ICU4J Api、Web 视图增强功能对 Java 8 语言功能的访问, 其作用域为目录访问, 自定义指针 API, 平台 VR 支持, 虚拟文件和后台处理优化。

本文介绍了如何开始使用 Android Nougat 构建应用程序以试用新功能, 并计划迁移或功能工作以面向新的 Android Nougat 平台。


## <a name="requirements"></a>要求

若要在基于 Xamarin 的应用中使用新的 Android Nougat 功能, 需要满足以下要求:

-   **Visual Studio 或 Visual Studio for Mac**&ndash;如果你使用的是 Visual Studio, 则需要4.2.0.628 或更高版本的 Visual Studio Tools for Xamarin。 如果使用 Visual Studio for Mac, 则需要 Visual Studio for Mac 版本6.1.0 或更高版本。

-   必须安装和配置**Xamarin** xamarin7.0或更高版本,并且必须安装VisualStudio或VisualStudioforMac。&ndash;

-   **Android SDK** Android SDK 7.0 (API 24) 或更高版本必须通过 Android SDK 管理器进行安装。

-   **Java 开发人员工具包**Xamarin Android 7.0 开发需要[JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)或更高版本, 前提是你要针对 API 级别24或更高版本进行开发 (JDK 8 还支持早于24的 api 级别)。 &ndash; 如果使用的是自定义控件或窗体预览器, 则需要使用64位版本的 JDK 8。

> [!IMPORTANT]
> Xamarin.Android 不支持 JDK 9。

请注意, 必须使用 Xamarin C6SR4 或更高版本重新生成应用, 才能使用 Android Nougat 可靠地工作。 由于 Android Nougat 只能链接到[NDK 提供的本机库](https://developer.android.com/about/versions/nougat/android-7.0-changes.html), 因此在 Android Nougat 上运行时, 使用这些库的现有**应用可能会**崩溃, 如果未正确重新生成它们。



## <a name="getting-started"></a>入门

若要开始使用适用于 Xamarin 的 Android Nougat, 必须先下载并安装最新的工具和 SDK 包, 然后才能创建 Android Nougat 项目:

1.  从 Xamarin 安装最新的 Xamarin Android 更新。

2.  安装**Android 7.0 (API 24)** 包和工具或更高版本。

3.  创建面向 Android Nougat 的新 Xamarin 项目。

4.  为 Android Nougat 配置模拟器或设备。

以下各部分对这些步骤进行了说明:


### <a name="install-xamarin-updates"></a>安装 Xamarin 更新

若要添加适用于 Android Nougat 的 Xamarin 支持, 请将 Visual Studio 中的更新通道或 Visual Studio for Mac 更改为稳定通道, 并应用最新的更新。 如果你还需要目前仅在 Alpha 或 Beta 通道中可用的功能, 则可以切换到 Alpha 或 Beta 通道 (Alpha 和 Beta 通道还支持 Android 7. x)。 有关如何更改更新 (发行版) 通道的信息, 请参阅[更改更新通道](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/change_updates_channel)。



### <a name="install-the-android-sdk"></a>安装 Android SDK

若要使用 Xamarin Android 7.0 创建项目, 必须首先使用 Android SDK 管理器安装**SDK 平台 Android N (API 24)** 或更高版本。 还必须安装最新**Android SDK Tools**:

1.  启动 Android SDK 管理器 (在 Visual Studio for Mac 中, 在 Visual Studio 中使用**工具 > 打开 Android SDK&hellip;管理器**; 在 Visual Studio 中, 使用**工具 > Android > Android SDK Manager**)。

2.  安装**Android 7.0 (API 24)** 或更高版本:

    [![在 Android SDK 管理器中选择 Android 7.0 包](nougat-images/preview-packages.png)](nougat-images/preview-packages.png#lightbox)

3.  安装最新的 Android SDK 工具:

    [![在 Android SDK 管理器中选择最新的 Android SDK 工具](nougat-images/preview-tools.png)](nougat-images/preview-tools.png#lightbox)

    必须安装 Android SDK Tools 修订版本25.2.2 或更高版本, Android SDK 平台工具 v24.0.3 或更高版本, 并 Android SDK 生成工具24.0.2 或更高版本。

4.  验证是否为 JDK 1.8 配置了**Java 开发工具包位置**:

    [![在 "工具选项" 下配置 JDK 8 路径](nougat-images/use-jdk-1.8.png)](nougat-images/use-jdk-1.8.png#lightbox)

    若要在 Visual Studio 中查看此设置, 请单击 "**工具" > 选项 "> Xamarin > Android 设置**"。 在 Visual Studio for Mac 中, 单击 "**首选项" > 项目 > SDK 位置 > Android**"。



### <a name="start-a-xamarinandroid-project"></a>启动 Xamarin Android 项目

创建新的 Xamarin Android 项目。 如果不熟悉通过 Xamarin 进行 Android 开发, 请参阅[Hello, Android](~/android/get-started/hello-android/index.md)了解如何创建 xamarin Android 项目。

创建 Android 项目时, 必须将版本设置配置为面向 Android 7.0 或更高版本。 例如, 若要以适用于 Android 7.0 的项目为目标, 必须将项目的目标 Android API 级别配置为**android 7.0 (API Nougat)** 。 建议将目标框架级别设置为 API 24 或更高版本。 有关配置 Android API 级别级别的详细信息, 请参阅[了解 ANDROID Api 级别](~/android/app-fundamentals/android-api-levels.md)。


> [!NOTE]
> 目前, 必须将**最低 android 版本**设置为**ANDROID 7.0 (API 24-Nougat)** , 以将应用部署到 android Nougat 设备或仿真程序。



### <a name="configure-an-emulator-or-device"></a>配置仿真程序或设备

如果使用的是仿真程序, 请使用以下设置启动 Android AVD Manager 并创建新设备:

-   设备:结点5X、结点6、结点周一至、结点播放机、结点9或像素 C。
-   目标:Android 7.0-API 级别24
-   ABI: x86 或 x86\_64

例如, 此虚拟设备配置为模拟结点 6:

[![使用结点6设备、Android 7.0 目标和 Intel Atom x86 CPU/ABI 配置 AVD](nougat-images/android-n-avd.png)](nougat-images/android-n-avd.png#lightbox)

如果你使用的是物理设备, 例如, 一个结点5、6或 9, 则可以通过无线 (OTA) 更新来更新设备, 或下载系统映像并直接刷新设备。 有关手动将设备更新到 Android Nougat 的详细信息, 请参阅[用于结点设备的 OTA 映像](https://developers.google.com/android/nexus/ota)。

请注意, Android Nougat 不支持 "结点5设备"。



## <a name="new-features"></a>新增功能

Android Nougat 引入了多种新特性和功能, 如多窗口支持、通知增强功能和数据保护程序。 以下各节重点介绍这些功能, 并提供可帮助你在应用程序中开始使用这些功能的链接。



### <a name="multi-window-mode"></a>多窗口模式

多窗口模式使用户能够一次打开两个应用程序, 并提供完全的多任务支持。 这些应用可在拆分屏幕模式下并行运行 (横向) 或上单 (纵向)。
用户可以在应用之间拖动分隔线以调整其大小, 并且可以在应用之间剪切和粘贴内容。 当在多窗口模式下显示两个应用时, 所选活动将继续运行, 同时未选定的活动将暂停, 但仍可见。 多窗口模式不会修改 Android 活动生命周期。

[![纵向和横向模式下在多窗口模式下运行的示例应用](nougat-images/multi-window-mode.png)](nougat-images/multi-window-mode.png#lightbox)

你可以配置 Xamarin Android 应用程序的活动如何支持多窗口模式。 例如, 你可以配置属性, 以便在多窗口模式下设置应用的最小大小和默认高度和宽度。 您可以使用新`Activity.IsInMultiWindowMode`属性来确定您的活动是否处于多窗口模式下。 例如：

```csharp
if (!IsInMultiWindowMode) {
    multiDisabledMessage.Visibility = ViewStates.Visible;
} else {
    multiDisabledMessage.Visibility = ViewStates.Gone;
}
```

[MultiWindowPlayground](https://developer.xamarin.com/samples/monodroid/android-n/MultiWindowPlayground/)示例应用包含C#的代码演示如何利用多个 windows 用户界面和应用。

有关多窗口模式的详细信息, 请参阅[多窗口支持](https://developer.android.com/guide/topics/ui/multi-window.html)。



### <a name="enhanced-notifications"></a>增强的通知

Android Nougat 引入了重新设计的通知系统。 它具有新的直接答复功能, 使用户能够在通知 UI 中直接回复传入文本消息的通知。 从 Android 7.0 开始, 接收多个消息时, 通知消息可以作为单个组捆绑在一起。 此外, 开发人员还可以自定义通知视图、利用通知中的系统装饰品, 并在生成通知时利用新的通知模板。


#### <a name="direct-reply"></a>直接答复

当用户收到传入消息的通知时, Android Nougat 可以在通知中回复消息 (而不是打开消息传递应用程序来发送答复)。
利用这种内嵌答复功能, 用户可以直接在通知界面中快速响应短信或短信:

[![带有内联直接答复字段的通知屏幕截图](nougat-images/notifications-inline-reply-sml.png)](nougat-images/notifications-inline-reply.png#lightbox)

若要在应用中支持此功能, 必须通过[RemoteInput](xref:Android.App.RemoteInput)对象向应用添加*内联答复操作*, 以便用户可以直接从通知 UI 中通过文本进行回复。
例如, 下面的代码生成`RemoteInput`用于接收文本输入的, 为答复操作生成挂起意向, 并创建远程输入启用的操作:

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

此操作已添加到通知:

```csharp
// Create the notification:
NotificationCompat.Builder builder = new NotificationCompat.Builder (ApplicationContext)
   .SetSmallIcon (Resource.Drawable.notification_icon)
   ...
   .AddAction (actionReplyByRemoteInput);
```

[消息服务](https://developer.xamarin.com/samples/monodroid/android-n/MessagingService/)示例应用包含C#的代码演示如何使用`RemoteInput`对象扩展通知。 有关向适用于 Android 7.0 或更高版本的应用添加内联答复操作的详细信息, 请参阅 Android[答复通知](https://developer.android.com/guide/topics/ui/notifiers/notifications.html#direct)主题。


#### <a name="bundled-notifications"></a>捆绑的通知

Android Nougat 可以将通知消息组合在一起 (例如, 按消息主题), 并显示组而不是每个单独的消息。
此*捆绑式通知*功能使用户能够在一次操作中消除或存档一组通知。 用户可以向下滑动以展开通知绑定, 以详细查看每个通知:

[![绑定通知的屏幕截图示例](nougat-images/bundled-notifications-sml.png)](nougat-images/bundled-notifications.png#lightbox)

为了支持捆绑式通知, 应用程序可以使用[SetGroup](xref:Android.App.Notification.Builder.SetGroup*)方法来捆绑类似的通知。 有关 Android N 中捆绑的通知组的详细信息, 请参阅 Android[捆绑通知](https://developer.android.com/guide/topics/ui/notifiers/notifications.html#bundle)主题。


#### <a name="custom-views"></a>自定义视图

Android Nougat 使你可以通过系统通知标头、操作和可展开布局创建自定义通知视图。 有关 Android Nougat 中的自定义通知视图的详细信息, 请参阅 Android[通知增强](https://developer.android.com/about/versions/nougat/android-7.0.html#notification_enhancements)主题。



### <a name="data-saver"></a>数据保护程序

从 Android Nougat 开始, 用户可以启用阻止后台数据使用的新的*数据保护程序*设置。 此设置还通知你的应用程序在前台使用的数据较少。 [ConnectivityManager](xref:Android.Net.ConnectivityManager)已在 Android Nougat 中扩展, 以便您的应用程序可以检查用户是否已启用数据保护程序, 以便您的应用程序在启用数据保护程序时可以尽力限制其数据使用量。

有关 Android Nougat 中新增的数据保护程序功能的详细信息, 请参阅 Android[优化网络数据使用](https://developer.android.com/training/basics/network-ops/data-saver.html)主题。



### <a name="app-shortcuts"></a>应用快捷方式

Android 7.1 引入了*应用快捷*功能, 使用户可以使用应用快速启动常见或推荐的任务。
为了激活快捷方式的菜单, 用户长时间按下一个或多&ndash;个菜单的应用图标时, 会显示一个快速振动。
释放按下会导致菜单保持:

[![用于消息应用的应用快捷菜单的示例屏幕](nougat-images/app-shortcuts-sml.png)](nougat-images/app-shortcuts.png#lightbox)

此功能仅适用于 API 级别25或更高版本。
有关 Android 7.1 中新应用快捷功能的详细信息, 请参阅 Android[应用快捷方式](https://developer.android.com/guide/topics/ui/shortcuts.html)主题。


### <a name="sample-code"></a>代码示例

提供了多个 Xamarin 示例, 用于演示如何利用 Android Nougat 功能:

-   [MultiWindowPlayground](https://developer.xamarin.com/samples/monodroid/android-n/MultiWindowPlayground/)演示如何使用 Android Nougat 中提供的多窗口 API。 您可以将示例应用程序切换为多窗口模式, 以查看它如何影响应用程序的生命周期和行为。

-   [消息服务](https://developer.xamarin.com/samples/monodroid/android-n/MessagingService/)是一种使用`NotificationCompatManager`发送通知的简单服务。 它还使用`RemoteInput`对象扩展通知, 以允许 Android Nougat 设备直接从通知中回复文本, 而无需打开应用。

-   [活动通知](https://developer.xamarin.com/samples/monodroid/android-n/ActiveNotifications/)演示了如何使用`NotificationManager` API 告诉你应用程序当前正在显示的通知数。

-   [作用域的目录访问](https://developer.xamarin.com/samples/monodroid/android-n/ScopedDirectoryAccess/)演示如何使用限定作用域的目录访问 API 轻松访问特定的目录。 这是必须在清单中定义`READ_EXTERNAL_STORAGE`或`WRITE_EXTERNAL_STORAGE`权限的替代方法。

-   [直接启动](https://developer.xamarin.com/samples/monodroid/android-n/DirectBoot/)说明如何将数据存储在设备加密存储中, 该存储始终可用, 同时在输入任何用户凭据 (PIN/模式/密码) 前后启动设备。


## <a name="summary"></a>总结

本文介绍了 Android Nougat, 并介绍了如何安装和配置适用于 Android Nougat 上的 Xamarin 开发的最新工具和包。 它还概述了 Android Nougat 中提供的主要功能, 并提供了指向示例源代码的链接, 可帮助你开始创建适用于 Android 的应用程序 Nougat。


## <a name="related-links"></a>相关链接

- [适用于开发人员的 Android 7。1](https://developer.android.com/about/versions/nougat/android-7.1.html)
- [Xamarin Android 7.0 发行说明](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/android/xamarin.android_7/xamarin.android_7.0/index.md)
