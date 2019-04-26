---
title: Android 控件 （小组件）
description: 用于创建 Xamarin.Android 用户界面的构建基块
ms.prod: xamarin
ms.assetid: B7A82166-B920-4672-B7A2-20DD5E0B5AEF
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 08/29/2018
ms.openlocfilehash: 842fb1df2c9cc1aaf1a106687179a3730c2503bd
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61041875"
---
# <a name="android-controls-widgets"></a>Android 控件 （小组件）

Xamarin.Android 会公开所有由 Android 提供的本机用户界面控件 （小组件）。 使用 Android 设计器的 Xamarin.Android 应用，或以编程方式通过 XML 布局文件，可以轻松地添加这些控件。 无论选择哪种方法，Xamarin.Android 公开的所有用户界面对象属性和 C# 中的方法。 以下各节介绍最常见的 Android 用户界面控件，并说明如何将它们合并到 Xamarin.Android 应用。

## <a name="action-barandroiduser-interfacecontrolsaction-barmd"></a>[ActionBar](~/android/user-interface/controls/action-bar.md) 

`ActionBar` 是活动标题、 导航接口和其他交互式项将显示一个工具栏。 通常情况下，`ActionBar`显示在活动的窗口的顶部。

![ActionBar 示例](images/action-bar.png)


## <a name="auto-completeandroiduser-interfacecontrolsauto-completemd"></a>[AutoCompleteTextView](~/android/user-interface/controls/auto-complete.md)

`AutoCompleteTextView` 是一个可编辑的文本视图元素，当用户输入时自动显示完成建议。 下拉列表菜单，用户可以从中选择要替换的编辑框的内容的项中显示的建议列表。

![AutoCompleteTextView 示例](images/auto-complete.png)


## <a name="buttonsandroiduser-interfacecontrolsbuttonsindexmd"></a>[Button](~/android/user-interface/controls/buttons/index.md)

`Button`是用户点击执行操作的 UI 元素。

![Button 示例](images/buttons.png)


## <a name="calendarandroiduser-interfacecontrolscalendarmd"></a>[Calendar](~/android/user-interface/controls/calendar.md)

`Calendar`类用于转换中的特定实例的值，例如年、 月、 小时、 数月，日和下一周的日期时间 （偏离 epoch 的毫秒值）。
`Calendar` 支持丰富的交互选项的日历数据，包括能够读取和写入事件、 与会者和提醒。 通过在应用程序中使用的日历提供程序，通过 API 添加的数据将出现在附带了 Android 内置日历应用程序。

![Calendar 示例](images/calendar.png)


## <a name="cardviewandroiduser-interfacecontrolscard-viewmd"></a>[CardView](~/android/user-interface/controls/card-view.md)

`CardView` 是类似于卡的视图中显示的文本和图像内容的 UI 组件。 `CardView` 作为实现`FrameLayout`带有圆的角和阴影的小组件。 通常情况下，`CardView`用来表示中的单个行项`ListView`或`GridView`查看组。

![CardView 示例](images/cardview.png)


## <a name="edit-textandroiduser-interfacecontrolsedit-textmd"></a>[EditText](~/android/user-interface/controls/edit-text.md)

`EditText` 是用于输入和修改文本的 UI 元素。

![EditText 示例](images/edit-text.png)


## <a name="galleryandroiduser-interfacecontrolsgallerymd"></a>[Gallery](~/android/user-interface/controls/gallery.md)

`Gallery` 是用于在水平滚动的列表; 中显示项的布局小组件它将在视图的中心定位在当前所选内容。

![Gallery 示例](images/gallery.png)


## <a name="navigation-barandroiduser-interfacecontrolsnavigation-barmd"></a>[Navigation Bar](~/android/user-interface/controls/navigation-bar.md)

*Navigation Bar*提供在不包括有关的硬件按钮的设备上的导航控件**主页**，**返回**，和**菜单**。

![Navigation Bar 示例](images/navigation-bar.png)


## <a name="pickersandroiduser-interfacecontrolspickersindexmd"></a>[Picker](~/android/user-interface/controls/pickers/index.md)

*选取器*是允许用户通过使用由 Android 提供的对话框中选择一个日期或时间的 UI 元素。

![Picker 示例](images/picker.png)


## <a name="popup-menuandroiduser-interfacecontrolspopup-menumd"></a>[PopupMenu](~/android/user-interface/controls/popup-menu.md)

`PopupMenu` 用于显示附加到特定视图的弹出菜单。

![PopupMenu 示例](images/popup-menu.png)


## <a name="ratingbarandroiduser-interfacecontrolsratingbarmd"></a>[RatingBar](~/android/user-interface/controls/ratingbar.md)

一个`RatingBar`是显示在星评级的 UI 元素。

![RatingBar 的示例](ratingbar-images/01-ratingbar.png)


## <a name="spinnerandroiduser-interfacecontrolsspinnermd"></a>[Spinner](~/android/user-interface/controls/spinner.md)

`Spinner` 是，可以快速从一组选择一个值的 UI 元素。 它是 simmilar 到下拉列表。 

![Spinner 示例](images/spinner.png)


## <a name="switchandroiduser-interfacecontrolsswitchmd"></a>[Switch](~/android/user-interface/controls/switch.md)

`Switch` 是允许用户以两个状态，例如在之间切换或关闭的 UI 元素。 `Switch`默认值为 OFF。

![Switch 示例](images/switch.png)


## <a name="textureviewandroiduser-interfacecontrolstexture-viewmd"></a>[TextureView](~/android/user-interface/controls/texture-view.md)

`TextureView` 是一个使用硬件加速 2D 呈现若要启用的视频或 OpenGL 内容流，要显示的视图。

![TextureView 示例](images/texture-view.png)


## <a name="toolbarandroiduser-interfacecontrolstool-barindexmd"></a>[Toolbar](~/android/user-interface/controls/tool-bar/index.md)

`Toolbar` （在 Android 5.0 Lollipop 中引入） 的小组件可以看作操作栏接口的泛化&ndash;它旨在替换操作栏。 `Toolbar`可以应用的布局中，在任何地方使用，它更自定义操作栏比。

![Toolbar 示例](images/toolbar.png)


## <a name="viewpagerandroiduser-interfacecontrolsview-pagerindexmd"></a>[ViewPager](~/android/user-interface/controls/view-pager/index.md) 

`ViewPager`是允许用户翻转左侧和右侧的数据页的布局管理器。

![ViewPager 示例](images/viewpager.png)


## <a name="webviewandroiduser-interfacecontrolsweb-viewmd"></a>[WebView](~/android/user-interface/controls/web-view.md)

`WebView` 是一个 UI 元素，您可以创建自己的窗口用于查看的网页 （或甚至开发完整的浏览器）。

![WebView 示例](images/web-view.png)

