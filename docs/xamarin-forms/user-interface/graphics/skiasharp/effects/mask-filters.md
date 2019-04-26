---
title: SkiaSharp 掩码筛选器
description: 了解如何使用掩码筛选器创建的模糊及其他 alpha 的效果。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 940422A1-8BC0-4039-8AD7-26C61320F858
author: davidbritch
ms.author: dabritch
ms.date: 08/27/2018
ms.openlocfilehash: d68153cdaad67b407def3ed5bfaddf928ef98bb4
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61289088"
---
# <a name="skiasharp-mask-filters"></a>SkiaSharp 掩码筛选器

[![下载示例](~/media/shared/download.png)下载示例](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)

掩码筛选器是操作几何图形和 alpha 通道的图形对象的效果。 若要使用掩码筛选器，设置[ `MaskFilter` ](xref:SkiaSharp.SKPaint.MaskFilter)的属性`SKPaint`对象的类型[ `SKMaskFilter` ](xref:SkiaSharp.SKMaskFilter)已通过调用之一创建了`SKMaskFilter`静态方法。

熟悉掩码筛选器的最佳方法是通过使用这些静态方法。 最有用的掩码筛选器创建模糊：

![使示例变得模糊](mask-filters-images/MaskFilterExample.png "模糊示例")

这是本文中所述的唯一掩码筛选器功能。 在下一篇文章[ **SkiaSharp 映像筛选器**](image-filters.md)还介绍了你可能更倾向于此的模糊效果。 

静态[ `SKMaskFilter.CreateBlur` ](xref:SkiaSharp.SKMaskFilter.CreateBlur(SkiaSharp.SKBlurStyle,System.Single))方法具有以下语法：

```csharp
public static SKMaskFilter CreateBlur (SKBlurStyle blurStyle, float sigma);
```

重载允许指定用来创建模糊和一个矩形，以避免模糊将与其他图形对象所覆盖的区域中的算法的标志。

[`SKBlurStyle`](xref:SkiaSharp.SKBlurStyle) 是一个枚举，包含下列成员：

- `Normal`
- `Solid`
- `Outer`
- `Inner`

以下示例中显示了这些样式的效果。 `sigma`参数指定的模糊程度。 在较旧版本的 Skia，radius 值指出了模糊程度。 如果为应用程序适合的半径值，则静态[ `SKMaskFilter.ConvertRadiusToSigma` ](xref:SkiaSharp.SKMaskFilter.ConvertRadiusToSigma*)可以从另一个转换的方法。 该方法乘以 0.57735 半径，并将 0.5。

**掩码模糊试验**页面[ **SkiaSharpFormsDemos** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)示例允许用户体验与模糊样式和 sigma 的值。 XAML 文件实例化`Picker`具有四个`SKBlurStyle`枚举成员和一个`Slider`函数指定 sigma 值：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp;assembly=SkiaSharp"
             xmlns:skiaforms="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Effects.MaskBlurExperimentPage"
             Title="Mask Blur Experiment">
    
    <StackLayout>
        <skiaforms:SKCanvasView x:Name="canvasView"
                                VerticalOptions="FillAndExpand"
                                PaintSurface="OnCanvasViewPaintSurface" />

        <Picker x:Name="blurStylePicker" 
                Title="Filter Blur Style" 
                Margin="10, 0"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.ItemsSource>
                <x:Array Type="{x:Type skia:SKBlurStyle}">
                    <x:Static Member="skia:SKBlurStyle.Normal" />
                    <x:Static Member="skia:SKBlurStyle.Solid" />
                    <x:Static Member="skia:SKBlurStyle.Outer" />
                    <x:Static Member="skia:SKBlurStyle.Inner" />
                </x:Array>
            </Picker.ItemsSource>

            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>

        <Slider x:Name="sigmaSlider"
                Maximum="10"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="{Binding Source={x:Reference sigmaSlider},
                              Path=Value,
                              StringFormat='Sigma = {0:F1}'}"
               HorizontalTextAlignment="Center" />
    </StackLayout>
</ContentPage>
```

代码隐藏文件使用这些值以创建`SKMaskFilter`对象，并将其设置为`MaskFilter`属性的`SKPaint`对象。 这`SKPaint`对象用于绘制文本字符串和位图：

```csharp
public partial class MaskBlurExperimentPage : ContentPage
{
    const string TEXT = "Blur My Text";

    SKBitmap bitmap = BitmapExtensions.LoadBitmapResource(
                            typeof(MaskBlurExperimentPage), 
                            "SkiaSharpFormsDemos.Media.SeatedMonkey.jpg");

    public MaskBlurExperimentPage ()
    {
        InitializeComponent ();
    }

