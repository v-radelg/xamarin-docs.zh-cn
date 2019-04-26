---
title: 第 12 章的摘要。 样式
description: 使用 Xamarin.Forms 创建移动应用：第 12 章的摘要。 样式
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 3EAE6BDC-8EFB-464B-A87B-1C35B8387BB3
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
ms.openlocfilehash: bb2cd1c97cc588923e0da1a8793f16445c111f0e
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61334277"
---
# <a name="summary-of-chapter-12-styles"></a>第 12 章的摘要。 样式

[![下载示例](~/media/shared/download.png)下载示例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12)

在 Xamarin.Forms 中，样式允许多个视图来共享的属性设置的集合。 这减少了标记，并使维护一致的视觉主题。

样式几乎总是定义，并在标记中使用。 类型的对象[ `Style` ](xref:Xamarin.Forms.Style)是在资源字典中实例化，然后设置为[ `Style` ](xref:Xamarin.Forms.VisualElement.Style)的可视元素使用属性`StaticResource`或`DynamicResource`标记扩展插件。

## <a name="the-basic-style"></a>基本样式

一个`Style`要求其[ `TargetType` ](xref:Xamarin.Forms.Style.TargetType)设置为适用于在可视对象的类型。 当`Style`实例化中的资源字典 （如很常见） 它还需要`x:Key`属性。

`Style`具有内容类型的属性[ `Setters` ](xref:Xamarin.Forms.Style.Setters)，这是一系列[ `Setter` ](xref:Xamarin.Forms.Setter)对象。 每个`Setter`相关联[ `Property` ](xref:Xamarin.Forms.Setter.Property)与[ `Value` ](xref:Xamarin.Forms.Setter.Value)。

在 XAML`Property`设置是 CLR 属性的名称 (如`Text`属性的`Button`)，但必须由可绑定的属性支持带样式的属性。 此外，必须将属性中指示的类定义`TargetType`设置，或由该类继承。

您可以指定`Value`设置使用属性元素`<Setter.Value>`。 这允许您设置`Value`中的文本字符串，或无法表示的对象`OnPlatform`对象，或者对对象实例化使用`x:Arguments`或`x:FactoryMethod`。 `Value`属性也可以设置与`StaticResource`到字典中的另一个项的表达式。

[ **BasicStyle** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/BasicStyle)程序演示了基本语法，并演示如何引用`Style`与`StaticResource`标记扩展：

