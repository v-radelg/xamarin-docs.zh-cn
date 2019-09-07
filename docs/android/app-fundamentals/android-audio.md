---
title: Android 音频
description: Android OS 为多媒体提供了广泛的支持，包括音频和视频。 本指南重点介绍 Android 中的音频，并介绍如何使用内置的音频播放器和录像机类以及低级音频 API 播放和录制音频。 还介绍了如何使用其他应用程序广播的音频事件，使开发人员能够构建表现良好的应用程序。
ms.prod: xamarin
ms.assetid: 646ED563-C34E-256D-4B56-29EE99881C27
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/28/2018
ms.openlocfilehash: 8719d521fe512f348aade0a3d43d2f0b0d3bf0ec
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70755629"
---
# <a name="android-audio"></a>Android 音频

_Android OS 为多媒体提供了广泛的支持，包括音频和视频。本指南重点介绍 Android 中的音频，并介绍如何使用内置的音频播放器和录像机类以及低级音频 API 播放和录制音频。还介绍了如何使用其他应用程序广播的音频事件，使开发人员能够构建表现良好的应用程序。_

## <a name="overview"></a>概述

现代移动设备采用了以前需要的设备&ndash;相机、音乐播放机和录像机等专用功能。 因此，多媒体框架已成为移动 Api 中的一流功能。

Android 为多媒体提供广泛支持。 本文介绍如何在 Android 中使用音频，并介绍了以下主题

1. **通过 MediaPlayer 播放音频**&ndash;使用`AudioTrack`内置`MediaPlayer`类播放音频，包括本地音频文件和具有类的流音频文件。

2. **录制音频**&ndash;使用内置`MediaRecorder`类记录音频。

3. 使用**音频通知**&ndash;使用音频通知来创建正常运行的应用程序，这些应用程序可以通过挂起或取消其音频输出来正确响应事件（例如传入的电话呼叫）。

4. 使用**低级别音频**通过直接写入内存`AudioTrack`缓冲区来使用类播放音频。 &ndash; 使用`AudioRecord`类记录音频并直接从内存缓冲区读取。

## <a name="requirements"></a>要求

本指南需要 Android 2.0 （API 级别5）或更高版本。 请注意，必须在设备上进行调试音频。

需要在`RECORD_AUDIO` **androidmanifest.xml**中请求权限：

![启用了录音\_音频的 Android 清单的 "所需权限" 部分](android-audio-images/image01.png)

## <a name="playing-audio-with-the-mediaplayer-class"></a>用 MediaPlayer 类播放音频

在 Android 中播放音频的最简单方法是采用内置的[MediaPlayer](xref:Android.Media.MediaPlayer)类。
`MediaPlayer`可以通过传入文件路径来播放本地文件或远程文件。 但是， `MediaPlayer`非常区分状态，并且在错误的状态下调用其方法之一将导致引发异常。 按照下面所述的顺序`MediaPlayer`进行交互很重要，以免出现错误。

### <a name="initializing-and-playing"></a>正在初始化和播放

播放音频时`MediaPlayer`需要以下顺序：

1. 实例化新的[MediaPlayer](xref:Android.Media.MediaPlayer)对象。

1. 将文件配置为通过[SetDataSource](xref:Android.Media.MediaPlayer.SetDataSource*)方法播放。

1. 调用[Prepare](xref:Android.Media.MediaPlayer.Prepare)方法来初始化播放器。

1. 调用[start](xref:Android.Media.MediaPlayer.Start)方法开始播放音频。

下面的代码示例演示了这种用法：

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

### <a name="suspending-and-resuming-playback"></a>挂起和继续播放

可以通过调用[Pause](xref:Android.Media.MediaPlayer.Pause)方法来挂起播放：

```csharp
player.Pause();
```

若要恢复暂停播放，请调用[Start](xref:Android.Media.MediaPlayer.Start)方法。
这将从播放中的暂停位置继续：

```csharp
player.Start();
```

在播放机上调用[Stop](xref:Android.Media.MediaPlayer.Stop)方法会结束正在进行的播放：

```csharp
player.Stop();
```

当不再需要播放机时，必须通过调用[Release](xref:Android.Media.MediaPlayer.Release)方法释放资源：

```csharp
player.Release();
```

## <a name="using-the-mediarecorder-class-to-record-audio"></a>使用 MediaRecorder 类录制音频

`MediaPlayer`用于在 Android 中录制音频的必然结果是[MediaRecorder](xref:Android.Media.MediaRecorder)类。 与类似`MediaPlayer`，它是区分状态的，并通过多个状态进行转换，使之能够开始记录。 若要录制音频， `RECORD_AUDIO`必须设置权限。 有关如何设置应用程序权限的说明，请参阅使用[androidmanifest.xml](~/android/platform/android-manifest.md)。

### <a name="initializing-and-recording"></a>初始化和记录

录制音频时`MediaRecorder`需要执行以下步骤：

