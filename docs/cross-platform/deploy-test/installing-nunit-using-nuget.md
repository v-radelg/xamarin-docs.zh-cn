---
title: 使用 NuGet 安装 NUnit 2.6.4
description: 本文档介绍了如何使用 NuGet 将 NUnit 3.0 降级到 NUnit 2.6.4。 使用 Xamarin.UITest 时，必须这样做，因为它不支持 NUnit 3.x。
ms.prod: xamarin
ms.assetid: 7683F2B8-7FDF-48C4-8E7D-649D4D4E79F0
author: asb3993
ms.author: amburns
ms.date: 03/24/2017
ms.openlocfilehash: b6b95f730b966ef5edaabbe7b0f333c2cacf5bc5
ms.sourcegitcommit: 64d6da88bb6ba222ab2decd2fdc8e95d377438a6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2019
ms.locfileid: "58070834"
---
# <a name="installing-nunit-264-using-nuget"></a>使用 NuGet 安装 NUnit 2.6.4

_本指南介绍了如何使用 NuGet 将 NUnit 3.0 降级到 NUnit 2.6.4。_

在 Visual Studio for Mac 中或使用 Xamarin.UITest 编写测试的开发者应使用 [NUnit 2.6.4](http://nunit.org/index.php?p=docHome&r=2.6.4)，因为 NUnit 3.0 或更高版本与 Visual Studio for Mac 或 Xamarin.UITest 不兼容。 使用 NUnit 3.0 在 Visual Studio for Mac 或 Xamarin.UITests 中尝试运行单元测试会失败。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

本指南介绍了如何使用 NuGet 安装可用于 Visual Studio for Mac 的 NUnit 2.6.4。 如有必要，这些步骤还将卸载 NUnit 3.0。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

本指南介绍如何在 Visual Studio 2015 中使用 NuGet 将 NUnit 3.0 或更高版本降级到 NUnit 2.6.4。

-----

## <a name="requirements"></a>要求

本指南假定存在一个用于移动应用项目和测试项目的现有解决方案。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

## <a name="installing-nunit-264-in-visual-studio-for-mac"></a>在 Visual Studio for Mac 中安装 NUnit 2.6.4

以下步骤介绍如何安装 NUnit 2.6.4。


1. **打开程序包管理器** - 右键单击“包”，然后从弹出菜单中选择“添加包”：

    [![](installing-nunit-using-nuget-images/add-packages-xs.png "右键单击“包”，然后从弹出菜单中选择“添加包”")](installing-nunit-using-nuget-images/add-packages-xs.png#lightbox)
    
1. **搜索 `NUnit version:2.6.4`** - Visual Studio for Mac 会卸载 NUnit 3.0（如果必要），然后下载并安装 NUnit 2.6.4。 在“添加包”对话框右上角处的“搜索”字段中，输入文本 `nunit version:2.6.4`。 从搜索结果中选择“NUnit”，然后单击“添加包”按钮：

    [![](installing-nunit-using-nuget-images/nunit-search-xs.png "从搜索结果中选择“NUnit”，然后单击“添加包”按钮")](installing-nunit-using-nuget-images/nunit-search-xs.png#lightbox)


可通过在 Solution Pad 中检查 NUnit 包版本号来确认是否安装了 NUnit 2.6.4：

[![](installing-nunit-using-nuget-images/nunit-2-6-4-installed.png "在 Solution Pad 中检查 NUnit 包的版本号")](installing-nunit-using-nuget-images/nunit-2-6-4-installed.png#lightbox)

## <a name="summary"></a>总结

本指南介绍如何使用包管理器控制台在 Visual Studio for Mac 中将 NUnit 3.0 降级到 NUnit 2.6.4。


# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

## <a name="installing-nunit-264-in-visual-studio"></a>在 Visual Studio 中安装 NUnit 2.6.4

本部分着重介绍如何在 Visual Studio 2015 或更高版本中使用 NuGet 包管理器控制台卸载 NUnit 3.0 和安装 NUnit 2.6.4。


1. **启动 NuGet 程序包管理器控制台** -  **选择“工具”>“NuGet 程序包管理器”>“程序包管理器控制台”**：

    [![](installing-nunit-using-nuget-images/package-manager-console.png "启动 NuGet 包管理器控制台 - 选择“工具”>“NuGet 包管理器”>“包管理器控制台”")](installing-nunit-using-nuget-images/package-manager-console.png#lightbox)
    
1. **验证 NUnit 版本** - 可通过运行命令 `Get-Package -Project <UITEST PROJECT>` 来验证已安装的 NUnit 的版本：

    ```bash
    [PM] Get-Package -Project [TEST PROJECT NAME]
    
        Id                                  Versions                                 ProjectName
        --                                  --------                                 -----------
    NUnit                               {3.0.1}                                  [TEST PROJECT NAME]
    Xamarin.UITest                      {1.2.0}                                  [TEST PROJECT NAME]
    ```

如果显示的版本为 NUnit 3.0 或更高版本，则必须降级到 NUnit 2.6.4。

1. **卸载 NUnit 3.0** - 使用 `Uninstall-Package` commandlet 卸载 NUnit 3.0：

        <PM> Uninstall-Package NUnit -Project <TEST PROJECT NAME>
        Attempting to gather dependencies information for package 'NUnit.3.0.1' with respect to project '<TEST PROJECT NAME>', targeting '.NETFramework,Version=v4.5'
        Resolving actions to uninstall package 'NUnit.3.0.1'
        Resolved actions to uninstall package 'NUnit.3.0.1'
        Removed package 'NUnit.3.0.1' from 'packages.config'
        Successfully uninstalled 'NUnit.3.0.1' from <TEST PROJECT NAME>

1. **安装 NUnit 2.6.4** - 通过 `Install-Package` commandlet 安装 Nunit 2.6.4，如以下代码段所示：

        <PM> Install-Package NUnit -Version 2.6.4 -Project <TEST PROJECT NAME>
        Attempting to gather dependencies information for package 'NUnit.2.6.4' with respect to project '<TEST PROJECT NAME>', targeting '.NETFramework,Version=v4.5'
        Attempting to resolve dependencies for package 'NUnit.2.6.4' with DependencyBehavior 'Lowest'
        Resolving actions to install package 'NUnit.2.6.4'
        Resolved actions to install package 'NUnit.2.6.4'
        Adding package 'NUnit.2.6.4' to folder 'Z:\Desktop\DowngradeNunit\packages'
        Added package 'NUnit.2.6.4' to folder 'Z:\Desktop\DowngradeNunit\packages'
        Added package 'NUnit.2.6.4' to 'packages.config'
        Successfully installed 'NUnit 2.6.4' to <TEST PROJECT NAME>
    
## <a name="summary"></a>总结

本指南介绍如何使用包管理器控制台在 Visual Studio 2015或更高版本中将 NUnit 3.0 降级到 NUnit 2.6.4。

-----

## <a name="related-links"></a>相关链接

- [NUnit 2.6.4](http://nunit.org/index.php?p=docHome&r=2.6.4)
- [NUnit 2.6.4 NuGet 包](https://www.nuget.org/packages/NUnit/2.6.4)
