---
title: 缩放转换
description: Thhis 文章探讨了 SkiaSharp 缩放转换为缩放到不同大小的对象，此示例代码进行了演示。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 54A43F3D-9DA8-44A7-9AE4-7E3025129A0B
author: davidbritch
ms.author: dabritch
ms.date: 03/23/2017
ms.openlocfilehash: 7dc7a2faabef86aa63b52739edda696efcb54aba
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76724239"
---
# <a name="the-scale-transform"></a>缩放转换

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_发现用于将对象缩放到各种大小的 SkiaSharp 缩放变换_

正如你在[**翻译转换**](translate.md)一文中看到的那样，转换转换可以将图形对象从一个位置移到另一个位置。 与此相反，缩放转换将更改图形的对象的大小：

![](scale-images/scaleexample.png "A tall word scaled in size")

缩放转换通常还会导致移动它们进行了更大的图形坐标。

之前，你看到了两个转换公式，用于描述 `dx` 和 `dy`的翻译因素的影响：

x = x + dx

y = y + dy

`sx` 和 `sy` 的规模因素是乘法，而不是加法：

x = sx 推荐配置x

y = sy 推荐配置y

Translate 因素的默认值为 0;缩放比例的默认值为 1。

`SKCanvas` 类定义四个 `Scale` 方法。 第一种[`Scale`](xref:SkiaSharp.SKCanvas.Scale(System.Single))方法适用于需要相同水平和垂直比例因子的情况：

```csharp
public void Scale (Single s)
```

这称为*isotropic*缩放 &mdash; 缩放，这两个方向相同。 增益缩放保留对象的长宽比。

利用第二[`Scale`](xref:SkiaSharp.SKCanvas.Scale(System.Single,System.Single))方法，您可以指定不同的水平和垂直缩放值：

```csharp
public void Scale (Single sx, Single sy)
```

这会导致*各向异性*缩放。
第三个[`Scale`](xref:SkiaSharp.SKCanvas.Scale(SkiaSharp.SKPoint))方法将两个缩放系数合并为一个 `SKPoint` 值：

```csharp
public void Scale (SKPoint size)
```

第四个 `Scale` 方法将很快说明。

