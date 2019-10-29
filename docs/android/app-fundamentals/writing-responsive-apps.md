---
title: 编写响应式应用程序
ms.prod: xamarin
ms.assetid: 452DF940-6331-55F0-D130-002822BBED55
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/15/2018
ms.openlocfilehash: 1900a4fc42778db07e78c41bbc0acfafbd594cdf
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73024193"
---
# <a name="writing-responsive-applications"></a>编写响应式应用程序

维护响应式 GUI 的关键之一是在后台线程上执行长时间运行的任务，使 GUI 不会被阻止。 假设要计算一个值以显示给用户，但该值需要5秒来计算：

```csharp
public class ThreadDemo : Activity
{
    TextView textview;

    protected override void OnCreate (Bundle bundle)
    {
        base.OnCreate (bundle);

        // Create a new TextView and set it as our view
        textview = new TextView (this);
        textview.Text = "Working..";

        SetContentView (textview);

        SlowMethod ();
    }

    private void SlowMethod ()
    {
        Thread.Sleep (5000);
        textview.Text = "Method Complete";
    }
}
```

这将起作用，但计算该值时，应用程序将 "挂起" 5 秒。 在这段时间内，应用程序将不会响应任何用户交互。 为此，我们想要在后台线程上进行计算：

```csharp
public class ThreadDemo : Activity
{
    TextView textview;

    protected override void OnCreate (Bundle bundle)
    {
        base.OnCreate (bundle);

        // Create a new TextView and set it as our view
        textview = new TextView (this);
        textview.Text = "Working..";

        SetContentView (textview);

        ThreadPool.QueueUserWorkItem (o => SlowMethod ());
    }

    private void SlowMethod ()
    {
        Thread.Sleep (5000);
        textview.Text = "Method Complete";
    }
}
```

现在，我们在后台线程上计算该值，以便 GUI 在计算过程中保持响应状态。 但是，在完成计算后，应用会崩溃，并将其保留在日志中：

```shell
E/mono    (11207): EXCEPTION handling: Android.Util.AndroidRuntimeException: Exception of type 'Android.Util.AndroidRuntimeException' was thrown.
E/mono    (11207):
E/mono    (11207): Unhandled Exception: Android.Util.AndroidRuntimeException: Exception of type 'Android.Util.AndroidRuntimeException' was thrown.
E/mono    (11207):   at Android.Runtime.JNIEnv.CallVoidMethod (IntPtr jobject, IntPtr jmethod, Android.Runtime.JValue[] parms)
E/mono    (11207):   at Android.Widget.TextView.set_Text (IEnumerable`1 value)
E/mono    (11207):   at MonoDroidDebugging.Activity1.SlowMethod ()
```

这是因为必须从 GUI 线程更新 GUI。 我们的代码从 ThreadPool 线程更新 GUI，导致应用崩溃。 需要在后台线程上计算我们的值，然后在 GUI 线程上进行更新，该线程通过[RunOnUIThread](xref:Android.App.Activity.RunOnUiThread*)进行处理：

```csharp
public class ThreadDemo : Activity
{
    TextView textview;

    protected override void OnCreate (Bundle bundle)
    {
        base.OnCreate (bundle);

        // Create a new TextView and set it as our view
        textview = new TextView (this);
        textview.Text = "Working..";

        SetContentView (textview);

        ThreadPool.QueueUserWorkItem (o => SlowMethod ());
    }

    private void SlowMethod ()
    {
        Thread.Sleep (5000);
        RunOnUiThread (() => textview.Text = "Method Complete");
    }
}
```

此代码按预期方式工作。 此 GUI 保持响应状态，并在计算复杂性后正确更新。

请注意，此方法不仅仅用于计算开销较高的值。 它可用于任何可以在后台完成的长时间运行的任务，例如 web 服务调用或下载 internet 数据。
