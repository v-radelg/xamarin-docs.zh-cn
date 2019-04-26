---
title: 更新在后台中的 Xamarin.iOS 应用
description: 本文档介绍了各种方法更新在后台，如区域监视、 后台获取和远程通知的 Xamarin.iOS 应用。
ms.prod: xamarin
ms.assetid: A2B2231A-C045-4C11-8176-F9966485197A
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: 835dccaea79467582f56fd4b8b6b3b8f42acd632
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61392326"
---
# <a name="updating-a-xamarinios-app-in-the-background"></a>更新在后台中的 Xamarin.iOS 应用

后台刷新是唤醒处于挂起状态的应用程序或未运行，并更新的新内容的过程。 iOS 提供了用于刷新在后台中的内容的三个选项：

1.  *区域监视*并*重要位置更改服务*-可感知位置的 Api 触发后台更新根据的更改在用户的位置。 这些 Api 可以将谨慎使用刷新在非基于位置的 iOS 6 应用程序中的内容在其他选项不可用。
1.  *后台提取 (iOS 7 +)* -一种临时方法刷新*非关键*更新的内容*经常*。
1.  *远程通知 (iOS 7 +)* -接收推送通知的应用程序可以使用通知触发的后台内容刷新。 此方法可以用于更新*重要的是，时间敏感型*更新的内容*偶尔*。


以下部分介绍这些选项的基础知识。

## <a name="region-monitoring-and-significant-location-changes"></a>区域监视和重要位置更改

iOS 提供了两个可感知位置的 Api 与后台处理功能：

1.  *区域监视*是设置了区域与边界，并在用户进入或退出区域时唤醒设备的过程。 区域是循环的并且可以具有不同大小。 如果用户跨越区域边界，设备将唤醒以处理事件，通常由触发通知或启动任务。 区域监视需要 GPS，并提高电池和数据使用情况。
1.  *显著位置更改服务*是更简单、 电源节省选项可用于与移动电话无线电收发器设备。 将设备切换单元格 towers 时通知你的应用程序侦听的显著位置更改。 此服务可用于唤醒挂起或终止的应用程序，并提供机会来检查在后台的新内容。 后台活动仅限于大约 10 秒，除非搭配[后台任务](~/ios/app-fundamentals/backgrounding/ios-backgrounding-techniques/ios-backgrounding-with-tasks.md)。


应用程序不需要位置`UIBackgroundMode`若要使用这些可感知位置的 Api。 因为 iOS 不会跟踪当设备唤醒中用户的位置的更改时，可以运行的任务类型，这些 Api 提供一种解决更新 iOS 6 上的背景中的内容。 *请记住，触发使用基于位置的 Api 的后台更新将在设备资源上进行绘制，并可能会使不了解为什么应用程序需要其位置的访问权限的用户感到困惑*。 对于后台处理不已在使用位置 Api 的应用程序中实现区域监视或位置的重大更改时需格外谨慎。

使用位置监视的后台处理应用程序公开 iOS 6 中的缺陷： 如果应用程序的需求不适合背景需要类别，它具有有限的后台处理选项。 两个新 Api，随着*后台提取*并*远程通知*，iOS 7 （和更高版本） 提供了更多应用程序后台处理的机会。 接下来的两部分介绍这些新的 Api。

<a name="background_fetch" />

## <a name="background-fetch-ios-7-and-greater"></a>后台获取 (iOS 7 及更高版本)

在 iOS 6 中，输入在前台应用程序所需时间来加载新内容，短暂地向用户显示他们已看到的内容。 后台获取，以加载新数据的应用程序*之前*用户启动的应用程序，并为用户提供的最新内容。

