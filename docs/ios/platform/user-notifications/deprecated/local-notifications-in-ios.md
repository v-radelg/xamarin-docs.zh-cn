---
title: 在 Xamarin.iOS 中的通知
description: 本部分演示如何在 Xamarin.iOS 中实现本地通知。 它将介绍 iOS 通知的各种 UI 元素，并讨论了 API 的涉及创建并显示一条通知。
ms.prod: xamarin
ms.assetid: 5BB76915-5DB0-48C7-A267-FA9F7C50793E
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 07/13/2018
ms.openlocfilehash: 6710abd28a2b0f992296008d12950b95ec29783d
ms.sourcegitcommit: dd73477b1bccbd7ca45c1fb4e794da6b36ca163d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/30/2019
ms.locfileid: "66394695"
---
# <a name="notifications-in-xamarinios"></a>在 Xamarin.iOS 中的通知

> [!IMPORTANT]
> 在本部分中的信息与有关 iOS 9 及更高。 适用于 iOS 10 及更高版本，请参阅[用户通知框架指南](~/ios/platform/user-notifications/index.md)。

iOS 具有三种方法可以向用户指示已收到通知：

- **声音或振动**-iOS 可以播放声音通知用户。 如果禁用了声音，可以配置该设备为振动。
- **警报**-可以使用有关通知的信息在屏幕上显示一个对话框。
- **徽章**-当发布一条通知时，可以显示一个数字 （带有徽章） 上的应用程序图标。

此外提供了 iOS*通知中心*，将向用户显示的所有通知，本地和远程。 用户可以访问这从屏幕顶部向下轻扫：

![通知中心](local-notifications-in-ios-images/image13.png "通知中心")

## <a name="creating-local-notifications-in-ios"></a>在 iOS 中创建本地通知

iOS 就相当简单，若要创建和处理本地通知。
首先，iOS 8 要求应用程序要求的显示通知用户的权限。 尝试发送本地通知之前将以下代码添加到您的应用程序-[附加示例](https://developer.xamarin.com/samples/monotouch/LocalNotifications/)将其放入**AppDelegate**的**FinishedLaunching**方法。

```csharp
var notificationSettings = UIUserNotificationSettings.GetSettingsForTypes(
    UIUserNotificationType.Alert | UIUserNotificationType.Badge | UIUserNotificationType.Sound, null
);
application.RegisterUserNotificationSettings(notificationSettings);
```

[![确认发送本地通知的能力](local-notifications-in-ios-images/image0-sml.png "确认能够发送本地通知")](local-notifications-in-ios-images/image0.png#lightbox)

若要计划本地通知，创建`UILocalNotification`对象，设置`FireDate`，并将其通过安排`ScheduleLocalNotification`方法`UIApplication.SharedApplication`对象。 下面的代码段演示如何安排将触发一分钟以后，并显示一条消息的警报的通知：

```csharp
UILocalNotification notification = new UILocalNotification();
notification.FireDate = NSDate.FromTimeIntervalSinceNow(15);
//notification.AlertTitle = "Alert Title"; // required for Apple Watch notifications
notification.AlertAction = "View Alert";
notification.AlertBody = "Your 15 second alert has fired!";
UIApplication.SharedApplication.ScheduleLocalNotification(notification);
```

下面的屏幕截图显示了此警报如下所示：

[![](local-notifications-in-ios-images/image2-sml.png "示例警报")](local-notifications-in-ios-images/image2.png#lightbox)

请注意，如果用户已选择*不允许*将显示通知，然后执行任何操作。

如果你想要应用于应用程序图标带有编号的徽章，您可以将其如以下代码行中所示：

```csharp
notification.ApplicationIconBadgeNumber = 1;
```

在顺序 play 图标时，声音将 SoundName 属性设置通知下面的代码段中所示：

```csharp
notification.SoundName = UILocalNotification.DefaultSoundName;
```

如果通知声音的长度超过 30 秒，iOS 将播放默认声音相反。

> [!IMPORTANT]
> 是将激发委托通知两次在 iOS 模拟器中的 bug。 在设备上运行应用程序时，不会发生此问题。

## <a name="handling-notifications"></a>处理通知

iOS 应用程序处理方式几乎完全相同的远程和本地通知。 当运行应用程序时，`ReceivedLocalNotification`方法或`ReceivedRemoteNotification`方法`AppDelegate`将调用类，并通知信息将作为参数传递。

应用程序可以以不同方式处理一条通知。 例如，应用程序可能只显示警报，以提醒用户有关的一些事件。 或通知可能用于向过程已完成，如到服务器的同步文件的用户显示警报。

下面的代码演示如何处理本地通知和显示警报，并将徽章编号重置为零：

```csharp
public override void ReceivedLocalNotification(UIApplication application, UILocalNotification notification)
{
    // show an alert
    UIAlertController okayAlertController = UIAlertController.Create(notification.AlertAction, notification.AlertBody, UIAlertControllerStyle.Alert);
    okayAlertController.AddAction(UIAlertAction.Create("OK", UIAlertActionStyle.Default, null));

    Window.RootViewController.PresentViewController(okayAlertController, true, null);

    // reset our badge
    UIApplication.SharedApplication.ApplicationIconBadgeNumber = 0;
}
```

如果应用程序未运行，iOS 将播放声音和/或更新图标徽章 （如果适用）。 当用户启动与警报关联的应用程序时，将启动应用程序和`FinishedLaunching`将调用在应用程序委托的方法和通知信息将通过传入`launchOptions`参数。 如果选项字典包含键`UIApplication.LaunchOptionsLocalNotificationKey`，则`AppDelegate`知道从本地通知已启动应用程序。 以下代码段演示了此过程：

```csharp
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
```

有关远程的通知，请`launchOptions`将具有`LaunchOptionsRemoteNotificationKey`带有关联`NSDictionary`包含远程通知负载。 您可以通过通知有效负载中提取`alert`， `badge`，和`sound`密钥。 下面的代码段演示如何获取远程通知：

```csharp
NSDictionary remoteNotification = options[UIApplication.LaunchOptionsRemoteNotificationKey];
if(remoteNotification != null)
{
    string alert = remoteNotification["alert"];
}
```

## <a name="summary"></a>总结

本部分介绍了如何创建和发布 Xamarin.iOS 中的通知。 它显示如何应用程序可能响应通知通过重写`ReceivedLocalNotification`方法或`ReceivedRemoteNotification`中的方法`AppDelegate`。

## <a name="related-links"></a>相关链接

- [本地通知 （示例）](https://developer.xamarin.com/samples/monotouch/LocalNotifications)
- [本地和推送通知面向开发人员](https://developer.apple.com/notifications/)
- [本地和推送通知编程指南](https://developer.apple.com/library/prerelease/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/)
- [UIApplication](http://iosapi.xamarin.com/?link=T%3aMonoTouch.UIKit.UIApplication)
- [UILocalNotification](http://iosapi.xamarin.com/?link=T%3aMonoTouch.UIKit.UILocalNotification)
