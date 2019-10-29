---
title: Xamarin 支持的用于 tvOS 的程序集
description: 为了帮助阐明可用于 tvOS 应用程序的功能，本文档提供了 Xamarin for tvOS 开发支持的程序集列表。
ms.prod: xamarin
ms.assetid: 0B1ACF06-65FF-49E2-B6BC-7AEC55638ED8
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 06/07/2016
ms.openlocfilehash: 371440f2e1ab28e802bf2d184b3e17d073a0c774
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73030688"
---
# <a name="assemblies-supported-by-xamarin-for-tvos"></a>Xamarin 支持的用于 tvOS 的程序集

## <a name="supported-assemblies"></a>支持的程序集

这是 Xamarin 为你的 tvOS 应用程序所支持的程序集的列表。下面列出了这些详细信息列表。  一些值得注意的遗漏包括 `System.EnterpriseServices`、ASP.NET stack 和 Windows 窗体。

|Assembly|已添加|API 兼容性|
|---|---|---|
|Mono.CompilerServices.SymbolWriter.dll|1.0|适用于编译器编写器。|
|Mono.Data.Sqlite.dll|1.2|SQLite 的 ADO.NET 提供程序;请参阅[限制](~/ios/data-cloud/system.data.md)。|
|Mono.Data.Tds.dll|1.2|TDS 协议支持;用于[system.web](~/ios/data-cloud/system.data.md)中的[SqlClient](xref:System.Data.SqlClient)支持。|
|Mono.Security.dll|1.0|加密 Api。|
|monotouch.dll|1.0|此程序集包含[ C# CocoaTouch API 的绑定](https://docs.microsoft.com/dotnet/api/?view=xamarinios-10.8)。|
|mscorlib.dll|1.0|[Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx)|
|OpenTK.dll|1.0|面向 OpenGL/OpenAL 对象的 Api，[扩展以提供 iPhone 设备支持](xref:OpenGLES)。|
|System.dll|1.0|[Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx)，外加以下命名空间中的类型： <ul><li>System.Collections.Specialized</li> <li>System.ComponentModel</li> <li>System.componentmodel</li> <li>System.Diagnostics</li> <li>System.IO.Compression</li> <li>System.Net</li> <li>系统 .Net. 缓存</li> <li>System.Net.Mail</li> <li>系统 .Net</li> <li>System.Net.NetworkInformation</li> <li>System.Net.Security</li> <li>System.Net.Sockets</li> <li>System.Security.Authentication</li> <li>System.Security.Cryptography</li> <li>系统定时器</li></ul>|
|System.Core.dll|1.0|[Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx)|
|System.Data.dll|1.2|[.Net 3.5](https://msdn.microsoft.com/library/ms229335.aspx)，[移除了某些功能](~/ios/data-cloud/system.data.md)。|
|"System.object"。|3.x|完整的 oData 客户端。|
|System.Drawing|1.0|仅限 Classic API。<br />_Xamarin .NET 4.5 或 Mobile framework 的 Unified API 不支持 system.object。_|
|System.Json.dll|1.1|[Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx)|
|System.Runtime.Serialization.dll|?|[Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx)|
|System.ServiceModel.dll|1.1|[Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx)中存在的[WCF](https://docs.microsoft.com/xamarin/cross-platform/data-cloud/web-services/)堆栈|
|System.ServiceModel.Web.dll|?|[Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx)，外加以下命名空间中的类型： <ul><li>System</li><li>System.ServiceModel.Channels</li><li>System.ServiceModel.Description</li><li>System.ServiceModel.Web</li></ul>|
|System.Transactions.dll|1.2|[.Net 3.5](https://msdn.microsoft.com/library/ms229335.aspx);[系统](https://docs.microsoft.com/xamarin/ios/data-cloud/system.data)支持的一部分。|
|System.Web.Services|1.1|.NET 3.5 配置文件中的[基本 Web 服务](https://docs.microsoft.com/xamarin/cross-platform/data-cloud/web-services/)，其中删除了服务器功能。|
|System.Xml.dll|1.0|[.NET 3.5](https://msdn.microsoft.com/library/ms229335.aspx)|
|System.Xml.Linq.dll|1.0|[.NET 3.5](https://msdn.microsoft.com/library/ms229335.aspx)|

<a name="Summary" />

## <a name="portable-class-libraries"></a>可移植类库

除 Mac 绑定外，tvOS 还可以使用[.Net 可移植类库](~/cross-platform/app-fundamentals/pcl.md)。

## <a name="related-links"></a>相关链接

- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人体学接口指南](https://developer.apple.com/tvos/human-interface-guidelines/)
- [TvOS 应用编程指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
