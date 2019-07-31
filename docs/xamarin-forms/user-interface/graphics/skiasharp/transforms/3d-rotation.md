---
title: SkiaSharp 中的三维旋转
description: 此文介绍了如何使用非仿射转换来旋转 3D 空间中的 2D 对象，此示例代码进行了演示。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: B5894EA0-C415-41F9-93A4-BBF6EC72AFB9
author: davidbritch
ms.author: dabritch
ms.date: 04/14/2017
ms.openlocfilehash: c6a600b7ae05df626d4315796c7a5e425639e2f8
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2019
ms.locfileid: "68652788"
---
# <a name="3d-rotations-in-skiasharp"></a>SkiaSharp 中的三维旋转

[![下载示例](~/media/shared/download.png)下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_非仿射转换用于旋转 3D 空间中的 2D 对象。_

非仿射转换的一个常见的应用程序可以模拟三维空间中的 2D 对象的旋转：

![](3d-rotation-images/3drotationsexample.png "在 3D 空间中旋转的文本字符串")

此工作涉及使用三维旋转，然后派生非仿射`SKMatrix`执行这些三维旋转的转换。

很难开发这`SKMatrix`转换仅在两个维度中工作。 在此 3 x 3 矩阵派生自用于在三维图中的 4-4 矩阵时，该作业变得容易。 SkiaSharp 包括[ `SKMatrix44` ](xref:SkiaSharp.SKMatrix44)类达到此目的，但在三维图中的一些背景知识是了解 3D 旋转和 4-4 转换矩阵的必要条件。

三维坐标系添加第三条坐标轴，称为 Z.从概念上讲，Z 轴是在屏幕上成直角。 在 3D 空间中的坐标点的三个数字来指示: （x，y，z）。 在 3D 在本文中，增加的 X 值中使用的坐标系统是向右和增加的 Y 值下降，就像两个维度中一样。 从屏幕里增加正 Z 值。 左上角，就像 2D 图形一样，是起点。 可以在屏幕视为与对此平面成直角 Z 轴 XY 平面。

这称为左侧坐标系统。 如果点的 X 坐标 （右侧），正方向左手食指右手食指的增大 Y 方向 （向下） 进行协调，您的拇指指向中增加 Z 坐标的方向，从扩展出屏幕。

在三维图中，转换基于 4-4 矩阵。 下面是 4-4 恒等矩阵：

<pre>
|  1  0  0  0  |
|  0  1  0  0  |
|  0  0  1  0  |
|  0  0  0  1  |
</pre>

在使用 4-4 矩阵，是可以方便地使用他们的行和列号标识的单元：

<pre>
|  M11  M12  M13  M14  |
|  M21  M22  M23  M24  |
|  M31  M32  M33  M34  |
|  M41  M42  M43  M44  |
</pre>

但是，SkiaSharp`Matrix44`类是稍有不同。 设置或获取各个单元格的值的唯一办法`SKMatrix44`是使用[ `Item` ](xref:SkiaSharp.SKMatrix44.Item(System.Int32,System.Int32))索引器。 行和列索引是从零开始而不是基于 1 和换行和列。 使用索引器访问在上图中的单元格 M14`[3, 0]`在`SKMatrix44`对象。

在 3D 图形系统中，三维点 （x，y，z） 转换为 1-4 4-4 转换矩阵相乘的矩阵：

<pre>
                 |  M11  M12  M13  M14  |
| x  y  z  1 | × |  M21  M22  M23  M24  | = | x'  y'  z'  w' |
                 |  M31  M32  M33  M34  |
                 |  M41  M42  M43  M44  |
</pre>

类似于 2D 转换发生在三个维度，3D 转换假定采用四个维度中的位置。 第四个维度被称为 W，并假定 3D 空间存在 W 坐标是等于 1 的 4d 空间内。 转换公式如下所示：

`x' = M11·x + M21·y + M31·z + M41`

`y' = M12·x + M22·y + M32·z + M42`

`z' = M13·x + M23·y + M33·z + M43`

`w' = M14·x + M24·y + M34·z + M44`

可通过转换公式的单元格`M11`， `M22`，`M33`缩放因素中的 X、 Y 和 Z 的说明，和`M41`， `M42`，和`M43`是 X、 Y 和 Z 翻译因素方向。

若要将这些坐标转换回三维空间其中 W 等于 1，则 x，y，和 z 坐标所有除以 w:

`x" = x' / w'`

`y" = y' / w'`

`z" = z' / w'`

`w" = w' / w' = 1`

该除数 w 提供在 3D 空间中的观点。 如果 w 等于 1，则会不出现任何透视。

在 3D 空间中的旋转可能相当复杂，但最简单的旋转是绕 X、 Y 和 Z 轴。 角度 α 绕 X 轴的旋转是此矩阵：

<pre>
|  1     0       0     0  |
|  0   cos(α)  sin(α)  0  |
|  0  –sin(α)  cos(α)  0  |
|  0     0       0     1  |
</pre>

X 值保持不变时受制于此转换。 绕 Y 轴的旋转离开保持不变的 Y 值：

<pre>
|  cos(α)  0  –sin(α)  0  |
|    0     1     0     0  |
|  sin(α)  0   cos(α)  0  |
|    0     0     0     1  |
</pre>

绕 Z 轴的旋转是与二维图形中的相同：

<pre>
|  cos(α)  sin(α)  0  0  |
| –sin(α)  cos(α)  0  0  |
|    0       0     1  0  |
|    0       0     0  1  |
</pre>

旋转的方向被隐含的坐标系统的左右手使用习惯。 这是惯用左手系统，因此，如果您指向越来越多的特定轴的值在左侧的滚动块 — 绕 X 轴旋转的右侧向下绕 Y 轴，并面向使用者绕 Z 轴旋转的旋转，然后的曲线 yo其他手指指示正角度的旋转的方向。

`SKMatrix44` 已通用化静态[ `CreateRotation` ](xref:SkiaSharp.SKMatrix44.CreateRotation(System.Single,System.Single,System.Single,System.Single))并[ `CreateRotationDegrees` ](xref:SkiaSharp.SKMatrix44.CreateRotationDegrees(System.Single,System.Single,System.Single,System.Single)) ，可以指定发生围绕其旋转的轴方法：

```csharp
public static SKMatrix44 CreateRotationDegrees (Single x, Single y, Single z, Single degrees)
```

绕 X 轴的旋转，将设置为 1，0，0 的前三个参数。 对于绕 Y 轴的旋转，将其设置为 0、 1、 0，并围绕 Z 轴旋转，请将其设置为 0，0，1。

透视是 4 的 4 的第四个列。 `SKMatrix44`没有用于创建透视转换的方法，但您可以创建一个自己使用以下代码：

```csharp
SKMatrix44 perspectiveMatrix = SKMatrix44.CreateIdentity();
perspectiveMatrix[3, 2] = -1 / depth;
```

参数名称的原因`depth`很快就会明显。 该代码创建的矩阵。

<pre>
|  1  0  0      0     |
|  0  1  0      0     |
|  0  0  1  -1/depth  |
|  0  0  0      1     |
</pre>

转换公式会导致以下计算的 w:

`w' = –z / depth + 1`

这不仅减少 X 和 Y 坐标，Z 值小于零时 （从概念上讲背后的 XY 平面） 时，增加正 Z 值的 X 和 Y 坐标。当 Z 坐标等于`depth`、 然后 w 为零，又无限坐标。 三维图形系统围绕照相机的隐喻，构建和`depth`此处的值表示从坐标系统的原点的照相机的距离。 图形对象是否有 Z 坐标，它是`depth`单位从原点，它从概念上讲触摸的相机镜头和变得可无限大。

请记住，您可以使用此`perspectiveMatrix`结合旋转矩阵的值。 如果要轮换的图形对象具有 X 或 Y 坐标大于`depth`，则此对象在三维空间中旋转是可能会涉及到 Z 坐标大于`depth`。 必须避免此操作 ！ 创建时`perspectiveMatrix`你想要设置`depth`为足够大，不管如何旋转的图形对象中的所有坐标的值。 这可确保永远不会有任何除数为零。

结合使用 3D 旋转和角度来看要求 4-4 矩阵相乘。 为此，`SKMatrix44`定义串联的方法。 如果`A`并`B`是`SKMatrix44`对象，则下面的代码设置等到 × b:

```csharp
A.PostConcat(B);
```

2D 图形系统中使用 4-4 转换矩阵时，它被应用于 2D 对象。 这些对象是固定的并且假定具有为零的 Z 坐标。 转换乘法会稍微简单一点比前面所示的转换：

<pre>
                 |  M11  M12  M13  M14  |
| x  y  0  1 | × |  M21  M22  M23  M24  | = | x'  y'  z'  w' |
                 |  M31  M32  M33  M34  |
                 |  M41  M42  M43  M44  |
</pre>

Z 结果并不涉及任何第三个矩阵的行中的单元格的转换公式中的 0 值：

x' = M11·x + M21·y + M41

y = M12·x + M22·y + M42

z = M13·x + M23·y + M43

w = M14·x + M24·y + M44

此外，z 坐标也不相关此处。 2D 图形系统中将显示一个三维对象，当它处于折叠状态到二维对象通过忽略 Z 坐标值。 转换公式是实际上只是这两个：

`x" = x' / w'`

`y" = y' / w'`

这意味着，第三个行*和*4-4 矩阵的第三列可以被忽略。

但如果是这样，为什么是 4-4 矩阵甚至需要第一个位置中？

尽管第三行和第三列的 4 通过 4 是二维转换、 第三个行和列不相关*做*发挥作用之前，当各种`SKMatrix44`值将全部相乘。 例如，假设乘绕 Y 轴透视转换的旋转：

<pre>
|  cos(α)  0  –sin(α)  0  |   |  1  0  0      0     |   |  cos(α)  0  –sin(α)   sin(α)/depth  |
|    0     1     0     0  | × |  0  1  0      0     | = |    0     1     0           0        |
|  sin(α)  0   cos(α)  0  |   |  0  0  1  -1/depth  |   |  sin(α)  0   cos(α)  -cos(α)/depth  |  
|    0     0     0     1  |   |  0  0  0      1     |   |    0     0     0           1        |
</pre>

在产品中，该单元格`M14`现在包含透视值。 如果你想要将该矩阵应用到 2D 对象，将消除第三个行和列将转换成 3 x 3 矩阵：

<pre>
|  cos(α)  0  sin(α)/depth  |
|    0     1       0        |
|    0     0       1        |
</pre>

现在它可以用于转换的 2D 点：

<pre>
                |  cos(α)  0  sin(α)/depth  |
|  x  y  1  | × |    0     1       0        | = |  x'  y'  z'  |
                |    0     0       1        |
</pre>

转换公式是：

`x' = cos(α)·x`

`y' = y`

`z' = (sin(α)/depth)·x + 1`

现在将所有内容除以 z:

`x" = cos(α)·x / ((sin(α)/depth)·x + 1)`

`y" = y / ((sin(α)/depth)·x + 1)`

X 值时使用一个正的角度绕 Y 轴，然后正旋转 2D 对象 recede 到后台，而负 X 值转到前台。 X 值看起来更接近于 Y 轴 （它所依据的余弦值） 作为最远的地方 Y 轴坐标变得更小或变复杂，因为他们远离观察者或更接近于查看器。

使用时`SKMatrix44`，执行所有的三维旋转和透视操作乘以各种`SKMatrix44`值。 然后您可以从 4 通过 4 提取二维的 3 x 3 矩阵使用矩阵[ `Matrix` ](xref:SkiaSharp.SKMatrix44.Matrix)属性的`SKMatrix44`类。 此属性返回熟悉`SKMatrix`值。

**旋转 3D**页的允许您对三维旋转进行试验。 [ **Rotation3DPage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/Rotation3DPage.xaml)文件实例化的四个滑块设置绕 X、 Y 和 Z 轴，旋转以及设置深度值：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Transforms.Rotation3DPage"
             Title="Rotation 3D">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
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
                    <Setter Property="Margin" Value="20, 0" />
                    <Setter Property="Maximum" Value="360" />
                </Style>
            </ResourceDictionary>
        </Grid.Resources>

        <Slider x:Name="xRotateSlider"
                Grid.Row="0"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="{Binding Source={x:Reference xRotateSlider},
                              Path=Value,
                              StringFormat='X-Axis Rotation = {0:F0}'}"
               Grid.Row="1" />

        <Slider x:Name="yRotateSlider"
                Grid.Row="2"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="{Binding Source={x:Reference yRotateSlider},
                              Path=Value,
                              StringFormat='Y-Axis Rotation = {0:F0}'}"
               Grid.Row="3" />

        <Slider x:Name="zRotateSlider"
                Grid.Row="4"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="{Binding Source={x:Reference zRotateSlider},
                              Path=Value,
                              StringFormat='Z-Axis Rotation = {0:F0}'}"
               Grid.Row="5" />

        <Slider x:Name="depthSlider"
                Grid.Row="6"
                Maximum="2500"
                Minimum="250"
                ValueChanged="OnSliderValueChanged" />

        <Label Grid.Row="7"
               Text="{Binding Source={x:Reference depthSlider},
                              Path=Value,
                              StringFormat='Depth = {0:F0}'}" />

        <skia:SKCanvasView x:Name="canvasView"
                           Grid.Row="8"
                           PaintSurface="OnCanvasViewPaintSurface" />
    </Grid>
