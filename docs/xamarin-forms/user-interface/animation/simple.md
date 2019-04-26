---
title: 在 Xamarin.Forms 中的简单动画
description: ViewExtensions 类提供了可用于构造简单动画的扩展方法。 本文演示如何创建和取消使用 ViewExtensions 类的动画。
ms.prod: xamarin
ms.assetid: 4A6FAE5A-848F-4CE0-BFA1-22A6309B5225
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/27/2017
ms.openlocfilehash: 93bb138b3a78b6052715ab987a4634a18cdce317
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60896958"
---
# <a name="simple-animations-in-xamarinforms"></a>在 Xamarin.Forms 中的简单动画

[![下载示例](~/media/shared/download.png)下载示例](https://developer.xamarin.com/samples/xamarin-forms/userinterface/animation/basic/)

_ViewExtensions 类提供了可用于构造简单动画的扩展方法。本文演示如何创建和取消使用 ViewExtensions 类的动画。_


[ `ViewExtensions` ](xref:Xamarin.Forms.ViewExtensions)类提供了以下可用于创建简单动画的扩展方法：

- [`TranslateTo`](xref:Xamarin.Forms.ViewExtensions.TranslateTo(Xamarin.Forms.VisualElement,System.Double,System.Double,System.UInt32,Xamarin.Forms.Easing)) 进行动画处理[ `TranslationX` ](xref:Xamarin.Forms.VisualElement.TranslationX)并[ `TranslationY` ](xref:Xamarin.Forms.VisualElement.TranslationY)属性的[ `VisualElement` ](xref:Xamarin.Forms.VisualElement)。
- [`ScaleTo`](xref:Xamarin.Forms.VisualElement.Scale) 进行动画处理[ `Scale` ](xref:Xamarin.Forms.VisualElement.Scale)的属性[ `VisualElement` ](xref:Xamarin.Forms.VisualElement)。
- [`RelScaleTo`](xref:Xamarin.Forms.ViewExtensions.RelScaleTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) 经过动画处理的增量增加或减少到适用[ `Scale` ](xref:Xamarin.Forms.VisualElement.Scale)的属性[ `VisualElement` ](xref:Xamarin.Forms.VisualElement)。
- [`RotateTo`](xref:Xamarin.Forms.ViewExtensions.RotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) 进行动画处理[ `Rotation` ](xref:Xamarin.Forms.VisualElement.Rotation)的属性[ `VisualElement` ](xref:Xamarin.Forms.VisualElement)。
- [`RelRotateTo`](xref:Xamarin.Forms.ViewExtensions.RelRotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) 经过动画处理的增量增加或减少到适用[ `Rotation` ](xref:Xamarin.Forms.VisualElement.Rotation)的属性[ `VisualElement` ](xref:Xamarin.Forms.VisualElement)。
- [`RotateXTo`](xref:Xamarin.Forms.ViewExtensions.RotateXTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) 进行动画处理[ `RotationX` ](xref:Xamarin.Forms.VisualElement.RotationX)的属性[ `VisualElement` ](xref:Xamarin.Forms.VisualElement)。
- [`RotateYTo`](xref:Xamarin.Forms.ViewExtensions.RotateYTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) 进行动画处理[ `RotationY` ](xref:Xamarin.Forms.VisualElement.RotationY)的属性[ `VisualElement` ](xref:Xamarin.Forms.VisualElement)。
- [`FadeTo`](xref:Xamarin.Forms.ViewExtensions.FadeTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) 进行动画处理[ `Opacity` ](xref:Xamarin.Forms.VisualElement.Opacity)的属性[ `VisualElement` ](xref:Xamarin.Forms.VisualElement)。

默认情况下，每个动画将采用 250 毫秒。 但是，创建动画时，可以指定每个动画的持续时间。

[ `ViewExtensions` ](xref:Xamarin.Forms.ViewExtensions)类还包括[ `CancelAnimations` ](xref:Xamarin.Forms.ViewExtensions.CancelAnimations(Xamarin.Forms.VisualElement))可用于取消任何动画的方法。

> [!NOTE]
> [ `ViewExtensions` ](xref:Xamarin.Forms.ViewExtensions)类提供[ `LayoutTo` ](xref:Xamarin.Forms.ViewExtensions.LayoutTo(Xamarin.Forms.VisualElement,Xamarin.Forms.Rectangle,System.UInt32,Xamarin.Forms.Easing))扩展方法。 但是，此方法旨在由布局进行动画处理的包含大小和定位更改布局状态之间转换。 因此，它应仅由[ `Layout` ](xref:Xamarin.Forms.Layout)子类。

中的动画扩展方法[ `ViewExtensions` ](xref:Xamarin.Forms.ViewExtensions)类是所有异步、 返回`Task<bool>`对象。 返回值是`false`动画完成后，如果和`true`如果动画将被取消。 因此，动画方法通常都应与使用`await`运算符，这使得可以轻松地确定何时完成动画。 此外，然后就可以通过执行前一种方法完成后的后续动画方法创建顺序动画。 有关详细信息，请参阅[复合动画](#compound)。

如果要求让动画在后台完成则`await`运算符，则可以省略。 在此方案中，动画扩展方法将快速启动动画，与在后台进行动画后返回。 此操作时可以采取的优点创建复合动画。 有关详细信息，请参阅[复合动画](#composite)。

有关详细信息`await`运算符，请参阅[异步支持概述](~/cross-platform/platform/async.md)。

## <a name="single-animations"></a>单个动画

在每个扩展方法[ `ViewExtensions` ](xref:Xamarin.Forms.ViewExtensions)实现渐进式属性从一个值更改为另一个值的时间段内的单个动画操作。 本部分探讨每个动画操作。

### <a name="rotation"></a>旋转

下面的代码示例演示了如何使用[ `RotateTo` ](xref:Xamarin.Forms.ViewExtensions.RotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))方法进行动画处理[ `Rotation` ](xref:Xamarin.Forms.VisualElement.Rotation)属性的[ `Image` ](xref:Xamarin.Forms.Image):

```csharp
await image.RotateTo (360, 2000);
image.Rotation = 0;
```

此代码之间进行动画处理[ `Image` ](xref:Xamarin.Forms.Image)通过旋转超过 2 秒 （2000年毫秒） 的最多 360 度的实例。 [ `RotateTo` ](xref:Xamarin.Forms.ViewExtensions.RotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))方法获取当前[ `Rotation` ](xref:Xamarin.Forms.VisualElement.Rotation)属性值的动画，开头，然后从该值旋转到其第一个参数 (360)。 一旦在动画完成，该映像[ `Rotation` ](xref:Xamarin.Forms.VisualElement.Rotation)属性重置为 0。 这可确保`Rotation`动画结束，可能会妨碍其他旋转后，属性不会保留在 360。

下面的屏幕截图显示在每个平台上进行旋转：

![](simple-images/rotateto.png "旋转动画")

### <a name="relative-rotation"></a>相对旋转

下面的代码示例演示了如何使用[ `RelRotateTo` ](xref:Xamarin.Forms.ViewExtensions.RelRotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))方法来以增量方式增加或减少[ `Rotation` ](xref:Xamarin.Forms.VisualElement.Rotation)属性的[ `Image` ](xref:Xamarin.Forms.Image):

