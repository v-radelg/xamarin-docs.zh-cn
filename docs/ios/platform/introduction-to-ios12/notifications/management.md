---
title: 在 Xamarin.iOS 中的通知管理
description: 本文档介绍如何使用 Xamarin.iOS 来充分利用 iOS 12 中引入的新通知管理功能。
ms.prod: xamarin
ms.assetid: F1D90729-F85A-425B-B633-E2FA38FB4A0C
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 09/04/2018
ms.openlocfilehash: 0157a685ac990c0626cd4d6001ef853c6a28b993
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/08/2019
ms.locfileid: "57668174"
---
# <a name="notification-management-in-xamarinios"></a>在 Xamarin.iOS 中的通知管理

在 iOS 12 中，操作系统可以从通知中心的深层链接和设置应用到应用的通知管理屏幕。 此屏幕应允许用户选择加入并利用各种类型的通知的应用会发送。

## <a name="sample-app-redgreennotifications"></a>应用程序示例：RedGreenNotifications

若要查看通知管理的工作原理的示例，看一看[RedGreenNotifications](https://developer.xamarin.com/samples/monotouch/iOS12/RedGreenNotifications)示例应用程序。

此示例应用 （红色和绿色） 发送两种类型的通知，并提供允许用户选择加入或退出任意一种类型的屏幕。

本指南中的代码段来自此示例应用。

## <a name="notification-management-screen"></a>通知管理屏幕

在示例应用中，`ManageNotificationsViewController`定义允许用户独立地启用和禁用通知红色和绿色通知的用户界面。 它是一种标准 [`UIViewController`](xref:UIKit.UIViewController)
包含[ `UISwitch` ](xref:UIKit.UISwitch)为每种通知类型。 切换为任何类型的通知开关将，保存在用户默认值，该类型的通知的用户的首选项：

```csharp
partial void HandleRedNotificationsSwitchValueChange(UISwitch sender)
{
    NSUserDefaults.StandardUserDefaults.SetBool(sender.On, RedNotificationsEnabledKey);
}
```

> [!NOTE]
> 通知管理屏幕还会检查用户已完全禁用应用的通知。 如果是这样，它会隐藏单个通知类型的切换。 为此，通知管理屏幕：
>
> - 调用[ `UNUserNotificationCenter.Current.GetNotificationSettingsAsync` ](xref:UserNotifications.UNUserNotificationCenter.GetNotificationSettingsAsync) ，并检查[ `AuthorizationStatus` ](xref:UserNotifications.UNNotificationSettings.AuthorizationStatus)属性。
> - 隐藏单个通知类型切换，如果应用已完全禁用通知。
> - 重新检查是否已被通知禁用应用程序移动到前台，因为用户可以启用/禁用通知 iOS 设置在任何时候每次。

示例应用`ViewController`类，该类实际发送本地通知，以确保该通知是一种类型的用户想要接收之前发送的通知，检查的用户的首选项：

```csharp
partial void HandleTapRedNotificationButton(UIButton sender)
{
    bool redEnabled = NSUserDefaults.StandardUserDefaults.BoolForKey(ManageNotificationsViewController.RedNotificationsEnabledKey);
    if (redEnabled)
    {
        // ...
```

## <a name="deep-link"></a>深层链接

iOS 的深层链接到应用的通知管理屏幕从通知中心和设置应用中的应用的通知设置。 若要实现此目的，应用必须：

- 指示通知管理屏幕可通过传递`UNAuthorizationOptions.ProvidesAppNotificationSettings`到应用的通知授权请求。
- 实现`OpenSettings`方法从[ `IUNUserNotificationCenterDelegate` ](xref:UserNotifications.IUNUserNotificationCenterDelegate)。

### <a name="authorization-request"></a>授权请求

通知管理屏幕可用，应用应通过向操作系统指示`UNAuthorizationOptions.ProvidesAppNotificationSettings`（以及任何其他通知传递选项所需） 选项设置为`RequestAuthorization`方法`UNUserNotificationCenter`。

例如，在示例应用`AppDelegate`:

```csharp
public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
{
    // Request authorization to send notifications
    UNUserNotificationCenter center = UNUserNotificationCenter.Current;
    var options = UNAuthorizationOptions.ProvidesAppNotificationSettings | UNAuthorizationOptions.Alert | UNAuthorizationOptions.Sound | UNAuthorizationOptions.Provisional;
    center.RequestAuthorization(options, (bool success, NSError error) =>
    {
        // ...
```

### <a name="opensettings-method"></a>OpenSettings 方法

`OpenSettings`深入链接到应用的通知管理屏幕中，系统调用的方法应直接向该屏幕将用户导航。

在示例应用中，此方法执行到 segue`ManageNotificationsViewController`如有必要：

```csharp
[Export("userNotificationCenter:openSettingsForNotification:")]
public void OpenSettings(UNUserNotificationCenter center, UNNotification notification)
{
    var navigationController = Window.RootViewController as UINavigationController;
    if (navigationController != null)
    {
        var currentViewController = navigationController.VisibleViewController;
        if (currentViewController is ViewController)
        {
            currentViewController.PerformSegue(ManageNotificationsViewController.ShowManageNotificationsSegue, this);
        }

    }
}
```

## <a name="related-links"></a>相关链接

- [示例应用程序 – RedGreenNotifications](https://developer.xamarin.com/samples/monotouch/iOS12/RedGreenNotifications)
- [在 Xamarin.iOS 中使用的用户通知框架](~/ios/platform/user-notifications/index.md)
- [UserNotifications (Apple)](https://developer.apple.com/documentation/usernotifications?language=objc)
- [什么是用户通知 (WWDC 2018) 中的新增功能](https://developer.apple.com/videos/play/wwdc2018/710/)
- [最佳实践和什么是用户通知 (WWDC 2017) 中的新增功能](https://developer.apple.com/videos/play/wwdc2017/708/)
- [生成远程通知 (Apple)](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/generating_a_remote_notification)
