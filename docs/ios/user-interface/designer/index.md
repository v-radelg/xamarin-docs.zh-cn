---
title: 在 iOS 设计器中生成用户界面
description: 本文档介绍如何使用 Xamarin Designer for iOS 通过情节提要和 xib 文件生成应用的用户界面。 它链接到介绍该工具的可用性、其基本功能和可设计控件的文档，并提供其用法的演练。
ms.prod: xamarin
ms.assetid: E35EFB69-EBBA-40E3-ADBE-CB8016F17127
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 05/31/2018
ms.openlocfilehash: 157e16da2c524029c29e767cd6b3e5eb550a2389
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73021753"
---
# <a name="building-user-interfaces-with-the-ios-designer"></a>在 iOS 设计器中生成用户界面

_Xamarin Designer for iOS 是 iOS 情节提要的可视化设计器和与 Visual Studio for Mac 和 Visual Studio 完全集成的 Interface Builder 格式。IOS 设计器与情节提要和 xib 格式保持完全兼容，因此除了 Xcode 的 Interface Builder 以外，还可以在 Visual Studio for Mac 或 Visual Studio 中编辑文件。此外，Xamarin Designer for iOS 还支持高级功能，如编辑器中设计时呈现的自定义控件。_

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

[![Visual Studio for Mac 中的 iOS 设计器](images/designer-vsmac-sml.png "IOS 设计器")](images/designer-vsmac.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Visual Studio 中的 iOS 设计器](images/designer-vs.png "IOS 设计器")](images/designer-vs.png#lightbox)

-----

## <a name="availability"></a>可用性

在 Windows 上的 Visual Studio 2017 Visual Studio for Mac 和中提供 Xamarin Designer for iOS。

这些指南假设你熟悉[Xamarin 入门指南](~/ios/get-started/index.md)中所述的内容。

## <a name="ios-designer-basicsintroductionmd"></a>[iOS 设计器基本知识](introduction.md)

本指南涵盖 Xamarin iOS 设计器的功能。 它介绍了设计器的基础知识，并演示了如何使用设计器直观地布局控件，以及如何编辑属性。

## <a name="designable-controls-overviewios-designable-controls-overviewmd"></a>[设计控件概述](ios-designable-controls-overview.md)

本指南深入探讨自定义控件、创建它们的方式以及在设计图面上呈现这些控件所必须满足的要求。 此外，它还演示了如何调试使用可设计的控件时可能出现的常见问题。

## <a name="walkthrough---using-custom-controls-with-ios-designerios-designable-controls-walkthroughmd"></a>[演练-使用 iOS 设计器的自定义控件](ios-designable-controls-walkthrough.md)

本文提供了分步演练，演示如何创建自定义控件并在 iOS 设计器中使用它。 它演示如何使控件在设计器的工具箱中可用，以便可以将其拖放到视图上。 此外，它还演示如何实现控件，使其在设计时和运行时正确呈现，以及如何创建可在设计时设置的属性。

## <a name="auto-layout-with-the-xamarin-ios-designerdesigner-auto-layoutmd"></a>[用 Xamarin iOS 设计器自动布局](designer-auto-layout.md)

本指南介绍 ios 自动布局和 iOS 设计器中提供的新约束工作流。
