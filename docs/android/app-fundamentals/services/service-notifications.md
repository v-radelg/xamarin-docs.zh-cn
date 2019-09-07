---
title: 服务通知
description: 本指南讨论 Android 服务如何使用本地通知将信息分派给用户。
ms.prod: xamarin
ms.assetid: 6C06FDE7-6385-40EF-AC7C-8EFB54E29F45
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: 5d25604db1f88702f4c24df21b3ebba6c9c2fc95
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70754820"
---
# <a name="service-notifications"></a>服务通知

_本指南讨论 Android 服务如何使用本地通知将信息分派给用户。_

## <a name="service-notifications-overview"></a>服务通知概述

服务通知允许应用向用户显示信息，即使 Android 应用程序不在前台。 通知可以为用户提供操作，例如显示应用程序中的活动。 下面的代码示例演示服务如何向用户分派通知：

```csharp
[Service]
public class MyService: Service 
{
    // A notification requires an id that is unique to the application.
    const int NOTIFICATION_ID = 9000;
    
    public override StartCommandResult OnStartCommand(Intent intent, StartCommandFlags flags, int startId)
    {
        // Code omitted for clarity - here is where the service would do something.
    
        // Work has finished, now dispatch anotification to let the user know.
        Notification.Builder notificationBuilder = new Notification.Builder(this)
            .SetSmallIcon(Resource.Drawable.ic_notification_small_icon)
            .SetContentTitle(Resources.GetString(Resource.String.notification_content_title))
            .SetContentText(Resources.GetString(Resource.String.notification_content_text));
        
        var notificationManager = (NotificationManager)GetSystemService(NotificationService);
        notificationManager.Notify(NOTIFICATION_ID, notificationBuilder.Build());
    }
}
```

此屏幕截图是显示的通知的示例：

[![状态栏中显示的通知图标](service-notifications-images/01-notification-sml.png)](service-notifications-images/01-notification.png#lightbox)

当用户从顶部向下滑动通知屏幕时，将显示完整通知：

![通知托盘中显示的 fred](service-notifications-images/02-fullnotification.png)

## <a name="updating-a-notification"></a>更新通知

若要更新通知，服务将使用同一通知 ID 重新发布通知。 Android 将在必要时在状态栏中显示或更新通知。

```csharp 
void UpdateNotification(string content)
{
   var notification = GetNotification(content, pendingIntent);

   NotificationManager notificationManager = (NotificationManager)GetSystemService(Context.NotificationService);
   notificationManager.Notify(NOTIFICATION_ID, notification);
}

Notification GetNotification(string content, PendingIntent intent)
{
   return new Notification.Builder(this)
           .SetContentTitle(tag)
           .SetContentText(content)
           .SetSmallIcon(Resource.Drawable.NotifyLg)
           .SetLargeIcon(BitmapFactory.DecodeResource(Resources, Resource.Drawable.Icon))
           .SetContentIntent(intent).Build();
}
```

有关通知的详细信息，请访问[Android 通知](~/android/app-fundamentals/notifications/index.md)指南的 "[本地通知](~/android/app-fundamentals/notifications/local-notifications.md)" 部分。

## <a name="related-links"></a>相关链接

- [Android 中的本地通知](~/android/app-fundamentals/notifications/local-notifications.md)
