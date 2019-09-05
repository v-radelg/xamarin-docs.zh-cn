---
title: 统一 Google Play 服务组件和 NuGet
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 5D962EB4-2CB3-4B7D-9D77-889DEACDAE02
author: conceptdev
ms.author: crdun
ms.date: 05/08/2018
ms.openlocfilehash: 8a7fd77a3f6460f0edbd76f8a4ccf45b32b3ed87
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2019
ms.locfileid: "70284981"
---
# <a name="unifying-google-play-services-components-and-nuget"></a>统一 Google Play 服务组件和 NuGet

## <a name="history"></a>历史记录

其中使用了多个 Google Play Services 组件和 NuGet 包：

- Google Play Services （Froyo）
- Google Play Services （Gingerbread）
- Google Play Services （ICS）
- Google Play Services （JellyBean）
- Google Play Services （KitKat）

Google 实际上只为 Google Play Services 附带了两个 .jar 文件：

- `google-play-services-froyo.jar`
- `google-play-services.jar`

存在差异是因为我们的工具没有正确`aapt.exe`地判断要用于给定应用程序的最大资源 API 级别。 这意味着，如果尝试在较低的 API 级别（如 Gingerbread）上使用 Google Play Services （KitKat）绑定，则会收到编译错误。

## <a name="unifying-google-play-services"></a>统一 Google Play Services

在最新版本的 Xamarin 中，我们现在会告诉`aapt.exe`你要使用的最大资源版本，因此，我们将为此问题提供帮助。

这意味着，无需为 Gingerbread/ICS/JellyBean/KitKat 提供单独的包（不过，由于它是一个完全不同的 .jar 文件，因此仍需要单独的 Froyo 绑定）。

为了使开发人员可以更轻松地进行操作，现在我们将组件和 NuGet 包统一为两个：

- Google Play Services （Froyo）（绑定`google-play-services-froyo.jar`）
- Google Play Services （绑定`google-play-services.jar`）

### <a name="which-one-should-be-used"></a>应使用哪一种？

几乎在所有情况下，都应使用 Google Play Services。 使用（Froyo）包的唯一理由是，如果你积极定位 Froyo。 这种单独的 .jar 文件存在于 Google 中的唯一原因是，由于 Froyo 在这一小部分设备上，它们自行决定停止支持它，因此此 .jar 文件是一个冻结且不受支持的 Google Play Services 快照。

### <a name="note-about-gingerbread"></a>有关 Gingerbread 的说明

默认情况下，Gingerbread 不支持片段，因此，在 Gingerbread 设备上运行时，绑定中的某些类将无法在应用中使用。 类似`MapFragment`的类在 Gingerbread 上不起作用，因此应改用`SupportMapFragment`其支持变量。 开发人员需要知道要使用哪一个。 Google 在其 Google Play Services 文档中记录了这种不兼容性。

### <a name="what-happens-to-the-old-componentsnugets"></a>旧组件/NuGet 会出现什么情况？

由于不再需要这些组件，因此我们已禁用/Delisted 以下组件/Nuget：

- Google Play Services （Gingerbread）
- Google Play Services （JellyBean）
- Google Play Services （KitKat）

现有_Google Play Services （ICS）_ 组件/Nuget 已重命名为 " _Google Play Services_ ，并将一直保持最新状态。 应更新引用其中一个已禁用/Delisted 包的所有项目，以使用此包。

禁用的组件仍然存在，并且应对它们仍在中引用的项目是可恢复的，以避免中断它们。 同样，delisted NuGet 包仍然存在并且可以还原。 它们不会不断更新。
