---
title: Xamarin.iOS 应用分发概述
description: 本文档概述适用于 Xamarin.iOS 应用程序的分发技巧，并引出有关该主题的更详细文档。
ms.prod: xamarin
ms.assetid: 341D36DB-BB07-FA94-BCC9-5F8C0B18C179
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/18/2017
ms.openlocfilehash: 7a261d1fc86fac16cf48575616359b083daebf47
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70756542"
---
# <a name="xamarinios-app-distribution-overview"></a>Xamarin.iOS 应用分发概述

_本文档概述适用于 Xamarin.iOS 应用程序的分发技巧，并引出有关该主题的更详细文档。_

Xamarin.iOS 应用开发完成后，软件开发生命周期中的下一步是将应用分发给用户，如下图中突出显示部分所示：

[![](images/publishingdiagram.png "iOS 应用开发完成后，下一步是将应用分发给用户，如此图中突出显示部分所示")](images/publishingdiagram.png#lightbox)

Apple 提供以下方式分发 iOS 应用程序，它们均受 Xamarin.iOS 支持：

1. [**App Store**](#App_Store_Distribution)
2. [**In-House（企业）** ](#In-House_Distribution)
3. [**Ad Hoc**](#Ad_Hoc_Distribution)

所有这些情况都要求使用相应的配置文件  预配应用程序。 配置文件是一种文件，其中包含了代码签名信息，以及应用程序标识和预期分发机制。 对于非 App Store 分发，该文件还包含可部署应用的设备的相关信息。

<a name="App_Store_Distribution"/>

## <a name="app-store-distribution"></a>App Store 分发

> [!IMPORTANT]
> Apple [已表明](https://developer.apple.com/ios/submit/)，从 2019 年 3 月开始，提交到 App Store 的所有应用和更新必须是使用 iOS 12.1 SDK 或更高版本构建的，包含在 Xcode 10.1 或更高版本中。
> 应用还应支持 iPhone XS 和 12.9 英寸 iPad Pro 屏幕大小。

这是将 iOS 应用程序分发给 iOS 设备上的使用者的主要方式。 提交到 App Store 的所有应用都需要 Apple 审批。

应用通过名为 iTunes Connect  的门户提交到 App Store。 [在 iTunes Connect 中配置应用](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md)指南提供有关如何设置并使用此门户准备 Xamarin.iOS 应用，以在 App Store 中发布的详细信息。

务必注意，只有属于 **Apple 开发人员计划**的开发人员有权访问 iTunes Connect。 **Apple 开发人员企业计划**成员没有访问权限。

有关详细信息，请访问 [App Store 分发](~/ios/deploy-test/app-distribution/app-store-distribution/index.md)指南。

<a name="In-House_Distribution"/>

## <a name="in-house-distribution"></a>In-House 分发

In-House 分发（有时称为企业分发  ）允许 **Apple 开发人员企业计划**成员在内部向同一组织内的其他成员分发应用。 In-House 分发的优势在于无需 App Store 审核，且没有可安装应用程序的设备数量限制。 但是，值得注意的是，**Apple 开发人员企业计划**成员**没有**权限访问 iTunes Connect，因此许可证持有人负责分发应用。

有关设置以及如何 In-House 分发应用程序的详细信息，请参阅[In-House 分发指南](~/ios/deploy-test/app-distribution/in-house-distribution.md)。

<a name="Ad_Hoc_Distribution"/>

## <a name="ad-hoc-distribution"></a>Ad Hoc 分发

Xamarin.iOS 应用程序可通过 Ad Hoc 分发进行用户测试，Ad Hoc 分发可在 **Apple 开发人员计划**和 **Apple 开发人员企业计划**中获取，它允许测试最多 100 台 iOS 设备。 无法选择 iTunes Connect 时，Ad Hoc 分发的最好用在公司内分发。

有关设置以及如何 Ad Hoc 分发应用程序的详细信息，请参阅[Ad Hoc 分发指南](~/ios/deploy-test/app-distribution/ad-hoc-distribution.md)。

## <a name="summary"></a>总结

本文简要概述了适用于 Xamarin.iOS 应用程序的分发机制。 它介绍了 iTunes App Store、Ad Hoc 和 In-House 部署，并提供更多详细信息的相关链接。

## <a name="related-links"></a>相关链接

- [App Store 分发](~/ios/deploy-test/app-distribution/app-store-distribution/index.md)
- [在 iTunes Connect 中配置应用](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md)
- [发布到 App Store](~/ios/deploy-test/app-distribution/app-store-distribution/publishing-to-the-app-store.md)
- [内部分发](~/ios/deploy-test/app-distribution/in-house-distribution.md)
- [Ad Hoc 分发](~/ios/deploy-test/app-distribution/ad-hoc-distribution.md)
- [iTunesMetadata.plist 文件](~/ios/deploy-test/app-distribution/itunesmetadata.md)
- [IPA 支持](~/ios/deploy-test/app-distribution/ipa-support.md)
- [疑难解答](~/ios/deploy-test/troubleshooting.md)
