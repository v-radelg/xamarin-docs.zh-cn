---
title: GridViewPager
ms.prod: xamarin
ms.assetid: A1CDD5F0-049B-4DFA-A268-8A875D26A675
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/02/2018
ms.openlocfilehash: 12e7e31cda9818a3cb2e2efc331a0be5d0c334e5
ms.sourcegitcommit: d3f48bfe72bfe03aca247d47bc64bfbfad1d8071
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/06/2019
ms.locfileid: "66740845"
---
# <a name="gridviewpager"></a>GridViewPager

[GridViewPager](https://developer.xamarin.com/samples/monodroid/wear/GridViewPager/)示例演示如何实现 Android wear 2D 选取器导航模式。

![GridViewPager 的方形显示屏上的示例屏幕截图](gridviewpager-images/gridviewpager.png)

首先添加[Xamarin Android Wear 支持](https://www.nuget.org/packages/Xamarin.Android.Wear/)到你的项目的 NuGet 包。

布局 XML 如下所示：

```xml
<android.support.wearable.view.GridViewPager xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/pager"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:keepScreenOn="true" />
```

创建 [`GridPagerAdapter`](https://developer.android.com/reference/android/support/wearable/view/GridPagerAdapter.html)
（或如子类 [`FragmentGridPagerAdapter`](https://developer.android.com/reference/android/support/wearable/view/FragmentGridPagerAdapter.html)
视图将显示为用户提供导航。

[示例适配器](https://github.com/xamarin/monodroid-samples/blob/master/wear/GridViewPager/GridViewPager/SimpleGridPagerAdapter.cs)演示如何实现所需的方法，包括重写`RowCount`， `GetColumnCount`， `GetBackground`，和 `GetFragment`

接通适配器所示：

```csharp
pager.Adapter = new SimpleGridPagerAdapter (this, FragmentManager);
```



## <a name="related-links"></a>相关链接

- [Google 的 2D 选取器文档](https://developer.android.com/training/wearables/ui/2d-picker.html)
- [android.support.wearable docs](https://developer.android.com/reference/android/support/wearable/view/package-summary.html)
- [GridViewPager (sample)](https://developer.xamarin.com/samples/monodroid/wear/GridViewPager/)
