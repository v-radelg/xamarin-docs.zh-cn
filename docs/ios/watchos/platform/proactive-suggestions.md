---
title: watchOS 在 Xamarin 中的主动建议
description: 本文介绍如何在提高参与度的 watchOS 3 应用程序中使用主动建议，通过使系统能够主动地向用户自动地提供有用的信息。
ms.prod: xamarin
ms.assetid: 10CC9F16-963C-44F1-8B98-F09FB2310DFF
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: 0c0bf6058b2ec7a8e3ef606bef9f725a476abffe
ms.sourcegitcommit: 7ccc7a9223cd1d3c42cd03ddfc28050a8ea776c2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/13/2019
ms.locfileid: "67865921"
---
# <a name="watchos-proactive-suggestions-in-xamarin"></a>watchOS 在 Xamarin 中的主动建议

_本文介绍如何在提高参与度的 watchOS 3 应用程序中使用主动建议，通过使系统能够主动地向用户自动地提供有用的信息。_


新 watchOS 3，主动建议存在新闻方式供用户与 Xamarin.iOS 应用程序通过向用户自动以适当的次数的主动存在有用的信息。


## <a name="about-proactive-suggestions"></a>有关主动建议

WatchOS 3 的新手`NSUserActivity`包括`MapItem`允许此应用提供可以在其他上下文中使用的位置信息的属性。 例如，如果应用显示酒店介绍并提供了`MapItem`位置，如果用户刚切换到地图应用程序，只需查看的旅馆的位置应为可用。

应用程序公开此功能对系统使用一系列技术如`NSUserActivity`，MapKit、 Media Player 和 UIKit。 此外，通过为应用提供主动建议支持，它可以获取更深入的 Siri 集成免费。

## <a name="location-based-suggestions"></a>基于位置的建议

WatchOS 3 的新手`NSUserActivity`类包括`MapItem`属性，它允许开发人员提供可以在其他上下文中使用的位置信息。 例如，如果应用显示餐馆评论，开发人员可以设置`MapItem`属性设置为的应用中查看用户餐厅的位置。 如果用户切换到地图应用程序，餐馆的位置是自动可用。

如果应用支持应用程序搜索，它可以使用的新地址组件`CSSearchableItemAttributesSet`类，以指定用户可能想要访问的位置。 通过设置`MapItem`属性的其他属性是自动填入。

除了设置之外`Latitude`并`Longitude`组件的地址属性，则建议应用程序提供`NamedLocation`和`PhoneNumbers`属性，因此使用 Siri 可以启动到的位置的调用。

## <a name="contextual-siri-reminders"></a>上下文 Siri 提醒

允许使用 Siri 来快速使应用程序的提示指出要查看的内容晚些时候当前正在应用中查看用户。 例如，如果它们已在应用中查看餐馆评论，它们可能调用 Siri 和说 *"提醒我有关此当我回到家。"* 使用 Siri 会在应用中生成提醒，其中评审的链接。

## <a name="implementing-proactive-suggestions"></a>实现主动建议

添加主动建议向 Xamarin.iOS 应用程序的支持是通常作为实现的一些 Api 或扩展该应用程序可能正在实现的几个 Api 上一样简单。

主动建议使用以下三种主要方式应用：

- **`NSUserActivity`** -可帮助了解用户在屏幕当前正在使用哪些信息系统。
- **位置建议**-如果应用程序提供或使用基于位置的信息，这些 API 扩展产品/服务的新方法以在应用间共享此信息。

通过实现以下支持的应用中：

- **上下文 Siri 提醒**-在 iOS 10，`NSUserActivity`已扩展为允许使用 Siri 来快速使应用程序的提示指出要查看的内容晚些时候当前正在应用中查看。
- **位置建议**-增强了 iOS 10`NSUserActivity`捕获查看在应用内的位置并将它们提升在整个系统的多个位置。
- **上下文 Siri 请求** -  `NSUserActivity`为提供的信息在应用内使用 Siri，以便用户可以获得方向或位置调用是在应用中的调用 Siri 提供上下文。

