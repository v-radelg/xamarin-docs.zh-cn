---
title: IOS 设计器中使用自定义控件
description: 本文档介绍如何创建自定义控件和使用 Xamarin 设计器使用适用于 iOS。 它显示了如何使控件可在 iOS 设计器的工具箱中，实现该控件，以便能够正确呈现和设计时间和的详细信息。
ms.prod: xamarin
ms.assetid: 9032B32E-97BD-4DA6-9955-811B84682578
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/22/2017
ms.openlocfilehash: 98504c9d5f210d55a2be4c85c52d4bc1418fc223
ms.sourcegitcommit: 650458de1d362cd7de174cacef7838f0e74426f3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/15/2019
ms.locfileid: "58070821"
---
# <a name="using-custom-controls-with-the-ios-designer"></a>IOS 设计器中使用自定义控件

## <a name="requirements"></a>要求

用于 iOS 的 Xamarin 设计器是 Visual Studio for Mac 和 Visual Studio 2017 和更高版本在 Windows 中可用。

本指南假定你熟悉中介绍的内容[入门指导](~/ios/get-started/index.md)。

## <a name="walkthrough"></a>演练

> [!IMPORTANT]
> 从开始 Xamarin.Studio 5.5 版中，在其中创建自定义控件的方法是较早版本略有不同。 若要创建自定义控件，或者`IComponent`接口时，需要 （以及相关的实现方法中） 或可以使用批注类`[DesignTimeVisible(true)]`。 在下面的演练示例正在使用后一种方法。


