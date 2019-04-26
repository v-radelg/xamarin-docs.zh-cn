---
title: 第 8 章的摘要。 代码和 XAML 协调工作
description: 使用 Xamarin.Forms 创建移动应用：第 8 章的摘要。 代码和 XAML 协调工作
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 5970DEEB-1FC9-4F78-B4F6-D403E16D22ED
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
ms.openlocfilehash: 75f153499edb6d979f9a0269a1439eaf8ca53878
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61334231"
---
# <a name="summary-of-chapter-8-code-and-xaml-in-harmony"></a>第 8 章的摘要。 代码和 XAML 协调工作

[![下载示例](~/media/shared/download.png)下载示例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08)

这一章更深入地探讨了 XAML，特别是代码和 XAML 的交互方式。

## <a name="passing-arguments"></a>传递自变量

一般情况下，在 XAML 中实例化的类必须具有公共无参数构造函数;生成的对象初始化通过属性设置。 但是，有两种方法可实例化并初始化对象。

尽管这些是通用的技术，但主要与 MVVM 视图模型结合使用。

### <a name="constructors-with-arguments"></a>带参数的构造函数

[ **ParameteredConstructorDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/ParameteredConstructorDemo)示例演示如何使用`x:Arguments`标记指定构造函数自变量。 这些自变量必须分隔元素标记，该值指示参数的类型。 对于基本的.NET 数据类型，以下标记有：

- `x:Object`
- `x:Boolean`
- `x:Byte`
- `x:Int16`
- `x:Int32`
- `x:Int64`
- `x:Single`
- `x:Double`
- `x:Decimal`
- `x:Char`
- `x:String`
- `x:TimeSpan`
- `x:Array`
- `x:DateTime`

### <a name="can-i-call-methods-from-xaml"></a>可以从 XAML 调用方法？

[ **FactoryMethodDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/FactoryMethodDemo)示例演示如何使用`x:FactoryMethod`元素可指定调用以创建对象的工厂方法。 此类的工厂方法必须是公共且静态的并且它必须创建在其中定义的类型的对象。 (例如[ `Color.FromRgb` ](xref:Xamarin.Forms.Color.FromRgb(System.Double,System.Double,System.Double))方法符合条件，因为它是公共且静态的并且返回类型的值`Color`。)工厂方法的参数内指定`x:Arguments`标记。

## <a name="the-xname-attribute"></a>X:name 特性

`x:Name`属性允许在 XAML 提供一个名称中实例化的对象。 这些名称的规则是与 C# 变量名称相同。 以下的返回`InitializeComponent`调用的构造函数中，代码隐藏文件可以引用这些名称以访问相应的 XAML 元素。 名称实际转换到生成的分部类中的私有字段 XAML 分析器。

[ **XamlClock** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/XamlClock)示例演示如何使用`x:Name`若要允许代码隐藏文件保留两个`Label`更新为当前日期和时间的 XAML 中定义的元素。

相同的名称不能用于同一页面上的多个元素。 这是一个特定的问题，如果您使用`OnPlatform`创建并行命名对象，为每个平台。 [ **PlatformSpecificLabele** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/PlatformSpecificLabels)示例演示如何执行一些类似的操作的更好方法。

## <a name="custom-xaml-based-views"></a>自定义基于 XAML 的视图

有几种方法，以避免重复在 XAML 中的标记。 通常使用以下方法将创建新的基于 XAML 的派生的类从[ `ContentView` ](xref:Xamarin.Forms.ContentView)。 此方法进行了演示[ **ColorViewList** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/ColorViewList)示例。 `ColorView`类派生自`ContentView`若要显示特定颜色，且其名称，而`ColorViewListPage`类派生自`ContentPage`像往常一样，显式创建的 17 实例`ColorView`。

访问`ColorView`XAML 中的类需要另一个 XML 命名空间声明，通常名为`local`中相同的程序集的类。

## <a name="events-and-handlers"></a>事件和处理程序

事件可以分配给事件处理程序中 XAML，但必须在代码隐藏文件中实现的事件处理程序本身。 [ **XamlKeypad** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/XamlKeypad)演示了如何构建 XAML 中的键盘用户界面以及如何实现`Clicked`代码隐藏文件中的处理程序。

## <a name="tap-gestures"></a>点击手势

任何`View`对象可以获取触控输入并从该输入生成事件。 `View`类定义[ `GestureRecognizers` ](xref:Xamarin.Forms.View.GestureRecognizers)可以包含一个或多个派生的类实例的集合属性[ `GestureRecognizer` ](xref:Xamarin.Forms.GestureRecognizer)。

[ `TapGestureRecognizer` ](xref:Xamarin.Forms.TapGestureRecognizer)生成[ `Tapped` ](xref:Xamarin.Forms.TapGestureRecognizer.Tapped)事件。 [ **MonkeyTap** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/MonkeyTap)程序演示了如何将附加`TapGestureRecognizer`对象到四个`BoxView`元素来创建一个冒充的游戏：

[![Monkey 分流点的三个屏幕截图](images/ch08fg07-small.png "模拟游戏")](images/ch08fg07-large.png#lightbox "模拟游戏")

但**MonkeyTap**程序真正需要的声音。 (请参阅[下一章](chapter09.md)。)

## <a name="related-links"></a>相关链接

- [第 8 章全文 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch08-Apr2016.pdf)
- [第 8 章示例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08)
- [第 8 章F#示例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/FS/XamlKeypad)
- [在 XAML 中传递自变量](~/xamarin-forms/xaml/passing-arguments.md)
