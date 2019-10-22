---
title: Xamarin. Forms TimePicker
description: TimePicker 是一个 Xamarin 窗体视图，它允许用户选择时间。 本文介绍如何在 Xamarin. Forms 应用程序中使用 TimePicker。
ms.prod: xamarin
ms.assetid: 2E99FB23-B82D-4EB4-AFB3-5002E736E7B2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/16/2018
ms.openlocfilehash: aae0791199b0e3042a3c619fcb11e7b877f52012
ms.sourcegitcommit: 21d8be9571a2fa89fb7d8ff0787ff4f957de0985
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/21/2019
ms.locfileid: "72695901"
---
# <a name="xamarinforms-timepicker"></a>Xamarin. Forms TimePicker

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-timepicker)

_允许用户选择时间的 Xamarin 窗体视图。_

Xamarin [`TimePicker`](xref:Xamarin.Forms.TimePicker)调用平台的时间选取器控件，并允许用户选择时间。 `TimePicker` 定义以下属性：

- 类型 `TimeSpan` [`Time`](xref:Xamarin.Forms.TimePicker.Time) ，所选时间默认为 `TimeSpan` 0。 @No__t_0 类型指示自午夜后的持续时间。
- `string` 类型的[`Format`](xref:Xamarin.Forms.TimePicker.Format) ，这是一个[标准](/dotnet/standard/base-types/standard-date-and-time-format-strings/)或[自定义](/dotnet/standard/base-types/custom-date-and-time-format-strings/)的 .net 格式设置字符串，该字符串默认为 "t"，这是短时间模式。
- [`Color`](xref:Xamarin.Forms.Color)类型的[`TextColor`](xref:Xamarin.Forms.TimePicker.TextColor) ，用于显示选定时间的颜色（默认为[`Color.Default`](xref:Xamarin.Forms.Color.Default)）。
- [`FontAttributes`](xref:Xamarin.Forms.FontAttributes)类型的[`FontAttributes`](xref:Xamarin.Forms.TimePicker.FontAttributes) ，默认为[`FontAtributes.None`](xref:Xamarin.Forms.FontAttributes.None)。
- `string` 类型的[`FontFamily`](xref:Xamarin.Forms.TimePicker.FontFamily) ，默认为 `null`。
- `double` 类型的[`FontSize`](xref:Xamarin.Forms.TimePicker.FontSize) ，默认值为-1.0。
- `CharacterSpacing` 类型 `double`，是 `TimePicker` 文本的字符之间的间距。

所有这些属性都是由[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)对象支持的，这意味着它们可以采用样式，属性可以是数据绑定的目标。 [@No__t_1](xref:Xamarin.Forms.TimePicker.Time)属性的默认绑定模式为[`BindingMode.TwoWay`](xref:Xamarin.Forms.BindingMode.TwoWay)，这意味着它可以是使用[ViewModel （MVVM）](~/xamarin-forms/enterprise-application-patterns/mvvm.md)体系结构的应用程序中数据绑定的目标。

[@No__t_1](xref:Xamarin.Forms.TimePicker)不包含用于指示新选定[`Time`](xref:Xamarin.Forms.TimePicker.Time)值的事件。 如果需要得到通知，可以为[`PropertyChanged`](xref:Xamarin.Forms.BindableObject.PropertyChanged)事件添加处理程序。

## <a name="initializing-the-time-property"></a>正在初始化时间属性

在代码中，你可以将[`Time`](xref:Xamarin.Forms.TimePicker.Time)属性初始化为 `TimeSpan` 类型的值：

```csharp
TimePicker timePicker = new TimePicker
{
  Time = new TimeSpan(4, 15, 26) // Time set to "04:15:26"
};
```

当在 XAML 中指定[`Time`](xref:Xamarin.Forms.TimePicker.Time)属性时，该值将转换为 `TimeSpan` 并进行验证，以确保毫秒数大于或等于0，并且小时数小于24。 应该用冒号分隔时间组件：

```xaml
<TimePicker Time="4:15:26" />
```

