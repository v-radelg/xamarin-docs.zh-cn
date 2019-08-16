---
title: 在 Xamarin 中查看控制器转换
description: 本文档介绍如何在 Xamarin iOS 应用程序中自定义视图控制器之间的动画转换。
ms.prod: xamarin
ms.assetid: CB3AC8E2-8A47-4839-AFA5-AE33047BB26C
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 06/14/2017
ms.openlocfilehash: 82a2e2b4abe5bc634c74c083b860d1f4f59a90b7
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/16/2019
ms.locfileid: "69528472"
---
# <a name="view-controller-transitions-in-xamarinios"></a>在 Xamarin 中查看控制器转换

UIKit 添加了对自定义呈现视图控制器时发生的动画转换的支持。 内置控制器以及直接从`UIViewController`继承的任何自定义控制器均提供了此支持。 此外, `UICollectionViewController`利用控制器转换自定义来利用集合视图布局中的动画转换。

## <a name="custom-transitions"></a>自定义转换

IOS 7 中视图控制器之间的动态过渡是完全可自定义的。 `UIViewController`现在包含一个`TransitioningDelegate`属性, 该属性在发生转换时向系统提供自定义的 animator 类。

若要将自定义转换`PresentViewController`用于:

1. 在要显示`UIModalPresentationStyle.Custom`的控制器上将设置为。`ModalPresentationStyle`
2. 实现`UIViewControllerTransitioningDelegate`以创建 animator 类, 该类是的`UIViewControllerAnimatedTransitioning`实例。
3. 将属性设置为的`UIViewControllerTransitioningDelegate`实例, 还会在要显示的控制器上设置。 `TransitioningDelegate`
4. 显示视图控制器。


例如, 下面的代码提供了一个类型为`ControllerTwo` `UIViewController`的类的视图控制器:

```csharp
showTwo.TouchUpInside += (object sender, EventArgs e) => {

    controllerTwo = new ControllerTwo ();

    this.PresentViewController (controllerTwo, true, null);
};
```

运行应用程序并点击按钮, 会导致第二个控制器视图的默认动画从底部动画显示, 如下所示:

 ![](transitions-images/no-custom-transition.png "运行应用并点击按钮将导致第二个控制器视图的默认动画从底部动画显示")

但是, 设置`ModalPresentationStyle`并`TransitioningDelegate`为转换生成自定义动画:

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

负责创建`UIViewControllerAnimatedTransitioning`子类的实例 (在以下示例中称为`CustomAnimator` ): `TransitioningDelegate`

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

发生转换时, 系统将创建一个实例`IUIViewControllerContextTransitioning`, 并将其传递到 animator 的方法。 `IUIViewControllerContextTransitioning`包含动画`ContainerView`发生的位置, 以及用于启动转换和要转换到的视图控制器的视图控制器。

`UIViewControllerAnimatedTransitioning`类处理实际动画。 必须实现两种方法:

1. `TransitionDuration`–返回动画的持续时间 (以秒为单位)。
1. `AnimateTransition`–执行实际动画。


例如, 下面的类实现`UIViewControllerAnimatedTransitioning`以对控制器视图的框架进行动画处理:

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

现在, 点击按钮时, 将使用在`UIViewControllerAnimatedTransitioning`类中实现的动画:

 ![](transitions-images/custom-transition.png "正在运行的放大效果的示例")

## <a name="collection-view-transitions"></a>集合视图转换

集合视图为创建动态转换提供内置支持:

- **导航控制器**–两个`UICollectionViewController`实例之间的动画转换可以选择在`UINavigationController`管理它们时自动处理。
- **转换布局**–新`UICollectionViewTransitionLayout`类允许在布局之间进行交互式转换。


### <a name="navigation-controller-transitions"></a>导航控制器转换

在导航控制器中使用时, `UICollectionViewController`包括对控制器间的动画转换的支持。 此支持是内置的, 只需几个简单的步骤即可实现:

1. 在`UseLayoutToLayoutNavigationTransitions`上将设置`false`为。 `UICollectionViewController`
1. 将的`UICollectionViewController`实例添加到导航控制器堆栈的根。
1. 创建另`UICollectionViewController`一个, 并将`UseLayoutToLayoutNavigtionTransitions`其属性`true`设置为。
1. 将第二`UICollectionViewController`个推送到导航控制器的堆栈上。


下面的代码将名`UICollectionViewController` `ImagesCollectionViewController`为的子类添加到导航控制器堆栈的根目录中, 并`UseLayoutToLayoutNavigationTransitions`将属性设置为`false`:

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

选择某一项时, 将创建的第二`ImagesController`个实例, 只使用其他布局类。 对于此控制器, `UseLayoutToLayoutNavigtionTransitions`将设置为`true`, 如下所示:

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

在`UseLayoutToLayoutNavigationTransitions`将控制器添加到导航堆栈之前, 必须设置该属性。 设置此属性后, 将使用两个控制器布局之间的动态过渡替换普通水平滑动转换, 如下所示:

![](transitions-images/nav2.png "两个控制器布局之间的动画转换")

### <a name="transition-layout"></a>转换布局

除了导航控制器中的布局转换支持外, 现在还提供了`UICollectionViewTransitionLayout`一个名为的新布局。 此布局类允许在布局转换过程中进行交互式控制, 方法是`TransitionProgress`允许从代码设置。 `UICollectionViewTransitionLayout`不同于-, 而不是从 iOS 6 替换`SetCollectionViewLayout`为-, 后者导致动画布局转换发生。 该方法不为控制动画转换的进度提供内置支持。

 `UICollectionViewTransitionLayout`例如, 通过管理原始布局以及要转换到的目标布局, 允许将笔势识别器配置为控制布局之间的转换以响应用户交互。

使用`UICollectionViewTransitionLayout`的笔势识别器中实现交互式转换的步骤如下所示:

1. 创建笔势识别器。
1. 调用的`StartInteractiveTransition` `UICollectionView`方法, 并向其传递目标布局和完成处理程序。
1. 设置从`UICollectionViewTransitionLayout` `TransitionProgress` 方法返回的`StartInteractiveTransition`实例的属性。
1. 使布局无效。
1. 调用的`FinishInteractiveTransition`方法`UICollectionView` 以`CancelInteractiveTransition`完成转换或取消方法。  `FinishInteractiveTransition`使动画完成其到目标布局的转换, 而`CancelInteractiveTransition`使动画返回到原始布局。
1. 在`StartInteractiveTransition`方法的完成处理程序中处理转换完成。
1. 将手势识别器添加到集合视图。


下面的代码实现了一个在缩小手势识别器内的交互式布局转换:

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

当用户 pinches 集合视图时, `TransitionProgress`相对于刻度的刻度设置。 在此实现中, 如果用户在转换完成 50% 之前结束了缩小, 则取消转换。 否则, 转换已完成。




## <a name="related-links"></a>相关链接

- [IOS 7 简介 (示例)](https://docs.microsoft.com/samples/xamarin/ios-samples/introtoios7)
- [iOS 7 用户界面概述](~/ios/platform/introduction-to-ios7/ios7-ui.md)
- [后台处理](~/ios/app-fundamentals/backgrounding/index.md)
