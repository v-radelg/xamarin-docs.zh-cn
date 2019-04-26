---
title: 第 16 章的摘要。 数据绑定
description: 使用 Xamarin.Forms 创建移动应用：第 16 章的摘要。 数据绑定
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: ED997DB0-C229-4868-A5FB-928703B377D6
author: davidbritch
ms.author: dabritch
ms.date: 07/18/2018
ms.openlocfilehash: df3cee3fe71553b63bf4b050cad141b66ad98a06
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61334168"
---
# <a name="summary-of-chapter-16-data-binding"></a>第 16 章的摘要。 数据绑定

[![下载示例](~/media/shared/download.png)下载示例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16)

> [!NOTE] 
> 此页上的说明表明其中 Xamarin.Forms 已脱离一书中介绍的内容的区域。

程序员通常会发现自己编写事件处理程序检测何时更改一个对象的属性，并使用它来更改另一个对象中属性的值。 可以使用的技术自动执行此过程*数据绑定*。 数据绑定通常在 XAML 中定义，并成为用户界面的定义的一部分。

通常，这些数据绑定到基础数据连接用户界面对象。 这是一种方法，中进行了探讨更[**第 18 章。MVVM**](chapter18.md)。 但是，数据绑定还可以连接两个或多个用户界面元素。 大多数早期的这一章中的数据绑定示例演示此方法。

## <a name="binding-basics"></a>绑定基础知识

在数据绑定中涉及多个属性、 方法和类：

- [ `Binding` ](xref:Xamarin.Forms.Binding)类派生自[ `BindingBase` ](xref:Xamarin.Forms.BindingBase)和封装数据绑定的许多特征
- [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext)属性定义由[ `BindableObject` ](xref:Xamarin.Forms.BindableObject)类
- [ `SetBinding` ](xref:Xamarin.Forms.BindableObject.SetBinding(Xamarin.Forms.BindableProperty,Xamarin.Forms.BindingBase))方法也由定义[ `BindableObject` ](xref:Xamarin.Forms.BindableObject)类
- [ `BindableObjectExtensions` ](xref:Xamarin.Forms.BindableObjectExtensions)类定义了三个其他`SetBinding`方法

以下两个类的绑定支持 XAML 标记扩展：

- [`BindingExtension`](xref:Xamarin.Forms.Xaml.BindingExtension) 支持`Binding`标记扩展
- [`ReferenceExtension`](xref:Xamarin.Forms.Xaml.ReferenceExtension) 支持`x:Reference`标记扩展

在数据绑定中涉及两个接口：

- [`INotifyPropertyChanged`](xref:System.ComponentModel.INotifyPropertyChanged) 在`System.ComponentModel`命名空间是用于实现在属性发生更改时通知
- [`IValueConverter`](xref:Xamarin.Forms.IValueConverter) 用于定义将值从一种类型转换为另一个数据绑定中的较小类

数据绑定连接两个相同的对象或 （更常见） 两个不同的对象的属性。 这两个属性嘿*源*并*目标*。 通常情况下，源属性中的更改会导致在目标属性发生更改，但有时反转方向。 而不考虑：

- *目标*属性必须为后盾 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty)
- *源*属性通常是实现的类的成员 [`INotifyPropertyChanged`](xref:System.ComponentModel.INotifyPropertyChanged)

实现的类`INotifyPropertyChanged`激发[ `PropertyChanged` ](xref:System.ComponentModel.INotifyPropertyChanged.PropertyChanged)属性值更改时事件。 `BindableObject` 实现`INotifyPropertyChanged`并自动触发`PropertyChanged`事件时由属性支持`BindableProperty`更改值，但您可以编写您自己的类实现`INotifyPropertyChanged`而无需派生自`BindableObject`。

## <a name="code-and-xaml"></a>代码和 XAML

[ **OpacityBindingCode** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/OpacityBindingCode)示例演示如何在代码中设置数据绑定：

- 源是`Value`属性 `Slider`
- 目标是`Opacity`属性 `Label`

通过设置连接两个对象`BindingContext`的`Label`对象传递给`Slider`对象。 两个属性连接通过调用[ `SetBinding` ](xref:Xamarin.Forms.BindableObjectExtensions.SetBinding*)上的扩展方法`Label`引用`OpacityProperty`可绑定的属性和`Value`属性`Slider`表示为字符串。

