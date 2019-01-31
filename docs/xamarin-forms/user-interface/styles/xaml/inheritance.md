---
title: 在 Xamarin.Forms 中的样式继承
description: 样式可以继承其他样式以减少重复和使重复使用。 本文介绍如何在 Xamarin.Forms 应用程序中执行的样式继承。
ms.prod: xamarin
ms.assetid: 67A3A39C-8CC0-446D-8162-FFA73582D3B8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/17/2016
ms.openlocfilehash: bef48db93ae76346802b6569080bb1e54e3e51b3
ms.sourcegitcommit: 817d26585093cd180a36b28179eb354b0eb900b3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/30/2019
ms.locfileid: "55291942"
---
# <a name="style-inheritance-in-xamarinforms"></a>在 Xamarin.Forms 中的样式继承

[![下载示例](~/media/shared/download.png)下载示例](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Styles/BasicStyles/)

_样式可以继承其他样式以减少重复和使重复使用。_

## <a name="style-inheritance-in-xaml"></a>在 XAML 中的样式继承

通过设置执行的样式继承[ `Style.BasedOn` ](xref:Xamarin.Forms.Style.BasedOn)属性设置为现有[ `Style` ](xref:Xamarin.Forms.Style)。 在 XAML，这通过设置来实现`BasedOn`属性设置为`StaticResource`引用以前创建的标记扩展`Style`。 在 C# 中，这通过设置来实现`BasedOn`属性设置为`Style`实例。

继承自基样式的样式可以包括[ `Setter` ](xref:Xamarin.Forms.Setter)实例的新属性，或使用它们来重写基准样式的样式。 此外，相同类型或派生自基本样式所针对的类型的类型，必须针对继承自基样式的样式。 例如，如果基本样式面向[ `View` ](xref:Xamarin.Forms.View)实例，可以针对基于的基本样式的样式`View`实例或派生的类型`View`类，如[ `Label`](xref:Xamarin.Forms.Label)并[ `Button` ](xref:Xamarin.Forms.Button)实例。

下面的代码演示*显式*设置在 XAML 页面中的样式继承：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Styles.StyleInheritancePage" Title="Inheritance" Icon="xaml.png">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style x:Key="baseStyle" TargetType="View">
                <Setter Property="HorizontalOptions"
                        Value="Center" />
                <Setter Property="VerticalOptions"
                        Value="CenterAndExpand" />
            </Style>
            <Style x:Key="labelStyle" TargetType="Label"
                   BasedOn="{StaticResource baseStyle}">
                ...
                <Setter Property="TextColor" Value="Teal" />
            </Style>
            <Style x:Key="buttonStyle" TargetType="Button"
                   BasedOn="{StaticResource baseStyle}">
                <Setter Property="BorderColor" Value="Lime" />
                ...
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>
    <ContentPage.Content>
        <StackLayout Padding="0,20,0,0">
            <Label Text="These labels"
                   Style="{StaticResource labelStyle}" />
            ...
            <Button Text="So is the button"
                    Style="{StaticResource buttonStyle}" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

`baseStyle`目标[ `View` ](xref:Xamarin.Forms.View)实例，并设置[ `HorizontalOptions` ](xref:Xamarin.Forms.View.HorizontalOptions)并[ `VerticalOptions` ](xref:Xamarin.Forms.View.VerticalOptions)属性。 `baseStyle`未直接在任何控件上设置。 相反，`labelStyle`和`buttonStyle`从它继承，设置其他可绑定属性值。 `labelStyle`并`buttonStyle`然后应用于[ `Label` ](xref:Xamarin.Forms.Label)实例并[ `Button` ](xref:Xamarin.Forms.Button)实例，通过设置其[ `Style` ](xref:Xamarin.Forms.VisualElement.Style)属性。 这会导致下面的屏幕截图中所示的外观：

