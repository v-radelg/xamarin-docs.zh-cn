---
title: 在 Xamarin.Forms 中 XAML 命名空间
description: XAML 使用的命名空间声明的 xmlns XML 属性。 本文介绍 XAML 命名空间语法，并演示如何声明 XAML 命名空间以访问的类型。
ms.prod: xamarin
ms.assetid: C03B5553-B199-4A19-9F0F-E5BCE1DB268F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/21/2018
ms.openlocfilehash: be6154631b8b51ec61feb4c713d925ff30505b7d
ms.sourcegitcommit: 93c9fe61eb2cdfa530960b4253eb85161894c882
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/07/2019
ms.locfileid: "55831750"
---
# <a name="xaml-namespaces-in-xamarinforms"></a>在 Xamarin.Forms 中 XAML 命名空间

_XAML 使用的命名空间声明的 xmlns XML 属性。本文介绍 XAML 命名空间语法，并演示如何声明 XAML 命名空间以访问的类型。_

## <a name="overview"></a>概述

有两个都是 XAML 文件的根元素中的 XAML 命名空间声明。 第一个定义默认命名空间，如下面的 XAML 代码示例中所示：

```csharp
xmlns="http://xamarin.com/schemas/2014/forms"
```

默认命名空间指定在没有任何前缀的 XAML 文件中定义的元素引用 Xamarin.Forms 类，如[ `ContentPage` ](xref:Xamarin.Forms.ContentPage)。

第二个命名空间声明使用`x`前缀，如下面的 XAML 代码示例中所示：

```csharp
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
```

XAML 使用前缀来引用命名空间中的类型时要使用的前缀与声明非默认命名空间。 `x`命名空间声明指定带前缀的 XAML 中定义元素`x`用于元素和属性是固有的 XAML （特别是 2009 XAML 规范）。

下表概括了`x`Xamarin.Forms 支持的命名空间属性：

|构造|描述|
|--- |--- |
|`x:Arguments`|指定非默认构造函数或工厂方法对象声明的构造函数自变量。|
|`x:Class`|指定在 XAML 中定义的类的命名空间和类。 类名必须与匹配的代码隐藏文件的类名。 请注意，此构造只能出现在 XAML 文件的根元素。|
|`x:DataType`|指定的 XAML 元素，它的子级，将绑定到的对象的类型。|
|`x:FactoryMethod`|指定可用于初始化对象的工厂方法。|
|`x:FieldModifier`|指定的命名 XAML 元素生成的字段的访问级别。|
|`x:Key`|指定的每个资源的唯一用户键`ResourceDictionary`。 键的值用于检索 XAML 资源，并通常用作参数`StaticResource`标记扩展。|
|`x:Name`|指定的 XAML 元素的运行时对象名称。 设置`x:Name`类似于在代码中声明变量。|
|`x:TypeArguments`|指定的泛型类型参数的泛型类型构造函数。|

有关详细信息`x:DataType`属性，请参阅[编译绑定](~/xamarin-forms/app-fundamentals/data-binding/compiled-bindings.md)。 有关详细信息`x:FieldModifier`属性，请参阅[字段修饰符](~/xamarin-forms/xaml/field-modifiers.md)。 有关详细信息`x:Arguments`， `x:FactoryMethod`，并`x:TypeArguments`特性，请参见[在 XAML 中传递参数](~/xamarin-forms/xaml/passing-arguments.md)。

在 XAML 中，命名空间声明从父元素继承到子元素。 因此，在 XAML 文件的根元素中定义一个命名空间，该文件中的所有元素都继承的命名空间声明。

## <a name="declaring-namespaces-for-types"></a>类型声明的命名空间

可以使用公共语言运行时 (CLR) 命名空间名称和程序集名称 （可选） 指定的命名空间声明中声明具有前缀的 XAML 命名空间，在 XAML 中引用类型。 这被通过定义以下中的关键字命名空间声明的值：

- **clr 命名空间：** 或**使用：** – 在包含要将公开为 XAML 元素的类型的程序集内的 CLR 命名空间声明。 此关键字是必需的。
- **程序集 =** – 包含所引用的 CLR 命名空间的程序集。 此值为程序集，不带文件扩展名的名称。 应为包含将引用程序集的 XAML 文件的项目文件中的引用建立对程序集的路径。 此关键字，则可省略**clr 命名空间**值是引用类型的应用程序代码在同一程序集中。

请注意，字符分隔`clr-namespace`或`using`标记和其值是一个冒号，而字符分隔`assembly`标记和其值是一个等号。 要使用两个标记之间的字符是分号。

下面的代码示例演示了一个 XAML 命名空间声明：

```xaml
<ContentPage ... xmlns:local="clr-namespace:HelloWorld" ...>
  ...
</ContentPage>
```

或者，这可以编写为：

```xaml
<ContentPage ... xmlns:local="using:HelloWorld" ...>
  ...
</ContentPage>
```

`local`前缀是用来指示命名空间中的类型是应用程序的本地约定。 或者，如果类型属于不同程序集，程序集名称也应被定义在命名空间声明中，如以下 XAML 代码示例所示：

```xaml
<ContentPage ... xmlns:behaviors="clr-namespace:Behaviors;assembly=BehaviorsLibrary" ...>
  ...
</ContentPage>
```

当将从导入的命名空间，类型的实例声明为以下 XAML 代码示例所示，然后指定命名空间前缀：

```xaml
<ListView ...>
  <ListView.Behaviors>
    <behaviors:EventToCommandBehavior EventName="ItemSelected" ... />
  </ListView.Behaviors>
</ListView>
```

有关定义自定义命名空间架构信息，请参阅[XAML 自定义 Namespace 架构](custom-namespace-schemas.md)。

## <a name="summary"></a>总结

本文引入 XAML 命名空间语法，并说明了如何以声明要访问的类型的 XAML 命名空间。 XAML 使用`xmlns`XML 属性的命名空间声明和类型，可以引用在 XAML 中声明具有前缀的 XAML 命名空间。

## <a name="related-links"></a>相关链接

- [在 XAML 中传递自变量](~/xamarin-forms/xaml/passing-arguments.md)
