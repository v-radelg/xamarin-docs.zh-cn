---
title: Android Audio
description: Android OS 提供了广泛支持为多媒体，其中包含音频和视频。 本指南重点介绍在 Android 中的音频，涵盖播放和录制音频，使用内置的音频播放器和记录器类，以及低级别的音频 API。 它还介绍如何使用由其他应用程序，广播音频事件，以便开发人员可以构建良好的应用程序。
ms.prod: xamarin
ms.assetid: 646ED563-C34E-256D-4B56-29EE99881C27
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/28/2018
ms.openlocfilehash: a1a9dd06fb3cd6899dd3a564072bb63e413edf22
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61022609"
---
# <a name="android-audio"></a>Android Audio

_Android OS 提供了广泛支持为多媒体，其中包含音频和视频。本指南重点介绍在 Android 中的音频，涵盖播放和录制音频，使用内置的音频播放器和记录器类，以及低级别的音频 API。它还介绍如何使用由其他应用程序，广播音频事件，以便开发人员可以构建良好的应用程序。_


## <a name="overview"></a>概述

新式移动设备已采用了功能，以前需要的设备的专用的部件&ndash;照相机、 音乐播放机和视频刻录机。 正因为如此，多媒体框架已成为移动 Api 中的第一类功能。

Android 提供了为多媒体的广泛支持。 这篇文章将检查使用音频在 Android 中，并涵盖以下主题

1.  **播放音频使用 MediaPlayer** &ndash;使用内置`MediaPlayer`类来播放音频，包括本地音频文件和使用的流式处理音频文件`AudioTrack`类。

2.  **录制音频**&ndash;使用内置`MediaRecorder`录制音频的类。

3.  **使用音频通知**&ndash;使用音频通知通过挂起或取消其音频输出创建良好的应用程序的正确响应事件 （如电话呼叫）。

4.  **使用低级别音频**&ndash;播放音频使用`AudioTrack`通过直接写入内存缓冲区的类。 录制音频使用`AudioRecord`类，并直接从内存缓冲区中读取。


## <a name="requirements"></a>要求

本指南需要 Android 2.0 （API 级别 5） 或更高版本。 请注意，必须在设备上完成调试 Android 上的音频。

必须请求`RECORD_AUDIO`中的权限**AndroidManifest.XML**:

![所需的记录与 Android 清单权限部分\_启用音频](android-audio-images/image01.png)



## <a name="playing-audio-with-the-mediaplayer-class"></a>播放音频与 MediaPlayer 类

