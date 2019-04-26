---
title: 检查器安装和要求
description: 本文档介绍如何安装 Xamarin 检查器，并讨论了受支持的操作系统、 Ide 和应用平台。
ms.prod: xamarin
ms.assetid: 81174493-02D3-4FF5-AD57-04F3288A7F94
author: lobrien
ms.author: laobri
ms.date: 06/19/2018
ms.openlocfilehash: 2bbf0bda42b7bce483d9d036ebf39314dcb73072
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61373464"
---
# <a name="inspector-installation-and-requirements"></a>检查器安装和要求

## <a name="download-and-installation"></a>下载和安装

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

1. 下载并安装[Visual Studio Enterprise](https://visualstudio.microsoft.com/vs/) ，然后选择**使用.NET 的移动开发**工作负荷。
1. [登录](https://docs.microsoft.com/visualstudio/ide/signing-in-to-visual-studio)若要启用你的企业订阅。
1. [检查](~/tools/inspector/inspect.md)你自己的应用 ！

# <a name="macostabmacos"></a>[macOS](#tab/macos)

1. 下载并安装[Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/)。
1. [登录](https://docs.microsoft.com/visualstudio/mac/activation)若要启用你的企业订阅。
1. [检查](~/tools/inspector/inspect.md)你自己的应用 ！

-----

## <a name="requirements"></a>要求

### <a name="supported-operating-systems"></a>Supported Operating Systems

- **Mac** -OS X 10.11 或更高版本
- **Windows** -Windows 7 或更高版本 (与 Internet Explorer 11 或更高版本和.NET 4.6.1 或更高版本)

### <a name="supported-ides"></a>支持的 Ide

- Visual Studio for Mac
- 使用 visual Studio 2017**使用.NET 的移动开发**工作负荷

适用于企业客户提供实时应用检测。

<a name="supported-platforms" />

### <a name="supported-app-platforms"></a>受支持的应用平台

|应用程序平台|IDE 支持|说明|
|--- |--- |--- |
|Mac|仅支持在 Visual Studio for Mac|
|iOS|在 Visual Studio 2017 和 Visual Studio for Mac 支持| 链接器行为必须设置为**不链接**(下**iOS 生成**项目选项) |
|Android|在 Visual Studio 2017 和 Visual Studio for Mac 支持|必须针对 Android > = 4.0.3，with **fastdev**启用。<br />必须使用 Google、 Visual Studio 或 Xamarin Android 仿真程序。 Android 7 仿真程序可能不允许在此时间的检查。|
|WPF|仅在 Visual Studio 2017 中支持|

<a name="reporting-bugs" />

## <a name="reporting-bugs"></a>报告 Bug

应直接通过 Visual Studio 报告 bug:

- **帮助 > 反馈 > 报告问题**

请提供所有以下信息：

### <a name="platform-version-information"></a>平台版本信息

此信息是至关重要的。

Visual Studio For Mac

- **Visual Studio > 有关 Visual Studio > 显示详细信息 > 复制信息**
- 粘贴 bug 报告

Visual Studio

- **帮助 > 关于 Visual Studio > 复制信息**
- 让我们知道你的操作系统版本和是否在运行 32 位或 64 位 Windows。

### <a name="log-files"></a>日志文件

始终附加 IDE 和检查器的客户端日志文件。

检查器客户端

- Mac: `~/Library/Logs/Xamarin/Inspector/Xamarin Inspector {date}.log`
- Windows：`%LOCALAPPDATA%\Xamarin\Inspector\logs\Xamarin Inspector {date}.log`

1.4.x 还提供从主菜单中查找工具 (macOS) 或资源管理器 (Windows) 直接选择日志文件的功能：

- **帮助 > 显示日志文件**

Visual Studio For Mac

- `~/Library/Logs/VisualStudio/7.0/Ide.log`

Visual Studio

- `%LOCALAPPDATA%\Xamarin\Logs\{VS version}\Inspector {date}.log`
- Visual Studio 的内容**输出**窗格也可能是信息性。

### <a name="project-settings"></a>项目设置

如果可以将附加 **.csproj**对于想要检查的项目，它会非常有帮助。 这是比询问有关单个设置更容易。

此外请确认您已在调试配置。

### <a name="selected-devices"></a>所选的设备

对于 Android 和 iOS，非常重要，我们知道什么设备时你想要检查进行调试。 我们需要知道：

- 设备在 IDE 中所示的名称
- 设备 OS 版本
- Android:验证使用 x86 仿真程序
- Android:您是否使用仿真程序平台是什么？ Google 仿真器？ Visual Studio Android 仿真器？ Xamarin Android Player？
- 正确进行调试的应用显示和函数，在该设备？
- 设备是否具有网络连接 （通过 web 浏览器的检查）？

[client-bugs]: https://github.com/Microsoft/workbooks/issues/new
