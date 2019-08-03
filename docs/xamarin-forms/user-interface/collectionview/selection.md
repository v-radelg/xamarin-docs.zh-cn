---
title: Xamarin CollectionView 选择
description: 默认情况下, CollectionView 选择处于禁用状态。 但是, 可以启用单个和多个选择。
ms.prod: xamarin
ms.assetid: 423D91C7-1E58-4735-9E80-58F11CDFD953
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/06/2019
ms.openlocfilehash: f1a3e8bb8959588e64339f70268370440f356be9
ms.sourcegitcommit: c6e56545eafd8ff9e540d56aba32aa6232c5315f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/02/2019
ms.locfileid: "68738962"
---
# <a name="xamarinforms-collectionview-selection"></a>Xamarin CollectionView 选择

![](~/media/shared/preview.png "此 API 当前为预发布版本")

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)

[`CollectionView`](xref:Xamarin.Forms.CollectionView)定义控制项选择的下列属性:

- [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode)类型[`SelectionMode`](xref:Xamarin.Forms.SelectionMode)为的选择模式。
- [`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem), 类型`object`为, 列表中的选定项。 此属性的默认绑定模式`TwoWay`为, 在未选择任何项时`null`具有值。
- [`SelectedItems`](xref:Xamarin.Forms.SelectableItemsView.SelectedItems), 类型`IList<object>`为, 列表中选定的项。 此属性的默认绑定模式`OneWay`为, 在未选择任何项时`null`具有值。
- [`SelectionChangedCommand`](xref:Xamarin.Forms.SelectableItemsView.SelectionChangedCommand), 类型`ICommand`为, 在选定项发生更改时执行。
- [`SelectionChangedCommandParameter`](xref:Xamarin.Forms.SelectableItemsView.SelectionChangedCommandParameter), 类型`object`为, 它是传递到的`SelectionChangedCommand`参数。

所有这些属性都由 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) 对象提供支持，这意味着这些属性可以作为数据绑定的目标。

默认情况下[`CollectionView`](xref:Xamarin.Forms.CollectionView) , 选定内容处于禁用状态。 但是, 可以通过将[`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode)属性值设置为[`SelectionMode`](xref:Xamarin.Forms.SelectionMode)枚举成员之一来更改此行为:

- `None`–表示无法选择项。 这是默认值。
- `Single`–指示可以选择单个项, 突出显示选定项。
- `Multiple`–指示可以选择多个项, 突出显示选定项。

[`CollectionView`](xref:Xamarin.Forms.CollectionView)定义一个[`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged)事件, 该事件在[`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem)属性发生更改时触发, 无论用户是从列表中选择项, 还是应用程序设置属性。 此外, 当[`SelectedItems`](xref:Xamarin.Forms.SelectableItemsView.SelectedItems)属性发生更改时, 也会触发此事件。 事件附带的[`SelectionChangedEventArgs`](xref:Xamarin.Forms.SelectionChangedEventArgs)对象有两个属性, 两者均为类型`IReadOnlyList<object>`: `SelectionChanged`

- `PreviousSelection`–选定内容更改之前选定的项的列表。
- `CurrentSelection`–选定内容更改后所选择的项的列表。

## <a name="single-selection"></a>单项选择

当属性设置为`Single`时, [`CollectionView`](xref:Xamarin.Forms.CollectionView)可以选择中的单个项。 [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) 选择项时, [`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem)属性将设置为选定项的值。 此属性发生更改时, [`SelectionChangedCommand`](xref:Xamarin.Forms.SelectableItemsView.SelectionChangedCommand)将执行 (将的值[`SelectionChangedCommandParameter`](xref:Xamarin.Forms.SelectableItemsView.SelectionChangedCommandParameter)传递到`ICommand`), 并[`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged)触发事件。

下面的 XAML 示例显示了[`CollectionView`](xref:Xamarin.Forms.CollectionView)一个可响应单项选择的:

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

在此代码示例中, `OnCollectionViewSelectionChanged`事件处理程序在事件激发[`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged)时执行, 事件处理程序检索之前选择的项和当前选定项:

```csharp
void OnCollectionViewSelectionChanged(object sender, SelectionChangedEventArgs e)
{
    string previous = (e.PreviousSelection.FirstOrDefault() as Monkey)?.Name;
    string current = (e.CurrentSelection.FirstOrDefault() as Monkey)?.Name;
    ...
}
```

> [!IMPORTANT]
> 事件可由[`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode)更改属性后发生的更改触发。 [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged)

