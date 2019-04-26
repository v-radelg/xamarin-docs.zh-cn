---
title: 在 Xamarin.Forms 中的可绑定布局
description: 可绑定布局启用布局类来生成其内容通过绑定到的项，设置每个项使用 DataTemplate 的外观的选项的集合。
ms.prod: xamarin
ms.assetid: 824C3319-20A0-42D0-8632-CDECD98349C3
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/18/2018
ms.openlocfilehash: b0e2d5e3c7923e5c3cf2adcc1dd104a97b78e727
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61321545"
---
# <a name="bindable-layouts-in-xamarinforms"></a>在 Xamarin.Forms 中的可绑定布局

[![下载示例](~/media/shared/download.png)下载示例](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/BindableLayouts/)

可绑定布局启用任何布局类都派生自[ `Layout<T>` ](xref:Xamarin.Forms.Layout`1)类来生成其内容通过绑定到一系列项，用于设置与每个项的外观的选项[ `DataTemplate`](xref:Xamarin.Forms.DataTemplate). 提供可绑定布局`BindableLayout`类，它提供以下附加的属性：

- `ItemsSource` – 指定的集合`IEnumerable`项显示的布局。
- `ItemTemplate` – 指定[ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate)要应用于布局显示的项集合中每个项目。
- `ItemTemplateSelector` – 指定[ `DataTemplateSelector` ](xref:Xamarin.Forms.DataTemplateSelector)将用于选择[ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate)在运行时的项。

这些属性可以附加到[ `AbsoluteLayout` ](xref:Xamarin.Forms.AbsoluteLayout)， [ `FlexLayout` ](xref:Xamarin.Forms.FlexLayout)， [ `Grid` ](xref:Xamarin.Forms.Grid)， [ `RelativeLayout` ](xref:Xamarin.Forms.RelativeLayout)并[ `StackLayout` ](xref:Xamarin.Forms.StackLayout)类，它们都派生自[ `Layout<T>` ](xref:Xamarin.Forms.Layout`1)类。

> [!NOTE]
> `ItemTemplate`属性将优先于时同时`ItemTemplate`和`ItemTemplateSelector`设置属性。

`Layout<T>`类公开[ `Children` ](xref:Xamarin.Forms.Layout`1.Children)布局的子元素都添加到集合。 当`BinableLayout.ItemsSource`属性是否设置为一系列项以及连接到[ `Layout<T>` ](xref:Xamarin.Forms.Layout`1)-派生的类中，集合中每个项添加到`Layout<T>.Children`通过布局显示的集合。 `Layout<T>`-基础集合发生更改时，派生的类然后将更新其子视图。 有关 Xamarin.Forms 布局循环的详细信息，请参阅[创建自定义布局](~/xamarin-forms/user-interface/layouts/custom.md)。

> [!IMPORTANT]
> 要显示的项集合很小，并滚动和选择不需要时，应仅使用可绑定的布局。 虽然可以由包装的可绑定布局提供滚动[ `ScrollView` ](xref:Xamarin.Forms.ScrollView)，建议不要因为可绑定布局缺少 UI 虚拟化。 滚动是必需的如包含 UI 虚拟化的可滚动视图[ `ListView` ](xref:Xamarin.Forms.ListView)或`CollectionView`，应使用。 不遵守此建议可能会导致性能问题。

## <a name="populating-a-bindable-layout-with-data"></a>填充数据的可绑定布局

