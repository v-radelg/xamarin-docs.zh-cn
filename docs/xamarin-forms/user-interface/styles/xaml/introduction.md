---
title: Xamarin.Forms 样式简介
description: 样式允许可视化元素，以自定义的外观。 样式为特定类型定义和包含该类型上可用的属性的值。
ms.prod: xamarin
ms.assetid: 3FF899C0-6CFB-4C1D-837D-9E9E10181967
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/27/2016
ms.openlocfilehash: 4048ec78d48b810b39d46fbcb7708860c478cce3
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/08/2019
ms.locfileid: "57667199"
---
# <a name="introduction-to-xamarinforms-styles"></a>Xamarin.Forms 样式简介

_样式允许可视化元素，以自定义的外观。样式为特定类型定义和包含该类型上可用的属性的值。_

Xamarin.Forms 应用程序通常包含多个具有相同的外观的控件。 例如，应用程序可能具有多个[ `Label` ](xref:Xamarin.Forms.Label)实例具有相同的字体选项和布局选项，如下面的 XAML 代码示例中所示：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
    xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
    x:Class="Styles.NoStylesPage"
    Title="No Styles"
    Icon="xaml.png">
    <ContentPage.Content>
        <StackLayout Padding="0,20,0,0">
            <Label Text="These labels"
                   HorizontalOptions="Center"
                   VerticalOptions="CenterAndExpand"
                   FontSize="Large" />
            <Label Text="are not"
                   HorizontalOptions="Center"
                   VerticalOptions="CenterAndExpand"
                   FontSize="Large" />
            <Label Text="using styles"
                   HorizontalOptions="Center"
                   VerticalOptions="CenterAndExpand"
                   FontSize="Large" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

以下代码示例演示了在 C# 中创建的等效页：

```csharp
public class NoStylesPageCS : ContentPage
{
    public NoStylesPageCS ()
    {
        Title = "No Styles";
        Icon = "csharp.png";
        Padding = new Thickness (0, 20, 0, 0);

        Content = new StackLayout {
            Children = {
                new Label {
                    Text = "These labels",
                    HorizontalOptions = LayoutOptions.Center,
                    VerticalOptions = LayoutOptions.CenterAndExpand,
                    FontSize = Device.GetNamedSize (NamedSize.Large, typeof(Label))
                },
                new Label {
                    Text = "are not",
                    HorizontalOptions = LayoutOptions.Center,
                    VerticalOptions = LayoutOptions.CenterAndExpand,
                    FontSize = Device.GetNamedSize (NamedSize.Large, typeof(Label))
                },
                new Label {
                    Text = "using styles",
                    HorizontalOptions = LayoutOptions.Center,
                    VerticalOptions = LayoutOptions.CenterAndExpand,
                    FontSize = Device.GetNamedSize (NamedSize.Large, typeof(Label))
                }
            }
        };
    }
}
```

每个[ `Label` ](xref:Xamarin.Forms.Label)实例具有相同的属性值，用于控制显示的文本的外观`Label`。 这会导致如以下屏幕截图中所示的外观：

