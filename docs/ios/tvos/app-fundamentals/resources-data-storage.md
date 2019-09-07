---
title: Xamarin 中的 tvOS 资源和数据存储
description: 本文介绍如何在使用 Xamarin 生成的 tvOS 应用中处理资源和永久性数据存储。 它讨论 iCloud 数据存储和按需资源。
ms.prod: xamarin
ms.assetid: C56B5046-D2C0-4B63-9CE0-ADAA0EFD368A
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/16/2017
ms.openlocfilehash: 4b50ba09488f1f052f3e41302a42c691089727ff
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70769649"
---
# <a name="tvos-resources-and-data-storage-in-xamarin"></a>Xamarin 中的 tvOS 资源和数据存储

_本文介绍如何在 tvOS 应用程序中使用资源和永久性数据存储。_

<a name="tvOS-Resource-Limitations" />

## <a name="tvos-resource-limitations"></a>tvOS 资源限制

与 iOS 设备不同，新的 Apple 电视为 tvOS 应用或数据提供了非常有限的持久本地存储。 对于非常小的项目（如用户首选项），tvOS 应用仍有权访问`NSUserDefaults` [500 KB 的数据](https://forums.developer.apple.com/message/50696#50696)。 但是，如果 tvOS 应用需要保存更多数量的信息，则它必须从[iCloud](#iCloud-Data-Storage)存储和检索数据。

此外，tvOS 将 Apple TV 应用的大小限制为200MB。 如果你的应用程序需要超出此大小的资源，则需要使用按[需资源](#On-Demand-Resources)（最多为一个2gb）打包并加载这些资源。 考虑到这些限制，很重要的一点是，正确地下载其他资产，为应用程序的用户提供最佳体验。 有关详细信息，请参阅 Apple[点播资源指南](https://developer.apple.com/library/prerelease/tvos/documentation/FileManagement/Conceptual/On_Demand_Resources_Guide/index.html#//apple_ref/doc/uid/TP40015083)。

<a name="Non-Persistent-Downloads" />

## <a name="non-persistent-downloads"></a>非持久下载

每个 tvOS 应用都提供了一个临时缓存目录，其其他资源和资产会下载到该目录。 只要应用仍在运行，此目录就会被保留。 但是，由于 Apple TV 需要为其他应用或系统使用腾出空间，因此可以删除此缓存。

因此，在下次启动应用程序时，应用程序不能依赖于先前下载的内容。 你的 tvOS 应用应始终检查是否存在所需的资源，并根据需要下载这些资源。

> [!IMPORTANT]
> 尽管你能够根据需要下载其他资产和资源，但 Apple 会警告你使用应用缓存中的所有空间，因为这可能会导致不可预知的结果。

<a name="Managing-Resources" />

## <a name="managing-resources"></a>管理资源

如上所述，由于 tvOS 应用程序可使用有限的非持久存储信息，因此这些限制要求仔细规划，为 Xamarin. tvOS 应用创建极佳的用户体验。

<a name="iCloud-Data-Storage" />

### <a name="icloud-data-storage"></a>iCloud 数据存储

由于 Apple TV 上的存储空间受到限制，因此，并不只是有非常有限的持久性本地存储，你的应用程序无法保证其以前下载的任何信息在下一次运行时都将可用。

因此，tvOS 应用必须在 iCloud 数据存储中存储任何用户数据。 Apple 为你的 tvOS 应用提供两个基于 iCloud 的数据存储选项：

- **ICloud 键值存储（KVS）** -对于您的应用程序可能需要的小部分信息（如用户首选项），可以使用 ICloud KVS 存储。 iCloud KVS 数据自动同步到云，并且所有用户的设备都运行相同的应用。 有关详细信息，请参阅 icloud 文档[简介](~/ios/data-cloud/introduction-to-icloud.md)的 "[关键值存储](~/ios/data-cloud/introduction-to-icloud.md)" 部分，或者查看[Icloud 文档中的关键值数据设计](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/iCloudDesignGuide/Chapters/DesigningForKey-ValueDataIniCloud.html#//apple_ref/doc/uid/TP40012094-CH7)Apple。
- **CloudKit** -若要存储较大的信息片段（大于1mb），请使用 Apple 的 CloudKit 框架。 与 iCloud KVS 存储不同，CloudKit 数据可在应用的所有用户（以及专用于单个用户）之间共享。 有关详细信息，请参阅 CloudKit 文档或 Apple 的[CloudKit 快速入门](https://developer.apple.com/library/prerelease/tvos/documentation/DataManagement/Conceptual/CloudKitQuickStart/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014987)[简介](~/ios/data-cloud/intro-to-cloudkit.md)。

> [!IMPORTANT]
> Apple [提供工具](https://developer.apple.com/support/allowing-users-to-manage-data/)，用于帮助开发人员正确处理欧盟一般数据保护条例 (GDPR)。

<a name="On-Demand-Resources" />

### <a name="on-demand-resources"></a>点播资源

按需资源提供应用程序的内容和资产（独立于应用程序捆绑），托管在应用商店，并根据应用程序的需要下载。 使用按需资源可以提供多达2GB 的数据。 它们允许较小的初始应用程序下载（tvOS 应用限制为最多200MB），同时还能根据需要提供丰富的资产。

当 tvOS 应用请求按需资源时，系统会自动管理将此内容下载和存储到应用的缓存目录。 应用必须等待下载此内容并使其可用，然后才能继续。

在应用程序的多个启动过程中，这些资源可能会继续缓存在 Apple 电视上，从而加快启动周期。 但是，在下次启动应用程序时，应用程序不能依赖于先前下载的内容。 有关更多详细信息，请参阅上面的[非永久性下载](#Non-Persistent-Downloads)部分。

使用 Xcode 创建与 "提供资源" 标记相关联的相关内容（例如，游戏级别2的所有资产）的捆绑。 稍后，你的应用将通过指定此资源标记请求按需资源。 应用应向用户显示用户界面，指出正在下载内容。 有关详细信息，请参阅 Apple[点播资源指南](https://developer.apple.com/library/prerelease/tvos/documentation/FileManagement/Conceptual/On_Demand_Resources_Guide/index.html#//apple_ref/doc/uid/TP40015083)。

> [!IMPORTANT]
> 请注意，在应用必须下载按需资源的时间和单个下载的大小之间，应采取适当的平衡。 如果游戏一直中断以下载新内容，或者如果单个下载花费了太长时间，则用户可能会对应用程序造成困扰。

<a name="Summary" />

## <a name="summary"></a>总结

本文介绍了 tvOS 系统在 tvOS 应用上放置的大小、资源和数据存储限制。 它提供了解决这些限制的选项，并为应用程序创建了极佳的用户体验。

## <a name="related-links"></a>相关链接

- [tvOS 示例](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+tvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人体学接口指南](https://developer.apple.com/tvos/human-interface-guidelines/)
- [TvOS 应用编程指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