播放音频在 Android 中的最简单方法是使用内置[MediaPlayer](https://developer.xamarin.com/api/type/Android.Media.MediaPlayer/)类。
`MediaPlayer` 可以通过传入的文件路径播放本地或远程文件。 但是，`MediaPlayer`是非常状态区分，并调用其方法之一在错误的状态将导致引发异常。 务必要与之交互`MediaPlayer`以避免错误如下所述的顺序。



### <a name="initializing-and-playing"></a>初始化和播放

播放音频与`MediaPlayer`需要按以下顺序：

1. 实例化一个新[MediaPlayer](https://developer.xamarin.com/api/type/Android.Media.MediaPlayer/)对象。

1. 配置文件以通过播放[SetDataSource](https://developer.xamarin.com/api/member/Android.Media.MediaPlayer.SetDataSource/p/Java.IO.FileDescriptor/)方法。

1. 调用[准备](https://developer.xamarin.com/api/member/Android.Media.MediaPlayer.Prepare/)方法以初始化播放器。

1. 调用[启动](https://developer.xamarin.com/api/member/Android.Media.MediaPlayer.Start/)方法开始音频播放。


下面的代码示例说明了这种用法：

```csharp
protected MediaPlayer player;
public void StartPlayer(String  filePath)
{
  if (player == null) {
    player = new MediaPlayer();
  } else {
    player.Reset();
    player.SetDataSource(filePath);
    player.Prepare();
    player.Start();
  }
}
```


### <a name="suspending-and-resuming-playback"></a>挂起和继续执行播放

可以通过调用挂起播放[暂停](https://developer.xamarin.com/api/member/Android.Media.MediaPlayer.Pause%28%29/)方法：

```csharp
player.Pause();
```

若要恢复已暂停的播放，请调用[启动](https://developer.xamarin.com/api/member/Android.Media.MediaPlayer.Start%28%29/)方法。
这将恢复从中播放暂停的位置：

```csharp
player.Start();
```

调用[停止](https://developer.xamarin.com/api/member/Android.Media.MediaPlayer.Stop%28%29/)播放机上的方法可结束正在进行播放：

```csharp
player.Stop();
```

当不再需要时播放机时，必须通过调用释放的资源[版本](https://developer.xamarin.com/api/member/Android.Media.MediaPlayer.Release%28%29/)方法：

```csharp
player.Release();
```



## <a name="using-the-mediarecorder-class-to-record-audio"></a>使用录制音频 MediaRecorder 类

到推论`MediaPlayer`为在 Android 中的录制音频[MediaRecorder](https://developer.xamarin.com/api/type/Android.Media.MediaRecorder/)类。 如`MediaPlayer`，其状态区分，并将经历若干种状态，若要访问它可以在那里开始录制的点。 若要录制音频，`RECORD_AUDIO`权限必须设置。 有关说明如何将应用程序设置权限请参阅[使用 AndroidManifest.xml](~/android/platform/android-manifest.md)。


### <a name="initializing-and-recording"></a>初始化和记录

录制音频和`MediaRecorder`需要执行以下步骤：

1. 实例化一个新[MediaRecorder](https://developer.xamarin.com/api/type/Android.Media.MediaRecorder/)对象。

2. 指定要用于捕获通过的音频输入哪些硬件设备[SetAudioSource](https://developer.xamarin.com/api/member/Android.Media.MediaRecorder.SetAudioSource/p/Android.Media.AudioSource/)方法。

3. 设置输出文件的音频格式使用[SetOutputFormat](https://developer.xamarin.com/api/member/Android.Media.MediaRecorder.SetOutputFormat/p/Android.Media.OutputFormat/)方法。 有关受支持的音频类型的列表，请参阅[Android 支持媒体格式](https://developer.android.com/guide/appendix/media-formats.html)。

4. 调用[SetAudioEncoder](https://developer.xamarin.com/api/member/Android.Media.MediaRecorder.SetAudioEncoder/p/Android.Media.AudioEncoder/)方法以设置编码类型的音频。

5. 调用[SetOutputFile](https://developer.xamarin.com/api/member/Android.Media.MediaRecorder.SetOutputFile/p/System.String/)方法，以指定将音频数据写入到输出文件的名称。

6. 调用[准备](https://developer.xamarin.com/api/member/Android.Media.MediaRecorder.Prepare%28%29/)方法来初始化记录器。

7. 调用[启动](https://developer.xamarin.com/api/member/Android.Media.MediaRecorder.Start%28%29/)方法开始录制。


下面的代码示例演示了此序列：

```csharp
protected MediaRecorder recorder;
void RecordAudio (String filePath)
{
  try {
    if (File.Exists (filePath)) {
      File.Delete (filePath);
    }
    if (recorder == null) {
      recorder = new MediaRecorder (); // Initial state.
    } else {
      recorder.Reset ();
      recorder.SetAudioSource (AudioSource.Mic);
      recorder.SetOutputFormat (OutputFormat.ThreeGpp);
      recorder.SetAudioEncoder (AudioEncoder.AmrNb);
      // Initialized state.
      recorder.SetOutputFile (filePath);
      // DataSourceConfigured state.
      recorder.Prepare (); // Prepared state
      recorder.Start (); // Recording state.
    }
  } catch (Exception ex) {
    Console.Out.WriteLine( ex.StackTrace);
  }
}
```


### <a name="stopping-recording"></a>正在停止录制

若要停止录制，请调用`Stop`方法`MediaRecorder`:

```csharp
recorder.Stop();
```



### <a name="cleaning-up"></a>清理

一次`MediaRecorder`已停止，调用[重置](https://developer.xamarin.com/api/member/Android.Media.MediaRecorder.Reset%28%29/)方法以将其放回其空闲状态：

```csharp
recorder.Reset();
```

当`MediaRecorder`是不再需要其资源必须释放通过调用[发行](https://developer.xamarin.com/api/member/Android.Media.MediaRecorder.Release%28%29/)方法：

```csharp
recorder.Release();
```


## <a name="managing-audio-notifications"></a>管理音频通知



### <a name="the-audiomanager-class"></a>AudioManager 类

[AudioManager](https://developer.xamarin.com/api/type/Android.Media.AudioManager/)类提供对音频通知，使应用程序知道音频事件发生时访问。 此服务还提供其他音频功能，如卷和响铃模式控制访问。 `AudioManager`允许处理控制音频播放的音频通知应用程序。



### <a name="managing-audio-focus"></a>管理音频焦点

设备 （内置播放器和录制器） 的音频资源可由所有正在运行的应用程序共享。

从概念上讲，它类似于其中只有一个应用程序具有键盘焦点的台式计算机上的应用程序： 选择后一个正在运行的应用程序通过鼠标单击，键盘输入将仅对该应用程序。

音频的焦点是类似的原理，可防止从播放或在同一时间录制音频的多个应用程序。 它是比键盘焦点要复杂一些，因为它是自愿&ndash;应用程序可以忽略这一事实，它不会当前不具有音频焦点且无论播放&ndash;，因为不同类型的可能的音频焦点请求。 例如，如果请求程序应只播放音频很短时间，它可能会请求暂时性焦点。

音频的焦点可能会被立即授予或最初被拒绝并授予更高版本。 例如，如果应用程序请求音频焦点在电话呼叫期间，它将被拒绝，但电话呼叫完成后，也可能会授予焦点。 在这种情况下，侦听器会注册以相应地响应，如果音频焦点移走。 请求音频焦点用于确定它正常播放或录制音频。

有关音频焦点的详细信息，请参阅[管理音频焦点](https://developer.android.com/training/managing-audio/audio-focus.html)。



#### <a name="registering-the-callback-for-audio-focus"></a>注册回调的音频焦点

注册`FocusChangeListener`从回调`IOnAudioChangeListener`是获取并释放音频焦点的一个重要部分。 这是焦点的因为授予的音频可能会延迟到更高版本的时间。 例如，应用程序可能会请求在进行电话呼叫时播放音乐。 电话呼叫完成之前，将不授予音频焦点。

出于此原因，回调对象作为参数传递`GetAudioFocus`方法的`AudioManager`，并且此注册回调的调用。 应用程序如果音频的焦点是最初被拒绝，但更高版本授予，将得到通知，通过调用`OnAudioFocusChange`在回调上。 同一方法用于告知应用程序，消失点音频焦点。

当应用程序完成后使用的音频资源时，它将调用`AbandonFocus`方法的`AudioManager`，并再次将传递在回调中。 此注销回调并释放的音频资源，以便其他应用程序可能会获取音频的焦点。



#### <a name="requesting-audio-focus"></a>请求的音频焦点

若要请求设备的音频资源所需的步骤如下所示：

1.  获取的句柄`AudioManager`系统服务。

2.  创建回调类的实例。

3.  通过调用请求设备的音频资源`RequestAudioFocus`方法`AudioManager`。 参数是回调对象、 流类型 （音乐、 语音呼叫、 环等） 和权限请求的访问权限的类型 （音频资源可以请求暂时不可用或无限期，例如）。

4.  如果授予的请求，`playMusic`立即调用方法并开始播放音频。

5.  如果请求遭拒绝，不执行任何进一步的操作。 在这种情况下，如果请求批准在更高版本时，仅将播放音频。


下面的代码示例说明了这些步骤：

```csharp
Boolean RequestAudioResources(INotificationReceiver parent)
{
  AudioManager audioMan = (AudioManager) GetSystemService(Context.AudioService);
  AudioManager.IOnAudioFocusChangeListener listener  = new MyAudioListener(this);
  var ret = audioMan.RequestAudioFocus (listener, Stream.Music, AudioFocus.Gain );
  if (ret == AudioFocusRequest.Granted) {
    playMusic();
    return (true);
  } else if (ret == AudioFocusRequest.Failed) {
    return (false);
  }
  return (false);
}
```


#### <a name="releasing-audio-focus"></a>释放音频焦点

轨道的播放完毕后`AbandonFocus`方法`AudioManager`调用。 这样，另一个应用程序以获取设备的音频资源。 如果他们已注册其自己的侦听器，其他应用程序将收到此音频的焦点更改的通知。


## <a name="low-level-audio-api"></a>低级别的音频 API

低级别的音频 Api 提供更好地控制音频播放和录制，因为它们直接交互而不是使用文件 Uri 的内存缓冲区。 有一些情况下，这种方法更可取。 这种情况下包括：

1.  当从播放加密的音频文件。

2.  当播放一系列简短的剪辑。

3.  音频流。


### <a name="audiotrack-class"></a>AudioTrack 类

[AudioTrack](https://developer.xamarin.com/api/type/Android.Media.AudioTrack/)类使用录制音频的低级别 Api，而且相当低级别的`MediaPlayer`类。


#### <a name="initializing-and-playing"></a>初始化和播放

若要播放音频的新实例`AudioTrack`必须实例化。 参数列表传递给[构造函数](https://developer.xamarin.com/api/type/Android.Media.AudioTrack/#memberlist)指定如何播放音频缓冲区中包含的示例。 参数是：

1.  Stream 类型&ndash;语音、 铃声、 音乐、 系统或警报。

2.  频率&ndash;以 Hz 的采样率。

3.  通道配置&ndash;Mono 或立体声。

4.  音频格式&ndash;8 位或 16 位编码。

5.  缓冲区大小&ndash;以字节为单位。

6.  缓冲区模式&ndash;流式处理或静态。


构造之后,[播放](https://developer.xamarin.com/api/member/Android.Media.AudioTrack.Play%28%29/)方法的`AudioTrack`调用，以将其设置为开始播放。 写入到的音频缓冲区`AudioTrack`开始播放：

```csharp
void PlayAudioTrack(byte[] audioBuffer)
{
  AudioTrack audioTrack = new AudioTrack(
    // Stream type
    Stream.Music,
    // Frequency
    11025,
    // Mono or stereo
    ChannelOut.Mono,
    // Audio encoding
    Android.Media.Encoding.Pcm16bit,
    // Length of the audio clip.
    audioBuffer.Length,
    // Mode. Stream or static.
    AudioTrackMode.Stream);

    audioTrack.Play();
    audioTrack.Write(audioBuffer, 0, audioBuffer.Length);
}
```


#### <a name="pausing-and-stopping-the-playback"></a>暂停和停止播放

调用[暂停](https://developer.xamarin.com/api/member/Android.Media.AudioTrack.Pause%28%29/)方法来暂停播放：

```csharp
audioTrack.Pause();
```

调用[停止](https://developer.xamarin.com/api/member/Android.Media.AudioTrack.Stop%28%29/)方法将会永久终止播放：

```csharp
audioTrack.Stop();
```


#### <a name="cleanup"></a>清理

当`AudioTrack`是不再需要其资源必须释放通过调用[发行](https://developer.xamarin.com/api/member/Android.Media.AudioTrack.Release%28%29/):

```csharp
audioTrack.Release();
```


### <a name="the-audiorecord-class"></a>AudioRecord 类

[AudioRecord](https://developer.xamarin.com/api/type/Android.Media.AudioRecord/)类相当于`AudioTrack`录制端。 如`AudioTrack`，它使用内存缓冲区直接，来代替文件和 Uri。 该配置要求`RECORD_AUDIO`权限设置在清单中。


#### <a name="initializing-and-recording"></a>初始化和记录

第一步是构造一个新[AudioRecord](https://developer.xamarin.com/api/type/Android.Media.AudioRecord/)对象。 参数列表传递给[构造函数](https://developer.xamarin.com/api/type/Android.Media.AudioRecord/#memberlist)提供用于记录所需的所有信息。 不同于在`AudioTrack`，其中的参数是很大程度上枚举中的等效参数`AudioRecord`是整数。 这些问题包括：

1.  硬件音频输入的源如麦克风。

2.  Stream 类型&ndash;语音、 铃声、 音乐、 系统或警报。

3.  频率&ndash;以 Hz 的采样率。

4.  通道配置&ndash;Mono 或立体声。

5.  音频格式&ndash;8 位或 16 位编码。

6.  缓冲区大小中字节


一次`AudioRecord`构造时，其[StartRecording](https://developer.xamarin.com/api/member/Android.Media.AudioRecord.StartRecording%28%29/)调用方法。 现在已准备好开始录制。 `AudioRecord`持续读取的音频缓冲区的输入，并编写出此输入到音频文件。

```csharp
void RecordAudio()
{
  byte[] audioBuffer = new byte[100000];
  var audRecorder = new AudioRecord(
    // Hardware source of recording.
    AudioSource.Mic,
    // Frequency
    11025,
    // Mono or stereo
    ChannelIn.Mono,
    // Audio encoding
    Android.Media.Encoding.Pcm16bit,
    // Length of the audio clip.
    audioBuffer.Length
  );
  audRecorder.StartRecording();
  while (true) {
    try
    {
      // Keep reading the buffer while there is audio input.
      audRecorder.Read(audioBuffer, 0, audioBuffer.Length);
      // Write out the audio file.
    } catch (Exception ex) {
      Console.Out.WriteLine(ex.Message);
      break;
    }
  }
}
```


#### <a name="stopping-the-recording"></a>停止录制

调用[停止](https://developer.xamarin.com/api/member/Android.Media.AudioRecord.Stop%28%29/)方法终止该录制：

```csharp
audRecorder.Stop();
```


#### <a name="cleanup"></a>清理

当`AudioRecord`不再需要对象，调用其[发行](https://developer.xamarin.com/api/member/Android.Media.AudioRecord.Release%28%29/)方法释放与之关联的所有资源：

```csharp
audRecorder.Release();
```


## <a name="summary"></a>总结

Android OS 提供了一个功能强大的框架，用于播放、 录制和管理音频。 本文介绍了如何播放和录制音频，使用的高层次`MediaPlayer`和`MediaRecorder`类。 接下来，它探讨了如何使用音频通知来共享音频设备之间不同的应用程序的资源。 最后，它处理如何播放和录制音频，使用低级别 Api，该接口直接与内存缓冲区。


## <a name="related-links"></a>相关链接

- [使用与音频 （示例）](https://developer.xamarin.com/samples/Example_WorkingWithAudio/)
- [媒体播放器](https://developer.xamarin.com/api/type/Android.Media.MediaPlayer/)
- [媒体录制器](https://developer.xamarin.com/api/type/Android.Media.MediaRecorder/)
- [Audio Manager](https://developer.xamarin.com/api/type/Android.Media.AudioManager/)
- [音频曲目](https://developer.xamarin.com/api/type/Android.Media.AudioTrack/)
- [音频录制器](https://developer.xamarin.com/api/type/Android.Media.AudioRecord/)