[![](introduction-images/no-styles.png "标签不带样式的外观")](introduction-images/no-styles-large.png#lightbox "标签不带样式的外观")

设置每个控件的外观会重复且容易出错。 相反，一种样式可以创建定义的外观，然后应用于所需的控制。

## <a name="create-a-style"></a>创建样式

[ `Style` ](xref:Xamarin.Forms.Style)类进行分组到一个对象，然后可以应用于多个可视元素实例的属性值的集合。 这有助于减少重复性的标记，并允许应用程序外观以更轻松地更改。

尽管主要针对基于 XAML 的应用程序设计样式，但它们也可以创建在 C# 中：

- [`Style`](xref:Xamarin.Forms.Style) 在 XAML 中创建的实例通常定义在[ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)分配给[ `Resources` ](xref:Xamarin.Forms.VisualElement.Resources)控件的集合页上，或设置为[ `Resources` ](xref:Xamarin.Forms.Application.Resources)应用程序的集合。
- [`Style`](xref:Xamarin.Forms.Style) 在页面的类中，或者可全局访问的类通常定义在 C# 中创建的实例。

选择在何处定义 [`Style`](xref:Xamarin.Forms.Style) 会影响其应用范围：

- [`Style`](xref:Xamarin.Forms.Style) 在控件级别定义的实例只能应用到控件，及其子级。
- [`Style`](xref:Xamarin.Forms.Style) 在页面级别定义的实例只能应用到页，及其子级。
- [`Style`](xref:Xamarin.Forms.Style) 在应用程序级别定义的实例可以应用整个应用程序。

每个[ `Style` ](xref:Xamarin.Forms.Style)实例包含一个或多个集合[ `Setter` ](xref:Xamarin.Forms.Setter)对象，每个`Setter`无[ `Property` ](xref:Xamarin.Forms.Setter.Property)和[`Value`](xref:Xamarin.Forms.Setter.Value). `Property`是该样式应用到，该元素的可绑定属性的名称和`Value`是应用于属性的值。

每个[ `Style` ](xref:Xamarin.Forms.Style)实例可以是*显式*，或*隐式*:

- *显式* [ `Style` ](xref:Xamarin.Forms.Style)通过指定定义实例[ `TargetType` ](xref:Xamarin.Forms.Style.TargetType)和`x:Key`值，并通过设置目标元素[`Style` ](xref:Xamarin.Forms.VisualElement.Style)属性设置为`x:Key`引用。 有关详细信息*显式*样式，请参阅[显式样式](~/xamarin-forms/user-interface/styles/explicit.md)。
- *隐式* [ `Style` ](xref:Xamarin.Forms.Style)仅指定定义实例[ `TargetType` ](xref:Xamarin.Forms.Style.TargetType)。 `Style`实例将然后会自动应用于该类型的所有元素。 请注意该子类`TargetType`不会自动具有`Style`应用。 有关详细信息*隐式*样式，请参阅[隐式样式](~/xamarin-forms/user-interface/styles/implicit.md)。

创建时[ `Style` ](xref:Xamarin.Forms.Style)，则[ `TargetType` ](xref:Xamarin.Forms.Style.TargetType)并总是必需的属性。 下面的代码示例演示*显式*样式 (请注意`x:Key`) 在 XAML 中创建：

```xaml
<Style x:Key="labelStyle" TargetType="Label">
    <Setter Property="HorizontalOptions" Value="Center" />
    <Setter Property="VerticalOptions" Value="CenterAndExpand" />
    <Setter Property="FontSize" Value="Large" />
</Style>
```

若要将应用`Style`，目标对象必须是[ `VisualElement` ](xref:Xamarin.Forms.VisualElement)相匹配[ `TargetType` ](xref:Xamarin.Forms.Style.TargetType)属性值为`Style`，如以下 XAML 代码示例中所示：

```xaml
<Label Text="Demonstrating an explicit style" Style="{StaticResource labelStyle}" />
```

样式的视图层次结构中较低级别优先于更高版本定义了。 例如，设置[ `Style` ](xref:Xamarin.Forms.Style) ，用于设置[ `Label.TextColor` ](xref:Xamarin.Forms.Label.TextColor)到`Red`在应用程序级别将被重写由设置的页级别样式`Label.TextColor`到`Green`. 同样，将控件级别样式将页级别样式中重写。 此外，如果`Label.TextColor`设置直接上的控件属性，此方法将优先于任何样式。

在本部分中的文章演示并说明如何创建并应用*显式*并*隐式*样式，如何创建全局样式、 样式继承，如何响应在运行时，样式更改以及如何使用 Xamarin.Forms 中包含的内置样式。

> [!NOTE]
> **Styleid 等各是什么？**
>
> 低于 Xamarin.Forms 2.2 [ `StyleId` ](xref:Xamarin.Forms.Element.StyleId)属性用于标识用于在 UI 测试中，以及主题引擎如 Pixate 中标识的应用程序中各个元素。 但是，引入了 Xamarin.Forms 2.2 [ `AutomationId` ](xref:Xamarin.Forms.Element.AutomationId)属性，已取代[ `StyleId` ](xref:Xamarin.Forms.Element.StyleId)属性。

## <a name="related-links"></a>相关链接

- [XAML 标记扩展](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [样式](xref:Xamarin.Forms.Style)
- [资源库](xref:Xamarin.Forms.Setter)
