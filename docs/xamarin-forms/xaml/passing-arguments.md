---
title: 在 XAML 中传递自变量
description: 本文演示如何使用可用于将参数传递到非默认构造函数，以调用工厂方法，并指定泛型参数的类型的 XAML 属性。
ms.prod: xamarin
ms.assetid: 8F3B267F-499E-4D79-9193-FCA99F199519
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/25/2016
ms.openlocfilehash: 80f332e45d6c46ad49543923e85cbb2eceadb378
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70767600"
---
# <a name="passing-arguments-in-xaml"></a>在 XAML 中传递自变量

[![下载示例](~/media/shared/download.png)下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-passingconstructorarguments)

_本文演示如何使用可用于将参数传递到非默认构造函数，以调用工厂方法，并指定泛型参数的类型的 XAML 属性。_

## <a name="overview"></a>概述

通常是使用构造函数需要参数，或通过调用静态创建方法的对象的实例化所必需的。 这可以通过在 XAML 中使用`x:Arguments`和`x:FactoryMethod`属性：

- `x:Arguments`特性用于指定非默认构造函数或工厂方法对象声明的构造函数自变量。 有关详细信息，请参阅[传递构造函数参数](#constructor_arguments)。
- `x:FactoryMethod`特性用于指定可用于初始化对象的工厂方法。 有关详细信息，请参阅[调用工厂方法](#factory_methods)。

此外，`x:TypeArguments`属性可用于指定泛型类型的构造函数的泛型类型参数。 有关详细信息，请参阅[指定泛型类型参数](#generic_type_arguments)。

<a name="constructor_arguments" />

## <a name="passing-constructor-arguments"></a>传递构造函数自变量

参数可以传递到非默认构造函数使用`x:Arguments`属性。 表示自变量的类型的 XML 元素中，每个构造函数自变量必须进行分隔。 Xamarin.Forms 的基本类型支持以下元素：

- `x:Object`
- `x:Boolean`
- `x:Byte`
- `x:Int16`
- `x:Int32`
- `x:Int64`
- `x:Single`
- `x:Double`
- `x:Decimal`
- `x:Char`
- `x:String`
- `x:TimeSpan`
- `x:Array`
- `x:DateTime`

下面的代码示例演示了如何使用`x:Arguments`有三个特性[ `Color` ](xref:Xamarin.Forms.Color)构造函数：

```xaml
<BoxView HeightRequest="150" WidthRequest="150" HorizontalOptions="Center">
  <BoxView.Color>
    <Color>
      <x:Arguments>
        <x:Double>0.9</x:Double>
      </x:Arguments>
    </Color>
  </BoxView.Color>
</BoxView>
<BoxView HeightRequest="150" WidthRequest="150" HorizontalOptions="Center">
  <BoxView.Color>
    <Color>
      <x:Arguments>
        <x:Double>0.25</x:Double>
        <x:Double>0.5</x:Double>
        <x:Double>0.75</x:Double>
      </x:Arguments>
    </Color>
  </BoxView.Color>
</BoxView>
<BoxView HeightRequest="150" WidthRequest="150" HorizontalOptions="Center">
  <BoxView.Color>
    <Color>
      <x:Arguments>
        <x:Double>0.8</x:Double>
        <x:Double>0.5</x:Double>
        <x:Double>0.2</x:Double>
        <x:Double>0.5</x:Double>
      </x:Arguments>
    </Color>
  </BoxView.Color>
</BoxView>
```

内的元素数`x:Arguments`标记，以及这些元素的类型必须与之一匹配[ `Color` ](xref:Xamarin.Forms.Color)构造函数。 `Color` [构造函数](xref:Xamarin.Forms.Color.%23ctor(System.Double))使用单个参数需要灰度值从 0 （黑色） 设置为 1 （白色）。 `Color` [构造函数](xref:Xamarin.Forms.Color.%23ctor(System.Double,System.Double,System.Double))带有三个参数需要红色、 绿色和蓝色值范围从 0 到 1。 `Color` [构造函数](xref:Xamarin.Forms.Color.%23ctor(System.Double,System.Double,System.Double,System.Double))带四个参数将作为第四个参数添加 alpha 通道。

以下屏幕截图显示了每个调用的结果[ `Color` ](xref:Xamarin.Forms.Color)构造函数使用指定的参数值：

![BoxView 指定的 x:Arguments](passing-arguments-images/passing-arguments.png)

<a name="factory_methods" />

## <a name="calling-factory-methods"></a>调用工厂方法

可以在 XAML 中通过指定方法的调用工厂方法采用`x:FactoryMethod`属性，并使用其自变量`x:Arguments`属性。 工厂方法是`public static`返回对象或值类或结构，它定义的方法的类型相同的方法。

[ `Color` ](xref:Xamarin.Forms.Color)结构定义的工厂方法数和下面的代码示例演示如何调用这三种页脚：

```xaml
<BoxView HeightRequest="150" WidthRequest="150" HorizontalOptions="Center">
  <BoxView.Color>
    <Color x:FactoryMethod="FromRgba">
      <x:Arguments>
        <x:Int32>192</x:Int32>
        <x:Int32>75</x:Int32>
        <x:Int32>150</x:Int32>                        
        <x:Int32>128</x:Int32>
      </x:Arguments>
    </Color>
  </BoxView.Color>
</BoxView>
<BoxView HeightRequest="150" WidthRequest="150" HorizontalOptions="Center">
  <BoxView.Color>
    <Color x:FactoryMethod="FromHsla">
      <x:Arguments>
        <x:Double>0.23</x:Double>
        <x:Double>0.42</x:Double>
        <x:Double>0.69</x:Double>
        <x:Double>0.7</x:Double>
      </x:Arguments>
    </Color>
  </BoxView.Color>
</BoxView>
<BoxView HeightRequest="150" WidthRequest="150" HorizontalOptions="Center">
  <BoxView.Color>
    <Color x:FactoryMethod="FromHex">
      <x:Arguments>
        <x:String>#FF048B9A</x:String>
      </x:Arguments>
    </Color>
  </BoxView.Color>
</BoxView>
```

内的元素数`x:Arguments`标记，以及这些元素的类型必须匹配要调用的工厂方法的参数。 [ `FromRgba` ](xref:Xamarin.Forms.Color.FromRgba(System.Int32,System.Int32,System.Int32,System.Int32))工厂方法需要四个[ `Int32` ](https://docs.microsoft.com/dotnet/api/system.int32)表示红色、 绿色、 蓝色和 alpha 值分别范围从 0 到 255 之间的参数。 [ `FromHsla` ](xref:Xamarin.Forms.Color.FromHsla(System.Double,System.Double,System.Double,System.Double))工厂方法需要四个[ `Double` ](https://docs.microsoft.com/dotnet/api/system.double)表示色调、 饱和度、 亮度和 alpha 值，分别范围从 0 到 1 的参数。 [ `FromHex` ](xref:Xamarin.Forms.Color.FromHex(System.String))工厂方法需要[ `String` ](https://docs.microsoft.com/dotnet/api/system.string)表示十六进制 (A) 代表 RGB 颜色。

以下屏幕截图显示了每个调用的结果[ `Color` ](xref:Xamarin.Forms.Color)工厂方法与指定的参数值：

![BoxView 通过 x:FactoryMethod 和 x:Arguments 指定的颜色](passing-arguments-images/factory-methods.png)

<a name="generic_type_arguments" />

## <a name="specifying-a-generic-type-argument"></a>指定泛型类型参数

可以使用指定泛型类型参数的泛型类型的构造函数`x:TypeArguments`属性，如下面的代码示例中所示：

```xaml
<ContentPage ...>
  <StackLayout>
    <StackLayout.Margin>
      <OnPlatform x:TypeArguments="Thickness">
        <On Platform="iOS" Value="0,20,0,0" />
        <On Platform="Android" Value="5, 10" />
        <On Platform="UWP" Value="10" />
      </OnPlatform>
    </StackLayout.Margin>
  </StackLayout>
</ContentPage>
```

[ `OnPlatform` ](xref:Xamarin.Forms.OnPlatform`1)类是一个泛型类，必须使用实例化`x:TypeArguments`匹配目标类型的属性。 在中[ `On` ](xref:Xamarin.Forms.On)类， [ `Platform` ](xref:Xamarin.Forms.On.Platform)属性可以接受单个`string`值或以逗号分隔的多个`string`值。 在此示例中， [ `StackLayout.Margin` ](xref:Xamarin.Forms.View.Margin)属性设置为特定于平台的[ `Thickness` ](xref:Xamarin.Forms.Thickness)。

## <a name="summary"></a>总结

本文演示了使用的 XAML 特性，可用于将参数传递到非默认构造函数，以调用工厂方法，并指定泛型参数的类型。

## <a name="related-links"></a>相关链接

- [XAML 命名空间](~/xamarin-forms/xaml/namespaces.md)
- [传递构造函数自变量 （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-passingconstructorarguments)
- [调用工厂方法 （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-callingfactorymethods)
