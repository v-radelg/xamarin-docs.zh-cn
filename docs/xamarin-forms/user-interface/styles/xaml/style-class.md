---
title: Xamarin.Forms 样式类
description: Xamarin.Forms 的样式类，多个样式应用到控件，而不必求助于样式继承。
ms.prod: xamarin
ms.assetid: 4762401E-2B48-48F1-B6E4-61F7AF8AA46F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/30/2019
ms.openlocfilehash: dd749a4a78adbab5317f1ae5ca6334caa009b9b3
ms.sourcegitcommit: 9dcb7377dc92ad921285fbb857b0be13030bbea3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/03/2019
ms.locfileid: "55668545"
---
# <a name="xamarinforms-style-classes"></a>Xamarin.Forms 样式类

[![下载示例](~/media/shared/download.png)下载示例](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Styles/BasicStyles/)

_Xamarin.Forms 的样式类，多个样式应用到控件，而不必求助于样式继承。_

## <a name="create-style-classes"></a>创建样式类

可以通过设置创建的样式类[ `Class` ](xref:Xamarin.Forms.Style.Class)上的属性[ `Style` ](xref:Xamarin.Forms.Style)到`string`表示的类名。 这提供了对显式样式使用的定义的优势`x:Key`属性，请将多个样式类可应用于[ `VisualElement` ](xref:Xamarin.Forms.VisualElement)。

> [!IMPORTANT]
> 多个样式可以共享相同的类名称，提供它们针对不同类型。 这可使多个样式类，具有相同命名为到不同目标类型。

以下示例显示三个[ `BoxView` ](xref:Xamarin.Forms.BoxView)样式类，和一个[ `VisualElement` ](xref:Xamarin.Forms.VisualElement)样式类：

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <Style TargetType="BoxView"
               Class="Separator">
            <Setter Property="BackgroundColor"
                    Value="#CCCCCC" />
            <Setter Property="HeightRequest"
                    Value="1" />
        </Style>

        <Style TargetType="BoxView"
               Class="Rounded">
            <Setter Property="BackgroundColor"
                    Value="#1FAECE" />
            <Setter Property="HorizontalOptions"
                    Value="Start" />
            <Setter Property="CornerRadius"
                    Value="10" />
        </Style>    

        <Style TargetType="BoxView"
               Class="Circle">
            <Setter Property="BackgroundColor"
                    Value="#1FAECE" />
            <Setter Property="WidthRequest"
                    Value="100" />
            <Setter Property="HeightRequest"
                    Value="100" />
            <Setter Property="HorizontalOptions"
                    Value="Start" />
            <Setter Property="CornerRadius"
                    Value="50" />
        </Style>

        <Style TargetType="VisualElement"
               Class="Rotated"
               ApplyToDerivedTypes="true">
            <Setter Property="Rotation"
                    Value="45" />
        </Style>        
    </ContentPage.Resources>
</ContentPage>
```

`Separator`， `Rounded`，并`Circle`样式类每个集[ `BoxView` ](xref:Xamarin.Forms.BoxView)为特定值的属性。

`Rotated`样式类具有[ `TargetType` ](xref:Xamarin.Forms.Style.TargetType)的[ `VisualElement` ](xref:Xamarin.Forms.VisualElement)，这意味着它只能应用到`VisualElement`实例。 但是，其[ `ApplyToDerivedTypes` ](xref:Xamarin.Forms.Style.ApplyToDerivedTypes)属性设置为`true`，这可确保它可以应用于派生自任何控件`VisualElement`，如[ `BoxView` ](xref:Xamarin.Forms.BoxView)。 有关将样式应用于派生类型的详细信息，请参阅[将样式应用于派生类型](implicit.md#apply-a-style-to-derived-types)。

等效的 C# 代码是：

```csharp
var separatorBoxViewStyle = new Style(typeof(BoxView))
{
    Class = "Separator",
    Setters =
    {
        new Setter
        {
            Property = VisualElement.BackgroundColorProperty,
            Value = Color.FromHex("#CCCCCC")
        },
        new Setter
        {
            Property = VisualElement.HeightRequestProperty,
            Value = 1
        }
    }
};

