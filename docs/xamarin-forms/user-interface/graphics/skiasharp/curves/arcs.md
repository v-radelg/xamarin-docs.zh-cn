---
title: 绘制弧线的三个方法
description: 本文介绍如何使用 SkiaSharp 定义弧线中三个不同的方式，并演示此示例代码。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: F1DA55E4-0182-4388-863C-5C340213BF3C
author: davidbritch
ms.author: dabritch
ms.date: 05/10/2017
ms.openlocfilehash: c93441bff02322fb938a67806ba7f5163c8c969e
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2019
ms.locfileid: "68511908"
---
# <a name="three-ways-to-draw-an-arc"></a>绘制弧线的三个方法

[![下载示例](~/media/shared/download.png)下载示例](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)

_了解如何使用 SkiaSharp 三种不同方式定义弧线_

一段弧线，是椭圆的一条曲线，如该无穷符号的圆角部分的圆周上：

![](arcs-images/arcsample.png "无穷大登录")

尽管该定义的简单起见，没有方法可以定义满足各种需求的弧线绘制函数，因此，在绘制弧线的最佳方式的图形系统之间不一致。出于此原因，`SKPath`类不限制本身只是一种方法。

`SKPath` 定义[ `AddArc` ](xref:SkiaSharp.SKPath.AddArc*)方法中，五个不同[ `ArcTo` ](xref:SkiaSharp.SKPath.ArcTo*)方法和两个相对[ `RArcTo` ](xref:SkiaSharp.SKPath.RArcTo*)方法。 这些方法分为三个类，指定一段弧线，它表示三种截然不同的方法。具体使用哪一个取决于可用来定义圆弧，并且此弧如何适应要绘制其他图形信息。

## <a name="the-angle-arc"></a>角度弧线

绘制弧线的角度弧线方法要求指定限定椭圆的矩形。 该椭圆圆周上的弧线的椭圆的中心从指示的弧线和其长度开头的角度指示。 两种不同方法绘制弧线角度。 这些是[ `AddArc` ](xref:SkiaSharp.SKPath.AddArc(SkiaSharp.SKRect,System.Single,System.Single))方法并[ `ArcTo` ](xref:SkiaSharp.SKPath.ArcTo(SkiaSharp.SKRect,System.Single,System.Single,System.Boolean))方法：

```csharp
public void AddArc (SKRect oval, Single startAngle, Single sweepAngle)

public void ArcTo (SKRect oval, Single startAngle, Single sweepAngle, Boolean forceMoveTo)
```

这些方法与 Android [`AddArc`](xref:Android.Graphics.Path.AddArc*)和 [`ArcTo`] x: ArcTo *) 方法相同。 IOS [ `AddArc` ](xref:CoreGraphics.CGPath.AddArc(System.nfloat,System.nfloat,System.nfloat,System.nfloat,System.nfloat,System.Boolean))方法类似，但仅限于弧线上圆的周长，而通用化到一个椭圆。

这两种方法开头`SKRect`值，该值定义的位置和大小的椭圆的：

![](arcs-images/anglearcoval.png "开始角度弧线的椭圆")

圆弧属于该椭圆的周长。

`startAngle`参数是以度为单位相对于椭圆的中心从右侧绘制一条横线顺时针角度。 `sweepAngle`自变量是相对于`startAngle`。 以下是`startAngle`和`sweepAngle`分别值 60 度的和 100 度：

![](arcs-images/anglearcangles.png "定义一段弧线，角度角度")

在开始角度开始圆弧。 其长度所依据的扫描角度。 圆弧以红色的如下所示：

![](arcs-images/anglearchighlight.png "突出显示的角度弧线")

添加到与路径曲线`AddArc`或`ArcTo`方法是只是该椭圆的周长的一部分：

![](arcs-images/anglearc.png "本身角度弧线")

`startAngle` 或`sweepAngle`参数可以为负数:圆弧对于正值`sweepAngle`为顺时针, 对于负值为逆时针。

但是， `AddArc` does*不*定义封闭的轮廓线。 如果您调用`LineTo`后`AddArc`，行从弧线末尾到中的点绘制`LineTo`方法，以及相同为 true 的`ArcTo`。

`AddArc` 自动启动新的轮廓和功能上等效于调用`ArcTo`的最后一个参数与`true`:

```csharp
path.ArcTo (oval, startAngle, sweepAngle, true);
```

最后一个参数名为`forceMoveTo`，并有效地导致`MoveTo`调用弧线的起始处。开始一个新轮廓的。 它是不是这样的最后一个参数与`false`:

```csharp
path.ArcTo (oval, startAngle, sweepAngle, false);
```

此版本的`ArcTo`从当前位置到弧线的起始绘制一条线。这意味着弧线可以某处是较大的轮廓的中间。

