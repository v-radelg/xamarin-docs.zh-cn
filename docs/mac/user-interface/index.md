---
title: 在 Xamarin.Mac 中的 macOS 用户界面控件
description: 本文档所链接到描述各种用户界面控件可供 Xamarin.Mac 开发人员的指南。 链接的内容将介绍 windows、 对话框、 警报、 菜单、 工具栏、 表视图、 大纲视图和的详细信息。
ms.prod: xamarin
ms.assetid: 876B6EC2-E158-43F2-B9C9-03F54F3D2A49
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/27/2018
ms.openlocfilehash: a12553cf0b7b9584bb8ff7bc04ed326ad4a7ad2a
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61281592"
---
# <a name="macos-user-interface-controls-in-xamarinmac"></a>在 Xamarin.Mac 中的 macOS 用户界面控件

_此文章链接到指南描述各种 macOS UI 控件。_

如果在 Xamarin.Mac 应用程序中使用 C# 和.NET，则可以访问同一个用户界面控件的使用的开发人员*Objective C*并*Xcode* does。 由于 Xamarin.Mac 与 Xcode 直接集成，可以使用 Xcode 的_Interface Builder_创建和维护你的用户界面 （或选择通过 C# 代码中直接创建）。

下面列出的指南提供有关使用 macOS UI 元素中的 Xamarin.Mac 应用程序的详细的信息。 强烈建议您明确[Hello，Mac](~/mac/get-started/hello-mac.md)文章第一次，具体而言[Xcode 和 Interface Builder 简介](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder)并[输出口和操作](~/mac/get-started/hello-mac.md#outlets-and-actions)关键概念和技术，我们将使用在每篇文章中介绍了部分中的，因为它。

可能想要看一看[公开 C# 类 / 方法添加到 Objective C](~/mac/internals/how-it-works.md#exposing-c-classes--methods-to-objective-c)一部分[Xamarin.Mac 内部机制](~/mac/internals/how-it-works.md)文档，因为其中解释了`Register`和`Export`用来布置于 C# 类对 OBJECTIVE-C 对象和 UI 元素的属性。

## <a name="windowsmacuser-interfacewindowmd"></a>[Windows](~/mac/user-interface/window.md)

本文介绍如何使用 windows 和 Xamarin.Mac 应用程序中的面板。 它介绍了创建和维护 windows 和在 Xcode 和 Interface Builder，加载 windows 中的面板和面板从.storyboard 或.xib 文件、 使用 windows，以及响应 C# 代码中的 windows。

## <a name="dialogsmacuser-interfacedialogmd"></a>[对话框](~/mac/user-interface/dialog.md)

本文介绍如何使用对话框和模式窗口中的 Xamarin.Mac 应用程序。 它介绍如何创建和维护在 Xcode 和 Interface Builder，使用标准对话框，并显示以及响应 C# 代码中的 windows 中的模式窗口。

## <a name="alertsmacuser-interfacealertmd"></a>[警报](~/mac/user-interface/alert.md)

本文介绍如何使用 Xamarin.Mac 应用程序中的警报。 它介绍如何创建和显示 C# 代码中的警报和响应的警报。

## <a name="menusmacuser-interfacemenumd"></a>[菜单](~/mac/user-interface/menu.md)

中的 Mac 应用程序的用户界面的各个部分都会使用到菜单从应用程序的主菜单顶部的弹出菜单和可以出现在窗口中的任何位置的上下文菜单的屏幕。 菜单是构成 Mac 应用程序用户体验的一个组成部分。 本文介绍如何使用 Cocoa 菜单在 Xamarin.Mac 应用程序中。

## <a name="standard-controlsmacuser-interfacestandard-controlsmd"></a>[标准控件](~/mac/user-interface/standard-controls.md)

使用标准的 AppKit 控件，如按钮、 标签、 文本字段、 复选框和分段的控件中的 Xamarin.Mac 应用程序。 本指南介绍将它们添加到 Xcode 的 Interface Builder 中用户界面设计、 将它们公开给代码中通过输出口和操作，以及使用 C# 代码中的 AppKit 控件。

## <a name="toolbarsmacuser-interfacetoolbarmd"></a>[工具栏](~/mac/user-interface/toolbar.md)

本文介绍如何使用 Xamarin.Mac 应用程序中的工具栏。 它介绍如何创建和维护工具栏在 Xcode 和 Interface Builder 中的如何公开给代码使用输出口和操作、 启用和禁用工具栏项以及响应 C# 代码中的工具栏项的工具栏项。

## <a name="table-viewsmacuser-interfacetable-viewmd"></a>[表视图](~/mac/user-interface/table-view.md)

本文介绍如何使用 Xamarin.Mac 应用程序中的表视图。 它介绍如何创建和维护表视图在 Xcode 和 Interface Builder 中的如何使用输出口和操作的代码公开表视图项、 如何填充表视图，以及响应 C# 代码中的表视图项。

## <a name="outline-viewsmacuser-interfaceoutline-viewmd"></a>[大纲视图](~/mac/user-interface/outline-view.md)

本文介绍如何使用 Xamarin.Mac 应用程序中的大纲视图。 它介绍如何创建和维护大纲视图在 Xcode 和 Interface Builder 中的如何使用输出口和操作的代码公开大纲视图项、 填充大纲视图和响应概述查看 C# 代码中的项。

## <a name="source-listsmacuser-interfacesource-listmd"></a>[源列表](~/mac/user-interface/source-list.md)

本文介绍如何使用 Xamarin.Mac 应用程序中的源列表。 它介绍如何创建和维护源列表在 Xcode 和 Interface Builder 中的如何公开给代码使用输出口和操作、 如何填充源列表，以及响应 C# 代码中的源列表项的源列表项。

## <a name="collection-viewsmacuser-interfacecollection-viewmd"></a>[集合视图](~/mac/user-interface/collection-view.md)

本文介绍如何使用 Xamarin.Mac 应用程序中的集合视图。 它介绍如何创建和维护集合视图在 Xcode 和 Interface Builder 中的如何使用输出口和操作的代码公开集合视图项，填充集合视图，以及响应 C# 代码中的集合视图。

## <a name="creating-custom-controlsmacuser-interfacecustom-controlsmd"></a>[创建自定义控件](~/mac/user-interface/custom-controls.md)

本文介绍如何创建自定义用户界面控件 (通过继承`NSControl`)、 绘制自定义界面控件，以及创建可以在 Xcode 的 Interface Builder 的自定义操作。

## <a name="mac-samples-gallery"></a>Mac 示例库

我们还建议看一看[Mac 示例库](https://developer.xamarin.com/samples/mac/all/)。 它包括丰富的现成的代码可帮助你快速开始 Xamarin.Mac 项目。

## <a name="related-links"></a>相关链接

- [macOS 人机界面指南](https://developer.apple.com/macos/human-interface-guidelines/overview/themes/)
