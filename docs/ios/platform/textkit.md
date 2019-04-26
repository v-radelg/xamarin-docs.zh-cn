---
title: 在 Xamarin.iOS TextKit
description: 本文档介绍如何在 Xamarin.iOS 中使用 TextKit。 TextKit 提供强大的文本布局和呈现功能。
ms.prod: xamarin
ms.assetid: 1D0477E8-CD1E-48A9-B7C8-7CA892069EFF
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/19/2017
ms.openlocfilehash: f08e37d17cc32e45232d54cc4a51bb48d7ec94b1
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61184494"
---
# <a name="textkit-in-xamarinios"></a>在 Xamarin.iOS TextKit

TextKit 是一个新 API，提供了强大的文本布局和呈现功能。 它基于低级别的核心文本框架，但可以更轻松地使用比核心文本。

若要使 TextKit 的功能可用于标准控件，多个 iOS 文本控件已重新实现，若要使用 TextKit，包括：

-  UITextView
-  UITextField
-  UILabel

## <a name="architecture"></a>体系结构

TextKit 提供文本存储区分开来的布局和显示，其中包括以下类的分层式体系结构：

-  `NSTextContainer` -提供的坐标系统和用于对布局文本的几何图形。
-  `NSLayoutManager` – 规格布局文本通过将文本转换标志符号。 
-  `NSTextStorage` – 包含文本数据，以及处理批处理的文本属性更新。 任何批处理更新在送达的更改，如重新计算布局和重新绘制文本的实际处理的布局管理器。


这三个类都应用于呈现文本视图。 内置的文本处理视图，如`UITextView`， `UITextField`，并`UILabel`已有这些设置，但您可以创建并将它们应用于任何`UIView`实例。

下图说明了此体系结构：

 ![](textkit-images/textkitarch.png "此图说明了 TextKit 体系结构")

## <a name="text-storage-and-attributes"></a>文本存储和属性

`NSTextStorage`类包含由视图显示的文本。 它也能进行通信的文本-例如更改为字符或其属性-显示的布局管理器的任何更改。 `NSTextStorage` 继承自`MSMutableAttributed`字符串，从而允许文本属性来指定批次之间的更改`BeginEditing`和`EndEditing`调用。

例如，下面的代码段指定变为前台和背景颜色，分别和面向特定的范围：

```csharp
textView.TextStorage.BeginEditing ();
textView.TextStorage.AddAttribute(UIStringAttributeKey.ForegroundColor, UIColor.Green, new NSRange(200, 400));
textView.TextStorage.AddAttribute(UIStringAttributeKey.BackgroundColor, UIColor.Black, new NSRange(210, 300));
textView.TextStorage.EndEditing ();
```

之后`EndEditing`是调用，所做的更改发送到布局管理器，这反过来会执行任何必要的布局和呈现计算为视图中显示的文本。

## <a name="layout-with-exclusion-path"></a>排除路径使用的布局

TextKit 还支持布局，并允许对于复杂的方案，如调用多列的文本和周围指定的路径流动文本*排除路径*。 排除路径被应用于文本容器，这会修改文本布局，从而导致要围绕指定的路径排列的文本的几何图形。

添加排除路径时，需要设置`ExclusionPaths`的布局管理器中的属性。 设置此属性会导致要使之无效的文本布局和流周围排除路径的文本的布局管理器。

### <a name="exclusion-based-on-a-cgpath"></a>排除基于 CGPath

请考虑以下`UITextView`子类实现：

```csharp
public class ExclusionPathView : UITextView
{
    CGPath exclusionPath;
    CGPoint initialPoint;
    CGPoint latestPoint;
    UIBezierPath bezierPath;

    public ExclusionPathView (string text)
    {
        Text = text;
        ContentInset = new UIEdgeInsets (20, 0, 0, 0);
        BackgroundColor = UIColor.White;
        exclusionPath = new CGPath ();
        bezierPath = UIBezierPath.Create ();

        LayoutManager.AllowsNonContiguousLayout = false;
    }

    public override void TouchesBegan (NSSet touches, UIEvent evt)
    {
        base.TouchesBegan (touches, evt);

        var touch = touches.AnyObject as UITouch;

        if (touch != null) {
            initialPoint = touch.LocationInView (this);
        }
    }

    public override void TouchesMoved (NSSet touches, UIEvent evt)
    {
        base.TouchesMoved (touches, evt);

        UITouch touch = touches.AnyObject as UITouch;

        if (touch != null) {
            latestPoint = touch.LocationInView (this);
            SetNeedsDisplay ();
        }
    }

    public override void TouchesEnded (NSSet touches, UIEvent evt)
    {
        base.TouchesEnded (touches, evt);

        bezierPath.CGPath = exclusionPath;
        TextContainer.ExclusionPaths = new UIBezierPath[] { bezierPath };
    }

    public override void Draw (CGRect rect)
    {
        base.Draw (rect);

        if (!initialPoint.IsEmpty) {

            using (var g = UIGraphics.GetCurrentContext ()) {

                g.SetLineWidth (4);
                UIColor.Blue.SetStroke ();

                if (exclusionPath.IsEmpty) {
                    exclusionPath.AddLines (new CGPoint[] { initialPoint, latestPoint });
                } else {
                    exclusionPath.AddLineToPoint (latestPoint);
                }

                g.AddPath (exclusionPath);
                g.DrawPath (CGPathDrawingMode.Stroke);
            }
        }
    }
}
```

此代码将添加对绘制文本视图使用核心图形上的支持。 由于`UITextView`类现已内置要 TextKit 用于其文本呈现和布局，它可以使用 TextKit，例如设置排除路径的所有功能。

> [!IMPORTANT]
> 此示例子类`UITextView`添加绘图支持触摸。 子类化`UITextView`无需获取 TextKit 的功能。



用户绘制的文本视图上绘制后`CGPath`应用于`UIBezierPath`通过设置实例`UIBezierPath.CGPath`属性：

```csharp
bezierPath.CGPath = exclusionPath;
```

更新以下代码行进行更新在路径两边的文本布局：

```csharp
TextContainer.ExclusionPaths = new UIBezierPath[] { bezierPath };
```

下面的屏幕截图演示了如何更改文本布局周围绘制的路径流动：

<!-- ![](textkit-images/exclusionpath1.png "This screenshot illustrates how the text layout changes to flow around the drawn path")--> 
![](textkit-images/exclusionpath2.png "此屏幕截图演示了如何更改文本布局周围绘制的路径流动")

请注意，布局管理器的`AllowsNonContiguousLayout`属性在这种情况下设置为 false。 这将导致重新计算该文本会更改其中的所有事例的布局。 通过避免完整布局刷新，尤其是在大型文档的情况下，此值设置为 true 可能有益于性能。 但是，将设置`AllowsNonContiguousLayout`为 true 将防止排除路径更新布局在某些情况下-例如，如果文本输入的运行时，没有尾随回车符之前要设置的路径。


## <a name="related-links"></a>相关链接

- [对 iOS 7 （示例） 简介](https://developer.xamarin.com/samples/monotouch/IntroToiOS7)
- [iOS 7 用户界面概述](~/ios/platform/introduction-to-ios7/ios7-ui.md)
- [后台处理](~/ios/app-fundamentals/backgrounding/index.md)