可绑定的布局通过设置使用数据填充其`ItemsSource`属性设置为任何实现的集合`IEnumerable`，并将其附加到[ `Layout<T>` ](xref:Xamarin.Forms.Layout`1)-派生的类：

```xaml
<Grid BindableLayout.ItemsSource="{Binding Items}" />
```

等效的 C# 代码是：

```csharp
IEnumerable<string> items = ...;
var grid = new Grid();
BindableLayout.SetItemsSource(grid, items);
```

时`BindableLayout.ItemsSource`附加的属性设置的布局，但`BindableLayout.ItemTemplate`附加的属性未设置，每个项`IEnumerable`集合将显示由[ `Label` ](xref:Xamarin.Forms.Label)由`BindableLayout`类。

## <a name="defining-item-appearance"></a>定义项外观

可以通过将定义可绑定的布局中的每个项的外观`BindableLayout.ItemTemplate`附加属性设置为[ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate):

```xaml
<StackLayout BindableLayout.ItemsSource="{Binding User.TopFollowers}"
             Orientation="Horizontal"
             ...>
    <BindableLayout.ItemTemplate>
        <DataTemplate>
            <controls:CircleImage Source="{Binding}"
                                  Aspect="AspectFill"
                                  WidthRequest="44"
                                  HeightRequest="44"
                                  ... />
        </DataTemplate>
    </BindableLayout.ItemTemplate>
</StackLayout>
```

等效的 C# 代码是：

```csharp
DataTemplate circleImageTemplate = ...;
var stackLayout = new StackLayout();
BindableLayout.SetItemsSource(stackLayout, viewModel.User.TopFollowers);
BindableLayout.SetItemTemplate(stackLayout, circleImageTemplate);
```

在此示例中，在每个项`TopFollowers`集合将显示由`CircleImage`中定义的视图[ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate):

![使用 DataTemplate 可绑定布局](bindable-layouts-images/top-followers.png "使用数据模板的可绑定布局")

有关数据模板的详细信息，请参阅 [Xamarin.Forms 数据模板](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)。

## <a name="choosing-item-appearance-at-runtime"></a>选择项在运行时的外观

可以在运行时，通过设置基于项值中，选择可绑定的布局中的每个项的外观`BindableLayout.ItemTemplateSelector`附加属性设置为[ `DataTemplateSelector` ](xref:Xamarin.Forms.DataTemplateSelector):

```xaml
<FlexLayout BindableLayout.ItemsSource="{Binding User.FavoriteTech}"
            BindableLayout.ItemTemplateSelector="{StaticResource TechItemTemplateSelector}"
            ... />
```

等效的 C# 代码是：

```csharp
DataTemplateSelector dataTemplateSelector = new TechItemTemplateSelector { ... };
var flexLayout = new FlexLayout();
BindableLayout.SetItemsSource(flexLayout, viewModel.User.FavoriteTech);
BindableLayout.SetItemTemplateSelector(flexLayout, dataTemplateSelector);
```

[ `DataTemplateSelector` ](xref:Xamarin.Forms.DataTemplateSelector)使用示例中，应用程序在下面的示例所示：

```csharp
public class TechItemTemplateSelector : DataTemplateSelector
{
    public DataTemplate DefaultTemplate { get; set; }
    public DataTemplate XamarinFormsTemplate { get; set; }

    protected override DataTemplate OnSelectTemplate(object item, BindableObject container)
    {
        return (string)item == "Xamarin.Forms" ? XamarinFormsTemplate : DefaultTemplate;
    }
}
```

`TechItemTemplateSelector`类定义`DefaultTemplate`并`XamarinFormsTemplate` [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate)设置为不同的数据模板的属性。 `OnSelectTemplate`方法将返回`XamarinFormsTemplate`，等于"Xamarin.Forms"项时项显示在核心旁边，与深红色。 项不等于"Xamarin.Forms"时`OnSelectTemplate`方法将返回`DefaultTemplate`，其中显示的项使用的默认颜色[ `Label` ](xref:Xamarin.Forms.Label):

![可绑定的布局与 DataTemplateSelector](bindable-layouts-images/favorite-tech.png "可绑定具有一个数据模板选择器的布局")

有关数据模板选择器的详细信息，请参阅[创建 Xamarin.Forms DataTemplateSelector](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md)。

## <a name="related-links"></a>相关链接

- [可绑定布局演示 （示例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/BindableLayouts/)
- [创建自定义布局](~/xamarin-forms/user-interface/layouts/custom.md)
- [Xamarin.Forms 数据模板](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)
- [创建 Xamarin.Forms DataTemplateSelector](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md)
