---
title: 适用于 Xamarin.iOS 和 tvOS 应用的无线部署
description: 本文档介绍如何以无线方式将 Xamarin.iOS 应用从 Visual Studio for Mac 或 Visual Studio 2019 部署到 iOS 设备。
ms.prod: xamarin
ms.assetid: 5AB4C5A9-4FBB-4DCB-BD72-0022D5439E65
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.custom: video
ms.date: 01/22/2019
ms.openlocfilehash: 82bdd2c160572b0d813fb87d81b09c00996cef18
ms.sourcegitcommit: c4be32ef914465e808d89767c4d5ee72afe93cc6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/02/2019
ms.locfileid: "58854829"
---
# <a name="wireless-deployment-for-xamarinios-and-tvos-apps"></a>适用于 Xamarin.iOS 和 tvOS 应用的无线部署

开发人员工作流的一个重要部分是部署到设备。 Xcode 9 引入了一个通过网络部署到 iOS 设备或 Apple TV 的选项，而不是在每次想部署和调试应用时都必须使用线缆硬连接设备。 Visual Studio for Mac 7.4 和 Visual Studio 15.6 版本中已引入此功能。

本指南详细介绍了如何通过网络配对和部署到设备。

## <a name="requirements"></a>要求

Visual Studio for Mac 和 Visual Studio 提供无线部署功能。

若要使用无线部署，必须具有：

# [<a name="visual-studio-for-mac"></a>Visual Studio for Mac](#tab/macos)

- macOS 10.12.4
- 最新版 Visual Studio for Mac
- Xcode 9.0 或更高版本
- 使用 iOS 11.0 或 tvOS 11.0 和更高版本的设备

# [<a name="visual-studio"></a>Visual Studio](#tab/windows)

- 最新版 Visual Studio
- 使用 iOS 11.0 或 tvOS 11.0 和更高版本的设备

应在 Mac 生成在主机上安装以下组件：

- macOS 10.12.4
- Visual Studio for Mac
- Xcode 9.0 或更高版本

-----

## <a name="connecting-a-device"></a>连接设备

为了在设备上以无线方式进行部署和调试，你必须将 iOS 设备或 Apple TV 与 Mac 上的 Xcode 配对。 配对后，你可以从 Visual Studio 中的设备目标列表选择。 

以下配对过程应只需在每个设备上执行一次。 Xcode 将保留连接设置。

<a name="pair" />

### <a name="pairing-an-ios-device-with-xcode"></a>将 iOS 设备与 Xcode 配对

1. 打开 Xcode 并转到“窗口 > 设备和模拟器”。
2. 使用闪电电缆将 iOS 设备插入 Mac 中。 你可能需要在设备上选择“信任此计算机”。
3. 选择设备，再选中“通过网络连接”复选框来配对设备：![显示“通过网络连接”选项的“设备和模拟器”窗口](wireless-deployment-images/image2.png)

### <a name="pairing-an-apple-tv-with-xcode"></a>将 Apple TV 与 Xcode 配对

1. 确保 Mac 和 Apple TV 连接到同一网络。

2. 打开 Xcode 并转到“窗口 > 设备和模拟器”。

3. 在 Apple TV 中，转到“设置 > 远程和设备 > 远程应用和设备”。

4. 在 Xcode 的“已发现”区域选择 Apple TV，然后输入 Apple TV 上显示的验证码。

5. 单击“连接”按钮。 成功配对后，Apple TV 旁会出现一个网络连接图标。

## <a name="deploy-to-a-device"></a>部署到设备

在设备以无线方式连接并准备好用于部署时，它会显示在设备目标列表中，就像设备通过 USB 连接一样。

若要在物理设备上测试，设备必须经过[预配](~/ios/get-started/installation/device-provisioning/index.md)。 尝试部署到设备之前，请确保执行此操作。 

若要部署到 iOS 或 tvOS 设备，请使用以下步骤：

1. 确保部署计算机和目标设备位于同一无线网络。 

2. 从目标设备列表中选择设备并运行应用程序。

2. 如果设备被锁定，系统将提示你解锁设备。 设备解锁后，应用会部署到设备。

无线调试在无线部署后会自动启用，因此，你可以像以往一样使用以前设置的断点并继续调试工作流。

## <a name="troubleshooting"></a>疑难解答

1. 始终确保 iOS 设备或 Apple TV 连接到与 Mac 相同的网络。

2. 如果设备未显示在 Visual Studio 中，请检查 Xcode 的“设备和模拟器”窗口。 

    * 如果 Xcode **未**将设备显示为已连接，请尝试再次[配对](#pair)设备。

    * 如果 Xcode 未将设备显示为已连接，请尝试重新启动 Visual Studio 和设备。

3. 如果尚未执行操作，你需要[预配](~/ios/get-started/installation/device-provisioning/index.md)设备。

4. 如果遇到有关此功能的问题且无法通过上述步骤修复，请在[开发人员社区](https://developercommunity.visualstudio.com/spaces/41/index.html)中提交问题。

## <a name="related-links"></a>相关链接

- [使用 Xcode 与无线设备配对](https://help.apple.com/xcode/mac/9.0/index.html?localePath=en.lproj#/devbc48d1bad)

## <a name="related-video"></a>相关视频

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Debug-to-iOS-Devices-Over-Wi-Fi/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
