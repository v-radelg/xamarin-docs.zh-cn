---
title: SkiaSharp 映像筛选器
description: 了解如何使用映像筛选器来创建模糊和投影。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 173E7B22-AEC8-4F12-B657-1C0CEE01AD63
author: davidbritch
ms.author: dabritch
ms.date: 08/27/2018
ms.openlocfilehash: 10b61a7279964e8e1fb76a88be4f253e96b59908
ms.sourcegitcommit: 1e3a0d853669dcc57d5dee0894d325d40c7d8009
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2019
ms.locfileid: "70199196"
---
# <a name="skiasharp-image-filters"></a>SkiaSharp 映像筛选器

[![下载示例](~/media/shared/download.png)下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

映像筛选器是在所有构成图像的像素的颜色位操作的效果。 它们是掩码筛选器，仅对 alpha 通道，如本文所述的功能更多[ **SkiaSharp 掩码筛选器**](mask-filters.md)。 若要使用的映像筛选器，设置[ `ImageFilter` ](xref:SkiaSharp.SKPaint.ImageFilter)的属性`SKPaint`对象的类型[ `SKImageFilter` ](xref:SkiaSharp.SKImageFilter)已通过调用类的静态方法之一创建了。

熟悉掩码筛选器的最佳方法是通过使用这些静态方法。 可以使用掩码筛选器进行模糊处理整个位图：

![使示例变得模糊](image-filters-images/ImageFilterExample.png "模糊示例")

本文还演示如何使用映像筛选器创建放置阴影，并在浮雕和雕刻效果。

## <a name="blurring-vector-graphics-and-bitmaps"></a>模糊矢量图形和位图

[`SKImageFilter.CreateBlur`](xref:SkiaSharp.SKImageFilter.CreateBlur*)静态方法创建的模糊效果与[`SKMaskFilter`](xref:SkiaSharp.SKMaskFilter)类中的模糊方法相比具有明显的优势:图像筛选器可以对整个位图进行模糊。 该方法具有以下语法：

```csharp
public static SkiaSharp.SKImageFilter CreateBlur (float sigmaX, float sigmaY,
                                                  SKImageFilter input = null,
                                                  SKImageFilter.CropRect cropRect = null);
```

该方法具有两个 sigma 值&mdash;第一个针对在水平方向和垂直方向的第二个中的模糊程度。 可以通过指定为可选的第三个参数的另一个映像筛选器进行级联的映像筛选器。 此外可以指定裁剪矩形。

**图像模糊试验**页面[ **SkiaSharpFormsDemos** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)包含两个`Slider`视图，可设置不同级别的模糊试验：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Effects.ImageBlurExperimentPage"
             Title="Image Blur Experiment">

    <StackLayout>
        <skia:SKCanvasView x:Name="canvasView"
                           VerticalOptions="FillAndExpand"
                           PaintSurface="OnCanvasViewPaintSurface" />

        <Slider x:Name="sigmaXSlider"
                Maximum="10"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="{Binding Source={x:Reference sigmaXSlider},
                              Path=Value,
                              StringFormat='Sigma X = {0:F1}'}"
               HorizontalTextAlignment="Center" />

        <Slider x:Name="sigmaYSlider"
                Maximum="10"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="{Binding Source={x:Reference sigmaYSlider},
                              Path=Value,
                              StringFormat='Sigma Y = {0:F1}'}"
               HorizontalTextAlignment="Center" />
    </StackLayout>
</ContentPage>
```

代码隐藏文件使用两个`Slider`值调用`SKImageFilter.CreateBlur`为`SKPaint`对象，用于显示文本和位图：


```csharp
public partial class ImageBlurExperimentPage : ContentPage
{
    const string TEXT = "Blur My Text";

    SKBitmap bitmap = BitmapExtensions.LoadBitmapResource(
                            typeof(MaskBlurExperimentPage),
                            "SkiaSharpFormsDemos.Media.SeatedMonkey.jpg");

