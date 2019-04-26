---
title: 传输层安全性 (TLS) 1.2
description: 本文档介绍了如何启用 TLS 1.2 的 Xamarin.iOS、 Xamarin.Android 和 Xamarin.Mac 项目。 它演示了如何执行此操作在 Visual Studio 2019 和 Visual Studio for mac。
ms.prod: xamarin
ms.assetid: 399F71C6-16A4-4ABC-B30D-AF17D066A5FA
author: asb3993
ms.author: amburns
ms.date: 04/20/2018
ms.openlocfilehash: 26870ae0e84a84a7b78f7766a8e134ecfc7b223e
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61304726"
---
# <a name="transport-layer-security-tls-12"></a>传输层安全性 (TLS) 1.2

使用最新版[_传输层安全性_(TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security)确保应用程序网络通讯都是安全非常重要。

> [!WARNING]
> **2018 年 4 月，** – 由于增强的安全性要求，包括 PCI 合规性主要云提供程序和 web 服务器应停止对 TLS 版本低于 1.2 的支持。  在以前版本的 Visual Studio 默认使用 TLS 的较旧版本创建的 Xamarin 项目。
>
> 为了确保您的应用程序继续使用这些服务器和服务，**应更新 Xamarin 项目以使用的设置，然后重新生成并重新部署您的应用程序**到你的用户。

项目必须引用**System.Net.Http**程序集，如下所示进行配置。

## <a name="update-xamarinandroid-to-tls-12"></a>Tls 1.2 更新 Xamarin.Android

更新**HttpClient 实现**并**SSL/TLS 实现**选项来启用 TLS 1.2 安全。

> [!NOTE]
> 需要 Android 5.0 或更高版本。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

这些设置可在**项目属性 > Android 选项**，然后单击**高级**按钮：

[![在 Visual Studio 中配置 HttpClient 和 TLS](transport-layer-security-images/android-win-sml.png)](transport-layer-security-images/android-win.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

这些设置可在**项目选项 > 生成 > Android 生成**选项卡：

[![为 Mac 在 Visual Studio 中配置 HttpClient 和 TLS](transport-layer-security-images/android-mac-sml.png)](transport-layer-security-images/android-mac.png#lightbox)

-----

## <a name="update-xamarinios-to-tls-12"></a>Tls 1.2 更新 Xamarin.iOS

更新**HttpClient 实现**启用 TSL 1.2 安全选项。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

此设置可在**项目属性 > iOS 生成**:

[![在 Visual Studio 中配置 HttpClient 和 TLS](transport-layer-security-images/ios-win-sml.png)](transport-layer-security-images/ios-win.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

此设置可在**项目选项 > 生成 > iOS 生成**选项卡：

[![在 Visual Studio for Mac 中配置 HttpClient](transport-layer-security-images/ios-mac-sml.png)](transport-layer-security-images/ios-mac.png#lightbox)

-----

## <a name="update-xamarinmac-to-tls-12"></a>Tls 1.2 更新 Xamarin.Mac

在 Visual Studio for Mac，若要在 Xamarin.Mac 应用中启用 TLS 1.2，更新**HttpClient 实现**选项**项目选项 > 生成 > Mac 生成**:

[![在 Visual Studio for Mac 中配置 HttpClient](transport-layer-security-images/macos-mac-sml.png)](transport-layer-security-images/macos-mac.png#lightbox)

> [!WARNING]
> 即将发布的 Xamarin.Mac 4.8 版本仅支持 macOS 10.9 或更高版本。
> 早期版本的 Xamarin.Mac 支持 macOS 10.7 或更高版本，但这些较旧的 macOS 版本缺少足够的 TLS 基础结构，无法支持 TLS 1.2。 若要面向 macOS 10.7 或 macOS 10.8，请使用 Xamarin.Mac 4.6 或更早版本。

## <a name="alternative-configuration-options"></a>备用配置选项

本部分讨论上面所示的 TLS 1.2 支持配置的替代方法。
如果他们了解使用的 TLS 支持不同级别的风险，应用程序开发人员应只考虑以下替代方法。

### <a name="httpclient-implementation"></a>HttpClient 实现

Xamarin 开发人员一直都能够在其代码中使用本机网络类，但是没有还用于确定哪些网络堆栈的选项可供`HttpClient`类。 这提供了一个熟悉的.NET API，具有本机平台的速度和安全性的优点。

选项为：

- **托管的堆栈**– 提供 Mono 网络功能，或
- **本机堆栈**– 各种网络提供基础平台 （Android、 iOS 或 macOS） 的 Api。

托管的堆栈提供最高级别的兼容性与现有.NET 代码，但它可以变慢，并导致更大的可执行文件大小。

本机选项可能会更快和具有更好的安全性 （包括 TLS 1.2），但可能无法提供的所有功能和选项`HttpClient`类。

### <a name="ssltls-implementation-android"></a>SSL/TLS 实现 (Android)

Android 项目选项还允许您选择支持的 SSL/TLS 实现：

- **Mono/托管**– 在 Android 上的 TLS 1.1
- **本机**– 在 Android 上的 TLS 1.2。

新的 Xamarin 项目的默认值为支持 TLS 1.2 （这推荐的所有项目） 的本机实现，但是您可以切换回托管代码如果所需的兼容性原因。

> [!IMPORTANT]
> **Mono/托管**选项已被[从 iOS 和 Mac 中删除](https://developer.xamarin.com/releases/ios/xamarin.ios_10/xamarin.ios_10.8/)项目选项。
>
> 在 iOS 和 Mac 平台上始终使用本机选项。

## <a name="platform-specific-details"></a>特定于平台的详细信息

上述摘要介绍了在 Xamarin 项目中的 HttpClient 和 SSL/TLS 实现项目级设置。 此外可以在代码中动态设置 HttpClient 实现。 请参阅这些特定于平台的指南，有关详细信息：

- [**Android**](~/android/app-fundamentals/http-stack.md)
- [**iOS 和 Mac**](~/cross-platform/macios/http-stack.md)

## <a name="summary"></a>总结

只要有可能，应用程序应使用传输层安全 (TLS) 1.2。
应更新现有应用程序根据本文中中的说明中的设置，然后重新生成和重新部署到你的客户。

## <a name="related-links"></a>相关链接

- [应用传输安全性](~/ios/app-fundamentals/ats.md)
- [Xamarin.Android 环境](~/android/deploy-test/environment.md)
- [Xamarin Cycle 9 (2017 年 2 月)](https://releases.xamarin.com/stable-release-cycle-9/)
- [TLS (Wikipedia)](https://en.wikipedia.org/wiki/Transport_Layer_Security)
- [Mono 4.8 发行说明-TLS 1.2 支持](https://www.mono-project.com/docs/about-mono/releases/4.8.0/#tls-12-support)
- [BoringSSL](https://boringssl.googlesource.com/boringssl/)
- [HttpClient、 HttpClientHandler 和 WebRequestHandler 所述](https://blogs.msdn.microsoft.com/henrikn/2012/08/07/httpclient-httpclienthandler-and-webrequesthandler-explained/)
- [System.Net.HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.118).aspx)
- [System.Net.HttpClientHandler](https://msdn.microsoft.com/library/system.net.http.httpclienthandler(v=vs.118).aspx)
- [System.Net.HttpMessageHandler](https://msdn.microsoft.com/library/system.net.http.httpmessagehandler(v=vs.118).aspx)
- [System.Net.HttpWebRequest](https://msdn.microsoft.com/library/system.net.httpwebrequest(v=vs.110).aspx)
- [System.Net.WebClient](https://msdn.microsoft.com/library/system.net.webclient(v=vs.110).aspx)
- [System.Net.WebRequest](https://msdn.microsoft.com/library/system.net.webrequest(v=vs.110).aspx)
- [java.net.URLConnection](https://developer.android.com/reference/java/net/URLConnection.html)
- [Foundation.CFNetwork](xref:CoreFoundation.CFNetwork)
- [Foundation.NSUrlConnection](xref:Foundation.NSUrlConnection)
- [System.Net.WebRequest](https://msdn.microsoft.com/library/system.net.webrequest(v=vs.110).aspx)
- [HTTP 客户端 （示例）](https://developer.xamarin.com/samples/monotouch/HttpClient/)
