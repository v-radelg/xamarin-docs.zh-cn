---
title: SkiaSharp 循环渐变
description: 了解有关不同类型的基于圆圈的渐变。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 400AE23A-6A0B-4FA8-BD6B-DE4146B04732
author: davidbritch
ms.author: dabritch
ms.date: 08/23/2018
ms.openlocfilehash: d56cc499112a937cd1a22664adeedd54c4397341
ms.sourcegitcommit: 1e3a0d853669dcc57d5dee0894d325d40c7d8009
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2019
ms.locfileid: "70199004"
---
# <a name="the-skiasharp-circular-gradients"></a>SkiaSharp 循环渐变

[![下载示例](~/media/shared/download.png)下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

[ `SKShader` ](xref:SkiaSharp.SKShader)类定义用于创建四种不同类型的渐变的静态方法。 [ **SkiaSharp 线性渐变**](linear-gradient.md)一文讨论[ `CreateLinearGradient` ](xref:SkiaSharp.SKShader.CreateLinearGradient*)方法。 本文介绍了其他三种类型的渐变，所有这些都按圆圈。

[ `CreateRadialGradient` ](xref:SkiaSharp.SKShader.CreateRadialGradient*)方法创建从圆形的中心发出的渐变：

![径向渐变示例](circular-gradients-images/RadialGradientSample.png)

[ `CreateSweepGradient` ](xref:SkiaSharp.SKShader.CreateSweepGradient*)方法创建的渐变效果扫描围绕圆的中心：

![扫描渐变示例](circular-gradients-images/SweepGradientSample.png)

第三个渐变的类型是非常不寻常。 它名为两个点圆锥渐变和定义的[ `CreateTwoPointConicalGradient` ](xref:SkiaSharp.SKShader.CreateTwoPointConicalGradient*)方法。 渐变到另一个扩展从一个圆：

![圆锥渐变示例](circular-gradients-images/ConicalGradientSample.png)

如果两个圆是不同的大小，渐变采用圆锥体的形式。

本文探讨了这些渐变中更多详细信息。

## <a name="the-radial-gradient"></a>径向渐变

[ `CreateRadialGradient` ](xref:SkiaSharp.SKShader.CreateRadialGradient(SkiaSharp.SKPoint,System.Single,SkiaSharp.SKColor[],System.Single[],SkiaSharp.SKShaderTileMode))方法具有以下语法：

```csharp
public static SKShader CreateRadialGradient (SKPoint center, 
                                             Single radius, 
                                             SKColor[] colors, 
                                             Single[] colorPos, 
                                             SKShaderTileMode mode)
```

一个[ `CreateRadialGradient` ](xref:SkiaSharp.SKShader.CreateRadialGradient(SkiaSharp.SKPoint,System.Single,SkiaSharp.SKColor[],System.Single[],SkiaSharp.SKShaderTileMode,SkiaSharp.SKMatrix))重载还包含转换矩阵参数。

前两个参数指定的中心圆和半径。 渐变开始在该中心并由里向外扩展的`radius`像素为单位。 除了会发生什么情况`radius`取决于[ `SKShaderTileMode` ](xref:SkiaSharp.SKShaderTileMode)参数。 `colors`参数是数组 （只需如下所示的线性渐变方法），两个或多个颜色和`colorPos`是 0 到 1 范围内的整数的数组。 这些整数表示的沿颜色的相对位置`radius`行。 可以将该参数设置为`null`，以便同样的颜色。

如果使用`CreateRadialGradient`要填充一个圆圈，可以设置的渐变的中心圆的中心，向圆的半径渐变的半径。 在这种情况下，`SKShaderTileMode`参数不起作用的渐变的呈现。 但是，如果渐变填充的区域大于圆定义渐变，则`SKShaderTileMode`自变量具有会发生什么情况之外圆深远的影响。

效果`SKShaderMode`中所示**径向渐变**页面[ **SkiaSharpFormsDemos** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)示例。 此页面的 XAML 文件实例化`Picker`，允许你选择的三个成员之一`SKShaderTileMode`枚举：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp;assembly=SkiaSharp"
             xmlns:skiaforms="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Effects.RadialGradientPage"
             Title="Radial Gradient">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="*" />
            <RowDefinition Height="Auto" />
        </Grid.RowDefinitions>

        <skiaforms:SKCanvasView x:Name="canvasView"
                                Grid.Row="0"
                                PaintSurface="OnCanvasViewPaintSurface" />

        <Picker x:Name="tileModePicker" 
                Grid.Row="1"
                Title="Shader Tile Mode" 
                Margin="10"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.ItemsSource>
                <x:Array Type="{x:Type skia:SKShaderTileMode}">
                    <x:Static Member="skia:SKShaderTileMode.Clamp" />
                    <x:Static Member="skia:SKShaderTileMode.Repeat" />
                    <x:Static Member="skia:SKShaderTileMode.Mirror" />
                </x:Array>
            </Picker.ItemsSource>

            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>
    </Grid>
</ContentPage>
```

代码隐藏文件的颜色使用径向渐变整个画布。 渐变中心设置为在画布的中心和半径设置为 100 像素。 只需两种颜色，黑色或白色包括渐变：

```csharp
public partial class RadialGradientPage : ContentPage
{
    public RadialGradientPage ()
    {
        InitializeComponent ();
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

        SKShaderTileMode tileMode =
            (SKShaderTileMode)(tileModePicker.SelectedIndex == -1 ?
                                        0 : tileModePicker.SelectedItem);

        using (SKPaint paint = new SKPaint())
        {
            paint.Shader = SKShader.CreateRadialGradient(
                                new SKPoint(info.Rect.MidX, info.Rect.MidY),
                                100,
                                new SKColor[] { SKColors.Black, SKColors.White },
                                null,
                                tileMode);

            canvas.DrawRect(info.Rect, paint);
        }
    }
}
```

此代码创建具有中心，逐渐淡出为白色 100 像素从中心的黑色的渐变。 超出该 radius 会发生什么情况取决于`SKShaderTileMode`参数：

[![径向渐变](circular-gradients-images/RadialGradient.png "径向渐变")](circular-gradients-images/RadialGradient-Large.png#lightbox)

在所有三种情况下，渐变填充画布。 在左侧的 iOS 屏幕上超出半径渐变继续执行上一种颜色为白色。 这就是结果的`SKShaderTileMode.Clamp`。 Android 屏幕显示`SKShaderTileMode.Repeat`以下内容的效果:从中心100像素开始, 渐变将再次开始, 第一种颜色为黑色。 渐变重复 radius 每 100 个像素。 

在右侧显示的通用 Windows 平台屏幕如何`SKShaderTileMode.Mirror`导致到备用 directions 渐变。 第一个渐变是从中心的黑色到在一个半径为 100 像素的白色。 下一步是从 100 像素 radius 在 200 像素 radius 黑色到白色，再次反转的下一步的渐变。

径向渐变中，可以使用两个以上的颜色。 **彩虹弧线渐变**示例将创建对应的彩虹和红色，结束颜色的八种颜色的数组以及八个位置值的数组：

```csharp
public class RainbowArcGradientPage : ContentPage
{
    public RainbowArcGradientPage ()
    {
        Title = "Rainbow Arc Gradient";

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
            float rainbowWidth = Math.Min(info.Width, info.Height) / 4f;

            // Center of arc and gradient is lower-right corner
            SKPoint center = new SKPoint(info.Width, info.Height);

            // Find outer, inner, and middle radius
            float outerRadius = Math.Min(info.Width, info.Height);
            float innerRadius = outerRadius - rainbowWidth;
            float radius = outerRadius - rainbowWidth / 2;

            // Calculate the colors and positions
            SKColor[] colors = new SKColor[8];
            float[] positions = new float[8];

            for (int i = 0; i < colors.Length; i++)
            {
                colors[i] = SKColor.FromHsl(i * 360f / 7, 100, 50);
                positions[i] = (i + (7f - i) * innerRadius / outerRadius) / 7f;
            }

            // Create sweep gradient based on center and outer radius
            paint.Shader = SKShader.CreateRadialGradient(center, 
                                                         outerRadius, 
                                                         colors, 
                                                         positions, 
                                                         SKShaderTileMode.Clamp);
            // Draw a circle with a wide line
            paint.Style = SKPaintStyle.Stroke;
            paint.StrokeWidth = rainbowWidth;

            canvas.DrawCircle(center, radius, paint);
        }
    }
}
```

假设的宽度最小值和画布的高度为 1000，这意味着，`rainbowWidth`值为 250 个字符。 `outerRadius`和`innerRadius`值设置为 1000年 750，分别。 这些值用于计算`positions`数组; 从 0.75f 八个值的范围为 1。 `radius`值用于描画圆。 875 意味着 250 像素笔划宽度扩展 750 像素为单位的半径和 1000年像素为单位的半径之间的值：

[![出喷薄彩虹弧线渐变](circular-gradients-images/RainbowArcGradient.png "出喷薄彩虹弧线渐变")](circular-gradients-images/RainbowArcGradient-Large.png#lightbox)

如果使用此渐变填充整个画布，会看到它是红色的内部半径范围之内。 这是因为`positions`数组不以 0 开头。 第一种颜色用于 0 到第一个数组值的偏移量。 渐变也是红色超出外部 radius 的。 这就是结果的`Clamp`平铺模式。 因为渐变用于描画粗线，看不到这些红色区域。

## <a name="radial-gradients-for-masking"></a>为需要进行掩码的径向渐变

线性渐变，如径向渐变可以合并透明或部分透明的颜色。 此功能可用于名为的进程_屏蔽_，从而隐藏图像地突出显示的图像的另一个部分的一部分。

**径向渐变掩码**页显示了示例。 该程序将加载一个资源位图。 `CENTER`和`RADIUS`字段已确定位图的检查和引用应突出显示的区域。 `PaintSurface`处理程序首先计算要显示位图的矩形中，然后显示该矩形中：

```csharp
public class RadialGradientMaskPage : ContentPage
{
    SKBitmap bitmap = BitmapExtensions.LoadBitmapResource(
        typeof(RadialGradientMaskPage),
        "SkiaSharpFormsDemos.Media.MountainClimbers.jpg");

