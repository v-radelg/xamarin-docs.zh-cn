---
title: 在 Xamarin.iOS 中广泛的颜色
description: 本文档介绍了广泛的颜色和如何可以在 Xamarin.iOS 或 Xamarin.Mac 应用中使用它。
ms.prod: xamarin
ms.assetid: 576E978A-F182-489A-83E4-D8CDC6890B24
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: 523aa1a97e1c536b5afbdb66c52f605382fa338d
ms.sourcegitcommit: a153623a69b5cb125f672df8007838afa32e9edf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/20/2019
ms.locfileid: "67268803"
---
# <a name="wide-color-in-xamarinios"></a>在 Xamarin.iOS 中广泛的颜色

_本文介绍如何广泛的颜色和如何可以在 Xamarin.iOS 或 Xamarin.Mac 应用中使用它。_

iOS 10 和 macOS Sierra 增强了对扩展范围像素格式和整个系统的核心图形、 Core 映像，Metal 是和 AVFoundation 等框架的范围内所有颜色空间的支持。 对具有广泛的颜色显示设备的支持进一步减轻通过提供在整个图形堆栈整个此行为。

## <a name="asset-catalogs"></a>资产目录

### <a name="supporting-wide-color-with-asset-catalogs"></a>支持广泛的颜色与资产目录

在 iOS 10 和 macOS Sierra，Apple 已扩展资产目录用于包含和对应用的捆绑包中的静态图像内容进行分类以支持广泛的颜色。

使用资产目录到应用程序具有以下优点：

- 它们提供静态图像资产的最佳部署选项。
- 它们支持自动颜色校正。
- 它们支持自动的像素格式优化。
- 它们支持应用程序进行切片和应用缩小这可确保只有已相关获取内容传递给最终用户的设备。

Apple 已获得广泛的颜色支持对资产目录进行以下增强功能：

- 它们支持 16 位 （每个颜色通道） 的源内容。
- 通过显示整个范围，它们支持目录的内容。 可以为 sRGB 或显示 P3 色域标记内容。

开发人员可以在其应用中支持广泛的颜色内容的三个选项：

1. **不执行任何操作**-由于广泛的颜色内容只应在应用程序需要显示鲜艳颜色 （其中它们将增强用户体验） 的情况下，此要求之外的内容应保留为的是。 它将继续在所有硬件情况下正确呈现。
2. **将现有内容升级到 Display P3** -这需要开发人员及其资产目录中的现有映像内容替换为一个新的 P3 格式已升级文件和资产编辑器中对这种情况下对其进行标记。 在生成时，将从这些资产生成 sRGB 派生映像。
3. **提供优化资产内容**-在此情况下，开发人员将提供 8 位 sRGB 和每个图像资产目录 （正确标记资产编辑器中） 中的 16 位 Display P3 远景。

### <a name="asset-catalog-deployment"></a>资产目录部署

当开发人员提交到包括广泛的颜色的图像内容的资产目录与应用商店应用时，将执行以下操作：

- 应用程序部署到最终用户，应用切片将以确保只有适当的内容变体传递给用户的设备。
- 在设备上不支持广泛的颜色，则包括广泛的颜色内容有效负载免费因为永远不会发送到设备。
- `NSImage` 在 macOS Sierra （及更高版本） 将自动选择的硬件的显示的最佳内容表示形式。
- 或者设备硬件显示特征更改时，将自动刷新显示的内容。

### <a name="asset-catalog-storage"></a>资产目录存储

资产目录存储具有以下属性和对应用程序带来的影响：

- 在生成时，Apple 会尝试优化的高质量图像转换通过图像内容的存储。
- 广泛的颜色内容的每个颜色通道使用 16 位。
- 使用内容相关的映像压缩来降低可交付结果的内容大小。 添加了新的"丢失"压缩文档，以进一步优化内容的大小。

## <a name="rendering-off-screen-images-in-app"></a>呈现应用程序中的屏外图像

根据要创建的应用程序的类型，它可能允许用户为包括映像内容他们已从 internet 收集或创建 （如矢量图形应用程序为例） 应用内直接图像内容。

在这种情况下，应用可以呈现所需的图像屏外中广泛使用的增强的功能添加到 iOS 和 macOS 的颜色。

### <a name="drawing-wide-color-in-ios"></a>在 iOS 中绘制广泛的颜色

在讨论如何正确绘制广泛的颜色图像在 iOS 10 中之前, 看一看以下常见 iOS 绘图代码：

```csharp
public UIImage DrawWideColorImage ()
{
    var size = new CGSize (250, 250);
    UIGraphics.BeginImageContext (size);

    ...

    UIGraphics.EndImageContext ();
    return image = UIGraphics.GetImageFromCurrentImageContext ();
    }
```

有的标准代码，将需要解决的问题_之前_可用于绘制广泛的颜色图像。 `UIGraphics.BeginImageContext (size)`启动 iOS 图像绘制方法具有以下限制：

- 它不能具有多个 8 位每个颜色通道创建映像的上下文。
- 它不能表示扩展范围 sRGB 颜色空间中的颜色。
- 它没有提供的接口以创建非 sRGB 颜色空间中的上下文，由于在后台调用的低级别 C 例程的功能。

若要处理这些限制，并在 iOS 10 中绘制的广泛的颜色映像，请改为使用以下代码：

