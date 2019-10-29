---
title: 在 Xamarin 中使用 RelativeLayout
description: 如何在 Xamarin Android 应用程序中使用 RelativeLayout
ms.prod: xamarin
ms.assetid: AFD9C849-02C3-E728-BC78-77A563612BC5
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/29/2018
ms.openlocfilehash: 6cac771f46242cc0475be0a7ec0d475950f4b4e1
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73028799"
---
# <a name="xamarinandroid-relativelayout"></a>Xamarin RelativeLayout

[`RelativeLayout`](xref:Android.Widget.RelativeLayout)是显示子[`View`](xref:Android.Views.View)的[`ViewGroup`](xref:Android.Views.ViewGroup)
相对位置的元素。 可以指定[`View`](xref:Android.Views.View)的位置，使其相对于同级元素（例如，指向给定元素的左侧或底部）或相对于[`RelativeLayout`](xref:Android.Widget.RelativeLayout)的位置
面积图（如靠下、靠上居中对齐）。

[`RelativeLayout`](xref:Android.Widget.RelativeLayout)是一种非常强大的实用工具，用于设计用户界面，因为它可以消除嵌套[`ViewGroup`](xref:Android.Views.ViewGroup)。 如果你发现自己使用几个嵌套[`LinearLayout`](xref:Android.Widget.LinearLayout)
组，你可以使用单个[`RelativeLayout`](xref:Android.Widget.RelativeLayout)来替换它们。

启动名为**HelloRelativeLayout**的新项目。

打开**Resources/Layout/main.axml**文件，并插入以下内容：

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

请注意每个 `android:layout_*` 属性，如 `layout_below`、`layout_alignParentRight`和 `layout_toLeftOf`。
使用[`RelativeLayout`](xref:Android.Widget.RelativeLayout)时，可以使用这些属性来描述要如何定位每个[`View`](xref:Android.Views.View)。 其中每个属性都定义了不同类型的相对位置。 某些属性使用同级[`View`](xref:Android.Views.View)的资源 ID 来定义其自己的相对位置。 例如，最后一个[`Button`](xref:Android.Widget.Button)定义为位于由 ID `ok` （这是以前的[`Button`](xref:Android.Widget.Button)）标识的[`View`](xref:Android.Views.View)的左侧和顶部对齐的顶部。

所有可用的布局特性都在[`RelativeLayout.LayoutParams`](xref:Android.Widget.RelativeLayout.LayoutParams)中定义。

请确保在[`OnCreate()`](xref:Android.App.Activity.OnCreate*)中加载此布局。
付款方式

```csharp
protected override void OnCreate (Bundle savedInstanceState)
{
    base.OnCreate (savedInstanceState);
    SetContentView (Resource.Layout.Main);
}
```

[`SetContentView(int)`](xref:Android.App.Activity.SetContentView*)方法加载资源 ID &mdash; 指定的[`Activity`](xref:Android.App.Activity)的布局文件，`Resource.Layout.Main` 引用**资源/布局 main.axml**布局文件。

运行该应用程序。 应会看到以下布局：

[使用 TextView、EditText 和两个按钮的相对布局![屏幕快照](relative-layout-images/helloviews2.png)](relative-layout-images/helloviews2.png#lightbox)

## <a name="resources"></a>资源

- [`RelativeLayout`](xref:Android.Widget.RelativeLayout)
- [`RelativeLayout.LayoutParams`](xref:Android.Widget.RelativeLayout.LayoutParams)
- [`TextView`](xref:Android.Widget.TextView)
- [`EditText`](xref:Android.Widget.EditText)
- [`Button`](xref:Android.Widget.Button)

_此页面的某些部分是基于 Android 开源项目创建和共享的工作的修改，并根据[创造性 Commons 2.5 归属许可证](https://creativecommons.org/licenses/by/2.5/)中所述的条款使用。_
