---
title: Xamarin 中的选取器控件
description: 本文档介绍如何在 Xamarin iOS 应用程序中设计和使用选取器控件。 本文介绍如何在代码和 iOS 设计器中实现选取器。
ms.prod: xamarin
ms.assetid: A2369EFC-285A-44DD-9E80-EC65BC3DF041
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 08/14/2018
ms.openlocfilehash: 9eec99ffe244ffdc290050bd54f083ad6582151d
ms.sourcegitcommit: 699de58432b7da300ddc2c85842e5d9e129b0dc5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2019
ms.locfileid: "70286388"
---
# <a name="picker-control-in-xamarinios"></a>Xamarin 中的选取器控件

[`UIPickerView`](xref:UIKit.UIPickerView)利用，可以通过滚动使用色轮的接口的单个组件来从列表中选取一个值。

选取器通常用于选择日期和时间;Apple 提供[`UIDatePicker`](xref:UIKit.UIDatePicker)
用于实现此目的的类。

本文介绍如何实现和使用`UIPickerView`和`UIDatePicker`控件。

## <a name="uipickerview"></a>UIPickerView

### <a name="implementing-a-picker"></a>实现选取器

通过实例化新`UIPickerView`的来实现选取器：

```csharp
UIPickerView pickerView = new UIPickerView(
    new CGRect(
        UIScreen.MainScreen.Bounds.X - UIScreen.MainScreen.Bounds.Width, 
        UIScreen.MainScreen.Bounds.Height - 230,
        UIScreen.MainScreen.Bounds.Width,
        180
    )
);
```

### <a name="pickers-and-storyboards"></a>选取器和情节提要

若要在**IOS 设计器**中创建选取器，请将 "**选取器" 视图**从 "**工具箱**" 拖动到设计图面。

![将选取器视图拖到设计图面上](picker-images/image1.png "将选取器视图拖到设计图面上")

### <a name="working-with-a-picker-control"></a>使用选取器控件

选取器使用_模型_与数据进行交互：

```csharp
public override void ViewDidLoad()
{
    base.ViewDidLoad();
    var pickerModel = new PeopleModel(personLabel);
    personPicker.Model = pickerModel;
}
```

[`UIPickerViewModel`](xref:UIKit.UIPickerViewModel)基类实现了两个接口，[`IUIPickerDataSource`](xref:UIKit.IUIPickerViewDataSource)
和[`IUIPickerViewDelegate`](xref:UIKit.IUIPickerViewDelegate)，它声明了用于指定选取器的数据以及它如何处理交互的各种方法：

```csharp
public class PeopleModel : UIPickerViewModel
{
    public string[] names = new string[] {
            "Amy Burns",
            "Kevin Mullins",
            "Craig Dunn",
            "Joel Martinez",
            "Charles Petzold",
            "David Britch",
            "Mark McLemore",
            "Tom Opegenorth",
            "Joseph Hill",
            "Miguel De Icaza"
        };

    private UILabel personLabel;

    public PeopleModel(UILabel personLabel)
    {
        this.personLabel = personLabel;
    }

    public override nint GetComponentCount(UIPickerView pickerView)
    {
        return 2;
    }

    public override nint GetRowsInComponent(UIPickerView pickerView, nint component)
    {
        return names.Length;
    }

    public override string GetTitle(UIPickerView pickerView, nint row, nint component)
    {
        if (component == 0)
            return names[row];
        else
            return row.ToString();
    }

    public override void Selected(UIPickerView pickerView, nint row, nint component)
    {
        personLabel.Text = $"This person is: {names[pickerView.SelectedRowInComponent(0)]},\n they are number {pickerView.SelectedRowInComponent(1)}";
    }

    public override nfloat GetComponentWidth(UIPickerView picker, nint component)
    {
        if (component == 0)
            return 240f;
        else
            return 40f;
    }

    public override nfloat GetRowHeight(UIPickerView picker, nint component)
    {
        return 40f;
    }
```

一个选取器可以有多个列或_组件_。 组件将选取器分区为多个部分，以便更容易和更具体的数据选择：

![具有两个组件的选取器](picker-images/image3.png "具有两个组件的选取器")

若要指定选取器中的组件数，请使用[`GetComponentCount`](xref:UIKit.UIPickerViewModel.GetComponentCount(UIKit.UIPickerView)) 
方法。

### <a name="customizing-a-pickers-appearance"></a>自定义选取器外观

