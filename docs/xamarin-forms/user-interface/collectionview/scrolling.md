---
title: Xamarin CollectionView 滚动
description: 当用户 swipes 启动滚动时，可以控制滚动的结束位置，以便完全显示项。 此外，CollectionView 还定义了两个 ScrollTo 方法，这些方法以编程方式将项滚动到视图中。
ms.prod: xamarin
ms.assetid: 2ED719AF-33D2-434D-949A-B70B479C9BA5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/13/2019
ms.openlocfilehash: 303266f44664f7f57aeaf36869a3a06c8eb91870
ms.sourcegitcommit: 699de58432b7da300ddc2c85842e5d9e129b0dc5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2019
ms.locfileid: "69888640"
---
# <a name="xamarinforms-collectionview-scrolling"></a>Xamarin CollectionView 滚动

![](~/media/shared/preview.png "此 API 当前为预发布版本")

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)

[`CollectionView`](xref:Xamarin.Forms.CollectionView)定义两[`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*)个方法，它们将项滚动到视图中。 其中一个重载将指定索引处的项滚动到视图中，而另一个重载将指定项滚动到视图中。 这两个重载都有其他参数，可以指定该参数以指示滚动完成后项的准确位置，以及是否对滚动进行动画处理。

[`CollectionView`](xref:Xamarin.Forms.CollectionView)定义在调用其中一个[`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*)方法时激发的[事件。`ScrollToRequested`](xref:Xamarin.Forms.ItemsView.ScrollToRequested) `IsAnimated` `Index` `ScrollToPosition`事件附带的[`ScrollToRequestedEventArgs`](xref:Xamarin.Forms.ScrollToRequestedEventArgs)对象具有多个`Item`属性，包括、、和。 `ScrollToRequested` 这些属性是从`ScrollTo`方法调用中指定的参数设置的。

