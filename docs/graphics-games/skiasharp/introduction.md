---
title: SkiaSharp 独立于平台的示例
description: 本文档提供了简要介绍了核心 SkiaSharp 概念。 具体而言，它讨论了获取并在 SKCanvas 上绘制。
ms.prod: xamarin
ms.techonology: xamarin-skiasharp
ms.assetid: 19506F08-2603-465E-A806-6BD01638DE90
author: davidbritch
ms.author: dabritch
ms.date: 10/03/2018
ms.openlocfilehash: 4d0e57b98a479112b9fdf4f9c503418f3966cc73
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61158884"
---
# <a name="skiasharp-platform-independent-examples"></a>SkiaSharp 独立于平台的示例

_这提供了独立于平台的简介 SkiaSharp 背后的概念_

SkiaSharp 提供了丰富且功能强大 2D 图形 API，可用于呈现到 2D 的缓冲区。  您可以使用这些实现自定义用户界面元素，并可以合并到你的应用程序的 2D 图形。 SkiaSharp 是绑定到的.NET [Skia](https://skia.org)库和继承的功能和此库的能力。

库是目前跨平台以[NuGet 包](https://www.nuget.org/packages/SkiaSharp)，您可以通过添加 NuGet 引用将它添加到你的项目。

若要绘制，你的代码将创建`SkCanvas`它描述了将执行绘制操作的图面。

## <a name="obtaining-an-skcanvas"></a>获取 SKCanvas

```csharp
using (var surface = SKSurface.Create (width: 640, height: 480, SKImageInfo.PlatformColorType, SKAlphaType.Premul)) {
    SKCanvas myCanvas = surface.Canvas;

    // Your drawing code goes here.
}
```

## <a name="drawing-on-skcanvas"></a>在 SKCanvas 上绘制

`SKCanvas`使用绘制模型在精神上类似于其他绘制模型，您可能熟悉，它使用可选的透明度通道使用的颜色，并可以绘制线条、 弧线、 文本和图像。

以下是一些可以使用 SkiaSharp 许多不同的事物。  以下变量示例中`canvas`SKCanvas 类型。

### <a name="drawing-xamagon"></a>绘制 Xamagon

此示例中绘制 Xamarin 的 Xamagon 徽标：

```csharp
// clear the canvas / fill with white
canvas.Clear (SKColors.White);

// set up drawing tools
using (var paint = new SKPaint ()) {
  paint.IsAntialias = true;
  paint.Color = new SKColor (0x2c, 0x3e, 0x50);
  paint.StrokeCap = SKStrokeCap.Round;

  // create the Xamagon path
  using (var path = new SKPath ()) {
    path.MoveTo (71.4311121f, 56f);
    path.CubicTo (68.6763107f, 56.0058575f, 65.9796704f, 57.5737917f, 64.5928855f, 59.965729f);
    path.LineTo (43.0238921f, 97.5342563f);
    path.CubicTo (41.6587026f, 99.9325978f, 41.6587026f, 103.067402f, 43.0238921f, 105.465744f);
    path.LineTo (64.5928855f, 143.034271f);
    path.CubicTo (65.9798162f, 145.426228f, 68.6763107f, 146.994582f, 71.4311121f, 147f);
    path.LineTo (114.568946f, 147f);
    path.CubicTo (117.323748f, 146.994143f, 120.020241f, 145.426228f, 121.407172f, 143.034271f);
    path.LineTo (142.976161f, 105.465744f);
    path.CubicTo (144.34135f, 103.067402f, 144.341209f, 99.9325978f, 142.976161f, 97.5342563f);
    path.LineTo (121.407172f, 59.965729f);
    path.CubicTo (120.020241f, 57.5737917f, 117.323748f, 56.0054182f, 114.568946f, 56f);
    path.LineTo (71.4311121f, 56f);
    path.Close ();

    // draw the Xamagon path
    canvas.DrawPath (path, paint);
  }
}
```

### <a name="drawing-text"></a>绘制文本

```csharp
// clear the canvas / fill with white
canvas.DrawColor (SKColors.White);

// set up drawing tools
using (var paint = new SKPaint ()) {
  paint.TextSize = 64.0f;
  paint.IsAntialias = true;
  paint.Color = new SKColor (0x42, 0x81, 0xA4);
  paint.IsStroke = false;

  // draw the text
  canvas.DrawText ("Skia", 0.0f, 64.0f, paint);
}
```

### <a name="drawing-bitmaps"></a>绘制位图

```csharp
Stream fileStream = File.OpenRead ("MyImage.png");

// clear the canvas / fill with white
canvas.DrawColor (SKColors.White);

// decode the bitmap from the stream
using (var stream = new SKManagedStream(fileStream))
using (var bitmap = SKBitmap.Decode(stream))
using (var paint = new SKPaint()) {
  canvas.DrawBitmap(bitmap, SKRect.Create(Width, Height), paint);
}
```

### <a name="drawing-with-image-filters"></a>使用映像筛选器进行绘制

```csharp
Stream fileStream = File.OpenRead ("MyImage.png"); // open a stream to an image file

// clear the canvas / fill with white
canvas.DrawColor (SKColors.White);

// decode the bitmap from the stream
using (var stream = new SKManagedStream(fileStream))
using (var bitmap = SKBitmap.Decode(stream))
using (var paint = new SKPaint()) {
  // create the image filter
  using (var filter = SKImageFilter.CreateBlur(5, 5)) {
    paint.ImageFilter = filter;

    // draw the bitmap through the filter
    canvas.DrawBitmap(bitmap, SKRect.Create(width, height), paint);
  }
}
```

## <a name="more-information"></a>详细信息

有关使用 SkiaSharp 详细信息可在[API 文档](https://docs.microsoft.com/dotnet/api/skiasharp)
