---
title: 外观 API Xamarin.iOS 中
description: iOS，可将应用 visual 属性设置静态类级别，而不是单个对象，以便更改应用到该控件在应用程序中的所有实例。
ms.prod: xamarin
ms.assetid: C1727F0C-82B1-D085-D46F-C6383FF04B16
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 11/15/2018
ms.openlocfilehash: bfbc902b0912527fea6aaa58c6706ef5a0ccbf8e
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61178245"
---
# <a name="appearance-api-in-xamarinios"></a>外观 API Xamarin.iOS 中

_iOS，可将应用 visual 属性设置静态类级别，而不是单个对象，以便更改应用到该控件在应用程序中的所有实例。_

此功能通过静态公开在 Xamarin.iOS`Appearance`支持它的所有 UIKit 控件的属性。 可视外观 （如颜色和背景图像着色作为属性） 可以因此轻松进行自定义要为应用程序提供一致的外观。 在 iOS 5 中引入了外观 API 和 iOS 9 中已弃用它的某些部分时它仍是实现某些样式设置和 Xamarin.iOS 应用程序中的主题设置效果的好方法。

## <a name="overview"></a>概述

iOS 允许自定义许多 UIKit 控件可使你想要将应用于你的应用程序的品牌符合标准控件的外观。

有两种不同的方式来应用自定义外观：

- **直接在控件实例上**– 您可以在很多控件包括工具栏、 导航栏、 按钮和滑块设置着色颜色、 背景图像和标题位置 （以及一些其他属性）。

- **外观静态属性上设置默认值**-通过为每个控件的可自定义属性来公开`Appearance`静态属性。 将应用于这些属性的任何自定义将用作后设置该属性创建该任何的类型控件默认值。

外观示例应用程序演示了这三种方法，如以下屏幕截图中所示：

[![](introduction-to-the-appearance-api-images/appearance01-sml.png "外观示例应用程序演示了这三种方法")](introduction-to-the-appearance-api-images/appearance01.png#lightbox)

截至 iOS 8、 外观代理已扩展到 TraitCollections。
 `AppearanceForTraitCollection` 可用于在特定特征集合上设置的默认外观。 你可以阅读更多有关此信息[情节提要简介](~/ios/user-interface/storyboards/unified-storyboards.md)指南。

## <a name="setting-appearance-properties"></a>设置外观属性

在第一个屏幕中，静态外观类用于设置样式的按钮和黄色/橙色元素如下：

```csharp
// Set the default appearance values
UIButton.Appearance.TintColor = UIColor.LightGray;
UIButton.Appearance.SetTitleColor(UIColor.FromRGB(0,127,14), UIControlState.Normal);

UISlider.Appearance.ThumbTintColor = UIColor.Red;
UISlider.Appearance.MinimumTrackTintColor = UIColor.Orange;
UISlider.Appearance.MaximumTrackTintColor = UIColor.Yellow;

UIProgressView.Appearance.ProgressTintColor = UIColor.Yellow;
UIProgressView.Appearance.TrackTintColor = UIColor.Orange;
```

这样，在设置绿色元素样式`ViewDidLoad`替代的默认值的方法和*外观*静态类：

```csharp
slider2.ThumbTintColor = UIColor.FromRGB (0,127,70); // dark green
slider2.MinimumTrackTintColor = UIColor.FromRGB (66,255,63);
slider2.MaximumTrackTintColor = UIColor.FromRGB (197,255,132);
```

```csharp
progress2.ProgressTintColor = UIColor.FromRGB (66,255,63);
progress2.TrackTintColor = UIColor.FromRGB (197,255,132);
```

## <a name="using-uiappearance-in-xamarinforms"></a>在 Xamarin.Forms 中使用 UIAppearance

外观 API 时，可能会很有用[样式的 iOS 应用](~/xamarin-forms/platform/ios/formatting.md#uiappearance)Xamarin.Forms 解决方案中。 中的几行`AppDelegate`类可帮助实现特定的配色方案，而无需创建[自定义呈现器](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)。

### <a name="custom-themes-and-uiappearance"></a>自定义主题和 UIAppearance

iOS 界面控件，为"主题"使用允许的用户的许多可视特性*UIAppearance* Api 来强制特定控件具有相同的外观的所有实例。 这作为在许多用户界面控件类，不在该控件的单个实例上的外观属性公开。 设置显示属性，静态`Appearance`属性会影响应用程序中该类型的所有控件。

若要更好地理解概念后，请考虑一个示例。

若要更改特定`UISegmentedControl`若要让洋红色色彩，我们将引用中我们屏幕上的特定控件`ViewDidLoad`:

```csharp
sg1.TintColor = UIColor.Magenta;
```

或者，在设计器的属性面板中设置的值：

[![](introduction-to-the-appearance-api-images/propertiespadtint.png "属性填充色彩")](introduction-to-the-appearance-api-images/propertiespadtint.png#lightbox)

下图说明了，这将设置上的控件名为 sg1 色调。

[![](introduction-to-the-appearance-api-images/image53.png "设置单个控件的浅色")](introduction-to-the-appearance-api-images/image53.png#lightbox)

若要在这种方式中设置多个控件将完全效率较低，因此我们可以改为设置静态`Appearance`类本身的属性。 下面的代码所示：

```csharp
UISegmentedControl.Appearance.TintColor = UIColor.Magenta;
```

下图现在显示这两个分段的控件设置为洋红色的外观：

[![](introduction-to-the-appearance-api-images/image54.png "设置外观控件浅色")](introduction-to-the-appearance-api-images/image54.png#lightbox)

`Appearance` 属性应设置尽早在应用程序生命周期，如在 AppDelegate 中的`FinishedLaunching`事件，或在 ViewController 才会显示受影响的控件。

请参阅[外观 API 简介](~/ios/user-interface/ios-ui/introduction-to-the-appearance-api.md)更多详细信息。

## <a name="related-links"></a>相关链接

- [外观 （示例）](https://developer.xamarin.com/samples/monotouch/Appearance/)
- [UIAppearance 协议参考](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIAppearance_Protocol/)
- [在 Xamarin.Forms 中的外观](~/xamarin-forms/platform/ios/formatting.md#uiappearance)
