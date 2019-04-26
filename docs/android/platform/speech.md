---
title: Android 语音
description: 本文介绍如何使用功能非常强大 Android.Speech 命名空间的基础知识。 从诞生，Android 已经能够识别语音并将其输出为文本。 它是一个相对简单的过程。 对于文本到语音，但是，该过程是更为复杂，因为不仅不语音引擎必须考虑到，但还语言可用且已安装从文本到语音 (TTS) 系统。
ms.prod: xamarin
ms.assetid: FA3B8EC4-34D2-47E3-ACEA-BD34B28115B9
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/02/2018
ms.openlocfilehash: e88f6e24cbf4c8b2f0c0486c6408e234e87066cc
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61228567"
---
# <a name="android-speech"></a>Android 语音

_本文介绍如何使用功能非常强大 Android.Speech 命名空间的基础知识。从诞生，Android 已经能够识别语音并将其输出为文本。它是一个相对简单的过程。对于文本到语音，但是，该过程是更为复杂，因为不仅不语音引擎必须考虑到，但还语言可用且已安装从文本到语音 (TTS) 系统。_

## <a name="speech-overview"></a>语音概述

具有系统，其中"了解"人类语音和 enunciates 正在键入的内容 — 语音转文本和文本到语音转换 — 如自然的沟通，与我们的设备在需求提高是日益增长区域中的移动开发。 很多情况下，具有文本将转换为语音，或反之，是非常有用的工具，可将合并到 android 应用程序的功能。

例如，使用向下移动电话使用，同时还能降低 clamp，用户需要运行他们的设备一手免费方式。 数不清的不同 Android 窗体因素 — 例如 Android Wear —，不断扩大的那些包含能够使用 Android 设备 （如平板电脑和记事本） 创建了更大的焦点上 TTS 的众多应用程序。

Google 提供使用 Android.Speech 命名空间中更丰富的 Api 开发人员，以涵盖大多数情况下的"语音识别"（如 for the blind 而设计的软件） 的设备。  该命名空间包含设施以允许文本转换为通过语音`Android.Speech.Tts`，用于执行转换，以及许多的引擎控制`RecognizerIntent`s 允许语音，以将转换为文本。

在设施的语音，以了解需要，在基于使用的硬件的限制。 不太可能在设备成功解释所说到它在每个语言中可用的所有内容。

## <a name="requirements"></a>要求

有本指南中，你的设备麦克风和扬声器以外没有特殊要求。

解释语音的 Android 设备的核心是使用`Intent`相对应的`OnActivityResult`。
它是重要的是，不过，若要识别不理解语音 — 但解释为文本。 差异十分重要。

### <a name="the-difference-between-understanding-and-interpreting"></a>了解和解释之间的差异

了解简单定义是您将能够通过的语调和上下文确定的讲述进行的真正意义。 只需解释意味着需要单词和输出它们在另一个窗体中。

请考虑使用日常对话中的以下简单示例： 

<kbd>喂，你好吗？</kbd>

而无需转折点 （侧重于特定的单词或单词的某些部分），它是一个简单的问题。 但是，如果慢的速度应用于线条，侦听的人会检测或提问者不是太高兴，并可能需要 cheering 提问者是 unwell。 如果焦点位于"是"，请求的人员是通常更感兴趣的响应。

不带相当强大音频处理以使利用转折点和一定程度的人工智能 (AI) 以了解上下文，软件甚至不能以了解所说的内容 — 的最简单的手机可以执行操作是将语音转换为文本。

## <a name="setting-up"></a>设置

使用语音系统之前，最好始终检查并确保设备的麦克风。 将有意义尝试 Kindle 或 Google 注意板上没有安装麦克风的情况下运行你的应用。

下面的代码示例演示如果麦克风可用，如果没有，请查询创建警报。 如果没有麦克风可用此时您将退出活动或禁用语音录制功能。

```csharp
string rec = Android.Content.PM.PackageManager.FeatureMicrophone;
if (rec != "android.hardware.microphone")
{
    var alert = new AlertDialog.Builder(recButton.Context);
    alert.SetTitle("You don't seem to have a microphone to record with");
    alert.SetPositiveButton("OK", (sender, e) =>
    {
        return;
    });
    alert.Show();
}
```

### <a name="creating-the-intent"></a>创建意向

语音系统意向使用特定类型的调用的意图`RecognizerIntent`。 此意向控制大量参数，包括多长时间等待用静音，直到记录被认为比，以识别并输出，任何其他语言和要包括在任何文本`Intent`的模式对话框作为指令的方式。 此代码片段`VOICE`是`readonly int`用于识别中的`OnActivityResult`。

