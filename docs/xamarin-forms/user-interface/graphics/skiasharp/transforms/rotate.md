---
title: 旋转转换
description: 本文探讨的效果和动画可能具有 SkiaSharp 旋转转换，并演示此示例代码。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: CBB3CD72-4377-4EA3-A768-0C4228229FC2
author: davidbritch
ms.author: dabritch
ms.date: 03/23/2017
ms.openlocfilehash: 399f19ba4ec1ed8494e8269fc4cd0682b466a31a
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61187696"
---
# <a name="the-rotate-transform"></a>旋转转换

[![下载示例](~/media/shared/download.png)下载示例](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)

_浏览的效果和动画可能具有 SkiaSharp 旋转转换_

旋转转换，使用 SkiaSharp 图形对象可以具有水平和垂直轴打破免费的对齐方式的约束：

![](rotate-images/rotateexample.png "围绕中心旋转的文本")

用于轮换的图形对象围绕点 （0，0），同时支持 SkiaSharp [ `RotateDegrees` ](xref:SkiaSharp.SKCanvas.RotateDegrees(System.Single))方法和一个[ `RotateRadians` ](xref:SkiaSharp.SKCanvas.RotateRadians(System.Single))方法：

```csharp
public void RotateDegrees (Single degrees)

public Void RotateRadians (Single radians)
```

360 度圆等同于 twoπ 弧度为单位，因此很容易的两个单位之间进行转换。 使用者为准很方便。 在.NET 中的所有三角函数[ `Math` ](xref:System.Math)类使用弧度的单位。

进行增加角度顺时针旋转。 （尽管笛卡尔坐标系统上的旋转逆时针按照约定，顺时针旋转是一致的 Y 坐标为增加如下所示 SkiaSharp 停止运行。）负角度和角度大于允许 360 度。

旋转转换公式是相对于平移和缩放更复杂。 对于 α 的角度，转换公式为：

x' = x•cos(α) – y•sin(α)   

y` = x•sin(α) + y•cos(α)

**基本旋转**页说明`RotateDegrees`方法。 [ **BasicRotate.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/BasicRotatePage.xaml.cs)文件与位于页面中央其基线将显示一些文本，并将其基于旋转`Slider`介于-360 到 360 之间的范围。 以下是相关的部分`PaintSurface`处理程序：

```csharp
using (SKPaint textPaint = new SKPaint
{
    Style = SKPaintStyle.Fill,
    Color = SKColors.Blue,
    TextAlign = SKTextAlign.Center,
    TextSize = 100
})
{
    canvas.RotateDegrees((float)rotateSlider.Value);
    canvas.DrawText(Title, info.Width / 2, info.Height / 2, textPaint);
}
```

因为旋转中心周围的画布，在此程序中设置的大多数角度的左上角的文本在屏幕上旋转：

[![](rotate-images/basicrotate-small.png "基本旋转页面的三个屏幕截图")](rotate-images/basicrotate-large.png#lightbox "基本旋转页面的三个屏幕截图")

经常需要旋转内容以使用这些版本的指定的中心点为中心[ `RotateDegrees` ](xref:SkiaSharp.SKCanvas.RotateDegrees(System.Single,System.Single,System.Single))并[ `RotateRadians` ](xref:SkiaSharp.SKCanvas.RotateRadians(System.Single,System.Single,System.Single))方法：

```csharp
public void RotateDegrees (Single degrees, Single px, Single py)

