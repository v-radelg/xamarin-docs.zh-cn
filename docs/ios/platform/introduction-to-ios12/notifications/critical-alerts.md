---
title: Xamarin 中的严重警报
description: 本文档介绍如何将关键警报与 Xamarin 一起使用。 IOS 12 中引入的关键警报是可播放声音的中断性通知，无论是否启用了 "请勿打扰" 或响铃开关均为 "关闭"。
ms.prod: xamarin
ms.assetid: 75742257-081D-44F4-B49E-FB807DF85262
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 09/04/2018
ms.openlocfilehash: 54a214215f77b66f6a4b134dcb8d27b26c44fb6c
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2019
ms.locfileid: "70291296"
---
# <a name="critical-alerts-in-xamarinios"></a>Xamarin 中的严重警报

对于 iOS 12，应用程序可以发送关键警报。 无论是否启用 "请勿打扰" 或响铃开关关闭，关键警报都将播放声音。 这些通知会造成中断，只应在用户必须立即采取措施时使用。

## <a name="custom-critical-alert-entitlement"></a>自定义关键警报权利

若要在应用中显示关键警报，请首先从 Apple[请求自定义关键警报通知权限](https://developer.apple.com/contact/request/notifications-critical-alerts-entitlement/)。

从 Apple 接收此权利并遵循有关如何配置应用以使用它的任何相关说明后，将自定义[权利](~/ios/deploy-test/provisioning/entitlements.md)添加到应用的**info.plist**文件。 然后，在模拟器和设备上对应用进行签名时，将**IOS 捆绑签名**选项配置为使用**info.plist** 。

## <a name="request-authorization"></a>请求授权

应用的通知授权请求会提示用户允许或禁止应用的通知。 如果通知授权请求要求提供发送关键警报的权限，则该应用程序还会使用户有机会选择使用关键警报。

以下代码通过传递适当的来请求发送关键警报和标准通知和声音的权限[`UNAuthorizationOptions`](xref:UserNotifications.UNAuthorizationOptions)
[`RequestAuthorization`](xref:UserNotifications.UNUserNotificationCenter.RequestAuthorization*)值：

```csharp
public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
{
    UNUserNotificationCenter center = UNUserNotificationCenter.Current;
    var options = UNAuthorizationOptions.Alert | UNAuthorizationOptions.Sound | UNAuthorizationOptions.CriticalAlert;
    center.RequestAuthorization(options, (bool success, NSError error) => {
        // ...
    );
    return true;
}
```

## <a name="local-critical-alerts"></a>本地关键警报

若要发送本地关键警报，请创建[`UNMutableNotificationContent`](xref:UserNotifications.UNMutableNotificationContent)
并将其`Sound`属性设置为：

- `UNNotificationSound.DefaultCriticalSound`，它使用默认的关键通知声音。
- `UNNotificationSound.GetCriticalSound`，它允许您指定与应用程序和卷捆绑在一起的自定义声音。

然后， `UNNotificationRequest`从通知内容创建，并将其添加到通知中心：

```csharp
var content = new UNMutableNotificationContent()
{
    Title = "Critical alert title",
    Body = "Text of the critical alert",
    CategoryIdentifier = "my-critical-alert-category",
    // Sound = UNNotificationSound.DefaultCriticalSound
    Sound = UNNotificationSound.GetCriticalSound("my_critical_sound.m4a", 1.0f)
};

var request = UNNotificationRequest.FromIdentifier(
    Guid.NewGuid().ToString(),
    content,
    UNTimeIntervalNotificationTrigger.CreateTrigger(3, false)
);

var center = UNUserNotificationCenter.Current;
center.AddNotificationRequest(request, null);
```

> [!IMPORTANT]
> 如果未对你的应用启用关键警报，则不会传递它们。 除了第一次应用请求发送关键警报时显示的提示，用户还可以在 iOS**设置**应用的应用**通知**部分启用或禁用关键警报。

## <a name="remote-critical-alerts"></a>远程关键警报

有关远程关键警报的信息，请参阅 WWDC 2018 和[生成远程通知](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/generating_a_remote_notification)文档中的[用户通知中的新增功能](https://developer.apple.com/videos/play/wwdc2018/710/)。

## <a name="related-links"></a>相关链接

- [Xamarin 中的用户通知框架](~/ios/platform/user-notifications/index.md)
- [UserNotifications （Apple）](https://developer.apple.com/documentation/usernotifications?language=objc)
- [用户通知中的新增功能（WWDC 2018）](https://developer.apple.com/videos/play/wwdc2018/710/)
- [用户通知中的最佳实践和新增功能（WWDC 2017）](https://developer.apple.com/videos/play/wwdc2017/708/)
- [生成远程通知（Apple）](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/generating_a_remote_notification)
