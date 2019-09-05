---
title: Xamarin 中的宽色
description: 本文档讨论了宽颜色以及如何在 Xamarin 或 Xamarin 应用程序中使用它。
ms.prod: xamarin
ms.assetid: 576E978A-F182-489A-83E4-D8CDC6890B24
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/17/2017
ms.openlocfilehash: a1f5301d0c5c0674e162b3d7689c83bbb4f6ae90
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2019
ms.locfileid: "70290535"
---
# <a name="wide-color-in-xamarinios"></a>Xamarin 中的宽色

_本文介绍广泛的颜色，以及如何在 Xamarin 或 Xamarin 应用程序中使用它。_

iOS 10 和 macOS Sierra 增强了对整个系统（包括核心图形、核心图像、金属和 AVFoundation 等框架）中的扩展范围像素格式和宽色域颜色空间的支持。 通过在整个图形堆栈中提供此行为，可进一步减轻对具有宽颜色显示的设备的支持。

## <a name="asset-catalogs"></a>资产目录

### <a name="supporting-wide-color-with-asset-catalogs"></a>支持资产目录的宽色

在 iOS 10 和 macOS Sierra 中，Apple 展开了用于在应用捆绑包中包含和分类静态图像内容的资产目录，以支持宽色。

使用资产目录可为应用提供以下好处：

- 它们为静态映像资产提供了最佳的部署选项。
- 它们支持自动的颜色更正。
- 它们支持自动像素格式优化。
- 它们支持应用切片和应用缩小，这可确保仅将相关内容传递到最终用户的设备。

Apple 已对资产目录进行了以下增强功能，以获得宽颜色支持：

- 它们支持16位（每个颜色通道）源内容。
- 它们支持按显示范围为内容编制目录。 可以为 sRGB 或显示 P3 gamuts 标记内容。

开发人员有三个选项用于在应用程序中支持宽色内容：

1. **不执行任何**操作-由于仅应在应用需要显示鲜艳颜色（其中会增强用户体验）的情况下使用宽色内容，因此此要求之外的内容应保持原样。 它将继续在所有硬件情况下正确呈现。
2. **升级现有内容以显示 P3** -这要求开发人员使用 P3 格式的新的升级文件替换其资产目录中的现有图像内容，并在 "资产编辑器" 中对其进行标记。 生成时，将从这些资产生成一个 sRGB 派生映像。
3. **提供优化的资产内容**-在这种情况下，开发人员将同时为资产目录中的每个图像提供8位 sRGB 和16位显示 P3 视觉视觉。

### <a name="asset-catalog-deployment"></a>资产目录部署

当开发人员将应用程序提交到应用商店（包含包含宽色图像内容的资产目录）时，将发生以下情况：

- 将应用部署到最终用户时，应用切片将确保仅向用户的设备发送适当的内容变型。
- 在不支持宽色的设备上，由于不会将宽色内容发送到设备，因此不会有有效的负载成本用于包括宽色内容。
- `NSImage`在 macOS Sierra （及更高版本）将自动为硬件显示选择最佳内容表示形式。
- 如果设备硬件显示特征发生更改，则将自动刷新显示的内容。

### <a name="asset-catalog-storage"></a>资产目录存储

资产目录存储具有以下属性和应用的含义：

- 在生成时，Apple 尝试通过高质量的图像转换优化图像内容的存储。
- 对于宽颜色内容，每个颜色通道使用16位。
- 内容相关图像压缩用于降低可交付结果内容的大小。 添加了新的 "有损" 的压缩，以进一步优化内容大小。

## <a name="rendering-off-screen-images-in-app"></a>在应用程序中呈现屏幕外图像

根据要创建的应用类型，它可能允许用户包括从 internet 收集的图像内容或直接在应用内部创建图像内容（例如，如矢量绘图应用）。

在这两种情况下，应用程序都可以使用添加到 iOS 和 macOS 的增强功能，以宽颜色呈现所需的图像。

### <a name="drawing-wide-color-in-ios"></a>在 iOS 中绘制宽色

在讨论如何在 iOS 10 中正确绘制宽彩色图像之前，请查看以下常见 iOS 绘图代码：

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

