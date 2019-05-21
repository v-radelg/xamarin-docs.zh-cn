---
title: 在 Xamarin.Forms 中的布局选项
description: 每个 Xamarin.Forms 视图具有类型 LayoutOptions 的 HorizontalOptions 和 VerticalOptions 属性。 本文介绍的每个 LayoutOptions 值对的对齐方式和扩展视图的影响。
ms.prod: xamarin
ms.assetid: 7CAB5631-5153-4DEF-8AD7-C6011CE44307
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/10/2017
ms.openlocfilehash: afeae9d893b2ebe161943286d348f7a7a1074b0b
ms.sourcegitcommit: b23a107b0fe3d2f814ae35b52a5855b6ce2a3513
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2019
ms.locfileid: "65925387"
---
# <a name="layout-options-in-xamarinforms"></a>在 Xamarin.Forms 中的布局选项

[![下载示例](~/media/shared/download.png)下载示例](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/LayoutOptions/)

_每个 Xamarin.Forms 视图具有类型 LayoutOptions 的 HorizontalOptions 和 VerticalOptions 属性。本文介绍的每个 LayoutOptions 值对的对齐方式和扩展视图的影响。_

## <a name="overview"></a>概述

[ `LayoutOptions` ](xref:Xamarin.Forms.LayoutOptions)结构封装两个布局首选项：

- **对齐方式**– 视图的首选对齐方式，确定其位置和其父布局中的大小。
- **扩展**– 仅由[ `StackLayout` ](xref:Xamarin.Forms.StackLayout)，和如果可用，该值指示视图是否应使用多余的空格。

这些布局首选项可应用于[ `View` ](xref:Xamarin.Forms.View)、 相对于其父级，通过设置[ `HorizontalOptions` ](xref:Xamarin.Forms.View.HorizontalOptions)或者[ `VerticalOptions` ](xref:Xamarin.Forms.View.VerticalOptions)属性`View`到一个公共字段从[ `LayoutOptions` ](xref:Xamarin.Forms.LayoutOptions)结构。 公共字段如下所示：

- [`Start`](xref:Xamarin.Forms.LayoutOptions.Start)
- [`Center`](xref:Xamarin.Forms.LayoutOptions.Center)
- [`End`](xref:Xamarin.Forms.LayoutOptions.End)
- [`Fill`](xref:Xamarin.Forms.LayoutOptions.Fill)
- [`StartAndExpand`](xref:Xamarin.Forms.LayoutOptions.StartAndExpand)
- [`CenterAndExpand`](xref:Xamarin.Forms.LayoutOptions.CenterAndExpand)
- [`EndAndExpand`](xref:Xamarin.Forms.LayoutOptions.EndAndExpand)
- [`FillAndExpand`](xref:Xamarin.Forms.LayoutOptions.FillAndExpand)

`Start`， `Center`， `End`，和`Fill`字段用于定义父布局中的视图的对齐方式：

- 为水平对齐方式[ `Start` ](xref:Xamarin.Forms.LayoutOptions.Start)位置[ `View` ](xref:Xamarin.Forms.View)上的左侧的父布局，并为垂直对齐方式，它将定位`View`顶部父布局。
- 水平和垂直对齐[ `Center` ](xref:Xamarin.Forms.LayoutOptions.Center)水平或垂直居中对齐[ `View` ](xref:Xamarin.Forms.View)。
- 为水平对齐方式[ `End` ](xref:Xamarin.Forms.LayoutOptions.End)位置[ `View` ](xref:Xamarin.Forms.View)上的父布局，并为垂直对齐方式的右下方，它将定位`View`底部父布局中。
- 为水平对齐方式[ `Fill` ](xref:Xamarin.Forms.LayoutOptions.Fill)确保[ `View` ](xref:Xamarin.Forms.View)填充宽度的父布局和垂直对齐方式，它可确保`View`填充父布局的高度。

`StartAndExpand`， `CenterAndExpand`， `EndAndExpand`，和`FillAndExpand`值用于定义对齐方式的首选项，以及是否在视图将占用更多的空间，如果父级范围内可用[ `StackLayout` ](xref:Xamarin.Forms.StackLayout)。

> [!NOTE]
> 视图的默认值[ `HorizontalOptions` ](xref:Xamarin.Forms.View.HorizontalOptions)并[ `VerticalOptions` ](xref:Xamarin.Forms.View.VerticalOptions)属性是[ `LayoutOptions.Fill` ](xref:Xamarin.Forms.LayoutOptions.Fill)。

<a name="alignment" />

## <a name="alignment"></a>对齐方式

对齐方式控制当父布局包含未使用的空间时视图中其父级布局的定位方式 （即父布局是其所有子项的合计大小更大）。

