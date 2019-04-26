---
title: 使用.NET 标准库来共享代码
description: 本文档介绍如何使用.NET 标准库来共享代码。 它讨论了创建.NET Standard 库、 编辑其设置和应用程序中使用。
ms.prod: xamarin
ms.assetid: 8C30F8D3-1920-453E-9E8B-D40696736FF2
author: conceptdev
ms.author: crdun
ms.custom: video
ms.date: 07/18/2018
ms.openlocfilehash: d07b248b36feee909db9c863eb17f1a900f58e60
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61191279"
---
# <a name="net-standard-library-code-sharing"></a>.NET 标准库代码共享

.NET standard 库的所有.NET 平台，包括 Xamarin 和.NET Core 具有一致的 API。 创建一个.NET 标准库，并从任何支持.NET Standard 平台的运行时使用它。 请参阅[此图表](https://docs.microsoft.com/dotnet/standard/net-standard#net-implementation-support)有关受支持的平台的详细信息。

.NET Standard 版本 1.0 到 1.6 提供的以增量方式更大的.NET Framework 的子集，而.NET Standard 2.0 的 Xamarin 应用程序和移植现有的可移植类库提供最佳的支持级别。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

## <a name="visual-studio-for-mac"></a>Visual Studio for Mac

本部分逐步讲解如何创建和使用.NET 标准库使用 Visual Studio for mac。

### <a name="creating-a-net-standard-library"></a>创建.NET 标准库

可以将.NET 标准库添加到你的解决方案通过以下步骤：

1. 在中**添加新项目**对话框中，选择 **.NET Core**类别，然后选择 **.NET 标准库**:

    ![创建.NET Standard 库](net-standard-images/vsm01-m157.png "创建新的.NET Standard 库")

2. 在下一个屏幕上选择的目标框架- **.NET Standard 2.0**建议：

    [![选择.NET Standard 2.0](net-standard-images/vsm01a-m157-sml.png)](net-standard-images/vsm01a-m157.png#lightbox)

3. 在最后一个屏幕上，键入项目名称，然后单击**创建**。

4. .NET Standard 库项目将显示在解决方案资源管理器中所示。 依赖项节点将指示库使用[NETStandard.Library](https://www.nuget.org/packages/NETStandard.Library/)。

    ![在解决方案中的依赖项节点指示.NET Standard](net-standard-images/vsm02-m157.png)

#### <a name="editing-net-standard-library-settings"></a>编辑.NET 标准库设置

可以查看和更改通过右键单击项目并选择.NET 标准库设置`Options`此屏幕截图中所示：

![编辑项目选项中的.NET Standard 目标框架](net-standard-images/vsm03-m157.png "编辑项目选项中的.NET 标准的目标框架版本")

内可以更改你的版本`netstandard`通过更改`Target Framework`下拉列表值。

**此外：** 您可以编辑`.csproj`直接以更改此值。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

## <a name="visual-studio-2017-windows"></a>Visual Studio 2017 (Windows)

本部分将指导完成如何创建和使用.NET 标准库使用 Visual Studio。

### <a name="creating-a-net-standard-library"></a>创建.NET Standard 库

.NET 标准库添加到你的解决方案是一个相当直截了当。

1. 在中**新的项目**对话框中，选择 **.NET Standard**类别，然后选择**类库 (.NET Standard)**。

    ![创建新的.NET 标准类库](net-standard-images/vs01-w157.png "创建新.NET Standard 类库")

2. .NET Standard 库项目将显示在解决方案资源管理器中所示。 依赖项节点将指示库使用[NETStandard.Library](https://www.nuget.org/packages/NETStandard.Library/)。

    ![项目文件夹中的 NETStandard.Library](net-standard-images/vs02-w157.png "解决方案中的.NET Standard 项目")

### <a name="editing-net-standard-library-settings"></a>编辑.NET Standard 库设置

.NET 标准库设置都可以查看和更改通过右键单击项目并选择**属性**此屏幕截图中所示：

![编辑项目属性中的.NET 标准的目标框架](net-standard-images/vs03-w157.png "引用方式与其他项目相同的.NET Standard 库")

**此外：** 您可以编辑`.csproj`直接以编辑`TargetFramework`元素，然后更改的是哪个版本作为目标 （例如。 `<TargetFramework>netstandard2.0</TargetFramework>`）格式模式中出现的位置生成。

### <a name="using-a-net-standard-library-project"></a>使用.NET Standard 库项目

一旦创建.NET Standard 库后，你可以与您通常将引用添加相同的方式添加对它从任何兼容的应用程序或库项目的引用。 在 Visual Studio 中，右键单击引用节点并选择**添加引用...** 然后切换到**项目 > 解决方案**选项卡上所示：

![引用.NET 标准库](net-standard-images/vs04.png "在 Visual Studio 中，右键单击引用节点并选择添加引用...然后切换到解决方案项目选项卡所示")

-----

## <a name="net-standard-and-xamarinforms-for-the-net-developer-video"></a>.NET standard 和 Xamarin.Forms 为.NET 开发人员 （视频）

> [!Video https://channel9.msdn.com/Shows/XamarinShow/NET-Standard-and-XamarinForms-for-the-NET-Developer/player]

## <a name="related-links"></a>相关链接

* [.NET standard](https://docs.microsoft.com/dotnet/standard/net-standard) -详细信息和与 PCL 之间的比较。
