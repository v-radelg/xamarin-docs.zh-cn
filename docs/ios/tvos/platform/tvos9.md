---
title: tvOS 9 简介
description: 本文介绍适用于 Xamarin.tvOS 开发人员的所有新的和修改 Api 和 tvOS 9 中的可用功能。
ms.prod: xamarin
ms.assetid: A7E738E1-9F94-489B-918F-7DF8F0810987
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 06/07/2016
ms.openlocfilehash: dda197f71b2a2ab3e0d61a838ab85d79b7a078c7
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61270317"
---
# <a name="introduction-to-tvos-9"></a>tvOS 9 简介

_本文介绍适用于 Xamarin.tvOS 开发人员的所有新的和修改 Api 和 tvOS 9 中的可用功能。_

Apple 已发布 Apple TV 硬件特点是经过重新设计，触摸启用远程，运行新的 （基于 iOS 9） 的 tvOS 操作系统的第四代。

第一次，tvOS 打开 Apple TV 平台为开发人员，您可以创建丰富的沉浸式应用程序然后将它们发布通过 Apple tv 设备内置 App Store 中的过程类似于编写和发布适用于使用 iTunes 应用在 iOS 应用程序的体验存储区。

如果您熟悉 Xamarin.iOS 开发，您会发现转换到 tvOS 相当简单。 Api 和功能的大部分都是相同，但是，许多常见的 Api （例如，WebKit) 不可用。 此外，使用与 Siri 远程会带来基于触摸屏的 iOS 设备中不存在的一些设计难题。

