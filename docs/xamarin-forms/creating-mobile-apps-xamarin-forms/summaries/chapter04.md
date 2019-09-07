---
title: 第 4 章的摘要。 滚动堆栈
description: 用 Xamarin 创建移动应用程序：第 4 章的摘要。 滚动堆栈
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 7A39FD4F-15AD-4F94-960E-9FEEB63FFD44
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
ms.openlocfilehash: 66e4f52e87a4398dd2e09d2d128f43de9a71a665
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70760844"
---
# <a name="summary-of-chapter-4-scrolling-the-stack"></a>第 4 章的摘要。 滚动堆栈

[![下载示例](~/media/shared/download.png)下载示例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04)

本章主要介绍的概念专门介绍*布局*，这是类和技术的 Xamarin.Forms 使用来组织页面上的多个视图的可视显示为整体的术语。

布局涉及多个派生的类[ `Layout` ](xref:Xamarin.Forms.Layout)并[ `Layout<T>` ](xref:Xamarin.Forms.Layout`1)。 本章主要介绍[ `StackLayout` ](xref:Xamarin.Forms.StackLayout)。

> [!NOTE]
> [ `FlexLayout` ](~/xamarin-forms/user-interface/layouts/flex-layout.md)中引入可以有类似的方式使用 Xamarin.Forms 3.0`StackLayout`但具有更大的灵活性。

这一章中还引入了都[ `ScrollView` ](xref:Xamarin.Forms.ScrollView)， [ `Frame` ](xref:Xamarin.Forms.Frame)，以及[ `BoxView` ](xref:Xamarin.Forms.BoxView)类。

## <a name="stacks-of-views"></a>视图的堆栈

[`StackLayout`](xref:Xamarin.Forms.StackLayout) 派生自`Layout<View>`，并继承[ `Children` ](xref:Xamarin.Forms.Layout`1)类型的属性`IList<View>`。 将多个视图项添加到此集合和`StackLayout`在水平或垂直堆栈中显示它们。

设置[ `Orientation` ](xref:Xamarin.Forms.StackLayout.Orientation)的属性`StackLayout`成员[ `StackOrientation` ](xref:Xamarin.Forms.StackOrientation)枚举，任一[ `Vertical` ](xref:Xamarin.Forms.StackOrientation.Vertical)或[`Horizontal`](xref:Xamarin.Forms.StackOrientation.Horizontal). 默认值为 `Vertical`。

设置[ `Spacing` ](xref:Xamarin.Forms.StackLayout.Spacing)的属性`StackLayout`到`double`要指定子级之间的间距的值。 默认值为 6。

在代码中，您可以添加到项目`Children`的集合`StackLayout`中`for`或`foreach`循环中所示[ **ColorLoop** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/ColorLoop)示例中，也可以初始化`Children`如所示的各个视图的列表集合[ **ColorList**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/ColorList)。 子级必须派生自`View`，但可以包括其他`StackLayout`对象。

## <a name="scrolling-content"></a>滚动内容

如果`StackLayout`包含太多的子级显示在页面上，可以将放`StackLayout`中[ `ScrollView` ](xref:Xamarin.Forms.ScrollView)允许滚动。

设置[ `Content` ](xref:Xamarin.Forms.ScrollView.Content)属性的`ScrollView`到您要滚动的视图。 这通常是`StackLayout`，但也可以是任何视图。

设置[ `Orientation` ](xref:Xamarin.Forms.ScrollView.Orientation)的属性`ScrollView`成员[ `ScrollOrientation` ](xref:Xamarin.Forms.ScrollOrientation)属性， [ `Vertical` ](xref:Xamarin.Forms.ScrollOrientation.Vertical)， [ `Horizontal` ](xref:Xamarin.Forms.ScrollOrientation.Horizontal)，或[ `Both` ](xref:Xamarin.Forms.ScrollOrientation.Both)。 默认值为 `Vertical`。 如果的内容`ScrollView`是`StackLayout`，两个方向应保持一致。

