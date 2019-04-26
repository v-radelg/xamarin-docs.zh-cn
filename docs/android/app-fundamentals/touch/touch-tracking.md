---
title: 多点触控手指在 Xamarin.Android 中跟踪
description: 本主题演示如何跟踪从多个手指触摸事件
ms.prod: xamarin
ms.assetid: 048D51F9-BD6C-4B44-8C53-CCEF276FC5CC
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/25/2018
ms.openlocfilehash: 34a9d2d9b8acb05a1b978a70e85038507032faaa
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61011778"
---
# <a name="multi-touch-finger-tracking"></a>多点触控手指跟踪

_本主题演示如何跟踪从多个手指触摸事件_

有些的时候多点触控应用程序需要跟踪各手指，因为它们在屏幕上同时移动。 一个典型的应用程序是一个 finger-paint 程序。 你希望用户能够绘制的一根手指，而且还可以同时使用多个手指绘制。 在您的程序处理多个触控事件，它需要区分哪些事件对应于每个手指。 Android 提供 ID 代码实现此目的，但获取和处理该代码可能有点棘手。

对于特定手指与关联的所有事件，ID 代码保持不变。 当手指首先触摸屏幕，并从屏幕上抬起手指后变为无效时分配的 ID 代码。
这些 ID 代码是通常非常小的整数，并且 Android 将它们重新用于更高版本的触控事件。

几乎总是跟踪各手指的程序维护触控体验跟踪的字典。 字典的键是标识特定手指的 ID 代码。 字典的值取决于应用程序。 在中[; FingerPaint](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/FingerPaint)程序中，每个手指笔划 （从触摸以释放） 是与包含呈现与该手指绘制的线条所需的所有信息的对象相关联。 程序定义了一个较小`FingerPaintPolyline`类实现此目的：

```csharp
class FingerPaintPolyline
{
    public FingerPaintPolyline()
    {
        Path = new Path();
    }

    public Color Color { set; get; }

    public float StrokeWidth { set; get; }

    public Path Path { private set; get; }
}
```

