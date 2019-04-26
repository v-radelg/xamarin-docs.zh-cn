---
title: 使用 Xamarin.iOS 中的核心 Spotlight 搜索
description: 本文档介绍如何在 Xamarin.iOS 应用程序中使用核心聚焦提供在应用程序内容的链接。 它讨论了如何创建、 还原、 更新和删除可搜索项。
ms.prod: xamarin
ms.assetid: 1374914C-0F63-41BF-BD97-EBCEE86E57B1
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/20/2017
ms.openlocfilehash: fb9ddcc39bd33199dc370897250cd0d74597612f
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61248454"
---
# <a name="search-with-core-spotlight-in-xamarinios"></a>使用 Xamarin.iOS 中的核心 Spotlight 搜索

核心聚焦是新框架，用于 iOS 9，其中的一个类似于数据库的 API，可添加、 编辑或删除您的应用程序中的内容的链接。 使用核心聚焦已添加的项目都将显示 iOS 设备上使用 Spotlight 搜索。

有关可使用核心聚焦编制索引的内容类型的示例，请查看 Apple 的消息、 邮件、 日历和说明应用程序。 目前，它们都使用核心聚焦来提供搜索结果。

## <a name="creating-an-item"></a>创建项

下面是创建项目和对其使用核心聚焦进行索引的一个示例：

```csharp
using CoreSpotlight;
...

// Create attributes to describe an item
var attributes = new CSSearchableItemAttributeSet();
attributes.Title = "App Center Test";
attributes.ContentDescription = "Automatically test your app on 1,000 devices in the cloud.";

// Create item
var item = new CSSearchableItem ("1", "products", attributes);

// Index item
CSSearchableIndex.DefaultSearchableIndex.Index (new CSSearchableItem[]{ item }, (error) => {
    // Successful?
    if (error !=null) {
        Console.WriteLine(error.LocalizedDescription);
    }
});
```

此信息会显示如下的搜索结果中所示：

[![](corespotlight-images/corespotlight01.png "核心聚焦搜索结果概述")](corespotlight-images/corespotlight01.png#lightbox)

## <a name="restoring-an-item"></a>还原的项目

当用户点击某项添加到你的应用，通过核心聚焦的搜索结果`AppDelegate`方法`ContinueUserActivity`称为 (此方法还用于`NSUserActivity`)。 例如：

```csharp
public override bool ContinueUserActivity (UIApplication application,
   NSUserActivity userActivity, UIApplicationRestorationHandler completionHandler)
{

    // Take action based on the activity type
    switch (userActivity.ActivityType) {
    case "com.xamarin.platform":
        // Restore the state of the app here...
        break;
    default:
        if (userActivity.ActivityType == CSSearchableItem.ActionType.ToString ()) {
            // Display content for searchable item...
        }
        break;
    }

    return true;
}
```

请注意，这次我们检查活动 having`ActivityType`的`CSSearchableItem.ActionType`。

## <a name="updating-an-item"></a>更新项

可能有我们创建了有了核心 Spotlight 索引项需要进行修改，如标题或缩略图中的更改是必需的。 若要进行此更改，我们使用相同的方法，又用于最初创建索引。
我们创建一个新`CSSearchableItem`使用相同的 ID，因为用于创建该项目并附加一个新`CSSearchableItemAttributeSet`包含已修改的属性：

[![](corespotlight-images/corespotlight02.png "更新项概述")](corespotlight-images/corespotlight02.png#lightbox)

当此项写入可搜索索引时，使用新信息更新现有项目。

## <a name="deleting-an-item"></a>删除项

核心聚焦提供多种方式来在不再需要时删除索引项。

首先，在可以删除项按标识符，例如：

```csharp
// Delete Items by ID
CSSearchableIndex.DefaultSearchableIndex.Delete(new string[]{"1","16"},(error) => {
    // Successful?
    if (error !=null) {
        Console.WriteLine(error.LocalizedDescription);
    }
});
```

接下来，您可以通过其域名删除一组索引项。 例如：

```csharp
// Delete by Domain Name
CSSearchableIndex.DefaultSearchableIndex.DeleteWithDomain(new string[]{"domain-name"},(error) => {
    // Successful?
    if (error !=null) {
        Console.WriteLine(error.LocalizedDescription);
    }
});
```

最后，可以删除所有索引项使用以下代码：

```csharp
// Delete all index items
CSSearchableIndex.DefaultSearchableIndex.DeleteAll((error) => {
    // Successful?
    if (error !=null) {
        Console.WriteLine(error.LocalizedDescription);
    }
});
```
## <a name="additional-core-spotlight-features"></a>其他核心聚焦功能

核心聚焦具有以下功能，可帮助保持准确和最新的索引：

- **批处理更新支持**– 如果您的应用程序需要创建或修改索引的一大组在同一时间，则整个批次可以发送到`Index`方法的`CSSearchableIndex`中一次调用的类。
- **响应的索引更改**– 使用`CSSearchableIndexDelegate`您的应用程序可以更改和通知响应来自可搜索索引。
- **应用数据保护**– 使用数据保护类，您可以实现安全添加到使用核心聚焦的可搜索索引的项上。



## <a name="related-links"></a>相关链接

- [iOS 9 示例](https://developer.xamarin.com/samples/ios/iOS9/)
- [面向开发人员的 iOS 9](https://developer.apple.com/ios/pre-release/)
- [iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [应用搜索编程指南](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/AppSearch/index.html#//apple_ref/doc/uid/TP40016308)
