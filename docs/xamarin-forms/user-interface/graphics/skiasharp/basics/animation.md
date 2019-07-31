---
title: SkiaSharp 中的基本动画
description: 本文介绍了如何进行动画处理 SkiaSharp 图形在 Xamarin.Forms 应用程序，并且此示例代码进行了演示。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 31C96FD6-07E4-4473-A551-24753A5118C3
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2017
ms.openlocfilehash: e9af5a78900fce714b214dfd3b3e0e83e17bbce2
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2019
ms.locfileid: "68648731"
---
# <a name="basic-animation-in-skiasharp"></a>SkiaSharp 中的基本动画

[![下载示例](~/media/shared/download.png)下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_了解如何对 SkiaSharp 图形进行动画处理_

您可以设置动画效果 SkiaSharp 图形在 Xamarin.Forms 中导致`PaintSurface`定期调用的方法，每次绘制图形略有不同。 下面是动画看似展开从中心的同心圆本文稍后所示：

![](animation-images/animationexample.png "从中心看似扩展的多个同心环")

**Pulsating 椭圆**页面[ **SkiaSharpFormsDemos** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)程序之间进行动画处理的椭圆的两个轴，以便它似乎 pulsating 和甚至可以控制此 pulsation 的速率。 [ **PulsatingEllipsePage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/PulsatingEllipsePage.xaml)文件实例化 Xamarin.Forms`Slider`和`Label`以显示当前滑块的值。 这是一种常见的方法来集成`SKCanvasView`与其他 Xamarin.Forms 视图：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.PulsatingEllipsePage"
             Title="Pulsating Ellipse">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="*" />
        </Grid.RowDefinitions>

        <Slider x:Name="slider"
                Grid.Row="0"
                Maximum="10"
                Minimum="0.1"
                Value="5"
                Margin="20, 0" />

        <Label Grid.Row="1"
               Text="{Binding Source={x:Reference slider},
                              Path=Value,
                              StringFormat='Cycle time = {0:F1} seconds'}"
               HorizontalTextAlignment="Center" />

        <skia:SKCanvasView x:Name="canvasView"
                           Grid.Row="2"
                           PaintSurface="OnCanvasViewPaintSurface" />
    </Grid>
</ContentPage>
```

实例化的代码隐藏文件`Stopwatch`对象作为高精度时钟。 `OnAppearing`重写集`pageIsActive`字段`true`，并调用一个名为方法`AnimationLoop`。 `OnDisappearing`重写设置`pageIsActive`字段`false`:

```csharp
Stopwatch stopwatch = new Stopwatch();
bool pageIsActive;
float scale;            // ranges from 0 to 1 to 0

public PulsatingEllipsePage()
{
    InitializeComponent();
}

protected override void OnAppearing()
{
    base.OnAppearing();
    pageIsActive = true;
    AnimationLoop();
}

protected override void OnDisappearing()
{
    base.OnDisappearing();
    pageIsActive = false;
}
```

`AnimationLoop`方法启动`Stopwatch`，然后循环时`pageIsActive`是`true`。 这是实质上是"无限循环"，而页处于活动状态，但它不会导致程序挂起，因为循环的结尾部分通过调用`Task.Delay`与`await`运算符，可让程序函数的其他部分。 参数`Task.Delay`后，即可在 1/30 秒后完成。 此项定义动画的帧速率。

```csharp
async Task AnimationLoop()
{
    stopwatch.Start();

    while (pageIsActive)
    {
        double cycleTime = slider.Value;
        double t = stopwatch.Elapsed.TotalSeconds % cycleTime / cycleTime;
        scale = (1 + (float)Math.Sin(2 * Math.PI * t)) / 2;
        canvasView.InvalidateSurface();
        await Task.Delay(TimeSpan.FromSeconds(1.0 / 30));
    }

    stopwatch.Stop();
}

```

`while`循环开始通过获取周期时间与`Slider`。 这是以秒为单位，例如，5 次。 第二个语句计算的值`t`有关*时间*。 有关`cycleTime`为 5，`t`将增加从 0 到 1 每隔 5 秒。 参数`Math.Sin`函数中的第二个语句范围从 0 到 2 π 每隔 5 秒。 `Math.Sin`函数将返回值从 0 到 1 后到 0，再到&ndash;1 和 0 每隔 5 秒，但其速度更慢更改时的值是接近 1 或-1 的值。 值 1 添加的值始终为正数，因此然后它除以 2，因此从 ½ 为 ½ 为介于 0 到 ½，但速度较慢的值时大约 1 和 0 到 1 的值范围。 这存储在`scale`字段中，和`SKCanvasView`失效。

`PaintSurface`方法使用此`scale`值来计算两个椭圆的轴：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    float maxRadius = 0.75f * Math.Min(info.Width, info.Height) / 2;
    float minRadius = 0.25f * maxRadius;

    float xRadius = minRadius * scale + maxRadius * (1 - scale);
    float yRadius = maxRadius * scale + minRadius * (1 - scale);

    using (SKPaint paint = new SKPaint())
    {
        paint.Style = SKPaintStyle.Stroke;
        paint.Color = SKColors.Blue;
        paint.StrokeWidth = 50;
        canvas.DrawOval(info.Width / 2, info.Height / 2, xRadius, yRadius, paint);

        paint.Style = SKPaintStyle.Fill;
        paint.Color = SKColors.SkyBlue;
        canvas.DrawOval(info.Width / 2, info.Height / 2, xRadius, yRadius, paint);
    }
}
```

