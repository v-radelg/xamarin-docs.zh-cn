---
title: 第3章摘要： 深入到文本
description: 用 Xamarin 创建移动应用：第3章的摘要。 深入到文本
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 2E5581A6-4D3E-4BD5-9FDB-ACBA0F0FC734
author: davidbritch
ms.author: dabritch
ms.date: 07/18/2018
ms.openlocfilehash: 69415b59bbd376330454302981e3216c236a16bb
ms.sourcegitcommit: 93697a20e6fc7da547a8714ac109d7953b61d63f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/28/2019
ms.locfileid: "72980922"
---
# <a name="summary-of-chapter-3-deeper-into-text"></a>第3章摘要： 深入到文本

[![下载示例](~/media/shared/download.png) 下载示例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03)

本章更深入地探讨了[`Label`](xref:Xamarin.Forms.Label)视图，包括颜色、字体和格式设置。

## <a name="wrapping-paragraphs"></a>包装段落

当 `Label` 的[`Text`](xref:Xamarin.Forms.Label.Text)属性包含长文本时，`Label` 会自动将其包装到[**Baskervilles**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/Baskervilles)示例所示的多个行。 你可以嵌入 Unicode 代码（如 "\u2014"）作为全身破折号，或C#使用 "\r" 之类的字符来换行。

当 `Label` 的[`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions)和[`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions)属性设置为 `LayoutOptions.Fill`时，`Label` 的总体大小受其容器可用空间的控制。 `Label` 被视为*受限制*。 `Label` 的大小为其容器的大小。

如果 `HorizontalOptions` 和 `VerticalOptions` 属性设置为除 `LayoutOptions.Fill`以外的值，则 `Label` 的大小由呈现文本所需的空间来控制，最大大小为其容器可用于 `Label`的大小。 `Label` 被认为是不受*限制*的，它会确定其大小。

（注意：*约束*和不受*约束的*术语可能会比较直观，因为无约束视图通常小于约束视图。 此外，本书籍的早期章节中并不一致地使用这些术语。）

`Label` 的视图可以在一个维度中受到约束，而在另一个维度中不受约束。 `Label` 将仅在多行上环绕文本（如果它水平约束）。

如果 `Label` 受到约束，则它可能比文本所需的空间要大得多。 文本可以位于 `Label`的整个区域内。 将[`HorizontalTextAlignment`](xref:Xamarin.Forms.Label.HorizontalTextAlignment)属性设置为[`TextAlignment`](xref:Xamarin.Forms.TextAlignment)枚举的成员（[`Start`](xref:Xamarin.Forms.TextAlignment.Start)、 [`Center`](xref:Xamarin.Forms.TextAlignment.Center)或[`End`](xref:Xamarin.Forms.TextAlignment.Center)），以控制段落中所有行的对齐方式。 默认值为 `Start`，并将文本左对齐。

将[`VerticalTextAlignment`](xref:Xamarin.Forms.Label.VerticalTextAlignment)属性设置为 `TextAlignment` 枚举的成员，以将文本置于 `Label`占用的区域的顶部、中间或底部。

将[`LineBreakMode`](xref:Xamarin.Forms.Label.LineBreakMode)属性设置为[`LineBreakMode`](xref:Xamarin.Forms.LineBreakMode)枚举的成员（[`WordWrap`](xref:Xamarin.Forms.LineBreakMode.WordWrap)、 [`CharacterWrap`](xref:Xamarin.Forms.LineBreakMode.CharacterWrap)、 [`NoWrap`](xref:Xamarin.Forms.LineBreakMode.NoWrap)、 [`HeadTruncation`](xref:Xamarin.Forms.LineBreakMode.HeadTruncation)、 [`MiddleTruncation`](xref:Xamarin.Forms.LineBreakMode.MiddleTruncation)或[`TailTruncation`](xref:Xamarin.Forms.LineBreakMode.TailTruncation)），以控制多个段落换行或被截断的行。

## <a name="text-and-background-colors"></a>文本和背景色