1. 创建新的解决方案从**iOS > 应用程序 > 单视图应用程序 > C#** 模板，其命名为`ScratchTicket`，然后继续完成新建项目向导：

    [![](ios-designable-controls-walkthrough-images/01new.png "创建新的解决方案")](ios-designable-controls-walkthrough-images/01new.png#lightbox)

1. 创建新的空类文件命名为`ScratchTicketView`:

    [![](ios-designable-controls-walkthrough-images/02new.png "创建一个新的 ScratchTicketView 类")](ios-designable-controls-walkthrough-images/02new.png#lightbox)

1. 添加以下代码为`ScratchTicketView`类：

    ```csharp
    using System;
    using System.ComponentModel;
    using CoreGraphics;
    using Foundation;
    using UIKit;
    
    namespace ScratchTicket
    {
        [Register("ScratchTicketView"), DesignTimeVisible(true)]
        public class ScratchTicketView : UIView
        {
            CGPath path;
            CGPoint initialPoint;
            CGPoint latestPoint;
            bool startNewPath = false;
            UIImage image;
    
            [Export("Image"), Browsable(true)]
            public UIImage Image
            {
                get { return image; }
                set
                {
                    image = value;
                    SetNeedsDisplay();
                }
            }
    
            public ScratchTicketView(IntPtr p)
                : base(p)
            {
                Initialize();
            }
    
            public ScratchTicketView()
            {
                Initialize();
            }
    
            void Initialize()
            {
                initialPoint = CGPoint.Empty;
                latestPoint = CGPoint.Empty;
                BackgroundColor = UIColor.Clear;
                Opaque = false;
                path = new CGPath();
                SetNeedsDisplay();
            }
    
            public override void TouchesBegan(NSSet touches, UIEvent evt)
            {
                base.TouchesBegan(touches, evt);
    
                var touch = touches.AnyObject as UITouch;
    
                if (touch != null)
                {
                    initialPoint = touch.LocationInView(this);
                }
            }
    
            public override void TouchesMoved(NSSet touches, UIEvent evt)
            {
                base.TouchesMoved(touches, evt);
    
                var touch = touches.AnyObject as UITouch;
    
                if (touch != null)
                {
                    latestPoint = touch.LocationInView(this);
                    SetNeedsDisplay();
                }
            }
    
            public override void TouchesEnded(NSSet touches, UIEvent evt)
            {
                base.TouchesEnded(touches, evt);
                startNewPath = true;
            }
    
            public override void Draw(CGRect rect)
            {
                base.Draw(rect);
    
                using (var g = UIGraphics.GetCurrentContext())
                {
                    if (image != null)
                        g.SetFillColor((UIColor.FromPatternImage(image).CGColor));
                    else
                        g.SetFillColor(UIColor.LightGray.CGColor);
                    g.FillRect(rect);
    
                    if (!initialPoint.IsEmpty)
                    {
                        g.SetLineWidth(20);
                        g.SetBlendMode(CGBlendMode.Clear);
                        UIColor.Clear.SetColor();
    
                        if (path.IsEmpty || startNewPath)
                        {
                            path.AddLines(new CGPoint[] { initialPoint, latestPoint });
                            startNewPath = false;
                        }
                        else
                        {
                            path.AddLineToPoint(latestPoint);
                        }
    
                        g.SetLineCap(CGLineCap.Round);
                        g.AddPath(path);        
                        g.DrawPath(CGPathDrawingMode.Stroke);
                    }
                }
            }
        }
    }
    ```


1. 添加`FillTexture.png`，`FillTexture2.png`并`Monkey.png`文件 (可用[从 GitHub](https://github.com/xamarin/ios-samples/blob/master/ScratchTicket/Resources/images.zip?raw=true)) 到**资源**文件夹。
    
1. 双击`Main.storyboard`文件以在设计器中打开它：

    [![](ios-designable-controls-walkthrough-images/03new.png "IOS 设计器")](ios-designable-controls-walkthrough-images/03new.png#lightbox)


1. 拖/放**图像视图**从**工具箱**拖动到情节提要中的视图。

    [![](ios-designable-controls-walkthrough-images/04new.png "图像视图添加到布局")](ios-designable-controls-walkthrough-images/04new.png#lightbox)


1. 选择**图像视图**并更改其**映像**属性设置为`Monkey.png`。

    [![](ios-designable-controls-walkthrough-images/05new.png "将图像视图图像属性设置为 Monkey.png")](ios-designable-controls-walkthrough-images/05new.png#lightbox)

    
1. 因为我们正在使用大小类，我们需要以限制此图像视图。 单击两次以使其处于约束模式的图像。 让我们通过单击 center 固定句柄将其约束到中心并将其水平和垂直对齐：

    [![](ios-designable-controls-walkthrough-images/06new.png "使图像内居中")](ios-designable-controls-walkthrough-images/06new.png#lightbox)

1. 要约束的高度和宽度大小固定句柄 （骨骼调整的句柄） 上单击，并分别选择宽度和高度：

    [![](ios-designable-controls-walkthrough-images/07new.png "将约束添加")](ios-designable-controls-walkthrough-images/07new.png#lightbox)


1. 更新基于约束通过单击更新按钮在工具栏中的帧：

    [![](ios-designable-controls-walkthrough-images/08new.png "约束工具栏")](ios-designable-controls-walkthrough-images/08new.png#lightbox)


1. 接下来，生成项目，以便**Scratch 票证视图**将显示在下面**自定义组件**工具箱中：

    [![](ios-designable-controls-walkthrough-images/09new.png "自定义组件工具箱")](ios-designable-controls-walkthrough-images/09new.png#lightbox)


1. 拖放到**Scratch 票证视图**，以便它将出现在 monkey 映像。 因此 Scratch 票证视图，如下所示完全涵盖 monkey 调整拖动句柄：

    [![](ios-designable-controls-walkthrough-images/10new.png "对该图像视图暂存票证视图")](ios-designable-controls-walkthrough-images/10new.png#lightbox)

1. 通过绘制边框，可选择这两个视图限制为图像视图 Scratch 票证视图。 选择选项以将其约束到宽度、 高度、 中心和中间和更新帧根据约束，如下所示：

    [![](ios-designable-controls-walkthrough-images/11new.png "居中和添加约束")](ios-designable-controls-walkthrough-images/11new.png#lightbox)


1. 运行应用程序和"scratch 关闭"以显示 monkey 的映像。

    [![](ios-designable-controls-walkthrough-images/10-app.png "运行示例应用")](ios-designable-controls-walkthrough-images/10-app.png#lightbox)

## <a name="adding-design-time-properties"></a>添加设计时属性

在设计器还包括自定义控件的属性类型数值、 枚举、 字符串、 布尔值、 CGSize、 UIColor 和 UIImage 的设计时支持。 若要演示，让我们将添加到属性`ScratchTicketView`设置为"是简要介绍了关闭。"的图像

将以下代码添加到`ScratchTicketView`类的属性：

```csharp
[Export("Image"), Browsable(true)]
public UIImage Image 
{
    get { return image; }
    set { 
            image = value;
            SetNeedsDisplay ();
        }
}
```

我们可能还想要添加到 null 检查`Draw`方法，如下所示：

```csharp
public override void Draw(CGRect rect)
{
    base.Draw(rect);

    using (var g = UIGraphics.GetCurrentContext())
    {
        if (image != null)
            g.SetFillColor ((UIColor.FromPatternImage (image).CGColor));
        else
            g.SetFillColor (UIColor.LightGray.CGColor);
            
        g.FillRect(rect);

        if (!initialPoint.IsEmpty)
        {
             g.SetLineWidth(20);
             g.SetBlendMode(CGBlendMode.Clear);
             UIColor.Clear.SetColor();

             if (path.IsEmpty || startNewPath)
             {
                 path.AddLines(new CGPoint[] { initialPoint, latestPoint });
                 startNewPath = false;
             }
             else
             {
                 path.AddLineToPoint(latestPoint);
             }

             g.SetLineCap(CGLineCap.Round);
             g.AddPath(path);       
             g.DrawPath(CGPathDrawingMode.Stroke);
        }
    }
}
```

其中包括`ExportAttribute`和一个`BrowsableAttribute`参数设置为`true`显示在设计器的属性会导致**属性**面板。 将属性更改为另一个映像添加项目，如`FillTexture2.png`，导致在设计时控件更新，如下所示：

 [![](ios-designable-controls-walkthrough-images/11-customproperty.png "编辑设计时属性")](ios-designable-controls-walkthrough-images/10-app.png#lightbox)

## <a name="summary"></a>总结

在本文中我们介绍如何创建自定义控件，以及在使用 iOS 设计器的 iOS 应用程序中使用。 我们已了解如何创建和生成控件以使其在设计器中的应用程序可以**工具箱**。 此外，还介绍了如何实现控件，以便它将在设计时和运行时，正确呈现，以及如何公开自定义控件设计器中的属性。



## <a name="related-links"></a>相关链接

- [ScratchTicket （示例）](https://developer.xamarin.com/samples/monotouch/ScratchTicket/)
- [所需的图像 （示例）](https://github.com/xamarin/ios-samples/blob/master/ScratchTicket/Resources/images.zip?raw=true)
