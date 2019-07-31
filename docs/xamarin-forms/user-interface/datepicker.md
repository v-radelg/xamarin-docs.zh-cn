---
title: Xamarin.Forms DatePicker
description: DatePicker 是允许用户选择日期的 Xamarin.Forms 视图。 本文介绍如何使用 Xamarin.Forms 应用程序中的 DatePicker。
ms.prod: xamarin
ms.assetid: 68E8EF8A-42E7-4939-8ABE-64D060E609D9
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/04/2018
ms.openlocfilehash: 521df49a7698bc149d6bca7460cff2df74402bda
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2019
ms.locfileid: "68657017"
---
# <a name="xamarinforms-datepicker"></a>Xamarin.Forms DatePicker

[![下载示例](~/media/shared/download.png)下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-datepicker)

_Xamarin.Forms 视图允许用户选择日期。_

Xamarin.Forms [ `DatePicker` ](xref:Xamarin.Forms.DatePicker)调用平台的日期选取器控件，并允许用户选择日期。 `DatePicker` 定义了八个属性：

- [`MinimumDate`](xref:Xamarin.Forms.DatePicker.MinimumDate) 类型的[ `DateTime` ](xref:System.DateTime)，默认为 1900 年的第一天。
- [`MaximumDate`](xref:Xamarin.Forms.DatePicker.MaximumDate) 类型的`DateTime`的默认值为 2100年的年份的最后一天。
- [`Date`](xref:Xamarin.Forms.DatePicker.Date) 类型的`DateTime`，默认值为所选日期[ `DateTime.Today` ](xref:System.DateTime.Today)。
- [`Format`](xref:Xamarin.Forms.DatePicker.Format) 类型的`string`、 一个[标准](/dotnet/standard/base-types/standard-date-and-time-format-strings/)或[自定义](/dotnet/standard/base-types/custom-date-and-time-format-strings/).NET 格式设置字符串，它默认为"D"，长日期模式。
- [`TextColor`](xref:Xamarin.Forms.DatePicker.TextColor) 类型的[ `Color` ](xref:Xamarin.Forms.Color)，用来显示默认为所选的日期的颜色[ `Color.Default` ](xref:Xamarin.Forms.Color.Default)。
- [`FontAttributes`](xref:Xamarin.Forms.DatePicker.FontAttributes) 类型的[ `FontAttributes` ](xref:Xamarin.Forms.FontAttributes)，其默认值为[ `FontAtributes.None` ](xref:Xamarin.Forms.FontAttributes.None)。
- [`FontFamily`](xref:Xamarin.Forms.DatePicker.FontFamily) 类型的`string`，其默认值为`null`。
- [`FontSize`](xref:Xamarin.Forms.DatePicker.FontSize) 类型的`double`，其默认值为-1.0。

`DatePicker`激发[ `DateSelected` ](xref:Xamarin.Forms.DatePicker.DateSelected)事件时用户选择日期。

> [!WARNING]
> 设置时`MinimumDate`并`MaximumDate`，请确保`MinimumDate`总是小于或等于`MaximumDate`。 否则为`DatePicker`将引发异常。

