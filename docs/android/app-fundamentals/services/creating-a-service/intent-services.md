---
title: 在 Xamarin.Android 中的意向服务
ms.prod: xamarin
ms.assetid: A5B86FE4-C8E2-4B0A-84CA-EF8F5119E31B
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: 1301f34ad1f7a0069c542ba81bf237a673fd239d
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61013107"
---
# <a name="intent-services-in-xamarinandroid"></a>在 Xamarin.Android 中的意向服务

## <a name="intent-services-overview"></a>意向服务概述

两者都已启动并绑定意味着，若要使性能保持平滑，服务需要异步执行工作的主线程上运行的服务。 若要解决此问题的最简单方式之一是使用_辅助队列处理器模式_、 完成的工作由单个线程提供服务的队列中的放置位置。 

[ `IntentService` ](https://developer.xamarin.com/api/type/Android.App.IntentService/)是一个的子类`Service`提供这种模式的特定 Android 实现的类。 将要管理的工作排入队列，正在启动工作线程来服务队列，并提取请求关闭要在辅助线程上运行的队列。 `IntentService`会默默地自行停止并删除工作线程队列中的没有更多工作时。
 
通过创建将工作提交到队列`Intent`，然后将其传递`Intent`到`StartService`方法。

不能停止或中断`OnHandleIntent`方法`IntentService`而正在处理。 此设计中，由于`IntentService`应保持无状态&ndash;它不应依赖于活动的连接或应用程序的其余部分通信。 `IntentService`旨在 statelessly 处理工作请求。

有两个要求子类化`IntentService`:

1. 新类型 (通过子类化创建`IntentService`) 仅重写`OnHandleIntent`方法。
2. 新类型的构造函数需要一个字符串，它用于命名将处理请求的工作线程。 调试应用程序时，主要使用此工作线程的名称。

下面的代码演示`IntentService`实现，使用重写`OnHandleIntent`方法：

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

将任务发送至`IntentService`通过实例化`Intent`，然后再调用[ `StartService` ](https://developer.xamarin.com/api/member/Android.Content.Context.StartService/p/Android.Content.Intent/)作为参数的意向的方法。 其目的将作为参数传递给服务`OnHandleIntent`方法。 此代码片段是发送到意向的工作请求的示例： 

```csharp
// This code might be called from within an Activity, for example in an event
// handler for a button click.
Intent downloadIntent = new Intent(this, typeof(DemoIntentService));

// This is just one example of passing some values to an IntentService via the Intent:
downloadIntent.Put
("file_to_download", "http://www.somewhere.com/file/to/download.zip");

StartService(downloadIntent);
```

`IntentService`可以提取值从意图，此代码片段中所示：  

```csharp
protected override void OnHandleIntent (Android.Content.Intent intent)
{
    string fileToDownload = intent.GetStringExtra("file_to_download");
    
    Log.Debug("DemoIntentService", $"File to download: {fileToDownload}.");
}
```


## <a name="related-links"></a>相关链接

- [IntentService](https://developer.xamarin.com/api/type/Android.App.IntentService/)
- [StartService](https://developer.xamarin.com/api/member/Android.Content.Context.StartService/p/Android.Content.Intent/)