</ContentPage>
```

请注意，`depthSlider`使用初始化`Minimum`250 的值。 这意味着此处要轮换的 2D 对象已限制为定义 250 像素半径沿原点的圆形的 X 和 Y 坐标。 此对象在 3D 空间中的任何旋转将始终导致坐标值小于 250。

[ **Rotation3DPage.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/Rotation3DPage.xaml.cs)代码隐藏文件加载位图为 300 像素正方形中：

```csharp
public partial class Rotation3DPage : ContentPage
{
    SKBitmap bitmap;

    public Rotation3DPage()
    {
        InitializeComponent();

        string resourceID = "SkiaSharpFormsDemos.Media.SeatedMonkey.jpg";
        Assembly assembly = GetType().GetTypeInfo().Assembly;

        using (Stream stream = assembly.GetManifestResourceStream(resourceID))
        {
            bitmap = SKBitmap.Decode(stream);
        }
    }

    void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
    {
        if (canvasView != null)
        {
            canvasView.InvalidateSurface();
        }
    }
    ...
}
```

如果在此位图上居中的 3D 转换，然后 X 和 Y 坐标介于 –150 和 150，虽然边角 212 像素从中心，以便所有内容都是 250 像素半径内。

`PaintSurface`处理程序将创建`SKMatrix44`对象基于滑块，并将其一起使用相乘`PostConcat`。 `SKMatrix`从最后提取值`SKMatrix44`对象括起来的转换转换可以在屏幕的中心旋转的中心：

```csharp
public partial class Rotation3DPage : ContentPage
{
    SKBitmap bitmap;