    public ImageBlurExperimentPage ()
    {
        InitializeComponent ();
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

        // Get values from sliders
        float sigmaX = (float)sigmaXSlider.Value;
        float sigmaY = (float)sigmaYSlider.Value;

        using (SKPaint paint = new SKPaint())
        {
            // Set SKPaint properties
            paint.TextSize = (info.Width - 100) / (TEXT.Length / 2);
            paint.ImageFilter = SKImageFilter.CreateBlur(sigmaX, sigmaY);

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

三个屏幕截图显示了各项设置`sigmaX`和`sigmaY`设置：

[![图像模糊实验](image-filters-images/ImageBlurExperiment.png "图像模糊实验")](image-filters-images/ImageBlurExperiment-Large.png#lightbox)

若要使在不同的显示大小和分辨率之间一致变得模糊，设置`sigmaX`和`sigmaY`为不变得模糊应用于图像的呈现的像素大小成正比的值。

## <a name="drop-shadow"></a>投影

[ `SKImageFilter.CreateDropShadow` ](xref:SkiaSharp.SKImageFilter.CreateDropShadow*)静态方法创建`SKImageFilter`投影的对象：

```csharp
public static SKImageFilter CreateDropShadow (float dx, float dy,
                                              float sigmaX, float sigmaY,
                                              SKColor color,
                                              SKDropShadowImageFilterShadowMode shadowMode,
                                              SKImageFilter input = null,
                                              SKImageFilter.CropRect cropRect = null);
```

此对象设置为`ImageFilter`属性的`SKPaint`对象，以及任何绘制并将该对象将具有其背后的投影。

`dx`和`dy`参数表示以像素为单位从图形对象的阴影的水平和垂直偏移量。 2D 图形中的约定是光源来自左上方，这意味着这些两个参数应为正数来定位下方和右侧的图形对象的阴影。

`sigmaX`和`sigmaY`参数越来越模糊投影的因素。

`color`参数是投影的颜色。 这`SKColor`值可以包括透明度。 一种可能性是颜色值`SKColors.Black.WithAlpha(0x80)`变暗任何颜色背景。

最后两个参数是可选的。

**删除卷影试验**程序的允许您使用的值进行试验`dx`， `dy`， `sigmaX`，并`sigmaY`以显示使用了投影的文本字符串。 XAML 文件实例化四个`Slider`视图来设置这些值：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Effects.DropShadowExperimentPage"
             Title="Drop Shadow Experiment">
    <ContentPage.Resources>
        <Style TargetType="Slider">
            <Setter Property="Margin" Value="10, 0" />
        </Style>

        <Style TargetType="Label">
            <Setter Property="HorizontalTextAlignment" Value="Center" />
        </Style>
    </ContentPage.Resources>

    <StackLayout>
        <skia:SKCanvasView x:Name="canvasView"
                           VerticalOptions="FillAndExpand"
                           PaintSurface="OnCanvasViewPaintSurface" />

        <Slider x:Name="dxSlider"
                Minimum="-20"
                Maximum="20"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="{Binding Source={x:Reference dxSlider},
                              Path=Value,
                              StringFormat='Horizontal offset = {0:F1}'}" />

        <Slider x:Name="dySlider"
                Minimum="-20"
                Maximum="20"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="{Binding Source={x:Reference dySlider},
                              Path=Value,
                              StringFormat='Vertical offset = {0:F1}'}" />

        <Slider x:Name="sigmaXSlider"
                Maximum="10"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="{Binding Source={x:Reference sigmaXSlider},
                              Path=Value,
                              StringFormat='Sigma X = {0:F1}'}" />

        <Slider x:Name="sigmaYSlider"
                Maximum="10"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="{Binding Source={x:Reference sigmaYSlider},
                              Path=Value,
                              StringFormat='Sigma Y = {0:F1}'}" />
    </StackLayout>
</ContentPage>
```

代码隐藏文件使用这些值上的蓝色文本字符串创建红色投影：

```csharp
public partial class DropShadowExperimentPage : ContentPage
{
    const string TEXT = "Drop Shadow";

