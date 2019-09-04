---
title: 裁剪 SkiaSharp 位图
description: 了解如何使用 SkiaSharp 来设计用于以交互方式用于裁剪矩形的用户界面。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 0A79AB27-C69F-4376-8FFE-FF46E4783F30
author: davidbritch
ms.author: dabritch
ms.date: 07/17/2018
ms.openlocfilehash: e9ba34dfcbdf041cb9bce7f277da3987acf9fec8
ms.sourcegitcommit: c9651cad80c2865bc628349d30e82721c01ddb4a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2019
ms.locfileid: "70228233"
---
# <a name="cropping-skiasharp-bitmaps"></a>裁剪 SkiaSharp 位图

[![下载示例](~/media/shared/download.png)下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

[**创建和绘制 SkiaSharp 位图**](drawing.md)一文所述方式`SKBitmap`可以将对象传递给`SKCanvas`构造函数。 要在位图上呈现该画布原因图形上调用任何绘图方法。 这些绘制方法包括`DrawBitmap`，这意味着该技术允许将传输部分或全部一个位图到另一个位图，可能与应用的转换。

可以使用该技术为通过调用裁剪位图[ `DrawBitmap` ](xref:SkiaSharp.SKCanvas.DrawBitmap(SkiaSharp.SKBitmap,SkiaSharp.SKRect,SkiaSharp.SKRect,SkiaSharp.SKPaint))与源和目标矩形的方法：

```csharp
canvas.DrawBitmap(bitmap, sourceRect, destRect);
```

但是，实现通常裁剪的应用程序提供一个接口，使用户能够以交互方式选择裁剪矩形：

![裁剪示例](cropping-images/CroppingSample.png "裁剪示例")

本文重点介绍该接口。

## <a name="encapsulating-the-cropping-rectangle"></a>封装裁剪矩形

最好将一个名为类中的裁剪逻辑的一些隔离`CroppingRectangle`。 构造函数参数包括通常是因此会裁剪其位图的大小，最大矩形和可选的纵横比。 构造函数首先定义一个初始的裁剪矩形，这使得在公共`Rect`类型的属性`SKRect`。 此初始裁剪矩形 80%的宽度和高度的位图的矩形中，但如果指定了纵横比，然后调整：

```csharp
class CroppingRectangle
{
    ···
    SKRect maxRect;             // generally the size of the bitmap
    float? aspectRatio;

    public CroppingRectangle(SKRect maxRect, float? aspectRatio = null)
    {
        this.maxRect = maxRect;
        this.aspectRatio = aspectRatio;

        // Set initial cropping rectangle
        Rect = new SKRect(0.9f * maxRect.Left + 0.1f * maxRect.Right,
                          0.9f * maxRect.Top + 0.1f * maxRect.Bottom,
                          0.1f * maxRect.Left + 0.9f * maxRect.Right,
                          0.1f * maxRect.Top + 0.9f * maxRect.Bottom);

        // Adjust for aspect ratio
        if (aspectRatio.HasValue)
        {
            SKRect rect = Rect;
            float aspect = aspectRatio.Value;

            if (rect.Width > aspect * rect.Height)
            {
                float width = aspect * rect.Height;
                rect.Left = (maxRect.Width - width) / 2;
                rect.Right = rect.Left + width;
            }
            else
            {
                float height = rect.Width / aspect;
                rect.Top = (maxRect.Height - height) / 2;
                rect.Bottom = rect.Top + height;
            }

            Rect = rect;
        }
    }
    
    public SKRect Rect { set; get; }
    ···
}
```

一个有用的信息的`CroppingRectangle`，能够也是一个数组`SKPoint`对应于在左上角、 右上、 右下角和左下方的顺序的裁剪矩形的四个角的值：

```csharp
class CroppingRectangle
{
    ···
    public SKPoint[] Corners
    {
        get
        {
            return new SKPoint[]
            {
                new SKPoint(Rect.Left, Rect.Top),
                new SKPoint(Rect.Right, Rect.Top),
                new SKPoint(Rect.Right, Rect.Bottom),
                new SKPoint(Rect.Left, Rect.Bottom)
            };
        }
    }
    ···
}
```

在以下方法调用中使用此数组`HitTest`。 `SKPoint`参数是对应于手指触摸屏输入的点或鼠标单击。 该方法返回一个索引 （0、 1、 2 或 3） 对应于手指或鼠标指针访问，在给定范围内的角`radius`参数： 

```csharp
class CroppingRectangle
{
    ···
    public int HitTest(SKPoint point, float radius)
    {
        SKPoint[] corners = Corners;

        for (int index = 0; index < corners.Length; index++)
        {
            SKPoint diff = point - corners[index];
                
            if ((float)Math.Sqrt(diff.X * diff.X + diff.Y * diff.Y) < radius)
            {
                return index;
            }
        }

        return -1;
    }
    ···
}
```

如果触摸或鼠标点未在`radius`的任何一个角单元，该方法将返回&ndash;1。

在最后一种方法`CroppingRectangle`称为`MoveCorner`，被调用来响应触摸或鼠标移动。 两个参数指示要移动的角和该角的新位置的索引。 该方法的第一个部分调整裁剪矩形的角，但始终的边界内的新位置上基于`maxRect`，它是位图的大小。 此逻辑还会考虑`MINIMUM`字段以避免执行任何操作到折叠裁剪矩形：

```csharp
class CroppingRectangle
{
    const float MINIMUM = 10;   // pixels width or height
    ···
    public void MoveCorner(int index, SKPoint point)
    {
        SKRect rect = Rect;

        switch (index)
        {
            case 0: // upper-left
                rect.Left = Math.Min(Math.Max(point.X, maxRect.Left), rect.Right - MINIMUM);
                rect.Top = Math.Min(Math.Max(point.Y, maxRect.Top), rect.Bottom - MINIMUM);
                break;

            case 1: // upper-right
                rect.Right = Math.Max(Math.Min(point.X, maxRect.Right), rect.Left + MINIMUM);
                rect.Top = Math.Min(Math.Max(point.Y, maxRect.Top), rect.Bottom - MINIMUM);
                break;

            case 2: // lower-right
                rect.Right = Math.Max(Math.Min(point.X, maxRect.Right), rect.Left + MINIMUM);
                rect.Bottom = Math.Max(Math.Min(point.Y, maxRect.Bottom), rect.Top + MINIMUM);
                break;

            case 3: // lower-left
                rect.Left = Math.Min(Math.Max(point.X, maxRect.Left), rect.Right - MINIMUM);
                rect.Bottom = Math.Max(Math.Min(point.Y, maxRect.Bottom), rect.Top + MINIMUM);
                break;
        }

        // Adjust for aspect ratio
        if (aspectRatio.HasValue)
        {
            float aspect = aspectRatio.Value;

            if (rect.Width > aspect * rect.Height)
            {
                float width = aspect * rect.Height;

                switch (index)
                {
                    case 0:
                    case 3: rect.Left = rect.Right - width; break;
                    case 1:
                    case 2: rect.Right = rect.Left + width; break;
                }
            }
            else
            {
                float height = rect.Width / aspect;

                switch (index)
                {
                    case 0:
                    case 1: rect.Top = rect.Bottom - height; break;
                    case 2:
                    case 3: rect.Bottom = rect.Top + height; break;
                }
            }
        }

        Rect = rect;
    }
}
```

该方法的第二部分调整为可选的长宽比。

请记住，此类中的所有内容都是以像素为单位。

## <a name="a-canvas-view-just-for-cropping"></a>只需为裁剪画布视图

`CroppingRectangle`类，你看到由`PhotoCropperCanvasView`类，该类派生自`SKCanvasView`。 此类负责显示位图和裁剪矩形，以及处理更改裁剪矩形的触摸或鼠标事件。

`PhotoCropperCanvasView`构造函数需要一个位图。 纵横比是可选的。 构造函数实例化类型的对象`CroppingRectangle`基于此位图和纵横比，并将其保存为字段：

```csharp
class PhotoCropperCanvasView : SKCanvasView
{
    ···
    SKBitmap bitmap;
    CroppingRectangle croppingRect;
    ···
    public PhotoCropperCanvasView(SKBitmap bitmap, float? aspectRatio = null)
    {
        this.bitmap = bitmap;

        SKRect bitmapRect = new SKRect(0, 0, bitmap.Width, bitmap.Height);
        croppingRect = new CroppingRectangle(bitmapRect, aspectRatio);
        ···
    }
    ···
}
```

因为此类派生自`SKCanvasView`，它不需要安装的处理程序`PaintSurface`事件。 它而是会重写其`OnPaintSurface`方法。 该方法显示位图，并使用几个`SKPaint`对象另存为字段以绘制当前裁剪矩形：

```csharp
class PhotoCropperCanvasView : SKCanvasView
{
    const int CORNER = 50;      // pixel length of cropper corner
    ···
    SKBitmap bitmap;
    CroppingRectangle croppingRect;
    SKMatrix inverseBitmapMatrix;
    ···
    // Drawing objects
    SKPaint cornerStroke = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.White,
        StrokeWidth = 10
    };

    SKPaint edgeStroke = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.White,
        StrokeWidth = 2
    };
    ···
    protected override void OnPaintSurface(SKPaintSurfaceEventArgs args)
    {
        base.OnPaintSurface(args);

        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear(SKColors.Gray);

        // Calculate rectangle for displaying bitmap 
        float scale = Math.Min((float)info.Width / bitmap.Width, (float)info.Height / bitmap.Height);
        float x = (info.Width - scale * bitmap.Width) / 2;
        float y = (info.Height - scale * bitmap.Height) / 2;
        SKRect bitmapRect = new SKRect(x, y, x + scale * bitmap.Width, y + scale * bitmap.Height);
        canvas.DrawBitmap(bitmap, bitmapRect);

        // Calculate a matrix transform for displaying the cropping rectangle
        SKMatrix bitmapScaleMatrix = SKMatrix.MakeIdentity();
        bitmapScaleMatrix.SetScaleTranslate(scale, scale, x, y);

        // Display rectangle
        SKRect scaledCropRect = bitmapScaleMatrix.MapRect(croppingRect.Rect);
        canvas.DrawRect(scaledCropRect, edgeStroke);

        // Display heavier corners
        using (SKPath path = new SKPath())
        {
            path.MoveTo(scaledCropRect.Left, scaledCropRect.Top + CORNER);
            path.LineTo(scaledCropRect.Left, scaledCropRect.Top);
            path.LineTo(scaledCropRect.Left + CORNER, scaledCropRect.Top);

            path.MoveTo(scaledCropRect.Right - CORNER, scaledCropRect.Top);
            path.LineTo(scaledCropRect.Right, scaledCropRect.Top);
            path.LineTo(scaledCropRect.Right, scaledCropRect.Top + CORNER);

            path.MoveTo(scaledCropRect.Right, scaledCropRect.Bottom - CORNER);
            path.LineTo(scaledCropRect.Right, scaledCropRect.Bottom);
            path.LineTo(scaledCropRect.Right - CORNER, scaledCropRect.Bottom);

            path.MoveTo(scaledCropRect.Left + CORNER, scaledCropRect.Bottom);
            path.LineTo(scaledCropRect.Left, scaledCropRect.Bottom);
            path.LineTo(scaledCropRect.Left, scaledCropRect.Bottom - CORNER);

            canvas.DrawPath(path, cornerStroke);
        }

        // Invert the transform for touch tracking
        bitmapScaleMatrix.TryInvert(out inverseBitmapMatrix);
    }
    ···
}
```

中的代码`CroppingRectangle`类根据在位图的像素大小根据裁剪矩形。 但是，通过位图的显示`PhotoCropperCanvasView`类根据缩放的显示区域的大小。 `bitmapScaleMatrix`计算中`OnPaintSurface`重写从位图像素映射到的大小和位图的位置，将显示。 然后使用此矩阵转换裁剪矩形，以便它可以显示相对于位图。

最后一行`OnPaintSurface`重写接收函数的反函数`bitmapScaleMatrix`并将其保存为`inverseBitmapMatrix`字段。 这用于触摸处理。

一个`TouchEffect`为字段，实例化对象和构造函数附加到一个处理程序`TouchAction`事件，但`TouchEffect`需要添加到`Effects`的集合_父_的`SKCanvasView`派生，以便完成`OnParentSet`重写：

```csharp
class PhotoCropperCanvasView : SKCanvasView
{
    ···
    const int RADIUS = 100;     // pixel radius of touch hit-test
    ···
    CroppingRectangle croppingRect;
    SKMatrix inverseBitmapMatrix;

    // Touch tracking 
    TouchEffect touchEffect = new TouchEffect();
    struct TouchPoint
    {
        public int CornerIndex { set; get; }
        public SKPoint Offset { set; get; }
    }

    Dictionary<long, TouchPoint> touchPoints = new Dictionary<long, TouchPoint>();
    ···
    public PhotoCropperCanvasView(SKBitmap bitmap, float? aspectRatio = null)
    {
        ···
        touchEffect.TouchAction += OnTouchEffectTouchAction;
    }
    ···
    protected override void OnParentSet()
    {
        base.OnParentSet();

        // Attach TouchEffect to parent view
        Parent.Effects.Add(touchEffect);
    }
    ···
    void OnTouchEffectTouchAction(object sender, TouchActionEventArgs args)
    {
        SKPoint pixelLocation = ConvertToPixel(args.Location);
        SKPoint bitmapLocation = inverseBitmapMatrix.MapPoint(pixelLocation);

        switch (args.Type)
        {
            case TouchActionType.Pressed:
                // Convert radius to bitmap/cropping scale
                float radius = inverseBitmapMatrix.ScaleX * RADIUS;

                // Find corner that the finger is touching
                int cornerIndex = croppingRect.HitTest(bitmapLocation, radius);

                if (cornerIndex != -1 && !touchPoints.ContainsKey(args.Id))
                {
                    TouchPoint touchPoint = new TouchPoint
                    {
                        CornerIndex = cornerIndex,
                        Offset = bitmapLocation - croppingRect.Corners[cornerIndex]
                    };

                    touchPoints.Add(args.Id, touchPoint);
                }
                break;

            case TouchActionType.Moved:
                if (touchPoints.ContainsKey(args.Id))
                {
                    TouchPoint touchPoint = touchPoints[args.Id];
                    croppingRect.MoveCorner(touchPoint.CornerIndex, 
                                            bitmapLocation - touchPoint.Offset);
                    InvalidateSurface();
                }
                break;

            case TouchActionType.Released:
            case TouchActionType.Cancelled:
                if (touchPoints.ContainsKey(args.Id))
                {
                    touchPoints.Remove(args.Id);
                }
                break;
        }
    }

    SKPoint ConvertToPixel(Xamarin.Forms.Point pt)
    {
        return new SKPoint((float)(CanvasSize.Width * pt.X / Width),
                           (float)(CanvasSize.Height * pt.Y / Height));
    }
}
```

触摸事件处理的`TouchAction`处理程序是以独立于设备的单位。 首先需要转换为像素，且采用这些`ConvertToPixel`方法底部的类，然后转换为`CroppingRectangle`单位使用`inverseBitmapMatrix`。

有关`Pressed`事件，`TouchAction`处理程序调用`HitTest`方法的`CroppingRectangle`。 如果这不是返回索引&ndash;1，然后裁剪矩形的角之一对其进行操作。 存储在索引和偏移量为实际触摸点从角`TouchPoint`对象，并添加到`touchPoints`字典。

有关`Moved`事件，`MoveCorner`方法的`CroppingRectangle`调用以移动的角，并可能进行调整的纵横比。

在任何时间，程序使用`PhotoCropperCanvasView`可以访问`CroppedBitmap`属性。 此属性使用`Rect`属性的`CroppingRectangle`若要创建新的位图的裁剪后的大小。 版本的`DrawBitmap`与目标和源矩形然后提取原始位图的子集：

```csharp
class PhotoCropperCanvasView : SKCanvasView
{
    ···
    SKBitmap bitmap;
    CroppingRectangle croppingRect;
    ···
    public SKBitmap CroppedBitmap
    {
        get
        {
            SKRect cropRect = croppingRect.Rect;
            SKBitmap croppedBitmap = new SKBitmap((int)cropRect.Width, 
                                                  (int)cropRect.Height);
            SKRect dest = new SKRect(0, 0, cropRect.Width, cropRect.Height);
            SKRect source = new SKRect(cropRect.Left, cropRect.Top, 
                                       cropRect.Right, cropRect.Bottom);

            using (SKCanvas canvas = new SKCanvas(croppedBitmap))
            {
                canvas.DrawBitmap(bitmap, source, dest);
            }

            return croppedBitmap;
        }
    }
    ···
}
```

## <a name="hosting-the-photo-cropper-canvas-view"></a>承载照片 cropper 画布视图

与处理裁剪的逻辑，这两个类**裁剪照片**页面 **[SkiaSharpFormsDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)** 应用程序具有很少要执行的任务。 XAML 文件实例化`Grid`到主机`PhotoCropperCanvasView`和一个**完成**按钮：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="SkiaSharpFormsDemos.Bitmaps.PhotoCroppingPage"
             Title="Photo Cropping">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="*" />
            <RowDefinition Height="Auto" />
        </Grid.RowDefinitions>

        <Grid x:Name="canvasViewHost"
              Grid.Row="0"
              BackgroundColor="Gray"
              Padding="5" />

        <Button Text="Done"
                Grid.Row="1"
                HorizontalOptions="Center"
                Margin="5"
                Clicked="OnDoneButtonClicked" />
    </Grid>
</ContentPage>
```

`PhotoCropperCanvasView`无法实例化的 XAML 文件中因为它需要的类型参数`SKBitmap`。

相反，`PhotoCropperCanvasView`使用一个资源位图的代码隐藏文件的构造函数中实例化：

```csharp
public partial class PhotoCroppingPage : ContentPage
{
    PhotoCropperCanvasView photoCropper;
    SKBitmap croppedBitmap;

    public PhotoCroppingPage ()
    {
        InitializeComponent ();

        SKBitmap bitmap = BitmapExtensions.LoadBitmapResource(GetType(),
            "SkiaSharpFormsDemos.Media.MountainClimbers.jpg");

        photoCropper = new PhotoCropperCanvasView(bitmap);
        canvasViewHost.Children.Add(photoCropper);
    }

    void OnDoneButtonClicked(object sender, EventArgs args)
    {
        croppedBitmap = photoCropper.CroppedBitmap;

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
        canvas.DrawBitmap(croppedBitmap, info.Rect, BitmapStretch.Uniform);
    }
}
```

然后，用户可以操作裁剪矩形：

[![照片 Cropper 1](cropping-images/PhotoCropping1.png "照片 Cropper 1")](cropping-images/PhotoCropping1-Large.png#lightbox)

已定义好的裁剪矩形，请单击**完成**按钮。 `Clicked`处理程序获取从裁剪后的位图`CroppedBitmap`的属性`PhotoCropperCanvasView`，并将替换一个新的页的所有内容`SKCanvasView`对象，它显示此裁剪后的位图：

[![照片 Cropper 2](cropping-images/PhotoCropping2.png "照片 Cropper 2")](cropping-images/PhotoCropping2-Large.png#lightbox)

尝试设置的第二个参数`PhotoCropperCanvasView`到 1.78f （例如）：

```csharp
photoCropper = new PhotoCropperCanvasView(bitmap, 1.78f);
```

你将看到限制为 16 9 纵横比为特征的高清晰电视的裁剪矩形。

<a name="tile-division" />

## <a name="dividing-a-bitmap-into-tiles"></a>将位图划分为磁贴

著名的 Xamarin.Forms 版本 14-15 个填数游戏出现在书的第 22 章[_使用 Xamarin.Forms 创建移动应用_](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md)并且可以作为下载[ **XamagonXuzzle**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/XamagonXuzzle)。 但是，填数游戏变得更加有趣 （且通常更具挑战性） 当基于照片库中的映像。

14-15 个填数游戏的此版本属于 **[SkiaSharpFormsDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)** 应用程序，并包含一系列页面标题为**照片拼图**。

**PhotoPuzzlePage1.xaml**文件都包含`Button`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="SkiaSharpFormsDemos.Bitmaps.PhotoPuzzlePage1"
             Title="Photo Puzzle">
    
    <Button Text="Pick a photo from your library"
            VerticalOptions="CenterAndExpand" 
            HorizontalOptions="CenterAndExpand"
            Clicked="OnPickButtonClicked"/>
    
</ContentPage>
```

代码隐藏文件实现`Clicked`处理程序，它使用`IPhotoLibrary`依赖关系服务，让用户从照片库中选择一张照片：

```csharp
public partial class PhotoPuzzlePage1 : ContentPage
{
    public PhotoPuzzlePage1 ()
    {
        InitializeComponent ();
    }

    async void OnPickButtonClicked(object sender, EventArgs args)
    {
        IPhotoLibrary photoLibrary = DependencyService.Get<IPhotoLibrary>();
        using (Stream stream = await photoLibrary.PickPhotoAsync())
        {
            if (stream != null)
            {
                SKBitmap bitmap = SKBitmap.Decode(stream);

                await Navigation.PushAsync(new PhotoPuzzlePage2(bitmap));
            }
        }
    }
}
```

该方法然后导航到`PhotoPuzzlePage2`，并传递到构造函数所选的位图。

它可能是，从库中选择的照片不面向它出现在照片库中，但是旋转或倒置。 （这是与 iOS 设备存在问题）。为此，`PhotoPuzzlePage2`可以旋转为所需方向图像。 XAML 文件包含三个按钮标记为**90&#x00B0;右**（沿顺时针方向表示的）， **90&#x00B0;左侧**（开始沿逆时针方向），和**完成**。

代码隐藏文件实现本文中所示的位图旋转逻辑 **[创建和上 SkiaSharp 位图绘制](drawing.md#rotating-bitmaps)** 。 用户可以旋转图像顺时针或逆时针旋转 90 度任意次数： 

```csharp
public partial class PhotoPuzzlePage2 : ContentPage
{
    SKBitmap bitmap;

    public PhotoPuzzlePage2 (SKBitmap bitmap)
    {
        this.bitmap = bitmap;

        InitializeComponent ();
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();
        canvas.DrawBitmap(bitmap, info.Rect, BitmapStretch.Uniform);
    }

    void OnRotateRightButtonClicked(object sender, EventArgs args)
    {
        SKBitmap rotatedBitmap = new SKBitmap(bitmap.Height, bitmap.Width);

        using (SKCanvas canvas = new SKCanvas(rotatedBitmap))
        {
            canvas.Clear();
            canvas.Translate(bitmap.Height, 0);
            canvas.RotateDegrees(90);
            canvas.DrawBitmap(bitmap, new SKPoint());
        }

        bitmap = rotatedBitmap;
        canvasView.InvalidateSurface();
    }

    void OnRotateLeftButtonClicked(object sender, EventArgs args)
    {
        SKBitmap rotatedBitmap = new SKBitmap(bitmap.Height, bitmap.Width);

        using (SKCanvas canvas = new SKCanvas(rotatedBitmap))
        {
            canvas.Clear();
            canvas.Translate(0, bitmap.Width);
            canvas.RotateDegrees(-90);
            canvas.DrawBitmap(bitmap, new SKPoint());
        }

        bitmap = rotatedBitmap;
        canvasView.InvalidateSurface();
    }

    async void OnDoneButtonClicked(object sender, EventArgs args)
    {
        await Navigation.PushAsync(new PhotoPuzzlePage3(bitmap));
    }
}
```

当用户单击**完成**按钮，`Clicked`处理程序导航到`PhotoPuzzlePage3`，在该页的构造函数中传递的最后一个旋转的位图。

`PhotoPuzzlePage3` 允许照片将其剪切。 该程序所需的方形的位图将划分为 4-4 网格的磁贴。

**PhotoPuzzlePage3.xaml**文件包含`Label`即`Grid`到主机`PhotoCropperCanvasView`，，另一个**完成**按钮：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="SkiaSharpFormsDemos.Bitmaps.PhotoPuzzlePage3"
             Title="Photo Puzzle">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="*" />
            <RowDefinition Height="Auto" />
        </Grid.RowDefinitions>

        <Label Text="Crop the photo to a square"
               Grid.Row="0"
               FontSize="Large"
               HorizontalTextAlignment="Center"
               Margin="5" />

        <Grid x:Name="canvasViewHost"
              Grid.Row="1"
              BackgroundColor="Gray"
              Padding="5" />

        <Button Text="Done"
                Grid.Row="2"
                HorizontalOptions="Center"
                Margin="5"
                Clicked="OnDoneButtonClicked" />
    </Grid>
</ContentPage>
```

实例化的代码隐藏文件`PhotoCropperCanvasView`与位图传递给其构造函数。 请注意，作为第二个参数传递 1 `PhotoCropperCanvasView`。 此为 1 的纵横比会强制为正方形的裁剪矩形：

```csharp
public partial class PhotoPuzzlePage3 : ContentPage
{
    PhotoCropperCanvasView photoCropper;

    public PhotoPuzzlePage3(SKBitmap bitmap)
    {
        InitializeComponent ();

        photoCropper = new PhotoCropperCanvasView(bitmap, 1f);
        canvasViewHost.Children.Add(photoCropper);
    }

    async void OnDoneButtonClicked(object sender, EventArgs args)
    {
        SKBitmap croppedBitmap = photoCropper.CroppedBitmap;
        int width = croppedBitmap.Width / 4;
        int height = croppedBitmap.Height / 4;

        ImageSource[] imgSources = new ImageSource[15];

        for (int row = 0; row < 4; row++)
        {
            for (int col = 0; col < 4; col++)
            {
                // Skip the last one!
                if (row == 3 && col == 3)
                    break;

                // Create a bitmap 1/4 the width and height of the original
                SKBitmap bitmap = new SKBitmap(width, height);
                SKRect dest = new SKRect(0, 0, width, height);
                SKRect source = new SKRect(col * width, row * height, (col + 1) * width, (row + 1) * height);

                // Copy 1/16 of the original into that bitmap
                using (SKCanvas canvas = new SKCanvas(bitmap))
                {
                    canvas.DrawBitmap(croppedBitmap, source, dest);
                }

                imgSources[4 * row + col] = (SKBitmapImageSource)bitmap;
            }
        }

        await Navigation.PushAsync(new PhotoPuzzlePage4(imgSources));
    }
}
```

**完成**按钮处理程序获取的宽度和高度的裁剪后的位图 （这两个值应为相同），然后将其划分为 15 单独的位图，其中每个为 1/4 的宽度和高度的原始。 （不创建可能 16 位图的最后一个。）`DrawBitmap`源和目标矩形的方法，将基于较大的位图的子集创建的位图。

## <a name="converting-to-xamarinforms-bitmaps"></a>将转换为 Xamarin.Forms 位图

在中`OnDoneButtonClicked`方法中，为 15 的位图创建的数组是类型[ `ImageSource` ](xref:Xamarin.Forms.ImageSource):

```csharp
ImageSource[] imgSources = new ImageSource[15];
```

`ImageSource` 是封装位图的 Xamarin.Forms 基类型。 幸运的是，SkiaSharp 允许从 SkiaSharp 位图转换为 Xamarin.Forms 位图。 **SkiaSharp.Views.Forms**程序集定义[ `SKBitmapImageSource` ](xref:SkiaSharp.Views.Forms.SKBitmapImageSource)派生的类`ImageSource`但可以创建基于 SkiaSharp`SKBitmap`对象。 `SKBitmapImageSource` 甚至定义之间的转换`SKBitmapImageSource`并`SKBitmap`，这如何`SKBitmap`对象存储在数组作为 Xamarin.Forms 位图：

```csharp
imgSources[4 * row + col] = (SKBitmapImageSource)bitmap;
```

位图的此数组传递到构造函数为`PhotoPuzzlePage4`。 该页面完全 Xamarin.Forms，并且不使用任何 SkiaSharp。 它是非常类似于[ **XamagonXuzzle**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/XamagonXuzzle)，因此不会在这里，所述，但它将显示所选的照片分成 15 正方形磁贴：

[![照片填数游戏 1](cropping-images/PhotoPuzzle1.png "照片填数游戏 1")](cropping-images/PhotoPuzzle1-Large.png#lightbox)

按下**随机化**按钮混合使用了所有磁贴：

[![照片填数游戏 2](cropping-images/PhotoPuzzle2.png "照片填数游戏 2")](cropping-images/PhotoPuzzle2-Large.png#lightbox)

现在可以将它们放在正确的顺序返回。 在同一行或列作为空方块中的所有磁贴可以点击以将其移至空的方块。 

## <a name="related-links"></a>相关链接

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
