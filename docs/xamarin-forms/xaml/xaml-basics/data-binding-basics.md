---
title: 第 4 部分。 数据绑定基础知识
description: 数据绑定使两个用于链接，这样一个导致更改中的其他对象的属性。
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 342288C3-BB4C-4924-B178-72E112D777BA
author: davidbritch
ms.author: dabritch
ms.date: 10/25/2017
ms.openlocfilehash: 4a24c8dbb9ab4e23afa03de4ae2dbc55ddfb5fa4
ms.sourcegitcommit: e000cc0765857c1d7f49538df9e62e9d3aa60775
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/18/2019
ms.locfileid: "56334561"
---
# <a name="part-4-data-binding-basics"></a>第 4 部分。 数据绑定基础知识

[![下载示例](~/media/shared/download.png)下载示例](https://developer.xamarin.com/samples/xamarin-forms/XamlSamples/)

_数据绑定使两个用于链接，这样一个导致更改中的其他对象的属性。这是一个非常有价值的工具，并可以完全在代码中定义数据绑定，而 XAML 提供快捷方式和便利。因此，在 Xamarin.Forms 中最重要的标记扩展之一绑定。_

## <a name="data-bindings"></a>数据绑定

数据绑定连接两个对象，调用的属性*源*并*目标*。 在代码中，则需要两个步骤：`BindingContext`的目标对象的属性必须设置为源对象，并`SetBinding`方法 (通常与结合使用`Binding`类) 必须在要将该对象的属性绑定到源属性的目标对象上调用对象。

目标属性必须是可绑定属性，这意味着目标对象必须派生自`BindableObject`。 联机 Xamarin.Forms 文档将指示哪个属性是可绑定属性。 属性`Label`如`Text`与可绑定属性关联`TextProperty`。

在标记中，您还必须执行相同的两个步骤所需在代码中，只不过`Binding`标记扩展可以代替`SetBinding`调用和`Binding`类。

但是，在 XAML 中定义数据绑定时，有多种方法来设置`BindingContext`的目标对象。 有时从代码隐藏文件中，有时使用设置`StaticResource`或`x:Static`标记扩展的内容有时`BindingContext`属性元素标记。

绑定最常使用与基础数据模型，通常在 MVVM （模型-视图-视图） 的应用程序体系结构、 实现连接程序的视觉对象中所述[第 5 部分。从数据绑定到 MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)，但可能会出现其他情形。

## <a name="view-to-view-bindings"></a>视图-视图绑定

您可以定义要链接的同一页面上的两个视图的属性的数据绑定。 在这种情况下，设置`BindingContext`的目标对象使用`x:Reference`标记扩展。

下面是包含的 XAML 文件`Slider`和两个`Label`视图，其中之一旋转`Slider`值，其中显示了另一个`Slider`值：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.SliderBindingsPage"
             Title="Slider Bindings Page">

    <StackLayout>
        <Label Text="ROTATION"
               BindingContext="{x:Reference Name=slider}"
               Rotation="{Binding Path=Value}"
               FontAttributes="Bold"
               FontSize="Large"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />

        <Slider x:Name="slider"
                Maximum="360"
                VerticalOptions="CenterAndExpand" />

        <Label BindingContext="{x:Reference slider}"
               Text="{Binding Value, StringFormat='The angle is {0:F0} degrees'}"
               FontAttributes="Bold"
               FontSize="Large"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />
    </StackLayout>
</ContentPage>
```

`Slider`包含`x:Name`这两个引用的属性`Label`视图使用`x:Reference`标记扩展。

`x:Reference`绑定扩展定义名为的属性`Name`若要在这种情况下设置为引用的元素的名称`slider`。 但是，`ReferenceExtension`定义的类`x:Reference`标记扩展还定义`ContentProperty`属性`Name`，这意味着它不是明确要求。 仅针对各种，第一个`x:Reference`包括"名称 ="，但第二个不：

```xaml
BindingContext="{x:Reference Name=slider}"
…
BindingContext="{x:Reference slider}"
```

`Binding`标记扩展本身可以有多个属性，就像`BindingBase`和`Binding`类。 `ContentProperty`有关`Binding`是`Path`，但"路径 ="可以省略标记扩展的一部分，如果路径中的第一项`Binding`标记扩展。 第一个示例具有"路径 ="，但第二个示例省略它：

```xaml
Rotation="{Binding Path=Value}"
…
Text="{Binding Value, StringFormat='The angle is {0:F0} degrees'}"
```

属性可以是在同一行或分成多行：

```xaml
Text="{Binding Value,
               StringFormat='The angle is {0:F0} degrees'}"
