---
title: 适用于 Android 的 HttpClient 堆栈和 SSL/TLS 实现选择器
description: HttpClient 堆栈和 SSL/TLS 实现选择器确定你的 Xamarin Android 应用将使用的 HttpClient 和 SSL/TLS 实现。
ms.prod: xamarin
ms.assetid: D7ABAFAB-5CA2-443D-B902-2C7F3AD69CE2
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 04/20/2018
ms.openlocfilehash: f9f9b112a083615f9cf1d74d7cf81f5ca4f7901f
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73019237"
---
# <a name="httpclient-stack-and-ssltls-implementation-selector-for-android"></a>适用于 Android 的 HttpClient 堆栈和 SSL/TLS 实现选择器

HttpClient 堆栈和 SSL/TLS 实现选择器确定你的 Xamarin Android 应用将使用的 HttpClient 和 SSL/TLS 实现。

项目必须引用**系统 .net. Http**程序集。

> [!WARNING]
> **2018 年4月**–由于安全要求增加，包括 PCI 合规性，主要云提供商和 web 服务器应停止支持早于1.2 的 TLS 版本。 在 Visual Studio 的早期版本中创建的 Xamarin 项目默认使用较旧版本的 TLS。
>
> 为了确保应用继续使用这些服务器和服务，**应使用如下所示的 "`Android HttpClient`" 和 "`Native TLS 1.2`" 设置来更新 Xamarin 项目，然后重新生成应用并将其重新部署**到用户。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Xamarin HttpClient 配置位于 "**项目选项" > Android 选项**"中，然后单击"**高级选项**"按钮。

下面是针对 TLS 1.2 支持的推荐设置：