本指南将为 Xamarin.tvOS 开发人员提供的所有新的和修改 Api 和 tvOS 9 中的可用功能的简介。 TvOS 的详细信息，请参阅 Apple[开发的新 Apple TV](https://developer.apple.com/tvos/)文档。

<a name="Supported-and-Unsupported-Capabilities" />

## <a name="supported-and-unsupported-capabilities"></a>支持和不支持的功能

运行在 Apple TV 的 tvOS 应用具有支持以下功能和特性：

 - 应用组
 - 后台模式
 - 数据保护
 - Game Center
 - 游戏控制器
 - iCloud
 - 应用内购买
 - Keychain 共享

不支持以下功能和性能：

 - Apple Pay
 - 应用沙盒
 - 关联的域
 - HealthKit
 - HomeKit
 - 应用间音频
 - 映射
 - 个人 VPN
 - 推送通知
 - Wallet
 - 无线附件配置

请参阅我们[支持程序集](~/ios/tvos/internals/assemblies.md)并[支持框架](~/ios/tvos/internals/frameworks.md)文档了解详细信息。

<a name="Apple-TV-Hardware" />

## <a name="apple-tv-hardware"></a>Apple TV 硬件

新建 Apple TV 具有以下硬件规范：

 - 64 位 A8 处理器
 - 32 GB 或 64 GB 的存储空间
 - 2 GB 的 RAM
 - 10/100Mbps Ethernet
 - WiFi 802.11a/b/g/n/ac
 - 分辨率为 1080p
 - HDMI
 - USB C 端口 （适用于开发人员和仅供诊断使用）
 - 新 Siri 远程或 Apple TV 远程 （基于区域）

### <a name="siri-remote"></a>Siri 远程

根据区域，提供 Apple TV 远程将有两个配置：Siri 远程或 Apple TV 远程。

Siri 远程目前已在以下国家/地区：

 - 澳大利亚
 - 加拿大
 - 法国
 - 德国
 - 日本
 - 西班牙
 - 英国
 - 美国

所有其他国家/地区，将收到的 Apple TV Remote 替换 Siri 按钮将显示与搜索文本输入的默认搜索屏幕的搜索按钮：

[![](tvos9-images/remote02.png "Siri 远程")](tvos9-images/remote02.png#lightbox)

有关详细信息，请参阅我们[Siri 远程和蓝牙控制器](~/ios/tvos/platform/remote-bluetooth.md)文档。

<a name="Apple-TV-Provisioning" />

## <a name="apple-tv-provisioning"></a>Apple TV 预配

就像开发 iOS，新的 tvOS 将用于开发和分发基于团队的成员身份和签名标识的已建立与 Apple 要求正确预配配置文件。

正确预配，还必须访问 tvOS 功能，如 iCloud KVS 或 CloudKit 数据存储。 请参阅我们[资源和数据存储](~/ios/tvos/app-fundamentals/resources-data-storage.md)iCloud 支持 Xamarin.tvOS 应用程序中的信息。

创建和安装一样使用 xamarin ios 应用预配配置文件。 在这种情况下，请参阅我们的 iOS[设备预配](~/ios/get-started/installation/device-provisioning/index.md)文档了解详细信息。

<a name="Apple-TV-Apps" />

## <a name="apple-tv-apps"></a>Apple TV 应用

新建 Apple TV 硬件和 tvOS 9 支持两种类型的应用： 传统和客户端-服务器应用程序。

<a name="Traditional-Apps" />

### <a name="traditional-apps"></a>传统的应用程序

传统的应用程序从 Apple TV App Store 购买，并且直接在设备上安装。 这些应用可以是游戏、 实用程序或使用相同的框架和技术为 Xamarin.iOS 应用开发的媒体应用程序。

Apple TV 应用具有的最大大小为 200 MB，可以下载额外的 2 GB 使用按需资源的内容。 请参阅我们[资源和数据存储](~/ios/tvos/app-fundamentals/resources-data-storage.md)有关详细信息。

请参阅我们[你好，tvOS 快速入门指南](~/ios/tvos/get-started/hello-tvos.md)您熟悉的工具和开发使用 Xamarin.tvOS tvOS 应用所需的概念。

<a name="Summary" />

### <a name="client-server-apps"></a>客户端-服务器应用程序

除了已安装的传统应用程序，Apple TV 可以轻松地创建基于 web 的客户端-服务器媒体流式处理应用程序使用 web 技术 （HTTPS、 XML 和 JavaScript）。 你将设计用户界面使用 Apple 的 TVML 标记语言和 JavaScript 用于定义使用 TVMLKit 的应用程序的行为。

有关详细信息，请参阅 Apple [Apple TV 标记语言参考](https://developer.apple.com/library/prerelease/tvos/documentation/LanguagesUtilities/Conceptual/ATV_Template_Guide/index.html#//apple_ref/doc/uid/TP40015064)， [TVJS 框架引用](https://developer.apple.com/library/prerelease/tvos/documentation/TVMLJS/Reference/TVJSFrameworkReference/index.html#//apple_ref/doc/uid/TP40016076)， [TVMLKit 框架引用](https://developer.apple.com/library/prerelease/tvos/documentation/TVMLKit/Reference/TVMLKit_Collection/index.html#//apple_ref/doc/uid/TP40016429)，[有关HTTP 实时流式处理](https://developer.apple.com/library/prerelease/tvos/referencelibrary/GettingStarted/AboutHTTPLiveStreaming/about/about.html#//apple_ref/doc/uid/TP40013978)并[HLS Apple TV 的创作规范](https://developer.apple.com/services-account/download?path=/Documentation/HLS_Authoring_Specification_for_Apple_TV/HLS_Authoring_Specification_for_Apple_TV.pdf)文档。

<a name="User-Interface-Challenges" />

## <a name="user-interface-challenges"></a>用户界面问题

与 iOS 或 OS X、 Apple TV 没有触摸屏或鼠标，允许用户直接选择并应用或其内容与之交互。 而是在用户导航应用程序的用户界面的新的 Siri 远程或蓝牙游戏控制器。 有关详细信息，请参阅我们[Siri 远程和蓝牙控制器](~/ios/tvos/platform/remote-bluetooth.md)文档。

此外，整体用户体验是与 iOS 或 Mac 应用程序往往是单个用户体验大大不同。 使用 Apple TV 中，用户体验往往是更多社交在本质上，其中多个用户可能同样的躺椅上与单个应用程序和彼此进行交互。 若要设计成功 Apple TV 应用体验 （新的应用程序或更改某个现有），这些更改必须纳入考虑范围。 

<a name="Summary" />

### <a name="working-with-focus-and-parallax-images"></a>使用焦点和视差图像

如上面所述，使用它的接口直接作为 ios 其中用户点击设备的屏幕上的映像但间接从使用 Siri 远程在室内不进行交互 Xamarin.tvOS 应用程序的用户。 若要显示和处理这种用户交互，Apple TV 使用焦点基于模型。 

焦点发生变动，作为使用精致的动画效果和副作用 （例如映像上视差效果） 用于明确识别当前具有焦点的用户界面项。

如果用户使用 Siri 远程上进行速度较慢且循环手势，已设定焦点的项将 sway 实时响应此移动。 Sway 出现时，照明的光泽应用于其图像进行似乎出类拔萃的图面。 给定的一段非活动状态后, 变暗聚焦不准的任何内容和焦点项将变得更大。

有关详细信息，请参阅我们[使用导航和焦点](~/ios/tvos/app-fundamentals/navigation-focus.md)并[使用的图标和图像](~/ios/tvos/app-fundamentals/icons-images.md)文档。

<a name="The-Home-Screen" />

### <a name="the-home-screen"></a>主屏幕

主页 Apple 电视屏幕显示所有应用的安装，并提供用于访问用户首选项的方法：

[![](tvos9-images/home01.png "主屏幕")](tvos9-images/home01.png#lightbox)

用户导航的上使用 Siri 远程使用焦点中选择一个应用并启动它在使用触摸手势的应用图标的网格。 应用图标是您的第一个机会，使在潜在用户上良好印象和应传达一目了然的应用程序的用途。

每个应用程序必须提供小型和大型版本的应用程序图标。 安装应用后，将 Apple 电视主页屏幕上使用的小图标。 最大版本可供 App Store。 大型应用图标应模拟的小图标版本的外观。

有关详细信息，请参阅我们[使用的图标和图像](~/ios/tvos/app-fundamentals/icons-images.md)文档。

<a name="The-Top-Shelf" />

### <a name="the-top-shelf"></a>顶层架子

如果用户具有 Apple 电视主页屏幕上的顶部行上放置 Xamarin.tvOS 应用，则当用户选择您的应用程序时将显示大型的顶架图像。 此映像应突出显示您的应用程序的功能，或提供指向其内容的直接链接。

[![](tvos9-images/topshelf01.png "顶层架子")](tvos9-images/topshelf01.png#lightbox)

可以为单个静态提供顶架图像`.png`或`.lsr`文件，也可以动态创建在运行时作为可获得焦点项的单个行。

而不是显示静态的顶架图像，它可以包含一组动态的滚动横幅或动态行或可获得焦点的项。 这两个这些动态样式，可以突出显示应用程序或跳转到其最常用功能提供的内容。

有关详细信息，请参阅我们[使用图标和图像](~/ios/tvos/app-fundamentals/icons-images.md)文档和 Apple [TVServices 框架引用](https://developer.apple.com/library/prerelease/tvos/documentation/TVServices/Reference/TVServices_Ref/index.html#//apple_ref/doc/uid/TP40016412)将 Top Shelf 扩展添加到您的应用程序提供的详细信息顶层的动态内容。






## <a name="related-links"></a>相关链接

- [tvOS 示例](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人机接口指南](https://developer.apple.com/tvos/human-interface-guidelines/)
- [适用于 tvOS 应用编程指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
- [构建适用于使用 Xamarin （视频） 的 tvOS 应用](https://university.xamarin.com/lightninglectures/tvos-with-xamarin)
