---
title: 在 Xamarin.iOS 应用搜索增强功能
description: 本文介绍如何增强功能对应用程序搜索所做的 Apple 已在 iOS 10 及如何在 Xamarin.iOS 中实现它们。
ms.prod: xamarin
ms.assetid: 30124DB6-6A02-4F66-A2D9-BBC8008E6B48
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/15/2017
ms.openlocfilehash: e61cb20f12f6373ef57beb759b933d3dd9b6e76e
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61408108"
---
# <a name="app-search-enhancements-in-xamarinios"></a>在 Xamarin.iOS 应用搜索增强功能

_本文介绍如何增强功能对应用程序搜索所做的 Apple 已在 iOS 10 及如何在 Xamarin.iOS 中实现它们。_

在 iOS 10 中，Apple 对进行了多项改进应用搜索如众深度链接、 应用内搜索，搜索继续符和可视化效果的验证结果。 本文将介绍 Xamarin.iOS 应用程序中实现这些功能。

## <a name="about-app-search-enhancements"></a>有关应用搜索增强功能

核心聚焦在 iOS 10 如提供对应用程序搜索的多项增强功能：

- **众深层链接受欢迎程度 （与差异隐私）** -提供了一种方法来升级在搜索结果中的深层链接应用内容。
- **应用内搜索**-使用新`CSSearchQuery`类以提供应用程序内 Spotlight 搜索功能对邮件、 消息和说明应用程序的工作方式类似。
- **搜索延续**-用户可以启动搜索在聚焦或 Safari，然后打开应用并继续搜索。
- **验证结果的可视化效果**-Apple[应用搜索 API 验证工具](https://search.developer.apple.com/appsearch-validation-tool)时只有测试现在会显示网站的标记和深度链接的可视表示形式。
- **消息应用映像共享**-允许在消息 （通过消息应用扩展） 中的共享 Spotlight 搜索中显示为提供的常用应用内映像。

以下各节将介绍这些主题的更多详细信息。

## <a name="crowdsourced-deep-link-popularity"></a>众深层链接受欢迎程度

iOS 10 提供了一种机制来计数的频率受欢迎的深度链接到应用后跟用户，并使用此信息来改进应用排名的内容在搜索结果中，同时仍保护用户的标识使用*差异隐私*。

为应用程序的使用`NSUserActivity`对象，用于提供深层链接 Url，`EligibleForPublicIndexing`属性设置为`true`，iOS 10 提交的子集*差异隐私哈希*到 Apple 的服务器。 然后，此信息用于提升在搜索结果中的常用应用程序内内容。

在 Xamarin.iOS 应用程序中实现深度链接的详细信息，请参阅我们[搜索与 NSUserActivity](~/ios/platform/search/nsuseractivity.md)文档。

## <a name="in-app-searching"></a>应用内搜索

通过实现新[CSSearchQuery](https://developer.apple.com/reference/corespotlight/cssearchquery)类，应用程序可提供 Spotlight 搜索和匹配规则技术，以查找内部本身，而无需用户无需退出应用程序 （类似于如何将邮件、 消息和说明应用程序的内容起作用）。

通常情况下，应用程序的支持`CSSearchQuery`不需要维护其自身的、 单独的搜索索引。 

## <a name="search-continuation"></a>搜索继续符

在 iOS 9 中，Apple 引入了搜索 Api (如核心聚焦`NSUserActivity`和 web 标记) 以提供深度喜欢的应用程序以允许用户以搜索该内容使用聚焦和 Safari 搜索接口中的内容。 请参阅我们[新的搜索 Api](~/ios/platform/search/index.md)文档了解详细信息。

在 iOS 10 Apple 基于此功能通过允许用户以启动搜索在聚焦或 Safari，然后继续执行搜索，当用户打开的应用。 

若要实现此功能，编辑应用程序的`Info.plist`文件中，添加`CoreSpotlightContinuation`类型的密钥**布尔**并将其值设置为`YES`:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

[![](app-search-enhancements-images/search01.png "编辑 Info.plist 文件中的 CoreSpotlightContinuation")](app-search-enhancements-images/search01.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](app-search-enhancements-images/searchw01.png "编辑 Info.plist 文件中的 CoreSpotlightContinuation")](app-search-enhancements-images/search01.png#lightbox)

-----

以响应用户继续搜索结果 (`NSUserActivity`)，编辑`AppDelegate.cs`文件，然后重写`ContinueUserActivity`方法。 例如：

```csharp
public override bool ContinueUserActivity (UIApplication application, NSUserActivity userActivity, UIApplicationRestorationHandler completionHandler)
{

    // Take action based on the activity type
    switch (userActivity.ActivityType) {
    case "com.xamarin.platform":
        // Restore the state of the app here...
        break;
    default:
        if (userActivity.ActivityType == CSSearchQuery.ContinuationActionType) {
            var search = userActivity.UserInfo.KeyForValue(CSSearchQuery.QueryString);
            // Continue user's search here...
        }
        break;
    }

    return true;
}
```

此代码将查找查询的继续操作类型 (`userActivity.ActivityType == CSSearchQuery.ContinuationActionType`)，然后读取从用户的当前查询`NSUserActivity`类的用户信息字典 (`userActivity.UserInfo.KeyForValue(CSSearchQuery.QueryString)`)。 在这里，应用需要采取操作以继续用户的搜索。

有关使用 Xamarin.iOS 应用程序中的搜索的详细信息，请参阅我们[搜索与核心聚焦](~/ios/platform/search/corespotlight.md)文档。

## <a name="visualization-of-validation-results"></a>验证结果的可视化效果

Apple[应用程序搜索 API 验证工具](https://search.developer.apple.com/appsearch-validation-tool)现在将显示网站的标记和深度链接的可视表示形式 (包括如在定义的标记[Schema.org](http://schema.org/)) 时只有测试。

通过使用验证工具，开发人员就可以看到 Applebot Web 爬网程序已编制索引为如标题、 说明、 URL 和任何其他站点的信息支持的元素。

有关使用 Web 标记的详细信息，请参阅我们[搜索与 Web 标记](~/ios/platform/search/web-markup.md)文档。

## <a name="message-app-image-sharing"></a>共享的消息应用映像

如果消息应用扩展为在消息中的共享提供了映像，扩展可以配置为允许用户执行 Spotlight 搜索从消息中的常用映像，而无需退出应用程序。

若要启用此功能，请执行以下操作：

1. 创建消息应用扩展。
2. 添加`com.apple.developer.associated-domains`到应用的权利，包括承载共享消息应用扩展的映像的 web 域的列表。 对于每个域，指定`spotlight-image-search`服务。
3. 添加`apple-app-site-association`到的映像所在的网站的文件。 此文件包含用于字典`spotlight-image-search`服务并包含应用程序的 ID，即团队 ID 或应用 ID 前缀后跟捆绑包 id。 该文件可以包含最多 500 个路径和模式，将通过聚焦索引并包括在常用图像搜索中。 有关详细信息，请参阅 Apple[创建并上传关联文件](https://developer.apple.com/library/prerelease/content/documentation/General/Conceptual/AppSearch/UniversalLinks.html#//apple_ref/doc/uid/TP40016308-CH12-SW4)文档。
4. 允许 Applebot 进行爬网在网站上找到。 请参阅 Apple[有关 Applebot](https://support.apple.com/HT204683)文档。

请参阅我们[消息应用集成](~/ios/platform/message-app-integration/index.md)文档了解详细信息。

## <a name="summary"></a>总结

本文只讨论了增强功能对应用程序搜索所做的 Apple 已在 iOS 10 及如何在 Xamarin.iOS 中实现它们。



## <a name="related-links"></a>相关链接

- [iOS 10 示例](https://developer.xamarin.com/samples/ios/iOS10/)
