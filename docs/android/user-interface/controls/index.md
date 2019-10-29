---
title: Android 控件（小组件）
description: 用于创建 Xamarin Android 用户界面的构建基块
ms.prod: xamarin
ms.assetid: B7A82166-B920-4672-B7A2-20DD5E0B5AEF
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 08/29/2018
ms.openlocfilehash: 8c0a2dffbe312cb25258cd2738b661ded2df8d7d
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029265"
---
# <a name="xamarinandroid-controls-widgets"></a>Xamarin Android 控件（小组件）

Xamarin 公开了 Android 提供的所有本机用户界面控件（小组件）。 可以使用 Android Designer 或通过 XML 布局文件以编程方式将这些控件添加到 Xamarin 应用程序。 无论选择哪种方法，Xamarin 都将公开中C#的所有用户界面对象属性和方法。 以下各节介绍最常见的 Android 用户界面控件，并说明如何将它们合并到 Xamarin Android 应用程序中。

## <a name="action-barandroiduser-interfacecontrolsaction-barmd"></a>[操作栏](~/android/user-interface/controls/action-bar.md) 

`ActionBar` 是显示活动标题、导航界面和其他交互式项目的工具栏。 通常，操作栏出现在活动窗口的顶部。

![示例 ActionBar](images/action-bar.png)

## <a name="auto-completeandroiduser-interfacecontrolsauto-completemd"></a>[自动完成](~/android/user-interface/controls/auto-complete.md)

`AutoCompleteTextView` 是在用户键入时自动显示完成建议的可编辑文本视图元素。 建议列表显示在下拉菜单中，用户可以从中选择要替换编辑框内容的项。

![自动完成的示例](images/auto-complete.png)

## <a name="buttonsandroiduser-interfacecontrolsbuttonsindexmd"></a>[按钮](~/android/user-interface/controls/buttons/index.md)

按钮是用户点击以执行操作的 UI 元素。

![示例按钮](images/buttons.png)

## <a name="calendarandroiduser-interfacecontrolscalendarmd"></a>[日历](~/android/user-interface/controls/calendar.md)

`Calendar` 类用于将时间（相对于 epoch 的毫秒值）转换为值（例如年、月、小时、月中的第几天）以及下一周的日期。
`Calendar` 支持大量与日历数据的交互选项，包括读取和写入事件、与会者和提醒的能力。 通过在你的应用程序中使用日历提供程序，你通过 API 添加的数据将出现在 Android 附带的内置日历应用中。

![示例日历](images/calendar.png)

## <a name="cardviewandroiduser-interfacecontrolscard-viewmd"></a>[CardView](~/android/user-interface/controls/card-view.md)

`CardView` 是一种 UI 组件，它在类似于卡的视图中提供文本和图像内容。 `CardView` 作为带有圆角和阴影的 `FrameLayout` 小组件实现。 通常，`CardView` 用于在 `ListView` 或 `GridView` 视图组中显示单个行项。

![示例卡片视图](images/cardview.png)

## <a name="edit-textandroiduser-interfacecontrolsedit-textmd"></a>[编辑文本](~/android/user-interface/controls/edit-text.md)

`EditText` 是用于输入和修改文本的 UI 元素。

![编辑文本示例](images/edit-text.png)

## <a name="galleryandroiduser-interfacecontrolsgallerymd"></a>[库](~/android/user-interface/controls/gallery.md)

`Gallery` 是用于显示水平滚动列表中的项的布局小组件;它将当前所选内容置于视图的中心。

![示例库](images/gallery.png)

## <a name="navigation-barandroiduser-interfacecontrolsnavigation-barmd"></a>[导航栏](~/android/user-interface/controls/navigation-bar.md)

*导航栏*在不包含**Home**、 **Back**和**Menu**硬件按钮的设备上提供导航控件。

![导航栏示例](images/navigation-bar.png)

## <a name="pickersandroiduser-interfacecontrolspickersindexmd"></a>[选取器](~/android/user-interface/controls/pickers/index.md)

*选取*器是允许用户通过使用 Android 提供的对话来选取日期或时间的 UI 元素。

![示例选取器](images/picker.png)

## <a name="popup-menuandroiduser-interfacecontrolspopup-menumd"></a>[弹出菜单](~/android/user-interface/controls/popup-menu.md)

`PopupMenu` 用于显示附加到特定视图的弹出菜单。

![示例弹出菜单](images/popup-menu.png)

## <a name="ratingbarandroiduser-interfacecontrolsratingbarmd"></a>[RatingBar](~/android/user-interface/controls/ratingbar.md)

`RatingBar` 是显示星形评级的 UI 元素。

![RatingBar 的示例](ratingbar-images/01-ratingbar.png)

## <a name="spinnerandroiduser-interfacecontrolsspinnermd"></a>[旋转图标](~/android/user-interface/controls/spinner.md)

`Spinner` 是一种 UI 元素，它提供从集中选择一个值的快速方法。 它 simmilar 为下拉列表。 

![示例微调框](images/spinner.png)

## <a name="switchandroiduser-interfacecontrolsswitchmd"></a>[切换](~/android/user-interface/controls/switch.md)

`Switch` 是允许用户在两个状态之间切换的 UI 元素，如 ON 或 OFF。 `Switch` 默认值为 OFF。

![示例开关](images/switch.png)

## <a name="textureviewandroiduser-interfacecontrolstexture-viewmd"></a>[TextureView](~/android/user-interface/controls/texture-view.md)

`TextureView` 是一种视图，它使用硬件加速2D 呈现来启用视频或 OpenGL 内容流。

![示例纹理视图](images/texture-view.png)

## <a name="toolbarandroiduser-interfacecontrolstool-barindexmd"></a>[工具栏](~/android/user-interface/controls/tool-bar/index.md)

`Toolbar` 小组件（在 Android 5.0 棒糖形中引入）可以被视为操作栏接口的泛化 &ndash; 用于替换操作栏。 可以在应用布局中的任意位置使用 `Toolbar`，它比操作栏更易于自定义。

![示例工具栏](images/toolbar.png)

## <a name="viewpagerandroiduser-interfacecontrolsview-pagerindexmd"></a>[ViewPager](~/android/user-interface/controls/view-pager/index.md) 

`ViewPager` 是一种布局管理器，允许用户在数据页中左右翻页。

![示例 ViewPager](images/viewpager.png)

## <a name="webviewandroiduser-interfacecontrolsweb-viewmd"></a>[WebView](~/android/user-interface/controls/web-view.md)

`WebView` 是一种 UI 元素，它允许您创建自己的窗口以查看网页（甚至开发完整的浏览器）。

![示例 Web 视图](images/web-view.png)
