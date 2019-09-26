---
title: 已通过 Xamarin 启动服务
ms.prod: xamarin
ms.assetid: 8CC3A850-4CD2-4F93-98EE-AF3470794000
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: 1b7bed0fc6dba1d9f80524ac3429b7fdcb751ab9
ms.sourcegitcommit: 699de58432b7da300ddc2c85842e5d9e129b0dc5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2019
ms.locfileid: "70755062"
---
# <a name="started-services-with-xamarinandroid"></a>已通过 Xamarin 启动服务

## <a name="started-services-overview"></a>已启动服务概述

已启动的服务通常会执行工作单元，而不会向客户端提供任何直接反馈或结果。 工作单元的一个示例是将文件上载到服务器的服务。 客户端将向服务发出请求，以将文件从设备上传到网站。 服务将不知不觉地上传文件（即使应用程序在前台没有活动），并在上传完成后终止自身。 请注意，已启动的服务将在应用程序的 UI 线程上运行。 这意味着，如果某个服务要执行将阻止 UI 线程的工作，则它必须根据需要创建和释放线程。

与绑定服务不同，"纯" 启动的服务及其客户端之间没有通信通道。 这意味着，已启动的服务将实现某些不同于绑定服务的生命周期方法。 以下列表突出显示了已启动服务中的常见生命周期方法：

- `OnCreate`&ndash;首次启动服务时调用一次。 这是应在其中实现初始化代码的位置。
- `OnBind`&ndash;此方法必须由所有服务类实现，但是，已启动的服务通常不会有绑定到它的客户端。 因此，已启动的服务只返回`null`。 相反，混合服务（即绑定服务与已启动服务的组合）必须实现并返回`Binder`客户端的。
- `OnStartCommand`为每个启动服务的请求调用，以响应对系统的`StartService`调用或重新启动系统。 &ndash; 这是服务可开始执行任何长时间运行的任务的位置。 方法返回一个`StartCommandResult`值，该值指示系统在由于内存不足而关闭后，如何或是否应处理重启服务。 此调用发生在主线程上。 下面更详细地介绍了此方法。
- `OnDestroy`&ndash;当服务被销毁时调用此方法。 它用于执行所需的任何最终清理。

启动服务的重要方法是`OnStartCommand`方法。 每次当服务收到执行一些工作的请求时，就会调用此方法。 以下代码片段是一个示例`OnStartCommand`： 

```csharp
public override StartCommandResult OnStartCommand (Android.Content.Intent intent, StartCommandFlags flags, int startId)
{
    // This method executes on the main thread of the application.
    Log.Debug ("DemoService", "DemoService started");
    ...
    return StartCommandResult.Sticky;
}
```

第一个参数是`Intent`包含要执行的工作的元数据的对象。 第二个参数包含`StartCommandFlags`一个值，该值提供有关方法调用的某些信息。 此参数具有以下两个可能的值之一：

- `StartCommandFlag.Redelivery`这意味着，是上`Intent`一个的重新传递。 `Intent` &ndash; 此值在服务返回`StartCommandResult.RedeliverIntent`后已停止，但在可以正确关闭之前已停止。
-`StartCommandFlag.Retry`此值在上一次`OnStartCommand`调用失败并且 Android 尝试再次使用与上一次失败尝试相同的目的尝试启动服务时接收。 &dash;

