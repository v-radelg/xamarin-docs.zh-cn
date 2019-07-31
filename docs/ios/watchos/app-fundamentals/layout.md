---
title: 使用 watchOS 在 Xamarin 中的布局
description: 本文档介绍如何创建使用 Xamarin 的 watchOS 布局。 它讨论界面控制器、 组、 分隔符和内容控件。
ms.prod: xamarin
ms.assetid: BEDB62A1-2249-4459-986F-413A41E63DF0
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: a714b8bd1984f22a138d09912a0a7fe1a51e857c
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2019
ms.locfileid: "68646807"
---
# <a name="working-with-watchos-layout-in-xamarin"></a>使用 watchOS 在 Xamarin 中的布局

设计布局的 Apple Watch[屏幕尺寸](~/ios/watchos/app-fundamentals/screen-sizes.md)了独特的挑战。

## <a name="design-tips"></a>设计提示

关键在于： 使您的用户界面，可读和小观看在屏幕上，使用大型手指易于使用。 别落入使用对设计的陷阱**iOS 模拟器**（将显示非常大） 和一个**鼠标指针**（适用于小触摸目标） ！

- 使用黑色背景-会创建带手表的黑色挡板效果较大的屏幕。

- 不执行填充屏幕布局周围-挡板窗体自然 visual 填充。

- 专注于提高可读性。 使用字体大小和颜色谨慎以确保是可读的文本。 使用内置的文本样式以获得自动动态类型支持。

![](layout-images/type.png "动态类型支持的示例")

- 专注于触摸目标大小。 与文本标签的按钮/tappable 表行应跨越整个屏幕。 Apple 说"永远不会将放在超过三个项并行"，并且如果你不使用图标和不文本标签。

- 使用[`Menu`控制](~/ios/watchos/user-interface/menu.md)到不经常使用的公开功能，使您的应用程序设计简洁明了。


## <a name="implementation"></a>实现

监视包包括以下控件以帮助您构建极具吸引力的监视应用程序布局：

### <a name="interface-controller"></a>界面控制器

`WKInterfaceController`是所有场景的基类。

界面控制器的设计图面的行为类似于垂直**组**： 可以将其他控件拖到界面控制器以及他们将自动布局的上述其他：

![](layout-images/controller-scene.png "控件是自动布局的上述其他")

可以设置**位置**并**大小**上每个控件来控制其外观的属性：

![](layout-images/positionsize-attributes.png "每个控件上设置的位置和大小属性")

如果大小设置为**相对于容器**可以提供比例值和偏移量的调整。 此屏幕截图显示了已设置为使用 80%的监视屏幕宽度的按钮 (**0.8**):

![](layout-images/button-attributes.png "提供比例值和偏移量的调整")


### <a name="group"></a>Group

`WKInterfaceGroup` 是可以配置为堆栈的简单布局容器控件垂直或水平。 它包括默认情况下，每个控件之间的间距，但您可以修改的间距 （和内边距） 中**属性**检查器。

![](layout-images/group-attributes.png "修改的间距和内边距属性检查器中")

组可以自己可调整大小和定位相对于围绕它们控件和组可以嵌套以创建复杂的布局。

![](layout-images/group-scene.png "组可以嵌套以创建复杂的布局")


### <a name="separator"></a>Separator

Separator 控件旨在帮助提供在布局中的可视指南。 使用分隔符 （或背景色或图像） 以帮助用户了解哪些内容相关联的屏幕上。

![](layout-images/separator-scene.png "分隔符用法的示例")

请注意不要使用整个屏幕宽度的蓝色和绿色分隔符所配置的任一**Fixed**或**相对于容器**大小。

### <a name="content-controls"></a>内容控件

没有布局都是不完整`Label`， `Image`， `Button`， `Switch`， `Slider`， `Map`，以及[其他控件](~/ios/watchos/user-interface/index.md)。
可以使用的布局中定位这些**组**或每个控件上的位置和大小设置。



## <a name="related-links"></a>相关链接

- [WatchKitCatalog （示例）](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog)
- [Apple 的布局参考](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/WatchHumanInterfaceGuidelines/Layout.html)
- [Apple 的颜色和版式引用](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/WatchHumanInterfaceGuidelines/ColorandTypography.html)
