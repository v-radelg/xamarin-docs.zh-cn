---
title: SkiaSharp 中的 SVG 路径数据
description: 本文介绍了如何定义 SkiaSharp 路径中的可缩放矢量图形格式，使用文本字符串，并且此示例代码进行了演示。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 1D53067B-3502-4D74-B89D-7EC496901AE2
author: davidbritch
ms.author: dabritch
ms.date: 05/24/2017
ms.openlocfilehash: a645ef4ed55c8b4c78bad94865777088f62f1175
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2019
ms.locfileid: "68647392"
---
# <a name="svg-path-data-in-skiasharp"></a>SkiaSharp 中的 SVG 路径数据

[![下载示例](~/media/shared/download.png)下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_定义使用可缩放矢量图形格式的文本字符串的路径_

[ `SKPath` ](xref:SkiaSharp.SKPath)类建立可缩放向量图形 (SVG) 规范的格式支持的从文本字符串的整个路径对象的定义。 本文稍后将看到，这样一个文本字符串中的整个路径的表示方式：

![](path-data-images/pathdatasample.png "SVG 路径数据定义的示例路径")

SVG 是编程语言的网页的基于 XML 的图形。 SVG 必须允许标记而不是一系列函数调用中定义的路径，因为标准 SVG 包括文本字符串形式指定的整个图形路径的极其简练的方法。

SkiaSharp，在这种格式称为"SVG 路径数据。" 在基于 Windows XAML 的编程环境，包括 Windows Presentation Foundation 和通用 Windows 平台，它名为还支持的格式[路径标记语法](/dotnet/framework/wpf/graphics-multimedia/path-markup-syntax)或[移动绘制命令语法和](/windows/uwp/xaml-platform/move-draw-commands-syntax/)。 它还可以充当矢量图形图像，尤其是在基于文本的文件，如 XML 交换格式。

[ `SKPath` ](xref:SkiaSharp.SKPath)类定义了两种方法与单词`SvgPathData`名称中：

```csharp
public static SKPath ParseSvgPathData(string svgPath)

public string ToSvgPathData()
```

静态[ `ParseSvgPathData` ](xref:SkiaSharp.SKPath.ParseSvgPathData(System.String))方法将字符串转换为`SKPath`对象，而[ `ToSvgPathData` ](xref:SkiaSharp.SKPath.ToSvgPathData)转换`SKPath`为字符串的对象。

下面是以点为中心 （0，0） 一个半径为 100-五点星形 SVG 字符串：

```
"M 0 -100 L 58.8 90.9, -95.1 -30.9, 95.1 -30.9, -58.8 80.9 Z"
```

字母是生成的命令`SKPath`对象：`M`指示`MoveTo`调用，`L`是`LineTo`，并且`Z`是`Close`关闭了分布。 每个编号对提供的点的 X 和 Y 坐标。 请注意，`L`命令后跟以逗号隔开的多个点。 在一系列的坐标和点、 逗号和空格以相同方式处理。 某些编程人员更愿意添加逗号的 X 和 Y 坐标之间，而不是点之间，但逗号或空格只需避免多义性。 这是完全合法的：

```
"M0-100L58.8 90.9-95.1-30.9 95.1-30.9-58.8 80.9Z"
```

在正式记录的 SVG 路径数据语法[SVG 规范的部分 8.3](http://www.w3.org/TR/SVG11/paths.html#PathData)。 下面是摘要：

## <a name="moveto"></a>**MoveTo**

```
M x y
```

这会通过设置当前位置开始一个新轮廓路径中。 路径数据应始终首先使用`M`命令。

## <a name="lineto"></a>**LineTo**

```
L x y ...
```

此命令向路径添加一条直线 （或行），并设置新的当前位置到最后一行的末尾。 可以按照`L`命令的多个对*x*并*y*坐标。

## <a name="horizontal-lineto"></a>**水平 LineTo**

```
H x ...
```

此命令向路径添加一条水平线，并设置新的当前位置到行尾。 可以按照`H`具有多个命令*x*坐标，但它没有太大意义。

## <a name="vertical-line"></a>**竖线**

```
V y ...
```

此命令向路径添加一条竖线，并设置新的当前位置到行尾。

## <a name="close"></a>**关闭**

```
Z
```

`C`命令通过从当前位置到轮廓线的开头添加一条直线，闭合轮廓。

## <a name="arcto"></a>**ArcTo**

要添加到分布的椭圆弧命令是到目前为止整个 SVG 路径数据规范中最复杂的命令。 它是在该数字可以表示以外的坐标值的唯一命令：

```
A rx ry rotation-angle large-arc-flag sweep-flag x y ...
```

*Rx*并*ry*参数是水平和垂直半径的椭圆。 *旋转角度*进行顺时针以度为单位。

设置*大型弧线标志*到大型圆弧 1 或 0 小圆弧。

设置*扫描标志*为 1。 对于沿顺时针方向和为 0 表示逆时针旋转。

弧的绘制到点 (*x*， *y*)，这将成为新的当前位置。

## <a name="cubicto"></a>**CubicTo**

```
C x1 y1 x2 y2 x3 y3 ...
```

此命令将从当前位置添加三次方贝塞尔曲线 (*x3*， *y3*)，这将成为新的当前位置。 点 (*x1*， *y1*) 和 (*x2*， *y2*) 的控制点。

可以通过单个指定多个贝塞尔曲线`C`命令。 点数必须为 3 的倍数。

还有一个"平滑"贝塞尔曲线命令：

```
S x2 y2 x3 y3 ...
```

此命令应遵循常规的贝塞尔曲线命令 （尽管并未严格要求的）。 平滑的贝塞尔曲线命令，以便它是围绕其相互点以前的贝塞尔曲线的第二个控制点的反射计算的第一个控制点。 这 3 个点就是共线，和两条贝塞尔曲线之间的连接是平滑。

## <a name="quadto"></a>**QuadTo**

```
Q x1 y1 x2 y2 ...
```

二次贝塞尔曲线的点的数目必须是 2 的倍数。 控制点为 (*x1*， *y1*) 的终结点 （和新的当前位置） 和 (*x2*， *y2*)

还有平滑二次曲线命令：

```
T x2 y2 ...
```

根据上一条二次曲线的控制点计算的控制点。

所有这些命令还在"相对"版本中可用的均坐标点相对于当前的位置。 这些相对命令小写字母开头，例如`c`而非`C`三次方贝塞尔曲线命令的相对版本。

这是 SVG 路径数据定义的范围。 不没有重复的组的命令或执行计算的任何类型的任何工具。 适用于命令`ConicTo`或其他类型的圆弧规范不可用。

静态[ `SKPath.ParseSvgPathData` ](xref:SkiaSharp.SKPath.ParseSvgPathData(System.String))方法需要一个有效的 SVG 命令字符串。 如果检测到任何语法错误，该方法返回`null`。 这是唯一的错误指示。

[ `ToSvgPathData` ](xref:SkiaSharp.SKPath.ToSvgPathData)方法非常方便获取 SVG 路径数据从现有`SKPath`对象将传输到另一个程序，或将存储在基于文本的文件格式，如 XML。 (`ToSvgPathData`方法未在本文中的示例代码中演示。)不要*不*预期`ToSvgPathData`返回完全符合创建路径的方法调用相对应的字符串。 具体而言，您会发现，弧线转换为多个`QuadTo`命令，这是从返回的路径数据中的显示方式`ToSvgPathData`。

**路径数据 Hello**页拼写出单词"HELLO"使用 SVG 路径数据。 同时`SKPath`并`SKPaint`对象定义为字段中[ `PathDataHelloPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/PathDataHelloPage.cs)类：

```csharp
public class PathDataHelloPage : ContentPage
{
    SKPath helloPath = SKPath.ParseSvgPathData(
        "M 0 0 L 0 100 M 0 50 L 50 50 M 50 0 L 50 100" +                // H
        "M 125 0 C 60 -10, 60 60, 125 50, 60 40, 60 110, 125 100" +     // E
        "M 150 0 L 150 100, 200 100" +                                  // L
        "M 225 0 L 225 100, 275 100" +                                  // L
        "M 300 50 A 25 50 0 1 0 300 49.9 Z");                           // O

    SKPaint paint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Blue,
        StrokeWidth = 10,
        StrokeCap = SKStrokeCap.Round,
        StrokeJoin = SKStrokeJoin.Round
    };
    ...
}
```

在点 （0，0） 的左上角开始定义文本字符串的路径。 每个字母是 50 个单位的宽度和高度，100 个单位，且字母以通过另一个 25 个单位，这意味着整个路径是宽 350 个单位。

'Hello"'H' 由三个单行轮廓，而 E 是两个连接三次方贝塞尔曲线。 请注意，`C`命令后跟六个点，并且两个控点具有 – 10 和为 110 时，将其置于其他字母的 Y 坐标范围之外的 Y 坐标。 L 是两个相互连接的直线，而 O 是使用呈现一个椭圆`A`命令。

请注意，`M`开始的最后一个轮廓的命令将位置设置为点 （350，50），这是垂直居中的左侧端的 O。 后面的第一个数字所示`A`命令、 ellipse 有 25 个水平半径和 50 的垂直半径。 终结点将由中的数字的最后一个对`A`命令，它表示点 （300，49.9）。 这是有意只是略有不同的起始点。 如果终结点设置为等于起始点，将不呈现圆弧。 若要绘制整个椭圆，您必须设置终结点接近 （但不是等于） 的起始点，或者必须使用两个或多个`A`命令时，每个完整的椭圆的一部分。

你可能想要将以下语句添加到页面的构造函数，然后设置断点，以检查生成的字符串：

```csharp
string str = helloPath.ToSvgPathData();
```

您会发现，弧线已替换为一长串`Q`使用二次贝塞尔曲线弧的段落近似段之间的命令。

`PaintSurface`处理程序获取紧密边界的路径，不包括的控点的 E 和 O 曲线。 三种转换将路径的中心移动到点 （0，0）、 缩放到的画布 （但还将笔划宽度考虑在内），大小的路径，然后将该路径的中心移动到画布的中心：

```csharp
public class PathDataHelloPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        SKRect bounds;
        helloPath.GetTightBounds(out bounds);

        canvas.Translate(info.Width / 2, info.Height / 2);

        canvas.Scale(info.Width / (bounds.Width + paint.StrokeWidth),
                     info.Height / (bounds.Height + paint.StrokeWidth));

        canvas.Translate(-bounds.MidX, -bounds.MidY);

        canvas.DrawPath(helloPath, paint);
    }
}
```

路径填充画布，看起来更合理，在横向模式中查看时：

[![](path-data-images/pathdatahello-small.png "路径数据 Hello 页面的三个屏幕截图")](path-data-images/pathdatahello-large.png#lightbox "路径数据 Hello 页面的三个屏幕截图")

**路径数据 Cat**页是类似。 路径和画图对象被定义为中的字段[ `PathDataCatPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/PathDataCatPage.cs)类：

