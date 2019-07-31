---
title: Xamarin.Forms TimePicker
description: TimePicker 是一个 Xamarin 窗体视图, 它允许用户选择时间。 本文介绍如何使用 Xamarin.Forms 应用程序中的 TimePicker。
ms.prod: xamarin
ms.assetid: 2E99FB23-B82D-4EB4-AFB3-5002E736E7B2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/16/2018
ms.openlocfilehash: d5c4cc6600c8192718257abf4ef1cbec49c12eee
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2019
ms.locfileid: "68656489"
---
# <a name="xamarinforms-timepicker"></a>Xamarin.Forms TimePicker

[![下载示例](~/media/shared/download.png)下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-timepicker)

_允许用户选择的时间，一个 Xamarin.Forms 视图。_

Xamarin.Forms [ `TimePicker` ](xref:Xamarin.Forms.TimePicker)调用平台的时间选取器控件，并允许用户选择的时间。 `TimePicker` 定义以下属性：

- [`Time`](xref:Xamarin.Forms.TimePicker.Time) 类型的`TimeSpan`，默认为在选定时间`TimeSpan`为 0。 `TimeSpan`类型指示持续时间为自午夜以来的时间。
- [`Format`](xref:Xamarin.Forms.TimePicker.Format) 类型的`string`、 一个[标准](/dotnet/standard/base-types/standard-date-and-time-format-strings/)或[自定义](/dotnet/standard/base-types/custom-date-and-time-format-strings/).NET 格式设置字符串，它默认为"t"，短时间模式。
- [`TextColor`](xref:Xamarin.Forms.TimePicker.TextColor) 类型的[ `Color` ](xref:Xamarin.Forms.Color)，用于显示所选的时间，默认为颜色[ `Color.Default` ](xref:Xamarin.Forms.Color.Default)。
- [`FontAttributes`](xref:Xamarin.Forms.TimePicker.FontAttributes) 类型的[ `FontAttributes` ](xref:Xamarin.Forms.FontAttributes)，其默认值为[ `FontAtributes.None` ](xref:Xamarin.Forms.FontAttributes.None)。
- [`FontFamily`](xref:Xamarin.Forms.TimePicker.FontFamily) 类型的`string`，其默认值为`null`。
- [`FontSize`](xref:Xamarin.Forms.TimePicker.FontSize) 类型的`double`，其默认值为-1.0。

所有这些属性受到[ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty)对象，这意味着它们可以设置的样式，这些属性可以是数据绑定的目标。 [ `Time` ](xref:Xamarin.Forms.TimePicker.Time)属性设置了默认绑定模式[ `BindingMode.TwoWay` ](xref:Xamarin.Forms.BindingMode.TwoWay)，这意味着它可以是数据绑定中使用的应用程序的目标[模型-视图-视图模型 (MVVM)](~/xamarin-forms/enterprise-application-patterns/mvvm.md)体系结构。

[ `TimePicker` ](xref:Xamarin.Forms.TimePicker)不包括一个事件来指示一个新的所选[ `Time` ](xref:Xamarin.Forms.TimePicker.Time)值。 如果你需要收到此通知，则可以添加的处理程序[ `PropertyChanged` ](xref:Xamarin.Forms.BindableObject.PropertyChanged)事件。

## <a name="initializing-the-time-property"></a>正在初始化时间属性

在代码中，您可以初始化[ `Time` ](xref:Xamarin.Forms.TimePicker.Time)属性的值类型`TimeSpan`:

```csharp
TimePicker timePicker = new TimePicker
{
  Time = new TimeSpan(4, 15, 26) // Time set to "04:15:26"
};
```

当[ `Time` ](xref:Xamarin.Forms.TimePicker.Time)属性指定在 XAML 中，将值转换为`TimeSpan`和验证以确保的毫秒数大于或等于 0，并且的小时数是小于 24。 时间组件应该用冒号分隔：

