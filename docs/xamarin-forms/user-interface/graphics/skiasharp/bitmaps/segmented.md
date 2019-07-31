---
title: SkiaSharp 位图的分段的显示
description: 显示 SkiaSharp 位图，以便某些区域被拉伸并不是某些区域。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 79AE2033-C41C-4447-95A6-76D22E913D19
author: davidbritch
ms.author: dabritch
ms.date: 07/17/2018
ms.openlocfilehash: ca6c8fafe4352bac83e5ae60b43627d4c7fdc10f
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2019
ms.locfileid: "68648671"
---
# <a name="segmented-display-of-skiasharp-bitmaps"></a>SkiaSharp 位图的分段的显示

[![下载示例](~/media/shared/download.png)下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

SkiaSharp`SKCanvas`对象定义一个名为方法`DrawBitmapNinePatch`和两个方法名为`DrawBitmapLattice`非常相似。 两个这些方法呈现到目标矩形的大小的位图，但而不是均匀拉伸位图，它们在其像素大小显示位图的部分和延伸位图的其他部分，使其适应矩形：

![分段示例](segmented-images/SegmentedSample.png "分段示例")

这些方法通常用于呈现用户界面对象，如按钮的组成部分的位图。 在设计按钮时，通常您希望用于基于内容的按钮的按钮的大小，但你可能希望该按钮的边框是相同的宽度，而不考虑该按钮的内容。 这是理想的应用程序`DrawBitmapNinePatch`。

`DrawBitmapNinePatch` 是一种特殊情况的`DrawBitmapLattice`但更简单的两种方法来使用和了解。

## <a name="the-nine-patch-display"></a>包含 9 个修补程序显示 

从概念上讲， [ `DrawBitmapNinePatch` ](xref:SkiaSharp.SKCanvas.DrawBitmapNinePatch(SkiaSharp.SKBitmap,SkiaSharp.SKRectI,SkiaSharp.SKRect,SkiaSharp.SKPaint))将位图划分为九个矩形：

![九个修补程序](segmented-images/NinePatch.png "九个修补程序")

在四个角的矩形将显示在其像素大小。 箭头表示，如位图的边缘上的其他区域将被拉伸水平或垂直到目标矩形的区域。 水平和垂直拉伸中心中的矩形。 

如果要在其像素尺寸显示甚至四个角的目标矩形中没有足够的空间，然后它们可向下缩放到的可用大小和执行任何操作，但会显示四个角。

若要将位图划分为以下九个矩形，它只是用来指定在中心的矩形。 这是语法的`DrawBitmapNinePatch`方法：

```csharp
canvas.DrawBitmapNinePatch(bitmap, centerRectangle, destRectangle, paint);
```

Center 矩形是相对于位图。 它是`SKRectI`值 (整数新版`SKRect`) 和所有坐标和大小都都以像素为单位。 目标矩形是相对于显示图面。 `paint` 自变量是可选的。

**九个修补程序显示**页面[ **SkiaSharpFormsDemos** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)示例首先使用静态构造函数来创建类型的公共静态属性`SKBitmap`:

```csharp
public partial class NinePatchDisplayPage : ContentPage
{
    static NinePatchDisplayPage()
    {
        using (SKCanvas canvas = new SKCanvas(FiveByFiveBitmap))
        using (SKPaint paint = new SKPaint
        {
            Style = SKPaintStyle.Stroke,
            Color = SKColors.Red,
            StrokeWidth = 10
        })
        {
            for (int x = 50; x < 500; x += 100)
                for (int y = 50; y < 500; y += 100)
                {
                    canvas.DrawCircle(x, y, 40, paint);
                }
        }
    }

    public static SKBitmap FiveByFiveBitmap { get; } = new SKBitmap(500, 500);
    ···
}
```

在本文中的两个其他页面使用该相同的位图。 位图为 500 像素方形，并包含 25 圆圈的数组的所有相同大小，每个都占据 100 像素正方形区域：

![圈出网格](segmented-images/CircleGrid.png "圆形网格")

该程序的实例构造函数创建`SKCanvasView`与`PaintSurface`处理程序，它使用`DrawBitmapNinePatch`显示拉伸到其整个显示器表面的位图：

```csharp
public class NinePatchDisplayPage : ContentPage
{
    ···
    public NinePatchDisplayPage()
    {
        Title = "Nine-Patch Display";

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

        SKRectI centerRect = new SKRectI(100, 100, 400, 400);
        canvas.DrawBitmapNinePatch(FiveByFiveBitmap, centerRect, info.Rect);
    }
}
```

`centerRect`矩形包含 16 圆的中心数组。 角中的圆圈显示在其像素大小，并相应地拉伸的其他所有内容：

[![包含 9 个修补程序显示](segmented-images/NinePatchDisplay.png "包含 9 个修补程序显示")](segmented-images/NinePatchDisplay-Large.png#lightbox)

UWP 页恰好为 500 像素宽，并因此将顶部和底部行显示为一系列的圆的大小相同。 否则，所有不在角中的圆圈将拉伸到窗体省略号。

对于对象包含的圆角和椭圆组合的奇怪显示，请尝试定义 center 矩形，以便它与重叠圆圈的行和列：

```csharp
SKRectI centerRect = new SKRectI(150, 150, 350, 350);
```

## <a name="the-lattice-display"></a>点阵显示

这两个`DrawBitmapLattice`方法都是类似于`DrawBitmapNinePatch`，但它们通用于所有的水平或垂直分割数。 这些部门定义对应于像素为单位的整数数组的数组。 

[ `DrawBitmapLattice` ](xref:SkiaSharp.SKCanvas.DrawBitmapLattice(SkiaSharp.SKBitmap,System.Int32[],System.Int32[],SkiaSharp.SKRect,SkiaSharp.SKPaint))具有参数的整数中的这些阵列的方法似乎不起作用。 [ `DrawBitmapLattice` ](xref:SkiaSharp.SKCanvas.DrawBitmapLattice(SkiaSharp.SKBitmap,SkiaSharp.SKLattice,SkiaSharp.SKRect,SkiaSharp.SKPaint))具有类型参数的方法`SKLattice`的工作原理，这是在如下所示的示例中使用。

[ `SKLattice` ](xref:SkiaSharp.SKLattice)结构定义四个属性：

- [`XDivs`](xref:SkiaSharp.SKLattice.XDivs)一个整数数组
- [`YDivs`](xref:SkiaSharp.SKLattice.YDivs)一个整数数组
- [`Flags`](xref:SkiaSharp.SKLattice.Flags)一个数组`SKLatticeFlags`，枚举类型
- [`Bounds`](xref:SkiaSharp.SKLattice.Bounds) 类型的`Nullable<SKRectI>`指定位图中的一个可选源矩形

`XDivs`数组分成垂直条带位图的宽度。 第一条从像素延伸 0 左侧到`XDivs[0]`。 此带呈现其像素宽度。 从第二个条带延伸`XDivs[0]`到`XDivs[1]`，并且拉伸。 从第三个条带延伸`XDivs[1]`到`XDivs[2]`和呈现其像素宽度。 最后一条从数组的最后一个元素扩展到位图的右边缘。 如果数组具有偶数数目的元素，则它会显示在其像素宽度。 否则，它的拉伸。 垂直条带线的总数是一个数组中的元素数。

`YDivs`数组是类似。 它会将该数组的高度划分为水平条带中。

共同`XDivs`和`YDivs`数组将位图划分为矩形。 矩形的数量等于水平条带线的数目和垂直条带线的数目的乘积。

根据 Skia 文档`Flags`数组包含一个元素的每个矩形，第一次的顶行是矩形，然后第二行等。 `Flags`数组类型是[ `SKLatticeFlags` ](xref:SkiaSharp.SKLatticeFlags)，具有以下成员的枚举：

- `Default` 使用值为 0
- `Transparent` 值为 1

但是，这些标志看起来不够，无法发挥作用，他们应该，和最好是将其忽略。 未设置，但`Flags`属性设置为`null`。 将其设置为一个数组`SKLatticeFlags`值大小足以包含矩形的总数。

**点阵九个修补**页上使用`DrawBitmapLattice`模拟`DrawBitmapNinePatch`。 它使用相同的位图中创建`NinePatchDisplayPage`:

```csharp
public class LatticeNinePatchPage : ContentPage
{
    SKBitmap bitmap = NinePatchDisplayPage.FiveByFiveBitmap;

    public LatticeNinePatchPage ()
    {
        Title = "Lattice Nine-Patch";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }
    `
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        SKLattice lattice = new SKLattice();
        lattice.XDivs = new int[] { 100, 400 };
        lattice.YDivs = new int[] { 100, 400 };
        lattice.Flags = new SKLatticeFlags[9]; 

        canvas.DrawBitmapLattice(bitmap, lattice, info.Rect);
    }
}
```

同时`XDivs`和`YDivs`属性设置为仅使用两个整数，将位图划分为三个条，水平和垂直两个数组的数组： 从像素 0 到 100 （呈现的像素大小） 像素从像素 100 到 400 像素 （拉伸） 和从 400 像素到像素 500 （像素大小）。 共同`XDivs`并`YDivs`定义的大小是 9 矩形总计的`Flags`数组。 只需创建该数组就足以创建的数组`SKLatticeFlags.Default`值。

显示等同于上一个程序：

[![包含 9 个修补程序点阵](segmented-images/LatticeNinePatch.png "点阵包含 9 个修补程序")](segmented-images/LatticeNinePatch-Large.png#lightbox)

**点阵显示**页将位图划分为 16 矩形：

```csharp
public class LatticeDisplayPage : ContentPage
{
    SKBitmap bitmap = NinePatchDisplayPage.FiveByFiveBitmap;

    public LatticeDisplayPage()
    {
        Title = "Lattice Display";

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

        SKLattice lattice = new SKLattice();
        lattice.XDivs = new int[] { 100, 200, 400 };
        lattice.YDivs = new int[] { 100, 300, 400 };

        int count = (lattice.XDivs.Length + 1) * (lattice.YDivs.Length + 1);
        lattice.Flags = new SKLatticeFlags[count];

        canvas.DrawBitmapLattice(bitmap, lattice, info.Rect);
    }
}
```

`XDivs`和`YDivs`数组是略有不同，从而导致显示不被完全与前面的示例为对称：

[![显示点阵](segmented-images/LatticeDisplay.png "点阵显示")](segmented-images/LatticeDisplay-Large.png#lightbox)

在 iOS 和 Android 映像在左侧，仅的较小圆圈来呈现其像素大小。 其他所有内容的拉伸。

**点阵显示**页可创建通用化`Flags`数组，从而可以试验`XDivs`和`YDivs`更轻松地。 具体而言，你将想要看到设置的第一个元素时，会发生什么情况`XDivs`或`YDivs`为 0 的数组。 

## <a name="related-links"></a>相关链接

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
