---
title: Xamarin 中的多点触控 Finger 跟踪
description: 本文档介绍如何在 Xamarin iOS 应用程序中跟踪多点触控笔势中的单个手指。 它围绕手指绘制应用程序示例。
ms.prod: xamarin
ms.assetid: 48E8B20D-0833-43D2-976A-0605DDB386E3
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/18/2017
ms.openlocfilehash: c3998424c8f4e9482a41e2891e65f0d13d8ac2f3
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73009178"
---
# <a name="multi-touch-finger-tracking-in-xamarinios"></a>Xamarin 中的多点触控 Finger 跟踪

_本文档演示如何从多个手指跟踪触摸事件_

有时，多点触控应用程序需要在屏幕上同时移动时跟踪各个手指。 一个典型的应用程序就是一个手指画图程序。 您希望用户能够用一个手指绘图，还可以一次使用多个手指进行绘制。 当程序处理多个触控事件时，它需要区分这两个手指。

当手指首次触摸屏幕时，iOS 将为该手指创建一个[`UITouch`](xref:UIKit.UITouch)对象。 此对象保持与手指在屏幕上移动的相同位置，然后从屏幕中抬起，此时对象被释放。 为了跟踪手指，程序应避免直接存储此 `UITouch` 对象。 相反，它可以使用类型 `IntPtr` 的[`Handle`](xref:Foundation.NSObject.Handle)属性来唯一标识这些 `UITouch` 对象。

