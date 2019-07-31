---
title: Xamarin.Forms 分档器
description: Xamarin.Forms 分档器允许用户从一系列值中选择的数字值。 它包含两个按钮带有负号和加号。 以增量方式将操作的两个按钮更改所选的值。
ms.prod: xamarin
ms.assetid: 62571B3E-D84B-4F52-9FC7-C105D6733B16
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/17/2018
ms.openlocfilehash: 6c89f04b1d1d87fed8d86d50cb68527391a7f317
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2019
ms.locfileid: "68656194"
---
# <a name="xamarinforms-stepper"></a>Xamarin.Forms 分档器

[![下载示例](~/media/shared/download.png)下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-stepperdemos)

_使用分档器从一系列值中选择的数字值。_

Xamarin.Forms [ `Stepper` ](xref:Xamarin.Forms.Stepper)包含带标记的两个按钮的负号和加号。 这些按钮可以由用户以增量方式选择操作`double`从一系列值的值。

[ `Stepper` ](xref:Xamarin.Forms.Stepper)定义类型的四个属性`double`:

- [`Increment`](xref:Xamarin.Forms.Stepper.Increment) 是要更改默认值为 1，通过所选的值的量。
- [`Minimum`](xref:Xamarin.Forms.Stepper.Minimum) 是默认值为 0 的最低要求。
- [`Maximum`](xref:Xamarin.Forms.Stepper.Maximum) 是默认值为 100 的范围内，最大。
- [`Value`](xref:Xamarin.Forms.Stepper.Value) 单步调试器的值，该值可介于`Minimum`和`Maximum`和默认值为 0。

所有这些属性受到[ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty)对象。 [ `Value` ](xref:Xamarin.Forms.Stepper.Value)属性设置了默认绑定模式[ `BindingMode.TwoWay` ](xref:Xamarin.Forms.BindingMode.TwoWay)，这意味着它很适合作为绑定源中使用的应用程序[模型-视图-视图模型 (MVVM)](~/xamarin-forms/enterprise-application-patterns/mvvm.md)体系结构。

