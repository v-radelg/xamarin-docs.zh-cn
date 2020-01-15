---
title: 使用 XAML 标记扩展
description: 此文章介绍了如何使用 Xamarin.Forms XAML 标记扩展元素特性，若要设置从各种源，从而增强的功能和灵活性的 XAML。
ms.prod: xamarin
ms.assetid: CE686893-609C-4EC3-9225-6C68D2A9F79C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/04/2019
ms.openlocfilehash: 47fd6d34a1ef286fa8e1a307ea6033c9c6a9feea
ms.sourcegitcommit: 211fed94fb96127a3e158ae1ff5d7eb831a203d8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/15/2020
ms.locfileid: "75955758"
---
# <a name="consuming-xaml-markup-extensions"></a>使用 XAML 标记扩展

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-markupextensions)

XAML 标记扩展帮助元素特性，若要设置从各种源，从而增强的功能和灵活性的 XAML。 多个 XAML 标记扩展是 XAML 2009 规范的一部分。 这些通常在与 XAML 文件中出现`x`命名空间前缀，且通常被引用到具有此前缀。 本文讨论了以下标记扩展：

- [`x:Static`](#static) – 引用静态属性、 字段或枚举成员。
- [`x:Reference`](#reference) – 名为页面上的元素的引用。
- [`x:Type`](#type) – 将属性设置为`System.Type`对象。
- [`x:Array`](#array) – 构造特定类型的对象的数组。
- [`x:Null`](#null) – 将属性设置为`null`值。
- [`OnPlatform`](#onplatform) -自定义根据每个平台的 UI 外观。
- [`OnIdiom`](#onidiom) -自定义 UI 外观基于的设备运行应用程序的惯用语法。
- [`DataTemplate`](#datatemplate-markup-extension) -将类型转换为[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)。
- [`FontImage`](#fontimage-markup-extension) -在可显示 `ImageSource`的任何视图中显示字体图标。

其他 XAML 标记扩展从历史上看其他 XAML 实现中，通过受支持和 Xamarin.Forms 还支持。 这些更全面介绍了其他文章：

- `StaticResource` 引用资源字典中的对象，如[**资源**](~/xamarin-forms/xaml/resource-dictionaries.md)字典一文中所述。
- `DynamicResource`-响应资源字典中对象的更改，如[**动态样式**](~/xamarin-forms/user-interface/styles/dynamic.md)一文中所述。
- `Binding`-在两个对象的属性之间建立链接，如[**数据绑定**](~/xamarin-forms/app-fundamentals/data-binding/index.md)一文中所述。
- `TemplateBinding`-从控件模板执行数据绑定，如[**Xamarin 窗体控件模板**](~/xamarin-forms/app-fundamentals/templates/control-template.md)一文中所述。
- `RelativeSource`-设置相对于绑定目标位置的绑定源，如[相关绑定](~/xamarin-forms/app-fundamentals/data-binding/relative-bindings.md)一文中所述。

[ `RelativeLayout` ](xref:Xamarin.Forms.RelativeLayout)布局使用自定义标记扩展[ `ConstraintExpression` ](xref:Xamarin.Forms.ConstraintExpression)。 文章中介绍了此标记扩展[ **RelativeLayout**](~/xamarin-forms/user-interface/layouts/relative-layout.md)。

<a name="static" />

## <a name="xstatic-markup-extension"></a>x:Static 标记扩展

`x:Static`标记扩展受[ `StaticExtension` ](xref:Xamarin.Forms.Xaml.StaticExtension)类。 类具有名为的单个属性[ `Member` ](xref:Xamarin.Forms.Xaml.StaticExtension.Member)类型的`string`设置为公共常量、 静态属性、 静态字段或枚举成员的名称。

一种常用的方式来使用`x:Static`是第一次定义类与某些常量或静态变量，如这小`AppConstants`类[ **MarkupExtensions** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-markupextensions)程序：

```csharp
static class AppConstants
{
    public static double NormalFontSize = 18;
}
```

**X： 静态演示**页上演示了多种方式来使用`x:Static`标记扩展。 最繁琐的方法实例化`StaticExtension`类之间`Label.FontSize`属性元素标记：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:sys="clr-namespace:System;assembly=mscorlib"
             xmlns:local="clr-namespace:MarkupExtensions"
             x:Class="MarkupExtensions.StaticDemoPage"
             Title="x:Static Demo">
    <StackLayout Margin="10, 0">
        <Label Text="Label No. 1">
            <Label.FontSize>
                <x:StaticExtension Member="local:AppConstants.NormalFontSize" />
            </Label.FontSize>
        </Label>

        ···

    </StackLayout>
</ContentPage>
```

此 XAML 分析器还允许`StaticExtension`类，以缩写为`x:Static`:

```xaml
<Label Text="Label No. 2">
    <Label.FontSize>
        <x:Static Member="local:AppConstants.NormalFontSize" />
    </Label.FontSize>
</Label>
```

这可以更进一步简化，但更改，将引入某些新的语法： 它包含将置于`StaticExtension`类和设置在大括号中的成员。 所生成的表达式将直接为`FontSize`属性：

```xaml
<Label Text="Label No. 3"
       FontSize="{x:StaticExtension Member=local:AppConstants.NormalFontSize}" />
```

请注意，存在*没有*引号引起来的大括号内。 `Member`属性的`StaticExtension`不再是一个 XML 属性。 而是表达式的，它是表达式的标记扩展的一部分。

正如你可以将缩写`x:StaticExtension`到`x:Static`时您将其用作对象元素，还可以简化它的大括号内表达式中：

```xaml
<Label Text="Label No. 4"
       FontSize="{x:Static Member=local:AppConstants.NormalFontSize}" />
```

`StaticExtension`类具有`ContentProperty`属性引用的属性`Member`，这会将此属性作为类的默认内容属性。 对于使用大括号表示 XAML 标记扩展，则可以消除`Member=`表达式的一部分：

```xaml
<Label Text="Label No. 5"
       FontSize="{x:Static local:AppConstants.NormalFontSize}" />
```

这是最常见的形式`x:Static`标记扩展。

**静态演示**页包含两个其他示例。 XAML 文件的根标记的.net 中包含的 XML 命名空间声明`System`命名空间：

```xaml
xmlns:sys="clr-namespace:System;assembly=mscorlib"
```

这允许`Label`字体大小设置为静态字段`Math.PI`。 该操作会相当小文本，因此`Scale`属性设置为`Math.E`:

```xaml
<Label Text="&#x03C0; &#x00D7; E sized text"
       FontSize="{x:Static sys:Math.PI}"
       Scale="{x:Static sys:Math.E}"
       HorizontalOptions="Center" />
```

最后一个示例显示`Device.RuntimePlatform`值。 `Environment.NewLine`静态属性用来插入新行字符两者之间`Span`对象：

```xaml
<Label HorizontalTextAlignment="Center"
       FontSize="{x:Static local:AppConstants.NormalFontSize}">
    <Label.FormattedText>
        <FormattedString>
            <Span Text="Runtime Platform: " />
            <Span Text="{x:Static sys:Environment.NewLine}" />
            <Span Text="{x:Static Device.RuntimePlatform}" />
        </FormattedString>
    </Label.FormattedText>
</Label>
```

下面是示例运行：

[![x:Static 演示](consuming-images/staticdemo-small.png "x:Static 演示")](consuming-images/staticdemo-large.png#lightbox "x:Static 演示")

<a name="reference" />

## <a name="xreference-markup-extension"></a>x:Reference 标记扩展

`x:Reference`标记扩展受[ `ReferenceExtension` ](xref:Xamarin.Forms.Xaml.ReferenceExtension)类。 类具有名为的单个属性[ `Name` ](xref:Xamarin.Forms.Xaml.ReferenceExtension.Name)类型的`string`设置为它已被授予与名称页上的元素名称`x:Name`。 这`Name`属性是内容的属性`ReferenceExtension`，因此`Name=`时不需要`x:Reference`在大括号中显示。

`x:Reference`标记扩展以独占方式用于数据绑定，一文中的更详细地介绍[**数据绑定**](~/xamarin-forms/app-fundamentals/data-binding/index.md)。

**X:reference 演示**页显示的两种用法`x:Reference`使用数据绑定，它用于设置的第一个`Source`属性`Binding`对象，并且它用于设置第二个`BindingContext`对于两个数据绑定的属性：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="MarkupExtensions.ReferenceDemoPage"
             x:Name="page"
             Title="x:Reference Demo">

    <StackLayout Margin="10, 0">

        <Label Text="{Binding Source={x:Reference page},
                              StringFormat='The type of this page is {0}'}"
               FontSize="18"
               VerticalOptions="CenterAndExpand"
               HorizontalTextAlignment="Center" />

        <Slider x:Name="slider"
                Maximum="360"
                VerticalOptions="Center" />

        <Label BindingContext="{x:Reference slider}"
               Text="{Binding Value, StringFormat='{0:F0}&#x00B0; rotation'}"
               Rotation="{Binding Value}"
               FontSize="24"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />

    </StackLayout>
</ContentPage>
```

这两`x:Reference`表达式使用的缩写的形式`ReferenceExtension`类名称，并消除`Name=`表达式的一部分。 在第一个示例中，`x:Reference`标记扩展嵌入在`Binding`标记扩展。 请注意，`Source`和`StringFormat`设置用逗号分隔。 下面是正在运行的程序：

[![x:Reference 演示](consuming-images/referencedemo-small.png "x:Reference 演示")](consuming-images/referencedemo-large.png#lightbox "x:Reference 演示")

<a name="type" />

## <a name="xtype-markup-extension"></a>x:Type 标记扩展

`x:Type`标记扩展是 C# 的 XAML 等效项[ `typeof` ](/dotnet/csharp/language-reference/keywords/typeof/)关键字。 它受[ `TypeExtension` ](xref:Xamarin.Forms.Xaml.TypeExtension)类，该类定义一个名为属性[ `TypeName` ](xref:Xamarin.Forms.Xaml.TypeExtension.TypeName)类型的`string`设置为类或结构的名称。 `x:Type`标记扩展返回[ `System.Type` ](xref:System.Type)类或结构的对象。 `TypeName` 是的 content 属性`TypeExtension`，因此`TypeName=`时不需要`x:Type`大括号，会显示。

在 Xamarin.Forms 中，有多个属性具有类型的自变量的`Type`。 示例包括[ `TargetType` ](xref:Xamarin.Forms.Style.TargetType)的属性`Style`，并[X:typearguments](~/xamarin-forms/xaml/passing-arguments.md#generic_type_arguments)用来在泛型类中指定参数属性。 但是，XAML 分析器不会执行`typeof`操作，自动和`x:Type`在这些情况下不使用标记扩展。

一个位置其中`x:Type`*是*要求是使用`x:Array`中所述的标记扩展[下一节](#array)。

`x:Type`构造一个菜单，其中每个菜单项对应于特定类型的对象时，标记扩展也是很有用。 你可以将关联`Type`对象与每个菜单项，，然后选择菜单项时实例化对象。

这是如何在导航菜单`MainPage`中**标记扩展**编程的工作原理。 **MainPage.xaml**文件包含`TableView`与每个`TextCell`对应于程序中的特定页：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:MarkupExtensions"
             x:Class="MarkupExtensions.MainPage"
             Title="Markup Extensions"
             Padding="10">
    <TableView Intent="Menu">
        <TableRoot>
            <TableSection>
                <TextCell Text="x:Static Demo"
                          Detail="Access constants or statics"
                          Command="{Binding NavigateCommand}"
                          CommandParameter="{x:Type local:StaticDemoPage}" />

                <TextCell Text="x:Reference Demo"
                          Detail="Reference named elements on the page"
                          Command="{Binding NavigateCommand}"
                          CommandParameter="{x:Type local:ReferenceDemoPage}" />

                <TextCell Text="x:Type Demo"
                          Detail="Associate a Button with a Type"
                          Command="{Binding NavigateCommand}"
                          CommandParameter="{x:Type local:TypeDemoPage}" />

                <TextCell Text="x:Array Demo"
                          Detail="Use an array to fill a ListView"
                          Command="{Binding NavigateCommand}"
                          CommandParameter="{x:Type local:ArrayDemoPage}" />

                ···                          

        </TableRoot>
    </TableView>
</ContentPage>
```

下面是在中打开主页**标记扩展**:

[![主页](consuming-images/mainpage-small.png "主页")](consuming-images/mainpage-large.png#lightbox "主页")

每个`CommandParameter`属性设置为`x:Type`引用了一个其他页面的标记扩展。 `Command`属性绑定到一个名为属性`NavigateCommand`。 此属性定义在`MainPage`代码隐藏文件：

```csharp
public partial class MainPage : ContentPage
{
    public MainPage()
    {
        InitializeComponent();

        NavigateCommand = new Command<Type>(async (Type pageType) =>
        {
            Page page = (Page)Activator.CreateInstance(pageType);
            await Navigation.PushAsync(page);
        });

        BindingContext = this;
    }

    public ICommand NavigateCommand { private set; get; }
}
```

`NavigateCommand`属性是`Command`对象，它实现包含类型的自变量的 execute 命令`Type`&mdash;的值`CommandParameter`。 该方法使用`Activator.CreateInstance`来实例化页，然后转到它。 最后，通过设置构造函数`BindingContext`到其自身页，这使得`Binding`上`Command`工作。 请参阅[**数据绑定**](~/xamarin-forms/app-fundamentals/data-binding/index.md)一文，特别是[ **Commanding** ](~/xamarin-forms/app-fundamentals/data-binding/commanding.md)一文，了解有关此类型的代码的更多详细信息。

**X： 类型演示**页使用类似的技术来实例化 Xamarin.Forms 元素，并将其添加到`StackLayout`。 该 XAML 文件最初由三个`Button`元素使用其`Command`属性设置为`Binding`和`CommandParameter`属性设置为三个 Xamarin.Forms 视图的类型：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="MarkupExtensions.TypeDemoPage"
             Title="x:Type Demo">

    <StackLayout x:Name="stackLayout"
                 Padding="10, 0">

        <Button Text="Create a Slider"
                HorizontalOptions="Center"
                VerticalOptions="CenterAndExpand"
                Command="{Binding CreateCommand}"
                CommandParameter="{x:Type Slider}" />

        <Button Text="Create a Stepper"
                HorizontalOptions="Center"
                VerticalOptions="CenterAndExpand"
                Command="{Binding CreateCommand}"
                CommandParameter="{x:Type Stepper}" />

        <Button Text="Create a Switch"
                HorizontalOptions="Center"
                VerticalOptions="CenterAndExpand"
                Command="{Binding CreateCommand}"
                CommandParameter="{x:Type Switch}" />
    </StackLayout>
</ContentPage>
```

代码隐藏文件定义和初始化`CreateCommand`属性：

```csharp
public partial class TypeDemoPage : ContentPage
{
    public TypeDemoPage()
    {
        InitializeComponent();

        CreateCommand = new Command<Type>((Type viewType) =>
        {
            View view = (View)Activator.CreateInstance(viewType);
            view.VerticalOptions = LayoutOptions.CenterAndExpand;
            stackLayout.Children.Add(view);
        });

        BindingContext = this;
    }

    public ICommand CreateCommand { private set; get; }
}
```

是该方法时执行`Button`被按下创建的自变量的新实例，设置其`VerticalOptions`属性，并将其添加到`StackLayout`。 这三个`Button`元素然后动态创建的视图与共享页：

[![x:Type 演示](consuming-images/typedemo-small.png "x:Type 演示")](consuming-images/typedemo-large.png#lightbox "x:Type 演示")

<a name="array" />

## <a name="xarray-markup-extension"></a>x:Array 标记扩展

`x:Array`标记扩展，可在标记中定义一个数组。 它受[ `ArrayExtension` ](xref:Xamarin.Forms.Xaml.ArrayExtension)类，该类定义两个属性：

- `Type` 类型的`Type`，指示数组中元素的类型。
- `Items` 类型的`IList`，它是项目本身的集合。 这是内容属性的`ArrayExtension`。

`x:Array`标记扩展本身永远不会显示在大括号中。 相反，`x:Array`开始和结束标记分隔的项的列表。 设置`Type`属性设置为`x:Type`标记扩展。

**X:array 演示**页显示了如何使用`x:Array`若要将项添加到`ListView`通过设置`ItemsSource`到一个数组的属性：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="MarkupExtensions.ArrayDemoPage"
             Title="x:Array Demo Page">
    <ListView Margin="10">
        <ListView.ItemsSource>
            <x:Array Type="{x:Type Color}">
                <Color>Aqua</Color>
                <Color>Black</Color>
                <Color>Blue</Color>
                <Color>Fuchsia</Color>
                <Color>Gray</Color>
                <Color>Green</Color>
                <Color>Lime</Color>
                <Color>Maroon</Color>
                <Color>Navy</Color>
                <Color>Olive</Color>
                <Color>Pink</Color>
                <Color>Purple</Color>
                <Color>Red</Color>
                <Color>Silver</Color>
                <Color>Teal</Color>
                <Color>White</Color>
                <Color>Yellow</Color>
            </x:Array>
        </ListView.ItemsSource>

        <ListView.ItemTemplate>
            <DataTemplate>
                <ViewCell>
                    <BoxView Color="{Binding}"
                             Margin="3" />    
                </ViewCell>
            </DataTemplate>
        </ListView.ItemTemplate>
    </ListView>
</ContentPage>        
```

`ViewCell`创建一个简单`BoxView`为每个颜色条目：

[![x:Array 演示](consuming-images/arraydemo-small.png "x:Array 演示")](consuming-images/arraydemo-large.png#lightbox "x:Array 演示")

有几种方法来指定对单个`Color`此数组中的项。 可以使用`x:Static`标记扩展：

```xaml
<x:Static Member="Color.Blue" />
```

或者，可以使用`StaticResource`从资源字典中检索一种颜色：

```xaml
<StaticResource Key="myColor" />
```

这篇文章后，你将看到还会创建一个新的颜色值的自定义 XAML 标记扩展：

```xaml
<local:HslColor H="0.5" S="1.0" L="0.5" />
```

在定义字符串或数字等常见类型的数组时，使用标记中列出[**传递构造函数自变量**](~/xamarin-forms/xaml/passing-arguments.md#constructor_arguments)文章来分隔的值。

<a name="null" />

## <a name="xnull-markup-extension"></a>x:Null 标记扩展

`x:Null`标记扩展受[ `NullExtension` ](xref:Xamarin.Forms.Xaml.NullExtension)类。 它没有属性，并且只需 XAML 等效于 C# [ `null` ](/dotnet/csharp/language-reference/keywords/null/)关键字。

`x:Null`标记扩展是很少需要和很少使用，但如果找到它需要，您会很高兴它存在。

**X:null 演示**页说明了一种情况时`x:Null`可能非常方便。 假设您定义一种隐式`Style`有关`Label`其中包括`Setter`，用于设置`FontFamily`属性设置为依赖于平台的系列名称：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="MarkupExtensions.NullDemoPage"
             Title="x:Null Demo">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style TargetType="Label">
                <Setter Property="FontSize" Value="48" />
                <Setter Property="FontFamily">
                    <Setter.Value>
                        <OnPlatform x:TypeArguments="x:String">
                            <On Platform="iOS" Value="Times New Roman" />
                            <On Platform="Android" Value="serif" />
                            <On Platform="UWP" Value="Times New Roman" />
                        </OnPlatform>
                    </Setter.Value>
                </Setter>
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>

    <ContentPage.Content>
        <StackLayout Padding="10, 0">
            <Label Text="Text 1" />
            <Label Text="Text 2" />

            <Label Text="Text 3"
                   FontFamily="{x:Null}" />

            <Label Text="Text 4" />
            <Label Text="Text 5" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>   
```

然后你将发现之一`Label`元素，所需的所有属性设置的隐式`Style`除`FontFamily`，想要将默认值。 您可以定义另一个`Style`为此目的，但更简单的方法是只需设置`FontFamily`属性的特定`Label`到`x:Null`，如下所示在中心`Label`。

下面是正在运行的程序：

[![x:Null 演示](consuming-images/nulldemo-small.png "x:Null 演示")](consuming-images/nulldemo-large.png#lightbox "x:Null 演示")

请注意，该四个的`Label`元素具有衬线字体，但在中心`Label`具有默认的 sans-serif 字体。

<a name="onplatform" />

## <a name="onplatform-markup-extension"></a>OnPlatform 标记扩展

`OnPlatform`标记扩展允许您自定义根据每个平台的 UI 外观。 它提供了相同的功能[ `OnPlatform` ](xref:Xamarin.Forms.OnPlatform`1)并[ `On` ](xref:Xamarin.Forms.On)类，但具有更简洁表示形式。

`OnPlatform`标记扩展受[ `OnPlatformExtension` ](xref:Xamarin.Forms.Xaml.OnPlatformExtension)类，该类定义以下属性：

- `Default` 类型的`object`，设置为默认值应用于表示平台的属性。
- `Android` 类型的`object`，设置一个值为要应用在 Android 上。
- `GTK` 类型的`object`，设置一个值为要应用于 GTK 平台。
- `iOS` 类型的`object`，设置为值用于在 iOS 上应用。
- `macOS` 类型的`object`，设置为值用于在 macOS 上应用。
- `Tizen` 类型的`object`，设置一个值为要应用于 Tizen 平台。
- `UWP` 类型的`object`，设置一个值为通用 Windows 平台上应用。
- `WPF` 类型的`object`，设置一个值为要应用于 Windows Presentation Foundation 平台。
- `Converter` 类型的`IValueConverter`，设置为`IValueConverter`实现。
- `ConverterParameter` 类型的`object`，设置一个值为要传递给`IValueConverter`实现。

> [!NOTE]
> 此 XAML 分析器允许[ `OnPlatformExtension` ](xref:Xamarin.Forms.Xaml.OnPlatformExtension)类以缩写为`OnPlatform`。

`Default`属性是 content 属性`OnPlatformExtension`。 因此，对于使用大括号表示 XAML 标记表达式，则可以消除`Default=`表达式的一部分提供，它是第一个参数。 如果未设置 `Default` 属性，则默认情况下，它将默认为[`BindableProperty.DefaultValue`](xref:Xamarin.Forms.BindableProperty.DefaultValue)属性值，前提是标记扩展以[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)为目标。

> [!IMPORTANT]
> XAML 分析器要求正确类型的值将会提供给属性使用`OnPlatform`标记扩展。 如果类型转换，则有必要，`OnPlatform`标记扩展将尝试执行它使用 Xamarin.Forms 提供的默认值转换器。 但是，有不能通过默认转换器，在这些情况下执行某些类型转换`Converter`属性应设置为`IValueConverter`实现。

**OnPlatform 演示**页显示了如何使用`OnPlatform`标记扩展：

```xaml
<BoxView Color="{OnPlatform Yellow, iOS=Red, Android=Green, UWP=Blue}"
         WidthRequest="{OnPlatform 250, iOS=200, Android=300, UWP=400}"  
         HeightRequest="{OnPlatform 250, iOS=200, Android=300, UWP=400}"
         HorizontalOptions="Center" />
```

在此示例中，所有这三个`OnPlatform`表达式使用的缩写的形式`OnPlatformExtension`类名。 这三个`OnPlatform`标记扩展集[ `Color` ](xref:Xamarin.Forms.BoxView.Color)， [ `WidthRequest` ](xref:Xamarin.Forms.VisualElement.WidthRequest)，并且[ `HeightRequest` ](xref:Xamarin.Forms.VisualElement.HeightRequest)属性[`BoxView` ](xref:Xamarin.Forms.BoxView)为 iOS、 Android 和 UWP 上不同的值。 标记扩展还为未指定，同时消除了在平台上的这些属性提供默认值`Default=`表达式的一部分。 请注意，由逗号分隔的标记扩展属性的设置。

下面是正在运行的程序：

[![OnPlatform 演示](consuming-images/onplatformdemo-small.png "OnPlatform 演示")](consuming-images/onplatformdemo-large.png#lightbox "OnPlatform 演示")

<a name="onidiom" />

## <a name="onidiom-markup-extension"></a>OnIdiom 标记扩展

`OnIdiom` 标记扩展使你可以基于运行应用程序的设备的用法来自定义 UI 外观。 它受[ `OnIdiomExtension` ](xref:Xamarin.Forms.Xaml.OnIdiomExtension)类，该类定义以下属性：

- `Default` 类型的`object`，设置为默认值应用于设备的惯用语言表示的属性。
- `Phone` 类型的`object`，设置为值用于在手机上应用。
- `Tablet` 类型的`object`，若要在平板电脑上应用的值设置。
- `Desktop` 类型的`object`，设置为值用于在桌面平台上应用。
- `TV` 类型的`object`，设置一个值为要应用于电视平台。
- `Watch` 类型的`object`，设置为值应用于监视平台。
- `Converter` 类型的`IValueConverter`，设置为`IValueConverter`实现。
- `ConverterParameter` 类型的`object`，设置一个值为要传递给`IValueConverter`实现。

> [!NOTE]
> 此 XAML 分析器允许[ `OnIdiomExtension` ](xref:Xamarin.Forms.Xaml.OnIdiomExtension)类以缩写为`OnIdiom`。

`Default`属性是 content 属性`OnIdiomExtension`。 因此，对于使用大括号表示 XAML 标记表达式，则可以消除`Default=`表达式的一部分提供，它是第一个参数。

> [!IMPORTANT]
> XAML 分析器要求正确类型的值将会提供给属性使用`OnIdiom`标记扩展。 如果类型转换，则有必要，`OnIdiom`标记扩展将尝试执行它使用 Xamarin.Forms 提供的默认值转换器。 但是，有不能通过默认转换器，在这些情况下执行某些类型转换`Converter`属性应设置为`IValueConverter`实现。

**OnIdiom 演示**页显示了如何使用`OnIdiom`标记扩展：

```xaml
<BoxView Color="{OnIdiom Yellow, Phone=Red, Tablet=Green, Desktop=Blue}"
         WidthRequest="{OnIdiom 100, Phone=200, Tablet=300, Desktop=400}"
         HeightRequest="{OnIdiom 100, Phone=200, Tablet=300, Desktop=400}"
         HorizontalOptions="Center" />
```

在此示例中，所有这三个`OnIdiom`表达式使用的缩写的形式`OnIdiomExtension`类名。 这三个`OnIdiom`标记扩展集[ `Color` ](xref:Xamarin.Forms.BoxView.Color)， [ `WidthRequest` ](xref:Xamarin.Forms.VisualElement.WidthRequest)，并且[ `HeightRequest` ](xref:Xamarin.Forms.VisualElement.HeightRequest)属性[`BoxView` ](xref:Xamarin.Forms.BoxView)为手机、 平板电脑和桌面的惯用语言在不同的值。 标记扩展还提供默认值为这些属性在未指定，同时消除习惯用语`Default=`表达式的一部分。 请注意，由逗号分隔的标记扩展属性的设置。

下面是正在运行的程序：

[![OnIdiom 演示](consuming-images/onidiomdemo-small.png "OnIdiom 演示")](consuming-images/onidiomdemo-large.png#lightbox "OnIdiom 演示")

## <a name="datatemplate-markup-extension"></a>System.windows.datatemplate> 标记扩展

`DataTemplate` 标记扩展允许将类型转换为[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)。 它受 `DataTemplateExtension` 类支持，该类定义类型 `string`的 `TypeName` 属性，该属性设置为要转换为 `DataTemplate`的类型的名称。 `TypeName`属性是 content 属性`DataTemplateExtension`。 因此，对于用大括号表示的 XAML 标记表达式，可以删除这类表达式的 `TypeName=` 部分。

> [!NOTE]
> XAML 分析程序允许将 `DataTemplateExtension` 类缩写为 `DataTemplate`。

此标记扩展的典型用法是在 Shell 应用程序中，如下面的示例中所示：

```xaml
<ShellContent Title="Monkeys"
              Icon="monkey.png"
              ContentTemplate="{DataTemplate views:MonkeysPage}" />
```

在此示例中，将 `MonkeysPage` 从[`ContentPage`](xref:Xamarin.Forms.ContentPage)转换为[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)，后者设置为 `ShellContent.ContentTemplate` 属性的值。 这可确保仅在导航到页面时创建 `MonkeysPage`，而不是在应用程序启动时创建。

有关 Shell 应用程序的详细信息，请参阅[Xamarin。 Forms Shell](~/xamarin-forms/app-fundamentals/shell/index.md)。

## <a name="fontimage-markup-extension"></a>FontImage 标记扩展

使用 `FontImage` 标记扩展，可以在任何可以显示 `ImageSource`的视图中显示字体图标。 它提供与 `FontImageSource` 类相同的功能，但具有更简洁的表示形式。

`FontImageExtension` 类支持 `FontImage` 标记扩展，并定义以下属性：

- `string`类型的 `FontFamily`，字体图标所属的字体系列。
- 类型 `string`的 `Glyph`，字体图标的 unicode 字符值。
- [`Color`](xref:Xamarin.Forms.Color)类型的 `Color`，显示字体图标时要使用的颜色。
- `double`类型的 `Size`，呈现的字体图标的大小，以与设备无关的单位。 默认值为 30。 此外，此属性可以设置为命名字体大小。

> [!NOTE]
> XAML 分析程序允许将 `FontImageExtension` 类缩写为 `FontImage`。

`Glyph`属性是 content 属性`FontImageExtension`。 因此，对于使用大括号表示 XAML 标记表达式，则可以消除`Glyph=`表达式的一部分提供，它是第一个参数。

**FontImage 演示**页显示了如何使用 `FontImage` 标记扩展：

```xaml
<Image BackgroundColor="#D1D1D1"
       Source="{FontImage &#xf30c;, FontFamily={OnPlatform iOS=Ionicons, Android=ionicons.ttf#}, Size=44}" />
```

在此示例中，`FontImageExtension` 类名称的缩写形式用于在[`Image`](xref:Xamarin.Forms.Image)中显示 Ionicons 字体系列中的 XBox 图标。 表达式还使用 `OnPlatform` 标记扩展在 iOS 和 Android 上指定不同的 `FontFamily` 属性值。 此外，还消除了表达式的 `Glyph=` 部分，而设置的标记扩展属性则用逗号分隔。 请注意，尽管图标的 unicode 字符是 `\uf30c`的，但它必须在 XAML 中进行转义，因此 `&#xf30c;`。

下面是正在运行的程序：

[![FontImage 标记扩展的屏幕截图](consuming-images/fontimagedemo.png "FontImage 演示")](consuming-images/fontimagedemo-large.png#lightbox "FontImage 演示")

有关通过在 `FontImageSource` 对象中指定字体图标数据来显示字体图标的信息，请参阅[显示字体图标](~/xamarin-forms/user-interface/text/fonts.md#display-font-icons)。

## <a name="define-your-own-markup-extensions"></a>定义你自己的标记扩展

如果遇到过需要在 Xamarin.Forms 中不可用的 XAML 标记扩展，则可以[创建您自己](creating.md)。

## <a name="related-links"></a>相关链接

- [标记扩展 （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-markupextensions)
- [从 Xamarin.Forms 书籍的 XAML 标记扩展一章](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter10.md)
- [资源字典](~/xamarin-forms/xaml/resource-dictionaries.md)
- [动态样式](~/xamarin-forms/user-interface/styles/dynamic.md)
- [数据绑定](~/xamarin-forms/app-fundamentals/data-binding/index.md)
- [Xamarin。 Forms Shell](~/xamarin-forms/app-fundamentals/shell/index.md)。
