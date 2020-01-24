---
title: Android 平台功能
description: This article explains how to add Android-specific functionality to Xamarin.Forms applications.
ms.prod: xamarin
ms.assetid: E24168F3-0138-4814-86EA-B467F6B8A545
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/11/2019
ms.openlocfilehash: ce8d0b834cff5b2eee46b4ace5de4a95d196726d
ms.sourcegitcommit: a3b7e016fb25584dbf57bae89b64a9f98031e7c9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2020
ms.locfileid: "76549992"
---
# <a name="android-platform-features"></a>Android 平台功能

Developing Xamarin.Forms applications for Android requires Visual Studio. The [supported platforms page](~/get-started/supported-platforms.md) contains more information about the pre-requisites.

## <a name="platform-specifics"></a>Platform-specifics

平台特定信息，可使用的功能仅适用于特定的平台，而无需实现自定义呈现器或效果。

The following platform-specific functionality is provided for Xamarin.Forms views, pages, and layouts on Android:

- 控制 Z 顺序的可视元素来确定绘制顺序。 For more information, see [VisualElement Elevation on Android](visualelement-elevation.md).
- 禁用上受支持的旧颜色模式[ `VisualElement` ](xref:Xamarin.Forms.VisualElement)。 For more information, see [VisualElement Legacy Color Mode on Android](legacy-color-mode.md).

The following platform-specific functionality is provided for Xamarin.Forms views on Android:

- 使用默认填充边距和阴影的 Android 按钮的值。 For more information, see [Button Padding and Shadows on Android](button-padding-shadow.md).
- 输入的法编辑器为设置选项的软键盘[ `Entry` ](xref:Xamarin.Forms.Entry)。 For more information, see [Entry Input Method Editor Options on Android](entry-ime-options.md).
- 在启用投影`ImageButton`。 For more information, see [ImageButton Drop Shadows on Android](imagebutton-drop-shadow.md).
- Enabling fast scrolling in a [`ListView`](xref:Xamarin.Forms.ListView) For more information, see [ListView Fast Scrolling on Android](listview-fast-scrolling.md).
- Controlling the transition that's used when opening a `SwipeView`. For more information, see [SwipeView Swipe Transition Mode](swipeview-swipetransitionmode.md).
- 控制是否[ `WebView` ](xref:Xamarin.Forms.WebView)可以显示混合的内容。 For more information, see [WebView Mixed Content on Android](webview-mixed-content.md).
- Enabling zoom on a [`WebView`](xref:Xamarin.Forms.WebView). For more information, see [WebView Zoom on Android](webview-zoom-controls.md).

The following platform-specific functionality is provided for Xamarin.Forms cells on Android:

- Enabling [`ViewCell`](xref:Xamarin.Forms.ViewCell) context actions legacy mode, so that the context actions menu is not updated when the selected item in a [`ListView`](xref:Xamarin.Forms.ListView) changes. 有关详细信息，请参阅[Android 上的 ViewCell 上下文操作](viewcell-context-actions.md)。

以下特定于平台的功能适用于 Android 上的 Xamarin 窗体页：

- 设置导航栏的高度[ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage)。 有关详细信息，请参阅[Android 上的 NavigationPage Bar Height](navigationpage-bar-height.md)。
- 中的页面中导航时禁用过渡动画[ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage)。 有关详细信息，请参阅[Android 上的 TabbedPage 页面过渡动画](tabbedpage-transition-animations.md)。
- 启用在页面间轻扫[ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage)。 有关详细信息，请参阅[Android 上的 TabbedPage Page 轻扫](tabbedpage-page-swiping.md)。
- 在设置工具栏位置和颜色[ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage)。 有关详细信息，请参阅[TabbedPage Toolbar 在 Android 上的位置和颜色](tabbedpage-toolbar-placement-color.md)。

以下特定于平台的功能适用于 Android 上的 Xamarin [`Application`](xref:Xamarin.Forms.Application)类：

- 设置屏幕键盘的操作模式。 有关详细信息，请参阅[Android 上的软键盘输入模式](soft-keyboard-input-mode.md)。
- 禁用[ `Disappearing` ](xref:Xamarin.Forms.Page.Appearing)并[ `Appearing` ](xref:Xamarin.Forms.Page.Appearing)页生命周期事件上暂停和继续分别使用 AppCompat 的应用程序。 有关详细信息，请参阅[Android 上的页面生命周期事件](page-lifecycle-events.md)。

## <a name="platform-support"></a>平台支持

默认情况下，默认的 Xamarin. Forms Android 项目使用早于 Android 5.0 之前的常用控件呈现样式。 使用该模板构建的应用程序的主活动的基类 `FormsApplicationActivity`。

## <a name="material-design-via-appcompat"></a>通过 AppCompat 的材料设计

Xamarin。窗体 Android 项目现在使用 `FormsAppCompatActivity` 作为其主活动的基类。 此类使用 Android 提供的**AppCompat**功能来实现材料设计主题。

若要向 Xamarin Android 项目添加材料设计主题，请按照[AppCompat 支持的安装说明进行](appcompat-material-design.md)操作

下面是带有默认 `FormsApplicationActivity`的**Todo**示例：

[![](images/before-appcompat-sml.png "Todo Sample Application Without AppCompat")](images/before-appcompat.png#lightbox "Todo Sample Application Without AppCompat")

在升级项目以使用 `FormsAppCompatActivity` （并添加其他主题信息）之后，这是同一代码：

[![](images/post-appcompat-sml.png "Todo Sample Application With AppCompat and Theming")](images/post-appcompat.png#lightbox "Todo Sample Application With AppCompat and Theming")

> [!NOTE]
> 使用 `FormsAppCompatActivity`时，[某些 Android 自定义呈现](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md)器的基类将有所不同。

## <a name="related-links"></a>相关链接

- [添加材料设计支持](appcompat-material-design.md)
