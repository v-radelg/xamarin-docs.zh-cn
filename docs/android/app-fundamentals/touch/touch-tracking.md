---
title: Xamarin 中的多点触控 Finger 跟踪
description: 本主题演示如何从多个手指跟踪触摸事件
ms.prod: xamarin
ms.assetid: 048D51F9-BD6C-4B44-8C53-CCEF276FC5CC
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/25/2018
ms.openlocfilehash: 26dfc4f9327f12d6854d72349dc46e0b4427fa72
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2019
ms.locfileid: "68643932"
---
# <a name="multi-touch-finger-tracking"></a>多点触控 Finger 跟踪

_本主题演示如何从多个手指跟踪触摸事件_

有时, 多点触控应用程序需要在屏幕上同时移动时跟踪各个手指。 一个典型的应用程序就是一个手指画图程序。 您希望用户能够用一个手指绘图, 还可以一次使用多个手指进行绘制。 当程序处理多个触控事件时, 它需要区分每个手指对应的事件。 Android 为此目的提供 ID 代码, 但获取和处理这些代码可能有点棘手。

对于与特定手指关联的所有事件, ID 代码保持不变。 当手指首次触摸屏幕时, 将分配 ID 代码, 并在手指从屏幕上提起后变为无效。
这些 ID 代码通常是非常小的整数, Android 重用它们以用于以后的触控事件。

几乎始终会有一次跟踪单个手指的程序维护用于触摸跟踪的字典。 字典键是标识特定手指的 ID 代码。 字典值依赖于应用程序。 在[FingerPaint](https://docs.microsoft.com/samples/xamarin/monodroid-samples/applicationfundamentals-fingerpaint)程序中, 每个 finger 笔划 (从触控到 release) 都与一个对象相关联, 该对象包含用该手指绘制的线条所需的所有信息。 程序出于此目的定义`FingerPaintPolyline`了一个小型类:

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

每个折线都有一种颜色、描边宽度和一个[`Path`](xref:Android.Graphics.Path) Android 图形对象, 可在绘制时聚合并渲染行的多个点。

下面显示的代码的其余部分包含在名为`View` `FingerPaintCanvasView`的导数中。 在当前由一个或多个手指绘制`FingerPaintPolyline`时, 该类将维护类型为的对象的字典:

```csharp
Dictionary<int, FingerPaintPolyline> inProgressPolylines = new Dictionary<int, FingerPaintPolyline>();
```

此字典允许视图快速获取与特定手指`FingerPaintPolyline`关联的信息。

类还会为已`List`完成的折线维护一个对象: `FingerPaintCanvasView`

```csharp
List<FingerPaintPolyline> completedPolylines = new List<FingerPaintPolyline>();
```

此`List`中的对象的顺序与它们的绘制顺序相同。

`FingerPaintCanvasView`重写由定义的`View`两种方法:[`OnDraw`](xref:Android.Views.View.OnDraw*)
和[`OnTouchEvent`](xref:Android.Views.View.OnTouchEvent*)。
在其`OnDraw`重写中, 视图绘制已完成的折线, 然后绘制正在进行的折线。

`OnTouchEvent`方法的重写通过从`ActionIndex`属性`pointerIndex`获取值开始。 此`ActionIndex`值在多个手指之间区分, 但在多个事件之间并不一致。 出于此原因, 请使用`pointerIndex`从`GetPointerId`方法获取指针`id`值。 此 ID*在*多个事件之间保持一致:

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

请注意, 重写使用`ActionMasked` `switch`语句中的属性而不是`Action`属性。 原因是：

当处理多点`Action`触控时, 属性的`MotionEventsAction.Down`值为, 第一个手指触摸屏幕`Pointer2Down` , 然后将和`Pointer3Down`的值作为第二个和第三个手指同时触摸屏幕。 由于第四个和第五个手指联系`Action` , 因此属性具有数值, 甚至不与`MotionEventsAction`枚举的成员相对应! 需要检查值中的位标志的值, 以解释它们的含义。

同样, 由于手指与屏幕保持联系`Action` , 因此该属性的`Pointer2Up`值`Pointer3Up` `Up`为, 第二个和第三个手指为第一个手指。

属性所采用的值数量更少, 因为它应`ActionIndex`与属性结合使用以区分多个手指。 `ActionMasked` 手指触摸屏幕时, 属性只能与第一个手指`MotionEventActions.Down`和`PointerDown`下一根手指相等。 在手指离开屏幕时, `ActionMasked`将的`Pointer1Up`值显示为后面的手指和`Up`第一个手指的值。

使用时`ActionMasked` ,可`MotionEvent`通过下一根手指来区分屏幕,但通常不需要使用该值,除非作为对象中其他方法的参数。`ActionIndex` 对于多点触控, 上面的代码中`GetPointerId`调用了这些方法中的最重要的一种方法。 该方法返回一个值, 该值可用于字典键, 以将特定事件与手指相关联。

`MotionEventActions.Down`[FingerPaint](https://docs.microsoft.com/samples/xamarin/monodroid-samples/applicationfundamentals-fingerpaint)`PointerDown` `OnTouchEvent`程序中的重写通过创建新 `FingerPaintPolyline`的对象并将其添加到字典中来处理和事件:

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

`pointerIndex`请注意, 还用于获取该指针在视图中的位置。 所有触摸信息都与`pointerIndex`值相关联。 `id`唯一标识跨多个消息的手指, 以便用于创建字典条目。

同样`OnTouchEvent` , 重写还会通过`MotionEventActions.Up`将已完成的折线传输到`completedPolylines`集合来处理和`Pointer1Up`相同, 以便在`OnDraw`重写过程中绘制它们。 此代码还会从`id`字典中删除该项:

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

在停机和向上事件之间, 通常有很多`MotionEventActions.Move`事件。 这些将在对`OnTouchEvent`的单个调用中捆绑在一起, 它们的处理方式必须与`Up` `Down`和事件的处理方式不同。 `pointerIndex` 之前`ActionIndex`从属性中获取的值必须被忽略。 相反, 该方法必须通过循环`pointerIndex` 0 `PointerCount`与属性来获取多个值`id` , 然后获取其中每个`pointerIndex`值的:

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

这种类型的处理允许[FingerPaint](https://docs.microsoft.com/samples/xamarin/monodroid-samples/applicationfundamentals-fingerpaint)程序跟踪单个手指并在屏幕上绘制结果:

[![FingerPaint 示例中的示例屏幕截图](touch-tracking-images/image01.png)](touch-tracking-images/image01.png#lightbox)

现在, 你已了解如何在屏幕上跟踪各个手指并区分它们。


## <a name="related-links"></a>相关链接

- [等效的 Xamarin iOS 指南](~/ios/app-fundamentals/touch/touch-tracking.md)
- [FingerPaint (示例)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/applicationfundamentals-fingerpaint)