    public DropShadowExperimentPage ()
    {
        InitializeComponent ();
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

        // Get values from sliders
        float dx = (float)dxSlider.Value;
        float dy = (float)dySlider.Value;
        float sigmaX = (float)sigmaXSlider.Value;
        float sigmaY = (float)sigmaYSlider.Value;

        using (SKPaint paint = new SKPaint())
        {
            // Set SKPaint properties
            paint.TextSize = info.Width / 7;
            paint.Color = SKColors.Blue;
            paint.ImageFilter = SKImageFilter.CreateDropShadow(
                                    dx,
                                    dy,
                                    sigmaX,
                                    sigmaY,
                                    SKColors.Red,
                                    SKDropShadowImageFilterShadowMode.DrawShadowAndForeground);

            SKRect textBounds = new SKRect();
            paint.MeasureText(TEXT, ref textBounds);

            // Center the text in the display rectangle
            float xText = info.Width / 2 - textBounds.MidX;
            float yText = info.Height / 2 - textBounds.MidY;

            canvas.DrawText(TEXT, xText, yText, paint);
        }
    }
}
```

下面是运行的程序：

[![删除卷影实验](image-filters-images/DropShadowExperiment.png "删除卷影实验")](image-filters-images/DropShadowExperiment-Large.png#lightbox)

在最右侧通用 Windows 平台的屏幕截图中的负偏移量的值会导致卷影才会出现上面和左侧的文本。 这表明光源右下方，不是计算机图形中的约定。 但这似乎不以任何方式，错误可能是因为卷影也将非常模糊，似乎比大多数投影更重在修饰。

## <a name="lighting-effects"></a>光照效果

`SKImageFilter`类定义使用类似的名称和参数，此处的不断增加的复杂性顺序列出的六种方法：

- [`CreateDistantLitDiffuse`](xref:SkiaSharp.SKImageFilter.CreateDistantLitDiffuse*)
- [`CreateDistantLitSpecular`](xref:SkiaSharp.SKImageFilter.CreateDistantLitSpecular*)
- [`CreatePointLitDiffuse`](xref:SkiaSharp.SKImageFilter.CreatePointLitDiffuse*)
- [`CreatePointLitSpecular`](xref:SkiaSharp.SKImageFilter.CreatePointLitSpecular*)
- [`CreateSpotLitDiffuse`](xref:SkiaSharp.SKImageFilter.CreateSpotLitDiffuse*)
- [`CreateSpotLitSpecular`](xref:SkiaSharp.SKImageFilter.CreateSpotLitSpecular*)

这些方法创建映像筛选器，以模仿三维图面上的不同类型的光效果。 生成的图像筛选器照亮二维对象，如同它们存在在 3D 空间中，这可能导致提升权限或凹陷出现这些对象，或使用反射照明。

`Distant`浅方法假定光线来自远距离。 为了人大开眼界对象，假定光线按一个方向一致类似于地球的较小区域上 Sun 三维空间中点。 `Point`浅方法模拟定位在 3D 空间中的所有方向发光灯泡。 `Spot` Light 具有位置和方向，类似于手电筒。

位置和方向在 3D 空间中的所指定的值与[ `SKPoint3` ](xref:SkiaSharp.SKPoint3)结构，它是类似于`SKPoint`具有名为三个属性，但`X`， `Y`，和`Z`。

数量和复杂程度对这些方法的参数进行试验与其困难。 若要开始，**存有一定距离 Light 试验**页中，可以试验参数`CreateDistantLightDiffuse`方法：

```csharp
public static SKImageFilter CreateDistantLitDiffuse (SKPoint3 direction,
                                                     SKColor lightColor,
                                                     float surfaceScale,
                                                     float kd,
                                                     SKImageFilter input = null,
                                                     SKImageFilter.CropRect cropRect = null);
