---
title: SkiaSharp 绘制一个简单的圆圈
description: 本文说明 SkiaSharp 绘制，在 Xamarin.Forms 应用程序中包括画布和绘制对象的基础知识，并演示此示例代码。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: E3A4E373-F65D-45C8-8E77-577A804AC3F8
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2017
ms.openlocfilehash: 484a3ac742d162e1d6faaa38ad2e4eb056ed64a3
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2019
ms.locfileid: "68644487"
---
# <a name="drawing-a-simple-circle-in-skiasharp"></a>SkiaSharp 绘制一个简单的圆圈

[![下载示例](~/media/shared/download.png)下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_了解 SkiaSharp 绘制，包括画布的基础知识和绘制对象_

本文介绍在 Xamarin.Forms 中使用 SkiaSharp，包括创建绘制图形的概念`SKCanvasView`对象以承载图形，处理`PaintSurface`事件，以及如何使用`SKPaint`要指定颜色和其他图形对象特性。

[ **SkiaSharpFormsDemos** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)程序包含本系列的 SkiaSharp 文章的所有示例代码。 第一页是已获授权**简单的圆圈**，并调用 page 类[ `SimpleCirclePage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/SimpleCirclePage.cs)。 此代码演示如何在一个半径为 100 像素的页的中心绘制一个圆。 圆的边框为红色，并内部的圆圈将显示为蓝色。

![](circle-images/circleexample.png "以红色标出将蓝色圆形")

[ `SimpleCirle` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/SimpleCirclePage.cs) Page 类派生自`ContentPage`和包含两个`using`SkiaSharp 命名空间指令：

```csharp
using SkiaSharp;
using SkiaSharp.Views.Forms;
```

以下类的构造函数创建[ `SKCanvasView` ](xref:SkiaSharp.Views.Forms.SKCanvasView)对象，将附加的处理程序[ `PaintSurface` ](xref:SkiaSharp.Views.Forms.SKCanvasView.PaintSurface)事件，并设置`SKCanvasView`对象作为页面的内容：

```csharp
public SimpleCirclePage()
{
    Title = "Simple Circle";

    SKCanvasView canvasView = new SKCanvasView();
    canvasView.PaintSurface += OnCanvasViewPaintSurface;
    Content = canvasView;
}
```

`SKCanvasView`占用页面的整个内容区域。 或者，你可以组合`SKCanvasView`与其他 Xamarin.Forms`View`派生类，你将看到在其他示例。

`PaintSurface`事件处理程序完成所有绘制的。 此方法可以多次调用应用程序运行时，这样它应保持的所有信息重新创建所需的图形显示：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    ...
}

```

[ `SKPaintSurfaceEventArgs` ](xref:SkiaSharp.Views.Forms.SKPaintSurfaceEventArgs)伴随该事件的对象具有两个属性：

- [`Info`](xref:SkiaSharp.Views.Forms.SKPaintSurfaceEventArgs.Info) 类型 [`SKImageInfo`](xref:SkiaSharp.SKImageInfo)
- [`Surface`](xref:SkiaSharp.Views.Forms.SKPaintSurfaceEventArgs.Surface) 类型 [`SKSurface`](xref:SkiaSharp.SKSurface)

`SKImageInfo`结构包含信息的绘图图面，最重要的是，其宽度和高度 （像素）。 `SKSurface`对象表示的绘图图面本身。 在此程序中的绘图图面是视频显示，但在其他程序`SKSurface`对象还可以表示使用 SkiaSharp 上绘制的位图。

最重要的属性`SKSurface`是[ `Canvas` ](xref:SkiaSharp.SKSurface.Canvas)类型的[ `SKCanvas` ](xref:SkiaSharp.SKCanvas)。 此类是绘图上下文用于执行实际绘制的图形。 `SKCanvas`对象将封装图形状态，其中包括图形转换和剪辑。

下面是典型的开始部分`PaintSurface`事件处理程序：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();
    ...
}

```

[ `Clear` ](xref:SkiaSharp.SKCanvas.Clear)方法清除在画布上添加透明的颜色。 重载允许您指定背景色的画布。

此处的目标是绘制用蓝色填充的红色圆圈。 由于这一特定图形图像包含两个不同的颜色，因此需要在两个步骤中完成的作业。 第一步是绘制圆的轮廓。 若要指定颜色和其他特征的行，创建并初始化[ `SKPaint` ](xref:SkiaSharp.SKPaint)对象：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    ...
    SKPaint paint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = Color.Red.ToSKColor(),
        StrokeWidth = 25
    };
    ...
}
```

[ `Style` ](xref:SkiaSharp.SKPaint.Style)属性指示你想要的*笔划*行 （在本例中的圆大纲） 而非*填充*内部。 三个成员[ `SKPaintStyle` ](xref:SkiaSharp.SKPaintStyle)枚举如下所示：

- [`Fill`](xref:SkiaSharp.SKPaintStyle.Fill)
- [`Stroke`](xref:SkiaSharp.SKPaintStyle.Stroke)
- [`StrokeAndFill`](xref:SkiaSharp.SKPaintStyle.StrokeAndFill)

默认值为 `Fill`。 第三个选项用于绘制线条笔画和内部填充颜色相同。

设置[ `Color` ](xref:SkiaSharp.SKPaint.Color)属性的值类型[ `SKColor` ](xref:SkiaSharp.SKColor)。 一种方法来获取`SKColor`值是通过将转换 Xamarin.Forms`Color`值设置为`SKColor`值使用扩展方法[ `ToSKColor` ](xref:SkiaSharp.Views.Forms.Extensions.ToSKColor*)。 [ `Extensions` ](xref:SkiaSharp.Views.Forms.Extensions)类中`SkiaSharp.Views.Forms`命名空间包含的其他方法的 Xamarin.Forms 值和 SkiaSharp 值之间进行转换。

[ `StrokeWidth` ](xref:SkiaSharp.SKPaint.StrokeWidth)属性指示的行宽度。 此处它被设置为 25 像素。

使用该`SKPaint`对象来绘制圆：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    ...
    canvas.DrawCircle(info.Width / 2, info.Height / 2, 100, paint);
    ...
}
```

相对于显示器表面的左上角坐标来指定坐标。 X 坐标向右增加和 Y 坐标增加停止运行。 在讨论有关图形中，通常的数学表示法 （x，y） 用于表示一个点。 点 （0，0） 是显示器表面的左上角，通常称为*原点*。

前两个参数的`DrawCircle`表示圆的中心的 X 和 Y 坐标。 这些被分配给一半的宽度和高度显示图面以放置在显示器表面的中心圆的中心。 第三个参数指定圆的半径，并最后一个参数是`SKPaint`对象。

若要填充的圆形内部，可以更改的两个属性`SKPaint`对象，并调用`DrawCircle`试。 此代码还显示了替代方法以获取`SKColor`之一的许多字段的值[ `SKColors` ](xref:SkiaSharp.SKColors)结构：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    ...
    paint.Style = SKPaintStyle.Fill;
    paint.Color = SKColors.Blue;
    canvas.DrawCircle(args.Info.Width / 2, args.Info.Height / 2, 100, paint);
}
```
这一次，`DrawCircle`调用填充使用的新属性的圆圈`SKPaint`对象。

