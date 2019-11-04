---
title: 第4章的摘要。 滚动堆栈
description: 用 Xamarin 创建移动应用程序：第4章的摘要。 滚动堆栈
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 7A39FD4F-15AD-4F94-960E-9FEEB63FFD44
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
ms.openlocfilehash: bda9d5cb323524981bed9c3bb55998513dd69aab
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73032879"
---
# <a name="summary-of-chapter-4-scrolling-the-stack"></a>第4章的摘要。 滚动堆栈

[![下载示例](~/media/shared/download.png) 下载示例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04)

本章主要致力于引入*布局*的概念，这是用于在页面上组织多个视图的可视显示的类和方法的整体术语。

布局涉及多个派生自[`Layout`](xref:Xamarin.Forms.Layout)和[`Layout<T>`](xref:Xamarin.Forms.Layout`1)的类。 本章重点介绍[`StackLayout`](xref:Xamarin.Forms.StackLayout)。

> [!NOTE]
> Xamarin 3.0 中引入的[`FlexLayout`](~/xamarin-forms/user-interface/layouts/flex-layout.md)可用于类似于 `StackLayout` 但具有更大灵活性的方式。

本章还介绍了[`ScrollView`](xref:Xamarin.Forms.ScrollView)、 [`Frame`](xref:Xamarin.Forms.Frame)和[`BoxView`](xref:Xamarin.Forms.BoxView)类。

## <a name="stacks-of-views"></a>视图堆栈

[`StackLayout`](xref:Xamarin.Forms.StackLayout)派生自 `Layout<View>` 并继承类型 `IList<View>`的[`Children`](xref:Xamarin.Forms.Layout`1)属性。 您可以向此集合添加多个视图项，并 `StackLayout` 在水平或垂直堆栈中显示它们。

将 `StackLayout` 的[`Orientation`](xref:Xamarin.Forms.StackLayout.Orientation)属性设置为[`Vertical`](xref:Xamarin.Forms.StackOrientation.Vertical)或[`Horizontal`](xref:Xamarin.Forms.StackOrientation.Horizontal)的[`StackOrientation`](xref:Xamarin.Forms.StackOrientation)枚举的成员。 默认值为 `Vertical`。

将 `StackLayout` 的[`Spacing`](xref:Xamarin.Forms.StackLayout.Spacing)属性设置为 `double` 值，以指定子级之间的间距。 默认值为6。

在代码中，你可以将项添加到 `for` 或 `foreach` 循环中的 `Children` 的 `StackLayout` 集合，如[**ColorLoop**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/ColorLoop)示例中所示，也可以使用 [**ColorList**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/ColorList) 中所示的单个视图的列表初始化 `Children` 集合。 . 子级必须派生自 `View`，但也可以包括其他 `StackLayout` 对象。

## <a name="scrolling-content"></a>滚动内容

如果 `StackLayout` 包含的子页面太多，则可以将 `StackLayout` 置于[`ScrollView`](xref:Xamarin.Forms.ScrollView)以允许滚动。

将 `ScrollView` 的[`Content`](xref:Xamarin.Forms.ScrollView.Content)属性设置为要滚动的视图。 这通常是一个 `StackLayout`，但它可以是任何视图。

将 `ScrollView` 的[`Orientation`](xref:Xamarin.Forms.ScrollView.Orientation)属性设置为[`ScrollOrientation`](xref:Xamarin.Forms.ScrollOrientation)属性、 [`Vertical`](xref:Xamarin.Forms.ScrollOrientation.Vertical)、 [`Horizontal`](xref:Xamarin.Forms.ScrollOrientation.Horizontal)或[`Both`](xref:Xamarin.Forms.ScrollOrientation.Both)的成员。 默认值为 `Vertical`。 如果 `ScrollView` 的内容是 `StackLayout`，则这两个方向应一致。

[**ReflectedColors**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/ReflectedColors)示例演示如何使用 `ScrollView` 和 `StackLayout` 来显示可用颜色。 该示例还演示了如何使用 .NET 反射获取 `Color` 结构的所有公共静态属性和字段，而无需显式列出它们。

