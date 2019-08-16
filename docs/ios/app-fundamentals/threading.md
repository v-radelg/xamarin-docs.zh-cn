---
title: Xamarin 中的线程
description: 本文档介绍如何在 Xamarin iOS 应用程序中使用系统线程 Api。 它讨论任务并行库、构建响应式应用程序和垃圾回收。
ms.prod: xamarin
ms.assetid: 50BCAF3B-1020-DDC1-0339-7028985AAC72
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 06/05/2017
ms.openlocfilehash: cbf0aace8806b3b253b523d9ed2001b466ae5237
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/16/2019
ms.locfileid: "69526940"
---
# <a name="threading-in-xamarinios"></a>Xamarin 中的线程

在使用异步委托模式或 BeginXXX 方法时, Xamarin iOS 运行时为开发人员提供了对`System.Threading.Thread, System.Threading.ThreadPool`.net 线程 api 的访问, 并在使用异步委托模式或方法以及支持任务并行库。



Xamarin 强烈建议使用[任务并行库](https://msdn.microsoft.com/library/dd460717.aspx)(TPL) 来生成应用程序, 原因如下:
- 默认 TPL 计划程序会将任务执行委托给线程池, 这反过来会动态增加所需的线程数, 同时避免太多线程最终争用 CPU 时间的情况。 
- 可以更方便地考虑 TPL 任务的操作。 使用一组丰富的 Api, 可以轻松地对其进行处理、计划、序列化其执行或启动多项操作。 
- 它是用新C#的异步语言扩展进行编程的基础。 


根据系统上可用的 CPU 内核数、系统负载和应用程序的要求, 线程池会根据需要慢慢地增长线程数。 您可以使用此线程池, 方法是: 在`System.Threading.ThreadPool`中调用方法, 或`System.Threading.Tasks.TaskScheduler`使用默认值 (*并行框架*的一部分)。

当开发人员需要创建响应式应用程序, 并且不希望阻止主 UI 运行循环时, 开发人员通常会使用线程。

 <a name="Developing_Responsive_Applications" />


## <a name="developing-responsive-applications"></a>开发响应式应用程序

对 UI 元素的访问应限制为运行应用程序主循环的同一线程。 如果要从线程更改主 UI, 则应使用[NSObject](xref:Foundation.NSObject)对代码进行排队, 如下所示:

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

上述代码在主线程的上下文中调用委托内的代码, 而不会导致可能导致应用程序崩溃的任何争用情况。

 <a name="Threading_and_Garbage_Collection" />


## <a name="threading-and-garbage-collection"></a>线程和垃圾回收

在执行过程中, 目标 C 运行时将创建和释放对象。 如果将对象标记为 "自动释放", 则目标 C 运行时将释放这些对象到线程的当前`NSAutoReleasePool`。 Xamarin 为中的`NSAutoRelease` `System.Threading.ThreadPool`每个线程创建一个池。 此扩展涵盖了在 TaskScheduler 中使用默认的创建的所有线程。

如果使用`System.Threading`创建自己的线程, 则必须提供自己`NSAutoRelease`的池, 以防止数据泄漏。 为此, 只需将线程包装在以下代码段中:

```csharp
void MyThreadStart (object arg)
{
   using (var ns = new NSAutoReleasePool ()){
      // Your code goes here.
   }
}
```

注意:自 Xamarin 5.2 起, 你不必自行`NSAutoReleasePool`提供, 因为系统会自动为你提供。


## <a name="related-links"></a>相关链接

- [使用 UI 线程](~/ios/user-interface/ios-ui/ui-thread.md)
