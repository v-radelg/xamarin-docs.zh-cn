---
title: Xamarin 样式简介
description: 样式允许自定义视觉对象的外观。 样式是针对特定类型定义的, 其中包含可用于该类型的属性的值。
ms.prod: xamarin
ms.assetid: 3FF899C0-6CFB-4C1D-837D-9E9E10181967
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/27/2016
ms.openlocfilehash: 35f8dad3590c07ceb3c93aa735b8c02d75098498
ms.sourcegitcommit: c9651cad80c2865bc628349d30e82721c01ddb4a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2019
ms.locfileid: "70228173"
---
# <a name="introduction-to-xamarinforms-styles"></a>Xamarin 样式简介

_样式允许自定义视觉对象的外观。样式是针对特定类型定义的, 其中包含可用于该类型的属性的值。_

Xamarin。窗体应用程序通常包含多个具有相同外观的控件。 例如, 应用程序可能有多个[`Label`](xref:Xamarin.Forms.Label)实例, 这些实例具有相同的字体选项和布局选项, 如下面的 XAML 代码示例所示:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
    xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
    x:Class="Styles.NoStylesPage"
    Title="No Styles"
    IconImageSource="xaml.png">
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
        IconImageSource = "csharp.png";
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

每[`Label`](xref:Xamarin.Forms.Label)个实例都具有相同的属性值, 用于控制所显示`Label`的文本的外观。 这会导致如以下屏幕截图中所示的外观：

[![不带样式的标签外观](introduction-images/no-styles.png)](introduction-images/no-styles-large.png#lightbox)

设置各个控件的外观可能是重复性的并且容易出错。 相反, 可以创建一个样式来定义外观, 然后将其应用于所需的控件。

## <a name="create-a-style"></a>创建样式

[`Style`](xref:Xamarin.Forms.Style)类将属性值的集合分组到一个对象中, 然后可以将该对象应用于多个视觉元素实例。 这有助于减少重复的标记, 并允许更轻松地更改应用程序的外观。

虽然样式主要设计用于基于 XAML 的应用程序, 但也可以在中C#创建它们:

- [`Style`](xref:Xamarin.Forms.Style)通常会在为[`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)控件、 [`Resources`](xref:Xamarin.Forms.Application.Resources)页或应用程序集合分配[`Resources`](xref:Xamarin.Forms.VisualElement.Resources)的中定义在 XAML 中创建的实例。
- [`Style`](xref:Xamarin.Forms.Style)中C#创建的实例通常在页的类中定义, 或在可全局访问的类中定义。

选择在何处定义 [`Style`](xref:Xamarin.Forms.Style) 会影响其应用范围：

- [`Style`](xref:Xamarin.Forms.Style)在控件级别定义的实例只能应用于控件及其子级。
- [`Style`](xref:Xamarin.Forms.Style)在页面级别定义的实例只能应用于页面及其子页面。
- [`Style`](xref:Xamarin.Forms.Style)可以在应用程序中应用在应用程序级别定义的实例。

每[`Style`](xref:Xamarin.Forms.Style)个实例都包含一个或多[`Setter`](xref:Xamarin.Forms.Setter)个对象的集合[`Property`](xref:Xamarin.Forms.Setter.Property) , `Setter`每个对象都[`Value`](xref:Xamarin.Forms.Setter.Value)有一个和一个。 是应用了样式的元素的可绑定属性的名称, `Value`是应用于属性的值。 `Property`

每[`Style`](xref:Xamarin.Forms.Style)个实例可以为*显式*或*隐式*:

- [`TargetType`](xref:Xamarin.Forms.Style.TargetType) [`Style`](xref:Xamarin.Forms.NavigableElement.Style) *显式* [`Style`](xref:Xamarin.Forms.Style)实例是通过指定和`x:Key`值, 并将目标元素的属性设置为引用来定义的。 `x:Key` 有关*显式*样式的详细信息, 请参阅[显式样式](~/xamarin-forms/user-interface/styles/explicit.md)。
- *隐式* [`Style`](xref:Xamarin.Forms.Style)实例是通过仅指定指定的[`TargetType`](xref:Xamarin.Forms.Style.TargetType)来定义的。 然后`Style` , 实例将自动应用于该类型的所有元素。 请注意, 的`TargetType`子类不会自动`Style`应用。 有关*隐式*样式的详细信息, 请参阅[隐式样式](~/xamarin-forms/user-interface/styles/implicit.md)。

创建[`Style`](xref:Xamarin.Forms.Style)[时`TargetType`](xref:Xamarin.Forms.Style.TargetType) , 始终需要属性。 下面的代码示例演示了在 XAML 中创建的`x:Key`显式样式 (注意):

```xaml
<Style x:Key="labelStyle" TargetType="Label">
    <Setter Property="HorizontalOptions" Value="Center" />
    <Setter Property="VerticalOptions" Value="CenterAndExpand" />
    <Setter Property="FontSize" Value="Large" />
</Style>
```

若要应用`Style`, 目标对象必须[`VisualElement`](xref:Xamarin.Forms.VisualElement)是与的[`TargetType`](xref:Xamarin.Forms.Style.TargetType) `Style`属性值相匹配的, 如下面的 XAML 代码示例所示:

```xaml
<Label Text="Demonstrating an explicit style" Style="{StaticResource labelStyle}" />
```

样式的视图层次结构中较低级别优先于更高版本定义了。 例如，设置[ `Style` ](xref:Xamarin.Forms.Style) ，用于设置[ `Label.TextColor` ](xref:Xamarin.Forms.Label.TextColor)到`Red`在应用程序级别将被重写由设置的页级别样式`Label.TextColor`到`Green`. 同样，将控件级别样式将页级别样式中重写。 此外, 如果`Label.TextColor`直接在控件属性上设置, 则这将优先于任何样式。

本节中的文章演示并说明如何创建和应用*显式*样式和*隐式*样式, 如何创建全局样式, 样式继承, 如何在运行时响应样式更改, 以及如何使用中包含的内置样式Xamarin。

> [!NOTE]
> **什么是 StyleId？**
>
> 在 Xamarin. Forms 2.2 之前, [`StyleId`](xref:Xamarin.Forms.Element.StyleId)属性用于识别应用程序中的单个元素, 用于标识 UI 测试中的标识和主题引擎 (如 Pixate)。 但是, Xamarin 2.2 引入[`AutomationId`](xref:Xamarin.Forms.Element.AutomationId)了属性, 该属性[`StyleId`](xref:Xamarin.Forms.Element.StyleId)取代了属性。

## <a name="related-links"></a>相关链接

- [XAML 标记扩展](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [样式](xref:Xamarin.Forms.Style)
- [资源库](xref:Xamarin.Forms.Setter)
