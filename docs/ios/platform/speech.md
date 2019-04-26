---
title: Xamarin.iOS 中的语音识别
description: 本文提供了新的语音 API，并演示如何在 Xamarin.iOS 应用程序以支持持续语音识别和转录文本到语音 （从直播或录制的音频流） 中实现它。
ms.prod: xamarin
ms.assetid: 64FED50A-6A28-4833-BEAE-63CEC9A09010
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: c1f488213f9b3be945fd98e09f630c243d0b0d62
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61382791"
---
# <a name="speech-recognition-in-xamarinios"></a>Xamarin.iOS 中的语音识别

_本文提供了新的语音 API，并演示如何在 Xamarin.iOS 应用程序以支持持续语音识别和转录文本到语音 （从直播或录制的音频流） 中实现它。_

新 ios 10、 Apple 发布了让 iOS 应用程序以支持持续语音识别和转录 （从直播或录制的音频流） 的语音到文本的语音识别 API。

根据 Apple，语音识别 API 具有以下功能和优点：

- 高度准确
- 先进的
- 易于使用
- 快速
- 支持多种语言
- 尊重用户隐私

## <a name="how-speech-recognition-works"></a>语音识别的工作原理

获取 （在任何 API 支持的口述语言） 的实时或预先录制音频，并将其传递到语音识别器，它将返回纯文本段录音所讲述词语的情况下，在 iOS 应用程序中实现语音识别。

