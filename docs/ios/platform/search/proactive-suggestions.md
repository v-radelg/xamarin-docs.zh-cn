---
title: 主动建议在 Xamarin.iOS 简介
description: 本文介绍如何通过使系统能够主动地向用户自动地提供有用的信息提高参与度的 Xamarin.iOS 应用中使用主动提供的建议。
ms.prod: xamarin
ms.assetid: 8DDD084A-0D1E-4DF7-B686-6309DCEFF5D3
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: 2ab0147f918b36dc47ef6eed7d9bf1b6295d9733
ms.sourcegitcommit: 3489c281c9eb5ada2cddf32d73370943342a1082
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2019
ms.locfileid: "58870399"
---
# <a name="introduction-to-proactive-suggestions-in-xamarinios"></a>主动建议在 Xamarin.iOS 简介

_本文介绍如何通过使系统能够主动地向用户自动地提供有用的信息提高参与度的 Xamarin.iOS 应用中使用主动提供的建议。_

新 iOS 10，主动建议存在新闻方式供用户与 Xamarin.iOS 应用程序通过向用户自动以适当的次数的主动存在有用的信息。

iOS 10 展示驾驶参与到应用的新方法使系统能够主动有用的信息自动向用户显示在适当的时间。 只需为 iOS 9 提供使用 Spotlight、 切换和 Siri 建议向应用添加深层搜索的功能 (请参阅[新的搜索 Api](~/ios/platform/search/index.md))，应用可以使用 iOS 10 公开可以提供给用户系统中的功能以下位置：

- 应用程序切换器
- 在锁定屏幕
- CarPlay
- 映射
- 使用 Siri 交互
- QuickType 建议

应用程序公开此功能对系统使用一系列技术如`NSUserActivity`，web 标记中，核心聚焦、 MapKit、 Media Player 和 UIKit。 此外，通过为应用提供主动建议支持，它可以获取更深入的 Siri 集成免费。

## <a name="location-based-suggestions"></a>基于位置的建议

IOS 10 中，为新`NSUserActivity`类包括`MapItem`属性，它允许开发人员提供可以在其他上下文中使用的位置信息。 例如，如果应用显示餐馆评论，开发人员可以设置`MapItem`属性设置为的应用中查看用户餐厅的位置。 如果用户切换到地图应用程序，餐馆的位置是自动可用。

如果应用支持应用程序搜索，它可以使用的新地址组件`CSSearchableItemAttributesSet`类，以指定用户可能想要访问的位置。 通过设置`MapItem`属性的其他属性是自动填入。

除了设置之外`Latitude`并`Longitude`组件的地址属性，则建议应用程序提供`NamedLocation`和`PhoneNumbers`属性，因此使用 Siri 可以启动到的位置的调用。

## <a name="web-markup-based-suggestions"></a>基于 web 标记建议

