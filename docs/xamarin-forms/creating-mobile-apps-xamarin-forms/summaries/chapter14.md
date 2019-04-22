---
title: 第 14 章的摘要。 绝对布局
description: 使用 Xamarin.Forms 创建移动应用：第 14 章的摘要。 绝对布局
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 88882A48-3226-42D1-96ED-241250B64A84
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
ms.openlocfilehash: 7371f134944d7492e51aa2d02247c0ab48345a47
ms.sourcegitcommit: 3489c281c9eb5ada2cddf32d73370943342a1082
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2019
ms.locfileid: "58870230"
---
# <a name="summary-of-chapter-14-absolute-layout"></a>第 14 章的摘要。 绝对布局

[![下载示例](~/media/shared/download.png)下载示例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14)

像`StackLayout`， [ `AbsoluteLayout` ](xref:Xamarin.Forms.AbsoluteLayout)派生自`Layout<View>`，并继承`Children`属性。 `AbsoluteLayout` 实现指定的位置及其子级和它们的大小，（可选），程序员需要布局系统。 位置由相对于左上角的子窗口左上角指定`AbsoluteLayout`以与设备无关单位。 `AbsoluteLayout` 此外实现按比例定位和调整大小功能。

`AbsoluteLayout` 应将顺序视为仅当程序员可以施加大小对的子对象时要使用的特殊用途布局系统 (例如，`BoxView`元素) 或当元素的大小并不会影响其他子级的位置。 `HorizontalOptions`并`VerticalOptions`属性不起作用的子级上`AbsoluteLayout`。

本章还引入了重要的功能*附加可绑定属性*允许在一个类中定义的属性 (在这种情况下`AbsoluteLayout`) 附加到另一个类 (的子级`AbsoluteLayout`)。

## <a name="absolutelayout-in-code"></a>在代码中的 AbsoluteLayout

