---
title: 适用于 Windows 的远程 iOS 模拟器
description: 使用适用于 Windows 的远程 iOS 模拟器，你可以在 Windows 与 Visual Studio 2019 一起显示的 iOS 模拟器上测试你的应用程序。
ms.prod: xamarin
ms.assetid: 63c50190-7e54-4140-a30d-1a0e577c47d7
author: davidortinau
ms.author: daortin
ms.date: 04/26/2019
ms.openlocfilehash: 8313af47882b1c8c47f1e82f62f0a43e4e9c704c
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029716"
---
# <a name="remoted-ios-simulator-for-windows"></a>适用于 Windows 的远程 iOS 模拟器

使用适用于 Windows 的远程 iOS 模拟器，你可以在 Windows 与 Visual Studio 2019 和 Visual Studio 2017 一起显示的 iOS 模拟器上测试你的应用程序。

[![在 Windows 上运行的 iOS 模拟器](images/hero-sml.png "在 Windows 上运行的 iOS 模拟器")](images/hero.png#lightbox)

## <a name="getting-started"></a>入门

适用于 Windows 的远程 iOS 模拟器作为 Xamarin 在 Visual Studio 2019 和 Visual Studio 2017 中的一部分自动安装。 若要使用它，请执行以下步骤：

1. [将 Visual 2019 与 Mac 生成主机配对](~/ios/get-started/installation/windows/connecting-to-mac/index.md)。
2. 在 Visual Studio 中，开始调试 iOS 或 tvOS 项目。 适用于 Windows 的远程 iOS 模拟器将显示在 Windows 计算机上。

观看[此视频](deploy.md)，了解循序渐进指南。

## <a name="simulator-window"></a>模拟器窗口

模拟器窗口顶部的工具栏包含许多有用的按钮：

- **Home** –模拟 iOS 设备上的 "主页" 按钮。
- **锁定**–锁定模拟器（轻扫以解锁）。
- **屏幕快照**–保存模拟器的屏幕截图（存储在**Pictures\Xamarin\iOS 模拟器 \\** ）中。
- [**设置**](#settings)–显示键盘、位置和其他设置。
- [**其他选项**](#other-options)–提供各种模拟器选项，如旋转、摇动手势和 Touch ID。

    [![iOS 模拟器映射示例](images/maps-app-sml.png "iOS 模拟器映射示例")](images/maps-app.png#lightbox)

## <a name="settings"></a>设置

单击工具栏的齿轮图标将打开 "**设置**" 窗口：

[![iOS 模拟器设置](images/settings-sml.png "iOS 模拟器设置")](images/settings.png#lightbox)

这些设置允许你启用硬件键盘，选择设备应报告的位置（同时支持静态和移动位置）、启用 Touch ID，并重置模拟器的内容和设置。

## <a name="other-options"></a>其他选项

工具栏的省略号按钮显示其他选项，如旋转、摇动手势和重新启动。 可以通过右键单击模拟器窗口中的任意位置，以列表的形式查看这些相同的选项：

[![iOS 模拟器其他设置](images/more-sml.png "iOS 模拟器其他设置")](images/more.png#lightbox)

## <a name="touchscreen-support"></a>触摸屏支持

大多数新式 Windows 计算机都具有触摸屏。 由于适用于 Windows 的远程 iOS 模拟器支持触摸交互，因此你可以使用与物理 iOS 设备一起使用的相同的挤压、刷和多指触摸笔势来测试应用。

同样，适用于 Windows 的远程 iOS 模拟器将 Windows 触笔输入视为 Apple 铅笔输入。

## <a name="sound-handling"></a>声音处理

模拟器播放的声音来自主机 Mac 的扬声器。
Windows 计算机上未听到 iOS 声音。

## <a name="disabling-the-remoted-ios-simulator-for-windows"></a>禁用适用于 Windows 的远程 iOS 模拟器

若要禁用适用于 Windows 的远程 iOS 模拟器，请导航到 "**工具" > 选项 "> Xamarin > IOS 设置**"，并取消选中 "**远程模拟器到 Windows**"。

[![用于使用模拟器的复选框](images/options-sml.png "用于使用模拟器的复选框")](images/options.png#lightbox)

禁用此选项后，调试将在连接的 Mac 生成主机上打开 iOS 模拟器。
