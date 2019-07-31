---
title: 按路径和区域进行剪裁
description: 本文介绍如何使用 SkiaSharp 剪辑图形路径与特定区域，以及如何创建区域，并演示此示例代码。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 8022FBF9-2208-43DB-94D8-0A4E9A5DA07F
author: davidbritch
ms.author: dabritch
ms.date: 06/16/2017
ms.openlocfilehash: 8978bd386ec2f2ea0f9960f079ce82750941cfad
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2019
ms.locfileid: "68655957"
---
# <a name="clipping-with-paths-and-regions"></a>按路径和区域进行剪裁

[![下载示例](~/media/shared/download.png)下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_使用剪辑图形路径指向的特定区域，并创建区域_

此外，有时需要限制对特定区域的图形渲染。 这称为*剪辑*。 您可以使用特殊效果，如通过锁眼看到 monkey 此图像剪辑：

![](clipping-images/clippingsample.png "通过锁眼 monkey")

*剪辑区域*是在其中呈现图形的屏幕区域。 不呈现的剪辑区域之外显示任何内容。 通常由一个矩形来定义剪辑区域或[ `SKPath` ](xref:SkiaSharp.SKPath)对象，但您可以或者定义剪辑区域使用[ `SKRegion` ](xref:SkiaSharp.SKRegion)对象。 这两种对象类型首先似乎相关，因为您可以从路径中创建一个区域。 但是, 你不能从区域创建路径, 它们在内部有很大不同:路径由一系列线条和曲线组成, 而区域由一系列水平扫描线定义。

上面的图像已通过**Monkey 通过锁眼**页。 [ `MonkeyThroughKeyholePage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/MonkeyThroughKeyholePage.cs)类定义使用 SVG 数据的路径，并使用构造函数从程序资源加载位图：

```csharp
public class MonkeyThroughKeyholePage : ContentPage
{
    SKBitmap bitmap;
    SKPath keyholePath = SKPath.ParseSvgPathData(
        "M 300 130 L 250 350 L 450 350 L 400 130 A 70 70 0 1 0 300 130 Z");

    public MonkeyThroughKeyholePage()
    {
        Title = "Monkey through Keyhole";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;

        string resourceID = "SkiaSharpFormsDemos.Media.SeatedMonkey.jpg";
        Assembly assembly = GetType().GetTypeInfo().Assembly;

        using (Stream stream = assembly.GetManifestResourceStream(resourceID))
        {
            bitmap = SKBitmap.Decode(stream);
        }
    }
    ...
}
```

尽管`keyholePath`对象描述的锁眼轮廓，坐标是完全任意的并且反映什么设备设计路径数据时非常方便。 出于此原因，`PaintSurface`处理程序获取此路径和调用的边界`Translate`和`Scale`将路径移动到屏幕的中央，使其几乎作为屏幕高度增加：


```csharp
public class MonkeyThroughKeyholePage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Set transform to center and enlarge clip path to window height
        SKRect bounds;
        keyholePath.GetTightBounds(out bounds);

        canvas.Translate(info.Width / 2, info.Height / 2);
        canvas.Scale(0.98f * info.Height / bounds.Height);
        canvas.Translate(-bounds.MidX, -bounds.MidY);

        // Set the clip path
        canvas.ClipPath(keyholePath);

        // Reset transforms
        canvas.ResetMatrix();

        // Display monkey to fill height of window but maintain aspect ratio
        canvas.DrawBitmap(bitmap,
            new SKRect((info.Width - info.Height) / 2, 0,
                       (info.Width + info.Height) / 2, info.Height));
    }
}
```

但不是呈现路径。 相反，以下转换，该路径用于设置此语句的剪辑区域：

```csharp
canvas.ClipPath(keyholePath);
```

`PaintSurface`处理程序然后将重置通过调用转换`ResetMatrix`和绘制位图将扩展到整个屏幕的高度。 此代码假定将位图是方形，这是此特定的位图。 仅在由剪切路径定义的区域中呈现位图：

[![](clipping-images/monkeythroughkeyhole-small.png "通过锁眼页面 Monkey 的三个屏幕截图")](clipping-images/monkeythroughkeyhole-large.png#lightbox "的 Monkey 锁眼页通过三个屏幕截图")

剪切路径是否受转换生效时`ClipPath`调用方法，并不转换生效时图形对象 （如位图） 显示。 剪切路径是使用保存的画布状态的一部分`Save`方法并使用已还原`Restore`方法。

## <a name="combining-clipping-paths"></a>组合剪切路径

严格地说，剪辑区域未"设置"`ClipPath`方法。 相反，它结合了现有剪切路径，开始为画布大小等于一个矩形。 你可以获取的剪辑区域使用的矩形边界[ `ClipBounds` ](xref:SkiaSharp.SKCanvas.ClipBounds)属性或[ `ClipDeviceBounds` ](xref:SkiaSharp.SKCanvas.ClipDeviceBounds)属性。 `ClipBounds`属性返回`SKRect`反映任何转换的值可能有效。 `ClipDeviceBounds`属性返回`RectI`值。 这是一个具有整数维度的矩形，描述在实际的像素尺寸的剪辑区域。

任何对`ClipPath`减少通过合并为新区域的剪辑区域的剪辑区域。 完整语法[ `ClipPath` ](xref:SkiaSharp.SKCanvas.ClipPath(SkiaSharp.SKPath,SkiaSharp.SKClipOperation,System.Boolean))方法是：

```csharp
public void ClipPath(SKPath path, SKClipOperation operation = SKClipOperation.Intersect, Boolean antialias = false);
```

此外，还有[ `ClipRect` ](xref:SkiaSharp.SKCanvas.ClipRect(SkiaSharp.SKRect,SkiaSharp.SKClipOperation,System.Boolean))结合一个矩形剪辑区域的方法：

```csharp
public Void ClipRect(SKRect rect, SKClipOperation operation = SKClipOperation.Intersect, Boolean antialias = false);
```

默认情况下，生成的剪辑区域是现有的剪辑区域的交集并`SKPath`或`SKRect`中指定`ClipPath`或`ClipRect`方法。 了这一点**四个圆圈相交剪辑**页。 `PaintSurface`处理程序中的[ `FourCircleInteresectClipPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/FourCircleIntersectClipPage.cs)类重用相同`SKPath`对象来创建四个重叠圆圈，其中每个减少了通过连续调用的剪辑区域`ClipPath`:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    float size = Math.Min(info.Width, info.Height);
    float radius = 0.4f * size;
    float offset = size / 2 - radius;

    // Translate to center
    canvas.Translate(info.Width / 2, info.Height / 2);

    using (SKPath path = new SKPath())
    {
        path.AddCircle(-offset, -offset, radius);
        canvas.ClipPath(path, SKClipOperation.Intersect);

        path.Reset();
        path.AddCircle(-offset, offset, radius);
        canvas.ClipPath(path, SKClipOperation.Intersect);

        path.Reset();
        path.AddCircle(offset, -offset, radius);
        canvas.ClipPath(path, SKClipOperation.Intersect);

        path.Reset();
        path.AddCircle(offset, offset, radius);
        canvas.ClipPath(path, SKClipOperation.Intersect);

        using (SKPaint paint = new SKPaint())
        {
            paint.Style = SKPaintStyle.Fill;
            paint.Color = SKColors.Blue;
            canvas.DrawPaint(paint);
        }
    }
}
```

剩下是以下四个圆圈的交集：

[![](clipping-images//fourcircleintersectclip-small.png "三重的四个圆圈相交剪辑页屏幕截图")](clipping-images/fourcircleintersectclip-large.png#lightbox "带来三倍的四个圆圈相交剪辑页屏幕截图")

[ `SKClipOperation` ](xref:SkiaSharp.SKClipOperation)枚举具有只有两个成员：

- `Difference` 从现有的剪辑区域中删除指定的路径或矩形

- `Intersect` 与指定的路径或使用现有的剪辑区域的矩形相交

如果替换四个`SKClipOperation.Intersect`中的自变量`FourCircleIntersectClipPage`类的`SKClipOperation.Difference`，可以看到如下：

[![](clipping-images//fourcircledifferenceclip-small.png "三个与差值运算的四个圆圈相交剪辑页屏幕截图")](clipping-images/fourcircledifferenceclip-large.png#lightbox "差值运算的四个圆圈相交剪辑页的三个屏幕截图")

已从剪辑区域中删除四个重叠圆圈。

**剪辑操作**页说明了这些只是一对圆圈的两个操作之间的差异。 在左侧的第一个圆圈将添加到与默认剪辑操作的剪辑区域`Intersect`，而在右侧的第二个圆圈将添加到剪辑区域与剪辑操作由文本标签指示：

[![](clipping-images//clipoperations-small.png "三重剪辑操作页屏幕截图")](clipping-images/clipoperations-large.png#lightbox "剪辑操作页面的三个屏幕截图")

[ `ClipOperationsPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ClipOperationsPage.cs)类定义了两个`SKPaint`对象作为字段，，然后将屏幕划分为两个矩形区域。 这些方面有所不同，具体取决电话是否是在纵向或横向模式下。 `DisplayClipOp`类，然后显示的文本和调用`ClipPath`使用两个圆的路径来说明每个剪辑操作：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    float x = 0;
    float y = 0;

    foreach (SKClipOperation clipOp in Enum.GetValues(typeof(SKClipOperation)))
    {
        // Portrait mode
        if (info.Height > info.Width)
        {
            DisplayClipOp(canvas, new SKRect(x, y, x + info.Width, y + info.Height / 2), clipOp);
            y += info.Height / 2;
        }
        // Landscape mode
        else
        {
            DisplayClipOp(canvas, new SKRect(x, y, x + info.Width / 2, y + info.Height), clipOp);
            x += info.Width / 2;
        }
    }
}