"**基本缩放**" 页演示 `Scale` 方法。 [**BasicScalePage**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/BasicScalePage.xaml)文件包含两个 `Slider` 元素，这些元素使您可以选择介于0和10之间的水平和垂直缩放系数。 [**BasicScalePage.xaml.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/BasicScalePage.xaml.cs)代码隐藏文件使用这些值来调用 `Scale`，然后显示用虚线描的圆角矩形，并调整其大小以适合画布左上角的某些文本：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear(SKColors.SkyBlue);

    using (SKPaint strokePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Red,
        StrokeWidth = 3,
        PathEffect = SKPathEffect.CreateDash(new float[] {  7, 7 }, 0)
    })
    using (SKPaint textPaint = new SKPaint
    {
        Style = SKPaintStyle.Fill,
        Color = SKColors.Blue,
        TextSize = 50
    })
    {
        canvas.Scale((float)xScaleSlider.Value,
                     (float)yScaleSlider.Value);

        SKRect textBounds = new SKRect();
        textPaint.MeasureText(Title, ref textBounds);

        float margin = 10;
        SKRect borderRect = SKRect.Create(new SKPoint(margin, margin), textBounds.Size);
        canvas.DrawRoundRect(borderRect, 20, 20, strokePaint);
        canvas.DrawText(Title, margin, -textBounds.Top + margin, textPaint);
    }
}
```

您可能想知道：缩放系数如何影响从 `SKPaint`的 `MeasureText` 方法返回的值呢？ 答案是： 根本不容易。 `Scale` 是 `SKCanvas`的方法。 它不会影响使用 `SKPaint` 对象执行的任何操作，除非你使用该对象在画布上呈现内容。

正如您所看到的，在 `Scale` 调用之后绘制的所有内容都会按比例增加：

[![](scale-images/basicscale-small.png "Triple screenshot of the Basic Scale page")](scale-images/basicscale-large.png#lightbox "Triple screenshot of the Basic Scale page")

文本，虚线中的边角，并在画布的左侧和顶部边缘和圆角的矩形之间的 10 个像素边距舍入的行的短划线的长度的宽度都受到相同的缩放比例。

> [!IMPORTANT]
> 通用 Windows 平台无法正确呈现 anisotropicly 缩放后的文本。

各向异性缩放原因变得不同的行的笔划宽度和对齐水平和垂直轴。 （此页面上的第一个图像也有明显的。）如果您不希望笔划宽度受缩放系数的影响，请将其设置为0，并且无论 `Scale` 设置如何，它都将始终为1个像素宽。

缩放是相对于画布的左上角。 这可能正是您所需但可能不会。 假设你想要在画布上放置文本和其他位置的矩形，并且你想要缩放相对于其中心。 在这种情况下，可以使用[`Scale`](xref:SkiaSharp.SKCanvas.Scale(System.Single,System.Single,System.Single,System.Single))方法的第四个版本，其中包括两个附加参数用于指定缩放中心：

```csharp
public void Scale (Single sx, Single sy, Single px, Single py)
```

`px` 和 `py` 参数定义有时称为 "*缩放中心*" 的点，但在 SkiaSharp 文档中称为 "*透视点*"。 这是不受缩放画布的相对于左上角的点。 所有缩放相对于该中心会发生。

"[**居中缩放**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/CenteredScalePage.xaml.cs)" 页显示其工作原理。 `PaintSurface` 处理程序与**基本缩放**程序类似，不同之处在于 `margin` 值计算为水平居中文本，这表示该程序最适合纵向模式：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear(SKColors.SkyBlue);

    using (SKPaint strokePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Red,
        StrokeWidth = 3,
        PathEffect = SKPathEffect.CreateDash(new float[] { 7, 7 }, 0)
    })
    using (SKPaint textPaint = new SKPaint
    {
        Style = SKPaintStyle.Fill,
        Color = SKColors.Blue,
        TextSize = 50
    })
    {
        SKRect textBounds = new SKRect();
        textPaint.MeasureText(Title, ref textBounds);
        float margin = (info.Width - textBounds.Width) / 2;

        float sx = (float)xScaleSlider.Value;
        float sy = (float)yScaleSlider.Value;
        float px = margin + textBounds.Width / 2;
        float py = margin + textBounds.Height / 2;

        canvas.Scale(sx, sy, px, py);

        SKRect borderRect = SKRect.Create(new SKPoint(margin, margin), textBounds.Size);
        canvas.DrawRoundRect(borderRect, 20, 20, strokePaint);
        canvas.DrawText(Title, margin, -textBounds.Top + margin, textPaint);
    }
}
```

圆角矩形的左上角位于画布的左侧，并从顶部 `margin` 像素 `margin`。 将 `Scale` 方法的最后两个参数设置为这些值以及文本的宽度和高度，也就是圆角矩形的宽度和高度。 这意味着，所有缩放是相对于该矩形的中心：

