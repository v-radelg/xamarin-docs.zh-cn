---
title: iOS 中的用户界面
description: 本文档链接到介绍如何在 Xamarin iOS 应用程序中生成用户界面的指南。 链接指南涵盖外观 API、创建用户界面对象、布局选项等。
ms.prod: xamarin
ms.assetid: 1BB46561-F503-491E-A27C-7878E7EBE00B
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 06/14/2017
ms.openlocfilehash: 106cadc6fef43c06597cf1e3b846bd48cecb6fa5
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2019
ms.locfileid: "70287054"
---
# <a name="user-interfaces-in-ios"></a>iOS 中的用户界面

## <a name="appearance-apiintroduction-to-the-appearance-apimd"></a>[外观 API](introduction-to-the-appearance-api.md)

iOS 允许使用 UIAppearance Api 为用户界面控件的多个视觉对象属性使用主题。

## <a name="creating-user-interface-objectsiosuser-interfaceios-uicreating-ui-objectsmd"></a>[创建用户界面对象](~/ios/user-interface/ios-ui/creating-ui-objects.md)

Apple 将相关功能部分分组到与 Xamarin 命名空间相同的 "框架" 中。 `UIKit`命名空间，包含适用于 iOS 的所有用户界面控件。

## <a name="layout-optionsiosuser-interfaceios-uilayout-optionsmd"></a>[布局选项](~/ios/user-interface/ios-ui/layout-options.md)

调整视图的大小或旋转时，有两种不同的机制来控制布局：自动调整和自动版式。

## <a name="providing-haptic-feedbackiosuser-interfaceios-uihaptic-feedbackmd"></a>[提供 Haptic 反馈](~/ios/user-interface/ios-ui/haptic-feedback.md)

本文介绍了 iOS 10 中可用的新类型的 haptic 反馈，以及如何在 Xamarin 中实现它们。

## <a name="working-with-the-ui-threadiosuser-interfaceios-uiui-threadmd"></a>[使用 UI 线程](~/ios/user-interface/ios-ui/ui-thread.md)

你的代码只应从主（或 UI）线程对用户界面控件进行更改。 在不同线程（如回调或后台线程）上发生的任何 UI 更新可能无法呈现到屏幕上，甚至可能会导致崩溃。




