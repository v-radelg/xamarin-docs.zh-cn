---
title: 倾斜转换
description: 本文介绍如何倾斜转换可以在 SkiaSharp 中, 创建倾斜的图形对象，并演示此示例代码。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: FDD16186-E3B7-4FF6-9BC2-8A2974BFF616
author: davidbritch
ms.author: dabritch
ms.date: 03/20/2017
ms.openlocfilehash: a0c65ab2d319e39b236799cd453874142206b467
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61355760"
---
# <a name="the-skew-transform"></a>倾斜转换

[![下载示例](~/media/shared/download.png)下载示例](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)

_请参阅如何倾斜转换可以在 SkiaSharp 中创建倾斜的图形对象_

SkiaSharp 中, 倾斜转换将倾斜图形对象，如在此图中的阴影：

![](skew-images/skewexample.png "下面举例说明倾斜从倾斜阴影文本程序")

倾斜将转换为一个平行四边形的矩形，但偏斜的椭圆仍是一个椭圆。

虽然 Xamarin.Forms 定义平移、 缩放和旋转的属性，但没有对应的属性中 Xamarin.Forms 偏差。

[ `Skew` ](xref:SkiaSharp.SKCanvas.Skew(System.Single,System.Single))方法的`SKCanvas`接受倾斜的水平扭曲和垂直两个参数：

```csharp
public void Skew (Single xSkew, Single ySkew)
```

第二个[ `Skew` ](xref:SkiaSharp.SKCanvas.Skew(SkiaSharp.SKPoint))方法结合了这些参数在单个`SKPoint`值：

```csharp
public void Skew (SKPoint skew)
```

但是，不太可能，您将这两种方法之一中使用隔离。

**倾斜试验**页可让您体验偏差值的 – 10 和 10 之间的范围。 使用来自两个偏差值在页上，在窗口左上角中定位文本字符串`Slider`元素。 下面是`PaintSurface`处理程序中的[ `SkewExperimentPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/SkewExperimentPage.xaml.cs)类：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    using (SKPaint textPaint = new SKPaint
    {
        Style = SKPaintStyle.Fill,
        Color = SKColors.Blue,
        TextSize = 200
    })
    {
        string text = "SKEW";
        SKRect textBounds = new SKRect();
        textPaint.MeasureText(text, ref textBounds);

        canvas.Skew((float)xSkewSlider.Value, (float)ySkewSlider.Value);
        canvas.DrawText(text, 0, -textBounds.Top, textPaint);
    }
}
```

值`xSkew`参数转变底部的文本适合正值或负值的左侧。 值`ySkew`对于正值或负值进行向下移动文本右侧：

[![](skew-images/skewexperiment-small.png "倾斜试验页面的三个屏幕截图")](skew-images/skewexperiment-large.png#lightbox "倾斜试验页面的三个屏幕截图")

如果`xSkew`值为的`ySkew`值，结果是旋转，但还应为某种程度上进行缩放 UWP 显示指示。

转换公式如下所示：

x = x + xSkew 推荐配置y

y = ySkew 推荐配置x + y

例如，对于一个正`xSkew`值，已转换`x'`值随着`y`会增加。 这就是哪些因素会导致倾斜。

如果三角形 200 像素宽和高 100 像素定位时，点 （0，0） 在其左上角和使用呈现`xSkew`值为 1.5，以下的平行四边形结果：

![](skew-images/skeweffect.png "对矩形的倾斜转换影响")

下边缘坐标的`y`值为 100，因此它是向右移动 150 像素。

有关的值为非零`xSkew`或`ySkew`、 仅点 （0，0） 保持不变。 该点可被视为倾斜的中心。 如果需要中心的倾斜为其他内容 （这通常都是如此），则没有`Skew`提供，它的方法。 你将需要进行显式结合`Translate`使用调用`Skew`调用。 居中在倾斜`px`和`py`，进行以下调用：

```csharp
canvas.Translate(px, py);
canvas.Skew(xSkew, ySkew);
canvas.Translate(-px, -py);
```

复合转换公式是：

x = x + xSkew 推荐配置（y – 上一年度）

y = ySkew 推荐配置(x – px) + y

如果`ySkew`为零，则`px`不使用值。 值是不相关的以及同样`ySkew`和`py`。

您可能更喜欢指定倾斜的角度的倾斜，如在此图中的角度 α 作为：

![](skew-images/skewangleeffect.png "指示对矩形的扭曲角度的倾斜转换影响")

为 100 像素垂直 150 像素 shift 的比率为在此示例中该角度的正切值 56.3 度。

XAML 文件**倾斜角度实验**页是类似于**倾斜角度**页上，不同之处在于`Slider`元素范围 – 90 度到 90 度。 [ `SkewAngleExperiment` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/SkewAngleExperimentPage.xaml.cs)代码隐藏文件中心页上的文本，并使用`Translate`设置中心的倾斜到页面的中心。 一小段`SkewDegrees`方法底部的代码将转换为角度，若要倾斜值：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    using (SKPaint textPaint = new SKPaint
    {
        Style = SKPaintStyle.Fill,
        Color = SKColors.Blue,
        TextSize = 200
    })
    {
        float xCenter = info.Width / 2;
        float yCenter = info.Height / 2;

        string text = "SKEW";
        SKRect textBounds = new SKRect();
        textPaint.MeasureText(text, ref textBounds);
        float xText = xCenter - textBounds.MidX;
        float yText = yCenter - textBounds.MidY;

        canvas.Translate(xCenter, yCenter);
        SkewDegrees(canvas, xSkewSlider.Value, ySkewSlider.Value);
        canvas.Translate(-xCenter, -yCenter);
        canvas.DrawText(text, xText, yText, textPaint);
    }
}

void SkewDegrees(SKCanvas canvas, double xDegrees, double yDegrees)
{
    canvas.Skew((float)Math.Tan(Math.PI * xDegrees / 180),
                (float)Math.Tan(Math.PI * yDegrees / 180));
}
```

随着角度接近正或负 90 度，正切值接近无穷大，但最多约 80 度或操作的角度可使用：

[![](skew-images/skewangleexperiment-small.png "三重的倾斜角度实验页的屏幕截图")](skew-images/skewangleexperiment-large.png#lightbox "带来三倍的倾斜角度实验页的屏幕截图")

较小的负水平扭曲可模拟倾斜或斜体文本作为**倾斜文本**页说明。 [ `ObliqueTextPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/ObliqueTextPage.cs)类显示了如何执行操作：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    using (SKPaint textPaint = new SKPaint()
    {
        Style = SKPaintStyle.Fill,
        Color = SKColors.Maroon,
        TextAlign = SKTextAlign.Center,
        TextSize = info.Width / 8       // empirically determined
    })
    {
        canvas.Translate(info.Width / 2, info.Height / 2);
        SkewDegrees(canvas, -20, 0);
        canvas.DrawText(Title, 0, 0, textPaint);
    }
}

