---
title: 在 Xamarin.Forms 中的缓动函数
description: Xamarin.Forms 包含允许您指定的传输函数，用于控制动画如何加快或减慢，因为它们正在运行的缓类。 本文演示如何使用预定义的缓动函数，以及如何创建自定义缓动函数。
ms.prod: xamarin
ms.assetid: E6F124C7-A161-4C1F-AF40-52F0935E54DE
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/14/2016
ms.openlocfilehash: 3532a557c6ed958974326ca79ad160b01e9b5289
ms.sourcegitcommit: b23a107b0fe3d2f814ae35b52a5855b6ce2a3513
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2019
ms.locfileid: "65926688"
---
# <a name="easing-functions-in-xamarinforms"></a>在 Xamarin.Forms 中的缓动函数

[![下载示例](~/media/shared/download.png)下载示例](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Animation/Easing/)

_Xamarin.Forms 包含允许您指定的传输函数，用于控制动画如何加快或减慢，因为它们正在运行的缓类。本文演示如何使用预定义的缓动函数，以及如何创建自定义缓动函数。_


[ `Easing` ](xref:Xamarin.Forms.Easing)类定义了大量可供动画的缓动函数：

- [ `BounceIn` ](xref:Xamarin.Forms.Easing.BounceIn)缓动函数弹跳动画的开头。
- [ `BounceOut` ](xref:Xamarin.Forms.Easing.BounceOut)缓动函数弹跳动画结束时。
- [ `CubicIn` ](xref:Xamarin.Forms.Easing.CubicIn)缓动函数缓慢加速动画。
- [ `CubicInOut` ](xref:Xamarin.Forms.Easing.CubicInOut)缓动函数在开始时，动画加速和减速的动画结束时。
- [ `CubicOut` ](xref:Xamarin.Forms.Easing.CubicOut)快速缓动函数减速的动画。
- [ `Linear` ](xref:Xamarin.Forms.Easing.Linear)缓动函数使用常量的速度，并且默认的缓动函数。
- [ `SinIn` ](xref:Xamarin.Forms.Easing.SinIn)顺利缓动函数加速动画。
- [ `SinInOut` ](xref:Xamarin.Forms.Easing.SinInOut)顺利缓动函数加快在开始时，动画并平稳减速的动画结束时。
- [ `SinOut` ](xref:Xamarin.Forms.Easing.SinOut)顺利缓动函数减速的动画。
- [ `SpringIn` ](xref:Xamarin.Forms.Easing.SpringIn)缓动函数使动画结束时非常快速地加快速度。
- [ `SpringOut` ](xref:Xamarin.Forms.Easing.SpringOut)缓动函数使动画结束时快速减速。

`In`和`Out`后缀指示缓动函数提供的效果是明显的动画，和 / 或结束时，在开头。

此外，可以创建自定义缓动函数。 有关详细信息，请参阅[自定义缓动函数](#customeasing)。

## <a name="consuming-an-easing-function"></a>使用缓动函数

中的动画扩展方法[ `ViewExtensions` ](xref:Xamarin.Forms.ViewExtensions)类允许指定为最后一种方法参数中，缓动函数，如下面的代码示例中所示：

```csharp
await image.TranslateTo(0, 200, 2000, Easing.BounceIn);
await image.ScaleTo(2, 2000, Easing.CubicIn);
await image.RotateTo(360, 2000, Easing.SinInOut);
await image.ScaleTo(1, 2000, Easing.CubicOut);
await image.TranslateTo(0, -200, 2000, Easing.BounceOut);
```

通过指定动画的缓动函数，动画速度变得的非线性，并生成缓动函数提供的效果。 创建动画时省略缓动函数会导致使用默认值的动画[ `Linear` ](xref:Xamarin.Forms.Easing.Linear)缓动函数，它将生成线性速度。

有关使用中的动画扩展方法的详细信息[ `ViewExtensions` ](xref:Xamarin.Forms.ViewExtensions)类，请参阅[简单动画](~/xamarin-forms/user-interface/animation/simple.md)。 缓动函数也可以使用通过[ `Animation` ](xref:Xamarin.Forms.Animation)类。 有关详细信息，请参阅[自定义动画](~/xamarin-forms/user-interface/animation/custom.md)。

<a name="customeasing" />

## <a name="custom-easing-functions"></a>自定义缓动函数

有三种主要方法与创建自定义缓动函数：

1. 创建一个方法采用`double`自变量，并返回`double`结果。
1. 创建 `Func<double, double>`。
1. 指定为参数的缓动函数[ `Easing` ](xref:Xamarin.Forms.Easing)构造函数。

在所有三种情况下，自定义缓动函数应返回 0 和 1 的 1 的自变量作为参数为 0。 但是，任何值可以返回的参数值为 0 和 1 之间。 现在将依次介绍每种方法。

### <a name="custom-easing-method"></a>自定义缓动方法

可以将自定义缓动函数定义为采用的方法`double`自变量，并返回`double`导致，如下面的代码示例中所示：

```csharp
await image.TranslateTo(0, 200, 2000, CustomEase);

double CustomEase (double t)
{
  return t == 0 || t == 1 ? t : (int)(5 * t) / 5.0;
}
```

`CustomEase`方法将传入值为 0、 0.2、 0.4，0.6，0.8 和 1 的值截断。 因此， [ `Image` ](xref:Xamarin.Forms.Image)实例在离散的跳转，而不是平稳转换。

### <a name="custom-easing-func"></a>自定义缓动函数

自定义缓动函数也可以定义为`Func<double, double>`，如以下代码示例所示：

```csharp
Func<double, double> CustomEase = t => 9 * t * t * t - 13.5 * t * t + 5.5 * t;
await image.TranslateTo(0, 200, 2000, CustomEase));
```

`CustomEase` `Func`表示缓动函数，以快速、 速度变慢和反转课程，，然后反转再次课程结束时快速加速。 因此，虽然的总体移动[ `Image` ](xref:Xamarin.Forms.Image)实例向下，它也会暂时反转课程中途动画。

### <a name="custom-easing-constructor"></a>自定义缓动构造函数

此外可以为的参数定义的自定义缓动函数[ `Easing` ](xref:Xamarin.Forms.Easing)构造函数，如下面的代码示例中所示：

```csharp
await image.TranslateTo (0, 200, 2000, new Easing (t => 1 - Math.Cos (10 * Math.PI * t) * Math.Exp (-5 * t)));
```

自定义缓动函数指定为 lambda 函数参数[ `Easing` ](xref:Xamarin.Forms.Easing)构造函数，并使用`Math.Cos`方法创建的阻碍缓慢放置效果`Math.Exp`方法。 因此， [ `Image` ](xref:Xamarin.Forms.Image)实例平移，以便它看起来放到其最终静止原位置。

## <a name="summary"></a>总结

本文演示了如何使用预定义的缓动函数，以及如何创建自定义缓动函数。 包括 Xamarin.Forms [ `Easing` ](xref:Xamarin.Forms.Easing)类，可用于指定在传输函数，用于控制动画如何加快或减慢，因为它们正在运行。



## <a name="related-links"></a>相关链接

- [异步支持概述](~/cross-platform/platform/async.md)
- [缓动函数 （示例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Animation/Easing/)
- [缓动](xref:Xamarin.Forms.Easing)
- [ViewExtensions](xref:Xamarin.Forms.ViewExtensions)
