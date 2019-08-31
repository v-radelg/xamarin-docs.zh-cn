---
title: GridViewPager
ms.prod: xamarin
ms.assetid: A1CDD5F0-049B-4DFA-A268-8A875D26A675
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/02/2018
ms.openlocfilehash: 5bdff0aee39375d8de296849056660f69a7d907f
ms.sourcegitcommit: 1e3a0d853669dcc57d5dee0894d325d40c7d8009
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2019
ms.locfileid: "70198536"
---
# <a name="gridviewpager"></a>GridViewPager

[GridViewPager](https://docs.microsoft.com/samples/xamarin/monodroid-samples/wear-gridviewpager)示例演示如何实现适用于 Android 磨损的2d 选取器导航模式。

![正方形显示屏上 GridViewPager 的示例屏幕截图](gridviewpager-images/gridviewpager.png)

首先将[Xamarin Android 磨损支持](https://www.nuget.org/packages/Xamarin.Android.Wear/)NuGet 包添加到你的项目。

布局 XML 如下所示:

```xml
<android.support.wearable.view.GridViewPager xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/pager"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:keepScreenOn="true" />
```

创建一个[`GridPagerAdapter`](https://developer.android.com/reference/android/support/wearable/view/GridPagerAdapter.html)
(或子类[`FragmentGridPagerAdapter`](https://developer.android.com/reference/android/support/wearable/view/FragmentGridPagerAdapter.html)
提供要在用户导航时显示的视图。

[示例适配器](https://github.com/xamarin/monodroid-samples/blob/master/wear/GridViewPager/GridViewPager/SimpleGridPagerAdapter.cs)演示如何实现所需的方法, 包括、 `RowCount` `GetColumnCount` `GetBackground`、和的替代`GetFragment`

按如下所示绑定适配器:

```csharp
pager.Adapter = new SimpleGridPagerAdapter (this, FragmentManager);
```



## <a name="related-links"></a>相关链接

- [Google 的2D 选取器文档](https://developer.android.com/training/wearables/ui/2d-picker.html)
- [可穿戴文档](https://developer.android.com/reference/android/support/wearable/view/package-summary.html)
- [GridViewPager (示例)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/wear-gridviewpager)
