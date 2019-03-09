---
title: 可用程序集
description: 本文档列出了可在 Xamarin.iOS、 Xamarin.Android 和 Xamarin.Mac 中使用的程序集。 它还链接到.NET Standard 库和可移植类库的文档。
ms.prod: xamarin
ms.assetid: AEF4ED0E-391F-4FA4-9F18-842BC24C272D
author: asb3993
ms.author: amburns
ms.date: 03/13/2018
ms.openlocfilehash: 8882a7cd36eab5e7905585f5de4d6585dfb53439
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/08/2019
ms.locfileid: "57672256"
---
# <a name="available-assemblies"></a>可用程序集

Xamarin.iOS、 Xamarin.Android 和 Xamarin.Mac 所有附带十几个程序集。 就像 Silverlight 是桌面.NET 程序集的扩展的子集，Xamarin 平台也是多个 Silverlight 和桌面.NET 程序集的扩展的子集。

Xamarin 的平台不是与现有程序集编译为不同的配置文件兼容的 ABI。 您必须重新编译源代码以生成程序集面向正确的配置文件 （就像您需要重新编译源代码，以分别面向 Silverlight 和.NET 3.5）。

Xamarin.Mac 应用程序可以编译中三种模式： 另一个使用 Xamarin 的策划移动配置文件、 Xamarin.Mac.NET 4.5 Framework 允许你针对现有的完整的桌面程序集，并且在系统 Mono 中找到一个使用.NET API 的不受支持安装。 有关详细信息，请参阅我们[目标框架](~/mac/platform/target-framework.md)文档。

## <a name="net-standard-libraries"></a>.NET 标准库

除了 iOS、 Android 和 Mac 项目都可以使用的 Xamarin 绑定[.NET Standard 库](~/cross-platform/app-fundamentals/net-standard.md)。

## <a name="portable-class-libraries"></a>可移植类库

此外可以使用 Xamarin 项目[.NET 可移植类库](~/cross-platform/app-fundamentals/pcl.md)，尽管支持.NET Standard 已弃用此技术。

## <a name="supported-assemblies"></a>支持的程序集

这些是中提供的程序集**引用管理器 > 程序集 > 框架**(Visual Studio 2017) 和**编辑引用 > 包**(Visual Studio 中为 Mac)，和兼容性与 Xamarin 平台。

