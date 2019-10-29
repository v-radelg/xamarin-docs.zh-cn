---
title: 绘制弧线的三个方法
description: 本文介绍如何使用 SkiaSharp 以三种不同方式定义弧线，并使用示例代码对此进行演示。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: F1DA55E4-0182-4388-863C-5C340213BF3C
author: davidbritch
ms.author: dabritch
ms.date: 05/10/2017
ms.openlocfilehash: 56c2da48c596fa35dd1a7658a38eee23c939e2fd
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029535"
---
# <a name="three-ways-to-draw-an-arc"></a>绘制弧线的三个方法

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_了解如何使用 SkiaSharp 以三种不同的方式定义弧线_

圆弧是椭圆圆周上的曲线，如此无限大符号的圆角部分：

![无穷号](arcs-images/arcsample.png)

尽管该定义简单，但无法定义满足每个需求的弧线绘制函数，因此，在绘制圆弧的最佳方式的图形系统之间没有任何共识。出于此原因，`SKPath` 类不会将自身局限于一种方法。

`SKPath` 定义[`AddArc`](xref:SkiaSharp.SKPath.AddArc*)方法、五个不同的[`ArcTo`](xref:SkiaSharp.SKPath.ArcTo*)方法和两个相对[`RArcTo`](xref:SkiaSharp.SKPath.RArcTo*)方法。 这些方法分为三个类别，表示三种用于指定圆弧的不同方法。使用哪种方法取决于可用于定义弧线的信息，以及此 arc 如何与要绘制的其他图形一起使用。

## <a name="the-angle-arc"></a>圆弧

绘制弧的圆弧方法要求您指定一个矩形来限定椭圆。 此椭圆的圆周上的圆弧由表示圆弧起始位置和其长度的椭圆的中心表示。 两种不同的方法绘制圆弧。 下面是[`AddArc`](xref:SkiaSharp.SKPath.AddArc(SkiaSharp.SKRect,System.Single,System.Single))方法和[`ArcTo`](xref:SkiaSharp.SKPath.ArcTo(SkiaSharp.SKRect,System.Single,System.Single,System.Boolean))方法：

```csharp
public void AddArc (SKRect oval, Single startAngle, Single sweepAngle)

public void ArcTo (SKRect oval, Single startAngle, Single sweepAngle, Boolean forceMoveTo)
```

这些方法与 Android [`AddArc`](xref:Android.Graphics.Path.AddArc*)和 [`ArcTo`] x： ArcTo *）方法相同。 IOS [`AddArc`](xref:CoreGraphics.CGPath.AddArc(System.nfloat,System.nfloat,System.nfloat,System.nfloat,System.nfloat,System.Boolean))方法类似，但仅限于圆形圆周上的弧形，而不是通用化到椭圆。

这两种方法都以 `SKRect` 值开头，此值定义了椭圆的位置和大小：

![开始圆弧的椭圆](arcs-images/anglearcoval.png)

圆弧是此椭圆的圆周的一部分。

`startAngle` 参数是以度为单位的顺时针角度（以度为单位），相对于从椭圆中心到右侧绘制的水平直线。 `sweepAngle` 参数是相对于 `startAngle`的。 下面分别 `startAngle` 和 `sweepAngle` 值60度和100度：

![定义圆弧的角度](arcs-images/anglearcangles.png)

圆弧从开始角度开始。 其长度由扫描角度来控制。 下面以红色显示 arc：

![突出显示的圆弧](arcs-images/anglearchighlight.png)

通过 `AddArc` 或 `ArcTo` 方法添加到路径中的曲线只是椭圆圆周的一部分：

![圆弧本身](arcs-images/anglearc.png)

`startAngle` 或 `sweepAngle` 参数可以为负数：对于正值，圆弧以顺时针表示 `sweepAngle`，并逆时针表示负值。

但 *`AddArc` 不会定义闭合*轮廓。 如果在 `AddArc`之后调用 `LineTo`，则将从弧线的末尾到 `LineTo` 方法中的点绘制一条线，同时 `ArcTo`也是如此。