您可以添加到子`Children`的集合`AbsoluteLayout`使用标准[ `Add` ](xref:System.Collections.Generic.ICollection`1.Add*)方法，但`AbsoluteLayout`还提供了扩展[ `Add` ](xref:Xamarin.Forms.AbsoluteLayout.IAbsoluteList`1.Add*)允许您指定的方法[ `Rectangle` ](xref:Xamarin.Forms.Rectangle)。 另一个[ `Add` ](xref:Xamarin.Forms.AbsoluteLayout.IAbsoluteList`1.Add*)方法仅需要[ `Point` ](xref:Xamarin.Forms.Point)，孩子在这种情况下是不受约束和调整自身大小。

您可以创建`Rectangle`具有值[构造函数](xref:Xamarin.Forms.Rectangle.%23ctor(System.Double,System.Double,System.Double,System.Double))需要四个值&mdash;指示相对于其父级的子级的左上角的位置的前两个和第二个两个，该值指示孩子的大小。 也可以使用[构造函数](xref:Xamarin.Forms.Rectangle.%23ctor(Xamarin.Forms.Point,Xamarin.Forms.Size))需要`Point`和一个[ `Size` ](xref:Xamarin.Forms.Size)值。

这些`Add`中演示的方法[ **AbsoluteDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/AbsoluteDemo)，哪些位置`BoxView`元素使用`Rectangle`的值，和一个`Label`使用只元素`Point`值。

[ **ChessboardFixed** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/ChessboardFixed)的示例使用 32`BoxView`元素来创建棋盘图案。 该程序提供了`BoxView`35 单位正方形的硬编码的元素大小。 `AbsoluteLayout`具有其`HorizontalOptions`并`VerticalOptions`设置为`LayoutOptions.Center`，这将导致`AbsoluteLayout`以 280 单位正方形的总大小。

## <a name="attached-bindable-properties"></a>附加可绑定属性

还有可能要设置的位置和 （可选） 的子项的大小`AbsoluteLayout`添加到之后`Children`使用静态方法的集合[ `AbsoluteLayout.SetLayoutBounds` ](xref:Xamarin.Forms.AbsoluteLayout.SetLayoutBounds(Xamarin.Forms.BindableObject,Xamarin.Forms.Rectangle))。 第一个参数是子;第二个是`Rectangle`对象。 您可以指定的子调整自身大小水平和/或垂直宽度和高度值设置为[ `AbsoluteLayout.AutoSize` ](xref:Xamarin.Forms.AbsoluteLayout.AutoSize)常量。

[ **ChessboardDynamic** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/ChessboardDynamic)示例 put`AbsoluteLayout`中`ContentView`与`SizeChanged`处理程序以调用`AbsoluteLayout.SetLayoutBounds`对所有的子对象以使它们尽可能大。  

附加可绑定属性的`AbsoluteLayout`定义为类型的静态只读字段`BindableProperty`名为[ `AbsoluteLayout.LayoutBoundsProperty` ](xref:Xamarin.Forms.AbsoluteLayout.LayoutBoundsProperty)。 静态`AbsoluteLayout.SetLayoutBounds`方法实现通过调用`SetValue`上具有的子级`AbsoluteLayout.LayoutBoundsProperty`。 子包含在其中存储附加可绑定属性和其值的字典。 在布局期间`AbsoluteLayout`可以通过调用来获取该值[ `AbsoluteLayout.GetLayoutBounds` ](xref:Xamarin.Forms.AbsoluteLayout.GetLayoutBounds(Xamarin.Forms.BindableObject))，这通过实现`GetValue`调用。

## <a name="proportional-sizing-and-positioning"></a>按比例调整大小和位置

`AbsoluteLayout` 实现按比例调整大小和定位功能。 类定义了第二个附加可绑定属性， [ `LayoutFlagsProperty` ](xref:Xamarin.Forms.AbsoluteLayout.LayoutFlagsProperty)，使用相关的静态方法[ `AbsoluteLayout.SetLayoutFlags` ](xref:Xamarin.Forms.AbsoluteLayout.SetLayoutFlags(Xamarin.Forms.BindableObject,Xamarin.Forms.AbsoluteLayoutFlags))并[ `AbsoluteLayout.GetLayoutFlags` ](xref:Xamarin.Forms.AbsoluteLayout.GetLayoutFlags(Xamarin.Forms.BindableObject))。

参数`AbsoluteLayout.SetLayoutFlags`和返回值的`AbsoluteLayout.GetLayoutFlags`是类型的值[ `AbsoluteLayoutFlags` ](xref:Xamarin.Forms.AbsoluteLayoutFlags)，具有以下成员的枚举：

- [`None`](xref:Xamarin.Forms.AbsoluteLayoutFlags.None) （等于 0）
- [`XProportional`](xref:Xamarin.Forms.AbsoluteLayoutFlags.XProportional) (1)
- [`YProportional`](xref:Xamarin.Forms.AbsoluteLayoutFlags.YProportional) (2)
- [`PositionProportional`](xref:Xamarin.Forms.AbsoluteLayoutFlags.PositionProportional) (3)
- [`WidthProportional`](xref:Xamarin.Forms.AbsoluteLayoutFlags.WidthProportional) (4)
- [`HeightProportional`](xref:Xamarin.Forms.AbsoluteLayoutFlags.HeightProportional) (8)
- [`SizeProportional`](xref:Xamarin.Forms.AbsoluteLayoutFlags.SizeProportional) (12)
- [`All`](xref:Xamarin.Forms.AbsoluteLayoutFlags.All) (\xFFFFFFFF)

可以将它们与组合C#按位 OR 运算符。

这些标志设置后，某些属性`Rectangle`布局边界结构使用的位置和大小子将按比例解释。

当`WidthProportional`设置标志，则`Width`值 1 表示，该子级则宽度相同`AbsoluteLayout`。 高度使用类似的方法。

按比例定位将考虑在内的大小。 当`XProportional`设置标志，则`X`属性的`Rectangle`布局边界是成比例。 值为 0 表示子项的左边缘置于左边缘`AbsoluteLayout`，但为 1 表示孩子的右边缘置于的右边缘的位置`AbsoluteLayout`，不超出的右边缘`AbsoluteLayout`可能 expect。 `X` 0.5 属性为中心水平在子`AbsoluteLayout`。

[ **ChessboardProportional** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/ChessboardProportional)示例演示如何使用按比例调整大小和位置。

## <a name="working-with-proportional-coordinates"></a>使用比例坐标

有时，很容易将按比例定位方式不同于中的实现方式`AbsoluteLayout`。 您可能希望使用比例坐标位置`X`属性为 1 的右边缘与定位子范围的左边的缘 （而不是右边缘） `AbsoluteLayout`。

定位此备选方案可以调用"小数部分子 coordinates"。 你可以从小数部分子坐标转换为所需的布局边界`AbsoluteLayout`使用以下公式：

layoutBounds.X = (fractionalChildCoordinate.X / (1-layoutBounds.Width))

layoutBounds.Y = (fractionalChildCoordinate.Y / (1-layoutBounds.Height))

[ **ProportionalCoordinateCalc** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/PropCoordCalc)示例演示了这。

## <a name="absolutelayout-and-xaml"></a>AbsoluteLayout 和 XAML

可以使用`AbsoluteLayout`在 XAML 中的子级上设置附加的可绑定属性和`AbsoluteLayout`使用的属性值`AbsoluteLayout.LayoutBounds`和`AbsoluteLayout.LayoutFlags`。 了这一点[ **AbsoluteXamlDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/AbsoluteXamlDemo)并[ **ChessboardXaml** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/ChessboardXaml)示例。 后一种程序包含 32`BoxView`元素，但使用隐式`Style`其中包括`AbsoluteLayout.LayoutFlags`属性以确保下一个最少标记。

包含类名称、 一个点和属性名称的 XAML 中的一个属性是*始终*附加可绑定属性。

## <a name="overlays"></a>覆盖层

可以使用`AbsoluteLayout`来构造*覆盖*，其中涵盖与其他控件，页面可能是为了防止用户与页面上的普通控件进行交互。

[ **SimpleOverlay** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/SimpleOverlay)示例演示了此种方法，并且还演示[ `ProgressBar` ](xref:Xamarin.Forms.ProgressBar)，后者将显示在程序完成向其的范围内任务。

## <a name="some-fun"></a>一些有趣

[ **DotMatrixClock** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/DotMatrixClock)示例显示的当前时间与模拟的 5 x 7 点矩阵显示。 每个圆点是`BoxView`（有其中 228） 大小调整和定位于`AbsoluteLayout`。

[![点阵时钟的三个屏幕截图](images/ch14fg08-small.png "点阵时钟")](images/ch14fg08-large.png#lightbox "点阵时钟")

[ **BouncingText** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/BouncingText)程序之间进行动画处理两个`Label`要在屏幕上弹跳水平和垂直方向的对象。



## <a name="related-links"></a>相关链接

- [第 14 章全文 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch14-Apr2016.pdf)
- [第 14 章示例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14)
- [AbsoluteLayout](~/xamarin-forms/user-interface/layouts/absolute-layout.md)
- [附加属性](~/xamarin-forms/xaml/attached-properties.md)
