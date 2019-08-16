---
title: Jelly Bean 功能
description: '本文档将对 Android 4.1 中引入的开发人员提供的新功能进行简要概述。 这些功能包括: 增强的通知、Android 横梁的更新, 用于共享大型文件、多媒体更新、对等网络发现、动画和新权限。'
ms.prod: xamarin
ms.assetid: 23F57634-2EF9-5C15-C710-B3E19A5AF7E1
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: 614a0e3952db42d2587930b66bf71ce4c703d035
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/16/2019
ms.locfileid: "69524074"
---
# <a name="jelly-bean-features"></a>Jelly Bean 功能

_本文档将对 Android 4.1 中引入的开发人员提供的新功能进行简要概述。这些功能包括: 增强的通知、Android 横梁的更新, 用于共享大型文件、多媒体更新、对等网络发现、动画和新权限。_



## <a name="overview"></a>概述

Android 4.1 (API 级别 16) (也称为 "Jelly Bean") 已于2012年7月9日发布。 本文将简要介绍 Android 4.1 中用于使用 Xamarin 的开发人员的一些新功能。 引入的一些新功能是动画的增强功能, 用于启动活动、照相机的新声音以及改进的应用程序堆栈导航支持。 现在可以用意向进行剪切和粘贴。

Android 应用程序的稳定性经过改进, 能够隔离对不稳定的内容提供程序的依赖关系。 服务也可能是隔离的, 因此只能由启动它们的活动访问。

已为使用 Bonjour、UPnP 或多播 DNS 服务的网络服务发现添加了支持。 现在, 可以使用格式文本、操作按钮和大图像的更丰富的通知。

最后, Android 4.1 添加了几个新权限。



## <a name="requirements"></a>要求

若要使用 Jelly Bean 开发 Xamarin Android 应用程序, 需要 Android SDK 安装 Xamarin 4.2.6 或更高版本以及 Android 4.1 (API 级别 16), 如以下屏幕截图所示:

[![在 Android SDK 管理器中选择 Android 4。1](jelly-bean-images/image1.png)](jelly-bean-images/image1.png#lightbox)



## <a name="whats-new"></a>新增功能



### <a name="animations"></a>Animations

可以使用缩放动画或使用`ActivityOptions`类的自定义动画来启动活动。 提供以下新方法以支持这些动画:

- `MakeScaleUpAnimation`–这将创建一个动画, 用于在屏幕上从起始位置和大小向上缩放活动窗口。
- `MakeThumbnailScaleUpAnimation`–这将创建从屏幕上指定位置向上缩放缩略图的动画。
- `MakeCustomAnimation`–这将从应用程序中的资源创建动画。 当活动打开时, 有一个动画用于活动停止时的另一个。


新`TimeAnimator`类提供一个接口, `TimeAnimator.ITimeListener`该接口可在每次动画中帧发生更改时通知应用程序。 例如, 请考虑以下实现`TimeAnimator.ITimeListener`:

```csharp
class MyTimeListener : Java.Lang.Object,  TimeAnimator.ITimeListener
{
    public void OnTimeUpdate(TimeAnimator animation, long totalTime, long deltaTime)
    {
        Log.Debug("Activity1", "totalTime={0}, deltaTime={1}", totalTime, deltaTime);
    }
}
```

现在, 若要使用类, 将创建的`TimeAnimator`实例, 并设置侦听器:

```csharp
var animator = new TimeAnimator();
animator.SetTimeListener(new MyTimeListener());
animator.Start();
```

实例运行时, 它将调用`ITimeAnimator.ITimeListener`, 然后它将记录 animator 的运行时间以及自上次调用方法以来的时间长度。 `TimeAnimator`



### <a name="application-stack-navigation"></a>应用程序堆栈导航

Android 4.1 改进了 Android 3.0 中引入的应用程序堆栈导航。 通过指定`ParentName`的属性`ActivityAttribute`, 当用户在操作栏上按[向上键](https://developer.android.com/design/patterns/navigation.html#up-vs-back)时, android 可以打开适当的父活动, android `ParentName`将实例化属性指定的活动。 这使应用程序可以保留执行给定任务的活动层次结构。

对于大多数应用程序, `ParentName`在活动上设置的足以提供适用于导航应用程序堆栈的正确行为;Android 通过为每个父活动创建一系列意向, 合成所需的反向堆栈。 但是, 因为这是一个人工应用程序堆栈, 所以, 每个综合活动都不会有自然活动将具有的已保存状态。 若要为合成父活动提供保存状态, 活动可以重写`OnPrepareNavigationUpTaskStack`方法。 此方法接收一个`TaskStackBuilder`实例, 该实例将具有一个用于创建后向堆栈的意向对象的集合。 活动可以修改这些方法, 以便在创建综合活动时, 它将收到正确的状态信息。

对于更复杂的方案, 可以使用活动类的新方法来处理向上导航和构造后堆栈的行为:

- `OnNavigateUp`–通过重写此方法, 可以在按下 "**向上**" 按钮时执行自定义操作。
- `NavigateUpTo`–调用此方法将导致应用程序从当前活动导航到给定意向指定的活动。
- `ParentActivityIntent`–用于获取将启动当前活动的父活动的意图。
- `ShouldUpRecreateTask`–此方法用于查询是否必须创建合成 back 堆栈才能向上导航到父活动。 如果`true`必须创建合成堆栈, 则返回。 
- `FinishAffinity`–调用此方法将在当前任务中完成当前活动及其下的所有活动, 该任务具有相同的任务关联。
- `OnCreateNavigateUpTaskStack`–当需要完全控制如何创建合成堆栈时, 此方法将被重写。




### <a name="camera"></a>照相机

有一个新的接口`Camera.IAutoFocusMoveCallback`, 可用于检测自动焦点开始或停止移动的时间。 可在以下代码片段中查看此新接口的示例:

```csharp
public class AutoFocusCallbackActivity : Activity, Camera.IAutoFocusCallback
{
    public void OnAutoFocus(bool success, Camera camera)
    {
        // camera is an instance of the camera service object.

        if (success)
        {
            // Auto focus was successful - do something here.
        }
        else
        {
            // Auto focus didn't happen for some reason - react to that here.
        }
    }
}
```

新类`MediaActionSound`提供一组 API, 用于为各种媒体操作生成声音。 照相机可能会发生多种操作, 这些操作是通过枚举`Android.Media.MediaActionSoundType`定义的:

- `MediaActionSoundType.FocusComplete`–焦点完成时播放的声音。
- `MediaActionSoundType.ShutterClick`–当拍摄静止图像图片时, 将播放此声音。
- `MediaActionSoundType.StartVideoRecording`–此声音用于指示视频的开始记录。
- `MediaActionSoundType.StopVideoRecording`–将播放此声音以指示视频录制的结束。


下面的代码片段演示如何使用`MediaActionSound`类:

```csharp
var mediaActionPlayer = new MediaActionSound();

// Preload the sound for a shutter click.
mediaActionPlayer.Load(MediaActionSoundType.ShutterClick);
var button = FindViewById<Button>(Resource.Id.MyButton);

// Play the sound on a button click.
button.Click += (sender, args) => mediaActionPlayer.Play(MediaActionSoundType.ShutterClick);

// This releases the preloaded resources. Don’t make any calls on
// mediaActionPlayer after this.
mediaActionPlayer.Release();
```



### <a name="connectivity"></a>连接性



#### <a name="android-beam"></a>Android 无线发送

Android 横梁是一种基于 NFC 的技术, 它允许两个 Android 设备相互通信。 Android 4.1 为传输大型文件提供了更好的支持。 使用新方法`NfcAdapter.SetBeamPushUris()`时, Android 会在备用传输机制 (如蓝牙) 之间切换, 以实现快速传送速度。



#### <a name="network-services-discovery"></a>网络服务发现

Android 4.1 包含新的 API, 用于基于 DNS 的多播服务发现。
这允许应用程序检测 Wi-fi 并将其与其他设备 (如打印机、照相机和媒体设备) 连接。 这些新的 API 在`Android.Net.Nsd`包中。

若要创建可供其他服务使用的服务, `NsdServiceInfo`类用于创建将定义服务属性的对象。 然后, 将此对象与`NsdManager.RegisterService()`的`NsdManager.ResolveListener`实现一起提供。 的`NsdManager.ResolveListener`实现用于通知成功注册并注销服务。

发现网络上的服务, 并将的`Nsd.DiscoveryListener`实现`NsdManager.discoverServices()`传递给。



#### <a name="network-usage"></a>网络使用情况

新方法`ConnectivityManager.IsActiveNetworkMetered`允许设备检查其是否连接到按流量计费的网络。 此方法可用于通过准确通知用户对数据操作收取昂贵费用来帮助管理数据使用。



#### <a name="wifi-direct-service-discovery"></a>WiFi 直接服务发现

类`WifiP2pManager`是在 Android 4.0 中引入的, 以支持*zeroconf*。 Zeroconf (零配置网络) 是一组技术, 使设备 (计算机、打印机、电话) 能够自动连接到网络, 并且可以干预用户网络操作员或特殊的配置服务器。

在 Jelly Bean 中`WifiP2pManager` , 可以使用*Bonjour*或*Upnp*发现附近的设备。 Bonjour 是 zeroconf 的 Apple 实现。 Upnp 是一组也支持 zeroconf 的网络协议。 添加了`WiFiP2pManager`以下方法来支持 wi-fi 服务发现:

- `AddLocalService()`–此方法用于通过 Wi-fi 向对等端发现应用程序即服务。
- `AddServiceRequest(`) –此方法用于向框架发送服务发现请求。 它用于初始化 Wi-fi 服务发现。
- `SetDnsSdResponseListeners()`–此方法用于注册要在接收来自 Bonjour 的发现请求的响应时调用的回调。
- `SetUpnpServiceResponseListener()`–此方法用于注册在接收对发现请求的响应时要调用的回调。




### <a name="content-providers"></a>内容提供商

类接收了新的`AcquireUnstableContentProvider`方法。 `ContentResolver` 此方法允许应用程序获取 "不稳定" 的内容提供程序。 通常情况下, 当应用程序获取内容提供程序, 并且该内容提供程序崩溃时, 应用程序将会崩溃。 使用此方法调用时, 如果内容提供程序崩溃, 应用程序将不会崩溃。 相反, `Android.OS.DeadObjectionException`将在内容提供程序的调用中引发, 以通知应用程序内容提供程序已消失。 与其他应用程序的内容提供商交互时, "不稳定" 内容提供程序很有帮助, 因为其他应用程序的错误代码不会影响其他应用程序。



### <a name="copy-and-paste-with-intents"></a>用意向复制和粘贴

类现在可以通过`Intent.ClipData`属性与对象关联。`ClipData` `Intent` 此方法允许将剪贴板中的额外数据与意向传输。 的`ClipData`实例可以包含一个或多个`ClipData.Item`。 `ClipData.Item`是以下类型的项:

- **文本**–这是任何文本字符串, 无论是 HTML 还是任何格式受内置 Android 样式范围支持的字符串。
- **意向**–任何`Intent`对象。
- **Uri** –可以是任何 Uri, 例如 HTTP 书签或内容提供商的 uri。




### <a name="isolated-services"></a>独立服务

隔离服务是在其自身的特殊进程下运行的服务, 不具有其自己的权限。 与服务的唯一通信是在启动服务并通过服务 API 绑定到服务时。 通过在装饰服务类的`IsolatedProcess="true"` `ServiceAttribute`中设置属性, 可以将服务声明为隔离。


### <a name="media"></a>媒体

新`Android.Media.MediaCodec`类提供低级别媒体编解码器的 API。 应用程序可以查询系统以确定设备上可用的低级编解码器。

添加了`Android.Media.Audiofx.AudioEffect`新的子类, 以支持捕获的音频上的其他音频预处理:

- `Android.Media.Audiofx.AcousticEchoCanceler`–此类用于预处理音频, 以从远程方删除从捕获的音频信号发出的信号。 例如, 从语音通信应用程序中删除回声。
- `Android.Media.Audiofx.AutomaticGainControl`–此类用于通过提高或降低输入信号来规范化捕获的信号, 以便输出信号为常量。
- `Android.Media.Audiofx.NoiseSuppressor`–此类将删除捕获的信号中的背景噪音。


并非所有设备都支持这些效果。 此方法`AudioEffect.IsAvailable`应由应用程序调用, 以查看在运行应用程序的设备上是否支持所涉及的音频效果。

类现在支持`SetNextMediaPlayer()`通过方法进行无缝播放。 `MediaPlayer` 此新方法指定当前媒体播放机完成播放时要启动的下一个 MediaPlayer。

以下新类提供了标准机制和 UI, 可用于选择媒体的播放位置:

- `MediaRouter`–此类允许应用程序控制从设备到外部扬声器或其他设备的媒体通道路由。
- `MediaRouterActionProvider`和`MediaRouteButton` –这些类可帮助提供用于选择和播放媒体的一致 UI。




### <a name="notifications"></a>通知

Android 4.1 允许应用程序更灵活, 并可控制显示通知。 现在, 应用程序可以向用户显示更大更好的通知。 新方法`NotificationBuilder.SetStyle()`允许在通知上设置新的三种新样式之一:

- `Notification.BigPictureStyle`–这是一个帮助器类, 它将生成将包含图像的通知。 下图显示了包含大图像的通知的示例:


 [![BigPictureStyle 通知的示例屏幕截图](jelly-bean-images/image2.png)](jelly-bean-images/image2.png#lightbox)

- `Notification.BigTextStyle`–这是一个帮助器类, 它将生成将具有多行文本的通知, 如电子邮件。 可以在以下屏幕截图中查看此新通知样式的示例:


 [![BigTextStyle 通知的示例屏幕截图](jelly-bean-images/image3.png)](jelly-bean-images/image3.png#lightbox)

- `Notification.InboxStyle`–这是一个帮助器类, 它将生成包含字符串列表的通知, 如电子邮件中的代码段, 如以下屏幕截图所示:


 [![通知的示例屏幕截图。 InboxStyle 通知](jelly-bean-images/image4.png)](jelly-bean-images/image4.png#lightbox)

通知使用普通或更大的样式时, 可以在通知消息的底部添加最多两个操作按钮。
下面的屏幕截图中显示了这种情况的一个示例, 其中的操作按钮显示在通知底部:

 [![通知消息下显示的操作按钮的示例屏幕截图](jelly-bean-images/image5.png)](jelly-bean-images/image5.png#lightbox)

`Notification`类收到了新常量, 使开发人员可以为通知指定五个优先级别之一。 可以使用`Priority`属性设置通知。



### <a name="permissions"></a>权限

添加了以下新权限:

- `READ_EXTERNAL_STORAGE`-应用程序需要对外部存储的只读访问权限。 默认情况下, 所有应用程序都具有读取访问权限, 但 Android 的未来版本将要求应用程序显式请求读取访问权限。
- `READ_USER_DICTIONARY`-允许对用户的 word 字典进行读取访问。
- `READ_CALL_LOG`-允许应用程序通过读取调用日志来获取有关传入和传出调用的信息。
- `WRITE_CALL_LOG`-允许应用程序在电话上写入呼叫日志。
- `WRITE_USER_DICTIONARY`-允许应用程序写入用户的 word 词典。


需要注意`READ_EXTERNAL_STORAGE`的一个重要更改-目前, Android 会自动授予此权限。 未来版本的 Android 将要求应用程序在授予权限之前请求此权限。



## <a name="summary"></a>总结

本文介绍了一些可在 Android 4.1 (API 级别 16) 中使用的新 API。 它突出显示了动画的一些变化, 并对活动启动进行了动画处理, 并引入了新的 API, 用于使用 Bonjour 或 UPnP 等协议的其他设备的网络发现。 还突出显示了对 API 的其他更改, 如通过意向剪切和粘贴数据、使用独立服务或 "不稳定" 内容提供程序的能力。

然后, 本文将介绍如何更新通知, 并介绍了一些已随 Android 4.1 引入的新权限


## <a name="related-links"></a>相关链接

- [时间动画示例 (示例)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/platformfeatures-timeanimatorexample)
- [Android 4.1 Api](https://developer.android.com/about/versions/android-4.1.html)
- [任务和反向堆栈](https://developer.android.com/guide/components/tasks-and-back-stack.html)
- [用 Back 和 Up 导航](https://developer.android.com/design/patterns/navigation.html)
