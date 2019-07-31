---
title: SkiaSharp 干扰和组合
description: 生成 Perlin 噪音着色器，并结合其他着色器。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 90C2D00A-2876-43EA-A836-538C3318CF93
author: davidbritch
ms.author: dabritch
ms.date: 08/23/2018
ms.openlocfilehash: dea7f5e51a864922d56f7b65d19b21a889cbc650
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2019
ms.locfileid: "68656155"
---
# <a name="skiasharp-noise-and-composing"></a>SkiaSharp 干扰和组合

[![下载示例](~/media/shared/download.png)下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

简单的向量图形可能会看起来不自然。 直线、 平滑曲线和纯色不类似于实际对象的不理想的部分。 1982 电影的计算机生成的图形处理时_Tron_，计算机科学家 Ken Perlin 开始开发使用随机进程提供这些映像更现实可行的纹理的算法。 于 1997 年，Ken Perlin 赢得技术成就的学院奖励。 他的工作就被称为 Perlin 噪音，和它在 SkiaSharp 中受支持。 以下是一个示例：

![Perlin 噪音示例](noise-images/NoiseSample.png "Perlin 噪音示例")

正如您所看到的每个像素不是随机的颜色值。 从像素到像素的连续性导致随机形状。

在 Skia Perlin 噪音的支持基于 W3C 规范适用于 CSS 和 SVG。 部分 8.20 [**筛选器效果模块级别 1** ](http://www.w3.org/TR/filter-effects-1/#feTurbulenceElement) C 代码中包含的基础的 Perlin 噪音算法。

## <a name="exploring-perlin-noise"></a>探索 Perlin 噪音

[ `SKShader` ](xref:SkiaSharp.SKShader)类定义了两个不同的静态方法，用于生成 Perlin 噪音： [ `CreatePerlinNoiseFractalNoise` ](xref:SkiaSharp.SKShader.CreatePerlinNoiseFractalNoise*)并[ `CreatePerlinNoiseTurbulence` ](xref:SkiaSharp.SKShader.CreatePerlinNoiseTurbulence*)。 参数是相同的：

```csharp
public static SkiaSharp CreatePerlinNoiseFractalNoise (float baseFrequencyX, float baseFrequencyY, int numOctaves, float seed);

public static SkiaSharp.SKShader CreatePerlinNoiseTurbulence (float baseFrequencyX, float baseFrequencyY, int numOctaves, float seed);
```

在具有附加重载版本中也存在这两种方法`SKPointI`参数。 部分[**平铺 Perlin 噪音**](#tiling-perlin-noise)讨论这些重载。

这两个`baseFrequency`参数都为正值范围从 0 到 1，在 SkiaSharp 文档中定义，但它们可以设置为较高的值。 值越大，随机图像在水平和垂直方向上变化就越大。

`numOctaves`值是一个整数为 1 或更高版本。 它与算法中的一个迭代因素。 每个其他八度音可发挥作用的效果，以便使用较高八度音值会影响是上一个八度音的一半。

`seed`参数是随机数字生成器的起始点。 指定为浮点值，尽管部分被截断之前使用它，且 0 与 1 相同。

**Perlin 噪音**页面[ **SkiaSharpFormsDemos**)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)示例允许您体验各种值`baseFrequency`和`numOctaves`参数。 下面是 XAML 文件：

```xaml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Effects.PerlinNoisePage"
             Title="Perlin Noise">

    <StackLayout>
        <skia:SKCanvasView x:Name="canvasView"
                           VerticalOptions="FillAndExpand"
                           PaintSurface="OnCanvasViewPaintSurface" />

        <Slider x:Name="baseFrequencyXSlider"
                Maximum="4"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <Label x:Name="baseFrequencyXText"
               HorizontalTextAlignment="Center" />

        <Slider x:Name="baseFrequencyYSlider"
                Maximum="4"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <Label x:Name="baseFrequencyYText"
               HorizontalTextAlignment="Center" />

        <StackLayout Orientation="Horizontal"
                     HorizontalOptions="Center"
                     Margin="10">

            <Label Text="{Binding Source={x:Reference octavesStepper},
                                  Path=Value,
                                  StringFormat='Number of Octaves: {0:F0}'}"
                   VerticalOptions="Center" />

            <Stepper x:Name="octavesStepper"
                     Minimum="1"
                     ValueChanged="OnStepperValueChanged" />
        </StackLayout>
    </StackLayout>
</ContentPage>
```

它使用两个`Slider`的两个视图`baseFrequency`参数。 若要扩展的更小的值范围，则对数滑块。 代码隐藏文件计算的参数`SKShader`方法的幂`Slider`值。 `Label`视图显示计算的值：

```csharp
float baseFreqX = (float)Math.Pow(10, baseFrequencyXSlider.Value - 4);
baseFrequencyXText.Text = String.Format("Base Frequency X = {0:F4}", baseFreqX);

float baseFreqY = (float)Math.Pow(10, baseFrequencyYSlider.Value - 4);
baseFrequencyYText.Text = String.Format("Base Frequency Y = {0:F4}", baseFreqY);
```

一个`Slider`为 1 的值为 0.001，对应`Slider`os 2 值对应于 0.01， `Slider` 3 的值对应于 0.1，和一个`Slider`对应于 1 的值为 4。

下面是包含该代码的代码隐藏文件：

```csharp
public partial class PerlinNoisePage : ContentPage
{
    public PerlinNoisePage()
    {
        InitializeComponent();
    }

    void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
    {
        canvasView.InvalidateSurface();
    }

    void OnStepperValueChanged(object sender, ValueChangedEventArgs args)
    {
        canvasView.InvalidateSurface();
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Get values from sliders and stepper
        float baseFreqX = (float)Math.Pow(10, baseFrequencyXSlider.Value - 4);
        baseFrequencyXText.Text = String.Format("Base Frequency X = {0:F4}", baseFreqX);

        float baseFreqY = (float)Math.Pow(10, baseFrequencyYSlider.Value - 4);
        baseFrequencyYText.Text = String.Format("Base Frequency Y = {0:F4}", baseFreqY);

        int numOctaves = (int)octavesStepper.Value;

        using (SKPaint paint = new SKPaint())
        {
            paint.Shader =
                SKShader.CreatePerlinNoiseFractalNoise(baseFreqX,
                                                       baseFreqY,
                                                       numOctaves,
                                                       0);

            SKRect rect = new SKRect(0, 0, info.Width, info.Height / 2);
            canvas.DrawRect(rect, paint);

            paint.Shader =
                SKShader.CreatePerlinNoiseTurbulence(baseFreqX,
                                                     baseFreqY,
                                                     numOctaves,
                                                     0);

            rect = new SKRect(0, info.Height / 2, info.Width, info.Height);
            canvas.DrawRect(rect, paint);
        }
    }
}
```

下面是在 iOS、 Android 和通用 Windows 平台 (UWP) 上运行的设备的程序。 在画布的上半部分显示不规则图形干扰。 动荡干扰是在底部一半：

[![Perlin 噪音](noise-images/PerlinNoise.png "Perlin 噪音")](noise-images/PerlinNoise-Large.png#lightbox)

相同的参数始终生成相同的模式，在窗口左上角开始。 调整宽度和高度 UWP 窗口时，这种一致性是很明显。 Windows 10 重绘屏幕，如画布的上半部分中的模式保持不变。

干扰模式集成了各种程度的透明度。 透明度变得明显，如果在中设置一种颜色`canvas.Clear()`调用。 该颜色将成为重要模式中。 您还会看到此效果的节[**组合多个着色器**](#combining-multiple-shaders)。

这些 Perlin 噪音模式很少单独使用。 通常这些受制进行混合模式和在更高版本的文章中讨论的颜色筛选器。

## <a name="tiling-perlin-noise"></a>平铺 Perlin 噪音

两个静态`SKShader`重载版本中还存在创建 Perlin 噪音的方法。 [ `CreatePerlinNoiseFractalNoise` ](xref:SkiaSharp.SKShader.CreatePerlinNoiseFractalNoise(System.Single,System.Single,System.Int32,System.Single,SkiaSharp.SKPointI))并[ `CreatePerlinNoiseTurbulence` ](xref:SkiaSharp.SKShader.CreatePerlinNoiseFractalNoise(System.Single,System.Single,System.Int32,System.Single,SkiaSharp.SKPointI))重载有一个额外`SKPointI`参数：

```csharp
public static SKShader CreatePerlinNoiseFractalNoise (float baseFrequencyX, float baseFrequencyY, int numOctaves, float seed, SKPointI tileSize);

public static SKShader CreatePerlinNoiseTurbulence (float baseFrequencyX, float baseFrequencyY, int numOctaves, float seed, SKPointI tileSize);
```

[ `SKPointI` ](xref:SkiaSharp.SKPointI)结构是熟悉的整数版本[ `SKPoint` ](xref:SkiaSharp.SKPoint)结构。 `SKPointI` 定义`X`并`Y`类型的属性`int`而非`float`。

这些方法创建指定大小的一个重复图案。 每个磁贴中的右边缘的左边缘相同上, 边缘是相同的下边缘。 这一特性进行了演示**平铺 Perlin 噪音**页。 XAML 文件类似于上面示例中，但它仅有`Stepper`用于更改视图`seed`参数：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Effects.TiledPerlinNoisePage"
             Title="Tiled Perlin Noise">

    <StackLayout>
        <skia:SKCanvasView x:Name="canvasView"
                           VerticalOptions="FillAndExpand"
                           PaintSurface="OnCanvasViewPaintSurface" />

        <StackLayout Orientation="Horizontal"
                     HorizontalOptions="Center"
                     Margin="10">

            <Label Text="{Binding Source={x:Reference seedStepper},
                                  Path=Value,
                                  StringFormat='Seed: {0:F0}'}"
                   VerticalOptions="Center" />

            <Stepper x:Name="seedStepper"
                     Minimum="1"
                     ValueChanged="OnStepperValueChanged" />

        </StackLayout>
    </StackLayout>
</ContentPage>
```

代码隐藏文件定义的磁贴大小的常量。 `PaintSurface`处理程序将创建该大小的位图和`SKCanvas`进行绘制到该位图。 `SKShader.CreatePerlinNoiseTurbulence`方法与该磁贴大小创建着色器。 在此位图上绘制此着色器：

```csharp
public partial class TiledPerlinNoisePage : ContentPage
{
    const int TILE_SIZE = 200;

    public TiledPerlinNoisePage()
    {
        InitializeComponent();
    }

    void OnStepperValueChanged(object sender, ValueChangedEventArgs args)
    {
        canvasView.InvalidateSurface();
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Get seed value from stepper
        float seed = (float)seedStepper.Value;

        SKRect tileRect = new SKRect(0, 0, TILE_SIZE, TILE_SIZE);

        using (SKBitmap bitmap = new SKBitmap(TILE_SIZE, TILE_SIZE))
        {
            using (SKCanvas bitmapCanvas = new SKCanvas(bitmap))
            {
                bitmapCanvas.Clear();

                // Draw tiled turbulence noise on bitmap
                using (SKPaint paint = new SKPaint())
                {
                    paint.Shader = SKShader.CreatePerlinNoiseTurbulence(
                                        0.02f, 0.02f, 1, seed,
                                        new SKPointI(TILE_SIZE, TILE_SIZE));

                    bitmapCanvas.DrawRect(tileRect, paint);
                }
            }

            // Draw tiled bitmap shader on canvas
            using (SKPaint paint = new SKPaint())
            {
                paint.Shader = SKShader.CreateBitmap(bitmap,
                                                     SKShaderTileMode.Repeat,
                                                     SKShaderTileMode.Repeat);
                canvas.DrawRect(info.Rect, paint);
            }

            // Draw rectangle showing tile
            using (SKPaint paint = new SKPaint())
            {
                paint.Style = SKPaintStyle.Stroke;
                paint.Color = SKColors.Black;
                paint.StrokeWidth = 2;

                canvas.DrawRect(tileRect, paint);
            }
        }
    }
}
```

位图创建，另一个后`SKPaint`对象用于通过调用创建平铺的位图模式`SKShader.CreateBitmap`。 请注意，两个参数为`SKShaderTileMode.Repeat`:

```csharp
paint.Shader = SKShader.CreateBitmap(bitmap,
                                     SKShaderTileMode.Repeat,
                                     SKShaderTileMode.Repeat);
```

此着色器用于涵盖画布。 最后，另一个`SKPaint`对象用于绘制矩形显示原始位图的大小。

仅`seed`参数是可选择从用户界面。 如果相同`seed`每个平台上使用模式，它们将显示相同的模式。 不同`seed`值会导致不同的模式：

[![平铺 Perlin 噪音](noise-images/TiledPerlinNoise.png "平铺 Perlin 噪音")](noise-images/TiledPerlinNoise-Large.png#lightbox)

在左上角的 200 像素正方形模式无缝地排列到其他磁贴中。

## <a name="combining-multiple-shaders"></a>组合多个着色器

`SKShader`类包括[ `CreateColor` ](xref:SkiaSharp.SKShader.CreateColor*)使用指定纯色创建着色器的方法。 此着色器不本身非常有用，因为只需将该颜色设置为`Color`的属性`SKPaint`对象，并将`Shader`属性设置为 null。

这`CreateColor`方法将会在另一种方法很有用的`SKShader`定义。 此方法是[ `CreateCompose` ](xref:SkiaSharp.SKShader.CreateCompose(SkiaSharp.SKShader,SkiaSharp.SKShader))，其中合并了两个着色器。 下面是语法：

```csharp
public static SKShader CreateCompose (SKShader dstShader, SKShader srcShader);
```

`srcShader` （源着色器） 有效地绘制的`dstShader`（目标着色器）。 如果源着色器是一种纯色或渐变不透明的情况下，将完全遮盖目标着色器。

Perlin 噪音着色器包含透明度。 如果该着色器是源，目标着色器将显示通过透明区域。

**组成 Perlin 噪音**页具有与第一个几乎完全相同的 XAML 文件**Perlin 噪音**页。 代码隐藏文件也是类似的。 但原始**Perlin 噪音**页上设置`Shader`的属性`SKPaint`到着色器返回的静态`CreatePerlinNoiseFractalNoise`和`CreatePerlinNoiseTurbulence`方法。 这**组成 Perlin 噪音**页上调用`CreateCompose`的组合着色器。 目标是创建使用纯色蓝色着色器`CreateColor`。 源是 Perlin 噪音着色器：

```csharp
public partial class ComposedPerlinNoisePage : ContentPage
{
    public ComposedPerlinNoisePage()
    {
        InitializeComponent();
    }

    void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
    {
        canvasView.InvalidateSurface();
    }

    void OnStepperValueChanged(object sender, ValueChangedEventArgs args)
    {
        canvasView.InvalidateSurface();
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Get values from sliders and stepper
        float baseFreqX = (float)Math.Pow(10, baseFrequencyXSlider.Value - 4);
        baseFrequencyXText.Text = String.Format("Base Frequency X = {0:F4}", baseFreqX);

        float baseFreqY = (float)Math.Pow(10, baseFrequencyYSlider.Value - 4);
        baseFrequencyYText.Text = String.Format("Base Frequency Y = {0:F4}", baseFreqY);

        int numOctaves = (int)octavesStepper.Value;

        using (SKPaint paint = new SKPaint())
        {
            paint.Shader = SKShader.CreateCompose(
                SKShader.CreateColor(SKColors.Blue),
                SKShader.CreatePerlinNoiseFractalNoise(baseFreqX,
                                                       baseFreqY,
                                                       numOctaves,
                                                       0));

            SKRect rect = new SKRect(0, 0, info.Width, info.Height / 2);
            canvas.DrawRect(rect, paint);

            paint.Shader = SKShader.CreateCompose(
                SKShader.CreateColor(SKColors.Blue),
                SKShader.CreatePerlinNoiseTurbulence(baseFreqX,
                                                     baseFreqY,
                                                     numOctaves,
                                                     0));

            rect = new SKRect(0, info.Height / 2, info.Width, info.Height);
            canvas.DrawRect(rect, paint);
        }
    }
}
```

不规则图形干扰着色器是在最前面;底部，动荡着色器是：

[![组成 Perlin 噪音](noise-images/ComposedPerlinNoise.png "组成 Perlin 噪音")](noise-images/ComposedPerlinNoise-Large.png#lightbox)

更多大蓝可以看到这些着色器是与前面的情况下显示**Perlin 噪音**页。 不同之处说明了干扰着色器中的透明度量。

此外，还有重载[ `CreateCompose` ](xref:SkiaSharp.SKShader.CreateCompose(SkiaSharp.SKShader,SkiaSharp.SKShader,SkiaSharp.SKBlendMode))方法：

```csharp
public static SKShader CreateCompose (SKShader dstShader, SKShader srcShader, SKBlendMode blendMode);
```

最后一个参数是的成员`SKBlendMode`枚举，包含在下一步的一系列文章中讨论的 29 成员的枚举[ **SkiaSharp 组合的情况下和混合模式**](../blend-modes/index.md)。

## <a name="related-links"></a>相关链接

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
