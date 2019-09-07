---
title: 第 17 章的摘要。 控制网格
description: 用 Xamarin 创建移动应用程序：第 17 章的摘要。 控制网格
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 71EDEF9C-4220-4D2E-A235-43F1EC8746C1
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: 37b5e2bbafa816de27390771ae6daa33c74f7651
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70760630"
---
# <a name="summary-of-chapter-17-mastering-the-grid"></a>第 17 章的摘要。 控制网格

[![下载示例](~/media/shared/download.png)下载示例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17)

[ `Grid` ](xref:Xamarin.Forms.Grid)是一个强大的布局机制，排列为行和列的单元格的子项。 与类似 HTML 不同`table`元素，`Grid`是仅用于布局而不是演示文稿的目的。

## <a name="the-basic-grid"></a>基本网格

`Grid` 派生自[ `Layout<View>` ](xref:Xamarin.Forms.Layout`1)，用于定义[ `Children` ](xref:Xamarin.Forms.Layout`1.Children)属性的`Grid`继承。 您可以填充此集合在 XAML 或代码中。

### <a name="the-grid-in-xaml"></a>XAML 中的网格

定义`Grid`在 XAML 中通常开头填充[ `RowDefinitions` ](xref:Xamarin.Forms.Grid.RowDefinitions)并[ `ColumnDefinitions` ](xref:Xamarin.Forms.Grid.ColumnDefinitions)集合`Grid`与[ `RowDefinition`](xref:Xamarin.Forms.RowDefinition)并[ `ColumnDefinition` ](xref:Xamarin.Forms.ColumnDefinition)对象。 这是如何建立的行数和列的`Grid`，及其属性。

`RowDefinition` 具有[ `Height` ](xref:Xamarin.Forms.RowDefinition.Height)属性和`ColumnDefinition`具有[ `Width` ](xref:Xamarin.Forms.ColumnDefinition.Width)属性，这两个类型[ `GridLength` ](xref:Xamarin.Forms.GridLength)，一种结构。

在 XAML 中， [ `GridLengthTypeConverter` ](xref:Xamarin.Forms.GridLengthTypeConverter)将转换到的简单文本字符串`GridLength`值。 在后台[`GridLength`构造函数](xref:Xamarin.Forms.GridLength.%23ctor(System.Double,Xamarin.Forms.GridUnitType))创建`GridLength`值根据的数量和类型的值[ `GridUnitType` ](xref:Xamarin.Forms.GridUnitType)，具有三个成员的枚举：

- [`Absolute`](xref:Xamarin.Forms.GridUnitType.Absolute) &mdash; 设备无关的单位 （在 XAML 中的数字） 中指定的宽度或高度
- [`Auto`](xref:Xamarin.Forms.GridUnitType.Auto) &mdash; 高度或宽度是自动调整大小基于单元格内容 (如在 XAML 中的"Auto")
- [`Star`](xref:Xamarin.Forms.GridUnitType.Star) &mdash; 按比例分配剩余的高度或宽度 (带数字"\*"，称为*星型*，在 XAML 中)

每个子`Grid`必须指定行和列 （显式或隐式）。 跨越行和列跨度是可选的。 这些指定使用附加的可绑定属性&mdash;由定义的属性`Grid`但设置的子级`Grid`。 `Grid` 定义了四个静态附加可绑定属性：

- [`RowProperty`](xref:Xamarin.Forms.Grid.RowProperty) &mdash; 从零开始的行;默认值为 0
- [`ColumnProperty`](xref:Xamarin.Forms.Grid.ColumnProperty) &mdash; 从零开始的列;默认值为 0
- [`RowSpanProperty`](xref:Xamarin.Forms.Grid.RowSpanProperty) &mdash; 数量的行跨越子;默认值为 1
- [`ColumnSpanProperty`](xref:Xamarin.Forms.Grid.ColumnSpanProperty) &mdash; 数字列的子跨越;默认值为 1

在代码中，程序可以使用八个静态方法来设置和获取这些值：

- [`Grid.SetRow`](xref:Xamarin.Forms.Grid.SetRow(Xamarin.Forms.BindableObject,System.Int32)) 和 [`Grid.GetRow`](xref:Xamarin.Forms.Grid.GetRow(Xamarin.Forms.BindableObject))
- [`Grid.SetColumn`](xref:Xamarin.Forms.Grid.SetColumn(Xamarin.Forms.BindableObject,System.Int32)) 和 [`Grid.GetColumn`](xref:Xamarin.Forms.Grid.GetColumn(Xamarin.Forms.BindableObject))
- [`Grid.SetRowSpan`](xref:Xamarin.Forms.Grid.SetRowSpan(Xamarin.Forms.BindableObject,System.Int32)) 和 [`Grid.GetRowSpan`](xref:Xamarin.Forms.Grid.GetRowSpan(Xamarin.Forms.BindableObject))
- [`Grid.SetColumnSpan`](xref:Xamarin.Forms.Grid.SetColumnSpan(Xamarin.Forms.BindableObject,System.Int32)) 和 [`Grid.GetColumnSpan`](xref:Xamarin.Forms.Grid.GetColumnSpan(Xamarin.Forms.BindableObject))

