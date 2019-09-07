---
title: 第 2 部分。 基本 XAML 语法
description: 本文介绍的属性元素的基本 XAML 语法功能和附加属性。
ms.prod: xamarin
ms.assetid: 4022F1DC-3802-4635-A553-688ABD3F0D5A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/25/2017
ms.openlocfilehash: f79a07a04eddeea1441f7938fdef210a37fb920a
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70772887"
---
# <a name="part-2-essential-xaml-syntax"></a>第 2 部分。 基本 XAML 语法

[![下载示例](~/media/shared/download.png)下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xamlsamples)

_XAML 主要用于实例化和初始化对象。但通常情况下，属性必须设置为复杂对象不能轻松地表示为 XML 字符串，并有时一个类定义的属性必须设置上一个子类。这些两个需要属性元素和附加的属性的基本 XAML 语法的功能。_

## <a name="property-elements"></a>属性元素

在 XAML 中，类的属性通常设置为 XML 属性：

```xaml
<Label Text="Hello, XAML!"
       VerticalOptions="Center"
       FontAttributes="Bold"
       FontSize="Large"
       TextColor="Aqua" />
```

但是，没有在 XAML 中设置属性的替代方法。 若要尝试使用此替代方法`TextColor`，首先删除现有`TextColor`设置：

```xaml
<Label Text="Hello, XAML!"
       VerticalOptions="Center"
       FontAttributes="Bold"
       FontSize="Large" />
```

打开空元素`Label`分隔到开始和结束标记来标记：

```xaml
<Label Text="Hello, XAML!"
       VerticalOptions="Center"
       FontAttributes="Bold"
       FontSize="Large">

</Label>
```

这些标记内添加的类名和句点分隔的属性名称组成的开始和结束标记：

```xaml
<Label Text="Hello, XAML!"
       VerticalOptions="Center"
       FontAttributes="Bold"
       FontSize="Large">
    <Label.TextColor>

    </Label.TextColor>
</Label>
```

将属性值设置为这些新的标记，像这样的内容：

```xaml
<Label Text="Hello, XAML!"
       VerticalOptions="Center"
       FontAttributes="Bold"
       FontSize="Large">
    <Label.TextColor>
        Aqua
    </Label.TextColor>
</Label>
```

若要指定这两种方式`TextColor`属性在功能上等效，但不要因为这会有效地进行设置的属性两次，并可能会不明确的同一属性使用的两种方法。

使用此新语法，可以引入了一些便利的术语：

- `Label` 是*对象元素*。 它是 Xamarin.Forms 对象表示为一个 XML 元素。
- `Text``VerticalOptions`，`FontAttributes`并`FontSize`是*属性的特性*。 它们是 Xamarin.Forms 属性表示为 XML 属性。
- 在此最后一段中，`TextColor`变得*property 元素*。 它是 Xamarin.Forms 属性，但它现在是一个 XML 元素。

定义的属性元素可能在第一次似乎是违反了 XML 语法，但它不是。 期间必须在 XML 中没有特殊含义。 对 XML 解码器，`Label.TextColor`是只是正常的子元素。

但是，在 XAML 中，此语法是非常特殊的。 属性元素的规则之一是，没有其他可以出现在`Label.TextColor`标记。 属性的值始终定义为属性元素的开始和结束标记之间的内容。

在多个属性，可以使用属性元素语法：

```xaml
<Label Text="Hello, XAML!"
       VerticalOptions="Center">
    <Label.FontAttributes>
        Bold
    </Label.FontAttributes>
    <Label.FontSize>
        Large
    </Label.FontSize>
    <Label.TextColor>
        Aqua
    </Label.TextColor>
</Label>
```

也可以使用属性元素语法的所有属性：