    void OnPickerSelectedIndexChanged(object sender, EventArgs args)
    {
        canvasView.InvalidateSurface();
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

        canvas.Clear(SKColors.Pink);

        // Get values from XAML controls
        SKBlurStyle blurStyle =
            (SKBlurStyle)(blurStylePicker.SelectedIndex == -1 ?
                                        0 : blurStylePicker.SelectedItem);

        float sigma = (float)sigmaSlider.Value;

        using (SKPaint paint = new SKPaint())
        {
            // Set SKPaint properties
            paint.TextSize = (info.Width - 100) / (TEXT.Length / 2);
            paint.MaskFilter = SKMaskFilter.CreateBlur(blurStyle, sigma);

            // Get text bounds and calculate display rectangle
            SKRect textBounds = new SKRect();
            paint.MeasureText(TEXT, ref textBounds);
            SKRect textRect = new SKRect(0, 0, info.Width, textBounds.Height + 50);

            // Center the text in the display rectangle
            float xText = textRect.Width / 2 - textBounds.MidX;
            float yText = textRect.Height / 2 - textBounds.MidY;

            canvas.DrawText(TEXT, xText, yText, paint);

            // Calculate rectangle for bitmap
            SKRect bitmapRect = new SKRect(0, textRect.Bottom, info.Width, info.Height);
            bitmapRect.Inflate(-50, -50);

            canvas.DrawBitmap(bitmap, bitmapRect, BitmapStretch.Uniform, paint: paint);
        }
    }
}
```

下面是在 iOS、 Android 和与通用 Windows 平台 (UWP) 上运行的程序`Normal`模糊样式和增加`sigma`级别：

[![屏蔽模糊试验-Normal](mask-filters-images/MaskBlurExperiment-Normal.png "掩码模糊处理实验-正常")](mask-filters-images/MaskBlurExperiment-Normal-Large.png#lightbox)

您会注意到，位图边缘受变得模糊。 `SKMaskFilter`类不是要使用如果你想要使整个位图图像模糊的正确效果。 为此，你将想要使用[ `SKImageFilter` ](xref:SkiaSharp.SKImageFilter)类，如在下一篇文章中所述[ **SkiaSharp 映像筛选器**](image-filters.md)。

为增加方向的更模糊文本`sigma`参数。 在与此程序的试验，你会注意到，为特定`sigma`值，是 Windows 10 桌面版上更极端变得模糊。 这种差异是因为像素密度较低比移动设备上的桌面监视器上，并且因此较低的文本框高度以像素为单位。 `sigma`值成正比在模糊程度上，以像素为单位，因此，对于给定`sigma`值的效果是更极端上较低分辨率显示。 在生产应用程序中，您可能需要计算`sigma`是图形的大小成正比的值。 

在你的应用程序将查找最佳的模糊级别确定之前尝试几个值。 例如，在**掩码模糊实验**页上，请尝试设置`sigma`如下所示：

```csharp
sigma = paint.TextSize / 18;
paint.MaskFilter = SKMaskFilter.CreateBlur(blurStyle, sigma);
```

现在`Slider`不起作用，但在平台之间保持一致的模糊程度：

[![屏蔽模糊试验-一致](mask-filters-images/MaskBlurExperiment-Consistent.png "屏蔽模糊试验-一致")](mask-filters-images/MaskBlurExperiment-Consistent-Large.png#lightbox)

所有屏幕截图到目前为止显示了使用创建的模糊`SKBlurStyle.Normal`枚举成员。 以下屏幕截图显示的效果`Solid`， `Outer`，和`Inner`模糊样式：

[![屏蔽模糊实验](mask-filters-images/MaskBlurExperiment.png "屏蔽模糊实验")](mask-filters-images/MaskBlurExperiment-Large.png#lightbox)

IOS 屏幕截图显示了`Solid`样式：文本字符都是作为纯黑色的笔画，仍然存在，模糊添加到这些文本字符的外部。 

Android 屏幕截图中所示中间`Outer`样式：字符笔画本身将消除 （如位图） 和模糊环绕的文本字符一次出现的空白区域。 

UWP 上正确显示的屏幕截图`Inner`样式。 模糊被限制为文本字符通常占用的区域。

[ **SkiaSharp 线性渐变**](shaders/linear-gradient.md#transparency-and-gradients)一文所述**反射渐变**使用线性渐变和一个转换来模拟的文本字符串的反射程序：

[![反射渐变](shaders/linear-gradient-images/ReflectionGradient.png "反射渐变")](shaders/linear-gradient-images/ReflectionGradient-Large.png#lightbox)

**模糊反射**页会将一条语句添加到该代码：

```csharp
public class BlurryReflectionPage : ContentPage
{
    const string TEXT = "Reflection";

    public BlurryReflectionPage()
    {
        Title = "Blurry Reflection";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPaint paint = new SKPaint())
        {
            // Set text color to blue
            paint.Color = SKColors.Blue;

            // Set text size to fill 90% of width
            paint.TextSize = 100;
            float width = paint.MeasureText(TEXT);
            float scale = 0.9f * info.Width / width;
            paint.TextSize *= scale;

            // Get text bounds
            SKRect textBounds = new SKRect();
            paint.MeasureText(TEXT, ref textBounds);

            // Calculate offsets to position text above center
            float xText = info.Width / 2 - textBounds.MidX;
            float yText = info.Height / 2;

            // Draw unreflected text
            canvas.DrawText(TEXT, xText, yText, paint);

            // Shift textBounds to match displayed text
            textBounds.Offset(xText, yText);

            // Use those offsets to create a gradient for the reflected text
            paint.Shader = SKShader.CreateLinearGradient(
                                new SKPoint(0, textBounds.Top),
                                new SKPoint(0, textBounds.Bottom),
                                new SKColor[] { paint.Color.WithAlpha(0),
                                                paint.Color.WithAlpha(0x80) },
                                null,
                                SKShaderTileMode.Clamp);

            // Create a blur mask filter
            paint.MaskFilter = SKMaskFilter.CreateBlur(SKBlurStyle.Normal, paint.TextSize / 36);

            // Scale the canvas to flip upside-down around the vertical center
            canvas.Scale(1, -1, 0, yText);

            // Draw reflected text
            canvas.DrawText(TEXT, xText, yText, paint);
        }
    }
}
```

新的语句将添加文本大小为基础的反射文本的模糊筛选的器：

```csharp
paint.MaskFilter = SKMaskFilter.CreateBlur(SKBlurStyle.Normal, paint.TextSize / 36);
```

此模糊筛选器会导致看起来更实际的反射：

[![模糊反射](mask-filters-images/BlurryReflection.png "模糊的反射")](mask-filters-images/BlurryReflection-Large.png#lightbox)

## <a name="related-links"></a>相关链接

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （示例）](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