```csharp
public UIImage DrawWideColorImage ()
{
    var size = new CGSize (250, 250);
    var render = new UIGraphicsImageRenderer (size);

    var image = render.CreateImage ((UIGraphicsImageRendererContext context) => {
        var bounds = context.Format.Bounds;
        CGRect slice;
        CGRect remainder;
        bounds.Divide (bounds.Width / 2, CGRectEdge.MinXEdge, out slice, out remainder);

        var redP3 = UIColor.FromDisplayP3 (1.0F, 0.0F, 0.0F, 1.0F);
        redP3.SetColor ();
        context.FillRect (slice);

        var redRGB = UIColor.Red;
        redRGB.SetColor ();
        context.FillRect (remainder);
    });

    // Return results
    return image;
}
```

新`UIGraphicsImageRenderer`类创建新的映像环境能够处理扩展范围 sRGB 颜色空间，并具有以下功能：

- 它完全是由默认的颜色。
- 它支持扩展范围 sRGB 颜色空间默认情况下。
- 以智能方式确定是否应呈现在 sRGB 或扩展范围 sRGB 颜色空间的 iOS 设备上运行应用的功能。
- 它完全和自动管理的映像上下文 (`CGContext`) 使开发人员无需担心如何调用的生存期的开始和结束上下文命令。
- 它是否与兼容`UIGraphics.GetCurrentContext()`方法。

`CreateImage`方法的`UIGraphicsImageRenderer`类是调用以创建广泛的颜色映像，并且传递具有图像上下文来绘制到完成处理程序。 所有绘图是内执行此完成处理程序，如下所示：

- `UIColor.FromDisplayP3`方法宽色域显示 P3 颜色空间内创建新完全饱和的红色，并使用它来绘制的矩形的前半。 
- 第二个一半的矩形中绘制正常 sRGB 完全满负荷状态进行比较的红色。

### <a name="drawing-wide-color-in-macos"></a>在 macOS 中绘制广泛的颜色

`NSImage`类扩展了 macOS Sierra 以支持广泛的颜色的图像的绘制。 例如：

```csharp
var size = CGSize(250,250);
var wideColorImage = new NSImage(size, false, (drawRect) =>{
    var rects = drawRect.Divide(drawRect.Size.Width/2,CGRect.MinXEdge);

    var color = new NSColor(1.0, 0.0, 0.0, 1.0).Set();
    var path = new NSBezierPath(rects.Slice).Fill();

    color = new NSColor(1.0, 0.0, 0.0, 1.0).Set();
    path = new NSBezierPath(rects.Remainder).Fill();

    // Return modified
    return true;
});
```

## <a name="rendering-on-screen-images-in-app"></a>在应用中屏幕上呈现图像

屏幕上呈现广泛的颜色的图像，过程的工作原理类似于绘制到屏幕外广泛的颜色图像适用于 macOS 和 iOS 上面介绍。

### <a name="rendering-on-screen-in-ios"></a>在 iOS 中呈现屏幕

当应用程序需要在 iOS 中屏幕上广泛的颜色呈现的图像时，重写`Draw`方法的`UIView`照常有问题。 例如：

```csharp
using System;
using UIKit;
using CoreGraphics;

namespace MonkeyTalk
{
    public class MonkeyView : UIView
    {
        public MonkeyView ()
        {
        }

        public override void Draw (CGRect rect)
        {
            base.Draw (rect);

            // Draw the image to screen
        ...
        }
    }
}
```

与 iOS 10 一样`UIGraphicsImageRenderer`如上所示，它可智能地确定是否应呈现在 sRGB 或扩展范围 sRGB 颜色空间何时运行该应用的 iOS 设备的功能中的类`Draw`调用方法。 此外，`UIImageView`进行了颜色以及 iOS 9.3 自托管。

如果应用需要知道如何呈现上完成`UIView`或`UIViewController`，可以检查新`DisplayGamut`属性的`UITraitCollection`类。 此值将为`UIDisplayGamut`以下枚举：

- P3
- Srgb
- 未指定

如果应用程序需要以控制哪些颜色空间用于绘制图像，它可以使用一个新`ContentsFormat`属性的`CALayer`来指定所需的颜色空间。 此值可以是`CAContentsFormat`以下枚举：

- Gray8Uint
- Rgba16Float
- Rgba8Uint

### <a name="rendering-on-screen-in-macos"></a>在 macOS 中屏幕上呈现

当应用程序需要在屏幕上在 macOS 中广泛的颜色呈现的图像时，重写`DrawRect`方法的`NSView`照常有问题。 例如：

```csharp
using System;
using AppKit;
using CoreGraphics;
using CoreAnimation;

namespace MonkeyTalkMac
{
    public class MonkeyView : NSView
    {
        public MonkeyView ()
        {
        }

        public override void DrawRect (CGRect dirtyRect)
        {
            base.DrawRect (dirtyRect);

            // Draw graphics into the view
            ...
        }
    }
}
```

同样，它以智能方式确定是否应呈现在 sRGB 或扩展范围 sRGB 颜色空间何时运行该应用的 Mac 硬件的功能中`DrawRect`调用方法。

如果应用程序需要以控制哪些颜色空间用于绘制图像，它可以使用一个新`DepthLimit`属性的`NSWindow`类，以指定所需的颜色空间。 此值可以是`NSWindowDepth`以下枚举：

- OneHundredTwentyEightBitRgb
- SixtyfourBitRgb
- TwentyfourBitRgb
