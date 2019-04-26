---
title: Objective C 入门
description: 本文档介绍了如何开始使用.NET 嵌入 Objective-c。 它讨论了要求、 安装.NET 嵌入从 NuGet 和受支持的平台。
ms.prod: xamarin
ms.assetid: 4ABC0247-B608-42D4-89CB-D2E598097142
author: lobrien
ms.author: laobri
ms.date: 11/14/2017
ms.openlocfilehash: 92b11db2ee566bcd9f3f8a3ee3771e163a47589b
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61040890"
---
# <a name="getting-started-with-objective-c"></a>Objective C 入门

这是适用于 OBJECTIVE-C 的其中介绍了所有支持平台的基础知识的入门页。

## <a name="requirements"></a>要求

若要使用.NET 嵌入 Objective C，您将需要运行的 Mac:

* macOS 10.12 (Sierra) 或更高版本
* Xcode 8.3.2 或更高版本
* [Mono 5.0](https://www.mono-project.com/download/)

你可以安装[Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/)来编辑和编译在C#代码。

> [!NOTE]
> * 早期版本的 macOS、 Xcode 和 Mono_可能_起作用，而是经过测试，不受支持
> * 在 Windows 中，可以进行代码生成，但就仅可以在其中安装 Xcode 的 Mac 计算机上编译该

## <a name="installing-net-embedding-from-nuget"></a>.NET 嵌入从 NuGet 安装

请按照这些[说明](~/tools/dotnet-embedding/get-started/install/install.md)来安装和配置.NET 嵌入为你的项目。

示例命令调用被列入[macOS](~/tools/dotnet-embedding/get-started/objective-c/macos.md)并[iOS](~/tools/dotnet-embedding/get-started/objective-c/ios.md)入门指南。

## <a name="platforms"></a>平台

OBJECTIVE-C 是一种语言，最常用于编写适用于 macOS、 iOS、 tvOS 和 watchOS 应用程序;.NET 嵌入支持所有这些平台。 每个平台使用暗示着[此处介绍的主要差异和这些](~/tools/dotnet-embedding/objective-c/platforms.md)。

### <a name="macos"></a>macOS

[创建 macOS 应用程序](~/tools/dotnet-embedding/get-started/objective-c/macos.md)是最简单的因为它不涉及任意数量的其他步骤，类似于设立标识、 provisining 配置文件、 模拟器和设备。 建议使用 macOS 文档之前适用于 iOS 的一个启动的。

### <a name="ios--tvos"></a>iOS / tvOS

请确保你已设置为之前尝试创建一个开发 iOS 应用程序使用.NET 嵌入。 [按照](~/tools/dotnet-embedding/get-started/objective-c/ios.md)假定您已成功地构建和部署 iOS 应用程序从您的计算机。

对 tvOS 的支持是类似于 iOS 的工作原理，只需在 Ide （Visual Studio 和 Xcode） 而不是 iOS 项目中使用 tvOS 项目。

> [!NOTE]
> 对 watchOS 将在未来版本中提供，并将非常类似于 iOS/tvOS 的支持。

## <a name="further-reading"></a>其他阅读材料

* [.NET 嵌入特定于 Objective C 的功能](~/tools/dotnet-embedding/objective-c/index.md)
* [适用于 OBJECTIVE-C 的最佳实践](~/tools/dotnet-embedding/objective-c/best-practices.md)
* [.NET 嵌入限制](~/tools/dotnet-embedding/limitations.md)
* [参与到开放源代码项目](https://github.com/mono/Embeddinator-4000/blob/master/Contributing.md)
* [错误代码和说明](~/tools/dotnet-embedding/errors.md)
* [目标平台](~/tools/dotnet-embedding/objective-c/platforms.md)

## <a name="related-links"></a>相关链接

- [天气示例 （iOS 和 macOS）](https://github.com/jamesmontemagno/embeddinator-weather)
