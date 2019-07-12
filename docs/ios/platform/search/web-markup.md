---
title: 使用 Web 标记在 Xamarin.iOS 中搜索
description: 本文档介绍如何创建链接到 Xamarin.iOS 应用程序的基于 web 的搜索结果。 它介绍如何启用 web 内容编制索引，从而使应用程序的网站可供检测，使用智能应用横幅、 通用链接和的详细信息。
ms.prod: xamarin
ms.assetid: 876315BA-2EF9-4275-AE33-A3A494BBF7FD
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/20/2017
ms.openlocfilehash: a9cf3dab9c112bf7ff99cbc0dd9541c3c1e35142
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67830138"
---
# <a name="search-with-web-markup-in-xamarinios"></a>使用 Web 标记在 Xamarin.iOS 中搜索

用于提供对 web 站点通过其内容的访问权限的应用程序 (不只从应用内)，web 内容可以使用特殊的链接，将通过 Apple 爬网，并提供到用户的 iOS 9 设备上应用的深层链接进行标记。

如果 iOS 应用程序已支持移动的深层链接，并且你的网站提供深层链接在应用中，Apple 的内容_Applebot_ web 爬网程序将此内容编制索引并自动将其添加到其云索引：

[![](web-markup-images/webmarkup01.png "云索引概述")](web-markup-images/webmarkup01.png#lightbox)

Apple 将呈现在 Spotlight 搜索和 Safari 搜索结果中的这些结果。
如果在用户点击其中的一个 （并且必须将应用程序安装），然后他们将转到应用程序中的内容：

[![](web-markup-images/webmarkup02.png "从搜索结果中的网站链接的深度")](web-markup-images/webmarkup02.png#lightbox)

## <a name="enabling-web-content-indexing"></a>启用 Web 内容索引编制

有四个可供搜索使用 Web 标记应用程序的内容所需的步骤：

1. 请确保 Apple 能够发现和索引定义作为该应用程序的网站**支持**或**市场营销**在 iTunes Connect 中的网站。
2. 请确保应用程序的网站包含所需的标记，来实现移动深层链接。 请参阅以下各节更多详细信息。
3. 启用 iOS 应用程序中处理的深层链接。
4. 添加应用程序的网站来提供丰富、 更具吸引力的结果提供给最终用户显示的结构化数据的标记。 尽管此步骤不是绝对必需的但强烈建议由 Apple。

以下部分将介绍这些步骤的详细信息。

## <a name="make-your-apps-website-discoverable"></a>使应用程序的网站发现

已找到应用程序的网站的 Apple 的最简单方法是使用为**支持**或**市场营销**网站时，将应用提交到 Apple 通过 iTunes Connect。

## <a name="using-smart-app-banners"></a>使用智能应用横幅

在你的网站提供清楚的链接到你的应用提供智能应用程序横幅。 如果尚未安装该应用程序，Safari 将自动提示用户安装您的应用程序。 否则使用可以点击**视图**链接以启动将应用程序从该网站。 例如，若要创建智能应用程序横幅，可以使用以下代码：

```xml
<meta name="AppName" content="app-id=123456, app-argument=http://company.com/AppName">
```

有关详细信息，请参阅 Apple[提升应用程序与智能应用横幅](https://developer.apple.com/library/ios/documentation/AppleApplications/Reference/SafariWebContent/PromotingAppswithAppBanners/PromotingAppswithAppBanners.html)文档。

## <a name="using-universal-links"></a>使用通用链接

新 ios 9、 通用链接提供了更好的选择智能应用横幅或现有的自定义 URL 方案提供以下：

- **唯一**– 不能由多个网站声明相同的 URL。
- **安全**– 一个签名的证书是所必需的以确保网站拥有由你和有效的网站链接到您的应用程序。
- **灵活**– 最终用户可以控制此 URL 启动该网站或应用程序。
- **通用**– 相同的 URL 可用于定义你的网站和应用程序的内容。

## <a name="using-twitter-cards"></a>使用 Twitter 卡

您可以使用 Twitter 的卡的应用程序的内容的深层链接。 例如:

```xml
<meta name="twitter:app:name:iphone" content="AppName">
<meta name="twitter:app:id:iphone" content="AppNameID">
<meta name="twitter:app:url:iphone" content="AppNameURL">
```

有关详细信息，请参阅 Twitter [Twitter 卡协议](http://dev.twitter.com/cards/mobile)文档。

## <a name="using-facebook-app-links"></a>使用 Facebook 应用链接

您可以使用 Facebook 应用程序链接的应用程序的内容的深层链接。 例如:

```xml
<meta property="al:ios:app_name" content="AppName">
<meta property="al:ios:app_store_id" content="AppNameID">
<meta property="al:ios:url" content="AppNameURL">
```

有关详细信息，请参阅 Facebook[应用程序链接](http://applinks.org)文档。

## <a name="opening-deep-links"></a>打开的深层链接

您需要添加支持打开和 Xamarin.iOS 应用程序中显示的深层链接。 编辑**AppDelegate.cs**文件，然后重写`OpenURL`方法来处理自定义的 URL 格式。 例如:

```csharp
public override bool OpenUrl (UIApplication application, NSUrl url, string sourceApplication, NSObject annotation)
{

    // Handling a URL in the form http://company.com/appname/?123
    try {
        var components = new NSUrlComponents(url,true);
        var path = components.Path;
        var query = components.Query;

        // Is this a known format?
        if (path == "/appname") {
            // Display the view controller for the content
            // specified in query (123)
            return ContentViewController.LoadContent(query);
        }
    } catch {
        // Ignore issue for now
    }

    return false;
}
```

在上述代码中，我们正在寻找一个 URL，其中包含`/appname`并传递的值`query`(`123`在此示例中) 到我们的应用程序以向用户显示请求的内容中的自定义视图控制器。

## <a name="providing-rich-results-with-structured-data"></a>与结构化数据提供丰富的结果

通过包括结构化数据标记，可以向最终用户，不再局限于只需标题和说明提供丰富的搜索结果。 包含图像、 应用程序特定的数据 （例如分级） 以及使用结构化数据标记的结果的操作。

丰富的结果更具吸引力和可帮助提高在云中排名基于搜索索引通过诱使更多用户能够与之交互。

提供结构化数据标记的一个选项是使用开放图谱。 例如：

```xml
<meta property="og:image" content="http://company.com/appname/icon.jpg">
<meta property="og:audio" content="http://company.com/appname/theme.m4a">
<meta property="og:video" content="http://company.com/appname/tutorial.mp4">
```

有关详细信息，请参阅[开放图谱](http://ogp.me)网站。

为结构化数据标记的另一种常见格式是 schema.org 的微数据格式。 例如:

```xml
<div itemprop="aggregateRating" itemscope itemtype="http://schema.org/AggregateRating">
    <span itemprop="ratingValue">4** stars -
    <span itemprop="reviewCount">255** reviews


```

可以以 schema.org 的 JSON-LD 格式表示相同的信息：

```xml
<script type="application/ld+json">
    "@content":"http://schema.org",
    "@type":"AggregateRating",
    "ratingValue":"4",
    "reviewCount":"255"
</script>
```

下面显示了你的网站向最终用户提供丰富的搜索结果中的元数据的一个示例：

[![](web-markup-images/deeplink01.png "丰富的搜索结果通过结构化数据标记")](web-markup-images/deeplink01.png#lightbox)

目前，Apple 支持 schema.org 中的以下架构类型：

- AggregateRating
- ImageObject
- InteractionCount
- 产品/服务
- 组织
- PriceRange
- 方案
- SearchAction

有关这些方案类型的详细信息，请参阅[schema.org](http://schema.org)。

## <a name="providing-actions-with-structured-data"></a>提供与结构化数据的操作

特定类型的结构化数据将允许搜索结果是由最终用户可执行。 目前支持以下操作：

- 拨打电话号码。
- 获取给定地址的映射方向。
- 播放音频或视频文件。

例如，定义要拨打的电话号码的操作可能看起来如下所示：

```xml
<div itemscope itemtype="http://schema.org/Organization">
    <span itemprop="telephone">(408) 555-1212**


```

当向最终用户提供此搜索结果中时，将在结果中显示一个小型手机图标。 如果用户点击该图标，将调用指定的数字。

以下 HTML 要添加要播放音频文件从搜索结果的操作：

```xml
<div itemscope itemtype="http://schema.org/AudioObject">
    <span itemprop="contentUrl">http://company.com/appname/greeting.m4a**


```

最后，下面的 HTML 会添加操作以从搜索结果中获取方向：

```xml
<div itemscope itemtype="http://schema.org/PostalAddress">
    <span itemprop="streetAddress">1 Infinite Loop**
    <span itemprop="addressLocality">Cupertino**
    <span itemprop="addressRegion">CA**
    <span itemprop="postalCode">95014**


```

有关详细信息，请参阅 Apple[应用程序搜索开发人员网站](https://developer.apple.com/ios/search/)。



## <a name="related-links"></a>相关链接

- [iOS 9 示例](https://developer.xamarin.com/samples/ios/iOS9/)
- [面向开发人员的 iOS 9](https://developer.apple.com/ios/pre-release/)
- [iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [应用搜索编程指南](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/AppSearch/index.html#//apple_ref/doc/uid/TP40016308)
