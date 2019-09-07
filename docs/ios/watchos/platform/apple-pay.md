---
title: Xamarin 中的 watchOS 上的 Apple Pay
description: 本文介绍了 Apple 在 watchOS 3 中所做的增强功能，以及如何在 Apple Watch 中实现 Apple Pay。
ms.prod: xamarin
ms.assetid: 32FF5D21-C252-485D-83AC-A7E592237962
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/17/2017
ms.openlocfilehash: 579f2afd8e52251973900f35ef91ac086adf7603
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70768650"
---
# <a name="apple-pay-on-watchos-in-xamarin"></a>Xamarin 中的 watchOS 上的 Apple Pay

Apple 在 watchOS 3 中对 Apple Pay 进行了几项增强，增加了对应用中付款的支持。 这样，用户就可以安全地提供支付和联系信息，以便直接从 Apple Watch 为实际货物和服务付费。

## <a name="about-apple-pay-enhancements"></a>关于 Apple Pay 增强功能

如上所述，Apple 已对 watchOS 3 中的 Apple Pay 进行了几项增强，使你可以通过安全的支付和联系信息直接从 Apple Watch 为物理商品和服务付费。 这些增强功能由对 PassKit 框架的修改提供。

使用 iOS 10 和 watchOS 3，添加了几个新的 Api，它们适用于 iOS 和 watchOS 以支持动态支付网络和新的沙箱测试环境。

## <a name="passkit-framework-enhancements"></a>PassKit 框架增强功能

在 iOS 10 中，已将 PassKit 框架扩展为支持外的`UIKit` Apple Pay，以允许卡颁发商在其应用中提供其卡。 

### <a name="supporting-apple-pay-outside-of-uikit"></a>UIKit 之外的支持 Apple Pay

通过使用[PKPaymentAuthorizationController](https://developer.apple.com/reference/passkit/pkpaymentauthorizationcontroller)和[PKPaymentAuthorixationControllerDelegate](https://developer.apple.com/reference/passkit/pkpaymentauthorizationcontrollerdelegate)，应用可以支持[PKPaymentAuthorizationViewController](https://developer.apple.com/reference/passkit/pkpaymentauthorizationviewcontroller)提供的同一功能，而无需使用 UIKit。 尽管此新 API 是支持 Apple Watch 上的 Apple Pay （也在特定意向中），但在其他情况下（如现有应用）是可选的。 但是，Apple 建议尽快迁移到新的 API，以便在所有开发人员的应用程序中提供广泛的 Apple Pay 支持，只需要一个基本代码。 有关意向和 Siri 集成的详细信息，请参阅[SiriKit 文档简介](~/ios/platform/sirikit/index.md)。

### <a name="presenting-issuer-cards-from-within-apps"></a>在应用内展示颁发者卡

使用 iOS 10 和 watchOS 3，PassKit 框架中已添加了新功能，使卡颁发商可以在自己的应用中提供其支付卡。 开发人员可以将`PKPaymentButtonTypeInStore` UIButton 添加到应用程序的用户界面，该界面将显示卡片 Apple Pay 按钮。

还可以使用[PKPassLibrary](https://developer.apple.com/reference/passkit/pkpasslibrary)类的方法以编程方式显示卡。`PresentPaymentPass`

## <a name="new-payment-network-support"></a>新的付款网络支持

IOS 10 和 watchOS 3 的新增功能，应用程序可在无需修改、重新编译应用程序并将其重新提交到应用商店时自动支持新的付款网络。

`PKPaymentNetwork`类的新的[AvailableNetworks](https://developer.apple.com/reference/passkit/pkpaymentrequest/1833288-availablenetworks)方法允许应用程序在运行时发现用户设备上的可用网络。 此外， [SupportedNetworks](https://developer.apple.com/reference/passkit/pkpaymentrequest/1619329-supportednetworks)属性已展开，以将付款提供程序的名称作为参数。 使用这些方法，应用程序可以自动支持支付提供商支持的任何网络。

有关详细信息，请参阅我们的[Apple Pay 配置](~/ios/platform/apple-pay.md)和 Apple [Apple Pay 指南](https://developer.apple.com/apple-pay/)。

## <a name="new-testing-environment"></a>新测试环境

使用 iOS 10 和 watchOS 3，Apple 引入了新的测试环境，使开发人员能够直接在 iOS 设备上预配测试付款卡。 然后，这个新的测试环境会将加密的测试付款数据返回到应用程序。

若要启用新的测试环境，请执行以下操作：

1. 在 iTunes Connect 中创建新的测试 iCloud 帐户。
2. 用新的测试帐户登录到 iOS 设备。
3. 设置要在其中测试应用程序的所需区域。
4. 使用 " [Apple Pay 指南](https://developer.apple.com/apple-pay/)" 中的测试支付卡之一来支付。

> [!NOTE]
> 通过切换 iCloud 帐户，该设备将自动切换到新的测试环境。 但是，在提交给 iTunes 应用商店之前，Apple 仍**要求**在生产环境中使用真实卡测试该应用。

## <a name="summary"></a>总结

本文介绍了 Apple 在 watchOS 3 中 Apple Pay 的增强功能，以及如何在 Xamarin 中实现这些功能。
