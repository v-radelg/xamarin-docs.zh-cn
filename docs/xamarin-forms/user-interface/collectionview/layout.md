---
title: Xamarin CollectionView 布局
description: 默认情况下, CollectionView 会在垂直列表中显示其项。 但是, 可以指定垂直和水平列表以及网格。
ms.prod: xamarin
ms.assetid: 5FE78207-1BD6-4706-91EF-B13932321FC9
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/01/2019
ms.openlocfilehash: 5fb92882f443007e5b3dd693f54e582757db1905
ms.sourcegitcommit: c6e56545eafd8ff9e540d56aba32aa6232c5315f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/02/2019
ms.locfileid: "68739022"
---
# <a name="xamarinforms-collectionview-layout"></a>Xamarin CollectionView 布局

![](~/media/shared/preview.png "此 API 当前为预发布版本")

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)

[`CollectionView`](xref:Xamarin.Forms.CollectionView)定义用于控制布局的下列属性:

- [`ItemsLayout`](xref:Xamarin.Forms.ItemsLayout)类型[`IItemsLayout`](xref:Xamarin.Forms.IItemsLayout)为的指定要使用的布局。
- [`ItemSizingStrategy`](xref:Xamarin.Forms.ItemsView.ItemSizingStrategy)类型[`ItemSizingStrategy`](xref:Xamarin.Forms.ItemSizingStrategy)为的指定要使用的项度量策略。

这些属性是由[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)对象支持的, 这意味着属性可以是数据绑定的目标。

默认情况下, [`CollectionView`](xref:Xamarin.Forms.CollectionView)会在垂直列表中显示其项。 但是, 可以使用以下任何布局:

- 垂直列表–在添加新项时垂直增长的单个列列表。
- 水平列表–在添加新项时水平增长的单个行列表。
- 垂直网格–在添加新项时垂直增长的多列网格。
- 水平网格–在添加新项时水平增长的多行网格。

可以通过将[`ItemsLayout`](xref:Xamarin.Forms.ItemsView.ItemsLayout)属性设置为[`ItemsLayout`](xref:Xamarin.Forms.ItemsLayout)从类派生的类来指定这些布局。 此类定义以下属性:

- [`Orientation`](xref:Xamarin.Forms.ItemsLayout.Orientation)类型[`ItemsLayoutOrientation`](xref:Xamarin.Forms.ItemsLayoutOrientation)为的指定添加添加项的方向[`CollectionView`](xref:Xamarin.Forms.CollectionView) 。
- [`SnapPointsAlignment`](xref:Xamarin.Forms.ItemsLayout.SnapPointsAlignment)类型[`SnapPointsAlignment`](xref:Xamarin.Forms.SnapPointsAlignment)为的指定对齐点如何与项对齐。
- [`SnapPointsType`](xref:Xamarin.Forms.ItemsLayout.SnapPointsType)类型[`SnapPointsType`](xref:Xamarin.Forms.SnapPointsType)为的指定滚动时对齐点的行为。

这些属性是由[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)对象支持的, 这意味着属性可以是数据绑定的目标。 有关对齐点的详细信息, 请参阅[Xamarin CollectionView 滚动](scrolling.md)指南中的 "[对齐点](scrolling.md#snap-points)"。

[`ItemsLayoutOrientation`](xref:Xamarin.Forms.ItemsLayoutOrientation)枚举定义以下成员:

- `Vertical`指示添加项时[将垂直扩展。`CollectionView`](xref:Xamarin.Forms.CollectionView)
- `Horizontal`指示添加项时[将水平扩展。`CollectionView`](xref:Xamarin.Forms.CollectionView)

类从类继承, 并定义一个`ItemSpacing`类型`double`为的属性, 该属性表示每个项周围的空白区域。 [`ItemsLayout`](xref:Xamarin.Forms.ItemsLayout) [`ListItemsLayout`](xref:Xamarin.Forms.ListItemsLayout) 此属性的默认值为 0, 其值必须始终大于或等于0。 类还定义静态`Vertical`和`Horizontal`成员。 `ListItemsLayout` 这些成员可以分别用来创建垂直或水平列表。 或者, 可以`ListItemsLayout`创建一个对象, 并将[`ItemsLayoutOrientation`](xref:Xamarin.Forms.ItemsLayoutOrientation)枚举成员指定为参数。

[`GridItemsLayout`](xref:Xamarin.Forms.GridItemsLayout) [类`ItemsLayout`](xref:Xamarin.Forms.ItemsLayout)从类继承, 并定义以下属性:

- `VerticalItemSpacing`, 类型`double`为, 表示每个项周围的垂直空白间距。 此属性的默认值为 0, 其值必须始终大于或等于0。
- `HorizontalItemSpacing`, 类型`double`为, 表示每个项周围的水平空白空间。 此属性的默认值为 0, 其值必须始终大于或等于0。
- `Span`, 类型`int`为, 表示要在网格中显示的列数或行数。 此属性的默认值为 1, 并且它的值必须始终大于或等于1。

这些属性是由[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)对象支持的, 这意味着属性可以是数据绑定的目标。

> [!NOTE]
> [`CollectionView`](xref:Xamarin.Forms.CollectionView)使用本机布局引擎执行布局。

## <a name="vertical-list"></a>垂直列表

默认情况下[`CollectionView`](xref:Xamarin.Forms.CollectionView) , 将以垂直列表布局显示其项。 因此, 不需要将[`ItemsLayout`](xref:Xamarin.Forms.ItemsView.ItemsLayout)属性设置为使用此布局:

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

但是, 为了完整性, [`CollectionView`](xref:Xamarin.Forms.CollectionView)可以通过将其[`ItemsLayout`](xref:Xamarin.Forms.ItemsView.ItemsLayout)属性设置为`VerticalList`来将设置为在垂直列表中显示其项:

```xaml
<CollectionView ItemsSource="{Binding Monkeys}"
                ItemsLayout="VerticalList">
    ...
</CollectionView>
```

此外, [`ItemsLayout`](xref:Xamarin.Forms.ItemsView.ItemsLayout)也可以通过将属性设置为[`ListItemsLayout`](xref:Xamarin.Forms.ListItemsLayout)类的对象, 并将[`ItemsLayoutOrientation`](xref:Xamarin.Forms.ItemsLayoutOrientation)枚举成员指定`Vertical`为自变量来完成此操作:

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

等效 C# 代码如下：

```csharp
CollectionView collectionView = new CollectionView
{
    ...
    ItemsLayout = ListItemsLayout.Vertical
};
```

这会生成单个列列表, 该列表将在添加新项时垂直增长:

