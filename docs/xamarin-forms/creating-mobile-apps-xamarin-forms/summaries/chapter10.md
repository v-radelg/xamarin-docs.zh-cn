---
title: 第 10 章的摘要。 XAML 标记扩展
description: 使用 Xamarin.Forms 创建移动应用：第 10 章的摘要。 XAML 标记扩展
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 575EAE55-BD4D-470F-A583-3D065FA102E2
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
ms.openlocfilehash: 076e9f5155492e5a69d906c587b24495fe39d3f1
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/08/2019
ms.locfileid: "57672633"
---
# <a name="summary-of-chapter-10-xaml-markup-extensions"></a>第 10 章的摘要。 XAML 标记扩展

[![下载示例](~/media/shared/download.png)下载示例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10)

通常情况下，XAML 分析器将为任何字符串作为属性值设置为基于标准转换为基本的.NET 数据类型，该属性的类型或[ `TypeConverter` ](xref:Xamarin.Forms.TypeConverter)附加到的属性或具有其类型的派生类[`TypeConverterAttribute`](xref:Xamarin.Forms.TypeConverterAttribute).

但很方便地设置属性，从不同源，例如，字典的路由或静态属性或字段的值中的项或某种类型的计算。

这是作业的*XAML 标记扩展*。 不管名称如何，XAML 标记扩展是*不*XML 的扩展。 XAML 始终是合法的 XML。

## <a name="the-code-infrastructure"></a>代码基础结构

XAML 标记扩展是一个类，实现[ `IMarkupExtension` ](xref:Xamarin.Forms.Xaml.IMarkupExtension)接口。 此类通常包含单词`Extension`在其名称末尾但通常会显示在 XAML 中没有该后缀。

下面的 XAML 标记扩展支持 XAML 的所有实现：

- `x:Static` 支持的 [`StaticExtension`](xref:Xamarin.Forms.Xaml.StaticExtension)
- `x:Reference` 支持的 [`ReferenceExtension`](xref:Xamarin.Forms.Xaml.ReferenceExtension)
- `x:Type` 支持的 [`TypeExtension`](xref:Xamarin.Forms.Xaml.TypeExtension)
- `x:Null` 支持的 [`NullExtension`](xref:Xamarin.Forms.Xaml.NullExtension)
- `x:Array` 支持的 [`ArrayExtension`](xref:Xamarin.Forms.Xaml.ArrayExtension)

这些四个 XAML 标记扩展支持的 XAML，包括 Xamarin.Forms 的许多实现：

- `StaticResource` 支持的 [`StaticResourceExtension`](xref:Xamarin.Forms.Xaml.StaticResourceExtension)
- `DynamicResource` 支持的 [`DynamicResourceExtension`](xref:Xamarin.Forms.Xaml.DynamicResourceExtension)
- `Binding` 受[ `BindingExtension` ](xref:Xamarin.Forms.Xaml.BindingExtension)&mdash;中所述[第 16 章。数据绑定](chapter16.md)
- `TemplateBinding` 受[ `TemplateBindingExtension` ](xref:Xamarin.Forms.Xaml.TemplateBindingExtension)&mdash;未涵盖一书中

其他的 XAML 标记扩展包含在门户中的 Xamarin.Forms [ `RelativeLayout` ](xref:Xamarin.Forms.RelativeLayout):

- [`ConstraintExpression`](xref:Xamarin.Forms.ConstraintExpression)&mdash;本书中未覆盖

## <a name="accessing-static-members"></a>访问静态成员

使用[ `x:Static` ](xref:Xamarin.Forms.Xaml.StaticExtension)元素将属性设置为公共静态属性、 字段或枚举成员的值。 设置[ `Member` ](xref:Xamarin.Forms.Xaml.StaticExtension.Member)属性绑定到静态成员。 它是通常更方便地指定`x:Static`和大括号中的成员名称。 名称`Member`属性不需要包含，只是该成员本身。 此常见语法所示[ **SharedStatics** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/SharedStatics)示例。 中定义的静态字段本身[ `AppConstants` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter10/SharedStatics/SharedStatics/SharedStatics/AppConstants.cs)类。 此技术，可建立执行程序使用的常量。

