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
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/21/2019
ms.locfileid: "68647909"
---
# <a name="bindable-layouts-in-xamarinforms"></a>Xamarin 中的可绑定布局

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-bindablelayouts)

可绑定布局支持从[`Layout<T>`](xref:Xamarin.Forms.Layout`1)类派生的任何布局类，通过绑定到项的集合生成其内容，并可选择使用[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)设置每个项的外观。 可绑定布局由 `BindableLayout` 类提供，该类公开了以下附加属性：

- `ItemsSource` –指定布局要显示的 `IEnumerable` 项的集合。
- `ItemTemplate` –指定要应用于布局所显示的项集合中的每一项的[`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 。
- `ItemTemplateSelector` –指定将用于在运行时为项选择[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)的[`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) 。

这些属性可以附加到[`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout)、 [`FlexLayout`](xref:Xamarin.Forms.FlexLayout)、 [`Grid`](xref:Xamarin.Forms.Grid)、 [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout)和[`StackLayout`](xref:Xamarin.Forms.StackLayout)类，它们都派生自[1](xref:Xamarin.Forms.Layout`1)类。

> [!NOTE]
> 如果同时设置了 `ItemTemplate` 和 `ItemTemplateSelector` 属性，则 `ItemTemplate` 属性将优先。

@No__t_0 类公开一个[`Children`](xref:Xamarin.Forms.Layout`1.Children)集合，其中向其中添加了布局的子元素。 当 `BinableLayout.ItemsSource` 属性设置为项的集合并附加到[`Layout<T>`](xref:Xamarin.Forms.Layout`1)派生类时，集合中的每一项都将添加到 `Layout<T>.Children` 集合中以供布局显示。 当基础集合发生更改时，`Layout<T>` 派生的类将更新其子视图。 有关 Xamarin 布局循环的详细信息，请参阅[创建自定义布局](~/xamarin-forms/user-interface/layouts/custom.md)。

仅当要显示的项的集合较小时，并且不需要滚动和选择时，才应使用可绑定的布局。 虽然滚动可以通过在[`ScrollView`](xref:Xamarin.Forms.ScrollView)中包装可绑定布局来提供，但不建议将其作为可绑定布局缺少 UI 虚拟化。 需要滚动时，应该使用包括 UI 虚拟化的可滚动视图，例如[`ListView`](xref:Xamarin.Forms.ListView)或[`CollectionView`](xref:Xamarin.Forms.CollectionView)。 如果未遵守此建议，可能会导致性能问题。

> [!IMPORTANT]
>尽管从技术上讲，可以将可绑定布局附加到从[`Layout<T>`](xref:Xamarin.Forms.Layout`1)类派生的任何布局类，但对于[`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout)、 [`Grid`](xref:Xamarin.Forms.Grid)和[`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout)类而言，这并不总是可行。 例如，假设要在使用可绑定布局的[`Grid`](xref:Xamarin.Forms.Grid)中显示数据集合，其中每个项都是一个包含多个属性的对象。 @No__t_0 中的每一行应显示集合中的对象，并且 `Grid` 中的每一列都显示对象的一个属性。 由于可绑定布局的[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)只能包含单个对象，因此该对象必须为包含多个视图的布局类，每个视图都在特定的 `Grid` 列中显示该对象的一个属性。 尽管此方案可以使用可绑定的布局 realised，但它会导致父 `Grid` 包含绑定集合中每个项的子 `Grid`，这对 `Grid` 布局的使用非常低效和有问题。

## <a name="populating-a-bindable-layout-with-data"></a>使用数据填充可绑定布局

通过将数据的 `ItemsSource` 属性设置为任何实现 `IEnumerable` 的集合，并将其附加到[`Layout<T>`](xref:Xamarin.Forms.Layout`1)派生的类，可以对可绑定布局进行填充：

```xaml
<Grid BindableLayout.ItemsSource="{Binding Items}" />
```

等效 C# 代码如下：

```csharp
IEnumerable<string> items = ...;
var grid = new Grid();
BindableLayout.SetItemsSource(grid, items);
```

如果在布局上设置 `BindableLayout.ItemsSource` 附加属性，但未设置 `BindableLayout.ItemTemplate` 附加属性，则 `IEnumerable` 集合中的每一项都将由 `BindableLayout` 类创建的[`Label`](xref:Xamarin.Forms.Label)显示。

## <a name="defining-item-appearance"></a>定义项外观

可以通过将 `BindableLayout.ItemTemplate` 附加属性设置为[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)来定义可绑定布局中每个项的外观：

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

在此示例中，`TopFollowers` 集合中的每一项将由[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)中定义的 `CircleImage` 视图显示：

![具有 System.windows.datatemplate> 的可绑定布局](bindable-layouts-images/top-followers.png "使用数据模板的可绑定布局")

有关数据模板的详细信息，请参阅 [Xamarin.Forms 数据模板](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)。

## <a name="choosing-item-appearance-at-runtime"></a>在运行时选择项外观

可以在运行时根据项值在运行时选择每个项的外观，方法是将 `BindableLayout.ItemTemplateSelector` 附加属性设置为[`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector)：

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

示例应用程序中使用的[`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector)如以下示例中所示：

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

@No__t_0 类定义设置为不同数据模板 `DefaultTemplate` 和 `XamarinFormsTemplate` [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)属性。 @No__t_0 方法返回 `XamarinFormsTemplate`，该在项等于 "Xamarin. Forms" 时，将显示一项，并在其旁边显示带有红桃的一项。 当项不等于 "Xamarin. Forms" 时，`OnSelectTemplate` 方法返回 `DefaultTemplate`，这将使用[`Label`](xref:Xamarin.Forms.Label)的默认颜色来显示项：

![具有并重的可绑定布局](bindable-layouts-images/favorite-tech.png "使用数据模板选择器的可绑定布局")

有关数据模板选择器的详细信息，请参阅[创建 Xamarin。窗体并重](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md)。

## <a name="related-links"></a>相关链接

- [可绑定布局演示（示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-bindablelayouts)
- [创建自定义布局](~/xamarin-forms/user-interface/layouts/custom.md)
- [Xamarin. Forms 数据模板](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)
- [创建 Xamarin. Forms 并重](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md)
