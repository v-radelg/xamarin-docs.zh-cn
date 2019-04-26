---
title: 使用 Xamarin.iOS 中 NSUserActivity 搜索
description: 本文档介绍如何将索引 NSUserActivity，使其可搜索在聚焦和 Safari。 介绍了如何响应在搜索结果中 NSUserActivity 的选择。
ms.prod: xamarin
ms.assetid: 0B28B284-C7C9-4C0D-A782-D471FBBC4CAE
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/20/2017
ms.openlocfilehash: b853bd3bc55a2d4cb613a9f0079aebae9f57027b
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61357016"
---
# <a name="search-with-nsuseractivity-in-xamarinios"></a>使用 Xamarin.iOS 中 NSUserActivity 搜索

`NSUserActivity` 在 iOS 8 中引入，用于为提交提供数据。
它允许你在应用程序，然后可传递给不同的 iOS 设备上运行的应用程序的另一个实例特定部分中创建的活动。 接收设备然后可以继续在上一设备，选择上一次用户离开的位置上启动的活动。 有关使用切换的详细信息，请参阅我们[简介切换](~/ios/platform/handoff.md)文档。

为 iOS 9，新`NSUserActivity`可以建立索引 （公开和私有） 和搜索从 Spotlight 搜索和 Safari。 通过将标记`NSUserActivity`为可搜索和添加可编制索引的元数据，可以在 iOS 设备上的搜索结果中列出的活动。

