---
title: SkiaSharp 中的路径效果
description: 本文介绍了各种 SkiaSharp 路径效果，路径用于进行描边，并填充，并演示此示例代码。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 95167D1F-A718-405A-AFCC-90E596D422F3
author: davidbritch
ms.author: dabritch
ms.date: 07/29/2017
ms.openlocfilehash: 82755a6a87ec0a47c10aac7078beeab6e14c218d
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70759384"
---
# <a name="path-effects-in-skiasharp"></a>SkiaSharp 中的路径效果

[![下载示例](~/media/shared/download.png)下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_发现允许路径用于进行描边，并填充的不同路径效果_

一个*路径效果*的一个实例[ `SKPathEffect` ](xref:SkiaSharp.SKPathEffect)使用八个由类定义的静态创建方法之一创建的类。 `SKPathEffect`对象设置为[ `PathEffect` ](xref:SkiaSharp.SKPaint.PathEffect)属性[ `SKPaint` ](xref:SkiaSharp.SKPaint)对象的有趣的效果，例如，有多种描画一条线较小的复制路径:

![链接链示例](effects-images/patheffectsample.png)

路径效果使您能够：

- 绘制一个行，其中点和短划线
- 绘制一个行，其中已填充的任何路径
- 填充区域的阴影线条
- 填充区域的平铺的路径
- 使尖锐角舍入
- 添加随机"抖动"到直线和曲线

此外，你可以组合两个或多个路径效果。

本文还演示了如何使用[ `GetFillPath` ](xref:SkiaSharp.SKPaint.GetFillPath*)方法`SKPaint`若要通过应用的属性将一条路径转换为另一个路径`SKPaint`，其中包括`StrokeWidth`和`PathEffect`。 这会导致一些有趣的技术，例如获取是另一个路径的大纲的路径。 `GetFillPath` 技术还有助于与路径效果。

## <a name="dots-and-dashes"></a>点和短划线

利用[ `PathEffect.CreateDash` ](xref:SkiaSharp.SKPathEffect.CreateDash(System.Single[],System.Single))一文中所述方法[**点和短划线**](~/xamarin-forms/user-interface/graphics/skiasharp/paths/dots.md)。 该方法的第一个参数是一个数组，包含两个或多个值，交替使用短划线的长度，以及连续的短划线的长度为偶数：

```csharp
public static SKPathEffect CreateDash (Single[] intervals, Single phase)
```

这些值是*不*相对于描边宽度。 例如，如果笔划宽度为 10，并且你想行组成方形短划线和正方形的间隙，设置`intervals`数组为 {10，10}。 `phase`参数指示虚线样式内行开始的位置。 在此示例中，如果你想要以开头的方形差距的行，设置`phase`为 10。

受影响的短划线结束`StrokeCap`属性的`SKPaint`。 对于宽描边宽度，它是很常见，若要将此属性设置为`SKStrokeCap.Round`要舍入短划线结尾。 在此情况下中的值`intervals`数组进行*不*包括舍入导致的额外长度。 这一事实意味着循环点要求指定的宽度为零。 笔划宽度为 10，若要创建具有圆点和相同的直径的点之间间隙的行，使用`intervals`数组 {0，20}。

**经过动画处理的以点分隔的文本**页是类似于**轮廓文本**文章中所述的页面[**集成文本和图形**](~/xamarin-forms/user-interface/graphics/skiasharp/basics/text.md)中它将显示通过设置概述文本字符`Style`的属性`SKPaint`对象传递给`SKPaintStyle.Stroke`。 此外，**经过动画处理的以点分隔的文本**使用`SKPathEffect.CreateDash`为提供此概述的以点分隔的外观和程序还进行动画处理`phase`自变量的`SKPathEffect.CreateDash`进行似乎旅行文本周围的点的方法字符。 以下是在横向模式中的页：

[![动态虚线文本页的三向屏幕截图](effects-images/animateddottedtext-small.png)](effects-images/animateddottedtext-large.png#lightbox)

[ `AnimatedDottedTextPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/DotDashMorphPage.cs)类首先定义一些常量，并且还重写`OnAppearing`和`OnDisappearing`动画的方法：

```csharp
public class AnimatedDottedTextPage : ContentPage
{
    const string text = "DOTTED";
    const float strokeWidth = 10;
    static readonly float[] dashArray = { 0, 2 * strokeWidth };

    SKCanvasView canvasView;
    bool pageIsActive;

    public AnimatedDottedTextPage()
    {
        Title = "Animated Dotted Text";

        canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }

    protected override void OnAppearing()
    {
        base.OnAppearing();
        pageIsActive = true;

        Device.StartTimer(TimeSpan.FromSeconds(1f / 60), () =>
        {
            canvasView.InvalidateSurface();
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

`PaintSurface`处理程序首先创建`SKPaint`对象显示的文本。 `TextSize`根据屏幕的宽度调整属性：

```csharp
public class AnimatedDottedTextPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Create an SKPaint object to display the text
        using (SKPaint textPaint = new SKPaint
            {
                Style = SKPaintStyle.Stroke,
                StrokeWidth = strokeWidth,
                StrokeCap = SKStrokeCap.Round,
                Color = SKColors.Blue,
            })
        {
            // Adjust TextSize property so text is 95% of screen width
            float textWidth = textPaint.MeasureText(text);
            textPaint.TextSize *= 0.95f * info.Width / textWidth;

            // Find the text bounds
            SKRect textBounds = new SKRect();
            textPaint.MeasureText(text, ref textBounds);

            // Calculate offsets to center the text on the screen
            float xText = info.Width / 2 - textBounds.MidX;
            float yText = info.Height / 2 - textBounds.MidY;

            // Animate the phase; t is 0 to 1 every second
            TimeSpan timeSpan = new TimeSpan(DateTime.Now.Ticks);
            float t = (float)(timeSpan.TotalSeconds % 1 / 1);
            float phase = -t * 2 * strokeWidth;

            // Create dotted line effect based on dash array and phase
            using (SKPathEffect dashEffect = SKPathEffect.CreateDash(dashArray, phase))
            {
                // Set it to the paint object
                textPaint.PathEffect = dashEffect;

                // And draw the text
                canvas.DrawText(text, xText, yText, textPaint);
            }
        }
    }
}
```

该方法末尾`SKPathEffect.CreateDash`使用调用方法`dashArray`定义为一个字段，并经过动画处理`phase`值。 `SKPathEffect`实例设置为`PathEffect`属性的`SKPaint`对象显示的文本。

或者，可以设置`SKPathEffect`对象传递给`SKPaint`之前测量文本和集中的页上的对象。 在这种情况下，但是，动画的点和短划线呈现文本的大小会导致一些变体，并且文本可能有点振动。 （试试看 ！）

您会注意到，作为文本字符四周的动画的点圆圈，没有已关闭的各段曲线中的某一点点似乎弹入和移出存在。 这是定义字符轮廓路径的开始和结束位置。 如果路径长度不是短划线图案 （在这种情况下 20 像素为单位），长度的整数倍，则该模式的唯一部分可容纳在路径的末尾。

可以调整以适应路径，短划线图案的长度，但需要确定路径的文章中介绍的技术的长度[**路径信息和枚举**](information.md).

**Dot / 短划线 Morph**程序之间进行动画处理的短划线模式本身，以便短划线似乎将划分为点，再次将到窗体短划线组合：

[![点划线平滑页面的三向屏幕截图](effects-images/dotdashmorph-small.png)](effects-images/dotdashmorph-large.png#lightbox)

[ `DotDashMorphPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/DotDashMorphPage.cs)类重写`OnAppearing`并`OnDisappearing`方法就像上一个程序一样，但该类定义`SKPaint`对象作为字段：

```csharp
public class DotDashMorphPage : ContentPage
{
    const float strokeWidth = 30;
    static readonly float[] dashArray = new float[4];

    SKCanvasView canvasView;
    bool pageIsActive = false;

    SKPaint ellipsePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        StrokeWidth = strokeWidth,
        StrokeCap = SKStrokeCap.Round,
        Color = SKColors.Blue
    };
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Create elliptical path
        using (SKPath ellipsePath = new SKPath())
        {
            ellipsePath.AddOval(new SKRect(50, 50, info.Width - 50, info.Height - 50));

            // Create animated path effect
            TimeSpan timeSpan = new TimeSpan(DateTime.Now.Ticks);
            float t = (float)(timeSpan.TotalSeconds % 3 / 3);
            float phase = 0;

            if (t < 0.25f)  // 1, 0, 1, 2 --> 0, 2, 0, 2
            {
                float tsub = 4 * t;
                dashArray[0] = strokeWidth * (1 - tsub);
                dashArray[1] = strokeWidth * 2 * tsub;
                dashArray[2] = strokeWidth * (1 - tsub);
                dashArray[3] = strokeWidth * 2;
            }
            else if (t < 0.5f)  // 0, 2, 0, 2 --> 1, 2, 1, 0
            {
                float tsub = 4 * (t - 0.25f);
                dashArray[0] = strokeWidth * tsub;
                dashArray[1] = strokeWidth * 2;
                dashArray[2] = strokeWidth * tsub;
                dashArray[3] = strokeWidth * 2 * (1 - tsub);
                phase = strokeWidth * tsub;
            }
            else if (t < 0.75f) // 1, 2, 1, 0 --> 0, 2, 0, 2
            {
                float tsub = 4 * (t - 0.5f);
                dashArray[0] = strokeWidth * (1 - tsub);
                dashArray[1] = strokeWidth * 2;
                dashArray[2] = strokeWidth * (1 - tsub);
                dashArray[3] = strokeWidth * 2 * tsub;
                phase = strokeWidth * (1 - tsub);
            }
            else               // 0, 2, 0, 2 --> 1, 0, 1, 2
            {
                float tsub = 4 * (t - 0.75f);
                dashArray[0] = strokeWidth * tsub;
                dashArray[1] = strokeWidth * 2 * (1 - tsub);
                dashArray[2] = strokeWidth * tsub;
                dashArray[3] = strokeWidth * 2;
            }

            using (SKPathEffect pathEffect = SKPathEffect.CreateDash(dashArray, phase))
            {
                ellipsePaint.PathEffect = pathEffect;
                canvas.DrawPath(ellipsePath, ellipsePaint);
            }
        }
    }
}
```

`PaintSurface`处理程序将创建基于页的大小的椭圆路径并执行一长段代码，用于设置`dashArray`和`phase`变量。 为动画变量`t`范围是从 0 到 1，`if`块分解成四个季度，并在每个这些季度，该时间`tsub`也介于 0 到 1。 最后，在该程序创建`SKPathEffect`并将其设置为`SKPaint`对象以进行绘制。

## <a name="from-path-to-path"></a>从路径的路径

[ `GetFillPath` ](xref:SkiaSharp.SKPaint.GetFillPath(SkiaSharp.SKPath,SkiaSharp.SKPath,System.Single))方法`SKPaint`将转换为另一个基于中设置一条路径`SKPaint`对象。 若要阐释其工作方式，将为`canvas.DrawPath`调用在上一程序中使用以下代码：

```csharp
SKPath newPath = new SKPath();
bool fill = ellipsePaint.GetFillPath(ellipsePath, newPath);
SKPaint newPaint = new SKPaint
{
    Style = fill ? SKPaintStyle.Fill : SKPaintStyle.Stroke
};
canvas.DrawPath(newPath, newPaint);

```

在此新代码中，`GetFillPath`调用转换`ellipsePath`（这是只需一个椭圆） 到`newPath`，然后显示与`newPaint`。 `newPaint`使用所有默认属性设置创建对象，只不过`Style`属性设置基于上一个布尔值，返回值从`GetFillPath`。

视觉对象是相同的颜色，在中设置除外`ellipsePaint`但不是`newPaint`。 而不是在中定义的简单椭圆`ellipsePath`，`newPath`包含定义的点和短划线系列的大量路径分布图。 这是应用的各种属性的结果`ellipsePaint`(具体而言， `StrokeWidth`， `StrokeCap`，和`PathEffect`) 到`ellipsePath`置于生成的路径和`newPath`。 `GetFillPath`方法返回一个布尔值，该值指示是否目标路径为要填充; 在此示例中，返回值是`true`用于填充的路径。

请尝试更改`Style`中设置`newPaint`到`SKPaintStyle.Stroke`，您将看到一个像素宽度行所述的各个路径分布。

## <a name="stroking-with-a-path"></a>使用路径描画

[ `SKPathEffect.Create1DPath` ](xref:SkiaSharp.SKPathEffect.Create1DPath(SkiaSharp.SKPath,System.Single,System.Single,SkiaSharp.SKPath1DPathEffectStyle))方法是从概念上讲类似于`SKPathEffect.CreateDash`只是指定一个路径而不是短划线和间隙的模式。 此路径将复制到笔划直线或曲线多次。

语法为：

```csharp
public static SKPathEffect Create1DPath (SKPath path, Single advance,
                                         Single phase, SKPath1DPathEffectStyle style)
```

通常，您将传递到的路径`Create1DPath`将很小，围绕点 （0，0） 居中。 `advance`参数指示的路径中的中心之间的距离，作为路径复制在行上。 通常情况下，路径的大致宽度到需要设置此参数。 `phase`与它相同此处的角色中执行的自变量完美`CreateDash`方法。

[ `SKPath1DPathEffectStyle` ](xref:SkiaSharp.SKPath1DPathEffectStyle)有三个成员：

- `Translate`
- `Rotate`
- `Morph`

`Translate`成员会导致要保留在相同方向，如复制根据直线或曲线的路径。 有关`Rotate`，路径根据为曲线的切线旋转。 路径都有其正常方向为水平线条。 `Morph` 类似于`Rotate`不同之处在于路径本身也曲线以匹配正在描边的行的曲率。

**1 D 路径效果**页演示这三个选项。 [ **OneDimensionalPathEffectPage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/OneDimensionalPathEffectPage.xaml)文件定义包含对应于枚举的三个成员的三个项的选取器：

```xaml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Curves.OneDimensionalPathEffectPage"
             Title="1D Path Effect">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="*" />
        </Grid.RowDefinitions>

        <Picker x:Name="effectStylePicker"
                Title="Effect Style"
                Grid.Row="0"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.ItemsSource>
                <x:Array Type="{x:Type x:String}">
                    <x:String>Translate</x:String>
                    <x:String>Rotate</x:String>
                    <x:String>Morph</x:String>
                </x:Array>
            </Picker.ItemsSource>
            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>

        <skia:SKCanvasView x:Name="canvasView"
                           PaintSurface="OnCanvasViewPaintSurface"
                           Grid.Row="1" />
    </Grid>
</ContentPage>
```

[ **OneDimensionalPathEffectPage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/OneDimensionalPathEffectPage.xaml.cs)代码分离文件定义了三个`SKPathEffect`作为字段的对象。 这些创建使用`SKPathEffect.Create1DPath`与`SKPath`对象创建使用`SKPath.ParseSvgPathData`。 第一种是简单的框中，第二个是一个菱形，第三个是一个矩形。 这些用于演示三个效果样式：

```csharp
public partial class OneDimensionalPathEffectPage : ContentPage
{
    SKPathEffect translatePathEffect =
        SKPathEffect.Create1DPath(SKPath.ParseSvgPathData("M -10 -10 L 10 -10, 10 10, -10 10 Z"),
                                  24, 0, SKPath1DPathEffectStyle.Translate);

    SKPathEffect rotatePathEffect =
        SKPathEffect.Create1DPath(SKPath.ParseSvgPathData("M -10 0 L 0 -10, 10 0, 0 10 Z"),
                                  20, 0, SKPath1DPathEffectStyle.Rotate);

    SKPathEffect morphPathEffect =
        SKPathEffect.Create1DPath(SKPath.ParseSvgPathData("M -25 -10 L 25 -10, 25 10, -25 10 Z"),
                                  55, 0, SKPath1DPathEffectStyle.Morph);

    SKPaint pathPaint = new SKPaint
    {
        Color = SKColors.Blue
    };

    public OneDimensionalPathEffectPage()
    {
        InitializeComponent();
    }

    void OnPickerSelectedIndexChanged(object sender, EventArgs args)
    {
        if (canvasView != null)
        {
            canvasView.InvalidateSurface();
        }
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPath path = new SKPath())
        {
            path.MoveTo(new SKPoint(0, 0));
            path.CubicTo(new SKPoint(2 * info.Width, info.Height),
                         new SKPoint(-info.Width, info.Height),
                         new SKPoint(info.Width, 0));

            switch ((string)effectStylePicker.SelectedItem))
            {
                case "Translate":
                    pathPaint.PathEffect = translatePathEffect;
                    break;

                case "Rotate":
                    pathPaint.PathEffect = rotatePathEffect;
                    break;

                case "Morph":
                    pathPaint.PathEffect = morphPathEffect;
                    break;
            }

            canvas.DrawPath(path, pathPaint);
        }
    }
}
```

`PaintSurface`处理程序将创建循环继续本身，并访问选取器来确定哪些的贝塞尔曲线`PathEffect`应该用于绘制它。 三个选项 — `Translate`， `Rotate`，和`Morph`— 从左到右显示：

[![1D 路径效果页面的三向屏幕截图](effects-images/1dpatheffect-small.png)](effects-images/1dpatheffect-large.png#lightbox)

中指定的路径`SKPathEffect.Create1DPath`始终填充方法。 中指定的路径`DrawPath`方法始终为描边，如果`SKPaint`对象都有其`PathEffect`属性设置为 1 D 路径效果。 请注意，`pathPaint`对象没有`Style`设置，它通常默认为`Fill`，但路径为无论描边。

在中使用的框`Translate`示例中为 20 像素正方形，和`advance`参数设置为 24。 这种差异会导致差异时行大致是水平还是垂直的但对应的框有点重叠时由于框的对角线为 28.3 像素行是沿对角方向的框。

中的菱形形状`Rotate`示例也是 20 个像素宽。 `advance` ，以便继续触摸，因为菱形旋转以及行的曲度点设置为 20。

中的矩形形状`Morph`示例是使用宽 50 像素`advance`55 设置以使它们弯曲的情况下绕贝塞尔曲线的矩形之间一小段时间间隙。

如果`advance`参数小于大小的路径，则复制的路径可以重叠。 这可能导致一些有趣的效果。 **链接链**页显示的重叠圆圈看起来类似于一个链接的链，挂起 catenary 的独特形状中的一系列：

[![链接链页面的三向屏幕截图](effects-images/linkedchain-small.png)](effects-images/linkedchain-large.png#lightbox)

看起来非常接近，将看到这些并不实际圆圈。 链中的每个链接是两个弧，大小调整和定位使它们看起来与相邻的链接进行连接。

链或电缆的统一权重分配挂起 catenary 窗体中。 内置的倒排 catenary 窗体的体系结构利用压力从体系结构的权重相等分布。 Catenary 具有看似简单的数学说明：

`y = a · cosh(x / a)`

*Cosh*是双曲余弦值函数。 有关*x*等于 0， *cosh*为零并*y*等于。 这是 catenary 的中心。 像*余弦*函数， *cosh*称为*甚至*，这意味着， *cosh(–x)* 等于*cosh(x)* ，并增加正或负参数值增加。 这些值描述窗体的 catenary 边的曲线。

查找适当的值以适应到手机的页的尺寸 catenary 不是直接计算。 如果*w*并*h*宽度和高度的矩形的最佳值满足以下公式：

`cosh(w / 2 / a) = 1 + h / a`

中的以下方法[ `LinkedChainPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/LinkedChainPage.cs)类包含该相等性指两个表达式左侧和右侧的等号作为`left`和`right`。 值较小，`left`大于`right`; 对于较大的值，`left`是小于`right`。 `while`用于限制中的循环上的一个最佳值:

```csharp
float FindOptimumA(float width, float height)
{
    Func<float, float> left = (float a) => (float)Math.Cosh(width / 2 / a);
    Func<float, float> right = (float a) => 1 + height / a;

    float gtA = 1;         // starting value for left > right
    float ltA = 10000;     // starting value for left < right

    while (Math.Abs(gtA - ltA) > 0.1f)
    {
        float avgA = (gtA + ltA) / 2;

        if (left(avgA) < right(avgA))
        {
            ltA = avgA;
        }
        else
        {
            gtA = avgA;
        }
    }

    return (gtA + ltA) / 2;
}
```

`SKPath`类的构造函数，并且所产生的中创建链接对象`SKPathEffect`然后将对象设置为`PathEffect`属性的`SKPaint`作为字段存储的对象：

```csharp
public class LinkedChainPage : ContentPage
{
    const float linkRadius = 30;
    const float linkThickness = 5;

    Func<float, float, float> catenary = (float a, float x) => (float)(a * Math.Cosh(x / a));

    SKPaint linksPaint = new SKPaint
    {
        Color = SKColors.Silver
    };

    public LinkedChainPage()
    {
        Title = "Linked Chain";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;

        // Create the path for the individual links
        SKRect outer = new SKRect(-linkRadius, -linkRadius, linkRadius, linkRadius);
        SKRect inner = outer;
        inner.Inflate(-linkThickness, -linkThickness);

        using (SKPath linkPath = new SKPath())
        {
            linkPath.AddArc(outer, 55, 160);
            linkPath.ArcTo(inner, 215, -160, false);
            linkPath.Close();

            linkPath.AddArc(outer, 235, 160);
            linkPath.ArcTo(inner, 395, -160, false);
            linkPath.Close();

            // Set that path as the 1D path effect for linksPaint
            linksPaint.PathEffect =
                SKPathEffect.Create1DPath(linkPath, 1.3f * linkRadius, 0,
                                          SKPath1DPathEffectStyle.Rotate);
        }
    }
    ...
}
```

主要工作`PaintSurface`处理程序是创建 catenary 本身的路径。 确定最佳之后并将其存储在`optA`变量时，它还需要计算从窗口顶部的偏移量。 然后，它可能会累积一系列`SKPoint`成一个路径中，打开的和绘制并用先前创建的路径值 catenary`SKPaint`对象：

```csharp
public class LinkedChainPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear(SKColors.Black);

        // Width and height of catenary
        int width = info.Width;
        float height = info.Height - linkRadius;

        // Find the optimum 'a' for this width and height
        float optA = FindOptimumA(width, height);

        // Calculate the vertical offset for that value of 'a'
        float yOffset = catenary(optA, -width / 2);

        // Create a path for the catenary
        SKPoint[] points = new SKPoint[width];

        for (int x = 0; x < width; x++)
        {
            points[x] = new SKPoint(x, yOffset - catenary(optA, x - width / 2));
        }

        using (SKPath path = new SKPath())
        {
            path.AddPoly(points, false);

            // And render that path with the linksPaint object
            canvas.DrawPath(path, linksPaint);
        }
    }
    ...
}
```

此程序定义中使用的路径`Create1DPath`具有其 （0，0） 中的中心点。 这似乎是合理因为 （0，0） 路径的终结点对齐线或它所装饰的曲线。 但是，可以使用非居中 （0，0），为一些特殊效果点。

**传送带**页创建类似于曲线的顶部和底部大小调整窗口的尺寸为外型传送带的路径。 该路径使用简单描边`SKPaint`对象 20 像素宽和彩色的灰色，然后能与另一个再次描边`SKPaint`对象`SKPathEffect`对象引用类似于较小范围的路径：

[![传送带页面的三向屏幕截图](effects-images/conveyorbelt-small.png)](effects-images/conveyorbelt-large.png#lightbox)

（0，0） 存储桶路径的终结点是句柄，因此，在`phase`参数进行动画处理、 存储桶似乎围绕传送带，可能会在底部的水 scooping 和顶部转储一下。

[ `ConveyorBeltPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ConveyorBeltPage.cs)类实现动画的重写`OnAppearing`和`OnDisappearing`方法。 在页面的构造函数中定义的存储桶的路径：

```csharp
public class ConveyorBeltPage : ContentPage
{
    SKCanvasView canvasView;
    bool pageIsActive = false;

    SKPaint conveyerPaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        StrokeWidth = 20,
        Color = SKColors.DarkGray
    };

    SKPath bucketPath = new SKPath();

    SKPaint bucketsPaint = new SKPaint
    {
        Color = SKColors.BurlyWood,
    };

    public ConveyorBeltPage()
    {
        Title = "Conveyor Belt";

        canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;

        // Create the path for the bucket starting with the handle
        bucketPath.AddRect(new SKRect(-5, -3, 25, 3));

        // Sides
        bucketPath.AddRoundedRect(new SKRect(25, -19, 27, 18), 10, 10,
                                  SKPathDirection.CounterClockwise);
        bucketPath.AddRoundedRect(new SKRect(63, -19, 65, 18), 10, 10,
                                  SKPathDirection.CounterClockwise);

        // Five slats
        for (int i = 0; i < 5; i++)
        {
            bucketPath.MoveTo(25, -19 + 8 * i);
            bucketPath.LineTo(25, -13 + 8 * i);
            bucketPath.ArcTo(50, 50, 0, SKPathArcSize.Small,
                             SKPathDirection.CounterClockwise, 65, -13 + 8 * i);
            bucketPath.LineTo(65, -19 + 8 * i);
            bucketPath.ArcTo(50, 50, 0, SKPathArcSize.Small,
                             SKPathDirection.Clockwise, 25, -19 + 8 * i);
            bucketPath.Close();
        }

        // Arc to suggest the hidden side
        bucketPath.MoveTo(25, -17);
        bucketPath.ArcTo(50, 50, 0, SKPathArcSize.Small,
                         SKPathDirection.Clockwise, 65, -17);
        bucketPath.LineTo(65, -19);
        bucketPath.ArcTo(50, 50, 0, SKPathArcSize.Small,
                         SKPathDirection.CounterClockwise, 25, -19);
        bucketPath.Close();

        // Make it a little bigger and correct the orientation
        bucketPath.Transform(SKMatrix.MakeScale(-2, 2));
        bucketPath.Transform(SKMatrix.MakeRotationDegrees(90));
    }
    ...
```

存储桶创建代码完成两个转换，使稍大一些的存储桶和侧向打开它。 应用这些转换是比调整前面的代码中的所有坐标更容易。

`PaintSurface`处理程序首先定义传送带本身的路径。 这是只需一对行和一对以绘制有 20 个像素宽暗灰色线的圆圈：

```csharp
public class ConveyorBeltPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        float width = info.Width / 3;
        float verticalMargin = width / 2 + 150;

        using (SKPath conveyerPath = new SKPath())
        {
            // Straight verticals capped by semicircles on top and bottom
            conveyerPath.MoveTo(width, verticalMargin);
            conveyerPath.ArcTo(width / 2, width / 2, 0, SKPathArcSize.Large,
                               SKPathDirection.Clockwise, 2 * width, verticalMargin);
            conveyerPath.LineTo(2 * width, info.Height - verticalMargin);
            conveyerPath.ArcTo(width / 2, width / 2, 0, SKPathArcSize.Large,
                               SKPathDirection.Clockwise, width, info.Height - verticalMargin);
            conveyerPath.Close();

            // Draw the conveyor belt itself
            canvas.DrawPath(conveyerPath, conveyerPaint);

            // Calculate spacing based on length of conveyer path
            float length = 2 * (info.Height - 2 * verticalMargin) +
                           2 * ((float)Math.PI * width / 2);

            // Value will be somewhere around 200
            float spacing = length / (float)Math.Round(length / 200);

            // Now animate the phase; t is 0 to 1 every 2 seconds
            TimeSpan timeSpan = new TimeSpan(DateTime.Now.Ticks);
            float t = (float)(timeSpan.TotalSeconds % 2 / 2);
            float phase = -t * spacing;

            // Create the buckets PathEffect
            using (SKPathEffect bucketsPathEffect =
                        SKPathEffect.Create1DPath(bucketPath, spacing, phase,
                                                  SKPath1DPathEffectStyle.Rotate))
            {
                // Set it to the Paint object and draw the path again
                bucketsPaint.PathEffect = bucketsPathEffect;
                canvas.DrawPath(conveyerPath, bucketsPaint);
            }
        }
    }
}
```

在横向模式下并不适用于绘制传送带的逻辑。

应大约为 200 像素相隔传送带上分布的存储桶。 但是，传送带可能并不是 200 像素长，这意味着为倍数`phase`自变量的`SKPathEffect.Create1DPath`是进行动画处理，存储桶将弹入和移出是否存在。

出于此原因，程序会先计算名为的值`length`即传送带的长度。 传送带组成的直线和以圆，因为这是一个简单的计算。 接下来，存储桶数的计算除以`length`通过 200。 这舍入为最接近的整数，而数量则是划分成`length`。 结果是不可或缺的存储桶数的间距。 `phase`参数是只是的一小部分。

## <a name="from-path-to-path-again"></a>从再次路径的路径

在底部`DrawSurface`处理程序中的**传送带**，注释掉`canvas.DrawPath`调用并替换为以下代码：

```csharp
SKPath newPath = new SKPath();
bool fill = bucketsPaint.GetFillPath(conveyerPath, newPath);
SKPaint newPaint = new SKPaint
{
    Style = fill ? SKPaintStyle.Fill : SKPaintStyle.Stroke
};
canvas.DrawPath(newPath, newPaint);
```

与前面的示例一样`GetFillPath`，您将看到的结果都是相同颜色除外。 执行后`GetFillPath`，则`newPath`对象包含的存储桶路径的多个副本，每个定位在同一个发现，动画的优势，它们在调用时。

## <a name="hatching-an-area"></a>阴影区域

[ `SKPathEffect.Create2DLines` ](xref:SkiaSharp.SKPathEffect.Create2DLine(System.Single,SkiaSharp.SKMatrix))方法填充平行直线，通常称为在区域*阴影线*。 该方法具有以下语法：

```csharp
public static SKPathEffect Create2DLine (Single width, SKMatrix matrix)
```

`width`参数指定的阴影线条的笔划宽度。 `matrix`参数是缩放和可选旋转的组合。 比例因子指示 Skia 使用空间的阴影线条的像素增量。 线之间的分离是减去的缩放因子`width`参数。 比例因子是否小于或等于`width`值之间的阴影线条，将有没有空间和区域将显示要填充。 指定的水平和垂直缩放相同的值。

默认情况下，阴影线条是水平的。 如果`matrix`参数包含旋转，沿顺时针方向旋转的阴影线条。

**阴影填充**页说明此路径效果。 [ `HatchFillPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/HatchFillPage.cs)类为字段定义三个路径效果，它们是为水平的阴影线条宽度为 3 个像素使用一个缩放系数，该值指示第一个分布相隔 6 个像素。 因此，行之间的分离是三个像素。 第二个路径效果是垂直的阴影线条宽度为 6 个像素夹角 24 像素相隔 （因此分离为 18 个像素），以及第三个是否是对角线阴影线 12 像素宽间距 36 像素相隔的。

```csharp
public class HatchFillPage : ContentPage
{
    SKPaint fillPaint = new SKPaint();

    SKPathEffect horzLinesPath = SKPathEffect.Create2DLine(3, SKMatrix.MakeScale(6, 6));

    SKPathEffect vertLinesPath = SKPathEffect.Create2DLine(6,
        Multiply(SKMatrix.MakeRotationDegrees(90), SKMatrix.MakeScale(24, 24)));

    SKPathEffect diagLinesPath = SKPathEffect.Create2DLine(12,
        Multiply(SKMatrix.MakeScale(36, 36), SKMatrix.MakeRotationDegrees(45)));

    SKPaint strokePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        StrokeWidth = 3,
        Color = SKColors.Black
    };
    ...
    static SKMatrix Multiply(SKMatrix first, SKMatrix second)
    {
        SKMatrix target = SKMatrix.MakeIdentity();
        SKMatrix.Concat(ref target, first, second);
        return target;
    }
}
```

请注意，矩阵`Multiply`方法。 水平和垂直缩放比例是相同的因为在其中缩放和旋转矩阵相乘的顺序并不重要。

`PaintSurface`处理程序使用三个不同颜色结合使用这些三个路径效果`fillPaint`以填充圆角的矩形调整为适合页面大小。 `Style`属性上设置`fillPaint`将被忽略; 当`SKPaint`对象包含从创建一个路径效果`SKPathEffect.Create2DLine`，而不考虑填充的区域：

```csharp
public class HatchFillPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPath roundRectPath = new SKPath())
        {
            // Create a path
            roundRectPath.AddRoundedRect(
                new SKRect(50, 50, info.Width - 50, info.Height - 50), 100, 100);

            // Horizontal hatch marks
            fillPaint.PathEffect = horzLinesPath;
            fillPaint.Color = SKColors.Red;
            canvas.DrawPath(roundRectPath, fillPaint);

            // Vertical hatch marks
            fillPaint.PathEffect = vertLinesPath;
            fillPaint.Color = SKColors.Blue;
            canvas.DrawPath(roundRectPath, fillPaint);

            // Diagonal hatch marks -- use clipping
            fillPaint.PathEffect = diagLinesPath;
            fillPaint.Color = SKColors.Green;

            canvas.Save();
            canvas.ClipPath(roundRectPath);
            canvas.DrawRect(new SKRect(0, 0, info.Width, info.Height), fillPaint);
            canvas.Restore();

            // Outline the path
            canvas.DrawPath(roundRectPath, strokePaint);
        }
    }
    ...
}
```

如果您仔细看一下结果，您将看到红色和蓝色阴影线不局限精确于圆角矩形。 （这是很明显的特征基础 Skia 代码。）如果这不满意，则会为绿色对角影线线显示一种替代方法：圆角矩形用作剪切路径，在整个页面上绘制阴影线。

`PaintSurface`处理程序结束调用来只需绘制圆角的矩形，以便您可以看到红色和蓝色阴影线条与差异：

[!["阴影填充" 页的三向屏幕截图](effects-images/hatchfill-small.png)](effects-images/hatchfill-large.png#lightbox)

Android 屏幕并不真正如下所示：屏幕截图的缩放导致了细的红线和细的空格，以将其合并到看似更宽的红线和更宽的空间。

## <a name="filling-with-a-path"></a>填充的路径

[ `SKPathEffect.Create2DPath` ](xref:SkiaSharp.SKPathEffect.Create2DPath(SkiaSharp.SKMatrix,SkiaSharp.SKPath))实际上允许您以填充区域的水平或垂直地复制路径平铺区域：

```csharp
public static SKPathEffect Create2DPath (SKMatrix matrix, SKPath path)
```

`SKMatrix`缩放比例指示复制路径的水平和垂直间距。 但不能旋转在路径中使用这`matrix`自变量; 如果你想旋转，路径旋转路径本身使用`Transform`方法定义的`SKPath`。

复制的路径通常与屏幕而不是所填充的区域的左侧和顶部边缘对齐。 可以通过提供介于 0 和缩放的比例来指定从左侧和顶部边的水平和垂直偏移量的平移因数重写此行为。

**路径磁贴填充**页说明此路径效果。 使用用于平铺区域的路径定义为中的字段[ `PathTileFillPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/PathTileFillPage.cs)类。 水平和垂直坐标范围从 –40 到 40，这意味着此路径是 80 像素方形：

```csharp
public class PathTileFillPage : ContentPage
{
    SKPath tilePath = SKPath.ParseSvgPathData(
        "M -20 -20 L 2 -20, 2 -40, 18 -40, 18 -20, 40 -20, " +
        "40 -12, 20 -12, 20 12, 40 12, 40 40, 22 40, 22 20, " +
        "-2 20, -2 40, -20 40, -20 8, -40 8, -40 -8, -20 -8 Z");
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPaint paint = new SKPaint())
        {
            paint.Color = SKColors.Red;

            using (SKPathEffect pathEffect =
                   SKPathEffect.Create2DPath(SKMatrix.MakeScale(64, 64), tilePath))
            {
                paint.PathEffect = pathEffect;

                canvas.DrawRoundRect(
                    new SKRect(50, 50, info.Width - 50, info.Height - 50),
                    100, 100, paint);
            }
        }
    }
}
```

在中`PaintSurface`处理程序，`SKPathEffect.Create2DPath`调用设置为 64 导致 80 像素方形磁贴以重叠的水平和垂直间距。 幸运的是，该路径类似于填数游戏块，可以很好地结合使用相邻的磁贴：

[![路径磁贴填充页的三向屏幕截图](effects-images/pathtilefill-small.png)](effects-images/pathtilefill-large.png#lightbox)

原始的屏幕截图中缩放导致某些扭曲，特别是在 Android 屏幕上。

请注意，这些磁贴始终显示整个并永远不会被截断。 在前两个屏幕截图不甚至明显所填充的区域是一个圆角的矩形。 如果你想要截断这些磁贴的特定区域，使用剪切路径。

请尝试设置`Style`属性`SKPaint`对象传递给`Stroke`，可以看到各个磁贴所述，而不是填充。

它还有可能以填充区域与平铺的位图，如本文中所示[ **SkiaSharp 位图平铺**](../effects/shaders/bitmap-tiling.md)。

## <a name="rounding-sharp-corners"></a>舍入尖角

**舍入 Heptagon**中提供程序[**绘制弧线的三个方法**](~/xamarin-forms/user-interface/graphics/skiasharp/curves/arcs.md)文章使用正切弧线曲线的点的七个面图。 **另一个舍入 Heptagon**页显示更简单的方法使用从创建一个路径效果[ `SKPathEffect.CreateCorner` ](xref:SkiaSharp.SKPathEffect.CreateCorner(System.Single))方法：

```csharp
public static SKPathEffect CreateCorner (Single radius)
```

尽管单个参数名为`radius`，必须将其设置为半个所需的圆角半径。 （这是基础 Skia 代码的特征）。

下面是`PaintSurface`处理程序中的[ `AnotherRoundedHeptagonPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/AnotherRoundedHeptagonPage.cs)类：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    int numVertices = 7;
    float radius = 0.45f * Math.Min(info.Width, info.Height);
    SKPoint[] vertices = new SKPoint[numVertices];
    double vertexAngle = -0.5f * Math.PI;       // straight up

    // Coordinates of the vertices of the polygon
    for (int vertex = 0; vertex < numVertices; vertex++)
    {
        vertices[vertex] = new SKPoint(radius * (float)Math.Cos(vertexAngle),
                                       radius * (float)Math.Sin(vertexAngle));
        vertexAngle += 2 * Math.PI / numVertices;
    }

    float cornerRadius = 100;

    // Create the path
    using (SKPath path = new SKPath())
    {
        path.AddPoly(vertices, true);

        // Render the path in the center of the screen
        using (SKPaint paint = new SKPaint())
        {
            paint.Style = SKPaintStyle.Stroke;
            paint.Color = SKColors.Blue;
            paint.StrokeWidth = 10;

            // Set argument to half the desired corner radius!
            paint.PathEffect = SKPathEffect.CreateCorner(cornerRadius / 2);

            canvas.Translate(info.Width / 2, info.Height / 2);
            canvas.DrawPath(path, paint);

            // Uncomment DrawCircle call to verify corner radius
            float offset = cornerRadius / (float)Math.Sin(Math.PI * (numVertices - 2) / numVertices / 2);
            paint.Color = SKColors.Green;
            // canvas.DrawCircle(vertices[0].X, vertices[0].Y + offset, cornerRadius, paint);
        }
    }
}
```

可以使用这种效果与描画或基于填充`Style`属性的`SKPaint`对象。 运行此处：

[![另一个圆形 Heptagon 页面的三向屏幕截图](effects-images/anotherroundedheptagon-small.png)](effects-images/anotherroundedheptagon-large.png#lightbox)

您将看到此圆角的 heptagon 等同于早期版本的程序。 如果需要更可信的圆角半径真正为 100 而不是 50 中指定`SKPathEffect.CreateCorner`调用时，您可以取消注释最后一条语句在程序就会看到一个 100 radius 圆角上叠加。

## <a name="random-jitter"></a>随机抖动

有时计算机图形中的完美直线不了您的需求，并且需要一些随机性。 在这种情况下，您将想要尝试[ `SKPathEffect.CreateDiscrete` ](xref:SkiaSharp.SKPathEffect.CreateDiscrete(System.Single,System.Single,System.UInt32))方法：

```csharp
public static SKPathEffect CreateDiscrete (Single segLength, Single deviation, UInt32 seedAssist)
```

用于描画或填充，可以使用此路径效果。 行分到多个已连接的网段 — 由指定的近似长度`segLength`— 和扩展在不同的方向。 指定的原始行偏离程度`deviation`。

最后一个参数是用于生成使用效果的伪随机序列的种子。 抖动效果看起来稍有不同的不同的种子。 自变量具有默认值为零，这意味着只要您运行该程序，效果都相同。 如果您希望不同抖动，每当重新绘制屏幕时，您可以设置为种子`Millisecond`属性的`DataTime.Now`值 （例如）。

**抖动试验**页面允许您试验描画矩形中的不同值：

[![JitterExperiment 页面的三向屏幕截图](effects-images/jitterexperiment-small.png)](effects-images/jitterexperiment-large.png#lightbox)

该程序非常简单。 [ **JitterExperimentPage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/JitterExperimentPage.xaml)文件实例化两个`Slider`元素和一个`SKCanvasView`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Curves.JitterExperimentPage"
             Title="Jitter Experiment">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="*" />
        </Grid.RowDefinitions>

        <Grid.Resources>
            <ResourceDictionary>
                <Style TargetType="Label">
                    <Setter Property="HorizontalTextAlignment" Value="Center" />
                </Style>

                <Style TargetType="Slider">
                    <Setter Property="Margin" Value="20, 0" />
                    <Setter Property="Minimum" Value="0" />
                    <Setter Property="Maximum" Value="100" />
                </Style>
            </ResourceDictionary>
        </Grid.Resources>

        <Slider x:Name="segLengthSlider"
                Grid.Row="0"
                ValueChanged="sliderValueChanged" />

        <Label Text="{Binding Source={x:Reference segLengthSlider},
                              Path=Value,
                              StringFormat='Segment Length = {0:F0}'}"
               Grid.Row="1" />

        <Slider x:Name="deviationSlider"
                Grid.Row="2"
                ValueChanged="sliderValueChanged" />

        <Label Text="{Binding Source={x:Reference deviationSlider},
                              Path=Value,
                              StringFormat='Deviation = {0:F0}'}"
               Grid.Row="3" />

        <skia:SKCanvasView x:Name="canvasView"
                           Grid.Row="4"
                           PaintSurface="OnCanvasViewPaintSurface" />
    </Grid>
</ContentPage>
```

`PaintSurface`处理程序中的[ **JitterExperimentPage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/JitterExperimentPage.xaml.cs)调用代码隐藏文件每当`Slider`值发生更改。 它将调用`SKPathEffect.CreateDiscrete`使用两个`Slider`值，并使用其绘制一个矩形：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    float segLength = (float)segLengthSlider.Value;
    float deviation = (float)deviationSlider.Value;

    using (SKPaint paint = new SKPaint())
    {
        paint.Style = SKPaintStyle.Stroke;
        paint.StrokeWidth = 5;
        paint.Color = SKColors.Blue;

        using (SKPathEffect pathEffect = SKPathEffect.CreateDiscrete(segLength, deviation))
        {
            paint.PathEffect = pathEffect;

            SKRect rect = new SKRect(100, 100, info.Width - 100, info.Height - 100);
            canvas.DrawRect(rect, paint);
        }
    }
}
```

您可以使用这种效果用于填充，填充区域的轮廓的这种情况下受到这些随机偏差。 **抖动文本**页演示了如何使用此路径效果显示的文本。 中的代码的大部分`PaintSurface`处理程序[ `JitterTextPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/JitterTextPage.cs)类专门致力于大小调整和居中文本：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    string text = "FUZZY";

    using (SKPaint textPaint = new SKPaint())
    {
        textPaint.Color = SKColors.Purple;
        textPaint.PathEffect = SKPathEffect.CreateDiscrete(3f, 10f);

        // Adjust TextSize property so text is 95% of screen width
        float textWidth = textPaint.MeasureText(text);
        textPaint.TextSize *= 0.95f * info.Width / textWidth;

        // Find the text bounds
        SKRect textBounds = new SKRect();
        textPaint.MeasureText(text, ref textBounds);

        // Calculate offsets to center the text on the screen
        float xText = info.Width / 2 - textBounds.MidX;
        float yText = info.Height / 2 - textBounds.MidY;

        canvas.DrawText(text, xText, yText, textPaint);
    }
}
```

此处它在横向模式下运行：

[![JitterText 页面的三向屏幕截图](effects-images/jittertext-small.png)](effects-images/jittertext-large.png#lightbox)

## <a name="path-outlining"></a>路径大纲显示

您所见的两个小示例[ `GetFillPath` ](xref:SkiaSharp.SKPaint.GetFillPath*)方法的`SKPaint`，其中存在两个版本：

```csharp
public Boolean GetFillPath (SKPath src, SKPath dst, Single resScale = 1)

