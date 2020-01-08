---
title: SkiaSharp 中手指绘画
description: 此文介绍了如何使用手指在 Xamarin.Forms 应用程序中，在 SkiaSharp 画布上绘制，此示例代码进行了演示。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 56929D74-8F2C-44C6-90E6-3FBABCDC0A4B
author: davidbritch
ms.author: dabritch
ms.date: 04/05/2017
ms.openlocfilehash: 98809dca503d03d3d592161608fa9a6fa0e5d394
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75488343"
---
# <a name="finger-painting-in-skiasharp"></a>SkiaSharp 中手指绘画

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_使用手指在画布上绘制。_

`SKPath`可以持续更新并显示对象。 此功能允许以进行交互式绘图，如程序中使用手指绘制的路径。

![](finger-paint-images/fingerpaintsample.png "An exercise in finger painting")

在 Xamarin.Forms 中的触摸支持不允许跟踪各手指在屏幕上，因此已开发 Xamarin.Forms 触控跟踪效果以提供更多点触控支持。 这种效果本文所述[**效果从调用事件**](~/xamarin-forms/app-fundamentals/effects/touch-tracking.md)。 示例程序[**触控跟踪效果演示**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/effects-touchtrackingeffect/)包括使用 SkiaSharp，包括一个手指绘制程序的两个页面。

[ **SkiaSharpFormsDemos** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)解决方案包括此点触控跟踪事件。 .NET Standard 库项目中包括`TouchEffect`类，`TouchActionType`枚举`TouchActionEventHandler`委托，和`TouchActionEventArgs`类。 每个平台项目都包括`TouchEffect`类用于该平台; iOS 项目还包含`TouchRecognizer`类。

**手指绘制**页面**SkiaSharpFormsDemos**是手指绘画的简化的实现。 它不允许选择颜色或宽度进行描边，则具有无法清除画布上，而且当然不能保存您的作品。

[ **FingerPaintPage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/LinesAndPaths/FingerPaintPage.xaml)文件 put`SKCanvasView`中单个单元格`Grid`并将其附加`TouchEffect`以便`Grid`:

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

附加`TouchEffect`直接向`SKCanvasView`不所有平台都工作。

[ **FingerPaintPage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Paths/FingerPaintPage.xaml.cs)代码分离文件定义了两个集合用于存储`SKPath`对象，以及`SKPaint`对象用于呈现这些路径：

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

顾名思义，`inProgressPaths`字典将存储当前正在由一个或多个手指绘制的路径。 字典的键是附带触控事件的触摸屏输入 ID。 `completedPaths`字段是已完成路径已绘制一个手指从屏幕上提起后的路径的集合。

`TouchAction`处理程序管理这两个集合。 当手指首先触摸屏幕，一个新`SKPath`添加到`inProgressPaths`。 当这根手指移动时，额外的点将添加到路径。 在手指松开，路径将转移到`completedPaths`集合。 可以同时绘制与多个手指。 指向某个路径或集合，每次更改后`SKCanvasView`失效：

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

伴随的触摸屏输入跟踪事件的点是 Xamarin.Forms 坐标;这些必须转换为 SkiaSharp 坐标是像素。 目的就在于此`ConvertToPixel`方法。

`PaintSurface`处理程序然后只需呈现这两个集合的路径。 正在进行中的路径的下方显示前面的完整的路径：

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

现在已了解如何绘制的线条以及如何定义使用参数化的等式的曲线。 在后面的部分[ **SkiaSharp 曲线和路径**](../curves/index.md)介绍了各种类型的曲线的`SKPath`支持。 但有用的先决条件是探讨[ **SkiaSharp 转换**](../transforms/index.md)。

## <a name="related-links"></a>相关链接

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
- [触控跟踪效果演示 （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/effects-touchtrackingeffect/)
- [调用效果中的事件](~/xamarin-forms/app-fundamentals/effects/touch-tracking.md)