```

页面不会使用最后两个可选参数。

三个`Slider`XAML 中的视图文件的允许您选择`Z`坐标`SKPoint3`值，`surfaceScale`参数，和`kd`"漫射照明常量"作为 API 文档中定义的参数：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaLightExperiment.MainPage"
             Title="Distant Light Experiment">

    <StackLayout>

        <skia:SKCanvasView x:Name="canvasView"
                           PaintSurface="OnCanvasViewPaintSurface"
                           VerticalOptions="FillAndExpand" />

        <Slider x:Name="zSlider"
                Minimum="-10"
                Maximum="10"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="{Binding Source={x:Reference zSlider},
                              Path=Value,
                              StringFormat='Z = {0:F0}'}"
               HorizontalTextAlignment="Center" />

        <Slider x:Name="surfaceScaleSlider"
                Minimum="-1"
                Maximum="1"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="{Binding Source={x:Reference surfaceScaleSlider},
                              Path=Value,
                              StringFormat='Surface Scale = {0:F1}'}"
               HorizontalTextAlignment="Center" />

        <Slider x:Name="lightConstantSlider"
                Minimum="-1"
                Maximum="1"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="{Binding Source={x:Reference lightConstantSlider},
                              Path=Value,
                              StringFormat='Light Constant = {0:F1}'}"
               HorizontalTextAlignment="Center" />
    </StackLayout>
</ContentPage>
```

代码隐藏文件获取这三个值，并使用它们来创建一个映像筛选器来显示文本字符串：

```csharp
public partial class DistantLightExperimentPage : ContentPage
{
    const string TEXT = "Lighting";

    public DistantLightExperimentPage()
    {
        InitializeComponent();
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

        float z = (float)zSlider.Value;
        float surfaceScale = (float)surfaceScaleSlider.Value;
        float lightConstant = (float)lightConstantSlider.Value;

        using (SKPaint paint = new SKPaint())
        {
            paint.IsAntialias = true;

            // Size text to 90% of canvas width
            paint.TextSize = 100;
            float textWidth = paint.MeasureText(TEXT);
            paint.TextSize *= 0.9f * info.Width / textWidth;

            // Find coordinates to center text
            SKRect textBounds = new SKRect();
            paint.MeasureText(TEXT, ref textBounds);

            float xText = info.Rect.MidX - textBounds.MidX;
            float yText = info.Rect.MidY - textBounds.MidY;

            // Create distant light image filter
            paint.ImageFilter = SKImageFilter.CreateDistantLitDiffuse(
                                    new SKPoint3(2, 3, z),
                                    SKColors.White,
                                    surfaceScale,
                                    lightConstant);

            canvas.DrawText(TEXT, xText, yText, paint);
        }
    }
}
```

第一个参数`SKImageFilter.CreateDistantLitDiffuse`是光的方向。 正 X 和 Y 坐标指示光是向右和向下指向。 在屏幕的正 Z 坐标点。 XAML 文件允许您选择负 Z 值, 但这只是为了您可以看到发生了什么情况:从概念上讲, 负 Z 坐标使光源指向屏幕。 用于任何其他然后小负值，光照效果将停止工作。

`surfaceScale`自变量的范围可以介于-1 到 1。 （高或较低的值不会进一步影响。）这些是在 Z 轴相对指示画布图面中的图形对象 （在此情况下，文本字符串） 的偏移量的值。 使用负值引发在画布的图面上面的文本字符串和正值按其下进画布。

`lightConstant`值应为正数。 （该程序允许负值以便您可以看到，它们会导致要停止工作的效果。）较高的值会导致更强烈的光。

这些因素可平衡获取浮雕时生效`surfaceScale`为负数 （与 iOS 和 Android 的屏幕截图中） 和阳文时生效`surfaceScale`为正，作为与右侧的 UWP 屏幕快照：

[![远处的光源实验](image-filters-images/DistantLightExperiment.png "远处的光源实验")](image-filters-images/DistantLightExperiment-Large.png#lightbox)

Android 屏幕快照具有 Z 值为 0，这意味着向下和向右仅指向光线。 在后台不点亮，照亮的文本字符串的图面并不是。 光只影响非常细微效果的文本的边缘。

[转换转换](../transforms/translate.md)中介绍了一种用于浮凸和阴文文本的替代方法:文本字符串将显示两次, 颜色分别偏移不同的颜色。

## <a name="related-links"></a>相关链接

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