[![](speech-images/speech01.png "语音识别的工作原理")](speech-images/speech01.png#lightbox)

### <a name="keyboard-dictation"></a>键盘听写

在 iOS 设备上，大多数用户认为的语音识别，他们认为 iPhone 4 秒与 iOS 5 中发布以及键盘听写内置 Siri 语音助手。

键盘听写支持的任何支持 TextKit 的界面元素 (如`UITextField`或`UITextArea`)，并通过单击 （直接向空格键左侧） 中的 iOS 虚拟键盘听写按钮的用户激活。

Apple 已发布 （收集自 2011 年起） 的以下键盘听写统计信息：

- 在 iOS 5 中发布后已广泛使用键盘听写。
- 大约 65,000 应用每日可以使用它。
- 有关的所有 iOS 第三个听写完成在第三方应用程序中。

键盘听写是的使用极其轻松，因为它不需要开发人员的一部分，而不是在应用程序的 UI 设计使用 TextKit 界面元素上的任何工作。 键盘听写还具有不需要从应用任何特殊权限请求，然后可以使用它的优点。

使用新的语音识别 Api 的应用将需要特殊权限授予用户，由于语音识别需要传输和 Apple 的服务器上的数据的临时存储。 请参阅我们[安全性和隐私增强功能](~/ios/app-fundamentals/security-privacy.md)文档了解详细信息。

虽然键盘听写容易实现，它却带来了几个限制和缺点：

- 它需要使用文本输入字段和显示键盘。
- 它适用于实时音频输入仅且应用具有无法控制音频录制过程。
- 它提供了无法控制用于解释用户的语音的语言。
- 没有为应用程序以了解是否甚至可供用户使用听写按钮方法。
- 应用程序不能自定义音频录制过程。
- 它提供了非常肤浅的结果集缺少计时和置信度等信息。

### <a name="speech-recognition-api"></a>语音识别 API

新 ios 10、 Apple 已发布了语音识别 API 提供更强大的 iOS 应用程序实现语音识别。 此 API 是同一个 Apple 使用 Siri 和键盘听写并且能够快速脚本提供先进的准确性。

语音识别 API 提供的结果以透明方式自定义向单个用户，而无需应用程序无需收集或访问任何私有用户数据。

语音识别 API 提供了返回到调用应用中的结果近乎实时地说到用户以及它提供了有关转换的结果比只包含文本的详细信息。 这些问题包括：

- 用户所说的内容的多个解释。
- 各翻译的置信度级别。
- 计时信息。

如上面所述，是通过实时源，或从预记录源和中的任何 50 多个语言和区域语言，支持的 iOS 10 可以提供适用于转换的音频。

语音识别 API 可以在任何运行 iOS 10 的 iOS 设备使用，在大多数情况下，需要实时的 internet 连接，因为 Apple 的服务器上发生的转换的大容量。 不过，某些较新的 iOS 设备始终支持、 设备上的特定语言的翻译。

Apple 已包含一个可用性 API，用于确定给定的语言是否可用于在当前时间点的转换。 应用应使用此 API，而不是直接测试 internet 连接本身。

如在键盘听写部分上文所述，语音识别将需要传输和 Apple 的服务器上的数据的临时存储，在 internet 上，因此，应用程序_必须_请求执行的用户的权限通过包括识别`NSSpeechRecognitionUsageDescription`中的键及其`Info.plist`文件并调用`SFSpeechRecognizer.RequestAuthorization`方法。 

其他基于用于语音识别的音频源，更改到应用程序的`Info.plist`文件可能需要。 请参阅我们[安全性和隐私增强功能](~/ios/app-fundamentals/security-privacy.md)文档了解详细信息。

## <a name="adopting-speech-recognition-in-an-app"></a>采用应用程序中的语音识别

有开发人员采用 iOS 应用程序中的语音识别时必须执行的四个主要步骤：

- 提供在应用的使用情况说明`Info.plist`文件中使用`NSSpeechRecognitionUsageDescription`密钥。 例如，相机应用程序可能包含以下描述中， _"这允许您只需通过说单词奶酪拍摄一张照片。"_
- 通过调用请求授权`SFSpeechRecognizer.RequestAuthorization`方法以呈现说明 (中提供`NSSpeechRecognitionUsageDescription`密钥更高版本) 的应用程序为什么需要语音识别访问一个对话框中的用户，使他们可以接受或拒绝。
- 创建语音识别请求：
    * 在磁盘上的预先录制音频，使用`SFSpeechURLRecognitionRequest`类。
    * 对于实时音频 （或音频从内存），使用`SFSPeechAudioBufferRecognitionRequest`类。
- 将语音识别请求传递到语音识别器 (`SFSpeechRecognizer`) 开始识别。 应用程序可以选择性地保留到返回`SFSpeechRecognitionTask`来监视和跟踪识别结果。

将下面将详细介绍这些步骤。

### <a name="providing-a-usage-description"></a>提供使用说明

若要提供所需`NSSpeechRecognitionUsageDescription`键中`Info.plist`文件中，执行以下操作：

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

1. 双击`Info.plist`文件将其打开进行编辑。
2. 切换到**源**视图： 

    [![](speech-images/speech02.png "源视图")](speech-images/speech02.png#lightbox)
3. 单击**添加新条目**，输入`NSSpeechRecognitionUsageDescription`有关**属性**，`String`有关**类型**和一个**使用说明**作为**值**。 例如： 

    [![](speech-images/speech03.png "添加 NSSpeechRecognitionUsageDescription")](speech-images/speech03.png#lightbox)
4. 如果应用程序将处理实时音频转录，它还需要麦克风使用说明。 单击**添加新条目**，输入`NSMicrophoneUsageDescription`有关**属性**，`String`有关**类型**和一个**使用说明**作为**值**。 例如： 

    [![](speech-images/speech04.png "添加 NSMicrophoneUsageDescription")](speech-images/speech04.png#lightbox)
4. 保存对文件所做的更改。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. 双击`Info.plist`文件将其打开进行编辑。
3. 单击**添加新条目**，输入`NSSpeechRecognitionUsageDescription`有关**属性**，`String`有关**类型**和一个**使用说明**作为**值**。 例如： 

    [![](speech-images/speech03w.png "添加 NSSpeechRecognitionUsageDescription")](speech-images/speech03w.png#lightbox)
4. 如果应用程序将处理实时音频转录，它还需要麦克风使用说明。 单击**添加新条目**，输入`NSMicrophoneUsageDescription`有关**属性**，`String`有关**类型**和一个**使用说明**作为**值**。 例如： 

    [![](speech-images/speech04w.png "添加 NSMicrophoneUsageDescription")](speech-images/speech04w.png#lightbox)
4. 保存对文件所做的更改。

-----

> [!IMPORTANT]
> 无法提供上述任一`Info.plist`键 (`NSSpeechRecognitionUsageDescription`或`NSMicrophoneUsageDescription`) 可能会导致应用程序尝试访问语音识别或实时音频麦克风时失败而不发出警告。




### <a name="requesting-authorization"></a>请求授权

若要请求允许应用程序访问语音识别所需的用户授权，编辑主视图控制器类并添加以下代码：

```csharp
using System;
using UIKit;
using Speech;

namespace MonkeyTalk
{
    public partial class ViewController : UIViewController
    {
        protected ViewController (IntPtr handle) : base (handle)
        {
            // Note: this .ctor should not contain any initialization logic.
        }

        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Request user authorization
            SFSpeechRecognizer.RequestAuthorization ((SFSpeechRecognizerAuthorizationStatus status) => {
                // Take action based on status
                switch (status) {
                case SFSpeechRecognizerAuthorizationStatus.Authorized:
                    // User has approved speech recognition
                    ...
                    break;
                case SFSpeechRecognizerAuthorizationStatus.Denied:
                    // User has declined speech recognition
                    ...
                    break;
                case SFSpeechRecognizerAuthorizationStatus.NotDetermined:
                    // Waiting on approval
                    ...
                    break;
                case SFSpeechRecognizerAuthorizationStatus.Restricted:
                    // The device is not permitted
                    ...
                    break;
                }
            });
        }
    }
}
```

`RequestAuthorization`方法`SFSpeechRecognizer`类将权限从用户请求访问语音识别使用中的开发人员提供的原因`NSSpeechRecognitionUsageDescription`密钥的`Info.plist`文件。

一个`SFSpeechRecognizerAuthorizationStatus`将结果返回给`RequestAuthorization`可用于执行操作的方法的回调例程根据用户的权限。 

> [!IMPORTANT]
> Apple 建议等待，直到用户已请求此权限之前需要语音识别的应用中开始操作。

### <a name="recognizing-pre-recorded-speech"></a>预先录制的语音识别

如果应用程序需要预先录制的 WAV 或 MP3 文件中的语音识别，它可以使用以下代码：

```csharp
using System;
using UIKit;
using Speech;
using Foundation;
...

public void RecognizeFile (NSUrl url)
{
    // Access new recognizer
    var recognizer = new SFSpeechRecognizer ();

    // Is the default language supported?
    if (recognizer == null) {
        // No, return to caller
        return;
    }

    // Is recognition available?
    if (!recognizer.Available) {
        // No, return to caller
        return;
    }

    // Create recognition task and start recognition
    var request = new SFSpeechUrlRecognitionRequest (url);
    recognizer.GetRecognitionTask (request, (SFSpeechRecognitionResult result, NSError err) => {
        // Was there an error?
        if (err != null) {
            // Handle error
            ...
        } else {
            // Is this the final translation?
            if (result.Final) {
                Console.WriteLine ("You said, \"{0}\".", result.BestTranscription.FormattedString);
            }
        }
    });
}
```

查看详细信息中的此代码时，首先，它会尝试创建语音识别器 (`SFSpeechRecognizer`)。 如果默认语言不受支持语音识别的`null`返回，函数退出。

如果语音识别器的默认语言不可用，应用程序进行检查以查看其是否当前可用于识别使用`Available`属性。 例如，识别可能不可用，如果设备没有有效的 internet 连接。

一个`SFSpeechUrlRecognitionRequest`创建从`NSUrl`预先录制在 iOS 设备，且该文件的位置传递到语音识别器处理的回调例程。

调用回调时，如果`NSError`不是`null`已必须处理的错误。 由于语音识别时以增量方式执行，因此可能会超过一次以便调用回调例程`SFSpeechRecognitionResult.Final`测试看是否翻译完成，转换的最佳版本写出属性 (`BestTranscription`)。

### <a name="recognizing-live-speech"></a>实时语音识别

如果应用程序想要识别实时语音，过程是非常类似于预先录制的语音识别。 例如：

```csharp
using System;
using UIKit;
using Speech;
using Foundation;
using AVFoundation;
...

#region Private Variables
private AVAudioEngine AudioEngine = new AVAudioEngine ();
private SFSpeechRecognizer SpeechRecognizer = new SFSpeechRecognizer ();
private SFSpeechAudioBufferRecognitionRequest LiveSpeechRequest = new SFSpeechAudioBufferRecognitionRequest ();
private SFSpeechRecognitionTask RecognitionTask;
#endregion
...

public void StartRecording ()
{
    // Setup audio session
    var node = AudioEngine.InputNode;
    var recordingFormat = node.GetBusOutputFormat (0);
    node.InstallTapOnBus (0, 1024, recordingFormat, (AVAudioPcmBuffer buffer, AVAudioTime when) => {
        // Append buffer to recognition request
        LiveSpeechRequest.Append (buffer);
    });

    // Start recording
    AudioEngine.Prepare ();
    NSError error;
    AudioEngine.StartAndReturnError (out error);

    // Did recording start?
    if (error != null) {
        // Handle error and return
        ...
        return;
    }

    // Start recognition
    RecognitionTask = SpeechRecognizer.GetRecognitionTask (LiveSpeechRequest, (SFSpeechRecognitionResult result, NSError err) => {
        // Was there an error?
        if (err != null) {
            // Handle error
            ...
        } else {
            // Is this the final translation?
            if (result.Final) {
                Console.WriteLine ("You said \"{0}\".", result.BestTranscription.FormattedString);
            }
        }
    });
}

public void StopRecording ()
{
    AudioEngine.Stop ();
    LiveSpeechRequest.EndAudio ();
}

public void CancelRecording ()
{
    AudioEngine.Stop ();
    RecognitionTask.Cancel ();
}
```

查看详细信息中的此代码时，它会创建多个私有变量，用于处理识别过程：

```csharp
private AVAudioEngine AudioEngine = new AVAudioEngine ();
private SFSpeechRecognizer SpeechRecognizer = new SFSpeechRecognizer ();
private SFSpeechAudioBufferRecognitionRequest LiveSpeechRequest = new SFSpeechAudioBufferRecognitionRequest ();
private SFSpeechRecognitionTask RecognitionTask;
```

它将被传递到的录制音频使用 AV Foundation`SFSpeechAudioBufferRecognitionRequest`来处理识别请求：

```csharp
var node = AudioEngine.InputNode;
var recordingFormat = node.GetBusOutputFormat (0);
node.InstallTapOnBus (0, 1024, recordingFormat, (AVAudioPcmBuffer buffer, AVAudioTime when) => {
    // Append buffer to recognition request
    LiveSpeechRequest.Append (buffer);
});
```

是应用程序尝试开始记录和记录不能启动时处理任何错误：

```csharp
AudioEngine.Prepare ();
NSError error;
AudioEngine.StartAndReturnError (out error);

// Did recording start?
if (error != null) {
    // Handle error and return
    ...
    return;
}
```

识别任务已启动并且句柄保留到识别任务 (`SFSpeechRecognitionTask`):

```csharp
RecognitionTask = SpeechRecognizer.GetRecognitionTask (LiveSpeechRequest, (SFSpeechRecognitionResult result, NSError err) => {
    ...
});
```

以类似的方式与上面使用预先录制语音上使用回调。

如果记录为已由用户，系统将通知音频引擎和语音识别请求：

```csharp
AudioEngine.Stop ();
LiveSpeechRequest.EndAudio ();
```

如果用户取消识别，音频引擎和识别任务将通知：

```csharp
AudioEngine.Stop ();
RecognitionTask.Cancel ();
```

务必要调用`RecognitionTask.Cancel`如果用户取消转换以释放内存和设备的处理器。

> [!IMPORTANT]
> 无法提供`NSSpeechRecognitionUsageDescription`或`NSMicrophoneUsageDescription``Info.plist`密钥可能会导致应用程序尝试访问语音识别或实时音频麦克风时失败而不发出警告 (`var node = AudioEngine.InputNode;`)。 请参阅**提供的使用情况描述**上面部分的详细信息。

## <a name="speech-recognition-limits"></a>语音识别限制

在 iOS 应用中使用语音识别时，Apple 施加以下限制：

- 语音识别可供所有应用程序，免费，但其使用情况不是不受限制：
    - IOS 设备具有有限的数量的可执行每日的识别。
    - 应用将请求每日基础上的全局限制。
- 应用必须准备好处理语音识别网络连接和使用情况速率限制失败。
- 语音识别可以为此用户的 iOS 设备上有电池耗尽和高网络流量较高的成本，Apple 施加的严格音频持续时间限制为大约一分钟的最大的语音。

如果应用经常超过速率限制的限制，Apple 要求开发人员，与他们联系。

## <a name="privacy-and-usability-considerations"></a>隐私和可用性注意事项

Apple 具有对于透明并在 iOS 应用程序中包括语音识别时遵循用户的隐私的以下建议：

- 记录用户的语音时, 请务必明确指示，录制发生在应用程序的用户界面中。 例如，应用可能发出声音"记录"，并显示录制的指示器。
- 不使用语音识别，如密码、 运行状况数据或财务信息的敏感用户信息。
- 显示识别结果_之前_对它们进行操作。 这不只提供应用程序执行操作，但允许用户处理所识别错误的反馈。

## <a name="summary"></a>总结

这篇文章已提供新的语音 API，并介绍了如何在 Xamarin.iOS 应用程序以支持持续语音识别和转录文本到语音 （从直播或录制的音频流） 中实现它。 



## <a name="related-links"></a>相关链接

- [SpeakToMe （示例）](https://developer.xamarin.com/samples/monotouch/ios10/SpeakToMe/)
