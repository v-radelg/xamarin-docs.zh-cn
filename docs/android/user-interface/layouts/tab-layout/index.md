---
title: 选项卡式布局
description: Android 中的选项卡式布局概述
ms.prod: xamarin
ms.assetid: 1CFF590A-AC86-C3B3-36CA-A70248BC7F97
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 05/08/2017
ms.openlocfilehash: 1e0632eb921b25fef40b8f0483ab80d62c9e1235
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70762196"
---
# <a name="tabbed-layouts"></a>选项卡式布局

## <a name="overview"></a>概述

在移动应用程序中，选项卡是一种常用的用户界面模式，因为它们简单易用。 它们提供了一种在应用程序的不同屏幕之间导航的一致且简单的方法。 Android 有多个用于选项卡式界面的 API： 

- **ActionBar**&ndash;这是 Android 3.0 （api 级别11）中引入的一组新的 API 的一部分，其目标是提供一致的导航和视图切换界面。 它已通过[Android 支持库 v7](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/)返回到 android 2.2 （API 级别8）。 

- **PagerTabStrip**指示的当前页、下一页页`ViewPager`和上一页。 &ndash; `ViewPager`仅可通过[Android 支持库 v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)提供。
     有关的详细信息`PagerTabStrip`，请参阅[ViewPager](~/android/user-interface/controls/view-pager/index.md)。

- **工具栏**是一个更新的更灵活的`ActionBar`操作栏组件。 &ndash; `Toolbar` `Toolbar`适用于 Android 5.0 棒糖或更高版本，也可通过[Android 支持库 v7](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/) NuGet 包用于较早版本的 android。 
    `Toolbar`当前是建议用于 Android 应用程序的操作栏组件。
    有关详细信息，请参阅[Toolbar](~/android/user-interface/controls/tool-bar/index.md)。 

## <a name="related-links"></a>相关链接

- [材料设计-选项卡](https://material.io/guidelines/components/tabs.html)- [ActionBar](https://developer.android.com/guide/topics/ui/actionbar.html)
- [Android 支持库 v7 AppCompat NuGet 包](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/)
- [v7 appcompat 库](https://developer.android.com/tools/support-library/features.html#v7-appcompat)
