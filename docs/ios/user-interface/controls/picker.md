---
title: 在 Xamarin.iOS 中选取器控件
description: 本文档介绍如何设计和使用 Xamarin.iOS 应用程序中的选取器控件。 还会讨论如何在代码中和 iOS 设计器中实现了选取器。
ms.prod: xamarin
ms.assetid: A2369EFC-285A-44DD-9E80-EC65BC3DF041
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 08/14/2018
ms.openlocfilehash: 946cba08e1e504962c093f67e336d72b654a3a41
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61229080"
---
# <a name="picker-control-in-xamarinios"></a>在 Xamarin.iOS 中选取器控件

一个[ `UIPickerView` ](xref:UIKit.UIPickerView)就可以通过滚动滚轮类似的界面的各个组件选择列表中的值。

选取器通常用于选择日期和时间;Apple 提供 [`UIDatePicker`](xref:UIKit.UIDatePicker)
此目的的的类。

本文介绍如何实现和使用`UIPickerView`和`UIDatePicker`控件。

## <a name="uipickerview"></a>UIPickerView

### <a name="implementing-a-picker"></a>实现了选取器

通过实例化一个新实现选取器`UIPickerView`:

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

若要创建中的选取**iOS 设计器**，拖动**选取器视图**从**工具箱**到设计图面。

![将选取器视图拖动到设计图面](picker-images/image1.png "将选取器视图拖动到设计图面")

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

[ `UIPickerViewModel` ](xref:UIKit.UIPickerViewModel)基类实现两个接口， [`IUIPickerDataSource`](xref:UIKit.IUIPickerViewDataSource)
并[ `IUIPickerViewDelegate` ](xref:UIKit.IUIPickerViewDelegate)，其中声明指定选取器的数据的各种方法以及如何处理交互：

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

选取器可以有多个列，或_组件_。 组件划分为选取器多个部分，从而更轻松、 更具体的数据选择：

![选取器的两个组件](picker-images/image3.png "的两个组件的选取器")

若要选取器中指定多个组件，请使用 [`GetComponentCount`](xref:UIKit.UIPickerViewModel.GetComponentCount(UIKit.UIPickerView)) 
方法。

### <a name="customizing-a-pickers-appearance"></a>自定义选取器的外观

若要自定义选取器的外观，请使用 [`UIPickerView.UIPickerViewAppearance`](xref:UIKit.UIPickerView.UIPickerViewAppearance)
类或重写[ `GetView` ](xref:UIKit.UIPickerViewModel.GetView(UIKit.UIPickerView,System.nint,System.nint,UIKit.UIView))并[ `GetRowHeight` ](xref:UIKit.UIPickerViewModel.GetRowHeight(UIKit.UIPickerView,System.nint))中的方法`UIPickerViewModel`。

## <a name="uidatepicker"></a>UIDatePicker

### <a name="implementing-a-date-picker"></a>实现日期选取器

通过实例化实现日期选取器`UIDatePicker`:

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

若要创建日期选取器中的**iOS 设计器**，拖动**日期选取器**从**工具箱**到设计图面。

![将日期选取器拖到设计图面](picker-images/image2.png "将日期选取器拖到设计图面")

### <a name="date-picker-properties"></a>日期选取器属性

#### <a name="minimum-and-maximum-date"></a>最小值和最大日期

[`MinimumDate`](xref:UIKit.UIDatePicker.MinimumDate) 并[ `MaximumDate` ](xref:UIKit.UIDatePicker.MaximumDate)限制的日期选取器中可用的日期范围。 例如，下面的代码可限制到 60 年到存在时间点导致日期选取器：

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
> 可以显式转换`DateTime`到`NSDate`:
> ```csharp
> DatePicker.MinimumDate = (NSDate)DateTime.Today.AddDays (-7);
> DatePicker.MaximumDate = (NSDate)DateTime.Today.AddDays (7);
> ```

#### <a name="minute-interval"></a>分钟间隔

[ `MinuteInterval` ](xref:UIKit.UIDatePicker.MinuteInterval)属性设置选取器显示分钟的时间间隔：

```csharp
datePickerView.MinuteInterval = 10;
```

#### <a name="mode"></a>模式

日期选取器支持四个[模式](xref:UIKit.UIDatePickerMode)，如下所示：

##### <a name="uidatepickermodetime"></a>UIDatePickerMode.Time

`UIDatePickerMode.Time` 显示以小时和分钟的选择器和可选 AM 或 PM 指定内容的时间：

```csharp
datePickerView.Mode = UIDatePickerMode.Time;
```

![UIDatePickerMode.Time](picker-images/image8.png "UIDatePickerMode.Time")

##### <a name="uidatepickermodedate"></a>UIDatePickerMode.Date

`UIDatePickerMode.Date` 显示具有月、 日和年选择器的日期：

```csharp
datePickerView.Mode = UIDatePickerMode.Date;
```

![UIDatePickerMode.Date](picker-images/image7.png "UIDatePickerMode.Date")

选择器的顺序取决于日期选取器的区域设置，它默认使用的系统区域设置。 上图显示中的选择器的布局`en_US`区域设置，但以下更改的顺序一天 |月 |年份：

