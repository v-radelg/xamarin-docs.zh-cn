---
title: Xamarin 中的 iOS 平台功能
description: 向 Xamarin 应用程序添加 iOS 特定功能。
ms.prod: xamarin
ms.assetid: 634AB62E-68C8-454C-838B-F1CC4E4E21BC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/11/2019
ms.openlocfilehash: 5d0e289ddeb7eabef6d96c8882c772c704c54b34
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75489721"
---
# <a name="ios-platform-features-in-xamarinforms"></a>Xamarin 中的 iOS 平台功能

开发适用于 iOS 的 Xamarin 应用程序需要 Visual Studio。 "[要求" 页](~/get-started/requirements.md)包含有关先决条件的详细信息。

## <a name="platform-specifics"></a>平台细节

平台特定信息，可使用的功能仅适用于特定的平台，而无需实现自定义呈现器或效果。

以下特定于平台的功能适用于 Xamarin。 Forms 视图、页面和 iOS 上的布局：

- 模糊处理的任何支持[ `VisualElement` ](xref:Xamarin.Forms.VisualElement)。 有关详细信息，请参阅[iOS 上的 VisualElement 模糊](visualelement-blur.md)。
- 禁用上受支持的旧颜色模式[ `VisualElement` ](xref:Xamarin.Forms.VisualElement)。 有关详细信息，请参阅[VisualElement 旧版彩色模式 On iOS](legacy-color-mode.md)。
- 在启用投影[ `VisualElement` ](xref:Xamarin.Forms.VisualElement)。 有关详细信息，请参阅[iOS 上的 VisualElement 投影](visualelement-drop-shadow.md)。

以下特定于平台的功能适用于适用于 iOS 的 Xamarin 视图：

- 设置[`Cell`](xref:Xamarin.Forms.Cell)背景色。 有关详细信息，请参阅[iOS 上的单元格背景色](cell-background-color.md)。
- 确保输入的文本适合[ `Entry` ](xref:Xamarin.Forms.Entry)通过调整字体大小。 有关详细信息，请参阅[iOS 上的输入字体大小](entry-font-size.md)。
- 在中设置的游标颜色[ `Entry` ](xref:Xamarin.Forms.Entry)。 有关详细信息，请参阅[iOS 上的入口游标颜色](entry-cursor-color.md)。
- 控制是否[`ListView`](xref:Xamarin.Forms.ListView)标题单元在滚动过程中浮动。 有关详细信息，请参阅[iOS 上的 ListView 组标头样式](listview-group-header-style.md)。
- 控制在更新[`ListView`](xref:Xamarin.Forms.ListView)项集合时是否禁用行动画。 有关详细信息，请参阅[在 iOS 上 ListView 行动画](listview-row-animations.md)。
- 设置分隔符样式[ `ListView` ](xref:Xamarin.Forms.ListView)。 有关详细信息，请参阅[iOS 上的 ListView 分隔符样式](listview-separator-style.md)。
- 控制当项选择发生在[ `Picker` ](xref:Xamarin.Forms.Picker)。 有关详细信息，请参阅[iOS 上的选取器项目选择](picker-selection.md)。
- 启用[ `Slider.Value` ](xref:Xamarin.Forms.Slider.Value)属性可以通过点击对一个位置上设置[ `Slider` ](xref:Xamarin.Forms.Slider)栏中，而不是按无需将`Slider`thumb。 有关详细信息，请参阅[滚动条上](slider-thumb.md)的滚动条。
- 控制在打开 `SwipeView`时使用的转换。 有关详细信息，请参阅[SwipeView 滑动过渡模式](swipeview-swipetransitionmode.md)。

以下特定于平台的功能适用于 iOS 上的 Xamarin 窗体页：

- 在隐藏导航栏分隔符[ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage)。 有关详细信息，请参阅[iOS 上的 NavigationPage Bar 分隔符](navigation-bar-separator.md)。
- 控制是否半透明的导航栏。 有关详细信息，请参阅[导航栏半透明度 On iOS](navigation-bar-translucent.md)。
- 控制是否状态栏文本的颜色上[ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage)调整以匹配导航栏的亮度。 有关详细信息，请参阅[NavigationPage Bar 文本 Color Mode On iOS](status-bar-text-color.md)。
- 控制是否将页标题显示为页导航栏中的大型标题。 有关详细信息，请参阅[iOS 上的大型页面标题](page-large-title.md)。
- 设置[`Page`](xref:Xamarin.Forms.Page)上主指示器的可见性。 有关详细信息，请参阅[iOS 上的主指示器可见性](page-home-indicator.md)。
- 设置状态条可见性[ `Page` ](xref:Xamarin.Forms.Page)。 有关详细信息，请参阅[iOS 上的页面状态栏可见性](page-status-bar-visibility.md)。
- 确保该页面内容位于上是安全的所有 iOS 设备的屏幕区域。 有关详细信息，请参阅[iOS 上的安全区域布局指南](page-safe-area-layout.md)。
- 设置模式页的呈现样式。 有关详细信息，请参阅[模式页面表示形式样式](page-presentation-style.md)。

以下特定于平台的功能适用于适用于 iOS 的 Xamarin 布局布局：

- 控制是否[ `ScrollView` ](xref:Xamarin.Forms.ScrollView)处理触摸手势或将其传递给其内容。 有关详细信息，请参阅[ScrollView 内容涉及 iOS](scrollview-content-touches.md)。

在 iOS 上，为 Xamarin [`Application`](xref:Xamarin.Forms.Application)类提供以下特定于平台的功能：

- 禁用命名字体大小的辅助功能缩放。 有关详细信息，请参阅[iOS 上命名字体大小的辅助功能缩放](named-font-size-scaling.md)。
- 启用控件的布局和呈现要在主线程上执行更新。 有关详细信息，请参阅[iOS 上的主线程控制更新](main-thread-updates-ui.md)。
- 启用[ `PanGestureRecognizer` ](xref:Xamarin.Forms.PanGestureRecognizer)中滚动视图来捕获和共享平移手势与滚动视图。 有关详细信息，请参阅[iOS 上的并发平移手势识别](application-pan-gesture.md)。

## <a name="ios-specific-formatting"></a>特定于 iOS 的格式

Xamarin 可以设置跨平台用户界面样式和颜色，但也可以使用 iOS 项目中的平台 Api 来设置 iOS 主题。

[阅读](formatting.md)有关使用 IOS 特定 api 设置用户界面格式的详细信息，如**info.plist**配置和 `UIAppearance` API。

![](images/status-white-sml.png "iOS Theming")

## <a name="other-ios-features"></a>其他 iOS 功能

使用[自定义呈现](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)器、 [DependencyService](~/xamarin-forms/app-fundamentals/dependency-service/index.md)和[MessagingCenter](~/xamarin-forms/app-fundamentals/messaging-center.md)，可以将各种本机功能合并到适用于 iOS 的 Xamarin 应用程序中。
