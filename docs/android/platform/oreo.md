---
title: Oreo 功能
description: 如何开始使用 Xamarin.Android 开发适用于 Android 的最新版本的应用。
ms.prod: xamarin
ms.assetid: EAEF7341-7A00-4439-9FAF-43882637BEF8
ms.technology: xamarin-android
ms.custom: video
author: conceptdev
ms.author: crdun
ms.date: 07/06/2018
ms.openlocfilehash: 24a9fa0e954ddba1451ba8bf98216550d7d70b51
ms.sourcegitcommit: c4be32ef914465e808d89767c4d5ee72afe93cc6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/02/2019
ms.locfileid: "58854777"
---
# <a name="oreo-features"></a>Oreo 功能

_如何开始使用 Xamarin.Android 开发适用于 Android 的最新版本的应用。_

[Android 8.0 Oreo](https://developer.android.com/index.html)是 Android 的最新版本可从 Google。 Android Oreo 为 Xamarin.Android 开发人员提供所需的许多新功能。 这些功能包括 XML、 可下载的字体、 自动填充和图片 (PIP) 中的图片中的通知通道、 通知徽章、 自定义字体。 Android Oreo 包括这些新功能的新 Api 和这些 Api 是适用于 Xamarin.Android 应用时使用 Xamarin.Android 8.0 及更高版本。

[![Android Oreo 英雄形象](oreo-images/01-android-o-logo-sml.png)](oreo-images/01-android-o-logo.png#lightbox)

本文旨在帮助您开始针对 Android 8.0 Oreo 中开发 Xamarin.Android 应用程序。 其中介绍了如何为安装必要的更新、 配置 SDK，并为测试创建模拟器 （或设备）。 它还提供 Android 8.0 Oreo 中的新增功能的概述，其中包含指向示例应用程序，展示了如何在 Xamarin.Android 应用中使用 Android Oreo 功能。


## <a name="requirements"></a>要求

以下是所需的基于 Xamarin 的应用中使用 Android Oreo 功能：

-   **Visual Studio** &ndash;如果使用的 Windows，则需要版本 15.5 或更高版本的 Visual Studio。  如果使用的是 Mac，则需要 Visual Studio for Mac 版本 7.2.0。

-   **Xamarin.Android** &ndash; Xamarin.Android 8.0 或更高版本必须安装并配置了 Visual Studio。

-   **Android SDK** &ndash; Android SDK 8.0 (API 26) 或更高版本必须安装通过 Android SDK 管理器。



## <a name="getting-started"></a>入门

若要开始使用 Xamarin.Android 的 Android Oreo，必须下载并安装最新工具和 SDK 包，然后才能创建 Android Oreo 项目：

1. 更新到最新版本的 Visual Studio。

2. 安装**Android 8.0.0 (API 26)** 或更高版本的包和工具通过 SDK 管理器。

3. 创建新的 Xamarin.Android 项目面向 Android Oreo (API 26)。

4. 配置仿真器或设备测试 Android Oreo 应用。

以下部分解释了每个步骤：

### <a name="update-visual-studio-and-xamarinandroid"></a>更新 Visual Studio 和 Xamarin.Android

若要添加到 Visual Studio Android Oreo 支持，请执行以下操作：

# [<a name="visual-studio"></a>Visual Studio](#tab/windows)

- 对于 Visual Studio 2019，使用[SDK 管理器](~/android/get-started/installation/android-sdk.md)安装 26.0 或更高版本的 API 级别。

- 如果使用 Visual Studio 2017:

    1. 更新到 Visual Studio 2017 版本 15.7 或更高版本 (请参阅[Visual Studio 2017 更新](https://docs.microsoft.com/visualstudio/install/update-visual-studio))。

    2. 使用[SDK 管理器](~/android/get-started/installation/android-sdk.md)安装 26.0 或更高版本的 API 级别。

# [<a name="visual-studio-for-mac"></a>Visual Studio for Mac](#tab/macos)

- 更新到最新稳定版本的 Visual Studio for Mac，如中所述[更新 Visual Studio for Mac](https://docs.microsoft.com/visualstudio/mac/update)。

-----

有关 Xamarin 支持 Android oreo 的详细信息，请参阅[Xamarin.Android 8.0 发行说明](https://docs.microsoft.com/xamarin/android/release-notes/8/8.0/)。



### <a name="install-the-android-sdk"></a>安装 Android SDK

若要创建项目时使用 Xamarin.Android 8.0，您必须首先使用 Xamarin Android SDK 管理器安装的 SDK 平台**Android 8.0-Oreo**或更高版本。 此外必须安装 Android SDK Tools 26.0 或更高版本。

# [<a name="visual-studio"></a>Visual Studio](#tab/windows)

1. 启动 SDK 管理器 (在 Visual Studio 中，单击**工具 > Android > Android SDK 管理器**)。

2. 安装**Android 8.0-Oreo**包。 如果使用 Android SDK 仿真器，请务必包括**x86**将需要的系统映像：

    [![S选择 Android 8.0 包在 Android SDK 管理器](oreo-images/win/01-android-o-packages.png)](oreo-images/win/01-android-o-packages.png#lightbox)

3. 安装**Android SDK Tools 26.0.2**或更高版本， **Android 的 SDK 平台工具 26.0.0**或更高版本，并且**Android 的 SDK 生成工具 26.0.0** （或更高版本）：

    [![S选择 Android SDK Tools 26 中 Android SDK 管理器](oreo-images/win/02-sdk-tools.png)](oreo-images/win/02-sdk-tools.png#lightbox)

# [<a name="visual-studio-for-mac"></a>Visual Studio for Mac](#tab/macos)

1. 启动 SDK 管理器 (在 Visual Studio for Mac 中，单击**工具 > SDK 管理器**)。

2. 安装**Android 8.0-Oreo** SDK 包。 如果使用 Android SDK 仿真器，请务必包括**x86**将需要的系统映像：

    [![S选择 Android 8.0 包在 SDK 管理器](oreo-images/mac/01-android-o-packages.png)](oreo-images/mac/01-android-o-packages.png#lightbox)

3. 安装**Android SDK Tools 26.0.2**或更高版本， **Android 的 SDK 平台工具 26.0.0**或更高版本，并且**Android 的 SDK 生成工具 26.0.0** （或更高版本）：

    [![S选择 Android SDK Tools 26 在 SDK 管理器](oreo-images/mac/02-sdk-tools.png)](oreo-images/mac/02-sdk-tools.png#lightbox)

-----



### <a name="start-a-xamarinandroid-project"></a>启动 Xamarin.Android 项目

创建新的 Xamarin.Android 项目。 如果您不熟悉如何使用 Xamarin 进行 Android 开发，请参阅[Hello，Android](~/android/get-started/hello-android/index.md)若要了解有关创建 Xamarin.Android 项目。

创建 Android 项目时，必须配置的版本设置应用到目标 Android 8.0 或更高版本。 例如，若要为 Android 8.0 针对你的项目，必须配置您的项目的目标 Android API 级别**Android 8.0 (API 26)**。 建议，还将目标框架设置为 API 26 或更高版本。 有关配置 Android API 级别级别的详细信息，请参阅[了解 Android API 级别](~/android/app-fundamentals/android-api-levels.md)。


### <a name="configure-an-emulator-or-device"></a>配置仿真器或设备

如果您尝试启动默认基于 Google GUI 的 AVD 管理器安装 Android SDK Tools 26.0 后或更高版本，可能会收到以下错误对话框，这会指示你可以使用命令行 AVD 管理器工具**avdmanager**改为:

# [<a name="visual-studio"></a>Visual Studio](#tab/windows)

![Android 仿真器管理器警告对话框](oreo-images/win/03-avd-warning.png)

# [<a name="visual-studio-for-mac"></a>Visual Studio for Mac](#tab/macos)

![Android 仿真器管理器警告对话框](oreo-images/mac/03-avd-warning.png)

-----

显示此消息，因为 Google 不再提供独立的 GUI AVD 管理器支持 26.0 及更高版本的 API。 对于 Android 8.0 Oreo，则必须使用 Xamarin Android 仿真器管理器或命令行`avdmanager`工具，用于为 Android Oreo 中创建的虚拟设备。

若要使用 Android 设备管理器创建和管理虚拟设备，请参阅[管理虚拟设备使用 Android 设备管理器](~/android/get-started/installation/android-emulator/device-manager.md)。
若要创建虚拟设备而无需 Android 设备管理器，请执行下一节中的步骤。


#### <a name="creating-virtual-devices-using-avdmanager"></a>创建虚拟设备使用 avdmanager

若要使用**avdmanager**若要创建新的虚拟设备，请执行以下步骤：

# [<a name="visual-studio"></a>Visual Studio](#tab/windows)

1.  打开命令提示符窗口，并设置`JAVA_HOME`到您的计算机上的 Java SDK 的位置。 对于典型的 Xamarin 安装，可以使用以下命令：

    ```cmd
    setx JAVA_HOME "C:\Program Files\Java\jdk1.8.0_131"
    ```

2.  将 Android SDK 的位置添加`bin`文件夹到你`PATH`。
    对于典型的 Xamarin 安装，可以使用以下命令：

    ```cmd
    setx PATH "%PATH%;C:\Program Files (x86)\Android\android-sdk\tools\bin"
    ```

3.  关闭命令提示符窗口并打开新的命令提示符窗口。 使用创建新的虚拟设备[avdmanager](https://developer.android.com/studio/command-line/avdmanager.html)命令。 例如，若要创建 AVD 名为**AVD Oreo 8.0**使用 x86 系统映像的 API 级别 26，使用以下命令：

    ```cmd
    avdmanager create avd -n AVD-Oreo-8.0 -k "system-images;android-26;google_apis;x86"
    ```

4.  您使用的提示时**你想要创建自定义硬件配置文件 [无]** 您可以输入**没有**并接受默认硬件配置文件。 如果您说**是**， **avdmanager**将提示你提供一系列用于自定义硬件配置文件的问题。

检查完**avdmanager**若要创建虚拟设备，它将包含在设备下拉菜单中：

[![NAVD 添加到设备下拉菜单中的新增功能](oreo-images/win/04-android-o-avd-sml.png)](oreo-images/win/04-android-o-avd.png#lightbox)

# [<a name="visual-studio-for-mac"></a>Visual Studio for Mac](#tab/macos)

1.  打开**终端**窗口并更改到在 mac 上的 Android SDK 工具目录的位置 对于典型的 Xamarin 安装，可以使用以下命令：

    ```bash
    cd ~/Library/Developer/Xamarin/android-sdk-macosx/tools/bin
    ```

2.  使用创建新的虚拟设备[avdmanager](https://developer.android.com/studio/command-line/avdmanager.html)命令。 例如，若要创建 AVD 名为**AVD Oreo 8.0**使用 x86 系统映像的 API 级别 26，使用以下命令：

    ```bash
    avdmanager create avd -n AVD-Oreo-8.0 -k "system-images;android-26;google_apis;x86"
    ```

3.  您使用的提示时**你想要创建自定义硬件配置文件 [无]** 您可以输入**没有**并接受默认硬件配置文件。 如果您说**是**， **avdmanager**将提示你提供一系列用于自定义硬件配置文件的问题。

在使用后**avdmanager**若要创建虚拟设备，它将包含在设备下拉菜单中：

[![NAVD 添加到设备下拉菜单中的新增功能](oreo-images/mac/04-android-o-avd-sml.png)](oreo-images/mac/04-android-o-avd.png#lightbox)

-----

有关配置 Android 仿真器以测试和调试的详细信息，请参阅[Android 仿真器上调试](~/android/deploy-test/debugging/debug-on-emulator.md)。

如果使用如 Nexus 或像素的物理设备，可以通过无线 (OTA) 更新通过自动更新你的设备或下载的系统映像并直接刷新你的设备。 有关手动将设备更新到 Android Oreo 的详细信息，请参阅[Nexus 和像素设备的出厂映像](https://developers.google.com/android/images)。



## <a name="new-features"></a>新增功能

Android Oreo 引入了各种新功能和功能，例如通知通道、 通知徽章、 在 XML 中的自定义字体、 可下载的字体、 自动填充和画中画。 以下部分重点介绍了这些特性，并提供链接，以帮助你开始在应用中使用它们。



### <a name="notification-channels"></a>通知通道

*通知通道*是针对通知应用程序定义的类别。
可以创建通知通道的每种类型的通知，您需要发送，并且可以创建通知通道，以反映您的应用程序的用户进行选择。 新的通知通道功能使您可以授予对不同类型的通知的用户进行精细控制。 例如，如果要实现的消息传送应用程序，可以创建由用户创建每个会话组的单独通知通道。

[通知通道](~/android/app-fundamentals/notifications/local-notifications.md#notif-chan)介绍了如何创建通知通道并将其用于发布本地通知。 实际的代码示例，请参阅[NotificationChannels](https://developer.xamarin.com/samples/monodroid/android-o/NotificationChannels)示例; 此示例应用程序管理两个通道，并设置其他通知选项。



### <a name="notification-badges"></a>通知徽章

通知徽章是显示在应用图标上，如以下屏幕截图中所示的小圆点：

[![E应用图标上的示例通知徽章](oreo-images/02-badges-sml.png)](oreo-images/02-badges.png#lightbox)

这些点表示有一个或多个通知通道，与该应用程序图标关联的应用中的新通知&ndash;这些是用户尚未取消或操作的通知。 用户可以长时间的按键的图标可关闭或作用于从长时间按菜单的通知该 appeaars 观察一下通知徽章，与关联的通知。

有关通知徽章的详细信息，请参阅 Android 开发人员[通知徽章](https://developer.android.com/guide/topics/ui/notifiers/notifications.html#Badges)主题。



### <a name="custom-fonts-in-xml"></a>在 XML 中的自定义字体

引入了 android Oreo *XML 中的字体*，从而有可能，从而将合并作为资源的自定义字体。 OpenType (**.otf**) 和 TrueType (**.ttf**) 支持字体格式。 若要将字体作为资源添加，请执行以下操作：

1. 创建**资源/字体**文件夹。

2. 将字体文件复制 (示例中， **.ttf**并 **.otf**文件) 到**资源/字体**。 

3. 如有必要，重命名每个字体文件，以便它符合的 Android 文件命名约定 (即，使用仅小写*a 到 z*， *0-9*，并在文件名中的下划线)。 例如，字体文件`Pacifico-Regular.ttf`无法重命名为类似于`pacifico.ttf`。

4. 使用新的应用自定义字体`android:fontFamily`在布局 XML 中的属性。 例如，以下`TextView`声明使用添加**pacifico.ttf**字体资源：

   ```xml
   <TextView
     android:text="Example Text in Pacifico Regular"
     android:layout_width="wrap_content"
     android:layout_height="wrap_content"
     android:fontFamily="@font/pacifico" />
   ```

此外可以创建字体系列 XML 文件，描述多个字体以及样式和粗细的详细信息。 有关详细信息，请参阅 Android 开发人员[XML 中的字体](https://developer.android.com/guide/topics/ui/look-and-feel/fonts-in-xml.html)主题。


### <a name="downloadable-fonts"></a>可下载的字体

从 Android Oreo 开始，应用程序可以请求字体，从提供程序，而无需将它们捆绑到 APK。 仅在需要时，将从网络下载字体。 此功能可以减少 APK 大小，节省电话内存和移动电话网络数据使用情况。 您还可以通过安装 Android 支持库 26 包 Android API 版本 14 及更高版本上使用此功能。

当您的应用程序需要一种字体时，创建`FontsRequest`对象 （指定要下载的字体） 然后将其传递给`FontsContract`方法下载字体。 以下步骤描述字体下载过程中更多详细信息：

1.  实例化[FontRequest](https://developer.android.com/reference/android/provider/FontRequest.html)对象。 

2.  子类并实例化[FontsContract.FontRequestCallback](https://developer.android.com/reference/android/provider/FontsContract.FontRequestCallback.html)。

3.  实现[FontRequestCallback.OnTypeFaceRetrieved](https://developer.android.com/reference/android/provider/FontsContract.FontRequestCallback.html#onTypefaceRetrieved%28android.graphics.Typeface%29)方法，用于处理完成字体请求。

4.  实现[FontRequestCallback.OnTypeFaceRequestFailed](https://developer.android.com/reference/android/provider/FontsContract.FontRequestCallback.html#onTypefaceRequestFailed%28int%29)方法，用来通知您的应用程序的字体请求过程中发生任何错误。

5.  调用[FontsContract.RequestFonts](https://developer.android.com/reference/android/provider/FontsContract.html#requestFonts(android.content.Context,%20android.provider.FontRequest,%20android.os.Handler,%20android.os.CancellationSignal,%20android.provider.FontsContract.FontRequestCallback))方法以从字体提供程序检索字体的字体。 

当您调用`RequestFonts`方法，它首先检查是否本地缓存字体 (从上一个调用`RequestFont`)。 如果未缓存，它调用字体提供程序，以异步方式检索字体，然后将结果返回到您的应用程序传递通过调用你`OnTypeFaceRetrieved`方法。

[可下载字体](https://developer.xamarin.com/samples/monodroid/android-o/DownloadableFonts)示例演示如何使用 Android Oreo 中引入的可下载字体功能。 

有关下载字体的详细信息，请参阅 Android 开发人员[可下载字体](https://developer.android.com/guide/topics/ui/look-and-feel/downloadable-fonts.html)主题。



### <a name="autofill"></a>自动填充

新_自动填充_Android Oreo 中的框架，使用户更轻松地处理重复任务，例如登录名、 帐户创建和信用卡交易。 用户花费更少的时间重新键入信息 （这可能会导致输入错误）。 您的应用程序能够使用自动填充框架之前，必须在系统设置 （用户可以启用或禁用自动填充） 中启用自动填充服务。

[AutofillFramework](https://developer.xamarin.com/samples/monodroid/android-o/AutoFillFramework/)示例演示如何使用自动填充框架。 它包括客户端活动与应 autofilled，并可以提供给客户端活动的自动填充数据的服务的视图的实现。

有关新的自动填充功能以及如何优化自动填充你的应用的详细信息，请参阅 Android 开发人员[Autofill Framework](https://developer.android.com/guide/topics/text/autofill.html)主题。



### <a name="picture-in-picture-pip"></a>图片 (PIP) 中的图片

Android Oreo 就可以用于在画中画 (PIP) 模式下，启动的活动覆盖另一个活动的屏幕。 此功能适用于视频播放。

若要指定应用程序的活动可以使用 PIP 模式，请为 true Android 清单中设置以下标记：

```xml
android:supportsPictureInPicture
```

若要指定您的活动应在 PIP 模式下时的行为方式，你使用新[PictureInPictureParams](https://developer.android.com/reference/android/app/PictureInPictureParams.html)对象。 `PictureInPictureParams` 表示一组用于初始化和更新 PIP 模式 （例如，活动的首选纵横比） 中的活动的参数。 以下新的 PIP 方法已添加到`Activity`Android Oreo 中：

-   [EnterPictureInPictureMode](https://developer.android.com/reference/android/app/Activity.html#enterPictureInPictureMode%28android.app.PictureInPictureParams%29) &ndash;将活动放入 PIP 模式。 活动放在屏幕上，角和屏幕的其余部分均显示在屏幕上的上一个活动。

-   [SetPictureInPictureParams](https://developer.android.com/reference/android/app/Activity.html#setPictureInPictureParams%28android.app.PictureInPictureParams%29) &ndash;更新活动的 PIP 配置设置 （例如，更改纵横比）。

[PictureInPicture](https://developer.xamarin.com/samples/monodroid/android-o/PictureInPicture)示例演示如何在 Oreo 中引入的手持设备的画中画 (PiP) 模式的基本用法。 该示例播放不间断地同时显示模式或其他活动之间来回切换的视频，它会继续。



### <a name="other-features"></a>其他功能

Android Oreo 包含很多其他新功能，如表情符号支持库中，位置 API，后台限制、 应用、 新的音频编解码器、 web 视图增强功能、 改进了的键盘导航支持和适用于的新 AAudio （pro 音频） API 的范围内所有颜色高性能低滞后时间的音频，详细了解这些功能，请参阅 Android 开发人员[Android Oreo 功能和 Api](https://developer.android.com/about/versions/oreo/android-8.0.html)主题。



## <a name="behavior-changes"></a>行为更改

Android Oreo 包括各种系统和 API 可能会影响的现有应用的功能的行为更改。 按如下所示介绍了这些更改。


### <a name="background-execution-limits"></a>后台执行限制

为了改善用户体验，Android Oreo 规定应用程序可执行的限制时在后台运行。 例如，如果用户是观看视频或玩游戏时，在后台运行的应用无法降低在前台运行需要进行大量视频的应用程序的性能。 因此，Android Oreo 置于不直接与用户交互的应用以下限制：

1.  **后台服务限制**&ndash;时在后台运行应用，它具有一个窗口，在其中的几分钟的它仍可创建和使用服务。 在该窗口结束时，Android 停止应用程序的后台服务并将其视为正在_空闲_。

2.  **限制广播** &ndash; Android 7.0 (API 25) 放置在应用注册以接收广播上的限制。 Android Oreo 使这些限制更严格。 例如，Android Oreo 应用不再可以在其清单中注册为隐式广播的广播的接收器。

有关新的后台执行限制的详细信息，请参阅 Android 开发人员[后台执行限制](https://developer.android.com/about/versions/oreo/background.html)主题。


### <a name="breaking-changes"></a>重大更改

应用程序面向 Android Oreo 或更高版本必须修改其应用程序以支持以下更改，在适用的情况：

- Android Oreo 弃用了设置单个通知的优先级的能力。 相反，创建通知通道时设置建议的重要性级别。 你将分配给通知通道的重要性级别适用于所有通知消息，你向其发布到它。

- 为应用程序面向的 Android Oreo`PendingIntent.GetService()`不工作，因为加上在后台启动的服务的新限制。 如果你面向的 Android Oreo，则应使用[PendingIntent.GetBroadcast](https://developer.xamarin.com/api/member/Android.App.PendingIntent.GetBroadcast/p/Android.Content.Context/System.Int32/Android.Content.Intent/Android.App.PendingIntentFlags/)相反。  


## <a name="sample-code"></a>代码示例

多个 Xamarin.Android 示例可以用来说明如何利用 Android Oreo 功能：

-   [NotificationsChannels](https://developer.xamarin.com/samples/monodroid/android-o/NotificationChannels)演示了如何使用 Android Oreo 中引入了新的通知通道系统。 此示例管理两个通知通道： 一个具有默认重要性和另一种具有高重要性。

-   [PictureInPicture](https://developer.xamarin.com/samples/monodroid/android-o/PictureInPicture)演示 Oreo 中引入的手持设备的画中画 (PiP) 模式的基本用法。 该示例播放不间断地同时显示模式或其他活动之间来回切换的视频，它会继续。

-   [AutofillFramework](https://developer.xamarin.com/samples/monodroid/android-o/AutoFillFramework)演示如何使用自动填充框架。 它包括客户端活动与应 autofilled，并可以提供给客户端活动的自动填充数据的服务的视图的实现。

-   [可下载字体](https://developer.xamarin.com/samples/monodroid/android-o/DownloadableFonts)举例说明如何使用前面所述的可下载字体功能。

-   [EmojiCompat](https://developer.xamarin.com/samples/monodroid/android-o/EmojiCompat)演示 EmojiCompat 支持库的使用情况。 可以使用此库以防止显示缺少的表情符号字符，而"豆腐"字符作为你的应用。

-   [位置更新挂起意向](https://developer.xamarin.com/samples/monodroid/android-o/AndroidPlayLocation/LocUpdPendIntent)说明了使用位置 API 来获取有关设备的定位更新使用`PendingIntent`。

-   [位置更新前景服务](https://developer.xamarin.com/samples/monodroid/android-o/AndroidPlayLocation/LocUpdFgService)演示了如何使用位置 API 来获取有关设备的位置使用绑定和启动前景服务更新。


## <a name="video"></a>视频

> [!VIDEO https://youtube.com/embed/OuvEcaMO-Ho]

**使用 C# 的 android 8.0 Oreo 开发**


## <a name="summary"></a>总结

本文引入了 Android Oreo，并介绍了如何安装和配置 Android Oreo 上的最新工具和 Xamarin.Android 开发的包。 它提供了 Android Oreo 中可用的主要功能的概述，多项新功能的示例源代码的链接。 它包含 API 文档的链接和 Android 开发人员主题可帮助您入门中创建适用于 Android Oreo 应用。 它还突出显示可能会影响现有应用程序的最重要的 Android Oreo 行为更改。


## <a name="related-links"></a>相关链接

- [Android 8.0 Oreo](https://developer.android.com/index.html)
