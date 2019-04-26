---
title: 创建 XAML 标记扩展
description: 本文介绍如何定义自己的自定义 Xamarin.Forms XAML 标记扩展。 XAML 标记扩展是一个类，实现 IMarkupExtension 或 IMarkupExtension<T>接口。
ms.prod: xamarin
ms.assetid: 797C1EF9-1C8E-4208-8610-9B79CCF17D46
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/05/2018
ms.openlocfilehash: e69d4b9dcf93c095804c5ac46527c03049580d1c
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61178063"
---
# <a name="creating-xaml-markup-extensions"></a>创建 XAML 标记扩展

[![下载示例](~/media/shared/download.png)下载示例](https://developer.xamarin.com/samples/xamarin-forms/XAML/MarkupExtensions/)

在以编程方式的级别中，XAML 标记扩展是实现的类[ `IMarkupExtension` ](xref:Xamarin.Forms.Xaml.IMarkupExtension)或[ `IMarkupExtension<T>` ](xref:Xamarin.Forms.Xaml.IMarkupExtension`1)接口。 你可以浏览在如下所述的标准标记扩展的源代码[ **MarkupExtensions** directory](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Xaml/MarkupExtensions)的 Xamarin.Forms GitHub 存储库。

还有可能通过派生自定义您自己自定义的 XAML 标记扩展`IMarkupExtension`或`IMarkupExtension<T>`。 如果标记扩展获取特定类型的值，使用泛型窗体。 这是 Xamarin.Forms 标记扩展的多个用例：

- `TypeExtension` 派生自 `IMarkupExtension<Type>`
- `ArrayExtension` 派生自 `IMarkupExtension<Array>`
- `DynamicResourceExtension` 派生自 `IMarkupExtension<DynamicResource>`
- `BindingExtension` 派生自 `IMarkupExtension<BindingBase>`
- `ConstraintExpression` 派生自 `IMarkupExtension<Constraint>`

这两个`IMarkupExtension`接口定义每个，只有一个方法名为`ProvideValue`:

```csharp
public interface IMarkupExtension
{
    object ProvideValue(IServiceProvider serviceProvider);
}

public interface IMarkupExtension<out T> : IMarkupExtension
{
    new T ProvideValue(IServiceProvider serviceProvider);
}
```

由于`IMarkupExtension<T>`派生自`IMarkupExtension`并包含`new`上的关键字`ProvideValue`，它同时包含`ProvideValue`方法。

通常，XAML 标记扩展的返回值定义参与的属性。 (明显的例外是`NullExtension`，在其中`ProvideValue`只需返回`null`。)`ProvideValue`方法具有一个参数类型的`IServiceProvider`将稍后在本文中讨论的。

## <a name="a-markup-extension-for-specifying-color"></a>用于指定颜色标记扩展

下面的 XAML 标记扩展使你可以构造`Color`值使用色调、 饱和度和亮度的组件。 它定义的颜色，包括 alpha 分量，将初始化为 1 的四个组件的四个属性。 类派生自`IMarkupExtension<Color>`以指示`Color`返回值：

```csharp
public class HslColorExtension : IMarkupExtension<Color>
{
    public double H { set; get; }

    public double S { set; get; }

    public double L { set; get; }

    public double A { set; get; } = 1.0;

    public Color ProvideValue(IServiceProvider serviceProvider)
    {
        return Color.FromHsla(H, S, L, A);
    }

    object IMarkupExtension.ProvideValue(IServiceProvider serviceProvider)
    {
        return (this as IMarkupExtension<Color>).ProvideValue(serviceProvider);
    }
}
```

因为`IMarkupExtension<T>`派生自`IMarkupExtension`，类必须包含两个`ProvideValue`方法，即： 返回`Color`，另一个返回`object`，但第二种方法只需调用第一种方法。

**HSL 颜色演示**页上显示不同的方式`HslColorExtension`可以在指定的颜色的 XAML 文件中出现`BoxView`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:MarkupExtensions"
             x:Class="MarkupExtensions.HslColorDemoPage"
             Title="HSL Color Demo">

    <ContentPage.Resources>
        <ResourceDictionary>
            <Style TargetType="BoxView">
                <Setter Property="WidthRequest" Value="80" />
                <Setter Property="HeightRequest" Value="80" />
                <Setter Property="HorizontalOptions" Value="Center" />
                <Setter Property="VerticalOptions" Value="CenterAndExpand" />
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>

    <StackLayout>
        <BoxView>
            <BoxView.Color>
                <local:HslColorExtension H="0" S="1" L="0.5" A="1" />
            </BoxView.Color>
        </BoxView>

        <BoxView>
            <BoxView.Color>
                <local:HslColor H="0.33" S="1" L="0.5" />
            </BoxView.Color>
        </BoxView>

        <BoxView Color="{local:HslColorExtension H=0.67, S=1, L=0.5}" />

        <BoxView Color="{local:HslColor H=0, S=0, L=0.5}" />

        <BoxView Color="{local:HslColor A=0.5}" />
    </StackLayout>
</ContentPage>
```

请注意，当`HslColorExtension`是一个 XML 标记，四个属性设置为属性，但它出现时大括号之间，由不带引号的逗号分隔的四个属性。 默认值为`H`， `S`，并`L`为 0 和的默认值`A`为 1，因此可以省略这些属性，如果你希望它们设置为默认值。 最后一个示例演示其中亮度为 0，这通常会导致黑色，但 alpha 通道是 0.5，因此它是半双工透明的将显示一个示例与白色背景页的灰色：

[![HSL 颜色演示](creating-images/hslcolordemo-small.png "HSL 颜色演示")](creating-images/hslcolordemo-large.png#lightbox "HSL 颜色演示")

## <a name="a-markup-extension-for-accessing-bitmaps"></a>用于访问位图标记扩展

参数`ProvideValue`是一个对象，实现[ `IServiceProvider` ](xref:System.IServiceProvider)接口，在.NET 中定义`System`命名空间。 此接口具有一个成员，一个名为方法`GetService`与`Type`参数。

`ImageResourceExtension`如下所示的类演示的可能用途之一`IServiceProvider`并`GetService`若要获取`IXmlLineInfoProvider`对象可提供行和字符，该值指示在其中检测到特定错误的信息。 在这种情况下，会引发的异常时`Source`未设置属性：

```csharp
[ContentProperty("Source")]
class ImageResourceExtension : IMarkupExtension<ImageSource>
{
    public string Source { set; get; }

    public ImageSource ProvideValue(IServiceProvider serviceProvider)
    {
        if (String.IsNullOrEmpty(Source))
        {
            IXmlLineInfoProvider lineInfoProvider = serviceProvider.GetService(typeof(IXmlLineInfoProvider)) as IXmlLineInfoProvider;
            IXmlLineInfo lineInfo = (lineInfoProvider != null) ? lineInfoProvider.XmlLineInfo : new XmlLineInfo();
            throw new XamlParseException("ImageResourceExtension requires Source property to be set", lineInfo);
        }

        string assemblyName = GetType().GetTypeInfo().Assembly.GetName().Name;
        return ImageSource.FromResource(assemblyName + "." + Source, typeof(ImageResourceExtension).GetTypeInfo().Assembly);
    }

    object IMarkupExtension.ProvideValue(IServiceProvider serviceProvider)
    {
        return (this as IMarkupExtension<ImageSource>).ProvideValue(serviceProvider);
    }
}
```

`ImageResourceExtension` 当需要访问作为嵌入资源的.NET Standard 库项目中存储的图像文件的 XAML 文件时非常有用。 它使用`Source`属性来调用静态`ImageSource.FromResource`方法。 此方法要求完全限定资源名称，其中包含的程序集名称、 文件夹名称和用句点分隔的文件名。 第二个参数`ImageSource.FromResource`方法提供的程序集名称，并仅为所需的发行版本 UWP 上。 无论如何，`ImageSource.FromResource`必须从包含位图，这意味着除非图像还在该库中，此 XAML 资源扩展不能为外部库的一部分的程序集调用。 (请参阅[**嵌入图像**](~/xamarin-forms/user-interface/images.md#embedded-images)访问位图作为嵌入资源存储的详细信息的文章。)

尽管`ImageResourceExtension`需要`Source`属性设置，`Source`属性指示在属性中为类的内容属性。 这意味着，`Source=`可以省略大括号中的表达式的一部分。 在中**图像资源演示**页上，`Image`元素提取使用文件夹名称和文件名用句点分隔的两个映像：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:MarkupExtensions"
             x:Class="MarkupExtensions.ImageResourceDemoPage"
             Title="Image Resource Demo">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="*" />
            <RowDefinition Height="*" />
        </Grid.RowDefinitions>

        <Image Source="{local:ImageResource Images.SeatedMonkey.jpg}"
               Grid.Row="0" />

        <Image Source="{local:ImageResource Images.FacePalm.jpg}"
               Grid.Row="1" />

    </Grid>
</ContentPage>
```

下面是运行的程序：

[![图像资源演示](creating-images/imageresourcedemo-small.png "图像资源演示")](creating-images/imageresourcedemo-large.png#lightbox "图像资源演示")

## <a name="service-providers"></a>服务提供商

通过使用`IServiceProvider`自变量`ProvideValue`，XAML 标记扩展有权访问在其中使用这些 XAML 文件有关的有用信息。 但若要使用`IServiceProvider`参数成功，您需要知道什么类型的服务是在特定上下文中可用。 若要了解此功能的最佳方法是通过研究现有 XAML 标记扩展中的源代码[ **MarkupExtensions**文件夹](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Xaml/MarkupExtensions)在 GitHub 上的 Xamarin.Forms 存储库中。 请注意某些类型的服务是 Xamarin.Forms 的内部。

在某些 XAML 标记扩展中，此服务可能会很有用：

```csharp
 IProvideValueTarget provideValueTarget = serviceProvider.GetService(typeof(IProvideValueTarget)) as IProvideValueTarget;
```

`IProvideValueTarget`接口定义两个属性`TargetObject`和`TargetProperty`。 在获得此信息`ImageResourceExtension`类，`TargetObject`是`Image`并`TargetProperty`是`BindableProperty`对象`Source`属性`Image`。 这是在其设置 XAML 标记扩展的属性。

`GetService`使用的自变量调用`typeof(IProvideValueTarget)`实际返回的类型的对象`SimpleValueTargetProvider`，其定义中`Xamarin.Forms.Xaml.Internals`命名空间。 如果强制转换的返回值`GetService`为该类型，你可以访问`ParentObjects`属性，它是一个数组，包含`Image`元素，`Grid`父项，并`ImageResourceDemoPage`的父级`Grid`。

## <a name="conclusion"></a>结束语

XAML 标记扩展通过扩展从各种源中设置属性的功能，在 XAML 中扮演重要角色。 此外，如果现有的 XAML 标记扩展未提供所需内容，您还可以编写您自己。

## <a name="related-links"></a>相关链接

- [标记扩展 （示例）](https://developer.xamarin.com/samples/xamarin-forms/XAML/MarkupExtensions/)
- [从 Xamarin.Forms 书籍的 XAML 标记扩展一章](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter10.md)
