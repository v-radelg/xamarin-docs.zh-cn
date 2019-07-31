---
title: 非可分离的混合模式
description: 使用非可分离的混合模式更改色调、 饱和度和亮度。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 97FA2730-87C0-4914-8C9F-C64A02CF9EEF
author: davidbritch
ms.author: dabritch
ms.date: 08/23/2018
ms.openlocfilehash: 9054539b08da89c0f7d8a93150866fb1b41e63f1
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2019
ms.locfileid: "68642778"
---
# <a name="the-non-separable-blend-modes"></a>非可分离的混合模式

[![下载示例](~/media/shared/download.png)下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

本文中所示[ **SkiaSharp 可分离混合模式**](separable.md)，可分离的混合模式单独执行的红色、 绿色和蓝色通道上的操作。 非可分离的混合模式不适用。 对颜色的色调、 饱和度和亮度级别进行操作，非可分离的混合模式可以更改颜色以有趣的方式：

![非可分离示例](non-separable-images/NonSeparableSample.png "非可分离示例")

## <a name="the-hue-saturation-luminosity-model"></a>色调-饱和度-亮度模型

若要了解非可分离的混合模式，它是所需的目标和源像素视为色调-饱和度-亮度模型中的颜色。 （亮度是也称为亮度。）

HSL 颜色模型一文中介绍过[**与 Xamarin.Forms 集成**](../../basics/integration.md)和一个示例程序，该文章中的允许使用 HSL 颜色的试验。 您可以创建`SKColor`值使用具有静态色调、 饱和度和亮度值[ `SKColor.FromHsl` ](xref:SkiaSharp.SKColor.FromHsl*)方法。

色调表示颜色的主波长。 色调值介于0到360之间, 并循环到累加和 subtractive 主副本:红色为值 0, 黄色为 60, 绿色表示 120, 青色表示 180, 蓝色表示 240, 洋红色为 300, 循环恢复为红色。

如果没有主色&mdash;例如，颜色是白色或黑色或灰影&mdash;然后色调是未定义，且通常设置为 0。 

饱和度值可以介于 0 和 100，并指定颜色的纯度。 虽然值低于 100 会导致要变得更 grayish 的颜色，饱和度值为 100 是最纯粹的颜色。 饱和度值为 0 会导致为灰色阴影。

亮度 （或亮度） 值指示如何明亮的颜色是。 亮度值 0 为黑色而不考虑其他设置。 同样，100 的亮度值为白色。 

HSL 值 （0、 100，50） 是纯红色显示的 RGB 值 (FF，00，00)。 RGB 值 (00，FF，FF)，纯青色 HSL 值 （180，100，50）。 饱和度减小时，因为主色组件会减少，而其他组件会增加。 在饱和度级别为 0，所有组件都都相同，颜色是灰色阴影。 降低亮度，以转到黑色;若要转到空白的亮度会增加。

## <a name="the-blend-modes-in-detail"></a>在详细信息中的混合模式

像其他混合模式下，四个非可分离的混合模式涉及 （这通常是位图图像） 的目标和源，这通常是一种颜色或渐变。 混合模式组合来自目标和源的色调、 饱和度和亮度值：

| 混合模式   | 源中的组件 | 从目标组件 |
| ------------ | ---------------------- | --------------------------- |
| `Hue`        | 色调                    | 饱和度和亮度   |
| `Saturation` | 饱和度             | 色调和亮度          |
| `Color`      | 色调和饱和度     | 亮度                  | 
| `Luminosity` | 亮度             | 色调和饱和度          | 

请参阅 W3C [**合成和混合级别 1** ](https://www.w3.org/TR/compositing-1/)算法规范。

**Blend 的非可分离模式**页包含`Picker`若要选择其中一种混合模式和三个`Slider`视图选择 HSL 颜色：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp;assembly=SkiaSharp"
             xmlns:skiaviews="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Effects.NonSeparableBlendModesPage"
             Title="Non-Separable Blend Modes">

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
                    <x:Static Member="skia:SKBlendMode.Hue" />
                    <x:Static Member="skia:SKBlendMode.Saturation" />
                    <x:Static Member="skia:SKBlendMode.Color" />
                    <x:Static Member="skia:SKBlendMode.Luminosity" />
                </x:Array>
            </Picker.ItemsSource>

            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>

        <Slider x:Name="hueSlider"
                Maximum="360"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <Slider x:Name="satSlider"
                Maximum="100"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <Slider x:Name="lumSlider"
                Maximum="100"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <StackLayout Orientation="Horizontal">
            <Label x:Name="hslLabel"
                   HorizontalOptions="CenterAndExpand" />

            <Label x:Name="rgbLabel"
                   HorizontalOptions="CenterAndExpand" />

        </StackLayout>
    </StackLayout>
</ContentPage>
```

为节省空间，这三个`Slider`视图不会标识该程序的用户界面中。 你将需要记住的顺序是色调、 饱和度和亮度。 两个`Label`视图页的底部显示 HSL 和 RGB 颜色值。

代码隐藏文件加载一个位图资源、 显示在画布上尽可能大地，然后涵盖在画布上添加一个矩形。 矩形颜色基于三个`Slider`视图和混合模式是中选定的一个`Picker`:

```csharp
public partial class NonSeparableBlendModesPage : ContentPage
{
    SKBitmap bitmap = BitmapExtensions.LoadBitmapResource(
                        typeof(NonSeparableBlendModesPage),
                        "SkiaSharpFormsDemos.Media.Banana.jpg");
    SKColor color;

    public NonSeparableBlendModesPage()
    {
        InitializeComponent();
    }

    void OnPickerSelectedIndexChanged(object sender, EventArgs args)
    {
        canvasView.InvalidateSurface();
    }

    void OnSliderValueChanged(object sender, ValueChangedEventArgs e)
    {
        // Calculate new color based on sliders
        color = SKColor.FromHsl((float)hueSlider.Value,
                                (float)satSlider.Value,
                                (float)lumSlider.Value);

        // Use labels to display HSL and RGB color values
        color.ToHsl(out float hue, out float sat, out float lum);

        hslLabel.Text = String.Format("HSL = {0:F0} {1:F0} {2:F0}",
                                      hue, sat, lum);

        rgbLabel.Text = String.Format("RGB = {0:X2} {1:X2} {2:X2}",
                                      color.Red, color.Green, color.Blue);

        canvasView.InvalidateSurface();
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();
        canvas.DrawBitmap(bitmap, info.Rect, BitmapStretch.Uniform);

        // Get blend mode from Picker
        SKBlendMode blendMode =
            (SKBlendMode)(blendModePicker.SelectedIndex == -1 ?
                                        0 : blendModePicker.SelectedItem);

        using (SKPaint paint = new SKPaint())
        {
            paint.Color = color;
            paint.BlendMode = blendMode;
            canvas.DrawRect(info.Rect, paint);
        }
    }
}
```

请注意该程序不会显示所选择的三个滑块 HSL 颜色值。 相反，它从这些滑块创建一个颜色值，然后使用[ `ToHsl` ](xref:SkiaSharp.SKColor.ToHsl*)方法以获取色调、 饱和度和亮度值。 这是因为`FromHsl`方法将 HSL 颜色转换为的 RGB 颜色，存储在内部在`SKColor`结构。 `ToHsl`方法将从 RGB 转换为 HSL，但结果不会总是原始值。 

例如， `FromHsl` HSL 值 （180、 50，0） 转换为代表 RGB 颜色 （0，0，0），因为`Luminosity`为零。 `ToHsl`方法： 将 RGB 颜色 （0，0，0） 转换为 HSL 颜色 （0，0，0），因为色调和饱和度值是否无关。 当使用此程序时，它是更好地查看程序正在使用而不使用滑块指定 HSL 颜色的表示形式。

`SKBlendModes.Hue`混合模式下保留目标的饱和度和亮度级别的同时使用 Hue 级别的源。 测试此混合模式时，饱和度和亮度滑块必须设置为 0 或 100 以外因为在这些情况下，Hue 没有唯一定义。

[![非可分离的混合模式-Hue](non-separable-images/NonSeparableBlendModes-Hue.png "非可分离的混合模式-Hue")](non-separable-images/NonSeparableBlendModes-Hue-Large.png#lightbox)

当你使用 （如与左侧的 iOS 屏幕截图） 将滑块设置为 0，则，所有内容变为红色。 但这并不意味着该图像是完全不存在的绿色和蓝色。 显然是在结果中存在的仍灰色底纹。 例如，代表 RGB 颜色 （40，40，C0） 等同于 HSL 颜色 （240、 50，50）。 Hue 是蓝色，但饱和度值 50 指示有红色和绿色组件以及。 如果设置为 0 而色调`SKBlendModes.Hue`，HSL 颜色是 （0，50，50），它代表 RGB 颜色 (C0，40，40)。 仍蓝色和绿色组件，但现在的主流组件为红色。

`SKBlendModes.Saturation`混合模式将源的饱和度级别与色调和目标的亮度相结合。 如色调、 饱和度定义不完善如果亮度是 0 或 100。 从理论上讲，任何两个极端之间的亮度设置应起作用。 但是，似乎会影响结果超过了预期的亮度设置。 亮度设置为 50，并可以查看图片的饱和度级别的设置方式：

[![非可分离的混合模式-饱和度](non-separable-images/NonSeparableBlendModes-Saturation.png "非可分离的混合模式-饱和度")](non-separable-images/NonSeparableBlendModes-Saturation-Large.png#lightbox)

可以使用此混合模式以提高颜色饱和度的乏味的映像，或可以为生成的图像组成仅灰色底纹减少到零 （例如，在左侧的 iOS 屏幕快照） 的饱和度。

`SKBlendModes.Color`混合模式下保留目标的亮度，但使用色调和饱和度的源。 同样，这意味着亮度滑块极端之间的某个位置的任何设置应起作用。 

[![非可分离的混合模式-颜色](non-separable-images/NonSeparableBlendModes-Color.png "非可分离的混合模式的颜色")](non-separable-images/NonSeparableBlendModes-Color-Large.png#lightbox)

稍后您将看到此混合模式下的应用程序。

最后，`SKBlendModes.Luminosity`混合模式与相反`SKBlendModes.Color`。 它保留色调和饱和度的目标，但使用源的亮度。 `Luminosity`混合模式是该批的最神秘的模式:"色调" 和 "饱和度" 滑块会影响图像, 但是即使在中等亮度, 图像也不是不同的:

[![非可分离的混合模式-亮度](non-separable-images/NonSeparableBlendModes-Luminosity.png "非可分离的混合模式-亮度")](non-separable-images/NonSeparableBlendModes-Luminosity-Large.png#lightbox)

从理论上讲，增加或减少的图像的亮度应使其较浅或变暗。 有趣的是，[亮度示例中 Skia **SkBlendMode 引用**](https://skia.org/user/api/SkBlendMode_Reference#Luminosity)非常相似。

它通常是不需要一个非可分离的混合模式使用包含的一种颜色应用于整个目标图像的源的情况。 结果是只是过大。 你将想要限制对该映像的一部分的效果。 在这种情况下，源可能会合并 transparancy，或可能是源将被限制为一个较小图形。

## <a name="a-matte-for-a-separable-mode"></a>对于可分离模式亚光效果

下面是一个包含为中的资源的位图[ **SkiaSharpFormsDemos** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)示例。 文件名是**Banana.jpg**:

![香蕉 Monkey](non-separable-images/Banana.jpg "香蕉 Monkey")

它是可以创建包含只香蕉亚光效果。 这也是中的资源[ **SkiaSharpFormsDemos** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)示例。 文件名是**BananaMatte.png**:

![香蕉遮罩](non-separable-images/BananaMatte.png "香蕉亚光效果")

除了黑色香蕉图形位图的其余部分是透明的。

**蓝色香蕉**页面使用该亚光效果来更改色调和饱和度持有 monkey，香蕉的但若要在映像中的其他不进行任何更改。 

在下面的示例`BlueBananaPage`类， **Banana.jpg**位图加载为一个字段。 构造函数加载**BananaMatte.png**位图作为`matteBitmap`对象，但它不会保留在构造函数之外的对象。 相反，第三个位图的名为`blueBananaBitmap`创建。 `matteBitmap`上绘制`blueBananaBitmap`跟`SKPaint`使用其`Color`设置为蓝色并将其`BlendMode`设置为`SKBlendMode.SrcIn`。 `blueBananaBitmap`透明，但与纯蓝色实线映像香蕉的相比大部分保持：

```csharp
public class BlueBananaPage : ContentPage
{
    SKBitmap bitmap = BitmapExtensions.LoadBitmapResource(
        typeof(BlueBananaPage),
        "SkiaSharpFormsDemos.Media.Banana.jpg");

    SKBitmap blueBananaBitmap;

    public BlueBananaPage()
    {
        Title = "Blue Banana";

        // Load banana matte bitmap (black on transparent)
        SKBitmap matteBitmap = BitmapExtensions.LoadBitmapResource(
            typeof(BlueBananaPage),
            "SkiaSharpFormsDemos.Media.BananaMatte.png");

        // Create a bitmap with a solid blue banana and transparent otherwise
        blueBananaBitmap = new SKBitmap(matteBitmap.Width, matteBitmap.Height);

        using (SKCanvas canvas = new SKCanvas(blueBananaBitmap))
        {
            canvas.Clear();
            canvas.DrawBitmap(matteBitmap, new SKPoint(0, 0));

            using (SKPaint paint = new SKPaint())
            {
                paint.Color = SKColors.Blue;
                paint.BlendMode = SKBlendMode.SrcIn;
                canvas.DrawPaint(paint);
            }
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

        canvas.DrawBitmap(bitmap, info.Rect, BitmapStretch.Uniform);

        using (SKPaint paint = new SKPaint())
        {
            paint.BlendMode = SKBlendMode.Color;
            canvas.DrawBitmap(blueBananaBitmap, 
                              info.Rect, 
                              BitmapStretch.Uniform, 
                              paint: paint);
        }
    }
}
```

`PaintSurface`处理程序与持有香蕉 monkey 绘制位图。 此代码后跟的显示`blueBananaBitmap`与`SKBlendMode.Color`。 通过香蕉的图面，每个像素的色调和饱和度将替换为蓝色，对应于色调值为 240 以及饱和度值为 100。 亮度，但是，保持不变，这意味着香蕉继续拥有不管其新颜色的真实纹理：

[![蓝色香蕉](non-separable-images/BlueBanana.png "蓝色香蕉")](non-separable-images/BlueBanana-Large.png#lightbox)

请尝试更改到混合模式`SKBlendMode.Saturation`。 香蕉仍保持为黄色，但它是一个更强烈的黄色。 在现实生活应用程序中，这可能是相对于启用香蕉蓝色更为理想效果。

## <a name="related-links"></a>相关链接

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
