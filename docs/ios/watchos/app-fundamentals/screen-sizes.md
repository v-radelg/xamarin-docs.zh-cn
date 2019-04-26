---
title: 使用 watchOS 在 Xamarin 中的屏幕大小
description: 本文档介绍如何使用各种 watchOS 屏幕大小。 它讨论了 watchOS 界面设计器中，watchOS 模拟器和图像资源。
ms.prod: xamarin
ms.assetid: 840DF939-2F59-4ABA-87D8-92AAC8A92BC4
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: b2f4cc71c1993e51ed55b51edd7c50d393e60873
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61412798"
---
# <a name="working-with-watchos-screen-sizes-in-xamarin"></a>使用 watchOS 在 Xamarin 中的屏幕大小

Apple Watch 现已推出两种屏幕大小：

- **38mm**
  - 第 136 x 170 逻辑像素为单位 （272 x 340 物理像素为单位）

- **42mm**
  - 156 x 195 逻辑像素 （312 x 390 物理像素为单位）。

您应考虑到设计和测试您的应用程序时的屏幕大小。

## <a name="watchos-interface-designer"></a>watchOS 界面设计器

默认情况下 Visual Studio for Mac 设计器将显示观看在界面控制器**Any Apple Watch**。

![](screen-sizes-images/screen-any-sml.png "设计器显示观看 Any Apple Watch 在界面控制器")

使用大小菜单来编辑和预览你在这两个可用的屏幕大小的情节提要：**38mm**或**42 mm**:

![](screen-sizes-images/screen-menu-sml.png "选择 38mm 或 42 mm 的大小")

较大的屏幕大小有时会呈现为较小屏幕上截断/隐藏的内容。
请确保在这两个大小上进行测试。


### <a name="interface-design"></a>接口设计

您的应用程序应相同的内容显示在屏幕上，而不考虑大小，并应扩展或收缩与相应的元素。 在 Visual Studio for Mac 设计器中，在属性检查器中，应使用**相对于容器**或**大小以适应内容**优先于固定大小。

![](screen-sizes-images/sizeattributepanel-sml.png "相对于容器或以适应内容的大小，请优先使用固定大小")

由于监视屏幕的黑色挡板周围，不建议提供围绕你的接口的填充。 让元素靠屏幕边缘，并让窗体应用程序周围的自然边框挡板。


## <a name="watchos-simulator"></a>watchOS 模拟器

当在模拟器上测试你可以轻松地切换之间使用的两个屏幕大小**硬件 > 设备**菜单。

![](screen-sizes-images/simulator.png "模拟器可以使用硬件设备菜单上的两个屏幕大小之间进行切换")


## <a name="image-resources"></a>图像资源

如果单个资产不会查找在不同的大小，则应使用多个图像资产。 图像资产目录允许单独的位图，以指定每种大小：

![](screen-sizes-images/images-xcassets.png "图像资产目录编辑器")

```csharp
// specify the asset name, the correct size will automatically be loaded
staticImage.SetImage(UIImage.FromBundle("Walkway"));
```

或者，使用代码来确定屏幕大小和完全加载不同的映像：

```csharp
bool large = WKInterfaceDevice.CurrentDevice.ScreenBounds.Size.Width > 136.0;
// Load image depending on screen size
using (var image = UIImage.FromBundle (large ? "42mm-Walkway" : "38mm-Walkway"))
{
   myImage.SetImage (image);

}
```

阅读有关使用的详细信息[图像控件](~/ios/watchos/user-interface/image.md)。



## <a name="related-links"></a>相关链接

- [watchOS 3 简介](~/ios/watchos/platform/introduction-to-watchos3/index.md)
