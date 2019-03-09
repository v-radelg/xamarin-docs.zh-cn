---
title: 使用 Xamarin.Android 启动的服务
ms.prod: xamarin
ms.assetid: 8CC3A850-4CD2-4F93-98EE-AF3470794000
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: df3d1bba57c1caf23c615410a184bc5458fc848b
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/08/2019
ms.locfileid: "57671346"
---
# <a name="started-services-with-xamarinandroid"></a>使用 Xamarin.Android 启动的服务

## <a name="started-services-overview"></a>启动的服务概述

启动的服务通常执行工作的单元，而无需向客户端提供的任何直接反馈或结果。 一个工作单元的示例是一种服务，将文件上载到服务器。 客户端将请求服务上传从设备到网站文件。 该服务将安静地上载文件 （即使应用程序在前台中有任何活动），并上传完成时终止自身。 请务必意识到已启动的服务将在应用程序的 UI 线程上运行。 这意味着如果一项服务将执行会阻塞 UI 线程的工作，它必须创建并根据需要释放的线程。

不同于绑定的服务，没有"纯"已启动的服务及其客户端之间的通信渠道。 这意味着已启动的服务将执行绑定的服务比一些不同的生命周期方法。 下表列出了已启动的服务中常见的生命周期方法：

* `OnCreate` &ndash; 调用一次当首次启动该服务。 这是应实现初始化代码的位置。
* `OnBind` &ndash; 必须由所有服务类，实现此方法，但已启动的服务通常无需绑定到它的客户端。 因此，已启动的服务只返回`null`。 一种混合服务 （这是绑定的服务和已启动的服务的组合） 与此相反，必须实现并返回`Binder`客户端。
* `OnStartCommand` &ndash; 为每个请求以启动服务，以响应对的调用的任何一个调用`StartService`或由系统重新启动。 这是该服务可以开始任何长时间运行的任务的位置。 该方法将返回`StartCommandResult`值，该值指示如何或如果系统应处理由于内存不足导致关闭后重新启动服务。 此调用会在主线程上的发生。 下面更详细地介绍了这些方法。
* `OnDestroy` &ndash; 当正在销毁该服务时，调用此方法。 它用于执行任何最终清理所需。

已启动的服务的重要方法是`OnStartCommand`方法。 它将调用每个时间服务收到的请求执行一些操作。 以下代码片段示范了`OnStartCommand`: 

```csharp
public override StartCommandResult OnStartCommand (Android.Content.Intent intent, StartCommandFlags flags, int startId)
{
    // This method executes on the main thread of the application.
    Log.Debug ("DemoService", "DemoService started");
    ...
    return StartCommandResult.Sticky;
}
```

第一个参数是`Intent`对象，其中包含有关要执行的元数据。 第二个参数包含`StartCommandFlags`提供了有关方法调用的一些信息的值。 此参数具有两个可能值之一：

* `StartCommandFlag.Redelivery` &ndash; 这意味着`Intent`是在前一次重新交付`Intent`。 此值时返回的服务提供`StartCommandResult.RedeliverIntent`但已停止，但它无法正确关闭。
* `StartCommandFlag.Retry` &dash; 在上一次时收到此值，则`OnStartCommand`调用失败，Android 尝试使用相同目的为以前的失败尝试再次启动该服务。
 
