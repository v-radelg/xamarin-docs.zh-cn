---
title: 在 Xamarin.iOS 中广泛的颜色
description: 本文档介绍了广泛的颜色和如何可以在 Xamarin.iOS 或 Xamarin.Mac 应用中使用它。 它还提供许多重要颜色相关概念，如颜色空间、 通道和主数据库的高级概述。
ms.prod: xamarin
ms.assetid: 576E978A-F182-489A-83E4-D8CDC6890B24
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: f139bcceda12752e43a3a8330fa0a0e038e539f9
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61423516"
---
# <a name="wide-color-in-xamarinios"></a>在 Xamarin.iOS 中广泛的颜色

_本文介绍如何广泛的颜色和如何可以在 Xamarin.iOS 或 Xamarin.Mac 应用中使用它。_

iOS 10 和 macOS Sierra 增强了对扩展范围像素格式和整个系统的核心图形、 Core 映像、 裸机和 AVFoundation 等框架的范围内所有颜色空间的支持。 对具有广泛的颜色显示设备的支持进一步减轻通过提供在整个图形堆栈整个此行为。

## <a name="about-wide-color"></a>有关广泛的颜色

如上面所述，iOS 10 和 macOS Sierra 增强了对扩展范围像素格式和在整个系统，包括核心图形、 Core 映像、 裸机和 AVFoundation 等框架的范围内所有颜色空间的支持。 对具有广泛的颜色显示设备的支持进一步减轻通过提供在整个图形堆栈整个此行为。

在 90 年代 Apple 创建 ColorSync 来处理在 mac 上处理的颜色 它们也有助于找到国际色彩联合会 (ICC) 来创建和升级的标准，用于处理计算机的硬件上的颜色集。 使用 ICC Apple 的工作已包含在 ColorSync，它已内置在 OS X （现在称为 macOS） 的核心。

在与硬件如 Retina 显示、 新 P3 显示和显示 （iMac 发布中发布 2015年） P3 颜色空间的显示技术的前沿，也是 Apple 和 TrueTone 显示专业人员 iPad、 iPhone 7 和 iPhone 7 中加上。

在 macOS Sierra 中广泛的颜色和 iOS 10，Apple 正在改变 macOS 和 iOS 句柄颜色以充分利用这些新的显示技术的方式。

## <a name="core-color-concepts"></a>颜色的核心概念

需要执行深入地了解在 macOS 和 iOS 中的颜色之前介绍的以下核心颜色概念：

### <a name="color-space"></a>颜色空间

颜色空间是一个环境中的颜色可以表示和比较。 它可以是定义其颜色组件的强度的一至四个维空间。 

