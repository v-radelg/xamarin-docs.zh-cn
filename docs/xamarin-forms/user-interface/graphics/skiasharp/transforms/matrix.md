---
title: SkiaSharp 中的矩阵转换
description: 本文深入探讨到 SkiaSharp 转换具有通用转换矩阵，并演示此示例代码。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 9EDED6A0-F0BF-4471-A9EF-E0D6C5954AE4
author: davidbritch
ms.author: dabritch
ms.date: 04/12/2017
ms.openlocfilehash: 192f0745874b54989ab9070014dae2a5e9e98110
ms.sourcegitcommit: 605f7c480c3f7b5dd364fdb1bd4d983de8f7ed25
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/13/2019
ms.locfileid: "56213772"
---
# <a name="matrix-transforms-in-skiasharp"></a>SkiaSharp 中的矩阵转换

[![下载示例](~/media/shared/download.png)下载示例](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)

_深入了解使用通用的变换矩阵的 SkiaSharp 转换_

应用于的所有转换`SKCanvas`对象中的单个实例合并[ `SKMatrix` ](xref:SkiaSharp.SKMatrix)结构。 这是一个类似于所有新式 2D 图形系统中的标准 3-3 转换矩阵。

如您所见，您可以使用转换在 SkiaSharp 中无需知道有关该转换矩阵，但转换矩阵是从理论角度而言，重要并且使用转换来修改路径时，它是非常重要或处理复杂的触摸输入，这两个这是本文和下一步中演示的。

![](matrix-images/matrixtransformexample.png "位图受制于仿射转换")

应用于当前转换矩阵`SKCanvas`是可随时通过访问只读[ `TotalMatrix` ](xref:SkiaSharp.SKCanvas.TotalMatrix)属性。 您可以设置新的转换矩阵使用[ `SetMatrix` ](xref:SkiaSharp.SKCanvas.SetMatrix(SkiaSharp.SKMatrix))方法，并且你可以还原该转换矩阵为默认值通过调用[ `ResetMatrix` ](xref:SkiaSharp.SKCanvas.ResetMatrix)。

唯一其他`SKCanvas`直接适用于画布的矩阵转换的成员是[ `Concat` ](xref:SkiaSharp.SKCanvas.Concat(SkiaSharp.SKMatrix@))该连接两个矩阵相乘它们。

默认转换矩阵为单位矩阵，其中 1 中的对角线的单元格和 0 在其他地方包括：

<pre>
| 1  0  0 |
| 0  1  0 |
| 0  0  1 |
</pre>

可以创建使用静态某标识矩阵[ `SKMatrix.MakeIdentity` ](xref:SkiaSharp.SKMatrix.MakeIdentity)方法：

```csharp
SKMatrix matrix = SKMatrix.MakeIdentity();
```

`SKMatrix`默认构造函数 does*不*返回恒等矩阵。 它返回矩阵的所有单元设置为零。 不要使用`SKMatrix`构造函数除非计划手动设置这些单元格。

当 SkiaSharp 呈现图形对象时，每个点 （x，y） 是有效地转换为与第三列中的 1 指示 1-3 矩阵：

<pre>
| x  y  1 |
</pre>

此 1-3 矩阵的 Z 坐标值设置为 1 表示三维点。 二维矩阵转换要求在三个维度中工作的原因有数学原因 （稍后讨论）。 您可以将作为表示点在 3D 坐标系统中，但始终在二维平面上此 1-3 矩阵，其中 Z 等于 1。

此 1-3 矩阵乘以转换矩阵，结果是在画布上呈现的点：

<pre>
              | 1  0  0 |
| x  y  1 | × | 0  1  0 | = | x'  y'  z' |
              | 0  0  1 |
</pre>

使用标准的矩阵乘法，已转换的点如下所示：

`x' = x`

`y' = y`

`z' = 1`

这是默认转换。

当`Translate`上调用方法`SKCanvas`对象，`tx`并`ty`参数`Translate`方法成为转换矩阵的第三个行中的前两个单元格：

<pre>
|  1   0   0 |
|  0   1   0 |
| tx  ty   1 |
</pre>

该乘法运算现在如下所示：

<pre>
              |  1   0   0 |
