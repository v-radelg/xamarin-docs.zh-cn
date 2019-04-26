---
title: Apple Pay Xamarin.iOS
description: 本指南介绍 Xamarin.iOS 环境以用于 Apple Pay 的支付实体商品，如食物、 娱乐和通过您的应用程序的成员身份设置。 它包括所需的标识符、 证书和授权信息。
ms.prod: xamarin
ms.assetid: A25AE660-B145-465F-9CCE-8D82BFD614C6
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 06/05/2017
ms.openlocfilehash: b971029ff3b2b1e8f5e63233d1d754c44b0e3309
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61346967"
---
# <a name="apple-pay-in-xamarinios"></a>Apple Pay Xamarin.iOS

_本指南介绍 Xamarin.iOS 环境以用于 Apple Pay 的支付实体商品，如食物、 娱乐和通过您的应用程序的成员身份设置。它包括所需的标识符、 证书和授权信息。_

Apple Pay 引入了 iOS 8，以及使用户能够支付实体商品，如食物、 娱乐和通过其 iOS 设备的成员身份。 适用于 iPhone 6 和 iPhone 6 的加号，且还可以与在应用商店购买内容 Apple Watch 配对。 在 iPhone 上使用，它将使用 Touch ID，作为一种方法，以确认并授权用户的信用卡或借记卡的事务。

## <a name="requirements"></a>要求

Apple Pay 才可以在 iOS 8 及更高版本，使用，因此需要 Xcode 6 的最小值。

以下各项时还需要将 Apple Pay 集成到您的应用程序：

 - 付款处理器平台
 - 商家标识符
 - Apple Pay 的证书
 - Apple Pay 权利

本文档将介绍更多详细信息中的这些项。

## <a name="differences-between-apple-pay-and-iap"></a>Apple Pay 和、 IAP 之间的差异

Apple Pay 的主要区别和*应用程序内购买*(、 IAP) 与他们在销售的产品。 *物理*销货通过 Apple Pay; 食物、 便利设施和物理娱乐 （如影院票证） 都是此示例。 与此相反，销售、 IAP*虚拟*商品，例如高级或其他内容，以及订阅 – 考虑其他几个月的流式处理服务或在游戏中的额外生活。

