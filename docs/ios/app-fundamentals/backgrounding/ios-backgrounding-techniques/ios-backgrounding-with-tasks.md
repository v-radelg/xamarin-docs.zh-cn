---
title: iOS 后台处理及任务
description: 本文档介绍如何使用后台任务后应用程序放置在后台执行长时间运行的任务。
ms.prod: xamarin
ms.assetid: 205D230E-C618-4D69-96EE-4B91D7819121
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: c8d1abebf6dec2b7b5fe76d57ff851fad457f2a8
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/08/2019
ms.locfileid: "57669825"
---
# <a name="ios-backgrounding-with-tasks"></a>iOS 后台处理及任务

若要执行后台处理在 iOS 上的最简单方法是将 backgrounding 要求拆分为任务，并在后台运行的任务。 任务是一个严格的时间限制下，通常在 iOS 7 + 上获取的应用程序已移至后在后台 6，在 iOS 上的处理时间大约 600 秒 （10 分钟） 和不超过 10 分钟。

后台任务可以分解为三个类别：

1.  **后台安全任务**-被调用任意位置有任务在应用程序中不希望中断应用程序应输入背景。
1.  **DidEnterBackground 任务**-在期间调用`DidEnterBackground`应用程序生命周期方法，以帮助清理和状态保存。
1.  **后台传输 (iOS 7 +)** -一种特殊的后台任务用于执行 iOS 7 上的网络传输。 与常规任务，后台传输没有预先确定的时间限制。


后台安全和`DidEnterBackground`任务是安全地使用 iOS 6 和 iOS 7，有一些细微差别。 让我们来研究这两种类型的更详细地介绍中的任务。

## <a name="creating-background-safe-tasks"></a>创建后台安全任务

某些应用程序包含不应在应用程序应更改状态的 iOS 中断的任务。 若要防止这些任务被中断的一种方法是将它们注册为长时间运行的任务的 iOS。 在应用程序中任何不希望被中断的任务应用户 put 应用到背景的任意位置可以使用此模式。 此模式的理想之选是任务，如将新用户的注册信息发送到你的服务器，或验证的登录信息。

下面的代码段说明如何注册要在后台运行的任务：

```csharp
nint taskID = UIApplication.SharedApplication.BeginBackgroundTask( () => {});

//runs on main or background thread
FinishLongRunningTask(taskID);

UIApplication.SharedApplication.EndBackgroundTask(taskID);
```

注册过程对任务的唯一标识符，与`taskID`，然后将其包装在匹配中和`BeginBackgroundTask`和`EndBackgroundTask`调用。 若要生成标识符，我们请调用`BeginBackgroundTask`方法`UIApplication`对象，然后上并启动长时间运行的任务，通常新线程。 任务完成后，我们调用`EndBackgroundTask`并传入相同的标识符。 这很重要，因为 iOS 将终止该应用程序，如果`BeginBackgroundTask`调用不具有匹配`EndBackgroundTask`。

> [!IMPORTANT]
> 后台安全任务可以运行在主线程或后台线程，具体取决于应用程序的需求。


## <a name="performing-tasks-during-didenterbackground"></a>在 DidEnterBackground 期间执行的任务

除了后台安全进行长时间运行任务，可以用于启动任务，如应用程序被放入后台注册。 iOS 提供中的事件方法*AppDelegate*类调用`DidEnterBackground`可用来保存应用程序状态、 保存用户数据和应用程序进入后台之前加密敏感内容。 应用程序大约需要五秒钟从此方法返回，或者会终止它。 因此，从调用可能需要超过五秒钟才能完成的清除任务内部`DidEnterBackground`方法。 必须在单独的线程中调用这些任务。

该过程是几乎完全相同的注册一个长时间运行的任务。 以下代码片段阐释了这一点在操作中：

```csharp
public override void DidEnterBackground (UIApplication application) {
  nint taskID = UIApplication.SharedApplication.BeginBackgroundTask( () => {});
  new Task ( () => {
    DoWork();
    UIApplication.SharedApplication.EndBackgroundTask(taskID);
  }).Start();
}
```

我们首先重写`DidEnterBackground`中的方法`AppDelegate`，其中我们注册我们的任务通过`BeginBackgroundTask`与我们在前面的示例中。 接下来，我们生成一个新线程和执行我们长时间运行的任务。 请注意，`EndBackgroundTask`现在从进行调用，在长时间运行的任务，因为`DidEnterBackground`方法均已返回。

