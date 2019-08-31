---
title: SkiaSharp 位图平铺
description: 平铺区域使用位图水平和垂直方向重复。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 9ED14E07-4DC8-4B03-8A33-772838BF51EA
author: davidbritch
ms.author: dabritch
ms.date: 08/23/2018
ms.openlocfilehash: f019b6e031774d7bcdf593015394d0c73c96949b
ms.sourcegitcommit: 1e3a0d853669dcc57d5dee0894d325d40c7d8009
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2019
ms.locfileid: "70198658"
---
# <a name="skiasharp-bitmap-tiling"></a>SkiaSharp 位图平铺

[![下载示例](~/media/shared/download.png)下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

[![下载示例](~/media/shared/download.png)下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/catclock)

如您在两个之前的文章中所见[ `SKShader` ](xref:SkiaSharp.SKShader)类可以创建线性或循环渐变。 这篇文章重点`SKShader`使用一个位图平铺区域的对象。 可以重复位图，水平或垂直地在其原始方向或或者水平和垂直翻转。 翻转可避免磁贴之间的不连续性：

![Bitmap 平铺示例](bitmap-tiling-images/BitmapTilingSample.png "Bitmap 平铺示例")

静态[ `SKShader.CreateBitmap` ](xref:SkiaSharp.SKShader.CreateBitmap(SkiaSharp.SKBitmap,SkiaSharp.SKShaderTileMode,SkiaSharp.SKShaderTileMode))创建此着色器的方法有`SKBitmap`参数和两个成员[ `SKShaderTileMode` ](xref:SkiaSharp.SKShaderTileMode)枚举：

```csharp
public static SKShader CreateBitmap (SKBitmap src, SKShaderTileMode tmx, SKShaderTileMode tmy)
```

两个参数指示用于水平平铺和垂直平铺模式。 这是相同的`SKShaderTileMode`也用于渐变方法的枚举。

一个[ `CreateBitmap` ](xref:SkiaSharp.SKShader.CreateBitmap(SkiaSharp.SKBitmap,SkiaSharp.SKShaderTileMode,SkiaSharp.SKShaderTileMode,SkiaSharp.SKMatrix))重载包含`SKMatrix`参数上的平铺位图执行转换：

```csharp
public static SKShader CreateBitmap (SKBitmap src, SKShaderTileMode tmx, SKShaderTileMode tmy, SKMatrix localMatrix)
```

本文包含平铺位图中使用此矩阵转换的几个示例。

## <a name="exploring-the-tile-modes"></a>浏览平铺模式

中的第一个程序**位图平铺**一部分**着色器和其他效果**页[ **SkiaSharpFormsDemos** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)示例演示了两个效果`SKShaderTileMode`参数。 **位图磁贴翻转模式**XAML 文件实例化`SKCanvasView`并将两个`Picker`允许您选择的视图`SKShaderTilerMode`水平和垂直平铺的值。 请注意，数组`SKShaderTileMode`中定义成员`Resources`部分：

```xaml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp;assembly=SkiaSharp"
             xmlns:skiaforms="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Effects.BitmapTileFlipModesPage"
             Title="Bitmap Tile Flip Modes">

    <ContentPage.Resources>
        <x:Array x:Key="tileModes"
                 Type="{x:Type skia:SKShaderTileMode}">
            <x:Static Member="skia:SKShaderTileMode.Clamp" />
            <x:Static Member="skia:SKShaderTileMode.Repeat" />
            <x:Static Member="skia:SKShaderTileMode.Mirror" />
        </x:Array>
    </ContentPage.Resources>

    <StackLayout>
        <skiaforms:SKCanvasView x:Name="canvasView"
                                VerticalOptions="FillAndExpand"
                                PaintSurface="OnCanvasViewPaintSurface" />

        <Picker x:Name="xModePicker"
                Title="Tile X Mode"
                Margin="10, 0"
                ItemsSource="{StaticResource tileModes}"
                SelectedIndex="0"
                SelectedIndexChanged="OnPickerSelectedIndexChanged" />

        <Picker x:Name="yModePicker"
                Title="Tile Y Mode"
                Margin="10, 10"
                ItemsSource="{StaticResource tileModes}"
                SelectedIndex="0"
                SelectedIndexChanged="OnPickerSelectedIndexChanged" />

    </StackLayout>
</ContentPage>
```

显示坐 monkey 的位图资源中加载的代码隐藏文件的构造函数。 它首先裁剪图像使用[ `ExtractSubset` ](xref:SkiaSharp.SKBitmap.ExtractSubset(SkiaSharp.SKBitmap,SkiaSharp.SKRectI))方法的`SKBitmap`以便 head 和英尺触摸位图的边缘。 然后，构造函数使用[ `Resize` ](xref:SkiaSharp.SKBitmap.Resize(SkiaSharp.SKImageInfo,SkiaSharp.SKBitmapResizeMethod))方法来创建另一位图的大小的一半。 这些更改使位图更适用于平铺：

```csharp
public partial class BitmapTileFlipModesPage : ContentPage
{
    SKBitmap bitmap;

    public BitmapTileFlipModesPage ()
    {
        InitializeComponent ();

        SKBitmap origBitmap = BitmapExtensions.LoadBitmapResource(
            GetType(), "SkiaSharpFormsDemos.Media.SeatedMonkey.jpg");

        // Define cropping rect
        SKRectI cropRect = new SKRectI(5, 27, 296, 260);

        // Get the cropped bitmap
        SKBitmap croppedBitmap = new SKBitmap(cropRect.Width, cropRect.Height);
        origBitmap.ExtractSubset(croppedBitmap, cropRect);

        // Resize to half the width and height
        SKImageInfo info = new SKImageInfo(cropRect.Width / 2, cropRect.Height / 2);
        bitmap = croppedBitmap.Resize(info, SKBitmapResizeMethod.Box);
    }

    void OnPickerSelectedIndexChanged(object sender, EventArgs args)
    {
        canvasView.InvalidateSurface();
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Get tile modes from Pickers
        SKShaderTileMode xTileMode =
            (SKShaderTileMode)(xModePicker.SelectedIndex == -1 ?
                                        0 : xModePicker.SelectedItem);
        SKShaderTileMode yTileMode =
            (SKShaderTileMode)(yModePicker.SelectedIndex == -1 ?
                                        0 : yModePicker.SelectedItem);

        using (SKPaint paint = new SKPaint())
        {
            paint.Shader = SKShader.CreateBitmap(bitmap, xTileMode, yTileMode);
            canvas.DrawRect(info.Rect, paint);
        }
    }
}
```

`PaintSurface`处理程序获取`SKShaderTileMode`两个设置`Picker`视图，并创建`SKShader`对象基于位图和这两个值。 此着色器用来填充画布：

[![位图磁贴翻转模式](bitmap-tiling-images/BitmapTileFlipModes.png "位图磁贴翻转模式")](bitmap-tiling-images/BitmapTileFlipModes-Large.png#lightbox)

在左侧的 iOS 屏幕显示的默认值的效果`SKShaderTileMode.Clamp`。 位图位于左上角。 在位图中，下面的像素为单位的底部行重复一直向下。 位图的右侧，一直在重复的像素为单位的最右侧列。 位图的右下角的深色棕色像素的颜色是画布的其余部分。 很明显，`Clamp`选项几乎从不与位图平铺一起使用 ！

Center 中的 Android 屏幕显示的结果`SKShaderTileMode.Repeat`为两个参数。 水平和垂直重复该磁贴。 通用 Windows 平台屏幕显示了`SKShaderTileMode.Mirror`。 重复但或者水平和垂直翻转磁贴。 此选项的优点是磁贴之间没有任何中断。

请记住，您可将不同的选项，用于水平和垂直重复。 您可以指定`SKShaderTileMode.Mirror`的第二个参数作为`CreateBitmap`但`SKShaderTileMode.Repeat`作为第三个参数。 在每行上仍之间进行交替的正常图像和镜像图像，但无放弃放弃是散乱的。

## <a name="patterned-backgrounds"></a>模式化的背景

位图平铺通常用于从相对较小位图创建图案的背景。 典型的示例是产生效果。

**算法砖墙**页创建类似于整个程序块和两个部分分隔实体的程序块的一个小位图。 下一个示例中使用此程序块，因为它具有静态构造函数创建并使其成为公共静态属性：

```csharp
public class AlgorithmicBrickWallPage : ContentPage
{
    static AlgorithmicBrickWallPage()
    {
        const int brickWidth = 64;
        const int brickHeight = 24;
        const int morterThickness = 6;
        const int bitmapWidth = brickWidth + morterThickness;
        const int bitmapHeight = 2 * (brickHeight + morterThickness);

        SKBitmap bitmap = new SKBitmap(bitmapWidth, bitmapHeight);

        using (SKCanvas canvas = new SKCanvas(bitmap))
        using (SKPaint brickPaint = new SKPaint())
        {
            brickPaint.Color = new SKColor(0xB2, 0x22, 0x22);

            canvas.Clear(new SKColor(0xF0, 0xEA, 0xD6));
            canvas.DrawRect(new SKRect(morterThickness / 2,
                                       morterThickness / 2,
                                       morterThickness / 2 + brickWidth,
                                       morterThickness / 2 + brickHeight),
                                       brickPaint);

            int ySecondBrick = 3 * morterThickness / 2 + brickHeight;

            canvas.DrawRect(new SKRect(0,
                                       ySecondBrick,
                                       bitmapWidth / 2 - morterThickness / 2,
                                       ySecondBrick + brickHeight),
                                       brickPaint);

            canvas.DrawRect(new SKRect(bitmapWidth / 2 + morterThickness / 2,
                                       ySecondBrick,
                                       bitmapWidth,
                                       ySecondBrick + brickHeight),
                                       brickPaint);
        }

        // Save as public property for other programs
        BrickWallTile = bitmap;
    }

    public static SKBitmap BrickWallTile { private set; get; }
    ···
}
```

合成的位图是 70 像素宽和 60 像素高：

![算法的程序块华尔街在摆脱磁贴](bitmap-tiling-images/AlgorithmicBrickWallTile.png "算法程序块华尔街在摆脱磁贴")

其余**算法砖墙**页创建`SKShader`水平和垂直重复此映像的对象：

```csharp
public class AlgorithmicBrickWallPage : ContentPage
{
    ···
    public AlgorithmicBrickWallPage ()
    {
        Title = "Algorithmic Brick Wall";

        // Create SKCanvasView
        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPaint paint = new SKPaint())
        {
            // Create bitmap tiling
            paint.Shader = SKShader.CreateBitmap(BrickWallTile,
                                                 SKShaderTileMode.Repeat,
                                                 SKShaderTileMode.Repeat);
            // Draw background
            canvas.DrawRect(info.Rect, paint);
        }
    }
}
```

下面是结果：

[![算法砖墙](bitmap-tiling-images/AlgorithmicBrickWall.png "算法砖墙")](bitmap-tiling-images/AlgorithmicBrickWall-Large.png#lightbox)

您可能希望有点更现实可行的内容。 在这种情况下，您可以拍摄照片的实际砖墙然后裁剪。 此位图是 300 像素宽和 150 像素高：

![程序块华尔街在摆脱磁贴](bitmap-tiling-images/BrickWallTile.jpg "程序块华尔街在摆脱磁贴")

在中使用此位图**摄影砖墙**页：

```csharp
public class PhotographicBrickWallPage : ContentPage
{
    SKBitmap bitmap = BitmapExtensions.LoadBitmapResource(
                        typeof(PhotographicBrickWallPage),
                        "SkiaSharpFormsDemos.Media.BrickWallTile.jpg");

    public PhotographicBrickWallPage()
    {
        Title = "Photographic Brick Wall";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPaint paint = new SKPaint())
        {
            // Create bitmap tiling
            paint.Shader = SKShader.CreateBitmap(bitmap,
                                                 SKShaderTileMode.Mirror,
                                                 SKShaderTileMode.Mirror);
            // Draw background
            canvas.DrawRect(info.Rect, paint);
        }
    }
}
```

请注意，`SKShaderTileMode`自变量`CreateBitmap`都是`Mirror`。 使用实际的映像创建的磁贴时，此选项是通常只需要。 镜像磁贴可避免中断：

[![照片砖墙](bitmap-tiling-images/PhotographicBrickWall.png "照片砖墙")](bitmap-tiling-images/PhotographicBrickWall-Large.png#lightbox)

所需一些工作以磁贴获取合适的位图。 这个不很好地起作用，因为突出显示的较暗的程序块太多。 它定期出现在重复的图像，显示此砖墙从较小位图构造这一事实。

**媒体**的文件夹[ **SkiaSharpFormsDemos** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)示例还包括 stone 墙上的此图像：

![Stone 华尔街在摆脱磁贴](bitmap-tiling-images/StoneWallTile.jpg "Stone 华尔街在摆脱磁贴")

但是，原始位图是有点太大的磁贴。 无法调整大小，但`SKShader.CreateBitmap`方法还可以调整磁贴大小通过将转换应用于它。 此选项进行了演示**Stone 墙**页：

```csharp
public class StoneWallPage : ContentPage
{
    SKBitmap bitmap = BitmapExtensions.LoadBitmapResource(
                        typeof(StoneWallPage),
                        "SkiaSharpFormsDemos.Media.StoneWallTile.jpg");

    public StoneWallPage()
    {
        Title = "Stone Wall";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPaint paint = new SKPaint())
        {
            // Create scale transform
            SKMatrix matrix = SKMatrix.MakeScale(0.5f, 0.5f);

            // Create bitmap tiling
            paint.Shader = SKShader.CreateBitmap(bitmap,
                                                 SKShaderTileMode.Mirror,
                                                 SKShaderTileMode.Mirror,
                                                 matrix);
            // Draw background
            canvas.DrawRect(info.Rect, paint);
        }
    }
}
```

`SKMatrix`创建值以将图像缩放为其原始大小的一半：

[![石 Wall](bitmap-tiling-images/StoneWall.png "石墙上")](bitmap-tiling-images/StoneWall-Large.png#lightbox)

工作转换在运行中使用的原始位图上`CreateBitmap`方法？ 或没有转换磁贴到结果数组？ 

若要回答此问题的简单方法是包含作为转换的一部分的旋转：

```csharp
SKMatrix matrix = SKMatrix.MakeScale(0.5f, 0.5f);
SKMatrix.PostConcat(ref matrix, SKMatrix.MakeRotationDegrees(15));
```

如果转换应用于单个磁贴，然后应旋转磁贴的每个重复的图像，，结果将包含许多的中断。 但可通过此磁贴的复合数组将转换的屏幕截图：

[![石墙上旋转](bitmap-tiling-images/StoneWallRotated.png "石墙上旋转")](bitmap-tiling-images/StoneWallRotated-Large.png#lightbox)

在部分[**磁贴的对齐方式**](#tile-alignment)，可以看到示例转换变换应用到着色器。

独立[ **Cat 时钟**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/catclock)示例 (不属于**SkiaSharpFormsDemos**) 模拟使用基于此 240 像素的方形位图位图平铺的 wood 粒度背景：

![木纹粒度](bitmap-tiling-images/WoodGrain.png "木纹粒度")

这就是照片的木质基底。 `SKShaderTileMode.Mirror`选项，它显示为更大的区域的 wood:

[![Cat 时钟](bitmap-tiling-images/CatClock.png "Cat 时钟")](bitmap-tiling-images/CatClock-Large.png#lightbox)

## <a name="tile-alignment"></a>磁贴的对齐方式

到目前为止显示的所有示例都使用着色器创建的`SKShader.CreateBitmap`以覆盖整个画布。 在大多数情况下，您将使用位图平铺以供填写更小的区域或 （更多很少） 用于填充的粗线的内部。 下面是用于较小的矩形的照片砖墙磁贴：

[![磁贴对齐](bitmap-tiling-images/TileAlignment.png "磁贴的对齐方式")](bitmap-tiling-images/TileAlignment-Large.png#lightbox)

这可能看起来没问题，或可能不是。 也许你是矩形的打扰平铺模式不会以开头的左上角的完整程序块。 这是因为着色器符合画布而不是它们修饰的图形化的对象。

解决方法很简单。 创建`SKMatrix`值基于的平移转换。 转换有效地将平铺的模式切换到想要对齐的平铺窗口左上角的点。 这种方法进行了演示**磁贴的对齐方式**页上，创建了未对齐的磁贴，如上所示的映像：

```csharp
public class TileAlignmentPage : ContentPage
{
    bool isAligned;

    public TileAlignmentPage()
    {
        Title = "Tile Alignment";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;

        // Add tap handler
        TapGestureRecognizer tap = new TapGestureRecognizer();
        tap.Tapped += (sender, args) =>
        {
            isAligned ^= true;
            canvasView.InvalidateSurface();
        };
        canvasView.GestureRecognizers.Add(tap);

        Content = canvasView;
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPaint paint = new SKPaint())
        {
            SKRect rect = new SKRect(info.Width / 7,
                                     info.Height / 7,
                                     6 * info.Width / 7,
                                     6 * info.Height / 7);

            // Get bitmap from other program
            SKBitmap bitmap = AlgorithmicBrickWallPage.BrickWallTile;

            // Create bitmap tiling
            if (!isAligned)
            {
                paint.Shader = SKShader.CreateBitmap(bitmap,
                                                     SKShaderTileMode.Repeat,
                                                     SKShaderTileMode.Repeat);
            }
            else
            {
                SKMatrix matrix = SKMatrix.MakeTranslation(rect.Left, rect.Top);

                paint.Shader = SKShader.CreateBitmap(bitmap,
                                                     SKShaderTileMode.Repeat,
                                                     SKShaderTileMode.Repeat,
                                                     matrix);
            }

            // Draw rectangle
            canvas.DrawRect(rect, paint);
        }
    }
}
```

**磁贴对齐**页包含`TapGestureRecognizer`。 点击或单击屏幕上，并计划切换到`SKShader.CreateBitmap`方法替换`SKMatrix`参数。 此转换将转移模式，以使左上角包含完整的程序块：

[![磁贴的对齐方式点击](bitmap-tiling-images/TileAlignmentTapped.png "磁贴点击的对齐方式")](bitmap-tiling-images/TileAlignmentTapped-Large.png#lightbox)

此方法还可用于确保中该对象来绘制的区域中居中显示位图平铺的模式。 在中**居中磁贴**页上，`PaintSurface`处理程序首先计算坐标，因为如果它要在画布的中心中显示单个位图。 它然后使用这些坐标来创建为一个平移`SKShader.CreateBitmap`。 此转换将整个模式，以使居中磁贴：

```csharp
public class CenteredTilesPage : ContentPage
{
    SKBitmap bitmap = BitmapExtensions.LoadBitmapResource(
                        typeof(CenteredTilesPage),
                        "SkiaSharpFormsDemos.Media.monkey.png");

    public CenteredTilesPage ()
    {
        Title = "Centered Tiles";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Find coordinates to center bitmap in canvas...
        float x = (info.Width - bitmap.Width) / 2f;
        float y = (info.Height - bitmap.Height) / 2f;

        using (SKPaint paint = new SKPaint())
        {
            // ... but use them to create a translate transform
            SKMatrix matrix = SKMatrix.MakeTranslation(x, y);
            paint.Shader = SKShader.CreateBitmap(bitmap, 
                                                 SKShaderTileMode.Repeat, 
                                                 SKShaderTileMode.Repeat, 
                                                 matrix);

            // Use that tiled bitmap pattern to fill a circle
            canvas.DrawCircle(info.Rect.MidX, info.Rect.MidY,
                              Math.Min(info.Width, info.Height) / 2,
                              paint);
        }
    }
}
```

`PaintSurface`处理程序最后会在画布的中心绘制一个圆圈。 果然，一个磁贴是完全在圆的中心和其他排列在对称模式：

[![居中磁贴](bitmap-tiling-images/CenteredTiles.png "居中磁贴")](bitmap-tiling-images/CenteredTiles-Large.png#lightbox)

居中的另一种方法是实际更容易。 而不是构造的磁贴放在中心一个平移，您可以居中平铺模式的角。 在`SKMatrix.MakeTranslation`调用，请使用画布的中心的自变量：

```csharp
SKMatrix matrix = SKMatrix.MakeTranslation(info.Rect.MidX, info.Rect.MidY);
```

模式是仍居中和对称的但没有磁贴是在中心：

[![居中磁贴备用](bitmap-tiling-images/CenteredTilesAlternate.png "居中磁贴备用")](bitmap-tiling-images/CenteredTilesAlternate-Large.png#lightbox)

## <a name="simplification-through-rotation"></a>通过旋转简化

有时使用中的旋转转换`SKShader.CreateBitmap`方法可以简化位图磁贴。 尝试定义的链链接 fence 的磁贴时，这将成为显而易见。 **ChainLinkTile.cs**文件创建的磁贴 （带有粉红色背景为清晰起见） 如下所示：

![硬的链链接磁贴](bitmap-tiling-images/HardChainLinkTile.png "硬的链链接磁贴")

该图块需要包含两个链接，以便代码将该图块划分为四个象限。 左上角和右下象限是相同的但它们不完整。 电线必须与在右上方和左下象限中一些其他绘制小凹槽必须处理的。 所有这些工作的文件是长达 174 行。

事实证明要实现更轻松地创建此磁贴：

![更轻松的链链接磁贴](bitmap-tiling-images/EasierChainLinkTile.png "更轻松的链链接磁贴")

如果位图磁贴着色器是旋转 90 度，视觉对象都几乎相同。

代码以创建更轻松的链链接磁贴是的一部分**链链接磁贴**页。 构造函数确定的设备上，运行该程序，然后调用的类型的磁贴大小`CreateChainLinkTile`，它可绘制线条、 路径和渐变着色器使用位图上：

```csharp
public class ChainLinkFencePage : ContentPage
{
    ···
    SKBitmap tileBitmap;

    public ChainLinkFencePage ()
    {
        Title = "Chain-Link Fence";

        // Create bitmap for chain-link tiling
        int tileSize = Device.Idiom == TargetIdiom.Desktop ? 64 : 128;
        tileBitmap = CreateChainLinkTile(tileSize);

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }

    SKBitmap CreateChainLinkTile(int tileSize)
    {
        tileBitmap = new SKBitmap(tileSize, tileSize);
        float wireThickness = tileSize / 12f;

        using (SKCanvas canvas = new SKCanvas(tileBitmap))
        using (SKPaint paint = new SKPaint())
        {
            canvas.Clear();
            paint.Style = SKPaintStyle.Stroke;
            paint.StrokeWidth = wireThickness;
            paint.IsAntialias = true;

            // Draw straight wires first
            paint.Shader = SKShader.CreateLinearGradient(new SKPoint(0, 0),
                                                         new SKPoint(0, tileSize),
                                                         new SKColor[] { SKColors.Silver, SKColors.Black },
                                                         new float[] { 0.4f, 0.6f },
                                                         SKShaderTileMode.Clamp);

            canvas.DrawLine(0, tileSize / 2,
                            tileSize / 2, tileSize / 2 - wireThickness / 2, paint);

            canvas.DrawLine(tileSize, tileSize / 2,
                            tileSize / 2, tileSize / 2 + wireThickness / 2, paint);

            // Draw curved wires
            using (SKPath path = new SKPath())
            {
                path.MoveTo(tileSize / 2, 0);
                path.LineTo(tileSize / 2 - wireThickness / 2, tileSize / 2);
                path.ArcTo(wireThickness / 2, wireThickness / 2,
                           0,
                           SKPathArcSize.Small,
                           SKPathDirection.CounterClockwise,
                           tileSize / 2, tileSize / 2 + wireThickness / 2);

                paint.Shader = SKShader.CreateLinearGradient(new SKPoint(0, 0),
                                                             new SKPoint(0, tileSize),
                                                             new SKColor[] { SKColors.Silver, SKColors.Black },
                                                             null,
                                                             SKShaderTileMode.Clamp);
                canvas.DrawPath(path, paint);

                path.Reset();
                path.MoveTo(tileSize / 2, tileSize);
                path.LineTo(tileSize / 2 + wireThickness / 2, tileSize / 2);
                path.ArcTo(wireThickness / 2, wireThickness / 2,
                           0,
                           SKPathArcSize.Small,
                           SKPathDirection.CounterClockwise,
                           tileSize / 2, tileSize / 2 - wireThickness / 2);

                paint.Shader = SKShader.CreateLinearGradient(new SKPoint(0, 0),
                                                             new SKPoint(0, tileSize),
                                                             new SKColor[] { SKColors.White, SKColors.Silver },
                                                             null,
                                                             SKShaderTileMode.Clamp);
                canvas.DrawPath(path, paint);
            }
            return tileBitmap;
        }
    }
    ···
}
```

除了电线，磁贴是透明的这意味着，您可以基于其他显示它。 程序中的一个位图资源加载、 显示它以填充画布，然后顶部绘制着色器：

```csharp
public class ChainLinkFencePage : ContentPage
{
    SKBitmap monkeyBitmap = BitmapExtensions.LoadBitmapResource(
        typeof(ChainLinkFencePage), "SkiaSharpFormsDemos.Media.SeatedMonkey.jpg");
    ···

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        canvas.DrawBitmap(monkeyBitmap, info.Rect, BitmapStretch.UniformToFill, 
                            BitmapAlignment.Center, BitmapAlignment.Start);

        using (SKPaint paint = new SKPaint())
        {
            paint.Shader = SKShader.CreateBitmap(tileBitmap, 
                                                 SKShaderTileMode.Repeat,
                                                 SKShaderTileMode.Repeat,
                                                 SKMatrix.MakeRotationDegrees(45));
            canvas.DrawRect(info.Rect, paint);
        }
    }
}
```

请注意，着色器是旋转 45 度，因此它是实际的链链接 fence 等面向：

[![链链接 Fence](bitmap-tiling-images/ChainLinkFence.png "链链接隔离")](bitmap-tiling-images/ChainLinkFence-Large.png#lightbox)

## <a name="animating-bitmap-tiles"></a>具有动画效果的位图磁贴

可以对整个位图平铺模式进行动画处理的矩阵转换进行动画处理。 您可能希望和 / 或水平或垂直移动的模式。 可以通过创建基于不断变化的坐标的平移转换来执行的操作。

还有可能较小位图上绘制或以操作位图的像素位每秒 60 次的速率。 该位图随后用于平铺和整个平铺的模式可能看起来要进行动画处理。 

**经过动画处理的位图磁贴**页展示这种方法。 位图实例化为将字段设为 64 像素正方形。 构造函数调用`DrawBitmap`使其初始外观。 如果`angle`字段是零 （因为它是首次调用该方法时），然后，位图包含 X 未超出两行。这些行进行足够长，以始终可以访问的位图，而不考虑边缘`angle`值： 

```csharp
public class AnimatedBitmapTilePage : ContentPage
{
    const int SIZE = 64;

    SKCanvasView canvasView;
    SKBitmap bitmap = new SKBitmap(SIZE, SIZE);
    float angle;
    ···

    public AnimatedBitmapTilePage ()
    {
        Title = "Animated Bitmap Tile";

        // Initialize bitmap prior to animation
        DrawBitmap();

        // Create SKCanvasView 
        canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }
    ···
    void DrawBitmap()
    {
        using (SKCanvas canvas = new SKCanvas(bitmap))
        using (SKPaint paint = new SKPaint())
        {
            paint.Style = SKPaintStyle.Stroke;
            paint.Color = SKColors.Blue;
            paint.StrokeWidth = SIZE / 8;

            canvas.Clear();
            canvas.Translate(SIZE / 2, SIZE / 2);
            canvas.RotateDegrees(angle);
            canvas.DrawLine(-SIZE, -SIZE, SIZE, SIZE, paint);
            canvas.DrawLine(-SIZE, SIZE, SIZE, -SIZE, paint);
        }
    }
    ···
}
```

中出现的动画开销`OnAppearing`和`OnDisappearing`重写。 `OnTimerTick`方法进行动画处理`angle`值从 0 度到 360 度每隔 10 秒旋转位图中的 X 图：

```csharp
public class AnimatedBitmapTilePage : ContentPage
{
    ···
    // For animation
    bool isAnimating;
    Stopwatch stopwatch = new Stopwatch();
    ···

    protected override void OnAppearing()
    {
        base.OnAppearing();

        isAnimating = true;
        stopwatch.Start();
        Device.StartTimer(TimeSpan.FromMilliseconds(16), OnTimerTick);
    }

    protected override void OnDisappearing()
    {
        base.OnDisappearing();

        stopwatch.Stop();
        isAnimating = false;
    }

    bool OnTimerTick()
    {
        const int duration = 10;     // seconds
        angle = (float)(360f * (stopwatch.Elapsed.TotalSeconds % duration) / duration);
        DrawBitmap();
        canvasView.InvalidateSurface();

        return isAnimating;
    }
    ···
}
```

由于 X 图的对称性，这等同于旋转`angle`从 0 度到 90 度每隔 2.5 秒值。

`PaintSurface`处理程序从位图创建着色器，并使用画图对象来颜色整个画布：

```csharp
public class AnimatedBitmapTilePage : ContentPage
{
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPaint paint = new SKPaint())
        {
            paint.Shader = SKShader.CreateBitmap(bitmap,
                                                 SKShaderTileMode.Mirror,
                                                 SKShaderTileMode.Mirror);
            canvas.DrawRect(info.Rect, paint);
        }
    }
}
```

`SKShaderTileMode.Mirror`选项确保每个位图中的 X 武器加入与相邻的位图中的 X 创建看起来很多总体动画的模式变得更加复杂，不是简单的动画会建议：

[![经过动画处理的位图磁贴](bitmap-tiling-images/AnimatedBitmapTile.png "经过动画处理的位图磁贴")](bitmap-tiling-images/AnimatedBitmapTile-Large.png#lightbox)

## <a name="related-links"></a>相关链接

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
- [CatClock （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/catclock)
