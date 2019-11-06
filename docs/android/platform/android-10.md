---
title: Android 10 功能
description: 如何开始使用 Xamarin 开发适用于 Android 10 的应用。
ms.assetid: B3342772-FB88-4B7F-BC15-8BC78EED749E
author: JonDouglas
ms.author: jodou
ms.date: 09/17/2019
ms.openlocfilehash: c19c9e5bd279824ea2d3e4e9f88857388f786a2c
ms.sourcegitcommit: b11dc46a9ba23483195e923de88cbef173730087
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/05/2019
ms.locfileid: "73612270"
---
# <a name="android-10-with-xamarin"></a>带有 Xamarin 的 Android 10

_如何开始使用 Xamarin 开发适用于 Android 10 的应用。_

Android 10 现在可从 Google 获取。 此版本提供了许多新功能和 Api，其中许多新功能是在最新 Android 设备中利用新硬件功能所必需的。

![Android 10 徽标](~/android/platform/android-10-images/android10_black.png)

本文旨在帮助你开始开发适用于 Android 10 的 Xamarin Android 应用。 它说明了如何安装所需的更新、如何配置 SDK，并准备好用于测试的模拟器或设备。 它还提供了 Android 10 中新增功能的概述，并提供了演示如何使用某些主要 Android 10 功能的示例源代码。

Xamarin 10.0 提供对 Android 10 的支持。 有关适用于 Android 10 的 Xamarin Android 支持的详细信息，请参阅 " [xamarin 10.0 发行说明](https://docs.microsoft.com/xamarin/android/release-notes/10/10.0)"。

## <a name="requirements"></a>要求

以下列表是在基于 Xamarin 的应用中使用 Android 10 功能所必需的：

- **Visual studio** -建议使用 visual studio 2019。 在 Windows update 上，Visual Studio 2019 版本16.3 或更高版本。 在 macOS 上，更新为适用于 Mac 版本8.3 或更高版本的 Visual Studio 2019。
- **Xamarin** -xamarin 10.0 或更高版本必须随 visual studio 一起安装（在 Windows 上，将在**移动开发**中自动安装 xamarin，并将其作为**Visual Studio 的一部分安装对于 Mac 安装程序**）
- **Java 开发人员工具包**-Xamarin 10.0 开发需要 JDK 8。 Microsoft 的 OpenJDK 分发版将作为 Visual Studio 的一部分自动安装。
- 必须通过 Android SDK 管理器安装 Android SDK API 29 **Android SDK** 。

## <a name="get-started"></a>入门

若要开始通过 Xamarin 开发 Android 10 应用，必须先下载并安装最新的工具和 SDK 包，然后才能创建第一个 Android 10 项目：

1. **建议使用 Visual Studio 2019**。 更新到 Visual Studio 2019 版本16.3 或更高版本。 如果使用 Visual Studio for Mac 2019，请更新到 Visual Studio 2019 for Mac 版本8.3 或更高版本。
2. 通过 SDK 管理器安装**Android 10 （API 29）** 包和工具。
    - Android 10 （API 29） SDK 平台
    - Android 10 （API 29）系统映像
    - Android SDK 版本-工具 29.0.0 +
    - Android SDK 平台-工具 29.0.0 +
    - Android Emulator 29.0.0 +
3. 创建面向 Android 10.0 的新 Xamarin 项目。
4. 配置仿真程序或设备以便测试 Android 10 应用。

下面介绍了其中的每个步骤：

### <a name="update-visual-studio"></a>更新 Visual Studio

建议使用 Visual Studio 2019 通过 Xamarin 生成 Android 10 应用。