    public Rotation3DPage()
    {
        InitializeComponent();

        string resourceID = "SkiaSharpFormsDemos.Media.SeatedMonkey.jpg";
        Assembly assembly = GetType().GetTypeInfo().Assembly;

        using (Stream stream = assembly.GetManifestResourceStream(resourceID))
        {
            bitmap = SKBitmap.Decode(stream);
        }
    }

    void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
    {
        if (canvasView != null)
        {
            canvasView.InvalidateSurface();
        }
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Find center of canvas
        float xCenter = info.Width / 2;
        float yCenter = info.Height / 2;

        // Translate center to origin
        SKMatrix matrix = SKMatrix.MakeTranslation(-xCenter, -yCenter);

        // Use 3D matrix for 3D rotations and perspective
        SKMatrix44 matrix44 = SKMatrix44.CreateIdentity();
        matrix44.PostConcat(SKMatrix44.CreateRotationDegrees(1, 0, 0, (float)xRotateSlider.Value));
        matrix44.PostConcat(SKMatrix44.CreateRotationDegrees(0, 1, 0, (float)yRotateSlider.Value));
        matrix44.PostConcat(SKMatrix44.CreateRotationDegrees(0, 0, 1, (float)zRotateSlider.Value));

        SKMatrix44 perspectiveMatrix = SKMatrix44.CreateIdentity();
        perspectiveMatrix[3, 2] = -1 / (float)depthSlider.Value;
        matrix44.PostConcat(perspectiveMatrix);

        // Concatenate with 2D matrix
        SKMatrix.PostConcat(ref matrix, matrix44.Matrix);

        // Translate back to center
        SKMatrix.PostConcat(ref matrix,
            SKMatrix.MakeTranslation(xCenter, yCenter));

        // Set the matrix and display the bitmap
        canvas.SetMatrix(matrix);
        float xBitmap = xCenter - bitmap.Width / 2;
        float yBitmap = yCenter - bitmap.Height / 2;
        canvas.DrawBitmap(bitmap, xBitmap, yBitmap);
    }
}
```

尝试使用第四个滑块时，您将注意到不同的深度设置不会移动进一步从查看器中，该对象，但改为更改的角度来看影响程度：

[![](3d-rotation-images/rotation3d-small.png "三重的旋转 3D 页屏幕截图")](3d-rotation-images/rotation3d-large.png#lightbox "带来三倍的旋转 3D 页屏幕截图")

**经过动画处理的旋转 3D**还使用`SKMatrix44`进行动画处理 3D 空间中的文本字符串。 `textPaint`对象设置为一个字段使用构造函数中，以确定文本的边界：

```csharp
public class AnimatedRotation3DPage : ContentPage
{
    SKCanvasView canvasView;
    float xRotationDegrees, yRotationDegrees, zRotationDegrees;
    string text = "SkiaSharp";
    SKPaint textPaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Black,
        TextSize = 100,
        StrokeWidth = 3,
    };
    SKRect textBounds;

    public AnimatedRotation3DPage()
    {
        Title = "Animated Rotation 3D";

        canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;

        // Measure the text
        textPaint.MeasureText(text, ref textBounds);
    }
    ...
}
```

`OnAppearing`替代定义三个 Xamarin.Forms`Animation`对象进行动画处理`xRotationDegrees`， `yRotationDegrees`，和`zRotationDegrees`字段不同的速率。 请注意，这些动画的段设置为素数数字 （5 秒、 7 秒和 11 秒），方便整体组合仅重复的每个 385 秒或超过 10 分钟：

```csharp
public class AnimatedRotation3DPage : ContentPage
{
    ...
    protected override void OnAppearing()
    {
        base.OnAppearing();

        new Animation((value) => xRotationDegrees = 360 * (float)value).
            Commit(this, "xRotationAnimation", length: 5000, repeat: () => true);

        new Animation((value) => yRotationDegrees = 360 * (float)value).
            Commit(this, "yRotationAnimation", length: 7000, repeat: () => true);

        new Animation((value) =>
        {
            zRotationDegrees = 360 * (float)value;
            canvasView.InvalidateSurface();
        }).Commit(this, "zRotationAnimation", length: 11000, repeat: () => true);
    }