```csharp
public class PathDataCatPage : ContentPage
{
    SKPath catPath = SKPath.ParseSvgPathData(
        "M 160 140 L 150 50 220 103" +              // Left ear
        "M 320 140 L 330 50 260 103" +              // Right ear
        "M 215 230 L 40 200" +                      // Left whiskers
        "M 215 240 L 40 240" +
        "M 215 250 L 40 280" +
        "M 265 230 L 440 200" +                     // Right whiskers
        "M 265 240 L 440 240" +
        "M 265 250 L 440 280" +
        "M 240 100" +                               // Head
        "A 100 100 0 0 1 240 300" +
        "A 100 100 0 0 1 240 100 Z" +
        "M 180 170" +                               // Left eye
        "A 40 40 0 0 1 220 170" +
        "A 40 40 0 0 1 180 170 Z" +
        "M 300 170" +                               // Right eye
        "A 40 40 0 0 1 260 170" +
        "A 40 40 0 0 1 300 170 Z");

    SKPaint paint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Orange,
        StrokeWidth = 5
    };
    ...
}
```

一只猫头为圆圈，并使用两个呈现此处`A`命令，其中每个绘制半圆。 同时`A`头定义为 100 的水平和垂直半径的命令。 第一个弧开始于 （240，100），并结束于 （240，300） 将成为第二个返回结尾的弧线的起始点 （240，100）。