所有这些功能都有一个共同点，它们都使用`NSUserActivity`中一个窗体或另一个用来提供相应的功能。 

## <a name="nsuseractivity"></a>NSUserActivity

如上所述，`NSUserActivity`帮助了解用户在屏幕当前正在使用哪些信息系统。 `NSUserActivity` 一种轻型状态缓存机制用户导航应用程序中捕获用户的活动。 例如，查看餐馆应用：

[![](proactive-suggestions-images/activity02.png "餐馆应用程序")](proactive-suggestions-images/activity02.png#lightbox)

使用以下对象交互：

1. 用户可用于应用，如`NSUserActivity`创建以重新创建应用程序更高版本的状态。
2. 如果一家餐厅搜索用户，则遵循同一模式，创建活动。
3. 同样，当用户查看结果。 在此最后一个的情况下，用户正在查看位置并在 iOS 10 中，系统是更好地识别的某些概念 （如位置或通信交互）。

仔细看一下最后一个屏幕：

[![](proactive-suggestions-images/activity03.png "NSUserActivity 有效负载")](proactive-suggestions-images/activity03.png#lightbox)

此处创建应用`NSUserActivity`和更高版本重新创建状态的信息填充。 应用程序还包含一些元数据位置的名称和地址等。 通过创建此活动，此应用程序允许 iOS 知道它表示用户的当前状态。

应用程序然后确定是否将播发无线以便提交、 另存为位置建议的临时值或添加到设备上 Spotlight 索引，用于显示搜索结果中的活动。

有关交接和 Spotlight 搜索的详细信息，请参阅我们[简介移交](~/ios/platform/handoff.md)并[iOS 9 新搜索 Api](~/ios/platform/search/index.md)指南。

### <a name="creating-an-activity"></a>创建活动

然后再创建一个活动，活动类型标识符将需要创建以将其标识。 活动类型标识符是添加到一个短字符串`NSUserActivityTypes`的应用程序的数组`Info.plist`用于唯一标识给定的用户的活动类型的文件。 为每个活动的应用程序支持，并公开给应用程序搜索数组中将有一个条目。 请参阅我们[创建活动类型的标识符引用](~/ios/platform/search/nsuseractivity.md)的更多详细信息。

在活动的示例所示：

```csharp
// Create App Activity
var activity = new NSUserActivity ("com.xamarin.platform");

// Define details
var info = new NSMutableDictionary ();
info.Add(new NSString("link"),new NSString("http://xamarin.com/platform"));

// Populate Activity
activity.Title = "The Xamarin Platform";
activity.UserInfo = info;

// Enable capabilities
activity.EligibleForSearch = true;
activity.EligibleForHandoff = true;
activity.EligibleForPublicIndexing = true;

// Inform system of Activity
activity.BecomeCurrent();
```

使用活动类型标识符创建一个新的活动。 接下来，以便可以在以后还原此状态下创建一些定义活动的元数据。 然后，该活动提供有意义的标题以及连接到的用户信息。 最后，一些功能已启用，并且该活动发送到系统。

上面的代码进一步得到增强，包括以下更改，从而提供到活动的上下文的元数据：

```csharp
...

// Provide context
var attributes = new CSSearchableItemAttributeSet ("com.xamarin.location");
attributes.ThumbnailUrl = myThumbnailURL;
attributes.Keywords = new string [] { "software", "mobile", "language" }; 
activity.ContentAttributeSet = attributes;

// Inform system of Activity
activity.BecomeCurrent();
```

如果开发人员能够显示与应用相同的信息的网站，该应用程序可以包括 URL 和可以在没有安装 （通过切换） 的应用程序的其他设备上显示内容：

```csharp
// Restore on the web
activity.WebPageUrl = new NSUrl("http://xamarin.com/platform");
```

### <a name="restoring-an-activity"></a>还原活动

以响应用户点击搜索结果 (`NSUserActivity`) 的应用程序中，编辑**AppDelegate.cs**文件，然后重写`ContinueUserActivity`方法。 例如:

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

确保此路径是相同的活动类型标识符 (`com.xamarin.platform`) 为上面创建的活动。 该应用使用的信息存储在`NSUserActivity`以返回到该用户离开的位置还原状态。

### <a name="benefits-of-creating-an-activity"></a>创建活动的优点

使用上面介绍的代码的最少工作量，应用程序现能够利用三个新的 iOS 10 功能：

- **Handoff**
- **Spotlight 搜索**
- **上下文 Siri 提醒**

以下部分将看看启用两个其他新的 iOS 10 功能：

- **位置建议**
- **上下文 Siri 请求**

### <a name="location-based-suggestions"></a>基于位置的建议 

需要更高版本的餐馆搜索应用程序示例。 如果已实现`NSUserActivity`和正确填充所有的元数据和属性，用户将能够执行以下操作：

1. 他们想要满足的朋友联系，在应用中找到一家餐厅。
2. 如果用户切换到地图应用程序，餐馆的地址自动建议作为目标。
3. 这甚至适用于第三方应用 (支持`NSUserActivity`)，因此用户可以切换到持续一段时间共享应用和餐馆的地址自动都还建议为存在的目标。
4. 它还提供了上下文到 Siri，这样用户可以调用 Siri 餐馆应用程序中并要求 *"获取方向..."* Siri 将提供给用户查看的餐馆的方向。

所有上述功能共同具有一件事，它们都指示建议的最初来源。 在上面的示例中，它是虚构的餐馆查看应用程序。

watchOS 3 已得到增强，以启用此功能通过多个小的修改，以及现有的框架的新增功能的应用：

- `NSUserActivity` 具有其他字段，用于捕获查看在应用内的位置信息。
- 多个新增功能已得到 MapKit 和 CoreSpotlight 捕获位置。
- 位置感知功能已添加到 Siri、 映射、 多任务和其他系统中的应用。

若要实现基于位置的建议，请使用上面介绍的相同活动代码开始：

```csharp
// Create App Activity
var activity = new NSUserActivity ("com.xamarin.platform");

// Define details
var info = new NSMutableDictionary ();
info.Add(new NSString("link"),new NSString("http://xamarin.com/platform"));

// Populate Activity
activity.Title = "The Xamarin Platform";
activity.UserInfo = info;

// Enable capabilities
activity.EligibleForSearch = true;
activity.EligibleForHandoff = true;
activity.EligibleForPublicIndexing = true;

// Provide context
var attributes = new CSSearchableItemAttributeSet ("com.xamarin.location");
attributes.ThumbnailUrl = myThumbnailURL;
attributes.Keywords = new string [] { "software", "mobile", "language" }; 
activity.ContentAttributeSet = attributes;

// Restore on the web
activity.WebPageUrl = new NSUrl("http://xamarin.com/platform");

// Inform system of Activity
activity.BecomeCurrent();
```

如果应用正在使用 MapKit，它只需添加当前映射`MKMapItem`到的活动：

```csharp
// Save MKMapItem location
activity.MapItem = myMapItem;
```

如果应用程序不使用 MapKit，它可以采用应用程序搜索并指定位置的以下新属性：

```csharp
// Provide context
var attributes = new CSSearchableItemAttributeSet ("com.xamarin.location");
...

attributes.NamedLocation = "Apple Inc.";
attributes.SubThoroughfare = "1";
attributes.Thoroughfare = "Infinite Loop";
attributes.City = "Cupertino";
attributes.StateOrProvince = "CA";
attributes.Country = "United States";
attributes.Latitude = 37.33072;
attributes.Longitude = 122.029674;
attributes.PhoneNumbers = new string[]{"(800) 275-2273"};
attributes.SupportsPhoneCalls = true;
attributes.SupportsNavigation = true;
```

看看上面的代码中详细信息。 首先，每个实例需要的位置的名称：

```csharp
attributes.NamedLocation = "Apple Inc.";
```

然后，根据的说明中所需的文本的文本基于实例 （例如 QuickType 键盘）：

```csharp
attributes.SubThoroughfare = "1";
attributes.Thoroughfare = "Infinite Loop";
attributes.City = "Cupertino";
attributes.StateOrProvince = "CA";
attributes.Country = "United States";
```

纬度和经度是可选的但可确保将用户路由到该应用程序想要将它们发送到的确切位置：

```csharp
attributes.Latitude = 37.33072;
attributes.Longitude = 122.029674;
```

通过设置电话号码，该应用程序可以访问使用 Siri 以便用户可以通过内容类似，从应用调用 Siri *"调用此位置":

```csharp
attributes.PhoneNumbers = new string[]{"(800) 275-2273"};
```

最后，应用可能表示该实例则适合用于导航和电话呼叫：

```csharp
attributes.SupportsPhoneCalls = true;
attributes.SupportsNavigation = true;
```
## <a name="activities-best-practices"></a>活动最佳做法

Apple 提供建议处理活动的以下最佳实践：

- 使用`NeedsSave`延迟有效负载更新。
- 请确保保留对当前活动的强引用。
- 仅传输小型包括刚好够用的信息以还原状态的有效负载。
- 确保活动类型标识符是唯一的描述性的使用反向 DNS 表示法来指定它们。 

## <a name="consuming-location-suggestions"></a>使用位置建议

此下一节将介绍使用来自系统 （如地图应用程序） 或其他第三方应用的其他部分的位置建议。

## <a name="routing-apps-and-locations-suggestions"></a>路由的应用程序和位置建议

本部分中将看看使用位置提供的建议直接路由的应用程序中。 若要添加此功能路由应用，开发人员将利用现有`MKDirectionsRequest`框架，如下所示：

- 若要升级的应用程序中的多任务。
- 若要将应用注册为路由的应用程序。
- 若要处理启动应用程序与 MapKit`MKDirectionsRequest`对象。
- 使 watchOS 能够了解建议应用程序中基于用户参与度。

应用程序入门 MapKit`MKDirectionsRequest`对象，它应会自动开始为提供的用户说明进行操作的请求的位置，或显示 UI，方便用户开始查询路线。 例如:


```csharp
using System;
using Foundation;
using UIKit;
using MapKit;
using CoreLocation;

namespace MonkeyChat
{
    [Register ("AppDelegate")]
    public class AppDelegate : UIApplicationDelegate, IUISplitViewControllerDelegate
    {
        ...
        
        public override bool OpenUrl (UIApplication app, NSUrl url, NSDictionary options)
        {
            if (MKDirectionsRequest.IsDirectionsRequestUrl (url)) {
                var request = new MKDirectionsRequest (url);
                var coordinate = request.Destination?.Placemark.Location?.Coordinate;
                var address = request.Destination.Placemark.AddressDictionary;
                if (coordinate.IsValid()) {
                    var geocoder = new CLGeocoder ();
                    geocoder.GeocodeAddress (address, (place, err) => {
                        // Handle the display of the address

                    });
                }
            }

            return true;
        }
    }       
}
```

看看此代码中详细信息。 它进行测试以了解它是否是有效的目标请求：

```csharp
if (MKDirectionsRequest.IsDirectionsRequestUrl(url)) {
```

如果是，则它会创建`MKDirectionsRequest`从 URL:

```csharp
var request = new MKDirectionsRequest(url);
```

新 watchOS 3，在应用程序可以发送不具有地理坐标中导致开发人员需要编码地址的地址：

```csharp
var geocoder = new CLGeocoder();
geocoder.GeocodeAddress(address, (place, err)=> {
    // Handle the display of the address
    
});

```

## <a name="summary"></a>总结

本文已涵盖主动建议，并介绍了如何开发人员可以使用它们的 Xamarin.iOS 应用程序为 watchOS。 它涉及的步骤来实现主动建议，并提供使用指南。


## <a name="related-links"></a>相关链接

- [watchOS 示例](https://developer.xamarin.com/samples/watchos/all/)
- [SiriKit 编程指南](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/index.html)
