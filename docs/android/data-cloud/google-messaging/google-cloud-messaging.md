---
title: Google Cloud Messaging
description: Google Cloud Messaging （GCM）是一种有助于在移动应用和服务器应用程序之间进行消息传送的服务。 本文概述了 GCM 的工作原理，并说明了如何配置 Google 服务以便你的应用可以使用 GCM。
ms.prod: xamarin
ms.assetid: DF8EF401-F63D-4BA0-B2C6-B22DF8FD60CB
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 05/02/2019
ms.openlocfilehash: e9b0337c9cdcfbd8f738a11c5dffff427df620bc
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76723659"
---
# <a name="google-cloud-messaging"></a>Google Cloud Messaging

> [!WARNING]
> 2018年4月10日起已弃用的 Google GCM。 下面的文档和示例项目可能不再维护。 Google 的 GCM 服务器和客户端 Api 将在 2019 5 月29日后删除。 Google 建议将 GCM 应用迁移到 Firebase Cloud 消息（FCM）。 有关 GCM 弃用和迁移的详细信息，请参阅[Google 弃用的云消息传送](https://developers.google.com/cloud-messaging/)。
>
> 若要开始使用 Xamarin 的 Firebase Cloud 消息传递，请参阅[Firebase Cloud 消息](firebase-cloud-messaging.md)。

_Google Cloud Messaging （GCM）是一种有助于在移动应用和服务器应用程序之间进行消息传送的服务。本文概述了 GCM 的工作原理，并说明了如何配置 Google 服务以便你的应用可以使用 GCM。_

[![Google Cloud Messaging 徽标](google-cloud-messaging-images/preview-sml.png)](google-cloud-messaging-images/preview.png#lightbox)

本主题提供了有关 Google Cloud Messaging 如何在应用程序与应用程序服务器之间路由消息的详细概述，并提供了获取凭据以使你的应用可以使用 GCM 服务的分步过程。

## <a name="overview"></a>概述

Google Cloud Messaging （GCM）是一种服务，用于处理服务器应用程序与移动客户端应用程序之间的消息的发送、路由和排队。 *客户端应用*是一种在设备上运行的支持 GCM 的应用。 *应用服务器*（由你或你的公司提供）是你的客户端应用通过 gcm 与之通信的支持 GCM 的服务器：

[![GCM 位于客户端应用和应用服务器之间](google-cloud-messaging-images/01-server-gcm-app-sml.png)](google-cloud-messaging-images/01-server-gcm-app.png#lightbox)

使用 GCM，应用服务器可以将消息发送到单个设备、一组设备或多个订阅了某个主题的设备。 你的客户端应用可以使用 GCM 从应用服务器订阅下游消息（例如，接收远程通知）。 此外，GCM 使客户端应用程序可以将上游消息发回到应用服务器。

## <a name="google-cloud-messaging-in-action"></a>Google Cloud Messaging 操作

当下游消息从应用程序服务器发送到客户端应用时，应用服务器会将消息发送到*GCM 连接服务器*;然后，GCM 连接服务器将消息转发到运行客户端应用的设备。 可以通过 HTTP 或[XMPP](https://firebase.google.com/docs/cloud-messaging/xmpp-server-ref) （可扩展消息传递和状态协议）发送消息。 因为客户端应用程序并不总是连接或运行，所以 GCM 连接服务器排队并存储消息，并在客户端应用程序重新连接时将其发送到客户端应用程序。 同样，如果应用服务器不可用，GCM 会将来自客户端应用的上游消息排队发送到应用服务器。

GCM 使用以下凭据来识别应用服务器和客户端应用，并使用这些凭据通过 GCM 授权消息事务：

- **Api 密钥 &ndash;** *api 密钥*可让应用服务器访问 Google services;GCM 使用此密钥对你的应用服务器进行身份验证。
    在可以使用 GCM 服务之前，必须先通过创建*项目*从[Google DEVELOPER Console](https://console.developers.google.com/)获取 API 密钥。 应确保 API 密钥的安全;有关保护 API 密钥的详细信息，请参阅[安全使用 api 密钥的最佳实践](https://support.google.com/cloud/answer/6310037?hl=en)。

- **发件人 id &ndash;** *发送方 id*向你的客户端应用授予应用服务器 &ndash; 它是一个唯一的编号，用于标识允许向客户端应用程序发送消息的应用程序服务器。
    发件人 ID 也是你的项目编号;注册项目时，从 Google 开发人员控制台获取发件人 ID。

- **注册令牌 &ndash;** *注册令牌*是给定设备上客户端应用的 GCM 标识。 注册令牌是在运行时生成的 &ndash; 应用程序在设备上运行时首次注册 GCM 时接收注册令牌。 注册令牌授权客户端应用的实例（在该特定设备上运行）以从 GCM 接收消息。

- **应用程序 ID** &ndash; 注册为从 GCM 接收消息的客户端应用（独立于任何给定设备）的标识。 在 Android 上，应用程序 ID 是在**androidmanifest.xml**中记录的包名称，如 `com.xamarin.gcmexample`。

[设置 Google Cloud Messaging](#settingup) （本指南稍后将提供有关创建项目和生成这些凭据的详细说明。

以下部分说明了当客户端应用通过 GCM 与应用服务器通信时，如何使用这些凭据。

### <a name="registration-with-gcm"></a>向 GCM 注册

设备上安装的客户端应用必须先在 GCM 上注册，然后才能进行消息传送。 客户端应用必须完成下图中所示的注册步骤：

[![应用注册步骤](google-cloud-messaging-images/02-app-registration-sml.png)](google-cloud-messaging-images/02-app-registration.png#lightbox)

1. 客户端应用联系 GCM 以获取注册令牌，并将发件人 ID 传递到 GCM。

2. GCM 将注册令牌返回到客户端应用。

3. 客户端应用将注册令牌转发到应用服务器。

应用程序服务器将缓存注册令牌，以便以后与客户端应用程序进行通信。 应用服务器可以根据需要将确认发送回客户端应用程序，以指示已收到注册令牌。 此握手发生之后，客户端应用程序可以从应用服务器接收消息（或将消息发送到）。

当客户端应用程序不再想要从应用程序服务器接收消息时，它可以向应用服务器发送请求以删除注册令牌。 如果客户端应用正在接收主题消息（在本文后面部分介绍），则它可以取消订阅主题。
如果从设备中卸载客户端应用，则 GCM 会检测到此情况，并自动通知应用服务器删除注册令牌。

### <a name="downstream-messaging"></a>下游消息

当应用服务器向客户端应用程序发送下游消息时，将按照下图所示的步骤进行操作：

[![下游消息存储和转发关系图](google-cloud-messaging-images/03-downstream-sml.png)](google-cloud-messaging-images/03-downstream.png#lightbox)

1. 应用服务器将消息发送到 GCM。

2. 如果客户端设备不可用，则 GCM 服务器会将消息存储在队列中，以供以后传输。

3. 当客户端设备可用时，GCM 会将消息发送到该设备上的客户端应用。

4. 客户端应用程序从 GCM 收到消息并相应地对其进行处理。 例如，如果消息是远程通知，则会向用户显示该消息。

在此消息传递方案（其中，应用服务器将消息发送到单个客户端应用）中，消息的长度最大可达4kB。

有关在 Android 上接收下游 GCM 消息的详细信息（包括代码示例），请参阅[远程通知](~/android/data-cloud/google-messaging/remote-notifications-with-gcm.md)。

#### <a name="topic-messaging"></a>主题消息

*主题消息传递*是一种下游消息，应用服务器将一条消息发送到多个订阅主题（如天气预报）的客户端应用设备。 主题消息的长度最大可为2KB，主题消息每个应用最多支持1000000个订阅。 如果 GCM 仅用于主题消息传递，则不需要客户端应用程序向应用服务器发送注册令牌。

#### <a name="group-messaging"></a>组消息传递

*组消息传递*是一种下游消息，应用服务器将一条消息发送到属于组的多个客户端应用设备（例如，属于单个用户的一组设备）。 对于 iOS 设备，组消息的最大长度为2KB，最高可达4KB 的 Android 设备。 组最多只能有20个成员。

### <a name="upstream-messaging"></a>上游消息传递

如果客户端应用程序连接到支持[XMPP](https://firebase.google.com/docs/cloud-messaging/xmpp-server-ref)的服务器，它可以将消息发送回应用程序服务器，如下图所示：

[![上游消息传送关系图](google-cloud-messaging-images/04-upstream-sml.png)](google-cloud-messaging-images/04-upstream.png#lightbox)

1. 客户端应用程序将消息发送到 GCM XMPP 连接服务器。

2. 如果应用服务器已断开连接，则 GCM 服务器会将消息存储在队列中，以便以后转发。

3. 重新连接应用服务器时，GCM 会将消息转发到应用服务器。

4. 应用服务器将分析消息以验证客户端应用的身份，然后将 "ack" 发送到 GCM 以确认消息接收。

5. 应用服务器处理该消息。

Google 的[上游消息](https://firebase.google.com/docs/cloud-messaging/xmpp-server-ref#upstream)说明如何构建 JSON 编码的消息并将其发送到运行 Google 的基于 XMPP 的云连接服务器的应用程序服务器。

<a name="settingup" />

## <a name="setting-up-google-cloud-messaging"></a>设置 Google Cloud Messaging

在应用中使用 GCM 服务之前，必须先获取用于访问 Google GCM 服务器的凭据。 以下部分介绍完成此过程所需的步骤：

### <a name="enable-google-services-for-your-app"></a>为你的应用启用 Google 服务

1. 使用 Google 帐户（即 gmail 地址）登录到[Google 开发人员控制台](https://developers.google.com/mobile/add?platform=android)，并创建一个新项目。 如果已有项目，请选择要启用 GCM 的项目。 在下面的示例中，将创建一个名为**XamarinGCM**的新项目：

    [![创建 XamarinGCM 项目](google-cloud-messaging-images/05-create-gcm-app-sml.png)](google-cloud-messaging-images/05-create-gcm-app.png#lightbox)

2. 接下来，输入应用的包名称（在此示例中，包名为**gcmexample**），然后单击 "**继续" 以选择并配置服务**：

    [输入包名称 ![](google-cloud-messaging-images/06-package-name-sml.png)](google-cloud-messaging-images/06-package-name.png#lightbox)

    请注意，此包名称也是应用的应用程序 ID。

3. "**选择和配置服务**" 部分列出了可添加到应用的 Google 服务。 单击 "**云消息传送**"：

    [![选择云消息传送](google-cloud-messaging-images/07-choose-gcm-service-sml.png)](google-cloud-messaging-images/07-choose-gcm-service.png#lightbox)

4. 接下来，单击 "**启用 GOOGLE CLOUD 消息传递**"：

    [![启用 Google Cloud Messaging](google-cloud-messaging-images/08-enable-gcm-sml.png)](google-cloud-messaging-images/08-enable-gcm.png#lightbox)

5. 将为您的应用程序生成一个**服务器 API 密钥**和一个**发送程序 ID** 。 记录这些值，然后单击 "**关闭**"：

    [显示 ![服务器 API 密钥和发件人 ID](google-cloud-messaging-images/09-get-api-key-and-id-sml.png)](google-cloud-messaging-images/09-get-api-key-and-id.png#lightbox)

    保护 API 密钥，&ndash; 不打算公开使用。 如果 API 密钥已泄露，则未经授权的服务器可能会将消息发布到客户端应用程序。
    [安全使用 api 密钥的最佳实践](https://support.google.com/cloud/answer/6310037?hl=en)提供了用于保护 API 密钥的有用指导原则。

### <a name="view-your-project-settings"></a>查看项目设置

你可以随时通过登录到[Google Cloud Console](https://console.cloud.google.com/)并选择你的项目来查看你的项目设置。 例如，你可以通过在页面顶部的下拉菜单中选择你的项目来查看**发送方 ID** （在本示例中，项目称为**XamarinGCM**）。 发件人 ID 是此屏幕截图中所示的项目编号（此处的发件人 ID 为**9349932736**）：

[查看发送方 ID ![](google-cloud-messaging-images/10-view-server-id-sml.png)](google-cloud-messaging-images/10-view-server-id.png#lightbox)

若要查看**api 密钥**，请单击 " **api 管理器**"，然后单击 "**凭据**"：

[![查看 API 密钥](google-cloud-messaging-images/11-view-credentials-sml.png)](google-cloud-messaging-images/11-view-credentials.png#lightbox)

## <a name="for-further-reading"></a>其他阅读材料

- [Rfc 6120](https://tools.ietf.org/html/rfc6120)和[rfc 6121](https://tools.ietf.org/html/rfc6121)解释并定义了可扩展的消息传递协议（XMPP）。

## <a name="summary"></a>摘要

本文提供 Google Cloud Messaging （GCM）的概述。 它介绍了用于在应用程序服务器和客户端应用程序之间识别和授权消息传递的各种凭据。 其中介绍了最常见的消息传递方案，并详细说明了向 GCM 注册应用程序以使用 GCM 服务的步骤。

## <a name="related-links"></a>相关链接

- [云消息传送](https://developers.google.com/cloud-messaging/)
