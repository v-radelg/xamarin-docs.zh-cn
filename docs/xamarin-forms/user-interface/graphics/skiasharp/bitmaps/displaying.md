---
title: 显示 SkiaSharp 位图
description: 了解如何显示的 SkiaSharp 中像素的位图大小和扩展以填充矩形，同时保存纵横比。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 8E074F8D-4715-4146-8CC0-FD7A8290EDE9
author: davidbritch
ms.author: dabritch
ms.date: 07/17/2018
ms.openlocfilehash: 47b4a1bb0249bc21bd75e82067cb00b3f272e202
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2019
ms.locfileid: "68642675"
---
# <a name="displaying-skiasharp-bitmaps"></a>显示 SkiaSharp 位图

[![下载示例](~/media/shared/download.png)下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

一文中引入了使用者的 SkiaSharp 位图 **[位图基础知识中 SkiaSharp](../basics/bitmaps.md)** 。 该文章介绍了三种方式来加载位图并通过三种方式显示位图。 本文介绍的技术加载位图并得更深入的使用`DrawBitmap`方法的`SKCanvas`。

![显示示例](displaying-images/DisplayingSample.png "显示示例")

          `DrawBitmapLattice`并`DrawBitmapNinePatch`一文中讨论方法 **[分段显示 SkiaSharp 位图](segmented.md)** 。

在此页上的示例取自 **[SkiaSharpFormsDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)** 应用程序。 从该应用程序的主页上，选择**SkiaSharp 位图**，然后转到**显示位图**部分。

## <a name="loading-a-bitmap"></a>正在加载位图

SkiaSharp 应用程序通常使用的位图来自三个不同来源之一：

- 借助 Internet 多
- 从可执行文件中嵌入的资源
- 从用户的照片库

还有可能 SkiaSharp 应用程序以创建新的位图，然后在其上绘制或从算法上设置位图位。 在文章中讨论了这些技术 **[创建和上 SkiaSharp 位图绘制](drawing.md)** 并 **[访问 SkiaSharp 位图像素](pixel-bits.md)** .

在以下三个代码示例中的加载位图，则该类假定为包含类型的字段`SKBitmap`:

```csharp
SKBitmap bitmap;
```

与项目 **[位图基础知识中 SkiaSharp](../basics/bitmaps.md)** 所述，通过 Internet 加载位图的最佳方法是使用[`HttpClient`](xref:System.Net.Http.HttpClient)类。 类的单个实例可以定义为字段：

```csharp
HttpClient httpClient = new HttpClient();
```

使用时`HttpClient`iOS 和 Android 应用程序，你将想要设置项目属性，如文档中所述 **[传输层安全 (TLS) 1.2](~/cross-platform/app-fundamentals/transport-layer-security.md)** 。

使用的代码`HttpClient`情况下，涉及`await`运算符，因此它必须位于`async`方法：

```csharp
try
{
    using (Stream stream = await httpClient.GetStreamAsync("https:// ··· "))
    using (MemoryStream memStream = new MemoryStream())
    {
        await stream.CopyToAsync(memStream);
        memStream.Seek(0, SeekOrigin.Begin);

        bitmap = SKBitmap.Decode(stream);
        ···
    };
}
catch
{
    ···
}
```

请注意，`Stream`对象，来自`GetStreamAsync`复制到`MemoryStream`。 Android 不允许`Stream`从`HttpClient`由主线程以外的异步方法中进行处理。 

[`SKBitmap.Decode`](xref:SkiaSharp.SKBitmap.Decode(System.IO.Stream))执行大量工作:传递`Stream`给它的对象引用一个内存块, 其中包含一个通用位图文件格式 (通常为 JPEG、PNG 或 GIF) 中的整个位图。 `Decode`方法必须确定格式，并再到 SkiaSharp 自己内部的位图格式解码的位图文件。

你的代码调用后`SKBitmap.Decode`，它可能会使`CanvasView`以便`PaintSurface`处理程序可以显示新加载的位图。

第二种方法来加载位图是通过.NET Standard 库中嵌入的资源中包括位图引用单个平台项目。 资源 ID 传递给[ `GetManifestResourceStream` ](xref:System.Reflection.Assembly.GetManifestResourceStream(System.String))方法。 此资源 ID 组成的程序集名称、 文件夹名称和文件名用句点分隔的资源：

```csharp
string resourceID = "assemblyName.folderName.fileName";
Assembly assembly = GetType().GetTypeInfo().Assembly;

using (Stream stream = assembly.GetManifestResourceStream(resourceID))
{
    bitmap = SKBitmap.Decode(stream);
    ···
}
```

也可以为 iOS、 Android 和通用 Windows 平台 (UWP) 的单个平台项目中的资源存储位图文件。 但是，加载这些位图需要位于平台项目中的代码。

获取位图的第三个方法是从用户的图片库。 下面的代码使用包含在一个依赖关系服务 **[SkiaSharpFormsDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)** 应用程序。 **SkiaSharpFormsDemo** .NET Standard 库包含`IPhotoLibrary`接口，而每个平台项目包含`PhotoLibrary`实现该接口的类。

```csharp
IPhotoicturePicker picturePicker = DependencyService.Get<IPhotoLibrary>();

using (Stream stream = await picturePicker.GetImageStreamAsync())
{
    if (stream != null)
    {
        bitmap = SKBitmap.Decode(stream);
        ···
    }
}
```

通常情况下，此类代码还将使失效`CanvasView`，以便`PaintSurface`处理程序可以显示新的位图。

`SKBitmap`类定义了多个有用的属性，包括[ `Width` ](xref:SkiaSharp.SKBitmap.Width)并[ `Height` ](xref:SkiaSharp.SKBitmap.Height)，显示的位图，以及许多方法，其中包括的像素尺寸若要创建位图，以复制这些值，并公开像素位的方法。 

## <a name="displaying-in-pixel-dimensions"></a>在像素尺寸中显示

SkiaSharp [ `Canvas` ](xref:SkiaSharp.SKCanvas)类定义了四个`DrawBitmap`方法。 这些方法允许位图，以显示两个完全不同的方式： 

- 指定`SKPoint`值 (或单独`x`和`y`值) 中其像素大小显示位图。 位图的像素是直接映射到的视频显示器的像素为单位。
- 指定矩形将导致要被拉伸到的大小和形状的矩形的位图。 

在使用其像素大小显示位图[ `DrawBitmap` ](xref:SkiaSharp.SKCanvas.DrawBitmap(SkiaSharp.SKBitmap,SkiaSharp.SKPoint,SkiaSharp.SKPaint))与`SKPoint`参数或[ `DrawBitmap` ](xref:SkiaSharp.SKCanvas.DrawBitmap(SkiaSharp.SKBitmap,System.Single,System.Single,SkiaSharp.SKPaint))具有单独`x`和`y`参数：

```csharp
DrawBitmap(SKBitmap bitmap, SKPoint pt, SKPaint paint = null)

DrawBitmap(SKBitmap bitmap, float x, float y, SKPaint paint = null)
```

这两种方法是在功能上相同的。 指定的点指示相对于 canvas 位图左上角的位置。 由于移动设备的像素的分辨率很高，较小位图通常显示在这些设备上很小。

可选`SKPaint`参数，可显示使用透明的位图。 若要执行此操作，创建`SKPaint`对象并设置`Color`属性设置为任何`SKColor`值，该值具有 alpha 通道小于 1。 例如：

```csharp
paint.Color = new SKColor(0, 0, 0, 0x80);
```

作为最后一个参数传递 0x80 指示 50%的透明度。 此外可以在其中一个预定义的颜色设置 alpha 通道：

```csharp
paint.Color = SKColors.Red.WithAlpha(0x80);
```

但是，是颜色本身是不相关。 使用时检查仅 alpha 通道`SKPaint`对象中`DrawBitmap`调用。

`SKPaint`对象也扮演着角色时显示位图使用混合模式或筛选效果。 这些文章中演示[SkiaSharp 组合的情况下和混合模式](../effects/blend-modes/index.md)并[SkiaSharp 映像筛选器](../effects/image-filters.md)。

**像素尺寸**页面 **[SkiaSharpFormsDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)** 示例程序将显示一个位图资源，可为 320 像素宽乘 240 像素高：

```csharp
public class PixelDimensionsPage : ContentPage
{
    SKBitmap bitmap;

    public PixelDimensionsPage()
    {
        Title = "Pixel Dimensions";

        // Load the bitmap from a resource
        string resourceID = "SkiaSharpFormsDemos.Media.Banana.jpg";
        Assembly assembly = GetType().GetTypeInfo().Assembly;

        using (Stream stream = assembly.GetManifestResourceStream(resourceID))
        {
            bitmap = SKBitmap.Decode(stream);
        }

        // Create the SKCanvasView and set the PaintSurface handler
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

        float x = (info.Width - bitmap.Width) / 2;
        float y = (info.Height - bitmap.Height) / 2;

        canvas.DrawBitmap(bitmap, x, y);
    }
}
```

`PaintSurface`处理程序通过计算中心位图`x`和`y`值基于显示器表面的像素尺寸和位图的像素尺寸：

[![像素尺寸](displaying-images/PixelDimensions.png "像素尺寸")](displaying-images/PixelDimensions-Large.png#lightbox)

如果应用程序想要在其左上角显示位图，它将只需传递的坐标 （0，0）。 

## <a name="a-method-for-loading-resource-bitmaps"></a>用于加载资源位图方法

有很多即将推出的示例将需要加载位图资源。 静态`BitmapExtensions`类中 **[SkiaSharpFormsDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)** 解决方案包含用于帮助解决问题的方法：

```csharp
static class BitmapExtensions
{
    public static SKBitmap LoadBitmapResource(Type type, string resourceID)
    {
        Assembly assembly = type.GetTypeInfo().Assembly;

        using (Stream stream = assembly.GetManifestResourceStream(resourceID))
        {
            return SKBitmap.Decode(stream);
        }
    }
    ···
}
```

请注意`Type`参数。 这可以是`Type`中存储的位图资源的程序集的任何类型与关联的对象。

这`LoadBitmapResource`方法将在需要的位图资源的所有后续示例中使用。

## <a name="stretching-to-fill-a-rectangle"></a>拉伸以填充矩形

`SKCanvas`类还定义了[ `DrawBitmap` ](xref:SkiaSharp.SKCanvas.DrawBitmap(SkiaSharp.SKBitmap,SkiaSharp.SKRect,SkiaSharp.SKPaint))呈现到一个矩形，另一个位图方法[ `DrawBitmap` ](xref:SkiaSharp.SKCanvas.DrawBitmap(SkiaSharp.SKBitmap,SkiaSharp.SKRect,SkiaSharp.SKRect,SkiaSharp.SKPaint))呈现为位图的矩形子集方法矩形：

```
DrawBitmap(SKBitmap bitmap, SKRect dest, SKPaint paint = null)

DrawBitmap(SKBitmap bitmap, SKRect source, SKRect dest, SKPaint paint = null)
```

在这两种情况下，位图拉伸以填充名为的矩形`dest`。 在第二个方法中，`source`矩形，可选择位图的子集。 `dest`矩形是相对于输出设备;`source`矩形是相对于位图。

**填充矩形**页演示这两种方法通过显示同一位图的第一个示例中使用早期在矩形中相同的大小作为画布： 

```csharp
public class FillRectanglePage : ContentPage
{
    SKBitmap bitmap =
        BitmapExtensions.LoadBitmapResource(typeof(FillRectanglePage),
                                            "SkiaSharpFormsDemos.Media.Banana.jpg");
    public FillRectanglePage ()
    {
        Title = "Fill Rectangle";

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

        canvas.DrawBitmap(bitmap, info.Rect);
    }
}
```

请注意，使用新`BitmapExtensions.LoadBitmapResource`方法以设置`SKBitmap`字段。 从获取目标矩形[ `Rect` ](xref:SkiaSharp.SKImageInfo.Rect)属性的`SKImageInfo`，它介绍显示器表面的大小：

[![填充矩形](displaying-images/FillRectangle.png "填充矩形")](displaying-images/FillRectangle-Large.png#lightbox)

这通常是_不_所需。 图像是因在水平和垂直方向上以不同的方式拉伸而失真。 当非像素大小显示位图，通常您想要保留位图的原始纵横比。

## <a name="stretching-while-preserving-the-aspect-ratio"></a>拉伸同时保存纵横比

同时保留纵横比拉伸位图是一个过程也称为_统一缩放_。 该术语建议算法的方法。 一个可能的解决方案所示**统一缩放**页：

```csharp
public class UniformScalingPage : ContentPage
{
    SKBitmap bitmap =
        BitmapExtensions.LoadBitmapResource(typeof(UniformScalingPage),
                                            "SkiaSharpFormsDemos.Media.Banana.jpg");
    public UniformScalingPage()
    {
        Title = "Uniform Scaling";

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

        float scale = Math.Min((float)info.Width / bitmap.Width, 
                               (float)info.Height / bitmap.Height);
        float x = (info.Width - scale * bitmap.Width) / 2;
        float y = (info.Height - scale * bitmap.Height) / 2;
        SKRect destRect = new SKRect(x, y, x + scale * bitmap.Width, 
                                           y + scale * bitmap.Height);

        canvas.DrawBitmap(bitmap, destRect);
    }
}
```

`PaintSurface`处理程序计算`scale`造型能够适用的显示宽度和高度为位图宽度和高度的比率的最小值。 `x`和`y`然后可用于集中缩放的位图中的显示宽度和高度计算值。 目标矩形具有的左上角`x`和`y`和右下角的这些值以及缩放的宽度和高度的位图：

[![统一缩放](displaying-images/UniformScaling.png "统一缩放")](displaying-images/UniformScaling-Large.png#lightbox)

打开手机，请参阅已延伸到该区域的位图：

[![统一缩放横向](displaying-images/UniformScaling-Landscape.png "统一缩放横向")](displaying-images/UniformScaling-Landscape-Large.png#lightbox)

使用此的优点`scale`身份变得明显时您想要实现稍有不同的算法。 假设你想要保留位图的长宽比，但还填充目标矩形。 做到这一点的唯一方法是通过裁剪该映像的一部分，但您可以实现该算法只需通过更改`Math.Min`到`Math.Max`在上面的代码。 下面是结果： 

[![统一缩放的替代方法](displaying-images/UniformScaling-Alternative.png "统一缩放的替代方法")](displaying-images/UniformScaling-Alternative-Large.png#lightbox)

位图的纵横比会保留，但有所剪裁区域左侧和右侧的位图。

## <a name="a-versatile-bitmap-display-function"></a>通用位图显示函数

基于 XAML 的编程环境 （例如 UWP 和 Xamarin.Forms） 具有一个工具用于扩展或压缩位图的大小，同时保留其纵横比。 虽然 SkiaSharp 不包括此功能，可以自行实现。 `BitmapExtensions`类中包含 [**SkiaSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)应用程序演示了如何。 类定义了两个新`DrawBitmap`执行纵横比计算的方法。 这些新方法是扩展方法的`SKCanvas`。

新`DrawBitmap`方法包含类型的参数`BitmapStretch`，在中定义的枚举**BitmapExtensions.cs**文件：

```csharp
public enum BitmapStretch
{
    None,
    Fill,
    Uniform,
    UniformToFill,
    AspectFit = Uniform,
    AspectFill = UniformToFill
}
```

`None`， `Fill`， `Uniform`，和`UniformToFill`成员将与 UWP 中的那些相同[ `Stretch` ](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.media.stretch.aspx)枚举。 类似 Xamarin.Forms [ `Aspect` ](xref:Xamarin.Forms.Aspect)枚举定义的成员`Fill`， `AspectFit`，并`AspectFill`。

**统一缩放**如上所示的页面为中心的矩形中的位图，但您可能希望其他选项，如定位在的左侧或右侧的矩形，或在顶部或底部的位图。 目的就在于此`BitmapAlignment`枚举：

```csharp
public enum BitmapAlignment
{
    Start,
    Center,
    End
}
```

对齐方式设置不起作用与一起使用时`BitmapStretch.Fill`。

第一个`DrawBitmap`扩展函数包含目标矩形，但没有源矩形。 因此，如果你想居中的位图，只需指定定义默认值`BitmapStretch`成员：

```csharp
static class BitmapExtensions
{
    ···
    public static void DrawBitmap(this SKCanvas canvas, SKBitmap bitmap, SKRect dest, 
                                  BitmapStretch stretch, 
                                  BitmapAlignment horizontal = BitmapAlignment.Center, 
                                  BitmapAlignment vertical = BitmapAlignment.Center, 
                                  SKPaint paint = null)
    {
        if (stretch == BitmapStretch.Fill)
        {
            canvas.DrawBitmap(bitmap, dest, paint);
        }
        else
        {
            float scale = 1;

            switch (stretch)
            {
                case BitmapStretch.None:
                    break;

                case BitmapStretch.Uniform:
                    scale = Math.Min(dest.Width / bitmap.Width, dest.Height / bitmap.Height);
                    break;

                case BitmapStretch.UniformToFill:
                    scale = Math.Max(dest.Width / bitmap.Width, dest.Height / bitmap.Height);
                    break;
            }

            SKRect display = CalculateDisplayRect(dest, scale * bitmap.Width, scale * bitmap.Height, 
                                                  horizontal, vertical);

            canvas.DrawBitmap(bitmap, display, paint);
        }
    }
    ···
}
```

此方法的主要用途是计算名为的缩放系数`scale`调用时然后应用于位图宽度和高度`CalculateDisplayRect`方法。 这是计算矩形用于显示基于水平和垂直对齐的位图的方法：

```csharp
static class BitmapExtensions
{
    ···
    static SKRect CalculateDisplayRect(SKRect dest, float bmpWidth, float bmpHeight, 
                                       BitmapAlignment horizontal, BitmapAlignment vertical)
    {
        float x = 0;
        float y = 0;

        switch (horizontal)
        {
            case BitmapAlignment.Center:
                x = (dest.Width - bmpWidth) / 2;
                break;

            case BitmapAlignment.Start:
                break;

            case BitmapAlignment.End:
                x = dest.Width - bmpWidth;
                break;
        }

        switch (vertical)
        {
            case BitmapAlignment.Center:
                y = (dest.Height - bmpHeight) / 2;
                break;

            case BitmapAlignment.Start:
                break;

            case BitmapAlignment.End:
                y = dest.Height - bmpHeight;
                break;
        }

        x += dest.Left;
        y += dest.Top;

        return new SKRect(x, y, x + bmpWidth, y + bmpHeight);
    }
}
```

`BitmapExtensions`类包含一个附加`DrawBitmap`源矩形具有用于指定位图的子集的方法。 此方法是类似于第一个不同之处在于根据计算的缩放因子`source`矩形，然后应用到`source`调用中的矩形`CalculateDisplayRect`:

```csharp
static class BitmapExtensions
{
    ···
    public static void DrawBitmap(this SKCanvas canvas, SKBitmap bitmap, SKRect source, SKRect dest,
                                  BitmapStretch stretch,
                                  BitmapAlignment horizontal = BitmapAlignment.Center,
                                  BitmapAlignment vertical = BitmapAlignment.Center,
                                  SKPaint paint = null)
    {
        if (stretch == BitmapStretch.Fill)
        {
            canvas.DrawBitmap(bitmap, source, dest, paint);
        }
        else
        {
            float scale = 1;

            switch (stretch)
            {
                case BitmapStretch.None:
                    break;

                case BitmapStretch.Uniform:
                    scale = Math.Min(dest.Width / source.Width, dest.Height / source.Height);
                    break;

                case BitmapStretch.UniformToFill:
                    scale = Math.Max(dest.Width / source.Width, dest.Height / source.Height);
                    break;
            }

            SKRect display = CalculateDisplayRect(dest, scale * source.Width, scale * source.Height, 
                                                  horizontal, vertical);

            canvas.DrawBitmap(bitmap, source, display, paint);
        }
    }
    ···
}
```

这两个新的第一个`DrawBitmap`方法进行了演示**缩放模式**页。 XAML 文件包含三种`Picker`元素，可选择的成员`BitmapStretch`和`BitmapAlignment`枚举：

```xaml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:SkiaSharpFormsDemos"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Bitmaps.ScalingModesPage"
             Title="Scaling Modes">

    <Grid Padding="10">
        <Grid.RowDefinitions>
            <RowDefinition Height="*" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
        </Grid.RowDefinitions>

        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="Auto" />
            <ColumnDefinition Width="*" />
        </Grid.ColumnDefinitions>

        <skia:SKCanvasView x:Name="canvasView" 
                           Grid.Row="0" Grid.Column="0" Grid.ColumnSpan="2"
                           PaintSurface="OnCanvasViewPaintSurface" />

        <Label Text="Stretch:"
               Grid.Row="1" Grid.Column="0"
               VerticalOptions="Center" />

        <Picker x:Name="stretchPicker"
                Grid.Row="1" Grid.Column="1"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.ItemsSource>
                <x:Array Type="{x:Type local:BitmapStretch}">
                    <x:Static Member="local:BitmapStretch.None" />
                    <x:Static Member="local:BitmapStretch.Fill" />
                    <x:Static Member="local:BitmapStretch.Uniform" />
                    <x:Static Member="local:BitmapStretch.UniformToFill" />
                </x:Array>
            </Picker.ItemsSource>

            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>

        <Label Text="Horizontal Alignment:"
               Grid.Row="2" Grid.Column="0"
               VerticalOptions="Center" />

        <Picker x:Name="horizontalPicker" 
                Grid.Row="2" Grid.Column="1"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.ItemsSource>
                <x:Array Type="{x:Type local:BitmapAlignment}">
                    <x:Static Member="local:BitmapAlignment.Start" />
                    <x:Static Member="local:BitmapAlignment.Center" />
                    <x:Static Member="local:BitmapAlignment.End" />
                </x:Array>
            </Picker.ItemsSource>

            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>

        <Label Text="Vertical Alignment:"
               Grid.Row="3" Grid.Column="0"
               VerticalOptions="Center" />

        <Picker x:Name="verticalPicker" 
                Grid.Row="3" Grid.Column="1"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.ItemsSource>
                <x:Array Type="{x:Type local:BitmapAlignment}">
                    <x:Static Member="local:BitmapAlignment.Start" />
                    <x:Static Member="local:BitmapAlignment.Center" />
                    <x:Static Member="local:BitmapAlignment.End" />
                </x:Array>
            </Picker.ItemsSource>

            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>
    </Grid>
</ContentPage>
```

代码隐藏文件只是使失效`CanvasView`任何`Picker`项已更改。 `PaintSurface`处理程序访问三个`Picker`调用的视图`DrawBitmap`扩展方法：

```csharp
public partial class ScalingModesPage : ContentPage
{
    SKBitmap bitmap =
        BitmapExtensions.LoadBitmapResource(typeof(ScalingModesPage),
                                            "SkiaSharpFormsDemos.Media.Banana.jpg");
    public ScalingModesPage()
    {
        InitializeComponent();
    }

    private void OnPickerSelectedIndexChanged(object sender, EventArgs args)
    {
        canvasView.InvalidateSurface();
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        SKRect dest = new SKRect(0, 0, info.Width, info.Height);

        BitmapStretch stretch = (BitmapStretch)stretchPicker.SelectedItem;
        BitmapAlignment horizontal = (BitmapAlignment)horizontalPicker.SelectedItem;
        BitmapAlignment vertical = (BitmapAlignment)verticalPicker.SelectedItem;

        canvas.DrawBitmap(bitmap, dest, stretch, horizontal, vertical);
    }
}
```

下面是一些组合的选项：

[![缩放模式](displaying-images/ScalingModes.png "缩放模式")](displaying-images/ScalingModes-Large.png#lightbox)

**矩形子集**页面就几乎具有相同的 XAML 文件作为**缩放模式**，但代码隐藏文件定义由给定的位图的矩形子集`SOURCE`字段： 

```csharp
public partial class ScalingModesPage : ContentPage
{
    SKBitmap bitmap =
        BitmapExtensions.LoadBitmapResource(typeof(ScalingModesPage),
                                            "SkiaSharpFormsDemos.Media.Banana.jpg");

    static readonly SKRect SOURCE = new SKRect(94, 12, 212, 118);

    public RectangleSubsetPage()
    {
        InitializeComponent();
    }

    private void OnPickerSelectedIndexChanged(object sender, EventArgs args)
    {
        canvasView.InvalidateSurface();
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        SKRect dest = new SKRect(0, 0, info.Width, info.Height);

        BitmapStretch stretch = (BitmapStretch)stretchPicker.SelectedItem;
        BitmapAlignment horizontal = (BitmapAlignment)horizontalPicker.SelectedItem;
        BitmapAlignment vertical = (BitmapAlignment)verticalPicker.SelectedItem;

        canvas.DrawBitmap(bitmap, SOURCE, dest, stretch, horizontal, vertical);
    }
}
```

此矩形源隔离 monkey 的头，如以下屏幕截图中所示：

[![矩形子集](displaying-images/RectangleSubset.png "矩形子集")](displaying-images/RectangleSubset-Large.png#lightbox)

## <a name="related-links"></a>相关链接

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

