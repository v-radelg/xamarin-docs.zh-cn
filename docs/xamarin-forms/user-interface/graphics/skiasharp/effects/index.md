---
title: SkiaSharp 效果
description: 了解如何更改正常显示的图形使用渐变、 位图平铺、 混合模式、 模糊和其他效果。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: B3E06572-8E2A-49FA-90D1-444C394CD516
author: davidbritch
ms.author: dabritch
ms.date: 08/22/2018
ms.openlocfilehash: da923a3542a57b6150e536ecb6649140e57c81e1
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2019
ms.locfileid: "68655358"
---
# <a name="skiasharp-effects"></a>SkiaSharp 效果

[![下载示例](~/media/shared/download.png)下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

SkiaSharp [ `SKPaint` ](xref:SkiaSharp.SKPaint)类定义了六个可以下的常规术语分类的属性_效果_。 这些是更改的图形以某种方式正常显示的属性。 SkiaSharp 效果划分为六个类别：

## <a name="path-effectscurveseffectsmd"></a>[路径效果](../curves/effects.md)

设置[ `PathEffect` ](xref:SkiaSharp.SKPaint.PathEffect)的属性`SKPaint`对象的类型[ `SKPathEffect` ](xref:SkiaSharp.SKPathEffect)显示虚线，或者要绘制笔画，或从路径中创建的图案填充的区域。 路径效果前面中本系列文章中所涉及[ **SkiaSharp 中的路径效果**](../curves/effects.md)。

## <a name="shadersshadersindexmd"></a>[着色器](shaders/index.md)

设置[ `Shader` ](xref:SkiaSharp.SKPaint.Shader)的属性`SKPaint`对象的类型[ `SKShader` ](xref:SkiaSharp.SKShader)显示线性或循环渐变、 平铺的位图和 Perlin 噪音模式。

## <a name="blend-modesblend-modesindexmd"></a>[混合模式](blend-modes/index.md)

设置[ `BlendMode` ](xref:SkiaSharp.SKPaint.BlendMode)的属性`SKPaint`成员[ `SKBlendMode` ](xref:SkiaSharp.SKBlendMode)枚举来控制源图形显示在目标上时，会发生什么情况。 SkiaSharp 支持所有 CSS 组合的情况下，混合模式，包括 Porter Duff 模式、 可分离的混合模式和非可分离的混合模式。

## <a name="mask-filtersmask-filtersmd"></a>[掩码筛选器](mask-filters.md)

设置[ `MaskFilter` ](xref:SkiaSharp.SKPaint.MaskFilter)的属性`SKPaint`对象的类型[ `SKMaskFilter` ](xref:SkiaSharp.SKMaskFilter)模糊和其他 alpha 的效果。

## <a name="image-filtersimage-filtersmd"></a>[映像筛选器](image-filters.md)

设置[ `ImageFilter` ](xref:SkiaSharp.SKPaint.ImageFilter)的属性`SKPaint`对象的类型[ `SKImageFilter` ](xref:SkiaSharp.SKImageFilter)模糊的位图和创建投影、 浮雕或雕刻效果。

## <a name="color-filterscolor-filtersmd"></a>[颜色筛选器](color-filters.md)

设置[ `ColorFilter` ](xref:SkiaSharp.SKPaint.ColorFilter)的属性`SKPaint`对象的类型[ `SKColorFilter` ](xref:SkiaSharp.SKColorFilter)更改颜色使用表或矩阵转换。

这些文章是中的所有示例都代码[ **SkiaSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)。 在主页上，选择**SkiaSharp 效果**。

## <a name="related-links"></a>相关链接

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