具有两个还呈现了两只眼睛`A`命令，并与 cat 的头第二个`A`作为第一个的开始位置到同一点结束命令`A`命令。 但是，这些对`A`命令不会定义一个椭圆。 使用的每个弧是 40 单位和半径也是 40 单位，这意味着这些弧线不是完整 semicircles。

`PaintSurface`处理程序与之前的示例中，执行类似的转换，但设置单个`Scale`身份保持长宽比，并提供小边距，以便只猫的须线不会接触屏幕的方面：

```csharp
public class PathDataCatPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear(SKColors.Black);

        SKRect bounds;
        catPath.GetBounds(out bounds);

        canvas.Translate(info.Width / 2, info.Height / 2);

        canvas.Scale(0.9f * Math.Min(info.Width / bounds.Width,
                                     info.Height / bounds.Height));

        canvas.Translate(-bounds.MidX, -bounds.MidY);

        canvas.DrawPath(catPath, paint);
    }
}
```

下面是运行的程序：

[![](path-data-images/pathdatacat-small.png "路径数据 Cat 页面的三个屏幕截图")](path-data-images/pathdatacat-large.png#lightbox "路径数据 Cat 页面的三个屏幕截图")

通常情况下，当`SKPath`对象定义为字段，必须在构造函数或另一种方法中定义路径的轮廓。 使用 SVG 路径数据时，但是，你已了解可以完全在字段定义中指定的路径。

较早**难看模拟时钟**示例[**旋转转换**](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/rotate.md)文章显示时钟指针为简单的线条。 **相当模拟时钟**以下程序将替换这些行`SKPath`定义为字段中的对象[ `PrettyAnalogClockPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/PrettyAnalogClockPage.cs)类连同`SKPaint`对象：

```csharp
public class PrettyAnalogClockPage : ContentPage
{
    ...
    // Clock hands pointing straight up
    SKPath hourHandPath = SKPath.ParseSvgPathData(
        "M 0 -60 C   0 -30 20 -30  5 -20 L  5   0" +
                "C   5 7.5 -5 7.5 -5   0 L -5 -20" +
                "C -20 -30  0 -30  0 -60 Z");

    SKPath minuteHandPath = SKPath.ParseSvgPathData(
        "M 0 -80 C   0 -75  0 -70  2.5 -60 L  2.5   0" +
                "C   2.5 5 -2.5 5 -2.5   0 L -2.5 -60" +
                "C 0 -70  0 -75  0 -80 Z");

    SKPath secondHandPath = SKPath.ParseSvgPathData(
        "M 0 10 L 0 -80");

    // SKPaint objects
    SKPaint handStrokePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Black,
        StrokeWidth = 2,
        StrokeCap = SKStrokeCap.Round
    };

    SKPaint handFillPaint = new SKPaint
    {
        Style = SKPaintStyle.Fill,
        Color = SKColors.Gray
    };
    ...
}
```

小时和分钟手现在具有括起来的区域。 若要使这些手各不相同，它们绘制黑色边框和灰色填充使用`handStrokePaint`和`handFillPaint`对象。

在早期**难看模拟时钟**示例中，在小圆圈标记小时和分钟绘制在循环中。 在此**相当模拟时钟**示例，使用完全不同的方法： 小时和分钟标记是使用绘制的虚线`minuteMarkPaint`和`hourMarkPaint`对象：

```csharp
public class PrettyAnalogClockPage : ContentPage
{
    ...
    SKPaint minuteMarkPaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Black,
        StrokeWidth = 3,
        StrokeCap = SKStrokeCap.Round,
        PathEffect = SKPathEffect.CreateDash(new float[] { 0, 3 * 3.14159f }, 0)
    };

    SKPaint hourMarkPaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Black,
        StrokeWidth = 6,
        StrokeCap = SKStrokeCap.Round,
        PathEffect = SKPathEffect.CreateDash(new float[] { 0, 15 * 3.14159f }, 0)
    };
    ...
}
```

[**点和短划线**](~/xamarin-forms/user-interface/graphics/skiasharp/paths/dots.md)文章讨论如何使用[ `SKPathEffect.CreateDash` ](xref:SkiaSharp.SKPathEffect.CreateDash*)方法来创建一条虚线。 第一个参数是`float`通常包含两个元素的数组:第一个元素是短划线的长度, 第二个元素是短划线之间的间隔。 当`StrokeCap`属性设置为`SKStrokeCap.Round`，然后向上舍入虚线的末端有效地通过短划线的两面上的笔划宽度加长短划线的长度。 因此，将第一个数组元素设置为 0 创建点线。

这些点之间的距离所依据的第二个数组元素。 如稍后您将看到，这两个`SKPaint`对象用于绘制一个半径为 90 个单位的圆圈。 因此，此圆的周长是 180π，这意味着在 60 分钟标记必须出现每 3 π 单位，这是中的第二个值`float`数组中`minuteMarkPaint`。 12 小时标记必须出现每 15π 单位，即在第二个值`float`数组。

`PrettyAnalogClockPage`类将计时器设置为要使之无效的图面每隔 16 毫秒和`PaintSurface`按照这个速率调用处理程序。 先前的定义`SKPath`和`SKPaint`对象允许的非常干净的绘制代码：

```csharp
public class PrettyAnalogClockPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Transform for 100-radius circle in center
        canvas.Translate(info.Width / 2, info.Height / 2);
        canvas.Scale(Math.Min(info.Width / 200, info.Height / 200));

        // Draw circles for hour and minute marks
        SKRect rect = new SKRect(-90, -90, 90, 90);
        canvas.DrawOval(rect, minuteMarkPaint);
        canvas.DrawOval(rect, hourMarkPaint);

        // Get time
        DateTime dateTime = DateTime.Now;

        // Draw hour hand
        canvas.Save();
        canvas.RotateDegrees(30 * dateTime.Hour + dateTime.Minute / 2f);
        canvas.DrawPath(hourHandPath, handStrokePaint);
        canvas.DrawPath(hourHandPath, handFillPaint);
        canvas.Restore();

        // Draw minute hand
        canvas.Save();
        canvas.RotateDegrees(6 * dateTime.Minute + dateTime.Second / 10f);
        canvas.DrawPath(minuteHandPath, handStrokePaint);
        canvas.DrawPath(minuteHandPath, handFillPaint);
        canvas.Restore();

        // Draw second hand
        double t = dateTime.Millisecond / 1000.0;

        if (t < 0.5)
        {
            t = 0.5 * Easing.SpringIn.Ease(t / 0.5);
        }
        else
        {
            t = 0.5 * (1 + Easing.SpringOut.Ease((t - 0.5) / 0.5));
        }

        canvas.Save();
        canvas.RotateDegrees(6 * (dateTime.Second + (float)t));
        canvas.DrawPath(secondHandPath, handStrokePaint);
        canvas.Restore();
    }
}
```

一些特殊的内容是在第二个指针，但是。 因为在更新时钟每隔 16 毫秒`Millisecond`属性的`DateTime`值可能可用于进行动画处理扫描第二个手动而不是一个在离散的跳转，将移动从第二个到第二个。 但此代码不允许可顺畅地移动。 相反，它使用 Xamarin.Forms [ `SpringIn` ](xref:Xamarin.Forms.Easing.SpringIn)并[ `SpringOut` ](xref:Xamarin.Forms.Easing.SpringOut)动画的缓动函数的一种不同的移动。 这些缓动函数会导致第二个指针以移动方式 jerkier&mdash;只能回滚一个小之前移动，然后稍有，遗憾的过度排除其目标，影响无法重现这些静态的屏幕截图中：

[![](path-data-images/prettyanalogclock-small.png "三倍的非常模拟时钟页屏幕截图")](path-data-images/prettyanalogclock-large.png#lightbox "带来三倍的非常模拟时钟页屏幕截图")


## <a name="related-links"></a>相关链接

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