    static readonly SKPoint CENTER = new SKPoint(180, 300);
    static readonly float RADIUS = 120;

    public RadialGradientMaskPage ()
    {
        Title = "Radial Gradient Mask";

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
                                new SKColor[] { SKColors.Transparent,
                                                SKColors.White },
                                new float[] { 0.6f, 1 },
                                SKShaderTileMode.Clamp);

            // Display rectangle using that gradient
            canvas.DrawRect(rect, paint);
        }
    }
}
```

绘制位图后, 一些简单的代码将为`CENTER`并`RADIUS`到`center`和`radius`，其中引用位图已扩展并显示移动中突出显示的区域。 这些值用于创建该中心和 radius 与径向渐变。 两种颜色开始透明在中心和半径的第一个的 60%。 渐变然后渐变为白色：

[![径向渐变掩码](circular-gradients-images/RadialGradientMask.png "径向渐变掩码")](circular-gradients-images/RadialGradientMask-Large.png#lightbox)

此方法不屏蔽位图的最佳方式。 问题在于掩码通常具有一种颜色的白皮书，选择是为了与画布的背景相匹配。 如果在后台是一些其他的颜色&mdash;或可能是渐变本身&mdash;不匹配。 屏蔽的更好方法本文中所示[SkiaSharp Porter Duff 混合模式](../blend-modes/porter-duff.md)。

## <a name="radial-gradients-for-specular-highlights"></a>有关反射高光的径向渐变

当光线照射到圆角的图面时，反映了在多个方向光，但光的某些直接在查看器的眼睛退回。 这通常会调用图面上的模糊的白色区域的外观_高光_。

在三维图形中，反射高光通常导致用来确定浅路径和明暗度的算法。 在二维图形中，反射高光有时添加建议一个三维表面之上的外观。 镜面高光可以将转换为倒圆角的红色球平面红色圆圈。

**径向镜面高光**页面使用径向渐变确切实现该目的。 `PaintSurface`处理程序才会通过计算的圆圈和两个半径`SKPoint`值&mdash;`center`和`offCenter`这就是在中心和圆形的左上边缘之间的中间位置：

```csharp
public class RadialSpecularHighlightPage : ContentPage
{
    public RadialSpecularHighlightPage()
    {
        Title = "Radial Specular Highlight";

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

        float radius = 0.4f * Math.Min(info.Width, info.Height);
        SKPoint center = new SKPoint(info.Rect.MidX, info.Rect.MidY);
        SKPoint offCenter = center - new SKPoint(radius / 2, radius / 2);

        using (SKPaint paint = new SKPaint())
        {
            paint.Shader = SKShader.CreateRadialGradient(
                                offCenter,
                                radius / 2,
                                new SKColor[] { SKColors.White, SKColors.Red },
                                null,
                                SKShaderTileMode.Clamp);

            canvas.DrawCircle(center, radius, paint);
        }
    }
}
```

`CreateRadialGradient`调用创建的渐变的开始处的`offCenter`与白色和结尾的一半 radius 距离处的红色的点。 如下所示：

[![径向高光](circular-gradients-images/RadialSpecularHighlight.png "径向高光")](circular-gradients-images/RadialSpecularHighlight-Large.png#lightbox)

如果您仔细查看此渐变，可能会决定它有缺陷。 渐变围绕某个特定点，并且你可能希望它就更不对称，以反映在圆角的图面。 在这种情况下，您可能倾向于高光的部分中，如下所示[**圆锥梯度的反射高光**](#conical-gradients-for-specular-highlights)。

## <a name="the-sweep-gradient"></a>扫描渐变

[ `CreateSweepGradient` ](xref:SkiaSharp.SKShader.CreateSweepGradient(SkiaSharp.SKPoint,SkiaSharp.SKColor[],System.Single[]))方法具有的所有渐变创建方法最简单的语法：

```csharp
public static SKShader CreateSweepGradient (SKPoint center, 
                                            SKColor[] colors, 
                                            Single[] colorPos)