使用其他的 XML 命名空间声明，您可以引用的公共静态属性、 字段或.NET framework 中定义的枚举成员中所示[ **SystemStatics** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/SystemStatics)示例.

## <a name="resource-dictionaries"></a>资源字典

`VisualElement`类定义一个名为属性[ `Resources` ](xref:Xamarin.Forms.VisualElement.Resources)可设置为类型的对象[ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)。 在 XAML 中，你可以将项存储在此字典中和确定它们与`x:Key`属性。 对项的所有引用都共享的资源字典中存储的项。

### <a name="staticresource-for-most-purposes"></a>在大多数情况的 StaticResource

在大多数情况下将使用[ `StaticResource` ](xref:Xamarin.Forms.Xaml.StaticResourceExtension)若要从资源字典中的引用项，如所示的标记扩展[ **ResourceSharing** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/ResourceSharing)示例. 可以使用`StaticResourceExtension`元素或`StaticResource`的大括号内：

[![资源共享的三个屏幕截图](images/ch10fg03-small.png "资源共享")](images/ch10fg03-large.png#lightbox "资源共享")

不要混淆`x:Static`标记扩展和`StaticResource`标记扩展。

### <a name="a-tree-of-dictionaries"></a>树的字典

当 XAML 分析器遇到`StaticResource`，它开始搜索匹配的键，在可视树并随后查找`ResourceDictionary`在应用程序的`App`类。 这样更深入地可视化树中重写的可视化树中级别更高的资源字典中的资源字典中的项。 了这一点[ **ResourceTrees** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/ResourceTrees)示例。

### <a name="dynamicresource-for-special-purposes"></a>DynamicResource 出于特殊目的

`StaticResource`标记扩展将导致在生成的可视树时要检索字典中的项`InitializeComponent`调用。 一种替代方法`StaticResource`是[ `DynamicResource` ](xref:Xamarin.Forms.Xaml.DynamicResourceExtension)，其维护字典键的链接和项引用的关键更改时更新目标。

之间的差异`StaticResource`并`DynamicResource`中所示[ **DynamicVsStatic** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/DynamicVsStatic)示例。

通过设置的属性`DynamicResource`中所述，必须由可绑定的属性支持[第 11 章可绑定的基础结构](chapter11.md)。

## <a name="lesser-used-markup-extensions"></a>使用较小的标记扩展

使用[ `x:Null` ](xref:Xamarin.Forms.Xaml.NullExtension)标记扩展将属性设置为`null`。

使用[ `x:Type` ](xref:Xamarin.Forms.Xaml.TypeExtension)标记扩展将属性设置为.NET`Type`对象。

使用[ `x:Array` ](xref:Xamarin.Forms.Xaml.ArrayExtension)以定义一个数组。 通过设置指定的数组成员的类型 [`Type`] 属性设置为`x:Type`标记扩展。

## <a name="a-custom-markup-extension"></a>自定义标记扩展

可以通过编写实现的类创建自己的 XAML 标记扩展[ `IMarkupExtension` ](xref:Xamarin.Forms.Xaml.IMarkupExtension)接口与[ `ProvideValue` ](xref:Xamarin.Forms.Xaml.IMarkupExtension.ProvideValue(System.IServiceProvider))方法。

[ `HslColorExtension` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/HslColorExtension.cs)类满足这些要求。 它将创建类型的值`Color`基于名为的属性的值`H`， `S`， `L`，和`A`。 此类是一个名为的 Xamarin.Forms 库中的第一个项[ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)构建且在过去这本书的使用。

[ **CustomExtensionDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/CustomExtensionDemo)示例演示如何引用此库和使用自定义标记扩展。

## <a name="related-links"></a>相关链接

- [第 10 章全文 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch10-Apr2016.pdf)
- [第 10 章示例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10)
- [XAML 标记扩展](~/xamarin-forms/xaml/markup-extensions/index.md)
