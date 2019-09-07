---
title: 像素和与设备无关的单位
description: 本文探讨了 SkiaSharp 坐标和 Xamarin.Forms 坐标之间的差异，并演示此示例代码。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 26C25BB8-FBE8-4B77-B01D-16A163A16890
author: davidbritch
ms.author: dabritch
ms.date: 02/09/2017
ms.openlocfilehash: d6011175a735eb81f83a023f7d32fccd6feadd47
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70759476"
---
# <a name="pixels-and-device-independent-units"></a>像素和与设备无关的单位

[![下载示例](~/media/shared/download.png)下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_了解 SkiaSharp 坐标和 Xamarin.Forms 坐标之间的差异_

本文探讨了 SkiaSharp 和 Xamarin.Forms 中使用的坐标系统中的差异。 你可以获取信息以两个坐标系统之间进行转换，并还绘制填充特定区域的图形：

![](pixels-images/screenfillexample.png "椭圆的填充屏幕")

如果您已的编程在 Xamarin.Forms 中一段时间，可能必须了解 Xamarin.Forms 坐标和大小。 绘制两个先前的文章中的圆圈可能看起来有点小给你。

这些圆圈*是*Xamarin.Forms 大小很小。 默认情况下，SkiaSharp 绘制的像素为单位而 Xamarin.Forms 根据基础平台建立一个独立于设备的单元的坐标和大小。 (Xamarin.Forms 坐标系统的详细信息可在[第 5 章。处理大小](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter05.md)一书*使用 Xamarin.Forms 创建移动应用*。)

中的页[ **SkewSharpFormsDemos** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)程序资格**图面大小**使用 SkiaSharp 文本输出以显示来自三个不同源的显示图面大小：

- 正常的 Xamarin.Forms [ `Width` ](xref:Xamarin.Forms.VisualElement.Width)并[ `Height` ](xref:Xamarin.Forms.VisualElement.Height)的属性`SKCanvasView`对象。
- [ `CanvasSize` ](xref:SkiaSharp.Views.Forms.SKCanvasView.CanvasSize)属性的`SKCanvasView`对象。
- [ `Size` ](xref:SkiaSharp.SKImageInfo.Size)的属性`SKImageInfo`值，该值与一致`Width`和`Height`在前面的两个页中使用的属性。

[ `SurfaceSizePage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/SurfaceSizePage.cs)类演示了如何显示这些值。 构造函数保存`SKCanvasView`对象作为字段，以便它可以访问在`PaintSurface`事件处理程序：

```csharp
SKCanvasView canvasView;

public SurfaceSizePage()
{
    Title = "Surface Size";

    canvasView = new SKCanvasView();
    canvasView.PaintSurface += OnCanvasViewPaintSurface;
    Content = canvasView;
}
```

`SKCanvas` 包括六个不同`DrawText`方法，但这[ `DrawText` ](xref:SkiaSharp.SKCanvas.DrawText(System.String,System.Single,System.Single,SkiaSharp.SKPaint))是最简单的方法：

```csharp
public void DrawText (String text, Single x, Single y, SKPaint paint)
```

指定文本字符串，X 和 Y 坐标开始，文本的位置和一个`SKPaint`对象。 X 坐标指定文本左侧的位置，但观察：Y 坐标指定文本*基线*的位置。 如果您曾经编写过手动加下划线的纸张上，在基线是在哪个字符 sit，及更低的下行字母 （例如与字母 g、 p、 q 和 y） 降的行。

`SKPaint`对象允许你指定的文本、 字体系列和文本大小的颜色。 默认情况下[ `TextSize` ](xref:SkiaSharp.SKPaint.TextSize)属性具有值为 12，这会导致在手机等高分辨率设备上的小文本。 在除最简单的应用程序，您还需要在大小上都显示的文本的一些信息。 `SKPaint`类定义[ `FontMetrics` ](xref:SkiaSharp.SKPaint.FontMetrics)属性和一些[ `MeasureText` ](xref:SkiaSharp.SKPaint.MeasureText(System.String))方法，但对于较少花哨的需求， [ `FontSpacing` ](xref:SkiaSharp.SKPaint.FontSpacing)属性提供间距连续文本行的建议的值。

以下`PaintSurface`处理程序将创建`SKPaint`对象`TextSize`的 40 像素，这是所需的下行字母的底部到顶部升部的文本的垂直高度。 `FontSpacing`值的`SKPaint`对象返回比的大约 47 像素有点大。

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    SKPaint paint = new SKPaint
    {
        Color = SKColors.Black,
        TextSize = 40
    };

    float fontSpacing = paint.FontSpacing;
    float x = 20;               // left margin
    float y = fontSpacing;      // first baseline
    float indent = 100;

    canvas.DrawText("SKCanvasView Height and Width:", x, y, paint);
    y += fontSpacing;
    canvas.DrawText(String.Format("{0:F2} x {1:F2}",
                                  canvasView.Width, canvasView.Height),
                    x + indent, y, paint);
    y += fontSpacing * 2;
    canvas.DrawText("SKCanvasView CanvasSize:", x, y, paint);
    y += fontSpacing;
    canvas.DrawText(canvasView.CanvasSize.ToString(), x + indent, y, paint);
    y += fontSpacing * 2;
    canvas.DrawText("SKImageInfo Size:", x, y, paint);
    y += fontSpacing;
    canvas.DrawText(info.Size.ToString(), x + indent, y, paint);
}
```

该方法的开头 （适用于在左侧的小边距） 20 X 坐标和 Y 坐标的文本的第一行`fontSpacing`，这是更多，而不是所需的显示图面顶部显示完整的第一个文本行的高度。 每次调用后`DrawText`，由一个或两个为增量增加的 Y 坐标`fontSpacing`。

下面是运行的程序：

[![](pixels-images/surfacesize-small.png "三重的面大小页面的屏幕截图")](pixels-images/surfacesize-large.png#lightbox "面大小页面的三个屏幕截图")

正如您所看到的`CanvasSize`的属性`SKCanvasView`并`Size`属性的`SKImageInfo`值是否一致中报告的像素尺寸。 `Height`并`Width`的属性`SKCanvasView`是 Xamarin.Forms 属性，并报告以与设备无关单位平台定义的视图的大小。

在左侧七个 iOS 模拟器的两个像素由每个与设备无关单位和 Android 的 Nexus 5，中心中的三个像素由每个单位。 这就是为什么前面所示的简单圆圈在不同平台上具有不同的大小。

如果您希望完全在设备无关的单位中工作，就可以做到设置`IgnorePixelScaling`的属性`SKCanvasView`到`true`。 但是，您可能不喜欢结果。 SkiaSharp 呈现图形的较小的设备图面，与设备无关的单位中视图的大小相等的像素大小。 （例如，SkiaSharp 会使用 360 x 512 像素的显示器表面上 Nexus 5。）它然后增加的大小，从而导致明显的位图 jaggies 该映像。

若要维护相同的图像分辨率，更好的解决方案是编写您自己的简单函数将两个坐标系统之间转换。

除了`DrawCircle`方法，`SKCanvas`还定义了两个`DrawOval`绘制椭圆的方法。 由两个半径而不是单个 radius 定义一个椭圆。 这些参数称为*主要 radius*并*次要 radius*。 `DrawOval`方法绘制一个具有两个半径的椭圆平行的 X 和 Y 轴。 (如果您需要具有不与 X 和 Y 轴平行的轴绘制一个椭圆，如本文所述，可以使用旋转转换[**旋转转换**](../transforms/rotate.md)或中所述的图形路径文章[**绘制弧线的三个方法**](../curves/arcs.md))。 此重载[ `DrawOval` ](xref:SkiaSharp.SKCanvas.DrawOval(System.Single,System.Single,System.Single,System.Single,SkiaSharp.SKPaint))方法的名称的两个半径参数`rx`和`ry`以指示它们是并行的 X 和 Y 轴：

```csharp
public void DrawOval (Single cx, Single cy, Single rx, Single ry, SKPaint paint)
```

是否可以绘制椭圆的填充的显示图面？ **椭圆填充**页说明如何。 `PaintSurface`中的事件处理程序[ **EllipseFillPage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/EllipseFillPage.xaml.cs)类中减去从一半笔划宽度`xRadius`和`yRadius`值以适合整个椭圆并将其概述了中的显示图面：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    float strokeWidth = 50;
    float xRadius = (info.Width - strokeWidth) / 2;
    float yRadius = (info.Height - strokeWidth) / 2;

    SKPaint paint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Blue,
        StrokeWidth = strokeWidth
    };
    canvas.DrawOval(info.Width / 2, info.Height / 2, xRadius, yRadius, paint);
}
```

运行此处：

[![](pixels-images/ellipsefill-small.png "三重的面大小页面的屏幕截图")](pixels-images/ellipsefill-large.png#lightbox "面大小页面的三个屏幕截图")

另[ `DrawOval` ](xref:SkiaSharp.SKCanvas.DrawOval(SkiaSharp.SKRect,SkiaSharp.SKPaint))方法有[ `SKRect` ](xref:SkiaSharp.SKRect)参数，它是根据其左上角和右下角的 X 和 Y 坐标定义一个矩形。 该椭圆填充该矩形，这表明它可以将其用于**椭圆填充**如下页：

```csharp
SKRect rect = new SKRect(0, 0, info.Width, info.Height);
canvas.DrawOval(rect, paint);
```

但是，，将截断所有四个边椭圆的边框边缘。 您需要调整所有`SKRect`构造函数参数基于`strokeWidth`以便正确正常工作：

```csharp
SKRect rect = new SKRect(strokeWidth / 2,
                         strokeWidth / 2,
                         info.Width - strokeWidth / 2,
                         info.Height - strokeWidth / 2);
canvas.DrawOval(rect, paint);
```

## <a name="related-links"></a>相关链接

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
