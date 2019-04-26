---
title: 路径信息和枚举
description: 本文介绍如何获取有关 SkiaSharp 路径的信息和枚举的内容，并演示此示例代码。
ms.prod: xamarin
ms.assetid: 8E8C5C6A-F324-4155-8652-7A77D231B3E5
ms.technology: xamarin-skiasharp
author: davidbritch
ms.author: dabritch
ms.date: 09/12/2017
ms.openlocfilehash: b5d32f486b45005534adc8929caf6158d1c62a4b
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61357145"
---
# <a name="path-information-and-enumeration"></a>路径信息和枚举

[![下载示例](~/media/shared/download.png)下载示例](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)

_获取有关路径的信息和枚举的内容_

[ `SKPath` ](xref:SkiaSharp.SKPath)类定义一些属性和方法，可用于获取有关路径的信息。 [ `Bounds` ](xref:SkiaSharp.SKPath.Bounds)并[ `TightBounds` ](xref:SkiaSharp.SKPath.TightBounds)属性 （和相关的方法） 获取路径的度量的维度。 [ `Contains` ](xref:SkiaSharp.SKPath.Contains(System.Single,System.Single))方法允许您确定某个特定点位于路径中。

有时它可用于确定所有的直线和曲线构成路径的总长度。 计算此长度不是从算法上简单的任务，因此整个类名为[ `PathMeasure` ](xref:SkiaSharp.SKPathMeasure)专用于它。

还有有时很有用，若要获取所有绘制操作和构成路径的点。 首先，此工具可能看起来不必要：如果您的程序创建了路径，该程序已经知道的内容。 但是，你已了解路径也可以创建由[路径效果](~/xamarin-forms/user-interface/graphics/skiasharp/curves/effects.md)并通过转换[为路径的文本字符串](~/xamarin-forms/user-interface/graphics/skiasharp/curves/text-paths.md)。 此外可以获取所有绘制操作和组成这些路径的点。 一种可能性是算法转换应用到所有点，例如，若要使文字环绕半球：

![](information-images/pathenumerationsample.png "包装在半球上的文本")

## <a name="getting-the-path-length"></a>获取路径长度

在本文中[**路径和文本**](~/xamarin-forms/user-interface/graphics/skiasharp/curves/text-paths.md)了解了如何使用[ `DrawTextOnPath` ](xref:SkiaSharp.SKCanvas.DrawTextOnPath(System.String,SkiaSharp.SKPath,System.Single,System.Single,SkiaSharp.SKPaint))方法来绘制其基线遵循的路径的文本字符串。 但如果你想要调整文本大小，以便精确适合的路径？ 圆环绘制文本非常简单，因为圆的周长很容易计算。 但的椭圆的周长或贝塞尔曲线的长度并不那么简单。

[ `SKPathMeasure` ](xref:SkiaSharp.SKPathMeasure)类可帮助。 [构造函数](xref:SkiaSharp.SKPathMeasure.%23ctor(SkiaSharp.SKPath,System.Boolean,System.Single))接受`SKPath`自变量，并且[ `Length` ](xref:SkiaSharp.SKPathMeasure.Length)属性将显示其长度。

此类进行了演示**路径长度**示例中，基于**贝塞尔曲线**页。 [ **PathLengthPage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/PathLengthPage.xaml)文件派生`InteractivePage`和包括在触控界面：

```xaml
<local:InteractivePage xmlns="http://xamarin.com/schemas/2014/forms"
                       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                       xmlns:local="clr-namespace:SkiaSharpFormsDemos"
                       xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
                       xmlns:tt="clr-namespace:TouchTracking"
                       x:Class="SkiaSharpFormsDemos.Curves.PathLengthPage"
                       Title="Path Length">
    <Grid BackgroundColor="White">
        <skia:SKCanvasView x:Name="canvasView"
                           PaintSurface="OnCanvasViewPaintSurface" />
        <Grid.Effects>
            <tt:TouchEffect Capture="True"
                            TouchAction="OnTouchEffectAction" />
        </Grid.Effects>
    </Grid>
</local:InteractivePage>
```

[ **PathLengthPage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/PathLengthPage.xaml.cs)代码隐藏文件，可将四个触摸点，以定义终结点和三次方贝塞尔曲线的控制点。 三个字段定义文本字符串，`SKPaint`对象和文本的计算所得的宽度：

```csharp
public partial class PathLengthPage : InteractivePage
{
    const string text = "Compute length of path";

    static SKPaint textPaint = new SKPaint
    {
        Style = SKPaintStyle.Fill,
        Color = SKColors.Black,
        TextSize = 10,
    };

    static readonly float baseTextWidth = textPaint.MeasureText(text);
    ...
}
```

