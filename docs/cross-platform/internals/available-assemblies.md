---
title: 可用程序集
description: 本文档列出了可在 Xamarin、Xamarin 和 Xamarin 中使用的程序集。 它还链接到有关 .NET Standard 库和可移植类库的文档。
ms.prod: xamarin
ms.assetid: AEF4ED0E-391F-4FA4-9F18-842BC24C272D
author: davidortinau
ms.author: daortin
ms.date: 03/13/2018
ms.openlocfilehash: 238011b4762f2d394629e75fbde476e618219df2
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73016350"
---
# <a name="available-assemblies"></a>可用程序集

Xamarin、Xamarin 和 Xamarin 均随附了数十个程序集。 正如 Silverlight 是桌面 .NET 程序集的扩展子集，Xamarin 平台也是多个 Silverlight 和桌面 .NET 程序集的扩展子集。

Xamarin 平台与为不同配置文件编译的现有程序集不兼容。 您必须重新编译源代码，以生成以正确配置文件为目标的程序集（就像您需要将源代码重新编译为面向 Silverlight 和 .NET 3.5 的情况一样）。

Xamarin 应用程序可以在三种模式下进行编译：一个使用 Xamarin 的特选移动配置文件，一个 Xamarin .NET 4.5 Framework，它允许您将现有的完整桌面程序集作为目标，而不支持使用在系统 Mono 中找到的 .NET API安装. 有关详细信息，请参阅我们的[目标框架](~/mac/platform/target-framework.md)文档。

## <a name="net-standard-libraries"></a>.NET Standard 库

除了 iOS、Android 和 Mac 绑定外，Xamarin 项目还可以使用[.NET Standard 库](~/cross-platform/app-fundamentals/net-standard.md)。

## <a name="portable-class-libraries"></a>可移植类库

Xamarin 项目还可以使用[.Net 可移植类库](~/cross-platform/app-fundamentals/pcl.md)，不过，这种技术在支持 .NET Standard 时是不推荐使用的。

## <a name="supported-assemblies"></a>支持的程序集

这些是在**引用管理 > 器**中可用的程序集 > 框架（Visual Studio 2017）和**编辑引用 > 包**（Visual Studio for Mac）以及它们与 Xamarin 平台的兼容性。