最后，第三个参数是一个整数值，它对于标识请求的应用程序是唯一的。 有可能多个调用方可以调用同一个服务对象。 此值用于关联请求以停止具有给定请求的服务来启动服务。 在[停止服务](#Stopping_the_Service)部分中将对此进行更详细的讨论。 

此值`StartCommandResult`由服务返回，作为对 Android 的建议（如果由于资源限制而终止了服务）。 有三个可能的`StartCommandResult`值：

- **[StartCommandResult. NotSticky](xref:Android.App.StartCommandResult.NotSticky)** &ndash;此值告知 Android 无需重启已终止的服务。 作为示例，请考虑在应用中为库生成缩略图的服务。 如果服务已终止，则在下次运行应用程序时，可以&ndash;重新创建缩略图，这并不是至关重要的。
- **[StartCommandResult.Sticky](xref:Android.App.StartCommandResult.Sticky)** &ndash; 这将指示 Android 重新启动服务, 但不会传递启动该服务的上一意向。 如果没有要处理的挂起意向，则`null`将为意向参数提供。 这种情况的一个示例可能是音乐播放器应用;该服务将重新启动以播放音乐，但会播放最后一首歌曲。
- **[StartCommandResult. RedeliverIntent](xref:Android.App.StartCommandResult.RedeliverIntent)** &ndash;此值将告知 Android 重新启动服务并重新传递最后一个`Intent`。 这是一个为应用程序下载数据文件的服务。 如果服务已终止，则仍需要下载数据文件。 返回`StartCommandResult.RedeliverIntent`后，当 Android 重新启动服务时，它还会将意图（包含要下载的文件的 URL）提供给该服务。 这将允许下载重新启动或恢复（具体取决于代码的确切实现）。

存在的第四个值`StartCommandResult`。 &ndash; `StartCommandResult.ContinuationMask` 此值由`OnStartCommand`返回，并说明 Android 将如何继续已终止的服务。 此值通常不用于启动服务。

此图显示了已启动服务的关键生命周期事件： 

![显示生命周期方法调用顺序的关系图](started-services-images/started-service-01.png "显示生命周期方法调用顺序的关系图。")

<a name="Stopping_the_Service" />

## <a name="stopping-the-service"></a>正在停止服务

已启动的服务将始终保持运行状态;只要有足够的系统资源，Android 就会保持服务运行。 客户端必须停止该服务，否则当服务完成其工作时，该服务可能会自行停止。 停止服务有两种方法： 

- **[Android. StopService （）](xref:Android.Content.Context.StopService*)** 客户端（如活动）可以通过`StopService`调用方法请求服务停止： &ndash;

    ```csharp
    StopService(new Intent(this, typeof(DemoService));
    ```

- **[Android. StopSelf （）](xref:Android.App.Service.StopSelf*)** 服务可以通过`StopSelf`调用以下内容自行关闭： &ndash;

    ```csharp
    StopSelf();
    ```

### <a name="using-startid-to-stop-a-service"></a>使用 startId 停止服务

多个调用方可以请求启动服务。 如果有未完成的启动请求，则该服务可以使用`startId` `OnStartCommand`传入的来阻止服务提前停止。 将对应于的最新`StartService`调用，并将在每次调用时递增。 `startId` `StartService`因此，如果对的后续请求尚未导致`OnStartCommand`调用，服务可以调用`StopSelfResult`，并向其传递接收到的最新值`startId` （而不是简单地调用`StopSelf`）。 如果`StartService`对的调用尚未导致相应的`OnStartCommand`调用，则系统将不会停止服务， `StopSelf`因为在调用中使用的`startId`与最新`StartService`调用不对应。

## <a name="related-links"></a>相关链接

- [StartedServicesDemo （示例）](https://docs.microsoft.com/samples/xamarin/monodroid-samples/applicationfundamentals-servicesamples-startedservicesdemo)
- [Android.App.Service](xref:Android.App.Service)
- [Android.App.StartCommandFlags](xref:Android.App.StartCommandFlags)
- [Android.App.StartCommandResult](xref:Android.App.StartCommandResult)
- [Android.Content.BroadcastReceiver](xref:Android.Content.BroadcastReceiver)
- [Android.Content.Intent](xref:Android.Content.Intent)
- [Android.OS.Handler](xref:Android.OS.Handler)
- [Android.Widget.Toast](xref:Android.Widget.Toast)
- [状态栏图标](https://developer.android.com/guide/practices/ui_guidelines/icon_design_status_bar.html)
