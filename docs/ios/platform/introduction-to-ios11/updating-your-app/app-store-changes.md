---
title: IOS 11 中的 app Store 更改
description: 本文档探讨到 App Store iOS 11 中的更改。 它讨论了应用程序的应用商店图标、 已提升的应用内购买、 经过重新设计的产品页、 客户通信和分阶段的发布。
ms.prod: xamarin
ms.assetid: 4A7A03FD-B4F2-4969-8676-A17260730FD6
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 09/13/2016
ms.openlocfilehash: 022d6b5c3f85863352dd1343752e934240b357aa
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61229602"
---
# <a name="app-store-changes-in-ios-11"></a>IOS 11 中的 app Store 更改

IOS 应用商店具有经过完全重新设计，这不仅允许用户高效地导航应用商店中，而且还允许您，作为开发人员，以提升你的应用的用户。 这些升级包括到应用内购买的更新和更新产品页。 iOS 11 还添加了有关如何与用户进行通信、 如何添加应用程序的图标，以及如何将应用发布到公共更新。

![新应用程序存储区布局](app-store-changes-images/image3.jpg)

经过重新设计应用程序存储区包含以下部分：

- **今天**– 此选项卡包括"编辑器的选取"或特色应用的应用程序。 若要将提升应用此处填写信息上[提升](https://developer.apple.com//contact/app-store/promote/)页。
- **游戏**– 设置为任何应用**游戏**类别在 iTunes Connect 中的可以找到此选项卡下。
- **应用**– 此选项卡上所有其他应用的功能。 用户可以浏览的特色的应用、 类别、 顶部付费/free。
- **更新**– 此应用显示为你的应用的更新。 即使通过应用发布[分阶段发布](#Phased_Release)，用户仍可以转到此选项卡，下载最新版本。
- **搜索**– 此选项卡允许用户搜索您的应用程序。

## <a name="store-icon"></a>应用商店图标

应用商店图标 （或营销图标） 在 iTunes Connect 中不再管理，而是必须作为包含[资产目录](~/ios/app-fundamentals/images-icons/app-icons.md)中你的应用程序二进制文件，类似于应用图标。 1024 x 1024 store 图标以 PNG 格式必须包含在 iOS 11 应用的成功提交的资产目录。

缩小应用程序可确保此附加资产目录不会增加应用程序大小。


## <a name="in-app-purchases-promoted-in-the-app-store"></a>在应用商店中提升的应用内购买

Apple 已应用内购买更容易地发现在应用商店中。 现在，您可以添加最多 20 个_提升的应用商店_现在可以在应用程序页上，在应用程序的产品页上，或通过搜索找到的应用内购买。

若要使用 App Store 中显示在应用内购买必须包括以下数据：

- **映像**– 需要提供描述应用内购买所执行的操作的图标的专门设计的映像。 此映像必须不同于应用程序图标，并且不能为屏幕快照。
- **名称**– 名称仅可最多 30 个字符。
- **说明**– 该说明仅可包含 45 个字符的最大值。

任何应用内购买升级受到 app store 审核，才能发布。

若要使你可用于升级的应用内购买，打开您的应用程序，并浏览到**功能 > 应用内购买**。 转到**应用程序应用商店促销 （可选）** 部分，然后添加 1024x1024 图像并**保存**，在下图中所示：

![ITune 连接中的应用商店促销部分](app-store-changes-images/image4.png)

此外需要添加`ShouldAddStorePayment`方法`SKPaymentTransactionObserver`应用程序中的协议。

应用内购买促销活动的详细信息，请参阅 Apple[提升您的应用内购买](https://developer.apple.com/app-store/promoting-in-app-purchases/)页。

## <a name="redesigned-product-page"></a>经过重新设计的产品页

产品页已得到了以下更改：

- 标题现在都设置为最多 30 个字符
- 已添加副标题
    - 它应该是补充了标题的简短摘要。
    - 副标题应具有最多 30 个字符
- 应用预览
    - 现在可以具有三个视频或屏幕截图。
    - 当用户访问该页面时，自动视频播放。
    - 有关详细信息，请参阅 Apple[应用预览](https://developer.apple.com/app-store/app-previews/)页。
- 促销文本
    - 这一新功能提供了可用于描述有关您的应用程序经常更改信息的文本的 170 个字符。
    - 它可以更新在任何时间无需提交应用程序的新版本。

## <a name="customer-communication"></a>客户通信

10.3，Apple 启动开发人员能够直接与通过能够对评审的响应的应用程序用户进行通信的新方法。 您可以访问这一新功能在 iTunes 中的通过浏览到连接**应用程序 > 活动 > 评级和评论**，在下图中所示：

![显示区域，用于输入批注答复对话框](app-store-changes-images/image5.png)

若要答复的用户时应注意的以下几点：

- 仅一次，回复，但这两个参与方可以编辑他们的意见，他们想与非常相似。
- 当响应批注时，用户将收到通知。
- 连接新的客户支持已在 iTunes 中创建角色。 具有此角色或管理员角色的用户可以响应的注释。

有关详细信息，请参阅 Apple[响应评审](https://developer.apple.com/app-store/responding-to-reviews/)页。

<a name="Phased_Release"/>

## <a name="phased-release"></a>分阶段的发布

与 iOS 11，Apple 已实现分阶段发布更新到您的应用程序的选项。 您可以分阶段的发布以允许你应用的更新以将逐渐发布给客户，确保该请求不会重载在生产环境。

在 iTunes Connect 中启用了分阶段的发布。 单击侧栏中应用，滚动到**分阶段发布为自动更新**部分的底部，然后选择**7 天期间使用的版本更新分阶段发布**:

![选项显示分阶段发布为自动更新](app-store-changes-images/image6.png)

更新后可立即供下载的应用商店的更新选项卡。 分阶段的发布在内仅适用于具有自动下载所选的用户。


## <a name="related-links"></a>相关链接

- [What's New iOS 11 (Apple) 中](https://developer.apple.com/ios/)
- [更新的应用商店产品页 (Apple)](https://developer.apple.com/app-store/product-page/)
- [将应用更新适用于 iOS 11 (WWDC) （视频）](https://developer.apple.com/videos/play/wwdc2017/204/)
