---
title: SkiaSharp 转换
description: 本文探讨了应用于在 Xamarin.Forms 应用程序中显示 SkiaSharp 图形转换，并演示此示例代码。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: E9BE322E-ECB3-4395-AFE4-4474A0F25551
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2017
ms.openlocfilehash: a57e50f098c92dbfcdcaa3139565d2ba0e291e3d
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61347750"
---
# <a name="skiasharp-transforms"></a>SkiaSharp 转换

[![下载示例](~/media/shared/download.png)下载示例](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)

_了解如何应用于显示 SkiaSharp 图形转换_

SkiaSharp 支持作为方法的实现的传统图形转换[ `SKCanvas` ](xref:SkiaSharp.SKCanvas)对象。 坐标和大小中指定的变换的改变，`SKCanvas`呈现图形对象的绘图功能。 转换通常是方便绘制重复图形或动画。 一些技术&mdash;如旋转位图或文本&mdash;不可能无需使用的转换。

SkiaSharp 转换支持以下操作：

- *转换*为 shift 坐标从一个位置到另一个
- *缩放*来增加或减少坐标和大小
- *旋转*旋转围绕点坐标
- *倾斜*移动协调水平或垂直，以便一个矩形成为一个平行四边形

这些参数称为*仿射*转换。 仿射转换始终保持平行直线和永远不会导致要成为无限的坐标或大小。 正方形永远不会转换为一个平行四边形之外的任何内容和圆形永远不会转换为一个椭圆之外的任何内容。

SkiaSharp 还支持非仿射转换 (也称为*投影*或*角度来看*转换) 基于标准的 3-3 转换矩阵。 非仿射转换可将一个正方形转换为任何凸四边形，是四个边与所有内角小于 180 度。 非仿射转换可能会导致坐标或大小变得无限的但它们是必需的三维效果。

## <a name="differences-between-skiasharp-and-xamarinforms-transforms"></a>SkiaSharp 和 Xamarin.Forms 转换之间的差异

Xamarin.Forms 还支持 SkiaSharp 相似的转换。 Xamarin.Forms [ `VisualElement` ](xref:Xamarin.Forms.VisualElement)类定义以下转换属性：

- [`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX) 和 [`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY)
- [`Scale`](xref:Xamarin.Forms.VisualElement.Scale)
- [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation)[ `RotationX` ](xref:Xamarin.Forms.VisualElement.RotationX)，和 [`RotationY`](xref:Xamarin.Forms.VisualElement.RotationY)

`RotationX`和`RotationY`属性是创建类似 3D 效果的角度来看转换。

有几个 SkiaSharp 转换和 Xamarin.Forms 转换之间的重要区别：

第一个区别就是 SkiaSharp 转换将应用于整个`SKCanvas`对象时的 Xamarin.Forms 转换应用于单个`VisualElement`派生类。 (可以应用到 Xamarin.Forms 转换`SKCanvasView`对象本身，因为`SKCanvasView`派生自`VisualElement`，但在其中`SKCanvasView`，SkiaSkarp 转换应用。)

SkiaSharp 转换是相对于左上角`SKCanvas`相对于左上角的 Xamarin.Forms 转换时`VisualElement`对其应用。 应用缩放时，这种差异很重要，旋转时会转换，因为这些转换始终是相对于某个特定点。

非常大的区别是 SKiaSharp 转换*方法*Xamarin.Forms 转换时*属性*。 这是超出的语法差异的语义差异：SkiaSharp 转换操作时执行一组 Xamarin.Forms 转换状态。 SkiaSharp 转换应用于随后绘制的图形对象，但不适用于应用转换之前，先绘制的图形对象。 与此相反，Xamarin.Forms 转换适用于以前呈现的元素的属性设置时，就立即。 SkiaSharp 转换是累积的因为会调用这些方法;当此属性设置与另一个值，会替换 Xamarin.Forms 转换。

在本部分中的所有示例程序都显示在**SkiaSharp 转换**一部分[ **SkiaSharpFormsDemos** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)程序。 可以在中找到源代码[**转换**](https://github.com/xamarin/xamarin-forms-samples/tree/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms)解决方案的文件夹。

## <a name="the-translate-transformtranslatemd"></a>[翻译转换](translate.md)

了解如何使用转换变换移动 SkiaSharp 图形。

## <a name="the-scale-transformscalemd"></a>[缩放转换](scale.md)

了解 SkiaSharp 缩放转换为缩放到不同大小的对象。

## <a name="the-rotate-transformrotatemd"></a>[旋转转换](rotate.md)

浏览的效果和动画可能具有 SkiaSharp 旋转转换。

## <a name="the-skew-transformskewmd"></a>[倾斜转换](skew.md)

请参阅如何倾斜转换可以创建倾斜的图形对象。

## <a name="matrix-transformsmatrixmd"></a>[矩阵转换](matrix.md)

深入了解 SkiaSharp 转换具有通用的变换矩阵。

## <a name="touch-manipulationstouchmd"></a>[触摸操作](touch.md)

使用矩阵转换来实现拖动、 缩放和旋转触摸操作。

## <a name="non-affine-transformsnon-affinemd"></a>[非仿射转换](non-affine.md)

超越 oridinary 与非仿射转换效果。

## <a name="3d-rotation3d-rotationmd"></a>[3D 旋转](3d-rotation.md)

非仿射转换用于旋转 3D 空间中的 2D 对象。


## <a name="related-links"></a>相关链接

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （示例）](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
