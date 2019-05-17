---
title: Xamarin.Forms Shell
description: 本指南介绍如何使用 Xamarin.Forms Shell，Xamarin.Forms Shell 通过提供大多数应用程序所需的基本功能简化了 Xamarin.Forms 应用程序。
ms.prod: xamarin
ms.assetid: 85B322AA-808F-41B6-953A-5877264AE643
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/06/2019
ms.openlocfilehash: 7699b39a6df6c64ae9a481d9171f23dc6a8eba57
ms.sourcegitcommit: 9d90a26cbe13ebd106f55ba4a5445f28d9c18a1a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2019
ms.locfileid: "65054187"
---
# <a name="xamarinforms-shell"></a>Xamarin.Forms Shell

![](~/media/shared/preview.png "此 API 当前为预发布版本")

## <a name="introductionintroductionmd"></a>[介绍](introduction.md)

Xamarin.Forms Shell 简化了移动应用程序开发，方法是提供大多数移动应用程序所需的基本功能。 包括常见的导航用户体验、基于 URI 的导航方案，以及集成的搜索处理程序。

## <a name="flyoutflyoutmd"></a>[浮出控件](flyout.md)

浮出控件是 Shell 应用程序的根菜单，可通过图标或从屏幕一侧轻扫进行访问。 浮出控件由可选标头、浮出控件项和可选菜单项组成。

## <a name="tabstabsmd"></a>[选项卡](tabs.md)

浮出控件后，Shell 应用程序中的下一级别导航为底部选项卡栏。 当一个选项卡包含多个页面时，页面将可通过顶部选项卡导航。

## <a name="navigationnavigationmd"></a>[导航](navigation.md)

Shell 应用程序包括基于 URI 的导航方案：使用路由导航到应用程序中的任何页面，而无需遵循设置的导航层次结构。

## <a name="searchsearchmd"></a>[搜索](search.md)

Shell 应用程序可以使用搜索框提供的集成搜索功能，并且可将搜索框添加到各个页面的顶部。

## <a name="custom-rendererscustomrenderersmd"></a>[自定义呈现器](customrenderers.md)

可通过各种 Shell 类公开的属性和方法对 Shell 应用程序进行高度自定义。 不过，在需要更为复杂的平台特定的自定义时，也可创建 Shell 自定义呈现器。
