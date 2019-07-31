---
title: 与 Xamarin.Forms 集成
description: 本文介绍如何创建响应触摸的 SkiaSharp 图形和 Xamarin.Forms 元素，并演示此示例代码。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 288224F1-7AEE-4148-A88D-A70C03F83D7A
author: davidbritch
ms.author: dabritch
ms.date: 02/09/2017
ms.openlocfilehash: 586cf0eaa7b0f38af61c9f7d619b30a39023a19e
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2019
ms.locfileid: "68652958"
---
# <a name="integrating-with-xamarinforms"></a>与 Xamarin.Forms 集成

[![下载示例](~/media/shared/download.png)下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_创建响应触摸和 Xamarin.Forms 元素 SkiaSharp 图形_

SkiaSharp 图形可以与 Xamarin.Forms 中通过多种方法的其余部分集成。 你可以结合 SkiaSharp 画布和 Xamarin.Forms 元素在同一页上，并甚至定位 SkiaSharp 画布顶部的 Xamarin.Forms 元素：

![](integration-images/integrationexample.png "选择与滑块的颜色")

在 Xamarin.Forms 中创建交互式 SkiaSharp 图形的另一种方法是通过触摸屏输入。
中的第二页[ **SkiaSharpFormsDemos** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)程序有权使用**点击切换填充**。 一个简单的圆形两种方法，它可绘制&mdash;没有填充和填充了&mdash;通过点击切换。 [ `TapToggleFillPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/TapToggleFillPage.xaml.cs)类显示了如何改变 SkiaSharp 图形以响应用户输入。

此页上，对于`SKCanvasView`类中实例化[TapToggleFill.xaml](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/TapToggleFillPage.xaml)文件，还会设置 Xamarin.Forms [ `TapGestureRecognizer` ](xref:Xamarin.Forms.TapGestureRecognizer)在视图上：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.TapToggleFillPage"
             Title="Tap Toggle Fill">

    <skia:SKCanvasView PaintSurface="OnCanvasViewPaintSurface">
        <skia:SKCanvasView.GestureRecognizers>
            <TapGestureRecognizer Tapped="OnCanvasViewTapped" />
        </skia:SKCanvasView.GestureRecognizers>
    </skia:SKCanvasView>
</ContentPage>
```

请注意`skia`XML 命名空间声明。

`Tapped`处理程序`TapGestureRecognizer`对象只需切换的值的布尔型字段，并调用[ `InvalidateSurface` ](xref:SkiaSharp.Views.Forms.SKCanvasView.InvalidateSurface)方法`SKCanvasView`:

```csharp
bool showFill = true;
...
void OnCanvasViewTapped(object sender, EventArgs args)
{
    showFill ^= true;
    (sender as SKCanvasView).InvalidateSurface();
}
```

在调用`InvalidateSurface`有效地生成调用`PaintSurface`处理程序，它使用`showFill`字段填充或不填充该圆圈：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    SKPaint paint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = Color.Red.ToSKColor(),
        StrokeWidth = 50
    };
    canvas.DrawCircle(info.Width / 2, info.Height / 2, 100, paint);

    if (showFill)
    {
        paint.Style = SKPaintStyle.Fill;
        paint.Color = SKColors.Blue;
        canvas.DrawCircle(info.Width / 2, info.Height / 2, 100, paint);
    }
}
```

`StrokeWidth`已设置属性为 50 地突出显示不同之处。 您还可以通过首先绘制内部，，然后概要中查看整个线条宽度。 默认情况下，图形图表的更高版本中绘制`PaintSurface`事件处理程序会掩盖与前面的处理程序中绘制。

**颜色浏览**页演示了如何，您还可以在与其他 Xamarin.Forms 元素中，集成 SkiaSharp 图形，并且还演示用于在 SkiaSharp 中定义的颜色的两个替代方法之间的差异。 静态[ `SKColor.FromHsl` ](xref:SkiaSharp.SKColor.FromHsl(System.Single,System.Single,System.Single,System.Byte))方法创建`SKColor`值基于色调-饱和度-亮度模型：

```csharp
public static SKColor FromHsl (Single h, Single s, Single l, Byte a)
```

静态[ `SKColor.FromHsv` ](xref:SkiaSharp.SKColor.FromHsv(System.Single,System.Single,System.Single,System.Byte))方法创建`SKColor`值基于类似的色调-饱和度-值模型：

```csharp
public static SKColor FromHsv (Single h, Single s, Single v, Byte a)
```

在这两种情况下，`h`参数范围是从 0 到 360。 `s`， `l`，和`v`参数范围从 0 到 100。 `a` （alpha 或不透明度） 参数范围是从 0 到 255 之间。

[ **ColorExplorePage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/ColorExplorePage.xaml)文件将创建两个`SKCanvasView`中的对象`StackLayout`与并行`Slider`和`Label`的视图，以便用户选择 HSL 和 HSV颜色值：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Basics.ColorExplorePage"
             Title="Color Explore">
    <StackLayout>
        <!-- Hue slider -->
        <Slider x:Name="hueSlider"
                Maximum="360"
                Margin="20, 0"
                ValueChanged="OnSliderValueChanged" />

        <Label HorizontalTextAlignment="Center"
               Text="{Binding Source={x:Reference hueSlider},
                              Path=Value,
                              StringFormat='Hue = {0:F0}'}" />

        <!-- Saturation slider -->
        <Slider x:Name="saturationSlider"
                Maximum="100"
                Margin="20, 0"
                ValueChanged="OnSliderValueChanged" />

        <Label HorizontalTextAlignment="Center"
               Text="{Binding Source={x:Reference saturationSlider},
                              Path=Value,
                              StringFormat='Saturation = {0:F0}'}" />

        <!-- Lightness slider -->
        <Slider x:Name="lightnessSlider"
                Maximum="100"
                Margin="20, 0"
                ValueChanged="OnSliderValueChanged" />

        <Label HorizontalTextAlignment="Center"
               Text="{Binding Source={x:Reference lightnessSlider},
                              Path=Value,
                              StringFormat='Lightness = {0:F0}'}" />

        <!-- HSL canvas view -->
        <Grid VerticalOptions="FillAndExpand">
            <skia:SKCanvasView x:Name="hslCanvasView"
                               PaintSurface="OnHslCanvasViewPaintSurface" />

            <Label x:Name="hslLabel"
                   HorizontalOptions="Center"
                   VerticalOptions="Center"
                   BackgroundColor="Black"
                   TextColor="White" />
        </Grid>

        <!-- Value slider -->
        <Slider x:Name="valueSlider"
                Maximum="100"
                Margin="20, 0"
                ValueChanged="OnSliderValueChanged" />

        <Label HorizontalTextAlignment="Center"
               Text="{Binding Source={x:Reference valueSlider},
                              Path=Value,
                              StringFormat='Value = {0:F0}'}" />

        <!-- HSV canvas view -->
        <Grid VerticalOptions="FillAndExpand">
            <skia:SKCanvasView x:Name="hsvCanvasView"
                               PaintSurface="OnHsvCanvasViewPaintSurface" />

            <Label x:Name="hsvLabel"
                   HorizontalOptions="Center"
                   VerticalOptions="Center"
                   BackgroundColor="Black"
                   TextColor="White" />
        </Grid>
    </StackLayout>
</ContentPage>
```

