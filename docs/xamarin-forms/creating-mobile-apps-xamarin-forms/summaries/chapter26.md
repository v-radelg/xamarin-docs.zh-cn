---
title: 第 26 章的摘要。 自定义布局
description: 用 Xamarin 创建移动应用程序：第 26 章的摘要。 自定义布局
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 2B7F4346-414E-49FF-97FB-B85E92D98A21
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: 1eb5153f8ab295696e373f4fdb65a4f8820a05bc
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70770940"
---
# <a name="summary-of-chapter-26-custom-layouts"></a>第 26 章的摘要。 自定义布局

[![下载示例](~/media/shared/download.png)下载示例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26)

Xamarin.Forms 包含多个类派生自[ `Layout<View>` ](xref:Xamarin.Forms.Layout`1):

- `StackLayout`,
- `Grid`,
- `AbsoluteLayout` 和
- `RelativeLayout`。

本章节介绍了如何创建您自己的类派生自`Layout<View>`。

## <a name="an-overview-of-layout"></a>布局事件的概述

处理 Xamarin.Forms 布局没有集中式的系统。 每个元素负责确定其自身的大小应该是什么，以及如何在特定区域内呈现自身。

### <a name="parents-and-children"></a>父级和子级

具有子级的每个元素负责定位在自身内部这些子级。 它是应基于最终确定的大小及其子级的父级的大小有可用和希望成为子项的大小。

### <a name="sizing-and-positioning"></a>大小和位置

布局开始与页面的可视化树的顶部，然后在继续进行所有分支。 在布局中最重要的公共方法是[ `Layout` ](xref:Xamarin.Forms.VisualElement.Layout(Xamarin.Forms.Rectangle))定义的`VisualElement`。 是其他元素调用的父级的每个元素`Layout`为每个子级的大小和位置相对于自身的窗体中提供子[ `Rectangle` ](xref:Xamarin.Forms.Rectangle)值。 这些`Layout`调用传播到整个可视化树。

调用`Layout`是为了使元素能够显示在屏幕上必需的会导致以下的只读属性设置。 它们是与一致`Rectangle`传递给该方法：

- [`Bounds`](xref:Xamarin.Forms.VisualElement.Bounds) 类型 `Rectangle`
- [`X`](xref:Xamarin.Forms.VisualElement.X) 类型 `double`
- [`Y`](xref:Xamarin.Forms.VisualElement.Y) 类型 `double`
- [`Width`](xref:Xamarin.Forms.VisualElement.Width) 类型 `double`
- [`Height`](xref:Xamarin.Forms.VisualElement.Height) 类型 `double`

早于`Layout`调用，请`Height`并`Width`模拟值为&ndash;1。

调用`Layout`还会触发对以下受保护方法的调用：

- [`SizeAllocated`](xref:Xamarin.Forms.VisualElement.SizeAllocated(System.Double,System.Double))它调用
- [`OnSizeAllocated`](xref:Xamarin.Forms.VisualElement.OnSizeAllocated(System.Double,System.Double))其中可以重写。

最后，将触发以下事件：

- [`SizeChanged`](xref:Xamarin.Forms.VisualElement.SizeChanged)

`OnSizeAllocated`方法重写`Page`和`Layout`，这是能够拥有子项的 Xamarin.Forms 中的只有两个类。 重写的方法调用

- [`UpdateChildrenLayout`](xref:Xamarin.Forms.Page.UpdateChildrenLayout) 有关`Page`派生类和[ `UpdateChildrenLayout` ](xref:Xamarin.Forms.Layout.UpdateChildrenLayout)为`Layout`派生类，调用
- [`LayoutChildren`](xref:Xamarin.Forms.Page.LayoutChildren(System.Double,System.Double,System.Double,System.Double)) 有关`Page`派生类和[ `LayoutChildren` ](xref:Xamarin.Forms.Layout.LayoutChildren(System.Double,System.Double,System.Double,System.Double))为`Layout`派生类。

`LayoutChildren` 然后，调用`Layout`为每个元素的子级。 如果至少一个子级有一个新的`Bounds`设置，然后触发以下事件：

- [`LayoutChanged`](xref:Xamarin.Forms.Page.LayoutChanged) 有关`Page`派生类和[ `LayoutChanged` ](xref:Xamarin.Forms.Layout.LayoutChanged)为`Layout`派生类

### <a name="constraints-and-size-requests"></a>约束和大小的请求

有关`LayoutChildren`智能地调用`Layout`上其所有子项，它必须知道*首选*或*需*大小的子级。 因此对调用`Layout`通过调用通常前面的每一个子节点

- [`GetSizeRequest`](xref:Xamarin.Forms.VisualElement.GetSizeRequest(System.Double,System.Double))

本书已发布后，`GetSizeRequest`方法已弃用，并替换为

- [`Measure`](xref:Xamarin.Forms.VisualElement.Measure(System.Double,System.Double,Xamarin.Forms.MeasureFlags))

`Measure`方法可容纳[ `Margin` ](xref:Xamarin.Forms.View.Margin)属性并包含类型的自变量[ `MeasureFlag` ](xref:Xamarin.Forms.MeasureFlags)，其中包含两个成员：

- [`IncludeMargins`](xref:Xamarin.Forms.MeasureFlags.IncludeMargins)
- [`None`](xref:Xamarin.Forms.MeasureFlags.None) 若要不包括边距

对于很多元素，`GetSizeRequest`或`Measure`从其呈现器获取本机元素的大小。 这两种方法具有参数的宽度和高度*约束*。 例如，`Label`将使用的宽度约束来确定如何包装多行文本。

这两`GetSizeRequest`并`Measure`返回类型的值[ `SizeRequest` ](xref:Xamarin.Forms.SizeRequest)，其中包含两个属性：

- [`Request`](xref:Xamarin.Forms.SizeRequest.Request) 类型 `Size`
- [`Minimum`](xref:Xamarin.Forms.SizeRequest.Minimum) 类型 `Size`

这两个值通常是相同的并且`Minimum`值通常可以忽略。

`VisualElement` 此外定义了受保护的方法类似于`GetSizeRequest`从中调用`GetSizeRequest`:

- [`OnSizeRequest`](xref:Xamarin.Forms.VisualElement.OnSizeRequest(System.Double,System.Double)) 返回`SizeRequest`值

该方法现在已弃用，并替换为：

- [`OnMeasure`](xref:Xamarin.Forms.VisualElement.OnMeasure(System.Double,System.Double))

每个类都派生自`Layout`或`Layout<T>`必须重写`OnSizeRequest`或`OnMeasure`。 这就是其中一个布局类确定其自身的大小，通常基于其子元素，它通过调用获取的大小`GetSizeRequest`或`Measure`对的子对象。 之前和之后调用`OnSizeRequest`或`OnMeasure`，`GetSizeRequest`或`Measure`进行调整基于以下属性：

- [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest)类型的`double`，会影响`Request`属性 `SizeRequest`
- [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) 类型的`double`，会影响`Request`属性 `SizeRequest`
- [`MinimumWidthRequest`](xref:Xamarin.Forms.VisualElement.MinimumWidthRequest) 类型的`double`，会影响`Minimum`属性 `SizeRequest`
- [`MinimumHeightRequest`](xref:Xamarin.Forms.VisualElement.MinimumHeightRequest) 类型的`double`，会影响`Minimum`属性 `SizeRequest`

### <a name="infinite-constraints"></a>无限的约束

约束参数传递给`GetSizeRequest`(或`Measure`) 和`OnSizeRequest`(或`OnMeasure`) 可以是无限期 (即，值`Double.PositiveInfinity`)。 但是，`SizeRequest`返回从这些方法不能包含无限维度。

无限约束指示所请求的大小应反映元素的自然大小。 垂直`StackLayout`调用`GetSizeRequest`(或`Measure`) 对其具有无限高度约束的子对象。 水平堆叠布局调用`GetSizeRequest`(或`Measure`) 对其具有无限宽度约束的子对象。 `AbsoluteLayout`调用`GetSizeRequest`(或`Measure`) 对其具有无限的宽度和高度约束的子对象。

### <a name="peeking-inside-the-process"></a>查看进程内部

[ **ExploreChildSize** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/ExploreChildSizes)显示约束和大小请求信息的简单布局。

## <a name="deriving-from-layoutview"></a>从布局\<视图派生 >

自定义布局类派生自`Layout<View>`。 它具有两项职责：

- 重写`OnMeasure`调用`Measure`对布局的所有子对象。 返回自身的布局的请求的大小
- 重写`LayoutChildren`调用`Layout`对布局的所有子对象

`for`或`foreach`这些重写中的循环应跳过任何子其`IsVisible`属性设置为`false`。

调用`OnMeasure`不能保证。 `OnMeasure` 不会调用如果布局的父级用于控制布局的大小 （例如，填充页面布局）。 出于此原因，`LayoutChildren`不能依赖于子大小期间获取`OnMeasure`调用。 非常频繁`LayoutChildren`本身必须调用`Measure`对布局的子对象，也可以实现某种类型的缓存逻辑 （稍后讨论） 的大小。

### <a name="an-easy-example"></a>一个简单示例

[ **VerticalStackDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/VerticalStackDemo)示例包含的简化[ `VerticalStack` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter26/VerticalStackDemo/VerticalStackDemo/VerticalStackDemo/VerticalStack.cs)类和其用法的演示。

### <a name="vertical-and-horizontal-positioning-simplified"></a>垂直和水平定位简化

一个作业的`VerticalStack`必须执行过程中发生`LayoutChildren`重写。 该方法使用的子`HorizontalOptions`属性来确定如何在其槽内定位子`VerticalStack`。 可以改为调用静态方法[ `Layout.LayoutChildIntoBoundingRect` ](xref:Xamarin.Forms.Layout.LayoutChildIntoBoundingRegion(Xamarin.Forms.VisualElement,Xamarin.Forms.Rectangle))。 此方法调用`Measure`上子并使用其`HorizontalOptions`和`VerticalOptions`属性来定位子指定矩形中的。

### <a name="invalidation"></a>失效

元素的属性中的更改通常会影响该元素在布局中的显示方式。 布局必须是未经验证，以触发新的布局。

`VisualElement` 定义受保护的方法[ `InvalidateMeasure` ](xref:Xamarin.Forms.VisualElement.InvalidateMeasure)，这通常称为属性更改处理程序的任何可绑定属性的更改会影响该元素的大小。 `InvalidateMeasure`方法将触发[ `MeasureInvalidated` ](xref:Xamarin.Forms.VisualElement.MeasureInvalidated)事件。

`Layout`类定义一个名为类似的受保护的方法[ `InvalidateLayout` ](xref:Xamarin.Forms.Layout.InvalidateLayout)，该`Layout`派生类应调用的任何更改仅影响如何定位和调整大小及其子级。

### <a name="some-rules-for-coding-layouts"></a>编码布局的一些规则

1. 定义的属性`Layout<T>`派生类应由可绑定属性和属性更改处理程序应调用`InvalidateLayout`。

2. 一个`Layout<T>`定义附加可绑定属性的派生类应重写[ `OnAdded` ](xref:Xamarin.Forms.Layout`1.OnAdded*)若要将属性更改处理程序添加到其子并[ `OnRemoved` ](xref:Xamarin.Forms.Layout`1.OnRemoved*)若要删除的处理程序。 该处理程序应检查在这些更改附加的可绑定属性，并通过调用来响应`InvalidateLayout`。

3. 一个`Layout<T>`实现子大小的缓存的派生类应重写`InvalidateLayout`并[ `OnChildMeasureInvalidated` ](xref:Xamarin.Forms.Layout.OnChildMeasureInvalidated)和调用这些方法时清除缓存。

### <a name="a-layout-with-properties"></a>具有属性的布局

[ `WrapLayout` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/WrapLayout.cs)类[ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)假定其所有子级都都相同的大小，并将从一个行 （或列） 的子级包装到下一步。 它定义`Orientation`类似属性`StackLayout`，并`ColumnSpacing`并`RowSpacing`属性，如`Grid`，并将其缓存子大小。

[ **PhotoWrap** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/PhotoWrap)示例 put`WrapLayout`中`ScrollView`用于显示照片存储。

### <a name="no-unconstrained-dimensions-allowed"></a>不允许不受约束的维度 ！

[ `UniformGridLayout` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/UniformGridLayout.cs)中[ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)库用来显示其在自身内部的所有子项。 因此，它无法处理的不受约束的维度，并且如果遇到其中一个，则会引发异常。

[**照片网格**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/PhotoGrid)示例演示`UniformGridLayout`:

[![照片网格的三个屏幕截图](images/ch26fg08-small.png "统一的网格布局")](images/ch26fg08-large.png#lightbox "统一的网格布局")

### <a name="overlapping-children"></a>重叠的子级

一个`Layout<T>`派生类可以重叠及其子级。 但是，在按顺序呈现子级`Children`集合，并不依据的顺序及其`Layout`调用方法。

`Layout`类定义允许您移动子集合中的两个方法：

- [`LowerChild`](xref:Xamarin.Forms.Layout.LowerChild(Xamarin.Forms.View)) 若要将子级移到集合的开头
- [`RaiseChild`](xref:Xamarin.Forms.Layout.RaiseChild(Xamarin.Forms.View)) 若要将子级移动到集合的末尾

对于重叠子集合的末尾处的子级直观地显示基于集合的开始处的子级。

[ `OverlapLayout` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/OverlapLayout.cs)类[ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)库定义附加的属性来指示呈现顺序，从而允许一个其若要在其他顶部显示的子级。 [ **StudentCardFile** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/StudentCardFile)示例演示了此：

[![学生卡文件网格的三个屏幕截图](images/ch26fg10-small.png "重叠布局子级")](images/ch26fg10-large.png#lightbox "重叠布局子级")

### <a name="more-attached-bindable-properties"></a>附加可绑定属性

[ `CartesianLayout` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/CartesianLayout.cs)类[ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)库定义了附加的可绑定属性来指定两个`Point`值和一个粗细值和操作`BoxView`元素类似于行。

[ **UnitCube** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/UnitCube)示例使用的绘制三维的多维数据集。

### <a name="layout-and-layoutto"></a>布局和 LayoutTo

一个`Layout<T>`派生类可以调用`LayoutTo`而非`Layout`进行动画处理布局。 [ `AnimatedCartesianLayout` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/AnimatedCartesianLayout.cs)类实现此目的，并且[ **AnimatedUnitCube** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/AnimatedUnitCube)示例说明了这一点。

## <a name="related-links"></a>相关链接

- [第 26 章全文 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch26-Apr2016.pdf)
- [第 26 章示例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26)
- [创建自定义布局](~/xamarin-forms/user-interface/layouts/custom.md)
