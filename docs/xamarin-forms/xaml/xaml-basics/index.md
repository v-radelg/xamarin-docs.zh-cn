---
title: Xamarin.Forms XAML 基础知识
description: 本指南介绍如何开始使用移动设备的跨平台 XAML。 XAML 允许开发人员在 Xamarin.Forms 应用程序使用标记而不是代码中定义的用户界面。
ms.prod: xamarin
ms.assetid: 67CC2CD6-D10A-4B14-9696-1D3A410EFFBF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/25/2017
ms.openlocfilehash: d8b522ce75b2b594242dca167242ad0362f6cbfc
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/16/2019
ms.locfileid: "69528304"
---
# <a name="xamarinforms-xaml-basics"></a>Xamarin.Forms XAML 基础知识

[![下载示例](~/media/shared/download.png)下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xamlsamples)

XAML (Extensible Application Markup Language) 允许开发人员在 Xamarin.Forms 应用程序中使用标记（而不是代码）来定义用户界面。 XAML 永远不会需要在 Xamarin.Forms 程序中，但通常会更简洁和更直观地一致比等效的代码，并可能会非常有用。 XAML 特别适用于常用 MVVM (ViewModel) 应用程序体系结构:XAML 定义通过基于 XAML 的数据绑定链接到 ViewModel 代码的视图。

## <a name="xaml-basics-contents"></a>XAML 基础知识内容

* [概述](#Overview)
* [第 1 部分：XAML 入门](~/xamarin-forms/xaml/xaml-basics/get-started-with-xaml.md)
* [第 2 部分：基本 XAML 语法](~/xamarin-forms/xaml/xaml-basics/essential-xaml-syntax.md)
* [第 3 部分：XAML 标记扩展](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
* [第 4 部分：数据绑定基础知识](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)
* [第 5 部分：从数据绑定到 MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)

除了这些 XAML 基础知识文章中，您可以下载一书的章节[使用 Xamarin.Forms 创建移动应用](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md):

[![](images/cover-sml.png "封面")](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md)

很多书的章节中更加详细地介绍 XAML 主题包括：


| 一章 | 下载 | 总结 |
|---------|---------|---------|
| 第 7 章。 XAML vs。代码 | [下载 PDF](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch07-Apr2016.pdf) | [摘要](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter07.md) |
| 第 8 章。 代码和 XAML 协调工作 | [下载 PDF](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch08-Apr2016.pdf) | [摘要](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter08.md) |
| 第 10 章。 XAML 标记扩展 | [下载 PDF](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch10-Apr2016.pdf) | [摘要](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter10.md) |
| 第 18 章。 MVVM | [下载 PDF](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch18-Apr2016.pdf) | [摘要](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter18.md) |

可以是这些章节[免费下载的](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md)。

<a name="Overview" />

## <a name="overview"></a>概述

XAML 是基于 XML 的语言由 Microsoft 创建的作为编程代码实例化和初始化对象，并组织这些对象在父-子层次结构中的替代方法。 XAML 适应多种技术在.NET framework 中，但它在定义的 Windows Presentation Foundation (WPF)、 Silverlight、 Windows 运行时和通用 Windows 中的用户界面布局中找到其最大的实用程序平台 (UWP)。

XAML 也是 Xamarin.Forms 的跨平台本机基于编程接口为 iOS、 Android 和 UWP 的一部分移动设备。 在 XAML 文件中，Xamarin.Forms 开发人员可以定义用户界面用作所有 Xamarin.Forms 视图、 布局和页面，以及自定义类。 可以编译或可执行文件中嵌入的 XAML 文件。 无论哪种方式，分析 XAML 信息在生成时查找命名的对象，并再次在运行时来实例化和初始化对象，并建立这些对象和编程代码之间的链接。

XAML 具有几大优势，等效的代码：

- XAML 通常会更简洁和可读比等效的代码。
- 在 XML 中固有的父-子层次结构允许 XAML 来模拟更 visual 清楚用户界面对象的父-子层次结构。
- XAML 可以轻松地手动编写的程序员，但还有助于为可工具化并通过可视化设计工具生成。

当然，也有缺点，主要与是固有的标记语言的限制：

- XAML 不能包含代码。 必须在代码文件中定义所有事件处理程序。
- XAML 不能包含重复处理的循环。 (但是，多个 Xamarin.Forms 视觉对象 — 最值得注意的是[ `ListView` ](xref:Xamarin.Forms.ListView) — 可以生成多个子级中的对象基于其`ItemsSource`集合。)
- XAML 不能包含有条件处理 （但是，数据绑定可以引用，可有效地处理某些条件的代码基于绑定转换器。）
- XAML 通常无法实例化类未定义无参数构造函数。 （但是，没有有时会解决此限制问题的方法。）
- XAML 通常不能调用方法。 （同样，此限制可以有时克服。）

还没有可视化设计器生成 XAML 在 Xamarin.Forms 应用程序。 所有 XAML 都必须手动编写的但没有[XAML 预览程序](~/xamarin-forms/xaml/xaml-previewer/index.md)。 新增到 XAML 的程序员可能想要频繁生成并运行其应用程序，尤其是之后可能不正确很明显的任何内容。 凭借大量经验在 XAML 中甚至开发人员知道试验有价值。

XAML 基本上是 XML，但 XAML 具有一些独特的语法功能。 最重要的是：

- 属性元素
- 附加的属性
- 标记扩展

这些功能都*不*XML 扩展。 XAML 是完全合法的 XML。 但是，这些 XAML 语法功能独特的方式使用 XML。 它们详细讨论了中的文章，其中简要介绍如何使用 XAML 来实现 MVVM 得出结论。

## <a name="requirements"></a>要求

本文假定你熟悉 Xamarin.Forms 工作。 本文还假定熟悉 XML，包括了解使用 XML 命名空间声明和条款*元素*，*标记*，并*特性*。

当您熟悉使用 Xamarin.Forms 和 XML 时，开始读取[第 1 部分。开始使用 XAML](~/xamarin-forms/xaml/xaml-basics/get-started-with-xaml.md)。

## <a name="related-links"></a>相关链接

- [XamlSamples](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xamlsamples)
- [创建移动应用书籍](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md)
- [Xamarin.Forms 示例](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.Forms)
