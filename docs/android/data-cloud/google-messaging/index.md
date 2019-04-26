---
title: Google 消息传送
description: 本部分包含介绍如何实现使用 Google 消息传送服务的 Xamarin.Android 应用的指南。
ms.prod: xamarin
ms.assetid: 85E8DF92-D160-4763-A7D3-458B4C31635F
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/12/2018
ms.openlocfilehash: 8c2e3705f0f867b6993d0bdcb22f6672c853498e
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61016734"
---
# <a name="google-messaging"></a>Google 消息传送

_本部分包含介绍如何实现使用 Google 消息传送服务的 Xamarin.Android 应用的指南。_

## <a name="firebase-cloud-messagingfirebase-cloud-messagingmd"></a>[Firebase 云消息传送](firebase-cloud-messaging.md)

Firebase Cloud Messaging (FCM) 是一种便于移动应用和服务器应用程序之间的消息传送的服务。 FCM 是到 Google Cloud Messaging 的 Google 的后续版本。 本文提供 FCM 的工作原理的概述和它提供的分步过程，以便您的应用程序可以使用 FCM 服务获取的凭据。

## <a name="remote-notifications-with-firebase-cloud-messagingremote-notifications-with-fcmmd"></a>[远程通知使用 Firebase Cloud Messaging](remote-notifications-with-fcm.md)

本演练介绍如何使用 Firebase Cloud Messaging 实现远程通知 （也称为推送通知） 的分步说明，Xamarin.Android 应用程序中。 它演示如何实现的各种类所需的通信使用 Firebase Cloud Messaging (FCM)，提供有关如何配置用于访问 FCM，Android 清单的示例并演示如何使用 Firebase 下游消息传递控制台。

## <a name="google-cloud-messaginggoogle-cloud-messagingmd"></a>[Google 云消息传送](google-cloud-messaging.md)

本部分提供 Google Cloud Messaging (GCM) 将您的应用程序和应用程序服务器之间的消息的路由的高级概述，并获取凭据，以便您的应用程序可以使用 GCM 服务提供的分步过程。 （请注意，已被 FCM 取代 GCM）。

> [!NOTE]
> 已被取代 GCM [Firebase Cloud Messaging](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md) (FCM)。
> GCM 服务器和客户端 Api[已弃用](https://firebase.googleblog.com/2018/04/time-to-upgrade-from-gcm-to-fcm.html)将不再提供为 2019 年 4 月 11 日推出。

## <a name="remote-notifications-with-google-cloud-messagingremote-notifications-with-gcmmd"></a>[Google 云消息传送与远程通知](remote-notifications-with-gcm.md)

本部分提供如何在 Xamarin.Android 中实现远程通知的分步说明使用 Google Cloud Messaging。
它还说明了必须用于启用 Google Cloud Messaging 的 Android 应用程序中的各种组件。


