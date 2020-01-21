---
title: Xamarin.Forms 滑块
description: Xamarin.Forms 滑块是可以由用户选择一个双精度值从连续范围操作的水平栏。 本文介绍如何使用滑块类从一系列连续值选择一个值。
ms.prod: xamarin
ms.assetid: 36B1C645-26E0-4874-B6B6-BDBF77662878
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/27/2019
ms.openlocfilehash: ddb25a1f01f91b627fc0c370f7f21e2a797b72cb
ms.sourcegitcommit: 191f1f3b13a14e2afadcb95126c5f653722f126f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/30/2019
ms.locfileid: "75545577"
---
# <a name="xamarinforms-slider"></a>Xamarin.Forms 滑块

[![下载示例](~/media/shared/download.png)下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-sliderdemos)

_使用滑块选择从一系列连续值。_

Xamarin.Forms [ `Slider` ](xref:Xamarin.Forms.Slider)是可以由用户选择操作的水平条`double`从连续范围的值。

`Slider`定义的类型的三个属性`double`:

- [`Minimum`](xref:Xamarin.Forms.Slider.Minimum) 是默认值为 0 的最低要求。
- [`Maximum`](xref:Xamarin.Forms.Slider.Maximum) 是默认值为 1 的最大的范围。
- [`Value`](xref:Xamarin.Forms.Slider.Value) 滑块的值，该值可介于`Minimum`和`Maximum`和默认值为 0。

所有三个属性受`BindableProperty`对象。 `Value`属性设置了默认绑定模式`BindingMode.TwoWay`，这意味着它很适合作为绑定源中使用的应用程序[模型-视图-视图模型 (MVVM)](~/xamarin-forms/enterprise-application-patterns/mvvm.md)体系结构。

