---
title: Porter Duff 的混合模式
description: 使用 Porter Duff 的混合模式来编写基于源和目标图像的场景。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 57F172F8-BA03-43EC-A215-ED6B78696BB5
author: davidbritch
ms.author: dabritch
ms.date: 08/23/2018
ms.openlocfilehash: f988c16e933214b3ce737febb89d05a451eb2f14
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61205438"
---
# <a name="porter-duff-blend-modes"></a>Porter Duff 的混合模式

[![下载示例](~/media/shared/download.png)下载示例](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)

Thomas Porter 和 Tom Duff，他们负责开发组合的情况下在 Lucasfilm 代数后命名 Porter Duff 的混合模式。 其纸张[_组合的情况下数字图像_](https://graphics.pixar.com/library/Compositing/paper.pdf)在 1984 年 7 月发行的已发布_计算机图形中_，页 253 向 259。 这些混合模式是组合的情况下，这组合到复合场景的各种映像的基础：

![Porter Duff 示例](porter-duff-images/PorterDuffSample.png "Porter Duff 示例")

## <a name="porter-duff-concepts"></a>Porter Duff 概念

假设 brownish 矩形所占的显示图面左侧和顶部 2 / 3:

![Porter Duff 目标](porter-duff-images/PorterDuffDst.png "Porter Duff 目标")

此区域称为_目标_有时_背景_或_背景_。

你想要绘制以下矩形的大小相同的目标。 该矩形是透明占据右侧和底部的三分之二的色调蓝色区域除外：

![Porter Duff 源](porter-duff-images/PorterDuffSrc.png "Porter Duff 源")

这称为_源_有时_前台_。

当在目标上显示的源代码时，下面是你期望的内容：

![Porter Duff 源覆盖](porter-duff-images/PorterDuffSrcOver.png "Porter Duff 源覆盖")

源的透明像素允许背景以显示通过，而略带蓝色源像素会掩盖背景。 这是正常情况下，并在为 SkiaSharp 中称为`SKBlendMode.SrcOver`。 值是默认设置的`BlendMode`属性时`SKPaint`对象首次实例化。

但是，就可以以指定不同的效果的不同的混合模式。 如果指定`SKBlendMode.DstOver`，则在源和目标的相交的区域中，会出现而不是源的目标：

![通过 Porter Duff 目标](porter-duff-images/PorterDuffDstOver.png "通过 Porter Duff 目标")

`SKBlendMode.DstIn`混合模式下显示的目标和源相交使用目标颜色区域：

![中的目标，Porter Duff](porter-duff-images/PorterDuffDstIn.png "Porter Duff 目标中")

混合模式的`SKBlendMode.Xor`(独占 OR) 导致没有要显示的两个方面与发生重叠的内容：

![Porter Duff 排他或者](porter-duff-images/PorterDuffXor.png "Porter Duff 异或")

彩色的目标和源矩形有效地将显示图面划分为四个可以以各种方式对应的目标和源矩形存在的唯一区域：

![Porter Duff](porter-duff-images/PorterDuff.png "Porter Duff")

右上方和左下角矩形是始终为空，因为在这些领域中的目标和源是透明。 目标颜色占用窗口左上区域中，以便区域或者与目标颜色或根本不是颜色。 同样，源颜色占用右下角区域中，以便与源颜色或根本不是，可以设置颜色区域。 可以与目标颜色，源颜色，或者根本未着色目标和源中间的交集。

组合的总数乘以时间 （适用于中心），3 或 12 2 （对于右下角） 是 2 （对于左上角）。 这些是 12 基本 Porter Duff 组合模式。

末尾的_组合的情况下数字图像_（页面 256） Porter 和 Duff 添加名为 13 模式_plus_ (对应于 SkiaSharp`SKBlendMode.Plus`成员和 W3C_较浅_模式 (这并不是与 W3C 混淆_变淡_模式。)这`Plus`模式添加目标和源颜色将稍后更详细地介绍的过程。

Skia 添加 14 模式称为`Modulate`非常类似于`Plus`不同之处在于目标和源颜色相乘。 它可将其视为其他 Porter Duff 混合模式。

以下是 14 Porter Duff 模式 SkiaSharp 中定义。 下表显示如何颜色上图中的三个非空白区域的每个：

| 模式       | 目标 | 交集 | 源 |
| ---------- |:-----------:|:------------:|:------:|
| `Clear`    |             |              |        |
| `Src`      |             | 源       | X      |
| `Dst`      | X           | 目标  |        |
| `SrcOver`  | X           | 源       | X      |
| `DstOver`  | X           | 目标  | X      |
| `SrcIn`    |             | 源       |        |
| `DstIn`    |             | 目标  |        |
| `SrcOut`   |             |              | X      |
| `DstOut`   | X           |              |        |
| `SrcATop`  | X           | 源       |        |
| `DstATop`  |             | 目标  | X      |
| `Xor`      | X           |              | X      |
| `Plus`     | X           | Sum          | X      |
| `Modulate` |             | 产品      |        | 

这些混合模式是对称的。 可交换的源和目标，且所有的模式仍可用。

命名约定的模式遵循几个简单规则：

- **Src**或**Dst**本身意味着只有源或目标像素是否可见。
- **转移**后缀指示了交集中可见的内容。 "结束"的其他绘制的源或目标。
- **在**后缀意味着仅交集进行着色。 输出被限制为只有在源或目标可以是"in"的其他部分。
- **出**后缀意味着交集不带颜色。 输出只是交集的一部分的源或目标可以是交集的"out"。
- **之上**后缀是联合**中**并**出**。它的其他包括其中的源或目标是"在"区域。

请注意的不同之处`Plus`和`Modulate`模式。 这些模式对源和目标像素执行不同类型的计算。 他们稍后所述更多详细信息。

**Porter Duff 网格**页在网格的窗体中的一个屏幕上显示所有 14 模式。 每种模式是一个单独的实例`SKCanvasView`。 因此，一个类派生自`SKCanvasView`名为`PorterDuffCanvasView`。 静态构造函数创建另一个使用其左上角区域中的 brownish 矩形，另一个略带蓝色矩形具有相同大小的两个位图：

```csharp
class PorterDuffCanvasView : SKCanvasView
{
    static SKBitmap srcBitmap, dstBitmap;

    static PorterDuffCanvasView()
    {
        dstBitmap = new SKBitmap(300, 300);
        srcBitmap = new SKBitmap(300, 300);

        using (SKPaint paint = new SKPaint())
        {
            using (SKCanvas canvas = new SKCanvas(dstBitmap))
            {
                canvas.Clear();
                paint.Color = new SKColor(0xC0, 0x80, 0x00);
                canvas.DrawRect(new SKRect(0, 0, 200, 200), paint);
            }
            using (SKCanvas canvas = new SKCanvas(srcBitmap))
            {
                canvas.Clear();
                paint.Color = new SKColor(0x00, 0x80, 0xC0);
                canvas.DrawRect(new SKRect(100, 100, 300, 300), paint);
            }
        }
    }
    ···
}
```

实例构造函数具有类型参数的`SKBlendMode`。 它将此参数保存在字段中。 

```csharp
class PorterDuffCanvasView : SKCanvasView
{
    ···
    SKBlendMode blendMode;

    public PorterDuffCanvasView(SKBlendMode blendMode)
    {
        this.blendMode = blendMode;
    }

    protected override void OnPaintSurface(SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Find largest square that fits
        float rectSize = Math.Min(info.Width, info.Height);
        float x = (info.Width - rectSize) / 2;
        float y = (info.Height - rectSize) / 2;
        SKRect rect = new SKRect(x, y, x + rectSize, y + rectSize);

        // Draw destination bitmap
        canvas.DrawBitmap(dstBitmap, rect);

        // Draw source bitmap
        using (SKPaint paint = new SKPaint())
        {
            paint.BlendMode = blendMode;
            canvas.DrawBitmap(srcBitmap, rect, paint);
        }

        // Draw outline
        using (SKPaint paint = new SKPaint())
        {
            paint.Style = SKPaintStyle.Stroke;
            paint.Color = SKColors.Black;
            paint.StrokeWidth = 2;
            rect.Inflate(-1, -1);
            canvas.DrawRect(rect, paint);
        }
    }
}
```

`OnPaintSurface`替代绘制两个位图。 通常情况下绘制第一个：

```csharp
canvas.DrawBitmap(dstBitmap, rect);
```

使用绘制第二个`SKPaint`对象在其中`BlendMode`属性已设置为构造函数参数：

```csharp
using (SKPaint paint = new SKPaint())
{
    paint.BlendMode = blendMode;
    canvas.DrawBitmap(srcBitmap, rect, paint);
}
```

其余部分`OnPaintSurface`替代周围绘制一个矩形以指示其大小的位图。

`PorterDuffGridPage`类创建的 14 个实例`PorterDurffCanvasView`，一个用于的每个成员`blendModes`数组。 顺序`SKBlendModes`数组中的成员是表的稍有不同的以便放置彼此相邻的类似模式。 14 的实例`PorterDuffCanvasView`组织中的标签以及`Grid`:

```csharp
public class PorterDuffGridPage : ContentPage
{
    public PorterDuffGridPage()
    {
        Title = "Porter-Duff Grid";

        SKBlendMode[] blendModes =
        {
            SKBlendMode.Src, SKBlendMode.Dst, SKBlendMode.SrcOver, SKBlendMode.DstOver,
            SKBlendMode.SrcIn, SKBlendMode.DstIn, SKBlendMode.SrcOut, SKBlendMode.DstOut,
            SKBlendMode.SrcATop, SKBlendMode.DstATop, SKBlendMode.Xor, SKBlendMode.Plus,
            SKBlendMode.Modulate, SKBlendMode.Clear
        };

        Grid grid = new Grid
        {
            Margin = new Thickness(5)
        };

        for (int row = 0; row < 4; row++)
        {
            grid.RowDefinitions.Add(new RowDefinition { Height = GridLength.Auto });
            grid.RowDefinitions.Add(new RowDefinition { Height = GridLength.Star });
        }

        for (int col = 0; col < 3; col++)
        {
            grid.ColumnDefinitions.Add(new ColumnDefinition { Width = GridLength.Star });
        }

        for (int i = 0; i < blendModes.Length; i++)
        {
            SKBlendMode blendMode = blendModes[i];
            int row = 2 * (i / 4);
            int col = i % 4;

            Label label = new Label
            {
                Text = blendMode.ToString(),
                HorizontalTextAlignment = TextAlignment.Center
            };
            Grid.SetRow(label, row);
            Grid.SetColumn(label, col);
            grid.Children.Add(label);

            PorterDuffCanvasView canvasView = new PorterDuffCanvasView(blendMode);

            Grid.SetRow(canvasView, row + 1);
            Grid.SetColumn(canvasView, col);
            grid.Children.Add(canvasView);
        }

        Content = grid;
    }
}
```

下面是结果：

[![Porter Duff 网格](porter-duff-images/PorterDuffGrid.png "Porter Duff 网格")](porter-duff-images/PorterDuffGrid-Large.png#lightbox)

你将想要使您自己确信透明度是至关重要的 Porter Duff 的混合模式运行正常。 `PorterDuffCanvasView`类包含三个对总共`Canvas.Clear`方法。 所有这些使用无参数方法，它将所有像素都设置为透明：

```csharp
canvas.Clear();
```

请尝试更改这些调用的任何位置，以像素为单位设置为不透明的白色：

```csharp
canvas.Clear(SKColors.White);
```

按照此更改后，一些的混合模式将起来起作用，但其他人将不会。 如果将源位图的背景设置为白色，则`SrcOver`模式不起作用，因为源位图，以便通过显示目标中的不透明的像素为单位。 如果设置的目标位图或为白色，然后在画布背景`DstOver`不起作用，因为目标不具有任何透明的像素为单位。

可能倾向于替换中的位图**Porter Duff 网格**页上使用更简单`DrawRect`调用。 有关目标矩形而不是源矩形，这将起作用。 源矩形必须包含不止是颜色色调蓝色的区域。 源矩形必须包含对应于目标的彩色区域的透明区域。 这些仅然后将混合模式下工作。

## <a name="using-mattes-with-porter-duff"></a>使用 Porter Duff 遮罩

**程序块-Wall 合成**页显示了经典的组合任务的示例：图片必须为收集组从几个部分，包括需要消除背景位图。 下面是**SeatedMonkey.jpg**有问题的背景位图：

![就位 Monkey](porter-duff-images/SeatedMonkey.jpg "装 Monkey")

在组合的情况下，相应准备_亚光效果_创建的这是另一个位图，否则为黑色，如果你想要显示的图像和透明。 此文件命名为**SeatedMonkeyMatte.png**是在中资源之间**媒体**文件夹中的[ **SkiaSharpFormsDemos** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)示例:

![就位 Monkey 遮罩](porter-duff-images/SeatedMonkeyMatte.png "装 Monkey 亚光效果")

这是_不_熟练地创建亚光效果。 理想情况下，亚光效果应包括部分透明的黑色像素，边缘像素和此亚光效果却没有。

XAML 文件**程序块-Wall 组合的情况下**页上实例化`SKCanvasView`和一个`Button`指导用户完成撰写最终映像的过程：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Effects.BrickWallCompositingPage"
             Title="Brick-Wall Compositing">

    <StackLayout>
        <skia:SKCanvasView x:Name="canvasView"
                           VerticalOptions="FillAndExpand"
                           PaintSurface="OnCanvasViewPaintSurface" />

        <Button Text="Show sitting monkey"
                HorizontalOptions="Center"
                Margin="0, 10"
                Clicked="OnButtonClicked" />

    </StackLayout>
</ContentPage>
```

代码隐藏文件加载它需要和处理的两个位图`Clicked`事件的`Button`。 为每个`Button`依次`step`字段是递增和的新`Text`属性设置为`Button`。 当`step`达到 5，它将重新设置为 0:

```csharp
public partial class BrickWallCompositingPage : ContentPage
{
    SKBitmap monkeyBitmap = BitmapExtensions.LoadBitmapResource(
        typeof(BrickWallCompositingPage), 
        "SkiaSharpFormsDemos.Media.SeatedMonkey.jpg");

    SKBitmap matteBitmap = BitmapExtensions.LoadBitmapResource(
        typeof(BrickWallCompositingPage), 
        "SkiaSharpFormsDemos.Media.SeatedMonkeyMatte.png");

    int step = 0;

    public BrickWallCompositingPage ()
    {
        InitializeComponent ();
    }

    void OnButtonClicked(object sender, EventArgs args)
    {
        Button btn = (Button)sender;
        step = (step + 1) % 5;

        switch (step)
        {
            case 0: btn.Text = "Show sitting monkey"; break;
            case 1: btn.Text = "Draw matte with DstIn"; break;
            case 2: btn.Text = "Draw sidewalk with DstOver"; break;
            case 3: btn.Text = "Draw brick wall with DstOver"; break;
            case 4: btn.Text = "Reset"; break;
        }

        canvasView.InvalidateSurface();
    }
    
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();
        ···
    }
}
```

当首次运行该程序时，执行任何操作都可以看到除`Button`:

[![程序块-Wall 组合步骤 0](porter-duff-images/BrickWallCompositing0.png "程序块-Wall 组合步骤 0")](porter-duff-images/BrickWallCompositing0-Large.png#lightbox)

按下`Button`一次将导致`step`增量为 1，并`PaintSurface`处理程序现在将显示**SeatedMonkey.jpg**:

```csharp
public partial class BrickWallCompositingPage : ContentPage
{
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        ···
        float x = (info.Width - monkeyBitmap.Width) / 2;
        float y = info.Height - monkeyBitmap.Height;

        // Draw monkey bitmap
        if (step >= 1)
        {
            canvas.DrawBitmap(monkeyBitmap, x, y);
        }
        ···
    }
}
```

没有任何`SKPaint`对象，因此无混合模式。 位图显示在屏幕的底部：

[![程序块-Wall 组合步骤 1](porter-duff-images/BrickWallCompositing1.png "程序块-Wall 组合步骤 1")](porter-duff-images/BrickWallCompositing1-Large.png#lightbox)

按`Button`再次和`step`递增为 2。 这是显示的关键步骤**SeatedMonkeyMatte.png**文件：

```csharp
public partial class BrickWallCompositingPage : ContentPage
{
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        ···
        // Draw matte to exclude monkey's surroundings
        if (step >= 2)
        {
            using (SKPaint paint = new SKPaint())
            {
                paint.BlendMode = SKBlendMode.DstIn;
                canvas.DrawBitmap(matteBitmap, x, y, paint);
            }
        }
        ···
    }
}
```

混合模式是`SKBlendMode.DstIn`，这意味着目标将被保留，对应于源的非透明区域的区域中。 对应于原始位图的目标矩形的其余部分将变为透明：

[![程序块-Wall 组合步骤 2](porter-duff-images/BrickWallCompositing2.png "程序块-Wall 组合步骤 2")](porter-duff-images/BrickWallCompositing2-Large.png#lightbox)

在后台已删除。 

下一步是要绘制一个矩形，类似于 monkey 坐在人行道。 此人行道的外观取决于两个着色器的组合： 纯色着色器和 Perlin 噪音着色器：

```csharp
public partial class BrickWallCompositingPage : ContentPage
{
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        ···
        const float sidewalkHeight = 80;
        SKRect rect = new SKRect(info.Rect.Left, info.Rect.Bottom - sidewalkHeight,
                                 info.Rect.Right, info.Rect.Bottom);