```

它是只是一个中心、 颜色、 数组和位置的颜色。 渐变的中心点右侧开始，并扫描 360 度围绕中心顺时针旋转。 请注意，没有任何`SKShaderTileMode`参数。

一个[ `CreateSweepGradient` ](xref:SkiaSharp.SKShader.CreateSweepGradient(SkiaSharp.SKPoint,SkiaSharp.SKColor[],System.Single[],SkiaSharp.SKMatrix))矩阵转换参数的重载是也可用。 若要更改的起始点的渐变到可以应用旋转转换。 此外可以应用一个比例转换，以便从顺时针方向更改为逆时针旋转。

**扫描渐变**页面使用扫描渐变颜色的圆形，50 像素的笔划宽度：

[![扫描中渐变](circular-gradients-images/SweepGradient.png "扫描渐变")](circular-gradients-images/SweepGradient-Large.png#lightbox)

`SweepGradientPage`类定义的八个的颜色的不同色调值数组。 请注意，数组开头和结尾 red （色调值为 0 或 360），将显示在最右侧的屏幕截图中：

```csharp
public class SweepGradientPage : ContentPage
{
    bool drawBackground;

    public SweepGradientPage ()
    {
        Title = "Sweep Gradient";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;

        TapGestureRecognizer tap = new TapGestureRecognizer();
        tap.Tapped += (sender, args) =>
        {
            drawBackground ^= true;
            canvasView.InvalidateSurface();
        };
        canvasView.GestureRecognizers.Add(tap);
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPaint paint = new SKPaint())
        {
            // Define an array of rainbow colors
            SKColor[] colors = new SKColor[8];

            for (int i = 0; i < colors.Length; i++)
            {
                colors[i] = SKColor.FromHsl(i * 360f / 7, 100, 50);
            }

            SKPoint center = new SKPoint(info.Rect.MidX, info.Rect.MidY);

            // Create sweep gradient based on center of canvas
            paint.Shader = SKShader.CreateSweepGradient(center, colors, null);

            // Draw a circle with a wide line
            const int strokeWidth = 50;
            paint.Style = SKPaintStyle.Stroke;
            paint.StrokeWidth = strokeWidth;

            float radius = (Math.Min(info.Width, info.Height) - strokeWidth) / 2;
            canvas.DrawCircle(center, radius, paint);

            if (drawBackground)
            {
                // Draw the gradient on the whole canvas
                paint.Style = SKPaintStyle.Fill;
                canvas.DrawRect(info.Rect, paint);
            }
        }
    }
}
```

该程序还实现`TapGestureRecognizer`这样的末尾的一些代码`PaintSurface`处理程序。 此代码使用的是同一渐变填充画布：

[![扫描中渐变完整](circular-gradients-images/SweepGradientFull.png "扫描渐变完整")](circular-gradients-images/SweepGradientFull-Large.png#lightbox)

这些屏幕截图演示了渐变填充的领域着色由它。 如果尚未开始和结尾的相同的颜色渐变，将同步到中心点的右侧。

## <a name="the-two-point-conical-gradient"></a>两个点圆锥渐变

[ `CreateTwoPointConicalGradient` ](xref:SkiaSharp.SKShader.CreateTwoPointConicalGradient(SkiaSharp.SKPoint,System.Single,SkiaSharp.SKPoint,System.Single,SkiaSharp.SKColor[],System.Single[],SkiaSharp.SKShaderTileMode))方法具有以下语法：

```csharp
public static SKShader CreateTwoPointConicalGradient (SKPoint startCenter, 
                                                      Single startRadius, 
                                                      SKPoint endCenter, 
                                                      Single endRadius, 
                                                      SKColor[] colors, 
                                                      Single[] colorPos, 
                                                      SKShaderTileMode mode)
