---
title: 在 Xamarin.Mac 中的情节提要简介
description: 本文介绍 Xamarin.Mac 应用中使用情节提要。 其中介绍了如何使用 Storyboard 和 Xcode 的 Interface Builder 创建和维护应用 UI。
ms.prod: xamarin
ms.assetid: F37BA503-0B25-489F-80A8-58C493291A55
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: 8a5a2f87c16a5dd040cefb2fbc615b01431ebcf5
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67832290"
---
# <a name="introduction-to-storyboards-in-xamarinmac"></a>在 Xamarin.Mac 中的情节提要简介

_本文介绍 Xamarin.Mac 应用中使用情节提要。它介绍如何创建和维护应用程序的 UI 使用 storyboard 和 Xcode 的 Interface Builder。_

情节提要，可开发用于不仅包括窗口定义和控制，但还包含不同的窗口之间的链接的 Xamarin.Mac 应用的用户界面 (通过 segue) 和视图状态。

[![](images/intro01.png "示例在 Xcode 中的 UI")](images/intro01.png#lightbox)

本文将提供使用情节提要来定义 Xamarin.Mac 应用的用户界面的简介。

<a name="What-are-Storyboards" />

## <a name="what-are-storyboards"></a>什么是情节提要？

使用演示图板，所有 Xamarin.Mac 应用的 UI，可以定义在单个位置所有用户界面及其各个元素之间导航。 适用于 Xamarin.Mac，情节提要中非常类似于情节提要用于 Xamarin.iOS。 但是，它们包含一组不同的_Segue 类型_由于不同的接口的惯用语言。

<a name="Working-with-Scenes" />

### <a name="working-with-scenes"></a>使用场景

如上面所述，情节提要定义所有分解的功能概述为给定应用程序的 UI 及其_视图控制器_。 在 Xcode 的 Interface Builder 中每个这些控制器位于其自己_场景_。

[![](images/intro02.png "示例视图控制器")](images/intro02.png#lightbox)

每个场景表示的给定的视图和视图控制器对具有一系列连接用户界面，因此显示它们之间的关系中每个场景的行 （称为 segues 设计）。 某些 segues 设计定义如何一个视图控制器包含一个或多个子视图或视图控制器。 其他 segues 设计，定义 （例如，显示一个弹出框或对话框的框） 的视图控制器之间的转换。 

[![](images/intro03.png "示例 segue")](images/intro03.png#lightbox)

需要注意的最重要一点是每个 Segue 表示某种形式的应用程序的 UI 的给定元素之间的数据的流。

<a name="Working-with-View-Controllers" />

### <a name="working-with-view-controllers"></a>使用视图控制器

视图控制器定义给定的视图中的 Mac 应用的信息和提供该信息的数据模型之间的关系。 在情节提要中的每个顶级场景都表示在 Xamarin.Mac 应用的代码中的一个视图控制器。

[![](images/intro04.png "示例又将视图控制器")](images/intro04.png#lightbox)

在这种方式，每个视图控制器是独立的、 可重用的配对的信息的可视表示形式 （视图） 和逻辑来提供和控制该信息。

可以在一个给定的场景中执行的所有内容将正常情况下已处理的单个`.xib`文件： 

- Subviews 和控制 （如按钮和文本框）。
- 定义元素的位置和自动布局限制。
- 绑定操作和输出口公开到代码的 UI 元素。

<a name="Working-with-Segues" />

### <a name="working-with-segues"></a>使用 Segue

如上面所述，segues 设计提供所有定义应用程序的 UI 的场景之间的关系。 如果你熟悉在情节提要中在 iOS 中工作，您知道 Segue 的 iOS 通常定义全屏视图之间的转换。 这不同于 macOS、 segues 设计通常定义的"包容"（其中一个场景是父级场景的子级） 时。

在 macOS，大多数应用程序往往组一起使用 UI 元素，例如拆分视图和选项卡相同时段内的其视图。 与 iOS 不同，其中视图需要打开和关闭屏幕过渡，由于有限物理显示空间。

给定包含向 macOS 的倾向，有些情况下， _Presentation Segue_使用，如模式 Windows、 表视图和 Popovers。

如果使用演示文稿 Segue，则可以覆盖`PrepareForSegue`的演示文稿来初始化和变量的父视图控制器方法并提供到显示视图控制器的任何数据。

<a name="Design-and-Run-Times" />

### <a name="design-and-run-times"></a>设计和运行的时间

在设计时 (当在 Xcode 的 Interface Builder ui 布局)，应用程序的 UI 的每个元素分解为构成项：

- **场景**-它是组成：
    - **查看控制器**-用于定义视图和支持它们的数据之间的关系。
    - **视图和子视图**-组成用户界面的实际元素。
    - **包含 segues 设计**-定义场景之间的父-子关系。
- **演示文稿 segues 设计**-定义不同的演示文稿模式。 

通过以这种方式定义的每个元素，它允许进行延迟加载，每个元素仅需要在运行时。 在 macOS 中的整个过程旨在使开发人员能够创建复杂、 灵活的用户界面要求最低的支持代码，以使它们运行，同时尽可能正在与系统资源的效率。

<a name="Storyboard-Quick-Start" />

## <a name="storyboard-quick-start"></a>情节提要快速入门

在中[情节提要快速启动](~/mac/platform/storyboards/quickstart.md)指南中，我们将创建一个简单的 Xamarin.Mac 应用介绍在使用情节提要来创建用户界面的关键概念。 示例应用程序将由一个分割视图，其中包含_内容区域_和一个_检查器区域_和它将显示一个简单的首选项对话框窗口。 我们将使用 segues 设计将所有用户界面元素绑定在一起。

<a name="Working-with-Storyboards" />

## <a name="working-with-storyboards"></a>使用情节提要

本部分介绍的深入详细信息[使用情节提要](~/mac/platform/storyboards/indepth.md)Xamarin.Mac 应用中。 我们需要深入了解场景，以及如何查看控制器和视图组成。 然后，我们将查看场景 segues 设计以及相关联的方式。 最后，我们将了解如何使用自定义的 Segue 类型。 

<a name="Complex-Storyboard-Example" />

## <a name="complex-storyboard-example"></a>复杂的情节提要示例

有关 Xamarin.Mac 应用中使用情节提要的一个复杂的示例的示例，请参阅[Sourcewriter 示例](https://developer.xamarin.com/samples/mac/SourceWriter/)。 SourceWriter 是一个非常简单的源代码编辑器，提供代码补全和简单语法突出显示支持。

SourceWriter 代码已经完全注释，且在可用时，提供了相关链接，链接涵盖了从关键技术或方法到 Xamarin.Mac 指南文档中的相关信息。

<a name="Summary" />

## <a name="summary"></a>总结

本文已快速浏览一下在 Xamarin.Mac 应用中使用情节提要。 我们了解了如何创建新的应用使用演示图板以及如何定义用户界面。 我们还了解到如何在不同的窗口之间导航和使用的视图状态转入。


## <a name="related-links"></a>相关链接

- [Hello，Mac（示例）](https://developer.xamarin.com/samples/mac/Hello_Mac/)
- [了解 Mac](~/mac/get-started/hello-mac.md)
- [使用 Windows](~/mac/user-interface/window.md)
- [OS X 人机界面指南](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
- [Windows 简介](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1)