几乎始终会有一次跟踪单个手指的程序维护用于触摸跟踪的字典。 对于 iOS 程序，字典键是用于标识特定手指的 `Handle` 值。 字典值依赖于应用程序。 在[FingerPaint](https://docs.microsoft.com/samples/xamarin/ios-samples/applicationfundamentals-fingerpaint)程序中，每个 finger 笔划（从触控到 release）都与一个对象相关联，该对象包含用该手指绘制的线条所需的所有信息。 程序出于此目的定义了一个小型 `FingerPaintPolyline` 类：

```csharp
class FingerPaintPolyline
{
    public FingerPaintPolyline()
    {
        Path = new CGPath();
    }

    public CGColor Color { set; get; }

    public float StrokeWidth { set; get; }

    public CGPath Path { private set; get; }
}
```

每个折线都有一种颜色、描边宽度和一个 iOS 图形[`CGPath`](xref:CoreGraphics.CGPath)对象，可在绘制直线时聚合并渲染其多个点。

下面显示的所有代码都包含在名为 `FingerPaintCanvasView``UIView` 导数中。 此类在当前由一个或多个手指绘制的情况中维护类型为 `FingerPaintPolyline` 对象的字典：

```csharp
Dictionary<IntPtr, FingerPaintPolyline> inProgressPolylines = new Dictionary<IntPtr, FingerPaintPolyline>();
```

此字典允许视图根据 `UITouch` 对象的 `Handle` 属性，快速获取与每个手指关联的 `FingerPaintPolyline` 信息。

`FingerPaintCanvasView` 类还会为已完成的折线维护一个 `List` 对象：

```csharp
List<FingerPaintPolyline> completedPolylines = new List<FingerPaintPolyline>();
```

此 `List` 中的对象的顺序与它们的绘制顺序相同。

`FingerPaintCanvasView` 覆盖 `View`定义的五个方法：

- [`TouchesBegan`](xref:UIKit.UIResponder.TouchesBegan(Foundation.NSSet,UIKit.UIEvent))
- [`TouchesMoved`](xref:UIKit.UIResponder.TouchesMoved(Foundation.NSSet,UIKit.UIEvent))
- [`TouchesEnded`](xref:UIKit.UIResponder.TouchesEnded(Foundation.NSSet,UIKit.UIEvent))
- [`TouchesCancelled`](xref:UIKit.UIResponder.TouchesCancelled(Foundation.NSSet,UIKit.UIEvent))
- [`Draw`](xref:UIKit.UIView.Draw(CoreGraphics.CGRect))

各种 `Touches` 重写将累积构成折线的点。

[`Draw`] 重写将绘制已完成的折线，然后绘制正在进行的折线：

```csharp
public override void Draw(CGRect rect)
{
    base.Draw(rect);

    using (CGContext context = UIGraphics.GetCurrentContext())
    {
        // Stroke settings
        context.SetLineCap(CGLineCap.Round);
        context.SetLineJoin(CGLineJoin.Round);

        // Draw the completed polylines
        foreach (FingerPaintPolyline polyline in completedPolylines)
        {
            context.SetStrokeColor(polyline.Color);
            context.SetLineWidth(polyline.StrokeWidth);
            context.AddPath(polyline.Path);
            context.DrawPath(CGPathDrawingMode.Stroke);
        }

        // Draw the in-progress polylines
        foreach (FingerPaintPolyline polyline in inProgressPolylines.Values)
        {
            context.SetStrokeColor(polyline.Color);
            context.SetLineWidth(polyline.StrokeWidth);
            context.AddPath(polyline.Path);
            context.DrawPath(CGPathDrawingMode.Stroke);
        }
    }
}
```

每个 `Touches` 重写都可能会报告多个手指的操作，这些操作由存储在 `touches` 参数中的一个或多个 `UITouch` 对象指示到方法。 `TouchesBegan` 重写通过这些对象的循环。 对于每个 `UITouch` 对象，方法创建并初始化新的 `FingerPaintPolyline` 对象，包括存储从 `LocationInView` 方法获取的指针的初始位置。 使用 `UITouch` 对象的 `Handle` 属性作为字典键，将此 `FingerPaintPolyline` 对象添加到 `InProgressPolylines` 字典：

```csharp
public override void TouchesBegan(NSSet touches, UIEvent evt)
{
    base.TouchesBegan(touches, evt);

    foreach (UITouch touch in touches.Cast<UITouch>())
    {
        // Create a FingerPaintPolyline, set the initial point, and store it
        FingerPaintPolyline polyline = new FingerPaintPolyline
        {
            Color = StrokeColor,
            StrokeWidth = StrokeWidth,
        };

        polyline.Path.MoveToPoint(touch.LocationInView(this));
        inProgressPolylines.Add(touch.Handle, polyline);
    }
    SetNeedsDisplay();
}
```

方法通过调用 `SetNeedsDisplay` 生成对 `Draw` 重写的调用并更新屏幕来结束。

手指或手指在屏幕上移动时，`View` 获取对其 `TouchesMoved` 重写的多次调用。 这会重写循环遍历 `touches` 参数中存储的 `UITouch` 对象，并将手指的当前位置添加到图形路径：

```csharp
public override void TouchesMoved(NSSet touches, UIEvent evt)
{
    base.TouchesMoved(touches, evt);

    foreach (UITouch touch in touches.Cast<UITouch>())
    {
        // Add point to path
        inProgressPolylines[touch.Handle].Path.AddLineToPoint(touch.LocationInView(this));
    }
    SetNeedsDisplay();
}
```

`touches` 集合仅包含自上次调用 `TouchesBegan` 或 `TouchesMoved`后已移动的手指 `UITouch` 对象。 如果需要 `UITouch` 对象与屏幕当前正在联系的*所有*手指相关，则可以通过方法的 `UIEvent` 参数的 `AllTouches` 属性获取该信息。

`TouchesEnded` 重写有两个作业。 它必须向图形路径添加最后一个点，并将 `FingerPaintPolyline` 对象从 `inProgressPolylines` 字典传输到 `completedPolylines` 列表中：

```csharp
public override void TouchesEnded(NSSet touches, UIEvent evt)
{
    base.TouchesEnded(touches, evt);

    foreach (UITouch touch in touches.Cast<UITouch>())
    {
        // Get polyline from dictionary and remove it from dictionary
        FingerPaintPolyline polyline = inProgressPolylines[touch.Handle];
        inProgressPolylines.Remove(touch.Handle);

        // Add final point to path and save with completed polylines
        polyline.Path.AddLineToPoint(touch.LocationInView(this));
        completedPolylines.Add(polyline);
    }
    SetNeedsDisplay();
}
```

只需放弃字典中的 `FingerPaintPolyline` 对象即可处理 `TouchesCancelled` 重写：

```csharp
public override void TouchesCancelled(NSSet touches, UIEvent evt)
{
    base.TouchesCancelled(touches, evt);

    foreach (UITouch touch in touches.Cast<UITouch>())
    {
        inProgressPolylines.Remove(touch.Handle);
    }
    SetNeedsDisplay();
}
```

同时，此处理允许[FingerPaint](https://docs.microsoft.com/samples/xamarin/ios-samples/applicationfundamentals-fingerpaint)程序跟踪单个手指并在屏幕上绘制结果：

[![](touch-tracking-images/image01.png "Tracking individual fingers and drawing the results on the screen")](touch-tracking-images/image01.png#lightbox)

现在，你已了解如何在屏幕上跟踪各个手指并区分它们。

## <a name="related-links"></a>相关链接

- [等效的 Xamarin Android 指南](~/android/app-fundamentals/touch/touch-tracking.md)
- [FingerPaint （示例）](https://docs.microsoft.com/samples/xamarin/ios-samples/applicationfundamentals-fingerpaint)
