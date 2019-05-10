---
title: Xamarin.Forms CollectionView EmptyView
description: 在之间导航，可以向用户提供反馈，当没有数据可用于显示时指定空的视图。 一个字符串、 视图或多个视图，可以是空的视图。
ms.prod: xamarin
ms.assetid: 6CEBCFE6-5577-4F68-9709-431062609153
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/06/2019
ms.openlocfilehash: 78e9ddcb1d9dd91dadea94016b206867ac9508e6
ms.sourcegitcommit: 9d90a26cbe13ebd106f55ba4a5445f28d9c18a1a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2019
ms.locfileid: "65048199"
---
# <a name="xamarinforms-collectionview-emptyview"></a>Xamarin.Forms CollectionView EmptyView

![](~/media/shared/preview.png "此 API 是当前预发布版本")

[![下载示例](~/media/shared/download.png)下载示例](https://github.com/xamarin/xamarin-forms-samples/tree/forms40/UserInterface/CollectionViewDemos/)

`CollectionView` 定义可用于没有要显示的数据时提供用户反馈的以下属性：

- `EmptyView`类型的`object`，字符串、 绑定或视图将显示何时`ItemsSource`属性是`null`，或指定集合`ItemsSource`属性是`null`或为空。 默认值为 `null`。
- `EmptyViewTemplate`类型的[ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate)，用于设置格式指定的模板`EmptyView`。 默认值为 `null`。

这些属性受到[ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty)对象，这意味着，属性可以是数据绑定的目标。

设置主要使用方案`EmptyView`属性显示时的筛选操作的用户反馈`CollectionView`从 web 服务检索数据的同时会产生任何数据，并显示用户反馈。

> [!NOTE]
> `EmptyView`属性可以设置为包含交互式内容，如果所需的视图。

有关数据模板的详细信息，请参阅 [Xamarin.Forms 数据模板](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)。

## <a name="display-a-string-when-data-is-unavailable"></a>数据不可用时显示的字符串

`EmptyView`属性可以设置为一个字符串，将显示何时`ItemsSource`属性是`null`，或指定集合`ItemsSource`属性是`null`或为空。 以下 XAML 演示此方案的示例：

```xaml
<CollectionView ItemsSource="{Binding EmptyMonkeys}"
                EmptyView="No items to display" />
```

等效的 C# 代码是：

```csharp
CollectionView collectionView = new CollectionView
{
    EmptyView = "No items to display"
};
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "EmptyMonkeys");
```

结果是，由于数据绑定集合`null`，该字符串设置为`EmptyView`显示属性值：

[![IOS 和 Android 上的文本为空视图之间导航垂直列表的屏幕截图](emptyview-images/null-itemssource.png "CollectionView 垂直文本空视图列表")](emptyview-images/null-itemssource-large.png#lightbox "CollectionView 具有空的文本的垂直列表视图")

## <a name="display-views-when-data-is-unavailable"></a>数据不可用时显示视图

`EmptyView`属性可以设置为视图中，将显示何时`ItemsSource`属性是`null`，或指定集合`ItemsSource`属性是`null`或为空。 这可以是单个视图中或包含多个子视图的视图。 下面的 XAML 示例演示`EmptyView`属性设置为包含多个子视图的视图：

```xaml
<StackLayout Margin="20">
    <SearchBar x:Name="searchBar"
               SearchCommand="{Binding FilterCommand}"
               SearchCommandParameter="{Binding Source={x:Reference searchBar}, Path=Text}"
               Placeholder="Filter" />
    <CollectionView ItemsSource="{Binding Monkeys}">
        <CollectionView.ItemTemplate>
            <DataTemplate>
                ...
            </DataTemplate>
        </CollectionView.ItemTemplate>
        <CollectionView.EmptyView>
            <StackLayout>
                <Label Text="No results matched your filter."
                       Margin="10,25,10,10"
                       FontAttributes="Bold"
                       FontSize="18"
                       HorizontalOptions="Fill"
                       HorizontalTextAlignment="Center" />
                <Label Text="Try a broader filter?"
                       FontAttributes="Italic"
                       FontSize="12"
                       HorizontalOptions="Fill"
                       HorizontalTextAlignment="Center" />
            </StackLayout>
        </CollectionView.EmptyView>
    </CollectionView>
</StackLayout>
```

等效的 C# 代码是：

```csharp
SearchBar searchBar = new SearchBar { ... };
CollectionView collectionView = new CollectionView
{
    EmptyView = new StackLayout
    {
        Children =
        {
            new Label { Text = "No results matched your filter.", ... },
            new Label { Text = "Try a broader filter?", ... }
        }
    }
};
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
```

当[ `SearchBar` ](xref:Xamarin.Forms.SearchBar)执行`FilterCommand`，通过显示的集合`CollectionView`进行筛选的搜索词会存储在[ `SearchBar.Text` ](xref:Xamarin.Forms.SearchBar.Text)属性。 如果为筛选操作会不产生任何数据， [ `StackLayout` ](xref:Xamarin.Forms.StackLayout)设置为`EmptyView`显示属性值：

[![使用自定义的空视图，在 iOS 和 Android 上 CollectionView 垂直列表的屏幕截图](emptyview-images/filter-multiple-views.png "与自定义的空视图之间导航垂直列表")](emptyview-images/filter-multiple-views-large.png#lightbox "CollectionView 垂直列表使用的自定义空的视图")

## <a name="display-a-templated-custom-type-when-data-is-unavailable"></a>数据不可用时显示的模板化自定义类型

`EmptyView`属性可以设置为自定义类型，其模板是显示何时`ItemsSource`属性是`null`，或指定集合`ItemsSource`属性是`null`或为空。 `EmptyViewTemplate`属性可以设置为[ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate)定义的外观`EmptyView`。 以下 XAML 演示此方案的示例：

```xaml
<StackLayout Margin="20">
    <SearchBar x:Name="searchBar"
               SearchCommand="{Binding FilterCommand}"
               SearchCommandParameter="{Binding Source={x:Reference searchBar}, Path=Text}"
               Placeholder="Filter" />
    <CollectionView ItemsSource="{Binding Monkeys}">
        <CollectionView.ItemTemplate>
            <DataTemplate>
                ...
            </DataTemplate>
        </CollectionView.ItemTemplate>
        <CollectionView.EmptyView>
            <views:FilterData Filter="{Binding Source={x:Reference searchBar}, Path=Text}" />
        </CollectionView.EmptyView>
        <CollectionView.EmptyViewTemplate>
            <DataTemplate>
                <Label Text="{Binding Filter, StringFormat='Your filter term of {0} did not match any records.'}"
                       Margin="10,25,10,10"
                       FontAttributes="Bold"
                       FontSize="18"
                       HorizontalOptions="Fill"
                       HorizontalTextAlignment="Center" />
            </DataTemplate>
        </CollectionView.EmptyViewTemplate>
    </CollectionView>
</StackLayout>
```

等效的 C# 代码是：

```csharp
SearchBar searchBar = new SearchBar { ... };
CollectionView collectionView = new CollectionView
{
    EmptyView = new FilterData { Filter = searchBar.Text },
    EmptyViewTemplate = new DataTemplate(() =>
    {
        return new Label { ... };
    })
};
```

`FilterData`类型定义`Filter`属性，并相应[ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty):

```csharp
public class FilterData : BindableObject
{
    public static readonly BindableProperty FilterProperty = BindableProperty.Create(nameof(Filter), typeof(string), typeof(FilterData), null);

    public string Filter
    {
        get { return (string)GetValue(FilterProperty); }
        set { SetValue(FilterProperty, value); }
    }
}
```

`EmptyView`属性设置为`FilterData`对象，并`Filter`属性数据绑定到[ `SearchBar.Text` ](xref:Xamarin.Forms.SearchBar.Text)属性。 当[ `SearchBar` ](xref:Xamarin.Forms.SearchBar)执行`FilterCommand`，通过显示的集合`CollectionView`进行筛选的搜索词会存储在`Filter`属性。 如果为筛选操作会不产生任何数据， [ `Label` ](xref:Xamarin.Forms.Label)中定义[ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate)，设置为`EmptyViewTemplate`属性值，会显示：

[![使用空的视图模板，在 iOS 和 Android 上 CollectionView 垂直列表的屏幕截图](emptyview-images/emptyviewtemplate.png "CollectionView 垂直列表与空视图模板")](emptyview-images/emptyviewtemplate-large.png#lightbox "CollectionView 垂直列表空视图模板")

> [!NOTE]
> 数据不可用时显示的模板化自定义类型时`EmptyViewTemplate`属性可以设置为包含多个子视图的视图。

## <a name="choose-an-emptyview-at-runtime"></a>选择在运行时 EmptyView

将显示为视图`EmptyView`数据不可用时，可以定义为[ `ContentView` ](xref:Xamarin.Forms.ContentView)中的对象[ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)。 `EmptyView`然后会将属性设置为特定`ContentView`根据一些业务逻辑，在运行时。 下面的 XAML 示例演示此方案的示例：

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="CollectionViewDemos.Views.EmptyViewSwapPage"
             Title="EmptyView (swap)">
    <ContentPage.Resources>
        <ContentView x:Key="BasicEmptyView">
            <StackLayout>
                <Label Text="No items to display."
                       Margin="10,25,10,10"
                       FontAttributes="Bold"
                       FontSize="18"
                       HorizontalOptions="Fill"
                       HorizontalTextAlignment="Center" />
            </StackLayout>
        </ContentView>
        <ContentView x:Key="AdvancedEmptyView">
            <StackLayout>
                <Label Text="No results matched your filter."
                       Margin="10,25,10,10"
                       FontAttributes="Bold"
                       FontSize="18"
                       HorizontalOptions="Fill"
                       HorizontalTextAlignment="Center" />
                <Label Text="Try a broader filter?"
                       FontAttributes="Italic"
                       FontSize="12"
                       HorizontalOptions="Fill"
                       HorizontalTextAlignment="Center" />
            </StackLayout>
        </ContentView>
    </ContentPage.Resources>

    <StackLayout Margin="20">
        <SearchBar x:Name="searchBar"
                   SearchCommand="{Binding FilterCommand}"
                   SearchCommandParameter="{Binding Source={x:Reference searchBar}, Path=Text}"
                   Placeholder="Filter" />
        <StackLayout Orientation="Horizontal">
            <Label Text="Toggle EmptyViews" />
            <Switch Toggled="OnEmptyViewSwitchToggled" />
        </StackLayout>
        <CollectionView x:Name="collectionView"
                        ItemsSource="{Binding Monkeys}">
            <CollectionView.ItemTemplate>
                <DataTemplate>
                    ...
                </DataTemplate>
            </CollectionView.ItemTemplate>
        </CollectionView>
    </StackLayout>
</ContentPage>
```

此 XAML 定义了两个[ `ContentView` ](xref:Xamarin.Forms.ContentView)页面级别中的对象[ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)，与[ `Switch` ](xref:Xamarin.Forms.Switch)对象控制其`ContentView`将对象设置为`EmptyView`属性值。 当[ `Switch` ](xref:Xamarin.Forms.Switch)处于切换状态`OnEmptyViewSwitchToggled`事件处理程序执行`ToggleEmptyView`方法：

```csharp
void ToggleEmptyView(bool isToggled)
{
    collectionView.EmptyView = isToggled ? Resources["BasicEmptyView"] : Resources["AdvancedEmptyView"];
}
```

`ToggleEmptyView`方法设置`EmptyView`的属性`collectionView`到两种状态之一的对象[ `ContentView` ](xref:Xamarin.Forms.ContentView)中存储的对象[ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)、 基于的值[ `Switch.IsToggled` ](xref:Xamarin.Forms.Switch.IsToggled)属性。 当[ `SearchBar` ](xref:Xamarin.Forms.SearchBar)执行`FilterCommand`，通过显示的集合`CollectionView`进行筛选的搜索词会存储在[ `SearchBar.Text` ](xref:Xamarin.Forms.SearchBar.Text)属性。 如果为筛选操作会不产生任何数据，`ContentView`对象设置为`EmptyView`属性显示：

[![包含在 iOS 和 Android 上的已交换空视图之间导航垂直列表的屏幕截图](emptyview-images/swap.png "CollectionView 垂直列表与交换的空视图")](emptyview-images/swap-large.png#lightbox "CollectionView 垂直列表交换为空的视图")

有关资源字典的详细信息，请参阅[Xamarin.Forms 资源字典](~/xamarin-forms/xaml/resource-dictionaries.md)。

## <a name="choose-an-emptyviewtemplate-at-runtime"></a>选择在运行时 EmptyViewTemplate

外观`EmptyView`可以在运行时，通过设置基于它的值，选择`CollectionView.EmptyViewTemplate`属性设置为[ `DataTemplateSelector` ](xref:Xamarin.Forms.DataTemplateSelector)对象：

```xaml
<ContentPage ...
             xmlns:controls="clr-namespace:CollectionViewDemos.Controls">
    <ContentPage.Resources>
        <DataTemplate x:Key="AdvancedTemplate">
            ...
        </DataTemplate>

        <DataTemplate x:Key="BasicTemplate">
            ...
        </DataTemplate>

        <controls:SearchTermDataTemplateSelector x:Key="SearchSelector"
                                                 DefaultTemplate="{StaticResource AdvancedTemplate}"
                                                 OtherTemplate="{StaticResource BasicTemplate}" />
    </ContentPage.Resources>

    <StackLayout Margin="20">
        <SearchBar x:Name="searchBar"
                   SearchCommand="{Binding FilterCommand}"
                   SearchCommandParameter="{Binding Source={x:Reference searchBar}, Path=Text}"
                   Placeholder="Filter" />
        <CollectionView ItemsSource="{Binding Monkeys}"
                        EmptyView="{Binding Source={x:Reference searchBar}, Path=Text}"
                        EmptyViewTemplate="{StaticResource SearchSelector}" />
    </StackLayout>
</ContentPage>
```

等效的 C# 代码是：

```csharp
SearchBar searchBar = new SearchBar { ... };
CollectionView collectionView = new CollectionView
{
    EmptyView = searchBar.Text,
    EmptyViewTemplate = new SearchTermDataTemplateSelector { ... }
};
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
```

`EmptyView`属性设置为[ `SearchBar.Text` ](xref:Xamarin.Forms.SearchBar.Text)属性，并且`EmptyViewTemplate`属性设置为`SearchTermDataTemplateSelector`对象。

当[ `SearchBar` ](xref:Xamarin.Forms.SearchBar)执行`FilterCommand`，通过显示的集合`CollectionView`进行筛选的搜索词会存储在[ `SearchBar.Text` ](xref:Xamarin.Forms.SearchBar.Text)属性。 如果为筛选操作会不产生任何数据， [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate)所选`SearchTermDataTemplateSelector`对象设置为`EmptyViewTemplate`属性和显示。

下面的示例演示`SearchTermDataTemplateSelector`类：

```csharp
public class SearchTermDataTemplateSelector : DataTemplateSelector
{
    public DataTemplate DefaultTemplate { get; set; }
    public DataTemplate OtherTemplate { get; set; }

    protected override DataTemplate OnSelectTemplate(object item, BindableObject container)
    {
        string query = (string)item;
        return query.ToLower().Equals("xamarin") ? OtherTemplate : DefaultTemplate;
    }
}
```

`SearchTermTemplateSelector`类定义`DefaultTemplate`并`OtherTemplate` [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate)设置为不同的数据模板的属性。 `OnSelectTemplate`重写返回`DefaultTemplate`，其中向用户显示一条消息，搜索查询不等于"xamarin"时。 等于"xamarin"，搜索查询时`OnSelectTemplate`重写返回`OtherTemplate`，这为用户显示一个基本的消息：

[![选择屏幕截图 CollectionView 运行时的空视图模板，在 iOS 和 Android](emptyview-images/datatemplateselector.png "运行时的空视图模板所选内容中之间导航")](emptyview-images/datatemplateselector-large.png#lightbox "运行时的空视图模板之间导航中的选定内容")

有关数据模板选择器的详细信息，请参阅[创建 Xamarin.Forms DataTemplateSelector](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md)。

## <a name="related-links"></a>相关链接

- [CollectionView （示例）](https://github.com/xamarin/xamarin-forms-samples/tree/forms40/UserInterface/CollectionViewDemos/)
- [Xamarin.Forms 数据模板](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)
- [Xamarin.Forms 资源字典](~/xamarin-forms/xaml/resource-dictionaries.md)
- [创建 Xamarin.Forms DataTemplateSelector](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md)