**角度弧线**页，可以使用两个滑块以指定开始日期和扫描角度。 XAML 文件实例化两个`Slider`元素和一个`SKCanvasView`。 `PaintCanvas`处理程序中的[ **AngleArcPage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/AngleArcPage.xaml.cs)文件绘制椭圆和弧线使用两个`SKPaint`对象定义为字段：

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

正如您所看到的开始角度和扫描角度可以执行对负值：

[![](arcs-images/anglearc-small.png "三重的角度弧线页屏幕截图")](arcs-images/anglearc-large.png#lightbox "带来三倍的角度弧线页屏幕截图")

生成一段弧线，此方法是通过算法最简单的并且易于派生的参数化的等式，用于描述弧线。如果知道的大小和位置的椭圆，并开始和扫描角度，起点和终点的圆弧可以使用计算简单三角函数：

`x = oval.MidX + (oval.Width / 2) * cos(angle)`

`y = oval.MidY + (oval.Height / 2) * sin(angle)`

`angle`值是`startAngle`或`startAngle + sweepAngle`。

两个角度来定义一段弧线，使用最适合于您知道你想要绘制，例如，要制作一个饼图弧线的 angular 长度的情况。 **分离型饼图**页说明了这一点。 [ `ExplodedPieChartPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ExplodedPieChartPage.cs)类使用的内部类可以定义一些虚构的数据和颜色：

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

`PaintSurface`处理程序首先循环访问要计算的项`totalValues`数。 它可以确定每个项的大小以的总分数，并将其转换为角度：

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

一个新`SKPath`为每个饼图扇区中创建对象。 路径都包含一行从中心，则`ArcTo`绘制圆弧和另一个行返回到的中心`Close`调用。 此程序显示"分离"饼图扇区，从而将它们解决所有问题从中心 50 个像素。 该任务需要为每个切片中的扫描角度的中点的方向的矢量：

[![](arcs-images/explodedpiechart-small.png "三重的分离型饼图页屏幕截图")](arcs-images/explodedpiechart-large.png#lightbox "带来三倍的分离型饼图页屏幕截图")

若要查看其外观没有"激增"的情况下，只需注释掉`Translate`调用：

[![](arcs-images/explodedpiechartunexploded-small.png "三重而无需爆炸式增长的分离型饼图页屏幕截图")](arcs-images/explodedpiechartunexploded-large.png#lightbox "三重而无需爆炸式增长的分离型饼图页屏幕截图")

## <a name="the-tangent-arc"></a>正切弧线

支持圆弧的第二个类型`SKPath`是*正切弧线*，因弧是正切为两个连接的线条的圆的周长而得名。

正切圆弧添加到路径上，通过调用[ `ArcTo` ](xref:SkiaSharp.SKPath.ArcTo(SkiaSharp.SKPoint,SkiaSharp.SKPoint,System.Single))具有两个方法`SKPoint`参数，或[ `ArcTo` ](xref:SkiaSharp.SKPath.ArcTo(System.Single,System.Single,System.Single,System.Single,System.Single))具有单独重载`Single`参数要点：

```csharp
public void ArcTo (SKPoint point1, SKPoint point2, Single radius)

public void ArcTo (Single x1, Single y1, Single x2, Single y2, Single radius)
```

这`ArcTo`方法是类似于 PostScript [ `arct` ](https://www.adobe.com/products/postscript/pdfs/PLRM.pdf) （页 532） 函数和 iOS [ `AddArcToPoint` ](xref:CoreGraphics.CGPath.AddArcToPoint(System.nfloat,System.nfloat,System.nfloat,System.nfloat,System.nfloat))方法。

`ArcTo`方法涉及到三个点：

- 如果当前点的轮廓或点 （0，0）`MoveTo`尚未调用
- 第一个点参数`ArcTo`方法，称为*角点*
- 第二个点参数`ArcTo`，称为*目标点*:

![](arcs-images/tangentarcthreepoints.png "开始正切弧线的三个点")

这 3 个点定义两个相互连接的直线：

![](arcs-images/tangentarcconnectinglines.png "连接正切弧线的三个点的线条")

如果三个点共线&mdash;也就是说，如果它们位于上一条直线&mdash;将绘制任何弧。

`ArcTo`方法还包括`radius`参数。 这将定义圆的半径：

![](arcs-images/tangentarccircle.png "正切弧的圆")

正切弧线的椭圆未通用化。

如果两个行满足在任意角度，该圆形可以插入这些行之间，这样就到这两个行的正切值：

![](arcs-images/tangentarctangentcircle.png "两行之间正切弧线圆形")

添加到轮廓线的曲线不触及中指定的点之一`ArcTo`方法。 它包含从当前的点到第一个切点和第二个在切点处，此处以红色显示结束一段弧线直线：

![](arcs-images/tangentarchighlight.png "之间的两行的突出显示切线图弧线")

下面是最后一条直线和添加到轮廓线的弧线：

![](arcs-images/tangentarc.png "之间的两行的突出显示切线图弧线")

可以从第二个切点继续轮廓。

**正切弧线**页面允许您试验正切圆弧。这是派生自的多个页面的第一个[ `InteractivePage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/InteractivePage.cs)，用于定义几个方便`SKPaint`对象，并执行`TouchPoint`处理：

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

`TangentArcPage` 类派生自 `InteractivePage`。 中的构造函数[ **TangentArcPage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/TangentArcPage.xaml.cs)文件都负责实例化和初始化`touchPoints`数组，并设置`baseCanvasView`(在`InteractivePage`) 到`SKCanvasView`在实例化对象[ **TangentArcPage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/TangentArcPage.xaml)文件：

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

`PaintSurface`处理程序使用`ArcTo`方法来绘制弧线基于接触点和一个`Slider`，但也通过算法计算的圆圈，表示的角度为基础：

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

下面是**正切弧线**运行页面：

[![](arcs-images/tangentarc-small.png "正切值弧线页的三个屏幕截图")](arcs-images/tangentarc-large.png#lightbox "带来三倍的正切值弧线页屏幕截图")

正切弧适合于创建圆的角，如圆角矩形。 因为`SKPath`已包括`AddRoundedRect`方法，**舍入 Heptagon**页说明如何使用`ArcTo`用于舍入七边的多边形的边角。 （代码通用化的任何正则多边形中）。

`PaintSurface`处理程序[ `RoundedHeptagonPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/RoundedHeptagonPage.cs)类包含一个`for`循环来计算七个 heptagon，若要计算的这些七个边指第二个顶点的坐标顶点。 这些点位置然后用于构造路径：

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

下面是运行的程序：

[![](arcs-images/roundedheptagon-small.png "舍入 Heptagon 页的三个屏幕截图")](arcs-images/roundedheptagon-large.png#lightbox "带来三倍的舍入 Heptagon 页屏幕截图")

## <a name="the-elliptical-arc"></a>椭圆弧

椭圆弧通过调用添加到路径[ `ArcTo` ](xref:SkiaSharp.SKPath.ArcTo(SkiaSharp.SKPoint,System.Single,SkiaSharp.SKPathArcSize,SkiaSharp.SKPathDirection,SkiaSharp.SKPoint))方法具有两个`SKPoint`参数，或[ `ArcTo` ](xref:SkiaSharp.SKPath.ArcTo(System.Single,System.Single,System.Single,SkiaSharp.SKPathArcSize,SkiaSharp.SKPathDirection,System.Single,System.Single))重载具有单独的 X 和 Y 坐标：

```csharp
public void ArcTo (SKPoint r, Single xAxisRotate, SKPathArcSize largeArc, SKPathDirection sweep, SKPoint xy)

public void ArcTo (Single rx, Single ry, Single xAxisRotate, SKPathArcSize largeArc, SKPathDirection sweep, Single x, Single y)
```

椭圆弧是与一致[椭圆弧](http://www.w3.org/TR/SVG11/paths.html#PathDataEllipticalArcCommands)包含在可缩放向量图形 (SVG) 和通用 Windows 平台[ `ArcSegment` ](/uwp/api/Windows.UI.Xaml.Media.ArcSegment/)类。

这些`ArcTo`方法的当前点轮廓线的两个点之间绘制一段弧线和最后一个参数`ArcTo`方法 (`xy`参数或单独`x`和`y`参数):

![](arcs-images/ellipticalarcpoints.png "两个定义椭圆弧的点")

第一个点参数`ArcTo`方法 (`r`，或`rx`和`ry`) 根本不是点，但改为指定的椭圆; 的水平和垂直半径

![](arcs-images/ellipticalarcellipse.png "定义椭圆弧椭圆")

`xAxisRotate`参数是要旋转该椭圆的顺时针角度数：

![](arcs-images/ellipticalarctiltedellipse.png "定义椭圆弧倾斜的椭圆")

如果该倾斜的椭圆然后定位，以便它涉及两个点，点由两个不同弧连接：

![](arcs-images/ellipticalarcellipse1.png "第一组椭圆弧形")

可以通过两种方式区分这两个弧线:顶部弧大于下弧形, 而当圆弧从左向右绘制时, 沿顺时针方向绘制顶部弧线, 而底部弧线以逆时针方向绘制。

还有可能以适合另一种方法的两个点之间的椭圆：

![](arcs-images/ellipticalarcellipse2.png "第二组的椭圆弧形")

现在没有的较小弧顺时针旋转，绘制的顶部和底部绘制的较大弧逆时针旋转。

因此可以通过定义总共四种方法中的倾斜椭圆弧连接这两个点：

![](arcs-images/ellipticalarccolors.png "所有四个椭圆弧形")

通过四种组合来区分这些四个弧[ `SKPathArcSize` ](xref:SkiaSharp.SKPathArcSize)并[ `SKPathDirection` ](xref:SkiaSharp.SKPathDirection)枚举的类型参数`ArcTo`方法：

- 红色SKPathArcSize 和 SKPathDirection
- 钩SKPathArcSize 和 SKPathDirection
- 蓝色SKPathArcSize 和 SKPathDirection
- 洋红色SKPathArcSize 和 SKPathDirection

如果倾斜的椭圆不足够大，以使其适合的两个点，然后与统一缩放直到有足够空间。 只有两个唯一弧线这种情况下连接两个点。 可以使用来区分这些`SKPathDirection`参数。

尽管此方法定义一段弧线，听起来很复杂第一次遇到，它允许定义一段弧线，旋转椭圆，使用的唯一方法，它是通常最简单的方法时需要与轮廓线的其他部分集成弧线。

**椭圆弧**页面允许您以交互方式设置的两个点的大小和椭圆的旋转。 `EllipticalArcPage`类派生自`InteractivePage`，并`PaintSurface`处理程序中的[ **EllipticalArcPage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/EllipticalArcPage.xaml.cs)代码隐藏文件绘制四个弧线：

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

运行此处：

[![](arcs-images/ellipticalarc-small.png "三个椭圆弧页屏幕截图")](arcs-images/ellipticalarc-large.png#lightbox "带来三倍的椭圆弧页屏幕截图")

**弧线无穷大**页使用椭圆弧绘制无穷符号。 无穷大登录基于两个圆圈，分隔 100 个单位 100 个单位的半径：

![](arcs-images/infinitycircles.png "两个圆")

跨越每个其他的两个行是到两个圆的正切值：

![](arcs-images/infinitycircleslines.png "带有切线的两个圆")

无穷符号是这些圈子和两行的部件的组合。 若要使用椭圆弧绘制无穷符号，必须确定其中的两个行是于圆形的正切值的坐标。

构造中的一个圆圈的右矩形：

![](arcs-images/infinitytriangle.png "使用切线和嵌入的圆圈的两个圆")

圆的半径为 100 个单位，三角形的斜边是 150 个单位，因此 α 的角度的反正弦值 （反正弦值） 为 100，150，通过或 41.8 度划分。 另一方的三角形的长度为 150 次 41.8 度的余弦或 112，也可以通过勾股定理计算。

然后可以使用此信息计算正切的点的坐标：

`x = 112·cos(41.8) = 83`

`y = 112·sin(41.8) = 75`

四个切点所是用圆半径为 100 点 （0，0） 上绘制居中无穷大登录所需的所有内容：

![](arcs-images/infinitycoordinates.png "使用切线和坐标的两个圆")

`PaintSurface`处理程序中的[ `ArcInfinityPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ArcInfinityPage.cs)类定位无穷符号，以便 （0，0） 点位于页上，在中心和缩放屏幕大小的路径：

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

该代码使用`Bounds`属性的`SKPath`来确定无穷大正弦值，以使其缩放到画布的大小的尺寸：

[![](arcs-images/arcinfinity-small.png "三重的弧线无穷大页屏幕截图")](arcs-images/arcinfinity-large.png#lightbox "带来三倍的弧线无穷大页屏幕截图")

结果看起来有点小，这表明`Bounds`属性的`SKPath`reporting 大于路径的大小。

在内部，Skia 近似圆弧使用多个二次贝塞尔曲线。 这些曲线 （正如您将看到在下一节中） 包含控制如何绘制曲线，但不属于呈现曲线的控制点。 `Bounds`属性包括这些控点。

若要获取更紧密的合适大小，请使用`TightBounds`属性，它不包括的控点。 下面是程序在横向模式下运行，以及使用`TightBounds`属性获取路径边界：

[![](arcs-images/arcinfinitytightbounds-small.png "三重通过紧密界限的弧线无穷大页屏幕截图")](arcs-images/arcinfinitytightbounds-large.png#lightbox "三重通过紧密界限的弧线无穷大页屏幕截图")

尽管弧线和直线之间的连接是数学上平滑，弧线直线的更改可能看起来有点突然发生。 更好的无穷符号在下一篇文章中显示给上[**三种类型的贝塞尔曲线**](beziers.md)。

## <a name="related-links"></a>相关链接

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （示例）](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