public void RotateRadians (Single radians, Single px, Single py)
```

**居中旋转**页面则类似**基本旋转**只不过的扩展的版本`RotateDegrees`用于设置旋转中心的同一点用来将文字放置：

```csharp
using (SKPaint textPaint = new SKPaint
{
    Style = SKPaintStyle.Fill,
    Color = SKColors.Blue,
    TextAlign = SKTextAlign.Center,
    TextSize = 100
})
{
    canvas.RotateDegrees((float)rotateSlider.Value, info.Width / 2, info.Height / 2);
    canvas.DrawText(Title, info.Width / 2, info.Height / 2, textPaint);
}
```

现在围绕用于定位文本，这是水平居中的文本的基线的点旋转文本：

[![](rotate-images/centeredrotate-small.png "居中旋转页面的三个屏幕截图")](rotate-images/centeredrotate-large.png#lightbox "居中旋转页面的三个屏幕截图")

与使用的居中新版`Scale`方法中，居中的新版`RotateDegrees`调用是一种快捷方式。 下面是该方法：

```csharp
RotateDegrees (degrees, px, py);
```

该调用是等效于以下：

```csharp
canvas.Translate(px, py);
canvas.RotateDegrees(degrees);
canvas.Translate(-px, -py);
```

您会发现，有时可以组合`Translate`使用调用`Rotate`调用。 例如，以下是`RotateDegrees`并`DrawText`调用中**居中旋转**页;

```csharp
canvas.RotateDegrees((float)rotateSlider.Value, info.Width / 2, info.Height / 2);
canvas.DrawText(Title, info.Width / 2, info.Height / 2, textPaint);
```

`RotateDegrees`调用相当于两个`Translate`调用和非居中`RotateDegrees`:

```csharp
canvas.Translate(info.Width / 2, info.Height / 2);
canvas.RotateDegrees((float)rotateSlider.Value);
canvas.Translate(-info.Width / 2, -info.Height / 2);
canvas.DrawText(Title, info.Width / 2, info.Height / 2, textPaint);
```

`DrawText`调用的特定位置显示文本相当于`Translate`调用后跟该位置`DrawText`在点 （0，0）：

```csharp
canvas.Translate(info.Width / 2, info.Height / 2);
canvas.RotateDegrees((float)rotateSlider.Value);
canvas.Translate(-info.Width / 2, -info.Height / 2);
canvas.Translate(info.Width / 2, info.Height / 2);
canvas.DrawText(Title, 0, 0, textPaint);
```

两个连续`Translate`调用相互抵消：

```csharp
canvas.Translate(info.Width / 2, info.Height / 2);
canvas.RotateDegrees((float)rotateSlider.Value);
canvas.DrawText(Title, 0, 0, textPaint);
```

从概念上讲，相比于它们在代码中的出现顺序应用两次转换。 `DrawText`调用在画布的左上角显示的文本。 `RotateDegrees`调用旋转该文本相对于左上角。 然后`Translate`调用文本移动到画布的中心。

通常，有几种方法将旋转和转换合并。 **旋转文本**页创建显示在下面的：

[![](rotate-images/rotatedtext-small.png "旋转文本页的三个屏幕截图")](rotate-images/rotatedtext-large.png#lightbox "旋转文本页的三个屏幕截图")

下面是`PaintSurface`处理程序[ `RotatedTextPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/RotatedTextPage.cs)类：

```csharp
static readonly string text = "    ROTATE";
...
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    using (SKPaint textPaint = new SKPaint
    {
        Color = SKColors.Black,
        TextSize = 72
    })
    {
        float xCenter = info.Width / 2;
        float yCenter = info.Height / 2;

        SKRect textBounds = new SKRect();
        textPaint.MeasureText(text, ref textBounds);
        float yText = yCenter - textBounds.Height / 2 - textBounds.Top;

        for (int degrees = 0; degrees < 360; degrees += 30)
        {
            canvas.Save();
            canvas.RotateDegrees(degrees, xCenter, yCenter);
            canvas.DrawText(text, xCenter, yText, textPaint);
            canvas.Restore();
        }
    }
}

```

`xCenter`和`yCenter`值指示画布的中心。 `yText`值是有点相对于的偏移量。 此值是必需，以便它真正垂直方向上居中的页上确定文本的位置的 Y 坐标。 `for`循环然后设置基于画布的中心旋转。 在 30 度的增量旋转。 使用绘制文本`yText`值。 单词之前的空格数"旋转"`text`值根据经验确定，以使这些 12 文本字符串之间的连接似乎是 dodecagon。

若要简化此代码的一种方法是增加每次循环后的 30 度的旋转角度`DrawText`调用。 这样可以消除对的调用需要`Save`和`Restore`。 请注意，`degrees`正文中不能再使用变量`for`块：

```csharp
for (int degrees = 0; degrees < 360; degrees += 30)
{
    canvas.DrawText(text, xCenter, yText, textPaint);
    canvas.RotateDegrees(30, xCenter, yCenter);
}

```

还有可能要使用的简单的窗体`RotateDegrees`循环通过调用前面加`Translate`将所有内容移到画布的中心：

```csharp
float yText = -textBounds.Height / 2 - textBounds.Top;

canvas.Translate(xCenter, yCenter);

for (int degrees = 0; degrees < 360; degrees += 30)
{
    canvas.DrawText(text, 0, yText, textPaint);
    canvas.RotateDegrees(30);
}
```

已修改`yText`计算不再包含`yCenter`。 现在`DrawText`调用画布顶部垂直文本居中对齐。

相比于在代码中的显示方式从概念上讲应用转换，因为它通常是可能开始更具有全局性的转换后, 跟多个本地转换。 这通常是将旋转和转换组合的最简单方法。

例如，假设你想要围绕其中心类似于在其轴上旋转一颗绘制图形对象，该旋转对象。 但还想要围绕类似于居然太阳全球屏幕的中心旋转此对象。

可以通过在画布上，在窗口左上角中定位对象，然后使用动画来旋转对象周围的角来执行此操作。 接下来，将转换水平等绕 radius 的对象。 现在，应用还绕原点第二个动画的旋转。 这样，大部分丑闻都围绕角的对象。 立即转换到画布的中心。

下面是`PaintSurface`处理程序，它包含这些转换调用按相反的顺序：

```csharp
float revolveDegrees, rotateDegrees;
...
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    using (SKPaint fillPaint = new SKPaint
    {
        Style = SKPaintStyle.Fill,
        Color = SKColors.Red
    })
    {
        // Translate to center of canvas
        canvas.Translate(info.Width / 2, info.Height / 2);

        // Rotate around center of canvas
        canvas.RotateDegrees(revolveDegrees);

        // Translate horizontally
        float radius = Math.Min(info.Width, info.Height) / 3;
        canvas.Translate(radius, 0);

        // Rotate around center of object
        canvas.RotateDegrees(rotateDegrees);

        // Draw a square
        canvas.DrawRect(new SKRect(-50, -50, 50, 50), fillPaint);
    }
}
```

