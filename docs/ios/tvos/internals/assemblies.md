---
title: 程序集支持的适用于 tvOS 的 Xamarin
description: 为了帮助阐明提供对 tvOS 应用程序的功能，本文档提供了一系列由 Xamarin 支持 tvOS 开发的程序集。
ms.prod: xamarin
ms.assetid: 0B1ACF06-65FF-49E2-B6BC-7AEC55638ED8
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 06/07/2016
ms.openlocfilehash: df50b4280335001f2d27ff23a91e4098eed3ba99
ms.sourcegitcommit: 495680e74c72e7c570e68cde95d3d3643b1fcc8a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/02/2019
ms.locfileid: "58870204"
---
# <a name="assemblies-supported-by-xamarin-for-tvos"></a>程序集支持的适用于 tvOS 的 Xamarin

## <a name="supported-assemblies"></a>支持的程序集

这是为 Xamarin.tvOS 应用支持的 Xamarin 的程序集的列表。下面列出了这些的详细的列表。  包括一些值得注意的疏漏`System.EnterpriseServices`，ASP.NET 堆栈和 Windows.Forms。

|Assembly|已添加|API 兼容性|
|---|---|---|
|Mono.CompilerServices.SymbolWriter.dll|1.0|编译器编写器。|
|Mono.Data.Sqlite.dll|1.2|SQLite; 的 ADO.NET 提供程序请参阅[限制](~/ios/data-cloud/system.data.md)。|
|Mono.Data.Tds.dll|1.2|TDS 协议的支持;用于[System.Data.SqlClient](xref:System.Data.SqlClient)内支持[System.Data](~/ios/data-cloud/system.data.md)。|
|Mono.Security.dll|1.0|加密 Api。|
|monotouch.dll|1.0|此程序集包含[C# 绑定到产品 CocoaTouch API](https://docs.microsoft.com/dotnet/api/?view=xamarinios-10.8)。|
|mscorlib.dll|1.0|[Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx)|
|OpenTK.dll|1.0|OpenGL/OpenAL 对象面向 Api，[扩展，使其提供 iPhone 设备支持](xref:OpenGLES)。|
|System.dll|1.0|[Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx)，加上以下命名空间中的类型： <ul><li>System.Collections.Specialized</li> <li>System.ComponentModel</li> <li>System.ComponentModel.Design</li> <li>System.Diagnostics</li> <li>System.IO.Compression</li> <li>System.Net</li> <li>System.Net.Cache</li> <li>System.Net.Mail</li> <li>System.Net.Mime</li> <li>System.Net.NetworkInformation</li> <li>System.Net.Security</li> <li>System.Net.Sockets</li> <li>System.Security.Authentication</li> <li>System.Security.Cryptography</li> <li>System.Timers</li></ul>|
|System.Core.dll|1.0|[Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx)|
|System.Data.dll|1.2|[.NET 3.5](https://msdn.microsoft.com/library/ms229335.aspx)，[删除某些功能与](~/ios/data-cloud/system.data.md)。|
|System.Data.Service.Client.dll|3.x|完整的 oData 客户端。|
|System.Drawing|1.0|System.Drawing API-仅经典 API。<br />_System.Drawing 不支持统一 API 中的 Xamarin.Mac.NET 4.5 或移动框架。_|
|System.Json.dll|1.1|[Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx)|
|System.Runtime.Serialization.dll|?|[Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx)|
|System.ServiceModel.dll|1.1|[WCF](http://docs.xamarin.com/guides/cross-platform/application_fundamentals/introduction_to_web_services)堆栈为存在在[Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx)|
|System.ServiceModel.Web.dll|?|[Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx)，加上以下命名空间中的类型： <ul><li>系统</li><li>System.ServiceModel.Channels</li><li>System.ServiceModel.Description</li><li>System.ServiceModel.Web</li></ul>|
|System.Transactions.dll|1.2|[.NET 3.5](https://msdn.microsoft.com/library/ms229335.aspx); 的一部分[System.Data](https://docs.microsoft.com/xamarin/ios/data-cloud/system.data)支持。|
|System.Web.Services|1.1|[基本 Web 服务](http://docs.xamarin.com/guides/cross-platform/application_fundamentals/introduction_to_web_services)从.NET 3.5，配置文件中删除的服务器功能。|
|System.Xml.dll|1.0|[3.5.NET](https://msdn.microsoft.com/library/ms229335.aspx)|
|System.Xml.Linq.dll|1.0|[3.5.NET](https://msdn.microsoft.com/library/ms229335.aspx)|

<a name="Summary" />

## <a name="portable-class-libraries"></a>可移植类库

除了 Mac 绑定，可以使用 Xamarin.tvOS [.NET 可移植类库](~/cross-platform/app-fundamentals/pcl.md)。

## <a name="related-links"></a>相关链接

- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人机接口指南](https://developer.apple.com/tvos/human-interface-guidelines/)
- [适用于 tvOS 应用编程指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
