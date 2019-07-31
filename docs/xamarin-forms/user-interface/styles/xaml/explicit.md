---
title: 在 Xamarin.Forms 中的显式样式
description: 显式样式是指通过设置其样式属性有选择地应用于控件。 本文介绍如何使用 Xamarin.Forms 应用程序中的显式样式。
ms.prod: xamarin
ms.assetid: C0DF9F8F-B431-4374-A574-325BC3C41A3B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/17/2016
ms.openlocfilehash: 567985b1d6c6aff87e0a6879f05ab711a0095d6f
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2019
ms.locfileid: "68647205"
---
# <a name="explicit-styles-in-xamarinforms"></a>在 Xamarin.Forms 中的显式样式

[![下载示例](~/media/shared/download.png)下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-basicstyles)

_显式样式是指通过设置其样式属性有选择地应用于控件。_

## <a name="create-an-explicit-style-in-xaml"></a>在 XAML 中创建显式样式

若要声明[ `Style` ](xref:Xamarin.Forms.Style)级别的页[ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)必须添加到页面，然后一个或多个`Style`声明可以包含在`ResourceDictionary`。 一个`Style`由*显式*其声明，从而`x:Key`属性中为其提供描述性密钥`ResourceDictionary`。 *显式*样式必须然后将应用到特定的可视元素通过设置其[ `Style` ](xref:Xamarin.Forms.NavigableElement.Style)属性。

下面的代码示例演示*显式*中页面的 XAML 中声明样式`ResourceDictionary`并应用于页面的[ `Label` ](xref:Xamarin.Forms.Label)实例：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Styles.ExplicitStylesPage" Title="Explicit" IconImageSource="xaml.png">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style x:Key="labelRedStyle" TargetType="Label">
                <Setter Property="HorizontalOptions"
                        Value="Center" />
                <Setter Property="VerticalOptions"
                        Value="CenterAndExpand" />
                <Setter Property="FontSize" Value="Large" />
                <Setter Property="TextColor" Value="Red" />
            </Style>
            <Style x:Key="labelGreenStyle" TargetType="Label">
                ...
                <Setter Property="TextColor" Value="Green" />
            </Style>
            <Style x:Key="labelBlueStyle" TargetType="Label">
                ...
                <Setter Property="TextColor" Value="Blue" />
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>
    <ContentPage.Content>
        <StackLayout Padding="0,20,0,0">
            <Label Text="These labels"
                   Style="{StaticResource labelRedStyle}" />
            <Label Text="are demonstrating"
                   Style="{StaticResource labelGreenStyle}" />
            <Label Text="explicit styles,"
                   Style="{StaticResource labelBlueStyle}" />
            <Label Text="and an explicit style override"
                   Style="{StaticResource labelBlueStyle}"
                   TextColor="Teal" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

[ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)定义三个*显式*样式应用于页面的[ `Label` ](xref:Xamarin.Forms.Label)实例。 每个`Style`用于显示文本以不同的颜色，同时也要设置字体大小和水平和垂直布局选项。 每个`Style`应用到不同`Label`通过设置其[ `Style` ](xref:Xamarin.Forms.NavigableElement.Style)属性使用`StaticResource`标记扩展。 这会导致下面的屏幕截图中所示的外观：

