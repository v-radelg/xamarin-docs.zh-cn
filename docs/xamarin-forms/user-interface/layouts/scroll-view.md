---
title: Xamarin.Forms ScrollView
description: 本文介绍如何使用 Xamarin.Forms ScrollView 类存在，无法容纳在一个屏幕上，并且具有内容留出空间适用于键盘的布局。
ms.prod: xamarin
ms.assetid: 7B542872-B3D1-49B3-B15E-0E98F53C1F6E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/18/2019
ms.openlocfilehash: bb10cda7c9899f176861ceee712cc876984c56ef
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75488265"
---
# <a name="xamarinforms-scrollview"></a>Xamarin.Forms ScrollView

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout)

[`ScrollView`](xref:Xamarin.Forms.ScrollView) 包含布局，使他们能够滚动屏幕外。 `ScrollView` 此外用于允许视图以显示键盘时自动移至屏幕的可见部分。

[![](scroll-view-images/layouts-sml.png "Xamarin.Forms Layouts")](scroll-view-images/layouts.png#lightbox "Xamarin.Forms Layouts")

## <a name="purpose"></a>目标

[`ScrollView`](xref:Xamarin.Forms.ScrollView)可用于确保更大的视图在较小的手机上很好地显示。 例如，适用于 iPhone 6s 的布局可能会剪裁 4 秒在 iPhone 上。 使用`ScrollView`将允许的较小屏幕上显示的布局裁剪后的部分。

## <a name="usage"></a>用量

> [!NOTE]
> [`ScrollView`](xref:Xamarin.Forms.ScrollView)对象不应嵌套。 此外，`ScrollView`不应与其他控件提供滚动功能，如嵌套 s`ListView`和`WebView`。

[`ScrollView`](xref:Xamarin.Forms.ScrollView)公开 `Content` 属性，该属性可以设置为单个视图或布局。 请考虑以下示例使用非常大的字数后, 跟布局`Entry`:

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
public class ScrollingDemoCode : ContentPage
{
    public ScrollingDemoCode()
    {
        StackLayout stackLayout = new StackLayout();
        stackLayout.Children.Add(new BoxView { BackgroundColor = Color.Red, HeightRequest = 600, WidthRequest = 150 });
        stackLayout.Children.Add(new Entry());
        ScrollView scrollView = new ScrollView();
        scrollView.Content = stackLayout;
        Content = scrollView;
    }
}
```

用户向下滚动，唯一之前`BoxView`可见：

![](scroll-view-images/scroll-start.png "BoxView in ScrollView")

请注意，当用户开始输入文本中的`Entry`，视图将滚动以使其在屏幕上可见：

![](scroll-view-images/scroll-end.png "Entry in ScrollView")

## <a name="properties"></a>属性

[`ScrollView`](xref:Xamarin.Forms.ScrollView) 定义以下属性:

- [`ContentSize`](xref:Xamarin.Forms.ScrollView.ContentSizeProperty) 获取[ `Size` ](xref:Xamarin.Forms.Size)值，该值表示内容的大小。
- [`Orientation`](xref:Xamarin.Forms.ScrollView.OrientationProperty) 获取或设置[ `ScrollOrientation` ](xref:Xamarin.Forms.ScrollOrientation)枚举值，该值表示滚动的方向`ScrollView`。
- [`ScrollX`](xref:Xamarin.Forms.ScrollView.ScrollXProperty) 获取`double`，它表示当前的滚动位置。
- [`ScrollY`](xref:Xamarin.Forms.ScrollView.ScrollYProperty) 获取`double`，表示当前 Y 滚动位置。
- [`HorizontalScrollBarVisibility`](xref:Xamarin.Forms.ScrollView.HorizontalScrollBarVisibilityProperty) 获取或设置[ `ScrollBarVisibility` ](xref:Xamarin.Forms.ScrollBarVisibility)值，该值表示当水平滚动条是否可见。
- [`VerticalScrollBarVisibility`](xref:Xamarin.Forms.ScrollView.VerticalScrollBarVisibilityProperty) 获取或设置[ `ScrollBarVisibility` ](xref:Xamarin.Forms.ScrollBarVisibility)值，该值表示当垂直滚动条是否可见。

> [!NOTE]
> 可以通过将[`Orientation`](xref:Xamarin.Forms.ScrollView.OrientationProperty)属性设置为 `Neither`来禁用滚动。

## <a name="methods"></a>方法

[`ScrollView`](xref:Xamarin.Forms.ScrollView)提供了一种 `ScrollToAsync` 方法，该方法可用于使用坐标或指定应使其可见的特定视图滚动视图。

当使用坐标，指定`x`和`y`坐标，以及一个布尔值，该值指示是否滚动应进行动画处理：

```csharp
scroll.ScrollToAsync(0, 150, true); //scrolls so that the position at 150px from the top is visible

scroll.ScrollToAsync(label, ScrollToPosition.Start, true); //scrolls so that the label is at the start of the list
```

> [!IMPORTANT]
> 当[`ScrollView.Orientation`](xref:Xamarin.Forms.ScrollView.OrientationProperty)属性设置为 `Neither`时，`ScrollToAsync` 方法不会导致滚动。

滚动到特定元素时，`ScrollToPosition` 枚举指定在视图中显示元素的位置：

- **Center** &ndash;滚动到视图的可见部分的中心元素。
- **结束**&ndash;滚动到视图的可见部分末尾的元素。
- **MakeVisible** &ndash;滚动元素，以便在视图中可见。
- **启动**&ndash;滚动到视图的可见部分的起始位置的元素。

`IsAnimated`属性指定如何将滚动视图。 如果设置为 "`true`"，则将使用平滑动画，而不是立即将内容移动到视图中。

## <a name="events"></a>Events

[`ScrollView`](xref:Xamarin.Forms.ScrollView)仅定义了一个事件，`Scrolled`。 `Scrolled` 查看已完成滚动时引发。 事件处理程序`Scrolled`采用`ScrolledEventArgs`，其中包含`ScrollX`和`ScrollY`属性。 以下演示如何使用当前的滚动位置的更新标签`ScrollView`:

```csharp
Label label = new Label { Text = "Position: " };
ScrollView scroll = new ScrollView();
scroll.Scrolled += (object sender, ScrolledEventArgs e) => {
    label.Text = "Position: " + e.ScrollX + " x " + e.ScrollY;
};
```

请注意，滚动位置可以是负数，由于滚动列表的末尾时的弹跳效果。

## <a name="related-links"></a>相关链接

- [布局 （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout)
- [BusinessTumble 示例 （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-businesstumble)
