---
title: HttpClient 和适用于 iOS/macOS 的 SSL/TLS 实现选择器
description: HttpClient 堆栈和 SSL/TLS 实现选择器确定将由你的 Xamarin iOS、 tvOS 或 macOS 应用的 HttpClient 和 SSL/TLS 实现。
ms.prod: xamarin
ms.assetid: 12101297-BB04-4410-85F0-A0D41B7E6591
author: asb3993
ms.author: amburns
ms.date: 04/20/2018
ms.openlocfilehash: fd48c7148aadd8d156544113e2d719295294bf40
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61261258"
---
# <a name="httpclient-and-ssltls-implementation-selector-for-iosmacos"></a>适用于 iOS/macOS 的 HttpClient 和 SSL/TLS 实现选择器

**HttpClient 实现选择器**适用于 Xamarin.iOS，Xamarin.tvOS 和 Xamarin.Mac 控制其`HttpClient`实现来使用。 您可以切换到使用 iOS、 tvOS 或 macOS 的本机传输协议的实现 (`NSUrlSession`或`CFNetwork`，具体视操作系统而定)。 好的结果是 TLS 1.2 支持，较小的二进制文件，并更快地下载;缺点是，它需要事件循环正在运行的异步执行的操作。

项目必须引用**System.Net.Http**程序集。

> [!WARNING]
> **2018 年 4 月，** – 由于增强的安全性要求，包括 PCI 合规性主要云提供程序和 web 服务器应停止对 TLS 版本低于 1.2 的支持。  在以前版本的 Visual Studio 默认使用 TLS 的较旧版本创建的 Xamarin 项目。
>
> 为了确保您的应用程序继续使用这些服务器和服务，**应更新与 Xamarin 项目`NSUrlSession`设置如下所示，然后重新生成并重新部署您的应用程序**到你的用户。

### <a name="selecting-an-httpclient-stack"></a>选择 HttpClient 堆栈

若要调整`HttpClient`正由您的应用程序：

1. 双击**项目名称**中**解决方案资源管理器**以打开项目选项。
2. 切换到**构建**设置为你的项目 (例如， **iOS 生成**的 Xamarin.iOS 应用程序)。
3. 从**HttpClient 实现**下拉列表中，选择`HttpClient`键入作为以下项之一：**NSUrlSession** （推荐）， **CFNetwork**，或**托管**。

