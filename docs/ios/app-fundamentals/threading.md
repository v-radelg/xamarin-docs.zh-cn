---
title: 在 Xamarin.iOS 中线程处理
description: 本文档介绍如何在 Xamarin.iOS 应用程序中使用 System.Threading Api。 它讨论了任务并行库，构建响应式应用程序和垃圾回收。
ms.prod: xamarin
ms.assetid: 50BCAF3B-1020-DDC1-0339-7028985AAC72
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 06/05/2017
ms.openlocfilehash: 7dbb0044f09d5bc00f2393eb647efba05a061c3f
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/08/2019
ms.locfileid: "57669539"
---
# <a name="threading-in-xamarinios"></a>在 Xamarin.iOS 中线程处理

Xamarin.iOS 运行时会使开发人员访问.NET 线程 Api，同时使用线程时显式 (`System.Threading.Thread, System.Threading.ThreadPool`) 和隐式时使用异步委托模式或 BeginXXX 方法，以及完整范围的支持的 Api任务并行库。



Xamarin 强烈建议你使用[任务并行库](https://msdn.microsoft.com/library/dd460717.aspx)(TPL) 用于构建应用程序的几个原因：
-  默认 TPL 计划程序会将委托将执行任务的线程池，反过来会动态增长的需要因为发生过程，同时避免了过多的线程的结束位置会争用 CPU 时间的方案的线程数。 
-  它是更轻松地考虑在 TPL 任务方面的操作。 轻松可以对其进行处理，将它们计划、 序列化其执行或启动很多与一组丰富的 Api 并行。 
-  它是使用新 C# async 语言扩展进行编程的基础。 


线程池将缓慢增长根据可用系统、 系统负载和应用程序需求的 CPU 内核数为所需的线程数。 可以通过调用方法中的使用此线程池`System.Threading.ThreadPool`或使用默认值`System.Threading.Tasks.TaskScheduler`(属于*并行框架*)。

通常开发人员使用的线程时他们需要创建响应式应用程序，并且他们不想要阻止运行循环的主要用户界面。

 <a name="Developing_Responsive_Applications" />


## <a name="developing-responsive-applications"></a>开发响应式应用程序

对 UI 元素的访问应限制为正在运行你的应用程序的主循环的同一个线程。 如果你想要从一个线程对主用户界面进行更改，应通过使用队列的代码[NSObject.InvokeOnMainThread](xref:Foundation.NSObject)，如下所示：

```csharp
MyThreadedRoutine ()  
{  
    var result = DoComputation ();  

    // we want to update an object that is managed by the main
    // thread; To do so, we need to ensure that we only access
    // this from the main thread:

    InvokeOnMainThread (delegate {  
        label.Text = "The result is: " + result;  
    });
}
```

上述调用而不会导致任何可能会导致你的应用程序可能崩溃的争用条件的代码将在主线程的上下文中的委托。

 <a name="Threading_and_Garbage_Collection" />


## <a name="threading-and-garbage-collection"></a>线程处理和垃圾回收

在过程中执行，OBJECTIVE-C 运行时将创建并释放对象。 如果对象被标记为"自动释放"OBJECTIVE-C 运行时将发布到这些对象在线程的当前`NSAutoReleasePool`。 Xamarin.iOS 将创建一个`NSAutoRelease`为从每个线程池`System.Threading.ThreadPool`和主线程。 这通过扩展包括在 System.Threading.Tasks 中使用默认的 TaskScheduler 创建任何线程。

如果您创建您自己使用的线程`System.Threading`需要提供你拥有`NSAutoRelease`池，以防止数据泄漏。 若要执行此操作，只是包装你在以下代码段中的线程：

```csharp
void MyThreadStart (object arg)
{
   using (var ns = new NSAutoReleasePool ()){
      // Your code goes here.
   }
}
```

注意:自 Xamarin.iOS 5.2 您无需提供自己`NSAutoReleasePool`不再将为您自动提供一个。


## <a name="related-links"></a>相关链接

- [使用 UI 线程](~/ios/user-interface/ios-ui/ui-thread.md)
