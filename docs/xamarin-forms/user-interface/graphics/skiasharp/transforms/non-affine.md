---
title: 非仿射转换
description: 此文介绍了如何使用转换矩阵的第三个列创建透视和锥化效果，此示例代码进行了演示。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 785F4D13-7430-492E-B24E-3B45C560E9F1
author: davidbritch
ms.author: dabritch
ms.date: 04/14/2017
ms.openlocfilehash: da820b0c48eaec52da76504b1aed8e9793c1e74d
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61352079"
---
# <a name="non-affine-transforms"></a>非仿射转换

[![下载示例](~/media/shared/download.png)下载示例](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)

_使用转换矩阵的第三个列创建透视和锥化效果_

平移、 缩放、 旋转和倾斜都属于*仿射*转换。 仿射转换保留平行直线。 如果并行转换之前的两个行，它们并行之后保持转换。 矩形始终转换为平行四边形。

但是，SkiaSharp 还是非仿射转换，其具有将转换为任何凸四边形的矩形的功能的支持：

![](non-affine-images/nonaffinetransformexample.png "转换为凸四边形位图")

凸四边形是具有内部角度始终小于 180 度和边不会超过每个其他四个边的图形。

非仿射转换矩阵的第三个行设置为 0、 0 和 1 以外的值时转换结果。 完整`SKMatrix`乘法是：

<pre>
              │ ScaleX  SkewY   Persp0 │
| x  y  1 | × │ SkewX   ScaleY  Persp1 │ = | x'  y'  z' |
              │ TransX  TransY  Persp2 │
</pre>

结果转换公式是：

x = ScaleX·x + SkewX·y + TransX

y = SkewY·x + ScaleY·y + TransY

z = Persp0·x + Persp1·y + Persp2

使用为二维转换的 3 x 3 矩阵的基本原理是，所有内容保留在平面上其中 Z 等于 1。 除非`Persp0`并`Persp1`均为 0，和`Persp2`等于 1，则转换已禁用该平面的 Z 坐标。

若要还原到一个二维转换这，坐标必须移回到该平面。 另一个步骤是必需的。 X，y，和 z 的值必须除以 z:

x" = x' / z'

y"= y / z

z"= z / z = 1

这些参数称为*齐次坐标*和 Möbius 条带安年 8 月 Ferdinand Möbius，得更好地已知为他拓扑的奇怪之处，通过其进行开发。

如果 z 为 0，无限坐标中的部门结果。 事实上，开发齐次坐标 Möbius 的动机之一是能够表示的无限值的有限数字。

在显示时图形，但是，你想要避免呈现内容转换为无限值的坐标。 不会呈现这些坐标。 在这些坐标附近出现的所有内容将非常大，可能不以可视方式一致。

在此等式，你不希望的 z 值变为 0:

z = Persp0·x + Persp1·y + Persp2

因此，这些值存在一些实际的限制： 

`Persp2`单元格可以是零或不为零。 如果`Persp2`为零，则 z 为点 （0，0），0，这是通常不可取因为该点是在二维图形中很常见。 如果`Persp2`不等于零，则不会丢失的一般性如果`Persp2`固定为 1。 例如，如果你确定`Persp2`应为 5，然后您可以只是矩阵中的所有单元格被除 5，这使得`Persp2`等于 1，并且结果将是相同。

出于这些原因，`Persp2`通常固定为 1，这是标识矩阵中的相同值。

通常情况下，`Persp0`和`Persp1`是小的数字。 例如，假设开头恒等矩阵但集`Persp0`为 0.01:

<pre>
| 1  0   0.01 |
| 0  1    0   |
| 0  0    1   |
</pre>

转换公式是：

x = x / （0.01·x + 1）

y = y / （0.01·x + 1）

现在，使用此转换来呈现原点定位 100 像素方框。 下面是如何转换的四个角：

（0，0） → （0，0）

（0，100） → （0，100）

（100，0） → （50，0）

（100，100） → （50，50）

当 x 为 100，则 z 分母为 2，因此 x 和 y 坐标会有效地缩减了一半。 框的右侧将成为短于左侧和右侧：

![](non-affine-images/nonaffinetransform.png "一个框，受制于非仿射转换")

`Persp`这些单元格名称的部分是因为透视收缩建议框现在倾斜最荒谬不过在查看器的右侧指"透视"。

