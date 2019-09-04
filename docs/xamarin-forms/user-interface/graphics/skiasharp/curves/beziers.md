---
title: 贝塞尔曲线的三种类型
description: 此文介绍了如何使用 SkiaSharp 呈现在 Xamarin.Forms 应用程序中的三次方、 二次，和圆锥贝塞尔曲线，此示例代码进行了演示。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 8FE0F6DC-16BC-435F-9626-DD1790C0145A
author: davidbritch
ms.author: dabritch
ms.date: 05/25/2017
ms.openlocfilehash: 9c1ee2e036fc903c7fe8422a32fba44cc93d43f9
ms.sourcegitcommit: c9651cad80c2865bc628349d30e82721c01ddb4a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2019
ms.locfileid: "70228268"
---
# <a name="three-types-of-bzier-curves"></a>贝塞尔曲线的三种类型

[![下载示例](~/media/shared/download.png)下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_了解如何使用 SkiaSharp 呈现三次方、 二次，和圆锥贝塞尔曲线_

圣皮埃尔贝塞尔 （1910年 – 1999 年），汽车公司 Renault，计算机辅助设计的汽车正文使用曲线的法语工程师命名贝塞尔曲线。

贝塞尔曲线已知非常适合交互设计:它们表现得很&mdash;好, singularities 不会导致曲线变得无限大或难以&mdash;操作, 并且通常具

![样本贝塞尔曲线](beziers-images/beziersample.png)

贝塞尔曲线通常定义字符轮廓的基于计算机的字体。

有关的维基百科文章[**贝赛尔曲线**](https://en.wikipedia.org/wiki/B%C3%A9zier_curve)包含一些非常有用的背景信息。 术语*贝赛尔曲线*实际上是指一系列类似的曲线。 SkiaSharp 支持三种类型的名为的贝塞尔曲线*三次方*，则*二次*，并*圆锥*。 也称为是圆锥*rational 二次方程式*。

## <a name="the-cubic-bzier-curve"></a>三次方贝塞尔曲线

中的三次是大多数开发人员认为当贝塞尔曲线的使用者提供的贝塞尔曲线的类型。

您可以添加到的三次方贝赛尔曲线`SKPath`对象使用[ `CubicTo` ](xref:SkiaSharp.SKPath.CubicTo(SkiaSharp.SKPoint,SkiaSharp.SKPoint,SkiaSharp.SKPoint))具有三个方法`SKPoint`参数，或[ `CubicTo` ](xref:SkiaSharp.SKPath.CubicTo(System.Single,System.Single,System.Single,System.Single,System.Single,System.Single))具有单独重载`x`和`y`参数：

```csharp
public void CubicTo (SKPoint point1, SKPoint point2, SKPoint point3)

public void CubicTo (Single x1, Single y1, Single x2, Single y2, Single x3, Single y3)
```

当前点轮廓线的曲线开始。 完成三次方贝塞尔曲线由四个点定义：

- 启动点： 如果当前点轮廓，或 （0，0） 中`MoveTo`尚未调用
- 第一次控制点：`point1`在`CubicTo`调用
- 第二个控制点：`point2`在`CubicTo`调用
- 终结点：`point3`在`CubicTo`调用

生成的曲线的开始处开始并结束在终结点。 曲线通常不会经历的两个控制点;改为两个控制点更像磁铁拉取针对它们的曲线函数。

了解的三次方贝塞尔曲线的最佳方式是通过试验。 这种情况**贝塞尔曲线**页上，派生自`InteractivePage`。 [ **BezierCurvePage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/BezierCurvePage.xaml)文件实例化`SKCanvasView`和`TouchEffect`。 [ **BezierCurvePage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/BezierCurvePage.xaml.cs)代码隐藏文件中创建四个`TouchPoint`其构造函数中的对象。 `PaintSurface`事件处理程序将创建`SKPath`呈现基于四个的贝赛尔曲线`TouchPoint`对象，并还将以点分隔的切线从控件点绘制到的终结点：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    // Draw path with cubic Bezier curve
    using (SKPath path = new SKPath())
    {
        path.MoveTo(touchPoints[0].Center);
        path.CubicTo(touchPoints[1].Center,
                     touchPoints[2].Center,
                     touchPoints[3].Center);

        canvas.DrawPath(path, strokePaint);
    }

    // Draw tangent lines
    canvas.DrawLine(touchPoints[0].Center.X,
                    touchPoints[0].Center.Y,
                    touchPoints[1].Center.X,
                    touchPoints[1].Center.Y, dottedStrokePaint);

    canvas.DrawLine(touchPoints[2].Center.X,
                    touchPoints[2].Center.Y,
                    touchPoints[3].Center.X,
                    touchPoints[3].Center.Y, dottedStrokePaint);

    foreach (TouchPoint touchPoint in touchPoints)
    {
       touchPoint.Paint(canvas);
    }
}
```

运行此处：

[![贝塞尔曲线页面的三向屏幕截图](beziers-images/beziercurve-small.png)](beziers-images/beziercurve-large.png#lightbox)

从数学上，该曲线是三次多项式的次数。 曲线最相交处的三个点的直线。 在开始点，该曲线始终是第一个控制点的切线，然后在相同的方向，一条直线从一开始点。 在终结点，该曲线始终是终结点的切线，然后在相同的方向，一条直线从第二个管理点。

三次方贝塞尔曲线始终受连接四个点凸四边形。 这称为*凸球面*。 如果起点和终点之间的直线上位于控点，然后贝赛尔曲线将呈现为一条直线。 但如第三个屏幕截图中所示，曲线可以还跨本身。

路径 contour 可以包含多个连接三次方贝塞尔曲线，但两条三次方贝塞尔曲线之间的连接将仅当以下三个点共线平滑 （即，位于上一条直线）：

- 第一条曲线的第二个控制点
- 第一条曲线，它也是第二条曲线的起始点的终结点
- 第二条曲线的第一个控制点

在下一篇文章中对[ **SVG 路径数据**](~/xamarin-forms/user-interface/graphics/skiasharp/curves/path-data.md)，您会发现一个工具用于简化平滑已连接的贝塞尔曲线的定义。

有时它可用于了解呈现三次方贝塞尔曲线的基础参数等式。 有关*t*范围从 0 到 1，以下参数方程确定如下所示：

点 = (1 – t) ³x₀ + 3t (1 – t) ²x₁ + 3t² (1 – t) x₂ + t³x₃

y(t) = (1 – t) ³y₀ + 3t (1 – t) ²y₁ + 3t² (1 – t) y₂ + t³y₃

最高的 3 指数确认这些是三次方 polynomials。 很容易地验证，当`t`等于 0，则点为 (x₀，y₀)，即起始点，以及何时`t`等于 1，该点为 (x₃，y₃)，这是终结点。 附近的起始点 (的低值的`t`)，（x₁，y₁） 的第一个控制点具有强生效，并且附近终结点 (' t 的高值) 第二个控制点 （x₂，y₂） 具有很大影响。

## <a name="bezier-curve-approximation-to-circular-arcs"></a>贝塞尔曲线的近似圆弧

有时很方便地使用贝塞尔曲线来呈现一个圆弧。三次方贝塞尔曲线可估计一个圆弧很好地最多每个季度圆形，因此四个已连接的贝塞尔曲线可定义整个圆圈。 在发布超过 25 年的两篇文章中讨论此近似值：

> Tor Dokken 等，"准确的曲度连续贝塞尔曲线由圆圈估算"*计算机辅助几何设计 7* (1990)，33: 41。

> Michael Goldapp、"三次方 Polynomials 通过圆弧的近似"*计算机辅助几何设计 8* （1991 年） 227 238。

下图显示了四个点标记为`pto`， `pt1`， `pt2`，和`pt3`定义近似于一个圆弧的贝赛尔曲线 （以红色显示）：

![具有贝塞尔曲线的圆弧的近似值](beziers-images/bezierarc45.png)

从起点和终点的行控点到都正切值到圆和贝塞尔曲线，并且它们具有的长度*L*。上面提到的第一篇文章指示最佳的贝塞尔曲线近似于一个圆弧时该长度*L*计算如下：

L = 4 × tan(α / 4) / 3

因此 L 等于 0.265 图中显示的 45 度的角度。 在代码中，此值将乘以圆的所需的半径。

**贝塞尔圆弧**页，可以尝试使用定义的贝赛尔曲线来近似圆弧的角度范围最多 180 度。 [ **BezierCircularArcPage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/BezierCircularArcPage.xaml)文件实例化`SKCanvasView`和`Slider`用于选择表示的角度。 `PaintSurface`中的事件处理程序[ **BezierCircularArgPage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/BezierCircularArcPage.xaml.cs)代码隐藏文件中使用转换来设置到画布的中心点 （0，0）。 它绘制圆形以进行比较，该点为中心，然后计算贝赛尔曲线的两个控制点：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    // Translate to center
    canvas.Translate(info.Width / 2, info.Height / 2);

    // Draw the circle
    float radius = Math.Min(info.Width, info.Height) / 3;
    canvas.DrawCircle(0, 0, radius, blackStroke);

    // Get the value of the Slider
    float angle = (float)angleSlider.Value;

    // Calculate length of control point line
    float length = radius * 4 * (float)Math.Tan(Math.PI * angle / 180 / 4) / 3;

    // Calculate sin and cosine for half that angle
    float sin = (float)Math.Sin(Math.PI * angle / 180 / 2);
    float cos = (float)Math.Cos(Math.PI * angle / 180 / 2);

    // Find the end points
    SKPoint point0 = new SKPoint(-radius * sin, radius * cos);
    SKPoint point3 = new SKPoint(radius * sin, radius * cos);

    // Find the control points
    SKPoint point0Normalized = Normalize(point0);
    SKPoint point1 = point0 + new SKPoint(length * point0Normalized.Y,
                                          -length * point0Normalized.X);

    SKPoint point3Normalized = Normalize(point3);
    SKPoint point2 = point3 + new SKPoint(-length * point3Normalized.Y,
                                          length * point3Normalized.X);

    // Draw the points
    canvas.DrawCircle(point0.X, point0.Y, 10, blackFill);
    canvas.DrawCircle(point1.X, point1.Y, 10, blackFill);
    canvas.DrawCircle(point2.X, point2.Y, 10, blackFill);
    canvas.DrawCircle(point3.X, point3.Y, 10, blackFill);

    // Draw the tangent lines
    canvas.DrawLine(point0.X, point0.Y, point1.X, point1.Y, dottedStroke);
    canvas.DrawLine(point3.X, point3.Y, point2.X, point2.Y, dottedStroke);

    // Draw the Bezier curve
    using (SKPath path = new SKPath())
    {
        path.MoveTo(point0);
        path.CubicTo(point1, point2, point3);
        canvas.DrawPath(path, redStroke);
    }
}

// Vector methods
SKPoint Normalize(SKPoint v)
{
    float magnitude = Magnitude(v);
    return new SKPoint(v.X / magnitude, v.Y / magnitude);
}

float Magnitude(SKPoint v)
{
    return (float)Math.Sqrt(v.X * v.X + v.Y * v.Y);
}

```

起点和终点 (`point0`和`point3`) 基于该圆形的普通参数等式计算。 因为圆的中心 （0，0），这些点可以也被视为径向向量从圆的中心到周长。 控制点是在行的正切值为圆圈，因此可以为这些径向向量直角。 直角到另一个向量是只需使用交换的 X 和 Y 坐标和负所做的其中一个原始向量。

下面是运行使用不同的角度的程序：

[![贝塞尔圆弧页面的三向屏幕截图](beziers-images/beziercirculararc-small.png)](beziers-images/beziercirculararc-large.png#lightbox)

仔细查看第三个屏幕截图中，并且将看到贝赛尔曲线值得注意的是偏离一个半圆，当角度 180 度，但 iOS 屏幕显示，它看起来非常适合每个季度圆圈可以很好地时表示的角度为 90 度。

四分之一圆面向此类时，则计算两个控制点的坐标是非常简单：

![贝塞尔曲线的近似值](beziers-images/bezierarc90.png)

如果圆的半径为 100，则*L* 55，并位于要记住的简单号。

**求平方圆**页之间进行动画处理之间一个圆圈和正方形图。 四个坐标此数组定义中的第一列中所示的贝塞尔曲线近似等效于该圆形[ `SquaringTheCirclePage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/SquaringTheCirclePage.cs)类：

```csharp
public class SquaringTheCirclePage : ContentPage
{
    SKPoint[,] points =
    {
        { new SKPoint(   0,  100), new SKPoint(     0,    125), new SKPoint() },
        { new SKPoint(  55,  100), new SKPoint( 62.5f,  62.5f), new SKPoint() },
        { new SKPoint( 100,   55), new SKPoint( 62.5f,  62.5f), new SKPoint() },
        { new SKPoint( 100,    0), new SKPoint(   125,      0), new SKPoint() },
        { new SKPoint( 100,  -55), new SKPoint( 62.5f, -62.5f), new SKPoint() },
        { new SKPoint(  55, -100), new SKPoint( 62.5f, -62.5f), new SKPoint() },
        { new SKPoint(   0, -100), new SKPoint(     0,   -125), new SKPoint() },
        { new SKPoint( -55, -100), new SKPoint(-62.5f, -62.5f), new SKPoint() },
        { new SKPoint(-100,  -55), new SKPoint(-62.5f, -62.5f), new SKPoint() },
        { new SKPoint(-100,    0), new SKPoint(  -125,      0), new SKPoint() },
        { new SKPoint(-100,   55), new SKPoint(-62.5f,  62.5f), new SKPoint() },
        { new SKPoint( -55,  100), new SKPoint(-62.5f,  62.5f), new SKPoint() },
        { new SKPoint(   0,  100), new SKPoint(     0,    125), new SKPoint() }
    };
    ...
}
```

第二列包含四个定义其区域大约是圆的面积相同的正方形的贝塞尔曲线的坐标。 (绘制正方形*确切*为给定的圆形区域是经典无法解决的几何问题[求平方圆](https://en.wikipedia.org/wiki/Squaring_the_circle)。)用于呈现带有贝塞尔曲线的方框，各段曲线的两个控制点相同，并且它们是共线的开始和结束点，因此贝赛尔曲线呈现为一条直线。

有关动画的内插值是数组的第三个列。 16 毫秒，将计时器设置为页面和`PaintSurface`按照这个速率调用处理程序：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    canvas.Translate(info.Width / 2, info.Height / 2);
    canvas.Scale(Math.Min(info.Width / 300, info.Height / 300));

    // Interpolate
    TimeSpan timeSpan = new TimeSpan(DateTime.Now.Ticks);
    float t = (float)(timeSpan.TotalSeconds % 3 / 3);   // 0 to 1 every 3 seconds
    t = (1 + (float)Math.Sin(2 * Math.PI * t)) / 2;     // 0 to 1 to 0 sinusoidally

    for (int i = 0; i < 13; i++)
    {
        points[i, 2] = new SKPoint(
            (1 - t) * points[i, 0].X + t * points[i, 1].X,
            (1 - t) * points[i, 0].Y + t * points[i, 1].Y);
    }

    // Create the path and draw it
    using (SKPath path = new SKPath())
    {
        path.MoveTo(points[0, 2]);

        for (int i = 1; i < 13; i += 3)
        {
            path.CubicTo(points[i, 2], points[i + 1, 2], points[i + 2, 2]);
        }
        path.Close();

        canvas.DrawPath(path, cyanFill);
        canvas.DrawPath(path, blueStroke);
    }
}
```

基于 sinusoidally 振荡值的插入点`t`。 内插的点然后用于构造一系列的四个已连接的贝塞尔曲线。 下面是运行动画：

[![求圆页面](beziers-images/squaringthecircle-small.png)](beziers-images/squaringthecircle-large.png#lightbox)

此类动画就不可能无需通过算法灵活，可以呈现为循环弧线和直线的曲线。

**贝塞尔无穷大**页还会利用的贝塞尔曲线的近似圆弧的功能。下面是`PaintSurface`处理程序[ `BezierInfinityPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/BezierInfinityPage.cs)类：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    using (SKPath path = new SKPath())
    {
        path.MoveTo(0, 0);                                // Center
        path.CubicTo(  50,  -50,   95, -100,  150, -100); // To top of right loop
        path.CubicTo( 205, -100,  250,  -55,  250,    0); // To far right of right loop
        path.CubicTo( 250,   55,  205,  100,  150,  100); // To bottom of right loop
        path.CubicTo(  95,  100,   50,   50,    0,    0); // Back to center  
        path.CubicTo( -50,  -50,  -95, -100, -150, -100); // To top of left loop
        path.CubicTo(-205, -100, -250,  -55, -250,    0); // To far left of left loop
        path.CubicTo(-250,   55, -205,  100, -150,  100); // To bottom of left loop
        path.CubicTo( -95,  100,  -50,   50,    0,    0); // Back to center
        path.Close();

        SKRect pathBounds = path.Bounds;
        canvas.Translate(info.Width / 2, info.Height / 2);
        canvas.Scale(0.9f * Math.Min(info.Width / pathBounds.Width,
                                     info.Height / pathBounds.Height));

        using (SKPaint paint = new SKPaint())
        {
            paint.Style = SKPaintStyle.Stroke;
            paint.Color = SKColors.Blue;
            paint.StrokeWidth = 5;

            canvas.DrawPath(path, paint);
        }
    }
}
```

它可能是非常好的练习来绘制图形纸上的这些坐标以查看它们如何相关。 无穷符号居中围绕点 （0，0），并在两个循环使用的中心 （–150，0） 和 （150，0） 和半径为 100。 中的一系列`CubicTo`命令，可以看到 X 坐标的控制点承接 –95 和 –205 的值 （这些值是 –150 加号和减号 55），205 和 95 (150 加号和减号 55)、 250 和左右两侧的 –250。 唯一的例外是无穷符号有超过本身在中心。 在这种情况下，控制点具有 50 和 – 50 若要查看中心附近的曲线下的组合使用的坐标。

下面是无穷符号：

[![贝塞尔无穷页的三向屏幕截图](beziers-images/bezierinfinity-small.png)](beziers-images/bezierinfinity-large.png#lightbox)

它是比更流畅的中心呈现的无穷符号**弧线无穷大**页上，从[**绘制弧线的三个方法**](~/xamarin-forms/user-interface/graphics/skiasharp/curves/arcs.md)文章。

## <a name="the-quadratic-bzier-curve"></a>二次贝塞尔曲线

二次贝塞尔曲线具有只有一个控制点，并且只需三个点定义曲线： 起始点、 控制点和终结点。 以下参数方程确定是为三次方贝塞尔曲线，非常类似，只不过最高的指数是 2，因此曲线是二次多项式的次数：

点 = (1 – t) ²x₀ + 2t (1 – t) x₁ + t²x₂

y(t) = (1 – t) ²y₀ + 2t (1 – t) y₁ + t²y₂

若要添加到路径的二次贝塞尔曲线，请使用[ `QuadTo` ](xref:SkiaSharp.SKPath.QuadTo(SkiaSharp.SKPoint,SkiaSharp.SKPoint))方法或[ `QuadTo` ](xref:SkiaSharp.SKPath.QuadTo(System.Single,System.Single,System.Single,System.Single))重载具有单独`x`和`y`坐标：

```csharp
public void QuadTo (SKPoint point1, SKPoint point2)

public void QuadTo (Single x1, Single y1, Single x2, Single y2)
```

方法从当前位置添加一条曲线`point2`与`point1`作为控点。

您可以体验使用二次贝塞尔曲线**二次曲线**页上，非常类似于**贝塞尔曲线**页上，但它具有只有三个触摸点。 下面是`PaintSurface`处理程序中的[ **QuadraticCurve.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/QuadraticCurvePage.xaml.cs)代码隐藏文件：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    // Draw path with quadratic Bezier
    using (SKPath path = new SKPath())
    {
        path.MoveTo(touchPoints[0].Center);
        path.QuadTo(touchPoints[1].Center,
                    touchPoints[2].Center);

        canvas.DrawPath(path, strokePaint);
    }

    // Draw tangent lines
    canvas.DrawLine(touchPoints[0].Center.X,
                    touchPoints[0].Center.Y,
                    touchPoints[1].Center.X,
                    touchPoints[1].Center.Y, dottedStrokePaint);

    canvas.DrawLine(touchPoints[1].Center.X,
                    touchPoints[1].Center.Y,
                    touchPoints[2].Center.X,
                    touchPoints[2].Center.Y, dottedStrokePaint);

    foreach (TouchPoint touchPoint in touchPoints)
    {
        touchPoint.Paint(canvas);
    }
}
```

和此处运行：

[![二次曲线页面的三向屏幕截图](beziers-images/quadraticcurve-small.png)](beziers-images/quadraticcurve-large.png#lightbox)

点线是为在起点和终点，曲线的正切值和满足在控点。

如果所需的常规形状，曲线，但需要的只是一个管理点，而不是两个方便起见，二次贝塞尔曲线是很好。 二次贝塞尔曲线比任何其他曲线，这正是它在内部用于在 Skia 呈现椭圆弧形更高效地呈现。

但是，二次贝塞尔曲线的形状不椭圆，这就是为什么需要多个二次 Béziers 来近似椭圆弧。二次贝塞尔曲线是改用出一条抛物线的段。

## <a name="the-conic-bzier-curve"></a>圆锥贝塞尔曲线

圆锥贝赛尔曲线&mdash;也称为合理的二次贝塞尔曲线&mdash;是一个相对较新的贝塞尔曲线的系列。 二次贝塞尔曲线，如合理的二次贝塞尔曲线涉及到起始点、 终结点和一个控制点。 但还需要合理的二次贝塞尔曲线*权重*值。 它称为*rational*二次，因为参数公式涉及比率。

以下参数方程确定 X 和 Y 是共享相同的分母的比率。 下面是有关分母的公式*t*介于 0 与 1 和权重值为*w*:

d(t) = (1 – t) ² + 2wt(1 – t) + t²

从理论上讲，合理的二次可能涉及到三个单独的权重值，一个用于每个这三个术语，但这些可以简化为只是一个中间一词的权重值。

只不过中间术语还包括权重值，并且表达式除以分母的 X 和 Y 坐标的以下参数方程确定是类似于二次贝塞尔曲线的以下参数方程确定：

点 = ((1 – t) ²x₀ + 2wt (1 – t) x₁ + t²x₂)) ÷ d(t)

y(t) = ((1 – t) ²y₀ + 2wt (1 – t) y₁ + t²y₂)) ÷ d(t)

也称为 rational 二次贝塞尔曲线*conics*因为它们完全可以代表任何圆锥部分的段&mdash;hyperbolas、 条抛物线合理组合、 椭圆和圆。

若要为路径添加合理的二次贝塞尔曲线，请使用[ `ConicTo` ](xref:SkiaSharp.SKPath.ConicTo(SkiaSharp.SKPoint,SkiaSharp.SKPoint,System.Single))方法或[ `ConicTo` ](xref:SkiaSharp.SKPath.ConicTo(System.Single,System.Single,System.Single,System.Single,System.Single))重载具有单独`x`和`y`坐标：

```csharp
public void ConicTo (SKPoint point1, SKPoint point2, Single weight)

public void ConicTo (Single x1, Single y1, Single x2, Single y2, Single weight)
```

请注意，最终`weight`参数。

**圆锥曲线**页，可以尝试使用这些曲线。 `ConicCurvePage` 类派生自 `InteractivePage`。 [ **ConicCurvePage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ConicCurvePage.xaml)文件实例化`Slider`选择 – 2 到 2 之间的权重值。 [ **ConicCurvePage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ConicCurvePage.xaml.cs)代码隐藏文件创建三个`TouchPoint`对象，和`PaintSurface`处理程序只需呈现带有切线到控件生成的曲线要点：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    // Draw path with conic curve
    using (SKPath path = new SKPath())
    {
        path.MoveTo(touchPoints[0].Center);
        path.ConicTo(touchPoints[1].Center,
                     touchPoints[2].Center,
                     (float)weightSlider.Value);

        canvas.DrawPath(path, strokePaint);
    }

    // Draw tangent lines
    canvas.DrawLine(touchPoints[0].Center.X,
                    touchPoints[0].Center.Y,
                    touchPoints[1].Center.X,
                    touchPoints[1].Center.Y, dottedStrokePaint);

    canvas.DrawLine(touchPoints[1].Center.X,
                    touchPoints[1].Center.Y,
                    touchPoints[2].Center.X,
                    touchPoints[2].Center.Y, dottedStrokePaint);

    foreach (TouchPoint touchPoint in touchPoints)
    {
        touchPoint.Paint(canvas);
    }
}
```

运行此处：

[![圆锥曲线页面的三向屏幕截图](beziers-images/coniccurve-small.png)](beziers-images/coniccurve-large.png#lightbox)

正如您所看到的看起来的权重是更高版本时拉取向更多的曲线的控制点。 权重为零，曲线将成为一条直线的起始点从终结点。

从理论上讲，负权重允许，以及导致曲线弄弯*消失*从控点。 但是，权重为-1 或以下原因中的参数化的等式，以变为负值的特定值的分母*t*。 可能出于此原因，负权重，将忽略`ConicTo`方法。 **圆锥曲线**程序允许您设置负权重，但您可以看到通过进行试验，负权重具有相同的效果的权重为零，并且导致一条直线呈现。

是很容易派生的控点和要使用粗细`ConicTo`方法，以便最多绘制一个圆弧 （但不是包括） 一个半圆。 在下图中，从起点和终点的切线满足在控点。

![圆弧的圆锥弧线渲染](beziers-images/conicarc.png)

您可以使用三角函数来确定控制点与圆心中心之间的距离:它是圆的半径除以α角度的余弦值。 若要开始和结束点之间绘制一个圆弧，将权重设置为该同一一半角度的余弦。 请注意，是否角是 180 度，然后切线永远不会满足和权重为零。 但对于角度小于 180 度、 数学计算工作正常。

**圆锥圆弧**页说明了这一点。 [ **ConicCircularArc.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ConicCircularArcPage.xaml)文件实例化`Slider`用于选择表示的角度。 `PaintSurface`处理程序中的[ **ConicCircularArc.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ConicCircularArcPage.xaml.cs)代码隐藏文件计算的控制点和重量：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    // Translate to center
    canvas.Translate(info.Width / 2, info.Height / 2);

    // Draw the circle
    float radius = Math.Min(info.Width, info.Height) / 4;
    canvas.DrawCircle(0, 0, radius, blackStroke);

    // Get the value of the Slider
    float angle = (float)angleSlider.Value;

    // Calculate sin and cosine for half that angle
    float sin = (float)Math.Sin(Math.PI * angle / 180 / 2);
    float cos = (float)Math.Cos(Math.PI * angle / 180 / 2);

    // Find the points and weight
    SKPoint point0 = new SKPoint(-radius * sin, radius * cos);
    SKPoint point1 = new SKPoint(0, radius / cos);
    SKPoint point2 = new SKPoint(radius * sin, radius * cos);
    float weight = cos;

    // Draw the points
    canvas.DrawCircle(point0.X, point0.Y, 10, blackFill);
    canvas.DrawCircle(point1.X, point1.Y, 10, blackFill);
    canvas.DrawCircle(point2.X, point2.Y, 10, blackFill);

    // Draw the tangent lines
    canvas.DrawLine(point0.X, point0.Y, point1.X, point1.Y, dottedStroke);
    canvas.DrawLine(point2.X, point2.Y, point1.X, point1.Y, dottedStroke);

    // Draw the conic
    using (SKPath path = new SKPath())
    {
        path.MoveTo(point0);
        path.ConicTo(point1, point2, weight);
        canvas.DrawPath(path, redStroke);
    }
}
```

正如您所看到的没有任何视觉区别之间`ConicTo`以红色显示的路径和基础圆圈显示供参考：

[![圆锥圆弧页面的三向屏幕截图](beziers-images/coniccirculararc-small.png)](beziers-images/coniccirculararc-large.png#lightbox)

但设置为 180 度和数学失败的角度。

遗憾的是这种情况下，`ConicTo`不支持负权重，因为从理论上讲 （基于以下参数方程确定），可以使用另一个调用到完成圆`ConicTo`使用相同的点，但负权重的值。 这将允许创建具有只使用两个整圆`ConicTo`曲线上任意角度之间 （但不是包括） 基于零度和 180 度。


## <a name="related-links"></a>相关链接

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
