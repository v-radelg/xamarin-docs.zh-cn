---
title: Xamarin Live Player 应用
description: 本文档介绍了 Xamarin Live Player 设备的实时应用程序，可用于预览代码更改。 它讨论了安装程序、 示例、 日志、 设置、 管理设备和的详细信息。
ms.prod: xamarin
ms.assetid: A7EB73C1-38D7-46C5-9AF6-4C571C168BE7
author: lobrien
ms.author: laobri
ms.date: 08/08/2017
ms.openlocfilehash: 89795e5df00b426c0f11c04a0844993071df1e25
ms.sourcegitcommit: c4be32ef914465e808d89767c4d5ee72afe93cc6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/02/2019
ms.locfileid: "58855193"
---
# <a name="xamarin-live-player-app"></a>Xamarin Live Player 应用

![预览功能](~/media/shared/preview.png)

> [!NOTE]
> 播放机的实时预览仅是在 Visual Studio 2017 中提供的。

以后在手机上安装的应用，请按照[安装说明进行操作](~/tools/live-player/install.md)连接到您的计算机。 请尝试之一[示例应用](~/tools/live-player/samples.md)以使其正常工作。

在启动时，Xamarin Live Player 应用如下所示：

![实时播放器 Android 应用程序屏幕截图](player-images/app-android-sml.png)

当您按下**与 Visual Studio 配对**，使用相机扫描条形码显示您的计算机上：

![Android 条形码扫描程序的屏幕截图](player-images/scan-android-sml.png)

如果连接成功，代码应在设备上几乎立即运行 (如[计算器示例](https://developer.xamarin.com/samples/mobile/LivePlayer/BasicCalculator)):

![设备上运行的示例计算器应用程序](player-images/basic-calculator-sml.png)

## <a name="options"></a>选项

按信息按钮 **(i)** 上的应用程序以显示底部**选项**菜单：

[![S选项菜单 creenshot](player-images/options-sml.png)](player-images/options.png#lightbox)

### <a name="logs"></a>日志

查看日志以诊断问题。

### <a name="settings"></a>设置

- 切换显示编译和运行时错误。
- 版本信息。
- 发送反馈。

[![S设置 creenshot](player-images/settings-sml.png)](player-images/settings.png#lightbox)

## <a name="managing-devices"></a>管理设备

若要首次连接设备，请按照中的说明[要求和安装程序](~/tools/live-player/install.md)。 可以对多个设备，并通过 IDE 管理它们。

# [<a name="visual-studio"></a>Visual Studio](#tab/windows)

在 Visual Studio 中，选择**工具 > Xamarin Live Player > 管理设备...**

![管理设备窗口](player-images/manage-tools-menu-vs.png)

此窗口可让你执行以下操作：

- 对通过扫描代码的新设备
- 或者通过键入其屏幕上显示的代码进行配对设备
- 从列表中删除现有的设备

此外可以从设备列表来访问此窗口。

# [<a name="visual-studio-for-mac"></a>Visual Studio for Mac](#tab/macos)

在 Visual Studio for Mac 中，选择**工具 > (Xamarin Live Player) 管理设备...**

![管理设备窗口](player-images/manage-tools-menu.png)

此窗口可让你执行以下操作：

- 对通过扫描代码的新设备
- 或者通过键入其屏幕上显示的代码进行配对设备
- 从列表中删除现有的设备

![管理设备窗口](player-images/manage.png)

此外可以从设备列表来访问此窗口：

![从设备列表中选择 Xamarin Live Player 设备](player-images/manage-device-menu.png)

-----

如果遇到任何问题，请参阅[限制和故障排除](~/tools/live-player/troubleshooting.md)。

## <a name="related-links"></a>相关链接

- [疑难解答](~/tools/live-player/troubleshooting.md)
- [示例，以便使用与 Live Player](https://developer.xamarin.com/samples/xamarin-live-player/all/)
