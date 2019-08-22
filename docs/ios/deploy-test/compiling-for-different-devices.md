---
title: 在 Xamarin.iOS 中针对不同的设备进行编译
description: 本文档介绍了可用于针对不同设备自定义 Xamarin.iOS 生成的各种生成配置选项。
ms.prod: xamarin
ms.assetid: 3B259248-887E-3E4F-E09C-7AD28C2A8CEE
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: 1b3bc6874a8584b60b15d1d373ad97fde565dfc4
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/16/2019
ms.locfileid: "69527129"
---
# <a name="compiling-for-different-devices-in-xamarinios"></a>在 Xamarin.iOS 中针对不同的设备进行编译

可以从项目的“iOS 生成”  属性页上配置可执行文件的生成属性，可通过右键单击项目名称并浏览至 Visual Studio for Mac 中的“选项”>“iOS 生成”  查找，或在 Visual Studio 的“属性”  中查找：

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)


[![](compiling-for-different-devices-images/image1.png "“项目 iOS 生成”属性页")](compiling-for-different-devices-images/image1.png#lightbox) 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](compiling-for-different-devices-images/image1a.png "“项目 iOS 生成”属性页")](compiling-for-different-devices-images/image1a.png#lightbox)

-----

除了 UI 上可用的配置选项外，你还可以向 [Xamarin.iOS 生成工具 (mtouch)](~/ios/deploy-test/mtouch.md) 传递自己的命令行选项集。

[http://iossupportmatrix.com/](http://iossupportmatrix.com/) 是一个有用的资源，可用于确保包含所有需要的设备、体系结构和 iOS 版本。

 <a name="SDK_Options" />


## <a name="sdk-options"></a>SDK 选项

Visual Studio for Mac 允许你配置两个与 SDK 相关的重要属性：用于生成软件的 iOS SDK 版本和部署目标（或所需的最低 iOS 版本）。

iOS SDK 版本  选项允许你使用不同版本的 Apple 发布的 SDK，这会将 Xamarin.iOS 定向到在生成过程中应该引用的编译器、链接器和库。 

部署目标  设置用于选择在其上运行应用程序的操作系统所需的最低版本。 这在项目的 Info.plist 文件中设置。 应该选取具有运行应用程序所需的所有 API 的最低版本。

一般情况下，Xamarin.iOS API 会公开最新版本的 SDK 中的所有可用方法，如有必要，我们会提供允许你检测功能在运行时是否可用的方便属性（例如，`UIDevice.UserInterfaceIdiom` 和 `UIDevice.IsMultitaskingSupported` 始终在 Xamarin.iOS 上运行，我们会完成幕后的所有工作）。

 <a name="Linking" />


## <a name="linking"></a>链接

查看[链接器](~/ios/deploy-test/linker.md)上的专用页，了解有关链接器帮助你缩小可执行文件的大小的方法，并了解如何有效地使用这种方法。

 <a name="Code_Generation_Engine" />


## <a name="code-generation-engine"></a>代码生成引擎

从 Xamarin.iOS 4.0 开始，有两个到 Xamarin.iOS 的代码生成后端。 正则 Mono 代码生成引擎和基于 LLVM 优化编译器的引擎。 每个引擎都各有利弊。

通常情况下，在开发过程中，你可能会使用 Mono 代码生成引擎，因为它允许你快速循环访问。 对于发布生成和 AppStore 部署，请切换为 LLVM 代码生成引擎。

LLVM 优化后端引擎会生成比 Mono 引擎更快、更严格的代码，但花费的编译时间较长。

你可以在 Visual Studio for Mac 或 Visual Studio 的 iOS 生成选项中启用它们。

[![](compiling-for-different-devices-images/image2.png "启用 LLVM")](compiling-for-different-devices-images/image2.png#lightbox)

[![](compiling-for-different-devices-images/image2a.png "启用 LLVM")](compiling-for-different-devices-images/image2a.png#lightbox)

 <a name="ARMV7_and_ARMV7s_support" />


## <a name="architecture-support"></a>体系结构支持

<a name="armv6-discontinued" />

### <a name="armv6-xamarinios-discontinued-support-for-armv6-with-v810"></a>ARMv6（Xamarin.iOS 已中断通过 v8.10 对 ARMv6 的支持）

- iPhone（原始），3G
- iPod 1 代、2 代

### <a name="armv7"></a>ARMv7

- iPhone 3GS、4、4S
- iPad 1、2、3、Mini
- iPod 3 代、4 代、5 代

### <a name="armv7s"></a>ARMv7s

- iPhone 5
- iPhone 5c
- iPad 4

如果仅面向 ARMv7s 处理器，生成的代码会快一些，但它不会再在 ARMv7 或 ARMv6 系统上运行，除非在程序包中编译一个包含多个可执行文件的胖二进制。

### <a name="arm64-xamarinios-started-supporting-arm64-in-v86"></a>ARM64（Xamarin.iOS 开始支持 v8.6 中的 ARM64）

- iPhone 5s
- iPhone SE
- iPhone 6、6 Plus
- iPhone 6s、6s Plus
- iPhone 7、7 Plus
- iPhone 8、8 Plus
- iPhone X
- iPad Air
- iPad Air 2
- iPad Mini 2、3、4
- iPad Pro（全部）

注意，提交到 App Store 的任意生成必须包含 64 位支持，这是 [Apple](https://developer.apple.com/news/?id=12172014b) 设置的要求。 此外，iOS 11 仅支持 64 位应用程序。

 <a name="ARM_Thumb_Support" />


### <a name="arm-thumb-2-support"></a>ARM Thumb-2 支持

Thumb 是 ARM 处理器使用的更紧凑的指令集。 通过启用 Thumb 支持，可以缩小可执行文件的大小，但执行时间会变慢。 ARMv7 和 ARMv7s 支持 Thumb。

 <a name="Conditional_framwork_useage" />


## <a name="conditional-framework-usage"></a>条件框架使用情况

如果项目想要利用较新的 iOS 版本中的部分功能，你可能需要有条件地依赖于某些新框架。 最好的例子是想在运行 iOS 4.0 或更高版本时使用 iAd 但仍支持 3.x 设备。 若要完成此操作，需要让 Xamarin.iOS 知道你需要针对 iAd 框架“弱”链接。 弱绑定确保仅在第一次需要框架中的类时，根据需要加载框架。

若要执行此操作，应执行以下步骤：

- 打开“项目选项”  并导航到“iOS 生成”  窗格。
- 将 `'-gcc_flags "-weak_framework iAd"'` 添加到你想弱链接到的每个配置上的其他选项  ：


[![](compiling-for-different-devices-images/image3.png "其他选项")](compiling-for-different-devices-images/image3.png#lightbox)


除此以外，你需要防止使用在可能不存在旧版本 iOS 的位置运行旧版本 iOS 的类型。 有几种方法来实现此目的，其中一种是分析 `UIDevice.CurrentDevice.SystemVersion`。



## <a name="related-links"></a>相关链接

- [链接器](~/ios/deploy-test/linker.md)
- [外部 - iOS 支持矩阵](http://iossupportmatrix.com/)
