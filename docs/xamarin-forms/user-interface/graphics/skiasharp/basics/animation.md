---
title: SkiaSharp 中的基本动画
description: 本文介绍了如何进行动画处理 SkiaSharp 图形在 Xamarin.Forms 应用程序，并且此示例代码进行了演示。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 31C96FD6-07E4-4473-A551-24753A5118C3
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2017
ms.openlocfilehash: 80de16a0cf9b601ac3795085b638b9d62812f4d9
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76725548"
---
# <a name="basic-animation-in-skiasharp"></a>SkiaSharp 中的基本动画

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_了解如何对 SkiaSharp 图形进行动画处理_

可以通过导致定期调用 `PaintSurface` 方法来对 Xamarin 中的 SkiaSharp 图形进行动画处理，每次绘制图形的方式略有不同。 下面是动画看似展开从中心的同心圆本文稍后所示：

![](animation-images/animationexample.png "Several concentric circles seemingly expanding from the center")

[**SkiaSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)程序中的**Pulsating 椭圆**页对椭圆的两个轴进行动画处理，使其看起来像 Pulsating，甚至还可以控制此 pulsation 的速率。 [**PulsatingEllipsePage**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/PulsatingEllipsePage.xaml)文件实例化 Xamarin. Forms `Slider` 和 `Label` 以显示滑块的当前值。 这是将 `SKCanvasView` 与其他 Xamarin 进行集成的常见方法。 Forms 视图：

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

代码隐藏文件会实例化一个 `Stopwatch` 对象以用作高精度时钟。 `OnAppearing` 重写将 `pageIsActive` 字段设置为 `true`，并调用名为 `AnimationLoop`的方法。 `OnDisappearing` 重写将 `pageIsActive` 字段设置为 `false`：

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

`AnimationLoop` 方法将启动 `Stopwatch`，然后在 `true``pageIsActive` 时循环。 在此页处于活动状态时，这实质上是一个 "无限循环"，但它不会导致程序挂起，因为循环会通过调用 `Task.Delay` 与 `await` 运算符来结束，这允许程序函数的其他部分使用。 `Task.Delay` 的参数使其在 1/30 秒后完成。 此项定义动画的帧速率。

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

`while` 循环从 `Slider`获取周期时间开始。 这是以秒为单位，例如，5 次。 第二个语句计算*时间*`t` 的值。 对于5的 `cycleTime`，每5秒 `t` 增加0到1。 第二个语句中 `Math.Sin` 函数的参数的范围为0到2π，每5秒一次。 `Math.Sin` 函数返回一个介于0到1之间的值，然后返回到0，然后每5秒 &ndash;1 和0，但当该值接近1或-1 时，值的更改速度将变慢。 值 1 添加的值始终为正数，因此然后它除以 2，因此从 ½ 为 ½ 为介于 0 到 ½，但速度较慢的值时大约 1 和 0 到 1 的值范围。 此项存储在 `scale` 字段中，`SKCanvasView` 无效。

`PaintSurface` 方法使用此 `scale` 值计算椭圆的两个轴：

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

该方法计算基于显示区域的大小最大 radius 和基于最大的半径的最小半径。 `scale` 值在0和1之间进行了动画处理，并返回到0，因此该方法使用它来计算 `xRadius`，并 `yRadius` `minRadius` 和 `maxRadius`之间的范围。 这些值用于绘制并填充椭圆，使用：

[![](animation-images/pulsatingellipse-small.png "Triple screenshot of the Pulsating Ellipse page")](animation-images/pulsatingellipse-large.png#lightbox "Triple screenshot of the Pulsating Ellipse page")

请注意，`SKPaint` 对象是在 `using` 块中创建的。 与许多 SkiaSharp 类一样 `SKPaint` 派生自 `SKObject`，后者派生自 `SKNativeObject`，后者实现[`IDisposable`](xref:System.IDisposable)接口。 `SKPaint` 重写 `Dispose` 方法以释放非托管资源。

 将 `SKPaint` 放置在 `using` 块中可确保在块的末尾调用 `Dispose`，以释放这些非托管资源。 当由 .NET 垃圾回收器释放 `SKPaint` 对象使用的内存，但在动画代码中，最好以更有序的方式释放内存，这种情况仍会发生。

 在这种特定情况下，更好的解决方案是创建两个 `SKPaint` 对象一次，并将其另存为字段。

这就是**扩展圆圈**动画的作用。 [`ExpandingCirclesPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/ExpandingCirclesPage.cs)类首先定义多个字段，包括 `SKPaint` 对象：

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

此程序使用不同的方法基于 Xamarin. Forms `Device.StartTimer` 方法。 每隔 `cycleTime` 毫秒 `t` 字段从0到1进行动画处理：

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

`PaintSurface` 处理程序绘制具有动画半径的五个同心圆圆圈。 如果 `baseRadius` 变量的计算结果为100，则当 `t` 从0到1进行动画处理时，五个圆圈的半径将从0增加到100、100到200、200、300、300、400、400、500。 对于大多数圆圈，`strokeWidth` 为50，但对于第一个圆圈，`strokeWidth` 从0到50进行动画处理。 对于大多数的圆圈，颜色为蓝色，但最后一个圆，颜色以动画形式从蓝到透明。 请注意指定不透明度的 `SKColor` 构造函数的第四个参数：

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

结果是，`t` 当 `t` 等于1时，图像看起来相同，而圆圈看起来始终持续展开：

[![](animation-images/expandingcircles-small.png "Triple screenshot of the Expanding Circles page")](animation-images/expandingcircles-large.png#lightbox "Triple screenshot of the Expanding Circles page")

## <a name="related-links"></a>相关链接

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