**测试透视**页，可以尝试使用的值`Persp0`和`Pers1`若要了解它们如何工作的。 这些矩阵的单元格的合理值是非常小的`Slider`在通用 Windows 平台中不能正确处理它们。 若要容纳 UWP 问题，这两个`Slider`中的元素[ **TestPerspective.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/TestPerspectivePage.xaml)需要初始化范围为从 1 到-1:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Transforms.TestPerspectivePage"
             Title="Test Perpsective">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="*" />
        </Grid.RowDefinitions>

        <Grid.Resources>
            <ResourceDictionary>
                <Style TargetType="Label">
                    <Setter Property="HorizontalTextAlignment" Value="Center" />
                </Style>

                <Style TargetType="Slider">
                    <Setter Property="Minimum" Value="-1" />
                    <Setter Property="Maximum" Value="1" />
                    <Setter Property="Margin" Value="20, 0" />
                </Style>
            </ResourceDictionary>
        </Grid.Resources>

        <Slider x:Name="persp0Slider"
                Grid.Row="0"
                ValueChanged="OnPersp0SliderValueChanged" />

        <Label x:Name="persp0Label"
               Text="Persp0 = 0.0000"
               Grid.Row="1" />

        <Slider x:Name="persp1Slider"
                Grid.Row="2"
                ValueChanged="OnPersp1SliderValueChanged" />

        <Label x:Name="persp1Label"
               Text="Persp1 = 0.0000"
               Grid.Row="3" />

        <skia:SKCanvasView x:Name="canvasView"
                           Grid.Row="4"
                           PaintSurface="OnCanvasViewPaintSurface" />
    </Grid>
</ContentPage>
```

事件处理程序中的滑块[ `TestPerspectivePage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/TestPerspectivePage.xaml.cs)代码隐藏文件中将值划分 100，以便它们 –0.01 和 0.01 之间的范围。 此外，在构造函数加载位图中：

```csharp
public partial class TestPerspectivePage : ContentPage
{
    SKBitmap bitmap;

    public TestPerspectivePage()
    {
        InitializeComponent();

        string resourceID = "SkiaSharpFormsDemos.Media.SeatedMonkey.jpg";
        Assembly assembly = GetType().GetTypeInfo().Assembly;

        using (Stream stream = assembly.GetManifestResourceStream(resourceID))
        {
            bitmap = SKBitmap.Decode(stream);
        }
    }

    void OnPersp0SliderValueChanged(object sender, ValueChangedEventArgs args)
    {
        Slider slider = (Slider)sender;
        persp0Label.Text = String.Format("Persp0 = {0:F4}", slider.Value / 100);
        canvasView.InvalidateSurface();
    }

    void OnPersp1SliderValueChanged(object sender, ValueChangedEventArgs args)
    {
        Slider slider = (Slider)sender;
        persp1Label.Text = String.Format("Persp1 = {0:F4}", slider.Value / 100);
        canvasView.InvalidateSurface();
    }
    ...
}
```

`PaintSurface`处理程序计算`SKMatrix`名为值`perspectiveMatrix`除以 100 这些两个滑块的值。 与结合使用这两个转换将此转换的中心放在中心的位图的转换：

```csharp
public partial class TestPerspectivePage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Calculate perspective matrix
        SKMatrix perspectiveMatrix = SKMatrix.MakeIdentity();
        perspectiveMatrix.Persp0 = (float)persp0Slider.Value / 100;
        perspectiveMatrix.Persp1 = (float)persp1Slider.Value / 100;

        // Center of screen
        float xCenter = info.Width / 2;
        float yCenter = info.Height / 2;

        SKMatrix matrix = SKMatrix.MakeTranslation(-xCenter, -yCenter);
        SKMatrix.PostConcat(ref matrix, perspectiveMatrix);
        SKMatrix.PostConcat(ref matrix, SKMatrix.MakeTranslation(xCenter, yCenter));

        // Coordinates to center bitmap on canvas
        float x = xCenter - bitmap.Width / 2;
        float y = yCenter - bitmap.Height / 2;

        canvas.SetMatrix(matrix);
        canvas.DrawBitmap(bitmap, x, y);
    }
}
```

下面是一些示例图像：