一个[ `StackLayout` ](xref:Xamarin.Forms.StackLayout)仅尊重`Start`， `Center`， `End`，以及`Fill` [ `LayoutOptions` ](xref:Xamarin.Forms.LayoutOptions)上在相反方向中的子视图的字段到`StackLayout`方向。 因此，子视图在垂直方向`StackLayout`可以设置其[ `HorizontalOptions` ](xref:Xamarin.Forms.View.HorizontalOptions)属性设置为之一`Start`， `Center`， `End`，或`Fill`字段。 同样，子视图中水平方向`StackLayout`可以设置其[ `VerticalOptions` ](xref:Xamarin.Forms.View.VerticalOptions)属性设置为其中一个`Start`， `Center`， `End`，或`Fill`字段。

一个[ `StackLayout` ](xref:Xamarin.Forms.StackLayout)不遵从`Start`， `Center`， `End`，以及`Fill` [ `LayoutOptions` ](xref:Xamarin.Forms.LayoutOptions)上的方向相同的子视图的字段`StackLayout`方向。 因此，垂直方向`StackLayout`将忽略`Start`， `Center`， `End`，或`Fill`字段上设置如果[ `VerticalOptions` ](xref:Xamarin.Forms.View.VerticalOptions)子视图的属性。 同样，水平方向`StackLayout`将忽略`Start`， `Center`， `End`，或`Fill`字段上设置如果[ `HorizontalOptions` ](xref:Xamarin.Forms.View.HorizontalOptions)子视图的属性。

> [!NOTE]
> [`LayoutOptions.Fill`](xref:Xamarin.Forms.LayoutOptions.Fill) 替代调整使用指定的请求的大小通常[ `HeightRequest` ](xref:Xamarin.Forms.VisualElement.HeightRequest)并[ `WidthRequest` ](xref:Xamarin.Forms.VisualElement.WidthRequest)属性。

下面的 XAML 代码示例演示了垂直方向[ `StackLayout` ](xref:Xamarin.Forms.StackLayout)其中每个子[ `Label` ](xref:Xamarin.Forms.Label)设置其[ `HorizontalOptions` ](xref:Xamarin.Forms.View.HorizontalOptions)属性中的四个对齐字段之一[ `LayoutOptions` ](xref:Xamarin.Forms.LayoutOptions)结构：

```xaml
<StackLayout Margin="0,20,0,0">
  ...
  <Label Text="Start" BackgroundColor="Gray" HorizontalOptions="Start" />
  <Label Text="Center" BackgroundColor="Gray" HorizontalOptions="Center" />
  <Label Text="End" BackgroundColor="Gray" HorizontalOptions="End" />
  <Label Text="Fill" BackgroundColor="Gray" HorizontalOptions="Fill" />
</StackLayout>
```

等效的 C# 代码如下所示：

```csharp
Content = new StackLayout
{
  Margin = new Thickness(0, 20, 0, 0),
  Children = {
    ...
    new Label { Text = "Start", BackgroundColor = Color.Gray, HorizontalOptions = LayoutOptions.Start },
    new Label { Text = "Center", BackgroundColor = Color.Gray, HorizontalOptions = LayoutOptions.Center },
    new Label { Text = "End", BackgroundColor = Color.Gray, HorizontalOptions = LayoutOptions.End },
    new Label { Text = "Fill", BackgroundColor = Color.Gray, HorizontalOptions = LayoutOptions.Fill }
  }
};
```

该代码会导致下面的屏幕截图中所示的布局：

