---
title: 日期选取器
description: 选择使用 DatePickerDialog 和 DialogFragment 的日历日期
ms.prod: xamarin
ms.assetid: F2BCD8D4-8957-EA53-C5A8-6BB603ADB47B
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 01/22/2018
ms.openlocfilehash: 9f82317f6041de3952d11b391afffafe6fbd8761
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61153442"
---
# <a name="date-picker"></a>日期选取器

## <a name="overview"></a>概述

没有用户时必须数据输入到 Android 应用程序的情况。 为了帮助解决这个问题，Android 框架提供了[ `DatePicker` ](https://developer.xamarin.com/api/type/Android.Widget.DatePicker/)小组件和[ `DatePickerDialog` ](https://developer.xamarin.com/api/type/Android.App.DatePickerDialog/) 。 `DatePicker` ，用户可以跨设备和应用程序一致的界面中选择年、 月和日。 `DatePickerDialog`是一个帮助器类，用于封装`DatePicker`在对话框中。

现代的 Android 应用程序应显示`DatePickerDialog`中[ `DialogFragment` ](https://developer.xamarin.com/api/type/Android.App.DialogFragment/)。 这将允许应用程序将显示为弹出对话框中的 DatePicker 或嵌入在活动中。 此外，`DialogFragment`将管理的生命周期和显示的对话框中，减少必须实现的代码量。

本指南将演示如何使用`DatePickerDialog`、 包装在`DialogFragment`。 示例应用程序将显示`DatePickerDialog`为模式对话框，当用户单击活动上的按钮。 当由用户设置日期时`TextView`将更新所选的日期。

[![选择日期的屏幕截图按钮，再通过日期选取器对话框](date-picker-images/image-01-sml.png)](date-picker-images/image-01.png#lightbox)

## <a name="requirements"></a>要求

本指南的示例应用程序面向 Android 4.1 （API 级别
16) 或更高版本，但适用于 Android 3.0 （API 级别为 11 或更高版本）。 它是 android 的可以支持较旧版本项目和一些代码更改 Android 支持库 v4 加。

## <a name="using-the-datepicker"></a>使用 DatePicker

此示例将扩展`DialogFragment`。 将托管子类并将其显示`DatePickerDialog`:

![特写的日期选取器对话框](date-picker-images/image-02.png)

当用户选择日期并单击**确定**按钮，`DatePickerDialog`将调用的方法[ `IOnDateSetListener.OnDateSet` ](https://developer.xamarin.com/api/member/Android.App.DatePickerDialog+IOnDateSetListener.OnDateSet/p/Android.Widget.DatePicker/System.Int32/System.Int32/System.Int32/)。
此接口由宿主实现`DialogFragment`。 如果用户单击**取消**按钮，则片段和对话框将关闭本身。

有几种方法`DialogFragment`可以返回到托管的活动的所选的日期：

1. **调用方法或设置属性**&ndash;活动可以提供的属性或方法专门为此值设置。

2. **引发事件** &ndash; `DialogFragment`可以定义将为事件时引发`OnDateSet`调用。

3. **使用`Action`**  &ndash; `DialogFragment`可以调用`Action<DateTime>`显示在活动中的日期。 该活动将提供`Action<DateTime`实例化时`DialogFragment`。 此示例将使用第三种方法，并要求提供活动`Action<DateTime>`到`DialogFragment`。



### <a name="extending-dialogfragment"></a>扩展 DialogFragment

显示的第一步`DatePickerDialog`是子类化`DialogFragment`，并将其实现`IOnDateSetListener`接口：

```csharp
public class DatePickerFragment : DialogFragment, 
                                  DatePickerDialog.IOnDateSetListener
{
    // TAG can be any string of your choice.
    public static readonly string TAG = "X:" + typeof (DatePickerFragment).Name.ToUpper();
    
    // Initialize this value to prevent NullReferenceExceptions.
    Action<DateTime> _dateSelectedHandler = delegate { };
    
    public static DatePickerFragment NewInstance(Action<DateTime> onDateSelected)
    {
        DatePickerFragment frag = new DatePickerFragment();
        frag._dateSelectedHandler = onDateSelected;
        return frag;
    }
    
    public override Dialog OnCreateDialog(Bundle savedInstanceState)
    {
        DateTime currently = DateTime.Now;
        DatePickerDialog dialog = new DatePickerDialog(Activity, 
                                                       this, 
                                                       currently.Year, 
                                                       currently.Month - 1,
                                                       currently.Day);
        return dialog;
    }
    
    public void OnDateSet(DatePicker view, int year, int monthOfYear, int dayOfMonth)
    {
        // Note: monthOfYear is a value between 0 and 11, not 1 and 12!
        DateTime selectedDate = new DateTime(year, monthOfYear + 1, dayOfMonth);
        Log.Debug(TAG, selectedDate.ToLongDateString());
        _dateSelectedHandler(selectedDate);
    }
}
```

`NewInstance`方法调用来实例化一个新`DatePickerFragment`。 此方法采用`Action<DateTime>`，将在用户单击时调用**确定**按钮`DatePickerDialog`。

当显示片段，Android 将调用的方法`OnCreateDialog`。 此方法将创建一个新`DatePickerDialog`对象，并使用当前日期和回调的对象对其进行初始化 (这是当前实例的`DatePickerFragment`)。


> [!NOTE]
> 请注意，月份中的值时`IOnDateSetListener.OnDateSet`调用在 0 到 11 和不在 1 到 12 范围内。 每月天数会在 1 到 31 （具体取决于选择了月） 的范围内。



### <a name="showing-the-datepickerfragment"></a>显示 DatePickerFragment

现在，`DialogFragment`已实现，本部分将说明如何在活动中使用片段。 在本指南附带示例应用，该活动将实例化`DialogFragment`使用`NewInstance`工厂方法，然后显示时，它调用`DialogFragment.Show`。 实例化的一部分`DialogFragment`，活动会`Action<DateTime>`，随后会显示在日期`TextView`承载活动：

```csharp
[Activity(Label = "@string/app_name", MainLauncher = true, Icon = "@drawable/icon")]
public class MainActivity : Activity
{
    TextView _dateDisplay;
    Button _dateSelectButton;

    protected override void OnCreate(Bundle bundle)
    {
        base.OnCreate(bundle);
        SetContentView(Resource.Layout.Main);

        _dateDisplay = FindViewById<TextView>(Resource.Id.date_display);
        _dateSelectButton = FindViewById<Button>(Resource.Id.date_select_button);
        _dateSelectButton.Click += DateSelect_OnClick;
    }

    void DateSelect_OnClick(object sender, EventArgs eventArgs)
    {
        DatePickerFragment frag = DatePickerFragment.NewInstance(delegate(DateTime time)
                                                                 {
                                                                     _dateDisplay.Text = time.ToLongDateString();
                                                                 });
        frag.Show(FragmentManager, DatePickerFragment.TAG);
    }
}
```


## <a name="summary"></a>总结

此示例介绍了如何显示`DatePicker`为弹出模式对话框的 Android 活动一部分的小组件。 它提供示例 DialogFragment 实现，并讨论`IOnDateSetListener`接口。 此示例还演示了如何 DialogFragment 可能与主机的活动来显示所选的日期进行交互。


## <a name="related-links"></a>相关链接

- [DialogFragment](https://developer.xamarin.com/api/type/Android.App.DialogFragment/)
- [DatePicker](https://developer.xamarin.com/api/type/Android.Widget.DatePicker/)
- [DatePickerDialog](https://developer.xamarin.com/api/type/Android.App.DatePickerDialog/)
- [DatePickerDialog.IOnDateSetListener](https://developer.xamarin.com/api/type/Android.App.DatePickerDialog+IOnDateSetListener/)
- [选择日期](https://github.com/xamarin/recipes/tree/master/Recipes/android/controls/datepicker/select_a_date)