[![](non-affine-images/testperspective-small.png "测试透视页面的三个屏幕截图")](non-affine-images/testperspective-large.png#lightbox "带来三倍的测试透视页屏幕截图")

在试验滑块时，您会发现值 0.0066 超出或低于 –0.0066 导致要突然变得断开和是不同的图像。 所转换的位图是 300 像素正方形。 因此从 –150 为 150 的位图的坐标范围相对于其中心转换。 请记住，z 的值是：

z = Persp0·x + Persp1·y + 1

如果`Persp0`或`Persp1`大于 0.0066 下面 –0.0066，则始终导致 z 位图的一些坐标或值为零。 为零，导致部门和呈现变得混乱。 在使用非仿射转换，你想要避免呈现任何内容导致被零除的坐标。

通常情况下，不会设置您`Persp0`和`Persp1`中隔离。 此外通常很有必要，在要实现某些类型的非仿射转换矩阵中设置其他单元格。

是一个此类非仿射转换*锥化转换*。 此类型的非仿射转换将保留整体的矩形的尺寸，但通过逐渐一侧：

![](non-affine-images/tapertransform.png "一个框，受制于锥化转换")

[ `TaperTransform` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/TaperTransform.cs)类执行通用的计算的非仿射转换基于这些参数：

- 所转换图像的矩形的大小
- 一个枚举，指示通过逐渐，矩形的边
- 另一个枚举，指示如何通过它逐渐，和
- 斜削的范围。

下面是代码：

```csharp
enum TaperSide { Left, Top, Right, Bottom }

enum TaperCorner { LeftOrTop, RightOrBottom, Both }

static class TaperTransform
{
    public static SKMatrix Make(SKSize size, TaperSide taperSide, TaperCorner taperCorner, float taperFraction)
    {
        SKMatrix matrix = SKMatrix.MakeIdentity();

        switch (taperSide)
        {
            case TaperSide.Left:
                matrix.ScaleX = taperFraction;
                matrix.ScaleY = taperFraction;
                matrix.Persp0 = (taperFraction - 1) / size.Width;

                switch (taperCorner)
                {
                    case TaperCorner.RightOrBottom:
                        break;

                    case TaperCorner.LeftOrTop:
                        matrix.SkewY = size.Height * matrix.Persp0;
                        matrix.TransY = size.Height * (1 - taperFraction);
                        break;

                    case TaperCorner.Both:
                        matrix.SkewY = (size.Height / 2) * matrix.Persp0;
                        matrix.TransY = size.Height * (1 - taperFraction) / 2;
                        break;
                }
                break;

            case TaperSide.Top:
                matrix.ScaleX = taperFraction;
                matrix.ScaleY = taperFraction;
                matrix.Persp1 = (taperFraction - 1) / size.Height;

                switch (taperCorner)
                {
                    case TaperCorner.RightOrBottom:
                        break;

                    case TaperCorner.LeftOrTop:
                        matrix.SkewX = size.Width * matrix.Persp1;
                        matrix.TransX = size.Width * (1 - taperFraction);
                        break;

                    case TaperCorner.Both:
                        matrix.SkewX = (size.Width / 2) * matrix.Persp1;
                        matrix.TransX = size.Width * (1 - taperFraction) / 2;
                        break;
                }
                break;

            case TaperSide.Right:
                matrix.ScaleX = 1 / taperFraction;
                matrix.Persp0 = (1 - taperFraction) / (size.Width * taperFraction);

                switch (taperCorner)
                {
                    case TaperCorner.RightOrBottom:
                        break;

                    case TaperCorner.LeftOrTop:
                        matrix.SkewY = size.Height * matrix.Persp0;
                        break;

                    case TaperCorner.Both:
                        matrix.SkewY = (size.Height / 2) * matrix.Persp0;
                        break;
                }
                break;

            case TaperSide.Bottom:
                matrix.ScaleY = 1 / taperFraction;
                matrix.Persp1 = (1 - taperFraction) / (size.Height * taperFraction);

                switch (taperCorner)
                {
                    case TaperCorner.RightOrBottom:
                        break;

                    case TaperCorner.LeftOrTop:
                        matrix.SkewX = size.Width * matrix.Persp1;
                        break;

                    case TaperCorner.Both:
                        matrix.SkewX = (size.Width / 2) * matrix.Persp1;
                        break;
                }
                break;
        }
        return matrix;
    }
}
```

在使用此类**锥化转换**页。 XAML 文件实例化两个`Picker`要选择的枚举值，元素和一个`Slider`选择锥化部分。 [ `PaintSurface` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/TaperTransformPage.xaml.cs#L55)处理程序将使用两个锥化转换将转换结果翻译以便相对于位图左上角的转换：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    TaperSide taperSide = (TaperSide)taperSidePicker.SelectedItem;
    TaperCorner taperCorner = (TaperCorner)taperCornerPicker.SelectedItem;
    float taperFraction = (float)taperFractionSlider.Value;

    SKMatrix taperMatrix =
        TaperTransform.Make(new SKSize(bitmap.Width, bitmap.Height),
                            taperSide, taperCorner, taperFraction);

    // Display the matrix in the lower-right corner
    SKSize matrixSize = matrixDisplay.Measure(taperMatrix);

    matrixDisplay.Paint(canvas, taperMatrix,
        new SKPoint(info.Width - matrixSize.Width,
                    info.Height - matrixSize.Height));

    // Center bitmap on canvas
    float x = (info.Width - bitmap.Width) / 2;
    float y = (info.Height - bitmap.Height) / 2;

    SKMatrix matrix = SKMatrix.MakeTranslation(-x, -y);
    SKMatrix.PostConcat(ref matrix, taperMatrix);
    SKMatrix.PostConcat(ref matrix, SKMatrix.MakeTranslation(x, y));

    canvas.SetMatrix(matrix);
    canvas.DrawBitmap(bitmap, x, y);
}
```

下面是一些可能的恶意活动：

[![](non-affine-images/tapertransform-small.png "三重锥化转换页屏幕截图")](non-affine-images/tapertransform-large.png#lightbox "锥化转换页上的三个屏幕截图")

另一种通用非仿射转换是在下一篇文章中所示的三维旋转[ **3D 旋转**](3d-rotation.md)。

非仿射转换可以将矩形转换为任何凸四边形。 这可通过演示**显示非仿射矩阵**页。 它是非常类似于**显示仿射矩阵**页上，从[**矩阵转换**](matrix.md)一文，只不过它具有第四个`TouchPoint`对象操作中的第四角的位图：

[![](non-affine-images/shownonaffinematrix-small.png "显示非仿射矩阵页上的三个屏幕截图")](non-affine-images/shownonaffinematrix-large.png#lightbox "显示非仿射矩阵页上的三个屏幕截图")

只要未尝试使内部角度的四个角位图大于 180 度或进行相互交叉的两个方面，该程序已成功计算从使用此方法的转换[ `ShowNonAffineMatrixPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/ShowNonAffineMatrixPage.xaml.cs)类：

