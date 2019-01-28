---
title: 实现文本到语音转换
description: 本文说明如何使用 Xamarin.Forms DependencyService 类调用每个平台的本机文本转语音 API。
ms.prod: xamarin
ms.assetid: 1D6164F9-4ECE-43A6-B583-1F5D5EFC1DDF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/18/2017
ms.openlocfilehash: af40dc99dffd6e04ad0d408aea58b23c5446461e
ms.sourcegitcommit: 2ee36611ef667affee7d417db947fbb614d75315
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2019
ms.locfileid: "54479753"
---
# <a name="implementing-text-to-speech"></a>实现文本到语音转换

[![下载示例](~/media/shared/download.png) 下载示例](https://developer.xamarin.com/samples/xamarin-forms/UsingDependencyService/)

本文将指导你创建一个使用 [`DependencyService`](xref:Xamarin.Forms.DependencyService) 访问本机文本转语音 API 的跨平台应用：

- **[创建接口](#Creating_the_Interface)** &ndash; 了解如何在共享代码中创建接口。
- **[iOS 实现](#iOS_Implementation)** &ndash; 了解如何在 iOS 本机代码中实现接口。
- **[Android 实现](#Android_Implementation)** &ndash; 了解如何在 Android 本机代码中实现接口。
- **[UWP 实现](#WindowsImplementation)** &ndash; 了解如何在通用 Windows 平台 (UWP) 的本机代码中实现接口。
- **[在共享代码中实现](#Implementing_in_Shared_Code)** &ndash; 了解如何使用 `DependencyService` 从共享代码调用本机实现。

使用 `DependencyService` 的应用程序将具有以下结构：

![](text-to-speech-images/tts-diagram.png "DependencyService 应用程序结构")

<a name="Creating_the_Interface" />

## <a name="creating-the-interface"></a>创建界面

首先，在表示打算实现的功能的共享代码中创建一个接口。 就此示例而言，该接口包含一个方法，即 `Speak`：

```csharp
public interface ITextToSpeech
{
    void Speak (string text);
}
```

在共享代码中对这个接口进行编码将使 Xamarin.Forms 应用能够访问各平台上的语音 API。

> [!NOTE]
> 实现接口的类必须具有无参数的构造函数才能使用 `DependencyService`。

<a name="iOS_Implementation" />

## <a name="ios-implementation"></a>iOS 实现

必须在每个特定于平台的应用程序项目中实现接口。 请注意，类有一个无参数的构造函数，从而使 `DependencyService` 可以创建新实例。

```csharp
[assembly: Dependency(typeof(TextToSpeechImplementation))]
namespace DependencyServiceSample.iOS
{

    public class TextToSpeechImplementation : ITextToSpeech
    {
        public TextToSpeechImplementation() { }

        public void Speak(string text)
        {
            var speechSynthesizer = new AVSpeechSynthesizer();
            var speechUtterance = new AVSpeechUtterance(text)
            {
                Rate = AVSpeechUtterance.MaximumSpeechRate / 4,
                Voice = AVSpeechSynthesisVoice.FromLanguage("en-US"),
                Volume = 0.5f,
                PitchMultiplier = 1.0f
            };

            speechSynthesizer.SpeakUtterance(speechUtterance);
        }
    }
}
```

`[assembly]` 属性将该类注册为 `ITextToSpeech` 接口的实现，这意味着可以在共享代码中使用 `DependencyService.Get<ITextToSpeech>()` 来创建它的实例。

<a name="Android_Implementation" />

## <a name="android-implementation"></a>Android 实现

Android 代码比 iOS 版本更复杂。 它需要访问由 `MainActivity.Instance` 属性公开的当前 Android 上下文：

```csharp
public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsAppCompatActivity
{
    internal static MainActivity Instance { get; private set; }

    protected override void OnCreate(Bundle bundle)
    {
        ...
    }
}
```

另外，它还需要实现继承自 Android 专用 `Java.Lang.Object` 并实现 `IOnInitListener` 接口的类。

```csharp
[assembly: Dependency(typeof(TextToSpeechImplementation))]
namespace DependencyServiceSample.Droid
{
    public class TextToSpeechImplementation : Java.Lang.Object, ITextToSpeech, TextToSpeech.IOnInitListener
    {
        TextToSpeech speaker;
        string toSpeak;

        public void Speak(string text)
        {
            toSpeak = text;
            if (speaker == null)
            {
                speaker = new TextToSpeech(MainActivity.Instance, this);
            }
            else
            {
                speaker.Speak(toSpeak, QueueMode.Flush, null, null);
            }
        }

        public void OnInit(OperationResult status)
        {
            if (status.Equals(OperationResult.Success))
            {
                speaker.Speak(toSpeak, QueueMode.Flush, null, null);
            }
        }
    }
}
```

`[assembly]` 属性将该类注册为 `ITextToSpeech` 接口的实现，这意味着可以在共享代码中使用 `DependencyService.Get<ITextToSpeech>()` 来创建它的实例。

<a name="WindowsImplementation" />

## <a name="universal-windows-platform-implementation"></a>通用 Windows 平台实现

通用 Windows 平台在 `Windows.Media.SpeechSynthesis` 命名空间中有一个语音 API。 唯一需要注意的是，请记住在清单中勾选“麦克风”功能，否则对语音 API 的访问会受到阻止。

```csharp
[assembly:Dependency(typeof(TextToSpeechImplementation))]
public class TextToSpeechImplementation : ITextToSpeech
{
    public async void Speak(string text)
    {
        var mediaElement = new MediaElement();
        var synth = new Windows.Media.SpeechSynthesis.SpeechSynthesizer();
        var stream = await synth.SynthesizeTextToStreamAsync(text);

        mediaElement.SetSource(stream, stream.ContentType);
        mediaElement.Play();
    }
}
```

`[assembly]` 属性将该类注册为 `ITextToSpeech` 接口的实现，这意味着可以在共享代码中使用 `DependencyService.Get<ITextToSpeech>()` 来创建它的实例。

<a name="Implementing_in_Shared_Code" />

## <a name="implementing-in-shared-code"></a>在共享代码中实现

现在我们可以编写和测试访问文本转语音接口的共享代码。 此简单页面包含一个触发语音功能的按钮。 它使用 `DependencyService` 获取 `ITextToSpeech` 接口的实例 &ndash; 在运行时，这个实例是特定于平台的实现，拥有对本机 SDK 的完全访问权限。

```csharp
public MainPage ()
{
    var speak = new Button {
        Text = "Hello, Forms !",
        VerticalOptions = LayoutOptions.CenterAndExpand,
        HorizontalOptions = LayoutOptions.CenterAndExpand,
    };
    speak.Clicked += (sender, e) => {
        DependencyService.Get<ITextToSpeech>().Speak("Hello from Xamarin Forms");
    };
    Content = speak;
}
```

在 iOS、Android 或 UWP 上运行此应用程序并按下该按钮，应用程序就会使用各平台上的本机语音 SDK 与你对话。

 ![iOS 和 Android 文本转语音按钮](text-to-speech-images/running.png "文本转语音示例")


## <a name="related-links"></a>相关链接

- [使用 DependencyService（示例）](https://developer.xamarin.com/samples/xamarin-forms/UsingDependencyService/)
- [DependencyServiceSample](https://developer.xamarin.com/samples/xamarin-forms/DependencyService/DependencyServiceSample/)
