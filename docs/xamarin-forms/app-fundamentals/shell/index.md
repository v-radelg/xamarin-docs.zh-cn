---
title: Xamarin.Forms Shell
description: 本指南介绍如何使用 Xamarin.Forms Shell，Xamarin.Forms Shell 通过提供大多数应用程序所需的基本功能简化了 Xamarin.Forms 应用程序。
ms.prod: xamarin
ms.assetid: 85B322AA-808F-41B6-953A-5877264AE643
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/28/2019
ms.openlocfilehash: 20ac6ad748e7056f7f8037a73a95de66b9eae3b6
ms.sourcegitcommit: 5f972a757030a1f17f99177127b4b853816a1173
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/21/2019
ms.locfileid: "69888919"
---
# <a name="xamarinforms-shell"></a>Xamarin.Forms Shell

## <a name="introductionintroductionmd"></a>[介绍](introduction.md)

Xamarin.Forms Shell 简化了移动应用程序开发，方法是提供大多数移动应用程序所需的基本功能。 包括常见的导航用户体验、基于 URI 的导航方案，以及集成的搜索处理程序。

## <a name="create-a-xamarinforms-shell-applicationcreatemd"></a>[创建 Xamarin.Forms Shell 应用程序](create.md)

创建 Xamarin.Forms Shell 应用程序的过程包括创建可对 `Shell` 类进行子类化的 XAML 文件，将应用程序的 `App` 类的 `MainPage` 属性设置为子类化的 `Shell` 对象，然后描述子类化的 `Shell` 类中的应用程序的视觉对象层次结构。

## <a name="flyoutflyoutmd"></a>[浮出控件](flyout.md)

浮出控件是 Shell 应用程序的根菜单，可通过图标或从屏幕一侧轻扫进行访问。 浮出控件由可选标头、浮出控件项和可选菜单项组成。

## <a name="tabstabsmd"></a>[选项卡](tabs.md)

浮出控件后，Shell 应用程序中的下一级别导航为底部选项卡栏。 或者，应用程序的导航模式可以以底部选项卡开头且不使用浮出控件。 在两种情况下，当底部选项卡包含多个页面时，页面将可通过顶部选项卡导航。

## <a name="page-configurationconfigurationmd"></a>[页面配置](configuration.md)

`Shell` 类定义了可用于在 Xamarin.Forms Shell 应用程序中配置页面外观的附加属性。 这包括设置页面颜色、禁用导航栏、禁用选项卡栏以及在导航栏中显示视图。

## <a name="navigationnavigationmd"></a>[导航](navigation.md)

Shell 应用程序包括基于 URI 的导航方案：使用路由导航到应用程序中的任何页面，而无需遵循设置的导航层次结构。

## <a name="searchsearchmd"></a>[搜索](search.md)

Shell 应用程序可以使用搜索框提供的集成搜索功能，并且可将搜索框添加到各个页面的顶部。

## <a name="lifecyclelifecyclemd"></a>[生命周期](lifecycle.md)

Shell 应用程序遵循 Xamarin.Forms 生命周期，当页面将出现在屏幕上时，将引发 `Appearing` 事件，当页面将从屏幕上消失时，将引发 `Disappearing` 事件。

## <a name="custom-rendererscustomrenderersmd"></a>[自定义呈现器](customrenderers.md)

可通过各种 Shell 类公开的属性和方法对 Shell 应用程序进行高度自定义。 不过，在需要更为复杂的平台特定的自定义时，也可创建 Shell 自定义呈现器。
