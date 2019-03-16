---
title: 第 3 部分。 XAML 标记扩展
description: XAML 标记扩展构成允许将属性设置为对象或从其他源间接引用的值的 XAML 中的重要功能。
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: F4A37564-B18B-42FF-B841-9A1949895AB6
author: davidbritch
ms.author: dabritch
ms.date: 03/27/2018
ms.openlocfilehash: 17ca8ec481b8af5ad0515e6544613864f0a66271
ms.sourcegitcommit: 650458de1d362cd7de174cacef7838f0e74426f3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/15/2019
ms.locfileid: "57981765"
---
# <a name="part-3-xaml-markup-extensions"></a>第 3 部分。 XAML 标记扩展

[![下载示例](~/media/shared/download.png)下载示例](https://developer.xamarin.com/samples/xamarin-forms/XamlSamples/)

_XAML 标记扩展构成允许将属性设置为对象或从其他源间接引用的值的 XAML 中的重要功能。XAML 标记扩展是特别重要的共享对象，并引用整个应用程序，使用的常量，但它们在数据绑定中查找其最大的实用程序。_

## <a name="xaml-markup-extensions"></a>XAML 标记扩展

一般情况下，使用 XAML 将对象的属性设置为显式值，如字符串、 数字、 枚举成员或转换为在后台值的字符串。

有时，但是，属性必须改为引用其他，某一位置定义的值，或可能由代码在运行时需要很少的处理。 出于这些目的，XAML*标记扩展*可用。

这些 XAML 标记扩展不是扩展的 XML。 XAML 是完全合法的 XML。 它们称为"扩展"因为它们由代码中实现的类支持`IMarkupExtension`。 您可以编写自己的自定义标记扩展。

在许多情况下，XAML 标记扩展是 XAML 文件中立即认出因为它们将显示由大括号分隔的属性设置为: {和}，但有时标记扩展将出现在标记为传统的元素。

## <a name="shared-resources"></a>共享的资源

某些 XAML 页属性设置为相同的值包含多个视图。 例如，许多这些属性设置`Button`对象是否相同：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.SharedResourcesPage"
             Title="Shared Resources Page">

    <StackLayout>
        <Button Text="Do this!"
                HorizontalOptions="Center"
                VerticalOptions="CenterAndExpand"
                BorderWidth="3"
                Rotation="-15"
                TextColor="Red"
                FontSize="24" />

        <Button Text="Do that!"
                HorizontalOptions="Center"
                VerticalOptions="CenterAndExpand"
                BorderWidth="3"
                Rotation="-15"
                TextColor="Red"
                FontSize="24" />

        <Button Text="Do the other thing!"
                HorizontalOptions="Center"
                VerticalOptions="CenterAndExpand"
                BorderWidth="3"
                Rotation="-15"
                TextColor="Red"
                FontSize="24" />

    </StackLayout>
</ContentPage>
```

如果其中一个属性需要更改，可能想要进行的更改只需一次而不是三次。 如果这是代码，您将可能使用常量和静态只读对象来帮助保持一致且轻松地修改此类值。

在 XAML，一种受欢迎的解决方案是将存储此类值或对象中*资源字典*。 `VisualElement`类定义一个名为属性`Resources`类型的`ResourceDictionary`，这是类型的键的字典`string`的类型和值`object`。 可以将对象放入此字典，然后从标记中，所有在 XAML 中引用它们。

若要在页面上使用的资源字典，包含一对`Resources`属性元素标记。 它是最方便的方式将这些页面的顶部：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.SharedResourcesPage"
             Title="Shared Resources Page">

    <ContentPage.Resources>

    </ContentPage.Resources>
    ...
</ContentPage>
```

还有必要显式包括`ResourceDictionary`标记：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.SharedResourcesPage"
             Title="Shared Resources Page">

    <ContentPage.Resources>
        <ResourceDictionary>

        </ResourceDictionary>
    </ContentPage.Resources>
    ...
</ContentPage>
```

现在可以对资源字典添加对象和各种类型的值。 这些类型必须是可实例化。 它们不能为抽象类，例如。 这些类型还必须具有公共无参数构造函数。 每个项需要一个使用指定的字典键`x:Key`属性。 例如：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.SharedResourcesPage"
             Title="Shared Resources Page">

    <ContentPage.Resources>
        <ResourceDictionary>
            <LayoutOptions x:Key="horzOptions"
                           Alignment="Center" />

            <LayoutOptions x:Key="vertOptions"
                           Alignment="Center"
                           Expands="True" />
        </ResourceDictionary>
    </ContentPage.Resources>
    ...
</ContentPage>
```

这两项是结构类型的值`LayoutOptions`，并且每个包含唯一键，另一个或两个属性设置。 在代码和标记，它是更常见，若要使用的静态字段`LayoutOptions`，但下面是更方便地设置属性。

现在需要设置`HorizontalOptions`并`VerticalOptions`对这些资源，这些按钮的属性和为此，执行`StaticResource`XAML 标记扩展：

```xaml
<Button Text="Do this!"
        HorizontalOptions="{StaticResource horzOptions}"
        VerticalOptions="{StaticResource vertOptions}"
        BorderWidth="3"
        Rotation="-15"
        TextColor="Red"
        FontSize="24" />
```

`StaticResource`标记扩展始终用大括号分隔，并且包括字典的键。

名称`StaticResource`使它有别于`DynamicResource`，它还支持 Xamarin.Forms。 `DynamicResource` 适用于在运行时，可能会更改的值与关联的字典键时`StaticResource`只是当构造页上的元素后从字典访问元素。

有关`BorderWidth`属性，它是必需的字典中存储一个双精度值。 XAML 可以方便地定义对等的常见数据类型的标记`x:Double`和`x:Int32`:

```xaml
<ContentPage.Resources>
    <ResourceDictionary>
        <LayoutOptions x:Key="horzOptions"
                       Alignment="Center" />

        <LayoutOptions x:Key="vertOptions"
                       Alignment="Center"
                       Expands="True" />

        <x:Double x:Key="borderWidth">
            3
        </x:Double>
    </ResourceDictionary>
</ContentPage.Resources>
```

不需要将其放在三个行。 此旋转角度此字典条目仅占用一行：

```xaml
<ContentPage.Resources>
    <ResourceDictionary>
        <LayoutOptions x:Key="horzOptions"
                       Alignment="Center" />

        <LayoutOptions x:Key="vertOptions"
                       Alignment="Center"
                       Expands="True" />

         <x:Double x:Key="borderWidth">
            3
         </x:Double>

        <x:Double x:Key="rotationAngle">-15</x:Double>
    </ResourceDictionary>
</ContentPage.Resources>
```

这两个资源可以引用方式与`LayoutOptions`值：

```xaml
<Button Text="Do this!"
        HorizontalOptions="{StaticResource horzOptions}"
        VerticalOptions="{StaticResource vertOptions}"
        BorderWidth="{StaticResource borderWidth}"
        Rotation="{StaticResource rotationAngle}"
        TextColor="Red"
        FontSize="24" />
```

类型的资源`Color`，可以使用直接分配这些类型的属性时使用的相同字符串表示形式。 创建资源时，将调用类型转换器。 下面是类型的资源`Color`:

```xaml
<Color x:Key="textColor">Red</Color>
```

通常情况下，程序集`FontSize`属性绑定到的成员`NamedSize`如枚举`Large`。 `FontSizeConverter`类在后台将其转换为依赖于平台的值使用的工作原理`Device.GetNamedSized`方法。 但是，在定义的字体大小资源时，它更有意义使用数字值，显示为此处`x:Double`类型：

```xaml
<x:Double x:Key="fontSize">24</x:Double>
```

现在所有的属性除外`Text`由资源设置定义：

```xaml
<Button Text="Do this!"
        HorizontalOptions="{StaticResource horzOptions}"
        VerticalOptions="{StaticResource vertOptions}"
        BorderWidth="{StaticResource borderWidth}"
        Rotation="{StaticResource rotationAngle}"
        TextColor="{StaticResource textColor}"
        FontSize="{StaticResource fontSize}" />
```

还有可能要使用`OnPlatform`来定义适用于平台的不同值的资源字典中。 下面是如何`OnPlatform`对象可以是不同的文本颜色的资源字典的一部分：

```xaml
<OnPlatform x:Key="textColor"
            x:TypeArguments="Color">
    <On Platform="iOS" Value="Red" />
    <On Platform="Android" Value="Aqua" />
    <On Platform="UWP" Value="#80FF80" />
</OnPlatform>
```

请注意，`OnPlatform`获取同时`x:Key`属性，因为它是在字典中的对象和一个`x:TypeArguments`属性，因为它是一个泛型类。 `iOS`， `Android`，并`UWP`特性转换为`Color`值在初始化对象时。

下面是最终完成 XAML 文件具有访问共享的六个值的三个按钮：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.SharedResourcesPage"
             Title="Shared Resources Page">

    <ContentPage.Resources>
        <ResourceDictionary>
            <LayoutOptions x:Key="horzOptions"
                           Alignment="Center" />

            <LayoutOptions x:Key="vertOptions"
                           Alignment="Center"
                           Expands="True" />

            <x:Double x:Key="borderWidth">3</x:Double>

            <x:Double x:Key="rotationAngle">-15</x:Double>

            <OnPlatform x:Key="textColor"
                        x:TypeArguments="Color">
                <On Platform="iOS" Value="Red" />
                <On Platform="Android" Value="Aqua" />
                <On Platform="UWP" Value="#80FF80" />
            </OnPlatform>

            <x:Double x:Key="fontSize">24</x:Double>
        </ResourceDictionary>
    </ContentPage.Resources>

    <StackLayout>
        <Button Text="Do this!"
                HorizontalOptions="{StaticResource horzOptions}"
                VerticalOptions="{StaticResource vertOptions}"
                BorderWidth="{StaticResource borderWidth}"
                Rotation="{StaticResource rotationAngle}"
                TextColor="{StaticResource textColor}"
                FontSize="{StaticResource fontSize}" />

        <Button Text="Do that!"
                HorizontalOptions="{StaticResource horzOptions}"
                VerticalOptions="{StaticResource vertOptions}"
                BorderWidth="{StaticResource borderWidth}"
                Rotation="{StaticResource rotationAngle}"
                TextColor="{StaticResource textColor}"
                FontSize="{StaticResource fontSize}" />

        <Button Text="Do the other thing!"
                HorizontalOptions="{StaticResource horzOptions}"
                VerticalOptions="{StaticResource vertOptions}"
                BorderWidth="{StaticResource borderWidth}"
                Rotation="{StaticResource rotationAngle}"
                TextColor="{StaticResource textColor}"
                FontSize="{StaticResource fontSize}" />

    </StackLayout>
</ContentPage>
```

屏幕截图验证一致样式和依赖于平台的样式：

[![](xaml-markup-extensions-images/sharedresources.png "设置控件的样式")](xaml-markup-extensions-images/sharedresources-large.png#lightbox "风格的控件")

尽管最常见的定义`Resources`集合顶部的页上，请记住，`Resources`属性定义由`VisualElement`，并且可以具有`Resources`上页面上其他元素的集合。 例如，请添加一个到`StackLayout`在此示例中：

```xaml
<StackLayout>
    <StackLayout.Resources>
        <ResourceDictionary>
            <Color x:Key="textColor">Blue</Color>
        </ResourceDictionary>
    </StackLayout.Resources>
    ...
</StackLayout>
```

你会发现这些按钮的文本颜色现在为蓝色。 基本上，只要 XAML 分析程序遇到`StaticResource`标记扩展，它在可视树搜索，并使用第一个`ResourceDictionary`遇到包含该密钥。

最常见的资源字典中存储的对象类型之一是 Xamarin.Forms `Style`，其定义的属性设置的集合。 样式将在本文中讨论[样式](~/xamarin-forms/user-interface/styles/index.md)。

有时开发人员熟悉 XAML 想知道是否他们可如将可视元素`Label`或`Button`中`ResourceDictionary`。 虽然肯定可能，但它没有太大意义。 用途`ResourceDictionary`是共享的对象。 不能共享一个可视元素。 同一个实例不能出现两次在单个页面上。

## <a name="the-xstatic-markup-extension"></a>X:static 标记扩展

尽管其名称的相似之处`x:Static`和`StaticResource`有很大差异。 `StaticResource` 从资源字典时返回的对象`x:Static`访问以下项之一：

- 公共静态字段
- 公共静态属性
- 公共常量字段
- 枚举成员。

`StaticResource`标记扩展支持的 XAML 实现定义的资源字典，虽然`x:Static`属于内部函数 XAML，作为`x`前缀显示。

下面是几个示例，演示如何`x:Static`可以显式引用静态字段和枚举成员：

```xaml
<Label Text="Hello, XAML!"
       VerticalOptions="{x:Static LayoutOptions.Start}"
       HorizontalTextAlignment="{x:Static TextAlignment.Center}"
       TextColor="{x:Static Color.Aqua}" />
```

到目前为止，这并不令人印象非常深刻。 但`x:Static`标记扩展还可以引用静态字段或属性从你自己的代码。 例如，下面是`AppConstants`类，其中包含一些您可能想要在整个应用程序的多个页面上使用的静态字段：

```csharp
using System;
using Xamarin.Forms;

namespace XamlSamples
{
    static class AppConstants
    {
        public static readonly Thickness PagePadding;

        public static readonly Font TitleFont;

        public static readonly Color BackgroundColor = Color.Aqua;

        public static readonly Color ForegroundColor = Color.Brown;

        static AppConstants()
        {
            switch (Device.RuntimePlatform)
            {
                case Device.iOS:
                    PagePadding = new Thickness(5, 20, 5, 0);
                    TitleFont = Font.SystemFontOfSize(35, FontAttributes.Bold);
                    break;

                case Device.Android:
                    PagePadding = new Thickness(5, 0, 5, 0);
                    TitleFont = Font.SystemFontOfSize(40, FontAttributes.Bold);
                    break;

                case Device.UWP:
                    PagePadding = new Thickness(5, 0, 5, 0);
                    TitleFont = Font.SystemFontOfSize(50, FontAttributes.Bold);
                    break;
            }
        }
    }
}
```

若要引用的 XAML 文件中此类的静态字段，将需要设法指出此文件的所在位置在 XAML 文件中。 使用 XML 命名空间声明来执行此操作。

回想一下作为标准 Xamarin.Forms XAML 模板的一部分创建的 XAML 文件包含两个 XML 命名空间声明： 一个用于访问 Xamarin.Forms 类和另一个用于引用标记和属性到 XAML 内部函数：

```csharp
xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
```

你将需要额外的 XML 命名空间声明，若要访问其他类。 每个其他的 XML 命名空间声明定义一个新的前缀。 若要访问的共享的应用程序的.NET 标准库的本地类，例如`AppConstants`，XAML 编程人员通常使用该前缀`local`。 命名空间声明必须指示 CLR （公共语言运行时） 命名空间名称，也称为.NET 命名空间名称，名称将显示在C#`namespace`定义中或在`using`指令：

```csharp
xmlns:local="clr-namespace:XamlSamples"
```

此外可以在.NET Standard 库引用任何程序集中定义.NET 命名空间的 XML 命名空间的声明。 例如，下面是`sys`的标准.NET 前缀`System`命名空间，即**mscorlib**程序集，这对于"Microsoft 通用对象运行时库，"花了一次，但现在表示"多语言标准常见对象运行时库。" 由于这是另一个程序集，您还必须指定程序集名称，在这种情况下**mscorlib**:

```csharp
xmlns:sys="clr-namespace:System;assembly=mscorlib"
```

请注意，在关键字`clr-namespace`后跟一个冒号，然后.NET 命名空间名称后, 接分号，关键字`assembly`，等号和程序集名称。

是的加上冒号`clr-namespace`但等号后面`assembly`。 语法方式故意定义在此：大多数 XML 命名空间声明引用一个 URI，如开始一个 URI 方案名称`http`，这始终后跟一个冒号。 `clr-namespace`此字符串的一部分用于模拟这种约定。

中包含以下两个命名空间声明**StaticConstantsPage**示例。 请注意，`BoxView`尺寸设置为`Math.PI`和`Math.E`，但缩放到原来的 100:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:XamlSamples"
             xmlns:sys="clr-namespace:System;assembly=mscorlib"
             x:Class="XamlSamples.StaticConstantsPage"
             Title="Static Constants Page"
             Padding="{x:Static local:AppConstants.PagePadding}">

    <StackLayout>
       <Label Text="Hello, XAML!"
              TextColor="{x:Static local:AppConstants.BackgroundColor}"
              BackgroundColor="{x:Static local:AppConstants.ForegroundColor}"
              Font="{x:Static local:AppConstants.TitleFont}"
              HorizontalOptions="Center" />

      <BoxView WidthRequest="{x:Static sys:Math.PI}"
               HeightRequest="{x:Static sys:Math.E}"
               Color="{x:Static local:AppConstants.ForegroundColor}"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand"
               Scale="100" />
    </StackLayout>
</ContentPage>
```

大小所产生的`BoxView`相对于屏幕是依赖于平台的：

 [![](xaml-markup-extensions-images/staticconstants.png "使用 X:static 标记扩展的控件")](xaml-markup-extensions-images/staticconstants-large.png#lightbox "使用 X:static 标记扩展的控件")

## <a name="other-standard-markup-extensions"></a>其他标准标记扩展

多个标记扩展是固有的 XAML 和在 Xamarin.Forms XAML 文件中受支持。 其中一些不常使用，但在需要时至关重要：

-  如果属性具有非`null`值的默认值，但你想要将其设置为`null`，将其设置为`{x:Null}`标记扩展。
-  如果某个属性属于类型`Type`，你可以将其分配给`Type`对象使用标记扩展`{x:Type someClass}`。
-  可在 XAML 中使用定义数组`x:Array`标记扩展。 此标记扩展具有所需的属性名为`Type`，该值指示数组中元素的类型。
- `Binding`标记扩展中讨论了[第 4 部分。数据绑定基础知识](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)。

## <a name="the-constraintexpression-markup-extension"></a>ConstraintExpression 标记扩展

标记扩展可以有属性，但它们不将设置等的 XML 属性。 在标记扩展中，属性设置以逗号分隔，并在大括号内显示没有引号。

这可以用名为 Xamarin.Forms 标记扩展说明`ConstraintExpression`，用于与`RelativeLayout`类。 作为常量，或相对于父级或其他命名的视图，可以指定的位置或子视图的大小。 语法`ConstraintExpression`允许您设置的位置或视图使用的大小`Factor`属性的另一个视图，加上一次`Constant`。 比这更复杂的任何内容需要代码。

以下是一个示例：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.RelativeLayoutPage"
             Title="RelativeLayout Page">

    <RelativeLayout>

        <!-- Upper left -->
        <BoxView Color="Red"
                 RelativeLayout.XConstraint=
                     "{ConstraintExpression Type=Constant,
                                            Constant=0}"
                 RelativeLayout.YConstraint=
                     "{ConstraintExpression Type=Constant,
                                            Constant=0}" />
        <!-- Upper right -->
        <BoxView Color="Green"
                 RelativeLayout.XConstraint=
                     "{ConstraintExpression Type=RelativeToParent,
                                            Property=Width,
                                            Factor=1,
                                            Constant=-40}"
                 RelativeLayout.YConstraint=
                     "{ConstraintExpression Type=Constant,
                                            Constant=0}" />
        <!-- Lower left -->
        <BoxView Color="Blue"
                 RelativeLayout.XConstraint=
                     "{ConstraintExpression Type=Constant,
                                            Constant=0}"
                 RelativeLayout.YConstraint=
                     "{ConstraintExpression Type=RelativeToParent,
                                            Property=Height,
                                            Factor=1,
                                            Constant=-40}" />
        <!-- Lower right -->
        <BoxView Color="Yellow"
                 RelativeLayout.XConstraint=
                     "{ConstraintExpression Type=RelativeToParent,
                                            Property=Width,
                                            Factor=1,
                                            Constant=-40}"
                 RelativeLayout.YConstraint=
                     "{ConstraintExpression Type=RelativeToParent,
                                            Property=Height,
                                            Factor=1,
                                            Constant=-40}" />

        <!-- Centered and 1/3 width and height of parent -->
        <BoxView x:Name="oneThird"
                 Color="Red"
                 RelativeLayout.XConstraint=
                     "{ConstraintExpression Type=RelativeToParent,
                                            Property=Width,
                                            Factor=0.33}"
                 RelativeLayout.YConstraint=
                     "{ConstraintExpression Type=RelativeToParent,
                                            Property=Height,
                                            Factor=0.33}"
                 RelativeLayout.WidthConstraint=
                     "{ConstraintExpression Type=RelativeToParent,
                                            Property=Width,
                                            Factor=0.33}"
                 RelativeLayout.HeightConstraint=
                     "{ConstraintExpression Type=RelativeToParent,
                                            Property=Height,
                                            Factor=0.33}"  />

        <!-- 1/3 width and height of previous -->
        <BoxView Color="Blue"
                 RelativeLayout.XConstraint=
                     "{ConstraintExpression Type=RelativeToView,
                                            ElementName=oneThird,
                                            Property=X}"
                 RelativeLayout.YConstraint=
                     "{ConstraintExpression Type=RelativeToView,
                                            ElementName=oneThird,
                                            Property=Y}"
                 RelativeLayout.WidthConstraint=
                     "{ConstraintExpression Type=RelativeToView,
                                            ElementName=oneThird,
                                            Property=Width,
                                            Factor=0.33}"
                 RelativeLayout.HeightConstraint=
                     "{ConstraintExpression Type=RelativeToView,
                                            ElementName=oneThird,
                                            Property=Height,
                                            Factor=0.33}"  />
    </RelativeLayout>
</ContentPage>
```

可能需要与此示例的最重要经验是标记扩展的语法：没有引号必须出现在标记扩展的大括号内。 键入时标记扩展在 XAML 文件中，是自然地想要将属性的值括在引号。 忍不住 ！

下面是运行的程序：

[![](xaml-markup-extensions-images/relativelayout.png "使用约束的相对布局")](xaml-markup-extensions-images/relativelayout-large.png#lightbox "相对布局使用约束")

## <a name="summary"></a>总结

如下所示的 XAML 标记扩展提供 XAML 文件的重要支持。 但也许是最有价值的 XAML 标记扩展`Binding`，其中并未包括在本系列的下一部分[第 4 部分。数据绑定基础知识](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)。



## <a name="related-links"></a>相关链接

- [XamlSamples](https://developer.xamarin.com/samples/xamarin-forms/XamlSamples/)
- [第 1 部分：XAML 入门](~/xamarin-forms/xaml/xaml-basics/get-started-with-xaml.md)
- [第 2 部分：基本 XAML 语法](~/xamarin-forms/xaml/xaml-basics/essential-xaml-syntax.md)
- [第 4 部分：数据绑定基础知识](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)
- [第 5 部分：从数据绑定到 MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)
