---
title: SkiaSharp 透明度
description: 使用透明度来组合在一个场景中的多个对象。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: B62F9487-C30E-4C63-BAB1-4C091FF50378
author: davidbritch
ms.author: dabritch
ms.date: 08/23/2018
ms.openlocfilehash: 74335de66e74f6adc7c9488a1b78c31d36d03f14
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70759408"
---
# <a name="skiasharp-transparency"></a>SkiaSharp 透明度

[![下载示例](~/media/shared/download.png)下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

如您所见[ `SKPaint` ](xref:SkiaSharp.SKPaint)类包括[ `Color` ](xref:SkiaSharp.SKPaint.Color)类型的属性[ `SKColor` ](xref:SkiaSharp.SKColor)。 `SKColor` 包括 alpha 通道，因此，任何内容与着色`SKColor`值可以是部分透明。 

中演示一些透明度[ **SkiaSharp 中的基本动画**](animation.md)一文。 本文将某种程度上更深入地探讨组合在一个场景，有时称为一种技术中的多个对象的透明度_混合_。 更高级的混合技术的文章中讨论[ **SkiaSharp 着色器**](../effects/shaders/index.md)部分。

首次创建使用四个参数的颜色时，可以设置的透明度级别[ `SKColor` ](xref:SkiaSharp.SKColor.%23ctor(System.Byte,System.Byte,System.Byte,System.Byte))构造函数：

```csharp
SKColor (byte red, byte green, byte blue, byte alpha);
```

Alpha 值为 0 是完全透明，完全不透明 alpha 值为 0xFF。 两个极端之间的值来创建部分透明的颜色。

此外，`SKColor`定义方便[ `WithAlpha` ](xref:SkiaSharp.SKColor.WithAlpha*)从现有的颜色，但使用指定的 alpha 级别创建新颜色的方法：

```csharp
SKColor halfTransparentBlue = SKColors.Blue.WithAlpha(0x80);
```

使用部分透明的文本进行了演示**代码更多代码**页面[ **SkiaSharpFormsDemos** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)示例。 此页淡两个文本字符串和缩小通过合并中的透明度`SKColor`值：

```csharp
public class CodeMoreCodePage : ContentPage
{
    SKCanvasView canvasView;
    bool isAnimating;
    Stopwatch stopwatch = new Stopwatch();
    double transparency;

    public CodeMoreCodePage ()
    {
        Title = "Code More Code";

        canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }

    protected override void OnAppearing()
    {
        base.OnAppearing();

        isAnimating = true;
        stopwatch.Start();
        Device.StartTimer(TimeSpan.FromMilliseconds(16), OnTimerTick);
    }

    protected override void OnDisappearing()
    {
        base.OnDisappearing();

        stopwatch.Stop();
        isAnimating = false;
    }

    bool OnTimerTick()
    {
        const int duration = 5;     // seconds
        double progress = stopwatch.Elapsed.TotalSeconds % duration / duration;
        transparency = 0.5 * (1 + Math.Sin(progress * 2 * Math.PI));
        canvasView.InvalidateSurface();

        return isAnimating;
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        const string TEXT1 = "CODE";
        const string TEXT2 = "MORE";

        using (SKPaint paint = new SKPaint())
        {
            // Set text width to fit in width of canvas
            paint.TextSize = 100;
            float textWidth = paint.MeasureText(TEXT1);
            paint.TextSize *= 0.9f * info.Width / textWidth;

            // Center first text string
            SKRect textBounds = new SKRect();
            paint.MeasureText(TEXT1, ref textBounds);

            float xText = info.Width / 2 - textBounds.MidX;
            float yText = info.Height / 2 - textBounds.MidY;

            paint.Color = SKColors.Blue.WithAlpha((byte)(0xFF * (1 - transparency)));
            canvas.DrawText(TEXT1, xText, yText, paint);

            // Center second text string
            textBounds = new SKRect();
            paint.MeasureText(TEXT2, ref textBounds);

            xText = info.Width / 2 - textBounds.MidX;
            yText = info.Height / 2 - textBounds.MidY;

            paint.Color = SKColors.Blue.WithAlpha((byte)(0xFF * transparency));
            canvas.DrawText(TEXT2, xText, yText, paint);
        }
    }
}
```

`transparency`字段进行动画处理，以在 0 到 1 之间变化并切换回正弦节奏中。 第一个文本字符串显示其 alpha 值中减去计算得出`transparency`取值范围为 1:

```csharp
paint.Color = SKColors.Blue.WithAlpha((byte)(0xFF * (1 - transparency)));
```

[ `WithAlpha` ](xref:SkiaSharp.SKColor.WithAlpha*)方法的现有颜色，下面是上设置的 alpha 分量`SKColors.Blue`。 第二个文本字符串使用计算出的 alpha 值`transparency`值本身：

```csharp
paint.Color = SKColors.Blue.WithAlpha((byte)(0xFF * transparency));
```

两个字，敦促用户"代码的详细信息"（或可能请求"更多代码"） 之间交替动画：

[![代码更多的代码](transparency-images/CodeMoreCode.png "代码更多的代码")](transparency-images/CodeMoreCode-Large.png#lightbox)

在上一篇文章中上[ **SkiaSharp 中的位图基础知识**](bitmaps.md)，了解了如何显示位图使用之一[ `DrawBitmap` ](xref:SkiaSharp.SKCanvas.DrawBitmap*)方法的`SKCanvas`。 所有`DrawBitmap`方法包括`SKPaint`对象作为最后一个参数。 默认情况下，此参数设置为`null`，可以忽略它。 

或者，可以设置`Color`属性的`SKPaint`对象来显示有一定的透明度的位图。 设置中的透明度级别`Color`属性的`SKPaint`允许您以淡入淡出中，位图或分解到另一个位图。 

中演示位图透明度**位图消失**页。 XAML 文件实例化`SKCanvasView`和一个`Slider`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Effects.BitmapDissolvePage"
             Title="Bitmap Dissolve">
    <StackLayout>
        <skia:SKCanvasView x:Name="canvasView"
                           VerticalOptions="FillAndExpand"
                           PaintSurface="OnCanvasViewPaintSurface" />

        <Slider x:Name="progressSlider"
                Margin="10"
                ValueChanged="OnSliderValueChanged" />
    </StackLayout>
</ContentPage>
```

代码隐藏文件加载两个位图资源。 这些位图不是大小相同，但它们是相同的纵横比：

```csharp
public partial class BitmapDissolvePage : ContentPage
{
    SKBitmap bitmap1;
    SKBitmap bitmap2;

    public BitmapDissolvePage()
    {
        InitializeComponent();

        // Load two bitmaps
        Assembly assembly = GetType().GetTypeInfo().Assembly;

        using (Stream stream = assembly.GetManifestResourceStream(
                                "SkiaSharpFormsDemos.Media.SeatedMonkey.jpg"))
        {
            bitmap1 = SKBitmap.Decode(stream);
        }
        using (Stream stream = assembly.GetManifestResourceStream(
                                "SkiaSharpFormsDemos.Media.FacePalm.jpg"))
        {
            bitmap2 = SKBitmap.Decode(stream);
        }
    }

    void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
    {
        canvasView.InvalidateSurface();
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Find rectangle to fit bitmap
        float scale = Math.Min((float)info.Width / bitmap1.Width,
                                (float)info.Height / bitmap1.Height);
        SKRect rect = SKRect.Create(scale * bitmap1.Width,
                                    scale * bitmap1.Height);
        float x = (info.Width - rect.Width) / 2;
        float y = (info.Height - rect.Height) / 2;
        rect.Offset(x, y);

        // Get progress value from Slider
        float progress = (float)progressSlider.Value;

        // Display two bitmaps with transparency
        using (SKPaint paint = new SKPaint())
        {
            paint.Color = paint.Color.WithAlpha((byte)(0xFF * (1 - progress)));
            canvas.DrawBitmap(bitmap1, rect, paint);

            paint.Color = paint.Color.WithAlpha((byte)(0xFF * progress));
            canvas.DrawBitmap(bitmap2, rect, paint);
        }
    }
}
```

`Color`属性的`SKPaint`对象设置为两个位图的两个互补 alpha 级别。 使用时`SKPaint`与位图，它并不重要的其余`Color`值。 最重要的就是 alpha 通道。 下面的代码只是调用`WithAlpha`上的默认值的方法`Color`属性。

移动`Slider`之间一个位图，另一个溶解以：

[![位图消隐](transparency-images/BitmapDissolve.png "位图消隐")](transparency-images/BitmapDissolve-Large.png#lightbox)

在过去的几个文章中，已了解如何使用 SkiaSharp 绘制文本、 圆、 椭圆、 圆角的矩形和位图。 下一步是[SkiaSharp 线和路径](../paths/index.md)在其中将学习如何相互连接的直线绘制图形路径中。

## <a name="related-links"></a>相关链接

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