| x  y  1 | × |  0   1   0 | = | x'  y'  z' |
              | tx  ty   1 |
</pre>

下面是转换公式：

`x' = x + tx`

`y' = y + ty`

缩放比例具有默认值为 1。 当您调用`Scale`上的新方法`SKCanvas`对象，包含生成的转换矩阵`sx`和`sy`中对角线的单元格的自变量：

<pre>
              | sx   0   0 |
| x  y  1 | × |  0  sy   0 | = | x'  y'  z' |
              |  0   0   1 |
</pre>

转换公式如下所示：

`x' = sx · x`

`y' = sy · y`

转换矩阵后调用`Skew`包含为缩放比例相邻矩阵单元中的两个参数：

<pre>
              │   1   ySkew   0 │
| x  y  1 | × │ xSkew   1     0 │ = | x'  y'  z' |
              │   0     0     1 │
</pre>

转换公式是：

`x' = x + xSkew · y`

`y' = ySkew · x + y`

为调用`RotateDegrees`或`RotateRadians`为 α 的角度，转换矩阵如下所示：

<pre>
              │  cos(α)  sin(α)  0 │
| x  y  1 | × │ –sin(α)  cos(α)  0 │ = | x'  y'  z' |
              │    0       0     1 │
</pre>

下面是转换公式：

`x' = cos(α) · x - sin(α) · y`

`y' = sin(α) · x - cos(α) · y`

0 度 α 时，它是恒等矩阵。 180 度 α 时，转换矩阵如下所示：

<pre>
| –1   0   0 |
|  0  –1   0 |
|  0   0   1 |
</pre>

180 度旋转等效于水平和垂直翻转对象的也可以通过设置为 – 1 的缩放比例。

所有这些类型的转换都属于*仿射*转换。 仿射转换永远不会涉及第三个列的矩阵，保持为 0、 0 和 1 的默认值。 文章[**非仿射转换**](non-affine.md)讨论了非仿射转换。

## <a name="matrix-multiplication"></a>矩阵乘法

使用转换矩阵的一个重要优点是可以通过矩阵乘法，这通常称为 SkiaSharp 文档中获得的复合转换*串联*。 与转换相关的方法中的许多`SKCanvas`指"预串联"或"pre-concat。" 这是乘法，这很重要，因为具有不可交换性矩阵乘法的顺序。

例如，对于文档[ `Translate` ](xref:SkiaSharp.SKCanvas.Translate(System.Single,System.Single))方法说它"Pre concats 当前与指定的转换矩阵"文档时为[ `Scale` ](xref:SkiaSharp.SKCanvas.Scale(System.Single,System.Single))方法说它"Pre-concats 当前与指定的缩放矩阵。"

这意味着指定的方法调用的转换是乘数 （左操作数） 和当前的转换矩阵是被乘数 （右操作数）。

假设`Translate`调用跟`Scale`:

```csharp
canvas.Translate(tx, ty);
canvas.Scale(sx, sy);
```

`Scale`转换乘以`Translate`对于复合转换矩阵转换：

<pre>
| sx   0   0 |   |  1   0   0 |   | sx   0   0 |
|  0  sy   0 | × |  0   1   0 | = |  0  sy   0 |
|  0   0   1 |   | tx  ty   1 |   | tx  ty   1 |
</pre>

`Scale` 无法调用之前`Translate`如下所示：

```csharp
canvas.Scale(sx, sy);
canvas.Translate(tx, ty);
```

在这种情况下，该乘法运算的顺序颠倒，并将缩放比例有效地应用于转换因子：

<pre>
|  1   0   0 |   | sx   0   0 |   |  sx      0    0 |
|  0   1   0 | × |  0  sy   0 | = |   0     sy    0 |
| tx  ty   1 |   |  0   0   1 |   | tx·sx  ty·sy  1 |
</pre>

下面是`Scale`使用透视点的方法：

```csharp
canvas.Scale(sx, sy, px, py);
```

这相当于以下的平移和缩放调用：

```csharp
canvas.Translate(px, py);
canvas.Scale(sx, sy);
canvas.Translate(–px, –py);
```

三个转换矩阵按相反的顺序从方法中代码的显示方式相乘：

