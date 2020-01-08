---
title: 使用人脸 API 感知情感识别
description: 人脸 API 作为输入，图像中采用的面部表情，并返回数据，其中包括在映像中每张人脸的情感集的置信度级别。 此文章介绍了如何使用人脸 API 来识别情感进行评级的 Xamarin.Forms 应用程序。
ms.prod: xamarin
ms.assetid: 19D36A7C-E8D8-43D1-BE80-48DE6C02879A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/10/2018
ms.openlocfilehash: 4dedcb0869c1e965679812239b1de09f07efa875
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75487615"
---
# <a name="perceived-emotion-recognition-using-the-face-api"></a>使用人脸 API 感知情感识别

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todocognitiveservices)

人脸 API 可以执行情感检测，在基于人为普通人的可感知批注的面部表达式中检测愤怒、蔑视、厌恶、恐惧、幸福、中性、悲伤和惊喜。 但是，请务必注意，单独的面部表达式可能不一定表示用户的内部状态。

除了为面部表达式返回情感结果外，人脸 API 还可以返回检测到的人脸的边界框。

通过客户端库，并通过 REST API，可以执行情感识别。 本文重点介绍对执行通过 REST API 的情感识别。 有关 REST API 的详细信息，请参阅[人脸 REST API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)。

人脸 API 还可用于识别在视频中，人员的面部表情，并可以返回其情感概况。 有关详细信息，请参阅[如何实时分析视频](/azure/cognitive-services/face/face-api-how-to-topics/howtoanalyzevideo_face/)。

