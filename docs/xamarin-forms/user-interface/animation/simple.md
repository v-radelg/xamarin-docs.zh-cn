---
title: Xamarin 中的简单动画
description: ViewExtensions 类提供了可用于构建简单动画的扩展方法。 本文演示如何使用 ViewExtensions 类创建和取消动画。
ms.prod: xamarin
ms.assetid: 4A6FAE5A-848F-4CE0-BFA1-22A6309B5225
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2019
ms.openlocfilehash: 116911787db128b103fb555554076704a0549db5
ms.sourcegitcommit: f8583585c501607fdfa061b95e9a9f385ed1d591
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/26/2019
ms.locfileid: "72959164"
---
# <a name="simple-animations-in-xamarinforms"></a>Xamarin 中的简单动画

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-animation-basic)

_ViewExtensions 类提供了可用于构建简单动画的扩展方法。本文演示如何使用 ViewExtensions 类创建和取消动画。_

[`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions)类提供以下扩展方法，这些方法可用于创建简单动画：

- [`TranslateTo`](xref:Xamarin.Forms.ViewExtensions.TranslateTo(Xamarin.Forms.VisualElement,System.Double,System.Double,System.UInt32,Xamarin.Forms.Easing))对[`VisualElement`](xref:Xamarin.Forms.VisualElement)的[`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX)和[`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY)属性进行动画处理。
- [`ScaleTo`](xref:Xamarin.Forms.ViewExtensions.ScaleTo*)对[`VisualElement`](xref:Xamarin.Forms.VisualElement)的[`Scale`](xref:Xamarin.Forms.VisualElement.Scale)属性进行动画处理。
- [`RelScaleTo`](xref:Xamarin.Forms.ViewExtensions.RelScaleTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))将动画增量增加或减小到[`VisualElement`](xref:Xamarin.Forms.VisualElement)的[`Scale`](xref:Xamarin.Forms.VisualElement.Scale)属性。
- [`RotateTo`](xref:Xamarin.Forms.ViewExtensions.RotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))对[`VisualElement`](xref:Xamarin.Forms.VisualElement)的[`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation)属性进行动画处理。
- [`RelRotateTo`](xref:Xamarin.Forms.ViewExtensions.RelRotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))将动画增量增加或减小到[`VisualElement`](xref:Xamarin.Forms.VisualElement)的[`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation)属性。
- [`RotateXTo`](xref:Xamarin.Forms.ViewExtensions.RotateXTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))对[`VisualElement`](xref:Xamarin.Forms.VisualElement)的[`RotationX`](xref:Xamarin.Forms.VisualElement.RotationX)属性进行动画处理。
- [`RotateYTo`](xref:Xamarin.Forms.ViewExtensions.RotateYTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))对[`VisualElement`](xref:Xamarin.Forms.VisualElement)的[`RotationY`](xref:Xamarin.Forms.VisualElement.RotationY)属性进行动画处理。
- [`FadeTo`](xref:Xamarin.Forms.ViewExtensions.FadeTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))对[`VisualElement`](xref:Xamarin.Forms.VisualElement)的[`Opacity`](xref:Xamarin.Forms.VisualElement.Opacity)属性进行动画处理。

默认情况下，每个动画将需要250毫秒。 但是，可以在创建动画时指定每个动画的持续时间。

[`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions)类还包括可用于取消任何动画的[`CancelAnimations`](xref:Xamarin.Forms.ViewExtensions.CancelAnimations(Xamarin.Forms.VisualElement))方法。

> [!NOTE]
> [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions)类提供[`LayoutTo`](xref:Xamarin.Forms.ViewExtensions.LayoutTo(Xamarin.Forms.VisualElement,Xamarin.Forms.Rectangle,System.UInt32,Xamarin.Forms.Easing))扩展方法。 但是，此方法旨在供布局用来对包含大小和位置更改的布局状态之间的转换进行动画处理。 因此，它只能由[`Layout`](xref:Xamarin.Forms.Layout)子类使用。

[`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions)类中的动画扩展方法都是异步的，并返回一个 `Task<bool>` 对象。 如果动画完成，则返回值为 `false`，如果取消动画，则 `true`。 因此，动画方法通常应与 `await` 运算符一起使用，这使得可以轻松确定动画的完成时间。 此外，它还可以创建具有在上一方法完成后执行的后续动画方法的连续动画。 有关详细信息，请参阅[复合动画](#compound)。

如果需要让动画在后台完成，则可以省略 `await` 运算符。 在这种情况下，动画扩展方法将在启动动画后快速返回，动画将在后台发生。 创建复合动画时，可以利用此操作。 有关详细信息，请参阅[复合动画](#composite)。

有关 `await` 运算符的详细信息，请参阅[异步支持概述](~/cross-platform/platform/async.md)。

## <a name="single-animations"></a>单个动画

[`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions)中的每个扩展方法实现一个动画操作，该操作在一段时间内从一个值逐渐更改一个值到另一个值。 本部分将探讨每个动画操作。

