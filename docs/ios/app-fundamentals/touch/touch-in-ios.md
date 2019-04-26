---
title: 触摸事件和手势 Xamarin.iOS
description: 本文档介绍如何使用触摸事件、 多点触控、 手势、 多个手势和 Xamarin.iOS 应用程序中的自定义笔势。
ms.prod: xamarin
ms.assetid: DA666DC9-446E-4CD1-B5A0-C6FFBC7E53AD
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: f7160c48e1b1ac85f4aa0173c0eb9f42b8fefca2
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61218603"
---
# <a name="touch-events-and-gestures-in-xamarinios"></a>触摸事件和手势 Xamarin.iOS

务必了解触摸事件和触摸 Api 的 iOS 应用程序，因为它们是与设备的所有物理交互的中心。 所有触摸交互不涉及都到`UITouch`对象。 在本文中，我们将了解如何使用`UITouch`类和其 Api 以支持触摸。 更高版本，我们将扩展在我们的知识，了解如何支持手势。

## <a name="enabling-touch"></a>启用触摸

中的控件`UIKit`– 这些子类化从 UIControl – 都是依赖于它们具有在 UIKit 中内置的手势的用户交互，因此不需要启用了触摸。 已启用。

但是，许多的视图中的`UIKit`不提供默认情况下启用的触摸。 有两种方法启用了触摸控件上。 第一种方法是检查属性面板中的 iOS 设计器中的启用用户交互复选框下面的屏幕截图中所示：

 [![](touch-in-ios-images/image1.png "检查属性面板中的 iOS 设计器中的启用用户交互复选框")](touch-in-ios-images/image1.png#lightbox)

我们还可以使用一个控制器设置`UserInteractionEnabled`属性设置为 true 的`UIView`类。 如果在代码中创建 UI，这是必需的。

以下代码行是一个示例：

```csharp
imgTouchMe.UserInteractionEnabled = true;
```

## <a name="touch-events"></a>触摸事件

有三个阶段的触控用户触摸屏幕，将他们的手指移动或删除他们的手指时出现的。 这些方法中定义`UIResponder`，即 UIView 类的基类。 iOS 将覆盖上关联的方法`UIView`和`UIViewController`用于处理触控：

-  `TouchesBegan` – 这第一次触摸屏幕时调用。
-  `TouchesMoved` – 这与用户的触摸屏输入更改的位置滑动着手指在屏幕上四处时调用。
-  `TouchesEnded` 或`TouchesCancelled`–`TouchesEnded`用户的手指提起离开屏幕后调用。  `TouchesCancelled` 获取当调用 iOS 取消触摸 – 例如，如果用户滑他或她手指离开一个用于取消按下的某个按钮。


通过 UIViews，若要检查触摸事件是否视图对象的边界内的堆栈触摸事件旅行以递归方式。 此情况通常称作_命中测试_。 它们将首先调用上的最顶层`UIView`或`UIViewController`，然后将对调用`UIView`和`UIViewControllers`下面这些视图层次结构中。

一个`UITouch`将在用户触摸屏幕每次创建对象。 `UITouch`对象包括有关触摸，如触摸发生时，它发生位置，如果触摸轻扫等的数据。触摸事件传递的收尾工作了属性 –`NSSet`包含一个或多个收尾工作了。 我们可以使用此属性来获取对触摸屏输入，并确定应用程序的响应。

重写一个触摸事件的类应首先调用基实现，然后获取`UITouch`与事件关联的对象。 若要获取对第一次触摸的引用，请调用`AnyObject`属性并将其转换为`UITouch`一样显示在下面的示例：

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

iOS 自动识别连续快速涉及在屏幕上，并且将收集这些 all 作为点击一次在单个`UITouch`对象。 这使得检查双击简单，只检查`TapCount`属性，如下面的代码中所示：

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

默认情况下，在控件上不启用多点触控。 在 iOS 设计器中，可以启用多点触控，如以下屏幕截图所示：

 [![](touch-in-ios-images/image2.png "在 iOS 设计器中启用多点触控")](touch-in-ios-images/image2.png#lightbox)

还有可能要以编程方式设置设置多点触控`MultipleTouchEnabled`属性，如以下代码行中所示：

```csharp
imgTouchMe.MultipleTouchEnabled = true;
```

若要确定如何多个手指触摸屏幕，请使用`Count`属性上的`UITouch`属性：

```csharp
public override void TouchesBegan (NSSet touches, UIEvent evt)
{
    base.TouchesBegan (touches, evt);
    lblNumberOfFingers.Text = "Number of fingers: " + touches.Count.ToString();
}
```

## <a name="determining-touch-location"></a>确定触摸位置

该方法`UITouch.LocationInView`返回 CGPoint 对象，其中包含给定视图中的触摸设备的坐标。 此外，我们可以测试以查看该位置是否在控件内通过调用方法`Frame.Contains`。 下面的代码段显示了此示例：

```csharp
if (this.imgTouchMe.Frame.Contains (touch.LocationInView (this.View)))
{
    // the touch event happened inside the UIView imgTouchMe.
}
```

现在，我们已了解触摸事件在 iOS 中，让我们来了解有关笔势识别器。

## <a name="gesture-recognizers"></a>笔势识别器

笔势识别器可以极大地简化并减少应用程序中支持触摸的编程工作。 iOS 手势识别器聚合成单个触控事件的一系列的触控事件。

Xamarin.iOS 提供的类`UIGestureRecognizer`作为以下内置手势识别器的基类：

-  *UITapGestureRecognizer* – 这是用于一个或多个分流点。
-  *UIPinchGestureRecognizer* – Pinching 并传播相隔手指。
-  *UIPanGestureRecognizer* – 平移或拖放。
-  *UISwipeGestureRecognizer* – 任意方向轻扫。
-  *UIRotationGestureRecognizer* – 旋转顺时针或逆时针旋转动作中的两根手指。
-  *UILongPressGestureRecognizer* – 按下并保持，有时称为 long 类型的值按或单击中长时间。


使用笔势识别器的基本模式如下所示：

1.  **笔势识别器实例化**– 首先，实例化`UIGestureRecognizer`子类。 实例化的对象将关联的视图，并将其进行垃圾回收时释放该视图。 不需要创建一个类级别变量作为此视图。
1.  **配置任何手势设置**– 下一步是配置笔势识别器。 请参阅上的 Xamarin 的文档`UIGestureRecognizer`的属性，可设置为控制行为的列表及其子类`UIGestureRecognizer`实例。
1.  **配置目标**– 由于其 Objective C 遗产，Xamarin.iOS 不会引发事件时的手势识别程序匹配一个手势。  `UIGestureRecognizer` 具有一种方法 – `AddTarget` – 的可接受匿名委托或 Objective C 选择器的代码执行笔势识别器进行匹配时。
1.  **启用笔势识别器**– 就像触摸事件与手势时，将仅识别启用了触摸交互。
1.  **笔势识别器添加到视图**– 最后一步是通过调用向视图添加手势`View.AddGestureRecognizer`，并将其传递手势识别器对象。

请参阅[笔势识别器示例](~/ios/app-fundamentals/touch/ios-touch-walkthrough.md#Gesture_Recognizer_Samples)有关如何在代码中实现它们的详细信息。

当调用手势的目标时，它将传递对发生的笔势的引用。 这允许手势目标以获取有关发生的笔势的信息。 可用信息的程度取决于使用的笔势识别器的类型。 Xamarin 的文档，为每个可用的数据有关的信息，请参阅`UIGestureRecognizer`子类。

请务必记住，笔势识别器添加到视图后，该视图 （和其下的任何视图） 将不会收到任何触控事件。 若要允许同时利用手势，触摸事件`CancelsTouchesInView`属性必须设置为 false，，如下面的代码所示：

```csharp
_tapGesture.Recognizer.CancelsTouchesInView = false;
```

每个`UIGestureRecognizer`具有提供有关状态的笔势识别器的重要信息的状态属性。 每次更改此属性的值，iOS 将调用向其提供更新的订阅方法。 如果自定义笔势识别器永远不会更新状态属性，订阅服务器上将永远不会调用，使笔势识别器无用。

手势可归纳如下两种类型之一：

1.  *离散*– 它们都可以识别这些手势只触发第一个时间。
1.  *连续*– 继续激发，只要它们都可以识别这些手势。


笔势识别器存在问题的以下状态之一：

-  *可能*– 这是所有的笔势识别器的初始状态。 这是默认值的状态的属性。
-  *开始*– 时第一次识别连续手势，状态将设置为开始。 这样，订阅当启动手势识别和更改时之间进行区分。
-  *已更改*– 连续手势已开始但尚未完成后，状态将设置为已更改每次触摸屏输入移动或更改，只要它仍包含在手势的预期的参数。
-  *已取消*– 如果识别器的时间从开始到已更改，并且无法再与此类以某种方式更改收尾工作了然后适合手势的模式将设置此状态。
-  *识别*– 笔势识别器收尾工作了一组相匹配，并将通知订阅服务器上手势已经完成时，将设置状态。
-  *结束*– 这是识别状态的别名。
-  *失败*– 当笔势识别器不再匹配收尾工作了，它所侦听的状态将更改为失败。


Xamarin.iOS 表示这些值在`UIGestureRecognizerState`枚举。

## <a name="working-with-multiple-gestures"></a>使用多个手势

默认情况下，iOS 不允许同时运行的默认手势。 相反，每个手势识别器将不确定的顺序接收触摸事件。 下面的代码段说明了如何使同时运行的手势识别程序：

```csharp
gesture.ShouldRecognizeSimultaneously += (UIGestureRecognizer r) => { return true; };
```

还有可能禁用在 iOS 中的手势。 有两个委托属性可允许笔势识别器检查的应用程序和当前的触控事件，以便确定如何，并且如果应识别笔势的状态。 两个事件是：

1.  *ShouldReceiveTouch* – 笔势识别器传递触控事件，并提供机会来检查收尾工作了，并确定哪些收尾工作将由笔势识别器之前调用此委托。
1.  *ShouldBegin* – 识别程序尝试将从可能的状态更改为另一种状态时调用。 返回 false，则将强制笔势识别器来更改为失败的状态。


您可以重写这些方法使用强类型化`UIGestureRecognizerDelegate`、 弱委派或绑定通过事件处理程序语法，如下面的代码段所示：

```csharp
gesture.ShouldReceiveTouch += (UIGestureRecognizer r, UITouch t) => { return true; };
```

最后，就可以进行排队的手势识别程序，以便它才会成功如果另一个笔势识别器失败。 例如，双击手势识别器失败时，才应成功点击一下笔势识别器。 下面的代码段提供了此示例：

```csharp
singleTapGesture.RequireGestureRecognizerToFail(doubleTapGesture);
```

## <a name="creating-a-custom-gesture"></a>创建自定义笔势

尽管 iOS 提供了一些默认手势识别器，它可能需要在某些情况下创建自定义笔势识别器。 创建自定义笔势识别器涉及以下步骤：

1.  子类`UIGestureRecognizer`。
1.  重写相应的触摸事件方法。
1.  通过基类的 State 属性识别状态向上冒泡。


将在中介绍的实践示例[使用 iOS 中的触控](ios-touch-walkthrough.md)演练。
