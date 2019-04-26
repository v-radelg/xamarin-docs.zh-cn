---
title: 演练-在 Xamarin.iOS 中使用本地通知
description: 在本部分中我们将了解如何在 Xamarin.iOS 应用程序中使用本地通知。 它将演示创建和发布一条通知，将弹出警报时的应用接收到的基础知识。
ms.prod: xamarin
ms.assetid: 32B9C6F0-2BB3-4295-99CB-A75418969A62
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: 7589784563906d60fc8026feac9ea16362463bfa
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61085090"
---
# <a name="walkthrough---using-local-notifications-in-xamarinios"></a>演练-在 Xamarin.iOS 中使用本地通知

_在本部分中我们将了解如何在 Xamarin.iOS 应用程序中使用本地通知。它将演示创建和发布一条通知，将弹出警报时的应用接收到的基础知识。_

> [!IMPORTANT]
> 在本部分中的信息与 iOS 9 相关和之前，它已被保留此处以支持较旧 iOS 版本。 适用于 iOS 10 及更高版本，请参阅[用户通知框架指南](~/ios/platform/user-notifications/index.md)的 iOS 设备上支持本地和远程通知。

## <a name="walkthrough"></a>演练

允许创建简单的应用程序将显示运行中的本地通知。 此应用程序上将具有一个按钮。 当我们单击按钮时，它将创建本地通知。 经过指定时间段后，我们将看到显示的通知。


1. 在 Visual Studio for Mac 中，创建一个新的单一视图 iOS 解决方案并对其`Notifications`。
1. 打开`Main.storyboard`文件，并将按钮拖到视图。 将该按钮命名**按钮**，并为其提供标题**添加通知**。 您可能还要设置某些[约束](~/ios/user-interface/designer/designer-auto-layout.md)到现在的按钮： 

    ![](local-notifications-in-ios-walkthrough-images/image3.png "设置在按钮上的某些约束")
1. 编辑`ViewController`类，并将以下事件处理程序添加到 ViewDidLoad 方法：

    ```csharp
    button.TouchUpInside += (sender, e) =>
    {
        // create the notification
        var notification = new UILocalNotification();

        // set the fire date (the date time in which it will fire)
        notification.FireDate = NSDate.FromTimeIntervalSinceNow(60);

        // configure the alert
        notification.AlertAction = "View Alert";
        notification.AlertBody = "Your one minute alert has fired!";

        // modify the badge
        notification.ApplicationIconBadgeNumber = 1;

        // set the sound to be the default sound
        notification.SoundName = UILocalNotification.DefaultSoundName;

        // schedule it
        UIApplication.SharedApplication.ScheduleLocalNotification(notification);
    };
    ```

    此代码将创建一条通知，使用声音、 图标徽章将值设置为 1，并向用户显示一条警报。

1. 编辑文件，接下来`AppDelegate.cs`，首先将以下代码添加到`FinishedLaunching`方法。 我们已检查，以查看设备是否正在运行 iOS 8 中，如果我们正在**需**以请求用户的权限，以接收通知：

    ```csharp
    if (UIDevice.CurrentDevice.CheckSystemVersion (8, 0)) {
            var notificationSettings = UIUserNotificationSettings.GetSettingsForTypes (
                UIUserNotificationType.Alert | UIUserNotificationType.Badge | UIUserNotificationType.Sound, null
            );

            application.RegisterUserNotificationSettings (notificationSettings);
        }
    ```

1. 仍在`AppDelegate.cs`，添加以下方法，将被调用时收到通知：

    ```csharp
    public override void ReceivedLocalNotification(UIApplication application, UILocalNotification notification)
            {
                // show an alert
                UIAlertController okayAlertController = UIAlertController.Create(notification.AlertAction, notification.AlertBody, UIAlertControllerStyle.Alert);
                okayAlertController.AddAction(UIAlertAction.Create("OK", UIAlertActionStyle.Default, null));

                UIApplication.SharedApplication.KeyWindow.RootViewController.PresentViewController(okayAlertController, true, null);

                // reset our badge
                UIApplication.SharedApplication.ApplicationIconBadgeNumber = 0;
            }

    ```

1. 我们需要以处理通知由于本地通知程序启动时所在的情况。 编辑方法`FinishedLaunching`在`AppDelegate`包含以下代码段：


    ```csharp
    // check for a notification

    if (launchOptions != null)
    {
        // check for a local notification
        if (launchOptions.ContainsKey(UIApplication.LaunchOptionsLocalNotificationKey))
        {
            var localNotification = launchOptions[UIApplication.LaunchOptionsLocalNotificationKey] as UILocalNotification;
            if (localNotification != null)
            {
                UIAlertController okayAlertController = UIAlertController.Create(localNotification.AlertAction, localNotification.AlertBody, UIAlertControllerStyle.Alert);
                okayAlertController.AddAction(UIAlertAction.Create("OK", UIAlertActionStyle.Default, null));

                Window.RootViewController.PresentViewController(okayAlertController, true, null);

                // reset our badge
                UIApplication.SharedApplication.ApplicationIconBadgeNumber = 0;
            }
        }
    }

    ```

1. 最后，运行该应用程序。 在 iOS 8 中，将提示允许通知。 单击**确定**，然后单击**添加通知**按钮。 片刻后您应看到警报对话框，如以下屏幕截图中所示：

    ![](local-notifications-in-ios-walkthrough-images/image0.png "确认可将通知发送") ![](local-notifications-in-ios-walkthrough-images/image1.png "添加通知按钮") ![](local-notifications-in-ios-walkthrough-images/image2.png "通知警报对话框")

## <a name="summary"></a>总结

本演练演示了如何使用各种 API 用于创建和发布在 iOS 中的通知。 它还演示了如何使用一个标记来向用户提供一些应用程序的特定反馈更新应用程序图标。


## <a name="related-links"></a>相关链接

- [本地通知 （示例）](https://developer.xamarin.com/samples/monotouch/LocalNotifications)
- [本地和推送通知编程指南](https://developer.apple.com/library/prerelease/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/)