### <a name="rotation"></a>旋转

下面的代码示例演示如何使用[`RotateTo`](xref:Xamarin.Forms.ViewExtensions.RotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))方法对[`Image`](xref:Xamarin.Forms.Image)的[`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation)属性进行动画处理：

```csharp
await image.RotateTo (360, 2000);
image.Rotation = 0;
```

此代码通过在2秒（2000毫秒）内向上旋转360度来对[`Image`](xref:Xamarin.Forms.Image)实例进行动画处理。 [`RotateTo`](xref:Xamarin.Forms.ViewExtensions.RotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))方法获取动画开始的当前[`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation)属性值，然后将该值从该值旋转到其第一个参数（360）。 动画完成后，图像的[`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation)属性重置为0。 这可确保在动画结束后 `Rotation` 属性不会保持为360，这会阻止增加旋转。

以下屏幕截图显示每个平台上正在进行的旋转：

![](simple-images/rotateto.png "Rotation Animation")

### <a name="relative-rotation"></a>相对旋转

下面的代码示例演示如何使用[`RelRotateTo`](xref:Xamarin.Forms.ViewExtensions.RelRotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))方法以增量方式增加或减少[`Image`](xref:Xamarin.Forms.Image)的[`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation)属性：

```csharp
await image.RelRotateTo (360, 2000);
```

此代码通过在2秒（2000毫秒）的起始位置旋转360度来对[`Image`](xref:Xamarin.Forms.Image)实例进行动画处理。 [`RelRotateTo`](xref:Xamarin.Forms.ViewExtensions.RelRotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))方法获取动画起始处的当前[`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation)属性值，然后将该值从该值加上其第一个参数（360）。 这可确保每个动画都将始终从起始位置旋转360度。 因此，如果在动画已正在进行时调用了新动画，则会从当前位置开始，并可能以不是360度增量的位置结束。

以下屏幕截图显示每个平台上正在进行的相对旋转：

![](simple-images/relrotateto.png "Relative Rotation Animation")

### <a name="scaling"></a>缩放

下面的代码示例演示如何使用[`ScaleTo`](xref:Xamarin.Forms.ViewExtensions.ScaleTo*)方法对[`Image`](xref:Xamarin.Forms.Image)的[`Scale`](xref:Xamarin.Forms.VisualElement.Scale)属性进行动画处理：

```csharp
await image.ScaleTo (2, 2000);
```

此代码通过在2秒（2000毫秒）内扩展到其大小的两倍来对[`Image`](xref:Xamarin.Forms.Image)实例进行动画处理。 [`ScaleTo`](xref:Xamarin.Forms.ViewExtensions.ScaleTo*)方法获取动画开始的当前[`Scale`](xref:Xamarin.Forms.VisualElement.Scale)属性值（默认值为1），然后从该值缩放到其第一个参数（2）。 这可以将图像的大小扩展到大小的两倍。

下面的屏幕截图显示每个平台上正在进行的缩放：

![](simple-images/scaleto.png "Scaling Animation")

> [!NOTE]
> [`VisualElement`](xref:Xamarin.Forms.VisualElement) 类还定义了 [`ScaleX`](xref:Xamarin.Forms.VisualElement.ScaleX) 和 [`ScaleY`](xref:Xamarin.Forms.VisualElement.ScaleY) 属性，这些属性可以在水平和垂直方向上以不同方式缩放 `VisualElement`。 可以通过[`Animation`](xref:Xamarin.Forms.Animation)类对这些属性进行动画处理。 有关详细信息，请参阅[Xamarin 中的自定义动画](custom.md)。

### <a name="relative-scaling"></a>相对缩放

下面的代码示例演示如何使用[`RelScaleTo`](xref:Xamarin.Forms.ViewExtensions.RelScaleTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))方法对[`Image`](xref:Xamarin.Forms.Image)的[`Scale`](xref:Xamarin.Forms.VisualElement.Scale)属性进行动画处理：

```csharp
await image.RelScaleTo (2, 2000);
```

此代码通过在2秒（2000毫秒）内扩展到其大小的两倍来对[`Image`](xref:Xamarin.Forms.Image)实例进行动画处理。 [`RelScaleTo`](xref:Xamarin.Forms.ViewExtensions.RelScaleTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))方法获取动画开始的当前[`Scale`](xref:Xamarin.Forms.VisualElement.Scale)属性值，然后将该值从该值加上其第一个参数（2）。 这可确保每个动画将始终从起始位置缩放2。

### <a name="scaling-and-rotation-with-anchors"></a>通过定位点进行缩放和旋转

[`AnchorX`](xref:Xamarin.Forms.VisualElement.AnchorX)和[`AnchorY`](xref:Xamarin.Forms.VisualElement.AnchorY)属性设置[`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation)和[`Scale`](xref:Xamarin.Forms.VisualElement.Scale)属性的缩放或旋转中心。 因此，它们的值还会影响[`RotateTo`](xref:Xamarin.Forms.ViewExtensions.RotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))和[`ScaleTo`](xref:Xamarin.Forms.ViewExtensions.ScaleTo*)方法。

