---
title: 选项卡式布局
description: 在 Android 中的选项卡式布局的概述
ms.prod: xamarin
ms.assetid: 1CFF590A-AC86-C3B3-36CA-A70248BC7F97
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 05/08/2017
ms.openlocfilehash: 32d1ce4e440a962e02fda052375171bea7676053
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/08/2019
ms.locfileid: "57670130"
---
# <a name="tabbed-layouts"></a>选项卡式布局


## <a name="overview"></a>概述

选项卡是由于其简单性和可用性的移动应用程序中的常见用户界面模式。 它们提供一致、 简单的方法的应用程序中的各种屏幕之间进行导航。 Android 有几个 API 的选项卡式接口： 

-   **ActionBar** &ndash;这是一组新的 API 的目标是提供一致的 Android 3.0 （API 级别为 11） 中引入的一部分导航和切换视图的接口。 已为 Android 2.2 （API 级别 8） 与返回移植[Android 支持库 v7](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/)。 

-   **PagerTabStrip** &ndash;指示当前、 下一步，和上一个页面的`ViewPager`。 `ViewPager` 仅可[Android 支持库 v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)。
     有关详细信息`PagerTabStrip`，请参阅[ViewPager](~/android/user-interface/controls/view-pager/index.md)。

-   **工具栏** &ndash; `Toolbar`是取代的更新、 更灵活的操作栏组件`ActionBar`。 `Toolbar` 在 Android 5.0 Lollipop 或更高版本，并且仍可用于较旧版本的 Android 通过[Android 支持库 v7](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/) NuGet 包。 
    `Toolbar` 目前在 Android 应用中使用的建议的操作栏组件。
    有关详细信息，请参阅[工具栏](~/android/user-interface/controls/tool-bar/index.md)。 



## <a name="related-links"></a>相关链接

- [材料设计-选项卡](https://material.io/guidelines/components/tabs.html)- [ActionBar](https://developer.android.com/guide/topics/ui/actionbar.html)
- [Android 支持库 v7 AppCompat NuGet 包](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/)
- [v7 appcompat 库](https://developer.android.com/tools/support-library/features.html#v7-appcompat)