[![](layout-options-images/alignment.png "对齐布局选项")](layout-options-images/alignment-large.png#lightbox "对齐布局选项")

<a name="expansion" />

## <a name="expansion"></a>扩展

扩展控制是否视图将占用更多空间，是否在内可用[ `StackLayout` ](xref:Xamarin.Forms.StackLayout)。 如果`StackLayout`包含未使用的空间 (即`StackLayout`大于所有子级的组合大小)，未使用的空间同样由通过设置请求扩展的所有子视图共享其[ `HorizontalOptions` ](xref:Xamarin.Forms.View.HorizontalOptions)或[ `VerticalOptions` ](xref:Xamarin.Forms.View.VerticalOptions)属性设置为[ `LayoutOptions` ](xref:Xamarin.Forms.LayoutOptions)使用字段`AndExpand`后缀。 请注意，当中的所有空间`StackLayout`是使用，扩展选项不起任何作用。

一个[ `StackLayout` ](xref:Xamarin.Forms.StackLayout)仅展开其方向的方向中的子视图。 因此，垂直方向`StackLayout`可以扩展设置的子视图及其[ `VerticalOptions` ](xref:Xamarin.Forms.View.VerticalOptions)属性设置为之一`StartAndExpand`， `CenterAndExpand`， `EndAndExpand`，或`FillAndExpand`字段，如果`StackLayout`包含未使用的空间。 同样，水平方向`StackLayout`可以扩展设置的子视图及其[ `HorizontalOptions` ](xref:Xamarin.Forms.View.HorizontalOptions)属性设置为其中一个`StartAndExpand`， `CenterAndExpand`， `EndAndExpand`，或`FillAndExpand`字段，如果`StackLayout`包含未使用的空间。

一个[ `StackLayout` ](xref:Xamarin.Forms.StackLayout)无法展开子视图中与自己的方向相反的方向。 因此，在垂直方向`StackLayout`，并设置[ `HorizontalOptions` ](xref:Xamarin.Forms.View.HorizontalOptions)属性上的子视图[ `StartAndExpand` ](xref:Xamarin.Forms.LayoutOptions.StartAndExpand)具有相同的效果与将属性设置为[`Start`](xref:Xamarin.Forms.LayoutOptions.Start).

> [!NOTE]
> 请注意，启用扩展不会更改视图的大小除非它使用[ `LayoutOptions.FillAndExpand` ](xref:Xamarin.Forms.LayoutOptions.FillAndExpand)。

下面的 XAML 代码示例演示了垂直方向[ `StackLayout` ](xref:Xamarin.Forms.StackLayout)其中每个子[ `Label` ](xref:Xamarin.Forms.Label)设置其[ `VerticalOptions` ](xref:Xamarin.Forms.View.VerticalOptions)属性中的 4 个扩展字段之一[ `LayoutOptions` ](xref:Xamarin.Forms.LayoutOptions)结构：

```xaml
<StackLayout Margin="0,20,0,0">
  ...
  <BoxView BackgroundColor="Red" HeightRequest="1" />
  <Label Text="Start" BackgroundColor="Gray" VerticalOptions="StartAndExpand" />
  <BoxView BackgroundColor="Red" HeightRequest="1" />
  <Label Text="Center" BackgroundColor="Gray" VerticalOptions="CenterAndExpand" />
  <BoxView BackgroundColor="Red" HeightRequest="1" />
  <Label Text="End" BackgroundColor="Gray" VerticalOptions="EndAndExpand" />
  <BoxView BackgroundColor="Red" HeightRequest="1" />
  <Label Text="Fill" BackgroundColor="Gray" VerticalOptions="FillAndExpand" />
  <BoxView BackgroundColor="Red" HeightRequest="1" />
</StackLayout>
```

等效的 C# 代码如下所示：

```csharp
Content = new StackLayout
{
  Margin = new Thickness(0, 20, 0, 0),
  Children = {
    ...
    new BoxView { BackgroundColor = Color.Red, HeightRequest = 1 },
    new Label { Text = "StartAndExpand", BackgroundColor = Color.Gray, VerticalOptions = LayoutOptions.StartAndExpand },
    new BoxView { BackgroundColor = Color.Red, HeightRequest = 1 },
    new Label { Text = "CenterAndExpand", BackgroundColor = Color.Gray, VerticalOptions = LayoutOptions.CenterAndExpand },
    new BoxView { BackgroundColor = Color.Red, HeightRequest = 1 },
    new Label { Text = "EndAndExpand", BackgroundColor = Color.Gray, VerticalOptions = LayoutOptions.EndAndExpand },
    new BoxView { BackgroundColor = Color.Red, HeightRequest = 1 },
    new Label { Text = "FillAndExpand", BackgroundColor = Color.Gray, VerticalOptions = LayoutOptions.FillAndExpand },
    new BoxView { BackgroundColor = Color.Red, HeightRequest = 1 }
  }
};
```

该代码会导致下面的屏幕截图中所示的布局：

[![](layout-options-images/expansion.png "扩展布局选项")](layout-options-images/expansion-large.png#lightbox "扩展布局选项")

每个[ `Label` ](xref:Xamarin.Forms.Label)占用相同数量的空间内[ `StackLayout` ](xref:Xamarin.Forms.StackLayout)。 但是，只有最终`Label`，集及其[ `VerticalOptions` ](xref:Xamarin.Forms.View.VerticalOptions)属性设置为[ `FillAndExpand` ](xref:Xamarin.Forms.LayoutOptions.FillAndExpand)具有不同的大小。 此外，每个`Label`分隔一个小的红色[ `BoxView` ](xref:Xamarin.Forms.BoxView)，这样空间`Label`占用轻松地查看。

## <a name="summary"></a>总结

本文介绍影响每个[ `LayoutOptions` ](xref:Xamarin.Forms.LayoutOptions)结构值对的对齐方式和视图，相对于其父级的扩展。 `Start`， `Center`， `End`，和`Fill`字段用于定义父布局中的视图的对齐方式和`StartAndExpand`， `CenterAndExpand`， `EndAndExpand`，和`FillAndExpand`字段用于定义对齐方式的首选项，并确定是否在视图将占用更多空间，是否在内可用[ `StackLayout` ](xref:Xamarin.Forms.StackLayout)。



## <a name="related-links"></a>相关链接

- [LayoutOptions （示例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/LayoutOptions/)
- [LayoutOptions](xref:Xamarin.Forms.LayoutOptions)
