---
title: 第 18 章的摘要。 MVVM
description: 使用 Xamarin.Forms 创建移动应用：第 18 章的摘要。 MVVM
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 6A774510-7709-4F60-8EF5-29D478176F8F
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: 6379bafb8c879237171951756441d1227f65b825
ms.sourcegitcommit: 495680e74c72e7c570e68cde95d3d3643b1fcc8a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/02/2019
ms.locfileid: "58870217"
---
# <a name="summary-of-chapter-18-mvvm"></a>第 18 章的摘要。 MVVM

[![Download 示例](~/media/shared/download.png) 下载示例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18)

构建应用程序的最佳方法之一是通过将用户界面与基础代码，这有时称为分离*业务逻辑*。 存在几种方法，但为基于 XAML 的环境量身定制的那个被称为模型-视图-视图模型或 MVVM。

## <a name="mvvm-interrelationships"></a>MVVM 的相互关系

MVVM 应用程序具有三个层：

- 该模型可提供基础数据，有时通过文件或 web 访问
- 该视图是用户界面或表示层，在 XAML 中通常实现
- ViewModel 连接模型和视图

该模型是未知的 ViewModel 和 ViewModel 是未知的视图。 这三层通常连接到对方使用以下机制：

![视图、 ViewModel 和视图](images/ch18fg03.png "MVVM")

在许多较小的程序 （和更大的），通常该模型不存在或其功能已集成到 ViewModel。

## <a name="viewmodels-and-data-binding"></a>Viewmodel 和数据绑定

若要参与数据绑定，ViewModel 必须能够的 ViewModel 的属性已更改时通知视图。 通过实现的 ViewModel 实现这[ `INotifyPropertyChanged` ](xref:System.ComponentModel.INotifyPropertyChanged)接口中`System.ComponentModel`命名空间。 这是.NET 而不是 Xamarin.Forms 的一部分。 (通常 Viewmodel 尝试维护平台独立性。)

`INotifyPropertyChanged`接口声明名为的单个事件[ `PropertyChanged` ](xref:System.ComponentModel.INotifyPropertyChanged) ，该值指示已更改的属性。

### <a name="a-viewmodel-clock"></a>ViewModel 时钟

[ `DateTimeViewModel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/DateTimeViewModel.cs)中[ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit)库定义的类型属性`DateTime`更改基于计时器。 类实现`INotifyPropertyChanged`，并激发`PropertyChanged`事件每当`DateTime`属性更改。

[ **MvvmClock** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18/MvvmClock)示例实例化此 ViewModel 并使用数据绑定到 ViewModel 显示更新的日期和时间信息。

### <a name="interactive-properties-in-a-viewmodel"></a>在 ViewModel 中的交互式属性

如所示，ViewModel 中的属性可以是更具交互性[ `SimpleMultiplierViewModel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter18/SimpleMultiplier/SimpleMultiplier/SimpleMultiplier/SimpleMultiplierViewModel.cs)类，该类是一部分的[ **SimpleMultiplier** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18/SimpleMultiplier)示例。 数据绑定提供了两个被乘数和乘数值`Slider`元素，并显示与产品`Label`。 但是，您可以对在 XAML 中的 ViewModel 或代码隐藏文件没有后续更改与此用户界面进行广泛的更改。

### <a name="a-color-viewmodel"></a>颜色 ViewModel

