---
title: SkiaSharp 线性渐变
description: 了解如何绘制笔画行或使用渐变填充区域组成的两种颜色渐变的混合。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 20A2A8C4-FEB7-478D-BF57-C92E26117B6A
author: davidbritch
ms.author: dabritch
ms.date: 08/23/2018
ms.openlocfilehash: 43a427b4eca174dce4af47bb43cb1a048a6cfe7c
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2019
ms.locfileid: "68647678"
---
# <a name="the-skiasharp-linear-gradient"></a>SkiaSharp 线性渐变

[![下载示例](~/media/shared/download.png)下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

[ `SKPaint` ](xref:SkiaSharp.SKPaint)类定义[ `Color` ](xref:SkiaSharp.SKPaint.Color)属性，用于绘制行或使用纯色填充区域。 或者可以绘制线条或填充的区域_渐变_，哪些是逐步混合的颜色：

![线性渐变示例](linear-gradient-images/LinearGradientSample.png "线性渐变示例")

最基本的渐变类型是_线性_渐变。 混合的颜色发生在第行 (称为_渐变线_) 从一个点到另一个。 垂直渐变线的行具有相同的颜色。 创建线性渐变使用两个静态之一[ `SKShader.CreateLinearGradient` ](xref:SkiaSharp.SKShader.CreateLinearGradient*)方法。 两个重载之间的区别是一个包括矩阵转换，另一个不。 

这些方法返回类型的对象[ `SKShader` ](xref:SkiaSharp.SKShader)设置为[ `Shader` ](xref:SkiaSharp.SKPaint.Shader)属性`SKPaint`。 如果`Shader`属性为非 null，它将替代`Color`属性。 为描边的任何行或任何所填充区域的使用此`SKPaint`对象所基于的渐变而不是纯的颜色。

> [!NOTE]
> `Shader`当包括时，将忽略属性`SKPaint`对象中`DrawBitmap`调用。 可以使用`Color`的属性`SKPaint`若要设置显示位图的透明度级别 (如本文所述[显示 SkiaSharp 位图](../../bitmaps/displaying.md#displaying-in-pixel-dimensions))，但不能使用`Shader`用于显示属性具有渐变透明度位图。 其他方法可用于显示具有渐变透明度的位图:这些文章在[SkiaSharp 循环渐变](circular-gradients.md#radial-gradients-for-masking)和[SkiaSharp 合成和 blend 模式](../blend-modes/porter-duff.md#gradient-transparency-and-transitions)一文中进行了介绍。

## <a name="corner-to-corner-gradients"></a>角角渐变

通常的线性渐变从一个矩形的角扩展到另一个。 如果的起点是矩形的左上角，可以扩展渐变：

- 沿垂直方向与左下角
- 沿水平方向与右上角
- 到右下角沿对角线方向

对角线方向线性渐变中的第一页中进行演示**SkiaSharp 着色器和其他效果**一部分[ **SkiaSharpFormsDemos** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)示例。 **角角渐变**页创建`SKCanvasView`其构造函数中。 `PaintSurface`处理程序将创建`SKPaint`对象中`using`语句，然后定义画布中居中的 300 像素正方形矩形：

```csharp
public class CornerToCornerGradientPage : ContentPage
{
    ···
    public CornerToCornerGradientPage ()
    {
        Title = "Corner-to-Corner Gradient";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
        ···
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPaint paint = new SKPaint())
        {
            // Create 300-pixel square centered rectangle
            float x = (info.Width - 300) / 2;
            float y = (info.Height - 300) / 2;
            SKRect rect = new SKRect(x, y, x + 300, y + 300);

            // Create linear gradient from upper-left to lower-right
            paint.Shader = SKShader.CreateLinearGradient(
                                new SKPoint(rect.Left, rect.Top),
                                new SKPoint(rect.Right, rect.Bottom),
                                new SKColor[] { SKColors.Red, SKColors.Blue },
                                new float[] { 0, 1 },
                                SKShaderTileMode.Repeat);

            // Draw the gradient on the rectangle
            canvas.DrawRect(rect, paint);
            ···
        }
    }
}
```

`Shader`的属性`SKPaint`分配`SKShader`返回值从静态`SKShader.CreateLinearGradient`方法。 五个参数是按如下所示：

- 矩形的左上角，此处设置的渐变的开始点
- 渐变的终点的矩形的右下角，此处设置
- 两个或多个颜色渐变构成的数组
- 一个数组`float`值，该值指示在渐变线的颜色的相对位置
- 成员[ `SKShaderTileMode` ](xref:SkiaSharp.SKShaderTileMode)枚举，指示渐变超出渐变线末端的行为方式

创建渐变对象后，`DrawRect`方法绘制 300 像素的方形矩形使用`SKPaint`包括着色器的对象。 此处它在 iOS、 Android 和通用 Windows 平台 (UWP) 上运行：

[![角角渐变](linear-gradient-images/CornerToCornerGradient.png "角角渐变")](linear-gradient-images/CornerToCornerGradient-Large.png#lightbox)

渐变线作为前两个参数指定的两个点定义。 请注意，这些点均相对于_画布_并_不_到显示带有渐变的图形对象。 沿渐变线颜色逐渐转换从左上角为蓝色在右下角的红色。 垂直渐变线的任何行具有常量颜色。

数组`float`值指定为第四个参数具有一对一的对应关系使用的颜色数组。 这些值表示发生这些颜色沿渐变线的相对位置。 在这里，0 意味着`Red`渐变线开始时发生 1 表示`Blue`行结束时发生。 数字必须升序排序，并应在 0 到 1 范围内。 如果它们不在该范围内，它们将调整为在该范围内。

非 0 和 1，可以为内容设置数组中的两个值。 试运行以下代码：

```csharp
new float[] { 0.25f, 0.75f }
```

渐变线的整个第一季度现在是纯红色，并最后一个季度是纯蓝色。 红色和蓝色的组合被限制为渐变线的中心部分。

通常情况下，你将想要空间这些位置值同样从 0 到 1。 如果是这样，您可以只需提供`null`的第四个参数作为`CreateLinearGradient`。

尽管两个 300 像素正方形矩形的角之间定义了此渐变，但不限于填充该矩形。 **角角渐变**页面包括一些额外的代码的响应点击或单击网页上的鼠标。 `drawBackground`之间切换字段`true`和`false`与每次点击。 如果值为`true`，则`PaintSurface`处理程序使用相同`SKPaint`对象以填充整个画布，然后绘制黑色的矩形，该值指示较小的矩形： 

```csharp
public class CornerToCornerGradientPage : ContentPage
{
    bool drawBackground;

    public CornerToCornerGradientPage ()
    {
        ···
        TapGestureRecognizer tap = new TapGestureRecognizer();
        tap.Tapped += (sender, args) =>
        {
            drawBackground ^= true;
            canvasView.InvalidateSurface();
        };
        canvasView.GestureRecognizers.Add(tap);
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        ···
        using (SKPaint paint = new SKPaint())
        {
            ···
            if (drawBackground)
            {
                // Draw the gradient on the whole canvas
                canvas.DrawRect(info.Rect, paint);

                // Outline the smaller rectangle
                paint.Shader = null;
                paint.Style = SKPaintStyle.Stroke;
                paint.Color = SKColors.Black;
                canvas.DrawRect(rect, paint);
            }
        }
    }
}
```

下面是点击屏幕后将看到什么：

[![角角渐变完整](linear-gradient-images/CornerToCornerGradientFull.png "角角渐变完整")](linear-gradient-images/CornerToCornerGradientFull-Large.png#lightbox)

请注意，渐变中都会重复相同的模式之外定义渐变线的点。 此重复出现的原因的最后一个参数`CreateLinearGradient`是`SKShaderTileMode.Repeat`。 （稍后您将看到其他选项。）

另请注意，用于指定渐变线的点并不唯一。 垂直渐变线的行具有相同的颜色，因此有无限数目的可以为相同的效果指定的渐变线。 例如时使用的水平渐变填充矩形，, 您可以指定的左上角和右上角，或的左下角和右下角或甚至与和并行这些行的任何两个点。

## <a name="interactively-experiment"></a>以交互方式试验

你可以以交互方式试验使用线性渐变**交互式线性渐变**页。 使用此页`InteractivePage`一文中引入类[**绘制弧线的三种方式**](../../curves/arcs.md)。`InteractivePage`句柄[ `TouchEffect` ](~/xamarin-forms/app-fundamentals/effects/touch-tracking.md)要维护一系列事件`TouchPoint`可以用手指或鼠标移动的对象。

XAML 文件将附加`TouchEffect`到的父`SKCanvasView`并且还包括`Picker`一个可选择的三个成员之一[ `SKShaderTileMode` ](xref:SkiaSharp.SKShaderTileMode)枚举：

```xaml
<local:InteractivePage xmlns="http://xamarin.com/schemas/2014/forms"
                       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                       xmlns:local="clr-namespace:SkiaSharpFormsDemos"
                       xmlns:skia="clr-namespace:SkiaSharp;assembly=SkiaSharp"
                       xmlns:skiaforms="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
                       xmlns:tt="clr-namespace:TouchTracking"
                       x:Class="SkiaSharpFormsDemos.Effects.InteractiveLinearGradientPage"
                       Title="Interactive Linear Gradient">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="*" />
            <RowDefinition Height="Auto" />
        </Grid.RowDefinitions>

        <Grid BackgroundColor="White"
              Grid.Row="0">
            <skiaforms:SKCanvasView x:Name="canvasView"
                                    PaintSurface="OnCanvasViewPaintSurface" />
            <Grid.Effects>
                <tt:TouchEffect Capture="True"
                                TouchAction="OnTouchEffectAction" />
            </Grid.Effects>
        </Grid>

        <Picker x:Name="tileModePicker" 
                Grid.Row="1"
                Title="Shader Tile Mode" 
                Margin="10"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.ItemsSource>
                <x:Array Type="{x:Type skia:SKShaderTileMode}">
                    <x:Static Member="skia:SKShaderTileMode.Clamp" />
                    <x:Static Member="skia:SKShaderTileMode.Repeat" />
                    <x:Static Member="skia:SKShaderTileMode.Mirror" />
                </x:Array>
            </Picker.ItemsSource>

            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>
    </Grid>
</local:InteractivePage>
```

代码隐藏文件中的构造函数创建两个`TouchPoint`起点和终点的线性渐变的对象。 `PaintSurface`处理程序定义 （适用于从红色到绿色为蓝色的渐变） 的三种颜色的数组，并获取当前`SKShaderTileMode`从`Picker`:

```csharp
public partial class InteractiveLinearGradientPage : InteractivePage
{
    public InteractiveLinearGradientPage ()
    {
        InitializeComponent ();

        touchPoints = new TouchPoint[2];

        for (int i = 0; i < 2; i++)
        { 
            touchPoints[i] = new TouchPoint
            {
                Center = new SKPoint(100 + i * 200, 100 + i * 200)
            };
        }

        InitializeComponent();
        baseCanvasView = canvasView;
    }

    void OnPickerSelectedIndexChanged(object sender, EventArgs args)
    {
        canvasView.InvalidateSurface();
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        SKColor[] colors = { SKColors.Red, SKColors.Green, SKColors.Blue };
        SKShaderTileMode tileMode =
            (SKShaderTileMode)(tileModePicker.SelectedIndex == -1 ?
                                        0 : tileModePicker.SelectedItem);

        using (SKPaint paint = new SKPaint())
        {
            paint.Shader = SKShader.CreateLinearGradient(touchPoints[0].Center,
                                                         touchPoints[1].Center,
                                                         colors,
                                                         null,
                                                         tileMode);
            canvas.DrawRect(info.Rect, paint);
        }
        ···
    }
}
```

`PaintSurface`处理程序将创建`SKShader`对象中的所有该信息，并使用它来颜色整个画布。 数组`float`的值设置为`null`。 否则，以同样空间三种颜色，会将该参数设置为 0、 0.5 和 1 的值的数组。

大部分`PaintSurface`处理程序专用于显示多个对象： 触摸点作为边框圈、 渐变线和垂直于接触点的渐变线的行：

```csharp
public partial class InteractiveLinearGradientPage : InteractivePage
{
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        ···
        // Display the touch points here rather than by TouchPoint
        using (SKPaint paint = new SKPaint())
        {
            paint.Style = SKPaintStyle.Stroke;
            paint.Color = SKColors.Black;
            paint.StrokeWidth = 3;

            foreach (TouchPoint touchPoint in touchPoints)
            {
                canvas.DrawCircle(touchPoint.Center, touchPoint.Radius, paint);
            }

            // Draw gradient line connecting touchpoints
            canvas.DrawLine(touchPoints[0].Center, touchPoints[1].Center, paint);

            // Draw lines perpendicular to the gradient line
            SKPoint vector = touchPoints[1].Center - touchPoints[0].Center;
            float length = (float)Math.Sqrt(Math.Pow(vector.X, 2) +
                                            Math.Pow(vector.Y, 2));
            vector.X /= length;
            vector.Y /= length;
            SKPoint rotate90 = new SKPoint(-vector.Y, vector.X);
            rotate90.X *= 200;
            rotate90.Y *= 200;

            canvas.DrawLine(touchPoints[0].Center, 
                            touchPoints[0].Center + rotate90, 
                            paint);

            canvas.DrawLine(touchPoints[0].Center,
                            touchPoints[0].Center - rotate90,
                            paint);

            canvas.DrawLine(touchPoints[1].Center,
                            touchPoints[1].Center + rotate90,
                            paint);

            canvas.DrawLine(touchPoints[1].Center,
                            touchPoints[1].Center - rotate90,
                            paint);
        }
    }
}
```

连接两个接触点的渐变线是轻松地绘制，但垂直行需要更多工作。 渐变线是转换为一个向量，规范化一个单位的长度，然后旋转 90 度。 该向量随后将授予的长度为 200 像素。 它用于绘制从触摸点为垂直渐变线于扩展的四行。

垂直的行的开头和结尾的渐变与一致。 超出这行代码会发生什么情况取决于设置的`SKShaderTileMode`枚举：

[![交互式线性渐变](linear-gradient-images/InteractiveLinearGradient.png "交互式线性渐变")](linear-gradient-images/InteractiveLinearGradient-Large.png#lightbox)

三个屏幕截图显示了三个不同的值的结果[ `SKShaderTileMode` ](xref:SkiaSharp.SKShaderTileMode)。 IOS 屏幕截图显示了`SKShaderTileMode.Clamp`，只需扩展了上边框的渐变的颜色。 `SKShaderTileMode.Repeat`选项在 Android 屏幕截图显示如何重复渐变模式。 `SKShaderTileMode.Mirror` UWP 的屏幕截图中的选项还重复模式，但是模式相反每次，从而导致没有颜色不连续性。

## <a name="gradients-on-gradients"></a>在渐变的渐变

`SKShader`类定义没有公共属性或方法除`Dispose`。 `SKShader`对象其静态方法创建的因此是固定不变。 即使为两个不同的对象使用的是同一渐变，很可能您需要略有不同渐变。 为此，你将需要创建一个新`SKShader`对象。

**渐变文本**页显示的文本和后台同时使用类似的渐变着色：

[![渐变文本](linear-gradient-images/GradientText.png "渐变文本")](linear-gradient-images/GradientText-Large.png#lightbox)

在渐变中的唯一差别是起点和终点。 用于显示文本使用的渐变基于文本的边框圆角的两个点。 有关背景信息，两个点基于整个画布。 下面是代码：

```csharp
public class GradientTextPage : ContentPage
{
    const string TEXT = "GRADIENT";

    public GradientTextPage ()
    {
        Title = "Gradient Text";

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
            // Create gradient for background
            paint.Shader = SKShader.CreateLinearGradient(
                                new SKPoint(0, 0),
                                new SKPoint(info.Width, info.Height),
                                new SKColor[] { new SKColor(0x40, 0x40, 0x40),
                                                new SKColor(0xC0, 0xC0, 0xC0) },
                                null,
                                SKShaderTileMode.Clamp);

            // Draw background
            canvas.DrawRect(info.Rect, paint);

            // Set TextSize to fill 90% of width
            paint.TextSize = 100;
            float width = paint.MeasureText(TEXT);
            float scale = 0.9f * info.Width / width;
            paint.TextSize *= scale;

            // Get text bounds
            SKRect textBounds = new SKRect();
            paint.MeasureText(TEXT, ref textBounds);

            // Calculate offsets to center the text on the screen
            float xText = info.Width / 2 - textBounds.MidX;
            float yText = info.Height / 2 - textBounds.MidY;

            // Shift textBounds by that amount
            textBounds.Offset(xText, yText);

            // Create gradient for text
            paint.Shader = SKShader.CreateLinearGradient(
                                new SKPoint(textBounds.Left, textBounds.Top),
                                new SKPoint(textBounds.Right, textBounds.Bottom),
                                new SKColor[] { new SKColor(0x40, 0x40, 0x40),
                                                new SKColor(0xC0, 0xC0, 0xC0) },
                                null,
                                SKShaderTileMode.Clamp);

            // Draw text
            canvas.DrawText(TEXT, xText, yText, paint);
        }
    }
}
```

`Shader`属性的`SKPaint`对象首次设置，以显示来覆盖背景的渐变。 渐变点设置为在画布的左上角和右下角。

该代码设置`TextSize`属性的`SKPaint`对象，以便在画布的宽度的 90%时显示的文本。 使用文本边界来计算`xText`并`yText`值要传递给`DrawText`方法使文本居中。

但是，第二个点渐变`CreateLinearGradient`调用必须引用时显示的文本相对于画布的左上角和右下角。 这通过转移来实现`textBounds`由同一个矩形`xText`和`yText`值：

```csharp
textBounds.Offset(xText, yText);
```

现在该矩形的左上角和右下角可以用于设置起点和终点的渐变。

## <a name="animating-a-gradient"></a>对渐变进行动画处理

有几种方法进行动画处理渐变。 一种方法是对起点和终点进行动画处理。 **渐变动画**页移动两个点居中的圈在画布上。 此圆的半径为一半宽度或高度的画布，两者中较小。 起点和终点都彼此相对上此圆和渐变转从白色到黑色与`Mirror`磁贴模式：

[![渐变动画](linear-gradient-images/GradientAnimation.png "渐变动画")](linear-gradient-images/GradientAnimation-Large.png#lightbox)

该构造函数创建`SKCanvasView`。 `OnAppearing`和`OnDisappearing`方法处理的动画逻辑：

```csharp
public class GradientAnimationPage : ContentPage
{
    SKCanvasView canvasView;
    bool isAnimating;
    double angle;
    Stopwatch stopwatch = new Stopwatch();

    public GradientAnimationPage()
    {
        Title = "Gradient Animation";

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
        const int duration = 3000;
        angle = 2 * Math.PI * (stopwatch.ElapsedMilliseconds % duration) / duration;
        canvasView.InvalidateSurface();

        return isAnimating;
    }
    ···
}
```

`OnTimerTick`方法计算`angle`以动画形式从 0 到 2 π 每隔 3 秒的值。 

下面是一种方法来计算两个渐变点。 `SKPoint`名为值`vector`计算从画布的中心扩展到圆角半径上的点。 此向量的方向取决于表示的角度的正弦和余弦值。 然后计算两个相反的渐变点:通过从中心点减去向量来计算一个点, 并通过将矢量添加到中心点来计算其他点:

```csharp
public class GradientAnimationPage : ContentPage
{
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPaint paint = new SKPaint())
        {
            SKPoint center = new SKPoint(info.Rect.MidX, info.Rect.MidY);
            int radius = Math.Min(info.Width, info.Height) / 2;
            SKPoint vector = new SKPoint((float)(radius * Math.Cos(angle)),
                                         (float)(radius * Math.Sin(angle)));

            paint.Shader = SKShader.CreateLinearGradient(
                                center - vector,
                                center + vector,
                                new SKColor[] { SKColors.White, SKColors.Black },
                                null,
                                SKShaderTileMode.Mirror);

            canvas.DrawRect(info.Rect, paint);
        }
    }
}
```

稍有不同的方法要求更少的代码。 此方法利用了[ `SKShader.CreateLinearGradient` ](xref:SkiaSharp.SKShader.CreateLinearGradient(SkiaSharp.SKPoint,SkiaSharp.SKPoint,SkiaSharp.SKColor[],System.Single[],SkiaSharp.SKShaderTileMode,SkiaSharp.SKMatrix))重载矩阵转换为的最后一个参数的方法。 这种方法是在版本[ **SkiaSharpFormsDemos** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)示例：

```csharp
public class GradientAnimationPage : ContentPage
{
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPaint paint = new SKPaint())
        {
            paint.Shader = SKShader.CreateLinearGradient(
                                new SKPoint(0, 0),
                                info.Width < info.Height ? new SKPoint(info.Width, 0) : 
                                                           new SKPoint(0, info.Height),
                                new SKColor[] { SKColors.White, SKColors.Black },
                                new float[] { 0, 1 },
                                SKShaderTileMode.Mirror,
                                SKMatrix.MakeRotation((float)angle, info.Rect.MidX, info.Rect.MidY));

            canvas.DrawRect(info.Rect, paint);
        }
    }
}
```

如果画布的宽度小于高度，则两个渐变点设置为 （0，0） 和 (`info.Width`，0)。 旋转转换的最后一个参数作为传递`CreateLinearGradient`有效地旋转中心周围屏幕的这两个点。

请注意，如果表示的角度为 0，则没有旋转，两个渐变点是在画布的左上角和右上角。 这些点不是计算中前面所示的相同渐变点`CreateLinearGradient`调用。 这些点，但是_并行_到水平渐变线的 bisects 在画布的中心，它们会导致相同的渐变。

**出喷薄彩虹渐变**

**彩虹渐变**页绘制出喷薄彩虹从画布的左上角到右下角。 但此彩虹渐变不是真正出喷薄彩虹等。 它是直线，而不是平的但基于由循环色调值从 0 到 360 之间的八个 HSL （色调-饱和度-亮度） 颜色：

```csharp
SKColor[] colors = new SKColor[8];

for (int i = 0; i < colors.Length; i++)
{
    colors[i] = SKColor.FromHsl(i * 360f / (colors.Length - 1), 100, 50);
}
```

代码是的一部分`PaintSurface`处理程序如下所示。 该处理程序首先创建定义扩展从画布的左上角到右下角的六面多边形的路径：

```csharp
public class RainbowGradientPage : ContentPage
{
    public RainbowGradientPage ()
    {
        Title = "Rainbow Gradient";

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

        using (SKPath path = new SKPath())
        {
            float rainbowWidth = Math.Min(info.Width, info.Height) / 2f;

            // Create path from upper-left to lower-right corner
            path.MoveTo(0, 0);
            path.LineTo(rainbowWidth / 2, 0);
            path.LineTo(info.Width, info.Height - rainbowWidth / 2);
            path.LineTo(info.Width, info.Height);
            path.LineTo(info.Width - rainbowWidth / 2, info.Height);
            path.LineTo(0, rainbowWidth / 2);
            path.Close();

            using (SKPaint paint = new SKPaint())
            {
                SKColor[] colors = new SKColor[8];

                for (int i = 0; i < colors.Length; i++)
                {
                    colors[i] = SKColor.FromHsl(i * 360f / (colors.Length - 1), 100, 50);
                }

                paint.Shader = SKShader.CreateLinearGradient(
                                    new SKPoint(0, rainbowWidth / 2), 
                                    new SKPoint(rainbowWidth / 2, 0),
                                    colors,
                                    null,
                                    SKShaderTileMode.Repeat);

                canvas.DrawPath(path, paint);
            }
        }
    }
}
```

`CreateLinearGradient`方法中的两个渐变点基于定义此路径的两个点:两个点接近左上角。 第一种是在画布上边缘和第二个是画布的左边缘。 下面是结果：

[![有故障的彩虹渐变](linear-gradient-images/RainbowGradientFaulty.png "出喷薄彩虹渐变故障")](linear-gradient-images/RainbowGradientFaulty-Large.png#lightbox)

这是一个有趣的映像，但并不相当的意图。 问题在于，当创建线性渐变，常量颜色的行被垂直于渐变线。 渐变线基于其中图涉及顶部和左侧边，并且该行通常不是将扩展到右下角的图边缘与垂直的点。 仅当在画布已方形，此方法将适用。

若要创建正确的彩虹渐变，渐变线必须垂直出喷薄彩虹的边缘。 这是一个更复杂的计算。 一个向量，必须定义与长边的图并行。 矢量是旋转 90 度，以便与该端垂直。 然后延长为图的宽度乘以`rainbowWidth`。 两个渐变点是根据上图中的一侧的点以及计算以及向量的点。 以下是中显示的代码**彩虹渐变**页面[ **SkiaSharpFormsDemos** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)示例：

```csharp
public class RainbowGradientPage : ContentPage
{
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        ···
        using (SKPath path = new SKPath())
        {
            ···
            using (SKPaint paint = new SKPaint())
            {
                ···
                // Vector on lower-left edge, from top to bottom 
                SKPoint edgeVector = new SKPoint(info.Width - rainbowWidth / 2, info.Height) - 
                                     new SKPoint(0, rainbowWidth / 2);

                // Rotate 90 degrees counter-clockwise:
                SKPoint gradientVector = new SKPoint(edgeVector.Y, -edgeVector.X);

                // Normalize
                float length = (float)Math.Sqrt(Math.Pow(gradientVector.X, 2) +
                                                Math.Pow(gradientVector.Y, 2));
                gradientVector.X /= length;
                gradientVector.Y /= length;

                // Make it the width of the rainbow
                gradientVector.X *= rainbowWidth;
                gradientVector.Y *= rainbowWidth;

                // Calculate the two points
                SKPoint point1 = new SKPoint(0, rainbowWidth / 2);
                SKPoint point2 = point1 + gradientVector;

                paint.Shader = SKShader.CreateLinearGradient(point1,
                                                             point2,
                                                             colors,
                                                             null,
                                                             SKShaderTileMode.Repeat);

                canvas.DrawPath(path, paint);
            }
        }
    }
}
```

现在出喷薄彩虹颜色与图一致：

[![出喷薄彩虹渐变](linear-gradient-images/RainbowGradient.png "出喷薄彩虹渐变")](linear-gradient-images/RainbowGradient-Large.png#lightbox)

**无穷大颜色**

中还使用 rainbow 渐变**无穷大颜色**页。 此页绘制使用本文所述的路径对象无穷符号[**三种类型的贝塞尔曲线**](../../curves/beziers.md#bezier-curve-approximation-to-circular-arcs)。 然后以与持续扫描在图像动画出喷薄彩虹渐变着色图像。

该构造函数创建`SKPath`描述无穷符号对象。 创建路径后，构造函数还可以获取路径的矩形边界。 它然后计算名为的值`gradientCycleLength`。 如果渐变基于的左上角和右下角`pathBounds`矩形，这`gradientCycleLength`值是渐变模式的总水平宽度：

```csharp
public class InfinityColorsPage : ContentPage
{
    ···
    SKCanvasView canvasView;

    // Path information 
    SKPath infinityPath;
    SKRect pathBounds;
    float gradientCycleLength;

    // Gradient information
    SKColor[] colors = new SKColor[8];
    ···

    public InfinityColorsPage ()
    {
        Title = "Infinity Colors";

        // Create path for infinity sign
        infinityPath = new SKPath();
        infinityPath.MoveTo(0, 0);                                  // Center
        infinityPath.CubicTo(  50,  -50,   95, -100,  150, -100);   // To top of right loop
        infinityPath.CubicTo( 205, -100,  250,  -55,  250,    0);   // To far right of right loop
        infinityPath.CubicTo( 250,   55,  205,  100,  150,  100);   // To bottom of right loop
        infinityPath.CubicTo(  95,  100,   50,   50,    0,    0);   // Back to center  
        infinityPath.CubicTo( -50,  -50,  -95, -100, -150, -100);   // To top of left loop
        infinityPath.CubicTo(-205, -100, -250,  -55, -250,    0);   // To far left of left loop
        infinityPath.CubicTo(-250,   55, -205,  100, -150,  100);   // To bottom of left loop
        infinityPath.CubicTo( -95,  100, - 50,   50,    0,    0);   // Back to center
        infinityPath.Close();

        // Calculate path information 
        pathBounds = infinityPath.Bounds;
        gradientCycleLength = pathBounds.Width +
            pathBounds.Height * pathBounds.Height / pathBounds.Width;

        // Create SKColor array for gradient
        for (int i = 0; i < colors.Length; i++)
        {
            colors[i] = SKColor.FromHsl(i * 360f / (colors.Length - 1), 100, 50);
        }

        canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }
    ···
}
```

构造函数还会创建`colors`彩虹的数组和`SKCanvasView`对象。

重写`OnAppearing`和`OnDisappearing`方法动画执行开销。 `OnTimerTick`方法进行动画处理`offset`字段从 0 到`gradientCycleLength`每两秒：

```csharp
public class InfinityColorsPage : ContentPage
{
    ···
    // For animation
    bool isAnimating;
    float offset;
    Stopwatch stopwatch = new Stopwatch();
    ···

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
        const int duration = 2;     // seconds
        double progress = stopwatch.Elapsed.TotalSeconds % duration / duration;
        offset = (float)(gradientCycleLength * progress);
        canvasView.InvalidateSurface();

        return isAnimating;
    }
    ···
}
```

最后，`PaintSurface`处理程序呈现无穷符号。 因为该路径包含负数和正数围绕中心点的坐标 （0，0），`Translate`使用画布上的转换移动到中心。 翻译转换后跟`Scale`应用一个比例因子，使无穷符号时仍可实现 95%的宽度和高度的画布中一样大的转换。 

请注意，`STROKE_WIDTH`常量添加到的宽度和高度的边界矩形的路径。 因此呈现的无穷大大小的大小增加了所有四个边的宽度的一半，路径将描边与此宽度的行：

```csharp
public class InfinityColorsPage : ContentPage
{
    const int STROKE_WIDTH = 50;
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Set transforms to shift path to center and scale to canvas size
        canvas.Translate(info.Width / 2, info.Height / 2);
        canvas.Scale(0.95f * 
            Math.Min(info.Width / (pathBounds.Width + STROKE_WIDTH),
                     info.Height / (pathBounds.Height + STROKE_WIDTH)));

        using (SKPaint paint = new SKPaint())
        {
            paint.Style = SKPaintStyle.Stroke;
            paint.StrokeWidth = STROKE_WIDTH;
            paint.Shader = SKShader.CreateLinearGradient(
                                new SKPoint(pathBounds.Left, pathBounds.Top),
                                new SKPoint(pathBounds.Right, pathBounds.Bottom),
                                colors,
                                null,
                                SKShaderTileMode.Repeat,
                                SKMatrix.MakeTranslation(offset, 0));

            canvas.DrawPath(infinityPath, paint);
        }
    }
}
```

看一看作为前两个参数传递的点`SKShader.CreateLinearGradient`。 这些点基于边界矩形的原始路径。 第一个点是 (&ndash;250， &ndash;100)，第二个 （250，100）。 SkiaSharp 的内部，这些点受制于当前的画布转换以便他们能够正确对齐以显示的无穷符号。

不到的最后一个参数的情况下`CreateLinearGradient`，你将看到从左上角的无穷符号越低到右的彩虹渐变效果。 （实际上，渐变扩展从左上角到右下角的边框。 呈现的无穷符号大于一半的边界矩形`STROKE_WIDTH`所有边的值。 因为渐变是红色的开始和结束时，并使用创建渐变`SKShaderTileMode.Repeat`，因此差异不明显。)

最后一个参数与`CreateLinearGradient`，渐变模式持续扫描在图像：

[![无穷大颜色](linear-gradient-images/InfinityColors.png "无穷大颜色")](linear-gradient-images/InfinityColors-Large.png#lightbox)

## <a name="transparency-and-gradients"></a>透明和渐变

参与为渐变的颜色可以包含的透明度。 而不是从一种颜色渐变到另一个渐变，渐变可以淡出从一种颜色为透明。 

此方法可用于一些有趣的效果。 一个典型的示例显示了具有其反射的图形对象：

[![反射渐变](linear-gradient-images/ReflectionGradient.png "反射渐变")](linear-gradient-images/ReflectionGradient-Large.png#lightbox)

颠倒的文本将显示为透明顶部到底部完全透明的 50%的渐变。 这些级别不透明度与相关联的 0x80 alpha 值和 0。

`PaintSurface`处理程序中的**反射渐变**页缩放到 90%的画布的宽度的文本的大小。 它然后计算`xText`和`yText`值来定位要在水平居中的文本，但在与垂直居中的页相对应的基线：

```csharp
public class ReflectionGradientPage : ContentPage
{
    const string TEXT = "Reflection";

    public ReflectionGradientPage ()
    {
        Title = "Reflection Gradient";

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

            // Scale the canvas to flip upside-down around the vertical center
            canvas.Scale(1, -1, 0, yText);

            // Draw reflected text
            canvas.DrawText(TEXT, xText, yText, paint);
        }
    }
}
```

那些`xText`并`yText`的值为相同的值用来显示中的反射的文本`DrawText`底部的调用`PaintSurface`处理程序。 之前该代码，但是，您会发现调用`Scale`方法的`SKCanvas`。 这`Scale`方法会进行水平缩放 1 （它不执行任何操作） 但垂直通过&ndash;1，这有效地将所有内容倒置翻转。 旋转中心的设置为点 (0， `yText`)，其中`yText`是垂直居中的画布，最初作为计算`info.Height`除以 2。

请记住，Skia 使用渐变颜色在画布转换之前的图形对象。 绘制 unreflected 的文本后，`textBounds`矩形会转移，因此它对应于显示的文本：

```csharp
textBounds.Offset(xText, yText);
```

`CreateLinearGradient`调用定义从该矩形的顶部到底部的渐变。 渐变是从完全透明的蓝色 (`paint.Color.WithAlpha(0)`) 为 50%透明蓝色 (`paint.Color.WithAlpha(0x80)`)。 画布转换翻转文本颠倒，因此 50%透明蓝色开始基线，并将变为透明顶部的文本。

## <a name="related-links"></a>相关链接

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