```xaml
<Label>
    <Label.Text>
        Hello, XAML!
    </Label.Text>
    <Label.FontAttributes>
        Bold
    </Label.FontAttributes>
    <Label.FontSize>
        Large
    </Label.FontSize>
    <Label.TextColor>
        Aqua
    </Label.TextColor>
    <Label.VerticalOptions>
        Center
    </Label.VerticalOptions>
</Label>
```

首先，属性元素语法可能看起来是不必要的过于繁琐替换好像某些内容相对来说非常简单，并在这些示例中这的确是这种情况。

但是，属性元素语法将变得重要太复杂，无法表示为一个简单的字符串属性的值时。 属性元素标记内可以实例化另一个对象，并设置其属性。 例如，你可以显式设置属性如`VerticalOptions`到`LayoutOptions`属性设置的值：

```xaml
<Label>
    ...
    <Label.VerticalOptions>
        <LayoutOptions Alignment="Center" />
    </Label.VerticalOptions>
</Label>
```

再例如：具有两个名为`RowDefinitions`和`ColumnDefinitions`的属性。 `Grid` 这两个属性属于类型`RowDefinitionCollection`并`ColumnDefinitionCollection`，这是集合的`RowDefinition`和`ColumnDefinition`对象。 需要使用属性元素语法来设置这些集合。

下面是为 XAML 文件的开头`GridDemoPage`类，其中显示的属性元素标记`RowDefinitions`和`ColumnDefinitions`集合：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.GridDemoPage"
             Title="Grid Demo Page">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="*" />
            <RowDefinition Height="100" />
        </Grid.RowDefinitions>
        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="Auto" />
            <ColumnDefinition Width="*" />
            <ColumnDefinition Width="100" />
        </Grid.ColumnDefinitions>
        ...
    </Grid>
</ContentPage>
```

请注意，用于定义自动调整大小的单元格的像素宽度和高度，以及星型设置单元格的缩写的语法。

## <a name="attached-properties"></a>附加属性

您刚才已了解`Grid`要求的属性元素`RowDefinitions`和`ColumnDefinitions`集合来定义的行和列。 但是，还必须对程序员来说，以指示行和列的某种方式其中的每个子`Grid`驻留。

中的每个子级的标记`Grid`指定行和列的该子项，使用以下属性：

- `Grid.Row`
- `Grid.Column`

这些属性的默认值为 0。 您还可以指示是否子跨越多个行或列具有这些属性：

- `Grid.RowSpan`
- `Grid.ColumnSpan`

这两个属性具有默认值为 1。

下面是完整的 GridDemoPage.xaml 文件：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.GridDemoPage"
             Title="Grid Demo Page">

    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="*" />
            <RowDefinition Height="100" />
        </Grid.RowDefinitions>

        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="Auto" />
            <ColumnDefinition Width="*" />
            <ColumnDefinition Width="100" />
        </Grid.ColumnDefinitions>

        <Label Text="Autosized cell"
               Grid.Row="0" Grid.Column="0"
               TextColor="White"
               BackgroundColor="Blue" />

        <BoxView Color="Silver"
                 HeightRequest="0"
                 Grid.Row="0" Grid.Column="1" />

        <BoxView Color="Teal"
                 Grid.Row="1" Grid.Column="0" />

        <Label Text="Leftover space"
               Grid.Row="1" Grid.Column="1"
               TextColor="Purple"
               BackgroundColor="Aqua"
               HorizontalTextAlignment="Center"
               VerticalTextAlignment="Center" />

        <Label Text="Span two rows (or more if you want)"
               Grid.Row="0" Grid.Column="2" Grid.RowSpan="2"
               TextColor="Yellow"
               BackgroundColor="Blue"
               HorizontalTextAlignment="Center"
               VerticalTextAlignment="Center" />

        <Label Text="Span two columns"
               Grid.Row="2" Grid.Column="0" Grid.ColumnSpan="2"
               TextColor="Blue"
               BackgroundColor="Yellow"
               HorizontalTextAlignment="Center"
               VerticalTextAlignment="Center" />

        <Label Text="Fixed 100x100"
               Grid.Row="2" Grid.Column="2"
               TextColor="Aqua"
               BackgroundColor="Red"
               HorizontalTextAlignment="Center"
               VerticalTextAlignment="Center" />

    </Grid>
</ContentPage>
```

