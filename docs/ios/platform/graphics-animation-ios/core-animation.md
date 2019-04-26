---
title: 在 Xamarin.iOS 中的核心动画
description: 本文将讨论核心动画框架，其中显示它如何实现高性能，以及如何在 UIKit 中的流畅动画要直接用于较低级别动画控件。
ms.prod: xamarin
ms.assetid: D4744147-FACB-415B-8155-3A6B3C35E527
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: a40d0911b7dabc900a4c6e50c692e4f091f22be9
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61206208"
---
# <a name="core-animation-in-xamarinios"></a>在 Xamarin.iOS 中的核心动画

_本文将讨论核心动画框架，其中显示它如何实现高性能，以及如何在 UIKit 中的流畅动画要直接用于较低级别动画控件。_

iOS 包括[*核心动画*](https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/CoreAnimation_guide/Introduction/Introduction.html)提供动画支持你的应用程序中的视图。
所有在 iOS 中的表滚动和不同视图之间轻扫等超流畅的动画是很适合他们做，因为它们在内部依赖于核心动画。

核心动画和核心图形框架可协同工作来创建精美的 2D 图形进行动画处理。 事实上核心动画可以甚至转换在 3D 空间中的 2D 图形创建令人惊叹、 迷人的体验。 但是，若要创建真正的 3D 图形，需要使用的内容，例如 OpenGL ES 游戏从而对诸如 MonoGame 的 API; 二是虽然 3D 不在本文的范围。

<a name="Using_Core_Animation" />

## <a name="core-animation"></a>核心动画

iOS 使用核心动画框架来创建动画效果，如视图之间转换，滑动菜单和滚动效果仅举几例。 有两种方法可以使用动画：

