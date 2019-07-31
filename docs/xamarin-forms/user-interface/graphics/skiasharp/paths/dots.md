---
title: 点和短划线在 SkiaSharp
description: 本文介绍如何掌握 SkiaSharp，在绘制点线和虚线线条的复杂性以及此示例代码进行了演示。
ms.prod: xamarin
ms.assetid: 8E9BCC13-830C-458C-9FC8-ECB4EAE66078
ms.technology: xamarin-skiasharp
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2017
ms.openlocfilehash: 9e12e901a4224580f72a4b92a4319e02a60efe46
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2019
ms.locfileid: "68643017"
---
# <a name="dots-and-dashes-in-skiasharp"></a>点和短划线在 SkiaSharp

[![下载示例](~/media/shared/download.png)下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_掌握在 SkiaSharp 绘制点线和虚线线条的复杂性_

SkiaSharp 可绘制线条的都是不可靠的而是点和短划线组成：

![](dots-images/dottedlinesample.png "点线")

为此可以使用*路径效果*，即实例[ `SKPathEffect` ](xref:SkiaSharp.SKPathEffect)设置为的类[ `PathEffect` ](xref:SkiaSharp.SKPaint.PathEffect)属性`SKPaint`。 可以创建路径效果 （或合并路径效果），它使用由定义的静态创建方法之一`SKPathEffect`。 (`SKPathEffect` SkiaSharp 通过支持的六个效果之一; 是其他部分中所述[ **SkiaSharp 效果**](../effects/index.md)。)

若要绘制虚线，请使用[ `SKPathEffect.CreateDash` ](xref:SkiaSharp.SKPathEffect.CreateDash(System.Single[],System.Single))静态方法。 有两个参数:第一个是`float`值的数组, 这些值指示点和短划线的长度以及它们之间的空格长度。 此数组必须具有偶数数目的元素，并且应至少两个元素。 （可以有零个元素数组中的，但该结果一条实线。）如果有两个元素，第一个圆点或短划线的长度，第二个间隙的长度在下一步的句点或破折号之前。 如果有多个两个元素，则它们是按以下顺序： 短划线的长度、 间隙长度、 短划线的长度、 间隙长度等。

通常情况下，您将想要的短划线和间隙长度笔划宽度的倍数。 如果笔划宽度为 10 个像素，例如，然后数组 {10，10} 将绘制点线的点和间隙的笔画粗细的长度相同。

但是，`StrokeCap`设置的`SKPaint`对象也会影响这些点和短划线。 稍后将看到，此数组的元素具有影响。

以点分隔和上演示了虚线**点和短划线**页。 [ **DotsAndDashesPage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/LinesAndPaths/DotsAndDashesPage.xaml)文件实例化两个`Picker`查看时，一个用于允许您选择笔画线帽和第二个选择的短划线数组：

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

 中的前三个项`dashArrayPicker`假定笔划宽度为 10 像素。 {10，10} 的点线，数组是 {30，10} 是虚线，和 {10、 10、 30、 10} 是针对点-划线行。 （其他三个将讨论很快。）

[ `DotsAndDashesPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/LinesAndPaths/DotsAndDashesPage.xaml.cs)代码隐藏文件包含`PaintSurface`事件处理程序和几个帮助器例程用于访问`Picker`视图：

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

[![](dots-images/dotsanddashes-small.png "三个点和短划线页屏幕截图")](dots-images/dotsanddashes-large.png#lightbox "三个点和短划线页屏幕截图")

但是，还应该 Android 屏幕显示点线使用数组 {10，10} 改为直线是实线。 这是怎么回事？ 问题是 Android 屏幕还具有笔划上限设置为`Square`。 这将扩展一半笔划宽度，从而导致填满间隙的所有短的划线。

若要获取解决此问题时使用的笔画上限`Square`或`Round`，必须减小的短划线长度是以数组的笔划长度 （有时会导致短划线的长度为 0），并提高笔划长度的间隙长度。 这是如何的最后三个短划线中的数组`Picker`计算在 XAML 文件中：

- {10，10} 将成为 {0，20} 的点线
- {30，10} 将成为 {20、 20} 的虚线
- {10、 10、 30、 10} 成为点线和虚线行 {0、 20、 20、 20}

点线和虚线绘制笔画的行，UWP 屏幕所示的上限`Round`。 `Round`描边端点通常粗线条中提供的点和短划线最佳外观。

到目前为止，将没有提到这一点做的第二个参数的`SKPathEffect.CreateDash`方法。 此参数名为`phase`，它是指行开头的点划线模式内的偏移量。 例如，如果 dash 数组为 {10，10} 和`phase`是 10，则在行开头间隔而不是一个圆点。

一个有趣的应用程序`phase`参数是动画。 **经过动画处理的临界点**页是类似于**Archimedean 螺旋**页上，不同之处在于[ `AnimatedSpiralPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Paths/AnimatedSpiralPage.cs)该类进行动画处理`phase`参数使用Xamarin.Forms`Device.Timer`方法：


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

[![](dots-images/animatedspiral-small.png "经过动画处理的临界点页的三个屏幕截图")](dots-images/animatedspiral-large.png#lightbox "带来三倍的经过动画处理的临界点页屏幕截图")

## <a name="related-links"></a>相关链接

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
