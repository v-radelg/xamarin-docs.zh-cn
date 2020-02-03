---
title: SkiaSharp 中的路径基础知识
description: 本文探讨了结合使用连接的直线和曲线的 SkiaSharp SKPath 对象，并演示此示例代码。
ms.prod: xamarin
ms.assetid: A7EDA6C2-3921-4021-89F3-211551E430F1
ms.technology: xamarin-skiasharp
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2017
ms.openlocfilehash: c892adf2f75ec00c4a9ee171ded78f79bb8227e9
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76725192"
---
# <a name="path-basics-in-skiasharp"></a>SkiaSharp 中的路径基础知识

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_浏览用于组合连接的线条和曲线的 SkiaSharp SKPath 对象_

图形路径的最重要功能之一是能够定义应已连接多个行和时应不将它们连接起来。 这些两个三角形顶部进行说明，可以是有意义的不同之处：

![](paths-images/connectedlinesexample.png "Two triangles showing the difference between connected and disconnected lines")

图形路径由[`SKPath`](xref:SkiaSharp.SKPath)对象封装。 路径是一个或多个*轮廓*的集合。 每个轮廓都是*连接*的直线和曲线的集合。 分布图未连接到彼此，但它们可能会以可视方式重叠。 有时单一 contour 可以重叠本身。

通常，等高线首先会调用以下 `SKPath`方法：

- 开始新等高线[`MoveTo`](xref:SkiaSharp.SKPath.MoveTo*)

该方法的参数是一个点，您可以将其表示为 `SKPoint` 值或单独的 X 和 Y 坐标。 `MoveTo` 调用在轮廓的开头和初始*当前点*之间建立一个点。 可以调用以下方法来继续使用直线或曲线从当前的点到点方法，然后将成为新的当前点中指定的轮廓：

- [`LineTo`](xref:SkiaSharp.SKPath.LineTo*)向路径添加直线
- [`ArcTo`](xref:SkiaSharp.SKPath.ArcTo*)以添加一条弧，这是圆或椭圆圆周上的直线
- [`CubicTo`](xref:SkiaSharp.SKPath.CubicTo*)以添加三次方贝塞尔曲线
- [`QuadTo`](xref:SkiaSharp.SKPath.QuadTo*)添加二次贝塞尔曲线
- [`ConicTo`](xref:SkiaSharp.SKPath.ConicTo*)以添加一个有理二次贝塞尔曲线，该曲线可准确渲染圆锥部分（省略号、parabolas 和 hyperbolas）

这些五个方法均包含描述直线或曲线所需的所有信息。 每个五个方法适用于前面紧邻的方法调用来建立的当前点一起使用。 例如，`LineTo` 方法基于当前点向等高线添加一条直线，因此 `LineTo` 的参数只是一个点。

`SKPath` 类还定义了与这六个方法同名但在开头使用 `R` 的方法：

- [`RMoveTo`](xref:SkiaSharp.SKPath.RMoveTo*)
- [`RLineTo`](xref:SkiaSharp.SKPath.RLineTo*)
- [`RArcTo`](xref:SkiaSharp.SKPath.RArcTo*)
- [`RCubicTo`](xref:SkiaSharp.SKPath.RCubicTo*)
- [`RQuadTo`](xref:SkiaSharp.SKPath.RQuadTo*)
- [`RConicTo`](xref:SkiaSharp.SKPath.RConicTo*)

`R` 代表*相对*的。 这些方法与没有 `R` 的相应方法具有相同的语法，但它们相对于当前点。 这些可以方便地绘制类似组成部分中多次调用的方法的路径。

等高线以对 `MoveTo` 或 `RMoveTo`的另一次调用结束，后者会开始一个新的轮廓，或对 `Close`的调用，这将关闭等高线。 `Close` 方法自动在从当前点到等高线的第一个点之间追加一条直线，并将该路径标记为闭合，这意味着它将在不使用任何笔触帽的情况下呈现。

