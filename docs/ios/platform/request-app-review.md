---
title: 在 Xamarin 中请求应用评审
description: 本文介绍 Apple 添加到 iOS 10 的 RequestReview 方法，并讨论如何在 Xamarin 中实现它。
ms.prod: xamarin
ms.assetid: 6408e707-b7dc-4557-b931-16a4d79b8930
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/29/2017
ms.openlocfilehash: 0522e6b1bcf3e0f927a403c12a9c27bb6b3bb3ff
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73031600"
---
# <a name="request-app-review-in-xamarinios"></a>在 Xamarin 中请求应用评审

_本文介绍 Apple 添加到 iOS 10 的 RequestReview 方法，以及如何在 Xamarin 中实现该方法。_

新的 iOS 10.3，`RequestReview()` 方法允许 iOS 应用要求用户对其进行评级或查看。 在用户已从应用商店安装的装运应用中调用此方法时，iOS 10 将处理开发人员的整个评级和审核过程。 由于此过程由应用商店策略控制，因此可能会显示警报，也可能不显示。

![](request-app-review-images/review01.png "A sample Review Request alert")

## <a name="requesting-a-rating-or-review"></a>请求评级或评审

虽然 `SKStoreReviewController` 类的 `RequestReview()` 静态方法可以在用户体验中有意义的任何位置调用，但是审核过程由应用商店策略控制和处理。 因此，此方法可能会也可能不会显示警报，并且决不会为响应用户操作（如点击按钮）而调用。

例如，应用程序可能会在启动了给定次数后请求审阅，或在播放机完成某一级别后，游戏可能会请求评审。

若要在 Xamarin iOS 应用完成启动后立即请求审阅，请对 `AppDelegate.cs` 文件进行以下更改：

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
> 在开发中调用 `RequestReview()` 将始终显示 "分级和检查" 对话框，以便可以对其进行测试。 这不适用于已通过 TestFlight 分发的应用，在此情况下，将忽略方法调用。

在用户已从应用商店安装的装运应用中调用 `RequestReview()` 方法时，iOS 10 将处理开发人员的全部评级和审核过程。 同样，由于此过程由应用商店策略控制，因此可能会显示警报，也可能不显示。

## <a name="linking-to-an-app-store-product-page"></a>链接到应用商店产品页 

除了新的 `RequestReview` 方法，开发人员还可以从应用内向应用商店中的应用程序的 "产品" 页提供深层链接。 通过将 `action=write-review` 追加到产品页 URL 的末尾，会打开一个页面，用户可在其中自动编写应用的审核。 

## <a name="summary"></a>总结

本文介绍了 Apple 添加到 iOS 10 的 RequestReview 方法，以及如何在 Xamarin 中实现它。

## <a name="related-links"></a>相关链接

- [iOSTenThree 示例](https://docs.microsoft.com/samples/xamarin/ios-samples/ios10-iostenthree/)
