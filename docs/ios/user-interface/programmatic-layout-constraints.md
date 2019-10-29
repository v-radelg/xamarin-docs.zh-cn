---
title: Xamarin 中的编程布局约束
description: 本指南演示如何在代码中C#使用 Ios 自动布局约束，而不是在 Ios 设计器中创建它们。
ms.prod: xamarin
ms.assetid: 119C8365-B470-4CD4-85F7-086F0A46DCBB
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/22/2017
ms.openlocfilehash: 2ec012f882d6bc721e657385db333fce7a9e1aaf
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73002188"
---
# <a name="programmatic-layout-constraints-in-xamarinios"></a>Xamarin 中的编程布局约束

_本指南演示如何在代码中C#使用 Ios 自动布局约束，而不是在 Ios 设计器中创建它们。_

自动布局（也称为 "自适应布局"）是一种响应式设计方法。 与过渡布局系统不同，其中每个元素的位置均硬编码为屏幕上的某个点，自动布局是关于*关系*的，即相对于设计图面上的其他元素的元素位置。 自动布局的核心是约束或规则，用于定义元素或一组元素在屏幕上其他元素的上下文中的位置。 由于元素未绑定到屏幕上的特定位置，因此约束有助于创建在不同屏幕大小和设备方向上看起来良好的自适应布局。

通常，在 iOS 中使用自动布局时，会使用 iOS 设计器以图形方式对 UI 项放置布局约束。 但是，有时您可能需要在代码中C#创建和应用约束。 例如，使用动态创建的 UI 元素添加到 `UIView`。

本指南将演示如何使用C#代码创建和使用约束，而不是在 IOS 设计器中以图形方式创建它们。

<a name="Creating-Constraints-Programmatically" />

## <a name="creating-constraints-programmatically"></a>以编程方式创建约束

如上所述，通常会在 iOS 设计器中使用自动布局约束。 对于那些需要以编程方式创建约束的时间，您有三个选项可供选择：

