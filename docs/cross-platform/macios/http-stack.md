---
title: 适用于 iOS/macOS 的 HttpClient 和 SSL/TLS 实现选择器
description: HttpClient stack 和 SSL/TLS 实现选择器决定了将由 Xamarin iOS、tvOS 或 macOS 应用使用的 HttpClient 和 SSL/TLS 实现。
ms.prod: xamarin
ms.assetid: 12101297-BB04-4410-85F0-A0D41B7E6591
author: conceptdev
ms.author: crdun
ms.date: 04/20/2018
ms.openlocfilehash: 305c0c939d99207e39a469f7e8c5370cc5555d38
ms.sourcegitcommit: 699de58432b7da300ddc2c85842e5d9e129b0dc5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2019
ms.locfileid: "71249977"
---
# <a name="httpclient-and-ssltls-implementation-selector-for-iosmacos"></a>适用于 iOS/macOS 的 HttpClient 和 SSL/TLS 实现选择器

Xamarin、tvOS 和 xamarin 的**HttpClient 实现选择器**控制要使用的`HttpClient`实现。 可以切换到使用 iOS、tvOS 或 macOS 本机传输（`NSUrlSession`或`CFNetwork`，具体取决于 OS）的实现。 优势是 TLS 1.2-支持、较小的二进制文件和更快速的下载;缺点是需要运行事件循环才能执行异步操作。

项目必须引用**系统 .net. Http**程序集。

> [!WARNING]
> **2018 年4月**–由于安全要求增加，包括 PCI 合规性，主要云提供商和 web 服务器应停止支持早于1.2 的 TLS 版本。 在 Visual Studio 的早期版本中创建的 Xamarin 项目默认使用较旧版本的 TLS。
>
> 为了确保你的应用程序继续使用这些服务器和服务，**你应该使用如下`NSUrlSession`所示的设置来更新 Xamarin 项目，然后重新生成应用并将其重新部署**到用户。

## <a name="selecting-an-httpclient-stack"></a>选择 HttpClient 堆栈

若要调整`HttpClient`应用使用的，请执行以下操作：

1. 双击 "**解决方案资源管理器**中的**项目名称**以打开" 项目选项 "。
2. 切换到项目的**生成**设置（例如，适用于 Xamarin ios 应用的**ios 生成**）。
3. 从 " **HttpClient 实现**" 下拉列表中`HttpClient` ，选择以下类型之一：**NSUrlSession**（建议）、 **CFNetwork**或**托管**。