[![从托管、 CFNetwork 或 NSUrlSession 选择 HttpClient 实现](http-stack-images/http-xs-sml.png)](http-stack-images/http-xs.png#lightbox)

> [!TIP]
> 以支持 TLS 1.2`NSUrlSession`建议选项。

### <a name="nsurlsession"></a>NSUrlSession

`NSURLSession`-基于处理程序基于本地`NSURLSession`框架可在 iOS 7 和更高版本。 
**这是建议的设置。**

#### <a name="pros"></a>专业人员

- 它使用本机 Api 进行更好的性能和较小的可执行文件大小。
- 如 TLS 1.2 的最新标准的支持。

#### <a name="cons"></a>缺点

- 需要 iOS 7 或更高版本。
- 某些`HttpClient`功能/选项将不可用。

### <a name="cfnetwork"></a>CFNetwork

`CFNetwork`-基于处理程序基于本地`CFNetwork`framework 推出 iOS 6 和更高版本。

#### <a name="pros"></a>专业人员

- 它使用本机 Api 进行更好的性能和较小的可执行文件大小。
- 对较新的标准，如 TLS 1.2 的支持。

#### <a name="cons"></a>缺点

- 需要 iOS 6 或更高版本。
- 在 watchOS 上不可用。
- 某些 HttpClient 功能/选项不可用。

### <a name="managed"></a>Managed

托管处理程序是随早期版本的 Xamarin 的完全托管的 HttpClient 处理程序。

#### <a name="pros"></a>专业人员

- 它具有的最兼容的功能集与 Microsoft.NET 和较旧的 Xamarin 版本。

#### <a name="cons"></a>缺点

- 它与 Apple 操作系统中不能完全集成，并仅限于 TLS 1.0。 它不能连接保护 web 服务器或云服务在将来。
- 它通常要慢得多在加密方面的违规比本机 Api。
- 它需要更多托管的代码，从而创建更大的应用程序可分发。

### <a name="programmatically-setting-the-httpmessagehandler"></a>以编程方式设置 HttpMessageHandler

除了上面所示的项目范围配置，您还可以实例化`HttpClient`，并插入所需`HttpMessageHandler`通过构造函数，这些代码片段中所示：

```csharp
// This will use the default message handler for the application; as
// set in the Project Options for the project.
HttpClient client = new HttpClient();

// This will create an HttpClient that explicitly uses the CFNetworkHandler
HttpClient client = new HttpClient(new CFNetworkHandler());

// This will create an HttpClient that explicitly uses NSUrlSessionHandler
HttpClient client = new HttpClient(new NSUrlSessionHandler());
```

这样就可以以使用其他`HttpMessageHandler`从中已声明**项目选项**对话框。

## <a name="ssltls-implementation"></a>SSL/TLS 实现

SSL （安全套接字层） 和其后续版本 TLS （传输层安全性） 支持 HTTP 和其他网络连接通过`System.Net.Security.SslStream`。 Xamarin.iOS、 Xamarin.tvOS 或 Xamarin.Mac 的`System.Net.Security.SslStream`实现将调用 Apple 的本机 SSL/TLS 实现，而不是使用 Mono 由提供的托管的实现。 Apple 的本机实现支持 TLS 1.2。

> [!WARNING]
> 即将发布的 Xamarin.Mac 4.8 版本仅支持 macOS 10.9 或更高版本。
> 早期版本的 Xamarin.Mac 支持 macOS 10.7 或更高版本，但这些较旧的 macOS 版本缺少足够的 TLS 基础结构，无法支持 TLS 1.2。 若要面向 macOS 10.7 或 macOS 10.8，请使用 Xamarin.Mac 4.6 或更早版本。

## <a name="app-transport-security"></a>应用传输安全性

Apple_应用程序传输安全_(ATS) 实施 internet 资源 （如应用程序的后端服务器） 和您的应用程序之间的安全连接。 ATS 可确保所有 internet 通信都符合以确保连接安全最佳实践，从而防止意外泄露敏感信息直接通过您的应用程序或正在使用的库。

由于默认情况下，为 iOS 9、 tvOS 9 和 OS X 10.11 (El Capitan) 构建应用程序中启用了 ATS 和更高版本，使用的所有连接`NSUrlConnection`，`CFUrl`或`NSUrlSession`将受到 ATS 安全要求。 如果你的连接不能满足这些要求，它们将失败并出现异常。

根据你的 HttpClient 堆栈和 SSL/TLS 实现选择，您可能需要对你的应用程序能够正确使用 ATS 进行修改。

若要了解有关 ATS 的详细信息，请参阅我们[应用程序传输安全指南](~/ios/app-fundamentals/ats.md)。

## <a name="known-issues"></a>已知问题

本部分介绍 TLS 支持 Xamarin.iOS 中已知的的问题。

### <a name="project-failed-to-load-with-error-requested-value-appletls-wasnt-found"></a>未能加载，出现错误"请求值 AppleTLS 找不到"项目

Xamarin.iOS 9.8 引入了包含一些新设置 **.csproj** Xamarin.iOS 应用程序文件。 与较旧版本 Xamarin.iOS 中打开项目时，这些更改可能会导致问题。 下面的屏幕截图是一个示例可能会显示在此方案中的错误消息：

![尝试加载项目时的错误的屏幕截图请求值旧版找不到](http-stack-images/tlserror-xs.png)

此错误引起的引入`MtouchTlsProvider`将设置为 Xamarin.iOS 9.8 中的项目文件。 如果它不是可更新到 Xamarin.iOS 9.8 （或更高版本），解决方法是手动编辑 **.csproj**文件应用程序中，删除`MtouchTlsprovider`元素，然后保存更改后的项目文件。

以下代码片段示范了什么`MtouchTlsProvider`设置可能看起来像内 **.csproj**文件：

```xml
<MtouchTlsProvider>Default</MtouchTlsProvider>
```

## <a name="related-links"></a>相关链接

- [传输层安全性 (TLS)](~/cross-platform/app-fundamentals/transport-layer-security.md)
- [应用传输安全性](~/ios/app-fundamentals/ats.md)
