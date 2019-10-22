---
title: 前景服务
ms.prod: xamarin
ms.assetid: C10FD999-7A91-4708-B642-0C1B0901BD24
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/19/2018
ms.openlocfilehash: 6f3427641ba4ace3b640fcc970fd33f55087a9c8
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/21/2019
ms.locfileid: "68644112"
---
# <a name="foreground-services"></a>前景服务

前台服务是一种特殊类型的绑定服务或已启动的服务。 偶尔，服务会执行用户必须主动识别的任务，这些服务称为_前台服务_。 前台服务的一个示例是在驱动或浏览时为用户提供指示的应用。 即使应用程序处于后台，服务也必须具有足够的资源才能正常工作，并且用户有一种快速而方便的方法来访问应用程序。 对于 Android 应用程序，这意味着前台服务应获得比 "常规" 服务更高的优先级，并且前台服务必须提供一个 `Notification`，只要服务正在运行，它就会显示在该服务中。

若要启动前台服务，应用程序必须派上通知 Android 启动该服务的意图。 然后，服务必须将自身注册为 Android 的前台服务。 在 Android 8.0 （或更高版本）上运行的应用程序应使用 `Context.StartForegroundService` 方法来启动该服务，而使用较早版本 Android 的设备上运行的应用程序应使用 `Context.StartService`

此C#扩展方法是如何启动前台服务的示例。 在 Android 8.0 及更高版本中，它将使用 `StartForegroundService` 方法，否则将使用较旧的 `StartService` 方法。

```csharp
public static void StartForegroundServiceCompat<T>(this Context context, Bundle args = null) where T : Service
{
    var intent = new Intent(context, typeof(T));
    if (args != null) 
    {
        intent.PutExtras(args);
    }

    if (Android.OS.Build.VERSION.SdkInt >= Android.OS.BuildVersionCodes.O)
    {
        context.StartForegroundService(intent);
    }
    else
    {
        context.StartService(intent);
    }
}
```

## <a name="registering-as-a-foreground-service"></a>注册为前台服务

前台服务启动后，它必须通过调用[`StartForeground`](xref:Android.App.Service.StartForeground*)向 Android 注册自身。 如果服务是以 `Service.StartForegroundService` 方法启动的，但未注册自身，则 Android 将停止服务并将应用标记为 "无响应"。

`StartForeground` 采用两个参数，两者都是必需的：

- 在应用程序中唯一的整数值，用于标识服务。
- 当服务正在运行时，Android 将在状态栏中显示的 `Notification` 对象。

只要服务正在运行，Android 就会在状态栏中显示通知。 通知至少会向运行该服务的用户提供视觉提示。 理想情况下，通知应为用户提供应用程序的快捷方式或其他操作按钮，以控制应用程序。 这种情况的一个示例是音乐播放器 &ndash; 显示的通知可能具有用于暂停/播放音乐、倒带到前一首歌曲或跳转到下一首歌曲的按钮。 

此代码片段是将服务注册为前台服务的示例：   

```csharp
// This is any integer value unique to the application.
public const int SERVICE_RUNNING_NOTIFICATION_ID = 10000;

public override StartCommandResult OnStartCommand(Intent intent, StartCommandFlags flags, int startId)
{
    // Code not directly related to publishing the notification has been omitted for clarity.
    // Normally, this method would hold the code to be run when the service is started.

    var notification = new Notification.Builder(this)
        .SetContentTitle(Resources.GetString(Resource.String.app_name))
        .SetContentText(Resources.GetString(Resource.String.notification_text))
        .SetSmallIcon(Resource.Drawable.ic_stat_name)
        .SetContentIntent(BuildIntentToShowMainActivity())
        .SetOngoing(true)
        .AddAction(BuildRestartTimerAction())
        .AddAction(BuildStopServiceAction())
        .Build();

    // Enlist this instance of the service as a foreground service
    StartForeground(SERVICE_RUNNING_NOTIFICATION_ID, notification);
}
```

之前的通知将显示类似于以下内容的状态栏通知：

![显示状态栏中的通知的图像](foreground-services-images/foreground-services-01.png "显示状态栏中的通知的图像")

此屏幕截图显示通知栏中的扩展通知，其中包含允许用户控制服务的两个操作：

![显示扩展通知的图像](foreground-services-images/foreground-services-02.png "显示展开通知的图像。")

有关通知的详细信息，请访问[Android 通知](~/android/app-fundamentals/notifications/index.md)指南的 "[本地通知](~/android/app-fundamentals/notifications/local-notifications.md)" 部分。

## <a name="unregistering-as-a-foreground-service"></a>作为前台服务注销

服务可以通过调用 `StopForeground` 的方法，将自身作为前台服务来取消列出。 `StopForeground` 将不会停止服务，但会删除通知图标并向 Android 发出信号，以便在必要时关闭此服务。

还可以通过将 `true` 传递给方法来删除显示的状态栏通知： 

```csharp
StopForeground(true);
```

如果服务在调用 `StopSelf` 或 `StopService` 时暂停，则将删除状态栏通知。

## <a name="related-links"></a>相关链接

- [Android. 服务](xref:Android.App.Service)
- [Android. StartForeground](xref:Android.App.Service.StartForeground*)
- [本地通知](~/android/app-fundamentals/notifications/local-notifications.md)
- [ForegroundServiceDemo （示例）](https://docs.microsoft.com/samples/xamarin/monodroid-samples/applicationfundamentals-servicesamples-foregroundservicedemo)