```xaml
<TimePicker Time="4:15:26" />
```

如果[ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext)的属性[ `TimePicker` ](xref:Xamarin.Forms.TimePicker)设置为包含属性的类型的 ViewModel 实例`TimeSpan`名为`SelectedTime`（例如），您可以实例化`TimePicker`如下所示：

```xaml
<TimePicker Time="{Binding SelectedTime}" />
```

在此示例中， [ `Time` ](xref:Xamarin.Forms.TimePicker.Time)属性初始化为`SelectedTime`ViewModel 中的属性。 因为`Time`属性设置了绑定模式[ `TwoWay` ](xref:Xamarin.Forms.BindingMode.TwoWay)、 用户选择自动传播到 ViewModel 任何新的时间。

如果[ `TimePicker` ](xref:Xamarin.Forms.TimePicker)不包含一个绑定上其[ `Time` ](xref:Xamarin.Forms.TimePicker.Time)属性，应用程序应附加到一个处理程序[ `PropertyChanged` ](xref:Xamarin.Forms.BindableObject.PropertyChanged)事件当用户选择新的时间，则会收到通知。

有关设置字体属性的信息，请参阅[字体](~/xamarin-forms/user-interface/text/fonts.md)。

## <a name="timepicker-and-layout"></a>TimePicker 和布局

可以使用不受约束的水平布局选项，如`Center`， `Start`，或`End`与[ `TimePicker` ](xref:Xamarin.Forms.TimePicker):

```xaml
<TimePicker ···
            HorizontalOptions="Center"
            ··· />
```

但是，这不是建议在一起。 这取决于设置[ `Format` ](xref:Xamarin.Forms.TimePicker.Format)所选择的属性，时间可能需要不同的显示宽度。 例如，"T"格式字符串会导致[ `TimePicker` ](xref:Xamarin.Forms.TimePicker)长格式显示时间视图和"4:15:26 AM"需要更大的显示宽度比"4:15 AM"的短时间格式 ("t")。 根据平台，这种差异可能会导致`TimePicker`视图，以更改宽度在布局中，或显示被截断。

> [!TIP]
> 最好是使用默认`HorizontalOptions`设置为`Fill`与[ `TimePicker` ](xref:Xamarin.Forms.TimePicker)，而不要使用的宽度`Auto`放置时`TimePicker`中`Grid`单元格。

## <a name="timepicker-in-an-application"></a>应用程序中的 TimePicker

[ **SetTimer** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-timepicker)示例包括[ `TimePicker` ](xref:Xamarin.Forms.TimePicker)， [ `Entry` ](xref:Xamarin.Forms.Entry)，和[ `Switch` ](xref:Xamarin.Forms.Switch)在其页面上的视图。 `TimePicker`可以用来选择时间，以及显示警报对话框，用于提醒用户中的文本时的时间发生`Entry`提供`Switch`上切换。 下面是 XAML 文件：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:SetTimer"
             x:Class="SetTimer.MainPage">
    <StackLayout>
        ...
        <Entry x:Name="_entry"
               Placeholder="Enter event to be reminded of" />
        <Label Text="Select the time below to be reminded at." />
        <TimePicker x:Name="_timePicker"
                    Time="11:00:00"
                    Format="T"
                    PropertyChanged="OnTimePickerPropertyChanged" />
        <StackLayout Orientation="Horizontal">
            <Label Text="Enable timer:" />
            <Switch x:Name="_switch"
                    HorizontalOptions="EndAndExpand"
                    Toggled="OnSwitchToggled" />
        </StackLayout>
    </StackLayout>
