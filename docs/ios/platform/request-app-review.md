---
title: 请求在 Xamarin.iOS 应用评审
description: 本文介绍 RequestReview 方法添加到 iOS 10 中，该 Apple，并讨论了如何在 Xamarin.iOS 中实现它。
ms.prod: xamarin
ms.assetid: 6408e707-b7dc-4557-b931-16a4d79b8930
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/29/2017
ms.openlocfilehash: f72aaa781b0712e206cf02725cfc434594287f41
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61365785"
---
# <a name="request-app-review-in-xamarinios"></a>请求在 Xamarin.iOS 应用评审

_这篇文章介绍 RequestReview 方法添加到 iOS 10 及如何在 Xamarin.iOS 中实现该 Apple。_

向 iOS 10.3，新`RequestReview()`方法允许 iOS 应用程序要求用户进行评估或查看它。 用户从应用商店安装的传送应用程序中调用此方法时，iOS 10 将处理整个分级和查看过程帮助开发人员。 由于此过程由应用商店策略管理，警报可能会或可能不会显示。

![](request-app-review-images/review01.png "评审请求一个示例警报")

## <a name="requesting-a-rating-or-review"></a>请求的评级或评价

虽然`RequestReview()`的静态方法`SKStoreReviewController`类可以调用在有意义的用户体验在任何时刻，控制和处理的应用商店策略审核过程。 因此，此方法可能会或可能不会显示警报并应永远不会调用以响应用户操作，例如点击一个按钮。

例如，应用可能会请求评审后，启动给定的数目的时间或播放机完成一个级别后，游戏可能会请求评审。

请求评审只要 Xamarin.iOS 应用完成启动，进行以下更改到`AppDelegate.cs`文件：

```csharp
using Foundation;
using StoreKit;
using UIKit;

namespace iOSTenThree
{
    [Register ("AppDelegate")]
    public class AppDelegate : UIApplicationDelegate
    {
        ...

        public override bool FinishedLaunching (UIApplication application, NSDictionary launchOptions)
        {
            // Request a review from the user
            SKStoreReviewController.RequestReview ();

            return true;
        }
        
        ...
        
    }
}
```

> [!NOTE]
> 调用`RequestReview()`在未得到充分开发应用程序将始终显示分级以及查看对话框，以便可以对其进行测试。 这不适用于已通过 TestFlight，其中将忽略方法调用中分发的应用。

当`RequestReview()`方法调用中用户已从应用商店安装的传送应用程序时，iOS 10 将帮助开发人员处理整个评级和评审过程。 同样，因为此过程由应用商店策略管理，警报将可能或可能不会显示。

## <a name="linking-to-an-app-store-product-page"></a>将链接到应用商店产品页 

除了新`RequestReview`方法，开发人员仍可以提供的深层链接到应用内从应用商店中的应用程序的产品页。 通过追加`action=write-review`为产品页面的 URL，页面将打开用户可以自动编写的应用程序的评审。 

## <a name="summary"></a>总结

本文只讨论了 RequestReview 方法添加到 iOS 10 及如何在 Xamarin.iOS 中实现该 Apple。



## <a name="related-links"></a>相关链接

- [iOSTenThree 示例](https://developer.xamarin.com/samples/ios/iOS10/iOSTenThree)