> [!div class="mx-tdCol2BreakAll"]
> |Assembly|API 兼容性|Xamarin iOS|Xamarin Android|Xamarin Mac|
> |--------|-----------------|-----------|---------------|-----------|
> |FSharp.Core.dll| |✓|✓|✓|
> |l18N|包括 CJK、MidEast、其他、罕见、西|✓|✓|✓|
> |Microsoft.CSharp.dll| |✓|✓|✓|
> |Mono.CSharp.dll| |✓|✓|✓|
> |Mono.Data.Sqlite.dll|SQLite 的 ADO.NET 提供程序;请参阅限制。|✓|✓|✓|
> |Mono.Data.Tds.dll|TDS 协议支持;用于[system.web](xref:System.Data)中的[SqlClient](xref:System.Data.SqlClient)支持。|✓|✓|✓|
> |Mono。&#8203;解释器 .dll| |✓| | |
> |Mono.Security.dll|加密 Api。|✓|✓|✓|
> |monotouch.dll|此程序集包含C# CocoaTouch API 的绑定。 这仅适用于经典 iOS 项目。|✓| | |
> |Monotouch.dialog.&#8203;Dialog-1| |✓| | |
> |Monotouch.dialog.&#8203;NUnitLite| |✓| | |
> |mscorlib.dll|[Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx)|✓|✓|✓|
> |OpenTK-1.0.dll|面向 OpenGL/OpenAL 对象的 Api，扩展以提供 iPhone 设备支持。|✓|✓|✓|
> |System.dll|[Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx)，外加以下命名空间中的类型：<br />System.Collections.Specialized<br />主板.&#8203;System.componentmodel<br />System.componentmodel<br />System.Diagnostics<br />System.IO<br />System.IO.Compression<br />System.IO.Compression.FileSystem<br />System.Net<br />系统 .Net. 缓存<br />System.Net.Mail<br />系统 .Net<br />System.Net。&#8203;System.net.networkinformation<br />System.Net.Security<br />System.Net.Sockets<br />System.object。&#8203;InteropServices<br />System.Runtime.Versioning<br />系统安全。&#8203;Accesscontrol-namespace<br />System.Security.Authentication<br />系统安全。&#8203;加密<br />System.Security.Permissions<br />System.Threading<br />系统定时器|✓|✓|✓|
> |主板.&#8203;System.componentmodel。&#8203;组合 .dll| |✓|✓|✓|
> |主板.&#8203;System.componentmodel。&#8203;DataAnnotations| |✓|✓|✓|
> |System.Core.dll|[Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx)|✓|✓|✓|
> |System.Data.dll|[.Net 3.5](https://msdn.microsoft.com/library/ms229335.aspx) ，[移除了某些功能](~/ios/data-cloud/system.data.md)。|✓|✓|✓|
> |System.object。&#8203;服务。&#8203;Client .dll|完整的 oData 客户端。|✓|✓|✓|
> |System.IO。&#8203;压缩| |✓|✓|✓|
> |System.IO。&#8203;压缩。&#8203;文件系统| |✓|✓|✓|
> |System.Json.dll|[Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx)|✓|✓|✓|
> |System.Net。&#8203;Http.sys| |✓|✓|✓|
> |主板.&#8203;数值型 .dll| |✓|✓|✓|
> |System.object。&#8203;序列化 .dll|[Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx)|✓|✓|✓|
> |主板.&#8203;System.servicemodel .dll|[Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx)中存在的 WCF 堆栈|✓|✓|✓|
> |主板.&#8203;一起。&#8203;内部 .dll| |✓|✓|✓|
> |主板.&#8203;一起。&#8203;Web.config|[Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx)，外加以下命名空间中的类型： <br />System<br />System.ServiceModel.Channels<br />System.ServiceModel.Description<br />System.ServiceModel.Web|✓|✓|✓|
> |主板.&#8203;事务 .dll|[.Net 3.5](https://msdn.microsoft.com/library/ms229335.aspx);[系统](~/ios/data-cloud/system.data.md)支持的一部分。|✓|✓|✓|
> |System.web。&#8203;服务 .dll|.NET 3.5 配置文件中的基本 Web 服务，其中删除了服务器功能。|✓|✓|✓|
> |主板.&#8203;Windows .dll| |✓|✓|✓|
> |主板.&#8203;Xml .dll|[.NET 3.5](https://msdn.microsoft.com/library/ms229335.aspx)|✓|✓|✓|
> |System.object。&#8203;Linq .dll|[.NET 3.5](https://msdn.microsoft.com/library/ms229335.aspx)|✓|✓|✓|
> |System.Xml.Serialization.dll| |✓|✓|✓|
> |Xamarin.iOS.dll|此程序集包含C# CocoaTouch API 的绑定。 这仅用于统一 iOS 项目。|✓| | |
> |Java.Interop.dll| | |✓| |
> |Mono.Android.dll| | |✓| |
> |Mono。&#8203;导出 .dll| | |✓| |
> |Mono.Posix.dll| | |✓| |
> |主板.&#8203;System.enterpriseservices.servicedcomponent| | |✓| |
> |Xamarin。&#8203;NUnitLite| | |✓| |
> |System.runtime.compilerservices。&#8203;SymbolWriter|适用于编译器编写器。| | |✓|
> |Xamarin.Mac.dll| | | |✓|
> |主板.&#8203;制图|在 Unified API 中，不支持 Xamarin、.NET 4.5 或移动框架。 使用[sysdrawing-coregraphics](https://github.com/mono/sysdrawing-coregraphics)库可以将绘图支持添加到 IOS 和 macOS|✓| |✓|
