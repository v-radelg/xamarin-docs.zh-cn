---
title: 用 Xamarin 构建用户界面
description: 本文档介绍如何在 Xamarin iOS 应用程序中生成用户界面。 它提供有关 iOS 设计器、演示图板、常规 iOS 界面概念和 iOS 用户界面控件的指南的链接。
ms.prod: xamarin
ms.assetid: 2B3E45FA-C30F-D708-0E8F-3EE02BD1A867
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 06/21/2017
ms.openlocfilehash: a749324c2963290c45b0e003af1518d1d517b30d
ms.sourcegitcommit: dde593cf9dedf4a056ffef86bcf2fa0640412a4d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/28/2020
ms.locfileid: "76794676"
---
# <a name="building-user-interfaces-with-xamarinios"></a>用 Xamarin 构建用户界面

## <a name="introduction-to-storyboardsiosuser-interfacestoryboardsindexmd"></a>[情节提要简介](~/ios/user-interface/storyboards/index.md)

情节提要是应用程序外观和流的直观表示形式。 Xamarin 引入了一个设计器，使 Xamarin iOS 应用程序能够利用演示图板，因此，您可以直观地设计应用程序屏幕，并使用C#来访问视图、控制器和 segue 以获得更多控制。 你还可以参考[本指南](~/ios/user-interface/designer/introduction.md)来了解 Xamarin Designer for iOS 的说明和演练

## <a name="ios-designeriosuser-interfacedesignerindexmd"></a>[iOS 设计器](~/ios/user-interface/designer/index.md)

我们为 iOS 情节提要格式生成了一个已完全集成到 Visual Studio for Mac 中的设计器。 IOS 设计器会保持与情节提要格式的完全兼容性，以便可以在 Xcode 或 Visual Studio for Mac 中编辑文件。 此外，编辑器还支持高级功能，如编辑器中设计时呈现的自定义控件。

## <a name="user-interface-in-iosiosuser-interfaceios-uiindexmd"></a>[iOS 中的用户界面](~/ios/user-interface/ios-ui/index.md)

介绍如何在 Xamarin iOS 应用程序中使用 iOS 用户界面，包括：外观 API、创建用户界面对象、布局选项、提供 Haptic 反馈和使用 UI 线程。

## <a name="user-interface-controlsiosuser-interfacecontrolsindexmd"></a>[用户界面控件](~/ios/user-interface/controls/index.md)

Xamarin 公开了 Apple 提供的所有本机用户界面对象。 使用 iOS 设计器（Xcode 的 Interface Builder 或以编程方式），可以轻松地将其添加到 Xamarin iOS 应用程序中。 无论选择哪种方法，Xamarin 都将公开中C#的所有用户界面对象属性和方法。
