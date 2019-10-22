---
title: Xamarin. Forms DatePicker
description: DatePicker 是一个 Xamarin 窗体视图，它允许用户选择日期。 本文介绍如何在 Xamarin. Forms 应用程序中使用 DatePicker。
ms.prod: xamarin
ms.assetid: 68E8EF8A-42E7-4939-8ABE-64D060E609D9
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/04/2018
ms.openlocfilehash: 15d4159d89a463c0335d9c91b24b55151c91de8c
ms.sourcegitcommit: 21d8be9571a2fa89fb7d8ff0787ff4f957de0985
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/21/2019
ms.locfileid: "72695915"
---
# <a name="xamarinforms-datepicker"></a>Xamarin. Forms DatePicker

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-datepicker)

_允许用户选择日期的 Xamarin 窗体视图。_

Xamarin [`DatePicker`](xref:Xamarin.Forms.DatePicker)调用平台的日期选取器控件，并允许用户选择日期。 `DatePicker` 定义了八个属性：

- [`DateTime`](xref:System.DateTime)类型的[`MinimumDate`](xref:Xamarin.Forms.DatePicker.MinimumDate) ，默认为1900年的第一天。
- `DateTime` 类型的[`MaximumDate`](xref:Xamarin.Forms.DatePicker.MaximumDate) ，默认为2100年的最后一天。
- [`Date`](xref:Xamarin.Forms.DatePicker.Date)类型 `DateTime`，即所选日期，默认为[`DateTime.Today`](xref:System.DateTime.Today)值。
- `string` 类型的[`Format`](xref:Xamarin.Forms.DatePicker.Format) 、[标准](/dotnet/standard/base-types/standard-date-and-time-format-strings/)或[自定义](/dotnet/standard/base-types/custom-date-and-time-format-strings/)的 .net 格式设置字符串（默认为 "D"）和长日期模式。
- [`TextColor`](xref:Xamarin.Forms.DatePicker.TextColor)类型[`Color`](xref:Xamarin.Forms.Color)，用于显示选定日期的颜色（默认为[`Color.Default`](xref:Xamarin.Forms.Color.Default)）。
- [`FontAttributes`](xref:Xamarin.Forms.FontAttributes)类型的[`FontAttributes`](xref:Xamarin.Forms.DatePicker.FontAttributes) ，默认为[`FontAtributes.None`](xref:Xamarin.Forms.FontAttributes.None)。
- `string` 类型的[`FontFamily`](xref:Xamarin.Forms.DatePicker.FontFamily) ，默认为 `null`。
- `double` 类型的[`FontSize`](xref:Xamarin.Forms.DatePicker.FontSize) ，默认值为-1.0。
- `CharacterSpacing` 类型 `double`，是 `DatePicker` 文本的字符之间的间距。

当用户选择日期时，`DatePicker` 激发[`DateSelected`](xref:Xamarin.Forms.DatePicker.DateSelected)事件。

> [!WARNING]
> 设置 `MinimumDate` 和 `MaximumDate` 时，请确保 `MinimumDate` 始终小于或等于 `MaximumDate`。 否则，`DatePicker` 会引发异常。

@No__t_0 确保 `Date` 介于 `MinimumDate` 和 `MaximumDate` （含）之间。 如果设置 `MinimumDate` 或 `MaximumDate` 以便 `Date` 不在两者之间，`DatePicker` 将调整 `Date` 的值。

所有八个属性都是由[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)对象支持的，这意味着它们可以采用样式，属性可以是数据绑定的目标。 @No__t_0 属性的默认绑定模式为[`BindingMode.TwoWay`](xref:Xamarin.Forms.BindingMode.TwoWay)，这意味着它可以是使用[ViewModel （MVVM）](~/xamarin-forms/enterprise-application-patterns/mvvm.md)体系结构的应用程序中数据绑定的目标。

## <a name="initializing-the-datetime-properties"></a>初始化 DateTime 属性

在代码中，可以将 `MinimumDate`、`MaximumDate` 和 `Date` 属性初始化为类型 `DateTime` 的值：

```csharp
DatePicker datePicker = new DatePicker
{
    MinimumDate = new DateTime(2018, 1, 1),
    MaximumDate = new DateTime(2018, 12, 31),
    Date = new DateTime(2018, 6, 21)
};
```

如果在 XAML 中指定了 `DateTime` 值，则 XAML 分析器将使用带有 `CultureInfo.InvariantCulture` 参数的 `DateTime.Parse` 方法将字符串转换为 `DateTime` 值。 必须以精确格式指定日期：两位数的月份、两位数日期和四位数年份，用斜杠分隔：

```xaml
<DatePicker MinimumDate="01/01/2018"
            MaximumDate="12/31/2018"
            Date="06/21/2018" />
```

如果 `DatePicker` 的 `BindingContext` 属性设置为包含类型为 `MinDate`、`MaxDate` 和 `SelectedDate` 的 `DateTime` 类型属性的 viewmodel 的实例（例如），则可以实例化此类 `DatePicker`:

```xaml
<DatePicker MinimumDate="{Binding MinDate}"
            MaximumDate="{Binding MaxDate}"
            Date="{Binding SelectedDate}" />
```

在此示例中，所有三个属性都初始化为 viewmodel 中的相应属性。 因为 `Date` 属性的绑定模式为 `TwoWay`，所以用户选择的任何新日期都会自动反映在 viewmodel 中。

如果 `DatePicker` 未在其 `Date` 属性上包含绑定，则应用程序应将处理程序附加到 `DateSelected` 事件，以便在用户选择新日期时收到通知。

有关设置字体属性的信息，请参阅[字体](~/xamarin-forms/user-interface/text/fonts.md)。

