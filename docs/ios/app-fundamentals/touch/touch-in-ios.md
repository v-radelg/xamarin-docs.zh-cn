---
title: Xamarin 中的触摸事件和手势
description: 本文档介绍如何在 Xamarin iOS 应用程序中处理触控事件、多点触控、手势、多个手势和自定义手势。
ms.prod: xamarin
ms.assetid: DA666DC9-446E-4CD1-B5A0-C6FFBC7E53AD
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/18/2017
ms.openlocfilehash: 885e5862e517303b4a2556b0b1bd3fa8759bbca6
ms.sourcegitcommit: 52fb214c0e0243587d4e9ad9306b75e92a8cc8b7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/01/2020
ms.locfileid: "76940887"
---
# <a name="touch-events-and-gestures-in-xamarinios"></a>Xamarin 中的触摸事件和手势

在 iOS 应用程序中了解触摸事件和触控 Api，这一点很重要，因为它们是与设备的所有物理交互的核心。 所有触摸交互都涉及一个 `UITouch` 对象。 在本文中，我们将了解如何使用 `UITouch` 类及其 Api 来支持触摸。 稍后，我们将在知识中展开以了解如何支持手势。

## <a name="enabling-touch"></a>启用触控

`UIKit` 中的控件-来自 UIControl 的子类–因此依赖于用户交互，这些用户交互的手势内置于 UIKit 中，因此不需要启用触控。 已启用该功能。

