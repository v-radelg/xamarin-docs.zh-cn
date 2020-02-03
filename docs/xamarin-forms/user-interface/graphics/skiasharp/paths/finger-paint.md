---
title: SkiaSharp 中手指绘画
description: 此文介绍了如何使用手指在 Xamarin.Forms 应用程序中，在 SkiaSharp 画布上绘制，此示例代码进行了演示。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 56929D74-8F2C-44C6-90E6-3FBABCDC0A4B
author: davidbritch
ms.author: dabritch
ms.date: 04/05/2017
ms.openlocfilehash: d5cf0927c64732d6d0a44204db9509fae77f0d1d
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76724071"
---
# <a name="finger-painting-in-skiasharp"></a>SkiaSharp 中手指绘画

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_使用手指在画布上绘制。_

可以持续更新和显示 `SKPath` 对象。 此功能允许以进行交互式绘图，如程序中使用手指绘制的路径。

![](finger-paint-images/fingerpaintsample.png "An exercise in finger painting")

在 Xamarin.Forms 中的触摸支持不允许跟踪各手指在屏幕上，因此已开发 Xamarin.Forms 触控跟踪效果以提供更多点触控支持。 此效果在[**从效果中调用事件**](~/xamarin-forms/app-fundamentals/effects/touch-tracking.md)一文中进行了介绍。 示例程序[**触摸跟踪效果演示**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/effects-touchtrackingeffect/)包含使用 SkiaSharp 的两个页面，其中包括一个手指刷程序。

[**SkiaSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)解决方案包括此触摸跟踪事件。 .NET Standard 库项目包括 `TouchEffect` 类、`TouchActionType` 枚举、`TouchActionEventHandler` 委托和 `TouchActionEventArgs` 类。 每个平台项目都包含一个适用于该平台的 `TouchEffect` 类;iOS 项目还包含一个 `TouchRecognizer` 类。

**SkiaSharpFormsDemos**中的**finger 绘图**页是一种简化的 finger 绘制实现。 它不允许选择颜色或宽度进行描边，则具有无法清除画布上，而且当然不能保存您的作品。

[**FingerPaintPage**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Paths/FingerPaintPage.xaml)文件将 `SKCanvasView` 置于单单元 `Grid` 中，并将 `TouchEffect` 附加到该 `Grid`：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             xmlns:tt="clr-namespace:TouchTracking"
             x:Class="SkiaSharpFormsDemos.Paths.FingerPaintPage"
             Title="Finger Paint">

    <Grid BackgroundColor="White">
        <skia:SKCanvasView x:Name="canvasView"
                           PaintSurface="OnCanvasViewPaintSurface" />
        <Grid.Effects>
            <tt:TouchEffect Capture="True"
                            TouchAction="OnTouchEffectAction" />
        </Grid.Effects>
    </Grid>
</ContentPage>
```

将 `TouchEffect` 直接附加到 `SKCanvasView` 不适用于所有平台。

[**FingerPaintPage.xaml.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Paths/FingerPaintPage.xaml.cs)代码隐藏文件定义了两个用于存储 `SKPath` 对象的集合，以及用于呈现这些路径的 `SKPaint` 对象：

```csharp
public partial class FingerPaintPage : ContentPage
{
    Dictionary<long, SKPath> inProgressPaths = new Dictionary<long, SKPath>();
    List<SKPath> completedPaths = new List<SKPath>();

    SKPaint paint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Blue,
        StrokeWidth = 10,
        StrokeCap = SKStrokeCap.Round,
        StrokeJoin = SKStrokeJoin.Round
    };

    public FingerPaintPage()
    {
        InitializeComponent();
    }
    ...
}
```

顾名思义，`inProgressPaths` 字典存储当前由一个或多个手指绘制的路径。 字典的键是附带触控事件的触摸屏输入 ID。 "`completedPaths`" 字段是一组路径的集合，这些路径在绘制从屏幕提升的路径时已完成。

`TouchAction` 处理程序管理这两个集合。 当手指首次触摸屏幕时，将向 `inProgressPaths`中添加新的 `SKPath`。 当这根手指移动时，额外的点将添加到路径。 当指针被释放时，路径会传输到 `completedPaths` 集合。 可以同时绘制与多个手指。 每次更改路径或集合后，`SKCanvasView` 会失效：

```csharp
public partial class FingerPaintPage : ContentPage
{
    ...
    void OnTouchEffectAction(object sender, TouchActionEventArgs args)
    {
        switch (args.Type)
        {
            case TouchActionType.Pressed:
                if (!inProgressPaths.ContainsKey(args.Id))
                {
                    SKPath path = new SKPath();
                    path.MoveTo(ConvertToPixel(args.Location));
                    inProgressPaths.Add(args.Id, path);
                    canvasView.InvalidateSurface();
                }
                break;

            case TouchActionType.Moved:
                if (inProgressPaths.ContainsKey(args.Id))
                {
                    SKPath path = inProgressPaths[args.Id];
                    path.LineTo(ConvertToPixel(args.Location));
                    canvasView.InvalidateSurface();
                }
                break;

            case TouchActionType.Released:
                if (inProgressPaths.ContainsKey(args.Id))
                {
                    completedPaths.Add(inProgressPaths[args.Id]);
                    inProgressPaths.Remove(args.Id);
                    canvasView.InvalidateSurface();
                }
                break;

            case TouchActionType.Cancelled:
                if (inProgressPaths.ContainsKey(args.Id))
                {
                    inProgressPaths.Remove(args.Id);
                    canvasView.InvalidateSurface();
                }
                break;
        }
    }
    ...
    SKPoint ConvertToPixel(Point pt)
    {
        return new SKPoint((float)(canvasView.CanvasSize.Width * pt.X / canvasView.Width),
                           (float)(canvasView.CanvasSize.Height * pt.Y / canvasView.Height));
    }
}
```

伴随的触摸屏输入跟踪事件的点是 Xamarin.Forms 坐标;这些必须转换为 SkiaSharp 坐标是像素。 这就是 `ConvertToPixel` 方法的用途。

然后 `PaintSurface` 处理程序只呈现两个路径集合。 正在进行中的路径的下方显示前面的完整的路径：

```csharp
public partial class FingerPaintPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKCanvas canvas = args.Surface.Canvas;
        canvas.Clear();

        foreach (SKPath path in completedPaths)
        {
            canvas.DrawPath(path, paint);
        }

        foreach (SKPath path in inProgressPaths.Values)
        {
            canvas.DrawPath(path, paint);
        }
    }
    ...
}
```

手指绘画仅受到您的才能：

[![](finger-paint-images/fingerpaint-small.png "Triple screenshot of the Finger Paint page")](finger-paint-images/fingerpaint-large.png#lightbox "Triple screenshot of the Finger Paint page")

现在已了解如何绘制的线条以及如何定义使用参数化的等式的曲线。 后面的[**SkiaSharp 曲线和轨迹**](../curves/index.md)部分介绍 `SKPath` 支持的各种曲线类型。 但有一个有用的先决条件就是探索[**SkiaSharp 转换**](../transforms/index.md)。

## <a name="related-links"></a>相关链接

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
- [触摸跟踪效果演示（示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/effects-touchtrackingeffect/)
- [从效果调用事件](~/xamarin-forms/app-fundamentals/effects/touch-tracking.md)
