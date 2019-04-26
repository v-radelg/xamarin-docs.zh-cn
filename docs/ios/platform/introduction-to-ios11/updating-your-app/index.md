---
title: 将应用更新到 iOS 11
description: 本文档所链接到各种指南描述 Xamarin.iOS 开发者使用的 iOS 11 版本提供的新功能。 例如，App Store 更改可视化设计更新，并应用图标更新。
ms.prod: xamarin
ms.assetid: EC809504-9CF6-4949-B6EE-36384297E744
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 09/13/2016
ms.openlocfilehash: 3193f27c30080a4335abfe969acb3c8b33516469
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61386335"
---
# <a name="updating-your-app-to-ios-11"></a>将应用更新到 iOS 11

在 iOS 11 中，Apple 已引入了体系结构更新、 新的可视更改和已更新的 iTunes Connect 过程。 本指南介绍这些更改，从而帮助你更新适用于 iOS 11 您 Xamarin.iOS 应用程序。

## <a name="architecture-changesarchitecture-changesmd"></a>[体系结构更改](architecture-changes.md)

应注意的 ios 11 的最大变化之一就是不推荐使用的应用程序，如中所述的 32 位支持[Apple 的](https://developer.apple.com/news/?id=06282017b)按释放。

本指南将引导您完成更新适用于 64 位应用。

## <a name="visual-design-updatesvisual-designmd"></a>[可视化设计更新](visual-design.md)

在 iOS 11 中，Apple 引入了新的可视更改，包括对导航栏、 搜索栏和表视图的更新。 除了已进行改进，允许通过边距和全屏幕内容进行更大的灵活性。 本指南中介绍了这些更改。

## <a name="app-store-changesapp-store-changesmd"></a>[App Store 更改](app-store-changes.md)

IOS 应用商店具有经过完全重新设计，这不仅允许用户高效地导航应用商店中，而且还允许您，作为开发人员，以提升你的应用的用户。 这些升级包括到应用内购买的更新和更新产品页。 iOS 11 还添加了有关如何与用户进行通信、 如何添加应用程序的图标，以及如何将应用发布到公共更新。

## <a name="app-icon-updates"></a>应用图标更新

> [!NOTE]
> 应用图标现在应传递由_资产目录_。 

有关使用资产目录信息，请参阅[App Store 图标](~/ios/app-fundamentals/images-icons/app-store-icon.md)指南。 有关迁移帮助图标从 Info.plist 到资产目录，请参阅[Info.plist 从迁移到资产目录](~/ios/app-fundamentals/images-icons/app-icons.md)指南。

资产目录中所需的图标名为**App Store**和应为 1024 x 1024 的大小。 Apple 已声明，资产目录中的应用存储图标不能是透明的，也不能包含 alpha 通道。

![应用存储图标位置资产目录中。](images/image1.png)

## <a name="related-links"></a>相关链接

- [What's New iOS 11 (Apple) 中](https://developer.apple.com/ios/)
- [更新的应用商店产品页 (Apple)](https://developer.apple.com/app-store/product-page/)
- [将应用更新适用于 iOS 11 (WWDC) （视频）](https://developer.apple.com/videos/play/wwdc2017/204/)
