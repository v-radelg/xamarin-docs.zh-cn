---
title: iOS 7 简介
description: 本文介绍如何在 iOS 7，包括视图控制器转换，UIView 动画，UIKit Dynamics 和文本工具包的增强功能中引入的主要新 Api。 它还介绍了一些对用户界面和新的增强多任务处理功能的更改。
ms.prod: xamarin
ms.assetid: 2C33018F-D64A-4BAA-A34E-082EF311D162
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/19/2017
ms.openlocfilehash: db2ce779962947e2121ff03280544a080e193e2e
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61037254"
---
# <a name="introduction-to-ios-7"></a>iOS 7 简介

_本文介绍如何在 iOS 7，包括视图控制器转换，UIView 动画，UIKit Dynamics 和文本工具包的增强功能中引入的主要新 Api。它还介绍了一些对用户界面和新的增强多任务处理功能的更改。_

iOS 7 是针对 iOS 的重大更新。 它引入了全新的用户界面设计的内容，而不是应用程序在 chrome 上将焦点放。 可视更改，以及 iOS 7 将添加大量新的 Api 来创建更丰富的交互和体验。 此文档调查新技术引入了 iOS 7，并作为执行进一步的研究的起点。

## <a name="uiview-animation-enhancements"></a>UIView 动画增强功能

iOS 7 增强 UIKit，允许应用程序执行以前需要直接在核心动画框架的删除操作中的动画支持。 例如， `UIView` spring 动画，以及关键帧动画，现在可以执行先前`CAKeyframeAnimation`应用于`CALayer`。

### <a name="spring-animations"></a>动画

 `UIView` 现在支持使用 spring 效果进行动画处理属性更改。 若要添加此，请调用`AnimateNotify`或`AnimateNotifyAsync`方法，传入值 spring 阻止比例和方向的初始 spring 速度，如下所述：

-  `springWithDampingRatio` – 介于 0 和 1，振荡其中较小的值会增加。
-  `initialSpringVelocity` – 每秒的总动画距离的百分比初始 spring 速度。


下面的代码生成 spring 效果图像视图的中心更改时：

```csharp
void AnimateWithSpring ()
{ 
    float springDampingRatio = 0.25f;
    float initialSpringVelocity = 1.0f;
    
    UIView.AnimateNotify (3.0, 0.0, springDampingRatio, initialSpringVelocity, 0, () => {
    
        imageView.Center = new CGPoint (imageView.Center.X, 400);   
            
    }, null);
}
```

此 spring 效果可使图像视图弹跳效果在它完成到新的中心位置，其动画，如下图所示：

 ![](images/spring-animation.png "此 spring 效果可使图像视图在它完成到新的中心位置动画弹跳效果")

### <a name="keyframe-animations"></a>关键帧动画

`UIView`类现在包括`AnimateWithKeyframes`上创建关键帧动画方法`UIView`。 此方法是与其他`UIView`动画方法，只不过额外`NSAction`传递作为参数，以包括关键帧。 内`NSAction`，通过调用添加关键帧`UIView.AddKeyframeWithRelativeStartTime`。

例如，下面的代码段将创建一个关键帧动画进行动画处理关于旋转视图的视图的中心以及：

```csharp
void AnimateViewWithKeyframes ()
{
    var initialTransform = imageView.Transform;
    var initialCeneter = imageView.Center;

    // can now use keyframes directly on UIView without needing to drop directly into Core Animation

    UIView.AnimateKeyframes (2.0, 0, UIViewKeyframeAnimationOptions.Autoreverse, () => {
        UIView.AddKeyframeWithRelativeStartTime (0.0, 0.5, () => { 
            imageView.Center = new CGPoint (200, 200);
        });

        UIView.AddKeyframeWithRelativeStartTime (0.5, 0.5, () => { 
            imageView.Transform = CGAffineTransform.MakeRotation ((float)Math.PI / 2);
        });
    }, (finished) => {
        imageView.Center = initialCeneter;
        imageView.Transform = initialTransform;

        AnimateWithSpring ();
    });
}
```

