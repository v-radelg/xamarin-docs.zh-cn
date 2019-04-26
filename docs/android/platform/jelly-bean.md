---
title: Jelly Bean 功能
description: 本文档将 Android 4.1 中引入的开发人员提供的新功能的高级别概述。 这些功能包括： 增强的通知，Android 无线发送共享大型文件、 多媒体、 对等网络发现、 动画、 新的权限的更新的更新。
ms.prod: xamarin
ms.assetid: 23F57634-2EF9-5C15-C710-B3E19A5AF7E1
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: 24fc14b0342591c56f5bf91862b0d94759a42834
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61078379"
---
# <a name="jelly-bean-features"></a>Jelly Bean 功能

_本文档将 Android 4.1 中引入的开发人员提供的新功能的高级别概述。这些功能包括： 增强的通知，Android 无线发送共享大型文件、 多媒体、 对等网络发现、 动画、 新的权限的更新的更新。_



## <a name="overview"></a>概述

Android 4.1 (API 级别 16)，也称为"Jelly Bean"，是在 2012 年 7 月 9 日的版本。 本文将使用 Xamarin.Android 的开发人员提供一些 Android 4.1 中的新增功能的高级别介绍。 某些引入这些新功能是用于启动活动、 相机、 新的声音并改进了对应用程序堆栈导航支持动画的增强功能。 现可与意图剪切和粘贴。

Android 应用程序的稳定性得到改进来隔离对不稳定的内容提供商的依赖关系的功能。 服务也可能是独立，以便它们仅可由访问启动它们的活动。

已添加了支持的网络服务发现使用 Bonjour、 UPnP 或多播的 DNS 基于服务。 就现在可以更丰富的文本、 操作按钮和图像的大小已设置格式的通知。

最后几个新权限已添加 Android 4.1 中。



## <a name="requirements"></a>要求

若要开发 Xamarin.Android 应用程序使用 Jelly Bean 需要 Xamarin.Android 4.2.6-或更高版本以及 Android 4.1 (API 级别为 16) 安装通过 Android SDK 管理器，如以下屏幕截图中所示：