## <a name="the-expands-option"></a>展开选项

当 `StackLayout` 堆栈其子级时，每个子元素会在 `StackLayout` 的总高度（取决于子节点的大小以及其 `HorizontalOptions` 和 `VerticalOptions` 属性的设置）中占用特定的槽。 这些属性分配了类型[`LayoutOptions`](xref:Xamarin.Forms.LayoutOptions)的值。

`LayoutOptions` 结构定义了两个属性：

- 枚举类型的[`Alignment`](xref:Xamarin.Forms.LayoutOptions.Alignment) [`LayoutAlignment`](xref:Xamarin.Forms.LayoutAlignment)具有四个成员、 [`Start`](xref:Xamarin.Forms.LayoutAlignment.Start)、 [`Center`](xref:Xamarin.Forms.LayoutAlignment.Center)、 [`End`](xref:Xamarin.Forms.LayoutAlignment.End)和[`Fill`](xref:Xamarin.Forms.LayoutAlignment.Fill)
- `bool` 类型的 [`Expands`](xref:Xamarin.Forms.LayoutOptions.Expands)

为方便起见，`LayoutOptions` 结构还定义了 `LayoutOptions` 类型的8个静态只读字段，该字段包含两个实例属性的所有组合：

- [`LayoutOptions.Start`](xref:Xamarin.Forms.LayoutOptions.Start)
- [`LayoutOptions.Center`](xref:Xamarin.Forms.LayoutOptions.Center)
- [`LayoutOptions.End`](xref:Xamarin.Forms.LayoutOptions.End)
- [`LayoutOptions.Fill`](xref:Xamarin.Forms.LayoutOptions.Fill)
- [`LayoutOptions.StartAndExpand`](xref:Xamarin.Forms.LayoutOptions.StartAndExpand)
- [`LayoutOptions.CenterAndExpand`](xref:Xamarin.Forms.LayoutOptions.CenterAndExpand)
- [`LayoutOptions.EndAndExpand`](xref:Xamarin.Forms.LayoutOptions.EndAndExpand)
- [`LayoutOptions.FillAndExpand`](xref:Xamarin.Forms.LayoutOptions.FillAndExpand)

以下讨论涉及默认垂直方向 `StackLayout`。 水平 `StackLayout` 类似。

对于垂直 `StackLayout`，`HorizontalOptions` 设置确定子级在 `StackLayout`宽度内的水平位置。 如果 `Alignment` 设置为 `Start`、`Center`或 `End`，则会使子元素不受限制。 子元素确定其宽度，并将其置于 `StackLayout`的左对齐、居中或右端。 `Fill` 选项会使子元素被水平约束，并填充 `StackLayout`的宽度。

对于垂直 `StackLayout`，每个子元素都是不受限制的，并根据子项的高度获取垂直槽，在这种情况下，`VerticalOptions` 设置不相关。

如果垂直 `StackLayout` 本身不受约束&mdash;即，如果其 `VerticalOptions` 设置为 `Start`、`Center`或 `End`，则 `StackLayout` 的高度为其子级的总高度。

但是，如果垂直 `StackLayout` 是垂直约束的&mdash;如果其 `VerticalOptions` 设置为 `Fill`，则&mdash;的高度将为其容器的高度，其长度可能大于其子级的总高度。 如果是这种情况，并且如果至少有一个子级具有 `true`的 `Expands` 标志的 `VerticalOptions` 设置，则 `StackLayout` 中的额外空间将在所有这些子集中平均分配，`Expands` 标志为 `true`。 子元素的总高度将等于 `StackLayout`的高度，而 `VerticalOptions` 设置的 `Alignment` 部分确定子级在其槽中的垂直位置。

[**VerticalOptionsDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/VerticalOptionsDemo)示例演示了这一点。

## <a name="frame-and-boxview"></a>Frame 和 BoxView

这两个矩形视图通常用于表示目的。