[![基本样式的三个屏幕截图](images/ch12fg01-small.png "基本样式")](images/ch12fg01-large.png#lightbox "基本样式")

`Style`对象和在中创建任何对象`Style`对象作为`Value`引用的所有视图之间共享设置`Style`。 `Style`不能包含任何内容不能共享，如`View`派生类。

无法在事件处理程序中设置`Style`。 `GestureRecognizers`不能在中设置属性`Style`因为它不由可绑定的属性。

## <a name="styles-in-code"></a>在代码中的样式

虽然不常见，但可以实例化和初始化`Style`代码中的对象。 这可通过演示[ **BasicStyleCode** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/BasicStyleCode)示例。

## <a name="style-inheritance"></a>样式继承

`Style` 具有[ `BasedOn` ](xref:Xamarin.Forms.Style.BasedOn)属性，可以将设置为`StaticResource`引用另一种样式的标记扩展。 这样，继承自上一个样式，以及添加或替换属性设置的样式。 [ **StyleInheritance** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/StyleInheritance)示例演示了这。

如果`Style2`基于`Style1`，则`TargetType`的`Style2`必须与相同`Style1`或派生自`Style1`。 在其中的资源字典`Style1`存储必须为相同的资源字典`Style2`或更高版本的可视化树中的资源字典。

## <a name="implicit-styles"></a>隐式样式

如果`Style`中的资源字典不具有`x:Key`属性设置，它自动分配有一个字典键和`Style`对象将成为*隐式样式*。 不含视图`Style`设置，并且其类型与匹配`TargetType`完全会发现该样式中，名为[ **ImplicitStyle** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/ImplicitStyle)示例演示。

隐式样式可以派生自`Style`与`x:Key`设置，但不是在相反方向。 您不能显式引用的隐式样式。

您可以实现三种类型的层次结构中具有样式和`BasedOn`:

- 从上定义的样式`Application`和`Page`到布局中的可视化树较低级别上定义的样式。
- 从如定义基类的样式`VisualElement`和`View`到为特定的类定义的样式。
- 从具有显式的字典键到隐式样式的样式。

这些层次结构所示[ **StyleHierarchy** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/StyleHierarchy)示例。

## <a name="dynamic-styles"></a>动态样式

可以通过引用的资源字典中的样式`DynamicResource`而非`StaticResource`。 这使得样式*动态样式*。 如果该样式将被替换的资源字典中由另一个样式为同一个键，引用与该样式的视图`DynamicResource`自动更改。 此外，如果没有具有指定键的字典项将导致`StaticResource`引发异常但不是`DynamicResource`。

可以使用此方法需要随时动态更改样式或作为主题[ **DynamicStyles** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/DynamicStyles)示例演示。

但是，不能设置`BasedOn`属性设置为`DynamicResource`构成扩展因为`BasedOn`可绑定的属性不支持。 若要动态派生一个样式，请不要设置`BasedOn`。 与此相反，设置[ `BaseResourceKey` ](xref:Xamarin.Forms.Style.BaseResourceKey)属性设置为你想要派生的样式的字典的键。 [ **DynamicStylesInheritance** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/DynaStylesInh)示例演示此技术。

## <a name="device-styles"></a>设备样式

[ `Device.Styles` ](xref:Xamarin.Forms.Device.Styles)嵌套的类定义使用六个样式的 12 个静态只读字段`TargetType`的`Label`，可以使用为常见类型的文本用法。

这些字段的六个属于类型`Style`您可以直接设置`Style`在代码中的属性：

- [`BodyStyle`](xref:Xamarin.Forms.Device.Styles.BodyStyle)
- [`TitleStyle`](xref:Xamarin.Forms.Device.Styles.TitleStyle)
- [`SubtitleStyle`](xref:Xamarin.Forms.Device.Styles.SubtitleStyle)
- [`CaptionStyle`](xref:Xamarin.Forms.Device.Styles.CaptionStyle)
- [`ListItemTextStyle`](xref:Xamarin.Forms.Device.Styles.ListItemTextStyle)
- [`ListItemDetailTextStyle`](xref:Xamarin.Forms.Device.Styles.ListItemDetailTextStyle)

其他六个字段是类型的`string`，可以用作动态样式的字典键：

- [`BodyStyleKey`](xref:Xamarin.Forms.Device.Styles.BodyStyleKey) 等于"BodyStyle"
- [`TitleStyleKey`](xref:Xamarin.Forms.Device.Styles.TitleStyleKey) 等于"TitleStyle"
- [`SubtitleStyleKey`](xref:Xamarin.Forms.Device.Styles.SubtitleStyleKey) 等于"SubtitleStyle"
- [`CaptionStyleKey`](xref:Xamarin.Forms.Device.Styles.CaptionStyleKey) 等于"CaptionStyle"
- [`ListItemTextStyleKey`](xref:Xamarin.Forms.Device.Styles.ListItemTextStyleKey) 等于"ListItemTextStyle"
- [`ListItemDetailTextStyleKey`](xref:Xamarin.Forms.Device.Styles.ListItemDetailTextStyleKey) 等于"ListItemDetailTextStyle"

这些样式所示[ **DeviceStylesList** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/DeviceStylesList)示例。

## <a name="related-links"></a>相关链接

- [第 12 章全文 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch12-Apr2016.pdf)
- [第 12 章示例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12)
- [样式](~/xamarin-forms/user-interface/styles/index.md)
