---
title: Toolbar
description: 工具栏是提供了更大的灵活性比默认操作栏的操作栏组件： 可以在应用中任意位置放置，可以更改其大小，并且它可以使用不同于应用的主题配色方案。 此外，每个应用屏幕可以有多个工具栏。
ms.prod: xamarin
ms.assetid: 22EE5FBD-3240-4308-AF76-EF45D72936DE
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: 2c9de4058fdaee53671e65f49ad95c3af5e127d6
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61082825"
---
# <a name="toolbar"></a>Toolbar

_工具栏是提供了更大的灵活性比默认操作栏的操作栏组件： 可以在应用中任意位置放置，可以更改其大小，并且它可以使用不同于应用的主题配色方案。此外，每个应用屏幕可以有多个工具栏。_

 
## <a name="overview"></a>概述

任何 Android 活动的一个重要的设计元素是*操作栏*。 在操作栏是用于导航、 搜索、 菜单和品牌 Android 应用中的 UI 组件。 在 Android 5.0 Lollipop，操作栏之前的 Android 版本 (也称为*应用程序栏*) 已提供此功能的建议的组件。 

`Toolbar` （在 Android 5.0 Lollipop 中引入） 的小组件可以看作操作栏接口的泛化&ndash;它旨在替换操作栏。 `Toolbar`可以应用的布局中，在任何地方使用，它更自定义操作栏比。 以下屏幕截图展示了自定义`Toolbar`本指南中创建的示例： 

[![使用编辑工具栏的示例屏幕截图保存和溢出菜单项](images/01-toolbar-sml.png)](images/01-toolbar.png#lightbox)

有一些重要差异`Toolbar`和操作栏： 

-   一个`Toolbar`可以放置在用户界面中的任何地方。

-   可以在同一屏幕上显示多个工具栏。

-   如果使用片段，每个片段可以拥有其自身`Toolbar`。 

-   一个`Toolbar`可以配置为跨越仅屏幕的部分的宽度。 

-   因为`Toolbar`未绑定到活动的窗口装潢的配色方案，它可以在视觉上不同的配色方案。 

-   与操作栏中，不同`Toolbar`不包括在左侧的图标。 在右侧其菜单使用较少的空间。 

-   `Toolbar`高度是可调整。 

-   其他视图可以包含在`Toolbar`。 

一个`Toolbar`可以包含一个或多个以下元素： 

-   导航按钮

-   品牌的徽标图像

-   标题和副标题

-   自定义视图

-   操作菜单

-   溢出菜单

Google [Material Design 准则、](https://material.google.com/)建议利用这些元素以不同的外观 （而不是仅依赖于应用程序图标和标题） 赋予应用程序。 

本指南介绍了最常用的`Toolbar`方案：

-   替换与某个活动的默认操作栏`Toolbar`。 

-   添加另一个`Toolbar`向活动。

-   使用**Android 支持库 v7 AppCompat**库 (称为*AppCompat*本指南的其余部分) 来部署`Toolbar`早期版本的 Android 上。 

 
 
## <a name="requirements"></a>要求

`Toolbar` Android 5.0 Lollipop (API 21) 及更高版本，提供。 当以 Android 为目标低于 Android 5.0 发布后时，使用[Android 支持库 v7 AppCompat](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/)，其中提供了向后兼容`Toolbar`支持 NuGet 包中。 
[工具栏兼容性](~/android/user-interface/controls/tool-bar/toolbar-compatibility.md)说明如何使用此库。 




## <a name="related-links"></a>相关链接

- [棒糖形工具栏 （示例）](https://developer.xamarin.com/samples/monodroid/android5.0/Toolbar/)
- [AppCompat 工具栏 （示例）](https://developer.xamarin.com/samples/monodroid/Supportv7/AppCompat/Toolbar/)
