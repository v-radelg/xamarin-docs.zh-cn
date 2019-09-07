---
title: Oreo 功能
description: 如何开始使用 Xamarin 开发适用于 Android 的最新版本的应用。
ms.prod: xamarin
ms.assetid: EAEF7341-7A00-4439-9FAF-43882637BEF8
ms.technology: xamarin-android
ms.custom: video
author: conceptdev
ms.author: crdun
ms.date: 07/06/2018
ms.openlocfilehash: 0387cd91bd24080417a5e9763410d68b6e688555
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70757498"
---
# <a name="oreo-features"></a>Oreo 功能

_如何开始使用 Xamarin 开发适用于 Android 的最新版本的应用。_

[Android 8.0 Oreo](https://developer.android.com/index.html)是 Google 提供的最新版本的 android。 Android Oreo 提供了许多针对 Xamarin 开发人员感兴趣的新功能。 这些功能包括通知通道、通知徽章、XML 中的自定义字体、可下载字体、自动填充和图片（PIP）。 Android Oreo 包含这些新功能的新 Api，在使用 Xamarin 8.0 和更高版本时，这些 Api 可用于 Xamarin Android 应用。

[![Android Oreo 英雄图像](oreo-images/01-android-o-logo-sml.png)](oreo-images/01-android-o-logo.png#lightbox)

本文旨在帮助你开始开发适用于 Android 8.0 Oreo 的 Xamarin Android 应用。 它说明了如何安装所需的更新、配置 SDK 以及创建用于测试的模拟器（或设备）。 它还概述了 Android 8.0 Oreo 中的新功能，并提供了指向示例应用的链接，说明如何在 Xamarin Android 应用中使用 Android Oreo 功能。

## <a name="requirements"></a>要求

在基于 Xamarin 的应用中使用 Android Oreo 功能时需要以下各项：

- **Visual Studio**&ndash;如果使用的是 Windows，则需要 Visual Studio 版本15.5 或更高版本。  如果你使用的是 Mac，Visual Studio for Mac 版本7.2.0 是必需的。

- 必须通过 Visual Studio 安装和配置**Xamarin** xamarin8.0或更高版本。&ndash;

- **Android SDK**&ndash;必须通过 Android SDK 管理器安装 Android SDK 8.0 （API 26）或更高版本。

## <a name="getting-started"></a>入门

若要开始使用适用于 Xamarin 的 Android Oreo，必须先下载并安装最新的工具和 SDK 包，然后才能创建 Android Oreo 项目：

1. 更新到最新版本的 Visual Studio。

2. 通过 SDK 管理器安装**Android 8.0.0 （API 26）** 或更高版本的包和工具。

3. 创建面向 Android Oreo （API 26）的新 Xamarin 项目。

4. 配置用于测试 Android Oreo 应用的模拟器或设备。

以下各部分对这些步骤进行了说明：

### <a name="update-visual-studio-and-xamarinandroid"></a>更新 Visual Studio 和 Xamarin

若要向 Visual Studio 添加 Android Oreo 支持，请执行以下操作：

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

- 对于 Visual Studio 2019，请使用[SDK 管理器](~/android/get-started/installation/android-sdk.md)安装 API 级别26.0 或更高版本。

- 如果使用的是 Visual Studio 2017：

    1. 更新到 Visual Studio 2017 版本15.7 或更高版本（请参阅[更新 Visual studio 2017](https://docs.microsoft.com/visualstudio/install/update-visual-studio)）。

    2. 使用[SDK 管理器](~/android/get-started/installation/android-sdk.md)安装 API 级别26.0 或更高版本。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

- 更新到 Visual Studio for Mac 的最新稳定版本，如[更新 Visual Studio for Mac](https://docs.microsoft.com/visualstudio/mac/update)中所述。

-----

有关适用于 Android Oreo 的 Xamarin 支持的详细信息，请参阅 " [xamarin 8.0 发行说明](https://docs.microsoft.com/xamarin/android/release-notes/8/8.0/)"。

### <a name="install-the-android-sdk"></a>安装 Android SDK

若要使用 Xamarin 8.0 创建项目，必须首先使用 Xamarin Android SDK 管理器安装适用于 Android 的 SDK 平台**8.0-Oreo**或更高版本。 还必须安装 Android SDK Tools 26.0 或更高版本。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. 启动 SDK 管理器（在 Visual Studio 中，单击 "**工具" > Android > Android SDK 管理器**） "。

2. 安装**Android 8.0-Oreo**包。 如果使用 Android SDK 模拟器，请确保包含需要的**x86**系统映像：

    [![在 Android SDK 管理器中选择 Android 8.0 包](oreo-images/win/01-android-o-packages.png)](oreo-images/win/01-android-o-packages.png#lightbox)

3. 安装**Android SDK Tools 26.0.2**或更高版本、 **Android SDK 平台工具 26.0.0**或更高版本，以及**Android SDK 生成工具 26.0.0** （或更高版本）：

    [![选择 Android SDK 管理器中 Android SDK Tools 26](oreo-images/win/02-sdk-tools.png)](oreo-images/win/02-sdk-tools.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

1. 启动 SDK 管理器（在 Visual Studio for Mac 中，单击 "**工具" > SDK 管理器**） "。

2. 安装**Android 8.0-Oreo** SDK 包。 如果使用 Android SDK 模拟器，请确保包含需要的**x86**系统映像：

    [![在 SDK 管理器中选择 Android 8.0 包](oreo-images/mac/01-android-o-packages.png)](oreo-images/mac/01-android-o-packages.png#lightbox)

3. 安装**Android SDK Tools 26.0.2**或更高版本、 **Android SDK 平台工具 26.0.0**或更高版本，以及**Android SDK 生成工具 26.0.0** （或更高版本）：

    [![选择 SDK 管理器中 Android SDK Tools 26](oreo-images/mac/02-sdk-tools.png)](oreo-images/mac/02-sdk-tools.png#lightbox)

-----

### <a name="start-a-xamarinandroid-project"></a>启动 Xamarin Android 项目

创建新的 Xamarin Android 项目。 如果不熟悉通过 Xamarin 进行 Android 开发，请参阅[Hello，Android](~/android/get-started/hello-android/index.md)了解如何创建 xamarin Android 项目。

创建 Android 项目时，必须将版本设置配置为面向 Android 8.0 或更高版本。 例如，若要以适用于 Android 8.0 的项目为目标，必须将项目的目标 Android API 级别配置为**android 8.0 （API 26）** 。 建议你同时将目标框架级别设置为 API 26 或更高版本。 有关配置 Android API 级别级别的详细信息，请参阅[了解 ANDROID Api 级别](~/android/app-fundamentals/android-api-levels.md)。

### <a name="configure-an-emulator-or-device"></a>配置仿真程序或设备

如果在安装 Android SDK Tools 26.0 或更高版本后，尝试启动默认基于 GUI 的 AVD Manager，则可能会出现以下错误对话框，指示你使用命令行 AVD 管理器工具**avdmanager** ：

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Android Emulator 管理器警告对话框](oreo-images/win/03-avd-warning.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

![Android Emulator 管理器警告对话框](oreo-images/mac/03-avd-warning.png)

-----

显示此消息是因为 Google 不再提供支持 API 26.0 和更高版本的独立 GUI AVD 管理器。 对于 android 8.0 Oreo，必须使用 Xamarin Android Emulator 管理器或命令行`avdmanager`工具创建适用于 android Oreo 的虚拟设备。

若要使用 Android Device Manager 来创建和管理虚拟设备，请参阅[使用 Android Device Manager 管理虚拟设备](~/android/get-started/installation/android-emulator/device-manager.md)。
若要创建不包含 Android Device Manager 的虚拟设备，请按照下一部分中的步骤进行操作。

#### <a name="creating-virtual-devices-using-avdmanager"></a>使用 avdmanager 创建虚拟设备

若要使用**avdmanager**创建新的虚拟设备，请执行以下步骤：

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. 打开命令提示符窗口，并将`JAVA_HOME`设置为 Java SDK 在计算机上的位置。 对于典型的 Xamarin 安装，你可以使用以下命令：

    ```cmd
    setx JAVA_HOME "C:\Program Files\Java\jdk1.8.0_131"
    ```

2. 将 Android SDK `bin`文件夹的位置添加`PATH`到。
    对于典型的 Xamarin 安装，你可以使用以下命令：

    ```cmd
    setx PATH "%PATH%;C:\Program Files (x86)\Android\android-sdk\tools\bin"
    ```

3. 关闭命令提示符窗口并打开一个新的命令提示符窗口。 使用[avdmanager](https://developer.android.com/studio/command-line/avdmanager.html)命令创建新的虚拟设备。 例如，若要使用 x86 系统映像为 API 级别26创建名为**AVD-8.0**的 AVD，请使用以下命令：

    ```cmd
    avdmanager create avd -n AVD-Oreo-8.0 -k "system-images;android-26;google_apis;x86"
    ```

4. 当提示你是否要**创建自定义硬件配置文件 [no]** 时，你可以输入**no**并接受默认硬件配置文件。 如果你说**是**， **avdmanager**会提示你提供自定义硬件配置文件的问题列表。

**Avdmanager**创建虚拟设备后，该设备将包含在设备下拉菜单中：

[![新 AVD 已添加到设备下拉菜单](oreo-images/win/04-android-o-avd-sml.png)](oreo-images/win/04-android-o-avd.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

1. 打开**终端**窗口，并切换到 Mac 上 Android SDK tools 目录的位置。 对于典型的 Xamarin 安装，你可以使用以下命令：

    ```bash
    cd ~/Library/Developer/Xamarin/android-sdk-macosx/tools/bin
    ```

2. 使用[avdmanager](https://developer.android.com/studio/command-line/avdmanager.html)命令创建新的虚拟设备。 例如，若要使用 x86 系统映像为 API 级别26创建名为**AVD-8.0**的 AVD，请使用以下命令：

    ```bash
    avdmanager create avd -n AVD-Oreo-8.0 -k "system-images;android-26;google_apis;x86"
    ```

3. 当提示你是否要**创建自定义硬件配置文件 [no]** 时，你可以输入**no**并接受默认硬件配置文件。 如果你说**是**， **avdmanager**会提示你提供自定义硬件配置文件的问题列表。

使用**avdmanager**创建虚拟设备后，该设备将包含在设备下拉菜单中：

[![新 AVD 已添加到设备下拉菜单](oreo-images/mac/04-android-o-avd-sml.png)](oreo-images/mac/04-android-o-avd.png#lightbox)

-----

有关配置 Android 仿真程序以进行测试和调试的详细信息，请参阅[Android Emulator 上的调试](~/android/deploy-test/debugging/debug-on-emulator.md)。

如果使用的是物理设备，如结点或像素，可以通过无线（OTA）更新来更新设备，或下载系统映像并直接刷新设备。 有关手动将设备更新到 Android Oreo 的详细信息，请参阅[用于结点和像素设备的工厂映像](https://developers.google.com/android/images)。

## <a name="new-features"></a>新增功能

Android Oreo 引入了各种新特性和功能，如通知通道、通知徽章、XML 中的自定义字体、可下载字体、自动填充和图片。 以下各节重点介绍这些功能，并提供可帮助你在应用程序中开始使用这些功能的链接。

### <a name="notification-channels"></a>通知通道

*通知通道*是应用定义的通知类别。
你可以为需要发送的每种类型的通知创建通知通道，还可以创建通知通道，以反映应用的用户所做的选择。 利用新的 "通知通道" 功能，您可以为用户提供对各种通知的精细控制。 例如，如果要实现消息传递应用，可以为用户创建的每个会话组创建单独的通知通道。

[通知通道](~/android/app-fundamentals/notifications/local-notifications.md#notif-chan)说明了如何创建通知通道，并将其用于发布本地通知。 有关实际的代码示例，请参阅[NotificationChannels](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-o-notificationchannels)示例;此示例应用管理两个通道并设置其他通知选项。

### <a name="notification-badges"></a>通知徽章

通知徽章是显示在应用程序图标上方的小点，如以下屏幕截图所示：

[![应用图标上的示例通知徽章](oreo-images/02-badges-sml.png)](oreo-images/02-badges.png#lightbox)

这些点表示应用程序中与该应用程序图标&ndash;关联的一个或多个通知通道有新的通知。这些是用户尚未解除或操作的通知。 用户可以长时间按一个图标，浏览与通知徽章关联的通知，从 appeaars 的长时间菜单关闭或操作通知。

有关锁屏通知的详细信息，请参阅 Android 开发人员[通知徽章](https://developer.android.com/guide/topics/ui/notifiers/notifications.html#Badges)主题。

### <a name="custom-fonts-in-xml"></a>XML 中的自定义字体

Android Oreo*在 XML 中*引入了字体，这使你可以将自定义字体作为资源合并。 支持 OpenType （ **. otf**）和 TrueType （ **. .ttf**）字体格式。 若要将字体添加为资源，请执行以下操作：

1. 创建**资源/字体**文件夹。

2. 将字体文件（如 **.ttf**和**otf**文件）复制到**资源/字体**。 

3. 如有必要，请重命名每个字体文件，使其遵守 Android 文件命名约定（即，在文件名中只使用小写*a-z*、 *0-9*和下划线）。 例如，可以将字体文件`Pacifico-Regular.ttf`重命名为类似`pacifico.ttf`的内容。

4. 在布局 XML 中使用新`android:fontFamily`属性应用自定义字体。 例如，下面`TextView`的声明使用添加的**pacifico. .ttf**字体资源：

   ```xml
   <TextView
     android:text="Example Text in Pacifico Regular"
     android:layout_width="wrap_content"
     android:layout_height="wrap_content"
     android:fontFamily="@font/pacifico" />
   ```

还可以创建一个描述多个字体的字体系列 XML 文件以及样式和权重的详细信息。 有关详细信息，请参阅 Android Developer [Fonts IN XML](https://developer.android.com/guide/topics/ui/look-and-feel/fonts-in-xml.html)主题。

### <a name="downloadable-fonts"></a>可下载字体

从 Android Oreo 开始，应用可以从提供程序请求字体，而不是将其绑定到 APK。 仅在需要时才从网络下载字体。 此功能可减少 APK 大小、节省电话内存和移动电话数据。 你还可以在 Android API 版本14及更高版本上使用此功能，方法是安装 Android 支持库26包。

当应用需要字体时，可以创建`FontsRequest`对象（指定要下载的字体），然后将其传递`FontsContract`给方法以下载字体。 以下步骤更详细地介绍了字体下载过程：

1. 实例化[FontRequest](https://developer.android.com/reference/android/provider/FontRequest.html)对象。 

2. 子类并实例化[FontsContract. FontRequestCallback](https://developer.android.com/reference/android/provider/FontsContract.FontRequestCallback.html)。

3. 实现[FontRequestCallback. OnTypeFaceRetrieved](https://developer.android.com/reference/android/provider/FontsContract.FontRequestCallback.html#onTypefaceRetrieved%28android.graphics.Typeface%29)方法，该方法用于处理字体请求的完成。

4. 实现[FontRequestCallback. OnTypeFaceRequestFailed](https://developer.android.com/reference/android/provider/FontsContract.FontRequestCallback.html#onTypefaceRequestFailed%28int%29)方法，该方法用于向应用程序通知在执行字体请求过程中发生的任何错误。

5. 调用[FontsContract. RequestFonts](https://developer.android.com/reference/android/provider/FontsContract.html#requestFonts(android.content.Context,%20android.provider.FontRequest,%20android.os.Handler,%20android.os.CancellationSignal,%20android.provider.FontsContract.FontRequestCallback))方法可从字体提供程序检索字体。 

当你调用`RequestFonts`方法时，它首先会检查是否在本地缓存了该字体（从以前对的`RequestFont`调用）。 如果未缓存此`OnTypeFaceRetrieved`方法，它将调用字体提供程序，以异步方式检索字体，然后通过调用方法将结果传递回应用程序。

[可下载字体](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-o-downloadablefonts)示例演示如何使用 Android Oreo 中引入的可下载字体功能。 

有关下载字体的详细信息，请参阅 Android Developer[可下载字体](https://developer.android.com/guide/topics/ui/look-and-feel/downloadable-fonts.html)主题。

### <a name="autofill"></a>自动填充

Android Oreo 中的新自动_填充_框架使用户能够更轻松地处理重复性任务，如登录、帐户创建和信用卡交易。 用户花费更少的时间重新键入信息（这可能会导致输入错误）。 在您的应用程序可以使用自动填充框架之前，必须在系统设置中启用自动填充服务（用户可以启用或禁用自动填充）。

[AutofillFramework](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-o-autofillframework)示例演示如何使用自动填充框架。 它包括具有应 autofilled 的视图的客户端活动的实现，以及可向客户端活动提供自动填充数据的服务。

有关新的自动填充功能以及如何优化应用程序进行自动填充的详细信息，请参阅 Android 开发人员自动[填充框架](https://developer.android.com/guide/topics/text/autofill.html)主题。

### <a name="picture-in-picture-pip"></a>图片（PIP）

Android Oreo 使活动可以在图片内（PIP）模式下启动，从而覆盖另一个活动的屏幕。 此功能适用于视频播放。

若要指定应用程序的活动可使用 PIP 模式，请在 Android 清单中将以下标志设置为 true：

```xml
android:supportsPictureInPicture
```

若要指定活动处于 PIP 模式时的行为方式，请使用新的[PictureInPictureParams](https://developer.android.com/reference/android/app/PictureInPictureParams.html)对象。 `PictureInPictureParams`表示一组参数，这些参数用于初始化和更新 PIP 模式下的活动（例如活动的首选纵横比）。 Android Oreo `Activity`中添加了以下新 PIP 方法：

- [EnterPictureInPictureMode](https://developer.android.com/reference/android/app/Activity.html#enterPictureInPictureMode%28android.app.PictureInPictureParams%29)&ndash;将活动置于 PIP 模式下。 活动位于屏幕的角上，而屏幕的其余部分则由屏幕上的前一个活动填充。

- [SetPictureInPictureParams](https://developer.android.com/reference/android/app/Activity.html#setPictureInPictureParams%28android.app.PictureInPictureParams%29)&ndash;更新活动的 PIP 配置设置（例如，纵横比的更改）。

[PictureInPicture](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-o-pictureinpicture)示例演示了在 Oreo 中引入的手持式设备的图画（PiP）模式的基本用法。 此示例播放一个视频，在显示模式或其他活动之间来回切换时，该视频无间断地继续。

### <a name="other-features"></a>其他功能

Android Oreo 包含许多其他新功能，如表情符号支持库、位置 API、背景限制、应用的宽色域颜色、新音频编解码器、Web 视图增强功能、改进的键盘导航支持和用于高性能低延迟音频，有关这些功能的详细信息，请参阅 Android 开发人员[Android Oreo 功能和 api](https://developer.android.com/about/versions/oreo/android-8.0.html)主题。

## <a name="behavior-changes"></a>行为更改

Android Oreo 包括各种系统和 API 行为更改，这些更改可能会影响现有应用程序的功能。 这些更改如下所述。

### <a name="background-execution-limits"></a>后台执行限制

为了改进用户体验，Android Oreo 对应用在后台运行时可以执行的操作施加限制。 例如，如果用户正在观看视频或玩游戏，在后台运行的应用可能会影响在前台运行的视频密集型应用程序的性能。 因此，Android Oreo 在不直接与用户交互的应用上施加以下限制：

1. **后台服务限制**&ndash;当应用在后台运行时，它有几分钟的时间，仍允许创建和使用服务。 在该窗口结束时，Android 会停止应用的后台服务，并将其视为_空闲状态_。

2. **广播限制**&ndash; Android 7.0 （API 25）对应用注册接收的广播施加限制。 Android Oreo 使得这些限制更严格。 例如，Android Oreo 应用程序无法再为其清单中的隐式广播注册广播接收器。

有关新的后台执行限制的详细信息，请参阅 Android 开发人员[后台执行限制](https://developer.android.com/about/versions/oreo/background.html)主题。

### <a name="breaking-changes"></a>重大更改

面向 Android Oreo 或更高版本的应用程序必须修改其应用程序，以支持以下更改（如果适用）：

- Android Oreo 弃用设置单个通知的优先级。 而是在创建通知通道时设置建议的重要性级别。 你分配给通知通道的重要性级别适用于你向其发送的所有通知消息。

- 对于面向 Android Oreo 的应用`PendingIntent.GetService()` ，由于在后台启动的服务的新限制，因此不起作用。 如果你面向的是 Android Oreo，则应该改用[PendingIntent。](xref:Android.App.PendingIntent.GetBroadcast*)  

## <a name="sample-code"></a>代码示例

提供了多个 Xamarin 示例，用于演示如何利用 Android Oreo 功能：

- [NotificationsChannels](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-o-notificationchannels)演示如何使用 Android Oreo 中引入的新通知通道系统。 此示例管理两个通知通道：一个具有默认重要性，另一个具有高重要性。

- [PictureInPicture](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-o-pictureinpicture)演示了在 Oreo 中引入的手持式设备的图画（PiP）模式的基本用法。 此示例播放一个视频，在显示模式或其他活动之间来回切换时，该视频无间断地继续。

- [AutofillFramework](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-o-autofillframework)演示如何使用自动填充框架。 它包括具有应 autofilled 的视图的客户端活动的实现，以及可向客户端活动提供自动填充数据的服务。

- [可下载字体](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-o-downloadablefonts)提供了有关如何使用前面所述的可下载字体功能的示例。

- [EmojiCompat](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-o-emojicompat)演示 EmojiCompat 支持库的使用情况。 您可以使用此库来防止您的应用程序将缺少的表情符号字符显示为 "tofu" 字符。

- [位置更新挂起意向](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-o-androidplaylocation-locupdpendintent)说明位置 API 的使用情况，以获取有关使用`PendingIntent`的设备位置的更新。

- [位置更新前景服务](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-o-androidplaylocation-locupdfgservice)演示了如何使用定位 API，通过绑定和启动的前台服务获取有关设备位置的更新。

## <a name="video"></a>视频

> [!VIDEO https://youtube.com/embed/OuvEcaMO-Ho]

**Android 8.0 Oreo 开发C#**

## <a name="summary"></a>总结

本文介绍了 Android Oreo，并介绍了如何安装和配置适用于 Android Oreo 上的 Xamarin 开发的最新工具和包。 其中概述了 Android Oreo 中提供的主要功能，并提供了多个新功能的示例源代码的链接。 它包含指向 API 文档和 Android 开发人员主题的链接，可帮助你开始创建适用于 Android 的应用 Oreo。 它还突出显示了可能影响现有应用的最重要的 Android Oreo 行为更改。

## <a name="related-links"></a>相关链接

- [Android 8.0 Oreo](https://developer.android.com/index.html)
