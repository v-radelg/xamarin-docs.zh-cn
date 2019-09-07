---
title: 第3部分-设置 Xamarin 跨平台解决方案
description: 本文档介绍如何在 Xamarin 中设置跨平台解决方案。 它讨论了各种代码共享策略，如共享项目和 .NET Standard。
ms.prod: xamarin
ms.assetid: 4139A6C2-D477-C563-C1AB-98CCD0D10A93
author: conceptdev
ms.author: crdun
ms.date: 03/27/2017
ms.openlocfilehash: acec74585487e9f0a0a13a80c5da49a187a4042f
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70758143"
---
# <a name="part-3---setting-up-a-xamarin-cross-platform-solution"></a>第3部分-设置 Xamarin 跨平台解决方案

无论使用何种平台，Xamarin 项目都使用相同的解决方案文件格式（Visual Studio **.sln**文件格式）。 解决方案可以在开发环境中共享，即使无法加载单独的项目（例如 Visual Studio for Mac 中的 Windows 项目）。

创建新的跨平台应用程序时，第一步是创建一个空白解决方案。 本部分介绍接下来要执行的操作：设置用于生成跨平台移动应用的项目。

 <a name="Sharing_Code" />

## <a name="sharing-code"></a>共享代码

有关如何跨平台实现代码共享的详细说明，请参阅[代码共享选项](~/cross-platform/app-fundamentals/code-sharing.md)文档。

 <a name="Shared_Asset_Projects" />

### <a name="shared-projects"></a>共享项目

共享代码文件的最简单方法是使用[共享项目](~/cross-platform/app-fundamentals/shared-projects.md)。

此方法允许你在不同的平台项目之间共享相同的代码，并使用编译器指令包含不同的特定于平台的代码路径。

 <a name="Portable_Class_Libraries" />

### <a name="portable-class-libraries-pcl"></a>可移植类库 (PCL)

在过去，.NET 项目文件（和生成的程序集）的目标是特定的框架版本。 这会阻止项目或程序集被不同框架共享。

可移植类库（PCL）是一种特殊类型的项目，可在不同的 CLI 平台（如 Xamarin 和 Xamarin）以及 WPF、通用 Windows 平台和 Xbox 之间使用。 该库只能利用整个 .NET framework 的子集，受限于目标平台的限制。

