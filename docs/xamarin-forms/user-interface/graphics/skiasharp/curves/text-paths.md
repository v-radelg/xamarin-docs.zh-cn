---
title: 路径和 SkiaSharp 中的文本
description: 本文探讨了相交 SkiaSharp 路径和文本，并演示此示例代码。
ms.prod: xamarin
ms.assetid: C14C07F6-4A84-4A8C-BDB4-CD61FBF0F79B
ms.technology: xamarin-skiasharp
author: davidbritch
ms.author: dabritch
ms.date: 08/01/2017
ms.openlocfilehash: d38391f3fd0f02dda8bfd92fce650c557bda0153
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2019
ms.locfileid: "68645213"
---
# <a name="paths-and-text-in-skiasharp"></a>路径和 SkiaSharp 中的文本

[![下载示例](~/media/shared/download.png)下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_了解之处的路径和文本_

在现代图形系统中，文本字体是字符轮廓，通常由二次贝塞尔曲线的集合。 因此，许多现代图形系统包含一种机制将文本字符转换为图形路径。

您所见，您可以绘制的文本字符轮廓，以及填充它们。 这使您可以显示这些字符轮廓使用特定的笔划宽度和甚至路径效果，如中所述[**路径效果**](effects.md)一文。 但它还可以将字符字符串转换`SKPath`对象。 这意味着文本概述了可用于与方法中所述的剪辑[**路径和区域中的剪辑**](clipping.md)一文。

除了使用路径效果来绘制字符轮廓，还可以创建基于派生自一个字符串，在路径的路径效果和你甚至可以合并两个效果：

![](text-paths-images/pathsandtextsample.png "文本路径效果")

在上一篇文章中上[**路径效果**](effects.md)，你看到了如何[ `GetFillPath` ](xref:SkiaSharp.SKPaint.GetFillPath(SkiaSharp.SKPath,SkiaSharp.SKPath,SkiaSharp.SKRect,System.Single))方法的`SKPaint`可以获得一个描边路径的概述。 此外可以与派生自字符轮廓路径使用此方法。

最后, 本文演示路径和文本的另一交集:使用[`DrawTextOnPath`](xref:SkiaSharp.SKCanvas.DrawTextOnPath(System.String,SkiaSharp.SKPath,System.Single,System.Single,SkiaSharp.SKPaint)) 的`SKCanvas`方法可以显示文本字符串, 使文本基线跟随曲线路径。

## <a name="text-to-path-conversion"></a>路径转换为文本

[ `GetTextPath` ](xref:SkiaSharp.SKPaint.GetTextPath(System.String,System.Single,System.Single))方法`SKPaint`字符将字符串转换为`SKPath`对象：

```csharp
public SKPath GetTextPath (String text, Single x, Single y)
```

`x`和`y`参数指示的左侧和右侧的文本基线的起始点。 玩中同一此处的角色`DrawText`方法的`SKCanvas`。 在路径中，基线的左侧和右侧的文本将具有 （x，y） 坐标。

`GetTextPath`方法如果你只是想要填充或绘制笔画产生的路径是一种浪费。 正常`DrawText`方法允许您执行该操作。 `GetTextPath`方法是更适用于其他路径所涉及的任务。

这些任务之一剪辑。 **剪辑文本**页创建基于 word"代码。 这些字符轮廓剪切路径 此路径将拉伸为要剪辑包含的图像的位图的页的大小**剪切文本**源代码：

[![](text-paths-images/clippingtext-small.png "剪切文本页的三个屏幕截图")](text-paths-images/clippingtext-large.png#lightbox "剪切文本页的三个屏幕截图")

[ `ClippingTextPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ClippingTextPage.cs)类构造函数将加载作为嵌入资源中存储的位图**媒体**解决方案的文件夹：

```csharp
public class ClippingTextPage : ContentPage
{
    SKBitmap bitmap;

    public ClippingTextPage()
    {
        Title = "Clipping Text";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;

        string resourceID = "SkiaSharpFormsDemos.Media.PageOfCode.png";
        Assembly assembly = GetType().GetTypeInfo().Assembly;

        using (Stream stream = assembly.GetManifestResourceStream(resourceID))
        {
            bitmap = SKBitmap.Decode(stream);
        }
    }
    ...
}
```

`PaintSurface`处理程序首先创建`SKPaint`适用于文本的对象。 `Typeface`属性设置并将`TextSize`，尽管对于此特定应用程序`TextSize`是完全任意的属性。 另外还要注意有没有`Style`设置。

`TextSize`并`Style`属性设置不需要由于此`SKPaint`对象仅用于`GetTextPath`调用使用的文本字符串"代码"。 该处理程序然后测量所产生的`SKPath`对象，应用三种转换，使其居中并缩放到页的大小。 然后可以将路径设置为剪切路径：

```csharp
public class ClippingTextPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear(SKColors.Blue);

        using (SKPaint paint = new SKPaint())
        {
            paint.Typeface = SKTypeface.FromFamilyName(null, SKTypefaceStyle.Bold);
            paint.TextSize = 10;

            using (SKPath textPath = paint.GetTextPath("CODE", 0, 0))
            {
                // Set transform to center and enlarge clip path to window height
                SKRect bounds;
                textPath.GetTightBounds(out bounds);

                canvas.Translate(info.Width / 2, info.Height / 2);
                canvas.Scale(info.Width / bounds.Width, info.Height / bounds.Height);
                canvas.Translate(-bounds.MidX, -bounds.MidY);

                // Set the clip path
                canvas.ClipPath(textPath);
            }
        }

        // Reset transforms
        canvas.ResetMatrix();

        // Display bitmap to fill window but maintain aspect ratio
        SKRect rect = new SKRect(0, 0, info.Width, info.Height);
        canvas.DrawBitmap(bitmap,
            rect.AspectFill(new SKSize(bitmap.Width, bitmap.Height)));
    }
}
```

剪切路径设置后，可以显示位图，并且它将剪辑到字符轮廓。 请注意，使用[ `AspectFill` ](xref:SkiaSharp.SKRect.AspectFill(SkiaSharp.SKSize))方法的`SKRect`计算矩形的同时保留纵横比填充页面。

**文本路径效果**页将单个与号字符转换为路径以创建一维路径效果。 此路径影响的画图对象然后用于绘制轮廓的同一字符的更大版本：

[![](text-paths-images/textpatheffect-small.png "三重的文本路径效果页屏幕截图")](text-paths-images/textpatheffect-large.png#lightbox "带来三倍的文本路径效果页屏幕截图")

中的工作的大部分[ `TextPathEffectPath` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/TextPathEffectPage.cs)类的字段和构造函数中发生。 定义为`SKPaint`字段的两个对象用于两个不同的目的:第一个 (名`textPathPaint`为) 用于将`TextSize`与50的符号转换为1d 路径效果的路径。 第二个 (`textPaint`) 用于显示具有该路径效果 & 符的更大版本。 因此，`Style`的此第二个画图对象设置为`Stroke`，但`StrokeWidth`因为使用 1d 路径效果时，不需要该属性未设置属性：

```csharp
public class TextPathEffectPage : ContentPage
{
    const string character = "@";
    const float littleSize = 50;

    SKPathEffect pathEffect;

    SKPaint textPathPaint = new SKPaint
    {
        TextSize = littleSize
    };

    SKPaint textPaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Black
    };

    public TextPathEffectPage()
    {
        Title = "Text Path Effect";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;

        // Get the bounds of textPathPaint
        SKRect textPathPaintBounds = new SKRect();
        textPathPaint.MeasureText(character, ref textPathPaintBounds);

        // Create textPath centered around (0, 0)
        SKPath textPath = textPathPaint.GetTextPath(character,
                                                    -textPathPaintBounds.MidX,
                                                    -textPathPaintBounds.MidY);
        // Create the path effect
        pathEffect = SKPathEffect.Create1DPath(textPath, littleSize, 0,
                                               SKPath1DPathEffectStyle.Translate);
    }
    ...
}
```

首先使用构造函数`textPathPaint`对象来测量使用 & 符`TextSize`为 50。 该矩形的中心坐标的负然后传递给`GetTextPath`方法将文本转换为路径。 生成的路径具有 （0，0） 中的字符，这非常适合 1d 路径效果的中心点。

你可能会认为`SKPathEffect`在构造函数的末尾创建对象可以设置为`PathEffect`属性的`textPaint`而不是保存为字段。 但这并非很好地工作，因为它失真的结果`MeasureText`调用中`PaintSurface`处理程序：

```csharp
public class TextPathEffectPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Set textPaint TextSize based on screen size
        textPaint.TextSize = Math.Min(info.Width, info.Height);

        // Do not measure the text with PathEffect set!
        SKRect textBounds = new SKRect();
        textPaint.MeasureText(character, ref textBounds);

        // Coordinates to center text on screen
        float xText = info.Width / 2 - textBounds.MidX;
        float yText = info.Height / 2 - textBounds.MidY;

        // Set the PathEffect property and display text
        textPaint.PathEffect = pathEffect;
        canvas.DrawText(character, xText, yText, textPaint);
    }
}
```

`MeasureText`调用用于中心页上的字符。 若要避免出现问题，`PathEffect`已测量文本后，但它在显示之前，将属性设置为绘制对象。

## <a name="outlines-of-character-outlines"></a>字符轮廓的轮廓

通常情况下[ `GetFillPath` ](xref:SkiaSharp.SKPaint.GetFillPath(SkiaSharp.SKPath,SkiaSharp.SKPath,SkiaSharp.SKRect,System.Single))方法的`SKPaint`通过应用油漆属性，将一个路径转换为另一种最值得注意的是描边宽度和路径效果。 当使用不路径会造成影响，`GetFillPath`能有效地创建概述了另一个路径的路径。 中已说明了这**点击到大纲路径**页面[**路径效果**](~/xamarin-forms/user-interface/graphics/skiasharp/curves/effects.md)文章。

您还可以调用`GetFillPath`从返回的路径上`GetTextPath`但首先您可能不完全确定哪些，将看到如下。

**字符轮廓轮廓**页说明该方法。 所有相关的代码位于`PaintSurface`处理程序[ `CharacterOutlineOutlinesPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/CharacterOutlineOutlinesPage.cs)类。

