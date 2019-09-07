---
title: 卸载 Xamarin
description: 本文档介绍了如何在 Mac 和 Windows 上卸载 Xamarin。 其中具体介绍了如何卸载 Mono、Xamarin.Android、Xamarin.iOS 和其他工具。
ms.prod: xamarin
ms.assetid: b83a85ec-842a-444c-8f82-c2464eda099b
author: conceptdev
ms.author: crdun
ms.date: 04/08/2017
ms.openlocfilehash: a5a9ddfe92bd8f5b743da2c535a93c282542c860
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70756788"
---
# <a name="uninstalling-xamarin"></a>卸载 Xamarin

本指南介绍如何在 macOS 上删除 Xamarin，或如何在 Windows 上从 Visual Studio 中删除 Xamarin。

如果需要使用通用安装程序重新安装 Xamarin，始终建议先重启计算机。

## <a name="uninstalling-xamarin-on-macos"></a>卸载 macOS 上的 Xamarin

本指南介绍了单独卸载每个产品的知识，可导航到相关部分查看。 遵循本指南中的全部指导，可卸载整个 Xamarin 工具集，包括列出的产品：

- [Mono](#uninstallmono)
- [Xamarin.Android](#uninstallandroid)
- [Xamarin.iOS](#uninstallios)
- [Xamarin.Mac](#uninstallmac)
- [工作簿](#uninstallworkbooks)
- [Xamarin Profiler](#uninstallprofiler)
- [安装程序](#uninstallinstaller)

> [!TIP]
> 我们提供了[卸载脚本](https://raw.githubusercontent.com/MicrosoftDocs/visualstudio-docs/master/mac/resources/uninstall-vsmac.sh)，可用于从 macOS 计算机删除 Xamarin。 有关使用该脚本的详细信息，请参阅本指南的[使用卸载脚本](#uninstallscript)一节。

### <a name="uninstalling-visual-studio-for-mac"></a>卸载 Visual Studio for Mac

从 Mac 中卸载 Xamarin 的第一步是在 /Applications 目录中找到 Visual Studio.app，并将其拖动到回收站。 或者，单击右键并选择“移到回收站”，如下图所示：

![将 Visual Studio 应用程序移动到回收站](uninstalling-xamarin-images/uninstall-image1.png)

删除此应用程序包会一并删除 Visual Studio for Mac，但文件系统上仍可能存在其他与 Xamarin 相关的文件。

要删除 Visual Studio for Mac 的所有痕迹，请在终端运行以下命令：

```bash
sudo rm -rf "/Applications/Visual Studio.app"
rm -rf ~/Library/Caches/VisualStudio
rm -rf ~/Library/Preferences/VisualStudio
rm -rf ~/Library/Preferences/Visual\ Studio
rm -rf ~/Library/Logs/VisualStudio
rm -rf ~/Library/VisualStudio
rm -rf ~/Library/Preferences/Xamarin/
rm -rf ~/Library/Developer/Xamarin
rm -rf ~/Library/Application\ Support/VisualStudio
rm -rf ~/Library/Application\ Support/VisualStudio/7.0/LocalInstall/Addins/
```

> [!NOTE]
> 若要了解如何卸载 Visual Studio for Mac，请参阅 docs.microsoft.com 上的[卸载](https://docs.microsoft.com/visualstudio/mac/uninstall)指南

<a name="uninstallmono" />

### <a name="uninstall-mono-sdk-mdk"></a>卸载 Mono SDK (MDK)

Mono 是 .NET Framework 的开放源代码实现，可供所有 Xamarin 产品（Xamarin.iOS、Xamarin.Android 和 Xamarin.Mac）使用，让用户能使用 C# 开发这些平台。

> [!WARNING]
> Xamarin 外的其他应用程序也使用 Mono，例如 Unity。 卸载 Mono 前，请确保 Mono 上没有其他依赖项。

要删除 Mono Framework，请在终端运行以下命令：

```bash
sudo rm -rf /Library/Frameworks/Mono.framework
sudo pkgutil --forget com.xamarin.mono-MDK.pkg
sudo rm /etc/paths.d/mono-commands
```

<a name="uninstallandroid" />

### <a name="uninstall-xamarinandroid"></a>卸载 Xamarin.Android

卸载 Xamarin.Android 时，需要删除使用 Xamarin.Android 时所需的大量项，例如 Android SDK 和 Java SDK。 本节介绍了如何卸载所有必要部分。

要删除 Xamarin.Android，请在终端运行以下命令：

```bash
sudo rm -rf /Developer/MonoDroid
rm -rf ~/Library/MonoAndroid
sudo pkgutil --forget com.xamarin.android.pkg
sudo rm -rf /Library/Frameworks/Xamarin.Android.framework
```

#### <a name="uninstall-android-sdk-and-java-sdk"></a>卸载 Android SDK 和 Java SDK

开发 Android 应用程序需要 Android SDK。 要完全删除 Android SDK 的所有部分，请在 ~/Library/Developer/Xamarin/ 中找到相关文件，并将其移到回收站。

不必卸载 Java SDK (JDK)，因为它已预先打包为 Mac OS X 一部分。

#### <a name="uninstall-android-avd"></a>卸载 Android AVD

> [!WARNING]
> 除 Visual Studio for Mac 之外，还有其他应用程序使用 Android AVD 和这些附加 Android 组件，如 Android Studio。
> 删除此目录可能导致 Android Studio 中的项目中断。 

要删除任何 Android AVD 和附加 Android 组件，请使用以下命令：

```bash
rm -rf ~/.android
```

要仅删除 Android AVD，请使用以下命令：

```bash
rm -rf ~/.android/avd
```

<a name="uninstallios" />

### <a name="uninstall-xamarinios"></a>卸载 Xamarin.iOS

Xamarin.iOS 支持使用 C# 或 F# 开发 iOS 应用程序。 要从计算机中卸载 Xamarin.iOS，请执行以下步骤：

要删除所有 Xamarin.iOS 文件，请在终端使用以下命令：

```bash
rm -rf ~/Library/MonoTouch
sudo rm -rf /Library/Frameworks/Xamarin.iOS.framework
sudo rm -rf /Developer/MonoTouch
sudo pkgutil --forget com.xamarin.monotouch.pkg
sudo pkgutil --forget com.xamarin.xamarin-ios-build-host.pkg
sudo pkgutil --forget com.xamarin.xamarin.ios.pkg
```

<a name="uninstallmac" />

### <a name="uninstall-xamarinmac"></a>卸载 Xamarin.Mac

可使用以下两个命令分别彻底删除 Mac 上的产品和许可证，进而从计算机上删除 Xamarin.Mac：

```bash
sudo rm -rf /Library/Frameworks/Xamarin.Mac.framework
rm -rf ~/Library/Xamarin.Mac
```

<a name="uninstallworkbooks" />

### <a name="uninstall-workbooks"></a>卸载 Workbooks

要删除 Xamarin Workbooks 1.2.2 版及更高版本，请在终端使用以下命令：

```bash
sudo /Library/Frameworks/Xamarin.Interactive.framework/Versions/Current/uninstall
```

对于较早版本，请参阅[工作簿](~/tools/workbooks/install.md#uninstall-macos)卸载指南。

<a name="uninstallprofiler" />

### <a name="uninstall-the-xamarin-profiler"></a>卸载 Xamarin Profiler

要删除 Xamarin Profiler，请在终端使用以下命令：

```bash
sudo rm -rf "/Applications/Xamarin Profiler.app"
```

<a name="uninstallinstaller" />

### <a name="uninstall-the-xamarin-installer"></a>卸载 Xamarin 安装程序

要删除 Xamarin 通用安装程序的所有痕迹，请使用以下命令：

```bash
rm -rf ~/Library/Caches/XamarinInstaller/
rm -rf ~/Library/Caches/VisualStudioInstaller/
rm -rf ~/Library/Logs/XamarinInstaller/
rm -rf ~/Library/Logs/VisualStudioInstaller/
rm -rf ~/Library/Preferences/Xamarin/
rm -rf "~/Library/Preferences/Visual Studio/"
```

<a name="uninstallscript" />

### <a name="using-the-uninstall-script"></a>使用卸载脚本

使用此卸载脚本，可一次性卸载 Visual Studio for Mac 及其关联的 Xamarin 组件。

该脚本中包含本文中出现的大部分命令。 由于可能存在外部依赖关系，脚本中忽略了两个主要部分：

- 卸载 Mono
- 卸载 Android AVD

要运行脚本，请执行以下步骤：

1. 右键单击脚本并选择“另存为…”， 在 Mac 上保存文件。

2. 打开“终端”，并将工作目录更改为下载脚本的位置：

    ```
    cd /location/of/file
    ```

3. 使脚本可执行，并通过 **sudo** 运行它：

    ```
    chmod +x ./xamarin_uninstall.sh
    sudo ./xamarin_uninstall.sh
    ```

4. 最后，删除卸载脚本。

此时，Xamarin 应该已从计算机上被卸载了。

<a name="uninstallwindows" />

## <a name="uninstalling-xamarin-on-windows"></a>卸载 Windows 上的 Xamarin

Xamarin 在以下各项中受支持：

- [Visual Studio 2019 和 Visual Studio 2017](#uninstallvs2017)
- [Visual Studio 2015](#uninstallvs2015)
- [Visual Studio 2013](#uninstallvs2015) [不支持]
- [Xamarin Studio](#uninstallxamarinstudio) [不支持]

<a name="uninstallvs2017" />

### <a name="visual-studio-2019-and-visual-studio-2017"></a>Visual Studio 2019 和 Visual Studio 2017

使用安装程序应用从 Visual Studio 2019 和 Visual Studio 2017 中卸载 Xamarin：

1. 使用“开始”菜单打开“Visual Studio 安装程序”。

2. 针对想要更改的实例按“修改”按钮。

    [![](uninstalling-xamarin-images/vs2017-02-sml.png "按“修改”按钮")](uninstalling-xamarin-images/vs2017-02.png#lightbox)

3. 在“工作负载”选项卡中，取消选择“使用 .NET 的移动开发”选项（在“移动与游戏”部分中）。

    [![](uninstalling-xamarin-images/vs2017-03-sml.png "取消选中“移动开发”工作负载")](uninstalling-xamarin-images/vs2017-03.png#lightbox)

4. 在窗口的右下角中，单击“修改”按钮。

5. 安装程序将删除已取消选择的组件（必须要在 Visual Studio 2017 关闭之后，安装程序才可进行修改）。

    [![](uninstalling-xamarin-images/vs2017-04-sml.png "按“修改”按钮")](uninstalling-xamarin-images/vs2017-04.png#lightbox)

通过在步骤 3 中切换到“各个组件”选项卡并取消选中特定组件，可卸载单个 Xamarin 组件（如 Profiler 或 Workbooks）：

[![](uninstalling-xamarin-images/vs2017-components-sml.png "卸载单个组件")](uninstalling-xamarin-images/vs2017-components.png#lightbox)

若要完全卸载 Visual Studio 2017，请从“启动”按钮旁具有三条横线的菜单中选择“卸载”。

[![](uninstalling-xamarin-images/vs2017-uninstall-sml.png "完全卸载 Visual Studio")](uninstalling-xamarin-images/vs2017-uninstall.png#lightbox)

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
>解决方法为，运行 Visual Studio 安装程序中的“修复”选项，这会重新安装缺少的组件。

## <a name="uninstalling-older-and-unsupported-products"></a>卸载旧产品和不受支持的产品

<a name="uninstallvs2015"></a>

### <a name="visual-studio-2015-and-earlier"></a>Visual Studio 2015 及更早版本

要完全卸载 Visual Studio 2015，请使用 [visualstudio.com 上的支持答案](https://visualstudio.microsoft.com/vs/support/vs2015/uninstall-visual-studio-2015/)。

通过“控制面板”，可从 Windows 计算机卸载 Xamarin。 导航到“程序和功能”，或“程序”>“卸载程序”，如下所示：

 [![](uninstalling-xamarin-images/image3.png "导航到“程序和功能”，或“程序”>“卸载程序”，如下所示")](uninstalling-xamarin-images/image3.png#lightbox) 

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

检查 VirtualStore 目录，查看 Windows 是否在此处存储了 Extensions\Xamarin 或 ComponentModelCache 目录的任何覆盖文件：

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

<a name="uninstallxamarinstudio"></a>

### <a name="uninstall-xamarin-studio-on-windows"></a>卸载 Windows 上的 Xamarin Studio

通过“控制面板”，可从 Windows 计算机卸载 Xamarin Studio。 导航到“程序和功能”，或“程序”>“卸载程序” 

若要卸载 Xamarin Studio，请在程序列表中找到 **Xamarin Studio 5.x.x**，然后单击“卸载”按钮。 

### <a name="uninstall-xamarin-studio-on-mac"></a>卸载 Mac 上的 Xamarin Studio

从 Mac 中卸载 Xamarin Studio 的第一步是在 **/Applications** 目录中找到 **Xamarin Studio.app**，并将其拖动到**回收站**。 或者，单击右键，然后选择“移到回收站”，如下所示：

 [![](uninstalling-xamarin-images/image1.png "或者，单击右键并选择“移到回收站”，如下所示")](uninstalling-xamarin-images/image1.png#lightbox)

删除此应用程序包会删除 Xamarin Studio，但与 Xamarin 相关的其他文件仍在文件系统上。

若要删除 Xamarin Studio 的所有痕迹，应在终端运行以下命令：

```bash
sudo rm -rf "/Applications/Xamarin Studio.app"
rm -rf ~/Library/Caches/XamarinStudio-*
rm -rf ~/Library/Preferences/XamarinStudio-*
rm -rf ~/Library/Logs/XamarinStudio-*
rm -rf ~/Library/XamarinStudio-*
```

## <a name="summary"></a>总结

本文介绍了如何通过使用终端命令从 Mac 完全卸载 Xamarin。 本文还介绍了如何通过“程序和功能”选项（适用于 Visual Studio 2015 及更早版本）和使用 Visual Studio 安装程序（适用于 Visual Studio 2017）从 Windows 计算机卸载 Xamarin。

## <a name="related-links"></a>相关链接

- [卸载脚本（示例）](https://raw.githubusercontent.com/MicrosoftDocs/visualstudio-docs/master/mac/resources/uninstall-vsmac.sh)