[![Visual Studio Android 选项](http-stack-images/android-win-sml.png)](http-stack-images/android-win.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

Xamarin HttpClient 配置位于**项目选项 > > Android 生成设置生成**"，然后单击"**常规**"选项卡。

下面是针对 TLS 1.2 支持的推荐设置：

[![Visual Studio for Mac Android 选项](http-stack-images/android-mac-sml.png)](http-stack-images/android-mac.png#lightbox)

-----

## <a name="alternative-configuration-options"></a>备用配置选项

### <a name="androidclienthandler"></a>AndroidClientHandler

AndroidClientHandler 是委托给本机 Java/OS 代码的新处理程序，而不是在托管代码中实现所有内容。
**建议使用此选项。**

#### <a name="pros"></a>专业人员

- 使用本机 API 以获得更好的性能和更小的可执行文件大小。
- 支持最新标准，例如 TLS 1.2。

#### <a name="cons"></a>各有利弊

- 需要 Android 4.1 或更高版本。
- 某些 HttpClient 功能/选项不可用。

### <a name="managed-httpclienthandler"></a>托管（HttpClientHandler）

托管处理程序是以前的 Xamarin Android 版本随附的完全托管的 HttpClient 处理程序。

#### <a name="pros"></a>专业人员

- 它与 MS .NET 和旧版 Xamarin 版本最兼容（功能）。

#### <a name="cons"></a>各有利弊

- 它未与操作系统完全集成（例如 仅限 TLS 1.0）。
- 通常速度慢得多（例如， 加密）。
- 它需要更多的托管代码，从而创建更大的应用程序。

### <a name="choosing-a-handler"></a>选择处理程序

`AndroidClientHandler` 和 `HttpClientHandler` 之间的选择取决于应用程序的需求。 建议 `AndroidClientHandler` 以获得最新的安全支持，例如：

- 需要 TLS 1.2 + 支持。
- 你的应用面向 Android 4.1 （API 16）或更高版本。
- 需要 `HttpClient`的 TLS 1.2 + 支持。
- 不需要 TLS 1.2 + `WebClient`支持。

如果需要 TLS 1.2 + 支持，但必须支持早于 Android 4.1 的 Android 版本，`HttpClientHandler` 是一个不错的选择。 如果需要 `WebClient`的 TLS 1.2 + 支持，这也是一个不错的选择。

从 Xamarin 8.3 开始，`HttpClientHandler` 默认为 "令人厌烦的 SSL" （`btls`）作为基础 TLS 提供程序。 令人厌烦的 SSL TLS 提供程序具有以下优势：

- 它支持 TLS 1.2 +。
- 它支持所有 Android 版本。
- 它为 `HttpClient` 和 `WebClient`提供 TLS 1.2 + 支持。

使用令人厌烦的 SSL 作为基础 TLS 提供程序的缺点是它可以增加生成的 APK 的大小（增加大约每个受支持 ABI 的额外 APK 大小为1MB）。

从 Xamarin 8.3 开始，默认 TLS 提供程序为 "令人厌烦" SSL （`btls`）。 如果你不想使用镗镗镗的 SSL，可以通过将 `$(AndroidTlsProvider)` 属性设置为 `legacy` 来还原到历史托管 SSL 实现（有关设置生成属性的详细信息，请参阅[生成过程](~/android/deploy-test/building-apps/build-process.md)）。

### <a name="programatically-using-androidclienthandler"></a>使用 `AndroidClientHandler` 以编程方式

`Xamarin.Android.Net.AndroidClientHandler` 是专用于 Xamarin 的 `HttpMessageHandler` 实现。
此类的实例将为所有 HTTP 连接使用本机 `java.net.URLConnection` 实现。 理论上，这将提高 HTTP 性能和更小的 APK 大小。

此代码片段举例说明了如何为 `HttpClient` 类的单个实例显式：

```csharp
// Android 4.1 or higher, Xamarin.Android 6.1 or higher
HttpClient client = new HttpClient(new Xamarin.Android.Net.AndroidClientHandler ());
```

> [!NOTE]
> 基础 Android 设备必须支持 TLS 1.2 （即Android 4.1 及更高版本）。 请注意，TLS 1.2 的官方支持在 Android 5.0 + 中。 但某些设备支持 Android 4.1 + 中的 TLS 1.2。

## <a name="ssltls-implementation-build-option"></a>SSL/TLS 实现生成选项

此项目选项可控制所有 web 请求将使用的基础 TLS 库，`HttpClient` 和 `WebRequest`。 默认情况下，将选择 TLS 1.2：

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[Visual Studio 中的![TLS/SSL 实现组合框](http-stack-images/tls06-vs.png)](http-stack-images/tls05-vs.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

[Visual Studio for Mac 中的![TLS/SSL 实现组合框](http-stack-images/tls06-xs.png)](http-stack-images/tls05-xs.png#lightbox)

-----

例如:

```csharp
var client = new HttpClient();
```

如果 HttpClient 实现已设置为 "**托管**"，tls 实现设置为 "**本机 TLS 1.2 +** "，则 `client` 对象会自动使用托管 `HttpClientHandler`，并为其 HTTP 自动使用 BORINGSSL 库提供的 tls 1。2发出.

但是，如果将**HttpClient 实现**设置为 `AndroidHttpClient`，则所有 `HttpClient` 对象都将使用基础 Java 类 `java.net.URLConnection`，并且不受**TLS/SSL 实现**值的影响。 `WebRequest` 对象将使用 BoringSSL 库。

## <a name="other-ways-to-control-ssltls-configuration"></a>控制 SSL/TLS 配置的其他方式

Xamarin Android 应用程序可以使用三种方法来控制 TLS 设置：

1. 在项目选项中选择 "HttpClient 实现" 和 "默认 TLS 库"。
2. 使用 `Xamarin.Android.Net.AndroidClientHandler`以编程方式使用。
3. 声明环境变量（可选）。

在三个选项中，推荐的方法是使用 Xamarin Android 项目选项为整个应用声明默认 `HttpMessageHandler` 和 TLS。 然后，如有必要，以编程方式实例化 `Xamarin.Android.Net.AndroidClientHandler` 对象。 上面介绍了这些选项。

使用环境变量 &ndash; 的第三个选项 &ndash; 如下所述。

### <a name="declare-environment-variables"></a>声明环境变量

在 Xamarin 中，有两个与 TLS 的使用相关的环境变量：

- 此环境变量 &ndash; `XA_HTTP_CLIENT_HANDLER_TYPE` 声明应用程序将使用的默认 `HttpMessageHandler`。 例如:

    ```csharp
    XA_HTTP_CLIENT_HANDLER_TYPE=Xamarin.Android.Net.AndroidClientHandler
    ```

- `XA_TLS_PROVIDER` &ndash; 此环境变量将声明将使用哪一个 TLS 库，`btls`、`legacy`或 `default` （这与省略此变量相同）：

    ```csharp
    XA_TLS_PROVIDER=btls
    ```

此环境变量通过向项目添加_环境文件_进行设置。 环境文件是 Unix 格式的纯文本文件，具有**AndroidEnvironment**的生成操作：

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Visual Studio 中 AndroidEnvironment 生成操作的屏幕截图。](http-stack-images/tls03-vs.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

![Visual Studio for Mac 中的 AndroidEnvironment 生成操作的屏幕截图。](http-stack-images/tls03-xs.png)

-----

有关环境变量和 Xamarin 的详细信息，请参阅[Xamarin 环境](~/android/deploy-test/environment.md)指南。

## <a name="related-links"></a>相关链接

- [传输层安全性 (TLS)](~/cross-platform/app-fundamentals/transport-layer-security.md)