将 `Label` 的[`TextColor`](xref:Xamarin.Forms.Label.TextColor)和[`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor)属性设置为[`Color`](xref:Xamarin.Forms.Color)值，以控制文本和背景的颜色。

`BackgroundColor` 适用于 `Label`所占用的整个区域的背景。 根据 `HorizontalOptions` 和 `VerticalOptions` 属性，该大小可能会远远大于显示文本所需的区域。 您可以使用颜色来试验 `HorizontalOptions`、`VerticalOptions`、`HorizontalExeAlignment`和 `VerticalTextAlignment` 的各个值，以了解它们如何影响 `Label`的大小和位置以及文本在 `Label`中的大小和位置。

## <a name="the-color-structure"></a>颜色结构

利用[`Color`](xref:Xamarin.Forms.Color)结构，可以将颜色指定为红-绿蓝（RGB）值，或使用颜色名称指定颜色。 Alpha 通道还可用于指示透明度。

使用 `Color` 构造函数指定：

- [灰色阴影](xref:Xamarin.Forms.Color.%23ctor(System.Double))
- [RGB 值](xref:Xamarin.Forms.Color.%23ctor(System.Double,System.Double,System.Double))
- [具有透明度的 RGB 值](xref:Xamarin.Forms.Color.%23ctor(System.Double,System.Double,System.Double,System.Double))

参数 `double` 值，范围从0到1。

你还可以使用多种静态方法来创建 `Color` 值：

- 从0到 1 `double` RGB 值的[`Color.FromRgb`](xref:Xamarin.Forms.Color.FromRgb(System.Double,System.Double,System.Double))
- [`Color.FromRgb`](xref:Xamarin.Forms.Color.FromRgb(System.Int32,System.Int32,System.Int32)) 0 到255之间的整数 RGB 值
- 为具有透明度 `double` RGB 值[`Color.FromRgba`](xref:Xamarin.Forms.Color.FromRgba(System.Double,System.Double,System.Double,System.Double))
- [`Color.FromRgba`](xref:Xamarin.Forms.Color.FromRgba(System.Int32,System.Int32,System.Int32,System.Int32))具有透明度的整数 RGB 值
- 用透明度 `double` HSL 值的[`Color.FromHsla`](xref:Xamarin.Forms.Color.FromHsla(System.Double,System.Double,System.Double,System.Double))
- [`Color.FromUint`](xref:Xamarin.Forms.Color.FromUint(System.UInt32))计算为（B + 256 \* （G + 256 \* （R + 256 \* a））的 `uint` 值）
- [`Color.FromHex`](xref:Xamarin.Forms.Color.FromHex(System.String))以 "#AARRGGBB"、"#RRGGBB" 或 "#ARGB" 或 "#RGB" 格式表示的十六进制数字的 `string` 格式，其中每个字母对应于 alpha、红色、绿色和蓝色通道的十六进制数字。 此方法主要用于 XAML 颜色转换[，如第7章、xaml 与代码](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter07.md)中所述。

创建后，`Color` 值是不可变的。 可从以下属性获取颜色特征：

- [`R`](xref:Xamarin.Forms.Color.R)
- [`G`](xref:Xamarin.Forms.Color.G)
- [`B`](xref:Xamarin.Forms.Color.B)
- [`A`](xref:Xamarin.Forms.Color.A)
- [`Hue`](xref:Xamarin.Forms.Color.Hue)
- [`Saturation`](xref:Xamarin.Forms.Color.Saturation)
- [`Luminosity`](xref:Xamarin.Forms.Color.Luminosity)

这些都是介于0到1之间的所有 `double` 值。

`Color` 还为常见颜色定义240的公共静态只读字段。 撰写本书时，只有17个常见颜色可用。

其他公共静态只读字段定义了将所有颜色通道设置为零的颜色：

- [`Color.Transparent`](xref:Xamarin.Forms.Color.Transparent)

多个实例方法允许修改现有颜色以创建新颜色：

- [`AddLuminosity`](xref:Xamarin.Forms.Color.AddLuminosity(System.Double))
- [`MultiplyAlpha`](xref:Xamarin.Forms.Color.MultiplyAlpha(System.Double))
- [`WithHue`](xref:Xamarin.Forms.Color.WithHue(System.Double))
- [`WithLuminosity`](xref:Xamarin.Forms.Color.WithLuminosity(System.Double))
- [`WithSaturation`](xref:Xamarin.Forms.Color.WithSaturation(System.Double))

最后，两个静态只读属性定义特殊颜色值：

- [`Color.Default`](xref:Xamarin.Forms.Color.Default)，所有通道均设置为 &ndash;1
- [`Color.Accent`](xref:Xamarin.Forms.Color.Accent)

`Color.Default` 旨在强制实施平台的配色方案，因此在不同平台上的不同上下文中具有不同的含义。 默认情况下，平台配色方案为：

- iOS：浅背景上的黑色文本
- Android：深色背景上的浅文本（书籍中的浅文本）或浅色背景上的深色文本（在示例代码存储库的**主**分支中通过 AppCompat 的材料设计）
- UWP：浅背景上的黑色文本

`Color.Accent` 值将导致平台特定的颜色（有时是用户可选择的）颜色在深色或浅色背景上可见。

## <a name="changing-the-application-color-scheme"></a>更改应用程序配色方案

各种平台都有默认的配色方案，如以上列表中所示。

以 Android 为目标时，可以通过在 AppCompat 文件中指定浅色主题，或通过[添加和材料设计](~/xamarin-forms/platform/android/appcompat-material-design.md)来切换到暗色方案。

对于 Windows 平台，用户通常选择颜色主题，但你可以将 `RequestedTheme` 属性集添加到平台的 App.config 文件中的 `Light` 或 `Dark`。 默认情况下，UWP 项目中的 App.config 文件包含一个设置为 `Light``RequestedTheme` 特性。

## <a name="font-sizes-and-attributes"></a>字号和属性

将 `Label` 的[`FontFamily`](xref:Xamarin.Forms.Label.FontFamily)属性设置为字符串（如 "罗马罗马"）以选择字体系列。 但是，您需要指定特定平台支持的字体系列，而这些平台在这方面是不一致的。

将 `Label` 的[`FontSize`](xref:Xamarin.Forms.Label.FontSize)属性设置为用于指定字体的大致高度的 `double`。 有关智能选择字体大小的详细信息，请参阅[第5章，处理大小](chapter05.md)。

还可以获取几个预设平台相关字体大小之一。 静态[`Device.GetNamedSize`](xref:Xamarin.Forms.Device.GetNamedSize(Xamarin.Forms.NamedSize,System.Type))方法和[重载](xref:Xamarin.Forms.Device.GetNamedSize(Xamarin.Forms.NamedSize,Xamarin.Forms.Element))都返回适合于平台的 `double` 字体大小值（基于[`NamedSize`](xref:Xamarin.Forms.NamedSize)枚举的成员）（[`Default`](xref:Xamarin.Forms.NamedSize.Default)、 [`Micro`](xref:Xamarin.Forms.NamedSize.Micro)、 [`Small`](xref:Xamarin.Forms.NamedSize.Small)、 [`Medium`](xref:Xamarin.Forms.NamedSize.Medium)和[`Large`](xref:Xamarin.Forms.NamedSize.Large)）。 从 `Medium` 成员返回的值不一定与 `Default`相同。 [**NamedFontSizes**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/NamedFontSizes)示例以这些命名大小显示文本。

将 `Label` 的[`FontAttributes`](xref:Xamarin.Forms.Label.FontAttributes)属性设置为这些[`FontAttributes`](xref:Xamarin.Forms.FontAttributes)枚举、 [`Bold`](xref:Xamarin.Forms.FontAttributes.Bold)、 [`Italic`](xref:Xamarin.Forms.FontAttributes.Italic)或[`None`](xref:Xamarin.Forms.FontAttributes.None)的成员。 可以将C# `Bold` 和 `Italic` 成员与按位 or 运算符组合在一起。

## <a name="formatted-text"></a>带格式文本

到目前为止，所有示例中，由 `Label` 显示的整个文本已进行统一格式设置。 若要更改文本字符串中的格式，请不要设置 `Label`的 `Text` 属性。 相反，请将[`FormattedText`](xref:Xamarin.Forms.Label.FormattedText)属性设置为[`FormattedString`](xref:Xamarin.Forms.FormattedString)类型的对象。

`FormattedString` 具有一个[`Spans`](xref:Xamarin.Forms.FormattedString.Spans)属性，该属性是[`Span`](xref:Xamarin.Forms.Span)对象的集合。 每个 `Span` 对象都有其自己的[`Text`](xref:Xamarin.Forms.Span.Text)、 [`FontFamily`](xref:Xamarin.Forms.Span.FontFamily)、 [`FontSize`](xref:Xamarin.Forms.Span.FontSize)、 [`FontAttributes`](xref:Xamarin.Forms.Span.FontAttributes)、 [`ForegroundColor`](xref:Xamarin.Forms.Span.ForegroundColor)和[`BackgroundColor`](xref:Xamarin.Forms.Span.BackgroundColor)属性。

[**VariableFormattedText**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/VarFormText)示例演示如何对单个文本行使用 `FormattedText` 属性， [**VariableFormattedParagraph**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/VarFormPara)演示整个段落的技术，如下所示：

[![可变格式段落的三向屏幕截图](images/ch03fg06-small.png "可变格式标签文本")](images/ch03fg06-large.png#lightbox "可变格式标签文本")

[**NamedFontSizes**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/NamedFontSizes)程序使用单个 `Label` 和 `FormattedString` 对象来显示每个平台的所有命名字体大小。

## <a name="related-links"></a>相关链接

- [第3章全文（PDF）](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch03-Apr2016.pdf)
- [第3章示例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03)
- [第 3 F#章示例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/FS)
- [标签](~/xamarin-forms/user-interface/text/label.md)
- [使用颜色](~/xamarin-forms/user-interface/colors.md)