"打开" 和 "闭合" 等高线之间的差异在 "**两个三角形等高线**" 页中进行了说明，该页面使用带有两个轮廓的 `SKPath` 对象呈现两个三角形。 第一个轮廓线是打开和关闭第二个。 下面是[`TwoTriangleContoursPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Paths/TwoTriangleContoursPage.cs)类：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    // Create the path
    SKPath path = new SKPath();

    // Define the first contour
    path.MoveTo(0.5f * info.Width, 0.1f * info.Height);
    path.LineTo(0.2f * info.Width, 0.4f * info.Height);
    path.LineTo(0.8f * info.Width, 0.4f * info.Height);
    path.LineTo(0.5f * info.Width, 0.1f * info.Height);

    // Define the second contour
    path.MoveTo(0.5f * info.Width, 0.6f * info.Height);
    path.LineTo(0.2f * info.Width, 0.9f * info.Height);
    path.LineTo(0.8f * info.Width, 0.9f * info.Height);
    path.Close();

    // Create two SKPaint objects
    SKPaint strokePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Magenta,
        StrokeWidth = 50
    };

    SKPaint fillPaint = new SKPaint
    {
        Style = SKPaintStyle.Fill,
        Color = SKColors.Cyan
    };

    // Fill and stroke the path
    canvas.DrawPath(path, fillPaint);
    canvas.DrawPath(path, strokePaint);
}
```

第一个等高线包含对使用 X 和 Y 坐标（而非 `SKPoint` 值）的[`MoveTo`](xref:SkiaSharp.SKPath.MoveTo(System.Single,System.Single))的调用，后跟三个对[`LineTo`](xref:SkiaSharp.SKPath.LineTo(System.Single,System.Single))的调用来绘制三角形的三个边。 第二个轮廓只包含两个对 `LineTo` 的调用，但会通过调用[`Close`](xref:SkiaSharp.SKPath.Close)完成等高线，这会关闭等高线。 重要的区别是：

[![](paths-images/twotrianglecontours-small.png "Triple screenshot of the Two Triangle Contours page")](paths-images/twotrianglecontours-large.png#lightbox "Triple screenshot of the Two Triangle Contours page")

正如您所看到的第一个轮廓显然是一系列的三个相互连接的直线，但结束时不会连接与的开始部分。 在顶部重叠两行。 第二个轮廓明显闭合，并使用较少的 `LineTo` 调用实现，因为 `Close` 方法自动添加最后一行来结束等高线。

`SKCanvas` 仅定义了一个[`DrawPath`](xref:SkiaSharp.SKCanvas.DrawPath(SkiaSharp.SKPath,SkiaSharp.SKPaint))方法，在此演示中调用了两次来填充和绘制路径。 所有轮廓进行都填充，甚至包括那些未关闭。 为了填充闭合的路径，则假定一条直线起点和终点的轮廓之间存在。 如果从第一个等高线中移除最后一个 `LineTo`，或从第二个轮廓中删除 `Close` 调用，则每个轮廓将只有两个边，但将填充为三角形。

`SKPath` 定义了许多其他方法和属性。 以下方法将整个分布图添加到路径，它可能已关闭或未关闭根据该方法：

- [`AddRect`](xref:SkiaSharp.SKPath.AddRect*)
- [`AddRoundedRect`](xref:SkiaSharp.SKPath.AddRoundedRect(SkiaSharp.SKRect,System.Single,System.Single,SkiaSharp.SKPathDirection))
- [`AddCircle`](xref:SkiaSharp.SKPath.AddCircle(System.Single,System.Single,System.Single,SkiaSharp.SKPathDirection))
- [`AddOval`](xref:SkiaSharp.SKPath.AddOval(SkiaSharp.SKRect,SkiaSharp.SKPathDirection))
- [`AddArc`](xref:SkiaSharp.SKPath.AddArc(SkiaSharp.SKRect,System.Single,System.Single))在椭圆的圆周上添加曲线
- 将另一个路径添加到当前路径[`AddPath`](xref:SkiaSharp.SKPath.AddPath*)
- [`AddPathReverse`](xref:SkiaSharp.SKPath.AddPathReverse(SkiaSharp.SKPath))以反向添加其他路径

请记住，`SKPath` 对象仅定义了一系列点和连接 &mdash; 几何。 仅当 `SKPath` 与 `SKPaint` 对象结合使用时，才是使用特定颜色、笔划宽度等呈现的路径。 另外，请记住，传递给 `DrawPath` 方法的 `SKPaint` 对象定义了整个路径的特征。 如果你想要绘制一些内容需要多个颜色，必须为每种颜色来使用一个单独的路径。

正如直线的起点和终点的外观是通过描边帽定义的，两行间的连接的外观由*笔划联接*定义。 为此，可将 `SKPaint` 的[`StrokeJoin`](xref:SkiaSharp.SKPaint.StrokeJoin)属性设置为[`SKStrokeJoin`](xref:SkiaSharp.SKStrokeJoin)枚举的成员：

- pointy 联接的 `Miter`
- 用于舍入联接的 `Round`
- 截联接的 `Bevel`

"**笔划联接**" 页显示了这三个笔划联接，其中包含类似于 "**笔划大写字母**" 页的代码。 这是[`StrokeJoinsPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Paths/StrokeJoinsPage.cs)类中 `PaintSurface` 事件处理程序：

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
        TextAlign = SKTextAlign.Right
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

    float xText = info.Width - 100;
    float xLine1 = 100;
    float xLine2 = info.Width - xLine1;
    float y = 2 * textPaint.FontSpacing;
    string[] strStrokeJoins = { "Miter", "Round", "Bevel" };

    foreach (string strStrokeJoin in strStrokeJoins)
    {
        // Display text
        canvas.DrawText(strStrokeJoin, xText, y, textPaint);

        // Get stroke-join value
        SKStrokeJoin strokeJoin;
        Enum.TryParse(strStrokeJoin, out strokeJoin);

        // Create path
        SKPath path = new SKPath();
        path.MoveTo(xLine1, y - 80);
        path.LineTo(xLine1, y + 80);
        path.LineTo(xLine2, y + 80);

        // Display thick line
        thickLinePaint.StrokeJoin = strokeJoin;
        canvas.DrawPath(path, thickLinePaint);

        // Display thin line
        canvas.DrawPath(path, thinLinePaint);
        y += 3 * textPaint.FontSpacing;
    }
}
```

下面是运行的程序：

[![](paths-images/strokejoins-small.png "Triple screenshot of the Stroke Joins page")](paths-images/strokejoins-large.png#lightbox "Triple screenshot of the Stroke Joins page")

斜接联接包含，其中用线条连接是尖角。 当在一个小角度加入两行时，斜接联接可能会变得相当长。 若要防止过长的斜接联接，则斜接联接的长度受 `SKPaint`的[`StrokeMiter`](xref:SkiaSharp.SKPaint.StrokeMiter)属性的值的限制。 超过此长度的斜接联接砍掉成为斜切接合。

## <a name="related-links"></a>相关链接

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