`baseTextWidth`字段是基于文本的宽度`TextSize`设置为 10。

`PaintSurface`处理程序绘制贝塞尔曲线，然后调整大小以适合其整个长度的文本：

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

        // Get path length
        SKPathMeasure pathMeasure = new SKPathMeasure(path, false, 1);

        // Find new text size
        textPaint.TextSize = pathMeasure.Length / baseTextWidth * 10;

        // Draw text on path
        canvas.DrawTextOnPath(text, path, 0, 0, textPaint);
    }
    ...
}
```

`Length`新创建的属性`SKPathMeasure`对象获取路径的长度。 路径长度除以`baseTextWidth`值 （这是基于文本大小为 10 的文本的宽度），然后再乘以 10 的基础文本大小。 结果是显示的文本沿该路径的新文本大小：

[![](information-images/pathlength-small.png "三重的路径长度页屏幕截图")](information-images/pathlength-large.png#lightbox "带来三倍的路径长度页屏幕截图")

贝塞尔曲线获取较长或更短，您可以看到更改文本大小。

## <a name="traversing-the-path"></a>遍历路径

`SKPathMeasure` 可以实现不止是度量值的路径的长度。 对于任何介于零和路径长度`SKPathMeasure`对象可以在该点获取路径，然后为路径曲线的正切值的位置。 正切值是可作为一个向量中的窗体`SKPoint`对象，或者作为对旋转封装在`SKMatrix`对象。 此处列出的方法`SKPathMeasure`的各不相同且灵活的方式获取此信息：

```csharp
Boolean GetPosition (Single distance, out SKPoint position)

Boolean GetTangent (Single distance, out SKPoint tangent)

Boolean GetPositionAndTangent (Single distance, out SKPoint position, out SKPoint tangent)

