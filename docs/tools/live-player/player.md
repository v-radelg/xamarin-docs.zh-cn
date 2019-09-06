---
title: Xamarin Live Player 应用
description: 本文档介绍 Xamarin Live Player 的应用程序，该应用程序可用于预览实时在设备上进行的代码更改。 它讨论了安装、示例、日志、设置、管理设备等。
ms.prod: xamarin
ms.assetid: A7EB73C1-38D7-46C5-9AF6-4C571C168BE7
author: conceptdev
ms.author: crdun
ms.date: 06/13/2019
ms.openlocfilehash: d725cb0687cce85f10dbf6915e4eeec785c0ae54
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2019
ms.locfileid: "70290210"
---
# <a name="xamarin-live-player-app"></a>Xamarin Live Player 应用

![预览版功能](~/media/shared/preview.png)

> [!WARNING]
> Xamarin Live Player 预览已结束。 应用不再可用。 以下说明适用于继续使用 Visual Studio 2017 预览的客户。

> [!TIP]
> 你可以在 Visual Studio 2019 或 Visual Studio for Mac 中使用[XAML 预览](~/xamarin-forms/xaml/xaml-previewer/index.md)器来查看你编辑的屏幕设计。

启动时，Xamarin Live Player 应用如下所示：

![Live Player Android 应用屏幕截图](player-images/app-android-sml.png)

按**配对到 Visual Studio**时，使用相机扫描计算机上显示的条形码：

![Android 条形码扫描器的屏幕截图](player-images/scan-android-sml.png)

如果连接成功，则代码几乎应立即在设备上运行（如[计算器示例](https://github.com/xamarin/mobile-samples/tree/master/LivePlayer/BasicCalculator)：

![在设备上运行的示例计算器应用](player-images/basic-calculator-sml.png)

## <a name="options"></a>选项

按下应用程序底部的信息按钮 **（i）** 显示 "**选项**" 菜单：

[!["选项" 菜单的屏幕截图](player-images/options-sml.png)](player-images/options.png#lightbox)

### <a name="logs"></a>日志

查看日志以诊断问题。

### <a name="settings"></a>设置

- 切换编译和运行时错误的显示。
- 版本信息。
- 发送反馈。

[![设置的屏幕截图](player-images/settings-sml.png)](player-images/settings.png#lightbox)

## <a name="managing-devices"></a>管理设备

若要首次连接设备，请按照[要求 & 安装程序](~/tools/live-player/install.md)中的说明进行操作。 可以将多个设备配对，并通过 IDE 对其进行管理。

# <a name="visual-studio-2017tabwindows"></a>[Visual Studio 2017](#tab/windows)

在 Visual Studio 中，选择 "**工具" > Xamarin Live Player > 管理设备 ...** "

![管理设备窗口](player-images/manage-tools-menu-vs.png)

此窗口可让你执行以下操作：

- 通过扫描代码配对新设备
- 通过键入在屏幕上显示的代码，也能配对设备
- 从列表中删除现有设备

你还可以从 "设备" 列表访问此窗口。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

在 Visual Studio for Mac 中，选择 "**工具" > （Xamarin Live Player） "管理设备 ...** "

![管理设备窗口](player-images/manage-tools-menu.png)

此窗口可让你执行以下操作：

- 通过扫描代码配对新设备
- 通过键入在屏幕上显示的代码，也能配对设备
- 从列表中删除现有设备

![管理设备窗口](player-images/manage.png)

你还可以从 "设备" 列表访问此窗口：

![从设备列表中选择 Xamarin Live Player 设备](player-images/manage-device-menu.png)

-----

如果遇到任何问题，请参阅[限制和故障排除](~/tools/live-player/troubleshooting.md)。

## <a name="related-links"></a>相关链接

- [疑难解答](~/tools/live-player/troubleshooting.md)

