---
title: 在 Xamarin 中使用 RelativeLayout
description: 如何在 Xamarin Android 应用程序中使用 RelativeLayout
ms.prod: xamarin
ms.assetid: AFD9C849-02C3-E728-BC78-77A563612BC5
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/29/2018
ms.openlocfilehash: af74ae3c7c87f501bff519bcfa361264205ca3f1
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/16/2019
ms.locfileid: "69522374"
---
# <a name="xamarinandroid-relativelayout"></a>Xamarin RelativeLayout

[`RelativeLayout`](xref:Android.Widget.RelativeLayout)是一个[`ViewGroup`](xref:Android.Views.ViewGroup)显示子级的[`View`](xref:Android.Views.View)
相对位置的元素。 的位置[`View`](xref:Android.Views.View)可以指定为相对于同级元素 (例如, 指向给定元素的左侧或下方) 或相对于[`RelativeLayout`](xref:Android.Widget.RelativeLayout)
面积图 (如靠下、靠上居中对齐)。

是一个非常强大的实用工具, 用于设计用户界面, 因为它可以消除[`ViewGroup`](xref:Android.Views.ViewGroup)嵌套的。 [`RelativeLayout`](xref:Android.Widget.RelativeLayout) 如果你发现自己使用了多个嵌套[`LinearLayout`](xref:Android.Widget.LinearLayout)
组, 你可以使用单个[`RelativeLayout`](xref:Android.Widget.RelativeLayout)来替换它们。

启动名为**HelloRelativeLayout**的新项目。

打开**Resources/Layout/main.axml**文件, 并插入以下内容:

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

请注意每个`android:layout_*`属性, `layout_below`如、 `layout_alignParentRight`和`layout_toLeftOf`。
使用[`RelativeLayout`](xref:Android.Widget.RelativeLayout)时, 可以使用这些属性来描述要如何定位每个[`View`](xref:Android.Views.View)。 其中每个属性都定义了不同类型的相对位置。 某些属性使用同级[`View`](xref:Android.Views.View)的资源 ID 来定义其自己的相对位置。 例如, [`Button`](xref:Android.Widget.Button)将 last 定义为位于由 ID `ok` (前一[`Button`](xref:Android.Widget.Button)项) 标识的[`View`](xref:Android.Views.View)左侧的左侧和顶部对齐的顶部。

所有可用的布局特性都在中[`RelativeLayout.LayoutParams`](xref:Android.Widget.RelativeLayout.LayoutParams)定义。

请确保在中加载此布局[`OnCreate()`](xref:Android.App.Activity.OnCreate*)
付款方式

```csharp
protected override void OnCreate (Bundle savedInstanceState)
{
    base.OnCreate (savedInstanceState);
    SetContentView (Resource.Layout.Main);
}
```

[`Activity`](xref:Android.App.Activity) &mdash; `Resource.Layout.Main`方法加载的布局文件, 由资源 ID 指定, 表示资源/布局/main.axml 布局文件。 [`SetContentView(int)`](xref:Android.App.Activity.SetContentView*)

运行该应用程序。 应会看到以下布局:

[![具有 TextView、EditText 和两个按钮的相对布局屏幕截图](relative-layout-images/helloviews2.png)](relative-layout-images/helloviews2.png#lightbox)

## <a name="resources"></a>资源

- [`RelativeLayout`](xref:Android.Widget.RelativeLayout)
- [`RelativeLayout.LayoutParams`](xref:Android.Widget.RelativeLayout.LayoutParams)
- [`TextView`](xref:Android.Widget.TextView)
- [`EditText`](xref:Android.Widget.EditText)
- [`Button`](xref:Android.Widget.Button)

_此页面的某些部分是基于 Android 开源项目创建和共享的工作的修改, 并根据[创造性 Commons 2.5 归属许可证](http://creativecommons.org/licenses/by/2.5/)中所述的条款使用。_
