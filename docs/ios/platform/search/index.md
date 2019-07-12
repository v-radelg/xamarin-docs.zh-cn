---
title: 在 Xamarin.iOS 中搜索 Api
description: 本文介绍如何使用新的应用程序搜索 Api 提供的 iOS 9 以允许用户搜索信息和 Xamarin.iOS 应用程序中的功能。
ms.prod: xamarin
ms.assetid: 7323EB3D-A78F-4BF0-9990-3160C7E83CF0
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/20/2017
ms.openlocfilehash: 87f7689a082c891bc199f4221e68ed7ab19adf85
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67832710"
---
# <a name="search-apis-in-xamarinios"></a>在 Xamarin.iOS 中搜索 Api

_本文介绍如何使用提供的 iOS 9 应用搜索 Api 以允许用户搜索信息和 Xamarin.iOS 应用程序中的功能。_

在 iOS 9 提供了全新的方法来访问信息和在 Xamarin.iOS 应用程序的功能已扩展搜索。 使用新的应用程序搜索 Api，应用程序内容由通过聚焦和 Safari 搜索结果中，切换和 Siri 提醒和建议可搜索。 这样用户就可以快速访问活动和您的应用程序内的深度信息。

此外，新的搜索 Api 使其更轻松地将不在前一搜索实现体验的情况下应用程序中的搜索功能集成。 正因为如此，Apple 声明它通常需要数小时才能使普遍可供搜索，使用应用程序搜索 iOS 9 应用程序的内容。

[![](images/intro01.png "举例说明如何使用应用程序搜索全局可搜索的 iOS 9 应用内容")](images/intro01.png#lightbox)

应用搜索由三个单独的 Api 的组成：

1. [**NSUserActivity** ](nsuseractivity.md) -这是 Apple 在 iOS 8 中发布的提交 api 扩展。 它用于使应用程序交互历史记录可供搜索，同时公开和私有） 用户。

2. [**核心聚焦**](corespotlight.md) -允许应用在搜索结果中显示其内容编制索引。 其工作原理类似数据库 API，可以添加、 删除项是应用内的索引专用内容的最佳方式。

3. [**WebMarkup** ](web-markup.md) -适用于提供一个 web 界面，通过其内容的访问权限的应用 (不只从应用内)。 可以使用特殊的链接，将通过 Apple 爬网，并提供深层链接到用户的 iOS 9 设备上的应用进行标记的 web 内容。

## <a name="selecting-an-app-search-approach"></a>选择应用搜索方法

确定哪种方法来实现取决于提供您的应用程序进行交互的类型和它提供的内容的类型。

使用以下准则：

- [**NSUserActivity** ](nsuseractivity.md) – 使用此框架提供可搜索性公钥和私钥内容和您的应用程序内导航点的可搜索性。

- [**核心聚焦**](corespotlight.md) – 使用此框架为专用数据存储在设备上提供可搜索性。

- [**Web 标记**](web-markup.md) – 使用此框架的应用的呈现不仅从其内容内的应用程序中，而从应用程序的网站也提供可搜索性。

使用上述每个应用程序搜索的方法不同，并且可以是单独，但是 Apple 将它们设计为协同工作。 当使用多个方法编制索引的特定项，请确保使用相同**项 ID**上每种方法，因此该个人链接工作到一起。

使用多个方法不但可以确保你的内容将会找到由最终用户，但还有助于改善从搜索中的项的排名。

中的排名过程时主要透明的开发人员，与给定项的用户交互设置很大程度后排名 （例如用户轻按链接）。
通过提供丰富、 带有提示性的项，可以确保，用户将 enticed 以便与你的内容，因此提升了排名。

## <a name="what-content-to-index"></a>内容索引

Apple 提供了有关哪些内容和操作下面建议，以提供有关应用程序中的搜索索引：

- 任何内容查看、 创建或策划的由应用程序中的用户。
- 导航点和应用程序中的功能。
- 比如，新消息、 内容或其他类型的最近下载到设备的项显示你的应用。

## <a name="app-search-enhancements"></a>应用搜索增强功能

核心聚焦在 iOS 10 如提供对应用程序搜索的多项增强功能：

- **众深层链接受欢迎程度 （与差异隐私）** -提供了一种方法来升级在搜索结果中的深层链接应用内容。
- **应用内搜索**-使用新`CSSearchQuery`类以提供应用程序内 Spotlight 搜索功能对邮件、 消息和说明应用程序的工作方式类似。
- **搜索延续**-用户可以启动搜索在聚焦或 Safari，然后打开应用并继续搜索。
- **验证结果的可视化效果**-Apple[应用搜索 API 验证工具](https://search.developer.apple.com/appsearch-validation-tool)时只有测试现在会显示网站的标记和深度链接的可视表示形式。
- **消息应用映像共享**-允许在消息 （通过消息应用扩展） 中的共享 Spotlight 搜索中显示为提供的常用应用内映像。

若要获取详细信息，请参阅我们[应用搜索增强功能](~/ios/platform/search/app-search-enhancements.md)指南。

### <a name="proactive-suggestions"></a>主动建议

iOS 10 展示驾驶参与到应用程序的新方法使系统能够主动有用的信息自动向用户显示在适当的时间。 只需为 iOS 9 提供了 iOS 10 应用程序可以公开可以从系统中的以下位置提供给用户的功能使用 Spotlight、 切换和 Siri 建议向应用添加深层搜索的功能：

- 应用程序切换器
- 在锁定屏幕
- CarPlay
- 映射
- 使用 Siri 交互
- QuickType 建议 

应用程序公开此功能对系统使用一系列技术，如[NSUserActivity](xref:Foundation.NSUserActivity)，web 标记中，核心聚焦、 MapKit、 Media Player 和 UIKit。

若要获取详细信息，请参阅我们[主动建议](~/ios/platform/search/proactive-suggestions.md)指南。

## <a name="summary"></a>总结

本文只讨论了新的 iOS 9 适用于 Xamarin.iOS 应用提供的搜索 API 功能。 它涵盖[NSUserActivity](nsuseractivity.md)，[核心聚焦](corespotlight.md)并[Web 标记](web-markup.md)索引内容的方法。 它已完成，但应在何时使用给定的搜索方法和类型的内容应该是什么的简短讨论编制索引。



## <a name="related-links"></a>相关链接

- [iOS 9 示例](https://developer.xamarin.com/samples/ios/iOS9/)
- [面向开发人员的 iOS 9](https://developer.apple.com/ios/pre-release/)
- [iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [应用搜索编程指南](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/AppSearch/index.html#//apple_ref/doc/uid/TP40016308)