如果[`TimePicker`](xref:Xamarin.Forms.TimePicker)的[`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext)属性设置为包含名为 `SelectedTime` `TimeSpan` 类型属性的 ViewModel 的实例（例如），则可以实例化 `TimePicker`，如下所示：

```xaml
<TimePicker Time="{Binding SelectedTime}" />
```

在此示例中， [`Time`](xref:Xamarin.Forms.TimePicker.Time)属性初始化为 ViewModel 中的 `SelectedTime` 属性。 因为 `Time` 属性的绑定模式为[`TwoWay`](xref:Xamarin.Forms.BindingMode.TwoWay)，所以用户选择的任何新时间都会自动传播到 ViewModel。

如果[`TimePicker`](xref:Xamarin.Forms.TimePicker)未在其[`Time`](xref:Xamarin.Forms.TimePicker.Time)属性上包含绑定，应用程序应将处理程序附加到[`PropertyChanged`](xref:Xamarin.Forms.BindableObject.PropertyChanged)事件，以便在用户选择新时间时收到通知。

有关设置字体属性的信息，请参阅[字体](~/xamarin-forms/user-interface/text/fonts.md)。

## <a name="timepicker-and-layout"></a>TimePicker 和布局

可以使用不受约束的水平布局选项，如 `Center`、`Start` 或 `End` [`TimePicker`](xref:Xamarin.Forms.TimePicker)：

```xaml
<TimePicker ···
            HorizontalOptions="Center"
            ··· />
```

但是，不建议这样做。 根据[`Format`](xref:Xamarin.Forms.TimePicker.Format)属性的设置，所选时间可能需要不同的显示宽度。 例如，"T" 格式字符串会使[`TimePicker`](xref:Xamarin.Forms.TimePicker)视图以长格式显示时间，而 "4:15:26 am" 需要比 "4:15 AM" 的短时间格式（"T"）更大的显示宽度。 根据具体的平台，这种差异可能会导致 `TimePicker` 视图更改布局中的宽度或使显示被截断。

> [!TIP]
> 最好使用 `Fill` 与[`TimePicker`](xref:Xamarin.Forms.TimePicker)的默认 `HorizontalOptions` 设置，而不是在 `TimePicker` 单元中放置 `Grid` 时使用 `Auto` 的宽度。

## <a name="timepicker-in-an-application"></a>应用程序中的 TimePicker

[**SetTimer**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-timepicker)示例在其页面上包含[`TimePicker`](xref:Xamarin.Forms.TimePicker)、 [`Entry`](xref:Xamarin.Forms.Entry)和[`Switch`](xref:Xamarin.Forms.Switch)视图。 @No__t_0 可用于选择某个时间，在该时间发生时，将显示一个警报对话框，该对话框会提醒用户在 `Entry` 中的文本，前提是 `Switch` 已切换。 下面是 XAML 文件：

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

通过[`Entry`](xref:Xamarin.Forms.Entry)可以输入将在所选时间发生时显示的提醒文本。 [@No__t_1](xref:Xamarin.Forms.TimePicker)为长时间格式分配了[`Format`](xref:Xamarin.Forms.TimePicker.Format)属性 "t"。 它有附加到[`PropertyChanged`](xref:Xamarin.Forms.BindableObject.PropertyChanged)事件的事件处理程序，并且[`Switch`](xref:Xamarin.Forms.Switch)具有附加到其[`Toggled`](xref:Xamarin.Forms.Switch.Toggled)事件的处理程序。 这些事件处理程序在代码隐藏文件中，并调用 `SetTriggerTime` 方法：

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

@No__t_0 方法基于 `DateTime.Today` 属性值和从[`TimePicker`](xref:Xamarin.Forms.TimePicker)返回的 `TimeSpan` 值计算计时器时间。 这是必需的，因为 `DateTime.Today` 属性返回一个表示当前日期的 `DateTime`，但时间为午夜。 如果计时器时间现在已经过去，则假定它是明天的时间。

计时器每秒计时，执行 `OnTimerTick` 方法，该方法检查[`Switch`](xref:Xamarin.Forms.Switch)是否打开以及当前时间是否大于或等于计时器时间。 出现计时器时间时， [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert*)方法会向用户显示一个警报对话框作为提醒。

首次运行该示例时， [`TimePicker`](xref:Xamarin.Forms.TimePicker)视图将初始化为11am。 点击 `TimePicker` 将调用平台时间选择器。 平台以不同的方式实现时间选取器，但对于该平台的用户而言，每种方法都很熟悉：

[![选择时间](timepicker-images/timepicker-open.png "选择时间")](timepicker-images/timepicker-open-large.png#lightbox "选择时间")

> [!TIP]
> 在 Android 上，可以通过重写自定义呈现器中的 `CreateTimePickerDialog` 方法来自定义[`TimePicker`](xref:Xamarin.Forms.TimePicker)对话框。 例如，这允许向对话框添加其他按钮。

选择一段时间后，所选时间会显示在[`TimePicker`](xref:Xamarin.Forms.TimePicker)中：

[![所选时间](timepicker-images/timepicker-selected.png "所选时间")](timepicker-images/timepicker-selected-large.png#lightbox "所选时间")

如果[`Switch`](xref:Xamarin.Forms.Switch)切换到 "开" 位置，应用程序将显示一个警报对话框，提醒用户在发生所选时间时[`Entry`](xref:Xamarin.Forms.Entry)中的文本：

[![计时器弹出窗口](timepicker-images/timer-test.png "计时器弹出窗口")](timepicker-images/timer-test-large.png#lightbox "计时器弹出窗口")

一旦显示了警报对话框，就会将[`Switch`](xref:Xamarin.Forms.Switch)切换到 off 位置。

## <a name="related-links"></a>相关链接

- [SetTimer 示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-timepicker)
- [TimePicker API](xref:Xamarin.Forms.TimePicker)
