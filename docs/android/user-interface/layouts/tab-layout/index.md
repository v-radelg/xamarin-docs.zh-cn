---
title: 选项卡式布局
description: Android 中的选项卡式布局概述
ms.prod: xamarin
ms.assetid: 1CFF590A-AC86-C3B3-36CA-A70248BC7F97
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 05/08/2017
ms.openlocfilehash: 4ca4200d0f9036ed76e20e3a1840303e7bb3b7e3
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73028780"
---
# <a name="tabbed-layouts"></a>选项卡式布局

## <a name="overview"></a>概述

在移动应用程序中，选项卡是一种常用的用户界面模式，因为它们简单易用。 它们提供了一种在应用程序的不同屏幕之间导航的一致且简单的方法。 Android 有多个用于选项卡式界面的 API： 

- **ActionBar** &ndash; 这是一组新的 API 的一部分，它是在 Android 3.0 （API 级别11）中引入的，其目标是提供一致的导航和视图切换界面。 它已通过[Android 支持库 v7](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/)返回到 android 2.2 （API 级别8）。 

- **PagerTabStrip** &ndash; 指示 `ViewPager`的当前、下一页和上一页。 `ViewPager` 仅通过[Android 支持库 v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)提供。
     有关 `PagerTabStrip`的详细信息，请参阅[ViewPager](~/android/user-interface/controls/view-pager/index.md)。

- **工具栏**&ndash; `Toolbar` 是一个更新了 `ActionBar`的更新且更灵活的操作栏组件。 `Toolbar` 适用于 Android 5.0 棒糖或更高版本，也可通过[Android 支持库 v7](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/) NuGet 包用于较早版本的 android。 
    `Toolbar` 当前是建议用于 Android 应用的操作栏组件。
    有关详细信息，请参阅[Toolbar](~/android/user-interface/controls/tool-bar/index.md)。 

## <a name="related-links"></a>相关链接

- [材料设计-选项卡](https://material.io/guidelines/components/tabs.html)- [ActionBar](https://developer.android.com/guide/topics/ui/actionbar.html)
- [Android 支持库 v7 AppCompat NuGet 包](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/)
- [v7 appcompat 库](https://developer.android.com/tools/support-library/features.html#v7-appcompat)
