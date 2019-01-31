---
title: Android 平台功能
description: 本文介绍如何将 Android 特有的功能添加到 Xamarin.Forms 应用程序。
ms.prod: xamarin
ms.assetid: E24168F3-0138-4814-86EA-B467F6B8A545
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/07/2018
ms.openlocfilehash: a90ae27bb4e0085d1cd0c5b36cf7c00fe5ebfec6
ms.sourcegitcommit: 817d26585093cd180a36b28179eb354b0eb900b3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/30/2019
ms.locfileid: "55292215"
---
# <a name="android-platform-features"></a>Android 平台功能

开发适用于 Android 的 Xamarin.Forms 应用程序需要 Visual Studio。 [要求页](~/get-started/installation.md)包含有关系统必备组件的详细信息。

## <a name="platform-specifics"></a>平台特定信息

平台特定信息，可使用的功能仅适用于特定的平台，而无需实现自定义呈现器或效果。

Xamarin.Forms 视图、 页面和 Android 上的布局提供了以下特定于平台的功能：

- 控制 Z 顺序的可视元素来确定绘制顺序。 有关详细信息，请参阅[VisualElement 提升 Android 上](visualelement-elevation.md)。
- 禁用上受支持的旧颜色模式[ `VisualElement` ](xref:Xamarin.Forms.VisualElement)。 有关详细信息，请参阅[VisualElement 旧式颜色模式在 Android 上](legacy-color-mode.md)。

在 Android 上 Xamarin.Forms 视图提供以下特定于平台的功能：

- 使用默认填充边距和阴影的 Android 按钮的值。 有关详细信息，请参阅[填充按钮，并在 Android 上的阴影](button-padding-shadow.md)。
- 输入的法编辑器为设置选项的软键盘[ `Entry` ](xref:Xamarin.Forms.Entry)。 有关详细信息，请参阅[条目输入法编辑器选项在 Android 上](entry-ime-options.md)。
- 在启用投影`ImageButton`。 有关详细信息，请参阅[ImageButton 删除阴影的 Android](imagebutton-drop-shadow.md)。
- 启用快速滚动[ `ListView` ](xref:Xamarin.Forms.ListView)的详细信息，请参阅[ListView 快速滚动在 Android 上](listview-fast-scrolling.md)。
- 控制是否[ `WebView` ](xref:Xamarin.Forms.WebView)可以显示混合的内容。 有关详细信息，请参阅[WebView 混合内容在 Android 上](webview-mixed-content.md)。

在 Android 上 Xamarin.Forms 页面提供以下特定于平台的功能：

- 设置导航栏的高度[ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage)。 有关详细信息，请参阅[NavigationPage 条在 Android 上的高度](navigationpage-bar-height.md)。
- 中的页面中导航时禁用过渡动画[ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage)。 有关详细信息，请参阅[TabbedPage 页面过渡动画在 Android 上](tabbedpage-transition-animations.md)。
- 启用在页面间轻扫[ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage)。 有关详细信息，请参阅[TabbedPage 页面在 Android 上轻扫](tabbedpage-page-swiping.md)。
- 在设置工具栏位置和颜色[ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage)。 有关详细信息，请参阅[TabbedPage 工具栏位置和颜色在 Android 上的](tabbedpage-toolbar-placement-color.md)。

以下特定于平台的功能提供适用于 Xamarin.Forms [ `Application` ](xref:Xamarin.Forms.Application)在 Android 上的类：

- 设置屏幕键盘的操作模式。 有关详细信息，请参阅[在 Android 上的软键盘输入模式](soft-keyboard-input-mode.md)。
- 禁用[ `Disappearing` ](xref:Xamarin.Forms.Page.Appearing)并[ `Appearing` ](xref:Xamarin.Forms.Page.Appearing)页生命周期事件上暂停和继续分别使用 AppCompat 的应用程序。 有关详细信息，请参阅[在 Android 上的页面生命周期事件](page-lifecycle-events.md)。

## <a name="platform-support"></a>平台支持

最初，默认 Xamarin.Forms Android 项目使用较旧样式的控件通常在 Android 5.0 之前的呈现。 使用模板生成的应用程序具有`FormsApplicationActivity`作为其主活动的基类。

## <a name="material-design-via-appcompat"></a>通过 AppCompat 材料设计

Xamarin.Forms Android 项目现在使用`FormsAppCompatActivity`作为其主活动的基类。 此类使用**AppCompat**由 Android 实现 Material Design 主题提供的功能。

若要添加到 Xamarin.Forms Android 项目的 Material Design 主题，请遵循[AppCompat 的安装说明支持](appcompat-material-design.md)

下面是**Todo**示例使用默认`FormsApplicationActivity`:

[![](images/before-appcompat-sml.png "待办事项示例应用程序而无需 AppCompat")](images/before-appcompat.png#lightbox "没有 AppCompat 的待办事项示例应用程序")

这是后升级项目以使用相同的代码和`FormsAppCompatActivity`（和添加其他主题信息）：

[![](images/post-appcompat-sml.png "AppCompat 和主题的待办事项示例应用程序")](images/post-appcompat.png#lightbox "AppCompat 和主题的待办事项示例应用程序")

> [!NOTE]
> 使用时`FormsAppCompatActivity`，则[对于某些 Android 自定义呈现器基类](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md)会有所不同。

## <a name="related-links"></a>相关链接

- [添加材料设计的支持](appcompat-material-design.md)
