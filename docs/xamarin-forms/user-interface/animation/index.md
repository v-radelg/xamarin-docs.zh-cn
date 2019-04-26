---
title: 在 Xamarin.Forms 中的动画
description: Xamarin.Forms 具有自己的动画基础结构直接用于创建简单动画，而且还多样化足以创建复杂动画。
ms.prod: xamarin
ms.assetid: AC0B4127-ECA3-44DA-8A24-A2B10A275083
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/14/2016
ms.openlocfilehash: bebb3e32f298a2079069787dba3453e1817cf64f
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61158584"
---
# <a name="animation-in-xamarinforms"></a>在 Xamarin.Forms 中的动画

_Xamarin.Forms 具有自己的动画基础结构直接用于创建简单动画，而且还多样化足以创建复杂动画。_

Xamarin.Forms 动画类针对一个典型的动画，渐进式属性从一个值更改到另一段时间内的可视元素的不同属性。 请注意，Xamarin.Forms 动画类没有 XAML 界面。 但是，在中封装动画[行为](~/xamarin-forms/app-fundamentals/behaviors/index.md)，然后从 XAML 引用。

## <a name="simple-animationssimplemd"></a>[简单动画](simple.md)

[ `ViewExtensions` ](xref:Xamarin.Forms.ViewExtensions)类提供了可用于构造简单动画的旋转、 缩放、 转换，和淡出的扩展方法[ `VisualElement` ](xref:Xamarin.Forms.VisualElement)实例。 本文演示如何创建和取消使用动画`ViewExtensions`类。

## <a name="easing-functionseasingmd"></a>[缓动函数](easing.md)

包括 Xamarin.Forms [ `Easing` ](xref:Xamarin.Forms.Easing)类，可用于指定在传输函数，用于控制动画如何加快或减慢，因为它们正在运行。 本文演示如何使用预定义的缓动函数，以及如何创建自定义缓动函数。

## <a name="custom-animationscustommd"></a>[自定义动画](custom.md)

[ `Animation` ](xref:Xamarin.Forms.Animation)类是所有 Xamarin.Forms 动画中的扩展方法构建基块[ `ViewExtensions` ](xref:Xamarin.Forms.ViewExtensions)类创建一个或多个`Animation`对象。 本文演示如何使用`Animation`类来创建和取消动画、 同步多个动画，并创建不通过现有的动画方法进行动画处理的属性进行动画处理的自定义动画。
