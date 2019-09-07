---
title: 第 15 章的摘要。 交互式接口
description: 用 Xamarin 创建移动应用程序：第 15 章的摘要。 交互式接口
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: F54E86F4-1CDA-474E-9B09-242060C2C13D
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: 1c30f87b9173d2ca4de0b2d91ad13145031e9b0a
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70760761"
---
# <a name="summary-of-chapter-15-the-interactive-interface"></a>第 15 章的摘要。 交互式接口

[![下载示例](~/media/shared/download.png)下载示例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15)

本章探讨了八个`View`允许与用户交互的派生类。

## <a name="view-overview"></a>查看概述

Xamarin.Forms 包含 20 可实例化类派生自`View`但不是`Layout`。 已在上一章节中讨论的这些六个：

- `Label`：[**第2章。应用剖析**](chapter02.md)
- `BoxView`：[**第3章：滚动堆栈**](chapter03.md)
- `Button`：[**第6章。按钮单击事件**](chapter06.md)
- `Image`：[**第13章：位图**](chapter13.md)
- `ActivityIndicator`：[**第13章：位图**](chapter13.md)
- `ProgressBar`：[**第14章：AbsoluteLayout**](chapter14.md)

这一章中的八个视图有效地允许用户与基本.NET 数据类型进行交互：

|数据类型|Views|
|--- |--- |
|`Double`|[`Slider`](xref:Xamarin.Forms.Slider), [`Stepper`](xref:Xamarin.Forms.Stepper)|
|`Boolean`|[`Switch`](xref:Xamarin.Forms.Switch)|
|`String`|[`Entry`](xref:Xamarin.Forms.Entry), [`Editor`](xref:Xamarin.Forms.Editor), [`SearchBar`](xref:Xamarin.Forms.SearchBar)|
|`DateTime`|[`DatePicker`](xref:Xamarin.Forms.DatePicker), [`TimePicker`](xref:Xamarin.Forms.TimePicker)|

您可以将这些视图作为基础数据类型的可视交互式表示形式。 这一概念在下一章中，探讨了更[**第 16 章。数据绑定**](chapter16.md)。

以下章节中介绍的剩余的六个视图：

- `WebView`：[**第16章。数据绑定**](chapter16.md)
- `Picker`：[**第19章。集合视图**](chapter19.md)
- `ListView`：[**第19章。集合视图**](chapter19.md)
- `TableView`：[**第19章。集合视图**](chapter19.md)
- `Map`：[**第28章。位置和地图**](chapter28.md)
- `OpenGLView`：本书未涵盖（并且不支持 Windows 平台）

## <a name="slider-and-stepper"></a>滑块和分档器

这两[ `Slider` ](xref:Xamarin.Forms.Slider)并[ `Stepper` ](xref:Xamarin.Forms.Stepper)允许用户从一系列选择的数字值。 `Slider`是一个连续范围时`Stepper`涉及离散值。

### <a name="slider-basics"></a>滑块基础知识

[ `Slider` ](xref:Xamarin.Forms.Slider)是水平条表示一系列值最小值在左侧到右侧的最大。 它定义了三个公共属性：

- [`Value`](xref:Xamarin.Forms.Slider.Value) 类型的`double`，默认值为 0
- [`Minimum`](xref:Xamarin.Forms.Slider.Minimum) 类型的`double`，默认值为 0
- [`Maximum`](xref:Xamarin.Forms.Slider.Maximum) 类型的`double`，默认值为 1

可绑定属性的支持这些属性，以确保它们一致：

- 对于所有三个属性， [ `coerceValue` ](xref:Xamarin.Forms.BindableProperty.CoerceValueDelegate)方法指定的可绑定属性可确保`Value`之间`Minimum`和`Maximum`。
- [ `validateValue` ](xref:Xamarin.Forms.BindableProperty.ValidateValueDelegate)方法`MinimumProperty`返回`false`如果`Minimum`被设置为一个值大于或等于`Maximum`，以及类似的`MaximumProperty`。 返回`false`从`validateValue`方法使`ArgumentException`引发。

`Slider` 激发[ `ValueChanged` ](xref:Xamarin.Forms.Slider.ValueChanged)具有事件[ `ValueChangedEventArgs` ](xref:Xamarin.Forms.ValueChangedEventArgs)自变量时`Value`属性更改，以编程方式或当用户操作`Slider`。

[ **SliderDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/SliderDemo)示例演示如何将简单的`Slider`。

### <a name="common-pitfalls"></a>常见缺陷

