---
title: SkiaSharp 中的路径基础知识
description: 本文探讨了结合使用连接的直线和曲线的 SkiaSharp SKPath 对象，并演示此示例代码。
ms.prod: xamarin
ms.assetid: A7EDA6C2-3921-4021-89F3-211551E430F1
ms.technology: xamarin-skiasharp
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2017
ms.openlocfilehash: 833e24e91a37b92eb5025aacdc9509f092b823cd
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61290705"
---
# <a name="path-basics-in-skiasharp"></a>SkiaSharp 中的路径基础知识

[![下载示例](~/media/shared/download.png)下载示例](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)

_了解结合使用连接的直线和曲线的 SkiaSharp SKPath 对象_

图形路径的最重要功能之一是能够定义应已连接多个行和时应不将它们连接起来。 这些两个三角形顶部进行说明，可以是有意义的不同之处：

![](paths-images/connectedlinesexample.png "显示连接和断开连接行之间的区别的两个三角形")

图形路径封装[ `SKPath` ](xref:SkiaSharp.SKPath)对象。 路径是一个或多个集合*轮廓*。 每个分布是一系列*连接*直线和曲线。 分布图未连接到彼此，但它们可能会以可视方式重叠。 有时单一 contour 可以重叠本身。

轮廓线通常从以下方法的调用开始`SKPath`:

- [`MoveTo`](xref:SkiaSharp.SKPath.MoveTo*) 若要开始新的轮廓

该方法的参数是可以表示为单个点`SKPoint`值，或将作为单独的 X 和 Y 坐标。 `MoveTo`调用建立轮廓和一个初始的点开头*当前点*。 可以调用以下方法来继续使用直线或曲线从当前的点到点方法，然后将成为新的当前点中指定的轮廓：

- [`LineTo`](xref:SkiaSharp.SKPath.LineTo*) 若要向路径添加一条直线
- [`ArcTo`](xref:SkiaSharp.SKPath.ArcTo*) 若要添加一段弧线，这是圆或椭圆圆周上的线条
- [`CubicTo`](xref:SkiaSharp.SKPath.CubicTo*) 若要添加的三次方贝塞尔样条
- [`QuadTo`](xref:SkiaSharp.SKPath.QuadTo*) 若要添加的二次贝塞尔样条
- [`ConicTo`](xref:SkiaSharp.SKPath.ConicTo*) 若要添加的合理二次贝塞尔样条，可以准确地呈现圆锥部分 （椭圆、 条抛物线合理组合和 hyperbolas） 的

这些五个方法均包含描述直线或曲线所需的所有信息。 每个五个方法适用于前面紧邻的方法调用来建立的当前点一起使用。 例如，`LineTo`方法将添加一条直线到轮廓取决于当前点，因此参数`LineTo`是单一点。

`SKPath`类还定义了具有相同的名称作为这些六种方法，但使用的方法`R`开头：

- [`RMoveTo`](xref:SkiaSharp.SKPath.RMoveTo*)
- [`RLineTo`](xref:SkiaSharp.SKPath.RLineTo*)
- [`RArcTo`](xref:SkiaSharp.SKPath.RArcTo*)
- [`RCubicTo`](xref:SkiaSharp.SKPath.RCubicTo*)
- [`RQuadTo`](xref:SkiaSharp.SKPath.RQuadTo*)
- [`RConicTo`](xref:SkiaSharp.SKPath.RConicTo*)

`R`代表*相对*。 这些方法都有相应的方法，而不必与相同的语法`R`但相对于当前点。 这些可以方便地绘制类似组成部分中多次调用的方法的路径。

轮廓线结尾再次调用`MoveTo`或`RMoveTo`，其中开始新的轮廓或调用`Close`，用于关闭轮廓。 `Close`方法自动追加一条直线从当前的点分布的第一个点并将路径标记为已关闭，这意味着它将呈现而无需任何笔划大写字母。

