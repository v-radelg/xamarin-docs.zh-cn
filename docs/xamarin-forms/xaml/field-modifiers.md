---
title: 在 Xamarin.Forms 中 XAML 字段修饰符
description: X:fieldmodifier 命名空间特性指定的命名 XAML 元素生成的字段的访问级别。
ms.prod: xamarin
ms.assetid: 12357CE0-3C11-4B62-947F-72DB6DFC23A2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/18/2018
ms.openlocfilehash: 8be56524ec1c5331f30418fcc29a4bd2c26ccde1
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61075293"
---
# <a name="xaml-field-modifiers-in-xamarinforms"></a>在 Xamarin.Forms 中 XAML 字段修饰符

_`x:FieldModifier`命名空间属性指定为命名 XAML 元素生成的字段的访问级别。_

## <a name="overview"></a>概述

该属性的有效值为：

- `Public` – 指定的 XAML 元素所生成的字段是`public`。
- `NotPublic` – 指定的 XAML 元素所生成的字段是`internal`对程序集。

如果未设置该属性的值，所生成的字段的元素将`private`。

必须满足以下条件`x:FieldModifier`要处理的属性：

- XAML 中的顶级元素必须是有效`x:Class`。
- 当前的 XAML 元素具有`x:Name`指定。

以下 XAML 演示设置该属性的示例：

```xaml
<Label x:Name="privateLabel" />
<Label x:Name="internalLabel" x:FieldModifier="NotPublic" />
<Label x:Name="publicLabel" x:FieldModifier="Public" />
```

> [!NOTE]
> `x:FieldModifier`特性不能用于指定 XAML 类的访问级别。
