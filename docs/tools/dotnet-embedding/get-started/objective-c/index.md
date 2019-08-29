---
title: 客观入门-C
description: 本文档介绍如何开始使用与目标 C 的 .NET 嵌入。 它讨论了要求、从 NuGet 安装 .NET 嵌入以及支持的平台。
ms.prod: xamarin
ms.assetid: 4ABC0247-B608-42D4-89CB-D2E598097142
author: lobrien
ms.author: laobri
ms.date: 11/14/2017
ms.openlocfilehash: 9d17027b1b84c7d742404c36fbdd4886de523914
ms.sourcegitcommit: 1dd7d09b60fcb1bf15ba54831ed3dd46aa5240cb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70119980"
---
# <a name="getting-started-with-objective-c"></a>客观入门-C

这是针对目标-C 的入门页面, 涵盖所有支持的平台的基础知识。

## <a name="requirements"></a>要求

若要将 .NET 嵌入与目标-C 一起使用, 需要运行以下 Mac:

- macOS 10.12 (塞拉利昂) 或更高版本
- Xcode 8.3.2 或更高版本
- [Mono 5。0](https://www.mono-project.com/download/)

您可以安装[Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/)来编辑和编译您C#的代码。

> [!NOTE]
> - 早期版本的 macOS、Xcode 和 Mono_可能_有效, 但未经测试且不受支持
> - 代码生成可以在 Windows 上完成, 但只能在安装了 Xcode 的 Mac 计算机上进行编译

## <a name="installing-net-embedding-from-nuget"></a>从 NuGet 安装 .NET 嵌入

按照以下[说明](~/tools/dotnet-embedding/get-started/install/install.md)安装和配置项目的 .net 嵌入。

[MacOS](~/tools/dotnet-embedding/get-started/objective-c/macos.md)和[iOS](~/tools/dotnet-embedding/get-started/objective-c/ios.md)入门指南中列出了一个示例命令调用。

## <a name="platforms"></a>平台

目标-C 是一种最常用于编写 macOS、iOS、tvOS 和 watchOS 应用程序的语言; .NET 嵌入支持所有这些平台。 使用每个平台意味着存在一些[关键差异, 这里介绍了这些差异](~/tools/dotnet-embedding/objective-c/platforms.md)。

### <a name="macos"></a>macOS

[创建 macOS 应用程序](~/tools/dotnet-embedding/get-started/objective-c/macos.md)最为简单, 因为它不涉及多个附加步骤, 如设置标识、provisining 配置文件、模拟器和设备。 建议你首先了解适用于 iOS 的 macOS 文档。

### <a name="ios--tvos"></a>iOS/tvOS

请确保已设置为开发 iOS 应用程序, 然后再尝试使用 .NET 嵌入进行创建。 [以下说明](~/tools/dotnet-embedding/get-started/objective-c/ios.md)假定你已成功从计算机生成并部署了 iOS 应用程序。

对 tvOS 的支持与 iOS 的工作方式类似, 只是在 Ide 中使用 tvOS 项目 (Visual Studio 和 Xcode), 而不是使用 iOS 项目。

> [!NOTE]
> 将来的版本将提供对 watchOS 的支持, 并将非常类似于 iOS/tvOS。

## <a name="further-reading"></a>其他阅读材料

- [特定于目标的 .NET 嵌入功能-C](~/tools/dotnet-embedding/objective-c/index.md)
- [针对目标的最佳实践-C](~/tools/dotnet-embedding/objective-c/best-practices.md)
- [.NET 嵌入限制](~/tools/dotnet-embedding/limitations.md)
- [参与开源项目](https://github.com/mono/Embeddinator-4000/blob/master/Contributing.md)
- [错误代码和描述](~/tools/dotnet-embedding/errors.md)
- [目标平台](~/tools/dotnet-embedding/objective-c/platforms.md)

## <a name="related-links"></a>相关链接

- [天气示例 (iOS & macOS)](https://github.com/jamesmontemagno/embeddinator-weather)