> [!IMPORTANT]
> iOS 使用[监视程序机制](https://developer.apple.com/library/ios/qa/qa1693/_index.html)以确保应用程序的 UI 保持响应。 花费太多时间在应用程序`DidEnterBackground`将成为在 UI 中无响应。 若要在后台运行的任务在启动允许`DidEnterBackground`及时地，来保持 UI 响应能力和终止应用程序阻止监视器返回。


## <a name="handling-background-task-time-limits"></a>处理后台任务时间限制

iOS 上放置严格限制，如何可以运行长时间的后台任务，以及如果`EndBackgroundTask`分配的时间内不进行调用，将终止该应用程序。 通过跟踪剩余的后台处理时间，，和使用到期处理程序在必要时，我们可以避免 iOS 终止应用程序。

### <a name="accessing-background-time-remaining"></a>访问背景的剩余时间

如果具有已注册的任务的应用程序获取移到后台，已注册的任务将获得大约 600 秒运行。 我们可以检查多长时间任务必须完成使用静态`BackgroundTimeRemaining`属性的`UIApplication`类。 下面的代码将向我们提供的时间，以秒为单位，离开我们的后台任务：

```csharp
double timeRemaining = UIApplication.SharedApplication.BackgroundTimeRemaining;
```

### <a name="avoiding-app-termination-with-expiration-handlers"></a>避免使用过期处理程序的应用程序终止

除了提供对访问`BackgroundTimeRemaining`属性，iOS 提供妥善的方式来处理通过背景时间到期**过期处理程序**。 这是代码的一项任务分配的时间即将过期时，将获取执行的可选块。 过期处理程序中的代码调用`EndBackgroundTask`，并传入任务 ID，以指示应用程序也行为，并阻止 iOS 终止应用程序，即使在任务运行时间。 `EndBackgroundTask` 在过期处理程序，以及在正常执行过程中，必须调用。 

过期处理程序表示为匿名函数使用 lambda 表达式，如下图所示：

```csharp
Task.Factory.StartNew( () => {

    //expirationHandler only called if background time allowed exceeded
    var taskId = UIApplication.SharedApplication.BeginBackgroundTask(() => {
        Console.WriteLine("Exhausted time");
        UIApplication.SharedApplication.EndBackgroundTask(taskId); 
    });
    while(myFlag == true)
    {
        Console.WriteLine(UIApplication.SharedApplication.TimeRemaining);
        myFlag = SomeCalculationNeedsMoreTime();
    }
    //Only called if loop terminated due to myFlag and not expiration of time
    UIApplication.SharedApplication.EndBackgroundTask(taskId);
});
```

虽然过期处理程序不需要的要运行的代码，您应始终使用过期处理程序与后台任务。

 <a name="background_tasks_in_iOS_7" />

## <a name="background-tasks-in-ios-7"></a>在 iOS 7 + 中的后台任务

在 iOS 7 对于后台任务中最大的变化是不任务如何实现，但在运行时。

前面曾提到，预 iOS 7，在后台运行的任务必须 600 秒才能完成。 此限制的原因之一是，在后台运行的任务会使设备保持唤醒状态对任务的持续时间：

 [![](ios-backgrounding-with-tasks-images/ios6.png "保持唤醒状态预 iOS 7 的应用程序的任务的关系图")](ios-backgrounding-with-tasks-images/ios6.png#lightbox)

iOS 7 后台处理适用于更长的电池寿命。 在 iOS 7 中，后台处理成为机会： 而不是保留在设备唤醒状态，任务时，尊重设备进入睡眠状态，并改为时执行的操作以块的形式处理设备被唤醒以处理电话呼叫、 通知、 传入电子邮件和其他常见中断。 下图提供了深入了解如何一项任务可能已损坏了：

 [![](ios-backgrounding-with-tasks-images/ios7.png "任务分解为区块后 iOS 7 的关系图")](ios-backgrounding-with-tasks-images/ios7.png#lightbox)

任务运行时不会再连续，因为执行网络传输的任务必须以不同方式处理在 iOS 7 中。 建议使用开发人员将`NSURlSession`API 来处理网络传输。 下一节是后台传输的概述。

 <a name="background-transfers" />

## <a name="background-transfers"></a>后台传输

在 iOS 7 中的后台传输的主干新增`NSURLSession`API。 `NSURLSession` 使我们能够创建的任务：

1.  传输的内容经过网络和设备的中断。
1.  上传和下载大型文件 (*后台传输服务*)。


让我们仔细看看其工作。

### <a name="nsurlsession-api"></a>NSURLSession API

 `NSURLSession` 是功能强大的 API，用于通过网络传输内容。 它提供了一套工具来处理通过网络中断和应用程序状态的更改的数据传输。

`NSURLSession` API 可以创建一个或多个会话，进而生成任务以在网络上的快速的相关数据块区。 任务以异步方式运行，以便快速、 可靠地传输数据。 因为`NSURLSession`是异步的每个会话需要让系统和应用程序知道已完成传输时的完成处理程序块。

若要执行预 iOS 7 和后 iOS 7 有效的网络传输，检查是否`NSURLSession`可供排入队列传输，并且使用正则后台任务来执行传输，如果不是：

```csharp
if ([NSURLSession class]) {
  // Create a background session and enqueue transfers
}
else {
  // Start a background task and transfer directly
  // Do NOT make calls to update the UI here!
}
```

> [!IMPORTANT]
> 避免进行调用以更新在 iOS 6 兼容代码中，在后台从 UI，如 iOS 6 不支持背景 UI 更新和将终止该应用程序。


`NSURLSession` API 包括一套丰富的功能来处理身份验证、 管理失败的传输和报告客户端-但不是服务器端的错误。 它可帮助在任务中中断 iOS 7，在运行时引入的桥和快速、 可靠地传输大型文件还提供支持。 下一部分探讨此第二个功能。

### <a name="background-transfer-service"></a>后台传输服务

在 iOS 7、 之前上传或下载在后台中的文件是不可靠。 后台任务获取有限的时间来运行，但若要将文件传输所需时间取决于网络及其文件的大小。 在 iOS 7，我们可以使用`NSURLSession`已成功上传和下载大型文件。 特定`NSURLSession`处理大型文件在后台的网络传输的会话类型被称为*后台传输服务*。

使用后台传输服务启动的传输由操作系统管理，并提供 Api 来处理身份验证和错误。 传输不受任意的时间限制，因为它们可以用于上传或下载大型文件，自动更新在后台，和的详细信息中的内容。 请参阅[后台传输演练](~/ios/app-fundamentals/backgrounding/ios-backgrounding-walkthroughs/background-transfer-walkthrough.md)有关如何实现服务的详细信息。

后台传输服务通常成对出现与后台提取或远程通知，以帮助应用程序刷新中背景的内容。 在接下来的两部分中，我们将介绍注册在后台运行 iOS 6 和 iOS 7 上的整个应用程序的概念。