        // Draw gravel sidewalk for monkey to sit on
        if (step >= 3)
        {
            using (SKPaint paint = new SKPaint())
            {
                paint.Shader = SKShader.CreateCompose(
                                    SKShader.CreateColor(SKColors.SandyBrown),
                                    SKShader.CreatePerlinNoiseTurbulence(0.1f, 0.3f, 1, 9));

                paint.BlendMode = SKBlendMode.DstOver;
                canvas.DrawRect(rect, paint);
            }
        }
        ···
    }
}
```

因为此人行道必须深入 monkey，混合模式是`DstOver`。 仅在背景色为透明色其中仅显示目标：

[![程序块-Wall 组合步骤 3](porter-duff-images/BrickWallCompositing3.png "程序块-Wall 组合步骤 3")](porter-duff-images/BrickWallCompositing3-Large.png#lightbox)

最后一步添加产生效果。 该程序作为静态属性使用可用的程序块-wall 位图磁贴`BrickWallTile`在`AlgorithmicBrickWallPage`类。 平移转换添加到`SKShader.CreateBitmap`调用，以便最后一行是完整的磁贴移动磁贴：

```csharp
public partial class BrickWallCompositingPage : ContentPage
{
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        ···
        // Draw bitmap tiled brick wall behind monkey
        if (step >= 4)
        {
            using (SKPaint paint = new SKPaint())
            {
                SKBitmap bitmap = AlgorithmicBrickWallPage.BrickWallTile;
                float yAdjust = (info.Height - sidewalkHeight) % bitmap.Height;

                paint.Shader = SKShader.CreateBitmap(bitmap,
                                                     SKShaderTileMode.Repeat,
                                                     SKShaderTileMode.Repeat,
                                                     SKMatrix.MakeTranslation(0, yAdjust));
                paint.BlendMode = SKBlendMode.DstOver;
                canvas.DrawRect(info.Rect, paint);
            }
        }
    }
}
```

为方便起见，`DrawRect`调用显示此着色器对整个画布，但`DstOver`模式将输出限制为仅画布仍透明的区域：

[![程序块-Wall 组合步骤 4](porter-duff-images/BrickWallCompositing4.png "程序块-Wall 组合步骤 4")](porter-duff-images/BrickWallCompositing4-Large.png#lightbox)

显然，还有其他方式来编写此场景。 它可以开始在后台和前台进行构建。 但使用的混合模式提供了更大的灵活性。 具体而言，遮罩的使用可以从组合场景要排除的位图的背景。

正如本文中获知[路径和区域中的剪辑](../../curves/clipping.md)，则`SKCanvas`类定义了三种类型的剪辑，对应于[ `ClipRect` ](xref:SkiaSharp.SKCanvas.ClipRect*)， [ `ClipPath` ](xref:SkiaSharp.SKCanvas.ClipPath*)，并[ `ClipRegion` ](xref:SkiaSharp.SKCanvas.ClipRegion*)方法。 Porter Duff 的混合模式添加另一种类型的剪辑，允许限制对任何内容，您可以绘制，包括位图图像。 在中使用遮罩**程序块-Wall 合成**实质上是定义剪辑区域。

## <a name="gradient-transparency-and-transitions"></a>渐变透明度和转换

Porter Duff 示例混合模式在这篇文章有所有涉及组成不透明像素和透明的像素为单位，但不是部分透明的像素的图像前面所示。 混合模式函数为这些像素为单位定义。 下表是 Porter Duff 的混合模式的更正式定义，使用表示法中 Skia [ **SkBlendMode 引用**](https://skia.org/user/api/SkBlendMode_Reference)。 (因为**SkBlendMode 引用**为 Skia 引用，使用 c + + 语法。)

从概念上讲，每个像素的红色、 绿色、 蓝色和 alpha 组件从字节转换为 0 到 1 范围内的浮点数。 Alpha 通道，0 表示完全透明，1 是完全不透明

下表中的表示法使用下列缩写：

- **Da**是目标的 alpha 通道
- **Dc**是 RGB 颜色的目标
- **Sa**是源 alpha 通道
- **Sc**是 RGB 颜色的源

RGB 颜色预先乘以 alpha 值。 例如，如果**Sc**表示纯红色，但**Sa**是 RGB 颜色为 0x80， **(0x80，0，0)**。 如果**Sa**为 0，则所有 RGB 组件也都是零。

结果显示在使用 alpha 通道和由逗号分隔的 RGB 颜色的方括号中： **[alpha，颜色]**。 颜色为红色、 绿色和蓝色组件单独执行计算：

| 模式       | 操作 |
| ---------- | --------- |
| `Clear`    | [0，0]    |
| `Src`      | [Sa，Sc]  |
| `Dst`      | [Da，Dc]  |
| `SrcOver`  | [Sa + Da·(1 – Sa)，Sc + Dc·(1 – Sa) | 
| `DstOver`  | [Da + Sa·(1 – Da) Dc + Sc·(1 – Da) |
| `SrcIn`    | [Sa·Da Sc·Da] |
| `DstIn`    | [Da·Sa Dc·Sa] |
| `SrcOut`   | [Sa·(1 – Da) Sc·(1 – Da)] |
| `DstOut`   | [Da·(1 – Sa)，Dc·(1 – Sa)] |
| `SrcATop`  | [Da Sc·Da + Dc·(1 – Sa)] |
| `DstATop`  | [Sa Dc·Sa + Sc·(1 – Da)] |
| `Xor`      | [Sa + Da – 2·Sa·Da Sc·(1 – Da) + Dc·(1 – Sa)] |
| `Plus`     | [Sa + Da、 Sc + Dc] |
| `Modulate` | [Sa·Da Sc·Dc] | 

这些操作是更轻松地分析何时**Da**并**Sa**为 0 或 1。 例如，对于默认值`SrcOver`模式下，如果**Sa**为 0，则**Sc**是 0，并将该结果也是 **[Da，Dc]**，目标 alpha 和颜色。 如果**Sa**为 1，则会得到 **[Sa，Sc]**，源 alpha 和颜色，或 **[1，Sc]**。

`Plus`和`Modulate`模式是从其他稍有不同，新的颜色，可以得到的源和目标组合。 `Plus`模式可以解释使用字节组件或浮点组件。 在中**Porter Duff 网格**目标颜色之前所示的页面 **(0xC0、 0x80，0x00)** 源颜色是 **(0x00、 0x80，0xC0)**。 添加组件的每个对，但在 0xFF 其限制之和。 结果是颜色 **(0xC0、 0xff 内，0xC0)**。 这是交集所示的颜色。

有关`Modulate`模式下，必须 RGB 值转换为浮点。 目标颜色是 **（0.75，0.5，0）** ，而源 **（0、 0.5、 0.75）**。 RGB 组件是每个全部相乘，并且结果为 **（0，0.25，0）**。 这是在交集所示的颜色**Porter Duff 网格**此模式下的页。

**Porter Duff 透明度**页面允许您检查 Porter Duff 的混合模式的部分透明的图形对象的进行操作。 XAML 文件包含`Picker`Porter Duff 模式：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp;assembly=SkiaSharp"
             xmlns:skiaviews="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Effects.PorterDuffTransparencyPage"
             Title="Porter-Duff Transparency">

    <StackLayout>
        <skiaviews:SKCanvasView x:Name="canvasView"
                                VerticalOptions="FillAndExpand"
                                PaintSurface="OnCanvasViewPaintSurface" />

        <Picker x:Name="blendModePicker"
                Title="Blend Mode"
                Margin="10"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.ItemsSource>
                <x:Array Type="{x:Type skia:SKBlendMode}">
                    <x:Static Member="skia:SKBlendMode.Clear" />
                    <x:Static Member="skia:SKBlendMode.Src" />
                    <x:Static Member="skia:SKBlendMode.Dst" />
                    <x:Static Member="skia:SKBlendMode.SrcOver" />
                    <x:Static Member="skia:SKBlendMode.DstOver" />
                    <x:Static Member="skia:SKBlendMode.SrcIn" />
                    <x:Static Member="skia:SKBlendMode.DstIn" />
                    <x:Static Member="skia:SKBlendMode.SrcOut" />
                    <x:Static Member="skia:SKBlendMode.DstOut" />
                    <x:Static Member="skia:SKBlendMode.SrcATop" />
                    <x:Static Member="skia:SKBlendMode.DstATop" />
                    <x:Static Member="skia:SKBlendMode.Xor" />
                    <x:Static Member="skia:SKBlendMode.Plus" />
                    <x:Static Member="skia:SKBlendMode.Modulate" />
                </x:Array>
            </Picker.ItemsSource>

            <Picker.SelectedIndex>
                3
            </Picker.SelectedIndex>
        </Picker>
    </StackLayout>
</ContentPage>
```

