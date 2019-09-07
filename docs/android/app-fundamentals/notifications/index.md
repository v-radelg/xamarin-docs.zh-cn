---
title: Xamarin 中的通知
ms.prod: xamarin
ms.assetid: 2E54F1D0-45F4-43A7-B3A3-4F483B7150CB
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: 57c77b5d8d6a4763dc7b5c7a62a51ffe3137827d
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70755364"
---
# <a name="notifications-in-xamarinandroid"></a>Xamarin 中的通知

本部分介绍如何在 Xamarin 中实施通知。 它介绍了 Android 通知的各种 UI 元素，并讨论了创建和显示通知所涉及的 API。

## <a name="local-notifications-in-androidlocal-notificationsmd"></a>[Android 中的本地通知](local-notifications.md)

本部分介绍如何在 Xamarin 中实现本地通知。 它介绍了 Android 通知的各种 UI 元素，并讨论了创建和显示通知所涉及的 Api。

## <a name="walkthrough---using-local-notifications-in-xamarinandroidlocal-notifications-walkthroughmd"></a>[演练-在 Xamarin 中使用本地通知](local-notifications-walkthrough.md)  

本演练介绍如何在 Xamarin Android 应用程序中使用本地通知。 它演示了创建和发布通知的基础知识。 当用户单击通知银箱中的通知时，它会启动第二个活动。 

## <a name="further-reading"></a>其他阅读材料

[Firebase 云消息传送](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md)&ndash; Firebase Cloud 消息传递（FCM）是一种便于在移动应用和服务器应用程序之间进行消息传递的服务。 Firebase Cloud 消息传递可用于实现 Xamarin Android 应用程序中的远程通知（也称为推送通知）。

[通知](https://developer.android.com/guide/topics/ui/notifiers/notifications.html)&ndash;此 android 开发人员主题是适用于 android 通知的最终指南。 它包含一个设计注意事项部分，该部分可帮助你设计通知，以使其符合 Android 用户界面的准则。 它在启动活动时提供有关 preserviing 导航的更多背景信息，并说明如何在通知中显示进度并控制锁屏上的媒体播放。

[NotificationListenerService](xref:Android.Service.Notification.NotificationListenerService)&ndash;此 Android 服务使你的应用可以侦听（并与）在 Android 设备上发布的所有通知，而不只是应用注册接收的通知。
请注意，用户必须显式授予你的应用程序的权限，以便能够侦听设备上的通知。

## <a name="related-links"></a>相关链接

- [本地通知（示例）](https://docs.microsoft.com/samples/xamarin/monodroid-samples/localnotifications)
- [远程通知（示例）](https://docs.microsoft.com/samples/xamarin/monodroid-samples/remotenotifications)
