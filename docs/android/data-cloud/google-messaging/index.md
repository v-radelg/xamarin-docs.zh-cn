---
title: Google 消息传送
description: 本部分包含的指南介绍如何使用 Google 消息服务实现 Xamarin Android 应用。
ms.prod: xamarin
ms.assetid: 85E8DF92-D160-4763-A7D3-458B4C31635F
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/12/2018
ms.openlocfilehash: bfcc526d1787caede4361030f5bf6718a59672b1
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70754395"
---
# <a name="google-messaging"></a>Google 消息传送

_本部分包含的指南介绍如何使用 Google 消息服务实现 Xamarin Android 应用。_

## <a name="firebase-cloud-messagingfirebase-cloud-messagingmd"></a>[Firebase 云消息传送](firebase-cloud-messaging.md)

Firebase Cloud 消息传递（FCM）是一种便于在移动应用和服务器应用程序之间进行消息传递的服务。 FCM 是 Google 的后续 Google Cloud Messaging。 本文概述了 FCM 的工作原理，并提供了获取凭据的分步过程，使应用程序可以使用 FCM 服务。

## <a name="remote-notifications-with-firebase-cloud-messagingremote-notifications-with-fcmmd"></a>[Firebase 云消息传送的远程通知](remote-notifications-with-fcm.md)

本演练逐步说明如何使用 Firebase Cloud 消息传递 Xamarin Android 应用程序中的远程通知（也称为推送通知）。 它说明了如何实现与 Firebase Cloud 消息传递（FCM）通信所需的各种类，并提供了有关如何配置 Android 清单以访问 FCM 的示例，并演示了使用 Firebase 的下游消息传递控制台.

## <a name="google-cloud-messaginggoogle-cloud-messagingmd"></a>[Google 云消息传送](google-cloud-messaging.md)

本部分概述了 Google Cloud Messaging （GCM）如何在应用程序与应用程序服务器之间路由消息，并提供了获取凭据以使应用程序可以使用 GCM 服务的分步过程。 （请注意，GCM 已被 FCM 取代。）

> [!NOTE]
> GCM 已被[Firebase 云消息](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md)（FCM）替代。
> GCM 服务器和客户端 Api[已弃用](https://firebase.googleblog.com/2018/04/time-to-upgrade-from-gcm-to-fcm.html)，并且将在2019年4月11日之后不再可用。

## <a name="remote-notifications-with-google-cloud-messagingremote-notifications-with-gcmmd"></a>[Google Cloud Messaging 的远程通知](remote-notifications-with-gcm.md)

本部分逐步说明如何使用 Google Cloud Messaging 在 Xamarin 中实现远程通知。
它介绍了在 Android 应用程序中启用 Google Cloud Messaging 必须利用的各种组件。
