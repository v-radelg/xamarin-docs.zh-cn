---
title: 使用 iOS 设计器构建用户界面
description: 本文档介绍如何使用 Xamarin 设计器适用于 iOS 生成应用的用户界面情节提要和.xib 文件。 它链接到文档的讨论工具的可用性，其基本功能，可设计的控件，并提供其用法的演练。
ms.prod: xamarin
ms.assetid: E35EFB69-EBBA-40E3-ADBE-CB8016F17127
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 05/31/2018
ms.openlocfilehash: 7c6529c539ab502fb6c13226acd18a57f8f6d58e
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61378660"
---
# <a name="building-user-interfaces-with-the-ios-designer"></a>使用 iOS 设计器构建用户界面

_用于 iOS 的 Xamarin 设计器是适用于 iOS 情节提要和 Interface Builder 格式适用于 Mac 和 Visual Studio 与 Visual Studio 完全集成的可视化设计器。IOS 设计器，以便可以在 Visual Studio for Mac 或 Visual Studio 除了 Xcode 的 Interface Builder 中编辑文件维护与情节提要和.xib 格式完全兼容。此外，在适用于 iOS 的 Xamarin 设计器支持高级的功能，例如在编辑器中的设计时呈现的自定义控件。_

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

[![iOS 设计器在 Visual Studio for Mac](images/designer-vsmac-sml.png "iOS 设计器")](images/designer-vsmac.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![iOS 设计器在 Visual Studio](images/designer-vs.png "iOS 设计器")](images/designer-vs.png#lightbox)

-----

## <a name="availability"></a>可用性

在 Visual Studio for Mac 和 Windows 上的 Visual Studio 2017 中提供了用于 iOS 的 Xamarin 设计器。

这些指南假定你熟悉中介绍的内容[Xamarin.iOS 快速入门将指导](~/ios/get-started/index.md)。

## <a name="ios-designer-basicsintroductionmd"></a>[iOS 设计器基本知识](introduction.md)

本指南介绍了 Xamarin iOS 设计器的功能。 它介绍了设计器基础知识，显示如何使用设计器以可视方式布置控件以及如何编辑属性。

## <a name="designable-controls-overviewios-designable-controls-overviewmd"></a>[可设计的控件概述](ios-designable-controls-overview.md)

在深度自定义控件，本指南介绍如何创建和哪些要求必须满足要在设计图面上呈现。 此外，它演示如何调试使用可设计的控件时可能发生的常见问题。

## <a name="walkthrough---using-custom-controls-with-ios-designerios-designable-controls-walkthroughmd"></a>[演练-使用 iOS 设计器中使用自定义控件](ios-designable-controls-walkthrough.md)

本文提供了分步演练说明如何创建自定义控件和 iOS 设计器中使用它。 它演示如何使控件在设计器的工具箱中可用，因此它可以拖动/放置到一个视图。 此外，它显示了如何实现控件，使其正确呈现在设计时和运行时，以及如何创建可以在设计时设置的属性。

## <a name="auto-layout-with-the-xamarin-ios-designerdesigner-auto-layoutmd"></a>[与 Xamarin iOS 设计器的自动布局](designer-auto-layout.md)

本指南介绍 iOS 自动布局和 iOS 设计器中可用的新约束工作流。