> [!WARNING]
> 在内部，`Slider`可确保`Minimum`是小于`Maximum`。 如果`Minimum`或`Maximum`曾经设置，以便`Minimum`是不小于`Maximum`，引发的异常。 请参阅[**预防措施**](#precautions)设置的详细信息部分下方`Minimum`和`Maximum`属性。

`Slider`将强制`Value`属性，以防止之间`Minimum`和`Maximum`(含） 之间。 如果`Minimum`属性设置为值大于`Value`属性，`Slider`设置`Value`属性设置为`Minimum`。 同样，如果`Maximum`设置为值小于`Value`，然后`Slider`设置`Value`属性设置为`Maximum`。

`Slider` 定义[ `ValueChanged` ](xref:Xamarin.Forms.Slider.ValueChanged)时引发的事件`Value`更改，通过用户操作`Slider`或当程序集`Value`直接属性。 一个`ValueChanged`时，也会激发事件`Value`属性强制转换时上, 一段中所述。

[ `ValueChangedEventArgs` ](xref:Xamarin.Forms.ValueChangedEventArgs)对象，它附带`ValueChanged`事件具有两个属性，这两个类型`double`: [ `OldValue` ](xref:Xamarin.Forms.ValueChangedEventArgs.OldValue)并[ `NewValue` ](xref:Xamarin.Forms.ValueChangedEventArgs.NewValue). 次激发事件时，值`NewValue`等同于`Value`属性的`Slider`对象。

`Slider` 还定义在拖动操作开始和结束时激发的 `DragStarted` 和 `DragCompleted` 事件。 与[`ValueChanged`](xref:Xamarin.Forms.Slider.ValueChanged)事件不同，`DragStarted` 和 `DragCompleted` 事件仅通过用户操作 `Slider`进行触发。 当引发 `DragStarted` 事件时，将执行 `ICommand`类型的 `DragStartedCommand`。 同样，在引发 `DragCompleted` 事件时，将执行类型为 `ICommand`的 `DragCompletedCommand`。

> [!WARNING]
> 不使用的不受约束的水平布局选项`Center`， `Start`，或`End`与`Slider`。 在 Android 和 UWP，`Slider`折叠到栏的长度为零，并在 iOS 上，在栏是非常短。 保留默认值`HorizontalOptions`设置为`Fill`，且不使用的宽度`Auto`放置时`Slider`中`Grid`布局。

`Slider`还定义了多个会影响其外观的属性：

- [`MinimumTrackColor`](xref:Xamarin.Forms.Slider.MinimumTrackColorProperty) 是在栏上左侧和右侧的滚动块的颜色。
- [`MaximumTrackColor`](xref:Xamarin.Forms.Slider.MaximumTrackColorProperty) 是条 thumb 右侧的颜色。
- [`ThumbColor`](xref:Xamarin.Forms.Slider.ThumbColorProperty) 为缩略图颜色。
- [`ThumbImageSource`](xref:Xamarin.Forms.Slider.ThumbImageSourceProperty) 是要用于滚动块的类型的图像[ `ImageSource` ](xref:Xamarin.Forms.ImageSource)。

> [!NOTE]
> `ThumbColor`和`ThumbImageSource`属性是互相排斥。 如果设置了这两个属性，`ThumbImageSource`属性将优先。

## <a name="basic-slider-code-and-markup"></a>滑块的基本代码和标记

[ **SliderDemos** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-sliderdemos)示例开头是功能上相同的但以不同方式实现的三个页面。 第一页仅使用 C# 代码、 第二个与事件处理程序在代码中，使用 XAML 和第三个是可以避免使用 XAML 文件中的数据绑定事件处理程序。

### <a name="creating-a-slider-in-code"></a>在代码中创建一个滑块

**滑块的基本代码**页面[ **SliderDemos** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-sliderdemos)示例演示显示创建`Slider`并将两个`Label`代码中的对象：

```csharp
public class BasicSliderCodePage : ContentPage
{
    public BasicSliderCodePage()
    {
        Label rotationLabel = new Label
        {
            Text = "ROTATING TEXT",
            FontSize = Device.GetNamedSize(NamedSize.Large, typeof(Label)),
            HorizontalOptions = LayoutOptions.Center,
            VerticalOptions = LayoutOptions.CenterAndExpand
        };

        Label displayLabel = new Label
        {
            Text = "(uninitialized)",
            HorizontalOptions = LayoutOptions.Center,
            VerticalOptions = LayoutOptions.CenterAndExpand
        };

        Slider slider = new Slider
        {
            Maximum = 360
        };
        slider.ValueChanged += (sender, args) =>
        {
            rotationLabel.Rotation = slider.Value;
            displayLabel.Text = String.Format("The Slider value is {0}", args.NewValue);
        };

        Title = "Basic Slider Code";
        Padding = new Thickness(10, 0);
        Content = new StackLayout
        {
            Children =
            {
                rotationLabel,
                slider,
                displayLabel
            }
        };
    }
}
```

`Slider`初始化为具有`Maximum`360 属性。 `ValueChanged`处理程序`Slider`使用`Value`的属性`slider`对象，以设置`Rotation`属性的第一个`Label`，并使用`String.Format`方法替换`NewValue`属性若要设置的事件参数`Text`属性的第二个`Label`。 这两种方法来获取当前值的`Slider`是可互换的。

下面是在 iOS 和 Android 设备上运行的程序：

[![基本滑块代码](slider-images/BasicSliderCode.png "基本滑块代码")](slider-images/BasicSliderCode-Large.png#lightbox)

第二个`Label`直到显示"（未初始化）"的文本`Slider`操作，这将导致第一个`ValueChanged`事件被触发。 请注意，为每个平台不同的显示的小数位数。 这些差异的平台的实现与相关`Slider`和更高版本中的部分中的这篇文章讨论[平台实现差异](#implementations)。

### <a name="creating-a-slider-in-xaml"></a>在 XAML 中创建一个滑块

**基本滑块 XAML**页在功能上与相同**滑块的基本代码**但主要是在 XAML 中实现：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="SliderDemos.BasicSliderXamlPage"
             Title="Basic Slider XAML"
             Padding="10, 0">
    <StackLayout>
        <Label x:Name="rotatingLabel"
               Text="ROTATING TEXT"
               FontSize="Large"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />

        <Slider Maximum="360"
                ValueChanged="OnSliderValueChanged" />

        <Label x:Name="displayLabel"
               Text="(uninitialized)"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />
    </StackLayout>
</ContentPage>
```

代码隐藏文件包含的处理程序`ValueChanged`事件：

```csharp
public partial class BasicSliderXamlPage : ContentPage
{
    public BasicSliderXamlPage()
    {
        InitializeComponent();
    }

    void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
    {
        double value = args.NewValue;
        rotatingLabel.Rotation = value;
        displayLabel.Text = String.Format("The Slider value is {0}", value);
    }
}
```

还有可能的事件处理程序，以获取`Slider`，在触发该事件通过`sender`参数。 `Value`属性包含的当前值：

```csharp
double value = ((Slider)sender).Value;
```

如果`Slider`对象提供与 XAML 文件中的名称`x:Name`属性 （例如，"滑块"），则事件处理程序无法直接引用该对象：

```csharp
double value = slider.Value;
```

### <a name="data-binding-the-slider"></a>数据绑定滑块

**基本滑块绑定**页显示了如何编写一个几乎等效程序，可以消除`Value`事件处理程序通过使用[数据绑定](~/xamarin-forms/app-fundamentals/data-binding/index.md):

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="SliderDemos.BasicSliderBindingsPage"
             Title="Basic Slider Bindings"
             Padding="10, 0">
    <StackLayout>
        <Label Text="ROTATING TEXT"
               Rotation="{Binding Source={x:Reference slider},
                                  Path=Value}"
               FontSize="Large"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />

        <Slider x:Name="slider"
                Maximum="360" />

        <Label x:Name="displayLabel"
               Text="{Binding Source={x:Reference slider},
                              Path=Value,
                              StringFormat='The Slider value is {0:F0}'}"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />
    </StackLayout>
</ContentPage>
```

`Rotation`属性的第一个`Label`绑定到`Value`的属性`Slider`，如是`Text`第二个属性`Label`与`StringFormat`规范。 基本的 "**滑块绑定**" 页的工作方式与前两个页面的工作方式略有不同：第一次显示页面时，第二 `Label` 显示带有值的文本字符串。 这是使用数据绑定的优点。 若要显示不带数据绑定的文本，您需要专门初始化`Text`的属性`Label`或模拟的激发`ValueChanged`通过从类构造函数调用的事件处理程序的事件。

<a name="precautions" />

## <a name="precautions"></a>预防措施

值`Minimum`属性必须始终为的值小于`Maximum`属性。 以下代码片段会导致`Slider`引发异常：

```csharp
// Throws an exception!
Slider slider = new Slider
{
    Minimum = 10,
    Maximum = 20
};
```

C# 编译器将生成在序列中，设置这两个属性的代码以及何时`Minimum`属性设置为 10，其值大于默认`Maximum`值为 1。 您可以通过设置这种情况下避免异常`Maximum`属性第一个：

```csharp
Slider slider = new Slider
{
    Maximum = 20,
    Minimum = 10
};
```

设置`Maximum`20 到不是问题大于默认值是`Minimum`值为 0。 当`Minimum`设置，则这不会早于`Maximum`值为 20。

在 XAML 中存在相同的问题。 它可确保订单中设置属性`Maximum`始终是大于`Minimum`:

```xaml
<Slider Maximum="20"
        Minimum="10" ... />
```

可以设置`Minimum`并`Maximum`值为负数，但仅在订单中其中`Minimum`是始终小于`Maximum`:

```xaml
<Slider Minimum="-20"
        Maximum="-10" ... />
```

`Value`属性始终为大于或等于`Minimum`值且小于或等于`Maximum`。 如果`Value`设置为该范围以外的值，该值将被强制为之间的范围内，但不会引发异常。 例如，此代码将*不*引发异常：

```csharp
Slider slider = new Slider
{
    Value = 10
};
```

相反，`Value`属性强制转换为`Maximum`值为 1。

以下是上面所示的代码片段：

```csharp
Slider slider = new Slider
{
    Maximum = 20,
    Minimum = 10
};
```

当`Minimum`设置为 10，则`Value`也设置为 10。

如果`ValueChanged`事件处理程序已附加时，`Value`属性强制转换为 0，其默认值以外的内容则`ValueChanged`触发事件。 下面是 XAML 的代码片段：

```xaml
<Slider ValueChanged="OnSliderValueChanged"
        Maximum="20"
        Minimum="10" />
```

当`Minimum`设置为 10，`Value`也设置为 10，和`ValueChanged`触发事件。 这可能已构建页的其余部分，并在处理程序可能会尝试以引用尚未创建页面上的其他元素之前。 你可能想要添加一些代码以`ValueChanged`处理程序，它会检查`null`页面上其他元素的值。 或者，可以设置`ValueChanged`之后的事件处理程序`Slider`尚未初始化的值。

<a name="implementations" />

## <a name="platform-implementation-differences"></a>平台实现差异

前面所示的屏幕截图显示的值`Slider`具有不同数量的小数点。 这与如何`Slider`在 Android 和 UWP 平台上实现。

### <a name="the-android-implementation"></a>Android 实现

Android 的实现`Slider`在 Android 上基于[ `SeekBar` ](xref:Android.Widget.SeekBar) ，并始终设置[ `Max` ](xref:Android.Widget.ProgressBar.Max)属性设置为 1000年。 这意味着，`Slider`在 Android 上具有仅另外 1001 离散值。 如果您设置`Slider`能够`Minimum`为 0 和一个`Maximum`5000，则为`Slider`操作，`Value`属性具有值 0、 5、 10、 15 和等。

### <a name="the-uwp-implementation"></a>UWP 实现

UWP 实现`Slider`基于 UWP [ `Slider` ](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.slider)控件。 `StepFrequency`属性的 UWP`Slider`设置为的差异`Maximum`和`Minimum`属性除以 10，但不能大于 1。

例如，对于默认范围为 0 到 1`StepFrequency`属性设置为 0.1。 作为`Slider`操作，`Value`属性被限制到 0，0.1、 0.2、 0.3、 0.4、 0.5、 0.6，0.7，0.8、 0.9，且 1.0。 (这是中的最后一页中显而易见[ **SliderDemos** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-sliderdemos)示例。)时之间的差异`Maximum`并`Minimum`属性为 10 或更高，则`StepFrequency`设置为 1，和`Value`属性具有整数值。

### <a name="the-stepslider-solution"></a>StepSlider 解决方案

更灵活`StepSlider`中所述[第 27 章。自定义呈现器](https://xamarin.azureedge.net/developer/xamarin-forms-book/XamarinFormsBook-Ch27-Apr2016.pdf)一书*使用 Xamarin.Forms 创建移动应用*。 `StepSlider`类似于`Slider`，但添加`Steps`属性指定值之间的数`Minimum`和`Maximum`。

## <a name="sliders-for-color-selection"></a>滑块的颜色选择

中的两个页面最终[ **SliderDemos** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-sliderdemos)示例都使用三个`Slider`颜色选择的实例。 第一页处理代码隐藏文件中的所有交互，而第二页显示了如何使用 ViewModel 数据绑定。

### <a name="handling-sliders-in-the-code-behind-file"></a>处理代码隐藏文件中的滑块

**RGB 颜色滑块**页上实例化`BoxView`以显示一种颜色，三个`Slider`实例选择的颜色和三个红色、 绿色和蓝色组件`Label`用于显示这些颜色的元素值：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="SliderDemos.RgbColorSlidersPage"
             Title="RGB Color Sliders">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style TargetType="Slider">
                <Setter Property="Maximum" Value="255" />
            </Style>

            <Style TargetType="Label">
                <Setter Property="HorizontalTextAlignment" Value="Center" />
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>

    <StackLayout Margin="10">
        <BoxView x:Name="boxView"
                 Color="Black"
                 VerticalOptions="FillAndExpand" />

        <Slider x:Name="redSlider"
                ValueChanged="OnSliderValueChanged" />

        <Label x:Name="redLabel" />

        <Slider x:Name="greenSlider"
                ValueChanged="OnSliderValueChanged" />

        <Label x:Name="greenLabel" />

        <Slider x:Name="blueSlider"
                ValueChanged="OnSliderValueChanged" />

        <Label x:Name="blueLabel" />
    </StackLayout>
</ContentPage>
```

一个`Style`提供了所有这三个`Slider`一个 0 到 255 的范围的元素。 `Slider`元素共享相同`ValueChanged`处理程序，这在代码隐藏文件中实现：

```csharp
public partial class RgbColorSlidersPage : ContentPage
{
    public RgbColorSlidersPage()
    {
        InitializeComponent();
    }

    void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
    {
        if (sender == redSlider)
        {
            redLabel.Text = String.Format("Red = {0:X2}", (int)args.NewValue);
        }
        else if (sender == greenSlider)
        {
            greenLabel.Text = String.Format("Green = {0:X2}", (int)args.NewValue);
        }
        else if (sender == blueSlider)
        {
            blueLabel.Text = String.Format("Blue = {0:X2}", (int)args.NewValue);
        }

        boxView.Color = Color.FromRgb((int)redSlider.Value,
                                      (int)greenSlider.Value,
                                      (int)blueSlider.Value);
    }
}
```

第一个部分集`Text`之一的属性`Label`为短文本字符串的值，该值指示实例`Slider`以十六进制格式。 然后，所有这三个`Slider`访问实例创建`Color`从 RGB 组件的值：

[![RGB 颜色滑块](slider-images/RgbColorSliders.png "RGB 颜色滑块")](slider-images/RgbColorSliders-Large.png#lightbox)

### <a name="binding-the-slider-to-a-viewmodel"></a>绑定到 ViewModel 的滑块

**HSL 颜色滑块**页显示了如何使用 ViewModel 来执行用于创建计算`Color`色调、 饱和度和亮度值中的值。 像所有 Viewmodel`HSLColorViewModel`类实现`INotifyPropertyChanged`接口，并且将触发`PropertyChanged`事件的一个属性发生更改时：

```csharp
public class HslColorViewModel : INotifyPropertyChanged
{
    Color color;

    public event PropertyChangedEventHandler PropertyChanged;

    public double Hue
    {
        set
        {
            if (color.Hue != value)
            {
                Color = Color.FromHsla(value, color.Saturation, color.Luminosity);
            }
        }
        get
        {
            return color.Hue;
        }
    }

    public double Saturation
    {
        set
        {
            if (color.Saturation != value)
            {
                Color = Color.FromHsla(color.Hue, value, color.Luminosity);
            }
        }
        get
        {
            return color.Saturation;
        }
    }

    public double Luminosity
    {
        set
        {
            if (color.Luminosity != value)
            {
                Color = Color.FromHsla(color.Hue, color.Saturation, value);
            }
        }
        get
        {
            return color.Luminosity;
        }
    }

    public Color Color
    {
        set
        {
            if (color != value)
            {
                color = value;
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs("Hue"));
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs("Saturation"));
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs("Luminosity"));
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs("Color"));
            }
        }
        get
        {
            return color;
        }
    }
}
```

Viewmodel 并`INotifyPropertyChanged`一文中讨论了接口[数据绑定](~/xamarin-forms/app-fundamentals/data-binding/index.md)。

**HslColorSlidersPage.xaml**文件实例化`HslColorViewModel`并将其设置为页面的`BindingContext`属性。 这允许要绑定到 ViewModel 中属性的 XAML 文件中的所有元素：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:SliderDemos"
             x:Class="SliderDemos.HslColorSlidersPage"
             Title="HSL Color Sliders">

    <ContentPage.BindingContext>
        <local:HslColorViewModel Color="Chocolate" />
    </ContentPage.BindingContext>

    <ContentPage.Resources>
        <ResourceDictionary>
            <Style TargetType="Label">
                <Setter Property="HorizontalTextAlignment" Value="Center" />
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>

    <StackLayout Margin="10">
        <BoxView Color="{Binding Color}"
                 VerticalOptions="FillAndExpand" />

        <Slider Value="{Binding Hue}" />
        <Label Text="{Binding Hue, StringFormat='Hue = {0:F2}'}" />

        <Slider Value="{Binding Saturation}" />
        <Label Text="{Binding Saturation, StringFormat='Saturation = {0:F2}'}" />

        <Slider Value="{Binding Luminosity}" />
        <Label Text="{Binding Luminosity, StringFormat='Luminosity = {0:F2}'}" />
    </StackLayout>
</ContentPage>
```

作为`Slider`操作元素，`BoxView`和`Label`元素更新从 ViewModel:

[![HSL 颜色滑块](slider-images/HslColorSliders.png "HSL 颜色滑块")](slider-images/HslColorSliders-Large.png#lightbox)

`StringFormat`组件的`Binding`标记扩展设置为"F2"的格式以显示两个小数位。 (在文章中讨论数据绑定中的格式设置字符串[字符串格式设置](~/xamarin-forms/app-fundamentals/data-binding/string-formatting.md)。)但是，UWP 版本的程序仅限于值为 0、 0.1、 0.2...0.9 以及 1.0。 这是 UWP 的实现的直接结果`Slider`上文所述的部分中[平台实现差异](#implementations)。

## <a name="related-links"></a>相关链接

- [滑块演示示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-sliderdemos)
- [滑块 API](xref:Xamarin.Forms.Slider)