void DisplayClipOp(SKCanvas canvas, SKRect rect, SKClipOperation clipOp)
{
    float textSize = textPaint.TextSize;
    canvas.DrawText(clipOp.ToString(), rect.MidX, rect.Top + textSize, textPaint);
    rect.Top += textSize;

    float radius = 0.9f * Math.Min(rect.Width / 3, rect.Height / 2);
    float xCenter = rect.MidX;
    float yCenter = rect.MidY;

    canvas.Save();

    using (SKPath path1 = new SKPath())
    {
        path1.AddCircle(xCenter - radius / 2, yCenter, radius);
        canvas.ClipPath(path1);

        using (SKPath path2 = new SKPath())
        {
            path2.AddCircle(xCenter + radius / 2, yCenter, radius);
            canvas.ClipPath(path2, clipOp);

            canvas.DrawPaint(fillPaint);
        }
    }

    canvas.Restore();
}
```

调用`DrawPaint`通常会导致整个画布，若要使用的填充`SKPaint`对象，但在这种情况下，该方法只是绘制剪辑区域内。

## <a name="exploring-regions"></a>探索区域

您还可以定义的剪辑区域[ `SKRegion` ](xref:SkiaSharp.SKRegion)对象。

一个新创建的`SKRegion`对象描述的空白区域。 在对象上的第一个调用是通常[ `SetRect` ](xref:SkiaSharp.SKRegion.SetRect(SkiaSharp.SKRectI)) ，以便在区域描述的矩形区域。 参数`SetRect`是`SKRectI`值&mdash;整数矩形坐标，因为它指定根据像素的矩形。 然后，可以调用[ `SetPath` ](xref:SkiaSharp.SKRegion.SetPath(SkiaSharp.SKPath,SkiaSharp.SKRegion))与`SKPath`对象。 这将创建相同的内部路径，但剪辑到初始的矩形区域的区域。

此外可以通过调用之一修改在区域[ `Op` ](xref:SkiaSharp.SKRegion.Op*)方法重载，例如这个：

```csharp
public Boolean Op(SKRegion region, SKRegionOperation op)
```

[ `SKRegionOperation` ](xref:SkiaSharp.SKRegionOperation)枚举是类似于`SKClipOperation`但它具有更多成员：

- `Difference`

- `Intersect`

- `Union`

- `XOR`

- `ReverseDifference`

- `Replace`

您要做的区域`Op`上的调用结合了作为参数基于指定的区域`SKRegionOperation`成员。 时最后一个区域适用于剪辑，您可以设置的 canvas 使用的剪辑区域作为[ `ClipRegion` ](xref:SkiaSharp.SKCanvas.ClipRegion(SkiaSharp.SKRegion,SkiaSharp.SKClipOperation))方法的`SKCanvas`:

```csharp
public void ClipRegion(SKRegion region, SKClipOperation operation = SKClipOperation.Intersect)
```

下面的屏幕截图显示了基于六个区域操作的剪辑区域。 左侧的圆圈可以是该区域的`Op`上，调用方法和正确的圆圈可以是传递给该区域`Op`方法：

[![](clipping-images//regionoperations-small.png "区域操作页面的三个屏幕截图")](clipping-images/regionoperations-large.png#lightbox "的区域操作页的三个屏幕截图")

组合这些两个圆的这些所有可能性都？ 考虑为其本身中所示的三个组件组合生成的图像`Difference`， `Intersect`，和`ReverseDifference`操作。 到第三个电源，两个或八个组合的总数。 缺少两个是原始区域 (这会产生不调用`Op`根本) 和完全空白区域。

它很难用于因为你需要先从该路径创建路径和区域，然后将组合多个区域中的剪辑区域。 整体结构**区域操作**页是非常类似于**剪辑操作**但[ `RegionOperationsPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/RegionOperationsPage.cs)类将屏幕分为六个区域和显示要用于此作业的区域所需的额外工作：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    float x = 0;
    float y = 0;
    float width = info.Height > info.Width ? info.Width / 2 : info.Width / 3;
    float height = info.Height > info.Width ? info.Height / 3 : info.Height / 2;

    foreach (SKRegionOperation regionOp in Enum.GetValues(typeof(SKRegionOperation)))
    {
        DisplayClipOp(canvas, new SKRect(x, y, x + width, y + height), regionOp);

        if ((x += width) >= info.Width)
        {
            x = 0;
            y += height;
        }
    }
}

