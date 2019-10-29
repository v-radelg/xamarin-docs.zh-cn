---
title: Xamarin 中的通知
ms.prod: xamarin
ms.assetid: 2E54F1D0-45F4-43A7-B3A3-4F483B7150CB
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/01/2018
ms.openlocfilehash: 5d91904e35a658b03d4602567e5a123cafd6926c
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73025538"
---
# <a name="notifications-in-xamarinandroid"></a>Xamarin 中的通知

本部分介绍如何在 Xamarin 中实施通知。 它介绍了 Android 通知的各种 UI 元素，并讨论了创建和显示通知所涉及的 API。

## <a name="local-notifications-in-androidlocal-notificationsmd"></a>[Android 中的本地通知](local-notifications.md)

本部分介绍如何在 Xamarin 中实现本地通知。 它介绍了 Android 通知的各种 UI 元素，并讨论了创建和显示通知所涉及的 Api。

## <a name="walkthrough---using-local-notifications-in-xamarinandroidlocal-notifications-walkthroughmd"></a>[演练-在 Xamarin 中使用本地通知](local-notifications-walkthrough.md)  

本演练介绍如何在 Xamarin Android 应用程序中使用本地通知。 它演示了创建和发布通知的基础知识。 当用户单击通知银箱中的通知时，它会启动第二个活动。 

## <a name="further-reading"></a>其他阅读材料

[Firebase cloud](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md)消息传递 &ndash; Firebase 云消息传送（FCM）是一种有助于在移动应用和服务器应用程序之间进行消息传递的服务。 Firebase Cloud 消息传递可用于实现 Xamarin Android 应用程序中的远程通知（也称为推送通知）。

此 Android 开发人员 &ndash; 的[通知](https://developer.android.com/guide/topics/ui/notifiers/notifications.html)是适用于 android 通知的最终指南。 它包含一个设计注意事项部分，该部分可帮助你设计通知，以使其符合 Android 用户界面的准则。 它在启动活动时提供有关 preserviing 导航的更多背景信息，并说明如何在通知中显示进度并控制锁屏上的媒体播放。

[NotificationListenerService](xref:Android.Service.Notification.NotificationListenerService) &ndash; 此 Android 服务使你的应用可以侦听（并与）在 Android 设备上发布的所有通知，而不只是应用注册接收的通知。
请注意，用户必须显式授予你的应用程序的权限，以便能够侦听设备上的通知。

## <a name="related-links"></a>相关链接

- [本地通知（示例）](https://docs.microsoft.com/samples/xamarin/monodroid-samples/localnotifications)
- [远程通知（示例）](https://docs.microsoft.com/samples/xamarin/monodroid-samples/remotenotifications)