这两个`SKCanvasView`元素位于单个单元格`Grid`与`Label`您坐在顶部显示的结果的 RGB 颜色值。

[ **ColorExplorePage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/ColorExplorePage.xaml.cs)代码隐藏文件是相对简单。 共享`ValueChanged`处理程序，三`Slider`元素只是使两者`SKCanvasView`元素。 `PaintSurface`处理程序所指示的颜色与清除画布`Slider`元素，并且还设置`Label`坐的`SKCanvasView`元素：

```csharp
public partial class ColorExplorePage : ContentPage
{
    public ColorExplorePage()
    {
        InitializeComponent();

        hueSlider.Value = 0;
        saturationSlider.Value = 100;
        lightnessSlider.Value = 50;
        valueSlider.Value = 100;
    }

    void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
    {
        hslCanvasView.InvalidateSurface();
        hsvCanvasView.InvalidateSurface();
    }

    void OnHslCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKColor color = SKColor.FromHsl((float)hueSlider.Value,
                                        (float)saturationSlider.Value,
                                        (float)lightnessSlider.Value);
        args.Surface.Canvas.Clear(color);

        hslLabel.Text = String.Format(" RGB = {0:X2}-{1:X2}-{2:X2} ",
                                      color.Red, color.Green, color.Blue);
    }

    void OnHsvCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKColor color = SKColor.FromHsv((float)hueSlider.Value,
                                        (float)saturationSlider.Value,
                                        (float)valueSlider.Value);
        args.Surface.Canvas.Clear(color);

        hsvLabel.Text = String.Format(" RGB = {0:X2}-{1:X2}-{2:X2} ",
                                      color.Red, color.Green, color.Blue);
    }
}
```

在 HSL 和 HSV 颜色模型中的色调值介于 0 到 360，并指示主导颜色色调的。 都是出喷薄彩虹的传统颜色： 红色、 橙色、 黄色、 绿色、 蓝色、 indigo、 紫色和后在圆圈中的为红色。

在 HSL 模型中，亮度的 0 值始终为黑色、 和 100 的值始终为白色。 饱和度值为 0，介于 0 和 100 之间的亮度值时，种灰色底纹。 增加饱和度将添加更多颜色。 （这些都是使用一个组件等于 255，另一个等于 0，且第三个范围从 0 到 255 的 RGB 值） 的纯颜色饱和度为 100，发生亮度为 50。

HSV 模型中的饱和度和值是 100 时，将导致纯颜色。 值为 0，而不考虑任何其他设置时的颜色为黑色。 饱和度是 0 和值的范围从 0 到 100 时，会出现灰色底纹。

但是，若要了解的两个模型的最佳方法是尝试使用它们自己：

[![](integration-images/colorexplore-large.png "颜色浏览页面的三个屏幕截图")](integration-images/colorexplore-small.png#lightbox "颜色浏览页面的三个屏幕截图")


## <a name="related-links"></a>相关链接

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