public Boolean GetFillPath (SKPath src, SKPath dst, SKRect cullRect, Single resScale = 1)
```

只有前两个参数是必需的。 方法访问引用的路径`src`自变量，修改基于中的笔划属性的路径数据`SKPaint`对象 (包括`PathEffect`属性)，然后将结果转换为`dst`路径。 `resScale`参数允许的精度来创建较小的目标路径，减少和`cullRect`参数可以消除外部矩形轮廓。

此方法的一个基本用途根本不涉及路径效果：`SKPaintStyle.Stroke` `GetFillPath` `PathEffect`如果对象的属性设置为，并且没有其集，则创建一个路径，该路径表示源路径的轮廓，就像它已被`Style` `SKPaint`绘制属性。

例如，如果`src`路径是一个简单的圆形的半径 500，并`SKPaint`对象指定的笔划宽度为 100，则`dst`路径将成为一个半径为 550 一个半径为 450，另两个同心环。 调用的方法`GetFillPath`因为填充这`dst`路径是相同描画`src`路径。 但您还可以绘制笔画`dst`路径以查看路径轮廓。

**点击到大纲路径**此进行了演示。 `SKCanvasView`并`TapGestureRecognizer`进行实例化[ **TapToOutlineThePathPage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/TapToOutlineThePathPage.xaml)文件。 [ **TapToOutlineThePathPage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/TapToOutlineThePathPage.xaml.cs)代码分离文件定义了三个`SKPaint`对象作为字段，两个用于描画与描边宽度为 100 和 20 和第三个填充：

```csharp
public partial class TapToOutlineThePathPage : ContentPage
{
    bool outlineThePath = false;