如果你使用的是 Visual Studio 2019，请更新到 Visual Studio 2019 版本16.3 或更高版本（有关说明，请参阅[将 Visual studio 2019 更新到最新版本](https://docs.microsoft.com/visualstudio/install/update-visual-studio)）。 在 macOS 上，更新到 Visual Studio 2019 for Mac 8.3 或更高版本（有关说明，请参阅[将 Visual studio 2019 For Mac 更新到最新版本](https://docs.microsoft.com/visualstudio/mac/update)）。

### <a name="install-the-android-sdk"></a>安装 Android SDK

若要使用 Xamarin 10.0 创建项目，必须首先使用 Android SDK 管理器安装适用于 Android 10 的 SDK 平台 **（API 级别29）** 。

1. 启动 SDK 管理器。 在 Visual Studio 中，单击 "**工具" > Android > Android SDK 管理器 "。** 在 Visual Studio for Mac 中，单击 "**工具" > "SDK 管理器"。**
2. 在右下角，单击齿轮图标，并选择 "**存储库 > Google （不支持）"：**

    ![Android SDK 管理器存储库选择](~/android/platform/android-10-images/sdkrepository.png)

3. 安装**Android 10 SDK 平台**包（在 "**平台**" 选项卡中作为**Android SDK 平台 29**列出）（有关使用 SDK 管理器的详细信息，请参阅[Android SDK 安装](https://docs.microsoft.com/xamarin/android/get-started/installation/android-sdk)）：

    ![Android SDK Manager 平台选项卡](~/android/platform/android-10-images/sdkplatforms.png)

### <a name="create-a-xamarinandroid-project"></a>创建 Xamarin Android 项目

创建新的 Xamarin Android 项目。 如果不熟悉通过 Xamarin 进行 Android 开发，请参阅[Hello，Android](https://docs.microsoft.com/xamarin/android/get-started/hello-android/index)了解如何创建 xamarin Android 项目。

创建 Android 项目时，必须将版本设置配置为面向 Android 10.0 或更高版本。 例如，若要以适用于 Android 10 的项目为目标，必须将项目的目标 Android API 级别配置为**android 10.0 （API 29）** 。 这包括**目标 Framework 版本**和**目标 ANDROID SDK 版本**到 API 29 或更高版本。 有关配置 Android API 级别的详细信息，请参阅[了解 ANDROID Api 级别。](https://docs.microsoft.com/xamarin/android/app-fundamentals/android-api-levels)

![Xamarin Android 目标框架](~/android/platform/android-10-images/targetframework.png)

### <a name="configure-a-device-or-emulator"></a>配置设备或仿真程序

如果你使用的是物理设备，如像素，则可以通过转到 "`System`"  > 在手机的设置中 `System update` > `Check for update` 来下载 Android 10 更新。 如果你想要闪现设备，请参阅在设备上闪烁[出厂映像](https://developers.google.com/android/images)或[OTA 映像](https://developers.google.com/android/ota)的说明。

如果使用的是仿真程序，请为 API 级别29创建虚拟设备，并选择基于 x86 的映像。 有关使用 Android Device Manager 创建和管理虚拟设备的信息，请参阅[使用 Android Device Manager 管理虚拟设备。](https://docs.microsoft.com/xamarin/android/get-started/installation/android-emulator/device-manager) 有关使用 Android Emulator 进行测试和调试的信息，请参阅[Android Emulator 上的调试。](https://docs.microsoft.com/xamarin/android/deploy-test/debugging/debug-on-emulator)

## <a name="new-features"></a>新增功能

Android 10 引入了多种新功能。 其中一些新功能旨在利用最新 Android 设备提供的新硬件功能，而其他功能旨在进一步增强 Android 用户体验：

## <a name="enhance-your-app-with-android-10-features-and-apis"></a>利用 Android 10 功能和 Api 增强你的应用

接下来，当你准备就绪时，深入了解 Android 10 并了解你可以使用的 [新功能和 api](https://developer.android.com/preview/api-overview.html) 。 下面是一些入门的主要功能。

对于每个应用，建议采用以下功能：

- **深色主题：**  确保通过添加 [深色主题](https://developer.android.com/preview/features/darktheme) 或启用 " [强制深色](https://developer.android.com/preview/features/darktheme#force_dark)" 来启用系统级深色主题的用户的体验是一致的。

![深色主题](~/android/platform/android-10-images/darktheme.png)

- 在应用中**支持 [gestural 导航](https://developer.android.com/preview/features/gesturalnav)**  ，方法是：转到边缘，并确保自定义笔势与系统导航手势互补。

![手势导航](~/android/platform/android-10-images/gesturenavigation.png)

- **针对 foldables 进行优化：** 通过 [针对 foldables 进行优化](https://developer.android.com/preview/features/foldables)， 提供当今创新设备上无缝的边缘到边缘体验。

![可折叠](~/android/platform/android-10-images/foldable.png)

如果与应用相关，建议使用以下功能：

- **更多交互式通知：**  如果你的通知包括消息，请 [在通知中启用建议的答复和操作](https://developer.android.com/preview/features#smart-suggestions) 以使用户参与，并让他们立即采取措施。
- **更好的生物识别：**  如果你使用生物识别身份验证，请转到 [BiometricPrompt](https://developer.android.com/reference/androidx/biometric/BiometricPrompt)，这是支持新式设备上指纹身份验证的首选方式。
- **丰富的记录：**  支持字幕或游戏录制，请启用 [音频播放捕获](https://developer.android.com/preview/features/playback-capture)。 这是一个很好的方法，可让用户更方便地访问用户并使应用更易于访问。
- **更好的编解码器：**  媒体应用程序，请尝试 [AV1](https://en.wikipedia.org/wiki/AV1) 用于视频流式处理和 [HDR10 +](https://en.wikipedia.org/wiki/High-dynamic-range_video#HDR10+) ，实现高动态范围视频。 对于语音和音乐流，可以使用 [Opus](http://opus-codec.org/)编码，对于音乐家，可使用 [本机 MIDI API](https://developer.android.com/preview/features/midi) 。
- **更好的网络 api：**  如果你的应用程序通过 Wi-fi 管理 IoT 设备，请尝试为配置、下载或打印等功能 新的 [网络连接 api](https://developer.android.com/preview/features#peer2peer) 。

这只是 Android 10 中许多新功能和 Api 的一部分。 若要查看所有这些资源，请访问 [适用于开发人员的 Android 10 站点](https://developer.android.com/about/versions/10/highlights)。

## <a name="behavior-changes"></a>行为变更

如果将 "目标 Android 版本" 设置为 "API 级别 29"，即使未实现上述新功能，cann 也会影响应用程序的行为。 以下列表概述了这些更改：

- [为了确保应用稳定性和兼容性，android 平台现在会限制你的应用可在 Android 10 中使用的非 SDK 接口](https://developer.android.com/about/versions/10/behavior-changes-10#non-sdk-restrictions)。
- [共享内存已更改](https://developer.android.com/about/versions/10/behavior-changes-10#shared-memory)。
- [Android 运行时 & AOT 正确性](https://developer.android.com/about/versions/10/behavior-changes-10#system-only-oat)。
- [对于全屏意向，权限必须请求 `USE_FULL_SCREEN_INTENT`](https://developer.android.com/about/versions/10/behavior-changes-10#full-screen-intents)。
- [支持 foldables](https://developer.android.com/about/versions/10/behavior-changes-10#foldables)。

## <a name="summary"></a>总结

本文介绍了 Android 10，并说明了如何安装和配置最新的工具和包，以便安装和配置适用于适用于 android 的 Xamarin Android 开发。 其中提供了 Android 10 中提供的主要功能的概述。 它包含指向 API 文档和 Android 开发人员主题的链接，可帮助你开始创建适用于 Android 10 的应用。 它还重点介绍了可能影响现有应用的最重要的 Android 10 行为更改。

## <a name="related-links"></a>相关链接

- [Android 10](https://developer.android.com/about/versions/10)