若要自定义选取器的外观，请使用[`UIPickerView.UIPickerViewAppearance`](xref:UIKit.UIPickerView.UIPickerViewAppearance)
类或重写[`GetView`](xref:UIKit.UIPickerViewModel.GetView(UIKit.UIPickerView,System.nint,System.nint,UIKit.UIView))中[`GetRowHeight`](xref:UIKit.UIPickerViewModel.GetRowHeight(UIKit.UIPickerView,System.nint)) `UIPickerViewModel`的和方法。

## <a name="uidatepicker"></a>UIDatePicker

### <a name="implementing-a-date-picker"></a>实现日期选取器

通过实例化`UIDatePicker`实现日期选取器：

```csharp
UIPickerView pickerView = new UIPickerView(
    new CGRect(
        UIScreen.MainScreen.Bounds.X - UIScreen.MainScreen.Bounds.Width,
        UIScreen.MainScreen.Bounds.Height - 230,
        UIScreen.MainScreen.Bounds.Width,
        180
     )
);
```

### <a name="date-pickers-and-storyboards"></a>日期选取器和情节提要

若要在**IOS 设计器**中创建日期选取器，请将 "**日期选取器**" 从 "**工具箱**" 拖动到设计图面。

![将日期选取器拖动到设计图面上](picker-images/image2.png "将日期选取器拖动到设计图面上")

### <a name="date-picker-properties"></a>日期选取器属性

#### <a name="minimum-and-maximum-date"></a>最小和最大日期

[`MinimumDate`](xref:UIKit.UIDatePicker.MinimumDate)和[`MaximumDate`](xref:UIKit.UIDatePicker.MaximumDate)限制日期选取器中可用的日期范围。 例如，以下代码将日期选取器限制为当前时间的60年：

```csharp
var calendar = new NSCalendar(NSCalendarType.Gregorian);
var currentDate = NSDate.Now;
var components = new NSDateComponents();
components.Year = -60;
NSDate minDate = calendar.DateByAddingComponents(components, NSDate.Now, NSCalendarOptions.None);
datePickerView.MinimumDate = minDate;
datePickerView.MaximumDate = NSDate.Now;
```

> [!TIP]
> 可以显式将转换`DateTime` `NSDate`为：
>
> ```csharp
> DatePicker.MinimumDate = (NSDate)DateTime.Today.AddDays (-7);
> DatePicker.MaximumDate = (NSDate)DateTime.Today.AddDays (7);
> ```

#### <a name="minute-interval"></a>分钟间隔

[`MinuteInterval`](xref:UIKit.UIDatePicker.MinuteInterval)属性设置选取器显示分钟数的时间间隔：

```csharp
datePickerView.MinuteInterval = 10;
```

#### <a name="mode"></a>模式

日期选取器支持四种[模式](xref:UIKit.UIDatePickerMode)，如下所述：

##### <a name="uidatepickermodetime"></a>UIDatePickerMode.Time

`UIDatePickerMode.Time`显示时间，其中包含小时和分钟选择器以及可选的 AM 或 PM 指定：

```csharp
datePickerView.Mode = UIDatePickerMode.Time;
```

![UIDatePickerMode.Time](picker-images/image8.png "UIDatePickerMode.Time")

##### <a name="uidatepickermodedate"></a>UIDatePickerMode.Date

`UIDatePickerMode.Date`显示日期，其中包含月、日和年选择器：

```csharp
datePickerView.Mode = UIDatePickerMode.Date;
```

![UIDatePickerMode.Date](picker-images/image7.png "UIDatePickerMode.Date")

选择器的顺序取决于日期选取器的区域设置，默认情况下使用系统区域设置。 上图显示了`en_US`区域设置中的选择器布局，但以下各项会将订单更改为 Day |Month |年

```csharp
datePickerView.Locale = NSLocale.FromLocaleIdentifier("en_GB");
```

![Day |Month |年](picker-images/image9.png "日 |Month |年")

##### <a name="uidatepickermodedateandtime"></a>UIDatePickerMode.DateAndTime

`UIDatePickerMode.DateAndTime`显示日期的缩短日期、以小时和分钟为单位的时间，以及可选的 AM 或 PM 指定（取决于是否使用12小时制或24小时制）：

```csharp
datePickerView.Mode = UIDatePickerMode.DateAndTime;
```

