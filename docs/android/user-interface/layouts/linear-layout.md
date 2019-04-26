---
title: LinearLayout
ms.prod: xamarin
ms.assetid: B49D129C-AF24-3C5A-C833-5A34AFBB2442
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/07/2018
ms.openlocfilehash: f3d0394f6b2388918f728bd5a25e9e809a832ca6
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61187085"
---
# <a name="linearlayout"></a>LinearLayout

[`LinearLayout`](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/) 是 [`ViewGroup`](https://developer.xamarin.com/api/type/Android.Views.ViewGroup/)
显示子 [`View`](https://developer.xamarin.com/api/type/Android.Views.View/)
线性方向中的元素垂直或水平。

您应该小心使用过度[ `LinearLayout` ](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/)。
如果开始嵌套多个[ `LinearLayout` ](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/)s，您可能需要考虑使用 [`RelativeLayout`](https://developer.xamarin.com/api/type/Android.Widget.RelativeLayout/)
改为。

启动一个名为的新项目**HelloLinearLayout**。

打开 **Resources/Layout/Main.axml** 并插入以下代码：

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation=    "vertical"
    android:layout_width=    "match_parent"
    android:layout_height=    "match_parent"    >

  <LinearLayout
      android:orientation=    "horizontal"
      android:layout_width=    "match_parent"
      android:layout_height=    "match_parent"
      android:layout_weight=    "1"    >
      <TextView
          android:text=    "red"
          android:gravity=    "center_horizontal"
          android:background=    "#aa0000"
          android:layout_width=    "wrap_content"
          android:layout_height=    "match_parent"
          android:layout_weight=    "1"    />
      <TextView
          android:text=    "green"
          android:gravity=    "center_horizontal"
          android:background=    "#00aa00"
          android:layout_width=    "wrap_content"
          android:layout_height=    "match_parent"
          android:layout_weight=    "1"    />
      <TextView
          android:text=    "blue"
          android:gravity=    "center_horizontal"
          android:background=    "#0000aa"
          android:layout_width=    "wrap_content"
          android:layout_height=    "match_parent"
          android:layout_weight=    "1"    />
      <TextView
          android:text=    "yellow"
          android:gravity=    "center_horizontal"
          android:background=    "#aaaa00"
          android:layout_width=    "wrap_content"
          android:layout_height=    "match_parent"
          android:layout_weight=    "1"    />
  </LinearLayout>
        
  <LinearLayout
    android:orientation=    "vertical"
    android:layout_width=    "match_parent"
    android:layout_height=    "match_parent"
    android:layout_weight=    "1"    >
    <TextView
        android:text=    "row one"
        android:textSize=    "15pt"
        android:layout_width=    "match_parent"
        android:layout_height=    "wrap_content"
        android:layout_weight=    "1"    />
    <TextView
        android:text=    "row two"
        android:textSize=    "15pt"
        android:layout_width=    "match_parent"
        android:layout_height=    "wrap_content"
        android:layout_weight=    "1"    />
    <TextView
        android:text=    "row three"
        android:textSize=    "15pt"
        android:layout_width=    "match_parent"
        android:layout_height=    "wrap_content"
        android:layout_weight=    "1"    />
    <TextView
        android:text=    "row four"
        android:textSize=    "15pt"
        android:layout_width=    "match_parent"
        android:layout_height=    "wrap_content"
       android:layout_weight=    "1"    />
  </LinearLayout>

</LinearLayout>
```

请仔细检查此 XML。 没有根 [`LinearLayout`](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/)
它定义自己的方向为垂直&ndash;所有子[ `View` ](https://developer.xamarin.com/api/type/Android.Views.View/)s （它的具有两个） 将是堆积垂直。 第一个子级是另一个 [`LinearLayout`](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/)
使用水平方向，并且第二个子级 [`LinearLayout`](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/)
使用垂直方向。 每个嵌套[ `LinearLayout` ](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/)s 包含多个 [`TextView`](https://developer.xamarin.com/api/type/Android.Widget.TextView/)
元素，它们是由其父级定义的方式与每个其他面向[ `LinearLayout` ](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/)。

现在，打开**HelloLinearLayout.cs**并确保它将加载**Resources/Layout/Main.axml**中的布局 [`OnCreate()`](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/p/Android.OS.Bundle/)
方法：

```csharp
protected override void OnCreate (Bundle savedInstanceState)
{
    base.OnCreate (savedInstanceState);
    SetContentView (Resource.Layout.Main);
}
```

[ `SetContentView(int)` ](https://developer.xamarin.com/api/member/Android.App.Activity.SetContentView/(System.Int32))方法加载的布局文件[ `Activity` ](https://developer.xamarin.com/api/type/Android.App.Activity/)，按资源 ID 指定&ndash;`Resources.Layout.Main`指**资源/布局 /Main.axml**布局文件。

运行该应用程序。 你应看到以下信息：

[![屏幕截图的第一个 LinearLayout 水平排列的应用，第二个垂直](linear-layout-images/helloviews1.png)](linear-layout-images/helloviews1.png#lightbox)

请注意，XML 属性定义每个视图的行为的方法。 尝试试验不同的值`android:layout_weight`若要查看如何分布的屏幕空间基于每个元素的权重。 请参阅[常见布局对象](https://developer.android.com/guide/topics/ui/declaring-layout.html)深入了解如何文档 [`LinearLayout`](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/)
句柄`android:layout_weight`属性。


## <a name="references"></a>参考资料

-   [`LinearLayout`](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/) 
-   [`TextView`](https://developer.xamarin.com/api/type/Android.Widget.TextView/) 

*此页的部分是基于工作创建和共享通过 Android 的开放源项目和使用中所述的条款的修改*
[*Creative Commons 2.5 Attribution 许可证*](http://creativecommons.org/licenses/by/2.5/).

