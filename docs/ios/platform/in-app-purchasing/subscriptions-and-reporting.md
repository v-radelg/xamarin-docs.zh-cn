---
title: Xamarin 中的订阅和报表
description: 本文档介绍无续订订阅、免费订阅、自动可续订订阅，并使用 iTunes Connect 对这些项目进行报告。
ms.prod: xamarin
ms.assetid: 27EE4234-07F5-D2CD-DC1C-86E27C20141E
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/18/2017
ms.openlocfilehash: 7f455d2164573d68db0a9c764f2b2cef5cc6d739
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2019
ms.locfileid: "70284042"
---
# <a name="subscriptions-and-reporting-in-xamarinios"></a>Xamarin 中的订阅和报表

## <a name="about-non-renewing-subscriptions"></a>关于非续订订阅

非续订订阅适用于代表服务销售的产品（例如，一周对导航应用程序的访问权限或对数据存档的限时访问权限）。   
   
无续订订阅和其他产品类型之间的主要区别：

- ITunes Connect 中的产品定义不包括术语。 应用程序代码必须能够从产品 ID 推断有效期。 
- 可以多次购买它们（如一种耗材的产品）。 需要应用程序来管理订阅期限/到期和续订，并阻止用户购买重叠订阅。 
- StoreKit Restore 功能不支持购买。 如果订阅应在用户的所有设备上都可用，则该应用程序必须与远程服务器一起设计和实现此功能。 在备份设备然后从备份还原时，应用程序还负责备份的订阅状态。 
- 实现概述
- 通常，非续订订阅应使用服务器交付的工作流和管理的同类产品来实现。 


## <a name="about-free-subscriptions"></a>关于免费订阅

免费订阅允许开发人员将免费内容放在 Newsstand 应用中（不能用于非 Newsstand 应用）。 免费订阅启动后，它将在用户的所有设备上可用。 免费订阅永不过期;它们仅在卸载应用程序时结束。

### <a name="implementation-overview"></a>实现概述

免费订阅的行为与自动可续订订阅非常类似。 在 iTunes Connect 中，应用程序必须有可用于 "购买" 的免费订阅产品。 当用户购买时，应验证免费订阅购买情况，如自动可续订订阅产品。 免费订阅事务可以还原。


## <a name="about-auto-renewable-subscriptions"></a>关于自动可续订订阅

自动可续订订阅主要用于 Newsstand 应用程序中。 它们代表一种产品，该产品向用户授予了给定时间段内的动态内容的访问权限，该时间是在 iTunes Connect 中配置的（设置时间段从7天到1年）。 订阅将自动续订，并在每个订阅期结束时向用户 Apple ID 收费，除非用户会退出。此产品类型适用于杂志或新闻订阅，用户可以在其中访问发布的每个问题，同时订阅有效。

### <a name="implementation-overview"></a>实现概述

应使用服务器交付的产品工作流（请参阅*回执验证和服务器交付的产品*部分）来实施自动可续订订阅。

#### <a name="shared-secret"></a>共享机密

验证服务器上的自动可续订订阅时，必须在 JSON 请求中使用应用内购买共享机密。 通过 iTunes Connect 创建/访问共享机密。

从 iTunes Connect 主页中选择 **"我的应用"** ：   
   
 [![](subscriptions-and-reporting-images/image2.png "选择“我的应用”")](subscriptions-and-reporting-images/image2.png#lightbox)  
 
选择一个应用程序，并单击 "**应用内购买**" 选项卡：

[![](subscriptions-and-reporting-images/image6.png "单击 \"应用内购买\" 选项卡")](subscriptions-and-reporting-images/image6.png#lightbox)

从页面底部选择 "**查看或生成共享机密**"：
   
 [![](subscriptions-and-reporting-images/image40.png "选择 \"查看或生成共享机密\"")](subscriptions-and-reporting-images/image40.png#lightbox)

 [![](subscriptions-and-reporting-images/image41.png "生成共享机密")](subscriptions-and-reporting-images/image41.png#lightbox)   
   
   
   
 若要使用共享机密，请将其包含在为自动可续订订阅验证应用内购买回执的 JSON 有效负载中，如下所示：

```csharp
{
   "receipt-data" : "(receipt bytes here)",
   "password"     : "(shared secret bytes here)"
}
```

如果购买有效，则响应的 "状态" 字段将为零，与其他产品类型一样。

#### <a name="downloading-items-after-the-initial-subscription-term"></a>在初始订阅期限后下载项

在交付订阅产品的过程中，该代码应经常针对 Apple 的服务器验证最新已知的回执。 如果自上次验证后订阅自动续订，则 JSON 响应将包含附加字段，通知应用程序已发生的事务（应扩展订阅有效性）。 JSON 响应将包含：

```csharp
{
   "status" : 0,
   "receipt" : { (receipt here) },
   "latest_receipt" : "(base-64 encoded receipt here)",
   "latest_receipt_info" : { (latest receipt info here) }
}
```

如果状态为零，则订阅仍有效，其他字段则保存有效数据。 如果状态为21006，则订阅已过期。 请参阅[验证自动可续订订阅回执](https://developer.apple.com/library/ios/releasenotes/General/ValidateAppStoreReceipt/Chapters/ValidateRemotely.html)文档中是否有其他错误代码。

#### <a name="restoring-auto-renewable-subscriptions"></a>还原自动可续订订阅

您将获得多个事务，即每次续订订阅时的原始采购交易记录和单独的事务。 你需要跟踪 "开始日期" 和 "条款"，以了解有效性期限。   
   
   
   
 SKPaymentTransaction 对象不包括订阅术语–应为每个术语使用不同的产品 ID，并编写可以从事务的购买日期中推断出订阅时间的代码。

#### <a name="testing-auto-renewal"></a>测试自动续订

为了更轻松地测试订阅，在沙盒中进行测试时，它们的持续时间会压缩。 1周订阅每3分钟续订一次，1年订阅每小时续订一次。 在沙盒中进行测试时，订阅将自动续订最多6次。

## <a name="reporting"></a>报告

iTunes Connect （ [itunesconnect.apple.com](http://itunesconnect.apple.com)）提供：   
   
 **销售和趋势**–显示应用下载、更新和应用内购买的详细信息。   
   
 **支付和财务报告**–详细说明应用所获得的收入，并列出已对你进行的付款和你的欠量。

示例销售和趋势报表如下所示：   

 [![](subscriptions-and-reporting-images/image42.png "销售和趋势报表示例")](subscriptions-and-reporting-images/image42.png#lightbox)   
   
 此外，还提供了一个 " [**行楷连接移动**IOS 应用（iTunes" 链接）](http://itunes.apple.com/us/app/itunes-connect-mobile/id376771144?mt=8)。
下面显示了一些可用统计信息的 iPhone 屏幕截图：   
   
 [![](subscriptions-and-reporting-images/image43.png "某些可用统计信息的 iPhone 屏幕截图")](subscriptions-and-reporting-images/image43.png#lightbox)
