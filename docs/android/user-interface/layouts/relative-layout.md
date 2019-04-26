---
title: 在 Xamarin.Android 中使用 RelativeLayout
description: 如何在 Xamarin.Android 应用程序中使用 RelativeLayout
ms.prod: xamarin
ms.assetid: AFD9C849-02C3-E728-BC78-77A563612BC5
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/29/2018
ms.openlocfilehash: af2972ecc92435836a75013e6203ba47c2c04627
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61303613"
---
# <a name="relativelayout"></a>RelativeLayout

[`RelativeLayout`](https://developer.xamarin.com/api/type/Android.Widget.RelativeLayout/) 是[ `ViewGroup` ](https://developer.xamarin.com/api/type/Android.Views.ViewGroup/)显示子 [`View`](https://developer.xamarin.com/api/type/Android.Views.View/)
中的相对位置的元素。 位置[ `View` ](https://developer.xamarin.com/api/type/Android.Views.View/)可以指定相对于同级元素 （如有关的左侧或给定元素的下面），或在定位相对于 [`RelativeLayout`](https://developer.xamarin.com/api/type/Android.Widget.RelativeLayout/)
区域 （如如对齐到底部，左侧的中心）。

一个[ `RelativeLayout` ](https://developer.xamarin.com/api/type/Android.Widget.RelativeLayout/)是一个非常强大的实用程序，对于设计用户界面，因为它可以消除嵌套[ `ViewGroup` ](https://developer.xamarin.com/api/type/Android.Views.ViewGroup/)s。 如果你发现自己使用多个嵌套 [`LinearLayout`](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/)
组，您可能能够将它们替换成单个[ `RelativeLayout` ](https://developer.xamarin.com/api/type/Android.Widget.RelativeLayout/)。

启动一个名为的新项目**HelloRelativeLayout**。

打开**Resources/Layout/Main.axml**文件并插入以下：

```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent">
    <TextView
        android:id="@+id/label"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Type here:"/>
    <EditText
        android:id="@+id/entry"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:background="@android:drawable/editbox_background"
        android:layout_below="@id/label"/>
    <Button
        android:id="@+id/ok"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_below="@id/entry"
        android:layout_alignParentRight="true"
        android:layout_marginLeft="10dip"
        android:text="OK" />
    <Button
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_toLeftOf="@id/ok"
        android:layout_alignTop="@id/ok"
        android:text="Cancel" />
</RelativeLayout>
```

请注意，每个`android:layout_*`这样的特性`layout_below`， `layout_alignParentRight`，和`layout_toLeftOf`。
使用时[ `RelativeLayout` ](https://developer.xamarin.com/api/type/Android.Widget.RelativeLayout/)，可以使用这些属性来描述你想要放置每个[ `View` ](https://developer.xamarin.com/api/type/Android.Views.View/)。 这些属性的每个定义一种不同的相对位置。 某些属性使用的资源 ID 的同级[ `View` ](https://developer.xamarin.com/api/type/Android.Views.View/)来定义其自己的相对位置。 例如上, 一次[ `Button` ](https://developer.xamarin.com/api/type/Android.Widget.Button/)定义为位于左侧的和对齐-使用--top-的[ `View` ](https://developer.xamarin.com/api/type/Android.Views.View/)由 ID 标识`ok`(即以前[`Button`](https://developer.xamarin.com/api/type/Android.Widget.Button/)).

中定义的所有可用的布局属性[ `RelativeLayout.LayoutParams` ](https://developer.xamarin.com/api/type/Android.Widget.RelativeLayout+LayoutParams/)。

请确保加载在此布局 [`OnCreate()`](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/p/Android.OS.Bundle/)
方法：

```csharp
protected override void OnCreate (Bundle savedInstanceState)
{
    base.OnCreate (savedInstanceState);
    SetContentView (Resource.Layout.Main);
}
```

[ `SetContentView(int)` ](https://developer.xamarin.com/api/member/Android.App.Activity.SetContentView/p/System.Int32/)方法加载的布局文件[ `Activity` ](https://developer.xamarin.com/api/type/Android.App.Activity/)，按资源 ID 指定&mdash;`Resource.Layout.Main`指**资源/布局 /Main.axml**布局文件。

运行该应用程序。 应会看到以下布局：

[![一个相对的布局，其中一个 TextView、 EditText 和两个按钮的屏幕截图](relative-layout-images/helloviews2.png)](relative-layout-images/helloviews2.png#lightbox)


## <a name="resources"></a>资源

-   [`RelativeLayout`](https://developer.xamarin.com/api/type/Android.Widget.RelativeLayout/)
-   [`RelativeLayout.LayoutParams`](https://developer.xamarin.com/api/type/Android.Widget.RelativeLayout+LayoutParams/)
-   [`TextView`](https://developer.xamarin.com/api/type/Android.Widget.TextView/)
-   [`EditText`](https://developer.xamarin.com/api/type/Android.Widget.EditText/)
-   [`Button`](https://developer.xamarin.com/api/type/Android.Widget.Button/)


*此页的部分是基于工作创建和共享通过 Android 的开放源项目和使用中所述的条款的修改*
[*Creative Commons 2.5 Attribution 许可证*](http://creativecommons.org/licenses/by/2.5/).