<pre>
|  1    0   0 |   | sx   0   0 |   |  1   0  0 |   |    sx         0     0 |
|  0    1   0 | × |  0  sy   0 | × |  0   1  0 | = |     0        sy     0 |
| –px  –py  1 |   |  0   0   1 |   | px  py  1 |   | px–px·sx  py–py·sy  1 |
</pre>

## <a name="the-skmatrix-structure"></a>SKMatrix 结构

`SKMatrix`结构定义的类型的九个读/写属性`float`对应于转换矩阵的九个单元：

<pre>
│ ScaleX  SkewY   Persp0 │
│ SkewX   ScaleY  Persp1 │
│ TransX  TransY  Persp2 │
</pre>

`SKMatrix` 此外定义了名为的属性[ `Values` ](xref:SkiaSharp.SKMatrix.Values)类型的`float[]`。 此属性可以用于设置或获取按顺序一次的九个值`ScaleX`， `SkewX`， `TransX`， `SkewY`， `ScaleY`， `TransY`， `Persp0`， `Persp1`，和`Persp2`。

`Persp0`， `Persp1`，并`Persp2`一文中讨论了单元格[**非仿射转换**](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/non-affine.md)。 如果这些单元格的其默认值为 0、 0 和 1，然后转换乘以的坐标点如下：

<pre>
              │ ScaleX  SkewY   0 │
| x  y  1 | × │ SkewX   ScaleY  0 │ = | x'  y'  z' |
              │ TransX  TransY  1 │
</pre>

`x' = ScaleX · x + SkewX · y + TransX`

`y' = SkewX · x + ScaleY · y + TransY`

`z' = 1`

这是完整的二维仿射转换。 仿射转换可保留平行线，这意味着一个矩形将永远不会转换为的平行四边形之外的任何内容。

`SKMatrix`结构定义用于创建多个静态方法`SKMatrix`值。 这些都返回`SKMatrix`值：

- [`MakeTranslation`](xref:SkiaSharp.SKMatrix.MakeTranslation(System.Single,System.Single))
- [`MakeScale`](xref:SkiaSharp.SKMatrix.MakeScale(System.Single,System.Single))
- [`MakeScale`](xref:SkiaSharp.SKMatrix.MakeScale(System.Single,System.Single,System.Single,System.Single)) 使用透视点
- [`MakeRotation`](xref:SkiaSharp.SKMatrix.MakeRotation(System.Single)) 以弧度为单位的角度
- [`MakeRotation`](xref:SkiaSharp.SKMatrix.MakeRotation(System.Single,System.Single,System.Single)) 角度，以弧度为单位使用透视点
- [`MakeRotationDegrees`](xref:SkiaSharp.SKMatrix.MakeRotationDegrees(System.Single))
- [`MakeRotationDegrees`](xref:SkiaSharp.SKMatrix.MakeRotationDegrees(System.Single,System.Single,System.Single)) 使用透视点
- [`MakeSkew`](xref:SkiaSharp.SKMatrix.MakeSkew(System.Single,System.Single))

`SKMatrix` 此外定义了多个静态方法相串联两个矩阵，这意味着若要将它们相乘。 这些方法的命名[ `Concat` ](xref:SkiaSharp.SKMatrix.Concat*)， [ `PostConcat` ](xref:SkiaSharp.SKMatrix.PostConcat*)，以及[ `PreConcat` ](xref:SkiaSharp.SKMatrix.PreConcat*)，并且有两个版本的每个。 这些方法都有没有返回值;相反，它们引用现有`SKMatrix`通过值`ref`参数。 在以下示例中， `A`， `B`，并`R`（适用于"结果"） 是所有`SKMatrix`值。

这两个`Concat`方法调用如下：

```csharp
SKMatrix.Concat(ref R, A, B);

SKMatrix.Concat(ref R, ref A, ref B);
```

它们可以执行以下乘法：

`R = B × A`

其他方法有只有两个参数。 第一个参数是修改后，并从方法调用返回时，包含两个矩阵的乘积。 这两个`PostConcat`方法调用如下：

```csharp
SKMatrix.PostConcat(ref A, B);

SKMatrix.PostConcat(ref A, ref B);
```

