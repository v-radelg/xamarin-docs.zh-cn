---
title: Xamarin. Forms CollectionView EmptyView
description: 在 CollectionView 中，可以指定一个空视图，在没有可显示的数据时向用户提供反馈。 空视图可以是字符串、视图或多个视图。
ms.prod: xamarin
ms.assetid: 6CEBCFE6-5577-4F68-9709-431062609153
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/06/2019
ms.openlocfilehash: c6a2a53f267a7f6764ec441944193e8c5ecd9189
ms.sourcegitcommit: 699de58432b7da300ddc2c85842e5d9e129b0dc5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2019
ms.locfileid: "68739193"
---
# <a name="xamarinforms-collectionview-emptyview"></a>Xamarin. Forms CollectionView EmptyView

![](~/media/shared/preview.png "此 API 当前为预发布版本")

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)

[`CollectionView`](xref:Xamarin.Forms.CollectionView)定义以下属性，这些属性可用于在没有要显示的数据时提供用户反馈：

- [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView)`object`、类型`null` 、 [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource)属性为`null`时将显示的字符串、绑定或视图，或由`ItemsSource`属性指定的集合为或为空。 默认值为 `null`。
- [`EmptyViewTemplate`](xref:Xamarin.Forms.ItemsView.EmptyViewTemplate)，类型[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)为，用于设置指定`EmptyView`的格式的模板。 默认值为 `null`。

这些属性是由[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)对象支持的，这意味着属性可以是数据绑定的目标。

设置[`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView)属性的主要使用方案是在对执行[`CollectionView`](xref:Xamarin.Forms.CollectionView)筛选操作时显示用户反馈，并在从 web 服务检索数据时显示用户反馈。

> [!NOTE]
> 如果需要，可以将[属性设置为包含交互式内容的视图。`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView)

有关数据模板的详细信息，请参阅 [Xamarin.Forms 数据模板](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)。

## <a name="display-a-string-when-data-is-unavailable"></a>数据不可用时显示字符串

`null` [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) `null` `ItemsSource`属性可以设置为字符串，当属性为时，或属性指定的集合为或空时，将显示此字符串。 [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) 以下 XAML 显示了此方案的示例：

```xaml
<CollectionView ItemsSource="{Binding EmptyMonkeys}"
                EmptyView="No items to display" />
```

等效 C# 代码如下：

```csharp
CollectionView collectionView = new CollectionView
{
    EmptyView = "No items to display"
};
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "EmptyMonkeys");
```

结果是，由于数据绑定集合为`null`，因此显示的字符串设置[`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView)为属性值：

[![在 iOS 和 Android 上使用文本为空视图的 CollectionView 垂直列表屏幕截图](emptyview-images/null-itemssource.png "文本为空的 CollectionView 垂直列表视图")](emptyview-images/null-itemssource-large.png#lightbox "文本为空的 CollectionView 垂直列表视图")

## <a name="display-views-when-data-is-unavailable"></a>数据不可用时显示视图

`null` [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) `null` `ItemsSource`属性可以设置为视图，当属性为时，或属性指定的集合为或空时，该视图将显示。 [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) 这可以是单个视图，也可以是包含多个子视图的视图。 下面的 XAML 示例演示`EmptyView`如何将属性设置为包含多个子视图的视图：

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

等效 C# 代码如下：

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

[`SearchBar`](xref:Xamarin.Forms.SearchBar)当[`CollectionView`](xref:Xamarin.Forms.CollectionView) [`SearchBar.Text`](xref:Xamarin.Forms.SearchBar.Text)执行时，将针对存储在属性中的搜索词筛选`FilterCommand`显示的集合。 如果筛选操作不产生数据， [`StackLayout`](xref:Xamarin.Forms.StackLayout)则会显示设置[`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView)为属性值：

