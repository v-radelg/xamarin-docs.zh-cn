---
title: 点和短划线在 SkiaSharp
description: 本文介绍如何掌握 SkiaSharp，在绘制点线和虚线线条的复杂性以及此示例代码进行了演示。
ms.prod: xamarin
ms.assetid: 8E9BCC13-830C-458C-9FC8-ECB4EAE66078
ms.technology: xamarin-skiasharp
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2017
ms.openlocfilehash: 229f60cbb96058454a1c634e53a7bb00ec725bcf
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76723752"
---
# <a name="dots-and-dashes-in-skiasharp"></a>点和短划线在 SkiaSharp

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_掌握 SkiaSharp 中绘制点线和虚线的复杂性_

SkiaSharp 可绘制线条的都是不可靠的而是点和短划线组成：

![](dots-images/dottedlinesample.png "Dotted line")

使用路径效果执行此操作，*路径效果*是您设置为 `SKPaint`的[`PathEffect`](xref:SkiaSharp.SKPaint.PathEffect)属性的[`SKPathEffect`](xref:SkiaSharp.SKPathEffect)类的实例。 您可以使用 `SKPathEffect`定义的静态创建方法之一创建路径效果（或组合路径效果）。 （`SKPathEffect` 是 SkiaSharp 支持的六种效果之一; 其他是在[**SkiaSharp 效果**](../effects/index.md)一节中介绍的。）

若要绘制点线或虚线，请使用[`SKPathEffect.CreateDash`](xref:SkiaSharp.SKPathEffect.CreateDash(System.Single[],System.Single))静态方法。 有两个参数：第一个是 `float` 值的数组，这些值指示点和短划线的长度以及它们之间的空格长度。 此数组必须具有偶数数目的元素，并且应至少两个元素。 （数组中可以有零个元素，但这会导致实线。）如果有两个元素，则第一个是点或短划线的长度，第二个元素是下一个点或短划线之前的间隔长度。 如果有多个两个元素，则它们是按以下顺序： 短划线的长度、 间隙长度、 短划线的长度、 间隙长度等。

通常情况下，您将想要的短划线和间隙长度笔划宽度的倍数。 如果笔划宽度为 10 个像素，例如，然后数组 {10，10} 将绘制点线的点和间隙的笔画粗细的长度相同。

但 `SKPaint` 对象的 `StrokeCap` 设置也会影响这些点和短划线。 稍后将看到，此数组的元素具有影响。

**点**划线和虚线页上演示了虚线和虚线。 [**DotsAndDashesPage**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Paths/DotsAndDashesPage.xaml)文件实例化两个 `Picker` 视图，一个视图用于选择笔划帽，第二个视图用于选择虚线数组：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp;assembly=SkiaSharp"
             xmlns:skiaforms="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Paths.DotsAndDashesPage"
             Title="Dots and Dashes">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="*" />
        </Grid.RowDefinitions>

        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="*" />
            <ColumnDefinition Width="*" />
        </Grid.ColumnDefinitions>

        <Picker x:Name="strokeCapPicker"
                Title="Stroke Cap"
                Grid.Row="0"
                Grid.Column="0"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.ItemsSource>
                <x:Array Type="{x:Type skia:SKStrokeCap}">
                    <x:Static Member="skia:SKStrokeCap.Butt" />
                    <x:Static Member="skia:SKStrokeCap.Round" />
                    <x:Static Member="skia:SKStrokeCap.Square" />
                </x:Array>
            </Picker.ItemsSource>
            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>

        <Picker x:Name="dashArrayPicker"
                Title="Dash Array"
                Grid.Row="0"
                Grid.Column="1"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.ItemsSource>
                <x:Array Type="{x:Type x:String}">
                    <x:String>10, 10</x:String>
                    <x:String>30, 10</x:String>
                    <x:String>10, 10, 30, 10</x:String>
                    <x:String>0, 20</x:String>
                    <x:String>20, 20</x:String>
                    <x:String>0, 20, 20, 20</x:String>
                </x:Array>
            </Picker.ItemsSource>
            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>

        <skiaforms:SKCanvasView x:Name="canvasView"
                                PaintSurface="OnCanvasViewPaintSurface"
                                Grid.Row="1"
                                Grid.Column="0"
                                Grid.ColumnSpan="2" />
    </Grid>