[ ![CollectionView 垂直列表布局的屏幕截图, 适用于 IOS 和 Android](layout-images/vertical-list.png "CollectionView 垂直列表布局")](layout-images/vertical-list-large.png#lightbox "CollectionView 垂直列表布局")

## <a name="horizontal-list"></a>水平列表

[`CollectionView`](xref:Xamarin.Forms.CollectionView)可以通过将其[`ItemsLayout`](xref:Xamarin.Forms.ItemsView.ItemsLayout)属性设置为, `HorizontalList`在水平列表中显示其项:

```xaml
<CollectionView ItemsSource="{Binding Monkeys}"
                ItemsLayout="HorizontalList">
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

此外, 也[`ItemsLayout`](xref:Xamarin.Forms.ItemsView.ItemsLayout)可以通过将属性设置[`ListItemsLayout`](xref:Xamarin.Forms.ListItemsLayout)为对象, 并将[`ItemsLayoutOrientation`](xref:Xamarin.Forms.ItemsLayoutOrientation)枚举成员指定`Horizontal`为参数来完成此操作:

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

等效 C# 代码如下：

```csharp
CollectionView collectionView = new CollectionView
{
    ...
    ItemsLayout = ListItemsLayout.Horizontal
};
```

这会生成单个行列表, 该列表在添加新项时水平增长:

[ ![CollectionView 水平列表布局的屏幕截图, 适用于 IOS 和 Android](layout-images/horizontal-list.png "CollectionView 水平列表布局")](layout-images/horizontal-list-large.png#lightbox "CollectionView 水平列表布局")

## <a name="vertical-grid"></a>垂直网格

[`CollectionView`](xref:Xamarin.Forms.CollectionView)可以通过将[`ItemsLayout`](xref:Xamarin.Forms.ItemsView.ItemsLayout)其属性[`GridItemsLayout`](xref:Xamarin.Forms.GridItemsLayout)设置为对象[`Orientation`](xref:Xamarin.Forms.ItemsLayout.Orientation) (其属性设置为`Vertical`), 将其项显示在垂直网格中:

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

等效 C# 代码如下：

```csharp
CollectionView collectionView = new CollectionView
{
    ...
    ItemsLayout = new GridItemsLayout(2, ItemsLayoutOrientation.Vertical)
};
```

默认情况下, 垂直[`GridItemsLayout`](xref:Xamarin.Forms.GridItemsLayout)将显示单个列中的项。 但是, 此示例将`GridItemsLayout.Span`属性设置为2。 这会生成一个两列网格, 这会在添加新项时垂直增长:

[ ![CollectionView 垂直网格布局的屏幕截图, 适用于 IOS 和 Android](layout-images/vertical-grid.png "CollectionView 垂直网格布局")](layout-images/vertical-grid-large.png#lightbox "CollectionView 垂直网格布局")

## <a name="horizontal-grid"></a>水平网格

[`CollectionView`](xref:Xamarin.Forms.CollectionView)可以通过将[`ItemsLayout`](xref:Xamarin.Forms.ItemsView.ItemsLayout)其属性[`GridItemsLayout`](xref:Xamarin.Forms.GridItemsLayout)设置为对象[`Orientation`](xref:Xamarin.Forms.ItemsLayout.Orientation) (其属性设置为`Horizontal`), 将其项显示在水平网格中:

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

等效 C# 代码如下：

```csharp
CollectionView collectionView = new CollectionView
{
    ...
    ItemsLayout = new GridItemsLayout(4, ItemsLayoutOrientation.Horizontal)
};
```

默认情况下, 水平[`GridItemsLayout`](xref:Xamarin.Forms.GridItemsLayout)将在单个行中显示项。 但是, 此示例将`GridItemsLayout.Span`属性设置为4。 这会导致四行网格, 这会在添加新项时水平增长:

[ ![CollectionView 水平网格布局的屏幕截图, 适用于 IOS 和 Android](layout-images/horizontal-grid.png "CollectionView 水平网格布局")](layout-images/horizontal-grid-large.png#lightbox "CollectionView 水平网格布局")

## <a name="item-spacing"></a>项间距

默认情况下, 中的[`CollectionView`](xref:Xamarin.Forms.CollectionView)每一项周围没有任何空白区域。 可以通过在使用`CollectionView`的项布局上设置属性来更改此行为。

[`ItemsLayout`](xref:Xamarin.Forms.ItemsView.ItemsLayout) [`ListItemsLayout`](xref:Xamarin.Forms.ListItemsLayout) `ListItemsLayout.ItemSpacing`如果将其属性`double`设置为对象, 则可以将属性设置为一个值, 该值表示每个项周围的空白区域: [`CollectionView`](xref:Xamarin.Forms.CollectionView)

```xaml
<CollectionView ItemsSource="{Binding Monkeys}">
    <CollectionView.ItemsLayout>
        <ListItemsLayout ItemSpacing="20">
            <x:Arguments>
                <ItemsLayoutOrientation>Vertical</ItemsLayoutOrientation>    
            </x:Arguments>
        </ListItemsLayout>
    </CollectionView.ItemsLayout>
    ...
</CollectionView>
```

> [!NOTE]
> `ListItemsLayout.ItemSpacing`属性具有一个验证回叫集, 它可确保属性的值始终大于或等于0。

等效 C# 代码如下：

```csharp
CollectionView collectionView = new CollectionView
{
    ...
    ItemsLayout = new ListItemsLayout(ItemsLayoutOrientation.Vertical)
    {
        ItemSpacing = 20
    }
};
```

此代码将生成垂直的单列列表, 每个项周围的间距为 20:

[![在 IOS 和 Android](layout-images/vertical-list-spacing.png "CollectionView 项间距")上具有项间距的 CollectionView 的屏幕截图](layout-images/vertical-list-spacing-large.png#lightbox "CollectionView 项间距")

[`GridItemsLayout`](xref:Xamarin.Forms.GridItemsLayout) `double` `GridItemsLayout.VerticalItemSpacing`如果将其[`ItemsLayout`](xref:Xamarin.Forms.ItemsView.ItemsLayout)属性`GridItemsLayout.HorizontalItemSpacing`设置为对象, 则和属性可以设置为在每个项周围垂直和水平表示空白空间的值: [`CollectionView`](xref:Xamarin.Forms.CollectionView)

```xaml
<CollectionView ItemsSource="{Binding Monkeys}">
    <CollectionView.ItemsLayout>
       <GridItemsLayout Orientation="Vertical"
                        Span="2"
                        VerticalItemSpacing="20"
                        HorizontalItemSpacing="30" />
    </CollectionView.ItemsLayout>
    ...
</CollectionView>
```

> [!NOTE]
> `GridItemsLayout.VerticalItemSpacing` 和`GridItemsLayout.HorizontalItemSpacing`属性设置了验证回调, 这可确保属性的值始终大于或等于0。

等效 C# 代码如下：

```csharp
CollectionView collectionView = new CollectionView
{
    ...
    ItemsLayout = new GridItemsLayout(2, ItemsLayoutOrientation.Vertical)
    {
        VerticalItemSpacing = 20,
        HorizontalItemSpacing = 30
    }
};
```

此代码生成垂直的两列网格, 每个项周围的垂直间距为 20, 每个项周围的水平间距为 30:

[![在 IOS 和 Android](layout-images/vertical-grid-spacing.png "CollectionView 项间距")上具有项间距的 CollectionView 的屏幕截图](layout-images/vertical-grid-spacing-large.png#lightbox "CollectionView 项间距")

## <a name="item-sizing"></a>项大小调整

默认情况下, 中[`CollectionView`](xref:Xamarin.Forms.CollectionView)的每一项都是单独测量和调整大小的, 前提是[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)中的 UI 元素不指定固定的大小。 此行为可以更改, 由[`CollectionView.ItemSizingStrategy`](xref:Xamarin.Forms.ItemsView.ItemSizingStrategy)属性值指定。 此属性值可以设置为[`ItemSizingStrategy`](xref:Xamarin.Forms.ItemSizingStrategy)枚举成员之一:

- `MeasureAllItems`–每个项都单独进行度量。 这是默认值。
- `MeasureFirstItem`–仅度量第一项, 并为所有后续项提供与第一个项相同的大小。

> [!IMPORTANT]
> 当`MeasureFirstItem`在项大小旨在跨所有项的情况下使用时, 大小调整策略会提高性能。

下面的代码示例演示如何设置[`ItemSizingStrategy`](xref:Xamarin.Forms.ItemsView.ItemSizingStrategy)属性:

```xaml
<CollectionView ...
                ItemSizingStrategy="MeasureFirstItem">
    ...
</CollectionView>
```

等效 C# 代码如下：

```csharp
CollectionView collectionView = new CollectionView
{
    ...
    ItemSizingStrategy = ItemSizingStrategy.MeasureFirstItem
};
```

## <a name="dynamic-resizing-of-items"></a>动态调整项的大小

中[`CollectionView`](xref:Xamarin.Forms.CollectionView)的项可以在运行时通过更改[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)中的元素的与布局相关的属性, 在运行时动态调整大小。 例如, 下面的代码示例更改[`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) [`Image`](xref:Xamarin.Forms.Image)对象的和[`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest)属性:

```csharp
void OnImageTapped(object sender, EventArgs e)
{
    Image image = sender as Image;
    image.HeightRequest = image.WidthRequest = image.HeightRequest.Equals(60) ? 100 : 60;
}
```

此`OnImageTapped`事件处理程序是为响应[`Image`](xref:Xamarin.Forms.Image)正在点击的对象而执行的, 它更改图像的尺寸以便于查看:

[![具有动态项大小的 CollectionView 的屏幕截图, 适用于 IOS 和 Android](layout-images/runtime-resizing.png "CollectionView 动态项大小")](layout-images/runtime-resizing-large.png#lightbox "CollectionView 动态项大小调整")

## <a name="right-to-left-layout"></a>从右到左布局

[`CollectionView`](xref:Xamarin.Forms.CollectionView)可以通过将其[`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection)属性设置为, [`RightToLeft`](xref:Xamarin.Forms.FlowDirection.RightToLeft)从右到左的流方向布局其内容。 但是, `FlowDirection`在理想情况下, 应在页面或根布局上设置属性, 这会使页面或根布局中的所有元素响应流方向:

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

具有父级[`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection)的元素的默认值为。 [`MatchParent`](xref:Xamarin.Forms.FlowDirection.MatchParent) [`CollectionView`](xref:Xamarin.Forms.CollectionView)因此, [`ContentPage`](xref:Xamarin.Forms.ContentPage)从`FlowDirection` `FlowDirection`继承属性值[,后者又从继承属性值。`StackLayout`](xref:Xamarin.Forms.StackLayout) 这会导致从右到左的布局, 如以下屏幕截图所示:

[ ![CollectionView 从右到左垂直列表布局的屏幕截图, 适用于 IOS 和 Android](layout-images/vertical-list-rtl.png "CollectionView 从右到左垂直列表布局")](layout-images/vertical-list-rtl-large.png#lightbox "CollectionView 从右到左垂直列表布局")

有关流方向的详细信息, 请参阅[从右到左的本地化](~/xamarin-forms/app-fundamentals/localization/right-to-left.md)。

## <a name="related-links"></a>相关链接

- [CollectionView (示例)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)
- [从右到左的本地化](~/xamarin-forms/app-fundamentals/localization/right-to-left.md)
- [Xamarin CollectionView 滚动](scrolling.md)
