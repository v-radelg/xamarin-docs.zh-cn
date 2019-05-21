---
title: Xamarin.Forms CollectionView 选定内容
description: 默认情况下，禁用 CollectionView 选择。 但是，可以启用单个和多个选择。
ms.prod: xamarin
ms.assetid: 423D91C7-1E58-4735-9E80-58F11CDFD953
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/06/2019
ms.openlocfilehash: febd48f2ffad86ab8b00bafca8c296377f74a07b
ms.sourcegitcommit: 482aef652bdaa440561252b6a1a1c0a40583cd32
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/21/2019
ms.locfileid: "65970698"
---
# <a name="xamarinforms-collectionview-selection"></a>Xamarin.Forms CollectionView 选定内容

[![下载示例](~/media/shared/download.png)下载示例](https://github.com/xamarin/xamarin-forms-samples/tree/forms40/UserInterface/CollectionViewDemos/)

[`CollectionView`](xref:Xamarin.Forms.CollectionView) 定义用于控制项选择的以下属性：

- [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode)类型的[ `SelectionMode` ](xref:Xamarin.Forms.SelectionMode)，选择模式。
- [`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem)类型的`object`，列表中的选定的项。 此属性具有`null`时未不选择任何项的值。
- [`SelectedItems`](xref:Xamarin.Forms.SelectableItemsView.SelectedItems)类型的`IList<object>`，则在列表中选择项。 此属性为只读，并且具有`null`时没有选定的项的值。
- [`SelectionChangedCommand`](xref:Xamarin.Forms.SelectableItemsView.SelectionChangedCommand)类型的`ICommand`，其中的选定的项发生更改时执行。
- [`SelectionChangedCommandParameter`](xref:Xamarin.Forms.SelectableItemsView.SelectionChangedCommandParameter)类型的`object`，这是传递给参数`SelectionChangedCommand`。

所有这些属性都由 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) 对象提供支持，这意味着这些属性可以作为数据绑定的目标。

默认情况下[ `CollectionView` ](xref:Xamarin.Forms.CollectionView)禁用所选内容。 但是，可以通过设置更改此行为[ `SelectionMode` ](xref:Xamarin.Forms.SelectableItemsView.SelectionMode)属性值设置为之一[ `SelectionMode` ](xref:Xamarin.Forms.SelectionMode)枚举成员：

- `None` – 指示不能选择项。 这是默认值。
- `Single` – 指示，选择单个项，而被突出显示的选定项。
- `Multiple` – 指示，选择多个项，而被突出显示所选的项。

[`CollectionView`](xref:Xamarin.Forms.CollectionView) 定义[ `SelectionChanged` ](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged)时引发的事件[ `SelectedItem` ](xref:Xamarin.Forms.SelectableItemsView.SelectedItem)属性更改，或者由于用户选择某个项，从列表中，或当应用程序设置属性。 此外，将激发此事件还何时[ `SelectedItems` ](xref:Xamarin.Forms.SelectableItemsView.SelectedItems)属性更改。 [ `SelectionChangedEventArgs` ](xref:Xamarin.Forms.SelectionChangedEventArgs)对象，它附带`SelectionChanged`事件具有两个属性，这两个类型`IReadOnlyList<object>`:

- `PreviousSelection` – 所选内容发生更改之前已选择的项的列表。
- `CurrentSelection` – 选定，所选内容更改后的项的列表。

## <a name="single-selection"></a>单项选择

当[ `SelectionMode` ](xref:Xamarin.Forms.SelectableItemsView.SelectionMode)属性设置为`Single`中的单个项[ `CollectionView` ](xref:Xamarin.Forms.CollectionView)可以选择。 选择某个项后， [ `SelectedItem` ](xref:Xamarin.Forms.SelectableItemsView.SelectedItem)属性将设置为所选的项的值。 此属性更改时， [ `SelectionChangedCommand` ](xref:Xamarin.Forms.SelectableItemsView.SelectionChangedCommand)执行 (值为[ `SelectionChangedCommandParameter` ](xref:Xamarin.Forms.SelectableItemsView.SelectionChangedCommandParameter)传递给`ICommand`)，并且[ `SelectionChanged` ](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged)触发事件。

下面的 XAML 示例演示[ `CollectionView` ](xref:Xamarin.Forms.CollectionView)可以响应单一项选择：

```xaml
<CollectionView ItemsSource="{Binding Monkeys}"
                SelectionMode="Single"
                SelectionChanged="OnCollectionViewSelectionChanged">
    ...
</CollectionView>
```

等效 C# 代码如下：

```csharp
CollectionView collectionView = new CollectionView
{
    SelectionMode = SelectionMode.Single
};
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
collectionView.SelectionChanged += OnCollectionViewSelectionChanged;
```

在此代码示例中，`OnCollectionViewSelectionChanged`事件处理程序执行时[ `SelectionChanged` ](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged)事件触发时，与检索之前选择的项和当前选定的项的事件处理程序：

```csharp
void OnCollectionViewSelectionChanged(object sender, SelectionChangedEventArgs e)
{
    string previous = (e.PreviousSelection.FirstOrDefault() as Monkey)?.Name;
    string current = (e.CurrentSelection.FirstOrDefault() as Monkey)?.Name;
    ...
}
```

> [!IMPORTANT]
> [ `SelectionChanged` ](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged)由于更改而发生的更改可以引发事件[ `SelectionMode` ](xref:Xamarin.Forms.SelectableItemsView.SelectionMode)属性。

以下屏幕截图显示中的单个项选择[ `CollectionView` ](xref:Xamarin.Forms.CollectionView):

[![单项选择，在 iOS 和 Android 的 CollectionView 垂直列表的屏幕截图](selection-images/single-selection.png "CollectionView 垂直列表与单选")](selection-images/single-selection-large.png#lightbox "一个 CollectionView 垂直列表所选内容")

## <a name="multiple-selection"></a>多个所选内容

当[ `SelectionMode` ](xref:Xamarin.Forms.SelectableItemsView.SelectionMode)属性设置为`Multiple`中的多项[ `CollectionView` ](xref:Xamarin.Forms.CollectionView)可以选择。 选择项后， [ `SelectedItems` ](xref:Xamarin.Forms.SelectableItemsView.SelectedItems)属性将设置为所选项目。 此属性更改时， [ `SelectionChangedCommand` ](xref:Xamarin.Forms.SelectableItemsView.SelectionChangedCommand)执行 (值为[ `SelectionChangedCommandParameter` ](xref:Xamarin.Forms.SelectableItemsView.SelectionChangedCommandParameter)传递给`ICommand`)，并且[ `SelectionChanged` ](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged)触发事件。

下面的 XAML 示例演示[ `CollectionView` ](xref:Xamarin.Forms.CollectionView) ，可以响应多个项选择：

```xaml
<CollectionView ItemsSource="{Binding Monkeys}"
                SelectionMode="Multiple"
                SelectionChanged="OnCollectionViewSelectionChanged">
    ...
</CollectionView>
```

等效 C# 代码如下：

```csharp
CollectionView collectionView = new CollectionView
{
    SelectionMode = SelectionMode.Multiple
};
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
collectionView.SelectionChanged += OnCollectionViewSelectionChanged;
```

在此代码示例中，`OnCollectionViewSelectionChanged`事件处理程序执行时[ `SelectionChanged` ](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged)事件触发时，与检索以前选定的项和当前选定的项的事件处理程序：

```csharp
void OnCollectionViewSelectionChanged(object sender, SelectionChangedEventArgs e)
{
    var previous = e.PreviousSelection;
    var current = e.CurrentSelection;
    ...
}
```

> [!IMPORTANT]
> [ `SelectionChanged` ](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged)由于更改而发生的更改可以引发事件[ `SelectionMode` ](xref:Xamarin.Forms.SelectableItemsView.SelectionMode)属性。

以下屏幕截图显示中的多个项选择[ `CollectionView` ](xref:Xamarin.Forms.CollectionView):

[![使用多个选择，在 iOS 和 Android 上 CollectionView 垂直列表的屏幕截图](selection-images/multiple-selection.png "CollectionView 与多个所选内容的垂直列表")](selection-images/multiple-selection-large.png#lightbox "CollectionView 垂直列表多个所选内容")

## <a name="single-pre-selection"></a>单个预选择

当[ `SelectionMode` ](xref:Xamarin.Forms.SelectableItemsView.SelectionMode)属性设置为`Single`中的单个项[ `CollectionView` ](xref:Xamarin.Forms.CollectionView)可以通过设置预先选择[ `SelectedItem` ](xref:Xamarin.Forms.SelectableItemsView.SelectedItem)对项的属性。 下面的 XAML 示例演示`CollectionView`，它预先选择单个项：

```xaml
<CollectionView ItemsSource="{Binding Monkeys}"
                SelectionMode="Single"
                SelectedItem="{Binding SelectedMonkey, Mode=TwoWay}">
    ...
</CollectionView>
```

等效 C# 代码如下：

```csharp
CollectionView collectionView = new CollectionView
{
    SelectionMode = SelectionMode.Single
};
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
collectionView.SetBinding(SelectableItemsView.SelectedItemProperty, "SelectedMonkey", BindingMode.TwoWay);
```

[ `SelectedItem` ](xref:Xamarin.Forms.SelectableItemsView.SelectedItem)属性数据绑定到`SelectedMonkey`已连接的视图模型，这是类型的属性`Monkey`。 一个`TwoWay`因此，如果用户更改选定的项的值使用绑定`SelectedMonkey`属性将设置与所选`Monkey`对象。 `SelectedMonkey`属性中定义`MonkeysViewModel`类和设置到的第四个项`Monkeys`集合：

```csharp
public class MonkeysViewModel : INotifyPropertyChanged
{
    ...
    public ObservableCollection<Monkey> Monkeys { get; private set; }

    Monkey selectedMonkey;
    public Monkey SelectedMonkey
    {
        get
        {
            return selectedMonkey;
        }
        set
        {
            if (selectedMonkey != value)
            {
                selectedMonkey = value;
            }
        }
    }

    public MonkeysViewModel()
    {
        ...
        selectedMonkey = Monkeys.Skip(3).FirstOrDefault();
    }
    ...
}
```

因此，当[ `CollectionView` ](xref:Xamarin.Forms.CollectionView)出现，列表中的第四个项已预先选择：

[![IOS 和 Android 上的单个预选择的 CollectionView 垂直列表的屏幕截图](selection-images/single-pre-selection.png "CollectionView 与单个预选择的垂直列表")](selection-images/single-pre-selection-large.png#lightbox "CollectionView 垂直列表使用单个预选择")

## <a name="multiple-pre-selection"></a>多个预选择

当[ `SelectionMode` ](xref:Xamarin.Forms.SelectableItemsView.SelectionMode)属性设置为`Multiple`中的多项[ `CollectionView` ](xref:Xamarin.Forms.CollectionView)可以预先选择。 下面的 XAML 示例演示`CollectionView`，使预选择多个项：

```xaml
<CollectionView x:Name="collectionView"
                ItemsSource="{Binding Monkeys}"
                SelectionMode="Multiple">
    ...
</CollectionView>
```

等效 C# 代码如下：

```csharp
CollectionView collectionView = new CollectionView
{
    SelectionMode = SelectionMode.Multiple
};
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
```

中的多项[ `CollectionView` ](xref:Xamarin.Forms.CollectionView)可以预先选择的将它们添加到[ `SelectedItems` ](xref:Xamarin.Forms.SelectableItemsView.SelectedItems)属性：

```csharp
collectionView.SelectedItems.Add(viewModel.Monkeys.Skip(1).FirstOrDefault());
collectionView.SelectedItems.Add(viewModel.Monkeys.Skip(3).FirstOrDefault());
collectionView.SelectedItems.Add(viewModel.Monkeys.Skip(4).FirstOrDefault());
```

> [!NOTE]
> [ `SelectedItems` ](xref:Xamarin.Forms.SelectableItemsView.SelectedItems)属性只读的因此不能使用双向数据绑定来预先选择的项。

因此，当[ `CollectionView` ](xref:Xamarin.Forms.CollectionView)出现，第二个，第四，预先选择列表中的第五个项并且：

[![IOS 和 Android 上的多个预选择的 CollectionView 垂直列表的屏幕截图](selection-images/multiple-pre-selection.png "CollectionView 具有多个预选择的垂直列表")](selection-images/multiple-pre-selection-large.png#lightbox "CollectionView 垂直使用多个预选择的列表")

## <a name="change-selected-item-color"></a>更改选定的项颜色

[`CollectionView`](xref:Xamarin.Forms.CollectionView) 具有`Selected` [ `VisualState` ](xref:Xamarin.Forms.VisualState)可用于启动中的选定项的可视化变更`CollectionView`。 一个常见用例此`VisualState`是更改下面的 XAML 示例中所示的选定项的背景色：

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <Style TargetType="Grid">
            <Setter Property="VisualStateManager.VisualStateGroups">
                <VisualStateGroupList>
                    <VisualStateGroup x:Name="CommonStates">
                        <VisualState x:Name="Normal" />
                        <VisualState x:Name="Selected">
                            <VisualState.Setters>
                                <Setter Property="BackgroundColor"
                                        Value="LightSkyBlue" />
                            </VisualState.Setters>
                        </VisualState>
                    </VisualStateGroup>
                </VisualStateGroupList>
            </Setter>
        </Style>
    </ContentPage.Resources>
    <StackLayout Margin="20">
        <CollectionView ItemsSource="{Binding Monkeys}"
                        SelectionMode="Single">
            <CollectionView.ItemTemplate>
                <DataTemplate>
                    <Grid Padding="10">
                        ...
                    </Grid>
                </DataTemplate>
            </CollectionView.ItemTemplate>
        </CollectionView>
    </StackLayout>
</ContentPage>
```

> [!IMPORTANT]
> [ `Style` ](xref:Xamarin.Forms.Style) ，其中包含`Selected``VisualState`必须拥有[ `TargetType` ](xref:Xamarin.Forms.Style.TargetType)是根元素的类型的属性值[ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate)，将其设置为`ItemTemplate`属性值。

在此示例中， [ `Style.TargetType` ](xref:Xamarin.Forms.Style.TargetType)属性值设置为`Grid`因为的根元素[ `ItemTemplate` ](xref:Xamarin.Forms.ItemsView.ItemTemplate)是[ `Grid` ](xref:Xamarin.Forms.Grid)。 `Selected` [ `VisualState` ](xref:Xamarin.Forms.VisualState)指定时中的项[ `CollectionView` ](xref:Xamarin.Forms.CollectionView)选择，则[ `BackgroundColor` ](xref:Xamarin.Forms.VisualElement.BackgroundColor)的项将设置为`LightSkyBlue`:

[![使用自定义单个所选的颜色，iOS 和 Android 上 CollectionView 垂直列表的屏幕截图](selection-images/single-selection-color.png "CollectionView 垂直列表与自定义单个所选的颜色")] (selection-images/single-selection-color-large.png#lightbox "使用自定义单个所选的颜色之间导航垂直列表")

可视状态的详细信息，请参阅[Xamarin.Forms 视觉状态管理器](~/xamarin-forms/user-interface/visual-state-manager.md)。

## <a name="disable-selection"></a>禁用所选内容

[`CollectionView`](xref:Xamarin.Forms.CollectionView) 默认情况下，选择处于禁用状态。 但是，如果`CollectionView`已选择启用，可以通过设置禁用[ `SelectionMode` ](xref:Xamarin.Forms.SelectableItemsView.SelectionMode)属性设置为`None`:

```xaml
<CollectionView ...
                SelectionMode="None" />
```

等效 C# 代码如下：

```csharp
CollectionView collectionView = new CollectionView
{
    ...
    SelectionMode = SelectionMode.None
};
```

当[ `SelectionMode` ](xref:Xamarin.Forms.SelectableItemsView.SelectionMode)属性设置为`None`中的项[ `CollectionView` ](xref:Xamarin.Forms.CollectionView)不能选择[ `SelectedItem` ](xref:Xamarin.Forms.SelectableItemsView.SelectedItem)属性将保持`null`，并[ `SelectionChanged` ](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged)将不会触发事件。

> [!NOTE]
> 当选择项并[ `SelectionMode` ](xref:Xamarin.Forms.SelectableItemsView.SelectionMode)属性更改从`Single`到`None`，则[ `SelectedItem` ](xref:Xamarin.Forms.SelectableItemsView.SelectedItem)属性将设置为`null`和[ `SelectionChanged` ](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged)将使用空触发事件`CurrentSelection`属性。

## <a name="related-links"></a>相关链接

- [CollectionView （示例）](https://github.com/xamarin/xamarin-forms-samples/tree/forms40/UserInterface/CollectionViewDemos/)
- [Xamarin.Forms Visual State Manager](~/xamarin-forms/user-interface/visual-state-manager.md)
