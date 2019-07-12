---
title: 在 Xamarin.iOS 中编程布局约束
description: 本指南介绍了使用 iOS 中的自动布局约束C#而不是创建在 iOS 设计器中的代码。
ms.prod: xamarin
ms.assetid: 119C8365-B470-4CD4-85F7-086F0A46DCBB
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/22/2017
ms.openlocfilehash: 089ada051b6780a15acfcdd7f9e32ddda1384d05
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67832053"
---
# <a name="programmatic-layout-constraints-in-xamarinios"></a>在 Xamarin.iOS 中编程布局约束

_本指南介绍了使用 iOS 中的自动布局约束C#而不是创建在 iOS 设计器中的代码。_

自动布局 （也称为"自适应布局"） 是响应式设计方法。 自动布局有关与过渡布局系统，其中每个元素的位置是硬编码到屏幕上的点，不同的是*关系*-相对于设计图面上其他元素的元素的位置。 自动布局的核心是元素的约束或规则，用于在屏幕上的其他元素的上下文中定义位置或组的元素。 由于元素不依赖于在屏幕上的特定位置，约束来帮助创建不同屏幕大小和设备方向上的自适应布局。

通常使用时自动布局在 iOS 中，将使用 iOS 设计器以图形方式放置 UI 项的布局约束。 但是，有时可能需要创建并应用中的约束C#代码。 例如，在使用动态创建的 UI 元素添加到`UIView`。

本指南将演示如何创建和使用约束使用C#而不是在 iOS 设计器中以图形方式创建的代码。

<a name="Creating-Constraints-Programmatically" />

## <a name="creating-constraints-programmatically"></a>以编程方式创建约束

如上面所述，通常您将使用自动布局限制 iOS 设计器中。 这时您需要以编程方式创建你的约束，有三个选项可供选择：