这些调用执行以下操作：

`A = A × B`

这两个`PreConcat`方法非常相似：

```csharp
SKMatrix.PreConcat(ref A, B);

SKMatrix.PreConcat(ref A, ref B);
```

这些调用执行以下操作：

`A = B × A`

所有这些方法的版本`ref`参数是在调用的基础实现效率稍有提高，但可能会令人困惑的某个人可以读取你代码中，并假设的任何内容`ref`修改参数该方法。 此外，通常很方便地将传递的参数之一的结果，`Make`方法，例如：

```csharp
SKMatrix result;
SKMatrix.Concat(result, SKMatrix.MakeTranslation(100, 100),
                        SKMatrix.MakeScale(3, 3));
```

这会创建以下矩阵：

<pre>
│   3    0  0 │
│   0    3  0 │
│ 100  100  1 │
</pre>

这是乘以转换变换缩放转换。 在此特定情况下`SKMatrix`结构具有一个名为方法提供一种快捷方式[ `SetScaleTranslate` ](xref:SkiaSharp.SKMatrix.SetScaleTranslate(System.Single,System.Single,System.Single,System.Single)):

```csharp
SKMatrix R = new SKMatrix();
R.SetScaleTranslate(3, 3, 100, 100);
```

这是一个多次时，安全地使用`SKMatrix`构造函数。 `SetScaleTranslate`方法设置矩阵的所有九个单元格。 此外可安全用于`SKMatrix`构造函数使用静态`Rotate`和`RotateDegrees`方法：

```csharp
SKMatrix R = new SKMatrix();

SKMatrix.Rotate(ref R, radians);

SKMatrix.Rotate(ref R, radians, px, py);

SKMatrix.RotateDegrees(ref R, degrees);

SKMatrix.RotateDegrees(ref R, degrees, px, py);
```

这些方法的用途*不*连接到的现有转换的旋转转换。 方法设置矩阵的所有单元格。 它们是功能上等同于`MakeRotation`并`MakeRotationDegrees`方法，只不过它们不实例化`SKMatrix`值。

假设您有`SKPath`你想要显示，但您希望使用它具有一定程度上不同的方向或不同中心点的对象。 您可以通过调用来修改该路径的所有坐标[ `Transform` ](xref:SkiaSharp.SKPath.Transform(SkiaSharp.SKMatrix))方法`SKPath`与`SKMatrix`参数。 **路径转换**页说明如何执行此操作。 [ `PathTransform` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/PathTransformPage.cs)类的引用`HendecagramPath`字段中的对象但使用其构造函数将转换应用到该路径：

```csharp
public class PathTransformPage : ContentPage
{
    SKPath transformedPath = HendecagramArrayPage.HendecagramPath;

    public PathTransformPage()
    {
        Title = "Path Transform";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;

        SKMatrix matrix = SKMatrix.MakeScale(3, 3);
        SKMatrix.PostConcat(ref matrix, SKMatrix.MakeRotationDegrees(360f / 22));
        SKMatrix.PostConcat(ref matrix, SKMatrix.MakeTranslation(300, 300));

        transformedPath.Transform(matrix);
    }
    ...
}
```

`HendecagramPath`对象具有在一个中心 （0，0），并从该中心的所有方向的 100 个单位向外扩展的星型 11 点。 这意味着该路径具有正和负坐标。 **路径转换**页时首选使用带有星号三次作为较大，并可使用所有正坐标。 此外，它不需要在星型以直接指向一个点。 它希望改为一个点在星型点直接向下。 （在星型具有 11 点，因为它不能具有这两者。）这需要通过 360 度旋转星形除以 22。

构造函数生成`SKMatrix`对象使用的三个单独转换从`PostConcat`采用以下模式，其中 A、 B 和 C 是的实例方法`SKMatrix`:

```csharp
SKMatrix matrix = A;
SKMatrix.PostConcat(ref A, B);
SKMatrix.PostConcat(ref A, C);
```

这是一系列连续乘法运算，因此结果是，如下所示：

`A × B × C`

连续乘法运算可帮助了解每个转换的作用。 缩放转换路径坐标大小增加到原来的 3，因此坐标范围从 –300 为 300。 旋转转换旋转围绕原点的星号。 翻译转换然后会将它移动通过 300 像素右和向下，因此所有改为正值的坐标。

