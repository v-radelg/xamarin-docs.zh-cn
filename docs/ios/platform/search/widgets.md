---
title: 搜索和主页屏幕小组件中的增强功能 iOS 10
description: 本文档介绍 Apple iOS 10，包括搜索和主屏幕小组件的更新中的小组件所做的增强功能。
ms.prod: xamarin
ms.assetid: D66FD9E1-9E23-4BB6-825C-ED19B8F72A81
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: f693b480fff141c177ed135ced60afd65abd77de
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61082162"
---
# <a name="search-and-home-screen-widget-enhancements-in-ios-10"></a>搜索和主页屏幕小组件中的增强功能 iOS 10

_本文介绍如何对 iOS 10 中的小组件系统进行了 Apple 的增强功能。_

Apple 引入了几个小组件系统，以确保小组件查看任何新的 iOS 存在 10 锁定屏幕的背景上均表现出色的增强功能。 此外，小组件现在包含[NCWidgetDisplayMode](https://developer.apple.com/reference/notificationcenter/ncwidgetdisplaymode)属性允许开发人员来描述多少内容是否可用并允许用户展开和折叠内容。

小组件 （也称为今天扩展） 是信息的一种特殊的 iOS 扩展显示少量有用或未公开及时的特定于应用的功能。 例如，新闻应用程序具有一个小组件，显示了热门资讯和日历应用提供了两个不同的小组件： 一个用于显示今天的事件，另一个用于显示即将到来的事件。

小组件可高度自定义和可能包含 UI 元素，例如文本、 图像、 按钮等。此外，开发人员可以进一步自定义其小组件的布局。

[![](widgets-images/widgets01.png "示例小组件")](widgets-images/widgets01.png#lightbox)

有两个主要位置的用户可以查看并与其交互的应用的小组件：

- **在搜索屏幕**-用户可以添加他们认为到其搜索屏幕最有用的小组件。 在家庭和锁定屏幕上的右轻扫可访问搜索屏幕。
- **在主页屏幕**-从主页屏幕中，用户可以使用 3D Touch 通过将压力应用于应用的图标打开快速操作列表。 快速操作列表上方，将显示应用的小组件。 请参阅我们[简介 3D Touch](~/ios/platform/3d-touch.md)文档了解详细信息。

## <a name="widgets-developer-suggestions"></a>小组件开发人员建议

理想情况下，开发人员应始终尝试并设计用户会想要添加到其搜索屏幕的小组件。 为此，Apple 具有以下建议：

- **创建非常棒，Glanceable 体验**-用户想小组件，可提供的状态更新的简短的 glanceable 信息或使其能够快速执行简单的任务。 这使得提供适当数量的信息并交互性一个必不可少。 只要有可能，允许用户执行某个给定的任务一次点击。 此外，由于小组件不支持平移或滚动，这将需要在小组件的设计考虑到。
- **快速显示内容**-小组件设计为 glanceable，因此用户应永远不会具有要等待的内容以加载后显示一个小组件。 小组件应缓存其内容，本地，以便在后台加载全新的内容时，它们可以显示最近访问的内容。
- **提供相应填充和边距**-小组件应永远不会看起来很拥挤，因此应避免扩展到边缘的小组件的视图的内容。 应始终有多个边缘和内容之间的像素宽边距。 Apple 还建议使用应用程序的图标，显示顶部的小组件，作为一个指南，对齐方式。 如果该小组件显示的网格布局，请确保有适当填充网格中的项之间，尝试限制到四个最大项目数。
- **使用自适应布局**-小组件的宽度将差异取决于其运行的设备和设备的方向。 如果折叠 （默认值） 或 （不支持所有小组件） 的扩展状态中显示的小组件的高度也差异取决于。 折叠小组件具有大约两个半标准 iOS 表行的高度。 开发人员可以扩展的小组件的请求大小，但它在理想情况下应早于屏幕的高度。 处于折叠状态，该小组件应显示唯一的基本、 独立的信息。 当扩展、 小组件应显示增强了处于折叠状态显示的主要内容的补充信息。 快速操作列表中显示的小组件将只能处于折叠状态。
- **无自定义小组件的背景**-由系统提供的较小的、 经过模糊处理的背景上显示的小组件。 这样做是为了推广小组件之间的一致性和提高其内容的易读性。 避免为小组件背景中使用的映像，因为它可能与用户的锁定和主页屏幕墙纸发生冲突。
- **使用系统字体中黑色或暗灰色**-时显示文本中的小组件，系统字体效果最佳。 字体应以黑色或暗灰色针对较小的、 经过模糊处理的小组件背景中突出显示。
- **提供应用程序访问时适当**-小组件的应始终从其应用程序单独操作，但是，如果需要更深入的功能，该小组件应该能够启动应用以查看或编辑特定的信息。 永远不会包含"打开应用"按钮，只需使用户可以点击内容本身并永远不会打开的第三方应用程序。
- **选择清除、 简洁的小组件名称**-对小组件的内容始终显示应用的图标和小组件的名称。 Apple 建议使用其主小组件的应用程序的名称和它提供了任何其他的清晰、 简洁名称。 时提供自定义小组件标题，它们应以该应用程序的名称 （例如映射附近、 地图餐馆，等等。） 作为前缀。
- **通知身份验证时将值添加**-如果其他功能或信息是可用仅当用户进行身份验证和登录，向用户显示此。 例如，滑水感觉的时间共享应用程序可能会说"到书籍骑行登录"。
- **选择快速操作列表小组件**-如果应用程序提供了多个小组件，开发人员应选择当用户将显示快速操作列表通过将压力应用于使用 3D Touch 应用程序的图标时所显示的一个。

有关使用小组件的更多详细信息，请参阅我们[扩展插件简介](~/ios/platform/extensions.md)，[简介 3D Touch](~/ios/platform/3d-touch.md)文档和 Apple 的[应用扩展编程指南](https://developer.apple.com/library/prerelease/content/documentation/General/Conceptual/ExtensibilityPG/index.html).

## <a name="working-with-vibrancy"></a>使用活力

活力可确保小组件的文本保持清晰显示给小组件的光，模糊 （由系统提供） 的背景上。 在 iOS 10 之前，开发人员会使用[NotificationCenterVibrancyEffect](https://developer.apple.com/reference/uikit/uivibrancyeffect/1613917-notificationcentervibrancyeffect)的小组件的活力。 例如：

```csharp
// DEPRECATED: Get Widget Vibrancy Effect
var vibrancy = UIVibrancyEffect.CreateForNotificationCenter ();
```

这在 iOS 10 中已弃用，应替换为[WidgetPrimaryVibrancyEffect](https://developer.apple.com/reference/uikit/uivibrancyeffect/1771278-widgetprimaryvibrancyeffect)或[WidgetSecondaryVibrancyEffect](https://developer.apple.com/reference/uikit/uivibrancyeffect/1771277-widgetsecondaryvibrancyeffect)。 例如：

```csharp
// Get Primary Widget Vibrancy Effect
var vibrancy = UIVibrancyEffect.CreatePrimaryVibrancyEffectForNotificationCenter ();

// Get Secondary Widget Vibrancy Effect
var vibrancy2 = UIVibrancyEffect.CreateSecondaryVibrancyEffectForNotificationCenter ();
```

## <a name="working-with-collapsed-and-expanded-widgets"></a>使用折叠和展开的小组件

新 ios 10、 小组件现在包含[NCWidgetDisplayMode](https://developer.apple.com/reference/notificationcenter/ncwidgetdisplaymode)属性允许开发人员来描述多少内容是否可用并允许用户展开和折叠内容。

最初显示小组件，它将处于折叠状态。 折叠小组件具有大约两个半标准 iOS 表行的高度。 开发人员可以扩展的小组件的请求大小，但它在理想情况下应早于屏幕的高度。 

处于折叠状态，该小组件应显示唯一的基本、 独立的信息。 当扩展、 小组件应显示增强了处于折叠状态显示的主要内容的补充信息。 例如，天气应用程序显示的当前天气状况折叠状态时，并将添加预测展开时的小时。

快速操作列表中显示的小组件将只能处于折叠状态。 如果应用程序提供多个小组件，开发人员应选择当用户将显示快速操作列表通过将压力应用于使用 3D Touch 应用程序的图标时所显示的一个。

下面的示例是简单今天的扩展名 （小组件） 处理的折叠和展开状态：

```csharp
using System;
using NotificationCenter;
using Foundation;
using UIKit;
using CoreGraphics;

namespace MonkeyAbout
{
    public partial class TodayViewController : UIViewController, INCWidgetProviding
    {
        protected TodayViewController (IntPtr handle) : base (handle)
        {
            // Note: this .ctor should not contain any initialization logic.
        }

        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Tell widget it can be expanded
            ExtensionContext.SetWidgetLargestAvailableDisplayMode (NCWidgetDisplayMode.Expanded);

            // Get the maximum size
            var maxSize = ExtensionContext.GetWidgetMaximumSize (NCWidgetDisplayMode.Expanded);
        }

        [Export ("widgetPerformUpdateWithCompletionHandler:")]
        public void WidgetPerformUpdate (Action<NCUpdateResult> completionHandler)
        {
            // Take action based on the display mode
            switch (ExtensionContext.GetWidgetActiveDisplayMode()) {
            case NCWidgetDisplayMode.Compact:
                Content.Text = "Let's Monkey About!";
                break;
            case NCWidgetDisplayMode.Expanded:
                Content.Text = "Gorilla!!!!";
                break;
            }

            // Report results
            // If an error is encoutered, use NCUpdateResultFailed
            // If there's no update required, use NCUpdateResultNoData
            // If there's an update, use NCUpdateResultNewData
            completionHandler (NCUpdateResult.NewData);
        }

        [Export ("widgetActiveDisplayModeDidChange:withMaximumSize:")]
        public void WidgetActiveDisplayModeDidChange (NCWidgetDisplayMode activeDisplayMode, CGSize maxSize)
        {
            // Take action based on the display mode
            switch (activeDisplayMode) {
            case NCWidgetDisplayMode.Compact:
                PreferredContentSize = maxSize;
                Content.Text = "Let's Monkey About!";
                break;
            case NCWidgetDisplayMode.Expanded:
                PreferredContentSize = new CGSize (0, 200);
                Content.Text = "Gorilla!!!!";
                break;
            }
        }

    }
}
```

请一下在详细信息小组件显示模式的特定代码。 此小组件支持的扩展状态情况通知给系统，它使用：

```csharp
// Tell widget it can be expanded
ExtensionContext.SetWidgetLargestAvailableDisplayMode (NCWidgetDisplayMode.Expanded);
```

若要获取小组件的当前显示模式下，它使用：

```csharp
ExtensionContext.GetWidgetActiveDisplayMode()
```

若要获取的折叠或展开状态的最大大小，它使用：

```csharp
// Get the maximum size
var maxSize = ExtensionContext.GetWidgetMaximumSize (NCWidgetDisplayMode.Expanded);
```

并为处理更改的状态 （显示模式），它使用：

```csharp
[Export ("widgetActiveDisplayModeDidChange:withMaximumSize:")]
public void WidgetActiveDisplayModeDidChange (NCWidgetDisplayMode activeDisplayMode, CGSize maxSize)
{
    // Take action based on the display mode
    switch (activeDisplayMode) {
    case NCWidgetDisplayMode.Compact:
        PreferredContentSize = maxSize;
        Content.Text = "Let's Monkey About!";
        break;
    case NCWidgetDisplayMode.Expanded:
        PreferredContentSize = new CGSize (0, 200);
        Content.Text = "Gorilla!!!!";
        break;
    }
}
```

除了设置每个状态 （折叠或展开） 的请求的大小，它还会更新显示以匹配新大小的内容。

## <a name="summary"></a>总结

本文已介绍 Apple 具有对 iOS 10 中的小组件系统所做的增强功能并展示了如何在 Xamarin.iOS 中实现它们。



## <a name="related-links"></a>相关链接

- [iOS 10 示例](https://developer.xamarin.com/samples/ios/iOS10/)
- [扩展插件简介](~/ios/platform/extensions.md)
- [3D Touch 简介](~/ios/platform/3d-touch.md)
- [应用扩展编程指南](https://developer.apple.com/library/prerelease/content/documentation/General/Conceptual/ExtensibilityPG/index.html)
