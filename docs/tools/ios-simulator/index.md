---
title: 适用于 Windows 的远程 iOS 模拟器
description: 远程 iOS 模拟器为 Windows，可显示在 Windows 和 Visual Studio 2019 iOS 模拟器上测试应用。
ms.prod: xamarin
ms.assetid: 63c50190-7e54-4140-a30d-1a0e577c47d7
author: lobrien
ms.author: laobri
ms.date: 04/02/2019
ms.openlocfilehash: b962390d5a5a365ada93d1778e3efb65839f41c5
ms.sourcegitcommit: c4be32ef914465e808d89767c4d5ee72afe93cc6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/02/2019
ms.locfileid: "58854946"
---
# <a name="remoted-ios-simulator-for-windows"></a>适用于 Windows 的远程 iOS 模拟器

远程 iOS 模拟器为 Windows，可在 Visual Studio 2019 和 Visual Studio 2017 的 Windows 中显示 iOS 模拟器上测试应用。

[![i在 Windows 上运行的 OS 模拟器](images/hero-sml.png "在 Windows 上运行的 iOS 模拟器")](images/hero.png#lightbox)

## <a name="getting-started"></a>入门

远程 iOS 模拟器为 Windows 自动安装 Xamarin 在 Visual Studio 2019 和 Visual Studio 2017 的一部分。 若要使用它，请执行以下步骤：

1. [到 Mac 生成主机配对 Visual 2019](~/ios/get-started/installation/windows/connecting-to-mac/index.md)。
2. 在 Visual Studio 中，开始调试 iOS 或 tvOS 项目。 远程 iOS 模拟器为 Windows 将显示在 Windows 计算机上。

观看[此视频](deploy.md)有关的分步指南。

## <a name="simulator-window"></a>模拟器窗口

在模拟器窗口的顶部工具栏中包含了许多有用的按钮：

- **家庭**– 模拟 iOS 设备上的主页按钮。
- **锁**– 锁定模拟器 （轻扫以解锁）。
- **屏幕截图**– 将保存在模拟器的屏幕截图 (存储在**Pictures\Xamarin\iOS 模拟器\\**)。
- [**设置**](#settings) – 显示键盘、 位置和其他设置。
- [**其他选项**](#other-options) – 引出了各种模拟器选项，例如旋转、 抖动手势和 Touch id。

    [![iOS 模拟器映射示例](images/maps-app-sml.png "iOS 模拟器映射示例")](images/maps-app.png#lightbox)

## <a name="settings"></a>设置

单击工具栏上的齿轮图标，打开**设置**窗口：

[![iOS 模拟器设置](images/settings-sml.png "iOS 模拟器设置")](images/settings.png#lightbox)

这些设置，若要启用硬件键盘，请选择应在设备的位置 （静态和移动位置同时支持） 的报表启用 Touch ID 和重置的内容和设置模拟器。

## <a name="other-options"></a>其他选项

工具栏上的省略号按钮可显示其他选项，例如旋转、 抖动手势和重新启动。 可以通过在模拟器窗口中的任意位置右键单击作为列表查看这些相同的选项：

[![iOS 模拟器其他设置](images/more-sml.png "iOS 模拟器的其他设置")](images/more.png#lightbox)

## <a name="touchscreen-support"></a>触摸屏支持

大多数现代 Windows 计算机具有触摸屏。 由于远程 iOS 模拟器为 Windows 支持触摸交互，可以测试你的应用使用同一个 pinch、 轻扫和物理 iOS 设备所用的多根手指触摸手势。

同样，远程 iOS 模拟器为 Windows 视为 Apple 铅笔输入 Windows 触笔输入。

## <a name="disabling-the-remoted-ios-simulator-for-windows"></a>禁用远程 iOS 模拟器的 Windows

若要禁用远程 iOS 模拟器为 Windows，请导航到**工具 > 选项 > Xamarin > iOS 设置**并取消选中**远程 Simulator 到 Windows**。

[![c若要使用模拟器的 heckbox](images/options-sml.png "复选框以使用模拟器")](images/options.png#lightbox)

使用此选项时禁用，调试将打开连接的 Mac 上的 iOS 模拟器生成主机。
