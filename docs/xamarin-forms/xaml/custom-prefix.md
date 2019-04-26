---
title: 建议在 Xamarin.Forms 中的前缀的 XAML Namespace
description: XmlnsPrefixAttribute 类可由控件作者，用于指定要关联的 XAML 命名空间，XAML 用法的建议的前缀。
ms.prod: xamarin
ms.assetid: 7B315BEC-7A35-48F4-A9C7-EF40255E95FF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/28/2019
ms.openlocfilehash: 33f18b3f9c9ddb6ab31ca92e2f192ffad783ec0c
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61075085"
---
# <a name="xaml-namespace-recommended-prefixes-in-xamarinforms"></a>建议在 Xamarin.Forms 中的前缀的 XAML Namespace

`XmlnsPrefixAttribute`类可由控件创作者指定要关联的 XAML 命名空间，XAML 用法的建议的前缀。 前缀时，可以支持对象树序列化为 XAML，或与设计环境进行交互时，具有 XAML 编辑功能。 例如：

- 可以使用 XAML 文本编辑器`XmlnsPrefixAttribute`作为初始的 XAML 命名空间的提示`xmlns`映射。
- 可以使用 XAML 设计环境`XmlnsPrefixAttribute`拖动对象从工具箱和到可视化设计图面上时，向 XAML 添加映射。

建议应在程序集级别定义的命名空间前缀`XmlnsPrefixAttribute`构造函数，它采用两个参数： 一个字符串，指定的 XAML 命名空间中，标识符和一个字符串，指定建议的前缀：

```csharp
[assembly: XmlnsPrefix("http://xamarin.com/schemas/2014/forms", "xf")]
```

前缀应使用短字符串，因为前缀通常应用于所有来自 XAML 命名空间的序列化元素。 因此，前缀字符串的长度可以具有的序列化的 XAML 输出大小产生明显的影响。

> [!NOTE]
> 多个`XmlnsPrefixAttribute`可以应用于程序集。 例如，如果定义多个 XAML 命名空间类型的程序集，您可以定义每个 XAML 命名空间不同的前缀值。

## <a name="related-links"></a>相关链接

- [XAML 自定义命名空间架构](custom-namespace-schemas.md)
- [在 Xamarin.Forms 中 XAML 命名空间](namespaces.md)
