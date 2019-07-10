---
title: 订阅和 Xamarin.iOS 中的报表
description: 本文档介绍非续订订阅，免费订阅、 可自动续订订阅，并使用 iTunes Connect 报告对这些项目。
ms.prod: xamarin
ms.assetid: 27EE4234-07F5-D2CD-DC1C-86E27C20141E
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: 44dd2b2eea31f47ed53d8ef25ff7b0667286179d
ms.sourcegitcommit: 58d8bbc19ead3eb535fb8248710d93ba0892e05d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2019
ms.locfileid: "67674562"
---
# <a name="subscriptions-and-reporting-in-xamarinios"></a>订阅和 Xamarin.iOS 中的报表

## <a name="about-non-renewing-subscriptions"></a>有关非续订订阅

非续订订阅适用于表示的时间限制 （对导航应用程序的一周的访问） 或数据存档到的限时访问等服务的销售产品的产品。   
   
非续订订阅和其他产品类型之间的主要差异：

-  在 iTunes Connect 中的产品定义不包括术语。 应用程序代码必须能够推断出的有效期从产品 id。 
-  他们可以购买多个时间 （如可使用的产品）。 应用程序所需管理订阅术语/过期和续订，并阻止用户购买重叠的订阅。 
-  购买不受 StoreKit 还原函数。 如果该订阅应可跨所有用户的设备，应用程序将需要设计和实现此功能与远程服务器结合使用。 应用程序也要负责备份订阅状态的情况下当设备备份然后还原中的备份。 
-  实现概述
-  使用服务器发出工作流和管理等易耗型产品，则通常应实现非续订订阅。 


## <a name="about-free-subscriptions"></a>有关免费订阅

免费订阅允许开发人员将免费内容放在 Newsstand 应用 （它们不能在非 Newsstand 应用中使用）。 启动免费订阅后它将在用户的所有设备上可用。 免费订阅永不过期;它们仅终止时卸载应用程序。

### <a name="implementation-overview"></a>实现概述

免费订阅更类似于可自动续订订阅。 应用程序必须在 iTunes Connect 中具有可供购买的免费订阅产品。 如果购买的用户，免费订阅购买应验证等可自动续订订阅产品。 可以还原免费订阅的事务。


## <a name="about-auto-renewable-subscriptions"></a>有关自动续订订阅

自动续订订阅主要用于在 Newsstand 应用程序中。 它们表示给定的配置在 iTunes Connect （设置范围从 7 天到 1 年期） 中的时间段内授予用户对动态内容的访问的产品。 自动续订，除非用户选择出每个的订阅期限结束时计费用户 Apple ID。此产品类型非常适合杂志或新闻订阅，其中用户获取的访问权限发布其订阅有效期间每个问题。

### <a name="implementation-overview"></a>实现概述

应使用 Server-Delivered 产品工作流实现自动续订订阅 (请参阅*回执验证和 Server-Delivered 产品*部分)。

#### <a name="shared-secret"></a>共享的机密

验证你的服务器上的自动续订订阅时，应用内购买共享密钥必须使用 JSON 请求中。 共享的密钥是通过 iTunes Connect 创建/访问。

从 iTunes Connect 主页上选择**我的应用**:   
   
 [![](subscriptions-and-reporting-images/image2.png "选择“我的应用”")](subscriptions-and-reporting-images/image2.png#lightbox)  
 
选择应用程序，然后单击**应用内购买**选项卡：

[![](subscriptions-and-reporting-images/image6.png "单击应用内购买选项卡")](subscriptions-and-reporting-images/image6.png#lightbox)

从页面底部，选择**视图，或者生成一个共享的机密**:
   
 [![](subscriptions-and-reporting-images/image40.png "选择视图或生成一个共享的机密")](subscriptions-and-reporting-images/image40.png#lightbox)

 [![](subscriptions-and-reporting-images/image41.png "生成共享的密钥")](subscriptions-and-reporting-images/image41.png#lightbox)   
   
   
   
 若要使用的共享的机密，请将其包含在验证了自动续订订阅，此类未应用内购买回执时发送到 Apple 的服务器的 JSON 有效负载：

```csharp
{
   "receipt-data" : "(receipt bytes here)",
   "password"     : "(shared secret bytes here)"
}
```

响应的状态字段将为零，如果购买有效，为与其他产品类型。

#### <a name="downloading-items-after-the-initial-subscription-term"></a>在初始订阅期后下载项

作为提供订阅产品的一部分，代码应频繁地验证对 Apple 的服务器的最新已知的回执。 如果订阅具有自动续订自上次验证后，JSON 响应将包含通知已发生的事务应用程序的其他字段 （这应扩展的订阅有效期）。 将包含 JSON 响应：

```csharp
{
   "status" : 0,
   "receipt" : { (receipt here) },
   "latest_receipt" : "(base-64 encoded receipt here)",
   "latest_receipt_info" : { (latest receipt info here) }
}
```

如果状态为零订阅是否仍有效，和其他字段保留有效的数据。 如果状态为 21006 订阅已过期。 请参阅[验证自动续订的订阅接收](https://developer.apple.com/library/ios/releasenotes/General/ValidateAppStoreReceipt/Chapters/ValidateRemotely.html)其他错误代码的文档。

#### <a name="restoring-auto-renewable-subscriptions"></a>还原可自动续订订阅

您将获得多个事务 – 原始购买事务以及每个订阅已续订的时间段的单独的事务。 您需要跟踪的开始日期和条款，若要了解的有效期。   
   
   
   
 SKPaymentTransaction 对象不包含在订阅期-应为每个字词使用不同的产品 ID 和编写代码，可以进行推断的订阅期限从购买日期的事务。

#### <a name="testing-auto-renewal"></a>测试自动续订

若要使其更轻松地测试订阅，其持续时间进行压缩时测试在沙盒中。 1 周的订阅续订每隔 3 分钟，1 年的订阅续订每隔一小时。 订阅将自动续订 6 次测试在沙盒中时的最大值。

## <a name="reporting"></a>报表

在 iTunes Connect ( [itunesconnect.apple.com](http://itunesconnect.apple.com)) 提供了：   
   
 **销售和趋势**– 显示详细信息的应用程序的下载、 更新和应用内购买。   
   
 **付款和财务报告**– 详细介绍了应用程序以及对你和你向支付多少所做的列表付款可获得的收入。

销售和趋势报表示例如下所示：   

 [![](subscriptions-and-reporting-images/image42.png "销售和趋势报表示例")](subscriptions-and-reporting-images/image42.png#lightbox)   
   
 此外，还有[**黑连接移动**iOS 应用 （iTunes 链接）](http://itunes.apple.com/us/app/itunes-connect-mobile/id376771144?mt=8)。
iPhone 某些可用的统计信息的屏幕截图如下所示：   
   
 [![](subscriptions-and-reporting-images/image43.png "iPhone 某些可用的统计信息的屏幕截图")](subscriptions-and-reporting-images/image43.png#lightbox)
