---
title: 在 Xamarin 中使用 watchOS 屏幕大小
description: 本文档介绍如何使用各种 watchOS 屏幕大小。 其中介绍了 watchOS 接口设计器、watchOS 模拟器和映像资源。
ms.prod: xamarin
ms.assetid: 840DF939-2F59-4ABA-87D8-92AAC8A92BC4
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/17/2017
ms.openlocfilehash: e9c87b76dc6845962450b8cb6fab921ea1748832
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70768318"
---
# <a name="working-with-watchos-screen-sizes-in-xamarin"></a>在 Xamarin 中使用 watchOS 屏幕大小

Apple Watch 提供了两种屏幕大小：

- **38mm**
  - 136 x 170 逻辑像素（272 x 340 物理像素）

- **42mm**
  - 156 x 195 逻辑像素（312 x 390 物理像素）。

设计和测试应用时，应考虑屏幕大小。

## <a name="watchos-interface-designer"></a>watchOS 接口设计器

默认情况下，Visual Studio for Mac 设计器将在**任何 Apple Watch**显示 "监视" 界面控制器。

![](screen-sizes-images/screen-any-sml.png "设计器将在任何 Apple Watch 显示监视接口控制器")

使用 "大小" 菜单可在任一可用屏幕尺寸上编辑和预览情节提要：**38mm**或**42mm**：

![](screen-sizes-images/screen-menu-sml.png "选择38mm 或42mm 大小")

屏幕大小越大，有时会呈现在较小屏幕上被截断/隐藏的内容。
请确保对这两种大小进行测试。

### <a name="interface-design"></a>接口设计

应用应在屏幕上显示相同的内容，而不考虑大小，应根据需要展开或折叠元素。 在 Visual Studio for Mac 设计器中，在 "属性检查器" 中，应使用 "**相对于容器**" 或 "**大小" 来根据固定大小调整内容**的优先级。

![](screen-sizes-images/sizeattributepanel-sml.png "使用相对于容器或大小的内容，根据优先顺序调整大小")

由于手表屏幕环绕在黑色档板上，因此不建议在界面周围提供填充。 让元素在屏幕边缘上停留，并让档板形成应用周围的自然边框。

## <a name="watchos-simulator"></a>watchOS 模拟器

在模拟器上进行测试时，可以使用**硬件 > 设备**菜单在两个屏幕大小之间轻松切换。

![](screen-sizes-images/simulator.png "模拟器可以使用硬件设备菜单在两个屏幕大小之间切换")

## <a name="image-resources"></a>图像资源

如果单个资产在不同尺寸上看起来不佳，则应使用多个图像资产。 图像资产目录允许为每个大小指定单独的位图：

![](screen-sizes-images/images-xcassets.png "图像资产目录编辑器")

```csharp
// specify the asset name, the correct size will automatically be loaded
staticImage.SetImage(UIImage.FromBundle("Walkway"));
```

或者，使用代码来确定屏幕大小并完全加载不同的图像：

```csharp
bool large = WKInterfaceDevice.CurrentDevice.ScreenBounds.Size.Width > 136.0;
// Load image depending on screen size
using (var image = UIImage.FromBundle (large ? "42mm-Walkway" : "38mm-Walkway"))
{
   myImage.SetImage (image);

}
```

阅读有关使用[图像控件](~/ios/watchos/user-interface/image.md)的详细信息。

## <a name="related-links"></a>相关链接

- [watchOS 3 简介](~/ios/watchos/platform/introduction-to-watchos3/index.md)
