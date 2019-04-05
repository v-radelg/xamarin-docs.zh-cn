---
title: 可扩展应用程序标记语言 (XAML)
description: XAML 是一种声明性标记语言，可用于定义用户界面。 使用 XAML 语法，而在单独的代码隐藏文件中定义的运行时行为的 XML 文件中定义的用户界面。
ms.prod: xamarin
ms.assetid: CD30EECC-8AC1-4CF5-A4FE-348420A6231E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/03/2019
ms.openlocfilehash: 1f6bdfb1aed87f8a2dabdc0efcea3a1b8bf2ecf2
ms.sourcegitcommit: be51b459a0a148ae3adca31d7599f53f7b2c3a68
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2019
ms.locfileid: "59019342"
---
# <a name="extensible-application-markup-language-xaml"></a>可扩展应用程序标记语言 (XAML)

_XAML 是一种声明性标记语言，可用于定义用户界面。 使用 XAML 语法，而在单独的代码隐藏文件中定义的运行时行为的 XML 文件中定义的用户界面。_

> [!VIDEO https://youtube.com/embed/H6UOrSyhTEE]

**Evolve 2016:成为 XAML Master**

> [!NOTE]
> 试用[XAML 标准预览版](standard/index.md)

## [<a name="xaml-controls"></a>XAML 控件](xaml-controls.md)

在 Xamarin.Forms 中定义的视图的所有可从 XAML 文件引用。

<a name="xaml" />

## [<a name="xaml-basics"></a>XAML 基础](xaml-basics/index.md)

XAML 允许开发人员在 Xamarin.Forms 应用程序使用标记而不是代码中定义的用户界面。 XAML 永远不会需要 Xamarin.Forms 程序中，但它是工具化程度，并通常会更直观地一致和比等效的代码更简洁。 XAML 是非常适合于与常用的模型-视图-视图模型 (MVVM) 应用程序体系结构一起使用：XAML 定义通过基于 XAML 的数据绑定链接到 ViewModel 代码的视图。

## [<a name="xaml-compilation"></a>XAML 编译](xamlc.md)

XAML 可以根据需要使用 XAML 编译器 (XAMLC) 直接编译为中间语言 (IL)。 本文介绍如何使用 XAMLC 和它的好处。

## [<a name="xaml-previewer"></a>XAML 预览程序](xaml-previewer/index.md)

[XAML 预览程序](~/xamarin-forms/xaml/xaml-previewer/index.md)呈现页的并排方案使用 XAML 标记，从而可以查看您的呈现将如你所键入的用户界面的实时预览。

## [<a name="xaml-namespaces"></a>XAML 命名空间](namespaces.md)

XAML 使用`xmlns`XML 属性的命名空间声明。 本文介绍 XAML 命名空间语法，并演示如何声明 XAML 命名空间以访问的类型。

## [<a name="xaml-custom-namespace-schemas"></a>XAML 自定义命名空间架构](custom-namespace-schemas.md)

可以使用定义 XAML 自定义命名空间架构`XmlnsDefinitionAttribute`类，该类指定自定义 URL 和一个或多个 CLR 命名空间之间的映射。 然后可以在 XAML 命名空间声明中使用自定义命名空间架构。

## [<a name="xaml-namespace-recommended-prefixes"></a>建议的 XAML 命名空间前缀](custom-prefix.md)

`XmlnsPrefixAttribute`类可由控件创作者指定要关联的 XAML 命名空间，XAML 用法的建议的前缀。

## [<a name="xaml-markup-extensions"></a>XAML 标记扩展](markup-extensions/index.md)

XAML 包括将属性设置为值或超出什么可以使用简单的字符串表示的对象的标记扩展。 其中包括常量、 静态属性和字段、 资源字典和数据绑定引用。

## [<a name="field-modifiers"></a>字段修饰符](field-modifiers.md)

`x:FieldModifier`命名空间属性指定为命名 XAML 元素生成的字段的访问级别。

## [<a name="passing-arguments"></a>传递参数](passing-arguments.md)

XAML 可用于将参数传递到非默认构造函数或工厂方法。 本文演示如何使用可用于将参数传递到构造函数，以调用工厂方法，并指定泛型参数的类型的 XAML 属性。

## [<a name="bindable-properties"></a>可绑定属性](bindable-properties.md)

在 Xamarin.Forms 中，公共语言运行时 (CLR) 属性的功能扩展可绑定属性。 可绑定属性是属性的特殊类型，其中 Xamarin.Forms 属性系统跟踪属性的值。 本文介绍了可绑定属性，并演示如何创建并使用它们。

## [<a name="attached-properties"></a>附加属性](attached-properties.md)

附加的属性是特殊类型的一个类中定义，但附加到其他对象的可绑定属性和可识别为属性的 XAML 中包含的类和属性名称之间以句点分隔。 本文介绍了附加属性，并演示如何创建并使用它们。

## [<a name="resource-dictionaries"></a>资源字典](resource-dictionaries.md)

XAML 资源是可以多次使用的对象的定义。 一个[ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)允许在单个位置中，定义和重新整个 Xamarin.Forms 应用程序中使用的资源。 本文演示了如何创建和使用`ResourceDictionary`，以及如何合并一个`ResourceDictionary`到另一个。

## [<a name="loading-xaml-at-runtime"></a>在运行时加载 XAML](runtime-load.md)

可以加载和分析在运行时可使用 XAML [ `LoadFromXaml` ](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*)扩展方法。
