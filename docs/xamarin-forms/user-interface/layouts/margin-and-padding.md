---
title: 边距和填充
description: 在用户界面中呈现元素时，边距和填充属性控制布局的行为。 本文说明了这两个属性之间的差异，以及如何设置它们。
ms.prod: xamarin
ms.assetid: BEB096BB-51DF-410F-B0F1-D235287B0F4A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/27/2016
ms.openlocfilehash: 66ac81631466131cf1ef44dde39aa768d31b65a1
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70772493"
---
# <a name="margin-and-padding"></a>边距和填充

_在用户界面中呈现元素时，边距和填充属性控制布局的行为。本文说明了这两个属性之间的差异，以及如何设置它们。_

## <a name="overview"></a>概述

边距和填充是相关的布局概念：

- [`Margin`](xref:Xamarin.Forms.View.Margin)属性表示元素与其相邻元素之间的距离，用于控制元素的呈现位置以及其相邻元素的呈现位置。 `Margin`可以在[布局](~/xamarin-forms/user-interface/controls/layouts.md)和[视图](~/xamarin-forms/user-interface/controls/views.md)类上指定值。
- [`Padding`](xref:Xamarin.Forms.Layout.Padding)属性表示元素及其子元素之间的距离，并用于将控件从它自己的内容中分离出来。 `Padding`可以在[布局](~/xamarin-forms/user-interface/controls/layouts.md)类上指定值。

下图说明了这两个概念：

边距和填充概念[ ![(margin-and-padding-images/margins-and-padding-sml.png " ")]](margin-and-padding-images/margins-and-padding.png#lightbox "边距和填充概念")

请注意[`Margin`](xref:Xamarin.Forms.View.Margin) ，值是累加的。 因此，如果两个相邻元素指定的边距为20个像素，则这些元素之间的距离将为40像素。 此外，边距和填充在应用两者时是累加的，因为在元素和任何内容之间的距离将为边距加上填充。

## <a name="specifying-a-thickness"></a>指定粗细

和属性均为类型[`Thickness`。](xref:Xamarin.Forms.Thickness) [`Margin`](xref:Xamarin.Forms.View.Margin) [`Padding`](xref:Xamarin.Forms.Layout.Padding) 创建`Thickness`结构时，有三种可能性：

- 创建由单个统一值定义的[结构。`Thickness`](xref:Xamarin.Forms.Thickness) 单个值将应用于元素的左侧、顶部、右侧和底部。
- 创建由水平和垂直值定义的[结构。`Thickness`](xref:Xamarin.Forms.Thickness) 水平值被对称应用到元素的左侧和右侧，垂直值被对称应用到元素的顶部和底部。
- 创建由四个非重复值定义的[结构，该结构应用于元素的左侧、顶部、右侧和底部。`Thickness`](xref:Xamarin.Forms.Thickness)

下面的 XAML 代码示例显示了三种可能性：

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
> `Thickness`值可以为负数，这通常会剪辑或 overdraws 内容。

## <a name="summary"></a>总结

本文说明了[`Margin`](xref:Xamarin.Forms.View.Margin)和[`Padding`](xref:Xamarin.Forms.Layout.Padding)属性之间的差异，以及如何设置它们。 元素在用户界面中呈现时的属性控件布局行为。

## <a name="related-links"></a>相关链接

- [边距](xref:Xamarin.Forms.View.Margin)
- [填充](xref:Xamarin.Forms.Layout.Padding)
- [粗细](xref:Xamarin.Forms.Thickness)
