---
title: 翻译转换
description: 本文探讨如何使用转换变换移动 SkiaSharp 图形在 Xamarin.Forms 应用程序，并演示此示例代码。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: BD28ADA1-49F9-44E2-A548-46024A29882F
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2017
ms.openlocfilehash: a51a441aeacf265093b82ddb65237887b0a30719
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61382405"
---
# <a name="the-translate-transform"></a>翻译转换

[![下载示例](~/media/shared/download.png)下载示例](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)

_了解如何使用转换变换移动 SkiaSharp 图形_

最简单中 SkiaSharp 转换的类型是*翻译*或*翻译*转换。 此转换将在水平和垂直方向上的图形对象。 在某种意义上，转换是最不必要的转换，因为通常可以完成相同的效果，只需更改绘图函数中使用的坐标。 当呈现一个路径，但是，所有坐标都封装在路径中，因此很容易得多，在应用转换变换，若要切换的完整路径。

转换也是适用于动画以及简单的文本效果：

![](translate-images/translateexample.png "雕刻，和浮雕与翻译的文本阴影")

[ `Translate` ](xref:SkiaSharp.SKCanvas.Translate(System.Single,System.Single))中的方法`SKCanvas`具有导致后来绘制的图形对象水平和垂直移动的两个参数：

```csharp
public void Translate (Single dx, Single dy)
```

这些参数可为负数。 第二个[ `Translate` ](xref:SkiaSharp.SKCanvas.Translate(SkiaSharp.SKPoint))方法将在单个的这两种转换值合并`SKPoint`值：

```csharp
public void Translate (SKPoint point)
```

**累积翻译**页[ **SkiaSharpForms** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)示例程序演示了多个调用的`Translate`方法具有累积性。 [ `AccumulatedTranslatePage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/AccumulatedTranslatePage.cs)类显示相同的矩形的 20 个版本，每个偏移量从上一个矩形刚好足够这样它们将延长，沿对角线。 下面是`PaintSurface`事件处理程序：

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

[![](translate-images/accumulatedtranslate-small.png "三重累积转换页屏幕截图")](translate-images/accumulatedtranslate-large.png#lightbox "累积转换页面的三个屏幕截图")

如果累积的平移因数`dx`和`dy`，和绘图函数中指定的点是 (`x`， `y`)，然后在点呈现的图形对象 (`x'`， `y'`)，其中：

x = x + dx

y = y + dy

这些参数称为*转换公式*以进行翻译。 默认值`dx`并`dy`新`SKCanvas`均为 0。

通常使用转换变换的阴影效果和类似的技巧，作为**翻译文本效果**页说明。 下面是相关部分`PaintSurface`处理程序中的[ `TranslateTextEffectsPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/TranslateTextEffectsPage.cs)类：

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

中的三个示例中，每个`Translate`称为用于显示要从指定的位置偏移的文本`x`和`y`变量。 然后文本会再次显示，在使用任何转换效果的另一种颜色：

[![](translate-images/translatetexteffects-small.png "三重的翻译文本效果页屏幕截图")](translate-images/translatetexteffects-large.png#lightbox "带来三倍的翻译文本效果页屏幕截图")

每三个示例显示了不同的方式而取消`Translate`调用：

第一个示例只需调用`Translate`同样但具有负值。 因为`Translate`调用具有累积性，这一序列的调用只需将总翻译还原为默认值为零。

第二个示例中调用[ `ResetMatrix` ](xref:SkiaSharp.SKCanvas.ResetMatrix)。 这会导致要返回到其默认状态的所有转换。

第三个示例将保存的状态`SKCanvas`对象通过调用[ `Save` ](xref:SkiaSharp.SKCanvas.Save) ，然后还原通过调用状态[ `Restore` ](xref:SkiaSharp.SKCanvas.Restore)。 这是最通用的方法来处理一系列的绘制操作的转换。 这些`Save`和`Restore`调用堆栈等函数：您可以调用`Save`多个次，，然后调用`Restore`在反向序列，以返回到之前的状态。 `Save`方法返回一个整数，并可以将传递到该整数[ `RestoreToCount` ](xref:SkiaSharp.SKCanvas.RestoreToCount*)若要有效地调用`Restore`多次。 [ `SaveCount` ](xref:SkiaSharp.SKCanvas.SaveCount)属性返回当前堆栈上保存的状态数。

此外可以使用[ `SKAutoCanvasRestore` ](xref:SkiaSharp.SKAutoCanvasRestore)还原画布状态的类。 此类的构造函数应调用`using`语句; 画布结束时自动还原状态`using`块。 

但是，您无需担心如何从一次调用的同时应用转换`PaintSurface`到下一个处理程序。 每次新调用`PaintSurface`提供了全新`SKCanvas`使用默认转换的对象。

另一个常见用途的`Translate`转换是用于呈现的视觉对象最初创建使用方便进行绘制的坐标。 例如，你可能想要使用中心点 （0，0） 处指定了模拟时钟的坐标。 你可以然后使用转换来显示时钟所需的位置。 此方法进行了演示 [**Hendecagram 数组**] 页。 [ `HendecagramArrayPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/HendecagramPage.cs)类首先创建`SKPath`11 星的对象。 `HendecagramPath`对象定义为公共、 静态和只读的因此，则可以从其他演示程序进行访问。 在静态构造函数创建：

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

如果在星型的中心点 （0，0），在星型的所有点都都在该点周围的圆圈。 每个点都增加 5/11ths 的 360 度的角度的正弦和余弦值的组合。 (也是可以通过增加 2 角度创建 11 星/第 11，3/11ths 或 4/圆的第 11。)该圆的半径设置为 100。

如果此路径呈现不涉及任何转换，在中心将定位在的左上角`SKCanvas`，并且仅每个季度的它将可见。 `PaintSurface`处理程序`HendecagramPage`改为使用`Translate`平铺在画布上添加星形的多个副本，每个随机着色：

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

[![](translate-images/hendecagramarray-small.png "三重 Hendecagram 数组页屏幕截图")](translate-images/hendecagramarray-large.png#lightbox "Hendecagram 数组页面的三个屏幕截图")

动画通常涉及到转换。 **Hendecagram 动画**页移动 11 星的圆圈。 [ `HendecagramAnimationPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/HendecagramAnimationPage.cs)类首先执行某些字段，然后重写`OnAppearing`和`OnDisappearing`方法来启动和停止 Xamarin.Forms 计时器：

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

`angle`字段以动画形式从 0 度到 360 度每隔 5 秒。 `PaintSurface`处理程序使用`angle`两种方式的属性： 指定中的颜色的色调`SKColor.FromHsl`方法，并作为参数`Math.Sin`和`Math.Cos`方法来控制在星型的位置：

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

`PaintSurface`处理程序调用`Translate`方法两次，首先要转换为中心的画布，然后再转换为以中心圆的周长 （0，0）。 圆的半径设置为保持页面的范围内的星号的同时尽可能大：

[![](translate-images/hendecagramanimation-small.png "三重 Hendecagram 动画页屏幕截图")](translate-images/hendecagramanimation-large.png#lightbox "Hendecagram 动画页面的三个屏幕截图")

请注意，在星型保持围绕页的中心为中心的相同方向。 它根本不会旋转。 这是一个作业用于旋转转换。


## <a name="related-links"></a>相关链接

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （示例）](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
