---
title: 应用内购买在 Xamarin.iOS
description: 本文档介绍如何销售数字产品和服务使用 StoreKit Api。 它链接到讨论配置、 易耗型产品、 非易耗型产品、 事务、 订阅和的详细信息的指南。
ms.prod: xamarin
ms.assetid: B41929D8-47E4-466D-1F09-6CC3C09C83B2
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: b4165f9e6c3088a41d6bd746b1912a64570c553a
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67832560"
---
# <a name="in-app-purchasing-in-xamarinios"></a>应用内购买在 Xamarin.iOS

iOS 应用程序可以销售数字产品或服务使用 StoreKit – 一组 Api 提供与 Apple 的服务器进行通信的 iOS 用于财务交易与用户通过其 Apple id。 StoreKit Api 的主要工作是与检索产品信息和执行事务 – 没有用户界面组件。 实现应用内购买的应用程序必须构建其自己的用户界面，并跟踪自定义代码，以向用户提供所需的产品或服务的已购买的项。

提供应用内购买功能需要多个步骤：

-  **在应用中配置**– 应用程序的预配配置文件，必须先设置正确。
-  **创建产品**– 必须在 iTunes Connect 门户中创建产品说明和价格。
-  **实现 StoreKit** – StoreKit API 必须实现根据正在销售的产品的类型。
-  **构建用户界面和产品本身**– 必须实现产品，包括跟踪每次购买和备份/还原它们在适当的机制。
-  **监视销售和接收资金**– 使用 iTunes Connect 提供信息来监视销售趋势和跟踪您的收入。

本文档介绍如何完成所有这些步骤，以提供应用内购买使用 Xamarin.iOS。

## <a name="requirements"></a>要求

若要支持应用内购买必须使用 Xcode 7 及更高版本使用 Xamarin.iOS 5.0 或更高版本。

## <a name="contents"></a>内容

* [应用内购买基本知识和配置](~/ios/platform/in-app-purchasing/in-app-purchase-basics-and-configuration.md)

* [StoreKit 概述和检索产品信息](~/ios/platform/in-app-purchasing/store-kit-overview-and-retreiving-product-information.md)

* [购买易耗型产品](~/ios/platform/in-app-purchasing/purchasing-consumable-products.md)

* [购买非易耗型产品](~/ios/platform/in-app-purchasing/purchasing-non-consumable-products.md)

* [事务和验证](~/ios/platform/in-app-purchasing/transactions-and-verification.md)

* [订阅和报告](~/ios/platform/in-app-purchasing/subscriptions-and-reporting.md)

## <a name="summary"></a>总结

本文已引入了应用内购买的概念，阐述了如何配置应用程序以充分利用它并显示使用 Xamarin.iOS 示例。 提到了：

-  **iOS 设置门户**– 使应用程序中的准则购买功能。
-  **在 iTunes Connect** – 产品销售应用程序中配置。
-  **存储工具包**– 用来构建应用内购买功能的类的说明。
-  **购买提高应用程序编码**– 如何构建应用内购买到 Xamarin.iOS 应用程序的示例。
-  **报告**– 概述可通过 iTunes Connect 的统计信息。


## <a name="related-links"></a>相关链接

- [InAppPurchaseSample](https://developer.xamarin.com/samples/StoreKit/)
- [在应用内购买编程指南](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/StoreKitGuide/Introduction.html)
- [iTunes Connect 开发人员指南](https://developer.apple.com/library/ios/documentation/LanguagesUtilities/Conceptual/iTunesConnect_Guide/iTunesConnect_Guide.pdf)
- [存储工具包框架引用](https://developer.apple.com/library/ios/documentation/StoreKit/Reference/StoreKit_Collection/StoreKit_Collection.pdf)
- [应用内购买产品标识符问题与解答](https://developer.apple.com/library/ios/#qa/qa1329/_index.html)
- [应用内购买技术说明](https://developer.apple.com/library/ios/#technotes/tn2259/_index.html)
- [你的第一个应用商店提交](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/Introduction/Introduction.html)
- [应用程序应用商店资源中心](https://developer.apple.com/appstore/index.html)
- [App Store 提交提示](https://developer.apple.com/appstore/resources/submission/tips.html)
- [App Store 审核指南](https://developer.apple.com/appstore/resources/approval/guidelines.html)
- [管理您的应用程序](https://developer.apple.com/appstore/resources/managing/index.html)