```csharp
await image.RelRotateTo (360, 2000);
```

此代码之间进行动画处理[ `Image` ](xref:Xamarin.Forms.Image)通过旋转 360 度从其起始位置超过 2 秒 （2000年毫秒） 的实例。 [ `RelRotateTo` ](xref:Xamarin.Forms.ViewExtensions.RelRotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))方法获取当前[ `Rotation` ](xref:Xamarin.Forms.VisualElement.Rotation)属性值的动画，开头，然后从该值旋转到值加上其第一个参数 (360)。 这可确保每个动画将始终是从起始位置的 360 度旋转。 因此，如果调用新动画的动画已正在进行时，它将开始从当前位置，并可能最终不是递增的 360 度的位置。

下面的屏幕截图显示每个平台上进行中的相对旋转：

![](simple-images/relrotateto.png "相对旋转动画")

### <a name="scaling"></a>缩放

下面的代码示例演示了如何使用[ `ScaleTo` ](xref:Xamarin.Forms.VisualElement.Scale)方法进行动画处理[ `Scale` ](xref:Xamarin.Forms.VisualElement.Scale)属性的[ `Image` ](xref:Xamarin.Forms.Image):

```csharp
await image.ScaleTo (2, 2000);
```

此代码之间进行动画处理[ `Image` ](xref:Xamarin.Forms.Image)通过纵向扩展到两次其大小超过 2 秒 （2000年毫秒） 的实例。 [ `ScaleTo` ](xref:Xamarin.Forms.VisualElement.Scale)方法获取当前[ `Scale` ](xref:Xamarin.Forms.VisualElement.Scale)开头的动画，然后可从该值扩展到其第一个参数 (2) 的属性值 （默认值为 1）。 此效果扩展到其大小的两倍的图像的大小。

