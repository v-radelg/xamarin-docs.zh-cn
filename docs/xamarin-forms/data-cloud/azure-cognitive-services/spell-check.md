---
title: 使用必应拼写检查 API 中的拼写检查
description: 必应拼写检查，将执行上下文的拼写检查的文本，提供内联单词拼写错误的建议。 本文介绍如何使用必应拼写检查 REST API 来更正拼写错误的 Xamarin.Forms 应用程序中。
ms.prod: xamarin
ms.assetid: B40EB103-FDC0-45C6-9940-FB4ACDC2F4F9
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/08/2017
ms.openlocfilehash: 924f5403f12250fcfc5f026438d08ed618fb373f
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75487602"
---
# <a name="spell-checking-using-the-bing-spell-check-api"></a>使用必应拼写检查 API 中的拼写检查

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todocognitiveservices)

_必应拼写检查对文本执行上下文拼写检查，并为拼写错误的单词提供内联建议。本文介绍如何使用必应拼写检查 REST API 更正 Xamarin. Forms 应用程序中的拼写错误。_

## <a name="overview"></a>概述

必应拼写检查 REST API 具有两种操作模式，并对 API 发出请求时，必须指定一种模式：

- `Spell` 无需更改任何大小写更正短文本 （最多 9 个字）。
- `Proof` 更正长文本、 提供的大小写更正和基本标点符号和取消积极的更正。

