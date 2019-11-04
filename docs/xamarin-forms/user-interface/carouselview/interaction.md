---
title: Xamarin CarouselView 交互
description: 可以通过 CurrentItem 和位置属性访问 CarouselView 中当前显示的项。
ms.prod: xamarin
ms.assetid: 854D97E5-D119-4BE2-AE7C-BD428792C992
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/14/2019
ms.openlocfilehash: fd85876b38c21c7ff1ea85d7a61c1449395d56f5
ms.sourcegitcommit: e71474f91639bb43159b22f5d534325c3270ba93
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/22/2019
ms.locfileid: "72749793"
---
# <a name="xamarinforms-carouselview-interaction"></a>Xamarin CarouselView 交互

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-carouselviewdemos/)

[`CarouselView`](xref:Xamarin.Forms.CarouselView)定义以下控制用户交互的属性：

- `object`类型的 `CurrentItem`，当前正在显示的项。 此属性的默认绑定模式为 `TwoWay`，如果没有要显示的数据，则具有 `null` 值。
- `ICommand`类型的 `CurrentItemChangedCommand`，在当前项发生更改时执行。
- `CurrentItemChangedCommandParameter`，属于 `object` 类型，是传递给 `CurrentItemChangedCommand` 的参数。
- `IsBounceEnabled`类型 `bool`，用于指定 `CarouselView` 是否在内容边界处弹跳。 默认值为 `true`。
- `bool`类型的 `IsSwipeEnabled`，它确定滑动手势是否将更改显示的项。 默认值为 `true`。
- `Position`，类型 `int`，即基础集合中当前项的索引。 此属性的默认绑定模式为 `TwoWay`，如果没有要显示的数据，则其值为0。
- `ICommand`类型的 `PositionChangedCommand`，将在位置更改时执行。
- `PositionChangedCommandParameter`，属于 `object` 类型，是传递给 `PositionChangedCommand` 的参数。

所有这些属性都由 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) 对象提供支持，这意味着这些属性可以作为数据绑定的目标。

[`CarouselView`](xref:Xamarin.Forms.CarouselView)定义一个 `CurrentItemChanged` 事件，该事件在 `CurrentItem` 属性发生更改时引发，无论是用户滚动还是应用程序设置属性。 `CurrentItemChanged` 事件附带的 `CurrentItemChangedEventArgs` 对象具有两个属性，两者都是 `object`类型：

- `PreviousItem` –上一项在属性更改后发生。
- `CurrentItem` –属性更改后的当前项。

[`CarouselView`](xref:Xamarin.Forms.CarouselView)还定义了一个 `PositionChanged` 事件，该事件在 `Position` 属性发生更改时引发，无论是用户滚动还是应用程序设置属性。 `PositionChanged` 事件附带的 `PositionChangedEventArgs` 对象具有两个属性，两者都是 `int`类型：

- `PreviousPosition` –属性更改后的上一个位置。
- `CurrentPosition` –属性更改后的当前位置。

## <a name="respond-to-the-current-item-changing"></a>响应当前项更改

当当前显示的项发生更改时，`CurrentItem` 属性将设置为该项的值。 此属性发生更改时，将用传递到 `ICommand`的 `CurrentItemChangedCommandParameter` 的值来执行 `CurrentItemChangedCommand`。 然后更新 `Position` 属性，并激发 `CurrentItemChanged` 事件。

> [!IMPORTANT]
> 当 `CurrentItem` 属性更改时，`Position` 属性将更改。 这将导致执行 `PositionChangedCommand`，并激发 `PositionChanged` 事件。

### <a name="event"></a>事件

下面的 XAML 示例显示了一个[`CarouselView`](xref:Xamarin.Forms.CarouselView)，该使用事件处理程序来响应当前项更改：

```xaml
<CarouselView ItemsSource="{Binding Monkeys}"
              CurrentItemChanged="OnCurrentItemChanged">
    ...
</CarouselView>
```

等效 C# 代码如下：

```csharp
CarouselView carouselView = new CarouselView();
carouselView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
carouselView.CurrentItemChanged += OnCurrentItemChanged;
```

在此示例中，当引发 `CurrentItemChanged` 事件时，将执行 `OnCurrentItemChanged` 事件处理程序，事件处理程序将公开以前的项和当前项：

