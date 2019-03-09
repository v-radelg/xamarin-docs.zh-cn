---
title: Android 9 Pie
description: 如何开始开发应用程序使用 Xamarin.Android 的 Android 9 饼图。
ms.prod: xamarin
ms.assetid: 6575DD32-9DC8-44E6-85EF-1F8BD07D3780
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 08/21/2018
ms.openlocfilehash: fa41affc57714254a12623f79da3dc1396ecd009
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/08/2019
ms.locfileid: "57670126"
---
# <a name="android-pie-features"></a>饼图的 android 功能

_如何开始开发应用程序使用 Xamarin.Android 的 Android 9 饼图。_

[Android 9 饼图](https://developer.android.com/about/versions/pie/)现在也可通过 Google。 许多新功能和 Api 将在此版本中，提供和其中的许多所需的最新的 Android 设备中充分利用新的硬件能力。

![Android 饼图英雄形象](pie-images/01-android-p-logo.png)

本文旨在帮助您开始开发 Xamarin.Android 应用程序 Android 饼图。 它说明了如何安装所需更新、 配置 SDK，并准备好仿真器或设备进行测试。 它还提供了 Android 饼图中的新增功能的概述，并提供说明了如何使用某些关键的 Android 饼图功能的示例源代码。

Xamarin.Android 9.0 提供对 Android 饼图的支持。 有关 Android 饼图的 Xamarin.Android 支持的详细信息，请参阅[Android P 开发人员预览版 3](https://docs.microsoft.com/xamarin/android/release-notes/9/9.0/#android-p-dp1)发行说明。

## <a name="requirements"></a>要求

以下列表是所需的基于 Xamarin 的应用中使用 Android 饼图功能：

-   **Visual Studio** &ndash;如果使用的 Windows，更新到 Visual Studio 2017 15.8 或更高版本。 如果使用的是 Mac，更新到 Visual Studio 2017 for Mac 7.6 或更高版本。

-   **Xamarin.Android** &ndash; Xamarin.Android 9.0.0.17 或更高版本必须与 Visual Studio 一起安装 (作为的一部分自动安装 Xamarin.Android**使用.NET 的移动开发**工作负荷)。

-   **Java 开发人员工具包** &ndash; Xamarin Android 9.0 开发需要[JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) (或可以试用 Microsoft 的分发的预览版[OpenJDK](~/android/get-started/installation/openjdk.md))。 作为的一部分自动安装 JDK8**使用.NET 的移动开发**工作负荷。

-   **Android SDK** &ndash;必须通过 Android SDK 管理器安装 Android SDK API 28 或更高版本。

## <a name="getting-started"></a>入门

若要开始开发使用 Xamarin.Android 的 Android 饼图应用，必须下载并安装最新工具和 SDK 包，然后才能创建第一个 Android 饼图项目：

1. 更新到[Visual Studio 2017 版本 15.8](https://docs.microsoft.com/visualstudio/releasenotes/vs2017-relnotes)或更高版本。 如果你使用 Visual Studio for Mac，更新至[Visual Studio 2017 for Mac 版本 7.6](https://docs.microsoft.com/visualstudio/releasenotes/vs2017-relnotes)或更高版本。

2. 安装**Android 饼图 (API 28)** 包和工具通过 SDK 管理器。

3. 创建新的 Xamarin.Android 项目面向**Android 9.0**。

4. 配置仿真器或设备测试 Android 饼图应用。

以下部分解释了每个步骤：


### <a name="update-visual-studio"></a>更新 Visual Studio

若要添加到 Visual Studio Android 饼图的支持，请更新到 Visual Studio 2017 15.8 或更高版本 (有关说明，请参阅[到最新版本更新 Visual Studio 2017](https://docs.microsoft.com/visualstudio/install/update-visual-studio))。

若要添加到 Visual Studio for Mac Android 饼图支持，请更新到 Mac 7.6 或更高版本的 Visual Studio 2017 (有关说明，请参阅[安装程序并安装 Visual Studio for Mac](https://docs.microsoft.com/visualstudio/mac/installation))。


### <a name="install-the-android-sdk"></a>安装 Android SDK

若要使用 Xamarin.Android 9.0 创建项目，您必须首先使用 Android SDK 管理器安装的 SDK 平台**Android 饼图 （API 级别 28）** 或更高版本。

1. 启动 SDK 管理器。 在 Visual Studio 中，单击**工具 > Android > Android SDK 管理器**。 在 Visual Studio for Mac 中，单击**工具 > SDK 管理器**。

2. 在右下角，单击齿轮图标并选择**存储库 > Google （不受支持）**:

    [![向 Google 设置存储库](pie-images/vs/set-repo-sml.png)](pie-images/vs/set-repo.png#lightbox)

3. 安装**Android 饼图**SDK 包，被列为**Android SDK 平台 28**中**平台**选项卡 （有关使用 SDK 管理器的详细信息，请参阅[Android SDK 安装程序](~/android/get-started/installation/android-sdk.md)):

    [![安装 Android 饼图包](pie-images/vs/sdk-manager-sml.png)](pie-images/vs/sdk-manager.png#lightbox)

4. 如果使用仿真程序，创建一个虚拟设备，支持**API 级别 28**。 有关创建虚拟设备的详细信息，请参阅[管理虚拟设备使用 Android 设备管理器](~/android/get-started/installation/android-emulator/device-manager.md)。



### <a name="start-a-xamarinandroid-project"></a>启动 Xamarin.Android 项目

创建新的 Xamarin.Android 项目。 如果您不熟悉如何使用 Xamarin 进行 Android 开发，请参阅[Hello，Android](~/android/get-started/hello-android/index.md)若要了解有关创建 Xamarin.Android 项目。

创建 Android 项目时，必须配置的版本设置应用到目标 Android 9.0 或更高版本。 例如，若要为 Android 饼图针对你的项目，必须配置您的项目的目标 Android API 级别**Android 9.0** (API 28)。 建议还目标框架级别设置为 API 28 或更高版本。 有关配置的 Android API 级别的详细信息，请参阅[了解 Android API 级别](~/android/app-fundamentals/android-api-levels.md)。


### <a name="configure-a-device-or-emulator"></a>配置设备或仿真程序

如果使用如 Nexus 或像素的物理设备，你可以将设备更新到 Android 饼图中的说明[Nexus 和像素设备的出厂映像](https://developers.google.com/android/images)。

如果使用仿真程序，创建虚拟设备的 API 级别 28，并选择基于 x86 的图像。 有关使用 Android 设备管理器来创建和管理虚拟设备的信息，请参阅[管理虚拟设备使用 Android 设备管理器](~/android/get-started/installation/android-emulator/device-manager.md)。
有关使用 Android 仿真程序进行测试和调试的信息，请参阅[Android 仿真器上调试](~/android/deploy-test/debugging/debug-on-emulator.md)。



## <a name="new-features"></a>新增功能

Android 饼图引入了各种新功能。 其中一些新功能旨在利用最新的 Android 设备，尽管其他人能够进一步增强 Android 用户体验所提供的新硬件功能：

-   **显示切除支持**&ndash;提供了 Api，若要查找的位置和形状_切除_在较新的 Android 设备上屏幕的顶部。

-   **通知增强功能**&ndash;通知消息现在可以显示图像和一个新`Person`类用于简化对话参与者。

-   **室内定位** &ndash; WiFi Round 往返时间协议，从而使应用程序以使用在室内设置中进行导航的 WiFi 设备的平台支持。

-   **支持多照相机**&ndash;提供的功能的访问的流同时从多个物理照相机 （例如双正面和双背面摄像头）。


以下部分重点介绍了这些特性，并提供简短代码示例来帮助你开始在应用中使用它们。

### <a name="display-cutout-support"></a>显示切除支持

有许多较新的 Android 设备使用边到边屏幕*显示切除*（或"提高"） 顶部的照相机和演讲者的显示。
下面的屏幕截图提供切除的仿真程序示例：

[![Android 仿真程序模拟切除](pie-images/02-example-cutout-sml.png)](pie-images/02-example-cutout.png#lightbox)

若要管理您的应用程序窗口具有显示切除设备上显示其内容的方式，Android 饼图添加了一个新[LayoutInDisplayCutoutMode](https://developer.android.com/reference/android/view/WindowManager.LayoutParams.html#layoutInDisplayCutoutMode)窗口布局属性。 此属性可以设置为以下值之一：

-   [LayoutInDisplayCutoutModeNever](https://developer.android.com/reference/android/view/WindowManager.LayoutParams.html#LAYOUT_IN_DISPLAY_CUTOUT_MODE_NEVER) &ndash;永远不允许在窗口与切除区域重叠。

-   [LayoutInDisplayCutoutModeShortEdges](https://developer.android.com/reference/android/view/WindowManager.LayoutParams.html#LAYOUT_IN_DISPLAY_CUTOUT_MODE_SHORT_EDGES) &ndash;窗口允许扩展到切除区域，但仅在屏幕的短边缘上。 

-   [LayoutInDisplayCutoutModeDefault](https://developer.android.com/reference/android/view/WindowManager.LayoutParams.html#LAYOUT_IN_DISPLAY_CUTOUT_MODE_DEFAULT) &ndash;窗口允许将剪切块包含在系统栏的情况下扩展到切除区域。

例如，若要防止应用窗口与切除区域重叠，布局切除将模式设置为*永远不会*: 

```csharp
Window.Attributes.LayoutInDisplayCutoutMode =
    Android.Views.LayoutInDisplayCutoutMode.Never;
```

以下示例提供这些切除模式的示例。 在左侧的第一个屏幕截图是在非全屏模式下的应用。 在中心屏幕截图中，使用应用程序的全屏与`LayoutInDisplayCutoutMode`设置为`LayoutInDisplayCutoutModeShortEdges`。 请注意，应用程序的白色背景扩展到显示切除区域：

[![在仿真程序中的示例显示切除模式](pie-images/03-cutout-modes-sml.png)](pie-images/03-cutout-modes.png#lightbox)

中的最终屏幕快照 (上面在右侧)，`LayoutInDisplayCutoutMode`设置为`LayoutInDisplayCutoutModeShortNever`之后变为全屏幕。
请注意，不允许使用应用程序的白色背景将扩展到显示切除区域。

如果需要更多详细的信息剪切块区域在设备上，你可以使用新[DisplayCutout](https://developer.android.com/reference/android/view/DisplayCutout.html)类。 `DisplayCutout` 表示不能用于显示内容的显示区域。 此信息可用于检索位置和形状的裁剪，使您的应用程序不会尝试在此非功能区域中显示的内容。

有关 Android P 中的新切除功能的详细信息，请参阅[显示切除支持](https://developer.android.com/about/versions/pie/android-9.0#cutout)。



### <a name="notifications-enhancements"></a>通知增强功能

Android 饼图引入了以下增强功能以提高消息传递体验：

-   通知通道 (在中引入[Android Oreo](~/android/platform/oreo.md)) 现在支持通道组的阻止。

-   通知系统具有三个新 Do Not 打扰的类别 （优先级的警报、 系统声音和媒体源）。 此外，有七个新 Do Not 打扰模式可用来取消显示 visual 中断 （如徽章、 通知光源、 状态条的外观，和启动的全屏幕活动）。

-   一个新[人员](https://developer.android.com/reference/android/app/Person.html)添加类来表示消息的发件人。 使用此类有助于通过标识 （包括其虚拟形象和 Uri） 的会话中涉及的人员来优化每个通知的呈现。

-   通知现在可以显示图像。 

下面的示例演示如何使用新的 Api 来生成包含图像的通知。 在下面的屏幕截图，一个文本通知发布并后跟包含嵌入图像的通知。 通知进行扩展 （如右侧所示），会显示第一次通知的文本和图像中嵌入时扩大，第二个通知：

[![使用映像的示例通知](pie-images/04-example-notifications-sml.png)](pie-images/04-example-notifications.png#lightbox)

下面的示例演示如何在 Android 饼图通知中包含图像和它演示如何使用新`Person`类：

1. 创建`Person`表示发件人的对象。 例如，在包含发件人的名称和图标`fromPerson`:

    ```csharp
    Icon senderIcon = Icon.CreateWithResource(this, Resource.Drawable.sender_icon);
    Person fromPerson = new Person.Builder()
        .SetIcon(senderIcon)
        .SetName("Mark Sender")
        .Build();
    ```

2. 创建`Notification.MessagingStyle.Message`，其中包含要发送，图像将传递到新的映像[Notification.MessagingStyle.Message.SetData](https://developer.android.com/reference/android/app/Notification.MessagingStyle.Message.html#setData%28java.lang.String,%20android.net.Uri)方法。
   例如：

    ```csharp
    Uri imageUri = Uri.Parse("android.resource://com.xamarin.pminidemo/drawable/example_image");
    Notification.MessagingStyle.Message message = new Notification.MessagingStyle
            .Message("Here's a picture of where I'm currently standing", 0, fromPerson)
            .SetData("image/", imageUri);
    ```

3. 向其中添加消息`Notification.MessagingStyle`对象。 例如：

    ```csharp
    Notification.MessagingStyle style = new Notification.MessagingStyle(fromPerson)
            .AddMessage(message);
    ```

4. 此样式插入通知生成器。 例如：

    ```csharp
    builder = new Notification.Builder(this, MY_CHANNEL)
        .SetContentTitle("Tour of the Colosseum")
        .SetContentText("I'm standing right here!")
        .SetSmallIcon(Resource.Mipmap.ic_notification)
        .SetStyle(style)
        .SetChannelId(MY_CHANNEL);
    ```

5. 发布通知。 例如：

    ```csharp
    const int notificationId = 1000;
    notificationManager.Notify(notificationId, builder.Build());
    ```

有关创建通知的详细信息，请参阅[本地通知](~/android/app-fundamentals/notifications/local-notifications.md)。


### <a name="indoor-positioning"></a>室内定位

Android 饼图支持 IEEE 802.11mc (也称为_WiFi Round 往返时间_或_WiFi RTT_)，这使得应用程序检测到一个距离或更多的 Wi-fi 访问点。 使用此信息，很可能你的应用以充分利用*室内定位*一到两个计量的精度。 Android 设备上提供的 IEEE 801.11mc 硬件支持，您的应用程序可以提供导航功能，例如基于位置的控件的智能设备或通过应用商店启用通过打开说明：

[![使用 WiFi RTT 的室内导航的示例](pie-images/05-wifi-rtt-sml.png)](pie-images/05-wifi-rtt.png#lightbox)

新[WifiRttManager](https://developer.android.com/reference/android/net/wifi/rtt/WifiRttManager)类和几个帮助程序类提供进行测量到的 Wi-fi 设备距离的手段。 有关 Android P 中引入的室内定位 Api 的详细信息，请参阅[Android.Net.Wifi.Rtt](https://developer.android.com/reference/android/net/wifi/rtt/package-summary)。


### <a name="multi-camera-support"></a>多照相机支持

许多较新的 Android 设备都具有双前面和/或双后可用于诸如立体声视觉、 增强视觉效果和改进的缩放功能等功能的摄像头。 Android P 引入了一个新[多照相机](https://developer.android.com/about/versions/pie/android-9.0#camera)API，它使应用程序以使用*逻辑照相机*(或*逻辑多照相机*) 支持的两个或多个物理相机。
若要确定是否设备支持逻辑多照相机，则可以查看的每个设备上的照相机功能，以查看它是否支持[RequestAvailableCapabilitiesLogicalMultiCamera](https://developer.android.com/reference/android/hardware/camera2/CameraMetadata#REQUEST_AVAILABLE_CAPABILITIES_LOGICAL_MULTI_CAMERA)。

Android 饼图还包括一个新[SessionConfiguration](https://developer.android.com/reference/android/hardware/camera2/params/SessionConfiguration.html)可用于帮助在初始捕获过程中减少延迟和无需启动并启动相机流的类。

详细了解多照相机中 Android P 支持，请参阅[多照相机支持和照相机更新](https://developer.android.com/about/versions/pie/android-9.0#camera)。


### <a name="other-features"></a>其他功能

此外，Android 饼图支持多个其他新功能：

-   新[AnimatedImageDrawable](https://developer.android.com/reference/android/graphics/drawable/AnimatedImageDrawable.html)类，该类可用于进行绘制和显示动画的图像。

-   一个新[ImageDecoder](https://developer.android.com/reference/android/graphics/ImageDecoder.html)类，用于替换`BitmapFactory`。 `ImageDecoder` 可用于解码`AnimatedImageDrawable`。

-   支持 HDR （高动态范围） 视频和 HEIF （高效率图像文件格式） 图像。

-   [JobScheduler](https://developer.android.com/reference/android/app/job/JobScheduler.html)已增强，以更智能地处理与网络相关的作业。 新[GetNetwork](https://developer.android.com/reference/android/app/job/JobParameters#getNetwork%28%29)方法[JobParameters](https://developer.android.com/reference/android/app/job/JobParameters)类返回最佳网络用于为给定的作业执行的任何网络请求。

有关最新的 Android 饼图功能的详细信息，请参阅[Android 9 功能和 Api](https://developer.android.com/about/versions/pie/android-9.0)。


## <a name="behavior-changes"></a>行为更改

当目标 Android 版本设置为 API 级别 28 时，有可能会影响应用程序的行为，即使未实现上面所述的新功能的多个平台更改。 以下列表是这些更改的简短摘要：

-  应用程序现在必须请求前景色的权限，才使用前景服务。

-  如果您的应用程序具有多个进程，它不能共享单个[WebView](https://developer.xamarin.com/api/type/Android.Webkit.WebView/)跨进程的数据目录。

-  不再允许直接访问另一个应用的数据目录的路径。

有关面向 Android P 的应用程序的行为更改的详细信息，请参阅[的行为更改](https://developer.android.com/about/versions/pie/android-9.0-changes-all#p-apps)。


## <a name="sample-code"></a>示例代码

[AndroidPMiniDemo](https://github.com/xamarin/monodroid-samples/tree/master/android-p/AndroidPMiniDemo)演示如何设置显示切除模式的 Android 饼图是 Xamarin.Android 示例应用程序如何使用新`Person`类，以及如何将发送一条通知，包括图像。


## <a name="summary"></a>总结

本文引入 Android 饼图，并介绍了如何使用安装和配置最新的工具和包 Xamarin.Android 开发 Android 饼图。 它提供有关这些功能的几个示例源代码 Android 饼图中提供的关键功能的概述。
它包含 API 文档的链接和 Android 开发人员主题可帮助您入门中创建适用于 Android 的饼图应用。 它还突出显示可能会影响现有应用程序的最重要 Android 饼图行为更改。


## <a name="related-links"></a>相关链接

- [Android 9 Pie](https://developer.android.com/about/versions/pie/)
