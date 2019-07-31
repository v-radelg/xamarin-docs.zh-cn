---
title: SkiaSharp 颜色筛选器
description: 使用颜色筛选器要转换的颜色的转换或表。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 774E7B55-AEC8-4F12-B657-1C0CEE01AD63
author: davidbritch
ms.author: dabritch
ms.date: 08/28/2018
ms.openlocfilehash: 5aa8b2e85d5a7d547af5333dcaf350025b86cc26
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2019
ms.locfileid: "68647701"
---
# <a name="skiasharp-color-filters"></a>SkiaSharp 颜色筛选器

[![下载示例](~/media/shared/download.png)下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

颜色筛选器可以将位图 （或其他图像） 中的颜色转换为其他颜色的色调分离等效果：

![颜色筛选器示例](color-filters-images/ColorFiltersExample.png "颜色筛选器示例")

若要使用的颜色筛选器，设置[ `ColorFilter` ](xref:SkiaSharp.SKPaint.ColorFilter)的属性`SKPaint`对象的类型[ `SKColorFilter` ](xref:SkiaSharp.SKColorFilter)在该类中的静态方法之一创建。 本文演示了： 

- 使用创建颜色转换[ `CreateColorMatrix` ](xref:SkiaSharp.SKColorFilter.CreateColorMatrix*)方法。
- 使用颜色表创建[ `CreateTable` ](xref:SkiaSharp.SKColorFilter.CreateTable*)方法。

## <a name="the-color-transform"></a>颜色转换

颜色转换涉及到使用矩阵来修改颜色。 与大多数 2D 图形系统中，类似 SkiaSharp 矩阵使用主要用于将坐标点转换为一文中的 iscussed [**矩阵转换中 SkiaSharp**](../transforms/matrix.md)。 [ `SKColorFilter` ](xref:SkiaSharp.SKColorFilter)还支持矩阵转换，但矩阵转换 RGB 颜色。 熟悉矩阵概念有必要了解这些颜色转换。 

颜色转换矩阵具有四个行和五个列的维度：

<pre>
| M11 M12 M13 M14 M15 |
| M21 M22 M23 M24 M25 |
| M31 M32 M33 M34 M35 |
| M41 M42 M43 M44 M45 |
</pre>

它会将 RGB 源颜色 (R、 G、 B，A) 转换为目标的颜色 (R，G，B、)。 

在矩阵乘法的准备，源颜色转换为 5 × 1 矩阵：

<pre>
| R |
| G |
| B |
| A |
| 1 |
</pre>

这些 R、 G、 B 和 A 的值为 0 到 255 之间的原始字节。 它们是_不_规范化为 0 到 1 的范围中的浮点值。

平移因子需要额外的单元格。 这是 3 × 3 矩阵转换二维坐标点的部分中所述的用法类似[ **3 x 3 矩阵的原因**](../transforms/matrix.md#the-reason-for-the-3-by-3-matrix)有关使用矩阵进行转换的文章中坐标点。

4 × 5 矩阵乘以 5 × 1 矩阵中，且该产品是转换后的颜色的 4 × 1 矩阵：

<pre>
| M11 M12 M13 M14 M15 |    | R |   | R' |
| M21 M22 M23 M24 M25 |    | G |   | G' |
| M31 M32 M33 M34 M35 |  × | B | = | B' |
| M41 M42 M43 M44 M45 |    | A |   | A' |
                           | 1 |
</pre>

以下是适用于 R 的单独公式，G，B' 和一个:

`R' = M11·R + M12·G + M13·B + M14·A + M15` 

`G' = M21·R + M22·G + M23·B + M24·A + M25` 

`B' = M31·R + M32·G + M33·B + M34·A + M35` 

`A' = M41·R + M42·G + M43·B + M44·A + M45` 

大部分矩阵包含，通常位于 0 到 2 的范围内的乘法因素。 但是，最后一列 (通过 M45 M15) 包含在公式中添加的值。 这些值通常介于 0 到 255。 结果被限制的值为 0 到 255 之间。

恒等矩阵的：

<pre>
| 1 0 0 0 0 |
| 0 1 0 0 0 |
| 0 0 1 0 0 |
| 0 0 0 1 0 |
</pre>

这会导致不改变颜色。 转换公式是：

`R' = R` 

`G' = G`

`B' = B`

`A' = A`

M44 单元格是非常重要，因为它将保留不透明度。 它通常是 M41、 M42 和 M43 是全为零，这种情况，因为您可能不希望不透明度基于红色、 绿色和蓝色值。 但是，如果 M44 为零，则的将为零，而不显示任何内容。

颜色矩阵的最常见用途之一是将彩色位图转换为灰度位图。 这涉及到的公式的红色、 绿色和蓝色值的加权平均值。 对于使用 （"标准红色绿色蓝色"） sRGB 颜色空间的视频显示器，此公式为：

灰色阴影 = 0.2126·R + 0.7152·G + 0.0722·B

若要将颜色位图转换为灰度位图，R，G，和 B 的结果必须等于相同的值。 该矩阵是：

<pre>
| 0.21 0.72 0.07 0 0 |
| 0.21 0.72 0.07 0 0 |
| 0.21 0.72 0.07 0 0 |
| 0    0    0    1 0 |
</pre>

没有 SkiaSharp 数据类型对应于此矩阵。 而是必须作为数组 20 表示矩阵`float`行顺序中的值： 第一个行，则第二个行和等。

静态[ `SKColorFilter.CreateColorMatrix` ](xref:SkiaSharp.SKColorFilter.CreateColorMatrix*)方法具有以下语法：

```csharp
public static SKColorFilter CreateColorMatrix (float[] matrix);
```

其中`matrix`是一个数组 20`float`值。 创建中的数组时C#，很容易地设置数字的格式，因此它们类似于 4 × 5 矩阵。 了这一点**灰度矩阵**页面[ **SkiaSharpFormsDemos** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)示例：

```csharp
public class GrayScaleMatrixPage : ContentPage
{
    SKBitmap bitmap = BitmapExtensions.LoadBitmapResource(
                        typeof(CenteredTilesPage),
                        "SkiaSharpFormsDemos.Media.Banana.jpg");

    public GrayScaleMatrixPage()
    {
        Title = "Gray-Scale Matrix";

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
            paint.ColorFilter =
                SKColorFilter.CreateColorMatrix(new float[]
                {
                    0.21f, 0.72f, 0.07f, 0, 0,
                    0.21f, 0.72f, 0.07f, 0, 0,
                    0.21f, 0.72f, 0.07f, 0, 0,
                    0,     0,     0,     1, 0
                });

            canvas.DrawBitmap(bitmap, info.Rect, BitmapStretch.Uniform, paint: paint);
        }
    }
}
```

`DrawBitmap`此代码中使用的方法是从**BitmapExtension.cs**文件中包含[ **SkiaSharpFormsDemos** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)示例。 

下面是 iOS、 Android 和通用 Windows 平台上运行的结果：

[![灰度矩阵](color-filters-images/GrayScaleMatrix.png "灰度矩阵")](color-filters-images/GrayScaleMatrix-Large.png#lightbox)

注意第四行、 第四列中的值。 这是乘以一个原始颜色的一个值的关键因素已转换的颜色值。 如果该单元格为零，将不会显示和问题可能很难找到。

当使用颜色矩阵进行试验，您可以从角度来看的源或目标的角度来看将转换。 源的红色像素应如何影响到目标的像素为单位： 红色、 绿色和蓝色？ 由第一个中的值_列_的矩阵。 或者，应目标红色像素受的影响的源的像素为单位： 红色、 绿色和蓝色？ 由第一个_行_的矩阵。

有关如何使用颜色转换的一些想法，请参阅[**重新着色图像**](https://docs.microsoft.com/dotnet/framework/winforms/advanced/recoloring-images)页。 讨论的焦点是 Windows 窗体和该矩阵是不同的格式，但概念是相同的。

**浅色矩阵**衰减源红色像素和略有强调红色和绿色像素为单位计算目标红色像素。 此过程同样发生绿色和蓝色像素：

```csharp
public class PastelMatrixPage : ContentPage
{
    SKBitmap bitmap = BitmapExtensions.LoadBitmapResource(
                        typeof(PastelMatrixPage),
                        "SkiaSharpFormsDemos.Media.MountainClimbers.jpg");

    public PastelMatrixPage()
    {
        Title = "Pastel Matrix";

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
            paint.ColorFilter =
                SKColorFilter.CreateColorMatrix(new float[]
                {
                    0.75f, 0.25f, 0.25f, 0, 0,
                    0.25f, 0.75f, 0.25f, 0, 0,
                    0.25f, 0.25f, 0.75f, 0, 0,
                    0, 0, 0, 1, 0
                });

            canvas.DrawBitmap(bitmap, info.Rect, BitmapStretch.Uniform, paint: paint);
        }
    }
}
```

结果是可以将颜色的亮度调节到静音，如下所示：

[![浅色矩阵](color-filters-images/PastelMatrix.png "浅色矩阵")](color-filters-images/PastelMatrix-Large.png#lightbox)

## <a name="color-tables"></a>颜色表

静态[ `SKColorFilter.CreateTable` ](xref:SkiaSharp.SKColorFilter.CreateTable*)方法有两个版本：

```csharp
public static SKColorFilter CreateTable (byte[] table);

public static SKColorFilter CreateTable (byte[] tableA, byte[] tableR, byte[] tableG, byte[] tableB);
```

数组始终包含 256 的条目。 在`CreateTable`为红色、 绿色和蓝色组件使用与一个表，在同一个表的方法。 这是一个简单的查找表:如果源颜色为 (r, G, b), 目标颜色为 (r ', B ', G '), 则通过与源组件建立索引`table`来获取目标组件:

`R' = table[R]`

`G' = table[G]`

`B' = table[B]`

在第二个方法中，每个四个颜色组件可以具有不同的颜色表，或可能在两个或多个组件之间共享相同的颜色表。

如果想要将一个参数设置为第二个`CreateTable`到序列中包含值 0 到 255 之间的颜色表的方法，可以使用`null`相反。 经常`CreateTable`调用具有`null`alpha 通道的第一个参数。

上的部分**海报**有关的文章中[访问 SkiaSharp 位图像素位](../bitmaps/pixel-bits.md#posterization)，已了解如何修改以减少其颜色分辨率的位图的各个像素位。 这是一种称为技术_海报_。 

此外可以色调分离与颜色表的位图。 构造函数**色调分离表**页创建的颜色表，将其索引映射到与底部字节设置为零的 6 位：

```csharp
public class PosterizeTablePage : ContentPage
{
    SKBitmap bitmap = BitmapExtensions.LoadBitmapResource(
                        typeof(PosterizeTablePage),
                        "SkiaSharpFormsDemos.Media.MonkeyFace.png");

    byte[] colorTable = new byte[256];

    public PosterizeTablePage()
    {
        Title = "Posterize Table";

        // Create color table
        for (int i = 0; i < 256; i++)
        {
            colorTable[i] = (byte)(0xC0 & i);
        }

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
            paint.ColorFilter =
                SKColorFilter.CreateTable(null, null, colorTable, colorTable);

            canvas.DrawBitmap(bitmap, info.Rect, BitmapStretch.Uniform, paint: paint);
        }
    }
}
```

程序选择的绿色和蓝色通道仅用于此颜色表。 红色通道继续拥有完全的解决方法：

[![色调分离表](color-filters-images/PosterizeTable.png "色调分离表")](color-filters-images/PosterizeTable-Large.png#lightbox)

各种颜色表可用于各种效果的不同的颜色通道。 

## <a name="related-links"></a>相关链接

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