```csharp
static SKMatrix ComputeMatrix(SKSize size, SKPoint ptUL, SKPoint ptUR, SKPoint ptLL, SKPoint ptLR)
{
    // Scale transform
    SKMatrix S = SKMatrix.MakeScale(1 / size.Width, 1 / size.Height);

    // Affine transform
    SKMatrix A = new SKMatrix
    {
        ScaleX = ptUR.X - ptUL.X,
        SkewY = ptUR.Y - ptUL.Y,
        SkewX = ptLL.X - ptUL.X,
        ScaleY = ptLL.Y - ptUL.Y,
        TransX = ptUL.X,
        TransY = ptUL.Y,
        Persp2 = 1
    };

    // Non-Affine transform
    SKMatrix inverseA;
    A.TryInvert(out inverseA);
    SKPoint abPoint = inverseA.MapPoint(ptLR);
    float a = abPoint.X;
    float b = abPoint.Y;

    float scaleX = a / (a + b - 1);
    float scaleY = b / (a + b - 1);

    SKMatrix N = new SKMatrix
    {
        ScaleX = scaleX,
        ScaleY = scaleY,
        Persp0 = scaleX - 1,
        Persp1 = scaleY - 1,
        Persp2 = 1
    };

    // Multiply S * N * A
    SKMatrix result = SKMatrix.MakeIdentity();
    SKMatrix.PostConcat(ref result, S);
    SKMatrix.PostConcat(ref result, N);
    SKMatrix.PostConcat(ref result, A);

    return result;
}
```

为了便于计算，此方法获取作为三个单独的转换，且显示这些转换如何修改位图的四个角箭头此处表示产品的总转换：

（0，0） （0，0） 的 → → （0，0） → (x，0，y0) （左上角）

(0，H) → （0，1） （0，1） → → (x1，y1) （左下方）

(W，0) → （1，0） （1，0） 的 → → (x2，y2) （右上方）

（W，H） → （1，1） (a，b) → → (x3 y3) （右下方）

在右侧的最后一个坐标是与四个触摸点相关联的四个点。 这些是位图的边角的最后一个坐标。

W 和 H 表示宽度和位图的高度。 第一个转换`S`只是缩放为 1 像素正方形的位图。 第二个转换为非仿射转换`N`，和第三个是仿射转换`A`。 该仿射转换基于三个点，因此它只需像前面仿射[ `ComputeMatrix` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/ShowAffineMatrixPage.xaml.cs#L68)方法并不涉及具有的第四个行 (a，b） 点。

`a`和`b`值进行计算，以便第三个转换为仿射转换。 该代码获取仿射变换的逆变换，然后使用该映射的右下角。 这是点 (a，b)。

非仿射转换的另一个用途是模拟三维图形。 在下一篇文章中， [ **3D 旋转**](3d-rotation.md)您了解如何将二维图形在 3D 空间中的。


## <a name="related-links"></a>相关链接

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （示例）](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
