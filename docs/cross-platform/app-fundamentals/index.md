---
title: 在多个平台上共享代码
description: 本文档链接到各种指南，这些指南介绍了用于共享代码的技术（包括可移植类库、共享项目、.NET Standard 和 NuGet）。
ms.prod: xamarin
ms.assetid: 7D179ACF-09A6-46EE-B49D-E27AB5F09CD4
author: davidortinau
ms.author: daortin
ms.date: 07/18/2018
ms.openlocfilehash: a91fba3cd1fba3bcf2317e8f9cb25631c62491cb
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73016824"
---
# <a name="sharing-code-on-multiple-platforms"></a>在多个平台上共享代码

这些文章介绍了可用于跨平台（包括 Windows、Android、iOS 等）共享代码的不同选项。

## <a name="code-sharing-overviewcode-sharingmd"></a>[代码共享概述](code-sharing.md)

了解适用于 Xamarin 项目的不同代码共享选项，包括 .NET Standard 库和共享项目。 还支持可移植类库，但它们被视为已弃用，以支持 .NET Standard。

## <a name="net-standardcross-platformapp-fundamentalsnet-standardmd"></a>[.NET Standard](~/cross-platform/app-fundamentals/net-standard.md)

.NET Standard 是在平台间共享代码的首选选项。 代码基于特定版本（2.0 提供与现有 .NET Framework 代码的最佳 API 兼容性），并可由支持该级别或更高级别的其他项目使用。 Visual Studio 2019 和 Visual Studio 2019 for Mac 支持 .NET Standard 项目。

## <a name="shared-projectscross-platformapp-fundamentalsshared-projectsmd"></a>[共享项目](~/cross-platform/app-fundamentals/shared-projects.md)

使用共享项目，可以编写由多个不同的应用程序项目引用的通用代码。 此代码编译为每个引用项目的一部分，并且可以包含编译器指令以帮助在共享代码库中包含特定于平台的功能。 本文讨论了共享项目的工作原理以及如何在 Xamarin 项目中创建和使用它们。

## <a name="portable-class-librariescross-platformapp-fundamentalspclmd"></a>[可移植类库](~/cross-platform/app-fundamentals/pcl.md)

可移植类库项目使你可以构建和分发包含共享代码的程序集，以便在多个平台上运行。 若要创建可移植类库（或 "PCL"），请首先选择要面向的平台，然后针对为这些平台定义的配置文件中提供的 .NET Framework 的子集编写代码。 在最新版本的 Visual Studio 中，Pcl 被视为已弃用;鼓励开发人员改用 2.0 .NET Standard。

## <a name="nuget-projects-multiplatform-libraries-for-code-sharingcross-platformapp-fundamentalsnuget-multiplatform-librariesindexmd"></a>[NuGet 项目：用于代码共享的多平台库](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/index.md)

可以从 PCL 或 .NET standard 项目自动生成 NuGet 包;使用单独的 NuGet 项目类型，可以将共享项目打包到 "bait" 和 "交换机" NuGet 包中。 本部分介绍如何为每个代码共享方案创建 NuGet 包。

## <a name="manually-creating-nuget-packages-for-xamarincross-platformapp-fundamentalsnuget-manualmd"></a>[手动为 Xamarin 创建 NuGet 包](~/cross-platform/app-fundamentals/nuget-manual.md)

有关创建适用于 Xamarin 平台的 NuGet 包的提示。

## <a name="use-cc-libraries-in-cross-platform-xamarin-projectscross-platformcppindexmd"></a>[在跨平台C++ Xamarin 项目中使用 C/库](~/cross-platform/cpp/index.md)

利用此方法，你可以分离 C/C++库的演变、NuGet 中C#的绑定和 Xamarin 应用程序。 功能由本机平台 C/C++库提供，但所有特定于平台的代码都独立于最终的 Xamarin 应用程序，因此可以实现最高性能，而不会重复代码。 
