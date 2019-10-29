---
title: Android 语音
description: 本文介绍使用非常强大的 Android. 语音命名空间的基本知识。 自其开始，Android 能够识别语音并将其作为文本输出。 这是一个相对简单的过程。 不过，对于文本到语音转换，此过程更加复杂，因为它不仅需要考虑语音引擎，还必须考虑到从文本到语音（TTS）系统中提供和安装的语言。
ms.prod: xamarin
ms.assetid: FA3B8EC4-34D2-47E3-ACEA-BD34B28115B9
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 04/02/2018
ms.openlocfilehash: e8c7d1a4fb3537644ed3b7737158a5e50abcdae5
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73019758"
---
# <a name="android-speech"></a>Android 语音

_本文介绍使用非常强大的 Android. 语音命名空间的基本知识。自其开始，Android 能够识别语音并将其作为文本输出。这是一个相对简单的过程。不过，对于文本到语音转换，此过程更加复杂，因为它不仅需要考虑语音引擎，还必须考虑到从文本到语音（TTS）系统中提供和安装的语言。_

## <a name="speech-overview"></a>语音概述

在移动开发中，系统会 "理解" 人为语音和 enunciates （语音到文本和文本转语音），这是一个不断增长的领域，因为与我们的设备进行自然通信的需求会上升。 在许多情况下，将文本转换为语音的功能（或反之）都是一个非常有用的工具，可将其合并到 android 应用程序中。

例如，在移动电话上，使用此夹具时，用户需要一种手动操作设备的方式。 不同 Android 外形规格的很多（如 Android 磨损）以及能够使用 Android 设备（如平板电脑和便笺垫）的不断扩大的包含在很大的 TTS 应用程序上。

Google 向开发人员提供了一组丰富的 Api，这些 Api 位于 Android. Speech 命名空间中，涵盖使设备 "语音识别" （如为盲设计的软件）的大多数实例。  命名空间包含的工具允许通过 `Android.Speech.Tts`将文本转换为语音，并控制用于执行转换的引擎，以及允许将语音转换为文本的多个 `RecognizerIntent`。

虽然设备可供理解，但根据所使用的硬件存在一些限制。 设备在每种语言中都能成功解释每个语言的内容。

## <a name="requirements"></a>要求

除了具有麦克风和扬声器的设备之外，本指南没有特殊要求。

用于解释语音的 Android 设备的核心是使用具有相应 `OnActivityResult`的 `Intent`。
但这一点很重要，那就是认识到语音不理解，但解释为文本。 差别很重要。

### <a name="the-difference-between-understanding-and-interpreting"></a>理解与解释之间的区别

了解的一个简单定义是，您可以通过色调和上下文来确定所说的实际含义。 仅解释采用字词并将其输出到另一个窗体的方法。

请考虑以下简单示例，在日常对话中使用：

<kbd>喂，你好吗？</kbd>

如果没有转折点（强调特定词或词的一部分），就是一个简单的问题。 但是，如果应用到线路的速度较慢，则倾听人员将检测到该提问者不太高兴，并且可能需要喝彩或提问者 unwell。 如果强调的位置为 "是"，则请求的人员通常会对响应感兴趣。

如果没有相当强大的音频处理功能来利用转折点，并有一定程度的人工智能（AI）来理解上下文，则软件甚至无法开始了解所说的内容，这就是一种简单的手机，就是将语音转换为文本。

## <a name="setting-up"></a>设置

在使用语音系统之前，检查以确保设备具有麦克风始终是明智之举。 在未安装麦克风的情况下，将不会再尝试在 Kindle 或 Google note 上运行你的应用程序。

下面的代码示例演示如何查询麦克风是否可用，如果没有，则创建警报。 如果此时没有可用的麦克风，可以退出活动或禁用录制语音功能。

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

语音系统的意图使用称为 `RecognizerIntent`的特定意向。 此方法控制大量参数，包括在记录被视为之前要等待的持续时间、要识别和输出的任何其他语言以及要在 `Intent`模式对话框中包含的任何文本作为说明。 在此代码段中，`VOICE` 是用于在 `OnActivityResult`中进行识别的 `readonly int`。

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

### <a name="conversion-of-the-speech"></a>语音转换

将在 `Intent`中传递从语音解释的文本，该文本在活动完成并且通过 `GetStringArrayListExtra(RecognizerIntent.ExtraResults)`访问时返回。 这会返回一个 `IList<string>`，该索引可用于使用和显示索引，具体取决于调用方意向中所请求的语言数（在 `RecognizerIntent.ExtraMaxResults`中指定）。 不过，对于任何列表，都需要进行检查以确保存在要显示的数据。

侦听 `StartActivityForResult`的返回值时，必须提供 `OnActivityResult` 方法。

在下面的示例中，`textBox` 是用于输出所听写内容的 `TextBox`。 它可以同样用于将文本传递到某种形式的解释器，并通过它将文本和分支与应用程序的其他部分进行比较。

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

## <a name="text-to-speech"></a>文本转语音

文本到语音并不像语音转换到文本，而是依赖于两个关键组件;要在设备上安装的文本到语音引擎以及要安装的语言。