- [布局定位点](#Layout-Anchors)-此 API 提供对受约束的 UI 项的定位点属性（如 `TopAnchor`、`BottomAnchor` 或 `HeightAnchor`）的访问权限。
- [布局约束](#Layout-Constraints)-可以使用 `NSLayoutConstraint` 类直接创建约束。
- [视觉对象格式设置语言](#Visual-Format-Language)-提供一种 ASCII 艺术方式，用于定义你的约束。

以下各节将详细介绍每个选项。

<a name="Layout-Anchors" />

### <a name="layout-anchors"></a>布局定位点

通过使用 `NSLayoutAnchor` 类，您可以使用一个流畅的接口基于所约束的 UI 项的定位属性创建约束。 例如，视图控制器的顶部和底部布局参考线公开了 `TopAnchor`、`BottomAnchor` 和 `HeightAnchor` 定位点属性，同时视图显示了边缘、中心、大小和基线属性。

> [!IMPORTANT]
> 除了一组标准的定位点属性，iOS 视图还包括 `LayoutMarginsGuides` 和 `ReadableContentGuide` 属性。 这些属性分别公开用于处理视图的边距和可读内容指南的 `UILayoutGuide` 对象。

布局定位点提供了几种使用易于阅读的精简格式创建约束的方法：

- **ConstraintEqualTo** -定义一个关系，其中 `first attribute = second attribute + [constant]` 具有可选的提供 `constant` 偏移量值。
- **ConstraintGreaterThanOrEqualTo** -定义一个关系，其中 `first attribute >= second attribute + [constant]` 具有可选的提供 `constant` 偏移量值。
- **ConstraintLessThanOrEqualTo** -定义一个关系，其中 `first attribute <= second attribute + [constant]` 具有可选的提供 `constant` 偏移量值。

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

典型的布局约束可以简单地表示为线性表达式。 请参见以下示例：

[![](programmatic-layout-constraints-images/graph01.png "A Layout Constraint expressed as a linear expression")](programmatic-layout-constraints-images/graph01.png#lightbox)

这会使用布局定位点转换为以下C#代码行：

```csharp
PurpleView.LeadingAnchor.ConstraintEqualTo (OrangeView.TrailingAnchor, 10).Active = true; 
```

C#代码的各个部分对应于公式的给定部分，如下所示：

|公式|代码|
|---|---|
|项1|PurpleView|
|属性1|LeadingAnchor|
|关系|ConstraintEqualTo|
|乘数|默认值为1.0，因此未指定|
|第2项|OrangeView|
|特性2|TrailingAnchor|
|返回的常量|10.0|

除了仅提供求解给定布局约束公式所需的参数以外，每个布局定位点方法还强制执行传递给它们的参数的类型安全。 因此，`LeadingAnchor` 或 `TrailingAnchor` 等水平约束锚仅可用于其他水平锚点类型，并且仅向大小约束提供乘数。

<a name="Layout-Constraints" />

### <a name="layout-constraints"></a>布局约束

您可以通过在代码中C#直接构造 `NSLayoutConstraint`，手动添加自动布局约束。 与使用布局定位点不同，您必须为每个参数指定一个值，即使它不会对所定义的约束产生任何影响。 这样一来，最终就会产生相当大的难以阅读的样板代码。 例如:

```csharp
//// Pin the leading edge of the view to the margin
NSLayoutConstraint.Create (OrangeView, NSLayoutAttribute.Leading, NSLayoutRelation.Equal, View, NSLayoutAttribute.LeadingMargin, 1.0f, 0.0f).Active = true;

//// Pin the trailing edge of the view to the margin
NSLayoutConstraint.Create (OrangeView, NSLayoutAttribute.Trailing, NSLayoutRelation.Equal, View, NSLayoutAttribute.TrailingMargin, 1.0f, 0.0f).Active = true;

//// Give the view a 1:2 aspect ratio
NSLayoutConstraint.Create (OrangeView, NSLayoutAttribute.Height, NSLayoutRelation.Equal, OrangeView, NSLayoutAttribute.Width, 2.0f, 0.0f).Active = true;
```

其中，`NSLayoutAttribute` 枚举定义视图的边距值，并对应于 `LayoutMarginsGuide` 属性（如 `Left`、`Right`、`Top` 和 `Bottom`），而 `NSLayoutRelation` 枚举定义将在给定属性之间创建的关系。`Equal`、`LessThanOrEqual` 或 `GreaterThanOrEqual`。

与布局定位点 API 不同，`NSLayoutConstraint` 创建方法不会突出显示特定约束的重要方面，也不会对约束执行编译时检查。 因此，很容易构造一个在运行时将引发异常的无效约束。

<a name="Visual-Format-Language" />

### <a name="visual-format-language"></a>视觉格式语言

利用视觉格式语言，你可以使用 ASCII 图（如字符串）定义约束，这些字符串提供了要创建的约束的直观表示形式。 这有以下优点和缺点：

- 视觉格式语言仅强制创建有效约束。
- 自动布局使用视觉格式语言将约束输出到控制台，因此调试消息与用于创建约束的代码类似。
- 利用视觉格式语言，你可以使用非常简洁的表达式同时创建多个约束。
- 由于视觉格式语言字符串没有编译方验证，因此只能在运行时发现问题。
- 由于视觉格式语言强调可视化效果，因此不能用它创建某些约束类型（如比率）。

使用视觉格式语言创建约束时，请执行以下步骤：

1. 创建一个包含视图对象和布局指南的 `NSDictionary`，以及一个将在定义格式时使用的字符串键。
2. （可选）创建一个 `NSDictionary`，该定义用作约束的常量值的一组键和值（`NSNumber`）。
3. 创建格式字符串以布局单个列或项的行。
4. 调用 `NSLayoutConstraint` 类的 `FromVisualFormat` 方法以生成约束。
5. 调用 `NSLayoutConstraint` 类的 `ActivateConstraints` 方法，以激活和应用这些约束。

例如，若要以可视格式语言创建前导和尾随约束，可以使用以下方法：

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

由于在使用默认间距时，视觉格式语言始终会创建附加到父视图的边距的零点约束，因此此代码将产生与上述示例相同的结果。

对于更复杂的 UI 设计（如单个行中的多个子视图），视觉格式语言同时指定水平间距和垂直对齐方式。 在上面的示例中，它指定 `AlignAllTop` `NSLayoutFormatOptions` 将行或列中的所有视图对齐到其顶部。

有关指定常见约束和视觉格式字符串语法的一些示例，请参阅 Apple 的[视觉格式语言附录](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/AutolayoutPG/VisualFormatLanguage.html#//apple_ref/doc/uid/TP40010853-CH27-SW1)。

<a name="Summary" />

## <a name="summary"></a>总结

本指南介绍了如何在中C#创建和使用自动布局约束，而不是在 IOS 设计器中以图形方式创建它们。 首先，它查看使用布局定位点（`NSLayoutAnchor`）处理自动布局。 接下来，它演示了如何使用布局约束（`NSLayoutConstraint`）。 最后，它使用自动布局的视觉格式语言显示。

## <a name="related-links"></a>相关链接

- [情节提要简介](~/ios/user-interface/storyboards/index.md)
- [iOS 设计控制演练](~/ios/user-interface/designer/ios-designable-controls-walkthrough.md)
- [用 Xamarin Designer for iOS 自动布局](~/ios/user-interface/designer/designer-auto-layout.md#modifying-in-code)
- [Apple-以编程方式创建约束](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/AutolayoutPG/ProgrammaticallyCreatingConstraints.html#//apple_ref/doc/uid/TP40010853-CH16-SW1)
- [Apple-视觉格式语言附录](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/AutolayoutPG/VisualFormatLanguage.html#//apple_ref/doc/uid/TP40010853-CH27-SW1)