操作`Slider`然后导致`Label`淡入和移出视图。

[ **OpacityBindingXaml** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/OpacityBindingXaml)是与数据绑定在 XAML 中设置的同一个程序。 `BindingContext`的`Label`设置为`x:Reference`标记扩展引用`Slider`，并`Opacity`属性的`Label`设置为`Binding`与标记扩展其[ `Path`](xref:Xamarin.Forms.Binding.Path)属性引用`Value`属性的`Slider`。

## <a name="source-and-bindingcontext"></a>源和 BindingContext

[ **BindingSourceCode** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/BindingSourceCode)示例代码中演示一种替代方法。 一个`Binding`通过设置创建对象[ `Source` ](xref:Xamarin.Forms.Binding.Source)属性设置为`Slider`对象并[ `Path` ](xref:Xamarin.Forms.Binding.Path)属性设置为"Value"。 [ `SetBinding` ](xref:Xamarin.Forms.BindableObject.SetBinding(Xamarin.Forms.BindableProperty,Xamarin.Forms.BindingBase))方法`BindableObject`上调用`Label`对象。

[ `Binding`构造函数](xref:Xamarin.Forms.Binding.%23ctor(System.String,Xamarin.Forms.BindingMode,Xamarin.Forms.IValueConverter,System.Object,System.String,System.Object))可能也使用以定义`Binding`对象。

[ **BindingSourceXaml** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/BindingSourceXaml)示例演示在 XAML 中可比较的方法。 `Opacity`的属性`Label`设置为`Binding`与标记扩展[ `Path` ](xref:Xamarin.Forms.Binding.Path)设置为`Value`属性和[ `Source` ](xref:Xamarin.Forms.Binding.Source)设置为嵌入`x:Reference`标记扩展。

总之，有两种方式引用的绑定源对象：

- 通过`BindingContext`目标属性
- 通过`Source`属性的`Binding`对象本身

如果同时指定两者，第二个优先。 利用`BindingContext`是传播在可视化树。 这是*非常*如果多个目标属性绑定到相同的源对象。

[ **WebViewDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/WebViewDemo)程序演示如何使用此技术[ `WebView` ](xref:Xamarin.Forms.WebView)元素。 两个`Button`向后和向前导航元素继承`BindingContext`从其父对象的引用`WebView`。 `IsEnabled`的两个按钮的属性然后可以通过简单`Binding`目标按钮的标记扩展`IsEnabled`基础设置的属性[ `CanGoBack` ](xref:Xamarin.Forms.WebView.CanGoBack)和[ `CanGoForward`](xref:Xamarin.Forms.WebView.CanGoForward)的只读属性`WebView`。

## <a name="the-binding-mode"></a>绑定模式

设置[ `Mode` ](xref:Xamarin.Forms.BindingBase.Mode)的属性`Binding`成员[ `BindingMode` ](xref:Xamarin.Forms.BindingMode)枚举：

- [`OneWay`](xref:Xamarin.Forms.BindingMode.OneWay) 因此，源属性中的更改会影响目标
- [`OneWayToSource`](xref:Xamarin.Forms.BindingMode.OneWayToSource) 因此，目标属性中的更改会影响源
- [`TwoWay`](xref:Xamarin.Forms.BindingMode.TwoWay) 因此，源和目标中的更改会影响每个其他
- [`Default`](xref:Xamarin.Forms.BindingMode.Default) 若要使用[ `DefaultBindingMode` ](xref:Xamarin.Forms.BindableProperty.DefaultBindingMode)时指定目标`BindableProperty`已创建。 如果未指定，默认值是`OneWay`正常可绑定属性和`OneWayToSource`为只读的可绑定属性。

> [!NOTE]
> `BindingMode`枚举现在还包括`OnTime`仅当绑定上下文更改时并不是在时应用绑定源属性发生更改。

可能需要通常在 MVVM 方案中的数据绑定的目标属性具有`DefaultBindingMode`的`TwoWay`。 这些是：

- `Value` 属性的`Slider`和 `Stepper`
- `IsToggled` 属性 `Switch`
- `Text` 属性的`Entry`， `Editor`，和 `SearchBar`
- `Date` 属性 `DatePicker`
- `Time` 属性 `TimePicker`

