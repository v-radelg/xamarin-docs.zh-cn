---
title: 使用 Xamarin.iOS 中的 UI 线程
description: 本文档介绍如何使用 Xamarin.iOS 中对 UI 线程。 它讨论了 UI 线程执行，提供后台线程示例中，并检查 async/await。
ms.prod: xamarin
ms.assetid: 98762ACA-AD5A-4E1E-A536-7AF3BE36D77E
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: 6dd55f5c4316ed8f1d4f16d9e282cc2647350518
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61245318"
---
# <a name="working-with-the-ui-thread-in-xamarinios"></a>使用 Xamarin.iOS 中的 UI 线程

应用程序用户界面始终是单线程，即使在多线程的设备 – 没有仅一种表示形式的屏幕，显示的内容的任何更改需要协调通过单个访问点。 这可以防止多个线程尝试 （例如） 在同一时间更新同一像素。

你的代码仅需更改用户界面控件的主 （或 UI） 线程。 （例如回调或后台线程中） 在不同线程发生的任何 UI 更新不获取呈现到屏幕，或甚至可能导致崩溃。

## <a name="ui-thread-execution"></a>UI 线程执行

要在视图中，创建控件或处理用户启动的事件，如触摸屏输入时，会在 UI 线程的上下文中已执行代码。

如果代码中的任务或回调的后台线程上执行它很可能不在主 UI 线程上执行。 在这种情况下应将代码包装在调用`InvokeOnMainThread`或`BeginInvokeOnMainThread`如下所示：

```csharp
InvokeOnMainThread ( () => {
    // manipulate UI controls
});
```

`InvokeOnMainThread`方法定义上`NSObject`以便它可以从内部调用任何 UIKit 对象 （如视图或视图控制器） 上定义的方法。

在调试 Xamarin.iOS 应用程序时，如果你的代码尝试从错误的线程访问 UI 控件，将引发错误。 这可帮助你跟踪并修复这些问题的 InvokeOnMainThread 方法。 这仅在调试时发生，且不会发布版本中引发错误。 此类情况下，将显示错误消息：

 ![](ui-thread-images/image10.png "UI 线程执行")

 <a name="Background_Thread_Example" />


## <a name="background-thread-example"></a>后台线程示例

下面是尝试访问用户界面控件示例 ( `UILabel`) 从后台线程使用简单的线程：

```csharp
new System.Threading.Thread(new System.Threading.ThreadStart(() => {
    label1.Text = "updated in thread"; // should NOT reference UILabel on background thread!
})).Start();
```

代码将引发`UIKitThreadAccessException`调试时。 若要解决此问题 （并确保只能从主 UI 线程访问用户界面控件），包装引用 UI 控件内的任何代码`InvokeOnMainThread`如下表达式：

```csharp
new System.Threading.Thread(new System.Threading.ThreadStart(() => {
    InvokeOnMainThread (() => {
        label1.Text = "updated in thread"; // this works!
    });
})).Start();
```

您不需要的使用这在本文档中，但示例的其余部分是在您的应用程序发出网络请求时要记住的重要概念使用通知中心或需要完成的处理程序将在另一台运行的其他方法线程。

 <a name="Async_Await_Example" />


## <a name="asyncawait-example"></a>Async/Await 示例

当使用C#5 async/await 关键字`InvokeOnMainThread`因为不需要等待的任务完成时此方法在调用线程上继续。

（它等待延迟方法调用，用于演示目的） 此示例代码演示 （它是 TouchUpInside 处理程序） 在 UI 线程调用的异步方法。 由于包含的方法在 UI 线程上调用的因此 UI 之类的操作上设置文本`UILabel`或显示`UIAlertView`后，可以安全地调用异步操作已完成的后台线程。

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

如果从后台线程 （非主 UI 线程） 调用异步方法然后`InvokeOnMainThread`也仍然需要。


## <a name="related-links"></a>相关链接

- [控件 （示例）](https://developer.xamarin.com/samples/Controls/)
- [线程处理](~/ios/app-fundamentals/threading.md)