> [!div class="mx-tdCol2BreakAll"]
> |Assembly|API 兼容性|Xamarin iOS|Xamarin Android|Xamarin Mac|
> |--------|-----------------|-----------|---------------|-----------|
> |FSharp.Core.dll| |✓|✓|✓|
> |l18N.dll|包含 CJK，MidEast，其他、 少见西部|✓|✓|✓|
> |Microsoft.CSharp.dll| |✓|✓|✓|
> |Mono.CSharp.dll| |✓|✓|✓|
> |Mono.Data.Sqlite.dll|SQLite; 的 ADO.NET 提供程序请参阅限制。|✓|✓|✓|
> |Mono.Data.Tds.dll|TDS 协议的支持;用于[System.Data.SqlClient](xref:System.Data.SqlClient)内支持[System.Data](xref:System.Data)。|✓|✓|✓|
> |Mono.Dynamic.&#8203;Interpreter.dll| |✓| | |
> |Mono.Security.dll|加密 Api。|✓|✓|✓|
> |monotouch.dll|此程序集包含 C# 绑定到产品 CocoaTouch API。 这是仅在经典 iOS 项目中可用。|✓| | |
> |MonoTouch.&#8203;Dialog-1.dll| |✓| | |
> |MonoTouch.&#8203;NUnitLite.dll| |✓| | |
> |mscorlib.dll|[Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx)|✓|✓|✓|
> |OpenTK-1.0.dll|面向 OpenGL/OpenAL 对象的扩展以提供 iPhone 设备支持的 Api。|✓|✓|✓|
> |System.dll|[Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx)，加上以下命名空间中的类型：<br />System.Collections.Specialized<br />System.&#8203;ComponentModel<br />System.ComponentModel.Design<br />System.Diagnostics<br />System.IO<br />System.IO.Compression<br />System.IO.Compression.FileSystem<br />System.Net<br />System.Net.Cache<br />System.Net.Mail<br />System.Net.Mime<br />System.Net.&#8203;NetworkInformation<br />System.Net.Security<br />System.Net.Sockets<br />System.Runtime.&#8203;InteropServices<br />System.Runtime.Versioning<br />System.Security.&#8203;AccessControl<br />System.Security.Authentication<br />System.Security.&#8203;Cryptography<br />System.Security.Permissions<br />System.Threading<br />System.Timers|✓|✓|✓|
> |System.&#8203;ComponentModel.&#8203;Composition.dll| |✓|✓|✓|
> |System.&#8203;ComponentModel.&#8203;DataAnnotations.dll| |✓|✓|✓|
> |System.Core.dll|[Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx)|✓|✓|✓|
> |System.Data.dll|[.NET 3.5](https://msdn.microsoft.com/library/ms229335.aspx) ，使用[删除某些功能](~/ios/data-cloud/system.data.md)。|✓|✓|✓|
> |System.Data.&#8203;Services.&#8203;Client.dll|完整的 oData 客户端。|✓|✓|✓|
> |System.IO.&#8203;Compression| |✓|✓|✓|
> |System.IO.&#8203;Compression.&#8203;FileSystem| |✓|✓|✓|
> |System.Json.dll|[Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx)|✓|✓|✓|
> |System.Net.&#8203;Http.dll| |✓|✓|✓|
> |System.&#8203;Numerics.dll| |✓|✓|✓|
> |System.Runtime.&#8203;Serialization.dll|[Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx)|✓|✓|✓|
> |System.&#8203;ServiceModel.dll|WCF 堆栈中存在[Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx)|✓|✓|✓|
> |System.&#8203;ServiceModel.&#8203;Internals.dll| |✓|✓|✓|
> |System.&#8203;ServiceModel.&#8203;Web.dll|[Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx)，加上以下命名空间中的类型： <br />系统<br />System.ServiceModel.Channels<br />System.ServiceModel.Description<br />System.ServiceModel.Web|✓|✓|✓|
> |System.&#8203;Transactions.dll|[.NET 3.5](https://msdn.microsoft.com/library/ms229335.aspx); 的一部分[System.Data](~/ios/data-cloud/system.data.md)支持。|✓|✓|✓|
> |System.Web.&#8203;Services.dll|从.NET 3.5，配置文件中删除的服务器功能的基本 Web 服务。|✓|✓|✓|
> |System.&#8203;Windows.dll| |✓|✓|✓|
> |System.&#8203;Xml.dll|[.NET 3.5](https://msdn.microsoft.com/library/ms229335.aspx)|✓|✓|✓|
> |System.Xml.&#8203;Linq.dll|[.NET 3.5](https://msdn.microsoft.com/library/ms229335.aspx)|✓|✓|✓|
> |System.Xml.Serialization.dll| |✓|✓|✓|
> |Xamarin.iOS.dll|此程序集包含 C# 绑定到产品 CocoaTouch API。 这仅在统一 iOS 项目中使用。|✓| | |
> |Java.Interop.dll| | |✓| |
> |Mono.Android.dll| | |✓| |
> |Mono.Android.&#8203;Export.dll| | |✓| |
> |Mono.Posix.dll| | |✓| |
> |System.&#8203;EnterpriseServices.dll| | |✓| |
> |Xamarin.Android.&#8203;NUnitLite.dll| | |✓| |
> |Mono.CompilerServices.&#8203;SymbolWriter.dll|编译器编写器。| | |✓|
> |Xamarin.Mac.dll| | | |✓|
> |System.&#8203;Drawing.dll|System.Drawing 不支持统一 API 中的 Xamarin.Mac、.NET 4.5 中或移动框架。 System.Drawing 支持可添加到 iOS 和 macOS 使用[sysdrawing coregraphics](https://github.com/mono/sysdrawing-coregraphics)库|✓| |✓|