```csharp
void OnCurrentItemChanged(object sender, CurrentItemChangedEventArgs e)
{
    Monkey previousItem = e.PreviousItem as Monkey;
    Monkey currentItem = e.CurrentItem as Monkey;
}
```

### <a name="command"></a>命令

下面的 XAML 示例显示了一个[`CarouselView`](xref:Xamarin.Forms.CarouselView)，该使用命令对当前项的更改进行响应：

```xaml
<CarouselView ItemsSource="{Binding Monkeys}"
              CurrentItemChangedCommand="{Binding ItemChangedCommand}"
              CurrentItemChangedCommandParameter="{Binding Source={RelativeSource Self}, Path=CurrentItem}">
    ...
</CarouselView>
```

等效 C# 代码如下：

```csharp
CarouselView carouselView = new CarouselView();
carouselView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
carouselView.SetBinding(CarouselView.CurrentItemChangedCommandProperty, "ItemChangedCommand");
carouselView.SetBinding(CarouselView.CurrentItemChangedCommandParameterProperty, new Binding("CurrentItem", source: RelativeBindingSource.Self));
```

在此示例中，`CurrentItemChangedCommand` 属性绑定到 `ItemChangedCommand` 属性，并将 `CurrentItem` 属性值作为参数传递给它。 然后，`ItemChangedCommand` 可以根据需要响应当前项更改：

```csharp
public ICommand ItemChangedCommand => new Command<Monkey>((item) =>
{
    PreviousMonkey = CurrentMonkey;
    CurrentMonkey = item;
});
```

在此示例中，`ItemChangedCommand` 更新用于存储以前和当前项的对象。

## <a name="respond-to-the-position-changing"></a>响应位置变化

当当前显示的项发生更改时，`Position` 属性将设置为基础集合中当前项的索引。 此属性发生更改时，将用传递到 `ICommand`的 `PositionChangedCommandParameter` 的值来执行 `PositionChangedCommand`。 然后，将激发 `PositionChanged` 事件。 如果 `Position` 属性已以编程方式更改，则[`CarouselView`](xref:Xamarin.Forms.CarouselView)将滚动到与 `Position` 值相对应的项。

> [!NOTE]
> 将 `Position` 属性设置为0将导致显示基础集合中的第一项。

### <a name="event"></a>事件

下面的 XAML 示例显示了一个[`CarouselView`](xref:Xamarin.Forms.CarouselView)，该使用事件处理程序来响应 `Position` 属性的更改：

```xaml
<CarouselView ItemsSource="{Binding Monkeys}"              
              PositionChanged="OnPositionChanged">
    ...
</CarouselView>
```

等效 C# 代码如下：

```csharp
CarouselView carouselView = new CarouselView();
carouselView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
carouselView.PositionChanged += OnPositionChanged;
```

在此示例中，当引发 `PositionChanged` 事件时，将执行 `OnPositionChanged` 事件处理程序，并在事件处理程序中公开上一个和当前的位置：

```csharp
void OnPositionChanged(object sender, PositionChangedEventArgs e)
{
    int previousItemPosition = e.PreviousPosition;
    int currentItemPosition = e.CurrentPosition;
}
```

### <a name="command"></a>命令

下面的 XAML 示例显示了一个[`CarouselView`](xref:Xamarin.Forms.CarouselView) ，它使用命令来响应 `Position` 属性的更改：

```xaml
<CarouselView ItemsSource="{Binding Monkeys}"
              PositionChangedCommand="{Binding PositionChangedCommand}"
              PositionChangedCommandParameter="{Binding Source={RelativeSource Self}, Path=Position}">
    ...
</CarouselView>
```

等效 C# 代码如下：

```csharp
CarouselView carouselView = new CarouselView();
carouselView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
carouselView.SetBinding(CarouselView.PositionChangedCommandProperty, "PositionChangedCommand");
carouselView.SetBinding(CarouselView.PositionChangedCommandParameterProperty, new Binding("Position", source: RelativeBindingSource.Self));
```

在此示例中，`PositionChangedCommand` 属性绑定到 `PositionChangedCommand` 属性，并将 `Position` 属性值作为参数传递给它。 然后，`PositionChangedCommand` 可以根据需要响应更改的位置：

