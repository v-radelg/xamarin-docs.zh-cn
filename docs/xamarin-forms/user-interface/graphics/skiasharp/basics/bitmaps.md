---
title: 位图中 SkiaSharp 的基础知识
description: 这篇文章介绍了如何从各种源加载位图 SkiaSharp 中的并将它们显示在 Xamarin.Forms 应用程序，并且此示例代码进行了演示。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 32C95DFF-9065-42D7-966C-D3DBD16906B3
author: davidbritch
ms.author: dabritch
ms.date: 07/17/2018
ms.openlocfilehash: 47fd6323e309353446c707730679a191cb8e923c
ms.sourcegitcommit: c6e56545eafd8ff9e540d56aba32aa6232c5315f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/02/2019
ms.locfileid: "68738891"
---
# <a name="bitmap-basics-in-skiasharp"></a>位图中 SkiaSharp 的基础知识

[![下载示例](~/media/shared/download.png)下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_从各种源加载位图并将其显示。_

SkiaSharp 中的支持是位图的非常大。 本文介绍如何仅基本&mdash;如何加载位图和它们的显示方式：

![](bitmaps-images/basicbitmaps-small.png "两个位图的显示")

部分中可包含位图中的多更深入地探讨[SkiaSharp 位图](../bitmaps/index.md)。

SkiaSharp 位图的类型的对象[ `SKBitmap` ](xref:SkiaSharp.SKBitmap)。 有许多方法来创建一个位图，但本文将限制到本身[ `SKBitmap.Decode` ](xref:SkiaSharp.SKBitmap.Decode(System.IO.Stream))方法，从.NET 加载位图`Stream`对象。

**基本位图**页面**SkiaSharpFormsDemos**程序演示了如何从三个不同的源加载位图：

- 借助 Internet 多
- 从可执行文件中嵌入的资源
- 从用户的照片库

三个`SKBitmap`对象作为字段中定义了这些三个源[ `BasicBitmapsPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/BasicBitmapsPage.cs)类：

```csharp
public class BasicBitmapsPage : ContentPage
{
    SKCanvasView canvasView;
    SKBitmap webBitmap;
    SKBitmap resourceBitmap;
    SKBitmap libraryBitmap;

    public BasicBitmapsPage()
    {
        Title = "Basic Bitmaps";

        canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
        ...
    }
    ...
}
```

## <a name="loading-a-bitmap-from-the-web"></a>从 Web 加载位图

若要加载基于 URL 的位图，可以使用[ `HttpClient` ](/dotnet/api/system.net.http.httpclient?view=netstandard-2.0)类。 您应实例化的一个实例`HttpClient`和重复使用它，因此将其存储为字段：

```csharp
HttpClient httpClient = new HttpClient();
```

使用时`HttpClient`iOS 和 Android 应用程序，你将想要设置项目属性，如文档中所述 **[传输层安全 (TLS) 1.2](~/cross-platform/app-fundamentals/transport-layer-security.md)** 。

因为它是最方便的方式使用`await`运算符，其中`HttpClient`，不能执行的代码中`BasicBitmapsPage`构造函数。 相反，它是一部分`OnAppearing`重写。 此处的 URL 指向具有一些示例位图的 Xamarin web 站点上的区域。 在网站上的包，可以追加大小调整为特定宽度位图的规范：


```csharp
protected override async void OnAppearing()
{
    base.OnAppearing();

    // Load web bitmap.
    string url = "https://developer.xamarin.com/demo/IMG_3256.JPG?width=480";

    try
    {
        using (Stream stream = await httpClient.GetStreamAsync(url))
        using (MemoryStream memStream = new MemoryStream())
        {
            await stream.CopyToAsync(memStream);
            memStream.Seek(0, SeekOrigin.Begin);

            webBitmap = SKBitmap.Decode(stream);
            canvasView.InvalidateSurface();
        };
    }
    catch
    {
    }
}
```

Android 操作系统使用时引发异常`Stream`从返回`GetStreamAsync`中`SKBitmap.Decode`方法因为它正在执行主线程上较长的操作。 出于此原因，位图文件的内容复制到`MemoryStream`对象使用`CopyToAsync`。

静态`SKBitmap.Decode`方法负责解码的位图文件。 它适用于 JPEG、 PNG 和 GIF 的位图格式，并将结果存储在内部 SkiaSharp 格式。 在此情况下，`SKCanvasView`需要失效允许`PaintSurface`处理程序以更新显示。

## <a name="loading-a-bitmap-resource"></a>正在加载位图资源

在代码中，方面加载位图的最简单方法直接在您的应用程序中包括的位图资源。 **SkiaSharpFormsDemos**程序包括一个名为文件夹**媒体**包含几个位图文件，其中一个名为**monkey.png**。 对于存储为程序资源的位图，必须使用**属性**对话框，可以为该文件提供**生成操作**的**嵌入的资源**！

每个嵌入资源都具有一个*资源 ID* , 其中包含项目名称、文件夹和文件名, 所有连接都按句点:**SkiaSharpFormsDemos**的。 可以通过指定该资源获取访问此资源 ID 作为参数到[ `GetManifestResourceStream` ](xref:System.Reflection.Assembly.GetManifestResourceStream(System.String))方法[ `Assembly` ](xref:System.Reflection.Assembly)类：

```csharp
string resourceID = "SkiaSharpFormsDemos.Media.monkey.png";
Assembly assembly = GetType().GetTypeInfo().Assembly;

using (Stream stream = assembly.GetManifestResourceStream(resourceID))
{
    resourceBitmap = SKBitmap.Decode(stream);
}
```

这`Stream`对象可以被直接传递到`SKBitmap.Decode`方法。

## <a name="loading-a-bitmap-from-the-photo-library"></a>从照片库加载位图

还有可能要从设备的图片库加载一张照片的用户。 Xamarin.Forms 本身未提供此功能。 该作业需要一个依赖关系服务，如本文所述[图片库从选取照片](~/xamarin-forms/app-fundamentals/dependency-service/photo-picker.md)。

**IPhotoLibrary.cs**中的文件**SkiaSharpFormsDemos**项目和三个**PhotoLibrary.cs**平台项目中的文件已改编自那篇文章。 此外，Android **MainActivity.cs**文章中所述修改文件和 iOS 项目已被授予权限以访问照片库使用两行代码靠近末尾部分给**info.plist**文件。

`BasicBitmapsPage`构造函数将添加`TapGestureRecognizer`到`SKCanvasView`要通知的分流点。 在点击，收到`Tapped`处理程序获取访问图片选取器依赖关系服务并调用`PickPhotoAsync`。 如果`Stream`返回对象，然后将它传递到`SKBitmap.Decode`方法：

```csharp
// Add tap gesture recognizer
TapGestureRecognizer tapRecognizer = new TapGestureRecognizer();
tapRecognizer.Tapped += async (sender, args) =>
{
    // Load bitmap from photo library
    IPhotoLibrary photoLibrary = DependencyService.Get<IPhotoLibrary>();

    using (Stream stream = await photoLibrary.PickPhotoAsync())
    {
        if (stream != null)
        {
            libraryBitmap = SKBitmap.Decode(stream);
            canvasView.InvalidateSurface();
        }
    }
};
canvasView.GestureRecognizers.Add(tapRecognizer);
```

请注意，`Tapped`处理程序还会调用`InvalidateSurface`方法的`SKCanvasView`对象。 这将生成到新的调用`PaintSurface`处理程序。

## <a name="displaying-the-bitmaps"></a>显示位图

`PaintSurface`显示三个位图所需的处理程序。 该处理程序假定手机处于纵向模式，并且将在画布垂直划分为三个相等部分。

使用最简单的形式显示第一个位图[ `DrawBitmap` ](xref:SkiaSharp.SKCanvas.DrawBitmap(SkiaSharp.SKBitmap,System.Single,System.Single,SkiaSharp.SKPaint))方法。 您需要指定是定位位图左上角的 X 和 Y 坐标：

```csharp
public void DrawBitmap (SKBitmap bitmap, Single x, Single y, SKPaint paint = null)
```

尽管`SKPaint`定义参数，它具有默认值为`null`，可以忽略它。 位图的像素只被转移到具有一对一的映射显示器表面的像素为单位。 可以看到应用程序的这`SKPaint`上的下一步部分中的参数[ **SkiaSharp 透明度**](transparency.md)。

程序可以获取位图的像素尺寸[ `Width` ](xref:SkiaSharp.SKBitmap.Width)并[ `Height` ](xref:SkiaSharp.SKBitmap.Height)属性。 这些属性允许程序计算的坐标，以在画布左上第三个中心位置位图：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    if (webBitmap != null)
    {
        float x = (info.Width - webBitmap.Width) / 2;
        float y = (info.Height / 3 - webBitmap.Height) / 2;
        canvas.DrawBitmap(webBitmap, x, y);
    }
    ...
}
```

其他两个位图显示使用的版本[ `DrawBitmap` ](xref:SkiaSharp.SKCanvas.DrawBitmap(SkiaSharp.SKBitmap,SkiaSharp.SKRect,SkiaSharp.SKPaint))与`SKRect`参数：

```csharp
public void DrawBitmap (SKBitmap bitmap, SKRect dest, SKPaint paint = null)
```

第三个版本[ `DrawBitmap` ](xref:SkiaSharp.SKCanvas.DrawBitmap(SkiaSharp.SKBitmap,SkiaSharp.SKRect,SkiaSharp.SKRect,SkiaSharp.SKPaint))具有两个`SKRect`指定到显示，但该版本的位图的矩形子集的参数不在本文中使用。

下面是代码，以显示从嵌入的资源位图加载位图：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    ...
    if (resourceBitmap != null)
    {
        canvas.DrawBitmap(resourceBitmap,
            new SKRect(0, info.Height / 3, info.Width, 2 * info.Height / 3));
    }
    ...
}
```

位图将拉伸为矩形，这就是原因 monkey 水平拉伸这些屏幕截图中的尺寸：

[![](bitmaps-images/basicbitmaps-small.png "三个基本位图页的屏幕截图")](bitmaps-images/basicbitmaps-large.png#lightbox "三个基本位图页的屏幕截图")

第三个图像&mdash;，仅可看到是否运行该程序，并从你自己的图片库加载一张照片&mdash;还显示在一个矩形，但该矩形的位置和大小调整以保持位图的长宽比。 此计算是更复杂一些，因为它需要计算缩放系数基于位图和目标矩形的大小和集中在该区域的矩形：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    ...
    if (libraryBitmap != null)
    {
        float scale = Math.Min((float)info.Width / libraryBitmap.Width,
                               info.Height / 3f / libraryBitmap.Height);

        float left = (info.Width - scale * libraryBitmap.Width) / 2;
        float top = (info.Height / 3 - scale * libraryBitmap.Height) / 2;
        float right = left + scale * libraryBitmap.Width;
        float bottom = top + scale * libraryBitmap.Height;
        SKRect rect = new SKRect(left, top, right, bottom);
        rect.Offset(0, 2 * info.Height / 3);

        canvas.DrawBitmap(libraryBitmap, rect);
    }
    else
    {
        using (SKPaint paint = new SKPaint())
        {
            paint.Color = SKColors.Blue;
            paint.TextAlign = SKTextAlign.Center;
            paint.TextSize = 48;

            canvas.DrawText("Tap to load bitmap",
                info.Width / 2, 5 * info.Height / 6, paint);
        }
    }
}
```

如果尚未从图片库中，加载任何位图则`else`块显示一些文本以提示用户点击屏幕。

您可以显示具有不同程度的透明性，并在下一篇文章位图[ **SkiaSharp 透明度**](transparency.md)介绍了如何。

## <a name="related-links"></a>相关链接

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
- [从图片库中选取照片](~/xamarin-forms/app-fundamentals/dependency-service/photo-picker.md)
