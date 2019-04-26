---
title: 第 3 章的摘要。 深入到文本
description: 使用 Xamarin.Forms 创建移动应用：第 3 章的摘要。 深入到文本
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 2E5581A6-4D3E-4BD5-9FDB-ACBA0F0FC734
author: davidbritch
ms.author: dabritch
ms.date: 07/18/2018
ms.openlocfilehash: 11b0a9c5b165df2b0d02bb09fb282cc18290e6ca
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61334538"
---
# <a name="summary-of-chapter-3-deeper-into-text"></a>第 3 章的摘要。 深入到文本

[![下载示例](~/media/shared/download.png)下载示例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03)

本章探讨[ `Label` ](xref:Xamarin.Forms.Label)更为深入，包括颜色、 字体和格式设置中的视图。

## <a name="wrapping-paragraphs"></a>包装段落

当[ `Text` ](xref:Xamarin.Forms.Label.Text)的属性`Label`包含长文本`Label`会自动将其包装到多个行所示[ **Baskervilles**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/Baskervilles)示例。 可以嵌入 Unicode 代码 \u2014 为长破折号，如或C#字符，如 '\r' 中断到新行。

当[ `HorizontalOptions` ](xref:Xamarin.Forms.View.HorizontalOptions)和[ `VerticalOptions` ](xref:Xamarin.Forms.View.VerticalOptions)的属性`Label`设置为`LayoutOptions.Fill`，则的总体大小`Label`受到空间，其容器使可用。 `Label`称为*约束*。 大小`Label`是其容器的大小。

当`HorizontalOptions`和`VerticalOptions`属性设置为值而不`LayoutOptions.Fill`，则大小`Label`受呈现的文本，最大大小，其容器使可用于所需的空间`Label`。 `Label`称为*不受约束*并确定其自身的大小。

（注意：条款*约束*并*不受约束*可能违反语感，因为不受约束的视图是通常小于受约束的视图。 此外，这些条款不使用一致地中书籍的章节。）

如视图`Label`可以被限制在一个维度和其他不受约束。 一个`Label`将仅文本换行在多个行上如果水平约束。

如果`Label`是约束，它可能占用多得多的空间比所需的文本。 文本可以定位的整个区域内`Label`。 设置[ `HorizontalTextAlignment` ](xref:Xamarin.Forms.Label.HorizontalTextAlignment)属性绑定到的成员[ `TextAlignment` ](xref:Xamarin.Forms.TextAlignment)枚举 ([`Start`](xref:Xamarin.Forms.TextAlignment.Start)， [ `Center` ](xref:Xamarin.Forms.TextAlignment.Center)，或[ `End` ](xref:Xamarin.Forms.TextAlignment.Center)) 来控制的段的所有行的对齐方式。 默认值是`Start`和左对齐的文本。

设置[ `VerticalTextAlignment` ](xref:Xamarin.Forms.Label.VerticalTextAlignment)属性绑定到的成员`TextAlignment`枚举放置在顶部、 中心或占用的区域的底部文本`Label`。

设置[ `LineBreakMode` ](xref:Xamarin.Forms.Label.LineBreakMode)属性绑定到的成员[ `LineBreakMode` ](xref:Xamarin.Forms.LineBreakMode)枚举 ([`WordWrap`](xref:Xamarin.Forms.LineBreakMode.WordWrap)， [ `CharacterWrap` ](xref:Xamarin.Forms.LineBreakMode.CharacterWrap)， [ `NoWrap` ](xref:Xamarin.Forms.LineBreakMode.NoWrap)， [ `HeadTruncation` ](xref:Xamarin.Forms.LineBreakMode.HeadTruncation)， [ `MiddleTruncation` ](xref:Xamarin.Forms.LineBreakMode.MiddleTruncation)，或[ `TailTruncation` ](xref:Xamarin.Forms.LineBreakMode.TailTruncation)) 到控制如何多行输入一个分段符，或将被截断。

## <a name="text-and-background-colors"></a>文本和背景色

设置[ `TextColor` ](xref:Xamarin.Forms.Label.TextColor)并[ `BackgroundColor` ](xref:Xamarin.Forms.VisualElement.BackgroundColor)属性的`Label`到[ `Color` ](xref:Xamarin.Forms.Color)值控制的文本和背景颜色。

`BackgroundColor`适用于占用的整个区域的背景`Label`。 具体取决于`HorizontalOptions`和`VerticalOptions`属性、 大小可能远远大于所显示的文本区域。 可以使用颜色来试验的各种值`HorizontalOptions`， `VerticalOptions`， `HorizontalExeAlignment`，和`VerticalTextAlignment`若要查看它们如何影响大小和位置`Label`，大小和位置中的文本和`Label`。

## <a name="the-color-structure"></a>颜色结构