`AddArc` 会自动启动一个新的轮廓，在功能上等效于 `ArcTo` 具有 `true`的最终参数的调用：

```csharp
path.ArcTo (oval, startAngle, sweepAngle, true);
```

最后一个参数称为 `forceMoveTo`，它会在圆弧开始时有效地引发 `MoveTo` 调用。这会开始一个新的轮廓。 如果 `false`的最后一个参数，则不会出现这种情况：

```csharp
path.ArcTo (oval, startAngle, sweepAngle, false);
```

此版本的 `ArcTo` 绘制从弧线的当前位置到开始的直线。这意味着圆弧可以是较大等高线中间的某个位置。

使用 "**角弧**" 页，您可以使用两个滑块来指定开始和扫描角度。 XAML 文件实例化两个 `Slider` 元素和一个 `SKCanvasView`。 [**AngleArcPage.xaml.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/AngleArcPage.xaml.cs)文件中的 `PaintCanvas` 处理程序使用两个定义为字段的 `SKPaint` 对象来绘制椭圆和弧线：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    SKRect rect = new SKRect(100, 100, info.Width - 100, info.Height - 100);
    float startAngle = (float)startAngleSlider.Value;
    float sweepAngle = (float)sweepAngleSlider.Value;

    canvas.DrawOval(rect, outlinePaint);

    using (SKPath path = new SKPath())
    {
        path.AddArc(rect, startAngle, sweepAngle);
        canvas.DrawPath(path, arcPaint);
    }
}
```

正如您所看到的，开始角度和扫描角度都可以采用负值：

[![直角坐标页面的三向屏幕截图](arcs-images/anglearc-small.png)](arcs-images/anglearc-large.png#lightbox)

这种生成弧形的方法是算法的最简单方法，可以很容易地推导出用于描述该圆弧的参数方程。若要了解椭圆的大小和位置以及起始和扫描角度，可以使用简单三角函数计算弧线的起点和终点：

`x = oval.MidX + (oval.Width / 2) * cos(angle)`

`y = oval.MidY + (oval.Height / 2) * sin(angle)`

`angle` 值为 `startAngle` 或 `startAngle + sweepAngle`。

如果您知道要绘制的弧线的角度长度（例如，绘制饼图），则使用两个角度来定义弧线是最好的情况。 **分离型饼图**页面说明了这一点。 [`ExplodedPieChartPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ExplodedPieChartPage.cs)类使用内部类定义一些制造的数据和颜色：

```csharp
class ChartData
{
    public ChartData(int value, SKColor color)
    {
        Value = value;
        Color = color;
    }

    public int Value { private set; get; }

    public SKColor Color { private set; get; }
}

ChartData[] chartData =
{
    new ChartData(45, SKColors.Red),
    new ChartData(13, SKColors.Green),
    new ChartData(27, SKColors.Blue),
    new ChartData(19, SKColors.Magenta),
    new ChartData(40, SKColors.Cyan),
    new ChartData(22, SKColors.Brown),
    new ChartData(29, SKColors.Gray)
};

```

