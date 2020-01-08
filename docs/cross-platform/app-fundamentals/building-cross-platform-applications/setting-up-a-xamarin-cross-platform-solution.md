---
title: 第3部分-设置 Xamarin 跨平台解决方案
description: 本文档介绍如何在 Xamarin 中设置跨平台解决方案。 它讨论了各种代码共享策略，如共享项目和 .NET Standard。
ms.prod: xamarin
ms.assetid: 4139A6C2-D477-C563-C1AB-98CCD0D10A93
author: davidortinau
ms.author: daortin
ms.date: 03/27/2017
ms.openlocfilehash: e7cde22115830a845ed82aa907195521f36b6866
ms.sourcegitcommit: d8af612b6b3218fea396d2f180e92071c4d4bf92
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/04/2020
ms.locfileid: "75663268"
---
# <a name="part-3---setting-up-a-xamarin-cross-platform-solution"></a>第3部分-设置 Xamarin 跨平台解决方案

无论使用何种平台，Xamarin 项目都使用相同的解决方案文件格式（Visual Studio **.sln**文件格式）。 解决方案可以在开发环境中共享，即使无法加载单独的项目（例如 Visual Studio for Mac 中的 Windows 项目）。

创建新的跨平台应用程序时，第一步是创建一个空白解决方案。 本部分介绍接下来要执行的操作：设置用于生成跨平台移动应用的项目。

## <a name="sharing-code"></a>共享代码

有关如何跨平台实现代码共享的详细说明，请参阅[代码共享选项](~/cross-platform/app-fundamentals/code-sharing.md)文档。

### <a name="net-standard"></a>.NET Standard

[.NET Standard](~/cross-platform/app-fundamentals/net-standard.md)项目提供了跨平台共享代码的简单方法，可生成可跨 Windows、Xamarin 平台（IOS、Android、Mac）和 Linux 使用的程序集。
这是为 Xamarin 解决方案共享代码的建议方法。

### <a name="other-options"></a>其他选项

在过去，Xamarin 使用[可移植类库（pcl）](~/cross-platform/app-fundamentals/pcl.md)] 和[共享项目](~/cross-platform/app-fundamentals/shared-projects.md)。 对于新项目，不建议这样做;你应考虑迁移现有应用程序以使用 .NET Standard。

## <a name="populating-the-solution"></a>填充解决方案

无论使用哪种方法来共享代码，整体解决方案结构都应该实现鼓励代码共享的分层体系结构。
Xamarin 方法是将代码分组为两个项目类型：

- **核心（或 "共享"）项目**–在一个位置编写可重复使用的代码，以便在不同的平台之间共享。 使用封装原则尽可能隐藏实现细节。
- **平台特定的应用程序项目**-使用可重用的代码，并尽可能少耦合。 在此级别上添加了特定于平台的功能，这些功能基于核心项目中公开的组件构建。

### <a name="core-project"></a>核心项目

共享代码的核心项目应 .NET Standard 并且只有引用可在所有平台上使用的程序集– ie。常见框架命名空间，如 `System`、`System.Core` 和 `System.Xml`。

核心项目应尽可能多地实现非 UI 功能，其中可能包括以下各层：

- **数据层**–负责物理数据存储的代码，例如 [SQLite-网络](https://www.nuget.org/packages/sqlite-net-pcl/)或甚至是 XML 文件。 数据层类通常仅由数据访问层使用。
- **数据访问层**–定义一个 API，该 API 支持应用程序功能所需的数据操作，例如访问数据列表的方法、单个数据项，还可以创建、编辑和删除它们。
- **服务访问层**–用于向应用程序提供云服务的可选层。 包含访问远程网络资源（web 服务、图像下载等）并可能会缓存结果的代码。
- **业务层**-定义对特定于平台的应用程序公开功能的模型类和外观或管理器类。

### <a name="platform-specific-application-projects"></a>特定于平台的应用程序项目

特定于平台的项目必须引用绑定到每个平台 SDK （Xamarin、Xamarin、Xamarin 或 Windows）以及 .NET Standard 项目所需的程序集。

平台特定的项目应实现：

- **应用程序层**–平台特定功能以及业务层对象与用户界面之间的绑定/转换。
- **用户界面层**–屏幕，自定义用户界面控件，表示验证逻辑。

## <a name="project-references"></a>项目引用

项目引用反映项目的依赖项。 核心项目限制它们对常见程序集的引用，使代码易于共享。
特定于平台的应用程序项目引用 .NET Standard 项目，以及需要利用目标平台的任何其他特定于平台的程序集。

案例研究中提供了有关如何构造项目的具体示例。

## <a name="adding-files"></a>添加文件

### <a name="build-action"></a>生成操作

为某些文件类型设置正确的生成操作非常重要。 此列表显示了针对某些常见文件类型的生成操作：

- **所有C#文件**–生成操作：编译
- **Xamarin 中的映像 & Windows** –生成操作：内容
- **Xamarin 中的 XIB 和情节提要文件**–生成操作： InterfaceDefinition
- **Android 中的图像和 XML 布局**–生成操作： AndroidResource
- **Windows 项目中的 XAML 文件**–生成操作：页
- **Xamarin. FORMS XAML 文件**–生成操作： EmbeddedResource

通常，IDE 将检测文件类型并建议正确的生成操作。

### <a name="case-sensitivity"></a>区分大小写

最后，请记住，一些平台具有区分大小写的文件系统（例如
iOS 和 Android），因此请确保使用一致的文件命名标准，并确保在代码中使用的文件名与文件系统完全匹配。 这对于你在代码中引用的图像和其他资源尤其重要。