打开和关闭分布图之间的差异所示**两个三角形轮廓**页上，使用`SKPath`具有两个要呈现两个三角形的轮廓的对象。 第一个轮廓线是打开和关闭第二个。 下面是[ `TwoTriangleContoursPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/LinesAndPaths/TwoTriangleContoursPage.cs)类：

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

对的调用包含的第一个轮廓[ `MoveTo` ](xref:SkiaSharp.SKPath.MoveTo(System.Single,System.Single))使用 X 和 Y 坐标而非`SKPoint`值后, 跟三个对[ `LineTo` ](xref:SkiaSharp.SKPath.LineTo(System.Single,System.Single))要绘制的三条边三角形。 第二个 contour 具有只有两个调用`LineTo`但完成通过调用 contour [ `Close` ](xref:SkiaSharp.SKPath.Close)，用于关闭轮廓。 重要的区别是：

[![](paths-images/twotrianglecontours-small.png "三重的两个三角形轮廓页屏幕截图")](paths-images/twotrianglecontours-large.png#lightbox "带来三倍的两个三角形轮廓页屏幕截图")

正如您所看到的第一个轮廓显然是一系列的三个相互连接的直线，但结束时不会连接与的开始部分。 在顶部重叠两行。 第二个 contour 显然关闭并完成了一个更少`LineTo`调用因为`Close`方法自动添加最终行以关闭轮廓。

`SKCanvas` 只定义一个[ `DrawPath` ](xref:SkiaSharp.SKCanvas.DrawPath(SkiaSharp.SKPath,SkiaSharp.SKPaint))方法，该方法在本演示中调用了两次以填满并绘制路径。 所有轮廓进行都填充，甚至包括那些未关闭。 为了填充闭合的路径，则假定一条直线起点和终点的轮廓之间存在。 如果删除最后一个`LineTo`从第一个轮廓或删除`Close`从第二个分布，每个分布的调用都将拥有但会仅两个方面，就好像三角形填充。

`SKPath` 定义了许多其他方法和属性。 以下方法将整个分布图添加到路径，它可能已关闭或未关闭根据该方法：

- [`AddRect`](xref:SkiaSharp.SKPath.AddRect*)
- [`AddRoundedRect`](xref:SkiaSharp.SKPath.AddRoundedRect(SkiaSharp.SKRect,System.Single,System.Single,SkiaSharp.SKPathDirection))
- [`AddCircle`](xref:SkiaSharp.SKPath.AddCircle(System.Single,System.Single,System.Single,SkiaSharp.SKPathDirection))
- [`AddOval`](xref:SkiaSharp.SKPath.AddOval(SkiaSharp.SKRect,SkiaSharp.SKPathDirection))
- [`AddArc`](xref:SkiaSharp.SKPath.AddArc(SkiaSharp.SKRect,System.Single,System.Single)) 若要添加一条曲线的椭圆圆周上
- [`AddPath`](xref:SkiaSharp.SKPath.AddPath*) 若要将另一路径添加到当前路径
- [`AddPathReverse`](xref:SkiaSharp.SKPath.AddPathReverse(SkiaSharp.SKPath)) 若要按相反的顺序添加另一个路径

请记住`SKPath`对象定义仅几何图形&mdash;一系列点和连接。 仅当`SKPath`与组合`SKPaint`对象是使用特定颜色、 笔划宽度等呈现的路径。 此外，请记住`SKPaint`对象传递给`DrawPath`方法定义特征的完整路径。 如果你想要绘制一些内容需要多个颜色，必须为每种颜色来使用一个单独的路径。

就像由描边端点定义的开始和结束的行的外观，通过定义两行之间的连接的外观*笔划联接*。 通过设置来指定这[ `StrokeJoin` ](xref:SkiaSharp.SKPaint.StrokeJoin)的属性`SKPaint`成员[ `SKStrokeJoin` ](xref:SkiaSharp.SKStrokeJoin)枚举：

- `Miter` 用于尖尖联接
- `Round` 用于圆角联接
- `Bevel` 已被截关闭联接

**笔划联接**页面的显示了这三个绘制笔画，代码类似于联接**笔划大写字母**页。 这是`PaintSurface`中的事件处理程序[ `StrokeJoinsPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/LinesAndPaths/StrokeJoinsPage.cs)类：

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

[![](paths-images/strokejoins-small.png "三重笔划联接页屏幕截图")](paths-images/strokejoins-large.png#lightbox "笔划联接页的三个屏幕截图")

斜接联接包含，其中用线条连接是尖角。 当在一个小角度加入两行时，斜接联接可能会变得相当长。 若要防止过长斜接联接，斜接联接的长度受限制的值[ `StrokeMiter` ](xref:SkiaSharp.SKPaint.StrokeMiter)属性的`SKPaint`。 超过此长度的斜接联接砍掉成为斜切接合。


## <a name="related-links"></a>相关链接

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （示例）](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