[![](inheritance-images/style-inheritance.png)](inheritance-images/style-inheritance-large.png#lightbox)

> [!NOTE]
> 隐式样式可以派生自显式样式，但不能被显式样式派生的隐式样式。

### <a name="respecting-the-inheritance-chain"></a>遵循继承链

一种样式只能继承自同一级别或更高版本，样式中的视图层次结构。 这表示：

- 应用程序级资源只能从其他应用程序级别资源继承。
- 页级别资源可以从应用程序级别资源及其他页级别资源继承。
- 控制级别的资源可以从应用程序级别资源、 页级别资源和其他控制级别资源继承。

在下面的代码示例演示此继承链：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Styles.StyleInheritancePage" Title="Inheritance" Icon="xaml.png">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style x:Key="baseStyle" TargetType="View">
              ...
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>
    <ContentPage.Content>
        <StackLayout Padding="0,20,0,0">
            <StackLayout.Resources>
                <ResourceDictionary>
                    <Style x:Key="labelStyle" TargetType="Label" BasedOn="{StaticResource baseStyle}">
                      ...
                    </Style>
                    <Style x:Key="buttonStyle" TargetType="Button" BasedOn="{StaticResource baseStyle}">
                      ...
                    </Style>
                </ResourceDictionary>
            </StackLayout.Resources>
            ...
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

在此示例中，`labelStyle`并`buttonStyle`是控制级别的资源，而`baseStyle`是一个页级别资源。 然而，尽管`labelStyle`并`buttonStyle`继承`baseStyle`，不可能`baseStyle`继承`labelStyle`或`buttonStyle`，因为视图层次结构中其各自的位置。

## <a name="style-inheritance-in-c35"></a>在 C 中的样式继承&#35;

等效的 C# 页上，其中[ `Style` ](xref:Xamarin.Forms.Style)实例直接分配给[ `Style` ](xref:Xamarin.Forms.VisualElement.Style)必选控件的属性下面的代码示例中所示：

```csharp
public class StyleInheritancePageCS : ContentPage
{
    public StyleInheritancePageCS ()
    {
        var baseStyle = new Style (typeof(View)) {
            Setters = {
                new Setter {
                    Property = View.HorizontalOptionsProperty, Value = LayoutOptions.Center    },
                ...
            }
        };

        var labelStyle = new Style (typeof(Label)) {
            BasedOn = baseStyle,
            Setters = {
                ...
                new Setter { Property = Label.TextColorProperty, Value = Color.Teal    }
            }
        };

        var buttonStyle = new Style (typeof(Button)) {
            BasedOn = baseStyle,
            Setters = {
                new Setter { Property = Button.BorderColorProperty, Value =    Color.Lime },
                ...
            }
        };
        ...

        Content = new StackLayout {
            Children = {
                new Label { Text = "These labels", Style = labelStyle },
                ...
                new Button { Text = "So is the button", Style = buttonStyle }
            }
        };
    }
}
```

`baseStyle`目标[ `View` ](xref:Xamarin.Forms.View)实例，并设置[ `HorizontalOptions` ](xref:Xamarin.Forms.View.HorizontalOptions)并[ `VerticalOptions` ](xref:Xamarin.Forms.View.VerticalOptions)属性。 `baseStyle`未直接在任何控件上设置。 相反，`labelStyle`和`buttonStyle`从它继承，设置其他可绑定属性值。 `labelStyle`并`buttonStyle`然后应用于[ `Label` ](xref:Xamarin.Forms.Label)实例并[ `Button` ](xref:Xamarin.Forms.Button)实例，通过设置其[ `Style` ](xref:Xamarin.Forms.VisualElement.Style)属性。

## <a name="related-links"></a>相关链接

- [XAML 标记扩展](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [基本样式 （示例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Styles/BasicStyles/)
- [使用样式 （示例）](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithStyles/)
- [ResourceDictionary](xref:Xamarin.Forms.ResourceDictionary)
- [样式](xref:Xamarin.Forms.Style)
- [资源库](xref:Xamarin.Forms.Setter)
