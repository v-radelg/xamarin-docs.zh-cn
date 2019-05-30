---
title: 第 3 部分-建立 Xamarin 跨平台解决方案
description: 本文档介绍如何设置在 Xamarin 中的跨平台解决方案。 它讨论了各种代码共享策略如共享的项目和.NET Standard。
ms.prod: xamarin
ms.assetid: 4139A6C2-D477-C563-C1AB-98CCD0D10A93
author: asb3993
ms.author: amburns
ms.date: 03/27/2017
ms.openlocfilehash: d20275bab4e4ce90f902a5e72321701d94b1d416
ms.sourcegitcommit: 4a1520dee7759f8355ea65c8bb3d1bac8ba58122
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/29/2019
ms.locfileid: "66354071"
---
# <a name="part-3---setting-up-a-xamarin-cross-platform-solution"></a>第 3 部分-建立 Xamarin 跨平台解决方案

所有 Xamarin 项目而不考虑要使用什么平台，都使用相同的解决方案文件格式 (在 Visual Studio **.sln**文件格式)。 即使在单独的项目不能加载 （如 Visual Studio for Mac 中的 Windows 项目） 时，可以在开发环境之间共享解决方案。



当创建新的跨平台应用程序，第一步是创建一个空白解决方案。 本部分介绍会发生什么情况下一步： 设置有关构建跨平台移动应用项目。

 <a name="Sharing_Code" />


## <a name="sharing-code"></a>共享代码

请参阅[代码共享选项](~/cross-platform/app-fundamentals/code-sharing.md)有关如何实现跨平台的代码共享的详细说明的文档。

 <a name="Shared_Asset_Projects" />


### <a name="shared-projects"></a>共享项目

共享代码文件的最简单方法使用[共享项目](~/cross-platform/app-fundamentals/shared-projects.md)。

此方法，可跨不同平台项目中，共享相同的代码并使用编译器指令将包含不同的、 特定于平台的代码路径。

 <a name="Portable_Class_Libraries" />


### <a name="portable-class-libraries-pcl"></a>可移植类库 (PCL)

从历史上看到特定的框架版本已针对.NET 项目文件 （和生成的程序集）。 这可以防止项目或共享的不同框架的程序集。

可移植类库 (PCL) 是项目的一种特殊的可跨不同 CLI 平台 Xamarin.iOS 和 Xamarin.Android，以及 WPF、 通用 Windows 平台和 Xbox 等使用。 库仅可以利用完整的.NET framework 中，目标平台受限制的子集。

你可以阅读更多有关 Xamarin 的[支持可移植类库](~/cross-platform/app-fundamentals/pcl.md)并按照该处的说明以查看如何[TaskyPortable 示例](https://github.com/xamarin/mobile-samples/tree/master/TaskyPortable)工作原理。


### <a name="net-standard"></a>.NET Standard

在 2016 中，引入[.NET Standard](~/cross-platform/app-fundamentals/net-standard.md)项目还提供生成可以在 Windows 中使用的程序集的平台、 Xamarin 平台 (iOS、 Android、 Mac) 和 Linux 之间共享代码的简单方法。

可以创建和使用 Pcl，如只是在每个版本 （从 1.0 到 1.6 版) 中提供的 Api 更轻松地发现，并且每个版本向后兼容.NET 标准库版本编号较小。



 <a name="Populating_the_Solution" />


## <a name="populating-the-solution"></a>填充解决方案

无论哪种方法使用共享代码，整体解决方案结构应实现一个分层体系结构，鼓励代码共享。
Xamarin 方法是组代码分离到两个项目类型：

-   **核心项目**– 在一个位置，若要在不同平台之间共享中编写可重用代码。 使用封装原则以隐藏实现细节，只要有可能。
-   **特定于平台的应用程序项目**– 使用尽可能少耦合与在可重用代码。 在此级别，基于在 Core 项目中公开的组件添加特定于平台的功能。


 <a name="Core_Project" />


### <a name="core-project"></a>Core 项目

共享的代码项目应只能引用可跨所有平台 – ie 的程序集。常见 framework 命名空间喜欢`System`，`System.Core`和`System.Xml`。