> [!NOTE]
> 如果还没有 [Azure 订阅](/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing)，可以在开始前创建一个[免费帐户](https://aka.ms/azfree-docs-mobileapps)。

必须获取 API 密钥才能使用人脸 API。 这可以获得[试用认知服务](https://azure.microsoft.com/try/cognitive-services/?api=face-api)。

有关人脸 API 的详细信息，请参阅[人脸 API](/azure/cognitive-services/face/overview/)。

## <a name="authentication"></a>身份验证 （可能为英文网页）

每个请求都会到人脸 API 需要 API 密钥，应将指定的值为`Ocp-Apim-Subscription-Key`标头。 下面的代码示例演示如何添加到的 API 密钥`Ocp-Apim-Subscription-Key`请求标头：

```csharp
public FaceRecognitionService()
{
  _client = new HttpClient();
  _client.DefaultRequestHeaders.Add("ocp-apim-subscription-key", Constants.FaceApiKey);
}
```

未能传递给人脸 API 的一个有效的 API 密钥将导致 401 响应错误。

## <a name="perform-emotion-recognition"></a>执行情感识别

包含图像的 POST 请求，从而执行情感识别`detect`API， `https://[location].api.cognitive.microsoft.com/face/v1.0`，其中`[location]]`是用于获取 API 密钥的区域。 可选的请求参数包括：

- `returnFaceId` – 是否返回 faceIds 检测的人脸。 默认值为 `true`。
- `returnFaceLandmarks` – 是否返回人脸检测到的人脸特征点。 默认值为 `false`。
- `returnFaceAttributes` -是否要分析，并返回指定的一个或多人脸属性。 受支持的人脸属性包括`age`， `gender`， `headPose`， `smile`， `facialHair`， `glasses`， `emotion`， `hair`， `makeup`， `occlusion`， `accessories`， `blur`， `exposure`，和`noise`。 请注意，人脸属性分析其他计算和时间成本。

必须在作为 URL 或二进制数据的 POST 请求正文中放置图像内容。

> [!NOTE]
> 支持的图像文件格式为 JPEG、 PNG、 GIF 和 BMP，和允许的文件大小为 1 KB 到 4 MB。

在示例应用程序，情感识别过程调用通过调用`DetectAsync`方法：

```csharp
Face[] faces = await _faceRecognitionService.DetectAsync(photoStream, true, false, new FaceAttributeType[] { FaceAttributeType.Emotion });
```

此方法调用中指定包含图像数据，应返回 faceIds，不应返回人脸特征点，并应分析的图像的情感的流。 它还指定将数组的形式返回结果`Face`对象。 依次`DetectAsync`方法将调用`detect`执行情感识别的 REST API:

```csharp
public async Task<Face[]> DetectAsync(Stream imageStream, bool returnFaceId, bool returnFaceLandmarks, IEnumerable<FaceAttributeType> returnFaceAttributes)
{
  var requestUrl =
    $"{Constants.FaceEndpoint}/detect?returnFaceId={returnFaceId}" +
    "&returnFaceLandmarks={returnFaceLandmarks}" +
    "&returnFaceAttributes={GetAttributeString(returnFaceAttributes)}";
  return await SendRequestAsync<Stream, Face[]>(HttpMethod.Post, requestUrl, imageStream);
}
```

此方法生成请求 URI，并随后发送到请求`detect`API 通过`SendRequestAsync`方法。

> [!NOTE]
> 在您为您用来获取订阅密钥的人脸 API 调用中，必须使用相同的区域。 例如，如果从订阅密钥的获取`westus`区域中，人脸检测终结点将是`https://westus.api.cognitive.microsoft.com/face/v1.0/detect`。

### <a name="send-the-request"></a>发送请求

`SendRequestAsync`方法向人脸 API 的 POST 请求，并返回结果作为`Face`数组：

```csharp
async Task<TResponse> SendRequestAsync<TRequest, TResponse>(HttpMethod httpMethod, string requestUrl, TRequest requestBody)
{
  var request = new HttpRequestMessage(httpMethod, Constants.FaceEndpoint);
  request.RequestUri = new Uri(requestUrl);
  if (requestBody != null)
  {
    if (requestBody is Stream)
    {
      request.Content = new StreamContent(requestBody as Stream);
      request.Content.Headers.ContentType = new MediaTypeHeaderValue("application/octet-stream");
    }
    else
    {
      // If the image is supplied via a URL
      request.Content = new StringContent(JsonConvert.SerializeObject(requestBody, s_settings), Encoding.UTF8, "application/json");
    }
  }

  HttpResponseMessage responseMessage = await _client.SendAsync(request);
  if (responseMessage.IsSuccessStatusCode)
  {
    string responseContent = null;
    if (responseMessage.Content != null)
    {
      responseContent = await responseMessage.Content.ReadAsStringAsync();
    }
    if (!string.IsNullOrWhiteSpace(responseContent))
    {
      return JsonConvert.DeserializeObject<TResponse>(responseContent, s_settings);
    }
    return default(TResponse);
  }
  else
  {
    ...
  }
  return default(TResponse);
}
```

如果通过流提供该映像，则该方法生成 POST 请求通过包装中的图像流`StreamContent`实例提供基于流的 HTTP 内容。 或者，如果通过 URL 提供该映像，则该方法将生成 POST 请求通过包装中的 URL`StringContent`实例提供基于字符串的 HTTP 内容。

然后将 POST 请求发送到`detect`API。 读取响应，将其反序列化，并返回到调用方法。

`detect` API 将在响应中，提供请求的有效，指示请求成功，并且请求的信息包含在响应中发送 HTTP 状态代码 200 （正常）。 有关可能的错误响应的列表，请参阅[人脸 REST API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)。

### <a name="process-the-response"></a>处理响应

API 响应以 JSON 格式返回。 以下 JSON 数据显示了一个典型的成功响应消息，提供所请求的示例应用程序数据：

```json
[  
   {  
      "faceId":"8a1a80fe-1027-48cf-a7f0-e61c0f005051",
      "faceRectangle":{  
         "top":192,
         "left":164,
         "width":339,
         "height":339
      },
      "faceAttributes":{  
         "emotion":{  
            "anger":0.0,
            "contempt":0.0,
            "disgust":0.0,
            "fear":0.0,
            "happiness":1.0,
            "neutral":0.0,
            "sadness":0.0,
            "surprise":0.0
         }
      }
   }
]
```

成功的响应消息包含人脸条目按降序排序，空响应表示没有检测到的人脸的人脸矩形大小进行排序的数组。 每个识别人脸包含一系列可选人脸属性，由指定的`returnFaceAttributes`自变量`DetectAsync`方法。

在示例应用程序，则 JSON 响应进行反序列化成一个数组`Face`对象。 因为规范化后的评分时解释从人脸 API 的结果，应将检测到的情感解释为具有最高评分的情感到累加为 1。 因此，示例应用程序在图中显示具有最大检测到人脸的最高分数的已识别的情感。 使用以下代码被实现此目的：

```csharp
emotionResultLabel.Text = faces.FirstOrDefault().FaceAttributes.Emotion.ToRankedList().FirstOrDefault().Key;
```

下面的屏幕截图显示了示例应用程序中的情感识别过程的结果：

![](emotion-recognition-images/emotion-recognition.png "Emotion Recognition")

## <a name="related-links"></a>相关链接

- [人脸 API](/azure/cognitive-services/face/overview/)。
- [Todo 认知服务 （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todocognitiveservices)
- [人脸 REST API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
