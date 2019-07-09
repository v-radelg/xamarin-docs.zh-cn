---
title: 使用 Translator API 的文本翻译
description: 可以使用 Microsoft Translator API 翻译语音和文本通过 REST API。 本文介绍如何使用 Microsoft 文本翻译 API 将文本从一种语言到 Xamarin.Forms 应用程序中的另一个翻译。
ms.prod: xamarin
ms.assetid: 68330242-92C5-46F1-B1E3-2395D8823B0C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/08/2017
ms.openlocfilehash: 73d5695b5f08a3f9085e1960260f2e2e4f30d78a
ms.sourcegitcommit: c1d85b2c62ad84c22bdee37874ad30128581bca6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/08/2019
ms.locfileid: "67658694"
---
# <a name="text-translation-using-the-translator-api"></a>使用 Translator API 的文本翻译

[![下载示例](~/media/shared/download.png)下载示例](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoCognitiveServices/)

_可以使用 Microsoft Translator API 翻译语音和文本通过 REST API。本文介绍如何使用 Microsoft 文本翻译 API 将文本从一种语言到 Xamarin.Forms 应用程序中的另一个翻译。_

## <a name="overview"></a>概述

Translator API 有两个组件：

- 文本翻译 REST API 将文本从一种语言翻译成另一种语言的文本。 API 会自动检测已发送之前将其转换的文本的语言。
- 语音翻译 REST API 进行转录语音从一种语言到另一种语言的文本。 API 还集成了朗读已翻译的文本返回的文本到语音转换功能。

本文重点介绍翻译文本从一种语言到另一个使用文本翻译 API。

若要使用文本翻译 API，必须获取 API 密钥。 这可以获得[如何注册 Microsoft 文本翻译 API](/azure/cognitive-services/translator/translator-text-how-to-signup/)。

有关 Microsoft 文本翻译 API 的详细信息，请参阅[Translator 文本 API 文档](/azure/cognitive-services/translator/)。

## <a name="authentication"></a>身份验证

对文本翻译 API 所做的每个请求需要一个 JSON Web 令牌 (JWT) 访问令牌，可以从在认知服务令牌服务获取`https://api.cognitive.microsoft.com/sts/v1.0/issueToken`。 可以通过向令牌服务中，发出 POST 请求来获取令牌指定`Ocp-Apim-Subscription-Key`标题，其中包含作为其值的 API 密钥。

下面的代码示例演示如何请求访问令牌的令牌服务：

```csharp
public AuthenticationService(string apiKey)
{
    subscriptionKey = apiKey;
    httpClient = new HttpClient();
    httpClient.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", apiKey);
}
...
async Task<string> FetchTokenAsync(string fetchUri)
{
    UriBuilder uriBuilder = new UriBuilder(fetchUri);
    uriBuilder.Path += "/issueToken";
    var result = await httpClient.PostAsync(uriBuilder.Uri.AbsoluteUri, null);
    return await result.Content.ReadAsStringAsync();
}
```

返回的访问令牌，它为 Base64 文本时，有 10 分钟的到期时间。 因此，示例应用程序将续订访问令牌每隔 9 分钟。

必须在每个文本翻译 API 中指定的访问令牌调用作为`Authorization`与字符串作为前缀的标头`Bearer`，下面的代码示例中所示：

```csharp
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", bearerToken);
```

有关认知服务令牌服务的详细信息，请参阅[身份验证令牌 API](http://docs.microsofttranslator.com/oauth-token.html)。

## <a name="performing-text-translation"></a>执行文本转换

可以通过在 GET 请求来实现文本翻译`translate`API， `https://api.microsofttranslator.com/v2/http.svc/translate`。 在示例应用程序，`TranslateTextAsync`方法将调用文本转换过程：

```csharp
public async Task<string> TranslateTextAsync(string text)
{
  ...
  string requestUri = GenerateRequestUri(Constants.TextTranslatorEndpoint, text, "en", "de");
  string accessToken = authenticationService.GetAccessToken();
  var response = await SendRequestAsync(requestUri, accessToken);
  var xml = XDocument.Parse(response);
  return xml.Root.Value;
}
```

`TranslateTextAsync`方法生成请求 URI，并从令牌的服务中检索访问令牌。 文本翻译请求然后发送到`translate`API，它将返回包含结果的 XML 响应。 分析 XML 响应，并转换结果返回到调用方法以显示。

有关文本翻译 REST Api 的详细信息，请参阅[Microsoft 文本翻译 API](http://docs.microsofttranslator.com/text-translate.html)。

### <a name="configuring-text-translation"></a>配置文本翻译

文本翻译过程可通过指定 HTTP 查询参数进行配置：

```csharp
string GenerateRequestUri(string endpoint, string text, string to)
{
  string requestUri = endpoint;
  requestUri += string.Format("?text={0}", Uri.EscapeUriString(text));
  requestUri += string.Format("&to={0}", to);
  return requestUri;
}
```

此方法设置要转换为文本的语言和要转换的文本。 Microsoft Translator 支持的语言的列表，请参阅[在 Microsoft 文本翻译 API 中支持的语言](/azure/cognitive-services/translator/languages/)。

> [!NOTE]
> 如果应用程序需要知道文本采用何种语言`Detect`可以调用 API 来检测文本字符串的语言。

### <a name="sending-the-request"></a>发送请求

`SendRequestAsync`方法将向文本翻译 REST API 发出 GET 请求并返回的响应：

```csharp
async Task<string> SendRequestAsync(string url, string bearerToken)
{
    if (httpClient == null)
    {
        httpClient = new HttpClient();
    }
    httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", bearerToken);

    var response = await httpClient.GetAsync(url);
    return await response.Content.ReadAsStringAsync();
}
```

此方法的访问令牌与基础之上构建的 GET 请求`Authorization`标头，使用字符串作为前缀`Bearer`。 然后将 GET 请求发送到`translate`API，其中包含请求 URL 指定的文本以进行翻译，以及要转换为文本的语言。 然后将对响应进行读取，并将其返回给调用的方法。

`translate` API 将在响应中，提供请求的有效，指示请求成功，并且请求的信息包含在响应中发送 HTTP 状态代码 200 （正常）。 有关可能的错误响应的列表，请参阅在响应消息[获取翻译](http://docs.microsofttranslator.com/text-translate.html#!/default/get_Translate)。

### <a name="processing-the-response"></a>处理响应

以 XML 格式返回 API 响应。 下面的 XML 数据显示了典型的成功响应消息：

```xml
<string xmlns="http://schemas.microsoft.com/2003/10/Serialization/">Morgen kaufen gehen ein</string>
```

在示例应用程序，XML 响应被解析为`XDocument`实例，返回到调用方法以显示以下屏幕截图中所示的 XML 根值：

![](text-translation-images/text-translation.png "文本翻译为德语")

## <a name="summary"></a>总结

本文介绍了如何使用 Microsoft 文本翻译 API 将从一种语言的文本翻译到 Xamarin.Forms 应用程序中的另一种语言的文本。 除了将文本翻译，Microsoft Translator API 还可以到另一种语言的文本转录从一种语言的语音。

## <a name="related-links"></a>相关链接

- [Translator 文本 API 文档](/azure/cognitive-services/translator/)。
- [使用 RESTful Web 服务](~/xamarin-forms/data-cloud/web-services/rest.md)
- [Todo 认知服务 （示例）](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoCognitiveServices/)
- [Microsoft 文本翻译 API](http://docs.microsofttranslator.com/text-translate.html)。