若要实现后台获取，请编辑*Info.plist* ，并检查**启用后台模式**并**后台获取**复选框：

 [![](updating-an-application-in-the-background-images/fetch.png "编辑 Info.plist 并选中启用后台模式和背景提取复选框")](updating-an-application-in-the-background-images/fetch.png#lightbox)

接下来，在`AppDelegate`，重写`FinishedLaunching`方法设置的最小的提取间隔。 在此示例中，我们让操作系统来决定如何通常提取新的内容：

```csharp
public override bool FinishedLaunching (UIApplication application, NSDictionary launchOptions)
{
  UIApplication.SharedApplication.SetMinimumBackgroundFetchInterval (UIApplication.BackgroundFetchIntervalMinimum);
  return true;
}
```

最后，通过重写执行 fetch`PerformFetch`中的方法`AppDelegate`，并传入*完成处理程序*。 完成处理程序是一个委托，采用`UIBackgroundFetchResult`:

```csharp
public override void PerformFetch (UIApplication application, Action<UIBackgroundFetchResult> completionHandler)
{
  // Check for new data, and display it
  ...
  
  // Inform system of fetch results
  completionHandler (UIBackgroundFetchResult.NewData);
}
```

完成更新内容时，我们让操作系统知道通过调用完成处理程序使用的相应状态。 iOS 提供完成处理程序状态的三个的选项：

1.  `UIBackgroundFetchResult.NewData` -已提取新内容，并且更新应用程序时调用。
1.  `UIBackgroundFetchResult.NoData` -逐步介绍了新内容 fetch，但不会提供任何内容时调用。
1.  `UIBackgroundFetchResult.Failed` -适用于错误处理，这称为时提取程序无法完成。


使用背景提取应用程序可以调用来更新在后台从 UI。 当用户打开应用时，UI 将最新并显示新内容。 这还将更新应用程序的应用程序切换器快照，以便用户可以看到该应用程序时有新内容。

> [!IMPORTANT]
> 一次`PerformFetch`是调用，应用程序具有大约 30 秒内开始下载新内容，并调用完成处理程序块。 如果此时间太长，将终止该应用程序。 请考虑使用背景提取与_后台传输服务_下载媒体或其他大型文件时。


### <a name="backgroundfetchinterval"></a>BackgroundFetchInterval

在上面的示例代码中，我们让操作系统来决定如何通常通过将最小的提取间隔设置为提取新内容`BackgroundFetchIntervalMinimum`。 iOS 提取间隔内提供三个选项：

1.  `BackgroundFetchIntervalNever` -告诉系统永远不会提取新内容。 用于关闭提取在某些情况下，例如当用户未登录。 这是提取间隔的默认值。 
1.  `BackgroundFetchIntervalMinimum` -，让系统决定通常要提取的方式根据用户模式、 电池寿命、 数据使用情况和其他应用程序的需求。
1.  `BackgroundFetchIntervalCustom` -如果您知道应用程序的内容获取更新的频率，您可以指定"休眠"时间间隔后每个提取，则在此期间阻止应用程序从提取新内容。 该时间间隔内启动后，系统将确定何时要获取其内容。


这两`BackgroundFetchIntervalMinimum`和`BackgroundFetchIntervalCustom`依赖于系统计划提取操作。 此时间间隔是动态的适应设备的需求，以及每个用户的习惯。 例如，如果一个用户检查应用程序每天早上，和另一个检查每隔一小时，iOS 将确保内容是最新的两个用户每次打开应用程序。

后台获取应该用于使用非关键内容经常更新的应用程序。 对于具有关键更新的应用程序，应使用远程通知。 远程通知基于后台提取，并共享同一个完成处理程序。 我们将深入探讨远程通知下一步。

 <a name="remote_notifications" />


## <a name="remote-notifications-ios-7-and-greater"></a>远程通知 (iOS 7 及更高版本)

推送通知是从提供程序发送到通过设备的 JSON 消息*Apple Push Notification 服务 (APNs)*。

在 iOS 6 中，传入的推送通知告知系统来提醒用户的应用程序中发生了一些有趣的事情。 单击通知提取应用程序从挂起或终止状态中唤醒，并应用将开始更新内容。 iOS 7 （和更高版本） 通过使应用程序有机会更新在后台中的内容来扩展普通的推送通知*之前*通知用户，以便用户可以打开该应用程序，并显示新内容立即。

若要实现远程通知，请编辑*Info.plist* ，并检查**启用后台模式**并**远程通知**复选框：

 [![](updating-an-application-in-the-background-images/remote.png "后台模式下设置为启用后台模式和远程通知")](updating-an-application-in-the-background-images/remote.png#lightbox)

接下来，设置`content-available`本身为 1 的推送通知的标志。 这可让应用程序知道要显示警报之前提取新的内容：

```csharp
'aps' {
  'content-available': 1,
  'alert': 'Something new has happened in your app!''
}
```

在中*AppDelegate*，重写`DidReceiveRemoteNotification`方法来检查可用内容的通知有效负载并调用相应的完成处理程序块：

```csharp
public override void DidReceiveRemoteNotification (UIApplication application, NSDictionary userInfo, Action<UIBackgroundFetchResult> completionHandler)
{
  if([content-available]) {
    // fetch content
    completionHandler (UIBackgroundFetchResult.NewData);
  }
}
```

远程通知应很少使用的更新应用程序的功能至关重要的内容。 远程通知的详细信息，请参阅 Xamarin [iOS 中的推送通知](~/ios/platform/user-notifications/deprecated/remote-notifications-in-ios.md)指南。

> [!IMPORTANT]
> 由于远程通知中的更新机制基于后台提取，因此应用程序必须开始下载新内容并接收通知，30 秒内调用完成处理程序块或 iOS 将终止该应用程序。 考虑将与远程通知_后台传输服务_下载媒体文件和其他大型文件在后台时。


### <a name="silent-remote-notifications"></a>无提示远程通知

远程通知是简单的方法，以通知应用程序的更新，然后启动提取新的内容，不过还有一些情况下，我们不需要通知用户，发生了更改。 例如，如果用户标志用于同步的文件，我们不必每次文件更新时通知他们。 文件同步不是令人惊讶的事件，也不需要用户立即采取行动。 用户只是希望在打开时要保持最新的文件。

上面所示的情况下，对于 iOS 允许的推送通知，以无提示方式-即，无需发送警报。 若要打开到无提示的常规通知，只需从通知有效负载删除警报：

```csharp
'aps' {
  'content-available': 1
}
```

#### <a name="rate-limits"></a>速率限制

从开发人员角度来看的正常和无提示通知的最大区别是无提示推送是速率限制。 如果请求速率太高，APNs 将延迟无提示推送的发送至设备。 这是为了确保应用程序，不会耗尽与无提示的通知太多的设备资源。

但是，APNs 将允许无提示通知"下面"旁边的普通远程通知或保持活动状态的响应。 由于正则通知不是限制的速率，它们可用于存储注册无提示通知从 APNs 推送到设备，如以下关系图所示：

 [![](updating-an-application-in-the-background-images/silent.png "常规通知可用于将存储的无提示通知从 APNs 推送到设备，此关系图所示")](updating-an-application-in-the-background-images/silent.png#lightbox)

> [!IMPORTANT]
> Apple 鼓励开发人员发送无提示推送通知，每当应用程序要求，并让计划将其传递 APNs。


在本部分中，我们介绍了用于刷新在后台中的内容的各种选项，若要运行的任务不适合于背景需要类别。 现在，让我们看一些操作中的这些 Api。

 [下一篇：第 4-iOS 后台处理演练](~/ios/app-fundamentals/backgrounding/ios-backgrounding-walkthroughs/index.md)