## <a name="datepicker-and-layout"></a>DatePicker 和布局

可以使用不受约束的水平布局选项，如 `Center`、`Start` 或 `End` `DatePicker`：

```xaml
<DatePicker ···
            HorizontalOptions="Center"
            ··· />
```

但是，不建议这样做。 根据 `Format` 属性的设置，所选日期可能需要不同的显示宽度。 例如，"D" 格式字符串会使 `DateTime` 以长格式显示日期，而 "星期三，9月12日，2018" 要求的显示宽度大于 "星期五，5月4，2018"。 根据具体的平台，这种差异可能会导致 `DateTime` 视图更改布局中的宽度或使显示被截断。

> [!TIP]
> 最好使用 `Fill` 与 `DatePicker` 的默认 `HorizontalOptions` 设置，而不是在 `DatePicker` 单元中放置 `Grid` 时使用 `Auto` 的宽度。

## <a name="datepicker-in-an-application"></a>应用程序中的 DatePicker

[**DaysBetweenDates**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-datepicker)示例在其页面上包含两个 `DatePicker` 视图。 这些值可用于选择两个日期，程序将计算这些日期之间的天数。 此程序不会更改 `MinimumDate` 和 `MaximumDate` 属性的设置，因此两个日期必须介于1900和2100之间。

下面是 XAML 文件：

```xaml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DaysBetweenDates"
             x:Class="DaysBetweenDates.MainPage">
    <ContentPage.Padding>
        <OnPlatform x:TypeArguments="Thickness">
            <On Platform="iOS" Value="0, 20, 0, 0" />
        </OnPlatform>
    </ContentPage.Padding>

    <StackLayout Margin="10">
        <Label Text="Days Between Dates"
               Style="{DynamicResource TitleStyle}"
               Margin="0, 20"
               HorizontalTextAlignment="Center" />

        <Label Text="Start Date:" />

        <DatePicker x:Name="startDatePicker"
                    Format="D"
                    Margin="30, 0, 0, 30"
                    DateSelected="OnDateSelected" />

        <Label Text="End Date:" />

        <DatePicker x:Name="endDatePicker"
                    MinimumDate="{Binding Source={x:Reference startDatePicker},
                                          Path=Date}"
                    Format="D"
                    Margin="30, 0, 0, 30"
                    DateSelected="OnDateSelected" />

        <StackLayout Orientation="Horizontal"
                     Margin="0, 0, 0, 30">
            <Label Text="Include both days in total: "
                   VerticalOptions="Center" />
            <Switch x:Name="includeSwitch"
                    Toggled="OnSwitchToggled" />
        </StackLayout>

        <Label x:Name="resultLabel"
               FontAttributes="Bold"
               HorizontalTextAlignment="Center" />

    </StackLayout>
</ContentPage>
```

为每个 `DatePicker` 分配一个 "D" 的 `Format` 属性作为长日期格式。 另请注意，`endDatePicker` 对象具有面向其 `MinimumDate` 属性的绑定。 绑定源是 `startDatePicker` 对象的选定 `Date` 属性。 这可确保结束日期始终晚于或等于开始日期。 除两个 `DatePicker` 对象外，`Switch` 标有 "包含全部日期"。

这两个 `DatePicker` 视图具有附加到 `DateSelected` 事件的处理程序，并且 `Switch` 包含附加到其 `Toggled` 事件的处理程序。 这些事件处理程序在代码隐藏文件中，并在两个日期之间触发了日期的新计算：

```csharp
public partial class MainPage : ContentPage
{
    public MainPage()
    {
        InitializeComponent();
    }

    void OnDateSelected(object sender, DateChangedEventArgs args)
    {
        Recalculate();
    }

    void OnSwitchToggled(object sender, ToggledEventArgs args)
    {
        Recalculate();
    }

    void Recalculate()
    {
        TimeSpan timeSpan = endDatePicker.Date - startDatePicker.Date +
            (includeSwitch.IsToggled ? TimeSpan.FromDays(1) : TimeSpan.Zero);

        resultLabel.Text = String.Format("{0} day{1} between dates",
                                            timeSpan.Days, timeSpan.Days == 1 ? "" : "s");
    }
}
```

第一次运行该示例时，两 `DatePicker` 视图都将初始化为当前日期。 以下屏幕截图显示了在 iOS、Android 和通用 Windows 平台上运行的程序：

[![开始日期的天数](datepicker-images/DaysBetweenDatesStart.png "开始日期的天数")](datepicker-images/DaysBetweenDatesStart-Large.png#lightbox "开始日期的天数")

点击任一 `DatePicker` 显示将调用平台日期选取器。 平台以不同的方式实现此日期选取器，但对于该平台的用户来说，每种方法都很熟悉：

[![日期间隔天数选择](datepicker-images/DaysBetweenDatesSelect.png "日期间隔天数选择")](datepicker-images/DaysBetweenDatesSelect-Large.png#lightbox "日期间隔天数选择")

> [!TIP]
> 在 Android 上，可以通过重写自定义呈现器中的 `CreateDatePickerDialog` 方法来自定义 `DatePicker` 对话框。 例如，这允许向对话框添加其他按钮。

选择两个日期后，应用程序会显示这些日期之间的天数：

[![日期之间的天数结果](datepicker-images/DaysBetweenDatesResult.png "日期之间的天数结果")](datepicker-images/DaysBetweenDatesResult-Large.png#lightbox "日期之间的天数结果")

## <a name="related-links"></a>相关链接

- [DaysBetweenDates 示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-datepicker)
- [DatePicker API](xref:Xamarin.Forms.DatePicker)