[![在 Android SDK 管理器中选择 Android 4.1](jelly-bean-images/image1.png)](jelly-bean-images/image1.png#lightbox)



## <a name="whats-new"></a>新增功能



### <a name="animations"></a>Animations

可以通过使用缩放动画或自定义动画启动活动`ActivityOptions`类。 提供以下新方法来支持这些动画：

-   `MakeScaleUpAnimation` – 这将创建可从起始位置和大小在屏幕上的活动窗口内扩展的动画。
-   `MakeThumbnailScaleUpAnimation` -这将从缩略图在屏幕上的指定位置中创建的动画，向上扩展。
-   `MakeCustomAnimation` – 这将从应用程序中的资源创建动画。 没有一个动画，以打开活动时，另一个用于活动停止时。


新`TimeAnimator`类提供了一个接口`TimeAnimator.ITimeListener`，可以每次以动画帧发生更改时通知应用程序。 例如，考虑以下实现`TimeAnimator.ITimeListener`:

```csharp
class MyTimeListener : Java.Lang.Object,  TimeAnimator.ITimeListener
{
    public void OnTimeUpdate(TimeAnimator animation, long totalTime, long deltaTime)
    {
        Log.Debug("Activity1", "totalTime={0}, deltaTime={1}", totalTime, deltaTime);
    }
}
```

现在，若要使用的类的实例`TimeAnimator`创建，并设置侦听器：

```csharp
var animator = new TimeAnimator();
animator.SetTimeListener(new MyTimeListener());
animator.Start();
```

作为`TimeAnimator`实例正在运行，它将调用`ITimeAnimator.ITimeListener`，哪个然后将日志如何运行和多长时间它因为自上次以来已方法已调用动画器已被长时间。



### <a name="application-stack-navigation"></a>应用程序堆栈导航

Android 4.1 改进了 Android 3.0 中引入了应用程序堆栈导航。 通过指定`ParentName`属性`ActivityAttribute`，当用户按下时，Android 可以打开正确的父活动[向上按钮](https://developer.android.com/design/patterns/navigation.html#up-vs-back)在操作栏中的 Android 将实例化指定的活动`ParentName`属性。 这允许应用程序保留的活动，使某个给定的任务的层次结构。

对于大多数应用程序设置`ParentName`活动是足够的信息适用于 Android 的导航的应用程序堆栈; 提供正确的行为Android 将通过创建一系列意向为每个父活动的合成必要 back 堆栈。 但是，由于这是将自定义应用程序堆栈，综合的每个活动将不能将具有自然活动的已保存的状态。 若要提供到综合父活动的已保存的状态，活动可能会重写`OnPrepareNavigationUpTaskStack`方法。 此方法接收`TaskStackBuilder`将有一系列的意图的实例对象，将使用 Android 创建 back 堆栈。 活动可能会修改这些意图，以便创建综合的活动时，它将接收正确的状态信息。

对于更复杂的方案，可用于处理导航的行为，并构造 back 堆栈 Activity 类上有新的方法：

-   `OnNavigateUp` – 通过重写此方法就可以执行自定义操作时<span class="ui">向上</span>按下按钮。
-   `NavigateUpTo` -调用此方法将导致应用程序以从当前活动导航到给定目的指定的活动。
-   `ParentActivityIntent` – 这用于获取意向，它将启动当前活动的父活动。
-   `ShouldUpRecreateTask` – 此方法用于查询是否必须创建综合的 back 堆栈以导航到父活动。 返回`true`如果必须创建综合的堆栈。 
-   `FinishAffinity` -调用此方法将完成当前活动和所有它下面当前任务中的活动具有相同的任务关联。
-   `OnCreateNavigateUpTaskStack` – 此方法被重写时需要具有完全控制如何创建综合的堆栈。




### <a name="camera"></a>照相机

没有新界面， `Camera.IAutoFocusMoveCallback`，这可用于检测何时自动聚焦已开始或停止移动。 以下代码段中，可以看到此新接口的示例：

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

新类`MediaActionSound`提供的 API 的一组用于生成适用于各种媒体操作的声音。 有多个操作可能出现的照相机，这些定义通过枚举`Android.Media.MediaActionSoundType`:

-   `MediaActionSoundType.FocusComplete` – 此将重点放完成时，将会播放的声音。
-   `MediaActionSoundType.ShutterClick` – 拍摄静止图像图片时，将播放这听起来。
-   `MediaActionSoundType.StartVideoRecording` – 使用这听起来指示开始的视频录制。
-   `MediaActionSoundType.StopVideoRecording` – 若要指示视频录制的末尾，将播放这听起来。


举例说明如何使用`MediaActionSound`类可以在以下代码片段中看到：

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

Android 无线发送是一种基于 NFC 技术，允许两个 Android 设备与彼此通信。 Android 4.1 传输的大型文件提供更好的支持。 使用新的方法时`NfcAdapter.SetBeamPushUris()`Android 都将改用备用传输机制 （例如蓝牙） 之间实现快速传输速度。



#### <a name="network-services-discovery"></a>网络服务发现

Android 4.1 包含新的 API 的多路广播基于 DNS 的服务发现。
这允许应用程序以检测并通过 Wi-fi 连接到其他设备，如打印机、 相机和媒体设备。 这些新的 API 位于`Android.Net.Nsd`包。

若要创建的服务，可供其他服务，`NsdServiceInfo`类用于创建一个对象，将定义服务的属性。 然后将此对象提供给`NsdManager.RegisterService()`的实现以及`NsdManager.ResolveListener`。 实现`NsdManager.ResolveListener`用于通知的成功注册和撤消服务注册。

若要发现的网络和实现上的服务`Nsd.DiscoveryListener`传递给`NsdManager.discoverServices()`。



#### <a name="network-usage"></a>网络使用情况

新的方法， `ConnectivityManager.IsActiveNetworkMetered` ，允许检查它是否连接到按流量计费的网络设备。 此方法可用于帮助管理数据使用情况的准确地通知用户可能昂贵的数据操作的费用。



#### <a name="wifi-direct-service-discovery"></a>WiFi 直接服务发现

`WifiP2pManager`类以支持 Android 4.0 中引入*zeroconf*。 Zeroconf （零个网络配置） 是一组技术，允许设备 （计算机、 打印机、 手机） 以自动连接到网络的人工网络运营商或特殊的配置服务器干预。

在 Jelly Bean`WifiP2pManager`可以发现附近的设备使用*Bonjour*或*Upnp*。 Bonjour 是 zeroconf Apple 的实现。 Upnp 还支持 zeroconf 的网络协议的设置。 以下方法添加到`WiFiP2pManager`以支持 Wi-fi 服务发现：

-   `AddLocalService()` – 使用此方法发现的对等方通过 Wi-fi 宣布作为服务的应用程序。
-   `AddServiceRequest(` ) – 此方法是将服务发现请求发送到框架。 它用于初始化的 Wi-fi 服务发现。
-   `SetDnsSdResponseListeners()` – 此方法用于注册要调用的接收从 Bonjour 发现请求的响应的回调。
-   `SetUpnpServiceResponseListener()` – 此方法用于注册要调用的接收的响应发现请求 Upnp 的回调。




### <a name="content-providers"></a>内容提供商

`ContentResolver`类已收到新的方法， `AcquireUnstableContentProvider`。 此方法允许应用程序获取"不稳定"的内容提供商。 通常情况下，如果应用程序获取内容提供商，而该内容提供程序发生崩溃，因此将应用程序。 使用此方法调用，如果发生故障，内容提供商，将不崩溃应用程序。 相反，`Android.OS.DeadObjectionException`将引发对内容提供程序的调用以通知的应用程序内容提供商具有已得到解决。 与其他应用程序中的内容提供商进行交互时，"不稳定"的内容提供商非常有用 – 它不太可能从其他应用程序的错误代码，将会影响另一个应用程序。



### <a name="copy-and-paste-with-intents"></a>复制和粘贴到意向

`Intent`类现在可以`ClipData`通过与其关联的对象`Intent.ClipData`属性。 此方法允许为额外的数据，从剪贴板将待传输的意图。 实例`ClipData`可以包含一个或多个`ClipData.Item`。 `ClipData.Item`以下类型的项：

-   **文本**– 这是任何文本，任一 HTML 字符串或其格式支持内置的 Android 样式的任何字符串跨越。
-  **意向**– 任何`Intent`对象。
-   **Uri** – 这可以是任何 URI，例如 HTTP 书签或内容提供程序的 URI。




### <a name="isolated-services"></a>隔离的服务

独立的服务是一种服务，在其自己的特殊进程下运行和不具有其自己的任何权限。 与该服务的唯一通信是何时启动该服务并将绑定到它通过服务 API。 可以通过设置的属性声明为独立的服务`IsolatedProcess="true"`在`ServiceAttribute`装饰服务类。


### <a name="media"></a>媒体

新`Android.Media.MediaCodec`类提供了对低级别的媒体编解码器的 API。 应用程序可以查询系统以找出哪些较低级别的编解码器是可在设备上。

新`Android.Media.Audiofx.AudioEffect`添加了子类以支持其他音频预上捕获音频处理过程：

-   `Android.Media.Audiofx.AcousticEchoCanceler` — 此类为预处理音频用于从捕获的音频信号从远程方删除信号。 例如，从语音通信应用程序删除 echo。
-   `Android.Media.Audiofx.AutomaticGainControl` — 此类用于通过在提升或降低输入的信号，以便输出信号是常量规范化捕获的信号。
-   `Android.Media.Audiofx.NoiseSuppressor` — 此类将从捕获的信号删除背景噪音。


并非所有设备将都支持这些效果。 该方法`AudioEffect.IsAvailable`应由运行该应用程序在设备上是否受支持的音频效果相关的应用程序调用。

`MediaPlayer`类现在支持具有无缝播放`SetNextMediaPlayer()`方法。 这种新方法指定下一步的 MediaPlayer 时当前媒体播放器完成其播放启动。

以下新类提供用于选择播放媒体的标准机制和 UI:

-   `MediaRouter` — 此类允许应用程序来控制路由到外部扬声器的设备或其他设备中的媒体通道。
-   `MediaRouterActionProvider` 和`MediaRouteButton`– 这些类帮助提供一致的用户界面，用于选择和播放媒体。




### <a name="notifications"></a>通知

Android 4.1 允许应用程序更大的灵活性和控制能力的显示通知。 应用程序现在可以向用户显示更大、 更好的通知。 新的方法，`NotificationBuilder.SetStyle()`允许新建三个新样式之一上通知设置：

-   `Notification.BigPictureStyle` – 这是将生成通知，其中将包含映像的一个帮助器类。 下图显示了一个通知，其中大映像的示例：


 [![BigPictureStyle 通知的示例屏幕截图](jelly-bean-images/image2.png)](jelly-bean-images/image2.png#lightbox)

-   `Notification.BigTextStyle` – 这是将生成将具有多行文本，如电子邮件通知的帮助器类。 以下屏幕截图中可以看到此新的通知样式的示例：


 [![BigTextStyle 通知的示例屏幕截图](jelly-bean-images/image3.png)](jelly-bean-images/image3.png#lightbox)

-   `Notification.InboxStyle` – 这是将生成包含一组字符串，如电子邮件中的代码片段的通知，如以下屏幕截图中所示的帮助器类：


 [![Notification.InboxStyle 通知的示例屏幕截图](jelly-bean-images/image4.png)](jelly-bean-images/image4.png#lightbox)

就可以通知使用正常或更大样式时，底部的通知消息添加最多两个操作按钮。
出现这种可在以下屏幕截图中，操作按钮是可见底部的通知：

 [![示例屏幕截图中显示以下通知消息的操作按钮](jelly-bean-images/image5.png)](jelly-bean-images/image5.png#lightbox)

`Notification`类已收到新常量，允许开发人员指定一个五个优先级别的通知。 这些可以设置通知使用`Priority`属性。



### <a name="permissions"></a>权限

已添加以下新权限：

-   `READ_EXTERNAL_STORAGE` -应用程序需要只读的访问到外部存储。 当前所有应用程序默认情况下，具有读取访问权限，但未来版本的 Android 将需要应用程序显式请求读取访问权限。
-   `READ_USER_DICTIONARY` -允许对用户的 word 词典的读取访问。
-   `READ_CALL_LOG` -允许应用程序通过读取调用日志来获取有关传入和传出调用的信息。
-   `WRITE_CALL_LOG` -允许应用程序能够在手机上写入调用日志。
-   `WRITE_USER_DICTIONARY` -允许应用程序要写入到用户的 word 词典。


需要注意的重要更改`READ_EXTERNAL_STORAGE`– 当前由 Android 自动授予此权限。 未来版本的 Android 将需要应用程序请求之前此权限授予该权限。



## <a name="summary"></a>总结

本文介绍了一些新的 API 的 Android 4.1 (API 级别为 16) 中可用。 它突出显示某些动画和进行动画处理的活动，该活动发布的更改，并引入了用于使用 Bonjour 或 UPnP 等协议的其他设备的网络发现的新 API。 API 的其他更改是将突出显示，例如剪切和粘贴数据通过意向，能够使用独立的服务或"不稳定"的内容提供商的功能。

这篇文章，然后接着介绍更新通知，并讨论某些 Android 4.1 中引入了新权限


## <a name="related-links"></a>相关链接

- [时间的动画示例 （示例）](https://developer.xamarin.com/samples/monodroid/PlatformFeatures/TimeAnimatorExample/)
- [Android 4.1 Api](https://developer.android.com/about/versions/android-4.1.html)
- [任务和后退堆栈](https://developer.android.com/guide/components/tasks-and-back-stack.html)
- [使用后退和向上导航](https://developer.android.com/design/patterns/navigation.html)
