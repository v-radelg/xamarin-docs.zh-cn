---
title: 工作簿安装和要求
description: 本文档介绍如何下载和安装 Xamarin 工作簿，讨论受支持的平台和系统要求。
ms.prod: xamarin
ms.assetid: 9D4E10E8-A288-4C6C-9475-02969198C119
author: lobrien
ms.author: laobri
ms.date: 06/19/2018
ms.openlocfilehash: a1001163d89a9a9cda16a7ee5e644307fcc9875c
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61218575"
---
# <a name="workbooks-installation-and-requirements"></a>工作簿安装和要求

<a name="install" />

## <a name="download-and-install"></a>下载并安装

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

1. 检查[要求](#requirements)下面。
2. 下载并安装[Xamarin 工作簿的 Windows](https://dl.xamarin.com/interactive/XamarinInteractive.msi)。
3. 启动[一番](~/tools/workbooks/workbook.md)与工作簿或尝试[示例](https://developer.xamarin.com/workbooks)

# <a name="macostabmacos"></a>[macOS](#tab/macos)

1. 检查[要求](#requirements)下面。
2. 下载并安装[Mac 的 Xamarin Workbooks](https://dl.xamarin.com/interactive/XamarinInteractive.pkg)。
3. 启动[一番](~/tools/workbooks/workbook.md)与工作簿或尝试[示例](https://developer.xamarin.com/workbooks)

-----

## <a name="requirements"></a>要求

#### <a name="supported-operating-systems"></a>Supported Operating Systems

- **Mac** -OS X 10.11 或更高版本
- **Windows** -Windows 7 或更高版本 (与 Internet Explorer 11 或更高版本和.NET 4.6.1 或更高版本)

#### <a name="supported-app-platforms"></a>受支持的应用平台

|应用程序平台|OS 支持|说明|
|--- |--- |--- |
|Mac|仅在 Mac 上支持|
|iOS|在 Mac 和 Windows 上受支持|Mac 上必须安装 Xamarin.iOS 11.0 和 Xcode 9.0 或更高版本 在 Windows 上运行 iOS 工作簿需要运行上述，所有的 Mac 生成主机和[远程 iOS 模拟器](~/tools/ios-simulator/index.md)安装在 Windows 上。|
|Android|在 Mac 和 Windows 上受支持|必须使用 Google、 Visual Studio 或 Xamarin Android 仿真程序中，通过虚拟设备 > = 5.0|
|WPF|仅在 Windows 上支持|
|控制台 (.NET Framework)|在 Mac 和 Windows 上受支持|
|控制台 （.NET Core）|在 Mac 和 Windows 上受支持|


## <a name="reporting-bugs"></a>报告 Bug

请[GitHub 上报告问题][bugs]，并包括所有的以下信息：

### <a name="log-files"></a>日志文件

始终将附加工作簿客户端日志文件：

- Mac: `~/Library/Logs/Xamarin/Workbooks/Xamarin Workbooks {date}.log`
- Windows：`%LOCALAPPDATA%\Xamarin\Workbooks\logs\Xamarin Workbooks {date}.log`

1.4.x 还提供从主菜单中查找工具 (macOS) 或资源管理器 (Windows) 直接选择日志文件的功能：

- **帮助 > 显示日志文件**

#### <a name="log-paths-for-workbooks-13-and-earlier"></a>为工作簿 1.3 及更早版本的日志路径：

- Mac: `~/Library/Logs/Xamarin/Inspector/Xamarin Inspector {date}.log`
- Windows：`%LOCALAPPDATA%\Xamarin\Inspector\logs\Xamarin Inspector {date}.log`

### <a name="platform-version-information"></a>平台版本信息

它是非常有帮助，了解有关您的操作系统的详细信息，安装 Xamarin 产品。

从工作簿中的主菜单：

* **帮助 > 复制版本信息**

#### <a name="instructions-for-workbooks-13-and-earlier"></a>为工作簿 1.3 及更早版本的说明：

Visual Studio For Mac

- **Visual Studio > 有关 Visual Studio > 显示详细信息 > 复制信息**
- 粘贴 bug 报告

Visual Studio

- **帮助 > 关于 Visual Studio > 复制信息**
- 让我们知道你的操作系统版本和是否在运行 32 位或 64 位 Windows。

### <a name="samples"></a>示例

如果可以附加或链接到 **.workbooks**文件出现问题，可帮助更快地解决 bug。

### <a name="devices"></a>设备

如果无法连接到 iOS 或 Android 工作簿，并且已签[我们故障排除页](~/tools/workbooks/troubleshooting/index.md)，我们将需要知道：

- 尝试连接到设备的名称
- 设备 OS 版本
- Android:验证使用 x86 仿真程序
- Android:您是否使用仿真程序平台是什么？ Google 仿真器？
  Visual Studio Android 仿真器？ Xamarin Android Player？
- 在 Windows 上的 iOS:哪个版本的 Xamarin 远程 iOS 模拟器是否已安装 (检查**添加/删除程序**中**控制面板**)？
- 在 Windows 上的 iOS:请提供平台版本信息的 Mac 生成主机
- 设备是否具有网络连接 （通过 web 浏览器的检查）？

[bugs]: https://github.com/Microsoft/workbooks/issues/new

## <a name="uninstall"></a>卸载

### <a name="windows"></a>Windows

具体取决于如何获取工作簿，可能需要执行两个卸载过程。 请检查这两种完全卸载该软件。

#### <a name="visual-studio-installer"></a>Visual Studio 安装程序

如果您有 Visual Studio 2017，打开**Visual Studio 安装程序**，并查看**各个组件**有关**Xamarin Workbooks**。 如果选中，请取消选中它，然后单击**修改**卸载。

#### <a name="system-uninstall"></a>系统卸载

如果您自己安装的工作簿与下载的安装程序，它将需要通过卸载**应用和功能**Windows 10 上或通过系统设置页面**添加/删除程序**控件中在较旧版本的 Windows 上的面板。

> **启动 > 设置 > 系统 > 应用和功能**

![](install-images/windows-remove.png "Xamarin Workbooks 如下所示&quot;应用&amp;功能&quot;")

**你仍应遵循 Visual Studio 安装程序以确保工作簿不会不获取重新安装不知情的情况下的过程。**

<a name="uninstall-macos" />

### <a name="macos"></a>macOS

从开始[1.2.2](https://developer.xamarin.com/releases/interactive/interactive-1.2/)，可以通过运行从终端卸载 Xamarin 工作簿：

```bash
sudo /Library/Frameworks/Xamarin.Interactive.framework/Versions/Current/uninstall
```

卸载程序将详细介绍，文件和目录，它将删除并继续下一步之前要求确认。

传递`-help`自变量`uninstall`更高级的方案的脚本。

对于旧版本，需手动删除以下各项：

1. 在 `"/Applications/Xamarin Workbooks.app"` 删除 Workbooks 应用
2. 在 `"Applications/Xamarin Inspector.app"` 删除 Inspector 应用
2. 删除加载项：`"~/Library/Application Support/XamarinStudio-6.0/LocalInstall/Addins/Xamarin.Interactive"` 和 `"~/Library/Application Support/XamarinStudio-6.0/LocalInstall/Addins/Xamarin.Inspector"`
3. 在 `/Library/Frameworks/Xamarin.Interactive.framework` 和 `/Library/Frameworks/Xamarin.Inspector.framework` 删除 Inspector 和支持文件

## <a name="downgrading"></a>降级

捆绑包标识符**应用程序/Xamarin Workbooks.app**从更改`com.xamarin.Inspector`到`com.xamarin.Workbooks`1.4 版中作为工作簿和检查器现在完全拆分。

由于在较旧的安装程序中的一个 bug，不能降级 1.4 或更高版本使用 1.3.2 或较旧的安装程序的版本。

若要从 1.4 或更高版本到 1.3.2 或更低版本降级：

1. [手动卸载 Workbooks 与 Inspector](#uninstall-macos)
2. 运行 1.3.2 或更低版本`.pkg`安装程序