```

执行任何方便。

请注意`StringFormat`在第二个属性`Binding`标记扩展。 在 Xamarin.Forms 中，绑定不执行任何隐式类型转换，并且如果您需要将非字符串对象显示为一个字符串，你必须提供类型转换器或使用`StringFormat`。 在后台，静态`String.Format`方法用于实现`StringFormat`。 这可能是问题，因为.NET 格式规范涉及大括号，还用来分隔标记扩展。 这将创建令人混乱 XAML 分析器的风险。 若要避免这种情况，请用单引号引起来将整个的格式设置字符串：

```xaml
Text="{Binding Value, StringFormat='The angle is {0:F0} degrees'}"
```

下面是正在运行的程序：

[![](data-binding-basics-images/sliderbinding.png "视图-视图绑定")](data-binding-basics-images/sliderbinding-large.png#lightbox "视图来查看绑定 ")

## <a name="the-binding-mode"></a>绑定模式

单个视图对其几个属性具有数据绑定。 但是，每个视图只能有一个`BindingContext`，因此，在该视图上的多个数据绑定必须全部引用同一对象的属性。

向此服务器和其他问题的解决方案涉及`Mode`属性设置为的成员`BindingMode`枚举：

- `Default`
- `OneWay` -值从源传输到目标
- `OneWayToSource` -值从目标传输到源
- `TwoWay` -值传输源和目标之间的这两种方式
- `OneTime` -数据源从转到目标，但仅当`BindingContext`更改

下面的程序说明的一个常见用途`OneWayToSource`和`TwoWay`绑定模式。 四个`Slider`视图都为了控制`Scale`， `Rotate`， `RotateX`，并`RotateY`属性的`Label`。 首先，它看起来像的这四个属性`Label`应为数据绑定目标，因为每个所设置`Slider`。 但是，`BindingContext`的`Label`可以是只有一个对象，并且有四个不同的滑块。

因此，所有绑定中都设置似乎是向后的方式：`BindingContext`的每四个滑块设置为`Label`，并在设置绑定`Value`滑块的属性。 通过使用`OneWayToSource`和`TwoWay`模式下，这些`Value`属性可以设置的源属性，即`Scale`， `Rotate`， `RotateX`，并`RotateY`的属性`Label`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.SliderTransformsPage"
             Padding="5"
             Title="Slider Transforms Page">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="*" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
        </Grid.RowDefinitions>

        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="*" />
            <ColumnDefinition Width="Auto" />
        </Grid.ColumnDefinitions>

        <!-- Scaled and rotated Label -->
        <Label x:Name="label"
               Text="TEXT"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />

        <!-- Slider and identifying Label for Scale -->
        <Slider x:Name="scaleSlider"
                BindingContext="{x:Reference label}"
                Grid.Row="1" Grid.Column="0"
                Maximum="10"
                Value="{Binding Scale, Mode=TwoWay}" />

        <Label BindingContext="{x:Reference scaleSlider}"
               Text="{Binding Value, StringFormat='Scale = {0:F1}'}"
               Grid.Row="1" Grid.Column="1"
               VerticalTextAlignment="Center" />

        <!-- Slider and identifying Label for Rotation -->
        <Slider x:Name="rotationSlider"
                BindingContext="{x:Reference label}"
                Grid.Row="2" Grid.Column="0"
                Maximum="360"
                Value="{Binding Rotation, Mode=OneWayToSource}" />

        <Label BindingContext="{x:Reference rotationSlider}"
               Text="{Binding Value, StringFormat='Rotation = {0:F0}'}"
               Grid.Row="2" Grid.Column="1"
               VerticalTextAlignment="Center" />

        <!-- Slider and identifying Label for RotationX -->
        <Slider x:Name="rotationXSlider"
                BindingContext="{x:Reference label}"
                Grid.Row="3" Grid.Column="0"
                Maximum="360"
                Value="{Binding RotationX, Mode=OneWayToSource}" />

        <Label BindingContext="{x:Reference rotationXSlider}"
               Text="{Binding Value, StringFormat='RotationX = {0:F0}'}"
               Grid.Row="3" Grid.Column="1"
               VerticalTextAlignment="Center" />

        <!-- Slider and identifying Label for RotationY -->
        <Slider x:Name="rotationYSlider"
                BindingContext="{x:Reference label}"
                Grid.Row="4" Grid.Column="0"
                Maximum="360"
                Value="{Binding RotationY, Mode=OneWayToSource}" />

        <Label BindingContext="{x:Reference rotationYSlider}"
               Text="{Binding Value, StringFormat='RotationY = {0:F0}'}"
               Grid.Row="4" Grid.Column="1"
               VerticalTextAlignment="Center" />
    </Grid>
</ContentPage>
```