var roundedBoxViewStyle = new Style(typeof(BoxView))
{
    Class = "Rounded",
    Setters =
    {
        new Setter
        {
            Property = VisualElement.BackgroundColorProperty,
            Value = Color.FromHex("#1FAECE")
        },
        new Setter
        {
            Property = View.HorizontalOptionsProperty,
            Value = LayoutOptions.Start
        },
        new Setter
        {
            Property = BoxView.CornerRadiusProperty,
            Value = 10
        }
    }
};

var circleBoxViewStyle = new Style(typeof(BoxView))
{
    Class = "Circle",
    Setters =
    {
        new Setter
        {
            Property = VisualElement.BackgroundColorProperty,
            Value = Color.FromHex("#1FAECE")
        },
        new Setter
        {
            Property = VisualElement.WidthRequestProperty,
            Value = 100
        },
        new Setter
        {
            Property = VisualElement.HeightRequestProperty,
            Value = 100
        },
        new Setter
        {
            Property = View.HorizontalOptionsProperty,
            Value = LayoutOptions.Start
        },
        new Setter
        {
            Property = BoxView.CornerRadiusProperty,
            Value = 50
        }
    }
};

var rotatedVisualElementStyle = new Style(typeof(VisualElement))
{
    Class = "Rotated",
    ApplyToDerivedTypes = true,
    Setters =
    {
        new Setter
        {
            Property = VisualElement.RotationProperty,
            Value = 45
        }
    }
};

Resources = new ResourceDictionary
{
    separatorBoxViewStyle,
    roundedBoxViewStyle,
    circleBoxViewStyle,
    rotatedVisualElementStyle
};
```

## <a name="consume-style-classes"></a>使用样式类

可以通过设置使用样式类[ `StyleClass` ](xref:Xamarin.Forms.VisualElement.StyleClass)控件，其类型的属性`IList<string>`，到样式类名的列表。 将应用的样式类，前提是控件的类型匹配[ `TargetType` ](xref:Xamarin.Forms.Style.TargetType)的样式类。

以下示例显示三个[ `BoxView` ](xref:Xamarin.Forms.BoxView)情况下，每个设置为不同的样式类：

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        ...
    </ContentPage.Resources>
    <StackLayout Margin="20">
        <BoxView StyleClass="Separator" />       
        <BoxView WidthRequest="100"
                 HeightRequest="100"
                 HorizontalOptions="Center"
                 StyleClass="Rounded, Rotated" />
        <BoxView HorizontalOptions="Center"
                 StyleClass="Circle" />
    </StackLayout>
</ContentPage>    
```

在此示例中，第一个[ `BoxView` ](xref:Xamarin.Forms.BoxView)的样式设置为将行分隔符，而第三个`BoxView`为圆形。 第二个`BoxView`具有两个样式类应用，它为 it 舍入角并将其旋转 45 度：

![](style-class-images/boxviews.png "BoxViews 样式的样式类")

> [!IMPORTANT]
> 多个样式类可以应用于控件，因为[ `StyleClass` ](xref:Xamarin.Forms.VisualElement.StyleClass)属性属于类型`IList<string>`。 当发生这种情况时，可以按列表按升序应用样式类。 因此，当多个样式类设置相同属性，最高的列表位置的样式类中的属性将优先。

等效的 C# 代码是：

```csharp
...
Content = new StackLayout
{
    Children =
    {
        new BoxView { StyleClass = new [] { "Separator" } },
        new BoxView { WidthRequest = 100, HeightRequest = 100, HorizontalOptions = LayoutOptions.Center, StyleClass = new [] { "Rounded", "Rotated" } },
        new BoxView { HorizontalOptions = LayoutOptions.Center, StyleClass = new [] { "Circle" } }
    }
};
```

## <a name="related-links"></a>相关链接

- [基本样式 （示例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Styles/BasicStyles/)
