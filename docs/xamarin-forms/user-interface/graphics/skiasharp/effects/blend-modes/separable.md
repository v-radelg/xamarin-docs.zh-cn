---
title: 可分离的混合模式
description: 使用可分离的混合模式更改红色、 绿色和蓝色颜色。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 66D1A537-A247-484E-B5B9-FBCB7838FBE9
author: davidbritch
ms.author: dabritch
ms.date: 08/23/2018
ms.openlocfilehash: 4fb5bd1e883adc3be89bde7cc0e1529e77165247
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2019
ms.locfileid: "68655044"
---
# <a name="the-separable-blend-modes"></a>可分离的混合模式

[![下载示例](~/media/shared/download.png)下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

本文中所示[ **SkiaSharp Porter Duff 混合模式**](porter-duff.md)，Porter Duff 的混合模式通常执行剪切操作。 可分离的混合模式有不同。 可分离模式更改图像的单独的红色、 绿色和蓝色颜色组件。 可分离的混合模式可以混合使用颜色来演示的红色、 绿色和蓝色的组合是确实白色：

![基本色](separable-images/SeparableSample.png "种基本色")

## <a name="lighten-and-darken-two-ways"></a>淡化和加深两种方法 

它是很常见的是某种程度上的位图太暗或太亮。 可以使用可分离的混合模式变淡或变暗 imabe。  实际上，两个中的可分离 blend 模式[ `SKBlendMode` ](xref:SkiaSharp.SKBlendMode)枚举命名`Lighten`和`Darken`。 

中演示了这两种模式**变淡和变暗**页。 XAML 文件实例化两个`SKCanvasView`对象和两个`Slider`视图：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Effects.LightenAndDarkenPage"
             Title="Lighten and Darken">
    <StackLayout>
        <skia:SKCanvasView x:Name="lightenCanvasView"
                           VerticalOptions="FillAndExpand"
                           PaintSurface="OnCanvasViewPaintSurface" />

        <Slider x:Name="lightenSlider"
                Margin="10"
                ValueChanged="OnSliderValueChanged" />

        <skia:SKCanvasView x:Name="darkenCanvasView"
                           VerticalOptions="FillAndExpand"
                           PaintSurface="OnCanvasViewPaintSurface" />

        <Slider x:Name="darkenSlider"
                Margin="10"
                ValueChanged="OnSliderValueChanged" />
    </StackLayout>
</ContentPage>
```

第一个`SKCanvasView`并`Slider`演示`SKBlendMode.Lighten`，并将第二对演示`SKBlendMode.Darken`。 这两个`Slider`视图都共享相同`ValueChanged`处理程序和两个`SKCanvasView`共用同一个`PaintSurface`处理程序。 这两个事件处理程序检查哪些对象将触发此事件：

```csharp
public partial class LightenAndDarkenPage : ContentPage
{
    SKBitmap bitmap = BitmapExtensions.LoadBitmapResource(
                typeof(SeparableBlendModesPage),
                "SkiaSharpFormsDemos.Media.Banana.jpg");

    public LightenAndDarkenPage ()
    {
        InitializeComponent ();
    }

    void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
    {
        if ((Slider)sender == lightenSlider)
        {
            lightenCanvasView.InvalidateSurface();
        }
        else
        {
            darkenCanvasView.InvalidateSurface();
        }
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Find largest size rectangle in canvas
        float scale = Math.Min((float)info.Width / bitmap.Width,
                               (float)info.Height / bitmap.Height);
        SKRect rect = SKRect.Create(scale * bitmap.Width, scale * bitmap.Height);
        float x = (info.Width - rect.Width) / 2;
        float y = (info.Height - rect.Height) / 2;
        rect.Offset(x, y);

        // Display bitmap
        canvas.DrawBitmap(bitmap, rect);

        // Display gray rectangle with blend mode
        using (SKPaint paint = new SKPaint())
        {
            if ((SKCanvasView)sender == lightenCanvasView)
            {
                byte value = (byte)(255 * lightenSlider.Value);
                paint.Color = new SKColor(value, value, value);
                paint.BlendMode = SKBlendMode.Lighten;
            }
            else
            {
                byte value = (byte)(255 * (1 - darkenSlider.Value));
                paint.Color = new SKColor(value, value, value);
                paint.BlendMode = SKBlendMode.Darken;
            }

            canvas.DrawRect(rect, paint);
        }
    }
}
```

`PaintSurface`处理程序计算适用于位图的矩形。 该处理程序将显示该位图，然后显示的对位图使用矩形`SKPaint`对象，其`BlendMode`属性设置为`SKBlendMode.Lighten`或`SKBlendMode.Darken`。 `Color`属性是基于灰影`Slider`。 有关`Lighten`模式下，颜色范围是从黑色到白色，但对于`Darken`其范围为从白色到黑色的模式。

从左到右的屏幕截图显示变大`Slider`顶部图像获取较浅和底部图像获取较暗的值：

[![淡化和加深](separable-images/LightenAndDarken.png "淡化和加深")](separable-images/LightenAndDarken-Large.png#lightbox)

此程序演示使用分离混合模式的正常方式:目标是某种类型的图像, 通常是位图。 源是使用显示的矩形`SKPaint`对象，其`BlendMode`属性设置为可分离混合模式。 矩形 （因为它是此处） 可以是纯色或渐变。 透明度是_不_通常适用于可分离的混合模式。

在试验与此程序时，你会发现这两个混合模式不变淡并且均匀使图像变暗。 相反，`Slider`似乎设置某种类型的阈值。 例如，当您增加`Slider`为`Lighten`模式下，图像的较暗的区域获得 light 第一个较浅区域保持不变。

有关`Lighten`模式下，如果目标像素的 RGB 颜色值 (灾难恢复，Dg，Db)，源像素的颜色 (Sr，Sg，Sb)，则输出为 （或 Og，Ob） 按以下方式计算：

 `Or = max(Dr, Sr)` `Og = max(Dg, Sg)`
 `Ob = max(Db, Sb)`

另外，对于红色、 绿色和蓝色，结果是目标和源中的较大的。 这将产生第一次变革目标深色区域的效果。

`Darken`模式，类似，但结果是较小的目标和源：

 `Or = min(Dr, Sr)` `Og = min(Dg, Sg)`
 `Ob = min(Db, Sb)`

红色、 绿色和蓝色组件是每个单独处理的这正是这些混合模式被称为_可分离_混合模式。 出于此原因，缩写**Dc**并**Sc**可用于目标和源颜色和理解，计算将应用于每个红色、 绿色和蓝色组件分开。

下表显示了所有可分离混合模式下与它们的用途的简短说明。 第二列显示了生成未更改的源颜色：

| 混合模式   | 无更改 | 操作 |
| ------------ | --------- | --------- |
| `Plus`       | 黑色     | 通过添加颜色变亮:Sc + Dc |
| `Modulate`   | 白色     | 通过按颜色加深:Sc·Dc | 
| `Screen`     | 黑色     | 补充的产品:Sc + Dc &ndash; Sc ·台 |
| `Overlay`    | 灰色      | 函数的反函数 `HardLight` |
| `Darken`     | 白色     | 最小的颜色： 最小值 （Sc、 Dc） |
| `Lighten`    | 黑色     | 最大的颜色： 最大值 （Sc、 Dc） |
| `ColorDodge` | 黑色     | 使更亮，基于源的目标 |
| `ColorBurn`  | 白色     | 基于源的目标会变暗 | 
| `HardLight`  | 灰色      | 类似于 harsh 聚焦的效果 |
| `SoftLight`  | 灰色      | 类似于软聚焦的效果 | 
| `Difference` | 黑色     | 从较浅的减去较暗的:Abs (Dc &ndash; Sc) | 
| `Exclusion`  | 黑色     | 类似于`Difference`但较低的对比度 |
| `Multiply`   | 白色     | 通过按颜色加深:Sc·Dc |

可以在 w3c 中找到更详细的算法[**合成和混合级别 1** ](https://www.w3.org/TR/compositing-1/)规范和 Skia [ **SkBlendMode 引用**](https://skia.org/user/api/SkBlendMode_Reference)，但这些两个源中的表示法不相同。 请记住`Plus`Porter Duff 混合模式，通常件和`Modulate`不是 W3C 规范的一部分。

如果源是透明的然后为所有可分离混合模式除`Modulate`，混合模式下不起作用。 正如您看到更早版本，`Modulate`混合模式下包含该乘法运算中的 alpha 通道。 否则为`Modulate`具有相同的效果`Multiply`。 

请注意，名为两种模式`ColorDodge`和`ColorBurn`。 单词_避开_并_刻录_源自于摄影暗室实践。 放大器照射通过为负，从而使照片打印。 无光，则输出为白色。 打印获取变暗详细光是打印较长的时间。 打印制造商通常用于手动或小对象阻止某些侵扰上打印，使较浅此区域的特定部分光线。 这称为_逃避_。 相反，使用它 （或阻止大多数光的手） 一个孔的不透明材料无法用于直接中变暗，它名为某个特定点的详细 light_刻录_。

**避开和 Burn**计划是非常类似于**变淡和变暗**。 XAML 文件是结构化相同但不同的元素名称和代码隐藏文件同样非常相似，但这两个混合模式的效果是完全不同：

[![避开和 Burn](separable-images/DodgeAndBurn.png "避开和 Burn")](separable-images/DodgeAndBurn-Large.png#lightbox)

对于小型`Slider`值，`Lighten`模式变浅深色区域第一次，而`ColorDodge`变浅更均匀。

图像处理应用程序通常允许逃避和刻录限制到特定区域，就像在暗室中。 此操作可以完成通过渐变，或使用不同的灰影的位图。

## <a name="exploring-the-separable-blend-modes"></a>探索可分离的混合模式

**Blend 的可分离模式**页面允许您检查所有可分离的混合模式。 它显示位图目标和使用的混合模式之一的有色的矩形源。 

XAML 文件定义`Picker`（若要选择混合模式） 和四个滑块。 前三个滑块，可以设置源的红色、 绿色和蓝色组件。 第四个滑块旨在通过设置灰色阴影覆盖这些值。 无法识别各个滑块，但颜色指示其功能：

```xaml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp;assembly=SkiaSharp"
             xmlns:skiaviews="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Effects.SeparableBlendModesPage"
             Title="Separable Blend Modes">

    <StackLayout>
        <skiaviews:SKCanvasView x:Name="canvasView"
                                VerticalOptions="FillAndExpand"
                                PaintSurface="OnCanvasViewPaintSurface" />

        <Picker x:Name="blendModePicker"
                Title="Blend Mode"
                Margin="10, 0"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.ItemsSource>
                <x:Array Type="{x:Type skia:SKBlendMode}">
                    <x:Static Member="skia:SKBlendMode.Plus" />
                    <x:Static Member="skia:SKBlendMode.Modulate" />
                    <x:Static Member="skia:SKBlendMode.Screen" />
                    <x:Static Member="skia:SKBlendMode.Overlay" />
                    <x:Static Member="skia:SKBlendMode.Darken" />
                    <x:Static Member="skia:SKBlendMode.Lighten" />
                    <x:Static Member="skia:SKBlendMode.ColorDodge" />
                    <x:Static Member="skia:SKBlendMode.ColorBurn" />
                    <x:Static Member="skia:SKBlendMode.HardLight" />
                    <x:Static Member="skia:SKBlendMode.SoftLight" />
                    <x:Static Member="skia:SKBlendMode.Difference" />
                    <x:Static Member="skia:SKBlendMode.Exclusion" />
                    <x:Static Member="skia:SKBlendMode.Multiply" />
                </x:Array>
            </Picker.ItemsSource>

            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>

        <Slider x:Name="redSlider"
                MinimumTrackColor="Red"
                MaximumTrackColor="Red"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <Slider x:Name="greenSlider"
                MinimumTrackColor="Green"
                MaximumTrackColor="Green"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <Slider x:Name="blueSlider"
                MinimumTrackColor="Blue"
                MaximumTrackColor="Blue"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <Slider x:Name="graySlider"
                MinimumTrackColor="Gray"
                MaximumTrackColor="Gray"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <Label x:Name="colorLabel"
               HorizontalTextAlignment="Center" />

    </StackLayout>
</ContentPage>
```

代码隐藏文件加载位图资源之一，并绘制它两次，一次在画布的上半部分中，再次在底部画布的下半部分：

```csharp
public partial class SeparableBlendModesPage : ContentPage
{
    SKBitmap bitmap = BitmapExtensions.LoadBitmapResource(
                        typeof(SeparableBlendModesPage),
                        "SkiaSharpFormsDemos.Media.Banana.jpg"); 

    public SeparableBlendModesPage()
    {
        InitializeComponent();
    }

    void OnPickerSelectedIndexChanged(object sender, EventArgs args)
    {
        canvasView.InvalidateSurface();
    }

    void OnSliderValueChanged(object sender, ValueChangedEventArgs e)
    {
        if (sender == graySlider)
        {
            redSlider.Value = greenSlider.Value = blueSlider.Value = graySlider.Value;
        }

        colorLabel.Text = String.Format("Color = {0:X2} {1:X2} {2:X2}",
                                        (byte)(255 * redSlider.Value),
                                        (byte)(255 * greenSlider.Value),
                                        (byte)(255 * blueSlider.Value));

        canvasView.InvalidateSurface();
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Draw bitmap in top half
        SKRect rect = new SKRect(0, 0, info.Width, info.Height / 2);
        canvas.DrawBitmap(bitmap, rect, BitmapStretch.Uniform);

        // Draw bitmap in bottom halr
        rect = new SKRect(0, info.Height / 2, info.Width, info.Height);
        canvas.DrawBitmap(bitmap, rect, BitmapStretch.Uniform);

        // Get values from XAML controls
        SKBlendMode blendMode =
            (SKBlendMode)(blendModePicker.SelectedIndex == -1 ?
                                        0 : blendModePicker.SelectedItem);

        SKColor color = new SKColor((byte)(255 * redSlider.Value),
                                    (byte)(255 * greenSlider.Value),
                                    (byte)(255 * blueSlider.Value));

        // Draw rectangle with blend mode in bottom half
        using (SKPaint paint = new SKPaint())
        {
            paint.Color = color;
            paint.BlendMode = blendMode;
            canvas.DrawRect(rect, paint);
        }
    }
}
```

靠近末尾部分给`PaintSurface`上使用所选的混合模式和所选的颜色的第二个位图绘制处理程序中，一个矩形。 您可以比较底部与原始位图顶部修改后的位图：

[![可分离的混合模式](separable-images/SeparableBlendModes.png "可分离的混合模式")](separable-images/SeparableBlendModes-Large.png#lightbox)

## <a name="additive-and-subtractive-primary-colors"></a>累加性和删减主色

**种基本色**页绘制红色、 绿色和蓝色的三个重叠圆圈：

[![累加性种基本色](separable-images/PrimaryColors-Additive.png "累加性的主要颜色")](separable-images/PrimaryColors-Additive.png#lightbox)

这些是累加性的主要颜色。 任何两个的组合会产生青、 品红和黄，和的所有三个组合为白色。

与绘制这些三个圆圈`SKBlendMode.Plus`模式，但您还可以使用`Screen`， `Lighten`，或`Difference`为相同的效果。 下面是该程序：

```csharp
public class PrimaryColorsPage : ContentPage
{
    bool isSubtractive;

    public PrimaryColorsPage ()
    {
        Title = "Primary Colors";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;

        // Switch between additive and subtractive primaries at tap
        TapGestureRecognizer tap = new TapGestureRecognizer();
        tap.Tapped += (sender, args) =>
        {
            isSubtractive ^= true;
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

        SKPoint center = new SKPoint(info.Rect.MidX, info.Rect.MidY);
        float radius = Math.Min(info.Width, info.Height) / 4;
        float distance = 0.8f * radius;     // from canvas center to circle center
        SKPoint center1 = center + 
            new SKPoint(distance * (float)Math.Cos(9 * Math.PI / 6),
                        distance * (float)Math.Sin(9 * Math.PI / 6));
        SKPoint center2 = center +
            new SKPoint(distance * (float)Math.Cos(1 * Math.PI / 6),
                        distance * (float)Math.Sin(1 * Math.PI / 6));
        SKPoint center3 = center +
            new SKPoint(distance * (float)Math.Cos(5 * Math.PI / 6),
                        distance * (float)Math.Sin(5 * Math.PI / 6));

        using (SKPaint paint = new SKPaint())
        {
            if (!isSubtractive)
            {
                paint.BlendMode = SKBlendMode.Plus; 
                System.Diagnostics.Debug.WriteLine(paint.BlendMode);

                paint.Color = SKColors.Red;
                canvas.DrawCircle(center1, radius, paint);

                paint.Color = SKColors.Lime;    // == (00, FF, 00)
                canvas.DrawCircle(center2, radius, paint);

                paint.Color = SKColors.Blue;
                canvas.DrawCircle(center3, radius, paint);
            }
            else
            {
                paint.BlendMode = SKBlendMode.Multiply
                System.Diagnostics.Debug.WriteLine(paint.BlendMode);

                paint.Color = SKColors.Cyan;
                canvas.DrawCircle(center1, radius, paint);

                paint.Color = SKColors.Magenta;
                canvas.DrawCircle(center2, radius, paint);

                paint.Color = SKColors.Yellow;
                canvas.DrawCircle(center3, radius, paint);
            }
        }
    }
}
```

该程序包括`TabGestureRecognizer`。 点击或单击屏幕时，该程序使用`SKBlendMode.Multiply`以显示三个删减主副本：

[![删减种基本色](separable-images/PrimaryColors-Subtractive.png "减主色")](separable-images/PrimaryColors-Subtractive-Large.png#lightbox)

`Darken`模式也适用于此相同的效果。

## <a name="related-links"></a>相关链接

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