最后，第三个参数是一个整数值，是唯一的标识请求的应用程序。 有可能多个调用方可能会调用同一服务对象。 此值用于将请求以停止与给定的请求来启动服务的服务相关联。 它将在部分更详细地讨论[停止服务](#Stopping_the_Service)。 

值`StartCommandResult`服务返回作为建议向 Android 上要执行的操作如果由于资源约束而终止服务。 有三个可能值为`StartCommandResult`:

* **[StartCommandResult.NotSticky](https://developer.xamarin.com/api/field/Android.App.StartCommandResult.NotSticky/)**  &ndash;此值会指出不需要重新启动已终止该服务的 Android。 作为此示例，请考虑在应用中生成库的缩略图的服务。 如果终止该服务，它不是重要立即重新创建缩略图&ndash;缩略图可以重新创建在下次运行应用程序。
* **[StartCommandResult.Sticky](https://developer.xamarin.com/api/field/Android.App.StartCommandResult.Sticky/)**  &ndash;这告知 Android 在重新启动服务，但不是能提供最初启动该服务的最后一个目的。 如果不有任何挂起的意向，若要处理，则`null`将意向参数提供。 出现这种可能是音乐播放器应用程序;该服务将重启准备好播放音乐，但它会播放最后一首歌曲。 
* **[StartCommandResult.RedeliverIntent](https://developer.xamarin.com/api/field/Android.App.StartCommandResult.RedeliverIntent/)**  &ndash;此值是将告知 Android，重新启动服务并重新提供上次`Intent`。 此示例是下载数据文件的应用服务。 如果终止该服务，数据文件仍需要下载。 通过返回`StartCommandResult.RedeliverIntent`，当 Android 到服务，重新启动的服务还会提供意图 （其中包含要下载的文件的 URL）。 这将允许您下载后，重新启动或恢复 （具体取决于代码的确切实现）。

还有第四个值`StartCommandResult` &ndash; `StartCommandResult.ContinuationMask`。 返回此值`OnStartCommand`并介绍了如何 Android 将继续该服务已终止。 通常，此值不用于启动服务。

已启动的服务的密钥生命周期事件显示在此图中： 

![图示的生命周期方法的调用的顺序](started-services-images/started-service-01.png "图示的生命周期方法的调用的顺序。")


<a name="Stopping_the_Service" />

## <a name="stopping-the-service"></a>停止服务

已启动的服务将继续无限期; 运行只要有足够的系统资源，android 会使服务正在运行。 客户端必须停止该服务，或者在完成其工作时，服务可能停止本身。 有两种方法来停止服务： 
 
* **[Android.Content.Context.StopService()](https://developer.xamarin.com/api/member/Android.Content.Context.StopService/p/Android.Content.Intent/)**  &ndash;客户端 （例如活动） 可以请求通过停止服务调用`StopService`方法： 

    ```csharp
    StopService(new Intent(this, typeof(DemoService));
    ```

* **[Android.App.Service.StopSelf()](https://developer.xamarin.com/api/member/Android.App.Service.StopSelf()/)**  &ndash;服务可能会自行关闭前通过调用`StopSelf`:

    ```csharp
    StopSelf();
    ```
    
### <a name="using-startid-to-stop-a-service"></a>使用 startId 来停止服务

多个调用方可以请求启动服务。 如果没有未完成的启动请求，服务可以使用`startId`传递到`OnStartCommand`将阻止服务被过早停止。 `startId`将对应于最新调用`StartService`，并将递增每次调用它。 因此，如果对后续请求`StartService`尚不导致调用`OnStartCommand`，该服务可以调用`StopSelfResult`，并向其传递的最新值`startId`已收到 (而不是只需调用`StopSelf`)。 如果调用`StartService`尚不导致相应地调用`OnStartCommand`，系统不会停止该服务，因为`startId`中使用`StopSelf`调用将不对应于最新`StartService`调用。


## <a name="related-links"></a>相关链接

- [StartedServicesDemo (sample)](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/ServiceSamples/StartedServicesDemo/)
- [Android.App.Service](https://developer.xamarin.com/api/type/Android.App.Service)
- [Android.App.StartCommandFlags](https://developer.xamarin.com/api/type/Android.App.StartCommandFlags)
- [Android.App.StartCommandResult](https://developer.xamarin.com/api/type/Android.App.StartCommandResult)
- [Android.Content.BroadcastReceiver](https://developer.xamarin.com/api/type/Android.Content.BroadcastReceiver/)
- [Android.Content.Intent](https://developer.xamarin.com/api/type/Android.Content.Intent)
- [Android.OS.Handler](https://developer.xamarin.com/api/type/Android.OS.Handler/)
- [Android.Widget.Toast](https://developer.xamarin.com/api/type/Android.Widget.Toast/)
- [状态栏图标](https://developer.android.com/guide/practices/ui_guidelines/icon_design_status_bar.html)