- [通过 UIKit](#Using_UIKit_Animation)，其中包括基于视图的动画，以及控制器之间的动画的过渡。
- [通过核心动画](#Using_Core_Animation)，哪些层直接，从而支持更精细地控制。

<a name="Using_UIKit_Animation" />

## <a name="using-uikit-animation"></a>使用 UIKit 动画

UIKit 提供了多种功能，可轻松地将动画添加到应用程序。 虽然它在内部使用的核心动画，但它避开它以便仅使用视图和控制器。

本部分讨论 UIKit 动画功能，包括：

-  控制器之间的转换
-  视图之间的转换
-  视图属性动画


### <a name="view-controller-transitions"></a>视图控制器转换

 `UIViewController` 通过视图控制器之间转换提供内置支持`PresentViewController`方法。 当使用`PresentViewController`，过渡到第二个控制器可以根据需要进行动画处理。

例如，考虑具有两个控制器，其中涉及的第一个控制器中的按钮调用的应用程序`PresentViewController`以显示第二个控制器。 若要控制哪些转换动画用于显示第二个控制器，只需设置其[ `ModalTransitionStyle` ](xref:UIKit.UIModalTransitionStyle)属性，如下所示：

```csharp
SecondViewController vc2 = new SecondViewController {
    ModalTransitionStyle = UIModalTransitionStyle.PartialCurl
};
```

在这种情况下`PartialCurl`使用动画，尽管其他几个可供选择，包括：

-  `CoverVertical` – 幻灯片向上从屏幕的底部
-  `CrossDissolve` – 旧视图淡出和淡入，新视图
-  `FlipHorizontal` 的从右到左水平翻转。 开除上转换翻转从左到右。


若要对转换进行动画处理，将传递`true`的第二个参数作为`PresentViewController`:

```csharp
PresentViewController (vc2, true, null);
```

以下屏幕截图显示了转换如下所示`PartialCurl`用例：

 ![](core-animation-images/06-view-transitions.png "此屏幕截图显示 PartialCurl 转换")

### <a name="view-transitions"></a>视图转换

控制器之间的转换，除了 UIKit 还支持视图要交换的另一个视图之间进行动画处理的转换。

例如，假设您有包含控制器`UIImageView`，在图像上的点击其中应显示第二个`UIImageView`。 要进行动画处理图像转换为第二个图像视图的视图的超级视图非常简单，调用`UIView.Transition`，并向其传递`toView`和`fromView`，如下所示：

```csharp
UIView.Transition (
    fromView: view1,
    toView: view2,
    duration: 2,
    options: UIViewAnimationOptions.TransitionFlipFromTop |
        UIViewAnimationOptions.CurveEaseInOut,
    completion: () => { Console.WriteLine ("transition complete"); });
```

`UIView.Transition` 此外采用`duration`参数，用于控制动画运行的时长，以及[ `options` ](xref:UIKit.UIViewAnimationOptions)指定动画等使用与缓动函数。 此外，可以指定动画完成时要调用的完成处理程序。

以下屏幕截图显示图像之间的动画的转换视图时`TransitionFlipFromTop`使用：

 ![](core-animation-images/07-animated-transition.png "此屏幕截图显示的图像视图使用 TransitionFlipFromTop 时之间的动画的转换")

### <a name="view-property-animations"></a>视图属性动画

UIKit 支持上对各种属性进行动画处理`UIView`类免费，其中包括：

-  Frame
-  边界
-  居中
-  Alpha
-  Transform
-  颜色


通过指定中的属性更改隐式发生了这些动画`NSAction`委托传递给静态`UIView.Animate`方法。 例如，下面的代码之间进行动画处理的中心点`UIImageView`:

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

这会导致图像进行动画处理来回在屏幕顶部，如下所示：

 ![](core-animation-images/08-animate-center.png "映像进行动画处理来回屏幕顶部作为输出")

如同`Transition`方法，`Animate`允许设置，以及缓动函数的持续时间。 此示例还使用`UIViewAnimationOptions.Autoreverse`选项，这会导致动画进行动画处理的值返回到的第一个。 但是，此代码还设置`Center`回其初始值中完成处理程序。 动画随着时间的推移插入属性值，而该属性的实际模型值始终是已设置的最终值。 在此示例中，值为超级视图的右侧附近的点。 如果没有设置`Center`到初始点，这是由于完成动画`Autoreverse`正在设置，图像会弹回到原来的右侧动画完成，如下所示：

 ![](core-animation-images/09-animation-complete.png "而不设置为初始点的中心，图像会弹回到原来的右侧在动画完成后")

## <a name="using-core-animation"></a>使用核心动画

 `UIView` 动画允许大量功能，应尽可能由于简易的实施。 前面曾提到，UIView 动画使用核心动画框架。 但是，不能与完成的一些事项`UIView`动画，例如对不能进行动画处理的视图的其他属性进行动画处理，或沿的非线性路径在插值。 在这种情况下需要更精细的控制，可以直接使用核心动画。

### <a name="layers"></a>层

动画时使用的核心动画，会通过自动发生*层*，其为类型`CALayer`。 因为层的层次结构，更像没有视图层次结构，一个层是从概念上讲类似于一个视图。 实际上，层后视图与视图添加对用户交互的支持。 您可以访问通过该视图的任何视图的层`Layer`属性。 事实上，在使用的上下文`Draw`方法的`UIView`实际创建从层。 在内部，支持的层`UIView`已设置为视图本身，这是什么调用其委托`Draw`。 因此，当绘制到`UIView`，您实际绘制到它的层。

层动画可以是隐式或显式。 隐式动画是声明性。 您只需声明层属性应更改和动画完全可以正常运行。 另一方面，显式动画会创建通过添加到某个层的动画类。 显式动画允许添加控制如何创建动画。 以下部分深入探讨更深入的隐式和显式动画。

### <a name="implicit-animations"></a>隐式动画

通过隐式动画层的属性进行动画处理的一种方法。 `UIView` 动画创建隐式动画。 但是，可以创建直接针对一个层还隐式动画。

例如，下面的代码设置层的`Contents`通过映像、 设置边框宽度和颜色，并为该视图的图层的一个子图层添加层：

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

若要添加的层的隐式动画，只需换行中的属性更改`CATransaction`。 这允许对不会如与一个视图的动画，可进行动画处理的属性进行动画处理`BorderWidth`和`BorderColor`，如下所示：

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

此代码还进行动画处理的层`Position`，即从左上角的 superlayer 的坐标度量的图层的定位点的位置。 定位点的一个层是层的坐标系统内的规范化的点。

下图显示的位置和锚点：

 ![](core-animation-images/10-postion-anchorpt.png "此图显示的位置和定位点")

运行示例时， `Position`，`BorderWidth`和`BorderColor`进行动画处理，如以下屏幕截图中所示：

 ![](core-animation-images/11-implicit-animation.png "运行示例时，位置、 边框宽度和边框颜色进行动画处理所示")

### <a name="explicit-animations"></a>显式动画

除了隐式动画，核心动画包括继承的类的各种`CAAnimation`，使您能够封装然后显式添加到某个层的动画。 这些行为允许更精细地控制动画，例如修改动画的起始值、 对动画进行分组和指定关键帧为允许的非线性路径。

下面的代码演示的显式动画使用示例`CAKeyframeAnimation`层之前 （在隐式动画节中） 所示：

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

此代码更改`Position`的层通过创建然后用于定义关键帧动画的路径。 请注意，图层的`Position`设置为的最终值`Position`从动画。 如果没有，层会突然返回到其`Position`之前动画因为动画只会更改演示文稿值而不是实际模型值。 通过将模型值设置为的最终值，从动画，层保持不变动画结束时。

以下屏幕截图显示包含图像进行动画处理通过指定的路径的图层：

 ![](core-animation-images/12-explicit-animation.png "此屏幕截图显示了包含图像进行动画处理通过指定的路径的图层")
 
## <a name="summary"></a>总结

在本文中我们介绍了通过所提供的动画功能*核心动画*框架。 核心动画，并展示如何为其提供支持 UIKit 中的动画和如何可以使用它直接为较低级别动画控件，我们探讨。

## <a name="related-links"></a>相关链接

- [核心动画示例](https://developer.xamarin.com/samples/monotouch/GraphicsAndAnimation/)
- [核心图形](~/ios/platform/graphics-animation-ios/core-graphics.md)
- [图形和动画演练](~/ios/platform/graphics-animation-ios/graphics-animation-walkthrough.md)
- [核心动画](https://github.com/xamarin/recipes/tree/master/Recipes/ios/animation/coreanimation)