但是，`UIKit` 中的许多视图默认情况下未启用触摸。 可以通过两种方法在控件上启用触摸。 第一种方法是在 iOS 设计器的属性板中检查 "已启用用户交互" 复选框，如以下屏幕截图所示：

 [![](touch-in-ios-images/image1.png "Check the User Interaction Enabled checkbox in the Property Pad of the iOS Designer")](touch-in-ios-images/image1.png#lightbox)

对于 `UIView` 类，还可以使用控制器将 `UserInteractionEnabled` 属性设置为 true。 如果 UI 是在代码中创建的，则此项是必需的。

下面的代码行是一个示例：

```csharp
imgTouchMe.UserInteractionEnabled = true;
```

## <a name="touch-events"></a>触摸事件

用户触摸屏幕、移动手指或删除手指时，会出现三个触摸阶段。 这些方法是在 `UIResponder`中定义的，这是 UIView 的基类。 iOS 将覆盖 `UIView` 上的关联方法和 `UIViewController` 来处理触控：

- `TouchesBegan` –第一次接触屏幕时，将调用此。
- `TouchesMoved` –当触摸位置随着用户在屏幕上滑动手指时，会调用此方法。
- `TouchesEnded` 或 `TouchesCancelled` –当用户的手指从屏幕上提起时，将调用 `TouchesEnded`。  如果 iOS 取消了触摸，则会调用 `TouchesCancelled`，例如，如果用户从按钮滑开按钮以取消按下。

触摸事件在 UIViews 堆栈中以递归方式传播，以检查触控事件是否在视图对象的边界内。 这通常称为_命中测试_。 首先在最顶层的 `UIView` 或 `UIViewController` 调用它们，然后在 `UIView` 上调用，然后在视图层次结构中的下面 `UIViewControllers`。

用户每次触摸屏幕时都会创建一个 `UITouch` 对象。 "`UITouch`" 对象包括有关触摸数据的数据，例如触摸发生时、触摸屏发生的位置、触摸屏输入点等。触摸事件传递了触摸属性–包含一个或多个触控的 `NSSet`。 我们可以使用此属性来获取对触控的引用，并确定应用程序的响应。

重写某个触摸事件的类应首先调用基实现，然后获取与该事件关联的 `UITouch` 对象。 若要获取对第一个触控的引用，请调用 `AnyObject` 属性，并将其转换为 `UITouch`，如以下示例中所示：

```csharp
public override void TouchesBegan (NSSet touches, UIEvent evt)
{
    base.TouchesBegan (touches, evt);
    UITouch touch = touches.AnyObject as UITouch;
    if (touch != null)
    {
        //code here to handle touch
    }
}
```

iOS 自动识别屏幕上的连续快速触控，并在单个 `UITouch` 对象中一点击即可将其全部收集。 这样就可以像检查 `TapCount` 属性一样轻松地进行双击，如以下代码所示：

```csharp
public override void TouchesBegan (NSSet touches, UIEvent evt)
{
    base.TouchesBegan (touches, evt);
    UITouch touch = touches.AnyObject as UITouch;
    if (touch != null)
    {
        if (touch.TapCount == 2)
        {
            // do something with the double touch.
        }
    }
}
```

## <a name="multi-touch"></a>多点触控

默认情况下，在控件上未启用多点触控。 可以在 iOS 设计器中启用多点触控，如以下屏幕截图所示：

 [![](touch-in-ios-images/image2.png "Multi-touch enabled in the iOS Designer")](touch-in-ios-images/image2.png#lightbox)

还可以通过设置 `MultipleTouchEnabled` 属性以编程方式设置多点触控，如以下代码行所示：

```csharp
imgTouchMe.MultipleTouchEnabled = true;
```

若要确定触摸屏幕的手指数量，请使用 `UITouch` 属性的 `Count` 属性：

```csharp
public override void TouchesBegan (NSSet touches, UIEvent evt)
{
    base.TouchesBegan (touches, evt);
    lblNumberOfFingers.Text = "Number of fingers: " + touches.Count.ToString();
}
```

## <a name="determining-touch-location"></a>确定触摸位置

方法 `UITouch.LocationInView` 返回一个 CGPoint 对象，该对象在给定视图中保存触摸的坐标。 此外，我们还可以通过调用方法 `Frame.Contains`来测试该位置是否在控件内。 下面的代码片段演示了一个示例：

```csharp
if (this.imgTouchMe.Frame.Contains (touch.LocationInView (this.View)))
{
    // the touch event happened inside the UIView imgTouchMe.
}
```

现在我们已经了解了 iOS 中的触摸事件，接下来让我们了解手势识别器。

## <a name="gesture-recognizers"></a>手势识别器

手势识别器可以极大地简化和减少在应用程序中支持触控的编程工作量。 iOS 手势识别器将一系列触摸事件聚合为单个触控事件。

Xamarin 提供类 `UIGestureRecognizer` 作为以下内置笔势识别器的基类：

- *UITapGestureRecognizer* –这适用于一个或多个点击。
- *UIPinchGestureRecognizer* –收缩和散布。
- *UIPanGestureRecognizer* –平移或拖动。
- *UISwipeGestureRecognizer* -沿任意方向轻扫。
- *UIRotationGestureRecognizer* –顺时针或逆时针运动旋转两根手指。
- *UILongPressGestureRecognizer* –按下并保持，有时称为长按下或长时间单击。

使用手势识别器的基本模式如下所示：

1. **实例化手势识别器**–首先实例化 `UIGestureRecognizer` 子类。 实例化的对象将由视图关联，并将在处理视图时进行垃圾收集。 不需要将此视图创建为类级别的变量。
1. **配置任何手势设置**–下一步是配置笔势识别器。 有关可设置为控制 `UIGestureRecognizer` 实例行为的属性列表，请查阅 Xamarin 的有关 `UIGestureRecognizer` 及其子类的文档。
1. **配置目标**–由于其目标为 C 的遗产，因此当手势识别器匹配笔势时，Xamarin 不会引发事件。  `UIGestureRecognizer` 具有方法– `AddTarget` –可以接受匿名委托，或使用在笔势识别器进行匹配时要执行的代码。
1. **启用手势识别器**–与使用触控事件一样，仅当启用触控交互时才会识别手势。
1. 向**视图添加手势识别器**–最后一步是通过调用 `View.AddGestureRecognizer` 将手势添加到视图，并向其传递笔势识别器对象。

有关如何在代码中实现它们的详细信息，请参阅[手势识别器示例](~/ios/app-fundamentals/touch/ios-touch-walkthrough.md#Gesture_Recognizer_Samples)。

调用该笔势的目标后，将向其传递对所发生的手势的引用。 这允许该笔势目标获取有关所发生的手势的信息。 可用的信息范围取决于所使用的笔势识别器的类型。 有关可用于每个 `UIGestureRecognizer` 子类的数据的信息，请参阅 Xamarin 的文档。

请记住，在将笔势识别器添加到视图后，视图（以及它下面的任何视图）不会收到任何触控事件。 若要允许同时使用触控事件和笔势，则 `CancelsTouchesInView` 属性必须设置为 false，如以下代码所示：

```csharp
_tapGesture.Recognizer.CancelsTouchesInView = false;
```

每个 `UIGestureRecognizer` 都有一个 State 属性，该属性提供有关笔势识别器状态的重要信息。 此属性的值每次发生更改时，iOS 将调用订阅方法来提供更新。 如果自定义手势识别器从不更新 State 属性，则永远不会调用该订阅服务器，因此，呈现手势识别器毫无用处。

手势可以汇总为以下两种类型之一：

1. *离散*–这些手势仅在首次识别时才会激发。
1. *连续*–只要识别这些手势，它们就会继续激发。

笔势识别器存在于以下状态之一：

- *可能*–这是所有手势识别器的初始状态。 这是 State 属性的默认值。
- *开始*–首次识别连续手势时，状态将设置为 "已开始"。 这使订阅可以区分手势识别开始的时间和更改时间。
- *Changed* –当连续手势开始但尚未完成后，每次触摸移动或更改时，状态将设置为 "已更改"，前提是该动作仍处于笔势的预期参数内。
- 已*取消*–如果识别器开始发生更改，则会设置此状态，然后，所做的更改将在中进行更改，使其不再适合手势的模式。
- 已*识别*–当手势识别器匹配一组触摸时，将设置状态，并通知订阅者该笔势已完成。
- 已*结束*–这是可识别状态的别名。
- *Failed* –当手势识别器不能再与它正在侦听的触控不匹配时，状态将更改为 "已失败"。

Xamarin 在 `UIGestureRecognizerState` 枚举中表示这些值。

## <a name="working-with-multiple-gestures"></a>使用多个手势

默认情况下，iOS 不允许同时运行默认手势。 相反，每个手势识别器将按不确定的顺序接收触控事件。 下面的代码片段演示了如何使手势识别器同时运行：

```csharp
gesture.ShouldRecognizeSimultaneously += (UIGestureRecognizer r) => { return true; };
```

还可以在 iOS 中禁用手势。 可以使用两个委托属性来检查应用程序的状态和当前的触摸事件，以决定是否应识别笔势。 这两个事件是：

1. *ShouldReceiveTouch* –在手势识别器传递触控事件之前，将调用此委托，并提供检查触控并决定手势识别器将处理哪些触摸的机会。
1. *ShouldBegin* –在识别器尝试将状态从可能更改为其他状态时调用。 如果返回 false，则会强制将手势识别器的状态更改为 "已失败"。

您可以使用强类型的 `UIGestureRecognizerDelegate`、弱委托或通过事件处理程序语法进行绑定来重写这些方法，如以下代码片段所示：

```csharp
gesture.ShouldReceiveTouch += (UIGestureRecognizer r, UITouch t) => { return true; };
```

最后，可以将手势识别器排队，以便仅当另一个手势识别器失败时才会成功。 例如，只有在双击手势识别器失败时，单点击手势识别器才会成功。 以下代码片段提供了一个示例：

```csharp
singleTapGesture.RequireGestureRecognizerToFail(doubleTapGesture);
```

## <a name="creating-a-custom-gesture"></a>创建自定义手势

尽管 iOS 提供了一些默认的手势识别器，但在某些情况下可能需要创建自定义手势识别器。 创建自定义手势识别器涉及以下步骤：

1. 子类 `UIGestureRecognizer`。
1. 重写相应的触摸事件方法。
1. 通过基类的状态属性来向上冒泡识别状态。

[使用 iOS](ios-touch-walkthrough.md)演练中介绍了这种情况的一个实际示例。