</ContentPage>
```

[ `Entry` ](xref:Xamarin.Forms.Entry)允许您输入所选的时间发生时将显示的提示文本。 [ `TimePicker` ](xref:Xamarin.Forms.TimePicker)分配[ `Format` ](xref:Xamarin.Forms.TimePicker.Format) "T"的长时间格式的属性。 它具有事件处理程序附加到[ `PropertyChanged` ](xref:Xamarin.Forms.BindableObject.PropertyChanged)事件，并且[ `Switch` ](xref:Xamarin.Forms.Switch)有一个处理程序附加到其[ `Toggled` ](xref:Xamarin.Forms.Switch.Toggled)事件。 这些事件处理程序是在代码隐藏文件和调用`SetTriggerTime`方法：

```csharp
public partial class MainPage : ContentPage
{
    DateTime _triggerTime;

    public MainPage()
    {
        InitializeComponent();

        Device.StartTimer(TimeSpan.FromSeconds(1), OnTimerTick);
    }

    bool OnTimerTick()
    {
        if (_switch.IsToggled && DateTime.Now >= _triggerTime)
        {
            _switch.IsToggled = false;
            DisplayAlert("Timer Alert", "The '" + _entry.Text + "' timer has elapsed", "OK");
        }
        return true;
    }

    void OnTimePickerPropertyChanged(object sender, PropertyChangedEventArgs args)
    {
        if (args.PropertyName == "Time")
        {
            SetTriggerTime();
        }
    }

    void OnSwitchToggled(object sender, ToggledEventArgs args)
    {
        SetTriggerTime();
    }

    void SetTriggerTime()
    {
        if (_switch.IsToggled)
        {
            _triggerTime = DateTime.Today + _timePicker.Time;
            if (_triggerTime < DateTime.Now)
            {
                _triggerTime += TimeSpan.FromDays(1);
            }
        }
    }
}
```

`SetTriggerTime`方法计算基于计时器时间`DateTime.Today`属性值和`TimeSpan`返回值[ `TimePicker` ](xref:Xamarin.Forms.TimePicker)。 这是必需的因为`DateTime.Today`属性返回`DateTime`，该值指示当前的日期，但午夜的时间。 如果计时器时间已过今天，则它被假定为明天。

每秒执行的计时器刻度`OnTimerTick`方法来检查是否[ `Switch` ](xref:Xamarin.Forms.Switch)是上以及当前时间晚于或等于计时器时间。 当计时器时间发生时， [ `DisplayAlert` ](xref:Xamarin.Forms.Page.DisplayAlert*)方法提供一个警报对话框，提醒用户。

首次运行示例时， [ `TimePicker` ](xref:Xamarin.Forms.TimePicker)到上午 11 初始化视图。 点击`TimePicker`调用平台时间选取器。 平台实现时间选取器很大不同，但是每种方法是熟悉该平台的用户：

[![选择时间](timepicker-images/timepicker-open.png "选择时间")](timepicker-images/timepicker-open-large.png#lightbox "选择时间")

> [!TIP]
> 在 Android 上， [ `TimePicker` ](xref:Xamarin.Forms.TimePicker)对话框可以通过重写自定义`CreateTimePickerDialog`中自定义呈现器的方法。 这样，例如，其他按钮来添加到了对话框。

选择一次之后, 在选定的时间显示在[ `TimePicker` ](xref:Xamarin.Forms.TimePicker):

[![所选时段](timepicker-images/timepicker-selected.png "所选时段")](timepicker-images/timepicker-selected-large.png#lightbox "所选时段")

前提[ `Switch` ](xref:Xamarin.Forms.Switch)切换到打开的位置，应用程序将显示警报对话框提醒用户中的文本[ `Entry` ](xref:Xamarin.Forms.Entry)所选的时间发生时：

[![计时器 Popup](timepicker-images/timer-test.png "计时器弹出")](timepicker-images/timer-test-large.png#lightbox "计时器弹出窗口")

只要显示警报对话框，则[ `Switch` ](xref:Xamarin.Forms.Switch)切换到 off 位置。

## <a name="related-links"></a>相关链接

- [SetTimer 示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-timepicker)
- [TimePicker API](xref:Xamarin.Forms.TimePicker)