* [布局定位点](#Layout-Anchors)-此 API 可以访问的定位点属性 (如`TopAnchor`，`BottomAnchor`或`HeightAnchor`) 的受约束的用户界面项。
* [布局约束](#Layout-Constraints)-您可以创建直接使用约束`NSLayoutConstraint`类。
* [格式设置的 visual 语言](#Visual-Format-Language)-提供类似于方法来定义你的约束是 ASCII 艺术。

以下各节将详细阐述每个选项在详细信息。

<a name="Layout-Anchors" />

### <a name="layout-anchors"></a>布局定位点

通过使用`NSLayoutAnchor`类，您具有用于创建基于受约束的用户界面项的定位点属性的约束的 fluent 界面。 例如，视图控制器的顶部和底部布局指南公开`TopAnchor`，`BottomAnchor`和`HeightAnchor`虽然视图公开边缘、 中心、 大小和基线属性的定位点属性。

> [!IMPORTANT]
> 定位点属性的标准集，除了 iOS 视图还包含`LayoutMarginsGuides`和`ReadableContentGuide`属性。 这些属性公开`UILayoutGuide`使用视图的边距和可读的对象分别内容指南。

布局定位点提供易于理解、 更简洁的格式创建约束的几种方法：

- **ConstraintEqualTo** -定义的关系其中`first attribute = second attribute + [constant]`可能提供与`constant`偏移值。
- **ConstraintGreaterThanOrEqualTo** -定义的关系其中`first attribute >= second attribute + [constant]`可能提供与`constant`偏移值。
- **ConstraintLessThanOrEqualTo** -定义的关系其中`first attribute <= second attribute + [constant]`可能提供与`constant`偏移值。

例如:

```csharp
// Get the parent view's layout
var margins = View.LayoutMarginsGuide;

// Pin the leading edge of the view to the margin
OrangeView.LeadingAnchor.ConstraintEqualTo (margins.LeadingAnchor).Active = true;

// Pin the trailing edge of the view to the margin
OrangeView.TrailingAnchor.ConstraintEqualTo (margins.TrailingAnchor).Active = true;

// Give the view a 1:2 aspect ratio
OrangeView.HeightAnchor.ConstraintEqualTo (OrangeView.WidthAnchor, 2.0f);
```

典型布局约束可以表示只需为线性的表达式。 请参见以下示例：

[![](programmatic-layout-constraints-images/graph01.png "表示为线性表达式布局约束")](programmatic-layout-constraints-images/graph01.png#lightbox)

这将被转换为以下行的C#代码使用布局的定位点：

```csharp
PurpleView.LeadingAnchor.ConstraintEqualTo (OrangeView.TrailingAnchor, 10).Active = true; 
```

其中的部分C#代码对应于公式的给定部分，如下所示：

|等式|代码|
|---|---|
|第 1 项|PurpleView|
|属性 1|LeadingAnchor|
|关系|ConstraintEqualTo|
|乘数|默认值为 1.0，因此未指定|
|第 2 项|OrangeView|
|属性 2|TrailingAnchor|
|返回的常量|10.0|

除了提供解决给定的布局约束公式所需的参数，每个布局定位点方法强制执行传递给它们的参数的类型安全性。 因此水平限制，如定位`LeadingAnchor`或`TrailingAnchor`只能在与其他水平定位点类型和乘数只提供给大小约束。

<a name="Layout-Constraints" />

### <a name="layout-constraints"></a>布局约束

您可以通过直接构造来手动添加自动布局约束`NSLayoutConstraint`在C#代码。 与使用布局的定位点，则必须指定每个参数的值，即使它不会影响上所定义的约束。 因此，您最终会生成大量难以阅读，样板代码。 例如:

```csharp
//// Pin the leading edge of the view to the margin
NSLayoutConstraint.Create (OrangeView, NSLayoutAttribute.Leading, NSLayoutRelation.Equal, View, NSLayoutAttribute.LeadingMargin, 1.0f, 0.0f).Active = true;

//// Pin the trailing edge of the view to the margin
NSLayoutConstraint.Create (OrangeView, NSLayoutAttribute.Trailing, NSLayoutRelation.Equal, View, NSLayoutAttribute.TrailingMargin, 1.0f, 0.0f).Active = true;

//// Give the view a 1:2 aspect ratio
NSLayoutConstraint.Create (OrangeView, NSLayoutAttribute.Height, NSLayoutRelation.Equal, OrangeView, NSLayoutAttribute.Width, 2.0f, 0.0f).Active = true;
```

其中`NSLayoutAttribute`枚举定义视图的边距的值，并对应于`LayoutMarginsGuide`属性，如`Left`， `Right`，`Top`并`Bottom`和`NSLayoutRelation`枚举定义的关系，将为给定的属性之间创建`Equal`，`LessThanOrEqual`或`GreaterThanOrEqual`。

不同于使用布局定位点 API，`NSLayoutConstraint`创建方法不突出显示特定约束的重要方面和有无编译时间执行约束检查。 因此，很容易构造约束无效，将在运行时引发异常。

<a name="Visual-Format-Language" />

### <a name="visual-format-language"></a>直观的形式语言

可视格式语言，可使用类似于提供约束创建的可视表示形式的字符串的 ASCII 图文定义约束。 这有以下优点和缺点：

- 可视格式语言强制实施仅有效约束创建。
- 自动布局输出到控制台使用可视格式语言，因此调试消息将类似于代码用于创建该约束的约束。
- 可视格式语言，可在同一时间使用非常简洁的表达式创建多个约束。
- 由于没有可视格式语言字符串没有编译端验证，可以仅在运行时发现问题。
- 由于 Visual 格式语言强调通过完整性某些约束类型不能使用它 （例如比率） 中创建可视化效果。

使用可视格式语言创建约束时，请执行以下步骤：

1. 创建`NSDictionary`，其中包含视图对象和布局参考线和定义的格式时将使用一个字符串键。
2. （可选） 创建`NSDictionary`，它定义一组键和值 (`NSNumber`) 用作约束的常量值。
3. 创建布局的格式字符串的单个列或行的项。
4. 调用`FromVisualFormat`方法的`NSLayoutConstraint`类生成约束。
5. 调用`ActivateConstraints`方法的`NSLayoutConstraint`类，以激活并应用约束。

例如，若要创建可视格式语言前导和尾随的约束，您可以使用以下：

```csharp
// Get views being constrained
var views = new NSMutableDictionary (); 
views.Add (new NSString ("orangeView"), OrangeView);

// Define format and assemble constraints
var format = "|-[orangeView]-|";
var constraints = NSLayoutConstraint.FromVisualFormat (format, NSLayoutFormatOptions.AlignAllTop, null, views);

// Apply constraints
NSLayoutConstraint.ActivateConstraints (constraints);
```

由于 Visual 格式语言始终会创建零个点约束附加到父视图的边距时使用的默认间距，此代码生成上面介绍的示例相同的结果。

对于更复杂的 UI 设计，如在单个行上的多个子视图可视格式语言指定的水平间距和垂直对齐方式。 如上面的示例中，它指定所示`AlignAllTop``NSLayoutFormatOptions`对齐视图中的行或列添加到其顶部的所有。

请参阅 Apple[可视格式语言附录](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/AutolayoutPG/VisualFormatLanguage.html#//apple_ref/doc/uid/TP40010853-CH27-SW1)指定常见约束和可视化的格式字符串语法的一些示例。

<a name="Summary" />

## <a name="summary"></a>总结

本指南提供创建和使用自动布局中的约束C#而不是在 iOS 设计器中以图形方式创建。 首先，它了解了使用布局的定位点 (`NSLayoutAnchor`) 来处理自动布局。 接下来，它介绍了如何使用布局限制 (`NSLayoutConstraint`)。 最后，它使用呈现可视格式语言进行自动布局。

## <a name="related-links"></a>相关链接

- [情节提要简介](~/ios/user-interface/storyboards/index.md)
- [iOS 可设计的控件演练](~/ios/user-interface/designer/ios-designable-controls-walkthrough.md)
- [适用于 iOS 的 Xamarin 设计器的自动布局](~/ios/user-interface/designer/designer-auto-layout.md#modifying-in-code)
- [Apple-以编程方式创建约束](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/AutolayoutPG/ProgrammaticallyCreatingConstraints.html#//apple_ref/doc/uid/TP40010853-CH16-SW1)
- [Apple-可视化格式语言附录](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/AutolayoutPG/VisualFormatLanguage.html#//apple_ref/doc/uid/TP40010853-CH27-SW1)
