---
title: 在 Xamarin.Forms 中 XAML 编译
description: 本文介绍如何 XAML 可以根据需要直接编译为中间语言 (IL) 使用 Xamarin.Forms XAML 编译器 (XAMLC)。
ms.prod: xamarin
ms.assetid: 9A2D10A6-5DFC-485F-A75A-2F7B98314025
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/22/2018
ms.openlocfilehash: 9567f3ad8d748a94a03cd1c86254072d4ba3bbdc
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61075111"
---
# <a name="xaml-compilation-in-xamarinforms"></a>在 Xamarin.Forms 中 XAML 编译

_XAML 可根据需要编译为中间语言 (IL) 使用 XAML 编译器 (XAMLC) 直接。_

XAML 编译提供了很多好处：

- 它会执行 XAML 的编译时检查，从而可向用户通知任何错误。
- 它会消除 XAML 元素的某些负载和实例化时间。
- 它通过不再包含 .xaml 文件，来帮助减小最终程序集的文件大小。

若要确保向后兼容性的默认情况下禁用 XAML 编译。 它可以在程序集和类级别启用通过添加[ `XamlCompilation` ](xref:Xamarin.Forms.Xaml.XamlCompilationAttribute)属性。

下面的代码示例演示如何在程序集级别启用 XAML 编译：

```csharp
using Xamarin.Forms.Xaml;
...
[assembly: XamlCompilation (XamlCompilationOptions.Compile)]
namespace PhotoApp
{
  ...
}
```

在此示例中，编译时检查的程序集内包含的所有 XAML 将执行，但在编译时而不是在运行时报告的 XAML 出现错误。 因此，`assembly`为前缀[ `XamlCompilation` ](xref:Xamarin.Forms.Xaml.XamlCompilationAttribute)属性指定该属性应用于整个程序集。

> [!NOTE]
> [ `XamlCompilation` ](xref:Xamarin.Forms.Xaml.XamlCompilationAttribute)属性和[ `XamlCompilationOptions` ](xref:Xamarin.Forms.Xaml.XamlCompilationOptions)枚举位于`Xamarin.Forms.Xaml`命名空间，必须导入使用它们。

下面的代码示例演示了在类级别启用 XAML 编译：

```csharp
using Xamarin.Forms.Xaml;
...
[XamlCompilation (XamlCompilationOptions.Compile)]
public class HomePage : ContentPage
{
  ...
}
```

在此示例中，编译时检查的 XAML 的`HomePage`类将在执行和错误报告作为编译过程的一部分。

> [!NOTE]
> 可以启用已编译的绑定，以提高 Xamarin.Forms 应用程序中的数据绑定性能。 有关详细信息，请参阅[编译绑定](~/xamarin-forms/app-fundamentals/data-binding/compiled-bindings.md)。

## <a name="related-links"></a>相关链接

- [`XamlCompilation`](xref:Xamarin.Forms.Xaml.XamlCompilationAttribute)
- [`XamlCompilationOptions`](xref:Xamarin.Forms.Xaml.XamlCompilationOptions)
