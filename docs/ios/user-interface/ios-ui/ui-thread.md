---
title: 使用 Xamarin 中的 UI 线程
description: 本文档介绍如何在 Xamarin 中使用 UI 线程。 它讨论 UI 线程执行，提供后台线程示例并检查 async/await。
ms.prod: xamarin
ms.assetid: 98762ACA-AD5A-4E1E-A536-7AF3BE36D77E
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/21/2017
ms.openlocfilehash: ab72034d7b565a31c59d997f03844b6c8c959785
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70768182"
---
# <a name="working-with-the-ui-thread-in-xamarinios"></a>使用 Xamarin 中的 UI 线程

应用程序用户界面始终是单线程的，即使在多线程设备中–只有屏幕的一种表示形式，并且对所显示内容所做的任何更改都需要通过单个 "访问点" 进行协调。 这可以防止多个线程尝试同时更新同一像素（例如）。

你的代码只应从主（或 UI）线程对用户界面控件进行更改。 在不同线程（如回调或后台线程）上发生的任何 UI 更新可能无法呈现到屏幕上，甚至可能会导致崩溃。

## <a name="ui-thread-execution"></a>UI 线程执行

在视图中创建控件或处理用户启动的事件（如触控）时，代码已在 UI 线程的上下文中执行。

如果代码是在后台线程上执行，则在任务或回调中，它很可能不会在主 UI 线程上执行。 在这种情况下，应将代码包装在对`InvokeOnMainThread` `BeginInvokeOnMainThread`的调用中，如下所示：

```csharp
InvokeOnMainThread ( () => {
    // manipulate UI controls
});
```

方法`InvokeOnMainThread`是在上`NSObject`定义的，因此可以从在任何 UIKit 对象（如视图或视图控制器）上定义的方法中调用它。

调试 Xamarin iOS 应用程序时，如果您的代码尝试从错误的线程访问 UI 控件，将引发错误。 这可以帮助您跟踪并修复 InvokeOnMainThread 方法的这些问题。 这仅在调试时出现，在发布版本中不会引发错误。 错误消息会如下所示：

 ![](ui-thread-images/image10.png "UI 线程执行")

 <a name="Background_Thread_Example" />

## <a name="background-thread-example"></a>后台线程示例

下面的示例尝试使用简单线程从后台线程访问用户界面控件`UILabel`（a）：

```csharp
new System.Threading.Thread(new System.Threading.ThreadStart(() => {
    label1.Text = "updated in thread"; // should NOT reference UILabel on background thread!
})).Start();
```

调试`UIKitThreadAccessException`时，该代码将引发。 若要解决此问题（并确保仅从主 UI 线程访问用户界面控件），请将引用 UI 控件的任何代码包装在`InvokeOnMainThread`表达式内，如下所示：

```csharp
new System.Threading.Thread(new System.Threading.ThreadStart(() => {
    InvokeOnMainThread (() => {
        label1.Text = "updated in thread"; // this works!
    });
})).Start();
```

对于本文档中的其余示例，你不需要使用此方法，但这是一个重要的概念，可以记住应用何时发出网络请求，使用通知中心或其他需要完成处理程序的方法（将在另一个上运行）thread.

 <a name="Async_Await_Example" />

## <a name="asyncawait-example"></a>Async/Await 示例

使用C# 5 个 async/await 关键字`InvokeOnMainThread`不是必需的，因为等待的任务完成后，该方法将在调用线程上继续。

此示例代码（在延迟方法调用中，纯粹用于演示目的）显示了在 UI 线程上调用的异步方法（它是 TouchUpInside 处理程序）。 由于在 ui 线程上调用了包含方法，因此在后台线程上完成异步操作后`UILabel` ，可以安全`UIAlertView`地调用在上设置或显示的 UI 操作。

```csharp
async partial void button2_TouchUpInside (UIButton sender)
{
    textfield1.ResignFirstResponder ();
    textfield2.ResignFirstResponder ();
    textview1.ResignFirstResponder ();
    label1.Text = "async method started";
    await Task.Delay(1000); // example purpose only
    label1.Text = "1 second passed";
    await Task.Delay(2000);
    label1.Text = "2 more seconds passed";
    await Task.Delay(1000);
    new UIAlertView("Async method complete", "This method", 
               null, "Cancel", null)
        .Show();
    label1.Text = "async method completed";
}
```

如果异步方法是从后台线程（而不是主 UI 线程）调用的， `InvokeOnMainThread`则仍需要。

## <a name="related-links"></a>相关链接

- [控件（示例）](https://docs.microsoft.com/samples/xamarin/ios-samples/controls)
- [线程处理](~/ios/app-fundamentals/threading.md)