    SKPaint redThickStroke = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Red,
        StrokeWidth = 100
    };

    SKPaint redThinStroke = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Red,
        StrokeWidth = 20
    };

    SKPaint blueFill = new SKPaint
    {
        Style = SKPaintStyle.Fill,
        Color = SKColors.Blue
    };

    public TapToOutlineThePathPage()
    {
        InitializeComponent();
    }

    void OnCanvasViewTapped(object sender, EventArgs args)
    {
        outlineThePath ^= true;
        (sender as SKCanvasView).InvalidateSurface();
    }
    ...
}
```

如果屏幕未被点击，`PaintSurface`处理程序使用`blueFill`和`redThickStroke`绘制对象以便呈现循环路径：

```csharp
public partial class TapToOutlineThePathPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPath circlePath = new SKPath())
        {
            circlePath.AddCircle(info.Width / 2, info.Height / 2,
                                 Math.Min(info.Width / 2, info.Height / 2) -
                                 redThickStroke.StrokeWidth);

            if (!outlineThePath)
            {
                canvas.DrawPath(circlePath, blueFill);
                canvas.DrawPath(circlePath, redThickStroke);
            }
            else
            {
                using (SKPath outlinePath = new SKPath())
                {
                    redThickStroke.GetFillPath(circlePath, outlinePath);

                    canvas.DrawPath(outlinePath, blueFill);
                    canvas.DrawPath(outlinePath, redThinStroke);
                }
            }
        }
    }
}
```

该圆形填充区域和描边按预期的方式：

[![正常点击以概述路径页面的三向屏幕截图](effects-images/taptooutlinethepathnormal-small.png)](effects-images/taptooutlinethepathnormal-large.png#lightbox)

点击屏幕上，当`outlineThePath`设置为`true`，并`PaintSurface`处理程序将创建全新`SKPath`对象，并将其用作对的调用中的目标路径`GetFillPath`上`redThickStroke`绘制对象。 然后填充区域和描边与该目标路径`redThinStroke`，这会导致在下面的示例：

[![边框点击以概述 "路径" 页的三向屏幕截图](effects-images/taptooutlinethepathoutlined-small.png)](effects-images/taptooutlinethepathoutlined-large.png#lightbox)

两个红色圆圈清楚地表示原始循环路径的已转换为两个循环分布图。

此方法可以是在开发用于路径中非常有用`SKPathEffect.Create1DPath`方法。 复制路径时，始终填充这些方法中指定的路径。 如果不想要填充的完整路径，则必须仔细定义轮廓。

例如，在**链接链**示例链接定义一系列的四个弧线，其中每个对基于两个半径概述要填充的路径的区域。 可以在中的代码替换为`LinkedChainPage`类来执行稍有不同。

首先，你将想要重新定义`linkRadius`常量：

```csharp
const float linkRadius = 27.5f;
const float linkThickness = 5;
```

`linkPath`现在只需要两个基于该单个 radius，与所需的弧线开始角度和扫描角度：

```csharp
using (SKPath linkPath = new SKPath())
{
    SKRect rect = new SKRect(-linkRadius, -linkRadius, linkRadius, linkRadius);
    linkPath.AddArc(rect, 55, 160);
    linkPath.AddArc(rect, 235, 160);

    using (SKPaint strokePaint = new SKPaint())
    {
        strokePaint.Style = SKPaintStyle.Stroke;
        strokePaint.StrokeWidth = linkThickness;

        using (SKPath outlinePath = new SKPath())
        {
            strokePaint.GetFillPath(linkPath, outlinePath);

            // Set that path as the 1D path effect for linksPaint
            linksPaint.PathEffect =
                SKPathEffect.Create1DPath(outlinePath, 1.3f * linkRadius, 0,
                                          SKPath1DPathEffectStyle.Rotate);

        }

    }
}
```

`outlinePath`对象，即边框的收件人`linkPath`描中指定的属性时`strokePaint`。

使用此方法的另一个示例接下来的一种方法中使用的路径。

## <a name="combining-path-effects"></a>组合路径效果

两种最终静态创建方法`SKPathEffect`都[ `SKPathEffect.CreateSum` ](xref:SkiaSharp.SKPathEffect.CreateSum(SkiaSharp.SKPathEffect,SkiaSharp.SKPathEffect))并[ `SKPathEffect.CreateCompose` ](xref:SkiaSharp.SKPathEffect.CreateCompose(SkiaSharp.SKPathEffect,SkiaSharp.SKPathEffect)):

```csharp
public static SKPathEffect CreateSum (SKPathEffect first, SKPathEffect second)