构造函数首先会创建`SKPaint`名为对象`textPaint`与`TextSize`属性基于页的大小。 这将转换为路径使用`GetTextPath`方法。 坐标参数`GetTextPath`有效中心在屏幕上的路径：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    using (SKPaint textPaint = new SKPaint())
    {
        // Set Style for the character outlines
        textPaint.Style = SKPaintStyle.Stroke;

        // Set TextSize based on screen size
        textPaint.TextSize = Math.Min(info.Width, info.Height);

        // Measure the text
        SKRect textBounds = new SKRect();
        textPaint.MeasureText("@", ref textBounds);

        // Coordinates to center text on screen
        float xText = info.Width / 2 - textBounds.MidX;
        float yText = info.Height / 2 - textBounds.MidY;

        // Get the path for the character outlines
        using (SKPath textPath = textPaint.GetTextPath("@", xText, yText))
        {
            // Create a new path for the outlines of the path
            using (SKPath outlinePath = new SKPath())
            {
                // Convert the path to the outlines of the stroked path
                textPaint.StrokeWidth = 25;
                textPaint.GetFillPath(textPath, outlinePath);

                // Stroke that new path
                using (SKPaint outlinePaint = new SKPaint())
                {
                    outlinePaint.Style = SKPaintStyle.Stroke;
                    outlinePaint.StrokeWidth = 5;
                    outlinePaint.Color = SKColors.Red;

                    canvas.DrawPath(outlinePath, outlinePaint);
                }
            }
        }
    }
}
```

`PaintSurface`处理程序然后将创建名为的新路径`outlinePath`。 这将成为对的调用中的目标路径`GetFillPath`。 `StrokeWidth` 25 原因属性`outlinePath`来描述描画文本字符的 25 个像素宽路径的轮廓。 然后显示此路径为笔划宽度为 5 的红色：

[![](text-paths-images/characteroutlineoutlines-small.png "三个字符轮廓轮廓页屏幕截图")](text-paths-images/characteroutlineoutlines-large.png#lightbox "带来三倍的字符轮廓轮廓页屏幕截图")

仔细看，将看到重叠情况下路径轮廓使尖锐的角。 这些是正常的项目，此过程。

## <a name="text-along-a-path"></a>沿着路径针对文本

通常将水平基线上显示文本。 可以旋转文本运行垂直或沿对角线方向，但在基线仍是一条直线。

有些的时候，但是，当想要将沿着一条曲线的文本。 这种情况[ `DrawTextOnPath` ](xref:SkiaSharp.SKCanvas.DrawTextOnPath(System.String,SkiaSharp.SKPath,System.Single,System.Single,SkiaSharp.SKPaint))方法的`SKCanvas`:

```csharp
public Void DrawTextOnPath (String text, SKPath path, Single hOffset, Single vOffset, SKPaint paint)
```

所做的第一个参数中指定的文本沿路径指定为第二个参数运行。 您可以开始从与路径开头的偏移量处的文本`hOffset`参数。 通常, 路径构成文本基线:文本升部位于路径的一侧, 文本下行字母位于另一侧。 但您可以偏移量中的路径与文本基线`vOffset`参数。

此方法具有任何工具可以提供有关设置的指导`TextSize`属性的`SKPaint`进行大小调整，完全以运行从路径的开头到末尾的文本。 有时您可以找出您自己的文本大小。 其他情况下将需要使用路径测量函数上的下一步的文章中进行描述[**路径信息和枚举**](information.md)。

**循环文本**程序使文本换行圆环。 很容易地确定一个圆的周长，因此很容易地调整大小以完全适合的文本。 `PaintSurface`处理程序[ `CircularTextPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/CircularTextPage.cs)类将计算基于页的大小的圆的半径。 该圆形将成为`circularPath`:

```csharp
public class CircularTextPage : ContentPage
{
    const string text = "xt in a circle that shapes the te";
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPath circularPath = new SKPath())
        {
            float radius = 0.35f * Math.Min(info.Width, info.Height);
            circularPath.AddCircle(info.Width / 2, info.Height / 2, radius);

            using (SKPaint textPaint = new SKPaint())
            {
                textPaint.TextSize = 100;
                float textWidth = textPaint.MeasureText(text);
                textPaint.TextSize *= 2 * 3.14f * radius / textWidth;

                canvas.DrawTextOnPath(text, circularPath, 0, 0, textPaint);
            }
        }
    }
}
```

`TextSize`属性的`textPaint`，使文本宽度与匹配圆的周长然后进行调整：

[![](text-paths-images/circulartext-small.png "循环的文本页的三个屏幕截图")](text-paths-images/circulartext-large.png#lightbox "带来三倍的循环的文本页屏幕截图")

文本本身也被选为有点循环:词 "圆圈" 既是句子的主题, 也是 prepositional 短语的对象。

## <a name="related-links"></a>相关链接

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
