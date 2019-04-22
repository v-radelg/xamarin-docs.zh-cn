---
title: Xamarin.Essentials:文本到语音转换
description: Xamarin.Essentials 中的 TextToSpeech 类允许应用程序使用内置的文本到语音转换引擎回讲设备中的文本并查询引擎可以支持的可用语言。
ms.assetid: AEEF03AE-A047-4DF0-B0E8-CC8D9A7B8351
author: jamesmontemagno
ms.author: jamont
ms.date: 11/04/2018
ms.openlocfilehash: 35ef922553cb91aa915c08df03414d1e5f3034cb
ms.sourcegitcommit: 3489c281c9eb5ada2cddf32d73370943342a1082
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2019
ms.locfileid: "59239922"
---
# <a name="xamarinessentials-text-to-speech"></a>Xamarin.Essentials:文本到语音转换

TextToSpeech 类允许应用程序使用内置的文本到语音转换引擎回讲设备中的文本并查询引擎可以支持的可用语言。

## <a name="get-started"></a>入门

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-text-to-speech"></a>使用 Text-to-Speech

在你的类中添加对 Xamarin.Essentials 的引用：

```csharp
using Xamarin.Essentials;
```

Text-to-Speech 通过调用具有文本和可选参数的 `SpeakAsync` 方法工作，并在完成语音样本后返回。

```csharp
public async Task SpeakNowDefaultSettings()
{
    await TextToSpeech.SpeakAsync("Hello World");

    // This method will block until utterance finishes.
}

public void SpeakNowDefaultSettings2()
{
    TextToSpeech.SpeakAsync("Hello World").ContinueWith((t) =>
    {
        // Logic that will run after utterance finishes.

    }, TaskScheduler.FromCurrentSynchronizationContext());
}
```

此方法采用可选的 `CancellationToken`，以便在语音样本启动时将其停止。

```csharp
CancellationTokenSource cts;
public async Task SpeakNowDefaultSettings()
{
    cts = new CancellationTokenSource();
    await TextToSpeech.SpeakAsync("Hello World", cancelToken: cts.Token);

    // This method will block until utterance finishes.
}

public void CancelSpeech()
{
    if (cts?.IsCancellationRequested ?? false)
        return;

    cts.Cancel();
}
```

Text-to-Speech 会自动将同一线程中的语音请求加入队列。

```csharp
bool isBusy = false;
public void SpeakMultiple()
{
    isBusy = true;
    Task.Run(async () =>
    {
        await TextToSpeech.SpeakAsync("Hello World 1");
        await TextToSpeech.SpeakAsync("Hello World 2");
        await TextToSpeech.SpeakAsync("Hello World 3");
        isBusy = false;
    });

    // or you can query multiple without a Task:
    Task.WhenAll(
        TextToSpeech.SpeakAsync("Hello World 1"),
        TextToSpeech.SpeakAsync("Hello World 2"),
        TextToSpeech.SpeakAsync("Hello World 3"))
        .ContinueWith((t) => { isBusy = false; }, TaskScheduler.FromCurrentSynchronizationContext());
}
```

### <a name="speech-settings"></a>语音设置

为了更好地控制如何使用可用于设置音量、音调和区域设置的 `SpeechOptions` 回讲音频。

```csharp
public async Task SpeakNow()
{
    var settings = new SpeechOptions()
        {
            Volume = .75f,
            Pitch = 1.0f
        };

    await TextToSpeech.SpeakAsync("Hello World", settings);
}
```

下面是这些参数的支持值：

| 参数 | 最低 | 最大值 |
| --- | :---: | :---: |
| 音调 | 0 | 2.0 |
| 音量 | 0 | 1.0 |

### <a name="speech-locales"></a>语音区域设置

每个平台支持不同的区域设置，以便使用不同语言和重音回讲文本。 平台具有用于指定区域设置的不同代码和方法，这就是 Xamarin.Essentials 为何提供跨平台 `Locale` 类以及使用 `GetLocalesAsync` 查询区域设置的方法的原因。

```csharp
public async Task SpeakNow()
{
    var locales = await TextToSpeech.GetLocalesAsync();

    // Grab the first locale
    var locale = locales.FirstOrDefault();

    var settings = new SpeechOptions()
        {
            Volume = .75f,
            Pitch = 1.0f,
            Locale = locale
        };

    await TextToSpeech.SpeakAsync("Hello World", settings);
}
```

## <a name="limitations"></a>限制

- 如果跨多个线程调用语音样本队列，则不能予以保证。
- 背景音频播放未受到官方支持。

## <a name="api"></a>API

- [TextToSpeech 源代码](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/TextToSpeech)
- [TextToSpeech API 文档](xref:Xamarin.Essentials.TextToSpeech)
