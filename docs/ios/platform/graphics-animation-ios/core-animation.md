---
title: Xamarin 中的核心动画
description: 本文将介绍核心动画框架，其中展示了它如何在 UIKit 中实现高性能的流畅动画，以及如何将其直接用于较低级别的动画控制。
ms.prod: xamarin
ms.assetid: D4744147-FACB-415B-8155-3A6B3C35E527
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/18/2017
ms.openlocfilehash: 60bab56440fc7227e14d31875a8b6108cd1a86f3
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73032484"
---
# <a name="core-animation-in-xamarinios"></a>Xamarin 中的核心动画

_本文将介绍核心动画框架，其中展示了它如何在 UIKit 中实现高性能的流畅动画，以及如何将其直接用于较低级别的动画控制。_

iOS 包括[*核心动画*](https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/CoreAnimation_guide/Introduction/Introduction.html)，用于为应用程序中的视图提供动画支持。
IOS 中的所有超高平滑动画（如滚动表和在不同视图之间进行轻扫）都将执行，因为它们在内部依赖于核心动画。

核心动画和核心图形框架可协同工作，创建精美的动画二维图形。 事实上，核心动画甚至可在三维空间中转换2D 图形，创建令人惊叹的迷人体验。 但是，若要创建真正的3D 图形，你需要使用诸如 OpenGL ES 之类的内容，或者将游戏转换为 API （如 MonoGame），尽管3D 超出了本文的范围。

<a name="Using_Core_Animation" />

## <a name="core-animation"></a>核心动画

iOS 使用核心动画框架来创建动画效果，例如，在视图之间过渡、滑动菜单和滚动效果等。 可以通过两种方式来使用动画：

