---
title: 线和笔划大写字母
description: 此文介绍了如何使用 SkiaSharp 绘制在 Xamarin.Forms 应用程序中具有不同笔画顶端行，此示例代码进行了演示。
ms.prod: xamarin
ms.assetid: 1F854DDD-5D1B-4DE4-BD2D-584439429FDB
ms.technology: xamarin-skiasharp
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2017
ms.openlocfilehash: 724a79e618321f97257718bf56dd1fdd18f73563
ms.sourcegitcommit: 191f1f3b13a14e2afadcb95126c5f653722f126f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/30/2019
ms.locfileid: "75545599"
---
# <a name="lines-and-stroke-caps"></a>线和笔划大写字母

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_了解如何使用 SkiaSharp 绘制具有不同笔画顶端行_

SkiaSharp，呈现单行是非常不同于呈现一系列相互连接的直线。 即使绘制单个线条，但是，它通常是需要为特定的笔划宽度的线条。 由于这些行变得更广，行尾的外观也变得重要。 名为行尾的外观*笔划 cap*:

![](lines-images/strokecapsexample.png "The three stroke caps options")

用于绘制单个线条`SKCanvas`定义一种简单[ `DrawLine` ](xref:SkiaSharp.SKCanvas.DrawLine(System.Single,System.Single,System.Single,System.Single,SkiaSharp.SKPaint))方法的参数指示的起始和结束的包含的行坐标`SKPaint`对象：

```csharp
canvas.DrawLine (x0, y0, x1, y1, paint);
```

默认情况下[ `StrokeWidth` ](xref:SkiaSharp.SKPaint.StrokeWidth)属性的新实例化`SKPaint`对象是 0，它具有为粗细中呈现的一个像素行 1 的值相同的效果。 这会显示非常细小手机，等的高分辨率设备上可能需要设置`StrokeWidth`到更大的值。 但后，开始画可调整大小的粗细的线，将引发另一个问题： 应开始和结束这些粗线条呈现方式？

调用的开始和结束的行的外观*线帽*，或者在 Skia，*笔划 cap*。 在此上下文中的"cap"一词是指一种类型的 hat&mdash;位于行尾的内容。 您设置[ `StrokeCap` ](xref:SkiaSharp.SKPaint.StrokeCap)的属性`SKPaint`对象的以下成员之一[ `SKStrokeCap` ](xref:SkiaSharp.SKStrokeCap)枚举：

- `Butt` （默认值）
- `Square`
- `Round`

