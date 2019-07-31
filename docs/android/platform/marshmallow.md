---
title: Marshmallow 功能
description: 本文介绍如何使用 Xamarin 开发适用于 Android 6.0 Marshmallow 的应用。
ms.prod: xamarin
ms.assetid: E4D6F183-98D2-460A-9D65-937639A899E0
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: 4ff0fa177bf28f00182c202ffba770d4be61b3c7
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2019
ms.locfileid: "68643349"
---
# <a name="marshmallow-features"></a>Marshmallow 功能

_本文介绍如何使用 Xamarin 开发适用于 Android 6.0 Marshmallow 的应用。_

本文概述了 Android 6.0 Marshmallow 中的新功能, 介绍了如何为 Android Marshmallow 开发准备 Xamarin, 并提供指向示例应用程序的链接, 这些示例应用程序演示了如何使用新的 Android MarshmallowXamarin Android 应用中的功能。 


## <a name="overview"></a>概述

[Android 6.0 Marshmallow](https://developer.android.com/about/versions/marshmallow/index.html)是 Android 棒糖形后的下一个主要 Android 版本。
Xamarin 支持 Android Marshmallow, 其中包括:

-   **API 23/Android 6.0 绑定**&ndash; Android 6.0 为下面所述的新功能添加了许多新 api; 当目标 api 级别23时, 这些 api 可供 Xamarin Android 应用使用。 有关 Android 6.0 Api 的详细信息, 请参阅[android 6.0 api](https://developer.android.com/preview/api-overview.html)。 

[![运行 Marshmallow 的平板电脑和手机的英雄图像](marshmallow-images/android-m-hero-sml.png)](marshmallow-images/android-m-hero.png#lightbox)

尽管 Marshmallow 版本主要侧重于 "波兰和质量", 但它还提供许多 Xamarin 开发人员感兴趣的新功能。 这些功能包括： 

-   **运行时权限**&ndash;此增强功能使用户能够在运行时基于每个案例批准安全权限。 

-   **身份验证改进**从 Android Marshmallow 开始, 应用现在可以使用指纹传感器来对用户进行身份验证, 而新的*确认凭据*功能最大限度地减少了输入密码的需求。 &ndash; 

-   **应用链接**此功能通过自动将应用与 web 域关联来帮助消除**应用选择器**弹出窗口的必要性。 &ndash; 

-   **直接共享**可以定义*直接共享目标*, 使用户能够快速、直观地共享用户; 此功能允许用户与其他应用共享内容。 &ndash; 

-   **语音交互**&ndash;此新 API 允许您在应用程序中生成对话语音功能。 

-   **4K 显示模式**&ndash;在 Android Marshmallow 中, 你的应用可以在支持4k 显示分辨率的硬件上请求它。 

-   **新增音频功能**&ndash;从 Marshmallow 开始, Android 现在支持 MIDI 协议。 它还提供了用于创建数字音频捕获和播放对象的新类, 并为关联音频和输入设备提供了新的 API 挂钩。 

-   **新视频功能**&ndash; Marshmallow 提供了一个新类, 可帮助应用以同步的顺序呈现音频和视频流; 此类还提供对动态播放速率的支持。 

-   **Android For Work**&ndash; Marshmallow 包括适用于公司拥有的单用户设备的增强控件。 它支持设备所有者无提示安装和卸载应用、自动接受系统更新、改进的证书管理、数据使用跟踪、权限管理和工作状态通知。 

-   **材料设计支持库**新的*设计支持库*提供了设计组件和模式, 使你可以更轻松地在应用程序中构建内容设计外观。 &ndash; 

此外, 许多核心 Android 库更新随 Android M 发布, 这些更新为 Android M 和早期版本的 Android 提供了新功能。

此外, 许多核心 Android 库更新随 Android Marshmallow 发布, 这些更新为 Android Marshmallow 和早期版本的 Android 提供了新功能。 本文介绍如何开始使用 Android Marshmallow 构建应用, 并概述了 Android 6.0 中新增的功能要点。 

## <a name="requirements"></a>要求

若要在基于 Xamarin 的应用中使用新的 Android Marshmallow 功能, 需要满足以下要求: 

-   必须安装和配置**Xamarin** xamarin5.1.7.12或更高版本,并且必须安装VisualStudio或XamarinStudio。&ndash;

-   **Visual Studio for Mac**或**Visual Studio** &ndash;如果使用 Visual Studio for Mac, 则需要5.9.7.22 或更高版本。 如果你使用的是 Visual Studio, 则需要用于 Visual Studio 的 Xamarin tools 版本3.11.1537 或更高版本。 

-   **Android SDK**&ndash;必须通过 Android SDK 管理器安装 Android SDK 6.0 (API 23) 或更高版本。

-   **Java 开发人员工具包**如果你正在开发 for API level 24 或更高版本 (jdk 1.8 还支持早于24的 api 级别, 包括 Marshmallow), 则需要[jdk 1.8](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)或更高版本。 &ndash; 如果使用的是自定义控件或窗体预览器, 则需要64位版本的 JDK 1.8。

如果要专门针对 API 级别23或更早版本进行开发, 可以继续使用[JDK 1.7](https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) 。 


## <a name="getting-started"></a>入门

若要开始使用适用于 Xamarin 的 Android Marshmallow, 必须先下载并安装最新的工具和 SDK 包, 然后才能创建 Android Marshmallow 项目: 

1.  从**稳定**通道安装最新的 Xamarin 更新。 

2.  安装 Android 6.0 Marshmallow SDK 包和工具。

3.  创建面向 Android 6.0 Marshmallow (API 级别 23) 的新的 Xamarin Android 项目。 

4.  为 Android Marshmallow 配置模拟器或设备。

以下各部分对这些步骤进行了说明:


### <a name="install-xamarin-updates"></a>安装 Xamarin 更新

若要更新 Xamarin 以使其包含对 Android 6.0 Marshmallow 的支持, 请将更新通道更改为 "**稳定**" 并安装所有更新。 有关从更新通道安装更新的详细信息, 请参阅[更改更新通道](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/change_updates_channel)。 


### <a name="install-the-android-60-sdk"></a>安装 Android 6.0 SDK

若要创建适用于 Android Marshmallow 的 Xamarin Android 项目, 必须首先使用 Android SDK 管理器安装 Android 6.0 SDK:

-   启动 Android SDK 管理器 (在 Visual Studio for Mac 中, 使用**工具 > SDK 管理器**; 在 Visual Studio 中, 使用**工具 > Android > Android SDK 管理器**) 并安装最新 Android SDK Tools:

    [![在 Android SDK 管理器中选择 Android SDK 工具](marshmallow-images/mnc-preview-tools.png)](marshmallow-images/mnc-preview-tools.png#lightbox)

-   同时, 安装最新的**Android 6.0** SDK 包:

    [![在 Android SDK 管理器中选择 Android 6.0 SDK 包](marshmallow-images/mnc-preview-packages.png)](marshmallow-images/mnc-preview-packages.png#lightbox)

必须安装 Android SDK Tools 修订版24.3.4 或更高版本。
有关使用 Android SDK 管理器安装 Android 6.0 SDK 的详细信息, 请参阅[SDK 管理器](https://developer.android.com/tools/help/sdk-manager.html)。



### <a name="start-a-xamarinandroid-project"></a>启动 Xamarin Android 项目

创建新的 Xamarin Android 项目。 如果你不熟悉通过 Xamarin 进行 Android 开发, 请参阅[Hello, android](~/android/get-started/hello-android/index.md)了解如何创建 android 项目。 

创建 Android 项目时, 必须将版本设置配置为面向 Android 6.0 MarshMallow。 若要面向 Marshmallow 的项目, 必须为**API 级别 23 (Xamarin v2.0 6.0 支持)** 配置项目。 有关配置 Android API 级别级别的详细信息, 请参阅[了解 ANDROID Api 级别](~/android/app-fundamentals/android-api-levels.md)。



### <a name="configure-an-emulator-or-device"></a>配置仿真程序或设备

如果使用的是仿真程序, 请使用以下设置启动 Android AVD Manager 并创建新设备:

-   设备:结点5、6或9。
-   目标:Android 6.0-API 级别23
-   ABI: x86

例如, 此虚拟设备配置为模拟结点 5:

[![使用 "结点 5" 设备、Android 6.0 目标和 Intel Atom (x86) 配置 AVD](marshmallow-images/android-m-avd.png)](marshmallow-images/android-m-avd.png#lightbox)

如果使用的是物理设备, 如结点5、6或 9, 则可以安装 Android Marshmallow 的预览图像。 有关将设备更新到 Android Marshmallow 的详细信息, 请参阅[硬件系统映像](https://developer.android.com/preview/download.html#images)。



## <a name="new-features"></a>新增功能

Android Marshmallow 中引入的许多更改都侧重于改进 Android 用户体验、提高性能并修复 bug。 不过, Marshmallow 还引入了对 Android 平台基础知识的一些广泛更改。 以下各节重点介绍了这些增强功能, 并提供了一些链接, 可帮助你开始在应用中使用新的 Android Marshmallow 功能。 



### <a name="runtime-permissions"></a>运行时权限

自 Android 棒糖形后, Android 权限系统经过了大幅优化和简化。 在 Android Marshmallow 中, 用户在运行时 (而不是在安装时) 根据具体情况授予权限。 若要在 Android Marshmallow 和更高版本上支持此功能, 请将应用程序设计为在运行时提示用户提供权限 (在需要权限的上下文中)。 此更改使用户能够更轻松地开始使用你的应用程序, 因为它简化了安装和升级应用程序的过程。 

若要详细了解如何在 Xamarin Android 应用程序中实现运行时权限, 请参阅[在 Android Marshmallow 中请求运行时权限](https://blog.xamarin.com/requesting-runtime-permissions-in-android-marshmallow/)。
Xamarin 还提供了一个示例应用, 阐释了运行时权限在 Android Marshmallow 中的工作方式 (及更高版本):[RuntimePermissions](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-m-runtimepermissions)。

此示例应用程序演示了以下内容:

-   如何在运行时检查并请求权限。
-   如何声明 Android M 设备的权限。

使用此示例应用:

1.  点击 "**照相机**" 或 "**联系人**" 按钮, 显示 "权限请求" 对话框。
2.  授予查看照相机或联系人片段的权限。

有关 Android Marshmallow 中的新运行时权限功能的详细信息, 请参阅使用[系统权限](https://developer.android.com/preview/features/runtime-permissions.html)。



### <a name="authentication-enhancements"></a>身份验证增强功能

Android Marshmallow 包括两个可帮助消除密码需要的身份验证增强功能:

-   **指纹身份验证**&ndash;使用指纹扫描来对用户进行身份验证。

-   **确认凭据**&ndash;根据设备解锁的时间, 对用户进行身份验证。

接下来介绍的链接和示例应用可帮助你熟悉这些新功能。


#### <a name="fingerprint-authentication"></a>指纹身份验证

在支持指纹扫描硬件的设备上, 你可以使用新`FingerPrintManager`的类来对用户进行身份验证。
有关 Android Marshmallow 中指纹身份验证功能的详细信息, 请参阅[指纹身份验证](https://developer.android.com/preview/api-overview.html#fingerprint-authentication)。

Xamarin 提供了一个示例应用, 演示如何使用注册的指纹在应用中对用户进行身份验证:[FingerprintDialog](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-m-fingerprintdialog)。

使用此示例应用:

1.  触摸 "**购买**" 按钮以打开指纹身份验证对话框。
2.  扫描已注册的指纹进行身份验证。

请注意, 此示例应用要求设备带有指纹读取器。
此应用不存储指纹 (或密码)。



#### <a name="voice-interactions"></a>语音交互

Android Marshmallow 中引入了新的语音交互功能, 使应用程序的用户可以使用其语音确认操作并从选项列表中进行选择。 有关语音交互的详细信息, 请参阅[语音交互 API 概述](https://developers.google.com/voice-actions/interaction/)。 

有关在 Xamarin Android 应用程序中实现语音交互的详细信息 (包括代码示例), 请参阅[使用语音交互向 Android 应用添加对话](https://blog.xamarin.com/add-a-conversation-to-your-android-app-with-voice-interactions/)。
提供了一个示例应用程序, 说明如何在 Xamarin Android 应用程序中使用语音交互 API:[语音交互](https://github.com/jamesmontemagno/MarshmallowSamples/tree/master/VoiceInteractions)。



#### <a name="confirm-credential"></a>确认凭据

使用 Android Marshmallow 的新*确认凭据*功能, 你可以让用户无需记住并输入特定于应用程序的密码, 方法是根据设备解锁的时间长度进行身份验证。
为此, 请使用的新`SetUserAuthenticationValidityDurationSeconds`方法。 `KeyGenerator` `KeyGuardManager`使用的`CreateConfirmDeviceCredentialIntent`方法从应用程序中重新对用户进行身份验证。 有关 Android Marshmallow 中的这一新功能的详细信息, 请参阅[确认凭据](https://developer.android.com/preview/api-overview.html#confirm-credential)。

Xamarin 提供了一个示例应用, 演示如何在应用中使用设备凭据 (如 PIN、模式或密码):[ConfirmCredential](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-m-confirmcredential)

使用此示例应用:

1.  在设备上设置安全的锁定屏幕 (**安全 > 安全 > Screenlock**)。
2.  点击 "**购买**" 按钮, 确认安全锁定屏幕凭据。



### <a name="chrome-custom-tabs"></a>Chrome 自定义选项卡

当用户点击某个 URL 时, 应用开发人员可以选择: 应用可以启动浏览器, 也可以使用基于的`WebView`应用内浏览器。 这两个选项&ndash;在启动浏览器时都面临着很大的难题, `WebView`这是一个不能自定义的繁重上下文交换机, 而不会与浏览器共享状态 此外, 使用可以`WebView`增加额外的维护开销。 

*Chrome 自定义选项卡*使你能够轻松、完美地地显示具有 Chrome 功能的网站, 而无需让用户离开你的应用程序。 此功能可让你的应用更好地控制用户的 web 体验;它使本机和 web 内容之间的转换更加无缝, 无需使用`WebView`。 您的应用程序还可以通过自定义以下内容, 影响 Chrome 的外观和感觉: 

-   工具栏颜色

-   进入和退出动画

-   Chrome 工具栏和溢出菜单中的自定义操作

-   Chrome 预启动和内容预提取 (用于更快的加载)

若要在 Xamarin Android 应用中利用此功能, 请下载并安装[Android 支持自定义选项卡库](https://www.nuget.org/packages/Xamarin.Android.Support.CustomTabs/)。
有关此功能的详细信息, 请参阅[Chrome 自定义选项卡](https://developer.chrome.com/multidevice/android/customtabs)。



### <a name="material-design-support-library"></a>材料设计支持库

Android 棒糖[形引入了](http://www.google.com/design/spec/material-design/introduction.html)一种新的设计语言, 可用于刷新 android 体验 (有关在 Xamarin 应用中使用材料设计的信息, 请参阅[材料主题](~/android/user-interface/material-theme.md))。 通过 Android Marshmallow, Google 引入了*Android 设计支持库*, 使应用程序开发人员可以更轻松地采用实质性设计的外观。 此库包含以下组件:

-   **CoordinatorLayout**新小组件类似于, 但其`FrameLayout`功能更强大。 `CoordinatorLayout` &ndash; 您可以将`CoordinatorLayout`用作子视图的容器或用作顶级布局, 并且它`layout_anchor`提供可用于相对于其他视图定位视图的属性。

-   **折叠工具栏**新的是一个折叠的应用程序栏`Toolbar`, 它是的包装。 `CollapsingToolbarLayout` &ndash; (请注意,*应用程序栏*是以前称为*操作栏*的。)

-   **浮动操作按钮**&ndash;用于在应用界面上表示主要操作的圆形按钮。

-   **用于编辑文本的浮动标签**&ndash;使用`EditText`新`TextInputLayout`小组件 (换行) 在用户输入文本时隐藏提示时显示浮动标签。

-   **导航视图**&ndash; 新`NavigationView`小组件可帮助你使用导航抽屉, 使用户更容易导航。

-   **Snackbar**&ndash; 新`SnackBar`小组件是一种轻型反馈机制 (类似于 toast), 它在屏幕底部显示一条简短消息, 并显示在屏幕底部的所有其他元素之上。

-   **材料选项卡**&ndash; 新`TabLayout`小组件提供用于显示选项卡的水平布局, 作为在应用中实现顶层导航的方式。

若要利用 Xamarin Android 应用中的[设计支持库](https://developer.android.com/tools/support-library/features.html#design), 请下载并安装 Xamarin [Xamarin 支持库设计](https://www.nuget.org/packages/Xamarin.Android.Support.Design/)NuGet 包。

有关使用 Xamarin Android 应用中的材料设计支持库的更多详细信息 (包括代码示例), 请参阅[Android 支持设计库中的精美材料设计](https://blog.xamarin.com/add-beautiful-material-design-with-the-android-support-design-library/)。
Xamarin 提供了一个示例应用, 演示了 Xamarin &ndash; [Cheesesquare](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android50-cheesesquare)上的新 Android 设计库。
此示例演示设计库的以下功能:


-   折叠工具栏
-   浮动操作按钮
-   查看定位
-   NavigationView
-   Snackbar

有关设计库的详细信息, 请参阅 Android 开发人员博客中的[Android 设计支持库](http://android-developers.blogspot.co.at/2015/05/android-design-support-library.html)。


### <a name="additional-library-updates"></a>其他库更新

除了 Android Marshmallow, Google 还宣布了对几个核心 Android 库的相关更新。 Xamarin 通过多个预览版 NuGet 包为这些更新提供 Xamarin 支持: 

-   [Google Play Services](https://www.nuget.org/packages?q=Xamarin+Google+Play+Services)最新版本的 Google Play Services 包括新的*应用程序邀请*功能, 使用户可以与朋友共享其应用程序。 &ndash; 有关此功能的详细信息, 请参阅[扩展应用与 Google 的应用邀请的访问](https://blog.xamarin.com/expand-your-apps-reach-with-googles-app-invites/)。 

-   [Android 支持库](https://www.nuget.org/packages?q=xamarin+support+library)&ndash;这些 nuget 提供了仅适用于库 api 的功能, 同时提供了向后兼容版本的 Android framework api。 

-   [Android 可穿戴库](https://www.nuget.org/packages/Xamarin.Android.Wear)&ndash;此 NuGet 包含 Google Play Services 绑定。 最新版本的可穿戴库将新功能 (包括更简单的自定义应用导航) 引入 Android 磨损平台。 


## <a name="summary"></a>总结

本文介绍了 Android Marshmallow, 并介绍了如何在 Marshmallow 上安装和配置适用于 Xamarin 开发的最新工具和包。 它还提供了适用于 Xamarin 开发的最令人兴奋的新 Android Marshmallow 功能的概述。


## <a name="related-links"></a>相关链接

- [Android 6.0 Marshmallow](https://developer.android.com/about/versions/marshmallow/index.html)
- [获取 Android SDK](https://developer.android.com/sdk/index.html#Other)
- [功能概述](https://developer.android.com/preview/api-overview.html)
- [发行说明](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/android/xamarin.android_5/xamarin.android_5.1.99/index.md)
- [RuntimePermissions (示例)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-m-runtimepermissions)
- [ConfirmCredential (示例)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-m-confirmcredential)
- [FingerprintDialog (示例)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-m-fingerprintdialog)