[![](wide-color-images/color00.png "颜色空间")](wide-color-images/color00.png#lightbox)

### <a name="color-channels"></a>颜色通道

颜色组件还可以为颜色通道引用。 RGB 空格、 灰色空格、 CMYK 空格或设备独立空间将是某些熟悉的表示形式。 

[![](wide-color-images/color02.png "颜色组件可以也被称为颜色通道")](wide-color-images/color02.png#lightbox)

### <a name="color-primaries"></a>颜色的主数据库便

颜色的主数据库便提供了用于比较和计算颜色的坐标系。 颜色的主数据库便通常会在最高强度版本内颜色通道可以生成给定颜色。

[![](wide-color-images/color01.png "颜色的主数据库便提供用于比较和计算颜色坐标系统")](wide-color-images/color01.png#lightbox)

表示的 RGB 颜色空间，对于颜色的主数据库便是 where`1.0`定位坐标 (如`[1.0, 0.0, 0.0]`为红色)。

### <a name="color-gamut"></a>色域

色域是指所有可以为给定颜色空间内各个颜色通道的组合定义的颜色。

[![](wide-color-images/color03.png "颜色范围示例")](wide-color-images/color03.png#lightbox)

## <a name="what-is-wide-color"></a>什么是广泛的颜色

之前介绍的广泛的颜色主题，讨论应具有当前的行业标准颜色，标准的 RGB 颜色空间 (sRGB) 有关。 它是立即计算中使用最广泛的颜色空间，是适用于 iOS 和 macOS 的默认颜色空间。

SRGB 颜色空间具有以下属性：

- 它根据 ITU-R BT.709 标准。
- 它具有 2.2 近似 Gamma。
- 它表示典型光照条件 (D65)。

由于 sRGB 广泛用于行业，开发人员可以做出一些假设，将显示在任何设备上如实地表示指定的颜色。 但是，这可能不始终是这种情况。 此外，还有几个颜色的 sRGB 颜色空间不足，因此，不能在其中表示。

例如，许多织物旨在通过使用很多油墨和染料落 sRGB 以外的线程。 此外将使用明亮、 鲜艳的颜色的 sRGB 颜色空间超出范围创建一个人遇到其日常生活中的许多产品。 某些最具吸引力的无法出现在 sRGB 颜色示例是在本质上日落、 autumn 叶、 特殊鲜花和热带 waters 等事项。

具有已捕获原始格式的数字图像的用户可能已在其包含所有这些颜色数据，即使它不能正确表示 sRGB 颜色空间中，并因此不能正确显示在屏幕上的设备上的图像。

### <a name="the-display-p3-color-space"></a>显示 P3 颜色空间

在 2015 年，Apple 已发布的新产品 （iMac 和 iPad Pro 9.7"） 提供新的显示 P3 颜色空间来处理创建的 sRGB 颜色空间的问题。

[![](wide-color-images/color04.png "新的显示 P3 颜色空间")](wide-color-images/color04.png#lightbox)

显示 P3 颜色空间具有以下属性：

- 为现代硬件平台支持广泛的颜色空间。
- 基于 SMPTE DCI P3 标准。 DCI P3 专为数字投影仪但进行了修改由 Apple 支持显示器。
- 它具有 2.2 近似 Gamma。
- 它表示典型光照条件 (D65)。

根据 Apple，用户会将工作流迁移到其移动平台。 解决主讲人 sRGB 不仅仅包括广泛的颜色显示所需的专业移动设备 (iPad 专业人员) 中的颜色呈现问题。 解决方法之一是升级工厂校准，以使每个设备已被校准在工厂，确保设备的颜色显示的准确性和一致性。

另一种解决方案是完整的系统范围的颜色管理 Apple 已内置到 iOS 10 和 macOS Sierra 包含。 

### <a name="the-extended-range-srgb-color-space"></a>扩展范围 sRGB 颜色空间

新的 iOS 10 系统范围内颜色管理时都需的所有现有 iOS 应用的生成，并为 sRGB 进行微调。 它设计为确保它不会影响这些现有的应用或颜色表示形式或应用程序性能。

若要处理这种情况下，Apple 已包含在 iOS 10 中的扩展范围 sRGB 颜色空间 （和以及 macOS Sierra）。

扩展范围 sRGB 颜色空间具有以下属性：

- 具有所有相同 sRGB 主副本。
- 它具有 2.2 近似 Gamma。
- 它表示典型光照条件 (D65)。
- 它支持负值，值大于一 (1)。

通过允许的值小于零且大于 1，扩展范围 sRGB 颜色空间不仅允许现有应用到在 sRGB 而导致的性能下降或扭曲，但它不存在颜色使颜色空间来表示内可见的任何颜色范围。 所有这些都是同时仍保持与 sRGB 颜色空间相同的定位点来完成。

### <a name="extended-range-srgb-in-action"></a>在操作中扩展的范围 sRGB

若要查看零和一之外的值中扩展范围 sRGB 颜色空间的工作原理，请执行下面的示例显示 P3 颜色空间中的最饱和红色的：

[![](wide-color-images/color05.png "零和一之外的值中扩展范围 sRGB 颜色空间的工作原理")](wide-color-images/color05.png#lightbox)

显示 P3 中此颜色将表示为`[1.0, 0.0, 0.0]`和中扩展范围 sRGB 会`[1.358, -0.074, -0.012]`。 因为 sRGB 值是完全包含在显示 P3 和 Display P3 值奠定"外部"的 sRGB 范围。

允许从极端正转到极端负值像素值的物理硬件，它可以显示任何可见范围中可用的颜色，并可以在扩展范围 sRGB 颜色空间中表示这些值。

### <a name="device-pixel-formats"></a>设备像素格式 

SRGB 颜色空间很大程度上已被标准化上使用 8 位像素格式，因为每个颜色通道的 8 位主要是不足以描述中 sRGB 颜色。 这不是完美，但足够好，并显示图像的内存和处理器使用情况之间的良好权衡。

显示 P3 可以表示颜色的 sRGB 颜色空间以外的坐标，因为它要求每个颜色通道，以正确反映颜色且扩展范围 sRGB 颜色空间的 16 位。

## <a name="system-wide-wide-color-support"></a>系统范围广泛的颜色支持

若要完全支持广泛的颜色和宽整个范围内 iOS 10 和 macOS Sierra，Apple 已扩展才能充分利用扩展范围 sRGB 颜色空间和 Display P3 以下框架：

- UIKit （适用于仅限 iOS)
- SceneKit
- 核心图形
- ImageIO
- Core 映像
- WebKit
- SpriteKit
- 核心动画
- AppKit （适用于仅 macOS)

此外，支持已得到增强的扩展范围 sRGB 颜色空间的显示屏和显示 P3 显示。

广泛的颜色受支持，可在以下应用程序内容类型：

- 应用程序捆绑包中包括的静态图像资源。
- 文档和基于网络的图像资源。
- 高级的媒体，如 Live 照片或原始格式的图像。
- 3D 图形着色器纹理的图像。

## <a name="solving-the-color-problem"></a>颜色问题的解决

在应用中显示的内容可以来自各种颜色丰富源。 此外，可以广泛的范围内的设备，每个都有其自己范围的颜色显示功能显示此内容。

IOS 10 应用桥接这些两个问题，请使用新内置的系统范围内颜色管理系统之间的差异。 此系统可确保在任何 iOS 设备，无论其颜色空间中已编码的图像上图像的外观相同。

颜色管理开始，每个图像具有关联的颜色空间 （或颜色配置文件）。 在使用此信息_颜色匹配过程_源映像中的颜色与输出设备中的颜色的匹配位置。 在图中的每个像素必须是颜色匹配，因为它可以需要较长时间并在设备的 CPU 时有压力。

由于性质_颜色匹配过程_，此转换可以是可能会显示"丢失"，如果输出设备具有与源映像了较小类。

幸运的是，请转到的计算_颜色匹配过程_可以轻松地进行硬件加速 （无论是 CPU 还是 GPU 上） 和 Apple 可确保它通过支持构建到如 Quartz 2D 的基本系统会自动工作ColorSync 和核心动画。 有关正确标记的内容，无需进行编码充分利用这些功能。

颜色管理中受支持的每个平台，如下所示：

- **macOS** -macOS 进行了颜色从一开始就托管。
- **iOS** -iOS 具有 iOS 9.3 和更高版本起，支持自动颜色管理。

### <a name="designing-for-wide-gamut"></a>对宽 Gamut 设计

Apple 已设计的以下建议，并使用宽颜色、 iOS 和 macOS 应用程序中的宽 gamut 图像内容：

- 只能使用宽的所有内容在此处的品牌感应在应用中，它们不应会自动在所有位置。
- 只能使用宽 gamut 内容其中鲜艳颜色将增强用户体验。
- 在不必要的现有应用程序将所有内容都更改为 P3。

Apple 的工具链可以支持宽 gamut 映像内容的逐步选择中，以便在应用中支持广泛的颜色不是要么情况。

### <a name="upgrading-existing-content-to-wide-color"></a>将现有内容升级到广泛的颜色

Apple 已升级到广泛的颜色的现有映像内容的以下建议：

- 不只是"分配"P3 配置文件中图像编辑应用的内容。 执行此操作将只需重新映射到新的颜色空间出现意外的结果，如拉伸以适应新的空间，因此更改图像的颜色内容的现有颜色。
- 图像内容将需要将"转换"使用图像编辑应用程序的显示 P3 配置文件。

### <a name="file-formats-and-color-profiles"></a>文件格式和颜色配置文件

Apple 具有以下建议以获得的文件格式和应用程序的广泛的颜色图像内容中使用的颜色配置文件：

- 为 RGB 工作空间中使用的"Display P3"颜色配置文件。
- 使用 16 位每个颜色通道模式。
- 使用 Late 2015 iMac （或更高版本） 以准确地预览图像内容。
- 导出为 16 位 PNG 文件，具有嵌入的"Display P3"ICC 配置文件的图像资产。

> [!IMPORTANT]
> 使用**保存用于 Web**或**导出资产**功能最受欢迎的图像编辑软件中找到_将不会_都可以为广泛的颜色映像，因为这些功能尚未更新为尚未支持所需的文件格式规范。

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

## <a name="colors-in-user-interfaces"></a>在用户界面中的颜色

在使用用户界面中的颜色，屏幕上的像素大部分都是使用纯色。 此外，大部分这些像素不来自图像资产，但直接由应用程序 （或代表的应用程序的操作系统） 绘制。

当在 UI 级别上工作时，宽色域颜色可以提供几个难题：

- **通信颜色**-当谈到协调设计人员和开发人员通常会有颜色时_假定_sRGB 颜色空间涉及。 一种颜色可能作为传达`rgb(128, 45, 56)`或`#FF0456`。 在宽 gamut 设计中，这些表示形式未提供足够的信息来准确地表示指定的颜色，颜色空间的工作也必须包括。 Apple 建议使用`P3(128, 45, 56)`和`P3#FF0456`相反。 
- **选取颜色**-的大多数常用图像编辑和设计软件会使用其内置的颜色选取器时受到与 sRGB 颜色空间相同的限制。 在设计器应确保它们在颜色选取器中的"Display P3"颜色空间中使用广泛的颜色的设计时。
- **编码颜色**-这两个`NSColor`(macOS) 和`UIColor`（iOS 和 tvOS） 具有新的便捷方法，用于直接生成 P3 颜色和都已扩展为中扩展范围 sRGB 颜色空间也支持的颜色。
- **存储颜色**-应用程序的文档中的存储宽 gamut 颜色时，应格外谨慎。 其中每个颜色通道正常执行的 sRGB 颜色空间 8 位、 16 位每个颜色通道应用于宽颜色。 开发人员还需要监视假定颜色空间 （因为所有内容上过去一直仅 sRGB） 的实例。

## <a name="colors-on-the-web"></a>在 Web 上找到的颜色

使用广泛的颜色在 web pages 和支持广泛的颜色显示的设备上时应小心。 如果已包含在该网站的图像内容的所有已适当地标记，将自动颜色匹配 iOS 和 macOS 并将其正确地显示它们。

媒体查询也是可用于帮助解决 P3 和 sRGB、 支持的设备之间的资产：

```xml
<picture>
    <source srcset="monkey-p3.jpg" media="(color-gamut: p3)">
    <source srcset="monkey-rpg.jpg">
</picture>
```

Apple 还具有将允许 CSS 假定的 sRGB 空间除了其他颜色空间中进行指定一个易于使用的功能建议。

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

## <a name="summary"></a>总结

本文只讨论了广泛的颜色以及可能要执行的在 Xamarin.iOS 或 Xamarin.Mac 应用内使用的方法。



## <a name="related-links"></a>相关链接

- [iOS 10 示例](https://developer.xamarin.com/samples/ios/iOS10/)
