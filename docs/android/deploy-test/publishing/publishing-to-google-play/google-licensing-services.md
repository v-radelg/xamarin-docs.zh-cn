---
title: Google 授权服务
ms.prod: xamarin
ms.assetid: E96BDCC3-454A-A797-5819-905E2BB1AC41
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 12/20/2017
ms.openlocfilehash: 47b9d76cb0b7b2fc48fe83d291479f7d1ed00eb8
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/16/2019
ms.locfileid: "69525213"
---
# <a name="google-licensing-services"></a>Google 授权服务

在 Google Play 推出之前，Android 应用程序依靠 Google Market 提供的旧版复制保护功能，确保只有经过授权的用户才可在其设备上运行应用程序。 复制保护机制具有局限，是不太理想的应用程序保护解决方案。

Google 授权取代了这种旧的复制保护机制。
Google 授权是一款灵活安全且基于网络的服务，Android 应用程序可查询该服务，确定某应用程序是否可在给定设备上运行。

Google 授权非常灵活，Android 应用程序可完全掌控何时检查许可证、多久检查许可证一次以及如何处理来自授权服务器的响应。

Google 授权安全可靠，每个响应均使用 RSA 键对进行签名，且该键对在 Google Play 服务器和应用程序之间专属共享。 Google Play 为开发人员提供了公钥，该公钥嵌入到 Android 应用程序中且可用于验证响应的身份。 Google Play 服务器将内部保存私钥。

采用了 Google 授权的应用程序将请求使用设备上 Google Play 托管的服务。 然后，Google Play 将此请求发送到 Google 授权服务器，后者使用许可状态进行响应： 

[![服务器授权工作流示意图](google-licensing-services-images/gp-licensing-service-overview.png)](google-licensing-services-images/gp-licensing-service-overview.png#lightbox)

上图描述了此工作流： 

- 应用程序会提供包名称、用于验证服务器响应的 nonce（加密验证器），以及可异步处理响应的回调  。 

- Google Play 会提供 Google 帐户等信息以及设备本身的信息（如 IMSI 编号）。 

Google 授权服务还是 APK 扩展文件的关键组件（将在本文档后面讨论）。 APK 扩展文件利用 Google 授权服务获取要下载的扩展文件的 URL。


## <a name="requirements"></a>要求

只有通过 Google Play 购买的应用程序才可享受 Google 授权服务的权益。 即使设备上未安装 Google Play，使用授权服务的应用程序仍将在该设备上正常运行。

Google Play 需连接 Internet 才可正常使用。 应用程序可缓存许可证，应对设备无权访问 Google Play 授权服务器的情况。

仅当使用 APK 扩展文件时，免费应用程序才需要 Google 授权。
