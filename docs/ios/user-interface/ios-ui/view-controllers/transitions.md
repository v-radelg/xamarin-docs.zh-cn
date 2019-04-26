---
title: 在 Xamarin.iOS 中的视图控制器转换
description: 本文档介绍如何自定义动画在 Xamarin.iOS 应用程序中的视图控制器之间的过渡。
ms.prod: xamarin
ms.assetid: CB3AC8E2-8A47-4839-AFA5-AE33047BB26C
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 06/14/2017
ms.openlocfilehash: 6711d3af06619aa54a2d735cb83862ed64abacec
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61199301"
---
# <a name="view-controller-transitions-in-xamarinios"></a>在 Xamarin.iOS 中的视图控制器转换

UIKit 添加自定义动画呈现视图控制器时，会发生的转换的支持。 此支持随内置的控制器，以及直接继承任何自定义控制器`UIViewController`。 此外，`UICollectionViewController`利用控制器转换自定义项，利用集合视图布局中的动画的转换。

## <a name="custom-transitions"></a>自定义转换

在 iOS 7 中的视图控制器之间经过动画处理的转换是完全可自定义。 `UIViewController` 现在包括`TransitioningDelegate`转换发生时提供对系统的自定义动画类的属性。

若要使用一个自定义转换`PresentViewController`:

1.  设置`ModalPresentationStyle`到`UIModalPresentationStyle.Custom`呈现在控制器上。
2.  实现`UIViewControllerTransitioningDelegate`创建动画器类，即实例`UIViewControllerAnimatedTransitioning`。
3.  设置`TransitioningDelegate`指向的实例的属性`UIViewControllerTransitioningDelegate`，还在控制器中提供。
4.  显示视图控制器。


例如，下面的代码提供类型的视图控制器`ControllerTwo`-`UIViewController`子类：

```csharp
showTwo.TouchUpInside += (object sender, EventArgs e) => {

    controllerTwo = new ControllerTwo ();

    this.PresentViewController (controllerTwo, true, null);
};
```

运行该应用并点击的按钮将导致第二个控制器的视图中进行动画处理底部，默认动画，如下所示：

 ![](transitions-images/no-custom-transition.png "运行应用并点击的按钮将导致第二个控制器视图中进行动画处理从底部的默认动画")

但是，将设置`ModalPresentationStyle`和`TransitioningDelegate`导致转换自定义动画：

```csharp
showTwo.TouchUpInside += (object sender, EventArgs e) => {

    controllerTwo = new ControllerTwo () {
        ModalPresentationStyle = UIModalPresentationStyle.Custom
        };

    transitioningDelegate = new TransitioningDelegate ();
    controllerTwo.TransitioningDelegate = transitioningDelegate;

    this.PresentViewController (controllerTwo, true, null);
};
```

`TransitioningDelegate`负责创建的实例`UIViewControllerAnimatedTransitioning`子类-调用`CustomAnimator`在下面的示例：

```csharp
public class TransitioningDelegate : UIViewControllerTransitioningDelegate
{
    CustomTransitionAnimator animator;

    public override IUIViewControllerAnimatedTransitioning GetAnimationControllerForPresentedController (UIViewController presented, UIViewController presenting, UIViewController source)
    {
        animator = new CustomTransitionAnimator ();
        return animator;
    }
}
```

进行转换时，系统创建的实例`IUIViewControllerContextTransitioning`，它传递到动画的方法。 `IUIViewControllerContextTransitioning` 包含`ContainerView`动画出现的位置，以及启动转换的视图控制器和视图控制器转换到。

`UIViewControllerAnimatedTransitioning`类处理实际的动画。 必须实现两种方法：

1.  `TransitionDuration` – 在数秒内返回动画的持续时间。
1.  `AnimateTransition` – 执行实际的动画。


例如，以下类实现`UIViewControllerAnimatedTransitioning`进行动画处理的帧的控制器的视图：

```csharp
public class CustomTransitionAnimator : UIViewControllerAnimatedTransitioning
{
    public CustomTransitionAnimator ()
    {
    }

    public override double TransitionDuration (IUIViewControllerContextTransitioning transitionContext)
    {
        return 1.0;
    }

    public override void AnimateTransition (IUIViewControllerContextTransitioning transitionContext)
    {
        var inView = transitionContext.ContainerView;
        var toVC = transitionContext.GetViewControllerForKey (UITransitionContext.ToViewControllerKey);
        var toView = toVC.View;

        inView.AddSubview (toView);

        var frame = toView.Frame;
        toView.Frame = CGRect.Empty;

        UIView.Animate (TransitionDuration (transitionContext), () => {
            toView.Frame = new CGRect (20, 20, frame.Width - 40, frame.Height - 40);
        }, () => {
            transitionContext.CompleteTransition (true);
        });
    }
}
```

现在，当点击按钮，动画中实现`UIViewControllerAnimatedTransitioning`使用类：

 ![](transitions-images/custom-transition.png "缩放有效运行的一个示例")

## <a name="collection-view-transitions"></a>集合视图转换

集合视图具有用于创建动画的过渡的内置支持：

-  **导航控制器**-经过动画处理的两个转换`UICollectionViewController`实例可以根据需要进行自动处理时`UINavigationController`对其进行管理。
-  **切换布局**– 一个新`UICollectionViewTransitionLayout`类允许交互式布局之间转换。


### <a name="navigation-controller-transitions"></a>导航控制器转换

在导航控制器中使用时`UICollectionViewController`包括对控制器之间的动画过渡的支持。 此项支持是内置的且需要仅几个简单步骤，以实现：