以下屏幕截图显示了中的[`CollectionView`](xref:Xamarin.Forms.CollectionView)单项选择:

带有单项选择的[ ![CollectionView 垂直列表的屏幕截图, 在 IOS 和 Android](selection-images/single-selection.png "CollectionView 垂直列表中选择了单项")](selection-images/single-selection-large.png#lightbox "带有单项选择的 CollectionView 垂直列表")

## <a name="multiple-selection"></a>多重选择

当属性设置为`Multiple`时, 可以选择中的[`CollectionView`](xref:Xamarin.Forms.CollectionView)多个项。 [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) 选择项时, [`SelectedItems`](xref:Xamarin.Forms.SelectableItemsView.SelectedItems)属性将设置为所选的项。 此属性发生更改时, [`SelectionChangedCommand`](xref:Xamarin.Forms.SelectableItemsView.SelectionChangedCommand)将执行 (将的值[`SelectionChangedCommandParameter`](xref:Xamarin.Forms.SelectableItemsView.SelectionChangedCommandParameter)传递到`ICommand`), 并[`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged)触发事件。

下面的 XAML 示例显示了[`CollectionView`](xref:Xamarin.Forms.CollectionView)一个可响应多项选择的:

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

在此代码示例中, `OnCollectionViewSelectionChanged`事件处理程序在事件激发[`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged)时执行, 事件处理程序检索之前选择的项和当前选定项:

```csharp
void OnCollectionViewSelectionChanged(object sender, SelectionChangedEventArgs e)
{
    var previous = e.PreviousSelection;
    var current = e.CurrentSelection;
    ...
}
```

> [!IMPORTANT]
> 事件可由[`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode)更改属性后发生的更改触发。 [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged)

以下屏幕截图显示了中的[`CollectionView`](xref:Xamarin.Forms.CollectionView)多项选择:

具有多个[![选定内容的 CollectionView 垂直列表的屏幕截图,]其中包含多个选择的 iOS 和 Android(selection-images/multiple-selection.png "CollectionView 垂直列表")](selection-images/multiple-selection-large.png#lightbox "具有多个选定内容的 CollectionView 垂直列表")

## <a name="single-pre-selection"></a>单项选择

当属性设置为`Single`时[`CollectionView`](xref:Xamarin.Forms.CollectionView) [,`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem)可以通过将属性设置为项来预先选择中的单个项。 [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) 下面的 XAML 示例显示了`CollectionView`一个预先选择单个项的:

```xaml
<CollectionView ItemsSource="{Binding Monkeys}"
                SelectionMode="Single"
                SelectedItem="{Binding SelectedMonkey}">
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
collectionView.SetBinding(SelectableItemsView.SelectedItemProperty, "SelectedMonkey");
```

> [!NOTE]
> 属性的默认绑定`TwoWay`模式为。 [`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem)

[ `SelectedItem` ](xref:Xamarin.Forms.SelectableItemsView.SelectedItem)属性数据绑定到`SelectedMonkey`已连接的视图模型，这是类型的属性`Monkey`。 默认情况下, `TwoWay`使用绑定, 以便在用户更改所选的项时, `SelectedMonkey`属性的值将设置为选定`Monkey`的对象。 在类中定义属性, 并将其设置为`Monkeys`集合的第四项: `SelectedMonkey` `MonkeysViewModel`

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

因此, 当[`CollectionView`](xref:Xamarin.Forms.CollectionView)显示时, 将预先选择列表中的第四项:

带有单项选择的[ ![CollectionView 垂直列表的屏幕截图,](selection-images/single-pre-selection.png "") ]其中包含单项选择(selection-images/single-pre-selection-large.png#lightbox "带有单项选择的 CollectionView 垂直列表")

## <a name="multiple-pre-selection"></a>多个预选择

当属性设置为`Multiple`时, 可以预先选择中的[`CollectionView`](xref:Xamarin.Forms.CollectionView)多个项。 [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) 下面的 XAML 示例显示了`CollectionView`一个, 它将启用多个项的预选择:

```xaml
<CollectionView x:Name="collectionView"
                ItemsSource="{Binding Monkeys}"
                SelectionMode="Multiple"
                SelectedItems="{Binding SelectedMonkeys}">
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
collectionView.SetBinding(SelectableItemsView.SelectedItemsProperty, "SelectedMonkeys");
```

> [!NOTE]
> 属性的默认绑定`OneWay`模式为。 [`SelectedItems`](xref:Xamarin.Forms.SelectableItemsView.SelectedItems)

[ `SelectedItems` ](xref:Xamarin.Forms.SelectableItemsView.SelectedItems)属性数据绑定到`SelectedMonkeys`已连接的视图模型，这是类型的属性`ObservableCollection<object>`。 在类中`Monkeys`定义`SelectedMonkeys` `MonkeysViewModel`属性, 并将其设置为集合中的第二、第四和第五项:

```csharp
namespace CollectionViewDemos.ViewModels
{
    public class MonkeysViewModel : INotifyPropertyChanged
    {
        ...
        ObservableCollection<object> selectedMonkeys;
        public ObservableCollection<object> SelectedMonkeys
        {
            get
            {
                return selectedMonkeys;
            }
            set
            {
                if (selectedMonkeys != value)
                {
                    selectedMonkeys = value;
                }
            }
        }

        public MonkeysViewModel()
        {
            ...
            SelectedMonkeys = new ObservableCollection<object>()
            {
                Monkeys[1], Monkeys[3], Monkeys[4]
            };
        }
        ...
    }
}
```

因此, 当[`CollectionView`](xref:Xamarin.Forms.CollectionView)显示时, 将预先选择列表中的第二个、第四个和第五个项:

具有多个预先[![选择的 CollectionView 垂直列表的屏幕截图,]其中(selection-images/multiple-pre-selection.png "包含多个预先")选择的 iOS 和 Android CollectionView 垂直列表](selection-images/multiple-pre-selection-large.png#lightbox "具有多个预选择的 CollectionView 垂直列表")

## <a name="clearing-selections"></a>清除选择

通过将[`SelectedItems`](xref:Xamarin.Forms.SelectableItemsView.SelectedItems) 和属性`null`设置为, 或将其绑定到的对象, 可以清除和属性。 [`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem)

## <a name="change-selected-item-color"></a>更改选定项的颜色

[`CollectionView`](xref:Xamarin.Forms.CollectionView)具有, 可用于启动对中`CollectionView`的选定项的视觉更改。 [`VisualState`](xref:Xamarin.Forms.VisualState) `Selected` 这`VisualState`种情况的一个常见用例是更改选定项的背景色, 如下面的 XAML 示例中所示:

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
> [`Style`](xref:Xamarin.Forms.Style) [`TargetType`](xref:Xamarin.Forms.Style.TargetType) `ItemTemplate`包含的属性值[必须为的`DataTemplate`](xref:Xamarin.Forms.DataTemplate)根元素的类型, 该类型设置为属性值。 `VisualState` `Selected`

在此示例中, [`Style.TargetType`](xref:Xamarin.Forms.Style.TargetType)属性值设置为`Grid` , 因为的根[`Grid`](xref:Xamarin.Forms.Grid)元素[`ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate)是。 `Selected` [`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor)指定在选择中[`CollectionView`](xref:Xamarin.Forms.CollectionView)的项时, 项的将设置为`LightSkyBlue`: [`VisualState`](xref:Xamarin.Forms.VisualState)

带有自定义[![单项选择颜色的 CollectionView 垂直列表屏幕截图,](selection-images/single-selection-color.png "具有自定义单项选择颜色")的 iOS 和 Android CollectionView 垂直列表](selection-images/single-selection-color-large.png#lightbox "使用自定义单项选择颜色的 CollectionView 垂直列表")

有关可视状态的详细信息, 请参阅[Xamarin。窗体可视状态管理器](~/xamarin-forms/user-interface/visual-state-manager.md)。

## <a name="disable-selection"></a>禁用选定内容

[`CollectionView`](xref:Xamarin.Forms.CollectionView)默认情况下禁用选择。 但是, 如果`CollectionView`启用了选择, 则可以通过[`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode)将属性设置为来`None`禁用它:

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

[`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem) [`CollectionView`](xref:Xamarin.Forms.CollectionView) `null` [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged)当属性设置为`None`时, 不能选择中的项, 属性将保留, 并且不会触发事件。 [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode)

> [!NOTE]
> 如果已选择某个项并且[`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode)属性从`Single`更改为`None`, [`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem)则属性将设置为`null` , 并且将使用空`CurrentSelection`属性[`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged)激发该事件.

## <a name="related-links"></a>相关链接

- [CollectionView (示例)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)
- [Xamarin. Forms 视觉对象状态管理器](~/xamarin-forms/user-interface/visual-state-manager.md)