在代码和 XAML，`Minimum`和`Maximum`属性设置中指定的顺序。 请务必初始化这些属性，以便`Maximum`始终是大于`Minimum`。 否则，将引发异常。

正在初始化`Slider`属性可能会导致`Value`要更改属性和`ValueChanged`事件被触发。 您应该确保`Slider`事件处理程序不会访问尚未在页初始化期间创建的视图。

`ValueChanged`期间不会激发事件`Slider`初始化除非`Value`属性更改。 您可以调用`ValueChanged`直接从代码处理程序。

### <a name="slider-color-selection"></a>滑块的颜色选择

[ **RgbSliders** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/RgbSliders)程序包含三种`Slider`允许您以交互方式选择一种颜色通过指定其 RGB 值的元素：

[![R G B 滑块的三个屏幕截图](images/ch15fg03-small.png "RGB 滑块")](images/ch15fg03-large.png#lightbox "RGB 滑块")

[ **TextFade** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/TextFade)示例使用两个`Slider`移动两个元素`Label`跨元素`AbsoluteLayout`和淡入到另一个。

### <a name="the-stepper-difference"></a>分档器差异

[ `Stepper` ](xref:Xamarin.Forms.Stepper)定义的相同的属性和事件作为`Slider`但`Maximum`属性初始化为 100 和`Stepper`定义了第四个属性：

- [`Increment`](xref:Xamarin.Forms.Stepper.Increment) 类型的`double`，将初始化为 1

直观地`Stepper`包含两个按钮标记为 **&ndash;** 并 **+** 。 按下 **&ndash;** 减少`Value`由`Increment`为最小值`Minimum`。 按下 **+** 增加`Value`由`Increment`最大为`Maximum`。

这可通过演示[ **StepperDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/StepperDemo)示例。

## <a name="switch-and-checkbox"></a>开关和复选框

[ `Switch` ](xref:Xamarin.Forms.Switch)允许用户指定一个布尔值。

### <a name="switch-basics"></a>切换基础知识

您可以看到，`Switch`包含可以禁用和启用切换功能。 类定义了一个属性：

- [`IsToggled`](xref:Xamarin.Forms.Switch.IsToggled) 类型 `bool`

`Switch` 定义一个事件：

- [`Toggled`](xref:Xamarin.Forms.Switch.Toggled) 伴随[ `ToggledEventArgs` ](xref:Xamarin.Forms.ToggledEventArgs)对象，当触发`IsToggled`属性更改。

[ **SwitchDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/SwitchDemo)程序演示`Switch`。

### <a name="a-traditional-checkbox"></a>传统的复选框

一些开发人员可能首选更传统`CheckBox`到`Switch`。 [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)库包含`CheckBox`派生的类`ContentView`。 `CheckBox` 由实现[CheckBox.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/CheckBox.xaml)并[CheckBox.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/CheckBox.xaml.cs)文件。 `CheckBox` 定义三个属性 (`Text`， `FontSize`，并`IsChecked`) 和一个`CheckedChanged`事件。

[ **CheckBoxDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/CheckBoxDemo)示例演示了这`CheckBox`。

## <a name="typing-text"></a>键入文本

Xamarin.Forms 定义了三个视图，让用户输入和编辑文本：

- [`Entry`](xref:Xamarin.Forms.Entry) 为单个文本行
- [`Editor`](xref:Xamarin.Forms.Editor) 对于多行文本
- [`SearchBar`](xref:Xamarin.Forms.SearchBar) 对于单个用于搜索的文本行。

`Entry` 并`Editor`派生自[ `InputView` ](xref:Xamarin.Forms.InputView)，它派生`View`。 `SearchBar` 直接派生`View`。

### <a name="keyboard-and-focus"></a>键盘和焦点

在手机和平板电脑，而无需物理键盘`Entry`， `Editor`，和`SearchBar`所有元素会都导致弹出的虚拟键盘。 此键盘在屏幕上存在相关输入焦点。 同时，必须具有一个视图，其[ `IsVisible` ](xref:Xamarin.Forms.VisualElement.IsVisible)和[ `IsEnabled` ](xref:Xamarin.Forms.VisualElement.IsEnabled)属性设置为`true`获取输入的焦点。

具有输入焦点所涉及的两种方法，一个只读的属性和两个事件。 这些都被定义为通过`VisualElement`:

- [ `Focus` ](xref:Xamarin.Forms.VisualElement.Focus)方法尝试将输入的焦点设置到一个元素并返回`true`如果成功，则
- [ `Unfocus` ](xref:Xamarin.Forms.VisualElement.Unfocus)方法将输入的焦点从此元素中
- [ `IsFocused` ](xref:Xamarin.Forms.VisualElement.IsFocused)只读属性指示该元素具有输入焦点
- [ `Focused` ](xref:Xamarin.Forms.VisualElement.Focused)事件指示当元素获得输入的焦点
- [ `Unfocused` ](xref:Xamarin.Forms.VisualElement.Unfocused)事件指示在元素丢失输入的焦点时

### <a name="choosing-the-keyboard"></a>选择键盘

[ `InputView` ](xref:Xamarin.Forms.InputView)从其`Entry`和`Editor`派生定义只有一个属性：

- [`Keyboard`](xref:Xamarin.Forms.InputView.Keyboard) 类型 [`Keyboard`](xref:Xamarin.Forms.Keyboard)

此设置指示键盘显示的类型。 某些键盘进行了优化的 Uri 或数字。

`Keyboard`类允许定义使用静态键盘[ `Keyboard.Create` ](xref:Xamarin.Forms.Keyboard.Create(Xamarin.Forms.KeyboardFlags))方法的类型参数替换[ `KeyboardFlags` ](xref:Xamarin.Forms.KeyboardFlags)，具有以下位标志的枚举：

- `None` 设置为 0
- [`CapitalizeSentence`](xref:Xamarin.Forms.KeyboardFlags.CapitalizeSentence) 设置为 1
- [`Spellcheck`](xref:Xamarin.Forms.KeyboardFlags.Spellcheck) 设置为 2
- [`Suggestions`](xref:Xamarin.Forms.KeyboardFlags.Suggestions) 将设置为 4
- [`All`](xref:Xamarin.Forms.KeyboardFlags.All) 设置为 \xFFFFFFFF

使用多行时[ `Editor` ](xref:Xamarin.Forms.Editor)何时需要一段或多个文本调用`Keyboard.Create`是选择键盘的好方法。 为单线条[ `Entry` ](xref:Xamarin.Forms.Entry)，以下静态只读属性的`Keyboard`非常有用：

- [`Default`](xref:Xamarin.Forms.Keyboard.Default)
- [`Text`](xref:Xamarin.Forms.Keyboard.Text)
- [`Chat`](xref:Xamarin.Forms.Keyboard.Chat)
- [`Url`](xref:Xamarin.Forms.Keyboard.Url)
- [`Email`](xref:Xamarin.Forms.Keyboard.Email)
- [`Telephone`](xref:Xamarin.Forms.Keyboard.Telephone)
- [`Numeric`](xref:Xamarin.Forms.Keyboard.Numeric) 对于使用或不带小数点的正数。

[ `KeyboardTypeConverter` ](xref:Xamarin.Forms.KeyboardTypeConverter)允许在 XAML 中指定这些属性，如所示[ **EntryKeyboards** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/EntryKeyboards)程序。

### <a name="entry-properties-and-events"></a>项属性和事件

单行[ `Entry` ](xref:Xamarin.Forms.Entry)定义以下属性：

- [`Text`](xref:Xamarin.Forms.Entry.Text) 类型的`string`，在显示的文本 `Entry`
- [`TextColor`](xref:Xamarin.Forms.Entry.TextColor) 类型 `Color`
- [`FontFamily`](xref:Xamarin.Forms.Entry.FontFamily) 类型 `string`
- [`FontSize`](xref:Xamarin.Forms.Entry.FontSize) 类型 `double`
- [`FontAttributes`](xref:Xamarin.Forms.Entry.FontAttributes) 类型 `FontAttributes`
- [`IsPassword`](xref:Xamarin.Forms.Entry.IsPassword) 类型的`bool`，这将导致将要屏蔽的字符
- [`Placeholder`](xref:Xamarin.Forms.Entry.Placeholder) 类型的`string`，使彩色中显示的文本`Entry`键入任何内容之前
- [`PlaceholderColor`](xref:Xamarin.Forms.Entry.PlaceholderColor) 类型 `Color`

`Entry`还定义了两个事件：

- [`TextChanged`](xref:Xamarin.Forms.Entry.TextChanged) 与[ `TextChangedEventArgs` ](xref:Xamarin.Forms.TextChangedEventArgs)对象，每当激发`Text`属性更改
- [`Completed`](xref:Xamarin.Forms.Entry.Completed)当用户已完成，并且消除键盘激发。 用户以特定于平台的方式指示完成

[ **QuadraticEquations** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/QuadaticEquations)示例演示了这两个事件。

### <a name="the-editor-difference"></a>编辑器差异

多行[ `Editor` ](xref:Xamarin.Forms.Editor)定义相同`Text`并`Font`属性作为`Entry`不包括其他属性。 `Editor` 此外定义了两个属性与相同`Entry`。

[**JustNotes** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/JustNotes)是保存并还原的内容的自由格式说明采用程序`Editor`。

### <a name="the-searchbar"></a>SearchBar

[ `SearchBar` ](xref:Xamarin.Forms.SearchBar)不是派生自`InputView`，因此它不具有`Keyboard`属性。 但它具有所有`Text`， `Font`，并`Placeholder`属性的`Entry`定义。 此外，`SearchBar`定义三个其他属性：

- [`CancelButtonColor`](xref:Xamarin.Forms.SearchBar.CancelButtonColor) 类型 `Color`
- [`SearchCommand`](xref:Xamarin.Forms.SearchBar.SearchCommand) 类型的[ `ICommand` ](xref:System.Windows.Input.ICommand)用于数据绑定和 MVVM
- [`SearchCommandParameter`](xref:Xamarin.Forms.SearchBar.SearchCommandParameter) 类型的`Object`，以用于 `SearchCommand`

特定于平台的取消按钮擦除文本。 `SearchBar`还包含一个特定于平台的搜索按钮。 按这些按钮中引发的两个事件之一的`SearchBar`定义：

- [`TextChanged`](xref:Xamarin.Forms.SearchBar.TextChanged) 伴随[ `TextChangedEventArgs` ](xref:Xamarin.Forms.TextChangedEventArgs)对象
- [`SearchButtonPressed`](xref:Xamarin.Forms.SearchBar.SearchButtonPressed)

[ **SearchBarDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/SearchBarDemo)示例演示`SearchBar`。

## <a name="date-and-time-selection"></a>日期和时间选择

[ `DatePicker` ](xref:Xamarin.Forms.DatePicker)并[ `TimePicker` ](xref:Xamarin.Forms.TimePicker)视图实现特定于平台的控制，以使用户能够指定日期或时间。

### <a name="the-datepicker"></a>DatePicker

[`DatePicker`](xref:Xamarin.Forms.DatePicker) 定义了四个属性：

- [`MinimumDate`](xref:Xamarin.Forms.DatePicker.MinimumDate) 类型的`DateTime`，将初始化为 1900 年 1 月 1 日
- [`MaximumDate`](xref:Xamarin.Forms.DatePicker.MaximumDate) 类型的`DateTime`，将初始化为 2100 年 12 月 31，
- [`Date`](xref:Xamarin.Forms.DatePicker.Date) 类型的`DateTime`，将初始化为 `DateTime.Today`
- [`Format`](xref:Xamarin.Forms.DatePicker.Format) 类型的`string`、.NET 格式设置字符串初始化为"d"，短日期模式，从而导致日期显示"7/20/1969"美国太平洋时区中。

可以设置`DateTime`表达为属性元素的属性并使用区域性不变短日期的 XAML 中的属性格式 ("7/20/1969")。   

[ **DaysBetweenDates** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/DaysBetweenDates)示例计算的由用户选择的两个日期之间的天数。

### <a name="the-timepicker-or-is-it-a-timespanpicker"></a>TimePicker （或其 TimeSpanPicker？）

[`TimePicker`](xref:Xamarin.Forms.TimePicker) 定义了两个属性，任何事件：

- [`Time`](xref:Xamarin.Forms.TimePicker.Time) 类型`TimeSpan`而非`DateTime`，自午夜以来经过的指示的时间
- [`Format`](xref:Xamarin.Forms.TimePicker.Format) 类型的`string`、 美国中的格式设置字符串初始化为"t"，从而导致"1:45 PM"时显示的短时间模式的.NET。

[ **SetTimer** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/SetTimer)程序演示了如何使用`TimePicker`计时器指定时间。 只有在将它保存在前台，仅适用于该程序。

**SetTimer**还演示了如何使用[ `DisplayAlert` ](xref:Xamarin.Forms.Page.DisplayAlert(System.String,System.String,System.String))方法`Page`以显示一个警告框。

## <a name="related-links"></a>相关链接

- [第 15 章全文 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch15-Apr2016.pdf)
- [第 15 章示例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15)
- [滑块](~/xamarin-forms/user-interface/slider.md)
- [项](~/xamarin-forms/user-interface/text/entry.md)
- [编辑器](~/xamarin-forms/user-interface/text/editor.md)
- [DatePicker](~/xamarin-forms/user-interface/datepicker.md)