Boolean GetMatrix (Single distance, out SKMatrix matrix, SKPathMeasureMatrixFlags flag)
```

成员[ `SKPathMeasureMatrixFlags` ](xref:SkiaSharp.SKPathMeasureMatrixFlags)枚举是：

- `GetPosition`
- `GetTangent`
- `GetPositionAndTangent`

**脚踏车半管道**页之间进行动画处理看起来沿三次方贝塞尔曲线来回骑脚踏车上一个简图：

[![](information-images/unicyclehalfpipe-small.png "三重的马车半管道页屏幕截图")](information-images/unicyclehalfpipe-large.png#lightbox "带来三倍的马车半管道页屏幕截图")

`SKPaint`用于描画半管道和脚踏车对象定义中的字段为`UnicycleHalfPipePage`类。 此外定义是`SKPath`脚踏车对象：

```csharp
public class UnicycleHalfPipePage : ContentPage
{
    ...
    SKPaint strokePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        StrokeWidth = 3,
        Color = SKColors.Black
    };

    SKPath unicyclePath = SKPath.ParseSvgPathData(
        "M 0 0" +
        "A 25 25 0 0 0 0 -50" +
        "A 25 25 0 0 0 0 0 Z" +
        "M 0 -25 L 0 -100" +
        "A 15 15 0 0 0 0 -130" +
        "A 15 15 0 0 0 0 -100 Z" +
        "M -25 -85 L 25 -85");
    ...
}
```

此类包含的标准重写之一`OnAppearing`和`OnDisappearing`动画的方法。 `PaintSurface`处理程序创建后半部分管道的路径，然后绘制它。 `SKPathMeasure`然后创建对象，根据此路径：

```csharp
public class UnicycleHalfPipePage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPath pipePath = new SKPath())
        {
            pipePath.MoveTo(50, 50);
            pipePath.CubicTo(0, 1.25f * info.Height,
                             info.Width - 0, 1.25f * info.Height,
                             info.Width - 50, 50);

            canvas.DrawPath(pipePath, strokePaint);

            using (SKPathMeasure pathMeasure = new SKPathMeasure(pipePath))
            {
                float length = pathMeasure.Length;

                // Animate t from 0 to 1 every three seconds
                TimeSpan timeSpan = new TimeSpan(DateTime.Now.Ticks);
                float t = (float)(timeSpan.TotalSeconds % 5 / 5);

                // t from 0 to 1 to 0 but slower at beginning and end
                t = (float)((1 - Math.Cos(t * 2 * Math.PI)) / 2);

                SKMatrix matrix;
                pathMeasure.GetMatrix(t * length, out matrix,
                                      SKPathMeasureMatrixFlags.GetPositionAndTangent);

                canvas.SetMatrix(matrix);
                canvas.DrawPath(unicyclePath, strokePaint);
            }
        }
    }
}
```

`PaintSurface`处理程序计算的值`t`，是由从 0 到 1 每隔 5 秒。 然后，它使用`Math.Cos`函数以将其转换为值`t`，范围从 0 到 1 并返回到 0，其中 0 对应于马车开头在左上角，而 1 对应于马车右上角。 余弦函数会导致要慢的放在管道的顶部和底部最快的速度。

请注意，此值的`t`必须乘以的第一个参数的路径长度`GetMatrix`。 矩阵然后应用到`SKCanvas`对象，用于绘制的马车路径。

## <a name="enumerating-the-path"></a>枚举路径

两个嵌入的类`SKPath`允许您枚举路径的内容。 这些类是[ `SKPath.Iterator` ](xref:SkiaSharp.SKPath.Iterator)并[ `SKPath.RawIterator` ](xref:SkiaSharp.SKPath.RawIterator)。 两个类是非常相似，但`SKPath.Iterator`可以消除与长度为零，或接近零长度的路径中的元素。 `RawIterator`下面的示例中使用。

你可以获取类型的对象`SKPath.RawIterator`通过调用[ `CreateRawIterator` ](xref:SkiaSharp.SKPath.CreateRawIterator)方法`SKPath`。 枚举通过的路径通过重复调用来实现[ `Next` ](xref:SkiaSharp.SKPath.RawIterator.Next*)方法。 将四个数组传递给它`SKPoint`值：

```csharp
SKPoint[] points = new SKPoint[4];
...
SKPathVerb pathVerb = rawIterator.Next(points);
```

`Next`方法返回的成员[ `SKPathVerb` ](xref:SkiaSharp.SKPathVerb)枚举类型。 这些值表示特定的绘图命令的路径中。 插入数组中的有效点数目取决于此谓词：

- `Move` 使用单个点
- `Line` 使用两个点
- `Cubic` 具有四个点
- `Quad` 使用三个点
- `Conic` 使用三个点 (，调用[ `ConicWeight` ](xref:SkiaSharp.SKPath.RawIterator.ConicWeight*)的权重的方法)
- `Close` 使用一个点
- `Done`

`Done`谓词指示路径枚举已完成。

请注意，有没有`Arc`谓词。 这表示所有弧线将都转换为贝塞尔曲线时添加到的路径。

中的信息的一些`SKPoint`是冗余的数组。 例如，如果`Move`谓词后跟`Line`谓词，则伴随的两个点的第一个`Line`等同于`Move`点。 在实践中，此冗余是非常有帮助。 时你会获得`Cubic`谓词，它都伴有定义三次方贝塞尔曲线的所有四个点。 不需要保留当前的位置由上一个谓词。

但是，是有问题的谓词， `Close`。 此命令之前由建立轮廓线的开头从当前位置绘制一条直线`Move`命令。 理想情况下，`Close`谓词应提供这两个点而不是只是一个点。 更糟的是在于点附带`Close`谓词始终是 （0，0）。 当您通过路径进行枚举时，您可能需要保留`Move`点和当前的位置。

## <a name="enumerating-flattening-and-malforming"></a>枚举、 平展和 Malforming

属性有时更加理想应用算法转换到错误的路径以某种方式：

![](information-images/pathenumerationsample.png "包装在半球上的文本")

大多数这些字母包含的直线，但这些直线具有显然已篡改成曲线。 这是如何实现？

关键是原始的直线被拆分为一系列的较小的直线。 然后可以以不同方式形成一条曲线操作这些单独的较小直线。

若要使用此过程，帮助[ **SkiaSharpFormsDemos** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)示例包含一个静态[ `PathExtensions` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/PathExtensions.cs)类`Interpolate`分解的方法到大量较短的行的长度只能有一个单元的直线。 此外，该类包含三种类型的贝塞尔曲线转换为一系列的近似曲线的小直线的几种方法。 (在本文中介绍了参数化公式[**三种类型的贝塞尔曲线**](~/xamarin-forms/user-interface/graphics/skiasharp/curves/beziers.md)。)此过程称为_平展_曲线：

```csharp
static class PathExtensions
{
    ...
    static SKPoint[] Interpolate(SKPoint pt0, SKPoint pt1)
    {
        int count = (int)Math.Max(1, Length(pt0, pt1));
        SKPoint[] points = new SKPoint[count];

        for (int i = 0; i < count; i++)
        {
            float t = (i + 1f) / count;
            float x = (1 - t) * pt0.X + t * pt1.X;
            float y = (1 - t) * pt0.Y + t * pt1.Y;
            points[i] = new SKPoint(x, y);
        }

        return points;
    }