假设已将[`Image`](xref:Xamarin.Forms.Image)放置在布局的中心，下面的代码示例演示了如何通过设置其[`AnchorY`](xref:Xamarin.Forms.VisualElement.AnchorY)属性，在布局中心附近旋转图像：

```csharp
double radius = Math.Min(absoluteLayout.Width, absoluteLayout.Height) / 2;
image.AnchorY = radius / image.Height;
await image.RotateTo(360, 2000);
```

若要围绕布局中心旋转[`Image`](xref:Xamarin.Forms.Image)实例，则必须将[`AnchorX`](xref:Xamarin.Forms.VisualElement.AnchorX)和[`AnchorY`](xref:Xamarin.Forms.VisualElement.AnchorY)属性设置为相对于 `Image`的宽度和高度的值。 在此示例中，`Image` 的中心定义为位于布局的中心，因此0.5 的 `AnchorX` 默认值不需要更改。 但 `AnchorY` 属性被重新定义为从 `Image` 顶部到布局中心点的值。 这可确保 `Image` 在布局中心点围绕布局旋转360度，如以下屏幕截图所示：

![](simple-images/rotate-anchors.png "Rotation Animation with Anchors")

### <a name="translation"></a>转换

下面的代码示例演示如何使用[`TranslateTo`](xref:Xamarin.Forms.ViewExtensions.TranslateTo(Xamarin.Forms.VisualElement,System.Double,System.Double,System.UInt32,Xamarin.Forms.Easing))方法对[`Image`](xref:Xamarin.Forms.Image)的[`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX)和[`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY)属性进行动画处理：

```csharp
await image.TranslateTo (-100, -100, 1000);
```

此代码通过在1秒（1000毫秒）的水平和垂直方向变换来对[`Image`](xref:Xamarin.Forms.Image)实例进行动画处理。 [`TranslateTo`](xref:Xamarin.Forms.ViewExtensions.TranslateTo(Xamarin.Forms.VisualElement,System.Double,System.Double,System.UInt32,Xamarin.Forms.Easing))方法同时将100像素的图像向上转换为左、100像素。 这是因为第一个和第二个参数均为负数。 提供正数会将图像向右和向下转换。

以下屏幕截图显示每个平台上正在进行的转换：

![](simple-images/translateto.png "Translation Animation")

> [!NOTE]
> 如果某个元素最初在屏幕上进行布局，然后在屏幕上转换，则在转换后，该元素的输入布局将保留在屏幕之外，并且用户无法与之交互。 因此，建议视图应在其最终位置布局，然后执行所需的任何翻译。

### <a name="fading"></a>淡入淡出

下面的代码示例演示如何使用[`FadeTo`](xref:Xamarin.Forms.ViewExtensions.FadeTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))方法对[`Image`](xref:Xamarin.Forms.Image)的[`Opacity`](xref:Xamarin.Forms.VisualElement.Opacity)属性进行动画处理：

```csharp
image.Opacity = 0;
await image.FadeTo (1, 4000);
```

此代码通过在4秒（4000毫秒）内淡化[`Image`](xref:Xamarin.Forms.Image)实例，对其进行动画处理。 [`FadeTo`](xref:Xamarin.Forms.ViewExtensions.FadeTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))方法获取动画起始处的当前[`Opacity`](xref:Xamarin.Forms.VisualElement.Opacity)属性值，然后将该值从该值淡到其第一个参数（1）。

以下屏幕截图显示每个平台上的淡化：

![](simple-images/fadeto.png "Fading Animation")

<a name="compound" />

## <a name="compound-animations"></a>复合动画

复合动画是动画的顺序组合，可使用 `await` 运算符创建，如以下代码示例所示：

```csharp
await image.TranslateTo (-100, 0, 1000);    // Move image left
await image.TranslateTo (-100, -100, 1000); // Move image up
await image.TranslateTo (100, 100, 2000);   // Move image diagonally down and right
await image.TranslateTo (0, 100, 1000);     // Move image left
await image.TranslateTo (0, 0, 1000);       // Move image up
```

