---
title: Xamarin 中的可绑定布局
description: 可绑定布局通过绑定到项的集合，使布局类能够生成其内容，并且可以选择使用 System.windows.datatemplate> 设置每个项的外观。
ms.prod: xamarin
ms.assetid: 824C3319-20A0-42D0-8632-CDECD98349C3
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/18/2018
ms.openlocfilehash: a824c892d21df9264b772bed09a4aef893f3b949
ms.sourcegitcommit: 699de58432b7da300ddc2c85842e5d9e129b0dc5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2019
ms.locfileid: "68647909"
---
# <a name="bindable-layouts-in-xamarinforms"></a>Xamarin 中的可绑定布局

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-bindablelayouts)

绑定布局支持从[`Layout<T>`](xref:Xamarin.Forms.Layout`1)类派生的任何布局类，通过绑定到项的集合来生成其内容，并且可以选择[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)使用设置每个项的外观。 可绑定布局由`BindableLayout`类提供，该类公开了以下附加属性：

- `ItemsSource`–指定布局要显示`IEnumerable`的项的集合。
- `ItemTemplate`–指定要[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)应用于布局所显示的项集合中的每个项的。
- `ItemTemplateSelector`–指定[`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector)将用于在运行时[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)为项选择的。

这些属性可以[`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout)连接到[`Grid`](xref:Xamarin.Forms.Grid)、 [`FlexLayout`](xref:Xamarin.Forms.FlexLayout) [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout)、、和[`StackLayout`](xref:Xamarin.Forms.StackLayout)类，它们都派生自[`Layout<T>`](xref:Xamarin.Forms.Layout`1)类。

> [!NOTE]
> 当`ItemTemplate`设置了`ItemTemplate` 和`ItemTemplateSelector`属性时, 属性将优先。

类公开一个[`Children`](xref:Xamarin.Forms.Layout`1.Children)集合，其中向其中添加了布局的子元素。 `Layout<T>` 当属性设置为项的集合并附加[`Layout<T>`](xref:Xamarin.Forms.Layout`1)到派生类时， `Layout<T>.Children`集合中的每一项都将添加到集合中，以供布局显示。 `BinableLayout.ItemsSource` 如果`Layout<T>`基础集合发生更改，则派生的类将更新其子视图。 有关 Xamarin 布局循环的详细信息，请参阅[创建自定义布局](~/xamarin-forms/user-interface/layouts/custom.md)。

仅当要显示的项的集合较小时，并且不需要滚动和选择时，才应使用可绑定的布局。 虽然滚动可以通过在中[`ScrollView`](xref:Xamarin.Forms.ScrollView)包装可绑定布局来提供，但不建议将其作为可绑定布局缺少 UI 虚拟化。 需要滚动时，应该使用包括 UI 虚拟化的可滚动视图， [`ListView`](xref:Xamarin.Forms.ListView)例如[`CollectionView`](xref:Xamarin.Forms.CollectionView)或。 如果未遵守此建议，可能会导致性能问题。

> [!IMPORTANT]
>尽管从技术上讲，可以将可绑定[`Layout<T>`](xref:Xamarin.Forms.Layout`1)布局附加到从类派生的任何布局类，但执行此操作并非总是可行的，尤其是[`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout)对于、 [`Grid`](xref:Xamarin.Forms.Grid)和[`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout)类。 例如，假设想要[`Grid`](xref:Xamarin.Forms.Grid)使用可绑定布局在中显示数据集合，其中的每个项都是一个包含多个属性的对象。 中的`Grid`每一行都应显示集合中的一个对象，其中的`Grid`每一列都显示一个对象的属性。 由于可[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)绑定布局的只能包含单个对象，因此该对象必须为包含多个视图的布局类，每个视图在特定`Grid`列中显示对象的一个属性。 尽管此方案可以使用可绑定的布局 realised，但它会生成`Grid`一个父项`Grid` ，其中包含绑定集合中每个项的子元素，这是`Grid`布局的高效率和有问题的用法。

## <a name="populating-a-bindable-layout-with-data"></a>使用数据填充可绑定布局

可以通过将数据的`ItemsSource`属性设置为实现`IEnumerable`的任何集合[`Layout<T>`](xref:Xamarin.Forms.Layout`1)并将其附加到派生类，来填充可绑定的布局：

```xaml
<Grid BindableLayout.ItemsSource="{Binding Items}" />
```

等效 C# 代码如下：

```csharp
IEnumerable<string> items = ...;
var grid = new Grid();
BindableLayout.SetItemsSource(grid, items);
```

[`Label`](xref:Xamarin.Forms.Label) `BindableLayout.ItemTemplate` `IEnumerable`如果在布局上设置`BindableLayout`附加属性，但未设置附加属性，则集合中的每个项都将由类创建的显示。 `BindableLayout.ItemsSource`

## <a name="defining-item-appearance"></a>定义项外观

可以通过将`BindableLayout.ItemTemplate`附加属性设置[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)为来定义可绑定布局中每个项的外观：

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

等效 C# 代码如下：

```csharp
DataTemplate circleImageTemplate = ...;
var stackLayout = new StackLayout();
BindableLayout.SetItemsSource(stackLayout, viewModel.User.TopFollowers);
BindableLayout.SetItemTemplate(stackLayout, circleImageTemplate);
```

在此示例中， `TopFollowers`集合中的每个项都将`CircleImage`由中[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)定义的视图显示：

![具有 system.windows.datatemplate> 的可绑定布局](bindable-layouts-images/top-followers.png "使用数据模板的可绑定布局")

有关数据模板的详细信息，请参阅 [Xamarin.Forms 数据模板](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)。

## <a name="choosing-item-appearance-at-runtime"></a>在运行时选择项外观

可以在运行时根据项值在运行时选择每个项的外观，方法是将`BindableLayout.ItemTemplateSelector`附加属性设置[`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector)为：

```xaml
<FlexLayout BindableLayout.ItemsSource="{Binding User.FavoriteTech}"
            BindableLayout.ItemTemplateSelector="{StaticResource TechItemTemplateSelector}"
            ... />
```

等效 C# 代码如下：

```csharp
DataTemplateSelector dataTemplateSelector = new TechItemTemplateSelector { ... };
var flexLayout = new FlexLayout();
BindableLayout.SetItemsSource(flexLayout, viewModel.User.FavoriteTech);
BindableLayout.SetItemTemplateSelector(flexLayout, dataTemplateSelector);
```

示例[`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector)应用程序中使用的如下例所示：

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

类定义`DefaultTemplate`了并`XamarinFormsTemplate`将[属性设置为不同的数据模板。`DataTemplate`](xref:Xamarin.Forms.DataTemplate) `TechItemTemplateSelector` `OnSelectTemplate` 方法`XamarinFormsTemplate`返回，当项等于 "Xamarin. Forms" 时，它会将项显示为深红色，并显示一项。 当项不等于 "Xamarin. Forms" 时， `OnSelectTemplate`方法将`DefaultTemplate`返回，它使用的默认颜色来[`Label`](xref:Xamarin.Forms.Label)显示项：

![具有并重的可绑定布局](bindable-layouts-images/favorite-tech.png "使用数据模板选择器的可绑定布局")

有关数据模板选择器的详细信息, 请参阅[创建 Xamarin。窗体并重](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md)。

## <a name="related-links"></a>相关链接

- [可绑定布局演示（示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-bindablelayouts)
- [创建自定义布局](~/xamarin-forms/user-interface/layouts/custom.md)
- [Xamarin. Forms 数据模板](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)
- [创建 Xamarin. Forms 并重](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md)
