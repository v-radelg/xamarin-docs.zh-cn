---
title: Xamarin.iOS 应用分发概述
description: 本文档概述适用于 Xamarin.iOS 应用程序的分发技巧，并引出有关该主题的更详细文档。
ms.prod: xamarin
ms.assetid: 341D36DB-BB07-FA94-BCC9-5F8C0B18C179
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/18/2017
ms.openlocfilehash: 20126849027f735e9ecd3599c290b4e7a57f837e
ms.sourcegitcommit: ec62e2624295aa502ec35ac782031d61d61c3aaa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/11/2020
ms.locfileid: "75886575"
---
# <a name="xamarinios-app-distribution-overview"></a>Xamarin.iOS 应用分发概述

_本文档概述适用于 Xamarin.iOS 应用程序的分发技巧，并引出有关该主题的更详细文档。_

Xamarin.iOS 应用开发完成后，软件开发生命周期中的下一步是将应用分发给用户，如下图中突出显示部分所示：

[![iOS 应用开发完成后，下一步是将应用分发给用户，如此图中突出显示部分所示](images/publishingdiagram.png)](images/publishingdiagram.png#lightbox)

Apple 提供以下方式分发 iOS 应用程序：

- [**App Store**](#app-store-distribution)
- [**内部（企业）** ](#in-house-distribution)
- [**即席分发**](#ad-hoc-distribution)
- [**适用于企业的自定义应用**](#custom-apps-for-business)

所有这些情况都要求使用相应的配置文件  预配应用程序。 配置文件是一种文件，其中包含了代码签名信息，以及应用程序标识和预期分发机制。 对于非 App Store 分发，该文件还包含可部署应用的设备的相关信息。

## <a name="app-store-distribution"></a>App Store 分发

> [!IMPORTANT]
> Apple [已表明](https://developer.apple.com/ios/submit/)，从 2019 年 3 月开始，提交到 App Store 的所有应用和更新必须是使用 iOS 12.1 SDK 或更高版本构建的，包含在 Xcode 10.1 或更高版本中。
> 应用还应支持 iPhone XS 和 12.9 英寸 iPad Pro 屏幕大小。

这是将 iOS 应用程序分发给 iOS 设备上的使用者的主要方式。 提交到 App Store 的所有应用都需要 Apple 审批。

应用通过名为 iTunes Connect  的门户提交到 App Store。 [在 iTunes Connect 中配置应用](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md)指南提供有关如何设置并使用此门户准备 Xamarin.iOS 应用，以在 App Store 中发布的详细信息。

务必注意，只有属于 **Apple 开发人员计划**的开发人员有权访问 iTunes Connect。 **Apple 开发人员企业计划**成员没有访问权限。

有关详细信息，请访问 [App Store 分发](~/ios/deploy-test/app-distribution/app-store-distribution/index.md)指南。

## <a name="in-house-distribution"></a>内部分发

In-House 分发（有时称为企业分发  ）允许 **Apple 开发人员企业计划**成员在内部向同一组织内的其他成员分发应用。 In-House 分发的优势在于无需 App Store 审核，且没有可安装应用程序的设备数量限制。 但是，值得注意的是，**Apple 开发人员企业计划**成员**没有**权限访问 iTunes Connect，因此许可证持有人负责分发应用。

有关设置以及如何内部分发应用程序的详细信息，请参阅[内部分发指南](~/ios/deploy-test/app-distribution/in-house-distribution.md)。

## <a name="ad-hoc-distribution"></a>即席分发

Xamarin.iOS 应用程序可通过 Ad Hoc 分发进行用户测试，Ad Hoc 分发可在 **Apple 开发人员计划**和 **Apple 开发人员企业计划**中获取，它允许测试最多 100 台 iOS 设备。 无法选择 iTunes Connect 时，Ad Hoc 分发的最好用在公司内分发。

有关设置以及如何即席分发应用程序的详细信息，请参阅[即席分发指南](~/ios/deploy-test/app-distribution/ad-hoc-distribution.md)。

## <a name="custom-apps-for-business"></a>适用于企业的自定义应用

Apple 允许将应用[自定义分发](https://developer.apple.com/business/custom-apps/)到企业和教育机构。 有关信息，请参阅 [Apple Business Manager 用户指南](https://support.apple.com/guide/apple-business-manager/welcome/web)。

## <a name="related-links"></a>相关链接

- [App Store 分发](~/ios/deploy-test/app-distribution/app-store-distribution/index.md)
- [在 iTunes Connect 中配置应用](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md)
- [发布到 App Store](~/ios/deploy-test/app-distribution/app-store-distribution/publishing-to-the-app-store.md)
- [内部分发](~/ios/deploy-test/app-distribution/in-house-distribution.md)
- [即席分发](~/ios/deploy-test/app-distribution/ad-hoc-distribution.md)
- [iTunesMetadata.plist 文件](~/ios/deploy-test/app-distribution/itunesmetadata.md)
- [IPA 支持](~/ios/deploy-test/app-distribution/ipa-support.md)
- [疑难解答](~/ios/deploy-test/troubleshooting.md)
