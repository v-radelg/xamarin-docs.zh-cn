---
title: Xamarin. Forms ScrollView
description: 本文介绍如何使用 Xamarin ScrollView 类提供无法在一个屏幕上容纳的布局，并使其具有内容为键盘提供空间。
ms.prod: xamarin
ms.assetid: 7B542872-B3D1-49B3-B15E-0E98F53C1F6E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/17/2019
ms.openlocfilehash: 8d523c6da6ca7feaf6894123822f789f37455865
ms.sourcegitcommit: 21d8be9571a2fa89fb7d8ff0787ff4f957de0985
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/21/2019
ms.locfileid: "72696855"
---
# <a name="xamarinforms-scrollview"></a>Xamarin. Forms ScrollView

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout)

[`ScrollView`](xref:Xamarin.Forms.ScrollView)包含布局，并使其能够在屏幕外滚动。 `ScrollView` 还用于在显示键盘时允许视图自动移动到屏幕的可见部分。

[![](scroll-view-images/layouts-sml.png "Xamarin.Forms Layouts")](scroll-view-images/layouts.png#lightbox "Xamarin.Forms Layouts")

## <a name="purpose"></a>目标

[`ScrollView`](xref:Xamarin.Forms.ScrollView)可用于确保更大的视图在较小的手机上很好地显示。 例如，可以在 iPhone 4s 上剪裁适用于 iPhone 6s 的布局。 使用 `ScrollView` 会允许在较小的屏幕上显示布局的已剪裁部分。

## <a name="usage"></a>用法

> [!NOTE]
> [`ScrollView`](xref:Xamarin.Forms.ScrollView)对象不应嵌套。 此外，`ScrollView`s 不应与提供滚动的其他控件嵌套，如 `ListView` 和 `WebView`。

[`ScrollView`](xref:Xamarin.Forms.ScrollView)公开 `Content` 属性，该属性可以设置为单个视图或布局。 请考虑此布局示例，其中包含一个非常大的 boxView，后跟一个 `Entry`：

```xaml
<ContentPage.Content>
    <ScrollView>
        <StackLayout>
            <BoxView BackgroundColor="Red" HeightRequest="600" WidthRequest="150" />
            <Entry />
        </StackLayout>
    </ScrollView>
</ContentPage.Content>
```

在 C# 中：

```csharp
var scroll = new ScrollView();
Content = scroll;
var stack = new StackLayout();
stack.Children.Add(new BoxView { BackgroundColor = Color.Red,    HeightRequest = 600, WidthRequest = 600 });
stack.Children.Add(new Entry());
```

用户向下滚动之前，只显示 `BoxView`：

![](scroll-view-images/scroll-start.png "BoxView in ScrollView")

请注意，当用户开始在 `Entry` 中输入文本时，视图将滚动以使其在屏幕上可见：

![](scroll-view-images/scroll-end.png "Entry in ScrollView")

## <a name="properties"></a>属性

[`ScrollView`](xref:Xamarin.Forms.ScrollView) 定义以下属性:

- [`ContentSize`](xref:Xamarin.Forms.ScrollView.ContentSizeProperty)获取表示内容大小的[`Size`](xref:Xamarin.Forms.Size)值。
- [`Orientation`](xref:Xamarin.Forms.ScrollView.OrientationProperty)获取或设置表示 `ScrollView` 滚动方向的[`ScrollOrientation`](xref:Xamarin.Forms.ScrollOrientation)枚举值。
- [`ScrollX`](xref:Xamarin.Forms.ScrollView.ScrollXProperty)获取表示当前 X 滚动位置的 `double`。
- [`ScrollY`](xref:Xamarin.Forms.ScrollView.ScrollYProperty)获取表示当前 Y 滚动位置的 `double`。
- [`HorizontalScrollBarVisibility`](xref:Xamarin.Forms.ScrollView.HorizontalScrollBarVisibilityProperty)获取或设置一个[`ScrollBarVisibility`](xref:Xamarin.Forms.ScrollBarVisibility)值，该值表示水平滚动条可见的时间。
- [`VerticalScrollBarVisibility`](xref:Xamarin.Forms.ScrollView.VerticalScrollBarVisibilityProperty)获取或设置一个[`ScrollBarVisibility`](xref:Xamarin.Forms.ScrollBarVisibility)值，该值表示垂直滚动条可见的时间。

> [!NOTE]
> 可以通过将[`Orientation`](xref:Xamarin.Forms.ScrollView.OrientationProperty)属性设置为 `Neither` 来禁用滚动。

## <a name="methods"></a>方法

[`ScrollView`](xref:Xamarin.Forms.ScrollView)提供了一种 `ScrollToAsync` 方法，该方法可用于使用坐标或指定应使其可见的特定视图滚动视图。

使用坐标时，指定 `x` 和 `y` 坐标，同时指定滚动是否应动画显示：

```csharp
scroll.ScrollToAsync(0, 150, true); //scrolls so that the position at 150px from the top is visible

scroll.ScrollToAsync(label, ScrollToPosition.Start, true); //scrolls so that the label is at the start of the list
```

> [!IMPORTANT]
> 当[`ScrollView.Orientation`](xref:Xamarin.Forms.ScrollView.OrientationProperty)属性设置为 `Neither` 时，`ScrollToAsync` 方法不会导致滚动。

滚动到特定元素时，`ScrollToPosition` 枚举指定在视图中显示元素的位置：

- **居中**&ndash; 将元素滚动到视图可见部分的中心。
- **End** &ndash; 将元素滚动到视图可见部分的结尾。
- **MakeVisible** &ndash; 滚动元素，使其在视图中可见。
- **开始**&ndash; 将元素滚动到视图可见部分的开头。

@No__t_0 属性指定如何滚动视图。 如果设置为 "`true`"，则将使用平滑动画，而不是立即将内容移动到视图中。

## <a name="events"></a>事件

[`ScrollView`](xref:Xamarin.Forms.ScrollView)仅定义了一个事件，`Scrolled`。 当视图滚动完成时，将引发 `Scrolled`。 @No__t_0 的事件处理程序采用 `ScrolledEventArgs`，后者具有 `ScrollX` 和 `ScrollY` 属性。 下面演示了如何使用 `ScrollView` 的当前滚动位置更新标签：

```csharp
Label label = new Label { Text = "Position: " };
ScrollView scroll = new ScrollView();
scroll.Scrolled += (object sender, ScrolledEventArgs e) => {
    label.Text = "Position: " + e.ScrollX + " x " + e.ScrollY;
};
```

请注意，滚动位置可能为负值，因为滚动到列表末尾时出现弹跳效果。

## <a name="related-links"></a>相关链接

- [布局（示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout)
- [BusinessTumble 示例（示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-businesstumble)
