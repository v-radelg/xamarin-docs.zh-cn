---
title: Android 9 Pie
description: 如何开始使用 Xamarin 开发适用于 Android 9 的应用。
ms.prod: xamarin
ms.assetid: 6575DD32-9DC8-44E6-85EF-1F8BD07D3780
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 08/21/2018
ms.openlocfilehash: 0105b43116df697bc6688becb77298c236dfa601
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73019877"
---
# <a name="android-pie-features"></a>Android 饼图功能

_如何开始使用 Xamarin 开发适用于 Android 9 的应用。_

[Android 9 饼图](https://developer.android.com/about/versions/pie/)现在可从 Google 获取。 此版本提供了许多新功能和 Api，其中许多新功能是在最新 Android 设备中利用新硬件功能所必需的。

![Android 饼图英雄图像](pie-images/01-android-p-logo.png)

本文旨在帮助你开始开发适用于 Android 的 Xamarin Android 应用程序。 它说明了如何安装所需的更新、如何配置 SDK，并准备好用于测试的模拟器或设备。 它还提供了 Android 饼图中新增功能的概述，并提供了演示如何使用某些主要 Android 饼图功能的示例源代码。

Xamarin 9.0 支持 Android 饼图。 有关适用于 Android 的 Xamarin Android 支持的详细信息，请参阅[Android P 开发人员预览版 3](https://docs.microsoft.com/xamarin/android/release-notes/9/9.0/#android-p-dp1)发行说明。

## <a name="requirements"></a>要求

以下列表是在基于 Xamarin 的应用中使用 Android 饼图功能所必需的：

- 建议使用**Visual studio** &ndash; visual studio 2019。
    如果你使用的是 Visual Studio 2017，则在 Windows update 上，Visual Studio 2017 版本15.8 或更高版本。 在 macOS 上，更新为适用于 Mac 版本7.6 或更高版本的 Visual Studio 2017。

- **Xamarin** &ndash; xamarin 9.0.0.17 或更高版本必须随 Visual Studio 一起安装（在采用 .net 工作负载的**移动开发**过程中，将自动安装 xamarin）。

- **Java 开发人员工具包**&ndash; Xamarin Android 9.0 开发需要[JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) （或者，你可以尝试预览 Microsoft [OpenJDK](~/android/get-started/installation/openjdk.md)的版本）。 在采用 .NET 工作负载的**移动开发**过程中，将自动安装 JDK8。

- 必须通过 Android SDK 管理器安装**Android SDK** &ndash; Android SDK API 28 或更高版本。

## <a name="getting-started"></a>入门

若要开始开发带有 Xamarin 的 Android 饼图应用，必须先下载并安装最新的工具和 SDK 包，然后才能创建第一个 Android 饼图项目：

1. 建议使用 Visual Studio 2019。 如果你使用的是 Visual Studio 2017，请更新到[Visual studio 2017 版本 15.8](https://docs.microsoft.com/visualstudio/releasenotes/vs2017-relnotes)或更高版本。 如果使用 Visual Studio for Mac，请更新为[适用于 Mac 版本7.6 或更高版本的 Visual Studio 2017](https://docs.microsoft.com/visualstudio/releasenotes/vs2017-relnotes) 。

2. 通过 SDK 管理器安装**Android 饼图（API 28）** 包和工具。

3. 创建面向**android 9.0**的新 Xamarin 项目。

4. 配置仿真程序或设备以便测试 Android 饼图应用。

以下各部分对这些步骤进行了说明：

### <a name="update-visual-studio"></a>更新 Visual Studio

建议使用 Visual Studio 2019 通过 Xamarin 构建 Android 应用程序。

如果你使用的是 Visual Studio 2017，请更新到 Visual Studio 2017 版本15.8 或更高版本（有关说明，请参阅[将 Visual studio 2017 更新到最新版本](https://docs.microsoft.com/visualstudio/install/update-visual-studio)）。 在 macOS 上，更新到 Visual Studio 2017 for Mac 7.6 或更高版本（有关说明，请参阅[设置和安装 Visual Studio for Mac](https://docs.microsoft.com/visualstudio/mac/installation)）。

### <a name="install-the-android-sdk"></a>安装 Android SDK

若要使用 Xamarin 9.0 创建项目，必须首先使用 Android SDK 管理器安装适用于 Android 的 SDK 平台 **（API 级别28）** 或更高版本。

1. 启动 SDK 管理器。 在 Visual Studio 中，单击 "**工具" > Android > Android SDK 管理器**"。 在 Visual Studio for Mac 中，单击 "**工具" > "SDK 管理器**"。

2. 在右下角，单击齿轮图标，并选择 "**存储库 > Google （不支持）** "：

    [将存储库设置为 Google![](pie-images/vs/set-repo-sml.png)](pie-images/vs/set-repo.png#lightbox)

3. 安装**Android 饼图**SDK 包，这些包在 "**平台**" 选项卡中作为**Android SDK 平台 28**列出（有关使用 SDK 管理器的详细信息，请参阅[Android SDK 安装](~/android/get-started/installation/android-sdk.md)）：

    [安装 Android 饼图![](pie-images/vs/sdk-manager-sml.png)](pie-images/vs/sdk-manager.png#lightbox)

4. 如果使用的是模拟器，请创建支持**API 级别 28**的虚拟设备。 有关创建虚拟设备的详细信息，请参阅[用 Android Device Manager 管理虚拟设备](~/android/get-started/installation/android-emulator/device-manager.md)。

### <a name="start-a-xamarinandroid-project"></a>启动 Xamarin Android 项目

创建新的 Xamarin Android 项目。 如果不熟悉通过 Xamarin 进行 Android 开发，请参阅[Hello，Android](~/android/get-started/hello-android/index.md)了解如何创建 xamarin Android 项目。

创建 Android 项目时，必须将版本设置配置为面向 Android 9.0 或更高版本。 例如，若要以适用于 Android 的项目为目标，必须将项目的目标 Android API 级别配置为**android 9.0** （API 28）。 建议也将目标框架级别设置为 API 28 或更高版本。 有关配置 Android API 级别的详细信息，请参阅[了解 ANDROID Api 级别](~/android/app-fundamentals/android-api-levels.md)。

### <a name="configure-a-device-or-emulator"></a>配置设备或仿真程序

如果使用的是物理设备，如结点或像素，则可以按照[为结点和像素设备出厂映像](https://developers.google.com/android/images)中的说明，将设备更新到 Android 饼图。

如果你使用的是仿真程序，请为 API 级别28创建虚拟设备，并选择基于 x86 的映像。 有关使用 Android Device Manager 创建和管理虚拟设备的信息，请参阅[使用 Android Device Manager 管理虚拟设备](~/android/get-started/installation/android-emulator/device-manager.md)。
有关使用 Android 模拟器进行测试和调试的信息，请参阅[Android Emulator 上的调试](~/android/deploy-test/debugging/debug-on-emulator.md)。

## <a name="new-features"></a>新增功能

Android 饼图引入了多种新功能。 其中一些新功能旨在利用最新 Android 设备提供的新硬件功能，而其他功能旨在进一步增强 Android 用户体验：

- **显示切除支持**&ndash; 提供 api，以便在更高版本的 Android 设备上查找页面顶部的_切除_位置和形状。

- **通知增强**&ndash; 通知消息现在可以显示图像，而新的 `Person` 类用于简化会话参与者。

- **室内定位**对 WiFi 往返时间协议 &ndash; 平台支持，这使得应用可以使用 wifi 设备在室内设置中进行导航。

- **多相机支持**&ndash; 提供从多个物理照相机（如双面和双回相机）同时访问流的功能。

以下各节重点介绍了这些功能，并提供了简短的代码示例，帮助你在应用程序中开始使用它们。

### <a name="display-cutout-support"></a>显示切除支持

许多具有边缘到边缘屏幕的较新 Android 设备在相机和发言人显示的顶部都有一个*显示切除*（或 "凹槽"）。
以下屏幕截图提供了一个剪切块示例：

[模拟切口的![Android 仿真程序](pie-images/02-example-cutout-sml.png)](pie-images/02-example-cutout.png#lightbox)

为了管理应用程序窗口在带有显示切除的设备上显示其内容的方式，Android 饼图添加了一个新的[LayoutInDisplayCutoutMode](https://developer.android.com/reference/android/view/WindowManager.LayoutParams.html#layoutInDisplayCutoutMode)窗口布局特性。 此属性可设置为以下值之一：

- [LayoutInDisplayCutoutModeNever](https://developer.android.com/reference/android/view/WindowManager.LayoutParams.html#LAYOUT_IN_DISPLAY_CUTOUT_MODE_NEVER) &ndash; 该窗口不能与裁剪区域重叠。

- [LayoutInDisplayCutoutModeShortEdges](https://developer.android.com/reference/android/view/WindowManager.LayoutParams.html#LAYOUT_IN_DISPLAY_CUTOUT_MODE_SHORT_EDGES) &ndash; 允许该窗口扩展到剪切区域，而只显示在屏幕的短边。 

- [LayoutInDisplayCutoutModeDefault](https://developer.android.com/reference/android/view/WindowManager.LayoutParams.html#LAYOUT_IN_DISPLAY_CUTOUT_MODE_DEFAULT) &ndash; 如果在系统栏中包含切口，则允许该窗口扩展到切除区域。

例如，若要防止应用窗口与剪切区域重叠，请将布局剪切模式设置为 "*从不*"： 

```csharp
Window.Attributes.LayoutInDisplayCutoutMode =
    Android.Views.LayoutInDisplayCutoutMode.Never;
```

下面的示例提供了这些裁剪模式的示例。 左侧的第一个屏幕截图是应用处于非全屏模式。 在中心屏幕截图中，应用程序将进入全屏，`LayoutInDisplayCutoutMode` 设置为 `LayoutInDisplayCutoutModeShortEdges`。 请注意，应用程序的白色背景延伸到了显示切除区：

[![示例在模拟器中显示切除模式](pie-images/03-cutout-modes-sml.png)](pie-images/03-cutout-modes.png#lightbox)

在最后的屏幕截图（右侧）中，`LayoutInDisplayCutoutMode` 设置为 `LayoutInDisplayCutoutModeShortNever`，然后进入全屏。
请注意，不允许应用程序的白色背景延伸到显示切除区域中。

如果需要有关设备上的切除区域的详细信息，可以使用新的[DisplayCutout](https://developer.android.com/reference/android/view/DisplayCutout.html)类。 `DisplayCutout` 表示不能用于显示内容的显示区域。 您可以使用此信息检索切口的位置和形状，以便您的应用程序不会尝试在此非功能区中显示内容。

有关 Android P 中新的切除功能的详细信息，请参阅[显示切除支持](https://developer.android.com/about/versions/pie/android-9.0#cutout)。

### <a name="notifications-enhancements"></a>通知增强功能

Android 饼图引入了以下增强功能来改善消息传递体验：

- 通知通道（在[Android Oreo](~/android/platform/oreo.md)中引入）现在支持阻止通道组。

- 通知系统包含三个新的 "不干扰" 类别（确定警报、系统声音和媒体源的优先级）。 此外，还有七种新的 "不干扰" 模式，可用于阻止视觉中断（例如徽章、通知灯、状态栏外观和启动全屏活动）。

- 添加了一个新的[Person](https://developer.android.com/reference/android/app/Person.html)类来表示邮件的发件人。 此类的使用有助于通过标识会话中涉及的人员（包括其头像和 Uri）来优化每个通知的呈现。

- 通知现在可以显示图像。 

下面的示例演示如何使用新 Api 生成包含图像的通知。 在下面的屏幕截图中，将发布文本通知，并在其后面跟有嵌入图像的通知。 当扩展通知（如右侧所示）时，将显示第一条通知的文本，并扩大第二个通知中嵌入的图像：

[带图像的![示例通知](pie-images/04-example-notifications-sml.png)](pie-images/04-example-notifications.png#lightbox)

下面的示例演示如何在 Android 饼图通知中包括图像，并演示如何使用新的 `Person` 类：

1. 创建表示发送方的 `Person` 对象。 例如，发送方的名称和图标包含在 `fromPerson`中：

    ```csharp
    Icon senderIcon = Icon.CreateWithResource(this, Resource.Drawable.sender_icon);
    Person fromPerson = new Person.Builder()
        .SetIcon(senderIcon)
        .SetName("Mark Sender")
        .Build();
    ```

2. 创建一个包含要发送的映像的 `Notification.MessagingStyle.Message`，并将该图像传递到新的[MessagingStyle](https://developer.android.com/reference/android/app/Notification.MessagingStyle.Message.html#setData%28java.lang.String,%20android.net.Uri)方法。
   例如:

    ```csharp
    Uri imageUri = Uri.Parse("android.resource://com.xamarin.pminidemo/drawable/example_image");
    Notification.MessagingStyle.Message message = new Notification.MessagingStyle
            .Message("Here's a picture of where I'm currently standing", 0, fromPerson)
            .SetData("image/", imageUri);
    ```

3. 将消息添加到 `Notification.MessagingStyle` 的对象。 例如:

    ```csharp
    Notification.MessagingStyle style = new Notification.MessagingStyle(fromPerson)
            .AddMessage(message);
    ```

4. 将此样式插入通知生成器。 例如:

    ```csharp
    builder = new Notification.Builder(this, MY_CHANNEL)
        .SetContentTitle("Tour of the Colosseum")
        .SetContentText("I'm standing right here!")
        .SetSmallIcon(Resource.Mipmap.ic_notification)
        .SetStyle(style)
        .SetChannelId(MY_CHANNEL);
    ```

5. 发布通知。 例如:

    ```csharp
    const int notificationId = 1000;
    notificationManager.Notify(notificationId, builder.Build());
    ```

有关创建通知的详细信息，请参阅[本地通知](~/android/app-fundamentals/notifications/local-notifications.md)。

### <a name="indoor-positioning"></a>室内定位

Android 饼图提供对 IEEE 802.11 mc （也称为 Wifi 往返_时间_或_wifi RTT_）的支持，使应用能够检测到一个或多个 wi-fi 接入点的距离。 使用此信息，你的应用程序可以利用*室内定位*，精确到2米。 在为 IEEE 801.11 mc 提供硬件支持的 Android 设备上，你的应用可以提供导航功能，如智能设备的基于位置的控制，或者通过应用商店实现的 turn 说明：

[使用 WiFi RTT 的室内导航![示例](pie-images/05-wifi-rtt-sml.png)](pie-images/05-wifi-rtt.png#lightbox)

新的[WifiRttManager](https://developer.android.com/reference/android/net/wifi/rtt/WifiRttManager)类和几个帮助器类提供测量 wi-fi 设备距离的方法。 有关 Android P 中引入的室内定位 Api 的详细信息，请参阅[android .net。](https://developer.android.com/reference/android/net/wifi/rtt/package-summary)

### <a name="multi-camera-support"></a>多相机支持

许多较新的 Android 设备具有可用于作为立体声视觉对象、增强的视觉效果和改进的缩放功能的双前门和/或双回相机。 Android P 引入了新的[多相机](https://developer.android.com/about/versions/pie/android-9.0#camera)API，使应用程序可以使用由两个或多个物理照相机支持的*逻辑照相机*（或*逻辑多相机*）。
若要确定设备是否支持逻辑多相机，可以查看设备上每个相机的功能，以查看它是否支持[RequestAvailableCapabilitiesLogicalMultiCamera](https://developer.android.com/reference/android/hardware/camera2/CameraMetadata#REQUEST_AVAILABLE_CAPABILITIES_LOGICAL_MULTI_CAMERA)。

Android 饼图还包含一个新的[SessionConfiguration](https://developer.android.com/reference/android/hardware/camera2/params/SessionConfiguration.html)类，可用于帮助减少初始捕获期间的延迟，无需启动和启动照相机流。

有关 Android P 中的多相机支持的详细信息，请参阅[多照相机支持和照相机更新](https://developer.android.com/about/versions/pie/android-9.0#camera)。

### <a name="other-features"></a>其他功能

此外，Android 饼图还支持其他几项新功能：

- 新的[AnimatedImageDrawable](https://developer.android.com/reference/android/graphics/drawable/AnimatedImageDrawable.html)类，可用于绘制和显示动画图像。

- 替换 `BitmapFactory`的新的[ImageDecoder](https://developer.android.com/reference/android/graphics/ImageDecoder.html)类。 `ImageDecoder` 可用于对 `AnimatedImageDrawable`解码。

- 支持 HDR （高动态范围）视频和 HEIF （高效率图像文件格式）图像。

- [JobScheduler](https://developer.android.com/reference/android/app/job/JobScheduler.html)已经过增强，可以更智能地处理与网络相关的作业。 [JobParameters](https://developer.android.com/reference/android/app/job/JobParameters)类的新的[GetNetwork](https://developer.android.com/reference/android/app/job/JobParameters#getNetwork%28%29)方法返回最佳网络，以执行给定作业的任何网络请求。

有关最新 Android 饼图功能的详细信息，请参阅[android 9 功能和 api](https://developer.android.com/about/versions/pie/android-9.0)。

## <a name="behavior-changes"></a>行为变更

如果将 "目标 Android 版本" 设置为 "API 级别 28"，则即使未实现上述新功能，也可能会影响应用的行为。 以下列表概述了这些更改：

- 应用现在必须先请求前台权限，然后才能使用前台服务。

- 如果应用有多个进程，则不能跨进程共享单个[web](xref:Android.Webkit.WebView)程序数据目录。

- 不再允许按路径直接访问另一个应用的数据目录。

有关面向 Android P 的应用的行为更改的详细信息，请参阅[行为更改](https://developer.android.com/about/versions/pie/android-9.0-changes-all#p-apps)。

## <a name="sample-code"></a>示例代码

[AndroidPMiniDemo](https://github.com/xamarin/monodroid-samples/tree/master/android-p/AndroidPMiniDemo)是适用于 Android 的 Xamarin 示例应用程序，它演示了如何设置显示裁剪模式，如何使用新的 `Person` 类，以及如何发送包含图像的通知。

## <a name="summary"></a>总结

本文介绍了 Android 饼图，并说明了如何安装和配置适用于 Xamarin Android 开发的最新工具和包（android）。 其中提供了 Android 饼图中提供的主要功能的概述，其中包含其中几种功能的示例源代码。
它包含 API 文档和 Android 开发人员主题的链接，可帮助你开始创建 Android 应用程序。 它还重点介绍了可能影响现有应用的最重要的 Android 饼图行为更改。

## <a name="related-links"></a>相关链接

- [Android 9 饼图](https://developer.android.com/about/versions/pie/)
