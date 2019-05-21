---
title: Xamarin.Forms CollectionView 滚动
description: 时启动一个滚动用户扫，以便完全显示的项可以控制滚动的结束位置。 此外，CollectionView 定义两个 ScrollTo 方法，以编程方式滚动到视图的项。
ms.prod: xamarin
ms.assetid: 2ED719AF-33D2-434D-949A-B70B479C9BA5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/06/2019
ms.openlocfilehash: b2f32f6695fffa27068fce9d8c12f4ecd9157bc2
ms.sourcegitcommit: 482aef652bdaa440561252b6a1a1c0a40583cd32
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/21/2019
ms.locfileid: "65970542"
---
# <a name="xamarinforms-collectionview-scrolling"></a>Xamarin.Forms CollectionView 滚动

[![下载示例](~/media/shared/download.png)下载示例](https://github.com/xamarin/xamarin-forms-samples/tree/forms40/UserInterface/CollectionViewDemos/)

[`CollectionView`](xref:Xamarin.Forms.CollectionView) 定义了两个[ `ScrollTo` ](xref:Xamarin.Forms.ItemsView.ScrollTo*)项滚动到视图的方法。 而其他滚动到视图中指定的项的指定索引处的项的重载之一滚动到视图中中。 这两个重载有可以指定以完成滚动后, 指示项的准确位置的其他参数，以及是否要进行动画处理滚动。

[`CollectionView`](xref:Xamarin.Forms.CollectionView) 定义[ `ScrollToRequested` ](xref:Xamarin.Forms.ItemsView.ScrollToRequested)时引发的事件之一[ `ScrollTo` ](xref:Xamarin.Forms.ItemsView.ScrollTo*)调用的方法。 [ `ScrollToRequestedEventArgs` ](xref:Xamarin.Forms.ScrollToRequestedEventArgs)对象，它附带`ScrollToRequested`事件都有很多属性，包括`IsAnimated`， `Index`， `Item`，和`ScrollToPosition`。 从参数中指定设置这些属性`ScrollTo`方法调用。

时启动一个滚动用户扫，以便完全显示的项可以控制滚动的结束位置。 此功能称为对齐，因为项对齐来定位时滚动停止。 有关详细信息，请参阅[对齐点](#snap-points)。

## <a name="scroll-an-item-at-an-index-into-view"></a>滚动到视图的索引处的项

第一个[ `ScrollTo` ](xref:Xamarin.Forms.ItemsView.ScrollTo*)方法重载滚动到视图中位于指定索引处的项。 给定[ `CollectionView` ](xref:Xamarin.Forms.CollectionView)对象名为`collectionView`，下面的示例演示如何向下滚动到视图的索引 12 处的项：

```csharp
collectionView.ScrollTo(12);
```

## <a name="scroll-an-item-into-view"></a>滚动到视图的项

第二个[ `ScrollTo` ](xref:Xamarin.Forms.ItemsView.ScrollTo*)方法重载滚动到视图中指定的项。 给定[ `CollectionView` ](xref:Xamarin.Forms.CollectionView)对象名为`collectionView`，下面的示例演示如何将指定的项滚动到视图：

```csharp
MonkeysViewModel viewModel = BindingContext as MonkeysViewModel;
Monkey monkey = viewModel.Monkeys.FirstOrDefault(m => m.Name == "Proboscis Monkey");
collectionView.ScrollTo(monkey);
```

## <a name="control-scroll-position"></a>控制滚动位置

完成滚动后的项的准确位置时滚动到视图的项，可以指定与`position`的参数[ `ScrollTo` ](xref:Xamarin.Forms.ItemsView.ScrollTo*)方法。 此参数接受[ `ScrollToPosition` ](xref:Xamarin.Forms.ScrollToPosition)枚举成员。

### <a name="makevisible"></a>MakeVisible

[ `ScrollToPosition.MakeVisible` ](xref:Xamarin.Forms.ScrollToPosition)成员指示直到显示在视图中应该滚动项：

```csharp
collectionView.ScrollTo(monkey, position: ScrollToPosition.MakeVisible);
```

此示例代码会导致将项滚动到视图所需的最小滚动：

[![滚动到视图，在 iOS 和 Android 上的与项之间导航垂直列表的屏幕截图](scrolling-images/scrolltoposition-makevisible.png "CollectionView 垂直滚动项列表")](scrolling-images/scrolltoposition-makevisible-large.png#lightbox "CollectionView 垂直列表项滚动")

> [!NOTE]
> [ `ScrollToPosition.MakeVisible` ](xref:Xamarin.Forms.ScrollToPosition)成员默认情况下，如果使用`position`调用时未指定参数`ScrollTo`方法。

### <a name="start"></a>Start

[ `ScrollToPosition.Start` ](xref:Xamarin.Forms.ScrollToPosition)成员指示该项目，应该滚动到视图的开头：

```csharp
collectionView.ScrollTo(monkey, position: ScrollToPosition.Start);
```

此示例代码会导致滚动到视图的开头的项：

[![滚动到视图，在 iOS 和 Android 上的与项之间导航垂直列表的屏幕截图](scrolling-images/scrolltoposition-start.png "CollectionView 垂直滚动项列表")](scrolling-images/scrolltoposition-start-large.png#lightbox "CollectionView 垂直列表项滚动")

### <a name="center"></a>居中

[ `ScrollToPosition.Center` ](xref:Xamarin.Forms.ScrollToPosition)成员指示项是否应该滚动到视图的中心：

```csharp
collectionView.ScrollTo(monkey, position: ScrollToPosition.Center);
```

此示例代码会导致滚动到视图的中心的项：

[![滚动到视图，在 iOS 和 Android 上的与项之间导航垂直列表的屏幕截图](scrolling-images/scrolltoposition-center.png "CollectionView 垂直滚动项列表")](scrolling-images/scrolltoposition-center-large.png#lightbox "CollectionView 垂直列表项滚动")

### <a name="end"></a>End — 结束

[ `ScrollToPosition.End` ](xref:Xamarin.Forms.ScrollToPosition)成员指示该项，应该滚动到视图的末尾：

```csharp
collectionView.ScrollTo(monkey, position: ScrollToPosition.End);
```

此示例代码会导致滚动到视图的末尾的项：

[![滚动到视图，在 iOS 和 Android 上的与项之间导航垂直列表的屏幕截图](scrolling-images/scrolltoposition-end.png "CollectionView 垂直滚动项列表")](scrolling-images/scrolltoposition-end-large.png#lightbox "CollectionView 垂直列表项滚动")

## <a name="disable-scroll-animation"></a>禁用滚动动画

滚动到视图的某个项时显示滚动动画。 但是，可以通过设置来禁用此动画`animate`的参数`ScrollTo`方法`false`:

```csharp
collectionView.ScrollTo(monkey, animate: false);
```

## <a name="snap-points"></a>对齐点

时启动一个滚动用户扫，以便完全显示的项可以控制滚动的结束位置。 此功能称为对齐，因为项对齐来定位时滚动停止，并受以下属性从[ `ItemsLayout` ](xref:Xamarin.Forms.ItemsLayout)类：

- [`SnapPointsType`](xref:Xamarin.Forms.ItemsLayout.SnapPointsType)类型的[ `SnapPointsType` ](xref:Xamarin.Forms.SnapPointsType)，滚动时显示指定的管理点的行为。
- [`SnapPointsAlignment`](xref:Xamarin.Forms.ItemsLayout.SnapPointsAlignment)类型的[ `SnapPointsAlignment` ](xref:Xamarin.Forms.SnapPointsAlignment)，指定管理单元点包含项的对齐方式。

这些属性受到[ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty)对象，这意味着，属性可以是数据绑定的目标。

> [!NOTE]
> 对齐发生时，将出现此问题的方向产生最少量的动作。

### <a name="snap-points-type"></a>对齐点类型

[ `SnapPointsType` ](xref:Xamarin.Forms.SnapPointsType)枚举定义以下成员：

- `None` 指示，滚动不会进行捕捉到的项。
- `Mandatory` 指示该内容始终与最接近的管理单元对齐指向其中滚动可以自然地终止，延时的方向。
- `MandatorySingle` 指示与相同的行为`Mandatory`，但仅一次滚动一个项。

默认情况下[ `SnapPointsType` ](xref:Xamarin.Forms.ItemsLayout.SnapPointsType)属性设置为`SnapPointsType.None`，这可确保该滚动不会按项，如以下屏幕截图中所示：

[![没有管理点，请在 iOS 和 Android 的 CollectionView 垂直列表的屏幕截图](scrolling-images/snappoints-none.png "之间导航而无需管理点的垂直列表")](scrolling-images/snappoints-none-large.png#lightbox "CollectionView 垂直列表，而无需管理单元点")

### <a name="snap-points-alignment"></a>对齐点对齐方式

[ `SnapPointsAlignment` ](xref:Xamarin.Forms.SnapPointsAlignment)枚举定义`Start`， `Center`，并`End`成员。

> [!IMPORTANT]
> 值[ `SnapPointsAlignment` ](xref:Xamarin.Forms.ItemsLayout.SnapPointsAlignment)属性才是考虑何时[ `SnapPointsType` ](xref:Xamarin.Forms.ItemsLayout.SnapPointsType)属性设置为`Mandatory`，或`MandatorySingle`。

#### <a name="start"></a>Start

`SnapPointsAlignment.Start`成员指示吸附点与项的前边缘对齐。

默认情况下[ `SnapPointsAlignment` ](xref:Xamarin.Forms.ItemsLayout.SnapPointsAlignment)属性设置为`SnapPointsAlignment.Start`。 但是，为了保持完整性，下面的 XAML 示例演示如何设置此枚举成员：

```xaml
<CollectionView x:Name="collectionView"
                ItemsSource="{Binding Monkeys}">
    <CollectionView.ItemsLayout>
        <ListItemsLayout SnapPointsType="MandatorySingle"
                         SnapPointsAlignment="Start">
            <x:Arguments>
                <ItemsLayoutOrientation>Vertical</ItemsLayoutOrientation>
            </x:Arguments>
        </ListItemsLayout>
    </CollectionView.ItemsLayout>
    <CollectionView.ItemTemplate>
        <DataTemplate>
            ...
        </DataTemplate>
    </CollectionView.ItemTemplate>
</CollectionView>
```

等效 C# 代码如下：

```csharp
CollectionView collectionView = new CollectionView
{
    ItemsLayout = new ListItemsLayout(ItemsLayoutOrientation.Vertical)
    {
        SnapPointsType = SnapPointsType.MandatorySingle,
        SnapPointsAlignment = SnapPointsAlignment.Start
    },
    ItemTemplate = new DataTemplate(() =>
    {
        return null;
    })
};
```

当启动滚动用户扫时，将与视图的顶部对齐顶部的项：

[![使用 iOS 和 Android 上开始管理单元点 CollectionView 垂直列表的屏幕截图](scrolling-images/snappoints-start.png "CollectionView 垂直列表开始吸附点")](scrolling-images/snappoints-start-large.png#lightbox "CollectionView 垂直列表开始使用对齐点")

#### <a name="center"></a>居中

`SnapPointsAlignment.Center`成员指示吸附点与项目的中心对齐。 下面的 XAML 示例演示如何设置此枚举成员：

```xaml
<CollectionView x:Name="collectionView"
                ItemsSource="{Binding Monkeys}">
    <CollectionView.ItemsLayout>
        <ListItemsLayout SnapPointsType="MandatorySingle"
                         SnapPointsAlignment="Center">
            <x:Arguments>
                <ItemsLayoutOrientation>Vertical</ItemsLayoutOrientation>
            </x:Arguments>
        </ListItemsLayout>
    </CollectionView.ItemsLayout>
    <CollectionView.ItemTemplate>
        <DataTemplate>
            ...
        </DataTemplate>
    </CollectionView.ItemTemplate>
</CollectionView>
```

等效 C# 代码如下：

```csharp
CollectionView collectionView = new CollectionView
{
    ItemsLayout = new ListItemsLayout(ItemsLayoutOrientation.Vertical)
    {
        SnapPointsType = SnapPointsType.MandatorySingle,
        SnapPointsAlignment = SnapPointsAlignment.Center
    },
    ItemTemplate = new DataTemplate(() =>
    {
        return null;
    })
};
```

时启动一个滚动用户扫，顶部的项将是居中对齐视图的顶部：

[![中心管理点，请在 iOS 和 Android 的 CollectionView 垂直列表的屏幕截图](scrolling-images/snappoints-center.png "CollectionView 与管理单元的中心点的垂直列表")](scrolling-images/snappoints-center-large.png#lightbox "CollectionView 垂直列表管理单元的中心点")

#### <a name="end"></a>End — 结束

`SnapPointsAlignment.End`成员指示吸附点与项的后边缘对齐。 下面的 XAML 示例演示如何设置此枚举成员：

```xaml
<CollectionView x:Name="collectionView"
                ItemsSource="{Binding Monkeys}">
    <CollectionView.ItemsLayout>
        <ListItemsLayout SnapPointsType="MandatorySingle"
                         SnapPointsAlignment="End">
            <x:Arguments>
                <ItemsLayoutOrientation>Vertical</ItemsLayoutOrientation>
            </x:Arguments>
        </ListItemsLayout>
    </CollectionView.ItemsLayout>
    <CollectionView.ItemTemplate>
        <DataTemplate>
            ...
        </DataTemplate>
    </CollectionView.ItemTemplate>
</CollectionView>
```

等效 C# 代码如下：

```csharp
CollectionView collectionView = new CollectionView
{
    ItemsLayout = new ListItemsLayout(ItemsLayoutOrientation.Vertical)
    {
        SnapPointsType = SnapPointsType.MandatorySingle,
        SnapPointsAlignment = SnapPointsAlignment.End
    },
    ItemTemplate = new DataTemplate(() =>
    {
        return null;
    })
};
```

当启动滚动用户扫时，将与视图的底部对齐项的底部：

[![结束管理单元点，请在 iOS 和 Android 的 CollectionView 垂直列表的屏幕截图](scrolling-images/snappoints-end.png "管理单元的终结点之间导航垂直列表")](scrolling-images/snappoints-end-large.png#lightbox "CollectionView 垂直列表与结束管理单元点")

## <a name="related-links"></a>相关链接

- [CollectionView （示例）](https://github.com/xamarin/xamarin-forms-samples/tree/forms40/UserInterface/CollectionViewDemos/)
