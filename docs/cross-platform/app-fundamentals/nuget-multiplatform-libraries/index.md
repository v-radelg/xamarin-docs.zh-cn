---
title: NuGet 多平台库项目 (又称 Nugetizer 3000)
description: 本文档介绍如何使用 Nugetizer 3000 工具自动创建 NuGet 包，以跨平台共享代码。
ms.prod: xamarin
ms.assetid: F0A5A9BB-86CD-44C9-8EE8-74D1E5E74A30
author: conceptdev
ms.author: crdun
ms.date: 07/25/2018
ms.openlocfilehash: 6d3f7b316e397705ecb9bd404007dcd9ef5aa183
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61266776"
---
# <a name="nuget-multiplatform-library-projects-nugetizer-3000"></a>NuGet 多平台库项目 (Nugetizer 3000)

_自动创建 NuGet 包，以在使用 Nugetizer 3000 的平台间共享代码 ！_

可以自动创建要在使用的平台间共享代码的 NuGet 包_Nugetizer 3000_。 这样就可以创建现有库项目中或通过创建一个新的 NuGet 包**多平台库项目**。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

是使用 Visual Studio for Mac 包含 Nugetizer 3000&ndash;寻找**库 > Mulitplatform 库**项目中的类型**文件 > 新建**窗口：

[![](images/mulitplatform-library-sml.png "创建新的多平台库窗口")](images/mulitplatform-library.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

若要在 Visual Studio 中使用 Nugetizer 3000，请[下载并运行 VSIX 安装程序](http://bit.ly/nugetizer-2017)。

-----

## <a name="building-nuget-packages"></a>生成 NuGet 包

每次生成项目的配置后，输出完整的 NuGet 包，这可以用来与其他应用程序在内部共享代码或上传到[NuGet.org](https://www.nuget.org)。

有三种方案使用此功能：

- [现有库项目](existing-library.md)

  从 PCL （或.NET Standard） 的现有项目创建 NuGet 包。

- [创建新的多平台库项目](single-codebase.md)

  创建一个新库以共享通用代码通过 NuGet，使用 PCL 或.NET Standard。

- [创建新的特定于平台的库项目](platform-specific.md)

  创建新的库和 NuGet，包括特定于平台的代码适用于 iOS 和 Android，并使用共享项目来包含公共代码和特定于平台的项目以支持特定于 iOS 或 Android 功能。

请参阅[元数据指南](metadata.md)有关必须添加到任何 NuGet 包的必需和可选元数据的详细信息。

## <a name="further-nuget-information"></a>NuGet 的详细信息

详细了解[手动为 Xamarin 创建 Nuget](~/cross-platform/app-fundamentals/nuget-manual.md)和如何[应用程序中包括 NuGet 包](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough)。

Microsoft 的[NuGet 文档](https://docs.microsoft.com/nuget/)上包含更多详细的信息 **.nupkg**格式和使用 Visual Studio 中的 NuGet 包。

NuGet 包项目的设计讨论 （也称为 NuGetizer 3000) 位于[NuGet GitHub 存储库](https://github.com/NuGet/Home/wiki/NuGetizer-3000)。

## <a name="related-links"></a>相关链接

- [NuGetizer 3000 用例](https://github.com/NuGet/Home/wiki/NuGetizer-Core-Scenarios)
- [手动为 Xamarin 创建 NuGet 包](~/cross-platform/app-fundamentals/nuget-manual.md)
- [NuGet 文档](https://docs.microsoft.com/nuget/)