在很大程度上，Android 设备附带了默认的 Google TTS 服务和至少一种语言。 这是在第一次设置设备时建立的，并基于设备的位置（例如，在德国设置的电话将安装德语版，而美国的电话将使用美国英语）。

### <a name="step-1---instantiating-texttospeech"></a>步骤 1-实例化 TextToSpeech

`TextToSpeech` 最多可能需要3个参数，第三个参数是可选的（`AppContext`、`IOnInitListener``engine`）。 侦听器用于绑定到服务，并测试引擎是否有任何可用的 Android 文本到语音引擎。 设备至少会有 Google 自己的引擎。

### <a name="step-2---finding-the-languages-available"></a>步骤 2-查找可用的语言

`Java.Util.Locale` 类包含一个名为 `GetAvailableLocales()`的有用方法。 然后，可以根据安装的语言对此语音引擎支持的语言列表进行测试。

生成 "理解的" 语言的列表是一件简单的事情。 始终会出现默认语言（用户首次设置其设备时设置的语言），因此在此示例中，`List<string>` 的第一个参数为 "Default"，则根据 `textToSpeech.IsLanguageAvailable(locale)`的结果，将填充列表的其余部分。

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

此代码调用[TextToSpeech](xref:Android.Speech.Tts.TextToSpeech.IsLanguageAvailable*)来测试给定区域设置的语言包是否已存在于设备上。
此方法返回一个[LanguageAvailableResult](xref:Android.Speech.Tts.LanguageAvailableResult)，该方法指示通过的区域设置的语言是否可用。 如果 `LanguageAvailableResult` 指示语言 `NotSupported`，则表明该语言没有可用的语音包（甚至出于下载）。 如果 `LanguageAvailableResult` 设置为 `MissingData`，则可以按照下面的步骤4中的说明下载新语言包。

### <a name="step-3---setting-the-speed-and-pitch"></a>步骤 3-设置速度和间距

Android 允许用户通过更改 `SpeechRate` 和 `Pitch` （语音的速度和音调）来更改语音声音。 这从0到1，对于这两个，"正常" 语音为1。

### <a name="step-4---testing-and-loading-new-languages"></a>步骤 4-测试和加载新语言

下载新语言是通过使用 `Intent`来执行的。 此意图的结果将导致调用[OnActivityResult](xref:Android.App.Activity.OnActivityResult*)方法。 与语音到文本示例（使用[RecognizerIntent](xref:Android.Speech.RecognizerIntent)作为 `Intent`的 `PutExtra` 参数）不同，测试和加载 `Intent`是基于 `Action`的：

- [TextToSpeech](xref:Android.Speech.Tts.TextToSpeech.Engine.ActionCheckTtsData) &ndash; 从平台 `TextToSpeech` 引擎启动一个活动，以验证设备上语言资源的正确安装和可用性。

- [TextToSpeech](xref:Android.Speech.Tts.TextToSpeech.Engine.ActionInstallTtsData) &ndash; 启动一个活动，该活动会提示用户下载所需的语言。

下面的代码示例演示如何使用这些操作来测试语言资源并下载一种新语言：

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

`TextToSpeech.Engine.ActionCheckTtsData` 测试语言资源的可用性。 此测试完成后，将调用 `OnActivityResult`。 如果需要下载语言资源，`OnActivityResult` 会触发 `TextToSpeech.Engine.ActionInstallTtsData` 操作，以启动允许用户下载所需语言的活动。 请注意，此 `OnActivityResult` 实现不检查 `Result` 的代码，因为在这个简化的示例中，已确定需要下载语言包。

`TextToSpeech.Engine.ActionInstallTtsData` 操作将导致向用户显示**GOOGLE TTS 语音数据**活动，以选择要下载的语言：

![Google TTS 语音数据活动](speech-images/01-google-tts-voice-data.png)

例如，用户可能会选择法语并单击 "下载" 图标以下载法语语音数据：

![下载法语的示例](speech-images/02-selecting-french.png)

下载完成后，会自动安装此数据。

### <a name="step-5---the-ioninitlistener"></a>步骤 5-IOnInitListener

要使活动能够将文本转换为语音，必须实现接口方法 `OnInit` （这是为 `TextToSpeech` 类的实例化指定的第二个参数）。 这会初始化侦听器并测试结果。

侦听器应至少测试 `OperationResult.Success` 和 `OperationResult.Failure`。
下面的示例演示了这一点：

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

在本指南中，我们介绍了将文本转换为语音和语音的基本知识，以及如何将它们包含在您自己的应用程序中的可能方法。 尽管它们不涵盖每种特定的情况，但你现在应该基本了解如何解释语音，如何安装新语言以及如何提高应用的 inclusivity。

## <a name="related-links"></a>相关链接

- [Xamarin. Forms DependencyService](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/dependencyservice//)
- [文本转语音（示例）](https://docs.microsoft.com/samples/xamarin/monodroid-samples/platformfeatures-texttospeech)
- [语音到文本（示例）](https://docs.microsoft.com/samples/xamarin/monodroid-samples/platformfeatures-speechtotext)
- [Android. 语音命名空间](xref:Android.Speech)
- [Android。](xref:Android.Speech.Tts)