> [!NOTE]
> 如果还没有 [Azure 订阅](/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing)，可以在开始前创建一个[免费帐户](https://aka.ms/azfree-docs-mobileapps)。

若要使用必应拼写检查 API，必须获取 API 密钥。 这可以获得[试用认知服务](https://azure.microsoft.com/try/cognitive-services/)

必应拼写检查 API 支持的语言的列表，请参阅[支持的语言](/azure/cognitive-services/bing-spell-check/bing-spell-check-supported-languages/)。 有关必应拼写检查 API 的详细信息，请参阅[必应拼写检查文档](/azure/cognitive-services/bing-spell-check/)。

## <a name="authentication"></a>身份验证 （可能为英文网页）

每个请求都会向必应拼写检查 API 需要 API 密钥，应将指定的值为`Ocp-Apim-Subscription-Key`标头。 下面的代码示例演示如何添加到的 API 密钥`Ocp-Apim-Subscription-Key`请求标头：

```csharp
public BingSpellCheckService()
{
    httpClient = new HttpClient();
    httpClient.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", Constants.BingSpellCheckApiKey);
}
```

未能传递给必应拼写检查 API 的一个有效的 API 密钥将导致 401 响应错误。

## <a name="performing-spell-checking"></a>执行拼写检查

拼写检查功能可以通过在 GET 或 POST 请求来实现`SpellCheck`API， `https://api.cognitive.microsoft.com/bing/v7.0/SpellCheck`。 当发出 GET 请求，作为查询参数发送要进行拼写检查的文本。 在 POST 请求时，请求正文中发送要进行拼写检查的文本。 GET 请求仅限于拼写检查 1500年个字符，由于查询参数字符串长度限制。 因此，除非短字符串正在拼写检查应通常由 POST 请求。

在示例应用程序，`SpellCheckTextAsync`方法将调用拼写检查过程：

```csharp
public async Task<SpellCheckResult> SpellCheckTextAsync(string text)
{
    string requestUri = GenerateRequestUri(Constants.BingSpellCheckEndpoint, text, SpellCheckMode.Spell);
    var response = await SendRequestAsync(requestUri);
    var spellCheckResults = JsonConvert.DeserializeObject<SpellCheckResult>(response);
    return spellCheckResults;
}
```

`SpellCheckTextAsync`方法生成请求 URI，并随后发送到请求`SpellCheck`API，它将返回包含结果的 JSON 响应。 JSON 响应进行反序列化，与返回给调用方法以显示结果。

### <a name="configuring-spell-checking"></a>配置中的拼写检查

拼写检查过程可通过指定 HTTP 查询参数进行配置：

```csharp
string GenerateRequestUri(string spellCheckEndpoint, string text, SpellCheckMode mode)
{
  string requestUri = spellCheckEndpoint;
  requestUri += string.Format("?text={0}", text);                         // text to spell check
  requestUri += string.Format("&mode={0}", mode.ToString().ToLower());    // spellcheck mode - proof or spell
  return requestUri;
}
```

此方法设置为选中，拼写，拼写检查模式下的文本。

有关必应拼写检查 REST API 的详细信息，请参阅[拼写检查 API v7 引用](/rest/api/cognitiveservices/bing-spell-check-api-v7-reference/)。

### <a name="sending-the-request"></a>发送请求

`SendRequestAsync`方法向必应拼写检查 REST API 发出 GET 请求并返回的响应：

```csharp
async Task<string> SendRequestAsync(string url)
{
    var response = await httpClient.GetAsync(url);
    return await response.Content.ReadAsStringAsync();
}
```

此方法将发送 GET 请求到`SpellCheck`API，使用请求 URL 指定的文本以进行翻译和拼写检查模式。 然后将对响应进行读取，并将其返回给调用的方法。

`SpellCheck` API 将在响应中，提供请求的有效，指示请求成功，并且请求的信息包含在响应中发送 HTTP 状态代码 200 （正常）。 响应对象的列表，请参阅[响应对象](/rest/api/cognitiveservices/bing-spell-check-api-v7-reference#response-objects)。

### <a name="processing-the-response"></a>处理响应

API 响应以 JSON 格式返回。 以下 JSON 数据显示拼写错误的文本的响应消息`Go shappin tommorow`:

```json
{  
   "_type":"SpellCheck",
   "flaggedTokens":[  
      {  
         "offset":3,
         "token":"shappin",
         "type":"UnknownToken",
         "suggestions":[  
            {  
               "suggestion":"shopping",
               "score":1
            }
         ]
      },
      {  
         "offset":11,
         "token":"tommorow",
         "type":"UnknownToken",
         "suggestions":[  
            {  
               "suggestion":"tomorrow",
               "score":1
            }
         ]
      }
   ],
   "correctionType":"High"
}
```

`flaggedTokens`数组包含在文本中标记为正在拼写不正确，或者通过编程方式不正确的单词的数组。 该数组将为空，如果找不到任何拼写或语法错误。 该数组内的标记是：

- `offset` – 的从零开始偏移量从文本字符串的开头到被标记为单词。
- `token` – 拼写不正确或语法不正确的文本字符串中的单词。
- `type` – 错误的单词被标记的类型。 有两个可能值 –`RepeatedToken`和`UnknownToken`。
- `suggestions` – 将更正拼写或语法错误的单词的数组。 数组组成`suggestion`和一个`score`，指示建议的更正是正确的置信度级别。

在示例应用程序，则 JSON 响应反序列化为`SpellCheckResult`实例，返回到调用方法以显示结果。 下面的代码示例演示如何将`SpellCheckResult`进行显示处理实例：

```csharp
var spellCheckResult = await bingSpellCheckService.SpellCheckTextAsync(TodoItem.Name);
foreach (var flaggedToken in spellCheckResult.FlaggedTokens)
{
  TodoItem.Name = TodoItem.Name.Replace(flaggedToken.Token, flaggedToken.Suggestions.FirstOrDefault().Suggestion);
}
```

此代码循环访问`FlaggedTokens`集合和替换任何拼写错误或与第一个建议的源文本中的语法不正确字词。 以下屏幕截图显示之前和之后的拼写检查：

![](spell-check-images/before-spell-check.png "Before Spell Check")

![](spell-check-images/after-spell-check.png "After Spell Check")

> [!NOTE]
> 上面的示例使用 `Replace` 为简单起见，但在大量文本中，它可以替换错误的标记。 API 提供了 `offset` 值，应在生产应用中使用该值来识别源文本中用于执行更新的正确位置。

## <a name="summary"></a>摘要

本文介绍了如何使用必应拼写检查 REST API 来更正拼写错误的 Xamarin.Forms 应用程序中。 必应拼写检查，将执行上下文的拼写检查的文本，提供内联单词拼写错误的建议。

## <a name="related-links"></a>相关链接

- [必应拼写检查文档](/azure/cognitive-services/bing-spell-check/)
- [使用 RESTful Web 服务](~/xamarin-forms/data-cloud/web-services/rest.md)
- [Todo 认知服务 （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todocognitiveservices)
- [必应拼写检查 API v7 引用](/rest/api/cognitiveservices/bing-spell-check-api-v7-reference/)