在内部，`DatePicker`可确保`Date`之间`MinimumDate`和`MaximumDate`(含） 之间。 如果`MinimumDate`或`MaximumDate`设置，以便`Date`之间，不是`DatePicker`的值将调整`Date`。

所有八个属性受[ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty)对象，这意味着它们可以设置的样式，这些属性可以是数据绑定的目标。 `Date`属性设置了默认绑定模式[ `BindingMode.TwoWay` ](xref:Xamarin.Forms.BindingMode.TwoWay)，这意味着它可以是数据绑定中使用的应用程序的目标[模型-视图-视图模型 (MVVM)](~/xamarin-forms/enterprise-application-patterns/mvvm.md)体系结构。

## <a name="initializing-the-datetime-properties"></a>初始化日期时间属性

在代码中，您可以初始化`MinimumDate`， `MaximumDate`，并`Date`类型的值的属性`DateTime`:

```csharp
DatePicker datePicker = new DatePicker
{
    MinimumDate = new DateTime(2018, 1, 1),
    MaximumDate = new DateTime(2018, 12, 31),
    Date = new DateTime(2018, 6, 21)
};
```

当`DateTime`值指定在 XAML，XAML 分析器使用`DateTime.Parse`方法替换`CultureInfo.InvariantCulture`参数来将字符串转换为`DateTime`值。 必须精确格式指定日期： 两位数的月份、 两位数的日期和由斜杠分隔的四位数年份：

```xaml
<DatePicker MinimumDate="01/01/2018"
            MaximumDate="12/31/2018"
            Date="06/21/2018" />
```

如果`BindingContext`的属性`DatePicker`设置为包含类型的属性的 ViewModel 的实例`DateTime`名为`MinDate`， `MaxDate`，并且`SelectedDate`（例如），您可以实例化`DatePicker`如下所示:

```xaml
<DatePicker MinimumDate="{Binding MinDate}"
            MaximumDate="{Binding MaxDate}"
            Date="{Binding SelectedDate}" />
```

在此示例中，所有三个属性将初始化为 ViewModel 中的相应属性。 因为`Date`属性设置了绑定模式`TwoWay`、 用户选择自动反映在 ViewModel 中的任何新日期。

如果`DatePicker`不包含一个绑定上其`Date`属性，应用程序应附加到一个处理程序`DateSelected`事件要通知当用户选择新的日期。

有关设置字体属性的信息，请参阅[字体](~/xamarin-forms/user-interface/text/fonts.md)。

## <a name="datepicker-and-layout"></a>DatePicker 和布局

可以使用不受约束的水平布局选项，如`Center`， `Start`，或`End`与`DatePicker`:

```xaml
<DatePicker ···
            HorizontalOptions="Center"
            ··· />
```

但是，这不是建议在一起。 这取决于设置`Format`所选的属性的日期可能需要不同的显示宽度。 例如，"D"格式字符串会导致`DateTime`若要用长格式和"星期三，2018 年 9 月 12，"显示日期，需要更大的显示宽度比"星期五，4，2018 年 5 月"。 根据平台，这种差异可能会导致`DateTime`视图，以更改宽度在布局中，或显示被截断。

> [!TIP]
> 最好是使用默认`HorizontalOptions`设置为`Fill`与`DatePicker`，而不要使用的宽度`Auto`放置时`DatePicker`中`Grid`单元格。

## <a name="datepicker-in-an-application"></a>应用程序中的 DatePicker

[ **DaysBetweenDates** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-datepicker)示例包含两个`DatePicker`在其页面上的视图。 这些可用于选择两个日期和程序会计算这些日期之间相差的天数。 该程序不会更改的设置`MinimumDate`和`MaximumDate`属性，因此，两个日期必须介于 1900 年 2100年。

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

每个`DatePicker`分配`Format`长日期格式"D"的属性。 另请注意`endDatePicker`对象具有面向的绑定其`MinimumDate`属性。 绑定源是所选`Date`属性的`startDatePicker`对象。 这可确保的结束日期始终更高版本或等于开始日期。 除了这两个`DatePicker`对象，`Switch`标记为"总共包含这两天"。

这两个`DatePicker`视图具有处理程序附加到`DateSelected`事件，并`Switch`具有一个处理程序附加到其`Toggled`事件。 这些事件处理程序中的代码隐藏文件，并触发新的计算的两个日期之间的天数：

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

该示例首先运行时，同时`DatePicker`视图将初始化为当天的日期。 下面的屏幕截图显示了在 iOS、 Android 和通用 Windows 平台上运行的程序：

[![开始日期之间的天数](datepicker-images/DaysBetweenDatesStart.png "开始的日期之间的天数")](datepicker-images/DaysBetweenDatesStart-Large.png#lightbox "开始的日期之间的天数")

点击的`DatePicker`显示调用平台日期选取器。 平台实现此日期选取器很大不同，但是每种方法是熟悉该平台的用户：

[![选择日期之间的天数](datepicker-images/DaysBetweenDatesSelect.png "日期之间的天数选择")](datepicker-images/DaysBetweenDatesSelect-Large.png#lightbox "天的日期之间选择")

> [!TIP]
> 在 Android 上，`DatePicker`可以通过重写自定义对话框`CreateDatePickerDialog`中自定义呈现器的方法。 这样，例如，其他按钮来添加到了对话框。

选择两个日期后，应用程序将显示这些日期之间相差的天数：

[![日期结果之间的天数](datepicker-images/DaysBetweenDatesResult.png "日期结果之间的天数")](datepicker-images/DaysBetweenDatesResult-Large.png#lightbox "日期结果之间的天数")

## <a name="related-links"></a>相关链接

- [DaysBetweenDates 示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-datepicker)
- [DatePicker API](xref:Xamarin.Forms.DatePicker)
