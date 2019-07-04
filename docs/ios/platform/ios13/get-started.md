---
title: 开始使用 iOS 13、 iPadOS 13、 13、 tvOS 和 watchOS 6
description: 本文档介绍如何获取最多生成 iOS 13、 iPadOS 13、 13、 tvOS 和 watchOS 6 使用 Xamarin 的应用设置。 它讨论了如何下载 Xcode 11 和更新 Visual Studio for mac。
ms.prod: xamarin
ms.assetid: 97414545-85D2-433C-9246-63B6763F488A
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 07/01/2019
ms.openlocfilehash: 554cddf5717401f912cab38c78a6af17659a0cf7
ms.sourcegitcommit: 8ecfa339d0f3e7687977bfe4fc96448942690183
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/03/2019
ms.locfileid: "67558686"
---
# <a name="get-started-with-ios-13"></a>IOS 13 入门

![预览版功能](~/media/shared/preview.png)

本文档介绍如何开始构建适用于 iOS 13 调用与 Xcode 11 发布的 Api 的 Xamarin 应用。 使用预览版需要 macOS 10.14.4 (Mojave) 或更高版本。

## <a name="download-and-install"></a>下载并安装

1. **安装 Xcode 11 beta** -注册 Apple 开发人员可以下载并安装最新版本的 Xcode 11 测试版，从[Apple 开发人员门户](https://developer.apple.com/download/)或**App Store**。

2. **运行 Xcode 11 测试版**– 运行 Xcode 11 之前更新和运行 Visual Studio for Mac，在安装一些工具，Xamarin 需要。

3. 在 Visual Studio for Mac 中，选择**Visual Studio > 检查更新...** ，选择**Xcode 11 预览**通道，并安装可用更新。

4. 在 Visual Studio for Mac 中，选择**Visual Studio > 首选项 > 项目 > SDK 位置 > Apple** ，然后选择**Xcode beta.app**。

5. （可选）如果评估使用此预览版_Xcode 11 测试版 3_，必须启用链接。 右键单击你的项目中，导航到**选项 > iOS 生成 > 链接器行为**并将链接器行为值设置为**仅上链接框架 Sdk**。 此解决方法不需要在将来的预览版。

6. （可选）**在 iOS 设备上安装 iOS 13** – 表示设备测试的应用程序与 Xcode 11，已注册的 Apple 开发人员可以使用 Api 引入[下载](https://developer.apple.com/download)并在其设备上安装操作系统。 因为 iOS 处于测试阶段，一定要小心在主要设备上安装它。

   > [!TIP]
   > 即使您的应用程序不使用任何新的 Api，请务必使用最新的 Xcode 11 Sdk 生成和测试，以确保一切按预期工作。 如果应用不会调用任何新的 Api，可以使用这些新的 Sdk 重新编译它并尚未升级到新的操作系统的设备上对其进行测试。
   >
   > 将设备升级到最新的操作系统版本从 Apple Xamarin 应用的测试之前，请确保：
   >
   > - 读取[Apple 的发行说明](https://developer.apple.com/download/)用于操作系统更新。

## <a name="related-links"></a>相关链接

- [下载 Xcode](https://developer.apple.com/download/)
- [Xamarin.iOS 预览版发行说明](/xamarin/ios/release-notes/12/12.99)
