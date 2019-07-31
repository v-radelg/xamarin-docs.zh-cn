---
title: 缩放转换
description: Thhis 文章探讨了 SkiaSharp 缩放转换为缩放到不同大小的对象，此示例代码进行了演示。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 54A43F3D-9DA8-44A7-9AE4-7E3025129A0B
author: davidbritch
ms.author: dabritch
ms.date: 03/23/2017
ms.openlocfilehash: 2e9259bed6ad0ae5a926cb75ea74c1f379897220
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2019
ms.locfileid: "68649278"
---
# <a name="the-scale-transform"></a>缩放转换

[![下载示例](~/media/shared/download.png)下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_发现 SkiaSharp 缩放转换为缩放到不同大小的对象_

如您所见中[**转换转换**](translate.md)文章翻译转换可以图形对象从一个位置移动到另一个。 与此相反，缩放转换将更改图形的对象的大小：

![](scale-images/scaleexample.png "在大小进行缩放、 高 word")

缩放转换通常还会导致移动它们进行了更大的图形坐标。

前面描述的翻译因素的影响的两个转换公式`dx`和`dy`:

x = x + dx

y = y + dy

缩放的比例`sx`和`sy`是乘法而不是累加性：

x = sx 推荐配置x

y = sy 推荐配置y

Translate 因素的默认值为 0;缩放比例的默认值为 1。

`SKCanvas`类定义了四个`Scale`方法。 第一个[ `Scale` ](xref:SkiaSharp.SKCanvas.Scale(System.Single))方法对于用例时所需的相同水平和垂直缩放因素是：

```csharp
public void Scale (Single s)
```

这称为*增益*缩放&mdash;缩放，它相同两个方向。 增益缩放保留对象的长宽比。

第二个[ `Scale` ](xref:SkiaSharp.SKCanvas.Scale(System.Single,System.Single))方法允许你指定的水平和垂直缩放不同的值：

```csharp
public void Scale (Single sx, Single sy)
```

这会导致*各向异性*缩放。
第三个[ `Scale` ](xref:SkiaSharp.SKCanvas.Scale(SkiaSharp.SKPoint))方法将组合在单个的两个缩放因素`SKPoint`值：

```csharp
public void Scale (SKPoint size)
```

第四个`Scale`将稍后所述方法。

**基本的规模**页说明`Scale`方法。 [ **BasicScalePage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/BasicScalePage.xaml)文件包含两个`Slider`元素，可选择介于 0 和 10 之间的水平和垂直比例因子。 [ **BasicScalePage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/BasicScalePage.xaml.cs)代码隐藏文件中使用这些值以调用`Scale`显示圆角的矩形用虚线描边和调整大小以适应在左上方的一些文本之前画布的角：

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

您可能想知道:缩放系数如何影响从`MeasureText`方法返回的`SKPaint`值？ 答案是:完全不需要。 `Scale` 是一种方法的`SKCanvas`。 它不影响与执行任何操作`SKPaint`对象，直到该对象用于呈现在画布上的某些内容。

您可以看到，所有内容绘制后`Scale`调用按比例增加：

[![](scale-images/basicscale-small.png "三个基本的规模页屏幕截图")](scale-images/basicscale-large.png#lightbox "带来三倍的基本的规模页屏幕截图")

文本，虚线中的边角，并在画布的左侧和顶部边缘和圆角的矩形之间的 10 个像素边距舍入的行的短划线的长度的宽度都受到相同的缩放比例。

> [!IMPORTANT]
> 通用 Windows 平台无法正确呈现 anisotropicly 缩放后的文本。

各向异性缩放原因变得不同的行的笔划宽度和对齐水平和垂直轴。 （这也是表露此页面上的第一个图像。）如果不希望笔划宽度受到影响的缩放比例，将其设置为 0，它将始终为一个像素宽而不考虑`Scale`设置。

缩放是相对于画布的左上角。 这可能正是您所需但可能不会。 假设你想要在画布上放置文本和其他位置的矩形，并且你想要缩放相对于其中心。 在这种情况下可以使用的第四个版本[ `Scale` ](xref:SkiaSharp.SKCanvas.Scale(System.Single,System.Single,System.Single,System.Single))方法，其中包含两个附加参数来指定缩放中心：

```csharp
public void Scale (Single sx, Single sy, Single px, Single py)
```

`px`并`py`参数定义一个点，它有时被称为*缩放中心*但中 SkiaSharp 文档被称为*透视点*。 这是不受缩放画布的相对于左上角的点。 所有缩放相对于该中心会发生。

[**居中规模**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/CenteredScalePage.xaml.cs)页显示了具体过程。 `PaintSurface`处理程序是类似于**基本的规模**程序不同之处在于`margin`计算值以使文本水平居中，这意味着程序适合在纵向模式下：

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

圆角矩形的左上角位于`margin`从画布左侧的像素和`margin`像素。 最后两个参数`Scale`方法设置为这些值加上的宽度和高度的文本，这也是圆角矩形的宽度和高度。 这意味着，所有缩放是相对于该矩形的中心：

[![](scale-images/centeredscale-small.png "居中缩放页的三个屏幕截图")](scale-images/centeredscale-large.png#lightbox "三重居中缩放页屏幕截图")

`Slider`在此程序中的元素具有一系列&ndash;10 到 10。 正如您所看到的负值的垂直缩放 （如在中心屏幕在 Android 上） 将导致对象绕水平轴通过缩放的中心的翻转。 负值，水平缩放 （例如，如右侧 UWP 屏幕所示） 会导致对象绕垂直轴通过缩放的中心的翻转。

版本[ `Scale` ](xref:SkiaSharp.SKCanvas.Scale(System.Single,System.Single,System.Single,System.Single))透视点的方法是一系列的三个的快捷方式`Translate`和`Scale`调用。 您可能想要了解这通过将替换为的工作原理`Scale`中的方法**居中规模**以下页：

```csharp
canvas.Translate(-px, -py);
```

这些是假负的透视点坐标。

现在再次运行程序。 你将看到，以便在中心是在画布的左上角向矩形和文本。 几乎不能，您可以看到它。 滑块不支持当然，因为现在不会在所有扩展程序。

现在，添加基本`Scale`（不带缩放的中心） 调用*之前*的`Translate`调用：

```csharp
canvas.Scale(sx, sy);
canvas.Translate(–px, –py);
```

如果您熟悉此练习中其他图形编程系统，您可能认为这就是问题，但它不是。 Skia 处理连续的转换调用有点以不同的方式从您可能熟悉。

使用连续`Scale`和`Translate`调用，圆角矩形的中心仍在左上角，但您可以将其横向相对于左上角的画布，这也是圆角矩形的中心。

现在，在此之前`Scale`调用中，添加另一个`Translate`居中值调用：

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

请记住的默认值`sx`和`sy`均为 1。 它很容易使您自己确信这些公式不转换中心点 （像素，py）。 它将保持在相同的位置相对于 canvas。

结合`Translate`和`Scale`调用，则顺序很重要。 如果`Translate`之后`Scale`，平移因数有效地进行缩放的缩放比例。 如果`Translate`之前`Scale`，平移因数不进行缩放。 此过程就会明白了某种程度上 (尽管较为复杂的数学) 引入的转换矩阵主题了。

`SKPath`类定义的只读[ `Bounds` ](xref:SkiaSharp.SKPath.Bounds)返回的属性`SKRect`路径中定义的坐标范围。 例如，当`Bounds`从更早版本，创建的 hendecagram 路径获取属性`Left`并`Top`矩形的属性是大约-100、 步长`Right`和`Bottom`属性大约 100，并`Width`和`Height`属性是大约 200 个。 （大部分的实际值都是小小于因为星星的点定义的一个圆圈一个半径为 100，但只有顶部的点是并行具有水平或垂直轴。）

此信息的可用性意味着它应该可以派生缩放和平移因素适用于缩放画布的大小的路径。 [**各向异性缩放**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/AnisotropicScalingPage.cs)页此进行了演示使用 11 星。 *各向异性*规模意味着，它是在水平方向和垂直方向，不相等这意味着在星型将不会保留原始纵横比。 下面是相关的代码中`PaintSurface`处理程序：

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

`pathBounds`获取此代码中，顶部附近，并随后用于更高版本的宽度和高度画布中的矩形`Scale`调用。 通过呈现时，本身的调用将扩展的路径中的坐标`DrawPath`星形，但是该调用将在画布的右上角中居中。 它需要向下和向左移动。 这是作业的`Translate`调用。 这两个属性的`pathBounds`是大约-100、 步长，因此翻译因素是大约 100。 因为`Translate`调用晚`Scale`调用，这些值的有效地缩放的缩放比例，以便它们将在星型的中心移动到画布的中心：

[![](scale-images/anisotropicscaling-small.png "三重各向异性缩放页屏幕截图")](scale-images/anisotropicscaling-large.png#lightbox "各向异性缩放页上的三个屏幕截图")

您可以考虑`Scale`和`Translate`调用的另一种方法是通过反向序列确定效果:`Translate`调用将移动路径, 使其完全可见, 但在画布的左上角。 `Scale`方法然后使该星型相对于左上角更大。

实际上，它将显示 star 是有点大于画布。 问题在于笔划宽度。 `Bounds`属性的`SKPath`指示坐标维数编码在路径中，并且该程序使用来扩展它。 使用特定的笔划宽度呈现路径时，呈现的路径大于画布。

若要解决此问题需要补偿的。 在此程序中的一种简单方法是添加以下语句右侧之前`Scale`调用：

```csharp
pathBounds.Inflate(strokePaint.StrokeWidth / 2,
                   strokePaint.StrokeWidth / 2);
```

这会增加`pathBounds`通过所有四个边 1.5 个单位的矩形。 仅当舍入笔划联接时，这是一个合理的解决方案。 斜接联接可以较长，而且很难计算。

此外可以使用类似的技术与文本，作为**各向异性文本**页说明。 下面是相关部分`PaintSurface`处理程序[ `AnisotropicTextPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/AnisotropicTextPage.cs)类：

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

它是类似的逻辑和文本扩展的基于文本的边界矩形从返回的页大小为`MeasureText`（这可能有点大于实际文本）：

[![](scale-images/anisotropictext-small.png "各向异性测试页的三个屏幕截图")](scale-images/anisotropictext-large.png#lightbox "各向异性测试页的三个屏幕截图")

如果您需要保持图形对象的纵横比，您需要使用增益缩放。 **增益缩放**页此进行了演示为 11 星。 从概念上讲，用于在增益缩放页的中心显示图形对象的步骤如下：

- 转换到左上角的图形对象的中心。
- 缩放基于水平和垂直页尺寸由图形对象维度划分的最小值的对象。
- 转换中心的缩放到页面的中心对象。

[ `IsotropicScalingPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/skia-sharp-forms/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/IsotropicScalingPage.cs)显示星号前会按相反的顺序执行以下步骤：

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

[![](scale-images/isotropicscaling-small.png "三重增益缩放页屏幕截图")](scale-images/isotropicscaling-large.png#lightbox "增益缩放页的三个屏幕截图")


## <a name="related-links"></a>相关链接

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
