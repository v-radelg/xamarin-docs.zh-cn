---
title: 在 Xamarin.iOS 应用程序传输安全
description: 应用程序传输安全 (ATS) 强制实施 internet 资源 （如应用程序的后端服务器） 和您的应用程序之间的安全连接。
ms.prod: xamarin
ms.assetid: F8C5E444-2D05-4D9B-A2EF-EB052CD6F007
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 06/13/2017
ms.openlocfilehash: a901e16b3d5befc25864af39cb255d1833400e7f
ms.sourcegitcommit: 946ce514fd6575aa6b93ff24181e02a60b24b106
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2019
ms.locfileid: "58677880"
---
# <a name="app-transport-security-in-xamarinios"></a>在 Xamarin.iOS 应用程序传输安全

_应用程序传输安全 (ATS) 强制实施 internet 资源 （如应用程序的后端服务器） 和您的应用程序之间的安全连接。_

本文将介绍应用传输安全对 iOS 9 应用程序强制实施的安全更改并[这为 Xamarin.iOS 项目意味着](#xamarinsupport)，将介绍[ATS 配置选项](#config)和它将介绍如何[选择退出的 ATS](#optout) ATS 必要。 由于默认情况下启用了 ATS，任何不安全的 internet 连接将引发在 iOS 9 应用程序中的异常 （除非已显式允许它）。


## <a name="about-app-transport-security"></a>有关应用程序传输安全

如上面所述，ATS 可确保所有 internet 通信的 iOS 9 和 OS X El Capitan 中都符合以确保连接安全最佳实践，从而防止意外泄露敏感信息直接通过您的应用程序或它是一个库使用。

对于现有应用程序，实现`HTTPS`协议只要有可能。 为新的 Xamarin.iOS 应用程序，应使用`HTTPS`以独占方式与 internet 资源进行通信时。 此外，必须使用与正向保密的 TLS 版本 1.2 加密高级 API 通信。

任何连接都使用[NSUrlConnection](xref:Foundation.NSUrlConnection)， [CFUrl](xref:CoreFoundation.CFUrl)或[NSUrlSession](xref:Foundation.NSUrlSession)将适用于 iOS 9 和 OS X 10.11 (El Capitan) 构建应用程序中默认情况下使用 ATS。

## <a name="default-ats-behavior"></a>默认 ATS 行为

由于默认情况下，所有连接使用适用于 iOS 9 和 OS X 10.11 (El Capitan) 都构建应用程序中启用了 ATS [NSUrlConnection](xref:Foundation.NSUrlConnection)， [CFUrl](xref:CoreFoundation.CFUrl)或[NSUrlSession](xref:Foundation.NSUrlSession)收费依据ATS 安全要求。 如果你的连接不能满足这些要求，它们将失败并出现异常。

### <a name="ats-connection-requirements"></a>ATS 连接要求

ATS 将强制所有 internet 连接的以下要求：

- 所有连接加密法必须都使用正向保密。 请参阅下面接受加密法的列表。
- 传输层安全性 (TLS) 协议必须为 1.2 或更高版本。
- 使用 2048 位或更高版本的 RSA 密钥，或 256 位或更高版本的椭圆曲线 (ECC) 密钥至少为 SHA256 指纹必须用于所有的证书。

同样，由于默认情况下在 iOS 9 中启用了 ATS，如果试图将不能满足这些要求的连接将导致引发异常。 

<a name="ATS-Compatible-Ciphers" />

### <a name="ats-compatible-ciphers"></a>ATS 兼容密码

通过安全的 internet 通信的 ATS 接受以下向前保密的密码类型：

- `TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384`
- `TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256`
- `TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384`
- `TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA`
- `TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256`
- `TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA`
- `TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384`
- `TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`
- `TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384`
- `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256`
- `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA`

有关使用 iOS internet 通信类的详细信息，请参阅 Apple [NSURLConnection 类引用](https://developer.apple.com/library/prerelease/ios/documentation/Cocoa/Reference/Foundation/Classes/NSURLConnection_Class/index.html#//apple_ref/doc/uid/TP40003755)， [CFURL 引用](https://developer.apple.com/library/prerelease/ios/documentation/CoreFoundation/Reference/CFURLRef/index.html#//apple_ref/doc/uid/20001206)或[NSURLSession 类引用](https://developer.apple.com/library/prerelease/ios/documentation/Foundation/Reference/NSURLSession_class/index.html#//apple_ref/doc/uid/TP40013435).

<a name="xamarinsupport" />

## <a name="supporting-ats-in-xamarinios"></a>在 Xamarin.iOS 中支持 ATS

因为 ATS 已启用 iOS 9 和 OS X El Capitan，默认情况下，如果你的 Xamarin.iOS 应用程序或任何库或它所使用的服务连接到 internet，需要采取某种操作或你的连接将导致引发异常。

对于现有应用程序，Apple 建议在支持`HTTPS`协议越早越好。 如果您是无法，因为您正在连接的第三方不支持的 web 服务`HTTPS`或如果支持`HTTPS`是不现实的你可以选择退出的 ATS。 请参阅[Opting 带 ATS](#optout)部分获取更多详细信息。

对于新的 Xamarin.iOS 应用，应使用`HTTPS`以独占方式与 internet 资源进行通信时。 同样，可能有情形 （如使用第三方 web 服务） 其中不可行，你将需要选择退出的 ATS。

此外，ATS 强制实施高级别 API 通信进行加密与正向保密使用 TLS 1.2 版。 请参阅[ATS 连接要求](#ats-connection-requirements)并[ATS 兼容加密法](#ats-compatible-ciphers)上面部分的更多详细信息。

尽管您可能不熟悉 TLS ([传输层安全性](https://en.wikipedia.org/wiki/Transport_Layer_Security)) 是 SSL 的后继 ([安全套接字层](https://en.wikipedia.org/wiki/Transport_Layer_Security)) 并提供了一系列上实施安全的加密协议网络连接。

TLS 级别控制您正在使用的 web 服务，因此受应用程序的控制。 同时`HttpClient`和`ModernHttpClient`应会自动使用 TLS 加密服务器支持的最高级别。

具体情况取决于在服务器 （尤其是如果它是第三方服务） 正在与谈话，可能需要禁用向前保密或选择较低的 TLS 级别。 请参阅[配置 ATS 选项](#configuring-ats-options)部分获取更多详细信息。

> [!IMPORTANT]
> 应用程序传输安全不适用于使用 Xamarin 应用**托管的 HTTPClient 实现**。 它适用于连接使用 CFNetwork **HTTPClient 实现**或**NSURLSession HTTPClient 实现**仅。

### <a name="setting-the-httpclient-implementation"></a>设置 HTTPClient 实现

若要设置 iOS 应用中使用的 HTTPClient 实现，双击**项目**中**解决方案资源管理器**以打开**项目选项**。 导航到**iOS 生成**，然后选择所需的客户端类型下**HttpClient 实现**下拉列表中：

![](ats-images/client01.png "设置 iOS 生成选项")


#### <a name="managed-handler"></a>托管处理程序

托管处理程序是完全托管的 HttpClient 处理程序已与以前版本的 Xamarin.iOS 已寄出，是默认处理程序。

专业人员：

- 它是与 Microsoft.NET 和较旧版本的 Xamarin 兼容。

缺点：

- 它不完全集成与 iOS （例如它被限制为 TLS 1.0）。
- 它是通常比本机 Api 要慢得多。
- 它需要更多托管的代码，并创建更大的应用程序。

#### <a name="cfnetwork-handler"></a>CFNetwork 处理程序

CFNetwork 基于处理程序基于本地`CFNetwork`框架。

专业人员：

- 使用本机 API 进行更好的性能和较小的可执行文件大小。
- 添加较新的标准，如 TLS 1.2 的支持。

缺点：

- 需要 iOS 6 或更高版本。
- WatchOS 的不可用。
- 某些 HttpClient 功能和选项不可用。

#### <a name="nsurlsession-handler"></a>NSUrlSession 处理程序

NSUrlSession 基于处理程序基于本地`NSUrlSession`API。

专业人员：

- 使用本机 API 进行更好的性能和较小的可执行文件大小。
- 添加较新的标准，如 TLS 1.2 的支持。

缺点：

- 需要 iOS 7 或更高版本。
- 某些 HttpClient 功能和选项不可用。 

## <a name="diagnosing-ats-issues"></a>诊断 ATS 问题

在尝试连接到 internet，直接或从 iOS 9 中的 web 视图时，你可能会遇到错误，在窗体中：

> 应用程序传输安全已阻止明文 HTTP (http://www.-the-blocked-domain.com)资源加载，因为它是不安全。 临时异常可通过应用的 Info.plist 文件进行配置。

IOS9，在应用程序传输安全 (ATS) 强制实施 internet 资源 （如应用程序的后端服务器） 和您的应用程序之间的安全连接。 此外，ATS 要求通信使用`HTTPS`协议和高级别 API 通信进行加密与正向保密使用 TLS 1.2 版。

由于默认情况下，所有连接使用适用于 iOS 9 和 OS X 10.11 (El Capitan) 都构建应用程序中启用了 ATS `NSURLConnection`，`CFURL`或`NSURLSession`将受到 ATS 安全要求。 如果你的连接不能满足这些要求，它们将失败并出现异常。

Apple 还提供了[TLSTool 示例应用](https://developer.apple.com/library/mac/samplecode/sc1236/Introduction/Intro.html#//apple_ref/doc/uid/DTS40014927-Intro-DontLinkElementID_2)可编译 (或根据需要与 Xamarin 的转码和C#)，用于诊断 ATS/TLS 的问题。 请参阅[Opting 带 ATS](#optout)部分获取有关如何解决此问题的信息。


<a name="config" />

## <a name="configuring-ats-options"></a>配置 AT 选项

可以通过在应用中设置特定键的值配置的几项功能的 ATS **Info.plist**文件。 以下项是可用于控制 ATS (_缩进以显示其嵌套方式_):

```csharp
NSAppTransportSecurity
    NSAllowsArbitraryLoads
    NSAllowsArbitraryLoadsInWebContent
    NSExceptionDomains
    <domain-name-for-exception-as-string>
        NSExceptionMinimumTLSVersion
        NSExceptionRequiresForwardSecrecy
        NSExceptionAllowsInsecureHTTPLoads
        NSRequiresCertificateTransparency
        NSIncludesSubdomains
        NSThirdPartyExceptionMinimumTLSVersion
        NSThirdPartyExceptionRequiresForwardSecrecy
        NSThirdPartyExceptionAllowsInsecureHTTPLoads
```

每个键具有以下类型和含义：

- **NSAppTransportSecurity** (`Dictionary`)-包含所有的设置键和值的 ATS。
- **NSAllowsArbitraryLoads** (`Boolean`)-如果`YES`ATS 将禁用的任何域**不**中列出`NSExceptionDomains`。 对于列出的域，将使用指定的安全设置。
- **NSAllowsArbitraryLoadsInWebContent** (`Boolean`)-如果`YES`将允许网页时的应用程序的其余部分仍启用 Apple 传输安全 (ATS) 保护正确加载。
- **NSExceptionDomains** (`Dictionary`)-域的集合，以及 ATS 对给定域应使用的安全设置。
- **< Domain-name-for-exception-as-string >** (`Dictionary`) 的一系列针对给定的域 （例如异常。 `www.xamarin.com`）格式模式中出现的位置生成。
- **NSExceptionMinimumTLSVersion** (`String`)-为最小的 TLS 版本`TLSv1.0`，`TLSv1.1`或`TLSv1.2`（这是默认值）。
- **NSExceptionRequiresForwardSecrecy** (`Boolean`)-如果`NO`域不需要使用密码，正向的安全。 默认值为 `YES`。
- **NSExceptionAllowsInsecureHTTPLoads** (`Boolean`)-如果`NO`（默认值） 与此域的所有通信必须都在`HTTPS`协议。
- **NSRequiresCertificateTransparency** (`Boolean`)-如果`YES`域的安全套接字层 (SSL) 必须包含有效的透明数据。 默认值为 `NO`。
- **NSIncludesSubdomains** (`Boolean`)-如果`YES`这些设置将替代此域的所有子域。 默认值为 `NO`。
- **NSThirdPartyExceptionMinimumTLSVersion** (`String`)-域外的开发人员控制第三方服务时所使用的 TLS 版本。
- **NSThirdPartyExceptionRequiresForwardSecrecy** (`Boolean`)-如果`YES`的第三方域需要向前保密。
- **NSThirdPartyExceptionAllowsInsecureHTTPLoads** (`Boolean`)-如果`YES`AT 将允许非安全的第三方域的通信。

<a name="optout" />

### <a name="opting-out-of-ats"></a>选择向外的 ATS

虽然 Apple 强烈建议使用`HTTPS`基于信息协议和安全通信到 internet，可能会有这并不总是可行的时间。 例如，如果要与第三方 web 服务进行通信或使用 internet 应用程序中提供的广告。

如果 Xamarin.iOS 应用程序必须对不安全的域进行请求，以下将更改为你的应用**Info.plist**文件将禁用 ATS 对给定域强制实施的安全默认值：

```xml
<key>NSAppTransportSecurity</key>
<dict>
    <key>NSExceptionDomains</key>
    <dict>
        <key>www.the-domain-name.com</key>
        <dict>
            <key>NSExceptionMinimumTLSVersion</key>
            <string>TLSv1.0</string>
            <key>NSExceptionRequiresForwardSecrecy</key>
            <false/>
            <key>NSExceptionAllowsInsecureHTTPLoads</key>
            <true/>
            <key>NSIncludesSubdomains</key>
            <true/>
        </dict>
    </dict>
</dict>
```

在 Visual Studio for Mac 中，双击`Info.plist`文件中**解决方案资源管理器**，切换到**源**查看并添加上述密钥：

[![](ats-images/ats01.png "Info.plist 文件的源视图")](ats-images/ats01.png#lightbox)


如果您的应用程序需要加载并显示来自不安全站点的 web 内容，请将以下代码添加到应用程序的**Info.plist**文件以允许网页的其余部分仍启用 Apple 传输安全 (ATS) 保护时正确加载应用程序：

```xml
<key>NSAppTransportSecurity</key>
<dict>
    <key> NSAllowsArbitraryLoadsInWebContent</key>
    <true/>
</dict>
```

（可选），可以在应用中进行以下更改**Info.plist**文件以完全禁用 ATS 为所有域和 internet 通信：

```xml
<key>NSAppTransportSecurity</key>
<dict>
    <key>NSAllowsArbitraryLoads</key>
    <true/>
</dict>
```

在 Visual Studio for Mac 中，双击`Info.plist`文件中**解决方案资源管理器**，切换到**源**查看并添加上述密钥：

[![](ats-images/ats02.png "Info.plist 文件的源视图")](ats-images/ats02.png#lightbox)

> [!IMPORTANT]
> 如果你的应用程序需要连接到不安全的网站，则应该**始终**作为异常使用输入的域`NSExceptionDomains`而不是关闭 ATS 完全使用`NSAllowsArbitraryLoads`。 `NSAllowsArbitraryLoads` 仅应在极端的紧急情况下使用。




同样，禁用 ATS 应_仅_用作最后的手段，如果切换到安全连接是不可用或不切实际。

<a name="Summary" />

## <a name="summary"></a>总结

此文已引入应用传输安全 (ATS)，所述强制执行与 internet 的通信安全的方式。 首先，我们介绍如何运行 iOS 9 的 Xamarin.iOS 应用程序的 ATS 要求的更改。 然后我们介绍如何控制 ATS 功能和选项。 最后，我们介绍如何选择退出 ATS Xamarin.iOS 应用程序中。



## <a name="related-links"></a>相关链接

- [iOS 9 示例](https://developer.xamarin.com/samples/ios/iOS9/)
- [面向开发人员的 iOS 9](https://developer.apple.com/ios/pre-release/)
- [iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