void DisplayClipOp(SKCanvas canvas, SKRect rect, SKRegionOperation regionOp)
{
    float textSize = textPaint.TextSize;
    canvas.DrawText(regionOp.ToString(), rect.MidX, rect.Top + textSize, textPaint);
    rect.Top += textSize;

    float radius = 0.9f * Math.Min(rect.Width / 3, rect.Height / 2);
    float xCenter = rect.MidX;
    float yCenter = rect.MidY;

    SKRectI recti = new SKRectI((int)rect.Left, (int)rect.Top,
                                (int)rect.Right, (int)rect.Bottom);

    using (SKRegion wholeRectRegion = new SKRegion())
    {
        wholeRectRegion.SetRect(recti);

        using (SKRegion region1 = new SKRegion(wholeRectRegion))
        using (SKRegion region2 = new SKRegion(wholeRectRegion))
        {
            using (SKPath path1 = new SKPath())
            {
                path1.AddCircle(xCenter - radius / 2, yCenter, radius);
                region1.SetPath(path1);
            }

            using (SKPath path2 = new SKPath())
            {
                path2.AddCircle(xCenter + radius / 2, yCenter, radius);
                region2.SetPath(path2);
            }

            region1.Op(region2, regionOp);

            canvas.Save();
            canvas.ClipRegion(region1);
            canvas.DrawPaint(fillPaint);
            canvas.Restore();
        }
    }
}
```

下面是有很大区别`ClipPath`方法和`ClipRegion`方法：

> [!IMPORTANT]
> 与不同`ClipPath`方法，`ClipRegion`方法不受转换。

若要了解这种差异很有必要，将有助于您了解哪些区域。 如果您已考虑如何剪辑操作或区域操作可能会在内部实现，它可能看起来非常复杂。 组合多个可能非常复杂的路径，并生成的路径的轮廓可能是算法非常棘手的工作。

如果每个路径缩短为水平扫描行，如老式清空 tube 电视中的一系列显著简化此作业。 每个扫描行是只需一条水平线起始点和终结点。 例如，10 个像素的半径的圆形可分解为 20 水平扫描行，其中每个圆的左侧部分开始，在右侧的部分结束。 结合使用的任何区域操作的两个圆成为非常简单，因为它是只需检查每个对相应扫描行的开始和结束坐标。

这是一个区域:一系列用于定义区域的水平扫描行。

但是，当区域被简化为一系列的扫描行，这些扫描线基于特定的像素尺寸。 严格地说，区域不是向量图形对象。 它是在更接近本质上比为路径的压缩单色位图。 因此，区域不能缩放或旋转不失真，并因此它们不转换时使用的剪辑区域。

但是，可以将转换应用到用于绘制的区域。 **区域画图**程序生动演示的内部区域性质。 [ `RegionPaintPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/RegionPaintPage.cs)类创建`SKRegion`对象基于`SKPath`10 单元 radius 圆。 一个转换，转换将会扩展该圆圈，填充页面：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    int radius = 10;

    // Create circular path
    using (SKPath circlePath = new SKPath())
    {
        circlePath.AddCircle(0, 0, radius);

        // Create circular region
        using (SKRegion circleRegion = new SKRegion())
        {
            circleRegion.SetRect(new SKRectI(-radius, -radius, radius, radius));
            circleRegion.SetPath(circlePath);

            // Set transform to move it to center and scale up
            canvas.Translate(info.Width / 2, info.Height / 2);
            canvas.Scale(Math.Min(info.Width / 2, info.Height / 2) / radius);

            // Fill region
            using (SKPaint fillPaint = new SKPaint())
            {
                fillPaint.Style = SKPaintStyle.Fill;
                fillPaint.Color = SKColors.Orange;

                canvas.DrawRegion(circleRegion, fillPaint);
            }

            // Stroke path for comparison
            using (SKPaint strokePaint = new SKPaint())
            {
                strokePaint.Style = SKPaintStyle.Stroke;
                strokePaint.Color = SKColors.Blue;
                strokePaint.StrokeWidth = 0.1f;

                canvas.DrawPath(circlePath, strokePaint);
            }
        }
    }
}
```

`DrawRegion`调用填充的区域以橙色，虽然`DrawPath`调用中比较的蓝色描边的原始路径：

[![](clipping-images//regionpaint-small.png "三重的区域绘制页的屏幕截图")](clipping-images/regionpaint-large.png#lightbox "的区域绘制页的三个屏幕截图")

区域显然是一系列离散的坐标。

如果不需要使用与此剪辑区域相关的转换，可用于区域剪辑，作为**四个-能**页说明。 [ `FourLeafCloverPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/FourLeafCloverPage.cs)类构造从四个循环区域的复合区域，该复合区域设置作为剪辑区域，然后绘制 360 直线从页面的中心方面的一系列：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    float xCenter = info.Width / 2;
    float yCenter = info.Height / 2;
    float radius = 0.24f * Math.Min(info.Width, info.Height);

    using (SKRegion wholeScreenRegion = new SKRegion())
    {
        wholeScreenRegion.SetRect(new SKRectI(0, 0, info.Width, info.Height));

        using (SKRegion leftRegion = new SKRegion(wholeScreenRegion))
        using (SKRegion rightRegion = new SKRegion(wholeScreenRegion))
        using (SKRegion topRegion = new SKRegion(wholeScreenRegion))
        using (SKRegion bottomRegion = new SKRegion(wholeScreenRegion))
        {
            using (SKPath circlePath = new SKPath())
            {
                // Make basic circle path
                circlePath.AddCircle(xCenter, yCenter, radius);

                // Left leaf
                circlePath.Transform(SKMatrix.MakeTranslation(-radius, 0));
                leftRegion.SetPath(circlePath);

                // Right leaf
                circlePath.Transform(SKMatrix.MakeTranslation(2 * radius, 0));
                rightRegion.SetPath(circlePath);

                // Make union of right with left
                leftRegion.Op(rightRegion, SKRegionOperation.Union);

                // Top leaf
                circlePath.Transform(SKMatrix.MakeTranslation(-radius, -radius));
                topRegion.SetPath(circlePath);

                // Combine with bottom leaf
                circlePath.Transform(SKMatrix.MakeTranslation(0, 2 * radius));
                bottomRegion.SetPath(circlePath);

                // Make union of top with bottom
                bottomRegion.Op(topRegion, SKRegionOperation.Union);

                // Exclusive-OR left and right with top and bottom
                leftRegion.Op(bottomRegion, SKRegionOperation.XOR);

                // Set that as clip region
                canvas.ClipRegion(leftRegion);

                // Set transform for drawing lines from center
                canvas.Translate(xCenter, yCenter);

                // Draw 360 lines
                for (double angle = 0; angle < 360; angle++)
                {
                    float x = 2 * radius * (float)Math.Cos(Math.PI * angle / 180);
                    float y = 2 * radius * (float)Math.Sin(Math.PI * angle / 180);

                    using (SKPaint strokePaint = new SKPaint())
                    {
                        strokePaint.Color = SKColors.Green;
                        strokePaint.StrokeWidth = 2;

                        canvas.DrawLine(0, 0, x, y, strokePaint);
                    }
                }
            }
        }
    }
}
```

它真正看上去不像四个-能，但它是一个图像，否则可能很难不经剪辑呈现：

[![](clipping-images//fourleafclover-small.png "三重的四个-能页屏幕截图")](clipping-images/fourleafclover-large.png#lightbox "带来三倍的四个-能页屏幕截图")


## <a name="related-links"></a>相关链接

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
