---
title: 使用 XAML 标记扩展
description: 本文介绍如何使用 Xamarin. Forms XAML 标记扩展通过允许从各种源设置元素特性来增强 XAML 的强大功能和灵活性。
ms.prod: xamarin
ms.assetid: CE686893-609C-4EC3-9225-6C68D2A9F79C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/27/2019
ms.openlocfilehash: a8698975d2609599e1404fbb9c87c617a54f23d7
ms.sourcegitcommit: 21d8be9571a2fa89fb7d8ff0787ff4f957de0985
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/21/2019
ms.locfileid: "72696350"
---
# <a name="consuming-xaml-markup-extensions"></a>使用 XAML 标记扩展

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-markupextensions)

XAML 标记扩展允许从多个源设置元素特性，从而帮助提高 XAML 的功能和灵活性。 Xaml 2009 规范包含若干 XAML 标记扩展。 它们显示在 XAML 文件中，并且具有习惯 `x` 命名空间前缀，通常是用此前缀引用的。 本文介绍以下标记扩展：

- [`x:Static`](#static) –引用静态属性、字段或枚举成员。
- [`x:Reference`](#reference) –引用页上的命名元素。
- [`x:Type`](#type) –设置 `System.Type` 对象的特性。
- [`x:Array`](#array) –构造特定类型的对象的数组。
- [`x:Null`](#null) –将属性设置为 `null` 值。
- [`OnPlatform`](#onplatform) –根据每个平台自定义 UI 外观。
- [`OnIdiom`](#onidiom) –基于运行应用程序的设备的用法自定义 UI 外观。
- [`DataTemplate`](#datatemplate-markup-extension) -将类型转换为[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)。
- [`FontImage`](#fontimage-markup-extension) -在可显示 `ImageSource` 的任何视图中显示字体图标。

其他 xaml 实现一直以来已支持其他 xaml 标记扩展，Xamarin 也支持。 其他文章中更全面地描述了这些内容：

- `StaticResource` 引用资源字典中的对象，如[**资源**](~/xamarin-forms/xaml/resource-dictionaries.md)字典一文中所述。
- `DynamicResource`-响应资源字典中对象的更改，如[**动态样式**](~/xamarin-forms/user-interface/styles/dynamic.md)一文中所述。
- `Binding`-在两个对象的属性之间建立链接，如[**数据绑定**](~/xamarin-forms/app-fundamentals/data-binding/index.md)一文中所述。
- `TemplateBinding`-从控件模板执行数据绑定，如[**从控件模板绑定一**](~/xamarin-forms/app-fundamentals/templates/control-templates/template-binding.md)文中所述。
- `RelativeSource`-设置相对于绑定目标位置的绑定源，如[相关绑定](~/xamarin-forms/app-fundamentals/data-binding/relative-bindings.md)一文中所述。

[@No__t_1](xref:Xamarin.Forms.RelativeLayout)布局会[`ConstraintExpression`](xref:Xamarin.Forms.ConstraintExpression)使用自定义标记扩展。 [**RelativeLayout**](~/xamarin-forms/user-interface/layouts/relative-layout.md)一文中介绍了此标记扩展。

<a name="static" />

## <a name="xstatic-markup-extension"></a>x:Static 标记扩展

[@No__t_2](xref:Xamarin.Forms.Xaml.StaticExtension)类支持 `x:Static` 标记扩展。 类具有一个名为[`Member`](xref:Xamarin.Forms.Xaml.StaticExtension.Member)类型 `string` 的属性，该属性设置为公共常量、静态属性、静态字段或枚举成员的名称。

使用 `x:Static` 的一种常用方法是先使用一些常量或静态变量定义一个类，如[**MarkupExtensions**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-markupextensions)程序中这一小 `AppConstants` 类：

```csharp
static class AppConstants
{
    public static double NormalFontSize = 18;
}
```

**X:Static 演示**页演示了几种使用 `x:Static` 标记扩展的方法。 最详细的方法实例化 `StaticExtension` 类 `Label.FontSize` 属性元素标记之间：

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

XAML 分析器还允许将 `StaticExtension` 类缩写为 `x:Static`：

```xaml
<Label Text="Label No. 2">
    <Label.FontSize>
        <x:Static Member="local:AppConstants.NormalFontSize" />
    </Label.FontSize>
</Label>
```

这可以进一步简化，但更改会引入一些新语法：它包含将 `StaticExtension` 类和成员设置置于大括号中。 生成的表达式直接设置为 `FontSize` 属性：

```xaml
<Label Text="Label No. 3"
       FontSize="{x:StaticExtension Member=local:AppConstants.NormalFontSize}" />
```

请注意，大括号内*没有引号。* @No__t_1 的 `Member` 属性不再是 XML 特性。 它是标记扩展的表达式的一部分。

正如你可以在将其用作对象元素时 `x:Static` 进行缩写 `x:StaticExtension`，还可以在大括号内的表达式中对其进行缩写：

```xaml
<Label Text="Label No. 4"
       FontSize="{x:Static Member=local:AppConstants.NormalFontSize}" />
```

@No__t_0 类具有引用属性 `Member` 的 `ContentProperty` 属性，该属性将此属性标记为类的默认内容属性。 对于用大括号表示的 XAML 标记扩展，可以消除表达式的 `Member=` 部分：

```xaml
<Label Text="Label No. 5"
       FontSize="{x:Static local:AppConstants.NormalFontSize}" />
```

这是 `x:Static` 标记扩展最常见的形式。

"**静态演示**" 页包含两个其他示例。 XAML 文件的根标记包含 .NET `System` 命名空间的 XML 命名空间声明：

```xaml
xmlns:sys="clr-namespace:System;assembly=mscorlib"
```

这允许 `Label` 字体大小设置为静态字段 `Math.PI`。 这会导致小文本，因此 `Scale` 属性设置为 `Math.E`：

```xaml
<Label Text="&#x03C0; &#x00D7; E sized text"
       FontSize="{x:Static sys:Math.PI}"
       Scale="{x:Static sys:Math.E}"
       HorizontalOptions="Center" />
```

最后一个示例显示 `Device.RuntimePlatform` 值。 @No__t_0 静态属性用于在两个 `Span` 对象之间插入一个换行符：

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

下面是运行的示例：

[![x:Static 演示](consuming-images/staticdemo-small.png "x:Static 演示")](consuming-images/staticdemo-large.png#lightbox "x:Static 演示")

<a name="reference" />

## <a name="xreference-markup-extension"></a>x:Reference 标记扩展

[@No__t_2](xref:Xamarin.Forms.Xaml.ReferenceExtension)类支持 `x:Reference` 标记扩展。 类有一个名为[`Name`](xref:Xamarin.Forms.Xaml.ReferenceExtension.Name)类型 `string` 的属性，该属性设置为页面上已为其指定了 `x:Name` 名称的元素的名称。 此 `Name` 属性是 `ReferenceExtension` 的 content 属性，因此 `x:Reference` 出现在大括号中时不需要 `Name=`。

@No__t_0 标记扩展以独占方式用于数据绑定，这将在[**数据绑定**](~/xamarin-forms/app-fundamentals/data-binding/index.md)一文中进行详细介绍。

**X:Reference 演示**页显示了两种使用数据绑定的 `x:Reference`，第一个使用它来设置 `Binding` 对象的 `Source` 属性; 第二个用法用于设置两个数据绑定的 `BindingContext` 属性:

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

这两个表达式 `x:Reference` 都使用 `ReferenceExtension` 类名称的缩写形式并消除表达式的 `Name=` 部分。 在第一个示例中，`x:Reference` 标记扩展嵌入 `Binding` 标记扩展中。 请注意，`Source` 和 `StringFormat` 设置由逗号分隔。 下面是正在运行的程序：

[![x:Reference 演示](consuming-images/referencedemo-small.png "x:Reference 演示")](consuming-images/referencedemo-large.png#lightbox "x:Reference 演示")

<a name="type" />

## <a name="xtype-markup-extension"></a>x:Type 标记扩展

@No__t_0 标记扩展是等效C#于 XAML 的[`typeof`](/dotnet/csharp/language-reference/keywords/typeof/)关键字。 它受[`TypeExtension`](xref:Xamarin.Forms.Xaml.TypeExtension)类支持，该类定义设置为类或结构名称的类型 `string` 的一个名为[`TypeName`](xref:Xamarin.Forms.Xaml.TypeExtension.TypeName)的属性。 @No__t_0 标记扩展返回该类或结构的[`System.Type`](xref:System.Type)对象。 `TypeName` 是 `TypeExtension` 的 content 属性，因此当 `x:Type` 显示大括号时，不需要 `TypeName=`。

在 Xamarin 中，有几个属性具有类型 `Type` 的参数。 示例包括 `Style` 的[`TargetType`](xref:Xamarin.Forms.Style.TargetType)属性，以及用于指定泛型类中的参数的[x:TypeArguments](~/xamarin-forms/xaml/passing-arguments.md#generic_type_arguments)特性。 但 XAML 分析器会自动执行 `typeof` 操作，在这些情况下不使用 `x:Type` 标记扩展。

需要 `x:Type` 的一个*位置是 `x:Array`* 标记扩展，[下一部分](#array)将对此进行介绍。

当构造菜单，其中每个菜单项对应于特定类型的对象时，`x:Type` 标记扩展也很有用。 您可以将 `Type` 对象与每个菜单项相关联，然后在选定菜单项时实例化对象。

这就是**标记扩展**程序中 `MainPage` 的导航菜单的工作方式。 **MainPage**文件包含一个 `TableView`，其中每个 `TextCell` 对应于程序中的特定页面：

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

下面是**标记扩展**中的打开主页：

[![主页](consuming-images/mainpage-small.png "主页")](consuming-images/mainpage-large.png#lightbox "主页")

每个 `CommandParameter` 属性都设置为引用其他页之一的 `x:Type` 标记扩展。 @No__t_0 属性绑定到名为 `NavigateCommand` 的属性。 此属性在 `MainPage` 代码隐藏文件中定义：

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

@No__t_0 属性是一个 `Command` 对象，该对象实现一个带有类型 `Type` 参数的 execute 命令，&mdash; 值为 `CommandParameter`。 方法使用 `Activator.CreateInstance` 来实例化页面，然后导航到该页面。 构造函数结束时，将页面的 `BindingContext` 设置为其自身，这样，`Command` 上的 `Binding` 才能工作。 有关此类代码的更多详细信息，请参阅[**数据绑定**](~/xamarin-forms/app-fundamentals/data-binding/index.md)一文和[**命令**](~/xamarin-forms/app-fundamentals/data-binding/commanding.md)项目。

**X:Type 演示**页使用类似的技术来实例化 Xamarin 和 Forms 元素，并将它们添加到 `StackLayout` 中。 XAML 文件最初由三个 `Button` 元素组成，它们的 `Command` 属性设置为 `Binding`，`CommandParameter` 属性设置为三个 Xamarin 的类型。 Forms 视图：

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

代码隐藏文件定义并初始化 `CreateCommand` 属性：

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

按下 `Button` 时执行的方法将创建参数的新实例，设置其 `VerticalOptions` 属性，并将其添加到 `StackLayout` 中。 然后，这三个 `Button` 元素将与动态创建的视图共享页面：

[![x:Type 演示](consuming-images/typedemo-small.png "x:Type 演示")](consuming-images/typedemo-large.png#lightbox "x:Type 演示")

<a name="array" />

## <a name="xarray-markup-extension"></a>x:Array 标记扩展

@No__t_0 标记扩展允许您在标记中定义数组。 它由[`ArrayExtension`](xref:Xamarin.Forms.Xaml.ArrayExtension)类支持，该类定义了两个属性：

- `Type` 类型的 `Type`，指示数组中元素的类型。
- `IList` 类型的 `Items`，它是项本身的集合。 这是 `ArrayExtension` 的 content 属性。

@No__t_0 标记扩展本身从不出现在大括号中。 相反，`x:Array` 开始标记和结束标记来分隔项列表。 将 `Type` 属性设置为 `x:Type` 标记扩展。

**X:Array 演示**页演示了如何通过将 `ItemsSource` 属性设置为一个数组，使用 `x:Array` 向 `ListView` 添加项：

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

@No__t_0 为每个颜色条目创建简单 `BoxView`：

[![x:Array 演示](consuming-images/arraydemo-small.png "x:Array 演示")](consuming-images/arraydemo-large.png#lightbox "x:Array 演示")

可以通过多种方式指定此数组中的单个 `Color` 项。 您可以使用 `x:Static` 标记扩展：

```xaml
<x:Static Member="Color.Blue" />
```

或者，您可以使用 `StaticResource` 从资源字典中检索颜色：

```xaml
<StaticResource Key="myColor" />
```

在本文末尾，你将看到一个自定义 XAML 标记扩展，它还会创建一个新的颜色值：

```xaml
<local:HslColor H="0.5" S="1.0" L="0.5" />
```

定义常见类型（如字符串或数字）的数组时，请使用[**传递构造函数参数**](~/xamarin-forms/xaml/passing-arguments.md#constructor_arguments)一文中列出的标记来分隔值。

<a name="null" />

## <a name="xnull-markup-extension"></a>x:Null 标记扩展

[@No__t_2](xref:Xamarin.Forms.Xaml.NullExtension)类支持 `x:Null` 标记扩展。 它没有属性，只是 XAML 等效项C# [`null`](/dotnet/csharp/language-reference/keywords/null/)关键字。

@No__t_0 标记扩展很少需要并且很少使用，但如果确实需要它，则很高兴您已经存在。

**X:Null 演示**页说明了 `x:Null` 可能很方便的一种情况。 假设您为 `Label` 定义了隐式 `Style`，其中包括将 `FontFamily` 属性设置为依赖于平台的系列名称的 `Setter`：

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

然后，你会发现，对于其中一个 `Label` 元素，你希望隐式 `Style` 中的所有属性设置（要作为默认值的 `FontFamily` 除外）。 您可以为此目的定义另一个 `Style` 但更简单的方法是将特定 `Label` 的 `FontFamily` 属性设置为 `x:Null`，如中心 `Label` 中所示。

下面是正在运行的程序：

[![x:Null 演示](consuming-images/nulldemo-small.png "x:Null 演示")](consuming-images/nulldemo-large.png#lightbox "x:Null 演示")

请注意，四个 `Label` 元素都有一个衬线字体，但中心 `Label` 具有默认的 sans-serif 字体。

<a name="onplatform" />

## <a name="onplatform-markup-extension"></a>OnPlatform 标记扩展

@No__t_0 标记扩展允许你根据每个平台自定义 UI 外观。 它提供与[`OnPlatform`](xref:Xamarin.Forms.OnPlatform`1)和[`On`](xref:Xamarin.Forms.On)类相同的功能，但具有更简洁的表示形式。

[@No__t_2](xref:Xamarin.Forms.Xaml.OnPlatformExtension)类支持 `OnPlatform` 标记扩展，该类定义以下属性：

- `object` 类型的 `Default`，将设置为应用于表示平台的属性的默认值。
- `object` 类型的 `Android`，你将设置为要在 Android 上应用的值。
- `object` 类型的 `GTK`，将设置为要在 GTK 平台上应用的值。
- `object` 类型的 `iOS`，你将设置为要在 iOS 上应用的值。
- `object` 类型的 `macOS`，将设置为要在 macOS 上应用的值。
- `object` 类型的 `Tizen`，将设置为要在 Tizen 平台上应用的值。
- `object` 类型的 `UWP`，您将设置为要在通用 Windows 平台上应用的值。
- `object` 类型的 `WPF`，将设置为要在 Windows Presentation Foundation 平台上应用的值。
- `IValueConverter` 类型的 `Converter`，您设置为 `IValueConverter` 实现。
- `object` 类型的 `ConverterParameter`，您将设置为要传递到 `IValueConverter` 实现的值。

> [!NOTE]
> XAML 分析器允许将[`OnPlatformExtension`](xref:Xamarin.Forms.Xaml.OnPlatformExtension)类缩写为 `OnPlatform`。

@No__t_0 属性是 `OnPlatformExtension` 的 content 属性。 因此，对于用大括号表示的 XAML 标记表达式，可以消除表达式的 `Default=` 部分，前提是它是第一个参数。 如果未设置 `Default` 属性，则默认情况下，它将默认为[`BindableProperty.DefaultValue`](xref:Xamarin.Forms.BindableProperty.DefaultValue)属性值，前提是标记扩展以[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)为目标。

> [!IMPORTANT]
> XAML 分析器需要向使用 `OnPlatform` 标记扩展的属性提供正确类型的值。 如果类型转换是必需的，则 `OnPlatform` 标记扩展将尝试使用由 Xamarin 提供的默认转换器执行该转换。 但是，某些类型转换无法由默认转换器执行，在这些情况下，应将 `Converter` 属性设置为 `IValueConverter` 实现。

**OnPlatform 演示**页显示了如何使用 `OnPlatform` 标记扩展：

```xaml
<BoxView Color="{OnPlatform Yellow, iOS=Red, Android=Green, UWP=Blue}"
         WidthRequest="{OnPlatform 250, iOS=200, Android=300, UWP=400}"  
         HeightRequest="{OnPlatform 250, iOS=200, Android=300, UWP=400}"
         HorizontalOptions="Center" />
```

在此示例中，所有三个 `OnPlatform` 表达式都使用 `OnPlatformExtension` 类名称的缩写形式。 这三个 `OnPlatform` 标记扩展将[`BoxView`](xref:Xamarin.Forms.BoxView)的[`Color`](xref:Xamarin.Forms.BoxView.Color)、 [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest)和[`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest)属性设置为 iOS、Android 和 UWP 上的不同值。 标记扩展还为未指定的平台上的这些属性提供默认值，同时消除表达式的 `Default=` 部分。 请注意，设置的标记扩展属性用逗号分隔。

下面是正在运行的程序：

[![OnPlatform 演示](consuming-images/onplatformdemo-small.png "OnPlatform 演示")](consuming-images/onplatformdemo-large.png#lightbox "OnPlatform 演示")

<a name="onidiom" />

## <a name="onidiom-markup-extension"></a>OnIdiom 标记扩展

@No__t_0 标记扩展使你可以基于运行应用程序的设备的用法来自定义 UI 外观。 它由[`OnIdiomExtension`](xref:Xamarin.Forms.Xaml.OnIdiomExtension)类支持，该类定义以下属性：

- `object` 类型的 `Default`，将设置为应用于表示设备惯例的属性的默认值。
- `object` 类型的 `Phone`，您将设置为要在手机上应用的值。
- `object` 类型的 `Tablet`，将设置为要在平板电脑上应用的值。
- `object` 类型的 `Desktop`，你将设置为要在桌面平台上应用的值。
- `object` 类型的 `TV`，您将其设置为在电视平台上应用的值。
- `object` 类型的 `Watch`，您将其设置为在监视平台上应用的值。
- `IValueConverter` 类型的 `Converter`，您设置为 `IValueConverter` 实现。
- `object` 类型的 `ConverterParameter`，您将设置为要传递到 `IValueConverter` 实现的值。

> [!NOTE]
> XAML 分析器允许将[`OnIdiomExtension`](xref:Xamarin.Forms.Xaml.OnIdiomExtension)类缩写为 `OnIdiom`。

@No__t_0 属性是 `OnIdiomExtension` 的 content 属性。 因此，对于用大括号表示的 XAML 标记表达式，可以消除表达式的 `Default=` 部分，前提是它是第一个参数。

> [!IMPORTANT]
> XAML 分析器需要向使用 `OnIdiom` 标记扩展的属性提供正确类型的值。 如果类型转换是必需的，则 `OnIdiom` 标记扩展将尝试使用由 Xamarin 提供的默认转换器执行该转换。 但是，某些类型转换无法由默认转换器执行，在这些情况下，应将 `Converter` 属性设置为 `IValueConverter` 实现。

**OnIdiom 演示**页显示了如何使用 `OnIdiom` 标记扩展：

```xaml
<BoxView Color="{OnIdiom Yellow, Phone=Red, Tablet=Green, Desktop=Blue}"
         WidthRequest="{OnIdiom 100, Phone=200, Tablet=300, Desktop=400}"
         HeightRequest="{OnIdiom 100, Phone=200, Tablet=300, Desktop=400}"
         HorizontalOptions="Center" />
```

在此示例中，所有三个 `OnIdiom` 表达式都使用 `OnIdiomExtension` 类名称的缩写形式。 这三个 `OnIdiom` 标记扩展将[`BoxView`](xref:Xamarin.Forms.BoxView)的[`Color`](xref:Xamarin.Forms.BoxView.Color)、 [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest)和[`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest)属性设置为手机、平板电脑和桌面惯例上不同的值。 标记扩展还为未指定的惯例中的这些属性提供默认值，同时消除表达式的 `Default=` 部分。 请注意，设置的标记扩展属性用逗号分隔。

下面是正在运行的程序：

[![OnIdiom 演示](consuming-images/onidiomdemo-small.png "OnIdiom 演示")](consuming-images/onidiomdemo-large.png#lightbox "OnIdiom 演示")

## <a name="datatemplate-markup-extension"></a>System.windows.datatemplate> 标记扩展

@No__t_0 标记扩展允许将类型转换为[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)。 它受 `DataTemplateExtension` 类支持，该类定义类型 `string` 的 `TypeName` 属性，该属性设置为要转换为 `DataTemplate` 的类型的名称。 @No__t_0 属性是 `DataTemplateExtension` 的 content 属性。 因此，对于用大括号表示的 XAML 标记表达式，可以消除表达式的 `TypeName=` 部分。

> [!NOTE]
> XAML 分析器允许将 `DataTemplateExtension` 类缩写为 `DataTemplate`。

此标记扩展的典型用法是在 Shell 应用程序中，如下面的示例中所示：

```xaml
<ShellContent Title="Monkeys"
              Icon="monkey.png"
              ContentTemplate="{DataTemplate views:MonkeysPage}" />
```

在此示例中，将 `MonkeysPage` 从[`ContentPage`](xref:Xamarin.Forms.ContentPage)转换为[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)，后者设置为 `ShellContent.ContentTemplate` 属性的值。 这可确保仅在导航到页面时创建 `MonkeysPage`，而不是在应用程序启动时创建。

有关 Shell 应用程序的详细信息，请参阅[Xamarin。 Forms Shell](~/xamarin-forms/app-fundamentals/shell/index.md)。

## <a name="fontimage-markup-extension"></a>FontImage 标记扩展

使用 `FontImage` 标记扩展，可以在任何可以显示 `ImageSource` 的视图中显示字体图标。 它提供与 `FontImageSource` 类相同的功能，但具有更简洁的表示形式。

@No__t_1 类支持 `FontImage` 标记扩展，该类定义以下属性：

- `string` 类型的 `FontFamily`，字体图标所属的字体系列。
- 类型 `string` 的 `Glyph`，字体图标的 unicode 字符值。
- `Color` 类型的 `Color`，显示字体图标时要使用的颜色。
- `double` 类型的 `Size`，呈现的字体图标的大小，以与设备无关的单位。

> [!NOTE]
> XAML 分析器允许将 `FontImageExtension` 类缩写为 `FontImage`。

@No__t_0 属性是 `FontImageExtension` 的 content 属性。 因此，对于用大括号表示的 XAML 标记表达式，可以消除表达式的 `Glyph=` 部分，前提是它是第一个参数。

**FontImage 演示**页显示了如何使用 `FontImage` 标记扩展：

```xaml
<Image BackgroundColor="#D1D1D1"
       Source="{FontImage &#xf30c;, FontFamily={OnPlatform iOS=Ionicons, Android=ionicons.ttf#}, Size=44}" />
```

在此示例中，`FontImageExtension` 类名称的缩写形式用于在[`Image`](xref:Xamarin.Forms.Image)中显示 Ionicons 字体系列中的 XBox 图标。 表达式还使用 `OnPlatform` 标记扩展在 iOS 和 Android 上指定不同的 `FontFamily` 属性值。 此外，还消除了表达式的 `Glyph=` 部分，而设置的标记扩展属性则用逗号分隔。 请注意，尽管图标的 unicode 字符是 `\uf30c` 的，但它必须在 XAML 中进行转义，因此 `&#xf30c;`。

下面是正在运行的程序：

[![FontImage 标记扩展的屏幕截图](consuming-images/fontimagedemo.png "FontImage 演示")](consuming-images/fontimagedemo-large.png#lightbox "FontImage 演示")

有关通过在 `FontImageSource` 对象中指定字体图标数据来显示字体图标的信息，请参阅[显示字体图标](~/xamarin-forms/user-interface/text/fonts.md#display-font-icons)。

## <a name="define-your-own-markup-extensions"></a>定义自己的标记扩展

如果你遇到了在 Xamarin. Forms 中不可用的 XAML 标记扩展的需要，则可以[创建自己](creating.md)的扩展。

## <a name="related-links"></a>相关链接

- [标记扩展（示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-markupextensions)
- [Xamarin 中的 XAML 标记扩展章节](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter10.md)
- [资源字典](~/xamarin-forms/xaml/resource-dictionaries.md)
- [动态样式](~/xamarin-forms/user-interface/styles/dynamic.md)
- [数据绑定](~/xamarin-forms/app-fundamentals/data-binding/index.md)
- [Xamarin。 Forms Shell](~/xamarin-forms/app-fundamentals/shell/index.md)。