</ContentPage>
```

 `dashArrayPicker` 中的前三项假定笔划宽度为10像素。 {10，10} 的点线，数组是 {30，10} 是虚线，和 {10、 10、 30、 10} 是针对点-划线行。 （其他三个将讨论很快。）

[`DotsAndDashesPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Paths/DotsAndDashesPage.xaml.cs)代码隐藏文件包含 `PaintSurface` 事件处理程序和用于访问 `Picker` 视图的几个帮助器例程：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    SKPaint paint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Blue,
        StrokeWidth = 10,
        StrokeCap = (SKStrokeCap)strokeCapPicker.SelectedItem,
        PathEffect = SKPathEffect.CreateDash(GetPickerArray(dashArrayPicker), 20)
    };

    SKPath path = new SKPath();
    path.MoveTo(0.2f * info.Width, 0.2f * info.Height);
    path.LineTo(0.8f * info.Width, 0.8f * info.Height);
    path.LineTo(0.2f * info.Width, 0.8f * info.Height);
    path.LineTo(0.8f * info.Width, 0.2f * info.Height);

    canvas.DrawPath(path, paint);
}

float[] GetPickerArray(Picker picker)
{
    if (picker.SelectedIndex == -1)
    {
        return new float[0];
    }

    string str = (string)picker.SelectedItem;
    string[] strs = str.Split(new char[] { ' ', ',' }, StringSplitOptions.RemoveEmptyEntries);
    float[] array = new float[strs.Length];

    for (int i = 0; i < strs.Length; i++)
    {
        array[i] = Convert.ToSingle(strs[i]);
    }
    return array;
}
```

在下面的屏幕截图上最左侧的 iOS 屏幕显示点线：

[![](dots-images/dotsanddashes-small.png "Triple screenshot of the Dots and Dashes page")](dots-images/dotsanddashes-large.png#lightbox "Triple screenshot of the Dots and Dashes page")

但是，还应该 Android 屏幕显示点线使用数组 {10，10} 改为直线是实线。 发生了什么情况？ 问题在于 Android 屏幕还具有 `Square`的笔划帽设置。 这将扩展一半笔划宽度，从而导致填满间隙的所有短的划线。

若要在使用 `Square` 或 `Round`的笔划帽时解决此问题，必须将数组中的短划线长度减小（有时会导致虚线长度为0），并按笔划长度增加间隙长度。 这是计算 XAML 文件中 `Picker` 的最后三个短划线数组的方式：

- {10，10} 将成为 {0，20} 的点线
- {30，10} 将成为 {20、 20} 的虚线
- {10、 10、 30、 10} 成为点线和虚线行 {0、 20、 20、 20}

UWP 屏幕显示 `Round`的笔划帽的虚线和虚线。 `Round` 描边帽通常在粗线中提供点和短划线的最佳外观。

到目前为止，并未提及 `SKPathEffect.CreateDash` 方法的第二个参数。 此参数名为 "`phase`"，它是指线条开头的点-短划线模式内的偏移量。 例如，如果短划线数组为 {10，10} 并且 `phase` 为10，则该行以间隙而不是点开始。

动画中的 `phase` 参数有一个有趣的应用程序。 **动画螺旋线**页面类似于**Archimedean 螺旋线**页面，只不过[`AnimatedSpiralPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Paths/AnimatedSpiralPage.cs)类使用 Xamarin `Device.Timer` 方法对 `phase` 参数进行动画处理：

```csharp
public class AnimatedSpiralPage : ContentPage
{
    const double cycleTime = 250;       // in milliseconds

    SKCanvasView canvasView;
    Stopwatch stopwatch = new Stopwatch();
    bool pageIsActive;
    float dashPhase;

    public AnimatedSpiralPage()
    {
        Title = "Animated Spiral";

        canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }

    protected override void OnAppearing()
    {
        base.OnAppearing();
        pageIsActive = true;
        stopwatch.Start();

        Device.StartTimer(TimeSpan.FromMilliseconds(33), () =>
        {
            double t = stopwatch.Elapsed.TotalMilliseconds % cycleTime / cycleTime;
            dashPhase = (float)(10 * t);
            canvasView.InvalidateSurface();

            if (!pageIsActive)
            {
                stopwatch.Stop();
            }

            return pageIsActive;
        });
    }
    ···  
}
```

当然，您必须以实际运行该程序，查看动画：

[![](dots-images/animatedspiral-small.png "Triple screenshot of the Animated Spiral page")](dots-images/animatedspiral-large.png#lightbox "Triple screenshot of the Animated Spiral page")

## <a name="related-links"></a>相关链接

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