[ `Color` ](xref:Xamarin.Forms.Color)结构允许您指定的颜色以红-绿-蓝 (RGB) 值或色调-饱和度-亮度 (HSL) 的值，或使用的颜色名称。 Alpha 通道也是可用于指示透明度。

使用`Color`构造函数来指定：

- [灰色阴影](xref:Xamarin.Forms.Color.%23ctor(System.Double))
- [RGB 值](xref:Xamarin.Forms.Color.%23ctor(System.Double,System.Double,System.Double))
- [具有透明度的 RGB 值](xref:Xamarin.Forms.Color.%23ctor(System.Double,System.Double,System.Double,System.Double))

参数是`double`值范围从 0 到 1。

此外可以使用多个静态方法来创建`Color`值：

- [`Color.FromRgb`](xref:Xamarin.Forms.Color.FromRgb(System.Double,System.Double,System.Double)) 有关`double`RGB 值从 0 到 1
- [`Color.FromRgb`](xref:Xamarin.Forms.Color.FromRgb(System.Int32,System.Int32,System.Int32)) 对于从 0 到 255 之间的整数 RGB 值
- [`Color.FromRgba`](xref:Xamarin.Forms.Color.FromRgba(System.Double,System.Double,System.Double,System.Double)) 有关`double`透明度的 RGB 值
- [`Color.FromRgba`](xref:Xamarin.Forms.Color.FromRgba(System.Int32,System.Int32,System.Int32,System.Int32)) 对于具有透明度整数 RGB 值
- [`Color.FromHsla`](xref:Xamarin.Forms.Color.FromHsla(System.Double,System.Double,System.Double,System.Double)) 有关`double`透明度 HSL 值
- [`Color.FromUint`](xref:Xamarin.Forms.Color.FromUint(System.UInt32)) 有关`uint`值计算方式为 (B + 256 * (G + 256 * (R + 256 * 一个)))
- [`Color.FromHex`](xref:Xamarin.Forms.Color.FromHex(System.String)) 有关`string`的十六进制数字构成，采用格式"#AARRGGBB"或"#RRGGBB"或"#ARGB"或"#RGB"，其中每个字母对应于十六进制数字的 alpha、 红色、 绿色和蓝色通道。 此方法是主要用于 XAML 颜色转换，如中所述[第 7 章、 XAML 与代码](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter07.md)。

创建之后，`Color`值是固定不变。 可以从下列属性获得的颜色特征：

- [`R`](xref:Xamarin.Forms.Color.R)
- [`G`](xref:Xamarin.Forms.Color.G)
- [`B`](xref:Xamarin.Forms.Color.B)
- [`A`](xref:Xamarin.Forms.Color.A)
- [`Hue`](xref:Xamarin.Forms.Color.Hue)
- [`Saturation`](xref:Xamarin.Forms.Color.Saturation)
- [`Luminosity`](xref:Xamarin.Forms.Color.Luminosity)

这些是所有`double`值范围从 0 到 1。

`Color` 此外定义了 240 公共静态只读字段的常用颜色。 在编写此书时，仅 17 常用颜色都不可用。

另一个的公共静态只读字段设置为零的所有颜色通道与定义的颜色：

- [`Color.Transparent`](xref:Xamarin.Forms.Color.Transparent)

多个实例方法允许修改现有的颜色来创建新颜色：

- [`AddLuminosity`](xref:Xamarin.Forms.Color.AddLuminosity(System.Double))
- [`MultiplyAlpha`](xref:Xamarin.Forms.Color.MultiplyAlpha(System.Double))
- [`WithHue`](xref:Xamarin.Forms.Color.WithHue(System.Double))
- [`WithLuminosity`](xref:Xamarin.Forms.Color.WithLuminosity(System.Double))
- [`WithSaturation`](xref:Xamarin.Forms.Color.WithSaturation(System.Double))

最后，两个静态只读属性定义特殊颜色值：

- [`Color.Default`](xref:Xamarin.Forms.Color.Default)所有频道都设置为&ndash;1
- [`Color.Accent`](xref:Xamarin.Forms.Color.Accent)

`Color.Default` 用于强制实施平台的配色方案，并因此在不同的平台上的不同上下文中具有不同的含义。 默认情况下将平台的颜色方案：

- iOS:浅色背景上的深文本
- Android:浅色 （在工作簿） 深色背景上的文本或浅色背景上的深文本 (材料设计通过在 AppCompat**主**的示例代码存储库的分支)
- UWP:浅色背景上的深文本

`Color.Accent`值会导致在深色或浅色背景可见的特定于平台的 （和用户有时可选） 颜色。

## <a name="changing-the-application-color-scheme"></a>更改应用程序配色方案

各种平台都具有默认配色方案，如上面的列表中所示。