[![从托管、CFNetwork 或 NSUrlSession 选择 HttpClient 实现](http-stack-images/http-xs-sml.png)](http-stack-images/http-xs.png#lightbox)

> [!TIP]
> 对于 TLS 1.2 支持， `NSUrlSession`建议使用此选项。

### <a name="nsurlsession"></a>NSUrlSession

基于的处理程序基于 iOS 7 和更`NSURLSession`高版本中提供的本机框架。 `NSURLSession` 
**这是建议的设置。**

#### <a name="pros"></a>专业人员

- 它使用本机 Api 来获得更好的性能和更小的可执行文件大小。
- 支持 TLS 1.2 等最新标准。

#### <a name="cons"></a>各有利弊

- 需要 iOS 7 或更高版本。
- 某些`HttpClient`功能/选项不可用。

### <a name="cfnetwork"></a>CFNetwork

基于的处理程序基于 iOS 6 和更`CFNetwork`高版本中提供的本机框架。 `CFNetwork`

#### <a name="pros"></a>专业人员

- 它使用本机 Api 来获得更好的性能和更小的可执行文件大小。
- 支持 TLS 1.2 等更新标准。

#### <a name="cons"></a>各有利弊

- 需要 iOS 6 或更高版本。
- 在 watchOS 上不可用。
- 某些 HttpClient 功能/选项不可用。

### <a name="managed"></a>Managed

托管处理程序是与以前版本的 Xamarin 一起提供的完全托管的 HttpClient 处理程序。

#### <a name="pros"></a>专业人员

- 它与 Microsoft .NET 和旧版 Xamarin 版本最兼容的功能集。

#### <a name="cons"></a>各有利弊

- 它未与 Apple Os 完全集成，并且仅限于 TLS 1.0。 将来可能无法连接到安全的 web 服务器或云服务。
- 它通常比本机 Api 的加密速度要慢得多。
- 它需要更多托管代码，从而创建更大的应用程序。

### <a name="programmatically-setting-the-httpmessagehandler"></a>以编程方式设置 HttpMessageHandler

除了上面所示的项目范围配置之外，还可以实例化`HttpClient`并注入构造函数所需`HttpMessageHandler`的，如以下代码片段所示：

```csharp
// This will use the default message handler for the application; as
// set in the Project Options for the project.
HttpClient client = new HttpClient();

// This will create an HttpClient that explicitly uses the CFNetworkHandler
HttpClient client = new HttpClient(new CFNetworkHandler());

// This will create an HttpClient that explicitly uses NSUrlSessionHandler
HttpClient client = new HttpClient(new NSUrlSessionHandler());
```

这样，就可以使用 " `HttpMessageHandler` **项目选项**" 对话框中声明的内容。

## <a name="ssltls-implementation"></a>SSL/TLS 实现

SSL （安全套接字层）及其后续版本 TLS （传输层安全性）通过`System.Net.Security.SslStream`提供对 HTTP 和其他网络连接的支持。 Xamarin、tvOS 或 xamarin 的`System.Net.Security.SslStream`实现将调用 Apple 的本机 SSL/TLS 实现，而不是使用 Mono 提供的托管实现。 Apple 的本机实现支持 TLS 1.2。

> [!WARNING]
> 即将发布的 Xamarin.Mac 4.8 版本仅支持 macOS 10.9 或更高版本。
> 早期版本的 Xamarin.Mac 支持 macOS 10.7 或更高版本，但这些较旧的 macOS 版本缺少足够的 TLS 基础结构，无法支持 TLS 1.2。 若要面向 macOS 10.7 或 macOS 10.8，请使用 Xamarin.Mac 4.6 或更早版本。

## <a name="app-transport-security"></a>应用传输安全性

Apple 的_应用传输安全_（ATS）强制实施 internet 资源（如应用的后端服务器）和你的应用之间的安全连接。 ATS 确保所有 internet 通信都符合安全连接最佳做法，从而防止直接通过应用或正在使用的库泄露敏感信息。

由于默认情况下，ATS 在为 iOS 9、tvOS 9 和 OS X 10.11 （El Capitan）和更高版本生成的应用中`NSUrlConnection`启用`CFUrl`了`NSUrlSession` ，因此使用或的所有连接都将受 ATS 安全要求。 如果连接不满足这些要求，它们将失败并出现异常。

根据你的 HttpClient 堆栈和 SSL/TLS 实现选择，你可能需要对应用进行修改才能在 ATS 上正常工作。

若要了解有关 ATS 的详细信息，请参阅我们的[应用传输安全指南](~/ios/app-fundamentals/ats.md)。

## <a name="known-issues"></a>已知问题

本部分介绍了 Xamarin 中的 TLS 支持的已知问题。

### <a name="project-failed-to-load-with-error-requested-value-appletls-wasnt-found"></a>项目加载失败，出现错误 "找不到请求的值 AppleTLS"

Xamarin 9.8 引入了一些新的设置，其中包含用于 Xamarin iOS 应用程序的 **.csproj**文件。 如果项目是用较旧版本的 Xamarin 打开的，则这些更改可能会导致问题。 以下屏幕截图是此方案中可能显示的错误消息的示例：

![尝试加载项目时出现错误的屏幕截图，找不到请求的旧值](http-stack-images/tlserror-xs.png)

此错误是由于将`MtouchTlsProvider`设置引入到 Xamarin 9.8 中的项目文件引起的。 如果无法更新到 Xamarin 9.8 （或更高版本），则解决方法是手动编辑 **.csproj**文件应用程序，移除`MtouchTlsprovider`元素，然后保存更改后的项目文件。

以下代码片段是一个在 **.csproj**文件中`MtouchTlsProvider`的设置的示例：

```xml
<MtouchTlsProvider>Default</MtouchTlsProvider>
```

## <a name="related-links"></a>相关链接

- [传输层安全性 (TLS)](~/cross-platform/app-fundamentals/transport-layer-security.md)
- [应用传输安全性](~/ios/app-fundamentals/ats.md)