以下屏幕截图显示在每个平台上进行缩放：

![](simple-images/scaleto.png "缩放动画")

> [!NOTE]
> [ `VisualElement` ](xref:Xamarin.Forms.VisualElement)类还定义[ `ScaleX` ](xref:Xamarin.Forms.VisualElement.ScaleX)并[ `ScaleY` ](xref:Xamarin.Forms.VisualElement.ScaleY)属性，可以缩放`VisualElement`中按不同方式水平和垂直方向。 可以使用这些属性进行动画处理[ `Animation` ](xref:Xamarin.Forms.Animation)类。 有关详细信息，请参阅[Xamarin.Forms 中的自定义动画](custom.md)。

### <a name="relative-scaling"></a>相对缩放

下面的代码示例演示了如何使用[ `RelScaleTo` ](xref:Xamarin.Forms.ViewExtensions.RelScaleTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))方法进行动画处理[ `Scale` ](xref:Xamarin.Forms.VisualElement.Scale)属性的[ `Image` ](xref:Xamarin.Forms.Image):

```csharp
await image.RelScaleTo (2, 2000);
```

此代码之间进行动画处理[ `Image` ](xref:Xamarin.Forms.Image)通过纵向扩展到两次其大小超过 2 秒 （2000年毫秒） 的实例。 [ `RelScaleTo` ](xref:Xamarin.Forms.ViewExtensions.RelScaleTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))方法获取当前[ `Scale` ](xref:Xamarin.Forms.VisualElement.Scale)开头的动画，然后可从该值扩展到其第一个参数 (2) 加上值的属性值。 这可确保每个动画将始终是从起始位置 2 的伸缩性。

### <a name="scaling-and-rotation-with-anchors"></a>缩放和旋转与定位点

[ `AnchorX` ](xref:Xamarin.Forms.VisualElement.AnchorX)并[ `AnchorY` ](xref:Xamarin.Forms.VisualElement.AnchorY)属性设置的缩放或旋转中心[ `Rotation` ](xref:Xamarin.Forms.VisualElement.Rotation)和[ `Scale`](xref:Xamarin.Forms.VisualElement.Scale)属性。 因此，它们的值也会影响[ `RotateTo` ](xref:Xamarin.Forms.ViewExtensions.RotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))并[ `ScaleTo` ](xref:Xamarin.Forms.VisualElement.Scale)方法。

给定[ `Image` ](xref:Xamarin.Forms.Image)已被放在中心的一种布局，下面的代码示例演示了如何通过设置轮换图像的布局中心及其[ `AnchorY` ](xref:Xamarin.Forms.VisualElement.AnchorY)属性：

```csharp
image.AnchorY = (Math.Min (absoluteLayout.Width, absoluteLayout.Height) / 2) / image.Height;
await image.RotateTo (360, 2000);
```

若要旋转[ `Image` ](xref:Xamarin.Forms.Image)围绕的中心实例的格式，请[ `AnchorX` ](xref:Xamarin.Forms.VisualElement.AnchorX)并[ `AnchorY` ](xref:Xamarin.Forms.VisualElement.AnchorY)属性必须设置为值相对于宽度和高度`Image`。 在此示例中，中心的`Image`定义要在布局中，在中心，因此默认`AnchorX`0.5 的值不需要更改。 但是，`AnchorY`属性被重定义为顶部的值`Image`到布局的中心点。 这可确保`Image`使布局的中心点围绕 360 度的旋转一圈，如以下屏幕截图中所示：

