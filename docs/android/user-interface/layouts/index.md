---
title: 布局
description: 定义 Xamarin Android 应用的视觉对象结构
ms.prod: xamarin
ms.assetid: 2BA72B0E-230D-4F98-B4D5-4EFB0D479789
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 08/18/2017
ms.openlocfilehash: f15aeff8590cf6d82168fd3e39ba6f78d649d2ab
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73028967"
---
# <a name="xamarinandroid-layouts"></a>Xamarin Android 布局

布局用于排列组成屏幕 UI 界面（如活动）的元素。 以下部分介绍如何在 Xamarin Android 应用中使用最常用的布局。

- [LinearLayout](~/android/user-interface/layouts/linear-layout.md)是一个视图组，它以垂直或水平方式以线性方向显示子视图元素。

    ![线性布局示例](images/linear-layout.png)

- [RelativeLayout](~/android/user-interface/layouts/relative-layout.md)是在相对位置显示子视图元素的视图组。 可以将视图的位置指定为相对于同级元素。

    ![示例相对布局](images/relative-layout.png)

- [TableLayout](~/android/user-interface/layouts/table-layout.md)是一个视图组，用于显示行和列中的子视图元素。

    ![示例表布局](images/table-layout.png)

- [RecyclerView](~/android/user-interface/layouts/recycler-view/index.md)是一个 UI 元素，用于显示列表或网格中的项集合，使用户能够滚动浏览该集合。

    ![示例 Recycler 视图](images/recycler-view.png)

- [ListView](~/android/user-interface/layouts/list-view/index.md)是创建可滚动项列表的视图组。 列表项自动使用列表适配器插入列表中。 `ListView` 是 Android 应用程序的重要 UI 组件，因为它可用于从菜单选项的简短列表到长列表的联系人或 internet 收藏夹。 它提供了一种简单的方法来显示可使用内置样式或广泛自定义的行的滚动列表。 ListView 实例需要适配器，以便将其与行视图中包含的数据一起馈送。

    ![示例列表视图](images/list-view.png)

- [GridView](~/android/user-interface/layouts/grid-view.md)是一个 UI 元素，用于显示二维网格中可滚动的项。

    ![网格视图示例](images/grid-view.png)

- [GridLayout](~/android/user-interface/layouts/grid-layout.md)是一个视图组，支持在二维网格中布局视图，这与 HTML 表类似。

    ![网格布局示例](images/grid-layout.png)

- 在移动应用程序中，[选项卡式布局](~/android/user-interface/layouts/tab-layout/index.md)是一种常用的用户界面模式，因为它们简单易用。 它们提供了一种在应用程序的不同屏幕之间导航的一致且简单的方法。

    ![选项卡式布局示例](images/tabbed-layout.png)
