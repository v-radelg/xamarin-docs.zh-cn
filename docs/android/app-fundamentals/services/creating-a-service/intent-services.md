---
title: Xamarin 中的意向服务
ms.prod: xamarin
ms.assetid: A5B86FE4-C8E2-4B0A-84CA-EF8F5119E31B
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: 4c868623ae08ac1366c1c9ea55c8d635f0a6a061
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2019
ms.locfileid: "68509140"
---
# <a name="intent-services-in-xamarinandroid"></a>Xamarin 中的意向服务

## <a name="intent-services-overview"></a>意向服务概述

已启动和绑定的服务都在主线程上运行, 这意味着, 若要使性能保持平稳, 服务需要以异步方式执行工作。 解决此问题的最简单方法之一是使用_辅助队列处理器模式_, 在该模式下, 要完成的工作放置在由单个线程维护的队列中。

[`IntentService`](xref:Android.App.IntentService)是`Service`类的子类, 它提供此模式的特定于 Android 的实现。 它将管理队列工作, 启动工作线程以服务队列, 并将请求从队列中拉取到在工作线程上运行。 当`IntentService`队列中没有更多的工作时, 将以静默方式自行停止并删除工作线程。

通过创建`Intent`并将`Intent`其传递给`StartService`方法, 将工作提交到队列。

如果`OnHandleIntent`方法正在运行, 则无法停止或中断`IntentService`该方法。 由于此设计, `IntentService`应保持无状态&ndash; , 它不应依赖于活动连接或与应用程序的其余部分通信。 `IntentService`旨在 statelessly 处理工作请求。

为子类`IntentService`提供两个要求:

1. 新类型 (由子类`IntentService`创建) 仅`OnHandleIntent`重写方法。
2. 新类型的构造函数需要一个字符串, 该字符串用于命名要处理请求的工作线程。 此工作线程的名称主要在调试应用程序时使用。

下面的代码演示`IntentService`具有重写`OnHandleIntent`的方法的实现:

```csharp
[Service]
public class DemoIntentService: IntentService
{
    public DemoIntentService () : base("DemoIntentService")
    {
    }

    protected override void OnHandleIntent (Android.Content.Intent intent)
    {
        Console.WriteLine ("perform some long running work");
        ...
        Console.WriteLine ("work complete");
    }
}
```

`IntentService`通过[`StartService`](xref:Android.Content.Context.StartService*)实例化将工作发送到, 然后使用该意向作为参数调用方法。 `Intent` 在`OnHandleIntent`方法中, 将此目的作为参数传递给服务。 此代码片段是向意向发送工作请求的示例: 

```csharp
// This code might be called from within an Activity, for example in an event
// handler for a button click.
Intent downloadIntent = new Intent(this, typeof(DemoIntentService));

// This is just one example of passing some values to an IntentService via the Intent:
downloadIntent.Put
("file_to_download", "http://www.somewhere.com/file/to/download.zip");

StartService(downloadIntent);
```

`IntentService`可以从源中提取值, 如以下代码片段所示:  

```csharp
protected override void OnHandleIntent (Android.Content.Intent intent)
{
    string fileToDownload = intent.GetStringExtra("file_to_download");

    Log.Debug("DemoIntentService", $"File to download: {fileToDownload}.");
}
```

## <a name="related-links"></a>相关链接

- [IntentService](xref:Android.App.IntentService)
- [StartService](xref:Android.Content.Context.StartService*)
