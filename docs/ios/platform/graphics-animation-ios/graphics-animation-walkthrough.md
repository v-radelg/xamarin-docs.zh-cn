---
title: 在 Xamarin.iOS 中使用核心图形和核心动画
description: 本文逐步演示如何创建使用核心图形和核心动画的应用程序。 它显示在响应用户触摸屏幕上绘制的方式，以及如何进行动画处理的图像沿路径移动。
ms.prod: xamarin
ms.assetid: 4B96D5CD-1BF5-4520-AAA6-2B857C83815C
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: 2690b60abe963cf7b02ca282b32091098a224ccf
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61091342"
---
# <a name="using-core-graphics-and-core-animation-in-xamarinios"></a>在 Xamarin.iOS 中使用核心图形和核心动画

在本演练中我们要绘制在响应中使用核心图形触摸屏输入的路径。 然后，我们将添加`CALayer`包含我们将在路径上的动画处理的图像。

下面的屏幕截图显示了完整的应用程序：

![](graphics-animation-walkthrough-images/00-final-app.png "已完成的应用程序")

我们开始下载之前*GraphicsDemo*本指南附带的示例。 可以下载[这里](https://developer.xamarin.com/samples/monotouch/GraphicsAndAnimation/)和位于**GraphicsWalkthrough**目录启动名为的项目**GraphicsDemo_starter**通过双击它，并打开`DemoView`类。

## <a name="drawing-a-path"></a>绘制路径


1. 在中`DemoView`添加`CGPath`变量到类并将其实例的构造函数中。 此外声明两个`CGPoint`变量，`initialPoint`和`latestPoint`，我们将使用捕获触摸点从中我们构建路径：
    
    ```csharp
    public class DemoView : UIView
    {
        CGPath path;
        CGPoint initialPoint;
        CGPoint latestPoint;
    
        public DemoView ()
        {
            BackgroundColor = UIColor.White;
    
            path = new CGPath ();
        }
    }
    ```

2. 添加以下 using 指令：

    ```csharp
    using CoreGraphics;
    using CoreAnimation;
    using Foundation;
    ```

3. 接下来，重写`TouchesBegan`和`TouchesMoved,`并添加要分别捕获初始接触点和每个后续的接触点的以下实现：

    ```csharp
    public override void TouchesBegan (NSSet touches, UIEvent evt){
    
        base.TouchesBegan (touches, evt);
    
        UITouch touch = touches.AnyObject as UITouch;
        
        if (touch != null) {
            initialPoint = touch.LocationInView (this);
        }
    }
    
    public override void TouchesMoved (NSSet touches, UIEvent evt){
    
        base.TouchesMoved (touches, evt);
    
        UITouch touch = touches.AnyObject as UITouch;
        
        if (touch != null) {
            latestPoint = touch.LocationInView (this);
            SetNeedsDisplay ();
        }
    }
    ```

    `SetNeedsDisplay` 将收尾工作了移动以便每次调用`Draw`对下一次运行的循环通过调用。

4. 我们会将行添加到中的路径`Draw`方法，并使用红色的虚线线与绘制。 [实现`Draw`](~/ios/platform/graphics-animation-ios/core-graphics.md)如下所示的代码：

    ```csharp
    public override void Draw (CGRect rect){
    
        base.Draw (rect);
    
        if (!initialPoint.IsEmpty) {
    
            //get graphics context
            using(CGContext g = UIGraphics.GetCurrentContext ()){
                    
                //set up drawing attributes
                g.SetLineWidth (2);
                UIColor.Red.SetStroke ();
    
                //add lines to the touch points
                if (path.IsEmpty) {
                    path.AddLines (new CGPoint[]{initialPoint, latestPoint});
                } else {
                    path.AddLineToPoint (latestPoint);
                }
            
                //use a dashed line
                g.SetLineDash (0, new nfloat[] { 5, 2 * (nfloat)Math.PI });
                                
                //add geometry to graphics context and draw it
                g.AddPath (path);       
                g.DrawPath (CGPathDrawingMode.Stroke);
            }
        }
    }
    ```

如果我们运行应用程序现在，我们可以触摸以绘制在屏幕上，如以下屏幕截图中所示：

![](graphics-animation-walkthrough-images/01-path.png "在屏幕上绘制")

## <a name="animating-along-a-path"></a>沿着路径针对进行动画处理

现在，我们已实现的代码，以允许用户绘制的路径，让我们来添加代码以动态显示在绘制路径上的层。

1. 首先，添加[ `CALayer` ](~/ios/platform/graphics-animation-ios/core-animation.md)变量到类并在构造函数中创建它：

    ```csharp
    public class DemoView : UIView
        {
            …
    
            CALayer layer;
    
            public DemoView (){
                …
    
                //create layer
                layer = new CALayer ();
                layer.Bounds = new CGRect (0, 0, 50, 50);
                layer.Position = new CGPoint (50, 50);
                layer.Contents = UIImage.FromFile ("monkey.png").CGImage;
                layer.ContentsGravity = CALayer.GravityResizeAspect;
                layer.BorderWidth = 1.5f;
                layer.CornerRadius = 5;
                layer.BorderColor = UIColor.Blue.CGColor;
                layer.BackgroundColor = UIColor.Purple.CGColor;
            }
    ```

2. 接下来，我们将为该视图的图层的一个子图层添加层时用户抬起手指从屏幕。 然后，我们将创建使用路径，一个关键帧动画进行动画处理的层`Position`。

    若要完成此我们需要重写`TouchesEnded`并添加以下代码：

    ```csharp
    public override void TouchesEnded (NSSet touches, UIEvent evt)
        {
            base.TouchesEnded (touches, evt);

            //add layer with image and animate along path

            if (layer.SuperLayer == null)
                Layer.AddSublayer (layer);

            // create a keyframe animation for the position using the path
            layer.Position = latestPoint;
            CAKeyFrameAnimation animPosition = (CAKeyFrameAnimation)CAKeyFrameAnimation.FromKeyPath ("position");
            animPosition.Path = path;
            animPosition.Duration = 3;
            layer.AddAnimation (animPosition, "position");
        }
    ```

3. 运行应用程序，现在和绘图后，添加具有图像的图层和传输沿绘制的路径：

![](graphics-animation-walkthrough-images/00-final-app.png "具有图像的图层添加和传输时沿绘制的路径")

## <a name="summary"></a>总结

在本文中，我们逐步完成绑定在一起的图形和动画概念的示例。 首先，我们介绍了如何使用核心图形绘制路径`UIView`以响应用户触摸屏输入。 然后，我们介绍了如何使用核心动画来使图像沿该路径移动。


## <a name="related-links"></a>相关链接

- [核心动画](~/ios/platform/graphics-animation-ios/core-animation.md)
- [核心图形](~/ios/platform/graphics-animation-ios/core-graphics.md)
- [核心动画方案](https://github.com/xamarin/recipes/tree/master/Recipes/ios/animation/coreanimation)