使用的框架也是主要的差异;[PassKit](https://developer.apple.com/library/ios/documentation/PassKit/Reference/PKPaymentAuthorizationViewController_Ref/)是用于 Apple Pay，尽管[StoreKit](https://developer.apple.com/library/ios/documentation/PassKit/Reference/PKPaymentAuthorizationViewController_Ref/) 、 IAP 为提供了框架 API。

使用 Apple Pay，Apple[状态](https://developer.apple.com/apple-pay/Getting-Started-with-Apple-Pay.pdf)，其"[] 免费用户、 商店或开发人员用于 Apple Pay 付款"。 相比之下，、 IAP 有 30%要收取费用的每个事务。 此外，使用 Apple Pay，事务不会通过 Apple 所有，相反，它将经历支付平台。

## <a name="using-a-payment-processor-platform"></a>使用付款处理器平台

一个 Apple Pay 的基本组成部分是处理付款。 虽然可能要自行执行此操作，但它需要的重要知识的加密
- Apple 的中所述[付款处理指南](https://developer.apple.com/library/ios/ApplePay_Guide/ProcessPayment.html)。
付款处理平台，但是，处理您，以便您能够集中精力构建您的应用程序的这些操作。

两个选项包括：

- **Stripe** -在[Stripe.com](https://stripe.com/)来访问其 Api。

- **JudoPay** -请查看其[github 上的 Xamarin 示例代码](https://github.com/Judopay/Xamarin-Sample-App)，并在注册[JudoPay.com](https://www.judopay.com/)。

## <a name="provisioning-for-apple-pay"></a>Apple pay 预配

配置应用以使用 Apple Pay，需要安装在 Apple 开发人员门户中，并在应用程序中。 有多个已成功预配 Apple pay 应用应遵循的步骤：

1. 创建商户 ID:
    - 请按照步骤[此处](~/ios/deploy-test/provisioning/capabilities/apple-pay-capabilities.md#merchantid)
2. 创建具有 Apply Pay 功能的应用 ID 并向其添加商户：
    - 请按照步骤[此处](~/ios/deploy-test/provisioning/capabilities/apple-pay-capabilities.md#appid)
3. 为商户 ID 生成证书：
    - 请按照步骤[此处](~/ios/deploy-test/provisioning/capabilities/apple-pay-capabilities.md#certificate)
4. 生成预配配置文件具有新创建的应用 ID:
    - 请按照步骤[此处](~/ios/get-started/installation/device-provisioning/manual-provisioning.md#provisioning)
5. 添加 Apple Pay 权利：
    - 选择 Apple pay 权利作为详细[这里](~/ios/deploy-test/provisioning/entitlements.md)，或手动将键/值对添加到文件中，从[此处](~/ios/deploy-test/provisioning/entitlements.md)

## <a name="working-with-apple-pay"></a>使用 Apple Pay

Apple 已对 Apple Pay 进行多项增强功能，允许用户从网站和通过与 Siri 和映射进行交互的安全还的 iOS 10。

IOS 10，几个新 Api 添加了采用了 iOS 和 watchOS 以支持动态付款网络和新的沙盒测试环境。

### <a name="apple-pay-website-integration"></a>Apple Pay 网站集成

新 ios 10、 开发人员可以将合并 Apple Pay 直接到其网站 using **ApplePay JS**。 浏览在 iOS 或 macOS 中的使用 Safari 网站的用户可以通过验证他们的 iPhone 或 Apple Watch 上的事务进行使用 Apple Pay 的支付。 有关详细信息，请参阅 Apple [ApplePay JP 框架引用](https://developer.apple.com/reference/applepayjs)。

### <a name="passkit-framework-enhancements"></a>PassKit 框架增强功能

在 iOS 10 中，已扩展 PassKit 框架为支持 Apple Pay 之外`UIKit`，并允许卡颁发者提供他们自己从其应用程序中的卡。


#### <a name="supporting-apple-pay-outside-of-uikit"></a>支持外部 UIKit Apple Pay

通过使用[PKPaymentAuthorizationController](https://developer.apple.com/reference/passkit/pkpaymentauthorizationcontroller)并[PKPaymentAuthorixationControllerDelegate](https://developer.apple.com/reference/passkit/pkpaymentauthorizationcontrollerdelegate)，应用可以支持提供的相同功能[PKPaymentAuthorizationViewController](https://developer.apple.com/reference/passkit/pkpaymentauthorizationviewcontroller)而无需使用 UIKit。 此新 API，尽管是必需的支持 Apple Pay Apple Watch 上 （和特定意图也） 是可选的在其他情况下 （例如现有的应用程序）。 但是，Apple 会建议将移动到新的 API 越早越好 Apple Pay 的广泛支持，在所有开发人员的应用程序提供单一代码库。 详细了解意向和 Siri 集成，请参阅我们[简介 SiriKit](~/ios/platform/sirikit/index.md)文档。

#### <a name="presenting-issuer-cards-from-within-apps"></a>显示从应用中的颁发者卡

与 iOS 10、 新功能已添加到允许卡颁发者提供其自己的应用中从其卡 PassKit 框架。 开发人员可以添加`PKPaymentButtonTypeInStore`UIButton 到应用的用户界面，将显示为卡的 Apple Pay 按钮。

`PresentPaymentPass`方法[PKPassLibrary](https://developer.apple.com/reference/passkit/pkpasslibrary)类还可用于以编程方式显示此卡。

### <a name="new-payment-network-support"></a>新的付款网络支持

新增到 iOS 10，应用程序可以自动支持新的付款网络而无需开发人员无需修改、 重新编译应用程序和其重新提交到 App Store 变为可用时。

新[AvailableNetworks](https://developer.apple.com/reference/passkit/pkpaymentrequest/1833288-availablenetworks)方法的`PKPaymentNetwork`类允许应用程序以发现在运行时的用户的设备上可用的网络。 此外， [SupportedNetworks](https://developer.apple.com/reference/passkit/pkpaymentrequest/1619329-supportednetworks)属性已扩展为需要支付提供商的名称作为参数。 使用这些方法，应用可以自动支持支付提供商支持的任何网络。

有关详细信息，请参阅我们[Apple 支付配置](~/ios/platform/apple-pay.md)和 Apple [Apple 支付指南](https://developer.apple.com/apple-pay/)。

### <a name="new-testing-environment"></a>新的测试环境

IOS 10，Apple 引入了新的测试环境，允许开发人员若要预配 iOS 设备上直接测试支付卡。 然后将此新的测试环境向应用返回加密的测试的支付数据。

若要启用新的测试环境，请执行以下操作：

1. 在 iTunes Connect 中创建新测试 iCloud 帐户。
2. 登录到 iOS 设备使用新的测试帐户。
3. 设置所需的区域中的应用程序进行测试。
4. 使用从测试支付卡之一[Apple 支付指南](https://developer.apple.com/apple-pay/)如何付款。

> [!IMPORTANT]
> 通过切换 iCloud 帐户，该设备将自动切换到新的测试环境。 但是，Apple 仍**需要**卡在生产环境中之前提交到 iTunes 应用商店的实际测试的应用程序。

## <a name="summary"></a>总结

在本文中，我们探讨了在应用中使用 Apple Pay 所需的不同项。 还介绍了如何创建商户 ID，以及如何使用内**Entitlements.plist**，这需要对其手动修改。

## <a name="related-links"></a>相关链接

- [应用内购买](~/ios/platform/in-app-purchasing/index.md)
- [PassKit 简介](~/ios/platform/passkit.md)
- [PassKit](https://developer.apple.com/library/ios/documentation/PassKit/Reference/PKPaymentAuthorizationViewController_Ref/)
- [Apple Pay](https://developer.apple.com/apple-pay/)
- [Emporium （示例）](https://developer.xamarin.com/samples/monotouch/ios9/Emporium/)