该方法计算基于显示区域的大小最大 radius 和基于最大的半径的最小半径。 `scale`值经过动画处理的介于 0 和 1 之间，并返回到 0，因此该方法使用的计算`xRadius`并`yRadius`之间的范围`minRadius`和`maxRadius`。 这些值用于绘制并填充椭圆，使用：

[![](animation-images/pulsatingellipse-small.png "三重脉动椭圆页屏幕截图")](animation-images/pulsatingellipse-large.png#lightbox "脉动椭圆页面的三个屏幕截图")

请注意，`SKPaint`中创建对象时`using`块。 像许多 SkiaSharp 类`SKPaint`派生自`SKObject`，它派生`SKNativeObject`，它实现[ `IDisposable` ](xref:System.IDisposable)接口。 `SKPaint` 重写`Dispose`方法来释放非托管的资源。

 将放`SKPaint`中`using`块确保`Dispose`用于释放这些非托管的资源的块结束时调用。 发生这种情况是否仍要使用的内存时`SKPaint`释放对象.NET 垃圾回收器，但在动画代码中，最好是主动地释放内存中更有序的方式。

 在此特定情况下更好的解决方案就是创建两个`SKPaint`对象一次并将其保存为字段。

这正是**展开圆圈**动画 does。 [ `ExpandingCirclesPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/skia-sharp-forms/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/ExpandingCirclesPage.cs)类首先定义多个字段，包括`SKPaint`对象：

```csharp
public class ExpandingCirclesPage : ContentPage
{
    const double cycleTime = 1000;       // in milliseconds

    SKCanvasView canvasView;
    Stopwatch stopwatch = new Stopwatch();
    bool pageIsActive;
    float t;
    SKPaint paint = new SKPaint
    {
        Style = SKPaintStyle.Stroke
    };

    public ExpandingCirclesPage()
    {
        Title = "Expanding Circles";

        canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }
    ...
}
```

此程序使用不同的方法来通过基于 Xamarin.Forms 的动画`Device.StartTimer`方法。 `t`字段以动画形式是从 0 到 1 每个`cycleTime`毫秒：

```csharp
public class ExpandingCirclesPage : ContentPage
{
    ...
    protected override void OnAppearing()
    {
        base.OnAppearing();
        pageIsActive = true;
        stopwatch.Start();

        Device.StartTimer(TimeSpan.FromMilliseconds(33), () =>
        {
            t = (float)(stopwatch.Elapsed.TotalMilliseconds % cycleTime / cycleTime);
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

`PaintSurface`处理程序绘制具有经过动画处理的半径的五个同心圆。 如果`baseRadius`变量计算为 100，然后为`t`从 0 到 1，从 0 到 100、 100 到 200、 200 到 300、 300 到 400 和 400 到 500 的五个圆圈增加半径的动画。 对于大多数的圆圈`strokeWidth`是 50 但第一个圆`strokeWidth`进行从 0 到 50 的动画处理。 对于大多数的圆圈，颜色为蓝色，但最后一个圆，颜色以动画形式从蓝到透明。 请注意，到第四个参数`SKColor`指定不透明度的构造函数：

```csharp
public class ExpandingCirclesPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        SKPoint center = new SKPoint(info.Width / 2, info.Height / 2);
        float baseRadius = Math.Min(info.Width, info.Height) / 12;

        for (int circle = 0; circle < 5; circle++)
        {
            float radius = baseRadius * (circle + t);

            paint.StrokeWidth = baseRadius / 2 * (circle == 0 ? t : 1);
            paint.Color = new SKColor(0, 0, 255,
                (byte)(255 * (circle == 4 ? (1 - t) : 1)));

            canvas.DrawCircle(center.X, center.Y, radius, paint);
        }
    }
}
```

结果是图像看起来相同 when`t`等于 0 时`t`等于 1，而圆形似乎继续扩大对不限次数：

[![](animation-images/expandingcircles-small.png "三重的展开圆圈页屏幕截图")](animation-images/expandingcircles-large.png#lightbox "带来三倍的展开圆圈页屏幕截图")


## <a name="related-links"></a>相关链接

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
