---
title: Firebase 云消息传送
description: Firebase Cloud Messaging (FCM) 是一种便于移动应用和服务器应用程序之间的消息传送的服务。 本文提供的 FCM 的工作原理，概述并介绍如何配置 Google 服务，以便您的应用程序可以使用 FCM。
ms.prod: xamarin
ms.assetid: E5314D7F-2AAC-40DA-BEBA-27C834F078DD
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 07/31/2018
ms.openlocfilehash: bf0523a98c8e68691228b6e305265277e2925fa3
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61017393"
---
# <a name="firebase-cloud-messaging"></a>Firebase 云消息传送

_Firebase Cloud Messaging (FCM) 是一种便于移动应用和服务器应用程序之间的消息传送的服务。本文提供的 FCM 的工作原理，概述并介绍如何配置 Google 服务，以便您的应用程序可以使用 FCM。_

[![Firebase Cloud Messaging 英雄形象](firebase-cloud-messaging-images/preview.png)](firebase-cloud-messaging-images/preview.png#lightbox)

本主题提供的 Firebase Cloud Messaging 将 Xamarin.Android 应用程序和应用程序服务器之间的消息的路由概述和它提供的分步过程，以便您的应用程序可以使用 FCM 服务获取的凭据。

## <a name="overview"></a>概述

Firebase Cloud Messaging (FCM) 是一个跨平台服务，处理发送、 路由和服务器应用程序和移动客户端应用程序之间的消息将排入队列。 FCM 是后续任务到 Google Cloud Messaging (GCM)，并基于 Google Play Services。

下图中所示，FCM 充当消息的发件人和客户端之间的中介。 一个*客户端应用*是设备运行的已启用 FCM 的应用。 *应用程序服务器*（由你或贵公司提供） 是客户端应用进行通信，与通过 FCM 的 FCM 启用服务器。 与不同的 GCM，FCM 使你可以将消息发送到客户端应用程序直接通过 Firebase 控制台通知 GUI:

[![FCM 位于客户端应用程序和应用程序服务器之间](firebase-cloud-messaging-images/01-server-fcm-app-sml.png)](firebase-cloud-messaging-images/01-server-fcm-app.png#lightbox)

使用 FCM，应用程序服务器可发送消息到单个设备、 一组设备，或多个订阅到主题的设备。 客户端应用程序可以使用 FCM 订阅下游消息从应用程序服务器 （例如，若要接收远程通知）。 有关不同类型的 Firebase 消息的详细信息，请参阅[关于 FCM 消息](https://firebase.google.com/docs/cloud-messaging/concept-options)。

## <a name="fcm-in-action"></a>Firebase Cloud Messaging 操作中

时从应用程序服务器的下游消息发送到客户端应用，应用程序服务器将发送到的消息*FCM 连接服务器*Google; 提供 FCM 连接服务器，反过来，将消息转发到正在运行的设备客户端应用程序。 可以通过 HTTP 发送消息或[XMPP](https://developers.google.com/cloud-messaging/ccs) （可扩展消息和状态显示协议）。 因为客户端应用程序不始终连接或运行的 FCM 连接服务器排入队列，并将存储消息，将它们发送到客户端应用程序重新连接并变得可用。 同样，FCM 排入队列上游消息从客户端应用程序到应用服务器，如果将应用程序服务器不可用。 有关 FCM 连接服务器的详细信息，请参阅[有关 Firebase 云消息传送服务器](https://firebase.google.com/docs/cloud-messaging/server)。

FCM 使用下面的凭据来确定应用程序服务器和客户端应用程序，并使用这些凭据授权通过 FCM 消息事务：

-   <a name="fcm-in-action-sender-id"></a>**发件人 ID** &ndash; *发件人 ID*是时创建 Firebase 项目分配一个唯一数字值。 发件人 ID 用于标识可以将消息发送到客户端应用程序的每个应用程序服务器。 发件人 ID 也是你的项目编号;注册你的项目时，可以从 Firebase 控制台获取的发件人 ID。 发件人 ID 的一个示例是`496915549731`。

-   <a name="fcm-in-action-api-key"></a>**API 密钥** &ndash; *API 密钥*授予应用程序服务器访问 Firebase 服务;FCM 使用此密钥进行身份验证的应用程序服务器。 此凭据也称为*服务器密钥*或*Web API 密钥*。 API 密钥的一个示例是`AJzbSyCTcpfRT1YRqbz-jIwp1h06YdauvewGDzk`。

-   <a name="fcm-in-action-app-id"></a>**应用程序 ID** &ndash;的客户端应用 （独立于任何给定的设备） 注册以接收来自 FCM 消息的标识。 应用程序 ID 的一个示例是`1:415712510732:android:0e1eb7a661af2460`。

-   <a name="fcm-in-action-registration-token"></a>**注册令牌** &ndash; *注册令牌*(也称为*实例 ID*) 是在客户端应用程序上的给定设备的 FCM 标识。 在运行时生成的注册令牌&ndash;时它首先会向注册 FCM 的设备上运行时，您的应用程序接收的注册令牌。 注册令牌授权客户端应用程序 （在该特定设备上运行） 的实例来接收来自 FCM 的消息。
    注册令牌的一个示例是`fkBQTHxKKhs:AP91bHuEedxM4xFAUn0z ... JKZS`（非常长的字符串）。

[设置了 Firebase Cloud Messaging](#setup_fcm) （在本指南的后面介绍） 提供了用于创建项目并生成这些凭据的详细的说明。 当创建新的项目中[Firebase 控制台](https://console.firebase.google.com/)，调用凭据文件**google-services.json**创建&ndash;中所述，将此文件添加到你的Xamarin.Android项目[使用 FCM 远程通知](~/android/data-cloud/google-messaging/remote-notifications-with-fcm.md)。

以下部分介绍当客户端应用程序与通过 FCM 的应用程序服务器通信时，如何使用这些凭据。


<a name="registration" />

### <a name="registration-with-fcm"></a>使用 FCM 的注册

消息传送进行之前，首先必须通过 FCM 注册客户端应用程序。 客户端应用程序必须完成下面的关系图中显示的注册步骤：

[![应用注册步骤关系图](firebase-cloud-messaging-images/02-app-registration-sml.png)](firebase-cloud-messaging-images/02-app-registration.png#lightbox)

1.  客户端应用程序与 FCM 以获取将发件人 ID、 API 密钥和应用程序 ID 传递到 FCM 的注册令牌。

2.  FCM 返回到客户端应用程序注册令牌。

3.  客户端应用程序 （可选） 将转发到应用服务器的注册令牌。

应用程序服务器将缓存与客户端应用程序的后续通信的注册令牌。 应用程序服务器可以将确认发送回客户端应用程序，以指示接收到的注册令牌。 发生此握手后，客户端应用程序可以接收消息 （或将消息发送到） 的应用程序服务器。 如果旧令牌已泄露，客户端应用程序可能会收到新的注册令牌 (请参阅[远程通知使用 FCM](~/android/data-cloud/google-messaging/remote-notifications-with-fcm.md)以举例说明如何应用接收注册令牌更新)。

当客户端应用程序不再想要从应用服务器接收消息时，它可以为要删除的注册令牌的应用程序服务器发送请求。 从设备上卸载客户端应用程序，FCM 会检测到这，自动通知要删除的注册令牌的应用程序服务器。



### <a name="downstream-messaging"></a>下游消息传送

下图说明了如何 Firebase Cloud Messaging 将存储和转发下游消息：

[![FCM 使用存储和转发的下游消息传送](firebase-cloud-messaging-images/03-downstream-sml.png)](firebase-cloud-messaging-images/03-downstream.png#lightbox)

当应用程序服务器将下游消息发送到客户端应用程序时，它作为枚举上图中使用以下步骤：

1.  应用程序服务器将消息发送到 FCM。

2.  如果客户端设备不可用，FCM 服务器以在稍后传输队列中存储消息。 最多 4 周的 FCM 存储中保留消息 (有关详细信息，请参阅[设置一条消息的生命期](https://firebase.google.com/docs/cloud-messaging/concept-options#ttl))。

3.  当客户端设备可用时，FCM 将转发到该设备上的客户端应用的消息。

4.  客户端应用程序接收来自 FCM 的消息、 处理，并为用户显示。 例如，如果消息是远程通知，则将它显示给通知区域中的用户。

在此消息传递方案中 （其中应用服务器发送一条消息到单个客户端应用），消息可以是长度最多为 4 kB。

有关接收在 Android 上的下游 FCM 消息的详细信息，请参阅[远程通知使用 FCM](~/android/data-cloud/google-messaging/remote-notifications-with-fcm.md)。

### <a name="topic-messaging"></a>主题的消息传送

*主题消息传送*使得应用程序服务器将消息发送到已选择加入某个特定主题的多个设备。 您还可以撰写并发送 Firebase 控制台通知 GUI 中主题的消息。 FCM 处理的路由和主题消息传送到已订阅的客户端。 此功能可用于消息，如天气警报、 股票报价和标题新闻。

[![主题的消息传送关系图](firebase-cloud-messaging-images/04-topic-messaging-sml.png)](firebase-cloud-messaging-images/04-topic-messaging.png#lightbox)

（在客户端应用程序获取注册令牌，如前面所述） 后，将主题消息传送中使用以下步骤：

1.  客户端应用程序通过将订阅消息发送到 FCM 订阅主题。

2.  应用程序服务器分发向 FCM 发送主题消息。

3.  FCM 将主题消息转发到已订阅该主题的客户端。

Firebase 主题消息传送的详细信息，请参阅 Google[主题的消息传送在 Android 上](https://firebase.google.com/docs/cloud-messaging/android/topic-messaging)。


<a name="setup_fcm" />

## <a name="setting-up-firebase-cloud-messaging"></a>Firebase Cloud Messaging 设置

应用程序中使用 FCM 服务之前，你必须创建一个新项目 （或导入现有项目） 通过[Firebase 控制台](https://console.firebase.google.com/)。 使用以下步骤创建您的应用程序的 Firebase Cloud Messaging 项目：

1.  登录到[Firebase 控制台](https://console.firebase.google.com/)使用 Google 帐户 （即，你的 Gmail 地址），单击**创建新项目**:

    [![创建新项目按钮](firebase-cloud-messaging-images/05-firebase-console-sml.png)](firebase-cloud-messaging-images/05-firebase-console.png#lightbox)

    如果你有现有的项目，单击**导入 Google 项目**。

2.  在中**创建项目**对话框中，输入你的项目的名称，然后单击**创建项目**。 在以下示例中，新的项目称为**XamarinFCM**创建：

    [![创建项目对话框](firebase-cloud-messaging-images/06-create-a-project-sml.png)](firebase-cloud-messaging-images/06-create-a-project.png#lightbox)

3.  在 Firebase 控制台**概述**，单击**将 Firebase 添加到 Android 应用**:

    [![将 Firebase 添加到 Android 应用](firebase-cloud-messaging-images/07-add-firebase-sml.png)](firebase-cloud-messaging-images/07-add-firebase.png#lightbox)

4.  在下一个屏幕中，输入您的应用程序的包名称。 在此示例中，包名称是**com.xamarin.fcmexample**。 此值必须匹配你的 Android 应用包名称。 也可以在输入应用昵称**应用昵称**字段：

    [![输入应用昵称 FCM 示例](firebase-cloud-messaging-images/08-package-name-sml.png)](firebase-cloud-messaging-images/08-package-name.png#lightbox)

5.  如果您的应用程序使用动态链接，邀请或 Google 身份验证，还必须输入签名证书在调试。 查找您的签名证书的详细信息，请参阅[查找密钥存储的 MD5 或 SHA1 签名](~/android/deploy-test/signing/keystore-signature.md)。
    在此示例中，为空的签名证书。

6.  单击**添加应用**:

    [![单击添加应用按钮](firebase-cloud-messaging-images/09-add-app-sml.png)](firebase-cloud-messaging-images/09-add-app.png#lightbox)

    应用程序自动生成服务器 API 密钥和客户端 ID。 此信息打包在**google-services.json**当您单击时自动下载的文件**添加应用**。
    请务必将此文件保存在安全的位置。

有关如何添加的详细示例**google-services.json**应用程序项目，以接收在 Android 上的 FCM 推送通知消息，请参阅[远程通知使用 FCM](~/android/data-cloud/google-messaging/remote-notifications-with-fcm.md)。



## <a name="for-further-reading"></a>有关其他参考资料

-   Google [Firebase Cloud Messaging](https://firebase.google.com/docs/cloud-messaging/) Firebase Cloud Messaging 的关键功能概述、 其工作方式，以及安装说明进行操作的说明。

-   Google[生成应用程序服务器发送请求](https://firebase.google.com/docs/cloud-messaging/send-message)介绍了如何使用你的应用程序服务器发送的消息。

-   [RFC 6120](https://tools.ietf.org/html/rfc6120)并[RFC 6121](https://tools.ietf.org/html/rfc6121)解释，并定义可扩展消息传送和协议 (XMPP)。

-   [关于 FCM 消息](https://firebase.google.com/docs/cloud-messaging/concept-options)介绍不同类型的使用 Firebase Cloud Messaging 可以发送的消息。

## <a name="summary"></a>总结

本文章提供 Firebase Cloud Messaging (FCM) 的概述。 它介绍了各种用于标识和授权应用程序服务器和客户端应用程序之间的消息传送的凭据。 它所示的注册和下游消息传送方案，并详细介绍如何通过 FCM 以使用 FCM 服务注册您的应用程序。


## <a name="related-links"></a>相关链接

- [Firebase 云消息传送](https://firebase.google.com/docs/cloud-messaging/)
