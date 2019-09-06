---
title: IOS 11 中的应用商店更改
description: 本文档探讨了 iOS 11 中的应用商店更改。 它讨论了应用程序的存储图标，升级的应用程序内购买、重新设计的产品页、客户通信和分阶段发布。
ms.prod: xamarin
ms.assetid: 4A7A03FD-B4F2-4969-8676-A17260730FD6
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 09/13/2016
ms.openlocfilehash: fa44ba260a62a6e0bd036c08c62371734b38d82d
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2019
ms.locfileid: "70286590"
---
# <a name="app-store-changes-in-ios-11"></a>IOS 11 中的应用商店更改

IOS 应用商店已进行了全面的重新设计，这不仅允许用户有效地浏览应用商店，而且还允许作为开发人员向用户推广你的应用程序。 这些促销活动包括更新到产品页的应用内购买和更新。 iOS 11 还添加了有关如何与用户通信的更新、如何添加应用图标，以及如何将应用程序发布到公共。

![新应用商店布局](app-store-changes-images/image3.jpg)

重新设计的 app store 包含以下部分：

- **今天**–此选项卡包含的应用是 "编辑者选取" 或特色应用。 若要在此处升级应用，请在 "[升级](https://developer.apple.com//contact/app-store/promote/)" 页上填写信息。
- **游戏**–此选项卡下可以找到设置为 iTunes Connect 中的**游戏**类别的任何应用。
- **应用**–此选项卡功能所有其他应用。 用户可以按特色应用、类别、顶部付费/免费进行浏览。
- **更新**–此应用显示应用的更新。 即使您通过[分阶段发布](#Phased_Release)发布应用程序，用户仍可以转到此选项卡并下载最新版本。
- **搜索**–此选项卡允许用户搜索你的应用。

## <a name="store-icon"></a>存储图标

在 iTunes Connect 中不再管理存储图标（或营销图标），而是必须将其作为[资产目录](~/ios/app-fundamentals/images-icons/app-icons.md)包含在应用二进制文件中，这与应用图标类似。 若要成功提交 iOS 11 应用，必须在资产目录中包含 1024 x 1024 存储图标（PNG 格式）。

应用缩小确保此附加资产目录不会增加应用的大小。


## <a name="in-app-purchases-promoted-in-the-app-store"></a>应用商店中升级的应用内购买

Apple 使得应用程序中购买的应用程序在应用程序存储中更易发现。 你现在可以添加最多20个_应用商店升级_的应用商店购买，现在可以在应用页面、应用的产品页上或通过搜索来找到。

若要在应用商店中显示应用内购买，必须包含以下数据：

- **映像**–您需要为描述应用内购买内容的图标提供一个专门设计的映像。 此图像必须不同于应用图标，并且不能是屏幕截图。
- **Name** –名称最多只能为30个字符。
- **说明**-说明最多只能为45个字符。

在发布应用商店之前，任何应用商店中的促销促销都将受到应用商店审查的限制。

若要使应用内购买能够升级，请打开应用，并浏览到 **> 应用内购买功能**。 请参阅**应用商店升级（可选）** 部分，添加 1024 x 1024 映像并**保存**，如下图所示：

![ITune Connect 中的应用商店提升部分](app-store-changes-images/image4.png)

还需要将`ShouldAddStorePayment`方法添加到应用中的`SKPaymentTransactionObserver`协议。

有关应用内购买促销的详细信息，请参阅 Apple[升级应用内购买](https://developer.apple.com/app-store/promoting-in-app-purchases/)页。

## <a name="redesigned-product-page"></a>重新设计的产品页

已对产品页面进行了以下更改：

- 标题现在最多设置为30个字符
- 添加了副标题
  - 它应该是补充标题的简短摘要。
  - 副标题应最多包含30个字符
- 应用预览
  - 你现在可以有三个视频或屏幕截图。
  - 用户访问页面时视频自动播放。
  - 有关详细信息，请参阅 Apple 的[应用预览](https://developer.apple.com/app-store/app-previews/)页面。
- 促销文本
  - 这项新功能提供了170个字符的文本，使你可以描述应用程序的频繁变化的信息。
  - 它可随时更新，而无需提交新版本的应用。

## <a name="customer-communication"></a>客户通信

在10.3 中，Apple 为开发人员提供了一种新的方法，使开发人员可以通过响应评审来直接与应用程序用户进行通信。 可以通过浏览到**应用 > 活动 > 分级和评论**来访问 iTunes connect 中的此新功能，如下图所示：

![显示用于输入批注的区域的对话框](app-store-changes-images/image5.png)

在答复用户时，请注意以下事项：

- 您只能答复一次，但双方都可以根据需要编辑其评论。
- 当你响应注释时，用户会收到通知。
- 已在 iTunes connect 中创建新的客户支持角色。 具有此角色或管理员角色的用户可以响应备注。

有关详细信息，请参阅 Apple[响应审核](https://developer.apple.com/app-store/responding-to-reviews/)页。

<a name="Phased_Release"/>

## <a name="phased-release"></a>分阶段发布

对于 iOS 11，Apple 已实现了用于更新应用程序的分段版本选项。 你可以启用分段发布，以允许将你的应用更新逐步发布给客户，确保需求不会重载你的生产环境。

在 iTunes Connect 中启用分阶段发布。 在边栏中单击你的应用程序，滚动到底部自动更新部分的**分段版本**，并**使用分阶段发布选择7天内的发布更新**：

![显示自动更新分阶段发布的选项](app-store-changes-images/image6.png)

你的更新可立即在应用商店的 "更新" 选项卡中下载。 分段版本仅适用于选择了自动下载的用户。


## <a name="related-links"></a>相关链接

- [IOS 11 （Apple）中的新增功能](https://developer.apple.com/ios/)
- [更新了应用商店产品页（Apple）](https://developer.apple.com/app-store/product-page/)
- [更新 iOS 11 应用（WWDC）（视频）](https://developer.apple.com/videos/play/wwdc2017/204/)