[![在 iOS 和 Android 上带有自定义空视图的 CollectionView 垂直列表屏幕截图](emptyview-images/filter-multiple-views.png "带有自定义空视图的 CollectionView 垂直列表")](emptyview-images/filter-multiple-views-large.png#lightbox "带有自定义空视图的 CollectionView 垂直列表")

## <a name="display-a-templated-custom-type-when-data-is-unavailable"></a>数据不可用时显示模板化自定义类型

`null` [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) `null` `ItemsSource`属性可以设置为自定义类型，该类型的模板在属性为时显示，或属性指定的集合为空时显示。 [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) 属性可以设置[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)为，用于`EmptyView`定义的外观。 [`EmptyViewTemplate`](xref:Xamarin.Forms.ItemsView.EmptyViewTemplate) 以下 XAML 显示了此方案的示例：

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

等效 C# 代码如下：

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

类型定义一个`Filter`属性和一个对应[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)的： `FilterData`

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

属性设置`Filter`为对象，属性数据绑定到[`SearchBar.Text`](xref:Xamarin.Forms.SearchBar.Text)属性。 `FilterData` [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) [`SearchBar`](xref:Xamarin.Forms.SearchBar)当[`CollectionView`](xref:Xamarin.Forms.CollectionView) `Filter`执行时，将针对存储在属性中的搜索词筛选`FilterCommand`显示的集合。 如果筛选操作不生成任何数据， [`Label`](xref:Xamarin.Forms.Label)则会显示在[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)中定义的， [`EmptyViewTemplate`](xref:Xamarin.Forms.ItemsView.EmptyViewTemplate)它设置为属性值：

[![在 iOS 和 Android 上带有空视图模板的 CollectionView 垂直列表屏幕截图](emptyview-images/emptyviewtemplate.png "带有空视图模板的 CollectionView 垂直列表")](emptyview-images/emptyviewtemplate-large.png#lightbox "带有空视图模板的 CollectionView 垂直列表")

> [!NOTE]
> 当数据不可用时显示模板化自定义类型时[`EmptyViewTemplate`](xref:Xamarin.Forms.ItemsView.EmptyViewTemplate) ，可以将属性设置为包含多个子视图的视图。

## <a name="choose-an-emptyview-at-runtime"></a>在运行时选择 EmptyView

数据不可用[`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView)时将显示为的视图可以定义为[`ContentView`](xref:Xamarin.Forms.ContentView)中[`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)的对象。 然后`EmptyView` ，可以在运行时将该属性`ContentView`设置为基于一些业务逻辑的特定。 下面的 XAML 示例显示了此方案的示例：

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

此 XAML 定义页面[`ContentView`](xref:Xamarin.Forms.ContentView)级别[`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)中的两个对象， [`Switch`](xref:Xamarin.Forms.Switch)对象[`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView)控制将哪个`ContentView`对象设置为属性值。 切换后，事件处理程序将执行`ToggleEmptyView`方法： `OnEmptyViewSwitchToggled` [`Switch`](xref:Xamarin.Forms.Switch)

```csharp
void ToggleEmptyView(bool isToggled)
{
    collectionView.EmptyView = isToggled ? Resources["BasicEmptyView"] : Resources["AdvancedEmptyView"];
}
```

`collectionView` [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) [`ContentView`](xref:Xamarin.Forms.ContentView)方法根据[`Switch.IsToggled`](xref:Xamarin.Forms.Switch.IsToggled)属性的值将对象的属性设置为存储在中[`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)的两个对象之一。 `ToggleEmptyView` [`SearchBar`](xref:Xamarin.Forms.SearchBar)当[`CollectionView`](xref:Xamarin.Forms.CollectionView) [`SearchBar.Text`](xref:Xamarin.Forms.SearchBar.Text)执行时，将针对存储在属性中的搜索词筛选`FilterCommand`显示的集合。 如果筛选操作不产生任何数据，则`ContentView`会显示设置`EmptyView`为属性的对象：

[![在 iOS 和 Android 上的 CollectionView 垂直列表中交换空视图的屏幕截图](emptyview-images/swap.png "带有交换空视图的 CollectionView 垂直列表")](emptyview-images/swap-large.png#lightbox "带有交换空视图的 CollectionView 垂直列表")

有关资源字典的详细信息，请参阅[Xamarin。 Forms 资源字典](~/xamarin-forms/xaml/resource-dictionaries.md)。

## <a name="choose-an-emptyviewtemplate-at-runtime"></a>在运行时选择 EmptyViewTemplate

通过[`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector)将属性设置为对象，可在运行时根据其值选择的外观： [`CollectionView.EmptyViewTemplate`](xref:Xamarin.Forms.ItemsView.EmptyViewTemplate)

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

等效 C# 代码如下：

```csharp
SearchBar searchBar = new SearchBar { ... };
CollectionView collectionView = new CollectionView
{
    EmptyView = searchBar.Text,
    EmptyViewTemplate = new SearchTermDataTemplateSelector { ... }
};
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
```

将属性设置[`SearchBar.Text`](xref:Xamarin.Forms.SearchBar.Text)为属性，并[`EmptyViewTemplate`](xref:Xamarin.Forms.ItemsView.EmptyViewTemplate)将属性设置为`SearchTermDataTemplateSelector`对象。 [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView)

[`SearchBar`](xref:Xamarin.Forms.SearchBar)当[`CollectionView`](xref:Xamarin.Forms.CollectionView) [`SearchBar.Text`](xref:Xamarin.Forms.SearchBar.Text)执行时，将针对存储在属性中的搜索词筛选`FilterCommand`显示的集合。 如果筛选操作不生成任何数据，则[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)会将`SearchTermDataTemplateSelector` [`EmptyViewTemplate`](xref:Xamarin.Forms.ItemsView.EmptyViewTemplate)对象选择的设置为属性，并显示。

下面的示例演示`SearchTermDataTemplateSelector`了类:

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

类定义`DefaultTemplate`了并`OtherTemplate`将[属性设置为不同的数据模板。`DataTemplate`](xref:Xamarin.Forms.DataTemplate) `SearchTermTemplateSelector` 当`OnSelectTemplate`搜索查询`DefaultTemplate`不等于 "xamarin" 时，重写将返回，后者向用户显示一条消息。 当搜索查询等于 "xamarin" 时，重写将`OnSelectTemplate`返回`OtherTemplate`，这将向用户显示基本消息：

[![IOS 和 Android 上的 CollectionView 运行时空视图模板选择的屏幕截图](emptyview-images/datatemplateselector.png "CollectionView 中的运行时空视图模板选择")](emptyview-images/datatemplateselector-large.png#lightbox "CollectionView 中的运行时空视图模板选择")

有关数据模板选择器的详细信息，请参阅[Create a Xamarin. Forms 并重](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md)。

## <a name="related-links"></a>相关链接

- [CollectionView （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)
- [Xamarin. Forms 数据模板](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)
- [Xamarin. Forms 资源字典](~/xamarin-forms/xaml/resource-dictionaries.md)
- [创建 Xamarin. Forms 并重](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md)
