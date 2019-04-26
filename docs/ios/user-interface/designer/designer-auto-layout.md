---
title: 适用于 iOS 的 Xamarin 设计器的自动布局
description: 本指南介绍 iOS 自动布局并介绍如何使用适用于 iOS 的 Xamarin 设计器来创建和编辑布局使用约束。 它还介绍了在代码中，播放动画更改限制，和的详细信息修改约束。
ms.prod: xamarin
ms.assetid: CAC7A715-55BB-45E2-BB6D-2168D36D428F
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: 41a9ec90b4b734dde7a982ac3d4b2e7b2082321c
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61412972"
---
# <a name="auto-layout-with-the-xamarin-designer-for-ios"></a>适用于 iOS 的 Xamarin 设计器的自动布局

自动布局 （也称为"自适应布局"） 是响应式设计方法。 自动布局有关与过渡布局系统，其中每个元素的位置是硬编码到屏幕上的点，不同的是*关系*-相对于设计图面上其他元素的元素的位置。 自动布局的核心是元素的约束或规则，用于在屏幕上的其他元素的上下文中定义位置或组的元素。 由于元素不依赖于在屏幕上的特定位置，约束来帮助创建不同屏幕大小和设备方向上的自适应布局。

在本指南中，我们将介绍约束以及如何在 Xamarin iOS 设计器中使用它们。 本指南不涉及以编程方式使用约束。 有关以编程方式使用自动布局的信息，请参阅[Apple 文档](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/AutolayoutPG/ProgrammaticallyCreatingConstraints.html)。

## <a name="requirements"></a>要求

用于 iOS 的 Xamarin 设计器是在 Visual Studio for Mac 在 Visual Studio 2017 和更高版本在 Windows 中可用。

本指南假定你了解从设计器的组件[iOS 设计器简介](~/ios/user-interface/designer/introduction.md)指南。

## <a name="introduction-to-constraints"></a>约束简介

约束是关系的在屏幕上的两个元素之间的数学表示法。 表示一个用户界面元素的位置，作为一种数学关系解决了与硬编码的 UI 元素的位置相关联的几个问题。 例如，如果我们将从屏幕的底部按钮 20px 放在纵向模式下，该按钮的位置将在横向模式下在屏幕上。 若要避免此问题，我们无法设置放置从视图的底部按钮 20px 的下边缘的约束。 作为将进行计算的位置的按钮边缘*button.bottom = view.bottom-20px*，这会将从视图的底部按钮 20px 置于在纵向和横向模式下。 计算放置基于数学关系的功能就是约束如此有用在 UI 设计。

当我们设置了约束时，我们会创建`NSLayoutConstraint`对象将作为参数，要约束的对象和属性，该对象或*属性*，该约束将在发挥作用。 在 iOS 设计器中，属性包括边缘如下所述*左*，*右*，*顶部*，并*底部*的元素。 它们还包括大小属性如下所述*高度*并*宽度*，和中心点位置， *centerX*并*centerY*。 例如，当我们添加的两个按钮的左边界位置的约束时，在设计器正在生成在后台的以下代码：

```csharp
View.AddConstraint (NSLayoutConstraint.Create (Button1, NSLayoutAttribute.Left, NSLayoutRelation.Equal, Button2, NSLayoutAttribute.Left, 1, 10));
```

下一节介绍如何使用约束使用 iOS 设计器中，包括启用和禁用自动布局和使用约束工具栏。

## <a name="enable-auto-layout"></a>启用自动布局

默认 iOS 设计器配置已启用的约束模式。 但是，如果需要以启用或禁用该手动，您可以在两个步骤的操作：

1.  单击设计图面上的空白空间。 这即取消选择任何元素，并引出了情节提要文档的属性。
1.  选中或取消选中**使用自动布局**属性面板中的复选框：

    ![](designer-auto-layout-images/image01.png "属性面板中的使用自动布局复选框")