void SkewDegrees(SKCanvas canvas, double xDegrees, double yDegrees)
{
    canvas.Skew((float)Math.Tan(Math.PI * xDegrees / 180),
                (float)Math.Tan(Math.PI * yDegrees / 180));
}
```

`TextAlign`的属性`SKPaint`设置为`Center`。 不涉及任何转换，`DrawText`调用使用的坐标 （0，0） 将放置在左上角的文本的水平居中的基线。 `SkewDegrees`相对于基线的 20 度水平扭曲文本。 `Translate`调用将水平居中的文本的基线移到画布的中心：

[![](skew-images/obliquetext-small.png "三个倾斜的文本页屏幕截图")](skew-images/obliquetext-large.png#lightbox "倾斜的文本页的三个屏幕截图")

**倾斜阴影文字**页演示了如何使用组合的 45 度倾斜和垂直缩放以便使其远离文字将倾斜文本阴影。 以下是相关的部分`PaintSurface`处理程序：

```csharp
using (SKPaint textPaint = new SKPaint())
{
    textPaint.Style = SKPaintStyle.Fill;
    textPaint.TextSize = info.Width / 6;   // empirically determined

    // Common to shadow and text
    string text = "Shadow";
    float xText = 20;
    float yText = info.Height / 2;

    // Shadow
    textPaint.Color = SKColors.LightGray;
    canvas.Save();
    canvas.Translate(xText, yText);
    canvas.Skew((float)Math.Tan(-Math.PI / 4), 0);
    canvas.Scale(1, 3);
    canvas.Translate(-xText, -yText);
    canvas.DrawText(text, xText, yText, textPaint);
    canvas.Restore();

    // Text
    textPaint.Color = SKColors.Blue;
    canvas.DrawText(text, xText, yText, textPaint);
}
```

阴影是显示第一个，然后选择文本：

[![](skew-images/skewshadowtext1-small.png "倾斜阴影文本页的三个屏幕截图")](skew-images/skewshadowtext1-large.png#lightbox "倾斜阴影文本页的三个屏幕截图")

垂直坐标传递到`DrawText`方法指示的位置相对于基线的文本。 这是用于倾斜的中心的相同垂直坐标。 如果文本字符串包含下行字母，此方法将无法工作。 例如，将"古怪"一词替换为"影子"，下面是结果：

[![](skew-images/skewshadowtext2-small.png "三重下行字母的备选单词倾斜阴影文字页屏幕截图")](skew-images/skewshadowtext2-large.png#lightbox "带来三倍的下行字母的备选单词倾斜阴影文本页面屏幕截图")

卷影和文本仍对齐的基线，但效果只是看起来有错误。 若要修复此错误，您需要获取文本边界：

```csharp
SKRect textBounds = new SKRect();
textPaint.MeasureText(text, ref textBounds);
```

`Translate`调用需要进行调整的下行字母的高度：

```csharp
canvas.Translate(xText, yText + textBounds.Bottom);
canvas.Skew((float)Math.Tan(-Math.PI / 4), 0);
canvas.Scale(1, 3);
canvas.Translate(-xText, -yText - textBounds.Bottom);
```

现在阴影扩展从这些下行字母的底部：

[![](skew-images/skewshadowtext3-small.png "三重调整来获得下行字母倾斜阴影文字页屏幕截图")](skew-images/skewshadowtext3-large.png#lightbox "三重调整来获得下行字母倾斜阴影文字页屏幕截图")


## <a name="related-links"></a>相关链接

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （示例）](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
