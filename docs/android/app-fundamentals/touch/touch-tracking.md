---
title: Xamarin 中的多点触控 Finger 跟踪
description: 本主题演示如何从多个手指跟踪触摸事件
ms.prod: xamarin
ms.assetid: 048D51F9-BD6C-4B44-8C53-CCEF276FC5CC
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 04/25/2018
ms.openlocfilehash: f960c3cec90bd331f5a1433a869c7720b40c9680
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73024269"
---
# <a name="multi-touch-finger-tracking"></a>多点触控 Finger 跟踪

_本主题演示如何从多个手指跟踪触摸事件_

有时，多点触控应用程序需要在屏幕上同时移动时跟踪各个手指。 一个典型的应用程序就是一个手指画图程序。 您希望用户能够用一个手指绘图，还可以一次使用多个手指进行绘制。 当程序处理多个触控事件时，它需要区分每个手指对应的事件。 Android 为此目的提供 ID 代码，但获取和处理这些代码可能有点棘手。

对于与特定手指关联的所有事件，ID 代码保持不变。 当手指首次触摸屏幕时，将分配 ID 代码，并在手指从屏幕上提起后变为无效。
这些 ID 代码通常是非常小的整数，Android 重用它们以用于以后的触控事件。

几乎始终会有一次跟踪单个手指的程序维护用于触摸跟踪的字典。 字典键是标识特定手指的 ID 代码。 字典值依赖于应用程序。 在[FingerPaint](https://docs.microsoft.com/samples/xamarin/monodroid-samples/applicationfundamentals-fingerpaint)程序中，每个 finger 笔划（从触控到 release）都与一个对象相关联，该对象包含用该手指绘制的线条所需的所有信息。 程序出于此目的定义了一个小型 `FingerPaintPolyline` 类：

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

每个折线都有一种颜色、描边宽度和 Android 图形[`Path`](xref:Android.Graphics.Path)对象，可在绘制直线的同时，对其进行多个点的绘制和呈现。

下面显示的代码的其余部分包含在名为 `FingerPaintCanvasView``View` 导数中。 此类在当前由一个或多个手指绘制的情况中维护类型为 `FingerPaintPolyline` 对象的字典：

```csharp
Dictionary<int, FingerPaintPolyline> inProgressPolylines = new Dictionary<int, FingerPaintPolyline>();
```

此字典允许视图快速获取与特定手指关联的 `FingerPaintPolyline` 信息。

`FingerPaintCanvasView` 类还会为已完成的折线维护一个 `List` 对象：

```csharp
List<FingerPaintPolyline> completedPolylines = new List<FingerPaintPolyline>();
```

此 `List` 中的对象的顺序与它们的绘制顺序相同。

`FingerPaintCanvasView` 重写 `View`定义的两种方法： [`OnDraw`](xref:Android.Views.View.OnDraw*)
和[`OnTouchEvent`](xref:Android.Views.View.OnTouchEvent*)。
在其 `OnDraw` 重写中，视图绘制已完成的折线，然后绘制正在进行的折线。

`OnTouchEvent` 方法的重写从 `ActionIndex` 属性获取 `pointerIndex` 值开始。 这 `ActionIndex` 值在多个手指之间区分，但它在多个事件之间并不一致。 出于此原因，请使用 `pointerIndex` 获取 `GetPointerId` 方法中 `id` 值的指针。 此 ID*在*多个事件之间保持一致：

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

请注意，重写使用 `switch` 语句中的 `ActionMasked` 属性，而不是 `Action` 属性。 原因是：

当你处理多点触控时，`Action` 属性的值为 `MotionEventsAction.Down`，第一个手指触摸屏幕，然后 `Pointer2Down` 和 `Pointer3Down` 的值，因为第二个和第三个手指还会触摸屏幕。 由于第四个和第五个手指联系，因此 `Action` 属性具有数字值，甚至与 `MotionEventsAction` 枚举的成员相对应！ 需要检查值中的位标志的值，以解释它们的含义。

同样，当手指与屏幕保持联系时，`Action` 属性的值为第二个和第三个手指的 `Pointer2Up` 和 `Pointer3Up`，并 `Up` 第一个手指。

`ActionMasked` 属性所采用的值的数量较少，因为它应与 `ActionIndex` 属性结合使用以区分多个手指。 手指触摸屏幕时，对于第一个手指，属性只能等于 `MotionEventActions.Down`，并 `PointerDown` 后接手指。 当手指离开屏幕时，`ActionMasked` 的值为 `Pointer1Up`，并为第一个手指提供 `Up`。

使用 `ActionMasked`时，`ActionIndex` 将在下一根手指之间进行区分并离开屏幕，但通常不需要使用该值，除非作为 `MotionEvent` 对象中其他方法的参数。 对于多点触控，其中最重要的一种方法是在上面的代码中 `GetPointerId` 调用。 该方法返回一个值，该值可用于字典键，以将特定事件与手指相关联。

[FingerPaint](https://docs.microsoft.com/samples/xamarin/monodroid-samples/applicationfundamentals-fingerpaint)程序中的 `OnTouchEvent` 重写通过创建新的 `FingerPaintPolyline` 对象并将其添加到字典中，处理 `MotionEventActions.Down` 并 `PointerDown` 事件相同：

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

请注意，`pointerIndex` 还用于获取手指在视图中的位置。 所有触摸信息都与 `pointerIndex` 值相关联。 `id` 唯一标识跨多个消息的手指，因此用于创建字典条目。

同样，`OnTouchEvent` 重写还会通过将已完成的折线传输到 `completedPolylines` 集合来处理 `MotionEventActions.Up` 和 `Pointer1Up`，以便在 `OnDraw` 重写期间绘制它们。 此代码还会从字典中删除 `id` 条目：

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

现在为棘手部分。

在停机和向上事件之间，通常有许多 `MotionEventActions.Move` 事件。 这些绑定在对 `OnTouchEvent`的单个调用中捆绑在一起，它们的处理方式与 `Down` 和 `Up` 事件的处理方式不同。 之前从 `ActionIndex` 属性中获取 `pointerIndex` 值必须被忽略。 相反，该方法必须通过循环0到 `PointerCount` 属性来获取多个 `pointerIndex` 值，然后获取其中每个 `pointerIndex` 值的 `id`：

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

这种类型的处理允许[FingerPaint](https://docs.microsoft.com/samples/xamarin/monodroid-samples/applicationfundamentals-fingerpaint)程序跟踪单个手指并在屏幕上绘制结果：

[FingerPaint 示例中的![示例屏幕截图](touch-tracking-images/image01.png)](touch-tracking-images/image01.png#lightbox)

现在，你已了解如何在屏幕上跟踪各个手指并区分它们。

## <a name="related-links"></a>相关链接

- [等效的 Xamarin iOS 指南](~/ios/app-fundamentals/touch/touch-tracking.md)
- [FingerPaint （示例）](https://docs.microsoft.com/samples/xamarin/monodroid-samples/applicationfundamentals-fingerpaint)
