---
title: Android 控件（小组件）
description: 用于创建 Xamarin Android 用户界面的构建基块
ms.prod: xamarin
ms.assetid: B7A82166-B920-4672-B7A2-20DD5E0B5AEF
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 08/29/2018
ms.openlocfilehash: 25e33ee2d77501b3913a3598ef7855714f0b9bc1
ms.sourcegitcommit: 52fb214c0e0243587d4e9ad9306b75e92a8cc8b7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/01/2020
ms.locfileid: "76940896"
---
# <a name="xamarinandroid-controls-widgets"></a>Xamarin Android 控件（小组件）

Xamarin 公开了 Android 提供的所有本机用户界面控件（小组件）。 可以使用 Android Designer 或通过 XML 布局文件以编程方式将这些控件添加到 Xamarin 应用程序。 无论选择哪种方法，Xamarin 都将公开中C#的所有用户界面对象属性和方法。 以下各节介绍最常见的 Android 用户界面控件，并说明如何将它们合并到 Xamarin Android 应用程序中。

## <a name="action-barandroiduser-interfacecontrolsaction-barmd"></a>[ActionBar](~/android/user-interface/controls/action-bar.md) 

`ActionBar` 是显示活动标题、导航界面和其他交互式项目的工具栏。 通常情况下，`ActionBar`显示在活动的窗口的顶部。

![ActionBar 示例](images/action-bar.png)

## <a name="auto-completeandroiduser-interfacecontrolsauto-completemd"></a>[自动完成](~/android/user-interface/controls/auto-complete.md)

`AutoCompleteTextView` 是一个可编辑的文本视图元素，当用户输入时自动显示完成建议。 建议列表显示在下拉菜单中，用户可以从中选择要替换编辑框内容的项。

![AutoCompleteTextView 示例](images/auto-complete.png)

## <a name="buttonsandroiduser-interfacecontrolsbuttonsindexmd"></a>[按钮](~/android/user-interface/controls/buttons/index.md)

`Button`是用户点击执行操作的 UI 元素。

![Button 示例](images/buttons.png)

## <a name="calendarandroiduser-interfacecontrolscalendarmd"></a>[日历](~/android/user-interface/controls/calendar.md)

`Calendar` 类用于将时间（相对于 epoch 的毫秒值）转换为值（例如年、月、小时、月中的第几天）以及下一周的日期。
`Calendar` 支持大量与日历数据的交互选项，包括读取和写入事件、与会者和提醒的能力。 通过在你的应用程序中使用日历提供程序，你通过 API 添加的数据将出现在 Android 附带的内置日历应用中。

![Calendar 示例](images/calendar.png)

## <a name="cardviewandroiduser-interfacecontrolscard-viewmd"></a>[CardView](~/android/user-interface/controls/card-view.md)

`CardView` 是一种 UI 组件，它在类似于卡的视图中提供文本和图像内容。 `CardView` 作为带有圆角和阴影的 `FrameLayout` 小组件实现。 通常，`CardView` 用于在 `ListView` 或 `GridView` 视图组中显示单个行项。

![CardView 示例](images/cardview.png)

## <a name="edit-textandroiduser-interfacecontrolsedit-textmd"></a>[EditText](~/android/user-interface/controls/edit-text.md)

`EditText` 是用于输入和修改文本的 UI 元素。

![EditText 示例](images/edit-text.png)

## <a name="galleryandroiduser-interfacecontrolsgallerymd"></a>[库](~/android/user-interface/controls/gallery.md)

`Gallery` 是用于显示水平滚动列表中的项的布局小组件;它将当前所选内容置于视图的中心。

![Gallery 示例](images/gallery.png)

## <a name="navigation-barandroiduser-interfacecontrolsnavigation-barmd"></a>[导航栏](~/android/user-interface/controls/navigation-bar.md)

*Navigation Bar*提供在不包括有关的硬件按钮的设备上的导航控件**主页**，**返回**，和**菜单**。

![Navigation Bar 示例](images/navigation-bar.png)

## <a name="pickersandroiduser-interfacecontrolspickersindexmd"></a>[选取器](~/android/user-interface/controls/pickers/index.md)

*选取*器是允许用户通过使用 Android 提供的对话来选取日期或时间的 UI 元素。

![Picker 示例](images/picker.png)

## <a name="popup-menuandroiduser-interfacecontrolspopup-menumd"></a>[弹出菜单](~/android/user-interface/controls/popup-menu.md)

`PopupMenu` 用于显示附加到特定视图的弹出菜单。

![PopupMenu 示例](images/popup-menu.png)

## <a name="ratingbarandroiduser-interfacecontrolsratingbarmd"></a>[RatingBar](~/android/user-interface/controls/ratingbar.md)

`RatingBar` 是显示星形评级的 UI 元素。

![RatingBar 的示例](ratingbar-images/01-ratingbar.png)

## <a name="spinnerandroiduser-interfacecontrolsspinnermd"></a>[旋转图标](~/android/user-interface/controls/spinner.md)

`Spinner` 是一种 UI 元素，它提供从集中选择一个值的快速方法。 它类似于下拉列表。 

![Spinner 示例](images/spinner.png)

## <a name="switchandroiduser-interfacecontrolsswitchmd"></a>[切换](~/android/user-interface/controls/switch.md)

`Switch` 是允许用户在两个状态之间切换的 UI 元素，如 ON 或 OFF。 `Switch` 默认值为 OFF。

![Switch 示例](images/switch.png)

## <a name="textureviewandroiduser-interfacecontrolstexture-viewmd"></a>[TextureView](~/android/user-interface/controls/texture-view.md)

`TextureView` 是一种视图，它使用硬件加速2D 呈现来启用视频或 OpenGL 内容流。

![TextureView 示例](images/texture-view.png)

## <a name="toolbarandroiduser-interfacecontrolstool-barindexmd"></a>[工具栏](~/android/user-interface/controls/tool-bar/index.md)

`Toolbar` 小组件（在 Android 5.0 棒糖形中引入）可以被视为操作栏接口的泛化 &ndash; 用于替换操作栏。 可以在应用布局中的任意位置使用 `Toolbar`，它比操作栏更易于自定义。

![Toolbar 示例](images/toolbar.png)

## <a name="viewpagerandroiduser-interfacecontrolsview-pagerindexmd"></a>[ViewPager](~/android/user-interface/controls/view-pager/index.md) 

`ViewPager` 是一种布局管理器，允许用户在数据页中左右翻页。

![ViewPager 示例](images/viewpager.png)

## <a name="webviewandroiduser-interfacecontrolsweb-viewmd"></a>[WebView](~/android/user-interface/controls/web-view.md)

`WebView` 是一种 UI 元素，它允许您创建自己的窗口以查看网页（甚至开发完整的浏览器）。

![WebView 示例](images/web-view.png)
