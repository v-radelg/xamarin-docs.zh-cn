---
title: tvOS 资源和 Xamarin 中的数据存储
description: 本文介绍如何使用资源，并使用 Xamarin 生成的 tvOS 应用程序中的持久数据存储。 它讨论了 iCloud 数据存储和按需资源。
ms.prod: xamarin
ms.assetid: C56B5046-D2C0-4B63-9CE0-ADAA0EFD368A
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: 8f8ecc115738fb97f71b4ea6b2cdcc5c2714372d
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61208518"
---
# <a name="tvos-resources-and-data-storage-in-xamarin"></a>tvOS 资源和 Xamarin 中的数据存储

_本文介绍如何使用资源和 Xamarin.tvOS 应用中的持久数据存储。_

<a name="tvOS-Resource-Limitations" />

## <a name="tvos-resource-limitations"></a>tvOS 资源限制

与 iOS 设备，新的 Apple TV 的 tvOS 应用或数据提供极其有限的持久的本地存储。 对于非常小的项 （如用户首选项），你的 tvOS 应用仍有权访问`NSUserDefaults`与[500 KB 的数据限制](https://forums.developer.apple.com/message/50696#50696)。 但是，如果您 Xamarin.tvOS 的应用程序需要保留更大容量的信息，它必须存储和检索该数据免遭[iCloud](#iCloud-Data-Storage)。

此外，tvOS 限制为 200 MB 的 Apple TV 应用的大小。 如果您的应用程序需要超过此大小的资源，它们将需要进行打包和使用加载[按需资源](#On-Demand-Resources)（最多额外的 2 GB)。 给定这些限制，很重要的正确时间下载其他资产的应用程序的用户提供最佳体验。 有关详细信息，请参阅 Apple[按需资源指南](https://developer.apple.com/library/prerelease/tvos/documentation/FileManagement/Conceptual/On_Demand_Resources_Guide/index.html#//apple_ref/doc/uid/TP40015083)。

<a name="Non-Persistent-Downloads" />

## <a name="non-persistent-downloads"></a>非持久的下载

每个 tvOS 应用提供其他资源和资产下载到一个临时缓存目录。 将保留此目录，只要该应用程序仍在运行。 但是，如 Apple TV 需要释放空间以进行其他应用程序或系统使用情况，可以删除此缓存。

因此，您的应用程序不能依赖于以前下载的内容不可启动下一次。 Xamarin.tvOS 应用程序应始终检查所需的资源存在，并根据需要下载它们。

> [!IMPORTANT]
> 而必须下载其他资产和资源所需的功能，Apple 会发出警告针对使用的所有应用程序的缓存中空间中，因为它会导致不可预知的结果。




<a name="Managing-Resources" />

## <a name="managing-resources"></a>管理资源

如上所述，由于受限，而非持久性存储的信息适用于 tvOS 应用这些限制需要认真计划，以创建 Xamarin.tvOS 应用的出色用户体验。

<a name="iCloud-Data-Storage" />

### <a name="icloud-data-storage"></a>iCloud 数据存储

因为 Apple TV 上的存储空间限制，不只是那里非常有限的持久的本地存储，你的应用程序不能保证下一次运行时，将提供它以前下载的任何信息。

因此，Xamarin.tvOS 应用必须 iCloud 数据存储区中存储任何用户数据。 Apple 提供对 tvOS 应用的两个 iCloud 基于数据存储选项：

- **iCloud 键值存储 (KVS)** -细节，您的应用程序可能会要求 （如用户首选项），可以使用 iCloud KVS 存储信息 (小于 1 MB)。 iCloud KVS 数据自动同步到云，并且所有运行同一应用程序的用户的设备。 有关详细信息请参阅[键-值存储](~/ios/data-cloud/introduction-to-icloud.md)一部分我们[iCloud 简介](~/ios/data-cloud/introduction-to-icloud.md)文档或 Apple[在 iCloud 中设计的键-值数据](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/iCloudDesignGuide/Chapters/DesigningForKey-ValueDataIniCloud.html#//apple_ref/doc/uid/TP40012094-CH7)文档。
- **CloudKit** -对于更大的信息的部分 （超过 1 MB），存储使用 CloudKit 的 Apple 框架。 与 iCloud KVS 存储，CloudKit 数据可以共享您的应用程序 （同时作为专用于单个用户） 的所有用户之间。 窗体的详细信息，请参阅我们[CloudKit 简介](~/ios/data-cloud/intro-to-cloudkit.md)文档或 Apple [CloudKit 快速启动](https://developer.apple.com/library/prerelease/tvos/documentation/DataManagement/Conceptual/CloudKitQuickStart/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014987)。

> [!IMPORTANT]
> Apple [提供工具](https://developer.apple.com/support/allowing-users-to-manage-data/)，用于帮助开发人员正确处理欧盟一般数据保护条例 (GDPR)。

<a name="On-Demand-Resources" />

### <a name="on-demand-resources"></a>按需资源

按需资源提供应用程序内容和资产 （独立于应用程序捆绑包） 的 App Store 上托管，并下载所需的应用程序。 最多额外的 2 GB 的数据都可以提供使用按需资源。 它们使较小的初始应用下载 （tvOS 应用限于最多 200 MB），同时仍提供所需的丰富资产。

当 tvOS 应用请求按需资源时，系统将自动管理下载并存储到应用程序的缓存目录的此内容。 您的应用程序必须等待此内容才能下载并可，然后才能继续。

这些资源可能会继续在 Apple TV 中在多个启动您的应用程序，从而加快发布周期整个缓存。 但是，您的应用程序不能依赖于在下一次启动任何以前下载的内容。 请参阅[Non-persistent 下载](#Non-Persistent-Downloads)上面部分的更多详细信息。

使用 Xcode 创建与给定的资源标记相关联的相关内容 （如游戏级别 2 的所有资产） 的捆绑包。 稍后您的应用程序将通过指定此资源标记请求按需资源。 您的应用程序应显示 UI 向用户表明该内容正在下载。 有关详细信息，请参阅 Apple[按需资源指南](https://developer.apple.com/library/prerelease/tvos/documentation/FileManagement/Conceptual/On_Demand_Resources_Guide/index.html#//apple_ref/doc/uid/TP40015083)。

> [!IMPORTANT]
> 若要达到最佳平衡的应用程序必须下载按需资源的次数和单独的下载大小之间，应格外小心。 如果不断地中断游戏玩法下载新内容或下载一次需要很长时间，用户可能会感到不便与你的应用。




<a name="Summary" />

## <a name="summary"></a>总结

本文只讨论了由 tvOS 系统放在 Xamarin.tvOS 应用上的大小、 资源和数据存储限制。 它提供了选项可以解决这些限制和建议，可创建出色的用户体验为您的应用程序。



## <a name="related-links"></a>相关链接

- [tvOS 示例](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人机接口指南](https://developer.apple.com/tvos/human-interface-guidelines/)
- [适用于 tvOS 应用编程指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
