---
title: 在 Xamarin.Forms 中的全局样式
description: 样式可全局添加到应用程序的资源字典。 这有助于避免跨页或控件的样式的重复项。
ms.prod: xamarin
ms.assetid: BDC65F82-65E0-4C8E-BB91-8E340EB2D15A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/17/2016
ms.openlocfilehash: 665f5d1653b74997519149cef68e0882f476179d
ms.sourcegitcommit: b23a107b0fe3d2f814ae35b52a5855b6ce2a3513
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2019
ms.locfileid: "65924615"
---
# <a name="global-styles-in-xamarinforms"></a>在 Xamarin.Forms 中的全局样式

[![下载示例](~/media/shared/download.png)下载示例](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Styles/BasicStyles/)

_样式可全局添加到应用程序的资源字典。这有助于避免跨页或控件的样式的重复项。_

## <a name="create-a-global-style-in-xaml"></a>在 XAML 中创建全局样式

默认情况下，从模板创建的所有 Xamarin.Forms 应用程序都使用“App”类来实现 [`Application`](xref:Xamarin.Forms.Application) 子类。 若要声明[ `Style` ](xref:Xamarin.Forms.Style)在应用程序级别，在应用程序的[ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)使用 XAML，默认值**应用**类必须替换 XAML**应用**类和关联的代码隐藏。 有关详细信息，请参阅[使用 App 类](~/xamarin-forms/app-fundamentals/application-class.md)。

下面的代码示例演示[ `Style` ](xref:Xamarin.Forms.Style)在应用程序级别声明：

```xaml
<Application xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Styles.App">
    <Application.Resources>
        <ResourceDictionary>
            <Style x:Key="buttonStyle" TargetType="Button">
                <Setter Property="HorizontalOptions" Value="Center" />
                <Setter Property="VerticalOptions" Value="CenterAndExpand" />
                <Setter Property="BorderColor" Value="Lime" />
                <Setter Property="BorderRadius" Value="5" />
                <Setter Property="BorderWidth" Value="5" />
                <Setter Property="WidthRequest" Value="200" />
                <Setter Property="TextColor" Value="Teal" />
            </Style>
        </ResourceDictionary>
    </Application.Resources>
</Application>
```

这[ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)定义单个*显式*样式`buttonStyle`，这将用于设置的外观[ `Button` ](xref:Xamarin.Forms.Button)实例。 但是，可以全局样式*显式*或*隐式*。

下面的代码示例显示了 XAML 页应用`buttonStyle`向该页面的[ `Button` ](xref:Xamarin.Forms.Button)实例：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Styles.ApplicationStylesPage" Title="Application" Icon="xaml.png">
    <ContentPage.Content>
        <StackLayout Padding="0,20,0,0">
            <Button Text="These buttons" Style="{StaticResource buttonStyle}" />
            <Button Text="are demonstrating" Style="{StaticResource buttonStyle}" />
            <Button Text="application style overrides" Style="{StaticResource buttonStyle}" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

这会导致下面的屏幕截图中所示的外观：

