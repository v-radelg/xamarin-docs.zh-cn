---
title: 共享代码概述
description: 本文档进行比较跨平台项目之间共享代码的不同的方法：共享项目、 可移植类库和.NET 标准，包括权益和各自的优缺点。
ms.prod: xamarin
ms.assetid: B73675D2-09A3-14C1-E41E-20352B819B53
author: conceptdev
ms.author: crdun
ms.date: 08/06/2018
ms.openlocfilehash: 98b5786ae4f071b4d8e8f854561db97aee037fdc
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61227978"
---
# <a name="sharing-code-overview"></a>共享代码概述

_本文档将跨平台项目之间共享代码的不同方法进行比较：.NET Standard、 共享项目和可移植类库，包括权益和各自的优缺点。_

有三种跨平台应用程序之间共享代码的方法：

- [**.NET 标准库**](#Net_Standard) –.NET Standard 项目中可以实现跨多个平台共享代码，并且可以访问大量的.NET Api （具体取决于版本）。 .NET standard 1.0-1.6 实现逐渐变大组 Api，而.NET Standard 2.0 提供了.NET BCL （包括 Xamarin 应用中提供的.NET Api） 的最佳覆盖范围。
- [**共享的项目**](#Shared_Projects) – 使用共享资产项目类型可组织源代码，并使用`#if`编译器指令根据需要管理特定于平台的要求。
- [**可移植类库**](#Portable_Class_Libraries) （已弃用） – 可移植类库 (Pcl) 可以面向多个平台的公共 API 图面，并使用接口来提供特定于平台的功能。 在最新版本的 Visual Studio 中已弃用 Pcl&ndash;改为使用.NET Standard。

代码共享策略的目标是支持多个平台可以在其中使用单个代码库此图中所示的体系结构。

 ![共享代码应用程序体系结构](code-sharing-images/conceptualarchitecture.png "共享代码应用程序体系结构")

本文比较了可用来帮助你选择你的应用程序的正确的项目类型的方法。

<a name="Net_Standard" />

## <a name="net-standard-libraries"></a>.NET 标准库

[.NET standard](~/cross-platform/app-fundamentals/net-standard.md)库提供了一组定义完善的可以在不同的项目类型，包括 Xamarin.Android 和 Xamarin.iOS 等跨平台项目中引用的基类库。 .NET standard 2.0 建议用于使用现有的.NET Framework 代码的最大兼容性。

![.NET standard 的关系图](code-sharing-images/netstandard.png ".NET Standard 的关系图")

### <a name="benefits"></a>优点

- 可以在多个项目间共享代码。
- 重构操作始终更新所有受影响的引用。
- 提供比 PCL 配置文件可查看大图面区域的.NET 基类库 (BCL)。 具体而言，.NET Standard 2.0 具有几乎相同 API 外围应用与.NET Framework 和建议的新的应用程序和移植现有 Pcl。

### <a name="disadvantages"></a>缺点

- 不能使用编译器指令，如`#if __IOS__`。

### <a name="remarks"></a>备注

.NET standard 是[类似于 PCL](https://docs.microsoft.com/dotnet/standard/net-standard#comparison-to-portable-class-libraries)，但使用平台支持和更多的 BCL 中的类的简单模型。

<a name="Shared_Projects" />

## <a name="shared-projects"></a>共享项目

[共享的项目](~/cross-platform/app-fundamentals/shared-projects.md)包含代码文件和引用它们的任何项目中包含的资产。 共享项目不会生成自己的已编译的输出。

此屏幕截图显示使用 （适用于 Android、 iOS 和 Windows），包含三个应用程序项目的解决方案文件**共享**包含常见 C# 源代码文件的项目：

![共享项目解决方案](code-sharing-images/sharedsolution.png "共享项目解决方案")

概念体系结构是在下图中，其中每个项目包括所有共享的源代码文件中所示：

![共享项目关系图](code-sharing-images/sharedassetproject.png "共享项目关系图")

### <a name="example"></a>示例

支持 iOS、 Android 和 Windows 的跨平台应用程序需要为每个平台的应用程序项目。 公共代码驻留在共享项目中。

一个示例解决方案将包含以下文件夹和项目 （项目名称为已选定表现力，你的项目无需遵守以下命名准则）：

- **共享**– 共享项目，其中包含所有项目通用的代码。
- **AppAndroid** – Xamarin.Android 应用程序项目。
- **AppiOS** – Xamarin.iOS 应用程序项目。
- **AppWindows** – Windows 应用程序项目。

在这种方式中的三个应用程序项目共享相同的源代码 （C# 中的文件共享）。 将在所有三个项目之间共享到共享代码的任何编辑。

### <a name="benefits"></a>优点

- 可以在多个项目间共享代码。
- 可以使用编译器指令 （例如在平台上基于分支共享的代码。 使用`#if __ANDROID__`，如中所述[构建跨平台应用程序](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md)文档)。
- 应用程序项目可以包含可以利用共享的代码的特定于平台的引用 (例如，使用`Community.CsharpSqlite.WP7`Tasky 示例为 Windows Phone 中)。

### <a name="disadvantages"></a>缺点

- 重构会影响 'inactive' 编译器指令内的代码不会更新这些指令内的代码。
- 与大多数其他项目类型，不同的是共享的项目具有未设置输出程序集。 在编译期间，文件被视为引用项目的一部分并编译到该程序集。 如果你想要共享你的代码作为程序集然后.NET Standard 或可移植类库是更好的解决方案。

<a name="Shared_Remarks" />

### <a name="remarks"></a>备注

应用程序开发人员编写代码的一个不错的解决方案仅适用于共享其应用程序中 （且不分发给其他开发人员）。

<a name="Portable_Class_Libraries" />

## <a name="portable-class-libraries"></a>可移植类库

> [!TIP]
> .NET standard 2.0 库，建议通过可移植类库。

可移植类库是[讨论了详细的介绍](~/cross-platform/app-fundamentals/pcl.md)。

![可移植库关系图](code-sharing-images/portableclasslibrary.png "可移植库关系图")

### <a name="benefits"></a>优点

- 可以在多个项目间共享代码。
- 重构操作始终更新所有受影响的引用。

### <a name="disadvantages"></a>缺点

- 在 Visual Studio 的最新版本中弃用，而被建议.NET Standard 库。 请参阅此[区别的说明](https://docs.microsoft.com/dotnet/standard/net-standard#comparison-to-portable-class-libraries)之间 PCL 和.NET Standard。
- 不能使用编译器指令。
- 只有.NET framework 的子集可用来使用，取决于所选配置文件 (请参阅[简介 PCL](~/cross-platform/app-fundamentals/pcl.md)的详细信息)。

### <a name="remarks"></a>备注

PCL 模板被视为最新版本的 Visual Studio 中不推荐使用。

## <a name="summary"></a>总结

代码共享选择的策略将根据你面向的平台。 选择最适合你的项目的方法。

.NET standard 是构建可共享代码库 （尤其在 NuGet 上发布） 的最佳选择。 共享的项目适用于应用程序开发人员计划在其跨平台应用中使用大量特定于平台的功能。

虽然 PCL 项目将继续支持在 Visual Studio 中，.NET 标准建议对新项目。

## <a name="related-links"></a>相关链接

- [生成跨平台应用程序 （主要文档）](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md)
- [可移植类库](~/cross-platform/app-fundamentals/pcl.md)
- [共享项目](~/cross-platform/app-fundamentals/shared-projects.md)
- [.NET Standard](~/cross-platform/app-fundamentals/net-standard.md)
- [案例研究：Tasky](~/cross-platform/app-fundamentals/building-cross-platform-applications/case-study-tasky.md)
- [Tasky 示例 (github)](https://github.com/xamarin/mobile-samples/tree/master/Tasky)
- [Tasky 示例使用 PCL (github)](https://github.com/xamarin/mobile-samples/tree/master/TaskyPortable)