每个 polyline 具有一种颜色，笔划宽度和 Android 图形[ `Path` ](https://developer.xamarin.com/api/type/Android.Graphics.Path/)地累积并呈现在行的多个点，因为所绘制的对象。

如下所示的代码的其余部分包含在`View`派生类名为`FingerPaintCanvasView`。 类维护类型的对象的字典`FingerPaintPolyline`他们主动正在由一个或多个手指绘制期间：

```csharp
Dictionary<int, FingerPaintPolyline> inProgressPolylines = new Dictionary<int, FingerPaintPolyline>();
```

此字典可让视图快速获取`FingerPaintPolyline`特定手指与关联的信息。

`FingerPaintCanvasView`类还维护`List`折线已完成的对象：

```csharp
List<FingerPaintPolyline> completedPolylines = new List<FingerPaintPolyline>();
```

在此对象`List`中已绘制它们的顺序相同。

`FingerPaintCanvasView` 重写两个方法定义的`View`: [`OnDraw`](https://developer.xamarin.com/api/member/Android.Views.View.OnDraw/p/Android.Graphics.Canvas/)
并[ `OnTouchEvent` ](https://developer.xamarin.com/api/member/Android.Views.View.OnTouchEvent/p/Android.Views.MotionEvent/)。
在其`OnDraw`重写时，该视图绘制已完成的折线，然后绘制正在折线。

重写`OnTouchEvent`方法首先获取`pointerIndex`值从`ActionIndex`属性。 这`ActionIndex`值区分多个手指，但它不一致跨多个事件。 为此，您使用`pointerIndex`若要获取指针`id`值从`GetPointerId`方法。 此 ID*是*一致跨多个事件：

```csharp
public override bool OnTouchEvent(MotionEvent args)
{
    // Get the pointer index
    int pointerIndex = args.ActionIndex;

    // Get the id to identify a finger over the course of its progress
    int id = args.GetPointerId(pointerIndex);

    // Use ActionMasked here rather than Action to reduce the number of possibilities
    switch (args.ActionMasked)
    {
        // ...
    }

    // Invalidate to update the view
    Invalidate();

    // Request continued touch input
    return true;
}
```

请注意，使用重写`ActionMasked`中的属性`switch`语句而不是`Action`属性。 原因是：

时就需要使用多点触控`Action`属性的值为`MotionEventsAction.Down`的第一个手指触摸屏幕，和的值`Pointer2Down`和`Pointer3Down`因为第二个和第三个手指同时触摸屏幕。 第四个和第五个手指进行联系人`Action`属性具有甚至不对应于的成员的数值`MotionEventsAction`枚举 ！ 您需要检查中的值来解释它们的含义的位标志的值。

同样，如在手指离开屏幕，请联系`Action`属性具有值`Pointer2Up`并`Pointer3Up`第二个和第三个手指，和`Up`的第一根手指。

`ActionMasked`属性采用上较少因为它旨在与结合使用的值数目`ActionIndex`属性来区分多个手指。 当手指触摸屏幕时，可以仅为属性`MotionEventActions.Down`的第一根手指和`PointerDown`的后续手指。 在手指离开屏幕，如`ActionMasked`具有值`Pointer1Up`后续手指和`Up`的第一根手指。

使用时`ActionMasked`，则`ActionIndex`后续手指触摸和保留的屏幕，但您通常不需要使用该以外的值作为中的其他方法的参数用于区分`MotionEvent`对象。 多点触控，最重要的一种方法是`GetPointerId`在上面的代码中调用。 方法返回一个值可用于字典键可以将手指的特定事件关联。

`OnTouchEvent`中重写[; FingerPaint](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/FingerPaint)程序进程`MotionEventActions.Down`并`PointerDown`相同通过创建一个新的事件`FingerPaintPolyline`对象并将其添加到字典：

```csharp
public override bool OnTouchEvent(MotionEvent args)
{
    // Get the pointer index
    int pointerIndex = args.ActionIndex;

    // Get the id to identify a finger over the course of its progress
    int id = args.GetPointerId(pointerIndex);

    // Use ActionMasked here rather than Action to reduce the number of possibilities
    switch (args.ActionMasked)
    {
        case MotionEventActions.Down:
        case MotionEventActions.PointerDown:

            // Create a Polyline, set the initial point, and store it
            FingerPaintPolyline polyline = new FingerPaintPolyline
            {
                Color = StrokeColor,
                StrokeWidth = StrokeWidth
            };

            polyline.Path.MoveTo(args.GetX(pointerIndex),
                                 args.GetY(pointerIndex));

            inProgressPolylines.Add(id, polyline);
            break;
        // ...
    }
    // ...        
}
```

请注意，`pointerIndex`还用于获取视图中的手指的位置。 与之关联的所有触摸信息`pointerIndex`值。 `id`唯一标识多条消息，手指，以便创建字典项的使用。

同样，`OnTouchEvent`还重写句柄`MotionEventActions.Up`并`Pointer1Up`相同通过转移到已完成的折线`completedPolylines`集合，因此它们可以在绘制`OnDraw`重写。 该代码还会删除`id`字典中的条目：

```csharp
public override bool OnTouchEvent(MotionEvent args)
{
    // ...
    switch (args.ActionMasked)
    {
        // ...
        case MotionEventActions.Up:
        case MotionEventActions.Pointer1Up:

            inProgressPolylines[id].Path.LineTo(args.GetX(pointerIndex),
                                                args.GetY(pointerIndex));

            // Transfer the in-progress polyline to a completed polyline
            completedPolylines.Add(inProgressPolylines[id]);
            inProgressPolylines.Remove(id);
            break;

        case MotionEventActions.Cancel:
            inProgressPolylines.Remove(id);
            break;
    }
    // ...        
}
```

现在的最为棘手的部分。

之间的向下和向上事件，通常有很多`MotionEventActions.Move`事件。 对单个调用中这些捆绑在一起`OnTouchEvent`，并且必须从以不同方式处理它们`Down`和`Up`事件。 `pointerIndex`从前面获取的值`ActionIndex`必须忽略属性。 相反，该方法必须获得多个`pointerIndex`循环之间 0 的值和`PointerCount`属性，然后获取`id`其中每个`pointerIndex`值：

```csharp
public override bool OnTouchEvent(MotionEvent args)
{
    // ...
    switch (args.ActionMasked)
    {
        // ...
        case MotionEventActions.Move:

            // Multiple Move events are bundled, so handle them differently
            for (pointerIndex = 0; pointerIndex < args.PointerCount; pointerIndex++)
            {
                id = args.GetPointerId(pointerIndex);

                inProgressPolylines[id].Path.LineTo(args.GetX(pointerIndex),
                                                    args.GetY(pointerIndex));
            }
            break;
        // ...
    }
    // ...        
}
```

处理此类允许[; FingerPaint](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/FingerPaint)程序来跟踪各手指，并在屏幕上绘制结果：

[![FingerPaint 示例中的示例屏幕截图](touch-tracking-images/image01.png)](touch-tracking-images/image01.png#lightbox)

现在已了解如何跟踪各手指在屏幕上并区分它们。


## <a name="related-links"></a>相关链接

- [等效的 Xamarin iOS 》 指南](~/ios/app-fundamentals/touch/touch-tracking.md)
- [FingerPaint （示例）](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/FingerPaint)