这些最好说明与示例程序。 **SkiaSharp 线和路径**一部分[ **SkiaSharpFormsDemos** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)程序开始页面标题为**笔划大写字母**基于[ `StrokeCapsPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/LinesAndPaths/StrokeCapsPage.cs)类。 此页定义`PaintSurface`循环访问的三个成员的事件处理程序`SKStrokeCap`显示这两个枚举成员的名称和绘制一条直线使用该笔划 cap 枚举：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    SKPaint textPaint = new SKPaint
    {
        Color = SKColors.Black,
        TextSize = 75,
        TextAlign = SKTextAlign.Center
    };

    SKPaint thickLinePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Orange,
        StrokeWidth = 50
    };

    SKPaint thinLinePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Black,
        StrokeWidth = 2
    };

    float xText = info.Width / 2;
    float xLine1 = 100;
    float xLine2 = info.Width - xLine1;
    float y = textPaint.FontSpacing;

    foreach (SKStrokeCap strokeCap in Enum.GetValues(typeof(SKStrokeCap)))
    {
        // Display text
        canvas.DrawText(strokeCap.ToString(), xText, y, textPaint);
        y += textPaint.FontSpacing;

        // Display thick line
        thickLinePaint.StrokeCap = strokeCap;
        canvas.DrawLine(xLine1, y, xLine2, y, thickLinePaint);

        // Display thin line
        canvas.DrawLine(xLine1, y, xLine2, y, thinLinePaint);
        y += 2 * textPaint.FontSpacing;
    }
}
```

每个成员的`SKStrokeCap`枚举，该处理程序绘制两个行，另一个笔画粗细设置为 50 个像素，另一行具有两个像素的笔画粗细定位在顶部上使用。 此第二行用于说明的几何开始和结束的独立于线条粗细和笔划上限的行：

[![](lines-images/strokecaps-small.png "Triple screenshot of the Stroke Caps page")](lines-images/strokecaps-large.png#lightbox "Triple screenshot of the Stroke Caps page")

正如您所看到的`Square`和`Round`笔划大写字母有效地通过一半笔划宽度在行开头和结尾处再次扩展行的长度。 需要确定呈现的图形对象的维度时，此扩展变得重要。

`SKCanvas`类还包含用于绘制多个行是有点奇怪的另一种方法：

```csharp
DrawPoints (SKPointMode mode, points, paint)
```

`points`参数是一个数组`SKPoint`值和`mode`属于[ `SKPointMode` ](xref:SkiaSharp.SKPointMode)枚举，它具有三个成员：

- `Points` 若要呈现的各个点
- `Lines` 若要连接的点的每个对
- `Polygon` 若要连接所有连续点

**多行**页演示此方法。 [ **MultipleLinesPage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/LinesAndPaths/MultipleLinesPage.xaml)文件实例化两个`Picker`视图使您选择的成员`SKPointMode`枚举的成员和`SKStrokeCap`枚举：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp;assembly=SkiaSharp"
             xmlns:skiaforms="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Paths.MultipleLinesPage"
             Title="Multiple Lines">
    <Grid>
        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="*" />
            <ColumnDefinition Width="*" />
        </Grid.ColumnDefinitions>

        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="*" />
        </Grid.RowDefinitions>

        <Picker x:Name="pointModePicker"
                Title="Point Mode"
                Grid.Row="0"
                Grid.Column="0"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.ItemsSource>
                <x:Array Type="{x:Type skia:SKPointMode}">
                    <x:Static Member="skia:SKPointMode.Points" />
                    <x:Static Member="skia:SKPointMode.Lines" />
                    <x:Static Member="skia:SKPointMode.Polygon" />
                </x:Array>
            </Picker.ItemsSource>
            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>

        <Picker x:Name="strokeCapPicker"
                Title="Stroke Cap"
                Grid.Row="0"
                Grid.Column="1"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.ItemsSource>
                <x:Array Type="{x:Type skia:SKStrokeCap}">
                    <x:Static Member="skia:SKStrokeCap.Butt" />
                    <x:Static Member="skia:SKStrokeCap.Round" />
                    <x:Static Member="skia:SKStrokeCap.Square" />
                </x:Array>
            </Picker.ItemsSource>
            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>

        <skiaforms:SKCanvasView x:Name="canvasView"
                                PaintSurface="OnCanvasViewPaintSurface"
                                Grid.Row="1"
                                Grid.Column="0"
                                Grid.ColumnSpan="2" />
    </Grid>
</ContentPage>
```

请注意 SkiaSharp 命名空间声明是稍有不同，因为`SkiaSharp`引用的成员所需的命名空间`SKPointMode`和`SKStrokeCap`枚举。 `SelectedIndexChanged`两个处理程序`Picker`视图只是使`SKCanvasView`对象：

```csharp
void OnPickerSelectedIndexChanged(object sender, EventArgs args)
{
    if (canvasView != null)
    {
        canvasView.InvalidateSurface();
    }
}
```

此处理程序需要检查是否存在`SKCanvasView`对象是第一个事件处理程序时，调用`SelectedIndex`的属性`Picker`在 XAML 文件中，设置为 0 和之前将发生这种情况`SKCanvasView`已实例化。

`PaintSurface`处理程序获取两个枚举值从`Picker`视图：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    // Create an array of points scattered through the page
    SKPoint[] points = new SKPoint[10];

    for (int i = 0; i < 2; i++)
    {
        float x = (0.1f + 0.8f * i) * info.Width;

        for (int j = 0; j < 5; j++)
        {
            float y = (0.1f + 0.2f * j) * info.Height;
            points[2 * j + i] = new SKPoint(x, y);
        }
    }

    SKPaint paint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.DarkOrchid,
        StrokeWidth = 50,
        StrokeCap = (SKStrokeCap)strokeCapPicker.SelectedItem
    };

    // Render the points by calling DrawPoints
    SKPointMode pointMode = (SKPointMode)pointModePicker.SelectedItem;
    canvas.DrawPoints(pointMode, points, paint);
}
```

屏幕截图显示了各种`Picker`选择：

[![](lines-images/multiplelines-small.png "Triple screenshot of the Multiple Lines page")](lines-images/multiplelines-large.png#lightbox "Triple screenshot of the Multiple Lines page")

在左侧显示了 iPhone 如何`SKPointMode.Points`枚举成员会导致`DrawPoints`要呈现的每个中点`SKPoint`线帽是否为一个方框数组`Butt`或`Square`。 线帽是否呈现圆圈`Round`。

Android 屏幕快照显示 `SKPointMode.Lines`的结果。 在这种情况下，`DrawPoints` 方法使用指定的线帽在每对 `SKPoint` 值之间绘制一条线，在这种情况下，`Round`。

改为使用 `SKPointMode.Polygon`时，将在数组中的连续点之间绘制一条线，但如果你看起来非常紧密，就会看到这些行没有连接。 每个这些单独的行开始和结束的指定的线帽。 如果您选择`Round`顶端行可能看起来连接，但它们实际上未连接。

行是否已连接或未连接是使用的图形路径的一个重要方面。

## <a name="related-links"></a>相关链接

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