    static SKPoint[] FlattenCubic(SKPoint pt0, SKPoint pt1, SKPoint pt2, SKPoint pt3)
    {
        int count = (int)Math.Max(1, Length(pt0, pt1) + Length(pt1, pt2) + Length(pt2, pt3));
        SKPoint[] points = new SKPoint[count];

        for (int i = 0; i < count; i++)
        {
            float t = (i + 1f) / count;
            float x = (1 - t) * (1 - t) * (1 - t) * pt0.X +
                        3 * t * (1 - t) * (1 - t) * pt1.X +
                        3 * t * t * (1 - t) * pt2.X +
                        t * t * t * pt3.X;
            float y = (1 - t) * (1 - t) * (1 - t) * pt0.Y +
                        3 * t * (1 - t) * (1 - t) * pt1.Y +
                        3 * t * t * (1 - t) * pt2.Y +
                        t * t * t * pt3.Y;
            points[i] = new SKPoint(x, y);
        }

        return points;
    }

    static SKPoint[] FlattenQuadratic(SKPoint pt0, SKPoint pt1, SKPoint pt2)
    {
        int count = (int)Math.Max(1, Length(pt0, pt1) + Length(pt1, pt2));
        SKPoint[] points = new SKPoint[count];

        for (int i = 0; i < count; i++)
        {
            float t = (i + 1f) / count;
            float x = (1 - t) * (1 - t) * pt0.X + 2 * t * (1 - t) * pt1.X + t * t * pt2.X;
            float y = (1 - t) * (1 - t) * pt0.Y + 2 * t * (1 - t) * pt1.Y + t * t * pt2.Y;
            points[i] = new SKPoint(x, y);
        }

        return points;
    }

    static SKPoint[] FlattenConic(SKPoint pt0, SKPoint pt1, SKPoint pt2, float weight)
    {
        int count = (int)Math.Max(1, Length(pt0, pt1) + Length(pt1, pt2));
        SKPoint[] points = new SKPoint[count];

        for (int i = 0; i < count; i++)
        {
            float t = (i + 1f) / count;
            float denominator = (1 - t) * (1 - t) + 2 * weight * t * (1 - t) + t * t;
            float x = (1 - t) * (1 - t) * pt0.X + 2 * weight * t * (1 - t) * pt1.X + t * t * pt2.X;
            float y = (1 - t) * (1 - t) * pt0.Y + 2 * weight * t * (1 - t) * pt1.Y + t * t * pt2.Y;
            x /= denominator;
            y /= denominator;
        }

        return points;
    }

