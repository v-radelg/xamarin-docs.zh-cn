---
title: 第 6 章的摘要。 按钮单击事件
description: 使用 Xamarin.Forms 创建移动应用：第 6 章的摘要。 按钮单击事件
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: D4F9C429-A6CF-40FA-AC68-3F149307A5F9
author: davidbritch
ms.author: dabritch
ms.date: 07/18/2018
ms.openlocfilehash: 12c8cdc19f9e6765ca25ade97bcfdbffb7b60381
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61334546"
---
# <a name="summary-of-chapter-6-button-clicks"></a>第 6 章的摘要。 按钮单击事件

[![下载示例](~/media/shared/download.png)下载示例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06)

[ `Button` ](xref:Xamarin.Forms.Button)是允许用户启动命令的视图。 一个`Button`由文本 (和 （可选） 一个图像中所示[的第 13 章、 位图](chapter13.md))。 因此，`Button`定义的属性与相同许多`Label`:

- [`Text`](xref:Xamarin.Forms.Button.Text)
- [`FontFamily`](xref:Xamarin.Forms.Button.FontFamily)
- [`FontSize`](xref:Xamarin.Forms.Button.FontSize)
- [`FontAttributes`](xref:Xamarin.Forms.Button.FontAttributes)
- [`TextColor`](xref:Xamarin.Forms.Button.TextColor)

`Button` 此外定义了三个属性控制其边框的外观，但这些属性和其相互独立的支持是特定于平台：

- [`BorderColor`](xref:Xamarin.Forms.Button.BorderColor) 类型 `Color`
- [`BorderWidth`](xref:Xamarin.Forms.Button.BorderWidth) 类型 `Double`
- [`BorderRadius`](xref:Xamarin.Forms.Button.BorderRadius) 类型 `Double`

`Button` 此外会继承的所有属性`VisualElement`并`View`，其中包括`BackgroundColor`， `HorizontalOptions`，和`VerticalOptions`。

## <a name="processing-the-click"></a>处理单击

`Button`类定义[ `Clicked` ](xref:Xamarin.Forms.Button.Clicked)在用户点击时激发的事件`Button`。 `Click`类型的处理程序是`EventHandler`。 第一个参数是`Button`对象，生成事件; 第二个参数为`EventArgs`对象，它提供任何其他信息。

[ **ButtonLogger** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/ButtonLogger)示例演示如何简单`Clicked`处理。

## <a name="sharing-button-clicks"></a>共享按钮单击

多个`Button`视图可以共享相同`Clicked`处理程序，但该处理程序通常需要确定哪个`Button`负责特定事件。 一种方法是用于存储各个`Button`对象作为字段，并检查哪一个触发此事件处理程序中的。

[ **TwoButtons** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/TwoButtons)示例演示此技术。 该程序还演示了如何设置[ `IsEnabled` ](xref:Xamarin.Forms.VisualElement.IsEnabled)的属性`Button`到`false`时按`Button`不再有效。 禁用`Button`不会生成`Clicked`事件。

## <a name="anonymous-event-handlers"></a>匿名事件处理程序

可以定义`Clicked`处理程序为匿名的 lambda 函数，作为[ **ButtonLambdas** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/ButtonLambdas)示例演示。 但是，匿名处理程序不能共享而无需一些杂乱的反射代码。

## <a name="distinguishing-views-with-ids"></a>具有 Id 区分视图

多个`Button`还可以通过设置区分对象[ `StyleId` ](xref:Xamarin.Forms.Element.StyleId)属性或[ `AutomationId` ](xref:Xamarin.Forms.Element.AutomationId)属性设置为`string`。 此属性定义由`Element`但在 Xamarin.Forms 中不使用它。 它旨在仅由应用程序的程序。

[ **SimplestKeypad** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/SimplestKeypad)示例的数字键盘上的所有 10 个数字键使用相同的事件处理程序，并区分其`StyleId`属性：

[![最简单的小键盘的三个屏幕截图](images/ch06fg04-small.png "计算器")](images/ch06fg04-large.png#lightbox "计算器")

## <a name="saving-transient-data"></a>正在保存暂时性数据

许多应用程序需要时终止程序保存数据和程序重新启动时重新加载该数据。 [ `Application` ](xref:Xamarin.Forms.Application)类定义帮助程序保存和还原的临时数据的多个成员：

- [ `Properties` ](xref:Xamarin.Forms.Application.Properties)属性是使用字典`string`密钥和`object`项。 字典的内容自动保存在应用程序在程序终止之前的本地存储并重新加载时启动该程序。
- `Application`类定义了三个受保护虚方法，该程序的标准`App`类重写： [ `OnStart` ](xref:Xamarin.Forms.Application.OnStart)， [ `OnSleep` ](xref:Xamarin.Forms.Application.OnSleep)，并[ `OnResume` ](xref:Xamarin.Forms.Application.OnResume). 这些是指*应用程序生命周期*事件。
- [ `SavePropertiesAsync` ](xref:Xamarin.Forms.Application.SavePropertiesAsync)方法保存字典的内容。

不需要调用`SavePropertiesAsync`。 自动在程序终止之前保存并在程序启动之前检索字典的内容。 在程序测试，以保存数据，如果程序崩溃期间非常有用。

也有用的是：

- [`Application.Current`](xref:Xamarin.Forms.Application.Current)返回当前的静态属性`Application`对象，您可以使用它来获取`Properties`字典。

第一步是标识想要持久保存在程序终止时的页面上的所有变量。 如果您知道这些变量更改其中的所有位置，您可以只需将其添加到`Properties`字典在该点。 在该页的构造函数，可以设置从变量`Properties`如果存在的键的字典。

较大程序可能需要处理的应用程序生命周期事件。 最重要的是`OnSleep`方法。 对此方法的调用指示该程序已离开前景色。 用户已按也许**主页**按钮在设备上，或显示所有应用程序，或关闭手机。 调用`OnSleep`是唯一的通知程序收到终止前。 该程序应利用这个机会，以确保`Properties`字典是最新。

调用`OnResume`指示该程序未终止后的最后一个调用到`OnSleep`但现在再次运行在前景中。 该程序可能会使用这个机会 （例如） 刷新的 internet 连接。

调用`OnStart`在程序启动期间发生。 不需要等待，直到此方法调用访问`Properties`字典因为内容已被还原时`App`调用构造函数。

[ **PersistentKeypad** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/PersistentKeypad)示例是非常类似于**SimplestKeypad**只不过该程序使用`OnSleep`重写以保存当前的键盘输入，并若要还原该数据页构造函数。

> [!NOTE]
> 正在保存程序设置的另一种方法提供的 Xamarin.Essentials[首选项](~/essentials/preferences.md)类。

## <a name="related-links"></a>相关链接

- [第 6 章全文 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch06-Apr2016.pdf)
- [第 6 章示例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06)
- [第 6 章F#示例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/FS)
- [Xamarin.Forms 按钮](~/xamarin-forms/user-interface/button.md)
