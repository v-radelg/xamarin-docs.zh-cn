---
title: 在 Xamarin.iOS 中动态通知操作按钮
description: 与 iOS 12，通知内容扩展可以添加、 删除和更新通知一起显示的操作按钮。 本文档介绍如何使用 Xamarin.iOS 动态通知操作按钮。
ms.prod: xamarin
ms.assetid: 6B34AD78-5117-42D0-B6E7-C8B4B453EAFF
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 09/04/2018
ms.openlocfilehash: 5611d673ecc7af896fd3a9e566e184e408b6b367
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60876053"
---
# <a name="dynamic-notification-action-buttons-in-xamarinios"></a>在 Xamarin.iOS 中动态通知操作按钮

在 iOS 12 中，通知还可以动态添加、 删除和更新其关联的操作按钮。 此类自定义使可能向用户提供通知的内容以及与它的用户的交互与直接相关的操作。

## <a name="sample-app-redgreennotifications"></a>应用程序示例：RedGreenNotifications

本指南中的代码段来自[RedGreenNotifications](https://developer.xamarin.com/samples/monotouch/iOS12/RedGreenNotifications)示例应用中，该示例演示了如何使用 Xamarin.iOS 适用于 iOS 12 中的通知操作按钮。

此示例应用将发送两种类型的本地通知： 红色和绿色。
应用程序发送一个通知之后，使用 3D Touch 若要查看其自定义用户界面。 然后，使用通知的操作按钮旋转它显示的图像。 旋转图像，如**重置旋转**将显示按钮，并根据需要将消失。

本指南中的代码段来自此示例应用。

## <a name="default-action-buttons"></a>默认操作按钮

通知的类别设置确定其默认操作按钮。

创建和应用程序启动时注册通知类别。
例如，在[示例应用](#sample-app-redgreennotifications)，则`FinishedLaunching`方法的`AppDelegate`执行以下操作：

- 定义一个红色的通知的类别和另一个用于绿色通知
- 通过调用注册这些类别 [`SetNotificationCategories`](xref:UserNotifications.UNUserNotificationCenter.SetNotificationCategories*)
方法 `UNUserNotificationCenter`
- 将一个附加 [`UNNotificationAction`](xref:UserNotifications.UNNotificationAction)
为每个类别

下面的示例代码显示了这的工作原理：

```csharp
public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
{
    // Request authorization to send notifications
    UNUserNotificationCenter center = UNUserNotificationCenter.Current;
    var options = UNAuthorizationOptions.Alert | UNAuthorizationOptions.Sound | UNAuthorizationOptions.Provisional | UNAuthorizationOptions.ProvidesAppNotificationSettings;
    center.RequestAuthorization(options, (bool success, NSError error) =>
    {
        // ...
        var rotateTwentyDegreesAction = UNNotificationAction.FromIdentifier("rotate-twenty-degrees-action", "Rotate 20°", UNNotificationActionOptions.None);

        var redCategory = UNNotificationCategory.FromIdentifier(
            "red-category",
            new UNNotificationAction[] { rotateTwentyDegreesAction },
            new string[] { },
            UNNotificationCategoryOptions.CustomDismissAction
        );

        var greenCategory = UNNotificationCategory.FromIdentifier(
            "green-category",
            new UNNotificationAction[] { rotateTwentyDegreesAction },
            new string[] { },
            UNNotificationCategoryOptions.CustomDismissAction
        );

        var set = new NSSet<UNNotificationCategory>(redCategory, greenCategory);
        center.SetNotificationCategories(set);
    });
    // ...
}
```

它的基于此代码中，任何通知 [`Content.CategoryIdentifier`](xref:UserNotifications.UNNotificationContent.CategoryIdentifier)
默认情况下，显示"red 类别"或"绿色类别"将**旋转 20 度**操作按钮。

## <a name="in-app-handling-of-notification-action-buttons"></a>应用程序内处理的通知操作按钮

`UNUserNotificationCenter` 具有`Delegate`类型的属性[ `IUNUserNotificationCenterDelegate` ](xref:UserNotifications.IUNUserNotificationCenterDelegate)。

在示例应用`AppDelegate`将自身设置用户通知中心中的委托为`FinishedLaunching`:

```csharp
public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
{
    // Request authorization to send notifications
    UNUserNotificationCenter center = UNUserNotificationCenter.Current;
    var options = // ...
    center.RequestAuthorization(options, (bool success, NSError error) =>
    {
        center.Delegate = this;
        // ...
```

然后，`AppDelegate`实现 [`DidReceiveNotificationResponse`](xref:UserNotifications.UNUserNotificationCenterDelegate_Extensions.DidReceiveNotificationResponse*)
若要处理的操作按钮点击：

```csharp
[Export("userNotificationCenter:didReceiveNotificationResponse:withCompletionHandler:")]
public void DidReceiveNotificationResponse(UNUserNotificationCenter center, UNNotificationResponse response, System.Action completionHandler)
{
    if (response.IsDefaultAction)
    {
        Console.WriteLine("ACTION: Default");
    }
    if (response.IsDismissAction)
    {
        Console.WriteLine("ACTION: Dismiss");
    }
    else
    {
        Console.WriteLine($"ACTION: {response.ActionIdentifier}");
    }

    completionHandler();
        }
```

此实现`DidReceiveNotificationResponse`不处理该通知**旋转 20 度**操作按钮。 相反，通知内容扩展处理点击此按钮。 下一节进一步讨论通知操作按钮处理。

## <a name="action-buttons-in-the-notification-content-extension"></a>在通知内容扩展的操作按钮

通知内容扩展包含定义通知的自定义接口的视图控制器。

可以使用此视图控制器`GetNotificationActions`和`SetNotificationActions`上的方法及其 [`ExtensionContext`](xref:UIKit.UIViewController.ExtensionContext)
若要访问和修改通知的操作按钮的属性。

在示例应用中，通知内容扩展视图控制器修改操作按钮仅当现有操作按钮上点击到响应。

> [!NOTE]
> 通知内容扩展可以响应在其视图控制器的点击操作按钮[ `DidReceiveNotificationResponse` ](xref:UserNotificationsUI.UNNotificationContentExtension_Extensions.DidReceiveNotificationResponse*)声明的一部分的方法[IUNNotificationContentExtension](xref:UserNotificationsUI.IUNNotificationContentExtension)。
>
> 尽管它共享同一个名称与`DidReceiveNotificationResponse`方法[上面所述](#in-app-handling-of-notification-action-buttons)，这是另一种方法。
>
> 通知内容扩展完成处理点击按钮后，它可以选择告知主应用程序来处理该相同点击的按钮。 若要执行此操作，它必须传递一个合适的值[UNNotificationContentExtensionResponseOption](xref:UserNotificationsUI.UNNotificationContentExtensionResponseOption)到其完成处理程序：
>
> - `Dismiss` 指示应取消的通知接口，并且主应用程序不需要处理点击按钮即可。
> - `DismissAndForwardAction` 指示应取消的通知接口，并且主应用程序还应处理点击按钮即可。
> - `DoNotDismiss` 指示，应该不消除通知接口，并且主应用程序不需要处理点击按钮即可。

内容扩展`DidReceiveNotificationResponse`方法确定点击了哪个操作按钮时，将在通知的接口和显示或隐藏图像旋转**重置**操作按钮：

```csharp
[Export("didReceiveNotificationResponse:completionHandler:")]
public void DidReceiveNotificationResponse(UNNotificationResponse response, Action<UNNotificationContentExtensionResponseOption> completionHandler)
{
    var rotationAction = ExtensionContext.GetNotificationActions()[0];

    if (response.ActionIdentifier == "rotate-twenty-degrees-action")
    {
        rotationButtonTaps += 1;

        double radians = (20 * rotationButtonTaps) * (2 * Math.PI / 360.0);
        Xamagon.Transform = CGAffineTransform.MakeRotation((float)radians);

        // 9 rotations * 20 degrees = 180 degrees. No reason to
        // show the reset rotation button when the image is half
        // or fully rotated.
        if (rotationButtonTaps % 9 == 0)
        {
            ExtensionContext.SetNotificationActions(new UNNotificationAction[] { rotationAction });
        }
        else if (rotationButtonTaps % 9 == 1)
        {
            var resetRotationAction = UNNotificationAction.FromIdentifier("reset-rotation-action", "Reset rotation", UNNotificationActionOptions.None);
            ExtensionContext.SetNotificationActions(new UNNotificationAction[] { rotationAction, resetRotationAction });
        }
    }

    if (response.ActionIdentifier == "reset-rotation-action")
    {
        rotationButtonTaps = 0;

        double radians = (20 * rotationButtonTaps) * (2 * Math.PI / 360.0);
        Xamagon.Transform = CGAffineTransform.MakeRotation((float)radians);

        ExtensionContext.SetNotificationActions(new UNNotificationAction[] { rotationAction });
    }

    completionHandler(UNNotificationContentExtensionResponseOption.DoNotDismiss);
}
```

在这种情况下，该方法传递`UNNotificationContentExtensionResponseOption.DoNotDismiss`到其完成处理程序。 这意味着，通知的接口将保持打开状态。

## <a name="related-links"></a>相关链接

- [示例应用程序 – RedGreenNotifications](https://developer.xamarin.com/samples/monotouch/iOS12/RedGreenNotifications)
- [在 Xamarin.iOS 中使用的用户通知框架](~/ios/platform/user-notifications/index.md)
- [声明可操作的通知类型](https://developer.apple.com/documentation/usernotifications/declaring_your_actionable_notification_types?language=objc)
- [UserNotifications (Apple)](https://developer.apple.com/documentation/usernotifications?language=objc)
- [什么是用户通知 (WWDC 2018) 中的新增功能](https://developer.apple.com/videos/play/wwdc2018/710/)
- [最佳实践和什么是用户通知 (WWDC 2017) 中的新增功能](https://developer.apple.com/videos/play/wwdc2017/708/)
- [生成远程通知 (Apple)](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/generating_a_remote_notification)
