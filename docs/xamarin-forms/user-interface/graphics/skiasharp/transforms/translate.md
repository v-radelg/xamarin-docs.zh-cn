---
title: 翻译转换
description: 本文探讨如何使用转换变换移动 SkiaSharp 图形在 Xamarin.Forms 应用程序，并演示此示例代码。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: BD28ADA1-49F9-44E2-A548-46024A29882F
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2017
ms.openlocfilehash: f1efd7610b32e6a3903d34fc2f8b5a6e20c9da8a
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76723601"
---
# <a name="the-translate-transform"></a>翻译转换

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_了解如何使用转换转换 SkiaSharp 图形_

SkiaSharp 中最简单的转换类型*是转换转换*或*转换*转换。 此转换将在水平和垂直方向上的图形对象。 在某种意义上，转换是最不必要的转换，因为通常可以完成相同的效果，只需更改绘图函数中使用的坐标。 当呈现一个路径，但是，所有坐标都封装在路径中，因此很容易得多，在应用转换变换，若要切换的完整路径。

转换也是适用于动画以及简单的文本效果：

![](translate-images/translateexample.png "Text shadow, engraving, and embossing with translation")

`SKCanvas` 中的[`Translate`](xref:SkiaSharp.SKCanvas.Translate(System.Single,System.Single))方法具有两个参数，这两个参数会导致在水平方向和垂直方向移动图形对象：

```csharp
public void Translate (Single dx, Single dy)
```

这些参数可为负数。 第二个[`Translate`](xref:SkiaSharp.SKCanvas.Translate(SkiaSharp.SKPoint))方法将两个翻译值组合成单个 `SKPoint` 值：

```csharp
public void Translate (SKPoint point)
```