```

参数以中心点和称为的两个圆的半径开头_启动_圆并_最终_圆圈。 剩余的三个参数均为适用于`CreateLinearGradient`和`CreateRadialGradient`。 一个[ `CreateTwoPointConicalGradient` ](xref:SkiaSharp.SKShader.CreateTwoPointConicalGradient(SkiaSharp.SKPoint,System.Single,SkiaSharp.SKPoint,System.Single,SkiaSharp.SKColor[],System.Single[],SkiaSharp.SKShaderTileMode,SkiaSharp.SKMatrix))重载包含矩阵转换。

渐变的起止日期开始圆端圆圈。 `SKShaderTileMode`参数控制会发生什么情况不在两个圆。 两个点圆锥渐变是完全不填充区域的唯一渐变。 如果两个圆有相同的半径，渐变被限制到一个具有相同的圆圈直径的宽度的矩形。 如果两个圆有不同的半径，渐变窗体锥形区域。

很可能会想要尝试的两个点圆锥渐变，因此**圆锥渐变**页派生`InteractivePage`以允许两个触摸点，若要移动的两个圆的半径：

```xaml
<local:InteractivePage xmlns="http://xamarin.com/schemas/2014/forms"
                       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                       xmlns:local="clr-namespace:SkiaSharpFormsDemos"
                       xmlns:skia="clr-namespace:SkiaSharp;assembly=SkiaSharp"
                       xmlns:skiaforms="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
                       xmlns:tt="clr-namespace:TouchTracking"
                       x:Class="SkiaSharpFormsDemos.Effects.ConicalGradientPage"
                       Title="Conical Gradient">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="*" />
            <RowDefinition Height="Auto" />
        </Grid.RowDefinitions>
        
        <Grid BackgroundColor="White"
              Grid.Row="0">
            <skiaforms:SKCanvasView x:Name="canvasView"
                                    PaintSurface="OnCanvasViewPaintSurface" />
            <Grid.Effects>
                <tt:TouchEffect Capture="True"
                                TouchAction="OnTouchEffectAction" />
            </Grid.Effects>
        </Grid>

        <Picker x:Name="tileModePicker" 
                Grid.Row="1"
                Title="Shader Tile Mode" 
                Margin="10"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.ItemsSource>
                <x:Array Type="{x:Type skia:SKShaderTileMode}">
                    <x:Static Member="skia:SKShaderTileMode.Clamp" />
                    <x:Static Member="skia:SKShaderTileMode.Repeat" />
                    <x:Static Member="skia:SKShaderTileMode.Mirror" />
                </x:Array>
            </Picker.ItemsSource>

            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>
    </Grid>