![UIDatePickerMode.DateAndTime](picker-images/image6.png "UIDatePickerMode.DateAndTime")

与[`UIDatePickerMode.Date`](#uidatepickermodedate)一样，选择器的顺序以及12或24小时制的使用取决于日期选取器的区域设置。

> [!TIP]
> 使用属性可在模式`UIDatePickerMode.Time`、 `UIDatePickerMode.Date`或`UIDatePickerMode.DateAndTime`中捕获日期选取器的值。 `Date` 此值存储为`NSDate`。

##### <a name="uidatepickermodecountdowntimer"></a>UIDatePickerMode.CountDownTimer

`UIDatePickerMode.CountDownTimer`显示小时和分钟值：

```csharp
datePickerView.Mode = UIDatePickerMode.CountDownTimer;
```

!["UIDatePickerMode.CountDownTimer"](picker-images/image5.png "UIDatePickerMode.CountDownTimer")

`CountDownDuration`属性以`UIDatePickerMode.CountDownTimer`模式捕获日期选取器的值。 例如，要将倒计时值添加到当前日期：

```csharp
var currentTime = NSDate.Now;
var countDownTimerTime = datePickerView.CountDownDuration;
var finishCountdown = currentTime.AddSeconds(countDownTimerTime);

dateLabel.Text = "Alarm set for:" + coundownTimeformat.ToString(finishCountdown);
```

#### <a name="nsdateformatter"></a>NSDateFormatter

若要设置`NSDate`的格式， [`NSDateFormatter`](xref:Foundation.NSDateFormatter)请使用。

若要使用`NSDateFormatter`，请调用[`ToString`](xref:Foundation.NSDateFormatter.ToString(Foundation.NSDate))其方法。 例如:

```csharp
var date = NSDate.Now;
var formatter = new NSDateFormatter();
formatter.DateStyle = NSDateFormatterStyle.Full;
formatter.TimeStyle = NSDateFormatterStyle.Full;
var formattedDate = formatter.ToString(d);
// Tuesday, August 14, 2018 at 11:20:42 PM Mountain Daylight Time
```

##### <a name="dateformat"></a>DateFormat

的属性（字符串） `NSDateFormatter`允许可自定义的日期格式规范： [`DateFormat`](xref:Foundation.NSDateFormatter.DateFormat)

```csharp
NSDateFormatter dateFormat = new NSDateFormatter();
dateFormat.DateFormat = "yyyy-MM-dd";
```

##### <a name="timestyle"></a>TimeStyle

属性（ [`NSDateFormatterStyle`](xref:Foundation.NSDateFormatterStyle) 的`NSDateFormatter`一个指定基于预先确定样式的时间格式设置： [`TimeStyle`](xref:Foundation.NSDateFormatter.TimeStyle)

```csharp
NSDateFormatter timeFormat = new NSDateFormatter();
timeFormat.TimeStyle = NSDateFormatterStyle.Short;
```

各种`NSDateFormatterStyle`值显示时间，如下所示：

- `NSDateFormatterStyle.Full`：7:46:00 东部夏令时
- `NSDateFormatterStyle.Long`：7:47:00 PM EDT
- `NSDateFormatterStyle.Medium`：7:47:00 PM
- `NSDateFormatterSytle.Short`：7:47 PM

##### <a name="datestyle"></a>DateStyle

的[`DateStyle`](xref:Foundation.NSDateFormatter.DateStyle)属性`NSDateFormatterStyle`（ ）`NSDateFormatter`指定基于预先确定样式的日期格式：

```csharp
NSDateFormatter dateTimeformat = new NSDateFormatter();
dateTimeformat.DateStyle = NSDateFormatterStyle.Long;
```

各种`NSDateFormatterStyle`值显示日期，如下所示：

- `NSDateFormatterStyle.Full`：2017年8月2日星期三，上午7:48
- `NSDateFormatterStyle.Long`：8月2日，2017 7:49 PM
- `NSDateFormatterStyle.Medium`：8月2日，2017，7:49 PM
- `NSDateFormatterStyle.Short`：8/2/17，7:50 PM

> [!NOTE]
> `DateFormat`和`DateStyle` 提供了指定`TimeStyle`日期和时间格式的不同方式。 / 最近设置的属性确定日期格式化程序的输出。

## <a name="related-links"></a>相关链接

- [PickerControl （示例）](https://docs.microsoft.com/samples/xamarin/ios-samples/pickercontrol)
