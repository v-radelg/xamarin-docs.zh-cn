---
title: 将应用更新到 iOS 11
description: 本文档链接到各种指南，这些指南介绍适用于 iOS 开发人员发布的 iOS 11 的新功能。 例如，视觉对象设计更新、应用商店更改和应用图标更新。
ms.prod: xamarin
ms.assetid: EC809504-9CF6-4949-B6EE-36384297E744
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 09/13/2016
ms.openlocfilehash: 81d863b12aa7748acc6876929fb5d6361a20b507
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73032119"
---
# <a name="updating-your-app-to-ios-11"></a>将应用更新到 iOS 11

在 iOS 11 中，Apple 引入了体系结构更新、新的视觉更改和更新的 iTunes 连接过程。 本指南将探讨每个更改，帮助你获取为 iOS 11 更新的 Xamarin iOS 应用。

## <a name="architecture-changesarchitecture-changesmd"></a>[体系结构更改](architecture-changes.md)

在 iOS 11 上，你应该注意的最重要的更改之一是弃用32位对应用的支持，如[Apple 的](https://developer.apple.com/news/?id=06282017b)新闻版本中所述。

本指南将指导你完成更新64位的应用。

## <a name="visual-design-updatesvisual-designmd"></a>[可视化设计更新](visual-design.md)

在 iOS 11 中，Apple 引入了新的视觉更改，包括对导航栏、搜索栏和表视图的更新。 此外，还改进了以使边距和全屏内容更具灵活性。 本指南中介绍了这些更改。

## <a name="app-store-changesapp-store-changesmd"></a>[App Store 更改](app-store-changes.md)

IOS 应用商店已进行了全面的重新设计，这不仅允许用户有效地浏览应用商店，而且还允许作为开发人员向用户推广你的应用程序。 这些促销活动包括更新到产品页的应用内购买和更新。 iOS 11 还添加了有关如何与用户通信的更新、如何添加应用图标，以及如何将应用程序发布到公共。

## <a name="app-icon-updates"></a>应用图标更新

> [!NOTE]
> 应用图标现在应由_资产目录_传递。 

有关使用资产目录的信息，请参阅[应用商店图标](~/ios/app-fundamentals/images-icons/app-store-icon.md)指南。 有关将图标从 info.plist 迁移到资产目录的帮助，请参阅[从信息迁移到资产](~/ios/app-fundamentals/images-icons/app-icons.md)目录指南。

资产目录中的 "必需" 图标命名为 " **App Store** "，其大小应为 1024 x 1024。 Apple 已声明，资产目录中的应用存储图标不能是透明的，也不能包含 alpha 通道。

![资产目录中的应用商店图标位置。](images/image1.png)

## <a name="related-links"></a>相关链接

- [IOS 11 （Apple）中的新增功能](https://developer.apple.com/ios/)
- [更新了应用商店产品页（Apple）](https://developer.apple.com/app-store/product-page/)
- [更新 iOS 11 应用（WWDC）（视频）](https://developer.apple.com/videos/play/wwdc2017/204/)