在 XAML 中设置这些值使用以下属性：

- `Grid.Row`
- `Grid.Column`
- `Grid.RowSpan`
- `Grid.ColumnSpan`

[ **SimpleGridDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/SimpleGridDemo)示例演示如何创建和初始化`Grid`在 XAML 中。

`Grid`继承[ `Padding` ](xref:Xamarin.Forms.Layout.Padding)属性从`Layout`和定义提供的行和列之间的间距的两个其他属性：

- [`RowSpacing`](xref:Xamarin.Forms.Grid.RowSpacing) 默认值为 6
- [`ColumnSpacing`](xref:Xamarin.Forms.Grid.ColumnSpacing) 默认值为 6

`RowDefinitions`和`ColumnDefinitions`集合不是绝对必需的。 如果不存在，`Grid`创建行和列`Grid`子级，并提供所有这些默认值`GridLength`的"\*"（星号）。

### <a name="the-grid-in-code"></a>在代码中网格

[ **GridCodeDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridCodeDemo)示例演示如何创建并填充`Grid`在代码中。 可以设置附加的属性，为每个孩子直接或间接调用其他`Add`等方法[ `Add` ](xref:Xamarin.Forms.Grid.IGridList`1.Add*)由定义[Grid.IGridList<T> ](xref:Xamarin.Forms.Grid.IGridList`1)接口。

### <a name="the-grid-bar-chart"></a>网格条形图

[ **GridBarChart** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridBarChart)示例演示如何添加多个`BoxView`元素`Grid`使用大容量[ `AddHorizontal` ](xref:Xamarin.Forms.Grid.IGridList`1.AddHorizontal*)方法。 默认情况下，这些`BoxView`元素具有相等宽度。 每个高度`BoxView`控制为类似于条形图。

`Grid`中**GridBarChart**示例共享`AbsoluteLayout`与最初不可见的父`Frame`。 该程序还会设置`TapGestureRecognizer`每个`BoxView`若要使用`Frame`来显示分流条的信息。

### <a name="alignment-in-the-grid"></a>在网格中的对齐方式

[ **GridAlignment** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridAlignment)示例演示如何使用`VerticalOptions`并`HorizontalOptions`属性来对齐中的子级`Grid`单元格。

[ **SpacingButtons** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/SpacingButtons)示例同样空格`Button`元素中居中`Grid`单元格。

### <a name="cell-dividers-and-borders"></a>单元格分隔条和边框

`Grid`不包括一项功能，绘制单元格分隔线或边框。 但是，您可以使您自己。

[ **GridCellDividers** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridCellDividers)演示了如何定义其他行和列专用于精简`BoxView`元素来模拟分隔线。

[ **GridCellBorders** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridCellBorders)程序不会创建任何其他单元，但将改为对齐`BoxView`模仿单元格边框的每个单元中的元素。

## <a name="almost-real-life-grid-examples"></a>几乎实际网格示例

[ **KeypadGrid** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/KeypadGrid)的示例使用`Grid`以显示键盘：

[![键盘网格的三个屏幕截图](images/ch17fg12-small.png "键盘网格")](images/ch17fg12-large.png#lightbox "键盘网格")

### <a name="responding-to-orientation-changes"></a>响应方向更改

`Grid`有助于构建一个程序来响应方向更改。 [ **GridRgbSliders** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridRgbSliders)示例演示了一种方法，以纵向手机的第二行和面向布局的手机的第二个列之间移动元素。

在程序初始化`Slider`0 到 255，以及使用数据绑定，以十六进制格式显示的滑块的值在范围内的元素。 因为`Slider`点和.NET 格式字符串设置为仅适用于整数的十六进制值为浮动[ `DoubleToIntConvert` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/DoubleToIntConverter.cs)类[ **Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)库很有帮助。

## <a name="related-links"></a>相关链接

- [第 17 章全文 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch17-Apr2016.pdf)
- [第 17 章示例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17)
- [网格](~/xamarin-forms/user-interface/layouts/grid.md)
