---
title: 在 Xamarin.iOS 中分组的通知
description: 与 iOS 12，就可以向组中的通知中心或由应用程序或线程在锁定屏幕通知。 本文档介绍如何将发送线程和非线程的通知使用 Xamarin.iOS。
ms.prod: xamarin
ms.assetid: C6FA7C25-061B-4FD7-8E55-88597D512F3C
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 9/4/2018
ms.openlocfilehash: 2e23bb13ad35e9a7a6386d881fe64f817ca8e216
ms.sourcegitcommit: a1a58afea68912c79d16a3f64de9a0c1feb2aeb4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/30/2019
ms.locfileid: "55233258"
---
# <a name="grouped-notifications-in-xamarinios"></a>在 Xamarin.iOS 中分组的通知

默认情况下，iOS 12 将一个组中放置的所有应用的通知。 锁定屏幕和通知中心显示该组为与最新通知在最前面的堆栈。 用户可以展开组以查看它包含并消除整个组的所有通知。

应用还可以通过线程，使用户更轻松地查找并与他们感兴趣的特定信息交互组通知。

## <a name="sample-app-groupednotifications"></a>应用程序示例：GroupedNotifications

若要了解如何使用 Xamarin.iOS 分组的通知，看一看[GroupedNotifications](https://developer.xamarin.com/samples/monotouch/iOS12/GroupedNotifications)示例应用程序。

此示例应用程序模拟与各种朋友发送每条消息的通知和对其进行分组的线程的对话。 它还演示了如何非线程的通知登录的应用程序级别组。

本指南中的代码段来自此示例应用。

## <a name="request-authorization-and-allow-foreground-notifications"></a>请求授权，并允许前台通知

应用可以发送本地通知之前，它必须请求执行此操作的权限。 在示例应用[ `AppDelegate` ](xref:UIKit.UIApplicationDelegate)，则[ `FinishedLaunching` ](xref:UIKit.UIApplicationDelegate.FinishedLaunching(UIKit.UIApplication,Foundation.NSDictionary))方法请求此权限：

```csharp
public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
{
    UNUserNotificationCenter center = UNUserNotificationCenter.Current;
    center.RequestAuthorization(UNAuthorizationOptions.Alert, (bool success, NSError error) =>
    {
        // Set the Delegate regardless of success; users can modify their notification
        // preferences at any time in the Settings app.
        center.Delegate = this;
    });
    return true;
}
```

[ `Delegate` ](xref:UserNotifications.UNUserNotificationCenter.Delegate) （设置上面） 用于[ `UNUserNotificationCenter` ](xref:UserNotifications.UNUserNotificationCenter)决定前台应用程序是否应通过调用完成处理程序传递给显示传入通知[`WillPresentNotification`](xref:UserNotifications.UNUserNotificationCenterDelegate_Extensions.WillPresentNotification(UserNotifications.IUNUserNotificationCenterDelegate,UserNotifications.UNUserNotificationCenter,UserNotifications.UNNotification,System.Action{UserNotifications.UNNotificationPresentationOptions})):

```csharp
[Export("userNotificationCenter:willPresentotification:withCompletionHandler:")]
public void WillPresentNotification(UNUserNotificationCenter center, UNNotification notification, System.Action<UNNotificationPresentationOptions> completionHandler)
{
    completionHandler(UNNotificationPresentationOptions.Alert);
}
```

[ `UNNotificationPresentationOptions.Alert` ](xref:UserNotifications.UNNotificationPresentationOptions)参数指示应用应显示警报，但不是播放声音或更新锁屏提醒。

## <a name="threaded-notifications"></a>线程的通知

点击的示例应用**与 Alice 的消息**按钮重复，使其与名为 Alice 朋友发送通知的会话。
因为此会话通知都属于同一个线程，锁定屏幕和通知中心将它们组合在一起。

若要使用不同的友元启动会话，请点击**选择新朋友**按钮。 此会话的通知出现在一个单独的组。

### <a name="threadidentifier"></a>ThreadIdentifier

随时示例应用程序启动新线程，它将创建唯一的线程标识符：

```csharp
void StartNewThread()
{
    threadId = $"message-{friend}";
    // ...
}
```

若要发送线程的通知，示例应用程序：

- 检查应用程序是否已授权发送通知。
- 创建 [`UNMutableNotificationContent`](xref:UserNotifications.UNMutableNotificationContent)
对象的通知的内容和设置其 [`ThreadIdentifier`](xref:UserNotifications.UNMutableNotificationContent.ThreadIdentifier)
为上面创建的线程标识符。
- 创建一个请求，并计划通知：

```csharp
async partial void ScheduleThreadedNotification(UIButton sender)
{
    var center = UNUserNotificationCenter.Current;

    UNNotificationSettings settings = await center.GetNotificationSettingsAsync();
    if (settings.AuthorizationStatus != UNAuthorizationStatus.Authorized)
    {
        return;
    }

    string author =  // ...
    string message = // ...

    var content = new UNMutableNotificationContent()
    {
        ThreadIdentifier = threadId,
        Title = author,
        Body = message,
        SummaryArgument = author
    };

    var request = UNNotificationRequest.FromIdentifier(
        Guid.NewGuid().ToString(),
        content,
        UNTimeIntervalNotificationTrigger.CreateTrigger(1, false)
    );

    center.AddNotificationRequest(request, null);

    // ...
}
```

从具有相同的线程标识符相同的应用程序的所有通知将都出现在相同的通知组。

> [!NOTE]
> 若要设置远程通知的线程标识符，将添加`thread-id`关键通知的 JSON 有效负载。 请参阅 Apple[生成远程通知](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/generating_a_remote_notification)文档的更多详细信息。

### <a name="summaryargument"></a>SummaryArgument

`SummaryArgument` 指定如何通知会影响属于通知的通知组的左下角会出现的摘要文本。 iOS 中同一组中的通知若要创建的总体摘要说明的聚合摘要文本。

示例应用使用摘要参数作为消息的作者。 使用此方法，可能会使用 Alice 的六个通知的一组的摘要文本**Alice 和我的 5 个更多通知**。

## <a name="unthreaded-notifications"></a>非线程的通知

示例应用程序的每次点击**约会提醒**按钮发送一个各种约会提醒通知。 由于这些提醒不线程的因此它们显示在锁定屏幕上的应用程序级别通知组和通知中心。

若要发送的非线程的通知，示例应用程序的`ScheduleUnthreadedNotification`方法使用与上面类似的代码。
但是，它不会设置`ThreadIdentifier`上`UNMutableNotificationContent`对象。

## <a name="related-links"></a>相关链接

- [示例应用程序 – GroupedNotifications](https://developer.xamarin.com/samples/monotouch/iOS12/GroupedNotifications)
- [在 Xamarin.iOS 中使用的用户通知框架](~/ios/platform/user-notifications/index.md)
- [什么是用户通知 (WWDC 2018) 中的新增功能](https://developer.apple.com/videos/play/wwdc2018/710/)
- [使用分组的通知 (WWDC 2018)](https://developer.apple.com/videos/play/wwdc2018/711/)
- [最佳实践和什么是用户通知 (WWDC 2017) 中的新增功能](https://developer.apple.com/videos/play/wwdc2017/708/)
- [生成远程通知 (Apple)](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/generating_a_remote_notification)