下面是在 iOS、 Android 和通用 Windows 平台上运行的程序：

[![](circle-images/simplecircle-small.png "三个简单的圆圈页屏幕截图")](circle-images/simplecircle-large.png#lightbox "三个简单的圆圈页屏幕截图")

时自行运行该程序，可以打开手机或模拟器侧向若要查看如何重绘图形。 每次需要重绘图形`PaintSurface`再次调用事件处理程序。

还有可能的颜色渐变或位图磁贴图形对象。 上一部分中讨论了这些选项[ **SkiaSharp 着色器**](../effects/shaders/index.md)。

`SKPaint`对象是略高于图形绘制属性的集合。 这些对象是轻量。 可以重用`SKPaint`对象，如此程序执行，也可以创建多个`SKPaint`对象的绘制属性的各种组合。 可以创建并初始化这些对象之外`PaintSurface`事件处理程序，并且您可以将其保存为字段页类中。

> [!NOTE]
> `SKPaint`类定义[ `IsAntialias` ](xref:SkiaSharp.SKPaint.IsAntialias)若要启用在图形的呈现抗锯齿。 抗锯齿通常导致直观地平滑边缘，因此您可能需要将此属性设置为`true`中的大多数您`SKPaint`对象。 为了简单起见，此属性是_不_设置中的大多数示例页面。

尽管圆轮廓的宽度指定为25像素&mdash;或圆&mdash;的半径的四分之一, 但它看起来很薄, 但有一个充分的理由:线条宽度的一半被蓝色圆圈遮盖。 参数`DrawCircle`方法定义一个圆形的抽象几何坐标。 蓝色的内部大小调整为该维度与最接近像素，但 25 个像素宽大纲跨越几何圆圈&mdash;一半内部和外部的下半部分上。

中的下一个示例[与 Xamarin.Forms 集成](~/xamarin-forms/user-interface/graphics/skiasharp/basics/integration.md)文章演示是以可视方式。


## <a name="related-links"></a>相关链接

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
