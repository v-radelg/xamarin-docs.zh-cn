---
title: 线和笔划大写字母
description: 此文介绍了如何使用 SkiaSharp 绘制在 Xamarin.Forms 应用程序中具有不同笔画顶端行，此示例代码进行了演示。
ms.prod: xamarin
ms.assetid: 1F854DDD-5D1B-4DE4-BD2D-584439429FDB
ms.technology: xamarin-skiasharp
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2017
ms.openlocfilehash: 9aaecb8c63ff28111097dce81954f523b4c7731b
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76725210"
---
# <a name="lines-and-stroke-caps"></a>线和笔划大写字母

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_了解如何使用 SkiaSharp 来绘制具有不同笔划帽的线条_

SkiaSharp，呈现单行是非常不同于呈现一系列相互连接的直线。 即使绘制单个线条，但是，它通常是需要为特定的笔划宽度的线条。 由于这些行变得更广，行尾的外观也变得重要。 线条末尾的外观称为 "*笔划帽*"：

![](lines-images/strokecapsexample.png "The three stroke caps options")

对于绘制单行，`SKCanvas` 定义简单的[`DrawLine`](xref:SkiaSharp.SKCanvas.DrawLine(System.Single,System.Single,System.Single,System.Single,SkiaSharp.SKPaint))方法，该方法的参数指示行的起始和结束坐标与 `SKPaint` 对象：

```csharp
canvas.DrawLine (x0, y0, x1, y1, paint);
```

默认情况下，新实例化的 `SKPaint` 对象的[`StrokeWidth`](xref:SkiaSharp.SKPaint.StrokeWidth)属性是0，它在呈现宽度为1个像素的行时具有与值1相同的效果。 这在高分辨率设备（如手机）上显得非常小，因此可能需要将 `StrokeWidth` 设置为较大的值。 但后，开始画可调整大小的粗细的线，将引发另一个问题： 应开始和结束这些粗线条呈现方式？

行的开头和结尾的外观称为*线帽*，在 Skia 中，为*描边帽*。 此上下文中的 "cap" 一词是指一种 hat &mdash; 位于行末尾的内容。 将 `SKPaint` 对象的[`StrokeCap`](xref:SkiaSharp.SKPaint.StrokeCap)属性设置为[`SKStrokeCap`](xref:SkiaSharp.SKStrokeCap)枚举的以下成员之一：

- `Butt` （默认值）
- `Square`
- `Round`

这些最好说明与示例程序。 [**SkiaSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)程序的**SkiaSharp Lines 和 Paths**部分以基于[`StrokeCapsPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Paths/StrokeCapsPage.cs)类的标题为 "**笔划大写**" 的页开始。 此页定义一个 `PaintSurface` 事件处理程序，该处理程序遍历 `SKStrokeCap` 枚举的三个成员，同时显示枚举成员的名称并使用该笔划帽绘制线条：

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

对于 `SKStrokeCap` 枚举的每个成员，处理程序将绘制两行，其中一个笔画的粗细为50像素，另一个行位于顶部，并以两个像素为笔画宽度。 此第二行用于说明的几何开始和结束的独立于线条粗细和笔划上限的行：

[![](lines-images/strokecaps-small.png "Triple screenshot of the Stroke Caps page")](lines-images/strokecaps-large.png#lightbox "Triple screenshot of the Stroke Caps page")

正如您所看到的，`Square` 和 `Round` 描边大写字母会有效地将行的长度延长到行首的半个笔划宽度处，并在末尾再次扩展。 需要确定呈现的图形对象的维度时，此扩展变得重要。

`SKCanvas` 类还包括另一种方法，用于绘制有点特殊的多行：

```csharp
DrawPoints (SKPointMode mode, points, paint)
```

`points` 参数是 `SKPoint` 值的数组，`mode` 是[`SKPointMode`](xref:SkiaSharp.SKPointMode)枚举的成员，它具有三个成员：

- 用于呈现各个点的 `Points`
- `Lines` 连接每对点
- `Polygon` 连接所有连续点

"**多行**" 页演示了此方法。 [**MultipleLinesPage**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Paths/MultipleLinesPage.xaml)文件实例化两个 `Picker` 视图，它们使你可以选择 `SKPointMode` 枚举的成员和 `SKStrokeCap` 枚举的成员：

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

请注意，SkiaSharp 命名空间声明略有不同，因为需要 `SkiaSharp` 命名空间才能引用 `SKPointMode` 和 `SKStrokeCap` 枚举的成员。 这两个 `Picker` 视图的 `SelectedIndexChanged` 处理程序只会使 `SKCanvasView` 对象失效：

```csharp
void OnPickerSelectedIndexChanged(object sender, EventArgs args)
{
    if (canvasView != null)
    {
        canvasView.InvalidateSurface();
    }
}
```

此处理程序需要检查 `SKCanvasView` 对象是否存在，因为当 XAML 文件中 `Picker` 的 `SelectedIndex` 属性设置为0，并在实例化 `SKCanvasView` 之前，就会调用事件处理程序。

`PaintSurface` 处理程序从 `Picker` 视图中获取两个枚举值：

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

屏幕截图显示了各种 `Picker` 选项：

[![](lines-images/multiplelines-small.png "Triple screenshot of the Multiple Lines page")](lines-images/multiplelines-large.png#lightbox "Triple screenshot of the Multiple Lines page")

左侧的 iPhone 显示 `SKPointMode.Points` 枚举成员如何导致 `DrawPoints` 将 `SKPoint` 数组中的每个点呈现为一个正方形（如果线帽为 `Butt` 或 `Square`）。 如果 `Round`线帽，则会呈现圆圈。

Android 屏幕快照显示 `SKPointMode.Lines`的结果。 在这种情况下，`DrawPoints` 方法使用指定的线帽在每对 `SKPoint` 值之间绘制一条线，在这种情况下，`Round`。

改为使用 `SKPointMode.Polygon`时，将在数组中的连续点之间绘制一条线，但如果你看起来非常紧密，就会看到这些行没有连接。 每个这些单独的行开始和结束的指定的线帽。 如果选择 `Round` cap，则线条可能看似已连接，但它们实际上未连接。

行是否已连接或未连接是使用的图形路径的一个重要方面。

## <a name="related-links"></a>相关链接

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