public static SKPathEffect CreateCompose (SKPathEffect outer, SKPathEffect inner)
```

这两种方法合并两个路径效果来创建复合路径效果。 `CreateSum`方法创建路径效果类似于分别应用的两个路径效果虽然`CreateCompose`应用一个路径效果 ( `inner`)，然后应用`outer`到的。

您已经看到如何`GetFillPath`方法`SKPaint`可以将一条路径转换为另一个路径基于`SKPaint`属性 (包括`PathEffect`)，不应将其*太*神秘方式`SKPaint`对象可以执行该操作两次，在指定的两个路径效果`CreateSum`或`CreateCompose`方法。

一个明显的用途`CreateSum`是定义`SKPaint`对象，它使用一条路径的效果，填充路径和描边与另一个路径效果的路径。 了这一点**框架中的猫**示例，显示带圆齿形边缘的范围内的猫数组：

[![框架页中猫的三向屏幕截图](effects-images/catsinframe-small.png)](effects-images/catsinframe-large.png#lightbox)

[ `CatsInFramePage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/CatsInFramePage.cs)类首先定义多个字段。 你可能会识别中的第一个字段[ `PathDataCatPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/PathDataCatPage.cs)类派生[ **SVG 路径数据**](~/xamarin-forms/user-interface/graphics/skiasharp/curves/path-data.md)一文。 第二个路径基于行和弧线的帧扇贝模式：

```csharp
public class CatsInFramePage : ContentPage
{
    // From PathDataCatPage.cs
    SKPath catPath = SKPath.ParseSvgPathData(
        "M 160 140 L 150 50 220 103" +              // Left ear
        "M 320 140 L 330 50 260 103" +              // Right ear
        "M 215 230 L 40 200" +                      // Left whiskers
        "M 215 240 L 40 240" +
        "M 215 250 L 40 280" +
        "M 265 230 L 440 200" +                     // Right whiskers
        "M 265 240 L 440 240" +
        "M 265 250 L 440 280" +
        "M 240 100" +                               // Head
        "A 100 100 0 0 1 240 300" +
        "A 100 100 0 0 1 240 100 Z" +
        "M 180 170" +                               // Left eye
        "A 40 40 0 0 1 220 170" +
        "A 40 40 0 0 1 180 170 Z" +
        "M 300 170" +                               // Right eye
        "A 40 40 0 0 1 260 170" +
        "A 40 40 0 0 1 300 170 Z");

    SKPaint catStroke = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        StrokeWidth = 5
    };

    SKPath scallopPath =
        SKPath.ParseSvgPathData("M 0 0 L 50 0 A 60 60 0 0 1 -50 0 Z");

    SKPaint framePaint = new SKPaint
    {
        Color = SKColors.Black
    };
    ...
}
```

`catPath`中可以使用`SKPathEffect.Create2DPath`方法如果`SKPaint`对象`Style`属性设置为`Stroke`。 但是，如果`catPath`直接在此程序中，然后整个猫头中将填充，并须线甚至不会显示使用。 （试试看 ！）需要获取该路径的轮廓和使用中的大纲`SKPathEffect.Create2DPath`方法。

构造函数完成此作业。 它会先应用到两个转换`catPath`移动 （0，0） 到中心点和缩小规模的大小。 `GetFillPath` 获取的分布图中的所有大纲`outlinedCatPath`，并且在使用该对象`SKPathEffect.Create2DPath`调用。 的缩放因素`SKMatrix`值是稍大于水平和垂直猫提供磁贴，虽然转换因子之间的小缓冲区大小派生的某种程度上根据经验，以便完全 cat 中是可见的左上角的帧：

```csharp
public class CatsInFramePage : ContentPage
{
    ...
    public CatsInFramePage()
    {
        Title = "Cats in Frame";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;

        // Move (0, 0) point to center of cat path
        catPath.Transform(SKMatrix.MakeTranslation(-240, -175));

        // Now catPath is 400 by 250
        // Scale it down to 160 by 100
        catPath.Transform(SKMatrix.MakeScale(0.40f, 0.40f));

        // Get the outlines of the contours of the cat path
        SKPath outlinedCatPath = new SKPath();
        catStroke.GetFillPath(catPath, outlinedCatPath);

        // Create a 2D path effect from those outlines
        SKPathEffect fillEffect = SKPathEffect.Create2DPath(
            new SKMatrix { ScaleX = 170, ScaleY = 110,
                           TransX = 75, TransY = 80,
                           Persp2 = 1 },
            outlinedCatPath);

        // Create a 1D path effect from the scallop path
        SKPathEffect strokeEffect =
            SKPathEffect.Create1DPath(scallopPath, 75, 0, SKPath1DPathEffectStyle.Rotate);

        // Set the sum the effects to frame paint
        framePaint.PathEffect = SKPathEffect.CreateSum(fillEffect, strokeEffect);
    }
    ...
}
```

然后，构造函数调用`SKPathEffect.Create1DPath`圆齿形帧。 请注意，路径的宽度为 100 个像素，但提前为 75 像素，以便复制的路径重叠在框架四周。 构造函数调用的最后一个语句`SKPathEffect.CreateSum`合并两个路径效果并将结果设置为`SKPaint`对象。

所有这些工作允许`PaintSurface`处理程序是非常简单。 它只需定义一个矩形，并使其使用`framePaint`:

```csharp
public class CatsInFramePage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        SKRect rect = new SKRect(50, 50, info.Width - 50, info.Height - 50);
        canvas.ClipRect(rect);
        canvas.DrawRect(rect, framePaint);
    }
}
```

路径效果背后的算法始终会导致用于描画或填充来显示，这可能导致一些视觉对象显示该矩形之外的整个路径。 `ClipRect`之前调用`DrawRect`调用允许为得更简洁的视觉对象。 （就可以试试不经剪辑 ！）

它是通常使用`SKPathEffect.CreateCompose`将一些抖动添加到另一个路径效果。 您当然可以体验靠您自己，但下面是一个略有不同的示例：

**虚线阴影线条**用虚线表示的阴影线条填充椭圆。 中的工作的大部分[ `DashedHatchLinesPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/DashedHatchLinesPage.cs)执行类直接在字段定义中的。 这些字段定义短划线效果和阴影效果。 它们定义为`static`因为中然后引用它们`SKPathEffect.CreateCompose`调用中`SKPaint`定义：