共享的项目应实现尽可能非 UI 功能，如有可能，它可能包含以下层：

-   **数据层**– 例如处理的物理数据存储的代码。  [SQLite NET](https://github.com/praeclarum/sqlite-net)，备用数据库喜欢[Realm.io](https://realm.io/products/realm-mobile-database/)或甚至是 XML 文件。 数据层类为通常仅由数据访问层。
-   **数据访问层**– 定义支持应用程序的功能的所需的数据操作，如方法来访问数据，各个数据项的列表，同时还创建、 编辑和删除它们的 API。
-   **服务访问层**– 一个可选层来提供云服务应用程序。 包含访问远程网络资源 （web 服务、 图像下载，等等） 的代码，可能缓存的结果。
-   **业务层**– 模型类和公开的特定于平台的应用程序的外观或管理器类的定义。


 <a name="Platform-Specific_Application_Projects" />


### <a name="platform-specific-application-projects"></a>特定于平台的应用程序项目

特定于平台的项目必须引用绑定到每个平台的 SDK （Xamarin.iOS、 Xamarin.Android、 Xamarin.Mac、 或 Windows） Core 共享的代码项目以及所需的程序集。

应实现特定于平台的项目：

-   **应用程序层**– 平台特定功能和业务层对象和用户界面之间的绑定/转换。
-   **用户界面层**– 屏幕、 自定义用户界面控件、 演示文稿的验证逻辑。


<a name="Example" />


### <a name="example"></a>示例

应用程序体系结构是在此图中所示：

 [ ![](setting-up-a-xamarin-cross-platform-solution-images/conceptualarchitecture.png "此图说明了应用程序体系结构")](setting-up-a-xamarin-cross-platform-solution-images/conceptualarchitecture.png#lightbox)

此屏幕截图显示了具有共享的核心项目、 iOS 和 Android 应用程序项目的解决方案设置。 对共享项目包含与每个体系结构层 （业务、 服务、 数据和数据访问代码） 相关的代码：

 ![](setting-up-a-xamarin-cross-platform-solution-images/core-solution-example.png "对共享项目包含与每个体系结构层 （业务、 服务、 数据和数据访问代码） 相关的代码")


 <a name="Project_References" />


## <a name="project-references"></a>项目引用

项目引用反映项目的依赖项。 核心项目限制其对公共程序集的引用，因此可以方便地共享代码。
特定于平台的应用程序项目引用共享代码，以及他们需要利用目标平台的任何其他特定于平台的程序集。

应用程序项目每个引用共享项目中，并包含对用户来说，存在功能所需的用户界面代码，如以下屏幕截图中所示：

![](setting-up-a-xamarin-cross-platform-solution-images/solution-android.png "应用程序项目共享项目中的每个引用") ![](setting-up-a-xamarin-cross-platform-solution-images/solution-ios.png "应用程序项目每个引用共享项目中")


案例研究中给出了应如何结构化项目的特定示例。

 <a name="Adding_Files" />


## <a name="adding-files"></a>添加文件

 <a name="Build_Action" />


### <a name="build-action"></a>生成操作

请务必设置正确的生成操作的某些文件类型。 此列表显示了一些常见的文件类型的生成操作：

-  **所有C#文件**– 生成操作：Compile
-   **Xamarin.iOS 和 Windows 中的映像**– 生成操作：内容
-   **XIB 和情节提要文件在 Xamarin.iOS** – 生成操作：InterfaceDefinition
-   **映像和在 Android 中的 AXML 布局**– 生成操作：AndroidResource
-  **Windows 项目中的 XAML 文件**– 生成操作：页面
-  **Xamarin.Forms XAML 文件**– 生成操作：EmbeddedResource


通常在 IDE 将检测的文件类型，并提供正确的生成操作建议。

 <a name="Case_Sensitivity" />


### <a name="case-sensitivity"></a>区分大小写

最后，请记住，某些平台具有区分大小写的文件系统 （例如。
iOS 和 Android) 因此请确保使用一致的文件命名标准，并确保在代码中使用的文件名称与文件系统完全匹配。 这一点尤其重要的图像和代码中引用的其他资源。