```csharp
var voiceIntent = new Intent(RecognizerIntent.ActionRecognizeSpeech);
voiceIntent.PutExtra(RecognizerIntent.ExtraLanguageModel, RecognizerIntent.LanguageModelFreeForm);
voiceIntent.PutExtra(RecognizerIntent.ExtraPrompt, Application.Context.GetString(Resource.String.messageSpeakNow));
voiceIntent.PutExtra(RecognizerIntent.ExtraSpeechInputCompleteSilenceLengthMillis, 1500);
voiceIntent.PutExtra(RecognizerIntent.ExtraSpeechInputPossiblyCompleteSilenceLengthMillis, 1500);
voiceIntent.PutExtra(RecognizerIntent.ExtraSpeechInputMinimumLengthMillis, 15000);
voiceIntent.PutExtra(RecognizerIntent.ExtraMaxResults, 1);
voiceIntent.PutExtra(RecognizerIntent.ExtraLanguage, Java.Util.Locale.Default);
StartActivityForResult(voiceIntent, VOICE);
```

### <a name="conversion-of-the-speech"></a>语音的转换

从语音解释的文本将中传递`Intent`，其返回时该活动完成后，通过访问`GetStringArrayListExtra(RecognizerIntent.ExtraResults)`。 这将返回`IList<string>`，而该索引可用于并显示，具体取决于语言中调用方意向请求数量 (其指定`RecognizerIntent.ExtraMaxResults`)。 与任何列表，它是值得选择，以确保没有要显示数据。

当侦听的返回值时`StartActivityForResult`，则`OnActivityResult`方法有提供。

在以下示例中，`textBox`是`TextBox`用于输出决定什么。 它同样可用于传递给解释器，并从某种形式的文本，该应用程序可以比较文本和分支到的应用程序其他部分。

```csharp
protected override void OnActivityResult(int requestCode, Result resultVal, Intent data)
{
    if (requestCode == VOICE)
    {
        if (resultVal == Result.Ok)
        {
            var matches = data.GetStringArrayListExtra(RecognizerIntent.ExtraResults);
            if (matches.Count != 0)
            {
                string textInput = textBox.Text + matches[0];
                textBox.Text = textInput;
                switch (matches[0].Substring(0, 5).ToLower())
                {
                    case "north":
                        MovePlayer(0);
                        break;
                    case "south":
                        MovePlayer(1);
                        break;
                }
            }
            else
            {
                textBox.Text = "No speech was recognised";
            }
        }
        base.OnActivityResult(requestCode, resultVal, data);
    }
}
```

## <a name="text-to-speech"></a>文本到语音转换

文本到语音转换刚好与之相反的语音到文本并不依赖于两个关键组件;文本到语音转换引擎正在设备上安装并正在安装一种语言。

默认值，Android 设备有很大程度上，安装 Google TTS 服务和至少一种语言。 这建立当设备首次设置时，并且将根据设备是时 （例如，在德国设置手机将安装德语的语言，而另一个 America 中将具有美国英语）。

### <a name="step-1---instantiating-texttospeech"></a>步骤 1-实例化 TextToSpeech

`TextToSpeech` 可能需要最多 3 个参数前, 两个需要与第三个可选的 (`AppContext`， `IOnInitListener`， `engine`)。 侦听器用于绑定到的服务和失败的测试具有被任意数量的可用 Android 的文本到语音转换引擎的引擎。 至少，设备将有 Google 的引擎。

### <a name="step-2---finding-the-languages-available"></a>步骤 2-查找可用的语言

`Java.Util.Locale`类包含一个名为的有用方法`GetAvailableLocales()`。 然后可以对已安装的语言测试这一系列语音引擎支持的语言。

这是普通的问题生成的"了解"语言的列表。 始终会默认语言 （用户设置时他们首次设置其设备的语言），因此，在此示例`List<string>`具有"Default"作为第一个参数，将根据的结果填充列表的其余部分`textToSpeech.IsLanguageAvailable(locale)`。

```csharp
var langAvailable = new List<string>{ "Default" };
var localesAvailable = Java.Util.Locale.GetAvailableLocales().ToList();
foreach (var locale in localesAvailable)
{
    var res = textToSpeech.IsLanguageAvailable(locale);
    switch (res)
    {
        case LanguageAvailableResult.Available:
          langAvailable.Add(locale.DisplayLanguage);
          break;
        case LanguageAvailableResult.CountryAvailable:
          langAvailable.Add(locale.DisplayLanguage);
          break;
        case LanguageAvailableResult.CountryVarAvailable:
          langAvailable.Add(locale.DisplayLanguage);
          break;
    }
}
langAvailable = langAvailable.OrderBy(t => t).Distinct().ToList();
```

