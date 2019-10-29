---
title: IOS 12、tvOS 12 和 watchOS 5 入门
description: 本文档介绍如何设置以构建具有 Xamarin 的 iOS 12、tvOS 12 和 watchOS 5 应用。 本文介绍了如何下载 Xcode 10 和更新 Visual Studio for Mac 和 Visual Studio 2017。
ms.prod: xamarin
ms.assetid: 6C0F0133-1A5F-408B-8BCA-BDCA313A55C2
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 09/19/2018
ms.openlocfilehash: 1db249a9e07f178461bcb052508d08f54ecea121
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73032016"
---
# <a name="get-started-with-ios-12-tvos-12-and-watchos-5"></a>IOS 12、tvOS 12 和 watchOS 5 入门

本文档介绍如何开始生成 Xamarin 应用，以便调用通过 Xcode 10 发布的 Api，适用于 iOS 12、tvOS 12 和 watchOS 5。

## <a name="download-and-install"></a>下载并安装

1. **安装 Xcode 10** –注册的 Apple 开发人员可以从[Apple 开发人员门户](https://developer.apple.com/download/)或**应用商店**下载并安装最新版本的 Xcode 10。

2. **运行 Xcode 10** –在更新和运行 Visual Studio for Mac 或 Visual Studio 2017 之前运行 Xcode 10，因为它安装了 Xamarin 所需的一些工具。

3. **更新 Visual Studio for Mac 和 Visual Studio 2017** –确保具有最新稳定版本的 Xamarin。

4. _（可选）_ **在 ios 设备上安装 ios 12** –

   对于使用 Xcode 10 引入的 Api 的应用的设备测试，已注册的 Apple 开发人员可以在其设备上[下载](https://developer.apple.com/download)并安装操作系统。

   > [!TIP]
   > 即使你的应用程序不使用任何新的 Api，也请务必使用最新的 Xcode 10 Sdk 生成它，并对其进行测试以确保一切按预期运行。 如果应用程序不调用任何新的 Api，你可以使用这些新的 Sdk 重新编译该应用程序，并在尚未升级到新操作系统的设备上对其进行测试。
   >
   > 在将设备升级到 Apple 的最新操作系统版本之前，请务必执行以下操作：
   >
   > - 阅读[Apple 发行说明](https://developer.apple.com/download/)，了解操作系统更新。
   > - 阅读 Xamarin 预览[版本博客文章](https://releases.xamarin.com/preview-release-xcode-10-beta-6/)。

## <a name="related-links"></a>相关链接

- [下载 Xcode](https://developer.apple.com/download/)