- [通过 UIKit](#Using_UIKit_Animation)，其中包含基于视图的动画以及控制器间的动画转换。
- [通过核心动画](#Using_Core_Animation)（直接），允许进行更精细的控制。

<a name="Using_UIKit_Animation" />

## <a name="using-uikit-animation"></a>使用 UIKit 动画

UIKit 提供了几项功能，使你可以轻松地向应用程序添加动画。 尽管它在内部使用核心动画，但它会将其抽象化，以便仅使用视图和控制器。

本部分介绍 UIKit 动画功能，包括：

- 控制器间的转换
- 视图间的转换
- 查看属性动画

### <a name="view-controller-transitions"></a>视图控制器转换

 `UIViewController` 通过 `PresentViewController` 方法为视图控制器之间的转换提供内置支持。 使用 `PresentViewController`时，可以选择性地对转换为第二个控制器的转换进行动画处理。

例如，假设有一个具有两个控制器的应用程序，在该应用程序中，按第一个控制器中的按钮会调用 `PresentViewController` 以显示第二个控制器。 若要控制用于显示第二个控制器的转换动画，只需设置其[`ModalTransitionStyle`](xref:UIKit.UIModalTransitionStyle)属性，如下所示：

```csharp
SecondViewController vc2 = new SecondViewController {
  ModalTransitionStyle = UIModalTransitionStyle.PartialCurl
};
```

在这种情况下，将使用 `PartialCurl` 的动画，尽管还有其他一些可用的动画，其中包括：

- `CoverVertical` –从屏幕底部向上滑动
- `CrossDissolve` –旧视图在新视图被淡化 & 淡出
- `FlipHorizontal`-从右到左水平翻转。 在消除上，过渡从左向右翻转。

若要对转换进行动画处理，请将 `true` 作为第二个自变量传递 `PresentViewController`：

```csharp
PresentViewController (vc2, true, null);
```

以下屏幕截图显示了 `PartialCurl` 情况下转换的样子：

 ![](core-animation-images/06-view-transitions.png "This screenshot shows the PartialCurl transition")

### <a name="view-transitions"></a>查看过渡

除了控制器间的转换以外，UIKit 还支持在视图之间对转换进行动画处理，以交换另一个视图的视图。

例如，假设有一个控制器用于 `UIImageView`，其中点击该图像应显示第二个 `UIImageView`。 若要动画处理视图的 superview 以转换为第二个图像视图，只需要调用 `UIView.Transition`，并向其传递 `toView` 和 `fromView` 如下所示：

```csharp
UIView.Transition (
  fromView: view1,
  toView: view2,
  duration: 2,
  options: UIViewAnimationOptions.TransitionFlipFromTop |
    UIViewAnimationOptions.CurveEaseInOut,
  completion: () => { Console.WriteLine ("transition complete"); });
```

`UIView.Transition` 还采用 `duration` 参数来控制动画运行的时间，并使用[`options`](xref:UIKit.UIViewAnimationOptions)来指定要使用的动画和缓动函数。 此外，还可以指定在动画完成时要调用的完成处理程序。

以下屏幕截图显示了使用 `TransitionFlipFromTop` 时，图像视图之间的动画转换：

 ![](core-animation-images/07-animated-transition.png "This screenshot shows the animated transition between the image views when TransitionFlipFromTop is used")

### <a name="view-property-animations"></a>查看属性动画

UIKit 支持对 `UIView` 类的各种属性进行免费动态处理，包括：

- Frame
- 汇编
- 居中
- Alpha
- Transform
- 颜色

通过指定传递给静态 `UIView.Animate` 方法的 `NSAction` 委托中的属性更改，隐式地发生这些动画。 例如，下面的代码对 `UIImageView`的中心点进行了动画处理：

```csharp
pt = imgView.Center;

UIView.Animate (
  duration: 2, 
  delay: 0, 
  options: UIViewAnimationOptions.CurveEaseInOut | 
    UIViewAnimationOptions.Autoreverse,
  animation: () => {
    imgView.Center = new CGPoint (View.Bounds.GetMaxX () 
      - imgView.Frame.Width / 2, pt.Y);},
  completion: () => {
    imgView.Center = pt; }
);
```

这会使图像在屏幕的顶部来回进行动画处理，如下所示：

 ![](core-animation-images/08-animate-center.png "An image animating back and forth across the top of the screen as the output")

与 `Transition` 方法一样，`Animate` 允许设置持续时间和缓动函数。 此示例还使用了 `UIViewAnimationOptions.Autoreverse` 选项，该选项会使动画从值动画返回到第一个选项。 但是，代码还会将 `Center` 设置回其在完成处理程序中的初始值。 当动画在一段时间内插值属性值时，属性的实际模型值始终是已设置的最终值。 在此示例中，值是 superview 右侧附近的一个点。 如果不将 `Center` 设置为初始点（动画完成的原因是由于正在设置 `Autoreverse`，则在动画完成后，图像将会与右侧对齐，如下所示：

 ![](core-animation-images/09-animation-complete.png "Without setting the Center to the initial point, the image would snap back to the right side after the animation completes")

## <a name="using-core-animation"></a>使用核心动画

 `UIView` 动画允许多种功能，应尽可能使用（如果可能）。 如前文所述，UIView 动画使用核心动画框架。 但是，某些事情无法用 `UIView` 动画完成，如对不能使用视图进行动画处理的附加属性进行动画处理，或沿非线性路径插值。 在需要更精细控制的情况下，也可以直接使用核心动画。

### <a name="layers"></a>层面

使用核心动画时，动画通过类型 `CALayer`的*层*发生。 层在概念上类似于视图，其中有一个层层次结构，与视图层次结构非常类似。 实际上，层返回视图，视图添加了对用户交互的支持。 您可以通过视图的 `Layer` 属性访问任何视图的层。 事实上，`UIView` 的 `Draw` 方法中使用的上下文实际上是从层创建的。 在内部，支持 `UIView` 的层将其委托设置为视图本身，这是调用 `Draw`的。 因此，当绘制到 `UIView`时，实际上会绘制到它的层。

层动画可以是隐式的，也可以是显式的。 隐式动画是声明性的。 只需声明应更改的层属性，动画就会正常运行。 另一方面，显式动画是通过添加到层的动画类创建的。 显式动画允许对动画的创建方式进行额外的控制。 以下部分深入探讨隐式和显式动画。

### <a name="implicit-animations"></a>隐式动画

对层的属性进行动画处理的一种方法是通过隐式动画。 `UIView` 动画创建隐式动画。 不过，也可以直接针对层创建隐式动画。

例如，下面的代码从图像设置一个层的 `Contents`，设置边框宽度和颜色，并将该层添加为视图层的子层：

```csharp
public override void ViewDidLoad ()
{
  base.ViewDidLoad ();

  layer = new CALayer ();
  layer.Bounds = new CGRect (0, 0, 50, 50);
  layer.Position = new CGPoint (50, 50);
  layer.Contents = UIImage.FromFile ("monkey2.png").CGImage;
  layer.ContentsGravity = CALayer.GravityResize;
  layer.BorderWidth = 1.5f;
  layer.BorderColor = UIColor.Green.CGColor;

  View.Layer.AddSublayer (layer);
}
```

若要为该层添加隐式动画，只需在 `CATransaction`中包装属性更改即可。 这样，就可以使用视图动画（如 `BorderWidth` 和 `BorderColor`）来对不动画处理的属性进行动画处理，如下所示：

```csharp
public override void ViewDidAppear (bool animated)
{
  base.ViewDidAppear (animated);

  CATransaction.Begin ();
  CATransaction.AnimationDuration = 10;
  layer.Position = new CGPoint (50, 400);
  layer.BorderWidth = 5.0f;
  layer.BorderColor = UIColor.Red.CGColor;
  CATransaction.Commit ();
}
```

此代码还对层的 `Position`进行了动画处理，该层的定位点位置是从 superlayer 坐标的左上方测量的。 层的定位点是该层坐标系统内的规范化点。

下图显示了位置和定位点：

 ![](core-animation-images/10-postion-anchorpt.png "This figure shows the position and anchor point")

运行该示例时，`Position`、`BorderWidth` 和 `BorderColor` 进行动画处理，如以下屏幕截图所示：

 ![](core-animation-images/11-implicit-animation.png "When the example is run, the Position, BorderWidth and BorderColor animate as shown")

### <a name="explicit-animations"></a>显式动画

除了隐式动画以外，核心动画还包含各种从 `CAAnimation` 继承的类，这些类可让您封装动画，然后将这些动画显式添加到层。 它们允许对动画进行更精细的控制，如修改动画的起始值、组合动画并指定关键帧以允许非线性路径。

下面的代码演示了一个使用 `CAKeyframeAnimation` 的显式动画的示例（在 "隐式动画" 部分中）：

```csharp
public override void ViewDidAppear (bool animated)
{
  base.ViewDidAppear (animated);
  
  // get the initial value to start the animation from
  CGPoint fromPt = layer.Position;
  
  /* set the position to coincide with the final animation value
  to prevent it from snapping back to the starting position
  after the animation completes*/
  layer.Position = new CGPoint (200, 300);
  
  // create a path for the animation to follow
  CGPath path = new CGPath ();
  path.AddLines (new CGPoint[] { fromPt, new CGPoint (50, 300), new CGPoint (200, 50), new CGPoint (200, 300) });
  
  // create a keyframe animation for the position using the path
  CAKeyFrameAnimation animPosition = (CAKeyFrameAnimation)CAKeyFrameAnimation.FromKeyPath ("position");
  animPosition.Path = path;
  animPosition.Duration = 2;
  
  // add the animation to the layer.
  /* the "position" key is used to overwrite the implicit animation created
  when the layer positino is set above*/
  layer.AddAnimation (animPosition, "position");
}
```

此代码通过创建用于定义关键帧动画的路径来更改层的 `Position`。 请注意，该层的 `Position` 设置为动画 `Position` 的最终值。 如果没有这样做，该层会突然返回到动画前面的 `Position`，因为动画仅更改表示值而不更改实际模型值。 通过将模型值设置为动画中的最终值，该层会停留在动画的末尾。

下面的屏幕截图显示了一个层，其中包含通过指定路径动画显示的图像：

 ![](core-animation-images/12-explicit-animation.png "This screenshot shows the layer containing the image animating through the specified path")

## <a name="summary"></a>总结

本文介绍了通过*核心动画*框架提供的动画功能。 我们检查了核心动画，同时显示了它在 UIKit 中的动画效果，以及如何将其直接用于较低级别的动画控件。

## <a name="related-links"></a>相关链接

- [核心动画示例](https://docs.microsoft.com/samples/xamarin/ios-samples/graphicsandanimation)
- [核心图形](~/ios/platform/graphics-animation-ios/core-graphics.md)
- [图形和动画演练](~/ios/platform/graphics-animation-ios/graphics-animation-walkthrough.md)
- [核心动画](https://github.com/xamarin/recipes/tree/master/Recipes/ios/animation/coreanimation)