![](simple-images/rotate-anchors.png "使用定位点的旋转动画")

### <a name="translation"></a>转换

下面的代码示例演示了如何使用[ `TranslateTo` ](xref:Xamarin.Forms.ViewExtensions.TranslateTo(Xamarin.Forms.VisualElement,System.Double,System.Double,System.UInt32,Xamarin.Forms.Easing))方法进行动画处理[ `TranslationX` ](xref:Xamarin.Forms.VisualElement.TranslationX)并[ `TranslationY` ](xref:Xamarin.Forms.VisualElement.TranslationY) 属性[`Image`](xref:Xamarin.Forms.Image):

```csharp
await image.TranslateTo (-100, -100, 1000);
```

此代码之间进行动画处理[ `Image` ](xref:Xamarin.Forms.Image)实例将转换其水平和垂直超过 1 秒 （1000年毫秒为单位）。 [ `TranslateTo` ](xref:Xamarin.Forms.ViewExtensions.TranslateTo(Xamarin.Forms.VisualElement,System.Double,System.Double,System.UInt32,Xamarin.Forms.Easing))映像 100 像素的左侧，并向上 100 像素，同时将转换方法。 这是因为第一个和第二个参数均为这两个负数。 提供正号将其转换到右和向下的图像。

下面的屏幕截图显示每个平台上进行中的翻译：

![](simple-images/translateto.png "翻译动画")

> [!NOTE]
> 如果元素是最初离开屏幕的布局，则转换到屏幕上，在转换后的元素的输入的布局保持状态屏幕外并且用户不能与之交互。 因此，建议，应在其最后一个位置中布局视图，然后所需的任何执行的转换。

### <a name="fading"></a>淡入淡出

下面的代码示例演示了如何使用[ `FadeTo` ](xref:Xamarin.Forms.ViewExtensions.FadeTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))方法进行动画处理[ `Opacity` ](xref:Xamarin.Forms.VisualElement.Opacity)属性的[ `Image` ](xref:Xamarin.Forms.Image):

```csharp
image.Opacity = 0;
await image.FadeTo (1, 4000);
```

此代码之间进行动画处理[ `Image` ](xref:Xamarin.Forms.Image)通过淡入淡出它在超过 4 秒 （4000 毫秒为单位） 的实例。 [ `FadeTo` ](xref:Xamarin.Forms.ViewExtensions.FadeTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))方法获取当前[ `Opacity` ](xref:Xamarin.Forms.VisualElement.Opacity)开头的动画，然后淡中的从该值到其第一个参数 (1) 的属性值。

下面的屏幕截图显示每个平台上进行中的淡入淡出：

![](simple-images/fadeto.png "淡入淡出动画")

<a name="compound" />

## <a name="compound-animations"></a>复合动画

复合动画是顺序组合的动画，并且可用于创建`await`运算符，如以下代码示例所示：

```csharp
await image.TranslateTo (-100, 0, 1000);    // Move image left
await image.TranslateTo (-100, -100, 1000); // Move image up
await image.TranslateTo (100, 100, 2000);   // Move image diagonally down and right
await image.TranslateTo (0, 100, 1000);     // Move image left
await image.TranslateTo (0, 0, 1000);       // Move image up
```

在此示例中， [ `Image` ](xref:Xamarin.Forms.Image)转换超过 6 秒 （6000 毫秒为单位）。 翻译`Image`使用五个动画`await`运算符，指示按顺序执行每个动画。 因此，后续的动画方法来执行前一种方法完成后。

<a name="composite" />

## <a name="composite-animations"></a>复合动画

复合动画是组合动画的两个或多个动画同时运行。 可以通过混合使用等待和非等待动画创建复合动画，如下面的代码示例中所示：

```csharp
image.RotateTo (360, 4000);
await image.ScaleTo (2, 2000);
await image.ScaleTo (1, 2000);
```

