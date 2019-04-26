---
title: 统一 Google Play 服务组件和 NuGet
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 5D962EB4-2CB3-4B7D-9D77-889DEACDAE02
author: asb3993
ms.author: amburns
ms.date: 05/08/2018
ms.openlocfilehash: 3f5c5f75ae1c7a44537afa59ff4a15d54b1df50b
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61357431"
---
# <a name="unifying-google-play-services-components-and-nuget"></a>统一 Google Play 服务组件和 NuGet

## <a name="history"></a>历史记录

存在用于将多个 Google Play Services 组件和 NuGet 包：

-   Google Play 服务 (Froyo)
-   Google Play 服务 (Gingerbread)
-   Google Play 服务 (ICS)
-   Google Play 服务 (JellyBean)
-   Google Play 服务 (KitKat)

Google 实际上只提供两个.jar 文件的 Google Play Services:

-   `google-play-services-froyo.jar`
-   `google-play-services.jar`

差异存在，因为未正确地告诉我们工具`aapt.exe`API 级别的最大资源是要用于给定应用程序。 这意味着，我们收到编译错误，如果我们尝试在 Gingerbread 等较低的 API 级别上使用 Google Play Services (KitKat) 绑定。

## <a name="unifying-google-play-services"></a>统一 Google Play 服务

在较新版本的 Xamarin.Android 中，我们现在告诉`aapt.exe`若要使用，因此这一问题消失为我们哪些资源的最大版本。

这意味着没有实际理由 Gingerbread/ICS/JellyBean/KitKat （但是我们仍需要单独的绑定适用于 Froyo 因为它完全是不同的.jar 文件） 的具有单独的包。

为了简化操作面向开发人员，现在，统一我们的组件和 NuGet 包复制到以下两个：

-   Google Play 服务 (Froyo) (将绑定`google-play-services-froyo.jar`)
-   Google Play 服务 (将绑定`google-play-services.jar`)

### <a name="which-one-should-be-used"></a>应使用哪一个？

在大多数情况下，应使用 Google Play Services。 若要使用 (Froyo) 包的唯一原因是如果您的主动目标 Froyo。 此单独的.jar 文件存在来自 Google 的唯一原因是由于 Froyo 是针对此类一小部分的设备，他们自己决定停止支持，因此此.jar 文件的 Google Play 服务已冻结，不受支持的快照。

### <a name="note-about-gingerbread"></a>请注意有关 Gingerbread

Gingerbread 不具有默认情况下，支持的片段，正因为如此，一些绑定中的类将无法使用运行时在 Gingerbread 设备上的应用程序中。 类等`MapFragment`不能用于 Gingerbread，并应改为使用其支持变体`SupportMapFragment`。 由开发人员知道要使用它。 此不兼容性由 Google 记录在其 Google Play Services 文档中。

### <a name="what-happens-to-the-old-componentsnugets"></a>旧组件/NuGet 会发生什么情况？

由于不再需要我们必须禁用/Delisted 以下组件/Nuget:

-   Google Play 服务 (Gingerbread)
-   Google Play 服务 (JellyBean)
-   Google Play 服务 (KitKat)

现有_Google Play 服务 (ICS)_ 已更名为组件/Nuget _Google Play Services_和将保留最新的继续。 应更新引用其中一个已禁用/Delisted 包的所有项目，以这种方法。

已禁用的组件仍存在，并且应该可还原的已为它们仍然引用中，若要避免中断它们的项目。 同样 delisted 的 NuGet 包仍存在，并且可恢复。 它们将不会更新今后。
