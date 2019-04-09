---
title: HttpClient 堆栈和适用于 Android 的 SSL/TLS 实现选择器
description: HttpClient 堆栈和 SSL/TLS 实现选择器确定将由您的 Xamarin.Android 应用程序的 HttpClient 和 SSL/TLS 实现。
ms.prod: xamarin
ms.assetid: D7ABAFAB-5CA2-443D-B902-2C7F3AD69CE2
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/20/2018
ms.openlocfilehash: a3704552c8fc147588919ecdde2813e831237d89
ms.sourcegitcommit: cc750b0d8086ed14f84cd8eb9a06f45c719b3cf4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/08/2019
ms.locfileid: "59239896"
---
# <a name="httpclient-stack-and-ssltls-implementation-selector-for-android"></a>HttpClient 堆栈和适用于 Android 的 SSL/TLS 实现选择器

HttpClient 堆栈和 SSL/TLS 实现选择器确定将由您的 Xamarin.Android 应用程序的 HttpClient 和 SSL/TLS 实现。

项目必须引用**System.Net.Http**程序集。

> [!WARNING]
> **2018 年 4 月，** – 由于增强的安全性要求，包括 PCI 合规性主要云提供程序和 web 服务器应停止对 TLS 版本低于 1.2 的支持。  在以前版本的 Visual Studio 默认使用 TLS 的较旧版本创建的 Xamarin 项目。
>
> 为了确保您的应用程序继续使用这些服务器和服务，**应更新与 Xamarin 项目`Android HttpClient`并`Native TLS 1.2`设置如下所示，然后重新生成并重新部署您的应用程序**到你用户。

# [<a name="visual-studio"></a>Visual Studio](#tab/windows)

Xamarin.Android HttpClient 配置处于**项目选项 > Android 选项**，然后单击**高级选项**按钮。

以下是建议的设置以支持 TLS 1.2:

[![Visual Studio Android Options](http-stack-images/android-win-sml.png)](http-stack-images/android-win.png#lightbox)


# [<a name="visual-studio-for-mac"></a>Visual Studio for Mac](#tab/macos)

Xamarin.Android HttpClient 配置处于**项目选项 > 生成 > Android 生成**设置，然后单击**常规**选项卡。

以下是建议的设置以支持 TLS 1.2:

[![Visual Studio for Mac Android Options](http-stack-images/android-mac-sml.png)](http-stack-images/android-mac.png#lightbox)

-----

## <a name="alternative-configuration-options"></a>备用配置选项

### <a name="androidclienthandler"></a>AndroidClientHandler

AndroidClientHandler 是委托给本机 Java/OS 代码而不是在托管代码中实现的所有内容的新处理程序。
**这是建议的选项。**

#### <a name="pros"></a>专业人员

- 更好的性能和较小的可执行文件大小对使用本机 API。
- 例如支持的最新标准。 TLS 1.2。

#### <a name="cons"></a>缺点

- 需要 Android 4.1 或更高版本。
- 某些 HttpClient 功能/选项不可用。

### <a name="managed-httpclienthandler"></a>托管 (HttpClientHandler)

托管处理程序是完全托管的 HttpClient 处理程序已附带先前的 Xamarin.Android 版本。

#### <a name="pros"></a>专业人员

- 它是最兼容 （功能） 与 MS.NET 和较旧的 Xamarin 版本。

#### <a name="cons"></a>缺点

- 它不完全集成与操作系统 （例如。 仅限 TLS 1.0）。
- 它是通常要慢得多 （例如。 加密） 比本机 API。
- 它需要更多托管的代码中，创建更大的应用程序。



### <a name="choosing-a-handler"></a>选择一个处理程序

之间的选择`AndroidClientHandler`和`HttpClientHandler`取决于应用程序的需求。 `AndroidClientHandler` 建议的最新的安全支持，例如。

-   需要使用 TLS 1.2 + 支持。
-   您的应用程序定目标到 Android 4.1 (API 16) 或更高版本。
-   需要 TLS 1.2 + 支持`HttpClient`。
-   不需要 TLS 1.2 + 支持`WebClient`。

`HttpClientHandler` 是一个不错的选择，如果您需要 TLS 1.2 + 支持，但必须支持 Android 4.1 之前的 Android 版本。 它也是一个不错的选择，如果您需要 TLS 1.2 + 支持`WebClient`。

从开始 Xamarin.Android 8.3`HttpClientHandler`默认为令人乏味的 SSL (`btls`) 为基础的 TLS 提供程序。 令人乏味的 SSL TLS 提供程序提供以下优势：

-   它支持 TLS 1.2 +。
-   它支持所有的 Android 版本。
-   它提供了两个的 TLS 1.2 + 支持`HttpClient`和`WebClient`。

将令人乏味的 SSL 用作基础 TLS 提供程序的缺点是它可以增加对生成的 APK （它将添加其他每个受支持的 ABI 的 APK 大小的大约 1 MB） 的大小。

从 Xamarin.Android 8.3 开始，默认 TLS 提供程序是令人乏味的 SSL (`btls`)。 如果您不想要使用令人乏味的 SSL，你可以通过设置还原到历史托管 SSL 实施`$(AndroidTlsProvider)`属性设置为`legacy`(有关设置生成属性的详细信息，请参阅[Build 过程](~/android/deploy-test/building-apps/build-process.md))。


### <a name="programatically-using-androidclienthandler"></a>以编程方式使用 `AndroidClientHandler`

`Xamarin.Android.Net.AndroidClientHandler`是`HttpMessageHandler`专门适用于 Xamarin.Android 的实现。
此类的实例将使用本机`java.net.URLConnection`实现适用于所有 HTTP 连接。 从理论上讲，这将提供 HTTP 性能和较小的 APK 大小的增加。

此代码片段示范了如何为的单个实例显式`HttpClient`类：

```csharp
// Android 4.1 or higher, Xamarin.Android 6.1 or higher
HttpClient client = new HttpClient(new Xamarin.Android.Net.AndroidClientHandler ());
```

> [!NOTE]
> 基础 Android 设备必须支持 TLS 1.2 (ie。Android 4.1 及更高版本)


## <a name="ssltls-implementation-build-option"></a>SSL/TLS 实现生成选项

此项目选项，可以控制哪些基础 TLS 库将由所有 web 请求，同时`HttpClient`和`WebRequest`。 默认情况下，选择 TLS 1.2:

# [<a name="visual-studio"></a>Visual Studio](#tab/windows)

[![TLS/SSL 实现组合框在 Visual Studio](http-stack-images/tls06-vs.png)](http-stack-images/tls05-vs.png#lightbox)

# [<a name="visual-studio-for-mac"></a>Visual Studio for Mac](#tab/macos)

[![TLS/SSL 实现组合框在 Visual Studio for Mac](http-stack-images/tls06-xs.png)](http-stack-images/tls05-xs.png#lightbox)

-----

例如：

```csharp
var client = new HttpClient();
```

如果 HttpClient 实现已设置为**托管**TLS 实现设置为**本机 TLS 1.2 +**，然后`client`对象将自动使用托管`HttpClientHandler`和TLS 1.2 （由 BoringSSL 库提供） 用于其 HTTP 请求。

但是，如果**HttpClient 实现**设置为`AndroidHttpClient`，然后所有`HttpClient`对象将使用基础 Java 类`java.net.URLConnection`并将不会影响**实现TLS/SSL**值。 `WebRequest` 对象将使用 BoringSSL 库。

## <a name="other-ways-to-control-ssltls-configuration"></a>其他方法来控制 SSL/TLS 配置

有三种方法的 Xamarin.Android 应用程序可以控制的 TLS 设置：

1. 项目选项中选择的 HttpClient 实现和默认 TLS 库。
2. 以编程方式使用`Xamarin.Android.Net.AndroidClientHandler`。
3. 环境变量声明 （可选）。

建议的方法是使用 Xamarin.Android 项目选项来声明默认值的三个选项，`HttpMessageHandler`和 TLS 对整个应用程序。 然后，如有必要，以编程方式实例化`Xamarin.Android.Net.AndroidClientHandler`对象。 这些选项是上面所述。

第三个选项&ndash;使用环境变量&ndash;如下所述。

### <a name="declare-environment-variables"></a>声明的环境变量

有两个与在 Xamarin.Android 中的 TLS 的使用相关的环境变量：

- `XA_HTTP_CLIENT_HANDLER_TYPE` &ndash; 此环境变量声明的默认`HttpMessageHandler`将使用该应用程序。 例如：

    ```csharp
    XA_HTTP_CLIENT_HANDLER_TYPE=Xamarin.Android.Net.AndroidClientHandler
    ```

- `XA_TLS_PROVIDER` &ndash; 此环境变量将声明的 TLS 库将使用，或者`btls`， `legacy`，或`default`（这是与省略此变量相同）：

    ```csharp
    XA_TLS_PROVIDER=btls
    ```

通过添加设置此环境变量_环境文件_到项目。 环境文件是 Unix 格式的纯文本格式的文件的生成操作**AndroidEnvironment**:

# [<a name="visual-studio"></a>Visual Studio](#tab/windows)

![在 Visual Studio 中的 AndroidEnvironment 生成操作的屏幕截图。](http-stack-images/tls03-vs.png)

# [<a name="visual-studio-for-mac"></a>Visual Studio for Mac](#tab/macos)

![屏幕截图 AndroidEnvironment 生成操作在 Visual Studio for mac。](http-stack-images/tls03-xs.png)

-----

请参阅[Xamarin.Android 环境](~/android/deploy-test/environment.md)有关环境变量和 Xamarin.Android 的更多详细信息的指南。


## <a name="related-links"></a>相关链接

- [传输层安全性 (TLS)](~/cross-platform/app-fundamentals/transport-layer-security.md)