> [!WARNING]
> 在内部， [ `Stepper` ](xref:Xamarin.Forms.Stepper)确保[ `Minimum` ](xref:Xamarin.Forms.Stepper.Minimum)是小于[ `Maximum` ](xref:Xamarin.Forms.Stepper.Maximum)。 如果`Minimum`或`Maximum`曾经设置，以便`Minimum`是不小于`Maximum`，引发的异常。 有关详细信息设置`Minimum`并`Maximum`属性，请参阅[预防措施](#precautions)部分。

[ `Stepper` ](xref:Xamarin.Forms.Stepper)将强制[ `Value` ](xref:Xamarin.Forms.Stepper.Value)属性，以防止之间[ `Minimum` ](xref:Xamarin.Forms.Stepper.Minimum)和[ `Maximum` ](xref:Xamarin.Forms.Stepper.Maximum)(含） 之间。 如果`Minimum`属性设置为值大于`Value`属性，`Stepper`设置`Value`属性设置为`Minimum`。 同样，如果`Maximum`设置为值小于`Value`，然后`Stepper`设置`Value`属性设置为`Maximum`。

[`Stepper`](xref:Xamarin.Forms.Stepper) 定义[ `ValueChanged` ](xref:Xamarin.Forms.Stepper.ValueChanged)时引发的事件[ `Value` ](xref:Xamarin.Forms.Stepper.Value)通过用户操作的更改`Stepper`或当应用程序设置`Value`直接属性。 一个`ValueChanged`时，也会激发事件`Value`属性强制转换时上, 一段中所述。

[ `ValueChangedEventArgs` ](xref:Xamarin.Forms.ValueChangedEventArgs)对象，它附带[ `ValueChanged` ](xref:Xamarin.Forms.Stepper.ValueChanged)事件具有两个属性，这两个类型`double`: [ `OldValue` ](xref:Xamarin.Forms.ValueChangedEventArgs.OldValue)和[`NewValue`](xref:Xamarin.Forms.ValueChangedEventArgs.NewValue). 次激发事件时，值`NewValue`等同于[ `Value` ](xref:Xamarin.Forms.Stepper.Value)属性[ `Stepper` ](xref:Xamarin.Forms.Stepper)对象。

## <a name="basic-stepper-code-and-markup"></a>基本分档器代码和标记

[ **StepperDemos** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-stepperdemos)示例包含三个页面是功能上相同的但以不同方式实现。 第一页仅使用C#代码，第二个使用 XAML 使用在代码中和第三个事件处理程序是可以避免使用 XAML 文件中的数据绑定事件处理程序。

### <a name="creating-a-stepper-in-code"></a>在代码中创建分档器

**基本分档器代码**页面[ **StepperDemos** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-stepperdemos)示例演示如何创建[ `Stepper` ](xref:Xamarin.Forms.Stepper)并将两个[`Label` ](xref:Xamarin.Forms.Label)代码中的对象：

```csharp
public class BasicStepperCodePage : ContentPage
{
    public BasicStepperCodePage()
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

        Stepper stepper = new Stepper
        {
            Maximum = 360,
            Increment = 30,
            HorizontalOptions = LayoutOptions.Center
        };
        stepper.ValueChanged += (sender, e) =>
        {
            rotationLabel.Rotation = stepper.Value;
            displayLabel.Text = string.Format("The Stepper value is {0}", e.NewValue);
        };

        Title = "Basic Stepper Code";
        Content = new StackLayout
        {
            Margin = new Thickness(20),
            Children = { rotationLabel, stepper, displayLabel }
        };
    }
}
```

[ `Stepper` ](xref:Xamarin.Forms.Stepper)初始化有[ `Maximum` ](xref:Xamarin.Forms.Stepper.Maximum) 360、 属性和一个[ `Increment` ](xref:Xamarin.Forms.Stepper.Increment)属性为 30。 操作`Stepper`更改所选的值以增量方式之间[ `Minimum` ](xref:Xamarin.Forms.Stepper.Minimum)到`Maximum`值的基础`Increment`属性。 [ `ValueChanged` ](xref:Xamarin.Forms.Stepper.ValueChanged)处理程序`Stepper`使用[ `Value` ](xref:Xamarin.Forms.Stepper.Value)属性`stepper`对象，以设置[ `Rotation` ](xref:Xamarin.Forms.VisualElement.Rotation)属性的第一个[ `Label` ](xref:Xamarin.Forms.Label) ，并使用`string.Format`方法替换`NewValue`属性设置的事件参数[ `Text` ](xref:Xamarin.Forms.Label.Text)属性第二个`Label`。 这两种方法来获取当前值的`Stepper`是可互换的。

下面的屏幕截图演示**基本分档器代码**页：

[![基本分档器代码](stepper-images/basic-stepper-code.png "基本分档器代码")](stepper-images/basic-stepper-code-large.png#lightbox)

第二个[ `Label` ](xref:Xamarin.Forms.Label)直到显示"（未初始化）"的文本[ `Stepper` ](xref:Xamarin.Forms.Stepper)操作，这将导致第一个[ `ValueChanged` ](xref:Xamarin.Forms.Stepper.ValueChanged)事件若要触发。

### <a name="creating-a-stepper-in-xaml"></a>在 XAML 中创建分档器

**基本分档器 XAML**页在功能上与相同**基本分档器代码**但主要是在 XAML 中实现：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="StepperDemo.BasicStepperXAMLPage"
             Title="Basic Stepper XAML">
    <StackLayout Margin="20">
        <Label x:Name="_rotatingLabel"
               Text="ROTATING TEXT"
               FontSize="Large"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />
        <Stepper Maximum="360"
                 Increment="30"
                 HorizontalOptions="Center"
                 ValueChanged="OnStepperValueChanged" />
        <Label x:Name="_displayLabel"
               Text="(uninitialized)"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />        
    </StackLayout>
</ContentPage>
```

代码隐藏文件包含的处理程序[ `ValueChanged` ](xref:Xamarin.Forms.Stepper.ValueChanged)事件：

```csharp
public partial class BasicStepperXAMLPage : ContentPage
{
    public BasicStepperXAMLPage()
    {
        InitializeComponent();
    }

    void OnStepperValueChanged(object sender, ValueChangedEventArgs e)
    {
        double value = e.NewValue;
        _rotatingLabel.Rotation = value;
        _displayLabel.Text = string.Format("The Stepper value is {0}", value);
    }
}
```

还有可能的事件处理程序，以获取[ `Stepper` ](xref:Xamarin.Forms.Stepper) ，在触发该事件通过`sender`参数。 [ `Value` ](xref:Xamarin.Forms.Stepper.Value)属性包含的当前值：

```csharp
double value = ((Stepper)sender).Value;
```

如果[ `Stepper` ](xref:Xamarin.Forms.Stepper)对象提供与 XAML 文件中的名称`x:Name`属性 （例如，"分档器"），则事件处理程序无法直接引用该对象：

```csharp
double value = stepper.Value;
```

### <a name="data-binding-the-stepper"></a>数据绑定分档器

**基本分档器绑定**页显示了如何编写一个几乎等效的应用程序，可以消除[ `Value` ](xref:Xamarin.Forms.Stepper.Value)事件处理程序通过使用[数据绑定](~/xamarin-forms/app-fundamentals/data-binding/index.md):

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="StepperDemo.BasicStepperBindingsPage"
             Title="Basic Stepper Bindings">
    <StackLayout Margin="20">
        <Label Text="ROTATING TEXT"
               Rotation="{Binding Source={x:Reference _stepper}, Path=Value}"
               FontSize="Large"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />
        <Stepper x:Name="_stepper"
                 Maximum="360"
                 Increment="30"
                 HorizontalOptions="Center" />
        <Label Text="{Binding Source={x:Reference _stepper}, Path=Value, StringFormat='The Stepper value is {0:F0}'}"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />
    </StackLayout>
</ContentPage>
```

[ `Rotation` ](xref:Xamarin.Forms.VisualElement.Rotation)的第一个属性[ `Label` ](xref:Xamarin.Forms.Label)绑定到[ `Value` ](xref:Xamarin.Forms.Stepper.Value)属性[ `Stepper` ](xref:Xamarin.Forms.Stepper)、 原样[ `Text` ](xref:Xamarin.Forms.Label.Text)属性的第二个`Label`与`StringFormat`规范。 **基本分档器绑定**页的工作方式与前两页的功能略有不同:第一次显示页面时, 第`Label`二个显示带有值的文本字符串。 这是使用数据绑定的优点。 若要显示不带数据绑定的文本，您需要专门初始化`Text`的属性`Label`或模拟的激发[ `ValueChanged` ](xref:Xamarin.Forms.Stepper.ValueChanged)通过从类构造函数调用的事件处理程序的事件.

## <a name="precautions"></a>预防措施

值[ `Minimum` ](xref:Xamarin.Forms.Stepper.Minimum)属性必须始终为的值小于[ `Maximum` ](xref:Xamarin.Forms.Stepper.Maximum)属性。 以下代码片段会导致[ `Stepper` ](xref:Xamarin.Forms.Stepper)引发异常：

```csharp
// Throws an exception!
Stepper stepper = new Stepper
{
    Minimum = 180,
    Maximum = 360
};
```

C#编译器生成的代码，用于在序列中，设置这两个属性以及何时[ `Minimum` ](xref:Xamarin.Forms.Stepper.Minimum)属性设置为 180，则其值大于默认值[ `Maximum` ](xref:Xamarin.Forms.Stepper.Maximum)值为 100。 您可以通过设置这种情况下避免异常`Maximum`属性第一个：

```csharp
Stepper stepper = new Stepper
{
    Maximum = 360,
    Minimum = 180
};
```

设置[ `Maximum` ](xref:Xamarin.Forms.Stepper.Maximum)到 360 之间并不是问题由于大于默认值[ `Minimum` ](xref:Xamarin.Forms.Stepper.Minimum)值为 0。 当`Minimum`设置，则这不会早于`Maximum`360 的值。

在 XAML 中存在相同的问题。 它可确保订单中设置属性[ `Maximum` ](xref:Xamarin.Forms.Stepper.Maximum)始终是大于`Minimum`:

```xaml
<Stepper Maximum="360"
         Minimum="180" ... />
```

可以设置[ `Minimum` ](xref:Xamarin.Forms.Stepper.Minimum)并[ `Maximum` ](xref:Xamarin.Forms.Stepper.Maximum)值为负数，但仅在订单中其中`Minimum`是始终小于`Maximum`:

```xaml
<Stepper Minimum="-360"
         Maximum="-180" ... />
```

[ `Value` ](xref:Xamarin.Forms.Stepper.Value)属性始终为大于或等于[ `Minimum` ](xref:Xamarin.Forms.Stepper.Minimum)值且小于或等于[ `Maximum` ](xref:Xamarin.Forms.Stepper.Maximum)。 如果`Value`设置为该范围以外的值，该值将被强制为之间的范围内，但不会引发异常。 例如，此代码将*不*引发异常：

```csharp
Stepper stepper = new Stepper
{
    Value = 180
};
```

相反， [ `Value` ](xref:Xamarin.Forms.Stepper.Value)属性强制转换为[ `Maximum` ](xref:Xamarin.Forms.Stepper.Maximum)值为 100。

以下是上面所示的代码片段：

```csharp
Stepper stepper = new Stepper
{
    Maximum = 360,
    Minimum = 180
};
```

当[ `Minimum` ](xref:Xamarin.Forms.Stepper.Minimum)设置为 180，则[ `Value` ](xref:Xamarin.Forms.Stepper.Value)也设置为 180。

如果[ `ValueChanged` ](xref:Xamarin.Forms.Stepper.ValueChanged)事件处理程序已附加时， [ `Value` ](xref:Xamarin.Forms.Stepper.Value)属性强制转换为 0，其默认值以外的内容则`ValueChanged`触发事件。 下面是 XAML 的代码片段：

```xaml
<Stepper ValueChanged="OnStepperValueChanged"
         Maximum="360"
         Minimum="180" />
```

当[ `Minimum` ](xref:Xamarin.Forms.Stepper.Minimum)设置为 180， [ `Value` ](xref:Xamarin.Forms.Stepper.Value)还将设置为 180，和[ `ValueChanged` ](xref:Xamarin.Forms.Stepper.ValueChanged)触发事件。 这可能已构建页的其余部分，并在处理程序可能会尝试以引用尚未创建页面上的其他元素之前。 你可能想要添加一些代码以`ValueChanged`处理程序，它会检查`null`页面上其他元素的值。 或者，可以设置`ValueChanged`事件处理程序之后[ `Stepper` ](xref:Xamarin.Forms.Stepper)尚未初始化的值。

## <a name="related-links"></a>相关链接

- [分档器演示示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-stepperdemos)
- [分档器 API](xref:Xamarin.Forms.Stepper)