[![](scale-images/centeredscale-small.png "Triple screenshot of the Centered Scale page")](scale-images/centeredscale-large.png#lightbox "Triple screenshot of the Centered Scale page")

此程序中 `Slider` 元素的范围为 &ndash;10 到10。 正如您所看到的负值的垂直缩放 （如在中心屏幕在 Android 上） 将导致对象绕水平轴通过缩放的中心的翻转。 负值，水平缩放 （例如，如右侧 UWP 屏幕所示） 会导致对象绕垂直轴通过缩放的中心的翻转。

使用透视点的[`Scale`](xref:SkiaSharp.SKCanvas.Scale(System.Single,System.Single,System.Single,System.Single))方法的版本是三个 `Translate` 和 `Scale` 调用的序列的快捷方式。 您可能希望通过将 "**中心刻度**" 页中的 `Scale` 方法替换为以下内容来了解此功能的工作原理：

```csharp
canvas.Translate(-px, -py);
```

这些是假负的透视点坐标。

现在再次运行程序。 你将看到，以便在中心是在画布的左上角向矩形和文本。 几乎不能，您可以看到它。 滑块不支持当然，因为现在不会在所有扩展程序。

现在，在 `Translate` 调用*之前*添加基本 `Scale` 调用（没有缩放中心）：

```csharp
canvas.Scale(sx, sy);
canvas.Translate(–px, –py);
```

如果您熟悉此练习中其他图形编程系统，您可能认为这就是问题，但它不是。 Skia 处理连续的转换调用有点以不同的方式从您可能熟悉。

对于连续的 `Scale` 和 `Translate` 调用，圆角矩形的中心仍处于左上角，但现在可以相对于画布的左上角（也是圆角矩形的中心）对其进行缩放。

现在，在 `Scale` 调用之前，添加另一个具有中心值的 `Translate` 调用：

```csharp
canvas.Translate(px, py);
canvas.Scale(sx, sy);
canvas.Translate(–px, –py);
```

这会移动缩放后的结果返回到原始位置。 这些三种调用是等效于：

```csharp
canvas.Scale(sx, sy, px, py);
```

单个转换会更加复杂，以便总转换公式：

 x = sx 推荐配置(x – px) + px

 y = sy 推荐配置(y – py) + py

请记住，`sx` 和 `sy` 的默认值为1。 它很容易使您自己确信这些公式不转换中心点 （像素，py）。 它将保持在相同的位置相对于 canvas。

将 `Translate` 和 `Scale` 调用组合时，顺序很重要。 如果 `Translate` 出现在 `Scale`之后，则可通过缩放因子有效地调整转换因素。 如果 `Translate` 出现在 `Scale`之前，则不缩放转换因素。 此过程就会明白了某种程度上 (尽管较为复杂的数学) 引入的转换矩阵主题了。

`SKPath` 类定义一个只读[`Bounds`](xref:SkiaSharp.SKPath.Bounds)属性，该属性返回定义路径中的坐标范围的 `SKRect`。 例如，如果从前面创建的 hendecagram 路径获取 `Bounds` 属性，则该矩形的 `Left` 和 `Top` 属性大约为–100，`Right` 和 `Bottom` 属性大约为100，`Width` 和 `Height` 属性大约为200。 （大部分的实际值都是小小于因为星星的点定义的一个圆圈一个半径为 100，但只有顶部的点是并行具有水平或垂直轴。）

此信息的可用性意味着它应该可以派生缩放和平移因素适用于缩放画布的大小的路径。 "[**各向异性缩放**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/AnisotropicScalingPage.cs)" 页通过11形星形向此演示。 "*各向异性*" 刻度表示水平和垂直方向不相等，这意味着星号不会保留其原始纵横比。 下面是 `PaintSurface` 处理程序中的相关代码：

```csharp
SKPath path = HendecagramPage.HendecagramPath;
SKRect pathBounds = path.Bounds;

using (SKPaint fillPaint = new SKPaint
{
    Style = SKPaintStyle.Fill,
    Color = SKColors.Pink
})
using (SKPaint strokePaint = new SKPaint
{
    Style = SKPaintStyle.Stroke,
    Color = SKColors.Blue,
    StrokeWidth = 3,
    StrokeJoin = SKStrokeJoin.Round
})
{
    canvas.Scale(info.Width / pathBounds.Width,
                 info.Height / pathBounds.Height);
    canvas.Translate(-pathBounds.Left, -pathBounds.Top);

    canvas.DrawPath(path, fillPaint);
    canvas.DrawPath(path, strokePaint);
}
```

在此代码的顶部附近获取 `pathBounds` 矩形，然后将其与 `Scale` 调用中画布的宽度和高度一起使用。 当 `DrawPath` 调用呈现路径时，此调用将会缩放路径的坐标，而星号将在画布的右上角进行居中。 它需要向下和向左移动。 这是 `Translate` 调用的作业。 这两个 `pathBounds` 属性大约为–100，因此翻译因素约为100。 由于 `Translate` 调用在 `Scale` 调用之后，因此这些值通过缩放因子有效地缩放，因此它们将星形中心移动到画布中心：

[![](scale-images/anisotropicscaling-small.png "Triple screenshot of the Anisotropic Scaling page")](scale-images/anisotropicscaling-large.png#lightbox "Triple screenshot of the Anisotropic Scaling page")

您可以考虑到 `Scale` 和 `Translate` 调用的另一种方法是通过反向顺序来确定效果： `Translate` 调用将移动路径，使其完全可见，但在画布的左上角。 然后 `Scale` 方法会使该星形相对于左上角更大。

实际上，它将显示 star 是有点大于画布。 问题在于笔划宽度。 `SKPath` 的 `Bounds` 属性指示在路径中编码的坐标的尺寸，这就是程序用来对其进行缩放的内容。 使用特定的笔划宽度呈现路径时，呈现的路径大于画布。

若要解决此问题需要补偿的。 此程序中的一种简单方法是在 `Scale` 调用之前添加以下语句：

```csharp
pathBounds.Inflate(strokePaint.StrokeWidth / 2,
                   strokePaint.StrokeWidth / 2);
```

这会将 `pathBounds` 矩形增加四个边1.5 单位。 仅当舍入笔划联接时，这是一个合理的解决方案。 斜接联接可以较长，而且很难计算。

你还可以使用与文本相同的技术，如 "**各向异性文本**" 页所示。 下面是[`AnisotropicTextPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/AnisotropicTextPage.cs)类中 `PaintSurface` 处理程序的相关部分：

```csharp
using (SKPaint textPaint = new SKPaint
{
    Style = SKPaintStyle.Stroke,
    Color = SKColors.Blue,
    StrokeWidth = 0.1f,
    StrokeJoin = SKStrokeJoin.Round
})
{
    SKRect textBounds = new SKRect();
    textPaint.MeasureText("HELLO", ref textBounds);

    // Inflate bounds by the stroke width
    textBounds.Inflate(textPaint.StrokeWidth / 2,
                       textPaint.StrokeWidth / 2);

    canvas.Scale(info.Width / textBounds.Width,
                 info.Height / textBounds.Height);
    canvas.Translate(-textBounds.Left, -textBounds.Top);

    canvas.DrawText("HELLO", 0, 0, textPaint);
}
```

它类似于逻辑，文本根据从 `MeasureText` 返回的文本边界矩形（比实际文本稍大）扩展到页面的大小：

[![](scale-images/anisotropictext-small.png "Triple screenshot of the Anisotropic Test page")](scale-images/anisotropictext-large.png#lightbox "Triple screenshot of the Anisotropic Test page")

如果您需要保持图形对象的纵横比，您需要使用增益缩放。 " **Isotropic 缩放**" 页对11形星形显示了此项。 从概念上讲，用于在增益缩放页的中心显示图形对象的步骤如下：

- 转换到左上角的图形对象的中心。
- 缩放基于水平和垂直页尺寸由图形对象维度划分的最小值的对象。
- 转换中心的缩放到页面的中心对象。

在显示星号之前， [`IsotropicScalingPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/IsotropicScalingPage.cs)会按相反的顺序执行这些步骤：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    SKPath path = HendecagramArrayPage.HendecagramPath;
    SKRect pathBounds = path.Bounds;

    using (SKPaint fillPaint = new SKPaint())
    {
        fillPaint.Style = SKPaintStyle.Fill;

        float scale = Math.Min(info.Width / pathBounds.Width,
                               info.Height / pathBounds.Height);

        for (int i = 0; i <= 10; i++)
        {
            fillPaint.Color = new SKColor((byte)(255 * (10 - i) / 10),
                                          0,
                                          (byte)(255 * i / 10));
            canvas.Save();
            canvas.Translate(info.Width / 2, info.Height / 2);
            canvas.Scale(scale);
            canvas.Translate(-pathBounds.MidX, -pathBounds.MidY);
            canvas.DrawPath(path, fillPaint);
            canvas.Restore();

            scale *= 0.9f;
        }
    }
}
```

代码还显示星型 10 次，10%和逐渐将颜色更改由红色变为蓝色，降低缩放每次挑选：

[![](scale-images/isotropicscaling-small.png "Triple screenshot of the Isotropic Scaling page")](scale-images/isotropicscaling-large.png#lightbox "Triple screenshot of the Isotropic Scaling page")

## <a name="related-links"></a>相关链接

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