```csharp
datePickerView.Locale = NSLocale.FromLocaleIdentifier("en_GB");
```

![日 |月 |年份](picker-images/image9.png "日 |月 |一年")

##### <a name="uidatepickermodedateandtime"></a>UIDatePickerMode.DateAndTime

`UIDatePickerMode.DateAndTime` 显示日期，以小时和分钟为单位和 （具体取决于是否使用采用 12 或 24 小时制） 可选 AM 或 PM 指定的时间缩短了的的视图：

```csharp
datePickerView.Mode = UIDatePickerMode.DateAndTime;
```

![UIDatePickerMode.DateAndTime](picker-images/image6.png "UIDatePickerMode.DateAndTime")

如同[ `UIDatePickerMode.Date` ](#uidatepickermodedate)，选择器的顺序和采用 12 或 24 小时制的使用取决于日期选取器的区域设置。

> [!TIP]
> 使用`Date`属性来捕获日期选取器模式下的值`UIDatePickerMode.Time`， `UIDatePickerMode.Date`，或`UIDatePickerMode.DateAndTime`。 此值存储为`NSDate`。

##### <a name="uidatepickermodecountdowntimer"></a>UIDatePickerMode.CountDownTimer

`UIDatePickerMode.CountDownTimer` 显示小时和分钟值：

```csharp
datePickerView.Mode = UIDatePickerMode.CountDownTimer;
```

!["UIDatePickerMode.CountDownTimer"](picker-images/image5.png "UIDatePickerMode.CountDownTimer")

`CountDownDuration`属性将捕获日期选取器中的值`UIDatePickerMode.CountDownTimer`模式。 例如，若要添加到当前日期的倒计时值：

```csharp
var currentTime = NSDate.Now;
var countDownTimerTime = datePickerView.CountDownDuration;
var finishCountdown = currentTime.AddSeconds(countDownTimerTime);

dateLabel.Text = "Alarm set for:" + coundownTimeformat.ToString(finishCountdown);
```

#### <a name="nsdateformatter"></a>NSDateFormatter

若要设置格式`NSDate`，使用[ `NSDateFormatter` ](xref:Foundation.NSDateFormatter)。

若要使用`NSDateFormatter`，调用其[ `ToString` ](xref:Foundation.NSDateFormatter.ToString(Foundation.NSDate))方法。 例如：

```csharp
var date = NSDate.Now;
var formatter = new NSDateFormatter();
formatter.DateStyle = NSDateFormatterStyle.Full;
formatter.TimeStyle = NSDateFormatterStyle.Full;
var formattedDate = formatter.ToString(d);
// Tuesday, August 14, 2018 at 11:20:42 PM Mountain Daylight Time
```

##### <a name="dateformat"></a>DateFormat

[ `DateFormat` ](xref:Foundation.NSDateFormatter.DateFormat)属性 （字符串） 的`NSDateFormatter`允许可自定义日期格式规范：

```csharp
NSDateFormatter dateFormat = new NSDateFormatter();
dateFormat.DateFormat = "yyyy-MM-dd";
```

##### <a name="timestyle"></a>TimeStyle

[ `TimeStyle` ](xref:Foundation.NSDateFormatter.TimeStyle)属性 ( [ `NSDateFormatterStyle` ](xref:Foundation.NSDateFormatterStyle)的`NSDateFormatter`指定时间格式设置基于预先确定的样式：

```csharp
NSDateFormatter timeFormat = new NSDateFormatter();
timeFormat.TimeStyle = NSDateFormatterStyle.Short;
```

各种`NSDateFormatterStyle`值显示时间，如下所示：

- `NSDateFormatterStyle.Full`：7:46:00 PM 东部夏时制时间
- `NSDateFormatterStyle.Long`：7:47:00 PM EDT
- `NSDateFormatterStyle.Medium`：7:47:00 PM
- `NSDateFormatterSytle.Short`：7:47 PM

##### <a name="datestyle"></a>DateStyle

[ `DateStyle` ](xref:Foundation.NSDateFormatter.DateStyle)属性 ( `NSDateFormatterStyle`) 的`NSDateFormatter`指定日期格式设置基于预先确定的样式：

```csharp
NSDateFormatter dateTimeformat = new NSDateFormatter();
dateTimeformat.DateStyle = NSDateFormatterStyle.Long;
```

各种`NSDateFormatterStyle`值显示日期，如下所示：

- `NSDateFormatterStyle.Full`：星期三，2017 年 8 月 2 日下午 7:48
- `NSDateFormatterStyle.Long`：2017 年 8 月 2日日下午 7:49
- `NSDateFormatterStyle.Medium`：2017 年 8 月 2日日，7:49 PM
- `NSDateFormatterStyle.Short`：2017 年 8 月 2 日，7:50 PM

> [!NOTE]
> `DateFormat` 并`DateStyle` / `TimeStyle`提供不同的方式指定日期和时间格式设置。 最新设置的属性确定日期格式化程序的输出。

## <a name="related-links"></a>相关链接

- [PickerControl （示例）](https://developer.xamarin.com/samples/monotouch/PickerControl/)
