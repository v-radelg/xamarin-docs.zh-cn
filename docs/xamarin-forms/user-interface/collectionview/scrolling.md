---
title: Xamarin CollectionView 滚动
description: 当用户 swipes 启动滚动时，可以控制滚动的结束位置，以便完全显示项。 此外，CollectionView 还定义了两个 ScrollTo 方法，这些方法以编程方式将项滚动到视图中。
ms.prod: xamarin
ms.assetid: 2ED719AF-33D2-434D-949A-B70B479C9BA5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/17/2019
ms.openlocfilehash: 7aef14cbb854d89a2088a450353b943402f76a86
ms.sourcegitcommit: 21d8be9571a2fa89fb7d8ff0787ff4f957de0985
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/21/2019
ms.locfileid: "72697223"
---
# <a name="xamarinforms-collectionview-scrolling"></a>Xamarin CollectionView 滚动

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)

[`CollectionView`](xref:Xamarin.Forms.CollectionView)定义了两个[`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*)方法，这些方法可将项滚动到视图中。 其中一个重载将指定索引处的项滚动到视图中，而另一个重载将指定项滚动到视图中。 两个重载都有其他参数，可以指定这些参数来指示项所属的组、项在滚动完成后的确切位置以及是否对滚动进行动画处理。

[`CollectionView`](xref:Xamarin.Forms.CollectionView)定义一个[`ScrollToRequested`](xref:Xamarin.Forms.ItemsView.ScrollToRequested)事件，当调用其中一个[`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*)方法时，将触发该事件。 @No__t_2 事件附带的[`ScrollToRequestedEventArgs`](xref:Xamarin.Forms.ScrollToRequestedEventArgs)对象具有多个属性，包括 `IsAnimated`、`Index`、`Item` 和 `ScrollToPosition`。 这些属性是从 `ScrollTo` 方法调用中指定的参数设置的。