[`Frame`](xref:Xamarin.Forms.Frame)视图在另一个视图周围显示一个矩形框架，可以是诸如 `StackLayout`之类的布局。 `Frame` 从设置为要在 `Frame`中显示的视图[`ContentView`](xref:Xamarin.Forms.ContentView)继承[`Content`](xref:Xamarin.Forms.ContentView.Content)属性。 默认情况下，`Frame` 是透明的。 设置以下三个属性以自定义框架的外观：

- 要使其可见的[`OutlineColor`](xref:Xamarin.Forms.Frame.OutlineColor)属性。 如果不知道基础配色方案，通常会将 `OutlineColor` 设置为 `Color.Accent`。
- [`HasShadow`](xref:Xamarin.Forms.Frame.HasShadow)属性可以设置为 `true` 在 iOS 设备上显示黑色阴影。
- 将[`Padding`](xref:Xamarin.Forms.Layout.Padding)属性设置为 `Thickness` 值，以便在框架和框架的内容之间留出一个空格。 所有边的默认值均为20个单位。

`Frame` 具有 `LayoutOptions.Fill`的默认 `HorizontalOptions` 和 `VerticalOptions` 值，这意味着，`Frame` 将填充其容器。 对于其他设置，`Frame` 的大小取决于其内容的大小。

[**FramedText**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/FramedText)示例演示了 `Frame`。

[`BoxView`](xref:Xamarin.Forms.BoxView)显示由其[`Color`](xref:Xamarin.Forms.BoxView.Color)属性指定的矩形区域。

如果 `BoxView` 受约束（其 `HorizontalOptions` 和 `VerticalOptions` 属性的默认设置为 `LayoutOptions.Fill`），则 `BoxView` 将填充它的可用空间。 如果 `BoxView` 不受约束（`HorizontalOptions` 并 `LayoutOptions` 设置为 `Start`、`Center`或 `End`），则它的默认维度为40单位正方形。 `BoxView` 可以在一个维度中受到约束，而在另一个维度中不受约束。

通常，您将设置 `BoxView` 的[`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest)和[`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest)属性，以使其具有特定的大小。 [**SizedBoxView**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/SizedBoxView)示例对此进行了说明。

您可以使用 `StackLayout` 的多个实例来合并 `Frame` 中的一个 `BoxView` 和几个 `Label` 实例，以显示特定的颜色，然后将这些视图中的每个实例放在 `StackLayout` 中的 `ScrollView` 上，以创建具有吸引力的颜色列表[**ColorBlocks**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/ColorBlocks)示例中所示：

[![颜色块的三个屏幕截图](images/ch04fg11-small.png "颜色列表")](images/ch04fg11-large.png#lightbox "颜色列表")

## <a name="a-scrollview-in-a-stacklayout"></a>StackLayout 中的 ScrollView？

将 `StackLayout` 放置在 `ScrollView` 中很常见，但在 `StackLayout` 中放置 `ScrollView` 有时也很方便。 理论上，这不是可能的，因为垂直 `StackLayout` 的子级是垂直不受约束的。 但 `ScrollView` 必须是垂直约束的。 必须为其提供特定的高度，以便它可以确定其子级的大小以进行滚动。

技巧是将 `StackLayout` 的 `VerticalOptions` 设置 `FillAndExpand`设置为 `ScrollView` 的子项。 [**BlackCat**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/BlackCat)示例演示了这一点。

**BlackCat**示例还演示如何定义和访问嵌入到共享库中的程序资源。 这也可以通过共享资产项目（Sap）来实现，但该过程有点棘手，如[**BlackCatSap**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/BlackCatSap)示例所示。

## <a name="related-links"></a>相关链接

- [第4章完整文本（PDF）](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch04-Apr2016.pdf)
- [第4章示例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04)
- [第 4 F#章示例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/FS)
- [StackLayout](~/xamarin-forms/user-interface/layouts/stack-layout.md)
- [ScrollView](~/xamarin-forms/user-interface/layouts/scroll-view.md)
- [BoxView](~/xamarin-forms/user-interface/boxview.md)