1. 实例化新的[MediaRecorder](xref:Android.Media.MediaRecorder)对象。

2. 指定要使用哪些硬件设备通过[SetAudioSource](xref:Android.Media.MediaRecorder.SetAudioSource*)方法捕获音频输入。

3. 使用[SetOutputFormat](xref:Android.Media.MediaRecorder.SetOutputFormat*)方法设置输出文件音频格式。 有关支持的音频类型的列表，请参阅[Android 支持的媒体格式](https://developer.android.com/guide/appendix/media-formats.html)。

4. 调用[SetAudioEncoder](xref:Android.Media.MediaRecorder.SetAudioEncoder*)方法来设置音频编码类型。

5. 调用[SetOutputFile](xref:Android.Media.MediaRecorder.SetOutputFile*)方法以指定写入音频数据的输出文件的名称。

6. 调用[Prepare](xref:Android.Media.MediaRecorder.Prepare)方法以初始化记录器。

7. 调用[start](xref:Android.Media.MediaRecorder.Start)方法开始记录。

下面的代码示例演示了此顺序：

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

若要停止录制，请`Stop` `MediaRecorder`对执行以下操作：

```csharp
recorder.Stop();
```

### <a name="cleaning-up"></a>清理

`MediaRecorder`停止后, 调用 [Reset](xref:Android.Media.MediaRecorder.Reset) 方法, 使其重新进入其空闲状态:

```csharp
recorder.Reset();
```

当不再需要`MediaRecorder`时, 必须通过调用 [Release](xref:Android.Media.MediaRecorder.Release) 方法释放其资源:

```csharp
recorder.Release();
```

## <a name="managing-audio-notifications"></a>管理音频通知

### <a name="the-audiomanager-class"></a>AudioManager 类

[AudioManager](xref:Android.Media.AudioManager)类提供对音频通知的访问，使应用程序能够了解音频事件发生的时间。 此服务还提供对其他音频功能（如音量和铃声模式控制）的访问。 `AudioManager`允许应用程序处理音频通知以控制音频播放。

### <a name="managing-audio-focus"></a>管理音频焦点

所有正在运行的应用程序共享设备的音频资源（内置播放机和记录器）。

从概念上讲，这类似于台式计算机上只有一个应用程序具有键盘焦点的应用程序：在通过鼠标单击其中一个正在运行的应用程序选择一个运行的应用程序后，键盘输入只会转到该应用程序。

音频焦点与此类似，可防止多个应用程序同时播放或录制音频。 它比键盘焦点更复杂，因为它是自愿&ndash;的，应用程序可以忽略它当前没有音频焦点，而不管&ndash;和，因为有不同类型的音频焦点可以请求. 例如，如果请求者只需播放音频一小段时间，则可能会请求暂时性的焦点。

可能会立即授予音频焦点，或最初拒绝音频焦点，稍后将其授予。 例如，如果某个应用程序在电话呼叫期间请求音频焦点，则它将被拒绝，但在电话呼叫完成后，就可以获得焦点。 在这种情况下，将注册侦听器以便在音频焦点消失时进行相应的响应。 请求音频焦点用于确定是否可以播放或录制音频。

有关音频焦点的详细信息，请参阅[管理音频焦点](https://developer.android.com/training/managing-audio/audio-focus.html)。

#### <a name="registering-the-callback-for-audio-focus"></a>为音频焦点注册回调

从注册`FocusChangeListener`回调是获取和释放音频焦点`IOnAudioChangeListener`的重要部分。 这是因为，对音频焦点的授予可能会推迟到稍后的时间。 例如，应用程序可能会请求在有电话呼叫时播放音乐。 电话呼叫结束之前，将不会授予音频焦点。

出于此原因，回调对象将作为参数传递到`GetAudioFocus`的`AudioManager`方法中，这是注册回调的此调用。 如果最初拒绝音频焦点，但后来却被授予，则会通过调用`OnAudioFocusChange`回调来通知应用程序。 使用相同的方法来告知应用程序音频焦点已消失。

当应用程序使用完音频资源后，它将调用`AbandonFocus`的方法`AudioManager`，并再次传入回调。 这会注销回调并释放音频资源，以便其他应用程序可以获得音频焦点。

#### <a name="requesting-audio-focus"></a>请求音频焦点

请求设备的音频资源所需的步骤如下所示：

1. 获取`AudioManager`系统服务的句柄。

2. 创建回调类的实例。

3. 通过对调用`RequestAudioFocus`方法`AudioManager`来请求设备的音频资源。 参数是回调对象、流类型（音乐、语音呼叫、环等）和所请求的访问权限的类型（例如，可以暂时请求音频资源或无限期地请求音频资源）。

4. 如果授予了该请求， `playMusic`则会立即调用方法，并且音频开始播放。

5. 如果拒绝请求，则不执行其他操作。 在这种情况下，仅当稍后授予请求时，才会播放音频。

下面的代码示例显示了以下步骤：

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

#### <a name="releasing-audio-focus"></a>正在释放音频焦点

播放跟踪完成后，将调用上`AbandonFocus` `AudioManager`的方法。 这允许其他应用程序获取设备的音频资源。 如果其他应用程序已注册自己的侦听器，则这些应用程序将收到此音频焦点更改的通知。

## <a name="low-level-audio-api"></a>低级别音频 API

低级别音频 Api 可以更好地控制音频播放和录制，因为它们直接与内存缓冲区交互，而不是使用文件 Uri。 在某些情况下，这种方法更可取。 这种情况包括：

1. 从加密的音频文件播放时。

2. 播放连续的短剪辑时。

3. 音频流式处理。

### <a name="audiotrack-class"></a>AudioTrack 类

[AudioTrack](xref:Android.Media.AudioTrack)类使用低级别音频 api 进行记录，并且是`MediaPlayer`类的低级别等效项。

#### <a name="initializing-and-playing"></a>正在初始化和播放

若要播放音频，必须实例化`AudioTrack`的新实例。 传入[构造函数](xref:Android.Media.AudioTrack)的参数列表指定如何播放缓冲区中包含的音频示例。 参数包括：

1. 流类型&ndash;语音、铃声、音乐、系统或警报。

2. 以&ndash; Hz 表示的采样速率的频率。

3. 通道配置&ndash; Mono 或立体声。

4. 8位&ndash;或16位编码的音频格式。

5. 缓冲区大小&ndash; （以字节为单位）。

6. 缓冲区模式&ndash;流式处理或静态。

构造后, 将调用`AudioTrack`的[播放](xref:Android.Media.AudioTrack.Play)方法, 将其设置为开始播放。 将音频缓冲区写入到`AudioTrack`开始播放：

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

调用[pause](xref:Android.Media.AudioTrack.Pause)方法以暂停播放：

```csharp
audioTrack.Pause();
```

调用[Stop](xref:Android.Media.AudioTrack.Stop)方法将永久终止播放：

```csharp
audioTrack.Stop();
```

#### <a name="cleanup"></a>清理

当不再需要`AudioTrack`时, 必须通过调用 [Release](xref:Android.Media.AudioTrack.Release) 释放其资源:

```csharp
audioTrack.Release();
```

### <a name="the-audiorecord-class"></a>AudioRecord 类

[AudioRecord](xref:Android.Media.AudioRecord)类等效于`AudioTrack`录制端。 与`AudioTrack`类似，它会直接使用内存缓冲区来代替文件和 uri。 它要求`RECORD_AUDIO`在清单中设置权限。

#### <a name="initializing-and-recording"></a>初始化和记录

第一步是构造一个新的[AudioRecord](xref:Android.Media.AudioRecord)对象。 传入[构造函数](xref:Android.Media.AudioRecord)的参数列表提供记录所需的所有信息。 与中`AudioTrack`不同，其中的参数是很大程度的枚举， `AudioRecord`中的等效参数是整数。 这些问题包括：

1. 硬件音频输入源，如麦克风。

2. 流类型&ndash;语音、铃声、音乐、系统或警报。

3. 以&ndash; Hz 表示的采样速率的频率。

4. 通道配置&ndash; Mono 或立体声。

5. 8位&ndash;或16位编码的音频格式。

6. 缓冲区大小（以字节为单位）

构造后，将调用其[StartRecording 方法。](xref:Android.Media.AudioRecord.StartRecording) `AudioRecord` 现在可以开始录制了。 `AudioRecord`持续读取音频缓冲区的输入，并将此输入写入音频文件。

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

#### <a name="stopping-the-recording"></a>正在停止记录

调用[Stop](xref:Android.Media.AudioRecord.Stop)方法会终止记录：

```csharp
audRecorder.Stop();
```

#### <a name="cleanup"></a>清理

当不再需要 `AudioRecord`对象时,调用其[Release](xref:Android.Media.AudioRecord.Release)方法会释放与其关联的所有资源:

```csharp
audRecorder.Release();
```

## <a name="summary"></a>总结

Android OS 提供了一个功能强大的框架，用于播放、记录和管理音频。 本文介绍如何使用高级`MediaPlayer`和`MediaRecorder`类播放和录制音频。 接下来，它探讨了如何使用音频通知在不同应用程序之间共享设备的音频资源。 最后，它介绍了如何使用低级别 Api 播放和录制音频，后者直接与内存缓冲区交互。

## <a name="related-links"></a>相关链接

- [使用音频（示例）](https://docs.microsoft.com/samples/xamarin/monodroid-samples/example-workingwithaudio)
- [Media Player](xref:Android.Media.MediaPlayer)
- [媒体录制器](xref:Android.Media.MediaRecorder)
- [音频管理器](xref:Android.Media.AudioManager)
- [音频轨](xref:Android.Media.AudioTrack)
- [音频录制器](xref:Android.Media.AudioRecord)