`Grid.Row`和`Grid.Column`设置为 0 不是必需的但通常包括为清晰起见。

下面是如下所示：

[![网格布局](essential-xaml-syntax-images/griddemo.png)](essential-xaml-syntax-images/griddemo-large.png#lightbox)

仅从语法，判断这些`Grid.Row`， `Grid.Column`， `Grid.RowSpan`，和`Grid.ColumnSpan`似乎是静态字段或属性的属性`Grid`，但有趣的是，`Grid`不定义任何名为`Row`， `Column`， `RowSpan`，或`ColumnSpan`。

相反，`Grid`定义名为四个可绑定属性`RowProperty`， `ColumnProperty`， `RowSpanProperty`，和`ColumnSpanProperty`。 这些是特殊类型的可绑定属性称为*附加属性*。 由定义`Grid`上的子级但设置类`Grid`。

当你希望使用这些附加属性在代码中，`Grid`类提供了名为的静态方法`SetRow`， `GetColumn`，依次类推。 但在 XAML 中，这些附加的属性设置为中的子级的属性`Grid`使用简单的属性名称。

附加的属性的形式始终可识别在 XAML 文件中包含的类和一个句点分隔的属性名称的属性。 它们被称为*附加属性*因为它们由一个类定义 (在这种情况下， `Grid`)，但附加到其他对象 (在本例中为子级的`Grid`)。 在布局期间`Grid`可以询问以了解在哪里放置每个子这些附加属性的值。

`AbsoluteLayout`类定义了名为两个附加的属性`LayoutBounds`和`LayoutFlags`。 下面是棋盘图案意识到使用按比例定位和调整大小功能`AbsoluteLayout`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.AbsoluteDemoPage"
             Title="Absolute Demo Page">

    <AbsoluteLayout BackgroundColor="#FF8080">
        <BoxView Color="#8080FF"
                 AbsoluteLayout.LayoutBounds="0.33, 0, 0.25, 0.25"
                 AbsoluteLayout.LayoutFlags="All" />

        <BoxView Color="#8080FF"
                 AbsoluteLayout.LayoutBounds="1, 0, 0.25, 0.25"
                 AbsoluteLayout.LayoutFlags="All" />

        <BoxView Color="#8080FF"
                 AbsoluteLayout.LayoutBounds="0, 0.33, 0.25, 0.25"
                 AbsoluteLayout.LayoutFlags="All" />

        <BoxView Color="#8080FF"
                 AbsoluteLayout.LayoutBounds="0.67, 0.33, 0.25, 0.25"
                 AbsoluteLayout.LayoutFlags="All" />

        <BoxView Color="#8080FF"
                 AbsoluteLayout.LayoutBounds="0.33, 0.67, 0.25, 0.25"
                 AbsoluteLayout.LayoutFlags="All" />

        <BoxView Color="#8080FF"
                 AbsoluteLayout.LayoutBounds="1, 0.67, 0.25, 0.25"
                 AbsoluteLayout.LayoutFlags="All" />

        <BoxView Color="#8080FF"
                 AbsoluteLayout.LayoutBounds="0, 1, 0.25, 0.25"
                 AbsoluteLayout.LayoutFlags="All" />

        <BoxView Color="#8080FF"
                 AbsoluteLayout.LayoutBounds="0.67, 1, 0.25, 0.25"
                 AbsoluteLayout.LayoutFlags="All" />

  </AbsoluteLayout>
</ContentPage>
```

和其内容如下：

[![绝对布局](essential-xaml-syntax-images/absolutedemo-large.png)](essential-xaml-syntax-images/absolutedemo-large.png#lightbox)

此类的某些内容，可能会质疑使用 XAML 的智慧。 当然，重复和的规律性`LayoutBounds`矩形所示，它可能会更好地实现在代码中。

这的确是合法的关注点，并且没有与平衡使用代码和标记定义用户界面时没问题。 很容易地在 XAML 中定义一些视觉对象，然后使用代码隐藏文件的构造函数添加一些可能会更好地生成在循环中的多个视觉对象。

## <a name="content-properties"></a>内容属性

在上一示例中， `StackLayout`， `Grid`，并`AbsoluteLayout`对象设置为`Content`属性`ContentPage`，这些布局的子级是实际中的项和`Children`集合。 但这些`Content`和`Children`属性却无处 XAML 文件中。

肯定可以包括`Content`并`Children`属性为属性的元素，如在**XamlPlusCode**示例：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.XamlPlusCodePage"
             Title="XAML + Code Page">
    <ContentPage.Content>
        <StackLayout>
            <StackLayout.Children>
                <Slider VerticalOptions="CenterAndExpand"
                        ValueChanged="OnSliderValueChanged" />

                <Label x:Name="valueLabel"
                       Text="A simple Label"
                       FontSize="Large"
                       HorizontalOptions="Center"
                       VerticalOptions="CenterAndExpand" />

                <Button Text="Click Me!"
                      HorizontalOptions="Center"
                      VerticalOptions="CenterAndExpand"
                      Clicked="OnButtonClicked" />
            </StackLayout.Children>
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

真正的问题是：为什么 XAML 文件中*不*需要这些属性元素？

允许在 XAML 中使用 Xamarin.Forms 中定义的元素中被标记了一个属性`ContentProperty`类中的属性。 如果您查找`ContentPage`类在联机的 Xamarin.Forms 文档，您将看到此属性：

```csharp
[Xamarin.Forms.ContentProperty("Content")]
public class ContentPage : TemplatedPage
```

这意味着，`Content`属性元素标记不是必需的。 出现的开始和结束之间的任何 XML 内容`ContentPage`标记假定要分配给`Content`属性。

 `StackLayout``Grid`， `AbsoluteLayout`，并`RelativeLayout`都派生`Layout<View>`，并且如果您查找`Layout<T>`Xamarin.Forms 文档中，在您将看到另一个`ContentProperty`属性：

```csharp
[Xamarin.Forms.ContentProperty("Children")]
public abstract class Layout<T> : Layout ...
```

允许内容的布局以会自动添加到`Children`集合，而无需显式`Children`属性元素标记。

其他类也有`ContentProperty`属性定义。 例如，内容的属性`Label`是`Text`。 检查其他人的 API 文档。

## <a name="platform-differences-with-onplatform"></a>使用 OnPlatform 平台差异

在单页面应用程序中很常见设置`Padding`页后，可以避免覆盖 iOS 状态栏上的属性。 在代码中，可以使用`Device.RuntimePlatform`属性实现此目的：

```csharp
if (Device.RuntimePlatform == Device.iOS)
{
    Padding = new Thickness(0, 20, 0, 0);
}
```

你还可以使用[`OnPlatform`](xref:Xamarin.Forms.OnPlatform`1)和[`On`](xref:Xamarin.Forms.On)类在 XAML 中执行类似操作。 首先添加为属性元素`Padding`属性页的顶部附近：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="...">

    <ContentPage.Padding>

    </ContentPage.Padding>
    ...
