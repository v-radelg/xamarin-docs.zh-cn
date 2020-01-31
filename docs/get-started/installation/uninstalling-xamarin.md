---
title: 卸载 Xamarin
description: 本文档介绍了如何从 Windows 中卸载 Xamarin。
ms.prod: xamarin
ms.assetid: b83a85ec-842a-444c-8f82-c2464eda099b
author: conceptdev
ms.author: crdun
ms.date: 01/22/2020
ms.openlocfilehash: 4c9096edddeb00070aaabc3e93b283f2d55c1bfa
ms.sourcegitcommit: a3b7e016fb25584dbf57bae89b64a9f98031e7c9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2020
ms.locfileid: "76550002"
---
# <a name="uninstall-xamarin-from-visual-studio"></a>从 Visual Studio 中卸载 Xamarin

本指南介绍如何在 Windows 上从 Visual Studio 中删除 Xamarin。

<a name="uninstallvs2017" />

## <a name="visual-studio-2019-and-visual-studio-2017"></a>Visual Studio 2019 和 Visual Studio 2017

使用安装程序应用从 Visual Studio 2019 和 Visual Studio 2017 中卸载 Xamarin：

1. 使用“开始”菜单  打开“Visual Studio 安装程序”  。

2. 针对想要更改的实例按“修改”  按钮。

    [![](uninstalling-xamarin-images/vs2017-02-sml.png "Press the modify button")](uninstalling-xamarin-images/vs2017-02.png#lightbox)

3. 在“工作负载”  选项卡中，取消选择“使用 .NET 的移动开发”  选项（在“移动与游戏”  部分中）。

    [![](uninstalling-xamarin-images/vs2017-03-sml.png "Uncheck the Mobile Development workload")](uninstalling-xamarin-images/vs2017-03.png#lightbox)

4. 在窗口的右下角中，单击“修改”  按钮。

5. 安装程序将删除已取消选择的组件（必须要在 Visual Studio 2017 关闭之后，安装程序才可进行修改）。

    [![](uninstalling-xamarin-images/vs2017-04-sml.png "Press the Modify button")](uninstalling-xamarin-images/vs2017-04.png#lightbox)

通过在步骤 3 中切换到“各个组件”  选项卡并取消选中特定组件，可卸载单个 Xamarin 组件（如 Profiler 或 Workbooks）：

[![](uninstalling-xamarin-images/vs2017-components-sml.png "Uninstall individual components")](uninstalling-xamarin-images/vs2017-components.png#lightbox)

若要完全卸载 Visual Studio 2017，请从“启动”  按钮旁具有三条横线的菜单中选择“卸载”  。

[![](uninstalling-xamarin-images/vs2017-uninstall-sml.png "Uninstall Visual Studio completely")](uninstalling-xamarin-images/vs2017-uninstall.png#lightbox)

> [!IMPORTANT]
> 如果并行 (SxS) 安装了两个（或多个）Visual Studio 的实例（如发行版和预览版），则卸载一个实例可能会删除另一个 Visual Studio 实例中的一些 Xamarin 功能，包括：
>
> - Xamarin Profiler
> - Xamarin Workbooks/Inspector
> - Xamarin 远程 iOS 模拟器
> - Apple Bonjour SDK
>
> 在某些情况下，卸载其中一个 SxS 实例可能导致这些功能将被错误地删除。 在卸载了 SxS 实例后，这可能会降低针对系统上剩余的 Visual Studio 实例的 Xamarin 平台性能。
>
>解决方法为，运行 Visual Studio 安装程序中的“修复”  选项，这会重新安装缺少的组件。

<a name="uninstallvs2015"></a>

## <a name="visual-studio-2015-and-earlier"></a>Visual Studio 2015 及更早版本

要完全卸载 Visual Studio 2015，请使用 [visualstudio.com 上的支持答案](https://visualstudio.microsoft.com/vs/support/vs2015/uninstall-visual-studio-2015/)。

通过“控制面板”  ，可从 Windows 计算机卸载 Xamarin。 导航到“程序和功能”  ，或“程序”>“卸载程序”  ，如下所示：

 [![](uninstalling-xamarin-images/image3.png "Navigate to Programs and Features or Programs  Uninstall a Program as illustrated here")](uninstalling-xamarin-images/image3.png#lightbox)

在控制面板中，卸载存在的所有以下项目：

- Xamarin
- Xamarin for Windows
- Xamarin.Android
- Xamarin.iOS
- 面向 Visual Studio 的 Xamarin

在资源管理器中，删除 Xamarin Visual Studio 扩展文件夹中的任何保留文件（所有版本，包括 Program Files 和 Program Files (x86)）：

```
C:\Program Files*\Microsoft Visual Studio 1*.0\Common7\IDE\Extensions\Xamarin
```

删除 Visual Studio 的 MEF 组件缓存目录，该目录应位于以下位置：

```
%LOCALAPPDATA%\Microsoft\VisualStudio\1*.0\ComponentModelCache
```

检查 VirtualStore  目录，查看 Windows 是否在此处存储了 Extensions\Xamarin  或 ComponentModelCache  目录的任何覆盖文件：

```
%LOCALAPPDATA%\VirtualStore
```

打开注册表编辑器 (regedit)，查找以下项：

```
HKEY_LOCAL_MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\SharedDlls
```

查找并删除与此模式匹配的所有项：

```
C:\Program Files*\Microsoft Visual Studio 1*.0\Common7\IDE\Extensions\Xamarin
```

查找此键：

```
HKEY_CURRENT_USER\Software\Microsoft\VisualStudio\1*.0\ExtensionManager\PendingDeletions
```

删除看起来可能与 Xamarin 相关的所有项。 例如，包含字词 `mono` 或 `xamarin` 的任何项。

打开管理员 cmd.exe 命令提示符，然后为每个安装的 Visual Studio 版本运行 `devenv /setup` 和 `devenv /updateconfiguration` 命令。 例如，为 Visual Studio 2015 运行：

```cmd
"%ProgramFiles(x86)%\Microsoft Visual Studio 14.0\Common7\IDE\devenv.exe" /setup
"%ProgramFiles(x86)%\Microsoft Visual Studio 14.0\Common7\IDE\devenv.exe" /updateconfiguration
```