代码隐藏文件已满使用线性渐变的大小相同的两个矩形。 目标渐变是从右上到左下。 它是 brownish 右上角中，但然后向中心开始淡入淡出为透明，并是透明的左下角。 

源矩形具有从左上向右下的渐变。 左上角是略带蓝色但再次淡出为透明的并且是透明的右下角。 

```csharp
public partial class PorterDuffTransparencyPage : ContentPage
{
    public PorterDuffTransparencyPage()
    {
        InitializeComponent();
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

        // Make square display rectangle smaller than canvas
        float size = 0.9f * Math.Min(info.Width, info.Height);
        float x = (info.Width - size) / 2;
        float y = (info.Height - size) / 2;
        SKRect rect = new SKRect(x, y, x + size, y + size);

        using (SKPaint paint = new SKPaint())
        {
            // Draw destination
            paint.Shader = SKShader.CreateLinearGradient(
                                new SKPoint(rect.Right, rect.Top),
                                new SKPoint(rect.Left, rect.Bottom),
                                new SKColor[] { new SKColor(0xC0, 0x80, 0x00),
                                                new SKColor(0xC0, 0x80, 0x00, 0) },
                                new float[] { 0.4f, 0.6f },
                                SKShaderTileMode.Clamp);

            canvas.DrawRect(rect, paint);

            // Draw source
            paint.Shader = SKShader.CreateLinearGradient(
                                new SKPoint(rect.Left, rect.Top),
                                new SKPoint(rect.Right, rect.Bottom),
                                new SKColor[] { new SKColor(0x00, 0x80, 0xC0), 
                                                new SKColor(0x00, 0x80, 0xC0, 0) },
                                new float[] { 0.4f, 0.6f },
                                SKShaderTileMode.Clamp);

            // Get the blend mode from the picker
            paint.BlendMode = blendModePicker.SelectedIndex == -1 ? 0 :
                                    (SKBlendMode)blendModePicker.SelectedItem;

            canvas.DrawRect(rect, paint);

            // Stroke surrounding rectangle
            paint.Shader = null;
            paint.BlendMode = SKBlendMode.SrcOver;
            paint.Style = SKPaintStyle.Stroke;
            paint.Color = SKColors.Black;
            paint.StrokeWidth = 3;
            canvas.DrawRect(rect, paint);
        }
    }
}
```