</local:InteractivePage>
```

代码隐藏文件定义了两个`TouchPoint`具有 50 到 100 之间的固定半径的对象：

```csharp
public partial class ConicalGradientPage : InteractivePage
{
    const int RADIUS1 = 50;
    const int RADIUS2 = 100;

    public ConicalGradientPage ()
    {
        touchPoints = new TouchPoint[2];

        touchPoints[0] = new TouchPoint
        {
            Center = new SKPoint(100, 100),
            Radius = RADIUS1
        };

        touchPoints[1] = new TouchPoint
        {
            Center = new SKPoint(300, 300),
            Radius = RADIUS2
        };

        InitializeComponent();
        baseCanvasView = canvasView;
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

        SKColor[] colors = { SKColors.Red, SKColors.Green, SKColors.Blue };
        SKShaderTileMode tileMode = 
            (SKShaderTileMode)(tileModePicker.SelectedIndex == -1 ? 
                                        0 : tileModePicker.SelectedItem);

        using (SKPaint paint = new SKPaint())
        {
            paint.Shader = SKShader.CreateTwoPointConicalGradient(touchPoints[0].Center,
                                                                  RADIUS1,
                                                                  touchPoints[1].Center,
                                                                  RADIUS2,
                                                                  colors,
                                                                  null,
                                                                  tileMode);
            canvas.DrawRect(info.Rect, paint);
        }

        // Display the touch points here rather than by TouchPoint
        using (SKPaint paint = new SKPaint())
        {
            paint.Style = SKPaintStyle.Stroke;
            paint.Color = SKColors.Black;
            paint.StrokeWidth = 3;

            foreach (TouchPoint touchPoint in touchPoints)
            {
                canvas.DrawCircle(touchPoint.Center, touchPoint.Radius, paint);
            }
        }
    }
}
```

`colors`数组是红色、 绿色和蓝色。 靠近末尾部分给代码`PaintSurface`处理程序如黑色圆圈，以便不影响渐变绘制的两个触摸点。

请注意，`DrawRect`调用使用渐变颜色整个画布。 一般情况下，但是，很多画布仍未通过渐变上色。 下面是显示三个可能的配置程序：

[![圆锥渐变](circular-gradients-images/ConicalGradient.png "圆锥渐变")](circular-gradients-images/ConicalGradient-Large.png#lightbox)

在左侧的 iOS 屏幕显示的效果`SKShaderTileMode`设置的`Clamp`。 渐变开始边缘的相反端到第二个圆最接近的较小圆圈内的红色。 `Clamp`值还会导致 red 继续圆锥体的点。 渐变结束用蓝色的外边缘的较大圆圈，以最接近的第一个圆圈，但会继续在用蓝色该圆圈内及更高版本。

Android 屏幕是类似，但`SKShaderTileMode`的`Repeat`。 现在，梯度第一个圆圈内开始和结束之外的第二个圆圈更清晰。 `Repeat`设置会导致再次重复使用的红色大圆圈内的渐变。

UWP 屏幕显示较小的圆形大圆圈内完全移动时，会发生什么情况。 梯度停止作为圆锥体和改为将填充整个区域。 效果类似于径向渐变，但如果在较大圆内不完全居中较小的圆形，则为非对称。

当一个圆圈嵌套在另一个，但它非常适合于反射的突出显示时，可能会怀疑实用用途的渐变。

## <a name="conical-gradients-for-specular-highlights"></a>有关反射高光圆锥渐变

在本文前面你已了解如何在使用径向渐变创建镜面高光。 此外可以使用两个点圆锥渐变，出于此目的，并需要它的外观：

[![圆锥高光](circular-gradients-images/ConicalSpecularHighlight.png "圆锥高光")](circular-gradients-images/ConicalSpecularHighlight-Large.png#lightbox)

非对称外观更好地建议对象的圆角的图面。 

中的绘图代码**圆锥镜面高光**页是与相同**径向镜面高光**页除外着色器：

```csharp
public class ConicalSpecularHighlightPage : ContentPage
{
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        ···
        using (SKPaint paint = new SKPaint())
        {
            paint.Shader = SKShader.CreateTwoPointConicalGradient(
                                offCenter,
                                1,
                                center,
                                radius,
                                new SKColor[] { SKColors.White, SKColors.Red },
                                null,
                                SKShaderTileMode.Clamp);

            canvas.DrawCircle(center, radius, paint);
        }
    }
}
```

两个圆具有的中心`offCenter`和`center`。 中心圆`center`涵盖了整个球，但中心圆的半径与关联`offCenter`只是一个像素的半径。 渐变有效地在该点开始和结束边缘的球。

## <a name="related-links"></a>相关链接

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
