---
title: 结合使用自定义控件和 iOS 设计器
description: 本文档介绍如何创建自定义控件并将其与 Xamarin Designer for iOS 一起使用。 它演示如何在 iOS 设计器的工具箱中使控件可用，实现控件，使其正确呈现和设计时间等。
ms.prod: xamarin
ms.assetid: 9032B32E-97BD-4DA6-9955-811B84682578
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/22/2017
ms.openlocfilehash: 4b8c72da1e280b83e215bca9316bc0b9de99402c
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73003801"
---
# <a name="using-custom-controls-with-the-ios-designer"></a>结合使用自定义控件和 iOS 设计器

## <a name="requirements"></a>要求

Xamarin Designer for iOS 在 Visual Studio for Mac 和 Visual Studio 2017 及更高版本的 Windows 中可用。

本指南假定你熟悉[入门指南](~/ios/get-started/index.md)中所述的内容。

## <a name="walkthrough"></a>演练

> [!IMPORTANT]
> 从 Xamarin 5.5 开始，创建自定义控件的方式与早期版本略有不同。 若要创建自定义控件，需要 `IComponent` 接口（使用关联的实现方法）或可以使用 `[DesignTimeVisible(true)]`对类进行批注。 下面的演练示例使用后一种方法。

1. 从 iOS > 应用创建新的解决方案 **> 单一视图应用程序 > C#** 模板，将其命名为`ScratchTicket`，并继续执行 "新建项目" 向导：

    [![](ios-designable-controls-walkthrough-images/01new.png "Create a new solution")](ios-designable-controls-walkthrough-images/01new.png#lightbox)

1. 创建名为 `ScratchTicketView`的新空类文件：

    [![](ios-designable-controls-walkthrough-images/02new.png "Create a new ScratchTicketView class")](ios-designable-controls-walkthrough-images/02new.png#lightbox)

1. 为 `ScratchTicketView` 类添加以下代码：

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

1. 将 `FillTexture.png`、`FillTexture2.png` 和 `Monkey.png` 文件（可[从 GitHub](https://github.com/xamarin/ios-samples/blob/master/ScratchTicket/Resources/images.zip?raw=true)获取）添加到**Resources**文件夹。

1. 双击 `Main.storyboard` 文件以在设计器中将其打开：

    [![](ios-designable-controls-walkthrough-images/03new.png "The iOS Designer")](ios-designable-controls-walkthrough-images/03new.png#lightbox)

1. 将 "图像"**视图**从 "**工具箱**" 拖放到情节提要中的视图上。

    [![](ios-designable-controls-walkthrough-images/04new.png "An Image View added to the layout")](ios-designable-controls-walkthrough-images/04new.png#lightbox)

1. 选择 "**图像" 视图**，并将其 " **image** " 属性更改为 `Monkey.png`。

    [![](ios-designable-controls-walkthrough-images/05new.png "Setting Image View Image property to Monkey.png")](ios-designable-controls-walkthrough-images/05new.png#lightbox)

1. 使用大小类时，需要约束此图像视图。 单击图像两次，将其置于约束模式。 让我们通过单击中心固定控点并将其垂直和水平对齐来将其限制到中心：

    [![](ios-designable-controls-walkthrough-images/06new.png "Centering the image")](ios-designable-controls-walkthrough-images/06new.png#lightbox)

1. 若要限制高度和宽度，请单击大小固定控点（"骨骼" 整形控点），并分别选择 "宽度" 和 "高度"：

    [![](ios-designable-controls-walkthrough-images/07new.png "Adding Constraints")](ios-designable-controls-walkthrough-images/07new.png#lightbox)

1. 单击工具栏中的 "更新" 按钮，根据约束更新框架：

    [![](ios-designable-controls-walkthrough-images/08new.png "The Constraints toolbar")](ios-designable-controls-walkthrough-images/08new.png#lightbox)

1. 接下来，生成项目，以便 "**暂存票证" 视图**将显示在 "工具箱" 中的 "**自定义组件**" 下面：

    [![](ios-designable-controls-walkthrough-images/09new.png "The Custom Components toolbox")](ios-designable-controls-walkthrough-images/09new.png#lightbox)

1. 拖放 "**暂存票证" 视图**，使其显示在 "猴子" 映像之上。 调整拖动控点，使草稿票证视图完全覆盖这些猴子，如下所示：

    [![](ios-designable-controls-walkthrough-images/10new.png "A Scratch Ticket View over the Image View")](ios-designable-controls-walkthrough-images/10new.png#lightbox)

1. 通过绘制边框来选择两个视图，将暂存票证视图限制为图像视图。 选择相应的选项，根据约束将其限制为宽度、高度、中心和中间和更新帧，如下所示：

    [![](ios-designable-controls-walkthrough-images/11new.png "Centering and adding Constraints")](ios-designable-controls-walkthrough-images/11new.png#lightbox)

1. 运行应用程序并 "擦掉" 映像以显示猴子。

    [![](ios-designable-controls-walkthrough-images/10-app.png "A sample app run")](ios-designable-controls-walkthrough-images/10-app.png#lightbox)

## <a name="adding-design-time-properties"></a>添加设计时属性

设计器还包括对属性类型 numeric、enumeration、string、bool、CGSize、UIColor 和 UIImage 的自定义控件的设计时支持。 为了说明这一点，让我们将一个属性添加到 `ScratchTicketView` 来设置 "有划痕的图像"。

将以下代码添加到属性的 `ScratchTicketView` 类：

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

我们可能还需要向 `Draw` 方法中添加 null 检查，如下所示：

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

如果将参数设置为的 `ExportAttribute` 和 `BrowsableAttribute` 包括 `true`，则会在设计器的**属性**面板中显示属性。 将属性更改为项目附带的另一个图像（如 `FillTexture2.png`）将导致在设计时更新控件，如下所示：

 [![](ios-designable-controls-walkthrough-images/11-customproperty.png "Editing Design Time properties")](ios-designable-controls-walkthrough-images/10-app.png#lightbox)

## <a name="summary"></a>总结

本文介绍了如何创建自定义控件，以及如何使用 iOS 设计器在 iOS 应用程序中使用它。 我们了解了如何创建和生成控件，使其可用于设计器的**工具箱**中的应用程序。 此外，我们还讨论了如何实现控件，使其在设计时和运行时都能正确呈现，以及如何在设计器中公开自定义控件属性。

## <a name="related-links"></a>相关链接

- [ScratchTicket （示例）](https://docs.microsoft.com/samples/xamarin/ios-samples/scratchticket)
- [必需的图像（示例）](https://github.com/xamarin/ios-samples/blob/master/ScratchTicket/Resources/images.zip?raw=true)
