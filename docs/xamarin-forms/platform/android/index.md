---
title: Android 平台功能
description: 本文介绍如何将特定于 Android 的功能添加到 Xamarin 应用程序。
ms.prod: xamarin
ms.assetid: E24168F3-0138-4814-86EA-B467F6B8A545
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/11/2019
ms.openlocfilehash: 94523bb019e366738de65ce0b05c70264fce738b
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75489760"
---
# <a name="android-platform-features"></a>Android 平台功能

开发适用于 Android 的 Xamarin 应用程序需要 Visual Studio。 "[要求" 页](~/get-started/requirements.md)包含有关先决条件的详细信息。

## <a name="platform-specifics"></a>平台细节

平台特定信息，可使用的功能仅适用于特定的平台，而无需实现自定义呈现器或效果。

以下特定于平台的功能适用于 Xamarin。 Forms 视图、页面和 Android 上的布局：

- 控制 Z 顺序的可视元素来确定绘制顺序。 有关详细信息，请参阅[Android 上的 VisualElement 提升](visualelement-elevation.md)。
- 禁用上受支持的旧颜色模式[ `VisualElement` ](xref:Xamarin.Forms.VisualElement)。 有关详细信息，请参阅[VisualElement 旧版彩色模式（Android](legacy-color-mode.md)）。

以下特定于平台的功能适用于适用于 Android 的 Xamarin 窗体视图：

- 使用默认填充边距和阴影的 Android 按钮的值。 有关详细信息，请参阅[Android 上的按钮填充和阴影](button-padding-shadow.md)。
- 输入的法编辑器为设置选项的软键盘[ `Entry` ](xref:Xamarin.Forms.Entry)。 有关详细信息，请参阅[Android 上的条目输入法编辑器选项](entry-ime-options.md)。
- 在启用投影`ImageButton`。 有关详细信息，请参阅[Android 上的 ImageButton 投影](imagebutton-drop-shadow.md)。
- 在[`ListView`](xref:Xamarin.Forms.ListView)中启用快速滚动有关详细信息，请参阅[在 Android 上进行 ListView 快速滚动](listview-fast-scrolling.md)。
- 控制在打开 `SwipeView`时使用的转换。 有关详细信息，请参阅[SwipeView 滑动过渡模式](swipeview-swipetransitionmode.md)。
- 控制是否[ `WebView` ](xref:Xamarin.Forms.WebView)可以显示混合的内容。 有关详细信息，请参阅[Android 上的 Web 视图混合内容](webview-mixed-content.md)。
- 启用缩放[`WebView`](xref:Xamarin.Forms.WebView)。 有关详细信息，请参阅[Android 上的 Web 视图缩放](webview-zoom-controls.md)。

以下特定于平台的功能适用于 Xamarin 上的 Xamarin 表单元格：

- 启用[`ViewCell`](xref:Xamarin.Forms.ViewCell)上下文操作旧版模式，以便在[`ListView`](xref:Xamarin.Forms.ListView)中的选定项发生更改时不会更新上下文操作菜单。 有关详细信息，请参阅[Android 上的 ViewCell 上下文操作](viewcell-context-actions.md)。

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
