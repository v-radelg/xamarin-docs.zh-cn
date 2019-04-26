---
title: Xamarin.iOS 中的严重警报
description: 本文档介绍如何使用 Xamarin.iOS 严重警报。 严重警报，引入了 iOS 12、 播放声音而不考虑请勿打扰是否在上的中断性通知或响铃开关处于关闭状态。
ms.prod: xamarin
ms.assetid: 75742257-081D-44F4-B49E-FB807DF85262
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 09/04/2018
ms.openlocfilehash: 264b1935aefe6f5ddf4bfcd17c491f6858bd4ffb
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60876100"
---
# <a name="critical-alerts-in-xamarinios"></a>Xamarin.iOS 中的严重警报

使用 iOS 12，应用可以将发送严重警报。 严重警报播放声音无论启用请勿打扰，或使用响铃开关处于关闭状态。 这些通知会造成中断，并且仅用于时用户必须立即采取行动。

## <a name="custom-critical-alert-entitlement"></a>自定义关键警报权利

若要在应用中，首先显示严重警报[请求自定义重要的警报通知权利](https://developer.apple.com/contact/request/notifications-critical-alerts-entitlement/)从 Apple。

从 Apple 接收此权利和有关如何配置你的应用任何相关联的说明来使用它，添加自定义后[权利](~/ios/deploy-test/provisioning/entitlements.md)到你的应用**Entitlements.plist**文件。 然后，配置你**iOS 捆绑签名**要使用的选项**Entitlements.plist**签名模拟器和设备上的应用程序时。

## <a name="request-authorization"></a>请求授权

应用的通知授权请求会提示用户允许或禁止应用的通知。 如果通知授权请求要求提供发送严重警报的权限，应用将还使用户有机会选择启用严重警报。

下面的代码请求将发送严重警报和标准通知和声音通过传递相应的权限 [`UNAuthorizationOptions`](xref:UserNotifications.UNAuthorizationOptions)
值复制到[ `RequestAuthorization` ](xref:UserNotifications.UNUserNotificationCenter.RequestAuthorization*):

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

## <a name="local-critical-alerts"></a>本地严重警报

若要发送本地严重警报，创建 [`UNMutableNotificationContent`](xref:UserNotifications.UNMutableNotificationContent)
并设置其`Sound`属性为：

- `UNNotificationSound.DefaultCriticalSound`它使用默认的重要通知声音。
- `UNNotificationSound.GetCriticalSound`可用于指定自定义声音捆绑在一起使用您的应用程序和卷。

然后，创建`UNNotificationRequest`从通知内容，并将其添加到通知中心：

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
> 如果不启用这些应用，将不会传递严重警报。 将显示第一个提示以及次发送严重警报的应用程序请求权限时，用户还可以启用或禁用你的应用中的严重警报**通知**部分中的 ios**设置**应用程序。

## <a name="remote-critical-alerts"></a>远程严重警报

有关远程严重警报的信息，请参阅[什么是新中的用户通知](https://developer.apple.com/videos/play/wwdc2018/710/)会话中的从 WWDC 2018 和[生成远程通知](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/generating_a_remote_notification)文档。

## <a name="related-links"></a>相关链接

- [在 Xamarin.iOS 中使用的用户通知框架](~/ios/platform/user-notifications/index.md)
- [UserNotifications (Apple)](https://developer.apple.com/documentation/usernotifications?language=objc)
- [什么是用户通知 (WWDC 2018) 中的新增功能](https://developer.apple.com/videos/play/wwdc2018/710/)
- [最佳实践和什么是用户通知 (WWDC 2017) 中的新增功能](https://developer.apple.com/videos/play/wwdc2017/708/)
- [生成远程通知 (Apple)](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/generating_a_remote_notification)
