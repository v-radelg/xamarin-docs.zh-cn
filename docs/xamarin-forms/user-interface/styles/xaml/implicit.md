---
title: 在 Xamarin.Forms 中的隐式样式
description: 隐式样式是指可供所有控件的相同的目标类型，而无需每个控件以引用样式。
ms.prod: xamarin
ms.assetid: 02A75F3B-4389-49D4-A2F4-AFD473A4A161
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/30/2019
ms.openlocfilehash: cdbfaafdac8f965adaf4b840b568154e40ef7e10
ms.sourcegitcommit: c9651cad80c2865bc628349d30e82721c01ddb4a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2019
ms.locfileid: "70228182"
---
# <a name="implicit-styles-in-xamarinforms"></a>在 Xamarin.Forms 中的隐式样式

[![下载示例](~/media/shared/download.png)下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-basicstyles)

_隐式样式是指可供所有控件的相同的目标类型，而无需每个控件以引用样式。_

## <a name="create-an-implicit-style-in-xaml"></a>在 XAML 中创建隐式样式

若要声明[ `Style` ](xref:Xamarin.Forms.Style)级别的页[ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)必须添加到页面，然后一个或多个`Style`声明可以包含在`ResourceDictionary`。 一个`Style`由*隐式*通过不指定`x:Key`属性。 然后将对匹配的可视元素应用样式`TargetType`确实如此，但不适用于元素派生自`TargetType`值。

下面的代码示例演示*隐式*中页面的 XAML 中声明样式`ResourceDictionary`，并应用于页面的[ `Entry` ](xref:Xamarin.Forms.Entry)实例：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" xmlns:local="clr-namespace:Styles;assembly=Styles" x:Class="Styles.ImplicitStylesPage" Title="Implicit" IconImageSource="xaml.png">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style TargetType="Entry">
                <Setter Property="HorizontalOptions" Value="Fill" />
                <Setter Property="VerticalOptions" Value="CenterAndExpand" />
                <Setter Property="BackgroundColor" Value="Yellow" />
                <Setter Property="FontAttributes" Value="Italic" />
                <Setter Property="TextColor" Value="Blue" />
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>
    <ContentPage.Content>
        <StackLayout Padding="0,20,0,0">
            <Entry Text="These entries" />
            <Entry Text="are demonstrating" />
            <Entry Text="implicit styles," />
            <Entry Text="and an implicit style override" BackgroundColor="Lime" TextColor="Red" />
            <local:CustomEntry Text="Subclassed Entry is not receiving the style" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

[ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)定义单个*隐式*样式应用于页面的[ `Entry` ](xref:Xamarin.Forms.Entry)实例。 `Style`用于显示黄色背景，蓝色文本，同时也要设置其他外观选项。 `Style`添加到页面的[ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)而无需指定`x:Key`属性。 因此，`Style`适用于所有`Entry`隐式实例，因为它们匹配[ `TargetType` ](xref:Xamarin.Forms.Style.TargetType)属性`Style`完全。 但是，`Style`不应用于`CustomEntry`实例，这是子类化`Entry`。 这会导致如以下屏幕截图中所示的外观：

