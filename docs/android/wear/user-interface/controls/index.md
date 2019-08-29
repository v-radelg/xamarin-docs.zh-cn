---
title: Android 损耗控件
ms.prod: xamarin
ms.assetid: 5B62A5F8-5E55-4B3C-BFC4-E21CDB27C08B
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: db240fa91c7eb99bd628fbb04c7c9352fb615e83
ms.sourcegitcommit: 1dd7d09b60fcb1bf15ba54831ed3dd46aa5240cb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70119946"
---
# <a name="android-wear-controls"></a>Android 损耗控件

Android 应用可以使用许多相同的控件, 这些控件已用于常规 Android 应用, 包括`Button`、 `TextView`和映像绘图。 还可以使用`ScrollView`布局`LinearLayout`控件, `RelativateLayout`其中包括、和。

此页通过[可穿戴支持](https://www.nuget.org/packages/Xamarin.Android.Wear/)NuGet 包从 Xamarin 项目中可用的[可穿戴 UI 库](https://developer.android.com/training/wearables/apps/layouts.html#UiLibrary)中链接到特定于 Android 的特定控件。 这些控件包括:

- **GridViewPager**创建一个二维导航界面, 用户可在其中向下滚动, 以便进行选择 (有关详细信息, 请参阅[GridViewPager):](~/android/wear/user-interface/controls/gridviewpager.md) &ndash;

    ![GridViewPager 的示例屏幕截图](images/gridviewpager.png)

适用于应用程序的其他重要控件包括:

- `BoxInsetLayout`(请参阅[使用屏幕大小](~/android/wear/screen-sizes.md)),

- `WatchViewStub`(请参阅[使用屏幕大小](~/android/wear/screen-sizes.md)),

- `CardFrame`(请参阅[Android 创建卡](https://developer.android.com/training/wearables/ui/cards.html)),

- `CardScrollView`(请参阅[Android 创建卡](https://developer.android.com/training/wearables/ui/cards.html)),

- `WearableListView`(请参阅[Android 创建列表](https://developer.android.com/training/wearables/ui/lists.html))。


## <a name="related-links"></a>相关链接

- [可穿戴文档](https://developer.android.com/reference/android/support/wearable/view/package-summary.html)