```csharp
public ICommand PositionChangedCommand => new Command<int>((position) =>
{
    PreviousPosition = CurrentPosition;
    CurrentPosition = position;
});
```

在此示例中，`PositionChangedCommand` 更新存储前一个和当前位置的对象。

## <a name="preset-the-current-item"></a>预设当前项

可以通过将 `CurrentItem` 属性设置为项，以编程方式设置[`CarouselView`](xref:Xamarin.Forms.CarouselView)中的当前项。 下面的 XAML 示例显示了预选择当前项的 `CarouselView`：

```xaml
<CarouselView ItemsSource="{Binding Monkeys}"
              CurrentItem="{Binding CurrentItem}">
    ...
</CarouselView>
```

等效 C# 代码如下：

```csharp
CarouselView carouselView = new CarouselView();
carouselView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
carouselView.SetBinding(CarouselView.CurrentItemProperty, "CurrentItem");
```

> [!NOTE]
> `CurrentItem` 属性的默认绑定模式为 `TwoWay`。

`CarouselView.CurrentItem` 属性数据绑定到 `Monkey`类型的已连接视图模型的 `CurrentItem` 属性。 默认情况下，使用 `TwoWay` 绑定，以便在用户更改当前项时，`CurrentItem` 属性的值将设置为当前 `Monkey` 对象。 `CurrentItem` 属性是在 `MonkeysViewModel` 类中定义的，并设置为 `Monkeys` 集合中的第四项：

```csharp
public class MonkeysViewModel : INotifyPropertyChanged
{
    // ...
    public ObservableCollection<Monkey> Monkeys { get; private set; }

    public Monkey CurrentItem { get; set; }

    public MonkeysViewModel()
    {
        // ...
        CurrentItem = Monkeys.Skip(3).FirstOrDefault();
        OnPropertyChanged("CurrentItem");
    }
}
```

## <a name="preset-the-position"></a>预设位置

可以通过将 `Position` 属性设置为基础集合中项的索引，以编程方式设置显示的项[`CarouselView`](xref:Xamarin.Forms.CarouselView) 。 下面的 XAML 示例显示了用于设置显示项的 `CarouselView`：

```xaml
<CarouselView ItemsSource="{Binding Monkeys}"
              Position="{Binding Position}">
    ...
</CarouselView>
```

等效 C# 代码如下：

```csharp
CarouselView carouselView = new CarouselView();
carouselView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
carouselView.SetBinding(CarouselView.PositionProperty, "Position");
```

> [!NOTE]
> `Position` 属性的默认绑定模式为 `TwoWay`。

`CarouselView.Position` 属性数据绑定到 `int`类型的已连接视图模型的 `Position` 属性。 默认情况下，使用 `TwoWay` 绑定，以便在用户滚动[`CarouselView`](xref:Xamarin.Forms.CarouselView)时，`Position` 属性的值将设置为所显示的项的索引。 `Position` 属性是在 `MonkeysViewModel` 类中定义的，并设置为 `Monkeys` 集合中的第四项：

```csharp
public class MonkeysViewModel : INotifyPropertyChanged
{
    // ...
    public int Position { get; set; }

    public MonkeysViewModel()
    {
        // ...
        Position = 3;
        OnPropertyChanged("Position");
    }
}
```

## <a name="clear-the-current-item"></a>清除当前项

可以通过将 `CurrentItem` 属性设置为，或将其绑定到 `null`来清除该属性。

## <a name="disable-bounce"></a>禁用弹跳

默认情况下， [`CarouselView`](xref:Xamarin.Forms.CarouselView)在内容边界处弹跳项。 可以通过将 `IsBounceEnabled` 属性设置为 `false`来禁用此设置。

## <a name="disable-swipe-interaction"></a>禁用滑动交互

默认情况下， [`CarouselView`](xref:Xamarin.Forms.CarouselView)允许用户使用滑动手势在项目间移动。 可以通过将 `IsSwipeEnabled` 属性设置为 `false`来禁用此滑动交互。

## <a name="related-links"></a>相关链接

- [CarouselView （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-carouselviewdemos/)
