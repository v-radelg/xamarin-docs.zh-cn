---
title: 可绑定属性
description: 本文介绍了可绑定属性，并演示如何创建并使用它们。
ms.prod: xamarin
ms.assetid: 1EE869D8-6FE1-45CA-A0AD-26EC7D032AD7
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/02/2016
ms.openlocfilehash: 50efd6b37d70fa835436c28c73b3d4f9fc6c7c83
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70758094"
---
# <a name="bindable-properties"></a>可绑定属性

[![下载示例](~/media/shared/download.png)下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/behaviors-eventtocommandbehavior)

_在 Xamarin.Forms 中，公共语言运行时 (CLR) 属性的功能扩展可绑定属性。可绑定属性是属性的特殊类型，其中 Xamarin.Forms 属性系统跟踪属性的值。本文介绍了可绑定属性，并演示如何创建并使用它们。_

## <a name="overview"></a>概述

可绑定属性扩展通过将数据备份具有的属性的 CLR 属性功能[ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty)类型，而不是支持使用字段属性。 可绑定属性的用途是提供支持数据绑定、 样式、 模板的属性系统，并通过父-子关系的值设置。 此外，可绑定属性可以提供默认值，验证属性值和监视属性更改回调。

属性应作为可绑定属性，以支持一个或多个以下功能：

- 作为一个有效*目标*数据绑定的属性。
- 通过将属性设置[样式](~/xamarin-forms/user-interface/styles/index.md)。
- 提供默认属性值不同于属性的类型的默认值。
- 正在验证属性的值。
- 监视属性更改。

Xamarin.Forms 的可绑定属性的示例包括[ `Label.Text` ](xref:Xamarin.Forms.Label.Text)， [ `Button.BorderRadius` ](xref:Xamarin.Forms.Button.BorderRadius)，以及[ `StackLayout.Orientation` ](xref:Xamarin.Forms.StackLayout.Orientation)。 每个可绑定属性都有一个相应`public static readonly`类型的属性[ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty)相同的类上公开，可绑定属性的标识符。 例如，相应的可绑定属性标识符`Label.Text`属性是[ `Label.TextProperty` ](xref:Xamarin.Forms.Label.TextProperty)。

<a name="consuming-bindable-property" />

## <a name="creating-and-consuming-a-bindable-property"></a>创建和使用可绑定属性

创建可绑定属性的过程如下所示：

1. 创建[ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty)具有的一个实例[ `BindableProperty.Create` ](xref:Xamarin.Forms.BindableProperty.Create*)方法重载。
1. 定义属性的访问器[ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty)实例。

请注意，所有[ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty)必须在 UI 线程上创建实例。 这意味着在 UI 线程运行的代码可以获取或设置可绑定属性的值。 但是，`BindableProperty`实例可以从其他线程封送到 UI 线程中访问[ `Device.BeginInvokeOnMainThread` ](xref:Xamarin.Forms.Device.BeginInvokeOnMainThread(System.Action))方法。

### <a name="creating-a-property"></a>创建属性

若要创建`BindableProperty`实例，包含的类必须派生自[ `BindableObject` ](xref:Xamarin.Forms.BindableObject)类。 但是，`BindableObject`类是高类层次结构中，因此大多数类用于用户界面功能支持可绑定属性。

可以通过声明创建可绑定的属性`public static readonly`类型的属性[ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty)。 可绑定属性应设置为返回值之一[ `BindableProperty.Create` ](xref:Xamarin.Forms.BindableProperty.Create(System.String,System.Type,System.Type,System.Object,Xamarin.Forms.BindingMode,Xamarin.Forms.BindableProperty.ValidateValueDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangedDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangingDelegate,Xamarin.Forms.BindableProperty.CoerceValueDelegate,Xamarin.Forms.BindableProperty.CreateDefaultValueDelegate))方法重载。 声明应为体内[ `BindableObject` ](xref:Xamarin.Forms.BindableObject)派生的类，但之外的任何成员定义。

创建时，则必须指定至少一个标识符[ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty)，以及以下参数：

- 名称[ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty)。
- 属性的类型。
- 所属对象的类型。
- 属性的默认值。 这可确保该属性总是返回特定的默认值，未设置，并且它可以不同于属性的类型的默认值。 默认值将是还原何时[ `ClearValue` ](xref:Xamarin.Forms.BindableObject.ClearValue(Xamarin.Forms.BindableProperty))可绑定属性上调用方法。

下面的代码演示具有标识符和四个必需参数的值的可绑定属性的示例：

```csharp
public static readonly BindableProperty EventNameProperty =
  BindableProperty.Create ("EventName", typeof(string), typeof(EventToCommandBehavior), null);
```

