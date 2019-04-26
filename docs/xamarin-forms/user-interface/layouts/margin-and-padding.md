---
title: 边距和填充
description: 元素呈现用户界面中时，边距和填充属性控制布局行为。 本文演示了两个属性，以及如何设置它们之间的差异。
ms.prod: xamarin
ms.assetid: BEB096BB-51DF-410F-B0F1-D235287B0F4A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/27/2016
ms.openlocfilehash: 595e673c59d23a45cbaf923a0d58faff2000c296
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61370926"
---
# <a name="margin-and-padding"></a>边距和填充

_元素呈现用户界面中时，边距和填充属性控制布局行为。本文演示了两个属性，以及如何设置它们之间的差异。_

## <a name="overview"></a>概述

边距和填充是相关的布局的概念：

- [ `Margin` ](xref:Xamarin.Forms.View.Margin)属性表示一个元素和其相邻元素之间的距离，用来控制元素的呈现位置和与其相邻元素的呈现位置。 `Margin` 可以在指定值[布局](~/xamarin-forms/user-interface/controls/layouts.md)并[视图](~/xamarin-forms/user-interface/controls/views.md)类。
- [ `Padding` ](xref:Xamarin.Forms.Layout.Padding)属性表示元素与其子元素之间的距离，用于将控件从其自己的内容。 `Padding` 可以在指定值[布局](~/xamarin-forms/user-interface/controls/layouts.md)类。

下图说明了这两个概念：

[![](margin-and-padding-images/margins-and-padding-sml.png "边距和填充概念")](margin-and-padding-images/margins-and-padding.png#lightbox "边距和填充概念")

请注意， [ `Margin` ](xref:Xamarin.Forms.View.Margin)值是累加性。 因此，如果两个相邻元素指定边距为 20 像素，则元素之间的距离将 40 像素。 此外，边距和填充是累加式的同时应用时，即边距和填充将是一个元素的任何内容之间的距离。

## <a name="specifying-a-thickness"></a>指定粗细

[ `Margin` ](xref:Xamarin.Forms.View.Margin)并[ `Padding` ](xref:Xamarin.Forms.Layout.Padding)类型的两个属性均[ `Thickness` ](xref:Xamarin.Forms.Thickness)。 有三种可能性创建时`Thickness`结构：

- 创建[ `Thickness` ](xref:Xamarin.Forms.Thickness)结构定义的单个统一的值。 单个值应用于左侧、 顶部、 右侧和元素的底部均。
- 创建[ `Thickness` ](xref:Xamarin.Forms.Thickness)水平和垂直值定义的结构。 水平值对称对称地应用于元素的上边和下边的垂直值应用于左侧和右侧的元素。
- 创建[ `Thickness` ](xref:Xamarin.Forms.Thickness)由四个非重复值应用于左侧、 顶部、 右侧和元素的底部均定义结构。

下面的 XAML 代码示例显示了所有三种可能性：

```xaml
<StackLayout Padding="0,20,0,0">
  <Label Text="Xamarin.Forms" Margin="20" />
  <Label Text="Xamarin.iOS" Margin="10, 15" />
  <Label Text="Xamarin.Android" Margin="0, 20, 15, 5" />
</StackLayout>
```

以下代码示例显示相应的 C# 代码：

```csharp
var stackLayout = new StackLayout {
  Padding = new Thickness(0,20,0,0),
  Children = {
    new Label { Text = "Xamarin.Forms", Margin = new Thickness (20) },
    new Label { Text = "Xamarin.iOS", Margin = new Thickness (10, 25) },
    new Label { Text = "Xamarin.Android", Margin = new Thickness (0, 20, 15, 5) }
  }
};
```

> [!NOTE]
> `Thickness` 值可以是负数，通常裁剪或拉伸过度内容。

## <a name="summary"></a>总结

本文说明了之间的差异[ `Margin` ](xref:Xamarin.Forms.View.Margin)并[ `Padding` ](xref:Xamarin.Forms.Layout.Padding)属性，以及如何将其设置。 元素呈现用户界面中时，属性控制布局行为。


## <a name="related-links"></a>相关链接

- [Margin](xref:Xamarin.Forms.View.Margin)
- [填充](xref:Xamarin.Forms.Layout.Padding)
- [粗细](xref:Xamarin.Forms.Thickness)