1.  设置`UseLayoutToLayoutNavigationTransitions`到`false`上`UICollectionViewController`。
1.  添加的实例`UICollectionViewController`到导航控制器堆栈根。
1.  创建另一个`UICollectionViewController`并设置其`UseLayoutToLayoutNavigtionTransitions`属性设置为`true`。
1.  推送第二个`UICollectionViewController`到导航控制器堆栈上。


下面的代码添加`UICollectionViewController`子类名为`ImagesCollectionViewController`根目录的导航控制器的堆栈与`UseLayoutToLayoutNavigationTransitions`属性设置为`false`:

```csharp
UIWindow window;
ImagesCollectionViewController viewController;
UICollectionViewFlowLayout layout;
UINavigationController navController;

public override bool FinishedLaunching (UIApplication app, NSDictionary options)
{
    window = new UIWindow (UIScreen.MainScreen.Bounds);

    // create and initialize a UICollectionViewFlowLayout
    layout = new UICollectionViewFlowLayout (){
        SectionInset = new UIEdgeInsets (10,5,10,5),
        MinimumInteritemSpacing = 5,
        MinimumLineSpacing = 5,
        ItemSize = new CGSize (100, 100)
    };

    viewController = new ImagesCollectionViewController (layout) {
            UseLayoutToLayoutNavigationTransitions = false
        };

    navController = new UINavigationController (viewController);

    window.RootViewController = navController;
    window.MakeKeyAndVisible ();
    
    return true;
}
```

选择某个项时，第二个实例`ImagesController`创建时，这次使用一个不同的布局的类。 此控制器`UseLayoutToLayoutNavigtionTransitions`设置为`true`，如下所示：

```csharp
CircleLayout circleLayout;
ImagesCollectionViewController controller2;

...

public override void ItemSelected (UICollectionView collectionView, NSIndexPath indexPath)
{
    // UseLayoutToLayoutNavigationTransitions when item is selected
    circleLayout = new CircleLayout (Monkeys.Instance.Count){
        ItemSize = new CGSize (100, 100)
    };
            
    controller2 = new ImagesCollectionViewController (circleLayout) {
        UseLayoutToLayoutNavigationTransitions = true
        };

    NavigationController.PushViewController (controller2, true);
}
```

`UseLayoutToLayoutNavigationTransitions`必须将控制器添加到导航堆栈之前设置属性。 设置此属性，正常水平的滑动转换将替换为两个控制器，布局之间的动画转换如下所示：

![](transitions-images/nav2.png "经过动画处理的两个控制器在布局之间转换")

### <a name="transition-layout"></a>转换布局

导航控制器中的布局转换支持，除了新的布局称为`UICollectionViewTransitionLayout`现已推出。 此布局类允许在布局转换过程中，交互式控制，从而`TransitionProgress`若要从代码中设置。 `UICollectionViewTransitionLayout` 不同于-和不能替代对-`SetCollectionViewLayout`从 iOS 6 导致动画的布局转换发生的方法。 用于控制动画转换的进度，该方法未提供内置支持。

 `UICollectionViewTransitionLayout` 例如，可以配置为控制响应用户交互，通过管理原始布局，以及转换为预期的布局中的布局之间的转换的手势识别程序。

实现中使用笔势识别器的交互式转换的步骤`UICollectionViewTransitionLayout`如下所示：

1.  创建笔势识别器。
1.  调用`StartInteractiveTransition`方法的`UICollectionView`，将其传递目标布局和完成处理程序。
1.  设置`TransitionProgress`的属性`UICollectionViewTransitionLayout`从返回实例`StartInteractiveTransition`方法。
1.  使布局失效。
1.  调用`FinishInteractiveTransition`方法`UICollectionView`完成转换或`CancelInteractiveTransition`方法来取消它。  `FinishInteractiveTransition` 使动画完成其转换为目标布局，而`CancelInteractiveTransition`导致动画返回到原始布局。
1.  处理在完成处理程序中的转换完成`StartInteractiveTransition`方法。
1.  将笔势识别器添加到集合视图。


下面的代码实现内捏合手势识别器的交互式布局转换：

```csharp
imagesController = new ImagesCollectionViewController (flowLayout);

nfloat sf = 0.4f;
UICollectionViewTransitionLayout trLayout = null;
UICollectionViewLayout nextLayout;

pinch = new UIPinchGestureRecognizer (g => {

    var progress = Math.Abs(1.0f -  g.Scale)/sf;

    if(trLayout == null){
        if(imagesController.CollectionView.CollectionViewLayout is CircleLayout)
            nextLayout = flowLayout;
        else
            nextLayout = circleLayout;

        trLayout = imagesController.CollectionView.StartInteractiveTransition (nextLayout, (completed, finished) => {   
            Console.WriteLine ("transition completed");
            trLayout = null;
        });
    }

    trLayout.TransitionProgress = (nfloat)progress;

    imagesController.CollectionView.CollectionViewLayout.InvalidateLayout ();

    if(g.State == UIGestureRecognizerState.Ended){
        if (trLayout.TransitionProgress > 0.5f)
            imagesController.CollectionView.FinishInteractiveTransition ();
        else
            imagesController.CollectionView.CancelInteractiveTransition ();
    }

});

imagesController.CollectionView.AddGestureRecognizer (pinch);

```

用户 pinches 集合视图中，如`TransitionProgress`相对于刻度 pinch 设置。 在此实现中，如果用户结束 pinch 之前转换为 50%完成，则取消转换。 否则，转换已完成。




## <a name="related-links"></a>相关链接

- [对 iOS 7 （示例） 简介](https://developer.xamarin.com/samples/monotouch/IntroToiOS7)
- [iOS 7 用户界面概述](~/ios/platform/introduction-to-ios7/ios7-ui.md)
- [后台处理](~/ios/app-fundamentals/backgrounding/index.md)