默认情况下，没有约束为创建或在图面上可见。 相反，它们会自动从推断出在编译时的帧信息。 若要添加约束，我们需要选择设计图面上的元素并向其添加约束。 我们可以执行操作，使用**约束工具栏**。

## <a name="constraints-toolbar"></a>约束工具栏

 [![](designer-auto-layout-images/toolbarnew.png "上下文菜单命令")](designer-auto-layout-images/toolbarnew.png#lightbox)

约束工具栏已更新，并且现在包含两个主要部分：

- **约束模式按钮切换**:以前，通过再次单击设计图面上的所选视图进入约束模式。 现在，您将在约束栏中使用此切换按钮：

  ![约束模式切换](designer-auto-layout-images/constraints.png)

- **"更新约束"按钮：** 请务必注意，具体取决于更改要在编辑模式的约束。
  - 约束编辑模式下此按钮调整要匹配的元素帧的约束。
  - 帧编辑模式中此按钮调整元素帧以匹配定义约束的位置。


## <a name="surface-based-constraint-editing"></a>编辑基于 surface 的约束

在上一节中，我们了解添加默认约束和删除约束使用约束工具栏。 有关更多的细微调整的约束编辑，我们可以与直接在设计图面上的约束进行交互。 本部分介绍基础知识的编辑图面基于约束，包括固定间距控件、 拖放区域和使用不同类型的约束。

### <a name="creating-constraints"></a>在创建约束

IOS 设计器工具提供用于操作设计图面上的元素的控件的两种类型。 *将控件拖*并*pin 间距的控件*，在下图中所示：

![视图控件](designer-auto-layout-images/controls.png)

这些是由约束栏中选择约束模式按钮进行切换。

4 形 T 元素的每一侧句柄定义*顶部*，*右*，*底部*，并*左*边缘的元素约束。 定义两个 I 形右侧和底部的元素句柄*高度*并*宽度*约束分别。 中间的正方形处理这两*centerX*并*centerY*约束。

若要创建的约束，选取一个句柄并拖动到设计图面上的某个位置。 开始拖动时，一系列绿色行/框将出现在告知你的面可以约束。 例如，下面的屏幕截图，我们要约束的顶边中间的按钮：

 [![](designer-auto-layout-images/image07.png "约束顶边的中间的按钮")](designer-auto-layout-images/image07.png#lightbox)

请注意跨其他两个按钮的三个绿色虚线。 绿线表示*拖放区域*，或我们可以约束到的其他元素的属性。 在上面的屏幕截图，其他两个按钮提供 3 个垂直放置区域 (*底部*， *centerY*，*顶部*) 来限制我们的按钮。 在视图顶部的绿色虚线表示视图控制器提供了在视图顶部的约束，稳定的绿色框表示视图控制器提供了以下顶部布局参考线的约束。

> [!IMPORTANT]
> 布局参考线是特殊类型的约束目标，使我们能够创建顶部和底部约束，请考虑存在系统条，如状态栏或工具栏。 主要用途之一是具有兼容 iOS 6 和 iOS 7 之间的应用，因为最新版本具有状态栏下方的容器视图。 顶部布局参考线的详细信息，请参阅[Apple 文档](https://developer.apple.com/library/ios/documentation/userexperience/conceptual/transitionguide/AppearanceCustomization.html#//apple_ref/doc/uid/TP40013174-CH15-SW2)。



接下来的三部分介绍如何使用不同类型的约束。

### <a name="size-constraints"></a>大小限制

有大小限制-*高度*并*宽度*-有两个选项。 第一个选项是拖动手柄以限制到相邻元素大小，如上面的示例所示。 另一个选项是双击要创建自助约束的句柄。 这使得我们可以指定常量大小值，如下面的屏幕截图所示：

 [![](designer-auto-layout-images/sizec.png "拖动手柄以限制到相邻元素大小，如下图所示")](designer-auto-layout-images/sizec.png#lightbox)

### <a name="center-constraints"></a>Center 约束

将创建正方形的句柄*centerX*或*centerY*约束，具体取决于上下文。 拖动正方形的句柄将亮起的其他元素提供这两个垂直和水平放置区域，如下面的屏幕截图所示：

 [![](designer-auto-layout-images/centerc.png "Center 约束")](designer-auto-layout-images/centerc.png#lightbox)

如果选择垂直拖放区域， *centerY*将创建约束。 如果您选择水平放置区域，该约束将根据*centerX*。

### <a name="combinational-constraints"></a>大体约束

若要创建对齐方式和两个元素之间的大小相等约束，可以选择项从指定的顺序的水平对齐方式、 垂直对齐方式和大小不如顶部工具栏，如下面的屏幕截图所示：

 [![](designer-auto-layout-images/image06.png "大体约束")](designer-auto-layout-images/image06.png#lightbox)

### <a name="visualizing-and-editing-constraints"></a>可视化和编辑约束

时添加约束时，它将显示在设计图面上为一条蓝线时选择一项：

 [![](designer-auto-layout-images/image09.png "可视化约束")](designer-auto-layout-images/image09.png#lightbox)

可以通过单击一条蓝线和编辑约束的值直接在属性面板中选择一个约束。 或者，双击一条蓝线将显示弹出框，使你能够编辑直接在设计图面上的值：

 [![](designer-auto-layout-images/image08.png "编辑约束")](designer-auto-layout-images/image08.png#lightbox)

## <a name="constraint-issues"></a>约束问题

使用约束时，可能会出现多种类型的问题：

-  **冲突的约束**-多个约束强制该元素具有冲突的属性的值和约束引擎不能对帐它们时将发生这种情况。
-  **Underconstrained 项**-必须由其组的约束和有效的约束的内部大小完全覆盖元素的属性 （位置 + 大小）。 如果这些值不明确，项则称其为 underconstrained。
-  **帧遗失而导致**— 当元素的帧和其组的约束定义两个不同的生成矩形时将发生这种情况。


本部分，上面列出的三个问题上将详细介绍并提供有关如何处理它们的详细信息。

### <a name="conflicting-constraints"></a>冲突的约束

冲突的约束标记为红色，并有一个警告符号。 将鼠标悬停警告符号上会显示弹出框与冲突有关的信息：

 [![](designer-auto-layout-images/image11.png "冲突的约束警告")](designer-auto-layout-images/image11.png#lightbox)

### <a name="underconstrained-items"></a>Underconstrained 的项

Underconstrained 的项显示为橙色，并触发视图控制器对象栏中的橙色标记图标的外观：

 [![](designer-auto-layout-images/image02.png "Underconstrained 的项显示为橙色")](designer-auto-layout-images/image02.png#lightbox)

如果单击该标记图标，可以在场景中获取 underconstrained 项有关的信息并解决问题，通过任一完全限定它们或通过删除其约束，如下面的屏幕截图所示：

 [![](designer-auto-layout-images/image10.png "修复 Underconstrained 的项")](designer-auto-layout-images/image10.png#lightbox)

### <a name="frame-misplacement"></a>帧遗失而导致

帧遗失而导致作为 underconstrained 项使用相同的颜色代码。 将始终使用其本机帧中，在图面上呈现的项，但在帧遗失而导致的情况下一个红色矩形将标记项将的结束位置运行程序时，如下面的屏幕截图所示：

 [![](designer-auto-layout-images/image05.png "示例框架遗失而导致视图")](designer-auto-layout-images/image05.png#lightbox)

若要解决帧遗失而导致错误，请选择**约束更新帧基础**约束工具栏 （最右侧按钮） 中的按钮：

 [![](designer-auto-layout-images/image03.png "更新帧根据约束工具栏按钮")](designer-auto-layout-images/image03.png#lightbox)

这将自动调整元素帧以匹配由控件定义的位置。

<a name="modifying-in-code" />

## <a name="modifying-constraints-in-code"></a>修改代码中的约束

根据您的应用程序的要求，有可能，您需要修改代码中的约束。 例如，重设大小或重新定位视图约束附加到，若要更改约束的优先级或完全停用一个约束。

若要访问代码中的约束，首先需要将其公开 iOS 设计器中，通过执行以下操作：

1. 创建约束作为正常 （使用任何上面列出的方法）。
2. 在中**文档大纲资源管理器**，查找所需的约束并进行选择：

    [![](designer-auto-layout-images/modify01.png "文档大纲资源管理器")](designer-auto-layout-images/modify01.png#lightbox)
3. 接下来，将分配**名称**到中的约束**小组件**选项卡**属性资源管理器**:

    [![](designer-auto-layout-images/modify02.png "小组件选项卡")](designer-auto-layout-images/modify02.png#lightbox)
4. 保存更改。

与上述更改后，可以访问在代码中的约束，并修改其属性。 例如，可以使用以下设置附加的视图为零的高度：

```csharp
ViewInfoHeight.Constant = 0;
```

IOS 设计器中提供以下设置的约束：

[![](designer-auto-layout-images/modify03.png "编辑属性资源管理器中的约束")](designer-auto-layout-images/modify03.png#lightbox)

### <a name="the-deferred-layout-pass"></a>延迟的布局处理过程

而不是立即更新更改限制到响应中的附加的视图，自动布局引擎将安排_推迟布局处理过程_为不久的将来。 在此延迟传递期间不只是给定的视图的约束更新，约束为层次结构中的每个视图都重新计算，更新以进行调整的新布局。

在任何时候，可以计划已推迟布局过程通过调用`SetNeedsLayout`或`SetNeedsUpdateConstraints`父视图的方法。 

已推迟布局处理过程包括两个通过的视图层次结构的唯一传递：

- **更新 Pass** -在此阶段中，自动布局引擎遍历的视图层次结构，并调用`UpdateViewConstraints`所有视图控制器上的方法和`UpdateConstraints`上的所有视图的方法。
- **在布局传递**-再次自动布局引擎遍历视图层次结构，但这一次调用`ViewWillLayoutSubviews`上的所有视图控制器方法和`LayoutSubviews`方法上的所有视图。 `LayoutSubviews`方法将更新`Frame`自动布局引擎计算的矩形，每个子视图的属性。

### <a name="animating-constraint-changes"></a>约束更改进行动画处理

还可以修改约束属性，可以使用核心动画进行动画处理对视图的约束的更改。 例如：

```csharp
UIView.BeginAnimations("OpenInfo");
UIView.SetAnimationDuration(1.0f);
ViewInfoHeight.Constant = 237;
View.LayoutIfNeeded();

//Execute Animation
UIView.CommitAnimations();
```

此处的关键调用`LayoutIfNeeded`动画块内部的父视图的方法。 这将告知要绘制动画的位置或大小的更改的每个"frame"的视图。 没有此行，视图会快速跳到最后一个版本而无需进行动画处理。

## <a name="summary"></a>总结

本指南介绍了 iOS 自动 （或"adaptive"） 布局和概念的约束，以在设计图面上的元素之间的关系的数学表示形式。 它介绍了如何在使用 iOS 设计器启用自动布局**约束工具栏**，和编辑单独在设计图面上的约束。 接下来，它介绍了如何对三个常见的约束问题进行故障排除。 最后，它介绍了如何修改代码中的约束。

## <a name="related-links"></a>相关链接

- [情节提要简介](~/ios/user-interface/storyboards/index.md)
- [iOS 可设计的控件演练](~/ios/user-interface/designer/ios-designable-controls-walkthrough.md)
- [Android 设计器概述](~/android/user-interface/android-designer/index.md)
- [以编程方式的约束](~/ios/user-interface/programmatic-layout-constraints.md)
- [Apple 的自动布局参考线](https://developer.apple.com/library/ios/documentation/userexperience/conceptual/AutolayoutPG/Introduction/Introduction.html#/apple_ref/doc/uid/TP40010853-CH13-SW1)
