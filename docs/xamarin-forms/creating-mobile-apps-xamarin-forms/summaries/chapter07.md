---
title: 第 7 章的摘要。 XAML 与代码
description: 使用 Xamarin.Forms 创建移动应用：第 7 章的摘要。 XAML 与代码
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: E91F387B-CE90-481C-8D90-CB25519BFD2B
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
ms.openlocfilehash: ce4dde3716176daf826678809339afb84c25d84a
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61334724"
---
# <a name="summary-of-chapter-7-xaml-vs-code"></a>第 7 章的摘要。 XAML 与代码

[![下载示例](~/media/shared/download.png)下载示例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07)

> [!NOTE]
> 此页上的说明表明其中 Xamarin.Forms 已脱离一书中介绍的内容的区域。

Xamarin.Forms 支持名为 Extensible Application Markup Language 的基于 XML 的标记语言或 XAML （读作"zammel"）。 XAML 可用于替代 C# 中，定义用户界面元素之间的绑定和基础数据内定义的 Xamarin.Forms 应用程序的用户界面布局。

## <a name="properties-and-attributes"></a>属性和特性

Xamarin.Forms 类和结构成为 XAML 中的 XML 元素，这些类和结构的属性将成为 XML 特性。 若要在 XAML 中实例化，类通常必须具有公共无参数构造函数。 在 XAML 中设置任何属性必须具有公共`set`访问器。

属性的基本数据类型 (`string`， `double`， `bool`，依次类推)，XAML 分析器使用标准`TryParse`方法将属性设置为这些类型。 XAML 分析器可以方便地处理枚举类型，并且如果使用的枚举类型进行标记，它可以将合并枚举成员`Flags`属性。

若要帮助 XAML 分析器，更复杂的类型 （或这些类型的属性） 可以包含[ `TypeConverterAttribute` ](xref:Xamarin.Forms.TypeConverterAttribute)标识派生的类[ `TypeConverter` ](xref:Xamarin.Forms.TypeConverter)它支持从转换对这些类型的字符串值。 例如， [ `ColorTypeConverter` ](xref:Xamarin.Forms.ColorTypeConverter)转换为颜色名称和字符串，如"#rrggbb"`Color`值。

## <a name="property-element-syntax"></a>属性元素语法

在 XAML，类和从其创建的对象都表示为 XML 元素。 这些参数称为*对象元素*。 大多数属性，这些对象表示为 XML 属性。 这些被称为*属性的特性*。

有时属性必须设置为一个对象，无法表示为一个简单的字符串。 在这种情况下，XAML 支持标记，调用*property 元素*，它包含的类名称和句点分隔的属性名称。 对象元素然后只能在一对属性元素标记中出现。

## <a name="adding-a-xaml-page-to-your-project"></a>将 XAML 页添加到你的项目

首次创建，或可以将 XAML 页添加到现有项目时，Xamarin.Forms 可移植类库可以包含一个 XAML 页面。 在添加新项对话框，选择的项的引用的 XAML 页面，或`ContentPage`和 XAML。 (不`ContentView`。)

> [!NOTE]
> Visual Studio 选项发生了更改编写的这一章。

创建两个文件： filename 扩展.xaml 的 XAML 文件和 C# 文件扩展名。 xaml.cs。 C# 文件通常称为*代码隐藏*的 XAML 文件。 代码隐藏文件是派生自的分部类定义`ContentPage`。 在生成时，XAML 进行分析，并为相同的类生成另一个分部类定义。 此生成的类包括一个名为方法`InitializeComponent`从代码隐藏文件的构造函数调用。

在运行时，在结束`InitializeComponent`调用所有已实例化并初始化就像他们已在 C# 代码创建 XAML 文件的元素。

XAML 文件中的根元素是`ContentPage`。 根标记包含至少两个 XML 命名空间声明，一个用于 Xamarin.Forms 元素以及其他定义`x`元素和所有 XAML 实现中的内部属性的前缀。 根标记还包含`x:Class`属性，用于指示命名空间和派生的类名称`ContentPage`。 此匹配的代码隐藏文件中的命名空间和类名称。

XAML 和代码的组合所示[ **CodePlusXaml** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07)示例。

## <a name="the-xaml-compiler"></a>XAML 编译器

Xamarin.Forms 具有 XAML 编译器，但其使用是可选的基于使用的[ `XamlCompilationAttribute` ](xref:Xamarin.Forms.Xaml.XamlCompilationAttribute)。 如果未编译 XAML，XAML 分析在生成时，并在 PCL 中，其中还进行分析，在运行时中嵌入的 XAML 文件。 如果编译 XAML，生成过程将 XAML 转换成二进制格式，并且该运行时过程更高效。

## <a name="platform-specificity-in-the-xaml-file"></a>XAML 文件中的平台特异性

在 XAML 中， [ `OnPlatform` ](xref:Xamarin.Forms.OnPlatform`1)类可用于选择依赖于平台的标记。 这是一个泛型类，必须使用实例化`x:TypeArguments`匹配目标类型的属性。 [ `OnIdiom` ](xref:Xamarin.Forms.OnIdiom`1)类十分类似，但使用少得多。

使用`OnPlatform`通讯簿已发布后已更改。 它最初具有名为的属性使用结合`iOS`， `Android`，和`WinPhone`。 现在用于子[ `On` ](xref:Xamarin.Forms.On)对象。 设置[ `Platform` ](xref:Xamarin.Forms.On.Platform)属性设置为一个字符串，包含具有公共一致`const`的字段[ `Device` ](xref:Xamarin.Forms.Device)类。 设置[ `Value` ](xref:Xamarin.Forms.On.Value)属性的值与一致`x:TypeArguments`属性的`OnPlatform`标记。

`OnPlatform` 中所示[ **ScaryColorList** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07/ScaryColorList)示例中，因此称为，因为它包含块几乎完全相同的 XAML。 此重复标记存在建议应可降低它的技术。

## <a name="the-content-property-attributes"></a>内容属性特性

某些属性元素频繁出现，如`<ContentPage.Content>`标记上的根元素`ContentPage`，或`<StackLayout.Children>`包含的子级的标记`StackLayout`。

每个类允许标识一个属性与[ `ContentPropertyAttribute` ](xref:Xamarin.Forms.ContentPropertyAttribute)类上。 对于此属性，则不需要的属性元素标记。 `ContentPage` 定义它的内容属性为`Content`，并`Layout<T>`(从其类`StackLayout`派生) 作为其内容属性定义`Children`。 这些属性元素标记不是必需的。

Property 元素的`Label`是`Text`。

## <a name="formatted-text"></a>带格式文本

[ **TextVariations** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07/TextVariations)示例包含设置的几个示例`Text`并`FormattedText`的属性`Label`。 在 XAML 中，`Span`对象显示为子级的`FormattedString`对象。

 如果多行字符串设置为`Text`属性，行尾字符都转换为空格字符，但多行字符串中出现的内容时，将保留行尾字符`Label`或`Label.Text`标记：

 [![共享的文本变体的三个屏幕截图](images/ch07fg03-small.png "格式的文本变体")](images/ch07fg03-large.png#lightbox "格式化文本变体")

## <a name="related-links"></a>相关链接

- [第 7 章全文 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch07-Apr2016.pdf)
- [第 7 章示例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07)
- [第 7 章F#示例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07/FS/CodePlusXaml)
- [XAML 基础知识](~/xamarin-forms/xaml/xaml-basics/index.md)