`PaintSurface` 处理程序首先遍历项以计算 `totalValues` 号。 从这个角度来看，它可以将每个项的大小确定为总计的小数部分，并将其转换为角度：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    int totalValues = 0;

    foreach (ChartData item in chartData)
    {
        totalValues += item.Value;
    }

    SKPoint center = new SKPoint(info.Width / 2, info.Height / 2);
    float explodeOffset = 50;
    float radius = Math.Min(info.Width / 2, info.Height / 2) - 2 * explodeOffset;
    SKRect rect = new SKRect(center.X - radius, center.Y - radius,
                             center.X + radius, center.Y + radius);

    float startAngle = 0;

    foreach (ChartData item in chartData)
    {
        float sweepAngle = 360f * item.Value / totalValues;

        using (SKPath path = new SKPath())
        using (SKPaint fillPaint = new SKPaint())
        using (SKPaint outlinePaint = new SKPaint())
        {
            path.MoveTo(center);
            path.ArcTo(rect, startAngle, sweepAngle, false);
            path.Close();

            fillPaint.Style = SKPaintStyle.Fill;
            fillPaint.Color = item.Color;

            outlinePaint.Style = SKPaintStyle.Stroke;
            outlinePaint.StrokeWidth = 5;
            outlinePaint.Color = SKColors.Black;

            // Calculate "explode" transform
            float angle = startAngle + 0.5f * sweepAngle;
            float x = explodeOffset * (float)Math.Cos(Math.PI * angle / 180);
            float y = explodeOffset * (float)Math.Sin(Math.PI * angle / 180);

            canvas.Save();
            canvas.Translate(x, y);

            // Fill and stroke the path
            canvas.DrawPath(path, fillPaint);
            canvas.DrawPath(path, outlinePaint);
            canvas.Restore();
        }

        startAngle += sweepAngle;
    }
}
```

为每个饼图切片创建新的 `SKPath` 对象。 该路径由一条线组成，然后是一个用于绘制圆弧的 `ArcTo`，另一行由 `Close` 调用的中心结果组成。 此程序通过将其从中心移出50像素来显示 "已分解" 的饼图切片。 该任务需要一个向量，其方向是每个切片的扫描角度的中点：

[分离型饼图页面的![三次屏幕截图](arcs-images/explodedpiechart-small.png)](arcs-images/explodedpiechart-large.png#lightbox)

若要查看不带 "爆炸式" 的外观，只需注释掉 `Translate` 调用：

[不带分解的分离型饼图页面的![三次屏幕截图](arcs-images/explodedpiechartunexploded-small.png)](arcs-images/explodedpiechartunexploded-large.png#lightbox)

## <a name="the-tangent-arc"></a>相切圆弧

`SKPath` 支持的第二种弧线类型为*相切圆弧*，因为圆弧是与两个连接的线相切的圆的周长。

向路径添加一个相切圆弧，并调用具有两个 `SKPoint` 参数的[`ArcTo`](xref:SkiaSharp.SKPath.ArcTo(SkiaSharp.SKPoint,SkiaSharp.SKPoint,System.Single))方法，或对点使用单独的 `Single` 参数的[`ArcTo`](xref:SkiaSharp.SKPath.ArcTo(System.Single,System.Single,System.Single,System.Single,System.Single))重载：

```csharp
public void ArcTo (SKPoint point1, SKPoint point2, Single radius)

public void ArcTo (Single x1, Single y1, Single x2, Single y2, Single radius)
```

此 `ArcTo` 方法类似于 PostScript [`arct`](https://www.adobe.com/products/postscript/pdfs/PLRM.pdf) （page 532）函数和 iOS [`AddArcToPoint`](xref:CoreGraphics.CGPath.AddArcToPoint(System.nfloat,System.nfloat,System.nfloat,System.nfloat,System.nfloat))方法。

`ArcTo` 方法涉及三个点：

- 等高线的当前点，或者，如果未调用 `MoveTo`，则为点（0，0）
- `ArcTo` 方法的第一个 point 参数，称为*角点*
- `ArcTo`的第二个 point 参数，称为*目标点*：

![开始相切圆弧的三个点](arcs-images/tangentarcthreepoints.png)

这三个点定义了两个连接的线条：

![连接切点的三个点的线条](arcs-images/tangentarcconnectinglines.png)

如果三个点为 colinear &mdash; 也就是说，如果它们位于同一直线上 &mdash; 将不绘制任何弧线。

`ArcTo` 方法还包括一个 `radius` 参数。 这会定义圆的半径：

![反正切圆弧的圆](arcs-images/tangentarccircle.png)

相切圆弧并不通用化椭圆。

如果两条线以任意角度相交，则可以在这些线条之间插入圆圈，使其与两行相切：

![两行之间的反正切圆弧](arcs-images/tangentarctangentcircle.png)

添加到等高线的曲线不触及 `ArcTo` 方法中指定的任何点。 它包含从当前点到第一个正切点的直线，以及在第二个相切点处结束的圆弧，此处显示为红色：

![两行之间突出显示的相切圆弧](arcs-images/tangentarchighlight.png)

下面是添加到等高线的最后直线和弧线：

![两行之间突出显示的相切圆弧](arcs-images/tangentarc.png)

等高线可以从第二个相切点继续。

"**相切弧线**" 页允许您试验切线。这是派生自[`InteractivePage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/InteractivePage.cs)的几个页面中的第一个页面，用于定义一些便利的 `SKPaint` 对象并执行 `TouchPoint` 处理：

