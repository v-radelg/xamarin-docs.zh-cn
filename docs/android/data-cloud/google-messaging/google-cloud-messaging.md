---
title: Google Cloud Messaging
description: Google Cloud Messaging (GCM) 是一种便于移动应用和服务器应用程序之间的消息传送的服务。 此文章概述的 GCM 的工作原理，并介绍如何配置 Google 服务，使您的应用程序可以使用 GCM。
ms.prod: xamarin
ms.assetid: DF8EF401-F63D-4BA0-B2C6-B22DF8FD60CB
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/12/2018
ms.openlocfilehash: c2ca567ffcb247622d1b3e8f3e0136c453723b96
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61017588"
---
# <a name="google-cloud-messaging"></a>Google Cloud Messaging

_Google Cloud Messaging (GCM) 是一种便于移动应用和服务器应用程序之间的消息传送的服务。此文章概述的 GCM 的工作原理，并介绍如何配置 Google 服务，使您的应用程序可以使用 GCM。_

[![Google Cloud Messaging 徽标](google-cloud-messaging-images/preview-sml.png)](google-cloud-messaging-images/preview.png#lightbox)

本主题提供的 Google Cloud Messaging 将您的应用程序和应用程序服务器之间的消息的路由概述和它提供的分步过程，以便您的应用程序可以使用 GCM 服务获取的凭据。

> [!NOTE]
> 已被取代 GCM [Firebase Cloud Messaging](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md) (FCM)。
> GCM 服务器和客户端 Api[已弃用](https://firebase.googleblog.com/2018/04/time-to-upgrade-from-gcm-to-fcm.html)将不再提供为 2019 年 4 月 11 日推出。

## <a name="overview"></a>概述

Google Cloud Messaging (GCM) 是处理发送、 路由和服务器应用程序和移动客户端应用程序之间的消息队列服务。 一个*客户端应用*是一种启用 GCM 的应用，设备上运行。 *应用程序服务器*（由你或贵公司提供） 是客户端应用程序通过 GCM 与通信的启用 GCM 的服务器：

[![GCM 驻留在客户端应用和应用程序服务器之间](google-cloud-messaging-images/01-server-gcm-app-sml.png)](google-cloud-messaging-images/01-server-gcm-app.png#lightbox)

使用 GCM，应用程序服务器可以向单个设备、 一组设备或多个订阅到主题的设备发送消息。 客户端应用程序可以使用 GCM 来订阅到下游消息从应用程序服务器 （例如，若要接收远程通知）。 此外，GCM 使得客户端应用以将上游消息发送回应用程序服务器。

有关为 GCM 实现应用程序服务器的信息，请参阅[关于 GCM 连接服务器](https://developers.google.com/cloud-messaging/server)。



## <a name="google-cloud-messaging-in-action"></a>Google 云消息传送在操作中

当下游消息从应用程序服务器发送到客户端应用程序时，应用程序服务器将发送到消息*GCM 连接服务器*; GCM 连接服务器，反过来，将消息转发到正在运行客户端应用程序的设备。 可以通过 HTTP 发送消息或[XMPP](https://developers.google.com/cloud-messaging/ccs) （可扩展消息和状态显示协议）。 因为客户端应用程序不始终连接或运行的 GCM 连接服务器排入队列，并将存储消息，将它们发送到客户端应用程序重新连接并变得可用。 同样，GCM 排入队列上游消息从客户端应用程序到应用服务器，如果将应用程序服务器不可用。

GCM 使用下面的凭据来标识应用程序服务器和客户端应用程序，并使用这些凭据授权通过 GCM 消息事务：

-   **API 密钥** &ndash; *API 密钥*允许应用程序服务器访问的 Google 服务;GCM 使用此密钥进行身份验证应用程序服务器。
    可以使用 GCM 服务之前，你必须首先获取的 API 密钥[Google Developer Console](https://console.developers.google.com/)通过创建*项目*。 API 密钥应保持安全;有关如何保护你的 API 密钥的详细信息，请参阅[的最佳做法使用 API 密钥安全地](https://support.google.com/cloud/answer/6310037?hl=en)。

-   **发件人 ID** &ndash; *发件人 ID*授权客户端应用程序到应用服务器&ndash;它是标识允许将消息发送到客户端应用程序的应用程序服务器的唯一编号。
    发件人 ID 也是你的项目编号;注册你的项目时，可以从 Google 开发人员控制台获取的发件人 ID。

-   **注册令牌** &ndash; *注册令牌*是在客户端应用程序上的给定设备的 GCM 标识。 在运行时生成的注册令牌&ndash;时它首先向 GCM 注册的设备上运行时，您的应用程序接收的注册令牌。 注册令牌授权客户端应用程序 （在该特定设备上运行） 的实例以从 GCM 中接收消息。

-   **应用程序 ID** &ndash;的客户端应用 （独立于任何给定的设备） 注册以从 GCM 中接收消息的标识。 在 Android 上，应用程序 ID 是在中记录的包名称**AndroidManifest.xml**，如`com.xamarin.gcmexample`。

[设置了 Google Cloud Messaging](#settingup) （在本指南的后面介绍） 提供了用于创建项目并生成这些凭据的详细的说明。

以下部分介绍当客户端应用与应用程序服务器通过 GCM 通信时，如何使用这些凭据。



### <a name="registration-with-gcm"></a>注册到 GCM

消息传送进行之前，首先必须向 GCM 注册设备上安装的客户端应用程序。 客户端应用程序必须完成下面的关系图中显示的注册步骤：

[![应用注册步骤](google-cloud-messaging-images/02-app-registration-sml.png)](google-cloud-messaging-images/02-app-registration.png#lightbox)

1.  客户端应用程序与 GCM 获取注册令牌，将发件人 ID 传递到 GCM。

2.  GCM 返回到客户端应用程序注册令牌。

3.  客户端应用程序将转发到应用服务器的注册令牌。

应用程序服务器将缓存与客户端应用程序的后续通信的注册令牌。 （可选） 在应用程序服务器可以将确认发送回客户端应用程序，以指示接收到的注册令牌。 发生此握手后，客户端应用程序可以接收消息 （或将消息发送到） 的应用程序服务器。

当客户端应用程序不再想要从应用服务器接收消息时，它可以为要删除的注册令牌的应用程序服务器发送请求。 如果客户端应用程序接收主题消息 （本文稍后所述），它可以取消订阅从主题。
从设备上卸载客户端应用程序，GCM 会检测到这，自动通知要删除的注册令牌的应用程序服务器。

Google[注册客户端应用](https://developers.google.com/cloud-messaging/registration)介绍了注册过程中更多详细信息; 其中介绍了取消注册和取消订阅，并且卸载客户端应用程序时，它描述的注销过程。



### <a name="downstream-messaging"></a>下游消息传送

当应用程序服务器将下游消息发送到客户端应用程序时，它遵循下图中所示的步骤：

[![下游消息存储和转发的关系图](google-cloud-messaging-images/03-downstream-sml.png)](google-cloud-messaging-images/03-downstream.png#lightbox)

1.  应用程序服务器将消息发送到 GCM。

2.  如果客户端设备不可用，在 GCM 服务器以在稍后传输队列中存储消息。

3.  当客户端设备可用时，GCM 将消息发送到该设备上的客户端应用。

4.  客户端应用程序从 GCM 中接收消息，并相应地对其进行处理。 例如，如果消息是远程通知，它是向用户显示。

在此消息传递方案中 （其中应用服务器发送一条消息到单个客户端应用），消息可以是长度最多为 4 kB。

有关详细信息 （包括代码示例） 接收在 Android 上的下游 GCM 消息，请参阅[远程通知](~/android/data-cloud/google-messaging/remote-notifications-with-gcm.md)。


#### <a name="topic-messaging"></a>主题的消息传送

*主题消息传送*是一种类型的下游消息传送应用程序服务器到订阅 （如天气预报） 主题的多个客户端应用程序设备发送一条消息的位置。 主题的消息可以是最大为 2 KB 的长度，并且消息传递主题支持最多 100 万个订阅每个应用。 如果 GCM 要使用仅进行消息传送的主题，客户端应用程序不需要注册令牌发送到应用服务器。 Google[实现主题的消息传送](https://developers.google.com/cloud-messaging/topic-messaging)介绍了如何将消息从应用程序服务器发送到某个特定主题订阅的多个设备。



#### <a name="group-messaging"></a>组消息传递

*组消息传送*是一种类型的下游消息传送应用服务器到属于某个组 （例如，一组属于单个用户的设备） 的多个客户端应用程序设备发送一条消息的位置。 组的消息可以是适用于 iOS 设备的长度最多为 2 KB 和高达 4 KB 的适用于 Android 设备的长度。 一组被限制为最多 20 个成员。 Google[设备组的消息传送](https://developers.google.com/cloud-messaging/notifications)介绍了如何应用服务器可向属于某个组的设备上运行的多个客户端应用程序实例发送一条消息。


### <a name="upstream-messaging"></a>上游消息传送

如果客户端应用程序连接到支持的服务器[XMPP](https://developers.google.com/cloud-messaging/ccs)，它可以将消息发送回应用程序服务器，如以下关系图中所示：

[![上游消息传送的关系图](google-cloud-messaging-images/04-upstream-sml.png)](google-cloud-messaging-images/04-upstream.png#lightbox)

1.  客户端应用程序将消息发送到 GCM XMPP 连接服务器。

2.  如果应用程序服务器断开连接，GCM 服务器会将消息存储到队列中以更高版本转发。

3.  当应用程序服务器重新连接时，GCM 将转发到应用服务器的消息。

4.  应用服务器分析消息来验证身份的客户端应用程序，然后它将"确认"发送到 GCM 确认消息接收。

5.  应用程序服务器处理的消息。

Google[上游消息](https://developers.google.com/cloud-messaging/ccs#upstream)介绍了如何构建 JSON 编码的消息并将其发送到运行 Google 的基于 XMPP 的云连接服务器的应用程序服务器。


<a name="settingup" />

## <a name="setting-up-google-cloud-messaging"></a>Google Cloud Messaging 设置

应用程序中使用 GCM 服务之前，必须首先获取对 Google 的 GCM 服务器的访问的凭据。 以下部分介绍了完成此过程所需的步骤：



### <a name="enable-google-services-for-your-app"></a>启用适用于应用的 Google 服务

1.  登录到[Google 开发人员控制台](https://developers.google.com/mobile/add?platform=android)使用 Google 帐户 （即，你的 gmail 地址），并创建一个新项目。 如果你有现有的项目，选择想要成为启用 GCM 的项目。 在以下示例中，新的项目称为**XamarinGCM**创建：

    [![创建 XamarinGCM 项目](google-cloud-messaging-images/05-create-gcm-app-sml.png)](google-cloud-messaging-images/05-create-gcm-app.png#lightbox)

2.  接下来，输入您的应用程序的包名称 (在此示例中，包名称是**com.xamarin.gcmexample**)，单击**继续选择和配置服务**:

    [![输入包名称](google-cloud-messaging-images/06-package-name-sml.png)](google-cloud-messaging-images/06-package-name.png#lightbox)

    请注意，此包名称也是您的应用程序的应用程序 ID。

3.  **选择并配置服务**部分列出了可以添加到你的应用的 Google 服务。 单击**云消息传送**:

    [![选择云消息传送](google-cloud-messaging-images/07-choose-gcm-service-sml.png)](google-cloud-messaging-images/07-choose-gcm-service.png#lightbox)

4.  接下来，单击**启用 GOOGLE CLOUD MESSAGING**:

    [![启用 Google Cloud Messaging](google-cloud-messaging-images/08-enable-gcm-sml.png)](google-cloud-messaging-images/08-enable-gcm.png#lightbox)

5.  一个**服务器 API 密钥**和一个**发件人 ID**为应用生成。 记录这些值，并且单击**关闭**:

    [![服务器 API 密钥和显示的发件人 ID](google-cloud-messaging-images/09-get-api-key-and-id-sml.png)](google-cloud-messaging-images/09-get-api-key-and-id.png#lightbox)

    保护 API 密钥&ndash;它不是以公共方式使用。 如果 API 密钥已泄漏，未经授权的服务器无法将消息发布到客户端应用程序。
    [有关使用 API 密钥安全最佳实践](https://support.google.com/cloud/answer/6310037?hl=en)提供有用的指导原则来保护你的 API 密钥。



### <a name="view-your-project-settings"></a>查看你的项目设置

可以通过登录到在任何时候查看你的项目设置[Google Cloud Console](https://console.cloud.google.com/)并选择你的项目。 例如，可以查看**发件人 ID**通过在页面顶部的下拉列表中选择你的项目 (在此示例中，该项目称为**XamarinGCM**)。 发件人 ID 是此屏幕截图中所示的项目编号 (此处的发件人 ID 是**9349932736**):

[![查看发件人 ID](google-cloud-messaging-images/10-view-server-id-sml.png)](google-cloud-messaging-images/10-view-server-id.png#lightbox)

若要查看**API 密钥**，单击**API 管理器**，然后单击**凭据**:

[![查看 API 密钥](google-cloud-messaging-images/11-view-credentials-sml.png)](google-cloud-messaging-images/11-view-credentials.png#lightbox)



## <a name="for-further-reading"></a>其他阅读材料

-   Google[注册客户端应用](https://developers.google.com/cloud-messaging/registration)介绍更多详细信息中的客户端注册过程，并提供有关配置自动重试和保持注册状态的同步信息。

-   [RFC 6120](https://tools.ietf.org/html/rfc6120)并[RFC 6121](https://tools.ietf.org/html/rfc6121)解释，并定义可扩展消息传送和协议 (XMPP)。



## <a name="summary"></a>总结

本文章提供 Google Cloud Messaging (GCM) 的概述。 它介绍了各种用于标识和授权应用程序服务器和客户端应用程序之间的消息传送的凭据。 所示的最常见的消息传送方案，并详细为您的应用程序注册到 GCM 使用 GCM 服务的步骤。


## <a name="related-links"></a>相关链接

- [云消息传送](https://developers.google.com/cloud-messaging/)
