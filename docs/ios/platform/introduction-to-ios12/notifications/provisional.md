---
title: 在 Xamarin.iOS 中临时通知
description: 本文档介绍如何使用 Xamarin.iOS 处理临时通知。 临时通知，iOS 12 中, 引入了允许应用程序发送静默通知而无需显式用户权限。
ms.prod: xamarin
ms.assetid: 5DCB36B9-2637-48AE-8FC0-F6124F08AC48
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 09/04/2018
ms.openlocfilehash: 434dd2e2ee7a0064b706872a228070c5114078c4
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61034767"
---
# <a name="provisional-notifications-in-xamarinios"></a>在 Xamarin.iOS 中临时通知

临时通知使应用程序来提供通知，而无需用户的明确前期同意。 这些通知安静地到达，并且仅在通知中心，用户加入或退出其持续传递之前预览它们可以在其中显示。

在通知中心，用户可以指定应用程序应停止传递临时通知，继续传递暂时，或启动醒目的位置传递。

## <a name="sample-app-redgreennotifications"></a>应用程序示例：RedGreenNotifications

看一看[RedGreenNotifications](https://developer.xamarin.com/samples/monotouch/iOS12/RedGreenNotifications)示例应用程序，将发送临时通知。

## <a name="sending-provisional-notifications"></a>发送临时通知

若要发送临时通知，提供`UNAuthorizationOptions.Provisional`为的选项 [`RequestAuthorization`](xref:UserNotifications.UNUserNotificationCenter.RequestAuthorization*)
方法的`UNUserNotificationCenter`:

```csharp
public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
{
    UNUserNotificationCenter center = UNUserNotificationCenter.Current;
    var options = UNAuthorizationOptions.Alert | UNAuthorizationOptions.Sound | UNAuthorizationOptions.Provisional;
    center.RequestAuthorization(options, (bool success, NSError error) => {
        // ...
    );
    return true;
}
```

如果在用户提升临时通知突出传递到`UNAuthorizationOptions`值传递给`RequestAuthorization`将确定新的通知传递设置 (在上面的代码`UNAuthorizationOptions.Alert`和`UNAuthorizationOptions.Sound`)。

## <a name="related-links"></a>相关链接

- [示例应用程序 – RedGreenNotifications](https://developer.xamarin.com/samples/monotouch/iOS12/RedGreenNotifications)
- [在 Xamarin.iOS 中使用的用户通知框架](~/ios/platform/user-notifications/index.md)
- [UserNotifications (Apple)](https://developer.apple.com/documentation/usernotifications?language=objc)
- [什么是用户通知 (WWDC 2018) 中的新增功能](https://developer.apple.com/videos/play/wwdc2018/710/)
- [最佳实践和什么是用户通知 (WWDC 2017) 中的新增功能](https://developer.apple.com/videos/play/wwdc2017/708/)
- [生成远程通知 (Apple)](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/generating_a_remote_notification)
