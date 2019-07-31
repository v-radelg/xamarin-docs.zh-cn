---
title: 在 Xamarin.Forms 中的布局
description: Xamarin.Forms 具有多个布局和功能的组织在屏幕上，内容和此文章介绍了它们。
ms.prod: xamarin
ms.assetid: 65030DA3-C7C1-4A02-B478-811073C39139
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 12/18/2018
ms.openlocfilehash: 1889579a48364204a977d63bd9bdb875df37a2bf
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2019
ms.locfileid: "68657035"
---
# <a name="layouts-in-xamarinforms"></a>在 Xamarin.Forms 中的布局

[![下载示例](~/media/shared/download.png)下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout)

Xamarin.Forms 具有多个布局和功能的组织在屏幕上的内容。

> [!VIDEO https://youtube.com/embed/4HlLjTZQzjM]

**Xamarin. Forms 布局视频**

每个布局控件被下述，以及如何处理屏幕方向更改的详细信息：

* **[StackLayout](stack-layout.md)** –用于以线性方式 (水平或垂直) 排列视图。 视图在 StackLayout 中的可以对齐到中心，左或向右的布局。
* **[AbsoluteLayout](absolute-layout.md)** –用于通过设置坐标 & 大小 (以绝对值或比率为依据) 来排列视图。 AbsoluteLayout 可以用于分层视图，以及定位到左、 靠右或居中。
* **[RelativeLayout](relative-layout.md)** –用于通过相对于其父级的维度 & 位置设置约束来排列视图。
* **[网格](grid.md)** -用于排列网格中的视图。 根据绝对值或比率，可以指定行和列。
* **[FlexLayout](flex-layout.md)** -用于水平或垂直排列视图, 并进行换行。
* **[ScrollView](scroll-view.md)** –用于在视图无法完全容纳在屏幕边界内时提供滚动。
* **[LayoutOptions](layout-options.md)** -定义视图相对于其父级的对齐方式和展开。
* **[输入透明度](#input_transparency)** –指定元素是否接收输入。
* **[边距和边距](margin-and-padding.md)** –演示如何在用户界面中呈现元素时控制布局行为。
* **[设备方向](device-orientation.md)** -介绍如何处理设备方向更改。
* **[Tablet 和桌面设备上的布局](tablet.md)** –演示如何针对每个平台上的更大屏幕进行优化。
* 可 **[绑定布局](bindable-layouts.md)** -通过绑定到项的集合, 启用布局类以生成其内容。
* **[创建自定义布局](custom.md)** -介绍如何创建自定义布局类。
* **[布局压缩](layout-compression.md)** -从可视化树中删除指定的布局, 以尝试改善页面呈现性能。

平台控件还可直接在与 Xamarin.Forms 布局[**本机嵌入**](~/xamarin-forms/platform/native-views/index.md) （新 Xamarin.Forms 2.2 中），你可以[**创建自定义布局**](custom.md)来满足特定要求。

下图直观显示布局控件：

[![](images/layouts-sml.png "Xamarin.Forms 布局")](images/layouts.png#lightbox "Xamarin.Forms 布局")

## <a name="choosing-the-right-layout"></a>选择正确的布局

选择应用程序中的布局可以帮助或伤害您为要创建具有吸引力且可使用 Xamarin.Forms 应用。 花费一些时间来考虑每个布局也可帮助你编写更干净且更具缩放性 UI 代码。 屏幕可以具有不同的布局来实现特定的设计的组合。

### <a name="stacklayoutstack-layoutmd"></a>[StackLayout](stack-layout.md)

`StackLayout`用于显示视图沿水平或垂直的行。 根据视图的确定位置和布局中的大小`HeightRequest`， `WidthRequest`，`HorizontalOptions`和`VerticalOptions`。 `StackLayout` 通常用作基布局，排列在屏幕上的其他布局。

有关何时示例`StackLayout`会是一个不错的选择，请考虑的应用程序需要显示一个按钮和具有左对齐的标签和右对齐按钮的标签。

```xaml
<StackLayout Orientation="Horizontal">
  <Label HorizontalOptions="StartAndExpand" Text="Label" />
  <Button HorizontalOptions="End" Text="Button" />
</StackLayout>
```

### <a name="flexlayoutflex-layoutmd"></a>[FlexLayout](flex-layout.md)

`FlexLayout`类似于`StackLayout`，因为它在水平或垂直显示子视图：

```xaml
<FlexLayout Direction="Column"
            AlignItems="Center"
            JustifyContent="SpaceEvenly">

    <Label Text="FlexLayout in Action" />
    <Button Text="Button" />
    <Label Text="Another Label" />
</FlexLayout>
```

但是，如果过多的子节点中单个行或列，容纳不下`FlexLayout`仍可包装这些视图。 `FlexLayout` CSS 灵活框布局模块上，为基础，具有许多相同的内置选项用于定位和对齐及其子级。

### <a name="absolutelayoutabsolute-layoutmd"></a>[AbsoluteLayout](absolute-layout.md)

`AbsoluteLayout`用于显示具有作为显式值或相对于布局的大小的值的大小和位置所指定的视图。 与不同`StackLayout`并`Grid`，`AbsoluteLayout`允许子视图重叠。 与不同`RelativeLayout`，`AbsoluteLayout`不允许您将置于屏幕上的元素。

有关何时示例`AbsoluteLayout`会是一个不错的选择，请考虑的应用程序需要提供作为堆栈对象的集合。 上述方法通常被显示的照片或多首歌曲的唱片集时。 下面的代码使用旋转以提示的超时和累积内容元素提供一堆的外观：

在 XAML:

```xaml
<AbsoluteLayout Padding="15">
  <Image AbsoluteLayout.LayoutFlags="PositionProportional" AbsoluteLayout.LayoutBounds="0.5, 0, 100, 100" Rotation="30"
    Source="bottom.png" />
  <Image AbsoluteLayout.LayoutFlags="PositionProportional" AbsoluteLayout.LayoutBounds="0.5, 0, 100, 100" Rotation="60"
    Source="middle.png" />
  <Image AbsoluteLayout.LayoutFlags="PositionProportional" AbsoluteLayout.LayoutBounds="0.5, 0, 100, 100"
    Source="cover.png" />
</AbsoluteLayout>
```

请注意上述代码中的以下方面：

- 每个`Image`显示在同一位置 （中间的水平空间）
- `Padding`视为`AbsoluteLayout`，但不同于`RelativeLayout`，这会将其忽略。
- `AbsoluteLayout.LayoutFlags` 指定将如何解释布局范围。 在这种情况下`PositionProportional`，意味着，坐标将布局的大小比率而大小将被解释为显式的大小。
- `AbsoluteLayout.Layoutbounds` 按此顺序指定的水平位置、 垂直位置、 宽度和高度。

### <a name="relativelayoutrelative-layoutmd"></a>[RelativeLayout](relative-layout.md)

`RelativeLayout`用于显示视图大小和位置指定为相对于布局或另一个视图的值的值。 相对值不需要匹配他对应相关视图上的值。 例如，就可以设置的视图`Width`属性设置为另一个视图成正比`X`属性。

RelativeLayout 可以用于创建跨设备的大小按比例扩展的用户界面。 以下 XAML 实现以在最顶部边角的框是图案中带有 flagpole 中心中的标志：

```xaml
<RelativeLayout HorizontalOptions="FillAndExpand" VerticalOptions="FillAndExpand">
  <BoxView Color="Blue" HeightRequest="50" WidthRequest="50"
    RelativeLayout.XConstraint= "{ConstraintExpression Type=RelativeToParent, Property=Width, Factor = 0}"
    RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor = 0}" />
  <BoxView Color="Red" HeightRequest="50" WidthRequest="50"
    RelativeLayout.XConstraint= "{ConstraintExpression Type=RelativeToParent, Property=Width, Factor = .9}"
    RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor = 0}" />
  <BoxView Color="Gray" WidthRequest="15" x:Name="pole"
    RelativeLayout.HeightConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor=.75}"
    RelativeLayout.XConstraint= "{ConstraintExpression Type=RelativeToParent, Property=Width, Factor = .45}"
    RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor = .25}" />
  <BoxView Color="Green"
    RelativeLayout.HeightConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor=.10, Constant=10}"
    RelativeLayout.WidthConstraint="{ConstraintExpression Type=RelativeToParent,Property=Width, Factor=.2, Constant=20}"
    RelativeLayout.XConstraint= "{ConstraintExpression Type=RelativeToView, ElementName=pole, Property=X, Constant=15}"
    RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToView, ElementName=pole, Property=Y, Constant=0}" />
</RelativeLayout>
```

请注意上述代码中的以下方面：

- 位置和大小指定为约束。
- 名为 flagpole，以便该标志的 （绿色框的） 可以相对于 flagpole 设置位置。
- 约束表达式具有`Factor`和`Constant`属性，可用于为序列图中定义的位置和大小 （或小数部分） 的属性的其他对象，以及一个常量。 常量可以为负数。

### <a name="gridgridmd"></a>[网格](grid.md)

`Grid`用于显示行和列中的元素。 请注意，在网格不是表，因此它不具有这一概念的单元格、 页眉和页脚行或行与列之间的边框。 一般情况下，不适合于显示表格数据网格。 使用，请考虑[ListView](~/xamarin-forms/user-interface/listview/index.md)或[TableView](~/xamarin-forms/user-interface/tableview.md)。

有关何时示例`Grid`是正确的布局，若要使用，请考虑一个计算器数字输入。 一个计算器的数字输入可能包含四个行和三个列，每个都有一个按钮。 下面的代码实现此设计：

```xaml
<Grid>
  <Grid.RowDefinitions>
    <RowDefinition Height="*" />
    <RowDefinition Height="*" />
    <RowDefinition Height="*" />
    <RowDefinition Height="*" />
  </Grid.RowDefinitions>

  <Grid.ColumnDefinitions>
    <ColumnDefinition Width="*" />
    <ColumnDefinition Width="*" />
    <ColumnDefinition Width="*" />
  </Grid.ColumnDefinitions>
  <Button Text="1" Grid.Row="0" Grid.Column="0" />
  <Button Text="2" Grid.Row="0" Grid.Column="1" />
  <Button Text="3" Grid.Row="0" Grid.Column="2" />
  <Button Text="4" Grid.Row="1" Grid.Column="0" />
  <Button Text="5" Grid.Row="1" Grid.Column="1" />
  <Button Text="6" Grid.Row="1" Grid.Column="2" />
  <Button Text="7" Grid.Row="2" Grid.Column="0" />
  <Button Text="8" Grid.Row="2" Grid.Column="1" />
  <Button Text="9" Grid.Row="2" Grid.Column="2" />
  <Button Text="0" Grid.Row="3" Grid.Column="1" />
  <Button Text="&lt;-" Grid.Row="3" Grid.Column="2" />
</Grid>
```

请注意上述代码中的以下方面：

- 网格和列显式指定，不会推断出的内容。
- `Height` 和`Width`可将该值设置为星号，这意味着，网格会设置这些值以填充可用空间。
- 每个按钮的位置由指定`Grid.Row`  &  `Grid.Column`属性。

### <a name="layoutoptionslayout-optionsmd"></a>[LayoutOptions](layout-options.md)

[ `LayoutOptions` ](xref:Xamarin.Forms.LayoutOptions)结构可用于定义对齐方式和视图，相对于其父级的扩展。

### <a name="margin-and-paddingmargin-and-paddingmd"></a>[边距和填充](margin-and-padding.md)

[ `Margin` ](xref:Xamarin.Forms.View.Margin)并[ `Padding` ](xref:Xamarin.Forms.Layout.Padding)元素呈现用户界面中时，属性控制布局行为。

<a name="input_transparency" />

### <a name="input-transparency"></a>输入的透明度

每个元素有[ `InputTransparent` ](xref:Xamarin.Forms.VisualElement.InputTransparent)用于定义该元素是否接收输入的属性。 其默认值是`false`，确保元素接收输入。

当容器类，如布局类，其值传输到子元素上设置此属性。 因此，设置[ `InputTransparent` ](xref:Xamarin.Forms.VisualElement.InputTransparent)属性设置为`true`布局类将导致不接收输入布局中的元素。

### <a name="device-orientationdevice-orientationmd"></a>[设备方向](device-orientation.md)

Xamarin.Forms 和其内置布局是能够处理设备方向中的更改。 请考虑您的应用程序将支持，以及如何将使哪个方向的横向和纵向模式中提供的空间使用。

### <a name="layout-for-tablet-and-desktop-appstabletmd"></a>[对于平板电脑和桌面应用程序的布局](tablet.md)

iOS、 Android 和通用 Windows 平台支持更大的屏幕大小上的所有适用于平板电脑设备 （以及便携式计算机和 Windows 的桌面）。 使用 Xamarin.Forms，可以通过检测设备类型和调整页面布局中，或使用完全不同的页面完全用于较大的屏幕来优化您的应用程序的较大的屏幕。

### <a name="bindable-layoutsbindable-layoutsmd"></a>[可绑定的布局](bindable-layouts.md)

类可实现[`Layout<T>`](xref:Xamarin.Forms.Layout`1)从类派生的任何布局类, 通过绑定到项的集合生成其内容, 并且可以选择使用[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)设置每个项的外观。 `BindableLayout`

### <a name="creating-a-custom-layoutcustommd"></a>[创建自定义布局](custom.md)

Xamarin.Forms 定义了四个布局类- [ `StackLayout` ](xref:Xamarin.Forms.StackLayout)， [ `AbsoluteLayout` ](xref:Xamarin.Forms.AbsoluteLayout)， [ `RelativeLayout` ](xref:Xamarin.Forms.RelativeLayout)，并[ `Grid` ](xref:Xamarin.Forms.Grid)，和每个不同的方式排列子项。 但是，有时通过 Xamarin.Forms 进行必要的组织不使用的布局页内容提供。 本文介绍如何编写一个自定义布局的类，并说明了方向区分`WrapLayout`类跨页上，水平排列子项，然后将包装对其他行的后续子级的显示。

### <a name="layout-compressionlayout-compressionmd"></a>[布局压缩](layout-compression.md)

布局压缩从可视化树中删除指定的布局，以试图提升页面呈现性能。 这带来的性能优势因页面复杂性、要使用的操作系统版本以及运行应用的设备而异。 不过，在旧设备上实现的性能提升最大。

## <a name="making-your-choice"></a>使您的选择

请注意，在大多数情况下，多个布局选项可用于实现您所需的设计。 当存在多个有效的选项时，请考虑哪种方法将是最适合你的情况。
大多数设计无法实现与只是一种布局，因此需要创建更复杂的设计作为嵌套布局。

## <a name="related-links"></a>相关链接

- [Apple 的人机接口指南](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/MobileHIG)
- [Android 设计网站](https://developer.android.com/design/index.html)
- [布局 （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout)
- [BusinessTumble 示例 （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-businesstumble)
