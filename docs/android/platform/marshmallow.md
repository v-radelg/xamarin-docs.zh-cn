---
title: Marshmallow 功能
description: 本文可帮助你开始使用 Xamarin.Android 开发适用于 Android 6.0 Marshmallow 应用中使用。
ms.prod: xamarin
ms.assetid: E4D6F183-98D2-460A-9D65-937639A899E0
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: a396f4fe59db36b134843d2538bcb470a452a85b
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/08/2019
ms.locfileid: "57668577"
---
# <a name="marshmallow-features"></a>Marshmallow 功能

_本文可帮助你开始使用 Xamarin.Android 开发适用于 Android 6.0 Marshmallow 应用中使用。_

本文概述了 Android 6.0 Marshmallow 中的新增功能、 介绍如何进行准备 Xamarin.Android 进行 Android Marshmallow 开发，并提供指向示例应用程序，展示了如何利用新的 Android Marshmallow在 Xamarin.Android 应用中的功能。 


## <a name="overview"></a>概述

[Android 6.0 Marshmallow](https://developer.android.com/about/versions/marshmallow/index.html)，是下一步的主要 Android Android Lollipop 之后的版本。
Xamarin.Android 支持 Android Marshmallow，包括：

-   **API 23/Android 6.0 绑定** &ndash; Android 6.0 添加许多新 Api，如下所述的新功能; 当你的目标 API 级别 23 时，这些 API 是适用于 Xamarin.Android 应用。 有关 Android 6.0 Api 的详细信息，请参阅[Android 6.0 Api](https://developer.android.com/preview/api-overview.html)。 

[![平板电脑和手机运行 Marshmallow Hero 图像](marshmallow-images/android-m-hero-sml.png)](marshmallow-images/android-m-hero.png#lightbox)

尽管 Marshmallow 版本主要侧重于"波兰语和质量"，但它还为 Xamarin.Android 开发人员提供感兴趣的许多新功能。 这些功能包括： 

-   **运行时权限**&ndash;此增强功能使用户能够在运行时批准的安全权限的情况。 

-   **身份验证的改进**&ndash;从 Android Marshmallow 开始，应用现在可以使用指纹传感器来进行身份验证用户和一个新*确认凭据*功能最大程度减少需要输入密码。 

-   **应用链接**&ndash;此功能有助于消除无**应用选择器**弹出通过自动将应用与 web 域相关联。 

-   **直接共享**&ndash;可以定义*直接共享目标*，让共享快速、 直观的用户; 此功能允许用户与其他应用共享内容。 

-   **语音交互**&ndash;这个新的 API 可用于构建对话式语音功能到你的应用。 

-   **4 K 显示模式**&ndash;在 Android Marshmallow，您的应用程序可以请求 4k 支持它的硬件上的显示分辨率。 

-   **音频的新功能** &ndash; Marshmallow 从开始，Android 现在支持 MIDI 协议。 它还提供了新类，以创建数字音频捕获和播放对象，并提供用于将音频和输入设备相关联的新 API 挂钩。 

-   **视频的新功能** &ndash; Marshmallow 提供了一个新类，可帮助应用程序呈现音频和视频流同步; 此类还提供对动态播放速度的支持。 

-   **Android for Work** &ndash; Marshmallow 包括增强的控件为公司拥有的单用户设备。 它支持无提示安装和卸载的应用程序的设备所有者、 自动接受系统更新、 改进了的证书管理、 数据使用情况跟踪、 权限管理和工作状态通知。 

-   **材料设计支持库**&ndash;的新*设计支持库*提供设计组件和模式，使你更轻松地将内置到应用 Material Design 外观和感觉。 

此外，Android m，已发布了很多核心 Android 库更新并且这些更新为 Android M 和早期版本的 Android 提供的新功能。

此外，Android Marshmallow 与已发布了很多核心 Android 库更新并且这些更新为 Android Marshmallow 和早期版本的 Android 提供的新功能。 本文介绍如何开始使用 Android Marshmallow 构建应用程序，并提供在 Android 6.0 中重点介绍新功能的概述。 

## <a name="requirements"></a>要求

以下是所需的基于 Xamarin 的应用中使用新的 Android Marshmallow 功能： 

-   **Xamarin.Android** &ndash; Xamarin.Android 5.1.7.12 或更高版本必须安装并配置了 Visual Studio 或 Xamarin Studio。

-   **Visual Studio for Mac**或**Visual Studio** &ndash;如果使用 Visual Studio for Mac 版本 5.9.7.22 或更高版本。 如果使用的 Visual Studio，版本 3.11.1537 或更高版本的 Visual Studio 的 Xamarin 工具是必需的。 

-   **Android SDK** &ndash; Android SDK 6.0 (API 23) 或更高版本必须安装通过 Android SDK 管理器。

-   **Java 开发人员工具包** &ndash; Xamarin.Android 需要[JDK 1.8](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)或更高，如果你正在开发的 API 级别 24 或更高版本 （JDK 1.8 还支持 API 级别低于 24，包括 Marshmallow）。 如果使用的自定义控件或窗体预览程序，则需要 JDK 1.8 的 64 位版本。

你可以继续使用[JDK 1.7](https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html)如果您是开发专门针对 API 级别 23 或更早版本。 


## <a name="getting-started"></a>入门

若要开始使用 Xamarin.Android 的 Android Marshmallow，必须下载并安装最新工具和 SDK 包，然后才能创建 Android Marshmallow 项目： 

1.  安装最新的 Xamarin 更新从**稳定**通道。 

2.  安装 Android 6.0 Marshmallow SDK 包和工具。

3.  创建新的 Xamarin.Android 项目面向 Android 6.0 Marshmallow (API 级别 23)。 

4.  为 Android Marshmallow 配置仿真器或设备。

以下部分解释了每个步骤：


### <a name="install-xamarin-updates"></a>安装 Xamarin 更新

若要更新 Xamarin，使其包括支持 Android 6.0 Marshmallow，更改到的更新通道**稳定**并安装所有更新。 有关从更新通道安装更新的详细信息，请参阅[更改更新通道](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/change_updates_channel)。 


### <a name="install-the-android-60-sdk"></a>安装 Android 6.0 SDK

若要为 Android Marshmallow 创建 Xamarin.Android 项目，您必须首先使用 Android SDK 管理器安装 Android 6.0 SDK:

-   启动 Android SDK 管理器 (在 Visual Studio for Mac 中，使用**工具 > SDK 管理器**; 在 Visual Studio 中，使用**工具 > Android > Android SDK 管理器**) 并安装最新的 Android SDK Tools:

    [![选择 Android SDK 管理器中的 Android SDK 工具](marshmallow-images/mnc-preview-tools.png)](marshmallow-images/mnc-preview-tools.png#lightbox)

-   此外，安装最新**Android 6.0** SDK 包：

    [![选择 Android SDK 管理器中的 Android 6.0 SDK 包](marshmallow-images/mnc-preview-packages.png)](marshmallow-images/mnc-preview-packages.png#lightbox)

必须安装 Android SDK Tools 修订 24.3.4 或更高版本。
有关使用 Android SDK 管理器安装 Android 6.0 SDK 的详细信息，请参阅[SDK 管理器](https://developer.android.com/tools/help/sdk-manager.html)。



### <a name="start-a-xamarinandroid-project"></a>启动 Xamarin.Android 项目

创建新的 Xamarin.Android 项目。 如果您不熟悉如何使用 Xamarin 进行 Android 开发，请参阅[Hello，Android](~/android/get-started/hello-android/index.md) ，了解如何创建 Android 项目。 

创建 Android 项目时，必须配置的版本设置应用到目标 Android 6.0 MarshMallow。 若要为 Marshmallow 针对你的项目，必须配置的项目**API 级别 23 （Xamarin.Android 6.0 版支持）**。 有关配置 Android API 级别级别的详细信息，请参阅[了解 Android API 级别](~/android/app-fundamentals/android-api-levels.md)。



### <a name="configure-an-emulator-or-device"></a>配置仿真器或设备

如果使用仿真程序，启动 Android AVD 管理器，并创建新的设备使用以下设置：

-   设备:Nexus 5、 6 或 9。
-   目标:Android 6.0-API 级别 23
-   ABI: x86

例如，此虚拟设备被配置为模拟 Nexus 5:

[![配置使用 Nexus 5 设备、 Android 6.0 目标和 Intel Atom (x86) 的 AVD](marshmallow-images/android-m-avd.png)](marshmallow-images/android-m-avd.png#lightbox)

如果使用的物理设备，如 Nexus 5，6，或 9，可以安装 Android Marshmallow 的预览图像。 有关将设备更新到 Android Marshmallow 的详细信息，请参阅[硬件系统映像](https://developer.android.com/preview/download.html#images)。



## <a name="new-features"></a>新增功能

许多 Android Marshmallow 中引入的更改被侧重于改进 Android 用户体验、 提高性能，并修复的 bug。 但是，Marshmallow 还引入了一些广泛更改 Android 平台的基础知识。 以下部分突出显示这些增强功能，并提供链接，以帮助你开始在应用程序中使用新的 Android Marshmallow 功能。 



### <a name="runtime-permissions"></a>运行时权限

Android 权限系统已显著优化，并简化自 Android 棒糖形。 在 Android Marshmallow 中的用户授予权限的情况的基础，在运行时中，而不是在安装时间。 若要支持此功能在 Android Marshmallow 上及更高版本，设计应用时提示用户获取在运行时 （在所需权限的上下文中） 的权限。 此更改使用户更轻松地开始使用您的应用程序立即因为它简化了安装和升级您的应用程序的过程。 

请参阅[在 Android Marshmallow 请求运行时权限](https://blog.xamarin.com/requesting-runtime-permissions-in-android-marshmallow/)有关 Xamarin.Android 应用中实现运行时权限 （包括代码示例） 的详细信息。
Xamarin 还提供了示例应用，演示了在 Android Marshmallow （及更高版本），运行时权限的工作原理：[RuntimePermissions](https://developer.xamarin.com/samples/monodroid/android-m/RuntimePermissions)。

此示例应用演示如下：

-   如何检查以及在运行时的请求权限。
-   如何声明适用于 Android M 设备的权限。

若要使用此示例应用：

1.  点击**照相机**或**联系人**按钮以显示权限请求对话框。
2.  授予查看照相机或联系人片段的权限。

有关在 Android Marshmallow 中新的运行时权限功能的详细信息，请参阅[使用系统权限](https://developer.android.com/preview/features/runtime-permissions.html)。



### <a name="authentication-enhancements"></a>身份验证增强功能

Android Marshmallow 包含使您不再需要的密码的两个身份验证增强功能：

-   **指纹身份验证**&ndash;使用指纹扫描用户进行身份验证。

-   **确认凭据**&ndash;基于多长时间，设备已解锁的用户进行身份验证。

链接和下一步所述的示例应用程序可以借助这些新功能来帮助你熟悉。


#### <a name="fingerprint-authentication"></a>指纹身份验证

在设备上支持的具有指纹扫描硬件，可以使用新`FingerPrintManager`类，以对用户进行身份验证。
有关在 Android Marshmallow 指纹身份验证功能的详细信息，请参阅[指纹身份验证](https://developer.android.com/preview/api-overview.html#fingerprint-authentication)。

Xamarin 提供了示例应用，演示了如何使用已注册的指纹进行身份验证应用程序中的用户：[FingerprintDialog](https://developer.xamarin.com/samples/monodroid/android-m/FingerprintDialog)。

若要使用此示例应用：

1.  触摸**采购**按钮以打开指纹身份验证对话框。
2.  在你已注册的指纹进行身份验证中进行扫描。

请注意，此示例应用程序要求使用指纹读取器的设备。
此应用不存储你的指纹 （或你的密码）。



#### <a name="voice-interactions"></a>语音交互

在 Android Marshmallow 中引入的新的语音交互功能允许您的应用程序的用户使用他们的语音以确认操作并从选项列表中选择。 有关语音交互的详细信息，请参阅[语音交互 API 概述](https://developers.google.com/voice-actions/interaction/)。 

请参阅[添加到 Android 应用使用的语音交互会话](https://blog.xamarin.com/add-a-conversation-to-your-android-app-with-voice-interactions/)有关在 Xamarin.Android 应用中实现的语音交互 （包括代码示例） 的详细信息。
提供了示例应用演示如何在 Xamarin.Android 应用程序中使用语音交互 API:[语音交互](https://github.com/jamesmontemagno/MarshmallowSamples/tree/master/VoiceInteractions)。



#### <a name="confirm-credential"></a>确认凭据

使用新*确认凭据*功能的 Android Marshmallow 可以释放用户无需记住，并且通过进行身份验证根据多长时间，其设备已解锁其输入特定于应用的密码。
若要执行此操作，您使用的新`SetUserAuthenticationValidityDurationSeconds`方法的`KeyGenerator`。 使用`KeyGuardManager`的`CreateConfirmDeviceCredentialIntent`方法重新进行身份验证从您的应用程序中的用户。 有关在 Android Marshmallow 此新功能的详细信息，请参阅[确认凭据](https://developer.android.com/preview/api-overview.html#confirm-credential)。

Xamarin 提供的示例应用演示了如何使用设备凭据 （如 PIN、 图案或密码） 的应用程序中：[ConfirmCredential](https://developer.xamarin.com/samples/monodroid/android-m/ConfirmCredential/)

若要使用此示例应用：

1.  设置你的设备上安全锁定屏幕 (**安全 > 安全 > Screenlock**)。
2.  点击**采购**按钮，然后确认安全锁定屏幕凭据。



### <a name="chrome-custom-tabs"></a>Chrome 自定义选项卡

在用户点击 URL 时，应用程序开发人员面临一个选择： 应用程序可以启动浏览器或使用基于应用程序内浏览器`WebView`。 这两个选项提出了挑战&ndash;启动浏览器是可自定义，而不是大量上下文切换`WebView`s 与浏览器不共享状态。 此外，使用的`WebView`s 可以添加额外的维护开销。 

*Chrome 自定义选项卡*使您可以轻松地完美地而无需离开您的应用程序用户显示与 Chrome 强大的网站。 此功能可为您的应用程序提供更好地控制用户的 web 体验;使本机之间的转换和 web 内容更加无缝而不必求助于`WebView`。 Chrome 的外观和通过自定义以下大家也会影响您的应用程序： 

-   工具栏颜色

-   进入和退出动画

-   Chrome 工具栏和溢出菜单中的自定义操作

-   开始前 chrome 和内容预提取 （的加载速度更快）

若要充分利用此功能的 Xamarin.Android 应用程序中，下载并安装[Android 支持自定义选项卡库](https://www.nuget.org/packages/Xamarin.Android.Support.CustomTabs/)。
有关此功能的详细信息，请参阅[Chrome 自定义选项卡](https://developer.chrome.com/multidevice/android/customtabs)。



### <a name="material-design-support-library"></a>材料设计支持库

Android Lollipop 引入[Material Design](http://www.google.com/design/spec/material-design/introduction.html)作为新的设计语言来刷新 Android 体验 (请参阅[材料主题](~/android/user-interface/material-theme.md)有关 Xamarin.Android 应用中使用材料设计)。 Google 引入了与 Android Marshmallow *Android 设计支持库*为了简化应用程序开发人员采用材料设计外观和感觉。 此库包括以下组件：

-   **CoordinatorLayout** &ndash;的新`CoordinatorLayout`小组件是与类似，但比功能更强大`FrameLayout`。 可以使用`CoordinatorLayout`作为子视图的容器或一个顶级的布局，和它提供了`layout_anchor`特性的定位点相对于其他视图的视图。

-   **折叠工具栏**&ndash;的新`CollapsingToolbarLayout`是折叠的应用栏，它是包装`Toolbar`。 (请注意，*应用程序栏*是什么以前称为*操作栏*。)

-   **浮点操作按钮**&ndash;表示应用程序的接口上的主要操作圆形按钮。

-   **浮点标签编辑文本**&ndash;使用新`TextInputLayout`小组件 (用于包装`EditText`) 时提示用户输入文本，则隐藏显示浮动的标签。

-   **导航视图**&ndash;新`NavigationView`小组件可帮助你为用户更轻松地导航的方式使用导航抽屉。

-   **Snackbar** &ndash;新`SnackBar`小组件是一个轻型的反馈机制 （类似于 toast 通知），在屏幕上，使其不显示最重要的屏幕上的其他元素的底部显示一个简短的消息。

-   **材料的选项卡**&ndash;新`TabLayout`小组件提供了水平布局，用于为应用程序中实现的顶级导航方法显示选项卡。

若要充分利用[设计支持库](https://developer.android.com/tools/support-library/features.html#design)在 Xamarin.Android 应用中，下载并安装 Xamarin [Xamarin 支持库设计](https://www.nuget.org/packages/Xamarin.Android.Support.Design/)NuGet 包。

请参阅[美观的材料设计与 Android 支持设计库](https://blog.xamarin.com/add-beautiful-material-design-with-the-android-support-design-library/)有关使用 Xamarin.Android 应用中的材料设计支持库 （包括代码示例） 的详细信息。
Xamarin 提供了示例应用，演示了新的 Android 设计库在 Xamarin.Android &ndash; [Cheesesquare](https://developer.xamarin.com/samples/monodroid/android5.0/Cheesesquare)。
此示例演示了设计库的以下功能：


-   折叠工具栏
-   浮动的操作按钮
-   视图锚定
-   NavigationView
-   Snackbar

有关设计库的详细信息，请参阅[Android 设计支持库](http://android-developers.blogspot.co.at/2015/05/android-design-support-library.html)Android 开发人员的博客中。


### <a name="additional-library-updates"></a>其他库更新

除了 Android Marshmallow，Google 已宣布对几个核心 Android 库的相关的更新。 Xamarin 提供了 Xamarin.Android 支持这些更新通过多个预览版本的 NuGet 包： 

-   [Google Play Services](https://www.nuget.org/packages?q=Xamarin+Google+Play+Services) &ndash; Google Play 服务的最新版本包括新*应用程序邀请*功能，从而使用户能够与朋友共享自己的应用程序。 有关此功能的详细信息，请参阅[使用 Google 的应用程序邀请的展开您的应用程序的市场宣传](https://blog.xamarin.com/expand-your-apps-reach-with-googles-app-invites/)。 

-   [Android 支持库](https://www.nuget.org/packages?q=xamarin+support+library)&ndash;这些 Nuget 提供的同时提供向后兼容版本的 Android 框架 Api 属性仅适用于库 Api 的功能。 

-   [Android 可穿戴库](https://www.nuget.org/packages/Xamarin.Android.Wear)&ndash;此 NuGet 包括 Google Play 服务绑定。 可穿戴库的最新版本为 Android Wear 平台带来了新功能 （包括用于自定义应用更易于导航）。 


## <a name="summary"></a>总结

本文引入 Android Marshmallow 并介绍了如何安装和 Marshmallow 上配置的最新工具和 Xamarin.Android 开发的包。 它还提供有关 Xamarin.Android 开发的最令人兴奋的新 Android Marshmallow 功能的概述。


## <a name="related-links"></a>相关链接

- [Android 6.0 Marshmallow](https://developer.android.com/about/versions/marshmallow/index.html)
- [获取 Android SDK](https://developer.android.com/sdk/index.html#Other)
- [功能概述](https://developer.android.com/preview/api-overview.html)
- [发行说明](https://developer.xamarin.com/releases/android/xamarin.android_5/xamarin.android_5.1.99/)
- [RuntimePermissions （示例）](https://developer.xamarin.com/samples/monodroid/android-m/RuntimePermissions)
- [ConfirmCredential （示例）](https://developer.xamarin.com/samples/monodroid/android-m/ConfirmCredential)
- [FingerprintDialog （示例）](https://developer.xamarin.com/samples/monodroid/android-m/FingerprintDialog)
