---
title: 前景服务
ms.prod: xamarin
ms.assetid: C10FD999-7A91-4708-B642-0C1B0901BD24
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/19/2018
ms.openlocfilehash: b1cc043ac94fb48e3e7fe6b7ba647cfb19a4c7eb
ms.sourcegitcommit: 450106d5f05b4473bf7f5b9100b2eaf18c9110de
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2019
ms.locfileid: "67522933"
---
# <a name="foreground-services"></a>前景服务

前景服务是一种特殊类型的绑定的服务或已启动的服务。 有时服务将执行任务，用户必须是主动了解，这些服务称为_前景服务_。 前景服务的一个示例是一个应用，会向用户提供说明驾车或步行时。 即使应用是在后台，仍然很重要的服务具有足够的资源才能正常工作，并且用户具有的快速而方便的方法来访问应用。 对于 Android 应用程序，这意味着前景服务应该接收优先级高于"regular"服务和前景服务必须提供`Notification`Android 将显示，只要服务正在运行。
 
若要开始的前景色服务，该应用程序必须调度告知 Android 在启动该服务将意向。 然后该服务必须注册为与 Android 前景服务本身。 Android 8.0 （或更高版本） 上运行的应用程序应使用`Context.StartForegroundService`方法以启动服务，而应使用具有较旧版本的 Android 设备运行的应用程序 `Context.StartService`

此 C# 扩展方法是举例说明如何启动前景服务。 在 Android 8.0 及更高版本会将`StartForegroundService`方法，否则为较旧`StartService`将使用方法。  

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

## <a name="registering-as-a-foreground-service"></a>作为前景色服务进行注册

前景服务启动后，它必须注册其自身与 Android 一起通过调用[ `StartForeground` ](https://developer.xamarin.com/api/member/Android.App.Service.StartForeground/p/System.Int32/Android.App.Notification/)。 如果该服务已启动与`Service.StartForegroundService`方法，但不注册自身，则 Android 将停止该服务并将标记为不响应的应用。

`StartForeground` 使用这两者都是必需的两个参数：
 
* 要标识该服务的应用程序中是唯一的一个整数值。
* 一个`Notification`Android 将显示为状态栏中，只要服务正在运行的对象。

Android 将为状态栏中显示通知，只要服务正在运行。 通知最小值，将向该服务正在运行的用户提供视觉提示。 理想情况下，通知应为用户提供应用程序或可能是某些操作按钮来控制应用程序的快捷方式。 此示例是音乐播放器&ndash;显示通知可能会有所按钮来暂停/播放音乐，可使退回到上一首歌曲，或可以跳到下一首歌曲。 

此代码片段是作为前台服务注册服务的示例：   

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

前一次通知将显示类似于下面的状态栏通知：

![在状态栏中显示通知的图像](foreground-services-images/foreground-services-01.png "通知显示在状态栏中的图像")

此屏幕截图显示包含允许用户控制的服务的两个操作的通知送纸器中展开的通知：

![显示扩展的通知图像](foreground-services-images/foreground-services-02.png "显示扩展的通知的图像。")

有关通知的详细信息现已推出[本地通知](~/android/app-fundamentals/notifications/local-notifications.md)一部分[Android 通知](~/android/app-fundamentals/notifications/index.md)指南。

## <a name="unregistering-as-a-foreground-service"></a>作为前景色服务正在注销

服务可以取消列出本身作为前台服务通过调用方法`StopForeground`。 `StopForeground` 不会停止该服务，但它将删除的通知图标和信号可以向下关闭此服务，如有必要的 Android。

此外可以通过传递中删除状态栏通知显示`true`方法： 

```csharp
StopForeground(true);
```

如果该服务将停止通过调用`StopSelf`或`StopService`，将删除状态栏通知。

## <a name="related-links"></a>相关链接

- [Android.App.Service](https://developer.xamarin.com/api/type/Android.App.Service/)
- [Android.App.Service.StartForeground](https://developer.xamarin.com/api/member/Android.App.Service.StartForeground/p/System.Int32/Android.App.Notification/)
- [本地通知](~/android/app-fundamentals/notifications/local-notifications.md)
- [ForegroundServiceDemo （示例）](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/ServiceSamples/ForegroundServiceDemo/)
