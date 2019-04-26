---
title: 服务通知
description: 本指南介绍如何 Android 服务可能使用本地通知调度到用户的信息。
ms.prod: xamarin
ms.assetid: 6C06FDE7-6385-40EF-AC7C-8EFB54E29F45
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: d56f67254a9eae334fa8ac3f08d3ef270800c309
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61012264"
---
# <a name="service-notifications"></a>服务通知

_本指南介绍如何 Android 服务可能使用本地通知调度到用户的信息。_


## <a name="service-notifications-overview"></a>服务通知概述

服务通知允许用于向用户显示信息的应用，即使 Android 应用程序不在前景中。 很可能的通知，以便提供用于用户，例如，显示从应用程序的活动操作。 下面的代码示例演示了一项服务可能会如何调度到用户的通知：

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

这是屏幕截图显示通知的示例：

[![在状态栏中显示的通知图标](service-notifications-images/01-notification-sml.png)](service-notifications-images/01-notification.png#lightbox)

当用户滑向下的顶部的通知屏幕时，将显示完整的通知：

![通知栏中显示的通知](service-notifications-images/02-fullnotification.png)


## <a name="updating-a-notification"></a>正在更新通知

若要更新通知，该服务将重新发布使用相同的通知 id。 Android 将显示或更新状态栏中根据需要的通知。

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

有关通知的详细信息现已推出[本地通知](~/android/app-fundamentals/notifications/local-notifications.md)一部分[Android 通知](~/android/app-fundamentals/notifications/index.md)指南。


## <a name="related-links"></a>相关链接

- [在 Android 中的本地通知](~/android/app-fundamentals/notifications/local-notifications.md)
