---
title: 如何彻底卸载 Xamarin for Visual Studio？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: c1742239-05ea-449d-9c99-611e5e5a90e4
author: asb3993
ms.author: amburns
ms.date: 12/02/2016
ms.openlocfilehash: 99fde9330498ee62d3cf6b5910c2cbfae39cfdeb
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61159618"
---
# <a name="how-do-i-perform-a-thorough-uninstall-for-xamarin-for-visual-studio"></a>如何彻底卸载 Xamarin for Visual Studio？


1.  在 Windows 控制面板中，卸载存在的所有以下项目：

    -   Xamarin
    -   Xamarin for Windows
    -   Xamarin.Android
    -   Xamarin.iOS
    -   面向 Visual Studio 的 Xamarin

2.  在资源管理器中，删除 Xamarin Visual Studio 扩展文件夹中的任何保留文件（所有版本，包括 _Program Files_ 和 _Program Files (x86)_）：

    _C:\\Program Files\*\\Microsoft Visual Studio 1\*.0\\Common7\\IDE\\Extensions\\Xamarin_

3.  同时删除 Visual Studio 的 MEF 组件缓存目录：

    _%LOCALAPPDATA%\\Microsoft\\VisualStudio\\1\*.0\\ComponentModelCache_

    实际上，此步骤本身通常不足以解决错误，如：

    -   “未正确加载'XamarinShellPackage'包”

    -   “无法打开项目文件... 缺少项目子类型”

    -   “对象引用未设置为某个对象的实例。  在 Xamarin.VisualStudio.IOS.XamarinIOSPackage.Initialize() 上”

    -   “SetSite 对包无效”（在 Visual Studio 的 _ActivityLog.xml_ 中）

    -   “LegacySitePackage 对包无效”（在 Visual Studio 的 _ActivityLog.xml_ 中）

    （另请参阅 [Clear MEF Component Cache](https://visualstudiogallery.msdn.microsoft.com/22b94661-70c7-4a93-9ca3-8b6dd45f47cd)（清除 MEF 组件缓存）Visual Studio 扩展。  若要深入了解 Visual Studio 中可能导致这些错误的上游问题，请参阅 [Bug 40781，第 19 条评论](https://bugzilla.xamarin.com/show_bug.cgi?id=40781#c19)。）

4.  此外，检查 _VirtualStore_ 目录，了解 Windows 是否可能在此处存储了 _Extensions\\Xamarin_ 或 _ComponentModelCache_ 目录的任何覆盖文件：

    _%LOCALAPPDATA%\\VirtualStore_

5.  打开注册表编辑器 (`regedit`)。

6.  查找以下键：

    _HKEY\_LOCAL\_MACHINE\\SOFTWARE\\Wow6432Node\\Microsoft\\Windows\\CurrentVersion\\SharedDlls_

7.  查找并删除与此模式匹配的所有项：

    _C:\\Program Files\*\\Microsoft Visual Studio 1\*.0\\Common7\\IDE\\Extensions\\Xamarin_

8.  查找此键：

    _HKEY\_CURRENT\_USER\\Software\\Microsoft\\VisualStudio\\1\*.0\\ExtensionManager\\PendingDeletions_

9.  删除看起来可能与 Xamarin 相关的所有项。  例如，下面的项曾在旧版 Xamarin 中引发问题：

    _Mono.VisualStudio.Shell,1.0_

10. 打开管理员 `cmd.exe` 命令提示符，然后为每个安装的 Visual Studio 版本运行 `devenv /setup` 和 `devenv /updateconfiguration` 命令。  例如，为 Visual Studio 2015 运行：

    ```
    "%ProgramFiles(x86)%\Microsoft Visual Studio 14.0\Common7\IDE\devenv.exe" /setup
    "%ProgramFiles(x86)%\Microsoft Visual Studio 14.0\Common7\IDE\devenv.exe" /updateconfiguration
    ```

11. 重新启动。

12. 重新安装当前稳定版本的 Xamarin 使用从[visualstudio.com](https://visualstudio.com/xamarin/)。

## <a name="additional-troubleshooting-steps-for-package-did-not-load-correctly"></a>关于“未正确加载包”的其他故障排除步骤

如果上述步骤无法解决“未正确加载包”的错误，可尝试下面几个步骤。

1.  创建新的 Windows 用户帐户。

2.  检查新用户加载 Xamarin Visual Studio 扩展时是否会发生错误。

3.  如果可以正常加载扩展，那么问题可能是由原始用户的某些存储设置引起的：

    -   **资源管理器中** - _%LOCALAPPDATA%\\Microsoft\\VisualStudio\\1\*.0_
    -   **regedit 中** - _HKEY\_CURRENT\_USER\\Software\\Microsoft\\VisualStudio\\1\*.0_
    -   **regedit 中** - _HKEY\_CURRENT\_USER\\Software\\Microsoft\\VisualStudio\\1\*.0\_Config_

4.  如果这些存储设置确实是问题的起因，可尝试备份，然后将其删除。