[ `ColorViewModel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ColorViewModel.cs)中[ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit)库集成 RGB 和 HSL 颜色模型。 中提供其演示[ **HslSliders** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18/HslSliders)示例：

[![三个屏幕截图 TK](images/ch18fg08-small.png "HSL 颜色模型")](images/ch18fg08-large.png#lightbox "HSL 颜色模型")

### <a name="streamlining-the-viewmodel"></a>简化 ViewModel

Viewmodel 中的代码可以通过定义简化`OnPropertyChanged`方法使用[ `CallerMemberName` ](xref:System.Runtime.CompilerServices.CallerMemberNameAttribute)属性，它会自动获取调用的属性名称。 [ `ViewModelBase` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ViewModelBase.cs)类[ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit)库就是如此，并为 Viewmodel 提供基类。

## <a name="the-command-interface"></a>命令界面

MVVM 适用于数据绑定和数据绑定处理的属性，因此 MVVM 似乎谈到处理是在不完善`Clicked`的事件`Button`或`Tapped`事件的`TapGestureRecognizer`。 若要允许 Viewmodel 来处理此类事件，Xamarin.Forms 支持*命令界面*。

命令界面表现在`Button`与两个公共属性：

- [`Command`](xref:Xamarin.Forms.Button.Command) 类型的[ `ICommand` ](xref:System.Windows.Input.ICommand) (在中定义`System.Windows.Input`命名空间)
- [`CommandParameter`](xref:Xamarin.Forms.Button.CommandParameter) 类型 `Object`

若要支持命令接口，ViewModel 必须定义类型的属性`ICommand`，然后数据绑定到`Command`属性的`Button`。 `ICommand`接口声明两个方法和一个事件：

- [ `Execute` ](xref:System.Windows.Input.ICommand.Execute(System.Object))用类型自变量的方法 `object`
- 一个[ `CanExecute` ](xref:System.Windows.Input.ICommand.CanExecute(System.Object))方法的类型参数替换`object`返回 `bool`
- 一个[ `CanExecuteChanged` ](xref:System.Windows.Input.ICommand.CanExecuteChanged)事件

在内部，ViewModel 设置类型的每个属性`ICommand`实现的类的实例`ICommand`接口。 通过数据绑定`Button`最初调用`CanExecute`方法，并禁用其自身，如果该方法返回`false`。 它还将设置的处理程序`CanExecuteChanged`事件并调用`CanExecute`每当触发该事件。 如果`Button`已启用，它会调用`Execute`方法只要`Button`单击。

你可能妥协 Xamarin.Forms 中，一些 Viewmodel，这些可能已支持命令接口。 为新的 Viewmodel 旨在仅与 Xamarin.Forms 一起使用，提供了 Xamarin.Forms [ `Command` ](xref:Xamarin.Forms.Command)类和一个[ `Command<T>` ](xref:Xamarin.Forms.Command`1)类实现`ICommand`接口。 泛型类型是参数的类型`Execute`和`CanExecute`方法。

### <a name="simple-method-executions"></a>简单的方法执行

[ **PowersOfThree** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18/PowersOfThree)示例演示如何使用 ViewModel 中的命令接口。 [ `PowersViewModel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter18/PowersOfThree/PowersOfThree/PowersOfThree/PowersViewModel.cs)类定义的类型的两个属性`ICommand`还定义它将传递到的最简单的两个私有属性[`Command`构造函数](xref:Xamarin.Forms.Command.%23ctor(System.Action))。 该程序包含到此 ViewModel 中的数据绑定`Command`的两个属性`Button`元素。

`Button`可以轻松地替换元素`TapGestureRecognizer`在 XAML 中具有无需更改代码的对象。

### <a name="a-calculator-almost"></a>一个计算器，几乎

[ **AddingMachine** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18/AddingMachine)示例使同时使用`Execute`并`CanExecute`方法的`ICommand`。 它使用[ `AdderViewModel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/AdderViewModel.cs)类[ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/AdderViewModel.cs)库。 ViewModel 包含 6 个属性类型的`ICommand`。 这些从初始化[`Command`构造函数](xref:Xamarin.Forms.Command.%23ctor(System.Action))并[`Command`构造函数](xref:Xamarin.Forms.Command.%23ctor(System.Action,System.Func{System.Boolean}))的`Command`并[`Command<T>`构造函数](https://docs.microsoft.com/dotnet/api/xamarin.forms.command.-ctor?view=xamarin-forms#Xamarin_Forms_Command__ctor_System_Action_System_Object__System_Func_System_Object_System_Boolean__)`Command<T>`。 加法机数字键绑定到的属性初始化`Command<T>`，和一个`string`自变量`Execute`和`CanExecute`标识特定密钥。

## <a name="viewmodels-and-the-application-lifecycle"></a>Viewmodel 和应用程序生命周期

`AdderViewModel`中使用**AddingMachine**示例还定义了两个方法名为`SaveState`和`RestoreState`。 和在其进入睡眠状态时再次启动时，将从应用程序中调用这些方法。



## <a name="related-links"></a>相关链接

- [第 18 章全文 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch18-Apr2016.pdf)
- [第 18 章示例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18)
- [《使用 Xamarin.Forms 的企业应用程序模式》电子书](~/xamarin-forms/enterprise-application-patterns/index.md)