iOS 9 添加到功能丰富了用户在聚焦和 Safari 搜索结果中看到的内容的网站中包含结构化的数据标记 (请参阅[搜索与 Web 标记](~/ios/platform/search/web-markup.md))。 iOS 10 添加了包括基于位置的标记的功能 (如[邮政地址](http://schema.org/PostalAddress)定义的[Schema.org](http://schema.org/)) 以进一步增强用户体验。 例如，如果用户视图的位置标记为网站上的页面，系统可以建议将打开地图时相同的位置。

## <a name="text-based-suggestions"></a>基于文本的建议

已在 iOS 10，以包括扩展 UIKit [TextContentType](https://developer.apple.com/reference/uikit/uitextinputtraits/1649656-textcontenttype)的属性[UITextInputTraits](https://developer.apple.com/reference/uikit/uitextinputtraits)类，以指定文本区域中的内容的语义含义。 使用此信息后，系统可以通常会自动选择相应的键盘类型、 改进自动更正建议和主动集成来自其他应用程序和网站的信息。

例如，如果用户标记的文本字段中输入文本`UITextContentType.FullStreetAddress`，系统可以建议自动填充该字段与最近查看用户的位置。

## <a name="media-based-suggestions"></a>基于介质的建议

如果应用程序可以播放媒体使用[MPPlayableContentManager](xref:MediaPlayer.MPPlayableContentManager) API，iOS 10 允许用户查看唱片集画面和锁定屏幕上播放通过应用的媒体。

## <a name="contextual-siri-reminders"></a>上下文 Siri 提醒

允许使用 Siri 来快速使应用程序的提示指出要查看的内容晚些时候当前正在应用中查看用户。 例如，如果它们已在应用中查看餐馆评论，它们可能调用 Siri 和说 *"提醒我有关此当我回到家。"* 使用 Siri 会在应用中生成提醒，其中评审的链接。

## <a name="contact-based-suggestions"></a>请联系基于建议

允许此应用的联系人 （和相关的联系信息） 显示在**联系**存储在系统中所有用户的联系人以及在 iOS 设备上的应用。

## <a name="ride-sharing-based-suggestions"></a>共享的骑行基于建议

如果持续一段时间共享应用程序使用[MKDirectionsRequest](xref:MapKit.MKDirectionsRequest) API，iOS 10 会将其显示为应用程序切换器中的一个选项有时如果用户可能会想滑水感觉的时间。 应用程序还必须注册为拼车应用通过指定`MKDirectionsModeRideShare`有关[MKDirectionsApplicationSupportedModes](https://developer.apple.com/library/content/documentation/General/Reference/InfoPlistKeyReference/Articles/iPhoneOSKeys.html)键中其`Info.plist`文件。

如果应用仅支持共享持续一段时间，将以系统建议 *"获取滑水感觉的时间到..."*，如果支持其他类型的路由方向 （如 Walking 或自行车），则系统将使用 *"获取方向到..."*

> [!IMPORTANT]
> [MKMapItem](xref:MapKit.MKMapItem)对象，该应用程序将收到对象可能不包括经度和纬度的信息，并且将需要地理编码。

## <a name="implementing-proactive-suggestions"></a>实现主动建议

添加主动建议向 Xamarin.iOS 应用程序的支持是通常作为实现的一些 Api 或扩展该应用程序可能正在实现的几个 Api 上一样简单。

主动建议使用以下三种主要方式应用：

- **`NSUserActivity` 和 Schema.org**  -  `NSUserActivity`帮助了解用户在屏幕当前正在使用哪些信息系统。 Schema.org 将添加到网页功能是类似的。
- **位置建议**-如果应用程序提供或使用基于位置的信息，这些 API 扩展产品/服务的新方法以在应用间共享此信息。
- **媒体的应用建议**-系统时，才能升级应用程序和其媒体内容基于上下文的 iOS 设备的用户的交互。

通过实现以下支持的应用中：

- **Handoff**  -  `NSUserActivity`已开始支持移交，允许开发人员在一个设备上启动活动，然后继续在另一台 iOS 8 (请参阅[简介切换](~/ios/platform/handoff.md))。
- **Spotlight 搜索**-iOS 9 添加了使用 Spotlight 搜索结果中推广应用内容`NSUserActivity`(请参阅[使用核心聚焦搜索](~/ios/platform/search/corespotlight.md))。
- **上下文 Siri 提醒**-在 iOS 10，`NSUserActivity`已扩展为允许使用 Siri 来快速使应用程序的提示指出要查看的内容用户当前查看的应用中在更高版本的日期。
- **位置建议**-增强了 iOS 10`NSUserActivity`捕获查看在应用内的位置并将它们提升在整个系统的多个位置。
- **上下文 Siri 请求** -  `NSUserActivity`为提供的信息在应用内使用 Siri，以便用户可以获得方向或位置调用是在应用中的调用 Siri 提供上下文。
- **请联系交互**-在 iOS 10 中新`NSUserActivity`允许通信应用程序从作为备用的通信方法联系人卡 （在联系人应用程序） 升级。

所有这些功能都有一个共同点，它们都使用`NSUserActivity`中一个窗体或另一个用来提供相应的功能。 

## <a name="nsuseractivity"></a>NSUserActivity

如上所述，`NSUserActivity`帮助了解用户在屏幕当前正在使用哪些信息系统。 `NSUserActivity` 一种轻型状态缓存机制用户导航应用程序中捕获用户的活动。 例如，查看餐馆应用：

[![](proactive-suggestions-images/activity02.png "缓存机制 NSUserActivity 轻型状态")](proactive-suggestions-images/activity02.png#lightbox)

使用以下对象交互：

1. 用户可用于应用，如`NSUserActivity`创建以重新创建应用程序更高版本的状态。
2. 如果一家餐厅搜索用户，则遵循同一模式，创建活动。
3. 同样，当用户查看结果。 在此最后一个的情况下，用户正在查看位置并在 iOS 10 中，系统是更好地识别的某些概念 （如位置或通信交互）。

仔细看一下最后一个屏幕：

[![](proactive-suggestions-images/activity03.png "NSUserActivity 详细信息")](proactive-suggestions-images/activity03.png#lightbox)

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

以响应用户点击搜索结果 (`NSUserActivity`) 的应用程序中，编辑**AppDelegate.cs**文件，然后重写`ContinueUserActivity`方法。 例如：

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

开发人员将需要确保此路径是相同的活动类型标识符 (`com.xamarin.platform`) 为上面创建的活动。 该应用使用的信息存储在`NSUserActivity`以返回到该用户离开的位置还原状态。

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
2. 当用户移动离开应用程序使用的多任务处理应用程序切换器时，系统会自动显示建议 （位于屏幕的底部） 以获取到餐馆使用其最喜欢的导航布局应用程序的说明操作。
3. 如果用户切换到消息应用程序并开始键入 *"让我们在符合"*，QuickType 键盘将自动建议粘贴餐馆的地址。
4. 如果用户切换到地图应用程序，餐馆的地址自动建议作为目标。
5. 这甚至适用于第三方应用 (支持`NSUserActivity`)，因此用户可以切换到持续一段时间共享应用和餐馆的地址自动都还建议为存在的目标。
6. 它还提供了上下文到 Siri，这样用户可以调用 Siri 餐馆应用程序中并要求 *"获取方向..."* Siri 将提供给用户查看的餐馆的方向。

所有上述功能共同具有一件事，它们都指示建议的最初来源。 在上面的示例中，它是虚构的餐馆查看应用程序。

iOS 10 已得到增强，以启用此功能通过多个小的修改，以及现有的框架的新增功能的应用：

- `NSUserActivity` 具有其他字段，用于捕获查看在应用内的位置信息。
- 多个新增功能已得到 MapKit 和 CoreSpotlight 捕获位置。
- 位置感知功能已添加到 Siri、 映射、 键盘、 多任务和其他系统中的应用。

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

位置的基于文本的说明，则需要为基于文本的实例 （例如 QuickType 键盘）：

```csharp
attributes.SubThoroughfare = "1";
attributes.Thoroughfare = "Infinite Loop";
attributes.City = "Cupertino";
attributes.StateOrProvince = "CA";
attributes.Country = "United States";
```

纬度和经度是可选的但可确保将用户路由到该应用程序想要将它们发送到，因此它应包含的确切位置：

```csharp
attributes.Latitude = 37.33072;
attributes.Longitude = 122.029674;
```

通过设置电话号码，该应用程序可以访问使用 Siri 以便用户可以通过内容类似，从应用调用 Siri *"调用该邻居"*:

```csharp
attributes.PhoneNumbers = new string[]{"(800) 275-2273"};
```

最后，应用可能表示该实例则适合用于导航和电话呼叫：

```csharp
attributes.SupportsPhoneCalls = true;
attributes.SupportsNavigation = true;
```

### <a name="implementing-contact-interactions"></a>实现联系交互

新 ios 10，通信应用是紧密地集成到中的联系人卡片的联系人应用程序。 对于已实现联系人交互的应用程序，用户可以将给定的应用的信息添加到其联系人中的特定人员。 如果，例如，它们达到顶部的卡发送一条消息的快速操作按钮，将作为一个选项，若要从其发送消息列出附加的应用。

如果选择了第三方应用程序，它可以记住并显示为消息的给定的人员在的下次用户想要与他们联系的默认方法。

联系人的交互实现在应用中使用`NSUserActivity`并在 iOS 10 中引入的新意向框架。 有关如何使用意向的更多详细信息，请参阅我们[了解 SiriKit 概念](~/ios/platform/sirikit/understanding-sirikit.md)并[实现 SiriKit](~/ios/platform/sirikit/implementing-sirikit.md)指南。

#### <a name="donating-interactions"></a>捐赠的交互

看看如何应用程序可以指定用于交互：

[![](proactive-suggestions-images/activity04.png "捐赠交互概述")](proactive-suggestions-images/activity04.png#lightbox)

应用创建`INInteraction`对象，其中包含**意向**(`INIntent`)，**参与者**并**元数据**。 **意向**代表如发起视频呼叫或发送短信的用户操作。 **参与者**包括接收通信的人员。 **元数据**定义其他信息，例如已成功发送的消息，等等。

开发人员永远不会直接创建的实例`INIntent`或`INIntentResponse`，它们会将一个 （基于应用程序代表用户完成的任务） 的特定子类别，从这些父类继承。 例如，`INSendMessageIntent`和`INSendMessageIntentResponse`用于发送短信。 

一旦已完全填充交互，调用`DonateInteraction`方法以通知的交互是可供使用的系统。

交互时用户与联系人卡片中的应用程序交互时，获取与捆绑`NSUserActivity`，然后用于启动该应用程序：

[![](proactive-suggestions-images/activity05.png "获取与用于启动该应用程序 NSUserActivity 捆绑在交互")](proactive-suggestions-images/activity05.png#lightbox)

看看下面的示例发送消息意图的：

```csharp
using System;
using Foundation;
using UIKit;
using Intents;

namespace MonkeyNotification
{
    public class DonateInteraction
    {
        #region Constructors
        public DonateInteraction ()
        {
        }
        #endregion

        #region Public Methods
        public void SendMessageIntent (string text, INPerson from, INPerson[] to)
        {

            // Create App Activity
            var activity = new NSUserActivity ("com.xamarin.message");

            // Define details
            var info = new NSMutableDictionary ();
            info.Add (new NSString ("message"), new NSString (text));

            // Populate Activity
            activity.Title = "Sent MonkeyChat Message";
            activity.UserInfo = info;

            // Enable capabilities
            activity.EligibleForSearch = true;
            activity.EligibleForHandoff = true;
            activity.EligibleForPublicIndexing = true;

            // Inform system of Activity
            activity.BecomeCurrent ();

            // Create message Intent
            var intent = new INSendMessageIntent (to, text, "", "MonkeyChat", from);

            // Create Intent Reaction
            var response = new INSendMessageIntentResponse (INSendMessageIntentResponseCode.Success, activity);

            // Create interaction
            var interaction = new INInteraction (intent, response);

            // Donate interaction to the system
            interaction.DonateInteraction ((err) => {
                // Handle donation error
                ...
            });
        }
        #endregion
    }
}
```

查看详细信息中的此代码时，它会创建并填充的实例`NSUserActivity`(如中所示[创建活动](#creating-an-activity)上面部分)。 接下来，它创建的实例`INSendMessageIntent`(它是从`INIntent`) 并使用要发送的消息的详细信息填充它：

```csharp
var intent = new INSendMessageIntent (to, text, "", "MonkeyChat", from);
```

`INSendMessageIntentResponse`创建并传递`NSUserActivity`上面创建：

```csharp
var response = new INSendMessageIntentResponse (INSendMessageIntentResponseCode.Success, activity);
```

`INInteraction`发送消息意向从生成 (`INSendMessageIntent`) 和响应 (`INSendMessageIntentResponse`) 只需创建：

```csharp
var interaction = new INInteraction (intent, response);
```

最后，系统是交互的通知：

```csharp
// Donate interaction to the system
interaction.DonateInteraction ((err) => {
    // Handle donation error
    ...
});
```

完成处理程序传入应用程序可以响应捐赠上成功或失败。

### <a name="activities-best-practices"></a>活动最佳做法

Apple 提供建议处理活动的以下最佳实践：

- 使用`NeedsSave`延迟有效负载更新。
- 请确保保留对当前活动的强引用。
- 仅传输小型包括刚好够用的信息以还原状态的有效负载。
- 确保活动类型标识符是唯一的描述性的使用反向 DNS 表示法来指定它们。 

## <a name="schemaorg"></a>Schema.org

如上所示，`NSUserActivity`帮助了解用户在屏幕当前正在使用哪些信息系统。 Schema.org 将添加到网页功能是类似的。

Schema.org 可以提供相同类型的基于位置到网站的交互。 Apple 设计新的位置建议，若要在本机应用程序中一样在 Safari 中查看时也运行。

Schema.org 一些背景信息：

- 它提供了一种开放的 web 标记词汇标准。
- 它可通过包含在网页上的结构化元数据。
- 有 500 多个架构表示可用的各种概念。
- 通过实现其网站上，开发人员可以获得一些使用的好处`NSUserActivity`本机应用程序中。

架构在像结构，特定于类型如树中排列*餐馆*，如从更通用的类型继承*当地营业*。 有关详细信息，请参阅[Schema.org](http://schema.org)。

例如，如果 web 页包含以下数据：

```xml
<script type="application/ld+json>
{
    "@context":"http://schema.org",
    "@type":"Restaurant",
    "telephone":"(415) 781-1111",
    "url":"https://www.yanksing.com",
    "address":{
        "@type":"PostalAddress",
        "streetAddress":"101 Spear St",
        "addressLocality":"San Francisco",
        "postalCode":"94105",
        "addressRegion":"CA"
    },
    "aggregateRating":{
        "@type":"AggregateRating",
        "ratingValue":"3.5",
        "reviewCount":"2022"
    }
}
</script>
```

如果用户查看过此页在 Safari 中，然后切换到另一个应用，将捕获位置信息的页面，并将其作为提供位置建议在系统的其他部分 （如上面的活动中所示） 中。

Safari 将提取符合以下架构属性的任何 web 页面上的所有内容：

- **PostalAddress**
- **GeoCoordinates**
- 电话属性中。

有关详细信息，请参阅我们[搜索与 Web 标记](~/ios/platform/search/web-markup.md)指南。

## <a name="consuming-location-suggestions"></a>使用位置建议

此下一节将介绍使用来自系统 （如地图应用程序） 或其他第三方应用的其他部分的位置建议。

有两种主要方式，应用程序可以使用位置的建议：

- 通过 QuickType 键盘。
- 直接通过使用路由的应用程序中的位置信息。

### <a name="location-suggestions-and-the-quicktype-keyboard"></a>位置的建议和 QuickType 键盘

如果应用程序处理基于文本的格式中的地址，该应用程序可以充分利用通过 QuickType UI 位置建议。 iOS 10 展开 QuickType UI 具有以下功能：

- 应用程序可以在 UI 中添加有关语义意图的文本字段的提示。
- 应用程序可以在应用中获取主动的建议。
- 应用程序可以受益于增强的自动更正。

新`TextContentType`iOS 10 中的文本字段控件的属性，开发人员可定义用户要在给定字段中输入的值语义意图。 例如：

```csharp
var textField = new UITextField();
textField.TextContentType = UITextContentType.FullStreetAddress;
```

将告知系统应用程序需要用户以在给定字段中输入完整街道地址。 这将允许 QuickType 键盘自动提供键盘位置的建议，当用户在此字段中输入一个值。

以下是几个可在开发人员的更常见类型`UITextContentType`静态类：

* `Name`
* `GivenName`
* `FamilyName`
* `Location`
* `FullStreetAddress`
* `AddressCityAndState`
* `TelephoneNumber`
* `EmailAddress`

### <a name="routing-apps-and-locations-suggestions"></a>路由的应用程序和位置建议

本部分中将看看使用位置提供的建议直接路由的应用程序中。 若要添加此功能路由应用，开发人员将利用现有`MKDirectionsRequest`框架，如下所示：

- 若要升级的应用程序中的多任务。
- 若要将应用注册为路由的应用程序。
- 若要处理启动应用程序与 MapKit`MKDirectionsRequest`对象。
- 若要使 iOS 能够了解如何在适当情况下，建议向用户应用基于用户参与度。

应用程序入门 MapKit`MKDirectionsRequest`对象，它应会自动开始为提供的用户说明进行操作的请求的位置，或显示 UI，方便用户开始查询路线。 例如：


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

新在 iOS 10 中，应用可以发送该原因，开发人员需要编码地址中不具有地理坐标的地址：

```csharp
var geocoder = new CLGeocoder();
geocoder.GeocodeAddress(address, (place, err)=> {
    // Handle the display of the address
    
});

```

## <a name="media-app-suggestions"></a>媒体的应用建议

如果应用程序处理任何类型的媒体等播客应用或流媒体内容，例如音频或视频中的，ios 10 中，它可以在系统中充分利用媒体的建议。

对于处理媒体的应用程序，iOS 支持以下行为：

- iOS 将提升用户很可能要使用以前的行为的应用。
- 聚焦和今日视图将显示与应用相关的建议。
- 如果应用满足下列触发器之一，它可能会提升到锁定屏幕建议：
    - 插入耳机或蓝牙设备中之后建立的连接。
    - 之后在汽车中获取。
    - 后到达家庭或工作。 

通过在 iOS 10 中包含简单的 API 调用，开发人员可以创建的媒体应用的用户提供更具吸引力锁定屏幕体验。 通过使用`MPPlayableContentManager`类来管理媒体的播放、 完全媒体控件 （如所述的音乐应用程序） 将应用程序在锁定屏幕上显示。


```csharp
using System;
using MediaPlayer;
using UIKit;

namespace MonkeyPlayer
{
    public class PlayableContentDelegate : MPPlayableContentDelegate
    {
        #region Constructors
        public PlayableContentDelegate ()
        {
        }
        #endregion

        #region Override methods
        public override void InitiatePlaybackOfContentItem (MPPlayableContentManager contentManager, Foundation.NSIndexPath indexPath, Action<Foundation.NSError> completionHandler)
        {
            // Access the media item to play
            var item = LoadMediaItem (indexPath);

            // Populate the lock screen
            PopulateNowPlayingItem (item);

            // Prep item to be played
            var status = PreparePlayback (item);

            // Call completion handler
            completionHandler (null);
        }
        #endregion

        #region Public Methods
        public MPMediaItem LoadMediaItem (Foundation.NSIndexPath indexPath)
        {
            var item = new MPMediaItem ();

            // Load item from media store
            ...

            return item;
        }

        public void PopulateNowPlayingItem (MPMediaItem item)
        {
            // Get Info Center and album art
            var infoCenter = MPNowPlayingInfoCenter.DefaultCenter;
            var albumArt = (item.Artwork == null) ? new MPMediaItemArtwork (UIImage.FromFile ("MissingAlbumArt.png")) : item.Artwork;

            // Populate Info Center
            infoCenter.NowPlaying.Title = item.Title;
            infoCenter.NowPlaying.Artist = item.Artist;
            infoCenter.NowPlaying.AlbumTitle = item.AlbumTitle;
            infoCenter.NowPlaying.PlaybackDuration = item.PlaybackDuration;
            infoCenter.NowPlaying.Artwork = albumArt;
        }

        public bool PreparePlayback (MPMediaItem item)
        {
            // Prepare media item for playback
            ...

            // Return results
            return true;
        }
        #endregion
    }
}
```

## <a name="summary"></a>总结

本文已涵盖主动建议，并介绍了开发人员可以如何使用它们驱动器流量发送到 Xamarin.iOS 应用程序。 它涉及的步骤来实现主动建议，并提供使用指南。



## <a name="related-links"></a>相关链接

- [iOS 10 示例](https://developer.xamarin.com/samples/ios/iOS10/)
- [SiriKit 编程指南](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/index.html)