```csharp
public class DashedHatchLinesPage : ContentPage
{
    static SKPathEffect dashEffect =
        SKPathEffect.CreateDash(new float[] { 30, 30 }, 0);

    static SKPathEffect hatchEffect = SKPathEffect.Create2DLine(20,
        Multiply(SKMatrix.MakeScale(60, 60),
                 SKMatrix.MakeRotationDegrees(45)));

    SKPaint paint = new SKPaint()
    {
        PathEffect = SKPathEffect.CreateCompose(dashEffect, hatchEffect),
        StrokeCap = SKStrokeCap.Round,
        Color = SKColors.Blue
    };
    ...
    static SKMatrix Multiply(SKMatrix first, SKMatrix second)
    {
        SKMatrix target = SKMatrix.MakeIdentity();
        SKMatrix.Concat(ref target, first, second);
        return target;
    }
}
```

`PaintSurface`处理程序需要包含仅标准开销加上一次调用`DrawOval`:

```csharp
public class DashedHatchLinesPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        canvas.DrawOval(info.Width / 2, info.Height / 2,
                        0.45f * info.Width, 0.45f * info.Height,
                        paint);
    }
    ...
}
```

如您所发现，阴影线不是精确地限制为内部的区域，并在此示例中，它们始终以在整个短划线左侧：

[![虚线影线线条页面的三向屏幕截图](effects-images/dashedhatchlines-small.png)](effects-images/dashedhatchlines-large.png#lightbox)

现在，您所见，范围从简单的点和短划线到奇怪的组合路径效果，使用您的想象力，并查看可以创建。

## <a name="related-links"></a>相关链接

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