    protected override void OnDisappearing()
    {
        base.OnDisappearing();
        this.AbortAnimation("xRotationAnimation");
        this.AbortAnimation("yRotationAnimation");
        this.AbortAnimation("zRotationAnimation");
    }
    ...
}
```

上一个程序，如中所示`PaintCanvas`处理程序将创建`SKMatrix44`值旋转和角度来看，并将其一起相乘：

```csharp
public class AnimatedRotation3DPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Find center of canvas
        float xCenter = info.Width / 2;
        float yCenter = info.Height / 2;

        // Translate center to origin
        SKMatrix matrix = SKMatrix.MakeTranslation(-xCenter, -yCenter);

        // Scale so text fits
        float scale = Math.Min(info.Width / textBounds.Width,
                               info.Height / textBounds.Height);
        SKMatrix.PostConcat(ref matrix, SKMatrix.MakeScale(scale, scale));

        // Calculate composite 3D transforms
        float depth = 0.75f * scale * textBounds.Width;

        SKMatrix44 matrix44 = SKMatrix44.CreateIdentity();
        matrix44.PostConcat(SKMatrix44.CreateRotationDegrees(1, 0, 0, xRotationDegrees));
        matrix44.PostConcat(SKMatrix44.CreateRotationDegrees(0, 1, 0, yRotationDegrees));
        matrix44.PostConcat(SKMatrix44.CreateRotationDegrees(0, 0, 1, zRotationDegrees));

        SKMatrix44 perspectiveMatrix = SKMatrix44.CreateIdentity();
        perspectiveMatrix[3, 2] = -1 / depth;
        matrix44.PostConcat(perspectiveMatrix);

        // Concatenate with 2D matrix
        SKMatrix.PostConcat(ref matrix, matrix44.Matrix);

        // Translate back to center
        SKMatrix.PostConcat(ref matrix,
            SKMatrix.MakeTranslation(xCenter, yCenter));

        // Set the matrix and display the text
        canvas.SetMatrix(matrix);
        float xText = xCenter - textBounds.MidX;
        float yText = yCenter - textBounds.MidY;
        canvas.DrawText(text, xText, yText, textPaint);
    }
}
```

此 3D 旋转放在多个 2D 转换将旋转中心移到屏幕上，在中心和缩放的文本字符串的大小，以便它在屏幕的宽度相同：

[![](3d-rotation-images/animatedrotation3d-small.png "经过动画处理的旋转 3D 页面的三个屏幕截图")](3d-rotation-images/animatedrotation3d-large.png#lightbox "带来三倍的经过动画处理的旋转 3D 页屏幕截图")


## <a name="related-links"></a>相关链接

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