可以阅读有关 Xamarin[对可移植类库的支持](~/cross-platform/app-fundamentals/pcl.md)的详细信息，并按照其中的说明查看[TaskyPortable 示例](https://github.com/xamarin/mobile-samples/tree/master/TaskyPortable)的工作方式。

### <a name="net-standard"></a>.NET Standard

[.NET Standard](~/cross-platform/app-fundamentals/net-standard.md)项目是在2016中引入的，它提供了跨平台共享代码的简单方法，可生成可跨 Windows、Xamarin 平台（IOS、Android、Mac）和 Linux 使用的程序集。

.NET Standard 库的创建和使用方式类似于 Pcl，只不过每个版本中提供的 Api （1.0 到1.6）都可以更轻松地发现，每个版本都与较低版本号向后兼容。

 <a name="Populating_the_Solution" />

## <a name="populating-the-solution"></a>填充解决方案

无论使用哪种方法来共享代码，整体解决方案结构都应该实现鼓励代码共享的分层体系结构。
Xamarin 方法是将代码分组为两个项目类型：

- **核心项目**–在一个位置编写可重复使用的代码，以便在不同的平台之间共享。 使用封装原则尽可能隐藏实现细节。
- **平台特定的应用程序项目**-使用可重用的代码，并尽可能少耦合。 在此级别上添加了特定于平台的功能，这些功能基于核心项目中公开的组件构建。

 <a name="Core_Project" />

### <a name="core-project"></a>核心项目

共享代码项目只应引用可在所有平台上使用的程序集– ie。常见框架命名空间， `System`如`System.Core` 、 `System.Xml`和。

共享项目应尽可能多地实现非 UI 功能，其中可能包括以下各层：

- **数据层**–负责物理数据存储的代码，例如  [SQLite-NET](https://github.com/praeclarum/sqlite-net)，一种替代数据库，如[Realm.io](https://realm.io/products/realm-mobile-database/)甚至 XML 文件。 数据层类通常仅由数据访问层使用。
- **数据访问层**–定义一个 API，该 API 支持应用程序功能所需的数据操作，例如访问数据列表的方法、单个数据项，还可以创建、编辑和删除它们。
- **服务访问层**–用于向应用程序提供云服务的可选层。 包含访问远程网络资源（web 服务、图像下载等）并可能会缓存结果的代码。
- **业务层**-定义对特定于平台的应用程序公开功能的模型类和外观或管理器类。

 <a name="Platform-Specific_Application_Projects" />

### <a name="platform-specific-application-projects"></a>特定于平台的应用程序项目

特定于平台的项目必须引用绑定到每个平台 SDK （Xamarin、Xamarin、Xamarin 或 Windows）以及核心共享代码项目所需的程序集。

平台特定的项目应实现：

- **应用程序层**–平台特定功能以及业务层对象与用户界面之间的绑定/转换。
- **用户界面层**–屏幕，自定义用户界面控件，表示验证逻辑。

<a name="Example" />

### <a name="example"></a>示例

此图说明了应用程序体系结构：

 [![](setting-up-a-xamarin-cross-platform-solution-images/conceptualarchitecture.png "此图说明了应用程序体系结构")](setting-up-a-xamarin-cross-platform-solution-images/conceptualarchitecture.png#lightbox)

此屏幕截图显示了使用共享核心项目、iOS 和 Android 应用程序项目设置的解决方案。 共享项目包含与每个体系结构层（业务、服务、数据和数据访问代码）相关的代码：

 ![](setting-up-a-xamarin-cross-platform-solution-images/core-solution-example.png "共享项目包含与每个体系结构层相关的代码（业务、服务、数据和数据访问代码）")

 <a name="Project_References" />

## <a name="project-references"></a>项目引用

项目引用反映项目的依赖项。 核心项目限制它们对常见程序集的引用，使代码易于共享。
特定于平台的应用程序项目引用共享代码，以及需要利用目标平台的任何其他特定于平台的程序集。

应用程序将项目每个引用共享项目，并包含向用户提供功能所需的用户界面代码，如以下屏幕截图所示：

![](setting-up-a-xamarin-cross-platform-solution-images/solution-android.png "应用程序项目共享项目中的每个引用") ![](setting-up-a-xamarin-cross-platform-solution-images/solution-ios.png "应用程序项目每个引用共享项目中")

案例研究中提供了有关如何构造项目的具体示例。

 <a name="Adding_Files" />

## <a name="adding-files"></a>添加文件

 <a name="Build_Action" />

### <a name="build-action"></a>生成操作

为某些文件类型设置正确的生成操作非常重要。 此列表显示了针对某些常见文件类型的生成操作：

- **所有C#文件**–生成操作：Compile
- **Xamarin 中的映像 & Windows** –生成操作：内容
- **Xamarin 中的 XIB 和情节提要文件**–生成操作：InterfaceDefinition
- **Android 中的图像和 main.axml 布局**–生成操作：AndroidResource
- **Windows 项目中的 XAML 文件**–生成操作：页面
- **Xamarin .xaml XAML 文件**–生成操作：EmbeddedResource

通常，IDE 将检测文件类型并建议正确的生成操作。

 <a name="Case_Sensitivity" />

### <a name="case-sensitivity"></a>区分大小写

最后，请记住，一些平台具有区分大小写的文件系统（例如
iOS 和 Android），因此请确保使用一致的文件命名标准，并确保在代码中使用的文件名与文件系统完全匹配。 这对于你在代码中引用的图像和其他资源尤其重要。