[ **BindingModes** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/BindingModes)示例演示包含数据绑定目标的位置的四个绑定模式`FontSize`属性的`Label`，而源`Value`属性的`Slider`。 这样，每个`Slider`来控制相应的字体大小`Label`。 但`Slider`不初始化元素，因为`DefaultBindingMode`的`FontSize`属性是`OneWay`。

[ **ReverseBinding** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/ReverseBinding)示例上设置绑定`Value`属性的`Slider`引用`FontSize`每个属性`Label`。 这似乎是向后，但工作原理更好地在初始化字词`Slider`元素因为`Value`的属性`Slider`具有`DefaultBindingMode`的`TwoWay`。

[![反向绑定的三个屏幕截图](images/ch16fg06-small.png "反向绑定")](images/ch16fg06-large.png#lightbox "反向绑定")

这是类似于如何在 MVVM 中定义的绑定，将使用这种类型的绑定频繁。

## <a name="string-formatting"></a>字符串格式设置

当目标属性属于类型`string`，可以使用[ `StringFormat` ](xref:Xamarin.Forms.BindingBase.StringFormat)属性定义`BindingBase`以源转换为`string`。 设置`StringFormat`属性设置为.NET 格式设置字符串，你将使用静态[ `String.Format` ](xref:System.String.Format(System.String,System.Object))格式显示对象。 时使用此格式设置字符串中标记扩展，请在它两边用单引号因此大括号不会被误解为嵌入的标记扩展。

[ **ShowViewValues** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/ShowViewValues)示例演示如何使用`StringFormat`在 XAML 中。

[ **WhatSizeBindings** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/WhatSizeBindings)示例演示如何使用绑定到显示页面大小`Width`并`Height`的属性`ContentPage`。

## <a name="why-is-it-called-path"></a>为什么它称为"路径"？

[ `Path` ](xref:Xamarin.Forms.Binding.Path)属性的`Binding`因此称为，因为它可以是属性和索引器用句点分隔的一系列。 [ **BindingPathDemos** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/BindingPathDemos)示例显示了几个示例。

## <a name="binding-value-converters"></a>绑定值转换器

不同类型的源和目标属性的绑定时，你可以使用绑定转换器的类型之间进行转换。 这是实现的类[ `IValueConverter` ](xref:Xamarin.Forms.IValueConverter)接口，并包含两个方法： [ `Convert` ](xref:Xamarin.Forms.IValueConverter.Convert(System.Object,System.Type,System.Object,System.Globalization.CultureInfo))将源转换到目标，以及[ `ConvertBack` ](xref:Xamarin.Forms.IValueConverter.ConvertBack(System.Object,System.Type,System.Object,System.Globalization.CultureInfo))若要将目标转换到源。

[ `IntToBoolConverter` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/IntToBoolConverter.cs)类[ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)库是一个示例，用于转换`int`到`bool`。 可通过演示[ **ButtonEnabler** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/ButtonEnabler)示例中，后者只允许`Button`如果至少一个字符键入`Entry`。

[ `BoolToStringConverter` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BoolToStringConverter.cs)类转换`bool`到`string`，并定义两个属性，以指定应为返回什么文本`false`和`true`值。
[ `BoolToColorConverter` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BoolToColorConverter.cs)类似。 [ **SwitchText** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/SwitchText)示例演示了如何使用这些两个转换器在基于浏览器中显示的不同文本`Switch`设置。

泛型[ `BoolToObjectConverter` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BoolToObjectConverter.cs)可以替换`BoolToStringConverter`并`BoolToColorConverter`用作通用`bool`-到-对象的任何类型的转换器。

## <a name="bindings-and-custom-views"></a>绑定和自定义视图

可以简化使用数据绑定的自定义控件。 [ `NewCheckBox.cs` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NewCheckBox.xaml.cs)代码文件定义`Text`， `TextColor`， `FontSize`， `FontAttributes`，和`IsChecked`属性，但没有在所有的控件的视觉对象的逻辑。
相反[ `NewCheckBox.cs.xaml` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NewCheckBox.xaml)文件包含通过数据绑定控件的视觉对象的所有标记上`Label`元素基于代码隐藏文件中定义的属性。

[ **NewCheckBoxDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/NewCheckBoxDemo)示例演示`NewCheckBox`自定义控件。



## <a name="related-links"></a>相关链接

- [第 16 章全文 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch16-Apr2016.pdf)
- [第 16 章示例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16)
- [数据绑定](~/xamarin-forms/app-fundamentals/data-binding/index.md)