这将创建[ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty)实例名为`EventName`，类型的`string`。 该属性归`EventToCommandBehavior`类，并具有默认值为`null`。 可绑定属性的命名约定是可绑定的属性标识符中指定属性名称必须匹配`Create`方法，使用"属性"追加到它。 因此，在上面的示例中，可绑定的属性标识符是`EventNameProperty`。

（可选） 在创建时[ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty)实例，下面可以指定参数：

- 绑定模式。 这用于指定将属性值更改将传播的方向。 在默认绑定模式中，更改将传播从*源*到*目标*。
- 设置属性值时，将调用一个验证委托。 有关详细信息，请参阅[验证回叫](#validation)。
- 属性更改将属性值已更改时调用的委托。 有关详细信息，请参阅[检测属性更改](#propertychanges)。
- 一个属性，更改将属性值将更改时调用的委托。 此委托具有与属性更改委托相同的签名。
- 属性值已更改时，将调用一个强制值委托。 有关详细信息，请参阅[强制值回叫](#coerce)。
- 一个`Func`，用来初始化默认属性值。 有关详细信息，请参阅[使用 Func 创建默认值](#defaultfunc)。

### <a name="creating-accessors"></a>创建访问器

属性访问器需要使用属性语法来访问可绑定的属性。 `Get`访问器应返回相应的可绑定属性中包含的值。 这可以通过调用来实现[ `GetValue` ](xref:Xamarin.Forms.BindableObject.GetValue(Xamarin.Forms.BindableProperty))方法，传入要获取的值的可绑定的属性标识符，然后将结果转换为所需的类型。 `Set`访问器应设置相应的可绑定属性的值。 这可以通过调用来实现[ `SetValue` ](xref:Xamarin.Forms.BindableObject.SetValue(Xamarin.Forms.BindableProperty,System.Object))方法，传入在其上设置值和要设置的值的可绑定的属性标识符。

下面的代码示例演示的访问器`EventName`可绑定的属性：

```csharp
public string EventName {
  get { return (string)GetValue (EventNameProperty); }
  set { SetValue (EventNameProperty, value); }
}
```

### <a name="consuming-a-bindable-property"></a>使用可绑定属性

一旦创建可绑定属性后，可以从 XAML 或代码使用它。 在 XAML，这被通过使用命名空间声明，该值指示 CLR 命名空间名称和 （可选） 程序集名称声明具有前缀的命名空间。 有关详细信息，请参阅[XAML 命名空间](~/xamarin-forms/xaml/namespaces.md)。

下面的代码示例演示了包含所引用的自定义类型的应用程序代码在同一程序集中定义的可绑定属性的自定义类型的 XAML 命名空间：

```xaml
<ContentPage ... xmlns:local="clr-namespace:EventToCommandBehavior" ...>
  ...
</ContentPage>
```

设置时使用命名空间声明`EventName`可绑定属性，如下面的 XAML 代码示例所示：

```xaml
<ListView ...>
  <ListView.Behaviors>
    <local:EventToCommandBehavior EventName="ItemSelected" ... />
  </ListView.Behaviors>
</ListView>
```

以下代码示例显示相应的 C# 代码：

```csharp
var listView = new ListView ();
listView.Behaviors.Add (new EventToCommandBehavior {
  EventName = "ItemSelected",
  ...
});
```

<a name="advanced" />

## <a name="advanced-scenarios"></a>高级的方案

创建时[ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty)实例时，有多种可设为启用高级可绑定属性方案的可选参数。 本部分探讨这些方案。

<a name="propertychanges" />

### <a name="detecting-property-changes"></a>检测属性更改

一个`static`属性更改回调方法可以通过指定注册与可绑定属性`propertyChanged`参数[ `BindableProperty.Create` ](xref:Xamarin.Forms.BindableProperty.Create(System.String,System.Type,System.Type,System.Object,Xamarin.Forms.BindingMode,Xamarin.Forms.BindableProperty.ValidateValueDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangedDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangingDelegate,Xamarin.Forms.BindableProperty.CoerceValueDelegate,Xamarin.Forms.BindableProperty.CreateDefaultValueDelegate))方法。 可绑定属性的值更改时，将调用指定的回调方法。

下面的代码示例演示如何`EventName`可绑定属性寄存器`OnEventNameChanged`作为属性更改回调方法的方法：

```csharp
public static readonly BindableProperty EventNameProperty =
  BindableProperty.Create (
    "EventName", typeof(string), typeof(EventToCommandBehavior), null, propertyChanged: OnEventNameChanged);
...

static void OnEventNameChanged (BindableObject bindable, object oldValue, object newValue)
{
  // Property changed implementation goes here
}
```

在属性更改回调方法中， [ `BindableObject` ](xref:Xamarin.Forms.BindableObject)参数用于表示一项更改和两个值所属的类的实例已报告`object`参数表示的新旧值可绑定属性。

<a name="validation" />

### <a name="validation-callbacks"></a>验证回叫

一个`static`验证回叫方法可通过指定注册到可绑定的属性`validateValue`参数[ `BindableProperty.Create` ](xref:Xamarin.Forms.BindableProperty.Create(System.String,System.Type,System.Type,System.Object,Xamarin.Forms.BindingMode,Xamarin.Forms.BindableProperty.ValidateValueDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangedDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangingDelegate,Xamarin.Forms.BindableProperty.CoerceValueDelegate,Xamarin.Forms.BindableProperty.CreateDefaultValueDelegate))方法。 设置可绑定属性的值时，将调用指定的回调方法。

下面的代码示例演示如何`Angle`可绑定属性寄存器`IsValidValue`作为验证回调方法的方法：

```csharp
public static readonly BindableProperty AngleProperty =
  BindableProperty.Create ("Angle", typeof(double), typeof(HomePage), 0.0, validateValue: IsValidValue);
...

static bool IsValidValue (BindableObject view, object value)
{
  double result;
  bool isDouble = double.TryParse (value.ToString (), out result);
  return (result >= 0 && result <= 360);
}
```

验证回叫提供了一个值，并应返回`true`的值是否有效的属性，否则`false`。 如果验证回调返回，将会引发异常`false`，应由开发人员。 可绑定属性设置时，验证回叫方法的典型用法约束整数或双精度型的值。 例如，`IsValidValue`方法检查属性值是`double`0 到 360 之间的范围内。

<a name="coerce" />

### <a name="coerce-value-callbacks"></a>强制值回叫

一个`static`强制值回叫方法可通过指定注册到可绑定的属性`coerceValue`参数[ `BindableProperty.Create` ](xref:Xamarin.Forms.BindableProperty.Create(System.String,System.Type,System.Type,System.Object,Xamarin.Forms.BindingMode,Xamarin.Forms.BindableProperty.ValidateValueDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangedDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangingDelegate,Xamarin.Forms.BindableProperty.CoerceValueDelegate,Xamarin.Forms.BindableProperty.CreateDefaultValueDelegate))方法。 可绑定属性的值更改时，将调用指定的回调方法。

强制的值回叫用于强制重新计算的可绑定属性的属性的值更改时。 例如，可以使用强制值回叫，以确保一个可绑定属性的值不大于另一个可绑定属性的值。

下面的代码示例演示如何`Angle`可绑定属性寄存器`CoerceAngle`方法作为强制值回叫方法：

```csharp
public static readonly BindableProperty AngleProperty = BindableProperty.Create (
  "Angle", typeof(double), typeof(HomePage), 0.0, coerceValue: CoerceAngle);
public static readonly BindableProperty MaximumAngleProperty = BindableProperty.Create (
  "MaximumAngle", typeof(double), typeof(HomePage), 360.0);
...

static object CoerceAngle (BindableObject bindable, object value)
{
  var homePage = bindable as HomePage;
  double input = (double)value;

  if (input > homePage.MaximumAngle) {
    input = homePage.MaximumAngle;
  }

  return input;
}
```

`CoerceAngle`方法检查的值`MaximumAngle`属性，并且如果`Angle`属性值大于它，它强制转换为值`MaximumAngle`属性值。

<a name="defaultfunc" />

### <a name="creating-a-default-value-with-a-func"></a>使用 Func 创建默认值

一个`Func`可用于初始化的默认值的可绑定的属性，如下面的代码示例中所示：

```csharp
public static readonly BindableProperty SizeProperty =
  BindableProperty.Create ("Size", typeof(double), typeof(HomePage), 0.0,
  defaultValueCreator: bindable => Device.GetNamedSize (NamedSize.Large, (Label)bindable));
```

`defaultValueCreator`参数设置为`Func`，它调用[ `Device.GetNamedSize` ](xref:Xamarin.Forms.Device.GetNamedSize(Xamarin.Forms.NamedSize,System.Type))方法以返回`double`表示使用的字体的命名的大小[ `Label` ](xref:Xamarin.Forms.Label)原生平台上。

## <a name="summary"></a>总结

本文介绍了可绑定属性，并演示了如何创建并使用它们。 可绑定属性是属性的特殊类型，其中 Xamarin.Forms 属性系统跟踪属性的值。

## <a name="related-links"></a>相关链接

- [XAML 命名空间](~/xamarin-forms/xaml/namespaces.md)
- [事件到，命令行为 （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/behaviors-eventtocommandbehavior)
- [验证回叫 （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-validationcallback)
- [强制值回叫 （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-coercevaluecallback)
- [BindableProperty](xref:Xamarin.Forms.BindableProperty)
- [BindableObject](xref:Xamarin.Forms.BindableObject)