```csharp
public class InteractivePage : ContentPage
{
    protected SKCanvasView baseCanvasView;
    protected TouchPoint[] touchPoints;

    protected SKPaint strokePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Black,
        StrokeWidth = 3
    };

    protected SKPaint redStrokePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Red,
        StrokeWidth = 15
    };

    protected SKPaint dottedStrokePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Black,
        StrokeWidth = 3,
        PathEffect = SKPathEffect.CreateDash(new float[] { 7, 7 }, 0)
    };

    protected void OnTouchEffectAction(object sender, TouchActionEventArgs args)
    {
        bool touchPointMoved = false;

        foreach (TouchPoint touchPoint in touchPoints)
        {
            float scale = baseCanvasView.CanvasSize.Width / (float)baseCanvasView.Width;
            SKPoint point = new SKPoint(scale * (float)args.Location.X,
                                        scale * (float)args.Location.Y);
            touchPointMoved |= touchPoint.ProcessTouchEvent(args.Id, args.Type, point);
        }

        if (touchPointMoved)
        {
            baseCanvasView.InvalidateSurface();
        }
    }
}
```

`TangentArcPage` 类派生自 `InteractivePage`。 [**TangentArcPage.xaml.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/TangentArcPage.xaml.cs)文件中的构造函数负责实例化和初始化 `touchPoints` 数组，并将 `baseCanvasView` （在 `InteractivePage`中）设置为[**TangentArcPage**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/TangentArcPage.xaml)文件中实例化的 `SKCanvasView` 对象：

```csharp
public partial class TangentArcPage : InteractivePage
{
    public TangentArcPage()
    {
        touchPoints = new TouchPoint[3];

        for (int i = 0; i < 3; i++)
        {
            TouchPoint touchPoint = new TouchPoint
            {
                Center = new SKPoint(i == 0 ? 100 : 500,
                                     i != 2 ? 100 : 500)
            };
            touchPoints[i] = touchPoint;
        }

        InitializeComponent();

        baseCanvasView = canvasView;
        radiusSlider.Value = 100;
    }

    void sliderValueChanged(object sender, ValueChangedEventArgs args)
    {
        if (canvasView != null)
        {
            canvasView.InvalidateSurface();
        }
    }
    ...
}
```

`PaintSurface` 处理程序使用 `ArcTo` 方法根据触控点和 `Slider`绘制圆弧，同时算法计算角度所基于的圆：

```csharp
public partial class TangentArcPage : InteractivePage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Draw the two lines that meet at an angle
        using (SKPath path = new SKPath())
        {
            path.MoveTo(touchPoints[0].Center);
            path.LineTo(touchPoints[1].Center);
            path.LineTo(touchPoints[2].Center);
            canvas.DrawPath(path, dottedStrokePaint);
        }

        // Draw the circle that the arc wraps around
        float radius = (float)radiusSlider.Value;

        SKPoint v1 = Normalize(touchPoints[0].Center - touchPoints[1].Center);
        SKPoint v2 = Normalize(touchPoints[2].Center - touchPoints[1].Center);

        double dotProduct = v1.X * v2.X + v1.Y * v2.Y;
        double angleBetween = Math.Acos(dotProduct);
        float hypotenuse = radius / (float)Math.Sin(angleBetween / 2);
        SKPoint vMid = Normalize(new SKPoint((v1.X + v2.X) / 2, (v1.Y + v2.Y) / 2));
        SKPoint center = new SKPoint(touchPoints[1].Center.X + vMid.X * hypotenuse,
                                     touchPoints[1].Center.Y + vMid.Y * hypotenuse);

        canvas.DrawCircle(center.X, center.Y, radius, this.strokePaint);

        // Draw the tangent arc
        using (SKPath path = new SKPath())
        {
            path.MoveTo(touchPoints[0].Center);
            path.ArcTo(touchPoints[1].Center, touchPoints[2].Center, radius);
            canvas.DrawPath(path, redStrokePaint);
        }

        foreach (TouchPoint touchPoint in touchPoints)
        {
            touchPoint.Paint(canvas);
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
}
```

