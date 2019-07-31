---
title: 折线和参数等式
description: 本文介绍如何到使用 SkiaSharp 呈现任何行，可以定义与参数方程确定，此示例代码进行了演示。
ms.prod: xamarin
ms.assetid: 85AEBB33-E954-4364-A6E1-808FAB197BEE
ms.technology: xamarin-skiasharp
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2017
ms.openlocfilehash: f326a2ef449b7c807be150a002a4afc600d9908d
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2019
ms.locfileid: "68652707"
---
# <a name="polylines-and-parametric-equations"></a>折线和参数等式

[![下载示例](~/media/shared/download.png)下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_使用 SkiaSharp 呈现可以定义与参数化的等式的任何行_

在中[ **SkiaSharp 曲线和路径**](../curves/index.md)部分的本指南中，您将看到的各种方法的[ `SKPath` ](xref:SkiaSharp.SKPath)定义来呈现某些类型的曲线。 但是，它，有时需要绘制曲线并不直接支持的一种`SKPath`。 在这种情况下，可以使用 polyline （一系列相互连接的直线） 绘制数学上可以定义任何曲线。 如果使各条线足够小，并且许多足够结果看起来类似于一条曲线。 此循环是实际 3,600 较短的行：

![](polylines-images/spiralexample.png "螺旋")

通常最好是定义根据参数方程确定一对曲线。 它们是 X 和 Y 坐标表示的公式依赖于第三个变量，有时称为`t`的时间。 例如，以下参数方程确定为定义的中心点 （0，0） 的 1 半径的圆形*t*从 0 到 1:

`x = cos(2πt)`

`y = sin(2πt)`

 如果你想 radius 大于 1，只需正弦和余弦值乘以该 radius 并如果你需要将中心移动到另一个位置添加这些值：

`x = xCenter + radius·cos(2πt)`

`y = yCenter + radius·sin(2πt)`

有关使用到的水平和垂直轴并行一个椭圆，涉及两个半径：

`x = xCenter + xRadius·cos(2πt)`

`y = yCenter + yRadius·sin(2πt)`

然后可以将等效 SkiaSharp 代码放在一个循环，用于计算各个点并将添加到路径中。 下面的 SkiaSharp 代码创建`SKPath`填充显示器表面的椭圆的对象。 循环直接周期通过 360 度。 中心是一半的宽度和高度显示图面，并因此是两个半径：

```csharp
SKPath path = new SKPath();

for (float angle = 0; angle < 360; angle += 1)
{
    double radians = Math.PI * angle / 180;
    float x = info.Width / 2 + (info.Width / 2) * (float)Math.Cos(radians);
    float y = info.Height / 2 + (info.Height / 2) * (float)Math.Sin(radians);

    if (angle == 0)
    {
        path.MoveTo(x, y);
    }
    else
    {
        path.LineTo(x, y);
    }
}
path.Close();
```

这会导致 360 较短的行定义的椭圆。 当呈现时，它将显示平滑。

当然，您无需创建使用一条折线，因为一个椭圆`SKPath`包括`AddOval`会为您的方法。 但你可能想要绘制视觉对象不由提供的`SKPath`。

**Archimedean 螺旋**页有代码，类似于椭圆代码，但有重要差异。 它循环继续 360 度圆的 10 次，持续调整 radius:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    SKPoint center = new SKPoint(info.Width / 2, info.Height / 2);
    float radius = Math.Min(center.X, center.Y);

    using (SKPath path = new SKPath())
    {
        for (float angle = 0; angle < 3600; angle += 1)
        {
            float scaledRadius = radius * angle / 3600;
            double radians = Math.PI * angle / 180;
            float x = center.X + scaledRadius * (float)Math.Cos(radians);
            float y = center.Y + scaledRadius * (float)Math.Sin(radians);
            SKPoint point = new SKPoint(x, y);

            if (angle == 0)
            {
                path.MoveTo(point);
            }
            else
            {
                path.LineTo(point);
            }
        }

        SKPaint paint = new SKPaint
        {
            Style = SKPaintStyle.Stroke,
            Color = SKColors.Red,
            StrokeWidth = 5
        };

        canvas.DrawPath(path, paint);
    }
}
```

也称为结果*算术螺旋*因为每个循环之间的偏移量是常量：

[![](polylines-images/archimedeanspiral-small.png "三重 Archimedean 螺旋页屏幕截图")](polylines-images/archimedeanspiral-large.png#lightbox "Archimedean 螺旋页面的三个屏幕截图")

请注意，`SKPath`中创建`using`块。 这`SKPath`消耗的内存比`SKPath`对象中的上一个程序，建议的`using`块是更适合释放任何非托管的资源。


## <a name="related-links"></a>相关链接

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
