---
title: 使用 Microsoft 语音 API 的语音识别
description: Microsoft 语音 API 是基于云的 API，提供了用于处理口述的语言的算法。 本文介绍如何使用 Microsoft 语音识别 REST API 将音频转换为 Xamarin.Forms 应用程序中的文本。
ms.prod: xamarin
ms.assetid: B435FF6B-8785-48D9-B2D9-1893F5A87EA1
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/08/2017
ms.openlocfilehash: eca079972f4e46c0cf60c4749658ff9a7fe1791b
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75489799"
---
# <a name="speech-recognition-using-the-microsoft-speech-api"></a>使用 Microsoft 语音 API 的语音识别

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todocognitiveservices)

_Microsoft Speech API 是一种基于云的 API，它提供用于处理口述语言的算法。本文介绍如何使用 Microsoft 语音识别 REST API 将音频转换为 Xamarin 应用程序中的文本。_

## <a name="overview"></a>概述

Microsoft 语音 API 有两个组件：

- 将语音转换为文本的语音识别 API。 可以通过 REST API、 客户端库或服务库执行语音识别。
- 文本到语音转换 API 将文本转换成口头字词。 通过 REST API 执行文本到语音转换。

本文重点介绍执行通过 REST API 的语音识别。 客户端和服务库支持返回部分结果，REST API 只能返回单个识别结果，而无需任何部分结果。

> [!NOTE]
> 如果还没有 [Azure 订阅](/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing)，可以在开始前创建一个[免费帐户](https://aka.ms/azfree-docs-mobileapps)。

若要使用 Microsoft 语音 API，必须获取 API 密钥。 这可以通过 Azure 获得[门户](https://portal.azure.com/)。 有关详细信息，请参阅[Azure 门户中创建认知服务帐户](/azure/cognitive-services/cognitive-services-apis-create-account)。

有关 Microsoft 语音 API 的详细信息，请参阅[Microsoft 语音 API 文档](/azure/cognitive-services/speech/home/)。

## <a name="authentication"></a>身份验证 （可能为英文网页）

Microsoft 语音 REST API 对所做的每个请求需要一个 JSON Web 令牌 (JWT) 访问令牌，可以从在认知服务令牌服务获取`https://api.cognitive.microsoft.com/sts/v1.0/issueToken`。 可以通过向令牌服务中，发出 POST 请求来获取令牌指定`Ocp-Apim-Subscription-Key`标题，其中包含作为其值的 API 密钥。

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

必须在每个 Microsoft 语音 REST API 中指定的访问令牌调用作为`Authorization`与字符串作为前缀的标头`Bearer`，下面的代码示例中所示：

```csharp
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", bearerToken);
```

未能将有效的访问令牌传递到 Microsoft 语音 REST API 将导致 403 响应错误。

## <a name="performing-speech-recognition"></a>执行语音识别

语音识别实现对的 POST 请求，从而`recognition`API， `https://speech.platform.bing.com/speech/recognition/`。 单个请求不能包含超过 10 秒的音频，并且总请求持续时间不能超过 14 秒。

必须在 wav 格式中的请求的 POST 正文中放置的音频内容。

在示例应用程序，`RecognizeSpeechAsync`方法将调用语音识别过程：

```csharp
public async Task<SpeechResult> RecognizeSpeechAsync(string filename)
{
    ...

    // Read audio file to a stream
    var file = await PCLStorage.FileSystem.Current.LocalStorage.GetFileAsync(filename);
    var fileStream = await file.OpenAsync(PCLStorage.FileAccess.Read);

    // Send audio stream to Bing and deserialize the response
    string requestUri = GenerateRequestUri(Constants.SpeechRecognitionEndpoint);
    string accessToken = authenticationService.GetAccessToken();
    var response = await SendRequestAsync(fileStream, requestUri, accessToken, Constants.AudioContentType);
    var speechResult = JsonConvert.DeserializeObject<SpeechResult>(response);

    fileStream.Dispose();
    return speechResult;
}
```

音频录制为 PCM wav 数据，每个特定于平台的项目中，`RecognizeSpeechAsync`方法使用`PCLStorage`NuGet 包，以打开音频文件作为流。 语音识别请求生成 URI 和一个访问令牌进行检索的令牌服务。 语音识别请求发布到`recognition`API，它将返回包含结果的 JSON 响应。 JSON 响应进行反序列化，与返回给调用方法以显示结果。

### <a name="configuring-speech-recognition"></a>配置语音识别

可以通过指定 HTTP 查询参数来配置语音识别过程：

```csharp
string GenerateRequestUri(string speechEndpoint)
{
    // To build a request URL, you should follow:
    // https://docs.microsoft.com/azure/cognitive-services/speech/getstarted/getstartedrest
    string requestUri = speechEndpoint;
    requestUri += @"dictation/cognitiveservices/v1?";
    requestUri += @"language=en-us";
    requestUri += @"&format=simple";
    System.Diagnostics.Debug.WriteLine(requestUri.ToString());
    return requestUri;
}
```

通过执行的主要配置`GenerateRequestUri`方法是设置的音频内容的区域设置。 有关支持的区域设置的列表，请参阅[支持的语言](/azure/cognitive-services/speech/api-reference-rest/supportedlanguages/)。

### <a name="sending-the-request"></a>发送请求

`SendRequestAsync`方法将向 Microsoft 语音 REST API 发出 POST 请求并返回的响应：

```csharp
async Task<string> SendRequestAsync(Stream fileStream, string url, string bearerToken, string contentType)
{
    if (httpClient == null)
    {
        httpClient = new HttpClient();
    }
    httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", bearerToken);

    var content = new StreamContent(fileStream);
    content.Headers.TryAddWithoutValidation("Content-Type", contentType);
    var response = await httpClient.PostAsync(url, content);
    return await response.Content.ReadAsStringAsync();
}
```

此方法生成的 POST 请求：

- 包装中的音频流`StreamContent`实例提供基于流的 HTTP 内容。
- 设置`Content-Type`到请求的标头`audio/wav; codec="audio/pcm"; samplerate=16000`。
- 添加到访问令牌`Authorization`标头，使用字符串作为前缀`Bearer`。

然后将 POST 请求发送到`recognition`API。 然后将对响应进行读取，并将其返回给调用的方法。

`recognition` API 将在响应中，提供请求的有效，指示请求成功，并且请求的信息包含在响应中发送 HTTP 状态代码 200 （正常）。 有关可能的错误响应的列表，请参阅[故障排除](/azure/cognitive-services/speech/troubleshooting)。

### <a name="processing-the-response"></a>处理响应

API 响应以 JSON 格式返回已识别的文本包含在与`name`标记。 以下 JSON 数据显示了典型的成功响应消息：

```json
{  
   "RecognitionStatus":"Success",
   "DisplayText":"Go shopping tomorrow.",
   "Offset":16000000,
   "Duration":17100000
}
```

在示例应用程序，则 JSON 响应反序列化为`SpeechResult`实例，与结果返回到调用方法以显示，如以下屏幕截图中所示：

![](speech-recognition-images/speech-recognition.png "Speech Recognition")

## <a name="summary"></a>摘要

本文介绍了如何使用 Microsoft 语音 REST API 将音频转换为 Xamarin.Forms 应用程序中的文本。 除了执行语音识别，Microsoft 语音 API 还可以将文本转换成口语。

## <a name="related-links"></a>相关链接

- [Microsoft 语音 API 文档](/azure/cognitive-services/speech/home/)。
- [使用 RESTful Web 服务](~/xamarin-forms/data-cloud/web-services/rest.md)
- [Todo 认知服务 （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todocognitiveservices)