此代码将调用[TextToSpeech.IsLanguageAvailable](https://developer.xamarin.com/api/member/Android.Speech.Tts.TextToSpeech.IsLanguageAvailable/p/Java.Util.Locale/)要测试是否已在设备上存在给定区域设置的语言包。 此方法返回[LanguageAvailableResult](https://developer.xamarin.com/api/type/Android.Speech.Tts.LanguageAvailableResult/)，指示是否可传递的区域设置的语言。 如果`LanguageAvailableResult`指示的语言是`NotSupported`，没有任何语音包可用 （即使对于下载），然后针对该语言。 如果`LanguageAvailableResult`设置为`MissingData`，则可能如下所述步骤 4 中下载新的语言包。

### <a name="step-3---setting-the-speed-and-pitch"></a>步骤 3-设置速度和间距

Android 允许用户通过更改 alter 语音的声音`SpeechRate`和`Pitch`（速度和语音的音的频率）。 这是由从 0 为 1，与"normal"语音为两个 1。

### <a name="step-4---testing-and-loading-new-languages"></a>步骤 4-测试和加载新语言

下载新的语言使用执行`Intent`。 此目的的结果将导致[OnActivityResult](https://developer.xamarin.com/api/member/Android.App.Activity.OnActivityResult/)要调用的方法。 与语音到文本的示例不同 (使用哪一种[RecognizerIntent](https://developer.xamarin.com/api/type/Android.Speech.RecognizerIntent/)作为`PutExtra`参数`Intent`)，测试和加载`Intent`s 是`Action`-基于：

-   [TextToSpeech.Engine.ActionCheckTtsData](https://developer.xamarin.com/api/field/Android.Speech.Tts.TextToSpeech+Engine.ActionCheckTtsData/) &ndash;从平台中启动活动`TextToSpeech`引擎来验证正确安装和在设备上的语言资源的可用性。

-   [TextToSpeech.Engine.ActionInstallTtsData](https://developer.xamarin.com/api/field/Android.Speech.Tts.TextToSpeech+Engine.ActionInstallTtsData/) &ndash;启动提示用户下载需要的语言的活动。

下面的代码示例说明了如何使用这些操作来测试语言资源和下载新的语言：

```csharp
var checkTTSIntent = new Intent();
checkTTSIntent.SetAction(TextToSpeech.Engine.ActionCheckTtsData);
StartActivityForResult(checkTTSIntent, NeedLang);
//
protected override void OnActivityResult(int req, Result res, Intent data)
{
    if (req == NeedLang)
    {
        var installTTS = new Intent();
        installTTS.SetAction(TextToSpeech.Engine.ActionInstallTtsData);
        StartActivity(installTTS);
    }
}
```

`TextToSpeech.Engine.ActionCheckTtsData` 语言资源的可用性测试。 `OnActivityResult` 此测试完成时调用。 如果需要下载，语言资源`OnActivityResult`激发一次`TextToSpeech.Engine.ActionInstallTtsData`操作启动的活动，允许用户下载需要的语言。 请注意，此`OnActivityResult`实现不会检查`Result`代码，因为在此简化的示例中，确定已经需要下载的语言包。

`TextToSpeech.Engine.ActionInstallTtsData`操作的原因**Google TTS 语音数据**活动以选择要下载的语言显示给用户：

![Google TTS 语音数据活动](speech-images/01-google-tts-voice-data.png)

例如，用户可能选择法语，并单击下载图标下载法语语音数据：

![下载法语语言的示例](speech-images/02-selecting-french.png)

在下载完成后将自动发生此类数据的安装。


### <a name="step-5---the-ioninitlistener"></a>步骤 5-IOnInitListener

有关用于转换文本到语音，接口方法的活动`OnInit`来实现 (这是指定的实例化的第二个参数`TextToSpeech`类)。 此初始化该侦听器并测试结果。

侦听器应测试两个`OperationResult.Success`和`OperationResult.Failure`最小值。
下面的示例显示了这一：

```csharp
void TextToSpeech.IOnInitListener.OnInit(OperationResult status)
{
    // if we get an error, default to the default language
    if (status == OperationResult.Error)
        textToSpeech.SetLanguage(Java.Util.Locale.Default);
    // if the listener is ok, set the lang
    if (status == OperationResult.Success)
        textToSpeech.SetLanguage(lang);
}
```

## <a name="summary"></a>总结

在本指南中我们已经探讨将文本到语音和语音转换为文本和如何将其包含在你自己的应用中的可能的方法的基础知识。 而它们并未涵盖每个特定情况下，您现在应具有基本了解如何解释语音、 如何安装新语言，以及如何提高您的应用程序的 inclusivity。



## <a name="related-links"></a>相关链接

- [Xamarin.Forms DependencyService](https://developer.xamarin.com/samples/UsingDependencyService/)
- [文本到语音转换 （示例）](https://developer.xamarin.com/samples/monodroid/PlatformFeatures/TextToSpeech)
- [语音转文本 （示例）](https://developer.xamarin.com/samples/monodroid/PlatformFeatures/SpeechToText)
- [Android.Speech 命名空间](https://developer.xamarin.com/api/namespace/Android.Speech/)
- [Android.Speech.Tts 命名空间](https://developer.xamarin.com/api/namespace/Android.Speech.Tts/)