对其中三个绑定`Slider`视图是`OneWayToSource`，这意味着，`Slider`值的属性中将导致更改其`BindingContext`，即`Label`名为`label`。 这三个`Slider`视图会改变`Rotate`， `RotateX`，和`RotateY`的属性`Label`。

但是，对于绑定`Scale`属性是`TwoWay`。 这是因为`Scale`属性具有默认值为 1，并使用`TwoWay`绑定的原因`Slider`初始值设置为 1 而不是 0。 如果该绑定`OneWayToSource`，则`Scale`属性将最初设置为从 0`Slider`默认值。 `Label`会显示，并向用户可能导致一些混淆。

 [![](data-binding-basics-images/slidertransforms.png "向后绑定")](data-binding-basics-images/slidertransforms-large.png#lightbox "向后的绑定")

 > [!NOTE]
 > [ `VisualElement` ](xref:Xamarin.Forms.VisualElement)类还具有[ `ScaleX` ](xref:Xamarin.Forms.VisualElement.ScaleX)并[ `ScaleY` ](xref:Xamarin.Forms.VisualElement.ScaleY)属性，缩放`VisualElement`x 轴和 y 轴上分别。

## <a name="bindings-and-collections"></a>绑定和集合

执行任何操作演示如何将优于模板化的 XAML 和数据绑定功能`ListView`。

`ListView` 定义`ItemsSource`类型的属性`IEnumerable`，和它在该集合中显示的项。 这些项可以是任何类型的对象。 默认情况下`ListView`使用`ToString`方法的每个项来显示该项。 有时这是正是您所希望，但在许多情况下，`ToString`返回对象的完全限定的类名称。

但是中的项`ListView`集合可以显示希望使用的方式*模板*，其中包括派生的类`Cell`。 在模板中每个项目都会被克隆`ListView`，并在模板已设置的数据绑定被传输到单个的克隆。

通常，你将想要创建使用这些项的自定义单元格`ViewCell`类。 此过程是太好理解在代码中，但在 XAML 中变得非常简单。

XamlSamples 中包含的项目是一个名为类`NamedColor`。 每个`NamedColor`对象具有`Name`并`FriendlyName`类型的属性`string`，和一个`Color`类型的属性`Color`。 此外，`NamedColor`具有 141 静态只读字段的类型`Color`对应于 Xamarin.Forms 中定义的颜色`Color`类。 静态构造函数创建`IEnumerable<NamedColor>`集合，其中包含`NamedColor`对象对应于这些静态字段，并将其分配给它的公共静态`All`属性。

设置静态`NamedColor.All`属性设置为`ItemsSource`的`ListView`轻松使用`x:Static`标记扩展：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:XamlSamples;assembly=XamlSamples"
             x:Class="XamlSamples.ListViewDemoPage"
             Title="ListView Demo Page">

    <ListView ItemsSource="{x:Static local:NamedColor.All}" />

</ContentPage>
```

结果显示建立的类型的真正项`XamlSamples.NamedColor`:

[![](data-binding-basics-images/listview1.png "绑定到集合")](data-binding-basics-images/listview1-large.png#lightbox "绑定到集合")

它不是太多信息，但`ListView`是可滚动和可选择。

若要定义的项模板，您需要跳出`ItemTemplate`属性设置为属性元素中，将其设置为`DataTemplate`，然后引用`ViewCell`。 向`View`属性的`ViewCell`可以定义一个或多个视图以显示每个项的布局。 下面是一个简单的示例：

```xaml
<ListView ItemsSource="{x:Static local:NamedColor.All}">
    <ListView.ItemTemplate>
        <DataTemplate>
            <ViewCell>
                <ViewCell.View>
                    <Label Text="{Binding FriendlyName}" />
                </ViewCell.View>
            </ViewCell>
        </DataTemplate>
    </ListView.ItemTemplate>
</ListView>
```

`Label`元素设置为`View`属性的`ViewCell`。 (`ViewCell.View`不需要标记，因为`View`属性是 content 属性`ViewCell`。)此标记将显示`FriendlyName`每个属性`NamedColor`对象：

[![](data-binding-basics-images/listview2.png "绑定到集合使用 DataTemplate")](data-binding-basics-images/listview2-large.png#lightbox "绑定到使用 DataTemplate 集合")

好多了。 现在需要的是项模板的详细信息和实际颜色条理。 若要支持此模板，某些值的对象定义和页面的资源字典中：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:XamlSamples"
             x:Class="XamlSamples.ListViewDemoPage"
             Title="ListView Demo Page">

    <ContentPage.Resources>
        <ResourceDictionary>
            <OnPlatform x:Key="boxSize"
                        x:TypeArguments="x:Double">
                <On Platform="iOS, Android, UWP" Value="50" />
            </OnPlatform>

            <OnPlatform x:Key="rowHeight"
                        x:TypeArguments="x:Int32">
                <On Platform="iOS, Android, UWP" Value="60" />
            </OnPlatform>

            <local:DoubleToIntConverter x:Key="intConverter" />

        </ResourceDictionary>
    </ContentPage.Resources>

    <ListView ItemsSource="{x:Static local:NamedColor.All}"
              RowHeight="{StaticResource rowHeight}">
        <ListView.ItemTemplate>
            <DataTemplate>
                <ViewCell>
                    <StackLayout Padding="5, 5, 0, 5"
                                 Orientation="Horizontal"
                                 Spacing="15">

                        <BoxView WidthRequest="{StaticResource boxSize}"
                                 HeightRequest="{StaticResource boxSize}"
                                 Color="{Binding Color}" />

                        <StackLayout Padding="5, 0, 0, 0"
                                     VerticalOptions="Center">

                            <Label Text="{Binding FriendlyName}"
                                   FontAttributes="Bold"
                                   FontSize="Medium" />

                            <StackLayout Orientation="Horizontal"
                                         Spacing="0">
                                <Label Text="{Binding Color.R,
                                       Converter={StaticResource intConverter},
                                       ConverterParameter=255,
                                       StringFormat='R={0:X2}'}" />

                                <Label Text="{Binding Color.G,
                                       Converter={StaticResource intConverter},
                                       ConverterParameter=255,
                                       StringFormat=', G={0:X2}'}" />

                                <Label Text="{Binding Color.B,
                                       Converter={StaticResource intConverter},
                                       ConverterParameter=255,
                                       StringFormat=', B={0:X2}'}" />
                            </StackLayout>
                        </StackLayout>
                    </StackLayout>
                </ViewCell>
            </DataTemplate>
        </ListView.ItemTemplate>
    </ListView>
</ContentPage>
```

请注意，使用`OnPlatform`来定义的大小`BoxView`和的高度`ListView`行。 尽管所有平台的值是相同的标记可能容易就可以调整为其他值，以微调显示。

## <a name="binding-value-converters"></a>绑定值转换器

以前**ListView 演示**XAML 文件显示各个`R`， `G`，并`B`Xamarin.Forms 属性`Color`结构。 这些属性属于类型`double`和 0 到 1 范围。 如果你想要显示的十六进制值，不能只使用`StringFormat`与格式规范"X2"。 仅用于范围内的整数，而，除了`double`需要乘以 255 的值。

这个小问题已解决了与*值转换器*，也称为*绑定转换器*。 这是一个类实现`IValueConverter`接口，这意味着它具有两个方法名为`Convert`和`ConvertBack`。 `Convert`值从源传输到目标; 当调用方法`ConvertBack`方法调用进行传输从目标到源中`OneWayToSource`或`TwoWay`绑定：

```csharp
using System;
using System.Globalization;
using Xamarin.Forms;

namespace XamlSamples
{
    class DoubleToIntConverter : IValueConverter
    {
        public object Convert(object value, Type targetType,
                              object parameter, CultureInfo culture)
        {
            double multiplier;

            if (!Double.TryParse(parameter as string, out multiplier))
                multiplier = 1;

            return (int)Math.Round(multiplier * (double)value);
        }

        public object ConvertBack(object value, Type targetType,
                                  object parameter, CultureInfo culture)
        {
            double divider;

            if (!Double.TryParse(parameter as string, out divider))
                divider = 1;

            return ((double)(int)value) / divider;
        }
    }
}
```

`ConvertBack`因为绑定仅一种方法从源到目标方法无法在此程序中播放一个角色。

绑定引用绑定转换器，且有`Converter`属性。 绑定转换器还可以接受使用指定的参数`ConverterParameter`属性。 对于一些更加通用，这是如何指定乘数。 绑定转换器检查是否存在有效的转换器参数`double`值。

转换器是实例化资源字典中，因此它可以在多个绑定之间共享：

```xaml
<local:DoubleToIntConverter x:Key="intConverter" />
```

三个数据绑定引用此单一实例。 请注意，`Binding`标记扩展包含一个嵌入`StaticResource`标记扩展：

```xaml
<Label Text="{Binding Color.R,
                      Converter={StaticResource intConverter},
                      ConverterParameter=255,
                      StringFormat='R={0:X2}'}" />
```

下面是结果：

[![](data-binding-basics-images/listview3.png "绑定到具有 DataTemplate 和转换器的集合")](data-binding-basics-images/listview3-large.png#lightbox "绑定到具有 DataTemplate 和转换器的集合")

`ListView`中处理动态如果执行某些步骤在基础数据，但是仅限中可能出现的更改非常完善。 如果项的集合分配给`ItemsSource`的属性`ListView`在运行时更改 — 的是，如果可以将项添加到或从集合中移除 — 使用`ObservableCollection`这些项的类。 `ObservableCollection` 实现`INotifyCollectionChanged`接口，并`ListView`将安装的处理程序`CollectionChanged`事件。

如果在运行时，更改项目本身的属性，则集合中的项应实现`INotifyPropertyChanged`对使用的属性值的接口和信号更改`PropertyChanged`事件。 在本系列的下一部分对此进行了演示[第 5 部分。从数据绑定到 MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)。

## <a name="summary"></a>总结

数据绑定提供了用于链接在页面内的两个对象之间或视觉对象之间的属性和基础数据的强大机制。 但在应用程序开始使用数据源时, 常用的应用程序体系结构模式开始显现出来，它作为一种有用的模式。 中对此进行了[第 5 部分。从数据绑定到 MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)。



## <a name="related-links"></a>相关链接

- [XamlSamples](https://developer.xamarin.com/samples/xamarin-forms/XamlSamples/)
- [第 1 部分：开始使用 XAML （示例）](~/xamarin-forms/xaml/xaml-basics/get-started-with-xaml.md)
- [第 2 部分：基本 XAML 语法 （示例）](~/xamarin-forms/xaml/xaml-basics/essential-xaml-syntax.md)
- [第 3 部分：XAML 标记扩展 （示例）](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [第 5 部分：从数据绑定到 MVVM （示例）](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)