下面是运行的**相切弧线**页面：

[相切弧线页面的![三次屏幕截图](arcs-images/tangentarc-small.png)](arcs-images/tangentarc-large.png#lightbox)

相切圆弧非常适合用于创建圆角，如圆角矩形。 由于 `SKPath` 已经包含一个 `AddRoundedRect` 方法，因此**舍入 Heptagon**页演示了如何使用 `ArcTo` 来舍入七个多边形的角。 （为任何常规多边形通用化代码。）

[`RoundedHeptagonPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/RoundedHeptagonPage.cs)类的 `PaintSurface` 处理程序包含一个 `for` 循环，用于计算 heptagon 的七个顶点的坐标，另一个用于计算这些顶点的七个边的中点。 然后，将使用这些位置点来构造路径：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    float cornerRadius = 100;
    int numVertices = 7;
    float radius = 0.45f * Math.Min(info.Width, info.Height);

    SKPoint[] vertices = new SKPoint[numVertices];
    SKPoint[] midPoints = new SKPoint[numVertices];

    double vertexAngle = -0.5f * Math.PI;       // straight up

    // Coordinates of the vertices of the polygon
    for (int vertex = 0; vertex < numVertices; vertex++)
    {
        vertices[vertex] = new SKPoint(radius * (float)Math.Cos(vertexAngle),
                                       radius * (float)Math.Sin(vertexAngle));
        vertexAngle += 2 * Math.PI / numVertices;
    }

    // Coordinates of the midpoints of the sides connecting the vertices
    for (int vertex = 0; vertex < numVertices; vertex++)
    {
        int prevVertex = (vertex + numVertices - 1) % numVertices;
        midPoints[vertex] = new SKPoint((vertices[prevVertex].X + vertices[vertex].X) / 2,
                                        (vertices[prevVertex].Y + vertices[vertex].Y) / 2);
    }

    // Create the path
    using (SKPath path = new SKPath())
    {
        // Begin at the first midpoint
        path.MoveTo(midPoints[0]);

        for (int vertex = 0; vertex < numVertices; vertex++)
        {
            SKPoint nextMidPoint = midPoints[(vertex + 1) % numVertices];

            // Draws a line from the current point, and then the arc
            path.ArcTo(vertices[vertex], nextMidPoint, cornerRadius);

            // Connect the arc with the next midpoint
            path.LineTo(nextMidPoint);
        }
        path.Close();

        // Render the path in the center of the screen
        using (SKPaint paint = new SKPaint())
        {
            paint.Style = SKPaintStyle.Stroke;
            paint.Color = SKColors.Blue;
            paint.StrokeWidth = 10;

            canvas.Translate(info.Width / 2, info.Height / 2);
            canvas.DrawPath(path, paint);
        }
    }
}

```

下面是正在运行的程序：

[![圆形 Heptagon 页面的三向屏幕截图](arcs-images/roundedheptagon-small.png)](arcs-images/roundedheptagon-large.png#lightbox)

## <a name="the-elliptical-arc"></a>椭圆弧

使用包含两个 `SKPoint` 参数的[`ArcTo`](xref:SkiaSharp.SKPath.ArcTo(SkiaSharp.SKPoint,System.Single,SkiaSharp.SKPathArcSize,SkiaSharp.SKPathDirection,SkiaSharp.SKPoint))方法，或具有不同 X 和 Y 坐标的[`ArcTo`](xref:SkiaSharp.SKPath.ArcTo(System.Single,System.Single,System.Single,SkiaSharp.SKPathArcSize,SkiaSharp.SKPathDirection,System.Single,System.Single))重载，将椭圆弧添加到路径中：

```csharp
public void ArcTo (SKPoint r, Single xAxisRotate, SKPathArcSize largeArc, SKPathDirection sweep, SKPoint xy)

public void ArcTo (Single rx, Single ry, Single xAxisRotate, SKPathArcSize largeArc, SKPathDirection sweep, Single x, Single y)
```

椭圆弧与可伸缩向量图形（SVG）和通用 Windows 平台[`ArcSegment`](/uwp/api/Windows.UI.Xaml.Media.ArcSegment/)类中包含的[椭圆弧](https://www.w3.org/TR/SVG11/paths.html#PathDataEllipticalArcCommands)一致。

这些 `ArcTo` 方法在两个点之间绘制一条圆弧（这是等高线的当前点）和 `ArcTo` 方法的最后一个参数（`xy` 参数或单独的 `x` 和 `y` 参数）：

![定义了椭圆弧的两个点](arcs-images/ellipticalarcpoints.png)

`ArcTo` 方法（`r`或 `rx` `ry`）的第一个 point 参数既不是点，而是指定椭圆的水平半径和垂直半径;

![定义了椭圆弧的椭圆](arcs-images/ellipticalarcellipse.png)

`xAxisRotate` 参数是旋转此椭圆的顺时针度数：

![定义椭圆弧的倾斜椭圆](arcs-images/ellipticalarctiltedellipse.png)

如果随后将此倾斜椭圆定位到了两个点，则点将由两个不同的弧线连接：

![第一组椭圆弧](arcs-images/ellipticalarcellipse1.png)

可以通过两种方式区分这两个弧线：顶部弧线大于下弧形，在从左到右绘制弧时，将沿顺时针方向绘制顶部弧，同时以逆时针方向绘制底部弧线。

还可以通过另一种方式在两个点之间调整椭圆的大小：

![第二组椭圆弧](arcs-images/ellipticalarcellipse2.png)

现在，顶部有一个较小的弧线，它以顺时针方向绘制，底部以逆时针方向绘制的较大弧线。

因此，这两个点可以通过由 "倾斜" 椭圆定义的弧来连接，这四种方式共有四种：

![全部四个椭圆弧](arcs-images/ellipticalarccolors.png)

这四个弧线由[`SKPathArcSize`](xref:SkiaSharp.SKPathArcSize)的四个组合和[`SKPathDirection`](xref:SkiaSharp.SKPathDirection)枚举类型参数的四个组合来区分 `ArcTo` 方法：

- red： SKPathArcSize 和 SKPathDirection
- 绿色： SKPathArcSize 和 SKPathDirection
- blue： SKPathArcSize 和 SKPathDirection
- 品红： SKPathArcSize 和 SKPathDirection

如果倾斜椭圆的大小不足以容纳在两个点之间，则会将其均匀缩放，直到其足够大。 这种情况下，只有两个唯一弧线连接两个点。 这些参数可以与 `SKPathDirection` 参数区分开来。

虽然这种定义弧线声音的方法在第一次遇到时复杂，但它还是允许使用旋转的椭圆来定义弧线的唯一方法，当您需要将弧线与等高线的其他部分集成时，这种方法通常是最简单的方法。

"**椭圆形弧线**" 页面允许您以交互方式设置两个点，以及椭圆的大小和旋转。 `EllipticalArcPage` 类派生自 `InteractivePage`， [**EllipticalArcPage.xaml.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/EllipticalArcPage.xaml.cs)代码隐藏文件中的 `PaintSurface` 处理程序绘制四个弧线：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    using (SKPath path = new SKPath())
    {
        int colorIndex = 0;
        SKPoint ellipseSize = new SKPoint((float)xRadiusSlider.Value,
                                          (float)yRadiusSlider.Value);
        float rotation = (float)rotationSlider.Value;

        foreach (SKPathArcSize arcSize in Enum.GetValues(typeof(SKPathArcSize)))
            foreach (SKPathDirection direction in Enum.GetValues(typeof(SKPathDirection)))
            {
                path.MoveTo(touchPoints[0].Center);
                path.ArcTo(ellipseSize, rotation,
                           arcSize, direction,
                           touchPoints[1].Center);

                strokePaint.Color = colors[colorIndex++];
                canvas.DrawPath(path, strokePaint);
                path.Reset();
            }
    }

    foreach (TouchPoint touchPoint in touchPoints)
    {
        touchPoint.Paint(canvas);
    }
}

```

此处运行的是：

[椭圆弧页面的![三次屏幕截图](arcs-images/ellipticalarc-small.png)](arcs-images/ellipticalarc-large.png#lightbox)

"**圆弧无穷**页" 使用椭圆弧绘制无穷号。 无穷大符号基于两个圆，半径为100单位，并以100单位分隔：

![两个圆圈](arcs-images/infinitycircles.png)

两行彼此相交：

![带有切线的两个圆圈](arcs-images/infinitycircleslines.png)

无穷号是这些圆和两行的组成部分的组合。 若要使用椭圆弧绘制无穷号，必须确定两条线与圆圈之间相切的坐标。

在其中一个圆圈中构造右矩形：

![带有切线和嵌入圆的两个圆圈](arcs-images/infinitytriangle.png)

圆的半径为100单位，三角形的斜边为150个单位，因此 angle α是100除以150或41.8 度的反正弦值（反正弦）。 三角形的另一侧的长度为41.8 度的余弦值150乘以112度的余弦值，也可由勾股定理定理计算。

然后，可以使用此信息计算相切点的坐标：

`x = 112·cos(41.8) = 83`

`y = 112·sin(41.8) = 75`

四个相切点都是在点（0，0）上以圆圈半径为100绘制无穷符号所必需的。

![具有相切线条和坐标的两个圆圈](arcs-images/infinitycoordinates.png)

[`ArcInfinityPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ArcInfinityPage.cs)类中的 `PaintSurface` 处理程序定位无穷符号，以便（0，0）点位于页面的中心，并将路径缩放为屏幕大小：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    using (SKPath path = new SKPath())
    {
        path.LineTo(83, 75);
        path.ArcTo(100, 100, 0, SKPathArcSize.Large, SKPathDirection.CounterClockwise, 83, -75);
        path.LineTo(-83, 75);
        path.ArcTo(100, 100, 0, SKPathArcSize.Large, SKPathDirection.Clockwise, -83, -75);
        path.Close();

        // Use path.TightBounds for coordinates without control points
        SKRect pathBounds = path.Bounds;

        canvas.Translate(info.Width / 2, info.Height / 2);
        canvas.Scale(Math.Min(info.Width / pathBounds.Width,
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

此代码使用 `SKPath` 的 `Bounds` 属性来确定无穷正弦的尺寸，以将其缩放为画布的大小：

[Arc 无穷页的![三次屏幕截图](arcs-images/arcinfinity-small.png)](arcs-images/arcinfinity-large.png#lightbox)

结果看起来有点小，这表示 `SKPath` 的 `Bounds` 属性报告的大小大于该路径。

在内部，Skia 使用多个二次贝塞尔曲线。 这些曲线（如下一节中所示）包含控制曲线绘制方式但不属于呈现曲线的控制点。 `Bounds` 属性包含这些控制点。

若要获得更严格的匹配，请使用不包括控制点的 `TightBounds` 属性。 下面是以横向模式运行的程序，并使用 `TightBounds` 属性获取路径边界：

[具有紧密型边界的 Arc 无穷页的![三次屏幕截图](arcs-images/arcinfinitytightbounds-small.png)](arcs-images/arcinfinitytightbounds-large.png#lightbox)

尽管弧线和直线之间的连接在数学上是平滑的，但从弧线到直线的更改似乎有点突然。 在下一篇文章中提供了[**三种类型的贝塞尔曲线**](beziers.md)的更好的无穷号。

## <a name="related-links"></a>相关链接

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
