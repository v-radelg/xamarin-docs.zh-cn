---
title: Xamarin 中的 XAML 字段修饰符
description: X:FieldModifier namespace 特性指定已命名的 XAML 元素的生成字段的访问级别。
ms.prod: xamarin
ms.assetid: 12357CE0-3C11-4B62-947F-72DB6DFC23A2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/02/2019
ms.openlocfilehash: 0f6050de943ca9878cf41b448d44bf222689be56
ms.sourcegitcommit: c6e56545eafd8ff9e540d56aba32aa6232c5315f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/02/2019
ms.locfileid: "68739449"
---
# <a name="xaml-field-modifiers-in-xamarinforms"></a>Xamarin 中的 XAML 字段修饰符

`x:FieldModifier` Namespace 特性指定为命名 XAML 元素生成的字段的访问级别。 特性的有效值为:

- `private`–指定 XAML 元素的生成字段只能在声明它的类的主体内访问。
- `public`–指定 XAML 元素的生成字段没有访问限制。
- `protected`–指定 XAML 元素的生成字段可在其类和派生类实例内访问。
- `internal`–指定只能在同一程序集内的类型内访问 XAML 元素的生成字段。
- `notpublic`–指定只能在同一程序集内的类型内访问 XAML 元素的生成字段。

默认情况下, 如果未设置该属性的值, 则该元素的生成字段将为`private`。

> [!NOTE]
> 特性的值可以使用任何大小写, 因为它将按 Xamarin 转换为小写形式。

若要处理某个`x:FieldModifier`属性, 必须满足以下条件:

- 顶级 XAML 元素必须是有效`x:Class`的。
- 当前 XAML 元素具有指定的`x:Name` 。

以下 XAML 显示了设置属性的示例:

```xaml
<Label x:Name="privateLabel" />
<Label x:Name="internalLabel" x:FieldModifier="internal" />
<Label x:Name="publicLabel" x:FieldModifier="public" />
```

> [!IMPORTANT]
> `x:FieldModifier`特性不能用于指定 XAML 类的访问级别。
