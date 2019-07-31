---
title: Toolbar
description: '工具栏是比默认操作栏提供更多灵活性的操作栏组件: 可以将其放置在应用中的任何位置, 可以更改其大小, 还可以使用不同于应用主题的配色方案。 此外, 每个应用屏幕都可以有多个工具栏。'
ms.prod: xamarin
ms.assetid: 22EE5FBD-3240-4308-AF76-EF45D72936DE
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: 25287e5aa52eeac712f93c3973e02c7e14c89a78
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2019
ms.locfileid: "68645101"
---
# <a name="toolbar"></a>Toolbar

_工具栏是比默认操作栏提供更多灵活性的操作栏组件: 可以将其放置在应用中的任何位置, 可以更改其大小, 还可以使用不同于应用主题的配色方案。此外, 每个应用屏幕都可以有多个工具栏。_

 
## <a name="overview"></a>概述

任何 Android 活动的关键设计元素都是一个*操作栏*。 操作栏是在 Android 应用中用于导航、搜索、菜单和品牌标记的 UI 组件。 在 android 5.0 棒糖之前的 Android 版本中, 建议使用操作栏 (也称为*应用栏*) 来提供此功能。 

小组件 (在 Android 5.0 棒糖形中引入) 可以被视为操作栏接口&ndash;的泛化, 它旨在替换操作栏。 `Toolbar` 可以`Toolbar`在应用布局中的任意位置使用, 它比操作栏更容易自定义。 以下屏幕截图演示了本`Toolbar`指南中创建的自定义示例: 

[![带有编辑、保存和溢出菜单项的工具栏的示例屏幕截图](images/01-toolbar-sml.png)](images/01-toolbar.png#lightbox)

`Toolbar`和操作栏之间存在一些重要的区别: 

-   `Toolbar`可以放置在用户界面中的任何位置。

-   同一屏幕上可以显示多个工具栏。

-   如果使用片段, 则每个片段都可以有`Toolbar`自己的片段。 

-   `Toolbar`可以配置为仅跨越屏幕的部分宽度。 

-   `Toolbar`由于未绑定到活动的窗口 décor 的配色方案, 因此它可以有一个视觉上不同的配色方案。 

-   不同于操作栏, `Toolbar`不会在左侧包含一个图标。 其菜单上的菜单使用更少的空间。 

-   高度`Toolbar`是可调整的。 

-   其他视图可以包括在中`Toolbar`。 

`Toolbar`可以包含一个或多个以下元素: 

-   导航按钮

-   品牌徽标图像

-   标题和副标题

-   自定义视图

-   操作菜单

-   溢出菜单

Google 的[材料设计指南](https://material.google.com/)建议利用这些元素, 使应用具有不同的外观 (而不只是依赖于应用程序图标和标题)。 

本指南涵盖最常使用`Toolbar`的方案:

-   将`Toolbar`活动的默认操作栏替换为。 

-   将第二`Toolbar`个添加到活动。

-   使用**android 支持库 v7 AppCompat**库 (在本指南的其余部分中称为 " *AppCompat* ") 来部署`Toolbar`到早期版本的 Android。 

 
 
## <a name="requirements"></a>要求

`Toolbar`适用于 Android 5.0 棒糖形 (API 21) 和更高版本。 如果面向 android 5.0 之前的 android 版本, 请使用[android 支持库 v7 AppCompat](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/), 它在 NuGet 包中`Toolbar`提供向后兼容的支持。 
[工具栏兼容性](~/android/user-interface/controls/tool-bar/toolbar-compatibility.md)介绍了如何使用此库。 




## <a name="related-links"></a>相关链接

- [棒糖形工具栏 (示例)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android50-toolbar)
- [AppCompat 工具栏 (示例)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/supportv7-appcompat-toolbar)
