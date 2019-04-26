---
title: 32/64 位平台注意事项
description: 本文档介绍面向 Xamarin.iOS 或 Xamarin.Mac 应用程序的 32 位和 64 位体系结构时，需要注意的各种注意事项。
ms.prod: xamarin
ms.assetid: F7126340-04B2-4A10-B14D-394E23527C1A
author: conceptdev
ms.author: crdun
ms.date: 03/29/2017
ms.openlocfilehash: 31eb0bfae58ecdca40548e46d1d9d95828be67b4
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61347809"
---
# <a name="3264-bit-platform-considerations"></a>32/64 位平台注意事项

虽然从历史上看，iOS 和 macOS 具有支持 32 位和 64 位应用程序，Apple 已逐渐弃用 32 位支持。

截至 iOS 11 中，将无法再启动 32 位应用程序，并[所有提交到 App Store 必须都支持 64 位](https://developer.apple.com/news/?id=06282017b)。

从 2018 年 1 月开始[到 Mac App Store 提交新应用程序必须支持 64 位](https://developer.apple.com/news/?id=06282017a)，并且必须按 2018 年 6 月更新现有应用程序。

Xamarin 的经典 API (`XamMac.dll`和`monotouch.dll`) 支持仅 32 位应用程序。 但是，使用新的 Xamarin.iOS 和 Xamarin.Mac 应用程序[统一 API](~/cross-platform/macios/unified/index.md) (`Xamarin.iOS`和`Xamarin.Mac`) 默认情况下，因此可以 32 位和 64 位，根据需要的目标。

## <a name="ios"></a>iOS

<a name="enable-64" />

### <a name="enabling-64-bit-builds-of-xamarinios-apps"></a>启用 64 位版本的 xamarin ios 应用

> [!WARNING]
> 出于历史原因，并帮助将较旧的 Xamarin.iOS 项目移至统一 API 和支持 64 位本部分包含。 所有新的 Xamarin.iOS 项目将默认使用统一 API 和面向 64 位。

对于 Xamarin.iOS 移动应用程序已转换为 Unified API，开发人员必须手动更新到面向 64 位的生成设置：

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

1. 在中**Solution Pad**，双击应用程序的项目以打开**项目选项**窗口。
2. 选择**iOS 生成**。
3. 适用于 iPhone 模拟器中，在**支持的体系结构**下拉列表中，选择**x86\_64**或**i386 + x86\_64**:

   [![将受支持的体系结构设置为 x86\_64 或 i386 + x86\_64](Images/Image01.png "Setting Supported architectures to x86\_64 or i386 + x86\_64")](Images/Image01-large.png#lightbox) 

4. 对于物理设备，选择一个可用**ARM64**组合：

   [![将受支持的体系结构设置为一个 ARM64 组合](Images/Image02.png "设置支持 ARM64 组合之一的体系结构")](Images/Image02-large.png#lightbox)

5. 单击 **“确定”**。
6. 执行干净的生成。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. 在中**解决方案资源管理器**，右键单击应用的项目，然后选择**属性**。
2. 选择**iOS 生成**。
3. 对于 iPhone 模拟器中，设置**支持的体系结构**至任一**x86\_64**或**i386 + x86\_64**: 

   [![将受支持的体系结构设置为 x86_64 或 i386 + x86\_64](Images/VS02.png "Setting Supported architectures to x86_64 or i386 + x86\_64")](Images/VS02-large.png#lightbox)

4. 对于物理设备，选择一个可用**ARM64**组合：
    
   [![将受支持的体系结构设置为一个 ARM64 组合](Images/VS01.png "设置支持 ARM64 组合之一的体系结构")](Images/VS01-large.png#lightbox)

5. 保存更改。
6. 执行干净的生成。

-----

ARMv7s 仅受 iPhone 5 （或更高版本） 中包含的 A6 处理器。 ARMv7 代码更快且小于 ARMv6、 仅适用于 iPhone 3GS 和更高版本，和时所需的 Apple iPad 或 5.0 的最低 iOS 版本作为目标。 ARMv6 适用于所有设备，但不再支持由编译器提供 Xcode 4.5 和更高版本。 

支持 iOS 8 iPhone 6 或其他 64 位设备上所需支持 ARM64 和提交新的或更新现有 iTunes 应用商店中的应用程序时所需的 Apple。

有关各种 iOS 设备的功能的全面信息，请参阅 Apple[设备兼容性](https://developer.apple.com/library/content/documentation/DeviceInformation/Reference/iOSDeviceCompatibility/DeviceCompatibilityMatrix/DeviceCompatibilityMatrix.html)文档。

### <a name="64-bit-and-binary-size-increases"></a>64 位和二进制大小增长

从 32 位到 64 位 iOS Apple 的转换期间应用将需要在 32 位和 64 位硬件上运行。 正因为如此，Xamarin 的统一 API 允许开发人员可以同时为目标。

面向 32 位和 64 位体系结构会显著增加应用程序的大小。 但是，因此这样做将允许较新的设备运行优化的代码，同时仍可支持较旧的设备。

> [!IMPORTANT]
> 如果提交到 iTunes 应用商店的 iOS 应用程序时收到以下消息 _"警告 ITMS 9000:缺少的 64 位支持。从 2015 年 2 月 1 日，新的 iOS 上传到应用商店的应用必须包含 64 位支持和 8 包含 Xcode 6 或更高版本的 SDK 通过 iOS 构建。若要启用 64 在项目中，我们建议使用 Xcode 生成的"标准体系结构"设置默认值生成单个二进制文件使用 32 位和 64 位代码。"_ 您需要切换到一个支持的体系结构**ARM64**组合 （如上所示）、 重新编译和重新提交。

## <a name="mac"></a>Mac

> [!IMPORTANT]
> 从 2018 年 1 月开始，到 Mac App Store 提交的所有新 Mac 应用程序必须支持 64 位。 现有 Mac 应用商店应用和他们的更新必须支持 64 位从 2018 年 6 月开始。 请参阅[Apple 的公告](https://developer.apple.com/news/?id=06282017a)并[介绍了如何更新到 64 位的 Xamarin.Mac 应用的指南](~/cross-platform/macios/32-and-64/mac-64-bit.md)。

大多数现代的 Mac 计算机支持 32 位和 64 位应用程序。   MacOS 10.6 (Snow Leopard) 是要在 32 位系统上运行的最后一个操作系统。   发布后 2010年支持这两个系统的大多数 Mac。

与 iOS 不同，许多最新版本的 macOS 中引入的新框架仅支持在 64 位模式下 （CloudKit、 EventKit、 GameController、 LocalAuthentication、 MediaLibrary、 MultipeerConnectivity、 NotificationCenter、 GLKit、 SpriteKit、 社会、和 MapKit，等等）。

Unified API 允许开发人员能够选择他们想要生成哪种应用程序：32 位或 64 位。

**32 位应用程序**将在 32 位和 64 位 Mac 计算机上运行的地址空间限制为 32 位，并要求所有库都是 32 位。

通常将使用此模式下，如果您有不在 64 位模式下运行的 32 位依赖项，如果你想要具有更小的下载，或者如果在将迁移到 64 位不有任何性能优势。

因为您将不能够使用 macOS Mavericks 和 macOS Yosemite 中提供的许多框架限制了此模式。

**64 位应用程序**将仅在 64 位 Mac 设备上运行。

对于 Mac，这是操作的首选的模式中使用的大多数 Mac 现在支持 64 位模式下，并拥有 Apple 提供的框架的完整集访问权限。

### <a name="enabling-64-bit-builds-of-xamarinmac-apps"></a>启用 64 位版本的 Xamarin.Mac 应用

有关生成使用 Xamarin.Mac 的 64 位应用程序的信息，请参阅[更新 Xamarin.Mac 统一应用程序迁移至 64 位](~/cross-platform/macios/32-and-64/mac-64-bit.md)指南。

## <a name="related-links"></a>相关链接

- [经典 vs 统一的 API 差异](https://developer.xamarin.com/releases/ios/api_changes/classic-vs-unified-8.6.0/)