在此示例中，将在6秒（6000毫秒）内转换[`Image`](xref:Xamarin.Forms.Image) 。 `Image` 的翻译使用五个动画，`await` 运算符指示每个动画按顺序执行。 因此，在上一个方法完成后，后续的动画方法会执行。

<a name="composite" />

## <a name="composite-animations"></a>复合动画

复合动画是同时运行两个或多个动画的动画的组合。 可以通过混合等待和非等待的动画来创建复合动画，如以下代码示例所示：

```csharp
image.RotateTo (360, 4000);
await image.ScaleTo (2, 2000);
await image.ScaleTo (1, 2000);
```

在此示例中，将对[`Image`](xref:Xamarin.Forms.Image)进行缩放并同时旋转4秒（4000毫秒）。 `Image` 的缩放将使用两个连续的动画，这些动画在旋转时同时发生。 [`RotateTo`](xref:Xamarin.Forms.ViewExtensions.RotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))方法无需 `await` 运算符即可执行，并立即返回第一个[`ScaleTo`](xref:Xamarin.Forms.ViewExtensions.ScaleTo*)动画，然后开始。 第一次 `ScaleTo` 方法调用的 `await` 运算符将延迟第二个 `ScaleTo` 方法调用，直到第一个 `ScaleTo` 方法调用完成。 此时，`RotateTo` 动画完成了一半，并且 `Image` 将旋转180度。 在最后2秒（2000毫秒）期间，第二个 `ScaleTo` 动画和 `RotateTo` 动画都完成。

### <a name="running-multiple-asynchronous-methods-concurrently"></a>并发运行多个异步方法

`static` `Task.WhenAny` 和 `Task.WhenAll` 方法用于并发运行多个异步方法，因此可用于创建复合动画。 这两种方法都返回 `Task` 对象，并接受每个方法的集合，每个方法都返回一个 `Task` 对象。 当其集合中的任何方法完成执行时，`Task.WhenAny` 方法完成，如下面的代码示例所示：

```csharp
await Task.WhenAny<bool>
(
  image.RotateTo (360, 4000),
  image.ScaleTo (2, 2000)
);
await image.ScaleTo (1, 2000);
```

在此示例中，`Task.WhenAny` 方法调用包含两个任务。 第一个任务将图像旋转4秒（4000毫秒），第二个任务在2秒（2000毫秒）内缩放图像。 当第二个任务完成时，`Task.WhenAny` 方法调用完成。 但是，尽管[`RotateTo`](xref:Xamarin.Forms.ViewExtensions.RotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))方法仍在运行，但第二个[`ScaleTo`](xref:Xamarin.Forms.ViewExtensions.ScaleTo*)方法可以开始。

当完成其集合中的所有方法时，`Task.WhenAll` 方法完成，如下面的代码示例所示：

```csharp
// 10 minute animation
uint duration = 10 * 60 * 1000;

await Task.WhenAll (
  image.RotateTo (307 * 360, duration),
  image.RotateXTo (251 * 360, duration),
  image.RotateYTo (199 * 360, duration)
);
```

在此示例中，`Task.WhenAll` 方法调用包含三个任务，每个任务的执行时间超过10分钟。 每个 `Task` 为[`RotateTo`](xref:Xamarin.Forms.ViewExtensions.RotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))提供不同数量的360度旋转–307旋转，适用于[`RotateXTo`](xref:Xamarin.Forms.ViewExtensions.RotateXTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))的251旋转和[`RotateYTo`](xref:Xamarin.Forms.ViewExtensions.RotateYTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))的199旋转。 这些值是质数，因此确保旋转不会同步，因此不会导致重复的模式。

以下屏幕截图显示每个平台上正在进行的多个旋转：

![](simple-images/multiple-rotations.png "Composite Animation")

## <a name="canceling-animations"></a>取消动画

应用程序可以通过调用 `static` [`ViewExtensions.CancelAnimations`](xref:Xamarin.Forms.ViewExtensions.CancelAnimations(Xamarin.Forms.VisualElement))方法来取消一个或多个动画，如以下代码示例所示：

```csharp
ViewExtensions.CancelAnimations (image);
```

这会立即取消当前正在[`Image`](xref:Xamarin.Forms.Image)实例上运行的所有动画。

## <a name="summary"></a>总结

本文演示了如何使用[`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions)类创建和取消动画。 此类提供扩展方法，这些方法可用于构造用于旋转、缩放、平移和淡化[`VisualElement`](xref:Xamarin.Forms.VisualElement)实例的简单动画。

## <a name="related-links"></a>相关链接

- [异步支持概述](~/cross-platform/platform/async.md)
- [基本动画（示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-animation-basic)
- [ViewExtensions](xref:Xamarin.Forms.ViewExtensions)