</ContentPage>
```

在这些标记包括`OnPlatform`标记。 `OnPlatform` 是一个泛型类。 您需要指定泛型类型参数，在这种情况下， `Thickness`，这是一种`Padding`属性。 幸运的是，没有专门用于定义名为的泛型参数的 XAML 属性`x:TypeArguments`。 这应与要设置的属性的类型匹配：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="...">

    <ContentPage.Padding>
        <OnPlatform x:TypeArguments="Thickness">

        </OnPlatform>
    </ContentPage.Padding>
  ...
</ContentPage>
```

`OnPlatform` 有一个名为`Platforms`也就是说`IList`的`On`对象。 该属性使用属性元素标记：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="...">

    <ContentPage.Padding>
        <OnPlatform x:TypeArguments="Thickness">
            <OnPlatform.Platforms>

            </OnPlatform.Platforms>
        </OnPlatform>
    </ContentPage.Padding>
  ...
</ContentPage>
```

现在，添加`On`元素。 为每个设置`Platform`属性和`Value`属性设置为标记`Thickness`属性：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="...">

    <ContentPage.Padding>
        <OnPlatform x:TypeArguments="Thickness">
            <OnPlatform.Platforms>
                <On Platform="iOS" Value="0, 20, 0, 0" />
                <On Platform="Android" Value="0, 0, 0, 0" />
                <On Platform="UWP" Value="0, 0, 0, 0" />
            </OnPlatform.Platforms>
        </OnPlatform>
    </ContentPage.Padding>
  ...
</ContentPage>
```