[ **ReflectedColors** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/ReflectedColors)示例演示如何使用`ScrollView`和`StackLayout`以显示可用的颜色。 此示例还演示如何使用.NET 反射来获取所有的公共静态属性和字段的`Color`而无需明确列出这些参数的结构。

## <a name="the-expands-option"></a>展开此项选项

当`StackLayout`堆栈及其子级，每个子占用的总高度内的特定位置`StackLayout`的子范围的大小和的设置取决于其`HorizontalOptions`和`VerticalOptions`属性。 这些属性分配类型的值[ `LayoutOptions` ](http://developer.xamstage.com/api/type/Xamarin.Forms.LayoutOptions/)。

`LayoutOptions`结构定义两个属性：

- [`Alignment`](xref:Xamarin.Forms.LayoutOptions.Alignment) 枚举类型的[ `LayoutAlignment` ](xref:Xamarin.Forms.LayoutAlignment)具有四个成员[ `Start` ](xref:Xamarin.Forms.LayoutAlignment.Start)， [ `Center` ](xref:Xamarin.Forms.LayoutAlignment.Center)， [ `End` ](xref:Xamarin.Forms.LayoutAlignment.End)，并 [`Fill`](xref:Xamarin.Forms.LayoutAlignment.Fill)
- [`Expands`](xref:Xamarin.Forms.LayoutOptions.Expands) 类型 `bool`

为方便起见，`LayoutOptions`结构还定义了类型的八个静态只读字段`LayoutOptions`那些包含两个实例属性的所有组合：

- [`LayoutOptions.Start`](xref:Xamarin.Forms.LayoutOptions.Start)
- [`LayoutOptions.Center`](xref:Xamarin.Forms.LayoutOptions.Center)
- [`LayoutOptions.End`](xref:Xamarin.Forms.LayoutOptions.End)
- [`LayoutOptions.Fill`](xref:Xamarin.Forms.LayoutOptions.Fill)
- [`LayoutOptions.StartAndExpand`](xref:Xamarin.Forms.LayoutOptions.StartAndExpand)
- [`LayoutOptions.CenterAndExpand`](xref:Xamarin.Forms.LayoutOptions.CenterAndExpand)
- [`LayoutOptions.EndAndExpand`](xref:Xamarin.Forms.LayoutOptions.EndAndExpand)
- [`LayoutOptions.FillAndExpand`](xref:Xamarin.Forms.LayoutOptions.FillAndExpand)

下面的讨论涉及`StackLayout`默认垂直方向。 水平`StackLayout`类似。

对于垂直`StackLayout`，则`HorizontalOptions`设置确定的宽度内如何水平定位子`StackLayout`。 `Alignment`设置为`Start`， `Center`，或`End`导致为水平方向不受约束的子级。 子确定其自身的宽度和位于左、 居中或右的`StackLayout`。 `Fill`选项将导致子要水平约束并填充的宽度`StackLayout`。

对于垂直`StackLayout`、 每个子级是垂直方向不受约束和获取垂直槽具体取决于子级的高度，在这种情况下`VerticalOptions`设置不起作用。

如果垂直`StackLayout`本身是不受约束&mdash;也就是说如果其`VerticalOptions`设置为`Start`， `Center`，或`End`，然后的高度`StackLayout`是及其子级的总高度。

但是，如果垂直`StackLayout`垂直约束&mdash;如果其`VerticalOptions`设置为`Fill`&mdash;然后的高度`StackLayout`将为其容器，这可能是大于总的高度及其子级的高度。 如果出现这种情况，并且有至少一个子`VerticalOptions`设置，并`Expands`标记`true`，然后中的额外空间`StackLayout`具有所有这些子项之间均匀分配`Expands`标志的`true`。 子级的总高度然后将等于的高度`StackLayout`，并`Alignment`属于`VerticalOptions`设置确定子如何垂直定位在其插槽中。

了这一点[ **VerticalOptionsDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/VerticalOptionsDemo)示例。

## <a name="frame-and-boxview"></a>框架和字数

这两个矩形视图通常用于演示目的。

[ `Frame` ](xref:Xamarin.Forms.Frame)视图显示另一个视图，可以是一种布局如矩形边框`StackLayout`。 `Frame` 继承[ `Content` ](xref:Xamarin.Forms.ContentView.Content)属性从[ `ContentView` ](xref:Xamarin.Forms.ContentView)设置为该视图中显示`Frame`。 `Frame`默认为透明。 设置框架的外观进行自定义的以下三个属性：

- [ `OutlineColor` ](xref:Xamarin.Forms.Frame.OutlineColor)属性以使其可见。 通常会设置`OutlineColor`到`Color.Accent`时不知道基础的配色方案。
- [ `HasShadow` ](xref:Xamarin.Forms.Frame.HasShadow)属性设置为`true`在 iOS 设备上显示黑色阴影。
- 设置[ `Padding` ](xref:Xamarin.Forms.Layout.Padding)属性设置为`Thickness`值，以在框架和框架之间留一个空格的内容。 默认值为所有边的 20 个单位。

`Frame`具有默认值`HorizontalOptions`并`VerticalOptions`的值`LayoutOptions.Fill`，这意味着，`Frame`将填充其容器。 使用其他设置的大小`Frame`基于其内容的大小。

`Frame`中所示[ **FramedText** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/FramedText)示例。

[ `BoxView` ](xref:Xamarin.Forms.BoxView)显示由指定的颜色的矩形区域及其[ `Color` ](xref:Xamarin.Forms.BoxView.Color)属性。

如果`BoxView`受到约束 (其`HorizontalOptions`并`VerticalOptions`属性将其默认设置的`LayoutOptions.Fill`)，则`BoxView`为其将填充可用空间。 如果`BoxView`是不受约束 (与`HorizontalOptions`并`LayoutOptions`的设置`Start`， `Center`，或`End`)，它具有一个默认尺寸为 40 单位正方形。 一个`BoxView`可以被限制在一个维度和其他不受约束。

通常情况下，您将设置[ `WidthRequest` ](xref:Xamarin.Forms.VisualElement.WidthRequest)并[ `HeightRequest` ](xref:Xamarin.Forms.VisualElement.HeightRequest)的属性`BoxView`以便为其提供特定的大小。 阐明了这一点[ **SizedBoxView** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/SizedBoxView)示例。

可以使用的多个实例`StackLayout`合并`BoxView`和几种`Label`实例中`Frame`显示特定颜色，然后将每个视图中放`StackLayout`中`ScrollView`创建极具吸引力列表中显示的颜色[ **ColorBlocks** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/ColorBlocks)示例：

[![颜色块的三个屏幕截图](images/ch04fg11-small.png "颜色列表")](images/ch04fg11-large.png#lightbox "列表的颜色")

## <a name="a-scrollview-in-a-stacklayout"></a>在 StackLayout 中 ScrollView？

将放`StackLayout`中`ScrollView`很常用，但放置`ScrollView`中`StackLayout`有时也是方便。 从理论上讲，这应该不是可能因为垂直的子级`StackLayout`是垂直方向不受约束。 但`ScrollView`必须垂直约束。 必须为其提供特定的高度，以便它能够进行滚动然后确定其子项的大小。

此技巧是为了让`ScrollView`的子`StackLayout``VerticalOptions`设置为`FillAndExpand`。 了这一点[ **BlackCat** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/BlackCat)示例。

**BlackCat**示例还演示了如何定义和访问的共享库中嵌入的程序资源。 这还可以实现使用共享资产项目 (SAPs)，但此过程稍微有些棘手，作为[ **BlackCatSap** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/BlackCatSap)示例演示。

## <a name="related-links"></a>相关链接

- [第 4 章全文 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch04-Apr2016.pdf)
- [第 4 章示例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04)
- [第 4 章F#示例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/FS)
- [StackLayout](~/xamarin-forms/user-interface/layouts/stack-layout.md)
- [ScrollView](~/xamarin-forms/user-interface/layouts/scroll-view.md)
- [BoxView](~/xamarin-forms/user-interface/boxview.md)