前两个参数`AddKeyframeWithRelativeStartTime`方法指定的开始时间和持续时间的关键帧，分别为整体的动画长度的一部分。 通过下一步的第二个旋转 90 度跟导致图像视图进行动画处理到其新中心通过第一个第二个，上面的示例。 由于动画指定`UIViewKeyframeAnimationOptions.Autoreverse`逆序也作为一个选项，这两个关键帧动画。 最后，最终的值设置为在完成处理程序中的初始状态。

下面的屏幕截图演示了组合的动画关键帧：

 ![](images/keyframes.png "此屏幕截图演示了通过关键帧的组合的动画")

## <a name="uikit-dynamics"></a>UIKit Dynamics

UIKit Dynamics 是一组新的 Api 在 UIKit 中，它允许应用程序创建动画基于物理引擎的交互。 UIKit Dynamics 封装在 2D 物理引擎，为了实现这一点。

该 API 是声明性本质上。 声明的物理交互通过创建对象的调用的行为方式*行为*-express 物理概念，例如重力、 冲突、 弹簧，等等。然后将行为附加到另一个对象，调用*动态动画器*，其中封装一个视图。 动态动画器采用的声明的物理引擎将行为应用于需要*动态项*-项实现`IUIDynamicItem`，如`UIView`。

有几个不同的基元行为可用于触发复杂的交互，包括：

-  `UIAttachmentBehavior` – 附加两个动态的项，以便它们将移动到一起，或将动态项附加到连接点。
-  `UICollisionBehavior` – 允许动态项参与冲突。
-  `UIDynamicItemBehavior` -指定一组常规要应用于动态项，如灵活性、 密度和冲突的属性。
-  `UIGravityBehavior` -适用于动态项，从而导致要加速引力方向中的项的重力。
-  `UIPushBehavior` – 适用于动态项强制。
-  `UISnapBehavior` -允许使用 spring 效果将其置于管理单元将动态选项。


虽然有许多基元，但添加到使用 UIKit Dynamics 视图基于物理引擎的交互的一般过程在各种行为之间是一致的：

1.  创建动态动画器。
1.  创建行为。
1.  将行为添加到动态动画器。


### <a name="dynamics-example"></a>Dynamics 示例

让我们看看的示例，添加重力和碰撞边界`UIView`。

#### <a name="uigravitybehavior"></a>UIGravityBehavior

向图像视图添加重力遵循上面所述的 3 个步骤。

我们将使用`ViewDidLoad`对于此示例的方法。 首先，添加`UIImageView`实例，如下所示：

```csharp
image = UIImage.FromFile ("monkeys.jpg");

imageView = new UIImageView (new CGRect (new CGPoint (View.Center.X - image.Size.Width / 2, 0), image.Size)) {
                    Image =  image
                }

View.AddSubview (imageView);
```

这将创建以在屏幕上边缘中心的图像视图。 若要使用重力使映像"回退"，创建的实例`UIDynamicAnimator`:

```csharp
dynAnimator = new UIDynamicAnimator (this.View);
```

`UIDynamicAnimator`采用的引用的实例`UIView`或`UICollectionViewLayout`，其中包含每个附加的行为进行动画处理的项。

接下来，创建`UIGravityBehavior`实例。 您可以将实现的一个或多个对象传递`IUIDynamicItem`，例如`UIView`:

```csharp
var gravity = new UIGravityBehavior (dynItems);
```

行为传递的数组`IUIDynamicItem`，在这种情况下，其中包含单个`UIImageView`我们进行动画处理的实例。

最后，将行为添加到动态动画器：

```csharp
dynAnimator.AddBehavior (gravity);
```

这会导致进行动画处理与重力，为如下所示的向下拖动图像：

![](images/gravity2.png "启动映像位置") 
![](images/gravity3.png "结束映像位置")

由于没有任何限制在屏幕的边界，请将图像视图只需转出底部。 若要约束视图，以便与屏幕的边缘图像有冲突，我们可以添加`UICollisionBehavior`。 我们将介绍这在下一节中。

#### <a name="uicollisionbehavior"></a>UICollisionBehavior

我们将首先创建`UICollisionBehavior`并将其添加到动态动画器，就像我们对`UIGravityBehavior`。

修改代码以包括`UICollisionBehavior`:

```csharp
using (image = UIImage.FromFile ("monkeys.jpg")) {

    imageView = new UIImageView (new CGRect (new CGPoint (View.Center.X - image.Size.Width / 2, 0), image.Size)) {
        Image =  image
    };

    View.AddSubview (imageView);

    // 1. create the dynamic animator
    dynAnimator = new UIDynamicAnimator (this.View);

    // 2. create behavior(s)
    var gravity = new UIGravityBehavior (imageView);
    var collision = new UICollisionBehavior (imageView) {
        TranslatesReferenceBoundsIntoBoundary = true
    };

    // 3. add behaviors(s) to the dynamic animator
    dynAnimator.AddBehaviors (gravity, collision);
}
```

`UICollisionBehavior`具有一个名为属性`TranslatesReferenceBoundsIntoBoundry`。 此值设置为`true`导致要用作冲突边界的视图的边界的引用。

现在，当该图像进行动画处理向下与重力，它弹回略有屏幕的底部确定那里 rest 之前。

<!--, as shown below:

 ![](images/bounce.png "Now, when the image animates downward with gravity, it bounces slightly off the bottom of the screen before settling to rest there")-->

#### <a name="uidynamicitembehavior"></a>UIDynamicItemBehavior

我们可以进一步控制下降的图像视图与其他行为的行为。 例如，我们可以添加`UIDynamicItemBehavior`增加的良好灵活性，从而导致要弹跳的详细信息时它与屏幕的底部有冲突的图像视图。

添加`UIDynamicItemBehavior`遵循相同的步骤与其他行为一样。 首先创建行为：

```csharp
var dynBehavior = new UIDynamicItemBehavior (dynItems) {
    Elasticity = 0.7f
};
```

然后，将行为添加到动态动画器：

 `dynAnimator.AddBehavior (dynBehavior);`

通过此行为后，图像视图退回的详细信息时它与边界发生冲突。

## <a name="general-user-interface-changes"></a>常规用户界面更改

例如 UIKit Dynamics、 控制器转换和上面所述的增强的 UIView 动画的 UIKit 新 Api，除了 iOS 7 引入了各种 visual 的 ui 的更改和相关的 API 更改各种视图和控件。 有关详细信息请参阅[iOS 7 用户界面概述](~/ios/platform/introduction-to-ios7/ios7-ui.md)。

## <a name="text-kit"></a>文本工具包

文本工具包是一个新 API，提供了强大的文本布局和呈现功能。 它基于核心文本框架的低级别，但可以更轻松地使用比核心文本。

有关详细信息，请参阅我们[TextKit](~/ios/platform/textkit.md)

## <a name="multitasking"></a>多任务

iOS 7 更改何时以及如何执行后台工作。 任务完成 iOS 7 中的不会再使应用程序保持唤醒状态时在后台运行的任务和应用程序都被唤醒，以非连续的方式处理的背景。 iOS 7 还添加了三个新的 Api 更新应用程序，并且在后台中的新内容：

-  后台获取 – 允许应用程序来更新按固定间隔在后台中的内容。
-  远程通知-允许应用程序以接收推送通知时，更新内容。 通知可以是无提示，也可以在锁定屏幕上显示一个横幅。
-  后台传输服务 – 允许上传和下载的数据，例如大型文件，而无需固定的时间限制。


有关新的多任务处理功能的更多详细信息，请参阅 Xamarin 的 iOS 部分[后台处理指南](~/ios/app-fundamentals/backgrounding/index.md)。

## <a name="summary"></a>总结

本文介绍如何向 iOS 几个主要的新增功能。 首先，它演示如何将自定义转换添加到视图控制器。 然后，它演示如何使用在集合视图中，同时从内导航控制器，以及以交互方式集合视图之间的转换。 接下来，它引入了对 UIView 动画，显示应用程序如何使用 UIKit 以前需要直接对核心动画的编程的内容所做的多项增强功能。 最后，新的 UIKit Dynamics API，它为 UIKit 带来物理引擎，是与文本工具包框架现已提供的丰富的文本支持一起引入的。

## <a name="related-links"></a>相关链接

- [对 iOS 7 （示例） 简介](https://developer.xamarin.com/samples/monotouch/IntroToiOS7)
- [iOS 7 用户界面概述](~/ios/platform/introduction-to-ios7/ios7-ui.md)
- [后台处理](~/ios/app-fundamentals/backgrounding/index.md)
