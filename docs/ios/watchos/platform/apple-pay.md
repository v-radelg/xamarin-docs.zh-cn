---
title: Apple Pay 在 Xamarin 中的 watchos
description: 本文介绍如何增强功能对 Apple Pay watchOS 3 以及如何实现它们在 Xamarin.iOS 中转换为 Apple Watch 进行 Apple 了。
ms.prod: xamarin
ms.assetid: 32FF5D21-C252-485D-83AC-A7E592237962
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: 354e03ee1e07ba99fcdeb05617bc65ed89f0e8c2
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61364110"
---
# <a name="apple-pay-on-watchos-in-xamarin"></a>Apple Pay 在 Xamarin 中的 watchos

Apple 已添加了对应用程序内付款支持的 watchOS 3 中对 Apple Pay 进行多项增强功能。 这允许用户安全地提供付款和联系信息直接从 Apple Watch 支付实体商品和服务。


## <a name="about-apple-pay-enhancements"></a>有关 Apple Pay 增强功能

作为上述 Stated，Apple 对进行了多项增强功能 Apple Pay，安全付款并联系信息以支付实体商品和服务直接从 Apple Watch 的 watchOS 3 中。 这些增强功能提供了对 PassKit 框架的修改。

IOS 10 和 watchOS 3，几个新 Api 添加了采用了 iOS 和 watchOS 以支持动态付款网络和新的沙盒测试环境。

## <a name="passkit-framework-enhancements"></a>PassKit 框架增强功能

在 iOS 10 中，已扩展 PassKit 框架为支持 Apple Pay 之外`UIKit`，并允许卡颁发者提供他们从其应用程序中的卡。 

### <a name="supporting-apple-pay-outside-of-uikit"></a>支持外部 UIKit Apple Pay

通过使用[PKPaymentAuthorizationController](https://developer.apple.com/reference/passkit/pkpaymentauthorizationcontroller)并[PKPaymentAuthorixationControllerDelegate](https://developer.apple.com/reference/passkit/pkpaymentauthorizationcontrollerdelegate)，应用可以支持提供的相同功能[PKPaymentAuthorizationViewController](https://developer.apple.com/reference/passkit/pkpaymentauthorizationviewcontroller)而无需使用 UIKit。 此新 API，尽管是必需的支持 Apple Pay Apple Watch 上 （和特定意图也） 是可选的在其他情况下 （例如现有的应用程序）。 但是，Apple 会建议将移动到新的 API 越早越好 Apple Pay 的广泛支持，在所有开发人员的应用程序提供单一代码库。 详细了解意向和 Siri 集成，请参阅我们[简介 SiriKit](~/ios/platform/sirikit/index.md)文档。

### <a name="presenting-issuer-cards-from-within-apps"></a>显示从应用中的颁发者卡

使用 iOS 10 和 watchOS 3，新的功能已添加到允许卡颁发者提供其自己的应用中从其支付卡 PassKit 框架。 开发人员可以添加`PKPaymentButtonTypeInStore`UIButton 到应用的用户界面，将显示为卡的 Apple Pay 按钮。

`PresentPaymentPass`方法[PKPassLibrary](https://developer.apple.com/reference/passkit/pkpasslibrary)类还可用于以编程方式显示此卡。

## <a name="new-payment-network-support"></a>新的付款网络支持

新 iOS 10 和 watchOS 3，到应用程序可以自动支持新的付款网络而无需开发人员无需修改、 重新编译应用程序和其重新提交到 App Store 变为可用时。

新[AvailableNetworks](https://developer.apple.com/reference/passkit/pkpaymentrequest/1833288-availablenetworks)方法的`PKPaymentNetwork`类允许应用程序以发现在运行时的用户的设备上可用的网络。 此外， [SupportedNetworks](https://developer.apple.com/reference/passkit/pkpaymentrequest/1619329-supportednetworks)属性已扩展为需要支付提供商的名称作为参数。 使用这些方法，应用可以自动支持支付提供商支持的任何网络。

有关详细信息，请参阅我们[Apple 支付配置](~/ios/platform/apple-pay.md)和 Apple [Apple 支付指南](https://developer.apple.com/apple-pay/)。

## <a name="new-testing-environment"></a>新的测试环境

IOS 10 和 watchOS 3，Apple 引入了新的测试环境，允许开发人员若要预配 iOS 设备上直接测试支付卡。 然后将此新的测试环境向应用返回加密的测试的支付数据。

若要启用新的测试环境，请执行以下操作：

1. 在 iTunes Connect 中创建新测试 iCloud 帐户。
2. 登录到 iOS 设备使用新的测试帐户。
3. 设置所需的区域中的应用程序进行测试。
4. 使用从测试支付卡之一[Apple 支付指南](https://developer.apple.com/apple-pay/)如何付款。

> [!NOTE]
> 通过切换 iCloud 帐户，该设备将自动切换到新的测试环境。 但是，Apple 仍**需要**卡在生产环境中之前提交到 iTunes 应用商店的实际测试的应用程序。

## <a name="summary"></a>总结

本文只讨论了增强功能对 Apple Pay 所做的 Apple 已在 watchOS 3，以及如何在 Xamarin.iOS 中实现它们。