如果以 Android 为目标，就可以通过指定浅色主题 Android.Manifest.xml 文件中或通过切换到深上浅方案[添加 AppCompat 和材料设计](~/xamarin-forms/platform/android/appcompat-material-design.md)。

对于 Windows 平台中，颜色主题通常选择用户，但您可以添加`RequestedTheme`属性设置为`Light`或`Dark`平台的 App.xaml 文件中。 默认情况下，在 UWP 项目的 App.xaml 文件包含`RequestedTheme`属性设置为`Light`。

## <a name="font-sizes-and-attributes"></a>字体大小和属性

设置[ `FontFamily` ](xref:Xamarin.Forms.Label.FontFamily)属性的`Label`到一个字符串，如"Times Roman"选择字体系列。 但是，您需要指定在特定平台支持的字体系列和平台是在这方面不一致。

设置[ `FontSize` ](xref:Xamarin.Forms.Label.FontSize)的属性`Label`到`double`用于指定字体的大致高度。 请参阅[第 5 章处理大小](chapter05.md)，有关详细信息以智能方式选择字体大小。

或者，你可以获取多个预设依赖于平台的字体大小之一。 静态[ `Device.GetNamedSize` ](xref:Xamarin.Forms.Device.GetNamedSize(Xamarin.Forms.NamedSize,System.Type))方法并[重载](xref:Xamarin.Forms.Device.GetNamedSize(Xamarin.Forms.NamedSize,Xamarin.Forms.Element))两者都返回`double`适合于平台的字体大小值基于成员[ `NamedSize` ](xref:Xamarin.Forms.NamedSize)枚举 ([`Default`](xref:Xamarin.Forms.NamedSize.Default)， [ `Micro` ](xref:Xamarin.Forms.NamedSize.Micro)， [ `Small` ](xref:Xamarin.Forms.NamedSize.Small)， [ `Medium` ](xref:Xamarin.Forms.NamedSize.Medium)， 并[ `Large` ](xref:Xamarin.Forms.NamedSize.Large))。 从返回的值`Medium`成员不一定是相同`Default`。 [ **NamedFontSizes** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/NamedFontSizes)示例使用这些命名大小显示文本。

设置[ `FontAttributes` ](xref:Xamarin.Forms.Label.FontAttributes)的属性`Label`给这些成员[ `FontAttributes` ](xref:Xamarin.Forms.FontAttributes)枚举[ `Bold` ](xref:Xamarin.Forms.FontAttributes.Bold)， [ `Italic`](xref:Xamarin.Forms.FontAttributes.Italic)，或[ `None` ](xref:Xamarin.Forms.FontAttributes.None)。 你可以组合`Bold`并`Italic`成员C#按位 OR 运算符。

## <a name="formatted-text"></a>带格式文本

中的所有示例到目前为止，整个文本都显示`Label`统一已设置格式。 若要改变在文本字符串中的格式设置，未设置`Text`属性的`Label`。 与此相反，设置[ `FormattedText` ](xref:Xamarin.Forms.Label.FormattedText)类型的对象的属性[ `FormattedString` ](xref:Xamarin.Forms.FormattedString)。

`FormattedString` 具有[ `Spans` ](xref:Xamarin.Forms.FormattedString.Spans)属性是一系列[ `Span` ](xref:Xamarin.Forms.Span)对象。 每个`Span`对象都有其自身[ `Text` ](xref:Xamarin.Forms.Span.Text)， [ `FontFamily` ](xref:Xamarin.Forms.Span.FontFamily)， [ `FontSize` ](xref:Xamarin.Forms.Span.FontSize)， [ `FontAttributes` ](xref:Xamarin.Forms.Span.FontAttributes)， [ `ForegroundColor` ](xref:Xamarin.Forms.Span.ForegroundColor)，并[ `BackgroundColor` ](xref:Xamarin.Forms.Span.BackgroundColor)属性。

[ **VariableFormattedText** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/VarFormText)示例演示如何使用`FormattedText`对于单行文本、 属性和[ **VariableFormattedParagraph**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/VarFormPara)说明整个段落，该方法，如下所示：

[![变量的三个屏幕快照格式的段落](images/ch03fg06-small.png "变量格式的标签文本")](images/ch03fg06-large.png#lightbox "变量格式的标签文本")

[ **NamedFontSizes** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/NamedFontSizes)程序使用单个`Label`和`FormattedString`要显示每个平台的已命名的字体大小的所有对象。



## <a name="related-links"></a>相关链接

- [第 3 章全文 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch03-Apr2016.pdf)
- [第 3 章示例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03)
- [第 3 章F#示例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/FS)
- [标签](~/xamarin-forms/user-interface/text/label.md)
- [处理颜色](~/xamarin-forms/user-interface/colors.md)
