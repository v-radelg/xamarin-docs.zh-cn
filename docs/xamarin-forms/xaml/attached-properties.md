---
title: 附加属性
description: 本文介绍了附加属性，并演示如何创建并使用它们。
ms.prod: xamarin
ms.assetid: 6E9DCDC3-A0E4-46A6-BAA9-4FEB6DF8A5A8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/02/2016
ms.openlocfilehash: c1fb596abb580cf42edda21ab71f2c70de59a547
ms.sourcegitcommit: b23a107b0fe3d2f814ae35b52a5855b6ce2a3513
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2019
ms.locfileid: "65924560"
---
# <a name="attached-properties"></a>附加属性

[![下载示例](~/media/shared/download.png)下载示例](https://developer.xamarin.com/samples/xamarin-forms/Effects/ShadowEffect/)

_附加的属性是特殊类型的一个类中定义，但附加到其他对象的可绑定属性和可识别为属性的 XAML 中包含的类和属性名称之间以句点分隔。本文介绍了附加属性，并演示如何创建并使用它们。_

## <a name="overview"></a>概述

附加属性启用要为其自己的类未定义的属性分配值的对象。 例如，子元素可使用附加属性，以通知其父元素它们将在用户界面中显示。 [ `Grid` ](xref:Xamarin.Forms.Grid)控件允许的行和子级通过设置指定的列`Grid.Row`和`Grid.Column`附加属性。 `Grid.Row` 并`Grid.Column`是附加的属性，因为它们的子级的元素上设置`Grid`，而不是在`Grid`本身。

可绑定属性应作为附加属性在以下方案中实现：

- 定义类需要而不具有用于类可用的属性设置机制时。
- 当类表示一种服务，需要与其他类轻松地集成。

可绑定属性的详细信息，请参阅[可绑定属性](~/xamarin-forms/xaml/bindable-properties.md)。

## <a name="creating-and-consuming-an-attached-property"></a>创建和使用附加的属性

创建附加的属性的过程如下所示：

1. 创建[ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty)具有的一个实例[ `CreateAttached` ](xref:Xamarin.Forms.BindableProperty.CreateAttached*)方法重载。
1. 提供`static` `Get` *PropertyName*并`Set` *PropertyName*方法访问器作为附加属性。

### <a name="creating-a-property"></a>创建属性

在创建时使用的附加的属性在其他类型中，创建属性的类不需要派生自[ `BindableObject` ](xref:Xamarin.Forms.BindableObject)。 但是，*目标*访问器的属性应是的或派生自， [ `BindableObject` ](xref:Xamarin.Forms.BindableObject)。

可以通过声明创建附加的属性`public static readonly`类型的属性[ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty)。 可绑定属性应设置为返回值之一[ `BindableProperty.CreateAttached` ](xref:Xamarin.Forms.BindableProperty.CreateAttached(System.String,System.Type,System.Type,System.Object,Xamarin.Forms.BindingMode,Xamarin.Forms.BindableProperty.ValidateValueDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangedDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangingDelegate,Xamarin.Forms.BindableProperty.CoerceValueDelegate,Xamarin.Forms.BindableProperty.CreateDefaultValueDelegate))方法重载。 声明应在其所属的类，但之外的任何成员定义的正文内。

下面的代码演示附加属性的示例：

```csharp
public static readonly BindableProperty HasShadowProperty =
  BindableProperty.CreateAttached ("HasShadow", typeof(bool), typeof(ShadowEffect), false);
```

这将创建一个名为的附加的属性`HasShadow`，类型的`bool`。 该属性归`ShadowEffect`类，并具有默认值为`false`。 附加属性的命名约定是附加的属性标识符中指定属性名称必须匹配`CreateAttached`方法，使用"属性"追加到它。 因此，在上面的示例中，附加的属性标识符是`HasShadowProperty`。

有关创建可绑定属性，包括可以在创建期间，指定的参数的详细信息请参阅[创建和使用可绑定属性](~/xamarin-forms/xaml/bindable-properties.md#consuming-bindable-property)。

### <a name="creating-accessors"></a>创建访问器

静态`Get` *PropertyName*并`Set` *PropertyName*方法都是必需的因为附加属性的访问器，否则将无法使用属性系统附加的属性。 `Get` *PropertyName*访问器应符合以下签名：

```csharp
public static valueType GetPropertyName(BindableObject target)
```

`Get` *PropertyName*取值函数的返回值包含在相应应`BindableProperty`字段的附加属性。 这可以通过调用来实现[ `GetValue` ](xref:Xamarin.Forms.BindableObject.GetValue(Xamarin.Forms.BindableProperty))方法，传入要获取的值的可绑定的属性标识符，然后将结果值输出到所需的类型强制转换。

`Set` *PropertyName*访问器应符合以下签名：

```csharp
public static void SetPropertyName(BindableObject target, valueType value)
```

`Set` *PropertyName*访问器应将相应的值设置`BindableProperty`字段的附加属性。 这可以通过调用来实现[ `SetValue` ](xref:Xamarin.Forms.BindableObject.SetValue(Xamarin.Forms.BindableProperty,System.Object))方法，传入在其上设置值和要设置的值的可绑定的属性标识符。

这两个访问器的*目标*对象应具有的或者派生[ `BindableObject` ](xref:Xamarin.Forms.BindableObject)。

下面的代码示例演示的访问器`HasShadow`附加属性：

```csharp
public static bool GetHasShadow (BindableObject view)
{
  return (bool)view.GetValue (HasShadowProperty);
}

public static void SetHasShadow (BindableObject view, bool value)
{
  view.SetValue (HasShadowProperty, value);
}
```

### <a name="consuming-an-attached-property"></a>使用附加的属性

一旦创建附加的属性后，可以从 XAML 或代码使用它。 在 XAML，这被通过使用命名空间声明，该值指示公共语言运行时 (CLR) 命名空间名称和 （可选） 程序集名称声明具有前缀的命名空间。 有关详细信息，请参阅[XAML 命名空间](~/xamarin-forms/xaml/namespaces.md)。

下面的代码示例演示了包含所引用的自定义类型的应用程序代码在同一程序集中定义的附加的属性的自定义类型的 XAML 命名空间：

```xaml
<ContentPage ... xmlns:local="clr-namespace:EffectsDemo" ...>
  ...
</ContentPage>
```

当特定控件上的附加的属性设置为以下 XAML 代码示例所示，然后使用命名空间声明：

```xaml
<Label Text="Label Shadow Effect" local:ShadowEffect.HasShadow="true" />
```

以下代码示例显示相应的 C# 代码：

```csharp
var label = new Label { Text = "Label Shadow Effect" };
ShadowEffect.SetHasShadow (label, true);
```

### <a name="consuming-an-attached-property-with-a-style"></a>使用附加的属性的方式

由样式之前，还可以将附加的属性添加到控件。 以下 XAML 代码示例所示*显式*使用的样式`HasShadow`附加属性，可以应用于[ `Label` ](xref:Xamarin.Forms.Label)控件：

```xaml
<Style x:Key="ShadowEffectStyle" TargetType="Label">
  <Style.Setters>
    <Setter Property="local:ShadowEffect.HasShadow" Value="true" />
  </Style.Setters>
</Style>
```

[ `Style` ](xref:Xamarin.Forms.Style)可应用于[ `Label` ](xref:Xamarin.Forms.Label)通过设置其[ `Style` ](xref:Xamarin.Forms.NavigableElement.Style)属性设置为`Style`实例使用`StaticResource`标记扩展，如下面的代码示例中所示：

```xaml
<Label Text="Label Shadow Effect" Style="{StaticResource ShadowEffectStyle}" />
```

有关样式的详细信息，请参阅[样式](~/xamarin-forms/user-interface/styles/index.md)。

## <a name="advanced-scenarios"></a>高级的方案

在创建附加的属性时，有大量可选参数，可设置为启用高级附加的属性的方案。 这包括检测属性更改、 验证属性值，以及将强制转换属性值。 有关详细信息，请参阅[高级方案](~/xamarin-forms/xaml/bindable-properties.md#advanced)。

## <a name="summary"></a>总结

本文介绍了附加属性，并演示了如何创建并使用它们。 附加的属性是一种特殊的可绑定属性，但附加到其他对象，并可识别在 XAML 中的一个类中定义为包含的类和一个句点分隔的属性名称的属性。


## <a name="related-links"></a>相关链接

- [可绑定属性](~/xamarin-forms/xaml/bindable-properties.md)
- [XAML 命名空间](~/xamarin-forms/xaml/namespaces.md)
- [阴影效果 （示例）](https://developer.xamarin.com/samples/xamarin-forms/Effects/ShadowEffect/)
- [BindableProperty](xref:Xamarin.Forms.BindableProperty)
- [BindableObject](xref:Xamarin.Forms.BindableObject)