`revolveDegrees`和`rotateDegrees`字段具有动画效果。 此程序使用不同的动画技术通过基于 Xamarin.Forms [ `Animation` ](xref:Xamarin.Forms.Animation)类。 (此类中所述[第 22 章*使用 Xamarin.Forms 创建移动应用*](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch22-Apr2016.pdf))`OnAppearing`重写创建两个`Animation`对象使用回调方法，然后调用`Commit`对它们的动画持续时间：

```csharp
protected override void OnAppearing()
{
    base.OnAppearing();

    new Animation((value) => revolveDegrees = 360 * (float)value).
        Commit(this, "revolveAnimation", length: 10000, repeat: () => true);

    new Animation((value) =>
    {
        rotateDegrees = 360 * (float)value;
        canvasView.InvalidateSurface();
    }).Commit(this, "rotateAnimation", length: 1000, repeat: () => true);
}
```

第一个`Animation`对象进行动画处理`revolveDegrees`从 0 到 360 度超过 10 秒度。 第二个进行动画处理`rotateDegrees`从 0 到 360 度度每 1 秒，也使图面无效生成另一个调用`PaintSurface`处理程序。 `OnDisappearing`替代取消这些两个动画：

```csharp
protected override void OnDisappearing()
{
    base.OnDisappearing();
    this.AbortAnimation("revolveAnimation");
    this.AbortAnimation("rotateAnimation");
}
```

**难看模拟时钟**（因更具吸引力的模拟时钟将在后续文章中所述而得名） 的程序使用旋转来绘制时钟小时并分钟标记并旋转手中。 该程序绘制使用圆点 （0，0) 一个半径为 100 的中心基于任意坐标系统的时钟。 它使用平移和缩放可以展开并在页上的该圆形的中心。

`Translate`并`Scale`调用执行全局应用时钟，所以这些都是第一个要调用的初始化以下`SKPaint`对象：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    using (SKPaint strokePaint = new SKPaint())
    using (SKPaint fillPaint = new SKPaint())
    {
        strokePaint.Style = SKPaintStyle.Stroke;
        strokePaint.Color = SKColors.Black;
        strokePaint.StrokeCap = SKStrokeCap.Round;

        fillPaint.Style = SKPaintStyle.Fill;
        fillPaint.Color = SKColors.Gray;

        // Transform for 100-radius circle centered at origin
        canvas.Translate(info.Width / 2f, info.Height / 2f);
        canvas.Scale(Math.Min(info.Width / 200f, info.Height / 200f));
        ...
    }
}
```

有的两个不同的大小必须在一个圆周中全天候绘制 60 标记。 `DrawCircle`调用绘制该圆点 （0，– 90），它相对于时钟的中心对应为 12:00。 `RotateDegrees`调用每个刻度后递增 6 度的旋转角度。 `angle`变量仅用于确定绘制大圆圈或小圆圈：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    ...
        // Hour and minute marks
        for (int angle = 0; angle < 360; angle += 6)
        {
            canvas.DrawCircle(0, -90, angle % 30 == 0 ? 4 : 2, fillPaint);
            canvas.RotateDegrees(6);
        }
    ...
    }
}
```

最后，`PaintSurface`处理程序获取当前时间，并为小时、 分钟和第二个手计算旋转度。 每个指针以便的旋转角度是相对于相对，12:00 位置绘制：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    ...
        DateTime dateTime = DateTime.Now;

        // Hour hand
        strokePaint.StrokeWidth = 20;
        canvas.Save();
        canvas.RotateDegrees(30 * dateTime.Hour + dateTime.Minute / 2f);
        canvas.DrawLine(0, 0, 0, -50, strokePaint);
        canvas.Restore();

        // Minute hand
        strokePaint.StrokeWidth = 10;
        canvas.Save();
        canvas.RotateDegrees(6 * dateTime.Minute + dateTime.Second / 10f);
        canvas.DrawLine(0, 0, 0, -70, strokePaint);
        canvas.Restore();

        // Second hand
        strokePaint.StrokeWidth = 2;
        canvas.Save();
        canvas.RotateDegrees(6 * dateTime.Second);
        canvas.DrawLine(0, 10, 0, -80, strokePaint);
        canvas.Restore();
    }
}
```

尽管双手相当不成熟，时钟是肯定的功能。

[![](rotate-images/uglyanalogclock-small.png "三重难看模拟时钟文本页面的屏幕截图")](rotate-images/uglyanalogclock-large.png#lightbox "Triple screenshot of the Ugly Analog page")

对于更具吸引力的时钟，请参阅文章[ **SVG 路径数据中 SkiaSharp**](../curves/path-data.md)。

## <a name="related-links"></a>相关链接

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （示例）](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
