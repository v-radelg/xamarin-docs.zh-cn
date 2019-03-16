---
ms.assetid: 814857C5-D54E-469F-97ED-EE1CAA0156BB
title: 桌面应用程序的移植指南
description: 如何分离现有 Windows 窗体或 WPF 应用程序创建跨平台应用，以在 macOS、 iOS、 Android 以及 UWP/Windows 10 上运行的简单说明。
author: asb3993
ms.author: amburns
ms.date: 04/26/2017
ms.openlocfilehash: 4bf1dea170bd6b63209693963d54cc2e16163eea
ms.sourcegitcommit: 650458de1d362cd7de174cacef7838f0e74426f3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/15/2019
ms.locfileid: "58071094"
---
# <a name="desktop-app-porting-guidance"></a>桌面应用程序的移植指南

大多数应用程序代码可以分为以下几个方面之一：

* （例如用户界面代码。 windows 和按钮）
* 第三方控件 （例如。 图）
* （例如业务逻辑。 验证规则）
* 本地数据存储和访问
* Web 服务和远程数据访问

Windows 窗体和 WPF 编写的应用程序与C#（或 Visual Basic.NET） 极其大量的业务逻辑、 本地数据访问和 web 服务代码可以在平台之间共享。

## <a name="net-portability-analyzer"></a>.NET 可移植性分析器

Visual Studio 2017 和更高版本支持[.NET 可移植性分析器](https://docs.microsoft.com/dotnet/articles/standard/portability-analyzer)([下载适用于 Windows](https://marketplace.visualstudio.com/items?itemName=ConnieYau.NETPortabilityAnalyzer)) 的可以检查现有应用程序，并告知你可移植代码量，"按原样"对其他平台。 您可以了解有关它的详细信息从此[第 9 频道视频](https://channel9.msdn.com/Blogs/Seth-Juarez/A-Brief-Look-at-the-NET-Portability-Analyzer)。

此外，还有一个命令行工具可以从下载[GitHub 上的可移植性分析器](https://github.com/Microsoft/dotnet-apiport)，用于提供相同的报告。

## <a name="x-of-my-code-is-portable-what-next"></a>"x %的我的代码是可移植的。 下一步？"

希望分析器显示一个较大的代码部分是可移植的但那里肯定会为每个应用程序的某些部分的_不能_移到其他平台。

不同的代码块将可能的原因之一导致这些存储桶中，下面更详细地介绍了：

* 可重用的可移植代码
* 需要更改的代码
* 不是可移植并需要重新编写代码

### <a name="re-useable-portable-code"></a>可重用的可移植代码

在所有平台上针对可用的 Api 进行编写的.NET 代码时可以执行跨平台保持不变。 理想情况下，您可以将所有这些代码移到可移植类库、 共享库或.NET 标准库，然后在现有的应用程序中测试它。

然后可以将该共享的库添加到应用程序项目类型提供的其他平台 （如 Android、 iOS、 macOS）。

### <a name="code-that-requires-changes"></a>需要更改的代码

一些.NET Api 可能不能在所有平台上。 如果在代码中存在这些 Api，你将需要重新编写这些部分以使用跨平台 Api。

这样的示例包括使用反射 api，可在.NET 4.6 中，但在所有平台上不可用上。

已重新编写使用可移植的 Api 的代码后，您应能够共享库中的该代码打包并在现有的应用程序中对其进行测试。

### <a name="code-that-isnt-portable-and-requires-a-re-write"></a>不是可移植并需要重新编写代码

不可能是跨平台的代码的示例包括：

- **用户界面**– Windows 窗体或 WPF 屏幕不能例如 Android 或 iOS 中，在项目中使用。 用户界面将需要重新编写，使用此[控件比较](~/cross-platform/desktop/controls/index.md)作为参考。

- **特定于平台的存储**-依赖于特定于平台的技术 （如本地 SQL Server Express 数据库） 的代码。 你将需要重新编写此使用 （例如数据库引擎的 SQLite) 的跨平台替代方法。
某些文件系统操作可能还需要进行调整，因为 UWP 有略有不同的 Api，用于 Android 和 iOS （例如。 某些文件系统是区分大小写和其他人不是）。

- **第三方组件**– 检查是否在应用程序中的第三方组件在其他平台上可用。 一些非可视 NuGet 包，如可能可用，但其他人 （尤其是可视控件如图表或媒体播放器）

## <a name="tips-for-making-code-portable"></a>使代码可移植的提示

- **依赖关系注入**– 提供每个平台的不同实现和

- **分层方法**– 是否 MVVM、 MVC、 MVP、 或某些其他模式，可以帮助您与特定于平台的代码分开的可移植代码。

- **消息传送**– 可以在代码中使用消息传递取消耦合应用程序的不同部分之间的交互。