此程序演示 Porter Duff 的混合模式可用于非位图图形对象。 但是，源必须包括透明区域。 这是此处的示例，因为渐变填充矩形，但的渐变的部分是透明。

以下是三个示例：

[![Porter Duff 透明度](porter-duff-images/PorterDuffTransparency.png "Porter Duff 透明度")](porter-duff-images/PorterDuffTransparency-Large.png#lightbox)

目标和源的配置是非常类似于原始的 Porter Duff 255 页中显示的图[_组合的情况下数字图像_](https://graphics.pixar.com/library/Compositing/paper.pdf)白皮书，不过它在此页演示混合模式是良好的部分透明的区域。

对于一些不同的效果，可以使用透明的梯度。 一种可能性屏蔽，它类似的方法中所示[**径向渐变进行屏蔽**](../shaders/circular-gradients.md#radial-gradients-for-masking)一部分**SkiaSharp 循环渐变页**。 大部分**组合的情况下掩码**页是类似于该早期版本的程序。 它加载位图资源，并确定要在其中显示该矩形。 径向渐变是基于创建的预先确定的中心和半径：

```csharp
public class CompositingMaskPage : ContentPage
{
    SKBitmap bitmap = BitmapExtensions.LoadBitmapResource(
        typeof(CompositingMaskPage),
        "SkiaSharpFormsDemos.Media.MountainClimbers.jpg");

    static readonly SKPoint CENTER = new SKPoint(180, 300);
    static readonly float RADIUS = 120;

    public CompositingMaskPage ()
    {
        Title = "Compositing Mask";

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

        // Find rectangle to display bitmap
        float scale = Math.Min((float)info.Width / bitmap.Width,
                               (float)info.Height / bitmap.Height);

        SKRect rect = SKRect.Create(scale * bitmap.Width, scale * bitmap.Height);

        float x = (info.Width - rect.Width) / 2;
        float y = (info.Height - rect.Height) / 2;
        rect.Offset(x, y);

        // Display bitmap in rectangle
        canvas.DrawBitmap(bitmap, rect);

        // Adjust center and radius for scaled and offset bitmap
        SKPoint center = new SKPoint(scale * CENTER.X + x,
                                        scale * CENTER.Y + y);
        float radius = scale * RADIUS;

        using (SKPaint paint = new SKPaint())
        {
            paint.Shader = SKShader.CreateRadialGradient(
                                center,
                                radius,
                                new SKColor[] { SKColors.Black,
                                                SKColors.Transparent },
                                new float[] { 0.6f, 1 },
                                SKShaderTileMode.Clamp);

            paint.BlendMode = SKBlendMode.DstIn;

            // Display rectangle using that gradient and blend mode
            canvas.DrawRect(rect, paint);
        }

        canvas.DrawColor(SKColors.Pink, SKBlendMode.DstOver);
    }
}
```

此程序的不同之处是渐变中心的黑色开头和结尾的透明度。 该字符串出现在混合模式的位图`DstIn`，其中只有几个方面的源的不透明的显示目标。

之后`DrawRect`调用中，画布的整个图面是透明定义径向渐变圆圈除外。 由最后一次调用：

```csharp
canvas.DrawColor(SKColors.Pink, SKBlendMode.DstOver);
```

被粉色的画布的透明区域：

[![组合的情况下掩码](porter-duff-images/CompositingMask.png "组合的情况下掩码")](porter-duff-images/CompositingMask-Large.png#lightbox)

此外可以使用 Porter Duff 模式和部分透明渐变到另一个图像中的转换。 **渐变过渡**页包含`Slider`以指示从 0 到 1 的转换中的进度级别和一个`Picker`选择所需的转换的类型：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Effects.GradientTransitionsPage"
             Title="Gradient Transitions">
    
    <StackLayout>
        <skia:SKCanvasView x:Name="canvasView"
                           VerticalOptions="FillAndExpand"
                           PaintSurface="OnCanvasViewPaintSurface" />

        <Slider x:Name="progressSlider"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="{Binding Source={x:Reference progressSlider},
                              Path=Value,
                              StringFormat='Progress = {0:F2}'}"
               HorizontalTextAlignment="Center" />

        <Picker x:Name="transitionPicker" 
                Title="Transition" 
                Margin="10"
                SelectedIndexChanged="OnPickerSelectedIndexChanged" />
        
    </StackLayout>
</ContentPage>
```

代码隐藏文件加载两个位图资源，以演示转换。 这些是相同的两个图像中使用**位图消失**在本文前面的页。 该代码还定义一个枚举，包含对应于三种类型的渐变的三个成员&mdash;线性的径向和扫描。 这些值加载到`Picker`:

```csharp
public partial class GradientTransitionsPage : ContentPage
{
    SKBitmap bitmap1 = BitmapExtensions.LoadBitmapResource(
        typeof(GradientTransitionsPage),
        "SkiaSharpFormsDemos.Media.SeatedMonkey.jpg");

    SKBitmap bitmap2 = BitmapExtensions.LoadBitmapResource(
        typeof(GradientTransitionsPage),
        "SkiaSharpFormsDemos.Media.FacePalm.jpg");

    enum TransitionMode
    {
        Linear,
        Radial,
        Sweep
    };

    public GradientTransitionsPage ()
    {
        InitializeComponent ();

        foreach (TransitionMode mode in Enum.GetValues(typeof(TransitionMode)))
        {
            transitionPicker.Items.Add(mode.ToString());
        }

        transitionPicker.SelectedIndex = 0;
    }

    void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
    {
        canvasView.InvalidateSurface();
    }

    void OnPickerSelectedIndexChanged(object sender, EventArgs args)
    {
        canvasView.InvalidateSurface();
    }
    ···
}
```

代码隐藏文件创建三个`SKPaint`对象。 `paint0`对象不使用混合模式。 此绘制对象用于绘制一个具有渐变从黑色到透明中所示的矩形`colors`数组。 `positions`上的位置基于数组`Slider`，但调整某种程度上。 如果`Slider`位于其最小值或最大值、`progress`的值为 0 或 1，和两个位图之一应是完全可见。 `positions`数组必须相应地设置这些值。

如果`progress`值为 0，则`positions`数组包含值-0.1 和 0。 SkiaSharp 将否则调整将等于 0，表示渐变是黑色只能在 0 和透明第一个值。 当`progress`为 0.5，则该数组包含 0.45 和 0.55 的值。 渐变是黑色从 0 到 0.45，则将转换为透明的并且是完全透明的 0.55 为 1。 当`progress`为 1，`positions`数组是 1 和 1.1 中，这意味着渐变是黑色从 0 到 1。

`colors`并`position`数组所使用的三个方法中`SKShader`创建渐变。 只创建一个这些着色器根据`Picker`选择： 

```csharp
public partial class GradientTransitionsPage : ContentPage
{
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Assume both bitmaps are square for display rectangle
        float size = Math.Min(info.Width, info.Height);
        SKRect rect = SKRect.Create(size, size);
        float x = (info.Width - size) / 2;
        float y = (info.Height - size) / 2;
        rect.Offset(x, y);

        using (SKPaint paint0 = new SKPaint())
        using (SKPaint paint1 = new SKPaint())
        using (SKPaint paint2 = new SKPaint())
        {
            SKColor[] colors = new SKColor[] { SKColors.Black,
                                               SKColors.Transparent };

            float progress = (float)progressSlider.Value;

            float[] positions = new float[]{ 1.1f * progress - 0.1f,
                                             1.1f * progress };

            switch ((TransitionMode)transitionPicker.SelectedIndex)
            {
                case TransitionMode.Linear:
                    paint0.Shader = SKShader.CreateLinearGradient(
                                        new SKPoint(rect.Left, 0),
                                        new SKPoint(rect.Right, 0),
                                        colors,
                                        positions,
                                        SKShaderTileMode.Clamp);
                    break;

                case TransitionMode.Radial:
                    paint0.Shader = SKShader.CreateRadialGradient(
                                        new SKPoint(rect.MidX, rect.MidY),
                                        (float)Math.Sqrt(Math.Pow(rect.Width / 2, 2) +
                                                         Math.Pow(rect.Height / 2, 2)),
                                        colors,
                                        positions,
                                        SKShaderTileMode.Clamp);
                    break;

                case TransitionMode.Sweep:
                    paint0.Shader = SKShader.CreateSweepGradient(
                                        new SKPoint(rect.MidX, rect.MidY),
                                        colors,
                                        positions);
                    break;
            }

            canvas.DrawRect(rect, paint0);

            paint1.BlendMode = SKBlendMode.SrcOut;
            canvas.DrawBitmap(bitmap1, rect, paint1);

            paint2.BlendMode = SKBlendMode.DstOver;
            canvas.DrawBitmap(bitmap2, rect, paint2);
        }
    }
}
```

但不使用混合模式的矩形中显示该渐变。 在此之后`DrawRect`调用中，画布只包含从黑色到透明的渐变。 黑色的量增加更高版本`Slider`值。

中的最后四个语句`PaintSurface`处理程序中，将显示两个位图。 `SrcOut`混合模式，即仅在后台透明区域中显示第一个位图。 `DstOver`第二个位图的模式，即在其中第一个位图不会显示这些领域中仅显示第二个位图。

以下屏幕截图显示三个不同的转换类型，每 50%标记处：

[![渐变过渡](porter-duff-images/GradientTransitions.png "渐变过渡")](porter-duff-images/GradientTransitions-Large.png#lightbox)

## <a name="related-links"></a>相关链接

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （示例）](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
