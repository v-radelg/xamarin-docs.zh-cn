---
title: 创建并在 SkiaSharp 位图上绘制
description: 了解如何创建 SkiaSharp 位图，然后通过创建基于这些画布绘制分析这些位图。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 79BD3266-D457-4E50-BDDF-33450035FA0F
author: davidbritch
ms.author: dabritch
ms.date: 07/17/2018
ms.openlocfilehash: 68d6cb1df8557b6055feb81b21ed5513592c71c4
ms.sourcegitcommit: 1e3a0d853669dcc57d5dee0894d325d40c7d8009
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2019
ms.locfileid: "70198148"
---
# <a name="creating-and-drawing-on-skiasharp-bitmaps"></a>创建并在 SkiaSharp 位图上绘制

[![下载示例](~/media/shared/download.png)下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

您已了解如何应用程序可以加载位图，从 Web、 应用程序资源和用户的照片库。 还有可能要创建新应用程序中的位图。 最简单的方法涉及到的构造函数之一[ `SKBitmap` ](xref:SkiaSharp.SKBitmap.%23ctor(System.Int32,System.Int32,System.Boolean)):

```csharp
SKBitmap bitmap = new SKBitmap(width, height);
```

`width`和`height`参数都是整数，指定位图的像素尺寸。 此构造函数创建一个全彩色位图使用每像素 4 个字节： 1 个字节每个为红色、 绿色、 蓝色和 alpha （透明度） 组件。 

创建新的位图后，需要获取一些位图的图面上。 您通常做这两种方法之一：

- 使用标准的位图上绘制`Canvas`绘图方法。
- 直接访问像素位。

本文演示了第一种方法：

![绘制示例](drawing-images/DrawingSample.png "绘图示例")

第二种方法在文章中讨论[**访问 SkiaSharp 位图像素**](pixel-bits.md)。

## <a name="drawing-on-the-bitmap"></a>在此位图上绘图

在的表面上绘制是位图的绘制视频显示器上相同。 若要绘制视频显示器上，你可以获取`SKCanvas`对象从`PaintSurface`事件参数。 若要绘制位图上，您创建`SKCanvas`对象使用[ `SKCanvas` ](xref:SkiaSharp.SKCanvas.%23ctor(SkiaSharp.SKBitmap))构造函数：

```csharp
SKCanvas canvas = new SKCanvas(bitmap);
```

完成绘制位图上后，您可以释放`SKCanvas`对象。 出于此原因，`SKCanvas`构造函数通常称为`using`语句：

```csharp
using (SKCanvas canvas = new SKCanvas(bitmap))
{
    ··· // call drawing function
}
```

然后可以显示位图。 在更高版本时，该程序可以创建一个新`SKCanvas`相同位图，并在其上绘制的更多基于对象。

**Hello 位图**页面 **[SkiaSharpFormsDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)** 应用程序写入的文本"Hello，位图 ！" 位图和显示的位图多次。  

构造函数`HelloBitmapPage`首先创建`SKPaint`用于显示文本的对象。 它会确定文本字符串的维度并使用这些维度创建一个位图。 然后，创建`SKCanvas`对象，基于该位图，调用`Clear`，然后调用`DrawText`。 它始终是一个好办法调用`Clear`与新位图因为新创建的位图可能包含随机数据。

构造函数创建最后`SKCanvasView`对象来显示位图：

```csharp
public partial class HelloBitmapPage : ContentPage
{
    const string TEXT = "Hello, Bitmap!";
    SKBitmap helloBitmap;

    public HelloBitmapPage()
    {
        Title = TEXT;

        // Create bitmap and draw on it
        using (SKPaint textPaint = new SKPaint { TextSize = 48 })
        {
            SKRect bounds = new SKRect();
            textPaint.MeasureText(TEXT, ref bounds);

            helloBitmap = new SKBitmap((int)bounds.Right,
                                       (int)bounds.Height);

            using (SKCanvas bitmapCanvas = new SKCanvas(helloBitmap))
            {
                bitmapCanvas.Clear();
                bitmapCanvas.DrawText(TEXT, 0, -bounds.Top, textPaint);
            }
        }

        // Create SKCanvasView to view result 
        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear(SKColors.Aqua);

        for (float y = 0; y < info.Height; y += helloBitmap.Height)
            for (float x = 0; x < info.Width; x += helloBitmap.Width)
            {
                canvas.DrawBitmap(helloBitmap, x, y);
            }
    }
}
```

`PaintSurface`处理程序呈现多个时间行和列中显示的位图。 请注意，`Clear`中的方法`PaintSurface`处理程序的自变量`SKColors.Aqua`，显示器表面的背景色哪种颜色：

[![Hello，位图 ！](drawing-images/HelloBitmap.png "Hello，位图 ！")](drawing-images/HelloBitmap-Large.png#lightbox)

浅绿色背景的外观显示位图为透明除文本之外。

## <a name="clearing-and-transparency"></a>清除和透明度

显示**Hello 位图**页演示位图创建的应用程序是透明的黑色文本除外。 这就是为什么显示图面水绿色显露出来。

以下文档`Clear` `SKCanvas`介绍了这些方法:"替换画布的当前剪辑中的所有像素。 使用 "替换" 一词会显示这些方法的重要特征:的`SKCanvas`所有绘制方法都将内容添加到现有的显示图面。 `Clear`方法_替换为_已有。

`Clear` 存在两个不同的版本： 

- [ `Clear` ](xref:SkiaSharp.SKCanvas.Clear(SkiaSharp.SKColor))方法替换`SKColor`参数替换该颜色的像素为单位为像素的显示图面。

- [ `Clear` ](xref:SkiaSharp.SKCanvas.Clear)不带任何参数的方法将替换与像素[ `SKColors.Empty` ](xref:SkiaSharp.SKColors.Empty)颜色，这是一种颜色的所有组件 （红色、 绿色、 蓝色和 alpha） 设置为零。 此颜色有时称为"透明的黑色。"

调用`Clear`新位图上没有自变量初始化整个位图是完全透明。 随后在位图上绘制的任何内容通常是不透明或半透明。

下面是要尝试的操作:在 " **Hello 位图**" 页上, `Clear`将应用于的`bitmapCanvas`方法替换为以下方法:

```csharp
bitmapCanvas.Clear(new SKColor(255, 0, 0, 128));
```

顺序`SKColor`构造函数参数是红色、 绿色、 蓝色和 alpha，其中每个值的范围可以介于 0 到 255 之间。 请记住 alpha 值为 0 是透明的而 255 的 alpha 值是不透明。

值 （255，0，0，128） 清除为 50%透明度的红色像素的位图像素。 这意味着位图背景半透明。 位图的半透明的红色背景与浅绿色背景的显示图面，若要创建灰色背景相结合。

尝试设置为透明的黑色文本的颜色，通过将置于以下赋值`SKPaint`初始值设定项：

```csharp
Color = new SKColor(0, 0, 0, 0)
```

您可能认为此透明文本会创建完全透明区域通过其还会显示图面浅绿色背景的位图。 但是，它是不一样。 什么是已在此位图上之上绘制文本。 透明文本将不会显示根本。

不`Draw`方法不断将位图更加透明。 仅`Clear`可以执行该操作。

## <a name="bitmap-color-types"></a>位图颜色类型

最简单`SKBitmap`构造函数允许您指定整数像素宽度和高度的位图。 其他`SKBitmap`构造函数是更复杂。 这些构造函数需要两个枚举类型的参数： [ `SKColorType` ](xref:SkiaSharp.SKColorType)并[ `SKAlphaType` ](xref:SkiaSharp.SKAlphaType)。 其他构造函数使用[ `SKImageInfo` ](xref:SkiaSharp.SKImageInfo)结构，它将这些信息合并。

`SKColorType`枚举具有 9 成员。 这些成员的每个描述了特定存储位图像素的方式：

- `Unknown`
- `Alpha8` &mdash; 每个像素都是 8 位，表示从完全透明到完全不透明的 alpha 值
- `Rgb565` &mdash; 每个像素都是 16 位，5 位为红色和蓝色和绿色的 6
- `Argb4444` &mdash; 每个像素都是 16 位，每个 4 的 alpha、 红色、 绿色和蓝色
- `Rgba8888` &mdash; 每个像素都是 32 位，8 每个表示红色、 绿色、 蓝色和 alpha
- `Bgra8888` &mdash; 每个像素都是 32 位，8 每个表示蓝色、 绿色、 红色和 alpha
- `Index8` &mdash; 每个像素为 8 位，并且表示的索引 [`SKColorTable`](xref:SkiaSharp.SKColorTable)
- `Gray8` &mdash; 每个像素都是表示从黑色到白色的灰影的 8 位
- `RgbaF16` &mdash; 每个像素都是 64 位，使用红、 绿、 蓝色和 alpha 以 16 位浮点格式

其中每个像素都是 32 像素 （4 字节） 的两个格式通常被称为_全彩色_格式。 从视频时显示自己的时间的其他格式日期的许多都不能够完整的颜色。 限制颜色位图比较适合于这些显示并允许位图，以占用内存较少的空间。 

如今，编程人员几乎总是使用全彩色位图并不去费神具有其他格式。 例外情况是`RgbaF16`允许更大的颜色分辨率比甚至完整颜色格式的格式。 但是，此格式用于特殊的用途，如医疗图像处理，并且没有太大意义与标准全彩色显示一起使用时。

本系列的文章将限制到本身`SKBitmap`默认情况下，如果未使用的格式的颜色`SKColorType`指定成员。 此默认格式取决于基础平台。 Xamarin.Forms 支持的平台，为默认颜色类型是：

- `Rgba8888` 适用于 iOS 和 Android
- `Bgra8888` 适用于 UWP

唯一的区别是在内存中，4 个字节的顺序，这仅将成为问题时直接访问像素位。 这不会变得重要直到你转到文章[**访问 SkiaSharp 位图像素**](pixel-bits.md)。

`SKAlphaType`枚举具有四个成员：

- `Unknown`
- `Opaque` &mdash; 位图有没有透明度
- `Premul` &mdash; 颜色组件预先乘以 alpha 组件
- `Unpremul` &mdash; 颜色组件不预先乘以 alpha 组件

下面是具有 50%透明度，并使用所示顺序红色、 绿色、 蓝色、 alpha 的字节数的 4 字节红色位图像素：

0xFF 0x00 0x00 0x80

在显示图面上呈现包含半透明的像素的位图时，每个位图像素的颜色组件必须乘以该像素的 alpha 值，并必须乘以显示器表面的相应像素的颜色组件通过减去 alpha 值 255。 然后可以组合两个像素。 位图时可以呈现速度更快的位图像素中的颜色组件已被预 mulitplied 的 alpha 值。 预乘的格式，将此类存储该相同的红色像素：

0x80 0x00 0x00 0x80

此性能改进就是为什么`SkiaSharp`默认情况下的位图创建的`Premul`格式。 但同样，就必须知道这仅当访问和操作像素位。

## <a name="drawing-on-existing-bitmaps"></a>在现有的位图上绘制

不需要创建新的位图来绘制它。 此外可以绘制现有位图。 

**Monkey 髭**页上使用其构造函数来加载**MonkeyFace.png**映像。 然后，创建`SKCanvas`对象基于该位图，并使用`SKPaint`和`SKPath`要在其上绘制髭对象：

```csharp
public partial class MonkeyMoustachePage : ContentPage
{
    SKBitmap monkeyBitmap;

    public MonkeyMoustachePage()
    {
        Title = "Monkey Moustache";

        monkeyBitmap = BitmapExtensions.LoadBitmapResource(GetType(),
            "SkiaSharpFormsDemos.Media.MonkeyFace.png");

        // Create canvas based on bitmap
        using (SKCanvas canvas = new SKCanvas(monkeyBitmap))
        {
            using (SKPaint paint = new SKPaint())
            {
                paint.Style = SKPaintStyle.Stroke;
                paint.Color = SKColors.Black;
                paint.StrokeWidth = 24;
                paint.StrokeCap = SKStrokeCap.Round;

                using (SKPath path = new SKPath())
                {
                    path.MoveTo(380, 390);
                    path.CubicTo(560, 390, 560, 280, 500, 280);

                    path.MoveTo(320, 390);
                    path.CubicTo(140, 390, 140, 280, 200, 280);

                    canvas.DrawPath(path, paint);
                }
            }
        }

        // Create SKCanvasView to view result 
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
        canvas.DrawBitmap(monkeyBitmap, info.Rect, BitmapStretch.Uniform);
    }
}
```

构造函数创建最后`SKCanvasView`其`PaintSurface`处理程序只需显示结果：

[![调整髭](drawing-images/MonkeyMoustache.png "调整髭")](drawing-images/MonkeyMoustache-Large.png#lightbox)

## <a name="copying-and-modifying-bitmaps"></a>复制并修改位图

方法`SKCanvas`可用于上绘制位图包括`DrawBitmap`。 这意味着，您可以绘制一个位图另一个，通常以某种方式对其进行修改。

通过访问实际像素位是最通用的方法来修改位图，主题的文章中介绍 **[访问 SkiaSharp 位图像素](pixel-bits.md)** 。 但有许多其他方法来修改不需要访问像素位的位图。

中包含的以下位图 **[SkiaSharpFormsDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)** 应用程序为 360 像素宽，高度 480 像素：

![山地车的攀岩运动员](drawing-images/MountainClimbers.jpg "Mountain 攀岩运动员")

假设从左侧 monkey，若要发布此照片，没有接收到的权限。 一种解决方案是掩盖使用一种称为技术的 monkey 的人脸_pixelization_。 因此不能进行功能所面临的像素为单位将替换块的颜色。 颜色的块通常派生自原始图像通过平均对应于这些块的像素的颜色。 但无需执行此平均计算自己。 它自动发生时将位图复制到较小的像素尺寸。 

左的 monkey 的人脸占用大约 72 像素正方形区域使用点 （112，238） 在左上角。 让我们使用其中每个为 8-8 像素正方形的彩色砖块 9 的 9 数组来替代该 72 像素的方形区域。

**Pixelize 映像**该位图中加载页面，首先创建名为一个小 9 像素正方形位图`faceBitmap`。 这是复制的目标只是 monkey 的人脸。 目标矩形面积只是 9 像素数，但源矩形是 72 像素正方形。 源像素为单位的每个 8-8 块会求平均值颜色合并到一个像素。

下一步是将原始位图复制到名为大小相同的新的位图`pixelizedBitmap`。 微小`faceBitmap`72 像素正方形目标矩形顶部的并复制，以便每个像素的`faceBitmap`扩展到 8 次其尺寸：

```csharp
public class PixelizedImagePage : ContentPage
{
    SKBitmap pixelizedBitmap;

    public PixelizedImagePage ()
    {
        Title = "Pixelize Image";

        SKBitmap originalBitmap = BitmapExtensions.LoadBitmapResource(GetType(),
            "SkiaSharpFormsDemos.Media.MountainClimbers.jpg");

        // Create tiny bitmap for pixelized face
        SKBitmap faceBitmap = new SKBitmap(9, 9);

        // Copy subset of original bitmap to that
        using (SKCanvas canvas = new SKCanvas(faceBitmap))
        {
            canvas.Clear();
            canvas.DrawBitmap(originalBitmap,
                              new SKRect(112, 238, 184, 310),   // source
                              new SKRect(0, 0, 9, 9));          // destination

        }

        // Create full-sized bitmap for copy
        pixelizedBitmap = new SKBitmap(originalBitmap.Width, originalBitmap.Height);

        using (SKCanvas canvas = new SKCanvas(pixelizedBitmap))
        {
            canvas.Clear();

            // Draw original in full size
            canvas.DrawBitmap(originalBitmap, new SKPoint());

            // Draw tiny bitmap to cover face
            canvas.DrawBitmap(faceBitmap, 
                              new SKRect(112, 238, 184, 310));  // destination
        }

        // Create SKCanvasView to view result
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
        canvas.DrawBitmap(pixelizedBitmap, info.Rect, BitmapStretch.Uniform);
    }
}
```

构造函数创建最后`SKCanvasView`来显示结果：

[![Pixelize 图像](drawing-images/PixelizeImage.png "Pixelize 图像")](drawing-images/PixelizeImage-Large.png#lightbox)

<a name="rotating-bitmaps" />

## <a name="rotating-bitmaps"></a>旋转位图

另一项常见任务旋转位图。 从 iPhone 或 iPad 照片库中检索的位图时，这是特别有用。 除非设备已保留在特定方向中拍摄照片时，很可能是散乱的或横向图片。

启用颠倒的位图要求创建与第一个大小相同的另一个位图，并设置一个转换，以便同时将第一个复制到第二个旋转 180 度。 在本部分中的示例的所有`bitmap`是`SKBitmap`需要旋转的对象：

```csharp
SKBitmap rotatedBitmap = new SKBitmap(bitmap.Width, bitmap.Height);

using (SKCanvas canvas = new SKCanvas(rotatedBitmap))
{
    canvas.Clear();
    canvas.RotateDegrees(180, bitmap.Width / 2, bitmap.Height / 2);
    canvas.DrawBitmap(bitmap, new SKPoint());
}
```

旋转 90 度，需要创建不同大小的原始交换的高度和宽度的位图。 例如，如果原始位图 1200年像素宽和高 800 像素，宽 800 像素和 1200年像素宽是旋转的位图。 设置平移和旋转，以便围绕其左上角旋转，然后移到视图位图。 (请记住`Translate`和`RotateDegrees`应用它们的方式相反顺序调用方法。)下面是旋转 90 度顺时针旋转的代码：

```csharp
SKBitmap rotatedBitmap = new SKBitmap(bitmap.Height, bitmap.Width);

using (SKCanvas canvas = new SKCanvas(rotatedBitmap))
{
    canvas.Clear();
    canvas.Translate(bitmap.Height, 0);
    canvas.RotateDegrees(90);
    canvas.DrawBitmap(bitmap, new SKPoint());
}
```                        

而此处是旋转逆时针旋转 90 度的类似函数：

```csharp
SKBitmap rotatedBitmap = new SKBitmap(bitmap.Height, bitmap.Width);

using (SKCanvas canvas = new SKCanvas(rotatedBitmap))
{
    canvas.Clear();
    canvas.Translate(0, bitmap.Width);
    canvas.RotateDegrees(-90);
    canvas.DrawBitmap(bitmap, new SKPoint());
}
```

这两种方法中使用**照片拼图**文章中所述的页面[**裁剪 SkiaSharp 位图**](cropping.md#tile-division)。

使用户可以旋转 90 度的增量中的位图的程序只需要实现一个功能的旋转 90 度。 然后，用户可以旋转 90 度任何增量通过重复执行此函数。

程序还可以在任意数量的旋转位图。 一个简单的方法是修改 180 度旋转 180 替换的通用函数`angle`变量：

```csharp
SKBitmap rotatedBitmap = new SKBitmap(bitmap.Width, bitmap.Height);

using (SKCanvas canvas = new SKCanvas(rotatedBitmap))
{
    canvas.Clear();
    canvas.RotateDegrees(angle, bitmap.Width / 2, bitmap.Height / 2);
    canvas.DrawBitmap(bitmap, new SKPoint());
}
```

但是，一般情况下，此逻辑将旋转位图的边角裁剪。 更好的方法是计算使用三角函数要包含这些边角旋转位图的大小。 

中显示此三角函数**位图轮显**页。 XAML 文件实例化`SKCanvasView`和一个`Slider`的范围可以介于 0 到 360 度与`Label`显示当前值：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Bitmaps.BitmapRotatorPage"
             Title="Bitmap Rotator">
    <StackLayout>
        <skia:SKCanvasView x:Name="canvasView"
                           VerticalOptions="FillAndExpand"
                           PaintSurface="OnCanvasViewPaintSurface" />

        <Slider x:Name="slider"
                Maximum="360"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="{Binding Source={x:Reference slider},
                              Path=Value,
                              StringFormat='Rotate by {0:F0}&#x00B0;'}"
               HorizontalTextAlignment="Center" />

    </StackLayout>
</ContentPage>
```

代码隐藏文件加载位图资源，并将其保存为静态的只读字段，将名为`originalBitmap`。 中显示的位图`PaintSurface`处理程序是`rotatedBitmap`，它最初设置为`originalBitmap`:

```csharp
public partial class BitmapRotatorPage : ContentPage
{
    static readonly SKBitmap originalBitmap = 
        BitmapExtensions.LoadBitmapResource(typeof(BitmapRotatorPage),
            "SkiaSharpFormsDemos.Media.Banana.jpg");

    SKBitmap rotatedBitmap = originalBitmap;

    public BitmapRotatorPage ()
    {
        InitializeComponent ();
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();
        canvas.DrawBitmap(rotatedBitmap, info.Rect, BitmapStretch.Uniform);
    }

    void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
    {
        double angle = args.NewValue;
        double radians = Math.PI * angle / 180;
        float sine = (float)Math.Abs(Math.Sin(radians));
        float cosine = (float)Math.Abs(Math.Cos(radians));
        int originalWidth = originalBitmap.Width;
        int originalHeight = originalBitmap.Height;
        int rotatedWidth = (int)(cosine * originalWidth + sine * originalHeight);
        int rotatedHeight = (int)(cosine * originalHeight + sine * originalWidth);

        rotatedBitmap = new SKBitmap(rotatedWidth, rotatedHeight);

        using (SKCanvas canvas = new SKCanvas(rotatedBitmap))
        {
            canvas.Clear(SKColors.LightPink);
            canvas.Translate(rotatedWidth / 2, rotatedHeight / 2);
            canvas.RotateDegrees((float)angle);
            canvas.Translate(-originalWidth / 2, -originalHeight / 2);
            canvas.DrawBitmap(originalBitmap, new SKPoint());
        }

        canvasView.InvalidateSurface();
    }
}
```

`ValueChanged`处理程序`Slider`执行的操作创建一个新的`rotatedBitmap`基于的旋转角度。 新的宽度和高度基于 absolute values 正弦的标识和余弦的原始宽度和高度。 用于绘制旋转位图上的原始位图转换原始位图中心移到原点，然后将其旋转的度数 （） 指定的数字，然后转换该中心到旋转位图的中心。 (`Translate`和`RotateDegrees`不是如何应用以相反顺序调用方法。)

请注意，使用`Clear`方法来执行的后台`rotatedBitmap`浅粉色。 这只是为了说明的大小`rotatedBitmap`上显示：

[![位图轮显](drawing-images/BitmapRotator.png "位图轮显")](drawing-images/BitmapRotator-Large.png#lightbox)

旋转的位图很大足以包含整个原始位图，但不是大。

## <a name="flipping-bitmaps"></a>翻转位图

调用另一个常见用途上位图_翻转_。 从概念上讲，在三个维度围绕垂直轴或通过位图的中心水平轴旋转位图。 垂直翻转创建镜像映像。

**位图挡板** 页面 **[SkiaSharpFormsDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)** 应用程序演示了这些过程。 XAML 文件包含`SKCanvasView`和用于翻转水平和垂直方向的两个按钮：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Bitmaps.BitmapFlipperPage"
             Title="Bitmap Flipper">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="*" />
            <RowDefinition Height="Auto" />
        </Grid.RowDefinitions>

        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="*" />
            <ColumnDefinition Width="*" />
        </Grid.ColumnDefinitions>
        
        <skia:SKCanvasView x:Name="canvasView"
                           Grid.Row="0" Grid.Column="0" Grid.ColumnSpan="2"
                           PaintSurface="OnCanvasViewPaintSurface" />

        <Button Text="Flip Vertical"
                Grid.Row="1" Grid.Column="0"
                Margin="0, 10"
                Clicked="OnFlipVerticalClicked" />

        <Button Text="Flip Horizontal"
                Grid.Row="1" Grid.Column="1"
                Margin="0, 10"
                Clicked="OnFlipHorizontalClicked" />
    </Grid>
</ContentPage>
```

代码隐藏文件实现在这两个操作`Clicked`按钮处理程序： 

```csharp
public partial class BitmapFlipperPage : ContentPage
{
    SKBitmap bitmap =
        BitmapExtensions.LoadBitmapResource(typeof(BitmapRotatorPage),
            "SkiaSharpFormsDemos.Media.SeatedMonkey.jpg");

    public BitmapFlipperPage()
    {
        InitializeComponent();
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();
        canvas.DrawBitmap(bitmap, info.Rect, BitmapStretch.Uniform);
    }

    void OnFlipVerticalClicked(object sender, ValueChangedEventArgs args)
    {
        SKBitmap flippedBitmap = new SKBitmap(bitmap.Width, bitmap.Height);

        using (SKCanvas canvas = new SKCanvas(flippedBitmap))
        {
            canvas.Clear();
            canvas.Scale(-1, 1, bitmap.Width / 2, 0);
            canvas.DrawBitmap(bitmap, new SKPoint());
        }

        bitmap = flippedBitmap;
        canvasView.InvalidateSurface();
    }

    void OnFlipHorizontalClicked(object sender, ValueChangedEventArgs args)
    {
        SKBitmap flippedBitmap = new SKBitmap(bitmap.Width, bitmap.Height);

        using (SKCanvas canvas = new SKCanvas(flippedBitmap))
        {
            canvas.Clear();
            canvas.Scale(1, -1, 0, bitmap.Height / 2);
            canvas.DrawBitmap(bitmap, new SKPoint());
        }

        bitmap = flippedBitmap;
        canvasView.InvalidateSurface();
    }
}
```

垂直翻转通过使用的水平比例因子缩放转换&ndash;1。 缩放的中心是位图的垂直居中。 水平翻转是一种缩放转换使用的垂直比例因子&ndash;1。 

您可以看到从 monkey 的 shirt 反向时，翻转不旋转相同。 但如右侧 UWP 屏幕截图所示，水平和垂直翻转都是相同旋转 180 度：

[![位图挡板](drawing-images/BitmapFlipper.png "位图翻转器")](drawing-images/BitmapFlipper-Large.png#lightbox)

可以使用类似的技术处理的另一常见任务裁剪矩形子集的位图。 这在下一篇文章中所述[**裁剪 SkiaSharp 位图**](cropping.md)。

## <a name="related-links"></a>相关链接

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