[![隐式样式示例](implicit-images/implicit-styles.png)](implicit-images/implicit-styles-large.png#lightbox)

此外，第四个[ `Entry` ](xref:Xamarin.Forms.Entry)重写[ `BackgroundColor` ](xref:Xamarin.Forms.VisualElement.BackgroundColor)并[ `TextColor` ](xref:Xamarin.Forms.Entry.TextColor)属性隐式样式应用到不同`Color`值。

### <a name="create-an-implicit-style-at-the-control-level"></a>在控件级别创建隐式样式

除了创建之外*隐式*页面级别的样式，则可以也在创建这些控件级别，如下面的代码示例中所示：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" xmlns:local="clr-namespace:Styles;assembly=Styles" x:Class="Styles.ImplicitStylesPage" Title="Implicit" IconImageSource="xaml.png">
    <ContentPage.Content>
        <StackLayout Padding="0,20,0,0">
            <StackLayout.Resources>
                <ResourceDictionary>
                    <Style TargetType="Entry">
                        <Setter Property="HorizontalOptions" Value="Fill" />
                        ...
                    </Style>
                </ResourceDictionary>
            </StackLayout.Resources>
            <Entry Text="These entries" />
            ...
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

在此示例中，*隐式* [ `Style` ](xref:Xamarin.Forms.Style)分配给[ `Resources` ](xref:Xamarin.Forms.VisualElement.Resources)系列[ `StackLayout` ](xref:Xamarin.Forms.StackLayout)控件。 *隐式*样式然后可以应用于控件及其子项。

有关创建在应用程序的样式信息[ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)，请参阅[全局样式](~/xamarin-forms/user-interface/styles/application.md)。

## <a name="create-an-implicit-style-in-c35"></a>在 C 中创建隐式样式&#35;

[`Style`](xref:Xamarin.Forms.Style) 实例可以添加到页面的[ `Resources` ](xref:Xamarin.Forms.VisualElement.Resources) C# 中通过创建一个新的集合[ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)，然后通过将添加`Style`实例到`ResourceDictionary`，如中所示下面的代码示例：

```csharp
public class ImplicitStylesPageCS : ContentPage
{
    public ImplicitStylesPageCS ()
    {
        var entryStyle = new Style (typeof(Entry)) {
            Setters = {
                ...
                new Setter { Property = Entry.TextColorProperty, Value = Color.Blue }
            }
        };

        ...
        Resources = new ResourceDictionary ();
        Resources.Add (entryStyle);

        Content = new StackLayout {
            Children = {
                new Entry { Text = "These entries" },
                new Entry { Text = "are demonstrating" },
                new Entry { Text = "implicit styles," },
                new Entry { Text = "and an implicit style override", BackgroundColor = Color.Lime, TextColor = Color.Red },
                new CustomEntry  { Text = "Subclassed Entry is not receiving the style" }
            }
        };
    }
}
```

构造函数定义单个*隐式*应用于页面的样式[ `Entry` ](xref:Xamarin.Forms.Entry)实例。 `Style`用于显示黄色背景，蓝色文本，同时也要设置其他外观选项。 `Style`添加到页面的[ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)而无需指定`key`字符串。 因此，`Style`适用于所有`Entry`隐式实例，因为它们匹配[ `TargetType` ](xref:Xamarin.Forms.Style.TargetType)属性`Style`完全。 但是，`Style`不应用于`CustomEntry`实例，这是子类化`Entry`。

## <a name="apply-a-style-to-derived-types"></a>将样式应用于派生类型

属性允许将样式应用于从[`TargetType`](xref:Xamarin.Forms.Style.TargetType)属性引用的基类型派生的控件。 [`Style.ApplyToDerivedTypes`](xref:Xamarin.Forms.Style.ApplyToDerivedTypes) 因此, 将此属性设置`true`为可启用单个样式以面向多个类型, 前提是这些类型派生自在`TargetType`属性中指定的基类型。

下面的示例演示了一个隐式样式, 该样式将[`Button`](xref:Xamarin.Forms.Button)实例的背景色设置为红色:

```xaml
<Style TargetType="Button"
       ApplyToDerivedTypes="True">
    <Setter Property="BackgroundColor"
            Value="Red" />
</Style>
```

将此样式置于页面级别[`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)将导致其应用于页面上的所有[`Button`](xref:Xamarin.Forms.Button)实例, 还会应用于从`Button`派生的任何控件。 但是, 如果[`ApplyToDerivedTypes`](xref:Xamarin.Forms.Style.ApplyToDerivedTypes)属性仍未设置, 则样式仅`Button`应用于实例。

等效 C# 代码如下：

```csharp
var buttonStyle = new Style(typeof(Button))
{
    ApplyToDerivedTypes = true,
    Setters =
    {
        new Setter
        {
            Property = VisualElement.BackgroundColorProperty,
            Value = Color.Red
        }
    }
};

Resources = new ResourceDictionary { buttonStyle };
```

## <a name="related-links"></a>相关链接

- [XAML 标记扩展](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [基本样式 （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-basicstyles)
- [使用样式 （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithstyles)
- [ResourceDictionary](xref:Xamarin.Forms.ResourceDictionary)
- [样式](xref:Xamarin.Forms.Style)
- [资源库](xref:Xamarin.Forms.Setter)