可以简化此标记。 内容属性的`OnPlatform`是`Platforms`，因此可以删除这些属性元素标记：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="...">

    <ContentPage.Padding>
        <OnPlatform x:TypeArguments="Thickness">
            <On Platform="iOS" Value="0, 20, 0, 0" />
            <On Platform="Android" Value="0, 0, 0, 0" />
            <On Platform="UWP" Value="0, 0, 0, 0" />
        </OnPlatform>
    </ContentPage.Padding>
  ...
</ContentPage>
```

`Platform`的属性`On`属于类型`IList<string>`，因此，如果值是相同的则可以包含多个平台：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="...">

    <ContentPage.Padding>
        <OnPlatform x:TypeArguments="Thickness">
            <On Platform="iOS" Value="0, 20, 0, 0" />
            <On Platform="Android, UWP" Value="0, 0, 0, 0" />
        </OnPlatform>
    </ContentPage.Padding>
  ...
</ContentPage>
```

由于 Android 和 UWP 设置的默认值为`Padding`，可以删除标记：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="...">

    <ContentPage.Padding>
        <OnPlatform x:TypeArguments="Thickness">
            <On Platform="iOS" Value="0, 20, 0, 0" />
        </OnPlatform>
    </ContentPage.Padding>
  ...
</ContentPage>
```

这是设置一个与平台相关的标准方法`Padding`在 XAML 中的属性。 如果`Value`设置不能由单个字符串，您可以为其定义属性元素：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="...">

    <ContentPage.Padding>
        <OnPlatform x:TypeArguments="Thickness">
            <On Platform="iOS">
                <On.Value>
                    0, 20, 0, 0
                </On.Value>
            </On>
        </OnPlatform>
    </ContentPage.Padding>
  ...
</ContentPage>
```

> [!NOTE]
> `OnPlatform`标记扩展还可以在 XAML 中用于根据每个平台自定义 UI 外观。 它提供与`OnPlatform`和`On`类相同的功能，但具有更简洁的表示形式。 有关详细信息，请参阅[OnPlatform 标记扩展](~/xamarin-forms/xaml/markup-extensions/consuming.md#onplatform)。

## <a name="summary"></a>总结

使用属性元素和附加的属性，很多基本 XAML 语法已建立。 但是，有时您需要将属性设置为对象以间接方式，例如，从资源字典。 在下一部分中，一部分介绍了此方法[3。XAML 标记扩展](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)。

## <a name="related-links"></a>相关链接

- [XamlSamples](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xamlsamples)
- [第 1 部分：XAML 入门](~/xamarin-forms/xaml/xaml-basics/get-started-with-xaml.md)
- [第 3 部分：XAML 标记扩展](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [第 4 部分：数据绑定基础知识](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)
- [第 5 部分：从数据绑定到 MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)