[![](application-images/application-styles-1.png "全局样式示例")](application-images/application-styles-1-large.png#lightbox "全局样式示例")

有关创建在页面的样式信息[ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)，请参阅[显式样式](~/xamarin-forms/user-interface/styles/explicit.md)并[隐式样式](~/xamarin-forms/user-interface/styles/implicit.md)。

### <a name="override-styles"></a>重写样式

样式的视图层次结构中较低级别优先于更高版本定义了。 例如，设置[ `Style` ](xref:Xamarin.Forms.Style) ，用于设置[ `Button.TextColor` ](xref:Xamarin.Forms.Button.TextColor)到`Red`在应用程序级别将被重写由设置的页级别样式`Button.TextColor`到`Green`. 同样，将控件级别样式将页级别样式中重写。 此外，如果`Button.TextColor`设置直接上的控件属性，这将优先于任何样式。 在下面的代码示例演示此优先顺序：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Styles.ApplicationStylesPage" Title="Application" Icon="xaml.png">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style x:Key="buttonStyle" TargetType="Button">
                ...
                <Setter Property="TextColor" Value="Red" />
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>
    <ContentPage.Content>
        <StackLayout Padding="0,20,0,0">
            <StackLayout.Resources>
                <ResourceDictionary>
                    <Style x:Key="buttonStyle" TargetType="Button">
                        ...
                        <Setter Property="TextColor" Value="Blue" />
                    </Style>
                </ResourceDictionary>
            </StackLayout.Resources>
            <Button Text="These buttons" Style="{StaticResource buttonStyle}" />
            <Button Text="are demonstrating" Style="{StaticResource buttonStyle}" />
            <Button Text="application style overrides" Style="{StaticResource buttonStyle}" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

原始`buttonStyle`，在应用程序级别定义，通过重写`buttonStyle`页级别定义的实例。 此外，由控件级别重写页面级样式`buttonStyle`。 因此， [ `Button` ](xref:Xamarin.Forms.Button)实例显示带有蓝色文本，如以下屏幕截图中所示：

[![](application-images/application-styles-2.png "重写样式示例")](application-images/application-styles-2-large.png#lightbox "重写样式示例")

## <a name="create-a-global-style-in-c35"></a>在 C 中创建全局样式&#35;

[`Style`](xref:Xamarin.Forms.Style) 可以将实例添加到应用程序的[ `Resources` ](xref:Xamarin.Forms.VisualElement.Resources) C# 中通过创建一个新的集合[ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)，然后通过将添加`Style`实例到`ResourceDictionary`，作为下面的代码示例所示：

```csharp
public class App : Application
{
    public App ()
    {
        var buttonStyle = new Style (typeof(Button)) {
            Setters = {
                ...
                new Setter { Property = Button.TextColorProperty,    Value = Color.Teal }
            }
        };

        Resources = new ResourceDictionary ();
        Resources.Add ("buttonStyle", buttonStyle);
        ...
    }
    ...
}
```

构造函数定义单个*显式*用于将应用于样式[ `Button` ](xref:Xamarin.Forms.Button)整个应用程序的实例。 *显式* [ `Style` ](xref:Xamarin.Forms.Style)实例将添加到[ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)使用[ `Add` ](xref:Xamarin.Forms.ResourceDictionary.Add(System.String,System.Object))方法，指定`key`字符串来指代`Style`实例。 `Style`实例然后应用到任何应用程序中的正确类型的控件。 但是，可以全局样式*显式*或*隐式*。

下面的代码示例显示了 C# 页应用`buttonStyle`向该页面的[ `Button` ](xref:Xamarin.Forms.Button)实例：

```csharp
public class ApplicationStylesPageCS : ContentPage
{
    public ApplicationStylesPageCS ()
    {
        ...
        Content = new StackLayout {
            Children = {
                new Button { Text = "These buttons", Style = (Style)Application.Current.Resources ["buttonStyle"] },
                new Button { Text = "are demonstrating", Style = (Style)Application.Current.Resources ["buttonStyle"] },
                new Button { Text = "application styles", Style = (Style)Application.Current.Resources ["buttonStyle"]
                }
            }
        };
    }
}
```

`buttonStyle`应用于[ `Button` ](xref:Xamarin.Forms.Button)实例通过设置其[ `Style` ](xref:Xamarin.Forms.NavigableElement.Style)属性和控件的外观`Button`实例。

## <a name="related-links"></a>相关链接

- [XAML 标记扩展](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [基本样式 （示例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Styles/BasicStyles/)
- [使用样式 （示例）](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithStyles/)
- [ResourceDictionary](xref:Xamarin.Forms.ResourceDictionary)
- [样式](xref:Xamarin.Forms.Style)
- [资源库](xref:Xamarin.Forms.Setter)
