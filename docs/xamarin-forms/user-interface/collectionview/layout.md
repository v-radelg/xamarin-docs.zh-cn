---
title: 指定 Xamarin.Forms 之间导航布局
description: 默认情况下，CollectionView 将各个项显示垂直列表中。 但是，可以指定垂直和水平列表和网格。
ms.prod: xamarin
ms.assetid: 5FE78207-1BD6-4706-91EF-B13932321FC9
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/15/2019
ms.openlocfilehash: 8ed365ed41ac31c66d41f1a32a7a16929cdc6770
ms.sourcegitcommit: 5d4e6677224971e2bc0268f405d192d0358c74b8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/21/2019
ms.locfileid: "58329782"
---
# <a name="specify-xamarinforms-collectionview-layout"></a>指定 Xamarin.Forms 之间导航布局

![预览](~/media/shared/preview.png)

[![下载示例](~/media/shared/download.png)下载示例](https://github.com/xamarin/xamarin-forms-samples/tree/forms40/UserInterface/CollectionViewDemos/)

> [!IMPORTANT]
> `CollectionView`当前为预览版，并且缺少一些其计划的功能。 此外，API 可能会更改为实现已完成。

`CollectionView` 定义用于控制布局的以下属性：

- `ItemsLayout`类型的`IItemsLayout`，指定要使用的布局。
- `ItemSizingStrategy`类型的`ItemSizingStrategy`，指定要使用的项度量值策略。

这些属性受到[ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty)对象，这意味着，属性可以是数据绑定的目标。

默认情况下，`CollectionView`将各个项显示垂直列表中。 但是，可以使用任何下列布局：

- 垂直列表 – 垂直增长时添加新项目时的单个列列表。
- 水平列表-水平变宽，添加新项目时的单个行列表。
- 垂直网格 – 垂直增长时添加新项目时的多列网格。
- 水平网格 – 水平变宽，添加新项目时的多行网格。

可以通过设置指定这些布局`ItemsLayout`属性类，该类派生`ItemsLayout`类。 此类定义以下属性：

- `Orientation`类型的`ItemsLayoutOrientation`，在其中指定的方向`CollectionView`扩展添加项目时。
- `SnapPointsAlignment`类型的`SnapPointsAlignment`，指定管理单元点包含项的对齐方式。
- `SnapPointsType`类型的`SnapPointsType`，滚动时显示指定的管理点的行为。

这些属性受到[ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty)对象，这意味着，属性可以是数据绑定的目标。 有关管理点的详细信息，请参阅[对齐点](scrolling.md#snap-points)中[向项滚动到视图](scrolling.md)指南。

`ItemsLayoutOrientation`枚举定义以下成员：

- `Vertical` 指示`CollectionView`添加项目时，将垂直展开。
- `Horizontal` 指示`CollectionView`添加项目时，将水平展开。

`ListItemsLayout`类继承自`ItemsLayout`类，并定义静态`VerticalList`和`HorizontalList`成员。 这些成员可用于分别创建垂直或水平列表。 或者，`ListItemsLayout`可以创建对象，指定`ItemsLayoutOrientation`作为自变量的枚举成员。

`GridItemsLayout`类继承自`ItemsLayout`类，并定义`Span`类型的属性， `int`，它表示要在网格中显示的行或列数。 默认值`Span`属性为 1，且其值必须始终为大于或等于 1。

> [!NOTE]
> `CollectionView` 使用本机布局引擎执行布局。

## <a name="vertical-list"></a>垂直列表

默认情况下，`CollectionView`将各个项显示垂直列表布局中。 因此，不需要设置`ItemsLayout`属性，以使用此布局：

```xaml
<CollectionView ItemsSource="{Binding Monkeys}">
    <CollectionView.ItemTemplate>
        <DataTemplate>
            <Grid Padding="10">
                <Grid.RowDefinitions>
                    <RowDefinition Height="Auto" />
                    <RowDefinition Height="Auto" />
                </Grid.RowDefinitions>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="Auto" />
                    <ColumnDefinition Width="Auto" />
                </Grid.ColumnDefinitions>
                <Image Grid.RowSpan="2"
                       Source="{Binding ImageUrl}"
                       Aspect="AspectFill"
                       HeightRequest="60"
                       WidthRequest="60" />
                <Label Grid.Column="1"
                       Text="{Binding Name}"
                       FontAttributes="Bold" />
                <Label Grid.Row="1"
                       Grid.Column="1"
                       Text="{Binding Location}"
                       FontAttributes="Italic"
                       VerticalOptions="End" />
            </Grid>
        </DataTemplate>
    </CollectionView.ItemTemplate>
</CollectionView>
```

但是，出于完整性的考虑，`CollectionView`可以设置为各个项显示垂直列表中，通过设置其`ItemsLayout`对静态`ListItemsLayout.VerticalList`成员：

```xaml
<CollectionView ItemsSource="{Binding Monkeys}"
                ItemsLayout="{x:Static ListItemsLayout.VerticalList}">
    ...
</CollectionView>
```

或者，也可以通过设置`ItemsLayout`指向的对象的属性`ListItemsLayout`类中，指定`Vertical``ItemsLayoutOrientation`作为自变量的枚举成员：

```xaml
<CollectionView ItemsSource="{Binding Monkeys}">
    <CollectionView.ItemsLayout>
        <ListItemsLayout>
            <x:Arguments>
                <ItemsLayoutOrientation>Vertical</ItemsLayoutOrientation>    
            </x:Arguments>
        </ListItemsLayout>
    </CollectionView.ItemsLayout>
    ...
</CollectionView>
```

等效的 C# 代码是：

```csharp
CollectionView collectionView = new CollectionView
{
    ...
    ItemsLayout = ListItemsLayout.VerticalList
};
```

这会导致单个列列表，如添加新项垂直增长时：

[![CollectionView 垂直列表布局，在 iOS 和 Android 上的屏幕截图](layout-images/vertical-list.png "CollectionView 垂直列表布局")](layout-images/vertical-list-large.png#lightbox "CollectionView 垂直列表布局")

## <a name="horizontal-list"></a>水平列表

`CollectionView` 可以通过设置水平列表中显示其项及其`ItemsLayout`属性设置为静态`ListItemsLayout.HorizontalList`成员：

```xaml
<CollectionView ItemsSource="{Binding Monkeys}"
                ItemsLayout="{x:Static ListItemsLayout.HorizontalList}">
    <CollectionView.ItemTemplate>
        <DataTemplate>
            <Grid Padding="10">
                <Grid.RowDefinitions>
                    <RowDefinition Height="35" />
                    <RowDefinition Height="35" />
                </Grid.RowDefinitions>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="70" />
                    <ColumnDefinition Width="140" />
                </Grid.ColumnDefinitions>
                <Image Grid.RowSpan="2"
                       Source="{Binding ImageUrl}"
                       Aspect="AspectFill"
                       HeightRequest="60"
                       WidthRequest="60" />
                <Label Grid.Column="1"
                       Text="{Binding Name}"
                       FontAttributes="Bold"
                       LineBreakMode="TailTruncation" />
                <Label Grid.Row="1"
                       Grid.Column="1"
                       Text="{Binding Location}"
                       LineBreakMode="TailTruncation"
                       FontAttributes="Italic"
                       VerticalOptions="End" />
            </Grid>
        </DataTemplate>
    </CollectionView.ItemTemplate>
</CollectionView>
```

或者，也可以通过设置`ItemsLayout`属性设置为`ListItemsLayout`对象，指定`Horizontal``ItemsLayoutOrientation`作为自变量的枚举成员：

```xaml
<CollectionView ItemsSource="{Binding Monkeys}">
    <CollectionView.ItemsLayout>
        <ListItemsLayout>
            <x:Arguments>
                <ItemsLayoutOrientation>Horizontal</ItemsLayoutOrientation>    
            </x:Arguments>
        </ListItemsLayout>
    </CollectionView.ItemsLayout>
    ...
</CollectionView>
```

等效的 C# 代码是：

```csharp
CollectionView collectionView = new CollectionView
{
    ...
    ItemsLayout = ListItemsLayout.HorizontalList
};
```

这会导致单个行列表，水平变宽，如添加新项：

[![CollectionView 水平列表布局，在 iOS 和 Android 上的屏幕截图](layout-images/horizontal-list.png "CollectionView 水平列表布局")](layout-images/horizontal-list-large.png#lightbox "CollectionView 水平列表布局")

## <a name="vertical-grid"></a>垂直网格

`CollectionView` 可以通过设置垂直网格中显示其项及其`ItemsLayout`属性设置为`GridItemsLayout`对象，其`Orientation`属性设置为`Vertical`:

```xaml
<CollectionView ItemsSource="{Binding Monkeys}">
    <CollectionView.ItemsLayout>
       <GridItemsLayout Orientation="Vertical"
                        Span="2" />
    </CollectionView.ItemsLayout>
    <CollectionView.ItemTemplate>
        <DataTemplate>
            <Grid Padding="10">
                <Grid.RowDefinitions>
                    <RowDefinition Height="35" />
                    <RowDefinition Height="35" />
                </Grid.RowDefinitions>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="70" />
                    <ColumnDefinition Width="80" />
                </Grid.ColumnDefinitions>
                <Image Grid.RowSpan="2"
                       Source="{Binding ImageUrl}"
                       Aspect="AspectFill"
                       HeightRequest="60"
                       WidthRequest="60" />
                <Label Grid.Column="1"
                       Text="{Binding Name}"
                       FontAttributes="Bold"
                       LineBreakMode="TailTruncation" />
                <Label Grid.Row="1"
                       Grid.Column="1"
                       Text="{Binding Location}"
                       LineBreakMode="TailTruncation"
                       FontAttributes="Italic"
                       VerticalOptions="End" />
            </Grid>
        </DataTemplate>
    </CollectionView.ItemTemplate>
</CollectionView>
```

等效的 C# 代码是：

```csharp
CollectionView collectionView = new CollectionView
{
    ...
    ItemsLayout = new GridItemsLayout(2, ItemsLayoutOrientation.Vertical)
};
```

默认情况下，垂直`GridItemsLayout`将在单个列中显示项。 但是，此示例设置`GridItemsLayout.Span`属性设置为 2。 这会导致两个列网格，如添加新项垂直增长时：

[![CollectionView 垂直网格布局，在 iOS 和 Android 上的屏幕截图](layout-images/vertical-grid.png "CollectionView 垂直网格布局")](layout-images/vertical-grid-large.png#lightbox "CollectionView 垂直网格布局")

## <a name="horizontal-grid"></a>水平网格

`CollectionView` 可以通过设置水平网格中显示其项及其`ItemsLayout`属性设置为`GridItemsLayout`对象，其`Orientation`属性设置为`Horizontal`:

```xaml
<CollectionView ItemsSource="{Binding Monkeys}">
    <CollectionView.ItemsLayout>
       <GridItemsLayout Orientation="Horizontal"
                        Span="4" />
    </CollectionView.ItemsLayout>
    <CollectionView.ItemTemplate>
        <DataTemplate>
            <Grid Padding="10">
                <Grid.RowDefinitions>
                    <RowDefinition Height="35" />
                    <RowDefinition Height="35" />
                </Grid.RowDefinitions>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="70" />
                    <ColumnDefinition Width="140" />
                </Grid.ColumnDefinitions>
                <Image Grid.RowSpan="2"
                       Source="{Binding ImageUrl}"
                       Aspect="AspectFill"
                       HeightRequest="60"
                       WidthRequest="60" />
                <Label Grid.Column="1"
                       Text="{Binding Name}"
                       FontAttributes="Bold"
                       LineBreakMode="TailTruncation" />
                <Label Grid.Row="1"
                       Grid.Column="1"
                       Text="{Binding Location}"
                       LineBreakMode="TailTruncation"
                       FontAttributes="Italic"
                       VerticalOptions="End" />
            </Grid>
        </DataTemplate>
    </CollectionView.ItemTemplate>
</CollectionView>
```

等效的 C# 代码是：

```csharp
CollectionView collectionView = new CollectionView
{
    ...
    ItemsLayout = new GridItemsLayout(4, ItemsLayoutOrientation.Horizontal)
};
```

默认情况下，水平`GridItemsLayout`将在单个行中显示项。 但是，此示例设置`GridItemsLayout.Span`属性设置为 4。 这会导致水平变宽，添加新项目时的四行网格：

[![CollectionView 水平网格布局，在 iOS 和 Android 上的屏幕截图](layout-images/horizontal-grid.png "CollectionView 水平网格布局")](layout-images/horizontal-grid-large.png#lightbox "CollectionView 水平网格布局")

## <a name="right-to-left-layout"></a>从右到左布局

`CollectionView` 从右到左流动方向中的其内容的布局可能通过设置其[ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection)属性设置为[ `RightToLeft` ](xref:Xamarin.Forms.FlowDirection.RightToLeft)。 但是，`FlowDirection`属性应在理想情况下设置页或根布局，这会导致页上或根布局中的所有元素，以响应流方向：

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="CollectionViewDemos.Views.VerticalListFlowDirectionPage"
             Title="Vertical list (RTL FlowDirection)"
             FlowDirection="RightToLeft">
    <StackLayout Margin="20">
        <CollectionView ItemsSource="{Binding Monkeys}">
            ...
        </CollectionView>
    </StackLayout>
</ContentPage>
```

默认值[ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection)使用的父元素[ `MatchParent` ](xref:Xamarin.Forms.FlowDirection.MatchParent)。 因此，`CollectionView`继承`FlowDirection`属性值从[ `StackLayout` ](xref:Xamarin.Forms.StackLayout)，这又继承`FlowDirection`属性值从[ `ContentPage` ](xref:Xamarin.Forms.ContentPage). 这会导致从右到左布局中的以下屏幕截图所示：

[![CollectionView 垂直列表中从右到左布局，在 iOS 和 Android 上的屏幕截图](layout-images/vertical-list-rtl.png "CollectionView 垂直列表中从右到左布局")](layout-images/vertical-list-rtl-large.png#lightbox "CollectionView 从右到左垂直列表布局")

有关流方向的详细信息，请参阅[从右到左本地化](~/xamarin-forms/app-fundamentals/localization/right-to-left.md)。

## <a name="item-sizing"></a>项大小调整

默认情况下，每个项目中`CollectionView`分别是测量和调整大小、 提供程序中的 UI 元素[ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate)未指定固定的大小。 指定此行为，可以更改`CollectionView.ItemSizingStrategy`属性值。 此属性的值可以设置为其中一个`ItemSizingStrategy`枚举成员：

- `MeasureAllItems` – 每个项分别进行测量。 这是默认值。
- `MeasureFirstItem` – 仅第一项度量单位，与所提供的第一项的大小相同的所有后续项。

> [!IMPORTANT]
> `MeasureFirstItem`大小调整策略应在项大小用于统一跨所有项，并且将导致更高的性能情况下使用。

下面的代码示例显示了设置`ItemSizingStrategy`属性：

```xaml
<CollectionView ...
                ItemSizingStrategy="MeasureFirstItem">
    ...
</CollectionView>
```

等效的 C# 代码是：

```csharp
CollectionView collectionView = new CollectionView
{
    ...
    ItemSizingStrategy = ItemSizingStrategy.MeasureFirstItem
};
```

## <a name="related-links"></a>相关链接

- [CollectionView （示例）](https://github.com/xamarin/xamarin-forms-samples/tree/forms40/UserInterface/CollectionViewDemos/)
- [从右到左本地化](~/xamarin-forms/app-fundamentals/localization/right-to-left.md)
- [向项滚动到视图](scrolling.md)