[**SkiaSharpForms**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)示例程序的**累积转换**页面说明了 `Translate` 方法的多个调用是累积的。 [`AccumulatedTranslatePage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/AccumulatedTranslatePage.cs)类显示同一个矩形的20个版本，每个版本都与前一个矩形的偏移量刚好足够，因此它们沿对角线拉伸。 下面是 `PaintSurface` 事件处理程序：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    using (SKPaint strokePaint = new SKPaint())
    {
        strokePaint.Color = SKColors.Black;
        strokePaint.Style = SKPaintStyle.Stroke;
        strokePaint.StrokeWidth = 3;

        int rectangleCount = 20;
        SKRect rect = new SKRect(0, 0, 250, 250);
        float xTranslate = (info.Width - rect.Width) / (rectangleCount - 1);
        float yTranslate = (info.Height - rect.Height) / (rectangleCount - 1);

        for (int i = 0; i < rectangleCount; i++)
        {
            canvas.DrawRect(rect, strokePaint);
            canvas.Translate(xTranslate, yTranslate);
        }
    }
}
```

连续矩形渗透页：

[![](translate-images/accumulatedtranslate-small.png "Triple screenshot of the Accumulated Translate page")](translate-images/accumulatedtranslate-large.png#lightbox "Triple screenshot of the Accumulated Translate page")

如果累积的平移因子 `dx` 和 `dy`，并且您在绘图函数中指定的点为（`x`，`y`），则图形对象将呈现在该点（`x'`，`y'`），其中：

x = x + dx

y = y + dy

这称为*转换公式*。 新 `SKCanvas` `dx` 和 `dy` 的默认值为0。

由于 "**翻译文本效果**" 页面所示，通常使用转换转换来实现阴影效果和类似的技术。 下面是[`TranslateTextEffectsPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/TranslateTextEffectsPage.cs)类中 `PaintSurface` 处理程序的相关部分：

```csharp
float textSize = 150;

using (SKPaint textPaint = new SKPaint())
{
    textPaint.Style = SKPaintStyle.Fill;
    textPaint.TextSize = textSize;
    textPaint.FakeBoldText = true;

    float x = 10;
    float y = textSize;

    // Shadow
    canvas.Translate(10, 10);
    textPaint.Color = SKColors.Black;
    canvas.DrawText("SHADOW", x, y, textPaint);
    canvas.Translate(-10, -10);
    textPaint.Color = SKColors.Pink;
    canvas.DrawText("SHADOW", x, y, textPaint);

    y += 2 * textSize;

    // Engrave
    canvas.Translate(-5, -5);
    textPaint.Color = SKColors.Black;
    canvas.DrawText("ENGRAVE", x, y, textPaint);
    canvas.ResetMatrix();
    textPaint.Color = SKColors.White;
    canvas.DrawText("ENGRAVE", x, y, textPaint);

    y += 2 * textSize;

    // Emboss
    canvas.Save();
    canvas.Translate(5, 5);
    textPaint.Color = SKColors.Black;
    canvas.DrawText("EMBOSS", x, y, textPaint);
    canvas.Restore();
    textPaint.Color = SKColors.White;
    canvas.DrawText("EMBOSS", x, y, textPaint);
}
```

在这三个示例的每个示例中，都将调用 `Translate` 以显示文本，使其与 `x` 和 `y` 变量给定的位置偏移。 然后文本会再次显示，在使用任何转换效果的另一种颜色：

[![](translate-images/translatetexteffects-small.png "Triple screenshot of the Translate Text Effects page")](translate-images/translatetexteffects-large.png#lightbox "Triple screenshot of the Translate Text Effects page")

这三个示例中的每一个都说明了取消 `Translate` 调用的不同方法：

第一个示例只是再次调用 `Translate`，但带有负值。 由于 `Translate` 调用是累积性的，因此，此调用序列只会将总转换还原为默认值零。

第二个示例调用[`ResetMatrix`](xref:SkiaSharp.SKCanvas.ResetMatrix)。 这会导致要返回到其默认状态的所有转换。

第三个示例通过调用[`Save`](xref:SkiaSharp.SKCanvas.Save)保存 `SKCanvas` 对象的状态，然后通过调用[`Restore`](xref:SkiaSharp.SKCanvas.Restore)还原状态。 这是最通用的方法来处理一系列的绘制操作的转换。 这些 `Save` 和 `Restore` 调用的功能类似于堆栈：可以多次调用 `Save`，然后在反向序列中调用 `Restore` 以返回到以前的状态。 `Save` 方法返回一个整数，你可以将该整数传递到[`RestoreToCount`](xref:SkiaSharp.SKCanvas.RestoreToCount*) ，以便有效地多次调用 `Restore`。 [`SaveCount`](xref:SkiaSharp.SKCanvas.SaveCount)属性返回当前在堆栈上保存的状态数。

你还可以使用[`SKAutoCanvasRestore`](xref:SkiaSharp.SKAutoCanvasRestore)类还原画布状态。 此类的构造函数应在 `using` 语句中调用;在 `using` 块结束时，将自动还原画布状态。

但是，您不必担心从 `PaintSurface` 处理程序的一次调用到接下来的转换。 对 `PaintSurface` 的每个新调用都提供具有默认转换的全新 `SKCanvas` 对象。

`Translate` 转换的另一种常见用途是呈现一个视觉对象，该对象最初是使用便于绘制的坐标创建的。 例如，你可能想要使用中心点 （0，0） 处指定了模拟时钟的坐标。 你可以然后使用转换来显示时钟所需的位置。 此方法在 [**Hendecagram Array**] 页中进行了演示。 [`HendecagramArrayPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/HendecagramArrayPage.cs)类首先创建一个用于11指星形的 `SKPath` 对象。 `HendecagramPath` 对象定义为 public、static 和只读，以便可以从其他演示程序访问它。 在静态构造函数创建：

```csharp
public class HendecagramArrayPage : ContentPage
{
    ...
    public static readonly SKPath HendecagramPath;

    static HendecagramArrayPage()
    {
        // Create 11-pointed star
        HendecagramPath = new SKPath();
        for (int i = 0; i < 11; i++)
        {
            double angle = 5 * i * 2 * Math.PI / 11;
            SKPoint pt = new SKPoint(100 * (float)Math.Sin(angle),
                                    -100 * (float)Math.Cos(angle));
            if (i == 0)
            {
                HendecagramPath.MoveTo(pt);
            }
            else
            {
                HendecagramPath.LineTo(pt);
            }
        }
        HendecagramPath.Close();
    }
}
```

如果在星型的中心点 （0，0），在星型的所有点都都在该点周围的圆圈。 每个点都增加 5/11ths 的 360 度的角度的正弦和余弦值的组合。 （也可以通过将角度增加 2/11、3/11ths 或圆形的 4/11 来创建11尖星形。）该圆的半径设置为100。

如果在呈现时不带任何转换的路径，则将中心置于 `SKCanvas`的左上角，且只有一个季度显示。 `HendecagramPage` 的 `PaintSurface` 处理程序改用 `Translate`，用星形的多个副本平铺画布，其中每个副本随机着色：

```csharp
public class HendecagramArrayPage : ContentPage
{
    Random random = new Random();
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPaint paint = new SKPaint())
        {
            for (int x = 100; x < info.Width + 100; x += 200)
                for (int y = 100; y < info.Height + 100; y += 200)
                {
                    // Set random color
                    byte[] bytes = new byte[3];
                    random.NextBytes(bytes);
                    paint.Color = new SKColor(bytes[0], bytes[1], bytes[2]);

                    // Display the hendecagram
                    canvas.Save();
                    canvas.Translate(x, y);
                    canvas.DrawPath(HendecagramPath, paint);
                    canvas.Restore();
                }
        }
    }
}

```

下面是结果：

[![](translate-images/hendecagramarray-small.png "Triple screenshot of the Hendecagram Array page")](translate-images/hendecagramarray-large.png#lightbox "Triple screenshot of the Hendecagram Array page")

动画通常涉及到转换。 " **Hendecagram 动画**" 页在圆圈中移动了11形星形。 [`HendecagramAnimationPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/HendecagramAnimationPage.cs)类以某些字段开头，并覆盖 `OnAppearing` 和 `OnDisappearing` 方法来启动和停止 Xamarin。窗体计时器：

```csharp
public class HendecagramAnimationPage : ContentPage
{
    const double cycleTime = 5000;      // in milliseconds

    SKCanvasView canvasView;
    Stopwatch stopwatch = new Stopwatch();
    bool pageIsActive;
    float angle;

    public HendecagramAnimationPage()
    {
        Title = "Hedecagram Animation";

        canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }

    protected override void OnAppearing()
    {
        base.OnAppearing();
        pageIsActive = true;
        stopwatch.Start();

        Device.StartTimer(TimeSpan.FromMilliseconds(33), () =>
        {
            double t = stopwatch.Elapsed.TotalMilliseconds % cycleTime / cycleTime;
            angle = (float)(360 * t);
            canvasView.InvalidateSurface();

            if (!pageIsActive)
            {
                stopwatch.Stop();
            }

            return pageIsActive;
        });
    }

    protected override void OnDisappearing()
    {
        base.OnDisappearing();
        pageIsActive = false;
    }
    ...
}
```

`angle` 字段每5秒从0度到360度进行动画处理。 `PaintSurface` 处理程序通过两种方式使用 `angle` 属性：指定 `SKColor.FromHsl` 方法中颜色的色调，并将其作为参数指定给 `Math.Sin`，并使用 `Math.Cos` 方法控制星形的位置：

```csharp
public class HendecagramAnimationPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();
        canvas.Translate(info.Width / 2, info.Height / 2);
        float radius = (float)Math.Min(info.Width, info.Height) / 2 - 100;

        using (SKPaint paint = new SKPaint())
        {
            paint.Style = SKPaintStyle.Fill;
            paint.Color = SKColor.FromHsl(angle, 100, 50);

            float x = radius * (float)Math.Sin(Math.PI * angle / 180);
            float y = -radius * (float)Math.Cos(Math.PI * angle / 180);
            canvas.Translate(x, y);
            canvas.DrawPath(HendecagramPage.HendecagramPath, paint);
        }
    }
}
```

`PaintSurface` 处理程序两次调用 `Translate` 方法（首先转换为画布的中心），然后转换为围绕（0，0）的圆的周长。 圆的半径设置为保持页面的范围内的星号的同时尽可能大：

[![](translate-images/hendecagramanimation-small.png "Triple screenshot of the Hendecagram Animation page")](translate-images/hendecagramanimation-large.png#lightbox "Triple screenshot of the Hendecagram Animation page")

请注意，在星型保持围绕页的中心为中心的相同方向。 它根本不会旋转。 这是一个作业用于旋转转换。

## <a name="related-links"></a>相关链接

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