[![](explicit-images/explicit-styles.png "显式样式示例")](explicit-images/explicit-styles-large.png#lightbox "显式样式示例")

此外，最终[ `Label` ](xref:Xamarin.Forms.Label)具有[ `Style` ](xref:Xamarin.Forms.Style)应用于它，但也会覆盖[ `TextColor` ](xref:Xamarin.Forms.Label.TextColor)属性设置为不同`Color`值。

### <a name="create-an-explicit-style-at-the-control-level"></a>在控件级别创建显式样式

除了创建之外*显式*页面级别的样式，则可以也在创建这些控件级别，如下面的代码示例中所示：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Styles.ExplicitStylesPage" Title="Explicit" IconImageSource="xaml.png">
    <ContentPage.Content>
        <StackLayout Padding="0,20,0,0">
            <StackLayout.Resources>
                <ResourceDictionary>
                    <Style x:Key="labelRedStyle" TargetType="Label">
                      ...
                    </Style>
                    ...
                </ResourceDictionary>
            </StackLayout.Resources>
            <Label Text="These labels" Style="{StaticResource labelRedStyle}" />
            ...
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

在此示例中，*显式* [ `Style` ](xref:Xamarin.Forms.Style)实例分配给[ `Resources` ](xref:Xamarin.Forms.VisualElement.Resources)系列[ `StackLayout` ](xref:Xamarin.Forms.StackLayout)控件。 然后可以将样式应用于控件及其子项。

有关创建在应用程序的样式信息[ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)，请参阅[全局样式](~/xamarin-forms/user-interface/styles/application.md)。

## <a name="create-an-explicit-style-in-c35"></a>在 C 中创建显式样式&#35;

[`Style`](xref:Xamarin.Forms.Style) 实例可以添加到页面的[ `Resources` ](xref:Xamarin.Forms.VisualElement.Resources) C# 中通过创建一个新的集合[ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)，然后通过将添加`Style`实例到`ResourceDictionary`，如中所示下面的代码示例：

```csharp
public class ExplicitStylesPageCS : ContentPage
{
    public ExplicitStylesPageCS ()
    {
        var labelRedStyle = new Style (typeof(Label)) {
            Setters = {
                ...
                new Setter { Property = Label.TextColorProperty, Value = Color.Red    }
            }
        };
        var labelGreenStyle = new Style (typeof(Label)) {
            Setters = {
                ...
                new Setter { Property = Label.TextColorProperty, Value = Color.Green }
            }
        };
        var labelBlueStyle = new Style (typeof(Label)) {
            Setters = {
                ...
                new Setter { Property = Label.TextColorProperty, Value = Color.Blue }
            }
        };

        Resources = new ResourceDictionary ();
        Resources.Add ("labelRedStyle", labelRedStyle);
        Resources.Add ("labelGreenStyle", labelGreenStyle);
        Resources.Add ("labelBlueStyle", labelBlueStyle);
        ...

        Content = new StackLayout {
            Children = {
                new Label { Text = "These labels",
                            Style = (Style)Resources ["labelRedStyle"] },
                new Label { Text = "are demonstrating",
                            Style = (Style)Resources ["labelGreenStyle"] },
                new Label { Text = "explicit styles,",
                            Style = (Style)Resources ["labelBlueStyle"] },
                new Label {    Text = "and an explicit style override",
                            Style = (Style)Resources ["labelBlueStyle"], TextColor = Color.Teal }
            }
        };
    }
}
```

构造函数定义三个*显式*应用于页面的样式[ `Label` ](xref:Xamarin.Forms.Label)实例。 每个*显式* [ `Style` ](xref:Xamarin.Forms.Style)添加到[ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)使用[ `Add` ](xref:Xamarin.Forms.ResourceDictionary.Add(System.String,System.Object))方法中，指定`key`字符串来指代`Style`实例。 每个`Style`应用到不同`Label`通过设置其[ `Style` ](xref:Xamarin.Forms.NavigableElement.Style)属性。

但是，没有使用没有优势[ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)此处。 相反， [ `Style` ](xref:Xamarin.Forms.Style)实例可以直接分配给[ `Style` ](xref:Xamarin.Forms.NavigableElement.Style)所需的可视元素的属性和`ResourceDictionary`可删除，在下面的示例所示代码示例：

```csharp
public class ExplicitStylesPageCS : ContentPage
{
    public ExplicitStylesPageCS ()
    {
        var labelRedStyle = new Style (typeof(Label)) {
            ...
        };
        var labelGreenStyle = new Style (typeof(Label)) {
            ...
        };
        var labelBlueStyle = new Style (typeof(Label)) {
            ...
        };
        ...
        Content = new StackLayout {
            Children = {
                new Label { Text = "These labels", Style = labelRedStyle },
                new Label { Text = "are demonstrating", Style = labelGreenStyle },
                new Label { Text = "explicit styles,", Style = labelBlueStyle },
                new Label { Text = "and an explicit style override", Style = labelBlueStyle,
                            TextColor = Color.Teal }
            }
        };
    }
}
```

构造函数定义三个*显式*应用于页面的样式[ `Label` ](xref:Xamarin.Forms.Label)实例。 每个`Style`用于显示文本以不同的颜色，同时也要设置字体大小和水平和垂直布局选项。 每个`Style`应用到不同`Label`通过设置其[ `Style` ](xref:Xamarin.Forms.NavigableElement.Style)属性。 此外，最终`Label`已`Style`应用，但也会覆盖`TextColor`属性设置为不同`Color`值。

## <a name="related-links"></a>相关链接

- [XAML 标记扩展](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [基本样式 （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-basicstyles)
- [使用样式 （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithstyles)
- [ResourceDictionary](xref:Xamarin.Forms.ResourceDictionary)
- [样式](xref:Xamarin.Forms.Style)
- [资源库](xref:Xamarin.Forms.Setter)
