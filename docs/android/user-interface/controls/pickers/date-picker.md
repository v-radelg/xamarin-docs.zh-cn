---
title: 日期选取器
description: 使用 DatePickerDialog 和 DialogFragment 选择日历日期
ms.prod: xamarin
ms.assetid: F2BCD8D4-8957-EA53-C5A8-6BB603ADB47B
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 01/22/2018
ms.openlocfilehash: b54a0795dee0bd7a02b419da497f9a1b3f3ee7ff
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029219"
---
# <a name="android-date-picker"></a>Android 日期选取器

## <a name="overview"></a>概述

有些情况下，用户必须在 Android 应用程序中输入数据。 为此，Android 框架提供了[`DatePicker`](xref:Android.Widget.DatePicker)小组件和[`DatePickerDialog`](xref:Android.App.DatePickerDialog) 。 `DatePicker` 允许用户在设备和应用程序之间的一致界面中选择年、月和日。 `DatePickerDialog` 是在对话框中封装 `DatePicker` 的帮助器类。

新式 Android 应用程序应在[`DialogFragment`](xref:Android.App.DialogFragment)中显示 `DatePickerDialog`。 这将允许应用程序将 DatePicker 显示为 popup 对话框或嵌入到活动中。 此外，`DialogFragment` 还将管理对话框的生命周期和显示，从而减少必须实现的代码量。

本指南将演示如何使用包装在 `DialogFragment`中的 `DatePickerDialog`。 当用户单击活动上的按钮时，示例应用程序会将 `DatePickerDialog` 显示为模式对话框。 用户设置日期后，将使用所选日期更新 `TextView`。

["选取日期" 按钮的![屏幕截图，后面是日期选取器对话框](date-picker-images/image-01-sml.png)](date-picker-images/image-01.png#lightbox)

## <a name="requirements"></a>要求

本指南的示例应用程序面向 Android 4.1 （API 级别）
16) 或更高版本，但适用于 Android 3.0 （API 级别11或更高版本）。 可以支持早期版本的 Android，并向项目中添加 Android 支持库 v4 和一些代码更改。

## <a name="using-the-datepicker"></a>使用 DatePicker

此示例将扩展 `DialogFragment`。 子类将托管并显示 `DatePickerDialog`：

![特写日期选取器对话框](date-picker-images/image-02.png)

当用户选择日期并单击 **"确定"** 按钮时，`DatePickerDialog` 将[`IOnDateSetListener.OnDateSet`](xref:Android.App.DatePickerDialog.IOnDateSetListener.OnDateSet*)调用方法。
此接口由宿主 `DialogFragment`实现。 如果用户单击 "**取消**" 按钮，则片段和对话框会自行关闭。

`DialogFragment` 可以通过多种方式将所选日期返回到宿主活动：

1. **调用方法或设置属性**&ndash; 活动可以提供专门用于设置此值的属性或方法。

2. **引发事件**&ndash; `DialogFragment` 可以定义在调用 `OnDateSet` 时将引发的事件。

3. **使用 `Action`** &ndash; `DialogFragment` 可以调用 `Action<DateTime>` 以显示活动中的日期。 在实例化 `DialogFragment`时，该活动将提供 `Action<DateTime`。 此示例将使用第三种方法，并要求活动向 `DialogFragment`提供 `Action<DateTime>`。

### <a name="extending-dialogfragment"></a>扩展 DialogFragment

显示 `DatePickerDialog` 的第一步是将 `DialogFragment` 子类，并使其实现 `IOnDateSetListener` 接口：

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

调用 `NewInstance` 方法来实例化新的 `DatePickerFragment`。 此方法采用一个 `Action<DateTime>`，当用户单击 `DatePickerDialog`中的 **"确定"** 按钮时，将调用该。

显示片段时，Android 将调用方法 `OnCreateDialog`。 此方法将创建一个新的 `DatePickerDialog` 对象，并使用当前日期和回调对象（该对象是 `DatePickerFragment`的当前实例）对其进行初始化。

> [!NOTE]
> 请注意，调用 `IOnDateSetListener.OnDateSet` 的月份值介于0到11（而不是1到12）的范围内。 月份中的日期将在1到31的范围内（具体取决于选择的月份）。

### <a name="showing-the-datepickerfragment"></a>显示 DatePickerFragment

现在已经实现了 `DialogFragment`，本节将讨论如何在活动中使用片段。 在本指南附带的示例应用程序中，活动将使用 `NewInstance` 工厂方法来实例化 `DialogFragment`，然后将其显示为调用 `DialogFragment.Show`。 在实例化 `DialogFragment`的过程中，活动将传递 `Action<DateTime>`，这会在活动承载的 `TextView` 中显示日期：

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

本示例讨论了如何将 `DatePicker` 小组件显示为作为 Android 活动一部分的弹出模式对话框。 它提供了一个示例 DialogFragment 实现并讨论了 `IOnDateSetListener` 接口。 此示例还演示了 DialogFragment 可能如何与主机活动交互以显示选定的日期。

## <a name="related-links"></a>相关链接

- [DialogFragment](xref:Android.App.DialogFragment)
- [DatePicker](xref:Android.Widget.DatePicker)
- [DatePickerDialog](xref:Android.App.DatePickerDialog)
- [DatePickerDialog.IOnDateSetListener](xref:Android.App.DatePickerDialog.IOnDateSetListener)
- [选择日期](https://github.com/xamarin/recipes/tree/master/Recipes/android/controls/datepicker/select_a_date)