此外， [`CollectionView`](xref:Xamarin.Forms.CollectionView)会定义一个 `Scrolled` 事件，该事件将激发以指示发生滚动。 @No__t_1 事件附带的 `ItemsViewScrolledEventArgs` 对象具有多个属性。 有关详细信息，请参阅[检测滚动](#detect-scrolling)。

[`CollectionView`](xref:Xamarin.Forms.CollectionView)还定义了一个 `ItemsUpdatingScrollMode` 属性，该属性表示在添加新项时 `CollectionView` 的滚动行为。 有关此属性的详细信息，请参阅[在添加新项时控制滚动位置](#control-scroll-position-when-new-items-are-added)。

当用户 swipes 启动滚动时，可以控制滚动的结束位置，以便完全显示项。 此功能称为 "对齐"，因为当滚动停止时，项会对齐到位置。 有关详细信息，请参阅[对齐点](#snap-points)。

[`CollectionView`](xref:Xamarin.Forms.CollectionView)还可以在用户滚动时以增量方式加载数据。 有关详细信息，请参阅[以增量方式加载数据](populate-data.md#load-data-incrementally)。

## <a name="detect-scrolling"></a>检测滚动

[`CollectionView`](xref:Xamarin.Forms.CollectionView)定义一个 `Scrolled` 事件，该事件将激发以指示发生滚动。 下面的 XAML 示例显示了一个 `CollectionView`，该为 `Scrolled` 事件设置事件处理程序：

```xaml
<CollectionView Scrolled="OnCollectionViewScrolled">
    ...
</CollectionView>
```

等效 C# 代码如下：

```csharp
CollectionView collectionView = new CollectionView();
collectionView.Scrolled += OnCollectionViewScrolled;
```

在此代码示例中，当引发 `Scrolled` 事件时，将执行 `OnCollectionViewScrolled` 事件处理程序：

```csharp
void OnCollectionViewScrolled(object sender, ItemsViewScrolledEventArgs e)
{
    Debug.WriteLine("HorizontalDelta: " + e.HorizontalDelta);
    Debug.WriteLine("VerticalDelta: " + e.VerticalDelta);
    Debug.WriteLine("HorizontalOffset: " + e.HorizontalOffset);
    Debug.WriteLine("VerticalOffset: " + e.VerticalOffset);
    Debug.WriteLine("FirstVisibleItemIndex: " + e.FirstVisibleItemIndex);
    Debug.WriteLine("CenterItemIndex: " + e.CenterItemIndex);
    Debug.WriteLine("LastVisibleItemIndex: " + e.LastVisibleItemIndex);
}
```

在此示例中，`OnCollectionViewScrolled` 事件处理程序输出事件随附的 `ItemsViewScrolledEventArgs` 对象的值。

> [!IMPORTANT]
> 对于用户启动的滚动以及以编程方式滚动，将触发 `Scrolled` 事件。

## <a name="scroll-an-item-at-an-index-into-view"></a>将索引中的项滚动到视图中

第一个[`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*)方法重载将指定索引处的项滚动到视图中。 给定一个名为 `collectionView` 的[`CollectionView`](xref:Xamarin.Forms.CollectionView)对象，下面的示例演示如何将位于索引12的项滚动到视图中：

```csharp
collectionView.ScrollTo(12);
```

或者，可以通过指定项和组索引将分组数据中的项滚动到视图中。 下面的示例演示如何将第二个组中的第三项滚动到视图中：

```csharp
// Items and groups are indexed from zero.
collectionView.ScrollTo(2, 1);
```

> [!NOTE]
> 调用[`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*)方法时，将激发[`ScrollToRequested`](xref:Xamarin.Forms.ItemsView.ScrollToRequested)事件。

## <a name="scroll-an-item-into-view"></a>将项滚动到视图

第二个[`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*)方法重载将指定项滚动到视图中。 给定一个名为 `collectionView` 的[`CollectionView`](xref:Xamarin.Forms.CollectionView)对象，下面的示例演示如何将 Proboscis 的猴子项滚动到视图中：

```csharp
MonkeysViewModel viewModel = BindingContext as MonkeysViewModel;
Monkey monkey = viewModel.Monkeys.FirstOrDefault(m => m.Name == "Proboscis Monkey");
collectionView.ScrollTo(monkey);
```

或者，可以通过指定项和组将分组数据中的项滚动到视图中。 下面的示例演示如何将猴组中的 Proboscis 猴子项滚动到视图中：

```csharp
GroupedAnimalsViewModel viewModel = BindingContext as GroupedAnimalsViewModel;
AnimalGroup group = viewModel.Animals.FirstOrDefault(a => a.Name == "Monkeys");
Animal monkey = group.FirstOrDefault(m => m.Name == "Proboscis Monkey");
collectionView.ScrollTo(monkey, group);
```

> [!NOTE]
> 调用[`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*)方法时，将激发[`ScrollToRequested`](xref:Xamarin.Forms.ItemsView.ScrollToRequested)事件。

## <a name="disable-scroll-animation"></a>禁用滚动动画

将项滚动到视图中时，将显示滚动动画。 但是，通过将 `ScrollTo` 方法的 `animate` 参数设置为 `false`，可以禁用此动画：

```csharp
collectionView.ScrollTo(monkey, animate: false);
```

## <a name="control-scroll-position"></a>控制滚动位置

在将某个项滚动到视图中时，可以使用[`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*)方法的 `position` 参数指定该项在滚动完成后的确切位置。 此参数接受[`ScrollToPosition`](xref:Xamarin.Forms.ScrollToPosition)枚举成员。

### <a name="makevisible"></a>MakeVisible

[@No__t_1](xref:Xamarin.Forms.ScrollToPosition)成员指示应滚动项，直到它在视图中可见：

```csharp
collectionView.ScrollTo(monkey, position: ScrollToPosition.MakeVisible);
```

此示例代码将导致滚动项进入查看所需的最少滚动：

[![在 iOS 和 Android 上将项滚动到视图中的 CollectionView 垂直列表屏幕截图](scrolling-images/scrolltoposition-makevisible.png "具有滚动项的 CollectionView 垂直列表")](scrolling-images/scrolltoposition-makevisible-large.png#lightbox "具有滚动项的 CollectionView 垂直列表")

> [!NOTE]
> 默认情况下，如果在调用 `ScrollTo` 方法时未指定 `position` 参数，则默认情况下使用[`ScrollToPosition.MakeVisible`](xref:Xamarin.Forms.ScrollToPosition)成员。

### <a name="start"></a>Start

[@No__t_1](xref:Xamarin.Forms.ScrollToPosition)成员指示该项应滚动到视图的开头：

```csharp
collectionView.ScrollTo(monkey, position: ScrollToPosition.Start);
```

此示例代码会将项滚动到视图的开头：

[![在 iOS 和 Android 上将项滚动到视图中的 CollectionView 垂直列表屏幕截图](scrolling-images/scrolltoposition-start.png "具有滚动项的 CollectionView 垂直列表")](scrolling-images/scrolltoposition-start-large.png#lightbox "具有滚动项的 CollectionView 垂直列表")

### <a name="center"></a>居中

[@No__t_1](xref:Xamarin.Forms.ScrollToPosition)成员指示该项应滚动到视图的中心：

```csharp
collectionView.ScrollTo(monkey, position: ScrollToPosition.Center);
```

此示例代码会将项滚动到视图的中心：

[![在 iOS 和 Android 上将项滚动到视图中的 CollectionView 垂直列表屏幕截图](scrolling-images/scrolltoposition-center.png "具有滚动项的 CollectionView 垂直列表")](scrolling-images/scrolltoposition-center-large.png#lightbox "具有滚动项的 CollectionView 垂直列表")

### <a name="end"></a>End — 结束

[@No__t_1](xref:Xamarin.Forms.ScrollToPosition)成员指示该项应滚动到视图的末尾：

```csharp
collectionView.ScrollTo(monkey, position: ScrollToPosition.End);
```

此示例代码会将项滚动到视图的末尾：

[![在 iOS 和 Android 上将项滚动到视图中的 CollectionView 垂直列表屏幕截图](scrolling-images/scrolltoposition-end.png "具有滚动项的 CollectionView 垂直列表")](scrolling-images/scrolltoposition-end-large.png#lightbox "具有滚动项的 CollectionView 垂直列表")

## <a name="control-scroll-position-when-new-items-are-added"></a>在添加新项时控制滚动位置

[`CollectionView`](xref:Xamarin.Forms.CollectionView)定义 `ItemsUpdatingScrollMode` 属性，该属性由可绑定的属性支持。 此属性获取或设置一个 `ItemsUpdatingScrollMode` 枚举值，该值表示在添加新项时 `CollectionView` 的滚动行为。 `ItemsUpdatingScrollMode` 枚举定义下列成员：

- `KeepItemsInView` 调整滚动偏移量，以便在添加新项时保持显示第一个可见项。
- `KeepScrollOffset` 在添加新项时，使滚动偏移量相对于列表的开头保持。
- `KeepLastItemInView` 调整滚动偏移量，以便在添加新项时使最后一项可见。

@No__t_0 属性的默认值为 `KeepItemsInView`。 因此，当新项添加到[`CollectionView`](xref:Xamarin.Forms.CollectionView)时，列表中的第一个可见项仍将显示。 若要确保新添加的项始终显示在列表的底部，应将 `ItemsUpdatingScrollMode` 属性设置为 `KeepLastItemInView`：

```xaml
<CollectionView ItemsUpdatingScrollMode="KeepLastItemInView">
    ...
</CollectionView>
```

等效 C# 代码如下：

```csharp
CollectionView collectionView = new CollectionView
{
    ItemsUpdatingScrollMode = ItemsUpdatingScrollMode.KeepLastItemInView
};
```

## <a name="scroll-bar-visibility"></a>滚动条可见性

[`CollectionView`](xref:Xamarin.Forms.CollectionView)定义 `HorizontalScrollBarVisibility` 和 `VerticalScrollBarVisibility` 属性，这些属性由可绑定属性支持。 这些属性获取或设置一个[`ScrollBarVisibility`](xref:Xamarin.Forms.ScrollBarVisibility)枚举值，该值表示水平或垂直滚动条何时可见。 `ScrollBarVisibility` 枚举定义下列成员：

- [`Default`](xref:Xamarin.Forms.ScrollBarVisibility)指示平台的默认滚动条行为，是 `HorizontalScrollBarVisibility` 和 `VerticalScrollBarVisibility` 属性的默认值。
- [`Always`](xref:Xamarin.Forms.ScrollBarVisibility)指示滚动条可见，即使在视图中显示了内容也是如此。
- [`Never`](xref:Xamarin.Forms.ScrollBarVisibility)指示滚动条将不可见，即使内容无法在视图中显示也是如此。

## <a name="snap-points"></a>对齐点

当用户 swipes 启动滚动时，可以控制滚动的结束位置，以便完全显示项。 此功能称为 "对齐"，因为当滚动停止时，项会对齐到位置，并且由[`ItemsLayout`](xref:Xamarin.Forms.ItemsLayout)类中的以下属性控制：

- [`SnapPointsType`](xref:Xamarin.Forms.SnapPointsType)类型的[`SnapPointsType`](xref:Xamarin.Forms.ItemsLayout.SnapPointsType)指定滚动时对齐点的行为。
- [`SnapPointsAlignment`](xref:Xamarin.Forms.SnapPointsAlignment)类型的[`SnapPointsAlignment`](xref:Xamarin.Forms.ItemsLayout.SnapPointsAlignment)指定对齐点如何与项对齐。

这些属性是由[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)对象支持的，这意味着属性可以是数据绑定的目标。

> [!NOTE]
> 发生对齐时，它将以生成最小动作量的方向发生。

### <a name="snap-points-type"></a>对齐点类型

[@No__t_1](xref:Xamarin.Forms.SnapPointsType)枚举定义以下成员：

- `None` 指示滚动不会对齐到项。
- `Mandatory` 指示内容始终对齐到最接近的对齐点，在该点上，滚动沿惯性方向自然停止。
- `MandatorySingle` 指示与 `Mandatory` 相同的行为，但一次只滚动一项。

默认情况下， [`SnapPointsType`](xref:Xamarin.Forms.ItemsLayout.SnapPointsType)属性设置为 `SnapPointsType.None`，这可确保滚动不会对齐项目，如以下屏幕截图所示：

[![IOS 和 Android 上不带对齐点的 CollectionView 垂直列表屏幕截图](scrolling-images/snappoints-none.png "不带对齐点的 CollectionView 垂直列表")](scrolling-images/snappoints-none-large.png#lightbox "不带对齐点的 CollectionView 垂直列表")

### <a name="snap-points-alignment"></a>对齐点对齐

[@No__t_1](xref:Xamarin.Forms.SnapPointsAlignment)枚举定义 `Start`、`Center` 和 `End` 成员。

> [!IMPORTANT]
> 仅当[`SnapPointsType`](xref:Xamarin.Forms.ItemsLayout.SnapPointsType)属性设置为 `Mandatory` 或 `MandatorySingle` 时，才考虑[`SnapPointsAlignment`](xref:Xamarin.Forms.ItemsLayout.SnapPointsAlignment)属性的值。

#### <a name="start"></a>Start

@No__t_0 成员指示对齐点与项的开头边缘对齐。

默认情况下， [`SnapPointsAlignment`](xref:Xamarin.Forms.ItemsLayout.SnapPointsAlignment)属性设置为 `SnapPointsAlignment.Start`。 但是，为了完整起见，以下 XAML 示例显示了如何设置此枚举成员：

```xaml
<CollectionView ItemsSource="{Binding Monkeys}">
    <CollectionView.ItemsLayout>
        <LinearItemsLayout Orientation="Vertical"
                           SnapPointsType="MandatorySingle"
                           SnapPointsAlignment="Start" />
    </CollectionView.ItemsLayout>
    ...
</CollectionView>
```

等效 C# 代码如下：

```csharp
CollectionView collectionView = new CollectionView
{
    ItemsLayout = new LinearItemsLayout(ItemsLayoutOrientation.Vertical)
    {
        SnapPointsType = SnapPointsType.MandatorySingle,
        SnapPointsAlignment = SnapPointsAlignment.Start
    },
    // ...
};
```

当用户 swipes 启动滚动时，顶部项目将与视图的顶部对齐：

[![IOS 和 Android 上包含开始对齐点的 CollectionView 垂直列表的屏幕截图](scrolling-images/snappoints-start.png "带有开始对齐点的 CollectionView 垂直列表")](scrolling-images/snappoints-start-large.png#lightbox "带有开始对齐点的 CollectionView 垂直列表")

#### <a name="center"></a>居中

@No__t_0 成员指示对齐点与项的中心对齐。 下面的 XAML 示例演示如何设置此枚举成员：

```xaml
<CollectionView ItemsSource="{Binding Monkeys}">
    <CollectionView.ItemsLayout>
        <LinearItemsLayout Orientation="Vertical"
                           SnapPointsType="MandatorySingle"
                           SnapPointsAlignment="Center" />
    </CollectionView.ItemsLayout>
    ...
</CollectionView>
```

等效 C# 代码如下：

```csharp
CollectionView collectionView = new CollectionView
{
    ItemsLayout = new LinearItemsLayout(ItemsLayoutOrientation.Vertical)
    {
        SnapPointsType = SnapPointsType.MandatorySingle,
        SnapPointsAlignment = SnapPointsAlignment.Center
    },
    // ...
};
```

当用户 swipes 启动滚动时，顶部的项目将在视图顶部居中对齐：

[![IOS 和 Android 上包含中心对齐点的 CollectionView 垂直列表的屏幕截图](scrolling-images/snappoints-center.png "中心对齐点的 CollectionView 垂直列表")](scrolling-images/snappoints-center-large.png#lightbox "中心对齐点的 CollectionView 垂直列表")

#### <a name="end"></a>End — 结束

@No__t_0 成员指示对齐点与项的尾部边缘对齐。 下面的 XAML 示例演示如何设置此枚举成员：

```xaml
<CollectionView ItemsSource="{Binding Monkeys}">
    <CollectionView.ItemsLayout>
        <LinearItemsLayout Orientation="Vertical"
                           SnapPointsType="MandatorySingle"
                           SnapPointsAlignment="End" />
    </CollectionView.ItemsLayout>
    ...
</CollectionView>
```

等效 C# 代码如下：

```csharp
CollectionView collectionView = new CollectionView
{
    ItemsLayout = new LinearItemsLayout(ItemsLayoutOrientation.Vertical)
    {
        SnapPointsType = SnapPointsType.MandatorySingle,
        SnapPointsAlignment = SnapPointsAlignment.End
    },
    // ...
};
```

当用户 swipes 启动滚动时，底部项目将与视图的底部对齐：

[![在 iOS 和 Android 上带有结束 snap 点的 CollectionView 垂直列表屏幕截图](scrolling-images/snappoints-end.png "结束对齐点的 CollectionView 垂直列表")](scrolling-images/snappoints-end-large.png#lightbox "结束对齐点的 CollectionView 垂直列表")

## <a name="related-links"></a>相关链接

- [CollectionView （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)
