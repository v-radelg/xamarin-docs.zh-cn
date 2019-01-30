---
title: 多点触控手指在 Xamarin.iOS 中跟踪
description: 本文档介绍如何跟踪各手指在 Xamarin.iOS 应用程序中的多点触控笔势。 它是围绕一个手指绘制应用程序示例。
ms.prod: xamarin
ms.assetid: 48E8B20D-0833-43D2-976A-0605DDB386E3
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: 09e895714cb4bbe241e4e14facaaee52079d55d9
ms.sourcegitcommit: a1a58afea68912c79d16a3f64de9a0c1feb2aeb4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/30/2019
ms.locfileid: "55233180"
---
# <a name="multi-touch-finger-tracking-in-xamarinios"></a>多点触控手指在 Xamarin.iOS 中跟踪

_本文档演示如何跟踪从多个手指触摸事件_

有些的时候多点触控应用程序需要跟踪各手指，因为它们在屏幕上同时移动。 一个典型的应用程序是一个 finger-paint 程序。 你希望用户能够绘制的一根手指，而且还可以同时使用多个手指绘制。 在您的程序处理多个触控事件，它需要区分这些根手指。

当手指首先触摸屏幕时，创建 iOS [ `UITouch` ](xref:UIKit.UITouch)这根手指的对象。 手指在屏幕上移动，并从屏幕上，此时释放该对象然后将此对象保持不变。 若要跟踪的手指，程序应避免将存储此`UITouch`直接对象。 相反，它可以使用[ `Handle` ](xref:Foundation.NSObject.Handle)类型的属性`IntPtr`来唯一地标识这些`UITouch`对象。

几乎总是跟踪各手指的程序维护触控体验跟踪的字典。 对于 iOS 程序，字典的键是`Handle`值，该值标识特定的手指。 字典的值取决于应用程序。 在中[; FingerPaint](https://developer.xamarin.com/samples/monotouch/ApplicationFundamentals/FingerPaint)程序中，每个手指笔划 （从触摸以释放） 是与包含呈现与该手指绘制的线条所需的所有信息的对象相关联。 程序定义了一个较小`FingerPaintPolyline`类实现此目的：

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

每个 polyline 具有一种颜色，笔划宽度和 iOS 图形[ `CGPath` ](xref:CoreGraphics.CGPath)地累积并呈现在行的多个点，因为所绘制的对象。


中包含的代码如下所示的所有其余`UIView`派生类名为`FingerPaintCanvasView`。 类维护类型的对象的字典`FingerPaintPolyline`他们主动正在由一个或多个手指绘制期间：

```csharp
Dictionary<IntPtr, FingerPaintPolyline> inProgressPolylines = new Dictionary<IntPtr, FingerPaintPolyline>();
```

此字典可让视图快速获取`FingerPaintPolyline`基于信息与每个手指`Handle`属性的`UITouch`对象。

`FingerPaintCanvasView`类还维护`List`折线已完成的对象：

```csharp
List<FingerPaintPolyline> completedPolylines = new List<FingerPaintPolyline>();
```

在此对象`List`中已绘制它们的顺序相同。

`FingerPaintCanvasView` 重写由定义的五种方法`View`:

- [`TouchesBegan`](xref:UIKit.UIResponder.TouchesBegan(Foundation.NSSet,UIKit.UIEvent))
- [`TouchesMoved`](xref:UIKit.UIResponder.TouchesMoved(Foundation.NSSet,UIKit.UIEvent))
- [`TouchesEnded`](xref:UIKit.UIResponder.TouchesEnded(Foundation.NSSet,UIKit.UIEvent))
- [`TouchesCancelled`](xref:UIKit.UIResponder.TouchesCancelled(Foundation.NSSet,UIKit.UIEvent))
- [`Draw`](xref:UIKit.UIView.Draw(CoreGraphics.CGRect))

各种`Touches`替代累积折线所组成的点。

[`Draw`] 替代绘制已完成的折线，然后进行中的折线：

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

每个`Touches`重写可能报告多个手指，由一个或多个操作`UITouch`中存储的对象`touches`方法自变量。 `TouchesBegan`替代循环访问这些对象。 每个`UITouch`对象，此方法创建并初始化一个新`FingerPaintPolyline`对象，包括存储的手指从获取初始位置`LocationInView`方法。 这`FingerPaintPolyline`对象添加到`InProgressPolylines`字典使用`Handle`属性的`UITouch`作为字典键的对象：

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

该方法最后将通过调用`SetNeedsDisplay`生成调用`Draw`重写和更新屏幕。

在屏幕上移动手指`View`获取对多个调用其`TouchesMoved`重写。 此替代同样遍历`UITouch`中存储的对象`touches`参数并将当前手指的位置添加到图形路径：

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

`touches`集合中包含只`UITouch`对象自上次调用以来已移动手指`TouchesBegan`或`TouchesMoved`。 如果您曾需要`UITouch`对象对应于*所有*当前接触屏幕的手指，该信息是通过提供`AllTouches`属性`UIEvent`方法自变量。

`TouchesEnded`替代具有两个作业。 它必须将最后一个点添加到图形路径和传输`FingerPaintPolyline`对象从`inProgressPolylines`字典`completedPolylines`列表：

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

`TouchesCancelled`重写由只放弃`FingerPaintPolyline`字典中的对象：

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

此处理，可完全[; FingerPaint](https://developer.xamarin.com/samples/monotouch/ApplicationFundamentals/FingerPaint)程序来跟踪各手指，并在屏幕上绘制结果：

[![](touch-tracking-images/image01.png "跟踪各手指并在屏幕上绘制结果")](touch-tracking-images/image01.png#lightbox)

现在已了解如何跟踪各手指在屏幕上并区分它们。



## <a name="related-links"></a>相关链接

- [等效的 Xamarin Android 指南](~/android/app-fundamentals/touch/touch-tracking.md)
- [FingerPaint （示例）](https://developer.xamarin.com/samples/monotouch/ApplicationFundamentals/FingerPaint)
