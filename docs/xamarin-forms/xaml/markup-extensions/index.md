---
title: XAML 标记扩展
description: 本文介绍如何使用 Xamarin.Forms XAML 标记扩展来扩展的功能和灵活性的 XAML 元素特性，若要从文本字符串以外的源设置，从而。
ms.prod: xamarin
ms.assetid: EB06C8B7-3FD5-47B7-A09C-A13063BD110F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/05/2018
ms.openlocfilehash: 40a50ecf93c8b77577e8e74df2e134b0ce3f80b6
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70767541"
---
# <a name="xaml-markup-extensions"></a>XAML 标记扩展

[![下载示例](~/media/shared/download.png)下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-markupextensions)

XAML 标记扩展可帮助元素特性，若要从文本字符串以外的源设置，从而扩展的功能和灵活性的 XAML。

例如，正常情况下设置`Color`属性的`BoxView`如下所示：

```xaml
<BoxView Color="Blue" />
```

或者，可以将其设置为十六进制的 RGB 颜色值：

```xaml
<BoxView Color="#FF0080" />
```

在任一情况下，文本字符串设置为`Color`属性转换为`Color`的值[ `ColorTypeConverter` ](xref:Xamarin.Forms.ColorTypeConverter)类。

您可能希望改为设置`Color`属性从存储中使用的资源字典的值或已创建的类的静态属性的值或类型的属性`Color`的页上，另一个元素或从构造分隔色调、 饱和度和亮度值。

所有这些选项都可能使用 XAML 标记扩展。 但不要让短语 "标记扩展" 心有余悸：XAML 标记扩展*不*是 XML 的扩展。 即使使用 XAML 标记扩展，XAML 始终是合法的 XML。

标记扩展是实际上只是以不同的方式来表示一个元素的属性。 XAML 标记扩展是通常可识别括在大括号中的属性设置：

```xaml
<BoxView Color="{StaticResource themeColor}" />
```

在大括号中的任何属性设置为*始终*XAML 标记扩展。 但是，正如您将看到，XAML 标记扩展还可以引用而不使用大括号。

本文分为两个部分：

## <a name="consuming-xaml-markup-extensionsconsumingmd"></a>[使用 XAML 标记扩展](consuming.md)  

使用 Xamarin.Forms 中定义的 XAML 标记扩展。

## <a name="creating-xaml-markup-extensionscreatingmd"></a>[创建 XAML 标记扩展](creating.md)

编写您自己自定义的 XAML 标记扩展。

## <a name="related-links"></a>相关链接

- [标记扩展 （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-markupextensions)
- [从 Xamarin.Forms 书籍的 XAML 标记扩展一章](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter10.md)
- [资源字典](~/xamarin-forms/xaml/resource-dictionaries.md)
- [动态样式](~/xamarin-forms/user-interface/styles/dynamic.md)
- [数据绑定](~/xamarin-forms/app-fundamentals/data-binding/index.md)