标准代码存在一些问题，需要先解决这些问题，_然后_才能将其用于绘制宽颜色图像。 用于启动 iOS 图像绘制的方法具有以下限制：`UIGraphics.BeginImageContext (size)`

- 它无法创建每个颜色通道超过8位的图像上下文。
- 它不能表示扩展范围 sRGB 颜色空间中的颜色。
- 它无法提供用于在非 sRGB 颜色空间中创建上下文的接口，因为在后台调用的是低级别 C 例程。

若要处理这些限制并在 iOS 10 中绘制宽颜色图像，请改用以下代码：

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

新`UIGraphicsImageRenderer`类创建一个能够处理扩展范围 sRGB 颜色空间的新映像上下文，并具有以下功能：

- 默认情况下，它是完全颜色的管理。
- 默认情况下，它支持扩展范围 sRGB 颜色空间。
- 它可智能地决定是否应基于运行应用的 iOS 设备的功能，在 sRGB 或扩展范围 sRGB 颜色空间中呈现。
- 它完全自动管理映像上下文（`CGContext`）生存期，因此开发人员无需担心如何调用开始和结束上下文命令。
- 它与`UIGraphics.GetCurrentContext()`方法兼容。

调用类`UIGraphicsImageRenderer`的方法来创建宽颜色图像，并向完成处理程序传递要绘制到`CreateImage`的图像上下文。 所有绘图都在此完成处理程序内完成，如下所示：

- `UIColor.FromDisplayP3`方法会在宽区中创建一个新的完全饱和颜色，并显示 P3 颜色空间，并用于绘制矩形的前半部分。 
- 矩形的后半部分以普通 sRGB 完全饱和颜色绘制，以进行比较。

### <a name="drawing-wide-color-in-macos"></a>在 macOS 中绘制宽色

`NSImage`类已在 macOS Sierra 中展开以支持图形宽彩色图像。 例如:

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

## <a name="rendering-on-screen-images-in-app"></a>在应用程序中呈现屏幕上的图像

若要在屏幕上呈现宽颜色图像，此过程的工作方式类似于为上面提供的 macOS 和 iOS 绘制离屏宽度的彩色图像。

### <a name="rendering-on-screen-in-ios"></a>在 iOS 中的屏幕上呈现

当应用需要在 iOS 的屏幕上以宽颜色呈现图像时，请像往常一样`Draw`重写中`UIView`所述的方法。 例如:

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

由于 iOS 10 与上面显示`UIGraphicsImageRenderer`的类相同，因此它会智能地根据调用`Draw`方法时运行该应用程序的 iOS 设备的功能，来确定它是否应呈现在 srgb 或扩展范围 srgb 颜色空间中。 此外，从`UIImageView` iOS 9.3 开始，还对进行了颜色管理。

如果应用需要知道如何`UIView`在或`UIViewController`上进行呈现，则可以检查`UITraitCollection`类的新`DisplayGamut`属性。 此值将为`UIDisplayGamut`以下值的枚举：

- P3
- Srgb
- 未指定

如果应用需要控制用于绘制图像的颜色空间，则它可以使用的`ContentsFormat` `CALayer`新属性来指定所需的颜色空间。 此值可以是`CAContentsFormat`以下值的枚举：

- Gray8Uint
- Rgba16Float
- Rgba8Uint

### <a name="rendering-on-screen-in-macos"></a>在 macOS 中的屏幕上呈现

如果应用需要在 macOS 中的屏幕上以宽颜色呈现图像，请像往常一样`DrawRect`重写相关`NSView`的方法。 例如：

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

同样，它会根据调用`DrawRect`方法时应用运行的 Mac 硬件的功能，智能地确定是否应呈现在 srgb 或扩展范围 srgb 颜色空间中。

如果应用需要控制用于绘制图像的颜色空间，则可以使用`DepthLimit` `NSWindow`类的新属性来指定所需的颜色空间。 此值可以是`NSWindowDepth`以下值的枚举：

- OneHundredTwentyEightBitRgb
- SixtyfourBitRgb
- TwentyfourBitRgb
