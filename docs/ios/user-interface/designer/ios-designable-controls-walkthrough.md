---
title: 结合使用自定义控件和 iOS 设计器
description: 本文档介绍如何创建自定义控件并将其与 Xamarin Designer for iOS 一起使用。 它演示如何在 iOS 设计器的工具箱中使控件可用，实现控件，使其正确呈现和设计时间等。
ms.prod: xamarin
ms.assetid: 9032B32E-97BD-4DA6-9955-811B84682578
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/22/2017
ms.openlocfilehash: 651df247e3b5616a3baa38e85159c6a6d5247807
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70768807"
---
# <a name="using-custom-controls-with-the-ios-designer"></a>结合使用自定义控件和 iOS 设计器

## <a name="requirements"></a>要求

Xamarin Designer for iOS 在 Visual Studio for Mac 和 Visual Studio 2017 及更高版本的 Windows 中可用。

本指南假定你熟悉[入门指南](~/ios/get-started/index.md)中所述的内容。

## <a name="walkthrough"></a>演练

> [!IMPORTANT]
> 从 Xamarin 5.5 开始，创建自定义控件的方式与早期版本略有不同。 若要创建自定义控件， `IComponent`需要提供接口（使用关联的实现方法）或可以使用`[DesignTimeVisible(true)]`批注类。 下面的演练示例使用后一种方法。

1. 使用 iOS > 应用创建新的解决方案 **> 单一视图应用程序 > C#** 模板，将其`ScratchTicket`命名为，然后继续完成新建项目向导：

    [![](ios-designable-controls-walkthrough-images/01new.png "创建新解决方案")](ios-designable-controls-walkthrough-images/01new.png#lightbox)

1. 创建名为`ScratchTicketView`的新空类文件：

    [![](ios-designable-controls-walkthrough-images/02new.png "创建新的 ScratchTicketView 类")](ios-designable-controls-walkthrough-images/02new.png#lightbox)

1. 为`ScratchTicketView`类添加以下代码：

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

1. `FillTexture.png`将、 `FillTexture2.png` 和`Monkey.png`文件（可[从 GitHub](https://github.com/xamarin/ios-samples/blob/master/ScratchTicket/Resources/images.zip?raw=true)获取）添加到**Resources**文件夹。

1. 双击该`Main.storyboard`文件以在设计器中将其打开：

    [![](ios-designable-controls-walkthrough-images/03new.png "IOS 设计器")](ios-designable-controls-walkthrough-images/03new.png#lightbox)

1. 将 "图像"**视图**从 "**工具箱**" 拖放到情节提要中的视图上。

    [![](ios-designable-controls-walkthrough-images/04new.png "添加到布局的图像视图")](ios-designable-controls-walkthrough-images/04new.png#lightbox)

1. 选择 "**图像" 视图**，并将其 " `Monkey.png` **image** " 属性更改为。

    [![](ios-designable-controls-walkthrough-images/05new.png "将图像视图图像属性设置为猴子。 png")](ios-designable-controls-walkthrough-images/05new.png#lightbox)

1. 使用大小类时，需要约束此图像视图。 单击图像两次，将其置于约束模式。 让我们通过单击中心固定控点并将其垂直和水平对齐来将其限制到中心：

    [![](ios-designable-controls-walkthrough-images/06new.png "对图像进行居中")](ios-designable-controls-walkthrough-images/06new.png#lightbox)

1. 若要限制高度和宽度，请单击大小固定控点（"骨骼" 整形控点），并分别选择 "宽度" 和 "高度"：

    [![](ios-designable-controls-walkthrough-images/07new.png "添加约束")](ios-designable-controls-walkthrough-images/07new.png#lightbox)

1. 单击工具栏中的 "更新" 按钮，根据约束更新框架：

    [![](ios-designable-controls-walkthrough-images/08new.png "约束工具栏")](ios-designable-controls-walkthrough-images/08new.png#lightbox)

1. 接下来，生成项目，以便 "**暂存票证" 视图**将显示在 "工具箱" 中的 "**自定义组件**" 下面：

    [![](ios-designable-controls-walkthrough-images/09new.png "自定义组件工具箱")](ios-designable-controls-walkthrough-images/09new.png#lightbox)

1. 拖放 "**暂存票证" 视图**，使其显示在 "猴子" 映像之上。 调整拖动控点，使草稿票证视图完全覆盖这些猴子，如下所示：

    [![](ios-designable-controls-walkthrough-images/10new.png "图像视图上的暂存票证视图")](ios-designable-controls-walkthrough-images/10new.png#lightbox)

1. 通过绘制边框来选择两个视图，将暂存票证视图限制为图像视图。 选择相应的选项，根据约束将其限制为宽度、高度、中心和中间和更新帧，如下所示：

    [![](ios-designable-controls-walkthrough-images/11new.png "居中和添加约束")](ios-designable-controls-walkthrough-images/11new.png#lightbox)

1. 运行应用程序并 "擦掉" 映像以显示猴子。

    [![](ios-designable-controls-walkthrough-images/10-app.png "示例应用运行")](ios-designable-controls-walkthrough-images/10-app.png#lightbox)

## <a name="adding-design-time-properties"></a>添加设计时属性

设计器还包括对属性类型 numeric、enumeration、string、bool、CGSize、UIColor 和 UIImage 的自定义控件的设计时支持。 为了说明这一点，让我们将一个属性`ScratchTicketView`添加到中，以设置 "有划痕的图像"。

将以下代码添加到`ScratchTicketView`属性的类：

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

我们可能还需要向`Draw`方法中添加 null 检查，如下所示：

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

`true`如果将`ExportAttribute` `BrowsableAttribute`和参数设置为，则会在设计器的**属性**面板中显示属性。 如果将属性更改为项目附带的另一个图像（如`FillTexture2.png`），则会在设计时更新控件，如下所示：

 [![](ios-designable-controls-walkthrough-images/11-customproperty.png "编辑设计时属性")](ios-designable-controls-walkthrough-images/10-app.png#lightbox)

## <a name="summary"></a>总结

本文介绍了如何创建自定义控件，以及如何使用 iOS 设计器在 iOS 应用程序中使用它。 我们了解了如何创建和生成控件，使其可用于设计器的**工具箱**中的应用程序。 此外，我们还讨论了如何实现控件，使其在设计时和运行时都能正确呈现，以及如何在设计器中公开自定义控件属性。

## <a name="related-links"></a>相关链接

- [ScratchTicket （示例）](https://docs.microsoft.com/samples/xamarin/ios-samples/scratchticket)
- [必需的图像（示例）](https://github.com/xamarin/ios-samples/blob/master/ScratchTicket/Resources/images.zip?raw=true)