    static double Length(SKPoint pt0, SKPoint pt1)
    {
        return Math.Sqrt(Math.Pow(pt1.X - pt0.X, 2) + Math.Pow(pt1.Y - pt0.Y, 2));
    }
}
```

从扩展方法中引用所有这些方法`CloneWithTransform`还包含在此类中，如下所示。 此方法通过枚举路径命令和构造基于的数据的新路径克隆一个路径。 但是，新路径仅包含`MoveTo`和`LineTo`调用。 所有曲线和直线被都减少到一系列小的行。

调用时`CloneWithTransform`，传递给方法`Func<SKPoint, SKPoint>`，它是使用函数`SKPaint`参数，它返回`SKPoint`值。 每个点来应用自定义算法转换为调用此函数：

```csharp
static class PathExtensions
{
    public static SKPath CloneWithTransform(this SKPath pathIn, Func<SKPoint, SKPoint> transform)
    {
        SKPath pathOut = new SKPath();

        using (SKPath.RawIterator iterator = pathIn.CreateRawIterator())
        {
            SKPoint[] points = new SKPoint[4];
            SKPathVerb pathVerb = SKPathVerb.Move;
            SKPoint firstPoint = new SKPoint();
            SKPoint lastPoint = new SKPoint();

            while ((pathVerb = iterator.Next(points)) != SKPathVerb.Done)
            {
                switch (pathVerb)
                {
                    case SKPathVerb.Move:
                        pathOut.MoveTo(transform(points[0]));
                        firstPoint = lastPoint = points[0];
                        break;

                    case SKPathVerb.Line:
                        SKPoint[] linePoints = Interpolate(points[0], points[1]);

                        foreach (SKPoint pt in linePoints)
                        {
                            pathOut.LineTo(transform(pt));
                        }

                        lastPoint = points[1];
                        break;

                    case SKPathVerb.Cubic:
                        SKPoint[] cubicPoints = FlattenCubic(points[0], points[1], points[2], points[3]);

                        foreach (SKPoint pt in cubicPoints)
                        {
                            pathOut.LineTo(transform(pt));
                        }

                        lastPoint = points[3];
                        break;

                    case SKPathVerb.Quad:
                        SKPoint[] quadPoints = FlattenQuadratic(points[0], points[1], points[2]);

                        foreach (SKPoint pt in quadPoints)
                        {
                            pathOut.LineTo(transform(pt));
                        }

                        lastPoint = points[2];
                        break;

                    case SKPathVerb.Conic:
                        SKPoint[] conicPoints = FlattenConic(points[0], points[1], points[2], iterator.ConicWeight());

                        foreach (SKPoint pt in conicPoints)
                        {
                            pathOut.LineTo(transform(pt));
                        }

                        lastPoint = points[2];
                        break;

                    case SKPathVerb.Close:
                        SKPoint[] closePoints = Interpolate(lastPoint, firstPoint);

                        foreach (SKPoint pt in closePoints)
                        {
                            pathOut.LineTo(transform(pt));
                        }

                        firstPoint = lastPoint = new SKPoint(0, 0);
                        pathOut.Close();
                        break;
                }
            }
        }
        return pathOut;
    }
    ...
}
```

克隆的路径减少到很小的直线，因为转换函数将已转换为曲线的直线，直线的功能。

请注意，该方法将调用在变量中的每个分布的第一个点保留`firstPoint`并在每个当前位置在变量中绘制命令`lastPoint`。 这些变量是必需构造最终关闭的行时`Close`遇到谓词。

**GlobularText**示例使用此扩展方法似乎是围绕半球文本换行中的三维效果：

[![](information-images/globulartext-small.png "三重 Globular 文本页屏幕截图")](information-images/globulartext-large.png#lightbox "Globular 文本页的三个屏幕截图")

[ `GlobularTextPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/GlobularTextPage.cs)类构造函数执行此转换。 它会创建`SKPaint`对象的文本，然后获取`SKPath`对象从`GetTextPath`方法。 这是传递到的路径`CloneWithTransform`扩展方法以及转换函数：

```csharp
public class GlobularTextPage : ContentPage
{
    SKPath globePath;

    public GlobularTextPage()
    {
        Title = "Globular Text";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;

        using (SKPaint textPaint = new SKPaint())
        {
            textPaint.Typeface = SKTypeface.FromFamilyName("Times New Roman");
            textPaint.TextSize = 100;

            using (SKPath textPath = textPaint.GetTextPath("HELLO", 0, 0))
            {
                SKRect textPathBounds;
                textPath.GetBounds(out textPathBounds);

                globePath = textPath.CloneWithTransform((SKPoint pt) =>
                {
                    double longitude = (Math.PI / textPathBounds.Width) *
                                            (pt.X - textPathBounds.Left) - Math.PI / 2;
                    double latitude = (Math.PI / textPathBounds.Height) *
                                            (pt.Y - textPathBounds.Top) - Math.PI / 2;

                    longitude *= 0.75;
                    latitude *= 0.75;

                    float x = (float)(Math.Cos(latitude) * Math.Sin(longitude));
                    float y = (float)Math.Sin(latitude);

                    return new SKPoint(x, y);
                });
            }
        }
    }
    ...
}
```

转换函数首先计算两个名为的值`longitude`和`latitude`，范围从-π/2 的顶部和文本，左侧到 π/2 的右侧和底部的文本。 这些值的范围不直观地令人满意，因此它们减少乘以 0.75。 （请尝试不使用这些调整代码。 将文本转换成过于隐蔽在北极和南极，并在两侧不可过于省略。）这些三维的球面坐标将转换为二维`x`和`y`由标准公式的坐标。

作为字段存储的新路径。 `PaintSurface`处理程序然后只是需要中心和缩放要在屏幕上显示的路径：

```csharp
public class GlobularTextPage : ContentPage
{
    SKPath globePath;
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPaint pathPaint = new SKPaint())
        {
            pathPaint.Style = SKPaintStyle.Fill;
            pathPaint.Color = SKColors.Blue;
            pathPaint.StrokeWidth = 3;
            pathPaint.IsAntialias = true;

            canvas.Translate(info.Width / 2, info.Height / 2);
            canvas.Scale(0.45f * Math.Min(info.Width, info.Height));     // radius
            canvas.DrawPath(globePath, pathPaint);
        }
    }
}
```

这是非常通用技术。 如果路径效果的数组中所述[**路径效果**](effects.md)文章非常未涵盖一些你认为应将包括在内，这是一种方法来填充空白。

## <a name="related-links"></a>相关链接

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （示例）](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
