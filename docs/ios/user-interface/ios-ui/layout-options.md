---
title: 在 Xamarin.iOS 中的布局选项
description: 本文档介绍不同的方式来布置在 Xamarin.iOS 中的用户界面。 它讨论了自动调整大小和自动布局。
ms.prod: xamarin
ms.assetid: D8180FEC-F300-42C0-B029-66803E0C1A5F
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: b35149028763691c17fe526673d023cc9b707c28
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61245974"
---
# <a name="layout-options-in-xamarinios"></a>在 Xamarin.iOS 中的布局选项

有两种不同机制，用于控制布局调整大小或旋转视图时：

-  **自动调整大小**– 在设计器中的自动调整大小检查器提供了一种方法来设置`AutoresizingMask`属性。 这将让控件锚定到其容器的边缘和/或修复它们的大小。 自动调整大小适用于 iOS 的所有版本。 这是下面更详细地描述
-  **自动布局**– 允许精细地控制用户界面控件的关系的 iOS 6 中引入的功能。 它将允许的位置相对于其他元素的元素的设计图面上的控件。 本主题中的更详细地介绍了[与 Xamarin iOS 设计器的自动布局](~/ios/user-interface/designer/designer-auto-layout.md)指南。

## <a name="autosizing"></a>自动调整大小

当用户调整窗口中的，如旋转设备时和方向更改，系统将自动调整大小根据其自动调整大小规则该窗口中的视图。 这些规则可以设置C#使用`AutoresizingMask`属性`UIView`中或在**Properties Pad**的 iOS 设计器中，如下图所示：

 [![](layout-options-images/image41.png "Visual Studio for Mac 设计器")](layout-options-images/image41.png#lightbox)

当控件被选定时，这使您可以手动指定的位置和尺寸控件，以及选择**自动调整大小**行为。 下面的屏幕截图中所示，我们可以使用弹簧和 struts 中自动调整大小控制到其父级的定义所选的视图的关系：

 [![](layout-options-images/image42.png "Visual Studio for Mac 设计器")](layout-options-images/image42.png#lightbox)

调整*spring*将导致要调整大小的视图的宽度或高度与其父视图的基础。 调整*strut*将使视图保持常量本身及其父视图，该特定的边缘之间的距离。

此外可以在代码中设置这些设置：

```csharp
textfield1.Frame = new RectangleF(15, 277, 79, 27);
textfield1.AutoresizingMask = UIViewAutoresizing.FlexibleRightMargin | UIViewAutoresizing.FlexibleBottomMargin;
```


若要测试自动调整大小设置，请启用不同**受支持设备方向**中项目的选项：

 [![](layout-options-images/image43a.png "自动调整大小设置")](layout-options-images/image43a.png#lightbox)

在代码隐藏中我们可以使用下面的代码，这会导致要水平调整大小的两个文本控件：

```csharp
textview1.AutoresizingMask = UIViewAutoresizing.FlexibleWidth;
textfield1.AutoresizingMask = UIViewAutoresizing.FlexibleWidth;
imageview1.AutoresizingMask = UIViewAutoresizing.FlexibleTopMargin | UIViewAutoresizing.FlexibleLeftMargin;
```


我们还可以调整使用设计器的控件。 如下面表现出选择 struts 将导致要保持而无需剪切掉视图底部的右对齐的图像：

 [![](layout-options-images/autoresize.png "Autorotation")](layout-options-images/autoresize.png#lightbox)

这些屏幕截图显示的控件如何调整大小或旋转屏幕时重新定位自身：

 [![](layout-options-images/image44a.png "Autorotation")](layout-options-images/image44a.png#lightbox)

请注意，文本视图和文本字段都拉伸以使保持不变的保留，并且由于右键边距，`FlexibleWidth`设置。 映像具有的顶部和左侧边距灵活，这意味着它保留底部和右边距 – 旋转屏幕时视图中保留该图像。 复杂的布局通常需要每个可见的控件上这些设置以使用户界面保持一致并防止控件重叠的视图的边界更改 （由于旋转或其他调整大小事件） 时的组合。





## <a name="related-links"></a>相关链接

- [控件 （示例）](https://developer.xamarin.com/samples/Controls/)
