---
title: Xamarin.Forms 网格
description: 本文介绍如何使用 Xamarin.Forms 网格类来呈现视图网格，具有行和列中。
ms.prod: xamarin
ms.assetid: 762B1802-D185-494C-B643-74EED55882FE
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/26/2017
ms.openlocfilehash: 7a6c737bff6c504fea10fd2e34e4b26249f73aff
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70770388"
---
# <a name="xamarinforms-grid"></a>Xamarin.Forms 网格

[![下载示例](~/media/shared/download.png)下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout)

[`Grid`](xref:Xamarin.Forms.Grid) 为行和列排列视图的支持。 可以设置行和列具有按比例大小或绝对大小。 `Grid`布局不应与传统表混淆和不应显示表格数据。 `Grid` 没有行、 列或单元格格式的概念。 HTML 与表不同，`Grid`纯粹用于对内容进行布局。

[![](grid-images/layouts-sml.png "Xamarin.Forms 布局")](grid-images/layouts.png#lightbox "Xamarin.Forms 布局")

本文将介绍：

- **[目的](#purpose)** &ndash;的常见用途`Grid`。
- **[使用情况](#usage)** &ndash;如何使用`Grid`来实现您所需的设计。
  - **[行和列](#rows-and-columns)** &ndash;指定行和列`Grid`。
  - **[将视图放置](#placing-views-in-a-grid)** &ndash;将视图添加到在网格中的特定行和列。
  - **[间距](#spacing)** &ndash;配置行和列之间的空格。
  - **[Span](#spans)**  &ndash;配置跨越多个行或列的元素。

![](grid-images/grid.png "网格探索")

## <a name="purpose"></a>目标

`Grid` 可用于排列到一个网格视图。 这是在许多情况下有用：

- 排列计算器应用程序中的按钮
- 在网格中，如 iOS 或 Android 的主屏幕中的排列按钮/选择
- 排列视图，以便可以在一个维度 （例如，在某些工具栏） 的大小相等的

## <a name="usage"></a>用法

与传统表不同`Grid`无法推断的数量和大小的行和列的内容。 相反，`Grid`已`RowDefinitions`和`ColumnDefinitions`集合。 这些日志包含行数和列布局方式的定义。视图将添加到`Grid`使用指定的行和列索引，该标识所属的行和列应置于一个视图。

### <a name="rows-and-columns"></a>行和列

行和列的信息存储在`Grid`的`RowDefinitions`  &  `ColumnDefinitions`属性，是每个集合的[ `RowDefinition` ](xref:Xamarin.Forms.RowDefinition)并[ `ColumnDefinition` ](xref:Xamarin.Forms.ColumnDefinition)对象，分别。 `RowDefinition` 只有一个属性， `Height`，并`ColumnDefinition`只有一个属性， `Width`。 高度和宽度的选项如下所示：

- **自动**&ndash;自动大小，以适应行或列中的内容。 指定作为[ `GridUnitType.Auto` ](xref:Xamarin.Forms.GridUnitType) C# 中或作为`Auto`在 XAML 中。
- **Proportional(*)** &ndash;为一定比例的剩余空间大小列和行。 指定为值和`GridUnitType.Star`用 C# 或作为`#*`XAML，在使用`#`正在所需的值。 指定一个行/列与`*`会让其以填充可用空间。
- **绝对**&ndash;调整列和具有特定的固定高度和宽度值的行的大小。 指定为值和`GridUnitType.Absolute`用 C# 或作为`#`XAML，在使用`#`正在所需的值。

> [!NOTE]
> 默认情况下，列的宽度值`*`设置为 Xamarin 中的默认值，这将确保该列填充可用空间。 默认情况下，行的高度值也`*`设置为。

请考虑的应用程序需要三个行和两个列。 底部行必须是完全 200px、 高和最上面一行必须是两次高度都是中间的一行。 左侧的列需要是足够宽，以适应内容，右侧列需要填充剩余的空间。

在 XAML:

```xaml
<Grid>
  <Grid.RowDefinitions>
    <RowDefinition Height="2*" />
    <RowDefinition Height="*" />
    <RowDefinition Height="200" />
  </Grid.RowDefinitions>
  <Grid.ColumnDefinitions>
    <ColumnDefinition Width="Auto" />
    <ColumnDefinition Width="*" />
  </Grid.ColumnDefinitions>
</Grid>
```

在 C# 中：

```csharp
Grid grid = new Grid();
grid.RowDefinitions.Add (new RowDefinition { Height = new GridLength(2, GridUnitType.Star) });
grid.RowDefinitions.Add (new RowDefinition { Height = new GridLength (1, GridUnitType.Star) });
grid.RowDefinitions.Add (new RowDefinition { Height = new GridLength(200)});
grid.ColumnDefinitions.Add (new ColumnDefinition{ Width = new GridLength (200) });
grid.ColumnDefinitions.Add(new ColumnDefinition { Width = new GridLength(1, GridUnitType.Auto) });
```

### <a name="placing-views-in-a-grid"></a>将视图放置在网格中

若要将视图中的放置`Grid`需要先将它们作为子级添加到网格中，然后再指定它们所属的行和列属于在中。

在 XAML 中，使用`Grid.Row`和`Grid.Column`指定放置每个单个视图。 请注意，`Grid.Row`和`Grid.Column`指定行和列的从零开始的列表为依据的位置。 这意味着在 4x4 网格中，左上角单元格为 (0，0) 和右下角单元格是 (3，3)。

`Grid`显示下面包含四个单元：

![](grid-images/label-grid.png "具有四个视图的网格")

在 XAML:

```xaml
<Grid>
  <Grid.RowDefinitions>
    <RowDefinition Height="*" />
    <RowDefinition Height="*" />
  </Grid.RowDefinitions>
  <Grid.ColumnDefinitions>
    <ColumnDefinition Width="*" />
    <ColumnDefinition Width="*" />
  </Grid.ColumnDefinitions>
  <Label Text="Top Left" Grid.Row="0" Grid.Column="0" />
  <Label Text="Top Right" Grid.Row="0" Grid.Column="1" />
  <Label Text="Bottom Left" Grid.Row="1" Grid.Column="0" />
  <Label Text="Bottom Right" Grid.Row="1" Grid.Column="1" />
</Grid>
```

在 C# 中：

```csharp
var grid = new Grid();

grid.RowDefinitions.Add(new RowDefinition { Height = new GridLength(1, GridUnitType.Star)});
grid.RowDefinitions.Add(new RowDefinition { Height = new GridLength(1, GridUnitType.Star)});
grid.ColumnDefinitions.Add(new ColumnDefinition { Width = new GridLength(1, GridUnitType.Star)});
grid.ColumnDefinitions.Add(new ColumnDefinition { Width = new GridLength(1, GridUnitType.Star)});

var topLeft = new Label { Text = "Top Left" };
var topRight = new Label { Text = "Top Right" };
var bottomLeft = new Label { Text = "Bottom Left" };
var bottomRight = new Label { Text = "Bottom Right" };

grid.Children.Add(topLeft, 0, 0);
grid.Children.Add(topRight, 1, 0);
grid.Children.Add(bottomLeft, 0, 1);
grid.Children.Add(bottomRight, 1, 1);
```

上面的代码中创建包含四个标签、 两个列和两行的网格。 请注意，每个标签将具有相同的大小和行将扩展以使用所有可用空间。

在上面的示例中，视图将添加到[ `Grid.Children` ](xref:Xamarin.Forms.Grid.Children)集合使用[ `Add` ](xref:Xamarin.Forms.Grid.IGridList`1.Add*)指定左侧和顶部参数的重载。 使用时[ `Add` ](xref:Xamarin.Forms.Grid.IGridList`1.Add*)重载，指定左、 右、 上边框和底部参数，而左侧和顶部参数将始终引用单元格内[ `Grid` ](xref:Xamarin.Forms.Grid)，右侧和底部参数可能看起来引用的单元格之外`Grid`。 这是因为右边的参数必须始终为大于左侧参数和底部参数必须始终为大于顶部的参数。 以下示例显示同时使用这二者的等效代码`Add`重载：

```csharp
// left, top
grid.Children.Add(topLeft, 0, 0);
grid.Children.Add(topRight, 1, 0);
grid.Children.Add(bottomLeft, 0, 1);
grid.Children.Add(bottomRight, 1, 1);

// left, right, top, bottom
grid.Children.Add(topLeft, 0, 1, 0, 1);
grid.Children.Add(topRight, 1, 2, 0, 1);
grid.Children.Add(bottomLeft, 0, 1, 1, 2);
grid.Children.Add(bottomRight, 1, 2, 1, 2);
```

### <a name="spacing"></a>间距

`Grid` 具有属性来控制行和列之间的间距。 以下属性可用于自定义用于`Grid`:

- **ColumnSpacing** &ndash;列之间的空间量。 此属性的默认值为 6。
- **RowSpacing** &ndash;行之间的空间量。 此属性的默认值为 6。

以下 XAML 指定`Grid`包含两列中，对应的一行和 5 像素的列之间的间距：

```xaml
<Grid ColumnSpacing="5">
  <Grid.ColumnDefinitions>
    <ColumnDefinition Width="*" />
    <ColumnDefinition Width="*" />
  </Grid.ColumnDefinitions>
</Grid>
```

在 C# 中：

```csharp
var grid = new Grid { ColumnSpacing = 5 };
grid.ColumnDefinitions.Add(new ColumnDefinition { Width = new GridLength (1, GridUnitType.Star)});
grid.ColumnDefinitions.Add(new ColumnDefinition { Width = new GridLength (1, GridUnitType.Star)});
```

### <a name="spans"></a>范围

通常如果使用的一个网格，则应占用多个行或列的元素。 请考虑一个简单的计算器应用程序：

![](grid-images/calculator.png "Calulator 应用程序")

请注意，0 按钮跨越就像两个列上为每个平台内置的计算器。 这通过`ColumnSpan`属性，用于指定列的数量的元素应占用。 该按钮的 XAML:

```xaml
<Button Text = "0" Grid.Row="4" Grid.Column="0" Grid.ColumnSpan="2" />
```

和 C# 中：

```csharp
Button zeroButton = new Button { Text = "0" };
controlGrid.Children.Add (zeroButton, 0, 4);
Grid.SetColumnSpan (zeroButton, 2);
```

请注意，在代码中，静态方法`Grid`类用于执行定位的更改包括对更改`ColumnSpan`和`RowSpan`。 此外请注意，可以在任何时候设置其他属性，与使用静态方法设置的属性必须已先在网格中发生了更改。

上面的计算器应用程序的完整 XAML 如下所示：

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="LayoutSamples.CalculatorGridXAML"
Title = "Calculator - XAML"
BackgroundColor="#404040">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style x:Key="plainButton" TargetType="Button">
                <Setter Property="BackgroundColor" Value="#eee"/>
                <Setter Property="TextColor" Value="Black" />
                <Setter Property="BorderRadius" Value="0"/>
                <Setter Property="FontSize" Value="40" />
            </Style>
            <Style x:Key="darkerButton" TargetType="Button">
                <Setter Property="BackgroundColor" Value="#ddd"/>
                <Setter Property="TextColor" Value="Black" />
                <Setter Property="BorderRadius" Value="0"/>
                <Setter Property="FontSize" Value="40" />
            </Style>
            <Style x:Key="orangeButton" TargetType="Button">
                <Setter Property="BackgroundColor" Value="#E8AD00"/>
                <Setter Property="TextColor" Value="White" />
                <Setter Property="BorderRadius" Value="0"/>
                <Setter Property="FontSize" Value="40" />
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>
    <ContentPage.Content>
        <Grid x:Name="controlGrid" RowSpacing="1" ColumnSpacing="1">
            <Grid.RowDefinitions>
                <RowDefinition Height="150" />
                <RowDefinition Height="*" />
                <RowDefinition Height="*" />
                <RowDefinition Height="*" />
                <RowDefinition Height="*" />
                <RowDefinition Height="*" />
            </Grid.RowDefinitions>
            <Grid.ColumnDefinitions>
                <ColumnDefinition Width="*" />
                <ColumnDefinition Width="*" />
                <ColumnDefinition Width="*" />
                <ColumnDefinition Width="*" />
            </Grid.ColumnDefinitions>
            <Label Text="0" Grid.Row="0" HorizontalTextAlignment="End" VerticalTextAlignment="End" TextColor="White"
        FontSize="60" Grid.ColumnSpan="4" />
            <Button Text = "C" Grid.Row="1" Grid.Column="0"
        Style="{StaticResource darkerButton}" />
            <Button Text = "+/-" Grid.Row="1" Grid.Column="1"
        Style="{StaticResource darkerButton}" />
            <Button Text = "%" Grid.Row="1" Grid.Column="2"
        Style="{StaticResource darkerButton}" />
            <Button Text = "div" Grid.Row="1" Grid.Column="3"
        Style="{StaticResource orangeButton}" />
            <Button Text = "7" Grid.Row="2" Grid.Column="0"
        Style="{StaticResource plainButton}" />
            <Button Text = "8" Grid.Row="2" Grid.Column="1"
        Style="{StaticResource plainButton}" />
            <Button Text = "9" Grid.Row="2" Grid.Column="2"
        Style="{StaticResource plainButton}" />
            <Button Text = "X" Grid.Row="2" Grid.Column="3"
        Style="{StaticResource orangeButton}" />
            <Button Text = "4" Grid.Row="3" Grid.Column="0"
        Style="{StaticResource plainButton}" />
            <Button Text = "5" Grid.Row="3" Grid.Column="1"
        Style="{StaticResource plainButton}" />
            <Button Text = "6" Grid.Row="3" Grid.Column="2"
        Style="{StaticResource plainButton}" />
            <Button Text = "-" Grid.Row="3" Grid.Column="3"
        Style="{StaticResource orangeButton}" />
            <Button Text = "1" Grid.Row="4" Grid.Column="0"
        Style="{StaticResource plainButton}" />
            <Button Text = "2" Grid.Row="4" Grid.Column="1"
        Style="{StaticResource plainButton}" />
            <Button Text = "3" Grid.Row="4" Grid.Column="2"
        Style="{StaticResource plainButton}" />
            <Button Text = "+" Grid.Row="4" Grid.Column="3"
        Style="{StaticResource orangeButton}" />
            <Button Text = "0" Grid.ColumnSpan="2"
        Grid.Row="5" Grid.Column="0" Style="{StaticResource plainButton}" />
            <Button Text = "." Grid.Row="5" Grid.Column="2"
        Style="{StaticResource plainButton}" />
            <Button Text = "=" Grid.Row="5" Grid.Column="3"
        Style="{StaticResource orangeButton}" />
        </Grid>
    </ContentPage.Content>
</ContentPage>
```

请注意顶部的网格的标签和零个按钮都是 occuping 多个列。 尽管无法使用嵌套的网格实现相似的布局`ColumnSpan`  &  `RowSpan`的方法是更简单。

C# 实现：

```csharp
public CalculatorGridCode ()
{
  Title = "Calculator - C#";
  BackgroundColor = Color.FromHex ("#404040");

  var plainButton = new Style (typeof(Button)) {
    Setters = {
      new Setter { Property = Button.BackgroundColorProperty, Value = Color.FromHex ("#eee") },
      new Setter { Property = Button.TextColorProperty, Value = Color.Black },
      new Setter { Property = Button.BorderRadiusProperty, Value = 0 },
      new Setter { Property = Button.FontSizeProperty, Value = 40 }
    }
  };
  var darkerButton = new Style (typeof(Button)) {
    Setters = {
      new Setter { Property = Button.BackgroundColorProperty, Value = Color.FromHex ("#ddd") },
      new Setter { Property = Button.TextColorProperty, Value = Color.Black },
      new Setter { Property = Button.BorderRadiusProperty, Value = 0 },
      new Setter { Property = Button.FontSizeProperty, Value = 40 }
    }
  };
  var orangeButton = new Style (typeof(Button)) {
    Setters = {
      new Setter { Property = Button.BackgroundColorProperty, Value = Color.FromHex ("#E8AD00") },
      new Setter { Property = Button.TextColorProperty, Value = Color.White },
      new Setter { Property = Button.BorderRadiusProperty, Value = 0 },
      new Setter { Property = Button.FontSizeProperty, Value = 40 }
    }
  };

  var controlGrid = new Grid { RowSpacing = 1, ColumnSpacing = 1 };
  controlGrid.RowDefinitions.Add (new RowDefinition { Height = new GridLength (150) });
  controlGrid.RowDefinitions.Add (new RowDefinition { Height = new GridLength (1, GridUnitType.Star) });
  controlGrid.RowDefinitions.Add (new RowDefinition { Height = new GridLength (1, GridUnitType.Star) });
  controlGrid.RowDefinitions.Add (new RowDefinition { Height = new GridLength (1, GridUnitType.Star) });
  controlGrid.RowDefinitions.Add (new RowDefinition { Height = new GridLength (1, GridUnitType.Star) });
  controlGrid.RowDefinitions.Add (new RowDefinition { Height = new GridLength (1, GridUnitType.Star) });

  controlGrid.ColumnDefinitions.Add (new ColumnDefinition { Width = new GridLength (1, GridUnitType.Star) });
  controlGrid.ColumnDefinitions.Add (new ColumnDefinition { Width = new GridLength (1, GridUnitType.Star) });
  controlGrid.ColumnDefinitions.Add (new ColumnDefinition { Width = new GridLength (1, GridUnitType.Star) });
  controlGrid.ColumnDefinitions.Add (new ColumnDefinition { Width = new GridLength (1, GridUnitType.Star) });

  var label = new Label {
    Text = "0",
    HorizontalTextAlignment = TextAlignment.End,
    VerticalTextAlignment = TextAlignment.End,
    TextColor = Color.White,
    FontSize = 60
  };
  controlGrid.Children.Add (label, 0, 0);

  Grid.SetColumnSpan (label, 4);

  controlGrid.Children.Add (new Button { Text = "C", Style = darkerButton }, 0, 1);
  controlGrid.Children.Add (new Button { Text = "+/-", Style = darkerButton }, 1, 1);
  controlGrid.Children.Add (new Button { Text = "%", Style = darkerButton }, 2, 1);
  controlGrid.Children.Add (new Button { Text = "div", Style = orangeButton }, 3, 1);
  controlGrid.Children.Add (new Button { Text = "7", Style = plainButton }, 0, 2);
  controlGrid.Children.Add (new Button { Text = "8", Style = plainButton }, 1, 2);
  controlGrid.Children.Add (new Button { Text = "9", Style = plainButton }, 2, 2);
  controlGrid.Children.Add (new Button { Text = "X", Style = orangeButton }, 3, 2);
  controlGrid.Children.Add (new Button { Text = "4", Style = plainButton }, 0, 3);
  controlGrid.Children.Add (new Button { Text = "5", Style = plainButton }, 1, 3);
  controlGrid.Children.Add (new Button { Text = "6", Style = plainButton }, 2, 3);
  controlGrid.Children.Add (new Button { Text = "-", Style = orangeButton }, 3, 3);
  controlGrid.Children.Add (new Button { Text = "1", Style = plainButton }, 0, 4);
  controlGrid.Children.Add (new Button { Text = "2", Style = plainButton }, 1, 4);
  controlGrid.Children.Add (new Button { Text = "3", Style = plainButton }, 2, 4);
  controlGrid.Children.Add (new Button { Text = "+", Style = orangeButton }, 3, 4);
  controlGrid.Children.Add (new Button { Text = ".", Style = plainButton }, 2, 5);
  controlGrid.Children.Add (new Button { Text = "=", Style = orangeButton }, 3, 5);

  var zeroButton = new Button { Text = "0", Style = plainButton };
  controlGrid.Children.Add (zeroButton, 0, 5);
  Grid.SetColumnSpan (zeroButton, 2);

  Content = controlGrid;
}
```

## <a name="related-links"></a>相关链接

- [借助 Xamarin.Forms，第 17 章创建移动应用](https://developer.xamarin.com/r/xamarin-forms/book/chapter17.pdf)
- [网格](xref:Xamarin.Forms.Grid)
- [布局 （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout)
- [BusinessTumble 示例 （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-businesstumble)