[![](nsuseractivity-images/apphistory01.png "应用历史记录概述")](nsuseractivity-images/apphistory01.png#lightbox)

如果用户选择从你的应用属于活动的搜索结果，将启动应用程序以及在活动中说明的`NSUserActivity`将重新启动并向用户显示。

下列属性`NSUserActivity`用于支持应用搜索：

 - `EligibleForHandoff` -如果`true`，可以切换操作中使用此活动。
 - `EligibleForSearch` -如果`true`，此活动将添加到设备上的索引，并在搜索结果中显示。
 - `EligibleForPublicIndexing` -如果`true`，此活动将添加到 Apple 的基于云的索引，并提供给具有在其 iOS 设备尚未安装您的应用程序的用户 （通过搜索）。 请参阅[公共搜索索引](#public-search-indexing)部分获取更多详细信息。
 - `Title` -为您的活动提供标题，并在搜索结果中显示。 用户还可以搜索标题本身的文本。
 - `Keywords` – 是一个用来描述您的活动将建立索引并进行最终用户可搜索的字符串数组。
 - `ContentAttributeSet` – 是`CSSearchableItemAttributeSet`用来进一步描述您的活动详细信息和提供丰富搜索结果中的内容。
 - `ExpirationDate` – 如果你想用于才会显示到给定日期的活动，可以提供此处该日期。
 - `WebpageURL` – 如果可以在 web 上查看该活动，或者如果您的应用程序支持 Safari 的深层链接，可以设置以访问此处的链接。

## <a name="nsuseractivity-quickstart"></a>NSUserActivity 快速入门

按照这些说明来实现的可搜索`NSUserActivity`应用程序中：

- [创建活动类型标识符](#creatingtypeid)
- [创建活动](#createactivity)
- [响应的活动](#respondactivity)
- [公共搜索索引](#indexing)

有一些[其他好处](#benefits)使用`NSUserActivity`使内容可搜索。

<a name="creatingtypeid" />

## <a name="creating-activity-type-identifiers"></a>创建活动类型标识符

创建搜索活动之前，你将需要创建_活动类型标识符_以将其标识。 活动类型标识符是添加到一个短字符串`NSUserActivityTypes`的应用程序的数组**Info.plist**用于唯一标识给定的用户的活动类型的文件。 为每个活动的应用程序支持，并公开给应用程序搜索数组中将有一个条目。 

Apple 建议使用活动类型标识符的反向 DNS 样式表示法以避免冲突。 例如：`com.company-name.appname.activity`对于特定的应用程序基于活动或`com.company-name.activity`，可以跨多个应用运行的活动。

在创建时使用的活动类型标识符`NSUserActivity`实例以确定活动的类型。 当用户点击搜索结果的结果作为继续执行活动时，活动类型 （以及应用程序的团队 ID) 确定哪些应用才能继续活动。

若要创建所需的活动类型标识符，以支持此行为，请编辑**Info.plist**文件，并切换到**源**视图。 添加`NSUserActivityTypes`密钥，然后创建标识符采用以下格式：

[![](nsuseractivity-images/type01.png "NSUserActivityTypes 密钥和 plist 编辑器中的所需的标识符")](nsuseractivity-images/type01.png#lightbox)

在上述示例中，我们创建一个新的活动类型标识符搜索活动 (`com.xamarin.platform`)。 创建你自己的应用时, 的内容替换为`NSUserActivityTypes`您的应用程序使用特定于活动的活动类型标识符数组支持。

<a name="createactivity" />

## <a name="creating-an-activity"></a>创建活动

下面是创建用于托管的设备上的索引搜索的活动的示例：

```csharp
// Create App Search Activity
var activity = new NSUserActivity ("com.xamarin.platform");

// Define details
var info = new NSMutableDictionary ();
info.Add(new NSString("link"),new NSString("http://xamarin.com/platform"));

// Populate Activity
activity.Title = "The Xamarin Platform";
activity.UserInfo = info;

// Add App Search ability
activity.EligibleForSearch = true;
activity.BecomeCurrent();
```

我们可以添加更多详细信息通过设置`ContentAttributeSet`属性的我们`NSUserActivity`，如下所示：

[![](nsuseractivity-images/apphistory02.png "添加搜索详细信息概述")](nsuseractivity-images/apphistory02.png#lightbox)

通过使用`ContentAttributeSet`可以创建丰富的搜索结果，诱使最终用户与之交互。

<a name="respondactivity" />

## <a name="responding-to-an-activity"></a>响应的活动

以响应用户点击搜索结果 (`NSUserActivity`) 对于我们的应用程序，编辑**AppDelegate.cs**文件，然后重写`ContinueUserActivity`方法。 例如：

```csharp
public override bool ContinueUserActivity (UIApplication application, NSUserActivity userActivity, UIApplicationRestorationHandler completionHandler)
{

    // Take action based on the activity type
    switch (userActivity.ActivityType) {
    case "com.xamarin.platform":
        // Restore the state of the app here...
        break;
    }

    return true;
}
```

请注意，这是用于响应提交请求的相同方法重写。 现在如果用户单击从我们的 Spotlight 搜索结果中的应用程序的链接，将转至前台 （或如果尚未运行，启动） 我们的应用程序并将显示的内容、 导航或该链接表示的功能：

[![](nsuseractivity-images/apphistory03.png "从搜索还原以前的状态")](nsuseractivity-images/apphistory03.png#lightbox)

<a name="indexing" />

## <a name="public-search-indexing"></a>公共搜索索引

如上面看到的 iOS 9 轻松以提供对应用程序内容和功能，用户已发现并使用给定的 iOS 设备上的搜索访问。 使用公共索引，iOS 9 提供一种用户人员尚未尚未发现的内容或功能 （或甚至尚未安装该应用程序） 也在其搜索中获取这些结果。

公共索引编制的工作原理如下所示：

1. 为您的应用程序创建活动时可以将其标记为公用。
2. 公共活动发送到 Apple，并在云中创建索引。
3. 在更多的用户与您的设备上的应用程序进行交互和使用的特定功能或由活动的内容，它将随相对排名。
4. 常用公共结果可向其他用户，即使他们没有安装的应用程序。
5. 这些公共结果将显示在 Spotlight 搜索和 Safari （如果该活动将包含一个 URL）。

我们可以获取更高版本，我们创建的专用搜索活动，并展开它都是公共的：

```csharp
// Create App Search Activity
var activity = new NSUserActivity ("com.xamarin.platform");

// Define details
var info = new NSMutableDictionary ();
info.Add(new NSString("link"),new NSString("http://xamarin.com/platform"));

// Populate Activity
activity.Title = "The Xamarin Platform";
activity.UserInfo = info;

// Add App Search ability
activity.EligibleForSearch = true;
activity.EligibleForPublicIndexing = true;
activity.BecomeCurrent();
```

只是因为活动已设置为通过设置公共索引`EligibleForPublicIndexing = true`，这并不意味着，它将自动添加到 Apple 的公有云索引。 必须首先满足以下条件：

1. 它必须出现在搜索结果中，并选择多个用户。 结果保持专用，直到满足活动 engagement 阈值。
2. 应用预配可防止从正在编制索引并使其成为公共任何特定于用户的数据。

<a name="benefits" />

## <a name="additional-benefits"></a>其他权益

通过采用通过应用搜索`NSUserActivity`还在应用中，获取以下功能：

- **Handoff** -由于应用搜索所公开的内容，导航和/或功能切换为使用相同的机制 (`NSUserActivity`)，可以轻松地允许应用程序的用户在一台设备上启动活动并继续在另一台。
- **使用 Siri 建议**-Siri 建议通常使标准建议，以及从您的应用程序的在职员工可以自动建议。
- **使用 Siri 智能提醒**-用户将能够提出 Siri 提醒关于您的应用程序中的活动。



## <a name="related-links"></a>相关链接

- [iOS 9 示例](https://developer.xamarin.com/samples/ios/iOS9/)
- [面向开发人员的 iOS 9](https://developer.apple.com/ios/pre-release/)
- [iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [应用搜索编程指南](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/AppSearch/index.html#//apple_ref/doc/uid/TP40016308)
- [博客文章和示例](https://blog.xamarin.com/improve-discoverability-with-search-in-ios-9/)
