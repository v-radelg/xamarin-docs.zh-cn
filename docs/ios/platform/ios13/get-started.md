---
title: IOS 13、iPadOS 13、tvOS 13 和 watchOS 6 入门
description: 本文档介绍如何设置，以通过 Xamarin 构建 iOS 13、iPadOS 13、tvOS 13 和 watchOS 6 应用。 它讨论了如何下载 Xcode 11 和 update Visual Studio for Mac。
ms.prod: xamarin
ms.assetid: 97414545-85D2-433C-9246-63B6763F488A
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 07/01/2019
ms.openlocfilehash: 1f190ef2f99e71c8cf21680f9902caccf3454450
ms.sourcegitcommit: 09bc69d7119a04684c9e804c5cb113b8b1bb7dfc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2019
ms.locfileid: "71206320"
---
# <a name="get-started-with-ios-13"></a>IOS 13 入门

本文档介绍如何开始生成 Xamarin 应用，以便调用 Xcode 11 发布的 Api （适用于 iOS 13）。 使用预览版需要 macOS 10.14.4 （Mojave）或更高版本。

## <a name="download-and-install"></a>下载并安装

1. **安装 Xcode 11** -注册的 Apple 开发人员可以从[Apple 开发人员门户](https://developer.apple.com/download/)或**应用商店**下载并安装最新版本的 Xcode 11。

2. **运行 Xcode 11** –在更新和运行 Visual Studio for Mac 之前运行 Xcode 11，因为它安装了 Xamarin 所需的一些工具。

3. 在 Visual Studio for Mac 中，选择 " **Visual Studio > 检查更新 ...** "，选择**稳定**通道，然后安装可用更新。

4. 可有可无**在 ios 设备上安装 ios 13** -对于使用 Xcode 11 引入的 api 的应用的设备测试，已注册的 Apple 开发人员可以在其设备上[下载](https://developer.apple.com/download)并安装操作系统。 

   > [!TIP]
   > 即使你的应用程序不使用任何新的 Api，也请务必使用最新的 Xcode 11 Sdk 生成它，并对其进行测试以确保一切按预期运行。 如果应用程序不调用任何新的 Api，你可以使用这些新的 Sdk 重新编译该应用程序，并在尚未升级到新操作系统的设备上对其进行测试。
   >
   > 在将设备升级到 Apple 的最新操作系统版本之前，请务必执行以下操作：
   >
   > - 阅读[Apple 发行说明](https://developer.apple.com/download/)，了解操作系统更新。

## <a name="related-links"></a>相关链接

- [下载 Xcode](https://developer.apple.com/download/)
- [Xamarin.iOS 发行说明](/xamarin/ios/release-notes/13/13.0)