在此示例中， [ `Image` ](xref:Xamarin.Forms.Image)已缩放和同时旋转超过 4 秒 （4000 毫秒为单位）。 缩放`Image`使用出现旋转时间的两个连续的动画。 [ `RotateTo` ](xref:Xamarin.Forms.ViewExtensions.RotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))方法执行，但不`await`运算符并立即返回，第一个[ `ScaleTo` ](xref:Xamarin.Forms.VisualElement.Scale)然后开始的动画。 `await`在第一个运算符`ScaleTo`方法调用会将第二个延迟`ScaleTo`直到第一个方法调用`ScaleTo`方法调用已完成。 此时`RotateTo`动画是一半方式完成和`Image`将旋转 180 度。 在最终的 2 秒 （2000年毫秒），第二个`ScaleTo`动画和`RotateTo`同时完成动画。

### <a name="running-multiple-asynchronous-methods-concurrently"></a>同时运行多个异步方法

`static` `Task.WhenAny`和`Task.WhenAll`方法用于同时运行多个异步方法，因此可以用于创建复合动画。 这两种方法返回`Task`对象，并接受的方法的集合，每个返回`Task`对象。 `Task.WhenAny`方法完成时其集合中的任何方法完成执行，如下面的代码示例中所示：

```csharp
await Task.WhenAny<bool>
(
  image.RotateTo (360, 4000),
  image.ScaleTo (2, 2000)
);
await image.ScaleTo (1, 2000);
```

在此示例中，`Task.WhenAny`方法调用包含两个任务。 第一个任务将图像旋转超过 4 秒 （4000 毫秒为单位），且第二个任务可以缩放该图像超过 2 秒 （2000年毫秒）。 第二个任务完成后，`Task.WhenAny`方法调用完成。 但是，即使[ `RotateTo` ](xref:Xamarin.Forms.ViewExtensions.RotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))方法仍在运行，第二个[ `ScaleTo` ](xref:Xamarin.Forms.VisualElement.Scale)方法可以开始。

`Task.WhenAll`方法完成时完成其集合中的所有方法，如下面的代码示例中所示：

```csharp
// 10 minute animation
uint duration = 10 * 60 * 1000;

await Task.WhenAll (
  image.RotateTo (307 * 360, duration),
  image.RotateXTo (251 * 360, duration),
  image.RotateYTo (199 * 360, duration)
);
```

在此示例中，`Task.WhenAll`方法调用包含三个任务，其中每个执行时间超过 10 分钟。 每个`Task`使得不同数量的 360 度旋转 – 为 307 旋转[ `RotateTo` ](xref:Xamarin.Forms.ViewExtensions.RotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))，为 251 旋转[ `RotateXTo` ](xref:Xamarin.Forms.ViewExtensions.RotateXTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))，和 199 循环[ `RotateYTo`](xref:Xamarin.Forms.ViewExtensions.RotateYTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)). 这些值是质数，因此确保旋转的未同步，并因此不会导致重复模式。

下面的屏幕截图显示每个平台上进行中的多个旋转：

![](simple-images/multiple-rotations.png "复合动画")

## <a name="canceling-animations"></a>正在取消动画

应用程序可以取消通过调用一个或多个动画`static` [ `ViewExtensions.CancelAnimations` ](xref:Xamarin.Forms.ViewExtensions.CancelAnimations(Xamarin.Forms.VisualElement))方法，如下面的代码示例中所示：

```csharp
ViewExtensions.CancelAnimations (image);
```

这将立即取消当前正在运行的所有动画[ `Image` ](xref:Xamarin.Forms.Image)实例。

## <a name="summary"></a>总结

本文说明了创建和取消使用动画[ `ViewExtensions` ](xref:Xamarin.Forms.ViewExtensions)类。 此类提供扩展方法可用于构造简单动画的旋转、 缩放、 转换，和淡化[ `VisualElement` ](xref:Xamarin.Forms.VisualElement)实例。


## <a name="related-links"></a>相关链接

- [异步支持概述](~/cross-platform/platform/async.md)
- [基本动画 （示例）](https://developer.xamarin.com/samples/xamarin-forms/userinterface/animation/basic/)
- [ViewExtensions](xref:Xamarin.Forms.ViewExtensions)