有其他生成相同的矩阵的序列。 下面是另一个：

```csharp
SKMatrix matrix = SKMatrix.MakeRotationDegrees(360f / 22);
SKMatrix.PostConcat(ref matrix, SKMatrix.MakeTranslation(100, 100));
SKMatrix.PostConcat(ref matrix, SKMatrix.MakeScale(3, 3));
```

这第一次，旋转围绕其中心路径，然后将其转换到右的 100 个像素并关闭所有坐标都均为正值。 星型然后增加的大小相对于其新的左上角，即 （0，0） 的点。

`PaintSurface`处理程序只需呈现此路径：

```csharp
public class PathTransformPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPaint paint = new SKPaint())
        {
            paint.Style = SKPaintStyle.Stroke;
            paint.Color = SKColors.Magenta;
            paint.StrokeWidth = 5;

            canvas.DrawPath(transformedPath, paint);
        }
    }
}

```

它将显示在画布的左上角中：

[![](matrix-images/pathtransform-small.png "路径转换页上的三个屏幕截图")](matrix-images/pathtransform-large.png#lightbox "路径转换页上的三个屏幕截图")

此程序的构造函数将矩阵应用到的路径进行以下调用：

```csharp
transformedPath.Transform(matrix);
```

路径*不*保留此矩阵作为属性。 相反，它适用于所有路径的坐标转换。 如果`Transform`调用同样，同样，应用转换和可以返回的唯一方法是通过应用其他撤消转换的矩阵。 幸运的是，`SKMatrix`结构定义[ `TryInvert` ](xref:SkiaSharp.SKMatrix.TryInvert*)方法获取矩阵反转给定的矩阵：

```csharp
SKMatrix inverse;
bool success = matrix.TryInverse(out inverse);
```

调用该方法`TryInverse`因为并非所有矩阵都是可逆转的但不太可能用于图形转换不可逆矩阵。

您还可以应用到矩阵转换`SKPoint`值、 数组的点， `SKRect`，或甚至只是在程序内的单个数字。 `SKMatrix`结构支持这些操作使用的单词开头的方法集合`Map`，如下：

```csharp
SKPoint transformedPoint = matrix.MapPoint(point);

SKPoint transformedPoint = matrix.MapPoint(x, y);

SKPoint[] transformedPoints = matrix.MapPoints(pointArray);

float transformedValue = matrix.MapRadius(floatValue);

SKRect transformedRect = matrix.MapRect(rect);
```

如果使用的最后一种方法，请记住，`SKRect`结构不能表示旋转的矩形。 该方法只适合`SKMatrix`值表示平移和缩放。

## <a name="interactive-experimentation"></a>交互式试验

若要了解的仿射转换的一种方法是位图的通过以交互方式移动在屏幕上四处的三个角并查看哪些转换结果。 这是背后的理念**显示仿射矩阵**页。 此页面要求也在其他演示中使用的其他两个类：

[ `TouchPoint` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/TouchPoint.cs)类显示一个半透明的圆形可在屏幕上拖动。 `TouchPoint` 要求`SKCanvasView`或元素的父级`SKCanvasView`有[ `TouchEffect` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/TouchEffect.cs)附加。 将 `Capture` 属性设置为 `true`。 在中`TouchAction`事件处理程序，程序必须调用`ProcessTouchEvent`中的方法`TouchPoint`为每个`TouchPoint`实例。 该方法将返回`true`如果触摸事件导致移动触摸点。 此外，`PaintSurface`处理程序必须调用`Paint`方法在每个`TouchPoint`实例，将其传递给`SKCanvas`对象。

`TouchPoint` 演示一种常见方式，可以在一个单独的类中封装 SkiaSharp 视觉对象。 该类还可以定义用于指定特征的视觉对象的属性和方法名为`Paint`与`SKCanvas`参数可以呈现。

`Center`属性的`TouchPoint`指示对象的位置。 可以设置此属性以初始化位置;在属性发生更改时在用户拖动画布周围的圆圈。

**显示仿射矩阵页**还要求[ `MatrixDisplay` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/MatrixDisplay.cs)类。 此类显示的单元格`SKMatrix`对象。 它具有两个公共方法：`Measure`以获取所呈现矩阵的维度和`Paint`显示它。 此类包含`MatrixPaint`类型的属性`SKPaint`，可以为不同的字体大小或颜色替换。

[ **ShowAffineMatrixPage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/ShowAffineMatrixPage.xaml)文件实例化`SKCanvasView`并将其附加`TouchEffect`。 [ **ShowAffineMatrixPage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/ShowAffineMatrixPage.xaml.cs)代码隐藏文件创建三个`TouchPoint`对象，然后将它们设置为对应于它加载从嵌入的位图的三个角的位置资源：

```csharp
public partial class ShowAffineMatrixPage : ContentPage
{
    SKMatrix matrix;
    SKBitmap bitmap;
    SKSize bitmapSize;

    TouchPoint[] touchPoints = new TouchPoint[3];

    MatrixDisplay matrixDisplay = new MatrixDisplay();

    public ShowAffineMatrixPage()
    {
        InitializeComponent();

        string resourceID = "SkiaSharpFormsDemos.Media.SeatedMonkey.jpg";
        Assembly assembly = GetType().GetTypeInfo().Assembly;

        using (Stream stream = assembly.GetManifestResourceStream(resourceID))
        {
            bitmap = SKBitmap.Decode(stream);
        }

        touchPoints[0] = new TouchPoint(100, 100);                  // upper-left corner
        touchPoints[1] = new TouchPoint(bitmap.Width + 100, 100);   // upper-right corner
        touchPoints[2] = new TouchPoint(100, bitmap.Height + 100);  // lower-left corner

        bitmapSize = new SKSize(bitmap.Width, bitmap.Height);
        matrix = ComputeMatrix(bitmapSize, touchPoints[0].Center,
                                           touchPoints[1].Center,
                                           touchPoints[2].Center);
    }
    ...
}
```

仿射矩阵定义唯一的三个点。 这三个`TouchPoint`对象对应于位图左上角、 右上方和左下角。 因为仅支持转换矩形变成平行四边形的仿射矩阵，其他三个隐含的第四个点。 调用构造函数以结束`ComputeMatrix`，其计算的单元格`SKMatrix`这 3 个点中的对象。

`TouchAction`处理程序调用`ProcessTouchEvent`方法的每个`TouchPoint`。 `scale`值将从 Xamarin.Forms 坐标转换为像素：

```csharp
public partial class ShowAffineMatrixPage : ContentPage
{
    ...
    void OnTouchEffectAction(object sender, TouchActionEventArgs args)
    {
        bool touchPointMoved = false;

        foreach (TouchPoint touchPoint in touchPoints)
        {
            float scale = canvasView.CanvasSize.Width / (float)canvasView.Width;
            SKPoint point = new SKPoint(scale * (float)args.Location.X,
                                        scale * (float)args.Location.Y);
            touchPointMoved |= touchPoint.ProcessTouchEvent(args.Id, args.Type, point);
        }

        if (touchPointMoved)
        {
            matrix = ComputeMatrix(bitmapSize, touchPoints[0].Center,
                                               touchPoints[1].Center,
                                               touchPoints[2].Center);
            canvasView.InvalidateSurface();
        }
    }
    ...
}
```

如果有的话`TouchPoint`已移动，则该方法调用`ComputeMatrix`再次和使图面无效。

`ComputeMatrix`方法确定权限隐含的这些三个点的矩阵。 名为矩阵`A`转换一个像素正方形矩形变成平行四边形基于三个点，而名为缩放转换`S`缩放到一个像素正方形矩形位图。 复合矩阵`S`× `A`:

```csharp
public partial class ShowAffineMatrixPage : ContentPage
{
    ...
    static SKMatrix ComputeMatrix(SKSize size, SKPoint ptUL, SKPoint ptUR, SKPoint ptLL)
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

        SKMatrix result = SKMatrix.MakeIdentity();
        SKMatrix.Concat(ref result, A, S);
        return result;
    }
    ...
}
```

最后，`PaintSurface`方法呈现位图基于该矩阵，矩阵显示在屏幕底部并呈现位图的三个角处的触摸点：

```csharp
public partial class ShowAffineMatrixPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Display the bitmap using the matrix
        canvas.Save();
        canvas.SetMatrix(matrix);
        canvas.DrawBitmap(bitmap, 0, 0);
        canvas.Restore();

        // Display the matrix in the lower-right corner
        SKSize matrixSize = matrixDisplay.Measure(matrix);

        matrixDisplay.Paint(canvas, matrix,
            new SKPoint(info.Width - matrixSize.Width,
                        info.Height - matrixSize.Height));

        // Display the touchpoints
        foreach (TouchPoint touchPoint in touchPoints)
        {
            touchPoint.Paint(canvas);
        }
    }
  }
```

首次加载页面，而其他两个屏幕显示它后某些操作时，iOS 屏幕截图显示了位图：

[![](matrix-images/showaffinematrix-small.png "显示仿射矩阵页面的三个屏幕截图")](matrix-images/showaffinematrix-large.png#lightbox "显示仿射矩阵页面的三个屏幕截图")

尽管它看起来像接触点拖动位图的边角，即只是一种幻想。 计算从接触点的矩阵转换位图，以便与接触点的边角保持一致。

更自然的用户可以移动、 调整大小，以及不旋转位图拖动边角，但通过使用一个或两个手指直接对对象后，若要拖动，捏合，与旋转。 在下一篇文章中对此进行了[**触摸操作**](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/touch.md)。

## <a name="the-reason-for-the-3-by-3-matrix"></a>原因 3 x 3 矩阵

可能会预期二维图形系统需要 2-2 转换矩阵：

<pre>
           │ ScaleX  SkewY  │
| x  y | × │                │ = | x'  y' |
           │ SkewX   ScaleY │
</pre>

这适用于缩放、 旋转和甚至倾斜，但并不支持的最基本的转换，这是转换。

问题是 2-2 矩阵，表示*线性*转换两个维度中。 线性转换保留一些基本的算术运算，但其中一个含义是线性转换永远不会更改的点 （0，0）。 线性转换无法进行转换。

在三个维度中一个线性转换矩阵如下所示：

<pre>
              │ ScaleX  SkewYX  SkewZX │
| x  y  z | × │ SkewXY  ScaleY  SkewZY │ = | x'  y'  z' |
              │ SkewXZ  SkewYZ  ScaleZ │
</pre>

标记为该单元格`SkewXY`表示的值，则会根据 Y 值的 X 坐标值; 该单元格`SkewXZ`意味着值，则会根据 Z; 的值的 X 坐标值扭曲同样适用于其他`Skew`单元格。

可以通过设置此 3D 转换矩阵限制到二维平面`SkewZX`并`SkewZY`为 0，和`ScaleZ`为 1:

<pre>
              │ ScaleX  SkewYX   0 │
| x  y  z | × │ SkewXY  ScaleY   0 │ = | x'  y'  z' |
              │ SkewXZ  SkewYZ   1 │
</pre>

如果完全在其中 Z 等于 1 的 3D 空间中的平面上绘制的二维图形，转换乘法如下所示：

<pre>
              │ ScaleX  SkewYX   0 │
| x  y  1 | × │ SkewXY  ScaleY   0 │ = | x'  y'  1 |
              │ SkewXZ  SkewYZ   1 │
</pre>

所有内容都保持在二维平面上其中 Z 等于 1，但`SkewXZ`和`SkewYZ`单元格有效地成为二维转换因子。

这是一个三维线性转换如何充当二维的非线性转换。 （通过类推，在三维图中的转换以 4-4 矩阵。）

`SKMatrix` SkiaSharp 中的结构定义为该行第三个属性：

<pre>
              │ ScaleX  SkewY   Persp0 │
| x  y  1 | × │ SkewX   ScaleY  Persp1 │ = | x'  y'  z` |
              │ TransX  TransY  Persp2 │
</pre>

值为非零`Persp0`和`Persp1`导致将对象移到二维平面移动其中 Z 等于 1 的转换。 这些对象移回该平面时，会发生什么情况文章中介绍了在[**非仿射转换**](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/non-affine.md)。


## <a name="related-links"></a>相关链接

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （示例）](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