此外， [`CollectionView`](xref:Xamarin.Forms.CollectionView)还定义了`Scrolled`一个事件，该事件将激发以指示发生滚动。 事件附带的对象具有多个属性。 `ItemsViewScrolledEventArgs` `Scrolled` 有关详细信息，请参阅[检测滚动](#detect-scrolling)。

[`CollectionView`](xref:Xamarin.Forms.CollectionView)还定义了`ItemsUpdatingScrollMode`一个属性，该属性表示在添加`CollectionView`新项时的滚动行为。 有关此属性的详细信息，请参阅[在添加新项时控制滚动位置](#control-scroll-position-when-new-items-are-added)。

当用户 swipes 启动滚动时，可以控制滚动的结束位置，以便完全显示项。 此功能称为 "对齐"，因为当滚动停止时，项会对齐到位置。 有关详细信息，请参阅[对齐点](#snap-points)。

[`CollectionView`](xref:Xamarin.Forms.CollectionView)还可以在用户滚动时以增量方式加载数据。 有关详细信息，请参阅[以增量方式加载数据](populate-data.md#load-data-incrementally)。

## <a name="detect-scrolling"></a>检测滚动

[`CollectionView`](xref:Xamarin.Forms.CollectionView)定义一个`Scrolled`事件，该事件将激发以指示发生滚动。 下面的 XAML 示例显示了`CollectionView`一个，它设置`Scrolled`事件的事件处理程序：

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

在此代码示例中， `OnCollectionViewScrolled`事件处理程序在`Scrolled`事件激发时执行：

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

事件处理程序输出事件随附的`ItemsViewScrolledEventArgs`对象的值。 `OnCollectionViewScrolled`

> [!IMPORTANT]
> 触发用户启动滚动的事件和以编程方式滚动事件。`Scrolled`

## <a name="scroll-an-item-at-an-index-into-view"></a>将索引中的项滚动到视图中

第一[`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*)种方法重载将指定索引处的项滚动到视图中。 给定一个[`CollectionView`](xref:Xamarin.Forms.CollectionView)名为`collectionView`的对象，下面的示例演示如何将索引12处的项滚动到视图中：

```csharp
collectionView.ScrollTo(12);
```

> [!NOTE]
> 调用[`ScrollToRequested`](xref:Xamarin.Forms.ItemsView.ScrollToRequested) [方法`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*)时，将触发事件。

## <a name="scroll-an-item-into-view"></a>将项滚动到视图

第二[`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*)个方法重载将指定项滚动到视图中。 给定一个[`CollectionView`](xref:Xamarin.Forms.CollectionView)名为`collectionView`的对象，下面的示例演示如何将指定项滚动到视图中：

```csharp
MonkeysViewModel viewModel = BindingContext as MonkeysViewModel;
Monkey monkey = viewModel.Monkeys.FirstOrDefault(m => m.Name == "Proboscis Monkey");
collectionView.ScrollTo(monkey);
```

> [!NOTE]
> 调用[`ScrollToRequested`](xref:Xamarin.Forms.ItemsView.ScrollToRequested) [方法`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*)时，将触发事件。

## <a name="scroll-bar-visibility"></a>滚动条可见性

[`CollectionView`](xref:Xamarin.Forms.CollectionView)定义`HorizontalScrollBarVisibility`由`VerticalScrollBarVisibility`可绑定属性支持的和属性。 这些属性可获取或设置[`ScrollBarVisibility`](xref:Xamarin.Forms.ScrollBarVisibility)一个枚举值，该值表示水平或垂直滚动条可见的时间。 `ScrollBarVisibility` 枚举定义下列成员：

- [`Default`](xref:Xamarin.Forms.ScrollBarVisibility)指示平台的默认滚动条行为，是`HorizontalScrollBarVisibility`和`VerticalScrollBarVisibility`属性的默认值。
- [`Always`](xref:Xamarin.Forms.ScrollBarVisibility)指示滚动条是可见的，即使在视图中显示内容时也是如此。
- [`Never`](xref:Xamarin.Forms.ScrollBarVisibility)指示即使内容无法在视图中显示，也不会显示滚动条。

## <a name="control-scroll-position"></a>控制滚动位置

在将项滚动到视图中时，可以使用`position` [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*)方法的参数指定项在滚动完成后的确切位置。 此参数接受[`ScrollToPosition`](xref:Xamarin.Forms.ScrollToPosition)枚举成员。

### <a name="makevisible"></a>MakeVisible

[`ScrollToPosition.MakeVisible`](xref:Xamarin.Forms.ScrollToPosition)成员指示应滚动项，直到它在视图中可见：

```csharp
collectionView.ScrollTo(monkey, position: ScrollToPosition.MakeVisible);
```

此示例代码将导致滚动项进入查看所需的最少滚动：

[![在 iOS 和 Android 上将项滚动到视图中的 CollectionView 垂直列表屏幕截图](scrolling-images/scrolltoposition-makevisible.png "具有滚动项的 CollectionView 垂直列表")](scrolling-images/scrolltoposition-makevisible-large.png#lightbox "具有滚动项的 CollectionView 垂直列表")

> [!NOTE]
> 默认情况下， `position`如果在调用`ScrollTo`方法时未指定参数，则使用[成员。`ScrollToPosition.MakeVisible`](xref:Xamarin.Forms.ScrollToPosition)

### <a name="start"></a>Start

[`ScrollToPosition.Start`](xref:Xamarin.Forms.ScrollToPosition)成员指示该项应滚动到视图的开头：

```csharp
collectionView.ScrollTo(monkey, position: ScrollToPosition.Start);
```

此示例代码会将项滚动到视图的开头：

[![在 iOS 和 Android 上将项滚动到视图中的 CollectionView 垂直列表屏幕截图](scrolling-images/scrolltoposition-start.png "具有滚动项的 CollectionView 垂直列表")](scrolling-images/scrolltoposition-start-large.png#lightbox "具有滚动项的 CollectionView 垂直列表")

### <a name="center"></a>居中

[`ScrollToPosition.Center`](xref:Xamarin.Forms.ScrollToPosition)成员指示该项应滚动到视图的中心：

```csharp
collectionView.ScrollTo(monkey, position: ScrollToPosition.Center);
```

此示例代码会将项滚动到视图的中心：

[![在 iOS 和 Android 上将项滚动到视图中的 CollectionView 垂直列表屏幕截图](scrolling-images/scrolltoposition-center.png "具有滚动项的 CollectionView 垂直列表")](scrolling-images/scrolltoposition-center-large.png#lightbox "具有滚动项的 CollectionView 垂直列表")

### <a name="end"></a>End — 结束

[`ScrollToPosition.End`](xref:Xamarin.Forms.ScrollToPosition)成员指示该项应滚动到视图的末尾：

```csharp
collectionView.ScrollTo(monkey, position: ScrollToPosition.End);
```

此示例代码会将项滚动到视图的末尾：

[![在 iOS 和 Android 上将项滚动到视图中的 CollectionView 垂直列表屏幕截图](scrolling-images/scrolltoposition-end.png "具有滚动项的 CollectionView 垂直列表")](scrolling-images/scrolltoposition-end-large.png#lightbox "具有滚动项的 CollectionView 垂直列表")

## <a name="disable-scroll-animation"></a>禁用滚动动画

将项滚动到视图中时，将显示滚动动画。 但是，可以通过将`animate` `ScrollTo`方法的参数设置为来`false`禁用此动画：

```csharp
collectionView.ScrollTo(monkey, animate: false);
```

## <a name="control-scroll-position-when-new-items-are-added"></a>在添加新项时控制滚动位置

[`CollectionView`](xref:Xamarin.Forms.CollectionView)定义一个`ItemsUpdatingScrollMode`属性，该属性由可绑定的属性支持。 此属性获取或设置一个`ItemsUpdatingScrollMode`枚举值，该值表示在添加新项`CollectionView`时的滚动行为。 `ItemsUpdatingScrollMode` 枚举定义下列成员：

- `KeepItemsInView`调整滚动偏移量，以便在添加新项时保持显示第一个可见项。
- `KeepScrollOffset`在添加新项时，使滚动偏移量相对于列表的开头保持。
- `KeepLastItemInView`调整滚动偏移量，以便在添加新项时使最后一项可见。

`ItemsUpdatingScrollMode`属性的默认值为`KeepItemsInView`。 因此，将新项添加到[`CollectionView`](xref:Xamarin.Forms.CollectionView)列表中的第一个可见项时，将保持显示。 若要确保新添加的项始终显示在列表的底部， `ItemsUpdatingScrollMode`应将属性设置为： `KeepLastItemInView`

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

## <a name="snap-points"></a>对齐点

当用户 swipes 启动滚动时，可以控制滚动的结束位置，以便完全显示项。 此功能称为 "对齐"，因为当滚动停止时项将对齐到位置，并由[`ItemsLayout`](xref:Xamarin.Forms.ItemsLayout)类中的以下属性控制：

- [`SnapPointsType`](xref:Xamarin.Forms.ItemsLayout.SnapPointsType)类型[`SnapPointsType`](xref:Xamarin.Forms.SnapPointsType)为的指定滚动时对齐点的行为。
- [`SnapPointsAlignment`](xref:Xamarin.Forms.ItemsLayout.SnapPointsAlignment)类型[`SnapPointsAlignment`](xref:Xamarin.Forms.SnapPointsAlignment)为的指定对齐点如何与项对齐。

这些属性是由[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)对象支持的，这意味着属性可以是数据绑定的目标。

> [!NOTE]
> 发生对齐时，它将以生成最小动作量的方向发生。

### <a name="snap-points-type"></a>对齐点类型

[`SnapPointsType`](xref:Xamarin.Forms.SnapPointsType)枚举定义以下成员：

- `None`指示滚动不会对齐到项。
- `Mandatory`指示内容始终对齐到最接近的对齐点，滚动点沿惯性方向自然停止。
- `MandatorySingle`指示与相同的行为`Mandatory`，但一次只滚动一项。

默认情况下， [`SnapPointsType`](xref:Xamarin.Forms.ItemsLayout.SnapPointsType)属性设置为`SnapPointsType.None`，这可确保滚动不会对齐项目，如以下屏幕截图所示：

[![IOS 和 Android 上不带对齐点的 CollectionView 垂直列表屏幕截图](scrolling-images/snappoints-none.png "不带对齐点的 CollectionView 垂直列表")](scrolling-images/snappoints-none-large.png#lightbox "不带对齐点的 CollectionView 垂直列表")

### <a name="snap-points-alignment"></a>对齐点对齐

枚举定义`Start` 、`Center`和成员`End` 。 [`SnapPointsAlignment`](xref:Xamarin.Forms.SnapPointsAlignment)

> [!IMPORTANT]
> 仅[`SnapPointsAlignment`](xref:Xamarin.Forms.ItemsLayout.SnapPointsAlignment) `Mandatory`当属性设置为或`MandatorySingle`时，才考虑属性的值。 [`SnapPointsType`](xref:Xamarin.Forms.ItemsLayout.SnapPointsType)

#### <a name="start"></a>Start

`SnapPointsAlignment.Start`成员指示对齐点与项的开头边缘对齐。

默认情况下， [`SnapPointsAlignment`](xref:Xamarin.Forms.ItemsLayout.SnapPointsAlignment)属性设置为。 `SnapPointsAlignment.Start` 但是，为了完整起见，以下 XAML 示例显示了如何设置此枚举成员：

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

当用户 swipes 启动滚动时，顶部项目将与视图的顶部对齐：

[![IOS 和 Android 上包含开始对齐点的 CollectionView 垂直列表的屏幕截图](scrolling-images/snappoints-start.png "带有开始对齐点的 CollectionView 垂直列表")](scrolling-images/snappoints-start-large.png#lightbox "带有开始对齐点的 CollectionView 垂直列表")

#### <a name="center"></a>居中

`SnapPointsAlignment.Center`成员指示对齐点与项的中心对齐。 下面的 XAML 示例演示如何设置此枚举成员：

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

当用户 swipes 启动滚动时，顶部的项目将在视图顶部居中对齐：

[![IOS 和 Android 上包含中心对齐点的 CollectionView 垂直列表的屏幕截图](scrolling-images/snappoints-center.png "中心对齐点的 CollectionView 垂直列表")](scrolling-images/snappoints-center-large.png#lightbox "中心对齐点的 CollectionView 垂直列表")

#### <a name="end"></a>End — 结束

`SnapPointsAlignment.End`成员指示对齐点与项的尾随边缘对齐。 下面的 XAML 示例演示如何设置此枚举成员：

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

当用户 swipes 启动滚动时，底部项目将与视图的底部对齐：

[![在 iOS 和 Android 上带有结束 snap 点的 CollectionView 垂直列表屏幕截图](scrolling-images/snappoints-end.png "结束对齐点的 CollectionView 垂直列表")](scrolling-images/snappoints-end-large.png#lightbox "结束对齐点的 CollectionView 垂直列表")

## <a name="related-links"></a>相关链接

- [CollectionView （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)
