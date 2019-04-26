---
title: iOS 中的用户界面
description: 本文档所链接到介绍如何构建用户界面中的 Xamarin.iOS 应用程序的指南。 链接的指南介绍外观 API，创建用户界面对象、 布局选项和的详细信息。
ms.prod: xamarin
ms.assetid: 1BB46561-F503-491E-A27C-7878E7EBE00B
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 06/14/2017
ms.openlocfilehash: efb88ada8a4b4c36dd49de137eb64acd63552968
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61382331"
---
# <a name="user-interfaces-in-ios"></a>iOS 中的用户界面

## <a name="appearance-apiintroduction-to-the-appearance-apimd"></a>[外观 API](introduction-to-the-appearance-api.md)

iOS 允许用户界面控件主题化使用 UIAppearance Api 的许多可视的特性。

## <a name="creating-user-interface-objectsiosuser-interfaceios-uicreating-ui-objectsmd"></a>[创建用户界面对象](~/ios/user-interface/ios-ui/creating-ui-objects.md)

Apple 相关的片段组合到"框架"等同于 Xamarin.iOS 的命名空间的功能。 `UIKit` 是包含适用于 iOS 的所有用户界面控件的命名空间。

## <a name="layout-optionsiosuser-interfaceios-uilayout-optionsmd"></a>[布局选项](~/ios/user-interface/ios-ui/layout-options.md)

有两种不同机制，用于控制布局调整大小或旋转视图时：自动调整大小和自动布局。

## <a name="providing-haptic-feedbackiosuser-interfaceios-uihaptic-feedbackmd"></a>[提供 Haptic 反馈](~/ios/user-interface/ios-ui/haptic-feedback.md)

本文介绍如何在 iOS 10 及如何在 Xamarin.iOS 中实现这些可用 haptic 反馈的新类型。

## <a name="working-with-the-ui-threadiosuser-interfaceios-uiui-threadmd"></a>[使用 UI 线程](~/ios/user-interface/ios-ui/ui-thread.md)

你的代码仅需更改用户界面控件的主 （或 UI） 线程。 （例如回调或后台线程中） 在不同线程发生的任何 UI 更新不获取呈现到屏幕，或甚至可能导致崩溃。




