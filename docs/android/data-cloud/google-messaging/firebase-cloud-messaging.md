---
title: Firebase 云消息传送
description: Firebase Cloud 消息传递（FCM）是一种便于在移动应用和服务器应用程序之间进行消息传递的服务。 本文概述了 FCM 的工作原理，并说明了如何配置 Google 服务，以便你的应用可以使用 FCM。
ms.prod: xamarin
ms.assetid: E5314D7F-2AAC-40DA-BEBA-27C834F078DD
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 07/31/2018
ms.openlocfilehash: c97c931445122cbaa613b87e3778f4dc9e92f4d0
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73023658"
---
# <a name="firebase-cloud-messaging"></a>Firebase 云消息传送

_Firebase Cloud 消息传递（FCM）是一种便于在移动应用和服务器应用程序之间进行消息传递的服务。本文概述了 FCM 的工作原理，并说明了如何配置 Google 服务，以便你的应用可以使用 FCM。_

[![Firebase 云消息传送英雄图像](firebase-cloud-messaging-images/preview.png)](firebase-cloud-messaging-images/preview.png#lightbox)

本主题简要概述了 Firebase 云消息传送 Xamarin 应用和应用服务器之间消息的方式，并提供了获取凭据的分步过程，使应用可以使用 FCM 服务。

## <a name="overview"></a>概述

Firebase 云消息传送（FCM）是一项跨平台服务，用于处理服务器应用程序与移动客户端应用程序之间的消息的发送、路由和排队。 FCM 是 Google Cloud Messaging （GCM）的后续版本，并以 Google Play Services 为基础构建。

如下面的关系图中所示，FCM 充当消息发送方和客户端之间的媒介。 *客户端应用*是一种在设备上运行的支持 FCM 的应用。 *应用服务器*（由你或你的公司提供）是 FCM 支持的服务器，客户端应用通过 FCM 与此服务器进行通信。 与 GCM 不同，FCM 使你可以通过 Firebase 控制台通知 GUI 直接将消息发送到客户端应用程序：

[![FCM 位于客户端应用和应用服务器之间](firebase-cloud-messaging-images/01-server-fcm-app-sml.png)](firebase-cloud-messaging-images/01-server-fcm-app.png#lightbox)

使用 FCM，应用程序服务器可以将消息发送到单个设备、一组设备或多个已订阅主题的设备。 客户端应用程序可以使用 FCM 从应用程序服务器（例如，接收远程通知）订阅下游消息。 有关不同类型的 Firebase 消息的详细信息，请参阅[关于 FCM 消息](https://firebase.google.com/docs/cloud-messaging/concept-options)。

## <a name="fcm-in-action"></a>Firebase 云消息传送操作

将下游消息从应用服务器发送到客户端应用时，应用服务器会将消息发送到 Google 提供的*FCM 连接服务器*;然后，FCM 连接服务器将消息转发到运行客户端应用的设备。 可以通过 HTTP 或[XMPP](https://developers.google.com/cloud-messaging/ccs) （可扩展消息传递和状态协议）发送消息。 因为客户端应用程序并不总是连接或运行，所以 FCM 连接服务器排队并存储消息，并在客户端应用程序重新连接时将其发送到客户端应用程序。 同样，如果应用服务器不可用，则 FCM 将从客户端应用程序向应用服务器发送上游消息。 有关 FCM 连接服务器的详细信息，请参阅[关于 Firebase Cloud 消息传递服务器](https://firebase.google.com/docs/cloud-messaging/server)。

FCM 使用以下凭据来标识应用服务器和客户端应用，并使用这些凭据通过 FCM 授权消息事务：

- <a name="fcm-in-action-sender-id"></a>**发件人 id &ndash;** *发送方 id*是在创建 Firebase 项目时分配的唯一数值。 发件人 ID 用于标识可以向客户端应用程序发送消息的每个应用程序服务器。 发件人 ID 也是你的项目编号;注册项目时，可以从 Firebase 控制台获取发件人 ID。 `496915549731`的发送方 ID 的示例。

- <a name="fcm-in-action-api-key"></a>**Api 密钥 &ndash;** *api 密钥*向应用服务器授予 Firebase services 的访问权限;FCM 使用此密钥对应用服务器进行身份验证。 此凭据也称为*服务器密钥*或*Web API 密钥*。 `AJzbSyCTcpfRT1YRqbz-jIwp1h06YdauvewGDzk`API 密钥的示例。

- <a name="fcm-in-action-app-id"></a>**应用 ID** &ndash; 注册为接收来自 FCM 的消息的客户端应用（独立于任何给定设备）的标识。 `1:415712510732:android:0e1eb7a661af2460`应用 ID 的示例。

- <a name="fcm-in-action-registration-token"></a>**注册令牌 &ndash;** 注册*令牌（也*称为*实例 ID*）是给定设备上客户端应用的 FCM 标识。 注册令牌是在运行时生成的 &ndash; 应用程序在设备上运行时首次注册 FCM 时接收注册令牌。 注册令牌授权客户端应用的实例（在该特定设备上运行），以接收来自 FCM 的消息。
    注册令牌的示例 `fkBQTHxKKhs:AP91bHuEedxM4xFAUn0z ... JKZS` （非常长的字符串）。

[设置 Firebase 云消息传送](#setup_fcm)（稍后在本指南中）提供了有关创建项目和生成这些凭据的详细说明。 当你在[Firebase 控制台](https://console.firebase.google.com/)中创建新项目时 **，将创建一个名为**FCM 的凭据文件 &ndash; 将此文件添加到你的 Xamarin Android 项目，如[使用的远程通知](~/android/data-cloud/google-messaging/remote-notifications-with-fcm.md)中所述。

以下部分说明了当客户端应用程序通过 FCM 与应用程序服务器通信时，如何使用这些凭据。

<a name="registration" />

### <a name="registration-with-fcm"></a>注册到 FCM

客户端应用必须先注册到 FCM，然后才能进行消息传送。 客户端应用必须完成下图中所示的注册步骤：

[![应用注册步骤示意图](firebase-cloud-messaging-images/02-app-registration-sml.png)](firebase-cloud-messaging-images/02-app-registration.png#lightbox)

1. 客户端应用联系 FCM 以获取注册令牌，并将发件人 ID、API 密钥和应用 ID 传递到 FCM。

2. FCM 将注册令牌返回到客户端应用。

3. 客户端应用（可选）将注册令牌转发到应用服务器。

应用程序服务器将缓存注册令牌，以便以后与客户端应用程序进行通信。 应用服务器可以将确认发送回客户端应用程序，以指示已收到注册令牌。 此握手发生之后，客户端应用程序可以从应用服务器接收消息（或将消息发送到）。 如果旧令牌被泄露，则客户端应用可能会收到新的注册令牌（有关应用如何接收注册令牌更新的示例，请参阅[使用 FCM 的远程通知](~/android/data-cloud/google-messaging/remote-notifications-with-fcm.md)）。

当客户端应用程序不再想要从应用程序服务器接收消息时，它可以向应用服务器发送请求以删除注册令牌。 如果从设备中卸载客户端应用，则 FCM 会检测到此情况，并自动通知应用服务器删除注册令牌。

### <a name="downstream-messaging"></a>下游消息

下图说明了 Firebase Cloud 消息存储和转发下游消息的方式：

[![FCM 使用存储和转发进行下游消息传递](firebase-cloud-messaging-images/03-downstream-sml.png)](firebase-cloud-messaging-images/03-downstream.png#lightbox)

当应用服务器向客户端应用程序发送下游消息时，它将使用上图中列举的以下步骤：

1. 应用服务器将消息发送到 FCM。

2. 如果客户端设备不可用，则 FCM 服务器会将消息存储在队列中，以便以后传输。 消息将在 FCM 存储中保留最多4周（有关详细信息，请参阅[设置消息的使用期限](https://firebase.google.com/docs/cloud-messaging/concept-options#ttl)）。

3. 当客户端设备可用时，FCM 会将消息转发到该设备上的客户端应用。

4. 客户端应用程序从 FCM 接收消息，对其进行处理，并将其显示给用户。 例如，如果消息是远程通知，则会向用户显示通知区域。

在此消息传递方案（其中，应用服务器将消息发送到单个客户端应用）中，消息的长度最大可达4kB。

有关在 Android 上接收下游 FCM 消息的详细信息，请参阅[通过 FCM 远程通知](~/android/data-cloud/google-messaging/remote-notifications-with-fcm.md)。

### <a name="topic-messaging"></a>主题消息

*主题消息传递*使应用服务器能够将消息发送到多个已选择特定主题的设备。 还可以通过 Firebase 控制台通知 GUI 撰写和发送主题消息。 FCM 处理主题消息到已订阅客户端的路由和传递。 此功能可用于天气警报、股票报价和新闻等消息。

[![主题消息传递关系图](firebase-cloud-messaging-images/04-topic-messaging-sml.png)](firebase-cloud-messaging-images/04-topic-messaging.png#lightbox)

在主题消息（在客户端应用程序获取注册令牌后，如前文所述）中使用以下步骤：

1. 客户端应用程序通过将订阅消息发送到 FCM 来订阅主题。

2. 应用服务器将主题消息发送到 FCM 以进行分发。

3. FCM 将主题消息转发到已订阅该主题的客户端。

有关 Firebase 主题消息的详细信息，请参阅[Android 上的 Google 主题消息](https://firebase.google.com/docs/cloud-messaging/android/topic-messaging)。

<a name="setup_fcm" />

## <a name="setting-up-firebase-cloud-messaging"></a>设置 Firebase 云消息传送

在应用中使用 FCM 服务之前，必须通过[Firebase 控制台](https://console.firebase.google.com/)创建一个新项目（或导入现有项目）。 使用以下步骤为应用程序创建 Firebase 云消息传送项目：

1. 用 Google 帐户（即 Gmail 地址）登录到[Firebase 控制台](https://console.firebase.google.com/)，并单击 "**创建新项目**"：

    [![创建新项目 "按钮](firebase-cloud-messaging-images/05-firebase-console-sml.png)](firebase-cloud-messaging-images/05-firebase-console.png#lightbox)

    如果已有项目，请单击 "**导入 Google 项目**"。

2. 在 "**创建项目**" 对话框中，输入项目的名称，然后单击 "**创建项目**"。 在下面的示例中，将创建一个名为**XamarinFCM**的新项目：

    [!["创建项目" 对话框](firebase-cloud-messaging-images/06-create-a-project-sml.png)](firebase-cloud-messaging-images/06-create-a-project.png#lightbox)

3. 在 Firebase 控制台**概述**中，单击 "**将 Firebase 添加到 Android 应用程序**"：

    [![将 Firebase 添加到 Android 应用](firebase-cloud-messaging-images/07-add-firebase-sml.png)](firebase-cloud-messaging-images/07-add-firebase.png#lightbox)

4. 在下一个屏幕中，输入应用的包名称。 在此示例中，包名称为**fcmexample**。 此值必须与 Android 应用的包名称匹配。 还可以在 "**应用昵称**" 字段中输入应用昵称：

    [![输入 FCM 示例作为应用别名](firebase-cloud-messaging-images/08-package-name-sml.png)](firebase-cloud-messaging-images/08-package-name.png#lightbox)

5. 如果你的应用使用动态链接、邀请或 Google Auth，你还必须输入你的调试签名证书。 有关查找签名证书的详细信息，请参阅[查找密钥存储的 MD5 或 SHA1 签名](~/android/deploy-test/signing/keystore-signature.md)。
    在此示例中，签名证书留空。

6. 单击 "**添加应用程序**"：

    [![单击 "添加应用程序" 按钮](firebase-cloud-messaging-images/09-add-app-sml.png)](firebase-cloud-messaging-images/09-add-app.png#lightbox)

    服务器 API 密钥和客户端 ID 会自动为应用程序生成。 此信息打包在**google-服务 json**文件中，该文件在你单击 "**添加应用程序**" 时自动下载。
    请确保将此文件保存在安全的位置。

有关如何将**google services json**添加到应用项目以在 Android 上接收 FCM 推送通知消息的详细示例，请参阅[使用 FCM 远程通知](~/android/data-cloud/google-messaging/remote-notifications-with-fcm.md)。

## <a name="for-further-reading"></a>获取进一步阅读

- Google 的[Firebase Cloud 消息传递](https://firebase.google.com/docs/cloud-messaging/)概述了 Firebase Cloud 消息传递的关键功能、其工作原理的说明以及设置说明。

- Google 的[生成应用服务器发送请求](https://firebase.google.com/docs/cloud-messaging/send-message)说明了如何通过应用服务器发送消息。

- [Rfc 6120](https://tools.ietf.org/html/rfc6120)和[rfc 6121](https://tools.ietf.org/html/rfc6121)解释并定义了可扩展的消息传递协议（XMPP）。

- [关于 FCM 消息](https://firebase.google.com/docs/cloud-messaging/concept-options)介绍可通过 Firebase 云消息传送发送的不同类型的消息。

## <a name="summary"></a>总结

本文提供 Firebase 云消息传送（FCM）的概述。 它介绍了用于在应用程序服务器和客户端应用程序之间识别和授权消息传递的各种凭据。 其中阐释了注册和下游消息传递方案，并详细说明了向 FCM 注册应用程序以使用 FCM 服务的步骤。

## <a name="related-links"></a>相关链接

- [Firebase 云消息传送](https://firebase.google.com/docs/cloud-messaging/)
