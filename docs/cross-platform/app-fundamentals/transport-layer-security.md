---
title: 传输层安全性 (TLS) 1。2
description: 本文档介绍如何为 Xamarin、Xamarin 和 Xamarin Mac 项目启用 TLS 1.2。 它演示了如何在 Visual Studio 2019 和 Visual Studio for Mac 中执行此操作。
ms.prod: xamarin
ms.assetid: 399F71C6-16A4-4ABC-B30D-AF17D066A5FA
author: asb3993
ms.author: amburns
ms.date: 04/20/2018
ms.openlocfilehash: d904ab25ee5a91fc33d9fa09c66a043e4bd33872
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2019
ms.locfileid: "68511413"
---
# <a name="transport-layer-security-tls-12"></a>传输层安全性 (TLS) 1。2

使用最新版本的[_传输层安全性_(TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security)非常重要, 可确保应用程序网络通信是安全的。

> [!WARNING]
> **2018 年4月**–由于安全要求增加, 包括 PCI 合规性, 主要云提供商和 web 服务器应停止支持早于1.2 的 TLS 版本。  在 Visual Studio 的早期版本中创建的 Xamarin 项目默认使用较旧版本的 TLS。
>
> 为了确保你的应用程序继续使用这些服务器和服务,**你应该更新 Xamarin 项目以使用以下设置, 然后重新生成应用并将其重新部署**到用户。

项目必须引用**系统 .net. Http**程序集, 并按如下所示进行配置。

## <a name="update-xamarinandroid-to-tls-12"></a>将 Xamarin 更新到 TLS 1。2

更新**HttpClient 实现**和**SSL/TLS 实现**选项以启用 TLS 1.2 安全性。

> [!NOTE]
> 需要 Android 5.0 或更高版本。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

这些设置可在 "**项目属性 > Android 选项**" 中找到, 然后单击 "**高级**" 按钮:

[![在 Visual Studio 中配置 HttpClient 和 TLS](transport-layer-security-images/android-win-sml.png)](transport-layer-security-images/android-win.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

这些设置可在 "**项目选项" "> 生成 > Android 生成**" 选项卡中找到:

[![在 Visual Studio for Mac 中配置 HttpClient 和 TLS](transport-layer-security-images/android-mac-sml.png)](transport-layer-security-images/android-mac.png#lightbox)

-----

## <a name="update-xamarinios-to-tls-12"></a>将 Xamarin 更新到 TLS 1。2

更新**HttpClient 实现**选项以启用 TSL 1.2 安全性。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

此设置可在 " **IOS 生成" > "项目属性**" 中找到:

[![在 Visual Studio 中配置 HttpClient 和 TLS](transport-layer-security-images/ios-win-sml.png)](transport-layer-security-images/ios-win.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

此设置可在 "**项目选项" > 生成 > IOS 生成**"选项卡中找到:

[![在 Visual Studio for Mac 中配置 HttpClient](transport-layer-security-images/ios-mac-sml.png)](transport-layer-security-images/ios-mac.png#lightbox)

-----

## <a name="update-xamarinmac-to-tls-12"></a>将 Xamarin 更新为 TLS 1。2

在 Visual Studio for Mac 中, 若要在 Xamarin. Mac 应用中启用 TLS 1.2, 请在 "**生成 > Mac 版本**" 中更新 "项目 > 选项" 中的**HttpClient 实现**选项:

[![在 Visual Studio for Mac 中配置 HttpClient](transport-layer-security-images/macos-mac-sml.png)](transport-layer-security-images/macos-mac.png#lightbox)

> [!WARNING]
> 即将发布的 Xamarin.Mac 4.8 版本仅支持 macOS 10.9 或更高版本。
> 早期版本的 Xamarin.Mac 支持 macOS 10.7 或更高版本，但这些较旧的 macOS 版本缺少足够的 TLS 基础结构，无法支持 TLS 1.2。 若要面向 macOS 10.7 或 macOS 10.8，请使用 Xamarin.Mac 4.6 或更早版本。

## <a name="alternative-configuration-options"></a>备用配置选项

本部分讨论了如上所示的 TLS 1.2 支持的配置的替代项。
如果应用程序开发人员了解使用不同级别的 TLS 支持的风险, 则应仅考虑使用这些方法。

### <a name="httpclient-implementation"></a>HttpClient 实现

Xamarin 开发人员始终能够使用其代码中的本机网络类, 但还有一个选项可确定`HttpClient`类使用哪些网络堆栈。 这会提供一个熟悉的 .NET API, 该 API 具有本机平台的速度和安全优势。

选项为：

- **托管堆栈**– Mono 提供的网络功能, 或者
- **本机堆栈**–基础平台 (Android、IOS 或 macOS) 提供的各种网络 api。

托管堆栈提供与现有 .NET 代码的最高级别的兼容性, 但它可能会变慢并导致更大的可执行文件大小。

本机选项可以更快地获得更好的安全性 (包括 TLS 1.2), 但可能不提供`HttpClient`类的所有功能和选项。

### <a name="ssltls-implementation-android"></a>SSL/TLS 实现 (Android)

Android 项目选项还允许选择要支持的 SSL/TLS 实现:

- **Mono/托管**– Android 上的 TLS 1。1
- Android 上的**本机**-TLS 1.2。

新的 Xamarin 项目默认为支持 TLS 1.2 的本机实现 (建议用于所有项目), 但出于兼容性原因, 你可以在需要时切换回托管代码。

> [!IMPORTANT]
> **Mono/托管**选项已[从 iOS 和 Mac 项目选项中删除](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/ios/xamarin.ios_10/xamarin.ios_10.8.md)。
>
> 本机选项始终在 iOS 和 Mac 平台上使用。

## <a name="platform-specific-details"></a>平台特定的详细信息

以上摘要说明了 Xamarin 项目中 HttpClient 和 SSL/TLS 实现的项目级设置。 还可以在代码中动态设置 HttpClient 实现。 有关详细信息, 请参阅以下特定于平台的指南:

- [**Android**](~/android/app-fundamentals/http-stack.md)
- [**iOS 和 Mac**](~/cross-platform/macios/http-stack.md)

## <a name="summary"></a>总结

应用程序应尽可能使用传输层安全性 (TLS) 1.2。
你应按照本文中的说明更新现有应用程序中的设置, 然后重新生成并重新部署到你的客户。

## <a name="related-links"></a>相关链接

- [应用传输安全性](~/ios/app-fundamentals/ats.md)
- [Xamarin Android 环境](~/android/deploy-test/environment.md)
- [Xamarin 周期 9 (2017 年2月)](https://releases.xamarin.com/stable-release-cycle-9/)
- [TLS (维基百科)](https://en.wikipedia.org/wiki/Transport_Layer_Security)
- [Mono 4.8 发行说明-TLS 1.2 支持](https://www.mono-project.com/docs/about-mono/releases/4.8.0/#tls-12-support)
- [BoringSSL](https://boringssl.googlesource.com/boringssl/)
- [说明了 HttpClient、HttpClientHandler 和 WebRequestHandler](https://blogs.msdn.microsoft.com/henrikn/2012/08/07/httpclient-httpclienthandler-and-webrequesthandler-explained/)
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
- [HTTP 客户端 (示例)](https://developer.xamarin.com/samples/monotouch/HttpClient/)
