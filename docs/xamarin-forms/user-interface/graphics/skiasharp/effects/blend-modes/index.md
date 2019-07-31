---
title: SkiaSharp 的混合模式
description: 使用混合模式，可定义图形对象堆叠在另一个时，会发生什么情况。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: CE1B222E-A2D0-4016-A532-EC1E59EE3D6B
author: davidbritch
ms.author: dabritch
ms.date: 08/23/2018
ms.openlocfilehash: 8071f310e899575699e1d0b925541f2863b00676
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2019
ms.locfileid: "68645192"
---
# <a name="skiasharp-blend-modes"></a>SkiaSharp 的混合模式

[![下载示例](~/media/shared/download.png)下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

这些文章开始关注[ `BlendMode` ](xref:SkiaSharp.SKPaint.BlendMode)的属性[ `SKPaint` ](xref:SkiaSharp.SKPaint)。 `BlendMode`属性属于类型[ `SKBlendMode` ](xref:SkiaSharp.SKBlendMode)，具有 29 成员的枚举。

`BlendMode`属性确定图形对象时，会发生什么情况 (通常称为_源_) 呈现在现有的图形对象 (称为_目标_)。 通常情况下，我们预计会掩盖其下的对象的新图形对象。 只是因为默认 blend 模式是将发生这种情况，但是`SKBlendMode.SrcOver`，这意味着源绘制_转移_目标。 其他 28 成员`SKBlendMode`产生其他副作用。 在图形编程中，技术的组合以各种方式的图形对象称为_组合的情况下_。

## <a name="the-skblendmodes-enumeration"></a>SKBlendModes 枚举

SkiaSharp 的混合模式密切对应于所述 W3C [**合成和混合级别 1** ](https://www.w3.org/TR/compositing-1/)规范。 Skia [ **SkBlendMode 引用**](https://skia.org/user/api/SkBlendMode_Reference)还提供了有用的背景信息。 有关混合模式下的常规介绍[**混合模式**](https://en.wikipedia.org/wiki/Blend_modes)维基百科文章是一个不错的起点。 因此，在该上下文中的混合模式有关的附加联机信息中 Adobe Photoshop 支持混合模式。

29 成员`SKBlendMode`枚举可以分为三个类别：

| Porter Duff | 可分离    | 非可分离 |
| ----------- | ------------ | ------------- |
| `Clear`     | `Modulate`   | `Hue`         |
| `Src`       | `Screen`     | `Saturation`  |
| `Dst`       | `Overlay`    | `Color`       |
| `SrcOver`   | `Darken`     | `Luminosity`  |
| `DstOver`   | `Lighten`    |               |
| `SrcIn`     | `ColorDodge` |               |
| `DstIn`     | `ColorBurn`  |               |
| `SrcOut`    | `HardLight`  |               |
| `DstOut`    | `SoftLight`  |               |
| `SrcATop`   | `Difference` |               |
| `DstATop`   | `Exclusion`  |               |
| `Xor`       | `Multiply`   |               |
| `Plus`      |              |               |

这三个类别的名称将采用在下文中的多个含义。 此处列出的成员的顺序是相同的定义`SKBlendMode`枚举。 第一列中的 13 枚举成员具有整数值 0 到 12。 第二列是对应于整数 13 到 24 的枚举成员和第三列中的成员具有 25 到 28 的值。

这些 blend 模式在 W3C**合成和 Blend Level 1**文档中的顺序_大致_相同, 但有一些区别:该`Src`模式在`Plus` W3C 文档中称为_Copy_ , 被称为_较浅_。 W3C 文档定义了_正常_不包括在混合模式`SKBlendModes`因为它是与相同`SrcOver`。 `Modulate`混合模式 （在第一列顶部） 不包括在 W3C 文档和讨论`Multiply`模式之前`Screen`。

因为`Modulate`混合模式是唯一的 Skia，作为额外的 Porter Duff 模式以及可分离模式下将进行讨论。

## <a name="the-importance-of-transparency"></a>透明度的重要性

从历史上看，组合的情况下开发的概念与结合_alpha 通道_。 在显示图面 ( `SKCanvas`如对象和全色位图) 中, 每个像素都包含4个字节:为红色、绿色和蓝色分量使用1个字节, 为透明度添加一个额外的字节。 此 alpha 组件是透明度的完全透明度为 0 和 0xFF 完全不透明，具有不同级别的那些值之间。

许多的混合模式依赖于透明度。 通常，当`SKCanvas`中第一次获得`PaintSurface`处理程序，或当`SKCanvas`创建位图上进行绘制，第一步是此调用：

```csharp
canvas.Clear();
```

此方法会替换为透明黑色像素，等效于画布的所有像素`new SKColor(0, 0, 0, 0)`或整数 0x00000000。 所有字节的所有像素被都初始化为零。

绘图图面的`SKCanvas`中获取`PaintSurface`处理程序可能显示为具有白色背景，但这只是因为`SKCanvasView`本身具有透明背景，和的页具有白色背景。 可以向自己演示这一事实，通过设置 Xamarin.Forms`BackgroundColor`属性的`SKCanvasView`为 Xamarin.Forms 颜色：

```csharp
canvasView.BackgroundColor = Color.Red;
```

或者，在派生类中`ContentPage`，可以设置页背景色：

```csharp
BackgroundColor = Color.Red;
```

你将看到此红色背景 SkiaSharp 图形因为 SkiaSharp 画布本身是透明的。

文章[ **SkiaSharp 透明度**](../../basics/transparency.md)使用透明度来排列复合图像中的多个图形中显示一些基本技巧。 混合模式谈论其他内容，但透明度仍然至关重要的混合模式。 

## <a name="skiasharp-porter-duff-blend-modesporter-duffmd"></a>[SkiaSharp Porter Duff 的混合模式](porter-duff.md)

使用 Porter Duff 的混合模式来编写基于源和目标图像的场景。

## <a name="skiasharp-separable-blend-modesseparablemd"></a>[SkiaSharp 可分离的混合模式](separable.md)

使用可分离的混合模式更改红色、 绿色和蓝色颜色。

## <a name="skiasharp-non-separable-blend-modesnon-separablemd"></a>[SkiaSharp 非可分离的混合模式](non-separable.md)

使用非可分离的混合模式更改色调、 饱和度和亮度。

## <a name="related-links"></a>相关链接

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
